---
title: 可測試性和 Entity Framework 4.0-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9430e2ab-261c-4e8e-8545-2ebc52d7a247
ms.openlocfilehash: 28ec5446ce9faf98fb8fff141832236d70b29daf
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181581"
---
# <a name="testability-and-entity-framework-40"></a>可測試性和 Entity Framework 4。0
Scott Allen

發佈2010 年 5 月

## <a name="introduction"></a>簡介

本白皮書將說明並示範如何使用 ADO.NET Entity Framework 4.0 和 Visual Studio 2010 撰寫可測試的程式碼。 本文不會嘗試將焦點放在特定的測試方法，例如測試導向設計（TDD）或行為導向設計（BDD）。 相反地，本文將著重于如何撰寫使用 ADO.NET Entity Framework 的程式碼，但仍可讓您以自動化方式輕鬆隔離和測試。 我們將探討常見的設計模式，以便在資料存取案例中進行測試，並瞭解如何在使用架構時套用這些模式。 我們也會探討架構的特定功能，以瞭解這些功能在可測試的程式碼中的使用方式。

## <a name="what-is-testable-code"></a>什麼是可測試的程式碼？

使用自動化單元測試來驗證軟體片段的功能，可提供許多值得的好處。 每個人都知道，良好的測試將會減少應用程式中的軟體缺失，並提升應用程式的品質

良好的單元測試套件可讓開發小組節省時間，並保有其所建立之軟體的控制權。 小組可以對現有程式碼進行變更、重構、重新設計和重建軟體，以符合新的需求，並在應用程式中加入新的元件，同時知道測試套件可以驗證應用程式的行為。 單元測試是快速意見反應迴圈的一部分，可在複雜度增加時，協助變更並保留軟體的可維護性。

不過，單元測試是以價格提供。 小組必須投入時間來建立及維護單元測試。 建立這些測試所需的工作量與基礎軟體的可**測試**性直接相關。 要測試的軟體有多簡單？ 設計具有可測試性之軟體的小組，會比使用未測試軟體的小組更快速地建立有效的測試。

Microsoft 設計了 ADO.NET Entity Framework 4.0 （EF4），並記住可測試性。 這並不表示開發人員會針對架構程式碼本身撰寫單元測試。 相反地，EF4 的可測試性目標讓您能夠輕鬆地建立以架構為基礎的可測試程式碼。 在查看特定範例之前，值得了解可測試程式碼的品質。

### <a name="the-qualities-of-testable-code"></a>可測試程式碼的品質

容易測試的程式碼一律會展示至少兩個特性。 首先，可測試的程式碼很容易**觀察**到。 假設有一組輸入，應該很容易觀察到程式碼的輸出。 例如，測試下列方法很簡單，因為方法會直接傳回計算的結果。

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

如果方法將計算的值寫入網路通訊端、資料庫資料表或檔案（如下列程式碼），則測試方法會很棘手。 測試必須執行額外的工作來取得值。

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

其次，可測試的程式碼很容易**隔離**。 讓我們使用下列虛擬程式碼，作為可測試程式碼的不良範例。

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

方法很容易觀察到-我們可以傳入保險原則，並確認傳回值符合預期的結果。 不過，若要測試方法，我們必須使用正確的架構來安裝資料庫，並設定 SMTP 伺服器，以防方法嘗試傳送電子郵件。

單元測試只會想要驗證方法內的計算邏輯，但是測試可能會失敗，因為電子郵件伺服器已離線，或資料庫伺服器已移動。 這兩個失敗都與測試想要驗證的行為無關。 這種行為很容易隔離。

致力於撰寫可測試程式碼的軟體發展人員，通常會努力在其所撰寫的程式碼中維護顧慮的分離。 上述方法應專注于商務計算，並將資料庫和電子郵件的執行詳細資料委派給其他元件。 Robert c. 會以單一責任原則來呼叫這一項。 物件應封裝單一、窄的責任，例如計算原則的值。 所有其他資料庫和通知工作都必須負責其他物件。 以這種方式撰寫的程式碼較容易隔離，因為它著重于單一工作。

在 .NET 中，我們有我們需要遵循單一責任原則並達成隔離的抽象概念。 我們可以使用介面定義，並強制程式碼使用介面抽象，而不是具象類型。 在本文稍後，我們將瞭解如何使用類似上述的錯誤範例，來處理*看起來*像是與資料庫交談的介面。 不過在測試時，我們可以替代不會與資料庫通訊的虛擬執行，而是將資料保留在記憶體中。 此虛擬執行會將程式碼與資料存取程式碼或資料庫設定中不相關的問題隔離。

隔離還有其他好處。 最後一種方法中的商業計算應該只需要幾毫秒的時間來執行，但測試本身可能會執行數秒，因為網路上的程式碼躍點，並與不同的伺服器交談。 單元測試應該會快速執行，以加速進行小型變更。 單元測試也應該是可重複且不會失敗，因為與測試無關的元件發生問題。 撰寫容易觀察和隔離的程式碼，可讓開發人員更輕鬆地撰寫程式碼測試、縮短等待測試執行的時間，更重要的是，花更少時間追蹤不存在的 bug。

希望您可以感謝測試的優點，並瞭解可測試的程式碼所展現的品質。 我們即將說明如何撰寫可與 EF4 搭配使用的程式碼，以將資料儲存至資料庫，同時可觀察和輕鬆隔離，但首先我們會縮小焦點，以討論資料存取的可測試設計。

## <a name="design-patterns-for-data-persistence"></a>資料持續性的設計模式

