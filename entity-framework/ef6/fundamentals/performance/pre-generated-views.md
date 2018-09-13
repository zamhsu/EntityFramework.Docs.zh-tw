---
title: 預先產生的對應檢視-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 917ba9c8-6ddf-4631-ab8c-c4fb378c2fcd
ms.openlocfilehash: cb374d007252710b42c31061bf15d7d32af0db27
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45489241"
---
# <a name="pre-generated-mapping-views"></a>預先產生的對應檢視
Entity Framework 可以執行查詢，或將變更儲存至資料來源之前，它必須產生一組對應檢視，來存取資料庫。 這些對應檢視是一組以抽象方式代表資料庫的 Entity SQL 陳述式，中繼資料的快取每個應用程式網域的一部分。 如果您在相同的應用程式定義域中建立多個相同的內容執行個體，它們會重複使用從快取的中繼資料，而不是重新產生它們的對應檢視。 對應檢視表產生是執行第一個查詢的整體成本的重要部分，因為 Entity Framework 可讓您預先產生對應檢視，並將它們包含在已編譯的專案。 如需詳細資訊，請參閱 <<c0> [ 效能考量 (Entity Framework)](~/ef6/fundamentals/performance/perf-whitepaper.md)。

## <a name="generating-mapping-views-with-the-ef-power-tools"></a>產生對應 EF Power tools 檢視

預先產生檢視表的最簡單方式是使用[EF Power Tools](http://visualstudiogallery.msdn.microsoft.com/72a60b14-1581-4b9b-89f2-846072eff19d)。 一旦您已安裝的 Power Tools 就會產生檢視表的功能表選項，如下所示。

-   針對**Code First**模型以滑鼠右鍵按一下包含您的 DbContext 類別的程式碼檔案。
-   針對**EF 設計工具**模型以滑鼠右鍵按一下您的 EDMX 檔案。

![產生檢視](~/ef6/media/generateviews.png)

一旦完成此程序就會產生如下所示的類別

![產生的檢視表](~/ef6/media/generatedviews.png)

現在當您執行 EF 應用程式會使用此類別來載入所需的檢視。 如果您的模型變更，而且您不重新產生這個類別 EF 會擲回例外狀況。

## <a name="generating-mapping-views-from-code---ef6-onwards"></a>從程式碼-從 EF6 開始產生的對應檢視

產生檢視的其他方式是使用 EF 所提供的 Api。 使用此方法時，您可以自由序列化的檢視，不過您喜歡，但您也需要載入您自己的檢視。

> [!NOTE]
> **EF6 及更新版本僅**-Entity Framework 6 中導入的 Api，這一節所示。 如果您使用這項資訊不適用於較早版本。

### <a name="generating-views"></a>產生檢視

使用 Api 來產生檢視表位於 System.Data.Entity.Core.Mapping.StorageMappingItemCollection 類別。 您可以使用 ObjectContext 的 MetadataWorkspace 內容的檔案，以擷取 StorageMappingCollection。 如果您使用較新的 DbContext API 時，則您可以使用來存取這 IObjectContextAdapter 如下所示，在這段程式碼中，我們有您呼叫 dbContext 的衍生 DbContext 的執行個體：

``` csharp
    var objectContext = ((IObjectContextAdapter) dbContext).ObjectContext;
    var  mappingCollection = (StorageMappingItemCollection)objectContext.MetadataWorkspace
                                                                        .GetItemCollection(DataSpace.CSSpace);
```

一旦您擁有 storagemappingitemcollection 由您就可以取得 GenerateViews 和 ComputeMappingHashValue 方法的存取。

``` csharp
    public Dictionary\<EntitySetBase, DbMappingView> GenerateViews(IList<EdmSchemaError> errors)
    public string ComputeMappingHashValue()
```

第一種方法會建立字典的項目，為每個容器對應中的檢視。 第二種方法計算單一容器對應的雜湊值，並用於執行階段驗證的模型已不會變更因為預先產生檢視。 涉及多個容器對應的複雜案例提供兩種方法的覆寫。

產生檢視時將呼叫 GenerateViews; 方法，並接著寫出產生的 EntitySetBase 和 DbMappingView。 您也必須儲存 ComputeMappingHashValue 方法所產生的雜湊。

### <a name="loading-views"></a>正在載入檢視

若要載入 GenerateViews 方法所產生的檢視，您可以提供 EF DbMappingViewCache 抽象類別繼承的類別。 DbMappingViewCache 指定必須實作的兩個方法：

``` csharp
    public abstract string MappingHashValue { get; }
    public abstract DbMappingView GetView(EntitySetBase extent);
```

MappingHashValue 屬性必須傳回 ComputeMappingHashValue 方法所產生的雜湊。 EF 時要要求檢視它會先產生，並比較該模型的雜湊值與這個屬性所傳回的雜湊。 如果兩者不符 EF 會擲回 EntityCommandCompilationException 例外狀況。

GetView 方法會接受 EntitySetBase，而且您需要傳回包含所產生的 EntitySql DbMappingVIew 已 GenerateViews 方法所產生字典中指定的 EntitySetBase 相關聯。 如果 EF 要求提供的檢視，您不需要再 GetView 應該傳回 null。

以下是擷取自 DbMappingViewCache 上面所述，在其中儲存和擷取所需的 EntitySql 一種方法，我們看到 Power tools 產生。

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

若要讓 EF 使用您新增您 DbMappingViewCache 會使用 DbMappingViewCacheTypeAttribute，指定為建立的內容。 下列程式碼中，我們會結合 BlogContext MyMappingViewCache 類別。

``` csharp
    [assembly: DbMappingViewCacheType(typeof(BlogContext), typeof(MyMappingViewCache))]
```

針對更複雜的案例，可以藉由指定對應檢視快取 factory 提供對應檢視快取執行個體。 這可以藉由實作抽象類別 System.Data.Entity.Infrastructure.MappingViews.DbMappingViewCacheFactory 完成。 可擷取或設定使用 StorageMappingItemCollection.MappingViewCacheFactoryproperty 對應檢視快取 factory 所使用的執行個體。
