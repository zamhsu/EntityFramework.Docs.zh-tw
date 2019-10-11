---
title: Database First-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: cc6ffdb3-388d-4e79-a201-01ec2577c949
ms.openlocfilehash: d40cff4ddccf43a394ef4f244653372a5a89b05a
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182455"
---
# <a name="database-first"></a><span data-ttu-id="07980-102">Database First</span><span class="sxs-lookup"><span data-stu-id="07980-102">Database First</span></span>
<span data-ttu-id="07980-103">這段影片和逐步解說提供使用 Entity Framework Database First 開發的簡介。</span><span class="sxs-lookup"><span data-stu-id="07980-103">This video and step-by-step walkthrough provide an introduction to Database First development using Entity Framework.</span></span> <span data-ttu-id="07980-104">Database First 可讓您從現有的資料庫對模型進行反向工程。</span><span class="sxs-lookup"><span data-stu-id="07980-104">Database First allows you to reverse engineer a model from an existing database.</span></span> <span data-ttu-id="07980-105">此模型會儲存在 EDMX 檔案（.edmx 副檔名）中，而且可以在 Entity Framework Designer 中查看和編輯。</span><span class="sxs-lookup"><span data-stu-id="07980-105">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="07980-106">您在應用程式中與互動的類別會從 EDMX 檔案自動產生。</span><span class="sxs-lookup"><span data-stu-id="07980-106">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="07980-107">觀看影片</span><span class="sxs-lookup"><span data-stu-id="07980-107">Watch the video</span></span>
<span data-ttu-id="07980-108">這段影片提供使用 Entity Framework 進行 Database First 開發的介紹。</span><span class="sxs-lookup"><span data-stu-id="07980-108">This video provides an introduction to Database First development using Entity Framework.</span></span> <span data-ttu-id="07980-109">Database First 可讓您從現有的資料庫對模型進行反向工程。</span><span class="sxs-lookup"><span data-stu-id="07980-109">Database First allows you to reverse engineer a model from an existing database.</span></span> <span data-ttu-id="07980-110">此模型會儲存在 EDMX 檔案（.edmx 副檔名）中，而且可以在 Entity Framework Designer 中查看和編輯。</span><span class="sxs-lookup"><span data-stu-id="07980-110">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="07980-111">您在應用程式中與互動的類別會從 EDMX 檔案自動產生。</span><span class="sxs-lookup"><span data-stu-id="07980-111">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

