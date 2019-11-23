---
title: Code First 到現有的資料庫-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: a7e60b74-973d-4480-868f-500a3899932e
ms.openlocfilehash: 61980bbd1f236f496a9d4fd92aa52264f1454615
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182624"
---
# <a name="code-first-to-an-existing-database"></a><span data-ttu-id="76bb4-102">Code First 到現有的資料庫</span><span class="sxs-lookup"><span data-stu-id="76bb4-102">Code First to an Existing Database</span></span>
<span data-ttu-id="76bb4-103">這段影片和逐步解說提供以現有資料庫為目標之 Code First 開發的簡介。</span><span class="sxs-lookup"><span data-stu-id="76bb4-103">This video and step-by-step walkthrough provide an introduction to Code First development targeting an existing database.</span></span> <span data-ttu-id="76bb4-104">Code First 可讓您使用 C\# 或 VB.Net 類別來定義模型。</span><span class="sxs-lookup"><span data-stu-id="76bb4-104">Code First allows you to define your model using C\# or VB.Net classes.</span></span> <span data-ttu-id="76bb4-105">您可以選擇性地使用類別和屬性上的屬性，或使用 Fluent API 來執行其他設定。</span><span class="sxs-lookup"><span data-stu-id="76bb4-105">Optionally additional configuration can be performed using attributes on your classes and properties or by using a fluent API.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="76bb4-106">觀看影片</span><span class="sxs-lookup"><span data-stu-id="76bb4-106">Watch the video</span></span>
<span data-ttu-id="76bb4-107">[Channel 9 現在提供](https://channel9.msdn.com/blogs/ef/code-first-to-existing-database-ef6-1-onwards-)這部影片。</span><span class="sxs-lookup"><span data-stu-id="76bb4-107">This video is [now available on Channel 9](https://channel9.msdn.com/blogs/ef/code-first-to-existing-database-ef6-1-onwards-).</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="76bb4-108">先決條件</span><span class="sxs-lookup"><span data-stu-id="76bb4-108">Pre-Requisites</span></span>

<span data-ttu-id="76bb4-109">您必須安裝**Visual Studio 2012**或**Visual Studio 2013** ，才能完成此逐步解說。</span><span class="sxs-lookup"><span data-stu-id="76bb4-109">You will need to have **Visual Studio 2012** or **Visual Studio 2013** installed to complete this walkthrough.</span></span>

<span data-ttu-id="76bb4-110">您也將需要安裝**Visual Studio 的 Entity Framework Tools**版本**6.1** （或更新版本）。</span><span class="sxs-lookup"><span data-stu-id="76bb4-110">You will also need version **6.1** (or later) of the **Entity Framework Tools for Visual Studio** installed.</span></span> <span data-ttu-id="76bb4-111">如需安裝最新版本 Entity Framework Tools 的詳細資訊，請參閱[取得 Entity Framework](~/ef6/fundamentals/install.md) 。</span><span class="sxs-lookup"><span data-stu-id="76bb4-111">See [Get Entity Framework](~/ef6/fundamentals/install.md) for information on installing the latest version of the Entity Framework Tools.</span></span>

## <a name="1-create-an-existing-database"></a><span data-ttu-id="76bb4-112">1. 建立現有的資料庫</span><span class="sxs-lookup"><span data-stu-id="76bb4-112">1. Create an Existing Database</span></span>

<span data-ttu-id="76bb4-113">通常當您將目標設為現有的資料庫時，就會建立它，但在此逐步解說中，我們需要建立要存取的資料庫。</span><span class="sxs-lookup"><span data-stu-id="76bb4-113">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="76bb4-114">讓我們繼續產生資料庫。</span><span class="sxs-lookup"><span data-stu-id="76bb4-114">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="76bb4-115">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76bb4-115">Open Visual Studio</span></span>
-   <span data-ttu-id="76bb4-116">**View&gt; 伺服器總管**</span><span class="sxs-lookup"><span data-stu-id="76bb4-116">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="76bb4-117">以滑鼠右鍵按一下 **資料連線-&gt; 新增連接 ...**</span><span class="sxs-lookup"><span data-stu-id="76bb4-117">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="76bb4-118">如果您還沒有從**伺服器總管**連接到資料庫，則必須選取 [ **Microsoft SQL Server** ] 做為資料來源</span><span class="sxs-lookup"><span data-stu-id="76bb4-118">If you haven’t connected to a database from **Server Explorer** before you’ll need to select **Microsoft SQL Server** as the data source</span></span>

    ![選取資料來源](~/ef6/media/selectdatasource.png)

-   <span data-ttu-id="76bb4-120">連線到您的 LocalDB 實例，並輸入 [**日誌**] 做為資料庫名稱</span><span class="sxs-lookup"><span data-stu-id="76bb4-120">Connect to your LocalDB instance, and enter **Blogging** as the database name</span></span>

    ![LocalDB 連接](~/ef6/media/localdbconnection.png)

-   <span data-ttu-id="76bb4-122">選取 **[確定]** ，系統會詢問您是否要建立新的資料庫，然後選取 **[是]**</span><span class="sxs-lookup"><span data-stu-id="76bb4-122">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![建立資料庫對話方塊](~/ef6/media/createdatabasedialog.png)

-   <span data-ttu-id="76bb4-124">新的資料庫現在會出現在伺服器總管中，以滑鼠右鍵按一下它，然後選取 [追加**查詢**]</span><span class="sxs-lookup"><span data-stu-id="76bb4-124">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="76bb4-125">將下列 SQL 複製到新的查詢中，然後以滑鼠右鍵按一下查詢並選取 [**執行**]。</span><span class="sxs-lookup"><span data-stu-id="76bb4-125">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

``` SQL
CREATE TABLE [dbo].[Blogs] (
    [BlogId] INT IDENTITY (1, 1) NOT NULL,
    [Name] NVARCHAR (200) NULL,
    [Url]  NVARCHAR (200) NULL,
    CONSTRAINT [PK_dbo.Blogs] PRIMARY KEY CLUSTERED ([BlogId] ASC)
);

CREATE TABLE [dbo].[Posts] (
    [PostId] INT IDENTITY (1, 1) NOT NULL,
    [Title] NVARCHAR (200) NULL,
    [Content] NTEXT NULL,
    [BlogId] INT NOT NULL,
    CONSTRAINT [PK_dbo.Posts] PRIMARY KEY CLUSTERED ([PostId] ASC),
    CONSTRAINT [FK_dbo.Posts_dbo.Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [dbo].[Blogs] ([BlogId]) ON DELETE CASCADE
);

INSERT INTO [dbo].[Blogs] ([Name],[Url])
VALUES ('The Visual Studio Blog', 'http://blogs.msdn.com/visualstudio/')

INSERT INTO [dbo].[Blogs] ([Name],[Url])
VALUES ('.NET Framework Blog', 'http://blogs.msdn.com/dotnet/')
```

## <a name="2-create-the-application"></a><span data-ttu-id="76bb4-126">2. 建立應用程式</span><span class="sxs-lookup"><span data-stu-id="76bb4-126">2. Create the Application</span></span>

<span data-ttu-id="76bb4-127">為了簡單起見，我們將建立一個使用 Code First 來執行資料存取的基本主控台應用程式：</span><span class="sxs-lookup"><span data-stu-id="76bb4-127">To keep things simple we’re going to build a basic console application that uses Code First to perform data access:</span></span>

-   <span data-ttu-id="76bb4-128">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76bb4-128">Open Visual Studio</span></span>
-   <span data-ttu-id="76bb4-129">**檔案&gt; 新&gt; 專案 。**</span><span class="sxs-lookup"><span data-stu-id="76bb4-129">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="76bb4-130">從左側功能表和**主控台應用程式**中選取 [ **Windows** ]</span><span class="sxs-lookup"><span data-stu-id="76bb4-130">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="76bb4-131">輸入**CodeFirstExistingDatabaseSample**作為名稱</span><span class="sxs-lookup"><span data-stu-id="76bb4-131">Enter **CodeFirstExistingDatabaseSample** as the name</span></span>
-   <span data-ttu-id="76bb4-132">選取 [確定]</span><span class="sxs-lookup"><span data-stu-id="76bb4-132">Select **OK**</span></span>

 

## <a name="3-reverse-engineer-model"></a><span data-ttu-id="76bb4-133">3. 反向工程模型</span><span class="sxs-lookup"><span data-stu-id="76bb4-133">3. Reverse Engineer Model</span></span>

<span data-ttu-id="76bb4-134">我們將使用 Visual Studio 的 Entity Framework Tools，協助我們產生一些要對應到資料庫的初始程式碼。</span><span class="sxs-lookup"><span data-stu-id="76bb4-134">We’re going to make use of the Entity Framework Tools for Visual Studio to help us generate some initial code to map to the database.</span></span> <span data-ttu-id="76bb4-135">這些工具只會產生程式碼，如果您想要的話，也可以手動輸入。</span><span class="sxs-lookup"><span data-stu-id="76bb4-135">These tools are just generating code that you could also type by hand if you prefer.</span></span>

-   <span data-ttu-id="76bb4-136">**專案-&gt; 加入新專案 。**</span><span class="sxs-lookup"><span data-stu-id="76bb4-136">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="76bb4-137">從左側功能表中選取 [**資料**]，然後**ADO.NET 實體資料模型**</span><span class="sxs-lookup"><span data-stu-id="76bb4-137">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="76bb4-138">輸入**BloggingCoNtext**作為名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="76bb4-138">Enter **BloggingContext** as the name and click **OK**</span></span>
-   <span data-ttu-id="76bb4-139">這會啟動**實體資料模型 Wizard**</span><span class="sxs-lookup"><span data-stu-id="76bb4-139">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="76bb4-140">選取 [**從資料庫 Code First** ]，然後按 **[下一步]**</span><span class="sxs-lookup"><span data-stu-id="76bb4-140">Select **Code First from Database** and click **Next**</span></span>

    ![Wizard One CFE](~/ef6/media/wizardonecfe.png)

-   <span data-ttu-id="76bb4-142">選取您在第一節中建立之資料庫的連接，然後按 **[下一步]**</span><span class="sxs-lookup"><span data-stu-id="76bb4-142">Select the connection to the database you created in the first section and click **Next**</span></span>

    ![Wizard 兩個 CFE](~/ef6/media/wizardtwocfe.png)

-   <span data-ttu-id="76bb4-144">按一下 [**資料表**] 旁的核取方塊以匯入所有資料表，然後按一下 **[完成]** 。</span><span class="sxs-lookup"><span data-stu-id="76bb4-144">Click the checkbox next to **Tables** to import all tables and click **Finish**</span></span>

    ![Wizard 三 CFE](~/ef6/media/wizardthreecfe.png)

<span data-ttu-id="76bb4-146">當反向工程程式完成之後，專案中會加入數個專案，讓我們來看一下新增了什麼內容。</span><span class="sxs-lookup"><span data-stu-id="76bb4-146">Once the reverse engineer process completes a number of items will have been added to the project, let's take a look at what's been added.</span></span>

### <a name="configuration-file"></a><span data-ttu-id="76bb4-147">組態檔</span><span class="sxs-lookup"><span data-stu-id="76bb4-147">Configuration file</span></span>

<span data-ttu-id="76bb4-148">已將 App.config 檔案加入至專案中，此檔案包含現有資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="76bb4-148">An App.config file has been added to the project, this file contains the connection string to the existing database.</span></span>

``` xml
<connectionStrings>
  <add  
    name="BloggingContext"  
    connectionString="data source=(localdb)\mssqllocaldb;initial catalog=Blogging;integrated security=True;MultipleActiveResultSets=True;App=EntityFramework"  
    providerName="System.Data.SqlClient" />
</connectionStrings>
```

<span data-ttu-id="76bb4-149">*您也會注意到設定檔案中的其他設定，這些都是預設的 EF 設定，告訴 Code First 在何處建立資料庫。因為我們要對應到現有的資料庫，所以我們的應用程式將會忽略這些設定。*</span><span class="sxs-lookup"><span data-stu-id="76bb4-149">*You’ll notice some other settings in the configuration file too, these are default EF settings that tell Code First where to create databases. Since we are mapping to an existing database these setting will be ignored in our application.*</span></span>

### <a name="derived-context"></a><span data-ttu-id="76bb4-150">衍生內容</span><span class="sxs-lookup"><span data-stu-id="76bb4-150">Derived Context</span></span>

<span data-ttu-id="76bb4-151">已將**BloggingCoNtext**類別加入至專案。</span><span class="sxs-lookup"><span data-stu-id="76bb4-151">A **BloggingContext** class has been added to the project.</span></span> <span data-ttu-id="76bb4-152">內容代表資料庫的會話，可讓我們查詢和儲存資料。</span><span class="sxs-lookup"><span data-stu-id="76bb4-152">The context represents a session with the database, allowing us to query and save data.</span></span>
<span data-ttu-id="76bb4-153">內容會公開模型中每個類型的**DbSet&lt;TEntity&gt;** 。</span><span class="sxs-lookup"><span data-stu-id="76bb4-153">The context exposes a **DbSet&lt;TEntity&gt;** for each type in our model.</span></span> <span data-ttu-id="76bb4-154">您也會注意到，預設的函式會使用**name =** 語法來呼叫基底的函數。</span><span class="sxs-lookup"><span data-stu-id="76bb4-154">You’ll also notice that the default constructor calls a base constructor using the **name=** syntax.</span></span> <span data-ttu-id="76bb4-155">這會告訴 Code First 此內容所使用的連接字串應該從設定檔載入。</span><span class="sxs-lookup"><span data-stu-id="76bb4-155">This tells Code First that the connection string to use for this context should be loaded from the configuration file.</span></span>

``` csharp
public partial class BloggingContext : DbContext
    {
        public BloggingContext()
            : base("name=BloggingContext")
        {
        }

        public virtual DbSet<Blog> Blogs { get; set; }
        public virtual DbSet<Post> Posts { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
        }
    }
```

<span data-ttu-id="76bb4-156">*當您使用設定檔中的連接字串時，請一律使用\*\*name =*\* 語法。這可確保如果連接字串不存在，Entity Framework 會擲回，而不是依照慣例建立新的資料庫。\*</span><span class="sxs-lookup"><span data-stu-id="76bb4-156">*You should always use the **name=** syntax when you are using a connection string in the config file. This ensures that if the connection string is not present then Entity Framework will throw rather than creating a new database by convention.*</span></span>

### <a name="model-classes"></a><span data-ttu-id="76bb4-157">模型類別</span><span class="sxs-lookup"><span data-stu-id="76bb4-157">Model classes</span></span>

<span data-ttu-id="76bb4-158">最後，也已將**Blog**和**Post**類別加入至專案。</span><span class="sxs-lookup"><span data-stu-id="76bb4-158">Finally, a **Blog** and **Post** class have also been added to the project.</span></span> <span data-ttu-id="76bb4-159">這些是組成模型的網域類別。</span><span class="sxs-lookup"><span data-stu-id="76bb4-159">These are the domain classes that make up our model.</span></span> <span data-ttu-id="76bb4-160">您會看到套用至類別的資料批註來指定設定，其中 Code First 慣例不會與現有資料庫的結構對齊。</span><span class="sxs-lookup"><span data-stu-id="76bb4-160">You'll see Data Annotations applied to the classes to specify configuration where the Code First conventions would not align with the structure of the existing database.</span></span> <span data-ttu-id="76bb4-161">例如，您會在**Blog.Name**和**Blog**上看到**StringLength**注釋，因為在資料庫中，其最大長度為**200** （Code First 預設為使用資料庫提供者所支援的上限長度，SQL Server 中為**Nvarchar （max）** ）。</span><span class="sxs-lookup"><span data-stu-id="76bb4-161">For example, you'll see the **StringLength** annotation on **Blog.Name** and **Blog.Url** since they have a maximum length of **200** in the database (the Code First default is to use the maximun length supported by the database provider - **nvarchar(max)** in SQL Server).</span></span>

``` csharp
public partial class Blog
{
    public Blog()
    {
        Posts = new HashSet<Post>();
    }

    public int BlogId { get; set; }

    [StringLength(200)]
    public string Name { get; set; }

    [StringLength(200)]
    public string Url { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}
```

## <a name="4-reading--writing-data"></a><span data-ttu-id="76bb4-162">4. 讀取 & 寫入資料</span><span class="sxs-lookup"><span data-stu-id="76bb4-162">4. Reading & Writing Data</span></span>

<span data-ttu-id="76bb4-163">既然我們已經有模型，就可以使用它來存取一些資料。</span><span class="sxs-lookup"><span data-stu-id="76bb4-163">Now that we have a model it’s time to use it to access some data.</span></span> <span data-ttu-id="76bb4-164">在**Program.cs**中執行**Main**方法，如下所示。</span><span class="sxs-lookup"><span data-stu-id="76bb4-164">Implement the **Main** method in **Program.cs** as shown below.</span></span> <span data-ttu-id="76bb4-165">此程式碼會建立內容的新實例，然後使用它來插入新的**Blog**。</span><span class="sxs-lookup"><span data-stu-id="76bb4-165">This code creates a new instance of our context and then uses it to insert a new **Blog**.</span></span> <span data-ttu-id="76bb4-166">然後，它會使用 LINQ 查詢來抓取資料庫中依**標題**字母順序排序的所有**blog** 。</span><span class="sxs-lookup"><span data-stu-id="76bb4-166">Then it uses a LINQ query to retrieve all **Blogs** from the database ordered alphabetically by **Title**.</span></span>

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

<span data-ttu-id="76bb4-167">您現在可以執行應用程式並加以測試。</span><span class="sxs-lookup"><span data-stu-id="76bb4-167">You can now run the application and test it out.</span></span>

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
.NET Framework Blog
ADO.NET Blog
The Visual Studio Blog
Press any key to exit...
```
 
## <a name="what-if-my-database-changes"></a><span data-ttu-id="76bb4-168">如果我的資料庫有所變更，該怎麼辦？</span><span class="sxs-lookup"><span data-stu-id="76bb4-168">What if My Database Changes?</span></span>

<span data-ttu-id="76bb4-169">[Code First 至資料庫] wizard 的設計，是要產生一組可供您調整和修改的起點類別。</span><span class="sxs-lookup"><span data-stu-id="76bb4-169">The Code First to Database wizard is designed to generate a starting point set of classes that you can then tweak and modify.</span></span> <span data-ttu-id="76bb4-170">如果您的資料庫架構變更，您可以手動編輯類別，或執行其他反向工程來覆寫類別。</span><span class="sxs-lookup"><span data-stu-id="76bb4-170">If your database schema changes you can either manually edit the classes or perform another reverse engineer to overwrite the classes.</span></span>

## <a name="using-code-first-migrations-to-an-existing-database"></a><span data-ttu-id="76bb4-171">使用現有資料庫的 Code First 移轉</span><span class="sxs-lookup"><span data-stu-id="76bb4-171">Using Code First Migrations to an Existing Database</span></span>

<span data-ttu-id="76bb4-172">如果您想要搭配現有的資料庫使用 Code First 移轉，請參閱[Code First 移轉至現有的資料庫](~/ef6/modeling/code-first/migrations/existing-database.md)。</span><span class="sxs-lookup"><span data-stu-id="76bb4-172">If you want to use Code First Migrations with an existing database, see [Code First Migrations to an existing database](~/ef6/modeling/code-first/migrations/existing-database.md).</span></span>

## <a name="summary"></a><span data-ttu-id="76bb4-173">摘要</span><span class="sxs-lookup"><span data-stu-id="76bb4-173">Summary</span></span>

<span data-ttu-id="76bb4-174">在本逐步解說中，我們探討了使用現有資料庫的 Code First 開發。</span><span class="sxs-lookup"><span data-stu-id="76bb4-174">In this walkthrough we looked at Code First development using an existing database.</span></span> <span data-ttu-id="76bb4-175">我們使用 Visual Studio 的 Entity Framework Tools，對對應至資料庫的一組類別進行反向工程，並可用來儲存和抓取資料。</span><span class="sxs-lookup"><span data-stu-id="76bb4-175">We used the Entity Framework Tools for Visual Studio to reverse engineer a set of classes that mapped to the database and could be used to store and retrieve data.</span></span>
