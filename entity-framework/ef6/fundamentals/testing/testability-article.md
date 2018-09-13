---
title: 可測試性和 Entity Framework 4.0
author: divega
ms.date: 10/23/2016
ms.assetid: 9430e2ab-261c-4e8e-8545-2ebc52d7a247
ms.openlocfilehash: 0ddf72ab46e2d67dc8a9cf75cbd40430352c5210
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490528"
---
# <a name="testability-and-entity-framework-40"></a>可測試性和 Entity Framework 4.0
Scott Allen

發行日期： 2010 年

## <a name="introduction"></a>簡介

這份白皮書說明並示範如何撰寫可測試的程式碼使用 ADO.NET Entity Framework 4.0 和 Visual Studio 2010。 這份文件不會嘗試將焦點放在特定的測試方法，例如測試導向設計 (TDD) 或行為導向設計 (BDD)。 改為本文將著重在如何撰寫使用 ADO.NET Entity Framework，但仍然容易隔離，並以自動化方式測試的程式碼。 我們將探討常見的設計模式，方便進行測試中的資料存取案例，並了解如何使用架構時，適用這些模式。 我們也將探討以查看這些功能可以在測試的程式碼中的運作方式的 framework 的特定功能。

## <a name="what-is-testable-code"></a>什麼是可測試的程式碼？

若要確認的軟體使用自動化的單元測試的能力提供許多理想的好處。 大家都知道，很好的測試將會減少軟體缺失，在應用程式，並增加應用程式的品質-但備妥的單元測試及遠遠超過只發現 bug 的數目。

優良的單元測試套件可讓開發小組節省時間，以及完全掌控他們建立的軟體。 小組可以變更現有的程式碼、 重構、 重新設計，並進行重建軟體，以符合新的需求，並同時了解測試套件的應用程式中加入新的元件可以確認應用程式的行為。 單元測試是推動變更和保留的可維護性軟體的複雜性增加時快速意見反應週期的一部分。

單元測試必須付出代價，不過。 小組必須投入的時間來建立與維護單元測試。 直接相關的建立這些測試所需的投入量**testability**的基礎的軟體。 如何輕鬆是測試軟體？ 可測試性記住設計軟體的小組會比使用非可測試軟體的小組更快建立有效的測試。

Microsoft 設計 ADO.NET Entity Framework 4.0 (EF4) 可測試性納入考量。 這並不表示開發人員撰寫針對本身的 framework 程式碼的單元測試。 相反地，可測試性的目標 EF4 讓您輕鬆建立可測試架構上建置的程式碼。 我們看看特定範例之前，或許值得了解可測試的程式碼的品質。

### <a name="the-qualities-of-testable-code"></a>可測試的程式碼的品質

很容易測試的程式碼一律會出現兩個以上的特性。 第一個、 可測試的程式碼很容易**觀察**。 指定輸入的某些設定，它應該很容易觀察所輸出的程式碼。 例如，測試下列方法很容易因為方法會直接傳回計算的結果。

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

測試方法會很困難，如果方法會將計算的值寫入至的網路通訊端、 資料庫資料表或檔案，以類似下列程式碼就行了。 測試必須執行其他工作，以擷取的值。

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

其次，可測試的程式碼很容易**隔離**。 讓我們使用下列虛擬程式碼可測試的程式碼不正確的範例。

``` csharp
    public int ComputePolicyValue(InsurancePolicy policy) {
        using (var connection = new SqlConnection("dbConnection"))
        using (var command = new SqlCommand(query, connection)) {

            // business calculations omitted ...               

            if (totalValue > notificationThreshold) {
                var message = new MailMessage();
                message.Subject = "Warning!";
                var client = new SmtpClient();
                client.Send(message);
            }
        }
        return totalValue;
    }
```

方法很容易觀察 – 可以傳入的保險原則，並確認傳回的值符合預期的結果。 不過，若要測試的方法我們必須要有資料庫，使用正確的結構描述中，安裝和設定 SMTP 伺服器，以防方法會嘗試傳送電子郵件。

單元測試只想要驗證在方法中，計算邏輯，但測試可能會失敗，因為電子郵件伺服器處於離線狀態，或資料庫伺服器已移動。 這兩個這些失敗無關確認要測試的行為。 行為很難隔離。

軟體開發人員致力於撰寫可測試的程式碼通常會致力於維持的重要性分離程式碼中所撰寫。 上述的方法應該專注於商業計算，並委派給其他元件的資料庫與電子郵件實作詳細資料。 Robert C.Martin 會將此稱為 Single Responsibility Principle。 物件，應該將封裝單一、 窄的責任，例如，計算原則的值。 所有其他資料庫和通知工作應該是某個其他物件的責任。 以這種方式撰寫的程式碼會比較容易隔離，因為它著重於單一工作。

在.NET 中，我們有我們需要依照 Single Responsibility Principle，達成隔離的抽象概念。 我們可以使用介面定義，並強制使用抽象介面，而不是具象類型的程式碼。 稍後在本文中我們會看到如何方法，如以上所顯示的錯誤範例可以使用介面*看起來*像是他們會討論到資料庫。 不過，在測試階段，我們可以取代 dummy 的實作不會與資料庫互動，但改為在記憶體中保存資料。 這個假的實作會隔離中的資料存取程式碼或資料庫組態的相關問題的程式碼。

還有其他優點，來隔離。 最後一個方法的商業計算應該只需要幾毫秒就能執行，但測試本身可能會執行幾秒鐘的時間與網路及討論的程式碼躍點到各種伺服器。 單元測試應該執行快速，以便進行小變更。 單元測試應該也是可重複和不失敗，因為測試與無關的元件發生問題。 撰寫程式碼，很容易觀察，並找出可讓您表示開發人員必須方便撰寫程式碼的測試，縮短測試執行時，所等待的時間，更重要的是，較少的時間追蹤不存在的 bug。

希望您能測試的優點，並了解可測試的程式碼表現出品質。 我們要說明如何撰寫程式碼搭配 EF4 將資料儲存至資料庫，同時保有可觀察和容易隔離，但首先我們會縮小討論資料存取的可測試的設計重點。

## <a name="design-patterns-for-data-persistence"></a>資料持續性的設計模式

這兩個稍早顯示的錯誤範例有太多職責。 第一個錯誤範例必須執行計算*和*寫入檔案。 第二個不正確的範例必須從資料庫讀取資料*並*執行的商業計算*和*傳送電子郵件。 藉由設計較小的方法，將重點分開，並委派給其他元件的責任，您要進行許多努力來撰寫可測試的程式碼。 目標是要建置功能，藉由撰寫小而聚焦的抽象概念中的動作。

當它移至資料持續性小而聚焦我們所需的抽象概念很常見它們已記錄在文件做為設計模式。 Martin Fowler 書籍 Patterns of Enterprise Application Architecture 已描述這些模式中列印的第一個工作。 我們會示範如何實作這些 ADO.NET Entity Framework，並配合這些模式之前，我們將提供下列各節中的這些模式的簡短描述。

### <a name="the-repository-pattern"></a>儲存機制模式

Fowler 表示儲存機制 」 之間居中協調的網域和資料使用類似集合的介面來存取網域物件的對應層 」。 儲存機制模式的目標是要隔離程式碼，從資料存取微末節，而我們可了解先前的隔離是針對可測試性所需的特性。

