---
title: 查詢類型-EF 核心
author: anpete
ms.author: anpete
ms.date: 2/26/2018
ms.assetid: 9F4450C5-1A3F-4BB6-AC19-9FAC64292AAD
ms.technology: entity-framework-core
uid: core/modeling/query-types
ms.openlocfilehash: 4e02f106e086d243b23a60c02838f32555be210e
ms.sourcegitcommit: 26f33758c47399ae933f22fec8e1d19fa7d2c0b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/19/2018
---
# <a name="query-types"></a><span data-ttu-id="347ee-102">查詢類型</span><span class="sxs-lookup"><span data-stu-id="347ee-102">Query Types</span></span>
> [!NOTE]
> <span data-ttu-id="347ee-103">這項功能的新 EF 核心 2.1</span><span class="sxs-lookup"><span data-stu-id="347ee-103">This feature is new in EF Core 2.1</span></span>

<span data-ttu-id="347ee-104">EF 核心模型可以包含實體類型，除了_查詢類型_，可用來執行資料庫查詢不會對應到實體類型的資料。</span><span class="sxs-lookup"><span data-stu-id="347ee-104">In addition to entity types, an EF Core model can contain _query types_, which can be used to carry out database queries against data that isn't mapped to entity types.</span></span>

<span data-ttu-id="347ee-105">查詢類型包含實體類型有許多相似之處：</span><span class="sxs-lookup"><span data-stu-id="347ee-105">Query types have many similarities with entity types:</span></span>

- <span data-ttu-id="347ee-106">它們也可以加入至模型可能是在`OnModelCreating`，或透過 「 設定 」 上的屬性衍生_DbContext_。</span><span class="sxs-lookup"><span data-stu-id="347ee-106">They can also be added to the model either in `OnModelCreating`, or via a "set" property on a derived _DbContext_.</span></span>
- <span data-ttu-id="347ee-107">它們支援許多相同的對應功能，像是繼承對應時，導覽屬性 （請參閱下面的限制），然後在關聯式存放區，讓您設定的目標資料庫物件和透過 fluent API 方法或資料註解的資料行。</span><span class="sxs-lookup"><span data-stu-id="347ee-107">They support many of the same mapping capabilities, like inheritance mapping, navigation properties (see limitations below) and, on relational stores, the ability to configure the target database objects and columns via fluent API methods or data annotations.</span></span>

<span data-ttu-id="347ee-108">不過它們是不同的實體中的類型，它們：</span><span class="sxs-lookup"><span data-stu-id="347ee-108">However they are different from entity types in that they:</span></span>

- <span data-ttu-id="347ee-109">不需要定義的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="347ee-109">Do not require a key to be defined.</span></span>
- <span data-ttu-id="347ee-110">永遠不會追蹤變更_DbContext_ ，因此會永遠不會插入、 更新或刪除資料庫上。</span><span class="sxs-lookup"><span data-stu-id="347ee-110">Are never tracked for changes on the _DbContext_ and therefore are never inserted, updated or deleted on the database.</span></span>
- <span data-ttu-id="347ee-111">永遠不會探索到的慣例。</span><span class="sxs-lookup"><span data-stu-id="347ee-111">Are never discovered by convention.</span></span>
- <span data-ttu-id="347ee-112">只支援的瀏覽對應功能的子集具體而言，它們可能永遠不會做為關聯性的主要端點。</span><span class="sxs-lookup"><span data-stu-id="347ee-112">Only support a subset of navigation mapping capabilities - Specifically, they may never act as the principal end of a relationship.</span></span>
- <span data-ttu-id="347ee-113">都能解決_ModelBuilder_使用`Query`方法而非`Entity`方法。</span><span class="sxs-lookup"><span data-stu-id="347ee-113">Are addressed on the _ModelBuilder_ using the `Query` method rather than the `Entity` method.</span></span>
- <span data-ttu-id="347ee-114">對應索引_DbContext_透過屬性的型別`DbQuery<T>`而非 `DbSet<T>`</span><span class="sxs-lookup"><span data-stu-id="347ee-114">Are mapped on the _DbContext_ through properties of type `DbQuery<T>` rather than `DbSet<T>`</span></span>
- <span data-ttu-id="347ee-115">會對應至資料庫物件使用`ToView`方法，而非`ToTable`。</span><span class="sxs-lookup"><span data-stu-id="347ee-115">Are mapped to database objects using the `ToView` method, rather than `ToTable`.</span></span>
- <span data-ttu-id="347ee-116">會對應至_定義查詢_-定義查詢是做為查詢類型的資料來源在模型中宣告的第二個查詢。</span><span class="sxs-lookup"><span data-stu-id="347ee-116">May be mapped to a _defining query_ - A defining query is a secondary query declared in the model that acts a data source for a query type.</span></span>

<span data-ttu-id="347ee-117">主要的使用方式案例的查詢類型的部分包括：</span><span class="sxs-lookup"><span data-stu-id="347ee-117">Some of the main usage scenarios for query types are:</span></span>

