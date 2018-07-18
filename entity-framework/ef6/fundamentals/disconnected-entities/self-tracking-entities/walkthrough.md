---
title: 自我追蹤實體的逐步解說-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: b21207c9-1d95-4aa3-ae05-bc5fe300dab0
caps.latest.revision: 3
ms.openlocfilehash: d07ae727ffba60a9296b34b9261acd99f7e8e3b6
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2018
ms.locfileid: "39120354"
---
# <a name="self-tracking-entities-walkthrough"></a><span data-ttu-id="026e8-102">自我追蹤實體的逐步解說</span><span class="sxs-lookup"><span data-stu-id="026e8-102">Self-Tracking Entities Walkthrough</span></span>
> [!IMPORTANT]
> <span data-ttu-id="026e8-103">我們不再建議使用自我追蹤實體範本。</span><span class="sxs-lookup"><span data-stu-id="026e8-103">We no longer recommend using the self-tracking-entities template.</span></span> <span data-ttu-id="026e8-104">繼續提供該範本只是為了支援現有應用程式。</span><span class="sxs-lookup"><span data-stu-id="026e8-104">It will only continue to be available to support existing applications.</span></span> <span data-ttu-id="026e8-105">如果您的應用程式需要使用已中斷連線的實體圖形，請考慮使用 [Trackable Entities](http://trackableentities.github.io/) (可追蹤的實體) 之類的其他替代項目，這是一項類似於自我追蹤實體的技術，可由社群更積極地進行開發或使用低層級變更追蹤 API 來撰寫自訂程式碼。</span><span class="sxs-lookup"><span data-stu-id="026e8-105">If your application requires working with disconnected graphs of entities, consider other alternatives such as [Trackable Entities](http://trackableentities.github.io/), which is a technology similar to Self-Tracking-Entities that is more actively developed by the community, or writing custom code using the low-level change tracking APIs.</span></span>

<span data-ttu-id="026e8-106">本逐步解說將示範 Windows Communication Foundation (WCF) 服務會公開傳回實體圖形作業的案例。</span><span class="sxs-lookup"><span data-stu-id="026e8-106">This walkthrough demonstrates the scenario in which a Windows Communication Foundation (WCF) service exposes an operation that returns an entity graph.</span></span> <span data-ttu-id="026e8-107">接著，用戶端應用程式會操作該圖形並提交修改至服務作業的驗證，並將更新儲存至資料庫，使用 Entity Framework。</span><span class="sxs-lookup"><span data-stu-id="026e8-107">Next, a client application manipulates that graph and submits the modifications to a service operation that validates and saves the updates to a database using Entity Framework.</span></span>

<span data-ttu-id="026e8-108">完成本逐步解說之前請確定您閱讀[自我追蹤實體](index.md)頁面。</span><span class="sxs-lookup"><span data-stu-id="026e8-108">Before completing this walkthrough make sure you read the [Self-Tracking Entities](index.md) page.</span></span>

<span data-ttu-id="026e8-109">這個逐步解說會完成下列動作：</span><span class="sxs-lookup"><span data-stu-id="026e8-109">This walkthrough completes the following actions:</span></span>

-   <span data-ttu-id="026e8-110">建立可存取的資料庫。</span><span class="sxs-lookup"><span data-stu-id="026e8-110">Creates a database to access.</span></span>
-   <span data-ttu-id="026e8-111">建立包含模型的類別庫。</span><span class="sxs-lookup"><span data-stu-id="026e8-111">Creates a class library that contains the model.</span></span>
-   <span data-ttu-id="026e8-112">交換 Self-Tracking Entity Generator 範本。</span><span class="sxs-lookup"><span data-stu-id="026e8-112">Swaps to the Self-Tracking Entity Generator template.</span></span>
-   <span data-ttu-id="026e8-113">將實體類別移至個別的專案中。</span><span class="sxs-lookup"><span data-stu-id="026e8-113">Moves the entity classes to a separate project.</span></span>
-   <span data-ttu-id="026e8-114">建立 WCF 服務公開查詢和儲存實體的作業。</span><span class="sxs-lookup"><span data-stu-id="026e8-114">Creates a WCF service that exposes operations to query and save entities.</span></span>
-   <span data-ttu-id="026e8-115">建立用戶端應用程式 （主控台和 WPF） 取用服務。</span><span class="sxs-lookup"><span data-stu-id="026e8-115">Creates client applications (Console and WPF) that consume the service.</span></span>

<span data-ttu-id="026e8-116">我們將使用第一個資料庫在此逐步解說中，但相同的技巧同樣適用於第一個模型。</span><span class="sxs-lookup"><span data-stu-id="026e8-116">We'll use Database First in this walkthrough but the same techniques apply equally to Model First.</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="026e8-117">必要條件</span><span class="sxs-lookup"><span data-stu-id="026e8-117">Pre-Requisites</span></span>

<span data-ttu-id="026e8-118">若要完成此逐步解說，您必須 Visual Studio 的最新版本。</span><span class="sxs-lookup"><span data-stu-id="026e8-118">To complete this walkthrough you will need a recent version of Visual Studio.</span></span>

## <a name="create-a-database"></a><span data-ttu-id="026e8-119">建立資料庫</span><span class="sxs-lookup"><span data-stu-id="026e8-119">Create a Database</span></span>

<span data-ttu-id="026e8-120">Visual Studio 隨附安裝的資料庫伺服器是您已安裝的 Visual Studio 版本而有所不同：</span><span class="sxs-lookup"><span data-stu-id="026e8-120">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="026e8-121">如果您使用 Visual Studio 2012，則您將會建立新的 LocalDB 資料庫。</span><span class="sxs-lookup"><span data-stu-id="026e8-121">If you are using Visual Studio 2012 then you'll be creating a LocalDB database.</span></span>
-   <span data-ttu-id="026e8-122">如果您使用 Visual Studio 2010 將會建立 SQL Express 資料庫。</span><span class="sxs-lookup"><span data-stu-id="026e8-122">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>

<span data-ttu-id="026e8-123">接下來產生的資料庫。</span><span class="sxs-lookup"><span data-stu-id="026e8-123">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="026e8-124">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="026e8-124">Open Visual Studio</span></span>
-   <span data-ttu-id="026e8-125">**檢視-&gt;伺服器總管**</span><span class="sxs-lookup"><span data-stu-id="026e8-125">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="026e8-126">以滑鼠右鍵按一下**資料連線-&gt;新增連接...**</span><span class="sxs-lookup"><span data-stu-id="026e8-126">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="026e8-127">如果您尚未連線至資料庫從伺服器總管之前您必須選取**Microsoft SQL Server**做為資料來源</span><span class="sxs-lookup"><span data-stu-id="026e8-127">If you haven’t connected to a database from Server Explorer before you’ll need to select **Microsoft SQL Server** as the data source</span></span>
-   <span data-ttu-id="026e8-128">連接到 LocalDB 或 SQL Express，何者而定，您已安裝</span><span class="sxs-lookup"><span data-stu-id="026e8-128">Connect to either LocalDB or SQL Express, depending on which one you have installed</span></span>
-   <span data-ttu-id="026e8-129">請輸入**STESample**做為資料庫名稱</span><span class="sxs-lookup"><span data-stu-id="026e8-129">Enter **STESample** as the database name</span></span>
-   <span data-ttu-id="026e8-130">選取  **確定**而且會要求您想要建立新資料庫，請選取**是**</span><span class="sxs-lookup"><span data-stu-id="026e8-130">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>
-   <span data-ttu-id="026e8-131">在 伺服器總管現在會顯示新的資料庫</span><span class="sxs-lookup"><span data-stu-id="026e8-131">The new database will now appear in Server Explorer</span></span>
-   <span data-ttu-id="026e8-132">如果您使用 Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="026e8-132">If you are using Visual Studio 2012</span></span>
    -   <span data-ttu-id="026e8-133">以滑鼠右鍵按一下伺服器總管 中的資料庫，然後選取**新查詢**</span><span class="sxs-lookup"><span data-stu-id="026e8-133">Right-click on the database in Server Explorer and select **New Query**</span></span>
    -   <span data-ttu-id="026e8-134">將下列 SQL 複製到新的查詢，然後以滑鼠右鍵按一下查詢並選取**Execute**</span><span class="sxs-lookup"><span data-stu-id="026e8-134">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>
-   <span data-ttu-id="026e8-135">如果您使用 Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="026e8-135">If you are using Visual Studio 2010</span></span>
    -   <span data-ttu-id="026e8-136">選取 **資料集&gt;Transact SQL 編輯器-&gt;新增查詢連接...**</span><span class="sxs-lookup"><span data-stu-id="026e8-136">Select **Data -&gt; Transact SQL Editor -&gt; New Query Connection...**</span></span>
    -   <span data-ttu-id="026e8-137">請輸入 **。\\SQLEXPRESS**作為伺服器名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="026e8-137">Enter **.\\SQLEXPRESS** as the server name and click **OK**</span></span>
    -   <span data-ttu-id="026e8-138">選取  **STESample**資料庫從下拉式清單頂端的查詢編輯器</span><span class="sxs-lookup"><span data-stu-id="026e8-138">Select the **STESample** database from the drop down at the top of the query editor</span></span>
    -   <span data-ttu-id="026e8-139">將下列 SQL 複製到新的查詢，然後以滑鼠右鍵按一下查詢並選取**執行 SQL**</span><span class="sxs-lookup"><span data-stu-id="026e8-139">Copy the following SQL into the new query, then right-click on the query and select **Execute SQL**</span></span>

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

    SET IDENTITY_INSERT [dbo].[Blogs] ON
    INSERT INTO [dbo].[Blogs] ([BlogId], [Name], [Url]) VALUES (1, N'ADO.NET Blog', N'blogs.msdn.com/adonet')
    SET IDENTITY_INSERT [dbo].[Blogs] OFF
    INSERT INTO [dbo].[Posts] ([Title], [Content], [BlogId]) VALUES (N'Intro to EF', N'Interesting stuff...', 1)
    INSERT INTO [dbo].[Posts] ([Title], [Content], [BlogId]) VALUES (N'What is New', N'More interesting stuff...', 1)
```

## <a name="create-the-model"></a><span data-ttu-id="026e8-140">建立模型</span><span class="sxs-lookup"><span data-stu-id="026e8-140">Create the Model</span></span>

<span data-ttu-id="026e8-141">首先，我們必須要將此模型的專案。</span><span class="sxs-lookup"><span data-stu-id="026e8-141">First up, we need a project to put the model in.</span></span>

-   <span data-ttu-id="026e8-142">**檔案-&gt;新增-&gt;專案...**</span><span class="sxs-lookup"><span data-stu-id="026e8-142">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="026e8-143">選取  **Visual C\#** 從左窗格中，然後**類別庫**</span><span class="sxs-lookup"><span data-stu-id="026e8-143">Select **Visual C\#** from the left pane and then **Class Library**</span></span>
-   <span data-ttu-id="026e8-144">請輸入**STESample**作為名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="026e8-144">Enter **STESample** as the name and click **OK**</span></span>

<span data-ttu-id="026e8-145">現在我們將建立簡單的模型來存取資料庫的 EF 設計工具中：</span><span class="sxs-lookup"><span data-stu-id="026e8-145">Now we'll create a simple model in the EF Designer to access our database:</span></span>

-   <span data-ttu-id="026e8-146">**專案-&gt;加入新項目...**</span><span class="sxs-lookup"><span data-stu-id="026e8-146">**Project -&gt; Add New Item...**</span></span>
-   <span data-ttu-id="026e8-147">選取 **資料**從左窗格中，然後**ADO.NET 實體資料模型**</span><span class="sxs-lookup"><span data-stu-id="026e8-147">Select **Data** from the left pane and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="026e8-148">請輸入**BloggingModel**作為名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="026e8-148">Enter **BloggingModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="026e8-149">選取 **從資料庫產生**，按一下 **下一步**</span><span class="sxs-lookup"><span data-stu-id="026e8-149">Select **Generate from database** and click **Next**</span></span>
-   <span data-ttu-id="026e8-150">輸入您在上一節中建立資料庫的連接資訊</span><span class="sxs-lookup"><span data-stu-id="026e8-150">Enter the connection information for the database that you created in the previous section</span></span>
-   <span data-ttu-id="026e8-151">請輸入**為 [bloggingcontext]** 做為連接字串，然後按一下 [名稱**下一步]**</span><span class="sxs-lookup"><span data-stu-id="026e8-151">Enter **BloggingContext** as the name for the connection string and click **Next**</span></span>
-   <span data-ttu-id="026e8-152">核取方塊旁**資料表**，按一下 **完成**</span><span class="sxs-lookup"><span data-stu-id="026e8-152">Check the box next to **Tables** and click **Finish**</span></span>

## <a name="swap-to-ste-code-generation"></a><span data-ttu-id="026e8-153">切換至 STE 程式碼產生</span><span class="sxs-lookup"><span data-stu-id="026e8-153">Swap to STE Code Generation</span></span>

<span data-ttu-id="026e8-154">現在，我們需要停用的預設程式碼產生和自我追蹤實體的交換。</span><span class="sxs-lookup"><span data-stu-id="026e8-154">Now we need to disable the default code generation and swap to Self-Tracking Entities.</span></span>

### <a name="if-you-are-using-visual-studio-2012"></a><span data-ttu-id="026e8-155">如果您使用 Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="026e8-155">If you are using Visual Studio 2012</span></span>

-   <span data-ttu-id="026e8-156">依序展開**BloggingModel.edmx**中**方案總管**並刪除**BloggingModel.tt**並**BloggingModel.Context.tt** 
    *這會停用的預設程式碼產生*</span><span class="sxs-lookup"><span data-stu-id="026e8-156">Expand **BloggingModel.edmx** in **Solution Explorer** and delete the **BloggingModel.tt** and **BloggingModel.Context.tt**
*This will disable the default code generation*</span></span>
-   <span data-ttu-id="026e8-157">以滑鼠右鍵按一下介面，然後選取 EF 設計工具上的空白區域**加入的程式碼產生項目...**</span><span class="sxs-lookup"><span data-stu-id="026e8-157">Right-click an empty area on the EF Designer surface and select **Add Code Generation Item...**</span></span>
-   <span data-ttu-id="026e8-158">選取 **線上**左的窗格中搜尋**STE 產生器**</span><span class="sxs-lookup"><span data-stu-id="026e8-158">Select **Online** from the left pane and search for **STE Generator**</span></span>
-   <span data-ttu-id="026e8-159">選取  **STE 適用於 C 的產生器\#** 範本，輸入**STETemplate**做為名稱，然後按一下**新增**</span><span class="sxs-lookup"><span data-stu-id="026e8-159">Select the **STE Generator for C\#** template, enter **STETemplate** as the name and click **Add**</span></span>
-   <span data-ttu-id="026e8-160">**STETemplate.tt**並**STETemplate.Context.tt**檔案會新增 BloggingModel.edmx 檔案巢狀</span><span class="sxs-lookup"><span data-stu-id="026e8-160">The **STETemplate.tt** and **STETemplate.Context.tt** files are added nested under the BloggingModel.edmx file</span></span>

### <a name="if-you-are-using-visual-studio-2010"></a><span data-ttu-id="026e8-161">如果您使用 Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="026e8-161">If you are using Visual Studio 2010</span></span>

-   <span data-ttu-id="026e8-162">以滑鼠右鍵按一下介面，然後選取 EF 設計工具上的空白區域**加入的程式碼產生項目...**</span><span class="sxs-lookup"><span data-stu-id="026e8-162">Right-click an empty area on the EF Designer surface and select **Add Code Generation Item...**</span></span>
-   <span data-ttu-id="026e8-163">選取 **程式碼**從左窗格中，然後**ADO.NET Self-Tracking Entity Generator**</span><span class="sxs-lookup"><span data-stu-id="026e8-163">Select **Code** from the left pane and then **ADO.NET Self-Tracking Entity Generator**</span></span>
-   <span data-ttu-id="026e8-164">請輸入**STETemplate**作為名稱，然後按一下**新增**</span><span class="sxs-lookup"><span data-stu-id="026e8-164">Enter **STETemplate** as the name and click **Add**</span></span>
-   <span data-ttu-id="026e8-165">**STETemplate.tt**並**STETemplate.Context.tt**檔案會直接新增至您的專案</span><span class="sxs-lookup"><span data-stu-id="026e8-165">The **STETemplate.tt** and **STETemplate.Context.tt** files are added directly to your project</span></span>

## <a name="move-entity-types-into-separate-project"></a><span data-ttu-id="026e8-166">將實體類型移至不同的專案</span><span class="sxs-lookup"><span data-stu-id="026e8-166">Move Entity Types into Separate Project</span></span>

<span data-ttu-id="026e8-167">若要使用自我追蹤實體用戶端應用程式需要從我們的模型產生實體類別的存取權。</span><span class="sxs-lookup"><span data-stu-id="026e8-167">To use Self-Tracking Entities our client application needs access to the entity classes generated from our model.</span></span> <span data-ttu-id="026e8-168">因為我們不想要公開 （expose） 用戶端應用程式的整個模型，所以我們要進入個別的專案中的實體類別。</span><span class="sxs-lookup"><span data-stu-id="026e8-168">Because we don't want to expose the whole model to the client application we're going to move the entity classes into a separate project.</span></span>

<span data-ttu-id="026e8-169">第一步是停止現有的專案中產生實體類別：</span><span class="sxs-lookup"><span data-stu-id="026e8-169">The first step is to stop generating entity classes in the existing project:</span></span>

-   <span data-ttu-id="026e8-170">以滑鼠右鍵按一下**STETemplate.tt**中**方案總管**，然後選取**屬性**</span><span class="sxs-lookup"><span data-stu-id="026e8-170">Right-click on **STETemplate.tt** in **Solution Explorer** and select **Properties**</span></span>
-   <span data-ttu-id="026e8-171">在 **屬性**視窗中清除**TextTemplatingFileGenerator**從**CustomTool**屬性</span><span class="sxs-lookup"><span data-stu-id="026e8-171">In the **Properties** window clear **TextTemplatingFileGenerator** from the **CustomTool** property</span></span>
-   <span data-ttu-id="026e8-172">依序展開**STETemplate.tt**中**方案總管 中**刪除其下的巢狀的所有檔案</span><span class="sxs-lookup"><span data-stu-id="026e8-172">Expand **STETemplate.tt** in **Solution Explorer** and delete all files nested under it</span></span>

<span data-ttu-id="026e8-173">接下來，我們即將加入新的專案，並在其中產生實體類別</span><span class="sxs-lookup"><span data-stu-id="026e8-173">Next, we are going to add a new project and generate the entity classes in it</span></span>

-   <span data-ttu-id="026e8-174">**檔案-&gt;新增-&gt;專案...**</span><span class="sxs-lookup"><span data-stu-id="026e8-174">**File -&gt; Add -&gt; Project...**</span></span>
-   <span data-ttu-id="026e8-175">選取  **Visual C\#** 從左窗格中，然後**類別庫**</span><span class="sxs-lookup"><span data-stu-id="026e8-175">Select **Visual C\#** from the left pane and then **Class Library**</span></span>
-   <span data-ttu-id="026e8-176">請輸入**STESample.Entities**作為名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="026e8-176">Enter **STESample.Entities** as the name and click **OK**</span></span>
-   <span data-ttu-id="026e8-177">**專案-&gt;加入現有項目...**</span><span class="sxs-lookup"><span data-stu-id="026e8-177">**Project -&gt; Add Existing Item...**</span></span>
-   <span data-ttu-id="026e8-178">瀏覽至**STESample**專案資料夾</span><span class="sxs-lookup"><span data-stu-id="026e8-178">Navigate to the **STESample** project folder</span></span>
-   <span data-ttu-id="026e8-179">若要檢視選取**的所有檔案 (\*。\*)**</span><span class="sxs-lookup"><span data-stu-id="026e8-179">Select to view **All Files (\*.\*)**</span></span>
-   <span data-ttu-id="026e8-180">選取  **STETemplate.tt**檔案</span><span class="sxs-lookup"><span data-stu-id="026e8-180">Select the **STETemplate.tt** file</span></span>
-   <span data-ttu-id="026e8-181">按一下下拉式箭號旁**新增**按鈕，然後選取**加入做為連結**</span><span class="sxs-lookup"><span data-stu-id="026e8-181">Click on the drop down arrow next to the **Add** button and select **Add As Link**</span></span>

    ![AddLinkedTemplate](~/ef6/media/addlinkedtemplate.png)

<span data-ttu-id="026e8-183">我們也要確定內容相同的命名空間中產生實體類別。</span><span class="sxs-lookup"><span data-stu-id="026e8-183">We're also going to make sure the entity classes get generated in the same namespace as the context.</span></span> <span data-ttu-id="026e8-184">這只會減少 using 陳述式，我們需要新增在我們的應用程式的數目。</span><span class="sxs-lookup"><span data-stu-id="026e8-184">This just reduces the number of using statements we need to add throughout our application.</span></span>

-   <span data-ttu-id="026e8-185">以滑鼠右鍵按一下連結**STETemplate.tt**中**方案總管**，然後選取**屬性**</span><span class="sxs-lookup"><span data-stu-id="026e8-185">Right-click on the linked **STETemplate.tt** in **Solution Explorer** and select **Properties**</span></span>
-   <span data-ttu-id="026e8-186">在 [**屬性**] 視窗中設定**自訂工具命名空間**到**STESample**</span><span class="sxs-lookup"><span data-stu-id="026e8-186">In the **Properties** window set **Custom Tool Namespace** to **STESample**</span></span>

<span data-ttu-id="026e8-187">STE 範本所產生的程式碼將會需要的參考**System.Runtime.Serialization**以編譯。</span><span class="sxs-lookup"><span data-stu-id="026e8-187">The code generated by the STE template will need a reference to **System.Runtime.Serialization** in order to compile.</span></span> <span data-ttu-id="026e8-188">此程式庫所需的 WCF **DataContract**並**DataMember**可序列化的實體類型所使用的屬性。</span><span class="sxs-lookup"><span data-stu-id="026e8-188">This library is needed for the WCF **DataContract** and **DataMember** attributes that are used on the serializable entity types.</span></span>

-   <span data-ttu-id="026e8-189">以滑鼠右鍵按一下**STESample.Entities**專案中**方案總管**，然後選取**加入參考...**</span><span class="sxs-lookup"><span data-stu-id="026e8-189">Right click on the **STESample.Entities** project in **Solution Explorer** and select **Add Reference...**</span></span>
    -   <span data-ttu-id="026e8-190">在 Visual Studio 2012-核取方塊旁**System.Runtime.Serialization** ，按一下  **確定**</span><span class="sxs-lookup"><span data-stu-id="026e8-190">In Visual Studio 2012 - check the box next to **System.Runtime.Serialization** and click **OK**</span></span>
    -   <span data-ttu-id="026e8-191">在 Visual Studio 2010-選取**System.Runtime.Serialization** ，按一下  **確定**</span><span class="sxs-lookup"><span data-stu-id="026e8-191">In Visual Studio 2010 - select **System.Runtime.Serialization** and click **OK**</span></span>

<span data-ttu-id="026e8-192">最後，我們的內容中的專案將會需要的實體類型的參考。</span><span class="sxs-lookup"><span data-stu-id="026e8-192">Finally, the project with our context in it will need a reference to the entity types.</span></span>

-   <span data-ttu-id="026e8-193">以滑鼠右鍵按一下**STESample**專案中**方案總管**，然後選取**加入參考...**</span><span class="sxs-lookup"><span data-stu-id="026e8-193">Right click on the **STESample** project in **Solution Explorer** and select **Add Reference...**</span></span>
    -   <span data-ttu-id="026e8-194">在 Visual Studio 2012-選取**解決方案**從左窗格中，核取方塊旁**STESample.Entities**按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="026e8-194">In Visual Studio 2012 - select **Solution** from the left pane, check the box next to **STESample.Entities** and click **OK**</span></span>
    -   <span data-ttu-id="026e8-195">在 Visual Studio 2010-選取**專案**索引標籤上，選取**STESample.Entities**按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="026e8-195">In Visual Studio 2010 - select the **Projects** tab, select **STESample.Entities** and click **OK**</span></span>

>[!NOTE]
> <span data-ttu-id="026e8-196">將實體類型移到個別的專案的另一個選項是將範本檔案，而不是將其連結從其預設位置。</span><span class="sxs-lookup"><span data-stu-id="026e8-196">Another option for moving the entity types to a separate project is to move the template file, rather than linking it from its default location.</span></span> <span data-ttu-id="026e8-197">如果您這麼做，您必須更新**inputFile**變數的範本，以提供 edmx 檔案的相對路徑 (在此範例中會 **...\\BloggingModel.edmx**)。</span><span class="sxs-lookup"><span data-stu-id="026e8-197">If you do this, you will need to update the **inputFile** variable in the template to provide the relative path to the edmx file (in this example that would be **..\\BloggingModel.edmx**).</span></span>

## <a name="create-a-wcf-service"></a><span data-ttu-id="026e8-198">建立 WCF 服務</span><span class="sxs-lookup"><span data-stu-id="026e8-198">Create a WCF Service</span></span>

<span data-ttu-id="026e8-199">現在就可以開始新增 WCF 服務，以公開我們的資料，我們先建立專案。</span><span class="sxs-lookup"><span data-stu-id="026e8-199">Now it's time to add a WCF Service to expose our data, we'll start by creating the project.</span></span>

-   <span data-ttu-id="026e8-200">**檔案-&gt;新增-&gt;專案...**</span><span class="sxs-lookup"><span data-stu-id="026e8-200">**File -&gt; Add -&gt; Project...**</span></span>
-   <span data-ttu-id="026e8-201">選取  **Visual C\#** 從左窗格中，然後**WCF 服務應用程式**</span><span class="sxs-lookup"><span data-stu-id="026e8-201">Select **Visual C\#** from the left pane and then **WCF Service Application**</span></span>
-   <span data-ttu-id="026e8-202">請輸入**STESample.Service**作為名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="026e8-202">Enter **STESample.Service** as the name and click **OK**</span></span>
-   <span data-ttu-id="026e8-203">將參考加入**System.Data.Entity**組件</span><span class="sxs-lookup"><span data-stu-id="026e8-203">Add a reference to the **System.Data.Entity** assembly</span></span>
-   <span data-ttu-id="026e8-204">將參考加入**STESample**並**STESample.Entities**專案</span><span class="sxs-lookup"><span data-stu-id="026e8-204">Add a reference to the **STESample** and **STESample.Entities** projects</span></span>

<span data-ttu-id="026e8-205">我們需要將 EF 連接字串複製到此專案，以便在執行階段找到。</span><span class="sxs-lookup"><span data-stu-id="026e8-205">We need to copy the EF connection string to this project so that it is found at runtime.</span></span>

-   <span data-ttu-id="026e8-206">開啟**App.Config**適用於檔案 * * STESample * * 專案和複製**connectionStrings**項目</span><span class="sxs-lookup"><span data-stu-id="026e8-206">Open the **App.Config** file for the **STESample **project and copy the **connectionStrings** element</span></span>
-   <span data-ttu-id="026e8-207">貼上**connectionStrings**元素的子元素當做**configuration**項目**Web.Config**中的檔案**STESample.Service**專案</span><span class="sxs-lookup"><span data-stu-id="026e8-207">Paste the **connectionStrings** element as a child element of the **configuration** element of the **Web.Config** file in the **STESample.Service** project</span></span>

<span data-ttu-id="026e8-208">現在就可以開始實作實際的服務。</span><span class="sxs-lookup"><span data-stu-id="026e8-208">Now it's time to implement the actual service.</span></span>

-   <span data-ttu-id="026e8-209">開啟**IService1.cs**並取代為下列程式碼的內容</span><span class="sxs-lookup"><span data-stu-id="026e8-209">Open **IService1.cs** and replace the contents with the following code</span></span>

``` csharp
    using System.Collections.Generic;
    using System.ServiceModel;

    namespace STESample.Service
    {
        [ServiceContract]
        public interface IService1
        {
            [OperationContract]
            List<Blog> GetBlogs();

            [OperationContract]
            void UpdateBlog(Blog blog);
        }
    }
```

-   <span data-ttu-id="026e8-210">開啟**Service1.svc**並取代為下列程式碼的內容</span><span class="sxs-lookup"><span data-stu-id="026e8-210">Open **Service1.svc** and replace the contents with the following code</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data;
    using System.Linq;

    namespace STESample.Service
    {
        public class Service1 : IService1
        {
            /// <summary>
            /// Gets all the Blogs and related Posts.
            /// </summary>
            public List<Blog> GetBlogs()
            {
                using (BloggingContext context = new BloggingContext())
                {
                    return context.Blogs.Include("Posts").ToList();
                }
            }

            /// <summary>
            /// Updates Blog and its related Posts.
            /// </summary>
            public void UpdateBlog(Blog blog)
            {
                using (BloggingContext context = new BloggingContext())
                {
                    try
                    {
                        // TODO: Perform validation on the updated order before applying the changes.

                        // The ApplyChanges method examines the change tracking information
                        // contained in the graph of self-tracking entities to infer the set of operations
                        // that need to be performed to reflect the changes in the database.
                        context.Blogs.ApplyChanges(blog);
                        context.SaveChanges();

                    }
                    catch (UpdateException)
                    {
                        // To avoid propagating exception messages that contain sensitive data to the client tier
                        // calls to ApplyChanges and SaveChanges should be wrapped in exception handling code.
                        throw new InvalidOperationException("Failed to update. Try your request again.");
                    }
                }
            }        
        }
    }
```

## <a name="consume-the-service-from-a-console-application"></a><span data-ttu-id="026e8-211">取用的主控台應用程式的服務</span><span class="sxs-lookup"><span data-stu-id="026e8-211">Consume the Service from a Console Application</span></span>

<span data-ttu-id="026e8-212">讓我們建立使用我們的服務的主控台應用程式。</span><span class="sxs-lookup"><span data-stu-id="026e8-212">Let's create a console application that uses our service.</span></span>

-   <span data-ttu-id="026e8-213">**檔案-&gt;新增-&gt;專案...**</span><span class="sxs-lookup"><span data-stu-id="026e8-213">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="026e8-214">選取  **Visual C\#** 從左窗格中，然後**主控台應用程式**</span><span class="sxs-lookup"><span data-stu-id="026e8-214">Select **Visual C\#** from the left pane and then **Console Application**</span></span>
-   <span data-ttu-id="026e8-215">請輸入**STESample.ConsoleTest**作為名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="026e8-215">Enter **STESample.ConsoleTest** as the name and click **OK**</span></span>
-   <span data-ttu-id="026e8-216">將參考加入**STESample.Entities**專案</span><span class="sxs-lookup"><span data-stu-id="026e8-216">Add a reference to the **STESample.Entities** project</span></span>

<span data-ttu-id="026e8-217">我們需要我們的 WCF 服務的服務參考</span><span class="sxs-lookup"><span data-stu-id="026e8-217">We need a service reference to our WCF service</span></span>

-   <span data-ttu-id="026e8-218">以滑鼠右鍵按一下**STESample.ConsoleTest**專案中**方案總管**，然後選取**加入服務參考...**</span><span class="sxs-lookup"><span data-stu-id="026e8-218">Right-click the **STESample.ConsoleTest** project in **Solution Explorer** and select **Add Service Reference...**</span></span>
-   <span data-ttu-id="026e8-219">按一下 **探索**</span><span class="sxs-lookup"><span data-stu-id="026e8-219">Click **Discover**</span></span>
-   <span data-ttu-id="026e8-220">請輸入**BloggingService**作為命名空間，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="026e8-220">Enter **BloggingService** as the namespace and click **OK**</span></span>

<span data-ttu-id="026e8-221">現在我們可以撰寫一些程式碼來取用服務。</span><span class="sxs-lookup"><span data-stu-id="026e8-221">Now we can write some code to consume the service.</span></span>

-   <span data-ttu-id="026e8-222">開啟**Program.cs**並取代為下列程式碼的內容。</span><span class="sxs-lookup"><span data-stu-id="026e8-222">Open **Program.cs** and replace the contents with the following code.</span></span>

``` csharp
    using STESample.ConsoleTest.BloggingService;
    using System;
    using System.Linq;

    namespace STESample.ConsoleTest
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Print out the data before we change anything
                Console.WriteLine("Initial Data:");
                DisplayBlogsAndPosts();

                // Add a new Blog and some Posts
                AddBlogAndPost();
                Console.WriteLine("After Adding:");
                DisplayBlogsAndPosts();

                // Modify the Blog and one of its Posts
                UpdateBlogAndPost();
                Console.WriteLine("After Update:");
                DisplayBlogsAndPosts();

                // Delete the Blog and its Posts
                DeleteBlogAndPost();
                Console.WriteLine("After Delete:");
                DisplayBlogsAndPosts();

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

            static void DisplayBlogsAndPosts()
            {
                using (var service = new Service1Client())
                {
                    // Get all Blogs (and Posts) from the service
                    // and print them to the console
                    var blogs = service.GetBlogs();
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(blog.Name);
                        foreach (var post in blog.Posts)
                        {
                            Console.WriteLine(" - {0}", post.Title);
                        }
                    }
                }

                Console.WriteLine();
                Console.WriteLine();
            }

            static void AddBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Create a new Blog with a couple of Posts
                    var newBlog = new Blog
                    {
                        Name = "The New Blog",
                        Posts =
                        {
                            new Post { Title = "Welcome to the new blog"},
                            new Post { Title = "What's new on the new blog"}
                        }
                    };

                    // Save the changes using the service
                    service.UpdateBlog(newBlog);
                }
            }

            static void UpdateBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Get all the Blogs
                    var blogs = service.GetBlogs();

                    // Use LINQ to Objects to find The New Blog
                    var blog = blogs.First(b => b.Name == "The New Blog");

                    // Update the Blogs name
                    blog.Name = "The Not-So-New Blog";

                    // Update one of the related posts
                    blog.Posts.First().Content = "Some interesting content...";

                    // Save the changes using the service
                    service.UpdateBlog(blog);
                }
            }

            static void DeleteBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Get all the Blogs
                    var blogs = service.GetBlogs();

                    // Use LINQ to Objects to find The Not-So-New Blog
                    var blog = blogs.First(b => b.Name == "The Not-So-New Blog");

                    // Mark all related Posts for deletion
                    // We need to call ToList because each Post will be removed from the
                    // Posts collection when we call MarkAsDeleted
                    foreach (var post in blog.Posts.ToList())
                    {
                        post.MarkAsDeleted();
                    }

                    // Mark the Blog for deletion
                    blog.MarkAsDeleted();

                    // Save the changes using the service
                    service.UpdateBlog(blog);
                }
            }
        }
    }
```

<span data-ttu-id="026e8-223">您現在可以執行應用程式來查看運作狀況。</span><span class="sxs-lookup"><span data-stu-id="026e8-223">You can now run the application to see it in action.</span></span>

-   <span data-ttu-id="026e8-224">以滑鼠右鍵按一下**STESample.ConsoleTest**專案中**方案總管**，然後選取**偵錯-&gt;開始新執行個體**</span><span class="sxs-lookup"><span data-stu-id="026e8-224">Right-click the **STESample.ConsoleTest** project in **Solution Explorer** and select **Debug -&gt; Start new instance**</span></span>

<span data-ttu-id="026e8-225">當應用程式執行時，您會看到下列輸出。</span><span class="sxs-lookup"><span data-stu-id="026e8-225">You'll see the following output when the application executes.</span></span>

```
Initial Data:
ADO.NET Blog
- Intro to EF
- What is New

After Adding:
ADO.NET Blog
- Intro to EF
- What is New
The New Blog
- Welcome to the new blog
- What's new on the new blog

After Update:
ADO.NET Blog
- Intro to EF
- What is New
The Not-So-New Blog
- Welcome to the new blog
- What's new on the new blog

After Delete:
ADO.NET Blog
- Intro to EF
- What is New

Press any key to exit...
```

## <a name="consume-the-service-from-a-wpf-application"></a><span data-ttu-id="026e8-226">使用 WPF 應用程式服務</span><span class="sxs-lookup"><span data-stu-id="026e8-226">Consume the Service from a WPF Application</span></span>

<span data-ttu-id="026e8-227">讓我們建立使用我們的服務的 WPF 應用程式。</span><span class="sxs-lookup"><span data-stu-id="026e8-227">Let's create a WPF application that uses our service.</span></span>

-   <span data-ttu-id="026e8-228">**檔案-&gt;新增-&gt;專案...**</span><span class="sxs-lookup"><span data-stu-id="026e8-228">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="026e8-229">選取  **Visual C\#** 從左窗格中，然後**WPF 應用程式**</span><span class="sxs-lookup"><span data-stu-id="026e8-229">Select **Visual C\#** from the left pane and then **WPF Application**</span></span>
-   <span data-ttu-id="026e8-230">請輸入**STESample.WPFTest**作為名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="026e8-230">Enter **STESample.WPFTest** as the name and click **OK**</span></span>
-   <span data-ttu-id="026e8-231">將參考加入**STESample.Entities**專案</span><span class="sxs-lookup"><span data-stu-id="026e8-231">Add a reference to the **STESample.Entities** project</span></span>

<span data-ttu-id="026e8-232">我們需要我們的 WCF 服務的服務參考</span><span class="sxs-lookup"><span data-stu-id="026e8-232">We need a service reference to our WCF service</span></span>

-   <span data-ttu-id="026e8-233">以滑鼠右鍵按一下**STESample.WPFTest**專案中**方案總管**，然後選取**加入服務參考...**</span><span class="sxs-lookup"><span data-stu-id="026e8-233">Right-click the **STESample.WPFTest** project in **Solution Explorer** and select **Add Service Reference...**</span></span>
-   <span data-ttu-id="026e8-234">按一下 **探索**</span><span class="sxs-lookup"><span data-stu-id="026e8-234">Click **Discover**</span></span>
-   <span data-ttu-id="026e8-235">請輸入**BloggingService**作為命名空間，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="026e8-235">Enter **BloggingService** as the namespace and click **OK**</span></span>

<span data-ttu-id="026e8-236">現在我們可以撰寫一些程式碼來取用服務。</span><span class="sxs-lookup"><span data-stu-id="026e8-236">Now we can write some code to consume the service.</span></span>

-   <span data-ttu-id="026e8-237">開啟**MainWindow.xaml**並取代為下列程式碼的內容。</span><span class="sxs-lookup"><span data-stu-id="026e8-237">Open **MainWindow.xaml** and replace the contents with the following code.</span></span>

``` xaml
    <Window
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:STESample="clr-namespace:STESample;assembly=STESample.Entities"
        mc:Ignorable="d" x:Class="STESample.WPFTest.MainWindow"
        Title="MainWindow" Height="350" Width="525" Loaded="Window_Loaded">

        <Window.Resources>
            <CollectionViewSource
                x:Key="blogViewSource"
                d:DesignSource="{d:DesignInstance {x:Type STESample:Blog}, CreateList=True}"/>
            <CollectionViewSource
                x:Key="blogPostsViewSource"
                Source="{Binding Posts, Source={StaticResource blogViewSource}}"/>
        </Window.Resources>

        <Grid DataContext="{StaticResource blogViewSource}">
            <DataGrid AutoGenerateColumns="False" EnableRowVirtualization="True"
                      ItemsSource="{Binding}" Margin="10,10,10,179">
                <DataGrid.Columns>
                    <DataGridTextColumn Binding="{Binding BlogId}" Header="Id" Width="Auto" IsReadOnly="True" />
                    <DataGridTextColumn Binding="{Binding Name}" Header="Name" Width="Auto"/>
                    <DataGridTextColumn Binding="{Binding Url}" Header="Url" Width="Auto"/>
                </DataGrid.Columns>
            </DataGrid>
            <DataGrid AutoGenerateColumns="False" EnableRowVirtualization="True"
                      ItemsSource="{Binding Source={StaticResource blogPostsViewSource}}" Margin="10,145,10,38">
                <DataGrid.Columns>
                    <DataGridTextColumn Binding="{Binding PostId}" Header="Id" Width="Auto"  IsReadOnly="True"/>
                    <DataGridTextColumn Binding="{Binding Title}" Header="Title" Width="Auto"/>
                    <DataGridTextColumn Binding="{Binding Content}" Header="Content" Width="Auto"/>
                </DataGrid.Columns>
            </DataGrid>
            <Button Width="68" Height="23" HorizontalAlignment="Right" VerticalAlignment="Bottom"
                    Margin="0,0,10,10" Click="buttonSave_Click">Save</Button>
        </Grid>
    </Window>
```

-   <span data-ttu-id="026e8-238">開啟 MainWindow 的程式碼後置 (**MainWindow.xaml.cs**)，並取代為下列程式碼的內容</span><span class="sxs-lookup"><span data-stu-id="026e8-238">Open the code behind for MainWindow (**MainWindow.xaml.cs**) and replace the contents with the following code</span></span>

``` csharp
    using STESample.WPFTest.BloggingService;
    using System.Collections.Generic;
    using System.Linq;
    using System.Windows;
    using System.Windows.Data;

    namespace STESample.WPFTest
    {
        public partial class MainWindow : Window
        {
            public MainWindow()
            {
                InitializeComponent();
            }

            private void Window_Loaded(object sender, RoutedEventArgs e)
            {
                using (var service = new Service1Client())
                {
                    // Find the view source for Blogs and populate it with all Blogs (and related Posts)
                    // from the Service. The default editing functionality of WPF will allow the objects
                    // to be manipulated on the screen.
                    var blogsViewSource = (CollectionViewSource)this.FindResource("blogViewSource");
                    blogsViewSource.Source = service.GetBlogs().ToList();
                }
            }

            private void buttonSave_Click(object sender, RoutedEventArgs e)
            {
                using (var service = new Service1Client())
                {
                    // Get the blogs that are bound to the screen
                    var blogsViewSource = (CollectionViewSource)this.FindResource("blogViewSource");
                    var blogs = (List<Blog>)blogsViewSource.Source;

                    // Save all Blogs and related Posts
                    foreach (var blog in blogs)
                    {
                        service.UpdateBlog(blog);
                    }

                    // Re-query for data to get database-generated keys etc.
                    blogsViewSource.Source = service.GetBlogs().ToList();
                }
            }
        }
    }
```

<span data-ttu-id="026e8-239">您現在可以執行應用程式來查看運作狀況。</span><span class="sxs-lookup"><span data-stu-id="026e8-239">You can now run the application to see it in action.</span></span>

-   <span data-ttu-id="026e8-240">以滑鼠右鍵按一下**STESample.WPFTest**專案中**方案總管**，然後選取**偵錯-&gt;開始新執行個體**</span><span class="sxs-lookup"><span data-stu-id="026e8-240">Right-click the **STESample.WPFTest** project in **Solution Explorer** and select **Debug -&gt; Start new instance**</span></span>
-   <span data-ttu-id="026e8-241">您可以管理使用螢幕的資料，並將它儲存服務會使用透過**儲存**按鈕</span><span class="sxs-lookup"><span data-stu-id="026e8-241">You can manipulate the data using the screen and save it via the service using the **Save** button</span></span>

![WPF](~/ef6/media/wpf.png)
