---
title: 第一次-模型 EF6
author: divega
ms.date: 2016-10-23
ms.assetid: e1b9c319-bb8a-4417-ac94-7890f257e7f6
ms.openlocfilehash: 3dd0eba29619f09995d7009dd29462c14bde98c4
ms.sourcegitcommit: 0d36e8ff0892b7f034b765b15e041f375f88579a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2018
ms.locfileid: "44251137"
---
# <a name="model-first"></a><span data-ttu-id="0adb3-102">第一次建立模型</span><span class="sxs-lookup"><span data-stu-id="0adb3-102">Model First</span></span>
<span data-ttu-id="0adb3-103">本影片以及逐步說明的逐步解說提供使用 Entity Framework 模型優先開發的簡介。</span><span class="sxs-lookup"><span data-stu-id="0adb3-103">This video and step-by-step walkthrough provide an introduction to Model First development using Entity Framework.</span></span> <span data-ttu-id="0adb3-104">第一次模型可讓您建立新的模型使用 Entity Framework Designer，然後透過模型產生資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="0adb3-104">Model First allows you to create a new model using the Entity Framework Designer and then generate a database schema from the model.</span></span> <span data-ttu-id="0adb3-105">模型會儲存在 EDMX 檔案 （副檔名為.edmx），及可以檢視和編輯 Entity Framework 設計工具中。</span><span class="sxs-lookup"><span data-stu-id="0adb3-105">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="0adb3-106">從 EDMX 檔案，會自動產生應用程式中與您互動的類別。</span><span class="sxs-lookup"><span data-stu-id="0adb3-106">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="0adb3-107">觀看影片</span><span class="sxs-lookup"><span data-stu-id="0adb3-107">Watch the video</span></span>
<span data-ttu-id="0adb3-108">本影片以及逐步說明的逐步解說提供使用 Entity Framework 模型優先開發的簡介。</span><span class="sxs-lookup"><span data-stu-id="0adb3-108">This video and step-by-step walkthrough provide an introduction to Model First development using Entity Framework.</span></span> <span data-ttu-id="0adb3-109">第一次模型可讓您建立新的模型使用 Entity Framework Designer，然後透過模型產生資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="0adb3-109">Model First allows you to create a new model using the Entity Framework Designer and then generate a database schema from the model.</span></span> <span data-ttu-id="0adb3-110">模型會儲存在 EDMX 檔案 （副檔名為.edmx），及可以檢視和編輯 Entity Framework 設計工具中。</span><span class="sxs-lookup"><span data-stu-id="0adb3-110">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="0adb3-111">從 EDMX 檔案，會自動產生應用程式中與您互動的類別。</span><span class="sxs-lookup"><span data-stu-id="0adb3-111">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

