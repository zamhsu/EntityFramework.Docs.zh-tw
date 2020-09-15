---
title: 複雜查詢運算子-EF Core
description: 使用 Entity Framework Core 時，更複雜的 LINQ 查詢運算子的深入資訊
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/complex-query-operators
ms.openlocfilehash: 57157fa1593c9e5fe54e5fbe6b2c58eca3d4b0e7
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071153"
---
# <a name="complex-query-operators"></a><span data-ttu-id="f70f9-103">複雜查詢運算子</span><span class="sxs-lookup"><span data-stu-id="f70f9-103">Complex Query Operators</span></span>

<span data-ttu-id="f70f9-104"> (LINQ) 的語言整合式查詢包含許多複雜的運算子，這些運算子結合多個資料來源或複雜的處理。</span><span class="sxs-lookup"><span data-stu-id="f70f9-104">Language Integrated Query (LINQ) contains many complex operators, which combine multiple data sources or does complex processing.</span></span> <span data-ttu-id="f70f9-105">並非所有 LINQ 運算子在伺服器端都有適當的翻譯。</span><span class="sxs-lookup"><span data-stu-id="f70f9-105">Not all LINQ operators have suitable translations on the server side.</span></span> <span data-ttu-id="f70f9-106">有時候，一個表單中的查詢會轉譯成伺服器，但如果以不同的表單撰寫，即使結果相同也不會轉譯。</span><span class="sxs-lookup"><span data-stu-id="f70f9-106">Sometimes, a query in one form translates to the server but if written in a different form doesn't translate even if the result is the same.</span></span> <span data-ttu-id="f70f9-107">此頁面描述一些複雜運算子及其支援的變化。</span><span class="sxs-lookup"><span data-stu-id="f70f9-107">This page describes some of the complex operators and their supported variations.</span></span> <span data-ttu-id="f70f9-108">在未來的版本中，我們可能會辨識更多模式，並新增其對應的翻譯。</span><span class="sxs-lookup"><span data-stu-id="f70f9-108">In future releases, we may recognize more patterns and add their corresponding translations.</span></span> <span data-ttu-id="f70f9-109">此外，請務必記住，提供者之間的翻譯支援會有所不同。</span><span class="sxs-lookup"><span data-stu-id="f70f9-109">It's also important to keep in mind that translation support varies between providers.</span></span> <span data-ttu-id="f70f9-110">在 SqlServer 中轉譯的特定查詢，在 SQLite 資料庫中可能無法運作。</span><span class="sxs-lookup"><span data-stu-id="f70f9-110">A particular query, which is translated in SqlServer, may not work for SQLite databases.</span></span>

