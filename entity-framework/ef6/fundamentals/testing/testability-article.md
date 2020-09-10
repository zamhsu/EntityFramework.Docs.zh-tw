---
title: 可測試性和 Entity Framework 4.0-EF6
description: 可測試性和 Entity Framework 4。0
author: divega
ms.date: 10/23/2016
ms.assetid: 9430e2ab-261c-4e8e-8545-2ebc52d7a247
ms.openlocfilehash: e1ded772bfee4f1870a891eaa525b33da8e9d3bc
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618360"
---
# <a name="testability-and-entity-framework-40"></a>可測試性和 Entity Framework 4。0
Scott Allen

發佈日期：2010 年 5 月

## <a name="introduction"></a>簡介

本白皮書將說明並示範如何使用 ADO.NET Entity Framework 4.0 和 Visual Studio 2010 撰寫可測試的程式碼。 這份檔不會嘗試將焦點放在特定的測試方法，像是測試導向設計 (TDD) 或行為導向設計 (BDD) 。 本文將著重于如何撰寫使用 ADO.NET Entity Framework 的程式碼，但仍能以自動化的方式輕鬆地隔離和測試。 我們將探討常見的設計模式，以便在資料存取案例中進行測試，並瞭解如何在使用架構時套用這些模式。 我們也將探討架構的特定功能，以瞭解這些功能如何在可測試的程式碼中運作。

## <a name="what-is-testable-code"></a>什麼是可測試的程式碼？

使用自動化單元測試來驗證軟體的能力，可提供許多值得的優點。 每個人都知道良好的測試將會減少應用程式中的軟體瑕疵，並提高應用程式的品質，但是有了單元測試之後，就不只是找出錯誤。

良好的單元測試套件可讓開發小組節省時間，並保有其所建立之軟體的控制權。 小組可以對現有程式碼進行變更、重構、重新設計及重建軟體，以符合新的需求，並將新的元件新增至應用程式，同時知道測試套件可以驗證應用程式的行為。 單元測試是快速意見反應週期的一部分，可在複雜性增加時加速變更並保留軟體的可維護性。

不過，單元測試有價格。 小組必須投入時間來建立和維護單元測試。 建立這些測試所需的工作量，與基礎軟體的可 **測試** 性直接相關。 測試軟體有多簡單？ 設計具有可測試性之軟體的團隊，會比使用未測試軟體的團隊更快速地建立有效的測試。

Microsoft 設計了 ADO.NET Entity Framework 4.0 (EF4) ，並牢記可測試性。 這並不表示開發人員會針對架構程式碼本身撰寫單元測試。 相反地，EF4 的可測試性目標可讓您輕鬆地建立以架構為基礎的可測試程式碼。 在查看特定範例之前，最好先瞭解可測試程式碼的品質。

### <a name="the-qualities-of-testable-code"></a>可測試程式碼的品質

易於測試的程式碼一律會顯示至少兩個特性。 首先，可測試的程式碼很容易 **觀察**到。 由於有一組輸入，因此應該很容易觀察程式碼的輸出。 例如，測試下列方法很容易，因為方法會直接傳回計算的結果。

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

如果方法將計算的值寫入網路通訊端、資料庫資料表或類似下列程式碼的檔案，則測試方法會很困難。 測試必須執行額外的工作來取得值。

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

其次，可測試的程式碼很容易 **隔離**。 讓我們使用下列虛擬程式碼作為測試程式碼的不良範例。

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

方法很容易觀察，我們可以傳入保險原則，並確認傳回值符合預期的結果。 不過，若要測試方法，我們必須以正確的架構安裝資料庫，並在方法嘗試傳送電子郵件時設定 SMTP 伺服器。

單元測試只會想要驗證方法內的計算邏輯，但是測試可能會失敗，因為電子郵件伺服器已離線，或資料庫伺服器已移動。 這兩個失敗與測試要驗證的行為無關。 此行為難以隔離。

致力於撰寫可測試程式碼的軟體發展人員，通常致力於在所撰寫的程式碼中維護關注點分離。 上述方法應該專注于商務計算，並將資料庫和電子郵件執行詳細資料委派給其他元件。 Robert c. 以單一責任原則來呼叫這項工作。 物件應封裝單一且較窄的責任，例如計算原則的值。 所有其他的資料庫和通知工作都應該是其他物件的責任。 以這種方式撰寫的程式碼比較容易隔離，因為它著重于單一工作。

在 .NET 中，我們需要遵循單一責任原則並達成隔離的抽象概念。 我們可以使用介面定義，並強制程式碼使用介面抽象，而不是具象類型。 本檔稍後會看到類似上述錯誤範例的方法如何 *使用類似它們* 與資料庫溝通的介面。 不過，在測試階段，我們可以替代未與資料庫通訊的虛擬實作為，但會將資料保存在記憶體中。 這個虛擬實作為隔離程式碼，與資料存取程式碼或資料庫設定中不相關的問題。

另外還有其他優點需要隔離。 最後一個方法中的商業計算應該只需要幾毫秒的時間來執行，但測試本身可能會執行數秒，作為網路周圍的程式碼躍點，並與各種伺服器交談。 單元測試應該會快速執行，以加速小型變更。 單元測試應該也是可重複的，而且不會失敗，因為與測試無關的元件有問題。 撰寫容易觀察及隔離的程式碼，可讓開發人員更輕鬆地撰寫程式碼的測試、花較少的時間等待測試執行，而且更重要的是，花較少的時間追蹤不存在的 bug。

