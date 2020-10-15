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
# <a name="databinding-with-wpf"></a>使用 WPF 的資料繫結

> [!IMPORTANT]
> **本檔僅適用于 .NET Framework 上的 WPF**
>
> 本檔說明 .NET Framework 上 WPF 的資料系結。 針對新的 .NET Core 專案，建議您使用 [EF Core](xref:core/index) 而不是 Entity Framework 6。 EF Core 中的資料系結檔如下： [使用 WPF 消費者入門](xref:core/get-started/wpf)。

本逐步解說將示範如何在「主從階層」表單中，將 POCO 型別系結至 WPF 控制項。 應用程式會使用 Entity Framework Api，以資料庫中的資料填入物件、追蹤變更，以及將資料保存到資料庫。

此模型會定義兩種參與一對多關聯性的類型： **類別** (主體 \\ 主機) 和 **產品** (相依 \\ 詳細資料) 。 然後，Visual Studio 工具會用來將模型中定義的類型系結至 WPF 控制項。 WPF 資料系結架構會啟用相關物件之間的導覽：選取主視圖中的資料列會導致詳細資料檢視以對應的子資料更新。

本逐步解說中的螢幕擷取畫面和程式代碼清單取自 Visual Studio 2013，但您可以使用 Visual Studio 2012 或 Visual Studio 2010 來完成此逐步解說。

## <a name="use-the-object-option-for-creating-wpf-data-sources"></a>使用 [物件] 選項來建立 WPF 資料來源

在舊版 Entity Framework 中，我們會在根據使用 EF 設計工具建立的模型建立新的資料來源時，建議您使用 **資料庫** 選項。 這是因為設計工具會產生衍生自 ObjectCoNtext 的內容，以及衍生自 EntityObject 的實體類別。 使用資料庫選項，可協助您撰寫與此 API 介面互動的最佳程式碼。

Visual Studio 2012 和 Visual Studio 2013 的 EF 設計工具會產生一個衍生自 DbCoNtext 的內容，以及簡單的 POCO 實體類別。 在 Visual Studio 2010 中，我們建議您交換使用 DbCoNtext 的程式碼產生範本，如本逐步解說稍後所述。

使用 DbCoNtext API 介面時，您應該在建立新的資料來源時使用 **物件** 選項，如本逐步解說所示。

如有需要，您可以針對以 EF 設計工具建立的模型， [還原為以 ObjectCoNtext 為基礎的程式碼產生](xref:ef6/modeling/designer/codegen/legacy-objectcontext) 。

## <a name="pre-requisites"></a>必要條件

您必須安裝 Visual Studio 2013、Visual Studio 2012 或 Visual Studio 2010，才能完成此逐步解說。

如果您使用 Visual Studio 2010，您也必須安裝 NuGet。 如需詳細資訊，請參閱 [安裝 NuGet](/nuget/install-nuget-client-tools)。  

## <a name="create-the-application"></a>建立應用程式

- 開啟 Visual Studio
- **檔案- &gt; 新增- &gt; 專案 .。。**
- 選取 **Windows**   左窗格中的 [Windows]，然後在右窗格中 **WPFApplication**
- 輸入 **WPFwithEFSample**   作為名稱
- 選取 **[確定]**

## <a name="install-the-entity-framework-nuget-package"></a>安裝 Entity Framework NuGet 套件

- 在方案總管中，以滑鼠右鍵按一下 **WinFormswithEFSample** 專案
- 選取 [**管理 NuGet 套件 ...** ]
- 在 [管理 NuGet 封裝] 對話方塊中，選取 [ **線上** ] 索引標籤，然後選擇 **EntityFramework** 套件
- 按一下 [安裝]  
    >[!NOTE]
    > 除了 EntityFramework 元件之外，也會加入 ComponentModel. DataAnnotations 的參考。 如果專案具有 system.string 的參考，則會在安裝 EntityFramework 套件時將其移除。 Entity Framework 6 應用程式不再使用 System.object 元件。

## <a name="define-a-model"></a>定義模型

在這個逐步解說中，您可以選擇使用 Code First 或 EF 設計工具來執行模型。 完成下列兩節的其中一節。

### <a name="option-1-define-a-model-using-code-first"></a>選項1：使用 Code First 定義模型

本節說明如何使用 Code First 建立模型及其相關聯的資料庫。 跳到下一節， (**選項2：使用 Database First 來定義模型) ** 如果您想要 DATABASE FIRST 使用 EF 設計工具，從資料庫將模型反向工程

