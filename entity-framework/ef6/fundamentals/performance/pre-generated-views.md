---
title: 預先產生的對應檢視-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 917ba9c8-6ddf-4631-ab8c-c4fb378c2fcd
caps.latest.revision: 3
ms.openlocfilehash: 9e74176d02afc424118219eec8e016843333cbb8
ms.sourcegitcommit: 390f3a37bc55105ed7cc5b0e0925b7f9c9e80ba6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2018
ms.locfileid: "39120474"
---
# <a name="pre-generated-mapping-views"></a><span data-ttu-id="9dab2-102">預先產生的對應檢視</span><span class="sxs-lookup"><span data-stu-id="9dab2-102">Pre-generated mapping views</span></span>
<span data-ttu-id="9dab2-103">Entity Framework 可以執行查詢，或將變更儲存至資料來源之前，它必須產生一組對應檢視，來存取資料庫。</span><span class="sxs-lookup"><span data-stu-id="9dab2-103">Before the Entity Framework can execute a query or save changes to the data source, it must generate a set of mapping views to access the database.</span></span> <span data-ttu-id="9dab2-104">這些對應檢視是一組以抽象方式代表資料庫的 Entity SQL 陳述式，中繼資料的快取每個應用程式網域的一部分。</span><span class="sxs-lookup"><span data-stu-id="9dab2-104">These mapping views are a set of Entity SQL statement that represent the database in an abstract way, and are part of the metadata which is cached per application domain.</span></span> <span data-ttu-id="9dab2-105">如果您在相同的應用程式定義域中建立多個相同的內容執行個體，它們會重複使用從快取的中繼資料，而不是重新產生它們的對應檢視。</span><span class="sxs-lookup"><span data-stu-id="9dab2-105">If you create multiple instances of the same context in the same application domain, they will reuse mapping views from the cached metadata rather than regenerating them.</span></span> <span data-ttu-id="9dab2-106">對應檢視表產生是執行第一個查詢的整體成本的重要部分，因為 Entity Framework 可讓您預先產生對應檢視，並將它們包含在已編譯的專案。</span><span class="sxs-lookup"><span data-stu-id="9dab2-106">Because mapping view generation is a significant part of the overall cost of executing the first query, the Entity Framework enables you to pre-generate mapping views and include them in the compiled project.</span></span> <span data-ttu-id="9dab2-107">如需詳細資訊，請參閱 <<c0> [ 效能考量 (Entity Framework)](~/ef6/fundamentals/performance/perf-whitepaper.md)。</span><span class="sxs-lookup"><span data-stu-id="9dab2-107">For more information, see  [Performance Considerations (Entity Framework)](~/ef6/fundamentals/performance/perf-whitepaper.md).</span></span>

## <a name="generating-mapping-views-with-the-ef-power-tools"></a><span data-ttu-id="9dab2-108">產生對應 EF Power tools 檢視</span><span class="sxs-lookup"><span data-stu-id="9dab2-108">Generating Mapping Views with the EF Power Tools</span></span>

