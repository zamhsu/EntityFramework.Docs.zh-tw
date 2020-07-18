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
# <a name="getting-started-with-ef-core-and-xamarin"></a><span data-ttu-id="41b64-102">EF Core 和 Xamarin 的消費者入門</span><span class="sxs-lookup"><span data-stu-id="41b64-102">Getting Started with EF Core and Xamarin</span></span>

<span data-ttu-id="41b64-103">在本教學課程中，您會建立一個[Xamarin. Forms](/xamarin/get-started/what-is-xamarin-forms)應用程式，以使用 Entity Framework Core 對 SQLite 資料庫執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="41b64-103">In this tutorial, you create a [Xamarin.Forms](/xamarin/get-started/what-is-xamarin-forms) application that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="41b64-104">您可以遵循教學課程，方法是使用 Windows 上的 Visual Studio 或 Visual Studio for Mac。</span><span class="sxs-lookup"><span data-stu-id="41b64-104">You can follow the tutorial by using Visual Studio on Windows or Visual Studio for Mac.</span></span>

> [!TIP]
> <span data-ttu-id="41b64-105">您可以[在 GitHub 上](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin)查看這篇文章的範例。</span><span class="sxs-lookup"><span data-stu-id="41b64-105">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="41b64-106">先決條件</span><span class="sxs-lookup"><span data-stu-id="41b64-106">Prerequisites</span></span>

<span data-ttu-id="41b64-107">安裝下列其中一項：</span><span class="sxs-lookup"><span data-stu-id="41b64-107">Install one of the below:</span></span>

