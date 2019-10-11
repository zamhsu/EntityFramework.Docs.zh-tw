---
title: 複雜查詢運算子-EF Core
author: smitpatel
ms.date: 10/03/2019
ms.assetid: 2e187a2a-4072-4198-9040-aaad68e424fd
uid: core/querying/complex-query-operators
ms.openlocfilehash: 350a7fa6a3ee1de16bad4b63e10842f9356a1b60
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72186257"
---
# <a name="complex-query-operators"></a>複雜查詢運算子

語言整合式查詢（LINQ）包含許多複雜運算子，可結合多個資料來源或執行複雜的處理。 並非所有的 LINQ 運算子在伺服器端都有適當的翻譯。 有時候，一個表單中的查詢會轉譯成伺服器，但如果是以不同的格式撰寫，即使結果相同也是一樣。 此頁面描述一些複雜運算子和其支援的變化。 在未來的版本中，我們可以辨識更多模式並新增其對應的翻譯。 也請務必記住，翻譯支援會因提供者而異。 在 SqlServer 中轉譯的特定查詢，對 SQLite 資料庫可能無法使用。

> [!TIP]
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) \(英文\)。

## <a name="join"></a>Join

LINQ Join 運算子可讓您根據每個來源的索引鍵選取器來連接兩個數據源，並在索引鍵符合時產生值的元組。 它自然轉譯為關係資料庫上的 `INNER JOIN`。 雖然 LINQ 聯結具有外部和內部索引鍵選取器，但資料庫需要單一聯結條件。 所以 EF Core 藉由比較外部索引鍵選取器與內部索引鍵選取器是否相等來產生聯結條件。 此外，如果索引鍵選取器是匿名型別，EF Core 會產生聯結條件以比較相等性元件。

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#Join)]

```SQL
SELECT [p].[PersonId], [p].[Name], [p].[PhotoId], [p0].[PersonPhotoId], [p0].[Caption], [p0].[Photo]
FROM [PersonPhoto] AS [p0]
INNER JOIN [Person] AS [p] ON [p0].[PersonPhotoId] = [p].[PhotoId]
```

## <a name="groupjoin"></a>GroupJoin

LINQ GroupJoin 運算子可讓您連接類似于聯結的兩個數據源，但它會建立一組內部值以符合專用項目。 執行如下列範例所示的查詢，會產生 `Blog` @ no__t-1 @ no__t-2 的結果。 由於資料庫（尤其是關係資料庫）無法代表用戶端物件的集合，因此在許多情況下，GroupJoin 不會轉譯為伺服器。 您必須從伺服器取得所有資料，才能執行 GroupJoin，而不需要特殊的選取器（下一個查詢）。 但是，如果選取器限制選取的資料，則從伺服器提取所有資料可能會導致效能問題（下面的第二個查詢）。 這就是為什麼 EF Core 不會轉譯 GroupJoin 的原因。

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupJoin)]

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupJoinComposed)]

## <a name="selectmany"></a>SelectMany

LINQ SelectMany 運算子可讓您列舉每個專用項目的集合選取器，並從每個資料來源產生值的元組。 在某種情況下，它是聯結，但不含任何條件，因此每個專用項目都與集合來源中的元素連接。 根據集合選取器與外部資料源相關的方式，SelectMany 可以轉譯成伺服器端上的各種不同查詢。

### <a name="collection-selector-doesnt-reference-outer"></a>集合選取器未參考外部

