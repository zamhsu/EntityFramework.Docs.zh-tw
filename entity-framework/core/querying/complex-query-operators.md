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
# <a name="complex-query-operators"></a><span data-ttu-id="efb7f-102">複雜查詢運算子</span><span class="sxs-lookup"><span data-stu-id="efb7f-102">Complex Query Operators</span></span>

<span data-ttu-id="efb7f-103">語言整合查詢 (LINQ) 包含許多複雜的運算子,它們合併多個資料源或執行複雜的處理。</span><span class="sxs-lookup"><span data-stu-id="efb7f-103">Language Integrated Query (LINQ) contains many complex operators, which combine multiple data sources or does complex processing.</span></span> <span data-ttu-id="efb7f-104">並非所有 LINQ 運算子在伺服器端都有合適的轉換。</span><span class="sxs-lookup"><span data-stu-id="efb7f-104">Not all LINQ operators have suitable translations on the server side.</span></span> <span data-ttu-id="efb7f-105">有時,一種形式的查詢將轉換為伺服器,但如果以不同形式編寫,即使結果相同也不會翻譯。</span><span class="sxs-lookup"><span data-stu-id="efb7f-105">Sometimes, a query in one form translates to the server but if written in a different form doesn't translate even if the result is the same.</span></span> <span data-ttu-id="efb7f-106">本頁介紹一些複雜的運算元及其支援的變體。</span><span class="sxs-lookup"><span data-stu-id="efb7f-106">This page describes some of the complex operators and their supported variations.</span></span> <span data-ttu-id="efb7f-107">在以後的版本中,我們可能會識別更多模式並添加相應的翻譯。</span><span class="sxs-lookup"><span data-stu-id="efb7f-107">In future releases, we may recognize more patterns and add their corresponding translations.</span></span> <span data-ttu-id="efb7f-108">請務必記住,翻譯支援因供應商而異。</span><span class="sxs-lookup"><span data-stu-id="efb7f-108">It's also important to keep in mind that translation support varies between providers.</span></span> <span data-ttu-id="efb7f-109">在 SqlServer 中翻譯的特定查詢可能不適用於 SQLite 資料庫。</span><span class="sxs-lookup"><span data-stu-id="efb7f-109">A particular query, which is translated in SqlServer, may not work for SQLite databases.</span></span>

> [!TIP]
> <span data-ttu-id="efb7f-110">您可以在 GitHub 上查看本文[的範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying)。</span><span class="sxs-lookup"><span data-stu-id="efb7f-110">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="join"></a><span data-ttu-id="efb7f-111">Join</span><span class="sxs-lookup"><span data-stu-id="efb7f-111">Join</span></span>

<span data-ttu-id="efb7f-112">LINQ Join 運算子允許您基於每個源的鍵選擇器連接兩個資料來源,在鍵匹配時生成一組值。</span><span class="sxs-lookup"><span data-stu-id="efb7f-112">The LINQ Join operator allows you to connect two data sources based on the key selector for each source, generating a tuple of values when the key matches.</span></span> <span data-ttu-id="efb7f-113">它自然地轉換為`INNER JOIN`關係資料庫。</span><span class="sxs-lookup"><span data-stu-id="efb7f-113">It naturally translates to `INNER JOIN` on relational databases.</span></span> <span data-ttu-id="efb7f-114">雖然 LINQ 聯接具有外部和內鍵選擇器,但資料庫需要單個聯接條件。</span><span class="sxs-lookup"><span data-stu-id="efb7f-114">While the LINQ Join has outer and inner key selectors, the database requires a single join condition.</span></span> <span data-ttu-id="efb7f-115">因此,EF Core 通過將外鍵選擇器與內鍵選擇器進行比較來生成聯接條件,以便相等。</span><span class="sxs-lookup"><span data-stu-id="efb7f-115">So EF Core generates a join condition by comparing the outer key selector to the inner key selector for equality.</span></span> <span data-ttu-id="efb7f-116">此外,如果鍵選擇器是匿名類型,EF Core 將生成聯接條件以明智地比較相等元件。</span><span class="sxs-lookup"><span data-stu-id="efb7f-116">Further, if the key selectors are anonymous types, EF Core generates a join condition to compare equality component wise.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#Join)]