* <span data-ttu-id="41b64-108">使用此工作負載[Visual Studio 2019 16.3 版或更新版本](https://www.visualstudio.com/downloads/)：</span><span class="sxs-lookup"><span data-stu-id="41b64-108">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this workload:</span></span>
  * <span data-ttu-id="41b64-109">**使用 .NET 的行動裝置程式開發**</span><span class="sxs-lookup"><span data-stu-id="41b64-109">**Mobile Development with .NET**</span></span>
* [<span data-ttu-id="41b64-110">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="41b64-110">Visual Studio for Mac</span></span>](https://visualstudio.microsoft.com/vs/mac/)

<span data-ttu-id="41b64-111">本檔提供每個平臺的[詳細逐步安裝指示](/xamarin/get-started/installation)。</span><span class="sxs-lookup"><span data-stu-id="41b64-111">This [documentation provides detailed step-by-step installation instructions](/xamarin/get-started/installation) for each platform.</span></span>

## <a name="download-and-run-the-sample-project"></a><span data-ttu-id="41b64-112">下載並執行範例專案</span><span class="sxs-lookup"><span data-stu-id="41b64-112">Download and run the sample project</span></span>

<span data-ttu-id="41b64-113">若要執行及探索此範例應用程式，請下載 GitHub 上的程式碼。</span><span class="sxs-lookup"><span data-stu-id="41b64-113">To run and explore this sample application, download the code on GitHub.</span></span>

<span data-ttu-id="41b64-114">下載之後，請 `EFGettingStarted.sln` 在 Visual Studio 或 Visual Studio for Mac 中開啟方案檔，然後在您選擇的平臺上執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="41b64-114">Once downloaded, open the solution file `EFGettingStarted.sln` in Visual Studio or Visual Studio for Mac and run the application on the platform of your choice.</span></span>

<span data-ttu-id="41b64-115">當應用程式第一次啟動時，它會在本機 SQLite 資料庫中填入兩個代表 blog 的專案。</span><span class="sxs-lookup"><span data-stu-id="41b64-115">When the app first starts, it will populate the local SQLite database with two entries representing blogs.</span></span>

![所有 blog 清單頁面的螢幕擷取畫面](_static/xamarin-tutorial-1.png)

<span data-ttu-id="41b64-117">按一下工具列中的 [**新增**] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="41b64-117">Click the **Add** button in the toolbar.</span></span>

<span data-ttu-id="41b64-118">隨即會出現新的頁面，讓您輸入新 blog 的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="41b64-118">A new page will appear that allows you to enter information about a new blog.</span></span>

![新 blog [編輯] 頁面的螢幕擷取畫面](_static/xamarin-tutorial-2.png)

<span data-ttu-id="41b64-120">填寫所有資訊，然後按一下工具列中的 [**儲存**]。</span><span class="sxs-lookup"><span data-stu-id="41b64-120">Fill out all the info and click **Save** from the toolbar.</span></span> <span data-ttu-id="41b64-121">新的 blog 會儲存至應用程式的 SQLite 資料庫，並且會顯示在清單中。</span><span class="sxs-lookup"><span data-stu-id="41b64-121">The new blog will save to the app's SQLite database and will show in the list.</span></span>

<span data-ttu-id="41b64-122">您可以按一下清單中的其中一個 blog 專案，並查看該 blog 的任何文章。</span><span class="sxs-lookup"><span data-stu-id="41b64-122">You can click on one of the blog entries in the list and see any posts for that blog.</span></span>

![Blog 文章清單頁面的螢幕擷取畫面](_static/xamarin-tutorial-3.png)

<span data-ttu-id="41b64-124">按一下工具列中的 [**新增**]。</span><span class="sxs-lookup"><span data-stu-id="41b64-124">Click **Add** in the toolbar.</span></span>

<span data-ttu-id="41b64-125">接著會出現一個頁面，讓您填寫新的 blog 文章的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="41b64-125">A page then appears that allows you to fill out information about a new blog post.</span></span>

![新增張貼頁面的螢幕擷取畫面](_static/xamarin-tutorial-4.png)

<span data-ttu-id="41b64-127">填寫所有資訊，然後按一下工具列中的 [**儲存**]。</span><span class="sxs-lookup"><span data-stu-id="41b64-127">Fill out all the information and click **Save** in the toolbar.</span></span>

<span data-ttu-id="41b64-128">新的貼文會與您在上一個步驟中按一下的 blog 文章相關聯，並且會儲存至應用程式的 SQLite 資料庫，並顯示在清單中。</span><span class="sxs-lookup"><span data-stu-id="41b64-128">The new post will be associated to the blog post you clicked on in a previous step and will save to the app's SQLite database and show in the list.</span></span>

<span data-ttu-id="41b64-129">返回 [blog 清單] 頁面。</span><span class="sxs-lookup"><span data-stu-id="41b64-129">Go back to the blog list page.</span></span> <span data-ttu-id="41b64-130">然後按一下工具列中的 [**全部刪除**]。</span><span class="sxs-lookup"><span data-stu-id="41b64-130">And click **Delete All** in the toolbar.</span></span> <span data-ttu-id="41b64-131">然後會從應用程式的 SQLite 資料庫中刪除所有的 blog 及其對應的文章。</span><span class="sxs-lookup"><span data-stu-id="41b64-131">All blogs and their corresponding posts will then be deleted from the app's SQLite database.</span></span>

![已刪除所有 blog 的應用程式螢幕擷取畫面](_static/xamarin-tutorial-5.png)

## <a name="explore-the-code"></a><span data-ttu-id="41b64-133">探索程式碼</span><span class="sxs-lookup"><span data-stu-id="41b64-133">Explore the code</span></span>

<span data-ttu-id="41b64-134">下列各節將逐步引導您完成範例專案中的程式碼，其會使用 EF Core 搭配 Xamarin 來讀取、建立、更新及刪除 SQLite 資料庫中的資料。</span><span class="sxs-lookup"><span data-stu-id="41b64-134">The following sections will walk you through the code in the sample project that reads, creates, updates, and deletes data from a SQLite database using EF Core with Xamarin.Forms.</span></span>

<span data-ttu-id="41b64-135">假設您熟悉[顯示資料](/xamarin/xamarin-forms/app-fundamentals/data-binding/)和在[頁面之間流覽](/xamarin/xamarin-forms/app-fundamentals/navigation/)的 Xamarin. Forms 主題。</span><span class="sxs-lookup"><span data-stu-id="41b64-135">It is assumed that you are familiar with the Xamarin.Forms topics of [displaying data](/xamarin/xamarin-forms/app-fundamentals/data-binding/) and [navigating between pages](/xamarin/xamarin-forms/app-fundamentals/navigation/).</span></span>

## <a name="entity-framework-core-nuget-packages"></a><span data-ttu-id="41b64-136">Entity Framework Core NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="41b64-136">Entity Framework Core NuGet packages</span></span>

<span data-ttu-id="41b64-137">若要使用 EF Core 建立 Xamarin 應用程式，請將您想要作為目標之 EF Core 資料庫提供者的套件，安裝到 [Xamarin] 表單方案中的所有專案。</span><span class="sxs-lookup"><span data-stu-id="41b64-137">To create Xamarin.Forms apps with EF Core, you install the package for the EF Core database provider(s) you want to target into all of the projects in the Xamarin.Forms solution.</span></span> <span data-ttu-id="41b64-138">本教學課程使用 SQLite 提供者。</span><span class="sxs-lookup"><span data-stu-id="41b64-138">This tutorial uses the SQLite provider.</span></span>

<span data-ttu-id="41b64-139">在 [Xamarin] 表單方案中的每個專案都需要下列 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="41b64-139">The following NuGet package is needed in each of the projects in the Xamarin.Forms solution.</span></span>

* `Microsoft.EntityFrameworkCore.Sqlite`

## <a name="model-classes"></a><span data-ttu-id="41b64-140">模型類別</span><span class="sxs-lookup"><span data-stu-id="41b64-140">Model classes</span></span>

<span data-ttu-id="41b64-141">SQLite 資料庫中透過 EF Core 存取的每個資料表都是以類別模型化。</span><span class="sxs-lookup"><span data-stu-id="41b64-141">Each table in the SQLite database accessed through EF Core is modeled in a class.</span></span> <span data-ttu-id="41b64-142">在此範例中，會使用兩個類別： `Blog` 和， `Post` 可以在資料夾中找到 `Models` 。</span><span class="sxs-lookup"><span data-stu-id="41b64-142">In this sample, two classes are used: `Blog` and `Post` which can be found in the `Models` folder.</span></span>

<span data-ttu-id="41b64-143">模型類別僅由屬性所組成，而這些屬性是資料庫中的模型資料行。</span><span class="sxs-lookup"><span data-stu-id="41b64-143">The model classes are composed only of properties, which model columns in the database.</span></span>

* <span data-ttu-id="41b64-144">**Blog.cs**</span><span class="sxs-lookup"><span data-stu-id="41b64-144">**Blog.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Blog.cs)]

