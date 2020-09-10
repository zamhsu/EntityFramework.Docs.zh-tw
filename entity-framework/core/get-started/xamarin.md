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
# <a name="getting-started-with-ef-core-and-xamarin"></a><span data-ttu-id="e1392-103">使用 EF Core 和 Xamarin 消費者入門</span><span class="sxs-lookup"><span data-stu-id="e1392-103">Getting Started with EF Core and Xamarin</span></span>

<span data-ttu-id="e1392-104">在本教學課程中，您會建立一個 [Xamarin. Forms](/xamarin/get-started/what-is-xamarin-forms) 應用程式，該應用程式會使用 Entity Framework Core 對 SQLite 資料庫執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="e1392-104">In this tutorial, you create a [Xamarin.Forms](/xamarin/get-started/what-is-xamarin-forms) application that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="e1392-105">您可以使用 Windows 上的 Visual Studio 或 Visual Studio for Mac 來遵循教學課程。</span><span class="sxs-lookup"><span data-stu-id="e1392-105">You can follow the tutorial by using Visual Studio on Windows or Visual Studio for Mac.</span></span>

> [!TIP]
> <span data-ttu-id="e1392-106">您可以 [在 GitHub 上](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin)查看這篇文章的範例。</span><span class="sxs-lookup"><span data-stu-id="e1392-106">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e1392-107">必要條件</span><span class="sxs-lookup"><span data-stu-id="e1392-107">Prerequisites</span></span>

<span data-ttu-id="e1392-108">安裝下列其中一項：</span><span class="sxs-lookup"><span data-stu-id="e1392-108">Install one of the below:</span></span>

