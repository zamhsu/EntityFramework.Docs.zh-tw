---
title: 資料繫結與 WinForms-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 80fc5062-2f1c-4dbd-ab6e-b99496784b36
ms.openlocfilehash: 8da5bf653221b7919abb89d6d33bc8ed172828a4
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490151"
---
# <a name="databinding-with-winforms"></a><span data-ttu-id="2ab25-102">與 WinForms 資料繫結</span><span class="sxs-lookup"><span data-stu-id="2ab25-102">Databinding with WinForms</span></span>
<span data-ttu-id="2ab25-103">此逐步解說示範如何在 [主版詳細資料] 表單的視窗 Forms (WinForms) 控制項繫結至 POCO 型別。</span><span class="sxs-lookup"><span data-stu-id="2ab25-103">This step-by-step walkthrough shows how to bind POCO types to Window Forms (WinForms) controls in a “master-detail" form.</span></span> <span data-ttu-id="2ab25-104">應用程式會使用 Entity Framework 填入資料庫的資料物件、 追蹤變更，並將資料保存到資料庫。</span><span class="sxs-lookup"><span data-stu-id="2ab25-104">The application uses Entity Framework to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="2ab25-105">此模型會定義參與的兩種類型一個對多關聯性中： 類別目錄 (主體\\主要) 和產品 (相依\\詳細資料)。</span><span class="sxs-lookup"><span data-stu-id="2ab25-105">The model defines two types that participate in one-to-many relationship: Category (principal\\master) and Product (dependent\\detail).</span></span> <span data-ttu-id="2ab25-106">然後，Visual Studio 工具用來繫結至 WinForms 控制項模型中定義的類型。</span><span class="sxs-lookup"><span data-stu-id="2ab25-106">Then, the Visual Studio tools are used to bind the types defined in the model to the WinForms controls.</span></span> <span data-ttu-id="2ab25-107">WinForms 資料繫結架構可讓相關的物件之間的瀏覽： 主版檢視中選取的資料列會導致對應的子資料更新的詳細資料檢視。</span><span class="sxs-lookup"><span data-stu-id="2ab25-107">The WinForms data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="2ab25-108">在本逐步解說的程式碼清單的螢幕擷取畫面取自 Visual Studio 2013，但您可以完成這個逐步解說中的使用 Visual Studio 2012 或 Visual Studio 2010。</span><span class="sxs-lookup"><span data-stu-id="2ab25-108">The screen shots and code listings in this walkthrough are taken from Visual Studio 2013 but you can complete this walkthrough with Visual Studio 2012 or Visual Studio 2010.</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="2ab25-109">必要條件</span><span class="sxs-lookup"><span data-stu-id="2ab25-109">Pre-Requisites</span></span>

<span data-ttu-id="2ab25-110">您必須有 Visual Studio 2013，才能完成此逐步解說安裝的 Visual Studio 2012 或 Visual Studio 2010。</span><span class="sxs-lookup"><span data-stu-id="2ab25-110">You need to have Visual Studio 2013, Visual Studio 2012 or Visual Studio 2010 installed to complete this walkthrough.</span></span>

