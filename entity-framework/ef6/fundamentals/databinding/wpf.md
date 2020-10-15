---
title: 使用 WPF 的資料系結-EF6
description: Entity Framework 6 中的 WPF 資料系結
author: ajcvickers
ms.date: 05/19/2020
uid: ef6/fundamentals/databinding/wpf
ms.openlocfilehash: b6dc484d09340671dfcd841e34234d55e9808af2
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065650"
---
# <a name="databinding-with-wpf"></a><span data-ttu-id="83b70-103">使用 WPF 的資料繫結</span><span class="sxs-lookup"><span data-stu-id="83b70-103">Databinding with WPF</span></span>

> [!IMPORTANT]
> <span data-ttu-id="83b70-104">**本檔僅適用于 .NET Framework 上的 WPF**</span><span class="sxs-lookup"><span data-stu-id="83b70-104">**This document is valid for WPF on the .NET Framework only**</span></span>
>
> <span data-ttu-id="83b70-105">本檔說明 .NET Framework 上 WPF 的資料系結。</span><span class="sxs-lookup"><span data-stu-id="83b70-105">This document describes databinding for WPF on the .NET Framework.</span></span> <span data-ttu-id="83b70-106">針對新的 .NET Core 專案，建議您使用 [EF Core](xref:core/index) 而不是 Entity Framework 6。</span><span class="sxs-lookup"><span data-stu-id="83b70-106">For new .NET Core projects, we recommend you use [EF Core](xref:core/index) instead of Entity Framework 6.</span></span> <span data-ttu-id="83b70-107">EF Core 中的資料系結檔如下： [使用 WPF 消費者入門](xref:core/get-started/wpf)。</span><span class="sxs-lookup"><span data-stu-id="83b70-107">The documentation for databinding in EF Core is here: [Getting Started with WPF](xref:core/get-started/wpf).</span></span>

<span data-ttu-id="83b70-108">本逐步解說將示範如何在「主從階層」表單中，將 POCO 型別系結至 WPF 控制項。</span><span class="sxs-lookup"><span data-stu-id="83b70-108">This step-by-step walkthrough shows how to bind POCO types to WPF controls in a “master-detail" form.</span></span> <span data-ttu-id="83b70-109">應用程式會使用 Entity Framework Api，以資料庫中的資料填入物件、追蹤變更，以及將資料保存到資料庫。</span><span class="sxs-lookup"><span data-stu-id="83b70-109">The application uses the Entity Framework APIs to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="83b70-110">此模型會定義兩種參與一對多關聯性的類型： **類別** (主體 \\ 主機) 和 **產品** (相依 \\ 詳細資料) 。</span><span class="sxs-lookup"><span data-stu-id="83b70-110">The model defines two types that participate in one-to-many relationship: **Category** (principal\\master) and **Product** (dependent\\detail).</span></span> <span data-ttu-id="83b70-111">然後，Visual Studio 工具會用來將模型中定義的類型系結至 WPF 控制項。</span><span class="sxs-lookup"><span data-stu-id="83b70-111">Then, the Visual Studio tools are used to bind the types defined in the model to the WPF controls.</span></span> <span data-ttu-id="83b70-112">WPF 資料系結架構會啟用相關物件之間的導覽：選取主視圖中的資料列會導致詳細資料檢視以對應的子資料更新。</span><span class="sxs-lookup"><span data-stu-id="83b70-112">The WPF data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="83b70-113">本逐步解說中的螢幕擷取畫面和程式代碼清單取自 Visual Studio 2013，但您可以使用 Visual Studio 2012 或 Visual Studio 2010 來完成此逐步解說。</span><span class="sxs-lookup"><span data-stu-id="83b70-113">The screen shots and code listings in this walkthrough are taken from Visual Studio 2013 but you can complete this walkthrough with Visual Studio 2012 or Visual Studio 2010.</span></span>

## <a name="use-the-object-option-for-creating-wpf-data-sources"></a><span data-ttu-id="83b70-114">使用 [物件] 選項來建立 WPF 資料來源</span><span class="sxs-lookup"><span data-stu-id="83b70-114">Use the 'Object' Option for Creating WPF Data Sources</span></span>

<span data-ttu-id="83b70-115">在舊版 Entity Framework 中，我們會在根據使用 EF 設計工具建立的模型建立新的資料來源時，建議您使用 **資料庫** 選項。</span><span class="sxs-lookup"><span data-stu-id="83b70-115">With previous version of Entity Framework we used to recommend using the **Database** option when creating a new Data Source based on a model created with the EF Designer.</span></span> <span data-ttu-id="83b70-116">這是因為設計工具會產生衍生自 ObjectCoNtext 的內容，以及衍生自 EntityObject 的實體類別。</span><span class="sxs-lookup"><span data-stu-id="83b70-116">This was because the designer would generate a context that derived from ObjectContext and entity classes that derived from EntityObject.</span></span> <span data-ttu-id="83b70-117">使用資料庫選項，可協助您撰寫與此 API 介面互動的最佳程式碼。</span><span class="sxs-lookup"><span data-stu-id="83b70-117">Using the Database option would help you write the best code for interacting with this API surface.</span></span>

<span data-ttu-id="83b70-118">Visual Studio 2012 和 Visual Studio 2013 的 EF 設計工具會產生一個衍生自 DbCoNtext 的內容，以及簡單的 POCO 實體類別。</span><span class="sxs-lookup"><span data-stu-id="83b70-118">The EF Designers for Visual Studio 2012 and Visual Studio 2013 generate a context that derives from DbContext together with simple POCO entity classes.</span></span> <span data-ttu-id="83b70-119">在 Visual Studio 2010 中，我們建議您交換使用 DbCoNtext 的程式碼產生範本，如本逐步解說稍後所述。</span><span class="sxs-lookup"><span data-stu-id="83b70-119">With Visual Studio 2010 we recommend swapping to a code generation template that uses DbContext as described later in this walkthrough.</span></span>

