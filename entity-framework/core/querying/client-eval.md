---
title: 客戶端與伺服器評估 - EF 核心
author: smitpatel
ms.date: 10/03/2019
ms.assetid: 8b6697cc-7067-4dc2-8007-85d80503d123
uid: core/querying/client-eval
ms.openlocfilehash: e01bd146c4dfe7a8d36b641cb52ae366fddd8239
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "78417757"
---
# <a name="client-vs-server-evaluation"></a>用戶端與伺服器評估

通常,實體框架核心嘗試盡可能評估伺服器上的查詢。 EF Core 將查詢的某些部分轉換為參數,可以在用戶端上對其進行計算。 查詢的其餘部分(以及生成的參數)將提供給資料庫提供者,以確定要在伺服器上評估的等效資料庫查詢。 EF Core 支持頂級投影中的部分客戶端評估(實質上是最後一`Select()`次調用 )。 如果查詢中的頂級投影無法轉換為伺服器,EF Core 將從伺服器獲取任何必需的數據,並計算用戶端上查詢的剩餘部分。 如果 EF Core 在頂級投影以外的任何位置檢測到運算式,無法轉換為伺服器,則將引發運行時異常。 瞭解[查詢的工作原理](xref:core/querying/how-query-works),以瞭解 EF Core 如何確定無法轉換為伺服器的內容。

> [!NOTE]
> 在版本 3.0 之前,實體框架核心支援查詢中的任何位置的客戶端評估。 有關詳細資訊,請參閱[以前的版本部分](#previous-versions)。

> [!TIP]
> 您可以在 GitHub 上查看本文[的範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying)。

## <a name="client-evaluation-in-the-top-level-projection"></a>頂級投影中的客戶端評估

在下面的示例中,説明器方法用於標準化博客的 URL,這些 URL 是從 SQL Server 資料庫返回的。 由於 SQL Server 提供程式無法深入瞭解此方法的實現方式,因此無法將其轉換為 SQL。 查詢的所有其他方面都在資料庫中計算,但通過此方法傳遞返回`URL`的是在用戶端上完成的。

[!code-csharp[Main](../../../samples/core/Querying/ClientEval/Sample.cs#ClientProjection)]

[!code-csharp[Main](../../../samples/core/Querying/ClientEval/Sample.cs#ClientMethod)]

## <a name="unsupported-client-evaluation"></a>不支援的客戶端評估

雖然客戶端評估很有用,但有時可能會導致性能不佳。 請考慮以下查詢,其中幫助器方法現在用於 where 篩選器。 由於無法在資料庫中應用篩選器,因此需要將所有數據提取到記憶體中,才能在用戶端上應用篩選器。 根據篩選器和伺服器上的數據量,客戶端評估可能會導致性能不佳。 因此,實體框架核心阻止此類客戶端評估,並引發運行時異常。

[!code-csharp[Main](../../../samples/core/Querying/ClientEval/Sample.cs#ClientWhere)]

## <a name="explicit-client-evaluation"></a>明確客戶端評估

在某些情況下,您可能需要顯式強制到客戶端評估中,例如:

- 數據量很小,因此對客戶端進行評估不會造成巨大的性能損失。
- 正在使用的 LINQ 運算子沒有伺服器端轉換。

在這種情況下,您可以通過調`AsEnumerable`用 方法`ToList`(`AsAsyncEnumerable`或`ToListAsync`(或非同步)來顯式加入加入客戶端評估。 通過使用`AsEnumerable`您將流式傳輸結果,但`ToList`使用會導致通過創建清單(這也會佔用額外記憶體)進行緩衝。 儘管要多次枚舉,但將結果存儲在清單中會很有幫助,因為資料庫只有一個查詢。 根據特定的用法,您應該評估哪種方法對案例更有用。

[!code-csharp[Main](../../../samples/core/Querying/ClientEval/Sample.cs#ExplicitClientEval)]

## <a name="potential-memory-leak-in-client-evaluation"></a>用戶端評估中的潛在記憶體洩漏

由於查詢轉換和編譯成本高昂,EF Core 緩存已編譯的查詢計劃。 緩存的委託在進行頂級投影的客戶端評估時可以使用客戶端代碼。 EF Core 為樹的客戶端評估部分生成參數,並通過替換參數值來重用查詢計劃。 但是表達式樹中的某些常量不能轉換為參數。 如果緩存的委託包含此類常量,則無法回收這些對象,因為它們仍在被引用。 如果此類物件包含 DbContext 或其他服務,則可能導致應用的記憶體使用量隨時間而增長。 此行為通常是記憶體洩漏的跡象。 每當發生無法使用當前資料庫提供程式映射的類型常量時,EF Core 都會引發異常。 常見原因及其解決方案如下:

- **在**用戶端投影中使用實例方法時,表達式樹包含實例的常量。 如果方法不使用實例中的任何數據,請考慮使該方法成為靜態方法。 如果需要方法體中的實例數據,則將特定數據作為參數傳遞給方法。
- **將常量參數傳遞給方法**:這種情況通常通過在參數`this`中使用到用戶端方法而出現。 請考慮將參數拆分為多個標量參數,該參數可以由資料庫提供程式映射。
- **其他常量**:如果遇到任何其他情況下的常量,則可以評估處理中是否需要常量。 如果需要具有常量,或者無法使用上述情況下的解決方案,則創建一個局部變數來存儲值並在查詢中使用局部變數。 EF Core 將本地變數轉換為參數。

## <a name="previous-versions"></a>舊版本

以下部分適用於 3.0 之前的 EF Core 版本。

較舊的 EF Core 版本支援查詢的任何部分的客戶端評估,而不僅僅是頂級投影。 這就是為什麼與[「不支援的客戶端評估](#unsupported-client-evaluation)」部分下發佈的查詢類似的查詢工作正常的原因。 由於此行為可能導致未注意的性能問題,EF Core 記錄了客戶端評估警告。 有關檢視紀錄紀錄輸出的詳細資訊,請參閱[紀錄紀錄](xref:core/miscellaneous/logging)。

可選,EF Core 允許您更改預設行為,以在執行客戶端評估時引發異常或不執行任何操作(投影中除外)。 異常引發行為將使其類似於 3.0 中的行為。 要更改行為,您需要在設置上下文選項時配置警告 - 通常`DbContext.OnConfiguring`在中`Startup.cs`,或者在中使用 ASP.NET 酷睿時。

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFQuerying;Trusted_Connection=True;")
        .ConfigureWarnings(warnings => warnings.Throw(RelationalEventId.QueryClientEvaluationWarning));
}
```
