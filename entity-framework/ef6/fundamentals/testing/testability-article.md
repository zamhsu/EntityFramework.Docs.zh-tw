---
title: 可測試性和實體框架 4.0 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9430e2ab-261c-4e8e-8545-2ebc52d7a247
ms.openlocfilehash: 96b6b2791f12b7d60a233f7e6dc77e5a8579fb66
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434309"
---
# <a name="testability-and-entity-framework-40"></a>可測試性和實體框架 4.0
斯科特·艾倫

發佈日期：2010 年 5 月

## <a name="introduction"></a>簡介

本白皮書介紹並演示了如何使用 ADO.NET 實體框架 4.0 和 Visual Studio 2010 編寫可測試代碼。 本文不嘗試專注於特定的測試方法,如測試驅動設計 (TDD) 或行為驅動設計 (BDD)。 相反,本文將重點介紹如何使用ADO.NET實體框架編寫代碼,但仍然易於以自動化方式隔離和測試。 我們將瞭解在數據訪問方案中簡化測試的常見設計模式,並瞭解如何在使用框架時應用這些模式。 我們還將查看框架的特定功能,瞭解這些功能如何在可測試代碼中工作。

## <a name="what-is-testable-code"></a>什麼是可測試代碼?

使用自動單元測試驗證軟體的能力提供了許多理想的好處。 每個人都知道,好的測試將減少應用程式中的軟體缺陷數量,並提高應用程式的品質,但單元測試的到位遠不止發現錯誤。

一個好的單元測試套件允許開發團隊節省時間並保持對所創建軟體的控制。 團隊可以對現有代碼進行更改、重構、重新設計和重組軟體以滿足新要求,並在知道測試套件可以驗證應用程式的行為的同時,將新元件添加到應用程式中。 單元測試是快速反饋週期的一部分,用於隨著複雜性的增加,促進更改並保留軟體的可維護性。

然而,單元測試是有代價的。 團隊必須投入時間創建和維護單元測試。 創建這些測試所需的工作量與基礎軟體的**可測試性**直接相關。 軟體測試有多容易? 設計具有可測試性的軟體的團隊將比使用不可測試軟體的團隊更快地創建有效的測試。

Microsoft 設計了ADO.NET實體框架 4.0 (EF4), 並考慮到可測試性。 這並不意味著開發人員將針對框架代碼本身編寫單元測試。 相反,EF4 的可測試性目標便於創建基於框架的可測試代碼。 在查看特定示例之前,瞭解可測試代碼的品質是值得的。

### <a name="the-qualities-of-testable-code"></a>可測試代碼的品質

易於測試的代碼始終表現出至少兩個特徵。 首先,可測試程式碼易於**觀察**。 給定一組輸入,應很容易觀察代碼的輸出。 例如,測試以下方法非常簡單,因為該方法直接返回計算結果。

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

如果方法將計算的值寫入網路套接字、資料庫表或檔(如以下代碼)中,則測試方法非常困難。 測試必須執行其他工作才能檢索該值。

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

其次,可測試代碼易於**隔離**。 讓我們使用以下偽代碼作為可測試代碼的壞示例。

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

該方法易於觀察 – 我們可以在保險單中傳遞並驗證返回價值與預期結果匹配。 但是,要測試該方法,我們需要使用正確的架構安裝資料庫,並配置 SMTP 伺服器,以防該方法嘗試發送電子郵件。

單元測試只想驗證方法內的計算邏輯,但測試可能會失敗,因為電子郵件伺服器處於脫機狀態,或者因為資料庫伺服器移動。 這兩個失敗都與測試要驗證的行為無關。 該行為難以隔離。

努力編寫可測試代碼的軟體開發人員通常努力在編寫的代碼中保持關注點的分離。 上述方法應側重於業務計算,並將資料庫和電子郵件實現詳細資訊委派給其他元件。 羅伯特·馬丁稱之為單一責任原則。 對象應封裝單個窄責任,例如計算策略的值。 所有其他資料庫和通知工作應由其他對象負責。 以這種方式編寫的代碼更易於隔離,因為它側重於單個任務。

在 .NET 中,我們有遵循單一責任原則並實現隔離所需的抽象。 我們可以使用介面定義,並強制代碼使用介面抽象而不是具體類型。 在本文的後面部分,我們將瞭解類似上面介紹的壞示例這樣的方法如何使用*看起來*他們將與資料庫對話的介面。 但是,在測試時,我們可以替換不與資料庫對話但將數據存儲在記憶體中的虛擬實現。 此虛擬實現將隔離代碼與數據訪問代碼或資料庫配置中的不相關問題。

隔離還有其他好處。 最後一種方法中的業務計算只需幾毫秒即可執行,但測試本身可能會運行幾秒鐘,因為代碼在網路中跳躍並與各種伺服器進行對話。 單元測試應快速運行,以方便進行小更改。 單元測試也應是可重複的,而不是失敗的,因為與測試無關的元件有問題。 編寫易於觀察和隔離的代碼意味著開發人員將更容易地為代碼編寫測試,花更少的時間等待測試執行,更重要的是,花費更少的時間來跟蹤不存在的 Bug。