```SQL
SELECT [p].[PersonId], [p].[Name], [p].[PhotoId], [p0].[PersonPhotoId], [p0].[Caption], [p0].[Photo]
FROM [PersonPhoto] AS [p0]
INNER JOIN [Person] AS [p] ON [p0].[PersonPhotoId] = [p].[PhotoId]
```

## <a name="groupjoin"></a><span data-ttu-id="efb7f-117">GroupJoin</span><span class="sxs-lookup"><span data-stu-id="efb7f-117">GroupJoin</span></span>

<span data-ttu-id="efb7f-118">LINQ GroupJoin 運算子允許您連接兩個類似於 Join 的數據源,但它創建一組內部值以匹配外部元素。</span><span class="sxs-lookup"><span data-stu-id="efb7f-118">The LINQ GroupJoin operator allows you to connect two data sources similar to Join, but it creates a group of inner values for matching outer elements.</span></span> <span data-ttu-id="efb7f-119">執行查詢(例如以下範例)將產生的結果`Blog` & `IEnumerable<Post>`。</span><span class="sxs-lookup"><span data-stu-id="efb7f-119">Executing a query like the following example generates a result of `Blog` & `IEnumerable<Post>`.</span></span> <span data-ttu-id="efb7f-120">由於資料庫(尤其是關係資料庫)無法表示客戶端物件的集合,因此 GroupJoin 在許多情況下不會轉換為伺服器。</span><span class="sxs-lookup"><span data-stu-id="efb7f-120">Since databases (especially relational databases) don't have a way to represent a collection of client-side objects, GroupJoin doesn't translate to the server in many cases.</span></span> <span data-ttu-id="efb7f-121">它要求您從伺服器獲取所有資料,無需特殊的選擇器(下面的第一個查詢)即可執行 GroupJoin。</span><span class="sxs-lookup"><span data-stu-id="efb7f-121">It requires you to get all of the data from the server to do GroupJoin without a special selector (first query below).</span></span> <span data-ttu-id="efb7f-122">但是,如果選擇器正在限制正在選擇的數據,則從伺服器獲取所有數據可能會導致性能問題(下面的第二個查詢)。</span><span class="sxs-lookup"><span data-stu-id="efb7f-122">But if the selector is limiting data being selected then fetching all of the data from the server may cause performance issues (second query below).</span></span> <span data-ttu-id="efb7f-123">這就是為什麼 EF 核心不翻譯 GroupJoin 的原因。</span><span class="sxs-lookup"><span data-stu-id="efb7f-123">That's why EF Core doesn't translate GroupJoin.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupJoin)]

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupJoinComposed)]

## <a name="selectmany"></a><span data-ttu-id="efb7f-124">SelectMany</span><span class="sxs-lookup"><span data-stu-id="efb7f-124">SelectMany</span></span>

<span data-ttu-id="efb7f-125">LINQ SelectMany 運算子允許您透過每個外部元素的集合選擇器枚舉,並從每個資料來源生成值組。</span><span class="sxs-lookup"><span data-stu-id="efb7f-125">The LINQ SelectMany operator allows you to enumerate over a collection selector for each outer element and generate tuples of values from each data source.</span></span> <span data-ttu-id="efb7f-126">從某種意義上說,它是聯接,但沒有任何條件,因此每個外部元素都與集合源中的元素連接。</span><span class="sxs-lookup"><span data-stu-id="efb7f-126">In a way, it's a join but without any condition so every outer element is connected with an element from the collection source.</span></span> <span data-ttu-id="efb7f-127">根據集合選擇器與外部數據源的關係,SelectMany 可以轉換為伺服器端的各種不同查詢。</span><span class="sxs-lookup"><span data-stu-id="efb7f-127">Depending on how the collection selector is related to the outer data source, SelectMany can translate into various different queries on the server side.</span></span>

### <a name="collection-selector-doesnt-reference-outer"></a><span data-ttu-id="efb7f-128">集合選擇器不引用外部</span><span class="sxs-lookup"><span data-stu-id="efb7f-128">Collection selector doesn't reference outer</span></span>