隔離的關鍵在於存放庫如何公開使用的集合型介面的物件。 您要使用的儲存機制中有不知道如何撰寫邏輯存放庫會具體化您要求的物件。 存放庫可能會向資料庫，或它可能只傳回從記憶體中集合的物件。 您的程式碼必須知道的就是存放庫會出現維護集合，並可擷取、 新增和刪除集合中的物件。

在現有.NET 應用程式的具體的存放庫通常繼承自泛型介面，如下所示：

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

我們將變更幾個介面定義當我們 EF4，提供實作，但基本概念維持不變。 程式碼可以使用實作這個介面的具象儲存機制來擷取實體的主索引鍵值，來擷取實體的述詞評估為基礎的集合，或只擷取所有可用的實體。 程式碼也可以新增和移除機構可透過儲存機制介面。

指定的員工 IRepository 物件，程式碼可以執行下列作業。

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

因為程式碼使用介面 （IRepository 的員工），我們可以提供不同的實作介面的程式碼。 一個實作可能會受到 EF4 和保存物件到 Microsoft SQL Server 資料庫實作。 （一個我們在測試期間使用） 的不同實作可能會受到記憶體內的員工清單的物件。 介面可協助達到隔離程式碼中。

請注意 IRepository&lt;T&gt;介面不會公開儲存作業。 我們要如何更新現有的物件？ 您可能會在 IRepository 定義包含儲存作業，與這些儲存機制的實作必須立即將物件保存至資料庫。 不過，許多應用程式中，我們不想要個別保存物件。 相反地，我們想要注入生命的物件，可能是從不同的存放庫，修改這些物件做為一部分的商務活動，然後將所有物件都保存為單一、 不可部分完成作業的一部分。 幸運的是，沒有一個模式，以允許這種類型的行為。

### <a name="the-unit-of-work-pattern"></a>工作單位模式

Fowler 說的工作單位會 「 維持物件的清單受到商務交易，並協調變更寫入和並行處理問題的解決方法 」。 它負責的工作單位，以追蹤變更的物件，我們注入生命，從存放庫，並將保存任何我們告訴工作單位，以認可變更時，我們所做的物件的變更。 它也是工作的所要採取的新物件，我們已新增到所有存放庫，並將物件插入至資料庫，以及管理刪除單位的責任。

如果您曾經做過任何工作與 ADO.NET 資料集然後您就已經很熟悉工作單位模式。 ADO.NET 資料集必須能夠追蹤我們的更新、 刪除和插入 DataRow 物件和無法 （以 TableAdapter 的說明） 調解我們所有的變更至資料庫。 不過，DataSet 物件模型的基礎資料庫已中斷連線的子集。 工作單位模式表現相同的行為，但使用商務物件和資料存取程式碼與隔離和感知的資料庫的網域物件。

模型的.NET 程式碼中的工作單位的抽象概念可能如下所示：

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

藉由公開我們可以確保單一工作單位的工作單位對存放庫的參考物件具有追蹤所有實體具體化期間商務交易的能力。 所有魔力的地方來協調與資料庫的記憶體變更是工作的真實單位的 Commit 方法的實作。 

指定 IUnitOfWork 參考，程式碼可以對從一或多個存放庫擷取商務物件中的變更，並儲存使用不可部分完成的認可作業的所有變更。

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a>延遲的負載模式

Fowler 會使用名稱消極式載入來描述 」 物件不包含的所有資料，您需要但知道如何取得它 」。 透明的消極式載入是一項重要功能有時撰寫可測試的企業程式碼，並使用關聯式資料庫。 例如，請考慮下列程式碼。

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

如何填入工時卡集合？ 有兩個可能的答案。 答案是員工存放庫中，當系統要求擷取員工，發出查詢來擷取這兩個員工及其員工的相關聯的時間記錄卡資訊。 這通常需要使用 JOIN 子句的查詢，並可能會導致擷取比應用程式的詳細資訊的關聯式資料庫中必須。 如果應用程式永遠不需要碰觸的工時卡屬性嗎？

第二個答案是載入 「 依需求 」 的工時卡屬性。 此消極式載入是隱含且透明的商務邏輯，因為程式碼不會叫用特殊的 Api 來擷取時間記錄卡資訊。 程式碼假設存在時所需時間記錄卡資訊。 還有一些魔法通常牽涉到的方法引動過程的執行階段攔截的消極式載入。 攔截程式碼是負責與資料庫通訊，並擷取時間記錄卡資訊，同時又可加以商務邏輯的商務邏輯。 此延遲載入 magic 可讓商務程式碼，將本身從資料擷取作業，會導致更多可測試的程式碼。

消極式載入的缺點是，當應用程式*沒有*需要程式碼會執行其他查詢的時間記錄卡資訊。 這不是問題，對於許多應用程式，但效能敏感應用程式或應用程式循環的 employee 物件的數目和執行查詢期間所要擷取工時卡 （問題通常稱為 「 N + 1 迴圈的每個反覆項目查詢問題），消極式載入是拖曳。 在這些情況下應用程式可能會想要提早中最有效率的方式載入時間記錄卡資訊。

幸運的是，我們會看到如何 EF4 支援這兩個隱含的消極式載入和有效率的積極式載入，因為我們將移至下一節，並實作這些模式。

## <a name="implementing-patterns-with-the-entity-framework"></a>實作與 Entity Framework 的模式

好消息是，最後一節所述的設計模式都容易使用 EF4 實作。 若要示範我們將使用簡單的 ASP.NET MVC 應用程式來編輯，並顯示員工和其相關聯的時間記錄卡資訊。 我們一開始先使用下列 「 純舊 CLR 物件 」 (Poco)。 

``` csharp
    public class Employee {
        public int Id { get; set; }
        public string Name { get; set; }
        public DateTime HireDate { get; set; }
        public ICollection<TimeCard> TimeCards { get; set; }
    }

    public class TimeCard {
        public int Id { get; set; }
        public int Hours { get; set; }
        public DateTime EffectiveDate { get; set; }
    }
```

我們將探討不同的方法和功能 EF4，但其目的是為持續 (PI) 盡可能保留這些類別，這些類別定義會稍有變化。 PI 物件並不知道*如何*，或甚至*如果*，它會保存的狀態存留在資料庫內。 PI 和 Poco 同時兼顧可測試軟體。 使用 POCO 方法的物件是較不受條件約束、 更有彈性且更容易測試，因為它們可以作業不存在的資料庫。

與就地 Poco 我們可以在 Visual Studio 中建立 Entity Data Model (EDM) （請參閱 圖 1）。 我們不會使用 EDM 來為實體產生程式碼。 相反地，我們想要使用我們 lovingly 製作手動的實體。 我們只會使用 EDM 產生資料庫結構描述，並提供 EF4 需要將物件對應至資料庫的中繼資料。

![ef test_01](~/ef6/media/eftest-01.jpg)

**圖 1**