使用 Code First 開發時，通常會先撰寫 .NET Framework 類別，以定義您的概念 (網域) 模型。

- 將新類別新增至 **WPFwithEFSample：**
  - 以滑鼠右鍵按一下專案名稱
  - 選取 **[** 新增]，然後選取 [**新增專案**]
  - 選取**類別**，並 **Product**輸入   類別名稱的產品
- 以下列程式碼取代 **Product**   類別定義：

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

**Product**類別的**Category**類別和**category**屬性上的**Products**屬性為導覽屬性。 在 Entity Framework 中，導覽屬性會提供一種方式來導覽兩個實體類型之間的關聯性。

除了定義實體之外，您還必須定義衍生自 DbCoNtext 的類別，並公開 DbSet &lt; TEntity &gt; 屬性。 DbSet &lt; TEntity &gt; 屬性可讓內容知道您想要包含在模型中的類型。

DbCoNtext 衍生類型的實例會在運行時間管理實體物件，包括將資料庫的資料填入物件、變更追蹤，以及將資料保存到資料庫。

- 使用下列定義，將新的 **ProductCoNtext** 類別新增至專案：

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

### <a name="option-2-define-a-model-using-database-first"></a>選項2：使用 Database First 定義模型

本節說明如何使用 EF designer，從資料庫使用 Database First 對模型進行反向工程。 如果您已完成上一節 (**選項1：使用 Code First) 來定義模型 **，請略過本節，並直接移至消極式 **載入** 區段。

#### <a name="create-an-existing-database"></a>建立現有的資料庫

通常當您將目標設為現有的資料庫時，就會建立該資料庫，但在此逐步解說中，我們需要建立要存取的資料庫。

隨 Visual Studio 安裝的資料庫伺服器會根據您所安裝的 Visual Studio 版本而有所不同：

- 如果您使用 Visual Studio 2010，將會建立 SQL Express 資料庫。
- 如果您使用 Visual Studio 2012，則會建立 [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) 資料庫。

讓我們繼續產生資料庫。

- **View- &gt; 伺服器總管**
- 以滑鼠右鍵按一下 [**資料連線- &gt; 新增連接 ...** ]
- 如果您還沒有從伺服器總管連接到資料庫，則必須選取 Microsoft SQL Server 作為資料來源

    ![變更資料來源](~/ef6/media/changedatasource.png)

- 連接至 LocalDB 或 SQL Express （視您安裝的版本而定），並輸入 **產品** 做為資料庫名稱

    ![加入連接 LocalDB](~/ef6/media/addconnectionlocaldb.png)

    ![新增連接快速](~/ef6/media/addconnectionexpress.png)

- 選取 **[確定]** ，系統會詢問您是否要建立新的資料庫，請選取 **[是]** 。

    ![建立資料庫](~/ef6/media/createdatabase.png)

- 新資料庫現在會出現在伺服器總管中，以滑鼠右鍵按一下該資料庫，然後選取 [追加**查詢**]。
- 將下列 SQL 複製到新的查詢，然後以滑鼠右鍵按一下查詢並選取 [**執行**]

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

#### <a name="reverse-engineer-model"></a>反向工程模型

我們將利用 Entity Framework Designer，其中包含做為 Visual Studio 的一部分，以建立我們的模型。

- **專案- &gt; 加入新專案 .。。**
- 從左側功能表中選取 [ **資料** ]，然後 **ADO.NET 實體資料模型**
- 輸入 **ProductModel** 做為名稱，然後按一下 **[確定]**
- 這會啟動 **實體資料模型 Wizard**
- 選取 [ **從資料庫產生** ]，然後按 **[下一步]**

    ![選擇模型內容](~/ef6/media/choosemodelcontents.png)

- 選取您在第一個區段中建立之資料庫的連接，輸入**ProductCoNtext**做為連接字串的名稱，然後按 **[下一步]** 。

    ![選擇您的連接](~/ef6/media/chooseyourconnection.png)

- 按一下 [資料表] 旁的核取方塊以匯入所有資料表，然後按一下 [完成]

    ![選擇您的物件](~/ef6/media/chooseyourobjects.png)

一旦反向工程程式完成，新的模型就會加入至您的專案並開啟，以供您在 Entity Framework Designer 中查看。 您的專案中也已加入 App.config 檔案，其中包含資料庫的連接詳細資料。

