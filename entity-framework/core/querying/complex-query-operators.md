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
# <a name="complex-query-operators"></a><span data-ttu-id="537ec-102">複雜查詢運算子</span><span class="sxs-lookup"><span data-stu-id="537ec-102">Complex Query Operators</span></span>

<span data-ttu-id="537ec-103">語言整合式查詢（LINQ）包含許多複雜運算子，可結合多個資料來源或執行複雜的處理。</span><span class="sxs-lookup"><span data-stu-id="537ec-103">Language Integrated Query (LINQ) contains many complex operators, which combine multiple data sources or does complex processing.</span></span> <span data-ttu-id="537ec-104">並非所有的 LINQ 運算子在伺服器端都有適當的翻譯。</span><span class="sxs-lookup"><span data-stu-id="537ec-104">Not all LINQ operators have suitable translations on the server side.</span></span> <span data-ttu-id="537ec-105">有時候，一個表單中的查詢會轉譯成伺服器，但如果是以不同的格式撰寫，即使結果相同也是一樣。</span><span class="sxs-lookup"><span data-stu-id="537ec-105">Sometimes, a query in one form translates to the server but if written in a different form doesn't translate even if the result is the same.</span></span> <span data-ttu-id="537ec-106">此頁面描述一些複雜運算子和其支援的變化。</span><span class="sxs-lookup"><span data-stu-id="537ec-106">This page describes some of the complex operators and their supported variations.</span></span> <span data-ttu-id="537ec-107">在未來的版本中，我們可以辨識更多模式並新增其對應的翻譯。</span><span class="sxs-lookup"><span data-stu-id="537ec-107">In future releases, we may recognize more patterns and add their corresponding translations.</span></span> <span data-ttu-id="537ec-108">也請務必記住，翻譯支援會因提供者而異。</span><span class="sxs-lookup"><span data-stu-id="537ec-108">It's also important to keep in mind that translation support varies between providers.</span></span> <span data-ttu-id="537ec-109">在 SqlServer 中轉譯的特定查詢，對 SQLite 資料庫可能無法使用。</span><span class="sxs-lookup"><span data-stu-id="537ec-109">A particular query, which is translated in SqlServer, may not work for SQLite databases.</span></span>

> [!TIP]
> <span data-ttu-id="537ec-110">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="537ec-110">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="join"></a><span data-ttu-id="537ec-111">Join</span><span class="sxs-lookup"><span data-stu-id="537ec-111">Join</span></span>

<span data-ttu-id="537ec-112">LINQ Join 運算子可讓您根據每個來源的索引鍵選取器來連接兩個數據源，並在索引鍵符合時產生值的元組。</span><span class="sxs-lookup"><span data-stu-id="537ec-112">The LINQ Join operator allows you to connect two data sources based on the key selector for each source, generating a tuple of values when the key matches.</span></span> <span data-ttu-id="537ec-113">它自然地轉譯為關係資料庫上的 `INNER JOIN`。</span><span class="sxs-lookup"><span data-stu-id="537ec-113">It naturally translates to `INNER JOIN` on relational databases.</span></span> <span data-ttu-id="537ec-114">雖然 LINQ 聯結具有外部和內部索引鍵選取器，但資料庫需要單一聯結條件。</span><span class="sxs-lookup"><span data-stu-id="537ec-114">While the LINQ Join has outer and inner key selectors, the database requires a single join condition.</span></span> <span data-ttu-id="537ec-115">所以 EF Core 藉由比較外部索引鍵選取器與內部索引鍵選取器是否相等來產生聯結條件。</span><span class="sxs-lookup"><span data-stu-id="537ec-115">So EF Core generates a join condition by comparing the outer key selector to the inner key selector for equality.</span></span> <span data-ttu-id="537ec-116">此外，如果索引鍵選取器是匿名型別，EF Core 會產生聯結條件以比較相等性元件。</span><span class="sxs-lookup"><span data-stu-id="537ec-116">Further, if the key selectors are anonymous types, EF Core generates a join condition to compare equality component wise.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#Join)]

```SQL
SELECT [p].[PersonId], [p].[Name], [p].[PhotoId], [p0].[PersonPhotoId], [p0].[Caption], [p0].[Photo]
FROM [PersonPhoto] AS [p0]
INNER JOIN [Person] AS [p] ON [p0].[PersonPhotoId] = [p].[PhotoId]
```

