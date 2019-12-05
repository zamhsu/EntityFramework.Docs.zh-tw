---
title: EF Core 2.0 中的新功能 - EF Core
author: divega
ms.date: 02/20/2018
ms.assetid: 2CB5809E-0EFB-44F6-AF14-9D5BFFFBFF9D
uid: core/what-is-new/ef-core-2.0
ms.openlocfilehash: 83f6b819409d502dba17a678d44a0746a4a77f4b
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824883"
---
# <a name="new-features-in-ef-core-20"></a>EF Core 2.0 中的新功能

## <a name="net-standard-20"></a>.NET Standard 2.0

EF Core 現在的目標是 .NET Standard 2.0，表示它可以處理 .NET Core 2.0、.NET Framework 4.6.1 以及其他實作 .NET Standard 2.0 的程式庫。
如需所支援實作的詳細資料，請參閱[支援的 .NET 實作](../platforms/index.md)。

## <a name="modeling"></a>模型化

### <a name="table-splitting"></a>資料表分割

現在可以將兩個或多個實體類型對應至相同的資料表；其中，將會共用主要索引鍵資料行，而且每個資料列都會對應至兩個或多個實體。

若要使用資料表分割，必須在共用資料表的所有實體類型之間設定識別關聯性 (其中外部索引鍵屬性形成主索引鍵)：

``` csharp
modelBuilder.Entity<Product>()
    .HasOne(e => e.Details).WithOne(e => e.Product)
    .HasForeignKey<ProductDetails>(e => e.Id);
modelBuilder.Entity<Product>().ToTable("Products");
modelBuilder.Entity<ProductDetails>().ToTable("Products");
```

如需此功能的詳細資訊，請詳讀[資料表分割章節](xref:core/modeling/table-splitting)。

### <a name="owned-types"></a>擁有的類型

擁有的實體類型可以與另一個擁有的實體類型共用相同的 .NET 類型，但因為它不能只由 .NET 類型識別，所以必須從另一個實體類型進行導覽。 包含定義導覽的實體是擁有者。 查詢擁有者時，預設會包含擁有的類型。

依照慣例，將為擁有的類型建立陰影主索引鍵，而且會使用資料表分割將它對應至與擁有者相同的資料表。 這允許使用擁有的類型，其類似在 EF6 中如何使用複雜類型：

``` csharp
modelBuilder.Entity<Order>().OwnsOne(p => p.OrderDetails, cb =>
    {
        cb.OwnsOne(c => c.BillingAddress);
        cb.OwnsOne(c => c.ShippingAddress);
    });

public class Order
{
    public int Id { get; set; }
    public OrderDetails OrderDetails { get; set; }
}

public class OrderDetails
{
    public StreetAddress BillingAddress { get; set; }
    public StreetAddress ShippingAddress { get; set; }
}

public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
}
```

如需這項功能的詳細資訊，請閱讀[擁有的實體類型](xref:core/modeling/owned-entities)一節。

### <a name="model-level-query-filters"></a>模型層級查詢篩選

EF Core 2.0 包含我們稱為「模型層級查詢篩選」的新功能。 此功能允許直接在中繼資料模型 (通常是在 OnModelCreating 中) 的「實體類型」上定義 LINQ 查詢述詞 (布林運算式通常會傳遞至 LINQ Where 查詢運算子)。 這類篩選會自動套用至任何涉及這些「實體類型」(包含間接參考的「實體類型」) 的 LINQ 查詢 (例如，使用 Include 或直接導覽屬性參考)。 此功能的一些常見應用如下：

- 虛刪除 -「實體類型」定義 IsDeleted 屬性。
- 多租用戶 -「實體類型」定義 TenantId 屬性。

以下簡單範例示範上述兩個案例的功能：

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    public int TenantId { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Post>().HasQueryFilter(
            p => !p.IsDeleted
            && p.TenantId == this.TenantId);
    }
}
```

我們會定義模型層級篩選，以實作 `Post` 實體類型執行個體的多租用戶和虛刪除。 請注意，使用 `DbContext` 實例層級屬性： `TenantId`。 模型層級篩選會使用正確內容執行個體 (即執行查詢的內容執行個體詢) 中的值。

可能會使用 IgnoreQueryFilters() 運算子停用個別 LINQ 查詢的篩選。

#### <a name="limitations"></a>限制

- 不允許導覽參考。 此功能可能是根據意見所新增。
- 篩選只能定義於階層的 root 實體類型上。

### <a name="database-scalar-function-mapping"></a>資料庫純量函式對應

EF Core 2.0 包含 [Paul Middleton](https://github.com/pmiddleton) 的重要比重，這會啟用資料庫純量函式與方法虛設常式的對應，以將它們用於 LINQ 查詢並轉譯為 SQL。

以下簡短描述說明如何使用此功能：

在 `DbContext` 上宣告靜態方法，並將它加上 `DbFunctionAttribute` 註釋：

``` csharp
public class BloggingContext : DbContext
{
    [DbFunction]
    public static int PostReadCount(int blogId)
    {
        throw new NotImplementedException();
    }
}
```

這類方法會自動予以註冊。 註冊後，LINQ 查詢中的方法呼叫可轉譯成 SQL 中的函式呼叫：

``` csharp
var query =
    from p in context.Posts
    where BloggingContext.PostReadCount(p.Id) > 5
    select p;