<span data-ttu-id="83b70-120">使用 DbCoNtext API 介面時，您應該在建立新的資料來源時使用 **物件** 選項，如本逐步解說所示。</span><span class="sxs-lookup"><span data-stu-id="83b70-120">When using the DbContext API surface you should use the **Object** option when creating a new Data Source, as shown in this walkthrough.</span></span>

<span data-ttu-id="83b70-121">如有需要，您可以針對以 EF 設計工具建立的模型， [還原為以 ObjectCoNtext 為基礎的程式碼產生](xref:ef6/modeling/designer/codegen/legacy-objectcontext) 。</span><span class="sxs-lookup"><span data-stu-id="83b70-121">If needed, you can [revert to ObjectContext based code generation](xref:ef6/modeling/designer/codegen/legacy-objectcontext) for models created with the EF Designer.</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="83b70-122">必要條件</span><span class="sxs-lookup"><span data-stu-id="83b70-122">Pre-Requisites</span></span>

<span data-ttu-id="83b70-123">您必須安裝 Visual Studio 2013、Visual Studio 2012 或 Visual Studio 2010，才能完成此逐步解說。</span><span class="sxs-lookup"><span data-stu-id="83b70-123">You need to have Visual Studio 2013, Visual Studio 2012 or Visual Studio 2010 installed to complete this walkthrough.</span></span>

<span data-ttu-id="83b70-124">如果您使用 Visual Studio 2010，您也必須安裝 NuGet。</span><span class="sxs-lookup"><span data-stu-id="83b70-124">If you are using Visual Studio 2010, you also have to install NuGet.</span></span> <span data-ttu-id="83b70-125">如需詳細資訊，請參閱 [安裝 NuGet](/nuget/install-nuget-client-tools)。</span><span class="sxs-lookup"><span data-stu-id="83b70-125">For more information, see [Installing NuGet](/nuget/install-nuget-client-tools).</span></span>  

## <a name="create-the-application"></a><span data-ttu-id="83b70-126">建立應用程式</span><span class="sxs-lookup"><span data-stu-id="83b70-126">Create the Application</span></span>

- <span data-ttu-id="83b70-127">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83b70-127">Open Visual Studio</span></span>
- <span data-ttu-id="83b70-128">**檔案- &gt; 新增- &gt; 專案 .。。**</span><span class="sxs-lookup"><span data-stu-id="83b70-128">**File -&gt; New -&gt; Project….**</span></span>
- <span data-ttu-id="83b70-129">選取 **Windows**   左窗格中的 [Windows]，然後在右窗格中 **WPFApplication**</span><span class="sxs-lookup"><span data-stu-id="83b70-129">Select **Windows** in the left pane and **WPFApplication** in the right pane</span></span>
- <span data-ttu-id="83b70-130">輸入 **WPFwithEFSample**   作為名稱</span><span class="sxs-lookup"><span data-stu-id="83b70-130">Enter **WPFwithEFSample** as the name</span></span>
- <span data-ttu-id="83b70-131">選取 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="83b70-131">Select **OK**</span></span>

## <a name="install-the-entity-framework-nuget-package"></a><span data-ttu-id="83b70-132">安裝 Entity Framework NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="83b70-132">Install the Entity Framework NuGet package</span></span>

- <span data-ttu-id="83b70-133">在方案總管中，以滑鼠右鍵按一下 **WinFormswithEFSample** 專案</span><span class="sxs-lookup"><span data-stu-id="83b70-133">In Solution Explorer, right-click on the **WinFormswithEFSample** project</span></span>
- <span data-ttu-id="83b70-134">選取 [**管理 NuGet 套件 ...** ]</span><span class="sxs-lookup"><span data-stu-id="83b70-134">Select **Manage NuGet Packages…**</span></span>
- <span data-ttu-id="83b70-135">在 [管理 NuGet 封裝] 對話方塊中，選取 [ **線上** ] 索引標籤，然後選擇 **EntityFramework** 套件</span><span class="sxs-lookup"><span data-stu-id="83b70-135">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
- <span data-ttu-id="83b70-136">按一下 [安裝]</span><span class="sxs-lookup"><span data-stu-id="83b70-136">Click **Install**</span></span>  
    >[!NOTE]
    > <span data-ttu-id="83b70-137">除了 EntityFramework 元件之外，也會加入 ComponentModel. DataAnnotations 的參考。</span><span class="sxs-lookup"><span data-stu-id="83b70-137">In addition to the EntityFramework assembly a reference to System.ComponentModel.DataAnnotations is also added.</span></span> <span data-ttu-id="83b70-138">如果專案具有 system.string 的參考，則會在安裝 EntityFramework 套件時將其移除。</span><span class="sxs-lookup"><span data-stu-id="83b70-138">If the project has a reference to System.Data.Entity, then it will be removed when the EntityFramework package is installed.</span></span> <span data-ttu-id="83b70-139">Entity Framework 6 應用程式不再使用 System.object 元件。</span><span class="sxs-lookup"><span data-stu-id="83b70-139">The System.Data.Entity assembly is no longer used for Entity Framework 6 applications.</span></span>

## <a name="define-a-model"></a><span data-ttu-id="83b70-140">定義模型</span><span class="sxs-lookup"><span data-stu-id="83b70-140">Define a Model</span></span>

