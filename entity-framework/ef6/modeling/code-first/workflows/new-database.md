---
title: Code First 至新的資料庫-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 2df6cb0a-7d8b-4e28-9d05-e2b9a90125af
ms.openlocfilehash: a19db575b685cde98509fff4a0efaf26106b26bc
ms.sourcegitcommit: 269c8a1a457a9ad27b4026c22c4b1a76991fb360
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2018
ms.locfileid: "46284118"
---
# <a name="code-first-to-a-new-database"></a><span data-ttu-id="ae446-102">Code First 至新的資料庫</span><span class="sxs-lookup"><span data-stu-id="ae446-102">Code First to a New Database</span></span>
<span data-ttu-id="ae446-103">本影片以及逐步說明的逐步解說提供目標的新資料庫的 Code First 開發的簡介。</span><span class="sxs-lookup"><span data-stu-id="ae446-103">This video and step-by-step walkthrough provide an introduction to Code First development targeting a new database.</span></span> <span data-ttu-id="ae446-104">此案例包含目標資料庫不存在和 Code First 會建立，或空的資料庫，Code First 會新增的新資料表。</span><span class="sxs-lookup"><span data-stu-id="ae446-104">This scenario includes targeting a database that doesn’t exist and Code First will create, or an empty database that Code First will add new tables to.</span></span> <span data-ttu-id="ae446-105">程式碼第一次可讓您定義您的模型使用 C\#或 VB.Net 類別。</span><span class="sxs-lookup"><span data-stu-id="ae446-105">Code First allows you to define your model using C\# or VB.Net classes.</span></span> <span data-ttu-id="ae446-106">其他設定 （選擇性） 可以使用屬性，在您的類別和屬性，或使用 fluent API 來執行。</span><span class="sxs-lookup"><span data-stu-id="ae446-106">Additional configuration can optionally be performed using attributes on your classes and properties or by using a fluent API.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="ae446-107">觀看影片</span><span class="sxs-lookup"><span data-stu-id="ae446-107">Watch the video</span></span>
<span data-ttu-id="ae446-108">這段影片提供目標的新資料庫的 Code First 開發的簡介。</span><span class="sxs-lookup"><span data-stu-id="ae446-108">This video provides an introduction to Code First development targeting a new database.</span></span> <span data-ttu-id="ae446-109">此案例包含目標資料庫不存在和 Code First 會建立，或空的資料庫，Code First 會新增的新資料表。</span><span class="sxs-lookup"><span data-stu-id="ae446-109">This scenario includes targeting a database that doesn’t exist and Code First will create, or an empty database that Code First will add new tables to.</span></span> <span data-ttu-id="ae446-110">程式碼第一次可讓您定義您使用 C# 或 VB.Net 類別的模型。</span><span class="sxs-lookup"><span data-stu-id="ae446-110">Code First allows you to define your model using C# or VB.Net classes.</span></span> <span data-ttu-id="ae446-111">其他設定 （選擇性） 可以使用屬性，在您的類別和屬性，或使用 fluent API 來執行。</span><span class="sxs-lookup"><span data-stu-id="ae446-111">Additional configuration can optionally be performed using attributes on your classes and properties or by using a fluent API.</span></span>