#### <a name="additional-steps-in-visual-studio-2010"></a>Visual Studio 2010 中的其他步驟

如果您在 Visual Studio 2010 中工作，則必須更新 EF 設計工具，才能使用 EF6 程式碼產生。

- 在 EF 設計工具中，以滑鼠右鍵按一下模型的空白位置，然後選取 [**新增程式碼產生專案 ...** ]。
- 從左側功能表中選取 [ **線上範本** ]，然後搜尋 **DbCoNtext**
- 選取 **適用于 C 的 EF 6.X DbCoNtext 產生器 \# ，** 輸入 **ProductsModel** 做為名稱，然後按一下 [新增]。

#### <a name="updating-code-generation-for-data-binding"></a>更新資料系結的程式碼產生

EF 會使用 T4 範本從您的模型產生程式碼。 隨附于 Visual Studio 或從 Visual Studio 資源庫下載的範本，適用于一般用途。 這表示，從這些範本產生的實體會有簡單的 ICollection &lt; T &gt; 屬性。 不過，使用 WPF 進行資料系結時，最好使用 **ObservableCollection** 進行集合屬性，讓 WPF 可以追蹤對集合所做的變更。 為了這個目的，我們將修改範本以使用 ObservableCollection。

- 開啟 **方案總管** 並尋找 **ProductModel .edmx** 檔案
- 尋找 **ProductModel.tt** 檔案，該檔案將會嵌套在 ProductModel .edmx 檔下

    ![WPF 產品模型範本](~/ef6/media/wpfproductmodeltemplate.png)

- 按兩下 ProductModel.tt 檔案，在 Visual Studio 編輯器中開啟該檔案
- 尋找並以 "**ObservableCollection**" 取代兩個出現的 "**ICollection**"。 這些大約位於行296和484。
- 尋找並取代第一個出現的 "**HashSet**" 和 "**ObservableCollection**"。 這項情況大約位於第50行。 **請勿** 取代稍後在程式碼中找到的第二次 HashSet。
- 找出並以 "**system.collections.objectmodel.observablecollection**" 取代唯一出現的 "**system.object**"。 這大約位於行424。
- 儲存 ProductModel.tt 檔案。 這應該會導致重新產生實體的程式碼。 如果程式碼不會自動重新產生，請在 ProductModel.tt 上按一下滑鼠右鍵，然後選擇 [執行自訂工具]。

如果您現在開啟 Category.cs 檔案 (該檔案在 ProductModel.tt) ，您應該會看到 Products 集合的類型為**ObservableCollection &lt; Product &gt; **。

編譯專案。

## <a name="lazy-loading"></a>消極式載入

**Product**類別的**Category**類別和**category**屬性上的**Products**屬性為導覽屬性。 在 Entity Framework 中，導覽屬性會提供一種方式來導覽兩個實體類型之間的關聯性。

EF 讓您可以選擇在第一次存取導覽屬性時，自動從資料庫載入相關實體。 使用這種類型的載入 (稱為消極式載入) ，請注意，當您第一次存取每個導覽屬性時，如果內容中還沒有內容，則會對資料庫執行個別的查詢。

