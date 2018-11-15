---
title: 資料繫結與 WPF-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: e90d48e6-bea5-47ef-b756-7b89cce4daf0
ms.openlocfilehash: 1933988277d3be8fecc02fced3293f2b7f80c901
ms.sourcegitcommit: ae399f9f3d1bae2c446b552247bd3af3ca5a2cf9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/04/2018
ms.locfileid: "48575661"
---
# <a name="databinding-with-wpf"></a><span data-ttu-id="01478-102">使用 WPF 資料繫結</span><span class="sxs-lookup"><span data-stu-id="01478-102">Databinding with WPF</span></span>
<span data-ttu-id="01478-103">此逐步解說示範如何在 [主版詳細資料] 表單中的 WPF 控制項繫結至 POCO 型別。</span><span class="sxs-lookup"><span data-stu-id="01478-103">This step-by-step walkthrough shows how to bind POCO types to WPF controls in a “master-detail" form.</span></span> <span data-ttu-id="01478-104">應用程式會使用 Entity Framework Api 填入資料庫的資料物件、 追蹤變更，並將資料保存到資料庫。</span><span class="sxs-lookup"><span data-stu-id="01478-104">The application uses the Entity Framework APIs to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="01478-105">此模型會定義一個對多關聯性中參與的兩種類型：**分類**(主體\\主要) 和**產品**(相依\\詳細資料)。</span><span class="sxs-lookup"><span data-stu-id="01478-105">The model defines two types that participate in one-to-many relationship: **Category** (principal\\master) and **Product** (dependent\\detail).</span></span> <span data-ttu-id="01478-106">然後，Visual Studio 工具用來繫結至 WPF 控制項模型中定義的類型。</span><span class="sxs-lookup"><span data-stu-id="01478-106">Then, the Visual Studio tools are used to bind the types defined in the model to the WPF controls.</span></span> <span data-ttu-id="01478-107">WPF 資料繫結架構可讓相關的物件之間的瀏覽： 主版檢視中選取的資料列會導致對應的子資料更新的詳細資料檢視。</span><span class="sxs-lookup"><span data-stu-id="01478-107">The WPF data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="01478-108">在本逐步解說的程式碼清單的螢幕擷取畫面取自 Visual Studio 2013，但您可以完成這個逐步解說中的使用 Visual Studio 2012 或 Visual Studio 2010。</span><span class="sxs-lookup"><span data-stu-id="01478-108">The screen shots and code listings in this walkthrough are taken from Visual Studio 2013 but you can complete this walkthrough with Visual Studio 2012 or Visual Studio 2010.</span></span>

## <a name="use-the-object-option-for-creating-wpf-data-sources"></a><span data-ttu-id="01478-109">使用 'Object' 選項建立的 WPF 資料來源</span><span class="sxs-lookup"><span data-stu-id="01478-109">Use the 'Object' Option for Creating WPF Data Sources</span></span>

<span data-ttu-id="01478-110">我們用來建議使用與上一個版本的 Entity Framework**資料庫**選項時建立新的資料來源以使用 EF 設計工具建立的模型為基礎。</span><span class="sxs-lookup"><span data-stu-id="01478-110">With previous version of Entity Framework we used to recommend using the **Database** option when creating a new Data Source based on a model created with the EF Designer.</span></span> <span data-ttu-id="01478-111">這是因為設計工具會產生衍生自 ObjectContext 的內容和實體類別，衍生自 EntityObject。</span><span class="sxs-lookup"><span data-stu-id="01478-111">This was because the designer would generate a context that derived from ObjectContext and entity classes that derived from EntityObject.</span></span> <span data-ttu-id="01478-112">使用 [資料庫] 選項會幫助您撰寫最佳的程式碼與此 API 介面互動。</span><span class="sxs-lookup"><span data-stu-id="01478-112">Using the Database option would help you write the best code for interacting with this API surface.</span></span>

<span data-ttu-id="01478-113">Visual Studio 2012 和 Visual Studio 2013 的 EF 設計工具會產生與簡單的 POCO 實體類別是衍生自 DbContext 的內容。</span><span class="sxs-lookup"><span data-stu-id="01478-113">The EF Designers for Visual Studio 2012 and Visual Studio 2013 generate a context that derives from DbContext together with simple POCO entity classes.</span></span> <span data-ttu-id="01478-114">使用 Visual Studio 2010 中，我們建議使用 DbContext，稍後在本逐步解說中所述的程式碼產生範本來交換。</span><span class="sxs-lookup"><span data-stu-id="01478-114">With Visual Studio 2010 we recommend swapping to a code generation template that uses DbContext as described later in this walkthrough.</span></span>

<span data-ttu-id="01478-115">DbContext API 介面時您應該使用**物件**選項建立新的資料來源時，在此逐步解說中所示。</span><span class="sxs-lookup"><span data-stu-id="01478-115">When using the DbContext API surface you should use the **Object** option when creating a new Data Source, as shown in this walkthrough.</span></span>

<span data-ttu-id="01478-116">如有需要您可以[還原為基礎的 ObjectContext 的程式碼產生](~/ef6/modeling/designer/codegen/legacy-objectcontext.md)使用 EF 設計工具建立的模型。</span><span class="sxs-lookup"><span data-stu-id="01478-116">If needed, you can [revert to ObjectContext based code generation](~/ef6/modeling/designer/codegen/legacy-objectcontext.md) for models created with the EF Designer.</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="01478-117">必要條件</span><span class="sxs-lookup"><span data-stu-id="01478-117">Pre-Requisites</span></span>

