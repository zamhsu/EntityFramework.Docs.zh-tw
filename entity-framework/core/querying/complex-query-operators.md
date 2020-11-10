---
title: 複雜查詢運算子-EF Core
description: 使用 Entity Framework Core 時，更複雜的 LINQ 查詢運算子的深入資訊
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/complex-query-operators
ms.openlocfilehash: 84c2518972355d31cf5a6a7bafc57b44162412c8
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430478"
---
# <a name="complex-query-operators"></a>複雜查詢運算子

 (LINQ) 的語言整合式查詢包含許多複雜的運算子，這些運算子結合多個資料來源或複雜的處理。 並非所有 LINQ 運算子在伺服器端都有適當的翻譯。 有時候，一個表單中的查詢會轉譯成伺服器，但如果以不同的表單撰寫，即使結果相同也不會轉譯。 此頁面描述一些複雜運算子及其支援的變化。 在未來的版本中，我們可能會辨識更多模式，並新增其對應的翻譯。 此外，請務必記住，提供者之間的翻譯支援會有所不同。 在 SqlServer 中轉譯的特定查詢，在 SQLite 資料庫中可能無法運作。

> [!TIP]
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/ComplexQuery) \(英文\)。

## <a name="join"></a>Join

LINQ Join 運算子可讓您根據每個來源的索引鍵選取器來連接兩個數據源，並在索引鍵符合時產生值的元組。 它會自然地轉譯為 `INNER JOIN` 關係資料庫。 當 LINQ 聯結具有外部和內部索引鍵選取器時，資料庫需要單一聯結條件。 因此 EF Core 藉由比較外部索引鍵選取器與內部索引鍵選取器的相等來產生聯結條件。

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#Join)]

```sql
SELECT [p].[PersonId], [p].[Name], [p].[PhotoId], [p0].[PersonPhotoId], [p0].[Caption], [p0].[Photo]
FROM [PersonPhoto] AS [p0]
INNER JOIN [Person] AS [p] ON [p0].[PersonPhotoId] = [p].[PhotoId]
```

此外，如果索引鍵選取器是匿名型別，EF Core 會產生聯結條件來比較相等元件的相等性。

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#JoinComposite)]

```sql
SELECT [p].[PersonId], [p].[Name], [p].[PhotoId], [p0].[PersonPhotoId], [p0].[Caption], [p0].[Photo]
FROM [PersonPhoto] AS [p0]
INNER JOIN [Person] AS [p] ON ([p0].[PersonPhotoId] = [p].[PhotoId] AND ([p0].[Caption] = N'SN'))
```

## <a name="groupjoin"></a>GroupJoin

LINQ GroupJoin 運算子可讓您連接類似于聯結的兩個數據源，但是它會建立一組內部值以符合專用項目。 執行類似下列範例的查詢會產生的結果 `Blog`  &  `IEnumerable<Post>` 。 由於資料庫 (特別是關係資料庫) 無法表示用戶端物件的集合，因此在許多情況下，GroupJoin 不會轉譯成伺服器。 您必須從伺服器取得所有資料，才能進行 GroupJoin，而不需要特殊的選取器 (第一個查詢) 。 但是，如果選取器會限制選取的資料，則從伺服器提取所有資料可能會導致效能問題 (第二個查詢) 。 這就是 EF Core 不會轉譯 GroupJoin 的原因。

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#GroupJoin)]

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#GroupJoinComposed)]

## <a name="selectmany"></a>SelectMany

LINQ SelectMany 運算子可讓您列舉每個專用項目的集合選取器，並從每個資料來源產生值的元組。 一種方式是聯結，但沒有任何條件，因此每個外部專案都會與集合來源的元素連接。 根據集合選取器與外部資料源的關聯方式，SelectMany 可以轉譯成伺服器端上的各種不同查詢。

### <a name="collection-selector-doesnt-reference-outer"></a>集合選取器不參考外部

