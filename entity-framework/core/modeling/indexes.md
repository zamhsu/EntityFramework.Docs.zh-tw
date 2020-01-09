---
title: 索引-EF Core
author: roji
ms.date: 12/16/2019
ms.assetid: 85b92003-b692-417d-ac1d-76d40dce664b
uid: core/modeling/indexes
ms.openlocfilehash: 810fccc0c6b035f515107601b245811f7b4118a6
ms.sourcegitcommit: 32c51c22988c6f83ed4f8e50a1d01be3f4114e81
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/27/2019
ms.locfileid: "75502132"
---
# <a name="indexes"></a><span data-ttu-id="531e2-102">索引</span><span class="sxs-lookup"><span data-stu-id="531e2-102">Indexes</span></span>

<span data-ttu-id="531e2-103">索引是許多資料存放區的通用概念。</span><span class="sxs-lookup"><span data-stu-id="531e2-103">Indexes are a common concept across many data stores.</span></span> <span data-ttu-id="531e2-104">雖然其在資料存放區中的執行可能會有所不同，但它們是用來讓根據資料行（或資料行集）進行查閱更有效率。</span><span class="sxs-lookup"><span data-stu-id="531e2-104">While their implementation in the data store may vary, they are used to make lookups based on a column (or set of columns) more efficient.</span></span>

<span data-ttu-id="531e2-105">無法使用資料批註來建立索引。</span><span class="sxs-lookup"><span data-stu-id="531e2-105">Indexes cannot be created using data annotations.</span></span> <span data-ttu-id="531e2-106">您可以使用流暢的 API，在單一資料行上指定索引，如下所示：</span><span class="sxs-lookup"><span data-stu-id="531e2-106">You can use the Fluent API to specify an index on a single column as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Index.cs?name=Index&highlight=4)]

<span data-ttu-id="531e2-107">您也可以在一個以上的資料行上指定索引：</span><span class="sxs-lookup"><span data-stu-id="531e2-107">You can also specify an index over more than one column:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexComposite.cs?name=Composite&highlight=4)]

> [!NOTE]
> <span data-ttu-id="531e2-108">依照慣例，索引會在當做外鍵使用的每一個屬性（或一組屬性）中建立。</span><span class="sxs-lookup"><span data-stu-id="531e2-108">By convention, an index is created in each property (or set of properties) that are used as a foreign key.</span></span>
>
> <span data-ttu-id="531e2-109">EF Core 只支援每一組不同屬性的一個索引。</span><span class="sxs-lookup"><span data-stu-id="531e2-109">EF Core only supports one index per distinct set of properties.</span></span> <span data-ttu-id="531e2-110">如果您使用流暢的 API，在已定義索引（依慣例或先前的設定）的一組屬性上設定索引，則您將會變更該索引的定義。</span><span class="sxs-lookup"><span data-stu-id="531e2-110">If you use the Fluent API to configure an index on a set of properties that already has an index defined, either by convention or previous configuration, then you will be changing the definition of that index.</span></span> <span data-ttu-id="531e2-111">如果您想要進一步設定依照慣例所建立的索引，這會很有用。</span><span class="sxs-lookup"><span data-stu-id="531e2-111">This is useful if you want to further configure an index that was created by convention.</span></span>

## <a name="index-uniqueness"></a><span data-ttu-id="531e2-112">索引唯一性</span><span class="sxs-lookup"><span data-stu-id="531e2-112">Index uniqueness</span></span>

<span data-ttu-id="531e2-113">根據預設，索引不是唯一的：多個資料列允許索引的資料行集具有相同的值。</span><span class="sxs-lookup"><span data-stu-id="531e2-113">By default, indexes aren't unique: multiple rows are allowed to have the same value(s) for the index's column set.</span></span> <span data-ttu-id="531e2-114">您可以建立唯一的索引，如下所示：</span><span class="sxs-lookup"><span data-stu-id="531e2-114">You can make an index unique as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexUnique.cs?name=IndexUnique&highlight=5)]

<span data-ttu-id="531e2-115">嘗試使用相同的索引資料行集值插入多個實體時，將會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="531e2-115">Attempting to insert more than one entity with the same values for the index's column set will cause an exception to be thrown.</span></span>

## <a name="index-name"></a><span data-ttu-id="531e2-116">索引名稱</span><span class="sxs-lookup"><span data-stu-id="531e2-116">Index name</span></span>

<span data-ttu-id="531e2-117">依照慣例，在關係資料庫中建立的索引會命名為 `IX_<type name>_<property name>`。</span><span class="sxs-lookup"><span data-stu-id="531e2-117">By convention, indexes created in a relational database are named `IX_<type name>_<property name>`.</span></span> <span data-ttu-id="531e2-118">若為複合索引，`<property name>` 會變成以底線分隔的屬性名稱清單。</span><span class="sxs-lookup"><span data-stu-id="531e2-118">For composite indexes, `<property name>` becomes an underscore separated list of property names.</span></span>

