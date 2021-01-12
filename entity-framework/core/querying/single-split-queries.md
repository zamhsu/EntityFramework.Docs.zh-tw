---
title: 單一與分割查詢-EF Core
description: 使用 Entity Framework Core 將查詢轉譯成 SQL 中的單一和分割查詢
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/single-split-queries
ms.openlocfilehash: 8615e7ba5247a90a1f980e9f7b1b23c81170971f
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128988"
---
# <a name="split-queries"></a><span data-ttu-id="9ba86-103">分割查詢</span><span class="sxs-lookup"><span data-stu-id="9ba86-103">Split queries</span></span>

## <a name="single-queries"></a><span data-ttu-id="9ba86-104">單一查詢</span><span class="sxs-lookup"><span data-stu-id="9ba86-104">Single queries</span></span>

<span data-ttu-id="9ba86-105">在關係資料庫中，所有相關的實體都會透過在單一查詢中導入聯結的方式載入。</span><span class="sxs-lookup"><span data-stu-id="9ba86-105">In relational databases, all related entities are loaded by introducing JOINs in single query.</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

<span data-ttu-id="9ba86-106">如果一般的 blog 有多個相關的文章，這些貼文的資料列將會複製 blog 的資訊。</span><span class="sxs-lookup"><span data-stu-id="9ba86-106">If a typical blog has multiple related posts, rows for these posts will duplicate the blog's information.</span></span> <span data-ttu-id="9ba86-107">這項重複會導致所謂的「笛卡兒爆炸」問題。</span><span class="sxs-lookup"><span data-stu-id="9ba86-107">This duplication leads to the so-called "cartesian explosion" problem.</span></span> <span data-ttu-id="9ba86-108">載入更多一對多關聯性時，重複的資料量可能會成長，而且會對應用程式的效能造成不良影響。</span><span class="sxs-lookup"><span data-stu-id="9ba86-108">As more one-to-many relationships are loaded, the amount of duplicated data may grow and adversely affect the performance of your application.</span></span>

## <a name="split-queries"></a><span data-ttu-id="9ba86-109">分割查詢</span><span class="sxs-lookup"><span data-stu-id="9ba86-109">Split queries</span></span>

> [!NOTE]
> <span data-ttu-id="9ba86-110">這項功能是在 EF Core 5.0 中引進。</span><span class="sxs-lookup"><span data-stu-id="9ba86-110">This feature was introduced in EF Core 5.0.</span></span> <span data-ttu-id="9ba86-111">它只有在使用時才適用 `Include` 。</span><span class="sxs-lookup"><span data-stu-id="9ba86-111">It only works when using `Include`.</span></span> <span data-ttu-id="9ba86-112">[此問題](https://github.com/dotnet/efcore/issues/21234) 是在沒有的情況下載入投影中的相關資料時，追蹤分割查詢的支援 `Include` 。</span><span class="sxs-lookup"><span data-stu-id="9ba86-112">[This issue](https://github.com/dotnet/efcore/issues/21234) is tracking support for split query when loading related data in projection without `Include`.</span></span>

<span data-ttu-id="9ba86-113">EF 可讓您指定應該將指定的 LINQ 查詢 *分割* 成多個 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="9ba86-113">EF allows you to specify that a given LINQ query should be *split* into multiple SQL queries.</span></span> <span data-ttu-id="9ba86-114">分割查詢會為每個包含的集合導覽產生額外的 SQL 查詢，而不是聯結。</span><span class="sxs-lookup"><span data-stu-id="9ba86-114">Instead of JOINs, split queries generate an additional SQL query for each included collection navigation:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs?name=AsSplitQuery&highlight=5)]

<span data-ttu-id="9ba86-115">它會產生下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="9ba86-115">It will produce the following SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
ORDER BY [b].[BlogId]