<span data-ttu-id="efb7f-129">當集合選擇器不引用來自外部源的任何內容時,結果是兩個數據源的點菜產品。</span><span class="sxs-lookup"><span data-stu-id="efb7f-129">When the collection selector isn't referencing anything from the outer source, the result is a cartesian product of both data sources.</span></span> <span data-ttu-id="efb7f-130">它轉換為`CROSS JOIN`關係資料庫。</span><span class="sxs-lookup"><span data-stu-id="efb7f-130">It translates to `CROSS JOIN` in relational databases.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToCrossJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
CROSS JOIN [Posts] AS [p]
```

### <a name="collection-selector-references-outer-in-a-where-clause"></a><span data-ttu-id="efb7f-131">集合選擇器引用 where 子句中的外部</span><span class="sxs-lookup"><span data-stu-id="efb7f-131">Collection selector references outer in a where clause</span></span>

<span data-ttu-id="efb7f-132">當集合選擇器具有引用外部元素的 where 子句時,EF Core 將其轉換為資料庫聯接,並使用謂詞作為聯接條件。</span><span class="sxs-lookup"><span data-stu-id="efb7f-132">When the collection selector has a where clause, which references the outer element, then EF Core translates it to a database join and uses the predicate as the join condition.</span></span> <span data-ttu-id="efb7f-133">通常,當在外部元素上使用集合導航作為集合選擇器時,會出現這種情況。</span><span class="sxs-lookup"><span data-stu-id="efb7f-133">Normally this case arises when using collection navigation on the outer element as the collection selector.</span></span> <span data-ttu-id="efb7f-134">如果外部元素的集合為空,則不會為該外部元素生成任何結果。</span><span class="sxs-lookup"><span data-stu-id="efb7f-134">If the collection is empty for an outer element, then no results would be generated for that outer element.</span></span> <span data-ttu-id="efb7f-135">但是,`DefaultIfEmpty`如果應用於集合選擇器,則外部元素將與內部元素的預設值連接。</span><span class="sxs-lookup"><span data-stu-id="efb7f-135">But if `DefaultIfEmpty` is applied on the collection selector then the outer element will be connected with a default value of the inner element.</span></span> <span data-ttu-id="efb7f-136">由於這種區別,這種查詢`INNER JOIN`在`DefaultIfEmpty`不存在`LEFT JOIN`時`DefaultIfEmpty`和 何時 應用時被翻譯為。</span><span class="sxs-lookup"><span data-stu-id="efb7f-136">Because of this distinction, this kind of queries translates to `INNER JOIN` in the absence of `DefaultIfEmpty` and `LEFT JOIN` when `DefaultIfEmpty` is applied.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
INNER JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

### <a name="collection-selector-references-outer-in-a-non-where-case"></a><span data-ttu-id="efb7f-137">收集選擇器引言外部非 where 的情況下</span><span class="sxs-lookup"><span data-stu-id="efb7f-137">Collection selector references outer in a non-where case</span></span>

<span data-ttu-id="efb7f-138">當集合選擇器引用外部元素時,該元素不在 where 子句中(如上文所述),它不會轉換為資料庫聯接。</span><span class="sxs-lookup"><span data-stu-id="efb7f-138">When the collection selector references the outer element, which isn't in a where clause (as the case above), it doesn't translate to a database join.</span></span> <span data-ttu-id="efb7f-139">這就是為什麼我們需要評估每個外部元素的集合選擇器。</span><span class="sxs-lookup"><span data-stu-id="efb7f-139">That's why we need to evaluate the collection selector for each outer element.</span></span> <span data-ttu-id="efb7f-140">它轉換為`APPLY`許多關係資料庫中的操作。</span><span class="sxs-lookup"><span data-stu-id="efb7f-140">It translates to `APPLY` operations in many relational databases.</span></span> <span data-ttu-id="efb7f-141">如果外部元素的集合為空,則不會為該外部元素生成任何結果。</span><span class="sxs-lookup"><span data-stu-id="efb7f-141">If the collection is empty for an outer element, then no results would be generated for that outer element.</span></span> <span data-ttu-id="efb7f-142">但是,`DefaultIfEmpty`如果應用於集合選擇器,則外部元素將與內部元素的預設值連接。</span><span class="sxs-lookup"><span data-stu-id="efb7f-142">But if `DefaultIfEmpty` is applied on the collection selector then the outer element will be connected with a default value of the inner element.</span></span> <span data-ttu-id="efb7f-143">由於這種區別,這種查詢`CROSS APPLY`在`DefaultIfEmpty`不存在`OUTER APPLY`時`DefaultIfEmpty`和 何時 應用時被翻譯為。</span><span class="sxs-lookup"><span data-stu-id="efb7f-143">Because of this distinction, this kind of queries translates to `CROSS APPLY` in the absence of `DefaultIfEmpty` and `OUTER APPLY` when `DefaultIfEmpty` is applied.</span></span> <span data-ttu-id="efb7f-144">某些資料庫(如 SQLite)不`APPLY`支援 運算符,因此可能無法翻譯此類查詢。</span><span class="sxs-lookup"><span data-stu-id="efb7f-144">Certain databases like SQLite don't support `APPLY` operators so this kind of query may not be translated.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToApply)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
CROSS APPLY [Posts] AS [p]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
OUTER APPLY [Posts] AS [p]
```

