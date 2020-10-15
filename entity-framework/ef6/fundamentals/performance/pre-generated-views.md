---
title: 預先產生的對應視圖-EF6
description: Entity Framework 6 中預先產生的對應視圖
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/performance/pre-generated-views
ms.openlocfilehash: 0c698e50f5c747d9953d9cf55dbbd8824ff8126d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062954"
---
# <a name="pre-generated-mapping-views"></a>預先產生的對應視圖
在 Entity Framework 可以執行查詢或將變更儲存至資料來源之前，必須先產生一組對應視圖來存取資料庫。 這些對應視圖是一組 Entity SQL 語句，以抽象的方式表示資料庫，而且是每個應用程式域所快取之中繼資料的一部分。 如果您在相同的應用程式域中建立相同內容的多個實例，則會使用來自快取中繼資料的對應視圖，而不是重新產生它們。 因為對應視圖產生是執行第一個查詢之整體成本的重要部分，Entity Framework 可讓您預先產生對應視圖，並將它們包含在已編譯的專案中。如需詳細資訊，請參閱  [ (Entity Framework) 的效能考慮 ](xref:ef6/fundamentals/performance/perf-whitepaper)。

## <a name="generating-mapping-views-with-the-ef-power-tools-community-edition"></a>使用 EF Power Tools 社區版本產生對應視圖

預先產生視圖的最簡單方式是使用 [EF Power Tools 社區版](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition)。 安裝 Power Tools 之後，您將會有一個可產生視圖的功能表選項，如下所示。

-   針對 **Code First** 模型，以滑鼠右鍵按一下包含 DbCoNtext 類別的程式碼檔案。
-   針對 **EF 設計** 工具模型，以滑鼠右鍵按一下您的 EDMX 檔案。

![產生視圖](~/ef6/media/generateviews.png)

程式完成後，您將會有類似下列所產生的類別：

![產生的視圖](~/ef6/media/generatedviews.png)

現在，當您執行應用程式時，EF 將會使用這個類別來視需要載入 views。 如果您的模型變更，而您未重新產生此類別，則 EF 會擲回例外狀況。

## <a name="generating-mapping-views-from-code---ef6-onwards"></a>從程式碼 EF6 開始產生對應視圖

產生視圖的另一種方式是使用 EF 提供的 Api。 使用這個方法時，您可以自由地將這些視圖序列化，但您也需要自行載入這些視圖。

> [!NOTE]
> **僅限 EF6** ：本章節中所顯示的 api 已于 Entity Framework 6 中引進。 如果您使用的是較舊的版本，則不適用此資訊。

### <a name="generating-views"></a>產生檢視

用來產生視圖的 Api 位於 StorageMappingItemCollection 類別的介面上。 您可以使用 ObjectCoNtext 的 MetadataWorkspace 來取得內容的 StorageMappingCollection。 如果您使用較新的 DbCoNtext API，則可以使用如下所示的 IObjectCoNtextAdapter 來存取此程式碼，在此程式碼中，我們有一個衍生 DbCoNtext 的實例，稱為 dbCoNtext：

``` csharp
    var objectContext = ((IObjectContextAdapter) dbContext).ObjectContext;
    var  mappingCollection = (StorageMappingItemCollection)objectContext.MetadataWorkspace
                                                                        .GetItemCollection(DataSpace.CSSpace);
```

StorageMappingItemCollection 之後，您就可以存取 GenerateViews 和 ComputeMappingHashValue 方法。

``` csharp
    public Dictionary\<EntitySetBase, DbMappingView> GenerateViews(IList<EdmSchemaError> errors)
    public string ComputeMappingHashValue()
```

第一個方法會針對容器對應中的每個視圖建立具有專案的字典。 第二個方法會計算單一容器對應的雜湊值，並在執行時間用來驗證模型自從預先產生之前未變更。 這兩種方法的覆寫是針對牽涉到多個容器對應的複雜案例而提供。

產生視圖時，您會呼叫 GenerateViews 方法，然後寫出產生的 EntitySetBase 和 DbMappingView。 您也需要儲存 ComputeMappingHashValue 方法所產生的雜湊。

### <a name="loading-views"></a>正在載入視圖

為了載入 GenerateViews 方法所產生的視圖，您可以提供 EF 與繼承自 DbMappingViewCache 抽象類別的類別。 DbMappingViewCache 指定您必須執行的兩個方法：

``` csharp
    public abstract string MappingHashValue { get; }
    public abstract DbMappingView GetView(EntitySetBase extent);
```

MappingHashValue 屬性必須傳回 ComputeMappingHashValue 方法所產生的雜湊。 當 EF 即將要求視圖時，它會先產生並比較模型的雜湊值與這個屬性所傳回的雜湊。 如果不相符，EF 會擲回 EntityCommandCompilationException 例外狀況。

GetView 方法會接受 EntitySetBase，而您必須傳回 DbMappingVIew，其中包含在 EntitySetBase 方法所產生的字典中與指定 GenerateViews 相關聯的 EntitySql。 如果 EF 要求您沒有的視圖，則 GetView 應該會傳回 null。

以下是使用 Power Tools 所產生之 DbMappingViewCache 的解壓縮，如上面所述，我們會在其中看到儲存和取出所需 EntitySql 的方式。

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

若要讓 EF 使用您的 DbMappingViewCache，您可以使用 DbMappingViewCacheTypeAttribute 來指定為其建立的內容。 在下列程式碼中，我們會將 BlogCoNtext 與 MyMappingViewCache 類別建立關聯。

``` csharp
    [assembly: DbMappingViewCacheType(typeof(BlogContext), typeof(MyMappingViewCache))]
```

針對更複雜的案例，可以藉由指定對應視圖快取處理站來提供對應視圖快取實例。 您可以藉由實 MappingViews. DbMappingViewCacheFactory 來完成這項工作。 您可以使用 StorageMappingItemCollection MappingViewCacheFactoryproperty 來抓取或設定所使用之對應 view cache factory 的實例。
