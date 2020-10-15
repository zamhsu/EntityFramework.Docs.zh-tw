---
title: Model First-EF6
description: Entity Framework 6 中的 Model First
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/workflows/model-first
ms.openlocfilehash: b47c5805a63a1ea94baad4fb5eb137b633f6be1d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066027"
---
# <a name="model-first"></a><span data-ttu-id="25aba-103">Model First</span><span class="sxs-lookup"><span data-stu-id="25aba-103">Model First</span></span>
<span data-ttu-id="25aba-104">這段影片和逐步解說將提供使用 Entity Framework Model First 開發的簡介。</span><span class="sxs-lookup"><span data-stu-id="25aba-104">This video and step-by-step walkthrough provide an introduction to Model First development using Entity Framework.</span></span> <span data-ttu-id="25aba-105">Model First 可讓您使用 Entity Framework Designer 建立新的模型，然後從模型產生資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="25aba-105">Model First allows you to create a new model using the Entity Framework Designer and then generate a database schema from the model.</span></span> <span data-ttu-id="25aba-106">模型會儲存在 EDMX 檔案 ( .edmx 副檔名) ，而且可以在 Entity Framework Designer 中加以查看和編輯。</span><span class="sxs-lookup"><span data-stu-id="25aba-106">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="25aba-107">您在應用程式中與互動的類別會自動從 EDMX 檔案產生。</span><span class="sxs-lookup"><span data-stu-id="25aba-107">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="25aba-108">觀賞影片</span><span class="sxs-lookup"><span data-stu-id="25aba-108">Watch the video</span></span>
<span data-ttu-id="25aba-109">這段影片和逐步解說將提供使用 Entity Framework Model First 開發的簡介。</span><span class="sxs-lookup"><span data-stu-id="25aba-109">This video and step-by-step walkthrough provide an introduction to Model First development using Entity Framework.</span></span> <span data-ttu-id="25aba-110">Model First 可讓您使用 Entity Framework Designer 建立新的模型，然後從模型產生資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="25aba-110">Model First allows you to create a new model using the Entity Framework Designer and then generate a database schema from the model.</span></span> <span data-ttu-id="25aba-111">模型會儲存在 EDMX 檔案 ( .edmx 副檔名) ，而且可以在 Entity Framework Designer 中加以查看和編輯。</span><span class="sxs-lookup"><span data-stu-id="25aba-111">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="25aba-112">您在應用程式中與互動的類別會自動從 EDMX 檔案產生。</span><span class="sxs-lookup"><span data-stu-id="25aba-112">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

