---
title: 預先產生的對應視圖-EF6
description: Entity Framework 6 中預先產生的對應視圖
author: divega
ms.date: 10/23/2016
ms.assetid: 917ba9c8-6ddf-4631-ab8c-c4fb378c2fcd
uid: ef6/fundamentals/performance/pre-generated-views
ms.openlocfilehash: 8318030603339c45c5c8459db580b7a8de5febda
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89615862"
---
# <a name="pre-generated-mapping-views"></a><span data-ttu-id="8fc52-103">預先產生的對應視圖</span><span class="sxs-lookup"><span data-stu-id="8fc52-103">Pre-generated mapping views</span></span>
<span data-ttu-id="8fc52-104">在 Entity Framework 可以執行查詢或將變更儲存至資料來源之前，必須先產生一組對應視圖來存取資料庫。</span><span class="sxs-lookup"><span data-stu-id="8fc52-104">Before the Entity Framework can execute a query or save changes to the data source, it must generate a set of mapping views to access the database.</span></span> <span data-ttu-id="8fc52-105">這些對應視圖是一組 Entity SQL 語句，以抽象的方式表示資料庫，而且是每個應用程式域所快取之中繼資料的一部分。</span><span class="sxs-lookup"><span data-stu-id="8fc52-105">These mapping views are a set of Entity SQL statement that represent the database in an abstract way, and are part of the metadata which is cached per application domain.</span></span> <span data-ttu-id="8fc52-106">如果您在相同的應用程式域中建立相同內容的多個實例，則會使用來自快取中繼資料的對應視圖，而不是重新產生它們。</span><span class="sxs-lookup"><span data-stu-id="8fc52-106">If you create multiple instances of the same context in the same application domain, they will reuse mapping views from the cached metadata rather than regenerating them.</span></span> <span data-ttu-id="8fc52-107">因為對應視圖產生是執行第一個查詢之整體成本的重要部分，Entity Framework 可讓您預先產生對應視圖，並將它們包含在已編譯的專案中。</span><span class="sxs-lookup"><span data-stu-id="8fc52-107">Because mapping view generation is a significant part of the overall cost of executing the first query, the Entity Framework enables you to pre-generate mapping views and include them in the compiled project.</span></span><span data-ttu-id="8fc52-108">如需詳細資訊，請參閱  [ (Entity Framework) 的效能考慮 ](xref:ef6/fundamentals/performance/perf-whitepaper)。</span><span class="sxs-lookup"><span data-stu-id="8fc52-108"> For more information, see  [Performance Considerations (Entity Framework)](xref:ef6/fundamentals/performance/perf-whitepaper).</span></span>

## <a name="generating-mapping-views-with-the-ef-power-tools-community-edition"></a><span data-ttu-id="8fc52-109">使用 EF Power Tools 社區版本產生對應視圖</span><span class="sxs-lookup"><span data-stu-id="8fc52-109">Generating Mapping Views with the EF Power Tools Community Edition</span></span>

<span data-ttu-id="8fc52-110">預先產生視圖的最簡單方式是使用 [EF Power Tools 社區版](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition)。</span><span class="sxs-lookup"><span data-stu-id="8fc52-110">The easiest way to pre-generate views is to use the [EF Power Tools Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition).</span></span> <span data-ttu-id="8fc52-111">安裝 Power Tools 之後，您將會有一個可產生視圖的功能表選項，如下所示。</span><span class="sxs-lookup"><span data-stu-id="8fc52-111">Once you have the Power Tools installed you will have a menu option to Generate Views, as below.</span></span>

-   <span data-ttu-id="8fc52-112">針對 **Code First** 模型，以滑鼠右鍵按一下包含 DbCoNtext 類別的程式碼檔案。</span><span class="sxs-lookup"><span data-stu-id="8fc52-112">For **Code First** models right-click on the code file that contains your DbContext class.</span></span>
-   <span data-ttu-id="8fc52-113">針對 **EF 設計** 工具模型，以滑鼠右鍵按一下您的 EDMX 檔案。</span><span class="sxs-lookup"><span data-stu-id="8fc52-113">For **EF Designer** models right-click on your EDMX file.</span></span>

![產生視圖](~/ef6/media/generateviews.png)

<span data-ttu-id="8fc52-115">程式完成後，您將會有類似下列所產生的類別：</span><span class="sxs-lookup"><span data-stu-id="8fc52-115">Once the process is finished you will have a class similar to the following generated</span></span>

![產生的視圖](~/ef6/media/generatedviews.png)

<span data-ttu-id="8fc52-117">現在，當您執行應用程式時，EF 將會使用這個類別來視需要載入 views。</span><span class="sxs-lookup"><span data-stu-id="8fc52-117">Now when you run your application EF will use this class to load views as required.</span></span> <span data-ttu-id="8fc52-118">如果您的模型變更，而您未重新產生此類別，則 EF 會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="8fc52-118">If your model changes and you do not re-generate this class then EF will throw an exception.</span></span>

