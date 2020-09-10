---
title: 使用 WPF 的開始-EF Core
description: 使用 WPF 搭配 Entity Framework Core 的使用者入門教學課程
author: jeremylikness
ms.author: jeliknes
ms.date: 07/24/2020
uid: core/get-started/wpf
ms.openlocfilehash: 1198da5c9564663ca26392b33462c727275a432d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619300"
---
# <a name="getting-started-with-wpf"></a><span data-ttu-id="54ff7-103">WPF 使用者入門</span><span class="sxs-lookup"><span data-stu-id="54ff7-103">Getting Started with WPF</span></span>

<span data-ttu-id="54ff7-104">本逐步解說將示範如何在「主要詳細資料」表單中，將 POCO 類型系結至 WPF 控制項。</span><span class="sxs-lookup"><span data-stu-id="54ff7-104">This step-by-step walkthrough shows how to bind POCO types to WPF controls in a "main-detail" form.</span></span> <span data-ttu-id="54ff7-105">應用程式會使用 Entity Framework Api，以資料庫中的資料填入物件、追蹤變更，以及將資料保存到資料庫。</span><span class="sxs-lookup"><span data-stu-id="54ff7-105">The application uses the Entity Framework APIs to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="54ff7-106">此模型會定義兩種參與一對多關聯性的類型： **類別** (主體 \\ 主要) 和 **產品** (相依 \\ 詳細資料) 。</span><span class="sxs-lookup"><span data-stu-id="54ff7-106">The model defines two types that participate in one-to-many relationship: **Category** (principal\\main) and **Product** (dependent\\detail).</span></span> <span data-ttu-id="54ff7-107">WPF 資料系結架構會啟用相關物件之間的導覽：選取主視圖中的資料列會導致詳細資料檢視以對應的子資料更新。</span><span class="sxs-lookup"><span data-stu-id="54ff7-107">The WPF data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="54ff7-108">本逐步解說中的螢幕擷取畫面和程式代碼清單取自 Visual Studio 2019 16.6.5。</span><span class="sxs-lookup"><span data-stu-id="54ff7-108">The screen shots and code listings in this walkthrough are taken from Visual Studio 2019 16.6.5.</span></span>

> [!TIP]
> <span data-ttu-id="54ff7-109">您可以 [在 GitHub 上](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF)查看這篇文章的範例。</span><span class="sxs-lookup"><span data-stu-id="54ff7-109">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF).</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="54ff7-110">必要條件</span><span class="sxs-lookup"><span data-stu-id="54ff7-110">Pre-Requisites</span></span>

* <span data-ttu-id="54ff7-111">您必須在選取 **.net 桌面工作負載** 的情況下安裝 Visual Studio 2019 16.3 或更新版本，才能完成此逐步解說。</span><span class="sxs-lookup"><span data-stu-id="54ff7-111">You need to have Visual Studio 2019 16.3 or later installed with the **.NET desktop workload** selected to complete this walkthrough.</span></span>
    
    <span data-ttu-id="54ff7-112">如需安裝最新版本 Visual Studio 的詳細資訊，請參閱 [安裝 Visual Studio](/visualstudio/install/install-visual-studio)。</span><span class="sxs-lookup"><span data-stu-id="54ff7-112">For more information about installing the latest version of Visual Studio, see [Install Visual Studio](/visualstudio/install/install-visual-studio).</span></span>

## <a name="create-the-application"></a><span data-ttu-id="54ff7-113">建立應用程式</span><span class="sxs-lookup"><span data-stu-id="54ff7-113">Create the Application</span></span>

1. <span data-ttu-id="54ff7-114">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="54ff7-114">Open Visual Studio</span></span>
2. <span data-ttu-id="54ff7-115">在開始視窗中，選擇 [建立新專案]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="54ff7-115">On the start window, choose **Create new project**.</span></span>
3. <span data-ttu-id="54ff7-116">搜尋 "WPF"，選擇 [ \*\*Wpf 應用程式 ( .Net Core]) \*\* 然後選擇 **[下一步]**。</span><span class="sxs-lookup"><span data-stu-id="54ff7-116">Search for "WPF," choose **WPF App (.NET Core)** and then choose **Next**.</span></span>
4. <span data-ttu-id="54ff7-117">在下一個畫面中，為專案命名，例如 **GetStartedWPF**，然後選擇 [建立] **。**</span><span class="sxs-lookup"><span data-stu-id="54ff7-117">At the next screen, give the project a name, for example, **GetStartedWPF**, and choose **Create.**</span></span>