SELECT [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title], [b].[BlogId]
FROM [Blogs] AS [b]
INNER JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId]
```

> [!NOTE]
> <span data-ttu-id="9ba86-116">一對多相關實體一律會透過相同查詢中的聯結載入，因為它不會影響效能。</span><span class="sxs-lookup"><span data-stu-id="9ba86-116">One-to-one related entities are always loaded via JOINs in the same query, as it has no performance impact.</span></span>

## <a name="enabling-split-queries-globally"></a><span data-ttu-id="9ba86-117">全域啟用分割查詢</span><span class="sxs-lookup"><span data-stu-id="9ba86-117">Enabling split queries globally</span></span>

<span data-ttu-id="9ba86-118">您也可以將分割查詢設定為應用程式內容的預設值：</span><span class="sxs-lookup"><span data-stu-id="9ba86-118">You can also configure split queries as the default for your application's context:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

<span data-ttu-id="9ba86-119">當分割查詢設定為預設值時，仍然可以將特定查詢設定為以單一查詢的形式執行：</span><span class="sxs-lookup"><span data-stu-id="9ba86-119">When split queries are configured as the default, it's still possible to configure specific queries to execute as single queries:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs?name=AsSingleQuery&highlight=5)]

<span data-ttu-id="9ba86-120">EF Core 在沒有任何設定的情況下，預設會使用單一查詢模式。</span><span class="sxs-lookup"><span data-stu-id="9ba86-120">EF Core uses single query mode by default in the absence of any configuration.</span></span> <span data-ttu-id="9ba86-121">因為這可能會造成效能問題，所以每當符合下列條件時，EF Core 就會產生警告：</span><span class="sxs-lookup"><span data-stu-id="9ba86-121">Since it may cause performance issues, EF Core generates a warning whenever following conditions are met:</span></span>

- <span data-ttu-id="9ba86-122">EF Core 會偵測到查詢會載入多個集合。</span><span class="sxs-lookup"><span data-stu-id="9ba86-122">EF Core detects that the query loads multiple collections.</span></span>
- <span data-ttu-id="9ba86-123">使用者尚未設定全域的查詢分割模式。</span><span class="sxs-lookup"><span data-stu-id="9ba86-123">User hasn't configured query splitting mode globally.</span></span>
- <span data-ttu-id="9ba86-124">`AsSingleQuery` / `AsSplitQuery` 查詢上的使用者未使用運算子。</span><span class="sxs-lookup"><span data-stu-id="9ba86-124">User hasn't used `AsSingleQuery`/`AsSplitQuery` operator on the query.</span></span>

<span data-ttu-id="9ba86-125">若要關閉警告，請將查詢分割模式全域或在查詢層級設定為適當的值。</span><span class="sxs-lookup"><span data-stu-id="9ba86-125">To turn off the warning, configure query splitting mode globally or at the query level to an appropriate value.</span></span>

## <a name="characteristics-of-split-queries"></a><span data-ttu-id="9ba86-126">分割查詢的特性</span><span class="sxs-lookup"><span data-stu-id="9ba86-126">Characteristics of split queries</span></span>

<span data-ttu-id="9ba86-127">雖然分割查詢可避免與聯結和笛卡兒爆炸相關的效能問題，但它也有一些缺點：</span><span class="sxs-lookup"><span data-stu-id="9ba86-127">While split query avoids the performance issues associated with JOINs and cartesian explosion, it also has some drawbacks:</span></span>

- <span data-ttu-id="9ba86-128">雖然大部分資料庫保證單一查詢的資料一致性，但多個查詢沒有這類保證存在。</span><span class="sxs-lookup"><span data-stu-id="9ba86-128">While most databases guarantee data consistency for single queries, no such guarantees exist for multiple queries.</span></span> <span data-ttu-id="9ba86-129">如果在執行查詢時同時更新資料庫，則產生的資料可能不一致。</span><span class="sxs-lookup"><span data-stu-id="9ba86-129">If the database is updated concurrently when executing your queries, resulting data may not be consistent.</span></span> <span data-ttu-id="9ba86-130">您可以藉由將查詢包裝在可序列化或快照集交易中來加以減輕，雖然這樣做可能會產生自己的效能問題。</span><span class="sxs-lookup"><span data-stu-id="9ba86-130">You can mitigate it by wrapping the queries in a serializable or snapshot transaction, although doing so may create performance issues of its own.</span></span> <span data-ttu-id="9ba86-131">如需詳細資訊，請參閱資料庫檔案。</span><span class="sxs-lookup"><span data-stu-id="9ba86-131">For more information, see your database's documentation.</span></span>
- <span data-ttu-id="9ba86-132">每個查詢目前暗示對您資料庫的額外網路往返。</span><span class="sxs-lookup"><span data-stu-id="9ba86-132">Each query currently implies an additional network roundtrip to your database.</span></span> <span data-ttu-id="9ba86-133">多個網路往返可能會降低效能，特別是在資料庫的延遲很高 (例如雲端服務) 的情況下。</span><span class="sxs-lookup"><span data-stu-id="9ba86-133">Multiple network roundtrip can degrade performance, especially where latency to the database is high (for example, cloud services).</span></span>
- <span data-ttu-id="9ba86-134">雖然某些資料庫允許同時使用多個查詢的結果 (使用 MARS 的 SQL Server，Sqlite) 大部分只允許在任何給定的時間點啟用單一查詢。</span><span class="sxs-lookup"><span data-stu-id="9ba86-134">While some databases allow consuming the results of multiple queries at the same time (SQL Server with MARS, Sqlite), most allow only a single query to be active at any given point.</span></span> <span data-ttu-id="9ba86-135">因此，必須先在應用程式的記憶體中緩衝處理舊版查詢的所有結果，然後再執行較新的查詢，這會導致記憶體需求增加。</span><span class="sxs-lookup"><span data-stu-id="9ba86-135">So all results from earlier queries must be buffered in your application's memory before executing later queries, which leads to increased memory requirements.</span></span>

<span data-ttu-id="9ba86-136">可惜的是，載入適用于所有案例的相關實體沒有一個策略。</span><span class="sxs-lookup"><span data-stu-id="9ba86-136">Unfortunately, there isn't one strategy for loading related entities that fits all scenarios.</span></span> <span data-ttu-id="9ba86-137">請仔細考慮單一和分割查詢的優點和缺點，以選取符合您需求的查詢。</span><span class="sxs-lookup"><span data-stu-id="9ba86-137">Carefully consider the advantages and disadvantages of single and split queries to select the one that fits your needs.</span></span>