## <a name="generating-mapping-views-from-code---ef6-onwards"></a><span data-ttu-id="8fc52-119">從程式碼 EF6 開始產生對應視圖</span><span class="sxs-lookup"><span data-stu-id="8fc52-119">Generating Mapping Views from Code - EF6 Onwards</span></span>

<span data-ttu-id="8fc52-120">產生視圖的另一種方式是使用 EF 提供的 Api。</span><span class="sxs-lookup"><span data-stu-id="8fc52-120">The other way to generate views is to use the APIs that EF provides.</span></span> <span data-ttu-id="8fc52-121">使用這個方法時，您可以自由地將這些視圖序列化，但您也需要自行載入這些視圖。</span><span class="sxs-lookup"><span data-stu-id="8fc52-121">When using this method you have the freedom to serialize the views however you like, but you also need to load the views yourself.</span></span>

> [!NOTE]
> <span data-ttu-id="8fc52-122">**僅限 EF6** ：本章節中所顯示的 api 已于 Entity Framework 6 中引進。</span><span class="sxs-lookup"><span data-stu-id="8fc52-122">**EF6 Onwards Only** - The APIs shown in this section were introduced in Entity Framework 6.</span></span> <span data-ttu-id="8fc52-123">如果您使用的是較舊的版本，則不適用此資訊。</span><span class="sxs-lookup"><span data-stu-id="8fc52-123">If you are using an earlier version this information does not apply.</span></span>

### <a name="generating-views"></a><span data-ttu-id="8fc52-124">產生檢視</span><span class="sxs-lookup"><span data-stu-id="8fc52-124">Generating Views</span></span>

<span data-ttu-id="8fc52-125">用來產生視圖的 Api 位於 StorageMappingItemCollection 類別的介面上。</span><span class="sxs-lookup"><span data-stu-id="8fc52-125">The APIs to generate views are on the System.Data.Entity.Core.Mapping.StorageMappingItemCollection class.</span></span> <span data-ttu-id="8fc52-126">您可以使用 ObjectCoNtext 的 MetadataWorkspace 來取得內容的 StorageMappingCollection。</span><span class="sxs-lookup"><span data-stu-id="8fc52-126">You can retrieve a StorageMappingCollection for a Context by using the MetadataWorkspace of an ObjectContext.</span></span> <span data-ttu-id="8fc52-127">如果您使用較新的 DbCoNtext API，則可以使用如下所示的 IObjectCoNtextAdapter 來存取此程式碼，在此程式碼中，我們有一個衍生 DbCoNtext 的實例，稱為 dbCoNtext：</span><span class="sxs-lookup"><span data-stu-id="8fc52-127">If you are using the newer DbContext API then you can access this by using the IObjectContextAdapter like below, in this code we have an instance of your derived DbContext called dbContext:</span></span>

``` csharp
    var objectContext = ((IObjectContextAdapter) dbContext).ObjectContext;
    var  mappingCollection = (StorageMappingItemCollection)objectContext.MetadataWorkspace
                                                                        .GetItemCollection(DataSpace.CSSpace);
```

<span data-ttu-id="8fc52-128">StorageMappingItemCollection 之後，您就可以存取 GenerateViews 和 ComputeMappingHashValue 方法。</span><span class="sxs-lookup"><span data-stu-id="8fc52-128">Once you have the StorageMappingItemCollection then you can get access to the GenerateViews and ComputeMappingHashValue methods.</span></span>

``` csharp
    public Dictionary\<EntitySetBase, DbMappingView> GenerateViews(IList<EdmSchemaError> errors)
    public string ComputeMappingHashValue()
```

<span data-ttu-id="8fc52-129">第一個方法會針對容器對應中的每個視圖建立具有專案的字典。</span><span class="sxs-lookup"><span data-stu-id="8fc52-129">The first method creates a dictionary with an entry for each view in the container mapping.</span></span> <span data-ttu-id="8fc52-130">第二個方法會計算單一容器對應的雜湊值，並在執行時間用來驗證模型自從預先產生之前未變更。</span><span class="sxs-lookup"><span data-stu-id="8fc52-130">The second method computes a hash value for the single container mapping and is used at runtime to validate that the model has not changed since the views were pre-generated.</span></span> <span data-ttu-id="8fc52-131">這兩種方法的覆寫是針對牽涉到多個容器對應的複雜案例而提供。</span><span class="sxs-lookup"><span data-stu-id="8fc52-131">Overrides of the two methods are provided for complex scenarios involving multiple container mappings.</span></span>

<span data-ttu-id="8fc52-132">產生視圖時，您會呼叫 GenerateViews 方法，然後寫出產生的 EntitySetBase 和 DbMappingView。</span><span class="sxs-lookup"><span data-stu-id="8fc52-132">When generating views you will call the GenerateViews method and then write out the resulting EntitySetBase and DbMappingView.</span></span> <span data-ttu-id="8fc52-133">您也需要儲存 ComputeMappingHashValue 方法所產生的雜湊。</span><span class="sxs-lookup"><span data-stu-id="8fc52-133">You will also need to store the hash generated by the ComputeMappingHashValue method.</span></span>

