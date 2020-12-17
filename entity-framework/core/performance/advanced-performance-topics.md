---
title: Advanced Performance 主題
description: Entity Framework Core 的 Advanced 效能主題
author: rick-anderson
ms.author: riande
ms.date: 12/9/2020
uid: core/performance/advanced-performance-topics
ms.openlocfilehash: 3c0340e1b36cbbb96d23db0633cb2eebc04dd970
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657800"
---
# <a name="advanced-performance-topics"></a>Advanced Performance 主題

## <a name="dbcontext-pooling"></a>DbContext 共用

`AddDbContextPool` 啟用實例的共用 `DbContext` 。 內容共用可以藉由重複使用內容實例來增加高規模案例的輸送量，例如 web 伺服器，而不是為每個要求建立新的實例。

使用 EF Core 的 ASP.NET Core 應用程式中的一般模式牽涉到將自訂類型註冊至相依性 <xref:Microsoft.EntityFrameworkCore.DbContext> [插入](/aspnet/core/fundamentals/dependency-injection) 容器，並透過控制器或 Razor Pages 中的函式參數取得該類型的實例。 使用函式插入時，會為每個要求建立新的內容實例。

<xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> 啟用可重複使用的內容實例集區。 若要使用內容共用，請使用 `AddDbContextPool` 方法，而不是 `AddDbContext` 在服務註冊期間：

```csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

`AddDbContextPool`使用時，在要求內容實例時，EF 會先檢查集區中是否有可用的實例。 要求處理完成之後，會重設執行個體上的任何狀態，並將執行個體本身傳回到集區。

這在概念上類似于連接共用在 ADO.NET 提供者中的運作方式，而且具有節省部分初始化內容實例成本的優點。

的 `poolSize` 參數會 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> 設定集區保留的實例數目上限。 一旦 `poolSize` 超過此限制，就不會快取新的內容實例，而且 EF 會回到依需求建立實例的非共用行為。

### <a name="limitations"></a>限制

應用程式應該進行程式碼剖析和測試，以顯示內容初始化是相當大的成本。

`AddDbContextPool` 有幾項限制可在內容的方法中完成 `OnConfiguring` 。

> [!WARNING]
> 避免在維護狀態的應用程式中使用內容共用。 例如，內容中不應該跨要求共用的私用欄位。 EF Core 只會在將內容實例新增至集區之前，重設其感知的狀態。

內容共用的運作方式是跨要求重複使用相同的內容實例。 這表示它實際上是以實例本身的 [Singleton](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) 來註冊，以便能夠保存。

內容共用適用于內容設定（包括已解決的服務）在要求之間固定的案例。 針對需要 [範圍](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) 服務的情況，或需要變更設定時，請勿使用共用。 除了在高度優化的案例中，來自共用的效能提升通常是可忽略的。

## <a name="query-caching-and-parameterization"></a>查詢快取和參數化

當 EF 收到要執行的 LINQ 查詢樹狀結構時，它必須先將該樹狀結構「編譯」到 SQL 查詢中。 因為這是相當繁重的程式，所以 EF 會根據查詢樹狀結構 *圖形* 來快取查詢：具有相同結構的查詢會在內部快取的編譯輸出中重複使用，並可略過重複的編譯。 不同的查詢仍可參考不同的 *值*，但只要這些值已正確地參數化，結構就會相同，而且快取會正常運作。

請考慮下列兩個查詢：

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#QueriesWithConstants)]

因為運算式樹狀架構包含不同的常數，所以運算式樹狀架構會有所不同，而且每個查詢都會由 EF Core 分別進行編譯。 此外，每個查詢都會產生稍微不同的 SQL 命令：

```sql
SELECT TOP(1) [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
WHERE [b].[Name] = N'blog1'

SELECT TOP(1) [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
WHERE [b].[Name] = N'blog2'
```

因為 SQL 不同，所以您的資料庫伺服器可能也需要針對這兩個查詢產生查詢計劃，而不是重複使用相同的計畫。

對您的查詢進行較小的修改可能會大幅改變事物：

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#QueriesWithParameterization)]

因為現在會將 blog 名稱 *參數化*，所以兩個查詢都有相同的樹狀圖形，而且 EF 只需要編譯一次。 產生的 SQL 也會進行參數化，讓資料庫可以重複使用相同的查詢計劃：

```sql
SELECT TOP(1) [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
WHERE [b].[Name] = @__blogName_0
```

請注意，您不需要將每個查詢參數化：有一些常數的查詢都是不錯的，事實上，資料庫 (和 EF) 有時可以在查詢參數化時，對無法執行的常數執行某些優化。 請參閱動態架構 [查詢](#dynamically-constructed-queries) 的一節，以取得適當的參數化很重要的範例。

> [!NOTE]
> EF Core 的 [事件計數器](xref:core/logging-events-diagnostics/event-counters) 會報告查詢快取點擊率。 在一般的應用程式中，此計數器會在程式啟動後立即達到100%，一旦大部分的查詢都至少執行一次。 如果此計數器維持穩定低於100%，則表示您的應用程式可能會執行一些與查詢快取不一致的情況，這是很好的想法。

> [!NOTE]
> 資料庫如何管理快取查詢計劃與資料庫相依。 例如，SQL Server 會隱含地維護 LRU 查詢計劃快取，而于 postgresql 不會 (但備妥的語句可能會產生非常類似的 end 效果) 。 如需詳細資訊，請參閱您的資料庫檔案。

## <a name="dynamically-constructed-queries"></a>動態構建的查詢

在某些情況下，必須以動態方式來建立 LINQ 查詢，而不是在原始程式碼中直接指定它們。 例如，在從用戶端接收任意查詢詳細資料的網站中，有開放式查詢運算子 (排序、篩選、分頁 ... ) ，就會發生這種情況。基本上，如果正確地完成，以動態方式建立的查詢可以像一般的查詢一樣有效率 (不過，您無法使用已編譯的查詢優化來進行動態查詢) 。 不過在實務上，它們經常是效能問題的來源，因為很容易就會不小心地產生具有每次不同圖形的運算式樹狀架構。

下列範例會使用兩種技術來動態地建立查詢; `Where` 只有當指定的參數不是 null 時，才會將運算子加入至查詢。 請注意，這不是用來以動態方式建立查詢的好用案例，而是為了簡單起見而使用它：

### <a name="with-constant"></a>[使用常數](#tab/with-constant)

[!code-csharp[Main](../../../samples/core/Benchmarks/DynamicallyConstructedQueries.cs?name=WithConstant&highlight=14-24)]

### <a name="with-parameter"></a>[With 參數](#tab/with-parameter)

[!code-csharp[Main](../../../samples/core/Benchmarks/DynamicallyConstructedQueries.cs?name=WithParameter&highlight=14)]

***

這兩種技術的效能評定可提供下列結果：

|        方法 |       平均數 |    錯誤 |    StdDev |   Gen 0 |  Gen 1 | Gen 2 | 已配置 |
|-------------- |-----------:|---------:|----------:|--------:|-------:|------:|----------:|
|  WithConstant | 1096.7 美國 | 12.54 美國 |  11.12 美國 | 13.6719 | 1.9531 |     - |  83.91 KB |
| WithParameter |   570.8 美國 | 42.43 美國 | 124.43 美國 |  5.8594 |      - |     - |  37.16 KB |

即使毫秒差異看起來很小，但請記住，常數版本會持續干擾快取，並導致其他查詢重新編譯，同時使其變慢。

> [!NOTE]
> 除非您真的需要，否則請避免使用運算式樹狀架構 API 來建立查詢。 除了 API 的複雜度之外，在使用時，很容易就會不小心地造成顯著的效能問題。