## <a name="groupjoin"></a><span data-ttu-id="537ec-117">GroupJoin</span><span class="sxs-lookup"><span data-stu-id="537ec-117">GroupJoin</span></span>

<span data-ttu-id="537ec-118">LINQ GroupJoin 運算子可讓您連接類似于聯結的兩個數據源，但它會建立一組內部值以符合專用項目。</span><span class="sxs-lookup"><span data-stu-id="537ec-118">The LINQ GroupJoin operator allows you to connect two data sources similar to Join, but it creates a group of inner values for matching outer elements.</span></span> <span data-ttu-id="537ec-119">執行如下列範例所示的查詢，會產生 `Blog` & `IEnumerable<Post>`的結果。</span><span class="sxs-lookup"><span data-stu-id="537ec-119">Executing a query like the following example generates a result of `Blog` & `IEnumerable<Post>`.</span></span> <span data-ttu-id="537ec-120">由於資料庫（尤其是關係資料庫）無法代表用戶端物件的集合，因此在許多情況下，GroupJoin 不會轉譯為伺服器。</span><span class="sxs-lookup"><span data-stu-id="537ec-120">Since databases (especially relational databases) don't have a way to represent a collection of client-side objects, GroupJoin doesn't translate to the server in many cases.</span></span> <span data-ttu-id="537ec-121">您必須從伺服器取得所有資料，才能執行 GroupJoin，而不需要特殊的選取器（下一個查詢）。</span><span class="sxs-lookup"><span data-stu-id="537ec-121">It requires you to get all of the data from the server to do GroupJoin without a special selector (first query below).</span></span> <span data-ttu-id="537ec-122">但是，如果選取器限制選取的資料，則從伺服器提取所有資料可能會導致效能問題（下面的第二個查詢）。</span><span class="sxs-lookup"><span data-stu-id="537ec-122">But if the selector is limiting data being selected then fetching all of the data from the server may cause performance issues (second query below).</span></span> <span data-ttu-id="537ec-123">這就是為什麼 EF Core 不會轉譯 GroupJoin 的原因。</span><span class="sxs-lookup"><span data-stu-id="537ec-123">That's why EF Core doesn't translate GroupJoin.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupJoin)]

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupJoinComposed)]

## <a name="selectmany"></a><span data-ttu-id="537ec-124">SelectMany</span><span class="sxs-lookup"><span data-stu-id="537ec-124">SelectMany</span></span>

<span data-ttu-id="537ec-125">LINQ SelectMany 運算子可讓您列舉每個專用項目的集合選取器，並從每個資料來源產生值的元組。</span><span class="sxs-lookup"><span data-stu-id="537ec-125">The LINQ SelectMany operator allows you to enumerate over a collection selector for each outer element and generate tuples of values from each data source.</span></span> <span data-ttu-id="537ec-126">在某種情況下，它是聯結，但不含任何條件，因此每個專用項目都與集合來源中的元素連接。</span><span class="sxs-lookup"><span data-stu-id="537ec-126">In a way, it's a join but without any condition so every outer element is connected with an element from the collection source.</span></span> <span data-ttu-id="537ec-127">根據集合選取器與外部資料源相關的方式，SelectMany 可以轉譯成伺服器端上的各種不同查詢。</span><span class="sxs-lookup"><span data-stu-id="537ec-127">Depending on how the collection selector is related to the outer data source, SelectMany can translate into various different queries on the server side.</span></span>

### <a name="collection-selector-doesnt-reference-outer"></a><span data-ttu-id="537ec-128">集合選取器未參考外部</span><span class="sxs-lookup"><span data-stu-id="537ec-128">Collection selector doesn't reference outer</span></span>

