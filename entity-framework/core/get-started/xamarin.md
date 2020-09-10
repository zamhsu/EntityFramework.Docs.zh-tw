---
title: 使用 EF Core 和 Xamarin EF Core 的消費者入門
description: 使用 Xamarin 建立行動裝置應用程式的使用者入門教學課程 Entity Framework Core
author: codemillmatt
ms.date: 07/07/2020
ms.author: masoucou
uid: core/get-started/xamarin
ms.openlocfilehash: b4a7e2260337d74329d309e9db32fe97a2131d73
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619292"
---
# <a name="getting-started-with-ef-core-and-xamarin"></a>使用 EF Core 和 Xamarin 消費者入門

在本教學課程中，您會建立一個 [Xamarin. Forms](/xamarin/get-started/what-is-xamarin-forms) 應用程式，該應用程式會使用 Entity Framework Core 對 SQLite 資料庫執行資料存取。

您可以使用 Windows 上的 Visual Studio 或 Visual Studio for Mac 來遵循教學課程。

> [!TIP]
> 您可以 [在 GitHub 上](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin)查看這篇文章的範例。

## <a name="prerequisites"></a>必要條件

安裝下列其中一項：

* 使用此工作負載[Visual Studio 2019 16.3 版或更新版本](https://www.visualstudio.com/downloads/)：
  * **使用 .NET 的行動裝置程式開發**
* [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)

本檔會針對每個平臺 [提供詳細的逐步安裝指示](/xamarin/get-started/installation) 。

## <a name="download-and-run-the-sample-project"></a>下載並執行範例專案

若要執行及探索此範例應用程式，請下載 GitHub 上的程式碼。

下載之後，請在 Visual Studio 中開啟方案檔， `EFGettingStarted.sln` 或 Visual Studio for Mac，然後在您選擇的平臺上執行應用程式。

當應用程式第一次啟動時，它會在本機 SQLite 資料庫中填入兩個代表 blog 的專案。

![所有 blog 清單頁面的螢幕擷取畫面](_static/xamarin-tutorial-1.png)

按一下工具列中的 [ **加入** ] 按鈕。

將會出現新的頁面，讓您輸入新的 blog 的相關資訊。

![新 blog [編輯] 頁面的螢幕擷取畫面](_static/xamarin-tutorial-2.png)

填寫所有資訊，然後按一下工具列中的 [ **儲存** ]。 新的 blog 會儲存至應用程式的 SQLite 資料庫，並顯示在清單中。

您可以按一下清單中的其中一個 blog 專案，查看該 blog 的任何文章。

![[Blog] 文章清單頁面的螢幕擷取畫面](_static/xamarin-tutorial-3.png)

在工具列中按一下 [ **加入** ]。

接著會出現一個頁面，讓您填寫新的 blog 貼文的相關資訊。

![[新增貼文] 頁面的螢幕擷取畫面](_static/xamarin-tutorial-4.png)

填寫所有資訊，然後按一下工具列中的 [ **儲存** ]。

新的貼文將會與您在上一個步驟中按一下的 blog 文章相關聯，並會儲存至應用程式的 SQLite 資料庫，並顯示在清單中。

返回至 [blog 清單] 頁面。 並按一下工具列中的 [ **全部刪除** ]。 然後，將會從應用程式的 SQLite 資料庫中刪除所有的 blog 和其對應的貼文。

![已刪除所有 blog 的應用程式螢幕擷取畫面](_static/xamarin-tutorial-5.png)

## <a name="explore-the-code"></a>探索程式碼

下列各節將逐步引導您完成範例專案中的程式碼，該程式碼會使用 EF Core 搭配 Xamarin，從 SQLite 資料庫讀取、建立、更新及刪除資料。

假設您已經熟悉 [顯示資料](/xamarin/xamarin-forms/app-fundamentals/data-binding/) 並在 [頁面之間流覽](/xamarin/xamarin-forms/app-fundamentals/navigation/)的 Xamarin. Forms 主題。

## <a name="entity-framework-core-nuget-packages"></a>Entity Framework Core NuGet 套件

若要使用 EF Core 建立 Xamarin 的 Forms 應用程式，請為 EF Core 資料庫提供 (者安裝套件，) 您想要以 Xamarin 方案中的所有專案為目標。 本教學課程使用 SQLite 提供者。

在 Xamarin 方案中的每個專案都需要下列 NuGet 套件。

* `Microsoft.EntityFrameworkCore.Sqlite`

## <a name="model-classes"></a>模型類別

在 SQLite 資料庫中透過 EF Core 存取的每個資料表都會在類別中模型化。 在此範例中，會使用兩個類別： `Blog` 以及 `Post` 可在資料夾中找到的類別 `Models` 。

模型類別只是由屬性所組成，而這些屬性是資料庫中的模型資料行。

* **Blog.cs**

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Blog.cs)]

* `Posts`屬性定義和之間的父子式關聯性 `Blog` `Post` 。

* **Post.cs**

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Post.cs)]

* `BlogId`和 `Blog` 屬性會與實例的父物件建立關聯 `Blog` `Post` 。

## <a name="data-context"></a>資料內容

`BloggingContext`類別位於 `Services` 資料夾中，並繼承自 EF Core `DbContext` 類別。 `DbContext`用來將資料庫查詢與變更群組在一起。

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Services/BloggingContext.cs)]

* 此類型類別中的這兩個屬性 `DbSet` 都是用來在代表 blog 和貼文的基礎資料表上運作。
* 在 `SQLitePCL.Batteries_V2.Init()` iOS 上起始 SQLite 的函式需要此項。
* 此 `OnConfiguring` 函數會在實體裝置上設定 SQLite 資料庫的位置。

## <a name="create-read-update--delete"></a>建立、讀取、更新及刪除

以下是應用程式中 EF Core 用來存取 SQLite 的一些實例。

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
  * 將 `Save_Clicked` `AddBlogPage.xaml.cs` 新 `Blog` 物件插入 SQLite 資料庫的功能。

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
  * `Save_Clicked`的函式會以新的來 `AddPostPage.xaml.cs` 更新現有的 `Blog` 物件 `Post` 。
  
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
  * 的函式會 `DeleteAll_Clicked` `BlogsPage.xaml.cs` 刪除 `Blog` SQLite 資料庫中的所有記錄，並將刪除程式集中至所有 `Blog` 子 `Post` 記錄。

```csharp
using (var blogContext = new BloggingContext())
{
    blogContext.RemoveRange(blogContext.Blogs);

    await blogContext.SaveChangesAsync();
}
```

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已瞭解如何使用 Xamarin 應用程式，利用 Entity Framework Core 來存取 SQLite 資料庫。

其他與 Xamarin 開發人員相關的 Entity Framework Core 主題：

* [設定 `DbContext`](xref:core/miscellaneous/configuring-dbcontext)
* 深入了解 [LINQ 查詢運算式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)
* [設定模型](xref:core/modeling/index)以指定如[必要](xref:core/modeling/entity-properties#required-and-optional-properties)和[最大長度](xref:core/modeling/entity-properties#maximum-length)之類的內容