上述兩個錯誤範例都有太多責任。 第一個不良範例必須執行計算*並*寫入檔案。 第二個不良範例必須從資料庫讀取資料 *，並*執行商務計算*並*傳送電子郵件。 藉由設計較小的方法來區分疑慮，並將責任委派給其他元件，您將會對撰寫可測試的程式碼做出絕佳的努力 其目標是透過從小型和焦點的抽象概念來撰寫動作，以建立功能。

有了資料持續性時，我們所尋找的小型和焦點抽象概念會被記錄為設計模式。 Fowler 的企業應用程式架構書籍模式，是在列印中描述這些模式的第一項工作。 我們會先在下列各節中提供這些模式的簡短描述，再示範這些 ADO.NET Entity Framework 如何執行並使用這些模式。

### <a name="the-repository-pattern"></a>存放庫模式

Fowler 會指出存放庫在網域和資料對應層之間，使用類似集合的介面來存取網域物件。 存放庫模式的目標是要隔離資料存取 minutiae 中的程式碼，而且如我們所見，先前的隔離是可測試性的必要特性。

隔離的關鍵在於，存放庫如何使用類似集合的介面來公開物件。 您撰寫來使用儲存機制的邏輯，並不知道存放庫如何具體化您要求的物件。 存放庫可能會與資料庫交談，或者它可能只會從記憶體中的集合傳回物件。 您的所有程式碼都必須知道，存放庫會出現來維護集合，而您可以從集合中取出、加入和刪除物件。

在現有的 .NET 應用程式中，實體存放庫通常會繼承自泛型介面，如下所示：

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

當我們提供 EF4 的執行時，我們會對介面定義進行一些變更，但基本概念會維持不變。 程式碼可以使用實作為此介面的具體存放庫，依其主要索引鍵值抓取實體、根據述詞的評估來抓取實體集合，或只抓取所有可用的實體。 程式碼也可以透過存放庫介面新增和移除實體。

在指定 Employee 物件的 IRepository 時，程式碼可以執行下列作業。

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

因為程式碼使用的是介面（Employee 的 IRepository），所以我們可以為程式碼提供不同的介面執行。 其中一個執行方式是將物件 EF4 並保存到 Microsoft SQL Server 資料庫中，藉以進行支援。 不同的執行（在測試期間所使用的）可能會受到員工物件的記憶體中清單所支援。 介面將有助於在程式碼中達成隔離。

請注意，IRepository @ no__t-0T @ no__t-1 介面不會公開儲存作業。 如何更新現有的物件？ 您可能會遇到包含儲存作業的 IRepository 定義，而且這些存放庫的執行必須立即將物件保存到資料庫中。 不過，在許多應用程式中，我們不想要個別保存物件。 相反地，我們想要將物件從不同的存放庫帶入現實，將這些物件修改為商務活動的一部分，然後將所有物件保存為單一不可部分完成作業的一部分。 幸運的是，有一個模式可以允許這種行為。

### <a name="the-unit-of-work-pattern"></a>工作單位模式

Fowler 指出「工作單位」將會「維護受商務交易影響的物件清單」，並協調寫出變更和並行處理問題的解決方式。 追蹤存放庫中的物件所做的變更，並保存我們對物件所做的任何變更（當我們告訴工作單位認可變更時），這是工作單位的責任。 將新物件新增至所有存放庫並將物件插入資料庫，也會負責管理刪除作業的工作單位。

如果您曾經完成過 ADO.NET 資料集的任何工作，則您已經熟悉工作單位模式。 ADO.NET 資料集能夠追蹤更新、刪除和插入 DataRow 物件，而且可以（透過 TableAdapter 的協助）協調資料庫的所有變更。 不過，DataSet 物件會將基礎資料庫的已中斷連接子集模型化。 工作單位模式會展現相同的行為，但會使用與資料存取程式碼隔離並不知道資料庫的商務物件和網域物件。

在 .NET 程式碼中建立工作單位模型的抽象概念可能如下所示：

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

藉由公開工作單位的存放庫參考，我們可以確保單一工作單位物件能夠追蹤在商務交易期間具體化的所有實體。 執行實際工作單元的 Commit 方法，就是將記憶體中的變更與資料庫進行協調的所有神奇之處。 

假設有 IUnitOfWork 參考，程式碼可以對從一或多個存放庫中抓取的商務物件進行變更，並使用不可部分完成的認可作業來儲存所有變更。

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a>延遲負載模式

Fowler 會使用「延遲負載」來描述「不包含您需要的所有資料，但知道如何取得」的物件。 透明的消極式載入是撰寫可測試的商務程式碼並使用關係資料庫時，必須具備的重要功能。 例如，請考慮下列程式碼。

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

如何填入工時卡集合？ 有兩個可能的答案。 其中一個答案是員工存放庫在要求提取員工時，會發出查詢，同時將員工與員工相關聯的時間卡片資訊一起取得。 在關係資料庫中，這通常需要含有聯結子句的查詢，而且可能會導致抓取比應用程式需要更多的資訊。 如果應用程式永遠不需要接觸工時卡屬性，該怎麼辦？

第二個答案是依需求載入工時卡屬性。 這種消極式載入對商務邏輯而言是隱含且透明的，因為程式碼不會叫用特殊的 Api 來取出時間卡片資訊。 這段程式碼會假設有需要的時間卡資訊。 消極式載入牽涉到一些神奇之處，通常牽涉到執行時間攔截方法調用。 攔截程式碼負責與資料庫交談並抓取時間卡資訊，同時讓商務邏輯維持商務邏輯的自由。 這項延遲負載魔術可讓商務程式碼將本身與資料抓取作業隔離，並導致更具測試性的程式碼。

