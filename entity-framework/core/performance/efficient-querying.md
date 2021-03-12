---
title: 有效率的查詢-EF Core
description: 使用 Entity Framework Core 進行有效率查詢的效能指南
author: roji
ms.date: 12/1/2020
uid: core/performance/efficient-querying
ms.openlocfilehash: e14837b779f2fbe8d5bf10206c6a336a952fc35b
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023870"
---
# <a name="efficient-querying"></a>有效率的查詢

有效率地查詢是廣泛的主題，其中涵蓋了範圍廣泛的索引、相關的實體載入策略以及許多其他主題。 本節詳細說明一些常見的主題，讓您的查詢更快，而使用者通常會遇到陷阱。

## <a name="use-indexes-properly"></a>適當地使用索引

查詢快速執行的主要決定因素是，查詢是否會適當地使用適當的索引：資料庫通常用來保存大量資料，而跨越整個資料表的查詢通常是嚴重效能問題的來源。 索引問題並不容易發現，因為指定的查詢是否會使用索引，因此不會立即察覺。 例如：

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#Indexes)]

找出索引問題的好方法是先找出緩慢的查詢，然後透過您資料庫的最愛工具檢查其查詢計劃。如需如何執行此作業的詳細資訊，請參閱 [ [效能診斷](xref:core/performance/performance-diagnosis) ] 頁面。 查詢計劃會顯示查詢是否流經整個資料表，或使用索引。

一般來說，使用索引或診斷與其相關的效能問題並沒有任何特殊的 EF 知識;與索引相關的一般資料庫知識，與 EF 應用程式相關，與不使用 EF 的應用程式一樣。 以下列出使用索引時要牢記在心的一些一般指導方針：

