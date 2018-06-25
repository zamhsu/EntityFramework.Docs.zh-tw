---
title: EF Core 2.0 中的新功能 - EF Core
author: divega
ms.author: divega
ms.date: 02/20/2018
ms.assetid: 2CB5809E-0EFB-44F6-AF14-9D5BFFFBFF9D
ms.technology: entity-framework-core
uid: core/what-is-new/ef-core-2.0
ms.openlocfilehash: 02d0b6fe2956e819e08e08c9a0658008abd36c34
ms.sourcegitcommit: b2d94cebdc32edad4fecb07e53fece66437d1b04
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
ms.locfileid: "29680019"
---
# <a name="new-features-in-ef-core-20"></a><span data-ttu-id="c55c0-102">EF Core 2.0 中的新功能</span><span class="sxs-lookup"><span data-stu-id="c55c0-102">New features in EF Core 2.0</span></span>

## <a name="net-standard-20"></a><span data-ttu-id="c55c0-103">.NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="c55c0-103">.NET Standard 2.0</span></span>
<span data-ttu-id="c55c0-104">EF Core 現在的目標是 .NET Standard 2.0，表示它可以處理 .NET Core 2.0、.NET Framework 4.6.1 以及其他實作 .NET Standard 2.0 的程式庫。</span><span class="sxs-lookup"><span data-stu-id="c55c0-104">EF Core now targets .NET Standard 2.0, which means it can work with .NET Core 2.0, .NET Framework 4.6.1, and other libraries that implement .NET Standard 2.0.</span></span>
<span data-ttu-id="c55c0-105">如需所支援實作的詳細資料，請參閱[支援的 .NET 實作](../platforms/index.md)。</span><span class="sxs-lookup"><span data-stu-id="c55c0-105">See [Supported .NET Implementations](../platforms/index.md) for more details on what is supported.</span></span>

## <a name="modeling"></a><span data-ttu-id="c55c0-106">模型化</span><span class="sxs-lookup"><span data-stu-id="c55c0-106">Modeling</span></span>

### <a name="table-splitting"></a><span data-ttu-id="c55c0-107">資料表分割</span><span class="sxs-lookup"><span data-stu-id="c55c0-107">Table splitting</span></span>

<span data-ttu-id="c55c0-108">現在可以將兩個或多個實體類型對應至相同的資料表；其中，將會共用主要索引鍵資料行，而且每個資料列都會對應至兩個或多個實體。</span><span class="sxs-lookup"><span data-stu-id="c55c0-108">It is now possible to map two or more entity types to the same table where the primary key column(s) will be shared and each row will correspond to two or more entities.</span></span>

<span data-ttu-id="c55c0-109">若要使用資料表分割，必須在共用資料表的所有實體類型之間設定識別關聯性 (其中外部索引鍵屬性形成主索引鍵)：</span><span class="sxs-lookup"><span data-stu-id="c55c0-109">To use table splitting an identifying relationship (where foreign key properties form the primary key) must be configured between all of the entity types sharing the table:</span></span>

``` csharp
modelBuilder.Entity<Product>()
    .HasOne(e => e.Details).WithOne(e => e.Product)
    .HasForeignKey<ProductDetails>(e => e.Id);
modelBuilder.Entity<Product>().ToTable("Products");
modelBuilder.Entity<ProductDetails>().ToTable("Products");
```

### <a name="owned-types"></a><span data-ttu-id="c55c0-110">擁有的類型</span><span class="sxs-lookup"><span data-stu-id="c55c0-110">Owned types</span></span>

<span data-ttu-id="c55c0-111">擁有的實體類型可以與另一個擁有的實體類型共用相同的 CLR 類型，但因為只有根據 CLR 類型並無法識別它，所以必須從另一個實體類型進行導覽。</span><span class="sxs-lookup"><span data-stu-id="c55c0-111">An owned entity type can share the same CLR type with another owned entity type, but since it cannot be identified just by the CLR type there must be a navigation to it from another entity type.</span></span> <span data-ttu-id="c55c0-112">包含定義導覽的實體是擁有者。</span><span class="sxs-lookup"><span data-stu-id="c55c0-112">The entity containing the defining navigation is the owner.</span></span> <span data-ttu-id="c55c0-113">查詢擁有者時，預設會包含擁有的類型。</span><span class="sxs-lookup"><span data-stu-id="c55c0-113">When querying the owner the owned types will be included by default.</span></span>

