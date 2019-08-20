---
title: 查詢類型-EF Core
author: anpete
ms.date: 02/26/2018
ms.assetid: 9F4450C5-1A3F-4BB6-AC19-9FAC64292AAD
uid: core/modeling/query-types
ms.openlocfilehash: c023d442b0fa2728bd20694a55ebb3a7b5c0efd1
ms.sourcegitcommit: 87e72899d17602f7526d6ccd22f3c8ee844145df
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2019
ms.locfileid: "69628417"
---
# <a name="query-types"></a><span data-ttu-id="39e6d-102">查詢類型</span><span class="sxs-lookup"><span data-stu-id="39e6d-102">Query Types</span></span>
> [!NOTE]
> <span data-ttu-id="39e6d-103">這項功能的新 EF Core 2.1</span><span class="sxs-lookup"><span data-stu-id="39e6d-103">This feature is new in EF Core 2.1</span></span>

<span data-ttu-id="39e6d-104">EF Core 模型可以包含實體類型，除了_查詢類型_，可用來執行資料庫查詢不會對應到實體類型的資料。</span><span class="sxs-lookup"><span data-stu-id="39e6d-104">In addition to entity types, an EF Core model can contain _query types_, which can be used to carry out database queries against data that isn't mapped to entity types.</span></span>

## <a name="compare-query-types-to-entity-types"></a><span data-ttu-id="39e6d-105">比較查詢類型，與實體類型</span><span class="sxs-lookup"><span data-stu-id="39e6d-105">Compare query types to entity types</span></span>

<span data-ttu-id="39e6d-106">查詢類型如下所示的實體類型中，它們：</span><span class="sxs-lookup"><span data-stu-id="39e6d-106">Query types are like entity types in that they:</span></span>

- <span data-ttu-id="39e6d-107">可以是加入至模型中`OnModelCreating`或透過 「 設定 」 上的屬性衍生_DbContext_。</span><span class="sxs-lookup"><span data-stu-id="39e6d-107">Can be added to the model either in `OnModelCreating` or via a "set" property on a derived _DbContext_.</span></span>
- <span data-ttu-id="39e6d-108">支援許多相同的對應功能，例如繼承對應和導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="39e6d-108">Support many of the same mapping capabilities, like inheritance mapping and navigation properties.</span></span> <span data-ttu-id="39e6d-109">在關聯式存放區，他們可以設定的目標資料庫物件和透過 fluent API 方法或資料註解的資料行。</span><span class="sxs-lookup"><span data-stu-id="39e6d-109">On relational stores, they can configure the target database objects and columns via fluent API methods or data annotations.</span></span>

<span data-ttu-id="39e6d-110">不過，它們是不同於實體中的型別，它們：</span><span class="sxs-lookup"><span data-stu-id="39e6d-110">However, they are different from entity types in that they:</span></span>

- <span data-ttu-id="39e6d-111">不需要定義的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="39e6d-111">Do not require a key to be defined.</span></span>
- <span data-ttu-id="39e6d-112">會永遠不會追蹤變更_DbContext_ ，因此會永遠不會插入、 更新或刪除資料庫。</span><span class="sxs-lookup"><span data-stu-id="39e6d-112">Are never tracked for changes on the _DbContext_ and therefore are never inserted, updated or deleted on the database.</span></span>
- <span data-ttu-id="39e6d-113">永遠不會探索到的慣例。</span><span class="sxs-lookup"><span data-stu-id="39e6d-113">Are never discovered by convention.</span></span>
- <span data-ttu-id="39e6d-114">只有特別支援瀏覽對應功能的子集：</span><span class="sxs-lookup"><span data-stu-id="39e6d-114">Only support a subset of navigation mapping capabilities - Specifically:</span></span>
  - <span data-ttu-id="39e6d-115">它們可能永遠不會做為關聯性的主要末端。</span><span class="sxs-lookup"><span data-stu-id="39e6d-115">They may never act as the principal end of a relationship.</span></span>
  - <span data-ttu-id="39e6d-116">它們只可以包含指向實體的參考導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="39e6d-116">They can only contain reference navigation properties pointing to entities.</span></span>
  - <span data-ttu-id="39e6d-117">實體不能包含查詢類型的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="39e6d-117">Entities cannot contain navigation properties to query types.</span></span>
