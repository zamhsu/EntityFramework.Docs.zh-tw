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
# <a name="pre-generated-mapping-views"></a>預先產生的對應視圖
在 Entity Framework 可以執行查詢或儲存變更至資料來源之前，它必須產生一組對應視圖來存取資料庫。 這些對應視圖是以抽象方式表示資料庫的一組 Entity SQL 語句，而且是每個應用程式域快取的中繼資料的一部分。 如果您在相同的應用程式域中建立相同內容的多個實例，則會從快取的中繼資料重複使用對應視圖，而不是重新產生它們。 因為對應視圖產生是執行第一個查詢之整體成本的重要部分，所以 Entity Framework 可讓您預先產生對應視圖，並將它們包含在已編譯的專案中。 如需詳細資訊，請參閱  [效能考慮（Entity Framework）](~/ef6/fundamentals/performance/perf-whitepaper.md)。

## <a name="generating-mapping-views-with-the-ef-power-tools-community-edition"></a>使用 EF Power Tools 的社區版本產生對應視圖

預先產生視圖的最簡單方式是使用[EF Power Tools 的「社區版](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition)」。 安裝好 Power tool 之後，您將會有一個可產生視圖的功能表選項，如下所示。

-   針對**Code First**模型，以滑鼠右鍵按一下包含 DbCoNtext 類別的程式碼檔案。
-   若是**EF Designer**模型，請以滑鼠右鍵按一下您的 EDMX 檔案。

![產生視圖](~/ef6/media/generateviews.png)

當程式完成時，您將會有類似下列所產生的類別：

![產生的視圖](~/ef6/media/generatedviews.png)

現在，當您執行應用程式時，EF 會使用此類別來視需要載入 views。 如果您的模型變更，而您不會重新產生此類別，則 EF 會擲回例外狀況。

## <a name="generating-mapping-views-from-code---ef6-onwards"></a>從程式碼 EF6 開始產生對應視圖

產生視圖的另一種方式是使用 EF 提供的 Api。 當您使用這個方法時，您可以自由地將這些視圖序列化，但是您也需要自行載入這些視圖。

> [!NOTE]
> **僅限 EF6**後-本節中所顯示的 api 是在 Entity Framework 6 中引進。 如果您使用較舊的版本，則不適用此資訊。

### <a name="generating-views"></a>產生檢視

用來產生視圖的 Api 位於 StorageMappingItemCollection 類別的上。 您可以使用 ObjectCoNtext 的 MetadataWorkspace 來取得內容的 StorageMappingCollection。 如果您使用較新的 DbCoNtext API，則可以使用 IObjectCoNtextAdapter （如下所示）來存取它，在此程式碼中，我們有一個衍生 DbCoNtext 的實例，稱為 dbCoNtext：

``` csharp
    var objectContext = ((IObjectContextAdapter) dbContext).ObjectContext;
    var  mappingCollection = (StorageMappingItemCollection)objectContext.MetadataWorkspace
                                                                        .GetItemCollection(DataSpace.CSSpace);
```

一旦有了 StorageMappingItemCollection，您就可以取得 GenerateViews 和 ComputeMappingHashValue 方法的存取權。

``` csharp
    public Dictionary\<EntitySetBase, DbMappingView> GenerateViews(IList<EdmSchemaError> errors)
    public string ComputeMappingHashValue()
```

第一個方法會建立字典，其中包含容器對應中每個視圖的專案。 第二個方法會計算單一容器對應的雜湊值，並在執行時間用來驗證在預先產生視圖之後，模型沒有變更。 針對涉及多個容器對應的複雜案例，會提供兩個方法的覆寫。

產生 views 時，您會呼叫 GenerateViews 方法，然後寫出產生的 EntitySetBase 和 DbMappingView。 您也需要儲存 ComputeMappingHashValue 方法所產生的雜湊。

### <a name="loading-views"></a>載入視圖

若要載入 GenerateViews 方法所產生的視圖，您可以提供 EF （繼承自 DbMappingViewCache 抽象類別的類別）。 DbMappingViewCache 指定兩個您必須執行的方法：

``` csharp
    public abstract string MappingHashValue { get; }
    public abstract DbMappingView GetView(EntitySetBase extent);
```

MappingHashValue 屬性必須傳回 ComputeMappingHashValue 方法所產生的雜湊。 當 EF 要要求視圖時，它會先產生並比較模型的雜湊值與此屬性所傳回的雜湊。 如果兩者不相符，則 EF 會擲回 EntityCommandCompilationException 例外狀況。

GetView 方法將會接受 EntitySetBase，而您必須傳回 DbMappingVIew，其中包含在 EntitySetBase 方法所產生的字典中，與給定 GenerateViews 相關聯之所產生的 EntitySql。 如果 EF 要求您不具有的視圖，則 GetView 應該會傳回 null。

以下是從如上面所述的 Power tool 所產生的 DbMappingViewCache 摘錄，在其中我們看到了一種儲存和取出所需 EntitySql 的方式。

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

若要讓 EF 使用您的 DbMappingViewCache，您所新增的會使用 DbMappingViewCacheTypeAttribute，並指定它所建立的內容。 在下面的程式碼中，我們將 BlogCoNtext 與 MyMappingViewCache 類別產生關聯。

``` csharp
    [assembly: DbMappingViewCacheType(typeof(BlogContext), typeof(MyMappingViewCache))]
```

針對更複雜的案例，可以藉由指定對應視圖快取 factory 來提供對應視圖快取實例。 這可以藉由實作為抽象類別 MappingViews 來完成。 DbMappingViewCacheFactory。 您可以使用 StorageMappingItemCollection. MappingViewCacheFactoryproperty 來抓取或設定所使用之對應視圖快取 factory 的實例。
