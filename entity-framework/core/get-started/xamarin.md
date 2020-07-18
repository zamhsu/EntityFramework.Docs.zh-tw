---
title: EF Core 和 Xamarin 的消費者入門 EF Core
author: codemillmatt
ms.date: 07/07/2020
ms.author: masoucou
uid: core/get-started/xamarin
ms.openlocfilehash: 14fe61e0ddd648f824bfe3c1c91a58af91b1c2d4
ms.sourcegitcommit: d85263b5d5d665dbaf94de8832e2917bce048b34
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2020
ms.locfileid: "86453029"
---
# <a name="getting-started-with-ef-core-and-xamarin"></a>EF Core 和 Xamarin 的消費者入門

在本教學課程中，您會建立一個[Xamarin. Forms](/xamarin/get-started/what-is-xamarin-forms)應用程式，以使用 Entity Framework Core 對 SQLite 資料庫執行資料存取。

您可以遵循教學課程，方法是使用 Windows 上的 Visual Studio 或 Visual Studio for Mac。

> [!TIP]
> 您可以[在 GitHub 上](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin)查看這篇文章的範例。

## <a name="prerequisites"></a>先決條件

安裝下列其中一項：

* 使用此工作負載[Visual Studio 2019 16.3 版或更新版本](https://www.visualstudio.com/downloads/)：
  * **使用 .NET 的行動裝置程式開發**
* [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)

本檔提供每個平臺的[詳細逐步安裝指示](/xamarin/get-started/installation)。

## <a name="download-and-run-the-sample-project"></a>下載並執行範例專案

若要執行及探索此範例應用程式，請下載 GitHub 上的程式碼。

下載之後，請 `EFGettingStarted.sln` 在 Visual Studio 或 Visual Studio for Mac 中開啟方案檔，然後在您選擇的平臺上執行應用程式。

當應用程式第一次啟動時，它會在本機 SQLite 資料庫中填入兩個代表 blog 的專案。

![所有 blog 清單頁面的螢幕擷取畫面](_static/xamarin-tutorial-1.png)

按一下工具列中的 [**新增**] 按鈕。

隨即會出現新的頁面，讓您輸入新 blog 的相關資訊。

![新 blog [編輯] 頁面的螢幕擷取畫面](_static/xamarin-tutorial-2.png)

填寫所有資訊，然後按一下工具列中的 [**儲存**]。 新的 blog 會儲存至應用程式的 SQLite 資料庫，並且會顯示在清單中。

您可以按一下清單中的其中一個 blog 專案，並查看該 blog 的任何文章。

![Blog 文章清單頁面的螢幕擷取畫面](_static/xamarin-tutorial-3.png)

按一下工具列中的 [**新增**]。

接著會出現一個頁面，讓您填寫新的 blog 文章的相關資訊。

![新增張貼頁面的螢幕擷取畫面](_static/xamarin-tutorial-4.png)

填寫所有資訊，然後按一下工具列中的 [**儲存**]。

新的貼文會與您在上一個步驟中按一下的 blog 文章相關聯，並且會儲存至應用程式的 SQLite 資料庫，並顯示在清單中。

返回 [blog 清單] 頁面。 然後按一下工具列中的 [**全部刪除**]。 然後會從應用程式的 SQLite 資料庫中刪除所有的 blog 及其對應的文章。

![已刪除所有 blog 的應用程式螢幕擷取畫面](_static/xamarin-tutorial-5.png)

## <a name="explore-the-code"></a>探索程式碼

下列各節將逐步引導您完成範例專案中的程式碼，其會使用 EF Core 搭配 Xamarin 來讀取、建立、更新及刪除 SQLite 資料庫中的資料。

假設您熟悉[顯示資料](/xamarin/xamarin-forms/app-fundamentals/data-binding/)和在[頁面之間流覽](/xamarin/xamarin-forms/app-fundamentals/navigation/)的 Xamarin. Forms 主題。

## <a name="entity-framework-core-nuget-packages"></a>Entity Framework Core NuGet 套件

若要使用 EF Core 建立 Xamarin 應用程式，請將您想要作為目標之 EF Core 資料庫提供者的套件，安裝到 [Xamarin] 表單方案中的所有專案。 本教學課程使用 SQLite 提供者。

在 [Xamarin] 表單方案中的每個專案都需要下列 NuGet 套件。

* `Microsoft.EntityFrameworkCore.Sqlite`

## <a name="model-classes"></a>模型類別

SQLite 資料庫中透過 EF Core 存取的每個資料表都是以類別模型化。 在此範例中，會使用兩個類別： `Blog` 和， `Post` 可以在資料夾中找到 `Models` 。

模型類別僅由屬性所組成，而這些屬性是資料庫中的模型資料行。

* **Blog.cs**

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Blog.cs)]

