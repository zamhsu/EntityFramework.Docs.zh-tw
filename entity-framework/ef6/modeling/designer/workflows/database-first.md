---
title: Database First-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: cc6ffdb3-388d-4e79-a201-01ec2577c949
ms.openlocfilehash: c81025fe7c3ad6398f003f7be2a3f9f072eec327
ms.sourcegitcommit: 269c8a1a457a9ad27b4026c22c4b1a76991fb360
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2018
ms.locfileid: "46284079"
---
# <a name="database-first"></a><span data-ttu-id="f2c93-102">第一次資料庫</span><span class="sxs-lookup"><span data-stu-id="f2c93-102">Database First</span></span>
<span data-ttu-id="f2c93-103">本影片以及逐步說明的逐步解說提供使用 Entity Framework 資料庫優先開發的簡介。</span><span class="sxs-lookup"><span data-stu-id="f2c93-103">This video and step-by-step walkthrough provide an introduction to Database First development using Entity Framework.</span></span> <span data-ttu-id="f2c93-104">資料庫第一次可讓您進行反向工程，從現有的資料庫模型。</span><span class="sxs-lookup"><span data-stu-id="f2c93-104">Database First allows you to reverse engineer a model from an existing database.</span></span> <span data-ttu-id="f2c93-105">模型會儲存在 EDMX 檔案 （副檔名為.edmx），及可以檢視和編輯 Entity Framework 設計工具中。</span><span class="sxs-lookup"><span data-stu-id="f2c93-105">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="f2c93-106">從 EDMX 檔案，會自動產生應用程式中與您互動的類別。</span><span class="sxs-lookup"><span data-stu-id="f2c93-106">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="f2c93-107">觀看影片</span><span class="sxs-lookup"><span data-stu-id="f2c93-107">Watch the video</span></span>
<span data-ttu-id="f2c93-108">這段影片中提供使用 Entity Framework 資料庫優先開發的簡介。</span><span class="sxs-lookup"><span data-stu-id="f2c93-108">This video provides an introduction to Database First development using Entity Framework.</span></span> <span data-ttu-id="f2c93-109">資料庫第一次可讓您進行反向工程，從現有的資料庫模型。</span><span class="sxs-lookup"><span data-stu-id="f2c93-109">Database First allows you to reverse engineer a model from an existing database.</span></span> <span data-ttu-id="f2c93-110">模型會儲存在 EDMX 檔案 （副檔名為.edmx），及可以檢視和編輯 Entity Framework 設計工具中。</span><span class="sxs-lookup"><span data-stu-id="f2c93-110">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="f2c93-111">從 EDMX 檔案，會自動產生應用程式中與您互動的類別。</span><span class="sxs-lookup"><span data-stu-id="f2c93-111">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