當集合選取器未參考來自外部來源的任何專案時，結果會是這兩個數據源的笛卡兒乘積。 它會轉譯為關係資料庫中的 `CROSS JOIN`。

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToCrossJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
CROSS JOIN [Posts] AS [p]
```

### <a name="collection-selector-references-outer-in-a-where-clause"></a>Where 子句中的集合選取器參考外部

當集合選取器具有 where 子句（其參考外部專案）時，EF Core 會將它轉譯為資料庫聯結，並使用述詞做為聯結條件。 一般來說，在外部專案上使用集合導覽做為集合選取器時，就會發生這種情況。 如果專用項目的集合是空的，則不會針對該專用項目產生任何結果。 但是，如果在集合選取器上套用 `DefaultIfEmpty`，則專用項目會與內部元素的預設值連接。 基於這項差異，這類查詢會在不 `DefaultIfEmpty` 時轉譯為 `INNER JOIN`，而在套用 `DefaultIfEmpty` 時，則會轉譯為 `LEFT JOIN`。

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
INNER JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

### <a name="collection-selector-references-outer-in-a-non-where-case"></a>集合選取器參考非 where 案例中的外部

當集合選取器參考不在 where 子句中的專用項目時（如上述案例所示），它不會轉譯成資料庫聯結。 這就是為什麼我們需要針對每個專用項目評估集合選取器。 它會轉譯為許多關係資料庫中的 `APPLY` 作業。 如果專用項目的集合是空的，則不會針對該專用項目產生任何結果。 但是，如果在集合選取器上套用 `DefaultIfEmpty`，則專用項目會與內部元素的預設值連接。 基於這項差異，這類查詢會在不 `DefaultIfEmpty` 時轉譯為 `CROSS APPLY`，而在套用 `DefaultIfEmpty` 時，則會轉譯為 `OUTER APPLY`。 SQLite 之類的某些資料庫不支援 @no__t 0 的運算子，因此這類查詢可能無法轉譯。

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToApply)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
CROSS APPLY [Posts] AS [p]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
OUTER APPLY [Posts] AS [p]
```

## <a name="groupby"></a>GroupBy

LINQ GroupBy 運算子會建立類型 `IGrouping<TKey, TElement>` 的結果，其中 `TKey` 和 `TElement` 可以是任意類型。 此外，`IGrouping` 會實 `IEnumerable<TElement>`，這表示您可以在群組之後使用任何 LINQ 運算子來撰寫它。 因為沒有任何資料庫結構可以代表 `IGrouping`，所以 GroupBy 運算子在大多數情況下都沒有轉譯。 當匯總運算子套用至每個群組（傳回純量）時，它可以轉譯為關係資料庫中的 SQL `GROUP BY`。 SQL `GROUP BY` 也有其限制。 您只需要以純量值群組。 投影只能包含群組索引鍵資料行，或套用至資料行的任何匯總。 EF Core 可識別此模式，並將它轉譯為伺服器，如下列範例所示：

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupBy)]

```SQL
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
```

EF Core 也會翻譯查詢，其中群組上的匯總運算子會出現在 Where 或 OrderBy （或其他排序） LINQ 運算子中。 它會針對 where 子句使用 SQL 中的 `HAVING` 子句。 套用 GroupBy 運算子之前的查詢部分，可以是任何複雜的查詢，只要它可以轉譯成伺服器即可。 此外，在群組查詢上套用匯總運算子，以從產生的來源移除群組之後，您可以像任何其他查詢一樣在其上撰寫。

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupByFilter)]

```SQL
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
HAVING COUNT(*) > 0
ORDER BY [p].[AuthorId]
```

EF Core 支援的匯總運算子如下所示

- Average
- Count
- LongCount
- max
- Min
- Sum

## <a name="left-join"></a>左方聯結

雖然 Left Join 不是 LINQ 運算子，但是關係資料庫具有左方聯結的概念，這通常用於查詢中。 LINQ 查詢中的特定模式會提供與伺服器上 @no__t 0 相同的結果。 EF Core 可識別這種模式，並在伺服器端產生對等的 `LEFT JOIN`。 此模式牽涉到在資料來源之間建立 GroupJoin，然後在群組來源上使用 SelectMany 運算子搭配 DefaultIfEmpty 來簡維群組，以便在內部沒有相關元素時符合 null。 下列範例會顯示該模式的外觀，以及它所產生的內容。

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#LeftJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

上述模式會在運算式樹狀架構中建立複雜的結構。 因此，EF Core 要求您在緊接在運算子後面的步驟中，將 GroupJoin 運算子的群組結果壓平合併。 即使使用 GroupJoin-DefaultIfEmpty-SelectMany，但在不同的模式下，我們也無法將其識別為左方聯結。
