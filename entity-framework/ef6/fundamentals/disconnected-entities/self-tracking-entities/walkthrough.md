---
title: 自我追蹤實體逐步解說-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: b21207c9-1d95-4aa3-ae05-bc5fe300dab0
ms.openlocfilehash: 9bd644461f50a7eff1006cb8866ca9a3b08b6b8d
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181706"
---
# <a name="self-tracking-entities-walkthrough"></a><span data-ttu-id="cf53e-102">自我追蹤實體逐步解說</span><span class="sxs-lookup"><span data-stu-id="cf53e-102">Self-Tracking Entities Walkthrough</span></span>
> [!IMPORTANT]
> <span data-ttu-id="cf53e-103">我們不再建議使用自我追蹤實體範本。</span><span class="sxs-lookup"><span data-stu-id="cf53e-103">We no longer recommend using the self-tracking-entities template.</span></span> <span data-ttu-id="cf53e-104">繼續提供該範本只是為了支援現有應用程式。</span><span class="sxs-lookup"><span data-stu-id="cf53e-104">It will only continue to be available to support existing applications.</span></span> <span data-ttu-id="cf53e-105">如果您的應用程式需要使用已中斷連線的實體圖形，請考慮使用 [Trackable Entities](https://trackableentities.github.io/) (可追蹤的實體) 之類的其他替代項目，這是一項類似於自我追蹤實體的技術，可由社群更積極地進行開發或使用低層級變更追蹤 API 來撰寫自訂程式碼。</span><span class="sxs-lookup"><span data-stu-id="cf53e-105">If your application requires working with disconnected graphs of entities, consider other alternatives such as [Trackable Entities](https://trackableentities.github.io/), which is a technology similar to Self-Tracking-Entities that is more actively developed by the community, or writing custom code using the low-level change tracking APIs.</span></span>

<span data-ttu-id="cf53e-106">本逐步解說示範 Windows Communication Foundation （WCF）服務公開傳回實體圖形之作業的案例。</span><span class="sxs-lookup"><span data-stu-id="cf53e-106">This walkthrough demonstrates the scenario in which a Windows Communication Foundation (WCF) service exposes an operation that returns an entity graph.</span></span> <span data-ttu-id="cf53e-107">接下來，用戶端應用程式會操作該圖形，並將修改提交至服務作業，以使用 Entity Framework 來驗證和儲存更新至資料庫。</span><span class="sxs-lookup"><span data-stu-id="cf53e-107">Next, a client application manipulates that graph and submits the modifications to a service operation that validates and saves the updates to a database using Entity Framework.</span></span>

<span data-ttu-id="cf53e-108">完成本逐步解說之前，請確定您已閱讀[自我追蹤實體](index.md)頁面。</span><span class="sxs-lookup"><span data-stu-id="cf53e-108">Before completing this walkthrough make sure you read the [Self-Tracking Entities](index.md) page.</span></span>

<span data-ttu-id="cf53e-109">這個逐步解說會完成下列動作：</span><span class="sxs-lookup"><span data-stu-id="cf53e-109">This walkthrough completes the following actions:</span></span>

-   <span data-ttu-id="cf53e-110">建立要存取的資料庫。</span><span class="sxs-lookup"><span data-stu-id="cf53e-110">Creates a database to access.</span></span>
-   <span data-ttu-id="cf53e-111">建立包含模型的類別庫。</span><span class="sxs-lookup"><span data-stu-id="cf53e-111">Creates a class library that contains the model.</span></span>
-   <span data-ttu-id="cf53e-112">交換至自我追蹤實體產生器範本。</span><span class="sxs-lookup"><span data-stu-id="cf53e-112">Swaps to the Self-Tracking Entity Generator template.</span></span>
-   <span data-ttu-id="cf53e-113">將實體類別移至不同的專案。</span><span class="sxs-lookup"><span data-stu-id="cf53e-113">Moves the entity classes to a separate project.</span></span>
-   <span data-ttu-id="cf53e-114">建立 WCF 服務，以公開作業來查詢和儲存實體。</span><span class="sxs-lookup"><span data-stu-id="cf53e-114">Creates a WCF service that exposes operations to query and save entities.</span></span>
-   <span data-ttu-id="cf53e-115">建立使用服務的用戶端應用程式（主控台和 WPF）。</span><span class="sxs-lookup"><span data-stu-id="cf53e-115">Creates client applications (Console and WPF) that consume the service.</span></span>

<span data-ttu-id="cf53e-116">我們將在本逐步解說中使用 Database First，但相同的技術同樣適用于 Model First。</span><span class="sxs-lookup"><span data-stu-id="cf53e-116">We'll use Database First in this walkthrough but the same techniques apply equally to Model First.</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="cf53e-117">先決條件</span><span class="sxs-lookup"><span data-stu-id="cf53e-117">Pre-Requisites</span></span>

<span data-ttu-id="cf53e-118">若要完成此逐步解說，您將需要最新版本的 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="cf53e-118">To complete this walkthrough you will need a recent version of Visual Studio.</span></span>

## <a name="create-a-database"></a><span data-ttu-id="cf53e-119">建立資料庫</span><span class="sxs-lookup"><span data-stu-id="cf53e-119">Create a Database</span></span>

<span data-ttu-id="cf53e-120">隨 Visual Studio 安裝的資料庫伺服器會根據您安裝的 Visual Studio 版本而有所不同：</span><span class="sxs-lookup"><span data-stu-id="cf53e-120">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="cf53e-121">如果您使用 Visual Studio 2012，則您將會建立 LocalDB 資料庫。</span><span class="sxs-lookup"><span data-stu-id="cf53e-121">If you are using Visual Studio 2012 then you'll be creating a LocalDB database.</span></span>
-   <span data-ttu-id="cf53e-122">如果您使用 Visual Studio 2010，您將會建立 SQL Express 資料庫。</span><span class="sxs-lookup"><span data-stu-id="cf53e-122">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>

<span data-ttu-id="cf53e-123">讓我們繼續產生資料庫。</span><span class="sxs-lookup"><span data-stu-id="cf53e-123">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="cf53e-124">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cf53e-124">Open Visual Studio</span></span>
-   <span data-ttu-id="cf53e-125">**View&gt; 伺服器總管**</span><span class="sxs-lookup"><span data-stu-id="cf53e-125">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="cf53e-126">以滑鼠右鍵按一下 **資料連線-&gt; 新增連接 ...**</span><span class="sxs-lookup"><span data-stu-id="cf53e-126">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="cf53e-127">如果您還沒有從伺服器總管連接到資料庫，則必須選取 [ **Microsoft SQL Server** ] 做為資料來源</span><span class="sxs-lookup"><span data-stu-id="cf53e-127">If you haven’t connected to a database from Server Explorer before you’ll need to select **Microsoft SQL Server** as the data source</span></span>
-   <span data-ttu-id="cf53e-128">連接到 LocalDB 或 SQL Express （視您安裝的版本而定）</span><span class="sxs-lookup"><span data-stu-id="cf53e-128">Connect to either LocalDB or SQL Express, depending on which one you have installed</span></span>
-   <span data-ttu-id="cf53e-129">輸入**STESample**做為資料庫名稱</span><span class="sxs-lookup"><span data-stu-id="cf53e-129">Enter **STESample** as the database name</span></span>
-   <span data-ttu-id="cf53e-130">選取 **[確定]** ，系統會詢問您是否要建立新的資料庫，然後選取 **[是]**</span><span class="sxs-lookup"><span data-stu-id="cf53e-130">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>
-   <span data-ttu-id="cf53e-131">新的資料庫現在會顯示在伺服器總管</span><span class="sxs-lookup"><span data-stu-id="cf53e-131">The new database will now appear in Server Explorer</span></span>
-   <span data-ttu-id="cf53e-132">如果您使用 Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="cf53e-132">If you are using Visual Studio 2012</span></span>
    -   <span data-ttu-id="cf53e-133">以滑鼠右鍵按一下伺服器總管中的資料庫，然後選取 [追加**查詢**]</span><span class="sxs-lookup"><span data-stu-id="cf53e-133">Right-click on the database in Server Explorer and select **New Query**</span></span>
    -   <span data-ttu-id="cf53e-134">將下列 SQL 複製到新的查詢中，然後以滑鼠右鍵按一下查詢並選取 [**執行**]。</span><span class="sxs-lookup"><span data-stu-id="cf53e-134">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>
-   <span data-ttu-id="cf53e-135">如果您使用 Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="cf53e-135">If you are using Visual Studio 2010</span></span>
    -   <span data-ttu-id="cf53e-136">選取**資料&gt; Transact-sql 編輯器-&gt; 新的查詢連接**。</span><span class="sxs-lookup"><span data-stu-id="cf53e-136">Select **Data -&gt; Transact SQL Editor -&gt; New Query Connection...**</span></span>
    -   <span data-ttu-id="cf53e-137">輸入 **。\\SQLEXPRESS**做為伺服器名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="cf53e-137">Enter **.\\SQLEXPRESS** as the server name and click **OK**</span></span>
    -   <span data-ttu-id="cf53e-138">從 [查詢編輯器] 頂端的下拉式選中選取 [ **STESample** ] 資料庫</span><span class="sxs-lookup"><span data-stu-id="cf53e-138">Select the **STESample** database from the drop down at the top of the query editor</span></span>
    -   <span data-ttu-id="cf53e-139">將下列 SQL 複製到新的查詢中，然後以滑鼠右鍵按一下查詢並選取 [**執行 SQL** ]。</span><span class="sxs-lookup"><span data-stu-id="cf53e-139">Copy the following SQL into the new query, then right-click on the query and select **Execute SQL**</span></span>

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

## <a name="create-the-model"></a><span data-ttu-id="cf53e-140">建立模型</span><span class="sxs-lookup"><span data-stu-id="cf53e-140">Create the Model</span></span>

<span data-ttu-id="cf53e-141">首先，我們需要一個專案來放置模型。</span><span class="sxs-lookup"><span data-stu-id="cf53e-141">First up, we need a project to put the model in.</span></span>

-   <span data-ttu-id="cf53e-142">**檔案&gt; 新&gt; 專案 。**</span><span class="sxs-lookup"><span data-stu-id="cf53e-142">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="cf53e-143">從左窗格中選取 [ **Visual C\#** ]，然後按一下 [**類別庫**]</span><span class="sxs-lookup"><span data-stu-id="cf53e-143">Select **Visual C\#** from the left pane and then **Class Library**</span></span>
-   <span data-ttu-id="cf53e-144">輸入**STESample**作為名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="cf53e-144">Enter **STESample** as the name and click **OK**</span></span>

<span data-ttu-id="cf53e-145">現在，我們將在 EF 設計工具中建立簡單的模型來存取我們的資料庫：</span><span class="sxs-lookup"><span data-stu-id="cf53e-145">Now we'll create a simple model in the EF Designer to access our database:</span></span>

-   <span data-ttu-id="cf53e-146">**專案-&gt; 加入新專案 。**</span><span class="sxs-lookup"><span data-stu-id="cf53e-146">**Project -&gt; Add New Item...**</span></span>
-   <span data-ttu-id="cf53e-147">從左窗格中選取 [**資料**]，然後**ADO.NET 實體資料模型**</span><span class="sxs-lookup"><span data-stu-id="cf53e-147">Select **Data** from the left pane and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="cf53e-148">輸入**BloggingModel**作為名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="cf53e-148">Enter **BloggingModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="cf53e-149">選取 [**從資料庫產生**]，然後按 **[下一步]**</span><span class="sxs-lookup"><span data-stu-id="cf53e-149">Select **Generate from database** and click **Next**</span></span>
-   <span data-ttu-id="cf53e-150">輸入您在上一節中建立之資料庫的連接資訊</span><span class="sxs-lookup"><span data-stu-id="cf53e-150">Enter the connection information for the database that you created in the previous section</span></span>
-   <span data-ttu-id="cf53e-151">輸入**BloggingCoNtext**做為連接字串的名稱，然後按 **[下一步]**</span><span class="sxs-lookup"><span data-stu-id="cf53e-151">Enter **BloggingContext** as the name for the connection string and click **Next**</span></span>
-   <span data-ttu-id="cf53e-152">勾選 [**資料表**] 旁的方塊，然後按一下 **[完成]**</span><span class="sxs-lookup"><span data-stu-id="cf53e-152">Check the box next to **Tables** and click **Finish**</span></span>

## <a name="swap-to-ste-code-generation"></a><span data-ttu-id="cf53e-153">交換至 STE 程式碼產生</span><span class="sxs-lookup"><span data-stu-id="cf53e-153">Swap to STE Code Generation</span></span>

<span data-ttu-id="cf53e-154">現在，我們需要停用預設的程式碼產生，並交換至自我追蹤實體。</span><span class="sxs-lookup"><span data-stu-id="cf53e-154">Now we need to disable the default code generation and swap to Self-Tracking Entities.</span></span>

### <a name="if-you-are-using-visual-studio-2012"></a><span data-ttu-id="cf53e-155">如果您使用 Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="cf53e-155">If you are using Visual Studio 2012</span></span>

-   <span data-ttu-id="cf53e-156">在**方案總管**中展開**BloggingModel** ，然後刪除**BloggingModel.tt**和**BloggingModel.CoNtext.tt**
    *這會停用預設的程式碼產生*</span><span class="sxs-lookup"><span data-stu-id="cf53e-156">Expand **BloggingModel.edmx** in **Solution Explorer** and delete the **BloggingModel.tt** and **BloggingModel.Context.tt**
*This will disable the default code generation*</span></span>
-   <span data-ttu-id="cf53e-157">以滑鼠右鍵按一下 EF 設計工具介面上的空白區域，然後選取 [**新增程式碼產生專案**]。</span><span class="sxs-lookup"><span data-stu-id="cf53e-157">Right-click an empty area on the EF Designer surface and select **Add Code Generation Item...**</span></span>
-   <span data-ttu-id="cf53e-158">從左窗格中選取 [**線上**]，然後搜尋 [ **STE**產生器]</span><span class="sxs-lookup"><span data-stu-id="cf53e-158">Select **Online** from the left pane and search for **STE Generator**</span></span>
-   <span data-ttu-id="cf53e-159">選取**適用于 C\#** 範本的 STE 產生器，並輸入**STETemplate**作為名稱，然後按一下 [**新增**]</span><span class="sxs-lookup"><span data-stu-id="cf53e-159">Select the **STE Generator for C\#** template, enter **STETemplate** as the name and click **Add**</span></span>
-   <span data-ttu-id="cf53e-160">**STETemplate.tt**和**STETemplate.CoNtext.tt**檔案會新增至 BloggingModel 的 .edmx 檔案底下</span><span class="sxs-lookup"><span data-stu-id="cf53e-160">The **STETemplate.tt** and **STETemplate.Context.tt** files are added nested under the BloggingModel.edmx file</span></span>

### <a name="if-you-are-using-visual-studio-2010"></a><span data-ttu-id="cf53e-161">如果您使用 Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="cf53e-161">If you are using Visual Studio 2010</span></span>

-   <span data-ttu-id="cf53e-162">以滑鼠右鍵按一下 EF 設計工具介面上的空白區域，然後選取 [**新增程式碼產生專案**]。</span><span class="sxs-lookup"><span data-stu-id="cf53e-162">Right-click an empty area on the EF Designer surface and select **Add Code Generation Item...**</span></span>
-   <span data-ttu-id="cf53e-163">從左窗格中選取 [程式**代碼**]，然後**ADO.NET 自我追蹤實體**產生器</span><span class="sxs-lookup"><span data-stu-id="cf53e-163">Select **Code** from the left pane and then **ADO.NET Self-Tracking Entity Generator**</span></span>
-   <span data-ttu-id="cf53e-164">輸入**STETemplate**作為名稱，然後按一下 [**新增**]</span><span class="sxs-lookup"><span data-stu-id="cf53e-164">Enter **STETemplate** as the name and click **Add**</span></span>
-   <span data-ttu-id="cf53e-165">**STETemplate.tt**和**STETemplate.CoNtext.tt**檔案會直接新增至您的專案</span><span class="sxs-lookup"><span data-stu-id="cf53e-165">The **STETemplate.tt** and **STETemplate.Context.tt** files are added directly to your project</span></span>

## <a name="move-entity-types-into-separate-project"></a><span data-ttu-id="cf53e-166">將實體類型移至不同的專案</span><span class="sxs-lookup"><span data-stu-id="cf53e-166">Move Entity Types into Separate Project</span></span>

<span data-ttu-id="cf53e-167">若要使用自我追蹤實體，我們的用戶端應用程式需要存取從我們的模型產生的實體類別。</span><span class="sxs-lookup"><span data-stu-id="cf53e-167">To use Self-Tracking Entities our client application needs access to the entity classes generated from our model.</span></span> <span data-ttu-id="cf53e-168">因為我們不想要將整個模型公開給用戶端應用程式，所以我們會將實體類別移至不同的專案。</span><span class="sxs-lookup"><span data-stu-id="cf53e-168">Because we don't want to expose the whole model to the client application we're going to move the entity classes into a separate project.</span></span>

<span data-ttu-id="cf53e-169">第一個步驟是停止在現有的專案中產生實體類別：</span><span class="sxs-lookup"><span data-stu-id="cf53e-169">The first step is to stop generating entity classes in the existing project:</span></span>

-   <span data-ttu-id="cf53e-170">以滑鼠右鍵按一下**方案總管**中的 [ **STETemplate.tt** ]，然後選取 [**屬性**]</span><span class="sxs-lookup"><span data-stu-id="cf53e-170">Right-click on **STETemplate.tt** in **Solution Explorer** and select **Properties**</span></span>
-   <span data-ttu-id="cf53e-171">在 [**屬性**] 視窗中，從**CustomTool**屬性清除**TextTemplatingFileGenerator**</span><span class="sxs-lookup"><span data-stu-id="cf53e-171">In the **Properties** window clear **TextTemplatingFileGenerator** from the **CustomTool** property</span></span>
-   <span data-ttu-id="cf53e-172">展開**方案總管**中的**STETemplate.tt** ，並刪除其底下的所有檔案</span><span class="sxs-lookup"><span data-stu-id="cf53e-172">Expand **STETemplate.tt** in **Solution Explorer** and delete all files nested under it</span></span>

<span data-ttu-id="cf53e-173">接下來，我們要加入新的專案，並在其中產生實體類別</span><span class="sxs-lookup"><span data-stu-id="cf53e-173">Next, we are going to add a new project and generate the entity classes in it</span></span>

-   <span data-ttu-id="cf53e-174">**檔案&gt; 的載入&gt; 專案 。**</span><span class="sxs-lookup"><span data-stu-id="cf53e-174">**File -&gt; Add -&gt; Project...**</span></span>
-   <span data-ttu-id="cf53e-175">從左窗格中選取 [ **Visual C\#** ]，然後按一下 [**類別庫**]</span><span class="sxs-lookup"><span data-stu-id="cf53e-175">Select **Visual C\#** from the left pane and then **Class Library**</span></span>
-   <span data-ttu-id="cf53e-176">輸入**STESample**作為名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="cf53e-176">Enter **STESample.Entities** as the name and click **OK**</span></span>
-   <span data-ttu-id="cf53e-177">**專案-&gt; 加入現有專案 。**</span><span class="sxs-lookup"><span data-stu-id="cf53e-177">**Project -&gt; Add Existing Item...**</span></span>
-   <span data-ttu-id="cf53e-178">流覽至**STESample**專案資料夾</span><span class="sxs-lookup"><span data-stu-id="cf53e-178">Navigate to the **STESample** project folder</span></span>
-   <span data-ttu-id="cf53e-179">選取以查看**所有檔案（\*.\*）**</span><span class="sxs-lookup"><span data-stu-id="cf53e-179">Select to view **All Files (\*.\*)**</span></span>
-   <span data-ttu-id="cf53e-180">選取**STETemplate.tt**檔案</span><span class="sxs-lookup"><span data-stu-id="cf53e-180">Select the **STETemplate.tt** file</span></span>
-   <span data-ttu-id="cf53e-181">按一下 [**新增**] 按鈕旁邊的下拉式箭號，然後選取 [**新增為連結**]。</span><span class="sxs-lookup"><span data-stu-id="cf53e-181">Click on the drop down arrow next to the **Add** button and select **Add As Link**</span></span>

    ![加入連結的範本](~/ef6/media/addlinkedtemplate.png)

<span data-ttu-id="cf53e-183">我們也要確保實體類別產生于與內容相同的命名空間中。</span><span class="sxs-lookup"><span data-stu-id="cf53e-183">We're also going to make sure the entity classes get generated in the same namespace as the context.</span></span> <span data-ttu-id="cf53e-184">這只會減少我們需要在整個應用程式中加入的 using 語句數目。</span><span class="sxs-lookup"><span data-stu-id="cf53e-184">This just reduces the number of using statements we need to add throughout our application.</span></span>

-   <span data-ttu-id="cf53e-185">以滑鼠右鍵按一下**方案總管**中的連結的**STETemplate.tt** ，然後選取 [**屬性**]</span><span class="sxs-lookup"><span data-stu-id="cf53e-185">Right-click on the linked **STETemplate.tt** in **Solution Explorer** and select **Properties**</span></span>
-   <span data-ttu-id="cf53e-186">在 [**屬性**] 視窗中，將**自訂工具命名空間**設為**STESample**</span><span class="sxs-lookup"><span data-stu-id="cf53e-186">In the **Properties** window set **Custom Tool Namespace** to **STESample**</span></span>

<span data-ttu-id="cf53e-187">STE 範本所產生的程式碼**需要對 system.string**的參考，才能進行編譯。</span><span class="sxs-lookup"><span data-stu-id="cf53e-187">The code generated by the STE template will need a reference to **System.Runtime.Serialization** in order to compile.</span></span> <span data-ttu-id="cf53e-188">在可序列化的實體類型上使用的 WCF **DataContract**和**DataMember**屬性需要此程式庫。</span><span class="sxs-lookup"><span data-stu-id="cf53e-188">This library is needed for the WCF **DataContract** and **DataMember** attributes that are used on the serializable entity types.</span></span>

-   <span data-ttu-id="cf53e-189">以滑鼠右鍵按一下**方案總管**中的 [ **STESample** ] 專案，然後選取 [**新增參考 ...** ]。</span><span class="sxs-lookup"><span data-stu-id="cf53e-189">Right click on the **STESample.Entities** project in **Solution Explorer** and select **Add Reference...**</span></span>
    -   <span data-ttu-id="cf53e-190">在 Visual Studio 2012-核取 [System.object] 旁的方塊，然後按一下 **[確定]** **。**</span><span class="sxs-lookup"><span data-stu-id="cf53e-190">In Visual Studio 2012 - check the box next to **System.Runtime.Serialization** and click **OK**</span></span>
    -   <span data-ttu-id="cf53e-191">在 Visual Studio 2010-選取 [ **System.web** ]，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="cf53e-191">In Visual Studio 2010 - select **System.Runtime.Serialization** and click **OK**</span></span>

<span data-ttu-id="cf53e-192">最後，含有其內容的專案將需要實體類型的參考。</span><span class="sxs-lookup"><span data-stu-id="cf53e-192">Finally, the project with our context in it will need a reference to the entity types.</span></span>

-   <span data-ttu-id="cf53e-193">以滑鼠右鍵按一下**方案總管**中的**STESample**專案，然後選取 [**新增參考 ...** ]。</span><span class="sxs-lookup"><span data-stu-id="cf53e-193">Right click on the **STESample** project in **Solution Explorer** and select **Add Reference...**</span></span>
    -   <span data-ttu-id="cf53e-194">在 Visual Studio 2012-從左窗格中選取 [**方案**]，勾選 [STESample] 旁的方塊，然後按一下 **[確定]** **。**</span><span class="sxs-lookup"><span data-stu-id="cf53e-194">In Visual Studio 2012 - select **Solution** from the left pane, check the box next to **STESample.Entities** and click **OK**</span></span>
    -   <span data-ttu-id="cf53e-195">在 Visual Studio 2010-選取 [**專案**] 索引標籤，選取 [STESample]，然後按一下 **[確定]** **。**</span><span class="sxs-lookup"><span data-stu-id="cf53e-195">In Visual Studio 2010 - select the **Projects** tab, select **STESample.Entities** and click **OK**</span></span>

>[!NOTE]
> <span data-ttu-id="cf53e-196">將實體類型移至個別專案的另一個選項是移動範本檔案，而不是從其預設位置連結。</span><span class="sxs-lookup"><span data-stu-id="cf53e-196">Another option for moving the entity types to a separate project is to move the template file, rather than linking it from its default location.</span></span> <span data-ttu-id="cf53e-197">如果您這樣做，您將需要更新範本中的**inputFile**變數，以提供 .edmx 檔案的相對路徑（在此範例中為 **.\\BloggingModel .edmx**）。</span><span class="sxs-lookup"><span data-stu-id="cf53e-197">If you do this, you will need to update the **inputFile** variable in the template to provide the relative path to the edmx file (in this example that would be **..\\BloggingModel.edmx**).</span></span>

## <a name="create-a-wcf-service"></a><span data-ttu-id="cf53e-198">建立 WCF 服務</span><span class="sxs-lookup"><span data-stu-id="cf53e-198">Create a WCF Service</span></span>

<span data-ttu-id="cf53e-199">現在可以加入 WCF 服務來公開我們的資料，我們將從建立專案開始。</span><span class="sxs-lookup"><span data-stu-id="cf53e-199">Now it's time to add a WCF Service to expose our data, we'll start by creating the project.</span></span>

-   <span data-ttu-id="cf53e-200">**檔案&gt; 的載入&gt; 專案 。**</span><span class="sxs-lookup"><span data-stu-id="cf53e-200">**File -&gt; Add -&gt; Project...**</span></span>
-   <span data-ttu-id="cf53e-201">從左窗格中選取 [ **Visual C\#** ]，然後從 [ **WCF 服務應用程式**]</span><span class="sxs-lookup"><span data-stu-id="cf53e-201">Select **Visual C\#** from the left pane and then **WCF Service Application**</span></span>
-   <span data-ttu-id="cf53e-202">輸入**STESample**做為名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="cf53e-202">Enter **STESample.Service** as the name and click **OK**</span></span>
-   <span data-ttu-id="cf53e-203">加入**system.object**元件的參考</span><span class="sxs-lookup"><span data-stu-id="cf53e-203">Add a reference to the **System.Data.Entity** assembly</span></span>
-   <span data-ttu-id="cf53e-204">加入**STESample**和**STESample 實體**專案的參考</span><span class="sxs-lookup"><span data-stu-id="cf53e-204">Add a reference to the **STESample** and **STESample.Entities** projects</span></span>

<span data-ttu-id="cf53e-205">我們需要將 EF 連接字串複製到這個專案，以便在執行時間中找到它。</span><span class="sxs-lookup"><span data-stu-id="cf53e-205">We need to copy the EF connection string to this project so that it is found at runtime.</span></span>

-   <span data-ttu-id="cf53e-206">開啟 **STESample **專案的**app.config**檔案，並複製**connectionStrings**元素</span><span class="sxs-lookup"><span data-stu-id="cf53e-206">Open the **App.Config** file for the **STESample **project and copy the **connectionStrings** element</span></span>
-   <span data-ttu-id="cf53e-207">在**STESample**專案中，貼上**connectionStrings**元素做為**web.config**檔案之**configuration**元素的子項目。</span><span class="sxs-lookup"><span data-stu-id="cf53e-207">Paste the **connectionStrings** element as a child element of the **configuration** element of the **Web.Config** file in the **STESample.Service** project</span></span>

<span data-ttu-id="cf53e-208">現在可以開始執行實際的服務。</span><span class="sxs-lookup"><span data-stu-id="cf53e-208">Now it's time to implement the actual service.</span></span>

-   <span data-ttu-id="cf53e-209">開啟**IService1.cs** ，並以下列程式碼取代內容</span><span class="sxs-lookup"><span data-stu-id="cf53e-209">Open **IService1.cs** and replace the contents with the following code</span></span>

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

-   <span data-ttu-id="cf53e-210">開啟**Service1** ，並以下列程式碼取代內容</span><span class="sxs-lookup"><span data-stu-id="cf53e-210">Open **Service1.svc** and replace the contents with the following code</span></span>

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

## <a name="consume-the-service-from-a-console-application"></a><span data-ttu-id="cf53e-211">從主控台應用程式取用服務</span><span class="sxs-lookup"><span data-stu-id="cf53e-211">Consume the Service from a Console Application</span></span>

<span data-ttu-id="cf53e-212">讓我們建立一個使用我們服務的主控台應用程式。</span><span class="sxs-lookup"><span data-stu-id="cf53e-212">Let's create a console application that uses our service.</span></span>

-   <span data-ttu-id="cf53e-213">**檔案&gt; 新&gt; 專案 。**</span><span class="sxs-lookup"><span data-stu-id="cf53e-213">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="cf53e-214">從左窗格中選取 [ **Visual C\#** ]，然後從 [**主控台應用程式**]</span><span class="sxs-lookup"><span data-stu-id="cf53e-214">Select **Visual C\#** from the left pane and then **Console Application**</span></span>
-   <span data-ttu-id="cf53e-215">輸入**STESample. ConsoleTest**作為名稱，然後按一下 **[確定]** 。</span><span class="sxs-lookup"><span data-stu-id="cf53e-215">Enter **STESample.ConsoleTest** as the name and click **OK**</span></span>
-   <span data-ttu-id="cf53e-216">加入**STESample**專案的參考</span><span class="sxs-lookup"><span data-stu-id="cf53e-216">Add a reference to the **STESample.Entities** project</span></span>

<span data-ttu-id="cf53e-217">我們需要 WCF 服務的服務參考</span><span class="sxs-lookup"><span data-stu-id="cf53e-217">We need a service reference to our WCF service</span></span>

-   <span data-ttu-id="cf53e-218">以滑鼠右鍵按一下**方案總管**中的 [ **ConsoleTest** ] 專案，然後選取 [**加入服務參考**]。</span><span class="sxs-lookup"><span data-stu-id="cf53e-218">Right-click the **STESample.ConsoleTest** project in **Solution Explorer** and select **Add Service Reference...**</span></span>
-   <span data-ttu-id="cf53e-219">按一下 [**探索**]</span><span class="sxs-lookup"><span data-stu-id="cf53e-219">Click **Discover**</span></span>
-   <span data-ttu-id="cf53e-220">輸入**BloggingService**作為命名空間，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="cf53e-220">Enter **BloggingService** as the namespace and click **OK**</span></span>

<span data-ttu-id="cf53e-221">現在我們可以撰寫一些程式碼來取用服務。</span><span class="sxs-lookup"><span data-stu-id="cf53e-221">Now we can write some code to consume the service.</span></span>

-   <span data-ttu-id="cf53e-222">開啟**Program.cs** ，並將內容取代為下列程式碼。</span><span class="sxs-lookup"><span data-stu-id="cf53e-222">Open **Program.cs** and replace the contents with the following code.</span></span>

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

<span data-ttu-id="cf53e-223">您現在可以執行應用程式來查看運作狀況。</span><span class="sxs-lookup"><span data-stu-id="cf53e-223">You can now run the application to see it in action.</span></span>

-   <span data-ttu-id="cf53e-224">以滑鼠右鍵按一下**方案總管**中的 [ **ConsoleTest** ] 專案，然後選取 [ **Debug-&gt; 開始新實例**]</span><span class="sxs-lookup"><span data-stu-id="cf53e-224">Right-click the **STESample.ConsoleTest** project in **Solution Explorer** and select **Debug -&gt; Start new instance**</span></span>

<span data-ttu-id="cf53e-225">當應用程式執行時，您會看到下列輸出。</span><span class="sxs-lookup"><span data-stu-id="cf53e-225">You'll see the following output when the application executes.</span></span>

```console
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

## <a name="consume-the-service-from-a-wpf-application"></a><span data-ttu-id="cf53e-226">從 WPF 應用程式取用服務</span><span class="sxs-lookup"><span data-stu-id="cf53e-226">Consume the Service from a WPF Application</span></span>

<span data-ttu-id="cf53e-227">讓我們建立使用我們服務的 WPF 應用程式。</span><span class="sxs-lookup"><span data-stu-id="cf53e-227">Let's create a WPF application that uses our service.</span></span>

-   <span data-ttu-id="cf53e-228">**檔案&gt; 新&gt; 專案 。**</span><span class="sxs-lookup"><span data-stu-id="cf53e-228">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="cf53e-229">從左窗格中選取 [ **Visual C\#** ]，然後按一下 [ **WPF 應用程式**]</span><span class="sxs-lookup"><span data-stu-id="cf53e-229">Select **Visual C\#** from the left pane and then **WPF Application**</span></span>
-   <span data-ttu-id="cf53e-230">輸入**STESample. WPFTest**作為名稱，然後按一下 **[確定]** 。</span><span class="sxs-lookup"><span data-stu-id="cf53e-230">Enter **STESample.WPFTest** as the name and click **OK**</span></span>
-   <span data-ttu-id="cf53e-231">加入**STESample**專案的參考</span><span class="sxs-lookup"><span data-stu-id="cf53e-231">Add a reference to the **STESample.Entities** project</span></span>

<span data-ttu-id="cf53e-232">我們需要 WCF 服務的服務參考</span><span class="sxs-lookup"><span data-stu-id="cf53e-232">We need a service reference to our WCF service</span></span>

-   <span data-ttu-id="cf53e-233">以滑鼠右鍵按一下**方案總管**中的 [ **WPFTest** ] 專案，然後選取 [**加入服務參考**]。</span><span class="sxs-lookup"><span data-stu-id="cf53e-233">Right-click the **STESample.WPFTest** project in **Solution Explorer** and select **Add Service Reference...**</span></span>
-   <span data-ttu-id="cf53e-234">按一下 [**探索**]</span><span class="sxs-lookup"><span data-stu-id="cf53e-234">Click **Discover**</span></span>
-   <span data-ttu-id="cf53e-235">輸入**BloggingService**作為命名空間，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="cf53e-235">Enter **BloggingService** as the namespace and click **OK**</span></span>

<span data-ttu-id="cf53e-236">現在我們可以撰寫一些程式碼來取用服務。</span><span class="sxs-lookup"><span data-stu-id="cf53e-236">Now we can write some code to consume the service.</span></span>

-   <span data-ttu-id="cf53e-237">開啟**mainwindow.xaml** ，並將內容取代為下列程式碼。</span><span class="sxs-lookup"><span data-stu-id="cf53e-237">Open **MainWindow.xaml** and replace the contents with the following code.</span></span>

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

-   <span data-ttu-id="cf53e-238">開啟 Mainwindow.xaml （**MainWindow.xaml.cs**）的程式碼後置，並以下列程式碼取代內容</span><span class="sxs-lookup"><span data-stu-id="cf53e-238">Open the code behind for MainWindow (**MainWindow.xaml.cs**) and replace the contents with the following code</span></span>

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

<span data-ttu-id="cf53e-239">您現在可以執行應用程式來查看運作狀況。</span><span class="sxs-lookup"><span data-stu-id="cf53e-239">You can now run the application to see it in action.</span></span>

-   <span data-ttu-id="cf53e-240">以滑鼠右鍵按一下**方案總管**中的 [ **WPFTest** ] 專案，然後選取 [ **Debug-&gt; 開始新實例**]</span><span class="sxs-lookup"><span data-stu-id="cf53e-240">Right-click the **STESample.WPFTest** project in **Solution Explorer** and select **Debug -&gt; Start new instance**</span></span>
-   <span data-ttu-id="cf53e-241">您可以使用畫面來運算元據，並使用 [**儲存**] 按鈕透過服務加以儲存</span><span class="sxs-lookup"><span data-stu-id="cf53e-241">You can manipulate the data using the screen and save it via the service using the **Save** button</span></span>

![WPF 主視窗](~/ef6/media/wpf.png)