<span data-ttu-id="ae446-112">**主講人**[Rowan Miller](http://romiller.com/)</span><span class="sxs-lookup"><span data-stu-id="ae446-112">**Presented By**: [Rowan Miller](http://romiller.com/)</span></span>

<span data-ttu-id="ae446-113">**視訊**: [WMV](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.wmv) | [MP4](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-mp4Video-CodeFirstNewDatabase.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.zip)</span><span class="sxs-lookup"><span data-stu-id="ae446-113">**Video**: [WMV](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.wmv) | [MP4](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-mp4Video-CodeFirstNewDatabase.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="ae446-114">必要條件</span><span class="sxs-lookup"><span data-stu-id="ae446-114">Pre-Requisites</span></span>

<span data-ttu-id="ae446-115">您必須至少是 Visual studio 2010 或 Visual Studio 2012 安裝完成本逐步解說。</span><span class="sxs-lookup"><span data-stu-id="ae446-115">You will need to have at least Visual Studio 2010 or Visual Studio 2012 installed to complete this walkthrough.</span></span>

<span data-ttu-id="ae446-116">如果您使用 Visual Studio 2010，您也必須有[NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)安裝。</span><span class="sxs-lookup"><span data-stu-id="ae446-116">If you are using Visual Studio 2010, you will also need to have [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installed.</span></span>

## <a name="1-create-the-application"></a><span data-ttu-id="ae446-117">1.建立應用程式</span><span class="sxs-lookup"><span data-stu-id="ae446-117">1. Create the Application</span></span>

<span data-ttu-id="ae446-118">為了簡單起見，我們要建置基本的主控台應用程式，會使用 Code First 來執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="ae446-118">To keep things simple we’re going to build a basic console application that uses Code First to perform data access.</span></span>

-   <span data-ttu-id="ae446-119">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ae446-119">Open Visual Studio</span></span>
-   <span data-ttu-id="ae446-120">**檔案-&gt;新增-&gt;專案...**</span><span class="sxs-lookup"><span data-stu-id="ae446-120">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="ae446-121">選取  **Windows**從左側的功能表和**主控台應用程式**</span><span class="sxs-lookup"><span data-stu-id="ae446-121">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="ae446-122">請輸入**CodeFirstNewDatabaseSample**做為名稱</span><span class="sxs-lookup"><span data-stu-id="ae446-122">Enter **CodeFirstNewDatabaseSample** as the name</span></span>
-   <span data-ttu-id="ae446-123">選取 [確定]</span><span class="sxs-lookup"><span data-stu-id="ae446-123">Select **OK**</span></span>

## <a name="2-create-the-model"></a><span data-ttu-id="ae446-124">2.建立模型</span><span class="sxs-lookup"><span data-stu-id="ae446-124">2. Create the Model</span></span>

<span data-ttu-id="ae446-125">讓我們定義非常簡單的模型，使用類別。</span><span class="sxs-lookup"><span data-stu-id="ae446-125">Let’s define a very simple model using classes.</span></span> <span data-ttu-id="ae446-126">我們會只定義它們的 Program.cs 檔案中，但在真實世界應用程式，您會將出您的類別分割成個別的檔案和潛在不同的專案。</span><span class="sxs-lookup"><span data-stu-id="ae446-126">We’re just defining them in the Program.cs file but in a real world application you would split your classes out into separate files and potentially a separate project.</span></span>

<span data-ttu-id="ae446-127">下方程式類別定義，在 Program.cs 中加入下列兩個類別。</span><span class="sxs-lookup"><span data-stu-id="ae446-127">Below the Program class definition in Program.cs add the following two classes.</span></span>

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

<span data-ttu-id="ae446-128">您會注意到，我們將會讓兩個導覽屬性 （Blog.Posts 和 Post.Blog） 虛擬。</span><span class="sxs-lookup"><span data-stu-id="ae446-128">You’ll notice that we’re making the two navigation properties (Blog.Posts and Post.Blog) virtual.</span></span> <span data-ttu-id="ae446-129">這可讓 Entity Framework 的消極式載入功能。</span><span class="sxs-lookup"><span data-stu-id="ae446-129">This enables the Lazy Loading feature of Entity Framework.</span></span> <span data-ttu-id="ae446-130">消極式載入會表示，這些屬性的內容會自動從資料庫載入當您嘗試存取它們。</span><span class="sxs-lookup"><span data-stu-id="ae446-130">Lazy Loading means that the contents of these properties will be automatically loaded from the database when you try to access them.</span></span>

## <a name="3-create-a-context"></a><span data-ttu-id="ae446-131">3.建立內容</span><span class="sxs-lookup"><span data-stu-id="ae446-131">3. Create a Context</span></span>

<span data-ttu-id="ae446-132">現在就可以開始定義 衍生的內容，表示與資料庫，讓我們可以查詢和儲存資料的工作階段。</span><span class="sxs-lookup"><span data-stu-id="ae446-132">Now it’s time to define a derived context, which represents a session with the database, allowing us to query and save data.</span></span> <span data-ttu-id="ae446-133">我們會定義的內容衍生自 System.Data.Entity.DbContext 並公開 （expose） 的具類型的 DbSet&lt;TEntity&gt;我們的模型中每個類別。</span><span class="sxs-lookup"><span data-stu-id="ae446-133">We define a context that derives from System.Data.Entity.DbContext and exposes a typed DbSet&lt;TEntity&gt; for each class in our model.</span></span>

<span data-ttu-id="ae446-134">我們現在正在使用 Entity Framework 中的類型，因此我們需要加入 EntityFramework NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="ae446-134">We’re now starting to use types from the Entity Framework so we need to add the EntityFramework NuGet package.</span></span>

-   <span data-ttu-id="ae446-135">**專案 –&gt;管理 NuGet 封裝...**</span><span class="sxs-lookup"><span data-stu-id="ae446-135">**Project –&gt; Manage NuGet Packages…**</span></span>
    <span data-ttu-id="ae446-136">注意： 如果您沒有**管理 NuGet 套件...**</span><span class="sxs-lookup"><span data-stu-id="ae446-136">Note: If you don’t have the **Manage NuGet Packages…**</span></span> <span data-ttu-id="ae446-137">您應該安裝的選項[最新版的 NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)</span><span class="sxs-lookup"><span data-stu-id="ae446-137">option you should install the [latest version of NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)</span></span>
-   <span data-ttu-id="ae446-138">選取 [ **Online** ] 索引標籤</span><span class="sxs-lookup"><span data-stu-id="ae446-138">Select the **Online** tab</span></span>
-   <span data-ttu-id="ae446-139">選取  **EntityFramework**封裝</span><span class="sxs-lookup"><span data-stu-id="ae446-139">Select the **EntityFramework** package</span></span>
-   <span data-ttu-id="ae446-140">按一下 **安裝**</span><span class="sxs-lookup"><span data-stu-id="ae446-140">Click **Install**</span></span>

<span data-ttu-id="ae446-141">加入 using 陳述式 System.Data.Entity Program.cs 的頂端。</span><span class="sxs-lookup"><span data-stu-id="ae446-141">Add a using statement for System.Data.Entity at the top of Program.cs.</span></span>

``` csharp
using System.Data.Entity;
```

<span data-ttu-id="ae446-142">在 Program.cs 中的文章類別下方新增下列衍生的內容。</span><span class="sxs-lookup"><span data-stu-id="ae446-142">Below the Post class in Program.cs add the following derived context.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```

<span data-ttu-id="ae446-143">以下是什麼 Program.cs 現在應該包含的完整清單。</span><span class="sxs-lookup"><span data-stu-id="ae446-143">Here is a complete listing of what Program.cs should now contain.</span></span>

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

<span data-ttu-id="ae446-144">這是我們要開始儲存和擷取資料的所有程式碼。</span><span class="sxs-lookup"><span data-stu-id="ae446-144">That is all the code we need to start storing and retrieving data.</span></span> <span data-ttu-id="ae446-145">很顯然有相當大背後運作原理，並讓我們看一下，待會兒但請先讓我們來觀看實作示範。</span><span class="sxs-lookup"><span data-stu-id="ae446-145">Obviously there is quite a bit going on behind the scenes and we’ll take a look at that in a moment but first let’s see it in action.</span></span>

## <a name="4-reading--writing-data"></a><span data-ttu-id="ae446-146">4.讀取和寫入資料</span><span class="sxs-lookup"><span data-stu-id="ae446-146">4. Reading & Writing Data</span></span>

<span data-ttu-id="ae446-147">在 Program.cs 中實作的 Main 方法，如下所示。</span><span class="sxs-lookup"><span data-stu-id="ae446-147">Implement the Main method in Program.cs as shown below.</span></span> <span data-ttu-id="ae446-148">此程式碼會建立我們的內容的新執行個體，然後用它來插入新的部落格</span><span class="sxs-lookup"><span data-stu-id="ae446-148">This code creates a new instance of our context and then uses it to insert a new Blog.</span></span> <span data-ttu-id="ae446-149">然後它會使用 LINQ 查詢來擷取依字母順序排序項目所使用的資料庫中的所有部落格。</span><span class="sxs-lookup"><span data-stu-id="ae446-149">Then it uses a LINQ query to retrieve all Blogs from the database ordered alphabetically by Title.</span></span>

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

<span data-ttu-id="ae446-150">您現在可以執行應用程式，並加以測試。</span><span class="sxs-lookup"><span data-stu-id="ae446-150">You can now run the application and test it out.</span></span>

```
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
### <a name="wheres-my-data"></a><span data-ttu-id="ae446-151">我的資料位於何處？</span><span class="sxs-lookup"><span data-stu-id="ae446-151">Where’s My Data?</span></span>

<span data-ttu-id="ae446-152">依照慣例 DbContext 已為您建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="ae446-152">By convention DbContext has created a database for you.</span></span>

-   <span data-ttu-id="ae446-153">如果本機 SQL Express 執行個體可用的 （使用 Visual Studio 2010 的預設安裝） 然後 Code First 已資料庫上建立該執行個體</span><span class="sxs-lookup"><span data-stu-id="ae446-153">If a local SQL Express instance is available (installed by default with Visual Studio 2010) then Code First has created the database on that instance</span></span>
-   <span data-ttu-id="ae446-154">如果沒有 SQL Express 則 Code First 會試著使用[LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) （使用 Visual Studio 2012 的預設安裝）</span><span class="sxs-lookup"><span data-stu-id="ae446-154">If SQL Express isn’t available then Code First will try and use [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) (installed by default with Visual Studio 2012)</span></span>
-   <span data-ttu-id="ae446-155">衍生的內容，在本例中是完整格式名稱來命名資料庫**CodeFirstNewDatabaseSample.BloggingContext**</span><span class="sxs-lookup"><span data-stu-id="ae446-155">The database is named after the fully qualified name of the derived context, in our case that is **CodeFirstNewDatabaseSample.BloggingContext**</span></span>

<span data-ttu-id="ae446-156">這些是預設慣例有 Code First 會使用將資料庫變更的各種方式，詳細資訊位於**DbContext 會探索模型和資料庫連接**主題。</span><span class="sxs-lookup"><span data-stu-id="ae446-156">These are just the default conventions and there are various ways to change the database that Code First uses, more information is available in the **How DbContext Discovers the Model and Database Connection** topic.</span></span>
<span data-ttu-id="ae446-157">您可以連接到此 Visual Studio 中使用伺服器總管 中的資料庫</span><span class="sxs-lookup"><span data-stu-id="ae446-157">You can connect to this database using Server Explorer in Visual Studio</span></span>

-   <span data-ttu-id="ae446-158">**檢視-&gt;伺服器總管**</span><span class="sxs-lookup"><span data-stu-id="ae446-158">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="ae446-159">以滑鼠右鍵按一下**資料連接**，然後選取**新增連接...**</span><span class="sxs-lookup"><span data-stu-id="ae446-159">Right click on **Data Connections** and select **Add Connection…**</span></span>
-   <span data-ttu-id="ae446-160">如果您尚未連線至資料庫從伺服器總管之前您必須選取 Microsoft SQL Server 做為資料來源</span><span class="sxs-lookup"><span data-stu-id="ae446-160">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![選取資料來源](~/ef6/media/selectdatasource.png)

-   <span data-ttu-id="ae446-162">連接到 LocalDB 或 SQL Express，何者而定，您已安裝</span><span class="sxs-lookup"><span data-stu-id="ae446-162">Connect to either LocalDB or SQL Express, depending on which one you have installed</span></span>

<span data-ttu-id="ae446-163">我們現在可以檢查 Code First 建立的結構描述。</span><span class="sxs-lookup"><span data-stu-id="ae446-163">We can now inspect the schema that Code First created.</span></span>

![初始的結構描述](~/ef6/media/schemainitial.png)

<span data-ttu-id="ae446-165">DbContext 出哪些類別，以查看我們所定義的 DbSet 屬性包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="ae446-165">DbContext worked out what classes to include in the model by looking at the DbSet properties that we defined.</span></span> <span data-ttu-id="ae446-166">然後，它會使用一組預設的 Code First 慣例決定資料表和資料行的名稱，判斷資料型別中，尋找主索引鍵等。</span><span class="sxs-lookup"><span data-stu-id="ae446-166">It then uses the default set of Code First conventions to determine table and column names, determine data types, find primary keys, etc.</span></span> <span data-ttu-id="ae446-167">稍後在本逐步解說中，我們將探討如何覆寫這些慣例。</span><span class="sxs-lookup"><span data-stu-id="ae446-167">Later in this walkthrough we’ll look at how you can override these conventions.</span></span>

## <a name="5-dealing-with-model-changes"></a><span data-ttu-id="ae446-168">5.處理模型變更</span><span class="sxs-lookup"><span data-stu-id="ae446-168">5. Dealing with Model Changes</span></span>

<span data-ttu-id="ae446-169">現在就可以開始對我們的模型進行一些變更，當我們做出這些變更，我們也需要更新資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="ae446-169">Now it’s time to make some changes to our model, when we make these changes we also need to update the database schema.</span></span> <span data-ttu-id="ae446-170">若要這樣做我們要使用的簡稱稱為 Code First 移轉或移轉的功能。</span><span class="sxs-lookup"><span data-stu-id="ae446-170">To do this we are going to use a feature called Code First Migrations, or Migrations for short.</span></span>

<span data-ttu-id="ae446-171">移轉可讓我們將說明如何升級 （及降級） 資料庫結構描述的步驟的已排序的集合。</span><span class="sxs-lookup"><span data-stu-id="ae446-171">Migrations allows us to have an ordered set of steps that describe how to upgrade (and downgrade) our database schema.</span></span> <span data-ttu-id="ae446-172">每個步驟，稱為移轉時，包含一些程式碼，描述要套用變更。</span><span class="sxs-lookup"><span data-stu-id="ae446-172">Each of these steps, known as a migration, contains some code that describes the changes to be applied.</span></span> 

<span data-ttu-id="ae446-173">第一個步驟是針對我們為 [bloggingcontext] 啟用 Code First 移轉。</span><span class="sxs-lookup"><span data-stu-id="ae446-173">The first step is to enable Code First Migrations for our BloggingContext.</span></span>

-   <span data-ttu-id="ae446-174">**工具-&gt;程式庫套件管理員-&gt;套件管理員主控台**</span><span class="sxs-lookup"><span data-stu-id="ae446-174">**Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
-   <span data-ttu-id="ae446-175">在套件管理員主控台中執行 **Enable-migrations** 命令</span><span class="sxs-lookup"><span data-stu-id="ae446-175">Run the **Enable-Migrations** command in Package Manager Console</span></span>
-   <span data-ttu-id="ae446-176">新的 [移轉] 資料夾已新增至我們的專案包含兩個項目：</span><span class="sxs-lookup"><span data-stu-id="ae446-176">A new Migrations folder has been added to our project that contains two items:</span></span>
    -   <span data-ttu-id="ae446-177">**Configuration.cs** – 這個檔案包含將用於移轉的移轉設定為 [bloggingcontext]。</span><span class="sxs-lookup"><span data-stu-id="ae446-177">**Configuration.cs** – This file contains the settings that Migrations will use for migrating BloggingContext.</span></span> <span data-ttu-id="ae446-178">我們不需要變更此逐步解說中的任何項目，但以下是您可以指定種子資料，註冊提供者，其他資料庫，變更命名空間的移轉會產生等。</span><span class="sxs-lookup"><span data-stu-id="ae446-178">We don’t need to change anything for this walkthrough, but here is where you can specify seed data, register providers for other databases, changes the namespace that migrations are generated in etc.</span></span>
    -   <span data-ttu-id="ae446-179">**&lt;時間戳記&gt;\_InitialCreate.cs** – 這是您第一次移轉，它代表已套用到資料庫，才從空的資料庫，其中包括部落格和文章資料表的變更.</span><span class="sxs-lookup"><span data-stu-id="ae446-179">**&lt;timestamp&gt;\_InitialCreate.cs** – This is your first migration, it represents the changes that have already been applied to the database to take it from being an empty database to one that includes the Blogs and Posts tables.</span></span> <span data-ttu-id="ae446-180">雖然我們讓 Code First 自動建立這些資料表，既然我們已選擇加入這些已轉換成移轉的移轉。</span><span class="sxs-lookup"><span data-stu-id="ae446-180">Although we let Code First automatically create these tables for us, now that we have opted in to Migrations they have been converted into a Migration.</span></span> <span data-ttu-id="ae446-181">程式碼第一次有也記錄在此移轉已套用我們本機資料庫。</span><span class="sxs-lookup"><span data-stu-id="ae446-181">Code First has also recorded in our local database that this Migration has already been applied.</span></span> <span data-ttu-id="ae446-182">檔案名稱上的時間戳記用來排序之用。</span><span class="sxs-lookup"><span data-stu-id="ae446-182">The timestamp on the filename is used for ordering purposes.</span></span>

    <span data-ttu-id="ae446-183">現在讓我們對我們的模型進行變更、 加入部落格類別中的 Url 屬性：</span><span class="sxs-lookup"><span data-stu-id="ae446-183">Now let’s make a change to our model, add a Url property to the Blog class:</span></span>

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }

    public virtual List<Post> Posts { get; set; }
}
```

-   <span data-ttu-id="ae446-184">執行**Add-migration AddUrl**命令在套件管理員主控台。</span><span class="sxs-lookup"><span data-stu-id="ae446-184">Run the **Add-Migration AddUrl** command in Package Manager Console.</span></span>
    <span data-ttu-id="ae446-185">新增移轉命令會檢查自最後一個移轉後變更，並則的任何變更，找到新的移轉。</span><span class="sxs-lookup"><span data-stu-id="ae446-185">The Add-Migration command checks for changes since your last migration and scaffolds a new migration with any changes that are found.</span></span> <span data-ttu-id="ae446-186">我們可以為移轉指定的名稱;在此情況下，我們會呼叫移轉 'AddUrl'。</span><span class="sxs-lookup"><span data-stu-id="ae446-186">We can give migrations a name; in this case we are calling the migration ‘AddUrl’.</span></span>
    <span data-ttu-id="ae446-187">Scaffold 程式碼說我們要加入的 Url 資料行，可以保存字串資料，為 dbo。部落格的資料表。</span><span class="sxs-lookup"><span data-stu-id="ae446-187">The scaffolded code is saying that we need to add a Url column, that can hold string data, to the dbo.Blogs table.</span></span> <span data-ttu-id="ae446-188">如有需要我們可以編輯的 scaffold 程式碼，但在此情況下不需要。</span><span class="sxs-lookup"><span data-stu-id="ae446-188">If needed, we could edit the scaffolded code but that’s not required in this case.</span></span>

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

-   <span data-ttu-id="ae446-189">執行**Update-database**命令在套件管理員主控台。</span><span class="sxs-lookup"><span data-stu-id="ae446-189">Run the **Update-Database** command in Package Manager Console.</span></span> <span data-ttu-id="ae446-190">此命令會將任何擱置中的移轉套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="ae446-190">This command will apply any pending migrations to the database.</span></span> <span data-ttu-id="ae446-191">讓移轉只會套用我們新的 AddUrl 移轉已套用我們為 InitialCreate 移轉。</span><span class="sxs-lookup"><span data-stu-id="ae446-191">Our InitialCreate migration has already been applied so migrations will just apply our new AddUrl migration.</span></span>
    <span data-ttu-id="ae446-192">提示： 您可以使用 **– Verbose**切換呼叫以查看針對資料庫執行的 SQL 更新資料庫時。</span><span class="sxs-lookup"><span data-stu-id="ae446-192">Tip: You can use the **–Verbose** switch when calling Update-Database to see the SQL that is being executed against the database.</span></span>

<span data-ttu-id="ae446-193">新的 [Url] 資料行現在已新增至部落格資料庫的資料表中:</span><span class="sxs-lookup"><span data-stu-id="ae446-193">The new Url column is now added to the Blogs table in the database:</span></span>

![使用 Url 的結構描述](~/ef6/media/schemawithurl.png)

## <a name="6-data-annotations"></a><span data-ttu-id="ae446-195">6.資料註釋</span><span class="sxs-lookup"><span data-stu-id="ae446-195">6. Data Annotations</span></span>

<span data-ttu-id="ae446-196">到目前為止我們只是已讓探索模型使用其預設慣例，EF，但那里要作為的時間類別不遵循慣例，以及我們必須要能夠執行進一步的組態。</span><span class="sxs-lookup"><span data-stu-id="ae446-196">So far we’ve just let EF discover the model using its default conventions, but there are going to be times when our classes don’t follow the conventions and we need to be able to perform further configuration.</span></span> <span data-ttu-id="ae446-197">有兩個選項，這個項目;我們將探討在本節中的資料註解，然後在下一節中的 fluent API。</span><span class="sxs-lookup"><span data-stu-id="ae446-197">There are two options for this; we’ll look at Data Annotations in this section and then the fluent API in the next section.</span></span>

-   <span data-ttu-id="ae446-198">讓我們加入我們的模型中的使用者類別</span><span class="sxs-lookup"><span data-stu-id="ae446-198">Let’s add a User class to our model</span></span>

``` csharp
public class User
{
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   <span data-ttu-id="ae446-199">我們也需要將一組加入至我們的衍生內容</span><span class="sxs-lookup"><span data-stu-id="ae446-199">We also need to add a set to our derived context</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }
}
```

-   <span data-ttu-id="ae446-200">如果我們嘗試新增移轉會得到錯誤指出 「*EntityType 'User' 已定義任何索引鍵。定義索引鍵為此 EntityType。 」*</span><span class="sxs-lookup"><span data-stu-id="ae446-200">If we tried to add a migration we’d get an error saying “*EntityType ‘User’ has no key defined. Define the key for this EntityType.”*</span></span> <span data-ttu-id="ae446-201">因為 EF 有沒有辦法知道使用者名稱應該使用者的主索引鍵。</span><span class="sxs-lookup"><span data-stu-id="ae446-201">because EF has no way of knowing that Username should be the primary key for User.</span></span>
-   <span data-ttu-id="ae446-202">我們現在使用資料註解，因此我們需要新增 using 陳述式，在 Program.cs 的頂端</span><span class="sxs-lookup"><span data-stu-id="ae446-202">We’re going to use Data Annotations now so we need to add a using statement at the top of Program.cs</span></span>

```
using System.ComponentModel.DataAnnotations;
```

-   <span data-ttu-id="ae446-203">現在的使用者名稱屬性，來識別它是主索引鍵加上註解</span><span class="sxs-lookup"><span data-stu-id="ae446-203">Now annotate the Username property to identify that it is the primary key</span></span>

``` csharp
public class User
{
    [Key]
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   <span data-ttu-id="ae446-204">使用**Add-migration AddUser**命令來建立要套用這些移轉的結構變更至資料庫</span><span class="sxs-lookup"><span data-stu-id="ae446-204">Use the **Add-Migration AddUser** command to scaffold a migration to apply these changes to the database</span></span>
-   <span data-ttu-id="ae446-205">執行**Update-database**命令，以將新的移轉套用至資料庫</span><span class="sxs-lookup"><span data-stu-id="ae446-205">Run the **Update-Database** command to apply the new migration to the database</span></span>

<span data-ttu-id="ae446-206">新的資料表現在已新增至資料庫：</span><span class="sxs-lookup"><span data-stu-id="ae446-206">The new table is now added to the database:</span></span>

![與使用者的結構描述](~/ef6/media/schemawithusers.png)

<span data-ttu-id="ae446-208">是支援的 EF 的註解的完整清單：</span><span class="sxs-lookup"><span data-stu-id="ae446-208">The full list of annotations supported by EF is:</span></span>

-   [<span data-ttu-id="ae446-209">KeyAttribute</span><span class="sxs-lookup"><span data-stu-id="ae446-209">KeyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.keyattribute)
-   [<span data-ttu-id="ae446-210">StringLengthAttribute</span><span class="sxs-lookup"><span data-stu-id="ae446-210">StringLengthAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute)
-   [<span data-ttu-id="ae446-211">MaxLengthAttribute</span><span class="sxs-lookup"><span data-stu-id="ae446-211">MaxLengthAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.maxlengthattribute)
-   [<span data-ttu-id="ae446-212">ConcurrencyCheckAttribute</span><span class="sxs-lookup"><span data-stu-id="ae446-212">ConcurrencyCheckAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.concurrencycheckattribute)
-   [<span data-ttu-id="ae446-213">使用 RequiredAttribute</span><span class="sxs-lookup"><span data-stu-id="ae446-213">RequiredAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute)
-   [<span data-ttu-id="ae446-214">TimestampAttribute</span><span class="sxs-lookup"><span data-stu-id="ae446-214">TimestampAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute)
-   [<span data-ttu-id="ae446-215">ComplexTypeAttribute</span><span class="sxs-lookup"><span data-stu-id="ae446-215">ComplexTypeAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.complextypeattribute)
-   [<span data-ttu-id="ae446-216">ColumnAttribute</span><span class="sxs-lookup"><span data-stu-id="ae446-216">ColumnAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.columnattribute)
-   [<span data-ttu-id="ae446-217">TableAttribute</span><span class="sxs-lookup"><span data-stu-id="ae446-217">TableAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.tableattribute)
-   [<span data-ttu-id="ae446-218">InversePropertyAttribute</span><span class="sxs-lookup"><span data-stu-id="ae446-218">InversePropertyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.inversepropertyattribute)
-   [<span data-ttu-id="ae446-219">ForeignKeyAttribute</span><span class="sxs-lookup"><span data-stu-id="ae446-219">ForeignKeyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.foreignkeyattribute)
-   [<span data-ttu-id="ae446-220">DatabaseGeneratedAttribute</span><span class="sxs-lookup"><span data-stu-id="ae446-220">DatabaseGeneratedAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.databasegeneratedattribute)
-   [<span data-ttu-id="ae446-221">NotMappedAttribute</span><span class="sxs-lookup"><span data-stu-id="ae446-221">NotMappedAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.notmappedattribute)

## <a name="7-fluent-api"></a><span data-ttu-id="ae446-222">7.Fluent API</span><span class="sxs-lookup"><span data-stu-id="ae446-222">7. Fluent API</span></span>

<span data-ttu-id="ae446-223">上一節中我們探討了以補充或覆寫慣例所偵測到使用資料註解。</span><span class="sxs-lookup"><span data-stu-id="ae446-223">In the previous section we looked at using Data Annotations to supplement or override what was detected by convention.</span></span> <span data-ttu-id="ae446-224">若要設定模型的其他方式是透過 Code First fluent API。</span><span class="sxs-lookup"><span data-stu-id="ae446-224">The other way to configure the model is via the Code First fluent API.</span></span>

<span data-ttu-id="ae446-225">大部分的模型組態可使用簡單的資料註解。</span><span class="sxs-lookup"><span data-stu-id="ae446-225">Most model configuration can be done using simple data annotations.</span></span> <span data-ttu-id="ae446-226">Fluent API 是更進階的方式來指定涵蓋資料註解可以另外執行一些更進階的組態無法使用資料註解的一切的模型組態。</span><span class="sxs-lookup"><span data-stu-id="ae446-226">The fluent API is a more advanced way of specifying model configuration that covers everything that data annotations can do in addition to some more advanced configuration not possible with data annotations.</span></span> <span data-ttu-id="ae446-227">資料註釋和 fluent API 可以一起使用。</span><span class="sxs-lookup"><span data-stu-id="ae446-227">Data annotations and the fluent API can be used together.</span></span>

<span data-ttu-id="ae446-228">若要存取 fluent API 您覆寫在 DbContext 的 OnModelCreating 方法。</span><span class="sxs-lookup"><span data-stu-id="ae446-228">To access the fluent API you override the OnModelCreating method in DbContext.</span></span> <span data-ttu-id="ae446-229">假設我們想要重新命名 User.DisplayName，會顯示儲存在資料行\_名稱。</span><span class="sxs-lookup"><span data-stu-id="ae446-229">Let’s say we wanted to rename the column that User.DisplayName is stored in to display\_name.</span></span>

-   <span data-ttu-id="ae446-230">覆寫為 [bloggingcontext] 上的 OnModelCreating 方法，以下列程式碼</span><span class="sxs-lookup"><span data-stu-id="ae446-230">Override the OnModelCreating method on BloggingContext with the following code</span></span>

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

-   <span data-ttu-id="ae446-231">使用**Add-migration ChangeDisplayName**命令來建立要套用這些移轉的結構變更至資料庫。</span><span class="sxs-lookup"><span data-stu-id="ae446-231">Use the **Add-Migration ChangeDisplayName** command to scaffold a migration to apply these changes to the database.</span></span>
-   <span data-ttu-id="ae446-232">執行**Update-database**命令，以將新的移轉套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="ae446-232">Run the **Update-Database** command to apply the new migration to the database.</span></span>

<span data-ttu-id="ae446-233">{3&gt;displayname&lt;3} 資料行現在已重新命名以顯示\_名稱：</span><span class="sxs-lookup"><span data-stu-id="ae446-233">The DisplayName column is now renamed to display\_name:</span></span>

![重新命名的顯示名稱的結構描述](~/ef6/media/schemawithdisplaynamerenamed.png)

## <a name="summary"></a><span data-ttu-id="ae446-235">總結</span><span class="sxs-lookup"><span data-stu-id="ae446-235">Summary</span></span>

<span data-ttu-id="ae446-236">在此逐步解說中，我們討論過使用新的資料庫的 Code First 開發。</span><span class="sxs-lookup"><span data-stu-id="ae446-236">In this walkthrough we looked at Code First development using a new database.</span></span> <span data-ttu-id="ae446-237">我們會定義使用類別的模型，然後使用該模型來建立資料庫和儲存和擷取資料。</span><span class="sxs-lookup"><span data-stu-id="ae446-237">We defined a model using classes then used that model to create a database and store and retrieve data.</span></span> <span data-ttu-id="ae446-238">建立資料庫之後我們會使用 Code First 移轉變更我們發展的模型結構描述。</span><span class="sxs-lookup"><span data-stu-id="ae446-238">Once the database was created we used Code First Migrations to change the schema as our model evolved.</span></span> <span data-ttu-id="ae446-239">我們也了解如何將模型設定為使用資料註釋和 Fluent API。</span><span class="sxs-lookup"><span data-stu-id="ae446-239">We also saw how to configure a model using Data Annotations and the Fluent API.</span></span>
