---
title: WPF 入門-EF Core
author: jeremylikness
ms.author: jeliknes
ms.date: 07/24/2020
uid: core/get-started/wpf
ms.openlocfilehash: 958fc579a72a9bf3c97c5551d55777df6c32293f
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535625"
---
# <a name="getting-started-with-wpf"></a><span data-ttu-id="e90ed-102">WPF 使用者入門</span><span class="sxs-lookup"><span data-stu-id="e90ed-102">Getting Started with WPF</span></span>

<span data-ttu-id="e90ed-103">此逐步解說會示範如何將 POCO 類型系結至「主要-詳細資料」表單中的 WPF 控制項。</span><span class="sxs-lookup"><span data-stu-id="e90ed-103">This step-by-step walkthrough shows how to bind POCO types to WPF controls in a "main-detail" form.</span></span> <span data-ttu-id="e90ed-104">應用程式會使用 Entity Framework Api，以資料庫中的資料來填入物件、追蹤變更，並將資料保存至資料庫。</span><span class="sxs-lookup"><span data-stu-id="e90ed-104">The application uses the Entity Framework APIs to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="e90ed-105">模型會定義兩個參與一對多關聯性的類型：**類別目錄**（主體 \\ 主要）和**產品**（相依 \\ 詳細資料）。</span><span class="sxs-lookup"><span data-stu-id="e90ed-105">The model defines two types that participate in one-to-many relationship: **Category** (principal\\main) and **Product** (dependent\\detail).</span></span> <span data-ttu-id="e90ed-106">WPF 資料系結架構可讓您在相關物件之間導覽：選取主視圖中的資料列會使詳細資料檢視以對應的子資料進行更新。</span><span class="sxs-lookup"><span data-stu-id="e90ed-106">The WPF data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="e90ed-107">本逐步解說中的螢幕擷取畫面和程式代碼清單取自 Visual Studio 2019 16.6.5。</span><span class="sxs-lookup"><span data-stu-id="e90ed-107">The screen shots and code listings in this walkthrough are taken from Visual Studio 2019 16.6.5.</span></span>

> [!TIP]
> <span data-ttu-id="e90ed-108">您可以[在 GitHub 上](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF)查看這篇文章的範例。</span><span class="sxs-lookup"><span data-stu-id="e90ed-108">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF).</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="e90ed-109">必要條件</span><span class="sxs-lookup"><span data-stu-id="e90ed-109">Pre-Requisites</span></span>

* <span data-ttu-id="e90ed-110">您必須在已選取 **.net 桌面工作負載**的情況下安裝 Visual Studio 2019 16.3 或更新版本，才能完成此逐步解說。</span><span class="sxs-lookup"><span data-stu-id="e90ed-110">You need to have Visual Studio 2019 16.3 or later installed with the **.NET desktop workload** selected to complete this walkthrough.</span></span>
    
    <span data-ttu-id="e90ed-111">如需安裝最新版本 Visual Studio 的詳細資訊，請參閱[Install Visual Studio](/visualstudio/install/install-visual-studio)。</span><span class="sxs-lookup"><span data-stu-id="e90ed-111">For more information about installing the latest version of Visual Studio, see [Install Visual Studio](/visualstudio/install/install-visual-studio).</span></span>

## <a name="create-the-application"></a><span data-ttu-id="e90ed-112">建立應用程式</span><span class="sxs-lookup"><span data-stu-id="e90ed-112">Create the Application</span></span>

1. <span data-ttu-id="e90ed-113">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e90ed-113">Open Visual Studio</span></span>
2. <span data-ttu-id="e90ed-114">在開始視窗中，選擇 [建立新專案]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="e90ed-114">On the start window, choose **Create new project**.</span></span>
3. <span data-ttu-id="e90ed-115">搜尋 "WPF"，選擇 [ **Wpf 應用程式（.Net Core）** ]，然後選擇 **[下一步]**。</span><span class="sxs-lookup"><span data-stu-id="e90ed-115">Search for "WPF," choose **WPF App (.NET Core)** and then choose **Next**.</span></span>
4. <span data-ttu-id="e90ed-116">在下一個畫面中，指定專案的名稱，例如**GetStartedWPF**，然後選擇 [**建立]。**</span><span class="sxs-lookup"><span data-stu-id="e90ed-116">At the next screen, give the project a name, for example, **GetStartedWPF**, and choose **Create.**</span></span>