消極式載入的缺點是，當應用程式需要時間卡資訊時，該程式*代碼會執行*額外的查詢。 這不是許多應用程式的顧慮，但是對於效能敏感的應用程式或應用程式會在多個員工物件上迴圈，並在迴圈的每次反覆運算期間執行查詢來取出時間卡（問題通常稱為 N + 1查詢問題），消極式載入是一個拖曳。 在這些情況下，應用程式可能會想要以最有效率的方式立即載入時間卡資訊。

幸好，我們將在進入下一節並執行這些模式時，看到 EF4 如何同時支援隱含的延遲載入和有效率的積極式載入。

## <a name="implementing-patterns-with-the-entity-framework"></a>使用 Entity Framework 來執行模式

好消息是，我們在上一節所描述的所有設計模式，都是使用 EF4 來執行。 為了示範，我們將使用簡單的 ASP.NET MVC 應用程式來編輯和顯示員工及其相關聯的時間卡資訊。 我們會先使用下列「單純的舊版 CLR 物件」（Poco）。 

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

當我們探索 EF4 的不同方法和功能時，這些類別定義會稍有變更，但其目的是要盡可能將這些類別保持為非持續性（PI）。 PI 物件不知道它所保存的狀態*是否*存在於*資料庫中。* PI 和 Poco 會與可測試的軟體搭配使用。 使用 POCO 方法的物件較不受限制、更有彈性且更容易測試，因為它們可以在沒有資料庫存在的情況下運作。

Poco 就緒之後，我們就可以在 Visual Studio 中建立實體資料模型（EDM）（請參閱 [圖 1]）。 我們不會使用 EDM 為我們的實體產生程式碼。 相反地，我們想要使用我們以手動方式所謂的實體。 我們只會使用 EDM 來產生我們的資料庫架構，並提供中繼資料 EF4 需要將物件對應到資料庫。

![ef test_01](~/ef6/media/eftest-01.jpg)

**圖1**

注意：如果您想要先開發 EDM 模型，可以從 EDM 產生乾淨的 POCO 程式碼。 您可以使用資料可程式性小組所提供的 Visual Studio 2010 延伸模組來執行這項操作。 若要下載擴充功能，請從 Visual Studio 中的 [工具] 功能表啟動 [擴充管理員]，並搜尋「POCO」範本的線上元件庫（請參閱 [圖 2]）。 有數個適用于 EF 的 POCO 範本。 如需使用範本的詳細資訊，請參閱 < @no__t 0Walkthrough：Entity Framework @ no__t-0 "的 POCO 範本。

![ef test_02](~/ef6/media/eftest-02.png)

**圖2**

從這個 POCO 開始，我們將探討兩種不同的可測試程式碼方法。 第一種方法是呼叫 EF 方法，因為它會利用來自 Entity Framework API 的抽象概念來執行工作和存放庫的單位。 在第二個方法中，我們將建立自己的自訂存放庫抽象概念，然後查看每個方法的優缺點。 我們將從探索 EF 方法開始。  

### <a name="an-ef-centric-implementation"></a>EF 中心的執行

請考慮 ASP.NET MVC 專案中的下列控制器動作。 動作會抓取 Employee 物件，並傳回結果以顯示員工的詳細觀點。

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

程式碼是否可測試？ 至少有兩個測試需要驗證動作的行為。 首先，我們想要確認動作會傳回正確的觀點–這是一個簡單的測試。 我們也想要撰寫測試來確認動作會抓取正確的員工，而我們想要這麼做，而不執行程式碼來查詢資料庫。 請記住，我們想要隔離受測程式碼。 隔離會確保測試不會因為資料存取程式碼或資料庫設定中的錯誤而失敗。 如果測試失敗，我們會知道控制器邏輯中有錯誤，而不是在較低層級的系統元件中。

為了達到隔離，我們需要一些抽象概念，像是我們稍早針對存放庫和工作單位所提供的介面。 請記住，存放庫模式是設計來在網域物件和資料對應層之間進行協調。 在此案例中，EF4*是*資料對應層，而且已經提供名為 IObjectSet @ No__t-1T @ no__t-2 的類似存放庫抽象概念（來自 system.web 命名空間）。 介面定義看起來如下所示。

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

IObjectSet @ no__t-0T @ no__t-1 符合存放庫的需求，因為它類似物件的集合（透過 IEnumerable @ no__t-2T @ no__t-3），並提供從模擬的集合新增和移除物件的方法。 Attach 和 Detach 方法會公開 EF4 API 的其他功能。 若要使用 IObjectSet @ no__t-0T @ no__t-1 做為存放庫的介面，我們需要一個工作單位抽象來將存放庫系結在一起。

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

此介面的一個具體執行會與 SQL Server 交談，而且很容易就能使用 EF4 的 ObjectCoNtext 類別來建立。 ObjectCoNtext 類別是 EF4 API 中的實際工作單位。

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

將 IObjectSet @ no__t-0T @ no__t-1 帶入生活，就像叫用 ObjectCoNtext 物件的 Createobjectset<tentity> 方法一樣簡單。 在幕後，架構將會使用我們在 EDM 中提供的中繼資料來產生具體的 ObjectSet @ no__t-0T @ no__t-1。 我們會繼續傳回 IObjectSet @ no__t-0T @ no__t-1 介面，因為它有助於保留用戶端程式代碼中的可測試性。

這個具體的執行功能在生產環境中很有用，但我們需要專注于如何使用我們的 IUnitOfWork 抽象概念來加速測試。

### <a name="the-test-doubles"></a>測試加倍

為了隔離控制器動作，我們必須能夠在實際工作單位（由 ObjectCoNtext 支援）和測試 double 或「假」工作單位（執行記憶體中的作業）之間切換。 執行此類型切換的常見方法是不要讓 MVC 控制器具現化工作單位，而是改以函式參數的形式將工作單位傳遞至控制器。

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

