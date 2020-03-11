---
title: 使用 WPF 進行資料系結-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: e90d48e6-bea5-47ef-b756-7b89cce4daf0
ms.openlocfilehash: 1933988277d3be8fecc02fced3293f2b7f80c901
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416593"
---
# <a name="databinding-with-wpf"></a><span data-ttu-id="256aa-102">使用 WPF 的資料系結</span><span class="sxs-lookup"><span data-stu-id="256aa-102">Databinding with WPF</span></span>
<span data-ttu-id="256aa-103">此逐步解說會示範如何將 POCO 類型系結至「主要-詳細資料」表單中的 WPF 控制項。</span><span class="sxs-lookup"><span data-stu-id="256aa-103">This step-by-step walkthrough shows how to bind POCO types to WPF controls in a “master-detail" form.</span></span> <span data-ttu-id="256aa-104">應用程式會使用 Entity Framework Api，以資料庫中的資料來填入物件、追蹤變更，並將資料保存至資料庫。</span><span class="sxs-lookup"><span data-stu-id="256aa-104">The application uses the Entity Framework APIs to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="256aa-105">模型會定義兩個參與一對多關聯性的類型：**類別目錄**（主體\\主要）和**產品**（相依\\詳細資料）。</span><span class="sxs-lookup"><span data-stu-id="256aa-105">The model defines two types that participate in one-to-many relationship: **Category** (principal\\master) and **Product** (dependent\\detail).</span></span> <span data-ttu-id="256aa-106">然後，Visual Studio 工具會用來將模型中定義的類型系結至 WPF 控制項。</span><span class="sxs-lookup"><span data-stu-id="256aa-106">Then, the Visual Studio tools are used to bind the types defined in the model to the WPF controls.</span></span> <span data-ttu-id="256aa-107">WPF 資料系結架構可讓您在相關物件之間導覽：選取主視圖中的資料列會使詳細資料檢視以對應的子資料進行更新。</span><span class="sxs-lookup"><span data-stu-id="256aa-107">The WPF data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="256aa-108">本逐步解說中的螢幕擷取畫面和程式代碼清單取自 Visual Studio 2013，但您可以使用 Visual Studio 2012 或 Visual Studio 2010 來完成此逐步解說。</span><span class="sxs-lookup"><span data-stu-id="256aa-108">The screen shots and code listings in this walkthrough are taken from Visual Studio 2013 but you can complete this walkthrough with Visual Studio 2012 or Visual Studio 2010.</span></span>

## <a name="use-the-object-option-for-creating-wpf-data-sources"></a><span data-ttu-id="256aa-109">使用 [物件] 選項來建立 WPF 資料來源</span><span class="sxs-lookup"><span data-stu-id="256aa-109">Use the 'Object' Option for Creating WPF Data Sources</span></span>

<span data-ttu-id="256aa-110">使用舊版 Entity Framework 我們在根據使用 EF 設計工具建立的模型建立新的資料來源時，建議使用**資料庫**選項。</span><span class="sxs-lookup"><span data-stu-id="256aa-110">With previous version of Entity Framework we used to recommend using the **Database** option when creating a new Data Source based on a model created with the EF Designer.</span></span> <span data-ttu-id="256aa-111">這是因為設計工具會產生衍生自 ObjectCoNtext 的內容，以及衍生自 EntityObject 的實體類別。</span><span class="sxs-lookup"><span data-stu-id="256aa-111">This was because the designer would generate a context that derived from ObjectContext and entity classes that derived from EntityObject.</span></span> <span data-ttu-id="256aa-112">使用資料庫選項可協助您撰寫與此 API 介面互動的最佳程式碼。</span><span class="sxs-lookup"><span data-stu-id="256aa-112">Using the Database option would help you write the best code for interacting with this API surface.</span></span>

<span data-ttu-id="256aa-113">Visual Studio 2012 和 Visual Studio 2013 的 EF 設計工具會產生一個與簡單 POCO 實體類別一起衍生自 DbCoNtext 的內容。</span><span class="sxs-lookup"><span data-stu-id="256aa-113">The EF Designers for Visual Studio 2012 and Visual Studio 2013 generate a context that derives from DbContext together with simple POCO entity classes.</span></span> <span data-ttu-id="256aa-114">在 Visual Studio 2010 中，我們建議您如本逐步解說稍後所述，使用 DbCoNtext 來交換程式碼產生範本。</span><span class="sxs-lookup"><span data-stu-id="256aa-114">With Visual Studio 2010 we recommend swapping to a code generation template that uses DbContext as described later in this walkthrough.</span></span>

<span data-ttu-id="256aa-115">使用 DbCoNtext API 介面時，您應該在建立新的資料來源時使用**物件**選項，如本逐步解說中所示。</span><span class="sxs-lookup"><span data-stu-id="256aa-115">When using the DbContext API surface you should use the **Object** option when creating a new Data Source, as shown in this walkthrough.</span></span>

<span data-ttu-id="256aa-116">如有需要，您可以針對使用 EF 設計工具所建立的模型，[還原為以 ObjectCoNtext 為基礎的程式碼產生](~/ef6/modeling/designer/codegen/legacy-objectcontext.md)。</span><span class="sxs-lookup"><span data-stu-id="256aa-116">If needed, you can [revert to ObjectContext based code generation](~/ef6/modeling/designer/codegen/legacy-objectcontext.md) for models created with the EF Designer.</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="256aa-117">必要條件</span><span class="sxs-lookup"><span data-stu-id="256aa-117">Pre-Requisites</span></span>

<span data-ttu-id="256aa-118">您必須安裝 Visual Studio 2013、Visual Studio 2012 或 Visual Studio 2010，才能完成此逐步解說。</span><span class="sxs-lookup"><span data-stu-id="256aa-118">You need to have Visual Studio 2013, Visual Studio 2012 or Visual Studio 2010 installed to complete this walkthrough.</span></span>

