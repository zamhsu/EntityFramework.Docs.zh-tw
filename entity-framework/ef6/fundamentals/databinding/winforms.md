---
title: 使用 WinForms 進行資料系結-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 80fc5062-2f1c-4dbd-ab6e-b99496784b36
ms.openlocfilehash: 4b3eee20ff238864b94ef4edfb97c1bae0713300
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181793"
---
# <a name="databinding-with-winforms"></a>使用 WinForms 進行資料系結
此逐步解說示範如何將 POCO 類型系結至「主要-詳細資料」表單中的 Window Forms （WinForms）控制項。 應用程式會使用 Entity Framework，以資料庫中的資料填入物件、追蹤變更，並將資料保存至資料庫。

模型會定義兩個參與一對多關聯性的類型：Category （principal @ no__t-0master）和 Product （相依 @ no__t-1detail）。 然後，Visual Studio 工具會用來將模型中定義的類型系結至 WinForms 控制項。 WinForms 資料系結架構可讓您在相關物件之間流覽：選取主視圖中的資料列會使詳細資料檢視以對應的子資料進行更新。

本逐步解說中的螢幕擷取畫面和程式代碼清單取自 Visual Studio 2013，但您可以使用 Visual Studio 2012 或 Visual Studio 2010 來完成此逐步解說。

## <a name="pre-requisites"></a>先決條件

您必須安裝 Visual Studio 2013、Visual Studio 2012 或 Visual Studio 2010，才能完成此逐步解說。

