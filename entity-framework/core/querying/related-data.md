---
title: "正在載入相關的資料-EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
ms.technology: entity-framework-core
uid: core/querying/related-data
ms.openlocfilehash: cd26bd2e6f85083f73d97b1356d0ba38f53e0b8f
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="loading-related-data"></a><span data-ttu-id="cea9b-102">載入相關的資料</span><span class="sxs-lookup"><span data-stu-id="cea9b-102">Loading Related Data</span></span>

<span data-ttu-id="cea9b-103">Entity Framework Core 可讓您在模型中使用的導覽屬性，來載入相關的實體。</span><span class="sxs-lookup"><span data-stu-id="cea9b-103">Entity Framework Core allows you to use the navigation properties in your model to load related entities.</span></span> <span data-ttu-id="cea9b-104">有三種常見的 O/RM 模式來載入相關的資料。</span><span class="sxs-lookup"><span data-stu-id="cea9b-104">There are three common O/RM patterns used to load related data.</span></span>
* <span data-ttu-id="cea9b-105">**積極式載入**表示相關的資料從資料庫載入做為初始查詢的一部分。</span><span class="sxs-lookup"><span data-stu-id="cea9b-105">**Eager loading** means that the related data is loaded from the database as part of the initial query.</span></span>
* <span data-ttu-id="cea9b-106">**明確式載入**表示相關的資料會明確地載入從資料庫在稍後。</span><span class="sxs-lookup"><span data-stu-id="cea9b-106">**Explicit loading** means that the related data is explicitly loaded from the database at a later time.</span></span>
* <span data-ttu-id="cea9b-107">**消極式載入**表示，相關的資料會以透明的方式從資料庫載入時存取導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="cea9b-107">**Lazy loading** means that the related data is transparently loaded from the database when the navigation property is accessed.</span></span> <span data-ttu-id="cea9b-108">消極式載入尚無法使用 EF 核心。</span><span class="sxs-lookup"><span data-stu-id="cea9b-108">Lazy loading is not yet possible with EF Core.</span></span>

> [!TIP]  
> <span data-ttu-id="cea9b-109">您可以檢視這篇文章[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)GitHub 上。</span><span class="sxs-lookup"><span data-stu-id="cea9b-109">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="eager-loading"></a><span data-ttu-id="cea9b-110">積極式載入</span><span class="sxs-lookup"><span data-stu-id="cea9b-110">Eager loading</span></span>

<span data-ttu-id="cea9b-111">您可以使用`Include`方法，以指定要包含在查詢結果的相關的資料。</span><span class="sxs-lookup"><span data-stu-id="cea9b-111">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="cea9b-112">在下列範例中，會在結果中傳回部落格會有其`Posts`屬性填入相關的文章。</span><span class="sxs-lookup"><span data-stu-id="cea9b-112">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]  
> <span data-ttu-id="cea9b-113">實體 Framework Core 將會自動修正向上導覽屬性與先前已載入至內容執行個體的任何其他實體。</span><span class="sxs-lookup"><span data-stu-id="cea9b-113">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="cea9b-114">因此即使沒有明確加入的資料瀏覽屬性，屬性可能仍然會填入某些或所有相關的項目先前已載入。</span><span class="sxs-lookup"><span data-stu-id="cea9b-114">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>


<span data-ttu-id="cea9b-115">您可以在單一查詢中包含多個關聯性中的相關的資料。</span><span class="sxs-lookup"><span data-stu-id="cea9b-115">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a><span data-ttu-id="cea9b-116">包括多個層級</span><span class="sxs-lookup"><span data-stu-id="cea9b-116">Including multiple levels</span></span>

<span data-ttu-id="cea9b-117">您可以向下鑽研到包含多個層級的相關的資料所使用的關聯性`ThenInclude`方法。</span><span class="sxs-lookup"><span data-stu-id="cea9b-117">You can drill down thru relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="cea9b-118">下列範例會載入所有的部落格、 其相關的文章，每次張貼的作者。</span><span class="sxs-lookup"><span data-stu-id="cea9b-118">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#SingleThenInclude)]

<span data-ttu-id="cea9b-119">您可以多個呼叫鏈結到`ThenInclude`繼續進一步包括層級的相關資料。</span><span class="sxs-lookup"><span data-stu-id="cea9b-119">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

<span data-ttu-id="cea9b-120">您可以結合這些全部都在相同查詢中包含相關的資料從多個層級和多個根節點。</span><span class="sxs-lookup"><span data-stu-id="cea9b-120">You can combine all of this to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#IncludeTree)]