## <a name="install-the-entity-framework-nuget-packages"></a><span data-ttu-id="e90ed-117">安裝 Entity Framework NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="e90ed-117">Install the Entity Framework NuGet packages</span></span>

1. <span data-ttu-id="e90ed-118">以滑鼠右鍵按一下方案，然後選擇 [**管理方案的 NuGet 套件**...]</span><span class="sxs-lookup"><span data-stu-id="e90ed-118">Right-click on the solution and choose **Manage NuGet Packages for Solution...**</span></span>

    ![Manage NuGet Packages](_static/wpf-tutorial-nuget.jpg)

1. <span data-ttu-id="e90ed-120">在搜尋方塊中鍵入 `entityframeworkcore.sqlite`。</span><span class="sxs-lookup"><span data-stu-id="e90ed-120">Type `entityframeworkcore.sqlite` in the search box.</span></span>
1. <span data-ttu-id="e90ed-121">選取 [ **microsoft.entityframeworkcore** ] 套件。</span><span class="sxs-lookup"><span data-stu-id="e90ed-121">Select the **Microsoft.EntityFrameworkCore.Sqlite** package.</span></span>
1. <span data-ttu-id="e90ed-122">檢查右窗格中的專案，然後按一下 [**安裝**]。</span><span class="sxs-lookup"><span data-stu-id="e90ed-122">Check the project in the right pane and click **Install**</span></span>

    ![Sqlite 封裝](_static/wpf-tutorial-sqlite.jpg)

1. <span data-ttu-id="e90ed-124">重複這些步驟來搜尋 `entityframeworkcore.proxies` 並安裝**microsoft.entityframeworkcore**。</span><span class="sxs-lookup"><span data-stu-id="e90ed-124">Repeat the steps to search for `entityframeworkcore.proxies` and install **Microsoft.EntityFrameworkCore.Proxies**.</span></span>

> [!NOTE]
> <span data-ttu-id="e90ed-125">當您安裝 Sqlite 套件時，它會自動提取相關的**microsoft.entityframeworkcore**基底套件。</span><span class="sxs-lookup"><span data-stu-id="e90ed-125">When you installed the Sqlite package, it automatically pulled down the related **Microsoft.EntityFrameworkCore** base package.</span></span> <span data-ttu-id="e90ed-126">**Microsoft.entityframeworkcore**封裝可支援「延遲載入」資料。</span><span class="sxs-lookup"><span data-stu-id="e90ed-126">The **Microsoft.EntityFrameworkCore.Proxies** package provides support for "lazy-loading" data.</span></span> <span data-ttu-id="e90ed-127">這表示當您有具有子實體的實體時，只有父代會在初始載入時提取。</span><span class="sxs-lookup"><span data-stu-id="e90ed-127">This means when you have entities with child entities, only the parents are fetched on the initial load.</span></span> <span data-ttu-id="e90ed-128">Proxy 會在嘗試存取子實體時偵測，並視需要自動載入它們。</span><span class="sxs-lookup"><span data-stu-id="e90ed-128">The proxies detect when an attempt to access the child entities is made and automatically loads them on demand.</span></span> 

## <a name="define-a-model"></a><span data-ttu-id="e90ed-129">定義模型</span><span class="sxs-lookup"><span data-stu-id="e90ed-129">Define a Model</span></span>

<span data-ttu-id="e90ed-130">在此逐步解說中，您將使用「code first」來執行模型。</span><span class="sxs-lookup"><span data-stu-id="e90ed-130">In this walkthrough you will implement a model using "code first."</span></span> <span data-ttu-id="e90ed-131">這表示 EF Core 會根據您定義的 c # 類別來建立資料庫資料表和架構。</span><span class="sxs-lookup"><span data-stu-id="e90ed-131">This means that EF Core will create the database tables and schema based on the C# classes you define.</span></span>

