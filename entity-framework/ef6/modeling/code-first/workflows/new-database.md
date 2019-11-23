---
title: Code First 至新的資料庫-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 2df6cb0a-7d8b-4e28-9d05-e2b9a90125af
ms.openlocfilehash: d540fc6e84049f345ae22998f94c309e0be73fc3
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182576"
---
# <a name="code-first-to-a-new-database"></a><span data-ttu-id="23df0-102">Code First 至新的資料庫</span><span class="sxs-lookup"><span data-stu-id="23df0-102">Code First to a New Database</span></span>
<span data-ttu-id="23df0-103">這段影片和逐步解說提供以新資料庫為目標之 Code First 開發的簡介。</span><span class="sxs-lookup"><span data-stu-id="23df0-103">This video and step-by-step walkthrough provide an introduction to Code First development targeting a new database.</span></span> <span data-ttu-id="23df0-104">此案例包含的目標資料庫不存在且 Code First 將會建立，或是 Code First 會加入新資料表的空資料庫。</span><span class="sxs-lookup"><span data-stu-id="23df0-104">This scenario includes targeting a database that doesn’t exist and Code First will create, or an empty database that Code First will add new tables to.</span></span> <span data-ttu-id="23df0-105">Code First 可讓您使用 C\# 或 VB.Net 類別來定義模型。</span><span class="sxs-lookup"><span data-stu-id="23df0-105">Code First allows you to define your model using C\# or VB.Net classes.</span></span> <span data-ttu-id="23df0-106">您可以選擇性地使用類別和屬性上的屬性，或使用 Fluent API 來執行其他設定。</span><span class="sxs-lookup"><span data-stu-id="23df0-106">Additional configuration can optionally be performed using attributes on your classes and properties or by using a fluent API.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="23df0-107">觀看影片</span><span class="sxs-lookup"><span data-stu-id="23df0-107">Watch the video</span></span>
<span data-ttu-id="23df0-108">這段影片提供以新資料庫為目標之 Code First 開發的簡介。</span><span class="sxs-lookup"><span data-stu-id="23df0-108">This video provides an introduction to Code First development targeting a new database.</span></span> <span data-ttu-id="23df0-109">此案例包含的目標資料庫不存在且 Code First 將會建立，或是 Code First 會加入新資料表的空資料庫。</span><span class="sxs-lookup"><span data-stu-id="23df0-109">This scenario includes targeting a database that doesn’t exist and Code First will create, or an empty database that Code First will add new tables to.</span></span> <span data-ttu-id="23df0-110">Code First 可讓您使用C#或 VB.Net 類別來定義您的模型。</span><span class="sxs-lookup"><span data-stu-id="23df0-110">Code First allows you to define your model using C# or VB.Net classes.</span></span> <span data-ttu-id="23df0-111">您可以選擇性地使用類別和屬性上的屬性，或使用 Fluent API 來執行其他設定。</span><span class="sxs-lookup"><span data-stu-id="23df0-111">Additional configuration can optionally be performed using attributes on your classes and properties or by using a fluent API.</span></span>

