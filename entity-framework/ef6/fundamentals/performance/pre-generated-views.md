---
title: 預先產生的對應視圖-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 917ba9c8-6ddf-4631-ab8c-c4fb378c2fcd
ms.openlocfilehash: 1fda9fe9638adce9b24a6b81aa081effeb0def81
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78419388"
---
# <a name="pre-generated-mapping-views"></a><span data-ttu-id="1b8b4-102">預先產生的對應視圖</span><span class="sxs-lookup"><span data-stu-id="1b8b4-102">Pre-generated mapping views</span></span>
<span data-ttu-id="1b8b4-103">在 Entity Framework 可以執行查詢或儲存變更至資料來源之前，它必須產生一組對應視圖來存取資料庫。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-103">Before the Entity Framework can execute a query or save changes to the data source, it must generate a set of mapping views to access the database.</span></span> <span data-ttu-id="1b8b4-104">這些對應視圖是以抽象方式表示資料庫的一組 Entity SQL 語句，而且是每個應用程式域快取的中繼資料的一部分。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-104">These mapping views are a set of Entity SQL statement that represent the database in an abstract way, and are part of the metadata which is cached per application domain.</span></span> <span data-ttu-id="1b8b4-105">如果您在相同的應用程式域中建立相同內容的多個實例，則會從快取的中繼資料重複使用對應視圖，而不是重新產生它們。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-105">If you create multiple instances of the same context in the same application domain, they will reuse mapping views from the cached metadata rather than regenerating them.</span></span> <span data-ttu-id="1b8b4-106">因為對應視圖產生是執行第一個查詢之整體成本的重要部分，所以 Entity Framework 可讓您預先產生對應視圖，並將它們包含在已編譯的專案中。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-106">Because mapping view generation is a significant part of the overall cost of executing the first query, the Entity Framework enables you to pre-generate mapping views and include them in the compiled project.</span></span><span data-ttu-id="1b8b4-107"> 如需詳細資訊，請參閱  [效能考慮（Entity Framework）](~/ef6/fundamentals/performance/perf-whitepaper.md)。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-107"> For more information, see  [Performance Considerations (Entity Framework)](~/ef6/fundamentals/performance/perf-whitepaper.md).</span></span>

## <a name="generating-mapping-views-with-the-ef-power-tools-community-edition"></a><span data-ttu-id="1b8b4-108">使用 EF Power Tools 的社區版本產生對應視圖</span><span class="sxs-lookup"><span data-stu-id="1b8b4-108">Generating Mapping Views with the EF Power Tools Community Edition</span></span>

<span data-ttu-id="1b8b4-109">預先產生視圖的最簡單方式是使用[EF Power Tools 的「社區版](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition)」。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-109">The easiest way to pre-generate views is to use the [EF Power Tools Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition).</span></span> <span data-ttu-id="1b8b4-110">安裝好 Power tool 之後，您將會有一個可產生視圖的功能表選項，如下所示。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-110">Once you have the Power Tools installed you will have a menu option to Generate Views, as below.</span></span>

-   <span data-ttu-id="1b8b4-111">針對**Code First**模型，以滑鼠右鍵按一下包含 DbCoNtext 類別的程式碼檔案。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-111">For **Code First** models right-click on the code file that contains your DbContext class.</span></span>
-   <span data-ttu-id="1b8b4-112">若是**EF Designer**模型，請以滑鼠右鍵按一下您的 EDMX 檔案。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-112">For **EF Designer** models right-click on your EDMX file.</span></span>

![產生視圖](~/ef6/media/generateviews.png)

<span data-ttu-id="1b8b4-114">當程式完成時，您將會有類似下列所產生的類別：</span><span class="sxs-lookup"><span data-stu-id="1b8b4-114">Once the process is finished you will have a class similar to the following generated</span></span>

![產生的視圖](~/ef6/media/generatedviews.png)

<span data-ttu-id="1b8b4-116">現在，當您執行應用程式時，EF 會使用此類別來視需要載入 views。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-116">Now when you run your application EF will use this class to load views as required.</span></span> <span data-ttu-id="1b8b4-117">如果您的模型變更，而您不會重新產生此類別，則 EF 會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-117">If your model changes and you do not re-generate this class then EF will throw an exception.</span></span>