<span data-ttu-id="83b70-141">在這個逐步解說中，您可以選擇使用 Code First 或 EF 設計工具來執行模型。</span><span class="sxs-lookup"><span data-stu-id="83b70-141">In this walkthrough you can chose to implement a model using Code First or the EF Designer.</span></span> <span data-ttu-id="83b70-142">完成下列兩節的其中一節。</span><span class="sxs-lookup"><span data-stu-id="83b70-142">Complete one of the two following sections.</span></span>

### <a name="option-1-define-a-model-using-code-first"></a><span data-ttu-id="83b70-143">選項1：使用 Code First 定義模型</span><span class="sxs-lookup"><span data-stu-id="83b70-143">Option 1: Define a Model using Code First</span></span>

<span data-ttu-id="83b70-144">本節說明如何使用 Code First 建立模型及其相關聯的資料庫。</span><span class="sxs-lookup"><span data-stu-id="83b70-144">This section shows how to create a model and its associated database using Code First.</span></span> <span data-ttu-id="83b70-145">跳到下一節， (\*\*選項2：使用 Database First 來定義模型) \*\* 如果您想要 DATABASE FIRST 使用 EF 設計工具，從資料庫將模型反向工程</span><span class="sxs-lookup"><span data-stu-id="83b70-145">Skip to the next section (**Option 2: Define a model using Database First)** if you would rather use Database First to reverse engineer your model from a database using the EF designer</span></span>

<span data-ttu-id="83b70-146">使用 Code First 開發時，通常會先撰寫 .NET Framework 類別，以定義您的概念 (網域) 模型。</span><span class="sxs-lookup"><span data-stu-id="83b70-146">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span>

- <span data-ttu-id="83b70-147">將新類別新增至 **WPFwithEFSample：**</span><span class="sxs-lookup"><span data-stu-id="83b70-147">Add a new class to the **WPFwithEFSample:**</span></span>
  - <span data-ttu-id="83b70-148">以滑鼠右鍵按一下專案名稱</span><span class="sxs-lookup"><span data-stu-id="83b70-148">Right-click on the project name</span></span>
  - <span data-ttu-id="83b70-149">選取 **[** 新增]，然後選取 [**新增專案**]</span><span class="sxs-lookup"><span data-stu-id="83b70-149">Select **Add**, then **New Item**</span></span>
  - <span data-ttu-id="83b70-150">選取**類別**，並 **Product**輸入   類別名稱的產品</span><span class="sxs-lookup"><span data-stu-id="83b70-150">Select **Class** and enter **Product** for the class name</span></span>
- <span data-ttu-id="83b70-151">以下列程式碼取代 **Product**   類別定義：</span><span class="sxs-lookup"><span data-stu-id="83b70-151">Replace the **Product** class definition with the following code:</span></span>

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

- Add a **Category** class with the following definition:

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

<span data-ttu-id="83b70-152">**Product**類別的**Category**類別和**category**屬性上的**Products**屬性為導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="83b70-152">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="83b70-153">在 Entity Framework 中，導覽屬性會提供一種方式來導覽兩個實體類型之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="83b70-153">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="83b70-154">除了定義實體之外，您還必須定義衍生自 DbCoNtext 的類別，並公開 DbSet &lt; TEntity &gt; 屬性。</span><span class="sxs-lookup"><span data-stu-id="83b70-154">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="83b70-155">DbSet &lt; TEntity &gt; 屬性可讓內容知道您想要包含在模型中的類型。</span><span class="sxs-lookup"><span data-stu-id="83b70-155">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="83b70-156">DbCoNtext 衍生類型的實例會在運行時間管理實體物件，包括將資料庫的資料填入物件、變更追蹤，以及將資料保存到資料庫。</span><span class="sxs-lookup"><span data-stu-id="83b70-156">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

- <span data-ttu-id="83b70-157">使用下列定義，將新的 **ProductCoNtext** 類別新增至專案：</span><span class="sxs-lookup"><span data-stu-id="83b70-157">Add a new **ProductContext** class to the project with the following definition:</span></span>

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

<span data-ttu-id="83b70-158">編譯專案。</span><span class="sxs-lookup"><span data-stu-id="83b70-158">Compile the project.</span></span>

### <a name="option-2-define-a-model-using-database-first"></a><span data-ttu-id="83b70-159">選項2：使用 Database First 定義模型</span><span class="sxs-lookup"><span data-stu-id="83b70-159">Option 2: Define a model using Database First</span></span>

<span data-ttu-id="83b70-160">本節說明如何使用 EF designer，從資料庫使用 Database First 對模型進行反向工程。</span><span class="sxs-lookup"><span data-stu-id="83b70-160">This section shows how to use Database First to reverse engineer your model from a database using the EF designer.</span></span> <span data-ttu-id="83b70-161">如果您已完成上一節 (\*\*選項1：使用 Code First) 來定義模型 \*\*，請略過本節，並直接移至消極式 **載入** 區段。</span><span class="sxs-lookup"><span data-stu-id="83b70-161">If you completed the previous section (**Option 1: Define a model using Code First)**, then skip this section and go straight to the **Lazy Loading** section.</span></span>

#### <a name="create-an-existing-database"></a><span data-ttu-id="83b70-162">建立現有的資料庫</span><span class="sxs-lookup"><span data-stu-id="83b70-162">Create an Existing Database</span></span>

<span data-ttu-id="83b70-163">通常當您將目標設為現有的資料庫時，就會建立該資料庫，但在此逐步解說中，我們需要建立要存取的資料庫。</span><span class="sxs-lookup"><span data-stu-id="83b70-163">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="83b70-164">隨 Visual Studio 安裝的資料庫伺服器會根據您所安裝的 Visual Studio 版本而有所不同：</span><span class="sxs-lookup"><span data-stu-id="83b70-164">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