<span data-ttu-id="531e2-119">您可以使用流暢的 API 來設定在資料庫中建立的索引名稱：</span><span class="sxs-lookup"><span data-stu-id="531e2-119">You can use the Fluent API to set the name of the index created in the database:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexName.cs?name=IndexName&highlight=5)]

## <a name="index-filter"></a><span data-ttu-id="531e2-120">索引篩選</span><span class="sxs-lookup"><span data-stu-id="531e2-120">Index filter</span></span>

<span data-ttu-id="531e2-121">某些關係資料庫可讓您指定篩選或部分索引。</span><span class="sxs-lookup"><span data-stu-id="531e2-121">Some relational databases allow you to specify a filtered or partial index.</span></span> <span data-ttu-id="531e2-122">這可讓您只為數據行值的子集編制索引，減少索引的大小，並同時改善效能和磁碟空間的使用。</span><span class="sxs-lookup"><span data-stu-id="531e2-122">This allows you to index only a subset of a column's values, reducing the index's size and improving both performance and disk space usage.</span></span> <span data-ttu-id="531e2-123">如需 SQL Server 篩選索引的詳細資訊，[請參閱檔](https://docs.microsoft.com/sql/relational-databases/indexes/create-filtered-indexes)集。</span><span class="sxs-lookup"><span data-stu-id="531e2-123">For more information on SQL Server filtered indexes, [see the documentation](https://docs.microsoft.com/sql/relational-databases/indexes/create-filtered-indexes).</span></span>

<span data-ttu-id="531e2-124">您可以使用流暢的 API 來指定索引的篩選準則（以 SQL 運算式形式提供）：</span><span class="sxs-lookup"><span data-stu-id="531e2-124">You can use the Fluent API to specify a filter on an index, provided as a SQL expression:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexFilter.cs?name=IndexFilter&highlight=5)]

<span data-ttu-id="531e2-125">使用 SQL Server 提供者 EF 時，會為屬於唯一索引一部分的所有可為 null 的資料行加入 `'IS NOT NULL'` 篩選準則。</span><span class="sxs-lookup"><span data-stu-id="531e2-125">When using the SQL Server provider EF adds an `'IS NOT NULL'` filter for all nullable columns that are part of a unique index.</span></span> <span data-ttu-id="531e2-126">若要覆寫此慣例，您可以提供 `null` 值。</span><span class="sxs-lookup"><span data-stu-id="531e2-126">To override this convention you can supply a `null` value.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexNoFilter.cs?name=IndexNoFilter&highlight=6)]

## <a name="included-columns"></a><span data-ttu-id="531e2-127">包含的資料行</span><span class="sxs-lookup"><span data-stu-id="531e2-127">Included columns</span></span>

<span data-ttu-id="531e2-128">某些關係資料庫可讓您設定一組資料行，這些資料行會包含在索引中，但不屬於其「金鑰」的一部分。</span><span class="sxs-lookup"><span data-stu-id="531e2-128">Some relational databases allow you to configure a set of columns which get included in the index, but aren't part of its "key".</span></span> <span data-ttu-id="531e2-129">當查詢中的所有資料行都包含在索引中當做索引鍵或非索引鍵資料行時，這可以大幅改善查詢效能，因為資料表本身不需要存取。</span><span class="sxs-lookup"><span data-stu-id="531e2-129">This can significantly improve query performance when all columns in the query are included in the index either as key or nonkey columns, as the table itself doesn't need to be accessed.</span></span> <span data-ttu-id="531e2-130">如需 SQL Server 內含資料行的詳細資訊，[請參閱檔](https://docs.microsoft.com/sql/relational-databases/indexes/create-indexes-with-included-columns)集。</span><span class="sxs-lookup"><span data-stu-id="531e2-130">For more information on SQL Server included columns, [see the documentation](https://docs.microsoft.com/sql/relational-databases/indexes/create-indexes-with-included-columns).</span></span>

<span data-ttu-id="531e2-131">在下列範例中，`Url` 資料行是索引鍵的一部分，因此對該資料行進行的任何查詢篩選都可以使用該索引。</span><span class="sxs-lookup"><span data-stu-id="531e2-131">In the following example, the `Url` column is part of the index key, so any query filtering on that column can use the index.</span></span> <span data-ttu-id="531e2-132">此外，只存取 `Title` 和 `PublishedOn` 資料行的查詢不需要存取資料表，而且會更有效率地執行：</span><span class="sxs-lookup"><span data-stu-id="531e2-132">But in addition, queries accessing only the `Title` and `PublishedOn` columns will not need to access the table and will run more efficiently:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexInclude.cs?name=IndexInclude&highlight=5-9)]