<span data-ttu-id="01478-118">您必須有 Visual Studio 2013，才能完成此逐步解說安裝的 Visual Studio 2012 或 Visual Studio 2010。</span><span class="sxs-lookup"><span data-stu-id="01478-118">You need to have Visual Studio 2013, Visual Studio 2012 or Visual Studio 2010 installed to complete this walkthrough.</span></span>

<span data-ttu-id="01478-119">如果您使用 Visual Studio 2010，您也必須安裝 NuGet。</span><span class="sxs-lookup"><span data-stu-id="01478-119">If you are using Visual Studio 2010, you also have to install NuGet.</span></span> <span data-ttu-id="01478-120">如需詳細資訊，請參閱 <<c0> [ 安裝 NuGet](https://docs.microsoft.com/nuget/install-nuget-client-tools)。</span><span class="sxs-lookup"><span data-stu-id="01478-120">For more information, see [Installing NuGet](https://docs.microsoft.com/nuget/install-nuget-client-tools).</span></span>  

## <a name="create-the-application"></a><span data-ttu-id="01478-121">建立應用程式</span><span class="sxs-lookup"><span data-stu-id="01478-121">Create the Application</span></span>

-   <span data-ttu-id="01478-122">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="01478-122">Open Visual Studio</span></span>
-   <span data-ttu-id="01478-123">**檔案-&gt;新增-&gt;專案...**</span><span class="sxs-lookup"><span data-stu-id="01478-123">**File -&gt; New -&gt; Project….**</span></span>
-   <span data-ttu-id="01478-124">選取  **Windows**的左窗格中並**WPFApplication**在右窗格</span><span class="sxs-lookup"><span data-stu-id="01478-124">Select **Windows** in the left pane and **WPFApplication** in the right pane</span></span>
-   <span data-ttu-id="01478-125">請輸入**WPFwithEFSample**做為名稱</span><span class="sxs-lookup"><span data-stu-id="01478-125">Enter **WPFwithEFSample** as the name</span></span>
-   <span data-ttu-id="01478-126">選取 [確定]</span><span class="sxs-lookup"><span data-stu-id="01478-126">Select **OK**</span></span>

## <a name="install-the-entity-framework-nuget-package"></a><span data-ttu-id="01478-127">安裝 Entity Framework NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="01478-127">Install the Entity Framework NuGet package</span></span>

-   <span data-ttu-id="01478-128">在 [方案總管] 中，以滑鼠右鍵按一下**WinFormswithEFSample**專案</span><span class="sxs-lookup"><span data-stu-id="01478-128">In Solution Explorer, right-click on the **WinFormswithEFSample** project</span></span>
-   <span data-ttu-id="01478-129">選取**管理 NuGet 封裝...**</span><span class="sxs-lookup"><span data-stu-id="01478-129">Select **Manage NuGet Packages…**</span></span>
-   <span data-ttu-id="01478-130">在 [管理 NuGet 套件] 對話方塊中，選取**線上**索引標籤，然後選擇**EntityFramework**封裝</span><span class="sxs-lookup"><span data-stu-id="01478-130">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
-   <span data-ttu-id="01478-131">按一下 **安裝**</span><span class="sxs-lookup"><span data-stu-id="01478-131">Click **Install**</span></span>  
    >[!NOTE]
    > <span data-ttu-id="01478-132">除了 EntityFramework 組件也會加入 System.ComponentModel.DataAnnotations 的參考。</span><span class="sxs-lookup"><span data-stu-id="01478-132">In addition to the EntityFramework assembly a reference to System.ComponentModel.DataAnnotations is also added.</span></span> <span data-ttu-id="01478-133">如果專案具有 System.Data.Entity 的參考，則它將會移除安裝 EntityFramework 套件時。</span><span class="sxs-lookup"><span data-stu-id="01478-133">If the project has a reference to System.Data.Entity, then it will be removed when the EntityFramework package is installed.</span></span> <span data-ttu-id="01478-134">System.Data.Entity 組件不會再用於 Entity Framework 6 應用程式。</span><span class="sxs-lookup"><span data-stu-id="01478-134">The System.Data.Entity assembly is no longer used for Entity Framework 6 applications.</span></span>

## <a name="define-a-model"></a><span data-ttu-id="01478-135">定義模型</span><span class="sxs-lookup"><span data-stu-id="01478-135">Define a Model</span></span>

<span data-ttu-id="01478-136">在本逐步解說，您可以選擇實作模型，使用程式碼優先 」 或 「 EF 設計工具。</span><span class="sxs-lookup"><span data-stu-id="01478-136">In this walkthrough you can chose to implement a model using Code First or the EF Designer.</span></span> <span data-ttu-id="01478-137">完成其中兩個下列各節。</span><span class="sxs-lookup"><span data-stu-id="01478-137">Complete one of the two following sections.</span></span>

### <a name="option-1-define-a-model-using-code-first"></a><span data-ttu-id="01478-138">選項 1： 定義使用 Code First 模型</span><span class="sxs-lookup"><span data-stu-id="01478-138">Option 1: Define a Model using Code First</span></span>

<span data-ttu-id="01478-139">本節說明如何建立模型和其相關聯的資料庫使用 Code First。</span><span class="sxs-lookup"><span data-stu-id="01478-139">This section shows how to create a model and its associated database using Code First.</span></span> <span data-ttu-id="01478-140">請跳至下一節 (**選項 2： 定義模型使用 Database First)** 如果您想要使用 Database First 反轉設計您的模型使用 EF 設計工具，從資料庫</span><span class="sxs-lookup"><span data-stu-id="01478-140">Skip to the next section (**Option 2: Define a model using Database First)** if you would rather use Database First to reverse engineer your model from a database using the EF designer</span></span>

<span data-ttu-id="01478-141">使用 Code First 開發時您通常會開始撰寫定義概念 （網域） 模型的.NET Framework 類別。</span><span class="sxs-lookup"><span data-stu-id="01478-141">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span>

-   <span data-ttu-id="01478-142">將新類別加入**WPFwithEFSample:**</span><span class="sxs-lookup"><span data-stu-id="01478-142">Add a new class to the **WPFwithEFSample:**</span></span>
    -   <span data-ttu-id="01478-143">以滑鼠右鍵按一下專案名稱</span><span class="sxs-lookup"><span data-stu-id="01478-143">Right-click on the project name</span></span>
    -   <span data-ttu-id="01478-144">選取 **新增**，然後**新項目**</span><span class="sxs-lookup"><span data-stu-id="01478-144">Select **Add**, then **New Item**</span></span>
    -   <span data-ttu-id="01478-145">選取 **類別**，然後輸入**產品**的類別名稱</span><span class="sxs-lookup"><span data-stu-id="01478-145">Select **Class** and enter **Product** for the class name</span></span>
-   <span data-ttu-id="01478-146">取代**產品**類別定義為下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="01478-146">Replace the **Product** class definition with the following code:</span></span>

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

<span data-ttu-id="01478-147">**產品**屬性上的**類別目錄**類別並**類別**屬性**產品**類別為導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="01478-147">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="01478-148">在 Entity Framework 中，導覽屬性會提供瀏覽兩個實體類型之間的關聯性的方法。</span><span class="sxs-lookup"><span data-stu-id="01478-148">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="01478-149">除了定義實體，您需要定義類別，衍生自 DbContext，並公開 DbSet&lt;TEntity&gt;屬性。</span><span class="sxs-lookup"><span data-stu-id="01478-149">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="01478-150">DbSet&lt;TEntity&gt;屬性可讓知道您想要在模型中包含哪些的類型的內容。</span><span class="sxs-lookup"><span data-stu-id="01478-150">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="01478-151">DbContext 衍生型別的執行個體在執行階段，其中包含填入資料庫的資料的物件期間同時管理實體物件、 變更追蹤，和保存資料至資料庫。</span><span class="sxs-lookup"><span data-stu-id="01478-151">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

-   <span data-ttu-id="01478-152">加入新**ProductContext**類別到專案中的下列定義：</span><span class="sxs-lookup"><span data-stu-id="01478-152">Add a new **ProductContext** class to the project with the following definition:</span></span>

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

<span data-ttu-id="01478-153">編譯專案。</span><span class="sxs-lookup"><span data-stu-id="01478-153">Compile the project.</span></span>

### <a name="option-2-define-a-model-using-database-first"></a><span data-ttu-id="01478-154">選項 2： 定義使用 Database First 模型</span><span class="sxs-lookup"><span data-stu-id="01478-154">Option 2: Define a model using Database First</span></span>

<span data-ttu-id="01478-155">本節說明如何使用 Database First，進行反向工程，您使用 EF 設計工具，從資料庫的模型。</span><span class="sxs-lookup"><span data-stu-id="01478-155">This section shows how to use Database First to reverse engineer your model from a database using the EF designer.</span></span> <span data-ttu-id="01478-156">如果您已完成上一節 (**選項 1： 定義模型使用 Code First)**，然後略過本節並直接前往**消極式載入**一節。</span><span class="sxs-lookup"><span data-stu-id="01478-156">If you completed the previous section (**Option 1: Define a model using Code First)**, then skip this section and go straight to the **Lazy Loading** section.</span></span>

#### <a name="create-an-existing-database"></a><span data-ttu-id="01478-157">建立現有的資料庫</span><span class="sxs-lookup"><span data-stu-id="01478-157">Create an Existing Database</span></span>

<span data-ttu-id="01478-158">通常當您的目標將已建立，現有的資料庫，但在此逐步解說，我們需要建立可存取的資料庫。</span><span class="sxs-lookup"><span data-stu-id="01478-158">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="01478-159">Visual Studio 隨附安裝的資料庫伺服器是您已安裝的 Visual Studio 版本而有所不同：</span><span class="sxs-lookup"><span data-stu-id="01478-159">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="01478-160">如果您使用 Visual Studio 2010 將會建立 SQL Express 資料庫。</span><span class="sxs-lookup"><span data-stu-id="01478-160">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="01478-161">如果您使用 Visual Studio 2012，則您將會建立[LocalDB](https://msdn.microsoft.com/library/hh510202.aspx)資料庫。</span><span class="sxs-lookup"><span data-stu-id="01478-161">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) database.</span></span>

<span data-ttu-id="01478-162">接下來產生的資料庫。</span><span class="sxs-lookup"><span data-stu-id="01478-162">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="01478-163">**檢視-&gt;伺服器總管**</span><span class="sxs-lookup"><span data-stu-id="01478-163">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="01478-164">以滑鼠右鍵按一下**資料連線-&gt;新增連接...**</span><span class="sxs-lookup"><span data-stu-id="01478-164">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="01478-165">如果您尚未連線至資料庫從伺服器總管之前您必須選取 Microsoft SQL Server 做為資料來源</span><span class="sxs-lookup"><span data-stu-id="01478-165">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![變更資料來源](~/ef6/media/changedatasource.png)

-   <span data-ttu-id="01478-167">連接到 LocalDB 或 SQL Express，何者而定，您已安裝，然後輸入**產品**做為資料庫名稱</span><span class="sxs-lookup"><span data-stu-id="01478-167">Connect to either LocalDB or SQL Express, depending on which one you have installed, and enter **Products** as the database name</span></span>

    ![新增連線 LocalDB](~/ef6/media/addconnectionlocaldb.png)

    ![新增連線 Express](~/ef6/media/addconnectionexpress.png)

-   <span data-ttu-id="01478-170">選取  **確定**而且會要求您想要建立新資料庫，請選取**是**</span><span class="sxs-lookup"><span data-stu-id="01478-170">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![建立資料庫](~/ef6/media/createdatabase.png)

-   <span data-ttu-id="01478-172">新資料庫現在會出現在 [伺服器總管] 中，按一下滑鼠右鍵，然後選取**新查詢**</span><span class="sxs-lookup"><span data-stu-id="01478-172">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="01478-173">將下列 SQL 複製到新的查詢，然後以滑鼠右鍵按一下查詢並選取**Execute**</span><span class="sxs-lookup"><span data-stu-id="01478-173">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

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

#### <a name="reverse-engineer-model"></a><span data-ttu-id="01478-174">反向工程模型</span><span class="sxs-lookup"><span data-stu-id="01478-174">Reverse Engineer Model</span></span>

<span data-ttu-id="01478-175">我們將利用 Entity Framework Designer 中，也就是 Visual Studio 的一部分，以建立我們的模型。</span><span class="sxs-lookup"><span data-stu-id="01478-175">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="01478-176">**專案-&gt;加入新項目...**</span><span class="sxs-lookup"><span data-stu-id="01478-176">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="01478-177">選取 **資料**從左側功能表，然後**ADO.NET 實體資料模型**</span><span class="sxs-lookup"><span data-stu-id="01478-177">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="01478-178">請輸入**ProductModel**作為名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="01478-178">Enter **ProductModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="01478-179">這會啟動**Entity Data Model 精靈**</span><span class="sxs-lookup"><span data-stu-id="01478-179">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="01478-180">選取 **從資料庫產生**，按一下 **下一步**</span><span class="sxs-lookup"><span data-stu-id="01478-180">Select **Generate from Database** and click **Next**</span></span>

    ![選擇模型內容](~/ef6/media/choosemodelcontents.png)

-   <span data-ttu-id="01478-182">選取您建立第一個區段中的資料庫連接中，輸入**ProductContext**做為連接字串，然後按一下 [名稱**下一步]**</span><span class="sxs-lookup"><span data-stu-id="01478-182">Select the connection to the database you created in the first section, enter **ProductContext** as the name of the connection string and click **Next**</span></span>

    ![選擇您的連線](~/ef6/media/chooseyourconnection.png)

-   <span data-ttu-id="01478-184">按一下 匯入的所有資料表，並按一下 完成 '資料表旁的核取方塊</span><span class="sxs-lookup"><span data-stu-id="01478-184">Click the checkbox next to ‘Tables’ to import all tables and click ‘Finish’</span></span>

    ![選擇您的物件](~/ef6/media/chooseyourobjects.png)

<span data-ttu-id="01478-186">反向工程程序完成後新模型加入您的專案，並讓您檢視在 Entity Framework Designer 中開啟。</span><span class="sxs-lookup"><span data-stu-id="01478-186">Once the reverse engineer process completes the new model is added to your project and opened up for you to view in the Entity Framework Designer.</span></span> <span data-ttu-id="01478-187">App.config 檔案也已新增至您的專案與資料庫的連線詳細資料。</span><span class="sxs-lookup"><span data-stu-id="01478-187">An App.config file has also been added to your project with the connection details for the database.</span></span>

#### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="01478-188">Visual Studio 2010 中的其他步驟</span><span class="sxs-lookup"><span data-stu-id="01478-188">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="01478-189">如果您正在 Visual Studio 2010 中將需要更新使用 EF6 程式碼產生的 EF 設計工具。</span><span class="sxs-lookup"><span data-stu-id="01478-189">If you are working in Visual Studio 2010 then you will need to update the EF designer to use EF6 code generation.</span></span>

-   <span data-ttu-id="01478-190">以滑鼠右鍵按一下您的模型，在 EF 設計工具中的非空點，然後選取**加入的程式碼產生項目...**</span><span class="sxs-lookup"><span data-stu-id="01478-190">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="01478-191">選取 **線上範本**從左側的功能表，並搜尋**DbContext**</span><span class="sxs-lookup"><span data-stu-id="01478-191">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="01478-192">選取**EF 6.x 適用於 C 的 DbContext Generator\#，** 輸入**ProductsModel**做為名稱並按一下 [新增]</span><span class="sxs-lookup"><span data-stu-id="01478-192">Select the **EF 6.x DbContext Generator for C\#,** enter **ProductsModel** as the name and click Add</span></span>

#### <a name="updating-code-generation-for-data-binding"></a><span data-ttu-id="01478-193">更新資料繫結的程式碼產生</span><span class="sxs-lookup"><span data-stu-id="01478-193">Updating code generation for data binding</span></span>

<span data-ttu-id="01478-194">EF 從模型使用 T4 範本產生程式碼。</span><span class="sxs-lookup"><span data-stu-id="01478-194">EF generates code from your model using T4 templates.</span></span> <span data-ttu-id="01478-195">隨附於 Visual Studio，或從 Visual Studio 組件庫下載的範本供一般用途使用。</span><span class="sxs-lookup"><span data-stu-id="01478-195">The templates shipped with Visual Studio or downloaded from the Visual Studio gallery are intended for general purpose use.</span></span> <span data-ttu-id="01478-196">這表示從這些範本所產生的實體有簡單的 ICollection&lt;T&gt;屬性。</span><span class="sxs-lookup"><span data-stu-id="01478-196">This means that the entities generated from these templates have simple ICollection&lt;T&gt; properties.</span></span> <span data-ttu-id="01478-197">不過，進行資料繫結使用 WPF 時，適合使用**ObservableCollection**集合屬性，因此該 WPF 可以追蹤的集合所做的變更。</span><span class="sxs-lookup"><span data-stu-id="01478-197">However, when doing data binding using WPF it is desirable to use **ObservableCollection** for collection properties so that WPF can keep track of changes made to the collections.</span></span> <span data-ttu-id="01478-198">為了這個目的，我們會修改範本，以使用 ObservableCollection。</span><span class="sxs-lookup"><span data-stu-id="01478-198">To this end we will to modify the templates to use ObservableCollection.</span></span>

-   <span data-ttu-id="01478-199">開啟**方案總管**並尋找**ProductModel.edmx**檔案</span><span class="sxs-lookup"><span data-stu-id="01478-199">Open the **Solution Explorer** and find **ProductModel.edmx** file</span></span>
-   <span data-ttu-id="01478-200">尋找**ProductModel.tt**下 ProductModel.edmx 檔案會進行巢狀檔案</span><span class="sxs-lookup"><span data-stu-id="01478-200">Find the **ProductModel.tt** file which will be nested under the ProductModel.edmx file</span></span>

    ![WPF 產品模型範本](~/ef6/media/wpfproductmodeltemplate.png)

-   <span data-ttu-id="01478-202">按兩下 ProductModel.tt 檔案在 Visual Studio 編輯器中開啟它</span><span class="sxs-lookup"><span data-stu-id="01478-202">Double-click on the ProductModel.tt file to open it in the Visual Studio editor</span></span>
-   <span data-ttu-id="01478-203">尋找和取代出現兩次的 「**ICollection**"with"**ObservableCollection**"。</span><span class="sxs-lookup"><span data-stu-id="01478-203">Find and replace the two occurrences of “**ICollection**” with “**ObservableCollection**”.</span></span> <span data-ttu-id="01478-204">這些是大約位於線條 296 及 484。</span><span class="sxs-lookup"><span data-stu-id="01478-204">These are located approximately at lines 296 and 484.</span></span>
-   <span data-ttu-id="01478-205">尋找和取代第一個出現的 「**HashSet**"with"**ObservableCollection**"。</span><span class="sxs-lookup"><span data-stu-id="01478-205">Find and replace the first occurrence of “**HashSet**” with “**ObservableCollection**”.</span></span> <span data-ttu-id="01478-206">這項問題位於大約第 50 行。</span><span class="sxs-lookup"><span data-stu-id="01478-206">This occurrence is located approximately at line 50.</span></span> <span data-ttu-id="01478-207">**不這麼做**取代 HashSet 稍後在程式碼中找到的第二個執行個體。</span><span class="sxs-lookup"><span data-stu-id="01478-207">**Do not** replace the second occurrence of HashSet found later in the code.</span></span>
-   <span data-ttu-id="01478-208">尋找和取代的唯一相符項目 」**System.Collections.Generic**"with"**System.Collections.ObjectModel**"。</span><span class="sxs-lookup"><span data-stu-id="01478-208">Find and replace the only occurrence of “**System.Collections.Generic**” with “**System.Collections.ObjectModel**”.</span></span> <span data-ttu-id="01478-209">這是大約位於行 424。</span><span class="sxs-lookup"><span data-stu-id="01478-209">This is located approximately at line 424.</span></span>
-   <span data-ttu-id="01478-210">儲存 ProductModel.tt 檔案。</span><span class="sxs-lookup"><span data-stu-id="01478-210">Save the ProductModel.tt file.</span></span> <span data-ttu-id="01478-211">這應該會造成重新產生實體的程式碼。</span><span class="sxs-lookup"><span data-stu-id="01478-211">This should cause the code for entities to be regenerated.</span></span> <span data-ttu-id="01478-212">如果程式碼不會自動重新產生，然後以滑鼠右鍵按一下 ProductModel.tt，並選擇 [執行自訂工具]。</span><span class="sxs-lookup"><span data-stu-id="01478-212">If the code does not regenerate automatically, then right click on ProductModel.tt and choose “Run Custom Tool”.</span></span>

<span data-ttu-id="01478-213">如果您現在開啟 Category.cs 檔案 （這在 ProductModel.tt 之下巢狀），則您應該會看到產品集合中具有類型**ObservableCollection&lt;產品&gt;**。</span><span class="sxs-lookup"><span data-stu-id="01478-213">If you now open the Category.cs file (which is nested under ProductModel.tt) then you should see that the Products collection has the type **ObservableCollection&lt;Product&gt;**.</span></span>

<span data-ttu-id="01478-214">編譯專案。</span><span class="sxs-lookup"><span data-stu-id="01478-214">Compile the project.</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="01478-215">消極式載入</span><span class="sxs-lookup"><span data-stu-id="01478-215">Lazy Loading</span></span>

<span data-ttu-id="01478-216">**產品**屬性上的**類別目錄**類別並**類別**屬性**產品**類別為導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="01478-216">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="01478-217">在 Entity Framework 中，導覽屬性會提供瀏覽兩個實體類型之間的關聯性的方法。</span><span class="sxs-lookup"><span data-stu-id="01478-217">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="01478-218">EF 可讓您選擇載入相關的實體從資料庫自動第一次存取導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="01478-218">EF gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="01478-219">使用此類型的載入 （稱為消極式載入），請注意，第一次存取每個導覽屬性不同的查詢將會針對資料庫執行如果內容已不在內容中。</span><span class="sxs-lookup"><span data-stu-id="01478-219">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="01478-220">使用 POCO 實體類型時，EF 會建立衍生的 proxy 型別的執行個體在執行階段，然後再覆寫虛擬屬性類別，藉此加入載入攔截程序中的達到消極式載入。</span><span class="sxs-lookup"><span data-stu-id="01478-220">When using POCO entity types, EF achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="01478-221">若要取得相關物件的消極式載入，您必須宣告瀏覽做為屬性 getter**公用**並**虛擬**(**Overridable** Visual Basic 中)，且您的類別不可以是**密封**(**NotOverridable** Visual Basic 中)。</span><span class="sxs-lookup"><span data-stu-id="01478-221">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and you class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="01478-222">使用資料庫時的第一個導覽屬性會自動進行虛擬化來啟用消極式載入。</span><span class="sxs-lookup"><span data-stu-id="01478-222">When using Database First navigation properties are automatically made virtual to enable lazy loading.</span></span> <span data-ttu-id="01478-223">第一個程式碼區段中，我們選擇基於相同原因，請瀏覽屬性虛擬</span><span class="sxs-lookup"><span data-stu-id="01478-223">In the Code First section we chose to make the navigation properties virtual for the same reason</span></span>

## <a name="bind-object-to-controls"></a><span data-ttu-id="01478-224">物件繫結至控制項</span><span class="sxs-lookup"><span data-stu-id="01478-224">Bind Object to Controls</span></span>

<span data-ttu-id="01478-225">加入做為資料來源，此 WPF 應用程式模型中所定義的類別。</span><span class="sxs-lookup"><span data-stu-id="01478-225">Add the classes that are defined in the model as data sources for this WPF application.</span></span>

-   <span data-ttu-id="01478-226">按兩下**MainWindow.xaml**在 [方案總管] 來開啟主表單</span><span class="sxs-lookup"><span data-stu-id="01478-226">Double-click **MainWindow.xaml** in Solution Explorer to open the main form</span></span>
-   <span data-ttu-id="01478-227">從主功能表中，選取**專案-&gt;加入新的資料來源...**</span><span class="sxs-lookup"><span data-stu-id="01478-227">From the main menu, select **Project -&gt; Add New Data Source …**</span></span>
    <span data-ttu-id="01478-228">(在 Visual Studio 2010 中，您必須選取**的資料-&gt;加入新資料來源...**)</span><span class="sxs-lookup"><span data-stu-id="01478-228">(in Visual Studio 2010, you need to select **Data -&gt; Add New Data Source…**)</span></span>
-   <span data-ttu-id="01478-229">在 選擇資料來源 Typewindow 中，選取**物件**，按一下 **下一步**</span><span class="sxs-lookup"><span data-stu-id="01478-229">In the Choose a Data Source Typewindow, select **Object** and click **Next**</span></span>
-   <span data-ttu-id="01478-230">在 [選取資料物件] 對話方塊中，展開**WPFwithEFSample**兩個時間和選取**類別**</span><span class="sxs-lookup"><span data-stu-id="01478-230">In the Select the Data Objects dialog, unfold the **WPFwithEFSample** two times and select **Category**</span></span>  
    <span data-ttu-id="01478-231">*若要選取不需要**產品**資料來源，因為我們會透過**產品**的屬性**分類**資料來源*</span><span class="sxs-lookup"><span data-stu-id="01478-231">*There is no need to select the **Product** data source, because we will get to it through the **Product**’s property on the **Category** data source*</span></span>  

    ![選取資料物件](~/ef6/media/selectdataobjects.png)

-   <span data-ttu-id="01478-233">按一下 **完成。**</span><span class="sxs-lookup"><span data-stu-id="01478-233">Click **Finish.**</span></span>
-   <span data-ttu-id="01478-234">[資料來源] 視窗會開啟 [MainWindow.xaml] 視窗旁邊*如果未顯示 [資料來源] 視窗，選取\*\*檢視-&gt;其他 Windows-&gt;資料來源*\*\*</span><span class="sxs-lookup"><span data-stu-id="01478-234">The Data Sources window is opened next to the MainWindow.xaml window *If the Data Sources window is not showing up, select **View -&gt; Other Windows-&gt; Data Sources***</span></span>
-   <span data-ttu-id="01478-235">按 釘選 圖示，讓資料來源 視窗不會不會自動隱藏。</span><span class="sxs-lookup"><span data-stu-id="01478-235">Press the pin icon, so the Data Sources window does not auto hide.</span></span> <span data-ttu-id="01478-236">您可能需要按 [重新整理] 按鈕，如果視窗已顯示。</span><span class="sxs-lookup"><span data-stu-id="01478-236">You may need to hit the refresh button if the window was already visible.</span></span>

    ![Data Sources](~/ef6/media/datasources.png)

-   <span data-ttu-id="01478-238">選取 **分類**資料來源，並將它拖曳到表單上。</span><span class="sxs-lookup"><span data-stu-id="01478-238">Select the **Category** data source and drag it on the form.</span></span>

<span data-ttu-id="01478-239">下列狀況時拖曳的項目此來源：</span><span class="sxs-lookup"><span data-stu-id="01478-239">The following happened when we dragged this source:</span></span>

-   <span data-ttu-id="01478-240">**CategoryViewSource**資源並**categoryDataGrid**控制項已加入至 XAML</span><span class="sxs-lookup"><span data-stu-id="01478-240">The **categoryViewSource** resource and the **categoryDataGrid** control were added to XAML</span></span> 
-   <span data-ttu-id="01478-241">在父格線項目上的 DataContext 屬性設定為"{StaticResource **categoryViewSource** }"。</span><span class="sxs-lookup"><span data-stu-id="01478-241">The DataContext property on the parent Grid element was set to "{StaticResource **categoryViewSource** }".</span></span> <span data-ttu-id="01478-242">**CategoryViewSource**資源做為外部的繫結來源\\父格線項目。</span><span class="sxs-lookup"><span data-stu-id="01478-242">The **categoryViewSource** resource serves as a binding source for the outer\\parent Grid element.</span></span> <span data-ttu-id="01478-243">內部的格線項目然後繼承的 DataContext 值從父代 （categoryDataGrid 的 ItemsSource 屬性設定為"{Binding}"） 的方格</span><span class="sxs-lookup"><span data-stu-id="01478-243">The inner Grid elements then inherit the DataContext value from the parent Grid (the categoryDataGrid’s ItemsSource property is set to "{Binding}")</span></span>

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

## <a name="adding-a-details-grid"></a><span data-ttu-id="01478-244">加入詳細資料方格</span><span class="sxs-lookup"><span data-stu-id="01478-244">Adding a Details Grid</span></span>

<span data-ttu-id="01478-245">既然我們已經讓我們來顯示類別目錄方格加入顯示相關聯的產品詳細資料方格。</span><span class="sxs-lookup"><span data-stu-id="01478-245">Now that we have a grid to display Categories let's add a details grid to display the associated Products.</span></span>

-   <span data-ttu-id="01478-246">選取 **產品**屬性從底下**分類**資料來源，並將它拖曳到表單上。</span><span class="sxs-lookup"><span data-stu-id="01478-246">Select the **Products** property from under the **Category** data source and drag it on the form.</span></span>
    -   <span data-ttu-id="01478-247">**CategoryProductsViewSource**資源並**productDataGrid**方格會新增至 XAML</span><span class="sxs-lookup"><span data-stu-id="01478-247">The **categoryProductsViewSource** resource and **productDataGrid** grid are added to XAML</span></span>
    -   <span data-ttu-id="01478-248">此資源的繫結路徑設定為產品</span><span class="sxs-lookup"><span data-stu-id="01478-248">The binding path for this resource is set to Products</span></span>
    -   <span data-ttu-id="01478-249">WPF 資料繫結架構可確保與所選分類的產品只有顯示在**productDataGrid**</span><span class="sxs-lookup"><span data-stu-id="01478-249">WPF data-binding framework ensures that only Products related to the selected Category show up in **productDataGrid**</span></span>
-   <span data-ttu-id="01478-250">從 工具箱 拖曳\*\* 按鈕\*\*入表單。</span><span class="sxs-lookup"><span data-stu-id="01478-250">From the Toolbox, drag **Button** on to the form.</span></span> <span data-ttu-id="01478-251">設定**名稱**屬性設**buttonSave**並**內容**屬性設**儲存**。</span><span class="sxs-lookup"><span data-stu-id="01478-251">Set the **Name** property to **buttonSave** and the **Content** property to **Save**.</span></span>

<span data-ttu-id="01478-252">表單看起來應該如下所示：</span><span class="sxs-lookup"><span data-stu-id="01478-252">The form should look similar to this:</span></span>

![Designer](~/ef6/media/designer.png) 

## <a name="add-code-that-handles-data-interaction"></a><span data-ttu-id="01478-254">將會處理與資料互動的程式碼</span><span class="sxs-lookup"><span data-stu-id="01478-254">Add Code that Handles Data Interaction</span></span>

<span data-ttu-id="01478-255">就可以開始將一些事件處理常式新增至主視窗。</span><span class="sxs-lookup"><span data-stu-id="01478-255">It's time to add some event handlers to the main window.</span></span>

-   <span data-ttu-id="01478-256">在 XAML 視窗中，按一下**&lt;視窗**項目，這會選取的主視窗</span><span class="sxs-lookup"><span data-stu-id="01478-256">In the XAML window, click on the **&lt;Window** element, this selects the main window</span></span>
-   <span data-ttu-id="01478-257">在 **屬性**視窗中選擇**事件**右上方，然後按兩下右側的文字方塊**Loaded**標籤</span><span class="sxs-lookup"><span data-stu-id="01478-257">In the **Properties** window choose **Events** at the top right, then double-click the text box to right of the **Loaded** label</span></span>

    ![主視窗屬性](~/ef6/media/mainwindowproperties.png)

-   <span data-ttu-id="01478-259">也加入**按一下 **事件**儲存**按兩下設計工具中的 儲存 按鈕的按鈕。</span><span class="sxs-lookup"><span data-stu-id="01478-259">Also add the **Click** event for the **Save** button by double-clicking the Save button in the designer.</span></span> 

<span data-ttu-id="01478-260">這將帶您前往程式碼後置表單，我們現在要編輯的程式碼，用以 ProductContext 執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="01478-260">This brings you to the code behind for the form, we'll now edit the code to use the ProductContext to perform data access.</span></span> <span data-ttu-id="01478-261">更新 MainWindow 的程式碼，如下所示。</span><span class="sxs-lookup"><span data-stu-id="01478-261">Update the code for the MainWindow as shown below.</span></span>

<span data-ttu-id="01478-262">程式碼會宣告的長時間執行執行個體**ProductContext**。</span><span class="sxs-lookup"><span data-stu-id="01478-262">The code declares a long-running instance of **ProductContext**.</span></span> <span data-ttu-id="01478-263">**ProductContext**物件用來查詢，並將資料儲存至資料庫。</span><span class="sxs-lookup"><span data-stu-id="01478-263">The **ProductContext** object is used to query and save data to the database.</span></span> <span data-ttu-id="01478-264">**Dispose （)** 上**ProductContext**執行個體然後，呼叫來自覆寫**OnClosing**方法。</span><span class="sxs-lookup"><span data-stu-id="01478-264">The **Dispose()** on the **ProductContext** instance is then called from the overridden **OnClosing** method.</span></span> <span data-ttu-id="01478-265">程式碼註解提供程式碼所執行的作業的相關詳細資料。</span><span class="sxs-lookup"><span data-stu-id="01478-265">The code comments provide details about what the code does.</span></span>

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

## <a name="test-the-wpf-application"></a><span data-ttu-id="01478-266">測試 WPF 應用程式</span><span class="sxs-lookup"><span data-stu-id="01478-266">Test the WPF Application</span></span>

-   <span data-ttu-id="01478-267">編譯並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="01478-267">Compile and run the application.</span></span> <span data-ttu-id="01478-268">如果您使用 Code First，則您會看到**WPFwithEFSample.ProductContext**為您建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="01478-268">If you used Code First, then you will see that a **WPFwithEFSample.ProductContext** database is created for you.</span></span>
-   <span data-ttu-id="01478-269">在下方方格中的最上層方格和產品名稱中輸入類別名稱*輸入不是任何項目識別碼資料行，因為資料庫會產生主要金鑰*</span><span class="sxs-lookup"><span data-stu-id="01478-269">Enter a category name in the top grid and product names in the bottom grid *Do not enter anything in ID columns, because the primary key is generated by the database*</span></span>

    ![與新的分類和產品的主視窗](~/ef6/media/screen1.png)

-   <span data-ttu-id="01478-271">按下**儲存** 按鈕，將資料儲存至資料庫</span><span class="sxs-lookup"><span data-stu-id="01478-271">Press the **Save** button to save the data to the database</span></span>

<span data-ttu-id="01478-272">DbContext 的呼叫後方**savechanges （)**，識別碼會填入資料庫產生值。</span><span class="sxs-lookup"><span data-stu-id="01478-272">After the call to DbContext’s **SaveChanges()**, the IDs are populated with the database generated values.</span></span> <span data-ttu-id="01478-273">因為我們會呼叫**Refresh()** 之後**savechanges （)** **DataGrid**控制項會更新含有新值。</span><span class="sxs-lookup"><span data-stu-id="01478-273">Because we called **Refresh()** after **SaveChanges()** the **DataGrid** controls are updated with the new values as well.</span></span>

![具有識別碼填入的主視窗](~/ef6/media/screen2.png)

## <a name="additional-resources"></a><span data-ttu-id="01478-275">其他資源</span><span class="sxs-lookup"><span data-stu-id="01478-275">Additional Resources</span></span>

<span data-ttu-id="01478-276">若要深入了解集合使用 WPF 資料繫結，請參閱[本主題](https://docs.microsoft.com/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections)WPF 文件中。</span><span class="sxs-lookup"><span data-stu-id="01478-276">To learn more about data binding to collections using WPF, see [this topic](https://docs.microsoft.com/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections) in the WPF documentation.</span></span>  
