---
title: 用戶端與伺服器評估-EF Core
description: 使用 Entity Framework Core 查詢的用戶端和伺服器評估
author: smitpatel
ms.date: 11/09/2020
uid: core/querying/client-eval
ms.openlocfilehash: a1f37cb4f9c10f825d7dcbe54e9eecf75fa109a3
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023779"
---
# <a name="client-vs-server-evaluation"></a>用戶端與伺服器評估

一般而言，Entity Framework Core 會盡可能地嘗試評估伺服器上的查詢。 EF Core 會將查詢的部分轉換成可在用戶端上評估的參數。 查詢的其餘部分 (以及產生的參數) 會提供給資料庫提供者，以判斷要在伺服器上評估的對等資料庫查詢。 EF Core 支援最上層投影的部分用戶端評估， (基本上是最後一個) 呼叫 `Select()` 。 如果查詢中的最上層投影無法轉譯為伺服器，EF Core 將會從伺服器提取所有必要的資料，並在用戶端上評估其餘的查詢部分。 如果 EF Core 在無法轉譯為伺服器的最上層投影以外的任何地方偵測到運算式，則會擲回執行時間例外狀況。 查看 [查詢的運作方式](xref:core/querying/how-query-works) ，以瞭解 EF Core 如何判斷無法轉譯至伺服器的內容。

> [!NOTE]
> 在3.0 版之前，Entity Framework Core 支援在查詢中的任何位置進行用戶端評估。 如需詳細資訊，請參閱「 [先前的版本」一節](#previous-versions)。

> [!TIP]
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Querying/ClientEvaluation) \(英文\)。

## <a name="client-evaluation-in-the-top-level-projection"></a>最上層投影中的用戶端評估

在下列範例中，helper 方法是用來標準化從 SQL Server 資料庫傳回的 blog 的 Url。 因為 SQL Server 提供者無法深入瞭解此方法的執行方式，所以無法將它轉譯成 SQL。 查詢的所有其他層面都會在資料庫中評估，但 `URL` 透過這個方法所傳回的會在用戶端上完成。

[!code-csharp[Main](../../../samples/core/Querying/ClientEvaluation/Program.cs#ClientProjection)]

[!code-csharp[Main](../../../samples/core/Querying/ClientEvaluation/Program.cs#ClientMethod)]

## <a name="unsupported-client-evaluation"></a>不支援的用戶端評估

雖然用戶端評估很有用，但有時可能會導致效能不佳。 請考慮下列查詢，在其中，helper 方法現在用於 where 篩選準則中。 因為無法在資料庫中套用篩選，所以必須將所有資料提取至記憶體中，以將篩選套用至用戶端。 根據篩選和伺服器上的資料量，用戶端評估可能會導致效能不佳。 因此，Entity Framework Core 會封鎖這類用戶端評估，並擲回執行時間例外狀況。

[!code-csharp[Main](../../../samples/core/Querying/ClientEvaluation/Program.cs#ClientWhere)]

## <a name="explicit-client-evaluation"></a>明確用戶端評估

在某些情況下，您可能需要明確地強制執行用戶端評估，如下所示

- 資料量很小，因此在用戶端上進行評估不會產生顯著的效能影響。
- 使用的 LINQ 運算子沒有伺服器端轉譯。

在這種情況下，您可以藉由呼叫方法（例如 `AsEnumerable` 或 `ToList` (） `AsAsyncEnumerable` 或 `ToListAsync` 非同步) 來明確加入宣告用戶端評估。 藉由使用，您將會 `AsEnumerable` 串流處理結果，但使用 `ToList` 會藉由建立清單來產生緩衝，這也會佔用額外的記憶體。 但是，如果您要列舉多次，則將結果儲存在清單中有助於更多，因為資料庫只有一個查詢。 根據特定使用方式，您應該評估哪一種方法對案例而言更有用。

[!code-csharp[Main](../../../samples/core/Querying/ClientEvaluation/Program.cs#ExplicitClientEvaluation)]

> [!TIP]
> 如果您正在使用 `AsAsyncEnumerable` ，而且想要進一步在用戶端上撰寫查詢，則可以使用定義非同步可列舉值運算子的 [System.object. Async](https://www.nuget.org/packages/System.Interactive.Async/) 程式庫。 如需詳細資訊，請參閱 [用戶端 linq 運算子](xref:core/miscellaneous/async#client-side-async-linq-operators)。

## <a name="potential-memory-leak-in-client-evaluation"></a>用戶端評估可能發生記憶體流失

由於查詢轉譯和編譯的成本很高，因此 EF Core 會快取已編譯的查詢計劃。 快取的委派可能會在執行最上層投影的用戶端評估時使用用戶端程式代碼。 EF Core 會為樹狀結構的用戶端評估部分產生參數，並藉由取代參數值來重複使用查詢計劃。 但是運算式樹狀架構中的某些常數無法轉換成參數。 如果快取的委派包含這類常數，則這些物件將無法進行垃圾收集，因為它們仍在參考中。 如果這類物件包含 DbCoNtext 或其他服務，則可能會導致應用程式的記憶體使用量隨著時間成長。 這種行為通常會造成記憶體流失的跡象。 EF Core 會在無法使用目前資料庫提供者對應的型別常數時擲回例外狀況。 常見原因和其解決方案如下：

- **使用實例方法**：在用戶端投影中使用實例方法時，運算式樹狀架構會包含實例的常數。 如果您的方法未使用實例中的任何資料，請考慮將方法設為靜態。 如果您需要方法主體中的實例資料，請將特定資料當作引數傳遞給方法。
- **將常數引數傳遞給方法**：這種情況通常會 `this` 在用戶端方法的引數中使用。 請考慮將引數分割成多個純量引數（可由資料庫提供者對應）。
- **其他常數**：如果常數是在其他任何情況下，您可以評估是否需要常數來處理。 如果必須有常數，或如果您無法使用上述案例中的解決方案，請建立本機變數來儲存值，並在查詢中使用區域變數。 EF Core 會將本機變數轉換成參數。

## <a name="previous-versions"></a>舊版

下一節適用于3.0 之前的 EF Core 版本。

舊版 EF Core 版本在查詢的任何部分中支援用戶端評估，而不只是最上層的投射。 這就是為什麼類似于未 [受支援用戶端評估](#unsupported-client-evaluation) 章節的查詢會正確運作。 由於這種行為可能會導致未察覺的效能問題，因此 EF Core 會記錄用戶端評估警告。 如需有關如何查看記錄輸出的詳細資訊，請參閱 [記錄](xref:core/logging-events-diagnostics/index)。

（選擇性） EF Core 可讓您變更預設行為，以在進行用戶端評估時擲回例外狀況或不執行任何動作 (但投影) 中除外。 例外狀況擲回行為會使它類似于3.0 中的行為。 若要變更此行為，您必須在為內容設定選項時（通常是在中）設定警告， `DbContext.OnConfiguring` `Startup.cs` 如果您使用的是 ASP.NET Core，則需要設定。

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFQuerying;Trusted_Connection=True;")
        .ConfigureWarnings(warnings => warnings.Throw(RelationalEventId.QueryClientEvaluationWarning));
}
```