```

請注意幾件事：

- 依照慣例，在產生 SQL 時，會使用方法的名稱做為函式的名稱（在此案例中為使用者定義函數），但是您可以在方法註冊期間覆寫名稱和架構。
- 目前僅支援純量函數。
- 您必須在資料庫中建立對應函式。 EF Core 遷移並不會負責建立它。

### <a name="self-contained-type-configuration-for-code-first"></a>Code First 的獨立類型組態

在 EF6 中，可能已封裝衍生自 *EntityTypeConfiguration* 之特定實體類型的 Code First 組態。 在 EF Core 2.0 中，我們將重新使用這種模式：

``` csharp
class CustomerConfiguration : IEntityTypeConfiguration<Customer>
{
    public void Configure(EntityTypeBuilder<Customer> builder)
    {
        builder.HasKey(c => c.AlternateKey);
        builder.Property(c => c.Name).HasMaxLength(200);
    }
}

...
// OnModelCreating
builder.ApplyConfiguration(new CustomerConfiguration());
```

## <a name="high-performance"></a>高效能

### <a name="dbcontext-pooling"></a>DbContext 共用

在 ASP.NET Core 應用程式中使用 EF Core 的基本模式，通常包含將自訂 DbContext 類型註冊到相依性插入系統，以及稍後透過控制器中的建構函式參數來取得該類型的執行個體。 這表示會為每個要求建立新的 DbContext 執行個體。

在 2.0 版中，我們引進在相依性插入中註冊自訂 DbContext 類型的新方式，而相依性插入會以透明方式引進可重複使用 DbContext 執行個體的集區中。 若要使用 DbContext 共用，請在服務註冊期間使用 `AddDbContextPool`，而非 `AddDbContext`：

``` csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

如果使用這種方法，則在控制器要求 DbContext 執行個體時，我們會先檢查集區中是否有執行個體可用。 要求處理完成之後，會重設執行個體上的任何狀態，並將執行個體本身傳回到集區。

這在概念上類似連線共用如何在 ADO.NET 提供者中操作，而且優點是節省 DbContext 執行個體的一些初始化成本。

### <a name="limitations"></a>限制

新的方法引進 DbContext 的 `OnConfiguring()` 方法中可進行作業的一些限制。

> [!WARNING]  
> 如果您在不應該於要求間共用的衍生 DbContext 類別中維護自己的狀態 (例如私用欄位)，請避免使用 DbContext 共用。 EF Core 只會重設在將 DbContext 執行個體新增至集區之前所知道的狀態。

### <a name="explicitly-compiled-queries"></a>明確地編譯查詢

這是第二個選擇加入的效能功能，專為高延展案例提供助益而設計。

舊版 EF 以及 LINQ to SQL 中已提供手動或明確已編譯的查詢 API，允許應用程式快取查詢的轉譯，這樣只要將它們計算一次，就能執行多次。

一般而言，雖然 EF Core 可以根據查詢運算式的雜湊表示法來自動編譯並快取查詢 ，但是可以使用這項機制透過不計算雜湊和快取查閱來提升少許效能，以允許應用程式透過委派叫用來使用已編譯的查詢。

``` csharp
// Create an explicitly compiled query
private static Func<CustomerContext, int, Customer> _customerById =
    EF.CompileQuery((CustomerContext db, int id) =>
        db.Customers
            .Include(c => c.Address)
            .Single(c => c.Id == id));

// Use the compiled query by invoking it
using (var db = new CustomerContext())
{
   var customer = _customerById(db, 147);
}
```

## <a name="change-tracking"></a>變更追蹤

### <a name="attach-can-track-a-graph-of-new-and-existing-entities"></a>附加可以追蹤全新和現有實體的圖形

EF Core 支援透過不同的機制來自動產生索引鍵值。 使用此功能時，如果重要屬性是 CLR 預設值，則會產生值：通常是零或 Null。 這表示可以將實體的圖表傳遞至 `DbContext.Attach` 或 `DbSet.Attach`，而且 EF Core 會將已設定索引鍵的實體標示為 `Unchanged`，同時將未設定索引鍵的實體標示為 `Added`。 使用產生的索引鍵時，這可以輕鬆地附加混合全新和現有實體的圖形。 `DbContext.Update` 和 `DbSet.Update` 的運作方式相同，差別在於將設定索引鍵的實體標示為 `Modified`，而不是 `Unchanged`。

## <a name="query"></a>查詢

### <a name="improved-linq-translation"></a>改良的 LINQ 轉譯