注意： 如果您想要先開發的 EDM 模型，就可以清除，產生在 EDM 中的 POCO 程式碼。 您可以使用 Data Programmability 小組所提供的 Visual Studio 2010 擴充功能來這樣做。 若要下載擴充功能，啟動擴充管理員，從 Visual Studio 中的 [工具] 功能表並搜尋 「 POCO"（請參閱圖 2） 的線上資源庫中的範本。 有數個 POCO 範本，可供使用 EF 的。 如需有關如何使用範本的詳細資訊，請參閱 「[逐步解說： POCO Entity framework 的範本](http://blogs.msdn.com/adonet/pages/walkthrough-poco-template-for-the-entity-framework.aspx)"。

![ef test_02](~/ef6/media/eftest-02.png)

**圖 2**

從這個起點的 POCO 我們將探討兩種不同方法，可測試的程式碼。 第一種方法我呼叫 EF 方法，因為它會利用從 Entity Framework API，來實作的工作和儲存機制的單位的抽象概念。 在第二個方法中我們建立我們自己的自訂儲存機制抽象概念，並接著會看到每一種方法的優缺點。 我們一開始先瀏覽 EF 方法。  

### <a name="an-ef-centric-implementation"></a>EF 中心實作

請考慮下列控制器動作從 ASP.NET MVC 專案。 動作會擷取員工物件，並傳回結果以顯示員工的詳細的檢視。

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

是可測試的程式碼嗎？ 有至少兩個測試，我們需要確認動作的行為。 首先，我們想要確認此動作會傳回正確的檢視 – 簡單的測試。 我們也會想要撰寫測試來確認動作會擷取正確的員工，而且我們想要執行這項操作，但不會執行程式碼來查詢資料庫。 請記住，我們想要隔離受測程式碼。 隔離可確保測試不會因為錯誤中的資料存取程式碼或資料庫組態失敗。 如果測試失敗，我們會知道我們有 bug，在控制器邏輯，而不是在一些較低的層級的系統元件。

若要達到我們需要一些抽象概念，如我們所提供的介面稍早儲存機制和工作單位的隔離。 請記住儲存機制模式設計來作為領域物件和資料對應層之間的橋樑。 在此案例中 EF4*已*對應的資料層，並已提供名為 IObjectSet 像是存放庫的抽象概念&lt;T&gt; （從 System.Data.Objects 命名空間中）。 介面定義看起來如下所示。

``` csharp
    public interface IObjectSet<TEntity> :
                     IQueryable<TEntity>,
                     IEnumerable<TEntity>,
                     IQueryable,
                     IEnumerable
                     where TEntity : class
    {
        void AddObject(TEntity entity);
        void Attach(TEntity entity);
        void DeleteObject(TEntity entity);
        void Detach(TEntity entity);
    }
```

IObjectSet&lt;T&gt;符合存放庫的需求，因為它類似於物件的集合 (透過 IEnumerable&lt;T&gt;)，並提供方法來新增和移除模擬的集合中的物件。 Attach 和 Detach 方法公開 EF4 API 的其他功能。 若要使用 IObjectSet&lt;T&gt;為存放庫的介面中，我們需要的工作抽象單位，將繫結在一起的存放庫。

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

一個具象實作這個介面會討論到 SQL Server，並輕鬆地建立使用 EF4 的 ObjectContext 類別。 ObjectContext 類別會是工作的實際 EF4 API 中單位。

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
        public SqlUnitOfWork() {
            var connectionString =
                ConfigurationManager
                    .ConnectionStrings[ConnectionStringName]
                    .ConnectionString;
            _context = new ObjectContext(connectionString);
        }

        public IObjectSet<Employee> Employees {
            get { return _context.CreateObjectSet<Employee>(); }
        }

        public IObjectSet<TimeCard> TimeCards {
            get { return _context.CreateObjectSet<TimeCard>(); }
        }

        public void Commit() {
            _context.SaveChanges();
        }

        readonly ObjectContext _context;
        const string ConnectionStringName = "EmployeeDataModelContainer";
    }
```

將 IObjectSet&lt;T&gt;化為現實是，只要叫用的 ObjectContext 物件 CreateObjectSet 方法。 在幕後，架構會使用中繼資料我們提供在 EDM 中，以產生實體的 ObjectSet&lt;T&gt;。 我們將著重於與傳回 IObjectSet&lt;T&gt;介面，因為它可協助保留在用戶端程式碼中的可測試性。

此具象實作可用於生產環境中，但我們要專注於如何我們將使用我們 IUnitOfWork 抽象概念，以利於進行測試。

### <a name="the-test-doubles"></a>測試替身

若要隔離的控制器動作，我們必須能夠實際工作單位的 （受 ObjectContext） 和工作 （執行於記憶體中作業） 的測試 double 或 「 假 」 單位之間切換。 常見的方法來執行這種類型的切換，就是工作的工作的不讓具現化單位，而是工作的工作的到控制器做為建構函式參數傳遞單位的 MVC 控制器。

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

上述程式碼是相依性插入的範例。 不允許建立它的相依性 （工作單位），但控制器中插入相依性的控制站。 在 MVC 專案中常會使用逆轉控制 (IoC) 容器中的組合中的自訂控制器 factory，來自動化相依性插入。 這些主題已超出本文討論的範圍，但您可以更藉由閱讀下列這篇文章結尾處的參考。

我們可以用來測試的工作實作的假單位看起來可能如下所示。

``` csharp
    public class InMemoryUnitOfWork : IUnitOfWork {
        public InMemoryUnitOfWork() {
            Committed = false;
        }
        public IObjectSet<Employee> Employees {
            get;
            set;
        }

        public IObjectSet<TimeCard> TimeCards {
            get;
            set;
        }

        public bool Committed { get; set; }
        public void Commit() {
            Committed = true;
        }
    }
```

請注意假工作單位的公開認可屬性。 它有時適合將功能新增至一個假的類別，方便進行測試。 在此情況下，它很容易觀察是否程式碼會藉由檢查認可屬性認可的工作單位。

我們也需要假 IObjectSet&lt;T&gt;保留在記憶體中的員工和工時卡物件。 我們可以提供使用泛型的單一實作。

``` csharp
    public class InMemoryObjectSet<T> : IObjectSet<T> where T : class
        public InMemoryObjectSet()
            : this(Enumerable.Empty<T>()) {
        }
        public InMemoryObjectSet(IEnumerable<T> entities) {
            _set = new HashSet<T>();
            foreach (var entity in entities) {
                _set.Add(entity);
            }
            _queryableSet = _set.AsQueryable();
        }
        public void AddObject(T entity) {
            _set.Add(entity);
        }
        public void Attach(T entity) {
            _set.Add(entity);
        }
        public void DeleteObject(T entity) {
            _set.Remove(entity);
        }
        public void Detach(T entity) {
            _set.Remove(entity);
        }
        public Type ElementType {
            get { return _queryableSet.ElementType; }
        }
        public Expression Expression {
            get { return _queryableSet.Expression; }
        }
        public IQueryProvider Provider {
            get { return _queryableSet.Provider; }
        }
        public IEnumerator<T> GetEnumerator() {
            return _set.GetEnumerator();
        }
        IEnumerator IEnumerable.GetEnumerator() {
            return GetEnumerator();
        }

        readonly HashSet<T> _set;
        readonly IQueryable<T> _queryableSet;
    }