> [!TIP]
> <span data-ttu-id="f70f9-111">您可以在 GitHub 上檢視此文章的[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="f70f9-111">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="join"></a><span data-ttu-id="f70f9-112">Join</span><span class="sxs-lookup"><span data-stu-id="f70f9-112">Join</span></span>

<span data-ttu-id="f70f9-113">LINQ Join 運算子可讓您根據每個來源的索引鍵選取器來連接兩個數據源，並在索引鍵符合時產生值的元組。</span><span class="sxs-lookup"><span data-stu-id="f70f9-113">The LINQ Join operator allows you to connect two data sources based on the key selector for each source, generating a tuple of values when the key matches.</span></span> <span data-ttu-id="f70f9-114">它會自然地轉譯為 `INNER JOIN` 關係資料庫。</span><span class="sxs-lookup"><span data-stu-id="f70f9-114">It naturally translates to `INNER JOIN` on relational databases.</span></span> <span data-ttu-id="f70f9-115">當 LINQ 聯結具有外部和內部索引鍵選取器時，資料庫需要單一聯結條件。</span><span class="sxs-lookup"><span data-stu-id="f70f9-115">While the LINQ Join has outer and inner key selectors, the database requires a single join condition.</span></span> <span data-ttu-id="f70f9-116">因此 EF Core 藉由比較外部索引鍵選取器與內部索引鍵選取器的相等來產生聯結條件。</span><span class="sxs-lookup"><span data-stu-id="f70f9-116">So EF Core generates a join condition by comparing the outer key selector to the inner key selector for equality.</span></span> <span data-ttu-id="f70f9-117">此外，如果索引鍵選取器是匿名型別，EF Core 會產生聯結條件來比較相等元件的狀況。</span><span class="sxs-lookup"><span data-stu-id="f70f9-117">Further, if the key selectors are anonymous types, EF Core generates a join condition to compare equality component wise.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#Join)]

```SQL
SELECT [p].[PersonId], [p].[Name], [p].[PhotoId], [p0].[PersonPhotoId], [p0].[Caption], [p0].[Photo]
FROM [PersonPhoto] AS [p0]
INNER JOIN [Person] AS [p] ON [p0].[PersonPhotoId] = [p].[PhotoId]
```

## <a name="groupjoin"></a><span data-ttu-id="f70f9-118">GroupJoin</span><span class="sxs-lookup"><span data-stu-id="f70f9-118">GroupJoin</span></span>

<span data-ttu-id="f70f9-119">LINQ GroupJoin 運算子可讓您連接類似于聯結的兩個數據源，但是它會建立一組內部值以符合專用項目。</span><span class="sxs-lookup"><span data-stu-id="f70f9-119">The LINQ GroupJoin operator allows you to connect two data sources similar to Join, but it creates a group of inner values for matching outer elements.</span></span> <span data-ttu-id="f70f9-120">執行類似下列範例的查詢會產生的結果 `Blog`  &  `IEnumerable<Post>` 。</span><span class="sxs-lookup"><span data-stu-id="f70f9-120">Executing a query like the following example generates a result of `Blog` & `IEnumerable<Post>`.</span></span> <span data-ttu-id="f70f9-121">由於資料庫 (特別是關係資料庫) 無法表示用戶端物件的集合，因此在許多情況下，GroupJoin 不會轉譯成伺服器。</span><span class="sxs-lookup"><span data-stu-id="f70f9-121">Since databases (especially relational databases) don't have a way to represent a collection of client-side objects, GroupJoin doesn't translate to the server in many cases.</span></span> <span data-ttu-id="f70f9-122">您必須從伺服器取得所有資料，才能進行 GroupJoin，而不需要特殊的選取器 (第一個查詢) 。</span><span class="sxs-lookup"><span data-stu-id="f70f9-122">It requires you to get all of the data from the server to do GroupJoin without a special selector (first query below).</span></span> <span data-ttu-id="f70f9-123">但是，如果選取器會限制選取的資料，則從伺服器提取所有資料可能會導致效能問題 (第二個查詢) 。</span><span class="sxs-lookup"><span data-stu-id="f70f9-123">But if the selector is limiting data being selected then fetching all of the data from the server may cause performance issues (second query below).</span></span> <span data-ttu-id="f70f9-124">這就是 EF Core 不會轉譯 GroupJoin 的原因。</span><span class="sxs-lookup"><span data-stu-id="f70f9-124">That's why EF Core doesn't translate GroupJoin.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupJoin)]

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupJoinComposed)]

## <a name="selectmany"></a><span data-ttu-id="f70f9-125">SelectMany</span><span class="sxs-lookup"><span data-stu-id="f70f9-125">SelectMany</span></span>

<span data-ttu-id="f70f9-126">LINQ SelectMany 運算子可讓您列舉每個專用項目的集合選取器，並從每個資料來源產生值的元組。</span><span class="sxs-lookup"><span data-stu-id="f70f9-126">The LINQ SelectMany operator allows you to enumerate over a collection selector for each outer element and generate tuples of values from each data source.</span></span> <span data-ttu-id="f70f9-127">一種方式是聯結，但沒有任何條件，因此每個外部專案都會與集合來源的元素連接。</span><span class="sxs-lookup"><span data-stu-id="f70f9-127">In a way, it's a join but without any condition so every outer element is connected with an element from the collection source.</span></span> <span data-ttu-id="f70f9-128">根據集合選取器與外部資料源的關聯方式，SelectMany 可以轉譯成伺服器端上的各種不同查詢。</span><span class="sxs-lookup"><span data-stu-id="f70f9-128">Depending on how the collection selector is related to the outer data source, SelectMany can translate into various different queries on the server side.</span></span>

### <a name="collection-selector-doesnt-reference-outer"></a><span data-ttu-id="f70f9-129">集合選取器不參考外部</span><span class="sxs-lookup"><span data-stu-id="f70f9-129">Collection selector doesn't reference outer</span></span>