希望您能夠理解測試的好處,並瞭解可測試代碼所展示的品質。 我們將討論如何編寫與 EF4 配合使用的代碼,將數據保存到資料庫中,同時保持可觀察且易於隔離,但首先,我們將縮小重點,以討論數據訪問的可測試設計。

## <a name="design-patterns-for-data-persistence"></a>資料持久性設計模式

前面提出的兩個不好的例子都有太多的責任。 第一個壞示例必須執行計算*並*寫入檔。 第二個壞例子必須從資料庫中讀取數據 *,並*執行業務計算*併*發送電子郵件。 通過設計更小的方法,將關注點分離並委派給其他元件,您將在編寫可測試代碼方面取得重大進展。 目標是通過從小型抽象和重點抽象中組合操作來構建功能。

在數據持久性方面,我們正在尋找的小型且重點突出的抽象非常普遍,已記錄為設計模式。 Martin Fowler 的著作《企業應用程式體系結構模式》是描述這些模式的第一部著作。 我們將在以下各節中簡要介紹這些模式,然後介紹這些ADO.NET實體框架如何實現和使用這些模式。

### <a name="the-repository-pattern"></a>存放庫模式

福勒說,一個存儲庫"使用類似集合的介面訪問域物件,在域和數據映射層之間進行仲介"。 存儲庫模式的目標是將代碼與數據訪問的細枝末節隔離開來,正如我們之前看到的,隔離是可測試性的必要特徵。

隔離的關鍵是存儲庫如何使用類似集合的介面公開物件。 您編寫用於使用儲存庫的邏輯不知道儲存庫將如何實現請求的物件。 存儲庫可能與資料庫對話,或者可能只是從記憶體中集合返回物件。 所有代碼需要知道的是,存儲庫似乎維護集合,並且可以從集合中檢索、添加和刪除物件。

在現有 .NET 應用程式中,具體存儲庫通常從通用介面繼承,如下所示:

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

在為 EF4 提供實現時,我們將對介面定義進行一些更改,但基本概念保持不變。 代碼可以使用實現此介面的具體存儲庫按其主鍵值檢索實體,基於謂詞的評估檢索實體集合,或者僅檢索所有可用實體。 代碼還可以透過儲存庫介面添加和刪除實體。

給定「員工」物件的 IRepository,代碼可以執行以下操作。

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

由於代碼使用介面(員工 IRepository),我們可以為代碼提供介面的不同實現。 一個實現可能是由 EF4 支援的實現,並將物件持久化到 Microsoft SQL Server 資料庫中。 不同的實現(我們在測試期間使用的實現)可能由記憶體中的員工物件列表支援。 該介面將有助於在代碼中實現隔離。

請注意,IRepository&lt; &gt; T 介面不會公開"保存"操作。 我們如何更新現有物件? 您可能會遇到包含 Save 操作的 IRepository 定義,這些儲存庫的實現需要立即將物件保存到資料庫中。 但是,在許多應用程式中,我們不想單獨保留物件。 相反,我們希望將物件(可能來自不同的存儲庫)帶入生活,將這些物件修改為商務活動的一部分,然後將所有物件保留為單個原子操作的一部分。 幸運的是,有一個模式允許這種類型的行為。

### <a name="the-unit-of-work-pattern"></a>工作單位模式

福勒說,一個工作單元將"維護受業務交易影響的物件清單,並協調更改的寫入和併發問題的解決」。。 工作單位有責任跟蹤我們從存儲庫中引入的物件的更改,並在告訴工作單位提交更改時保留我們對物件所做的任何更改。 工作單元也有責任將我們添加到所有存儲庫的新對象並插入資料庫,以及進行刪除。

如果您曾經做過ADO.NET數據集的任何工作,那麼你已經熟悉工作模式的單位。 ADO.NET DataSet 能夠追蹤 DataRow 物件的更新、刪除和插入,並且可以(在表適配器的説明下)協調我們對資料庫的所有更改。 但是,DataSet 物件對基礎資料庫的斷開連接子集建模。 工作模式單元表現出相同的行為,但適用於與數據訪問代碼隔離且不知道資料庫的業務物件和域物件。

對 .NET 代碼中工作單元建模的抽象可能如下所示:

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

通過公開來自工作單元的存儲庫引用,我們可以確保單個工作單元的對象能夠跟蹤業務事務期間實現的所有實體。 實際工作單元的 Commit 方法的實現是所有魔術都恰好在記憶體中更改與資料庫協調的地方。 

給定 IUnitOfWork 引用,代碼可以更改從一個或多個儲存庫檢索的業務物件,並使用原子提交操作保存所有更改。

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a>延遲載入模式

Fowler 使用名稱延遲負載來描述"一個不包含所需所有數據但知道如何獲取數據的物件"。 透明延遲載入是編寫可測試業務代碼和使用關係資料庫時需要具備的重要功能。 例如,請考慮以下代碼。

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

如何填充時間卡集合? 有兩個可能的答案。 一個答案是,當員工存儲庫被要求獲取員工時,會發出查詢以檢索員工以及員工關聯的時卡資訊。 在關係資料庫中,這通常需要使用 JOIN 子句的查詢,並且可能導致檢索比應用程式需要的資訊更多。 如果應用程式不需要觸摸 TimeCard 屬性,該怎麼辦?