```

這個測試替身委派 (delegate) 大部分的工作到基礎的 HashSet&lt;T&gt;物件。 請注意該 IObjectSet&lt;T&gt;需要泛型條件約束，強制使用 T 做為類別 （參考型別），而且也會迫使我們實作 IQueryable&lt;T&gt;。 就可以輕鬆地進行記憶體中的集合會顯示為 IQueryable&lt;T&gt;使用標準的 LINQ 運算子 AsQueryable。

### <a name="the-tests"></a>測試

傳統的單元測試會使用單一測試類別，可保存的所有動作的測試的所有單一的 MVC 控制器中。 我們可以撰寫這些測試或任何類型的單元測試時，使用記憶體中 fakes 我們所建置。 不過，本文中我們會避免在整合測試類別方法和改為群組我們將焦點放在特定功能的測試。  比方說，「 建立新的員工 」 可能是我們想要測試，因此我們會使用單一測試類別，以確認負責建立新的員工的單一控制器動作的功能。

沒有需所有這些更細緻的測試類別的一些常見設定程式碼。 比方說，我們總是需要建立我們的記憶體中存放庫和假的工作單位。 我們也會需要具有假單位的插入工作的員工控制站的執行個體。 我們將使用的基底類別測試類別上的檔案，以共用這個常見的安裝程式碼。

``` csharp
    public class EmployeeControllerTestBase {
        public EmployeeControllerTestBase() {
            _employeeData = EmployeeObjectMother.CreateEmployees()
                                                .ToList();
            _repository = new InMemoryObjectSet<Employee>(_employeeData);
            _unitOfWork = new InMemoryUnitOfWork();
            _unitOfWork.Employees = _repository;
            _controller = new EmployeeController(_unitOfWork);
        }

        protected IList<Employee> _employeeData;
        protected EmployeeController _controller;
        protected InMemoryObjectSet<Employee> _repository;
        protected InMemoryUnitOfWork _unitOfWork;
    }
```

「 物件發明 「 我們使用的基底類別是一個常見的模式，來建立測試資料。 物件母親包含跨多個測試裝置，使用的測試實體具現化的 factory 方法。

``` csharp
    public static class EmployeeObjectMother {
        public static IEnumerable<Employee> CreateEmployees() {
            yield return new Employee() {
                Id = 1, Name = "Scott", HireDate=new DateTime(2002, 1, 1)
            };
            yield return new Employee() {
                Id = 2, Name = "Poonam", HireDate=new DateTime(2001, 1, 1)
            };
            yield return new Employee() {
                Id = 3, Name = "Simon", HireDate=new DateTime(2008, 1, 1)
            };
        }
        // ... more fake data for different scenarios
    }
```

我們可以使用的基底類別的 EmployeeControllerTestBase 數目 （請參閱 圖 3） 的測試裝置。 每個測試固件會測試特定的控制器動作。 例如，一個測試固件將著重在測試期間 （以顯示用於建立員工檢視） 的 HTTP GET 要求，使用 [建立] 動作，而不同的裝置將著重在使用 HTTP POST 要求中的 [建立] 動作 (才會提交的資訊使用者若要建立某位員工）。 每個衍生的類別負責只在特定的內容中，並提供驗證結果，針對其特定測試的內容所需的判斷提示所需的設定。

![ef test_03](~/ef6/media/eftest-03.png)

**圖 3**

此處所提供的命名慣例和測試樣式不需要再提供可測試的程式碼 – 這是只有一個方法。 [圖 4] 顯示在 Jet 大腦 Resharper 中執行的測試會測試執行器外掛程式適用於 Visual Studio 2010。

![ef test_04](~/ef6/media/eftest-04.png)

**圖 4**

使用基底類別，來處理共用的設定程式碼，每個控制器動作的單元測試是小型且容易撰寫。 測試會執行快速 （因為我們要執行的記憶體內作業），和不應因不相關的基礎結構或帶來了環保問題而失敗，（因為我們已隔離受測試的單位）。

``` csharp
    [TestClass]
    public class EmployeeControllerCreateActionPostTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldAddNewEmployeeToRepository() {
            _controller.Create(_newEmployee);
            Assert.IsTrue(_repository.Contains(_newEmployee));
        }
        [TestMethod]
        public void ShouldCommitUnitOfWork() {
            _controller.Create(_newEmployee);
            Assert.IsTrue(_unitOfWork.Committed);
        }
        // ... more tests

        Employee _newEmployee = new Employee() {
            Name = "NEW EMPLOYEE",
            HireDate = new System.DateTime(2010, 1, 1)
        };
    }
```

在這些測試中，基底類別會執行大部分的設定工作。 請記住，基底類別建構函式會建立於記憶體中存放庫、 假的工作單位，以及 EmployeeController 類別的執行個體。 測試類別衍生自這個基底類別，並著重於測試的 Create 方法的細節。 在此情況下細節歸結為任何單元測試程序中，您會看到 「 排列，採取行動，並判斷提示 」 的步驟：

-   建立 newEmployee 物件來模擬內送資料。
-   叫用的 EmployeeController 的 [建立] 動作，並傳入 newEmployee。
-   確認 [建立] 動作會產生預期的結果 （顯示存放庫中的員工）。

我們所建置，可讓我們來測試任何 EmployeeController 動作。 比方說，當我們撰寫測試員工控制器的 Index 動作時我們可以繼承自測試基底類別，以建立相同的基底安裝程式進行測試。 一次的基底類別會建立於記憶體中存放庫、 假的工作單位和 EmployeeController 的執行個體。 將焦點放在叫用的索引動作只需要索引動作的測試，並測試模型之動作的品質傳回。

``` csharp
    [TestClass]
    public class EmployeeControllerIndexActionTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldBuildModelWithAllEmployees() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                          as IEnumerable<Employee>;
            Assert.IsTrue(model.Count() == _employeeData.Count);
        }
        [TestMethod]
        public void ShouldOrderModelByHiredateAscending() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                         as IEnumerable<Employee>;
            Assert.IsTrue(model.SequenceEqual(
                           _employeeData.OrderBy(e => e.HireDate)));
        }
        // ...
    }
```

我們會建立使用記憶體中的 fakes 測試都是測試導向*狀態*的軟體。 例如，測試建立動作，我們想要建立動作執行 – 之後，請檢查存放庫的狀態時沒有儲存機制保存新進員工？

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

稍後我們將探討測試架構的互動。 如果受測程式碼叫用我們的物件上適當的方法，並傳遞正確的參數，則會要求基礎的互動測試。 現在我們將繼續在封面上另一種設計模式-消極式載入。

## <a name="eager-loading-and-lazy-loading"></a>積極式載入和消極式載入

在某個時間點，在 ASP.NET MVC web 應用程式，我們可能會想要顯示員工的資訊，並包含員工的相關聯的工時卡。 比方說，我們可能會有時間記錄卡的摘要顯示系統中顯示員工的名稱和執行總數的工時卡。 有數種方法，我們可以拿來實作這項功能。

### <a name="projection"></a>Projection

一個簡單的方法，建立摘要是以構建模型專屬於我們想要在檢視中顯示的資訊。 在此案例中模型看起來可能如下所示。

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

請注意，EmployeeSummaryViewModel 不是實體，亦即它不是我們想要保存在資料庫中。 我們只有要使用這個類別來檢視隨機處理資料，以強型別的方式。 檢視模型是資料傳輸物件 (DTO)，因為它不包含的任何行為 （沒有任何方法） – 只有屬性。 屬性會保留我們需要移動的資料。 它很容易就能具現化此使用 LINQ 的標準投射運算子 – Select 運算子的檢視模型。

``` csharp
    public ViewResult Summary(int id) {
        var model = _unitOfWork.Employees
                               .Where(e => e.Id == id)
                               .Select(e => new EmployeeSummaryViewModel
                                  {
                                    Name = e.Name,
                                    TotalTimeCards = e.TimeCards.Count()
                                  })
                               .Single();
        return View(model);
    }