<span data-ttu-id="537ec-129">當集合選取器未參考來自外部來源的任何專案時，結果會是這兩個數據源的笛卡兒乘積。</span><span class="sxs-lookup"><span data-stu-id="537ec-129">When the collection selector isn't referencing anything from the outer source, the result is a cartesian product of both data sources.</span></span> <span data-ttu-id="537ec-130">它會轉譯成關係資料庫中的 `CROSS JOIN`。</span><span class="sxs-lookup"><span data-stu-id="537ec-130">It translates to `CROSS JOIN` in relational databases.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToCrossJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
CROSS JOIN [Posts] AS [p]
```

### <a name="collection-selector-references-outer-in-a-where-clause"></a><span data-ttu-id="537ec-131">Where 子句中的集合選取器參考外部</span><span class="sxs-lookup"><span data-stu-id="537ec-131">Collection selector references outer in a where clause</span></span>

<span data-ttu-id="537ec-132">當集合選取器具有 where 子句（其參考外部專案）時，EF Core 會將它轉譯為資料庫聯結，並使用述詞做為聯結條件。</span><span class="sxs-lookup"><span data-stu-id="537ec-132">When the collection selector has a where clause, which references the outer element, then EF Core translates it to a database join and uses the predicate as the join condition.</span></span> <span data-ttu-id="537ec-133">一般來說，在外部專案上使用集合導覽做為集合選取器時，就會發生這種情況。</span><span class="sxs-lookup"><span data-stu-id="537ec-133">Normally this case arises when using collection navigation on the outer element as the collection selector.</span></span> <span data-ttu-id="537ec-134">如果專用項目的集合是空的，則不會針對該專用項目產生任何結果。</span><span class="sxs-lookup"><span data-stu-id="537ec-134">If the collection is empty for an outer element, then no results would be generated for that outer element.</span></span> <span data-ttu-id="537ec-135">但是，如果在集合選取器上套用 `DefaultIfEmpty`，則專用項目會與內部元素的預設值連接。</span><span class="sxs-lookup"><span data-stu-id="537ec-135">But if `DefaultIfEmpty` is applied on the collection selector then the outer element will be connected with a default value of the inner element.</span></span> <span data-ttu-id="537ec-136">基於此區分，這類查詢會在沒有 `DefaultIfEmpty` 時轉譯為 `INNER JOIN`，並在套用 `DefaultIfEmpty` 時轉譯為 `LEFT JOIN`。</span><span class="sxs-lookup"><span data-stu-id="537ec-136">Because of this distinction, this kind of queries translates to `INNER JOIN` in the absence of `DefaultIfEmpty` and `LEFT JOIN` when `DefaultIfEmpty` is applied.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
INNER JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

### <a name="collection-selector-references-outer-in-a-non-where-case"></a><span data-ttu-id="537ec-137">集合選取器參考非 where 案例中的外部</span><span class="sxs-lookup"><span data-stu-id="537ec-137">Collection selector references outer in a non-where case</span></span>

<span data-ttu-id="537ec-138">當集合選取器參考不在 where 子句中的專用項目時（如上述案例所示），它不會轉譯成資料庫聯結。</span><span class="sxs-lookup"><span data-stu-id="537ec-138">When the collection selector references the outer element, which isn't in a where clause (as the case above), it doesn't translate to a database join.</span></span> <span data-ttu-id="537ec-139">這就是為什麼我們需要針對每個專用項目評估集合選取器。</span><span class="sxs-lookup"><span data-stu-id="537ec-139">That's why we need to evaluate the collection selector for each outer element.</span></span> <span data-ttu-id="537ec-140">它會轉譯成許多關係資料庫中的 `APPLY` 作業。</span><span class="sxs-lookup"><span data-stu-id="537ec-140">It translates to `APPLY` operations in many relational databases.</span></span> <span data-ttu-id="537ec-141">如果專用項目的集合是空的，則不會針對該專用項目產生任何結果。</span><span class="sxs-lookup"><span data-stu-id="537ec-141">If the collection is empty for an outer element, then no results would be generated for that outer element.</span></span> <span data-ttu-id="537ec-142">但是，如果在集合選取器上套用 `DefaultIfEmpty`，則專用項目會與內部元素的預設值連接。</span><span class="sxs-lookup"><span data-stu-id="537ec-142">But if `DefaultIfEmpty` is applied on the collection selector then the outer element will be connected with a default value of the inner element.</span></span> <span data-ttu-id="537ec-143">基於此區分，這類查詢會在沒有 `DefaultIfEmpty` 時轉譯為 `CROSS APPLY`，並在套用 `DefaultIfEmpty` 時轉譯為 `OUTER APPLY`。</span><span class="sxs-lookup"><span data-stu-id="537ec-143">Because of this distinction, this kind of queries translates to `CROSS APPLY` in the absence of `DefaultIfEmpty` and `OUTER APPLY` when `DefaultIfEmpty` is applied.</span></span> <span data-ttu-id="537ec-144">SQLite 之類的某些資料庫不支援 `APPLY` 運算子，因此這類查詢可能無法轉譯。</span><span class="sxs-lookup"><span data-stu-id="537ec-144">Certain databases like SQLite don't support `APPLY` operators so this kind of query may not be translated.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToApply)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
CROSS APPLY [Posts] AS [p]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
OUTER APPLY [Posts] AS [p]
```

