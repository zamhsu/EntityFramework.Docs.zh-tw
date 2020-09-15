---
title: 索引-EF Core
description: 在 Entity Framework Core 模型中設定索引
author: roji
ms.date: 12/16/2019
uid: core/modeling/indexes
ms.openlocfilehash: 3a89f1ae9727dcd8f086e915e666721572636314
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071403"
---
# <a name="indexes"></a><span data-ttu-id="26b49-103">索引</span><span class="sxs-lookup"><span data-stu-id="26b49-103">Indexes</span></span>

<span data-ttu-id="26b49-104">在許多資料存放區中，索引是常見的概念。</span><span class="sxs-lookup"><span data-stu-id="26b49-104">Indexes are a common concept across many data stores.</span></span> <span data-ttu-id="26b49-105">雖然它們在資料存放區中的執行方式可能不同，但它們是用來根據資料行 (或資料行集進行查閱，) 更有效率。</span><span class="sxs-lookup"><span data-stu-id="26b49-105">While their implementation in the data store may vary, they are used to make lookups based on a column (or set of columns) more efficient.</span></span>

<span data-ttu-id="26b49-106">無法使用資料批註建立索引。</span><span class="sxs-lookup"><span data-stu-id="26b49-106">Indexes cannot be created using data annotations.</span></span> <span data-ttu-id="26b49-107">您可以使用流暢的 API，在單一資料行上指定索引，如下所示：</span><span class="sxs-lookup"><span data-stu-id="26b49-107">You can use the Fluent API to specify an index on a single column as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Index.cs?name=Index&highlight=4)]

<span data-ttu-id="26b49-108">您也可以在一個以上的資料行上指定索引：</span><span class="sxs-lookup"><span data-stu-id="26b49-108">You can also specify an index over more than one column:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexComposite.cs?name=Composite&highlight=4)]

> [!NOTE]
> <span data-ttu-id="26b49-109">依照慣例，會在每個屬性 (中建立索引，或使用一組屬性) 做為外鍵。</span><span class="sxs-lookup"><span data-stu-id="26b49-109">By convention, an index is created in each property (or set of properties) that are used as a foreign key.</span></span>
>
> <span data-ttu-id="26b49-110">EF Core 僅針對每一組不同的屬性支援一個索引。</span><span class="sxs-lookup"><span data-stu-id="26b49-110">EF Core only supports one index per distinct set of properties.</span></span> <span data-ttu-id="26b49-111">如果您使用流暢的 API 來設定一組屬性的索引，這些屬性已定義索引（依照慣例或先前的設定），則您將變更該索引的定義。</span><span class="sxs-lookup"><span data-stu-id="26b49-111">If you use the Fluent API to configure an index on a set of properties that already has an index defined, either by convention or previous configuration, then you will be changing the definition of that index.</span></span> <span data-ttu-id="26b49-112">如果您想要進一步設定依慣例建立的索引，這會很有用。</span><span class="sxs-lookup"><span data-stu-id="26b49-112">This is useful if you want to further configure an index that was created by convention.</span></span>

## <a name="index-uniqueness"></a><span data-ttu-id="26b49-113">索引唯一性</span><span class="sxs-lookup"><span data-stu-id="26b49-113">Index uniqueness</span></span>

<span data-ttu-id="26b49-114">根據預設，索引不是唯一的：多個資料列可以有相同的值， (s) 用於索引的資料行集。</span><span class="sxs-lookup"><span data-stu-id="26b49-114">By default, indexes aren't unique: multiple rows are allowed to have the same value(s) for the index's column set.</span></span> <span data-ttu-id="26b49-115">您可以建立唯一的索引，如下所示：</span><span class="sxs-lookup"><span data-stu-id="26b49-115">You can make an index unique as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexUnique.cs?name=IndexUnique&highlight=5)]

<span data-ttu-id="26b49-116">嘗試插入多個具有相同索引資料行集值的實體，將會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="26b49-116">Attempting to insert more than one entity with the same values for the index's column set will cause an exception to be thrown.</span></span>

## <a name="index-name"></a><span data-ttu-id="26b49-117">索引名稱</span><span class="sxs-lookup"><span data-stu-id="26b49-117">Index name</span></span>

<span data-ttu-id="26b49-118">依照慣例，在關係資料庫中建立的索引會命名為 `IX_<type name>_<property name>` 。</span><span class="sxs-lookup"><span data-stu-id="26b49-118">By convention, indexes created in a relational database are named `IX_<type name>_<property name>`.</span></span> <span data-ttu-id="26b49-119">如果是複合索引， `<property name>` 則會變成以底線分隔的屬性名稱清單。</span><span class="sxs-lookup"><span data-stu-id="26b49-119">For composite indexes, `<property name>` becomes an underscore separated list of property names.</span></span>

