---
title: 無索引鍵實體類型-EF Core
description: 如何使用 Entity Framework Core 設定無索引鍵實體類型
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: 129e24b154ba32583435aeb742dbf478350344e8
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824669"
---
# <a name="keyless-entity-types"></a><span data-ttu-id="4c6a3-103">無索引鍵實體類型</span><span class="sxs-lookup"><span data-stu-id="4c6a3-103">Keyless Entity Types</span></span>

> [!NOTE]
> <span data-ttu-id="4c6a3-104">這項功能已在 EF Core 2.1 中的查詢類型名稱下新增。</span><span class="sxs-lookup"><span data-stu-id="4c6a3-104">This feature was added in EF Core 2.1 under the name of query types.</span></span> <span data-ttu-id="4c6a3-105">在 EF Core 3.0 中，概念已重新命名為無索引鍵的實體類型。</span><span class="sxs-lookup"><span data-stu-id="4c6a3-105">In EF Core 3.0 the concept was renamed to keyless entity types.</span></span>

<span data-ttu-id="4c6a3-106">除了一般實體類型之外，EF Core 模型也可以包含_無索引鍵的實體類型_，可用來對不包含索引鍵值的資料執行資料庫查詢。</span><span class="sxs-lookup"><span data-stu-id="4c6a3-106">In addition to regular entity types, an EF Core model can contain _keyless entity types_, which can be used to carry out database queries against data that doesn't contain key values.</span></span>

## <a name="keyless-entity-types-characteristics"></a><span data-ttu-id="4c6a3-107">無索引鍵實體類型特性</span><span class="sxs-lookup"><span data-stu-id="4c6a3-107">Keyless entity types characteristics</span></span>

<span data-ttu-id="4c6a3-108">無索引鍵實體類型支援許多與一般實體類型相同的對應功能，例如繼承對應和導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="4c6a3-108">Keyless entity types support many of the same mapping capabilities as regular entity types, like inheritance mapping and navigation properties.</span></span> <span data-ttu-id="4c6a3-109">在關聯式存放區上，他們可以透過 Fluent API 方法或資料批註來設定目標資料庫物件和資料行。</span><span class="sxs-lookup"><span data-stu-id="4c6a3-109">On relational stores, they can configure the target database objects and columns via fluent API methods or data annotations.</span></span>

<span data-ttu-id="4c6a3-110">不過，它們與一般實體類型不同之處如下：</span><span class="sxs-lookup"><span data-stu-id="4c6a3-110">However, they are different from regular entity types in that they:</span></span>

- <span data-ttu-id="4c6a3-111">不能定義索引鍵。</span><span class="sxs-lookup"><span data-stu-id="4c6a3-111">Cannot have a key defined.</span></span>
- <span data-ttu-id="4c6a3-112">永遠不會針對_DbCoNtext_中的變更進行追蹤，因此永遠不會在資料庫上插入、更新或刪除。</span><span class="sxs-lookup"><span data-stu-id="4c6a3-112">Are never tracked for changes in the _DbContext_ and therefore are never inserted, updated or deleted on the database.</span></span>
- <span data-ttu-id="4c6a3-113">一律不會依照慣例來探索。</span><span class="sxs-lookup"><span data-stu-id="4c6a3-113">Are never discovered by convention.</span></span>
- <span data-ttu-id="4c6a3-114">僅支援導覽對應功能的子集，特別是：</span><span class="sxs-lookup"><span data-stu-id="4c6a3-114">Only support a subset of navigation mapping capabilities, specifically:</span></span>
  - <span data-ttu-id="4c6a3-115">它們可能永遠不會做為關聯性的主要端點。</span><span class="sxs-lookup"><span data-stu-id="4c6a3-115">They may never act as the principal end of a relationship.</span></span>
  - <span data-ttu-id="4c6a3-116">他們可能不會有所擁有實體的導覽</span><span class="sxs-lookup"><span data-stu-id="4c6a3-116">They may not have navigations to owned entities</span></span>
  - <span data-ttu-id="4c6a3-117">它們只能包含指向一般實體的參考導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="4c6a3-117">They can only contain reference navigation properties pointing to regular entities.</span></span>
  - <span data-ttu-id="4c6a3-118">實體不能包含無索引鍵實體類型的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="4c6a3-118">Entities cannot contain navigation properties to keyless entity types.</span></span>
