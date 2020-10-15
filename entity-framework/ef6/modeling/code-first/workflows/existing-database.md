---
title: Code First 至現有的資料庫-EF6
description: Code First 至 Entity Framework 6 中的現有資料庫
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/workflows/existing-database
ms.openlocfilehash: 2305236f68cfdc9f6e1582cca1163abf380b4075
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066417"
---
# <a name="code-first-to-an-existing-database"></a><span data-ttu-id="fde0e-103">Code First 至現有的資料庫</span><span class="sxs-lookup"><span data-stu-id="fde0e-103">Code First to an Existing Database</span></span>
<span data-ttu-id="fde0e-104">這段影片和逐步解說將提供以現有資料庫為目標的 Code First 開發簡介。</span><span class="sxs-lookup"><span data-stu-id="fde0e-104">This video and step-by-step walkthrough provide an introduction to Code First development targeting an existing database.</span></span> <span data-ttu-id="fde0e-105">Code First 可讓您使用 C 或 VB.Net 類別來定義您的模型 \# 。</span><span class="sxs-lookup"><span data-stu-id="fde0e-105">Code First allows you to define your model using C\# or VB.Net classes.</span></span> <span data-ttu-id="fde0e-106">您也可以使用類別和屬性上的屬性，或使用流暢的 API，來執行其他設定。</span><span class="sxs-lookup"><span data-stu-id="fde0e-106">Optionally additional configuration can be performed using attributes on your classes and properties or by using a fluent API.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="fde0e-107">觀賞影片</span><span class="sxs-lookup"><span data-stu-id="fde0e-107">Watch the video</span></span>
<span data-ttu-id="fde0e-108">這段影片 [現在可在 Channel 9 上](https://channel9.msdn.com/blogs/ef/code-first-to-existing-database-ef6-1-onwards-)取得。</span><span class="sxs-lookup"><span data-stu-id="fde0e-108">This video is [now available on Channel 9](https://channel9.msdn.com/blogs/ef/code-first-to-existing-database-ef6-1-onwards-).</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="fde0e-109">必要條件</span><span class="sxs-lookup"><span data-stu-id="fde0e-109">Pre-Requisites</span></span>

<span data-ttu-id="fde0e-110">您必須安裝 **Visual Studio 2012** 或 **Visual Studio 2013** ，才能完成此逐步解說。</span><span class="sxs-lookup"><span data-stu-id="fde0e-110">You will need to have **Visual Studio 2012** or **Visual Studio 2013** installed to complete this walkthrough.</span></span>

<span data-ttu-id="fde0e-111">您也需要安裝 Visual Studio 版本 **6.1** (或更新版本的 **Entity Framework Tools**) 。</span><span class="sxs-lookup"><span data-stu-id="fde0e-111">You will also need version **6.1** (or later) of the **Entity Framework Tools for Visual Studio** installed.</span></span> <span data-ttu-id="fde0e-112">如需安裝最新版本 Entity Framework Tools 的詳細資訊，請參閱 [取得 Entity Framework](xref:ef6/fundamentals/install) 。</span><span class="sxs-lookup"><span data-stu-id="fde0e-112">See [Get Entity Framework](xref:ef6/fundamentals/install) for information on installing the latest version of the Entity Framework Tools.</span></span>

## <a name="1-create-an-existing-database"></a><span data-ttu-id="fde0e-113">1. 建立現有的資料庫</span><span class="sxs-lookup"><span data-stu-id="fde0e-113">1. Create an Existing Database</span></span>

<span data-ttu-id="fde0e-114">通常當您將目標設為現有的資料庫時，就會建立該資料庫，但在此逐步解說中，我們需要建立要存取的資料庫。</span><span class="sxs-lookup"><span data-stu-id="fde0e-114">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="fde0e-115">讓我們繼續產生資料庫。</span><span class="sxs-lookup"><span data-stu-id="fde0e-115">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="fde0e-116">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fde0e-116">Open Visual Studio</span></span>
-   <span data-ttu-id="fde0e-117">**View- &gt; 伺服器總管**</span><span class="sxs-lookup"><span data-stu-id="fde0e-117">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="fde0e-118">以滑鼠右鍵按一下 [**資料連線- &gt; 新增連接 ...** ]</span><span class="sxs-lookup"><span data-stu-id="fde0e-118">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="fde0e-119">如果您還沒有從 **伺服器總管** 連接到資料庫，則必須選取 **Microsoft SQL Server** 作為資料來源</span><span class="sxs-lookup"><span data-stu-id="fde0e-119">If you haven’t connected to a database from **Server Explorer** before you’ll need to select **Microsoft SQL Server** as the data source</span></span>

    ![選取資料來源](~/ef6/media/selectdatasource.png)

-   <span data-ttu-id="fde0e-121">連接到您的 LocalDB 實例，並輸入 [程式 **撰寫** ] 作為資料庫名稱</span><span class="sxs-lookup"><span data-stu-id="fde0e-121">Connect to your LocalDB instance, and enter **Blogging** as the database name</span></span>

    ![LocalDB 連接](~/ef6/media/localdbconnection.png)

-   <span data-ttu-id="fde0e-123">選取 **[確定]** ，系統會詢問您是否要建立新的資料庫，請選取 **[是]** 。</span><span class="sxs-lookup"><span data-stu-id="fde0e-123">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![[建立資料庫] 對話方塊](~/ef6/media/createdatabasedialog.png)

-   <span data-ttu-id="fde0e-125">新資料庫現在會出現在伺服器總管中，以滑鼠右鍵按一下該資料庫，然後選取 [追加**查詢**]。</span><span class="sxs-lookup"><span data-stu-id="fde0e-125">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="fde0e-126">將下列 SQL 複製到新的查詢，然後以滑鼠右鍵按一下查詢並選取 [**執行**]</span><span class="sxs-lookup"><span data-stu-id="fde0e-126">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

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

## <a name="2-create-the-application"></a><span data-ttu-id="fde0e-127">2. 建立應用程式</span><span class="sxs-lookup"><span data-stu-id="fde0e-127">2. Create the Application</span></span>

<span data-ttu-id="fde0e-128">為了簡單起見，我們將建立一個基本的主控台應用程式，使用 Code First 來執行資料存取：</span><span class="sxs-lookup"><span data-stu-id="fde0e-128">To keep things simple we will build a basic console application that uses Code First to do the data access:</span></span>

-   <span data-ttu-id="fde0e-129">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fde0e-129">Open Visual Studio</span></span>
-   <span data-ttu-id="fde0e-130">**檔案- &gt; 新增- &gt; 專案 .。。**</span><span class="sxs-lookup"><span data-stu-id="fde0e-130">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="fde0e-131">從左側功能表和**主控台應用程式**選取 [ **Windows** ]</span><span class="sxs-lookup"><span data-stu-id="fde0e-131">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="fde0e-132">輸入 **CodeFirstExistingDatabaseSample** 作為名稱</span><span class="sxs-lookup"><span data-stu-id="fde0e-132">Enter **CodeFirstExistingDatabaseSample** as the name</span></span>
-   <span data-ttu-id="fde0e-133">選取 [確定]</span><span class="sxs-lookup"><span data-stu-id="fde0e-133">Select **OK**</span></span>

 

## <a name="3-reverse-engineer-model"></a><span data-ttu-id="fde0e-134">3. 反轉工程模型</span><span class="sxs-lookup"><span data-stu-id="fde0e-134">3. Reverse Engineer Model</span></span>

<span data-ttu-id="fde0e-135">我們將使用 Visual Studio 的 Entity Framework Tools，以協助我們產生一些初始程式碼來對應到資料庫。</span><span class="sxs-lookup"><span data-stu-id="fde0e-135">We will use the Entity Framework Tools for Visual Studio to help us generate some initial code to map to the database.</span></span> <span data-ttu-id="fde0e-136">這些工具只會產生程式碼，如果您想要的話，也可以手動輸入。</span><span class="sxs-lookup"><span data-stu-id="fde0e-136">These tools are just generating code that you could also type by hand if you prefer.</span></span>

-   <span data-ttu-id="fde0e-137">**專案- &gt; 加入新專案 .。。**</span><span class="sxs-lookup"><span data-stu-id="fde0e-137">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="fde0e-138">從左側功能表中選取 [ **資料** ]，然後 **ADO.NET 實體資料模型**</span><span class="sxs-lookup"><span data-stu-id="fde0e-138">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="fde0e-139">輸入 **BloggingCoNtext** 做為名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="fde0e-139">Enter **BloggingContext** as the name and click **OK**</span></span>
-   <span data-ttu-id="fde0e-140">這會啟動 **實體資料模型 Wizard**</span><span class="sxs-lookup"><span data-stu-id="fde0e-140">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="fde0e-141">**從資料庫選取 Code First** ，然後按 **[下一步]**</span><span class="sxs-lookup"><span data-stu-id="fde0e-141">Select **Code First from Database** and click **Next**</span></span>

    ![Wizard One CFE](~/ef6/media/wizardonecfe.png)

-   <span data-ttu-id="fde0e-143">選取您在第一個區段中建立之資料庫的連接，然後按 [ **下一步]**</span><span class="sxs-lookup"><span data-stu-id="fde0e-143">Select the connection to the database you created in the first section and click **Next**</span></span>

    ![Wizard 兩 CFE](~/ef6/media/wizardtwocfe.png)

-   <span data-ttu-id="fde0e-145">按一下 [**資料表**] 旁的核取方塊以匯入所有資料表，然後按一下 **[完成]** 。</span><span class="sxs-lookup"><span data-stu-id="fde0e-145">Click the checkbox next to **Tables** to import all tables and click **Finish**</span></span>

    ![Wizard 三 CFE](~/ef6/media/wizardthreecfe.png)

<span data-ttu-id="fde0e-147">當反向工程程式完成之後，專案中會加入一些專案，讓我們看看已新增的內容。</span><span class="sxs-lookup"><span data-stu-id="fde0e-147">Once the reverse engineer process completes a number of items will have been added to the project, let's take a look at what's been added.</span></span>

### <a name="configuration-file"></a><span data-ttu-id="fde0e-148">組態檔</span><span class="sxs-lookup"><span data-stu-id="fde0e-148">Configuration file</span></span>

<span data-ttu-id="fde0e-149">專案中已加入 App.config 檔案，此檔案包含現有資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="fde0e-149">An App.config file has been added to the project, this file contains the connection string to the existing database.</span></span>

``` xml
<connectionStrings>
  <add  
    name="BloggingContext"  
    connectionString="data source=(localdb)\mssqllocaldb;initial catalog=Blogging;integrated security=True;MultipleActiveResultSets=True;App=EntityFramework"  
    providerName="System.Data.SqlClient" />
</connectionStrings>
```

<span data-ttu-id="fde0e-150">*您也會注意到設定檔中的其他設定，這些都是預設的 EF 設定，告訴 Code First 在何處建立資料庫。因為我們會對應到現有的資料庫，所以我們的應用程式會忽略這些設定。*</span><span class="sxs-lookup"><span data-stu-id="fde0e-150">*You’ll notice some other settings in the configuration file too, these are default EF settings that tell Code First where to create databases. Since we are mapping to an existing database these setting will be ignored in our application.*</span></span>

### <a name="derived-context"></a><span data-ttu-id="fde0e-151">衍生內容</span><span class="sxs-lookup"><span data-stu-id="fde0e-151">Derived Context</span></span>

<span data-ttu-id="fde0e-152">已將 **BloggingCoNtext** 類別加入至專案。</span><span class="sxs-lookup"><span data-stu-id="fde0e-152">A **BloggingContext** class has been added to the project.</span></span> <span data-ttu-id="fde0e-153">內容代表與資料庫的會話，可讓我們查詢和儲存資料。</span><span class="sxs-lookup"><span data-stu-id="fde0e-153">The context represents a session with the database, allowing us to query and save data.</span></span>
<span data-ttu-id="fde0e-154">此內容會針對模型中的每個類型公開\*\*DbSet &lt; TEntity &gt; \*\* 。</span><span class="sxs-lookup"><span data-stu-id="fde0e-154">The context exposes a **DbSet&lt;TEntity&gt;** for each type in our model.</span></span> <span data-ttu-id="fde0e-155">您也會注意到，預設的函式會使用 **name =** 語法來呼叫基底函式。</span><span class="sxs-lookup"><span data-stu-id="fde0e-155">You’ll also notice that the default constructor calls a base constructor using the **name=** syntax.</span></span> <span data-ttu-id="fde0e-156">這會告訴 Code First 應從設定檔載入要用於此內容的連接字串。</span><span class="sxs-lookup"><span data-stu-id="fde0e-156">This tells Code First that the connection string to use for this context should be loaded from the configuration file.</span></span>

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

<span data-ttu-id="fde0e-157">*當您在設定檔中使用連接字串時，您應該一律使用 **name =** 語法。這可確保如果連接字串不存在，Entity Framework 將會擲回，而不是依照慣例建立新的資料庫。*</span><span class="sxs-lookup"><span data-stu-id="fde0e-157">*You should always use the **name=** syntax when you are using a connection string in the config file. This ensures that if the connection string is not present then Entity Framework will throw rather than creating a new database by convention.*</span></span>

### <a name="model-classes"></a><span data-ttu-id="fde0e-158">模型類別</span><span class="sxs-lookup"><span data-stu-id="fde0e-158">Model classes</span></span>

<span data-ttu-id="fde0e-159">最後，也已將 **Blog** 和 **Post** 類別新增至專案。</span><span class="sxs-lookup"><span data-stu-id="fde0e-159">Finally, a **Blog** and **Post** class have also been added to the project.</span></span> <span data-ttu-id="fde0e-160">這些是組成模型的網域類別。</span><span class="sxs-lookup"><span data-stu-id="fde0e-160">These are the domain classes that make up our model.</span></span> <span data-ttu-id="fde0e-161">您會看到套用至類別的資料批註來指定設定，其中 Code First 慣例不會與現有資料庫的結構一致。</span><span class="sxs-lookup"><span data-stu-id="fde0e-161">You'll see Data Annotations applied to the classes to specify configuration where the Code First conventions would not align with the structure of the existing database.</span></span> <span data-ttu-id="fde0e-162">例如，您會在**Blog.Name**和**Blog**上看到**StringLength**批註，因為它們在資料庫中的最大長度為**200** (Code First 預設值是使用資料庫提供者所支援的上限長度-Nvarchar (SQL Server) 中的\*\*最大) \*\* 。</span><span class="sxs-lookup"><span data-stu-id="fde0e-162">For example, you'll see the **StringLength** annotation on **Blog.Name** and **Blog.Url** since they have a maximum length of **200** in the database (the Code First default is to use the maximun length supported by the database provider - **nvarchar(max)** in SQL Server).</span></span>

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

## <a name="4-reading--writing-data"></a><span data-ttu-id="fde0e-163">4. 讀取 & 寫入資料</span><span class="sxs-lookup"><span data-stu-id="fde0e-163">4. Reading & Writing Data</span></span>

<span data-ttu-id="fde0e-164">現在我們已有模型，您可以使用它來存取某些資料。</span><span class="sxs-lookup"><span data-stu-id="fde0e-164">Now that we have a model it’s time to use it to access some data.</span></span> <span data-ttu-id="fde0e-165">在**Program.cs**中執行**Main**方法，如下所示。</span><span class="sxs-lookup"><span data-stu-id="fde0e-165">Implement the **Main** method in **Program.cs** as shown below.</span></span> <span data-ttu-id="fde0e-166">這段程式碼會建立內容的新實例，然後使用它來插入新的 **Blog**。</span><span class="sxs-lookup"><span data-stu-id="fde0e-166">This code creates a new instance of our context and then uses it to insert a new **Blog**.</span></span> <span data-ttu-id="fde0e-167">然後，它會使用 LINQ 查詢來從資料庫中依**標題**的字母順序來取得所有的**blog** 。</span><span class="sxs-lookup"><span data-stu-id="fde0e-167">Then it uses a LINQ query to retrieve all **Blogs** from the database ordered alphabetically by **Title**.</span></span>

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

<span data-ttu-id="fde0e-168">您現在可以執行應用程式並進行測試。</span><span class="sxs-lookup"><span data-stu-id="fde0e-168">You can now run the application and test it.</span></span>

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
.NET Framework Blog
ADO.NET Blog
The Visual Studio Blog
Press any key to exit...
```
 
## <a name="what-if-my-database-changes"></a><span data-ttu-id="fde0e-169">如果我的資料庫有所變更，該怎麼辦？</span><span class="sxs-lookup"><span data-stu-id="fde0e-169">What if My Database Changes?</span></span>

<span data-ttu-id="fde0e-170">[資料庫的 Code First wizard] 是設計用來產生類別的起始點集合，然後您可以進行調整和修改。</span><span class="sxs-lookup"><span data-stu-id="fde0e-170">The Code First to Database wizard is designed to generate a starting point set of classes that you can then tweak and modify.</span></span> <span data-ttu-id="fde0e-171">如果您的資料庫架構變更，您可以手動編輯類別，或執行另一個反向工程來覆寫類別。</span><span class="sxs-lookup"><span data-stu-id="fde0e-171">If your database schema changes you can either manually edit the classes or perform another reverse engineer to overwrite the classes.</span></span>

## <a name="using-code-first-migrations-to-an-existing-database"></a><span data-ttu-id="fde0e-172">使用現有資料庫的 Code First 移轉</span><span class="sxs-lookup"><span data-stu-id="fde0e-172">Using Code First Migrations to an Existing Database</span></span>

<span data-ttu-id="fde0e-173">如果您想要搭配現有的資料庫使用 Code First 移轉，請參閱 [Code First 移轉至現有的資料庫](xref:ef6/modeling/code-first/migrations/existing-database)。</span><span class="sxs-lookup"><span data-stu-id="fde0e-173">If you want to use Code First Migrations with an existing database, see [Code First Migrations to an existing database](xref:ef6/modeling/code-first/migrations/existing-database).</span></span>

## <a name="summary"></a><span data-ttu-id="fde0e-174">摘要</span><span class="sxs-lookup"><span data-stu-id="fde0e-174">Summary</span></span>

<span data-ttu-id="fde0e-175">在這個逐步解說中，我們探討了如何使用現有的資料庫進行 Code First 開發。</span><span class="sxs-lookup"><span data-stu-id="fde0e-175">In this walkthrough we looked at Code First development using an existing database.</span></span> <span data-ttu-id="fde0e-176">我們使用了 Visual Studio 的 Entity Framework Tools，針對對應至資料庫的一組類別進行反向工程，並可用來儲存和取出資料。</span><span class="sxs-lookup"><span data-stu-id="fde0e-176">We used the Entity Framework Tools for Visual Studio to reverse engineer a set of classes that mapped to the database and could be used to store and retrieve data.</span></span>