## <a name="generating-mapping-views-from-code---ef6-onwards"></a><span data-ttu-id="1b8b4-118">從程式碼 EF6 開始產生對應視圖</span><span class="sxs-lookup"><span data-stu-id="1b8b4-118">Generating Mapping Views from Code - EF6 Onwards</span></span>

<span data-ttu-id="1b8b4-119">產生視圖的另一種方式是使用 EF 提供的 Api。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-119">The other way to generate views is to use the APIs that EF provides.</span></span> <span data-ttu-id="1b8b4-120">當您使用這個方法時，您可以自由地將這些視圖序列化，但是您也需要自行載入這些視圖。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-120">When using this method you have the freedom to serialize the views however you like, but you also need to load the views yourself.</span></span>

> [!NOTE]
> <span data-ttu-id="1b8b4-121">**僅限 EF6**後-本節中所顯示的 api 是在 Entity Framework 6 中引進。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-121">**EF6 Onwards Only** - The APIs shown in this section were introduced in Entity Framework 6.</span></span> <span data-ttu-id="1b8b4-122">如果您使用較舊的版本，則不適用此資訊。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-122">If you are using an earlier version this information does not apply.</span></span>

### <a name="generating-views"></a><span data-ttu-id="1b8b4-123">產生檢視</span><span class="sxs-lookup"><span data-stu-id="1b8b4-123">Generating Views</span></span>

<span data-ttu-id="1b8b4-124">用來產生視圖的 Api 位於 StorageMappingItemCollection 類別的上。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-124">The APIs to generate views are on the System.Data.Entity.Core.Mapping.StorageMappingItemCollection class.</span></span> <span data-ttu-id="1b8b4-125">您可以使用 ObjectCoNtext 的 MetadataWorkspace 來取得內容的 StorageMappingCollection。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-125">You can retrieve a StorageMappingCollection for a Context by using the MetadataWorkspace of an ObjectContext.</span></span> <span data-ttu-id="1b8b4-126">如果您使用較新的 DbCoNtext API，則可以使用 IObjectCoNtextAdapter （如下所示）來存取它，在此程式碼中，我們有一個衍生 DbCoNtext 的實例，稱為 dbCoNtext：</span><span class="sxs-lookup"><span data-stu-id="1b8b4-126">If you are using the newer DbContext API then you can access this by using the IObjectContextAdapter like below, in this code we have an instance of your derived DbContext called dbContext:</span></span>

``` csharp
    var objectContext = ((IObjectContextAdapter) dbContext).ObjectContext;
    var  mappingCollection = (StorageMappingItemCollection)objectContext.MetadataWorkspace
                                                                        .GetItemCollection(DataSpace.CSSpace);
```

<span data-ttu-id="1b8b4-127">一旦有了 StorageMappingItemCollection，您就可以取得 GenerateViews 和 ComputeMappingHashValue 方法的存取權。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-127">Once you have the StorageMappingItemCollection then you can get access to the GenerateViews and ComputeMappingHashValue methods.</span></span>

``` csharp
    public Dictionary\<EntitySetBase, DbMappingView> GenerateViews(IList<EdmSchemaError> errors)
    public string ComputeMappingHashValue()
```

<span data-ttu-id="1b8b4-128">第一個方法會建立字典，其中包含容器對應中每個視圖的專案。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-128">The first method creates a dictionary with an entry for each view in the container mapping.</span></span> <span data-ttu-id="1b8b4-129">第二個方法會計算單一容器對應的雜湊值，並在執行時間用來驗證在預先產生視圖之後，模型沒有變更。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-129">The second method computes a hash value for the single container mapping and is used at runtime to validate that the model has not changed since the views were pre-generated.</span></span> <span data-ttu-id="1b8b4-130">針對涉及多個容器對應的複雜案例，會提供兩個方法的覆寫。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-130">Overrides of the two methods are provided for complex scenarios involving multiple container mappings.</span></span>

<span data-ttu-id="1b8b4-131">產生 views 時，您會呼叫 GenerateViews 方法，然後寫出產生的 EntitySetBase 和 DbMappingView。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-131">When generating views you will call the GenerateViews method and then write out the resulting EntitySetBase and DbMappingView.</span></span> <span data-ttu-id="1b8b4-132">您也需要儲存 ComputeMappingHashValue 方法所產生的雜湊。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-132">You will also need to store the hash generated by the ComputeMappingHashValue method.</span></span>

