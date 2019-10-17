---
title: 用戶端與伺服器的評估-EF Core
author: smitpatel
ms.date: 10/03/2019
ms.assetid: 8b6697cc-7067-4dc2-8007-85d80503d123
uid: core/querying/client-eval
ms.openlocfilehash: 5cfb05041f04246712fb699f58b407f70a75ce92
ms.sourcegitcommit: 37d0e0fd1703467918665a64837dc54ad2ec7484
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72445962"
---
# <a name="client-vs-server-evaluation"></a>用戶端與伺服器的評估

一般的規則是，Entity Framework Core 嘗試在伺服器上盡可能評估查詢。 EF Core 會將查詢的某些部分轉換成參數，它可以在用戶端進行評估。 查詢的其餘部分（連同產生的參數）會提供給資料庫提供者，以決定要在伺服器上評估的對等資料庫查詢。 EF Core 支援最上層投影中的部分用戶端評估（基本上，`Select()` 的最後一次呼叫）。 如果查詢中的最上層投影無法轉譯成伺服器，EF Core 將會從伺服器提取所有必要的資料，並評估用戶端上的查詢其餘部分。 如果 EF Core 在最上層投射以外的任何地方偵測到運算式，但無法將其轉譯為伺服器，則會擲回執行時間例外狀況。 查看[查詢如何運作](xref:core/querying/how-query-works)，以瞭解 EF Core 如何判斷無法轉譯成伺服器的內容。

> [!NOTE]
> 在3.0 版之前，請在查詢的任何位置 Entity Framework Core 支援的用戶端評估。 如需詳細資訊，請參閱[先前的版本一節](#previous-versions)。

> [!TIP]
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) \(英文\)。

## <a name="client-evaluation-in-the-top-level-projection"></a>最上層投影中的用戶端評估

在下列範例中，helper 方法是用來將 blog 的 Url 標準化，而這些是從 SQL Server 資料庫傳回的。 由於 SQL Server 提供者無法深入瞭解如何執行此方法，因此無法將它轉譯為 SQL。 查詢的所有其他層面都會在資料庫中進行評估，但透過這個方法傳遞傳回的 `URL` 會在用戶端上完成。

[!code-csharp[Main](../../../samples/core/Querying/ClientEval/Sample.cs#ClientProjection)]

[!code-csharp[Main](../../../samples/core/Querying/ClientEval/Sample.cs#ClientMethod)]

## <a name="unsupported-client-evaluation"></a>不支援的用戶端評估

雖然用戶端評估很有用，但有時可能會導致效能不佳。 請考慮下列查詢，其中的 helper 方法現在用於 where 篩選準則中。 因為無法在資料庫中套用篩選，所以必須將所有資料提取到記憶體中，以在用戶端上套用篩選。 根據篩選器和伺服器上的資料量而定，用戶端評估可能會導致效能不佳。 因此 Entity Framework Core 會封鎖這類用戶端評估，並擲回執行時間例外狀況。

[!code-csharp[Main](../../../samples/core/Querying/ClientEval/Sample.cs#ClientWhere)]

## <a name="explicit-client-evaluation"></a>明確的用戶端評估

在某些情況下，您可能需要明確地強制執行用戶端評估，如下所示

- 資料量很小，因此在用戶端上進行評估並不會產生明顯的效能影響。
- 使用的 LINQ 運算子沒有伺服器端轉譯。

在這種情況下，您可以藉由呼叫 `AsEnumerable` 或 `ToList` （`AsAsyncEnumerable` 或 `ToListAsync` 以進行非同步）的方法，明確加入宣告用戶端評估。 藉由使用 `AsEnumerable`，您就可以串流處理結果，但使用 `ToList` 會藉由建立清單來產生緩衝，這也會佔用額外的記憶體。 不過，如果您要列舉多次，則將結果儲存在清單中會有説明，因為只有一個資料庫查詢。 根據特定的使用方式，您應該評估哪一種方法在案例中更有用。

[!code-csharp[Main](../../../samples/core/Querying/ClientEval/Sample.cs#ExplicitClientEval)]

## <a name="potential-memory-leak-in-client-evaluation"></a>用戶端評估中可能發生記憶體流失

由於查詢轉譯和編譯的成本很高，EF Core 快取已編譯的查詢計劃。 快取的委派可能會在執行最上層投影的用戶端評估時使用用戶端程式代碼。 EF Core 會為樹狀結構的用戶端評估部分產生參數，並藉由取代參數值來重複使用查詢計劃。 但是運算式樹狀架構中的某些常數無法轉換成參數。 如果快取的委派包含這類常數，則這些物件無法進行垃圾收集，因為它們仍然被參考。 如果這類物件在其中包含 DbCoNtext 或其他服務，則可能會導致應用程式的記憶體使用量隨著時間成長。 這種行為通常是記憶體流失的正負號。 EF Core 擲回例外狀況時，會在無法使用目前資料庫提供者對應的類型常數時擲回。 常見的原因和其解決方案如下所示：

- **使用實例方法**：在用戶端投射中使用實例方法時，運算式樹狀架構會包含實例的常數。 如果您的方法不使用實例中的任何資料，請考慮將方法設為靜態。 如果您需要方法主體中的實例資料，請將特定資料當做引數傳遞至方法。
- **將常數引數傳遞至方法**：這種情況通常是在用戶端方法的引數中使用 `this` 所引發。 請考慮將中的引數分割為多個純量引數（可由資料庫提供者對應）。
- **其他常數**：如果在任何其他情況下都有常數，您可以評估處理時是否需要常數。 如果必須有常數，或如果您無法使用上述案例中的解決方案，請建立本機變數來儲存值，並在查詢中使用區域變數。 EF Core 會將本機變數轉換成參數。

## <a name="previous-versions"></a>舊版本

下一節適用于3.0 之前的 EF Core 版本。

舊版的 EF Core 支援在查詢的任何部分中進行用戶端評估，而不只是最上層的預測。 這就是為什麼在 [[不支援的用戶端評估](#unsupported-client-evaluation)] 區段下張貼的查詢會正常運作。 由於此行為可能會造成未察覺的效能問題，EF Core 記錄用戶端評估警告。 如需有關查看記錄輸出的詳細資訊，請參閱[記錄](xref:core/miscellaneous/logging)。

（選擇性） EF Core 允許您變更預設行為，使其在執行用戶端評估時擲回例外狀況或不執行任何動作（在投影中則除外）。 例外狀況擲回行為會使其類似于3.0 中的行為。 若要變更此行為，您必須在設定內容的選項時（通常是在 `DbContext.OnConfiguring`）中設定警告，或如果您使用 ASP.NET Core，則為 `Startup.cs`。

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFQuerying;Trusted_Connection=True;")
        .ConfigureWarnings(warnings => warnings.Throw(RelationalEventId.QueryClientEvaluationWarning));
}
```