* <span data-ttu-id="41b64-145">`Posts`屬性會定義與之間的父子式關聯 `Blog` 性 `Post` 。</span><span class="sxs-lookup"><span data-stu-id="41b64-145">The `Posts` property defines a parent-child relationship between `Blog` and `Post`.</span></span>

* <span data-ttu-id="41b64-146">**Post.cs**</span><span class="sxs-lookup"><span data-stu-id="41b64-146">**Post.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Post.cs)]

* <span data-ttu-id="41b64-147">`BlogId`和 `Blog` 屬性會與實例的父物件產生關聯 `Blog` `Post` 。</span><span class="sxs-lookup"><span data-stu-id="41b64-147">The `BlogId` and `Blog` properties relate back to the parent `Blog` object for the instance of the `Post`.</span></span>

## <a name="data-context"></a><span data-ttu-id="41b64-148">資料內容</span><span class="sxs-lookup"><span data-stu-id="41b64-148">Data context</span></span>

<span data-ttu-id="41b64-149">`BloggingContext`類別位於 `Services` 資料夾中，並繼承自 EF Core `DbContext` 類別。</span><span class="sxs-lookup"><span data-stu-id="41b64-149">The `BloggingContext` class is located in the `Services` folder and inherits from the EF Core `DbContext` class.</span></span> <span data-ttu-id="41b64-150">`DbContext`是用來將資料庫查詢和變更群組在一起。</span><span class="sxs-lookup"><span data-stu-id="41b64-150">A `DbContext` is used to group together database queries and changes.</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Services/BloggingContext.cs)]