- <span data-ttu-id="4c6a3-119">需要使用 `.HasNoKey()` 方法呼叫來設定。</span><span class="sxs-lookup"><span data-stu-id="4c6a3-119">Need to be configured with `.HasNoKey()` method call.</span></span>
- <span data-ttu-id="4c6a3-120">可能會對應到_定義查詢_。</span><span class="sxs-lookup"><span data-stu-id="4c6a3-120">May be mapped to a _defining query_.</span></span> <span data-ttu-id="4c6a3-121">定義查詢是在模型中宣告的查詢，做為無索引鍵實體類型的資料來源。</span><span class="sxs-lookup"><span data-stu-id="4c6a3-121">A defining query is a query declared in the model that acts as a data source for a keyless entity type.</span></span>

## <a name="usage-scenarios"></a><span data-ttu-id="4c6a3-122">使用案例</span><span class="sxs-lookup"><span data-stu-id="4c6a3-122">Usage scenarios</span></span>

<span data-ttu-id="4c6a3-123">無索引鍵實體類型的一些主要使用案例如下：</span><span class="sxs-lookup"><span data-stu-id="4c6a3-123">Some of the main usage scenarios for keyless entity types are:</span></span>

- <span data-ttu-id="4c6a3-124">做為[原始 SQL 查詢](xref:core/querying/raw-sql)的傳回型別。</span><span class="sxs-lookup"><span data-stu-id="4c6a3-124">Serving as the return type for [raw SQL queries](xref:core/querying/raw-sql).</span></span>
- <span data-ttu-id="4c6a3-125">對應至不含主鍵的資料庫 views。</span><span class="sxs-lookup"><span data-stu-id="4c6a3-125">Mapping to database views that do not contain a primary key.</span></span>
- <span data-ttu-id="4c6a3-126">對應至未定義主鍵的資料表。</span><span class="sxs-lookup"><span data-stu-id="4c6a3-126">Mapping to tables that do not have a primary key defined.</span></span>
- <span data-ttu-id="4c6a3-127">對應至模型中定義的查詢。</span><span class="sxs-lookup"><span data-stu-id="4c6a3-127">Mapping to queries defined in the model.</span></span>

## <a name="mapping-to-database-objects"></a><span data-ttu-id="4c6a3-128">對應至資料庫物件</span><span class="sxs-lookup"><span data-stu-id="4c6a3-128">Mapping to database objects</span></span>

<span data-ttu-id="4c6a3-129">將無索引鍵實體類型對應至資料庫物件是使用 `ToTable` 或 `ToView` Fluent API 來達成。</span><span class="sxs-lookup"><span data-stu-id="4c6a3-129">Mapping a keyless entity type to a database object is achieved using the `ToTable` or `ToView` fluent API.</span></span> <span data-ttu-id="4c6a3-130">從 EF Core 的觀點來看，這個方法中指定的資料庫物件是一個_視圖_，這表示它會被視為唯讀查詢來源，而且不能是 update、insert 或 delete 作業的目標。</span><span class="sxs-lookup"><span data-stu-id="4c6a3-130">From the perspective of EF Core, the database object specified in this method is a _view_, meaning that it is treated as a read-only query source and cannot be the target of update, insert or delete operations.</span></span> <span data-ttu-id="4c6a3-131">不過，這並不表示資料庫物件實際上必須是資料庫的視圖。</span><span class="sxs-lookup"><span data-stu-id="4c6a3-131">However, this does not mean that the database object is actually required to be a database view.</span></span> <span data-ttu-id="4c6a3-132">它也可以是將視為唯讀的資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="4c6a3-132">It can alternatively be a database table that will be treated as read-only.</span></span> <span data-ttu-id="4c6a3-133">相反地，針對一般實體類型，EF Core 會假設 `ToTable` 方法中指定的資料庫物件可以視為_資料表_，這表示它可以做為查詢來源，但也可做為更新、刪除和插入作業的目標。</span><span class="sxs-lookup"><span data-stu-id="4c6a3-133">Conversely, for regular entity types, EF Core assumes that a database object specified in the `ToTable` method can be treated as a _table_, meaning that it can be used as a query source but also targeted by update, delete and insert operations.</span></span> <span data-ttu-id="4c6a3-134">事實上，您可以在 `ToTable` 中指定資料庫的名稱，而且只要此視圖設定為可在資料庫上更新，一切都應該能正常執行。</span><span class="sxs-lookup"><span data-stu-id="4c6a3-134">In fact, you can specify the name of a database view in `ToTable` and everything should work fine as long as the view is configured to be updatable on the database.</span></span>