- <span data-ttu-id="347ee-118">臨機操作做為傳回型別`FromSql()`查詢。</span><span class="sxs-lookup"><span data-stu-id="347ee-118">Serving as the return type for ad hoc `FromSql()` queries.</span></span>
- <span data-ttu-id="347ee-119">將對應至資料庫檢視。</span><span class="sxs-lookup"><span data-stu-id="347ee-119">Mapping to database views.</span></span>
- <span data-ttu-id="347ee-120">對應至沒有定義主索引鍵的資料表。</span><span class="sxs-lookup"><span data-stu-id="347ee-120">Mapping to tables that do not have a primary key defined.</span></span>
- <span data-ttu-id="347ee-121">對應至模型中定義的查詢。</span><span class="sxs-lookup"><span data-stu-id="347ee-121">Mapping to queries defined in the model.</span></span>

> [!TIP]
> <span data-ttu-id="347ee-122">對應至資料庫物件的查詢類型用來達成`ToView`fluent 應用程式開發的應用程式開發介面。</span><span class="sxs-lookup"><span data-stu-id="347ee-122">Mapping a query type to a database object is achieved using the `ToView` fluent API.</span></span> <span data-ttu-id="347ee-123">EF 核心的觀點而言，此方法中指定的資料庫物件是_檢視_，這表示它會被視為唯讀查詢來源而且無法更新目標的、 插入或刪除作業。</span><span class="sxs-lookup"><span data-stu-id="347ee-123">From the perspective of EF Core, the database object specified in this method is a _view_, meaning that it is treated as a read-only query source and cannot be the target of update, insert or delete operations.</span></span> <span data-ttu-id="347ee-124">不過，這不表示資料庫物件的資料庫檢視實際需要-它也可以將被視為唯讀的資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="347ee-124">However, this does not mean that the database object is actually required to be a database view - It can alternatively be a database table that will be treated as read-only.</span></span> <span data-ttu-id="347ee-125">相反地，實體類型 EF 核心假設中指定的資料庫物件`ToTable`方法可用來當做_資料表_，這表示它可以當做查詢來源，但是也鎖定更新刪除和插入作業。</span><span class="sxs-lookup"><span data-stu-id="347ee-125">Conversely, for entity types, EF Core assumes that a database object specified in the `ToTable` method can be treated as a _table_, meaning that it can be used as a query source but also targeted by update, delete and insert operations.</span></span> <span data-ttu-id="347ee-126">事實上，您可以指定資料庫檢視中的名稱`ToTable`和所有項目應該會正常運作，只要在檢視器設定為可在資料庫上更新。</span><span class="sxs-lookup"><span data-stu-id="347ee-126">In fact, you can specify the name of a database view in `ToTable` and everything should work fine as long as the view is configured to be updatable on the database.</span></span>

## <a name="example"></a><span data-ttu-id="347ee-127">範例</span><span class="sxs-lookup"><span data-stu-id="347ee-127">Example</span></span>

<span data-ttu-id="347ee-128">下列範例會示範如何使用查詢型別來查詢資料庫檢視。</span><span class="sxs-lookup"><span data-stu-id="347ee-128">The following example shows how to use Query Type to query a database view.</span></span>

> [!TIP]
> <span data-ttu-id="347ee-129">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFrameworkCore/tree/dev/samples/QueryTypes) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="347ee-129">You can view this article's [sample](https://github.com/aspnet/EntityFrameworkCore/tree/dev/samples/QueryTypes) on GitHub.</span></span>

<span data-ttu-id="347ee-130">首先，我們會定義簡單的部落格和後置模型：</span><span class="sxs-lookup"><span data-stu-id="347ee-130">First, we define a simple Blog and Post model:</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#Entities)]

<span data-ttu-id="347ee-131">接下來，我們會定義簡單的資料庫檢視，可讓我們查詢的每個部落格相關聯的文章編號：</span><span class="sxs-lookup"><span data-stu-id="347ee-131">Next, we define a simple database view that will allow us to query the number of posts associated with each blog:</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#View)]

<span data-ttu-id="347ee-132">接下來，我們會定義以保存從資料庫檢視結果的類別：</span><span class="sxs-lookup"><span data-stu-id="347ee-132">Next, we define a class to hold the result from the database view:</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#QueryType)]

<span data-ttu-id="347ee-133">接下來，我們設定中的查詢類型_OnModelCreating_使用`modelBuilder.Query<T>`應用程式開發介面。</span><span class="sxs-lookup"><span data-stu-id="347ee-133">Next, we configure the query type in _OnModelCreating_ using the `modelBuilder.Query<T>` API.</span></span>
<span data-ttu-id="347ee-134">我們可以使用標準 fluent 應用程式開發設定應用程式開發介面來設定查詢類型的對應：</span><span class="sxs-lookup"><span data-stu-id="347ee-134">We use standard fluent configuration APIs to configure the mapping for the Query Type:</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#Configuration)]

<span data-ttu-id="347ee-135">最後，我們可以查詢資料庫檢視，以標準方式：</span><span class="sxs-lookup"><span data-stu-id="347ee-135">Finally, we can query the database view in the standard way:</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#Query)]

> [!TIP]
> <span data-ttu-id="347ee-136">請注意，我們也已定義的內容層級查詢屬性 (DbQuery) 做為查詢對這種類型的根。</span><span class="sxs-lookup"><span data-stu-id="347ee-136">Note we have also defined a context level query property (DbQuery) to act as a root for queries against this type.</span></span>