<span data-ttu-id="07980-112">**提供者**：[Rowan 莎莎](https://romiller.com/)</span><span class="sxs-lookup"><span data-stu-id="07980-112">**Presented By**: [Rowan Miller](https://romiller.com/)</span></span>

<span data-ttu-id="07980-113">**影片**：[WMV](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.wmv)@NO__T[-1 個](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-mp4video-databasefirst.m4v) | [WMV （ZIP）](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.zip)</span><span class="sxs-lookup"><span data-stu-id="07980-113">**Video**: [WMV](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.wmv) | [MP4](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-mp4video-databasefirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="07980-114">先決條件</span><span class="sxs-lookup"><span data-stu-id="07980-114">Pre-Requisites</span></span>

<span data-ttu-id="07980-115">您至少必須安裝 Visual Studio 2010 或 Visual Studio 2012，才能完成此逐步解說。</span><span class="sxs-lookup"><span data-stu-id="07980-115">You will need to have at least Visual Studio 2010 or Visual Studio 2012 installed to complete this walkthrough.</span></span>

<span data-ttu-id="07980-116">如果您使用 Visual Studio 2010，您也必須安裝[NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) 。</span><span class="sxs-lookup"><span data-stu-id="07980-116">If you are using Visual Studio 2010, you will also need to have [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installed.</span></span>

 

## <a name="1-create-an-existing-database"></a><span data-ttu-id="07980-117">1.建立現有的資料庫</span><span class="sxs-lookup"><span data-stu-id="07980-117">1. Create an Existing Database</span></span>

<span data-ttu-id="07980-118">通常當您將目標設為現有的資料庫時，就會建立它，但在此逐步解說中，我們需要建立要存取的資料庫。</span><span class="sxs-lookup"><span data-stu-id="07980-118">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="07980-119">隨 Visual Studio 安裝的資料庫伺服器會根據您安裝的 Visual Studio 版本而有所不同：</span><span class="sxs-lookup"><span data-stu-id="07980-119">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="07980-120">如果您使用 Visual Studio 2010，您將會建立 SQL Express 資料庫。</span><span class="sxs-lookup"><span data-stu-id="07980-120">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="07980-121">如果您使用 Visual Studio 2012，則您將會建立[LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx)資料庫。</span><span class="sxs-lookup"><span data-stu-id="07980-121">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) database.</span></span>

 

<span data-ttu-id="07980-122">讓我們繼續產生資料庫。</span><span class="sxs-lookup"><span data-stu-id="07980-122">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="07980-123">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07980-123">Open Visual Studio</span></span>
-   <span data-ttu-id="07980-124">**View-&gt; 伺服器總管**</span><span class="sxs-lookup"><span data-stu-id="07980-124">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="07980-125">以滑鼠右鍵按一下 [**資料連線-&gt; 新增連接 ...** ]</span><span class="sxs-lookup"><span data-stu-id="07980-125">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="07980-126">如果您還沒有從伺服器總管連接到資料庫，則必須選取 [Microsoft SQL Server] 做為資料來源</span><span class="sxs-lookup"><span data-stu-id="07980-126">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![選取資料來源](~/ef6/media/selectdatasource.png)

-   <span data-ttu-id="07980-128">連接到 LocalDB 或 SQL Express （視您安裝的版本而定），然後輸入**DatabaseFirst**做為資料庫名稱</span><span class="sxs-lookup"><span data-stu-id="07980-128">Connect to either LocalDB or SQL Express, depending on which one you have installed, and enter **DatabaseFirst.Blogging** as the database name</span></span>

    ![Sql Express 連接 DF](~/ef6/media/sqlexpressconnectiondf.png)

    ![LocalDB 連接 DF](~/ef6/media/localdbconnectiondf.png)

-   <span data-ttu-id="07980-131">選取 **[確定]** ，系統會詢問您是否要建立新的資料庫，然後選取 **[是]**</span><span class="sxs-lookup"><span data-stu-id="07980-131">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![建立資料庫對話方塊](~/ef6/media/createdatabasedialog.png)

-   <span data-ttu-id="07980-133">新的資料庫現在會出現在伺服器總管中，以滑鼠右鍵按一下它，然後選取 [追加**查詢**]</span><span class="sxs-lookup"><span data-stu-id="07980-133">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="07980-134">將下列 SQL 複製到新的查詢中，然後以滑鼠右鍵按一下查詢並選取 [**執行**]。</span><span class="sxs-lookup"><span data-stu-id="07980-134">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

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

## <a name="2-create-the-application"></a><span data-ttu-id="07980-135">2.建立應用程式</span><span class="sxs-lookup"><span data-stu-id="07980-135">2. Create the Application</span></span>

<span data-ttu-id="07980-136">為了簡單起見，我們將建立一個使用 Database First 來執行資料存取的基本主控台應用程式：</span><span class="sxs-lookup"><span data-stu-id="07980-136">To keep things simple we’re going to build a basic console application that uses the Database First to perform data access:</span></span>

-   <span data-ttu-id="07980-137">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07980-137">Open Visual Studio</span></span>
-   <span data-ttu-id="07980-138">**檔案 &gt; 個新 &gt; 個專案 。**</span><span class="sxs-lookup"><span data-stu-id="07980-138">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="07980-139">從左側功能表和**主控台應用程式**中選取 [ **Windows** ]</span><span class="sxs-lookup"><span data-stu-id="07980-139">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="07980-140">輸入**DatabaseFirstSample**作為名稱</span><span class="sxs-lookup"><span data-stu-id="07980-140">Enter **DatabaseFirstSample** as the name</span></span>
-   <span data-ttu-id="07980-141">選取 [確定]</span><span class="sxs-lookup"><span data-stu-id="07980-141">Select **OK**</span></span>

 

## <a name="3-reverse-engineer-model"></a><span data-ttu-id="07980-142">3.反向工程模型</span><span class="sxs-lookup"><span data-stu-id="07980-142">3. Reverse Engineer Model</span></span>

<span data-ttu-id="07980-143">我們將使用 Entity Framework Designer （隨附于 Visual Studio 的一部分）來建立我們的模型。</span><span class="sxs-lookup"><span data-stu-id="07980-143">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="07980-144">**專案-&gt; 加入新專案 。**</span><span class="sxs-lookup"><span data-stu-id="07980-144">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="07980-145">從左側功能表中選取 [**資料**]，然後**ADO.NET 實體資料模型**</span><span class="sxs-lookup"><span data-stu-id="07980-145">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="07980-146">輸入**BloggingModel**作為名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="07980-146">Enter **BloggingModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="07980-147">這會啟動**實體資料模型 Wizard**</span><span class="sxs-lookup"><span data-stu-id="07980-147">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="07980-148">選取 [**從資料庫產生**]，然後按 **[下一步]**</span><span class="sxs-lookup"><span data-stu-id="07980-148">Select **Generate from Database** and click **Next**</span></span>

    ![Wizard 步驟1](~/ef6/media/wizardstep1.png)

-   <span data-ttu-id="07980-150">選取您在第一個區段中建立之資料庫的連接，並輸入**BloggingCoNtext**做為連接字串的名稱，然後按 **[下一步]**</span><span class="sxs-lookup"><span data-stu-id="07980-150">Select the connection to the database you created in the first section, enter **BloggingContext** as the name of the connection string and click **Next**</span></span>

    ![Wizard 步驟2](~/ef6/media/wizardstep2.png)

-   <span data-ttu-id="07980-152">按一下 [資料表] 旁的核取方塊以匯入所有資料表，然後按一下 [完成]</span><span class="sxs-lookup"><span data-stu-id="07980-152">Click the checkbox next to ‘Tables’ to import all tables and click ‘Finish’</span></span>

    ![Wizard 步驟3](~/ef6/media/wizardstep3.png)

 

<span data-ttu-id="07980-154">反向工程程式完成之後，新的模型就會新增至您的專案，並開啟以供您在 Entity Framework Designer 中觀看。</span><span class="sxs-lookup"><span data-stu-id="07980-154">Once the reverse engineer process completes the new model is added to your project and opened up for you to view in the Entity Framework Designer.</span></span> <span data-ttu-id="07980-155">App.config 檔案也已新增至您的專案，其中包含資料庫的連接詳細資料。</span><span class="sxs-lookup"><span data-stu-id="07980-155">An App.config file has also been added to your project with the connection details for the database.</span></span>

![模型初始](~/ef6/media/modelinitial.png)

### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="07980-157">Visual Studio 2010 中的其他步驟</span><span class="sxs-lookup"><span data-stu-id="07980-157">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="07980-158">如果您使用 Visual Studio 2010，則需要遵循一些額外的步驟，才能升級至最新版本的 Entity Framework。</span><span class="sxs-lookup"><span data-stu-id="07980-158">If you are working in Visual Studio 2010 there are some additional steps you need to follow to upgrade to the latest version of Entity Framework.</span></span> <span data-ttu-id="07980-159">升級很重要，因為它可讓您存取改良的 API 介面、更容易使用的應用程式，以及最新的錯誤修正。</span><span class="sxs-lookup"><span data-stu-id="07980-159">Upgrading is important because it gives you access to an improved API surface, that is much easier to use, as well as the latest bug fixes.</span></span>

<span data-ttu-id="07980-160">首先，我們需要從 NuGet 取得最新版本的 Entity Framework。</span><span class="sxs-lookup"><span data-stu-id="07980-160">First up, we need to get the latest version of Entity Framework from NuGet.</span></span>

-   <span data-ttu-id="07980-161">**專案– &gt; 管理 NuGet 套件 ...** 
    *如果您沒有 [\*\*管理 NuGet 套件 ...*\* ] 選項，您應該安裝[最新版本的 nuget](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) \*</span><span class="sxs-lookup"><span data-stu-id="07980-161">**Project –&gt; Manage NuGet Packages…**
*If you don’t have the **Manage NuGet Packages…** option you should install the [latest version of NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)*</span></span>
-   <span data-ttu-id="07980-162">選取 [**線上**] 索引標籤</span><span class="sxs-lookup"><span data-stu-id="07980-162">Select the **Online** tab</span></span>
-   <span data-ttu-id="07980-163">選取**EntityFramework**套件</span><span class="sxs-lookup"><span data-stu-id="07980-163">Select the **EntityFramework** package</span></span>
-   <span data-ttu-id="07980-164">按一下 [安裝]。</span><span class="sxs-lookup"><span data-stu-id="07980-164">Click **Install**</span></span>

<span data-ttu-id="07980-165">接下來，我們需要交換我們的模型，以產生會使用 DbCoNtext API 的程式碼，這是在 Entity Framework 的較新版本中引進。</span><span class="sxs-lookup"><span data-stu-id="07980-165">Next, we need to swap our model to generate code that makes use of the DbContext API, which was introduced in later versions of Entity Framework.</span></span>

-   <span data-ttu-id="07980-166">在 EF 設計工具中，以滑鼠右鍵按一下模型的空白位置，然後選取 [**新增程式碼產生專案**...]。</span><span class="sxs-lookup"><span data-stu-id="07980-166">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="07980-167">從左側功能表中選取 [**線上範本**]，然後搜尋**DbCoNtext**</span><span class="sxs-lookup"><span data-stu-id="07980-167">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="07980-168">選取**C @ no__t-1**的 EF 5.x DbCoNtext 產生器，輸入**BloggingModel**做為名稱，然後按一下 [**新增**]</span><span class="sxs-lookup"><span data-stu-id="07980-168">Select the EF **5.x DbContext Generator for C\#**, enter **BloggingModel** as the name and click **Add**</span></span>

    ![DbCoNtext 範本](~/ef6/media/dbcontexttemplate.png)

 

## <a name="4-reading--writing-data"></a><span data-ttu-id="07980-170">4.讀取 & 寫入資料</span><span class="sxs-lookup"><span data-stu-id="07980-170">4. Reading & Writing Data</span></span>

<span data-ttu-id="07980-171">既然我們已經有模型，就可以使用它來存取一些資料。</span><span class="sxs-lookup"><span data-stu-id="07980-171">Now that we have a model it’s time to use it to access some data.</span></span> <span data-ttu-id="07980-172">我們即將用來存取資料的類別會根據 EDMX 檔案自動產生。</span><span class="sxs-lookup"><span data-stu-id="07980-172">The classes we are going to use to access data are being automatically generated for you based on the EDMX file.</span></span>

<span data-ttu-id="07980-173">*這個螢幕擷取畫面是來自 Visual Studio 2012，如果您使用 Visual Studio 2010，BloggingModel.tt 和 BloggingModel.CoNtext.tt 檔案就會直接在您的專案底下，而不是在 EDMX 檔案底下。*</span><span class="sxs-lookup"><span data-stu-id="07980-173">*This screen shot is from Visual Studio 2012, if you are using Visual Studio 2010 the BloggingModel.tt and BloggingModel.Context.tt files will be directly under your project rather than nested under the EDMX file.*</span></span>

![產生的類別 DF](~/ef6/media/generatedclassesdf.png)

 

<span data-ttu-id="07980-175">在 Program.cs 中執行 Main 方法，如下所示。</span><span class="sxs-lookup"><span data-stu-id="07980-175">Implement the Main method in Program.cs as shown below.</span></span> <span data-ttu-id="07980-176">此程式碼會建立內容的新實例，然後使用它來插入新的 Blog。</span><span class="sxs-lookup"><span data-stu-id="07980-176">This code creates a new instance of our context and then uses it to insert a new Blog.</span></span> <span data-ttu-id="07980-177">然後，它會使用 LINQ 查詢來抓取資料庫中依標題字母順序排序的所有 Blog。</span><span class="sxs-lookup"><span data-stu-id="07980-177">Then it uses a LINQ query to retrieve all Blogs from the database ordered alphabetically by Title.</span></span>

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

<span data-ttu-id="07980-178">您現在可以執行應用程式並加以測試。</span><span class="sxs-lookup"><span data-stu-id="07980-178">You can now run the application and test it out.</span></span>

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
 

## <a name="5-dealing-with-database-changes"></a><span data-ttu-id="07980-179">5.處理資料庫變更</span><span class="sxs-lookup"><span data-stu-id="07980-179">5. Dealing with Database Changes</span></span>

<span data-ttu-id="07980-180">現在可以對資料庫架構進行一些變更，當我們進行這些變更時，我們也需要更新我們的模型來反映這些變更。</span><span class="sxs-lookup"><span data-stu-id="07980-180">Now it’s time to make some changes to our database schema, when we make these changes we also need to update our model to reflect those changes.</span></span>

<span data-ttu-id="07980-181">第一個步驟是對資料庫架構進行一些變更。</span><span class="sxs-lookup"><span data-stu-id="07980-181">The first step is to make some changes to the database schema.</span></span> <span data-ttu-id="07980-182">我們要將 Users 資料表加入至架構。</span><span class="sxs-lookup"><span data-stu-id="07980-182">We’re going to add a Users table to the schema.</span></span>

-   <span data-ttu-id="07980-183">以滑鼠右鍵按一下伺服器總管中的 [ **DatabaseFirst** ] 資料庫，然後選取 [追加**查詢**]</span><span class="sxs-lookup"><span data-stu-id="07980-183">Right-click on the **DatabaseFirst.Blogging** database in Server Explorer and select **New Query**</span></span>
-   <span data-ttu-id="07980-184">將下列 SQL 複製到新的查詢中，然後以滑鼠右鍵按一下查詢並選取 [**執行**]。</span><span class="sxs-lookup"><span data-stu-id="07980-184">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

``` SQL
CREATE TABLE [dbo].[Users]
(
    [Username] NVARCHAR(50) NOT NULL PRIMARY KEY,  
    [DisplayName] NVARCHAR(MAX) NULL
)
```

<span data-ttu-id="07980-185">現在已更新架構，就可以使用這些變更來更新模型。</span><span class="sxs-lookup"><span data-stu-id="07980-185">Now that the schema is updated, it’s time to update the model with those changes.</span></span>

-   <span data-ttu-id="07980-186">在 EF 設計工具中，以滑鼠右鍵按一下模型的空白位置，然後選取 [從資料庫更新模型]，這將會啟動更新嚮導</span><span class="sxs-lookup"><span data-stu-id="07980-186">Right-click on an empty spot of your model in the EF Designer and select ‘Update Model from Database…’, this will launch the Update Wizard</span></span>
-   <span data-ttu-id="07980-187">在 [更新嚮導] 的 [新增] 索引標籤上，核取 [資料表] 旁的方塊，這表示我們想要從架構新增任何新的資料表。</span><span class="sxs-lookup"><span data-stu-id="07980-187">On the Add tab of the Update Wizard check the box next to Tables, this indicates that we want to add any new tables from the schema.</span></span>
    <span data-ttu-id="07980-188">@no__t 0The 重新整理 索引標籤會顯示模型中任何現有的資料表，並會在更新期間檢查是否有變更。刪除 索引標籤會顯示已從架構移除的任何資料表，而且也會在更新過程中從模型中移除。系統會自動偵測這兩個索引標籤上的資訊，並提供僅供參考之用，您無法變更任何設定。 \*</span><span class="sxs-lookup"><span data-stu-id="07980-188">*The Refresh tab shows any existing tables in the model that will be checked for changes during the update. The Delete tabs show any tables that have been removed from the schema and will also be removed from the model as part of the update. The information on these two tabs is automatically detected and is provided for informational purposes only, you cannot change any settings.*</span></span>

    ![重新整理嚮導](~/ef6/media/refreshwizard.png)

-   <span data-ttu-id="07980-190">按一下 [更新嚮導] 上的 [完成]</span><span class="sxs-lookup"><span data-stu-id="07980-190">Click Finish on the Update Wizard</span></span>

 

<span data-ttu-id="07980-191">模型現在已更新為包含新的使用者實體，其對應至我們新增至資料庫的使用者資料表。</span><span class="sxs-lookup"><span data-stu-id="07980-191">The model is now updated to include a new User entity that maps to the Users table we added to the database.</span></span>

![已更新模型](~/ef6/media/modelupdated.png)

## <a name="summary"></a><span data-ttu-id="07980-193">總結</span><span class="sxs-lookup"><span data-stu-id="07980-193">Summary</span></span>

<span data-ttu-id="07980-194">在本逐步解說中，我們探討了 Database First 開發，這讓我們能夠在以現有資料庫為基礎的 EF 設計工具中建立模型。</span><span class="sxs-lookup"><span data-stu-id="07980-194">In this walkthrough we looked at Database First development, which allowed us to create a model in the EF Designer based on an existing database.</span></span> <span data-ttu-id="07980-195">然後，我們會使用該模型來讀取及寫入資料庫中的某些資料。</span><span class="sxs-lookup"><span data-stu-id="07980-195">We then used that model to read and write some data from the database.</span></span> <span data-ttu-id="07980-196">最後，我們更新了模型，以反映我們對資料庫架構所做的變更。</span><span class="sxs-lookup"><span data-stu-id="07980-196">Finally, we updated the model to reflect changes we made to the database schema.</span></span>