<span data-ttu-id="f70f9-130">當集合選取器未參考外部來源的任何資料時，結果會是兩個數據源的笛卡兒乘積。</span><span class="sxs-lookup"><span data-stu-id="f70f9-130">When the collection selector isn't referencing anything from the outer source, the result is a cartesian product of both data sources.</span></span> <span data-ttu-id="f70f9-131">它會轉譯為 `CROSS JOIN` 關係資料庫中的。</span><span class="sxs-lookup"><span data-stu-id="f70f9-131">It translates to `CROSS JOIN` in relational databases.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToCrossJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
CROSS JOIN [Posts] AS [p]
```

### <a name="collection-selector-references-outer-in-a-where-clause"></a><span data-ttu-id="f70f9-132">集合選取器參考 where 子句中的 outer</span><span class="sxs-lookup"><span data-stu-id="f70f9-132">Collection selector references outer in a where clause</span></span>

<span data-ttu-id="f70f9-133">當集合選取器具有 where 子句（參考外部專案）時，EF Core 會將它轉譯成資料庫聯結，並使用述詞做為聯結條件。</span><span class="sxs-lookup"><span data-stu-id="f70f9-133">When the collection selector has a where clause, which references the outer element, then EF Core translates it to a database join and uses the predicate as the join condition.</span></span> <span data-ttu-id="f70f9-134">一般來說，在專用項目上使用集合導覽作為集合選取器時，會發生這種情況。</span><span class="sxs-lookup"><span data-stu-id="f70f9-134">Normally this case arises when using collection navigation on the outer element as the collection selector.</span></span> <span data-ttu-id="f70f9-135">如果外部專案的集合是空的，則不會針對該專用項目產生任何結果。</span><span class="sxs-lookup"><span data-stu-id="f70f9-135">If the collection is empty for an outer element, then no results would be generated for that outer element.</span></span> <span data-ttu-id="f70f9-136">但是，如果在 `DefaultIfEmpty` 集合選取器上套用，則外部專案將會與內部元素的預設值連接。</span><span class="sxs-lookup"><span data-stu-id="f70f9-136">But if `DefaultIfEmpty` is applied on the collection selector then the outer element will be connected with a default value of the inner element.</span></span> <span data-ttu-id="f70f9-137">由於這種差異，這類的查詢在沒有 `INNER JOIN` 和套用時， `DefaultIfEmpty` 會轉譯為 `LEFT JOIN` `DefaultIfEmpty` 。</span><span class="sxs-lookup"><span data-stu-id="f70f9-137">Because of this distinction, this kind of queries translates to `INNER JOIN` in the absence of `DefaultIfEmpty` and `LEFT JOIN` when `DefaultIfEmpty` is applied.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
INNER JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

### <a name="collection-selector-references-outer-in-a-non-where-case"></a><span data-ttu-id="f70f9-138">集合選取器參考非 where 案例中的 outer</span><span class="sxs-lookup"><span data-stu-id="f70f9-138">Collection selector references outer in a non-where case</span></span>

<span data-ttu-id="f70f9-139">當集合選取器參考不在 where 子句中的外部專案時 (上述) 的情況下，它不會轉譯成資料庫聯結。</span><span class="sxs-lookup"><span data-stu-id="f70f9-139">When the collection selector references the outer element, which isn't in a where clause (as the case above), it doesn't translate to a database join.</span></span> <span data-ttu-id="f70f9-140">這就是為什麼我們需要評估每個專用項目的集合選取器。</span><span class="sxs-lookup"><span data-stu-id="f70f9-140">That's why we need to evaluate the collection selector for each outer element.</span></span> <span data-ttu-id="f70f9-141">它會轉譯為 `APPLY` 許多關係資料庫中的作業。</span><span class="sxs-lookup"><span data-stu-id="f70f9-141">It translates to `APPLY` operations in many relational databases.</span></span> <span data-ttu-id="f70f9-142">如果外部專案的集合是空的，則不會針對該專用項目產生任何結果。</span><span class="sxs-lookup"><span data-stu-id="f70f9-142">If the collection is empty for an outer element, then no results would be generated for that outer element.</span></span> <span data-ttu-id="f70f9-143">但是，如果在 `DefaultIfEmpty` 集合選取器上套用，則外部專案將會與內部元素的預設值連接。</span><span class="sxs-lookup"><span data-stu-id="f70f9-143">But if `DefaultIfEmpty` is applied on the collection selector then the outer element will be connected with a default value of the inner element.</span></span> <span data-ttu-id="f70f9-144">由於這種差異，這類的查詢在沒有 `CROSS APPLY` 和套用時， `DefaultIfEmpty` 會轉譯為 `OUTER APPLY` `DefaultIfEmpty` 。</span><span class="sxs-lookup"><span data-stu-id="f70f9-144">Because of this distinction, this kind of queries translates to `CROSS APPLY` in the absence of `DefaultIfEmpty` and `OUTER APPLY` when `DefaultIfEmpty` is applied.</span></span> <span data-ttu-id="f70f9-145">某些資料庫（例如 SQLite）不支援 `APPLY` 運算子，因此這類查詢可能無法轉譯。</span><span class="sxs-lookup"><span data-stu-id="f70f9-145">Certain databases like SQLite don't support `APPLY` operators so this kind of query may not be translated.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToApply)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
CROSS APPLY [Posts] AS [p]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
OUTER APPLY [Posts] AS [p]
```