<span data-ttu-id="0adb3-112">**主講人**[Rowan Miller](http://romiller.com/)</span><span class="sxs-lookup"><span data-stu-id="0adb3-112">**Presented By**: [Rowan Miller](http://romiller.com/)</span></span>

<span data-ttu-id="0adb3-113">**視訊**: [WMV](http://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.wmv) | [MP4](http://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-mp4video-modelfirst.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.zip)</span><span class="sxs-lookup"><span data-stu-id="0adb3-113">**Video**: [WMV](http://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.wmv) | [MP4](http://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-mp4video-modelfirst.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="0adb3-114">必要條件</span><span class="sxs-lookup"><span data-stu-id="0adb3-114">Pre-Requisites</span></span>

<span data-ttu-id="0adb3-115">您必須擁有 Visual Studio 2010 或 Visual Studio 2012 安裝完成本逐步解說。</span><span class="sxs-lookup"><span data-stu-id="0adb3-115">You will need to have Visual Studio 2010 or Visual Studio 2012 installed to complete this walkthrough.</span></span>

<span data-ttu-id="0adb3-116">如果您使用 Visual Studio 2010，您也必須有[NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)安裝。</span><span class="sxs-lookup"><span data-stu-id="0adb3-116">If you are using Visual Studio 2010, you will also need to have [NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installed.</span></span>

## <a name="1-create-the-application"></a><span data-ttu-id="0adb3-117">1.建立應用程式</span><span class="sxs-lookup"><span data-stu-id="0adb3-117">1. Create the Application</span></span>

<span data-ttu-id="0adb3-118">為了簡單起見，我們要建置基本的主控台應用程式，會使用 Model First 來執行資料存取：</span><span class="sxs-lookup"><span data-stu-id="0adb3-118">To keep things simple we’re going to build a basic console application that uses the Model First to perform data access:</span></span>

-   <span data-ttu-id="0adb3-119">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0adb3-119">Open Visual Studio</span></span>
-   <span data-ttu-id="0adb3-120">**檔案-&gt;新增-&gt;專案...**</span><span class="sxs-lookup"><span data-stu-id="0adb3-120">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="0adb3-121">選取  **Windows**從左側的功能表和**主控台應用程式**</span><span class="sxs-lookup"><span data-stu-id="0adb3-121">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="0adb3-122">請輸入**ModelFirstSample**做為名稱</span><span class="sxs-lookup"><span data-stu-id="0adb3-122">Enter **ModelFirstSample** as the name</span></span>
-   <span data-ttu-id="0adb3-123">選取 [確定]</span><span class="sxs-lookup"><span data-stu-id="0adb3-123">Select **OK**</span></span>

## <a name="2-create-model"></a><span data-ttu-id="0adb3-124">2.建立模型</span><span class="sxs-lookup"><span data-stu-id="0adb3-124">2. Create Model</span></span>

<span data-ttu-id="0adb3-125">我們將利用 Entity Framework Designer 中，也就是 Visual Studio 的一部分，以建立我們的模型。</span><span class="sxs-lookup"><span data-stu-id="0adb3-125">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="0adb3-126">**專案-&gt;加入新項目...**</span><span class="sxs-lookup"><span data-stu-id="0adb3-126">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="0adb3-127">選取 **資料**從左側功能表，然後**ADO.NET 實體資料模型**</span><span class="sxs-lookup"><span data-stu-id="0adb3-127">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="0adb3-128">請輸入**BloggingModel**作為名稱，然後按一下**確定**，這會啟動 Entity Data Model 精靈</span><span class="sxs-lookup"><span data-stu-id="0adb3-128">Enter **BloggingModel** as the name and click **OK**, this launches the Entity Data Model Wizard</span></span>
-   <span data-ttu-id="0adb3-129">選取 **空的模型**，按一下 **完成**</span><span class="sxs-lookup"><span data-stu-id="0adb3-129">Select **Empty Model** and click **Finish**</span></span>

    ![建立空的模型](~/ef6/media/createemptymodel.png)

<span data-ttu-id="0adb3-131">Entity Framework 設計工具會開啟空白的模型。</span><span class="sxs-lookup"><span data-stu-id="0adb3-131">The Entity Framework Designer is opened with a blank model.</span></span> <span data-ttu-id="0adb3-132">現在我們可以開始將實體、 屬性和關聯加入至模型。</span><span class="sxs-lookup"><span data-stu-id="0adb3-132">Now we can start adding entities, properties and associations to the model.</span></span>

-   <span data-ttu-id="0adb3-133">以滑鼠右鍵按一下設計介面，並選取**屬性**</span><span class="sxs-lookup"><span data-stu-id="0adb3-133">Right-click on the design surface and select **Properties**</span></span>
-   <span data-ttu-id="0adb3-134">在 [屬性] 視窗變更**實體容器名稱**要**為 [bloggingcontext]**
    *這是將會為您的內容產生衍生內容的名稱代表資料庫中，讓我們可以查詢和儲存資料的工作階段*</span><span class="sxs-lookup"><span data-stu-id="0adb3-134">In the Properties window change the **Entity Container Name** to **BloggingContext**
*This is the name of the derived context that will be generated for you, the context represents a session with the database, allowing us to query and save data*</span></span>
-   <span data-ttu-id="0adb3-135">以滑鼠右鍵按一下設計介面，然後選取**加入新-&gt;實體...**</span><span class="sxs-lookup"><span data-stu-id="0adb3-135">Right-click on the design surface and select **Add New -&gt; Entity…**</span></span>
-   <span data-ttu-id="0adb3-136">輸入**部落格**做為實體名稱並**BlogId**做為索引鍵的名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="0adb3-136">Enter **Blog** as the entity name and **BlogId** as the key name and click **OK**</span></span>

    ![加入實體部落格](~/ef6/media/addblogentity.png)

-   <span data-ttu-id="0adb3-138">以滑鼠右鍵按一下設計介面，然後選取新的實體**加入新-&gt;純量屬性**，輸入**名稱**做為屬性的名稱。</span><span class="sxs-lookup"><span data-stu-id="0adb3-138">Right-click on the new entity on the design surface and select **Add New -&gt; Scalar Property**, enter **Name** as the name of the property.</span></span>
-   <span data-ttu-id="0adb3-139">重複此程序，新增**Url**屬性。</span><span class="sxs-lookup"><span data-stu-id="0adb3-139">Repeat this process to add a **Url** property.</span></span>
-   <span data-ttu-id="0adb3-140">以滑鼠右鍵按一下**Url**屬性在設計介面，然後選取**屬性**，在 屬性 視窗變更**Nullable**設為  **True**
    *這可讓我們的部落格儲存至資料庫，但未指派其 Url*</span><span class="sxs-lookup"><span data-stu-id="0adb3-140">Right-click on the **Url** property on the design surface and select **Properties**, in the Properties window change the **Nullable** setting to **True**
*This allows us to save a Blog to the database without assigning it a Url*</span></span>
-   <span data-ttu-id="0adb3-141">使用您剛學會的技術加入**Post**實體**PostId**索引鍵屬性</span><span class="sxs-lookup"><span data-stu-id="0adb3-141">Using the techniques you just learnt, add a **Post** entity with a **PostId** key property</span></span>
-   <span data-ttu-id="0adb3-142">新增**標題**並**內容**純量屬性**Post**實體</span><span class="sxs-lookup"><span data-stu-id="0adb3-142">Add **Title** and **Content** scalar properties to the **Post** entity</span></span>

<span data-ttu-id="0adb3-143">現在，我們有幾個實體時，就可以新增關聯 （或關聯性） 之間。</span><span class="sxs-lookup"><span data-stu-id="0adb3-143">Now that we have a couple of entities, it’s time to add an association (or relationship) between them.</span></span>

-   <span data-ttu-id="0adb3-144">以滑鼠右鍵按一下設計介面，然後選取**加入新-&gt;關聯...**</span><span class="sxs-lookup"><span data-stu-id="0adb3-144">Right-click on the design surface and select **Add New -&gt; Association…**</span></span>
-   <span data-ttu-id="0adb3-145">請指向的關聯性某一端**部落格**的多重性**一個**和其他端點來**Post**多重性為**許多** 
    *這表示，部落格有許多文章和文章屬於一個部落格*</span><span class="sxs-lookup"><span data-stu-id="0adb3-145">Make one end of the relationship point to **Blog** with a multiplicity of **One** and the other end point to **Post** with a multiplicity of **Many**
*This means that a Blog has many Posts and a Post belongs to one Blog*</span></span>
-   <span data-ttu-id="0adb3-146">請確定**外部索引鍵屬性加入 'Post' 實體**核取方塊，然後按一下  **確定**</span><span class="sxs-lookup"><span data-stu-id="0adb3-146">Ensure the **Add foreign key properties to 'Post' Entity** box is checked and click **OK**</span></span>

    ![新增關聯 MF](~/ef6/media/addassociationmf.png)

<span data-ttu-id="0adb3-148">我們現在有簡單的模型，我們可以產生的資料庫，並使用來讀取和寫入資料。</span><span class="sxs-lookup"><span data-stu-id="0adb3-148">We now have a simple model that we can generate a database from and use to read and write data.</span></span>

![建立初始的模型](~/ef6/media/modelinitial.png)

### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="0adb3-150">Visual Studio 2010 中的其他步驟</span><span class="sxs-lookup"><span data-stu-id="0adb3-150">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="0adb3-151">如果您正在 Visual Studio 2010 中有您要升級至最新版的 Entity Framework 遵循一些額外的步驟。</span><span class="sxs-lookup"><span data-stu-id="0adb3-151">If you are working in Visual Studio 2010 there are some additional steps you need to follow to upgrade to the latest version of Entity Framework.</span></span> <span data-ttu-id="0adb3-152">升級很重要，因為它可讓您存取改良的 API 介面，也就是容易使用，以及最新的 bug 修正。</span><span class="sxs-lookup"><span data-stu-id="0adb3-152">Upgrading is important because it gives you access to an improved API surface, that is much easier to use, as well as the latest bug fixes.</span></span>

<span data-ttu-id="0adb3-153">首先，我們要從 NuGet 取得最新版的 Entity Framework。</span><span class="sxs-lookup"><span data-stu-id="0adb3-153">First up, we need to get the latest version of Entity Framework from NuGet.</span></span>

-   <span data-ttu-id="0adb3-154">**專案 –&gt;管理 NuGet 封裝...** 
    *如果您沒有\*\*管理 NuGet 套件...*\* 選項，您應該安裝[最新版的 NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)\*</span><span class="sxs-lookup"><span data-stu-id="0adb3-154">**Project –&gt; Manage NuGet Packages…**
*If you don’t have the **Manage NuGet Packages…** option you should install the [latest version of NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)*</span></span>
-   <span data-ttu-id="0adb3-155">選取 [ **Online** ] 索引標籤</span><span class="sxs-lookup"><span data-stu-id="0adb3-155">Select the **Online** tab</span></span>
-   <span data-ttu-id="0adb3-156">選取  **EntityFramework**封裝</span><span class="sxs-lookup"><span data-stu-id="0adb3-156">Select the **EntityFramework** package</span></span>
-   <span data-ttu-id="0adb3-157">按一下 **安裝**</span><span class="sxs-lookup"><span data-stu-id="0adb3-157">Click **Install**</span></span>

<span data-ttu-id="0adb3-158">接下來，我們需要交換我們的模型來產生程式碼會使用所導入新版的 Entity Framework DbContext API。</span><span class="sxs-lookup"><span data-stu-id="0adb3-158">Next, we need to swap our model to generate code that makes use of the DbContext API, which was introduced in later versions of Entity Framework.</span></span>

-   <span data-ttu-id="0adb3-159">以滑鼠右鍵按一下您的模型，在 EF 設計工具中的非空點，然後選取**加入的程式碼產生項目...**</span><span class="sxs-lookup"><span data-stu-id="0adb3-159">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="0adb3-160">選取 **線上範本**從左側的功能表，並搜尋**DbContext**</span><span class="sxs-lookup"><span data-stu-id="0adb3-160">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="0adb3-161">選取 EF **5.x 適用於 C 的 DbContext Generator\#**，輸入**BloggingModel**做為名稱，然後按一下**新增**</span><span class="sxs-lookup"><span data-stu-id="0adb3-161">Select the EF **5.x DbContext Generator for C\#**, enter **BloggingModel** as the name and click **Add**</span></span>

    ![DbContext 範本](~/ef6/media/dbcontexttemplate.png)

## <a name="3-generating-the-database"></a><span data-ttu-id="0adb3-163">3.產生資料庫</span><span class="sxs-lookup"><span data-stu-id="0adb3-163">3. Generating the Database</span></span>

<span data-ttu-id="0adb3-164">我們的模型，Entity Framework 可以計算資料庫結構描述，讓我們來儲存和擷取使用模型資料。</span><span class="sxs-lookup"><span data-stu-id="0adb3-164">Given our model, Entity Framework can calculate a database schema that will allow us to store and retrieve data using the model.</span></span>

<span data-ttu-id="0adb3-165">Visual Studio 隨附安裝的資料庫伺服器是您已安裝的 Visual Studio 版本而有所不同：</span><span class="sxs-lookup"><span data-stu-id="0adb3-165">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="0adb3-166">如果您使用 Visual Studio 2010 將會建立 SQL Express 資料庫。</span><span class="sxs-lookup"><span data-stu-id="0adb3-166">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="0adb3-167">如果您使用 Visual Studio 2012，則您將會建立[LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx)資料庫。</span><span class="sxs-lookup"><span data-stu-id="0adb3-167">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) database.</span></span>

<span data-ttu-id="0adb3-168">接下來產生的資料庫。</span><span class="sxs-lookup"><span data-stu-id="0adb3-168">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="0adb3-169">以滑鼠右鍵按一下設計介面，並選取**從模型產生資料庫...**</span><span class="sxs-lookup"><span data-stu-id="0adb3-169">Right-click on the design surface and select **Generate Database from Model…**</span></span>
-   <span data-ttu-id="0adb3-170">按一下 **新增連接...**</span><span class="sxs-lookup"><span data-stu-id="0adb3-170">Click **New Connection…**</span></span> <span data-ttu-id="0adb3-171">指定 LocalDB 或 SQL Express，Visual studio 版本而定，您會使用，請輸入**ModelFirst.Blogging**做為資料庫名稱。</span><span class="sxs-lookup"><span data-stu-id="0adb3-171">and specify either LocalDB or SQL Express, depending on which version of Visual Studio you are using, enter **ModelFirst.Blogging** as the database name.</span></span>

    ![LocalDB 連接 MF](~/ef6/media/localdbconnectionmf.png)

    ![Sql Express 連接 MF](~/ef6/media/sqlexpressconnectionmf.png)

-   <span data-ttu-id="0adb3-174">選取  **確定**而且會要求您想要建立新資料庫，請選取**是**</span><span class="sxs-lookup"><span data-stu-id="0adb3-174">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>
-   <span data-ttu-id="0adb3-175">選取 [**下一步]** 和 Entity Framework 設計工具會在計算指令碼來建立資料庫結構描述</span><span class="sxs-lookup"><span data-stu-id="0adb3-175">Select **Next** and the Entity Framework Designer will calculate a script to create the database schema</span></span>
-   <span data-ttu-id="0adb3-176">一旦指令碼會顯示，按一下**完成**指令碼會加入至專案，並且開啟</span><span class="sxs-lookup"><span data-stu-id="0adb3-176">Once the script is displayed, click **Finish** and the script will be added to your project and opened</span></span>
-   <span data-ttu-id="0adb3-177">以滑鼠右鍵按一下指令碼，然後選取**Execute**，系統會提示您指定要連接的資料庫指定 LocalDB 或 SQL Server Express，您使用 Visual studio 版本而定</span><span class="sxs-lookup"><span data-stu-id="0adb3-177">Right-click on the script and select **Execute**, you will be prompted to specify the database to connect to, specify LocalDB or SQL Server Express, depending on which version of Visual Studio you are using</span></span>

## <a name="4-reading--writing-data"></a><span data-ttu-id="0adb3-178">4.讀取和寫入資料</span><span class="sxs-lookup"><span data-stu-id="0adb3-178">4. Reading & Writing Data</span></span>

<span data-ttu-id="0adb3-179">現在，我們就可以開始使用它來存取部分資料的模型。</span><span class="sxs-lookup"><span data-stu-id="0adb3-179">Now that we have a model it’s time to use it to access some data.</span></span> <span data-ttu-id="0adb3-180">我們將類別，用以存取資料自動產生根據 EDMX 檔。</span><span class="sxs-lookup"><span data-stu-id="0adb3-180">The classes we are going to use to access data are being automatically generated for you based on the EDMX file.</span></span>

<span data-ttu-id="0adb3-181">*這個螢幕擷取畫面是來自 Visual Studio 2012 中，如果您使用 Visual Studio 2010 BloggingModel.tt 且 BloggingModel.Context.tt 檔案將會直接在您的專案之下，而非巢狀於 EDMX 檔之下。*</span><span class="sxs-lookup"><span data-stu-id="0adb3-181">*This screen shot is from Visual Studio 2012, if you are using Visual Studio 2010 the BloggingModel.tt and BloggingModel.Context.tt files will be directly under your project rather than nested under the EDMX file.*</span></span>

![產生的類別](~/ef6/media/generatedclasses.png)

<span data-ttu-id="0adb3-183">在 Program.cs 中實作的 Main 方法，如下所示。</span><span class="sxs-lookup"><span data-stu-id="0adb3-183">Implement the Main method in Program.cs as shown below.</span></span> <span data-ttu-id="0adb3-184">此程式碼會建立我們的內容的新執行個體，然後用它來插入新的部落格</span><span class="sxs-lookup"><span data-stu-id="0adb3-184">This code creates a new instance of our context and then uses it to insert a new Blog.</span></span> <span data-ttu-id="0adb3-185">然後它會使用 LINQ 查詢來擷取依字母順序排序項目所使用的資料庫中的所有部落格。</span><span class="sxs-lookup"><span data-stu-id="0adb3-185">Then it uses a LINQ query to retrieve all Blogs from the database ordered alphabetically by Title.</span></span>

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

<span data-ttu-id="0adb3-186">您現在可以執行應用程式，並加以測試。</span><span class="sxs-lookup"><span data-stu-id="0adb3-186">You can now run the application and test it out.</span></span>

```
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```

## <a name="5-dealing-with-model-changes"></a><span data-ttu-id="0adb3-187">5.處理模型變更</span><span class="sxs-lookup"><span data-stu-id="0adb3-187">5. Dealing with Model Changes</span></span>

<span data-ttu-id="0adb3-188">現在就可以開始對我們的模型進行一些變更，當我們做出這些變更，我們也需要更新資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="0adb3-188">Now it’s time to make some changes to our model, when we make these changes we also need to update the database schema.</span></span>

<span data-ttu-id="0adb3-189">我們一開始先將新的 「 使用者 」 實體加入至我們的模型。</span><span class="sxs-lookup"><span data-stu-id="0adb3-189">We’ll start by adding a new User entity to our model.</span></span>

-   <span data-ttu-id="0adb3-190">加入新**使用者**實體名稱取代**Username**做為索引鍵的名稱和**字串**做為索引鍵的屬性類型</span><span class="sxs-lookup"><span data-stu-id="0adb3-190">Add a new **User** entity name with **Username** as the key name and **String** as the property type for the key</span></span>

    ![新增使用者實體](~/ef6/media/adduserentity.png)

-   <span data-ttu-id="0adb3-192">以滑鼠右鍵按一下**使用者名稱**屬性，在設計介面，然後選取**屬性**，在 屬性 視窗變更**MaxLength**設為  **50** 
    *這會限制可以儲存為 50 個字元的使用者名稱中的資料*</span><span class="sxs-lookup"><span data-stu-id="0adb3-192">Right-click on the **Username** property on the design surface and select **Properties**, In the Properties window change the **MaxLength** setting to **50**
*This restricts the data that can be stored in username to 50 characters*</span></span>
-   <span data-ttu-id="0adb3-193">新增**DisplayName**純量屬性**使用者**實體</span><span class="sxs-lookup"><span data-stu-id="0adb3-193">Add a **DisplayName** scalar property to the **User** entity</span></span>

<span data-ttu-id="0adb3-194">我們現在有更新的模型，我們已準備好更新資料庫以容納我們新的使用者實體類型。</span><span class="sxs-lookup"><span data-stu-id="0adb3-194">We now have an updated model and we are ready to update the database to accommodate our new User entity type.</span></span>

-   <span data-ttu-id="0adb3-195">以滑鼠右鍵按一下設計介面，並選取**從模型產生資料庫...**，entity Framework 會計算指令碼來重新建立已更新的模型為基礎的結構描述。</span><span class="sxs-lookup"><span data-stu-id="0adb3-195">Right-click on the design surface and select **Generate Database from Model…**, Entity Framework will calculate a script to recreate a schema based on the updated model.</span></span>
-   <span data-ttu-id="0adb3-196">按一下 **完成**</span><span class="sxs-lookup"><span data-stu-id="0adb3-196">Click **Finish**</span></span>
-   <span data-ttu-id="0adb3-197">您可能會收到有關覆寫現有的 DDL 指令碼和模型的對應和儲存體組件的警告，請按一下**是**這些兩個警告</span><span class="sxs-lookup"><span data-stu-id="0adb3-197">You may receive warnings about overwriting the existing DDL script and the mapping and storage parts of the model, click **Yes** for both these warnings</span></span>
-   <span data-ttu-id="0adb3-198">若要建立的資料庫更新的 SQL 指令碼會開啟</span><span class="sxs-lookup"><span data-stu-id="0adb3-198">The updated SQL script to create the database is opened for you</span></span>  
    <span data-ttu-id="0adb3-199">*產生指令碼會卸除所有現有的資料表，並再重新建立全新的結構描述。這可能適用於本機開發，但並不可行，將變更推送到已部署的資料庫。如果您需要將變更發佈至已部署的資料庫，您必須編輯指令碼，或使用結構描述比較工具來計算移轉指令碼。*</span><span class="sxs-lookup"><span data-stu-id="0adb3-199">*The script that is generated will drop all existing tables and then recreate the schema from scratch. This may work for local development but is not a viable for pushing changes to a database that has already been deployed. If you need to publish changes to a database that has already been deployed, you will need to edit the script or use a schema compare tool to calculate a migration script.*</span></span>
-   <span data-ttu-id="0adb3-200">以滑鼠右鍵按一下指令碼，然後選取**Execute**，系統會提示您指定要連接的資料庫指定 LocalDB 或 SQL Server Express，您使用 Visual studio 版本而定</span><span class="sxs-lookup"><span data-stu-id="0adb3-200">Right-click on the script and select **Execute**, you will be prompted to specify the database to connect to, specify LocalDB or SQL Server Express, depending on which version of Visual Studio you are using</span></span>

## <a name="summary"></a><span data-ttu-id="0adb3-201">總結</span><span class="sxs-lookup"><span data-stu-id="0adb3-201">Summary</span></span>

<span data-ttu-id="0adb3-202">在本逐步解說，我們探討了第一個模型的開發，這可讓我們在 EF 設計工具中建立模型，並再從該模型產生資料庫。</span><span class="sxs-lookup"><span data-stu-id="0adb3-202">In this walkthrough we looked at Model First development, which allowed us to create a model in the EF Designer and then generate a database from that model.</span></span> <span data-ttu-id="0adb3-203">然後，我們會使用模型來讀取和寫入資料庫中的一些資料。</span><span class="sxs-lookup"><span data-stu-id="0adb3-203">We then used the model to read and write some data from the database.</span></span> <span data-ttu-id="0adb3-204">最後，我們會更新模型，然後再重新建立資料庫結構描述，以符合模型。</span><span class="sxs-lookup"><span data-stu-id="0adb3-204">Finally, we updated the model and then recreated the database schema to match the model.</span></span>