```

有兩個值得注意的功能，上述程式碼。 第一次 – 程式碼很容易測試，因為仍然容易觀察和隔離。 Select 運算子對我們的記憶體中 fakes 也一樣好，其方式就如同針對真實的工作單元。

``` csharp
    [TestClass]
    public class EmployeeControllerSummaryActionTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldBuildModelWithCorrectEmployeeSummary() {
            var id = 1;
            var result = _controller.Summary(id);
            var model = result.ViewData.Model as EmployeeSummaryViewModel;
            Assert.IsTrue(model.TotalTimeCards == 3);
        }
        // ...
    }
```

第二個值得注意的功能是如何將程式碼讓 EF4 來產生單一、 有效率的查詢，以組合在一起的員工和時間記錄卡資訊。 我們已經將載入員工資訊及時間記錄卡資訊相同的物件而不使用任何特殊的 Api。 程式碼只被表示它需要使用標準的 LINQ 運算子，針對記憶體中的資料來源，以及遠端資料來源的資訊。 EF4 無法轉譯 LINQ 查詢與 C 所產生的運算式樹狀架構\#成單一且有效率的 T-SQL 查詢的編譯器。

``` SQL
    SELECT
    [Limit1].[Id] AS [Id],
    [Limit1].[Name] AS [Name],
    [Limit1].[C1] AS [C1]
    FROM (SELECT TOP (2)
      [Project1].[Id] AS [Id],
      [Project1].[Name] AS [Name],
      [Project1].[C1] AS [C1]
      FROM (SELECT
        [Extent1].[Id] AS [Id],
        [Extent1].[Name] AS [Name],
        (SELECT COUNT(1) AS [A1]
         FROM [dbo].[TimeCards] AS [Extent2]
         WHERE [Extent1].[Id] =
               [Extent2].[EmployeeTimeCard_TimeCard_Id]) AS [C1]
              FROM [dbo].[Employees] AS [Extent1]
               WHERE [Extent1].[Id] = @p__linq__0
         )  AS [Project1]
    )  AS [Limit1]
```

有其他的時候，我們不想要檢視模型或 DTO 物件，但實際的實體。 當我們知道我們必須員工*和*員工的工時卡，我們積極可以載入相關的資料，以不干擾且有效率的方式。

### <a name="explicit-eager-loading"></a>明確的積極式載入

當我們想要提早載入相關的實體的資訊，我們需要某種機制，商務邏輯 （或在此案例中，控制器動作邏輯） 來表示其想要儲存機制。 EF4 ObjectQuery&lt;T&gt;類別會定義包含方法，以指定要在查詢期間擷取的相關的物件。 請記住 EF4 ObjectContext 會公開實體透過實體的 ObjectSet&lt;T&gt;類別繼承自 ObjectQuery&lt;T&gt;。  如果我們使用 ObjectSet&lt;T&gt;我們控制器動作，我們可以撰寫下列程式碼中的參考指定的時間記錄卡資訊對於每個員工的積極式載入。

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

不過，由於我們想要保留我們的程式碼可測試我們都不會公開 ObjectSet&lt;T&gt;從外部工作類別的實際單位。 相反地，我們依賴 IObjectSet&lt;T&gt;介面，也就是您更輕鬆地假的但 IObjectSet&lt;T&gt;不會定義包含方法。 LINQ 的優點是，我們可以建立自己的 Include 運算子。

``` csharp
    public static class QueryableExtensions {
        public static IQueryable<T> Include<T>
                (this IQueryable<T> sequence, string path) {
            var objectQuery = sequence as ObjectQuery<T>;
            if(objectQuery != null)
            {
                return objectQuery.Include(path);
            }
            return sequence;
        }
    }
```

請注意包含定義這個運算子作為擴充方法的 IQueryable&lt;T&gt;而不是 IObjectSet&lt;T&gt;。 這讓我們能夠使用方法與更廣泛的可能的類型，包括 IQueryable&lt;T&gt;，IObjectSet&lt;T&gt;，ObjectQuery&lt;T&gt;，和 ObjectSet&lt;T&gt;。 萬一基礎序列不是正版的 EF4 ObjectQuery&lt;T&gt;，則沒有完成沒有壞處，且包含運算子會執行任何作業。 如果基礎序列*已*ObjectQuery&lt;T&gt; (或衍生自 ObjectQuery&lt;T&gt;)，則 EF4 會看到我們的需求，其他的資料，並制定適當的 SQL查詢。

以此新的運算子，就地我們可以明確要求時間記錄卡資訊的積極式的載入從儲存機制。

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

當執行實際的 ObjectContext，程式碼就會產生下列的單一查詢。 查詢會從一次用來將 employee 物件具體化和完整填入其 [工時] 屬性中的資料庫收集足夠的資訊。

``` SQL
    SELECT
    [Project1].[Id] AS [Id],
    [Project1].[Name] AS [Name],
    [Project1].[HireDate] AS [HireDate],
    [Project1].[C1] AS [C1],
    [Project1].[Id1] AS [Id1],
    [Project1].[Hours] AS [Hours],
    [Project1].[EffectiveDate] AS [EffectiveDate],
    [Project1].[EmployeeTimeCard_TimeCard_Id] AS [EmployeeTimeCard_TimeCard_Id]
    FROM ( SELECT
         [Extent1].[Id] AS [Id],
         [Extent1].[Name] AS [Name],
         [Extent1].[HireDate] AS [HireDate],
         [Extent2].[Id] AS [Id1],
         [Extent2].[Hours] AS [Hours],
         [Extent2].[EffectiveDate] AS [EffectiveDate],
         [Extent2].[EmployeeTimeCard_TimeCard_Id] AS
                    [EmployeeTimeCard_TimeCard_Id],
         CASE WHEN ([Extent2].[Id] IS NULL) THEN CAST(NULL AS int)
         ELSE 1 END AS [C1]
         FROM  [dbo].[Employees] AS [Extent1]
         LEFT OUTER JOIN [dbo].[TimeCards] AS [Extent2] ON [Extent1].[Id] = [Extent2].[EmployeeTimeCard_TimeCard_Id]
    )  AS [Project1]
    ORDER BY [Project1].[HireDate] ASC,
             [Project1].[Id] ASC, [Project1].[C1] ASC
