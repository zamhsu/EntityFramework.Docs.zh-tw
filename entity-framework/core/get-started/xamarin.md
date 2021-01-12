---
title: 使用 EF Core 和 Xamarin EF Core 的消費者入門
description: 使用 Xamarin 建立行動裝置應用程式的使用者入門教學課程 Entity Framework Core
author: codemillmatt
ms.date: 07/07/2020
ms.author: masoucou
uid: core/get-started/xamarin
ms.openlocfilehash: c19c70903926c2c8ff4b51cca7781859af7e3567
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128780"
---
# <a name="getting-started-with-ef-core-and-xamarin"></a><span data-ttu-id="89655-103">使用 EF Core 和 Xamarin 消費者入門</span><span class="sxs-lookup"><span data-stu-id="89655-103">Getting Started with EF Core and Xamarin</span></span>

<span data-ttu-id="89655-104">在本教學課程中，您會建立一個 [Xamarin. Forms](/xamarin/get-started/what-is-xamarin-forms) 應用程式，該應用程式會使用 Entity Framework Core 對 SQLite 資料庫執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="89655-104">In this tutorial, you create a [Xamarin.Forms](/xamarin/get-started/what-is-xamarin-forms) application that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="89655-105">您可以使用 Windows 上的 Visual Studio 或 Visual Studio for Mac 來遵循教學課程。</span><span class="sxs-lookup"><span data-stu-id="89655-105">You can follow the tutorial by using Visual Studio on Windows or Visual Studio for Mac.</span></span>

> [!TIP]
> <span data-ttu-id="89655-106">您可以檢視本文中的 [GitHut 範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin)。</span><span class="sxs-lookup"><span data-stu-id="89655-106">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="89655-107">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="89655-107">Prerequisites</span></span>

<span data-ttu-id="89655-108">安裝下列其中一項：</span><span class="sxs-lookup"><span data-stu-id="89655-108">Install one of the below:</span></span>