## <a name="install-the-entity-framework-nuget-packages"></a><span data-ttu-id="54ff7-118">安裝 Entity Framework NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="54ff7-118">Install the Entity Framework NuGet packages</span></span>

1. <span data-ttu-id="54ff7-119">以滑鼠右鍵按一下方案，然後選擇 [**管理解決方案的 NuGet 套件**...]</span><span class="sxs-lookup"><span data-stu-id="54ff7-119">Right-click on the solution and choose **Manage NuGet Packages for Solution...**</span></span>

    ![Manage NuGet Packages](_static/wpf-tutorial-nuget.jpg)

1. <span data-ttu-id="54ff7-121">在搜尋方塊中鍵入 `entityframeworkcore.sqlite`。</span><span class="sxs-lookup"><span data-stu-id="54ff7-121">Type `entityframeworkcore.sqlite` in the search box.</span></span>
1. <span data-ttu-id="54ff7-122">選取 **Microsoft.entityframeworkcore Sqlite** 套件。</span><span class="sxs-lookup"><span data-stu-id="54ff7-122">Select the **Microsoft.EntityFrameworkCore.Sqlite** package.</span></span>
1. <span data-ttu-id="54ff7-123">檢查右窗格中的專案，然後按一下 [**安裝**]。</span><span class="sxs-lookup"><span data-stu-id="54ff7-123">Check the project in the right pane and click **Install**</span></span>

    ![Sqlite 封裝](_static/wpf-tutorial-sqlite.jpg)

1. <span data-ttu-id="54ff7-125">重複這些步驟，以搜尋 `entityframeworkcore.proxies` 並安裝**microsoft.entityframeworkcore。**</span><span class="sxs-lookup"><span data-stu-id="54ff7-125">Repeat the steps to search for `entityframeworkcore.proxies` and install **Microsoft.EntityFrameworkCore.Proxies**.</span></span>

> [!NOTE]
> <span data-ttu-id="54ff7-126">當您安裝 Sqlite 封裝時，它會自動拉出相關的 **microsoft.entityframeworkcore** 基底套件。</span><span class="sxs-lookup"><span data-stu-id="54ff7-126">When you installed the Sqlite package, it automatically pulled down the related **Microsoft.EntityFrameworkCore** base package.</span></span> <span data-ttu-id="54ff7-127">**Microsoft.entityframeworkcore**封裝提供「消極式載入」資料的支援。</span><span class="sxs-lookup"><span data-stu-id="54ff7-127">The **Microsoft.EntityFrameworkCore.Proxies** package provides support for "lazy-loading" data.</span></span> <span data-ttu-id="54ff7-128">這表示當您的實體具有子系實體時，只會在初始載入時提取父代。</span><span class="sxs-lookup"><span data-stu-id="54ff7-128">This means when you have entities with child entities, only the parents are fetched on the initial load.</span></span> <span data-ttu-id="54ff7-129">當嘗試存取子實體時，proxy 會偵測到，並且會視需要自動載入這些實體。</span><span class="sxs-lookup"><span data-stu-id="54ff7-129">The proxies detect when an attempt to access the child entities is made and automatically loads them on demand.</span></span> 

## <a name="define-a-model"></a><span data-ttu-id="54ff7-130">定義模型</span><span class="sxs-lookup"><span data-stu-id="54ff7-130">Define a Model</span></span>

<span data-ttu-id="54ff7-131">在這個逐步解說中，您將使用「code first」來執行模型。</span><span class="sxs-lookup"><span data-stu-id="54ff7-131">In this walkthrough you will implement a model using "code first."</span></span> <span data-ttu-id="54ff7-132">這表示 EF Core 將根據您定義的 c # 類別，建立資料庫資料表和架構。</span><span class="sxs-lookup"><span data-stu-id="54ff7-132">This means that EF Core will create the database tables and schema based on the C# classes you define.</span></span>