## <a name="groupby"></a><span data-ttu-id="f70f9-146">GroupBy</span><span class="sxs-lookup"><span data-stu-id="f70f9-146">GroupBy</span></span>

<span data-ttu-id="f70f9-147">LINQ GroupBy 運算子會建立類型的結果 `IGrouping<TKey, TElement>` `TKey` ，而且可以 `TElement` 是任何任意型別。</span><span class="sxs-lookup"><span data-stu-id="f70f9-147">LINQ GroupBy operators create a result of type `IGrouping<TKey, TElement>` where `TKey` and `TElement` could be any arbitrary type.</span></span> <span data-ttu-id="f70f9-148">此外， `IGrouping` `IEnumerable<TElement>` 也會執行，這表示您可以使用群組之後的任何 LINQ 運算子來撰寫它。</span><span class="sxs-lookup"><span data-stu-id="f70f9-148">Furthermore, `IGrouping` implements `IEnumerable<TElement>`, which means you can compose over it using any LINQ operator after the grouping.</span></span> <span data-ttu-id="f70f9-149">因為沒有任何資料庫結構可以表示 `IGrouping` ，所以在大部分情況下，GroupBy 運算子沒有任何轉譯。</span><span class="sxs-lookup"><span data-stu-id="f70f9-149">Since no database structure can represent an `IGrouping`, GroupBy operators have no translation in most cases.</span></span> <span data-ttu-id="f70f9-150">當匯總運算子套用至傳回純量的每個群組時，可以將它轉譯為 `GROUP BY` 關係資料庫中的 SQL。</span><span class="sxs-lookup"><span data-stu-id="f70f9-150">When an aggregate operator is applied to each group, which returns a scalar, it can be translated to SQL `GROUP BY` in relational databases.</span></span> <span data-ttu-id="f70f9-151">SQL 也 `GROUP BY` 有限制。</span><span class="sxs-lookup"><span data-stu-id="f70f9-151">The SQL `GROUP BY` is restrictive too.</span></span> <span data-ttu-id="f70f9-152">您只需以純量值群組。</span><span class="sxs-lookup"><span data-stu-id="f70f9-152">It requires you to group only by scalar values.</span></span> <span data-ttu-id="f70f9-153">投影只能包含群組索引鍵資料行或套用在資料行上的任何匯總。</span><span class="sxs-lookup"><span data-stu-id="f70f9-153">The projection can only contain grouping key columns or any aggregate applied over a column.</span></span> <span data-ttu-id="f70f9-154">EF Core 識別此模式，並將它轉譯成伺服器，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="f70f9-154">EF Core identifies this pattern and translates it to the server, as in the following example:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupBy)]

```SQL
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
```

<span data-ttu-id="f70f9-155">EF Core 也會轉譯查詢，其中群組上的匯總運算子會出現在 Where 或 OrderBy (或其他排序) LINQ 運算子中。</span><span class="sxs-lookup"><span data-stu-id="f70f9-155">EF Core also translates queries where an aggregate operator on the grouping appears in a Where or OrderBy (or other ordering) LINQ operator.</span></span> <span data-ttu-id="f70f9-156">它會 `HAVING` 在 SQL 中針對 where 子句使用子句。</span><span class="sxs-lookup"><span data-stu-id="f70f9-156">It uses `HAVING` clause in SQL for the where clause.</span></span> <span data-ttu-id="f70f9-157">套用 GroupBy 運算子之前的查詢部分可以是任何複雜的查詢，只要它可以轉譯成伺服器即可。</span><span class="sxs-lookup"><span data-stu-id="f70f9-157">The part of the query before applying the GroupBy operator can be any complex query as long as it can be translated to server.</span></span> <span data-ttu-id="f70f9-158">此外，當您套用群組查詢的匯總運算子來移除所產生來源的群組時，您可以像任何其他查詢一樣，在其上層撰寫。</span><span class="sxs-lookup"><span data-stu-id="f70f9-158">Furthermore, once you apply aggregate operators on a grouping query to remove groupings from the resulting source, you can compose on top of it like any other query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupByFilter)]