* <span data-ttu-id="e1392-109">使用此工作負載[Visual Studio 2019 16.3 版或更新版本](https://www.visualstudio.com/downloads/)：</span><span class="sxs-lookup"><span data-stu-id="e1392-109">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this workload:</span></span>
  * <span data-ttu-id="e1392-110">**使用 .NET 的行動裝置程式開發**</span><span class="sxs-lookup"><span data-stu-id="e1392-110">**Mobile Development with .NET**</span></span>
* [<span data-ttu-id="e1392-111">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e1392-111">Visual Studio for Mac</span></span>](https://visualstudio.microsoft.com/vs/mac/)

<span data-ttu-id="e1392-112">本檔會針對每個平臺 [提供詳細的逐步安裝指示](/xamarin/get-started/installation) 。</span><span class="sxs-lookup"><span data-stu-id="e1392-112">This [documentation provides detailed step-by-step installation instructions](/xamarin/get-started/installation) for each platform.</span></span>

## <a name="download-and-run-the-sample-project"></a><span data-ttu-id="e1392-113">下載並執行範例專案</span><span class="sxs-lookup"><span data-stu-id="e1392-113">Download and run the sample project</span></span>

<span data-ttu-id="e1392-114">若要執行及探索此範例應用程式，請下載 GitHub 上的程式碼。</span><span class="sxs-lookup"><span data-stu-id="e1392-114">To run and explore this sample application, download the code on GitHub.</span></span>

<span data-ttu-id="e1392-115">下載之後，請在 Visual Studio 中開啟方案檔， `EFGettingStarted.sln` 或 Visual Studio for Mac，然後在您選擇的平臺上執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="e1392-115">Once downloaded, open the solution file `EFGettingStarted.sln` in Visual Studio or Visual Studio for Mac and run the application on the platform of your choice.</span></span>

<span data-ttu-id="e1392-116">當應用程式第一次啟動時，它會在本機 SQLite 資料庫中填入兩個代表 blog 的專案。</span><span class="sxs-lookup"><span data-stu-id="e1392-116">When the app first starts, it will populate the local SQLite database with two entries representing blogs.</span></span>

![所有 blog 清單頁面的螢幕擷取畫面](_static/xamarin-tutorial-1.png)

<span data-ttu-id="e1392-118">按一下工具列中的 [ **加入** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="e1392-118">Click the **Add** button in the toolbar.</span></span>

<span data-ttu-id="e1392-119">將會出現新的頁面，讓您輸入新的 blog 的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="e1392-119">A new page will appear that allows you to enter information about a new blog.</span></span>

![新 blog [編輯] 頁面的螢幕擷取畫面](_static/xamarin-tutorial-2.png)

<span data-ttu-id="e1392-121">填寫所有資訊，然後按一下工具列中的 [ **儲存** ]。</span><span class="sxs-lookup"><span data-stu-id="e1392-121">Fill out all the info and click **Save** from the toolbar.</span></span> <span data-ttu-id="e1392-122">新的 blog 會儲存至應用程式的 SQLite 資料庫，並顯示在清單中。</span><span class="sxs-lookup"><span data-stu-id="e1392-122">The new blog will save to the app's SQLite database and will show in the list.</span></span>

<span data-ttu-id="e1392-123">您可以按一下清單中的其中一個 blog 專案，查看該 blog 的任何文章。</span><span class="sxs-lookup"><span data-stu-id="e1392-123">You can click on one of the blog entries in the list and see any posts for that blog.</span></span>

![[Blog] 文章清單頁面的螢幕擷取畫面](_static/xamarin-tutorial-3.png)

<span data-ttu-id="e1392-125">在工具列中按一下 [ **加入** ]。</span><span class="sxs-lookup"><span data-stu-id="e1392-125">Click **Add** in the toolbar.</span></span>

<span data-ttu-id="e1392-126">接著會出現一個頁面，讓您填寫新的 blog 貼文的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="e1392-126">A page then appears that allows you to fill out information about a new blog post.</span></span>

![[新增貼文] 頁面的螢幕擷取畫面](_static/xamarin-tutorial-4.png)

<span data-ttu-id="e1392-128">填寫所有資訊，然後按一下工具列中的 [ **儲存** ]。</span><span class="sxs-lookup"><span data-stu-id="e1392-128">Fill out all the information and click **Save** in the toolbar.</span></span>

<span data-ttu-id="e1392-129">新的貼文將會與您在上一個步驟中按一下的 blog 文章相關聯，並會儲存至應用程式的 SQLite 資料庫，並顯示在清單中。</span><span class="sxs-lookup"><span data-stu-id="e1392-129">The new post will be associated to the blog post you clicked on in a previous step and will save to the app's SQLite database and show in the list.</span></span>

<span data-ttu-id="e1392-130">返回至 [blog 清單] 頁面。</span><span class="sxs-lookup"><span data-stu-id="e1392-130">Go back to the blog list page.</span></span> <span data-ttu-id="e1392-131">並按一下工具列中的 [ **全部刪除** ]。</span><span class="sxs-lookup"><span data-stu-id="e1392-131">And click **Delete All** in the toolbar.</span></span> <span data-ttu-id="e1392-132">然後，將會從應用程式的 SQLite 資料庫中刪除所有的 blog 和其對應的貼文。</span><span class="sxs-lookup"><span data-stu-id="e1392-132">All blogs and their corresponding posts will then be deleted from the app's SQLite database.</span></span>

![已刪除所有 blog 的應用程式螢幕擷取畫面](_static/xamarin-tutorial-5.png)

## <a name="explore-the-code"></a><span data-ttu-id="e1392-134">探索程式碼</span><span class="sxs-lookup"><span data-stu-id="e1392-134">Explore the code</span></span>

<span data-ttu-id="e1392-135">下列各節將逐步引導您完成範例專案中的程式碼，該程式碼會使用 EF Core 搭配 Xamarin，從 SQLite 資料庫讀取、建立、更新及刪除資料。</span><span class="sxs-lookup"><span data-stu-id="e1392-135">The following sections will walk you through the code in the sample project that reads, creates, updates, and deletes data from a SQLite database using EF Core with Xamarin.Forms.</span></span>

<span data-ttu-id="e1392-136">假設您已經熟悉 [顯示資料](/xamarin/xamarin-forms/app-fundamentals/data-binding/) 並在 [頁面之間流覽](/xamarin/xamarin-forms/app-fundamentals/navigation/)的 Xamarin. Forms 主題。</span><span class="sxs-lookup"><span data-stu-id="e1392-136">It is assumed that you are familiar with the Xamarin.Forms topics of [displaying data](/xamarin/xamarin-forms/app-fundamentals/data-binding/) and [navigating between pages](/xamarin/xamarin-forms/app-fundamentals/navigation/).</span></span>

## <a name="entity-framework-core-nuget-packages"></a><span data-ttu-id="e1392-137">Entity Framework Core NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="e1392-137">Entity Framework Core NuGet packages</span></span>

<span data-ttu-id="e1392-138">若要使用 EF Core 建立 Xamarin 的 Forms 應用程式，請為 EF Core 資料庫提供 (者安裝套件，) 您想要以 Xamarin 方案中的所有專案為目標。</span><span class="sxs-lookup"><span data-stu-id="e1392-138">To create Xamarin.Forms apps with EF Core, you install the package for the EF Core database provider(s) you want to target into all of the projects in the Xamarin.Forms solution.</span></span> <span data-ttu-id="e1392-139">本教學課程使用 SQLite 提供者。</span><span class="sxs-lookup"><span data-stu-id="e1392-139">This tutorial uses the SQLite provider.</span></span>

<span data-ttu-id="e1392-140">在 Xamarin 方案中的每個專案都需要下列 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="e1392-140">The following NuGet package is needed in each of the projects in the Xamarin.Forms solution.</span></span>

* `Microsoft.EntityFrameworkCore.Sqlite`

## <a name="model-classes"></a><span data-ttu-id="e1392-141">模型類別</span><span class="sxs-lookup"><span data-stu-id="e1392-141">Model classes</span></span>

<span data-ttu-id="e1392-142">在 SQLite 資料庫中透過 EF Core 存取的每個資料表都會在類別中模型化。</span><span class="sxs-lookup"><span data-stu-id="e1392-142">Each table in the SQLite database accessed through EF Core is modeled in a class.</span></span> <span data-ttu-id="e1392-143">在此範例中，會使用兩個類別： `Blog` 以及 `Post` 可在資料夾中找到的類別 `Models` 。</span><span class="sxs-lookup"><span data-stu-id="e1392-143">In this sample, two classes are used: `Blog` and `Post` which can be found in the `Models` folder.</span></span>

<span data-ttu-id="e1392-144">模型類別只是由屬性所組成，而這些屬性是資料庫中的模型資料行。</span><span class="sxs-lookup"><span data-stu-id="e1392-144">The model classes are composed only of properties, which model columns in the database.</span></span>

* <span data-ttu-id="e1392-145">**Blog.cs**</span><span class="sxs-lookup"><span data-stu-id="e1392-145">**Blog.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Blog.cs)]

* <span data-ttu-id="e1392-146">`Posts`屬性定義和之間的父子式關聯性 `Blog` `Post` 。</span><span class="sxs-lookup"><span data-stu-id="e1392-146">The `Posts` property defines a parent-child relationship between `Blog` and `Post`.</span></span>

* <span data-ttu-id="e1392-147">**Post.cs**</span><span class="sxs-lookup"><span data-stu-id="e1392-147">**Post.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Post.cs)]

