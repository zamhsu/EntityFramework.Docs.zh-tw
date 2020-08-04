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
# <a name="getting-started-with-wpf"></a>WPF 使用者入門

此逐步解說會示範如何將 POCO 類型系結至「主要-詳細資料」表單中的 WPF 控制項。 應用程式會使用 Entity Framework Api，以資料庫中的資料來填入物件、追蹤變更，並將資料保存至資料庫。

模型會定義兩個參與一對多關聯性的類型：**類別目錄**（主體 \\ 主要）和**產品**（相依 \\ 詳細資料）。 WPF 資料系結架構可讓您在相關物件之間導覽：選取主視圖中的資料列會使詳細資料檢視以對應的子資料進行更新。

本逐步解說中的螢幕擷取畫面和程式代碼清單取自 Visual Studio 2019 16.6.5。

> [!TIP]
> 您可以[在 GitHub 上](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF)查看這篇文章的範例。

## <a name="pre-requisites"></a>必要條件

* 您必須在已選取 **.net 桌面工作負載**的情況下安裝 Visual Studio 2019 16.3 或更新版本，才能完成此逐步解說。
    
    如需安裝最新版本 Visual Studio 的詳細資訊，請參閱[Install Visual Studio](/visualstudio/install/install-visual-studio)。

## <a name="create-the-application"></a>建立應用程式

1. 開啟 Visual Studio
2. 在開始視窗中，選擇 [建立新專案]****。
3. 搜尋 "WPF"，選擇 [ **Wpf 應用程式（.Net Core）** ]，然後選擇 **[下一步]**。
4. 在下一個畫面中，指定專案的名稱，例如**GetStartedWPF**，然後選擇 [**建立]。**

## <a name="install-the-entity-framework-nuget-packages"></a>安裝 Entity Framework NuGet 套件

1. 以滑鼠右鍵按一下方案，然後選擇 [**管理方案的 NuGet 套件**...]

    ![Manage NuGet Packages](_static/wpf-tutorial-nuget.jpg)

1. 在搜尋方塊中鍵入 `entityframeworkcore.sqlite`。
1. 選取 [ **microsoft.entityframeworkcore** ] 套件。
1. 檢查右窗格中的專案，然後按一下 [**安裝**]。

    ![Sqlite 封裝](_static/wpf-tutorial-sqlite.jpg)

1. 重複這些步驟來搜尋 `entityframeworkcore.proxies` 並安裝**microsoft.entityframeworkcore**。

> [!NOTE]
> 當您安裝 Sqlite 套件時，它會自動提取相關的**microsoft.entityframeworkcore**基底套件。 **Microsoft.entityframeworkcore**封裝可支援「延遲載入」資料。 這表示當您有具有子實體的實體時，只有父代會在初始載入時提取。 Proxy 會在嘗試存取子實體時偵測，並視需要自動載入它們。 

## <a name="define-a-model"></a>定義模型

在此逐步解說中，您將使用「code first」來執行模型。 這表示 EF Core 會根據您定義的 c # 類別來建立資料庫資料表和架構。

加入新的類別。 為它命名： `Product.cs` ，並將它填入如下：

**`Product.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Product.cs)]

接下來，新增名為的類別 `Category.cs` ，並在其中填入下列程式碼：

**`Category.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Category.cs)]

**Product**類別上**Category**類別和**category**屬性的**Products**屬性是導覽屬性。 在 Entity Framework 中，導覽屬性會提供一種方式來導覽兩個實體類型之間的關聯性。

除了定義實體以外，您還需要定義衍生自 DbCoNtext 的類別，並公開 DbSet &lt; TEntity &gt; 屬性。 DbSet &lt; TEntity &gt; 屬性可讓內容知道您想要包含在模型中的類型。

DbCoNtext 衍生類型的實例會在運行時間管理實體物件，其中包括以資料庫的資料填入物件、變更追蹤，以及將資料保存至資料庫。

使用下列定義，將新的 `ProductContext.cs` 類別新增至專案：