```

好消息是動作方法內的程式碼會保留完整測試。 我們不需要提供任何額外的功能，如我們 fakes 以支援包含運算子。 壞消息是我們必須使用包含運算子內的程式碼，我們想要將非持續性的持續性。 這是您要建置可測試的程式碼時評估的權衡取捨的型別其基本範例。 有些的時候，當您需要讓外部儲存機制抽象概念，以符合效能目標的持續性考量流失。

積極式載入的替代方案是消極式載入。 我們執行消極式載入方式*不*需要我們的企業程式碼，明確地宣布的需求相關聯的資料。 相反地，我們使用應用程式中的實體，以及額外的資料是否需要 Entity Framework 會將資料依需求載入。

### <a name="lazy-loading"></a>消極式載入

它很容易想像一下，我們不知道需要什麼資料的商務邏輯。 我們可能會知道邏輯需要 employee 物件，但我們可能會分支到不同的執行路徑，其中部分這些路徑需要時間記錄卡資訊從員工，但某些不。 這種情況非常適合做為隱含消極式載入，因為神奇地出現在可視需要的資料。

消極式載入，也就是延後載入，並置於一些需求實體物件。 使用，則為 true 的永續性無知的 Poco 中不會面臨永續層，從任何需求，但是，則為 true 的持續性無知的幾乎不可能達成。  相反地，我們會測量持續性無知的相對度數。 它會很不幸，如果我們需要從導向的持續性的基底類別繼承，或是使用特製化的集合，以達到在 Poco 的消極式載入。 幸運的是，EF4 具有干擾性較低的解決方案。

### <a name="virtually-undetectable"></a>幾乎無法偵測到

當使用 POCO 物件，EF4 可以動態產生之實體的執行階段 proxy。 這些 proxy 無形的方式包裝具體化的 Poco 提供的攔截每個屬性的其他服務取得和設定作業來執行其他工作。 這類服務是我們所需的消極式載入功能。 另一個服務是高效率變更追蹤機制可以記錄程式變更實體的屬性值時。 保存任何已修改之實體使用 UPDATE 命令變更的清單是 objectcontext 使用 SaveChanges 方法期間。

這些 proxy 運作，不過，他們需要要連結到屬性 get 的方法，而且實體和 proxy 上的設定作業達成此目標，藉由覆寫虛擬成員。 因此，如果我們想要有隱含的消極式載入和高效率變更追蹤我們需要回到我們的 POCO 類別定義，並將標示為虛擬屬性。

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

我們仍然可以說 「 員工 」 實體是大部分持續。 唯一的需求是要使用的虛擬成員，這不會影響程式碼可測試性。 我們不需要衍生自任何特殊的基底類別，或甚至使用特殊的集合，專用於消極式載入。 因為程式碼示範，任何類別實作 ICollection&lt;T&gt;可保存相關的實體。

另外還有我們得先讓我們的工作單位內的其中一個次要變更。 消極式載入會*關閉*依預設，當直接使用為 ObjectContext 物件。 沒有的屬性，我們可以在屬性設定 ContextOptions 啟用延後載入，而且我們可以在我們真正的工作單位，設定此屬性，如果我們想要啟用消極式載入所有位置。

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
         public SqlUnitOfWork() {
             // ...
             _context = new ObjectContext(connectionString);
             _context.ContextOptions.LazyLoadingEnabled = true;
         }    
         // ...
     }
```

啟用隱含的消極式載入，應用程式程式碼可以使用員工與員工的工時卡同時保有 ef 將額外的資料所需的工作不知道。

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

消極式載入，可讓應用程式程式碼更容易撰寫，並使用 proxy 神奇的程式碼保持完全可測試性。 記憶體中的工作單位的 fakes 可以只預先載入假的實體與相關聯的資料，需要在測試期間時。

現在我們將在其中將注意力轉移無法建立存放庫使用 IObjectSet&lt;T&gt;並查看 隱藏所有符號的持續性架構的抽象概念。

## <a name="custom-repositories"></a>自訂存放庫

當我們在本文中，先介紹工作單位設計模式時我們提供一些範例程式碼的工作單位的外觀。 讓我們重新顯示這個使用員工和員工時間記錄卡案例，我們已使用原始的想法。

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

此工作單位的與我們在上一節中建立的工作單位的主要差異是此工作單位的不使用任何從 EF4 framework 的抽象概念的方式 (沒有任何 IObjectSet&lt;T&gt;)。 IObjectSet&lt;T&gt;以及儲存機制介面，但它會公開 API 的運作方式可能不完全對齊與我們的應用程式的需求。 在此即將推出的方法中，我們將代表存放庫使用自訂 IRepository&lt;T&gt;抽象概念。

許多開發人員測試為導向的設計、 行為導向設計，以及網域導向方法設計偏好 IRepository&lt;T&gt;原因有幾種方法。 首先，IRepository&lt;T&gt;介面代表 「 防損毀 」 層。 中所述 Eric evans 網域導向設計著作防損毀層會保留您的網域程式碼離開的基礎結構的 Api，例如持續性 API。 其次，開發人員可以建置符合應用程式的確切需求 （如撰寫測試時，所發現） 存放庫的方法。 比方說，我們可能會經常需要找出使用識別碼值，因此我們可以將 FindById 方法新增至存放庫介面的單一實體。  我們 IRepository&lt;T&gt;定義看起來如下所示。

``` csharp
    public interface IRepository<T>
                    where T : class, IEntity {
        IQueryable<T> FindAll();
        IQueryable<T> FindWhere(Expression<Func\<T, bool>> predicate);
        T FindById(int id);       
        void Add(T newEntity);
        void Remove(T entity);
    }
```

請注意，我們會將 IQueryable 重新置放&lt;T&gt;介面來公開 （expose） 實體集合。 IQueryable&lt;T&gt;允許流入 EF4 提供者，並讓提供者查詢的整體檢視的 LINQ 運算式樹狀架構。 第二個選項是以傳回 IEnumerable&lt;T&gt;，這表示 EF4 LINQ 提供者只會看到建立的儲存機制內的運算式。 任何的分組、 排序和儲存機制之外所做的預測將不被納入 SQL 命令傳送至資料庫，可能會損及效能。 另一方面，儲存機制傳回只 IEnumerable&lt;T&gt;結果將永遠不會令您吃驚使用新的 SQL 命令。 這兩種方法將會運作，且這兩種方式保持可測試。

很容易提供單一實作 IRepository&lt;T&gt;介面使用泛型和 EF4 ObjectContext API。

``` csharp
    public class SqlRepository<T> : IRepository<T>
                                    where T : class, IEntity {
        public SqlRepository(ObjectContext context) {
            _objectSet = context.CreateObjectSet<T>();
        }
        public IQueryable<T> FindAll() {
            return _objectSet;
        }
        public IQueryable<T> FindWhere(
                               Expression<Func\<T, bool>> predicate) {
            return _objectSet.Where(predicate);
        }
        public T FindById(int id) {
            return _objectSet.Single(o => o.Id == id);
        }
        public void Add(T newEntity) {
            _objectSet.AddObject(newEntity);
        }
        public void Remove(T entity) {
            _objectSet.DeleteObject(entity);
        }
        protected ObjectSet<T> _objectSet;
    }
```

IRepository&lt;T&gt;方法會提供一些額外的控制，透過我們的查詢因為用戶端可叫用方法，以回到實體。 在方法中，我們可以提供額外的檢查和強制執行應用程式的條件約束的 LINQ 運算子。 請注意，此介面有兩個條件約束的泛型型別參數上。 第一個條件約束是類別缺點，誤導所需的 ObjectSet&lt;T&gt;，並在第二個條件約束強制實作 IEntity – 建立應用程式的抽象概念實體。 IEntity 介面會強迫實體必須有可讀取的 Id 屬性，以及我們接著可以使用這個屬性在 FindById 方法。 IEntity 被定義為下列程式碼。

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