希望您可以知道測試的優點，並瞭解測試程式碼所展現的品質。 我們即將說明如何撰寫可與 EF4 搭配運作的程式碼，以將資料儲存到資料庫中，同時還能觀察並容易隔離，但首先我們要將焦點縮小，以討論資料存取的可測試設計。

## <a name="design-patterns-for-data-persistence"></a>資料持續性的設計模式

稍早所示的兩個不良範例都具有太多責任。 第一個不良範例必須執行計算 *並* 寫入至檔案。 第二個不良範例必須從資料庫讀取資料 *，並* 執行商務計算 *並* 傳送電子郵件。 藉由設計較小的方法來分隔考慮並將責任委派給其他元件，您將會在撰寫可測試的程式碼時產生絕佳的進展 其目標是要藉由從小型和焦點的抽象概念組成動作來建立功能。

當資料持續性的時候，我們所要尋找的小型和焦點抽象概念很常見，它們已記載為設計模式。 Fowler 企業應用程式架構的書籍模式，是在列印時描述這些模式的第一項工作。 我們將在下列各節中提供這些模式的簡短說明，我們將示範這些 ADO.NET Entity Framework 如何實行這些模式並搭配使用。

### <a name="the-repository-pattern"></a>存放庫模式

Fowler 會指出「存放庫」會使用類似集合的介面來協調網域和資料對應層，以存取網域物件」。 存放庫模式的目標是要將程式碼與資料存取的 minutiae 隔離，而且我們看到先前的隔離是可測試性的必要特性。

隔離的關鍵是存放庫如何使用類似集合的介面來公開物件。 您所撰寫用來使用存放庫的邏輯，並不知道存放庫將如何具體化您要求的物件。 存放庫可能會與資料庫通訊，也可能只是從記憶體中的集合傳回物件。 您所有的程式碼都必須知道，存放庫似乎會維護集合，而且您可以從集合中取出、新增和刪除物件。

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

當我們提供 EF4 的執行時，我們會對介面定義進行一些變更，但基本概念仍維持不變。 程式碼可以使用實作為此介面的具體存放庫，依其主鍵值抓取實體、根據述詞的評估取得實體的集合，或單純取出所有可用的實體。 程式碼也可以透過存放庫介面新增和移除實體。

由於有員工物件的 IRepository，程式碼可以執行下列作業。

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