<span data-ttu-id="54ff7-133">新增類別。</span><span class="sxs-lookup"><span data-stu-id="54ff7-133">Add a new class.</span></span> <span data-ttu-id="54ff7-134">為其指定名稱 `Product.cs` ，並將其填入如下：</span><span class="sxs-lookup"><span data-stu-id="54ff7-134">Give it the name: `Product.cs` and populate it like this:</span></span>

**`Product.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Product.cs)]

<span data-ttu-id="54ff7-135">接下來，新增名為的類別 `Category.cs` ，並在其中填入下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="54ff7-135">Next, add a class named `Category.cs` and populate it with the following code:</span></span>

**`Category.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Category.cs)]

<span data-ttu-id="54ff7-136">**Product**類別的**Category**類別和**category**屬性上的**Products**屬性為導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="54ff7-136">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="54ff7-137">在 Entity Framework 中，導覽屬性會提供一種方式來導覽兩個實體類型之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="54ff7-137">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="54ff7-138">除了定義實體之外，您還必須定義衍生自 DbCoNtext 的類別，並公開 DbSet &lt; TEntity &gt; 屬性。</span><span class="sxs-lookup"><span data-stu-id="54ff7-138">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="54ff7-139">DbSet &lt; TEntity &gt; 屬性可讓內容知道您想要包含在模型中的類型。</span><span class="sxs-lookup"><span data-stu-id="54ff7-139">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="54ff7-140">DbCoNtext 衍生類型的實例會在運行時間管理實體物件，包括將資料庫的資料填入物件、變更追蹤，以及將資料保存到資料庫。</span><span class="sxs-lookup"><span data-stu-id="54ff7-140">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

<span data-ttu-id="54ff7-141">`ProductContext.cs`使用下列定義將新類別新增至專案：</span><span class="sxs-lookup"><span data-stu-id="54ff7-141">Add a new `ProductContext.cs` class to the project with the following definition:</span></span>