第二個答案是"按需"載入 TimeCard 屬性。 此延遲載入對業務邏輯是隱式的和透明的,因為代碼不會調用特殊的 API 來檢索時卡資訊。 代碼假定時間卡資訊在需要時存在。 延遲載入涉及一些魔術,通常涉及方法調用的運行時攔截。 攔截代碼負責與資料庫交談和檢索時卡資訊,同時使業務邏輯自由成為業務邏輯。 這種惰性載入魔力允許業務代碼將自己與數據檢索操作隔離開來,並生成更可測試的代碼。

延遲載入的缺點是,當應用程式*確實需要*時卡資訊時,程式碼將執行其他查詢。 這不是許多應用程式的問題,但對於性能敏感的應用程式或應用程式迴圈訪問多個員工物件和執行查詢以在迴圈的每次反覆運算期間檢索時間卡(通常稱為 N+1 查詢問題),延遲載入是一種阻力。 在這些情況下,應用程式可能希望以最有效的方式緊急載入時間卡資訊。

幸運的是,我們將瞭解 EF4 如何支援隱式惰性負載和高效的熱要負載,同時進入下一節並實現這些模式。

## <a name="implementing-patterns-with-the-entity-framework"></a>使用實體框架實現模式

好消息是,我們在最後一節中介紹的所有設計模式都非常簡單地使用 EF4 實現。 為了展示我們將使用簡單的ASP.NET MVC 應用程式來編輯和顯示員工及其關聯的時卡資訊。 我們將首先使用以下「普通舊 CLR 物件」(POCO)。 

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

當我們探索 EF4 的不同方法和功能時,這些類定義將略有變化,但目的是盡可能將這些類保留為持久性無知 (PI)。 PI*物件不知道如何,**甚至即使它*保持的狀態生活在資料庫中。 PI 和 POCO 與可測試軟體齊頭並進。 使用 POCO 方法的物件約束較小、更靈活且更易於測試,因為它們可以在沒有資料庫存在的情況下運行。

在 POCO 到位後,我們可以在可視化工作室中創建實體數據模型 (EDM),參見圖 1)。 我們不會使用 EDM 為我們的實體生成代碼。 相反,我們希望使用我們精心打造的實體。 我們只會使用 EDM 生成資料庫架構並提供將物件映射到資料庫所需的元數據 EF4。

![埃夫·test_01](~/ef6/media/eftest-01.jpg)

**圖 1**

注意:如果要首先開發 EDM 模型,則可以從 EDM 生成乾淨的 POCO 代碼。 您可以使用資料可程式設計團隊提供的 Visual Studio 2010 擴展來執行此操作。 要下載擴展,請從 Visual Studio 中的「工具」功能表啟動擴展管理器,並搜索「POCO」樣本的線上庫(見圖 2)。 有幾種 POCO 範本可用於 EF。 有關使用範本的詳細資訊,請參閱"[演練:實體框架的 POCO 範本](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)"。

![埃夫test_02](~/ef6/media/eftest-02.png)

**圖 2**

從這個 POCO 起點,我們將探討兩種不同的可測試代碼方法。 我稱之為 EF 方法的第一種方法是因為它利用實體框架 API 中的抽象來實現工作單元和儲存庫。 在第二種方法中,我們將創建自己的自定義存儲庫抽象,然後瞭解每種方法的優缺點。 我們將首先探索 EF 方法。  

### <a name="an-ef-centric-implementation"></a>以 EF 為中心的實施

請考慮 ASP.NET MVC 專案中的以下控制器操作。 該操作檢索"員工"物件並返回結果以顯示員工的詳細視圖。

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

代碼是可測試的嗎? 我們至少需要測試兩個來驗證操作的行為。 首先,我們要驗證操作返回正確的視圖 - 一個簡單的測試。 我們還希望編寫一個測試來驗證操作檢索正確的員工,並且我們希望在不執行代碼來查詢資料庫的情況下執行此操作。 請記住,我們要隔離被測的代碼。 隔離將確保測試不會因為數據訪問代碼或資料庫配置中的 Bug 而失敗。 如果測試失敗,我們將知道控制器邏輯中存在 Bug,而不是某些較低級別的系統元件中的錯誤。

為了實現隔離,我們需要一些抽象,例如我們前面為存儲庫和工作單元提供的介面。 請記住,存儲庫模式旨在在域物件和數據映射層之間進行仲介。 在這種情況下,EF4*是*資料映射層,並且已經提供了一個名為 IObjectSet&lt;T(&gt;來自 System.Data.object 命名空間)的類似儲存庫的抽象。 介面定義如下所示。

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

IObjectSet&lt; &gt; T 滿足儲存庫的要求,因為它類似於物件集合&lt;&gt;(透過IE55t),並提供從類比集合中添加和刪除物件的方法。 附加和分離方法公開 EF4 API 的其他功能。 要使用&lt;IObjectSet&gt; T 作為儲存庫的介面,我們需要一個工作抽象單元來將儲存庫綁定在一起。

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

此介面的一個具體實現將與 SQL Server 對話,並且使用 EF4 中的 ObjectContext 類輕鬆創建。 ObjectContext 類別是 EF4 API 中的實際工作單元。

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

