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
> **這個文件只有在 .NET 框架上的 WPF 上有效**
>
> 本文件介紹 .NET 框架上 WPF 的數據綁定。 對於新的 .NET 核心項目,我們建議您使用[EF Core](/ef/core)而不是實體框架 6。 EF Core 中的數據綁定文檔在["問題#778"](https://github.com/dotnet/EntityFramework.Docs/issues/778)中跟蹤。

# <a name="databinding-with-wpf"></a>使用 WPF 的資料繫結
此分步演練演示如何以「主詳細資訊」形式將 POCO 類型綁定到 WPF 控件。 應用程式使用實體框架 API 使用資料庫中的數據填充物件、跟蹤更改並將數據儲存到資料庫中。

模型定義了參與一對多關係的兩種類型:**類別**\\(主體主控形狀)**和產品**(相關\\詳細資訊)。 然後,Visual Studio 工具用於將模型中定義的類型綁定到 WPF 控制件。 WPF 資料繫結框架支援相關物件之間的導航:選擇主檢視中的行會導致詳細檢視使用相應的子資料進行更新。

這個演練中的螢幕截圖和代碼清單取自 Visual Studio 2013,但您可以使用 Visual Studio 2012 或 Visual Studio 2010 完成本演練。

## <a name="use-the-object-option-for-creating-wpf-data-sources"></a>使用物件選項建立 WPF 資料來源

對於以前版本的實體框架,我們曾建議在基於使用 EF 設計器創建的模型創建新數據來源時使用**資料庫**選項。 這是因為設計器將生成從ObjectContext派生的上下文和派生自實體物件的實體類。 使用"資料庫"選項將説明您編寫用於與此 API 曲面互動的最佳程式碼。

2012 年 Visual Studio 的 EF 設計人員和 2013 年可視化工作室生成源自 DbContext 的上下文以及簡單的 POCO 實體類。 使用 Visual Studio 2010,我們建議交換到代碼生成範本,該範本使用 DbContext,如本演練後面的所述。

使用 DbContext API 曲面時,應在建立新資料來源時使用**Object**選項,如本演練所示。

如果需要,您可以為使用 EF 設計器建立的模型[還原到基於 ObjectContext 的程式碼產生](~/ef6/modeling/designer/codegen/legacy-objectcontext.md)。

## <a name="pre-requisites"></a>必要條件

您需要安裝 Visual Studio 2013、Visual Studio 2012 或 Visual Studio 2010 才能完成本演練。

如果您使用的是 Visual Studio 2010,則還必須安裝 NuGet。 有關詳細資訊,請參閱安裝[NuGet](https://docs.microsoft.com/nuget/install-nuget-client-tools)。  

## <a name="create-the-application"></a>建立應用程式

-   開啟 Visual Studio
-   **檔案&gt;&gt;- 新增 - 專案...**
-   在左邊窗格中選擇 **Windows,** 在右邊窗格中選擇 **WPF 應用程式**
-   輸入 **WPF 與 EFSample** 作為名稱
-   選擇 **"確定"**

## <a name="install-the-entity-framework-nuget-package"></a>安裝實體框架 NuGet 套件

-   在解決方案資源管理員中,右鍵按**下 WinForms 與 EFSample**專案
-   選擇 **"管理 NuGet 包..."...**
-   在「管理 NuGet」 套件「對話框中,選擇 **」連線**「工具」選項卡並選擇 **「實體框架**」套件
-   單擊"**安裝"**  
    >[!NOTE]
    > 除了實體框架程式集外,還添加了對 System.元件模型.Data註釋的引用。 如果專案具有對 System.Data.Entity 的引用,則在安裝實體框架包時將刪除該專案。 系統.Data.實體程式集不再用於實體框架6應用程式。

## <a name="define-a-model"></a>定義模型

在本演練中,您可以選擇使用代碼優先或 EF 設計器實現模型。 完成以下兩節之一。

### <a name="option-1-define-a-model-using-code-first"></a>選項 1: 首先使用程式碼定義模型

本節演示如何使用 Code First 創建模型及其關聯的資料庫。 如果您希望使用資料庫優先使用 EF 設計器從資料庫反向專案模型,請跳到下一部分(**選項 2:使用資料庫優先定義模型)**

使用代碼優先開發時,通常先編寫定義概念(網域)模型的 .NET 框架類。

-   向 **WPFEFSample**新增新類:
    -   右鍵按下項目名稱
    -   選擇 **'新增**',然後**選擇'新增專案**'
    -   選擇**類別類別的類別** **Product** 
-   將 **產品** 類別定義為以下代碼:

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

**產品**類別的 **「產品」** 類別**類別屬性**是導航屬性。 **Products** 在實體框架中,導航屬性提供了一種導航兩種實體類型之間的關係的方法。

除了定義實體之外,還需要定義派生自 DbContext 並公開&lt;DbSet&gt;TEntity 屬性的類。 DbSet&lt;TEntity&gt;屬性讓上下文知道要在模型中包括哪些類型。

DbContext 衍生類型的實體在執行時管理實體物件,其中包括將對象與資料庫中的數據填充、更改追蹤和將資料儲存到資料庫。

-   新增新的專案加入新的專案的**ProductContext**類別:

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

編譯專案。

### <a name="option-2-define-a-model-using-database-first"></a>選項 2: 首先使用資料庫定義模型

本節演示如何使用資料庫 First 使用 EF 設計器從資料庫反向工程模型。 如果完成了上一節(**選項 1:使用代碼優先定義模型),** 則跳過此部分並直接轉到 **「延遲載入**」部分。

#### <a name="create-an-existing-database"></a>建立現有資料庫

通常,當您將現有資料庫作為目標時,該資料庫將創建,但本演練需要創建要訪問的資料庫。

與 Visual Studio 一起安裝的資料庫伺服器因已安裝的 Visual Studio 版本而異:

-   如果您使用的是 Visual Studio 2010,您將創建一個 SQL Express 資料庫。
-   如果您使用的是 Visual Studio 2012,則您將創建一個[本地資料庫](https://msdn.microsoft.com/library/hh510202.aspx)資料庫。

讓我們繼續生成資料庫。

-   **檢視&gt;- 伺服器資源管理員**
-   右鍵按**下 資料&gt;連線 - 新增連線...**
-   如果尚未從伺服器資源管理員連接到資料庫,則需要選擇 Microsoft SQL Server 作為資料來源

    ![變更資料來源](~/ef6/media/changedatasource.png)

-   連接到本地資料庫或 SQL Express,具體取決於已安裝的哪個,然後輸入 **「產品**」作為資料庫名稱

    ![新增連線本地資料庫](~/ef6/media/addconnectionlocaldb.png)

    ![新增連線快訊](~/ef6/media/addconnectionexpress.png)

-   選擇 **「 確定」** 系統會詢問您要建立新資料庫,請選擇「**是**」

    ![建立資料庫](~/ef6/media/createdatabase.png)

-   新数据庫現在將顯示在伺服器資源管理器中,右鍵單擊它並選擇 **"新查詢"**
-   將以下 SQL 複製到新查詢中,然後右鍵單擊查詢並選擇 **"執行"**

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

#### <a name="reverse-engineer-model"></a>反向專案模型

我們將利用實體框架設計器(作為 Visual Studio 的一部分)創建我們的模型。

-   **專案&gt;- 新增新項目...**
-   從左方選單中選擇**資料**,然後**ADO.NET 實體資料模型**
-   輸入**產品模型**作為名稱,然後單擊 **"確定"**
-   這會啟動**實體資料模型精靈**
-   選擇 **"從数据庫生成**",然後單擊 **"下一步"**

    ![選擇模型內容](~/ef6/media/choosemodelcontents.png)

-   選擇與在第一部分中創建的資料庫的連接,輸入**ProductContext**作為連接字串的名稱,然後單擊「**下一步」**

    ![選擇您的連線](~/ef6/media/chooseyourconnection.png)

-   單擊"表格"旁邊的複選框導入所有表,然後單擊"完成"

    ![選擇物件](~/ef6/media/chooseyourobjects.png)

反向工程過程完成後,新模型將添加到專案中,並打開,供您在實體框架設計器中查看。 App.config 檔也已添加到專案中,並包含資料庫的連接詳細資訊。

#### <a name="additional-steps-in-visual-studio-2010"></a>視覺工作室 2010 中的其他步驟

如果您在 Visual Studio 2010 中工作,則需要更新 EF 設計器才能使用 EF6 代碼生成。

-   右鍵單擊 EF 設計器中模型的空白點,然後選擇 **"添加代碼生成項..."**
-   從左方選單**中選擇「連線樣本」** 並搜尋**DbContext**
-   選擇**EF 6.x\#DbContext 產生器為 C ,** 輸入**產品模型**作為名稱,然後按兩下"添加"

#### <a name="updating-code-generation-for-data-binding"></a>更新資料繫結的代碼產生

EF 使用 T4 範本從模型生成代碼。 視覺工作室附帶或從 Visual Studio 庫下載的範本用於一般用途。 這意味著從這些範本生成的實體具有簡單的&lt;ICollection&gt;T 屬性。 但是,使用 WPF 執行數據綁定時,最好將**可觀察集合**用於集合屬性,以便 WPF 可以追蹤對集合所做的更改。 為此,我們將修改範本以使用可觀察集合。

-   開啟**解決方案資源管理員**並尋找**產品模型.edmx**檔案
-   尋找將嵌套在 ProductModel.edmx 檔案下的**ProductModel.tt**檔案

    ![WPF 產品模型範本](~/ef6/media/wpfproductmodeltemplate.png)

-   按兩下ProductModel.tt檔以在可視化工作室編輯器中打開該檔
-   尋找 **「ICollection」** 的兩個事件並將其替換為「**可觀察集合**」 。 這些位於大約 296 和 484 線。
-   尋找「**哈希集**」 的第一次出現與「**可觀察集合」 取代**。 此事件大約位於第 50 行。 **不要**替換代碼後面找到的第二個哈希集。
-   尋找「**系統.集合.通用**」 的唯一匹配項並將其替換為「**系統.集合.objectModel」。** 這大約位於第 424 行。
-   保存ProductModel.tt檔。 這應會導致重新生成實體的代碼。 如果代碼未自動重新生成,則右鍵單擊ProductModel.tt並選擇「運行自定義工具」。。

如果您現在打開Category.cs檔(嵌套在ProductModel.tt下),那麼您應該看到產品集合具有 **"可&lt;觀察&gt;集合產品**"的類型。

編譯專案。

## <a name="lazy-loading"></a>消極式載入

**產品**類別的 **「產品」** 類別**類別屬性**是導航屬性。 **Products** 在實體框架中,導航屬性提供了一種導航兩種實體類型之間的關係的方法。

EF 為您提供了在首次訪問導航屬性時自動從資料庫載入相關實體的選項。 使用這種類型的載入(稱為延遲載入),請注意,如果內容尚未在上下文中,則第一次訪問每個導航屬性時,將對資料庫執行單獨的查詢。

使用 POCO 實體類型時,EF 透過在執行時建立派生代理類型的實例,然後在類中重寫虛擬屬性來添加載入掛鉤,從而實現延遲載入。 要獲取相關物件的延遲載入,必須聲明導航屬性 getter 為**公共**和**虛擬**(在 Visual Basic 中**可重寫**),並且不得**密封**類(在 Visual Basic 中**不可重寫**)。 使用資料庫時,第一個導航屬性會自動成為虛擬屬性,以啟用延遲載入。 在「代碼第一」部分中,我們選擇出於同樣的原因使導航屬性成為虛擬

## <a name="bind-object-to-controls"></a>將物件繫結檔到控制項

將模型中定義的類添加為此 WPF 應用程式的數據來源。

-   雙擊解決方案資源管理員中的**MainWindow.xaml**以開啟主窗體
-   從主選單中,選擇 **「&gt;專案 - 新增新資料來源...**
    ( Visual Studio 2010 中,您需要選擇**資料 -&gt;新增新資料來源...**
-   在「選擇資料來源類型視窗」中,選擇**物件**並按**下 「下一步**」
-   在"選擇數據物件"對話框中,展開**WPF 與 EFSample** 兩次並選擇 **"類別"**  
    *無需選擇**產品**資料來源,因為我們將透過 **「產品****」在類別**資料來源上的屬性取得它*  

    ![選擇資料物件](~/ef6/media/selectdataobjects.png)

-   按一下 **[完成]**。
-   *如果「資料來源」視窗未顯示,則選擇 **「查看&gt;&gt;- 其他 Windows- 資料來源**」,* 在 MainWindow.xaml 視窗旁邊打開資料來源。
-   按引腳圖示,以便數據源視窗不會自動隱藏。 如果視窗已可見,則可能需要點擊刷新按鈕。

    ![資料來源](~/ef6/media/datasources.png)

-   選擇 **「類別」** 資料來源並將其拖曳到窗體上。

當我們拖動此源時發生了以下情況:

-   **類別ViewSource**資源與**類別資料網格**控制項已新增到 XAML 
-   父格格元素上的 DataContext 屬性設置為"[靜態資源類別**ViewSource** ]"。**類別 ViewSource**資源用\\作外部 父網格元素的綁定源。 然後,內部網格元素從父網格繼承 DataContext 值(類別 DataGrid 的 ItemsSource 屬性設定為"{綁定}")

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

## <a name="adding-a-details-grid"></a>新增詳細資訊格線

現在,我們有一個網格來顯示類別,讓我們添加一個詳細資訊網格來顯示關聯的產品。

-   從 **「類別」** 資料來源下選擇 **「產品**」屬性,並將其拖動到窗體上。
    -   **類別 ProductsViewSource**資源和**產品資料網格**將新增到 XAML
    -   此資源的結合路徑設定為產品
    -   WPF 資料繫結框架可確保產品**DataGrid**中只顯示與所選類別相關的產品
-   從「工具箱」中,將**按鈕**拖動到窗體。 將 **「名稱」** 屬性**設定為按鈕「 儲存**」,將 **「內容」** 屬性設定為 **「 儲存**」 。

表單的名稱應表示:

![設計師](~/ef6/media/designer.png) 

## <a name="add-code-that-handles-data-interaction"></a>新增處理資料互動的代碼

是時候將一些事件處理程式添加到主視窗了。

-   在 XAML 視窗中,按下**&lt;視窗**元素,這會選擇主視窗
-   在 **「屬性」** 視窗中選擇右上角**的事件**,然後按兩下 **「已載入**」分頁右側的文字框

    ![主視窗屬性](~/ef6/media/mainwindowproperties.png)

-   此外,透過雙擊設計器中的「**儲存**」按鈕,添加 **「儲存**」按鈕的 Click 事件。 

這帶來了表單背後的代碼,我們現在將編輯代碼以使用 ProductContext 執行數據訪問。 更新主視窗的代碼,如下所示。

該代碼聲明**產品上下文**的長期運行實例。 **ProductContext**物件用於查詢數據並將其保存到資料庫中。 然後,從重寫的**On關閉**方法調用**ProductContext**實例上的**Dispose()。**代碼註釋提供有關代碼功能的詳細資訊。

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

## <a name="test-the-wpf-application"></a>測試 WPF 應用程式

-   編譯並執行應用程式。 如果您先使用代碼,您將看到為您創建了**WPFwithEFSample.ProductContext**資料庫。
-   在頂端網格中輸入類別名稱,在底部網格中輸入產品名稱*不要在 ID 列中輸入任何內容,因為主鍵由資料庫生成*

    ![包含新類別和產品的主視窗](~/ef6/media/screen1.png)

-   按 **「儲存」** 按鈕將資料儲存到資料庫

呼叫 DbContext 的**儲存變更 ()** 後,使用資料庫產生的值填充 ID。 因為我們在**保存更改()** 後調用**Refresh()** **DataGrid**控制項也使用新值進行更新。

![已填充了已填充了"已填充的"主視窗"](~/ef6/media/screen2.png)

## <a name="additional-resources"></a>其他資源

要瞭解有關使用 WPF 綁定到集合的資料詳細資訊,請參閱 WPF 文件中[的本主題](https://docs.microsoft.com/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections)。  