## <a name="groupby"></a><span data-ttu-id="efb7f-145">GroupBy</span><span class="sxs-lookup"><span data-stu-id="efb7f-145">GroupBy</span></span>

<span data-ttu-id="efb7f-146">LINQ GroupBy 運算子建立`IGrouping<TKey, TElement>`類型`TKey`的結果`TElement`, 其中 和可以是任意類型。</span><span class="sxs-lookup"><span data-stu-id="efb7f-146">LINQ GroupBy operators create a result of type `IGrouping<TKey, TElement>` where `TKey` and `TElement` could be any arbitrary type.</span></span> <span data-ttu-id="efb7f-147">此外,`IGrouping``IEnumerable<TElement>`實現 ,這意味著您可以在分組后使用任何 LINQ 運算子在它上進行組合。</span><span class="sxs-lookup"><span data-stu-id="efb7f-147">Furthermore, `IGrouping` implements `IEnumerable<TElement>`, which means you can compose over it using any LINQ operator after the grouping.</span></span> <span data-ttu-id="efb7f-148">由於沒有資料庫結構可以表示`IGrouping`, GroupBy 運算符在大多數情況下沒有翻譯。</span><span class="sxs-lookup"><span data-stu-id="efb7f-148">Since no database structure can represent an `IGrouping`, GroupBy operators have no translation in most cases.</span></span> <span data-ttu-id="efb7f-149">當聚合運算符應用於返回標量的每個組時,可以在關係資料庫中將其轉換為 SQL。 `GROUP BY`</span><span class="sxs-lookup"><span data-stu-id="efb7f-149">When an aggregate operator is applied to each group, which returns a scalar, it can be translated to SQL `GROUP BY` in relational databases.</span></span> <span data-ttu-id="efb7f-150">SQL`GROUP BY`也受到限制。</span><span class="sxs-lookup"><span data-stu-id="efb7f-150">The SQL `GROUP BY` is restrictive too.</span></span> <span data-ttu-id="efb7f-151">它要求您僅按標量值進行分組。</span><span class="sxs-lookup"><span data-stu-id="efb7f-151">It requires you to group only by scalar values.</span></span> <span data-ttu-id="efb7f-152">投影只能包含對鍵列或應用於列的任何聚合。</span><span class="sxs-lookup"><span data-stu-id="efb7f-152">The projection can only contain grouping key columns or any aggregate applied over a column.</span></span> <span data-ttu-id="efb7f-153">EF Core 識別此模式並將其轉換為伺服器,如以下範例所示:</span><span class="sxs-lookup"><span data-stu-id="efb7f-153">EF Core identifies this pattern and translates it to the server, as in the following example:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupBy)]

```SQL
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
```

<span data-ttu-id="efb7f-154">EF Core 還轉換分組上的聚合運算符顯示在"位置或訂單By(或其他排序)LINQ"運算符中的查詢。</span><span class="sxs-lookup"><span data-stu-id="efb7f-154">EF Core also translates queries where an aggregate operator on the grouping appears in a Where or OrderBy (or other ordering) LINQ operator.</span></span> <span data-ttu-id="efb7f-155">它使用`HAVING`SQL 中子句作為 where 子句。</span><span class="sxs-lookup"><span data-stu-id="efb7f-155">It uses `HAVING` clause in SQL for the where clause.</span></span> <span data-ttu-id="efb7f-156">在應用 GroupBy 運算符之前,查詢的部分是任何複雜的查詢,只要可以轉換為伺服器。</span><span class="sxs-lookup"><span data-stu-id="efb7f-156">The part of the query before applying the GroupBy operator can be any complex query as long as it can be translated to server.</span></span> <span data-ttu-id="efb7f-157">此外,一旦在分組查詢上應用聚合運算符以從生成的源中刪除分組,就可以像任何其他查詢一樣在其上面進行組合。</span><span class="sxs-lookup"><span data-stu-id="efb7f-157">Furthermore, once you apply aggregate operators on a grouping query to remove groupings from the resulting source, you can compose on top of it like any other query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupByFilter)]