將 IObjectSet&lt; &gt; T 變為生命就像調用物件上下文物件的 CreateObjectSet 方法一樣簡單。 在幕後,架構將使用我們在 EDM 中提供的中繼資料來產生具體的 ObjectSet&lt;T&gt;。 我們將堅持返回&lt;IObjectSet&gt;T 介面,因為它將有助於在客戶端代碼中保留可測試性。

此具體實現在生產中非常有用,但我們需要關注如何使用IUnitOfWork抽象來簡化測試。

### <a name="the-test-doubles"></a>測試雙精度值

要隔離控制器操作,我們需要能夠在實際工作單元(由 ObjectContext 支援)和測試雙精度或「假」工作單元(執行記憶體中操作)之間切換。 執行此類切換的常見方法是不要讓 MVC 控制器實例化工作單元,而是將工作單元作為構造函數參數傳遞到控制器中。

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

上述代碼是依賴項注入的範例。 我們不允許控制器創建其依賴項(工作單位),但將依賴項注入控制器。 在 MVC 專案中,通常使用自訂控制器工廠與控制反轉 (IoC) 容器一起使用,以自動執行依賴項注入。 這些主題超出了本文的範圍,但您可以通過關注本文末尾的引用來閱讀更多內容。

我們可以用於測試的假工作實現單元可能如下所示。

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

請注意,假工作單元會公開已提交的屬性。 有時將功能添加到便於測試的假類中非常有用。 在這種情況下,通過檢查"提交"屬性,很容易觀察代碼是否提交工作單元。

我們還需要一個假的 IObjectSet&lt; &gt; T 來將員工和 TimeCard 物件保留在記憶體中。 我們可以使用泛型提供單個實現。

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

此測試將其大部分工作雙重委託給基礎哈希集&lt;&gt;T 物件。 請注意,IObjectSet&lt; &gt; T 需要一個泛型約束,強制 T 作為類別(引用類型),&lt;並且&gt;還強制我們 實現 IQuery T 。 使用標準 LINQ 運算子 AsQuery 使記憶體中集合&lt;&gt;顯示為 IQuery T 是很容易的。

### <a name="the-tests"></a>測試

傳統的單元測試將使用單個測試類來保存單個 MVC 控制器中所有操作的所有測試。 我們可以使用我們構建的記憶體中的假,編寫這些測試或任何類型的單元測試。 但是,在本文中,我們將避免採用單片式測試類方法,而是將測試分組以專注於特定的功能。例如,"創建新員工"可能是我們想要測試的功能,因此我們將使用單個測試類來驗證負責創建新員工的單個控制器操作。

對於所有這些細粒度測試類,我們需要一些常見的設置代碼。 例如,我們總是需要創建記憶體中存儲庫和假工作單元。 我們還需要一個員工控制器的實例,並注入假的工作單元。 我們將使用基類跨測試類共用此公共設置代碼。

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

我們在基類中使用的「物件母體」是創建測試數據的一種常見模式。 物件母體包含用於實例化測試實體的工廠方法,用於跨多個測試夾具使用。

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

我們可以將員工控制器測試Base作為許多測試夾具的基礎類(見圖 3)。 每個測試夾具將測試特定的控制器操作。 例如,一個測試夾具將側重於測試 HTTP GET 請求期間使用的「創建」操作(以顯示創建員工的檢視),其他韌體將側重於 HTTP POST 請求中使用的「創建」操作(以獲取使用者提交的資訊以創建員工)。 每個派生類只負責其特定上下文中所需的設置,並提供驗證其特定測試上下文結果所需的斷言。

![埃夫·test_03](~/ef6/media/eftest-03.png)

**圖 3**

此處介紹的命名約定和測試樣式對於可測試代碼來說並不是必需的,這隻是一種方法。 圖 4 顯示了 Visual Studio 2010 的 Jet Brains Resharper 測試運行外掛程式中運行的測試。

![埃夫test_04](~/ef6/media/eftest-04.png)

**圖 4**

使用處理共用設置代碼的基類時,每個控制器操作的單位測試都很小且易於編寫。 測試將快速執行(因為我們正在執行記憶體中操作),並且不應因為不相關的基礎結構或環境問題而失敗(因為我們隔離了被測單元)。

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

在這些測試中,基類執行大多數設置工作。 請記住,基類構造函數創建記憶體中存儲庫、假工作單元和 EmployController 類的實例。 測試類派生自此基類,並側重於測試 Create 方法的具體細節。 在這種情況下,具體情況可歸結為您將在任何單元測試過程中看到的「安排、操作和斷言」步驟:

-   創建新的「員工」物件以模擬傳入數據。
-   調用"創建員工控制程式"的操作並傳遞給新員工。
-   驗證"創建"操作生成預期結果(員工顯示在存儲庫中)。

我們構建的內容允許我們測試任何員工控制器操作。 例如,當我們為"員工"控制器的 Index 操作編寫測試時,我們可以從測試基類繼承,為測試建立相同的基本設置。 再次,基類將創建記憶體中存儲庫、假工作單元和員工控制器的實例。 索引操作的測試只需要專注於調用 Index 操作並測試操作返回的模型的品質。

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

我們使用記憶體中假體建立測試測試測試軟體*的狀態*。 例如,在測試「創建」操作時,我們希望在創建操作執行後檢查存儲庫的狀態 - 儲存庫是否留住新員工?

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