* <span data-ttu-id="89655-109">使用此工作負載[Visual Studio 2019 16.3 版或更新版本](https://www.visualstudio.com/downloads/)：</span><span class="sxs-lookup"><span data-stu-id="89655-109">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this workload:</span></span>
  * <span data-ttu-id="89655-110">**使用 .NET 的行動裝置程式開發**</span><span class="sxs-lookup"><span data-stu-id="89655-110">**Mobile Development with .NET**</span></span>
* [<span data-ttu-id="89655-111">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="89655-111">Visual Studio for Mac</span></span>](https://visualstudio.microsoft.com/vs/mac/)

<span data-ttu-id="89655-112">本檔會針對每個平臺 [提供詳細的逐步安裝指示](/xamarin/get-started/installation) 。</span><span class="sxs-lookup"><span data-stu-id="89655-112">This [documentation provides detailed step-by-step installation instructions](/xamarin/get-started/installation) for each platform.</span></span>

## <a name="download-and-run-the-sample-project"></a><span data-ttu-id="89655-113">下載並執行範例專案</span><span class="sxs-lookup"><span data-stu-id="89655-113">Download and run the sample project</span></span>

<span data-ttu-id="89655-114">若要執行及探索此範例應用程式，請下載 GitHub 上的程式碼。</span><span class="sxs-lookup"><span data-stu-id="89655-114">To run and explore this sample application, download the code on GitHub.</span></span>

<span data-ttu-id="89655-115">下載之後，請在 Visual Studio 中開啟方案檔， `EFGettingStarted.sln` 或 Visual Studio for Mac，然後在您選擇的平臺上執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="89655-115">Once downloaded, open the solution file `EFGettingStarted.sln` in Visual Studio or Visual Studio for Mac and run the application on the platform of your choice.</span></span>

<span data-ttu-id="89655-116">當應用程式第一次啟動時，它會在本機 SQLite 資料庫中填入兩個代表 blog 的專案。</span><span class="sxs-lookup"><span data-stu-id="89655-116">When the app first starts, it will populate the local SQLite database with two entries representing blogs.</span></span>

![所有 blog 清單頁面的螢幕擷取畫面](_static/xamarin-tutorial-1.png)

<span data-ttu-id="89655-118">按一下工具列中的 [ **加入** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="89655-118">Click the **Add** button in the toolbar.</span></span>

<span data-ttu-id="89655-119">將會出現新的頁面，讓您輸入新的 blog 的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="89655-119">A new page will appear that allows you to enter information about a new blog.</span></span>

![新 blog [編輯] 頁面的螢幕擷取畫面](_static/xamarin-tutorial-2.png)

<span data-ttu-id="89655-121">填寫所有資訊，然後按一下工具列中的 [ **儲存** ]。</span><span class="sxs-lookup"><span data-stu-id="89655-121">Fill out all the info and click **Save** from the toolbar.</span></span> <span data-ttu-id="89655-122">新的 blog 會儲存至應用程式的 SQLite 資料庫，並顯示在清單中。</span><span class="sxs-lookup"><span data-stu-id="89655-122">The new blog will save to the app's SQLite database and will show in the list.</span></span>

<span data-ttu-id="89655-123">您可以按一下清單中的其中一個 blog 專案，查看該 blog 的任何文章。</span><span class="sxs-lookup"><span data-stu-id="89655-123">You can click on one of the blog entries in the list and see any posts for that blog.</span></span>

![[Blog] 文章清單頁面的螢幕擷取畫面](_static/xamarin-tutorial-3.png)

<span data-ttu-id="89655-125">在工具列中按一下 [ **加入** ]。</span><span class="sxs-lookup"><span data-stu-id="89655-125">Click **Add** in the toolbar.</span></span>

<span data-ttu-id="89655-126">接著會出現一個頁面，讓您填寫新的 blog 貼文的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="89655-126">A page then appears that allows you to fill out information about a new blog post.</span></span>

![[新增貼文] 頁面的螢幕擷取畫面](_static/xamarin-tutorial-4.png)

<span data-ttu-id="89655-128">填寫所有資訊，然後按一下工具列中的 [ **儲存** ]。</span><span class="sxs-lookup"><span data-stu-id="89655-128">Fill out all the information and click **Save** in the toolbar.</span></span>

<span data-ttu-id="89655-129">新的貼文將會與您在上一個步驟中按一下的 blog 文章相關聯，並會儲存至應用程式的 SQLite 資料庫，並顯示在清單中。</span><span class="sxs-lookup"><span data-stu-id="89655-129">The new post will be associated to the blog post you clicked on in a previous step and will save to the app's SQLite database and show in the list.</span></span>

<span data-ttu-id="89655-130">返回至 [blog 清單] 頁面。</span><span class="sxs-lookup"><span data-stu-id="89655-130">Go back to the blog list page.</span></span> <span data-ttu-id="89655-131">並按一下工具列中的 [ **全部刪除** ]。</span><span class="sxs-lookup"><span data-stu-id="89655-131">And click **Delete All** in the toolbar.</span></span> <span data-ttu-id="89655-132">然後，將會從應用程式的 SQLite 資料庫中刪除所有的 blog 和其對應的貼文。</span><span class="sxs-lookup"><span data-stu-id="89655-132">All blogs and their corresponding posts will then be deleted from the app's SQLite database.</span></span>

![已刪除所有 blog 的應用程式螢幕擷取畫面](_static/xamarin-tutorial-5.png)

## <a name="explore-the-code"></a><span data-ttu-id="89655-134">探索程式碼</span><span class="sxs-lookup"><span data-stu-id="89655-134">Explore the code</span></span>

<span data-ttu-id="89655-135">下列各節將逐步引導您完成範例專案中的程式碼，該程式碼會使用 EF Core 搭配 Xamarin，從 SQLite 資料庫讀取、建立、更新及刪除資料。</span><span class="sxs-lookup"><span data-stu-id="89655-135">The following sections will walk you through the code in the sample project that reads, creates, updates, and deletes data from a SQLite database using EF Core with Xamarin.Forms.</span></span>

<span data-ttu-id="89655-136">假設您已經熟悉 [顯示資料](/xamarin/xamarin-forms/app-fundamentals/data-binding/) 並在 [頁面之間流覽](/xamarin/xamarin-forms/app-fundamentals/navigation/)的 Xamarin. Forms 主題。</span><span class="sxs-lookup"><span data-stu-id="89655-136">It is assumed that you are familiar with the Xamarin.Forms topics of [displaying data](/xamarin/xamarin-forms/app-fundamentals/data-binding/) and [navigating between pages](/xamarin/xamarin-forms/app-fundamentals/navigation/).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="89655-137">Entity Framework Core 使用反映來叫用 Xamarin 連結器可能在 **發行** 模式設定中去除的函式。</span><span class="sxs-lookup"><span data-stu-id="89655-137">Entity Framework Core uses reflection to invoke functions which the Xamarin.iOS linker may strip out while in **Release** mode configurations.</span></span> <span data-ttu-id="89655-138">您可以透過兩種方式來避免這種情況。</span><span class="sxs-lookup"><span data-stu-id="89655-138">You can avoid that in one of two ways.</span></span>
>
> * <span data-ttu-id="89655-139">第一個方法是將新增 `--linkskip System.Core` 至 **iOS 組建** 選項中的 **其他 mtouch 引數**。</span><span class="sxs-lookup"><span data-stu-id="89655-139">The first is to add `--linkskip System.Core` to the **Additional mtouch arguments** in the **iOS Build** options.</span></span>
> * <span data-ttu-id="89655-140">或者，  `Don't Link` 在 **ios 組建** 選項中將 [Xamarin] 連結器行為設定為。</span><span class="sxs-lookup"><span data-stu-id="89655-140">Alternatively set the Xamarin.iOS **Linker behavior** to `Don't Link` in the **iOS Build** options.</span></span>
> <span data-ttu-id="89655-141">本文將[詳細說明 xamarin 連結器](/xamarin/ios/deploy-test/linker)，包括如何在 xamarin 上設定行為。</span><span class="sxs-lookup"><span data-stu-id="89655-141">[This article explains more about the Xamarin.iOS linker](/xamarin/ios/deploy-test/linker) including how to set the behavior on Xamarin.iOS.</span></span>
>

## <a name="entity-framework-core-nuget-packages"></a><span data-ttu-id="89655-142">Entity Framework Core NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="89655-142">Entity Framework Core NuGet packages</span></span>

<span data-ttu-id="89655-143">若要使用 EF Core 建立 Xamarin 的 Forms 應用程式，請為 EF Core 資料庫提供 (者安裝套件，) 您想要以 Xamarin 方案中的所有專案為目標。</span><span class="sxs-lookup"><span data-stu-id="89655-143">To create Xamarin.Forms apps with EF Core, you install the package for the EF Core database provider(s) you want to target into all of the projects in the Xamarin.Forms solution.</span></span> <span data-ttu-id="89655-144">本教學課程使用 SQLite 提供者。</span><span class="sxs-lookup"><span data-stu-id="89655-144">This tutorial uses the SQLite provider.</span></span>

<span data-ttu-id="89655-145">在 Xamarin 方案中的每個專案都需要下列 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="89655-145">The following NuGet package is needed in each of the projects in the Xamarin.Forms solution.</span></span>

* `Microsoft.EntityFrameworkCore.Sqlite`

## <a name="model-classes"></a><span data-ttu-id="89655-146">模型類別</span><span class="sxs-lookup"><span data-stu-id="89655-146">Model classes</span></span>

<span data-ttu-id="89655-147">在 SQLite 資料庫中透過 EF Core 存取的每個資料表都會在類別中模型化。</span><span class="sxs-lookup"><span data-stu-id="89655-147">Each table in the SQLite database accessed through EF Core is modeled in a class.</span></span> <span data-ttu-id="89655-148">在此範例中，會使用兩個類別： `Blog` 以及 `Post` 可在資料夾中找到的類別 `Models` 。</span><span class="sxs-lookup"><span data-stu-id="89655-148">In this sample, two classes are used: `Blog` and `Post` which can be found in the `Models` folder.</span></span>

<span data-ttu-id="89655-149">模型類別只是由屬性所組成，而這些屬性是資料庫中的模型資料行。</span><span class="sxs-lookup"><span data-stu-id="89655-149">The model classes are composed only of properties, which model columns in the database.</span></span>

* <span data-ttu-id="89655-150">**Blog.cs**</span><span class="sxs-lookup"><span data-stu-id="89655-150">**Blog.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Blog.cs)]

* <span data-ttu-id="89655-151">`Posts`屬性定義和之間的父子式關聯性 `Blog` `Post` 。</span><span class="sxs-lookup"><span data-stu-id="89655-151">The `Posts` property defines a parent-child relationship between `Blog` and `Post`.</span></span>

* <span data-ttu-id="89655-152">**Post.cs**</span><span class="sxs-lookup"><span data-stu-id="89655-152">**Post.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Post.cs)]

