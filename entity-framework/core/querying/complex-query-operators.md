---
title: 複雜查詢運算子 - EF 核心
author: smitpatel
ms.date: 10/03/2019
ms.assetid: 2e187a2a-4072-4198-9040-aaad68e424fd
uid: core/querying/complex-query-operators
ms.openlocfilehash: 44c2695ea003da043925740a52596fd27da638f8
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "78417740"
---
# <a name="complex-query-operators"></a>複雜查詢運算子

語言整合查詢 (LINQ) 包含許多複雜的運算子,它們合併多個資料源或執行複雜的處理。 並非所有 LINQ 運算子在伺服器端都有合適的轉換。 有時,一種形式的查詢將轉換為伺服器,但如果以不同形式編寫,即使結果相同也不會翻譯。 本頁介紹一些複雜的運算元及其支援的變體。 在以後的版本中,我們可能會識別更多模式並添加相應的翻譯。 請務必記住,翻譯支援因供應商而異。 在 SqlServer 中翻譯的特定查詢可能不適用於 SQLite 資料庫。

> [!TIP]
> 您可以在 GitHub 上查看本文[的範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying)。

## <a name="join"></a>Join

LINQ Join 運算子允許您基於每個源的鍵選擇器連接兩個資料來源,在鍵匹配時生成一組值。 它自然地轉換為`INNER JOIN`關係資料庫。 雖然 LINQ 聯接具有外部和內鍵選擇器,但資料庫需要單個聯接條件。 因此,EF Core 通過將外鍵選擇器與內鍵選擇器進行比較來生成聯接條件,以便相等。 此外,如果鍵選擇器是匿名類型,EF Core 將生成聯接條件以明智地比較相等元件。

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#Join)]

```SQL
SELECT [p].[PersonId], [p].[Name], [p].[PhotoId], [p0].[PersonPhotoId], [p0].[Caption], [p0].[Photo]
FROM [PersonPhoto] AS [p0]
INNER JOIN [Person] AS [p] ON [p0].[PersonPhotoId] = [p].[PhotoId]
```

## <a name="groupjoin"></a>GroupJoin

LINQ GroupJoin 運算子允許您連接兩個類似於 Join 的數據源,但它創建一組內部值以匹配外部元素。 執行查詢(例如以下範例)將產生的結果`Blog` & `IEnumerable<Post>`。 由於資料庫(尤其是關係資料庫)無法表示客戶端物件的集合,因此 GroupJoin 在許多情況下不會轉換為伺服器。 它要求您從伺服器獲取所有資料,無需特殊的選擇器(下面的第一個查詢)即可執行 GroupJoin。 但是,如果選擇器正在限制正在選擇的數據,則從伺服器獲取所有數據可能會導致性能問題(下面的第二個查詢)。 這就是為什麼 EF 核心不翻譯 GroupJoin 的原因。

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupJoin)]

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupJoinComposed)]

## <a name="selectmany"></a>SelectMany

LINQ SelectMany 運算子允許您透過每個外部元素的集合選擇器枚舉,並從每個資料來源生成值組。 從某種意義上說,它是聯接,但沒有任何條件,因此每個外部元素都與集合源中的元素連接。 根據集合選擇器與外部數據源的關係,SelectMany 可以轉換為伺服器端的各種不同查詢。

### <a name="collection-selector-doesnt-reference-outer"></a>集合選擇器不引用外部

當集合選擇器不引用來自外部源的任何內容時,結果是兩個數據源的點菜產品。 它轉換為`CROSS JOIN`關係資料庫。

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToCrossJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
CROSS JOIN [Posts] AS [p]
```

### <a name="collection-selector-references-outer-in-a-where-clause"></a>集合選擇器引用 where 子句中的外部

當集合選擇器具有引用外部元素的 where 子句時,EF Core 將其轉換為資料庫聯接,並使用謂詞作為聯接條件。 通常,當在外部元素上使用集合導航作為集合選擇器時,會出現這種情況。 如果外部元素的集合為空,則不會為該外部元素生成任何結果。 但是,`DefaultIfEmpty`如果應用於集合選擇器,則外部元素將與內部元素的預設值連接。 由於這種區別,這種查詢`INNER JOIN`在`DefaultIfEmpty`不存在`LEFT JOIN`時`DefaultIfEmpty`和 何時 應用時被翻譯為。

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
INNER JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

### <a name="collection-selector-references-outer-in-a-non-where-case"></a>收集選擇器引言外部非 where 的情況下

當集合選擇器引用外部元素時,該元素不在 where 子句中(如上文所述),它不會轉換為資料庫聯接。 這就是為什麼我們需要評估每個外部元素的集合選擇器。 它轉換為`APPLY`許多關係資料庫中的操作。 如果外部元素的集合為空,則不會為該外部元素生成任何結果。 但是,`DefaultIfEmpty`如果應用於集合選擇器,則外部元素將與內部元素的預設值連接。 由於這種區別,這種查詢`CROSS APPLY`在`DefaultIfEmpty`不存在`OUTER APPLY`時`DefaultIfEmpty`和 何時 應用時被翻譯為。 某些資料庫(如 SQLite)不`APPLY`支援 運算符,因此可能無法翻譯此類查詢。

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

LINQ GroupBy 運算子建立`IGrouping<TKey, TElement>`類型`TKey`的結果`TElement`, 其中 和可以是任意類型。 此外,`IGrouping``IEnumerable<TElement>`實現 ,這意味著您可以在分組后使用任何 LINQ 運算子在它上進行組合。 由於沒有資料庫結構可以表示`IGrouping`, GroupBy 運算符在大多數情況下沒有翻譯。 當聚合運算符應用於返回標量的每個組時,可以在關係資料庫中將其轉換為 SQL。 `GROUP BY` SQL`GROUP BY`也受到限制。 它要求您僅按標量值進行分組。 投影只能包含對鍵列或應用於列的任何聚合。 EF Core 識別此模式並將其轉換為伺服器,如以下範例所示:

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupBy)]

```SQL
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
```

EF Core 還轉換分組上的聚合運算符顯示在"位置或訂單By(或其他排序)LINQ"運算符中的查詢。 它使用`HAVING`SQL 中子句作為 where 子句。 在應用 GroupBy 運算符之前,查詢的部分是任何複雜的查詢,只要可以轉換為伺服器。 此外,一旦在分組查詢上應用聚合運算符以從生成的源中刪除分組,就可以像任何其他查詢一樣在其上面進行組合。

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupByFilter)]

```SQL
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
HAVING COUNT(*) > 0
ORDER BY [p].[AuthorId]
```

聚合運算符 EF Core 支援所選取

- Average
- Count
- LongCount
- 最大值
- 最小值
- Sum

## <a name="left-join"></a>左側聯結

雖然左聯接不是 LINQ 運算符,但關係資料庫具有「左聯接」的概念,該概念在查詢中經常使用。 LINQ 查詢中的特定模式給出的結果與伺服器上的`LEFT JOIN`a 相同。 EF Core 識別此類模式,`LEFT JOIN`並在伺服器 端生成等效模式。 該模式涉及在資料源之間創建 GroupJoin,然後使用分組源上的 DefaultIfEmpty 的 SelectMany 運算符將分組拼合,以在內部沒有相關元素時匹配 null。 下面的示例顯示了該模式的外觀及其生成。

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#LeftJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

上述模式在表達式樹中創建一個複雜的結構。 因此,EF Core 要求您在緊接運算符的步驟中拼出 GroupJoin 運算符的分組結果。 即使使用組 Join-預設 IfEmpty-SelectMany,但採用不同的模式,我們可能不會將其標識為「左聯接」。