<span data-ttu-id="f2c93-112">**主講人**[Rowan Miller](http://romiller.com/)</span><span class="sxs-lookup"><span data-stu-id="f2c93-112">**Presented By**: [Rowan Miller](http://romiller.com/)</span></span>

<span data-ttu-id="f2c93-113">**視訊**: [WMV](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.wmv) | [MP4](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-mp4video-databasefirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.zip)</span><span class="sxs-lookup"><span data-stu-id="f2c93-113">**Video**: [WMV](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.wmv) | [MP4](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-mp4video-databasefirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="f2c93-114">必要條件</span><span class="sxs-lookup"><span data-stu-id="f2c93-114">Pre-Requisites</span></span>

<span data-ttu-id="f2c93-115">您必須至少是 Visual studio 2010 或 Visual Studio 2012 安裝完成本逐步解說。</span><span class="sxs-lookup"><span data-stu-id="f2c93-115">You will need to have at least Visual Studio 2010 or Visual Studio 2012 installed to complete this walkthrough.</span></span>

<span data-ttu-id="f2c93-116">如果您使用 Visual Studio 2010，您也必須有[NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)安裝。</span><span class="sxs-lookup"><span data-stu-id="f2c93-116">If you are using Visual Studio 2010, you will also need to have [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installed.</span></span>

 

## <a name="1-create-an-existing-database"></a><span data-ttu-id="f2c93-117">1.建立現有的資料庫</span><span class="sxs-lookup"><span data-stu-id="f2c93-117">1. Create an Existing Database</span></span>

<span data-ttu-id="f2c93-118">通常當您的目標將已建立，現有的資料庫，但在此逐步解說，我們需要建立可存取的資料庫。</span><span class="sxs-lookup"><span data-stu-id="f2c93-118">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="f2c93-119">Visual Studio 隨附安裝的資料庫伺服器是您已安裝的 Visual Studio 版本而有所不同：</span><span class="sxs-lookup"><span data-stu-id="f2c93-119">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="f2c93-120">如果您使用 Visual Studio 2010 將會建立 SQL Express 資料庫。</span><span class="sxs-lookup"><span data-stu-id="f2c93-120">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="f2c93-121">如果您使用 Visual Studio 2012，則您將會建立[LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx)資料庫。</span><span class="sxs-lookup"><span data-stu-id="f2c93-121">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) database.</span></span>

 

<span data-ttu-id="f2c93-122">接下來產生的資料庫。</span><span class="sxs-lookup"><span data-stu-id="f2c93-122">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="f2c93-123">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2c93-123">Open Visual Studio</span></span>
-   <span data-ttu-id="f2c93-124">**檢視-&gt;伺服器總管**</span><span class="sxs-lookup"><span data-stu-id="f2c93-124">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="f2c93-125">以滑鼠右鍵按一下**資料連線-&gt;新增連接...**</span><span class="sxs-lookup"><span data-stu-id="f2c93-125">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="f2c93-126">如果您尚未連線至資料庫從伺服器總管之前您必須選取 Microsoft SQL Server 做為資料來源</span><span class="sxs-lookup"><span data-stu-id="f2c93-126">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![選取資料來源](~/ef6/media/selectdatasource.png)

-   <span data-ttu-id="f2c93-128">連接到 LocalDB 或 SQL Express，何者而定，您已安裝，然後輸入**DatabaseFirst.Blogging**做為資料庫名稱</span><span class="sxs-lookup"><span data-stu-id="f2c93-128">Connect to either LocalDB or SQL Express, depending on which one you have installed, and enter **DatabaseFirst.Blogging** as the database name</span></span>

    ![Sql Express 連接 DF](~/ef6/media/sqlexpressconnectiondf.png)

    ![LocalDB 連接 DF](~/ef6/media/localdbconnectiondf.png)

-   <span data-ttu-id="f2c93-131">選取  **確定**而且會要求您想要建立新資料庫，請選取**是**</span><span class="sxs-lookup"><span data-stu-id="f2c93-131">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![建立資料庫 對話方塊](~/ef6/media/createdatabasedialog.png)

-   <span data-ttu-id="f2c93-133">新資料庫現在會出現在 [伺服器總管] 中，按一下滑鼠右鍵，然後選取**新查詢**</span><span class="sxs-lookup"><span data-stu-id="f2c93-133">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="f2c93-134">將下列 SQL 複製到新的查詢，然後以滑鼠右鍵按一下查詢並選取**Execute**</span><span class="sxs-lookup"><span data-stu-id="f2c93-134">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

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
```

## <a name="2-create-the-application"></a><span data-ttu-id="f2c93-135">2.建立應用程式</span><span class="sxs-lookup"><span data-stu-id="f2c93-135">2. Create the Application</span></span>

<span data-ttu-id="f2c93-136">為了簡單起見，我們要建置基本的主控台應用程式，會使用 Database First 來執行資料存取：</span><span class="sxs-lookup"><span data-stu-id="f2c93-136">To keep things simple we’re going to build a basic console application that uses the Database First to perform data access:</span></span>

-   <span data-ttu-id="f2c93-137">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2c93-137">Open Visual Studio</span></span>
-   <span data-ttu-id="f2c93-138">**檔案-&gt;新增-&gt;專案...**</span><span class="sxs-lookup"><span data-stu-id="f2c93-138">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="f2c93-139">選取  **Windows**從左側的功能表和**主控台應用程式**</span><span class="sxs-lookup"><span data-stu-id="f2c93-139">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="f2c93-140">請輸入**DatabaseFirstSample**做為名稱</span><span class="sxs-lookup"><span data-stu-id="f2c93-140">Enter **DatabaseFirstSample** as the name</span></span>
-   <span data-ttu-id="f2c93-141">選取 [確定]</span><span class="sxs-lookup"><span data-stu-id="f2c93-141">Select **OK**</span></span>

 

## <a name="3-reverse-engineer-model"></a><span data-ttu-id="f2c93-142">3.反向工程模型</span><span class="sxs-lookup"><span data-stu-id="f2c93-142">3. Reverse Engineer Model</span></span>

<span data-ttu-id="f2c93-143">我們將利用 Entity Framework Designer 中，也就是 Visual Studio 的一部分，以建立我們的模型。</span><span class="sxs-lookup"><span data-stu-id="f2c93-143">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="f2c93-144">**專案-&gt;加入新項目...**</span><span class="sxs-lookup"><span data-stu-id="f2c93-144">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="f2c93-145">選取 **資料**從左側功能表，然後**ADO.NET 實體資料模型**</span><span class="sxs-lookup"><span data-stu-id="f2c93-145">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="f2c93-146">請輸入**BloggingModel**作為名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="f2c93-146">Enter **BloggingModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="f2c93-147">這會啟動**Entity Data Model 精靈**</span><span class="sxs-lookup"><span data-stu-id="f2c93-147">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="f2c93-148">選取 **從資料庫產生**，按一下 **下一步**</span><span class="sxs-lookup"><span data-stu-id="f2c93-148">Select **Generate from Database** and click **Next**</span></span>

    ![精靈步驟 1](~/ef6/media/wizardstep1.png)

-   <span data-ttu-id="f2c93-150">選取您建立第一個區段中的資料庫連接中，輸入**為 [bloggingcontext]** 做為連接字串，然後按一下 [名稱**下一步]**</span><span class="sxs-lookup"><span data-stu-id="f2c93-150">Select the connection to the database you created in the first section, enter **BloggingContext** as the name of the connection string and click **Next**</span></span>

    ![精靈步驟 2](~/ef6/media/wizardstep2.png)

-   <span data-ttu-id="f2c93-152">按一下 匯入的所有資料表，並按一下 完成 '資料表旁的核取方塊</span><span class="sxs-lookup"><span data-stu-id="f2c93-152">Click the checkbox next to ‘Tables’ to import all tables and click ‘Finish’</span></span>

    ![Krok 3](~/ef6/media/wizardstep3.png)

 

<span data-ttu-id="f2c93-154">反向工程程序完成後新模型加入您的專案，並讓您檢視在 Entity Framework Designer 中開啟。</span><span class="sxs-lookup"><span data-stu-id="f2c93-154">Once the reverse engineer process completes the new model is added to your project and opened up for you to view in the Entity Framework Designer.</span></span> <span data-ttu-id="f2c93-155">App.config 檔案也已新增至您的專案與資料庫的連線詳細資料。</span><span class="sxs-lookup"><span data-stu-id="f2c93-155">An App.config file has also been added to your project with the connection details for the database.</span></span>

![建立初始的模型](~/ef6/media/modelinitial.png)

### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="f2c93-157">Visual Studio 2010 中的其他步驟</span><span class="sxs-lookup"><span data-stu-id="f2c93-157">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="f2c93-158">如果您正在 Visual Studio 2010 中有您要升級至最新版的 Entity Framework 遵循一些額外的步驟。</span><span class="sxs-lookup"><span data-stu-id="f2c93-158">If you are working in Visual Studio 2010 there are some additional steps you need to follow to upgrade to the latest version of Entity Framework.</span></span> <span data-ttu-id="f2c93-159">升級很重要，因為它可讓您存取改良的 API 介面，也就是容易使用，以及最新的 bug 修正。</span><span class="sxs-lookup"><span data-stu-id="f2c93-159">Upgrading is important because it gives you access to an improved API surface, that is much easier to use, as well as the latest bug fixes.</span></span>

<span data-ttu-id="f2c93-160">首先，我們要從 NuGet 取得最新版的 Entity Framework。</span><span class="sxs-lookup"><span data-stu-id="f2c93-160">First up, we need to get the latest version of Entity Framework from NuGet.</span></span>

-   <span data-ttu-id="f2c93-161">**專案 –&gt;管理 NuGet 封裝...** 
    *如果您沒有\*\*管理 NuGet 套件...*\* 選項，您應該安裝[最新版的 NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)\*</span><span class="sxs-lookup"><span data-stu-id="f2c93-161">**Project –&gt; Manage NuGet Packages…**
*If you don’t have the **Manage NuGet Packages…** option you should install the [latest version of NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)*</span></span>
-   <span data-ttu-id="f2c93-162">選取 [ **Online** ] 索引標籤</span><span class="sxs-lookup"><span data-stu-id="f2c93-162">Select the **Online** tab</span></span>
-   <span data-ttu-id="f2c93-163">選取  **EntityFramework**封裝</span><span class="sxs-lookup"><span data-stu-id="f2c93-163">Select the **EntityFramework** package</span></span>
-   <span data-ttu-id="f2c93-164">按一下 **安裝**</span><span class="sxs-lookup"><span data-stu-id="f2c93-164">Click **Install**</span></span>

<span data-ttu-id="f2c93-165">接下來，我們需要交換我們的模型來產生程式碼會使用所導入新版的 Entity Framework DbContext API。</span><span class="sxs-lookup"><span data-stu-id="f2c93-165">Next, we need to swap our model to generate code that makes use of the DbContext API, which was introduced in later versions of Entity Framework.</span></span>

-   <span data-ttu-id="f2c93-166">以滑鼠右鍵按一下您的模型，在 EF 設計工具中的非空點，然後選取**加入的程式碼產生項目...**</span><span class="sxs-lookup"><span data-stu-id="f2c93-166">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="f2c93-167">選取 **線上範本**從左側的功能表，並搜尋**DbContext**</span><span class="sxs-lookup"><span data-stu-id="f2c93-167">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="f2c93-168">選取 EF **5.x 適用於 C 的 DbContext Generator\#**，輸入**BloggingModel**做為名稱，然後按一下**新增**</span><span class="sxs-lookup"><span data-stu-id="f2c93-168">Select the EF **5.x DbContext Generator for C\#**, enter **BloggingModel** as the name and click **Add**</span></span>

    ![DbContext 範本](~/ef6/media/dbcontexttemplate.png)

 

## <a name="4-reading--writing-data"></a><span data-ttu-id="f2c93-170">4.讀取和寫入資料</span><span class="sxs-lookup"><span data-stu-id="f2c93-170">4. Reading & Writing Data</span></span>

<span data-ttu-id="f2c93-171">現在，我們就可以開始使用它來存取部分資料的模型。</span><span class="sxs-lookup"><span data-stu-id="f2c93-171">Now that we have a model it’s time to use it to access some data.</span></span> <span data-ttu-id="f2c93-172">我們將類別，用以存取資料自動產生根據 EDMX 檔。</span><span class="sxs-lookup"><span data-stu-id="f2c93-172">The classes we are going to use to access data are being automatically generated for you based on the EDMX file.</span></span>

<span data-ttu-id="f2c93-173">*這個螢幕擷取畫面是來自 Visual Studio 2012 中，如果您使用 Visual Studio 2010 BloggingModel.tt 且 BloggingModel.Context.tt 檔案將會直接在您的專案之下，而非巢狀於 EDMX 檔之下。*</span><span class="sxs-lookup"><span data-stu-id="f2c93-173">*This screen shot is from Visual Studio 2012, if you are using Visual Studio 2010 the BloggingModel.tt and BloggingModel.Context.tt files will be directly under your project rather than nested under the EDMX file.*</span></span>

![產生的類別 DF](~/ef6/media/generatedclassesdf.png)

 

<span data-ttu-id="f2c93-175">在 Program.cs 中實作的 Main 方法，如下所示。</span><span class="sxs-lookup"><span data-stu-id="f2c93-175">Implement the Main method in Program.cs as shown below.</span></span> <span data-ttu-id="f2c93-176">此程式碼會建立我們的內容的新執行個體，然後用它來插入新的部落格</span><span class="sxs-lookup"><span data-stu-id="f2c93-176">This code creates a new instance of our context and then uses it to insert a new Blog.</span></span> <span data-ttu-id="f2c93-177">然後它會使用 LINQ 查詢來擷取依字母順序排序項目所使用的資料庫中的所有部落格。</span><span class="sxs-lookup"><span data-stu-id="f2c93-177">Then it uses a LINQ query to retrieve all Blogs from the database ordered alphabetically by Title.</span></span>

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

<span data-ttu-id="f2c93-178">您現在可以執行應用程式，並加以測試。</span><span class="sxs-lookup"><span data-stu-id="f2c93-178">You can now run the application and test it out.</span></span>

```
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
 

## <a name="5-dealing-with-database-changes"></a><span data-ttu-id="f2c93-179">5.因應資料庫變更</span><span class="sxs-lookup"><span data-stu-id="f2c93-179">5. Dealing with Database Changes</span></span>

<span data-ttu-id="f2c93-180">現在就可以開始對資料庫結構描述，進行一些變更，當我們做出這些變更，我們也需要更新我們的模型，以反映這些變更。</span><span class="sxs-lookup"><span data-stu-id="f2c93-180">Now it’s time to make some changes to our database schema, when we make these changes we also need to update our model to reflect those changes.</span></span>

<span data-ttu-id="f2c93-181">第一個步驟是對資料庫結構描述進行一些變更。</span><span class="sxs-lookup"><span data-stu-id="f2c93-181">The first step is to make some changes to the database schema.</span></span> <span data-ttu-id="f2c93-182">我們要加入使用者資料表的結構描述。</span><span class="sxs-lookup"><span data-stu-id="f2c93-182">We’re going to add a Users table to the schema.</span></span>

-   <span data-ttu-id="f2c93-183">以滑鼠右鍵按一下**DatabaseFirst.Blogging**伺服器總管 中的資料庫，並選取**新查詢**</span><span class="sxs-lookup"><span data-stu-id="f2c93-183">Right-click on the **DatabaseFirst.Blogging** database in Server Explorer and select **New Query**</span></span>
-   <span data-ttu-id="f2c93-184">將下列 SQL 複製到新的查詢，然後以滑鼠右鍵按一下查詢並選取**Execute**</span><span class="sxs-lookup"><span data-stu-id="f2c93-184">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

``` SQL
CREATE TABLE [dbo].[Users]
(
    [Username] NVARCHAR(50) NOT NULL PRIMARY KEY,  
    [DisplayName] NVARCHAR(MAX) NULL
)
```

<span data-ttu-id="f2c93-185">現在，更新結構描述時，就可以使用這些變更來更新模型。</span><span class="sxs-lookup"><span data-stu-id="f2c93-185">Now that the schema is updated, it’s time to update the model with those changes.</span></span>

-   <span data-ttu-id="f2c93-186">以滑鼠右鍵按一下空白的模型中的 EF 設計工具和選取 ' 從資料庫更新模型...' 的位置，這會啟動 更新精靈</span><span class="sxs-lookup"><span data-stu-id="f2c93-186">Right-click on an empty spot of your model in the EF Designer and select ‘Update Model from Database…’, this will launch the Update Wizard</span></span>
-   <span data-ttu-id="f2c93-187">在 [更新精靈] 核取方塊中，資料表旁的 [新增] 索引標籤中，這表示我們想要從結構描述中加入任何新的資料表。</span><span class="sxs-lookup"><span data-stu-id="f2c93-187">On the Add tab of the Update Wizard check the box next to Tables, this indicates that we want to add any new tables from the schema.</span></span>
    <span data-ttu-id="f2c93-188">*[重新整理] 索引標籤會顯示任何現有的資料表，將會檢查變更期間更新模型中。刪除索引標籤會顯示已移除從結構描述，也會移除從模型更新一部分的任何資料表。這些兩個索引標籤上的資訊會自動偵測，並提供僅供參考之用，您無法變更任何設定。*</span><span class="sxs-lookup"><span data-stu-id="f2c93-188">*The Refresh tab shows any existing tables in the model that will be checked for changes during the update. The Delete tabs show any tables that have been removed from the schema and will also be removed from the model as part of the update. The information on these two tabs is automatically detected and is provided for informational purposes only, you cannot change any settings.*</span></span>

    ![重新整理精靈](~/ef6/media/refreshwizard.png)

-   <span data-ttu-id="f2c93-190">按一下 [完成]，在 [更新精靈]</span><span class="sxs-lookup"><span data-stu-id="f2c93-190">Click Finish on the Update Wizard</span></span>

 

<span data-ttu-id="f2c93-191">模型現在會更新以包含新的使用者實體會對應到使用者資料表，我們新增至資料庫。</span><span class="sxs-lookup"><span data-stu-id="f2c93-191">The model is now updated to include a new User entity that maps to the Users table we added to the database.</span></span>

![更新的模型](~/ef6/media/modelupdated.png)

## <a name="summary"></a><span data-ttu-id="f2c93-193">總結</span><span class="sxs-lookup"><span data-stu-id="f2c93-193">Summary</span></span>

<span data-ttu-id="f2c93-194">在本逐步解說，我們探討了第一個資料庫的開發，這可讓我們來根據現有資料庫的 EF 設計工具中建立模型。</span><span class="sxs-lookup"><span data-stu-id="f2c93-194">In this walkthrough we looked at Database First development, which allowed us to create a model in the EF Designer based on an existing database.</span></span> <span data-ttu-id="f2c93-195">然後，我們會使用該模型來讀取和寫入資料庫中的一些資料。</span><span class="sxs-lookup"><span data-stu-id="f2c93-195">We then used that model to read and write some data from the database.</span></span> <span data-ttu-id="f2c93-196">最後，我們已更新模型，以反映資料庫結構描述我們所做的變更。</span><span class="sxs-lookup"><span data-stu-id="f2c93-196">Finally, we updated the model to reflect changes we made to the database schema.</span></span>
