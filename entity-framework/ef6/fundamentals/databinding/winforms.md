---
title: 使用 WinForms 進行資料系結-EF6
description: 在 Entity Framework 6 中使用 WinForms 進行資料系結
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/databinding/winforms
ms.openlocfilehash: ef2f19831cd5ce8b1faefeb51f23faef4e167529
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065676"
---
# <a name="databinding-with-winforms"></a><span data-ttu-id="45c4f-103">使用 WinForms 的資料繫結</span><span class="sxs-lookup"><span data-stu-id="45c4f-103">Databinding with WinForms</span></span>
<span data-ttu-id="45c4f-104">本逐步解說將示範如何將 POCO 型別系結至視窗表單 (在「主從階層」表單中 WinForms) 控制項。</span><span class="sxs-lookup"><span data-stu-id="45c4f-104">This step-by-step walkthrough shows how to bind POCO types to Window Forms (WinForms) controls in a “master-detail" form.</span></span> <span data-ttu-id="45c4f-105">應用程式會使用 Entity Framework，以資料庫中的資料填入物件、追蹤變更，以及將資料保存到資料庫。</span><span class="sxs-lookup"><span data-stu-id="45c4f-105">The application uses Entity Framework to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="45c4f-106">此模型會定義兩種參與一對多關聯性的類型：類別 (主體 \\ 主機) 和產品 (相依 \\ 詳細資料) 。</span><span class="sxs-lookup"><span data-stu-id="45c4f-106">The model defines two types that participate in one-to-many relationship: Category (principal\\master) and Product (dependent\\detail).</span></span> <span data-ttu-id="45c4f-107">然後，Visual Studio 工具會用來將模型中定義的類型系結至 WinForms 控制項。</span><span class="sxs-lookup"><span data-stu-id="45c4f-107">Then, the Visual Studio tools are used to bind the types defined in the model to the WinForms controls.</span></span> <span data-ttu-id="45c4f-108">WinForms 資料系結架構會啟用相關物件之間的導覽：選取主視圖中的資料列會導致詳細資料檢視以對應的子資料更新。</span><span class="sxs-lookup"><span data-stu-id="45c4f-108">The WinForms data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="45c4f-109">本逐步解說中的螢幕擷取畫面和程式代碼清單取自 Visual Studio 2013，但您可以使用 Visual Studio 2012 或 Visual Studio 2010 來完成此逐步解說。</span><span class="sxs-lookup"><span data-stu-id="45c4f-109">The screen shots and code listings in this walkthrough are taken from Visual Studio 2013 but you can complete this walkthrough with Visual Studio 2012 or Visual Studio 2010.</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="45c4f-110">必要條件</span><span class="sxs-lookup"><span data-stu-id="45c4f-110">Pre-Requisites</span></span>

<span data-ttu-id="45c4f-111">您必須安裝 Visual Studio 2013、Visual Studio 2012 或 Visual Studio 2010，才能完成此逐步解說。</span><span class="sxs-lookup"><span data-stu-id="45c4f-111">You need to have Visual Studio 2013, Visual Studio 2012 or Visual Studio 2010 installed to complete this walkthrough.</span></span>