- <span data-ttu-id="83b70-165">如果您使用 Visual Studio 2010，將會建立 SQL Express 資料庫。</span><span class="sxs-lookup"><span data-stu-id="83b70-165">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
- <span data-ttu-id="83b70-166">如果您使用 Visual Studio 2012，則會建立 [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) 資料庫。</span><span class="sxs-lookup"><span data-stu-id="83b70-166">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) database.</span></span>

<span data-ttu-id="83b70-167">讓我們繼續產生資料庫。</span><span class="sxs-lookup"><span data-stu-id="83b70-167">Let's go ahead and generate the database.</span></span>

- <span data-ttu-id="83b70-168">**View- &gt; 伺服器總管**</span><span class="sxs-lookup"><span data-stu-id="83b70-168">**View -&gt; Server Explorer**</span></span>
- <span data-ttu-id="83b70-169">以滑鼠右鍵按一下 [**資料連線- &gt; 新增連接 ...** ]</span><span class="sxs-lookup"><span data-stu-id="83b70-169">Right click on **Data Connections -&gt; Add Connection…**</span></span>
- <span data-ttu-id="83b70-170">如果您還沒有從伺服器總管連接到資料庫，則必須選取 Microsoft SQL Server 作為資料來源</span><span class="sxs-lookup"><span data-stu-id="83b70-170">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![變更資料來源](~/ef6/media/changedatasource.png)

- <span data-ttu-id="83b70-172">連接至 LocalDB 或 SQL Express （視您安裝的版本而定），並輸入 **產品** 做為資料庫名稱</span><span class="sxs-lookup"><span data-stu-id="83b70-172">Connect to either LocalDB or SQL Express, depending on which one you have installed, and enter **Products** as the database name</span></span>

    ![加入連接 LocalDB](~/ef6/media/addconnectionlocaldb.png)

    ![新增連接快速](~/ef6/media/addconnectionexpress.png)

- <span data-ttu-id="83b70-175">選取 **[確定]** ，系統會詢問您是否要建立新的資料庫，請選取 **[是]** 。</span><span class="sxs-lookup"><span data-stu-id="83b70-175">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![建立資料庫](~/ef6/media/createdatabase.png)

- <span data-ttu-id="83b70-177">新資料庫現在會出現在伺服器總管中，以滑鼠右鍵按一下該資料庫，然後選取 [追加**查詢**]。</span><span class="sxs-lookup"><span data-stu-id="83b70-177">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
- <span data-ttu-id="83b70-178">將下列 SQL 複製到新的查詢，然後以滑鼠右鍵按一下查詢並選取 [**執行**]</span><span class="sxs-lookup"><span data-stu-id="83b70-178">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

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

#### <a name="reverse-engineer-model"></a><span data-ttu-id="83b70-179">反向工程模型</span><span class="sxs-lookup"><span data-stu-id="83b70-179">Reverse Engineer Model</span></span>

<span data-ttu-id="83b70-180">我們將利用 Entity Framework Designer，其中包含做為 Visual Studio 的一部分，以建立我們的模型。</span><span class="sxs-lookup"><span data-stu-id="83b70-180">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

- <span data-ttu-id="83b70-181">**專案- &gt; 加入新專案 .。。**</span><span class="sxs-lookup"><span data-stu-id="83b70-181">**Project -&gt; Add New Item…**</span></span>
- <span data-ttu-id="83b70-182">從左側功能表中選取 [ **資料** ]，然後 **ADO.NET 實體資料模型**</span><span class="sxs-lookup"><span data-stu-id="83b70-182">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
- <span data-ttu-id="83b70-183">輸入 **ProductModel** 做為名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="83b70-183">Enter **ProductModel** as the name and click **OK**</span></span>
- <span data-ttu-id="83b70-184">這會啟動 **實體資料模型 Wizard**</span><span class="sxs-lookup"><span data-stu-id="83b70-184">This launches the **Entity Data Model Wizard**</span></span>
- <span data-ttu-id="83b70-185">選取 [ **從資料庫產生** ]，然後按 **[下一步]**</span><span class="sxs-lookup"><span data-stu-id="83b70-185">Select **Generate from Database** and click **Next**</span></span>

    ![選擇模型內容](~/ef6/media/choosemodelcontents.png)

- <span data-ttu-id="83b70-187">選取您在第一個區段中建立之資料庫的連接，輸入**ProductCoNtext**做為連接字串的名稱，然後按 **[下一步]** 。</span><span class="sxs-lookup"><span data-stu-id="83b70-187">Select the connection to the database you created in the first section, enter **ProductContext** as the name of the connection string and click **Next**</span></span>

    ![選擇您的連接](~/ef6/media/chooseyourconnection.png)

- <span data-ttu-id="83b70-189">按一下 [資料表] 旁的核取方塊以匯入所有資料表，然後按一下 [完成]</span><span class="sxs-lookup"><span data-stu-id="83b70-189">Click the checkbox next to ‘Tables’ to import all tables and click ‘Finish’</span></span>

    ![選擇您的物件](~/ef6/media/chooseyourobjects.png)

<span data-ttu-id="83b70-191">一旦反向工程程式完成，新的模型就會加入至您的專案並開啟，以供您在 Entity Framework Designer 中查看。</span><span class="sxs-lookup"><span data-stu-id="83b70-191">Once the reverse engineer process completes the new model is added to your project and opened up for you to view in the Entity Framework Designer.</span></span> <span data-ttu-id="83b70-192">您的專案中也已加入 App.config 檔案，其中包含資料庫的連接詳細資料。</span><span class="sxs-lookup"><span data-stu-id="83b70-192">An App.config file has also been added to your project with the connection details for the database.</span></span>

#### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="83b70-193">Visual Studio 2010 中的其他步驟</span><span class="sxs-lookup"><span data-stu-id="83b70-193">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="83b70-194">如果您在 Visual Studio 2010 中工作，則必須更新 EF 設計工具，才能使用 EF6 程式碼產生。</span><span class="sxs-lookup"><span data-stu-id="83b70-194">If you are working in Visual Studio 2010 then you will need to update the EF designer to use EF6 code generation.</span></span>

- <span data-ttu-id="83b70-195">在 EF 設計工具中，以滑鼠右鍵按一下模型的空白位置，然後選取 [**新增程式碼產生專案 ...** ]。</span><span class="sxs-lookup"><span data-stu-id="83b70-195">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
- <span data-ttu-id="83b70-196">從左側功能表中選取 [ **線上範本** ]，然後搜尋 **DbCoNtext**</span><span class="sxs-lookup"><span data-stu-id="83b70-196">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
- <span data-ttu-id="83b70-197">選取 **適用于 C 的 EF 6.X DbCoNtext 產生器 \# ，** 輸入 **ProductsModel** 做為名稱，然後按一下 [新增]。</span><span class="sxs-lookup"><span data-stu-id="83b70-197">Select the **EF 6.x DbContext Generator for C\#,** enter **ProductsModel** as the name and click Add</span></span>

#### <a name="updating-code-generation-for-data-binding"></a><span data-ttu-id="83b70-198">更新資料系結的程式碼產生</span><span class="sxs-lookup"><span data-stu-id="83b70-198">Updating code generation for data binding</span></span>

<span data-ttu-id="83b70-199">EF 會使用 T4 範本從您的模型產生程式碼。</span><span class="sxs-lookup"><span data-stu-id="83b70-199">EF generates code from your model using T4 templates.</span></span> <span data-ttu-id="83b70-200">隨附于 Visual Studio 或從 Visual Studio 資源庫下載的範本，適用于一般用途。</span><span class="sxs-lookup"><span data-stu-id="83b70-200">The templates shipped with Visual Studio or downloaded from the Visual Studio gallery are intended for general purpose use.</span></span> <span data-ttu-id="83b70-201">這表示，從這些範本產生的實體會有簡單的 ICollection &lt; T &gt; 屬性。</span><span class="sxs-lookup"><span data-stu-id="83b70-201">This means that the entities generated from these templates have simple ICollection&lt;T&gt; properties.</span></span> <span data-ttu-id="83b70-202">不過，使用 WPF 進行資料系結時，最好使用 **ObservableCollection** 進行集合屬性，讓 WPF 可以追蹤對集合所做的變更。</span><span class="sxs-lookup"><span data-stu-id="83b70-202">However, when doing data binding using WPF it is desirable to use **ObservableCollection** for collection properties so that WPF can keep track of changes made to the collections.</span></span> <span data-ttu-id="83b70-203">為了這個目的，我們將修改範本以使用 ObservableCollection。</span><span class="sxs-lookup"><span data-stu-id="83b70-203">To this end we will to modify the templates to use ObservableCollection.</span></span>

- <span data-ttu-id="83b70-204">開啟 **方案總管** 並尋找 **ProductModel .edmx** 檔案</span><span class="sxs-lookup"><span data-stu-id="83b70-204">Open the **Solution Explorer** and find **ProductModel.edmx** file</span></span>
- <span data-ttu-id="83b70-205">尋找 **ProductModel.tt** 檔案，該檔案將會嵌套在 ProductModel .edmx 檔下</span><span class="sxs-lookup"><span data-stu-id="83b70-205">Find the **ProductModel.tt** file which will be nested under the ProductModel.edmx file</span></span>

    ![WPF 產品模型範本](~/ef6/media/wpfproductmodeltemplate.png)

- <span data-ttu-id="83b70-207">按兩下 ProductModel.tt 檔案，在 Visual Studio 編輯器中開啟該檔案</span><span class="sxs-lookup"><span data-stu-id="83b70-207">Double-click on the ProductModel.tt file to open it in the Visual Studio editor</span></span>
- <span data-ttu-id="83b70-208">尋找並以 "**ObservableCollection**" 取代兩個出現的 "**ICollection**"。</span><span class="sxs-lookup"><span data-stu-id="83b70-208">Find and replace the two occurrences of “**ICollection**” with “**ObservableCollection**”.</span></span> <span data-ttu-id="83b70-209">這些大約位於行296和484。</span><span class="sxs-lookup"><span data-stu-id="83b70-209">These are located approximately at lines 296 and 484.</span></span>
- <span data-ttu-id="83b70-210">尋找並取代第一個出現的 "**HashSet**" 和 "**ObservableCollection**"。</span><span class="sxs-lookup"><span data-stu-id="83b70-210">Find and replace the first occurrence of “**HashSet**” with “**ObservableCollection**”.</span></span> <span data-ttu-id="83b70-211">這項情況大約位於第50行。</span><span class="sxs-lookup"><span data-stu-id="83b70-211">This occurrence is located approximately at line 50.</span></span> <span data-ttu-id="83b70-212">**請勿** 取代稍後在程式碼中找到的第二次 HashSet。</span><span class="sxs-lookup"><span data-stu-id="83b70-212">**Do not** replace the second occurrence of HashSet found later in the code.</span></span>
- <span data-ttu-id="83b70-213">找出並以 "**system.collections.objectmodel.observablecollection**" 取代唯一出現的 "**system.object**"。</span><span class="sxs-lookup"><span data-stu-id="83b70-213">Find and replace the only occurrence of “**System.Collections.Generic**” with “**System.Collections.ObjectModel**”.</span></span> <span data-ttu-id="83b70-214">這大約位於行424。</span><span class="sxs-lookup"><span data-stu-id="83b70-214">This is located approximately at line 424.</span></span>
- <span data-ttu-id="83b70-215">儲存 ProductModel.tt 檔案。</span><span class="sxs-lookup"><span data-stu-id="83b70-215">Save the ProductModel.tt file.</span></span> <span data-ttu-id="83b70-216">這應該會導致重新產生實體的程式碼。</span><span class="sxs-lookup"><span data-stu-id="83b70-216">This should cause the code for entities to be regenerated.</span></span> <span data-ttu-id="83b70-217">如果程式碼不會自動重新產生，請在 ProductModel.tt 上按一下滑鼠右鍵，然後選擇 [執行自訂工具]。</span><span class="sxs-lookup"><span data-stu-id="83b70-217">If the code does not regenerate automatically, then right click on ProductModel.tt and choose “Run Custom Tool”.</span></span>