<span data-ttu-id="26b49-120">您可以使用流暢的 API 來設定在資料庫中建立的索引名稱：</span><span class="sxs-lookup"><span data-stu-id="26b49-120">You can use the Fluent API to set the name of the index created in the database:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexName.cs?name=IndexName&highlight=5)]

## <a name="index-filter"></a><span data-ttu-id="26b49-121">索引篩選</span><span class="sxs-lookup"><span data-stu-id="26b49-121">Index filter</span></span>

<span data-ttu-id="26b49-122">某些關係資料庫可讓您指定篩選或部分索引。</span><span class="sxs-lookup"><span data-stu-id="26b49-122">Some relational databases allow you to specify a filtered or partial index.</span></span> <span data-ttu-id="26b49-123">這可讓您只編制資料行值的子集索引，減少索引的大小，並同時改善效能和磁碟空間使用量。</span><span class="sxs-lookup"><span data-stu-id="26b49-123">This allows you to index only a subset of a column's values, reducing the index's size and improving both performance and disk space usage.</span></span> <span data-ttu-id="26b49-124">如需 SQL Server 篩選索引的詳細資訊， [請參閱檔](/sql/relational-databases/indexes/create-filtered-indexes)。</span><span class="sxs-lookup"><span data-stu-id="26b49-124">For more information on SQL Server filtered indexes, [see the documentation](/sql/relational-databases/indexes/create-filtered-indexes).</span></span>

<span data-ttu-id="26b49-125">您可以使用流暢的 API 來指定索引的篩選準則，提供為 SQL 運算式：</span><span class="sxs-lookup"><span data-stu-id="26b49-125">You can use the Fluent API to specify a filter on an index, provided as a SQL expression:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexFilter.cs?name=IndexFilter&highlight=5)]

<span data-ttu-id="26b49-126">使用 SQL Server 提供者 EF 時 `'IS NOT NULL'` ，會針對屬於唯一索引一部分的可為 null 的資料行加入篩選。</span><span class="sxs-lookup"><span data-stu-id="26b49-126">When using the SQL Server provider EF adds an `'IS NOT NULL'` filter for all nullable columns that are part of a unique index.</span></span> <span data-ttu-id="26b49-127">若要覆寫此慣例，您可以提供 `null` 值。</span><span class="sxs-lookup"><span data-stu-id="26b49-127">To override this convention you can supply a `null` value.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexNoFilter.cs?name=IndexNoFilter&highlight=6)]

## <a name="included-columns"></a><span data-ttu-id="26b49-128">包含的資料行</span><span class="sxs-lookup"><span data-stu-id="26b49-128">Included columns</span></span>

<span data-ttu-id="26b49-129">某些關係資料庫可讓您設定一組包含在索引中的資料行，但不是其「索引鍵」的一部分。</span><span class="sxs-lookup"><span data-stu-id="26b49-129">Some relational databases allow you to configure a set of columns which get included in the index, but aren't part of its "key".</span></span> <span data-ttu-id="26b49-130">當查詢中的所有資料行都包含在索引中做為索引鍵或非索引鍵資料行時，這可大幅提升查詢效能，因為資料表本身不需要存取。</span><span class="sxs-lookup"><span data-stu-id="26b49-130">This can significantly improve query performance when all columns in the query are included in the index either as key or nonkey columns, as the table itself doesn't need to be accessed.</span></span> <span data-ttu-id="26b49-131">如需 SQL Server 內含資料行的詳細資訊， [請參閱檔](/sql/relational-databases/indexes/create-indexes-with-included-columns)。</span><span class="sxs-lookup"><span data-stu-id="26b49-131">For more information on SQL Server included columns, [see the documentation](/sql/relational-databases/indexes/create-indexes-with-included-columns).</span></span>

<span data-ttu-id="26b49-132">在下列範例中，資料 `Url` 行是索引鍵的一部分，因此該資料行上的任何查詢篩選都可以使用索引。</span><span class="sxs-lookup"><span data-stu-id="26b49-132">In the following example, the `Url` column is part of the index key, so any query filtering on that column can use the index.</span></span> <span data-ttu-id="26b49-133">此外，只存取和資料行的 `Title` 查詢 `PublishedOn` 不需要存取資料表，而且會更有效率地執行：</span><span class="sxs-lookup"><span data-stu-id="26b49-133">But in addition, queries accessing only the `Title` and `PublishedOn` columns will not need to access the table and will run more efficiently:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexInclude.cs?name=IndexInclude&highlight=5-9)]