- <span data-ttu-id="39e6d-118">都能解決_ModelBuilder_使用`Query`方法而非`Entity`方法。</span><span class="sxs-lookup"><span data-stu-id="39e6d-118">Are addressed on the _ModelBuilder_ using the `Query` method rather than the `Entity` method.</span></span>
- <span data-ttu-id="39e6d-119">在對應_DbContext_透過屬性的型別`DbQuery<T>`而非 `DbSet<T>`</span><span class="sxs-lookup"><span data-stu-id="39e6d-119">Are mapped on the _DbContext_ through properties of type `DbQuery<T>` rather than `DbSet<T>`</span></span>
- <span data-ttu-id="39e6d-120">會對應至使用的資料庫物件`ToView`方法，而非`ToTable`。</span><span class="sxs-lookup"><span data-stu-id="39e6d-120">Are mapped to database objects using the `ToView` method, rather than `ToTable`.</span></span>
- <span data-ttu-id="39e6d-121">可對應到_定義查詢_-定義查詢是次要的查詢，可做為查詢類型的資料來源模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="39e6d-121">May be mapped to a _defining query_ - A defining query is a secondary query declared in the model that acts a data source for a query type.</span></span>

## <a name="usage-scenarios"></a><span data-ttu-id="39e6d-122">使用案例</span><span class="sxs-lookup"><span data-stu-id="39e6d-122">Usage scenarios</span></span>

<span data-ttu-id="39e6d-123">查詢類型的主要使用案例包括：</span><span class="sxs-lookup"><span data-stu-id="39e6d-123">Some of the main usage scenarios for query types are:</span></span>

- <span data-ttu-id="39e6d-124">臨機操作做為傳回型別`FromSql()`查詢。</span><span class="sxs-lookup"><span data-stu-id="39e6d-124">Serving as the return type for ad hoc `FromSql()` queries.</span></span>
- <span data-ttu-id="39e6d-125">將對應至資料庫檢視。</span><span class="sxs-lookup"><span data-stu-id="39e6d-125">Mapping to database views.</span></span>
- <span data-ttu-id="39e6d-126">沒有定義主索引鍵的資料表的對應。</span><span class="sxs-lookup"><span data-stu-id="39e6d-126">Mapping to tables that do not have a primary key defined.</span></span>
- <span data-ttu-id="39e6d-127">對應至模型中定義的查詢。</span><span class="sxs-lookup"><span data-stu-id="39e6d-127">Mapping to queries defined in the model.</span></span>

## <a name="mapping-to-database-objects"></a><span data-ttu-id="39e6d-128">對應至資料庫物件</span><span class="sxs-lookup"><span data-stu-id="39e6d-128">Mapping to database objects</span></span>