因為我們的實體都必須實作這個介面，IEntity 可視為持續性無知的小型違規情形。 請記住永續性無知即將權衡取捨，而且許多 FindById 功能將勝過所需的介面所加諸的條件約束。 介面會將不會影響對可測試性。

具現化即時 IRepository&lt;T&gt;需要 EF4 ObjectContext，所以工作實作的具象單位應該管理的具現化。

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
        public SqlUnitOfWork() {
            var connectionString =
                ConfigurationManager
                    .ConnectionStrings[ConnectionStringName]
                    .ConnectionString;

            _context = new ObjectContext(connectionString);
            _context.ContextOptions.LazyLoadingEnabled = true;
        }

        public IRepository<Employee> Employees {
            get {
                if (_employees == null) {
                    _employees = new SqlRepository<Employee>(_context);
                }
                return _employees;
            }
        }

        public IRepository<TimeCard> TimeCards {
            get {
                if (_timeCards == null) {
                    _timeCards = new SqlRepository<TimeCard>(_context);
                }
                return _timeCards;
            }
        }

        public void Commit() {
            _context.SaveChanges();
        }

        SqlRepository<Employee> _employees = null;
        SqlRepository<TimeCard> _timeCards = null;
        readonly ObjectContext _context;
        const string ConnectionStringName = "EmployeeDataModelContainer";
    }
```

### <a name="using-the-custom-repository"></a>使用自訂存放庫

使用我們的自訂存放庫並不是明顯不同於使用存放庫根據 IObjectSet&lt;T&gt;。 而不是直接對屬性套用 LINQ 運算子，我們必須先叫用其中一個存放庫的方法，擷取 IQueryable&lt;T&gt;參考。

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

請注意我們在先前所實作的自訂包含運算子將運作而不需要變更。 存放庫的 FindById 方法會移除重複的邏輯，從想要擷取單一實體的動作。

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

沒有任何重大的差異，我們已討論過的兩種方法的 testability 中。 我們可以提供假的實作的 IRepository&lt;T&gt;藉由建置具象類別受到 HashSet&lt;員工&gt;-就像我們在上一節。 不過，有些開發人員會想要使用模擬 （mock） 物件和模擬物件架構，而不要建立 fakes。 我們將探討測試我們的實作，並模擬 （mock） 和 fakes 之間的差異下, 一節將討論使用模擬 （mock）。

### <a name="testing-with-mocks"></a>使用模擬 （mock） 進行測試

有不同的方法，若要建置哪些 Martin Fowler 呼叫 「 測試替身 」。 測試替身 （例如 double 的電影 stunt) 是建置 」，就能 「 實際測試期間的實際執行物件的物件。 我們建立記憶體中存放庫是與 SQL Server 的存放庫的測試替身。 我們已了解如何使用這些測試替身在單元測試期間隔離程式碼，並保留快速執行的測試。

我們已建置的測試替身有真實的工作的實作。 在幕後，每個儲存具象集合的物件，並將新增及從此集合移除物件，因為我們在測試期間操作存放庫。 有些開發人員想要建置其測試替身，請在這種方式 – 真正的程式碼與工作實作。  這些測試雙精度浮點數是我們所謂*fakes*。 它們有工作的實作，但是並不實際生產環境中使用。 假的存放庫實際上不會寫入資料庫。 假的 SMTP 伺服器實際上不會透過網路傳送的電子郵件訊息。

### <a name="mocks-versus-fakes"></a>與 Fakes 的模擬 （mock)

還有另一種稱為雙精確度的測試*模擬*。 雖然 fakes 工作實作，mock 就派沒有實作。 透過模擬物件架構的協助，我們會在執行階段建構這些模擬 （mock） 物件，並將其當做測試替身。 在本節中，我們將使用模擬 (mock) 架構 Moq 開放原始碼。 以下是使用 Moq 動態建立測試替身員工儲存機制的簡單範例。

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

我們會要求 IRepository Moq&lt;員工&gt;實作並建置一個動態。 我們可以讓物件實作 IRepository&lt;員工&gt;藉由存取物件屬性的 Mock&lt;T&gt;物件。 它是我們可以將它傳遞至我們的控制站，這個內部物件，而且不會知道如果這是測試替身或實際的儲存機制。 我們可以叫用物件上的方法，就像我們會叫用的實際實作的物件上的方法。

您必須想知道什麼模擬儲存機制時一樣我們叫用 Add 方法。 因為沒有實作的模擬 （mock） 物件後方，加入任何作用。 像我們使用我們所撰寫，fakes 讓員工會被捨棄，沒有任何在幕後的具象集合。 傳回值的 FindById 呢？ 在此情況下模擬 （mock） 物件沒有的唯一可以進行操作，也就是傳回的預設值。 因為我們會傳回參考型別 （員工），則傳回的值會是 null 值。

模擬 （mock） 可能看似毫無價值;不過，有兩個我們未討論到的模擬 （mock） 的多個功能。 首先，Moq framework 記錄進行模擬 （mock） 物件上的所有呼叫。 稍後在程式碼中，我們可以要求 Moq 任何人都叫用 Add 方法，則任何人都叫用 FindById 方法。 我們會看到我們可以在稍後如何在測試中使用此 「 黑箱作業 」 錄製功能。

第二個很棒的功能是如何以程式設計的模擬 （mock） 物件使用 Moq*期望*。 預期會告訴模擬 （mock） 物件如何回應任何給定的互動。 比方說，我們可以預期的情況下藉由程式設計納入我們的 mock，並告訴它有人會叫用 FindById 時，傳回將 employee 物件。 Moq 架構會使用安裝程式 API 和 lambda 運算式程式這些期待。

``` csharp
    [TestMethod]
    public void MockSample() {
        Mock<IRepository<Employee>> mock =
            new Mock<IRepository<Employee>>();
        mock.Setup(m => m.FindById(5))
            .Returns(new Employee {Id = 5});
        IRepository<Employee> repository = mock.Object;
        var employee = repository.FindById(5);
        Assert.IsTrue(employee.Id == 5);
    }
```

在此範例中，我們要求以動態方式建立儲存機制，Moq，然後我們進行程式設計的人的存放庫。 預期會告訴某人叫用傳遞的值為 5 FindById 方法時，傳回的 Id 值為 5 的新員工物件的模擬 （mock） 物件。 這項測試通過，而且我們不需要建置完整的實作，以假 IRepository&lt;T&gt;。

讓我們再次瀏覽我們稍早撰寫的測試，並修改他們使用模擬 （mock），而不是 fakes。 就像之前，我們將使用的基底類別來設定所需的所有控制器的測試基礎結構的常見項目。

``` csharp
    public class EmployeeControllerTestBase {
        public EmployeeControllerTestBase() {
            _employeeData = EmployeeObjectMother.CreateEmployees()
                                                .AsQueryable();
            _repository = new Mock<IRepository<Employee>>();
            _unitOfWork = new Mock<IUnitOfWork>();
            _unitOfWork.Setup(u => u.Employees)
                       .Returns(_repository.Object);
            _controller = new EmployeeController(_unitOfWork.Object);
        }

        protected IQueryable<Employee> _employeeData;
        protected Mock<IUnitOfWork> _unitOfWork;
        protected EmployeeController _controller;
        protected Mock<IRepository<Employee>> _repository;
    }