* <span data-ttu-id="89655-153">`BlogId`和 `Blog` 屬性會與實例的父物件建立關聯 `Blog` `Post` 。</span><span class="sxs-lookup"><span data-stu-id="89655-153">The `BlogId` and `Blog` properties relate back to the parent `Blog` object for the instance of the `Post`.</span></span>

## <a name="data-context"></a><span data-ttu-id="89655-154">資料內容</span><span class="sxs-lookup"><span data-stu-id="89655-154">Data context</span></span>

<span data-ttu-id="89655-155">`BloggingContext`類別位於 `Services` 資料夾中，並繼承自 EF Core `DbContext` 類別。</span><span class="sxs-lookup"><span data-stu-id="89655-155">The `BloggingContext` class is located in the `Services` folder and inherits from the EF Core `DbContext` class.</span></span> <span data-ttu-id="89655-156">`DbContext`用來將資料庫查詢與變更群組在一起。</span><span class="sxs-lookup"><span data-stu-id="89655-156">A `DbContext` is used to group together database queries and changes.</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Services/BloggingContext.cs)]

* <span data-ttu-id="89655-157">此類型類別中的這兩個屬性 `DbSet` 都是用來在代表 blog 和貼文的基礎資料表上運作。</span><span class="sxs-lookup"><span data-stu-id="89655-157">Both properties in this class of type `DbSet` are used to operate on the underlying tables representing Blogs and Posts.</span></span>
* <span data-ttu-id="89655-158">在 `SQLitePCL.Batteries_V2.Init()` iOS 上起始 SQLite 的函式需要此項。</span><span class="sxs-lookup"><span data-stu-id="89655-158">The `SQLitePCL.Batteries_V2.Init()` is needed in the constructor to initiate SQLite on iOS.</span></span>
* <span data-ttu-id="89655-159">此 `OnConfiguring` 函數會在實體裝置上設定 SQLite 資料庫的位置。</span><span class="sxs-lookup"><span data-stu-id="89655-159">The `OnConfiguring` function sets up the location of the SQLite database on the physical device.</span></span>