* <span data-ttu-id="41b64-151">此型別類別中的兩個屬性 `DbSet` 都是用來在代表 blog 和文章的基礎資料表上操作。</span><span class="sxs-lookup"><span data-stu-id="41b64-151">Both properties in this class of type `DbSet` are used to operate on the underlying tables representing Blogs and Posts.</span></span>
* <span data-ttu-id="41b64-152">在 `SQLitePCL.Batteries_V2.Init()` 要于 iOS 上起始 SQLite 的函式中，需要有。</span><span class="sxs-lookup"><span data-stu-id="41b64-152">The `SQLitePCL.Batteries_V2.Init()` is needed in the constructor to initiate SQLite on iOS.</span></span>
* <span data-ttu-id="41b64-153">函式會 `OnConfiguring` 在實體裝置上設定 SQLite 資料庫的位置。</span><span class="sxs-lookup"><span data-stu-id="41b64-153">The `OnConfiguring` function sets up the location of the SQLite database on the physical device.</span></span>

## <a name="create-read-update--delete"></a><span data-ttu-id="41b64-154">建立、讀取、更新及刪除</span><span class="sxs-lookup"><span data-stu-id="41b64-154">Create, read, update & delete</span></span>

<span data-ttu-id="41b64-155">以下是應用程式中的一些實例，其中 EF Core 用來存取 SQLite。</span><span class="sxs-lookup"><span data-stu-id="41b64-155">The following are some instances in the app where EF Core is used to access SQLite.</span></span>

### <a name="read"></a><span data-ttu-id="41b64-156">讀取</span><span class="sxs-lookup"><span data-stu-id="41b64-156">Read</span></span>

* <span data-ttu-id="41b64-157">傳回所有記錄。</span><span class="sxs-lookup"><span data-stu-id="41b64-157">Return all records.</span></span>
  * <span data-ttu-id="41b64-158">的函式會傳回 `OnAppearing` `BlogsPage.xaml.cs` 所有 `Blog` 記錄，並將其儲存在 `List` 變數中。</span><span class="sxs-lookup"><span data-stu-id="41b64-158">The `OnAppearing` function of `BlogsPage.xaml.cs` returns all `Blog` records and stores them into a `List` variable.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var theBlogs = blogContext.Blogs.ToList();
}
```

* <span data-ttu-id="41b64-159">傳回特定記錄。</span><span class="sxs-lookup"><span data-stu-id="41b64-159">Return specific records.</span></span>
  * <span data-ttu-id="41b64-160">的函式 `OnAppearing` `PostsPage.xaml.cs` 會傳回 `Post` 包含特定的記錄 `BlogId` 。</span><span class="sxs-lookup"><span data-stu-id="41b64-160">The `OnAppearing` function of `PostsPage.xaml.cs` returns `Post` records that contain a specific `BlogId`.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var postList = blogContext.Posts
        .Where(p => p.BlogId == BlogId)
        .ToList();
}
```

### <a name="create"></a><span data-ttu-id="41b64-161">建立</span><span class="sxs-lookup"><span data-stu-id="41b64-161">Create</span></span>