<span data-ttu-id="45c4f-112">如果您使用 Visual Studio 2010，您也必須安裝 NuGet。</span><span class="sxs-lookup"><span data-stu-id="45c4f-112">If you are using Visual Studio 2010, you also have to install NuGet.</span></span> <span data-ttu-id="45c4f-113">如需詳細資訊，請參閱 [安裝 NuGet](https://docs.nuget.org/docs/start-here/installing-nuget)。</span><span class="sxs-lookup"><span data-stu-id="45c4f-113">For more information, see [Installing NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).</span></span>

## <a name="create-the-application"></a><span data-ttu-id="45c4f-114">建立應用程式</span><span class="sxs-lookup"><span data-stu-id="45c4f-114">Create the Application</span></span>

-   <span data-ttu-id="45c4f-115">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="45c4f-115">Open Visual Studio</span></span>
-   <span data-ttu-id="45c4f-116">**檔案- &gt; 新增- &gt; 專案 .。。**</span><span class="sxs-lookup"><span data-stu-id="45c4f-116">**File -&gt; New -&gt; Project….**</span></span>
-   <span data-ttu-id="45c4f-117">在左窗格中選取 [ **windows** ]，然後在右窗格中選取 [ **windows FormsApplication** ]</span><span class="sxs-lookup"><span data-stu-id="45c4f-117">Select **Windows** in the left pane and **Windows FormsApplication** in the right pane</span></span>
-   <span data-ttu-id="45c4f-118">輸入 **WinFormswithEFSample** 作為名稱</span><span class="sxs-lookup"><span data-stu-id="45c4f-118">Enter **WinFormswithEFSample** as the name</span></span>
-   <span data-ttu-id="45c4f-119">選取 [確定]</span><span class="sxs-lookup"><span data-stu-id="45c4f-119">Select **OK**</span></span>

## <a name="install-the-entity-framework-nuget-package"></a><span data-ttu-id="45c4f-120">安裝 Entity Framework NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="45c4f-120">Install the Entity Framework NuGet package</span></span>

-   <span data-ttu-id="45c4f-121">在方案總管中，以滑鼠右鍵按一下 **WinFormswithEFSample** 專案</span><span class="sxs-lookup"><span data-stu-id="45c4f-121">In Solution Explorer, right-click on the **WinFormswithEFSample** project</span></span>
-   <span data-ttu-id="45c4f-122">選取 [**管理 NuGet 套件 ...** ]</span><span class="sxs-lookup"><span data-stu-id="45c4f-122">Select **Manage NuGet Packages…**</span></span>
-   <span data-ttu-id="45c4f-123">在 [管理 NuGet 封裝] 對話方塊中，選取 [ **線上** ] 索引標籤，然後選擇 **EntityFramework** 套件</span><span class="sxs-lookup"><span data-stu-id="45c4f-123">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
-   <span data-ttu-id="45c4f-124">按一下 [安裝]</span><span class="sxs-lookup"><span data-stu-id="45c4f-124">Click **Install**</span></span>  
    > [!NOTE]
    > <span data-ttu-id="45c4f-125">除了 EntityFramework 元件之外，也會加入 ComponentModel. DataAnnotations 的參考。</span><span class="sxs-lookup"><span data-stu-id="45c4f-125">In addition to the EntityFramework assembly a reference to System.ComponentModel.DataAnnotations is also added.</span></span> <span data-ttu-id="45c4f-126">如果專案具有 system.string 的參考，則會在安裝 EntityFramework 套件時將其移除。</span><span class="sxs-lookup"><span data-stu-id="45c4f-126">If the project has a reference to System.Data.Entity, then it will be removed when the EntityFramework package is installed.</span></span> <span data-ttu-id="45c4f-127">Entity Framework 6 應用程式不再使用 System.object 元件。</span><span class="sxs-lookup"><span data-stu-id="45c4f-127">The System.Data.Entity assembly is no longer used for Entity Framework 6 applications.</span></span>

## <a name="implementing-ilistsource-for-collections"></a><span data-ttu-id="45c4f-128">針對集合執行 IListSource</span><span class="sxs-lookup"><span data-stu-id="45c4f-128">Implementing IListSource for Collections</span></span>

<span data-ttu-id="45c4f-129">集合屬性必須執行 IListSource 介面，以在使用 Windows Forms 時啟用雙向資料系結與排序。</span><span class="sxs-lookup"><span data-stu-id="45c4f-129">Collection properties must implement the IListSource interface to enable two-way data binding with sorting when using Windows Forms.</span></span> <span data-ttu-id="45c4f-130">為了達到此目的，我們將擴充 ObservableCollection 以新增 IListSource 功能。</span><span class="sxs-lookup"><span data-stu-id="45c4f-130">To do this we are going to extend ObservableCollection to add IListSource functionality.</span></span>

-   <span data-ttu-id="45c4f-131">將 **ObservableListSource** 類別新增至專案：</span><span class="sxs-lookup"><span data-stu-id="45c4f-131">Add an **ObservableListSource** class to the project:</span></span>
    -   <span data-ttu-id="45c4f-132">以滑鼠右鍵按一下專案名稱</span><span class="sxs-lookup"><span data-stu-id="45c4f-132">Right-click on the project name</span></span>
    -   <span data-ttu-id="45c4f-133">選取 [**加入 &gt; 新專案**]</span><span class="sxs-lookup"><span data-stu-id="45c4f-133">Select **Add -&gt; New Item**</span></span>
    -   <span data-ttu-id="45c4f-134">選取 [ **類別** ]，然後輸入 **ObservableListSource** 做為類別名稱</span><span class="sxs-lookup"><span data-stu-id="45c4f-134">Select **Class** and enter **ObservableListSource** for the class name</span></span>
-   <span data-ttu-id="45c4f-135">以下列程式碼取代預設產生的程式碼：</span><span class="sxs-lookup"><span data-stu-id="45c4f-135">Replace the code generated by default with the following code:</span></span>

<span data-ttu-id="45c4f-136">*這個類別會啟用雙向資料系結以及排序。類別衍生自 ObservableCollection &lt; T &gt; ，並新增 IListSource 的明確實作為。IListSource 的 GetList ( # A1 方法會實作為傳回與 ObservableCollection 保持同步的 IBindingList 執行。ToBindingList 所產生的 IBindingList 實支援排序。ToBindingList 擴充方法定義于 EntityFramework 元件中。*</span><span class="sxs-lookup"><span data-stu-id="45c4f-136">*This class enables two-way data binding as well as sorting. The class derives from ObservableCollection&lt;T&gt; and adds an explicit implementation of IListSource. The GetList() method of IListSource is implemented to return an IBindingList implementation that stays in sync with the ObservableCollection. The IBindingList implementation generated by ToBindingList supports sorting. The ToBindingList extension method is defined in the EntityFramework assembly.*</span></span>

``` csharp
    using System.Collections;
    using System.Collections.Generic;
    using System.Collections.ObjectModel;
    using System.ComponentModel;
    using System.Diagnostics.CodeAnalysis;
    using System.Data.Entity;

    namespace WinFormswithEFSample
    {
        public class ObservableListSource<T> : ObservableCollection<T>, IListSource
            where T : class
        {
            private IBindingList _bindingList;

            bool IListSource.ContainsListCollection { get { return false; } }

            IList IListSource.GetList()
            {
                return _bindingList ?? (_bindingList = this.ToBindingList());
            }
        }
    }
```

## <a name="define-a-model"></a><span data-ttu-id="45c4f-137">定義模型</span><span class="sxs-lookup"><span data-stu-id="45c4f-137">Define a Model</span></span>

<span data-ttu-id="45c4f-138">在這個逐步解說中，您可以選擇使用 Code First 或 EF 設計工具來執行模型。</span><span class="sxs-lookup"><span data-stu-id="45c4f-138">In this walkthrough you can chose to implement a model using Code First or the EF Designer.</span></span> <span data-ttu-id="45c4f-139">完成下列兩節的其中一節。</span><span class="sxs-lookup"><span data-stu-id="45c4f-139">Complete one of the two following sections.</span></span>

### <a name="option-1-define-a-model-using-code-first"></a><span data-ttu-id="45c4f-140">選項1：使用 Code First 定義模型</span><span class="sxs-lookup"><span data-stu-id="45c4f-140">Option 1: Define a Model using Code First</span></span>

<span data-ttu-id="45c4f-141">本節說明如何使用 Code First 建立模型及其相關聯的資料庫。</span><span class="sxs-lookup"><span data-stu-id="45c4f-141">This section shows how to create a model and its associated database using Code First.</span></span> <span data-ttu-id="45c4f-142">跳到下一節， (\*\*選項2：使用 Database First 來定義模型) \*\* 如果您想要 DATABASE FIRST 使用 EF 設計工具，從資料庫將模型反向工程</span><span class="sxs-lookup"><span data-stu-id="45c4f-142">Skip to the next section (**Option 2: Define a model using Database First)** if you would rather use Database First to reverse engineer your model from a database using the EF designer</span></span>

<span data-ttu-id="45c4f-143">使用 Code First 開發時，通常會先撰寫 .NET Framework 類別，以定義您的概念 (網域) 模型。</span><span class="sxs-lookup"><span data-stu-id="45c4f-143">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span>

-   <span data-ttu-id="45c4f-144">將新的 **產品** 類別加入至專案</span><span class="sxs-lookup"><span data-stu-id="45c4f-144">Add a new **Product** class to project</span></span>
-   <span data-ttu-id="45c4f-145">以下列程式碼取代預設產生的程式碼：</span><span class="sxs-lookup"><span data-stu-id="45c4f-145">Replace the code generated by default with the following code:</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    namespace WinFormswithEFSample
    {
        public class Product
        {
            public int ProductId { get; set; }
            public string Name { get; set; }

            public int CategoryId { get; set; }
            public virtual Category Category { get; set; }
        }
    }
```

-   <span data-ttu-id="45c4f-146">將 **類別目錄** 類別加入至專案。</span><span class="sxs-lookup"><span data-stu-id="45c4f-146">Add a **Category** class to the project.</span></span>
-   <span data-ttu-id="45c4f-147">以下列程式碼取代預設產生的程式碼：</span><span class="sxs-lookup"><span data-stu-id="45c4f-147">Replace the code generated by default with the following code:</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    namespace WinFormswithEFSample
    {
        public class Category
        {
            private readonly ObservableListSource<Product> _products =
                    new ObservableListSource<Product>();

            public int CategoryId { get; set; }
            public string Name { get; set; }
            public virtual ObservableListSource<Product> Products { get { return _products; } }
        }
    }
```

<span data-ttu-id="45c4f-148">除了定義實體之外，您還必須定義衍生自**DbCoNtext**的類別，並公開\*\*DbSet &lt; TEntity &gt; \*\*屬性。</span><span class="sxs-lookup"><span data-stu-id="45c4f-148">In addition to defining entities, you need to define a class that derives from **DbContext** and exposes **DbSet&lt;TEntity&gt;** properties.</span></span> <span data-ttu-id="45c4f-149">**DbSet**屬性可讓內容知道您想要包含在模型中的類型。</span><span class="sxs-lookup"><span data-stu-id="45c4f-149">The **DbSet** properties let the context know which types you want to include in the model.</span></span> <span data-ttu-id="45c4f-150">**DbCoNtext**和**DbSet**類型是在 EntityFramework 元件中定義。</span><span class="sxs-lookup"><span data-stu-id="45c4f-150">The **DbContext** and **DbSet** types are defined in the EntityFramework assembly.</span></span>

<span data-ttu-id="45c4f-151">DbCoNtext 衍生類型的實例會在運行時間管理實體物件，包括將資料庫的資料填入物件、變更追蹤，以及將資料保存到資料庫。</span><span class="sxs-lookup"><span data-stu-id="45c4f-151">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

-   <span data-ttu-id="45c4f-152">將新的 **ProductCoNtext** 類別新增至專案。</span><span class="sxs-lookup"><span data-stu-id="45c4f-152">Add a new **ProductContext** class to the project.</span></span>
-   <span data-ttu-id="45c4f-153">以下列程式碼取代預設產生的程式碼：</span><span class="sxs-lookup"><span data-stu-id="45c4f-153">Replace the code generated by default with the following code:</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Linq;
    using System.Text;

    namespace WinFormswithEFSample
    {
        public class ProductContext : DbContext
        {
            public DbSet<Category> Categories { get; set; }
            public DbSet<Product> Products { get; set; }
        }
    }
```

<span data-ttu-id="45c4f-154">編譯專案。</span><span class="sxs-lookup"><span data-stu-id="45c4f-154">Compile the project.</span></span>

### <a name="option-2-define-a-model-using-database-first"></a><span data-ttu-id="45c4f-155">選項2：使用 Database First 定義模型</span><span class="sxs-lookup"><span data-stu-id="45c4f-155">Option 2: Define a model using Database First</span></span>

<span data-ttu-id="45c4f-156">本節說明如何使用 EF designer，從資料庫使用 Database First 對模型進行反向工程。</span><span class="sxs-lookup"><span data-stu-id="45c4f-156">This section shows how to use Database First to reverse engineer your model from a database using the EF designer.</span></span> <span data-ttu-id="45c4f-157">如果您已完成上一節 (\*\*選項1：使用 Code First) 來定義模型 \*\*，請略過本節，並直接移至消極式 **載入** 區段。</span><span class="sxs-lookup"><span data-stu-id="45c4f-157">If you completed the previous section (**Option 1: Define a model using Code First)**, then skip this section and go straight to the **Lazy Loading** section.</span></span>

#### <a name="create-an-existing-database"></a><span data-ttu-id="45c4f-158">建立現有的資料庫</span><span class="sxs-lookup"><span data-stu-id="45c4f-158">Create an Existing Database</span></span>

<span data-ttu-id="45c4f-159">通常當您將目標設為現有的資料庫時，就會建立該資料庫，但在此逐步解說中，我們需要建立要存取的資料庫。</span><span class="sxs-lookup"><span data-stu-id="45c4f-159">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="45c4f-160">隨 Visual Studio 安裝的資料庫伺服器會根據您所安裝的 Visual Studio 版本而有所不同：</span><span class="sxs-lookup"><span data-stu-id="45c4f-160">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="45c4f-161">如果您使用 Visual Studio 2010，將會建立 SQL Express 資料庫。</span><span class="sxs-lookup"><span data-stu-id="45c4f-161">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="45c4f-162">如果您使用 Visual Studio 2012，則會建立 [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) 資料庫。</span><span class="sxs-lookup"><span data-stu-id="45c4f-162">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) database.</span></span>

<span data-ttu-id="45c4f-163">讓我們繼續產生資料庫。</span><span class="sxs-lookup"><span data-stu-id="45c4f-163">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="45c4f-164">**View- &gt; 伺服器總管**</span><span class="sxs-lookup"><span data-stu-id="45c4f-164">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="45c4f-165">以滑鼠右鍵按一下 [**資料連線- &gt; 新增連接 ...** ]</span><span class="sxs-lookup"><span data-stu-id="45c4f-165">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="45c4f-166">如果您還沒有從伺服器總管連接到資料庫，則必須選取 Microsoft SQL Server 作為資料來源</span><span class="sxs-lookup"><span data-stu-id="45c4f-166">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![變更資料來源](~/ef6/media/changedatasource.png)

-   <span data-ttu-id="45c4f-168">連接至 LocalDB 或 SQL Express （視您安裝的版本而定），並輸入 **產品** 做為資料庫名稱</span><span class="sxs-lookup"><span data-stu-id="45c4f-168">Connect to either LocalDB or SQL Express, depending on which one you have installed, and enter **Products** as the database name</span></span>

    ![加入連接 LocalDB](~/ef6/media/addconnectionlocaldb.png)

    ![新增連接快速](~/ef6/media/addconnectionexpress.png)

-   <span data-ttu-id="45c4f-171">選取 **[確定]** ，系統會詢問您是否要建立新的資料庫，請選取 **[是]** 。</span><span class="sxs-lookup"><span data-stu-id="45c4f-171">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![建立資料庫](~/ef6/media/createdatabase.png)

-   <span data-ttu-id="45c4f-173">新資料庫現在會出現在伺服器總管中，以滑鼠右鍵按一下該資料庫，然後選取 [追加**查詢**]。</span><span class="sxs-lookup"><span data-stu-id="45c4f-173">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="45c4f-174">將下列 SQL 複製到新的查詢，然後以滑鼠右鍵按一下查詢並選取 [**執行**]</span><span class="sxs-lookup"><span data-stu-id="45c4f-174">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

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

#### <a name="reverse-engineer-model"></a><span data-ttu-id="45c4f-175">反向工程模型</span><span class="sxs-lookup"><span data-stu-id="45c4f-175">Reverse Engineer Model</span></span>

<span data-ttu-id="45c4f-176">我們將利用 Entity Framework Designer，其中包含做為 Visual Studio 的一部分，以建立我們的模型。</span><span class="sxs-lookup"><span data-stu-id="45c4f-176">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="45c4f-177">**專案- &gt; 加入新專案 .。。**</span><span class="sxs-lookup"><span data-stu-id="45c4f-177">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="45c4f-178">從左側功能表中選取 [ **資料** ]，然後 **ADO.NET 實體資料模型**</span><span class="sxs-lookup"><span data-stu-id="45c4f-178">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="45c4f-179">輸入 **ProductModel** 做為名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="45c4f-179">Enter **ProductModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="45c4f-180">這會啟動 **實體資料模型 Wizard**</span><span class="sxs-lookup"><span data-stu-id="45c4f-180">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="45c4f-181">選取 [ **從資料庫產生** ]，然後按 **[下一步]**</span><span class="sxs-lookup"><span data-stu-id="45c4f-181">Select **Generate from Database** and click **Next**</span></span>

    ![選擇模型內容](~/ef6/media/choosemodelcontents.png)

-   <span data-ttu-id="45c4f-183">選取您在第一個區段中建立之資料庫的連接，輸入**ProductCoNtext**做為連接字串的名稱，然後按 **[下一步]** 。</span><span class="sxs-lookup"><span data-stu-id="45c4f-183">Select the connection to the database you created in the first section, enter **ProductContext** as the name of the connection string and click **Next**</span></span>

    ![選擇您的連接](~/ef6/media/chooseyourconnection.png)

-   <span data-ttu-id="45c4f-185">按一下 [資料表] 旁的核取方塊以匯入所有資料表，然後按一下 [完成]</span><span class="sxs-lookup"><span data-stu-id="45c4f-185">Click the checkbox next to ‘Tables’ to import all tables and click ‘Finish’</span></span>

    ![選擇您的物件](~/ef6/media/chooseyourobjects.png)

<span data-ttu-id="45c4f-187">一旦反向工程程式完成，新的模型就會加入至您的專案並開啟，以供您在 Entity Framework Designer 中查看。</span><span class="sxs-lookup"><span data-stu-id="45c4f-187">Once the reverse engineer process completes the new model is added to your project and opened up for you to view in the Entity Framework Designer.</span></span> <span data-ttu-id="45c4f-188">您的專案中也已加入 App.config 檔案，其中包含資料庫的連接詳細資料。</span><span class="sxs-lookup"><span data-stu-id="45c4f-188">An App.config file has also been added to your project with the connection details for the database.</span></span>

#### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="45c4f-189">Visual Studio 2010 中的其他步驟</span><span class="sxs-lookup"><span data-stu-id="45c4f-189">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="45c4f-190">如果您在 Visual Studio 2010 中工作，則必須更新 EF 設計工具，才能使用 EF6 程式碼產生。</span><span class="sxs-lookup"><span data-stu-id="45c4f-190">If you are working in Visual Studio 2010 then you will need to update the EF designer to use EF6 code generation.</span></span>

-   <span data-ttu-id="45c4f-191">在 EF 設計工具中，以滑鼠右鍵按一下模型的空白位置，然後選取 [**新增程式碼產生專案 ...** ]。</span><span class="sxs-lookup"><span data-stu-id="45c4f-191">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="45c4f-192">從左側功能表中選取 [ **線上範本** ]，然後搜尋 **DbCoNtext**</span><span class="sxs-lookup"><span data-stu-id="45c4f-192">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="45c4f-193">選取 **適用于 C 的 EF 6.X DbCoNtext 產生器 \# ，** 輸入 **ProductsModel** 做為名稱，然後按一下 [新增]。</span><span class="sxs-lookup"><span data-stu-id="45c4f-193">Select the **EF 6.x DbContext Generator for C\#,** enter **ProductsModel** as the name and click Add</span></span>

#### <a name="updating-code-generation-for-data-binding"></a><span data-ttu-id="45c4f-194">更新資料系結的程式碼產生</span><span class="sxs-lookup"><span data-stu-id="45c4f-194">Updating code generation for data binding</span></span>

<span data-ttu-id="45c4f-195">EF 會使用 T4 範本從您的模型產生程式碼。</span><span class="sxs-lookup"><span data-stu-id="45c4f-195">EF generates code from your model using T4 templates.</span></span> <span data-ttu-id="45c4f-196">隨附于 Visual Studio 或從 Visual Studio 資源庫下載的範本，適用于一般用途。</span><span class="sxs-lookup"><span data-stu-id="45c4f-196">The templates shipped with Visual Studio or downloaded from the Visual Studio gallery are intended for general purpose use.</span></span> <span data-ttu-id="45c4f-197">這表示，從這些範本產生的實體會有簡單的 ICollection &lt; T &gt; 屬性。</span><span class="sxs-lookup"><span data-stu-id="45c4f-197">This means that the entities generated from these templates have simple ICollection&lt;T&gt; properties.</span></span> <span data-ttu-id="45c4f-198">不過，在進行資料系結時，最好具有可執行 IListSource 的集合屬性。</span><span class="sxs-lookup"><span data-stu-id="45c4f-198">However, when doing data binding it is desirable to have collection properties that implement IListSource.</span></span> <span data-ttu-id="45c4f-199">這就是我們建立上述 ObservableListSource 類別的原因，現在我們要修改範本以利用這個類別。</span><span class="sxs-lookup"><span data-stu-id="45c4f-199">This is why we created the ObservableListSource class above and we are now going to modify the templates to make use of this class.</span></span>

-   <span data-ttu-id="45c4f-200">開啟 **方案總管** 並尋找 **ProductModel .edmx** 檔案</span><span class="sxs-lookup"><span data-stu-id="45c4f-200">Open the **Solution Explorer** and find **ProductModel.edmx** file</span></span>
-   <span data-ttu-id="45c4f-201">尋找 **ProductModel.tt** 檔案，該檔案將會嵌套在 ProductModel .edmx 檔下</span><span class="sxs-lookup"><span data-stu-id="45c4f-201">Find the **ProductModel.tt** file which will be nested under the ProductModel.edmx file</span></span>

    ![產品型號範本](~/ef6/media/productmodeltemplate.png)

-   <span data-ttu-id="45c4f-203">按兩下 ProductModel.tt 檔案，在 Visual Studio 編輯器中開啟該檔案</span><span class="sxs-lookup"><span data-stu-id="45c4f-203">Double-click on the ProductModel.tt file to open it in the Visual Studio editor</span></span>
-   <span data-ttu-id="45c4f-204">尋找並以 "**ObservableListSource**" 取代兩個出現的 "**ICollection**"。</span><span class="sxs-lookup"><span data-stu-id="45c4f-204">Find and replace the two occurrences of “**ICollection**” with “**ObservableListSource**”.</span></span> <span data-ttu-id="45c4f-205">這些都位於大約行296和484。</span><span class="sxs-lookup"><span data-stu-id="45c4f-205">These are located at approximately lines 296 and 484.</span></span>
-   <span data-ttu-id="45c4f-206">尋找並取代第一個出現的 "**HashSet**" 和 "**ObservableListSource**"。</span><span class="sxs-lookup"><span data-stu-id="45c4f-206">Find and replace the first occurrence of “**HashSet**” with “**ObservableListSource**”.</span></span> <span data-ttu-id="45c4f-207">這次出現的位置大約是50行。</span><span class="sxs-lookup"><span data-stu-id="45c4f-207">This occurrence is located at approximately line 50.</span></span> <span data-ttu-id="45c4f-208">**請勿** 取代稍後在程式碼中找到的第二次 HashSet。</span><span class="sxs-lookup"><span data-stu-id="45c4f-208">**Do not** replace the second occurrence of HashSet found later in the code.</span></span>
-   <span data-ttu-id="45c4f-209">儲存 ProductModel.tt 檔案。</span><span class="sxs-lookup"><span data-stu-id="45c4f-209">Save the ProductModel.tt file.</span></span> <span data-ttu-id="45c4f-210">這應該會導致重新產生實體的程式碼。</span><span class="sxs-lookup"><span data-stu-id="45c4f-210">This should cause the code for entities to be regenerated.</span></span> <span data-ttu-id="45c4f-211">如果程式碼不會自動重新產生，請在 ProductModel.tt 上按一下滑鼠右鍵，然後選擇 [執行自訂工具]。</span><span class="sxs-lookup"><span data-stu-id="45c4f-211">If the code does not regenerate automatically, then right click on ProductModel.tt and choose “Run Custom Tool”.</span></span>

<span data-ttu-id="45c4f-212">如果您現在開啟 Category.cs 檔案 (該檔案在 ProductModel.tt) ，您應該會看到 Products 集合的類型為\*\*ObservableListSource &lt; Product &gt; \*\*。</span><span class="sxs-lookup"><span data-stu-id="45c4f-212">If you now open the Category.cs file (which is nested under ProductModel.tt) then you should see that the Products collection has the type **ObservableListSource&lt;Product&gt;**.</span></span>

<span data-ttu-id="45c4f-213">編譯專案。</span><span class="sxs-lookup"><span data-stu-id="45c4f-213">Compile the project.</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="45c4f-214">消極式載入</span><span class="sxs-lookup"><span data-stu-id="45c4f-214">Lazy Loading</span></span>

<span data-ttu-id="45c4f-215">**Product**類別的**Category**類別和**category**屬性上的**Products**屬性為導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="45c4f-215">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="45c4f-216">在 Entity Framework 中，導覽屬性會提供一種方式來導覽兩個實體類型之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="45c4f-216">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="45c4f-217">EF 讓您可以選擇在第一次存取導覽屬性時，自動從資料庫載入相關實體。</span><span class="sxs-lookup"><span data-stu-id="45c4f-217">EF gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="45c4f-218">使用這種類型的載入 (稱為消極式載入) ，請注意，當您第一次存取每個導覽屬性時，如果內容中還沒有內容，則會對資料庫執行個別的查詢。</span><span class="sxs-lookup"><span data-stu-id="45c4f-218">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="45c4f-219">使用 POCO 實體類型時，EF 會在執行時間建立衍生 proxy 型別的實例，然後覆寫類別中的虛擬屬性以新增載入攔截，藉以達到消極式載入。</span><span class="sxs-lookup"><span data-stu-id="45c4f-219">When using POCO entity types, EF achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="45c4f-220">若要取得相關物件的消極式載入，您必須在 Visual Basic) 中將導覽屬性 getter 宣告為 **public** 和 **virtual** (可覆 **寫** ，而且您的類別不得在 Visual Basic) 中 **密封** (**NotOverridable** 。</span><span class="sxs-lookup"><span data-stu-id="45c4f-220">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and you class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="45c4f-221">使用 Database First 導覽屬性會自動設為虛擬，以啟用消極式載入。</span><span class="sxs-lookup"><span data-stu-id="45c4f-221">When using Database First navigation properties are automatically made virtual to enable lazy loading.</span></span> <span data-ttu-id="45c4f-222">在 [Code First] 區段中，我們選擇讓導覽屬性基於相同的原因進行虛擬</span><span class="sxs-lookup"><span data-stu-id="45c4f-222">In the Code First section we chose to make the navigation properties virtual for the same reason</span></span>

## <a name="bind-object-to-controls"></a><span data-ttu-id="45c4f-223">將物件系結至控制項</span><span class="sxs-lookup"><span data-stu-id="45c4f-223">Bind Object to Controls</span></span>

<span data-ttu-id="45c4f-224">將模型中定義的類別新增為此 WinForms 應用程式的資料來源。</span><span class="sxs-lookup"><span data-stu-id="45c4f-224">Add the classes that are defined in the model as data sources for this WinForms application.</span></span>

-   <span data-ttu-id="45c4f-225">從主功能表選取 [**專案- &gt; 加入新的資料來源**...]</span><span class="sxs-lookup"><span data-stu-id="45c4f-225">From the main menu, select **Project -&gt; Add New Data Source …**</span></span>
    <span data-ttu-id="45c4f-226"> (在 Visual Studio 2010 中，您必須選取 [ **資料- &gt; 新增資料來源** ]) </span><span class="sxs-lookup"><span data-stu-id="45c4f-226">(in Visual Studio 2010, you need to select **Data -&gt; Add New Data Source…**)</span></span>
-   <span data-ttu-id="45c4f-227">在 [選擇資料來源類型] 視窗中選取 [**物件**]，然後按 **[下一步]** 。</span><span class="sxs-lookup"><span data-stu-id="45c4f-227">In the Choose a Data Source Type window, select **Object** and click **Next**</span></span>
-   <span data-ttu-id="45c4f-228">在 [選取資料物件] 對話方塊中，將 **WinFormswithEFSample** 展開兩次，然後選取 [ **類別** ]，就不需要選取 [產品] 資料來源，因為我們會透過類別資料來源上的產品屬性來取得它。</span><span class="sxs-lookup"><span data-stu-id="45c4f-228">In the Select the Data Objects dialog, unfold the **WinFormswithEFSample** two times and select **Category** There is no need to select the Product data source, because we will get to it through the Product’s property on the Category data source.</span></span>

    ![資料來源](~/ef6/media/datasource.png)

-   <span data-ttu-id="45c4f-230">按一下 [完成]  。</span><span class="sxs-lookup"><span data-stu-id="45c4f-230">Click **Finish.**</span></span>
    <span data-ttu-id="45c4f-231">如果 [資料來源] 視窗未顯示，請選取 [ **View- &gt; 其他 Windows- &gt; 資料來源**]。</span><span class="sxs-lookup"><span data-stu-id="45c4f-231">If the Data Sources window is not showing up, select **View -&gt; Other Windows-&gt; Data Sources**</span></span>
-   <span data-ttu-id="45c4f-232">按下釘選圖示，讓 [資料來源] 視窗不會自動隱藏。</span><span class="sxs-lookup"><span data-stu-id="45c4f-232">Press the pin icon, so the Data Sources window does not auto hide.</span></span> <span data-ttu-id="45c4f-233">如果視窗已顯示，您可能需要按 [重新整理] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="45c4f-233">You may need to hit the refresh button if the window was already visible.</span></span>

    ![資料來源2](~/ef6/media/datasource2.png)

-   <span data-ttu-id="45c4f-235">在方案總管中，按兩下 **Form1.cs** 檔案，以在 [設計師] 中開啟主表單。</span><span class="sxs-lookup"><span data-stu-id="45c4f-235">In Solution Explorer, double-click the **Form1.cs** file to open the main form in designer.</span></span>
-   <span data-ttu-id="45c4f-236">選取 [ **類別** ] 資料來源，然後將它拖曳到表單上。</span><span class="sxs-lookup"><span data-stu-id="45c4f-236">Select the **Category** data source and drag it on the form.</span></span> <span data-ttu-id="45c4f-237">預設會將新的 DataGridView (**categoryDataGridView**) 和導覽工具列控制項新增至設計工具。</span><span class="sxs-lookup"><span data-stu-id="45c4f-237">By default, a new DataGridView (**categoryDataGridView**) and Navigation toolbar controls are added to the designer.</span></span> <span data-ttu-id="45c4f-238">這些控制項系結至 BindingSource (**categoryBindingSource**) 和系結導覽器 (**categoryBindingNavigator**) 元件也會建立。</span><span class="sxs-lookup"><span data-stu-id="45c4f-238">These controls are bound to the BindingSource (**categoryBindingSource**) and Binding Navigator (**categoryBindingNavigator**) components that are created as well.</span></span>
-   <span data-ttu-id="45c4f-239">編輯 **categoryDataGridView**上的資料行。</span><span class="sxs-lookup"><span data-stu-id="45c4f-239">Edit the columns on the **categoryDataGridView**.</span></span> <span data-ttu-id="45c4f-240">我們想要將 [ **類別類別** ] 資料行設定為唯讀。</span><span class="sxs-lookup"><span data-stu-id="45c4f-240">We want to set the **CategoryId** column to read-only.</span></span> <span data-ttu-id="45c4f-241">在儲存資料之後，資料庫會產生 [ **類別類別** ] 屬性的值。</span><span class="sxs-lookup"><span data-stu-id="45c4f-241">The value for the **CategoryId** property is generated by the database after we save the data.</span></span>
    -   <span data-ttu-id="45c4f-242">以滑鼠右鍵按一下 DataGridView 控制項，然後選取 [編輯資料行]。</span><span class="sxs-lookup"><span data-stu-id="45c4f-242">Right-click the DataGridView control and select Edit Columns…</span></span>
    -   <span data-ttu-id="45c4f-243">選取 [類別類別] 資料行，並將 ReadOnly 設定為 True</span><span class="sxs-lookup"><span data-stu-id="45c4f-243">Select the CategoryId column and set ReadOnly to True</span></span>
    -   <span data-ttu-id="45c4f-244">按下 [確定]</span><span class="sxs-lookup"><span data-stu-id="45c4f-244">Press OK</span></span>
-   <span data-ttu-id="45c4f-245">從 [類別目錄] 資料來源底下選取 [產品]，然後將它拖曳到表單上。</span><span class="sxs-lookup"><span data-stu-id="45c4f-245">Select Products from under the Category data source and drag it on the form.</span></span> <span data-ttu-id="45c4f-246">ProductDataGridView 和 productBindingSource 會新增到表單中。</span><span class="sxs-lookup"><span data-stu-id="45c4f-246">The productDataGridView and productBindingSource are added to the form.</span></span>
-   <span data-ttu-id="45c4f-247">編輯 productDataGridView 上的資料行。</span><span class="sxs-lookup"><span data-stu-id="45c4f-247">Edit the columns on the productDataGridView.</span></span> <span data-ttu-id="45c4f-248">我們想要隱藏 [類別目錄] 和 [類別] 資料行，並將 [ProductId] 設定為唯讀。</span><span class="sxs-lookup"><span data-stu-id="45c4f-248">We want to hide the CategoryId and Category columns and set ProductId to read-only.</span></span> <span data-ttu-id="45c4f-249">在儲存資料之後，資料庫會產生 ProductId 屬性的值。</span><span class="sxs-lookup"><span data-stu-id="45c4f-249">The value for the ProductId property is generated by the database after we save the data.</span></span>
    -   <span data-ttu-id="45c4f-250">以滑鼠右鍵按一下 DataGridView 控制項，然後選取 [ **編輯資料行**]。</span><span class="sxs-lookup"><span data-stu-id="45c4f-250">Right-click the DataGridView control and select **Edit Columns...**.</span></span>
    -   <span data-ttu-id="45c4f-251">選取 [ **ProductId** ] 資料行，並將 **ReadOnly** 設定為 **True**。</span><span class="sxs-lookup"><span data-stu-id="45c4f-251">Select the **ProductId** column and set **ReadOnly** to **True**.</span></span>
    -   <span data-ttu-id="45c4f-252">選取 [ **類別類別** ] 資料行，然後按 [ **移除** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="45c4f-252">Select the **CategoryId** column and press the **Remove** button.</span></span> <span data-ttu-id="45c4f-253">請使用 [ **類別** ] 資料行來進行相同的動作。</span><span class="sxs-lookup"><span data-stu-id="45c4f-253">Do the same with the **Category** column.</span></span>
    -   <span data-ttu-id="45c4f-254">按下 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="45c4f-254">Press **OK**.</span></span>

    <span data-ttu-id="45c4f-255">到目前為止，我們在設計工具中將 DataGridView 控制項與 BindingSource 元件相關聯。</span><span class="sxs-lookup"><span data-stu-id="45c4f-255">So far, we associated our DataGridView controls with BindingSource components in the designer.</span></span> <span data-ttu-id="45c4f-256">在下一節中，我們會將程式碼加入至程式碼後後方，以將 categoryBindingSource 設定為目前由 DbCoNtext 追蹤的實體集合。</span><span class="sxs-lookup"><span data-stu-id="45c4f-256">In the next section we will add code to the code behind to set categoryBindingSource.DataSource to the collection of entities that are currently tracked by DbContext.</span></span> <span data-ttu-id="45c4f-257">當我們從類別下拖放產品時，WinForms 會負責設定 productsBindingSource 的資料，將 categoryBindingSource 和 productsBindingSource 屬性設定為 Products。</span><span class="sxs-lookup"><span data-stu-id="45c4f-257">When we dragged-and-dropped Products from under the Category, the WinForms took care of setting up the productsBindingSource.DataSource property to categoryBindingSource and productsBindingSource.DataMember property to Products.</span></span> <span data-ttu-id="45c4f-258">由於這個系結，因此只有屬於目前所選類別的產品會顯示在 productDataGridView 中。</span><span class="sxs-lookup"><span data-stu-id="45c4f-258">Because of this binding, only the products that belong to the currently selected Category will be displayed in the productDataGridView.</span></span>
-   <span data-ttu-id="45c4f-259">按一下滑鼠右鍵並選取 [**已啟用**]，以啟用導覽工具列上的 [**儲存**] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="45c4f-259">Enable the **Save** button on the Navigation toolbar by clicking the right mouse button and selecting **Enabled**.</span></span>

    ![表單1設計師](~/ef6/media/form1-designer.png)

-   <span data-ttu-id="45c4f-261">按兩下按鈕，以新增 [儲存] 按鈕的事件處理常式。</span><span class="sxs-lookup"><span data-stu-id="45c4f-261">Add the event handler for the save button by double-clicking on the button.</span></span> <span data-ttu-id="45c4f-262">這會加入事件處理常式，並帶您前往表單的程式碼後移。</span><span class="sxs-lookup"><span data-stu-id="45c4f-262">This will add the event handler and bring you to the code behind for the form.</span></span> <span data-ttu-id="45c4f-263">**CategoryBindingNavigatorSaveItem \_ Click**事件處理常式的程式碼將會在下一節中新增。</span><span class="sxs-lookup"><span data-stu-id="45c4f-263">The code for the **categoryBindingNavigatorSaveItem\_Click** event handler will be added in the next section.</span></span>

## <a name="add-the-code-that-handles-data-interaction"></a><span data-ttu-id="45c4f-264">新增處理資料互動的程式碼</span><span class="sxs-lookup"><span data-stu-id="45c4f-264">Add the Code that Handles Data Interaction</span></span>

<span data-ttu-id="45c4f-265">我們現在會新增程式碼，以使用 ProductCoNtext 來執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="45c4f-265">We'll now add the code to use the ProductContext to perform data access.</span></span> <span data-ttu-id="45c4f-266">更新主表單視窗的程式碼，如下所示。</span><span class="sxs-lookup"><span data-stu-id="45c4f-266">Update the code for the main form window as shown below.</span></span>

<span data-ttu-id="45c4f-267">此程式碼會宣告長時間執行的 ProductCoNtext 實例。</span><span class="sxs-lookup"><span data-stu-id="45c4f-267">The code declares a long-running instance of ProductContext.</span></span> <span data-ttu-id="45c4f-268">ProductCoNtext 物件是用來查詢資料庫，並將資料儲存至資料庫。</span><span class="sxs-lookup"><span data-stu-id="45c4f-268">The ProductContext object is used to query and save data to the database.</span></span> <span data-ttu-id="45c4f-269">然後，會從覆寫的依序 onclosing 方法呼叫 ProductCoNtext 實例上的 Dispose ( # A1 方法。</span><span class="sxs-lookup"><span data-stu-id="45c4f-269">The Dispose() method on the ProductContext instance is then called from the overridden OnClosing method.</span></span> <span data-ttu-id="45c4f-270">程式碼批註會提供程式碼用途的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="45c4f-270">The code comments provide details about what the code does.</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.ComponentModel;
    using System.Data;
    using System.Drawing;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Windows.Forms;
    using System.Data.Entity;

    namespace WinFormswithEFSample
    {
        public partial class Form1 : Form
        {
            ProductContext _context;
            public Form1()
            {
                InitializeComponent();
            }

            protected override void OnLoad(EventArgs e)
            {
                base.OnLoad(e);
                _context = new ProductContext();

                // Call the Load method to get the data for the given DbSet
                // from the database.
                // The data is materialized as entities. The entities are managed by
                // the DbContext instance.
                _context.Categories.Load();

                // Bind the categoryBindingSource.DataSource to
                // all the Unchanged, Modified and Added Category objects that
                // are currently tracked by the DbContext.
                // Note that we need to call ToBindingList() on the
                // ObservableCollection<TEntity> returned by
                // the DbSet.Local property to get the BindingList<T>
                // in order to facilitate two-way binding in WinForms.
                this.categoryBindingSource.DataSource =
                    _context.Categories.Local.ToBindingList();
            }

            private void categoryBindingNavigatorSaveItem_Click(object sender, EventArgs e)
            {
                this.Validate();

                // Currently, the Entity Framework doesn’t mark the entities
                // that are removed from a navigation property (in our example the Products)
                // as deleted in the context.
                // The following code uses LINQ to Objects against the Local collection
                // to find all products and marks any that do not have
                // a Category reference as deleted.
                // The ToList call is required because otherwise
                // the collection will be modified
                // by the Remove call while it is being enumerated.
                // In most other situations you can do LINQ to Objects directly
                // against the Local property without using ToList first.
                foreach (var product in _context.Products.Local.ToList())
                {
                    if (product.Category == null)
                    {
                        _context.Products.Remove(product);
                    }
                }

                // Save the changes to the database.
                this._context.SaveChanges();

                // Refresh the controls to show the values         
                // that were generated by the database.
                this.categoryDataGridView.Refresh();
                this.productsDataGridView.Refresh();
            }

            protected override void OnClosing(CancelEventArgs e)
            {
                base.OnClosing(e);
                this._context.Dispose();
            }
        }
    }
```

## <a name="test-the-windows-forms-application"></a><span data-ttu-id="45c4f-271">測試 Windows Forms 應用程式</span><span class="sxs-lookup"><span data-stu-id="45c4f-271">Test the Windows Forms Application</span></span>

-   <span data-ttu-id="45c4f-272">編譯並執行應用程式，您可以測試此功能。</span><span class="sxs-lookup"><span data-stu-id="45c4f-272">Compile and run the application and you can test out the functionality.</span></span>

    ![儲存前的表單1](~/ef6/media/form1beforesave.png)

-   <span data-ttu-id="45c4f-274">儲存產生的金鑰儲存後，會顯示在畫面上。</span><span class="sxs-lookup"><span data-stu-id="45c4f-274">After saving the store generated keys are shown on the screen.</span></span>

    ![儲存後的表單1](~/ef6/media/form1aftersave.png)

-   <span data-ttu-id="45c4f-276">如果您使用 Code First，您也會看到為您建立了 **WinFormswithEFSample ProductCoNtext** 資料庫。</span><span class="sxs-lookup"><span data-stu-id="45c4f-276">If you used Code First, then you will also see that a **WinFormswithEFSample.ProductContext** database is created for you.</span></span>

    ![伺服器物件總管](~/ef6/media/serverobjexplorer.png)