**`ProductContext.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/ProductContext.cs)]

* <span data-ttu-id="54ff7-142">`DbSet`通知 EF Core 應將哪些 c # 實體對應到資料庫。</span><span class="sxs-lookup"><span data-stu-id="54ff7-142">The `DbSet` informs EF Core what C# entities should be mapped to the database.</span></span>
* <span data-ttu-id="54ff7-143">有多種方式可以設定 EF Core `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="54ff7-143">There are a variety of ways to configure the EF Core `DbContext`.</span></span> <span data-ttu-id="54ff7-144">您可以在中閱讀相關資訊：設定 [DbCoNtext](xref:core/miscellaneous/configuring-dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="54ff7-144">You can read about them in: [Configuring a DbContext](xref:core/miscellaneous/configuring-dbcontext).</span></span>
* <span data-ttu-id="54ff7-145">此範例會使用覆 `OnConfiguring` 寫來指定 Sqlite 資料檔案。</span><span class="sxs-lookup"><span data-stu-id="54ff7-145">This example uses the `OnConfiguring` override to specify a Sqlite data file.</span></span>
* <span data-ttu-id="54ff7-146">此 `UseLazyLoadingProxies` 呼叫會告知 EF Core 要執行消極式載入，因此子實體會在從父系存取時自動載入。</span><span class="sxs-lookup"><span data-stu-id="54ff7-146">The `UseLazyLoadingProxies` call tells EF Core to implement lazy-loading, so child entities are automatically loaded when accessed from the parent.</span></span>

<span data-ttu-id="54ff7-147">按 **CTRL + SHIFT + B** 或流覽至 **組建 &gt; 組建方案** ，以編譯專案。</span><span class="sxs-lookup"><span data-stu-id="54ff7-147">Press **CTRL+SHIFT+B** or navigate to **Build &gt; Build Solution** to compile the project.</span></span>

> [!TIP]
> <span data-ttu-id="54ff7-148">瞭解不同的 was，讓您的資料庫和 EF Core 模型保持同步： [管理資料庫架構](xref:core/managing-schemas/index)。</span><span class="sxs-lookup"><span data-stu-id="54ff7-148">Learn about the different was to keep your database and EF Core models in sync: [Managing Database Schemas](xref:core/managing-schemas/index).</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="54ff7-149">消極式載入</span><span class="sxs-lookup"><span data-stu-id="54ff7-149">Lazy Loading</span></span>

<span data-ttu-id="54ff7-150">**Product**類別的**Category**類別和**category**屬性上的**Products**屬性為導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="54ff7-150">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="54ff7-151">在 Entity Framework Core 中，導覽屬性會提供一種方式來導覽兩個實體類型之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="54ff7-151">In Entity Framework Core, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="54ff7-152">EF Core 讓您可以選擇在第一次存取導覽屬性時，自動從資料庫載入相關實體。</span><span class="sxs-lookup"><span data-stu-id="54ff7-152">EF Core gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="54ff7-153">使用這種類型的載入 (稱為消極式載入) ，請注意，當您第一次存取每個導覽屬性時，如果內容中還沒有內容，則會對資料庫執行個別的查詢。</span><span class="sxs-lookup"><span data-stu-id="54ff7-153">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="54ff7-154">使用「單純的 c # 物件」 (POCO) 實體類型時，EF Core 會在執行時間建立衍生的 proxy 類型實例，然後覆寫類別中的虛擬屬性以新增載入攔截，以達成消極式載入。</span><span class="sxs-lookup"><span data-stu-id="54ff7-154">When using "Plain Old C# Object" (POCO) entity types, EF Core achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="54ff7-155">若要取得相關物件的消極式載入，您必須在 Visual Basic) 中將導覽屬性 getter 宣告為 **public** 和 **virtual** (可覆 **寫** ，而且您的類別不得在 Visual Basic) 中 **密封** (**NotOverridable** 。</span><span class="sxs-lookup"><span data-stu-id="54ff7-155">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and your class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="54ff7-156">使用 Database First 時，導覽屬性會自動設為虛擬，以啟用消極式載入。</span><span class="sxs-lookup"><span data-stu-id="54ff7-156">When using Database First, navigation properties are automatically made virtual to enable lazy loading.</span></span> 

## <a name="bind-object-to-controls"></a><span data-ttu-id="54ff7-157">將物件系結至控制項</span><span class="sxs-lookup"><span data-stu-id="54ff7-157">Bind Object to Controls</span></span>

<span data-ttu-id="54ff7-158">將模型中定義的類別加入為這個 WPF 應用程式的資料來源。</span><span class="sxs-lookup"><span data-stu-id="54ff7-158">Add the classes that are defined in the model as data sources for this WPF application.</span></span>

1. <span data-ttu-id="54ff7-159">按兩下方案總管中的 [ **MainWindow** ] 以開啟主要表單</span><span class="sxs-lookup"><span data-stu-id="54ff7-159">Double-click **MainWindow.xaml** in Solution Explorer to open the main form</span></span>
1. <span data-ttu-id="54ff7-160">選擇 [ **xaml** ] 索引標籤以編輯 xaml。</span><span class="sxs-lookup"><span data-stu-id="54ff7-160">Choose the **XAML** tab to edit the XAML.</span></span>
1. <span data-ttu-id="54ff7-161">緊接在開頭 `Window` 標記之後，新增下列來源以連接到 EF Core 實體。</span><span class="sxs-lookup"><span data-stu-id="54ff7-161">Immediately after the opening `Window` tag, add the following sources to connect to the EF Core entities.</span></span>

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=1-13&highlight=9-13)]

1. <span data-ttu-id="54ff7-162">這會設定「父」類別的來源，以及「詳細資料」產品的第二個來源。</span><span class="sxs-lookup"><span data-stu-id="54ff7-162">This sets up source for the "parent" categories, and second source for the "detail" products.</span></span>
1. <span data-ttu-id="54ff7-163">接下來，在結束記號之後，將下列標記新增至您的 XAML `Window.Resources` 。</span><span class="sxs-lookup"><span data-stu-id="54ff7-163">Next, add the following markup to your XAML after the closing `Window.Resources` tag.</span></span>

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=15-26)]

