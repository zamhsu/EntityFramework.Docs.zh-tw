---
title: 現有的資料庫-EF6 的 code First
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: a7e60b74-973d-4480-868f-500a3899932e
caps.latest.revision: 3
ms.openlocfilehash: 47581a7ae9ff534d26ce82365bcbe2b254247495
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2018
ms.locfileid: "39120335"
---
# <a name="code-first-to-an-existing-database"></a><span data-ttu-id="7e37f-102">Code First 至現有的資料庫</span><span class="sxs-lookup"><span data-stu-id="7e37f-102">Code First to an Existing Database</span></span>
<span data-ttu-id="7e37f-103">本影片以及逐步說明的逐步解說提供以現有的資料庫為目標的 Code First 開發的簡介。</span><span class="sxs-lookup"><span data-stu-id="7e37f-103">This video and step-by-step walkthrough provide an introduction to Code First development targeting an existing database.</span></span> <span data-ttu-id="7e37f-104">程式碼第一次可讓您定義您的模型使用 C\#或 VB.Net 類別。</span><span class="sxs-lookup"><span data-stu-id="7e37f-104">Code First allows you to define your model using C\# or VB.Net classes.</span></span> <span data-ttu-id="7e37f-105">（選擇性） 其他設定可以使用屬性，在您的類別和屬性，或使用 fluent API 來執行。</span><span class="sxs-lookup"><span data-stu-id="7e37f-105">Optionally additional configuration can be performed using attributes on your classes and properties or by using a fluent API.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="7e37f-106">觀看影片</span><span class="sxs-lookup"><span data-stu-id="7e37f-106">Watch the video</span></span>
<span data-ttu-id="7e37f-107">這段影片[現已在 Channel 9 上](http://channel9.msdn.com/blogs/ef/code-first-to-existing-database-ef6-1-onwards-)。</span><span class="sxs-lookup"><span data-stu-id="7e37f-107">This video is [now available on Channel 9](http://channel9.msdn.com/blogs/ef/code-first-to-existing-database-ef6-1-onwards-).</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="7e37f-108">必要條件</span><span class="sxs-lookup"><span data-stu-id="7e37f-108">Pre-Requisites</span></span>

<span data-ttu-id="7e37f-109">您必須擁有**Visual Studio 2012**或是**Visual Studio 2013**安裝才能完成此逐步解說。</span><span class="sxs-lookup"><span data-stu-id="7e37f-109">You will need to have **Visual Studio 2012** or **Visual Studio 2013** installed to complete this walkthrough.</span></span>

<span data-ttu-id="7e37f-110">您也需要新版**6.1** （或更新版本） 的**Entity Framework Tools for Visual Studio**安裝。</span><span class="sxs-lookup"><span data-stu-id="7e37f-110">You will also need version **6.1** (or later) of the **Entity Framework Tools for Visual Studio** installed.</span></span> <span data-ttu-id="7e37f-111">請參閱[取得 Entity Framework](~/ef6/fundamentals/install.md)如需安裝最新版的 Entity Framework 工具的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="7e37f-111">See [Get Entity Framework](~/ef6/fundamentals/install.md) for information on installing the latest version of the Entity Framework Tools.</span></span>

## <a name="1-create-an-existing-database"></a><span data-ttu-id="7e37f-112">1.建立現有的資料庫</span><span class="sxs-lookup"><span data-stu-id="7e37f-112">1. Create an Existing Database</span></span>

<span data-ttu-id="7e37f-113">通常當您的目標將已建立，現有的資料庫，但在此逐步解說，我們需要建立可存取的資料庫。</span><span class="sxs-lookup"><span data-stu-id="7e37f-113">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="7e37f-114">接下來產生的資料庫。</span><span class="sxs-lookup"><span data-stu-id="7e37f-114">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="7e37f-115">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e37f-115">Open Visual Studio</span></span>
-   <span data-ttu-id="7e37f-116">**檢視-&gt;伺服器總管**</span><span class="sxs-lookup"><span data-stu-id="7e37f-116">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="7e37f-117">以滑鼠右鍵按一下**資料連線-&gt;新增連接...**</span><span class="sxs-lookup"><span data-stu-id="7e37f-117">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="7e37f-118">如果您尚未連線至資料庫，以從**伺服器總管**您必須選取才能**Microsoft SQL Server**做為資料來源</span><span class="sxs-lookup"><span data-stu-id="7e37f-118">If you haven’t connected to a database from **Server Explorer** before you’ll need to select **Microsoft SQL Server** as the data source</span></span>

    ![SelectDataSource](~/ef6/media/selectdatasource.png)

-   <span data-ttu-id="7e37f-120">連線到您的 LocalDB 執行個體，然後輸入**部落格**做為資料庫名稱</span><span class="sxs-lookup"><span data-stu-id="7e37f-120">Connect to your LocalDB instance, and enter **Blogging** as the database name</span></span>

    ![LocalDBConnection](~/ef6/media/localdbconnection.png)

-   <span data-ttu-id="7e37f-122">選取  **確定**而且會要求您想要建立新資料庫，請選取**是**</span><span class="sxs-lookup"><span data-stu-id="7e37f-122">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![CreateDatabaseDialog](~/ef6/media/createdatabasedialog.png)

-   <span data-ttu-id="7e37f-124">新資料庫現在會出現在 [伺服器總管] 中，按一下滑鼠右鍵，然後選取**新查詢**</span><span class="sxs-lookup"><span data-stu-id="7e37f-124">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="7e37f-125">將下列 SQL 複製到新的查詢，然後以滑鼠右鍵按一下查詢並選取**Execute**</span><span class="sxs-lookup"><span data-stu-id="7e37f-125">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

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

## <a name="2-create-the-application"></a><span data-ttu-id="7e37f-126">2.建立應用程式</span><span class="sxs-lookup"><span data-stu-id="7e37f-126">2. Create the Application</span></span>

<span data-ttu-id="7e37f-127">為了簡單起見，我們要建置基本的主控台應用程式，會使用 Code First 來執行資料存取：</span><span class="sxs-lookup"><span data-stu-id="7e37f-127">To keep things simple we’re going to build a basic console application that uses Code First to perform data access:</span></span>

-   <span data-ttu-id="7e37f-128">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e37f-128">Open Visual Studio</span></span>
-   <span data-ttu-id="7e37f-129">**檔案-&gt;新增-&gt;專案...**</span><span class="sxs-lookup"><span data-stu-id="7e37f-129">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="7e37f-130">選取  **Windows**從左側的功能表和**主控台應用程式**</span><span class="sxs-lookup"><span data-stu-id="7e37f-130">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="7e37f-131">請輸入**CodeFirstExistingDatabaseSample**做為名稱</span><span class="sxs-lookup"><span data-stu-id="7e37f-131">Enter **CodeFirstExistingDatabaseSample** as the name</span></span>
-   <span data-ttu-id="7e37f-132">選取 [確定]</span><span class="sxs-lookup"><span data-stu-id="7e37f-132">Select **OK**</span></span>

 

## <a name="3-reverse-engineer-model"></a><span data-ttu-id="7e37f-133">3.反向工程模型</span><span class="sxs-lookup"><span data-stu-id="7e37f-133">3. Reverse Engineer Model</span></span>

<span data-ttu-id="7e37f-134">我們將利用 Entity Framework Tools for Visual Studio 可協助我們產生一些初始的程式碼，以對應至資料庫。</span><span class="sxs-lookup"><span data-stu-id="7e37f-134">We’re going to make use of the Entity Framework Tools for Visual Studio to help us generate some initial code to map to the database.</span></span> <span data-ttu-id="7e37f-135">這些工具只會產生您也可以輸入以手動方式視您的程式碼。</span><span class="sxs-lookup"><span data-stu-id="7e37f-135">These tools are just generating code that you could also type by hand if you prefer.</span></span>

-   <span data-ttu-id="7e37f-136">**專案-&gt;加入新項目...**</span><span class="sxs-lookup"><span data-stu-id="7e37f-136">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="7e37f-137">選取 **資料**從左側功能表，然後**ADO.NET 實體資料模型**</span><span class="sxs-lookup"><span data-stu-id="7e37f-137">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="7e37f-138">請輸入**為 [bloggingcontext]** 作為名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="7e37f-138">Enter **BloggingContext** as the name and click **OK**</span></span>
-   <span data-ttu-id="7e37f-139">這會啟動**Entity Data Model 精靈**</span><span class="sxs-lookup"><span data-stu-id="7e37f-139">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="7e37f-140">選取  **Code First 從資料庫**，按一下 **下一步**</span><span class="sxs-lookup"><span data-stu-id="7e37f-140">Select **Code First from Database** and click **Next**</span></span>

    ![WizardOneCFE](~/ef6/media/wizardonecfe.png)

-   <span data-ttu-id="7e37f-142">選取您在第一節中建立資料庫的連接，然後按一下**下一步**</span><span class="sxs-lookup"><span data-stu-id="7e37f-142">Select the connection to the database you created in the first section and click **Next**</span></span>

    ![WizardTwoCFE](~/ef6/media/wizardtwocfe.png)

-   <span data-ttu-id="7e37f-144">按一下核取方塊旁**資料表**匯入的所有資料表，並按一下**完成**</span><span class="sxs-lookup"><span data-stu-id="7e37f-144">Click the checkbox next to **Tables** to import all tables and click **Finish**</span></span>

    ![WizardThreeCFE](~/ef6/media/wizardthreecfe.png)

<span data-ttu-id="7e37f-146">反向工程程序完成的項目數目將已加入至專案，讓我們看一下什麼已加入。</span><span class="sxs-lookup"><span data-stu-id="7e37f-146">Once the reverse engineer process completes a number of items will have been added to the project, let's take a look at what's been added.</span></span>

### <a name="configuration-file"></a><span data-ttu-id="7e37f-147">組態檔</span><span class="sxs-lookup"><span data-stu-id="7e37f-147">Configuration file</span></span>

<span data-ttu-id="7e37f-148">App.config 檔案已新增至專案，此檔案包含現有資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="7e37f-148">An App.config file has been added to the project, this file contains the connection string to the existing database.</span></span>

``` xml
<connectionStrings>
  <add  
    name="BloggingContext"  
    connectionString="data source=(localdb)\mssqllocaldb;initial catalog=Blogging;integrated security=True;MultipleActiveResultSets=True;App=EntityFramework"  
    providerName="System.Data.SqlClient" />
</connectionStrings>
```

<span data-ttu-id="7e37f-149">*您也會發現其他某些設定組態檔中的，這些是預設 EF 設定會告訴程式碼第一次建立資料庫的位置。因為我們在我們的應用程式中對應至現有的資料庫將會忽略這些設定。*</span><span class="sxs-lookup"><span data-stu-id="7e37f-149">*You’ll notice some other settings in the configuration file too, these are default EF settings that tell Code First where to create databases. Since we are mapping to an existing database these setting will be ignored in our application.*</span></span>

### <a name="derived-context"></a><span data-ttu-id="7e37f-150">在衍生的內容</span><span class="sxs-lookup"><span data-stu-id="7e37f-150">Derived Context</span></span>

<span data-ttu-id="7e37f-151">A**為 [bloggingcontext]** 類別已新增至專案。</span><span class="sxs-lookup"><span data-stu-id="7e37f-151">A **BloggingContext** class has been added to the project.</span></span> <span data-ttu-id="7e37f-152">內容代表資料庫中，讓我們可以查詢和儲存資料的工作階段。</span><span class="sxs-lookup"><span data-stu-id="7e37f-152">The context represents a session with the database, allowing us to query and save data.</span></span>
<span data-ttu-id="7e37f-153">內容會公開**DbSet&lt;TEntity&gt;** 我們的模型中每個類型。</span><span class="sxs-lookup"><span data-stu-id="7e37f-153">The context exposes a **DbSet&lt;TEntity&gt;** for each type in our model.</span></span> <span data-ttu-id="7e37f-154">您也會注意到預設的建構函式呼叫基底建構函式，使用**名稱 =** 語法。</span><span class="sxs-lookup"><span data-stu-id="7e37f-154">You’ll also notice that the default constructor calls a base constructor using the **name=** syntax.</span></span> <span data-ttu-id="7e37f-155">這會告訴程式碼第一個要用於此內容的連接字串應該從組態檔中載入。</span><span class="sxs-lookup"><span data-stu-id="7e37f-155">This tells Code First that the connection string to use for this context should be loaded from the configuration file.</span></span>

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

<span data-ttu-id="7e37f-156">*您應該一律使用**名稱 =** 當您在組態檔中使用的連接字串的語法。這可確保，如果連接字串不存在則 Entity Framework 將會擲回而非依照慣例建立新的資料庫。*</span><span class="sxs-lookup"><span data-stu-id="7e37f-156">*You should always use the **name=** syntax when you are using a connection string in the config file. This ensures that if the connection string is not present then Entity Framework will throw rather than creating a new database by convention.*</span></span>

### <a name="model-classes"></a><span data-ttu-id="7e37f-157">模型類別</span><span class="sxs-lookup"><span data-stu-id="7e37f-157">Model classes</span></span>

<span data-ttu-id="7e37f-158">最後，**部落格**並**Post**類別也已新增至專案。</span><span class="sxs-lookup"><span data-stu-id="7e37f-158">Finally, a **Blog** and **Post** class have also been added to the project.</span></span> <span data-ttu-id="7e37f-159">這些是我們的模型所組成的網域類別。</span><span class="sxs-lookup"><span data-stu-id="7e37f-159">These are the domain classes that make up our model.</span></span> <span data-ttu-id="7e37f-160">您會看到資料註釋套用至類別，來指定組態，Code First 慣例會不符合現有資料庫的結構。</span><span class="sxs-lookup"><span data-stu-id="7e37f-160">You'll see Data Annotations applied to the classes to specify configuration where the Code First conventions would not align with the structure of the existing database.</span></span> <span data-ttu-id="7e37f-161">例如，您會看到**StringLength**上的註釋**Blog.Name**並**Blog.Url**因為它們有最大長度為**200**中資料庫 (第一個程式碼的預設值是使用資料庫提供者-支援的上限長度**nvarchar （max)** SQL Server 中)。</span><span class="sxs-lookup"><span data-stu-id="7e37f-161">For example, you'll see the **StringLength** annotation on **Blog.Name** and **Blog.Url** since they have a maximum length of **200** in the database (the Code First default is to use the maximun length supported by the database provider - **nvarchar(max)** in SQL Server).</span></span>

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

## <a name="4-reading--writing-data"></a><span data-ttu-id="7e37f-162">4.讀取和寫入資料</span><span class="sxs-lookup"><span data-stu-id="7e37f-162">4. Reading & Writing Data</span></span>

<span data-ttu-id="7e37f-163">現在，我們就可以開始使用它來存取部分資料的模型。</span><span class="sxs-lookup"><span data-stu-id="7e37f-163">Now that we have a model it’s time to use it to access some data.</span></span> <span data-ttu-id="7e37f-164">實作**Main**方法中的**Program.cs** ，如下所示。</span><span class="sxs-lookup"><span data-stu-id="7e37f-164">Implement the **Main** method in **Program.cs** as shown below.</span></span> <span data-ttu-id="7e37f-165">這段程式碼會建立我們的內容的新執行個體，然後用它來插入新**部落格**。</span><span class="sxs-lookup"><span data-stu-id="7e37f-165">This code creates a new instance of our context and then uses it to insert a new **Blog**.</span></span> <span data-ttu-id="7e37f-166">然後，使用 LINQ 查詢來擷取所有**部落格**從資料庫依字母順序排序**標題**。</span><span class="sxs-lookup"><span data-stu-id="7e37f-166">Then it uses a LINQ query to retrieve all **Blogs** from the database ordered alphabetically by **Title**.</span></span>

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

<span data-ttu-id="7e37f-167">您現在可以執行應用程式，並加以測試。</span><span class="sxs-lookup"><span data-stu-id="7e37f-167">You can now run the application and test it out.</span></span>

```
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
.NET Framework Blog
ADO.NET Blog
The Visual Studio Blog
Press any key to exit...
```
 
## <a name="what-if-my-database-changes"></a><span data-ttu-id="7e37f-168">如果我的資料庫變更嗎？</span><span class="sxs-lookup"><span data-stu-id="7e37f-168">What if My Database Changes?</span></span>

<span data-ttu-id="7e37f-169">Code First 資料庫精靈 被設計來產生起始點的一組類別，您可以稍後調整和修改。</span><span class="sxs-lookup"><span data-stu-id="7e37f-169">The Code First to Database wizard is designed to generate a starting point set of classes that you can then tweak and modify.</span></span> <span data-ttu-id="7e37f-170">如果您的資料庫結構描述變更您可以手動編輯類別或執行另一個反向工程，以覆寫的類別。</span><span class="sxs-lookup"><span data-stu-id="7e37f-170">If your database schema changes you can either manually edit the classes or perform another reverse engineer to overwrite the classes.</span></span>

## <a name="using-code-first-migrations-to-an-existing-database"></a><span data-ttu-id="7e37f-171">使用 Code First 移轉到現有的資料庫</span><span class="sxs-lookup"><span data-stu-id="7e37f-171">Using Code First Migrations to an Existing Database</span></span>

<span data-ttu-id="7e37f-172">如果您想要使用現有的資料庫中的 Code First 移轉，請參閱[Code First 移轉到現有的資料庫](~/ef6/modeling/code-first/migrations/existing-database.md)。</span><span class="sxs-lookup"><span data-stu-id="7e37f-172">If you want to use Code First Migrations with an existing database, see [Code First Migrations to an existing database](~/ef6/modeling/code-first/migrations/existing-database.md).</span></span>

## <a name="summary"></a><span data-ttu-id="7e37f-173">總結</span><span class="sxs-lookup"><span data-stu-id="7e37f-173">Summary</span></span>

<span data-ttu-id="7e37f-174">在此逐步解說中，我們討論過使用現有資料庫的 Code First 開發。</span><span class="sxs-lookup"><span data-stu-id="7e37f-174">In this walkthrough we looked at Code First development using an existing database.</span></span> <span data-ttu-id="7e37f-175">我們會使用 Entity Framework Tools for Visual Studio 進行反向工程的一組對應到資料庫，而且可用來儲存和擷取資料的類別。</span><span class="sxs-lookup"><span data-stu-id="7e37f-175">We used the Entity Framework Tools for Visual Studio to reverse engineer a set of classes that mapped to the database and could be used to store and retrieve data.</span></span>
