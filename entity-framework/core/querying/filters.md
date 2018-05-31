---
title: 全域查詢篩選條件 - EF Core
author: anpete
ms.author: anpete
ms.date: 11/03/2017
ms.technology: entity-framework-core
uid: core/querying/filters
ms.openlocfilehash: 4e3c3c99d155f69e00fed99c415f519808ea1a68
ms.sourcegitcommit: 6e379265e4f087fb7cf180c824722c81750554dc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
ms.locfileid: "26053898"
---
# <a name="global-query-filters"></a><span data-ttu-id="967ab-102">全域查詢篩選條件</span><span class="sxs-lookup"><span data-stu-id="967ab-102">Global Query Filters</span></span>

<span data-ttu-id="967ab-103">全域查詢篩選條件是中繼資料模型 (通常位於 *OnModelCreating*) 中套用到實體類型的 LINQ 查詢述詞 (布林運算式通常會傳遞到 LINQ *Where* 查詢運算子)。</span><span class="sxs-lookup"><span data-stu-id="967ab-103">Global query filters are LINQ query predicates (a boolean expression typically passed to the LINQ *Where* query operator) applied to Entity Types in the metadata model (usually in *OnModelCreating*).</span></span> <span data-ttu-id="967ab-104">這類篩選會自動套用至任何涉及這些「實體類型」(包含間接參考的「實體類型」) 的 LINQ 查詢 (例如，使用 Include 或直接導覽屬性參考)。</span><span class="sxs-lookup"><span data-stu-id="967ab-104">Such filters are automatically applied to any LINQ queries involving those Entity Types, including Entity Types referenced indirectly, such as through the use of Include or direct navigation property references.</span></span> <span data-ttu-id="967ab-105">此功能的一些常見應用如下：</span><span class="sxs-lookup"><span data-stu-id="967ab-105">Some common applications of this feature are:</span></span>

* <span data-ttu-id="967ab-106">**虛刪除**：實體類型會定義 *IsDeleted* 屬性。</span><span class="sxs-lookup"><span data-stu-id="967ab-106">**Soft delete** - An Entity Type defines an *IsDeleted* property.</span></span>
* <span data-ttu-id="967ab-107">**多租用戶**：實體類型會定義 *TenantId* 屬性。</span><span class="sxs-lookup"><span data-stu-id="967ab-107">**Multi-tenancy** - An Entity Type defines a *TenantId* property.</span></span>

## <a name="example"></a><span data-ttu-id="967ab-108">範例</span><span class="sxs-lookup"><span data-stu-id="967ab-108">Example</span></span>

<span data-ttu-id="967ab-109">下列範例示範如何使用全域查詢篩選條件，在簡單的部落格模型中實作虛刪除和多租用戶查詢行為。</span><span class="sxs-lookup"><span data-stu-id="967ab-109">The following example shows how to use Global Query Filters to implement soft-delete and multi-tenancy query behaviors in a simple blogging model.</span></span>

> [!TIP]
> <span data-ttu-id="967ab-110">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFrameworkCore/tree/dev/samples/QueryFilters) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="967ab-110">You can view this article's [sample](https://github.com/aspnet/EntityFrameworkCore/tree/dev/samples/QueryFilters) on GitHub.</span></span>

<span data-ttu-id="967ab-111">首先，定義實體：</span><span class="sxs-lookup"><span data-stu-id="967ab-111">First, define the entities:</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryFilters/Program.cs#Entities)]

<span data-ttu-id="967ab-112">請注意 _Blog_ 實體上 __tenantId_ 欄位的宣告。</span><span class="sxs-lookup"><span data-stu-id="967ab-112">Note the declaration of a __tenantId_ field on the _Blog_ entity.</span></span> <span data-ttu-id="967ab-113">這會用來將每個 Blog 執行個體與特定租用戶產生關聯。</span><span class="sxs-lookup"><span data-stu-id="967ab-113">This will be used to associate each Blog instance with a specific tenant.</span></span> <span data-ttu-id="967ab-114">此外，還定義了 _Post_ 實體類型上的 _IsDeleted_ 屬性。</span><span class="sxs-lookup"><span data-stu-id="967ab-114">Also defined is an _IsDeleted_ property on the _Post_ entity type.</span></span> <span data-ttu-id="967ab-115">這會用來追蹤是否已將 _Post_ 執行個體「虛刪除」。</span><span class="sxs-lookup"><span data-stu-id="967ab-115">This is used this to keep track of whether a _Post_ instance has been "soft-deleted".</span></span> <span data-ttu-id="967ab-116">也就是，將執行個體標示為已刪除，而不需實際移除基礎資料。</span><span class="sxs-lookup"><span data-stu-id="967ab-116">I.e. The instance is marked as deleted withouth physically removing the underlying data.</span></span>

<span data-ttu-id="967ab-117">接下來，使用 ```HasQueryFilter``` API，在 _OnModelCreating_ 中設定查詢篩選條件。</span><span class="sxs-lookup"><span data-stu-id="967ab-117">Next, configure the query filters in _OnModelCreating_ using the ```HasQueryFilter``` API.</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryFilters/Program.cs#Configuration)]

<span data-ttu-id="967ab-118">傳遞到 _HasQueryFilter_ 呼叫的述詞運算式，現在將針對那些類型自動套用到任何 LINQ 查詢。</span><span class="sxs-lookup"><span data-stu-id="967ab-118">The predicate expressions passed to the _HasQueryFilter_ calls will now automatically be applied to any LINQ queries for those types.</span></span>

> [!TIP]
> <span data-ttu-id="967ab-119">請注意 DbContext 執行個體層級欄位的使用方式：```_tenantId``` 用來設定目前的租用戶。</span><span class="sxs-lookup"><span data-stu-id="967ab-119">Note the use of a DbContext instance level field: ```_tenantId``` used to set the current tenant.</span></span> <span data-ttu-id="967ab-120">模型層級篩選將使用正確內容執行個體中的值。</span><span class="sxs-lookup"><span data-stu-id="967ab-120">Model-level filters will use the value from the correct context instance.</span></span> <span data-ttu-id="967ab-121">也就是，執行查詢的執行個體。</span><span class="sxs-lookup"><span data-stu-id="967ab-121">I.e. The instance that is executing the query.</span></span>

## <a name="disabling-filters"></a><span data-ttu-id="967ab-122">停用篩選條件</span><span class="sxs-lookup"><span data-stu-id="967ab-122">Disabling Filters</span></span>

<span data-ttu-id="967ab-123">可能會使用 ```IgnoreQueryFilters()``` 運算子來停用個別 LINQ 查詢的篩選條件。</span><span class="sxs-lookup"><span data-stu-id="967ab-123">Filters may be disabled for individual LINQ queries by using the ```IgnoreQueryFilters()``` operator.</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a><span data-ttu-id="967ab-124">限制</span><span class="sxs-lookup"><span data-stu-id="967ab-124">Limitations</span></span>

<span data-ttu-id="967ab-125">全域查詢篩選條件具有下列限制：</span><span class="sxs-lookup"><span data-stu-id="967ab-125">Global query filters have the following limitations:</span></span>

* <span data-ttu-id="967ab-126">篩選條件不能包含對導覽屬性的參考。</span><span class="sxs-lookup"><span data-stu-id="967ab-126">Filters cannot contain references to navigation properties.</span></span>
* <span data-ttu-id="967ab-127">只能針對繼承階層的根實體類型定義篩選條件。</span><span class="sxs-lookup"><span data-stu-id="967ab-127">Filters can only be defined for the root Entity Type of an inheritance hierarchy.</span></span>