1. <span data-ttu-id="54ff7-164">請注意， `CategoryId` 會設定為， `ReadOnly` 因為它是由資料庫所指派，且無法變更。</span><span class="sxs-lookup"><span data-stu-id="54ff7-164">Note that the `CategoryId` is set to `ReadOnly` because it is assigned by the database and cannot be changed.</span></span>

## <a name="adding-a-details-grid"></a><span data-ttu-id="54ff7-165">加入詳細資料方格</span><span class="sxs-lookup"><span data-stu-id="54ff7-165">Adding a Details Grid</span></span>

<span data-ttu-id="54ff7-166">現在方格已存在，可顯示類別目錄，您可以加入詳細資料方格來顯示產品。</span><span class="sxs-lookup"><span data-stu-id="54ff7-166">Now that the grid exists to display categories, the details grid can be added to show products.</span></span>

**`MainWindow.xaml`**

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=27-40)]

<span data-ttu-id="54ff7-167">最後， `Save` 在 click 事件中加入按鈕和網路 `Button_Click` 。</span><span class="sxs-lookup"><span data-stu-id="54ff7-167">Finally, add a `Save` button and wire in the click event to `Button_Click`.</span></span>

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=41-42)]

<span data-ttu-id="54ff7-168">您的設計檢視看起來應該像這樣：</span><span class="sxs-lookup"><span data-stu-id="54ff7-168">Your design view should look like this:</span></span>

![WPF 設計工具的螢幕擷取畫面](_static/wpf-tutorial-designer.jpg)

## <a name="add-code-that-handles-data-interaction"></a><span data-ttu-id="54ff7-170">新增處理資料互動的程式碼</span><span class="sxs-lookup"><span data-stu-id="54ff7-170">Add Code that Handles Data Interaction</span></span>

<span data-ttu-id="54ff7-171">現在可以將一些事件處理常式加入主視窗中。</span><span class="sxs-lookup"><span data-stu-id="54ff7-171">It's time to add some event handlers to the main window.</span></span>

1. <span data-ttu-id="54ff7-172">在 [XAML] 視窗中，按一下 [ \*\* &lt; 視窗 &gt; \*\* ] 元素，選取主視窗。</span><span class="sxs-lookup"><span data-stu-id="54ff7-172">In the XAML window, click on the **&lt;Window&gt;** element, to select the main window.</span></span>
1. <span data-ttu-id="54ff7-173">在 [ **屬性** ] 視窗中，選擇右上方的 [ **事件** ]，然後按兩下 **載入** 的標籤右邊的文字方塊。</span><span class="sxs-lookup"><span data-stu-id="54ff7-173">In the **Properties** window choose **Events** at the top right, then double-click the text box to right of the **Loaded** label.</span></span>

    ![主視窗屬性](_static/wpf-tutorial-loaded.jpg)

<span data-ttu-id="54ff7-175">這會帶您前往表單的程式碼後接，現在我們將編輯程式碼，以使用 `ProductContext` 來執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="54ff7-175">This brings you to the code behind for the form, we'll now edit the code to use the `ProductContext` to perform data access.</span></span> <span data-ttu-id="54ff7-176">更新程式碼，如下所示。</span><span class="sxs-lookup"><span data-stu-id="54ff7-176">Update the code as shown below.</span></span>

<span data-ttu-id="54ff7-177">程式碼會宣告的長時間執行實例 `ProductContext` 。</span><span class="sxs-lookup"><span data-stu-id="54ff7-177">The code declares a long-running instance of `ProductContext`.</span></span> <span data-ttu-id="54ff7-178">`ProductContext`物件可用來查詢資料庫，並將資料儲存至資料庫。</span><span class="sxs-lookup"><span data-stu-id="54ff7-178">The `ProductContext` object is used to query and save data to the database.</span></span> <span data-ttu-id="54ff7-179">`Dispose()` `ProductContext` 然後，會從覆寫的方法呼叫實例上的方法 `OnClosing` 。</span><span class="sxs-lookup"><span data-stu-id="54ff7-179">The `Dispose()` method on the `ProductContext` instance is then called from the overridden `OnClosing` method.</span></span><span data-ttu-id="54ff7-180">程式碼註解會說明每個步驟的作用。</span><span class="sxs-lookup"><span data-stu-id="54ff7-180"> The code comments explain what each step does.</span></span>