由於程式碼使用的是 (IRepository 員工) 的介面，因此我們可以為程式碼提供不同的介面實現。 其中一個實作為 EF4，並將物件保存到 Microsoft SQL Server 資料庫中。 我們在) 測試期間所使用的不同實 (，可能會受到員工物件的記憶體中清單所支援。 介面將有助於在程式碼中達成隔離。

請注意，IRepository &lt; T &gt; 介面不會公開儲存作業。 如何更新現有的物件？ 您可能會遇到包含儲存作業的 IRepository 定義，而這些儲存機制的執行必須立即將物件保存到資料庫中。 不過，在許多應用程式中，我們不想要個別保存物件。 相反地，我們想要將物件從不同的存放庫帶入生命，將這些物件修改為商務活動的一部分，然後在單一、不可部分完成的作業中保存所有物件。 幸運的是，有一個模式可以允許這種類型的行為。

### <a name="the-unit-of-work-pattern"></a>工作單位模式

Fowler 指出工作單位會「維護受商務交易影響的物件清單，並協調寫出變更和並行問題的解決」。 從存放庫追蹤我們所提供之物件的變更，以及當我們告知工作單位認可變更時，我們對物件所做的任何變更，都是負責追蹤的工作單位。 工作單位也必須負責採用我們新增至所有存放庫的新物件，並將物件插入資料庫，也會管理刪除。

如果您曾經使用過 ADO.NET 資料集完成任何工作，就已經熟悉工作單位模式。 ADO.NET 資料集能夠追蹤更新、刪除和插入 DataRow 物件，並可在 TableAdapter 的協助下 (，) 協調對資料庫所做的所有變更。 不過，資料集物件會建立基礎資料庫之中斷連接子集的模型。 工作單位模式表現相同的行為，但使用的是與資料存取程式碼和不知道資料庫隔離的商務物件和網域物件。

在 .NET 程式碼中建立工作單位模型的抽象概念可能如下所示：

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

藉由公開工作單位的存放庫參考，我們可以確保單一工作單位物件能夠追蹤在商務交易期間具體化的所有實體。 實際工作單位的 Commit 方法的執行方式，就是將記憶體中的變更與資料庫協調。 

有了 IUnitOfWork 參考，程式碼可以變更從一個或多個存放庫取出的商務物件，並使用不可部分完成的認可作業來儲存所有變更。

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a>延遲負載模式

Fowler 使用名稱消極式載入來描述「不包含所需資料的物件，但知道如何取得」。 透明消極式載入是撰寫可測試的商務程式碼並使用關係資料庫時，必須具備的一項重要功能。 例如，請考慮下列程式碼。

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

工時卡集合的填入方式為何？ 有兩個可能的答案。 其中一個答案是員工存放庫在要求提取員工時，會發出查詢來取得員工以及員工相關聯的時間卡資訊。 在關係資料庫中，這通常需要具有 JOIN 子句的查詢，而且可能會導致抓取超過應用程式需求的資訊。 如果應用程式永遠不需要接觸工時卡屬性，該怎麼辦？

第二個答案是載入工時卡屬性「依需求」。 這項消極式載入在商務邏輯中是隱含且透明的，因為程式碼不會叫用特殊的 Api 來取出時間卡資訊。 這段程式碼假設在需要時，會出現時間卡資訊。 延遲載入通常牽涉到方法調用的執行時間攔截，因此有一些魔術。 攔截程式碼負責與資料庫的交談，並取出時間卡資訊，同時讓商務邏輯保持商務邏輯。 這種延遲的負載魔術可讓商務程式碼將本身與資料抓取作業隔離，並產生更多可測試的程式碼。

延遲載入的缺點是，當應用程式需要時間卡資訊時，程式碼 *將會執行* 額外的查詢。 這對許多應用程式而言並不重要，但是針對效能敏感的應用程式或應用程式會在迴圈的每個反復專案期間，執行查詢來取出時間卡片 (問題通常稱為「N + 1 查詢」) 問題，所以消極式載入是一項拖曳。 在這些情況下，應用程式可能會想要以最有效率的方式立即載入時間卡片資訊。

幸運的是，當我們移至下一節並實行這些模式時，我們將瞭解 EF4 如何支援隱含的延遲載入和有效率的積極式載入。

## <a name="implementing-patterns-with-the-entity-framework"></a>使用 Entity Framework 執行模式

好消息是，我們在上一節中描述的所有設計模式都是使用 EF4 來執行的簡單方式。 為了示範，我們將使用簡單的 ASP.NET MVC 應用程式來編輯和顯示員工及其相關聯的時間卡資訊。 我們將從使用下列「簡單的 CLR 物件」 (Poco) 開始。 

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

當我們探索 EF4 的不同方法和功能時，這些類別定義會稍微變更，但其目的是要盡可能將這些類別保留 (PI) 。 PI 物件並不知道它所保存的*if*狀態在資料庫中的存留*程度*。 PI 與 Poco 與可測試的軟體密切配合。 使用 POCO 方法的物件較不受限制、更有彈性且更容易測試，因為它們可以在沒有資料庫存在的情況下運作。

有了 Poco，我們就可以在 Visual Studio 中建立實體資料模型 (EDM)  (請參閱 [圖 1]) 。 我們不會使用 EDM 來為我們的實體產生程式碼。 相反地，我們想要使用我們所謂手動製作的實體。 我們只會使用 EDM 來產生資料庫架構，並提供 EF4 需要的中繼資料，以將物件對應到資料庫。

![ef test_01](~/ef6/media/eftest-01.jpg)

**圖1**

注意：如果您想要先開發 EDM 模型，您可以從 EDM 產生乾淨的 POCO 程式碼。 您可以使用資料可程式性小組所提供的 Visual Studio 2010 延伸模組來進行這項作業。 若要下載延伸模組，請從 Visual Studio 的 [工具] 功能表啟動 [擴充管理員]，並搜尋 "POCO" 的線上範本元件庫 (請參閱 [圖 2]) 。 有數個適用于 EF 的 POCO 範本。 如需使用範本的詳細資訊，請參閱「 [逐步解說：適用于 Entity Framework 的 POCO 範本](/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)」。

![ef test_02](~/ef6/media/eftest-02.png)

**圖2**

從這個 POCO 起點，我們將探討兩種可測試程式碼的不同方法。 我稱之為 EF 方法的第一種方法，是利用 Entity Framework API 的抽象概念來執行工作單位和存放庫。 在第二個方法中，我們會建立自己的自訂存放庫抽象概念，然後查看每個方法的優缺點。 我們將從探索 EF 方法開始著手。  

### <a name="an-ef-centric-implementation"></a>EF 中心的實作為

從 ASP.NET MVC 專案考慮下列控制器動作。 此動作會抓取 Employee 物件並傳回結果，以顯示員工的詳細觀點。

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

程式碼是否可測試？ 至少有兩個測試需要驗證動作的行為。 首先，我們想要確認該動作會傳回正確的 view –簡單的測試。 我們也想要撰寫測試來確認該動作會抓取正確的員工，而且我們想要執行此動作，而不需要執行程式碼來查詢資料庫。 請記住，我們想要隔離受測程式碼。 隔離可確保測試不會因為資料存取程式碼或資料庫設定中的錯誤而失敗。 如果測試失敗，我們會知道控制器邏輯中有錯誤，而不是在某些較低層級的系統元件中。

為了達成隔離，我們需要一些抽象概念，例如我們稍早針對存放庫和工作單位提供的介面。 請記住，儲存機制模式是設計來協調網域物件和資料對應層之間的協調。 在此案例中，EF4 *是* 資料對應層，且已 &lt; &gt; 從 system.object 命名空間) 提供名為 IObjectSet T (的類似儲存機制抽象概念。 介面定義看起來如下所示。

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

IObjectSet &lt; T &gt; 符合存放庫的需求，因為它類似于透過 IEnumerable T)  (的物件集合 &lt; &gt; ，並提供方法來新增和移除模擬集合中的物件。 附加和卸離方法會公開 EF4 API 的額外功能。 若要使用 IObjectSet &lt; T &gt; 做為存放庫的介面，我們需要一個工作單位抽象單位來將存放庫系結在一起。

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

此介面有一個具體的實作為 SQL Server，而且很容易就能使用 EF4 的 ObjectCoNtext 類別來建立。 ObjectCoNtext 類別是 EF4 API 中的實際工作單位。

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

將 IObjectSet 的工作變得很簡單，只要叫 &lt; &gt; 用 ObjectCoNtext 物件的 createobjectset<tentity> 方法即可。 在幕後，架構會使用我們在 EDM 中提供的中繼資料來產生具體的 ObjectSet &lt; T &gt; 。 我們會保留傳回 IObjectSet &lt; T 介面， &gt; 因為它有助於保留用戶端程式代碼中的可測試性。

這種具體的執行在生產環境中很有用，但我們必須專注于如何使用我們的 IUnitOfWork 抽象概念來加速測試。

### <a name="the-test-doubles"></a>測試雙精度浮點數

為了找出控制器動作，我們必須能夠在 (的 ObjectCoNtext) 和測試雙或「假」工作單元之間切換， (執行記憶體中的作業) 。 執行這種類型切換的常見方法是不要讓 MVC 控制器具現化工作單位，但改以函式的形式將工作單位傳至控制器中，以作為函式參數。

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

上述程式碼是相依性插入的範例。 我們不允許控制器 (工作單位來建立相依性) 但會將相依性插入控制器中。 在 MVC 專案中，通常會搭配使用自訂控制器 factory 與控制反轉 (IoC) 容器來自動插入相依性。 這些主題已超出本文的討論範圍，但您可以遵循本文結尾的參考來閱讀更多內容。

您可以用來進行測試的假工作單元，看起來可能如下所示。

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

請注意，虛設的工作單位會公開認可的屬性。 將功能新增至可協助測試的假類別，有時會很有用。 在此情況下，很容易就能觀察程式碼是否藉由檢查認可的屬性來認可工作單位。

我們也需要假的 IObjectSet &lt; T &gt; 來保存員工和工時卡在記憶體中的物件。 我們可以使用泛型提供單一的實作為。

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

這項測試會將大部分的工作委派給基礎 HashSet &lt; T &gt; 物件。 請注意，IObjectSet &lt; T &gt; 需要泛型條件約束，強制 T 做為)  (參考型別的類別，而且也會強制我們執行 IQueryable &lt; T &gt; 。 &lt; &gt; 使用標準 LINQ 運算子 AsQueryable，可輕鬆地將記憶體中的集合顯示為 IQueryable T。

### <a name="the-tests"></a>測試

傳統單元測試會使用單一測試類別來保存單一 MVC 控制器中所有動作的所有測試。 我們可以使用我們所建立的記憶體 fakes，撰寫這些測試或任何類型的單元測試。 不過，在本文中，我們會避免整合型測試類別的方法，而是將測試分組以專注于特定的功能。例如，「建立新員工」可能是我們想要測試的功能，因此我們將使用單一測試類別來確認負責建立新員工的單一控制器動作。

所有這些精細的測試類別都需要一些常見的設定程式碼。 例如，我們一律需要建立記憶體中的存放庫和假的工作單元。 我們也需要員工控制器的實例，並插入假的工作單位。 我們會使用基類，在測試類別之間共用這個通用的設定程式碼。

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

我們在基類中使用的「物件母親」是建立測試資料的一種常見模式。 母親物件包含可具現化測試實體以便跨多個測試裝置使用的 factory 方法。

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

我們可以使用 EmployeeControllerTestBase 作為許多測試裝置的基類 (請參閱 [圖 3]) 。 每個測試裝置都會測試特定的控制器動作。 例如，一個測試裝置將著重于測試在 HTTP GET 要求期間使用的 [建立] 動作 (為了顯示建立員工) 的觀點，而不同的裝置會著重在 HTTP POST 要求中使用的 [建立] 動作 (以取得使用者所提交的資訊，以建立員工) 。 每個衍生類別只負責其特定內容中所需的設定，以及提供驗證其特定測試內容之結果所需的判斷提示。

![ef test_03](~/ef6/media/eftest-03.png)

**圖3**

測試程式碼不需要此處提供的命名慣例和測試樣式，只是其中一種方法。 [圖 4] 顯示 Visual Studio 2010 的 Jet 大腦 Resharper 測試執行器外掛程式中執行的測試。

![ef test_04](~/ef6/media/eftest-04.png)

**圖4**

利用基類來處理共用的安裝程式碼，每個控制器動作的單元測試都很小，而且很容易撰寫。 測試將會快速執行 (因為我們會) 執行記憶體中的作業，而且不應該因為不相關的基礎結構或環境 (問題而失敗，因為我們已將測試中的單元隔離) 。

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

在這些測試中，基類會進行大部分的設定工作。 請記住，基類的函式會建立記憶體中的存放庫、假的工作單位，以及 EmployeeController 類別的實例。 測試類別衍生自此基類，並著重于測試 Create 方法的細節。 在此情況下，詳細資訊歸結到您將在任何單元測試程式中看到的「排列、操作和判斷提示」步驟：

-   建立 newEmployee 物件以模擬傳入的資料。
-   叫用 EmployeeController 的 Create 動作，然後傳入 newEmployee。
-   確認 [建立] 動作會產生預期的結果 (員工出現在 [存放庫]) 中。

我們所建立的內容可讓我們測試任何 EmployeeController 動作。 例如，當我們為員工控制器的索引動作撰寫測試時，我們可以從測試基類繼承，以針對測試建立相同的基本設定。 同樣地，基類會建立記憶體中的存放庫、假的工作單位，以及 EmployeeController 的實例。 索引動作的測試只需要專注于叫用索引動作，並測試動作所傳回之模型的品質。

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

我們使用記憶體內部 fakes 建立的測試，是針對測試軟體的 *狀態* 。 例如，在測試建立動作時，我們想要在建立動作執行之後檢查儲存機制的狀態–存放庫是否保有新的員工？

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

稍後我們將探討以互動為基礎的測試。 互動測試將會詢問受測程式碼是否在物件上叫用適當的方法，並傳遞正確的參數。 現在，我們將移至另一個設計模式，也就是消極式載入。

## <a name="eager-loading-and-lazy-loading"></a>積極式載入和消極式載入

在 ASP.NET MVC web 應用程式中的某個時間點，我們可能會想要顯示員工的資訊並包含員工相關聯的時間卡。 例如，我們可能會有一個顯示員工名稱和系統中的總時間卡總數的時間卡摘要顯示。 有幾種方法可以執行這項功能。

### <a name="projection"></a>Projection

建立摘要的一個簡單方法是，針對我們想要顯示在視圖中的資訊，建立專用的模型。 在此案例中，模型看起來可能如下所示。

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

請注意，EmployeeSummaryViewModel 不是實體，換句話說，這不是我們想要保存在資料庫中的內容。 我們只會使用這個類別，以強型別的方式將資料隨機播放到視圖中。 視圖模型就像是 (DTO) 的資料傳輸物件，因為它不包含任何方法 () –僅限屬性。 這些屬性會保存需要移動的資料。 使用 LINQ 的標準投射運算子（Select 運算子）來具現化此視圖模型很容易。

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

上述程式碼有兩個值得注意的功能。 首先–程式碼很容易測試，因為它仍然很容易觀察和隔離。 Select 運算子的運作方式，與我們對實際工作單位執行的記憶體內部 fakes 一樣。

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

第二個值得注意的功能是程式碼如何讓 EF4 產生單一、有效率的查詢，將員工和時間卡片資訊組合在一起。 我們已將員工資訊和時間卡片資訊載入相同的物件中，而不需要使用任何特殊的 Api。 此程式碼只會使用適用于記憶體內部資料來源和遠端資料源的標準 LINQ 運算子來表示所需的資訊。 EF4 能夠將 LINQ 查詢和 C 編譯器所產生的運算式樹狀架構，轉譯 \# 成單一且有效率的 t-SQL 查詢。

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

在其他情況下，我們不想使用視圖模型或 DTO 物件，而是實際實體。 當我們知道需要員工 *和* 員工的時間卡時，可以立即以不顯眼且有效率的方式載入相關資料。

### <a name="explicit-eager-loading"></a>明確的積極式載入

當我們想要立即負載相關的實體資訊時，我們需要某種機制來 (商務邏輯，而在此案例中，控制器動作邏輯) 來表達其對存放庫的期望。 EF4 ObjectQuery &lt; T &gt; 類別會定義包含方法，以指定要在查詢期間取得的相關物件。 請記住，EF4 ObjectCoNtext 會透過 &lt; &gt; 繼承自 ObjectQuery t 的具體 ObjectSet T 類別來公開實體 &lt; &gt; 。如果我們 &lt; &gt; 在控制器動作中使用 ObjectSet T 參考，我們可以撰寫下列程式碼來指定每位員工的一次性載入的時間卡資訊。

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

不過，由於我們想要讓程式碼可供測試，因此不會 &lt; &gt; 從實際的工作單位類別外部公開 ObjectSet T。 相反地，我們會依賴 IObjectSet &lt; T &gt; 介面，此介面較容易偽造，但 IObjectSet &lt; T 不 &gt; 會定義 Include 方法。 LINQ 的優點是我們可以建立自己的 Include 運算子。

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

請注意，這個 Include 運算子定義為 IQueryable T 的擴充方法， &lt; &gt; 而不是 IObjectSet &lt; T &gt; 。 如此一來，我們就能夠搭配更廣泛的可能類型來使用方法，包括 IQueryable &lt; T &gt; 、IObjectSet &lt; T &gt; 、ObjectQuery &lt; T &gt; 和 ObjectSet &lt; T &gt; 。 當基礎序列不是正版 EF4 ObjectQuery &lt; T 時，就不 &gt; 會有任何損害，而且 Include 運算子也不會有任何作用。 如果基礎序列 *是* ObjectQuery &lt; t &gt; (或衍生自 ObjectQuery &lt; t &gt;) ，則 EF4 將會看到我們對其他資料的需求，並制訂適當的 SQL 查詢。

有了這個新的運算子之後，我們就可以明確地向存放庫要求立即載入的時間卡資訊。

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

針對實際的 ObjectCoNtext 執行時，程式碼會產生下列單一查詢。 此查詢會從資料庫收集足夠的資訊以具體化員工物件，並完整填入其工時卡屬性。

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

最棒的是，動作方法內的程式碼仍可完全測試。 我們不需要為 fakes 提供任何額外的功能來支援 Include 運算子。 壞消息是我們必須使用我們想要保留非持續性的程式碼內的 Include 運算子。 這是在建立可測試的程式碼時，需要評估的取捨類型的主要範例。 有時您需要讓保存庫抽象以外的持續性問題發生，以符合效能目標。

積極式載入的替代方案是消極式載入。 消極式載入表示我們 *不* 需要我們的商務程式碼來明確宣佈相關資料的需求。 相反地，我們會在應用程式中使用我們的實體，如果需要其他資料，Entity Framework 將視需要載入資料。

### <a name="lazy-loading"></a>消極式載入

很容易就能想像出一種商務邏輯所需的資料。 我們可能知道邏輯需要員工物件，但我們可能會分支到不同的執行路徑，其中某些路徑需要來自員工的時間卡資訊，而有些則不需要。 像這樣的案例很適合用於隱含的延遲載入，因為資料神奇地出現在需要的基礎上。

消極式載入（也稱為延後載入）會在實體物件上放置一些需求。 具有真正持續性無知的 Poco 不會面對持續性層的任何需求，但真正的持續性無知幾乎無法達成。相反地，我們會以相對的角度測量持續性無知。 如果我們需要從持續性導向基類繼承，或使用特製化集合來達到 Poco 中的消極式載入，則會很不幸。 幸運的是，EF4 有一個較不具侵入性的解決方案。

### <a name="virtually-undetectable"></a>幾乎無法偵測

使用 POCO 物件時，EF4 可以動態產生實體的執行時間 proxy。 這些 proxy 會以不可見的方式包裝具體化 Poco，並藉由攔截每個屬性的 get 和 set 作業來執行其他工作，以提供其他服務。 這種服務是我們要尋找的消極式載入功能。 另一項服務是有效率的變更追蹤機制，可在程式變更實體的屬性值時記錄。 在 SaveChanges 方法期間，ObjectCoNtext 會使用變更清單來保存任何已修改的實體（使用 UPDATE 命令）。

不過，若要讓這些 proxy 能夠運作，他們需要一種方法來攔截實體上的屬性 get 和 set 作業，而 proxy 會覆寫虛擬成員以達成此目標。 因此，如果我們想要有隱含的延遲載入和有效率的變更追蹤，我們必須回到 POCO 類別定義，並將屬性標示為虛擬。

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

我們仍然可以說，Employee 實體大多是持續性。 唯一的需求是使用虛擬成員，而這不會影響程式碼的可測試性。 我們不需要從任何特殊基類衍生，或甚至使用消極式載入專用的特殊集合。 如同程式碼所示，執行 ICollection T 的任何類別 &lt; &gt; 都可以用來保存相關的實體。

此外，我們還需要在我們的工作單元內進行一項微小的變更。 當直接使用 ObjectCoNtext 物件時，消極式載入預設為 *關閉* 。 我們可以在 CoNtextOptions 屬性上設定一個屬性來啟用延後載入，如果我們想要在所有地方啟用消極式載入，我們可以在實際的工作單元內設定這個屬性。

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

啟用隱含消極式載入後，應用程式程式碼可以使用員工和員工相關聯的時間卡，而其餘知道不知道 EF 載入額外資料所需的工作。

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

消極式載入可讓應用程式程式碼更容易撰寫，並以 proxy 魔術讓程式碼保持完整的可測試性。 工作單位的記憶體內部 fakes 可以在測試期間視需要預先載入具有相關聯資料的假實體。

現在，我們將使用 IObjectSet T 來建立存放庫的注意力 &lt; &gt; ，並查看抽象概念來隱藏持續性架構的所有徵兆。

## <a name="custom-repositories"></a>自訂存放庫

當我們在本文中第一次看到工作單位設計模式時，我們提供了一些範例程式碼，可讓工作單位看起來像這樣。 讓我們使用我們使用的員工和員工的時間卡案例，重新展示此原始概念。

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

此工作單位和我們在上一節中建立的工作單位之間的主要差異在於，此工作單元如何不使用 EF4 架構中的任何抽象 (沒有 IObjectSet &lt; T &gt;) 。 IObjectSet &lt; T &gt; 適用于存放庫介面，但它所公開的 API 可能無法與應用程式的需求完全一致。 在接下來的方法中，我們將使用自訂的 IRepository &lt; T 抽象概念來代表存放庫 &gt; 。

許多開發人員都遵循以測試為導向的設計、行為導向設計，以及領域導向的方法設計，基於許多原因而偏好使用 IRepository &lt; T &gt; 方法。 首先，IRepository &lt; T &gt; 介面代表「反損毀」層。 如同網域導向設計書籍中的 Eric Evans 所述，反損毀層可讓您的網域程式碼遠離基礎結構 Api，例如持續性 API。 其次，開發人員可以在存放庫中建立方法，以符合應用程式 (在) 撰寫測試時所發現的確切需求。 例如，我們可能經常需要使用識別碼值找出單一實體，因此我們可以將 FindById 方法新增至存放庫介面。我們 &lt; 的 IRepository T &gt; 定義看起來會如下所示。

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

請注意，我們會回頭使用 IQueryable &lt; T &gt; 介面來公開實體集合。 IQueryable &lt; T &gt; 可讓 LINQ 運算式樹狀架構流入 EF4 提供者，並為提供者提供查詢的整體觀點。 第二個選項是傳回 IEnumerable &lt; T &gt; ，這表示 EF4 LINQ 提供者將只會看到在存放庫內建立的運算式。 在存放庫以外完成的任何群組、排序和投影都不會組成傳送至資料庫的 SQL 命令，這可能會影響效能。 另一方面，只傳回 IEnumerable T 結果的存放庫 &lt; &gt; 永遠不會對您造成新的 SQL 命令感到驚訝。 這兩種方法都可以運作，而且這兩種方法都可供測試。

您可以直接 &lt; &gt; 使用泛型和 EF4 ObjectCoNtext API 來提供 IRepository T 介面的單一執行。

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

IRepository &lt; T &gt; 方法可讓我們對查詢有一些額外的控制權，因為用戶端必須叫用方法來取得實體。 在方法內部，我們可以提供額外的檢查和 LINQ 運算子來強制執行應用程式條件約束。 請注意，介面在泛型型別參數上有兩個條件約束。 第一個條件約束是 ObjectSet T 所需的類別缺點污點 &lt; &gt; ，而第二個條件約束會強制我們的實體執行 IEntity，也就是為應用程式建立的抽象概念。 IEntity 介面會強制實體具有可讀取的識別碼屬性，然後我們可以在 FindById 方法中使用這個屬性。 IEntity 會使用下列程式碼來定義。

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

IEntity 可能被視為不符合持續性無知，因為我們的實體需要執行這個介面。 記住持續性無知是關於取捨，而針對許多 FindById 功能，會超過介面所加諸的限制。 介面對可測試性沒有任何影響。

具現化即時 IRepository &lt; T &gt; 需要 EF4 ObjectCoNtext，因此具體的工作執行單位應該管理具現化。

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

使用我們的自訂存放庫，與使用以 IObjectSet T 為基礎的儲存機制並無太大差異 &lt; &gt; 。 我們必須先叫用存放庫的方法，以抓取 IQueryable T 參考，而不是直接將 LINQ 運算子套用至 &lt; 屬性 &gt; 。

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

請注意，我們先前實行的自訂 Include 運算子將可運作，而不會變更。 存放庫的 FindById 方法會從嘗試取出單一實體的動作中移除重複的邏輯。

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

這兩種方法的可測試性並沒有明顯的差異。 我們可以藉 &lt; 由建立 HashSet 員工所支援的具體類別，來提供 IRepository T 的假實， &gt; &lt; &gt; 就像我們在上一節中所做的一樣。 不過，有些開發人員偏好使用 mock 物件和模擬物件架構，而不是建立 fakes。 我們將探討如何使用模擬來測試我們的執行，並在下一節討論模擬和 fakes 之間的差異。

### <a name="testing-with-mocks"></a>使用模擬進行測試

有不同的方法可以建立 Fowler 「測試雙精度」的聖馬丁。 測試雙 (像是電影 stunt 雙) ，是您在測試期間針對真實的生產物件所建立的「獨立」物件。 我們所建立的記憶體內部存放庫會針對與 SQL Server 交談的存放庫測試雙精度浮點數。 我們已瞭解如何在單元測試期間使用這些測試雙精度浮點數，以隔離程式碼並讓測試快速執行。

我們所建立的測試將會有實際的工作執行。 在幕後，每個物件都會儲存物件的具體集合，而且當我們在測試期間操作存放庫時，它們會在此集合中加入和移除物件。 有些開發人員想要以這種方式來建立測試，這種方式是使用真正的程式碼和運作中的。這些測試會加倍，我們稱之為 *fakes*。 它們具有運作中的實作為，但其實不足以供生產環境使用。 假的存放庫實際上不會寫入資料庫。 假的 SMTP 伺服器實際上不會透過網路傳送電子郵件訊息。

### <a name="mocks-versus-fakes"></a>模擬與 Fakes

另外還有另一種類型的測試雙重稱為 *mock*。 雖然 fakes 具有運作中的執行，但模擬沒有任何執行。 藉由模擬物件架構的說明，我們會在執行時間時建立這些 mock 物件，並將它們作為測試雙精度浮點數。 在本節中，我們將使用開放原始碼模擬 framework Moq。 以下是使用 Moq 動態建立員工存放庫之測試雙的簡單範例。

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

我們要求 Moq 進行 IRepository &lt; 員工的 &gt; 執行，並以動態方式建立一個。 我們可以藉 &lt; &gt; 由存取 Mock T 物件的 object 屬性，取得執行 IRepository 員工的 &lt; 物件 &gt; 。 這是我們可以傳遞至控制器的內建物件，而且不會知道這是測試雙重或真正的儲存機制。 我們可以在物件上叫用方法，就像我們在實際執行的物件上叫用方法一樣。

當我們叫用 Add 方法時，您必須想知道 mock 存放庫會做什麼。 因為 mock 物件背後沒有任何執行，所以 Add 不會執行任何動作。 幕後沒有具象的集合，就像我們所撰寫的 fakes 一樣，因此會捨棄員工。 FindById 傳回的值呢？ 在此情況下，mock 物件會執行唯一可執行檔動作，這會傳回預設值。 由於我們會傳回 (員工) 的參考型別，因此傳回值為 null 值。

模擬可能音效毫無價值;不過，我們還沒有談到模擬的兩項功能。 首先，Moq 架構會記錄對 mock 物件發出的所有呼叫。 稍後在程式碼中，我們可以詢問 Moq 是否有任何人叫用 Add 方法，或是否有任何人叫用 FindById 方法。 我們稍後會看到如何在測試中使用這個「黑箱」錄製功能。

第二個很棒的功能，就是我們可以如何使用 Moq 來對具有 *預期*的 mock 物件進行程式設計。 預期會告知 mock 物件如何回應任何指定的互動。 例如，我們可以在我們的 mock 中設計預期的程式，並在有人叫用 FindById 時告訴它傳回 employee 物件。 Moq 架構會使用安裝程式 API 和 lambda 運算式來設計這些期望。

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

在此範例中，我們會要求 Moq 動態建立存放庫，然後在預期的情況下為存放庫設計程式。 預期當有人叫用傳遞值為5的 FindById 方法時，預期會告知 mock 物件傳回識別碼值為5的新員工物件。 這種測試會通過，而且我們不需要建立完整的實 IRepository &lt; t &gt; 。

讓我們重新審視稍早撰寫的測試，並將其重新修改為使用模擬，而不是 fakes。 就像之前一樣，我們將使用基類來設定所有控制器測試所需的基礎結構的通用部分。

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

安裝程式碼大多保持不變。 我們不使用 fakes，而是使用 Moq 來建立模擬物件。 當程式碼叫用 employee 屬性時，基底類別會排列模擬單位來傳回 mock 存放庫。 Mock 設定的其餘部分會在每個特定案例專用的測試裝置內進行。 例如，索引動作的測試裝置會設定模擬儲存機制，以在動作叫用 mock 儲存機制的 FindAll 方法時，傳回員工清單。

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

除了期望外，我們的測試看起來類似于先前的測試。 不過，有了 mock 架構的錄音功能，我們就可以從不同的角度進行測試。 在下一節中，我們將探討這個新的觀點。

### <a name="state-versus-interaction-testing"></a>狀態與互動測試

您可以使用不同的技巧來測試具有 mock 物件的軟體。 其中一個方法是使用以狀態為基礎的測試，也就是我們在這份檔中的目前為止所做的。 以狀態為基礎的測試會對軟體的狀態進行判斷提示。 在最後一項測試中，我們已在控制器上叫用動作方法，並對它應該建立的模型提出判斷提示。 以下是一些測試狀態的其他範例：

-   確認在建立執行之後，存放庫包含新的 employee 物件。
-   確認模型保存在執行索引之後的所有員工清單。
-   確認在刪除執行之後，儲存機制不包含指定的員工。

模擬物件的另一種方法是驗證 *互動*。 以狀態為基礎的測試會對物件的狀態進行判斷提示，以互動為基礎的測試會對物件的互動進行判斷提示。 例如：

-   確認控制器會在建立執行時叫用存放庫的 Add 方法。
-   確認控制器在索引執行時叫用存放庫的 FindAll 方法。
-   確認控制器會叫用工作單位的 Commit 方法，以在編輯執行時儲存變更。

互動測試通常需要較少的測試資料，因為我們不會在集合內刺探並驗證計數。 例如，如果我們知道詳細資料動作會以正確的值叫用存放庫的 FindById 方法，則此動作的行為可能會正確。 我們可以驗證此行為，而不需設定任何要從 FindById 傳回的測試資料。

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

上述測試裝置中唯一需要的設定是基類所提供的設定。 當我們叫用控制器動作時，Moq 會記錄與 mock 儲存機制的互動。 使用 Moq 的 Verify API，我們可以詢問 Moq 是否已叫用具有適當識別碼值的 FindById。 如果控制器未叫用方法，或叫用具有未預期參數值的方法，則 Verify 方法會擲回例外狀況，而且測試將會失敗。

以下是另一個範例，確認建立動作在目前的工作單位上叫用 Commit。

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

互動測試有一個危險，那就是不能指定互動的趨勢。 Mock 物件記錄和驗證與模擬物件之每次互動的能力，不表示測試應該嘗試驗證每個互動。 某些互動是實作為詳細資料，您應該只驗證滿足目前測試 *所需* 的互動。

模擬或 fakes 之間的選擇主要取決於您要測試的系統，以及您的個人 (或小組) 喜好設定。 模擬物件可以大幅減少實作為測試雙精確度所需的程式碼數量，但不是每個人都熟悉程式設計預期以及驗證互動。

## <a name="conclusions"></a>結論

在本白皮書中，我們示範了幾種方法來建立可測試的程式碼，同時使用 ADO.NET Entity Framework 來進行資料持續性。 我們可以利用內建的抽象（例如 IObjectSet &lt; t &gt; ），或建立自己的抽象概念，例如 IRepository &lt; t &gt; 。在這兩種情況下，ADO.NET Entity Framework 4.0 中的 POCO 支援都可讓這些抽象概念的取用者維持持續性和高測試性。 隱含消極式載入等其他 EF4 功能可讓商務和應用程式服務程式碼正常運作，而不需要擔心關聯式資料存放區的詳細資料。 最後，在單元測試中，我們所建立的抽象概念很容易模擬或假，而且我們可以使用這些測試，以達到快速執行、高度隔離和可靠的測試。

### <a name="additional-resources"></a>其他資源

-   Robert c. 「 [單一責任原則](https://www.objectmentor.com/resources/articles/srp.pdf)」
-   Fowler，*企業應用程式架構模式*的[模式目錄](https://www.martinfowler.com/eaaCatalog/index.html)
-   Griffin Caprio，「相依性 [插入](https://msdn.microsoft.com/magazine/cc163739.aspx)」
-   資料可程式性 Blog [：「逐步解說：使用 Entity Framework 4.0 的測試驅動開發](/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)」。
-   資料可程式性 Blog：「 [使用存放庫和 Entity Framework 4.0 的工作單位模式](/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)」
-   Aaron Jensen，「 [電腦規格簡介](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)」
-   Eric 先生，" [BDD With MSTest](https://saintgimp.org/2009/01/20/bdd-with-mstest/)"
-   Eric Evans，「 [網域導向設計](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)」
-   聖馬丁 Fowler，" [模擬非存根](https://martinfowler.com/articles/mocksArentStubs.html)"
-   聖馬丁 Fowler，" [Test Double](https://martinfowler.com/bliki/TestDouble.html)"
-   [Moq](https://code.google.com/p/moq/)

### <a name="biography"></a>傳記

Scott Allen 是 Pluralsight 的技術人員和 OdeToCode.com 創辦人的成員。 在15年的商務軟體開發中，Scott 已針對從8位 embedded 裝置到可高度擴充的 ASP.NET web 應用程式的所有專案，處理解決方案。 您可以在他的 OdeToCode 或 Twitter 的網路上，觸及 Scott [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode) 。