上述程式碼是相依性插入的範例。 我們不允許控制器建立其相依性（工作單位），但會將相依性插入控制器中。 在 MVC 專案中，通常會搭配使用自訂控制器 factory 與反轉控制（IoC）容器，將相依性插入自動化。 這些主題已超出本文的範圍，但您可以遵循本文結尾的參考來閱讀更多。

可用於測試的假工作單位實作為可能如下所示。

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

請注意，假的工作單位會公開一個已認可的屬性。 將功能新增至可加速測試的假類別，有時會很有用。 在此情況下，您可以藉由檢查已認可的屬性，輕鬆觀察程式碼是否認可工作單位。

我們也需要假的 IObjectSet @ no__t-0T @ no__t-1，將員工和工時卡物件保存在記憶體中。 我們可以使用泛型來提供單一的執行。

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

這項測試會將大部分的工作都委派給基礎 HashSet @ no__t-0T @ no__t-1 物件。 請注意，IObjectSet @ no__t-0T @ no__t-1 需要泛型條件約束強制執行 T 做為類別（參考型別），同時也會強制我們執行 IQueryable @ no__t-2T @ no__t-3。 使用標準 LINQ 運算子 AsQueryable，可以輕鬆地將記憶體中的集合顯示為 IQueryable @ no__t-0T @ no__t-1。

### <a name="the-tests"></a>測試

傳統單元測試會使用單一測試類別來保存單一 MVC 控制器中所有動作的所有測試。 我們可以使用我們所建立的記憶體中 fakes，撰寫這些測試或任何類型的單元測試。 不過，在本文中，我們將避免整合型測試類別方法，並改為將我們的測試分組，以專注于特定的功能。  例如，「建立新員工」可能是我們想要測試的功能，因此我們將使用單一測試類別來驗證負責建立新員工的單一控制器動作。

所有這些精細的測試類別都需要一些常見的安裝程式碼。 例如，我們一律需要建立記憶體內部儲存機制和假的工作單位。 我們也需要員工控制器的實例，並插入假的工作單位。 我們會使用基類，在測試類別之間共用這個通用的安裝程式碼。

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

我們在基類中使用的「媽媽物件」是用來建立測試資料的一種常見模式。 媽媽物件包含 factory 方法，可具現化測試實體以在多個測試裝置上使用。

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

我們可以使用 EmployeeControllerTestBase 做為一些測試裝置的基類（請參閱 [圖 3]）。 每個測試裝置都會測試特定的控制器動作。 例如，一個測試裝置將著重在測試 HTTP GET 要求期間所使用的建立動作（以顯示用來建立員工的視圖），而不同的裝置將著重于 HTTP POST 要求中所使用的建立動作（以取得所提交的資訊建立員工的使用者）。 每個衍生類別只負責其特定內容中所需的設定，並提供驗證其特定測試內容之結果所需的判斷提示。

![ef test_03](~/ef6/media/eftest-03.png)

**圖3**

在這裡提供的命名慣例和測試樣式不需要用於可測試的程式碼，它只是一種方法。 [圖 4] 顯示 Visual Studio 2010 的 Jet 大腦 Resharper 測試執行器外掛程式中執行的測試。

![ef test_04](~/ef6/media/eftest-04.png)

**圖4**

有了用來處理共用安裝程式碼的基類，每個控制器動作的單元測試都很小，而且容易撰寫。 測試會快速執行（因為我們正在執行記憶體中的作業），而且不應該因為不相關的基礎結構或環境問題而失敗（因為我們已隔離受測試的單元）。

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

在這些測試中，基底類別會執行大部分的設定工作。 請記住，基類的處理常式會建立記憶體內部存放庫、假的工作單位，以及 EmployeeController 類別的實例。 測試類別衍生自這個基類，並著重于測試 Create 方法的細節。 在此情況下，細節會向下包括至您將在任何單元測試程式中看到的「排列」、「act」和「判斷提示」步驟：

-   建立 newEmployee 物件以模擬傳入的資料。
-   叫用 EmployeeController 的 Create 動作，並傳入 newEmployee。
-   確認 [建立] 動作會產生預期的結果（員工出現在存放庫中）。

我們所建立的內容可讓我們測試任何 EmployeeController 動作。 例如，當我們針對員工控制器的 [索引] 動作撰寫測試時，我們可以從測試基類繼承，為我們的測試建立相同的基本設定。 同樣地，基底類別會建立記憶體內部儲存機制、假的工作單位，以及 EmployeeController 的實例。 索引動作的測試只需要專注于叫用索引動作，並測試動作傳回的模型品質。

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

我們使用記憶體內部 fakes 所建立的測試，是針對測試軟體的*狀態*。 例如，當測試建立動作時，我們想要在建立動作執行後檢查儲存機制的狀態–存放庫是否持有新員工？

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

稍後我們將探討以互動為基礎的測試。 以互動為基礎的測試會詢問受測程式碼是否已叫用物件上的適當方法，並傳遞正確的參數。 我們現在要繼續探討另一個設計模式–消極式載入。

## <a name="eager-loading-and-lazy-loading"></a>積極式載入和消極式載入

在 ASP.NET MVC web 應用程式的某個時間點，我們可能會想要顯示員工的資訊，並包含員工的相關聯卡片。 例如，我們可能會有一個 [時間卡摘要] 顯示，顯示員工名稱和系統中的總卡片數。 有數種方法可以執行這項功能。

### <a name="projection"></a>Projection

