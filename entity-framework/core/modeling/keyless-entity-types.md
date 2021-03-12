---
title: 無索引鍵實體類型-EF Core
description: 如何使用 Entity Framework Core 設定無索引鍵實體類型
author: AndriySvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: 488b21767993474d3e90677ee1e480a970b09edb
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023935"
---
# <a name="keyless-entity-types"></a><span data-ttu-id="b7ebc-103">無索引鍵實體類型</span><span class="sxs-lookup"><span data-stu-id="b7ebc-103">Keyless Entity Types</span></span>

> [!NOTE]
> <span data-ttu-id="b7ebc-104">這項功能是在查詢類型的名稱下加入。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-104">This feature was added under the name of query types.</span></span> <span data-ttu-id="b7ebc-105">在 EF Core 3.0 中，概念已重新命名為無索引鍵的實體類型。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-105">In EF Core 3.0 the concept was renamed to keyless entity types.</span></span> <span data-ttu-id="b7ebc-106">`[Keyless]`資料批註在 EFCore 5.0 中變成可供使用。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-106">The `[Keyless]` Data Annotation became available in EFCore 5.0.</span></span>

<span data-ttu-id="b7ebc-107">除了一般實體類型之外，EF Core 模型也可以包含 _無索引鍵的實體類型_，可用來針對不包含索引鍵值的資料執行資料庫查詢。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-107">In addition to regular entity types, an EF Core model can contain _keyless entity types_, which can be used to carry out database queries against data that doesn't contain key values.</span></span>

## <a name="defining-keyless-entity-types"></a><span data-ttu-id="b7ebc-108">定義無索引鍵實體類型</span><span class="sxs-lookup"><span data-stu-id="b7ebc-108">Defining Keyless entity types</span></span>

<span data-ttu-id="b7ebc-109">您可以使用資料批註或流暢的 API 來定義無索引鍵實體類型：</span><span class="sxs-lookup"><span data-stu-id="b7ebc-109">Keyless entity types can be defined using either the Data Annotation or the Fluent API:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="b7ebc-110">資料批註</span><span class="sxs-lookup"><span data-stu-id="b7ebc-110">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Keyless.cs?Name=Keyless&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="b7ebc-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="b7ebc-111">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Keyless.cs?Name=Keyless&highlight=4)]

***

## <a name="keyless-entity-types-characteristics"></a><span data-ttu-id="b7ebc-112">無索引鍵實體類型特性</span><span class="sxs-lookup"><span data-stu-id="b7ebc-112">Keyless entity types characteristics</span></span>

<span data-ttu-id="b7ebc-113">無索引鍵實體類型支援許多與一般實體類型相同的對應功能，例如繼承對應和導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-113">Keyless entity types support many of the same mapping capabilities as regular entity types, like inheritance mapping and navigation properties.</span></span> <span data-ttu-id="b7ebc-114">在關聯式存放區上，他們可以透過流暢的 API 方法或資料批註來設定目標資料庫物件和資料行。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-114">On relational stores, they can configure the target database objects and columns via fluent API methods or data annotations.</span></span>

<span data-ttu-id="b7ebc-115">不過，它們與一般實體類型不同，因為它們：</span><span class="sxs-lookup"><span data-stu-id="b7ebc-115">However, they are different from regular entity types in that they:</span></span>

- <span data-ttu-id="b7ebc-116">無法定義索引鍵。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-116">Cannot have a key defined.</span></span>
- <span data-ttu-id="b7ebc-117">永遠不會追蹤 _DbCoNtext_ 中的變更，因此永遠不會在資料庫中插入、更新或刪除。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-117">Are never tracked for changes in the _DbContext_ and therefore are never inserted, updated or deleted on the database.</span></span>
- <span data-ttu-id="b7ebc-118">慣例不會探索到。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-118">Are never discovered by convention.</span></span>
- <span data-ttu-id="b7ebc-119">只支援一組流覽對應功能，特別是：</span><span class="sxs-lookup"><span data-stu-id="b7ebc-119">Only support a subset of navigation mapping capabilities, specifically:</span></span>
  - <span data-ttu-id="b7ebc-120">它們絕對不能做為關聯性的主要端點。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-120">They may never act as the principal end of a relationship.</span></span>
  - <span data-ttu-id="b7ebc-121">他們可能沒有擁有實體的導覽</span><span class="sxs-lookup"><span data-stu-id="b7ebc-121">They may not have navigations to owned entities</span></span>
  - <span data-ttu-id="b7ebc-122">它們只能包含指向一般實體的參考導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-122">They can only contain reference navigation properties pointing to regular entities.</span></span>
  - <span data-ttu-id="b7ebc-123">實體不能包含無索引鍵實體類型的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-123">Entities cannot contain navigation properties to keyless entity types.</span></span>