<span data-ttu-id="9dab2-109">預先產生檢視表的最簡單方式是使用[EF Power Tools](http://visualstudiogallery.msdn.microsoft.com/72a60b14-1581-4b9b-89f2-846072eff19d)。</span><span class="sxs-lookup"><span data-stu-id="9dab2-109">The easiest way to pre-generate views is to use the [EF Power Tools](http://visualstudiogallery.msdn.microsoft.com/72a60b14-1581-4b9b-89f2-846072eff19d).</span></span> <span data-ttu-id="9dab2-110">一旦您已安裝的 Power Tools 就會產生檢視表的功能表選項，如下所示。</span><span class="sxs-lookup"><span data-stu-id="9dab2-110">Once you have the Power Tools installed you will have a menu option to Generate Views, as below.</span></span>

-   <span data-ttu-id="9dab2-111">針對**Code First**模型以滑鼠右鍵按一下包含您的 DbContext 類別的程式碼檔案。</span><span class="sxs-lookup"><span data-stu-id="9dab2-111">For **Code First** models right-click on the code file that contains your DbContext class.</span></span>
-   <span data-ttu-id="9dab2-112">針對**EF 設計工具**模型以滑鼠右鍵按一下您的 EDMX 檔案。</span><span class="sxs-lookup"><span data-stu-id="9dab2-112">For **EF Designer** models right-click on your EDMX file.</span></span>

![generateViews](~/ef6/media/generateviews.png)

<span data-ttu-id="9dab2-114">一旦完成此程序就會產生如下所示的類別</span><span class="sxs-lookup"><span data-stu-id="9dab2-114">Once the process is finished you will have a class similar to the following generated</span></span>

![generatedViews](~/ef6/media/generatedviews.png)

<span data-ttu-id="9dab2-116">現在當您執行 EF 應用程式會使用此類別來載入所需的檢視。</span><span class="sxs-lookup"><span data-stu-id="9dab2-116">Now when you run your application EF will use this class to load views as required.</span></span> <span data-ttu-id="9dab2-117">如果您的模型變更，而且您不重新產生這個類別 EF 會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9dab2-117">If your model changes and you do not re-generate this class then EF will throw an exception.</span></span>

## <a name="generating-mapping-views-from-code---ef6-onwards"></a><span data-ttu-id="9dab2-118">從程式碼-從 EF6 開始產生的對應檢視</span><span class="sxs-lookup"><span data-stu-id="9dab2-118">Generating Mapping Views from Code - EF6 Onwards</span></span>

<span data-ttu-id="9dab2-119">產生檢視的其他方式是使用 EF 所提供的 Api。</span><span class="sxs-lookup"><span data-stu-id="9dab2-119">The other way to generate views is to use the APIs that EF provides.</span></span> <span data-ttu-id="9dab2-120">使用此方法時，您可以自由序列化的檢視，不過您喜歡，但您也需要載入您自己的檢視。</span><span class="sxs-lookup"><span data-stu-id="9dab2-120">When using this method you have the freedom to serialize the views however you like, but you also need to load the views yourself.</span></span>

> [!NOTE]
> <span data-ttu-id="9dab2-121">**EF6 及更新版本僅**-Entity Framework 6 中導入的 Api，這一節所示。</span><span class="sxs-lookup"><span data-stu-id="9dab2-121">**EF6 Onwards Only** - The APIs shown in this section were introduced in Entity Framework 6.</span></span> <span data-ttu-id="9dab2-122">如果您使用這項資訊不適用於較早版本。</span><span class="sxs-lookup"><span data-stu-id="9dab2-122">If you are using an earlier version this information does not apply.</span></span>

### <a name="generating-views"></a><span data-ttu-id="9dab2-123">產生檢視</span><span class="sxs-lookup"><span data-stu-id="9dab2-123">Generating Views</span></span>

<span data-ttu-id="9dab2-124">使用 Api 來產生檢視表位於 System.Data.Entity.Core.Mapping.StorageMappingItemCollection 類別。</span><span class="sxs-lookup"><span data-stu-id="9dab2-124">The APIs to generate views are on the System.Data.Entity.Core.Mapping.StorageMappingItemCollection class.</span></span> <span data-ttu-id="9dab2-125">您可以使用 ObjectContext 的 MetadataWorkspace 內容的檔案，以擷取 StorageMappingCollection。</span><span class="sxs-lookup"><span data-stu-id="9dab2-125">You can retrieve a StorageMappingCollection for a Context by using the MetadataWorkspace of an ObjectContext.</span></span> <span data-ttu-id="9dab2-126">如果您使用較新的 DbContext API 時，則您可以使用來存取這 IObjectContextAdapter 如下所示，在這段程式碼中，我們有您呼叫 dbContext 的衍生 DbContext 的執行個體：</span><span class="sxs-lookup"><span data-stu-id="9dab2-126">If you are using the newer DbContext API then you can access this by using the IObjectContextAdapter like below, in this code we have an instance of your derived DbContext called dbContext:</span></span>

``` csharp
    var objectContext = ((IObjectContextAdapter) dbContext).ObjectContext;
    var  mappingCollection = (StorageMappingItemCollection)objectContext.MetadataWorkspace
                                                                        .GetItemCollection(DataSpace.CSSpace);
```

<span data-ttu-id="9dab2-127">一旦您擁有 storagemappingitemcollection 由您就可以取得 GenerateViews 和 ComputeMappingHashValue 方法的存取。</span><span class="sxs-lookup"><span data-stu-id="9dab2-127">Once you have the StorageMappingItemCollection then you can get access to the GenerateViews and ComputeMappingHashValue methods.</span></span>

``` csharp
    public Dictionary\<EntitySetBase, DbMappingView> GenerateViews(IList<EdmSchemaError> errors)
    public string ComputeMappingHashValue()
```

<span data-ttu-id="9dab2-128">第一種方法會建立字典的項目，為每個容器對應中的檢視。</span><span class="sxs-lookup"><span data-stu-id="9dab2-128">The first method creates a dictionary with an entry for each view in the container mapping.</span></span> <span data-ttu-id="9dab2-129">第二種方法計算單一容器對應的雜湊值，並用於執行階段驗證的模型已不會變更因為預先產生檢視。</span><span class="sxs-lookup"><span data-stu-id="9dab2-129">The second method computes a hash value for the single container mapping and is used at runtime to validate that the model has not changed since the views were pre-generated.</span></span> <span data-ttu-id="9dab2-130">涉及多個容器對應的複雜案例提供兩種方法的覆寫。</span><span class="sxs-lookup"><span data-stu-id="9dab2-130">Overrides of the two methods are provided for complex scenarios involving multiple container mappings.</span></span>

<span data-ttu-id="9dab2-131">產生檢視時將呼叫 GenerateViews; 方法，並接著寫出產生的 EntitySetBase 和 DbMappingView。</span><span class="sxs-lookup"><span data-stu-id="9dab2-131">When generating views you will call the GenerateViews method and then write out the resulting EntitySetBase and DbMappingView.</span></span> <span data-ttu-id="9dab2-132">您也必須儲存 ComputeMappingHashValue 方法所產生的雜湊。</span><span class="sxs-lookup"><span data-stu-id="9dab2-132">You will also need to store the hash generated by the ComputeMappingHashValue method.</span></span>

### <a name="loading-views"></a><span data-ttu-id="9dab2-133">正在載入檢視</span><span class="sxs-lookup"><span data-stu-id="9dab2-133">Loading Views</span></span>

<span data-ttu-id="9dab2-134">若要載入 GenerateViews 方法所產生的檢視，您可以提供 EF DbMappingViewCache 抽象類別繼承的類別。</span><span class="sxs-lookup"><span data-stu-id="9dab2-134">In order to load the views generated by the GenerateViews method, you can provide EF with a class that inherits from the DbMappingViewCache abstract class.</span></span> <span data-ttu-id="9dab2-135">DbMappingViewCache 指定必須實作的兩個方法：</span><span class="sxs-lookup"><span data-stu-id="9dab2-135">DbMappingViewCache specifies two methods that you must implement:</span></span>

``` csharp
    public abstract string MappingHashValue { get; }
    public abstract DbMappingView GetView(EntitySetBase extent);
```

<span data-ttu-id="9dab2-136">MappingHashValue 屬性必須傳回 ComputeMappingHashValue 方法所產生的雜湊。</span><span class="sxs-lookup"><span data-stu-id="9dab2-136">The MappingHashValue property must return the hash generated by the ComputeMappingHashValue method.</span></span> <span data-ttu-id="9dab2-137">EF 時要要求檢視它會先產生，並比較該模型的雜湊值與這個屬性所傳回的雜湊。</span><span class="sxs-lookup"><span data-stu-id="9dab2-137">When EF is going to ask for views it will first generate and compare the hash value of the model with the hash returned by this property.</span></span> <span data-ttu-id="9dab2-138">如果兩者不符 EF 會擲回 EntityCommandCompilationException 例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9dab2-138">If they do not match then EF will throw an EntityCommandCompilationException exception.</span></span>

<span data-ttu-id="9dab2-139">GetView 方法會接受 EntitySetBase，而且您需要傳回包含所產生的 EntitySql DbMappingVIew 已 GenerateViews 方法所產生字典中指定的 EntitySetBase 相關聯。</span><span class="sxs-lookup"><span data-stu-id="9dab2-139">The GetView method will accept an EntitySetBase and you need to return a DbMappingVIew containing the EntitySql that was generated for that was associated with the given EntitySetBase in the dictionary generated by the GenerateViews method.</span></span> <span data-ttu-id="9dab2-140">如果 EF 要求提供的檢視，您不需要再 GetView 應該傳回 null。</span><span class="sxs-lookup"><span data-stu-id="9dab2-140">If EF asks for a view that you do not have then GetView should return null.</span></span>

<span data-ttu-id="9dab2-141">以下是擷取自 DbMappingViewCache 上面所述，在其中儲存和擷取所需的 EntitySql 一種方法，我們看到 Power tools 產生。</span><span class="sxs-lookup"><span data-stu-id="9dab2-141">The following is an extract from the DbMappingViewCache that is generated with the Power Tools as described above, in it we see one way to store and retrieve the EntitySql required.</span></span>

``` csharp
    public override string MappingHashValue
    {
        get { return "a0b843f03dd29abee99789e190a6fb70ce8e93dc97945d437d9a58fb8e2afd2e"; }
    }

    public override DbMappingView GetView(EntitySetBase extent)
    {
        if (extent == null)
        {
            throw new ArgumentNullException("extent");
        }

        var extentName = extent.EntityContainer.Name + "." + extent.Name;

        if (extentName == "BlogContext.Blogs")
        {
            return GetView2();
        }

        if (extentName == "BlogContext.Posts")
        {
            return GetView3();
        }

        return null;
    }

    private static DbMappingView GetView2()
    {
        return new DbMappingView(@"
            SELECT VALUE -- Constructing Blogs
            [BlogApp.Models.Blog](T1.Blog_BlogId, T1.Blog_Test, T1.Blog_title, T1.Blog_Active, T1.Blog_SomeDecimal)
            FROM (
            SELECT
                T.BlogId AS Blog_BlogId,
                T.Test AS Blog_Test,
                T.title AS Blog_title,
                T.Active AS Blog_Active,
                T.SomeDecimal AS Blog_SomeDecimal,
                True AS _from0
            FROM CodeFirstDatabase.Blog AS T
            ) AS T1");
    }
```

<span data-ttu-id="9dab2-142">若要讓 EF 使用您新增您 DbMappingViewCache 會使用 DbMappingViewCacheTypeAttribute，指定為建立的內容。</span><span class="sxs-lookup"><span data-stu-id="9dab2-142">To have EF use your DbMappingViewCache you add use the DbMappingViewCacheTypeAttribute, specifying the context that it was created for.</span></span> <span data-ttu-id="9dab2-143">下列程式碼中，我們會結合 BlogContext MyMappingViewCache 類別。</span><span class="sxs-lookup"><span data-stu-id="9dab2-143">In the code below we associate the BlogContext with the MyMappingViewCache class.</span></span>

``` csharp
    [assembly: DbMappingViewCacheType(typeof(BlogContext), typeof(MyMappingViewCache))]
```

<span data-ttu-id="9dab2-144">針對更複雜的案例，可以藉由指定對應檢視快取 factory 提供對應檢視快取執行個體。</span><span class="sxs-lookup"><span data-stu-id="9dab2-144">For more complex scenarios, mapping view cache instances can be provided by specifying a mapping view cache factory.</span></span> <span data-ttu-id="9dab2-145">這可以藉由實作抽象類別 System.Data.Entity.Infrastructure.MappingViews.DbMappingViewCacheFactory 完成。</span><span class="sxs-lookup"><span data-stu-id="9dab2-145">This can be done by implementing the abstract class System.Data.Entity.Infrastructure.MappingViews.DbMappingViewCacheFactory.</span></span> <span data-ttu-id="9dab2-146">可擷取或設定使用 StorageMappingItemCollection.MappingViewCacheFactoryproperty 對應檢視快取 factory 所使用的執行個體。</span><span class="sxs-lookup"><span data-stu-id="9dab2-146">The instance of the mapping view cache factory that is used can be retrieved or set using the StorageMappingItemCollection.MappingViewCacheFactoryproperty.</span></span>