建立摘要的一個簡單方法是，針對我們想要在視圖中顯示的資訊來建立專用的模型。 在此案例中，此模型看起來可能如下所示。

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

請注意，EmployeeSummaryViewModel 不是實體，換句話說，這不是我們想要保存在資料庫中的內容。 我們只會使用這個類別，以強型別方式將資料隨機放入視圖中。 視圖模型類似于資料傳輸物件（DTO），因為它不包含任何行為（沒有方法）–只有屬性。 屬性會保存我們需要移動的資料。 使用 LINQ 的標準投射運算子（Select 運算子）來具現化此視圖模型是很容易的。

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

上述程式碼有兩個值得注意的功能。 首先–程式碼很容易測試，因為它仍然很容易觀察和隔離。 Select 運算子的運作方式與我們的記憶體內部 fakes 相同，因為它是針對實際工作單元。

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

第二個值得注意的功能是程式碼如何讓 EF4 產生單一、有效率的查詢，將員工和時間卡資訊組合在一起。 我們已將員工資訊和時間卡資訊載入至相同的物件，而不需使用任何特殊 Api。 程式碼只會使用適用于記憶體內部資料來源和遠端資料源的標準 LINQ 運算子，來表示其所需的資訊。 EF4 能夠將 LINQ 查詢所產生的運算式樹狀架構和 C @ no__t-0 編譯器轉譯成單一且有效率的 T-SQL 查詢。

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
         )  AS [Project1]
    )  AS [Limit1]
```

有時候，我們不想要使用視圖模型或 DTO 物件，而是使用實際實體。 當我們知道我們需要員工*和*員工的時間卡時，我們可以立即以不顯眼且有效率的方式來載入相關資料。

### <a name="explicit-eager-loading"></a>明確的積極式載入

當我們想要立即載入相關的實體資訊時，我們需要一些商務邏輯（或在此案例中為控制器動作邏輯）的機制，以表達其對存放庫的期望。 EF4 ObjectQuery @ no__t-0T @ no__t-1 類別會定義 Include 方法，以指定要在查詢期間取得的相關物件。 請記住，EF4 ObjectCoNtext 會透過衍生自 No__t @ ObjectQuery-No__t @ 2T-3 的具體 ObjectSet @ no__t-0T @ no__t-1 類別來公開實體。  如果我們在控制器動作中使用 ObjectSet @ no__t-0T @ no__t-1 參考，我們可以撰寫下列程式碼來指定每個員工的即時卡片資訊載入。

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

不過，因為我們想要讓程式碼保持可測試性，所以我們不會從真實的工作單位類別外公開 ObjectSet @ no__t-0T @ no__t-1。 相反地，我們會依賴 IObjectSet @ no__t-0T @ no__t-1 介面，這較容易假，但是 IObjectSet @ no__t-2T @ no__t-3 不會定義 Include 方法。 LINQ 的優點是我們可以建立自己的 Include 運算子。

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

請注意，此 Include 運算子定義為 IQueryable @ no__t-0T @ no__t-1 的擴充方法，而不是 IObjectSet @ no__t-2T @ no__t-3。 這讓我們能夠使用具有更廣泛可能類型的方法，包括 IQueryable @ no__t-0T @ no__t-1、IObjectSet @ no__t-2T @ no__t-3、ObjectQuery @ no__t-4T @ no__t-5 和 ObjectSet @ no__t-6T @ no__t-7。 在事件中，基礎序列不是正版 EF4 ObjectQuery @ no__t-0T @ no__t-1，因此不會有任何傷害，而且 Include 運算子不會有任何作用。 如果基礎序列*是*ObjectQuery @ No__t-1T @ no__t-2 （或衍生自 ObjectQuery @ No__t-3T @ no__t-4），則 EF4 會看到我們對其他資料的需求，並制訂適當的 SQL 查詢。

有了這個新的操作員，我們就可以從存放庫明確要求立即載入的時間卡資訊。

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

針對實際的 ObjectCoNtext 執行時，程式碼會產生下列單一查詢。 查詢會從資料庫收集足夠的資訊，以具體化 employee 物件並完全填入其工時卡屬性。

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
         FROM  [dbo].[Employees] AS [Extent1]
         LEFT OUTER JOIN [dbo].[TimeCards] AS [Extent2] ON [Extent1].[Id] = [Extent2].[EmployeeTimeCard_TimeCard_Id]
    )  AS [Project1]
    ORDER BY [Project1].[HireDate] ASC,
             [Project1].[Id] ASC, [Project1].[C1] ASC
```

最棒的是，動作方法內的程式碼仍可進行完整測試。 我們不需要為 fakes 提供任何額外的功能來支援 Include 運算子。 壞消息是我們必須在程式碼內使用 Include 運算子，我們想要保持非持續性。 這是您在建立可測試的程式碼時，需要評估之取捨類型的主要範例。 有時候，您需要讓持續性考慮在存放庫抽象以外流失，以符合效能目標。

積極式載入的替代方法是消極式載入。 消極式載入表示我們*不*需要我們的商務程式碼，就能明確宣告相關資料的需求。 相反地，我們會在應用程式中使用我們的實體，如果需要額外的資料，Entity Framework 會視需要載入資料。

### <a name="lazy-loading"></a>消極式載入

很容易就能想像一下，我們不知道什麼是商務邏輯所需的資料。 我們可能知道邏輯需要 employee 物件，但我們可能會分支到不同的執行路徑，其中有些路徑需要員工的時間卡資訊，有些則不會。 這類案例非常適合用於隱含的消極式載入，因為資料神奇地出現在必要的基礎上。