* `Posts`屬性會定義與之間的父子式關聯 `Blog` 性 `Post` 。

* **Post.cs**

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Post.cs)]

* `BlogId`和 `Blog` 屬性會與實例的父物件產生關聯 `Blog` `Post` 。

## <a name="data-context"></a>資料內容

`BloggingContext`類別位於 `Services` 資料夾中，並繼承自 EF Core `DbContext` 類別。 `DbContext`是用來將資料庫查詢和變更群組在一起。

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Services/BloggingContext.cs)]

* 此型別類別中的兩個屬性 `DbSet` 都是用來在代表 blog 和文章的基礎資料表上操作。
* 在 `SQLitePCL.Batteries_V2.Init()` 要于 iOS 上起始 SQLite 的函式中，需要有。
* 函式會 `OnConfiguring` 在實體裝置上設定 SQLite 資料庫的位置。

## <a name="create-read-update--delete"></a>建立、讀取、更新及刪除

以下是應用程式中的一些實例，其中 EF Core 用來存取 SQLite。

### <a name="read"></a>讀取

* 傳回所有記錄。
  * 的函式會傳回 `OnAppearing` `BlogsPage.xaml.cs` 所有 `Blog` 記錄，並將其儲存在 `List` 變數中。

```csharp
using (var blogContext = new BloggingContext())
{
    var theBlogs = blogContext.Blogs.ToList();
}
```

* 傳回特定記錄。
  * 的函式 `OnAppearing` `PostsPage.xaml.cs` 會傳回 `Post` 包含特定的記錄 `BlogId` 。

```csharp
using (var blogContext = new BloggingContext())
{
    var postList = blogContext.Posts
        .Where(p => p.BlogId == BlogId)
        .ToList();
}
```

### <a name="create"></a>建立

* 插入新的記錄。
  * 的 `Save_Clicked` 功能會將 `AddBlogPage.xaml.cs` 新的 `Blog` 物件插入 SQLite 資料庫。

```csharp
var blog = new Blog { Url = blogUrl.Text };

using (var blogContext = new BloggingContext())
{
    blogContext.Add(blog);

    await blogContext.SaveChangesAsync();
}
```

### <a name="update"></a>更新

* 更新現有的記錄。
  * 的 `Save_Clicked` 功能會 `AddPostPage.xaml.cs` 使用新的來更新現有的 `Blog` 物件 `Post` 。
  
```csharp
var newPost = new Post
{
    BlogId = BlogId,
    Content = postCell.Text,
    Title = titleCell.Text
};

using (var blogContext = new BloggingContext())
{
    var blog = await blogContext
        .Blogs
        .FirstAsync(b => b.BlogId == BlogId);

    blog.Posts.Add(newPost);

    await blogContext.SaveChangesAsync();
}
```

### <a name="delete"></a>刪除

* 刪除具有 cascade 至子記錄的所有記錄。
  * 的函式會 `DeleteAll_Clicked` `BlogsPage.xaml.cs` 刪除 `Blog` SQLite 資料庫中的所有記錄，並將刪除功能串聯至所有 `Blog` 子 `Post` 記錄。

```csharp
using (var blogContext = new BloggingContext())
{
    blogContext.RemoveRange(blogContext.Blogs);

    await blogContext.SaveChangesAsync();
}
```

## <a name="next-steps"></a>後續步驟

在此使用者入門中，您已瞭解如何使用 Entity Framework Core，來透過 Xamarin 應用程式存取 SQLite 資料庫。

其他與 Xamarin 開發人員相關的 Entity Framework Core 主題：

* [設定`DbContext`](xref:core/miscellaneous/configuring-dbcontext)
* 深入了解 [LINQ 查詢運算式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)
* [設定模型](xref:core/modeling/index)以指定如[必要](xref:core/modeling/entity-properties#required-and-optional-properties)和[最大長度](xref:core/modeling/entity-properties#maximum-length)之類的內容