* <span data-ttu-id="e1392-148">`BlogId`和 `Blog` 屬性會與實例的父物件建立關聯 `Blog` `Post` 。</span><span class="sxs-lookup"><span data-stu-id="e1392-148">The `BlogId` and `Blog` properties relate back to the parent `Blog` object for the instance of the `Post`.</span></span>

## <a name="data-context"></a><span data-ttu-id="e1392-149">資料內容</span><span class="sxs-lookup"><span data-stu-id="e1392-149">Data context</span></span>

<span data-ttu-id="e1392-150">`BloggingContext`類別位於 `Services` 資料夾中，並繼承自 EF Core `DbContext` 類別。</span><span class="sxs-lookup"><span data-stu-id="e1392-150">The `BloggingContext` class is located in the `Services` folder and inherits from the EF Core `DbContext` class.</span></span> <span data-ttu-id="e1392-151">`DbContext`用來將資料庫查詢與變更群組在一起。</span><span class="sxs-lookup"><span data-stu-id="e1392-151">A `DbContext` is used to group together database queries and changes.</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Services/BloggingContext.cs)]

* <span data-ttu-id="e1392-152">此類型類別中的這兩個屬性 `DbSet` 都是用來在代表 blog 和貼文的基礎資料表上運作。</span><span class="sxs-lookup"><span data-stu-id="e1392-152">Both properties in this class of type `DbSet` are used to operate on the underlying tables representing Blogs and Posts.</span></span>
* <span data-ttu-id="e1392-153">在 `SQLitePCL.Batteries_V2.Init()` iOS 上起始 SQLite 的函式需要此項。</span><span class="sxs-lookup"><span data-stu-id="e1392-153">The `SQLitePCL.Batteries_V2.Init()` is needed in the constructor to initiate SQLite on iOS.</span></span>
* <span data-ttu-id="e1392-154">此 `OnConfiguring` 函數會在實體裝置上設定 SQLite 資料庫的位置。</span><span class="sxs-lookup"><span data-stu-id="e1392-154">The `OnConfiguring` function sets up the location of the SQLite database on the physical device.</span></span>

