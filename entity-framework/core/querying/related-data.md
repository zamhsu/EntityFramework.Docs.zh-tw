---
title: "正在載入相關的資料-EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
ms.technology: entity-framework-core
uid: core/querying/related-data
ms.openlocfilehash: ec69bb128890a1e0b72fe77014f37747585bb5a5
ms.sourcegitcommit: 3b21a7fdeddc7b3c70d9b7777b72bef61f59216c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2018
---
# <a name="loading-related-data"></a>載入相關的資料

Entity Framework Core 可讓您在模型中使用的導覽屬性，來載入相關的實體。 有三種常見的 O/RM 模式來載入相關的資料。
* **積極式載入**表示相關的資料從資料庫載入做為初始查詢的一部分。
* **明確式載入**表示相關的資料會明確地載入從資料庫在稍後。
* **消極式載入**表示，相關的資料會以透明的方式從資料庫載入時存取導覽屬性。 消極式載入尚無法使用 EF 核心。

> [!TIP]  
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) \(英文\)。

## <a name="eager-loading"></a>積極式載入

您可以使用`Include`方法，以指定要包含在查詢結果的相關的資料。 在下列範例中，會在結果中傳回部落格會有其`Posts`屬性填入相關的文章。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]  
> 實體 Framework Core 將會自動修正向上導覽屬性與先前已載入至內容執行個體的任何其他實體。 因此即使沒有明確加入的資料瀏覽屬性，屬性可能仍然會填入某些或所有相關的項目先前已載入。


您可以在單一查詢中包含多個關聯性中的相關的資料。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a>包括多個層級

您可以向下鑽研到包含多個層級的相關的資料所使用的關聯性`ThenInclude`方法。 下列範例會載入所有的部落格、 其相關的文章，每次張貼的作者。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#SingleThenInclude)]

> [!NOTE]  
> 目前版本的 Visual Studio 提供不正確的程式碼完成的選項，而且可能會導致標示有語法錯誤時使用的正確運算式`ThenInclude`之後集合導覽屬性的方法。 這是在 https://github.com/dotnet/roslyn/issues/8237 追蹤 IntelliSense 問題的徵兆。 它可以安全地忽略這些假性的語法錯誤，只要程式碼正確，而且可以成功編譯。 

您可以多個呼叫鏈結到`ThenInclude`繼續進一步包括層級的相關資料。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

您可以結合這些全部都在相同查詢中包含相關的資料從多個層級和多個根節點。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#IncludeTree)]

若要包含的其中一個實體所包含的多個相關的實體。 例如，當查詢`Blog`s，包括`Posts`接著又想將同時包含`Author`和`Tags`的`Posts`。 若要這樣做，您要指定每個包含從根目錄開始的路徑。 例如，`Blog -> Posts -> Author`和`Blog -> Posts -> Tags`。 這不表示您會收到多餘的聯結，在大部分情況下，將會合併 EF 聯結產生 SQL 時。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

### <a name="ignored-includes"></a>忽略包含

如果您變更查詢，使它不會再傳回查詢開始的實體類型的執行個體時，會忽略 include 運算子。

在下列範例中，包含運算子會根據`Blog`，但然後`Select`運算子用來變更查詢以傳回匿名型別。 在此情況下，包含運算子會有任何作用。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#IgnoredInclude)]

根據預設，EF 核心會記錄警告時包含運算子都會被忽略。 請參閱[記錄](../miscellaneous/logging.md)檢視記錄輸出的詳細資訊。 對擲回或不執行任何動作，則會忽略 include 運算子時，您可以變更行為。 這是通常在設定為您的內容-選項時`DbContext.OnConfiguring`，或在`Startup.cs`如果您使用 ASP.NET Core。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/ThrowOnIgnoredInclude/BloggingContext.cs#OnConfiguring)]

## <a name="explicit-loading"></a>明確式載入

> [!NOTE]  
> 在 EF 核心 1.1 已引入此功能。

您可以明確載入的導覽屬性，透過`DbContext.Entry(...)`應用程式開發介面。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#Eager)]

您也會明確可以藉由執行不同的查詢會傳回相關的實體載入導覽屬性。 如果已啟用變更追蹤，則載入時實體，EF Core 將會自動設定來參考任何實體已經載入，新載入實體的導覽屬性並設定參考的已載入實體的導覽屬性新載入的實體。

### <a name="querying-related-entities"></a>查詢相關的實體

您也可以取得 LINQ 查詢，表示導覽屬性的內容。

這可讓您執行像是透過相關的項目執行彙總運算子，而不載入記憶體。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

您也可以篩選哪些相關的實體載入至記憶體。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a>消極式載入

EF 核心尚不支援消極式載入。 您可以檢視[消極式載入項目，在我們的待辦項目](https://github.com/aspnet/EntityFramework/issues/3797)追蹤這項功能。

## <a name="related-data-and-serialization"></a>相關的資料和序列化

因為 EF 核心會自動修正向上導覽屬性，您可以得到循環物件圖形中。 例如，載入部落格和它相關的文章將會導致參考的文章集合部落格物件。 這些文章中的每個都會有部落格的參考。

某些序列化架構不允許這類循環。 例如，Json.NET 將會擲回下列例外狀況，如果有發生循環。

> Newtonsoft.Json.JsonSerializationException： 自我參考型別 'MyApplication.Models.Blog' 屬性 '部落格' 偵測到迴圈。

如果您使用 ASP.NET Core，您可以設定 Json.NET 忽略它找到的物件圖形中的循環。 這是`ConfigureServices(...)`方法中的`Startup.cs`。

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
