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
# <a name="databinding-with-winforms"></a>與 WinForms 資料繫結
此逐步解說示範如何在 [主版詳細資料] 表單的視窗 Forms (WinForms) 控制項繫結至 POCO 型別。 應用程式會使用 Entity Framework 填入資料庫的資料物件、 追蹤變更，並將資料保存到資料庫。

此模型會定義參與的兩種類型一個對多關聯性中： 類別目錄 (主體\\主要) 和產品 (相依\\詳細資料)。 然後，Visual Studio 工具用來繫結至 WinForms 控制項模型中定義的類型。 WinForms 資料繫結架構可讓相關的物件之間的瀏覽： 主版檢視中選取的資料列會導致對應的子資料更新的詳細資料檢視。

在本逐步解說的程式碼清單的螢幕擷取畫面取自 Visual Studio 2013，但您可以完成這個逐步解說中的使用 Visual Studio 2012 或 Visual Studio 2010。

## <a name="pre-requisites"></a>必要條件

您必須有 Visual Studio 2013，才能完成此逐步解說安裝的 Visual Studio 2012 或 Visual Studio 2010。

如果您使用 Visual Studio 2010，您也必須安裝 NuGet。 如需詳細資訊，請參閱 <<c0> [ 安裝 NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)。

## <a name="create-the-application"></a>建立應用程式

-   開啟 Visual Studio
-   **檔案-&gt;新增-&gt;專案...**
-   選取  **Windows**的左窗格中並**Windows FormsApplication**在右窗格
-   請輸入**WinFormswithEFSample**做為名稱
-   選取 [確定]

## <a name="install-the-entity-framework-nuget-package"></a>安裝 Entity Framework NuGet 套件

-   在 [方案總管] 中，以滑鼠右鍵按一下**WinFormswithEFSample**專案
-   選取**管理 NuGet 封裝...**
-   在 [管理 NuGet 套件] 對話方塊中，選取**線上**索引標籤，然後選擇**EntityFramework**封裝
-   按一下 **安裝**  
    > [!NOTE]
    > 除了 EntityFramework 組件也會加入 System.ComponentModel.DataAnnotations 的參考。 如果專案具有 System.Data.Entity 的參考，則它將會移除安裝 EntityFramework 套件時。 System.Data.Entity 組件不會再用於 Entity Framework 6 應用程式。

## <a name="implementing-ilistsource-for-collections"></a>IListSource 實作集合

集合屬性必須實作 IListSource 介面以啟用排序時使用 Windows Form 的雙向資料繫結。 若要這樣做，我們即將擴充 ObservableCollection 加入 IListSource 功能。

-   新增**ObservableListSource**類別至專案：
    -   以滑鼠右鍵按一下專案名稱
    -   選取 **新增-&gt;新項目**
    -   選取 **類別**，然後輸入**ObservableListSource**的類別名稱
-   取代為下列程式碼的預設產生的程式碼：

*這個類別會啟用雙向資料繫結，以及排序。類別衍生自 ObservableCollection&lt;T&gt;並新增明確的 IListSource 實作。IListSource getlist （） 方法實作傳回 IBindingList 實作，它與 ObservableCollection 保持同步。產生 ToBindingList IBindingList 實作支援排序。EntityFramework 組件中定義 ToBindingList 擴充方法。*

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

在本逐步解說，您可以選擇實作模型，使用程式碼優先 」 或 「 EF 設計工具。 完成其中兩個下列各節。

### <a name="option-1-define-a-model-using-code-first"></a>選項 1： 定義使用 Code First 模型

本節說明如何建立模型和其相關聯的資料庫使用 Code First。 請跳至下一節 (**選項 2： 定義模型使用 Database First)** 如果您想要使用 Database First 反轉設計您的模型使用 EF 設計工具，從資料庫

使用 Code First 開發時您通常會開始撰寫定義概念 （網域） 模型的.NET Framework 類別。