### <a name="loading-views"></a><span data-ttu-id="1b8b4-133">載入視圖</span><span class="sxs-lookup"><span data-stu-id="1b8b4-133">Loading Views</span></span>

<span data-ttu-id="1b8b4-134">若要載入 GenerateViews 方法所產生的視圖，您可以提供 EF （繼承自 DbMappingViewCache 抽象類別的類別）。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-134">In order to load the views generated by the GenerateViews method, you can provide EF with a class that inherits from the DbMappingViewCache abstract class.</span></span> <span data-ttu-id="1b8b4-135">DbMappingViewCache 指定兩個您必須執行的方法：</span><span class="sxs-lookup"><span data-stu-id="1b8b4-135">DbMappingViewCache specifies two methods that you must implement:</span></span>

``` csharp
    public abstract string MappingHashValue { get; }
    public abstract DbMappingView GetView(EntitySetBase extent);
```

<span data-ttu-id="1b8b4-136">MappingHashValue 屬性必須傳回 ComputeMappingHashValue 方法所產生的雜湊。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-136">The MappingHashValue property must return the hash generated by the ComputeMappingHashValue method.</span></span> <span data-ttu-id="1b8b4-137">當 EF 要要求視圖時，它會先產生並比較模型的雜湊值與此屬性所傳回的雜湊。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-137">When EF is going to ask for views it will first generate and compare the hash value of the model with the hash returned by this property.</span></span> <span data-ttu-id="1b8b4-138">如果兩者不相符，則 EF 會擲回 EntityCommandCompilationException 例外狀況。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-138">If they do not match then EF will throw an EntityCommandCompilationException exception.</span></span>

<span data-ttu-id="1b8b4-139">GetView 方法將會接受 EntitySetBase，而您必須傳回 DbMappingVIew，其中包含在 EntitySetBase 方法所產生的字典中，與給定 GenerateViews 相關聯之所產生的 EntitySql。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-139">The GetView method will accept an EntitySetBase and you need to return a DbMappingVIew containing the EntitySql that was generated for that was associated with the given EntitySetBase in the dictionary generated by the GenerateViews method.</span></span> <span data-ttu-id="1b8b4-140">如果 EF 要求您不具有的視圖，則 GetView 應該會傳回 null。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-140">If EF asks for a view that you do not have then GetView should return null.</span></span>

<span data-ttu-id="1b8b4-141">以下是從如上面所述的 Power tool 所產生的 DbMappingViewCache 摘錄，在其中我們看到了一種儲存和取出所需 EntitySql 的方式。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-141">The following is an extract from the DbMappingViewCache that is generated with the Power Tools as described above, in it we see one way to store and retrieve the EntitySql required.</span></span>

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

<span data-ttu-id="1b8b4-142">若要讓 EF 使用您的 DbMappingViewCache，您所新增的會使用 DbMappingViewCacheTypeAttribute，並指定它所建立的內容。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-142">To have EF use your DbMappingViewCache you add use the DbMappingViewCacheTypeAttribute, specifying the context that it was created for.</span></span> <span data-ttu-id="1b8b4-143">在下面的程式碼中，我們將 BlogCoNtext 與 MyMappingViewCache 類別產生關聯。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-143">In the code below we associate the BlogContext with the MyMappingViewCache class.</span></span>

``` csharp
    [assembly: DbMappingViewCacheType(typeof(BlogContext), typeof(MyMappingViewCache))]
```

<span data-ttu-id="1b8b4-144">針對更複雜的案例，可以藉由指定對應視圖快取 factory 來提供對應視圖快取實例。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-144">For more complex scenarios, mapping view cache instances can be provided by specifying a mapping view cache factory.</span></span> <span data-ttu-id="1b8b4-145">這可以藉由實作為抽象類別 MappingViews 來完成。 DbMappingViewCacheFactory。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-145">This can be done by implementing the abstract class System.Data.Entity.Infrastructure.MappingViews.DbMappingViewCacheFactory.</span></span> <span data-ttu-id="1b8b4-146">您可以使用 StorageMappingItemCollection. MappingViewCacheFactoryproperty 來抓取或設定所使用之對應視圖快取 factory 的實例。</span><span class="sxs-lookup"><span data-stu-id="1b8b4-146">The instance of the mapping view cache factory that is used can be retrieved or set using the StorageMappingItemCollection.MappingViewCacheFactoryproperty.</span></span>