> [!NOTE]
> <span data-ttu-id="4c6a3-135">`ToView` 假設物件已經存在於資料庫中，而不是由遷移所建立。</span><span class="sxs-lookup"><span data-stu-id="4c6a3-135">`ToView` assumes that the object already exists in the database and it won't be created by migrations.</span></span>

## <a name="example"></a><span data-ttu-id="4c6a3-136">範例</span><span class="sxs-lookup"><span data-stu-id="4c6a3-136">Example</span></span>

<span data-ttu-id="4c6a3-137">下列範例顯示如何使用無索引鍵實體類型來查詢資料庫檢視。</span><span class="sxs-lookup"><span data-stu-id="4c6a3-137">The following example shows how to use keyless entity types to query a database view.</span></span>

> [!TIP]
> <span data-ttu-id="4c6a3-138">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="4c6a3-138">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) on GitHub.</span></span>

<span data-ttu-id="4c6a3-139">首先，我們會定義簡單的 Blog 和 Post 模型：</span><span class="sxs-lookup"><span data-stu-id="4c6a3-139">First, we define a simple Blog and Post model:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

<span data-ttu-id="4c6a3-140">接下來，我們會定義一個簡單的資料庫檢視，讓我們查詢與每個 blog 相關聯的貼文數目：</span><span class="sxs-lookup"><span data-stu-id="4c6a3-140">Next, we define a simple database view that will allow us to query the number of posts associated with each blog:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

<span data-ttu-id="4c6a3-141">接下來，我們會定義一個類別，以保存資料庫檢視的結果：</span><span class="sxs-lookup"><span data-stu-id="4c6a3-141">Next, we define a class to hold the result from the database view:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

<span data-ttu-id="4c6a3-142">接下來，我們會使用 `HasNoKey` API，在_OnModelCreating_中設定無索引鍵實體類型。</span><span class="sxs-lookup"><span data-stu-id="4c6a3-142">Next, we configure the keyless entity type in _OnModelCreating_ using the `HasNoKey` API.</span></span>
<span data-ttu-id="4c6a3-143">我們會使用流暢的設定 API 來設定無索引鍵實體類型的對應：</span><span class="sxs-lookup"><span data-stu-id="4c6a3-143">We use fluent configuration API to configure the mapping for the keyless entity type:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

<span data-ttu-id="4c6a3-144">接下來，我們會設定 `DbContext` 以包含 `DbSet<T>`：</span><span class="sxs-lookup"><span data-stu-id="4c6a3-144">Next, we configure the `DbContext` to include the `DbSet<T>`:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

<span data-ttu-id="4c6a3-145">最後，我們可以透過標準方式來查詢資料庫檢視：</span><span class="sxs-lookup"><span data-stu-id="4c6a3-145">Finally, we can query the database view in the standard way:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> <span data-ttu-id="4c6a3-146">請注意，我們也定義了內容層級查詢屬性（DbSet），以做為此類型之查詢的根。</span><span class="sxs-lookup"><span data-stu-id="4c6a3-146">Note we have also defined a context level query property (DbSet) to act as a root for queries against this type.</span></span>