* 當索引加速查詢時，它們也會使更新變慢，因為它們必須保持在最新狀態。 避免定義不需要的索引，並考慮使用 [索引篩選準則](xref:core/modeling/indexes#index-filter) 將索引限制為數據列的子集，藉此減少此額外負荷。
* 複合索引可以加速篩選多個資料行的查詢，但也可以加速查詢，而這些查詢不會篩選所有索引資料行，視順序而定。 例如，資料行 A 和 B 上的索引會加速 A 和 B 的查詢篩選，以及只查詢篩選的查詢，但不會加速僅限 B 的查詢篩選。
* 如果查詢依據運算式篩選資料行 (例如 `price / 2`) ，就不能使用簡單的索引。 不過，您可以為您的運算式定義已 [儲存的保存資料行](xref:core/modeling/generated-properties#computed-columns) ，並在該資料行上建立索引。 某些資料庫也支援運算式索引，可以直接用來加速任何運算式的查詢篩選。
* 不同的資料庫可讓您以各種方式設定索引，而且在許多情況下，EF Core 提供者會透過流暢的 API 公開這些索引。 例如，SQL Server 提供者可讓您設定 [索引是否已叢集化，或](xref:core/providers/sql-server/indexes#clustering)設定其 [填滿因數](xref:core/providers/sql-server/indexes#fill-factor)。 如需詳細資訊，請參閱您的提供者檔。

## <a name="project-only-properties-you-need"></a>只投影您需要的屬性

EF Core 可讓您輕鬆地查詢實體實例，然後在程式碼中使用這些實例。 不過，查詢實體實例可能會經常從資料庫中取回更多資料。 請考慮下列事項：

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#ProjectEntities)]

雖然這段程式碼只需要每個 Blog 的 `Url` 屬性，但是整個 blog 實體都會被提取，而不需要的資料行則會從資料庫傳送：

```sql
SELECT [b].[BlogId], [b].[CreationDate], [b].[Name], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
```

您可以使用來將其優化， `Select` 以告訴 EF 要投影哪些資料行：

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#ProjectSingleProperty)]

產生的 SQL 只會取回所需的資料行：

```csharp
SELECT [b].[Url]
FROM [Blogs] AS [b]
```

如果您需要投影出多個資料行，請使用您想要的屬性來投影至 c # 匿名型別。

請注意，這項技術對於唯讀查詢非常有用，但如果您需要 *更新* 提取的 blog，則會變得更複雜，因為 EF 的變更追蹤只適用于實體實例。 您可以藉由附加修改過的 Blog 實例，並告訴 EF 哪些屬性已變更，但這是更先進的技術，但可能不值得您執行更新，而不需要載入整個實體。

## <a name="limit-the-resultset-size"></a>限制結果集大小

根據預設，查詢會傳回符合其篩選準則的所有資料列：

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#NoLimit)]

由於傳回的資料列數目取決於您資料庫中的實際資料，因此不可能知道要從資料庫載入多少資料、結果會佔用多少記憶體，以及處理這些結果時所產生的額外負載 (例如，透過網路) 將它們傳送至使用者瀏覽器。 十分，測試資料庫通常包含少量的資料，因此所有專案在測試時都能正常運作，但當查詢開始在真實世界的資料上執行，而且傳回許多資料列時，效能問題突然出現。

因此，通常值得考慮限制結果的數目：

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#Limit25)]

您的 UI 至少會顯示一則訊息，指出資料庫 (中可能存在更多資料列，並允許) 以其他方式進行抓取。 全方位的解決方案會執行 *分頁*，其中您的 UI 一次只會顯示特定數目的資料列，並允許使用者視需要前進到下一個頁面;這通常會結合 <xref:System.Linq.Enumerable.Take%2A> 和 <xref:System.Linq.Enumerable.Skip%2A> 運算子，以在每次都選取 resultset 中的特定範圍。

## <a name="avoid-cartesian-explosion-when-loading-related-entities"></a>在載入相關實體時避免笛卡兒爆炸

在關係資料庫中，所有相關的實體都會透過在單一查詢中導入聯結的方式載入。

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

如果一般的 blog 有多個相關的文章，這些貼文的資料列將會複製 blog 的資訊。 這項重複會導致所謂的「笛卡兒爆炸」問題。 載入更多一對多關聯性時，重複的資料量可能會成長，而且會對應用程式的效能造成不良影響。

EF 可透過使用「分割查詢」來避免此效果，這會透過個別查詢來載入相關實體。 如需詳細資訊，請閱讀 [分割和單一查詢的相關檔](xref:core/querying/single-split-queries)。

> [!NOTE]
> 目前的 [分割查詢](xref:core/querying/single-split-queries) 執行會執行每個查詢的往返。 我們打算在未來改進這項功能，並在單一往返中執行所有查詢。

## <a name="load-related-entities-eagerly-when-possible"></a>盡可能立即載入相關實體

建議您先閱讀 [相關實體的專用頁面](xref:core/querying/related-data) ，再繼續進行本節。

處理相關的實體時，我們通常會事先知道需要載入的專案：一般範例會載入一組特定的 Blog，以及所有的貼文。 在這些情況下，最好是使用積極式 [載入](xref:core/querying/related-data/eager)，讓 EF 可以在一個往返中提取所有必要的資料。 EF Core 5.0 中引進的 [篩選包含](xref:core/querying/related-data/eager#filtered-include) 功能，也可讓您限制要載入的相關實體，同時讓載入程式保持積極，因此在單一往返中雖可行：

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs#FilteredInclude)]

在其他情況下，我們可能不知道我們在取得其主體實體之前需要哪些相關實體。 例如，在載入某些 Blog 時，我們可能需要查閱一些其他資料來源（可能是 webservice），以瞭解我們是否對該 Blog 的文章感興趣。 在這些情況下，您可以使用[明確](xref:core/querying/related-data/explicit)[或消極](xref:core/querying/related-data/lazy)式載入來個別提取相關的實體，並填入 Blog 的貼文導覽。 請注意，由於這些方法並不積極，因此需要對資料庫進行額外的往返，也就是緩慢的來源;根據您的特定案例，只要一律載入所有貼文，而不是執行額外的往返，並選擇性地只取得您需要的貼文，可能會更有效率。

### <a name="beware-of-lazy-loading"></a>注意消極式載入

消極式[載入](xref:core/querying/related-data/lazy)通常看起來像是一個非常有用的方式來撰寫資料庫邏輯，因為 EF Core 會在程式碼存取資料庫時，從資料庫自動載入相關實體。 這可避免載入不需要的相關實體 (例如 [明確載入](xref:core/querying/related-data/explicit)) ，而且看似讓程式設計人員不必全部處理相關的實體。 不過，消極式載入特別容易產生不必要的額外往返，而導致應用程式變慢。

請考慮下列事項：

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#NPlusOne)]

這種看似無害的程式碼會逐一查看所有的 blog 和其貼文，並將其列印出來。開啟 EF Core 的 [語句記錄](xref:core/logging-events-diagnostics/index) 會顯示下列各項：

```console
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (1ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      SELECT [b].[BlogId], [b].[Rating], [b].[Url]
      FROM [Blogs] AS [b]
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (5ms) [Parameters=[@__p_0='1'], CommandType='Text', CommandTimeout='30']
      SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Title]
      FROM [Post] AS [p]
      WHERE [p].[BlogId] = @__p_0
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (1ms) [Parameters=[@__p_0='2'], CommandType='Text', CommandTimeout='30']
      SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Title]
      FROM [Post] AS [p]
      WHERE [p].[BlogId] = @__p_0
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (1ms) [Parameters=[@__p_0='3'], CommandType='Text', CommandTimeout='30']
      SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Title]
      FROM [Post] AS [p]
      WHERE [p].[BlogId] = @__p_0

... and so on
```

這其中發生了什麼狀況？ 為什麼會針對上述的簡單迴圈傳送這些查詢？ 使用消極式載入時，只有在存取文章屬性時，才會將 Blog 的貼文 (延遲) 載入;如此一來，內部 foreach 中的每個反復專案都會在其本身的往返中觸發額外的資料庫查詢。 如此一來，在初始查詢載入所有的 blog 之後，我們就會在 *每個 blog* 中有另一個查詢，並載入其所有文章;這有時稱為 *N + 1* 問題，而這可能會造成極大的效能問題。

假設我們將需要所有的 blog 貼文，請改為在這裡使用積極式載入。 我們可以使用 [Include](xref:core/querying/related-data/eager#eager-loading) 運算子來執行載入，但因為我們只需要 (Blog 的 url，因此應該只 [載入) 所需的內容](xref:core/performance/efficient-querying#project-only-properties-you-need) 。 因此，我們會改為使用投影：

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#EagerlyLoadRelatedAndProject)]

這會讓 EF Core 在單一查詢中提取所有的 Blog 以及它們的貼文。 在某些情況下，使用 [分割查詢](xref:core/querying/single-split-queries)來避免笛卡兒爆炸效果也可能很有用。

> [!WARNING]
> 由於消極式載入讓您很容易不小心觸發 N + 1 問題，因此建議您避免使用它。 積極或明確的載入，可讓您在發生資料庫往返時，在原始程式碼中非常清楚。

## <a name="buffering-and-streaming"></a>緩衝處理和串流

緩衝處理是指將您所有的查詢結果載入到記憶體中，而串流表示 EF 會每次都將應用程式放入單一結果，而不會在記憶體中包含整個結果集。 原則而言，串流查詢的記憶體需求是固定的，不論查詢傳回1個數據列或 1000;另一方面，緩衝查詢需要更多的記憶體，傳回的資料列越多。 對於產生大型結果集的查詢而言，這可能是重要的效能因素。

查詢緩衝區或資料流程是否取決於其評估方式：

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#BufferingAndStreaming)]

如果您的查詢只傳回一些結果，您可能不需要擔心這一點。 但是，如果您的查詢可能會傳回大量的資料列，則值得考慮串流處理而非緩衝處理。

> [!NOTE]
> <xref:System.Linq.Enumerable.ToList%2A> <xref:System.Linq.Enumerable.ToArray%2A> 如果您想要在結果上使用另一個 LINQ 運算子，請避免使用或，這將會不必要地將所有結果緩衝到記憶體中。 請改用 <xref:System.Linq.Enumerable.AsEnumerable%2A>。

### <a name="internal-buffering-by-ef"></a>EF 的內部緩衝

在某些情況下，無論您如何評估查詢，EF 本身會在內部緩衝結果集。 發生這種情況的兩個案例如下：

* 當重試執行策略就緒時。 這樣做的目的是為了確保在稍後重試查詢時傳回相同的結果。
* 使用 [split 查詢](xref:core/querying/single-split-queries) 時，會緩衝處理除了最後一個查詢以外的所有結果集，除非 SQL Server 上已啟用 MARS。 這是因為通常無法同時啟用多個查詢結果集。

請注意，除了您透過 LINQ 運算子所造成的任何緩衝之外，也會發生此內部緩衝。 例如，如果您在 <xref:System.Linq.Enumerable.ToList%2A> 查詢上使用，且正在重試執行策略，則會將結果集載入記憶體 *兩次*：一次由 EF 在內部，而一次是 <xref:System.Linq.Enumerable.ToList%2A> 。

## <a name="tracking-no-tracking-and-identity-resolution"></a>追蹤、無追蹤和識別解析

建議您先閱讀 [追蹤和不追蹤的專用頁面，](xref:core/querying/tracking) 再繼續進行本節。

EF 預設會追蹤實體實例，以便在呼叫時，偵測並保存它們的變更 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 。 追蹤查詢的另一個影響是 EF 會偵測是否已載入資料的實例，並自動傳回追蹤的實例，而不是傳回新的實例;這稱為 *識別解析*。 從效能的觀點來看，變更追蹤表示下列各項：

* EF 內部會維護追蹤實例的字典。 載入新的資料時，EF 會檢查字典以查看是否已針對該實體的索引鍵追蹤實例， (識別解析) 。 載入查詢的結果時，字典維護和查閱需要一些時間。
* 在將載入的實例處理至應用程式之前，EF 會 *快照* 該實例，並在內部保留快照集。 當 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 呼叫時，應用程式的實例會與快照集進行比較，以探索要保存的變更。 快照集會佔用更多的記憶體，而且快照集進程本身需要時間。有時可能會透過 [值](xref:core/modeling/value-comparers)比較子來指定不同且可能更有效率的快照集行為，或使用變更追蹤 proxy 來完全略過快照集進程 (但這是) 的一組缺點。

在未將變更存回資料庫的唯讀案例中，您可以使用 [無追蹤查詢](xref:core/querying/tracking#no-tracking-queries)來避免上述的負荷。 不過，由於沒有追蹤查詢不會執行識別解析，因此多個其他載入的資料列所參考的資料庫資料列將會具體化為不同的實例。

為了說明這一點，假設我們從資料庫載入大量的貼文，以及每一篇文章所參考的 Blog。 如果出現100文章參考相同的 Blog，追蹤查詢會透過識別解析來偵測到此情況，而且所有 Post 實例都會參考相同的重複複製的 Blog 實例。 相反地，無追蹤查詢會重複相同的 Blog 100 次，而且應用程式程式碼必須據以撰寫。

以下是比較追蹤的結果，以及每個包含20篇文章的查詢載入10個 Blog 的追蹤行為。 您[可以從這裡取得原始程式碼](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Benchmarks/QueryTrackingBehavior.cs)，並將其作為您自己的度量基礎。

|       方法 | NumBlogs | NumPostsPerBlog |       平均數 |    錯誤 |   StdDev |     Median | 外觀比例 | RatioSD |   Gen 0 |   Gen 1 | Gen 2 | 已配置 |
|------------- |--------- |---------------- |-----------:|---------:|---------:|-----------:|------:|--------:|--------:|--------:|------:|----------:|
|   AsTracking |       10 |              20 | 1414.7 美國 | 27.20 美國 | 45.44 美國 | 1405.5 美國 |  1.00 |    0.00 | 60.5469 | 13.6719 |     - | 380.11 KB |
| AsNoTracking |       10 |              20 |   993.3 美國 | 24.04 美國 | 65.40 美國 |   966.2 美國 |  0.71 |    0.05 | 37.1094 |  6.8359 |     - | 232.89 KB |

最後，您可以使用無追蹤查詢，然後將傳回的實例附加至內容，以指定要進行的變更，來執行更新，而不會造成變更追蹤的額外負荷。 這會將變更追蹤的負擔從 EF 轉移給使用者，而且只有在變更追蹤的額外負荷已顯示為無法透過分析或基準測試來接受時，才會進行嘗試。

## <a name="using-raw-sql"></a>使用原始 SQL

在某些情況下，您的查詢會有更優化的 SQL，但 EF 不會產生。 當 SQL 結構是不支援的資料庫的擴充功能，或只是因為 EF 尚未轉譯時，就會發生這種情況。 在這些情況下，以手動方式撰寫 SQL 可提供大幅的效能提升，而且 EF 支援數種方式來執行這項作業。

* [直接在您的查詢中](xref:core/querying/raw-sql)使用原始 SQL，例如 via <xref:Microsoft.EntityFrameworkCore.RelationalQueryableExtensions.FromSqlRaw%2A> 。 EF 甚至還可讓您使用一般 LINQ 查詢來撰寫原始 SQL，讓您只在原始 SQL 中表達查詢的一部分。 當原始 SQL 只需要在程式碼基底的單一查詢中使用時，這是很好的技巧。
* 定義 [使用者定義函數](xref:core/querying/database-functions) (UDF) ，然後從您的查詢中呼叫該函式。 請注意，從5.0 開始，EF 會讓 Udf 傳回完整的結果集，這些結果稱為資料表值函式 (Tvf) -而且也可將對應至函式 `DbSet` ，使其看起來就像是另一個資料表。
* 在您的查詢中定義資料庫檢視和查詢。 請注意，與函式不同的是，views 無法接受參數。

> [!NOTE]
> 原始 SQL 通常在確定 EF 無法產生您想要的 SQL 之後，以及效能對於指定的查詢是否很重要時，通常會用來做為最後的手段。 使用原始 SQL 會帶來可觀的維護缺點。

## <a name="asynchronous-programming"></a>非同步程式設計

一般而言，為了讓您的應用程式能夠進行調整，請務必一律使用非同步 Api，而不是同步一個 (例如， <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> 而不是 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>) 。 同步 Api 會在資料庫 i/o 期間封鎖執行緒，增加執行緒的需求，以及必須發生的執行緒內容切換數目。

如需詳細資訊，請參閱 [非同步程式設計](xref:core/miscellaneous/async)上的頁面。

> [!WARNING]
> 避免在相同的應用程式中混用同步和非同步程式碼-很容易不小心觸發微妙的執行緒集區耗盡問題。

## <a name="additional-resources"></a>其他資源

請參閱 null 比較檔頁面的 [效能區段](xref:core/querying/null-comparisons#writing-performant-queries) ，以取得比較可為 null 值時的一些最佳作法。