<span data-ttu-id="83b70-218">如果您現在開啟 Category.cs 檔案 (該檔案在 ProductModel.tt) ，您應該會看到 Products 集合的類型為\*\*ObservableCollection &lt; Product &gt; \*\*。</span><span class="sxs-lookup"><span data-stu-id="83b70-218">If you now open the Category.cs file (which is nested under ProductModel.tt) then you should see that the Products collection has the type **ObservableCollection&lt;Product&gt;**.</span></span>

<span data-ttu-id="83b70-219">編譯專案。</span><span class="sxs-lookup"><span data-stu-id="83b70-219">Compile the project.</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="83b70-220">消極式載入</span><span class="sxs-lookup"><span data-stu-id="83b70-220">Lazy Loading</span></span>

<span data-ttu-id="83b70-221">**Product**類別的**Category**類別和**category**屬性上的**Products**屬性為導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="83b70-221">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="83b70-222">在 Entity Framework 中，導覽屬性會提供一種方式來導覽兩個實體類型之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="83b70-222">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="83b70-223">EF 讓您可以選擇在第一次存取導覽屬性時，自動從資料庫載入相關實體。</span><span class="sxs-lookup"><span data-stu-id="83b70-223">EF gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="83b70-224">使用這種類型的載入 (稱為消極式載入) ，請注意，當您第一次存取每個導覽屬性時，如果內容中還沒有內容，則會對資料庫執行個別的查詢。</span><span class="sxs-lookup"><span data-stu-id="83b70-224">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="83b70-225">使用 POCO 實體類型時，EF 會在執行時間建立衍生 proxy 型別的實例，然後覆寫類別中的虛擬屬性以新增載入攔截，藉以達到消極式載入。</span><span class="sxs-lookup"><span data-stu-id="83b70-225">When using POCO entity types, EF achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="83b70-226">若要取得相關物件的消極式載入，您必須在 Visual Basic) 中將導覽屬性 getter 宣告為 **public** 和 **virtual** (可覆 **寫** ，而且您的類別不得在 Visual Basic) 中 **密封** (**NotOverridable** 。</span><span class="sxs-lookup"><span data-stu-id="83b70-226">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and you class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="83b70-227">使用 Database First 導覽屬性會自動設為虛擬，以啟用消極式載入。</span><span class="sxs-lookup"><span data-stu-id="83b70-227">When using Database First navigation properties are automatically made virtual to enable lazy loading.</span></span> <span data-ttu-id="83b70-228">在 [Code First] 區段中，我們選擇讓導覽屬性基於相同的原因進行虛擬</span><span class="sxs-lookup"><span data-stu-id="83b70-228">In the Code First section we chose to make the navigation properties virtual for the same reason</span></span>

## <a name="bind-object-to-controls"></a><span data-ttu-id="83b70-229">將物件系結至控制項</span><span class="sxs-lookup"><span data-stu-id="83b70-229">Bind Object to Controls</span></span>

<span data-ttu-id="83b70-230">將模型中定義的類別加入為這個 WPF 應用程式的資料來源。</span><span class="sxs-lookup"><span data-stu-id="83b70-230">Add the classes that are defined in the model as data sources for this WPF application.</span></span>

- <span data-ttu-id="83b70-231">按兩下方案總管中的 [ **MainWindow** ] 以開啟主要表單</span><span class="sxs-lookup"><span data-stu-id="83b70-231">Double-click **MainWindow.xaml** in Solution Explorer to open the main form</span></span>
- <span data-ttu-id="83b70-232">從主功能表選取 [**專案- &gt; 加入新的資料來源**...]</span><span class="sxs-lookup"><span data-stu-id="83b70-232">From the main menu, select **Project -&gt; Add New Data Source …**</span></span>
    <span data-ttu-id="83b70-233"> (在 Visual Studio 2010 中，您必須選取 [ **資料- &gt; 新增資料來源** ]) </span><span class="sxs-lookup"><span data-stu-id="83b70-233">(in Visual Studio 2010, you need to select **Data -&gt; Add New Data Source…**)</span></span>
- <span data-ttu-id="83b70-234">在 [選擇資料來源 Typewindow] 中選取 [**物件**]，然後按 **[下一步]** 。</span><span class="sxs-lookup"><span data-stu-id="83b70-234">In the Choose a Data Source Typewindow, select **Object** and click **Next**</span></span>
- <span data-ttu-id="83b70-235">在 [選取資料物件] 對話方塊中，將**WPFwithEFSample**展開   兩次，然後選取 [**類別**]</span><span class="sxs-lookup"><span data-stu-id="83b70-235">In the Select the Data Objects dialog, unfold the **WPFwithEFSample** two times and select **Category**</span></span>  
    <span data-ttu-id="83b70-236">*不需要選取**產品**資料來源，因為我們會透過**類別**資料來源上的**產品**屬性來取得它。*</span><span class="sxs-lookup"><span data-stu-id="83b70-236">*There is no need to select the **Product** data source, because we will get to it through the **Product**’s property on the **Category** data source*</span></span>  

    ![選取資料物件](~/ef6/media/selectdataobjects.png)

- <span data-ttu-id="83b70-238">按一下 [完成]  。</span><span class="sxs-lookup"><span data-stu-id="83b70-238">Click **Finish.**</span></span>
- <span data-ttu-id="83b70-239">如果 [資料來源] 視窗未顯示，請在 [MainWindow] 視窗旁邊開啟 [資料來源] 視窗 *，然後選取 [ **View- &gt; 其他 Windows- &gt; 資料來源*** ]。</span><span class="sxs-lookup"><span data-stu-id="83b70-239">The Data Sources window is opened next to the MainWindow.xaml window   *If the Data Sources window is not showing up, select **View -&gt; Other Windows-&gt; Data Sources***</span></span>
- <span data-ttu-id="83b70-240">按下釘選圖示，讓 [資料來源] 視窗不會自動隱藏。</span><span class="sxs-lookup"><span data-stu-id="83b70-240">Press the pin icon, so the Data Sources window does not auto hide.</span></span> <span data-ttu-id="83b70-241">如果視窗已顯示，您可能需要按 [重新整理] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="83b70-241">You may need to hit the refresh button if the window was already visible.</span></span>

    ![Data Sources](~/ef6/media/datasources.png)

- <span data-ttu-id="83b70-243">選取 [ **類別** ] 資料來源，然後將它拖曳到表單上。</span><span class="sxs-lookup"><span data-stu-id="83b70-243">Select the **Category** data source and drag it on the form.</span></span>

<span data-ttu-id="83b70-244">拖曳此來源時，會發生下列情況：</span><span class="sxs-lookup"><span data-stu-id="83b70-244">The following happened when we dragged this source:</span></span>

- <span data-ttu-id="83b70-245">**CategoryViewSource**資源和**categoryDataGrid**控制項已新增至 XAML</span><span class="sxs-lookup"><span data-stu-id="83b70-245">The **categoryViewSource** resource and the **categoryDataGrid** control were added to XAML</span></span>
- <span data-ttu-id="83b70-246">父方格元素上的 DataCoNtext 屬性已設定為 "{StaticResource **categoryViewSource** }"。</span><span class="sxs-lookup"><span data-stu-id="83b70-246">The DataContext property on the parent Grid element was set to "{StaticResource **categoryViewSource** }".</span></span><span data-ttu-id="83b70-247">**CategoryViewSource**資源可作為外部父方格元素的系結來源 \\ 。</span><span class="sxs-lookup"><span data-stu-id="83b70-247"> The **categoryViewSource** resource serves as a binding source for the outer\\parent Grid element.</span></span> <span data-ttu-id="83b70-248">然後，內部方格元素會從父方格繼承 DataCoNtext 值， (categoryDataGrid 的 ItemsSource 屬性設定為 "{Binding}" ) </span><span class="sxs-lookup"><span data-stu-id="83b70-248">The inner Grid elements then inherit the DataContext value from the parent Grid (the categoryDataGrid’s ItemsSource property is set to "{Binding}")</span></span>

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

## <a name="adding-a-details-grid"></a><span data-ttu-id="83b70-249">加入詳細資料方格</span><span class="sxs-lookup"><span data-stu-id="83b70-249">Adding a Details Grid</span></span>

<span data-ttu-id="83b70-250">現在我們有一個顯示類別的方格，讓我們加入詳細資料方格來顯示相關聯的產品。</span><span class="sxs-lookup"><span data-stu-id="83b70-250">Now that we have a grid to display Categories let's add a details grid to display the associated Products.</span></span>

- <span data-ttu-id="83b70-251">從 [ **類別目錄**] 資料來源底下選取 [ **Products** ] 屬性，然後將它拖曳到表單上。</span><span class="sxs-lookup"><span data-stu-id="83b70-251">Select the **Products** property from under the **Category** data source and drag it on the form.</span></span>
  - <span data-ttu-id="83b70-252">**CategoryProductsViewSource**資源和**productDataGrid**方格會新增至 XAML</span><span class="sxs-lookup"><span data-stu-id="83b70-252">The **categoryProductsViewSource** resource and **productDataGrid** grid are added to XAML</span></span>
  - <span data-ttu-id="83b70-253">此資源的系結路徑設定為 Products</span><span class="sxs-lookup"><span data-stu-id="83b70-253">The binding path for this resource is set to Products</span></span>
  - <span data-ttu-id="83b70-254">WPF 資料系結架構可確保只有與所選分類相關的產品會顯示在**productDataGrid**中</span><span class="sxs-lookup"><span data-stu-id="83b70-254">WPF data-binding framework ensures that only Products related to the selected Category show up in **productDataGrid**</span></span>
- <span data-ttu-id="83b70-255">從 [工具箱] 中，將 **按鈕** 拖曳至表單。</span><span class="sxs-lookup"><span data-stu-id="83b70-255">From the Toolbox, drag **Button** on to the form.</span></span> <span data-ttu-id="83b70-256">將 [ **名稱** ] 屬性設為 **buttonSave** ，並將 [ **Content** ] 屬性設定為 [ **儲存**]。</span><span class="sxs-lookup"><span data-stu-id="83b70-256">Set the **Name** property to **buttonSave** and the **Content** property to **Save**.</span></span>

<span data-ttu-id="83b70-257">表單看起來應該像這樣：</span><span class="sxs-lookup"><span data-stu-id="83b70-257">The form should look similar to this:</span></span>

![設計工具表單](~/ef6/media/designer.png)

## <a name="add-code-that-handles-data-interaction"></a><span data-ttu-id="83b70-259">新增處理資料互動的程式碼</span><span class="sxs-lookup"><span data-stu-id="83b70-259">Add Code that Handles Data Interaction</span></span>

<span data-ttu-id="83b70-260">現在可以將一些事件處理常式加入主視窗中。</span><span class="sxs-lookup"><span data-stu-id="83b70-260">It's time to add some event handlers to the main window.</span></span>

- <span data-ttu-id="83b70-261">在 XAML 視窗中，按一下 [ \*\* &lt; 視窗\*\*] 元素，這會選取主視窗。</span><span class="sxs-lookup"><span data-stu-id="83b70-261">In the XAML window, click on the **&lt;Window** element, this selects the main window</span></span>
- <span data-ttu-id="83b70-262">在 [ **屬性** ] 視窗中，選擇右上方的 [ **事件** ]，然後按兩下 **載入** 的標籤右邊的文字方塊</span><span class="sxs-lookup"><span data-stu-id="83b70-262">In the **Properties** window choose **Events** at the top right, then double-click the text box to right of the **Loaded** label</span></span>

    ![主視窗屬性](~/ef6/media/mainwindowproperties.png)

- <span data-ttu-id="83b70-264">也請按兩下設計工具中的 [儲存] 按鈕，以加入 [**儲存**] 按鈕的**Click**事件。</span><span class="sxs-lookup"><span data-stu-id="83b70-264">Also add the **Click** event for the **Save** button by double-clicking the Save button in the designer.</span></span>

<span data-ttu-id="83b70-265">這會帶您前往表單的程式碼後接，我們現在將編輯程式碼，以使用 ProductCoNtext 來執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="83b70-265">This brings you to the code behind for the form, we'll now edit the code to use the ProductContext to perform data access.</span></span> <span data-ttu-id="83b70-266">更新 MainWindow 的程式碼，如下所示。</span><span class="sxs-lookup"><span data-stu-id="83b70-266">Update the code for the MainWindow as shown below.</span></span>

<span data-ttu-id="83b70-267">此程式碼會宣告長時間執行的 **ProductCoNtext**實例。</span><span class="sxs-lookup"><span data-stu-id="83b70-267">The code declares a long-running instance of **ProductContext**.</span></span> <span data-ttu-id="83b70-268">**ProductCoNtext**物件是用來查詢資料庫，並將資料儲存至資料庫。</span><span class="sxs-lookup"><span data-stu-id="83b70-268">The **ProductContext** object is used to query and save data to the database.</span></span> <span data-ttu-id="83b70-269">然後，會從覆寫的**依序 onclosing**方法呼叫**ProductCoNtext**實例上的**Dispose ( # B1** 。</span><span class="sxs-lookup"><span data-stu-id="83b70-269">The **Dispose()** on the **ProductContext** instance is then called from the overridden **OnClosing** method.</span></span><span data-ttu-id="83b70-270">程式碼批註會提供程式碼用途的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="83b70-270"> The code comments provide details about what the code does.</span></span>

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

## <a name="test-the-wpf-application"></a><span data-ttu-id="83b70-271">測試 WPF 應用程式</span><span class="sxs-lookup"><span data-stu-id="83b70-271">Test the WPF Application</span></span>

- <span data-ttu-id="83b70-272">編譯並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="83b70-272">Compile and run the application.</span></span> <span data-ttu-id="83b70-273">如果您使用 Code First，就會看到為您建立了 **WPFwithEFSample ProductCoNtext** 資料庫。</span><span class="sxs-lookup"><span data-stu-id="83b70-273">If you used Code First, then you will see that a **WPFwithEFSample.ProductContext** database is created for you.</span></span>
- <span data-ttu-id="83b70-274">在頂端方格中輸入分類名稱，底部方格中的產品名稱不會在 [*識別碼] 資料行中輸入任何資料，因為主鍵是由資料庫所產生*。</span><span class="sxs-lookup"><span data-stu-id="83b70-274">Enter a category name in the top grid and product names in the bottom grid   *Do not enter anything in ID columns, because the primary key is generated by the database*</span></span>

    ![具有新類別和產品的主視窗](~/ef6/media/screen1.png)

- <span data-ttu-id="83b70-276">按下 [ **儲存** ] 按鈕，將資料儲存至資料庫</span><span class="sxs-lookup"><span data-stu-id="83b70-276">Press the **Save** button to save the data to the database</span></span>

<span data-ttu-id="83b70-277">呼叫 DbCoNtext 的 \*\*SaveChanges ( # B1 \*\*之後，識別碼就會填入資料庫產生的值。</span><span class="sxs-lookup"><span data-stu-id="83b70-277">After the call to DbContext’s **SaveChanges()**, the IDs are populated with the database generated values.</span></span> <span data-ttu-id="83b70-278">因為我們在 SaveChanges 之後呼叫了 \*\*Refresh ( # B1 \*\* \*\* ( # B3 \*\* ，所以 **DataGrid** 控制項也會以新的值更新。</span><span class="sxs-lookup"><span data-stu-id="83b70-278">Because we called **Refresh()** after **SaveChanges()** the **DataGrid** controls are updated with the new values as well.</span></span>

![填入識別碼的主視窗](~/ef6/media/screen2.png)

## <a name="additional-resources"></a><span data-ttu-id="83b70-280">其他資源</span><span class="sxs-lookup"><span data-stu-id="83b70-280">Additional Resources</span></span>

<span data-ttu-id="83b70-281">若要深入瞭解如何使用 WPF 將資料系結至集合，請參閱 WPF 檔中的 [這個主題](/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections) 。</span><span class="sxs-lookup"><span data-stu-id="83b70-281">To learn more about data binding to collections using WPF, see [this topic](/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections) in the WPF documentation.</span></span>  