**`MainWindow.xaml.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml.cs)]

> [!NOTE]
> <span data-ttu-id="54ff7-181">在第一次執行時，程式碼會使用的呼叫 `EnsureCreated()` 來建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="54ff7-181">The code uses a call to `EnsureCreated()` to build the database on the first run.</span></span> <span data-ttu-id="54ff7-182">這可供示範之用，但在生產環境應用程式中，您應該查看 [遷移](xref:core/managing-schemas/migrations/index) 來管理您的架構。</span><span class="sxs-lookup"><span data-stu-id="54ff7-182">This is acceptable for demos, but in production apps you should look at [migrations](xref:core/managing-schemas/migrations/index) to manage your schema.</span></span> <span data-ttu-id="54ff7-183">程式碼也會以同步方式執行，因為它使用本機 SQLite 資料庫。</span><span class="sxs-lookup"><span data-stu-id="54ff7-183">The code also executes synchronously because it uses a local SQLite database.</span></span> <span data-ttu-id="54ff7-184">針對通常牽涉到遠端伺服器的生產案例，請考慮使用和方法的非同步 `Load` 版本 `SaveChanges` 。</span><span class="sxs-lookup"><span data-stu-id="54ff7-184">For production scenarios that typically involve a remote server, consider using the asynchronous versions of the `Load` and `SaveChanges` methods.</span></span>

## <a name="test-the-wpf-application"></a><span data-ttu-id="54ff7-185">測試 WPF 應用程式</span><span class="sxs-lookup"><span data-stu-id="54ff7-185">Test the WPF Application</span></span>

<span data-ttu-id="54ff7-186">按下 **F5** 或選擇 [ **Debug &gt; 開始調試**程式]，以編譯並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="54ff7-186">Compile and run the application by pressing **F5** or choosing **Debug &gt; Start Debugging**.</span></span> <span data-ttu-id="54ff7-187">應該使用名為的檔案自動建立資料庫 `products.db` 。</span><span class="sxs-lookup"><span data-stu-id="54ff7-187">The database should be automatically created with a file named `products.db`.</span></span> <span data-ttu-id="54ff7-188">輸入類別名稱，然後按 enter 鍵，然後將產品新增至下方方格。</span><span class="sxs-lookup"><span data-stu-id="54ff7-188">Enter a category name and hit enter, then add products to the lower grid.</span></span> <span data-ttu-id="54ff7-189">按一下 [儲存]，並以資料庫提供的識別碼監看方格重新整理。</span><span class="sxs-lookup"><span data-stu-id="54ff7-189">Click save and watch the grid refresh with the database provided ids.</span></span> <span data-ttu-id="54ff7-190">將資料列反白顯示並按 **Delete** 以移除資料列。</span><span class="sxs-lookup"><span data-stu-id="54ff7-190">Highlight a row and hit **Delete** to remove the row.</span></span> <span data-ttu-id="54ff7-191">當您按一下 [ **儲存**] 時，就會刪除實體。</span><span class="sxs-lookup"><span data-stu-id="54ff7-191">The entity will be deleted when you click **Save**.</span></span>

![正在執行應用程式](_static/wpf-tutorial-app.jpg)

## <a name="property-change-notification"></a><span data-ttu-id="54ff7-193">屬性變更通知</span><span class="sxs-lookup"><span data-stu-id="54ff7-193">Property Change Notification</span></span>

<span data-ttu-id="54ff7-194">此範例依賴四個步驟來同步處理實體與 UI。</span><span class="sxs-lookup"><span data-stu-id="54ff7-194">This example relies on four steps to synchronize the entities with the UI.</span></span>

1. <span data-ttu-id="54ff7-195">初始呼叫會 `_context.Categories.Load()` 載入類別目錄資料。</span><span class="sxs-lookup"><span data-stu-id="54ff7-195">The initial call `_context.Categories.Load()` loads the categories data.</span></span>
1. <span data-ttu-id="54ff7-196">延遲載入 proxy 會載入相依產品資料。</span><span class="sxs-lookup"><span data-stu-id="54ff7-196">The lazy-loading proxies load the dependent products data.</span></span>
1. <span data-ttu-id="54ff7-197">EF Core 的內建變更追蹤會在呼叫時，對實體進行必要的修改，包括插入與刪除 `_context.SaveChanges()` 。</span><span class="sxs-lookup"><span data-stu-id="54ff7-197">EF Core's built-in change tracking makes the necessary modifications to entities, including insertions and deletions, when `_context.SaveChanges()` is called.</span></span>
1. <span data-ttu-id="54ff7-198">`DataGridView.Items.Refresh()`使用新產生的識別碼來強制重載的呼叫。</span><span class="sxs-lookup"><span data-stu-id="54ff7-198">The calls to `DataGridView.Items.Refresh()` force a reload with the newly generated ids.</span></span>

<span data-ttu-id="54ff7-199">這適用于我們的使用者入門範例，但您可能需要額外的程式碼以供其他案例使用。</span><span class="sxs-lookup"><span data-stu-id="54ff7-199">This works for our getting started sample, but you may require additional code for other scenarios.</span></span> <span data-ttu-id="54ff7-200">WPF 控制項會藉由讀取實體上的欄位和屬性來呈現 UI。</span><span class="sxs-lookup"><span data-stu-id="54ff7-200">WPF controls render the UI by reading the fields and properties on your entities.</span></span> <span data-ttu-id="54ff7-201">當您在使用者介面中編輯 (UI) 的值時，該值會傳遞至您的實體。</span><span class="sxs-lookup"><span data-stu-id="54ff7-201">When you edit a value in the user interface (UI), that value is passed to your entity.</span></span> <span data-ttu-id="54ff7-202">當您直接在實體上變更屬性的值時（例如從資料庫載入它），WPF 將不會立即反映 UI 中的變更。</span><span class="sxs-lookup"><span data-stu-id="54ff7-202">When you change the value of a property directly on your entity, such as loading it from the database, WPF will not immediately reflect the changes in the UI.</span></span> <span data-ttu-id="54ff7-203">轉譯引擎必須收到變更的通知。</span><span class="sxs-lookup"><span data-stu-id="54ff7-203">The rendering engine must be notified of the changes.</span></span> <span data-ttu-id="54ff7-204">專案是藉由手動呼叫來執行此操作 `Refresh()` 。</span><span class="sxs-lookup"><span data-stu-id="54ff7-204">The project did this by manually calling `Refresh()`.</span></span> <span data-ttu-id="54ff7-205">自動執行此通知的簡單方法是執行 [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) 介面。</span><span class="sxs-lookup"><span data-stu-id="54ff7-205">An easy way to automate this notification is by implementing the [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) interface.</span></span> <span data-ttu-id="54ff7-206">WPF 元件會自動偵測介面並註冊變更事件。</span><span class="sxs-lookup"><span data-stu-id="54ff7-206">WPF components will automatically detect the interface and register for change events.</span></span> <span data-ttu-id="54ff7-207">實體負責引發這些事件。</span><span class="sxs-lookup"><span data-stu-id="54ff7-207">The entity is responsible for raising these events.</span></span>

> [!TIP]
> <span data-ttu-id="54ff7-208">若要深入瞭解如何處理變更，請參閱： [如何執行屬性變更通知](/dotnet/framework/wpf/data/how-to-implement-property-change-notification)。</span><span class="sxs-lookup"><span data-stu-id="54ff7-208">To learn more about how to handle changes, read: [How to implement property change notification](/dotnet/framework/wpf/data/how-to-implement-property-change-notification).</span></span>

## <a name="next-steps"></a><span data-ttu-id="54ff7-209">後續步驟</span><span class="sxs-lookup"><span data-stu-id="54ff7-209">Next Steps</span></span>

<span data-ttu-id="54ff7-210">深入瞭解如何設定 [DbCoNtext](xref:core/miscellaneous/configuring-dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="54ff7-210">Learn more about [Configuring a DbContext](xref:core/miscellaneous/configuring-dbcontext).</span></span>