```SQL
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
HAVING COUNT(*) > 0
ORDER BY [p].[AuthorId]
```

<span data-ttu-id="f70f9-159">EF Core 支援的匯總運算子如下所示</span><span class="sxs-lookup"><span data-stu-id="f70f9-159">The aggregate operators EF Core supports are as follows</span></span>

- <span data-ttu-id="f70f9-160">Average</span><span class="sxs-lookup"><span data-stu-id="f70f9-160">Average</span></span>
- <span data-ttu-id="f70f9-161">計數</span><span class="sxs-lookup"><span data-stu-id="f70f9-161">Count</span></span>
- <span data-ttu-id="f70f9-162">LongCount</span><span class="sxs-lookup"><span data-stu-id="f70f9-162">LongCount</span></span>
- <span data-ttu-id="f70f9-163">最大值</span><span class="sxs-lookup"><span data-stu-id="f70f9-163">Max</span></span>
- <span data-ttu-id="f70f9-164">最小值</span><span class="sxs-lookup"><span data-stu-id="f70f9-164">Min</span></span>
- <span data-ttu-id="f70f9-165">Sum</span><span class="sxs-lookup"><span data-stu-id="f70f9-165">Sum</span></span>

## <a name="left-join"></a><span data-ttu-id="f70f9-166">左方聯結</span><span class="sxs-lookup"><span data-stu-id="f70f9-166">Left Join</span></span>

<span data-ttu-id="f70f9-167">雖然左方聯結不是 LINQ 運算子，但關係資料庫具有左方聯結的概念，其通常會在查詢中使用。</span><span class="sxs-lookup"><span data-stu-id="f70f9-167">While Left Join isn't a LINQ operator, relational databases have the concept of a Left Join which is frequently used in queries.</span></span> <span data-ttu-id="f70f9-168">LINQ 查詢中的特定模式會提供與伺服器上相同的結果 `LEFT JOIN` 。</span><span class="sxs-lookup"><span data-stu-id="f70f9-168">A particular pattern in LINQ queries gives the same result as a `LEFT JOIN` on the server.</span></span> <span data-ttu-id="f70f9-169">EF Core 識別這類模式，並在伺服器端產生對等的 `LEFT JOIN` 。</span><span class="sxs-lookup"><span data-stu-id="f70f9-169">EF Core identifies such patterns and generates the equivalent `LEFT JOIN` on the server side.</span></span> <span data-ttu-id="f70f9-170">此模式包含在兩個數據源之間建立 GroupJoin，然後在內部沒有相關專案時，使用 SelectMany 運算子搭配群組來源上的 DefaultIfEmpty 來比對 null，以將群組壓平合併。</span><span class="sxs-lookup"><span data-stu-id="f70f9-170">The pattern involves creating a GroupJoin between both the data sources and then flattening out the grouping by using the SelectMany operator with DefaultIfEmpty on the grouping source to match null when the inner doesn't have a related element.</span></span> <span data-ttu-id="f70f9-171">下列範例會顯示該模式的外觀和產生的內容。</span><span class="sxs-lookup"><span data-stu-id="f70f9-171">The following example shows what that pattern looks like and what it generates.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#LeftJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

<span data-ttu-id="f70f9-172">上述模式會在運算式樹狀結構中建立複雜結構。</span><span class="sxs-lookup"><span data-stu-id="f70f9-172">The above pattern creates a complex structure in the expression tree.</span></span> <span data-ttu-id="f70f9-173">因此，EF Core 要求您在緊接著運算子之後的步驟中，將 GroupJoin 運算子的群組結果壓平合併。</span><span class="sxs-lookup"><span data-stu-id="f70f9-173">Because of that, EF Core requires you to flatten out the grouping results of the GroupJoin operator in a step immediately following the operator.</span></span> <span data-ttu-id="f70f9-174">即使使用了 GroupJoin-DefaultIfEmpty-SelectMany，但在不同的模式下，也無法將其識別為左方聯結。</span><span class="sxs-lookup"><span data-stu-id="f70f9-174">Even if the GroupJoin-DefaultIfEmpty-SelectMany is used but in a different pattern, we may not identify it as a Left Join.</span></span>