<span data-ttu-id="e90ed-132">加入新的類別。</span><span class="sxs-lookup"><span data-stu-id="e90ed-132">Add a new class.</span></span> <span data-ttu-id="e90ed-133">為它命名： `Product.cs` ，並將它填入如下：</span><span class="sxs-lookup"><span data-stu-id="e90ed-133">Give it the name: `Product.cs` and populate it like this:</span></span>

**`Product.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Product.cs)]

<span data-ttu-id="e90ed-134">接下來，新增名為的類別 `Category.cs` ，並在其中填入下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="e90ed-134">Next, add a class named `Category.cs` and populate it with the following code:</span></span>

**`Category.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Category.cs)]

<span data-ttu-id="e90ed-135">**Product**類別上**Category**類別和**category**屬性的**Products**屬性是導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="e90ed-135">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="e90ed-136">在 Entity Framework 中，導覽屬性會提供一種方式來導覽兩個實體類型之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="e90ed-136">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="e90ed-137">除了定義實體以外，您還需要定義衍生自 DbCoNtext 的類別，並公開 DbSet &lt; TEntity &gt; 屬性。</span><span class="sxs-lookup"><span data-stu-id="e90ed-137">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="e90ed-138">DbSet &lt; TEntity &gt; 屬性可讓內容知道您想要包含在模型中的類型。</span><span class="sxs-lookup"><span data-stu-id="e90ed-138">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="e90ed-139">DbCoNtext 衍生類型的實例會在運行時間管理實體物件，其中包括以資料庫的資料填入物件、變更追蹤，以及將資料保存至資料庫。</span><span class="sxs-lookup"><span data-stu-id="e90ed-139">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

<span data-ttu-id="e90ed-140">使用下列定義，將新的 `ProductContext.cs` 類別新增至專案：</span><span class="sxs-lookup"><span data-stu-id="e90ed-140">Add a new `ProductContext.cs` class to the project with the following definition:</span></span>