如果您使用 Visual Studio 2010，您也必須安裝 NuGet。 如需詳細資訊，請參閱[安裝 NuGet](https://docs.nuget.org/docs/start-here/installing-nuget)。

## <a name="create-the-application"></a>建立應用程式

-   開啟 Visual Studio
-   **檔案 &gt; 個新 &gt; 個專案 ...。**
-   在左窗格中選取 [ **windows** ]，然後在右窗格中選取 [ **windows FormsApplication** ]
-   輸入**WinFormswithEFSample**作為名稱
-   選取 [確定]

## <a name="install-the-entity-framework-nuget-package"></a>安裝 Entity Framework NuGet 套件

-   在方案總管中，以滑鼠右鍵按一下**WinFormswithEFSample**專案
-   選取 [**管理 NuGet 套件 ...** ]
-   在 [管理 NuGet 套件] 對話方塊中，選取 [**線上**] 索引標籤，然後選擇 [ **EntityFramework** ] 套件
-   按一下 [安裝]。  
    > [!NOTE]
    > 除了 EntityFramework 元件之外，也會新增 System.workflow.componentmodel.activity. DataAnnotations 的參考。 如果專案具有 system.string 實體的參考，則會在安裝 EntityFramework 封裝時將它移除。 System.web 元件不再用於 Entity Framework 6 應用程式。

## <a name="implementing-ilistsource-for-collections"></a>為集合執行 IListSource

集合屬性必須執行 IListSource 介面，以在使用 Windows Forms 時，啟用具有排序的雙向資料系結。 為了這麼做，我們將擴充 ObservableCollection 來新增 IListSource 功能。

-   將**ObservableListSource**類別新增至專案：
    -   以滑鼠右鍵按一下專案名稱
    -   選取 [**新增-&gt; 個新專案**]
    -   選取 [**類別**]，然後輸入**ObservableListSource**做為類別名稱
-   將預設產生的程式碼取代為下列程式碼：

@no__t 0This 類別可啟用雙向資料系結，以及排序。類別衍生自 ObservableCollection @ no__t-0T @ no__t-1，並新增 IListSource 的明確實作為。IListSource 的 GetList （）方法會實作為傳回 IBindingList，並與 ObservableCollection 保持同步。ToBindingList 所產生的 IBindingList 實作為支援排序。ToBindingList 擴充方法定義于 EntityFramework 元件中。 *

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

## <a name="define-a-model"></a>定義模型

在此逐步解說中，您可以選擇使用 Code First 或 EF Designer 來執行模型。 完成下列兩節的其中一個。

### <a name="option-1-define-a-model-using-code-first"></a>選項 1：使用 Code First 定義模型

本節說明如何使用 Code First 建立模型及其相關聯的資料庫。 跳到下一節（**Option 2：如果您想要使用 Database First 從使用 EF 設計工具的資料庫還原模型，請使用 Database First）**  定義模型

使用 Code First 開發時，您通常會從撰寫定義概念（網域）模型的 .NET Framework 類別開始著手。

-   將新的**產品**類別加入至專案
-   將預設產生的程式碼取代為下列程式碼：

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

-   將**Category**類別加入至專案。
-   將預設產生的程式碼取代為下列程式碼：

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

除了定義實體以外，您還需要定義衍生自**DbCoNtext**的類別，並公開**DbSet @ no__t-2TEntity @ no__t-3**屬性。 **DbSet**屬性可讓內容知道您想要包含在模型中的類型。 **DbCoNtext**和**DbSet**類型定義于 EntityFramework 元件中。

DbCoNtext 衍生類型的實例會在運行時間管理實體物件，其中包括以資料庫的資料填入物件、變更追蹤，以及將資料保存至資料庫。

-   將新的**ProductCoNtext**類別加入至專案。
-   將預設產生的程式碼取代為下列程式碼：

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

編譯專案。

### <a name="option-2-define-a-model-using-database-first"></a>選項 2：使用 Database First 定義模型

本節說明如何使用 Database First，從使用 EF 設計工具的資料庫，對模型進行反向工程。 如果您已完成上一節（**Option 1：使用 Code First）**  定義模型，然後略過此區段並直接移至 [**延遲載入**] 區段。

#### <a name="create-an-existing-database"></a>建立現有的資料庫

通常當您將目標設為現有的資料庫時，就會建立它，但在此逐步解說中，我們需要建立要存取的資料庫。

隨 Visual Studio 安裝的資料庫伺服器會根據您安裝的 Visual Studio 版本而有所不同：

-   如果您使用 Visual Studio 2010，您將會建立 SQL Express 資料庫。
-   如果您使用 Visual Studio 2012，則您將會建立[LocalDB](https://msdn.microsoft.com/library/hh510202.aspx)資料庫。

讓我們繼續產生資料庫。

-   **View-&gt; 伺服器總管**
-   以滑鼠右鍵按一下 [**資料連線-&gt; 新增連接 ...** ]
-   如果您還沒有從伺服器總管連接到資料庫，則必須選取 [Microsoft SQL Server] 做為資料來源

    ![變更資料來源](~/ef6/media/changedatasource.png)

-   連接到 LocalDB 或 SQL Express （視您安裝的版本而定），然後輸入**Products**作為資料庫名稱

    ![新增連接 LocalDB](~/ef6/media/addconnectionlocaldb.png)

    ![新增連接快速](~/ef6/media/addconnectionexpress.png)

-   選取 **[確定]** ，系統會詢問您是否要建立新的資料庫，然後選取 **[是]**

    ![建立資料庫](~/ef6/media/createdatabase.png)

-   新的資料庫現在會出現在伺服器總管中，以滑鼠右鍵按一下它，然後選取 [追加**查詢**]
-   將下列 SQL 複製到新的查詢中，然後以滑鼠右鍵按一下查詢並選取 [**執行**]。

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

我們將使用 Entity Framework Designer （隨附于 Visual Studio 的一部分）來建立我們的模型。

-   **專案-&gt; 加入新專案 。**
-   從左側功能表中選取 [**資料**]，然後**ADO.NET 實體資料模型**
-   輸入**ProductModel**作為名稱，然後按一下 **[確定]**
-   這會啟動**實體資料模型 Wizard**
-   選取 [**從資料庫產生**]，然後按 **[下一步]**

    ![ChooseModelContents](~/ef6/media/choosemodelcontents.png)

-   選取您在第一個區段中建立之資料庫的連接，並輸入**ProductCoNtext**做為連接字串的名稱，然後按 **[下一步]**

    ![選擇您的連線](~/ef6/media/chooseyourconnection.png)

-   按一下 [資料表] 旁的核取方塊以匯入所有資料表，然後按一下 [完成]

    ![選擇您的物件](~/ef6/media/chooseyourobjects.png)

反向工程程式完成之後，新的模型就會新增至您的專案，並開啟以供您在 Entity Framework Designer 中觀看。 App.config 檔案也已新增至您的專案，其中包含資料庫的連接詳細資料。

#### <a name="additional-steps-in-visual-studio-2010"></a>Visual Studio 2010 中的其他步驟

如果您是在 Visual Studio 2010 中工作，則必須更新 EF 設計工具以使用 EF6 程式碼產生。

-   在 EF 設計工具中，以滑鼠右鍵按一下模型的空白位置，然後選取 [**新增程式碼產生專案**...]。
-   從左側功能表中選取 [**線上範本**]，然後搜尋**DbCoNtext**
-   選取**C @ no__t-1 的 EF 6.X DbCoNtext**產生器，輸入**ProductsModel**做為名稱，然後按一下 [新增]

#### <a name="updating-code-generation-for-data-binding"></a>更新資料系結的程式碼產生

EF 會使用 T4 範本從您的模型產生程式碼。 隨附于 Visual Studio 或從 Visual Studio 資源庫下載的範本，主要是供一般用途使用。 這表示從這些範本產生的實體具有簡單的 ICollection @ no__t-0T @ no__t-1 屬性。 不過，在進行資料系結時，您需要有可執行 IListSource 的集合屬性。 這就是為什麼我們會建立上面的 ObservableListSource 類別，而我們現在要修改範本來使用這個類別。

-   開啟**方案總管**並尋找**ProductModel .edmx**檔案
-   尋找**ProductModel.tt**檔案，該檔案將會在 ProductModel .edmx 檔案底下加以嵌套

    ![產品型號範本](~/ef6/media/productmodeltemplate.png)

-   按兩下 ProductModel.tt 檔案，在 [Visual Studio 編輯器] 中開啟該檔案
-   尋找並以 "**ObservableListSource**" 取代 "**ICollection**" 的兩個出現專案。 這些位置大約是296和484行。
-   尋找並將第一個出現的 "**HashSet**" 取代為 "**ObservableListSource**"。 這個事件位於大約第50行。 **請**不要取代稍後在程式碼中找到的第二個出現的 HashSet。
-   儲存 ProductModel.tt 檔案。 這應該會導致重新產生實體的程式碼。 如果程式碼未自動重新產生，請以滑鼠右鍵按一下 [ProductModel.tt]，然後選擇 [執行自訂工具]。

如果您現在開啟 Category.cs 檔案（此檔案是在 ProductModel.tt 底下），您應該會看到 Products 集合的類型為**ObservableListSource @ no__t-1Product @ no__t-2**。

編譯專案。

## <a name="lazy-loading"></a>消極式載入

**Product**類別上**Category**類別和**category**屬性的**Products**屬性是導覽屬性。 在 Entity Framework 中，導覽屬性會提供一種方式來導覽兩個實體類型之間的關聯性。

當您第一次存取導覽屬性時，EF 可讓您選擇自動從資料庫載入相關實體。 使用這種類型的載入（稱為消極式載入）時，請注意，當您第一次存取每個導覽屬性時，如果內容尚未存在於環境中，則會針對資料庫執行個別的查詢。

使用 POCO 實體類型時，EF 會在執行時間建立衍生 proxy 類型的實例，然後覆寫類別中的虛擬屬性來新增載入攔截，以達到消極式載入。 若要取得相關物件的消極式載入，您必須將導覽屬性 getter 宣告為**public**和**virtual** （可在 Visual Basic 中覆**寫**），而且您的類別不得為**密封**（在 Visual Basic 中為**NotOverridable** ）。 使用 Database First 導覽屬性會自動設為虛擬，以啟用消極式載入。 在 [Code First] 區段中，我們選擇將導覽屬性設為 [虛擬]，原因如下

## <a name="bind-object-to-controls"></a>將物件系結至控制項

將模型中定義的類別新增為此 WinForms 應用程式的資料來源。

-   從主功能表中，選取 [**專案-&gt;] [加入新的資料來源**...]
    （在 Visual Studio 2010 中，您需要選取 [**資料-&gt;] [加入新的資料來源**...]）
-   在 [選擇資料來源類型] 視窗中，選取 [**物件**]，然後按 **[下一步]**
-   在 [選取資料物件] 對話方塊中，展開**WinFormswithEFSample**兩次，然後選取 [**類別**]，而不需要選取 [產品] 資料來源，因為我們會透過類別目錄資料源上的產品屬性來取得它。

    ![資料來源](~/ef6/media/datasource.png)

-   按一下 **[完成]。**
    如果 [資料來源] 視窗未顯示，請選取 [ **View-&gt; 個其他的 Windows-@no__t 2] 資料來源**
-   按釘選圖示，讓 [資料來源] 視窗不會自動隱藏。 如果視窗已經是可見的，您可能需要按 [重新整理] 按鈕。

    ![資料來源2](~/ef6/media/datasource2.png)

-   在方案總管中，按兩下**Form1.cs**檔案，以在 [設計師] 中開啟主要表單。
-   選取 [**類別**] 資料來源，並將它拖曳至表單上。 根據預設，新的 DataGridView （**categoryDataGridView**）和導覽工具列控制項都會加入至設計工具。 這些控制項也會系結至所建立的 BindingSource （**categoryBindingSource**）和系結導覽器（**categoryBindingNavigator**）元件。
-   編輯**categoryDataGridView**上的資料行。 我們想要將 [**類別**清單] 資料行設定為唯讀。 在儲存資料之後，資料庫會產生 [**類別**] 屬性的值。
    -   以滑鼠右鍵按一下 DataGridView 控制項，然後選取 [編輯資料行]。
    -   選取 [類別 Id] 資料行，並將 ReadOnly 設定為 True
    -   按 [確定]
-   從 [類別] [資料來源] 底下選取 [產品]，並將它拖曳至表單上。 ProductDataGridView 和 productBindingSource 會新增至表單。
-   編輯 productDataGridView 上的資料行。 我們想要隱藏 [類別目錄] 和 [類別] 資料行，並將 [ProductId] 設定為唯讀。 在儲存資料之後，資料庫會產生 ProductId 屬性的值。
    -   以滑鼠右鍵按一下 DataGridView 控制項，然後選取 [**編輯資料行**]。
    -   選取 [ **ProductId** ] 資料行，並將**ReadOnly**設定為**True**。
    -   選取 [**類別**清單] 資料行，然後按 [**移除**] 按鈕。 對 [**類別**] 資料行執行相同的動作。
    -   按下**確定**。

    到目前為止，我們在設計工具中將 DataGridView 控制項與 BindingSource 元件相關聯。 在下一節中，我們將在程式碼後置中新增程式碼，將 categoryBindingSource 設定為 DbCoNtext 目前所追蹤的實體集合。 當我們從類別下拖放產品時，WinForms 會負責將 productsBindingSource 屬性設定為 categoryBindingSource，並將 productsBindingSource 屬性設為 Products。 因為此系結，所以只有屬於目前所選分類的產品才會顯示在 productDataGridView 中。
-   按一下滑鼠右鍵並選取 [**已啟用**]，以啟用導覽工具列上的 [**儲存**] 按鈕。

    ![表單1設計工具](~/ef6/media/form1-designer.png)

-   按兩下按鈕，以新增 [儲存] 按鈕的事件處理常式。 這會新增事件處理常式，並帶您前往表單的程式碼後置。 **CategoryBindingNavigatorSaveItem @ no__t-1Click**事件處理常式的程式碼將在下一節中新增。

## <a name="add-the-code-that-handles-data-interaction"></a>加入處理資料互動的程式碼

我們現在會新增程式碼，以使用 ProductCoNtext 來執行資料存取。 更新主表單視窗的程式碼，如下所示。

程式碼會宣告 ProductCoNtext 的長時間執行實例。 ProductCoNtext 物件可用來查詢和儲存資料至資料庫。 然後會從覆寫的 OnClosing 方法呼叫 ProductCoNtext 實例上的 Dispose （）方法。 程式碼批註會提供程式碼用途的詳細資料。

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

## <a name="test-the-windows-forms-application"></a>測試 Windows Forms 應用程式

-   編譯並執行應用程式，您可以測試功能。

    ![儲存前先表單1](~/ef6/media/form1beforesave.png)

-   儲存商店產生的金鑰之後，畫面上會顯示。

    ![儲存後的表單1](~/ef6/media/form1aftersave.png)

-   如果您使用 Code First，您也會看到為您建立**WinFormswithEFSample ProductCoNtext**資料庫。

    ![伺服器物件總管](~/ef6/media/serverobjexplorer.png)