<span data-ttu-id="39e6d-129">對應至資料庫物件的查詢類型用來達成`ToView`fluent API。</span><span class="sxs-lookup"><span data-stu-id="39e6d-129">Mapping a query type to a database object is achieved using the `ToView` fluent API.</span></span> <span data-ttu-id="39e6d-130">EF Core 的觀點而言，此方法中指定的資料庫物件是_檢視_，這表示它會被視為唯讀查詢來源而且無法更新目標的、 插入或刪除作業。</span><span class="sxs-lookup"><span data-stu-id="39e6d-130">From the perspective of EF Core, the database object specified in this method is a _view_, meaning that it is treated as a read-only query source and cannot be the target of update, insert or delete operations.</span></span> <span data-ttu-id="39e6d-131">不過，這不表示資料庫物件實際上需要為資料庫檢視，-它也可以將視為唯讀的資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="39e6d-131">However, this does not mean that the database object is actually required to be a database view - It can alternatively be a database table that will be treated as read-only.</span></span> <span data-ttu-id="39e6d-132">相反地，實體類型 EF Core 假設中指定的資料庫物件`ToTable`方法可用來當做_資料表_，這表示它可以當做查詢來源，但是也鎖定更新刪除和插入作業。</span><span class="sxs-lookup"><span data-stu-id="39e6d-132">Conversely, for entity types, EF Core assumes that a database object specified in the `ToTable` method can be treated as a _table_, meaning that it can be used as a query source but also targeted by update, delete and insert operations.</span></span> <span data-ttu-id="39e6d-133">事實上，您可以在其中指定的名稱中的資料庫檢視`ToTable`和所有項目應可正常運作，只要檢視已成為可更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="39e6d-133">In fact, you can specify the name of a database view in `ToTable` and everything should work fine as long as the view is configured to be updatable on the database.</span></span>

## <a name="example"></a><span data-ttu-id="39e6d-134">範例</span><span class="sxs-lookup"><span data-stu-id="39e6d-134">Example</span></span>

<span data-ttu-id="39e6d-135">下列範例示範如何使用查詢型別來查詢資料庫檢視。</span><span class="sxs-lookup"><span data-stu-id="39e6d-135">The following example shows how to use Query Type to query a database view.</span></span>

> [!TIP]
> <span data-ttu-id="39e6d-136">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/QueryTypes) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="39e6d-136">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/QueryTypes) on GitHub.</span></span>

<span data-ttu-id="39e6d-137">首先，我們會定義一個簡單的部落格和後置模型：</span><span class="sxs-lookup"><span data-stu-id="39e6d-137">First, we define a simple Blog and Post model:</span></span>

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#Entities)]

<span data-ttu-id="39e6d-138">接下來，我們會定義簡單的資料庫檢視，讓我們查詢每個部落格相關聯的貼文數目：</span><span class="sxs-lookup"><span data-stu-id="39e6d-138">Next, we define a simple database view that will allow us to query the number of posts associated with each blog:</span></span>

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#View)]

<span data-ttu-id="39e6d-139">接下來，我們會定義類別以包裝資料庫檢視的結果：</span><span class="sxs-lookup"><span data-stu-id="39e6d-139">Next, we define a class to hold the result from the database view:</span></span>

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#QueryType)]

<span data-ttu-id="39e6d-140">接下來，我們設定中的查詢類型_OnModelCreating_使用`modelBuilder.Query<T>`API。</span><span class="sxs-lookup"><span data-stu-id="39e6d-140">Next, we configure the query type in _OnModelCreating_ using the `modelBuilder.Query<T>` API.</span></span>
<span data-ttu-id="39e6d-141">我們可以使用標準的 fluent 組態 Api 來設定查詢類型的對應：</span><span class="sxs-lookup"><span data-stu-id="39e6d-141">We use standard fluent configuration APIs to configure the mapping for the Query Type:</span></span>

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#Configuration)]

<span data-ttu-id="39e6d-142">接下來, 我們會`DbContext`設定, 以`DbQuery<T>`包含:</span><span class="sxs-lookup"><span data-stu-id="39e6d-142">Next, we configure the `DbContext` to include the `DbQuery<T>`:</span></span>

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#DbQuery)]

<span data-ttu-id="39e6d-143">最後，我們可以查詢資料庫檢視，以標準方式：</span><span class="sxs-lookup"><span data-stu-id="39e6d-143">Finally, we can query the database view in the standard way:</span></span>

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#Query)]

> [!TIP]
> <span data-ttu-id="39e6d-144">請注意，我們也已定義的內容層級查詢屬性 (DbQuery) 做為查詢對這種類型的根。</span><span class="sxs-lookup"><span data-stu-id="39e6d-144">Note we have also defined a context level query property (DbQuery) to act as a root for queries against this type.</span></span>