**`ProductContext.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/ProductContext.cs)]

* <span data-ttu-id="e90ed-141">`DbSet`會通知 EF Core 哪些 c # 實體應該對應到資料庫。</span><span class="sxs-lookup"><span data-stu-id="e90ed-141">The `DbSet` informs EF Core what C# entities should be mapped to the database.</span></span>
* <span data-ttu-id="e90ed-142">設定 EF Core 的方式有很多種 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="e90ed-142">There are a variety of ways to configure the EF Core `DbContext`.</span></span> <span data-ttu-id="e90ed-143">您可以在：設定[DbCoNtext](/ef/core/miscellaneous/configuring-dbcontext)中閱讀相關資訊。</span><span class="sxs-lookup"><span data-stu-id="e90ed-143">You can read about them in: [Configuring a DbContext](/ef/core/miscellaneous/configuring-dbcontext).</span></span>
* <span data-ttu-id="e90ed-144">這個範例會使用覆 `OnConfiguring` 寫來指定 Sqlite 資料檔案。</span><span class="sxs-lookup"><span data-stu-id="e90ed-144">This example uses the `OnConfiguring` override to specify a Sqlite data file.</span></span>
* <span data-ttu-id="e90ed-145">`UseLazyLoadingProxies`呼叫會告訴 EF Core 執行消極式載入，因此從父系存取時，會自動載入子實體。</span><span class="sxs-lookup"><span data-stu-id="e90ed-145">The `UseLazyLoadingProxies` call tells EF Core to implement lazy-loading, so child entities are automatically loaded when accessed from the parent.</span></span>

<span data-ttu-id="e90ed-146">按**CTRL + SHIFT + B** ，或流覽至**組建 &gt; 組建方案**來編譯專案。</span><span class="sxs-lookup"><span data-stu-id="e90ed-146">Press **CTRL+SHIFT+B** or navigate to **Build &gt; Build Solution** to compile the project.</span></span>

> [!TIP]
> <span data-ttu-id="e90ed-147">瞭解不同的 was，讓您的資料庫和 EF Core 模型保持同步：[管理資料庫架構](/ef/core/managing-schemas)。</span><span class="sxs-lookup"><span data-stu-id="e90ed-147">Learn about the different was to keep your database and EF Core models in sync: [Managing Database Schemas](/ef/core/managing-schemas).</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="e90ed-148">消極式載入</span><span class="sxs-lookup"><span data-stu-id="e90ed-148">Lazy Loading</span></span>

<span data-ttu-id="e90ed-149">**Product**類別上**Category**類別和**category**屬性的**Products**屬性是導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="e90ed-149">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="e90ed-150">在 Entity Framework Core 中，導覽屬性會提供一種方式來導覽兩個實體類型之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="e90ed-150">In Entity Framework Core, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="e90ed-151">EF Core 提供您第一次存取導覽屬性時，自動從資料庫載入相關實體的選項。</span><span class="sxs-lookup"><span data-stu-id="e90ed-151">EF Core gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="e90ed-152">使用這種類型的載入（稱為消極式載入）時，請注意，當您第一次存取每個導覽屬性時，如果內容尚未存在於環境中，則會針對資料庫執行個別的查詢。</span><span class="sxs-lookup"><span data-stu-id="e90ed-152">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="e90ed-153">使用「單純舊的 c # 物件」（POCO）實體類型時，EF Core 會在執行時間建立衍生 proxy 類型的實例，然後覆寫類別中的虛擬屬性來新增載入攔截，以達到消極式載入。</span><span class="sxs-lookup"><span data-stu-id="e90ed-153">When using "Plain Old C# Object" (POCO) entity types, EF Core achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="e90ed-154">若要取得相關物件的消極式載入，您必須將導覽屬性 getter 宣告為**public**和**virtual** （可在 Visual Basic 中覆**寫**），而且您的類別不得為**密封**（在 Visual Basic 中為**NotOverridable** ）。</span><span class="sxs-lookup"><span data-stu-id="e90ed-154">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and your class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="e90ed-155">使用 Database First 時，會自動將導覽屬性設為虛擬，以啟用消極式載入。</span><span class="sxs-lookup"><span data-stu-id="e90ed-155">When using Database First, navigation properties are automatically made virtual to enable lazy loading.</span></span> 

## <a name="bind-object-to-controls"></a><span data-ttu-id="e90ed-156">將物件系結至控制項</span><span class="sxs-lookup"><span data-stu-id="e90ed-156">Bind Object to Controls</span></span>

<span data-ttu-id="e90ed-157">加入模型中所定義的類別，做為此 WPF 應用程式的資料來源。</span><span class="sxs-lookup"><span data-stu-id="e90ed-157">Add the classes that are defined in the model as data sources for this WPF application.</span></span>

1. <span data-ttu-id="e90ed-158">按兩下方案總管中的 [ **mainwindow.xaml** ] 以開啟主要表單</span><span class="sxs-lookup"><span data-stu-id="e90ed-158">Double-click **MainWindow.xaml** in Solution Explorer to open the main form</span></span>
1. <span data-ttu-id="e90ed-159">選擇 [ **xaml** ] 索引標籤來編輯 xaml。</span><span class="sxs-lookup"><span data-stu-id="e90ed-159">Choose the **XAML** tab to edit the XAML.</span></span>
1. <span data-ttu-id="e90ed-160">緊接在開頭 `Window` 標記之後，新增下列來源以連接到 EF Core 實體。</span><span class="sxs-lookup"><span data-stu-id="e90ed-160">Immediately after the opening `Window` tag, add the following sources to connect to the EF Core entities.</span></span>

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=1-13&highlight=9-13)]

1. <span data-ttu-id="e90ed-161">這會設定「父系」分類的來源，以及「詳細資料」產品的第二個來源。</span><span class="sxs-lookup"><span data-stu-id="e90ed-161">This sets up source for the "parent" categories, and second source for the "detail" products.</span></span>
1. <span data-ttu-id="e90ed-162">接下來，在結束記號之後，將下列標記新增至您的 XAML `Window.Resources` 。</span><span class="sxs-lookup"><span data-stu-id="e90ed-162">Next, add the following markup to your XAML after the closing `Window.Resources` tag.</span></span>

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=15-26)]

1. <span data-ttu-id="e90ed-163">請注意， `CategoryId` 會設定為， `ReadOnly` 因為它是由資料庫所指派，而且無法變更。</span><span class="sxs-lookup"><span data-stu-id="e90ed-163">Note that the `CategoryId` is set to `ReadOnly` because it is assigned by the database and cannot be changed.</span></span>

## <a name="adding-a-details-grid"></a><span data-ttu-id="e90ed-164">加入詳細資料方格</span><span class="sxs-lookup"><span data-stu-id="e90ed-164">Adding a Details Grid</span></span>

<span data-ttu-id="e90ed-165">現在方格已經存在，可顯示類別目錄，您可以加入 [詳細資料] 方格來顯示產品。</span><span class="sxs-lookup"><span data-stu-id="e90ed-165">Now that the grid exists to display categories, the details grid can be added to show products.</span></span>

**`MainWindow.xaml`**

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=27-40)]

<span data-ttu-id="e90ed-166">最後，將 `Save` click 事件中的按鈕和連線加入至 `Button_Click` 。</span><span class="sxs-lookup"><span data-stu-id="e90ed-166">Finally, add a `Save` button and wire in the click event to `Button_Click`.</span></span>

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=41-42)]

<span data-ttu-id="e90ed-167">您的設計檢視看起來應該像這樣：</span><span class="sxs-lookup"><span data-stu-id="e90ed-167">Your design view should look like this:</span></span>

![WPF 設計工具的螢幕擷取畫面](_static/wpf-tutorial-designer.jpg)

## <a name="add-code-that-handles-data-interaction"></a><span data-ttu-id="e90ed-169">加入處理資料互動的程式碼</span><span class="sxs-lookup"><span data-stu-id="e90ed-169">Add Code that Handles Data Interaction</span></span>

<span data-ttu-id="e90ed-170">現在可以在主視窗中加入一些事件處理常式。</span><span class="sxs-lookup"><span data-stu-id="e90ed-170">It's time to add some event handlers to the main window.</span></span>

1. <span data-ttu-id="e90ed-171">在 [XAML] 視窗中，按一下\*\* &lt; 視窗 &gt; \*\*元素以選取主視窗。</span><span class="sxs-lookup"><span data-stu-id="e90ed-171">In the XAML window, click on the **&lt;Window&gt;** element, to select the main window.</span></span>
1. <span data-ttu-id="e90ed-172">在 [**屬性**] 視窗中，選擇右上方的 [**事件**]，然後按兩下 [已**載入**] 標籤右邊的文字方塊。</span><span class="sxs-lookup"><span data-stu-id="e90ed-172">In the **Properties** window choose **Events** at the top right, then double-click the text box to right of the **Loaded** label.</span></span>

    ![主視窗屬性](_static/wpf-tutorial-loaded.jpg)

<span data-ttu-id="e90ed-174">這會帶您前往表單的程式碼後置，我們現在會編輯程式碼，以使用 `ProductContext` 執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="e90ed-174">This brings you to the code behind for the form, we'll now edit the code to use the `ProductContext` to perform data access.</span></span> <span data-ttu-id="e90ed-175">更新程式碼，如下所示。</span><span class="sxs-lookup"><span data-stu-id="e90ed-175">Update the code as shown below.</span></span>

<span data-ttu-id="e90ed-176">此程式碼會宣告的長時間執行實例 `ProductContext` 。</span><span class="sxs-lookup"><span data-stu-id="e90ed-176">The code declares a long-running instance of `ProductContext`.</span></span> <span data-ttu-id="e90ed-177">`ProductContext`物件可用來查詢和儲存資料至資料庫。</span><span class="sxs-lookup"><span data-stu-id="e90ed-177">The `ProductContext` object is used to query and save data to the database.</span></span> <span data-ttu-id="e90ed-178">`Dispose()` `ProductContext` 然後會從覆寫的方法呼叫實例上的方法 `OnClosing` 。</span><span class="sxs-lookup"><span data-stu-id="e90ed-178">The `Dispose()` method on the `ProductContext` instance is then called from the overridden `OnClosing` method.</span></span><span data-ttu-id="e90ed-179">程式碼註解會說明每個步驟的作用。</span><span class="sxs-lookup"><span data-stu-id="e90ed-179"> The code comments explain what each step does.</span></span>

**`MainWindow.xaml.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml.cs)]