稍後我們將介紹基於交互的測試。 基於交互的測試將詢問被測代碼是否在我們的物件上調用了正確的方法並傳遞了正確的參數。 現在,我們將在封面上移動另一種設計模式 - 延遲負載。

## <a name="eager-loading-and-lazy-loading"></a>熱裝和延遲載入

在 mVC Web 應用程式ASP.NET的某個時刻,我們可能希望顯示員工的資訊,並包括員工的相關時間卡。 例如,我們可能有一個時間卡摘要顯示,顯示員工的姓名和系統中的考勤卡總數。 我們可以採取幾種方法來實現此功能。

### <a name="projection"></a>投射

創建摘要的一種簡單方法是構建一個模型,專門用於要在視圖中顯示的資訊。 在這種情況下,模型可能如下所示。

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

請注意,員工摘要視圖模型不是實體 ,換句話說,它不是我們想要保留在資料庫中的內容。 我們只會使用此類以強類型方式將數據隨機排列到檢視中。 視圖模型類似於數據傳輸物件 (DTO),因為它不包含任何行為(沒有方法) - 僅包含屬性。 屬性將保存我們需要移動的數據。 使用 LINQ 的標準投影運算符 - Select 運算符,可以輕鬆實例化此檢視模型。

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

上述代碼有兩個值得注意的功能。 首先 , 程式碼很容易測試, 因為它仍然易於觀察和隔離. Select 運算符與針對實際工作單元的記憶體假一樣出色。

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

第二個值得注意的特徵是,代碼如何允許 EF4 生成單個高效的查詢,以組合員工和時卡資訊。 我們將員工資訊和工時卡資訊載入到同一物件中,而無需使用任何特殊的 API。 代碼僅表示它使用標準 LINQ 運算元對記憶體中數據源和遠端數據來源執行所需的資訊。 EF4 能夠將 LINQ\#查詢和 C 編譯器生成的運算式樹轉換為單個高效的 T-SQL 查詢。

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

有時,我們不想使用視圖模型或 DTO 物件,而是使用實際實體。 當我們知道我們需要員工*和員工*的時間卡時,我們可以以不顯眼和高效的方式急切地載入相關數據。

### <a name="explicit-eager-loading"></a>明確渴望載入

當我們想要急切地載入相關的實體資訊時,我們需要一些業務邏輯機制(或在這種情況下,控制器操作邏輯)來表達對存儲庫的需求。 EF4 物件&lt;&gt;查詢 T 類定義一個 Include 方法,以指定在查詢期間要檢索的相關物件。 請記住 EF4 物件上下&lt;文通過&gt;&lt;從&gt;物件查詢 T 繼承的具體 ObjectSet T 類公開實體。如果我們在控制器操作中使用&lt;ObjectSet&gt;T引用,我們可以編寫以下代碼來為每個員工指定熱切的時卡資訊負載。

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

但是,由於我們試圖保持代碼的可測試性,因此我們不會在工作類的實際單元&lt;&gt;之外 公開ObjectSet T。 相反,我們&lt;依賴於 IObjectSet&gt;T 介面,該介面更易於偽造,但 IObjectSet&lt;T&gt;不定義 Include 方法。 LINQ 的優點是我們可以創建自己的"包括"運算符。

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

請注意,此包含運算元定義&lt;為 I 可查詢&gt;T 而不是 IObjectSet&lt;T&gt;的擴充方法。 這&lt;使我們能夠將該方法用於更廣泛的可能類型,包括 IQuery T、IObjectSet&gt;&lt;T、ObjectQuery&gt;&lt;T&gt;和&lt;&gt;Objectset T 。 如果基礎序列不是真正的 EF4&lt;物件查詢&gt;T, 則不會造成任何傷害,並且 Include 運算符是 no-op。 如果基礎序列*是*&lt;ObjectQuery&gt;T( 或從 ObjectQuery&lt;&gt;T 派生),則 EF4 將看到我們對其他資料的要求,並制定正確的 SQL 查詢。

有了這個新的運算符,我們可以顯式要求從存儲庫中載入時間卡資訊。

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

當對實際物件上下文運行時,代碼將生成以下單個查詢。 查詢在一次行程中從資料庫中收集足夠的資訊,以實現員工物件並完全填充其 TimeCard 屬性。

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

好消息是操作方法內的代碼仍然完全可測試。 我們不需要為我們的假貨提供任何其他功能來支援 Include 運算符。 壞消息是,我們必須在代碼內使用 Include 運算符,以保持持久性無知。 這是構建可測試代碼時需要評估的權衡類型的範例。 有時,您需要讓持久性問題洩漏到存儲庫抽象之外,以滿足性能目標。

熱裝的替代是延遲載入。 延遲載入意味著我們不需要業務*not*代碼來顯式聲明關聯數據的要求。 相反,我們在應用程式中使用我們的實體,如果需要其他數據,實體框架將按需載入數據。

### <a name="lazy-loading"></a>消極式載入

很容易想像一個場景,即我們不知道業務邏輯需要哪些數據。 我們可能知道邏輯需要員工物件,但我們可能分支到不同的執行路徑,其中某些路徑需要員工提供時卡資訊,而有些路徑不需要。 像這樣的方案非常適合隱式延遲載入,因為數據會根據需要神奇地顯示。