<span data-ttu-id="c55c0-114">依照慣例，將為擁有的類型建立陰影主索引鍵，而且會使用資料表分割將它對應至與擁有者相同的資料表。</span><span class="sxs-lookup"><span data-stu-id="c55c0-114">By convention a shadow primary key will be created for the owned type and it will be mapped to the same table as the owner by using table splitting.</span></span> <span data-ttu-id="c55c0-115">這允許使用擁有的類型，其類似在 EF6 中如何使用複雜類型：</span><span class="sxs-lookup"><span data-stu-id="c55c0-115">This allows to use owned types similarly to how complex types are used in EF6:</span></span>

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
<span data-ttu-id="c55c0-116">如需這項功能的詳細資訊，請閱讀[擁有的實體類型](xref:core/modeling/owned-entities)一節。</span><span class="sxs-lookup"><span data-stu-id="c55c0-116">Read the [section on owned entity types](xref:core/modeling/owned-entities) for more information on this feature.</span></span>

### <a name="model-level-query-filters"></a><span data-ttu-id="c55c0-117">模型層級查詢篩選</span><span class="sxs-lookup"><span data-stu-id="c55c0-117">Model-level query filters</span></span>

<span data-ttu-id="c55c0-118">EF Core 2.0 包含我們稱為「模型層級查詢篩選」的新功能。</span><span class="sxs-lookup"><span data-stu-id="c55c0-118">EF Core 2.0 includes a new feature we call Model-level query filters.</span></span> <span data-ttu-id="c55c0-119">此功能允許直接在中繼資料模型 (通常是在 OnModelCreating 中) 的「實體類型」上定義 LINQ 查詢述詞 (布林運算式通常會傳遞至 LINQ Where 查詢運算子)。</span><span class="sxs-lookup"><span data-stu-id="c55c0-119">This feature allows LINQ query predicates (a boolean expression typically passed to the LINQ Where query operator) to be defined directly on Entity Types in the metadata model (usually in OnModelCreating).</span></span> <span data-ttu-id="c55c0-120">這類篩選會自動套用至任何涉及這些「實體類型」(包含間接參考的「實體類型」) 的 LINQ 查詢 (例如，使用 Include 或直接導覽屬性參考)。</span><span class="sxs-lookup"><span data-stu-id="c55c0-120">Such filters are automatically applied to any LINQ queries involving those Entity Types, including Entity Types referenced indirectly, such as through the use of Include or direct navigation property references.</span></span> <span data-ttu-id="c55c0-121">此功能的一些常見應用如下：</span><span class="sxs-lookup"><span data-stu-id="c55c0-121">Some common applications of this feature are:</span></span>

- <span data-ttu-id="c55c0-122">虛刪除 -「實體類型」定義 IsDeleted 屬性。</span><span class="sxs-lookup"><span data-stu-id="c55c0-122">Soft delete - An Entity Types defines an IsDeleted property.</span></span>
- <span data-ttu-id="c55c0-123">多租用戶 -「實體類型」定義 TenantId 屬性。</span><span class="sxs-lookup"><span data-stu-id="c55c0-123">Multi-tenancy - An Entity Type defines a TenantId property.</span></span>

