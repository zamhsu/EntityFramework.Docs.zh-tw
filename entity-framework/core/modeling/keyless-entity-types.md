---
title: 無索引鍵實體類型-EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 02/26/2018
ms.assetid: 9F4450C5-1A3F-4BB6-AC19-9FAC64292AAD
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: b968ac9602b9aa1f1c1e3181b6b76a64394d70f0
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/20/2019
ms.locfileid: "71150840"
---
# <a name="keyless-entity-types"></a><span data-ttu-id="b2b09-102">無索引鍵實體類型</span><span class="sxs-lookup"><span data-stu-id="b2b09-102">Keyless Entity Types</span></span>
> [!NOTE]
> <span data-ttu-id="b2b09-103">此功能是 EF Core 2.1 中的新功能。</span><span class="sxs-lookup"><span data-stu-id="b2b09-103">This feature is new in EF Core 2.1.</span></span> <span data-ttu-id="b2b09-104">在3.0 之前，它們稱為查詢類型</span><span class="sxs-lookup"><span data-stu-id="b2b09-104">Prior to 3.0 they were known as Query Types</span></span>

<span data-ttu-id="b2b09-105">除了一般實體類型之外，EF Core 模型也可以包含_無索引鍵的實體類型_，可用來對不包含索引鍵值的資料執行資料庫查詢。</span><span class="sxs-lookup"><span data-stu-id="b2b09-105">In addition to regular entity types, an EF Core model can contain _keyless entity types_, which can be used to carry out database queries against data that doesn't contain key values.</span></span>

## <a name="keyless-entity-types-characteristics"></a><span data-ttu-id="b2b09-106">無索引鍵實體類型特性</span><span class="sxs-lookup"><span data-stu-id="b2b09-106">Keyless entity types characteristics</span></span>

<span data-ttu-id="b2b09-107">無索引鍵實體類型支援許多與一般實體類型相同的對應功能，例如繼承對應和導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="b2b09-107">Keyless entity types support many of the same mapping capabilities as regular entity types, like inheritance mapping and navigation properties.</span></span> <span data-ttu-id="b2b09-108">在關聯式存放區，他們可以設定的目標資料庫物件和透過 fluent API 方法或資料註解的資料行。</span><span class="sxs-lookup"><span data-stu-id="b2b09-108">On relational stores, they can configure the target database objects and columns via fluent API methods or data annotations.</span></span>

<span data-ttu-id="b2b09-109">不過，它們與一般實體類型不同之處如下：</span><span class="sxs-lookup"><span data-stu-id="b2b09-109">However, they are different from regular entity types in that they:</span></span>

- <span data-ttu-id="b2b09-110">不能定義索引鍵。</span><span class="sxs-lookup"><span data-stu-id="b2b09-110">Cannot have a key defined.</span></span>
- <span data-ttu-id="b2b09-111">永遠不會針對_DbCoNtext_中的變更進行追蹤，因此永遠不會在資料庫上插入、更新或刪除。</span><span class="sxs-lookup"><span data-stu-id="b2b09-111">Are never tracked for changes in the _DbContext_ and therefore are never inserted, updated or deleted on the database.</span></span>
- <span data-ttu-id="b2b09-112">永遠不會探索到的慣例。</span><span class="sxs-lookup"><span data-stu-id="b2b09-112">Are never discovered by convention.</span></span>
- <span data-ttu-id="b2b09-113">僅支援導覽對應功能的子集，特別是：</span><span class="sxs-lookup"><span data-stu-id="b2b09-113">Only support a subset of navigation mapping capabilities, specifically:</span></span>
  - <span data-ttu-id="b2b09-114">它們可能永遠不會做為關聯性的主要末端。</span><span class="sxs-lookup"><span data-stu-id="b2b09-114">They may never act as the principal end of a relationship.</span></span>
  - <span data-ttu-id="b2b09-115">他們可能不會有所擁有實體的導覽</span><span class="sxs-lookup"><span data-stu-id="b2b09-115">They may not have navigations to owned entities</span></span>
  - <span data-ttu-id="b2b09-116">它們只能包含指向一般實體的參考導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="b2b09-116">They can only contain reference navigation properties pointing to regular entities.</span></span>
  - <span data-ttu-id="b2b09-117">實體不能包含無索引鍵實體類型的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="b2b09-117">Entities cannot contain navigation properties to keyless entity types.</span></span>