> [!NOTE]
> <span data-ttu-id="e90ed-180">程式碼會使用的呼叫， `EnsureCreated()` 在第一次執行時建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="e90ed-180">The code uses a call to `EnsureCreated()` to build the database on the first run.</span></span> <span data-ttu-id="e90ed-181">這是可接受的示範，但在生產環境應用程式中，您應該查看[遷移](/ef/core/managing-schemas/migrations/)以管理您的架構。</span><span class="sxs-lookup"><span data-stu-id="e90ed-181">This is acceptable for demos, but in production apps you should look at [migrations](/ef/core/managing-schemas/migrations/) to manage your schema.</span></span> <span data-ttu-id="e90ed-182">程式碼也會以同步方式執行，因為它使用本機 SQLite 資料庫。</span><span class="sxs-lookup"><span data-stu-id="e90ed-182">The code also executes synchronously because it uses a local SQLite database.</span></span> <span data-ttu-id="e90ed-183">針對通常牽涉到遠端伺服器的生產案例，請考慮使用和方法的非同步 `Load` 版本 `SaveChanges` 。</span><span class="sxs-lookup"><span data-stu-id="e90ed-183">For production scenarios that typically involve a remote server, consider using the asynchronous versions of the `Load` and `SaveChanges` methods.</span></span>