## <a name="create-read-update--delete"></a><span data-ttu-id="e1392-155">建立、讀取、更新及刪除</span><span class="sxs-lookup"><span data-stu-id="e1392-155">Create, read, update & delete</span></span>

<span data-ttu-id="e1392-156">以下是應用程式中 EF Core 用來存取 SQLite 的一些實例。</span><span class="sxs-lookup"><span data-stu-id="e1392-156">The following are some instances in the app where EF Core is used to access SQLite.</span></span>

### <a name="read"></a><span data-ttu-id="e1392-157">讀取</span><span class="sxs-lookup"><span data-stu-id="e1392-157">Read</span></span>

* <span data-ttu-id="e1392-158">傳回所有記錄。</span><span class="sxs-lookup"><span data-stu-id="e1392-158">Return all records.</span></span>
  * <span data-ttu-id="e1392-159">的函式會傳回 `OnAppearing` `BlogsPage.xaml.cs` 所有 `Blog` 記錄，並將其儲存在 `List` 變數中。</span><span class="sxs-lookup"><span data-stu-id="e1392-159">The `OnAppearing` function of `BlogsPage.xaml.cs` returns all `Blog` records and stores them into a `List` variable.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var theBlogs = blogContext.Blogs.ToList();
}
```

* <span data-ttu-id="e1392-160">傳回特定記錄。</span><span class="sxs-lookup"><span data-stu-id="e1392-160">Return specific records.</span></span>
  * <span data-ttu-id="e1392-161">的函式 `OnAppearing` `PostsPage.xaml.cs` 會傳回 `Post` 包含特定的記錄 `BlogId` 。</span><span class="sxs-lookup"><span data-stu-id="e1392-161">The `OnAppearing` function of `PostsPage.xaml.cs` returns `Post` records that contain a specific `BlogId`.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var postList = blogContext.Posts
        .Where(p => p.BlogId == BlogId)
        .ToList();
}
```

### <a name="create"></a><span data-ttu-id="e1392-162">建立</span><span class="sxs-lookup"><span data-stu-id="e1392-162">Create</span></span>