使用 POCO 實體類型時，EF 會在執行時間建立衍生 proxy 型別的實例，然後覆寫類別中的虛擬屬性以新增載入攔截，藉以達到消極式載入。 若要取得相關物件的消極式載入，您必須在 Visual Basic) 中將導覽屬性 getter 宣告為 **public** 和 **virtual** (可覆 **寫** ，而且您的類別不得在 Visual Basic) 中 **密封** (**NotOverridable** 。 使用 Database First 導覽屬性會自動設為虛擬，以啟用消極式載入。 在 [Code First] 區段中，我們選擇讓導覽屬性基於相同的原因進行虛擬

## <a name="bind-object-to-controls"></a>將物件系結至控制項

將模型中定義的類別加入為這個 WPF 應用程式的資料來源。

- 按兩下方案總管中的 [ **MainWindow** ] 以開啟主要表單
- 從主功能表選取 [**專案- &gt; 加入新的資料來源**...]
     (在 Visual Studio 2010 中，您必須選取 [ **資料- &gt; 新增資料來源** ]) 
- 在 [選擇資料來源 Typewindow] 中選取 [**物件**]，然後按 **[下一步]** 。
- 在 [選取資料物件] 對話方塊中，將**WPFwithEFSample**展開   兩次，然後選取 [**類別**]  
    *不需要選取**產品**資料來源，因為我們會透過**類別**資料來源上的**產品**屬性來取得它。*  

    ![選取資料物件](~/ef6/media/selectdataobjects.png)

- 按一下 [完成]  。
- 如果 [資料來源] 視窗未顯示，請在 [MainWindow] 視窗旁邊開啟 [資料來源] 視窗 *，然後選取 [ **View- &gt; 其他 Windows- &gt; 資料來源*** ]。
- 按下釘選圖示，讓 [資料來源] 視窗不會自動隱藏。 如果視窗已顯示，您可能需要按 [重新整理] 按鈕。

    ![Data Sources](~/ef6/media/datasources.png)

- 選取 [ **類別** ] 資料來源，然後將它拖曳到表單上。

拖曳此來源時，會發生下列情況：

- **CategoryViewSource**資源和**categoryDataGrid**控制項已新增至 XAML
- 父方格元素上的 DataCoNtext 屬性已設定為 "{StaticResource **categoryViewSource** }"。**CategoryViewSource**資源可作為外部父方格元素的系結來源 \\ 。 然後，內部方格元素會從父方格繼承 DataCoNtext 值， (categoryDataGrid 的 ItemsSource 屬性設定為 "{Binding}" ) 

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

## <a name="adding-a-details-grid"></a>加入詳細資料方格

現在我們有一個顯示類別的方格，讓我們加入詳細資料方格來顯示相關聯的產品。

- 從 [ **類別目錄**] 資料來源底下選取 [ **Products** ] 屬性，然後將它拖曳到表單上。
  - **CategoryProductsViewSource**資源和**productDataGrid**方格會新增至 XAML
  - 此資源的系結路徑設定為 Products
  - WPF 資料系結架構可確保只有與所選分類相關的產品會顯示在**productDataGrid**中
- 從 [工具箱] 中，將 **按鈕** 拖曳至表單。 將 [ **名稱** ] 屬性設為 **buttonSave** ，並將 [ **Content** ] 屬性設定為 [ **儲存**]。

表單看起來應該像這樣：

![設計工具表單](~/ef6/media/designer.png)

## <a name="add-code-that-handles-data-interaction"></a>新增處理資料互動的程式碼

現在可以將一些事件處理常式加入主視窗中。

- 在 XAML 視窗中，按一下 [ ** &lt; 視窗**] 元素，這會選取主視窗。
- 在 [ **屬性** ] 視窗中，選擇右上方的 [ **事件** ]，然後按兩下 **載入** 的標籤右邊的文字方塊

    ![主視窗屬性](~/ef6/media/mainwindowproperties.png)

- 也請按兩下設計工具中的 [儲存] 按鈕，以加入 [**儲存**] 按鈕的**Click**事件。

這會帶您前往表單的程式碼後接，我們現在將編輯程式碼，以使用 ProductCoNtext 來執行資料存取。 更新 MainWindow 的程式碼，如下所示。

此程式碼會宣告長時間執行的 **ProductCoNtext**實例。 **ProductCoNtext**物件是用來查詢資料庫，並將資料儲存至資料庫。 然後，會從覆寫的**依序 onclosing**方法呼叫**ProductCoNtext**實例上的**Dispose ( # B1** 。程式碼批註會提供程式碼用途的詳細資料。

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

- 編譯並執行應用程式。 如果您使用 Code First，就會看到為您建立了 **WPFwithEFSample ProductCoNtext** 資料庫。
- 在頂端方格中輸入分類名稱，底部方格中的產品名稱不會在 [*識別碼] 資料行中輸入任何資料，因為主鍵是由資料庫所產生*。

    ![具有新類別和產品的主視窗](~/ef6/media/screen1.png)

- 按下 [ **儲存** ] 按鈕，將資料儲存至資料庫

呼叫 DbCoNtext 的 **SaveChanges ( # B1 **之後，識別碼就會填入資料庫產生的值。 因為我們在 SaveChanges 之後呼叫了 **Refresh ( # B1 ** ** ( # B3 ** ，所以 **DataGrid** 控制項也會以新的值更新。

![填入識別碼的主視窗](~/ef6/media/screen2.png)

## <a name="additional-resources"></a>其他資源

若要深入瞭解如何使用 WPF 將資料系結至集合，請參閱 WPF 檔中的 [這個主題](/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections) 。  