## <a name="groupby"></a><span data-ttu-id="537ec-145">GroupBy</span><span class="sxs-lookup"><span data-stu-id="537ec-145">GroupBy</span></span>

<span data-ttu-id="537ec-146">LINQ GroupBy 運算子會建立類型 `IGrouping<TKey, TElement>` 的結果，其中 `TKey` 和 `TElement` 可以是任何任意類型。</span><span class="sxs-lookup"><span data-stu-id="537ec-146">LINQ GroupBy operators create a result of type `IGrouping<TKey, TElement>` where `TKey` and `TElement` could be any arbitrary type.</span></span> <span data-ttu-id="537ec-147">此外，`IGrouping` 會實 `IEnumerable<TElement>`，這表示您可以在群組之後使用任何 LINQ 運算子來撰寫它。</span><span class="sxs-lookup"><span data-stu-id="537ec-147">Furthermore, `IGrouping` implements `IEnumerable<TElement>`, which means you can compose over it using any LINQ operator after the grouping.</span></span> <span data-ttu-id="537ec-148">由於沒有任何資料庫結構可以代表 `IGrouping`，因此在大部分情況下，GroupBy 運算子不會有任何轉譯。</span><span class="sxs-lookup"><span data-stu-id="537ec-148">Since no database structure can represent an `IGrouping`, GroupBy operators have no translation in most cases.</span></span> <span data-ttu-id="537ec-149">當匯總運算子套用至每個群組（傳回純量）時，它可以轉譯成關係資料庫中的 SQL `GROUP BY`。</span><span class="sxs-lookup"><span data-stu-id="537ec-149">When an aggregate operator is applied to each group, which returns a scalar, it can be translated to SQL `GROUP BY` in relational databases.</span></span> <span data-ttu-id="537ec-150">SQL `GROUP BY` 也受到限制。</span><span class="sxs-lookup"><span data-stu-id="537ec-150">The SQL `GROUP BY` is restrictive too.</span></span> <span data-ttu-id="537ec-151">您只需要以純量值群組。</span><span class="sxs-lookup"><span data-stu-id="537ec-151">It requires you to group only by scalar values.</span></span> <span data-ttu-id="537ec-152">投影只能包含群組索引鍵資料行，或套用至資料行的任何匯總。</span><span class="sxs-lookup"><span data-stu-id="537ec-152">The projection can only contain grouping key columns or any aggregate applied over a column.</span></span> <span data-ttu-id="537ec-153">EF Core 可識別此模式，並將它轉譯為伺服器，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="537ec-153">EF Core identifies this pattern and translates it to the server, as in the following example:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupBy)]

```SQL
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
```

<span data-ttu-id="537ec-154">EF Core 也會翻譯查詢，其中群組上的匯總運算子會出現在 Where 或 OrderBy （或其他排序） LINQ 運算子中。</span><span class="sxs-lookup"><span data-stu-id="537ec-154">EF Core also translates queries where an aggregate operator on the grouping appears in a Where or OrderBy (or other ordering) LINQ operator.</span></span> <span data-ttu-id="537ec-155">它會針對 where 子句使用 SQL 中的 `HAVING` 子句。</span><span class="sxs-lookup"><span data-stu-id="537ec-155">It uses `HAVING` clause in SQL for the where clause.</span></span> <span data-ttu-id="537ec-156">套用 GroupBy 運算子之前的查詢部分，可以是任何複雜的查詢，只要它可以轉譯成伺服器即可。</span><span class="sxs-lookup"><span data-stu-id="537ec-156">The part of the query before applying the GroupBy operator can be any complex query as long as it can be translated to server.</span></span> <span data-ttu-id="537ec-157">此外，在群組查詢上套用匯總運算子，以從產生的來源移除群組之後，您可以像任何其他查詢一樣在其上撰寫。</span><span class="sxs-lookup"><span data-stu-id="537ec-157">Furthermore, once you apply aggregate operators on a grouping query to remove groupings from the resulting source, you can compose on top of it like any other query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupByFilter)]