-   加入新**產品**類別至專案
-   取代為下列程式碼的預設產生的程式碼：

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

-   新增**分類**類別至專案。
-   取代為下列程式碼的預設產生的程式碼：

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

除了定義實體，您必須定義衍生自類別**DbContext** ，並公開**DbSet&lt;TEntity&gt;** 屬性。 **DbSet**屬性可讓知道您想要在模型中包含哪些的類型的內容。 **DbContext**並**DbSet** EntityFramework 組件中所定義的類型。

DbContext 衍生型別的執行個體在執行階段，其中包含填入資料庫的資料的物件期間同時管理實體物件、 變更追蹤，和保存資料至資料庫。

-   加入新**ProductContext**類別至專案。
-   取代為下列程式碼的預設產生的程式碼：

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

### <a name="option-2-define-a-model-using-database-first"></a>選項 2： 定義使用 Database First 模型

本節說明如何使用 Database First，進行反向工程，您使用 EF 設計工具，從資料庫的模型。 如果您已完成上一節 (**選項 1： 定義模型使用 Code First)**，然後略過本節並直接前往**消極式載入**一節。

#### <a name="create-an-existing-database"></a>建立現有的資料庫

通常當您的目標將已建立，現有的資料庫，但在此逐步解說，我們需要建立可存取的資料庫。

Visual Studio 隨附安裝的資料庫伺服器是您已安裝的 Visual Studio 版本而有所不同：