```SQL
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
HAVING COUNT(*) > 0
ORDER BY [p].[AuthorId]
```

<span data-ttu-id="efb7f-158">聚合運算符 EF Core 支援所選取</span><span class="sxs-lookup"><span data-stu-id="efb7f-158">The aggregate operators EF Core supports are as follows</span></span>

- <span data-ttu-id="efb7f-159">Average</span><span class="sxs-lookup"><span data-stu-id="efb7f-159">Average</span></span>
- <span data-ttu-id="efb7f-160">Count</span><span class="sxs-lookup"><span data-stu-id="efb7f-160">Count</span></span>
- <span data-ttu-id="efb7f-161">LongCount</span><span class="sxs-lookup"><span data-stu-id="efb7f-161">LongCount</span></span>
- <span data-ttu-id="efb7f-162">最大值</span><span class="sxs-lookup"><span data-stu-id="efb7f-162">Max</span></span>
- <span data-ttu-id="efb7f-163">最小值</span><span class="sxs-lookup"><span data-stu-id="efb7f-163">Min</span></span>
- <span data-ttu-id="efb7f-164">Sum</span><span class="sxs-lookup"><span data-stu-id="efb7f-164">Sum</span></span>

## <a name="left-join"></a><span data-ttu-id="efb7f-165">左側聯結</span><span class="sxs-lookup"><span data-stu-id="efb7f-165">Left Join</span></span>

<span data-ttu-id="efb7f-166">雖然左聯接不是 LINQ 運算符,但關係資料庫具有「左聯接」的概念,該概念在查詢中經常使用。</span><span class="sxs-lookup"><span data-stu-id="efb7f-166">While Left Join isn't a LINQ operator, relational databases have the concept of a Left Join which is frequently used in queries.</span></span> <span data-ttu-id="efb7f-167">LINQ 查詢中的特定模式給出的結果與伺服器上的`LEFT JOIN`a 相同。</span><span class="sxs-lookup"><span data-stu-id="efb7f-167">A particular pattern in LINQ queries gives the same result as a `LEFT JOIN` on the server.</span></span> <span data-ttu-id="efb7f-168">EF Core 識別此類模式,`LEFT JOIN`並在伺服器 端生成等效模式。</span><span class="sxs-lookup"><span data-stu-id="efb7f-168">EF Core identifies such patterns and generates the equivalent `LEFT JOIN` on the server side.</span></span> <span data-ttu-id="efb7f-169">該模式涉及在資料源之間創建 GroupJoin,然後使用分組源上的 DefaultIfEmpty 的 SelectMany 運算符將分組拼合,以在內部沒有相關元素時匹配 null。</span><span class="sxs-lookup"><span data-stu-id="efb7f-169">The pattern involves creating a GroupJoin between both the data sources and then flattening out the grouping by using the SelectMany operator with DefaultIfEmpty on the grouping source to match null when the inner doesn't have a related element.</span></span> <span data-ttu-id="efb7f-170">下面的示例顯示了該模式的外觀及其生成。</span><span class="sxs-lookup"><span data-stu-id="efb7f-170">The following example shows what that pattern looks like and what it generates.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#LeftJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

<span data-ttu-id="efb7f-171">上述模式在表達式樹中創建一個複雜的結構。</span><span class="sxs-lookup"><span data-stu-id="efb7f-171">The above pattern creates a complex structure in the expression tree.</span></span> <span data-ttu-id="efb7f-172">因此,EF Core 要求您在緊接運算符的步驟中拼出 GroupJoin 運算符的分組結果。</span><span class="sxs-lookup"><span data-stu-id="efb7f-172">Because of that, EF Core requires you to flatten out the grouping results of the GroupJoin operator in a step immediately following the operator.</span></span> <span data-ttu-id="efb7f-173">即使使用組 Join-預設 IfEmpty-SelectMany,但採用不同的模式,我們可能不會將其標識為「左聯接」。</span><span class="sxs-lookup"><span data-stu-id="efb7f-173">Even if the GroupJoin-DefaultIfEmpty-SelectMany is used but in a different pattern, we may not identify it as a Left Join.</span></span>
