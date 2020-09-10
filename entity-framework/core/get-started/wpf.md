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
# <a name="getting-started-with-wpf"></a>WPF 使用者入門

本逐步解說將示範如何在「主要詳細資料」表單中，將 POCO 類型系結至 WPF 控制項。 應用程式會使用 Entity Framework Api，以資料庫中的資料填入物件、追蹤變更，以及將資料保存到資料庫。

此模型會定義兩種參與一對多關聯性的類型： **類別** (主體 \\ 主要) 和 **產品** (相依 \\ 詳細資料) 。 WPF 資料系結架構會啟用相關物件之間的導覽：選取主視圖中的資料列會導致詳細資料檢視以對應的子資料更新。

本逐步解說中的螢幕擷取畫面和程式代碼清單取自 Visual Studio 2019 16.6.5。

> [!TIP]
> 您可以 [在 GitHub 上](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF)查看這篇文章的範例。

## <a name="pre-requisites"></a>必要條件

* 您必須在選取 **.net 桌面工作負載** 的情況下安裝 Visual Studio 2019 16.3 或更新版本，才能完成此逐步解說。
    
    如需安裝最新版本 Visual Studio 的詳細資訊，請參閱 [安裝 Visual Studio](/visualstudio/install/install-visual-studio)。

## <a name="create-the-application"></a>建立應用程式

1. 開啟 Visual Studio
2. 在開始視窗中，選擇 [建立新專案]****。
3. 搜尋 "WPF"，選擇 [ **Wpf 應用程式 ( .Net Core]) ** 然後選擇 **[下一步]**。
4. 在下一個畫面中，為專案命名，例如 **GetStartedWPF**，然後選擇 [建立] **。**

## <a name="install-the-entity-framework-nuget-packages"></a>安裝 Entity Framework NuGet 套件

1. 以滑鼠右鍵按一下方案，然後選擇 [**管理解決方案的 NuGet 套件**...]

    ![Manage NuGet Packages](_static/wpf-tutorial-nuget.jpg)

1. 在搜尋方塊中鍵入 `entityframeworkcore.sqlite`。
1. 選取 **Microsoft.entityframeworkcore Sqlite** 套件。
1. 檢查右窗格中的專案，然後按一下 [**安裝**]。

    ![Sqlite 封裝](_static/wpf-tutorial-sqlite.jpg)

1. 重複這些步驟，以搜尋 `entityframeworkcore.proxies` 並安裝**microsoft.entityframeworkcore。**

> [!NOTE]
> 當您安裝 Sqlite 封裝時，它會自動拉出相關的 **microsoft.entityframeworkcore** 基底套件。 **Microsoft.entityframeworkcore**封裝提供「消極式載入」資料的支援。 這表示當您的實體具有子系實體時，只會在初始載入時提取父代。 當嘗試存取子實體時，proxy 會偵測到，並且會視需要自動載入這些實體。 

## <a name="define-a-model"></a>定義模型

在這個逐步解說中，您將使用「code first」來執行模型。 這表示 EF Core 將根據您定義的 c # 類別，建立資料庫資料表和架構。

新增類別。 為其指定名稱 `Product.cs` ，並將其填入如下：

**`Product.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Product.cs)]

接下來，新增名為的類別 `Category.cs` ，並在其中填入下列程式碼：

**`Category.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Category.cs)]

**Product**類別的**Category**類別和**category**屬性上的**Products**屬性為導覽屬性。 在 Entity Framework 中，導覽屬性會提供一種方式來導覽兩個實體類型之間的關聯性。

除了定義實體之外，您還必須定義衍生自 DbCoNtext 的類別，並公開 DbSet &lt; TEntity &gt; 屬性。 DbSet &lt; TEntity &gt; 屬性可讓內容知道您想要包含在模型中的類型。

DbCoNtext 衍生類型的實例會在運行時間管理實體物件，包括將資料庫的資料填入物件、變更追蹤，以及將資料保存到資料庫。

`ProductContext.cs`使用下列定義將新類別新增至專案：