消極式載入（也稱為延後載入）會將一些需求放在實體物件上。 具有真正持續性無知的 Poco 不會面對持續性層的任何需求，但真正的持續性無知實際上不可能達成。  相反地，我們會以相對角度來測量持續性無知。 如果我們需要繼承自持續性導向基類，或使用特定集合來達到 Poco 中的消極式載入，則會很遺憾。 幸好，EF4 有一個較不具侵入性的解決方案。

### <a name="virtually-undetectable"></a>幾乎無法偵測

使用 POCO 物件時，EF4 可以動態產生實體的執行時間 proxy。 這些 proxy 會以不可見的方式包裝具體化 Poco，並藉由攔截每個屬性 get 和 set 作業來執行其他工作，以提供額外的服務。 其中一項服務就是我們要尋找的消極式載入功能。 另一個服務是一種有效率的變更追蹤機制，可以在程式變更實體的屬性值時記錄。 在 SaveChanges 方法期間，ObjectCoNtext 會使用變更清單來保存任何已修改的實體，並使用 UPDATE 命令。

不過，若要讓這些 proxy 運作，它們需要一個方法來連結實體的屬性 get 和 set 作業，而 proxy 會藉由覆寫虛擬成員來達成此目標。 因此，如果我們想要進行隱含的消極式載入和有效率的變更追蹤，我們必須回到 POCO 類別定義，並將屬性標示為 virtual。

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

我們仍然可以說，Employee 實體大部分的持續性都是未知的。 唯一的需求是使用虛擬成員，而這不會影響程式碼的可測試性。 我們不需要衍生自任何特殊基類，或甚至使用消極式載入專用的特殊集合。 如程式碼所示，任何執行 ICollection @ no__t-0T @ no__t-1 的類別都可以用來保存相關的實體。

此外，我們還需要在工作單元中進行一項次要變更。 當直接使用 ObjectCoNtext 物件時，消極式載入會預設為*關閉*。 我們可以在 CoNtextoptions.negotiate 屬性上設定一個屬性來啟用延後載入，而且我們可以在實際工作單元內設定此屬性（如果我們想要在所有位置啟用消極式載入）。

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

啟用隱含的消極式載入時，應用程式程式碼可以使用員工和員工的相關聯的時間卡，同時知道不知道 EF 載入額外資料所需的工作。

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

消極式載入可讓應用程式的程式碼更容易撰寫，而且使用 proxy 的神奇，程式碼就能保持完整的可測試性。 工作單位的記憶體內部 fakes 可以在測試期間視需要預先載入具有相關聯資料的假實體。

此時，我們會使用 IObjectSet @ no__t-0T @ no__t-1 來將注意力轉成建立存放庫，並查看抽象概念以隱藏所有持續性架構的符號。

## <a name="custom-repositories"></a>自訂存放庫

當我們在本文中第一次提供工作單位設計模式時，我們提供了一些範例程式碼，說明工作單位的外觀。 讓我們使用我們使用的員工和員工的時間卡案例，來重新展示這一原始的想法。

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

此工作單位和我們在上一節中建立的工作單位之間的主要差異在於，此工作單位不會使用 EF4 架構中的任何抽象概念（沒有 IObjectSet @ no__t-0T @ no__t-1）。 IObjectSet @ no__t-0T @ no__t-1 可以做為存放庫介面，但它公開的 API 可能無法完全符合我們的應用程式需求。 在即將推出的方法中，我們將使用自訂的 IRepository @ no__t-0T @ no__t-1 抽象概念來呈現存放庫。

許多開發人員遵循測試導向設計、行為導向設計，以及網域導向的方法設計，都偏好使用 IRepository @ no__t-0T @ no__t-1 方法的原因有好幾種。 首先，IRepository @ no__t-0T @ no__t-1 介面代表「反毀」層。 如 Eric Evans 在他的網域導向設計書籍中所述，反毀層會讓您的網域程式碼遠離基礎結構 Api，例如持續性 API。 其次，開發人員可以將方法建立到存放庫中，以符合應用程式的確切需求（在撰寫測試時發現）。 例如，我們可能經常需要使用識別碼值來尋找單一實體，因此我們可以將 FindById 方法新增至存放庫介面。  我們的 IRepository @ no__t-0T @ no__t-1 定義看起來會像下面這樣。

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

請注意，我們會回到使用 IQueryable @ no__t-0T @ no__t-1 介面來公開實體集合。 IQueryable @ no__t-0T @ no__t-1 可讓 LINQ 運算式樹狀架構流入 EF4 提供者，並為提供者提供查詢的整體觀點。 第二個選項是傳回 IEnumerable @ no__t-0T @ no__t-1，這表示 EF4 LINQ 提供者只會看到存放庫內建的運算式。 在儲存機制外部完成的任何分組、排序和投射，都不會在傳送至資料庫的 SQL 命令中構成，因而可能會造成效能降低。 另一方面，只傳回 IEnumerable @ no__t-0T @ no__t-1 結果的存放庫，並不會對您造成新的 SQL 命令感到驚訝。 這兩種方法都能運作，而且這兩種方法都保持可測試狀態。

使用泛型和 EF4 ObjectCoNtext API 來提供 IRepository @ no__t-0T @ no__t-1 介面的單一實作為簡單的方式。

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

IRepository @ no__t-0T @ no__t-1 方法可讓我們進一步控制查詢，因為用戶端必須叫用方法來取得實體。 在方法內，我們可以提供額外的檢查和 LINQ 運算子，以強制執行應用程式條件約束。 請注意，介面有兩個泛型型別參數的條件約束。 第一個條件約束是 ObjectSet @ no__t-0T @ no__t-1 所需的類別缺點污點，而第二個條件約束會強制我們的實體執行 IEntity –為應用程式建立的抽象概念。 IEntity 介面會強制實體具有可讀取的 Id 屬性，然後我們可以在 FindById 方法中使用這個屬性。 IEntity 是以下列程式碼定義。

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