<span data-ttu-id="cea9b-121">若要包含的其中一個實體所包含的多個相關的實體。</span><span class="sxs-lookup"><span data-stu-id="cea9b-121">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="cea9b-122">例如，當查詢`Blog`s，包括`Posts`接著又想將同時包含`Author`和`Tags`的`Posts`。</span><span class="sxs-lookup"><span data-stu-id="cea9b-122">For example, when querying `Blog`s, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="cea9b-123">若要這樣做，您要指定每個包含從根目錄開始的路徑。</span><span class="sxs-lookup"><span data-stu-id="cea9b-123">To do this, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="cea9b-124">例如， `Blog -> Posts -> Author` 和 `Blog -> Posts -> Tags`。</span><span class="sxs-lookup"><span data-stu-id="cea9b-124">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="cea9b-125">這不表示您會收到多餘的聯結，在大部分情況下，將會合併 EF 聯結產生 SQL 時。</span><span class="sxs-lookup"><span data-stu-id="cea9b-125">This does not mean you will get redundant joins, in most cases EF will consolidate the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

### <a name="ignored-includes"></a><span data-ttu-id="cea9b-126">忽略包含</span><span class="sxs-lookup"><span data-stu-id="cea9b-126">Ignored includes</span></span>

<span data-ttu-id="cea9b-127">如果您變更查詢，使它不會再傳回查詢開始的實體類型的執行個體時，會忽略 include 運算子。</span><span class="sxs-lookup"><span data-stu-id="cea9b-127">If you change the query so that it no longer returns instances of the entity type that the query began with, then the include operators are ignored.</span></span>

<span data-ttu-id="cea9b-128">在下列範例中，包含運算子會根據`Blog`，但然後`Select`運算子用來變更查詢以傳回匿名型別。</span><span class="sxs-lookup"><span data-stu-id="cea9b-128">In the following example, the include operators are based on the `Blog`, but then the `Select` operator is used to change the query to return an anonymous type.</span></span> <span data-ttu-id="cea9b-129">在此情況下，包含運算子會有任何作用。</span><span class="sxs-lookup"><span data-stu-id="cea9b-129">In this case, the include operators have no effect.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#IgnoredInclude)]

<span data-ttu-id="cea9b-130">根據預設，EF 核心會記錄警告時包含運算子都會被忽略。</span><span class="sxs-lookup"><span data-stu-id="cea9b-130">By default, EF Core will log a warning when include operators are ignored.</span></span> <span data-ttu-id="cea9b-131">請參閱[記錄](../miscellaneous/logging.md)檢視記錄輸出的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="cea9b-131">See [Logging](../miscellaneous/logging.md) for more information on viewing logging output.</span></span> <span data-ttu-id="cea9b-132">對擲回或不執行任何動作，則會忽略 include 運算子時，您可以變更行為。</span><span class="sxs-lookup"><span data-stu-id="cea9b-132">You can change the behavior when an include operator is ignored to either throw or do nothing.</span></span> <span data-ttu-id="cea9b-133">這是通常在設定為您的內容-選項時`DbContext.OnConfiguring`，或在`Startup.cs`如果您使用 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="cea9b-133">This is done when setting up the options for your context - typically in `DbContext.OnConfiguring`, or in `Startup.cs` if you are using ASP.NET Core.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/ThrowOnIgnoredInclude/BloggingContext.cs#OnConfiguring)]

## <a name="explicit-loading"></a><span data-ttu-id="cea9b-134">明確式載入</span><span class="sxs-lookup"><span data-stu-id="cea9b-134">Explicit loading</span></span>

> [!NOTE]  
> <span data-ttu-id="cea9b-135">在 EF 核心 1.1 已引入此功能。</span><span class="sxs-lookup"><span data-stu-id="cea9b-135">This feature was introduced in EF Core 1.1.</span></span>

<span data-ttu-id="cea9b-136">您可以明確載入的導覽屬性，透過`DbContext.Entry(...)`應用程式開發介面。</span><span class="sxs-lookup"><span data-stu-id="cea9b-136">You can explicitly load a navigation property via the `DbContext.Entry(...)` API.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#Eager)]

<span data-ttu-id="cea9b-137">您也會明確可以藉由執行不同的查詢會傳回相關的實體載入導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="cea9b-137">You can also explicitly load a navigation property by executing a seperate query that returns the related entities.</span></span> <span data-ttu-id="cea9b-138">如果已啟用變更追蹤，則載入時實體，EF Core 將會自動設定來參考任何實體已經載入，新載入實體的導覽屬性並設定參考的已載入實體的導覽屬性新載入的實體。</span><span class="sxs-lookup"><span data-stu-id="cea9b-138">If change tracking is enabled, then when loading an entity, EF Core will automatically set the navigation properties of the newly-loaded entitiy to refer to any entities already loaded, and set the navigation properties of the already-loaded entities to refer to the newly-loaded entity.</span></span>