讓更多查詢順利執行，並在資料庫中評估更多邏輯 (而不是在記憶體中) 以及從資料庫擷取較少的資料。

### <a name="groupjoin-improvements"></a>GroupJoin 改善

這項工作可改善針對群組聯結所產生的 SQL。 群組聯結最常是選擇性導覽屬性的子查詢結果。

### <a name="string-interpolation-in-fromsql-and-executesqlcommand"></a>FromSql 和 ExecuteSqlCommand 中的字串插值

C# 6 已引進「字串插值」，此功能允許 C# 運算式直接內嵌在字串常值中，並提供不錯的方式在執行階段建置字串。 在 EF Core 2.0 中，我們已在接受原始 SQL 字串的兩個主要 API 中新增內插字串的特殊支援：`FromSql` and `ExecuteSqlCommand`. 這項新的C#支援可讓您以「安全」的方式使用字串內插補點。 也就是說，可防止在執行階段動態建構 SQL 時可能發生的常見 SQL 插入錯誤。

請看以下範例：

``` csharp
var city = "London";
var contactTitle = "Sales Representative";

using (var context = CreateContext())
{
    context.Set<Customer>()
        .FromSql($@"
            SELECT *
            FROM ""Customers""
            WHERE ""City"" = {city} AND
                ""ContactTitle"" = {contactTitle}")
            .ToArray();
  }
```

在此範例中，有兩個以 SQL 格式字串內嵌的變數。 EF Core 會產生下列 SQL：

```sql
@p0='London' (Size = 4000)
@p1='Sales Representative' (Size = 4000)

SELECT *
FROM ""Customers""
WHERE ""City"" = @p0
    AND ""ContactTitle"" = @p1
```

### <a name="effunctionslike"></a>EF.Functions.Like()

我們已新增 EF.Functions 屬性，而 EF Core 或提供者用來定義可對應至資料庫函式或運算子的方法，以在 LINQ 查詢 中予以叫用。 這種方法的第一個範例是 Like()：

``` csharp
var aCustomers =
    from c in context.Customers
    where EF.Functions.Like(c.Name, "a%")
    select c;
```

請注意，Like() 隨附記憶體中實作，這在處理記憶體中資料庫或用戶端需要進行述詞評估時都很方便使用。

## <a name="database-management"></a>資料庫管理

### <a name="pluralization-hook-for-dbcontext-scaffolding"></a>DbCoNtext 樣板的複數表示勾點

EF Core 2.0 引進新的 *IPluralizer* 服務，以用來將實體類型名稱單數化，並將 DbSet 名稱複數化。 預設實作是不操作，因此這只是人員可以輕鬆插入其專屬 pluralizer 的攔截。

以下是開發人員在其專屬 pluralizer 中攔截的方式：

``` csharp
public class MyDesignTimeServices : IDesignTimeServices
{
    public void ConfigureDesignTimeServices(IServiceCollection services)
    {
        services.AddSingleton<IPluralizer, MyPluralizer>();
    }
}

public class MyPluralizer : IPluralizer
{
    public string Pluralize(string name)
    {
        return Inflector.Inflector.Pluralize(name) ?? name;
    }

    public string Singularize(string name)
    {
        return Inflector.Inflector.Singularize(name) ?? name;
    }
}
```

## <a name="others"></a>其他

### <a name="move-adonet-sqlite-provider-to-sqlitepclraw"></a>將 ADO.NET SQLite 提供者移至 SQLitePCL.raw

這可在 Microsoft.Data.Sqlite 中提供更穩固的方案，以將原生 SQLite 二進位檔散發到不同的平台。

### <a name="only-one-provider-per-model"></a>一個模型只會有一個提供者

大幅增加提供者如何與模型互動，以及簡化慣例、註釋和 Fluent API 如何與不同的提供者搭配運作。

EF Core 2.0 現在會為使用的每個不同提供者建置不同的 [IModel](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/Metadata/IModel.cs)。 應用程式通常可以看到這項作業。 這已加速簡化較低階中繼資料 API；因此，任何對*一般關聯式中繼資料概念*的存取一律是透過 `.Relational` 呼叫來進行，而非 `.SqlServer`、`.Sqlite` 等等。

### <a name="consolidated-logging-and-diagnostics"></a>匯總記錄和診斷

記錄 (根據 ILogger) 和診斷 (根據 DiagnosticSource) 機制現在共用更多程式碼。

在 2.0 中，已經變更傳送至 ILogger 之訊息的事件識別碼。 在 EF Core 程式碼中，事件識別碼現在是唯一的。 例如，這些訊息現在也遵循 MVC 所使用結構化記錄的標準模式。

記錄器類別也已經變更。 現在已有一組類別可透過 [DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/DbLoggerCategory.cs) 進行存取。

DiagnosticSource 事件現在會使用相同的事件識別碼名稱作為對應的 `ILogger` 訊息。