IEntity 可視為持續性無知的小違規，因為我們的實體需要實作為此介面。 記住持續性無知是關於取捨，而許多 FindById 功能將會超過介面加諸的條件約束。 介面不會影響可測試性。

具現化即時 IRepository @ no__t-0T @ no__t-1 需要 EF4 ObjectCoNtext，因此，具體的工作單位實作為應該管理具現化。

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

使用我們的自訂存放庫並不會與使用以 IObjectSet @ no__t-0T @ no__t-1 為基礎的儲存機制截然不同。 我們必須先叫用一個存放庫的方法來抓取 IQueryable @ no__t-0T @ no__t-1 參考，而不是直接將 LINQ 運算子套用至屬性。

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

請注意，我們先前實施的自訂 Include 運算子不需要變更即可正常執行。 存放庫的 FindById 方法會從嘗試取得單一實體的動作中移除重複的邏輯。

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

我們已檢查過的兩種方法的可測試性並沒有顯著的差異。 我們可以藉由建立 HashSet @ no__t-2Employee @ no__t-3 支援的具象類別，就像我們在上一節中所做的一樣，提供 IRepository @ no__t-0T @ no__t-1 的虛設實。 不過，有些開發人員偏好使用 mock 物件和模擬物件架構，而不是建立 fakes。 在下一節中，我們將探討如何使用模擬來測試我們的執行，並討論模擬與 fakes 之間的差異。

### <a name="testing-with-mocks"></a>使用模擬進行測試

有不同的方法可以建立 Fowler 會呼叫「測試雙精度」的方式。 測試雙精度浮點數（例如電影 stunt 雙精度浮點數）是您在測試期間，針對實際的生產物件所建立的「獨立式」物件。 我們所建立的記憶體內部存放庫會針對與 SQL Server 交談的存放庫進行測試。 我們已瞭解如何在單元測試期間使用這些測試雙精度浮點數來隔離程式碼，並讓測試快速執行。

我們所建立的測試加倍，有實際的工作執行。 在幕後，每一個都會儲存物件的具象集合，而且當我們在測試期間操作儲存機制時，會在此集合中新增和移除物件。 有些開發人員想要以這種方式來建立測試加倍，包括實際的程式碼和工作的執行。  這些測試加倍是我們所謂的*fakes*。 他們有工作中的執行，但它們並不足以供生產環境使用。 假存放庫實際上並不會寫入資料庫。 假的 SMTP 伺服器實際上不會透過網路傳送電子郵件訊息。

### <a name="mocks-versus-fakes"></a>模擬與 Fakes

還有另一種類型的測試 double，稱為*mock*。 雖然 fakes 有運作中的執行，但模擬不會有任何實作為。 在模擬物件架構的協助之下，我們會在執行時間中建立這些 mock 物件，並將它們當做測試加倍。 在本節中，我們會使用開放原始碼模擬 framework Moq。 以下是使用 Moq 以動態方式為員工存放庫建立測試 double 的簡單範例。

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

我們會要求 Moq 進行 IRepository @ no__t-0Employee @ no__t-1 的執行，並以動態方式建立一個。 我們可以藉由存取 Mock @ no__t-2T @ no__t-3 物件的 Object 屬性，來取得執行 IRepository @ no__t-0Employee @ no__t-1 的物件。 這是我們可以傳入控制器的內建物件，而且不知道這是測試雙重或真正的存放庫。 我們可以叫用物件上的方法，就像我們會在實際執行的物件上叫用方法一樣。

當我們叫用 Add 方法時，您必須想知道模擬儲存機制會執行的動作。 因為 mock 物件後面沒有任何執行，所以 Add 不會執行任何操作。 在幕後不會有具體的集合，像是我們所撰寫的 fakes，所以會捨棄員工。 FindById 的傳回值呢？ 在此情況下，mock 物件會執行唯一可以做的事，這會傳回預設值。 因為我們傳回的是參考型別（Employee），所以傳回值為 null 值。

模擬可能聽起來不萬能;不過，我們還沒有談到模擬的兩個功能。 首先，Moq 架構會記錄在 mock 物件上發出的所有呼叫。 稍後在程式碼中，如果有人叫用 Add 方法，或者任何人叫用了 FindById 方法，就可以詢問 Moq。 我們稍後會看到如何在測試中使用此「黑色箱」錄製功能。

第二個很棒的功能是我們可以如何使用 Moq 來程式設計具有*預期*的模擬物件。 預期會告訴 mock 物件如何回應任何指定的互動。 例如，我們可以在我們的 mock 中編寫預期的程式，並在有人叫用 FindById 時，告訴它傳回 employee 物件。 Moq 架構會使用安裝程式 API 和 lambda 運算式來設計這些預期。

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

在此範例中，我們會要求 Moq 以動態方式建立存放庫，然後在預期的情況下為存放庫進行程式設計。 當有人叫用傳遞值為5的 FindById 方法時，預期會告訴 mock 物件傳回識別碼值為5的新員工物件。 這種測試會成功，而且我們不需要為假的 IRepository @ no__t-0T @ no__t-1 建立完整的執行。

讓我們重新流覽稍早撰寫的測試，並將其修改為使用模擬，而不是 fakes。 就像之前一樣，我們將使用基類來設定所有控制器測試所需的基礎結構的通用部分。

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