延遲載入(也稱為延遲載入)確實對我們的實體物件提出了一些要求。 具有真正持久性無知的 POCO 不會面臨來自持久性層的任何要求,但真正的持久性無知幾乎不可能實現。相反,我們測量相對程度的持久性無知。 如果我們需要從面向持久性的基類繼承或使用專用集合在 POCO 中實現延遲載入,那將是不幸的。 幸運的是,EF4 具有侵入性較低的解決方案。

### <a name="virtually-undetectable"></a>幾乎偵測不到

使用 POCO 物件時,EF4 可以動態生成實體的運行時代理。 這些代理無形地包裝了具體化的 POCO,並通過攔截每個屬性獲取和設置操作來執行其他工作來提供其他服務。 其中一項服務是我們要查找的延遲載入功能。 另一個服務是一種有效的更改跟蹤機制,它可以記錄程式何時更改實體的屬性值。 儲存變更方法期間,ObjectContext 使用更改清單來保留使用 UPDATE 命令的任何已修改實體。

但是,要使這些代理正常工作,他們需要一種方法來挂鉤到實體上獲取和設置操作,而代理通過重寫虛擬成員來實現此目標。 因此,如果我們想要隱式延遲載入和高效的更改跟蹤,我們需要回到 POCO 類定義並將屬性標記為虛擬。

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

我們仍然可以說,員工實體大多是堅持無知。 唯一的要求是使用虛擬成員,這不會影響代碼的可測試性。 我們不需要從任何特殊的基類派生,甚至不需要使用專用於延遲載入的特殊集合。 如代碼所示,任何實現&lt;ICollection&gt;T 的類都可以保留相關實體。

在我們的工作單元內,我們還需要進行一些小的變化。 預設情況下,當直接使用 ObjectContext 物件時,延遲載入*處於關閉狀態*。 我們可以在 ContextOptions 屬性上設置一個屬性,以啟用延遲載入,如果我們想要在任何地方啟用延遲載入,我們可以在實際工作單元中設置此屬性。

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

啟用隱式延遲載入後,應用程式代碼可以使用員工和員工關聯的時間卡,同時對 EF 載入額外資料所需的工作一無所知。

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

延遲載入使應用程式代碼更易於編寫,並且使用代理魔法,程式碼保持完全可測試。 工作單元的記憶體中偽造可以簡單地在測試期間在需要時預載入帶有相關數據的假實體。

此時,我們將從使用&lt;IObjectSet&gt;T 構建儲存庫轉向我們的注意力,並查看抽象來隱藏持久性框架的所有跡象。

## <a name="custom-repositories"></a>自訂儲存函式庫

當我們在本文中首次介紹工作設計單元模式時,我們提供了一些示例代碼,說明工作單元的外觀。 讓我們使用我們一直在處理的員工和員工時卡方案重新呈現這一原始想法。

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

此工作單元與我們在上一節中創建的工作單元之間的主要區別是此工作單元不使用 EF4 框架中的任何抽象(沒有&lt;IObjectSet&gt; T)。 IObjectSet&lt; &gt; T 作為儲存庫介面工作良好,但它公開的 API 可能不能完全與我們的應用程式需求一致。 在即將採用的方法中,我們將使用自定義&lt;IRepository&gt;T 抽象表示儲存庫。

許多遵循測試驅動設計、行為驅動設計和域驅動方法設計的開發人員都更喜歡&lt;IRepository&gt;T 方法,原因有多種。 首先,IRepository&lt;&gt;T 介面表示"反腐敗"層。 正如 Eric Evans 在他的域驅動設計書中描述的那樣,反腐敗層使域代碼遠離基礎結構 API,如持久性 API。 其次,開發人員可以將方法構建到存儲庫中,以滿足應用程序的確切需求(如編寫測試時發現的那樣)。 例如,我們可能需要使用 ID 值查找單個實體,以便我們可以將 FindById 方法添加到儲存庫介面。我們的&lt;IRepository&gt; T 定義如下所示。

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

請注意,我們將回到使用&lt;IQuery&gt;T 介面來公開實體集合。 IQueryT&lt;&gt;允許 LINQ 運算式樹流入 EF4 提供程式,並賦予提供程式查詢的整體檢視。 第二個選項是返回 IE55t&lt;&gt;T ,這意味著 EF4 LINQ 提供程式將只看到儲存庫內生成的運算式。 在存儲庫之外完成的任何分組、排序和投影將不會組成發送到資料庫的 SQL 命令,這可能會損害性能。 另一方面,僅返回 IE550t&lt;&gt;結果的儲存庫永遠不會讓您使用新的 SQL 命令感到驚訝。 這兩種方法都工作,並且兩種方法都是可測試的。

使用泛型和 EF4 ObjectContext&lt;&gt;API 提供 IRepository T 介面的單個實現非常簡單。

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

IRepository&lt;&gt;T 方法為我們提供了對查詢的一些附加控制,因為客戶端必須調用方法才能訪問實體。 在該方法內,我們可以提供額外的檢查和LINQ運算符來強制執行應用程式約束。 請注意,介面對泛型類型參數有兩個約束。 第一個約束是&lt;ObjectSet&gt;T 所需的類缺點,第二個約束強制我們的實體實現 IEntity - 為應用程式創建的抽象。 IEntity 介面強制實體具有可讀 Id 屬性,然後我們可以在 FindById 方法中使用此屬性。 IEntity 使用以下代碼定義。

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