- <span data-ttu-id="b7ebc-124">需要使用 `[Keyless]` 資料批註或 `.HasNoKey()` 方法呼叫來設定。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-124">Need to be configured with a `[Keyless]` data annotation or a `.HasNoKey()` method call.</span></span>
- <span data-ttu-id="b7ebc-125">可能對應至 _定義查詢_。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-125">May be mapped to a _defining query_.</span></span> <span data-ttu-id="b7ebc-126">定義查詢是在模型中宣告的查詢，可作為無索引鍵實體類型的資料來源。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-126">A defining query is a query declared in the model that acts as a data source for a keyless entity type.</span></span>

## <a name="usage-scenarios"></a><span data-ttu-id="b7ebc-127">使用方式情節</span><span class="sxs-lookup"><span data-stu-id="b7ebc-127">Usage scenarios</span></span>

<span data-ttu-id="b7ebc-128">無索引鍵實體類型的部分主要使用案例如下：</span><span class="sxs-lookup"><span data-stu-id="b7ebc-128">Some of the main usage scenarios for keyless entity types are:</span></span>

- <span data-ttu-id="b7ebc-129">做為 [原始 SQL 查詢](xref:core/querying/raw-sql)的傳回型別。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-129">Serving as the return type for [raw SQL queries](xref:core/querying/raw-sql).</span></span>
- <span data-ttu-id="b7ebc-130">對應至不含主鍵的資料庫 views。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-130">Mapping to database views that do not contain a primary key.</span></span>
- <span data-ttu-id="b7ebc-131">對應至未定義主鍵的資料表。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-131">Mapping to tables that do not have a primary key defined.</span></span>
- <span data-ttu-id="b7ebc-132">對應至模型中定義的查詢。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-132">Mapping to queries defined in the model.</span></span>

## <a name="mapping-to-database-objects"></a><span data-ttu-id="b7ebc-133">對應至資料庫物件</span><span class="sxs-lookup"><span data-stu-id="b7ebc-133">Mapping to database objects</span></span>

<span data-ttu-id="b7ebc-134">使用或流暢的 API 即可將無索引鍵實體類型對應至資料庫 `ToTable` 物件 `ToView` 。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-134">Mapping a keyless entity type to a database object is achieved using the `ToTable` or `ToView` fluent API.</span></span> <span data-ttu-id="b7ebc-135">從 EF Core 的觀點來看，這個方法中所指定的資料庫物件是一個 _視圖_，這表示它被視為唯讀查詢來源，不能是 update、insert 或 delete 作業的目標。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-135">From the perspective of EF Core, the database object specified in this method is a _view_, meaning that it is treated as a read-only query source and cannot be the target of update, insert or delete operations.</span></span> <span data-ttu-id="b7ebc-136">但是，這並不表示資料庫物件實際上必須是資料庫的視圖。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-136">However, this does not mean that the database object is actually required to be a database view.</span></span> <span data-ttu-id="b7ebc-137">它也可以是將被視為唯讀的資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-137">It can alternatively be a database table that will be treated as read-only.</span></span> <span data-ttu-id="b7ebc-138">相反地，對於一般的實體類型，EF Core 會假設方法中指定的資料庫物件 `ToTable` 可以視為 _資料表_，這表示它可以當做查詢來源使用，也可以當做 update、delete 和 insert 作業的目標。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-138">Conversely, for regular entity types, EF Core assumes that a database object specified in the `ToTable` method can be treated as a _table_, meaning that it can be used as a query source but also targeted by update, delete and insert operations.</span></span> <span data-ttu-id="b7ebc-139">事實上，您可以在中指定資料庫的名稱 `ToTable` ，而且只要視圖設定為可在資料庫上更新，一切都應可正常運作。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-139">In fact, you can specify the name of a database view in `ToTable` and everything should work fine as long as the view is configured to be updatable on the database.</span></span>

