---
title: "查詢類型-EF 核心"
author: anpete
ms.author: anpete
ms.date: 2/26/2018
ms.assetid: 9F4450C5-1A3F-4BB6-AC19-9FAC64292AAD
ms.technology: entity-framework-core
uid: core/modeling/query-types
ms.openlocfilehash: 19a371c65da33e8209cc1ab3423a67c34ddae61e
ms.sourcegitcommit: fc68321c211aca38f7b9dc3a75677c6ca1b2524b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2018
---
# <a name="query-types"></a><span data-ttu-id="acf9c-102">查詢類型</span><span class="sxs-lookup"><span data-stu-id="acf9c-102">Query Types</span></span>
> [!NOTE]
> <span data-ttu-id="acf9c-103">這項功能的新 EF 核心 2.1</span><span class="sxs-lookup"><span data-stu-id="acf9c-103">This feature is new in EF Core 2.1</span></span>

<span data-ttu-id="acf9c-104">查詢類型都是可以加入至 EF 核心模型的唯讀查詢結果類型。</span><span class="sxs-lookup"><span data-stu-id="acf9c-104">Query Types are read-only query result types that can be added to the EF Core model.</span></span> <span data-ttu-id="acf9c-105">查詢類型啟用臨機操作查詢 （例如匿名型別），但不更有彈性，因為它們可以指定的對應設定。</span><span class="sxs-lookup"><span data-stu-id="acf9c-105">Query Types enable ad-hoc querying (like anonymous types), but are more flexible because they can have mapping configuration specified.</span></span>

<span data-ttu-id="acf9c-106">在概念上類似於實體類型，包括：</span><span class="sxs-lookup"><span data-stu-id="acf9c-106">They are conceptually similar to Entity Types in that:</span></span>

- <span data-ttu-id="acf9c-107">它們都是 POCO C# 類型加入至模型中，有兩種```OnModelCreating```使用```ModelBuilder.Query```方法，或透過 DbContext 「 組 」 屬性 (如查詢類型這類屬性型別為```DbQuery<T>```而```DbSet<T>```)。</span><span class="sxs-lookup"><span data-stu-id="acf9c-107">They are POCO C# types that are added to the model, either in ```OnModelCreating``` using the ```ModelBuilder.Query``` method, or via a DbContext "set" property (for query types such a property is typed as ```DbQuery<T>``` rather that ```DbSet<T>```).</span></span>
- <span data-ttu-id="acf9c-108">它們支援的大部分相同的對應功能，做為一般實體型別。</span><span class="sxs-lookup"><span data-stu-id="acf9c-108">They support much of the same mapping capabilities as regular entity types.</span></span> <span data-ttu-id="acf9c-109">例如繼承對應、 巡覽 （請參閱下面的 limitiations），然後在關聯式存放區，讓您設定目標資料庫結構描述物件，透過```ToTable```， ```HasColumn``` fluent api 方法 （或資料註解）。</span><span class="sxs-lookup"><span data-stu-id="acf9c-109">For example, inheritance mapping, navigations (see limitiations below) and, on relational stores, the ability to configure the target database schema objects via ```ToTable```, ```HasColumn``` fluent-api methods (or data annotations).</span></span>

<span data-ttu-id="acf9c-110">查詢類型會與不同的實體中的類型，它們：</span><span class="sxs-lookup"><span data-stu-id="acf9c-110">Query Types are different from entity types in that they:</span></span>

- <span data-ttu-id="acf9c-111">不需要定義的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="acf9c-111">Do not require a key to be defined.</span></span>
- <span data-ttu-id="acf9c-112">永遠不會追蹤變更追蹤程式。</span><span class="sxs-lookup"><span data-stu-id="acf9c-112">Are never tracked by the Change Tracker.</span></span>
- <span data-ttu-id="acf9c-113">永遠不會探索到的慣例。</span><span class="sxs-lookup"><span data-stu-id="acf9c-113">Are never discovered by convention.</span></span>
- <span data-ttu-id="acf9c-114">只支援的瀏覽對應功能的子集具體而言，它們可能永遠不會做為關聯性的主要端點。</span><span class="sxs-lookup"><span data-stu-id="acf9c-114">Only support a subset of navigation mapping capabilities - Specifically, they may never act as the principal end of a relationship.</span></span>
- <span data-ttu-id="acf9c-115">會對應至_定義查詢_-定義查詢是次要的查詢，做為資料來源的查詢類型。</span><span class="sxs-lookup"><span data-stu-id="acf9c-115">May be mapped to a _defining query_ - A Defining Query is a secondary query that acts a data source for a Query Type.</span></span>