```

設定程式碼大致保持相同。 而不是使用 fakes，我們將使用 Moq 建構模擬 （mock） 物件。 基底類別排列的模擬 （mock） 的工作單位時要傳回的模擬 （mock） 的存放庫的程式碼會叫用 [員工] 屬性。 模擬 （mock） 的安裝程式的其餘部分就會專用於每個特定案例的測試裝置內進行。 比方說，測試固件，索引動作會設定模擬儲存機制時動作會叫用模擬的儲存機制的 FindAll 方法傳回的員工清單。

``` csharp
    [TestClass]
    public class EmployeeControllerIndexActionTests
               : EmployeeControllerTestBase {
        public EmployeeControllerIndexActionTests() {
            _repository.Setup(r => r.FindAll())
                        .Returns(_employeeData);
        }
        // .. tests
        [TestMethod]
        public void ShouldBuildModelWithAllEmployees() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                          as IEnumerable<Employee>;
            Assert.IsTrue(model.Count() == _employeeData.Count());
        }
        // .. and more tests
    }
```

除了期望，我們的測試看起來像我們之前的測試。 不過，記錄的能力，模擬 （mock） 架構的我們可以方法測試從不同的角度。 我們將探討這個新的檢視方塊下, 一節。

### <a name="state-versus-interaction-testing"></a>與互動的測試狀態

有不同的技術，可用來測試軟體與模擬 （mock） 物件。 其中一個方法是使用狀態為基礎的測試，這是我們已經在這份文件中到目前為止。 狀態為基礎的軟體狀態相關的測試會判斷提示。 在最後一項測試中我們叫用動作方法的控制站上進行它應該會建置有關模型的判斷提示。 以下是一些其他的測試狀態的範例：

-   確認存放庫建立執行之後，包含 新的 員工 物件。
-   請確認索引執行之後，模型會保留一份所有員工。
-   確認存放庫未包含特定的員工，刪除執行之後。

您會看到與模擬 （mock） 物件的另一種方法是確認*互動*。 當狀態為基礎的物件狀態有關的測試會判斷提示時，互動以有關物件如何互動的測試會判斷提示。 例如: 

-   請確認控制器在建立執行時，會叫用儲存機制中的 Add 方法。
-   請確認控制器在索引執行時，會叫用儲存機制的 FindAll 方法。
-   請確認控制站會叫用的單位工作的 Commit 方法，以編輯執行時，儲存變更。

測試互動通常需要較少的測試資料，因為我們不在集合內閒晃，並驗證計數。 例如，如果我們知道的詳細資料動作會叫用的儲存機制 FindById 方法，使用正確的值-然後動作的可能行為正確無誤。 我們可以確認這種行為，而不需設定 FindById 從傳回的任何測試資料。

``` csharp
    [TestClass]
    public class EmployeeControllerDetailsActionTests
               : EmployeeControllerTestBase {
         // ...
        [TestMethod]
        public void ShouldInvokeRepositoryToFindEmployee() {
            var result = _controller.Details(_detailsId);
            _repository.Verify(r => r.FindById(_detailsId));
        }
        int _detailsId = 1;
    }
```

在上述的測試固件所需的唯一設定是基底類別所提供的安裝程式。 當我們叫用控制器動作時，Moq 會記錄與模擬 （mock） 的存放庫互動。 使用驗證 API 的 Moq，我們可以詢問 Moq 是否控制器叫用 FindById 適當的識別碼值。 如果控制器未叫用方法，或叫用具有非預期的參數值的方法，驗證方法會擲回例外狀況，而且測試會失敗。

以下是工作的另一個範例若要確認目前單位上建立動作叫用的認可。

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

一個危險互動測試是透過指定互動傾向。 記錄，並驗證與模擬 （mock） 物件的每個互動並不表示測試模擬 （mock） 物件的功能應該嘗試確認每個互動。 某些互動是實作詳細資料，以及您應該只驗證互動*必要*滿足目前的測試。

模擬 （mock） 或 fakes 之間選擇主要取決於您要測試的系統和您的個人 （或小組） 喜好設定。 模擬 （mock） 物件可大幅減少您需要實作測試替身的程式碼數量，但並非所有人已熟悉程式設計期望和驗證互動。

## <a name="conclusions"></a>結論

在這份文件中，我們已示範了數種方法來建立可測試的程式碼時使用 ADO.NET Entity Framework，以保存資料。 我們可以利用內建的抽象概念，例如 IObjectSet&lt;T&gt;，或建立自己的抽象概念，例如 IRepository&lt;T&gt;。  在這兩種情況下，ADO.NET Entity Framework 4.0 的 POCO 支援可讓這些抽象概念的取用者，保持一致，非持續性及高度可測試性。 其他 EF4 功能，例如隱含的消極式載入可讓商務和應用程式的服務程式碼，而不必擔心關聯式資料存放區的詳細資料。 最後，我們所建立的抽象概念進行模擬 （mock） 或假單元測試內，我們可以使用這些測試替身，以達到快速執行中、 高隔離和可靠的測試。

### <a name="additional-resources"></a>其他資源

-   Robert C.Martin，「[單一責任原則](http://www.objectmentor.com/resources/articles/srp.pdf)"
-   Martin Fowler[模式目錄](http://www.martinfowler.com/eaaCatalog/index.html)從*的企業應用程式架構模式*
-   Griffin Caprio 」[相依性插入](https://msdn.microsoft.com/magazine/cc163739.aspx)"
-   資料可程式性的部落格，「[逐步解說： 測試為導向的開發與 Entity Framework 4.0](http://blogs.msdn.com/adonet/pages/walkthrough-test-driven-development-with-the-entity-framework-4-0.aspx)"。
-   資料可程式性的部落格，「[使用的儲存機制和工作單位模式與 Entity Framework 4.0](http://blogs.msdn.com/adonet/archive/2009/06/16/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0.aspx)"
-   Dave Astels 」 [BDD 入門](http://blog.daveastels.com/files/BDD_Intro.pdf)"
-   Aaron Jensen 」[簡介機器規格](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)"
-   Eric Lee 」 [BDD 使用 MSTest](http://blogs.msdn.com/elee/archive/2009/01/20/bdd-with-mstest.aspx)"
-   Eric Evans 」[網域導向設計](http://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)"
-   Martin Fowler 」[模擬 （mock） 不是虛設常式](http://martinfowler.com/articles/mocksArentStubs.html)"
-   Martin Fowler 」[測試雙精度浮點數](http://martinfowler.com/bliki/TestDouble.html)"
-   Jeremy Miller"[與互動的測試狀態](http://codebetter.com/blogs/jeremy.miller/articles/129544.aspx)"
-   [Moq](http://code.google.com/p/moq/)

### <a name="biography"></a>自傳

技術人員，在 Pluralsight 的成員和 OdeToCode.com 的創辦人 Scott Allen。 在 15 歲商業軟體開發的詳細資訊，Scott 曾解決方案用於從 8 位元的內嵌裝置，到高度可調整的 ASP.NET web 應用程式的所有項目。 您可以在他的部落格 OdeToCode，或在 Twitter 上透過 Scott [ http://twitter.com/OdeToCode ](http://twitter.com/OdeToCode)。