> [!NOTE]
> <span data-ttu-id="b7ebc-140">`ToView` 假設物件已經存在於資料庫中，而不是由遷移所建立。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-140">`ToView` assumes that the object already exists in the database and it won't be created by migrations.</span></span>

## <a name="example"></a><span data-ttu-id="b7ebc-141">範例</span><span class="sxs-lookup"><span data-stu-id="b7ebc-141">Example</span></span>

<span data-ttu-id="b7ebc-142">下列範例示範如何使用無索引鍵的實體類型來查詢資料庫檢視。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-142">The following example shows how to use keyless entity types to query a database view.</span></span>

> [!TIP]
> <span data-ttu-id="b7ebc-143">您可以在 GitHub 上檢視此文章的[範例](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/KeylessEntityTypes) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-143">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/KeylessEntityTypes) on GitHub.</span></span>

<span data-ttu-id="b7ebc-144">首先，我們會定義簡單的 Blog 和 Post 模型：</span><span class="sxs-lookup"><span data-stu-id="b7ebc-144">First, we define a simple Blog and Post model:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

<span data-ttu-id="b7ebc-145">接下來，我們會定義一個簡單的資料庫檢視，讓我們查詢與每個 blog 相關聯的貼文數目：</span><span class="sxs-lookup"><span data-stu-id="b7ebc-145">Next, we define a simple database view that will allow us to query the number of posts associated with each blog:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

<span data-ttu-id="b7ebc-146">接下來，我們會定義一個類別來保存資料庫檢視的結果：</span><span class="sxs-lookup"><span data-stu-id="b7ebc-146">Next, we define a class to hold the result from the database view:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

<span data-ttu-id="b7ebc-147">接下來，我們會使用 API 在 _OnModelCreating_ 中設定無索引鍵實體類型 `HasNoKey` 。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-147">Next, we configure the keyless entity type in _OnModelCreating_ using the `HasNoKey` API.</span></span>
<span data-ttu-id="b7ebc-148">我們會使用流暢的設定 API 來設定無索引鍵實體類型的對應：</span><span class="sxs-lookup"><span data-stu-id="b7ebc-148">We use fluent configuration API to configure the mapping for the keyless entity type:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

<span data-ttu-id="b7ebc-149">接下來，我們會將設定 `DbContext` 為包含 `DbSet<T>` ：</span><span class="sxs-lookup"><span data-stu-id="b7ebc-149">Next, we configure the `DbContext` to include the `DbSet<T>`:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

<span data-ttu-id="b7ebc-150">最後，我們可以用標準方式來查詢資料庫檢視：</span><span class="sxs-lookup"><span data-stu-id="b7ebc-150">Finally, we can query the database view in the standard way:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> <span data-ttu-id="b7ebc-151">請注意，我們也定義了內容層級查詢屬性 (DbSet) 做為針對此型別進行查詢的根。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-151">Note we have also defined a context level query property (DbSet) to act as a root for queries against this type.</span></span>

> [!TIP]
> <span data-ttu-id="b7ebc-152">若要使用記憶體內部提供者來測試對應至 views 的無索引鍵實體型別，請透過將它們對應至查詢 `ToInMemoryQuery` 。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-152">To test keyless entity types mapped to views using the in-memory provider map them to a query via `ToInMemoryQuery`.</span></span> <span data-ttu-id="b7ebc-153">如需詳細資訊，請參閱使用此技術的可執行 [範例](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Testing/ItemsWebApi/) 。</span><span class="sxs-lookup"><span data-stu-id="b7ebc-153">See a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Testing/ItemsWebApi/) using this technique for more details.</span></span>