<span data-ttu-id="c55c0-124">以下簡單範例示範上述兩個案例的功能：</span><span class="sxs-lookup"><span data-stu-id="c55c0-124">Here is a simple example demonstrating the feature for the two scenarios listed above:</span></span>

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
            && p.TenantId == this.TenantId );
    }
}
```
<span data-ttu-id="c55c0-125">我們會定義模型層級篩選，以實作 ```Post``` 實體類型執行個體的多租用戶和虛刪除。</span><span class="sxs-lookup"><span data-stu-id="c55c0-125">We define a model-level filter that implements multi-tenancy and soft-delete for instances of the ```Post``` Entity Type.</span></span> <span data-ttu-id="c55c0-126">請注意如何使用 DbContext 執行個體層級屬性：```TenantId```。</span><span class="sxs-lookup"><span data-stu-id="c55c0-126">Note the use of a DbContext instance level property: ```TenantId```.</span></span> <span data-ttu-id="c55c0-127">模型層級篩選將使用正確內容執行個體中的值。</span><span class="sxs-lookup"><span data-stu-id="c55c0-127">Model-level filters will use the value from the correct context instance.</span></span> <span data-ttu-id="c55c0-128">也就是執行查詢的項目。</span><span class="sxs-lookup"><span data-stu-id="c55c0-128">I.e. the one that is executing the query.</span></span>

<span data-ttu-id="c55c0-129">可能會使用 IgnoreQueryFilters() 運算子停用個別 LINQ 查詢的篩選。</span><span class="sxs-lookup"><span data-stu-id="c55c0-129">Filters may be disabled for individual LINQ queries using the IgnoreQueryFilters() operator.</span></span>

#### <a name="limitations"></a><span data-ttu-id="c55c0-130">限制</span><span class="sxs-lookup"><span data-stu-id="c55c0-130">Limitations</span></span>

- <span data-ttu-id="c55c0-131">不允許導覽參考。</span><span class="sxs-lookup"><span data-stu-id="c55c0-131">Navigation references are not allowed.</span></span> <span data-ttu-id="c55c0-132">此功能可能是根據意見所新增。</span><span class="sxs-lookup"><span data-stu-id="c55c0-132">This feature may be added based on feedback.</span></span>
- <span data-ttu-id="c55c0-133">篩選只能定義於階層的 root 實體類型上。</span><span class="sxs-lookup"><span data-stu-id="c55c0-133">Filters can only be defined on the root Entity Type of a hierarchy.</span></span>

### <a name="database-scalar-function-mapping"></a><span data-ttu-id="c55c0-134">資料庫純量函式對應</span><span class="sxs-lookup"><span data-stu-id="c55c0-134">Database scalar function mapping</span></span>

<span data-ttu-id="c55c0-135">EF Core 2.0 包含 [Paul Middleton](https://github.com/pmiddleton) 的重要比重，這會啟用資料庫純量函式與方法虛設常式的對應，以將它們用於 LINQ 查詢並轉譯為 SQL。</span><span class="sxs-lookup"><span data-stu-id="c55c0-135">EF Core 2.0 includes an important contribution from [Paul Middleton](https://github.com/pmiddleton) which enables mapping database scalar functions to method stubs so that they can be used in LINQ queries and translated to SQL.</span></span>

<span data-ttu-id="c55c0-136">以下簡短描述說明如何使用此功能：</span><span class="sxs-lookup"><span data-stu-id="c55c0-136">Here is a brief description of how the feature can be used:</span></span>

<span data-ttu-id="c55c0-137">在 `DbContext` 上宣告靜態方法，並將它加上 `DbFunctionAttribute` 註釋：</span><span class="sxs-lookup"><span data-stu-id="c55c0-137">Declare a static method on your `DbContext` and annotate it with `DbFunctionAttribute`:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    [DbFunction]
    public static int PostReadCount(int blogId)
    {
        throw new Exception();
    }
}
```

<span data-ttu-id="c55c0-138">這類方法會自動予以註冊。</span><span class="sxs-lookup"><span data-stu-id="c55c0-138">Methods like this are automatically registered.</span></span> <span data-ttu-id="c55c0-139">註冊後，LINQ 查詢中的方法呼叫可轉譯成 SQL 中的函式呼叫：</span><span class="sxs-lookup"><span data-stu-id="c55c0-139">Once registered, calls to the method in a LINQ query can be translated to function calls in SQL:</span></span>

``` csharp
var query =
    from p in context.Posts
    where BloggingContext.PostReadCount(p.Id) > 5
    select p;
```

<span data-ttu-id="c55c0-140">請注意幾件事：</span><span class="sxs-lookup"><span data-stu-id="c55c0-140">A few things to note:</span></span>

- <span data-ttu-id="c55c0-141">依照慣例，產生 SQL 時，方法的名稱會用作函式的名稱 (在此情況下，為使用者定義函式)，但您可以在方法註冊期間覆寫名稱和結構描述</span><span class="sxs-lookup"><span data-stu-id="c55c0-141">By convention the name of the method is used as the name of a function (in this case a user defined function) when generating the SQL, but you can override the name and schema during method registration</span></span>
- <span data-ttu-id="c55c0-142">目前只支援純量函式</span><span class="sxs-lookup"><span data-stu-id="c55c0-142">Currently only scalar functions are supported</span></span>
- <span data-ttu-id="c55c0-143">您必須在資料庫中建立對應的函式，例如 EF Core 移轉不負責建立它</span><span class="sxs-lookup"><span data-stu-id="c55c0-143">You must create the mapped function in the database, e.g. EF Core migrations will not take care of creating it</span></span>

