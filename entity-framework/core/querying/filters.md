---
title: "全域查詢篩選器的 EF 核心"
author: anpete
ms.author: anpete
ms.date: 11/03/2017
ms.technology: entity-framework-core
uid: core/querying/filters
ms.openlocfilehash: 4e3c3c99d155f69e00fed99c415f519808ea1a68
ms.sourcegitcommit: 6e379265e4f087fb7cf180c824722c81750554dc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="global-query-filters"></a><span data-ttu-id="5326d-102">全域查詢篩選器</span><span class="sxs-lookup"><span data-stu-id="5326d-102">Global Query Filters</span></span>

<span data-ttu-id="5326d-103">全域查詢篩選器是 LINQ 查詢述詞 (布林值運算式通常傳遞至 LINQ*其中*查詢運算子) 套用至實體類型的中繼資料模型中 (通常在*OnModelCreating*)。</span><span class="sxs-lookup"><span data-stu-id="5326d-103">Global query filters are LINQ query predicates (a boolean expression typically passed to the LINQ *Where* query operator) applied to Entity Types in the metadata model (usually in *OnModelCreating*).</span></span> <span data-ttu-id="5326d-104">這類篩選器會自動套用至涉及這些實體類型，包括實體類型的直接或間接，例如透過使用包含參考導覽屬性參考任何 LINQ 查詢。</span><span class="sxs-lookup"><span data-stu-id="5326d-104">Such filters are automatically applied to any LINQ queries involving those Entity Types, including Entity Types referenced indirectly, such as through the use of Include or direct navigation property references.</span></span> <span data-ttu-id="5326d-105">這項功能的一些常見的應用程式如下：</span><span class="sxs-lookup"><span data-stu-id="5326d-105">Some common applications of this feature are:</span></span>

* <span data-ttu-id="5326d-106">**虛刪除**-實體類型定義*IsDeleted*屬性。</span><span class="sxs-lookup"><span data-stu-id="5326d-106">**Soft delete** - An Entity Type defines an *IsDeleted* property.</span></span>
* <span data-ttu-id="5326d-107">**多租用戶**-實體類型定義*TenantId*屬性。</span><span class="sxs-lookup"><span data-stu-id="5326d-107">**Multi-tenancy** - An Entity Type defines a *TenantId* property.</span></span>

## <a name="example"></a><span data-ttu-id="5326d-108">範例</span><span class="sxs-lookup"><span data-stu-id="5326d-108">Example</span></span>

<span data-ttu-id="5326d-109">下列範例會示範如何使用全域查詢篩選器來實作簡單的部落格模型中的虛刪除和多租用戶的查詢行為。</span><span class="sxs-lookup"><span data-stu-id="5326d-109">The following example shows how to use Global Query Filters to implement soft-delete and multi-tenancy query behaviors in a simple blogging model.</span></span>

> [!TIP]
> <span data-ttu-id="5326d-110">您可以檢視這篇文章[範例](https://github.com/aspnet/EntityFrameworkCore/tree/dev/samples/QueryFilters)GitHub 上。</span><span class="sxs-lookup"><span data-stu-id="5326d-110">You can view this article's [sample](https://github.com/aspnet/EntityFrameworkCore/tree/dev/samples/QueryFilters) on GitHub.</span></span>

<span data-ttu-id="5326d-111">首先，定義實體：</span><span class="sxs-lookup"><span data-stu-id="5326d-111">First, define the entities:</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryFilters/Program.cs#Entities)]

<span data-ttu-id="5326d-112">請注意 _ 宣告_tenantId_欄位_部落格_實體。</span><span class="sxs-lookup"><span data-stu-id="5326d-112">Note the declaration of a __tenantId_ field on the _Blog_ entity.</span></span> <span data-ttu-id="5326d-113">這會用來將每個部落格執行個體與特定租用戶產生關聯。</span><span class="sxs-lookup"><span data-stu-id="5326d-113">This will be used to associate each Blog instance with a specific tenant.</span></span> <span data-ttu-id="5326d-114">也定義是_IsDeleted_屬性_Post_實體類型。</span><span class="sxs-lookup"><span data-stu-id="5326d-114">Also defined is an _IsDeleted_ property on the _Post_ entity type.</span></span> <span data-ttu-id="5326d-115">這使用此選項以追蹤是否_Post_執行個體已 「 虛刪除 」。</span><span class="sxs-lookup"><span data-stu-id="5326d-115">This is used this to keep track of whether a _Post_ instance has been "soft-deleted".</span></span> <span data-ttu-id="5326d-116">也就是執行個體標示為已刪除 withouth 實際移除基礎資料。</span><span class="sxs-lookup"><span data-stu-id="5326d-116">I.e. The instance is marked as deleted withouth physically removing the underlying data.</span></span>

<span data-ttu-id="5326d-117">接下來，設定中的查詢篩選器_OnModelCreating_使用```HasQueryFilter```應用程式開發介面。</span><span class="sxs-lookup"><span data-stu-id="5326d-117">Next, configure the query filters in _OnModelCreating_ using the ```HasQueryFilter``` API.</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryFilters/Program.cs#Configuration)]

<span data-ttu-id="5326d-118">述詞運算式傳遞至_HasQueryFilter_呼叫，現在將會自動套用至任何 LINQ 查詢，針對這些型別。</span><span class="sxs-lookup"><span data-stu-id="5326d-118">The predicate expressions passed to the _HasQueryFilter_ calls will now automatically be applied to any LINQ queries for those types.</span></span>

> [!TIP]
> <span data-ttu-id="5326d-119">請注意使用 DbContext 執行個體層級欄位：```_tenantId```用來設定目前的租用戶。</span><span class="sxs-lookup"><span data-stu-id="5326d-119">Note the use of a DbContext instance level field: ```_tenantId``` used to set the current tenant.</span></span> <span data-ttu-id="5326d-120">模型層級篩選器會使用正確的內容執行個體的值。</span><span class="sxs-lookup"><span data-stu-id="5326d-120">Model-level filters will use the value from the correct context instance.</span></span> <span data-ttu-id="5326d-121">也就是正在執行查詢的執行個體。</span><span class="sxs-lookup"><span data-stu-id="5326d-121">I.e. The instance that is executing the query.</span></span>

## <a name="disabling-filters"></a><span data-ttu-id="5326d-122">停用篩選器</span><span class="sxs-lookup"><span data-stu-id="5326d-122">Disabling Filters</span></span>

<span data-ttu-id="5326d-123">篩選器可能會停用個別的 LINQ 查詢使用```IgnoreQueryFilters()```運算子。</span><span class="sxs-lookup"><span data-stu-id="5326d-123">Filters may be disabled for individual LINQ queries by using the ```IgnoreQueryFilters()``` operator.</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a><span data-ttu-id="5326d-124">限制</span><span class="sxs-lookup"><span data-stu-id="5326d-124">Limitations</span></span>

<span data-ttu-id="5326d-125">全域查詢篩選器有下列限制：</span><span class="sxs-lookup"><span data-stu-id="5326d-125">Global query filters have the following limitations:</span></span>

* <span data-ttu-id="5326d-126">篩選條件不能包含參考導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="5326d-126">Filters cannot contain references to navigation properties.</span></span>
* <span data-ttu-id="5326d-127">只可以定義篩選，根實體類型的繼承階層架構。</span><span class="sxs-lookup"><span data-stu-id="5326d-127">Filters can only be defined for the root Entity Type of an inheritance hierarchy.</span></span>