**`ProductContext.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/ProductContext.cs)]

* `DbSet`通知 EF Core 應將哪些 c # 實體對應到資料庫。
* 有多種方式可以設定 EF Core `DbContext` 。 您可以在中閱讀相關資訊：設定 [DbCoNtext](xref:core/miscellaneous/configuring-dbcontext)。
* 此範例會使用覆 `OnConfiguring` 寫來指定 Sqlite 資料檔案。
* 此 `UseLazyLoadingProxies` 呼叫會告知 EF Core 要執行消極式載入，因此子實體會在從父系存取時自動載入。

按 **CTRL + SHIFT + B** 或流覽至 **組建 &gt; 組建方案** ，以編譯專案。

> [!TIP]
> 瞭解不同的 was，讓您的資料庫和 EF Core 模型保持同步： [管理資料庫架構](xref:core/managing-schemas/index)。

## <a name="lazy-loading"></a>消極式載入

**Product**類別的**Category**類別和**category**屬性上的**Products**屬性為導覽屬性。 在 Entity Framework Core 中，導覽屬性會提供一種方式來導覽兩個實體類型之間的關聯性。

EF Core 讓您可以選擇在第一次存取導覽屬性時，自動從資料庫載入相關實體。 使用這種類型的載入 (稱為消極式載入) ，請注意，當您第一次存取每個導覽屬性時，如果內容中還沒有內容，則會對資料庫執行個別的查詢。

使用「單純的 c # 物件」 (POCO) 實體類型時，EF Core 會在執行時間建立衍生的 proxy 類型實例，然後覆寫類別中的虛擬屬性以新增載入攔截，以達成消極式載入。 若要取得相關物件的消極式載入，您必須在 Visual Basic) 中將導覽屬性 getter 宣告為 **public** 和 **virtual** (可覆 **寫** ，而且您的類別不得在 Visual Basic) 中 **密封** (**NotOverridable** 。 使用 Database First 時，導覽屬性會自動設為虛擬，以啟用消極式載入。 

## <a name="bind-object-to-controls"></a>將物件系結至控制項

將模型中定義的類別加入為這個 WPF 應用程式的資料來源。

1. 按兩下方案總管中的 [ **MainWindow** ] 以開啟主要表單
1. 選擇 [ **xaml** ] 索引標籤以編輯 xaml。
1. 緊接在開頭 `Window` 標記之後，新增下列來源以連接到 EF Core 實體。

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=1-13&highlight=9-13)]

1. 這會設定「父」類別的來源，以及「詳細資料」產品的第二個來源。
1. 接下來，在結束記號之後，將下列標記新增至您的 XAML `Window.Resources` 。

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=15-26)]

1. 請注意， `CategoryId` 會設定為， `ReadOnly` 因為它是由資料庫所指派，且無法變更。

## <a name="adding-a-details-grid"></a>加入詳細資料方格

現在方格已存在，可顯示類別目錄，您可以加入詳細資料方格來顯示產品。

**`MainWindow.xaml`**

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=27-40)]

最後， `Save` 在 click 事件中加入按鈕和網路 `Button_Click` 。

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=41-42)]

您的設計檢視看起來應該像這樣：

![WPF 設計工具的螢幕擷取畫面](_static/wpf-tutorial-designer.jpg)

## <a name="add-code-that-handles-data-interaction"></a>新增處理資料互動的程式碼

現在可以將一些事件處理常式加入主視窗中。

1. 在 [XAML] 視窗中，按一下 [ ** &lt; 視窗 &gt; ** ] 元素，選取主視窗。
1. 在 [ **屬性** ] 視窗中，選擇右上方的 [ **事件** ]，然後按兩下 **載入** 的標籤右邊的文字方塊。

    ![主視窗屬性](_static/wpf-tutorial-loaded.jpg)

這會帶您前往表單的程式碼後接，現在我們將編輯程式碼，以使用 `ProductContext` 來執行資料存取。 更新程式碼，如下所示。

程式碼會宣告的長時間執行實例 `ProductContext` 。 `ProductContext`物件可用來查詢資料庫，並將資料儲存至資料庫。 `Dispose()` `ProductContext` 然後，會從覆寫的方法呼叫實例上的方法 `OnClosing` 。程式碼註解會說明每個步驟的作用。

**`MainWindow.xaml.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml.cs)]

> [!NOTE]
> 在第一次執行時，程式碼會使用的呼叫 `EnsureCreated()` 來建立資料庫。 這可供示範之用，但在生產環境應用程式中，您應該查看 [遷移](xref:core/managing-schemas/migrations/index) 來管理您的架構。 程式碼也會以同步方式執行，因為它使用本機 SQLite 資料庫。 針對通常牽涉到遠端伺服器的生產案例，請考慮使用和方法的非同步 `Load` 版本 `SaveChanges` 。

## <a name="test-the-wpf-application"></a>測試 WPF 應用程式

按下 **F5** 或選擇 [ **Debug &gt; 開始調試**程式]，以編譯並執行應用程式。 應該使用名為的檔案自動建立資料庫 `products.db` 。 輸入類別名稱，然後按 enter 鍵，然後將產品新增至下方方格。 按一下 [儲存]，並以資料庫提供的識別碼監看方格重新整理。 將資料列反白顯示並按 **Delete** 以移除資料列。 當您按一下 [ **儲存**] 時，就會刪除實體。

![正在執行應用程式](_static/wpf-tutorial-app.jpg)

## <a name="property-change-notification"></a>屬性變更通知

此範例依賴四個步驟來同步處理實體與 UI。

1. 初始呼叫會 `_context.Categories.Load()` 載入類別目錄資料。
1. 延遲載入 proxy 會載入相依產品資料。
1. EF Core 的內建變更追蹤會在呼叫時，對實體進行必要的修改，包括插入與刪除 `_context.SaveChanges()` 。
1. `DataGridView.Items.Refresh()`使用新產生的識別碼來強制重載的呼叫。

這適用于我們的使用者入門範例，但您可能需要額外的程式碼以供其他案例使用。 WPF 控制項會藉由讀取實體上的欄位和屬性來呈現 UI。 當您在使用者介面中編輯 (UI) 的值時，該值會傳遞至您的實體。 當您直接在實體上變更屬性的值時（例如從資料庫載入它），WPF 將不會立即反映 UI 中的變更。 轉譯引擎必須收到變更的通知。 專案是藉由手動呼叫來執行此操作 `Refresh()` 。 自動執行此通知的簡單方法是執行 [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) 介面。 WPF 元件會自動偵測介面並註冊變更事件。 實體負責引發這些事件。

> [!TIP]
> 若要深入瞭解如何處理變更，請參閱： [如何執行屬性變更通知](/dotnet/framework/wpf/data/how-to-implement-property-change-notification)。

## <a name="next-steps"></a>後續步驟

深入瞭解如何設定 [DbCoNtext](xref:core/miscellaneous/configuring-dbcontext)。