<span data-ttu-id="256aa-119">如果您使用 Visual Studio 2010，您也必須安裝 NuGet。</span><span class="sxs-lookup"><span data-stu-id="256aa-119">If you are using Visual Studio 2010, you also have to install NuGet.</span></span> <span data-ttu-id="256aa-120">如需詳細資訊，請參閱[安裝 NuGet](https://docs.microsoft.com/nuget/install-nuget-client-tools)。</span><span class="sxs-lookup"><span data-stu-id="256aa-120">For more information, see [Installing NuGet](https://docs.microsoft.com/nuget/install-nuget-client-tools).</span></span>  

## <a name="create-the-application"></a><span data-ttu-id="256aa-121">建立應用程式</span><span class="sxs-lookup"><span data-stu-id="256aa-121">Create the Application</span></span>

-   <span data-ttu-id="256aa-122">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="256aa-122">Open Visual Studio</span></span>
-   <span data-ttu-id="256aa-123">**檔案&gt; 新&gt; 專案 ...。**</span><span class="sxs-lookup"><span data-stu-id="256aa-123">**File -&gt; New -&gt; Project….**</span></span>
-   <span data-ttu-id="256aa-124">在左窗格中選取  **Windows** ，然後在右窗格中 **WPFApplication**</span><span class="sxs-lookup"><span data-stu-id="256aa-124">Select **Windows** in the left pane and **WPFApplication** in the right pane</span></span>
-   <span data-ttu-id="256aa-125">輸入 **WPFwithEFSample** 作為名稱</span><span class="sxs-lookup"><span data-stu-id="256aa-125">Enter **WPFwithEFSample** as the name</span></span>
-   <span data-ttu-id="256aa-126">選取 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="256aa-126">Select **OK**</span></span>

## <a name="install-the-entity-framework-nuget-package"></a><span data-ttu-id="256aa-127">安裝 Entity Framework NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="256aa-127">Install the Entity Framework NuGet package</span></span>

-   <span data-ttu-id="256aa-128">在方案總管中，以滑鼠右鍵按一下**WinFormswithEFSample**專案</span><span class="sxs-lookup"><span data-stu-id="256aa-128">In Solution Explorer, right-click on the **WinFormswithEFSample** project</span></span>
-   <span data-ttu-id="256aa-129">選取 [**管理 NuGet 套件 ...** ]</span><span class="sxs-lookup"><span data-stu-id="256aa-129">Select **Manage NuGet Packages…**</span></span>
-   <span data-ttu-id="256aa-130">在 [管理 NuGet 套件] 對話方塊中，選取 [**線上**] 索引標籤，然後選擇 [ **EntityFramework** ] 套件</span><span class="sxs-lookup"><span data-stu-id="256aa-130">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
-   <span data-ttu-id="256aa-131">按一下 [安裝]</span><span class="sxs-lookup"><span data-stu-id="256aa-131">Click **Install**</span></span>  
    >[!NOTE]
    > <span data-ttu-id="256aa-132">除了 EntityFramework 元件之外，也會新增 System.workflow.componentmodel.activity. DataAnnotations 的參考。</span><span class="sxs-lookup"><span data-stu-id="256aa-132">In addition to the EntityFramework assembly a reference to System.ComponentModel.DataAnnotations is also added.</span></span> <span data-ttu-id="256aa-133">如果專案具有 system.string 實體的參考，則會在安裝 EntityFramework 封裝時將它移除。</span><span class="sxs-lookup"><span data-stu-id="256aa-133">If the project has a reference to System.Data.Entity, then it will be removed when the EntityFramework package is installed.</span></span> <span data-ttu-id="256aa-134">System.web 元件不再用於 Entity Framework 6 應用程式。</span><span class="sxs-lookup"><span data-stu-id="256aa-134">The System.Data.Entity assembly is no longer used for Entity Framework 6 applications.</span></span>

## <a name="define-a-model"></a><span data-ttu-id="256aa-135">定義模型</span><span class="sxs-lookup"><span data-stu-id="256aa-135">Define a Model</span></span>

<span data-ttu-id="256aa-136">在此逐步解說中，您可以選擇使用 Code First 或 EF Designer 來執行模型。</span><span class="sxs-lookup"><span data-stu-id="256aa-136">In this walkthrough you can chose to implement a model using Code First or the EF Designer.</span></span> <span data-ttu-id="256aa-137">完成下列兩節的其中一個。</span><span class="sxs-lookup"><span data-stu-id="256aa-137">Complete one of the two following sections.</span></span>

### <a name="option-1-define-a-model-using-code-first"></a><span data-ttu-id="256aa-138">選項1：使用 Code First 定義模型</span><span class="sxs-lookup"><span data-stu-id="256aa-138">Option 1: Define a Model using Code First</span></span>

<span data-ttu-id="256aa-139">本節說明如何使用 Code First 建立模型及其相關聯的資料庫。</span><span class="sxs-lookup"><span data-stu-id="256aa-139">This section shows how to create a model and its associated database using Code First.</span></span> <span data-ttu-id="256aa-140">如果您想要使用 Database First 從使用 EF 設計工具的資料庫還原模型，請跳到下一節（**選項2：使用 Database First 定義模型）**</span><span class="sxs-lookup"><span data-stu-id="256aa-140">Skip to the next section (**Option 2: Define a model using Database First)** if you would rather use Database First to reverse engineer your model from a database using the EF designer</span></span>

<span data-ttu-id="256aa-141">使用 Code First 開發時，您通常會從撰寫定義概念（網域）模型的 .NET Framework 類別開始著手。</span><span class="sxs-lookup"><span data-stu-id="256aa-141">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span>

-   <span data-ttu-id="256aa-142">將新類別新增至 **WPFwithEFSample：**</span><span class="sxs-lookup"><span data-stu-id="256aa-142">Add a new class to the **WPFwithEFSample:**</span></span>
    -   <span data-ttu-id="256aa-143">以滑鼠右鍵按一下專案名稱</span><span class="sxs-lookup"><span data-stu-id="256aa-143">Right-click on the project name</span></span>
    -   <span data-ttu-id="256aa-144">依**序選取 [** 新增] 和 [**新增專案**]</span><span class="sxs-lookup"><span data-stu-id="256aa-144">Select **Add**, then **New Item**</span></span>
    -   <span data-ttu-id="256aa-145">選取 [**類別**]，然後輸入 **產品** 做為類別名稱</span><span class="sxs-lookup"><span data-stu-id="256aa-145">Select **Class** and enter **Product** for the class name</span></span>
-   <span data-ttu-id="256aa-146">使用下列程式碼取代 **Product** 類別定義：</span><span class="sxs-lookup"><span data-stu-id="256aa-146">Replace the **Product** class definition with the following code:</span></span>

``` csharp
    namespace WPFwithEFSample
    {
        public class Product
        {
            public int ProductId { get; set; }
            public string Name { get; set; }

            public int CategoryId { get; set; }
            public virtual Category Category { get; set; }
        }
    }

-   Add a **Category** class with the following definition:

    using System.Collections.ObjectModel;

    namespace WPFwithEFSample
    {
        public class Category
        {
            public Category()
            {
                this.Products = new ObservableCollection<Product>();
            }

            public int CategoryId { get; set; }
            public string Name { get; set; }

            public virtual ObservableCollection<Product> Products { get; private set; }
        }
    }
```

<span data-ttu-id="256aa-147">**Product**類別上**Category**類別和**category**屬性的**Products**屬性是導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="256aa-147">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="256aa-148">在 Entity Framework 中，導覽屬性會提供一種方式來導覽兩個實體類型之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="256aa-148">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="256aa-149">除了定義實體以外，您還需要定義衍生自 DbCoNtext 的類別，並公開 DbSet&lt;TEntity&gt; 屬性。</span><span class="sxs-lookup"><span data-stu-id="256aa-149">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="256aa-150">DbSet&lt;TEntity&gt; 屬性可讓內容知道您想要包含在模型中的類型。</span><span class="sxs-lookup"><span data-stu-id="256aa-150">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="256aa-151">DbCoNtext 衍生類型的實例會在運行時間管理實體物件，其中包括以資料庫的資料填入物件、變更追蹤，以及將資料保存至資料庫。</span><span class="sxs-lookup"><span data-stu-id="256aa-151">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

-   <span data-ttu-id="256aa-152">使用下列定義，將新的**ProductCoNtext**類別新增至專案：</span><span class="sxs-lookup"><span data-stu-id="256aa-152">Add a new **ProductContext** class to the project with the following definition:</span></span>

``` csharp
    using System.Data.Entity;

    namespace WPFwithEFSample
    {
        public class ProductContext : DbContext
        {
            public DbSet<Category> Categories { get; set; }
            public DbSet<Product> Products { get; set; }
        }
    }
```

<span data-ttu-id="256aa-153">編譯專案。</span><span class="sxs-lookup"><span data-stu-id="256aa-153">Compile the project.</span></span>

### <a name="option-2-define-a-model-using-database-first"></a><span data-ttu-id="256aa-154">選項2：使用 Database First 定義模型</span><span class="sxs-lookup"><span data-stu-id="256aa-154">Option 2: Define a model using Database First</span></span>

<span data-ttu-id="256aa-155">本節說明如何使用 Database First，從使用 EF 設計工具的資料庫，對模型進行反向工程。</span><span class="sxs-lookup"><span data-stu-id="256aa-155">This section shows how to use Database First to reverse engineer your model from a database using the EF designer.</span></span> <span data-ttu-id="256aa-156">如果您已完成上一節（**選項1：使用 Code First 定義模型）** ，請略過本節，並直接移至 [消極式**載入**] 區段。</span><span class="sxs-lookup"><span data-stu-id="256aa-156">If you completed the previous section (**Option 1: Define a model using Code First)**, then skip this section and go straight to the **Lazy Loading** section.</span></span>

#### <a name="create-an-existing-database"></a><span data-ttu-id="256aa-157">建立現有的資料庫</span><span class="sxs-lookup"><span data-stu-id="256aa-157">Create an Existing Database</span></span>

<span data-ttu-id="256aa-158">通常當您將目標設為現有的資料庫時，就會建立它，但在此逐步解說中，我們需要建立要存取的資料庫。</span><span class="sxs-lookup"><span data-stu-id="256aa-158">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="256aa-159">隨 Visual Studio 安裝的資料庫伺服器會根據您安裝的 Visual Studio 版本而有所不同：</span><span class="sxs-lookup"><span data-stu-id="256aa-159">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="256aa-160">如果您使用 Visual Studio 2010，您將會建立 SQL Express 資料庫。</span><span class="sxs-lookup"><span data-stu-id="256aa-160">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="256aa-161">如果您使用 Visual Studio 2012，則您將會建立[LocalDB](https://msdn.microsoft.com/library/hh510202.aspx)資料庫。</span><span class="sxs-lookup"><span data-stu-id="256aa-161">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) database.</span></span>

<span data-ttu-id="256aa-162">讓我們繼續產生資料庫。</span><span class="sxs-lookup"><span data-stu-id="256aa-162">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="256aa-163">**View&gt; 伺服器總管**</span><span class="sxs-lookup"><span data-stu-id="256aa-163">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="256aa-164">以滑鼠右鍵按一下 **資料連線-&gt; 新增連接 ...**</span><span class="sxs-lookup"><span data-stu-id="256aa-164">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="256aa-165">如果您還沒有從伺服器總管連接到資料庫，則必須選取 [Microsoft SQL Server] 做為資料來源</span><span class="sxs-lookup"><span data-stu-id="256aa-165">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![變更資料來源](~/ef6/media/changedatasource.png)

-   <span data-ttu-id="256aa-167">連接到 LocalDB 或 SQL Express （視您安裝的版本而定），然後輸入**Products**作為資料庫名稱</span><span class="sxs-lookup"><span data-stu-id="256aa-167">Connect to either LocalDB or SQL Express, depending on which one you have installed, and enter **Products** as the database name</span></span>

    ![新增連接 LocalDB](~/ef6/media/addconnectionlocaldb.png)

    ![新增連接快速](~/ef6/media/addconnectionexpress.png)

-   <span data-ttu-id="256aa-170">選取 **[確定]** ，系統會詢問您是否要建立新的資料庫，然後選取 **[是]**</span><span class="sxs-lookup"><span data-stu-id="256aa-170">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![建立 Database](~/ef6/media/createdatabase.png)

-   <span data-ttu-id="256aa-172">新的資料庫現在會出現在伺服器總管中，以滑鼠右鍵按一下它，然後選取 [追加**查詢**]</span><span class="sxs-lookup"><span data-stu-id="256aa-172">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="256aa-173">將下列 SQL 複製到新的查詢中，然後以滑鼠右鍵按一下查詢並選取 [**執行**]。</span><span class="sxs-lookup"><span data-stu-id="256aa-173">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

``` SQL
    CREATE TABLE [dbo].[Categories] (
        [CategoryId] [int] NOT NULL IDENTITY,
        [Name] [nvarchar](max),
        CONSTRAINT [PK_dbo.Categories] PRIMARY KEY ([CategoryId])
    )

    CREATE TABLE [dbo].[Products] (
        [ProductId] [int] NOT NULL IDENTITY,
        [Name] [nvarchar](max),
        [CategoryId] [int] NOT NULL,
        CONSTRAINT [PK_dbo.Products] PRIMARY KEY ([ProductId])
    )

    CREATE INDEX [IX_CategoryId] ON [dbo].[Products]([CategoryId])

    ALTER TABLE [dbo].[Products] ADD CONSTRAINT [FK_dbo.Products_dbo.Categories_CategoryId] FOREIGN KEY ([CategoryId]) REFERENCES [dbo].[Categories] ([CategoryId]) ON DELETE CASCADE
```

#### <a name="reverse-engineer-model"></a><span data-ttu-id="256aa-174">反向工程模型</span><span class="sxs-lookup"><span data-stu-id="256aa-174">Reverse Engineer Model</span></span>

<span data-ttu-id="256aa-175">我們將使用 Entity Framework Designer （隨附于 Visual Studio 的一部分）來建立我們的模型。</span><span class="sxs-lookup"><span data-stu-id="256aa-175">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="256aa-176">**專案-&gt; 加入新專案 。**</span><span class="sxs-lookup"><span data-stu-id="256aa-176">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="256aa-177">從左側功能表中選取 [**資料**]，然後**ADO.NET 實體資料模型**</span><span class="sxs-lookup"><span data-stu-id="256aa-177">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="256aa-178">輸入**ProductModel**作為名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="256aa-178">Enter **ProductModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="256aa-179">這會啟動**實體資料模型 Wizard**</span><span class="sxs-lookup"><span data-stu-id="256aa-179">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="256aa-180">選取 [**從資料庫產生**]，然後按 **[下一步]**</span><span class="sxs-lookup"><span data-stu-id="256aa-180">Select **Generate from Database** and click **Next**</span></span>

    ![選擇模型內容](~/ef6/media/choosemodelcontents.png)

-   <span data-ttu-id="256aa-182">選取您在第一個區段中建立之資料庫的連接，並輸入**ProductCoNtext**做為連接字串的名稱，然後按 **[下一步]**</span><span class="sxs-lookup"><span data-stu-id="256aa-182">Select the connection to the database you created in the first section, enter **ProductContext** as the name of the connection string and click **Next**</span></span>

    ![選擇您的連線](~/ef6/media/chooseyourconnection.png)

-   <span data-ttu-id="256aa-184">按一下 [資料表] 旁的核取方塊以匯入所有資料表，然後按一下 [完成]</span><span class="sxs-lookup"><span data-stu-id="256aa-184">Click the checkbox next to ‘Tables’ to import all tables and click ‘Finish’</span></span>

    ![選擇您的物件](~/ef6/media/chooseyourobjects.png)

<span data-ttu-id="256aa-186">反向工程程式完成之後，新的模型就會新增至您的專案，並開啟以供您在 Entity Framework Designer 中觀看。</span><span class="sxs-lookup"><span data-stu-id="256aa-186">Once the reverse engineer process completes the new model is added to your project and opened up for you to view in the Entity Framework Designer.</span></span> <span data-ttu-id="256aa-187">App.config 檔案也已新增至您的專案，其中包含資料庫的連接詳細資料。</span><span class="sxs-lookup"><span data-stu-id="256aa-187">An App.config file has also been added to your project with the connection details for the database.</span></span>

#### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="256aa-188">Visual Studio 2010 中的其他步驟</span><span class="sxs-lookup"><span data-stu-id="256aa-188">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="256aa-189">如果您是在 Visual Studio 2010 中工作，則必須更新 EF 設計工具以使用 EF6 程式碼產生。</span><span class="sxs-lookup"><span data-stu-id="256aa-189">If you are working in Visual Studio 2010 then you will need to update the EF designer to use EF6 code generation.</span></span>

-   <span data-ttu-id="256aa-190">在 EF 設計工具中，以滑鼠右鍵按一下模型的空白位置，然後選取 [**新增程式碼產生專案**...]。</span><span class="sxs-lookup"><span data-stu-id="256aa-190">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="256aa-191">從左側功能表中選取 [**線上範本**]，然後搜尋**DbCoNtext**</span><span class="sxs-lookup"><span data-stu-id="256aa-191">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="256aa-192">選取**適用于 C\#的 EF 6.X DbCoNtext**產生器，輸入**ProductsModel**做為名稱，然後按一下 [新增]</span><span class="sxs-lookup"><span data-stu-id="256aa-192">Select the **EF 6.x DbContext Generator for C\#,** enter **ProductsModel** as the name and click Add</span></span>

#### <a name="updating-code-generation-for-data-binding"></a><span data-ttu-id="256aa-193">更新資料系結的程式碼產生</span><span class="sxs-lookup"><span data-stu-id="256aa-193">Updating code generation for data binding</span></span>

<span data-ttu-id="256aa-194">EF 會使用 T4 範本從您的模型產生程式碼。</span><span class="sxs-lookup"><span data-stu-id="256aa-194">EF generates code from your model using T4 templates.</span></span> <span data-ttu-id="256aa-195">隨附于 Visual Studio 或從 Visual Studio 資源庫下載的範本，主要是供一般用途使用。</span><span class="sxs-lookup"><span data-stu-id="256aa-195">The templates shipped with Visual Studio or downloaded from the Visual Studio gallery are intended for general purpose use.</span></span> <span data-ttu-id="256aa-196">這表示從這些範本產生的實體具有簡單的 ICollection&lt;T&gt; 屬性。</span><span class="sxs-lookup"><span data-stu-id="256aa-196">This means that the entities generated from these templates have simple ICollection&lt;T&gt; properties.</span></span> <span data-ttu-id="256aa-197">不過，使用 WPF 進行資料系結時，最好使用集合屬性的**ObservableCollection** ，讓 WPF 可以追蹤對集合所做的變更。</span><span class="sxs-lookup"><span data-stu-id="256aa-197">However, when doing data binding using WPF it is desirable to use **ObservableCollection** for collection properties so that WPF can keep track of changes made to the collections.</span></span> <span data-ttu-id="256aa-198">為此，我們會將範本修改為使用 ObservableCollection。</span><span class="sxs-lookup"><span data-stu-id="256aa-198">To this end we will to modify the templates to use ObservableCollection.</span></span>

-   <span data-ttu-id="256aa-199">開啟**方案總管**並尋找**ProductModel .edmx**檔案</span><span class="sxs-lookup"><span data-stu-id="256aa-199">Open the **Solution Explorer** and find **ProductModel.edmx** file</span></span>
-   <span data-ttu-id="256aa-200">尋找**ProductModel.tt**檔案，該檔案將會在 ProductModel .edmx 檔案底下加以嵌套</span><span class="sxs-lookup"><span data-stu-id="256aa-200">Find the **ProductModel.tt** file which will be nested under the ProductModel.edmx file</span></span>

    ![WPF 產品型號範本](~/ef6/media/wpfproductmodeltemplate.png)

-   <span data-ttu-id="256aa-202">按兩下 ProductModel.tt 檔案，在 [Visual Studio 編輯器] 中開啟該檔案</span><span class="sxs-lookup"><span data-stu-id="256aa-202">Double-click on the ProductModel.tt file to open it in the Visual Studio editor</span></span>
-   <span data-ttu-id="256aa-203">尋找並以 "**ObservableCollection**" 取代 "**ICollection**" 的兩個出現專案。</span><span class="sxs-lookup"><span data-stu-id="256aa-203">Find and replace the two occurrences of “**ICollection**” with “**ObservableCollection**”.</span></span> <span data-ttu-id="256aa-204">這些是大約位於296和484行的位置。</span><span class="sxs-lookup"><span data-stu-id="256aa-204">These are located approximately at lines 296 and 484.</span></span>
-   <span data-ttu-id="256aa-205">尋找並將第一個出現的 "**HashSet**" 取代為 "**ObservableCollection**"。</span><span class="sxs-lookup"><span data-stu-id="256aa-205">Find and replace the first occurrence of “**HashSet**” with “**ObservableCollection**”.</span></span> <span data-ttu-id="256aa-206">這項專案大約位於第50行。</span><span class="sxs-lookup"><span data-stu-id="256aa-206">This occurrence is located approximately at line 50.</span></span> <span data-ttu-id="256aa-207">**請**不要取代稍後在程式碼中找到的第二個出現的 HashSet。</span><span class="sxs-lookup"><span data-stu-id="256aa-207">**Do not** replace the second occurrence of HashSet found later in the code.</span></span>
-   <span data-ttu-id="256aa-208">尋找並以 "**system.collections.objectmodel.observablecollection**" 取代唯一出現的 "**system.object**"。</span><span class="sxs-lookup"><span data-stu-id="256aa-208">Find and replace the only occurrence of “**System.Collections.Generic**” with “**System.Collections.ObjectModel**”.</span></span> <span data-ttu-id="256aa-209">這大約位於第424行。</span><span class="sxs-lookup"><span data-stu-id="256aa-209">This is located approximately at line 424.</span></span>
-   <span data-ttu-id="256aa-210">儲存 ProductModel.tt 檔案。</span><span class="sxs-lookup"><span data-stu-id="256aa-210">Save the ProductModel.tt file.</span></span> <span data-ttu-id="256aa-211">這應該會導致重新產生實體的程式碼。</span><span class="sxs-lookup"><span data-stu-id="256aa-211">This should cause the code for entities to be regenerated.</span></span> <span data-ttu-id="256aa-212">如果程式碼未自動重新產生，請以滑鼠右鍵按一下 [ProductModel.tt]，然後選擇 [執行自訂工具]。</span><span class="sxs-lookup"><span data-stu-id="256aa-212">If the code does not regenerate automatically, then right click on ProductModel.tt and choose “Run Custom Tool”.</span></span>

<span data-ttu-id="256aa-213">如果您現在開啟 Category.cs 檔案（此檔案是以 ProductModel.tt 為基礎），您應該會看到 Products 集合的類型為**ObservableCollection&lt;產品&gt;** 。</span><span class="sxs-lookup"><span data-stu-id="256aa-213">If you now open the Category.cs file (which is nested under ProductModel.tt) then you should see that the Products collection has the type **ObservableCollection&lt;Product&gt;**.</span></span>

<span data-ttu-id="256aa-214">編譯專案。</span><span class="sxs-lookup"><span data-stu-id="256aa-214">Compile the project.</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="256aa-215">消極式載入</span><span class="sxs-lookup"><span data-stu-id="256aa-215">Lazy Loading</span></span>

<span data-ttu-id="256aa-216">**Product**類別上**Category**類別和**category**屬性的**Products**屬性是導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="256aa-216">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="256aa-217">在 Entity Framework 中，導覽屬性會提供一種方式來導覽兩個實體類型之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="256aa-217">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="256aa-218">當您第一次存取導覽屬性時，EF 可讓您選擇自動從資料庫載入相關實體。</span><span class="sxs-lookup"><span data-stu-id="256aa-218">EF gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="256aa-219">使用這種類型的載入（稱為消極式載入）時，請注意，當您第一次存取每個導覽屬性時，如果內容尚未存在於環境中，則會針對資料庫執行個別的查詢。</span><span class="sxs-lookup"><span data-stu-id="256aa-219">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="256aa-220">使用 POCO 實體類型時，EF 會在執行時間建立衍生 proxy 類型的實例，然後覆寫類別中的虛擬屬性來新增載入攔截，以達到消極式載入。</span><span class="sxs-lookup"><span data-stu-id="256aa-220">When using POCO entity types, EF achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="256aa-221">若要取得相關物件的消極式載入，您必須將導覽屬性 getter 宣告為**public**和**virtual** （可在 Visual Basic 中覆**寫**），而且您的類別不得為**密封**（在 Visual Basic 中為**NotOverridable** ）。</span><span class="sxs-lookup"><span data-stu-id="256aa-221">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and you class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="256aa-222">使用 Database First 導覽屬性會自動設為虛擬，以啟用消極式載入。</span><span class="sxs-lookup"><span data-stu-id="256aa-222">When using Database First navigation properties are automatically made virtual to enable lazy loading.</span></span> <span data-ttu-id="256aa-223">在 [Code First] 區段中，我們選擇將導覽屬性設為 [虛擬]，原因如下</span><span class="sxs-lookup"><span data-stu-id="256aa-223">In the Code First section we chose to make the navigation properties virtual for the same reason</span></span>

## <a name="bind-object-to-controls"></a><span data-ttu-id="256aa-224">將物件系結至控制項</span><span class="sxs-lookup"><span data-stu-id="256aa-224">Bind Object to Controls</span></span>

<span data-ttu-id="256aa-225">加入模型中所定義的類別，做為此 WPF 應用程式的資料來源。</span><span class="sxs-lookup"><span data-stu-id="256aa-225">Add the classes that are defined in the model as data sources for this WPF application.</span></span>

-   <span data-ttu-id="256aa-226">按兩下方案總管中的 [ **mainwindow.xaml** ] 以開啟主要表單</span><span class="sxs-lookup"><span data-stu-id="256aa-226">Double-click **MainWindow.xaml** in Solution Explorer to open the main form</span></span>
-   <span data-ttu-id="256aa-227">從主功能表中，選取 [**專案-&gt; 加入新的資料來源**...]</span><span class="sxs-lookup"><span data-stu-id="256aa-227">From the main menu, select **Project -&gt; Add New Data Source …**</span></span>
    <span data-ttu-id="256aa-228">（在 Visual Studio 2010 中，您需要選取 [**資料-&gt; 加入新的資料來源**...]）</span><span class="sxs-lookup"><span data-stu-id="256aa-228">(in Visual Studio 2010, you need to select **Data -&gt; Add New Data Source…**)</span></span>
-   <span data-ttu-id="256aa-229">在 [選擇資料來源] Typewindow 中，選取 [**物件**]，然後按 **[下一步]**</span><span class="sxs-lookup"><span data-stu-id="256aa-229">In the Choose a Data Source Typewindow, select **Object** and click **Next**</span></span>
-   <span data-ttu-id="256aa-230">在 [選取資料物件] 對話方塊中，展開**WPFwithEFSample** 兩次，然後選取 [**類別**]</span><span class="sxs-lookup"><span data-stu-id="256aa-230">In the Select the Data Objects dialog, unfold the **WPFwithEFSample** two times and select **Category**</span></span>  
    <span data-ttu-id="256aa-231">*不需要選取**產品**資料來源，因為我們會透過**類別**資料來源上的**產品**屬性來取得它*</span><span class="sxs-lookup"><span data-stu-id="256aa-231">*There is no need to select the **Product** data source, because we will get to it through the **Product**’s property on the **Category** data source*</span></span>  

    ![選取資料物件](~/ef6/media/selectdataobjects.png)

-   <span data-ttu-id="256aa-233">按一下 [完成]。</span><span class="sxs-lookup"><span data-stu-id="256aa-233">Click **Finish.**</span></span>
-   <span data-ttu-id="256aa-234">如果 [資料來源] 視窗未顯示，請在 [Mainwindow.xaml] 視窗旁開啟 [資料來源] 視窗 *，選取 [ **View-&gt; 其他 Windows&gt; 資料來源**]*</span><span class="sxs-lookup"><span data-stu-id="256aa-234">The Data Sources window is opened next to the MainWindow.xaml window *If the Data Sources window is not showing up, select **View -&gt; Other Windows-&gt; Data Sources***</span></span>
-   <span data-ttu-id="256aa-235">按釘選圖示，讓 [資料來源] 視窗不會自動隱藏。</span><span class="sxs-lookup"><span data-stu-id="256aa-235">Press the pin icon, so the Data Sources window does not auto hide.</span></span> <span data-ttu-id="256aa-236">如果視窗已經是可見的，您可能需要按 [重新整理] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="256aa-236">You may need to hit the refresh button if the window was already visible.</span></span>

    ![資料來源](~/ef6/media/datasources.png)

-   <span data-ttu-id="256aa-238">選取 [ **類別**] 資料來源，並將它拖曳至表單上。</span><span class="sxs-lookup"><span data-stu-id="256aa-238">Select the **Category** data source and drag it on the form.</span></span>

<span data-ttu-id="256aa-239">當我們拖曳此來源時，就會發生下列情況：</span><span class="sxs-lookup"><span data-stu-id="256aa-239">The following happened when we dragged this source:</span></span>

-   <span data-ttu-id="256aa-240">**CategoryViewSource**資源和**categoryDataGrid**控制項已加入至 XAML</span><span class="sxs-lookup"><span data-stu-id="256aa-240">The **categoryViewSource** resource and the **categoryDataGrid** control were added to XAML</span></span> 
-   <span data-ttu-id="256aa-241">父方格元素上的 DataCoNtext 屬性已設定為 "{StaticResource **categoryViewSource** }"。</span><span class="sxs-lookup"><span data-stu-id="256aa-241">The DataContext property on the parent Grid element was set to "{StaticResource **categoryViewSource** }".</span></span><span data-ttu-id="256aa-242"> **CategoryViewSource**資源可作為外部\\父方格元素的系結來源。</span><span class="sxs-lookup"><span data-stu-id="256aa-242"> The **categoryViewSource** resource serves as a binding source for the outer\\parent Grid element.</span></span> <span data-ttu-id="256aa-243">然後，內部方格元素會從父方格繼承 DataCoNtext 值（categoryDataGrid 的 ItemsSource 屬性設定為 "{Binding}"）</span><span class="sxs-lookup"><span data-stu-id="256aa-243">The inner Grid elements then inherit the DataContext value from the parent Grid (the categoryDataGrid’s ItemsSource property is set to "{Binding}")</span></span>

``` xml
    <Window.Resources>
        <CollectionViewSource x:Key="categoryViewSource"
                                d:DesignSource="{d:DesignInstance {x:Type local:Category}, CreateList=True}"/>
    </Window.Resources>
    <Grid DataContext="{StaticResource categoryViewSource}">
        <DataGrid x:Name="categoryDataGrid" AutoGenerateColumns="False" EnableRowVirtualization="True"
                    ItemsSource="{Binding}" Margin="13,13,43,191"
                    RowDetailsVisibilityMode="VisibleWhenSelected">
            <DataGrid.Columns>
                <DataGridTextColumn x:Name="categoryIdColumn" Binding="{Binding CategoryId}"
                                    Header="Category Id" Width="SizeToHeader"/>
                <DataGridTextColumn x:Name="nameColumn" Binding="{Binding Name}"
                                    Header="Name" Width="SizeToHeader"/>
            </DataGrid.Columns>
        </DataGrid>
    </Grid>
```

## <a name="adding-a-details-grid"></a><span data-ttu-id="256aa-244">加入詳細資料方格</span><span class="sxs-lookup"><span data-stu-id="256aa-244">Adding a Details Grid</span></span>

<span data-ttu-id="256aa-245">既然我們已經有方格可以顯示類別，讓我們新增 [詳細資料] 方格來顯示相關聯的產品。</span><span class="sxs-lookup"><span data-stu-id="256aa-245">Now that we have a grid to display Categories let's add a details grid to display the associated Products.</span></span>

-   <span data-ttu-id="256aa-246">從 [ **類別**] [資料來源] 底下選取 [ **Products** ] 屬性，並將它拖曳至表單上。</span><span class="sxs-lookup"><span data-stu-id="256aa-246">Select the **Products** property from under the **Category** data source and drag it on the form.</span></span>
    -   <span data-ttu-id="256aa-247">**CategoryProductsViewSource**資源和**productDataGrid**方格會新增至 XAML</span><span class="sxs-lookup"><span data-stu-id="256aa-247">The **categoryProductsViewSource** resource and **productDataGrid** grid are added to XAML</span></span>
    -   <span data-ttu-id="256aa-248">此資源的系結路徑已設定為產品</span><span class="sxs-lookup"><span data-stu-id="256aa-248">The binding path for this resource is set to Products</span></span>
    -   <span data-ttu-id="256aa-249">WPF 資料系結架構可確保只有與所選類別相關的產品才會顯示在**productDataGrid**中</span><span class="sxs-lookup"><span data-stu-id="256aa-249">WPF data-binding framework ensures that only Products related to the selected Category show up in **productDataGrid**</span></span>
-   <span data-ttu-id="256aa-250">從 [工具箱] 中，將**按鈕**拖曳至表單。</span><span class="sxs-lookup"><span data-stu-id="256aa-250">From the Toolbox, drag **Button** on to the form.</span></span> <span data-ttu-id="256aa-251">將 [**名稱**] 屬性設為**buttonSave** ，並將 [**內容**] 屬性設定為 [**儲存**]。</span><span class="sxs-lookup"><span data-stu-id="256aa-251">Set the **Name** property to **buttonSave** and the **Content** property to **Save**.</span></span>

<span data-ttu-id="256aa-252">表單看起來應該像這樣：</span><span class="sxs-lookup"><span data-stu-id="256aa-252">The form should look similar to this:</span></span>

![設計師](~/ef6/media/designer.png) 

## <a name="add-code-that-handles-data-interaction"></a><span data-ttu-id="256aa-254">加入處理資料互動的程式碼</span><span class="sxs-lookup"><span data-stu-id="256aa-254">Add Code that Handles Data Interaction</span></span>

<span data-ttu-id="256aa-255">現在可以在主視窗中加入一些事件處理常式。</span><span class="sxs-lookup"><span data-stu-id="256aa-255">It's time to add some event handlers to the main window.</span></span>

-   <span data-ttu-id="256aa-256">在 [XAML] 視窗中，按一下 [ **&lt;] 視窗**元素，這會選取主視窗</span><span class="sxs-lookup"><span data-stu-id="256aa-256">In the XAML window, click on the **&lt;Window** element, this selects the main window</span></span>
-   <span data-ttu-id="256aa-257">在 [**屬性**] 視窗中，選擇右上方的 [**事件**]，然後按兩下 [**載入**的標籤] 右邊的文字方塊</span><span class="sxs-lookup"><span data-stu-id="256aa-257">In the **Properties** window choose **Events** at the top right, then double-click the text box to right of the **Loaded** label</span></span>

    ![主視窗屬性](~/ef6/media/mainwindowproperties.png)

-   <span data-ttu-id="256aa-259">也請在設計工具中按兩下 [儲存] 按鈕，以新增 [**儲存**] 按鈕的**Click**事件。</span><span class="sxs-lookup"><span data-stu-id="256aa-259">Also add the **Click** event for the **Save** button by double-clicking the Save button in the designer.</span></span> 

<span data-ttu-id="256aa-260">這會帶您前往表單的程式碼後置，我們現在會編輯程式碼，以使用 ProductCoNtext 來執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="256aa-260">This brings you to the code behind for the form, we'll now edit the code to use the ProductContext to perform data access.</span></span> <span data-ttu-id="256aa-261">更新 Mainwindow.xaml 的程式碼，如下所示。</span><span class="sxs-lookup"><span data-stu-id="256aa-261">Update the code for the MainWindow as shown below.</span></span>

<span data-ttu-id="256aa-262">程式碼會宣告**ProductCoNtext**的長時間執行實例。</span><span class="sxs-lookup"><span data-stu-id="256aa-262">The code declares a long-running instance of **ProductContext**.</span></span> <span data-ttu-id="256aa-263">**ProductCoNtext**物件可用來查詢和儲存資料至資料庫。</span><span class="sxs-lookup"><span data-stu-id="256aa-263">The **ProductContext** object is used to query and save data to the database.</span></span> <span data-ttu-id="256aa-264">然後會從覆寫的**OnClosing**方法呼叫**ProductCoNtext**實例上的**Dispose （）** 。</span><span class="sxs-lookup"><span data-stu-id="256aa-264">The **Dispose()** on the **ProductContext** instance is then called from the overridden **OnClosing** method.</span></span><span data-ttu-id="256aa-265"> 程式碼批註會提供程式碼用途的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="256aa-265"> The code comments provide details about what the code does.</span></span>

``` csharp
    using System.Data.Entity;
    using System.Linq;
    using System.Windows;

    namespace WPFwithEFSample
    {
        public partial class MainWindow : Window
        {
            private ProductContext _context = new ProductContext();
            public MainWindow()
            {
                InitializeComponent();
            }

            private void Window_Loaded(object sender, RoutedEventArgs e)
            {
                System.Windows.Data.CollectionViewSource categoryViewSource =
                    ((System.Windows.Data.CollectionViewSource)(this.FindResource("categoryViewSource")));

                // Load is an extension method on IQueryable,
                // defined in the System.Data.Entity namespace.
                // This method enumerates the results of the query,
                // similar to ToList but without creating a list.
                // When used with Linq to Entities this method
                // creates entity objects and adds them to the context.
                _context.Categories.Load();

                // After the data is loaded call the DbSet<T>.Local property
                // to use the DbSet<T> as a binding source.
                categoryViewSource.Source = _context.Categories.Local;
            }

            private void buttonSave_Click(object sender, RoutedEventArgs e)
            {
                // When you delete an object from the related entities collection
                // (in this case Products), the Entity Framework doesn’t mark
                // these child entities as deleted.
                // Instead, it removes the relationship between the parent and the child
                // by setting the parent reference to null.
                // So we manually have to delete the products
                // that have a Category reference set to null.

                // The following code uses LINQ to Objects
                // against the Local collection of Products.
                // The ToList call is required because otherwise the collection will be modified
                // by the Remove call while it is being enumerated.
                // In most other situations you can use LINQ to Objects directly
                // against the Local property without using ToList first.
                foreach (var product in _context.Products.Local.ToList())
                {
                    if (product.Category == null)
                    {
                        _context.Products.Remove(product);
                    }
                }

                _context.SaveChanges();
                // Refresh the grids so the database generated values show up.
                this.categoryDataGrid.Items.Refresh();
                this.productsDataGrid.Items.Refresh();
            }

            protected override void OnClosing(System.ComponentModel.CancelEventArgs e)
            {
                base.OnClosing(e);
                this._context.Dispose();
            }
        }

    }
```

## <a name="test-the-wpf-application"></a><span data-ttu-id="256aa-266">測試 WPF 應用程式</span><span class="sxs-lookup"><span data-stu-id="256aa-266">Test the WPF Application</span></span>

-   <span data-ttu-id="256aa-267">編譯並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="256aa-267">Compile and run the application.</span></span> <span data-ttu-id="256aa-268">如果您使用 Code First，您會看到為您建立**WPFwithEFSample ProductCoNtext**資料庫。</span><span class="sxs-lookup"><span data-stu-id="256aa-268">If you used Code First, then you will see that a **WPFwithEFSample.ProductContext** database is created for you.</span></span>
-   <span data-ttu-id="256aa-269">在上方方格中輸入類別目錄名稱，並在底部方格中的產品名稱*不輸入任何資料行，因為主鍵是由資料庫產生*。</span><span class="sxs-lookup"><span data-stu-id="256aa-269">Enter a category name in the top grid and product names in the bottom grid *Do not enter anything in ID columns, because the primary key is generated by the database*</span></span>

    ![具有新類別和產品的主視窗](~/ef6/media/screen1.png)

-   <span data-ttu-id="256aa-271">按 [**儲存**] 按鈕，將資料儲存至資料庫</span><span class="sxs-lookup"><span data-stu-id="256aa-271">Press the **Save** button to save the data to the database</span></span>

<span data-ttu-id="256aa-272">呼叫 DbCoNtext 的**SaveChanges （）** 之後，就會以資料庫產生的值填入識別碼。</span><span class="sxs-lookup"><span data-stu-id="256aa-272">After the call to DbContext’s **SaveChanges()**, the IDs are populated with the database generated values.</span></span> <span data-ttu-id="256aa-273">因為我們在**SaveChanges （）** 之後呼叫**Refresh （）** ，所以**DataGrid**控制項也會以新的值更新。</span><span class="sxs-lookup"><span data-stu-id="256aa-273">Because we called **Refresh()** after **SaveChanges()** the **DataGrid** controls are updated with the new values as well.</span></span>

![已填入識別碼的主視窗](~/ef6/media/screen2.png)

## <a name="additional-resources"></a><span data-ttu-id="256aa-275">其他資源</span><span class="sxs-lookup"><span data-stu-id="256aa-275">Additional Resources</span></span>

<span data-ttu-id="256aa-276">若要深入瞭解使用 WPF 將資料系結至集合的詳細資訊，請參閱 WPF 檔中的[這個主題](https://docs.microsoft.com/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections)。</span><span class="sxs-lookup"><span data-stu-id="256aa-276">To learn more about data binding to collections using WPF, see [this topic](https://docs.microsoft.com/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections) in the WPF documentation.</span></span>  