## <a name="create-read-update--delete"></a><span data-ttu-id="89655-160">建立、讀取、更新及刪除</span><span class="sxs-lookup"><span data-stu-id="89655-160">Create, read, update & delete</span></span>

<span data-ttu-id="89655-161">以下是應用程式中 EF Core 用來存取 SQLite 的一些實例。</span><span class="sxs-lookup"><span data-stu-id="89655-161">The following are some instances in the app where EF Core is used to access SQLite.</span></span>

### <a name="read"></a><span data-ttu-id="89655-162">讀取</span><span class="sxs-lookup"><span data-stu-id="89655-162">Read</span></span>

* <span data-ttu-id="89655-163">傳回所有記錄。</span><span class="sxs-lookup"><span data-stu-id="89655-163">Return all records.</span></span>
  * <span data-ttu-id="89655-164">的函式會傳回 `OnAppearing` `BlogsPage.xaml.cs` 所有 `Blog` 記錄，並將其儲存在 `List` 變數中。</span><span class="sxs-lookup"><span data-stu-id="89655-164">The `OnAppearing` function of `BlogsPage.xaml.cs` returns all `Blog` records and stores them into a `List` variable.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var theBlogs = blogContext.Blogs.ToList();
}
```

* <span data-ttu-id="89655-165">傳回特定記錄。</span><span class="sxs-lookup"><span data-stu-id="89655-165">Return specific records.</span></span>
  * <span data-ttu-id="89655-166">的函式 `OnAppearing` `PostsPage.xaml.cs` 會傳回 `Post` 包含特定的記錄 `BlogId` 。</span><span class="sxs-lookup"><span data-stu-id="89655-166">The `OnAppearing` function of `PostsPage.xaml.cs` returns `Post` records that contain a specific `BlogId`.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var postList = blogContext.Posts
        .Where(p => p.BlogId == BlogId)
        .ToList();
}
```