* <span data-ttu-id="41b64-162">插入新的記錄。</span><span class="sxs-lookup"><span data-stu-id="41b64-162">Insert a new record.</span></span>
  * <span data-ttu-id="41b64-163">的 `Save_Clicked` 功能會將 `AddBlogPage.xaml.cs` 新的 `Blog` 物件插入 SQLite 資料庫。</span><span class="sxs-lookup"><span data-stu-id="41b64-163">The `Save_Clicked` function of `AddBlogPage.xaml.cs` inserts a new `Blog` object into the SQLite database.</span></span>

```csharp
var blog = new Blog { Url = blogUrl.Text };

using (var blogContext = new BloggingContext())
{
    blogContext.Add(blog);

    await blogContext.SaveChangesAsync();
}
```

### <a name="update"></a><span data-ttu-id="41b64-164">更新</span><span class="sxs-lookup"><span data-stu-id="41b64-164">Update</span></span>

* <span data-ttu-id="41b64-165">更新現有的記錄。</span><span class="sxs-lookup"><span data-stu-id="41b64-165">Update an existing record.</span></span>
  * <span data-ttu-id="41b64-166">的 `Save_Clicked` 功能會 `AddPostPage.xaml.cs` 使用新的來更新現有的 `Blog` 物件 `Post` 。</span><span class="sxs-lookup"><span data-stu-id="41b64-166">The `Save_Clicked` function of `AddPostPage.xaml.cs` updates an existing `Blog` object with a new `Post`.</span></span>
  
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

### <a name="delete"></a><span data-ttu-id="41b64-167">刪除</span><span class="sxs-lookup"><span data-stu-id="41b64-167">Delete</span></span>

* <span data-ttu-id="41b64-168">刪除具有 cascade 至子記錄的所有記錄。</span><span class="sxs-lookup"><span data-stu-id="41b64-168">Delete all records with cascade to child records.</span></span>
  * <span data-ttu-id="41b64-169">的函式會 `DeleteAll_Clicked` `BlogsPage.xaml.cs` 刪除 `Blog` SQLite 資料庫中的所有記錄，並將刪除功能串聯至所有 `Blog` 子 `Post` 記錄。</span><span class="sxs-lookup"><span data-stu-id="41b64-169">The `DeleteAll_Clicked` function of `BlogsPage.xaml.cs` deletes all the `Blog` records in the SQLite database and cascades the deletes to all of the `Blog` child `Post` records.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    blogContext.RemoveRange(blogContext.Blogs);

    await blogContext.SaveChangesAsync();
}
```

## <a name="next-steps"></a><span data-ttu-id="41b64-170">後續步驟</span><span class="sxs-lookup"><span data-stu-id="41b64-170">Next steps</span></span>

<span data-ttu-id="41b64-171">在此使用者入門中，您已瞭解如何使用 Entity Framework Core，來透過 Xamarin 應用程式存取 SQLite 資料庫。</span><span class="sxs-lookup"><span data-stu-id="41b64-171">In this getting started you have learned how to use a Xamarin.Forms application to access a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="41b64-172">其他與 Xamarin 開發人員相關的 Entity Framework Core 主題：</span><span class="sxs-lookup"><span data-stu-id="41b64-172">Other Entity Framework Core topics of interest to Xamarin developers:</span></span>

* [<span data-ttu-id="41b64-173">設定`DbContext`</span><span class="sxs-lookup"><span data-stu-id="41b64-173">Configuring a `DbContext`</span></span>](xref:core/miscellaneous/configuring-dbcontext)
* <span data-ttu-id="41b64-174">深入了解 [LINQ 查詢運算式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span><span class="sxs-lookup"><span data-stu-id="41b64-174">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="41b64-175">[設定模型](xref:core/modeling/index)以指定如[必要](xref:core/modeling/entity-properties#required-and-optional-properties)和[最大長度](xref:core/modeling/entity-properties#maximum-length)之類的內容</span><span class="sxs-lookup"><span data-stu-id="41b64-175">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