<span data-ttu-id="25aba-113">**主講人**[Rowan Miller](https://romiller.com/)</span><span class="sxs-lookup"><span data-stu-id="25aba-113">**Presented By**: [Rowan Miller](https://romiller.com/)</span></span>

<span data-ttu-id="25aba-114">**影片**： [wmv](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.wmv)的  |  [MP4](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-mp4video-modelfirst.m4v)  |  [wmv .wmv (ZIP) ](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.zip)</span><span class="sxs-lookup"><span data-stu-id="25aba-114">**Video**: [WMV](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.wmv) | [MP4](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-mp4video-modelfirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="25aba-115">必要條件</span><span class="sxs-lookup"><span data-stu-id="25aba-115">Pre-Requisites</span></span>

<span data-ttu-id="25aba-116">您必須安裝 Visual Studio 2010 或 Visual Studio 2012，才能完成此逐步解說。</span><span class="sxs-lookup"><span data-stu-id="25aba-116">You will need to have Visual Studio 2010 or Visual Studio 2012 installed to complete this walkthrough.</span></span>

<span data-ttu-id="25aba-117">如果您使用 Visual Studio 2010，也需要安裝 [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) 。</span><span class="sxs-lookup"><span data-stu-id="25aba-117">If you are using Visual Studio 2010, you will also need to have [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installed.</span></span>

## <a name="1-create-the-application"></a><span data-ttu-id="25aba-118">1. 建立應用程式</span><span class="sxs-lookup"><span data-stu-id="25aba-118">1. Create the Application</span></span>

<span data-ttu-id="25aba-119">為了簡單起見，我們將建立一個基本的主控台應用程式，以使用 Model First 來執行資料存取：</span><span class="sxs-lookup"><span data-stu-id="25aba-119">To keep things simple we’re going to build a basic console application that uses the Model First to perform data access:</span></span>

-   <span data-ttu-id="25aba-120">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="25aba-120">Open Visual Studio</span></span>
-   <span data-ttu-id="25aba-121">**檔案- &gt; 新增- &gt; 專案 .。。**</span><span class="sxs-lookup"><span data-stu-id="25aba-121">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="25aba-122">從左側功能表和**主控台應用程式**選取 [ **Windows** ]</span><span class="sxs-lookup"><span data-stu-id="25aba-122">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="25aba-123">輸入 **ModelFirstSample** 作為名稱</span><span class="sxs-lookup"><span data-stu-id="25aba-123">Enter **ModelFirstSample** as the name</span></span>
-   <span data-ttu-id="25aba-124">選取 [確定]</span><span class="sxs-lookup"><span data-stu-id="25aba-124">Select **OK**</span></span>

## <a name="2-create-model"></a><span data-ttu-id="25aba-125">2. 建立模型</span><span class="sxs-lookup"><span data-stu-id="25aba-125">2. Create Model</span></span>

<span data-ttu-id="25aba-126">我們將利用 Entity Framework Designer，其中包含做為 Visual Studio 的一部分，以建立我們的模型。</span><span class="sxs-lookup"><span data-stu-id="25aba-126">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="25aba-127">**專案- &gt; 加入新專案 .。。**</span><span class="sxs-lookup"><span data-stu-id="25aba-127">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="25aba-128">從左側功能表中選取 [ **資料** ]，然後 **ADO.NET 實體資料模型**</span><span class="sxs-lookup"><span data-stu-id="25aba-128">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="25aba-129">輸入 **BloggingModel** 做為名稱，然後按一下 **[確定]**，隨即啟動實體資料模型 Wizard</span><span class="sxs-lookup"><span data-stu-id="25aba-129">Enter **BloggingModel** as the name and click **OK**, this launches the Entity Data Model Wizard</span></span>
-   <span data-ttu-id="25aba-130">選取 [ **空的模型** ]，然後按一下 **[完成]**</span><span class="sxs-lookup"><span data-stu-id="25aba-130">Select **Empty Model** and click **Finish**</span></span>

    ![建立空的模型](~/ef6/media/createemptymodel.png)

<span data-ttu-id="25aba-132">Entity Framework Designer 會以空白模型開啟。</span><span class="sxs-lookup"><span data-stu-id="25aba-132">The Entity Framework Designer is opened with a blank model.</span></span> <span data-ttu-id="25aba-133">現在我們可以開始將實體、屬性和關聯加入至模型。</span><span class="sxs-lookup"><span data-stu-id="25aba-133">Now we can start adding entities, properties and associations to the model.</span></span>

-   <span data-ttu-id="25aba-134">以滑鼠右鍵按一下設計介面，然後選取 [**屬性**]</span><span class="sxs-lookup"><span data-stu-id="25aba-134">Right-click on the design surface and select **Properties**</span></span>
-   <span data-ttu-id="25aba-135">在屬性視窗將**實體容器名稱**變更為**BloggingCoNtext** 
     *這是將為您產生的衍生內容名稱，內容代表與資料庫的會話，可讓我們查詢及儲存資料*</span><span class="sxs-lookup"><span data-stu-id="25aba-135">In the Properties window change the **Entity Container Name** to **BloggingContext**
*This is the name of the derived context that will be generated for you, the context represents a session with the database, allowing us to query and save data*</span></span>
-   <span data-ttu-id="25aba-136">以滑鼠右鍵按一下設計介面，然後選取 [**新增- &gt; 實體 ...** ]</span><span class="sxs-lookup"><span data-stu-id="25aba-136">Right-click on the design surface and select **Add New -&gt; Entity…**</span></span>
-   <span data-ttu-id="25aba-137">輸入 **Blog** 作為機構名稱，並以 **BlogId** 作為索引鍵名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="25aba-137">Enter **Blog** as the entity name and **BlogId** as the key name and click **OK**</span></span>

    ![新增 Blog 實體](~/ef6/media/addblogentity.png)

-   <span data-ttu-id="25aba-139">在設計介面上的新實體上按一下滑鼠右鍵，然後選取 [ **加入 &gt; 新的-** 純量屬性]，輸入 **名稱** 做為屬性的名稱。</span><span class="sxs-lookup"><span data-stu-id="25aba-139">Right-click on the new entity on the design surface and select **Add New -&gt; Scalar Property**, enter **Name** as the name of the property.</span></span>
-   <span data-ttu-id="25aba-140">重複此程式以新增 **Url** 屬性。</span><span class="sxs-lookup"><span data-stu-id="25aba-140">Repeat this process to add a **Url** property.</span></span>
-   <span data-ttu-id="25aba-141">以滑鼠右鍵按一下設計介面上的 [ **Url** ] 屬性，然後選取 [**屬性**]，在 [屬性視窗將**可為 null**的設定變更為 [ **True**]， 
     *這可讓我們將 Blog 儲存至資料庫，而不需為其指派 Url*</span><span class="sxs-lookup"><span data-stu-id="25aba-141">Right-click on the **Url** property on the design surface and select **Properties**, in the Properties window change the **Nullable** setting to **True**
*This allows us to save a Blog to the database without assigning it a Url*</span></span>
-   <span data-ttu-id="25aba-142">使用您剛剛學會的技巧，新增具有**PostId**索引鍵屬性的**Post**實體</span><span class="sxs-lookup"><span data-stu-id="25aba-142">Using the techniques you just learnt, add a **Post** entity with a **PostId** key property</span></span>
-   <span data-ttu-id="25aba-143">將 **標題** 和 **內容** 純量屬性加入至 **Post** 實體</span><span class="sxs-lookup"><span data-stu-id="25aba-143">Add **Title** and **Content** scalar properties to the **Post** entity</span></span>

<span data-ttu-id="25aba-144">既然我們有幾個實體，現在就可以在兩者之間加入關聯 (或關聯性) 。</span><span class="sxs-lookup"><span data-stu-id="25aba-144">Now that we have a couple of entities, it’s time to add an association (or relationship) between them.</span></span>

-   <span data-ttu-id="25aba-145">以滑鼠右鍵按一下設計介面，然後選取 [**加入新的 &gt; 關聯 ...** ]。</span><span class="sxs-lookup"><span data-stu-id="25aba-145">Right-click on the design surface and select **Add New -&gt; Association…**</span></span>
-   <span data-ttu-id="25aba-146">將關聯性點的一端**設為子系，而**另一端的多重性為**1** ，另一個端點則以**許多**不同的方式**張貼**， 
     *這表示 blog 有許多貼文，而貼文屬於一個 blog*</span><span class="sxs-lookup"><span data-stu-id="25aba-146">Make one end of the relationship point to **Blog** with a multiplicity of **One** and the other end point to **Post** with a multiplicity of **Many**
*This means that a Blog has many Posts and a Post belongs to one Blog*</span></span>
-   <span data-ttu-id="25aba-147">確定已核取 [ **將外鍵屬性加入至 ' Post '] 實體** 方塊，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="25aba-147">Ensure the **Add foreign key properties to 'Post' Entity** box is checked and click **OK**</span></span>

    ![新增 Association MF](~/ef6/media/addassociationmf.png)

<span data-ttu-id="25aba-149">我們現在有一個簡單的模型，可從中產生資料庫，並用來讀取和寫入資料。</span><span class="sxs-lookup"><span data-stu-id="25aba-149">We now have a simple model that we can generate a database from and use to read and write data.</span></span>

![模型初始](~/ef6/media/modelinitial.png)

### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="25aba-151">Visual Studio 2010 中的其他步驟</span><span class="sxs-lookup"><span data-stu-id="25aba-151">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="25aba-152">如果您在 Visual Studio 2010 中工作，您必須遵循一些額外的步驟，才能升級至最新版本的 Entity Framework。</span><span class="sxs-lookup"><span data-stu-id="25aba-152">If you are working in Visual Studio 2010 there are some additional steps you need to follow to upgrade to the latest version of Entity Framework.</span></span> <span data-ttu-id="25aba-153">升級很重要，因為它可讓您存取改良的 API 介面、更容易使用，以及最新的錯誤修正。</span><span class="sxs-lookup"><span data-stu-id="25aba-153">Upgrading is important because it gives you access to an improved API surface, that is much easier to use, as well as the latest bug fixes.</span></span>

<span data-ttu-id="25aba-154">首先，我們需要從 NuGet 取得最新版本的 Entity Framework。</span><span class="sxs-lookup"><span data-stu-id="25aba-154">First up, we need to get the latest version of Entity Framework from NuGet.</span></span>

-   <span data-ttu-id="25aba-155">**專案– &gt;管理 NuGet 套件 ...** 
    *如果您沒有 [\*\*管理 NuGet 套件 ...*\* ] 選項，您應該安裝[最新版本的 nuget](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) \*</span><span class="sxs-lookup"><span data-stu-id="25aba-155">**Project –&gt; Manage NuGet Packages…**
*If you don’t have the **Manage NuGet Packages…** option you should install the [latest version of NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)*</span></span>
-   <span data-ttu-id="25aba-156">選取 [ **線上** ] 索引標籤</span><span class="sxs-lookup"><span data-stu-id="25aba-156">Select the **Online** tab</span></span>
-   <span data-ttu-id="25aba-157">選取 **EntityFramework** 套件</span><span class="sxs-lookup"><span data-stu-id="25aba-157">Select the **EntityFramework** package</span></span>
-   <span data-ttu-id="25aba-158">按一下 [安裝]</span><span class="sxs-lookup"><span data-stu-id="25aba-158">Click **Install**</span></span>

<span data-ttu-id="25aba-159">接下來，我們需要交換模型以產生使用 DbCoNtext API 的程式碼，這是在 Entity Framework 的較新版本中引進的。</span><span class="sxs-lookup"><span data-stu-id="25aba-159">Next, we need to swap our model to generate code that makes use of the DbContext API, which was introduced in later versions of Entity Framework.</span></span>

-   <span data-ttu-id="25aba-160">在 EF 設計工具中，以滑鼠右鍵按一下模型的空白位置，然後選取 [**新增程式碼產生專案 ...** ]。</span><span class="sxs-lookup"><span data-stu-id="25aba-160">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="25aba-161">從左側功能表中選取 [ **線上範本** ]，然後搜尋 **DbCoNtext**</span><span class="sxs-lookup"><span data-stu-id="25aba-161">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="25aba-162">選取**適用于 C \# **的 EF 5.x DbCoNtext 產生器，輸入**BloggingModel**做為名稱，然後按一下 [**新增**]。</span><span class="sxs-lookup"><span data-stu-id="25aba-162">Select the EF **5.x DbContext Generator for C\#**, enter **BloggingModel** as the name and click **Add**</span></span>

    ![DbCoNtext 範本](~/ef6/media/dbcontexttemplate.png)

## <a name="3-generating-the-database"></a><span data-ttu-id="25aba-164">3. 產生資料庫</span><span class="sxs-lookup"><span data-stu-id="25aba-164">3. Generating the Database</span></span>

<span data-ttu-id="25aba-165">根據我們的模型，Entity Framework 可以計算資料庫架構，讓我們使用模型來儲存和取出資料。</span><span class="sxs-lookup"><span data-stu-id="25aba-165">Given our model, Entity Framework can calculate a database schema that will allow us to store and retrieve data using the model.</span></span>

<span data-ttu-id="25aba-166">隨 Visual Studio 安裝的資料庫伺服器會根據您所安裝的 Visual Studio 版本而有所不同：</span><span class="sxs-lookup"><span data-stu-id="25aba-166">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="25aba-167">如果您使用 Visual Studio 2010，將會建立 SQL Express 資料庫。</span><span class="sxs-lookup"><span data-stu-id="25aba-167">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="25aba-168">如果您使用 Visual Studio 2012，則會建立 [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) 資料庫。</span><span class="sxs-lookup"><span data-stu-id="25aba-168">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) database.</span></span>

<span data-ttu-id="25aba-169">讓我們繼續產生資料庫。</span><span class="sxs-lookup"><span data-stu-id="25aba-169">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="25aba-170">以滑鼠右鍵按一下設計介面，然後選取 [**從模型產生資料庫**...]</span><span class="sxs-lookup"><span data-stu-id="25aba-170">Right-click on the design surface and select **Generate Database from Model…**</span></span>
-   <span data-ttu-id="25aba-171">按一下 [**新增連接 ...** ]</span><span class="sxs-lookup"><span data-stu-id="25aba-171">Click **New Connection…**</span></span> <span data-ttu-id="25aba-172">並指定 LocalDB 或 SQL Express，視您使用的 Visual Studio 版本而定，輸入 **ModelFirst** 做為資料庫名稱。</span><span class="sxs-lookup"><span data-stu-id="25aba-172">and specify either LocalDB or SQL Express, depending on which version of Visual Studio you are using, enter **ModelFirst.Blogging** as the database name.</span></span>

    ![LocalDB 連接 MF](~/ef6/media/localdbconnectionmf.png)

    ![Sql Express Connection MF](~/ef6/media/sqlexpressconnectionmf.png)

-   <span data-ttu-id="25aba-175">選取 **[確定]** ，系統會詢問您是否要建立新的資料庫，請選取 **[是]** 。</span><span class="sxs-lookup"><span data-stu-id="25aba-175">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>
-   <span data-ttu-id="25aba-176">選取 **[下一步]** ，Entity Framework Designer 將會計算用來建立資料庫架構的腳本</span><span class="sxs-lookup"><span data-stu-id="25aba-176">Select **Next** and the Entity Framework Designer will calculate a script to create the database schema</span></span>
-   <span data-ttu-id="25aba-177">腳本顯示之後，請按一下 **[完成]** ，然後將腳本新增至您的專案並開啟</span><span class="sxs-lookup"><span data-stu-id="25aba-177">Once the script is displayed, click **Finish** and the script will be added to your project and opened</span></span>
-   <span data-ttu-id="25aba-178">在腳本上按一下滑鼠右鍵，然後選取 [ **執行**]，系統會提示您指定要連接的資料庫、指定 LocalDB 或 SQL Server Express （視您使用的 Visual Studio 版本而定）。</span><span class="sxs-lookup"><span data-stu-id="25aba-178">Right-click on the script and select **Execute**, you will be prompted to specify the database to connect to, specify LocalDB or SQL Server Express, depending on which version of Visual Studio you are using</span></span>

## <a name="4-reading--writing-data"></a><span data-ttu-id="25aba-179">4. 讀取 & 寫入資料</span><span class="sxs-lookup"><span data-stu-id="25aba-179">4. Reading & Writing Data</span></span>

<span data-ttu-id="25aba-180">現在我們已有模型，您可以使用它來存取某些資料。</span><span class="sxs-lookup"><span data-stu-id="25aba-180">Now that we have a model it’s time to use it to access some data.</span></span> <span data-ttu-id="25aba-181">我們將用來存取資料的類別會根據 EDMX 檔案自動產生。</span><span class="sxs-lookup"><span data-stu-id="25aba-181">The classes we are going to use to access data are being automatically generated for you based on the EDMX file.</span></span>

<span data-ttu-id="25aba-182">*這個螢幕擷取畫面是來自 Visual Studio 2012，如果您使用 Visual Studio 2010，BloggingModel.tt 和 BloggingModel.CoNtext.tt 檔案將直接在您的專案下，而不是在 EDMX 檔案底下。*</span><span class="sxs-lookup"><span data-stu-id="25aba-182">*This screen shot is from Visual Studio 2012, if you are using Visual Studio 2010 the BloggingModel.tt and BloggingModel.Context.tt files will be directly under your project rather than nested under the EDMX file.*</span></span>

![產生的類別](~/ef6/media/generatedclasses.png)

<span data-ttu-id="25aba-184">在 Program.cs 中執行 Main 方法，如下所示。</span><span class="sxs-lookup"><span data-stu-id="25aba-184">Implement the Main method in Program.cs as shown below.</span></span> <span data-ttu-id="25aba-185">這段程式碼會建立內容的新實例，然後使用它來插入新的 Blog。</span><span class="sxs-lookup"><span data-stu-id="25aba-185">This code creates a new instance of our context and then uses it to insert a new Blog.</span></span> <span data-ttu-id="25aba-186">然後，它會使用 LINQ 查詢來從資料庫中依標題的字母順序來取得所有的 Blog。</span><span class="sxs-lookup"><span data-stu-id="25aba-186">Then it uses a LINQ query to retrieve all Blogs from the database ordered alphabetically by Title.</span></span>

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

<span data-ttu-id="25aba-187">您現在可以執行應用程式並進行測試。</span><span class="sxs-lookup"><span data-stu-id="25aba-187">You can now run the application and test it out.</span></span>

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```

## <a name="5-dealing-with-model-changes"></a><span data-ttu-id="25aba-188">5. 處理模型變更</span><span class="sxs-lookup"><span data-stu-id="25aba-188">5. Dealing with Model Changes</span></span>

<span data-ttu-id="25aba-189">現在是時候對我們的模型進行一些變更，當我們進行這些變更時，我們也需要更新資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="25aba-189">Now it’s time to make some changes to our model, when we make these changes we also need to update the database schema.</span></span>

<span data-ttu-id="25aba-190">我們一開始會將新的使用者實體加入至模型。</span><span class="sxs-lookup"><span data-stu-id="25aba-190">We’ll start by adding a new User entity to our model.</span></span>

-   <span data-ttu-id="25aba-191">使用使用者名稱**做為索引**鍵名稱和**字串**做為索引鍵的屬性類型，加入新的**使用者**機構名稱</span><span class="sxs-lookup"><span data-stu-id="25aba-191">Add a new **User** entity name with **Username** as the key name and **String** as the property type for the key</span></span>

    ![新增使用者實體](~/ef6/media/adduserentity.png)

-   <span data-ttu-id="25aba-193">以滑鼠右鍵按一下設計介面上的 [使用者**名稱**] 屬性，然後選取 [**屬性**]，在 [屬性視窗將**MaxLength**設定變更為**50**， 
     *這會將可儲存在使用者名稱中的資料限制為50個字元*</span><span class="sxs-lookup"><span data-stu-id="25aba-193">Right-click on the **Username** property on the design surface and select **Properties**, In the Properties window change the **MaxLength** setting to **50**
*This restricts the data that can be stored in username to 50 characters*</span></span>
-   <span data-ttu-id="25aba-194">將 **DisplayName** 純量屬性新增至 **使用者** 實體</span><span class="sxs-lookup"><span data-stu-id="25aba-194">Add a **DisplayName** scalar property to the **User** entity</span></span>

<span data-ttu-id="25aba-195">我們現在已有更新的模型，並已準備好更新資料庫以容納新的使用者實體類型。</span><span class="sxs-lookup"><span data-stu-id="25aba-195">We now have an updated model and we are ready to update the database to accommodate our new User entity type.</span></span>

-   <span data-ttu-id="25aba-196">以滑鼠右鍵按一下設計介面，然後選取 [ **從模型產生資料庫 ...**]，Entity Framework 將會計算腳本，以根據更新的模型重新建立架構。</span><span class="sxs-lookup"><span data-stu-id="25aba-196">Right-click on the design surface and select **Generate Database from Model…**, Entity Framework will calculate a script to recreate a schema based on the updated model.</span></span>
-   <span data-ttu-id="25aba-197">按一下 [完成]</span><span class="sxs-lookup"><span data-stu-id="25aba-197">Click **Finish**</span></span>
-   <span data-ttu-id="25aba-198">您可能會收到關於覆寫現有 DDL 腳本的警告，以及模型的對應和儲存部分，請針對這兩個警告按一下 **[是]** 。</span><span class="sxs-lookup"><span data-stu-id="25aba-198">You may receive warnings about overwriting the existing DDL script and the mapping and storage parts of the model, click **Yes** for both these warnings</span></span>
-   <span data-ttu-id="25aba-199">系統會為您開啟用來建立資料庫的已更新 SQL 腳本</span><span class="sxs-lookup"><span data-stu-id="25aba-199">The updated SQL script to create the database is opened for you</span></span>  
    <span data-ttu-id="25aba-200">*產生的腳本將會卸載所有現有的資料表，然後從頭重新建立架構。這可能適用于本機開發，但無法將變更推送至已部署的資料庫。如果您需要將變更發行至已部署的資料庫，您必須編輯腳本，或使用架構比較工具來計算遷移腳本。*</span><span class="sxs-lookup"><span data-stu-id="25aba-200">*The script that is generated will drop all existing tables and then recreate the schema from scratch. This may work for local development but is not a viable for pushing changes to a database that has already been deployed. If you need to publish changes to a database that has already been deployed, you will need to edit the script or use a schema compare tool to calculate a migration script.*</span></span>
-   <span data-ttu-id="25aba-201">在腳本上按一下滑鼠右鍵，然後選取 [ **執行**]，系統會提示您指定要連接的資料庫、指定 LocalDB 或 SQL Server Express （視您使用的 Visual Studio 版本而定）。</span><span class="sxs-lookup"><span data-stu-id="25aba-201">Right-click on the script and select **Execute**, you will be prompted to specify the database to connect to, specify LocalDB or SQL Server Express, depending on which version of Visual Studio you are using</span></span>

## <a name="summary"></a><span data-ttu-id="25aba-202">摘要</span><span class="sxs-lookup"><span data-stu-id="25aba-202">Summary</span></span>

<span data-ttu-id="25aba-203">在這個逐步解說中，我們探討了 Model First 開發，可讓我們在 EF 設計工具中建立模型，然後從該模型產生資料庫。</span><span class="sxs-lookup"><span data-stu-id="25aba-203">In this walkthrough we looked at Model First development, which allowed us to create a model in the EF Designer and then generate a database from that model.</span></span> <span data-ttu-id="25aba-204">接著，我們使用此模型來讀取和寫入資料庫中的一些資料。</span><span class="sxs-lookup"><span data-stu-id="25aba-204">We then used the model to read and write some data from the database.</span></span> <span data-ttu-id="25aba-205">最後，我們已更新模型，然後重新建立資料庫架構以符合模型。</span><span class="sxs-lookup"><span data-stu-id="25aba-205">Finally, we updated the model and then recreated the database schema to match the model.</span></span>