### <a name="self-contained-type-configuration-for-code-first"></a><span data-ttu-id="c55c0-144">Code First 的獨立類型組態</span><span class="sxs-lookup"><span data-stu-id="c55c0-144">Self-contained type configuration for code first</span></span>

<span data-ttu-id="c55c0-145">在 EF6 中，可能已封裝衍生自 *EntityTypeConfiguration* 之特定實體類型的 Code First 組態。</span><span class="sxs-lookup"><span data-stu-id="c55c0-145">In EF6 it was possible to encapsulate the code first configuration of a specific entity type by deriving from *EntityTypeConfiguration*.</span></span> <span data-ttu-id="c55c0-146">在 EF Core 2.0 中，我們將重新使用這種模式：</span><span class="sxs-lookup"><span data-stu-id="c55c0-146">In EF Core 2.0 we are bringing this pattern back:</span></span>

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

## <a name="high-performance"></a><span data-ttu-id="c55c0-147">高效能</span><span class="sxs-lookup"><span data-stu-id="c55c0-147">High Performance</span></span>

### <a name="dbcontext-pooling"></a><span data-ttu-id="c55c0-148">DbContext 共用</span><span class="sxs-lookup"><span data-stu-id="c55c0-148">DbContext pooling</span></span>

<span data-ttu-id="c55c0-149">在 ASP.NET Core 應用程式中使用 EF Core 的基本模式，通常包含將自訂 DbContext 類型註冊到相依性插入系統，以及稍後透過控制器中的建構函式參數來取得該類型的執行個體。</span><span class="sxs-lookup"><span data-stu-id="c55c0-149">The basic pattern for using EF Core in an ASP.NET Core application usually involves registering a custom DbContext type into the dependency injection system and later obtaining instances of that type through constructor parameters in controllers.</span></span> <span data-ttu-id="c55c0-150">這表示會為每個要求建立新的 DbContext 執行個體。</span><span class="sxs-lookup"><span data-stu-id="c55c0-150">This means a new instance of the DbContext is created for each requests.</span></span>

<span data-ttu-id="c55c0-151">在 2.0 版中，我們引進在相依性插入中註冊自訂 DbContext 類型的新方式，而相依性插入會以透明方式引進可重複使用 DbContext 執行個體的集區中。</span><span class="sxs-lookup"><span data-stu-id="c55c0-151">In version 2.0 we are introducing a new way to register custom DbContext types in dependency injection which transparently introduces a pool of reusable DbContext instances.</span></span> <span data-ttu-id="c55c0-152">若要使用 DbContext 共用，請在服務註冊期間使用 ```AddDbContextPool```，而非 ```AddDbContext```：</span><span class="sxs-lookup"><span data-stu-id="c55c0-152">To use DbContext pooling, use the ```AddDbContextPool``` instead of ```AddDbContext``` during service registration:</span></span>

``` csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

<span data-ttu-id="c55c0-153">如果使用這種方法，則在控制器要求 DbContext 執行個體時，我們會先檢查集區中是否有執行個體可用。</span><span class="sxs-lookup"><span data-stu-id="c55c0-153">If this method is used, at the time a DbContext instance is requested by a controller we will first check if there is an instance available in the pool.</span></span> <span data-ttu-id="c55c0-154">要求處理完成之後，會重設執行個體上的任何狀態，並將執行個體本身傳回到集區。</span><span class="sxs-lookup"><span data-stu-id="c55c0-154">Once the request processing finalizes, any state on the instance is reset and the instance is itself returned to the pool.</span></span>

<span data-ttu-id="c55c0-155">這在概念上類似連線共用如何在 ADO.NET 提供者中操作，而且優點是節省 DbContext 執行個體的一些初始化成本。</span><span class="sxs-lookup"><span data-stu-id="c55c0-155">This is conceptually similar to how connection pooling operates in ADO.NET providers and has the advantage of saving some of the cost of initialization of DbContext instance.</span></span>

### <a name="limitations"></a><span data-ttu-id="c55c0-156">限制</span><span class="sxs-lookup"><span data-stu-id="c55c0-156">Limitations</span></span>

<span data-ttu-id="c55c0-157">新的方法引進 DbContext 的 ```OnConfiguring()``` 方法中可進行作業的一些限制。</span><span class="sxs-lookup"><span data-stu-id="c55c0-157">The new method introduces a few limitations on what can be done in the ```OnConfiguring()``` method of the DbContext.</span></span>

> [!WARNING]  
> <span data-ttu-id="c55c0-158">如果您在不應該於要求間共用的衍生 DbContext 類別中維護自己的狀態 (例如私用欄位)，則請避免使用 DbContext 共用。</span><span class="sxs-lookup"><span data-stu-id="c55c0-158">Avoid using DbContext Pooling if you maintain your own state (e.g. private fields) in your derived DbContext class that should not be shared across requests.</span></span> <span data-ttu-id="c55c0-159">EF Core 只會重設在將 DbContext 執行個體新增至集區之前所知道的狀態。</span><span class="sxs-lookup"><span data-stu-id="c55c0-159">EF Core will only reset the state that is aware of before adding a DbContext instance to the pool.</span></span>

### <a name="explicitly-compiled-queries"></a><span data-ttu-id="c55c0-160">明確地編譯查詢</span><span class="sxs-lookup"><span data-stu-id="c55c0-160">Explicitly compiled queries</span></span>

<span data-ttu-id="c55c0-161">這是設計成提供高延展案例優點的第二個選擇加入的效能功能。</span><span class="sxs-lookup"><span data-stu-id="c55c0-161">This is the second opt-in performance features designed to offer benefits in high-scale scenarios.</span></span>

<span data-ttu-id="c55c0-162">舊版 EF 以及 LINQ to SQL 中已提供手動或明確已編譯的查詢 API，允許應用程式快取查詢的轉譯，這樣只要將它們計算一次，就能執行多次。</span><span class="sxs-lookup"><span data-stu-id="c55c0-162">Manual or explicitly compiled query APIs have been available in previous versions of EF and also in LINQ to SQL to allow applications to cache the translation of queries so that they can be computed only once and executed many times.</span></span>

<span data-ttu-id="c55c0-163">一般而言，雖然 EF Core 可以根據查詢運算式的雜湊表示法來自動編譯並快取查詢 ，但是可以使用這項機制透過不計算雜湊和快取查閱來提升少許效能，以允許應用程式透過委派叫用來使用已編譯的查詢。</span><span class="sxs-lookup"><span data-stu-id="c55c0-163">Although in general EF Core can automatically compile and cache queries based on a hashed representation of the query expressions, this mechanism can be used to obtain a small performance gain by bypassing the computation of the hash and the cache lookup, allowing the application to use an already compiled query through the invocation of a delegate.</span></span>

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

## <a name="change-tracking"></a><span data-ttu-id="c55c0-164">變更追蹤</span><span class="sxs-lookup"><span data-stu-id="c55c0-164">Change Tracking</span></span>

### <a name="attach-can-track-a-graph-of-new-and-existing-entities"></a><span data-ttu-id="c55c0-165">附加可以追蹤全新和現有實體的圖形</span><span class="sxs-lookup"><span data-stu-id="c55c0-165">Attach can track a graph of new and existing entities</span></span>

<span data-ttu-id="c55c0-166">EF Core 支援透過不同的機制來自動產生索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="c55c0-166">EF Core supports automatic generation of key values through a variety of mechanisms.</span></span> <span data-ttu-id="c55c0-167">使用此功能時，如果重要屬性是 CLR 預設值，則會產生值：通常是零或 Null。</span><span class="sxs-lookup"><span data-stu-id="c55c0-167">When using this feature, a value is generated if the key property is the CLR default--usually zero or null.</span></span> <span data-ttu-id="c55c0-168">這表示可以將實體的圖表傳遞至 `DbContext.Attach` 或 `DbSet.Attach`，而且 EF Core 會將已設定索引鍵的實體標示為 `Unchanged`，同時將未設定索引鍵的實體標示為 `Added`。</span><span class="sxs-lookup"><span data-stu-id="c55c0-168">This means that a graph of entities can be passed to `DbContext.Attach` or `DbSet.Attach` and EF Core will mark those entities that have a key already set as `Unchanged` while those entities that do not have a key set will be marked as `Added`.</span></span> <span data-ttu-id="c55c0-169">使用產生的索引鍵時，這可以輕鬆地附加混合全新和現有實體的圖形。</span><span class="sxs-lookup"><span data-stu-id="c55c0-169">This makes it easy to attach a graph of mixed new and existing entities when using generated keys.</span></span> <span data-ttu-id="c55c0-170">`DbContext.Update` 和 `DbSet.Update` 的運作方式相同，差別在於將設定索引鍵的實體標示為 `Modified`，而不是 `Unchanged`。</span><span class="sxs-lookup"><span data-stu-id="c55c0-170">`DbContext.Update` and `DbSet.Update` work in the same way, except that entities with a key set are marked as `Modified` instead of `Unchanged`.</span></span>

## <a name="query"></a><span data-ttu-id="c55c0-171">查詢</span><span class="sxs-lookup"><span data-stu-id="c55c0-171">Query</span></span>

### <a name="improved-linq-translation"></a><span data-ttu-id="c55c0-172">改善的 LINQ 轉譯</span><span class="sxs-lookup"><span data-stu-id="c55c0-172">Improved LINQ Translation</span></span>

<span data-ttu-id="c55c0-173">讓更多查詢順利執行，並在資料庫中評估更多邏輯 (而不是在記憶體中) 以及從資料庫擷取較少的資料。</span><span class="sxs-lookup"><span data-stu-id="c55c0-173">Enables more queries to successfully execute, with more logic being evaluated in the database (rather than in-memory) and less data unnecessarily being retrieved from the database.</span></span>

### <a name="groupjoin-improvements"></a><span data-ttu-id="c55c0-174">GroupJoin 改善</span><span class="sxs-lookup"><span data-stu-id="c55c0-174">GroupJoin improvements</span></span>

<span data-ttu-id="c55c0-175">這項工作可改善針對群組聯結所產生的 SQL。</span><span class="sxs-lookup"><span data-stu-id="c55c0-175">This work improves the SQL that is generated for group joins.</span></span> <span data-ttu-id="c55c0-176">群組聯結最常是選擇性導覽屬性的子查詢結果。</span><span class="sxs-lookup"><span data-stu-id="c55c0-176">Group joins are most often a result of sub-queries on optional navigation properties.</span></span>

### <a name="string-interpolation-in-fromsql-and-executesqlcommand"></a><span data-ttu-id="c55c0-177">FromSql 和 ExecuteSqlCommand 中的字串插值</span><span class="sxs-lookup"><span data-stu-id="c55c0-177">String interpolation in FromSql and ExecuteSqlCommand</span></span>

<span data-ttu-id="c55c0-178">C# 6 已引進「字串插值」，此功能允許 C# 運算式直接內嵌在字串常值中，並提供不錯的方式在執行階段建置字串。</span><span class="sxs-lookup"><span data-stu-id="c55c0-178">C# 6 introduced String Interpolation, a feature that allows C# expressions to be directly embedded in string literals, providing a nice way of building strings at runtime.</span></span> <span data-ttu-id="c55c0-179">在 EF Core 2.0 中，我們已在接受原始 SQL 字串的兩個主要 API 中新增內插字串的特殊支援：```FromSql``` and ```ExecuteSqlCommand```.</span><span class="sxs-lookup"><span data-stu-id="c55c0-179">In EF Core 2.0 we added special support for interpolated strings to our two primary APIs that accept raw SQL strings: ```FromSql``` and ```ExecuteSqlCommand```.</span></span> <span data-ttu-id="c55c0-180">這個新的支援允許以「安全」方式使用 C# 字串插值。</span><span class="sxs-lookup"><span data-stu-id="c55c0-180">This new support allows C# string interpolation to be used in a 'safe' manner.</span></span> <span data-ttu-id="c55c0-181">亦即，可防止在執行階段動態建構 SQL 時可能發生的常見 SQL 插入錯誤。</span><span class="sxs-lookup"><span data-stu-id="c55c0-181">I.e. in a way that protects against common SQL injection mistakes that can occur when dynamically constructing SQL at runtime.</span></span>

<span data-ttu-id="c55c0-182">請看以下範例：</span><span class="sxs-lookup"><span data-stu-id="c55c0-182">Here is an example:</span></span>

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

<span data-ttu-id="c55c0-183">在此範例中，有兩個以 SQL 格式字串內嵌的變數。</span><span class="sxs-lookup"><span data-stu-id="c55c0-183">In this example, there are two variables embedded in the SQL format string.</span></span> <span data-ttu-id="c55c0-184">EF Core 會產生下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="c55c0-184">EF Core will produce the following SQL:</span></span>

```sql
@p0='London' (Size = 4000)
@p1='Sales Representative' (Size = 4000)