當集合選取器未參考外部來源的任何資料時，結果會是兩個數據源的笛卡兒乘積。 它會轉譯為 `CROSS JOIN` 關係資料庫中的。

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#SelectManyConvertedToCrossJoin)]

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
CROSS JOIN [Posts] AS [p]
```

### <a name="collection-selector-references-outer-in-a-where-clause"></a>集合選取器參考 where 子句中的 outer

當集合選取器具有 where 子句（參考外部專案）時，EF Core 會將它轉譯成資料庫聯結，並使用述詞做為聯結條件。 一般來說，在專用項目上使用集合導覽作為集合選取器時，會發生這種情況。 如果外部專案的集合是空的，則不會針對該專用項目產生任何結果。 但是，如果在 `DefaultIfEmpty` 集合選取器上套用，則外部專案將會與內部元素的預設值連接。 由於這種差異，這類的查詢在沒有 `INNER JOIN` 和套用時， `DefaultIfEmpty` 會轉譯為 `LEFT JOIN` `DefaultIfEmpty` 。

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#SelectManyConvertedToJoin)]

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
INNER JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

### <a name="collection-selector-references-outer-in-a-non-where-case"></a>集合選取器參考非 where 案例中的 outer

當集合選取器參考不在 where 子句中的外部專案時 (上述) 的情況下，它不會轉譯成資料庫聯結。 這就是為什麼我們需要評估每個專用項目的集合選取器。 它會轉譯為 `APPLY` 許多關係資料庫中的作業。 如果外部專案的集合是空的，則不會針對該專用項目產生任何結果。 但是，如果在 `DefaultIfEmpty` 集合選取器上套用，則外部專案將會與內部元素的預設值連接。 由於這種差異，這類的查詢在沒有 `CROSS APPLY` 和套用時， `DefaultIfEmpty` 會轉譯為 `OUTER APPLY` `DefaultIfEmpty` 。 某些資料庫（例如 SQLite）不支援 `APPLY` 運算子，因此這類查詢可能無法轉譯。

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#SelectManyConvertedToApply)]

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
CROSS APPLY [Posts] AS [p]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
OUTER APPLY [Posts] AS [p]
```

## <a name="groupby"></a>GroupBy

LINQ GroupBy 運算子會建立類型的結果 `IGrouping<TKey, TElement>` `TKey` ，而且可以 `TElement` 是任何任意型別。 此外， `IGrouping` `IEnumerable<TElement>` 也會執行，這表示您可以使用群組之後的任何 LINQ 運算子來撰寫它。 因為沒有任何資料庫結構可以表示 `IGrouping` ，所以在大部分情況下，GroupBy 運算子沒有任何轉譯。 當匯總運算子套用至傳回純量的每個群組時，可以將它轉譯為 `GROUP BY` 關係資料庫中的 SQL。 SQL 也 `GROUP BY` 有限制。 您只需以純量值群組。 投影只能包含群組索引鍵資料行或套用在資料行上的任何匯總。 EF Core 識別此模式，並將它轉譯成伺服器，如下列範例所示：

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#GroupBy)]

```sql
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
```

EF Core 也會轉譯查詢，其中群組上的匯總運算子會出現在 Where 或 OrderBy (或其他排序) LINQ 運算子中。 它會 `HAVING` 在 SQL 中針對 where 子句使用子句。 套用 GroupBy 運算子之前的查詢部分可以是任何複雜的查詢，只要它可以轉譯成伺服器即可。 此外，當您套用群組查詢的匯總運算子來移除所產生來源的群組時，您可以像任何其他查詢一樣，在其上層撰寫。

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#GroupByFilter)]

```sql
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
HAVING COUNT(*) > 0
ORDER BY [p].[AuthorId]
```

EF Core 支援的匯總運算子如下所示

- 平均
- 計數
- LongCount
- 最大值
- Min
- Sum

## <a name="left-join"></a>左方聯結

雖然左方聯結不是 LINQ 運算子，但關係資料庫具有左方聯結的概念，其通常會在查詢中使用。 LINQ 查詢中的特定模式會提供與伺服器上相同的結果 `LEFT JOIN` 。 EF Core 識別這類模式，並在伺服器端產生對等的 `LEFT JOIN` 。 此模式包含在兩個數據源之間建立 GroupJoin，然後在內部沒有相關專案時，使用 SelectMany 運算子搭配群組來源上的 DefaultIfEmpty 來比對 null，以將群組壓平合併。 下列範例會顯示該模式的外觀和產生的內容。

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#LeftJoin)]

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

上述模式會在運算式樹狀結構中建立複雜結構。 因此，EF Core 要求您在緊接著運算子之後的步驟中，將 GroupJoin 運算子的群組結果壓平合併。 即使使用了 GroupJoin-DefaultIfEmpty-SelectMany，但在不同的模式下，也無法將其識別為左方聯結。