IEntity 可被視為對持久性無知的輕微違反,因為我們的實體需要實現此介面。 請記住,持久性無知是關於權衡的,對於許多 FindById 功能來說,它將超過介面施加的約束。 該介面對可測試性沒有影響。

實例化即時&lt;IRepository&gt;T 需要 EF4 物件上下文,因此具體的工作實現單元應管理實例化。

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

### <a name="using-the-custom-repository"></a>使用自訂儲存函式庫

使用我們的自訂儲存庫與使用基於&lt;IObjectSet&gt;T 的儲存庫沒有顯著差異。 我們首先需要調用儲存庫的方法來獲取 IQuery&lt;&gt;T 引用,而不是將 LINQ 運算符直接應用於屬性。

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

請注意,我們以前實現的自定義"包括"運算符將工作而不進行更改。 存儲庫的 FindById 方法從嘗試檢索單個實體的操作中刪除重複的邏輯。

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

我們研究過的兩種方法的可測試性沒有顯著差異。 我們可以通過&lt;構建由 HashSet&gt;&lt;員工&gt;支援的混凝土類來提供 IRepository T 的虛假實現-就像我們在上一節中所做的那樣。 但是,一些開發人員更喜歡使用類比物件和類比物件框架,而不是構建假物。 我們將在下一節中介紹使用模擬來測試我們的實現,並討論類比和假之間的差異。

### <a name="testing-with-mocks"></a>使用模擬測試

構建馬丁·福勒所謂的"雙測試雙"的方法不同。 測試雙精度(如電影特技雙)是您在測試期間為實際生產物件構建為「站立」的物件。 我們創建的記憶體記憶體記憶體庫是與 SQL Server 對話的儲存庫的測試雙精度值。 我們已經看到如何在單元測試期間使用這些測試雙精度值來隔離代碼並保持測試快速運行。

我們構建的測試雙精度值具有實際的工作實現。 在後台,每個物件都存儲一個具體的物件集合,並且在測試期間操作存儲庫時,它們將從此集合中添加和刪除物件。 一些開發人員喜歡用實際代碼和工作實現來構建測試雙精度值。這些測試雙號是我們稱之為*假貨*的。 它們具有工作實現,但它們不夠真實,可用於生產。 假存儲庫實際上不會寫入資料庫。 假 SMTP 伺服器實際上不會透過網路發送電子郵件。

### <a name="mocks-versus-fakes"></a>類比與假像

還有另一種類型的測試雙稱為*模擬*。 雖然假有工作實現,但模擬沒有實現。 在類比物件框架的説明下,我們在運行時構造這些類比物件,並將其用作測試雙精度值。 在本節中,我們將使用開源類比框架 Moq。 下面是使用 Moq 動態為員工存儲庫創建測試雙精度值的簡單示例。

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

我們要求 Moq&lt;提供 IRepository 員工&gt;實現,並動態構建一個。 通過&lt;存取&gt;&lt;&gt;Mock T 物件的 Object 屬性,我們可以造訪實現 IRepository 員工的物件。 它是這個內部對象,我們可以傳遞到我們的控制器,他們不知道這是一個測試雙或真正的存儲庫。 我們可以調用對象上的方法,就像在具有實際實現的對象上調用方法一樣。

您必須想知道類比儲存庫在調用 Add 方法時將執行什麼操作。 由於類比物件後面沒有實現,因此 Add 不執行任何操作。 幕後沒有像我們寫的假貨那樣的具體收藏,所以員工被丟棄了。 FindById 的返回值如何? 在這種情況下,模擬物件執行它唯一能做的,即返回預設值。 由於我們要返回引用類型(員工),因此返回值為null值。

類比聽起來可能毫無價值;然而,還有兩個類比功能,我們還沒有談到。 首先,Moq 框架記錄對模擬對象進行的所有調用。 在代碼的後面部分,我們可以問 Moq 是否有人調用了 Add 方法,或者是否有人調用了 FindById 方法。 稍後我們將瞭解如何在測試中使用此"黑匣子"錄製功能。

第二個偉大的功能是我們如何使用 Moq 編程一個模擬物件*與期望*。 期望值告訴類比物件如何回應任何給定的交互。 例如,我們可以將期望值程式設計到類比中,並告訴它當有人調用 FindById 時返回員工物件。 Moq 框架使用安裝程式 API 和 lambda 運算式來對這些期望進行程式設計。

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

在此示例中,我們要求 Moq 動態構建存儲庫,然後我們對儲存庫進行程式設計。 當某人調用傳遞值為 5 的 FindById 方法時,期望告訴模擬物件返回 Id 值為 5 的新員工物件。 此測試通過,我們不需要建構一個完整的實作假 IRepository&lt;T&gt;。

讓我們重溫我們之前編寫的測試,並重新編寫它們以使用類比而不是假測試。 與以前一樣,我們將使用基類來設置所有控制器測試所需的通用基礎結構部分。

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

設置代碼大致相同。 我們將使用 Moq 來建構模擬物件,而不是使用假物。 基類安排類比工作單元在代碼調用"員工"屬性時返回類比存儲庫。 類比設置的其餘部分將發生在專用於每個特定場景的測試夾具內。 例如,當操作調用類比儲存庫的 FindAll 方法時,Index 操作的測試韌體將設置類比儲存庫以返回員工清單。

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