## <a name="test-the-wpf-application"></a><span data-ttu-id="e90ed-184">測試 WPF 應用程式</span><span class="sxs-lookup"><span data-stu-id="e90ed-184">Test the WPF Application</span></span>

<span data-ttu-id="e90ed-185">按**F5**或選擇 [Debug] [ \*\* &gt; 開始調試\*\*程式]，以編譯並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="e90ed-185">Compile and run the application by pressing **F5** or choosing **Debug &gt; Start Debugging**.</span></span> <span data-ttu-id="e90ed-186">應該使用名為的檔案自動建立資料庫 `products.db` 。</span><span class="sxs-lookup"><span data-stu-id="e90ed-186">The database should be automatically created with a file named `products.db`.</span></span> <span data-ttu-id="e90ed-187">輸入類別名稱，然後按 enter 鍵，然後將產品新增至下方方格。</span><span class="sxs-lookup"><span data-stu-id="e90ed-187">Enter a category name and hit enter, then add products to the lower grid.</span></span> <span data-ttu-id="e90ed-188">按一下 [儲存]，並以資料庫提供的識別碼監看格線重新整理。</span><span class="sxs-lookup"><span data-stu-id="e90ed-188">Click save and watch the grid refresh with the database provided ids.</span></span> <span data-ttu-id="e90ed-189">反白顯示資料列，並按**Delete**鍵來移除資料列。</span><span class="sxs-lookup"><span data-stu-id="e90ed-189">Highlight a row and hit **Delete** to remove the row.</span></span> <span data-ttu-id="e90ed-190">當您按一下 [**儲存**] 時，將會刪除實體。</span><span class="sxs-lookup"><span data-stu-id="e90ed-190">The entity will be deleted when you click **Save**.</span></span>

![正在執行應用程式](_static/wpf-tutorial-app.jpg)

## <a name="property-change-notification"></a><span data-ttu-id="e90ed-192">屬性變更通知</span><span class="sxs-lookup"><span data-stu-id="e90ed-192">Property Change Notification</span></span>

<span data-ttu-id="e90ed-193">這個範例依賴四個步驟來同步處理實體與 UI。</span><span class="sxs-lookup"><span data-stu-id="e90ed-193">This example relies on four steps to synchronize the entities with the UI.</span></span> 