-   如果您使用 Visual Studio 2010 將會建立 SQL Express 資料庫。
-   如果您使用 Visual Studio 2012，則您將會建立[LocalDB](https://msdn.microsoft.com/library/hh510202.aspx)資料庫。

接下來產生的資料庫。

-   **檢視-&gt;伺服器總管**
-   以滑鼠右鍵按一下**資料連線-&gt;新增連接...**
-   如果您尚未連線至資料庫從伺服器總管之前您必須選取 Microsoft SQL Server 做為資料來源

    ![變更資料來源](~/ef6/media/changedatasource.png)

-   連接到 LocalDB 或 SQL Express，何者而定，您已安裝，然後輸入**產品**做為資料庫名稱

    ![新增連線 LocalDB](~/ef6/media/addconnectionlocaldb.png)

    ![新增連線 Express](~/ef6/media/addconnectionexpress.png)

-   選取  **確定**而且會要求您想要建立新資料庫，請選取**是**

    ![建立資料庫](~/ef6/media/createdatabase.png)

-   新資料庫現在會出現在 [伺服器總管] 中，按一下滑鼠右鍵，然後選取**新查詢**
-   將下列 SQL 複製到新的查詢，然後以滑鼠右鍵按一下查詢並選取**Execute**

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

我們將利用 Entity Framework Designer 中，也就是 Visual Studio 的一部分，以建立我們的模型。

-   **專案-&gt;加入新項目...**
-   選取 **資料**從左側功能表，然後**ADO.NET 實體資料模型**
-   請輸入**ProductModel**作為名稱，然後按一下 **[確定]**
-   這會啟動**Entity Data Model 精靈**
-   選取 **從資料庫產生**，按一下 **下一步**

    ![ChooseModelContents](~/ef6/media/choosemodelcontents.png)

-   選取您建立第一個區段中的資料庫連接中，輸入**ProductContext**做為連接字串，然後按一下 [名稱**下一步]**

    ![選擇您的連線](~/ef6/media/chooseyourconnection.png)

-   按一下 匯入的所有資料表，並按一下 完成 '資料表旁的核取方塊

    ![選擇您的物件](~/ef6/media/chooseyourobjects.png)

反向工程程序完成後新模型加入您的專案，並讓您檢視在 Entity Framework Designer 中開啟。 App.config 檔案也已新增至您的專案與資料庫的連線詳細資料。

#### <a name="additional-steps-in-visual-studio-2010"></a>Visual Studio 2010 中的其他步驟

如果您正在 Visual Studio 2010 中將需要更新使用 EF6 程式碼產生的 EF 設計工具。

-   以滑鼠右鍵按一下您的模型，在 EF 設計工具中的非空點，然後選取**加入的程式碼產生項目...**
-   選取 **線上範本**從左側的功能表，並搜尋**DbContext**
-   選取**EF 6.x 適用於 C 的 DbContext Generator\#，** 輸入**ProductsModel**做為名稱並按一下 [新增]

#### <a name="updating-code-generation-for-data-binding"></a>更新資料繫結的程式碼產生

EF 從模型使用 T4 範本產生程式碼。 隨附於 Visual Studio，或從 Visual Studio 組件庫下載的範本供一般用途使用。 這表示從這些範本所產生的實體有簡單的 ICollection&lt;T&gt;屬性。 不過，進行資料繫結時，需要實作 IListSource 的集合屬性。 這就是為什麼我們建立上述 ObservableListSource 類別和我們現在要修改範本，請使用這個類別。

-   開啟**方案總管**並尋找**ProductModel.edmx**檔案
-   尋找**ProductModel.tt**下 ProductModel.edmx 檔案會進行巢狀檔案

    ![產品模型範本](~/ef6/media/productmodeltemplate.png)

-   按兩下 ProductModel.tt 檔案在 Visual Studio 編輯器中開啟它
-   尋找和取代出現兩次的 「**ICollection**"with"**ObservableListSource**"。 這些是位在大約行 296 和 484。
-   尋找和取代第一個出現的 「**HashSet**"with"**ObservableListSource**"。 這項問題位於大約第 50 行。 **不這麼做**取代 HashSet 稍後在程式碼中找到的第二個執行個體。
-   儲存 ProductModel.tt 檔案。 這應該會造成重新產生實體的程式碼。 如果程式碼不會自動重新產生，然後以滑鼠右鍵按一下 ProductModel.tt，並選擇 [執行自訂工具]。

如果您現在開啟 Category.cs 檔案 （這在 ProductModel.tt 之下巢狀），則您應該會看到產品集合中具有類型**ObservableListSource&lt;產品&gt;**。

編譯專案。

## <a name="lazy-loading"></a>消極式載入

**產品**屬性上的**類別目錄**類別並**類別**屬性**產品**類別為導覽屬性。 在 Entity Framework 中，導覽屬性會提供瀏覽兩個實體類型之間的關聯性的方法。

EF 可讓您選擇載入相關的實體從資料庫自動第一次存取導覽屬性。 使用此類型的載入 （稱為消極式載入），請注意，第一次存取每個導覽屬性不同的查詢將會針對資料庫執行如果內容已不在內容中。

使用 POCO 實體類型時，EF 會建立衍生的 proxy 型別的執行個體在執行階段，然後再覆寫虛擬屬性類別，藉此加入載入攔截程序中的達到消極式載入。 若要取得相關物件的消極式載入，您必須宣告瀏覽做為屬性 getter**公用**並**虛擬**(**Overridable** Visual Basic 中)，且您的類別不可以是**密封**(**NotOverridable** Visual Basic 中)。 使用資料庫時的第一個導覽屬性會自動進行虛擬化來啟用消極式載入。 第一個程式碼區段中，我們選擇基於相同原因，請瀏覽屬性虛擬

## <a name="bind-object-to-controls"></a>物件繫結至控制項

加入做為資料來源，此 WinForms 應用程式模型中所定義的類別。

-   從主功能表中，選取**專案-&gt;加入新的資料來源...**
    (在 Visual Studio 2010 中，您必須選取**的資料-&gt;加入新資料來源...**)
-   在 [選擇資料來源類型] 視窗中，選取**物件**，按一下 [**下一步]**
-   在 選取資料物件 對話方塊中，展開**WinFormswithEFSample**兩次，然後選取**分類**就不需要選取 Product 資料來源，因為我們會透過該產品的上前往在 類別目錄資料來源上的屬性。

    ![資料來源](~/ef6/media/datasource.png)

-   按一下 **完成。**
    *如果未顯示 資料來源 視窗，選取 * * * 檢視-&gt;其他 Windows-&gt;資料來源**
-   按 釘選 圖示，讓資料來源 視窗不會不會自動隱藏。 您可能需要按 [重新整理] 按鈕，如果視窗已顯示。

    ![資料來源 2](~/ef6/media/datasource2.png)

-   在 [方案總管] 中，按兩下**Form1.cs**設計工具中開啟主要表單的檔案。
-   選取 **分類**資料來源，並將它拖曳到表單上。 根據預設，新的 DataGridView (**categoryDataGridView**) 並瀏覽工具列上的控制項加入至設計工具。 這些控制項會繫結至 BindingSource (**categoryBindingSource**) 和繫結的巡覽器 (**categoryBindingNavigator**) 一併建立的元件。
-   在 編輯資料行**categoryDataGridView**。 我們想要設定**CategoryId**為唯讀的資料行。 值**CategoryId**我們儲存資料之後，將會產生資料庫的屬性。
    -   DataGridView 控制項中以滑鼠右鍵按一下並選取 編輯資料行...
    -   選取 [CategoryId] 欄，然後將 ReadOnly 設定為 True
    -   按 [確定]
-   選取產品類別目錄資料來源底下，並將它拖曳表單上。 ProductDataGridView 和 productBindingSource 會新增至表單。
-   編輯 productDataGridView 上的資料行。 我們想要隱藏 CategoryId 和類別目錄的資料行，並將產品編號設定為唯讀。 ProductId 屬性的值是由資料庫產生的之後我們儲存的資料。
    -   DataGridView 控制項中以滑鼠右鍵按一下並選取**編輯資料行...**.
    -   選取  **ProductId**資料行並將**ReadOnly**來**True**。
    -   選取 [ **CategoryId**資料行，然後按**移除**] 按鈕。 執行相同的作業**分類**資料行。
    -   按下**確定**。

    到目前為止，我們會與設計工具中的 BindingSource 元件關聯 DataGridView 控制項。 下一節我們將程式碼加入程式碼後置 categoryBindingSource.DataSource 設 DbContext 目前所追蹤之實體的集合。 當我們拖曳和置放的產品，從該類別中，WinForms 之下花了負責設定產品的 categoryBindingSource 和 productsBindingSource.DataMember 屬性 productsBindingSource.DataSource 屬性。 因為此繫結，則會顯示在 productDataGridView 的只有屬於目前選取之分類的產品。
-   啟用**儲存**上按一下滑鼠右鍵，然後選取 [瀏覽] 工具列按鈕**已啟用**。

    ![1 的表單設計工具](~/ef6/media/form1-designer.png)

-   加入事件處理常式，儲存按兩下按鈕的按鈕。 這將會加入事件處理常式，並將您帶到程式碼後置中，表單。 程式碼**categoryBindingNavigatorSaveItem\_按一下**事件處理常式會新增下一節。

## <a name="add-the-code-that-handles-data-interaction"></a>新增處理與資料互動的程式碼

現在，我們要加入的程式碼，用以 ProductContext 執行資料存取。 更新主表單的視窗的程式碼，如下所示。

程式碼會宣告 ProductContext 的長時間執行執行個體。 ProductContext 物件用來查詢，並將資料儲存至資料庫。 從覆寫 OnClosing 方法接著呼叫 ProductContext 執行個體上的 dispose （） 方法。 程式碼註解提供程式碼所執行的作業的相關詳細資料。

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

    ![1 會形成儲存前](~/ef6/media/form1beforesave.png)

-   在儲存之後產生的存放區索引鍵會顯示在螢幕上。

    ![形成儲存 1 後](~/ef6/media/form1aftersave.png)

-   如果您使用 Code First，則您也會看到所**WinFormswithEFSample.ProductContext**為您建立資料庫。

    ![Server 物件總管](~/ef6/media/serverobjexplorer.png)