SELECT *
FROM ""Customers""
WHERE ""City"" = @p0
    AND ""ContactTitle"" = @p1
```

### <a name="effunctionslike"></a><span data-ttu-id="c55c0-185">EF.Functions.Like()</span><span class="sxs-lookup"><span data-stu-id="c55c0-185">EF.Functions.Like()</span></span>

<span data-ttu-id="c55c0-186">我們已新增 EF.Functions 屬性，而 EF Core 或提供者用來定義可對應至資料庫函式或運算子的方法，以在 LINQ 查詢 中予以叫用。</span><span class="sxs-lookup"><span data-stu-id="c55c0-186">We have added the EF.Functions property which can be used by EF Core or providers to define methods that map to database functions or operators so that those can be invoked in LINQ queries.</span></span> <span data-ttu-id="c55c0-187">這種方法的第一個範例是 Like()：</span><span class="sxs-lookup"><span data-stu-id="c55c0-187">The first example of such a method is Like():</span></span>

``` csharp
var aCustomers =
    from c in context.Customers
    where EF.Functions.Like(c.Name, "a%");
    select c;
```

<span data-ttu-id="c55c0-188">請注意，Like() 隨附記憶體中實作，這在處理記憶體中資料庫或用戶端需要進行述詞評估時都很方便使用。</span><span class="sxs-lookup"><span data-stu-id="c55c0-188">Note that Like() comes with an in-memory implementation, which can be handy when working against an in-memory database or when evaluation of the predicate needs to occur on the client side.</span></span>

## <a name="database-management"></a><span data-ttu-id="c55c0-189">資料庫管理</span><span class="sxs-lookup"><span data-stu-id="c55c0-189">Database management</span></span>

### <a name="pluralization-hook-for-dbcontext-scaffolding"></a><span data-ttu-id="c55c0-190">DbContext Scaffolding 的複數攔截</span><span class="sxs-lookup"><span data-stu-id="c55c0-190">Pluralization hook for DbContext Scaffolding</span></span>

<span data-ttu-id="c55c0-191">EF Core 2.0 引進新的 *IPluralizer* 服務，以用來將實體類型名稱單數化，並將 DbSet 名稱複數化。</span><span class="sxs-lookup"><span data-stu-id="c55c0-191">EF Core 2.0 introduces a new *IPluralizer* service that is used to singularize entity type names and pluralize DbSet names.</span></span> <span data-ttu-id="c55c0-192">預設實作是不操作，因此這只是人員可以輕鬆插入其專屬 pluralizer 的攔截。</span><span class="sxs-lookup"><span data-stu-id="c55c0-192">The default implementation is a no-op, so this is just a hook where folks can easily plug in their own pluralizer.</span></span>

<span data-ttu-id="c55c0-193">以下是開發人員在其專屬 pluralizer 中攔截的方式：</span><span class="sxs-lookup"><span data-stu-id="c55c0-193">Here is what it looks like for a developer to hook in their own pluralizer:</span></span>

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

## <a name="others"></a><span data-ttu-id="c55c0-194">其他</span><span class="sxs-lookup"><span data-stu-id="c55c0-194">Others</span></span>

### <a name="move-adonet-sqlite-provider-to-sqlitepclraw"></a><span data-ttu-id="c55c0-195">將 ADO.NET SQLite 提供者移至 SQLitePCL.raw</span><span class="sxs-lookup"><span data-stu-id="c55c0-195">Move ADO.NET SQLite provider to SQLitePCL.raw</span></span>
<span data-ttu-id="c55c0-196">這可在 Microsoft.Data.Sqlite 中提供更穩固的方案，以將原生 SQLite 二進位檔散發到不同的平台。</span><span class="sxs-lookup"><span data-stu-id="c55c0-196">This gives us a more robust solution in Microsoft.Data.Sqlite for distributing native SQLite binaries on different platforms.</span></span>

### <a name="only-one-provider-per-model"></a><span data-ttu-id="c55c0-197">一個模型只會有一個提供者</span><span class="sxs-lookup"><span data-stu-id="c55c0-197">Only one provider per model</span></span>
<span data-ttu-id="c55c0-198">大幅增加提供者如何與模型互動，以及簡化慣例、註釋和 Fluent API 如何與不同的提供者搭配運作。</span><span class="sxs-lookup"><span data-stu-id="c55c0-198">Significantly augments how providers can interact with the model and simplifies how conventions, annotations and fluent APIs work with different providers.</span></span>

<span data-ttu-id="c55c0-199">EF Core 2.0 現在會為使用的每個不同提供者建置不同的 [IModel](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IModel.cs)。</span><span class="sxs-lookup"><span data-stu-id="c55c0-199">EF Core 2.0 will now build a different [IModel](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IModel.cs) for each different provider being used.</span></span> <span data-ttu-id="c55c0-200">應用程式通常可以看到這項作業。</span><span class="sxs-lookup"><span data-stu-id="c55c0-200">This is usually transparent to the application.</span></span> <span data-ttu-id="c55c0-201">這已加速簡化較低階中繼資料 API；因此，任何對*一般關聯式中繼資料概念*的存取一律是透過 `.Relational` 呼叫來進行，而非 `.SqlServer`、`.Sqlite` 等等。</span><span class="sxs-lookup"><span data-stu-id="c55c0-201">This has facilitated a simplification of lower-level metadata APIs such that any access to *common relational metadata concepts* is always made through a call to `.Relational` instead of `.SqlServer`, `.Sqlite`, etc.</span></span>

### <a name="consolidated-logging-and-diagnostics"></a><span data-ttu-id="c55c0-202">合併的記錄和診斷</span><span class="sxs-lookup"><span data-stu-id="c55c0-202">Consolidated Logging and Diagnostics</span></span>

<span data-ttu-id="c55c0-203">記錄 (根據 ILogger) 和診斷 (根據 DiagnosticSource) 機制現在共用更多程式碼。</span><span class="sxs-lookup"><span data-stu-id="c55c0-203">Logging (based on ILogger) and Diagnostics (based on DiagnosticSource) mechanisms now share more code.</span></span>

<span data-ttu-id="c55c0-204">在 2.0 中，已經變更傳送至 ILogger 之訊息的事件識別碼。</span><span class="sxs-lookup"><span data-stu-id="c55c0-204">The event IDs for messages sent to an ILogger have changed in 2.0.</span></span> <span data-ttu-id="c55c0-205">在 EF Core 程式碼中，事件識別碼現在是唯一的。</span><span class="sxs-lookup"><span data-stu-id="c55c0-205">The event IDs are now unique across EF Core code.</span></span> <span data-ttu-id="c55c0-206">例如，這些訊息現在也遵循 MVC 所使用結構化記錄的標準模式。</span><span class="sxs-lookup"><span data-stu-id="c55c0-206">These messages now also follow the standard pattern for structured logging used by, for example, MVC.</span></span>

<span data-ttu-id="c55c0-207">記錄器類別也已經變更。</span><span class="sxs-lookup"><span data-stu-id="c55c0-207">Logger categories have also changed.</span></span> <span data-ttu-id="c55c0-208">現在已有一組類別可透過 [DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/DbLoggerCategory.cs) 進行存取。</span><span class="sxs-lookup"><span data-stu-id="c55c0-208">There is now a well-known set of categories accessed through [DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/DbLoggerCategory.cs).</span></span>

<span data-ttu-id="c55c0-209">DiagnosticSource 事件現在會使用相同的事件識別碼名稱作為對應的 `ILogger` 訊息。</span><span class="sxs-lookup"><span data-stu-id="c55c0-209">DiagnosticSource events now use the same event ID names as the corresponding `ILogger` messages.</span></span>