```SQL
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
HAVING COUNT(*) > 0
ORDER BY [p].[AuthorId]
```

<span data-ttu-id="537ec-158">EF Core 支援的匯總運算子如下所示</span><span class="sxs-lookup"><span data-stu-id="537ec-158">The aggregate operators EF Core supports are as follows</span></span>

- <span data-ttu-id="537ec-159">平均</span><span class="sxs-lookup"><span data-stu-id="537ec-159">Average</span></span>
- <span data-ttu-id="537ec-160">[計數]</span><span class="sxs-lookup"><span data-stu-id="537ec-160">Count</span></span>
- <span data-ttu-id="537ec-161">LongCount</span><span class="sxs-lookup"><span data-stu-id="537ec-161">LongCount</span></span>
- <span data-ttu-id="537ec-162">Max</span><span class="sxs-lookup"><span data-stu-id="537ec-162">Max</span></span>
- <span data-ttu-id="537ec-163">Min</span><span class="sxs-lookup"><span data-stu-id="537ec-163">Min</span></span>
- <span data-ttu-id="537ec-164">Sum</span><span class="sxs-lookup"><span data-stu-id="537ec-164">Sum</span></span>

## <a name="left-join"></a><span data-ttu-id="537ec-165">左方聯結</span><span class="sxs-lookup"><span data-stu-id="537ec-165">Left Join</span></span>

<span data-ttu-id="537ec-166">雖然 Left Join 不是 LINQ 運算子，但是關係資料庫具有左方聯結的概念，這通常用於查詢中。</span><span class="sxs-lookup"><span data-stu-id="537ec-166">While Left Join isn't a LINQ operator, relational databases have the concept of a Left Join which is frequently used in queries.</span></span> <span data-ttu-id="537ec-167">LINQ 查詢中的特定模式會提供與伺服器上 `LEFT JOIN` 相同的結果。</span><span class="sxs-lookup"><span data-stu-id="537ec-167">A particular pattern in LINQ queries gives the same result as a `LEFT JOIN` on the server.</span></span> <span data-ttu-id="537ec-168">EF Core 可識別這種模式，並在伺服器端產生對等的 `LEFT JOIN`。</span><span class="sxs-lookup"><span data-stu-id="537ec-168">EF Core identifies such patterns and generates the equivalent `LEFT JOIN` on the server side.</span></span> <span data-ttu-id="537ec-169">此模式牽涉到在資料來源之間建立 GroupJoin，然後在群組來源上使用 SelectMany 運算子搭配 DefaultIfEmpty 來簡維群組，以便在內部沒有相關元素時符合 null。</span><span class="sxs-lookup"><span data-stu-id="537ec-169">The pattern involves creating a GroupJoin between both the data sources and then flattening out the grouping by using the SelectMany operator with DefaultIfEmpty on the grouping source to match null when the inner doesn't have a related element.</span></span> <span data-ttu-id="537ec-170">下列範例會顯示該模式的外觀，以及它所產生的內容。</span><span class="sxs-lookup"><span data-stu-id="537ec-170">The following example shows what that pattern looks like and what it generates.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#LeftJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

<span data-ttu-id="537ec-171">上述模式會在運算式樹狀架構中建立複雜的結構。</span><span class="sxs-lookup"><span data-stu-id="537ec-171">The above pattern creates a complex structure in the expression tree.</span></span> <span data-ttu-id="537ec-172">因此，EF Core 要求您在緊接在運算子後面的步驟中，將 GroupJoin 運算子的群組結果壓平合併。</span><span class="sxs-lookup"><span data-stu-id="537ec-172">Because of that, EF Core requires you to flatten out the grouping results of the GroupJoin operator in a step immediately following the operator.</span></span> <span data-ttu-id="537ec-173">即使使用 GroupJoin-DefaultIfEmpty-SelectMany，但在不同的模式下，我們也無法將其識別為左方聯結。</span><span class="sxs-lookup"><span data-stu-id="537ec-173">Even if the GroupJoin-DefaultIfEmpty-SelectMany is used but in a different pattern, we may not identify it as a Left Join.</span></span>