### <a name="querying-related-entities"></a><span data-ttu-id="cea9b-139">查詢相關的實體</span><span class="sxs-lookup"><span data-stu-id="cea9b-139">Querying related entities</span></span>

<span data-ttu-id="cea9b-140">您也可以取得 LINQ 查詢，表示導覽屬性的內容。</span><span class="sxs-lookup"><span data-stu-id="cea9b-140">You can also get a LINQ query that represents the contents of a navigation property.</span></span>

<span data-ttu-id="cea9b-141">這可讓您執行像是透過相關的項目執行彙總運算子，而不載入記憶體。</span><span class="sxs-lookup"><span data-stu-id="cea9b-141">This allows you to do things such as running an aggregate operator over the related entities without loading them into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

<span data-ttu-id="cea9b-142">您也可以篩選哪些相關的實體載入至記憶體。</span><span class="sxs-lookup"><span data-stu-id="cea9b-142">You can also filter which related entities are loaded into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a><span data-ttu-id="cea9b-143">消極式載入</span><span class="sxs-lookup"><span data-stu-id="cea9b-143">Lazy loading</span></span>

<span data-ttu-id="cea9b-144">EF 核心尚不支援消極式載入。</span><span class="sxs-lookup"><span data-stu-id="cea9b-144">Lazy loading is not yet supported by EF Core.</span></span> <span data-ttu-id="cea9b-145">您可以檢視[消極式載入項目，在我們的待辦項目](https://github.com/aspnet/EntityFramework/issues/3797)追蹤這項功能。</span><span class="sxs-lookup"><span data-stu-id="cea9b-145">You can view the [lazy loading item on our backlog](https://github.com/aspnet/EntityFramework/issues/3797) to track this feature.</span></span>

## <a name="related-data-and-serialization"></a><span data-ttu-id="cea9b-146">相關的資料和序列化</span><span class="sxs-lookup"><span data-stu-id="cea9b-146">Related data and serialization</span></span>

<span data-ttu-id="cea9b-147">因為 EF 核心會自動修正向上導覽屬性，您可以得到循環物件圖形中。</span><span class="sxs-lookup"><span data-stu-id="cea9b-147">Because EF Core will automatically fix-up navigation properties, you can end up with cycles in your object graph.</span></span> <span data-ttu-id="cea9b-148">例如，載入部落格和它相關的文章將會導致參考的文章集合部落格物件。</span><span class="sxs-lookup"><span data-stu-id="cea9b-148">For example, Loading a blog and it's related posts will result in a blog object that references a collection of posts.</span></span> <span data-ttu-id="cea9b-149">這些文章中的每個都會有部落格的參考。</span><span class="sxs-lookup"><span data-stu-id="cea9b-149">Each of those posts will have a reference back to the blog.</span></span>

<span data-ttu-id="cea9b-150">某些序列化架構不允許這類循環。</span><span class="sxs-lookup"><span data-stu-id="cea9b-150">Some serialization frameworks do not allow such cycles.</span></span> <span data-ttu-id="cea9b-151">例如，Json.NET 將會擲回下列例外狀況，如果有發生循環。</span><span class="sxs-lookup"><span data-stu-id="cea9b-151">For example, Json.NET will throw the following exception if a cycle is encoutered.</span></span>

> <span data-ttu-id="cea9b-152">Newtonsoft.Json.JsonSerializationException： 自我參考型別 'MyApplication.Models.Blog' 屬性 '部落格' 偵測到迴圈。</span><span class="sxs-lookup"><span data-stu-id="cea9b-152">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span></span>

<span data-ttu-id="cea9b-153">如果您使用 ASP.NET Core，您可以設定 Json.NET 忽略它找到的物件圖形中的循環。</span><span class="sxs-lookup"><span data-stu-id="cea9b-153">If you are using ASP.NET Core, you can configure Json.NET to ignore cycles that it finds in the object graph.</span></span> <span data-ttu-id="cea9b-154">這是`ConfigureServices(...)`方法中的`Startup.cs`。</span><span class="sxs-lookup"><span data-stu-id="cea9b-154">This is done in the `ConfigureServices(...)` method in `Startup.cs`.</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddMvc()
        .AddJsonOptions(
            options => options.SerializerSettings.ReferenceLoopHandling = Newtonsoft.Json.ReferenceLoopHandling.Ignore
        );

    ...
}
```