### <a name="create"></a><span data-ttu-id="89655-167">建立</span><span class="sxs-lookup"><span data-stu-id="89655-167">Create</span></span>

* <span data-ttu-id="89655-168">插入新的記錄。</span><span class="sxs-lookup"><span data-stu-id="89655-168">Insert a new record.</span></span>
  * <span data-ttu-id="89655-169">將 `Save_Clicked` `AddBlogPage.xaml.cs` 新 `Blog` 物件插入 SQLite 資料庫的功能。</span><span class="sxs-lookup"><span data-stu-id="89655-169">The `Save_Clicked` function of `AddBlogPage.xaml.cs` inserts a new `Blog` object into the SQLite database.</span></span>

```csharp
var blog = new Blog { Url = blogUrl.Text };

using (var blogContext = new BloggingContext())
{
    blogContext.Add(blog);

    await blogContext.SaveChangesAsync();
}
```

### <a name="update"></a><span data-ttu-id="89655-170">更新</span><span class="sxs-lookup"><span data-stu-id="89655-170">Update</span></span>

* <span data-ttu-id="89655-171">更新現有的記錄。</span><span class="sxs-lookup"><span data-stu-id="89655-171">Update an existing record.</span></span>
  * <span data-ttu-id="89655-172">`Save_Clicked`的函式會以新的來 `AddPostPage.xaml.cs` 更新現有的 `Blog` 物件 `Post` 。</span><span class="sxs-lookup"><span data-stu-id="89655-172">The `Save_Clicked` function of `AddPostPage.xaml.cs` updates an existing `Blog` object with a new `Post`.</span></span>

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

### <a name="delete"></a><span data-ttu-id="89655-173">刪除</span><span class="sxs-lookup"><span data-stu-id="89655-173">Delete</span></span>

* <span data-ttu-id="89655-174">刪除具有 cascade 至子記錄的所有記錄。</span><span class="sxs-lookup"><span data-stu-id="89655-174">Delete all records with cascade to child records.</span></span>
  * <span data-ttu-id="89655-175">的函式會 `DeleteAll_Clicked` `BlogsPage.xaml.cs` 刪除 `Blog` SQLite 資料庫中的所有記錄，並將刪除程式集中至所有 `Blog` 子 `Post` 記錄。</span><span class="sxs-lookup"><span data-stu-id="89655-175">The `DeleteAll_Clicked` function of `BlogsPage.xaml.cs` deletes all the `Blog` records in the SQLite database and cascades the deletes to all of the `Blog` child `Post` records.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    blogContext.RemoveRange(blogContext.Blogs);

    await blogContext.SaveChangesAsync();
}
```

## <a name="next-steps"></a><span data-ttu-id="89655-176">後續步驟</span><span class="sxs-lookup"><span data-stu-id="89655-176">Next steps</span></span>

<span data-ttu-id="89655-177">在此快速入門中，您已瞭解如何使用 Xamarin 應用程式，利用 Entity Framework Core 來存取 SQLite 資料庫。</span><span class="sxs-lookup"><span data-stu-id="89655-177">In this getting started you have learned how to use a Xamarin.Forms application to access a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="89655-178">其他與 Xamarin 開發人員相關的 Entity Framework Core 主題：</span><span class="sxs-lookup"><span data-stu-id="89655-178">Other Entity Framework Core topics of interest to Xamarin developers:</span></span>

* [<span data-ttu-id="89655-179">設定 `DbContext`</span><span class="sxs-lookup"><span data-stu-id="89655-179">Configuring a `DbContext`</span></span>](xref:core/dbcontext-configuration/index)
* <span data-ttu-id="89655-180">深入了解 [LINQ 查詢運算式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span><span class="sxs-lookup"><span data-stu-id="89655-180">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="89655-181">[設定模型](xref:core/modeling/index)以指定如[必要](xref:core/modeling/entity-properties#required-and-optional-properties)和[最大長度](xref:core/modeling/entity-properties#maximum-length)之類的內容</span><span class="sxs-lookup"><span data-stu-id="89655-181">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