**`ProductContext.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/ProductContext.cs)]

* `DbSet`會通知 EF Core 哪些 c # 實體應該對應到資料庫。
* 設定 EF Core 的方式有很多種 `DbContext` 。 您可以在：設定[DbCoNtext](/ef/core/miscellaneous/configuring-dbcontext)中閱讀相關資訊。
* 這個範例會使用覆 `OnConfiguring` 寫來指定 Sqlite 資料檔案。
* `UseLazyLoadingProxies`呼叫會告訴 EF Core 執行消極式載入，因此從父系存取時，會自動載入子實體。

按**CTRL + SHIFT + B** ，或流覽至**組建 &gt; 組建方案**來編譯專案。

> [!TIP]
> 瞭解不同的 was，讓您的資料庫和 EF Core 模型保持同步：[管理資料庫架構](/ef/core/managing-schemas)。

## <a name="lazy-loading"></a>消極式載入

**Product**類別上**Category**類別和**category**屬性的**Products**屬性是導覽屬性。 在 Entity Framework Core 中，導覽屬性會提供一種方式來導覽兩個實體類型之間的關聯性。

EF Core 提供您第一次存取導覽屬性時，自動從資料庫載入相關實體的選項。 使用這種類型的載入（稱為消極式載入）時，請注意，當您第一次存取每個導覽屬性時，如果內容尚未存在於環境中，則會針對資料庫執行個別的查詢。

使用「單純舊的 c # 物件」（POCO）實體類型時，EF Core 會在執行時間建立衍生 proxy 類型的實例，然後覆寫類別中的虛擬屬性來新增載入攔截，以達到消極式載入。 若要取得相關物件的消極式載入，您必須將導覽屬性 getter 宣告為**public**和**virtual** （可在 Visual Basic 中覆**寫**），而且您的類別不得為**密封**（在 Visual Basic 中為**NotOverridable** ）。 使用 Database First 時，會自動將導覽屬性設為虛擬，以啟用消極式載入。 

## <a name="bind-object-to-controls"></a>將物件系結至控制項

加入模型中所定義的類別，做為此 WPF 應用程式的資料來源。

1. 按兩下方案總管中的 [ **mainwindow.xaml** ] 以開啟主要表單
1. 選擇 [ **xaml** ] 索引標籤來編輯 xaml。
1. 緊接在開頭 `Window` 標記之後，新增下列來源以連接到 EF Core 實體。

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=1-13&highlight=9-13)]

1. 這會設定「父系」分類的來源，以及「詳細資料」產品的第二個來源。
1. 接下來，在結束記號之後，將下列標記新增至您的 XAML `Window.Resources` 。

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=15-26)]

1. 請注意， `CategoryId` 會設定為， `ReadOnly` 因為它是由資料庫所指派，而且無法變更。

## <a name="adding-a-details-grid"></a>加入詳細資料方格

現在方格已經存在，可顯示類別目錄，您可以加入 [詳細資料] 方格來顯示產品。

**`MainWindow.xaml`**

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=27-40)]

最後，將 `Save` click 事件中的按鈕和連線加入至 `Button_Click` 。

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=41-42)]

您的設計檢視看起來應該像這樣：

![WPF 設計工具的螢幕擷取畫面](_static/wpf-tutorial-designer.jpg)

## <a name="add-code-that-handles-data-interaction"></a>加入處理資料互動的程式碼

現在可以在主視窗中加入一些事件處理常式。

1. 在 [XAML] 視窗中，按一下** &lt; 視窗 &gt; **元素以選取主視窗。
1. 在 [**屬性**] 視窗中，選擇右上方的 [**事件**]，然後按兩下 [已**載入**] 標籤右邊的文字方塊。

    ![主視窗屬性](_static/wpf-tutorial-loaded.jpg)

這會帶您前往表單的程式碼後置，我們現在會編輯程式碼，以使用 `ProductContext` 執行資料存取。 更新程式碼，如下所示。

此程式碼會宣告的長時間執行實例 `ProductContext` 。 `ProductContext`物件可用來查詢和儲存資料至資料庫。 `Dispose()` `ProductContext` 然後會從覆寫的方法呼叫實例上的方法 `OnClosing` 。程式碼註解會說明每個步驟的作用。

**`MainWindow.xaml.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml.cs)]

> [!NOTE]
> 程式碼會使用的呼叫， `EnsureCreated()` 在第一次執行時建立資料庫。 這是可接受的示範，但在生產環境應用程式中，您應該查看[遷移](/ef/core/managing-schemas/migrations/)以管理您的架構。 程式碼也會以同步方式執行，因為它使用本機 SQLite 資料庫。 針對通常牽涉到遠端伺服器的生產案例，請考慮使用和方法的非同步 `Load` 版本 `SaveChanges` 。

## <a name="test-the-wpf-application"></a>測試 WPF 應用程式

按**F5**或選擇 [Debug] [ ** &gt; 開始調試**程式]，以編譯並執行應用程式。 應該使用名為的檔案自動建立資料庫 `products.db` 。 輸入類別名稱，然後按 enter 鍵，然後將產品新增至下方方格。 按一下 [儲存]，並以資料庫提供的識別碼監看格線重新整理。 反白顯示資料列，並按**Delete**鍵來移除資料列。 當您按一下 [**儲存**] 時，將會刪除實體。

![正在執行應用程式](_static/wpf-tutorial-app.jpg)

## <a name="property-change-notification"></a>屬性變更通知

這個範例依賴四個步驟來同步處理實體與 UI。 

1. 初始呼叫會 `_context.Categories.Load()` 載入類別目錄資料。
1. 消極式載入 proxy 會載入相依產品資料。
1. EF Core 的內建變更追蹤會在呼叫時，對實體進行必要的修改，包括插入和刪除 `_context.SaveChanges()` 。
1. 的呼叫會以 `DataGridView.Items.Refresh()` 新產生的識別碼來強制重載。

這適用于我們的快速入門範例，但您可能需要額外的程式碼才能進行其他案例。 WPF 控制項會藉由讀取實體上的欄位和屬性來呈現 UI。 當您在使用者介面（UI）中編輯值時，該值會傳遞至您的實體。 當您直接變更實體的屬性值（例如從資料庫載入）時，WPF 不會立即反映 UI 中的變更。 呈現引擎必須收到變更的通知。 專案是藉由手動呼叫來執行這項操作 `Refresh()` 。 自動化此通知的簡單方式是藉由執行[INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged)介面。 WPF 元件會自動偵測介面，並註冊變更事件。 實體會負責引發這些事件。 

> [!TIP]
> 若要深入瞭解如何處理變更，請參閱：[如何執行屬性變更通知](/dotnet/framework/wpf/data/how-to-implement-property-change-notification)。

## <a name="next-steps"></a>後續步驟

深入瞭解如何設定[DbCoNtext](/ef/core/miscellaneous/configuring-dbcontext)。