* <span data-ttu-id="e1392-163">插入新的記錄。</span><span class="sxs-lookup"><span data-stu-id="e1392-163">Insert a new record.</span></span>
  * <span data-ttu-id="e1392-164">將 `Save_Clicked` `AddBlogPage.xaml.cs` 新 `Blog` 物件插入 SQLite 資料庫的功能。</span><span class="sxs-lookup"><span data-stu-id="e1392-164">The `Save_Clicked` function of `AddBlogPage.xaml.cs` inserts a new `Blog` object into the SQLite database.</span></span>

```csharp
var blog = new Blog { Url = blogUrl.Text };

using (var blogContext = new BloggingContext())
{
    blogContext.Add(blog);

    await blogContext.SaveChangesAsync();
}
```

### <a name="update"></a><span data-ttu-id="e1392-165">更新</span><span class="sxs-lookup"><span data-stu-id="e1392-165">Update</span></span>

* <span data-ttu-id="e1392-166">更新現有的記錄。</span><span class="sxs-lookup"><span data-stu-id="e1392-166">Update an existing record.</span></span>
  * <span data-ttu-id="e1392-167">`Save_Clicked`的函式會以新的來 `AddPostPage.xaml.cs` 更新現有的 `Blog` 物件 `Post` 。</span><span class="sxs-lookup"><span data-stu-id="e1392-167">The `Save_Clicked` function of `AddPostPage.xaml.cs` updates an existing `Blog` object with a new `Post`.</span></span>
  
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

### <a name="delete"></a><span data-ttu-id="e1392-168">刪除</span><span class="sxs-lookup"><span data-stu-id="e1392-168">Delete</span></span>

* <span data-ttu-id="e1392-169">刪除具有 cascade 至子記錄的所有記錄。</span><span class="sxs-lookup"><span data-stu-id="e1392-169">Delete all records with cascade to child records.</span></span>
  * <span data-ttu-id="e1392-170">的函式會 `DeleteAll_Clicked` `BlogsPage.xaml.cs` 刪除 `Blog` SQLite 資料庫中的所有記錄，並將刪除程式集中至所有 `Blog` 子 `Post` 記錄。</span><span class="sxs-lookup"><span data-stu-id="e1392-170">The `DeleteAll_Clicked` function of `BlogsPage.xaml.cs` deletes all the `Blog` records in the SQLite database and cascades the deletes to all of the `Blog` child `Post` records.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    blogContext.RemoveRange(blogContext.Blogs);

    await blogContext.SaveChangesAsync();
}
```

## <a name="next-steps"></a><span data-ttu-id="e1392-171">後續步驟</span><span class="sxs-lookup"><span data-stu-id="e1392-171">Next steps</span></span>

<span data-ttu-id="e1392-172">在此快速入門中，您已瞭解如何使用 Xamarin 應用程式，利用 Entity Framework Core 來存取 SQLite 資料庫。</span><span class="sxs-lookup"><span data-stu-id="e1392-172">In this getting started you have learned how to use a Xamarin.Forms application to access a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="e1392-173">其他與 Xamarin 開發人員相關的 Entity Framework Core 主題：</span><span class="sxs-lookup"><span data-stu-id="e1392-173">Other Entity Framework Core topics of interest to Xamarin developers:</span></span>

* [<span data-ttu-id="e1392-174">設定 `DbContext`</span><span class="sxs-lookup"><span data-stu-id="e1392-174">Configuring a `DbContext`</span></span>](xref:core/miscellaneous/configuring-dbcontext)
* <span data-ttu-id="e1392-175">深入了解 [LINQ 查詢運算式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span><span class="sxs-lookup"><span data-stu-id="e1392-175">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="e1392-176">[設定模型](xref:core/modeling/index)以指定如[必要](xref:core/modeling/entity-properties#required-and-optional-properties)和[最大長度](xref:core/modeling/entity-properties#maximum-length)之類的內容</span><span class="sxs-lookup"><span data-stu-id="e1392-176">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