安裝程式碼最常維持不變。 我們會使用 Moq 來建立模擬物件，而不是使用 fakes。 當程式碼叫用 employee 屬性時，基底類別會針對模擬工作單位進行排列，以傳回 mock 存放庫。 Mock 設定的其餘部分將會發生在每個特定案例專用的測試裝置內。 例如，「索引」動作的測試裝置會設定模擬儲存機制，以在動作叫用模擬儲存機制的 FindAll 方法時，傳回員工清單。

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

除了預期外，我們的測試看起來會像之前的測試。 不過，透過模擬架構的錄音功能，我們可以從不同角度來進行測試。 我們將在下一節探討這個新的觀點。

### <a name="state-versus-interaction-testing"></a>狀態與互動測試

您可以使用不同的技術來測試具有 mock 物件的軟體。 其中一種方法是使用狀態測試，這是我們在目前為止所做的工作。 以狀態為基礎的測試會對軟體的狀態做出判斷提示。 在最後一個測試中，我們在控制器上叫用了動作方法，並對它所應建立的模型做出判斷提示。 以下是一些測試狀態的其他範例：

-   確認存放庫在 Create 執行之後包含新的 employee 物件。
-   確認模型在索引執行後保存所有員工的清單。
-   確認在刪除執行後，存放庫未包含指定的員工。

另一個您會看到模擬物件的方法，就是驗證*互動*。 雖然以狀態為基礎的測試會對物件的狀態進行判斷提示，但互動式測試會對物件的互動方式做出判斷提示。 例如:

-   確認在建立執行時，控制器會叫用存放庫的 Add 方法。
-   確認控制器會在執行索引時，叫用存放庫的 FindAll 方法。
-   確認控制器會叫用工作單位的 Commit 方法，以在執行 [編輯] 時儲存變更。

互動測試通常需要較少的測試資料，因為我們不會在集合內刺探並驗證計數。 例如，如果我們知道詳細資料動作會使用正確的值叫用存放庫的 FindById 方法，則此動作的行為可能會正確。 我們可以驗證此行為，而不需要設定任何要從 FindById 傳回的測試資料。

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

上述測試裝置中唯一需要的設定是由基類提供的安裝程式。 當我們叫用控制器動作時，Moq 會記錄與 mock 存放庫的互動。 使用 Moq 的驗證 API，我們可以在控制器叫用具有適當識別碼值的 FindById 時，詢問 Moq。 如果控制器未叫用方法，或以非預期的參數值叫用方法，則 Verify 方法會擲回例外狀況，且測試將會失敗。

以下是另一個驗證 Create 動作在目前工作單位上叫用 Commit 的範例。

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

互動測試有一個危險，那就是過度指定互動的趨勢。 模擬物件記錄並確認每個與模擬物件互動的能力，並不表示測試應該嘗試驗證每個互動。 某些互動是執行詳細資料，您應該只驗證滿足目前測試*所需*的互動。

模擬或 fakes 之間的選擇主要取決於您要測試的系統和您的個人（或小組）喜好設定。 模擬物件可以大幅減少執行測試加倍所需的程式碼數量，但並非所有人都有熟悉的程式設計期望和驗證互動。

## <a name="conclusions"></a>結論

在本文中，我們示範了數種方法來建立可測試的程式碼，同時使用 ADO.NET Entity Framework 來進行資料持續性。 我們可以利用內建的抽象概念（例如 IObjectSet @ no__t-0T @ no__t-1），或建立自己的抽象概念，例如 IRepository @ no__t-2T @ no__t-3。  在這兩種情況下，ADO.NET Entity Framework 4.0 中的 POCO 支援可讓這些抽象類別的取用者維持持續性未知且高度可測試性。 隱含消極式載入之類的其他 EF4 功能可讓商務和應用程式服務程式碼工作，而不需擔心關聯式資料存放區的詳細資訊。 最後，我們所建立的抽象概念在單元測試中很容易模擬或假，而我們可以使用這些測試來達到快速執行、高度隔離和可靠的測試。

### <a name="additional-resources"></a>其他資源

-   Robert c. 「[單一責任原則](https://www.objectmentor.com/resources/articles/srp.pdf)」
-   聖馬丁 Fowler，*企業應用程式架構模式*的[模式目錄](https://www.martinfowler.com/eaaCatalog/index.html)
-   Griffin Caprio，「相依性[插入](https://msdn.microsoft.com/magazine/cc163739.aspx)」
-   資料可程式性 Blog，"@no__t 0Walkthrough：以 Entity Framework 4.0 @ no__t-0 "測試導向的開發。
-   資料可程式性的 Blog，「[使用存放庫和 Entity Framework 4.0 的工作單位模式](https://blogs.msdn.com/adonet/archive/2009/06/16/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0.aspx)」
-   Aaron Jensen 「[簡介機器規格](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)」
-   Eric 先生，" [BDD With MSTest](https://blogs.msdn.com/elee/archive/2009/01/20/bdd-with-mstest.aspx)"
-   Eric Evans，「[網域導向設計](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)」
-   聖馬丁 Fowler，"[模擬不是存根](https://martinfowler.com/articles/mocksArentStubs.html)"
-   聖馬丁 Fowler，" [Test Double](https://martinfowler.com/bliki/TestDouble.html)"
-   [Moq](https://code.google.com/p/moq/)

### <a name="biography"></a>自傳

Scott Allen 是 Pluralsight 和創辦人 OdeToCode.com 的技術人員的成員。 在15年的商務軟體開發中，Scott 已針對從8位 embedded 裝置到可高度擴充性 ASP.NET web 應用程式的所有專案，處理解決方案。 您可以在他的 OdeToCode，或在 Twitter 上的[https://twitter.com/OdeToCode](https://twitter.com/OdeToCode)，觸及 Scott 的 blog。