除了期望值之外,我們的測試看起來與之前的測試相似。 然而,隨著類比框架的錄製能力,我們可以從不同的角度進行測試。 我們將在下一節中介紹這一新觀點。

### <a name="state-versus-interaction-testing"></a>狀態與互動測試

您可以使用不同的技術來測試使用類比物件的軟體。 一種方法是使用基於狀態的測試,這就是我們在本文中到目前為止所做的。 基於狀態的測試對軟體的狀態進行斷言。 在上一次測試中,我們在控制器上調用了一個操作方法,並斷言它應該構建的模型。 下面是測試狀態的其他一些範例:

-   在執行"創建"後驗證存儲庫包含新員工物件。
-   在索引執行後驗證模型包含所有員工的清單。
-   在執行"刪除"後,驗證存儲庫不包含給定員工。

使用模擬物件會看到的另一種方法是驗證*互動*。 雖然基於狀態的測試對物件的狀態進行斷言,但基於交互的測試會斷言物件如何交互。 例如：

-   在執行"創建"時驗證控制器呼叫儲存庫的 Add 方法。
-   當索引執行時,驗證控制器調用存儲庫的 FindAll 方法。
-   驗證控制器調用工作單元的提交方法,以在"編輯"執行時保存更改。

交互測試通常需要較少的測試數據,因為我們不是在集合內部進行戳造和驗證計數。 例如,如果我們知道「詳細資訊」操作調用具有正確值的存儲庫的 FindById 方法,則操作可能運行正常。 我們可以驗證此行為,而無需設置任何測試數據從 FindById 返回。

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

上述測試夾具中唯一需要的設置是基類提供的設置。 當我們調用控制器操作時,Moq 將記錄與類比儲存庫的交互。 使用 Moq 的驗證 API,我們可以詢問 Moq 控制器是否使用正確的 ID 值呼叫 FindById。 如果控制器未調用方法,或者使用意外參數值調用該方法,則 Verify 方法將引發異常,並且測試將失敗。

下面是另一個範例,用於驗證"創建操作在當前工作單元上調用提交"

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

交互測試的一個危險是過度指定交互的傾向。 類比物件記錄和驗證與模擬物件的每次交互的能力並不意味著測試應嘗試驗證每個交互。 某些互動是實現詳細資訊,您只應驗證滿足當前測試*所需的*交互。

在類比或假貨之間進行選擇很大程度上取決於您測試的系統以及您的個人(或團隊)偏好。 Mock 物件可以大大減少實現測試雙精度值所需的代碼量,但並不是每個人都會舒適地實現程式設計期望和驗證交互。

## <a name="conclusions"></a>結論

在本文中,我們演示了幾種在使用ADO.NET實體框架進行數據持久性時創建可測試代碼的方法。 我們可以利用內置的抽象&lt;,如IObjectSet&gt;T, 或者創建我們自己的抽象,如IRepositoryT。&gt; &lt;在這兩種情況下,ADO.NET實體框架 4.0 中的 POCO 支援允許這些抽象的消費者保持持久無知和高度可測試。 隱式延遲載入等其他 EF4 功能允許業務和應用程式服務代碼工作,而不必擔心關係數據存儲的詳細資訊。 最後,我們創建的抽象在單元測試中很容易類比或偽造,我們可以使用這些測試雙精度值來實現快速運行、高度隔離和可靠的測試。

### <a name="additional-resources"></a>其他資源

-   羅伯特·馬丁,"[單一責任原則](https://www.objectmentor.com/resources/articles/srp.pdf)"
-   馬丁·福勒[Catalog of Patterns](https://www.martinfowler.com/eaaCatalog/index.html),*企業應用程序體系結構模式模式目錄*
-   格裡芬·卡普里奧,"[依賴注入](https://msdn.microsoft.com/magazine/cc163739.aspx)"
-   數據可程式設計性部落格,演[練:使用實體框架 4.0 的測試驅動開發](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)"。
-   數據可程式設計性博客,"[使用具有實體框架 4.0 的儲存庫和工作單元模式](https://docs.microsoft.com/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)"
-   亞倫·詹森,"[介紹機器規格](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)"
-   埃裡克·李[,"BDD與MSTest"](https://saintgimp.org/2009/01/20/bdd-with-mstest/)
-   埃裡克·埃文斯,"[領域驅動設計](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)"
-   馬丁·福勒,"[模仿不是斯圖布斯](https://martinfowler.com/articles/mocksArentStubs.html)"
-   馬丁·福勒,"[測試雙](https://martinfowler.com/bliki/TestDouble.html)"
-   [莫克](https://code.google.com/p/moq/)

### <a name="biography"></a>傳記

Scott Allen 是多元視的技術人員,也是 OdeToCode.com的創始人。 在 15 年的商業軟體開發中,Scott 一直致力於從 8 位嵌入式裝置到高度可擴展 ASP.NET Web 應用程式等所有解決方案。 您可以在 OdeToCode 的部落[https://twitter.com/OdeToCode](https://twitter.com/OdeToCode)格上或在推特上 聯繫斯科特。