- <span data-ttu-id="b2b09-118">需要使用`.HasNoKey()`方法呼叫來設定。</span><span class="sxs-lookup"><span data-stu-id="b2b09-118">Need to be configured with `.HasNoKey()` method call.</span></span>
- <span data-ttu-id="b2b09-119">可能會對應到_定義查詢_。</span><span class="sxs-lookup"><span data-stu-id="b2b09-119">May be mapped to a _defining query_.</span></span> <span data-ttu-id="b2b09-120">定義查詢是在模型中宣告的查詢，做為無索引鍵實體類型的資料來源。</span><span class="sxs-lookup"><span data-stu-id="b2b09-120">A defining query is a query declared in the model that acts as a data source for a keyless entity type.</span></span>

## <a name="usage-scenarios"></a><span data-ttu-id="b2b09-121">使用案例</span><span class="sxs-lookup"><span data-stu-id="b2b09-121">Usage scenarios</span></span>

<span data-ttu-id="b2b09-122">無索引鍵實體類型的一些主要使用案例如下：</span><span class="sxs-lookup"><span data-stu-id="b2b09-122">Some of the main usage scenarios for keyless entity types are:</span></span>

- <span data-ttu-id="b2b09-123">做為[原始 SQL 查詢](xref:core/querying/raw-sql)的傳回型別。</span><span class="sxs-lookup"><span data-stu-id="b2b09-123">Serving as the return type for [raw SQL queries](xref:core/querying/raw-sql).</span></span>
- <span data-ttu-id="b2b09-124">對應至不含主鍵的資料庫 views。</span><span class="sxs-lookup"><span data-stu-id="b2b09-124">Mapping to database views that do not contain a primary key.</span></span>
- <span data-ttu-id="b2b09-125">沒有定義主索引鍵的資料表的對應。</span><span class="sxs-lookup"><span data-stu-id="b2b09-125">Mapping to tables that do not have a primary key defined.</span></span>
- <span data-ttu-id="b2b09-126">對應至模型中定義的查詢。</span><span class="sxs-lookup"><span data-stu-id="b2b09-126">Mapping to queries defined in the model.</span></span>

## <a name="mapping-to-database-objects"></a><span data-ttu-id="b2b09-127">對應至資料庫物件</span><span class="sxs-lookup"><span data-stu-id="b2b09-127">Mapping to database objects</span></span>

<span data-ttu-id="b2b09-128">使用`ToTable` 或`ToView` Fluent API，將無索引鍵實體類型對應至資料庫物件。</span><span class="sxs-lookup"><span data-stu-id="b2b09-128">Mapping a keyless entity type to a database object is achieved using the `ToTable` or `ToView` fluent API.</span></span> <span data-ttu-id="b2b09-129">EF Core 的觀點而言，此方法中指定的資料庫物件是_檢視_，這表示它會被視為唯讀查詢來源而且無法更新目標的、 插入或刪除作業。</span><span class="sxs-lookup"><span data-stu-id="b2b09-129">From the perspective of EF Core, the database object specified in this method is a _view_, meaning that it is treated as a read-only query source and cannot be the target of update, insert or delete operations.</span></span> <span data-ttu-id="b2b09-130">不過，這並不表示資料庫物件實際上必須是資料庫的視圖。</span><span class="sxs-lookup"><span data-stu-id="b2b09-130">However, this does not mean that the database object is actually required to be a database view.</span></span> <span data-ttu-id="b2b09-131">它也可以是將視為唯讀的資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="b2b09-131">It can alternatively be a database table that will be treated as read-only.</span></span> <span data-ttu-id="b2b09-132">相反地，針對一般實體類型，EF Core 會假設`ToTable`方法中指定的資料庫物件可以視為_資料表_，這表示它可以當做查詢來源使用，但也會以更新、刪除和插入作業為目標。</span><span class="sxs-lookup"><span data-stu-id="b2b09-132">Conversely, for regular entity types, EF Core assumes that a database object specified in the `ToTable` method can be treated as a _table_, meaning that it can be used as a query source but also targeted by update, delete and insert operations.</span></span> <span data-ttu-id="b2b09-133">事實上，您可以在其中指定的名稱中的資料庫檢視`ToTable`和所有項目應可正常運作，只要檢視已成為可更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="b2b09-133">In fact, you can specify the name of a database view in `ToTable` and everything should work fine as long as the view is configured to be updatable on the database.</span></span>