1. <span data-ttu-id="e90ed-194">初始呼叫會 `_context.Categories.Load()` 載入類別目錄資料。</span><span class="sxs-lookup"><span data-stu-id="e90ed-194">The initial call `_context.Categories.Load()` loads the categories data.</span></span>
1. <span data-ttu-id="e90ed-195">消極式載入 proxy 會載入相依產品資料。</span><span class="sxs-lookup"><span data-stu-id="e90ed-195">The lazy-loading proxies load the dependent products data.</span></span>
1. <span data-ttu-id="e90ed-196">EF Core 的內建變更追蹤會在呼叫時，對實體進行必要的修改，包括插入和刪除 `_context.SaveChanges()` 。</span><span class="sxs-lookup"><span data-stu-id="e90ed-196">EF Core's built-in change tracking makes the necessary modifications to entities, including insertions and deletions, when `_context.SaveChanges()` is called.</span></span>
1. <span data-ttu-id="e90ed-197">的呼叫會以 `DataGridView.Items.Refresh()` 新產生的識別碼來強制重載。</span><span class="sxs-lookup"><span data-stu-id="e90ed-197">The calls to `DataGridView.Items.Refresh()` force a reload with the newly generated ids.</span></span>

<span data-ttu-id="e90ed-198">這適用于我們的快速入門範例，但您可能需要額外的程式碼才能進行其他案例。</span><span class="sxs-lookup"><span data-stu-id="e90ed-198">This works for our getting started sample, but you may require additional code for other scenarios.</span></span> <span data-ttu-id="e90ed-199">WPF 控制項會藉由讀取實體上的欄位和屬性來呈現 UI。</span><span class="sxs-lookup"><span data-stu-id="e90ed-199">WPF controls render the UI by reading the fields and properties on your entities.</span></span> <span data-ttu-id="e90ed-200">當您在使用者介面（UI）中編輯值時，該值會傳遞至您的實體。</span><span class="sxs-lookup"><span data-stu-id="e90ed-200">When you edit a value in the user interface (UI), that value is passed to your entity.</span></span> <span data-ttu-id="e90ed-201">當您直接變更實體的屬性值（例如從資料庫載入）時，WPF 不會立即反映 UI 中的變更。</span><span class="sxs-lookup"><span data-stu-id="e90ed-201">When you change the value of a property directly on your entity, such as loading it from the database, WPF will not immediately reflect the changes in the UI.</span></span> <span data-ttu-id="e90ed-202">呈現引擎必須收到變更的通知。</span><span class="sxs-lookup"><span data-stu-id="e90ed-202">The rendering engine must be notified of the changes.</span></span> <span data-ttu-id="e90ed-203">專案是藉由手動呼叫來執行這項操作 `Refresh()` 。</span><span class="sxs-lookup"><span data-stu-id="e90ed-203">The project did this by manually calling `Refresh()`.</span></span> <span data-ttu-id="e90ed-204">自動化此通知的簡單方式是藉由執行[INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged)介面。</span><span class="sxs-lookup"><span data-stu-id="e90ed-204">An easy way to automate this notification is by implementing the [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) interface.</span></span> <span data-ttu-id="e90ed-205">WPF 元件會自動偵測介面，並註冊變更事件。</span><span class="sxs-lookup"><span data-stu-id="e90ed-205">WPF components will automatically detect the interface and register for change events.</span></span> <span data-ttu-id="e90ed-206">實體會負責引發這些事件。</span><span class="sxs-lookup"><span data-stu-id="e90ed-206">The entity is responsible for raising these events.</span></span> 

> [!TIP]
> <span data-ttu-id="e90ed-207">若要深入瞭解如何處理變更，請參閱：[如何執行屬性變更通知](/dotnet/framework/wpf/data/how-to-implement-property-change-notification)。</span><span class="sxs-lookup"><span data-stu-id="e90ed-207">To learn more about how to handle changes, read: [How to implement property change notification](/dotnet/framework/wpf/data/how-to-implement-property-change-notification).</span></span>

## <a name="next-steps"></a><span data-ttu-id="e90ed-208">後續步驟</span><span class="sxs-lookup"><span data-stu-id="e90ed-208">Next Steps</span></span>

<span data-ttu-id="e90ed-209">深入瞭解如何設定[DbCoNtext](/ef/core/miscellaneous/configuring-dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="e90ed-209">Learn more about [Configuring a DbContext](/ef/core/miscellaneous/configuring-dbcontext).</span></span>