### <a name="loading-views"></a><span data-ttu-id="8fc52-134">正在載入視圖</span><span class="sxs-lookup"><span data-stu-id="8fc52-134">Loading Views</span></span>

<span data-ttu-id="8fc52-135">為了載入 GenerateViews 方法所產生的視圖，您可以提供 EF 與繼承自 DbMappingViewCache 抽象類別的類別。</span><span class="sxs-lookup"><span data-stu-id="8fc52-135">In order to load the views generated by the GenerateViews method, you can provide EF with a class that inherits from the DbMappingViewCache abstract class.</span></span> <span data-ttu-id="8fc52-136">DbMappingViewCache 指定您必須執行的兩個方法：</span><span class="sxs-lookup"><span data-stu-id="8fc52-136">DbMappingViewCache specifies two methods that you must implement:</span></span>

``` csharp
    public abstract string MappingHashValue { get; }
    public abstract DbMappingView GetView(EntitySetBase extent);
```

<span data-ttu-id="8fc52-137">MappingHashValue 屬性必須傳回 ComputeMappingHashValue 方法所產生的雜湊。</span><span class="sxs-lookup"><span data-stu-id="8fc52-137">The MappingHashValue property must return the hash generated by the ComputeMappingHashValue method.</span></span> <span data-ttu-id="8fc52-138">當 EF 即將要求視圖時，它會先產生並比較模型的雜湊值與這個屬性所傳回的雜湊。</span><span class="sxs-lookup"><span data-stu-id="8fc52-138">When EF is going to ask for views it will first generate and compare the hash value of the model with the hash returned by this property.</span></span> <span data-ttu-id="8fc52-139">如果不相符，EF 會擲回 EntityCommandCompilationException 例外狀況。</span><span class="sxs-lookup"><span data-stu-id="8fc52-139">If they do not match then EF will throw an EntityCommandCompilationException exception.</span></span>

<span data-ttu-id="8fc52-140">GetView 方法會接受 EntitySetBase，而您必須傳回 DbMappingVIew，其中包含在 EntitySetBase 方法所產生的字典中與指定 GenerateViews 相關聯的 EntitySql。</span><span class="sxs-lookup"><span data-stu-id="8fc52-140">The GetView method will accept an EntitySetBase and you need to return a DbMappingVIew containing the EntitySql that was generated for that was associated with the given EntitySetBase in the dictionary generated by the GenerateViews method.</span></span> <span data-ttu-id="8fc52-141">如果 EF 要求您沒有的視圖，則 GetView 應該會傳回 null。</span><span class="sxs-lookup"><span data-stu-id="8fc52-141">If EF asks for a view that you do not have then GetView should return null.</span></span>

<span data-ttu-id="8fc52-142">以下是使用 Power Tools 所產生之 DbMappingViewCache 的解壓縮，如上面所述，我們會在其中看到儲存和取出所需 EntitySql 的方式。</span><span class="sxs-lookup"><span data-stu-id="8fc52-142">The following is an extract from the DbMappingViewCache that is generated with the Power Tools as described above, in it we see one way to store and retrieve the EntitySql required.</span></span>

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

<span data-ttu-id="8fc52-143">若要讓 EF 使用您的 DbMappingViewCache，您可以使用 DbMappingViewCacheTypeAttribute 來指定為其建立的內容。</span><span class="sxs-lookup"><span data-stu-id="8fc52-143">To have EF use your DbMappingViewCache you add use the DbMappingViewCacheTypeAttribute, specifying the context that it was created for.</span></span> <span data-ttu-id="8fc52-144">在下列程式碼中，我們會將 BlogCoNtext 與 MyMappingViewCache 類別建立關聯。</span><span class="sxs-lookup"><span data-stu-id="8fc52-144">In the code below we associate the BlogContext with the MyMappingViewCache class.</span></span>

``` csharp
    [assembly: DbMappingViewCacheType(typeof(BlogContext), typeof(MyMappingViewCache))]
```

<span data-ttu-id="8fc52-145">針對更複雜的案例，可以藉由指定對應視圖快取處理站來提供對應視圖快取實例。</span><span class="sxs-lookup"><span data-stu-id="8fc52-145">For more complex scenarios, mapping view cache instances can be provided by specifying a mapping view cache factory.</span></span> <span data-ttu-id="8fc52-146">您可以藉由實 MappingViews. DbMappingViewCacheFactory 來完成這項工作。</span><span class="sxs-lookup"><span data-stu-id="8fc52-146">This can be done by implementing the abstract class System.Data.Entity.Infrastructure.MappingViews.DbMappingViewCacheFactory.</span></span> <span data-ttu-id="8fc52-147">您可以使用 StorageMappingItemCollection MappingViewCacheFactoryproperty 來抓取或設定所使用之對應 view cache factory 的實例。</span><span class="sxs-lookup"><span data-stu-id="8fc52-147">The instance of the mapping view cache factory that is used can be retrieved or set using the StorageMappingItemCollection.MappingViewCacheFactoryproperty.</span></span>