<span data-ttu-id="23df0-112">**主講人**[Rowan Miller](https://romiller.com/)</span><span class="sxs-lookup"><span data-stu-id="23df0-112">**Presented By**: [Rowan Miller](https://romiller.com/)</span></span>

<span data-ttu-id="23df0-113">**影片**： [wmv](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.wmv) | [.wmv](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-mp4Video-CodeFirstNewDatabase.m4v) | [wmv （ZIP）](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.zip)</span><span class="sxs-lookup"><span data-stu-id="23df0-113">**Video**: [WMV](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.wmv) | [MP4](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-mp4Video-CodeFirstNewDatabase.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="23df0-114">先決條件</span><span class="sxs-lookup"><span data-stu-id="23df0-114">Pre-Requisites</span></span>

<span data-ttu-id="23df0-115">您至少必須安裝 Visual Studio 2010 或 Visual Studio 2012，才能完成此逐步解說。</span><span class="sxs-lookup"><span data-stu-id="23df0-115">You will need to have at least Visual Studio 2010 or Visual Studio 2012 installed to complete this walkthrough.</span></span>

<span data-ttu-id="23df0-116">如果您使用 Visual Studio 2010，您也必須安裝[NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) 。</span><span class="sxs-lookup"><span data-stu-id="23df0-116">If you are using Visual Studio 2010, you will also need to have [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installed.</span></span>

## <a name="1-create-the-application"></a><span data-ttu-id="23df0-117">1. 建立應用程式</span><span class="sxs-lookup"><span data-stu-id="23df0-117">1. Create the Application</span></span>

<span data-ttu-id="23df0-118">為了簡單起見，我們將建立一個使用 Code First 來執行資料存取的基本主控台應用程式。</span><span class="sxs-lookup"><span data-stu-id="23df0-118">To keep things simple we’re going to build a basic console application that uses Code First to perform data access.</span></span>

-   <span data-ttu-id="23df0-119">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23df0-119">Open Visual Studio</span></span>
-   <span data-ttu-id="23df0-120">**檔案&gt; 新&gt; 專案 。**</span><span class="sxs-lookup"><span data-stu-id="23df0-120">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="23df0-121">從左側功能表和**主控台應用程式**中選取 [ **Windows** ]</span><span class="sxs-lookup"><span data-stu-id="23df0-121">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="23df0-122">輸入**CodeFirstNewDatabaseSample**作為名稱</span><span class="sxs-lookup"><span data-stu-id="23df0-122">Enter **CodeFirstNewDatabaseSample** as the name</span></span>
-   <span data-ttu-id="23df0-123">選取 [確定]</span><span class="sxs-lookup"><span data-stu-id="23df0-123">Select **OK**</span></span>

## <a name="2-create-the-model"></a><span data-ttu-id="23df0-124">2. 建立模型</span><span class="sxs-lookup"><span data-stu-id="23df0-124">2. Create the Model</span></span>

<span data-ttu-id="23df0-125">讓我們使用類別來定義一個非常簡單的模型。</span><span class="sxs-lookup"><span data-stu-id="23df0-125">Let’s define a very simple model using classes.</span></span> <span data-ttu-id="23df0-126">我們只是在 Program.cs 檔案中定義它們，但在真實世界的應用程式中，您會將類別分割成不同的檔案，而且可能是個別的專案。</span><span class="sxs-lookup"><span data-stu-id="23df0-126">We’re just defining them in the Program.cs file but in a real world application you would split your classes out into separate files and potentially a separate project.</span></span>

<span data-ttu-id="23df0-127">在 Program.cs 中的 Program 類別定義底下，新增下列兩個類別。</span><span class="sxs-lookup"><span data-stu-id="23df0-127">Below the Program class definition in Program.cs add the following two classes.</span></span>

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }

    public virtual List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public int BlogId { get; set; }
    public virtual Blog Blog { get; set; }
}
```

<span data-ttu-id="23df0-128">您會注意到，我們會將這兩個導覽屬性（Blog 和文章 Blog）虛擬。</span><span class="sxs-lookup"><span data-stu-id="23df0-128">You’ll notice that we’re making the two navigation properties (Blog.Posts and Post.Blog) virtual.</span></span> <span data-ttu-id="23df0-129">這會啟用 Entity Framework 的消極式載入功能。</span><span class="sxs-lookup"><span data-stu-id="23df0-129">This enables the Lazy Loading feature of Entity Framework.</span></span> <span data-ttu-id="23df0-130">消極式載入表示這些屬性的內容會在您嘗試存取時，自動從資料庫載入。</span><span class="sxs-lookup"><span data-stu-id="23df0-130">Lazy Loading means that the contents of these properties will be automatically loaded from the database when you try to access them.</span></span>

## <a name="3-create-a-context"></a><span data-ttu-id="23df0-131">3. 建立內容</span><span class="sxs-lookup"><span data-stu-id="23df0-131">3. Create a Context</span></span>

<span data-ttu-id="23df0-132">現在可以定義衍生的內容，代表與資料庫的會話，讓我們能夠查詢和儲存資料。</span><span class="sxs-lookup"><span data-stu-id="23df0-132">Now it’s time to define a derived context, which represents a session with the database, allowing us to query and save data.</span></span> <span data-ttu-id="23df0-133">我們會定義衍生自 DbCoNtext 的內容，並針對模型中的每個類別公開具類型的 DbSet&lt;TEntity&gt;。</span><span class="sxs-lookup"><span data-stu-id="23df0-133">We define a context that derives from System.Data.Entity.DbContext and exposes a typed DbSet&lt;TEntity&gt; for each class in our model.</span></span>

<span data-ttu-id="23df0-134">我們現在開始使用 Entity Framework 的類型，因此我們需要新增 EntityFramework NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="23df0-134">We’re now starting to use types from the Entity Framework so we need to add the EntityFramework NuGet package.</span></span>

-   <span data-ttu-id="23df0-135">**專案–&gt; 管理 NuGet 套件 。**</span><span class="sxs-lookup"><span data-stu-id="23df0-135">**Project –&gt; Manage NuGet Packages…**</span></span>
    <span data-ttu-id="23df0-136">注意：如果您沒有 [**管理 NuGet 套件 ...** ]</span><span class="sxs-lookup"><span data-stu-id="23df0-136">Note: If you don’t have the **Manage NuGet Packages…**</span></span> <span data-ttu-id="23df0-137">選項您應該安裝[最新版的 NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)</span><span class="sxs-lookup"><span data-stu-id="23df0-137">option you should install the [latest version of NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)</span></span>
-   <span data-ttu-id="23df0-138">選取 [**線上**] 索引標籤</span><span class="sxs-lookup"><span data-stu-id="23df0-138">Select the **Online** tab</span></span>
-   <span data-ttu-id="23df0-139">選取**EntityFramework**套件</span><span class="sxs-lookup"><span data-stu-id="23df0-139">Select the **EntityFramework** package</span></span>
-   <span data-ttu-id="23df0-140">按一下 [**安裝**]</span><span class="sxs-lookup"><span data-stu-id="23df0-140">Click **Install**</span></span>

<span data-ttu-id="23df0-141">在 Program.cs 頂端，為 system.string 新增 using 語句。</span><span class="sxs-lookup"><span data-stu-id="23df0-141">Add a using statement for System.Data.Entity at the top of Program.cs.</span></span>

``` csharp
using System.Data.Entity;
```

<span data-ttu-id="23df0-142">在 Program.cs 中的 Post 類別底下，新增下列衍生內容。</span><span class="sxs-lookup"><span data-stu-id="23df0-142">Below the Post class in Program.cs add the following derived context.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```

<span data-ttu-id="23df0-143">以下是 Program.cs 現在應該包含的完整清單。</span><span class="sxs-lookup"><span data-stu-id="23df0-143">Here is a complete listing of what Program.cs should now contain.</span></span>

``` csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data.Entity;

namespace CodeFirstNewDatabaseSample
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }

    public class Blog
    {
        public int BlogId { get; set; }
        public string Name { get; set; }

        public virtual List<Post> Posts { get; set; }
    }

    public class Post
    {
        public int PostId { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }

    public class BloggingContext : DbContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }
    }
}
```

<span data-ttu-id="23df0-144">這就是我們開始儲存和抓取資料所需的所有程式碼。</span><span class="sxs-lookup"><span data-stu-id="23df0-144">That is all the code we need to start storing and retrieving data.</span></span> <span data-ttu-id="23df0-145">很明顯地，我們會在幕後討論一下，但我們先看看它的運作情形。</span><span class="sxs-lookup"><span data-stu-id="23df0-145">Obviously there is quite a bit going on behind the scenes and we’ll take a look at that in a moment but first let’s see it in action.</span></span>

## <a name="4-reading--writing-data"></a><span data-ttu-id="23df0-146">4. 讀取 & 寫入資料</span><span class="sxs-lookup"><span data-stu-id="23df0-146">4. Reading & Writing Data</span></span>

<span data-ttu-id="23df0-147">在 Program.cs 中執行 Main 方法，如下所示。</span><span class="sxs-lookup"><span data-stu-id="23df0-147">Implement the Main method in Program.cs as shown below.</span></span> <span data-ttu-id="23df0-148">此程式碼會建立內容的新實例，然後使用它來插入新的 Blog。</span><span class="sxs-lookup"><span data-stu-id="23df0-148">This code creates a new instance of our context and then uses it to insert a new Blog.</span></span> <span data-ttu-id="23df0-149">然後，它會使用 LINQ 查詢來抓取資料庫中依標題字母順序排序的所有 Blog。</span><span class="sxs-lookup"><span data-stu-id="23df0-149">Then it uses a LINQ query to retrieve all Blogs from the database ordered alphabetically by Title.</span></span>

``` csharp
class Program
{
    static void Main(string[] args)
    {
        using (var db = new BloggingContext())
        {
            // Create and save a new Blog
            Console.Write("Enter a name for a new Blog: ");
            var name = Console.ReadLine();

            var blog = new Blog { Name = name };
            db.Blogs.Add(blog);
            db.SaveChanges();

            // Display all Blogs from the database
            var query = from b in db.Blogs
                        orderby b.Name
                        select b;

            Console.WriteLine("All blogs in the database:");
            foreach (var item in query)
            {
                Console.WriteLine(item.Name);
            }

            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

<span data-ttu-id="23df0-150">您現在可以執行應用程式並加以測試。</span><span class="sxs-lookup"><span data-stu-id="23df0-150">You can now run the application and test it out.</span></span>

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
### <a name="wheres-my-data"></a><span data-ttu-id="23df0-151">我的資料在哪裡？</span><span class="sxs-lookup"><span data-stu-id="23df0-151">Where’s My Data?</span></span>

<span data-ttu-id="23df0-152">依照慣例，DbCoNtext 已為您建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="23df0-152">By convention DbContext has created a database for you.</span></span>

-   <span data-ttu-id="23df0-153">如果可以使用本機 SQL Express 實例（預設會隨 Visual Studio 2010 安裝），則 Code First 已在該實例上建立資料庫</span><span class="sxs-lookup"><span data-stu-id="23df0-153">If a local SQL Express instance is available (installed by default with Visual Studio 2010) then Code First has created the database on that instance</span></span>
-   <span data-ttu-id="23df0-154">如果無法使用 SQL Express，Code First 將會嘗試並使用[LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) （預設會與 Visual Studio 2012 一起安裝）</span><span class="sxs-lookup"><span data-stu-id="23df0-154">If SQL Express isn’t available then Code First will try and use [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) (installed by default with Visual Studio 2012)</span></span>
-   <span data-ttu-id="23df0-155">資料庫會在衍生內容的完整名稱之後命名，在此案例中為**CodeFirstNewDatabaseSample. BloggingCoNtext**</span><span class="sxs-lookup"><span data-stu-id="23df0-155">The database is named after the fully qualified name of the derived context, in our case that is **CodeFirstNewDatabaseSample.BloggingContext**</span></span>

<span data-ttu-id="23df0-156">這些只是預設慣例，而且有各種方式可以變更 Code First 使用的資料庫，而**DbCoNtext 如何探索模型和資料庫連接**主題中提供詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="23df0-156">These are just the default conventions and there are various ways to change the database that Code First uses, more information is available in the **How DbContext Discovers the Model and Database Connection** topic.</span></span>
<span data-ttu-id="23df0-157">您可以使用中的伺服器總管來連接到此資料庫 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23df0-157">You can connect to this database using Server Explorer in Visual Studio</span></span>

-   <span data-ttu-id="23df0-158">**View&gt; 伺服器總管**</span><span class="sxs-lookup"><span data-stu-id="23df0-158">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="23df0-159">以滑鼠右鍵按一下 [**資料連線**]，然後選取 [**新增連接 ...** ]</span><span class="sxs-lookup"><span data-stu-id="23df0-159">Right click on **Data Connections** and select **Add Connection…**</span></span>
-   <span data-ttu-id="23df0-160">如果您還沒有從伺服器總管連接到資料庫，則必須選取 [Microsoft SQL Server] 做為資料來源</span><span class="sxs-lookup"><span data-stu-id="23df0-160">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![選取資料來源](~/ef6/media/selectdatasource.png)

-   <span data-ttu-id="23df0-162">連接到 LocalDB 或 SQL Express （視您安裝的版本而定）</span><span class="sxs-lookup"><span data-stu-id="23df0-162">Connect to either LocalDB or SQL Express, depending on which one you have installed</span></span>

<span data-ttu-id="23df0-163">我們現在可以檢查 Code First 建立的架構。</span><span class="sxs-lookup"><span data-stu-id="23df0-163">We can now inspect the schema that Code First created.</span></span>

![架構初始](~/ef6/media/schemainitial.png)

<span data-ttu-id="23df0-165">DbCoNtext 會藉由查看我們定義的 DbSet 屬性，來處理要包含在模型中的類別。</span><span class="sxs-lookup"><span data-stu-id="23df0-165">DbContext worked out what classes to include in the model by looking at the DbSet properties that we defined.</span></span> <span data-ttu-id="23df0-166">然後，它會使用一組預設的 Code First 慣例來判斷資料表和資料行名稱、判斷資料類型、尋找主鍵等。</span><span class="sxs-lookup"><span data-stu-id="23df0-166">It then uses the default set of Code First conventions to determine table and column names, determine data types, find primary keys, etc.</span></span> <span data-ttu-id="23df0-167">稍後在本逐步解說中，我們將探討如何覆寫這些慣例。</span><span class="sxs-lookup"><span data-stu-id="23df0-167">Later in this walkthrough we’ll look at how you can override these conventions.</span></span>

## <a name="5-dealing-with-model-changes"></a><span data-ttu-id="23df0-168">5. 處理模型變更</span><span class="sxs-lookup"><span data-stu-id="23df0-168">5. Dealing with Model Changes</span></span>

<span data-ttu-id="23df0-169">現在是時候對我們的模型進行一些變更，當我們進行這些變更時，我們也需要更新資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="23df0-169">Now it’s time to make some changes to our model, when we make these changes we also need to update the database schema.</span></span> <span data-ttu-id="23df0-170">為了這麼做，我們將使用稱為 Code First 移轉的功能或簡短的遷移。</span><span class="sxs-lookup"><span data-stu-id="23df0-170">To do this we are going to use a feature called Code First Migrations, or Migrations for short.</span></span>

<span data-ttu-id="23df0-171">遷移可讓我們擁有一組已排序的步驟，說明如何升級（和降級）我們的資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="23df0-171">Migrations allows us to have an ordered set of steps that describe how to upgrade (and downgrade) our database schema.</span></span> <span data-ttu-id="23df0-172">其中每個步驟（稱為「遷移」）都會包含一些程式碼來描述要套用的變更。</span><span class="sxs-lookup"><span data-stu-id="23df0-172">Each of these steps, known as a migration, contains some code that describes the changes to be applied.</span></span> 

<span data-ttu-id="23df0-173">第一個步驟是啟用 BloggingCoNtext 的 Code First 移轉。</span><span class="sxs-lookup"><span data-stu-id="23df0-173">The first step is to enable Code First Migrations for our BloggingContext.</span></span>

-   <span data-ttu-id="23df0-174">**工具-&gt; 程式庫套件管理員-&gt; 套件管理員主控台**</span><span class="sxs-lookup"><span data-stu-id="23df0-174">**Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
-   <span data-ttu-id="23df0-175">在套件管理員主控台中執行 **Enable-migrations** 命令</span><span class="sxs-lookup"><span data-stu-id="23df0-175">Run the **Enable-Migrations** command in Package Manager Console</span></span>
-   <span data-ttu-id="23df0-176">已將新的 [遷移] 資料夾新增至包含兩個專案的專案：</span><span class="sxs-lookup"><span data-stu-id="23df0-176">A new Migrations folder has been added to our project that contains two items:</span></span>
    -   <span data-ttu-id="23df0-177">**Configuration.cs** –此檔案包含遷移將用於遷移 BloggingCoNtext 的設定。</span><span class="sxs-lookup"><span data-stu-id="23df0-177">**Configuration.cs** – This file contains the settings that Migrations will use for migrating BloggingContext.</span></span> <span data-ttu-id="23df0-178">我們不需要變更此逐步解說的任何專案，但您可以在這裡指定種子資料、註冊其他資料庫的提供者、變更在其中產生遷移的命名空間等等。</span><span class="sxs-lookup"><span data-stu-id="23df0-178">We don’t need to change anything for this walkthrough, but here is where you can specify seed data, register providers for other databases, changes the namespace that migrations are generated in etc.</span></span>
    -   <span data-ttu-id="23df0-179">**&lt;時間戳記&gt;\_InitialCreate.cs** –這是您第一次的遷移，它代表已套用至資料庫的變更，使其成為包含 [blog] 和 [貼文] 資料表的空資料庫。</span><span class="sxs-lookup"><span data-stu-id="23df0-179">**&lt;timestamp&gt;\_InitialCreate.cs** – This is your first migration, it represents the changes that have already been applied to the database to take it from being an empty database to one that includes the Blogs and Posts tables.</span></span> <span data-ttu-id="23df0-180">雖然我們會讓 Code First 自動為我們建立這些資料表，但現在我們已選擇要進行遷移，並將其轉換為遷移。</span><span class="sxs-lookup"><span data-stu-id="23df0-180">Although we let Code First automatically create these tables for us, now that we have opted in to Migrations they have been converted into a Migration.</span></span> <span data-ttu-id="23df0-181">Code First 也會記錄在我們的本機資料庫中，這項遷移已套用。</span><span class="sxs-lookup"><span data-stu-id="23df0-181">Code First has also recorded in our local database that this Migration has already been applied.</span></span> <span data-ttu-id="23df0-182">檔案名上的時間戳記會用於排序目的。</span><span class="sxs-lookup"><span data-stu-id="23df0-182">The timestamp on the filename is used for ordering purposes.</span></span>

    <span data-ttu-id="23df0-183">現在，讓我們對模型進行變更，並將 Url 屬性新增至 Blog 類別：</span><span class="sxs-lookup"><span data-stu-id="23df0-183">Now let’s make a change to our model, add a Url property to the Blog class:</span></span>

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }

    public virtual List<Post> Posts { get; set; }
}
```

-   <span data-ttu-id="23df0-184">在 [套件管理員主控台] 中執行 [**新增-遷移 AddUrl** ] 命令。</span><span class="sxs-lookup"><span data-stu-id="23df0-184">Run the **Add-Migration AddUrl** command in Package Manager Console.</span></span>
    <span data-ttu-id="23df0-185">「新增-遷移」命令會在您上次遷移後檢查變更，並 scaffold 所發現的任何變更來進行新的遷移。</span><span class="sxs-lookup"><span data-stu-id="23df0-185">The Add-Migration command checks for changes since your last migration and scaffolds a new migration with any changes that are found.</span></span> <span data-ttu-id="23df0-186">我們可以提供一個名稱給遷移;在此情況下，我們會呼叫「AddUrl」遷移。</span><span class="sxs-lookup"><span data-stu-id="23df0-186">We can give migrations a name; in this case we are calling the migration ‘AddUrl’.</span></span>
    <span data-ttu-id="23df0-187">Scaffold 程式碼的意思是，我們需要將可保存字串資料的 Url 資料行加入至 dbo。[Blog] 資料表。</span><span class="sxs-lookup"><span data-stu-id="23df0-187">The scaffolded code is saying that we need to add a Url column, that can hold string data, to the dbo.Blogs table.</span></span> <span data-ttu-id="23df0-188">如有需要，我們可以編輯 scaffold 程式碼，但在此情況下不需要這麼做。</span><span class="sxs-lookup"><span data-stu-id="23df0-188">If needed, we could edit the scaffolded code but that’s not required in this case.</span></span>

``` csharp
namespace CodeFirstNewDatabaseSample.Migrations
{
    using System;
    using System.Data.Entity.Migrations;

    public partial class AddUrl : DbMigration
    {
        public override void Up()
        {
            AddColumn("dbo.Blogs", "Url", c => c.String());
        }

        public override void Down()
        {
            DropColumn("dbo.Blogs", "Url");
        }
    }
}
```

-   <span data-ttu-id="23df0-189">在 [套件管理員主控台] 中執行 [**更新-資料庫**] 命令。</span><span class="sxs-lookup"><span data-stu-id="23df0-189">Run the **Update-Database** command in Package Manager Console.</span></span> <span data-ttu-id="23df0-190">此命令會將任何暫止的遷移套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="23df0-190">This command will apply any pending migrations to the database.</span></span> <span data-ttu-id="23df0-191">我們的 InitialCreate 遷移已套用，因此，遷移只會套用我們的新 AddUrl 遷移。</span><span class="sxs-lookup"><span data-stu-id="23df0-191">Our InitialCreate migration has already been applied so migrations will just apply our new AddUrl migration.</span></span>
    <span data-ttu-id="23df0-192">提示：呼叫 Update-Database 時，您可以使用 **– Verbose**參數來查看正在對資料庫執行的 SQL。</span><span class="sxs-lookup"><span data-stu-id="23df0-192">Tip: You can use the **–Verbose** switch when calling Update-Database to see the SQL that is being executed against the database.</span></span>

<span data-ttu-id="23df0-193">新的 Url 資料行現在已新增至資料庫中的 Blog 資料表：</span><span class="sxs-lookup"><span data-stu-id="23df0-193">The new Url column is now added to the Blogs table in the database:</span></span>

![具有 Url 的架構](~/ef6/media/schemawithurl.png)

## <a name="6-data-annotations"></a><span data-ttu-id="23df0-195">6. 資料批註</span><span class="sxs-lookup"><span data-stu-id="23df0-195">6. Data Annotations</span></span>

<span data-ttu-id="23df0-196">到目前為止，我們只是讓 EF 使用其預設慣例來探索模型，但有時候我們的類別不會遵循慣例，而且我們需要能夠執行進一步的設定。</span><span class="sxs-lookup"><span data-stu-id="23df0-196">So far we’ve just let EF discover the model using its default conventions, but there are going to be times when our classes don’t follow the conventions and we need to be able to perform further configuration.</span></span> <span data-ttu-id="23df0-197">有兩個選項可供您選擇：我們將在此區段中查看資料批註，然後在下一節中探討 Fluent API。</span><span class="sxs-lookup"><span data-stu-id="23df0-197">There are two options for this; we’ll look at Data Annotations in this section and then the fluent API in the next section.</span></span>

-   <span data-ttu-id="23df0-198">讓我們在模型中新增使用者類別</span><span class="sxs-lookup"><span data-stu-id="23df0-198">Let’s add a User class to our model</span></span>

``` csharp
public class User
{
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   <span data-ttu-id="23df0-199">我們也需要將集合新增至我們的衍生內容</span><span class="sxs-lookup"><span data-stu-id="23df0-199">We also need to add a set to our derived context</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }
}
```

-   <span data-ttu-id="23df0-200">如果我們嘗試新增遷移，就會收到錯誤，指出「*EntityType ' User ' 未定義索引鍵。定義此 EntityType 的索引鍵。* 」</span><span class="sxs-lookup"><span data-stu-id="23df0-200">If we tried to add a migration we’d get an error saying “*EntityType ‘User’ has no key defined. Define the key for this EntityType.”*</span></span> <span data-ttu-id="23df0-201">因為 EF 無法得知使用者名稱應為使用者的主要金鑰。</span><span class="sxs-lookup"><span data-stu-id="23df0-201">because EF has no way of knowing that Username should be the primary key for User.</span></span>
-   <span data-ttu-id="23df0-202">我們現在要使用資料批註，所以我們需要在 Program.cs 頂端新增 using 語句</span><span class="sxs-lookup"><span data-stu-id="23df0-202">We’re going to use Data Annotations now so we need to add a using statement at the top of Program.cs</span></span>

```csharp
using System.ComponentModel.DataAnnotations;
```

-   <span data-ttu-id="23df0-203">現在，為 Username 屬性加上批註，以識別它是主要金鑰</span><span class="sxs-lookup"><span data-stu-id="23df0-203">Now annotate the Username property to identify that it is the primary key</span></span>

``` csharp
public class User
{
    [Key]
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   <span data-ttu-id="23df0-204">使用**新增-遷移 AddUser**命令來 scaffold 遷移，以將這些變更套用至資料庫</span><span class="sxs-lookup"><span data-stu-id="23df0-204">Use the **Add-Migration AddUser** command to scaffold a migration to apply these changes to the database</span></span>
-   <span data-ttu-id="23df0-205">執行 [**更新-資料庫**] 命令，將新的遷移套用至資料庫</span><span class="sxs-lookup"><span data-stu-id="23df0-205">Run the **Update-Database** command to apply the new migration to the database</span></span>

<span data-ttu-id="23df0-206">新的資料表現在已新增至資料庫：</span><span class="sxs-lookup"><span data-stu-id="23df0-206">The new table is now added to the database:</span></span>

![使用者的架構](~/ef6/media/schemawithusers.png)

<span data-ttu-id="23df0-208">EF 支援的完整注釋清單如下：</span><span class="sxs-lookup"><span data-stu-id="23df0-208">The full list of annotations supported by EF is:</span></span>

-   [<span data-ttu-id="23df0-209">KeyAttribute</span><span class="sxs-lookup"><span data-stu-id="23df0-209">KeyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.keyattribute)
-   [<span data-ttu-id="23df0-210">StringLengthAttribute</span><span class="sxs-lookup"><span data-stu-id="23df0-210">StringLengthAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute)
-   [<span data-ttu-id="23df0-211">MaxLengthAttribute</span><span class="sxs-lookup"><span data-stu-id="23df0-211">MaxLengthAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.maxlengthattribute)
-   [<span data-ttu-id="23df0-212">ConcurrencyCheckAttribute</span><span class="sxs-lookup"><span data-stu-id="23df0-212">ConcurrencyCheckAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.concurrencycheckattribute)
-   [<span data-ttu-id="23df0-213">RequiredAttribute</span><span class="sxs-lookup"><span data-stu-id="23df0-213">RequiredAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute)
-   [<span data-ttu-id="23df0-214">TimestampAttribute</span><span class="sxs-lookup"><span data-stu-id="23df0-214">TimestampAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute)
-   [<span data-ttu-id="23df0-215">ComplexTypeAttribute</span><span class="sxs-lookup"><span data-stu-id="23df0-215">ComplexTypeAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.complextypeattribute)
-   [<span data-ttu-id="23df0-216">ColumnAttribute</span><span class="sxs-lookup"><span data-stu-id="23df0-216">ColumnAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.columnattribute)
-   [<span data-ttu-id="23df0-217">TableAttribute</span><span class="sxs-lookup"><span data-stu-id="23df0-217">TableAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.tableattribute)
-   [<span data-ttu-id="23df0-218">InversePropertyAttribute</span><span class="sxs-lookup"><span data-stu-id="23df0-218">InversePropertyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.inversepropertyattribute)
-   [<span data-ttu-id="23df0-219">ForeignKeyAttribute</span><span class="sxs-lookup"><span data-stu-id="23df0-219">ForeignKeyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.foreignkeyattribute)
-   [<span data-ttu-id="23df0-220">DatabaseGeneratedAttribute</span><span class="sxs-lookup"><span data-stu-id="23df0-220">DatabaseGeneratedAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.databasegeneratedattribute)
-   [<span data-ttu-id="23df0-221">NotMappedAttribute</span><span class="sxs-lookup"><span data-stu-id="23df0-221">NotMappedAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.notmappedattribute)

## <a name="7-fluent-api"></a><span data-ttu-id="23df0-222">7. 流暢的 API</span><span class="sxs-lookup"><span data-stu-id="23df0-222">7. Fluent API</span></span>

<span data-ttu-id="23df0-223">在上一節中，我們探討了如何使用資料批註來補充或覆寫慣例所偵測到的內容。</span><span class="sxs-lookup"><span data-stu-id="23df0-223">In the previous section we looked at using Data Annotations to supplement or override what was detected by convention.</span></span> <span data-ttu-id="23df0-224">設定模型的另一種方式是透過 Code First Fluent API。</span><span class="sxs-lookup"><span data-stu-id="23df0-224">The other way to configure the model is via the Code First fluent API.</span></span>

<span data-ttu-id="23df0-225">大部分的模型設定都可以使用簡單的資料批註來完成。</span><span class="sxs-lookup"><span data-stu-id="23df0-225">Most model configuration can be done using simple data annotations.</span></span> <span data-ttu-id="23df0-226">Fluent API 是指定模型設定的更先進方式，其中涵蓋資料批註除了其他不可能使用資料批註而進行的更先進設定之外，還可執行檔所有動作。</span><span class="sxs-lookup"><span data-stu-id="23df0-226">The fluent API is a more advanced way of specifying model configuration that covers everything that data annotations can do in addition to some more advanced configuration not possible with data annotations.</span></span> <span data-ttu-id="23df0-227">可以同時使用資料批註和 Fluent API。</span><span class="sxs-lookup"><span data-stu-id="23df0-227">Data annotations and the fluent API can be used together.</span></span>

<span data-ttu-id="23df0-228">若要存取 Fluent API 您必須覆寫 DbCoNtext 中的 OnModelCreating 方法。</span><span class="sxs-lookup"><span data-stu-id="23df0-228">To access the fluent API you override the OnModelCreating method in DbContext.</span></span> <span data-ttu-id="23df0-229">假設我們想要重新命名資料行，其中儲存了使用者的 DisplayName，以顯示\_的名稱。</span><span class="sxs-lookup"><span data-stu-id="23df0-229">Let’s say we wanted to rename the column that User.DisplayName is stored in to display\_name.</span></span>

-   <span data-ttu-id="23df0-230">使用下列程式碼覆寫 BloggingCoNtext 上的 OnModelCreating 方法</span><span class="sxs-lookup"><span data-stu-id="23df0-230">Override the OnModelCreating method on BloggingContext with the following code</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Entity<User>()
            .Property(u => u.DisplayName)
            .HasColumnName("display_name");
    }
}
```

-   <span data-ttu-id="23df0-231">使用**Add-遷移 ChangeDisplayName**命令來 scaffold 遷移，以將這些變更套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="23df0-231">Use the **Add-Migration ChangeDisplayName** command to scaffold a migration to apply these changes to the database.</span></span>
-   <span data-ttu-id="23df0-232">執行 [**更新-資料庫**] 命令，將新的遷移套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="23df0-232">Run the **Update-Database** command to apply the new migration to the database.</span></span>

<span data-ttu-id="23df0-233">[DisplayName] 資料行現在已重新命名為顯示\_名稱：</span><span class="sxs-lookup"><span data-stu-id="23df0-233">The DisplayName column is now renamed to display\_name:</span></span>

![已重新命名顯示名稱的架構](~/ef6/media/schemawithdisplaynamerenamed.png)

## <a name="summary"></a><span data-ttu-id="23df0-235">摘要</span><span class="sxs-lookup"><span data-stu-id="23df0-235">Summary</span></span>

<span data-ttu-id="23df0-236">在本逐步解說中，我們探討了使用新資料庫的 Code First 開發。</span><span class="sxs-lookup"><span data-stu-id="23df0-236">In this walkthrough we looked at Code First development using a new database.</span></span> <span data-ttu-id="23df0-237">我們使用類別定義了模型，然後使用該模型來建立資料庫並儲存和抓取資料。</span><span class="sxs-lookup"><span data-stu-id="23df0-237">We defined a model using classes then used that model to create a database and store and retrieve data.</span></span> <span data-ttu-id="23df0-238">建立資料庫之後，我們就會在模型演進時，使用 Code First 移轉來變更架構。</span><span class="sxs-lookup"><span data-stu-id="23df0-238">Once the database was created we used Code First Migrations to change the schema as our model evolved.</span></span> <span data-ttu-id="23df0-239">我們也看到了如何使用資料批註和流暢的 API 來設定模型。</span><span class="sxs-lookup"><span data-stu-id="23df0-239">We also saw how to configure a model using Data Annotations and the Fluent API.</span></span>