<span data-ttu-id="acf9c-116">主要的使用方式案例的查詢類型的部分包括：</span><span class="sxs-lookup"><span data-stu-id="acf9c-116">Some of the main usage scenarios for query types are:</span></span>

- <span data-ttu-id="acf9c-117">將對應至資料庫檢視。</span><span class="sxs-lookup"><span data-stu-id="acf9c-117">Mapping to database views.</span></span>
- <span data-ttu-id="acf9c-118">對應至沒有定義主索引鍵的資料表。</span><span class="sxs-lookup"><span data-stu-id="acf9c-118">Mapping to tables that do not have a primary key defined.</span></span>
- <span data-ttu-id="acf9c-119">臨機操作做為傳回型別```FromSql()```查詢。</span><span class="sxs-lookup"><span data-stu-id="acf9c-119">Serving as the return type for ad hoc ```FromSql()``` queries.</span></span>
- <span data-ttu-id="acf9c-120">對應至模型中定義的查詢。</span><span class="sxs-lookup"><span data-stu-id="acf9c-120">Mapping to queries defined in the model.</span></span>

> [!TIP]
> <span data-ttu-id="acf9c-121">對應到資料庫檢視的查詢類型用來達成```ToTable```fluent 應用程式開發的應用程式開發介面。</span><span class="sxs-lookup"><span data-stu-id="acf9c-121">Mapping a query type to a database view is achieved using the ```ToTable``` fluent API.</span></span>

## <a name="example"></a><span data-ttu-id="acf9c-122">範例</span><span class="sxs-lookup"><span data-stu-id="acf9c-122">Example</span></span>

<span data-ttu-id="acf9c-123">下列範例會示範如何使用查詢型別來查詢資料庫檢視。</span><span class="sxs-lookup"><span data-stu-id="acf9c-123">The following example shows how to use Query Type to query a database view.</span></span>

> [!TIP]
> <span data-ttu-id="acf9c-124">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFrameworkCore/tree/dev/samples/QueryTypes) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="acf9c-124">You can view this article's [sample](https://github.com/aspnet/EntityFrameworkCore/tree/dev/samples/QueryTypes) on GitHub.</span></span>

<span data-ttu-id="acf9c-125">首先，我們會定義簡單的部落格和後置模型：</span><span class="sxs-lookup"><span data-stu-id="acf9c-125">First, we define a simple Blog and Post model:</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#Entities)]

<span data-ttu-id="acf9c-126">接下來，我們會定義簡單的資料庫檢視，可讓我們查詢的每個部落格相關聯的文章編號：</span><span class="sxs-lookup"><span data-stu-id="acf9c-126">Next, we define a simple database view that will allow us to query the number of posts associated with each blog:</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#View)]

<span data-ttu-id="acf9c-127">接下來，我們會定義以保存從資料庫檢視結果的類別：</span><span class="sxs-lookup"><span data-stu-id="acf9c-127">Next, we define a class to hold the result from the database view:</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#QueryType)]

<span data-ttu-id="acf9c-128">接下來，我們設定中的查詢類型_OnModelCreating_使用```modelBuilder.Query<T>```應用程式開發介面。</span><span class="sxs-lookup"><span data-stu-id="acf9c-128">Next, we configure the query type in _OnModelCreating_ using the ```modelBuilder.Query<T>``` API.</span></span>
<span data-ttu-id="acf9c-129">我們可以使用標準 fluent 應用程式開發設定應用程式開發介面來設定查詢類型的對應：</span><span class="sxs-lookup"><span data-stu-id="acf9c-129">We use standard fluent configuration APIs to configure the mapping for the Query Type:</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#Configuration)]

<span data-ttu-id="acf9c-130">最後，我們可以查詢資料庫檢視，以標準方式：</span><span class="sxs-lookup"><span data-stu-id="acf9c-130">Finally, we can query the database view in the standard way:</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#Query)]

> [!TIP]
> <span data-ttu-id="acf9c-131">請注意，我們也已定義的內容層級查詢屬性 (DbQuery) 做為查詢對這種類型的根。</span><span class="sxs-lookup"><span data-stu-id="acf9c-131">Note we have also defined a context level query property (DbQuery) to act as a root for queries against this type.</span></span>