<span data-ttu-id="2ab25-111">如果您使用 Visual Studio 2010，您也必須安裝 NuGet。</span><span class="sxs-lookup"><span data-stu-id="2ab25-111">If you are using Visual Studio 2010, you also have to install NuGet.</span></span> <span data-ttu-id="2ab25-112">如需詳細資訊，請參閱 <<c0> [ 安裝 NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)。</span><span class="sxs-lookup"><span data-stu-id="2ab25-112">For more information, see [Installing NuGet](http://docs.nuget.org/docs/start-here/installing-nuget).</span></span>

## <a name="create-the-application"></a><span data-ttu-id="2ab25-113">建立應用程式</span><span class="sxs-lookup"><span data-stu-id="2ab25-113">Create the Application</span></span>

-   <span data-ttu-id="2ab25-114">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2ab25-114">Open Visual Studio</span></span>
-   <span data-ttu-id="2ab25-115">**檔案-&gt;新增-&gt;專案...**</span><span class="sxs-lookup"><span data-stu-id="2ab25-115">**File -&gt; New -&gt; Project….**</span></span>
-   <span data-ttu-id="2ab25-116">選取  **Windows**的左窗格中並**Windows FormsApplication**在右窗格</span><span class="sxs-lookup"><span data-stu-id="2ab25-116">Select **Windows** in the left pane and **Windows FormsApplication** in the right pane</span></span>
-   <span data-ttu-id="2ab25-117">請輸入**WinFormswithEFSample**做為名稱</span><span class="sxs-lookup"><span data-stu-id="2ab25-117">Enter **WinFormswithEFSample** as the name</span></span>
-   <span data-ttu-id="2ab25-118">選取 [確定]</span><span class="sxs-lookup"><span data-stu-id="2ab25-118">Select **OK**</span></span>

## <a name="install-the-entity-framework-nuget-package"></a><span data-ttu-id="2ab25-119">安裝 Entity Framework NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="2ab25-119">Install the Entity Framework NuGet package</span></span>

-   <span data-ttu-id="2ab25-120">在 [方案總管] 中，以滑鼠右鍵按一下**WinFormswithEFSample**專案</span><span class="sxs-lookup"><span data-stu-id="2ab25-120">In Solution Explorer, right-click on the **WinFormswithEFSample** project</span></span>
-   <span data-ttu-id="2ab25-121">選取**管理 NuGet 封裝...**</span><span class="sxs-lookup"><span data-stu-id="2ab25-121">Select **Manage NuGet Packages…**</span></span>
-   <span data-ttu-id="2ab25-122">在 [管理 NuGet 套件] 對話方塊中，選取**線上**索引標籤，然後選擇**EntityFramework**封裝</span><span class="sxs-lookup"><span data-stu-id="2ab25-122">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
-   <span data-ttu-id="2ab25-123">按一下 **安裝**</span><span class="sxs-lookup"><span data-stu-id="2ab25-123">Click **Install**</span></span>  
    > [!NOTE]
    > <span data-ttu-id="2ab25-124">除了 EntityFramework 組件也會加入 System.ComponentModel.DataAnnotations 的參考。</span><span class="sxs-lookup"><span data-stu-id="2ab25-124">In addition to the EntityFramework assembly a reference to System.ComponentModel.DataAnnotations is also added.</span></span> <span data-ttu-id="2ab25-125">如果專案具有 System.Data.Entity 的參考，則它將會移除安裝 EntityFramework 套件時。</span><span class="sxs-lookup"><span data-stu-id="2ab25-125">If the project has a reference to System.Data.Entity, then it will be removed when the EntityFramework package is installed.</span></span> <span data-ttu-id="2ab25-126">System.Data.Entity 組件不會再用於 Entity Framework 6 應用程式。</span><span class="sxs-lookup"><span data-stu-id="2ab25-126">The System.Data.Entity assembly is no longer used for Entity Framework 6 applications.</span></span>

## <a name="implementing-ilistsource-for-collections"></a><span data-ttu-id="2ab25-127">IListSource 實作集合</span><span class="sxs-lookup"><span data-stu-id="2ab25-127">Implementing IListSource for Collections</span></span>

<span data-ttu-id="2ab25-128">集合屬性必須實作 IListSource 介面以啟用排序時使用 Windows Form 的雙向資料繫結。</span><span class="sxs-lookup"><span data-stu-id="2ab25-128">Collection properties must implement the IListSource interface to enable two-way data binding with sorting when using Windows Forms.</span></span> <span data-ttu-id="2ab25-129">若要這樣做，我們即將擴充 ObservableCollection 加入 IListSource 功能。</span><span class="sxs-lookup"><span data-stu-id="2ab25-129">To do this we are going to extend ObservableCollection to add IListSource functionality.</span></span>

-   <span data-ttu-id="2ab25-130">新增**ObservableListSource**類別至專案：</span><span class="sxs-lookup"><span data-stu-id="2ab25-130">Add an **ObservableListSource** class to the project:</span></span>
    -   <span data-ttu-id="2ab25-131">以滑鼠右鍵按一下專案名稱</span><span class="sxs-lookup"><span data-stu-id="2ab25-131">Right-click on the project name</span></span>
    -   <span data-ttu-id="2ab25-132">選取 **新增-&gt;新項目**</span><span class="sxs-lookup"><span data-stu-id="2ab25-132">Select **Add -&gt; New Item**</span></span>
    -   <span data-ttu-id="2ab25-133">選取 **類別**，然後輸入**ObservableListSource**的類別名稱</span><span class="sxs-lookup"><span data-stu-id="2ab25-133">Select **Class** and enter **ObservableListSource** for the class name</span></span>
-   <span data-ttu-id="2ab25-134">取代為下列程式碼的預設產生的程式碼：</span><span class="sxs-lookup"><span data-stu-id="2ab25-134">Replace the code generated by default with the following code:</span></span>

<span data-ttu-id="2ab25-135">*這個類別會啟用雙向資料繫結，以及排序。類別衍生自 ObservableCollection&lt;T&gt;並新增明確的 IListSource 實作。IListSource getlist （） 方法實作傳回 IBindingList 實作，它與 ObservableCollection 保持同步。產生 ToBindingList IBindingList 實作支援排序。EntityFramework 組件中定義 ToBindingList 擴充方法。*</span><span class="sxs-lookup"><span data-stu-id="2ab25-135">*This class enables two-way data binding as well as sorting. The class derives from ObservableCollection&lt;T&gt; and adds an explicit implementation of IListSource. The GetList() method of IListSource is implemented to return an IBindingList implementation that stays in sync with the ObservableCollection. The IBindingList implementation generated by ToBindingList supports sorting. The ToBindingList extention method is defined in the EntityFramework assembly.*</span></span>

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

## <a name="define-a-model"></a><span data-ttu-id="2ab25-136">定義模型</span><span class="sxs-lookup"><span data-stu-id="2ab25-136">Define a Model</span></span>

<span data-ttu-id="2ab25-137">在本逐步解說，您可以選擇實作模型，使用程式碼優先 」 或 「 EF 設計工具。</span><span class="sxs-lookup"><span data-stu-id="2ab25-137">In this walkthrough you can chose to implement a model using Code First or the EF Designer.</span></span> <span data-ttu-id="2ab25-138">完成其中兩個下列各節。</span><span class="sxs-lookup"><span data-stu-id="2ab25-138">Complete one of the two following sections.</span></span>

### <a name="option-1-define-a-model-using-code-first"></a><span data-ttu-id="2ab25-139">選項 1： 定義使用 Code First 模型</span><span class="sxs-lookup"><span data-stu-id="2ab25-139">Option 1: Define a Model using Code First</span></span>

<span data-ttu-id="2ab25-140">本節說明如何建立模型和其相關聯的資料庫使用 Code First。</span><span class="sxs-lookup"><span data-stu-id="2ab25-140">This section shows how to create a model and its associated database using Code First.</span></span> <span data-ttu-id="2ab25-141">請跳至下一節 (**選項 2： 定義模型使用 Database First)** 如果您想要使用 Database First 反轉設計您的模型使用 EF 設計工具，從資料庫</span><span class="sxs-lookup"><span data-stu-id="2ab25-141">Skip to the next section (**Option 2: Define a model using Database First)** if you would rather use Database First to reverse engineer your model from a database using the EF designer</span></span>

<span data-ttu-id="2ab25-142">使用 Code First 開發時您通常會開始撰寫定義概念 （網域） 模型的.NET Framework 類別。</span><span class="sxs-lookup"><span data-stu-id="2ab25-142">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span>

-   <span data-ttu-id="2ab25-143">加入新**產品**類別至專案</span><span class="sxs-lookup"><span data-stu-id="2ab25-143">Add a new **Product** class to project</span></span>
-   <span data-ttu-id="2ab25-144">取代為下列程式碼的預設產生的程式碼：</span><span class="sxs-lookup"><span data-stu-id="2ab25-144">Replace the code generated by default with the following code:</span></span>

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

-   <span data-ttu-id="2ab25-145">新增**分類**類別至專案。</span><span class="sxs-lookup"><span data-stu-id="2ab25-145">Add a **Category** class to the project.</span></span>
-   <span data-ttu-id="2ab25-146">取代為下列程式碼的預設產生的程式碼：</span><span class="sxs-lookup"><span data-stu-id="2ab25-146">Replace the code generated by default with the following code:</span></span>

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

<span data-ttu-id="2ab25-147">除了定義實體，您必須定義衍生自類別**DbContext** ，並公開**DbSet&lt;TEntity&gt;** 屬性。</span><span class="sxs-lookup"><span data-stu-id="2ab25-147">In addition to defining entities, you need to define a class that derives from **DbContext** and exposes **DbSet&lt;TEntity&gt;** properties.</span></span> <span data-ttu-id="2ab25-148">**DbSet**屬性可讓知道您想要在模型中包含哪些的類型的內容。</span><span class="sxs-lookup"><span data-stu-id="2ab25-148">The **DbSet** properties let the context know which types you want to include in the model.</span></span> <span data-ttu-id="2ab25-149">**DbContext**並**DbSet** EntityFramework 組件中所定義的類型。</span><span class="sxs-lookup"><span data-stu-id="2ab25-149">The **DbContext** and **DbSet** types are defined in the EntityFramework assembly.</span></span>

<span data-ttu-id="2ab25-150">DbContext 衍生型別的執行個體在執行階段，其中包含填入資料庫的資料的物件期間同時管理實體物件、 變更追蹤，和保存資料至資料庫。</span><span class="sxs-lookup"><span data-stu-id="2ab25-150">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

-   <span data-ttu-id="2ab25-151">加入新**ProductContext**類別至專案。</span><span class="sxs-lookup"><span data-stu-id="2ab25-151">Add a new **ProductContext** class to the project.</span></span>
-   <span data-ttu-id="2ab25-152">取代為下列程式碼的預設產生的程式碼：</span><span class="sxs-lookup"><span data-stu-id="2ab25-152">Replace the code generated by default with the following code:</span></span>

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

<span data-ttu-id="2ab25-153">編譯專案。</span><span class="sxs-lookup"><span data-stu-id="2ab25-153">Compile the project.</span></span>

### <a name="option-2-define-a-model-using-database-first"></a><span data-ttu-id="2ab25-154">選項 2： 定義使用 Database First 模型</span><span class="sxs-lookup"><span data-stu-id="2ab25-154">Option 2: Define a model using Database First</span></span>

<span data-ttu-id="2ab25-155">本節說明如何使用 Database First，進行反向工程，您使用 EF 設計工具，從資料庫的模型。</span><span class="sxs-lookup"><span data-stu-id="2ab25-155">This section shows how to use Database First to reverse engineer your model from a database using the EF designer.</span></span> <span data-ttu-id="2ab25-156">如果您已完成上一節 (**選項 1： 定義模型使用 Code First)**，然後略過本節並直接前往**消極式載入**一節。</span><span class="sxs-lookup"><span data-stu-id="2ab25-156">If you completed the previous section (**Option 1: Define a model using Code First)**, then skip this section and go straight to the **Lazy Loading** section.</span></span>

#### <a name="create-an-existing-database"></a><span data-ttu-id="2ab25-157">建立現有的資料庫</span><span class="sxs-lookup"><span data-stu-id="2ab25-157">Create an Existing Database</span></span>

<span data-ttu-id="2ab25-158">通常當您的目標將已建立，現有的資料庫，但在此逐步解說，我們需要建立可存取的資料庫。</span><span class="sxs-lookup"><span data-stu-id="2ab25-158">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="2ab25-159">Visual Studio 隨附安裝的資料庫伺服器是您已安裝的 Visual Studio 版本而有所不同：</span><span class="sxs-lookup"><span data-stu-id="2ab25-159">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="2ab25-160">如果您使用 Visual Studio 2010 將會建立 SQL Express 資料庫。</span><span class="sxs-lookup"><span data-stu-id="2ab25-160">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="2ab25-161">如果您使用 Visual Studio 2012，則您將會建立[LocalDB](https://msdn.microsoft.com/library/hh510202.aspx)資料庫。</span><span class="sxs-lookup"><span data-stu-id="2ab25-161">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) database.</span></span>

<span data-ttu-id="2ab25-162">接下來產生的資料庫。</span><span class="sxs-lookup"><span data-stu-id="2ab25-162">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="2ab25-163">**檢視-&gt;伺服器總管**</span><span class="sxs-lookup"><span data-stu-id="2ab25-163">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="2ab25-164">以滑鼠右鍵按一下**資料連線-&gt;新增連接...**</span><span class="sxs-lookup"><span data-stu-id="2ab25-164">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="2ab25-165">如果您尚未連線至資料庫從伺服器總管之前您必須選取 Microsoft SQL Server 做為資料來源</span><span class="sxs-lookup"><span data-stu-id="2ab25-165">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![變更資料來源](~/ef6/media/changedatasource.png)

-   <span data-ttu-id="2ab25-167">連接到 LocalDB 或 SQL Express，何者而定，您已安裝，然後輸入**產品**做為資料庫名稱</span><span class="sxs-lookup"><span data-stu-id="2ab25-167">Connect to either LocalDB or SQL Express, depending on which one you have installed, and enter **Products** as the database name</span></span>

    ![新增連線 LocalDB](~/ef6/media/addconnectionlocaldb.png)

    ![新增連線 Express](~/ef6/media/addconnectionexpress.png)

-   <span data-ttu-id="2ab25-170">選取  **確定**而且會要求您想要建立新資料庫，請選取**是**</span><span class="sxs-lookup"><span data-stu-id="2ab25-170">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![建立資料庫](~/ef6/media/createdatabase.png)

-   <span data-ttu-id="2ab25-172">新資料庫現在會出現在 [伺服器總管] 中，按一下滑鼠右鍵，然後選取**新查詢**</span><span class="sxs-lookup"><span data-stu-id="2ab25-172">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="2ab25-173">將下列 SQL 複製到新的查詢，然後以滑鼠右鍵按一下查詢並選取**Execute**</span><span class="sxs-lookup"><span data-stu-id="2ab25-173">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

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

#### <a name="reverse-engineer-model"></a><span data-ttu-id="2ab25-174">反向工程模型</span><span class="sxs-lookup"><span data-stu-id="2ab25-174">Reverse Engineer Model</span></span>

<span data-ttu-id="2ab25-175">我們將利用 Entity Framework Designer 中，也就是 Visual Studio 的一部分，以建立我們的模型。</span><span class="sxs-lookup"><span data-stu-id="2ab25-175">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="2ab25-176">**專案-&gt;加入新項目...**</span><span class="sxs-lookup"><span data-stu-id="2ab25-176">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="2ab25-177">選取 **資料**從左側功能表，然後**ADO.NET 實體資料模型**</span><span class="sxs-lookup"><span data-stu-id="2ab25-177">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="2ab25-178">請輸入**ProductModel**作為名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="2ab25-178">Enter **ProductModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="2ab25-179">這會啟動**Entity Data Model 精靈**</span><span class="sxs-lookup"><span data-stu-id="2ab25-179">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="2ab25-180">選取 **從資料庫產生**，按一下 **下一步**</span><span class="sxs-lookup"><span data-stu-id="2ab25-180">Select **Generate from Database** and click **Next**</span></span>

    ![ChooseModelContents](~/ef6/media/choosemodelcontents.png)

-   <span data-ttu-id="2ab25-182">選取您建立第一個區段中的資料庫連接中，輸入**ProductContext**做為連接字串，然後按一下 [名稱**下一步]**</span><span class="sxs-lookup"><span data-stu-id="2ab25-182">Select the connection to the database you created in the first section, enter **ProductContext** as the name of the connection string and click **Next**</span></span>

    ![選擇您的連線](~/ef6/media/chooseyourconnection.png)

-   <span data-ttu-id="2ab25-184">按一下 匯入的所有資料表，並按一下 完成 '資料表旁的核取方塊</span><span class="sxs-lookup"><span data-stu-id="2ab25-184">Click the checkbox next to ‘Tables’ to import all tables and click ‘Finish’</span></span>

    ![選擇您的物件](~/ef6/media/chooseyourobjects.png)

<span data-ttu-id="2ab25-186">反向工程程序完成後新模型加入您的專案，並讓您檢視在 Entity Framework Designer 中開啟。</span><span class="sxs-lookup"><span data-stu-id="2ab25-186">Once the reverse engineer process completes the new model is added to your project and opened up for you to view in the Entity Framework Designer.</span></span> <span data-ttu-id="2ab25-187">App.config 檔案也已新增至您的專案與資料庫的連線詳細資料。</span><span class="sxs-lookup"><span data-stu-id="2ab25-187">An App.config file has also been added to your project with the connection details for the database.</span></span>

#### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="2ab25-188">Visual Studio 2010 中的其他步驟</span><span class="sxs-lookup"><span data-stu-id="2ab25-188">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="2ab25-189">如果您正在 Visual Studio 2010 中將需要更新使用 EF6 程式碼產生的 EF 設計工具。</span><span class="sxs-lookup"><span data-stu-id="2ab25-189">If you are working in Visual Studio 2010 then you will need to update the EF designer to use EF6 code generation.</span></span>

-   <span data-ttu-id="2ab25-190">以滑鼠右鍵按一下您的模型，在 EF 設計工具中的非空點，然後選取**加入的程式碼產生項目...**</span><span class="sxs-lookup"><span data-stu-id="2ab25-190">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="2ab25-191">選取 **線上範本**從左側的功能表，並搜尋**DbContext**</span><span class="sxs-lookup"><span data-stu-id="2ab25-191">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="2ab25-192">選取**EF 6.x 適用於 C 的 DbContext Generator\#，** 輸入**ProductsModel**做為名稱並按一下 [新增]</span><span class="sxs-lookup"><span data-stu-id="2ab25-192">Select the **EF 6.x DbContext Generator for C\#,** enter **ProductsModel** as the name and click Add</span></span>

#### <a name="updating-code-generation-for-data-binding"></a><span data-ttu-id="2ab25-193">更新資料繫結的程式碼產生</span><span class="sxs-lookup"><span data-stu-id="2ab25-193">Updating code generation for data binding</span></span>

<span data-ttu-id="2ab25-194">EF 從模型使用 T4 範本產生程式碼。</span><span class="sxs-lookup"><span data-stu-id="2ab25-194">EF generates code from your model using T4 templates.</span></span> <span data-ttu-id="2ab25-195">隨附於 Visual Studio，或從 Visual Studio 組件庫下載的範本供一般用途使用。</span><span class="sxs-lookup"><span data-stu-id="2ab25-195">The templates shipped with Visual Studio or downloaded from the Visual Studio gallery are intended for general purpose use.</span></span> <span data-ttu-id="2ab25-196">這表示從這些範本所產生的實體有簡單的 ICollection&lt;T&gt;屬性。</span><span class="sxs-lookup"><span data-stu-id="2ab25-196">This means that the entities generated from these templates have simple ICollection&lt;T&gt; properties.</span></span> <span data-ttu-id="2ab25-197">不過，進行資料繫結時，需要實作 IListSource 的集合屬性。</span><span class="sxs-lookup"><span data-stu-id="2ab25-197">However, when doing data binding it is desirable to have collection properties that implement IListSource.</span></span> <span data-ttu-id="2ab25-198">這就是為什麼我們建立上述 ObservableListSource 類別和我們現在要修改範本，請使用這個類別。</span><span class="sxs-lookup"><span data-stu-id="2ab25-198">This is why we created the ObservableListSource class above and we are now going to modify the templates to make use of this class.</span></span>

-   <span data-ttu-id="2ab25-199">開啟**方案總管**並尋找**ProductModel.edmx**檔案</span><span class="sxs-lookup"><span data-stu-id="2ab25-199">Open the **Solution Explorer** and find **ProductModel.edmx** file</span></span>
-   <span data-ttu-id="2ab25-200">尋找**ProductModel.tt**下 ProductModel.edmx 檔案會進行巢狀檔案</span><span class="sxs-lookup"><span data-stu-id="2ab25-200">Find the **ProductModel.tt** file which will be nested under the ProductModel.edmx file</span></span>

    ![產品模型範本](~/ef6/media/productmodeltemplate.png)

-   <span data-ttu-id="2ab25-202">按兩下 ProductModel.tt 檔案在 Visual Studio 編輯器中開啟它</span><span class="sxs-lookup"><span data-stu-id="2ab25-202">Double-click on the ProductModel.tt file to open it in the Visual Studio editor</span></span>
-   <span data-ttu-id="2ab25-203">尋找和取代出現兩次的 「**ICollection**"with"**ObservableListSource**"。</span><span class="sxs-lookup"><span data-stu-id="2ab25-203">Find and replace the two occurrences of “**ICollection**” with “**ObservableListSource**”.</span></span> <span data-ttu-id="2ab25-204">這些是位在大約行 296 和 484。</span><span class="sxs-lookup"><span data-stu-id="2ab25-204">These are located at approximately lines 296 and 484.</span></span>
-   <span data-ttu-id="2ab25-205">尋找和取代第一個出現的 「**HashSet**"with"**ObservableListSource**"。</span><span class="sxs-lookup"><span data-stu-id="2ab25-205">Find and replace the first occurrence of “**HashSet**” with “**ObservableListSource**”.</span></span> <span data-ttu-id="2ab25-206">這項問題位於大約第 50 行。</span><span class="sxs-lookup"><span data-stu-id="2ab25-206">This occurrence is located at approximately line 50.</span></span> <span data-ttu-id="2ab25-207">**不這麼做**取代 HashSet 稍後在程式碼中找到的第二個執行個體。</span><span class="sxs-lookup"><span data-stu-id="2ab25-207">**Do not** replace the second occurrence of HashSet found later in the code.</span></span>
-   <span data-ttu-id="2ab25-208">儲存 ProductModel.tt 檔案。</span><span class="sxs-lookup"><span data-stu-id="2ab25-208">Save the ProductModel.tt file.</span></span> <span data-ttu-id="2ab25-209">這應該會造成重新產生實體的程式碼。</span><span class="sxs-lookup"><span data-stu-id="2ab25-209">This should cause the code for entities to be regenerated.</span></span> <span data-ttu-id="2ab25-210">如果程式碼不會自動重新產生，然後以滑鼠右鍵按一下 ProductModel.tt，並選擇 [執行自訂工具]。</span><span class="sxs-lookup"><span data-stu-id="2ab25-210">If the code does not regenerate automatically, then right click on ProductModel.tt and choose “Run Custom Tool”.</span></span>

<span data-ttu-id="2ab25-211">如果您現在開啟 Category.cs 檔案 （這在 ProductModel.tt 之下巢狀），則您應該會看到產品集合中具有類型**ObservableListSource&lt;產品&gt;**。</span><span class="sxs-lookup"><span data-stu-id="2ab25-211">If you now open the Category.cs file (which is nested under ProductModel.tt) then you should see that the Products collection has the type **ObservableListSource&lt;Product&gt;**.</span></span>

<span data-ttu-id="2ab25-212">編譯專案。</span><span class="sxs-lookup"><span data-stu-id="2ab25-212">Compile the project.</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="2ab25-213">消極式載入</span><span class="sxs-lookup"><span data-stu-id="2ab25-213">Lazy Loading</span></span>

<span data-ttu-id="2ab25-214">**產品**屬性上的**類別目錄**類別並**類別**屬性**產品**類別為導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="2ab25-214">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="2ab25-215">在 Entity Framework 中，導覽屬性會提供瀏覽兩個實體類型之間的關聯性的方法。</span><span class="sxs-lookup"><span data-stu-id="2ab25-215">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="2ab25-216">EF 可讓您選擇載入相關的實體從資料庫自動第一次存取導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="2ab25-216">EF gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="2ab25-217">使用此類型的載入 （稱為消極式載入），請注意，第一次存取每個導覽屬性不同的查詢將會針對資料庫執行如果內容已不在內容中。</span><span class="sxs-lookup"><span data-stu-id="2ab25-217">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="2ab25-218">使用 POCO 實體類型時，EF 會建立衍生的 proxy 型別的執行個體在執行階段，然後再覆寫虛擬屬性類別，藉此加入載入攔截程序中的達到消極式載入。</span><span class="sxs-lookup"><span data-stu-id="2ab25-218">When using POCO entity types, EF achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="2ab25-219">若要取得相關物件的消極式載入，您必須宣告瀏覽做為屬性 getter**公用**並**虛擬**(**Overridable** Visual Basic 中)，且您的類別不可以是**密封**(**NotOverridable** Visual Basic 中)。</span><span class="sxs-lookup"><span data-stu-id="2ab25-219">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and you class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="2ab25-220">使用資料庫時的第一個導覽屬性會自動進行虛擬化來啟用消極式載入。</span><span class="sxs-lookup"><span data-stu-id="2ab25-220">When using Database First navigation properties are automatically made virtual to enable lazy loading.</span></span> <span data-ttu-id="2ab25-221">第一個程式碼區段中，我們選擇基於相同原因，請瀏覽屬性虛擬</span><span class="sxs-lookup"><span data-stu-id="2ab25-221">In the Code First section we chose to make the navigation properties virtual for the same reason</span></span>

## <a name="bind-object-to-controls"></a><span data-ttu-id="2ab25-222">物件繫結至控制項</span><span class="sxs-lookup"><span data-stu-id="2ab25-222">Bind Object to Controls</span></span>

<span data-ttu-id="2ab25-223">加入做為資料來源，此 WinForms 應用程式模型中所定義的類別。</span><span class="sxs-lookup"><span data-stu-id="2ab25-223">Add the classes that are defined in the model as data sources for this WinForms application.</span></span>

-   <span data-ttu-id="2ab25-224">從主功能表中，選取**專案-&gt;加入新的資料來源...**</span><span class="sxs-lookup"><span data-stu-id="2ab25-224">From the main menu, select **Project -&gt; Add New Data Source …**</span></span>
    <span data-ttu-id="2ab25-225">(在 Visual Studio 2010 中，您必須選取**的資料-&gt;加入新資料來源...**)</span><span class="sxs-lookup"><span data-stu-id="2ab25-225">(in Visual Studio 2010, you need to select **Data -&gt; Add New Data Source…**)</span></span>
-   <span data-ttu-id="2ab25-226">在 [選擇資料來源類型] 視窗中，選取**物件**，按一下 [**下一步]**</span><span class="sxs-lookup"><span data-stu-id="2ab25-226">In the Choose a Data Source Type window, select **Object** and click **Next**</span></span>
-   <span data-ttu-id="2ab25-227">在 選取資料物件 對話方塊中，展開**WinFormswithEFSample**兩次，然後選取**分類**就不需要選取 Product 資料來源，因為我們會透過該產品的上前往在 類別目錄資料來源上的屬性。</span><span class="sxs-lookup"><span data-stu-id="2ab25-227">In the Select the Data Objects dialog, unfold the **WinFormswithEFSample** two times and select **Category** There is no need to select the Product data source, because we will get to it through the Product’s property on the Category data source.</span></span>

    ![資料來源](~/ef6/media/datasource.png)

-   <span data-ttu-id="2ab25-229">按一下 **完成。**
    *如果未顯示 資料來源 視窗，選取 \* \* \* 檢視-&gt;其他 Windows-&gt;資料來源*\*</span><span class="sxs-lookup"><span data-stu-id="2ab25-229">Click **Finish.**
*If the Data Sources window is not showing up, select\*\*\*View -&gt; Other Windows-&gt; Data Sources*\*</span></span>
-   <span data-ttu-id="2ab25-230">按 釘選 圖示，讓資料來源 視窗不會不會自動隱藏。</span><span class="sxs-lookup"><span data-stu-id="2ab25-230">Press the pin icon, so the Data Sources window does not auto hide.</span></span> <span data-ttu-id="2ab25-231">您可能需要按 [重新整理] 按鈕，如果視窗已顯示。</span><span class="sxs-lookup"><span data-stu-id="2ab25-231">You may need to hit the refresh button if the window was already visible.</span></span>

    ![資料來源 2](~/ef6/media/datasource2.png)

-   <span data-ttu-id="2ab25-233">在 [方案總管] 中，按兩下**Form1.cs**設計工具中開啟主要表單的檔案。</span><span class="sxs-lookup"><span data-stu-id="2ab25-233">In Solution Explorer, double-click the **Form1.cs** file to open the main form in designer.</span></span>
-   <span data-ttu-id="2ab25-234">選取 **分類**資料來源，並將它拖曳到表單上。</span><span class="sxs-lookup"><span data-stu-id="2ab25-234">Select the **Category** data source and drag it on the form.</span></span> <span data-ttu-id="2ab25-235">根據預設，新的 DataGridView (**categoryDataGridView**) 並瀏覽工具列上的控制項加入至設計工具。</span><span class="sxs-lookup"><span data-stu-id="2ab25-235">By default, a new DataGridView (**categoryDataGridView**) and Navigation toolbar controls are added to the designer.</span></span> <span data-ttu-id="2ab25-236">這些控制項會繫結至 BindingSource (**categoryBindingSource**) 和繫結的巡覽器 (**categoryBindingNavigator**) 一併建立的元件。</span><span class="sxs-lookup"><span data-stu-id="2ab25-236">These controls are bound to the BindingSource (**categoryBindingSource**) and Binding Navigator (**categoryBindingNavigator**) components that are created as well.</span></span>
-   <span data-ttu-id="2ab25-237">在 編輯資料行**categoryDataGridView**。</span><span class="sxs-lookup"><span data-stu-id="2ab25-237">Edit the columns on the **categoryDataGridView**.</span></span> <span data-ttu-id="2ab25-238">我們想要設定**CategoryId**為唯讀的資料行。</span><span class="sxs-lookup"><span data-stu-id="2ab25-238">We want to set the **CategoryId** column to read-only.</span></span> <span data-ttu-id="2ab25-239">值**CategoryId**我們儲存資料之後，將會產生資料庫的屬性。</span><span class="sxs-lookup"><span data-stu-id="2ab25-239">The value for the **CategoryId** property is generated by the database after we save the data.</span></span>
    -   <span data-ttu-id="2ab25-240">DataGridView 控制項中以滑鼠右鍵按一下並選取 編輯資料行...</span><span class="sxs-lookup"><span data-stu-id="2ab25-240">Right-click the DataGridView control and select Edit Columns…</span></span>
    -   <span data-ttu-id="2ab25-241">選取 [CategoryId] 欄，然後將 ReadOnly 設定為 True</span><span class="sxs-lookup"><span data-stu-id="2ab25-241">Select the CategoryId column and set ReadOnly to True</span></span>
    -   <span data-ttu-id="2ab25-242">按 [確定]</span><span class="sxs-lookup"><span data-stu-id="2ab25-242">Press OK</span></span>
-   <span data-ttu-id="2ab25-243">選取產品類別目錄資料來源底下，並將它拖曳表單上。</span><span class="sxs-lookup"><span data-stu-id="2ab25-243">Select Products from under the Category data source and drag it on the form.</span></span> <span data-ttu-id="2ab25-244">ProductDataGridView 和 productBindingSource 會新增至表單。</span><span class="sxs-lookup"><span data-stu-id="2ab25-244">The productDataGridView and productBindingSource are added to the form.</span></span>
-   <span data-ttu-id="2ab25-245">編輯 productDataGridView 上的資料行。</span><span class="sxs-lookup"><span data-stu-id="2ab25-245">Edit the columns on the productDataGridView.</span></span> <span data-ttu-id="2ab25-246">我們想要隱藏 CategoryId 和類別目錄的資料行，並將產品編號設定為唯讀。</span><span class="sxs-lookup"><span data-stu-id="2ab25-246">We want to hide the CategoryId and Category columns and set ProductId to read-only.</span></span> <span data-ttu-id="2ab25-247">ProductId 屬性的值是由資料庫產生的之後我們儲存的資料。</span><span class="sxs-lookup"><span data-stu-id="2ab25-247">The value for the ProductId property is generated by the database after we save the data.</span></span>
    -   <span data-ttu-id="2ab25-248">DataGridView 控制項中以滑鼠右鍵按一下並選取**編輯資料行...**.</span><span class="sxs-lookup"><span data-stu-id="2ab25-248">Right-click the DataGridView control and select **Edit Columns...**.</span></span>
    -   <span data-ttu-id="2ab25-249">選取  **ProductId**資料行並將**ReadOnly**來**True**。</span><span class="sxs-lookup"><span data-stu-id="2ab25-249">Select the **ProductId** column and set **ReadOnly** to **True**.</span></span>
    -   <span data-ttu-id="2ab25-250">選取 [ **CategoryId**資料行，然後按**移除**] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="2ab25-250">Select the **CategoryId** column and press the **Remove** button.</span></span> <span data-ttu-id="2ab25-251">執行相同的作業**分類**資料行。</span><span class="sxs-lookup"><span data-stu-id="2ab25-251">Do the same with the **Category** column.</span></span>
    -   <span data-ttu-id="2ab25-252">按下**確定**。</span><span class="sxs-lookup"><span data-stu-id="2ab25-252">Press **OK**.</span></span>

    <span data-ttu-id="2ab25-253">到目前為止，我們會與設計工具中的 BindingSource 元件關聯 DataGridView 控制項。</span><span class="sxs-lookup"><span data-stu-id="2ab25-253">So far, we associated our DataGridView controls with BindingSource components in the designer.</span></span> <span data-ttu-id="2ab25-254">下一節我們將程式碼加入程式碼後置 categoryBindingSource.DataSource 設 DbContext 目前所追蹤之實體的集合。</span><span class="sxs-lookup"><span data-stu-id="2ab25-254">In the next section we will add code to the code behind to set categoryBindingSource.DataSource to the collection of entities that are currently tracked by DbContext.</span></span> <span data-ttu-id="2ab25-255">當我們拖曳和置放的產品，從該類別中，WinForms 之下花了負責設定產品的 categoryBindingSource 和 productsBindingSource.DataMember 屬性 productsBindingSource.DataSource 屬性。</span><span class="sxs-lookup"><span data-stu-id="2ab25-255">When we dragged-and-dropped Products from under the Category, the WinForms took care of setting up the productsBindingSource.DataSource property to categoryBindingSource and productsBindingSource.DataMember property to Products.</span></span> <span data-ttu-id="2ab25-256">因為此繫結，則會顯示在 productDataGridView 的只有屬於目前選取之分類的產品。</span><span class="sxs-lookup"><span data-stu-id="2ab25-256">Because of this binding, only the products that belong to the currently selected Category will be displayed in the productDataGridView.</span></span>
-   <span data-ttu-id="2ab25-257">啟用**儲存**上按一下滑鼠右鍵，然後選取 [瀏覽] 工具列按鈕**已啟用**。</span><span class="sxs-lookup"><span data-stu-id="2ab25-257">Enable the **Save** button on the Navigation toolbar by clicking the right mouse button and selecting **Enabled**.</span></span>

    ![1 的表單設計工具](~/ef6/media/form1-designer.png)

-   <span data-ttu-id="2ab25-259">加入事件處理常式，儲存按兩下按鈕的按鈕。</span><span class="sxs-lookup"><span data-stu-id="2ab25-259">Add the event handler for the save button by double-clicking on the button.</span></span> <span data-ttu-id="2ab25-260">這將會加入事件處理常式，並將您帶到程式碼後置中，表單。</span><span class="sxs-lookup"><span data-stu-id="2ab25-260">This will add the event handler and bring you to the code behind for the form.</span></span> <span data-ttu-id="2ab25-261">程式碼**categoryBindingNavigatorSaveItem\_按一下**事件處理常式會新增下一節。</span><span class="sxs-lookup"><span data-stu-id="2ab25-261">The code for the **categoryBindingNavigatorSaveItem\_Click** event handler will be added in the next section.</span></span>

## <a name="add-the-code-that-handles-data-interaction"></a><span data-ttu-id="2ab25-262">新增處理與資料互動的程式碼</span><span class="sxs-lookup"><span data-stu-id="2ab25-262">Add the Code that Handles Data Interaction</span></span>

<span data-ttu-id="2ab25-263">現在，我們要加入的程式碼，用以 ProductContext 執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="2ab25-263">We'll now add the code to use the ProductContext to perform data access.</span></span> <span data-ttu-id="2ab25-264">更新主表單的視窗的程式碼，如下所示。</span><span class="sxs-lookup"><span data-stu-id="2ab25-264">Update the code for the main form window as shown below.</span></span>

<span data-ttu-id="2ab25-265">程式碼會宣告 ProductContext 的長時間執行執行個體。</span><span class="sxs-lookup"><span data-stu-id="2ab25-265">The code declares a long-running instance of ProductContext.</span></span> <span data-ttu-id="2ab25-266">ProductContext 物件用來查詢，並將資料儲存至資料庫。</span><span class="sxs-lookup"><span data-stu-id="2ab25-266">The ProductContext object is used to query and save data to the database.</span></span> <span data-ttu-id="2ab25-267">從覆寫 OnClosing 方法接著呼叫 ProductContext 執行個體上的 dispose （） 方法。</span><span class="sxs-lookup"><span data-stu-id="2ab25-267">The Dispose() method on the ProductContext instance is then called from the overridden OnClosing method.</span></span> <span data-ttu-id="2ab25-268">程式碼註解提供程式碼所執行的作業的相關詳細資料。</span><span class="sxs-lookup"><span data-stu-id="2ab25-268">The code comments provide details about what the code does.</span></span>

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

## <a name="test-the-windows-forms-application"></a><span data-ttu-id="2ab25-269">測試 Windows Forms 應用程式</span><span class="sxs-lookup"><span data-stu-id="2ab25-269">Test the Windows Forms Application</span></span>

-   <span data-ttu-id="2ab25-270">編譯並執行應用程式，您可以測試功能。</span><span class="sxs-lookup"><span data-stu-id="2ab25-270">Compile and run the application and you can test out the functionality.</span></span>

    ![1 會形成儲存前](~/ef6/media/form1beforesave.png)

-   <span data-ttu-id="2ab25-272">在儲存之後產生的存放區索引鍵會顯示在螢幕上。</span><span class="sxs-lookup"><span data-stu-id="2ab25-272">After saving the store generated keys are shown on the screen.</span></span>

    ![形成儲存 1 後](~/ef6/media/form1aftersave.png)

-   <span data-ttu-id="2ab25-274">如果您使用 Code First，則您也會看到所**WinFormswithEFSample.ProductContext**為您建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="2ab25-274">If you used Code First, then you will also see that a **WinFormswithEFSample.ProductContext** database is created for you.</span></span>

    ![Server 物件總管](~/ef6/media/serverobjexplorer.png)
