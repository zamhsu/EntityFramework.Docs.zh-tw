---
title: 與 WPF 資料繫結 - EF6
author: divega
ms.date: 04/02/2020
ms.assetid: e90d48e6-bea7785-47ef-b756-7b89cce4daf0
ms.openlocfilehash: 6908e2a7597d0c199620c6015ed3ea06226c5ea9
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80639149"
---
> [!IMPORTANT]
> <span data-ttu-id="c1ce0-102">**這個文件只有在 .NET 框架上的 WPF 上有效**</span><span class="sxs-lookup"><span data-stu-id="c1ce0-102">**This document is valid for WPF on the .NET Framework only**</span></span>
>
> <span data-ttu-id="c1ce0-103">本文件介紹 .NET 框架上 WPF 的數據綁定。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-103">This document describes databinding for WPF on the .NET Framework.</span></span> <span data-ttu-id="c1ce0-104">對於新的 .NET 核心項目,我們建議您使用[EF Core](/ef/core)而不是實體框架 6。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-104">For new .NET Core projects, we recommend you use [EF Core](/ef/core) instead of Entity Framework 6.</span></span> <span data-ttu-id="c1ce0-105">EF Core 中的數據綁定文檔在["問題#778"](https://github.com/dotnet/EntityFramework.Docs/issues/778)中跟蹤。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-105">The documentation for databinding in EF Core is tracked in [Issue #778](https://github.com/dotnet/EntityFramework.Docs/issues/778).</span></span>

# <a name="databinding-with-wpf"></a><span data-ttu-id="c1ce0-106">使用 WPF 的資料繫結</span><span class="sxs-lookup"><span data-stu-id="c1ce0-106">Databinding with WPF</span></span>
<span data-ttu-id="c1ce0-107">此分步演練演示如何以「主詳細資訊」形式將 POCO 類型綁定到 WPF 控件。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-107">This step-by-step walkthrough shows how to bind POCO types to WPF controls in a “master-detail" form.</span></span> <span data-ttu-id="c1ce0-108">應用程式使用實體框架 API 使用資料庫中的數據填充物件、跟蹤更改並將數據儲存到資料庫中。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-108">The application uses the Entity Framework APIs to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="c1ce0-109">模型定義了參與一對多關係的兩種類型:**類別**\\(主體主控形狀)**和產品**(相關\\詳細資訊)。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-109">The model defines two types that participate in one-to-many relationship: **Category** (principal\\master) and **Product** (dependent\\detail).</span></span> <span data-ttu-id="c1ce0-110">然後,Visual Studio 工具用於將模型中定義的類型綁定到 WPF 控制件。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-110">Then, the Visual Studio tools are used to bind the types defined in the model to the WPF controls.</span></span> <span data-ttu-id="c1ce0-111">WPF 資料繫結框架支援相關物件之間的導航:選擇主檢視中的行會導致詳細檢視使用相應的子資料進行更新。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-111">The WPF data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="c1ce0-112">這個演練中的螢幕截圖和代碼清單取自 Visual Studio 2013,但您可以使用 Visual Studio 2012 或 Visual Studio 2010 完成本演練。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-112">The screen shots and code listings in this walkthrough are taken from Visual Studio 2013 but you can complete this walkthrough with Visual Studio 2012 or Visual Studio 2010.</span></span>

## <a name="use-the-object-option-for-creating-wpf-data-sources"></a><span data-ttu-id="c1ce0-113">使用物件選項建立 WPF 資料來源</span><span class="sxs-lookup"><span data-stu-id="c1ce0-113">Use the 'Object' Option for Creating WPF Data Sources</span></span>

<span data-ttu-id="c1ce0-114">對於以前版本的實體框架,我們曾建議在基於使用 EF 設計器創建的模型創建新數據來源時使用**資料庫**選項。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-114">With previous version of Entity Framework we used to recommend using the **Database** option when creating a new Data Source based on a model created with the EF Designer.</span></span> <span data-ttu-id="c1ce0-115">這是因為設計器將生成從ObjectContext派生的上下文和派生自實體物件的實體類。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-115">This was because the designer would generate a context that derived from ObjectContext and entity classes that derived from EntityObject.</span></span> <span data-ttu-id="c1ce0-116">使用"資料庫"選項將説明您編寫用於與此 API 曲面互動的最佳程式碼。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-116">Using the Database option would help you write the best code for interacting with this API surface.</span></span>

<span data-ttu-id="c1ce0-117">2012 年 Visual Studio 的 EF 設計人員和 2013 年可視化工作室生成源自 DbContext 的上下文以及簡單的 POCO 實體類。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-117">The EF Designers for Visual Studio 2012 and Visual Studio 2013 generate a context that derives from DbContext together with simple POCO entity classes.</span></span> <span data-ttu-id="c1ce0-118">使用 Visual Studio 2010,我們建議交換到代碼生成範本,該範本使用 DbContext,如本演練後面的所述。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-118">With Visual Studio 2010 we recommend swapping to a code generation template that uses DbContext as described later in this walkthrough.</span></span>

<span data-ttu-id="c1ce0-119">使用 DbContext API 曲面時,應在建立新資料來源時使用**Object**選項,如本演練所示。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-119">When using the DbContext API surface you should use the **Object** option when creating a new Data Source, as shown in this walkthrough.</span></span>

<span data-ttu-id="c1ce0-120">如果需要,您可以為使用 EF 設計器建立的模型[還原到基於 ObjectContext 的程式碼產生](~/ef6/modeling/designer/codegen/legacy-objectcontext.md)。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-120">If needed, you can [revert to ObjectContext based code generation](~/ef6/modeling/designer/codegen/legacy-objectcontext.md) for models created with the EF Designer.</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="c1ce0-121">必要條件</span><span class="sxs-lookup"><span data-stu-id="c1ce0-121">Pre-Requisites</span></span>

<span data-ttu-id="c1ce0-122">您需要安裝 Visual Studio 2013、Visual Studio 2012 或 Visual Studio 2010 才能完成本演練。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-122">You need to have Visual Studio 2013, Visual Studio 2012 or Visual Studio 2010 installed to complete this walkthrough.</span></span>

<span data-ttu-id="c1ce0-123">如果您使用的是 Visual Studio 2010,則還必須安裝 NuGet。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-123">If you are using Visual Studio 2010, you also have to install NuGet.</span></span> <span data-ttu-id="c1ce0-124">有關詳細資訊,請參閱安裝[NuGet](https://docs.microsoft.com/nuget/install-nuget-client-tools)。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-124">For more information, see [Installing NuGet](https://docs.microsoft.com/nuget/install-nuget-client-tools).</span></span>  

## <a name="create-the-application"></a><span data-ttu-id="c1ce0-125">建立應用程式</span><span class="sxs-lookup"><span data-stu-id="c1ce0-125">Create the Application</span></span>

-   <span data-ttu-id="c1ce0-126">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1ce0-126">Open Visual Studio</span></span>
-   <span data-ttu-id="c1ce0-127">**檔案&gt;&gt;- 新增 - 專案...**</span><span class="sxs-lookup"><span data-stu-id="c1ce0-127">**File -&gt; New -&gt; Project….**</span></span>
-   <span data-ttu-id="c1ce0-128">在左邊窗格中選擇 **Windows,** 在右邊窗格中選擇 **WPF 應用程式**</span><span class="sxs-lookup"><span data-stu-id="c1ce0-128">Select **Windows** in the left pane and **WPFApplication** in the right pane</span></span>
-   <span data-ttu-id="c1ce0-129">輸入 **WPF 與 EFSample** 作為名稱</span><span class="sxs-lookup"><span data-stu-id="c1ce0-129">Enter **WPFwithEFSample** as the name</span></span>
-   <span data-ttu-id="c1ce0-130">選擇 **"確定"**</span><span class="sxs-lookup"><span data-stu-id="c1ce0-130">Select **OK**</span></span>

## <a name="install-the-entity-framework-nuget-package"></a><span data-ttu-id="c1ce0-131">安裝實體框架 NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="c1ce0-131">Install the Entity Framework NuGet package</span></span>

-   <span data-ttu-id="c1ce0-132">在解決方案資源管理員中,右鍵按**下 WinForms 與 EFSample**專案</span><span class="sxs-lookup"><span data-stu-id="c1ce0-132">In Solution Explorer, right-click on the **WinFormswithEFSample** project</span></span>
-   <span data-ttu-id="c1ce0-133">選擇 **"管理 NuGet 包..."...**</span><span class="sxs-lookup"><span data-stu-id="c1ce0-133">Select **Manage NuGet Packages…**</span></span>
-   <span data-ttu-id="c1ce0-134">在「管理 NuGet」 套件「對話框中,選擇 **」連線**「工具」選項卡並選擇 **「實體框架**」套件</span><span class="sxs-lookup"><span data-stu-id="c1ce0-134">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
-   <span data-ttu-id="c1ce0-135">單擊"**安裝"**</span><span class="sxs-lookup"><span data-stu-id="c1ce0-135">Click **Install**</span></span>  
    >[!NOTE]
    > <span data-ttu-id="c1ce0-136">除了實體框架程式集外,還添加了對 System.元件模型.Data註釋的引用。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-136">In addition to the EntityFramework assembly a reference to System.ComponentModel.DataAnnotations is also added.</span></span> <span data-ttu-id="c1ce0-137">如果專案具有對 System.Data.Entity 的引用,則在安裝實體框架包時將刪除該專案。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-137">If the project has a reference to System.Data.Entity, then it will be removed when the EntityFramework package is installed.</span></span> <span data-ttu-id="c1ce0-138">系統.Data.實體程式集不再用於實體框架6應用程式。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-138">The System.Data.Entity assembly is no longer used for Entity Framework 6 applications.</span></span>

## <a name="define-a-model"></a><span data-ttu-id="c1ce0-139">定義模型</span><span class="sxs-lookup"><span data-stu-id="c1ce0-139">Define a Model</span></span>

<span data-ttu-id="c1ce0-140">在本演練中,您可以選擇使用代碼優先或 EF 設計器實現模型。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-140">In this walkthrough you can chose to implement a model using Code First or the EF Designer.</span></span> <span data-ttu-id="c1ce0-141">完成以下兩節之一。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-141">Complete one of the two following sections.</span></span>

### <a name="option-1-define-a-model-using-code-first"></a><span data-ttu-id="c1ce0-142">選項 1: 首先使用程式碼定義模型</span><span class="sxs-lookup"><span data-stu-id="c1ce0-142">Option 1: Define a Model using Code First</span></span>

<span data-ttu-id="c1ce0-143">本節演示如何使用 Code First 創建模型及其關聯的資料庫。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-143">This section shows how to create a model and its associated database using Code First.</span></span> <span data-ttu-id="c1ce0-144">如果您希望使用資料庫優先使用 EF 設計器從資料庫反向專案模型,請跳到下一部分(**選項 2:使用資料庫優先定義模型)**</span><span class="sxs-lookup"><span data-stu-id="c1ce0-144">Skip to the next section (**Option 2: Define a model using Database First)** if you would rather use Database First to reverse engineer your model from a database using the EF designer</span></span>

<span data-ttu-id="c1ce0-145">使用代碼優先開發時,通常先編寫定義概念(網域)模型的 .NET 框架類。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-145">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span>

-   <span data-ttu-id="c1ce0-146">向 **WPFEFSample**新增新類:</span><span class="sxs-lookup"><span data-stu-id="c1ce0-146">Add a new class to the **WPFwithEFSample:**</span></span>
    -   <span data-ttu-id="c1ce0-147">右鍵按下項目名稱</span><span class="sxs-lookup"><span data-stu-id="c1ce0-147">Right-click on the project name</span></span>
    -   <span data-ttu-id="c1ce0-148">選擇 **'新增**',然後**選擇'新增專案**'</span><span class="sxs-lookup"><span data-stu-id="c1ce0-148">Select **Add**, then **New Item**</span></span>
    -   <span data-ttu-id="c1ce0-149">選擇**類別類別的類別** **Product** </span><span class="sxs-lookup"><span data-stu-id="c1ce0-149">Select **Class** and enter **Product** for the class name</span></span>
-   <span data-ttu-id="c1ce0-150">將 **產品** 類別定義為以下代碼:</span><span class="sxs-lookup"><span data-stu-id="c1ce0-150">Replace the **Product** class definition with the following code:</span></span>

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

<span data-ttu-id="c1ce0-151">**產品**類別的 **「產品」** 類別**類別屬性**是導航屬性。 **Products**</span><span class="sxs-lookup"><span data-stu-id="c1ce0-151">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="c1ce0-152">在實體框架中,導航屬性提供了一種導航兩種實體類型之間的關係的方法。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-152">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="c1ce0-153">除了定義實體之外,還需要定義派生自 DbContext 並公開&lt;DbSet&gt;TEntity 屬性的類。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-153">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="c1ce0-154">DbSet&lt;TEntity&gt;屬性讓上下文知道要在模型中包括哪些類型。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-154">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="c1ce0-155">DbContext 衍生類型的實體在執行時管理實體物件,其中包括將對象與資料庫中的數據填充、更改追蹤和將資料儲存到資料庫。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-155">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

-   <span data-ttu-id="c1ce0-156">新增新的專案加入新的專案的**ProductContext**類別:</span><span class="sxs-lookup"><span data-stu-id="c1ce0-156">Add a new **ProductContext** class to the project with the following definition:</span></span>

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

<span data-ttu-id="c1ce0-157">編譯專案。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-157">Compile the project.</span></span>

### <a name="option-2-define-a-model-using-database-first"></a><span data-ttu-id="c1ce0-158">選項 2: 首先使用資料庫定義模型</span><span class="sxs-lookup"><span data-stu-id="c1ce0-158">Option 2: Define a model using Database First</span></span>

<span data-ttu-id="c1ce0-159">本節演示如何使用資料庫 First 使用 EF 設計器從資料庫反向工程模型。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-159">This section shows how to use Database First to reverse engineer your model from a database using the EF designer.</span></span> <span data-ttu-id="c1ce0-160">如果完成了上一節(**選項 1:使用代碼優先定義模型),** 則跳過此部分並直接轉到 **「延遲載入**」部分。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-160">If you completed the previous section (**Option 1: Define a model using Code First)**, then skip this section and go straight to the **Lazy Loading** section.</span></span>

#### <a name="create-an-existing-database"></a><span data-ttu-id="c1ce0-161">建立現有資料庫</span><span class="sxs-lookup"><span data-stu-id="c1ce0-161">Create an Existing Database</span></span>

<span data-ttu-id="c1ce0-162">通常,當您將現有資料庫作為目標時,該資料庫將創建,但本演練需要創建要訪問的資料庫。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-162">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="c1ce0-163">與 Visual Studio 一起安裝的資料庫伺服器因已安裝的 Visual Studio 版本而異:</span><span class="sxs-lookup"><span data-stu-id="c1ce0-163">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="c1ce0-164">如果您使用的是 Visual Studio 2010,您將創建一個 SQL Express 資料庫。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-164">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="c1ce0-165">如果您使用的是 Visual Studio 2012,則您將創建一個[本地資料庫](https://msdn.microsoft.com/library/hh510202.aspx)資料庫。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-165">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) database.</span></span>

<span data-ttu-id="c1ce0-166">讓我們繼續生成資料庫。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-166">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="c1ce0-167">**檢視&gt;- 伺服器資源管理員**</span><span class="sxs-lookup"><span data-stu-id="c1ce0-167">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="c1ce0-168">右鍵按**下 資料&gt;連線 - 新增連線...**</span><span class="sxs-lookup"><span data-stu-id="c1ce0-168">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="c1ce0-169">如果尚未從伺服器資源管理員連接到資料庫,則需要選擇 Microsoft SQL Server 作為資料來源</span><span class="sxs-lookup"><span data-stu-id="c1ce0-169">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![變更資料來源](~/ef6/media/changedatasource.png)

-   <span data-ttu-id="c1ce0-171">連接到本地資料庫或 SQL Express,具體取決於已安裝的哪個,然後輸入 **「產品**」作為資料庫名稱</span><span class="sxs-lookup"><span data-stu-id="c1ce0-171">Connect to either LocalDB or SQL Express, depending on which one you have installed, and enter **Products** as the database name</span></span>

    ![新增連線本地資料庫](~/ef6/media/addconnectionlocaldb.png)

    ![新增連線快訊](~/ef6/media/addconnectionexpress.png)

-   <span data-ttu-id="c1ce0-174">選擇 **「 確定」** 系統會詢問您要建立新資料庫,請選擇「**是**」</span><span class="sxs-lookup"><span data-stu-id="c1ce0-174">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![建立資料庫](~/ef6/media/createdatabase.png)

-   <span data-ttu-id="c1ce0-176">新数据庫現在將顯示在伺服器資源管理器中,右鍵單擊它並選擇 **"新查詢"**</span><span class="sxs-lookup"><span data-stu-id="c1ce0-176">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="c1ce0-177">將以下 SQL 複製到新查詢中,然後右鍵單擊查詢並選擇 **"執行"**</span><span class="sxs-lookup"><span data-stu-id="c1ce0-177">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

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

#### <a name="reverse-engineer-model"></a><span data-ttu-id="c1ce0-178">反向專案模型</span><span class="sxs-lookup"><span data-stu-id="c1ce0-178">Reverse Engineer Model</span></span>

<span data-ttu-id="c1ce0-179">我們將利用實體框架設計器(作為 Visual Studio 的一部分)創建我們的模型。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-179">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="c1ce0-180">**專案&gt;- 新增新項目...**</span><span class="sxs-lookup"><span data-stu-id="c1ce0-180">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="c1ce0-181">從左方選單中選擇**資料**,然後**ADO.NET 實體資料模型**</span><span class="sxs-lookup"><span data-stu-id="c1ce0-181">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="c1ce0-182">輸入**產品模型**作為名稱,然後單擊 **"確定"**</span><span class="sxs-lookup"><span data-stu-id="c1ce0-182">Enter **ProductModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="c1ce0-183">這會啟動**實體資料模型精靈**</span><span class="sxs-lookup"><span data-stu-id="c1ce0-183">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="c1ce0-184">選擇 **"從数据庫生成**",然後單擊 **"下一步"**</span><span class="sxs-lookup"><span data-stu-id="c1ce0-184">Select **Generate from Database** and click **Next**</span></span>

    ![選擇模型內容](~/ef6/media/choosemodelcontents.png)

-   <span data-ttu-id="c1ce0-186">選擇與在第一部分中創建的資料庫的連接,輸入**ProductContext**作為連接字串的名稱,然後單擊「**下一步」**</span><span class="sxs-lookup"><span data-stu-id="c1ce0-186">Select the connection to the database you created in the first section, enter **ProductContext** as the name of the connection string and click **Next**</span></span>

    ![選擇您的連線](~/ef6/media/chooseyourconnection.png)

-   <span data-ttu-id="c1ce0-188">單擊"表格"旁邊的複選框導入所有表,然後單擊"完成"</span><span class="sxs-lookup"><span data-stu-id="c1ce0-188">Click the checkbox next to ‘Tables’ to import all tables and click ‘Finish’</span></span>

    ![選擇物件](~/ef6/media/chooseyourobjects.png)

<span data-ttu-id="c1ce0-190">反向工程過程完成後,新模型將添加到專案中,並打開,供您在實體框架設計器中查看。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-190">Once the reverse engineer process completes the new model is added to your project and opened up for you to view in the Entity Framework Designer.</span></span> <span data-ttu-id="c1ce0-191">App.config 檔也已添加到專案中,並包含資料庫的連接詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-191">An App.config file has also been added to your project with the connection details for the database.</span></span>

#### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="c1ce0-192">視覺工作室 2010 中的其他步驟</span><span class="sxs-lookup"><span data-stu-id="c1ce0-192">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="c1ce0-193">如果您在 Visual Studio 2010 中工作,則需要更新 EF 設計器才能使用 EF6 代碼生成。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-193">If you are working in Visual Studio 2010 then you will need to update the EF designer to use EF6 code generation.</span></span>

-   <span data-ttu-id="c1ce0-194">右鍵單擊 EF 設計器中模型的空白點,然後選擇 **"添加代碼生成項..."**</span><span class="sxs-lookup"><span data-stu-id="c1ce0-194">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="c1ce0-195">從左方選單**中選擇「連線樣本」** 並搜尋**DbContext**</span><span class="sxs-lookup"><span data-stu-id="c1ce0-195">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="c1ce0-196">選擇**EF 6.x\#DbContext 產生器為 C ,** 輸入**產品模型**作為名稱,然後按兩下"添加"</span><span class="sxs-lookup"><span data-stu-id="c1ce0-196">Select the **EF 6.x DbContext Generator for C\#,** enter **ProductsModel** as the name and click Add</span></span>

#### <a name="updating-code-generation-for-data-binding"></a><span data-ttu-id="c1ce0-197">更新資料繫結的代碼產生</span><span class="sxs-lookup"><span data-stu-id="c1ce0-197">Updating code generation for data binding</span></span>

<span data-ttu-id="c1ce0-198">EF 使用 T4 範本從模型生成代碼。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-198">EF generates code from your model using T4 templates.</span></span> <span data-ttu-id="c1ce0-199">視覺工作室附帶或從 Visual Studio 庫下載的範本用於一般用途。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-199">The templates shipped with Visual Studio or downloaded from the Visual Studio gallery are intended for general purpose use.</span></span> <span data-ttu-id="c1ce0-200">這意味著從這些範本生成的實體具有簡單的&lt;ICollection&gt;T 屬性。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-200">This means that the entities generated from these templates have simple ICollection&lt;T&gt; properties.</span></span> <span data-ttu-id="c1ce0-201">但是,使用 WPF 執行數據綁定時,最好將**可觀察集合**用於集合屬性,以便 WPF 可以追蹤對集合所做的更改。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-201">However, when doing data binding using WPF it is desirable to use **ObservableCollection** for collection properties so that WPF can keep track of changes made to the collections.</span></span> <span data-ttu-id="c1ce0-202">為此,我們將修改範本以使用可觀察集合。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-202">To this end we will to modify the templates to use ObservableCollection.</span></span>

-   <span data-ttu-id="c1ce0-203">開啟**解決方案資源管理員**並尋找**產品模型.edmx**檔案</span><span class="sxs-lookup"><span data-stu-id="c1ce0-203">Open the **Solution Explorer** and find **ProductModel.edmx** file</span></span>
-   <span data-ttu-id="c1ce0-204">尋找將嵌套在 ProductModel.edmx 檔案下的**ProductModel.tt**檔案</span><span class="sxs-lookup"><span data-stu-id="c1ce0-204">Find the **ProductModel.tt** file which will be nested under the ProductModel.edmx file</span></span>

    ![WPF 產品模型範本](~/ef6/media/wpfproductmodeltemplate.png)

-   <span data-ttu-id="c1ce0-206">按兩下ProductModel.tt檔以在可視化工作室編輯器中打開該檔</span><span class="sxs-lookup"><span data-stu-id="c1ce0-206">Double-click on the ProductModel.tt file to open it in the Visual Studio editor</span></span>
-   <span data-ttu-id="c1ce0-207">尋找 **「ICollection」** 的兩個事件並將其替換為「**可觀察集合**」 。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-207">Find and replace the two occurrences of “**ICollection**” with “**ObservableCollection**”.</span></span> <span data-ttu-id="c1ce0-208">這些位於大約 296 和 484 線。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-208">These are located approximately at lines 296 and 484.</span></span>
-   <span data-ttu-id="c1ce0-209">尋找「**哈希集**」 的第一次出現與「**可觀察集合」 取代**。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-209">Find and replace the first occurrence of “**HashSet**” with “**ObservableCollection**”.</span></span> <span data-ttu-id="c1ce0-210">此事件大約位於第 50 行。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-210">This occurrence is located approximately at line 50.</span></span> <span data-ttu-id="c1ce0-211">**不要**替換代碼後面找到的第二個哈希集。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-211">**Do not** replace the second occurrence of HashSet found later in the code.</span></span>
-   <span data-ttu-id="c1ce0-212">尋找「**系統.集合.通用**」 的唯一匹配項並將其替換為「**系統.集合.objectModel」。**</span><span class="sxs-lookup"><span data-stu-id="c1ce0-212">Find and replace the only occurrence of “**System.Collections.Generic**” with “**System.Collections.ObjectModel**”.</span></span> <span data-ttu-id="c1ce0-213">這大約位於第 424 行。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-213">This is located approximately at line 424.</span></span>
-   <span data-ttu-id="c1ce0-214">保存ProductModel.tt檔。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-214">Save the ProductModel.tt file.</span></span> <span data-ttu-id="c1ce0-215">這應會導致重新生成實體的代碼。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-215">This should cause the code for entities to be regenerated.</span></span> <span data-ttu-id="c1ce0-216">如果代碼未自動重新生成,則右鍵單擊ProductModel.tt並選擇「運行自定義工具」。。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-216">If the code does not regenerate automatically, then right click on ProductModel.tt and choose “Run Custom Tool”.</span></span>

<span data-ttu-id="c1ce0-217">如果您現在打開Category.cs檔(嵌套在ProductModel.tt下),那麼您應該看到產品集合具有 **"可&lt;觀察&gt;集合產品**"的類型。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-217">If you now open the Category.cs file (which is nested under ProductModel.tt) then you should see that the Products collection has the type **ObservableCollection&lt;Product&gt;**.</span></span>

<span data-ttu-id="c1ce0-218">編譯專案。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-218">Compile the project.</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="c1ce0-219">消極式載入</span><span class="sxs-lookup"><span data-stu-id="c1ce0-219">Lazy Loading</span></span>

<span data-ttu-id="c1ce0-220">**產品**類別的 **「產品」** 類別**類別屬性**是導航屬性。 **Products**</span><span class="sxs-lookup"><span data-stu-id="c1ce0-220">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="c1ce0-221">在實體框架中,導航屬性提供了一種導航兩種實體類型之間的關係的方法。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-221">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="c1ce0-222">EF 為您提供了在首次訪問導航屬性時自動從資料庫載入相關實體的選項。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-222">EF gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="c1ce0-223">使用這種類型的載入(稱為延遲載入),請注意,如果內容尚未在上下文中,則第一次訪問每個導航屬性時,將對資料庫執行單獨的查詢。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-223">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="c1ce0-224">使用 POCO 實體類型時,EF 透過在執行時建立派生代理類型的實例,然後在類中重寫虛擬屬性來添加載入掛鉤,從而實現延遲載入。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-224">When using POCO entity types, EF achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="c1ce0-225">要獲取相關物件的延遲載入,必須聲明導航屬性 getter 為**公共**和**虛擬**(在 Visual Basic 中**可重寫**),並且不得**密封**類(在 Visual Basic 中**不可重寫**)。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-225">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and you class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="c1ce0-226">使用資料庫時,第一個導航屬性會自動成為虛擬屬性,以啟用延遲載入。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-226">When using Database First navigation properties are automatically made virtual to enable lazy loading.</span></span> <span data-ttu-id="c1ce0-227">在「代碼第一」部分中,我們選擇出於同樣的原因使導航屬性成為虛擬</span><span class="sxs-lookup"><span data-stu-id="c1ce0-227">In the Code First section we chose to make the navigation properties virtual for the same reason</span></span>

## <a name="bind-object-to-controls"></a><span data-ttu-id="c1ce0-228">將物件繫結檔到控制項</span><span class="sxs-lookup"><span data-stu-id="c1ce0-228">Bind Object to Controls</span></span>

<span data-ttu-id="c1ce0-229">將模型中定義的類添加為此 WPF 應用程式的數據來源。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-229">Add the classes that are defined in the model as data sources for this WPF application.</span></span>

-   <span data-ttu-id="c1ce0-230">雙擊解決方案資源管理員中的**MainWindow.xaml**以開啟主窗體</span><span class="sxs-lookup"><span data-stu-id="c1ce0-230">Double-click **MainWindow.xaml** in Solution Explorer to open the main form</span></span>
-   <span data-ttu-id="c1ce0-231">從主選單中,選擇 **「&gt;專案 - 新增新資料來源...**</span><span class="sxs-lookup"><span data-stu-id="c1ce0-231">From the main menu, select **Project -&gt; Add New Data Source …**</span></span>
    <span data-ttu-id="c1ce0-232">( Visual Studio 2010 中,您需要選擇**資料 -&gt;新增新資料來源...**</span><span class="sxs-lookup"><span data-stu-id="c1ce0-232">(in Visual Studio 2010, you need to select **Data -&gt; Add New Data Source…**)</span></span>
-   <span data-ttu-id="c1ce0-233">在「選擇資料來源類型視窗」中,選擇**物件**並按**下 「下一步**」</span><span class="sxs-lookup"><span data-stu-id="c1ce0-233">In the Choose a Data Source Typewindow, select **Object** and click **Next**</span></span>
-   <span data-ttu-id="c1ce0-234">在"選擇數據物件"對話框中,展開**WPF 與 EFSample** 兩次並選擇 **"類別"**</span><span class="sxs-lookup"><span data-stu-id="c1ce0-234">In the Select the Data Objects dialog, unfold the **WPFwithEFSample** two times and select **Category**</span></span>  
    <span data-ttu-id="c1ce0-235">*無需選擇**產品**資料來源,因為我們將透過 **「產品\*\*\*\*」在類別**資料來源上的屬性取得它*</span><span class="sxs-lookup"><span data-stu-id="c1ce0-235">*There is no need to select the **Product** data source, because we will get to it through the **Product**’s property on the **Category** data source*</span></span>  

    ![選擇資料物件](~/ef6/media/selectdataobjects.png)

-   <span data-ttu-id="c1ce0-237">按一下 **[完成]**。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-237">Click **Finish.**</span></span>
-   <span data-ttu-id="c1ce0-238">*如果「資料來源」視窗未顯示,則選擇 **「查看&gt;&gt;- 其他 Windows- 資料來源**」,* 在 MainWindow.xaml 視窗旁邊打開資料來源。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-238">The Data Sources window is opened next to the MainWindow.xaml window *If the Data Sources window is not showing up, select **View -&gt; Other Windows-&gt; Data Sources***</span></span>
-   <span data-ttu-id="c1ce0-239">按引腳圖示,以便數據源視窗不會自動隱藏。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-239">Press the pin icon, so the Data Sources window does not auto hide.</span></span> <span data-ttu-id="c1ce0-240">如果視窗已可見,則可能需要點擊刷新按鈕。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-240">You may need to hit the refresh button if the window was already visible.</span></span>

    ![資料來源](~/ef6/media/datasources.png)

-   <span data-ttu-id="c1ce0-242">選擇 **「類別」** 資料來源並將其拖曳到窗體上。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-242">Select the **Category** data source and drag it on the form.</span></span>

<span data-ttu-id="c1ce0-243">當我們拖動此源時發生了以下情況:</span><span class="sxs-lookup"><span data-stu-id="c1ce0-243">The following happened when we dragged this source:</span></span>

-   <span data-ttu-id="c1ce0-244">**類別ViewSource**資源與**類別資料網格**控制項已新增到 XAML</span><span class="sxs-lookup"><span data-stu-id="c1ce0-244">The **categoryViewSource** resource and the **categoryDataGrid** control were added to XAML</span></span> 
-   <span data-ttu-id="c1ce0-245">父格格元素上的 DataContext 屬性設置為"[靜態資源類別**ViewSource** ]"。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-245">The DataContext property on the parent Grid element was set to "{StaticResource **categoryViewSource** }".</span></span><span data-ttu-id="c1ce0-246">**類別 ViewSource**資源用\\作外部 父網格元素的綁定源。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-246"> The **categoryViewSource** resource serves as a binding source for the outer\\parent Grid element.</span></span> <span data-ttu-id="c1ce0-247">然後,內部網格元素從父網格繼承 DataContext 值(類別 DataGrid 的 ItemsSource 屬性設定為"{綁定}")</span><span class="sxs-lookup"><span data-stu-id="c1ce0-247">The inner Grid elements then inherit the DataContext value from the parent Grid (the categoryDataGrid’s ItemsSource property is set to "{Binding}")</span></span>

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

## <a name="adding-a-details-grid"></a><span data-ttu-id="c1ce0-248">新增詳細資訊格線</span><span class="sxs-lookup"><span data-stu-id="c1ce0-248">Adding a Details Grid</span></span>

<span data-ttu-id="c1ce0-249">現在,我們有一個網格來顯示類別,讓我們添加一個詳細資訊網格來顯示關聯的產品。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-249">Now that we have a grid to display Categories let's add a details grid to display the associated Products.</span></span>

-   <span data-ttu-id="c1ce0-250">從 **「類別」** 資料來源下選擇 **「產品**」屬性,並將其拖動到窗體上。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-250">Select the **Products** property from under the **Category** data source and drag it on the form.</span></span>
    -   <span data-ttu-id="c1ce0-251">**類別 ProductsViewSource**資源和**產品資料網格**將新增到 XAML</span><span class="sxs-lookup"><span data-stu-id="c1ce0-251">The **categoryProductsViewSource** resource and **productDataGrid** grid are added to XAML</span></span>
    -   <span data-ttu-id="c1ce0-252">此資源的結合路徑設定為產品</span><span class="sxs-lookup"><span data-stu-id="c1ce0-252">The binding path for this resource is set to Products</span></span>
    -   <span data-ttu-id="c1ce0-253">WPF 資料繫結框架可確保產品**DataGrid**中只顯示與所選類別相關的產品</span><span class="sxs-lookup"><span data-stu-id="c1ce0-253">WPF data-binding framework ensures that only Products related to the selected Category show up in **productDataGrid**</span></span>
-   <span data-ttu-id="c1ce0-254">從「工具箱」中,將**按鈕**拖動到窗體。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-254">From the Toolbox, drag **Button** on to the form.</span></span> <span data-ttu-id="c1ce0-255">將 **「名稱」** 屬性**設定為按鈕「 儲存**」,將 **「內容」** 屬性設定為 **「 儲存**」 。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-255">Set the **Name** property to **buttonSave** and the **Content** property to **Save**.</span></span>

<span data-ttu-id="c1ce0-256">表單的名稱應表示:</span><span class="sxs-lookup"><span data-stu-id="c1ce0-256">The form should look similar to this:</span></span>

![設計師](~/ef6/media/designer.png) 

## <a name="add-code-that-handles-data-interaction"></a><span data-ttu-id="c1ce0-258">新增處理資料互動的代碼</span><span class="sxs-lookup"><span data-stu-id="c1ce0-258">Add Code that Handles Data Interaction</span></span>

<span data-ttu-id="c1ce0-259">是時候將一些事件處理程式添加到主視窗了。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-259">It's time to add some event handlers to the main window.</span></span>

-   <span data-ttu-id="c1ce0-260">在 XAML 視窗中,按下**&lt;視窗**元素,這會選擇主視窗</span><span class="sxs-lookup"><span data-stu-id="c1ce0-260">In the XAML window, click on the **&lt;Window** element, this selects the main window</span></span>
-   <span data-ttu-id="c1ce0-261">在 **「屬性」** 視窗中選擇右上角**的事件**,然後按兩下 **「已載入**」分頁右側的文字框</span><span class="sxs-lookup"><span data-stu-id="c1ce0-261">In the **Properties** window choose **Events** at the top right, then double-click the text box to right of the **Loaded** label</span></span>

    ![主視窗屬性](~/ef6/media/mainwindowproperties.png)

-   <span data-ttu-id="c1ce0-263">此外,透過雙擊設計器中的「**儲存**」按鈕,添加 **「儲存**」按鈕的 Click 事件。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-263">Also add the **Click** event for the **Save** button by double-clicking the Save button in the designer.</span></span> 

<span data-ttu-id="c1ce0-264">這帶來了表單背後的代碼,我們現在將編輯代碼以使用 ProductContext 執行數據訪問。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-264">This brings you to the code behind for the form, we'll now edit the code to use the ProductContext to perform data access.</span></span> <span data-ttu-id="c1ce0-265">更新主視窗的代碼,如下所示。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-265">Update the code for the MainWindow as shown below.</span></span>

<span data-ttu-id="c1ce0-266">該代碼聲明**產品上下文**的長期運行實例。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-266">The code declares a long-running instance of **ProductContext**.</span></span> <span data-ttu-id="c1ce0-267">**ProductContext**物件用於查詢數據並將其保存到資料庫中。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-267">The **ProductContext** object is used to query and save data to the database.</span></span> <span data-ttu-id="c1ce0-268">然後,從重寫的**On關閉**方法調用**ProductContext**實例上的**Dispose()。**</span><span class="sxs-lookup"><span data-stu-id="c1ce0-268">The **Dispose()** on the **ProductContext** instance is then called from the overridden **OnClosing** method.</span></span><span data-ttu-id="c1ce0-269">代碼註釋提供有關代碼功能的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-269"> The code comments provide details about what the code does.</span></span>

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

## <a name="test-the-wpf-application"></a><span data-ttu-id="c1ce0-270">測試 WPF 應用程式</span><span class="sxs-lookup"><span data-stu-id="c1ce0-270">Test the WPF Application</span></span>

-   <span data-ttu-id="c1ce0-271">編譯並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-271">Compile and run the application.</span></span> <span data-ttu-id="c1ce0-272">如果您先使用代碼,您將看到為您創建了**WPFwithEFSample.ProductContext**資料庫。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-272">If you used Code First, then you will see that a **WPFwithEFSample.ProductContext** database is created for you.</span></span>
-   <span data-ttu-id="c1ce0-273">在頂端網格中輸入類別名稱,在底部網格中輸入產品名稱*不要在 ID 列中輸入任何內容,因為主鍵由資料庫生成*</span><span class="sxs-lookup"><span data-stu-id="c1ce0-273">Enter a category name in the top grid and product names in the bottom grid *Do not enter anything in ID columns, because the primary key is generated by the database*</span></span>

    ![包含新類別和產品的主視窗](~/ef6/media/screen1.png)

-   <span data-ttu-id="c1ce0-275">按 **「儲存」** 按鈕將資料儲存到資料庫</span><span class="sxs-lookup"><span data-stu-id="c1ce0-275">Press the **Save** button to save the data to the database</span></span>

<span data-ttu-id="c1ce0-276">呼叫 DbContext 的**儲存變更 ()** 後,使用資料庫產生的值填充 ID。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-276">After the call to DbContext’s **SaveChanges()**, the IDs are populated with the database generated values.</span></span> <span data-ttu-id="c1ce0-277">因為我們在**保存更改()** 後調用**Refresh()** **DataGrid**控制項也使用新值進行更新。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-277">Because we called **Refresh()** after **SaveChanges()** the **DataGrid** controls are updated with the new values as well.</span></span>

![已填充了已填充了"已填充的"主視窗"](~/ef6/media/screen2.png)

## <a name="additional-resources"></a><span data-ttu-id="c1ce0-279">其他資源</span><span class="sxs-lookup"><span data-stu-id="c1ce0-279">Additional Resources</span></span>

<span data-ttu-id="c1ce0-280">要瞭解有關使用 WPF 綁定到集合的資料詳細資訊,請參閱 WPF 文件中[的本主題](https://docs.microsoft.com/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections)。</span><span class="sxs-lookup"><span data-stu-id="c1ce0-280">To learn more about data binding to collections using WPF, see [this topic](https://docs.microsoft.com/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections) in the WPF documentation.</span></span>  