> [!NOTE]
> <span data-ttu-id="b2b09-134">`ToView`假設物件已經存在於資料庫中，而且不會由遷移所建立。</span><span class="sxs-lookup"><span data-stu-id="b2b09-134">`ToView` assumes that the object already exists in the database and it won't be created by migrations.</span></span>

## <a name="example"></a><span data-ttu-id="b2b09-135">範例</span><span class="sxs-lookup"><span data-stu-id="b2b09-135">Example</span></span>

<span data-ttu-id="b2b09-136">下列範例顯示如何使用無索引鍵實體類型來查詢資料庫檢視。</span><span class="sxs-lookup"><span data-stu-id="b2b09-136">The following example shows how to use keyless entity types to query a database view.</span></span>

> [!TIP]
> <span data-ttu-id="b2b09-137">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/QueryTypes) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="b2b09-137">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/QueryTypes) on GitHub.</span></span>

<span data-ttu-id="b2b09-138">首先，我們會定義一個簡單的部落格和後置模型：</span><span class="sxs-lookup"><span data-stu-id="b2b09-138">First, we define a simple Blog and Post model:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

<span data-ttu-id="b2b09-139">接下來，我們會定義簡單的資料庫檢視，讓我們查詢每個部落格相關聯的貼文數目：</span><span class="sxs-lookup"><span data-stu-id="b2b09-139">Next, we define a simple database view that will allow us to query the number of posts associated with each blog:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

<span data-ttu-id="b2b09-140">接下來，我們會定義類別以包裝資料庫檢視的結果：</span><span class="sxs-lookup"><span data-stu-id="b2b09-140">Next, we define a class to hold the result from the database view:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

<span data-ttu-id="b2b09-141">接下來，我們會使用`HasNoKey` API，在_OnModelCreating_中設定無索引鍵實體類型。</span><span class="sxs-lookup"><span data-stu-id="b2b09-141">Next, we configure the keyless entity type in _OnModelCreating_ using the `HasNoKey` API.</span></span>
<span data-ttu-id="b2b09-142">我們會使用流暢的設定 API 來設定無索引鍵實體類型的對應：</span><span class="sxs-lookup"><span data-stu-id="b2b09-142">We use fluent configuration API to configure the mapping for the keyless entity type:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

<span data-ttu-id="b2b09-143">接下來，我們會`DbContext`設定，以`DbSet<T>`包含：</span><span class="sxs-lookup"><span data-stu-id="b2b09-143">Next, we configure the `DbContext` to include the `DbSet<T>`:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

<span data-ttu-id="b2b09-144">最後，我們可以查詢資料庫檢視，以標準方式：</span><span class="sxs-lookup"><span data-stu-id="b2b09-144">Finally, we can query the database view in the standard way:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> <span data-ttu-id="b2b09-145">請注意，我們也定義了內容層級查詢屬性（DbSet），以做為此類型之查詢的根。</span><span class="sxs-lookup"><span data-stu-id="b2b09-145">Note we have also defined a context level query property (DbSet) to act as a root for queries against this type.</span></span>
