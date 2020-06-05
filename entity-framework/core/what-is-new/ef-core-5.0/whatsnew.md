---
title: 5.0 EF Core 的新功能
description: EF Core 5.0 中的新功能總覽
author: ajcvickers
ms.date: 06/02/2020
uid: core/what-is-new/ef-core-5.0/whatsnew.md
ms.openlocfilehash: 45d851a4b08a26dda0c24e20c79f42964fa4fae4
ms.sourcegitcommit: 1f0f93c66b2b50e03fcbed90260e94faa0279c46
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2020
ms.locfileid: "84418934"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="ff997-103">5.0 EF Core 的新功能</span><span class="sxs-lookup"><span data-stu-id="ff997-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="ff997-104">EF Core 5.0 目前正在開發中。</span><span class="sxs-lookup"><span data-stu-id="ff997-104">EF Core 5.0 is currently in development.</span></span>
<span data-ttu-id="ff997-105">此頁面將包含每個預覽中所引進之有趣變更的總覽。</span><span class="sxs-lookup"><span data-stu-id="ff997-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="ff997-106">此頁面不會複製[EF Core 5.0 的計畫](plan.md)。</span><span class="sxs-lookup"><span data-stu-id="ff997-106">This page does not duplicate the [plan for EF Core 5.0](plan.md).</span></span>
<span data-ttu-id="ff997-107">此計畫描述 EF Core 5.0 的整體主題，包括我們打算在交付最終版本之前包含的所有專案。</span><span class="sxs-lookup"><span data-stu-id="ff997-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="ff997-108">我們會將這裡的連結新增至正式檔，因為它已發佈。</span><span class="sxs-lookup"><span data-stu-id="ff997-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-5"></a><span data-ttu-id="ff997-109">Preview 5</span><span class="sxs-lookup"><span data-stu-id="ff997-109">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="ff997-110">資料庫定序</span><span class="sxs-lookup"><span data-stu-id="ff997-110">Database collations</span></span>

<span data-ttu-id="ff997-111">現在，您可以在 EF 模型中指定資料庫的預設定序。</span><span class="sxs-lookup"><span data-stu-id="ff997-111">The default collation for a database can now be specified in the EF model.</span></span>
<span data-ttu-id="ff997-112">這會傳送至產生的遷移，以便在建立資料庫時設定定序。</span><span class="sxs-lookup"><span data-stu-id="ff997-112">This will flow through to generated migrations to set the collation when the database is created.</span></span>
<span data-ttu-id="ff997-113">例如：</span><span class="sxs-lookup"><span data-stu-id="ff997-113">For example:</span></span>

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="ff997-114">接著，遷移會產生下列內容，以在 SQL Server 上建立資料庫：</span><span class="sxs-lookup"><span data-stu-id="ff997-114">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="ff997-115">您也可以指定用於特定資料庫資料行的定序。</span><span class="sxs-lookup"><span data-stu-id="ff997-115">The collation to use for specific database columns can also be specified.</span></span>
<span data-ttu-id="ff997-116">例如：</span><span class="sxs-lookup"><span data-stu-id="ff997-116">For example:</span></span>

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="ff997-117">對於未使用遷移的人員，定序現在會在 DbCoNtext 架構時，從資料庫進行反向工程。</span><span class="sxs-lookup"><span data-stu-id="ff997-117">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="ff997-118">最後， `EF.Functions.Collate()` 允許使用不同定序的特定查詢。</span><span class="sxs-lookup"><span data-stu-id="ff997-118">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span>
<span data-ttu-id="ff997-119">例如：</span><span class="sxs-lookup"><span data-stu-id="ff997-119">For example:</span></span>

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="ff997-120">這會產生 SQL Server 的下列查詢：</span><span class="sxs-lookup"><span data-stu-id="ff997-120">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="ff997-121">請注意，特定定序應謹慎使用，因為它們可能會對資料庫效能造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="ff997-121">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="ff997-122">檔是由問題[#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273)追蹤。</span><span class="sxs-lookup"><span data-stu-id="ff997-122">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="ff997-123">IDesignTimeDbCoNtextFactory 中的流程引數</span><span class="sxs-lookup"><span data-stu-id="ff997-123">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="ff997-124">引數現在會從命令列流動到 `CreateDbContext` [IDesignTimeDbCoNtextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1)的方法中。</span><span class="sxs-lookup"><span data-stu-id="ff997-124">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span></span> <span data-ttu-id="ff997-125">例如，若要指出這是開發組建， `dev` 可以在命令列上傳遞自訂引數（例如）：</span><span class="sxs-lookup"><span data-stu-id="ff997-125">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can passed on the command line:</span></span>

```
dotnet ef migrations add two --verbose --dev
``` 

<span data-ttu-id="ff997-126">這個引數接著會流入處理站，它可以用來控制內容的建立和初始化方式。</span><span class="sxs-lookup"><span data-stu-id="ff997-126">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span>
<span data-ttu-id="ff997-127">例如：</span><span class="sxs-lookup"><span data-stu-id="ff997-127">For example:</span></span>

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args) 
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="ff997-128">檔是由問題[#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419)追蹤。</span><span class="sxs-lookup"><span data-stu-id="ff997-128">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="ff997-129">沒有識別解析的追蹤查詢</span><span class="sxs-lookup"><span data-stu-id="ff997-129">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="ff997-130">現在沒有任何追蹤查詢可以設定為執行識別解析。</span><span class="sxs-lookup"><span data-stu-id="ff997-130">No-tracking queries can now be configured to perform identity resolution.</span></span>
<span data-ttu-id="ff997-131">例如，下列查詢會針對每個貼文建立新的 Blog 實例，即使每個 Blog 都有相同的主要金鑰。</span><span class="sxs-lookup"><span data-stu-id="ff997-131">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span> 

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="ff997-132">不過，通常會稍微變慢，而且一定會使用更多的記憶體，因此可以變更此查詢，以確保只會建立單一的 Blog 實例：</span><span class="sxs-lookup"><span data-stu-id="ff997-132">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="ff997-133">請注意，這只適用于沒有追蹤的查詢，因為所有追蹤查詢都已經呈現此行為。</span><span class="sxs-lookup"><span data-stu-id="ff997-133">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="ff997-134">此外，在遵循 API 審查之後， `PerformIdentityResolution` 語法將會變更。</span><span class="sxs-lookup"><span data-stu-id="ff997-134">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span>
<span data-ttu-id="ff997-135">請參閱[#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073)。</span><span class="sxs-lookup"><span data-stu-id="ff997-135">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="ff997-136">檔是由問題[#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895)追蹤。</span><span class="sxs-lookup"><span data-stu-id="ff997-136">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="ff997-137">儲存的（保存）計算資料行</span><span class="sxs-lookup"><span data-stu-id="ff997-137">Stored (persisted) computed columns</span></span>

<span data-ttu-id="ff997-138">大部分的資料庫都允許在計算後儲存計算的資料行值。</span><span class="sxs-lookup"><span data-stu-id="ff997-138">Most databases allow computed column values to be stored after computation.</span></span>
<span data-ttu-id="ff997-139">雖然這會佔用磁碟空間，但計算的資料行只會在 update 上計算一次，而不是每次抓取它的值。</span><span class="sxs-lookup"><span data-stu-id="ff997-139">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span>
<span data-ttu-id="ff997-140">這也可讓某些資料庫的資料行編制索引。</span><span class="sxs-lookup"><span data-stu-id="ff997-140">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="ff997-141">EF Core 5.0 允許將計算資料行設定為已儲存。</span><span class="sxs-lookup"><span data-stu-id="ff997-141">EF Core 5.0 allows computed columns to be configured as stored.</span></span>
<span data-ttu-id="ff997-142">例如：</span><span class="sxs-lookup"><span data-stu-id="ff997-142">For example:</span></span>
 
```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="ff997-143">SQLite 計算資料行</span><span class="sxs-lookup"><span data-stu-id="ff997-143">SQLite computed columns</span></span>

<span data-ttu-id="ff997-144">EF Core 現在支援 SQLite 資料庫中的計算資料行。</span><span class="sxs-lookup"><span data-stu-id="ff997-144">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="ff997-145">Preview 4</span><span class="sxs-lookup"><span data-stu-id="ff997-145">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="ff997-146">在模型中設定資料庫精確度/規模</span><span class="sxs-lookup"><span data-stu-id="ff997-146">Configure database precision/scale in model</span></span>

<span data-ttu-id="ff997-147">屬性的有效位數和小數位數現在可以使用模型產生器來指定。</span><span class="sxs-lookup"><span data-stu-id="ff997-147">Precision and scale for a property can now be specified using the model builder.</span></span>
<span data-ttu-id="ff997-148">例如：</span><span class="sxs-lookup"><span data-stu-id="ff997-148">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="ff997-149">精確度和小數位數仍然可以透過完整資料庫類型來設定，例如 "decimal （16，4）"。</span><span class="sxs-lookup"><span data-stu-id="ff997-149">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span> 

<span data-ttu-id="ff997-150">檔是由問題[#527](https://github.com/dotnet/EntityFramework.Docs/issues/527)追蹤。</span><span class="sxs-lookup"><span data-stu-id="ff997-150">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="ff997-151">指定 SQL Server 索引填滿因數</span><span class="sxs-lookup"><span data-stu-id="ff997-151">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="ff997-152">在 SQL Server 上建立索引時，現在可以指定填滿因數。</span><span class="sxs-lookup"><span data-stu-id="ff997-152">The fill factor can now be specified when creating an index on SQL Server.</span></span>
<span data-ttu-id="ff997-153">例如：</span><span class="sxs-lookup"><span data-stu-id="ff997-153">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="ff997-154">Preview 3</span><span class="sxs-lookup"><span data-stu-id="ff997-154">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="ff997-155">篩選的包含</span><span class="sxs-lookup"><span data-stu-id="ff997-155">Filtered Include</span></span>

<span data-ttu-id="ff997-156">Include 方法現在支援篩選包含的實體。</span><span class="sxs-lookup"><span data-stu-id="ff997-156">The Include method now supports filtering of the entities included.</span></span>
<span data-ttu-id="ff997-157">例如：</span><span class="sxs-lookup"><span data-stu-id="ff997-157">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="ff997-158">此查詢將會連同每個相關聯的貼文一起傳回 blog，但只有在貼文標題包含「乳酪」時。</span><span class="sxs-lookup"><span data-stu-id="ff997-158">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="ff997-159">Skip 和 Take 也可以用來減少包含的實體數目。</span><span class="sxs-lookup"><span data-stu-id="ff997-159">Skip and Take can also be used to reduce the number of included entities.</span></span>
<span data-ttu-id="ff997-160">例如：</span><span class="sxs-lookup"><span data-stu-id="ff997-160">For example:</span></span>
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="ff997-161">此查詢將會傳回包含每個 blog 中最多五篇文章的 blog。</span><span class="sxs-lookup"><span data-stu-id="ff997-161">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="ff997-162">如需完整詳細資料，請參閱[包含檔](xref:core/querying/related-data#filtered-include)。</span><span class="sxs-lookup"><span data-stu-id="ff997-162">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="ff997-163">導覽屬性的新 ModelBuilder API</span><span class="sxs-lookup"><span data-stu-id="ff997-163">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="ff997-164">導覽屬性主要是在[定義關聯](xref:core/modeling/relationships)性時設定。</span><span class="sxs-lookup"><span data-stu-id="ff997-164">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span>
<span data-ttu-id="ff997-165">不過，在 `Navigation` 導覽屬性需要其他設定的情況下，可以使用新的方法。</span><span class="sxs-lookup"><span data-stu-id="ff997-165">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span>
<span data-ttu-id="ff997-166">例如，當依照慣例找不到欄位時，設定導覽的支援欄位：</span><span class="sxs-lookup"><span data-stu-id="ff997-166">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="ff997-167">請注意， `Navigation` API 不會取代關聯性設定。</span><span class="sxs-lookup"><span data-stu-id="ff997-167">Note that the `Navigation` API does not replace relationship configuration.</span></span>
<span data-ttu-id="ff997-168">相反地，它允許在已經探索或定義的關聯性中，額外設定導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="ff997-168">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="ff997-169">請參閱設定[導覽屬性檔](xref:core/modeling/relationships#configuring-navigation-properties)。</span><span class="sxs-lookup"><span data-stu-id="ff997-169">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="ff997-170">命名空間和連接字串的新命令列參數</span><span class="sxs-lookup"><span data-stu-id="ff997-170">New command-line parameters for namespaces and connection strings</span></span> 

<span data-ttu-id="ff997-171">現在，遷移和架構可讓您在命令列上指定命名空間。</span><span class="sxs-lookup"><span data-stu-id="ff997-171">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span>
<span data-ttu-id="ff997-172">例如，若要對資料庫進行反向工程，將內容和模型類別放在不同的命名空間中：</span><span class="sxs-lookup"><span data-stu-id="ff997-172">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span> 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="ff997-173">如需完整詳細資料，請參閱[遷移](xref:core/managing-schemas/migrations/index#namespaces)和[還原工程](xref:core/managing-schemas/scaffolding#directories-and-namespaces)檔。</span><span class="sxs-lookup"><span data-stu-id="ff997-173">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="ff997-174">此外，連接字串現在可以傳遞至 `database-update` 命令：</span><span class="sxs-lookup"><span data-stu-id="ff997-174">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="ff997-175">如需完整詳細資料，請參閱[工具檔](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update)。</span><span class="sxs-lookup"><span data-stu-id="ff997-175">See the [Tools documentation](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="ff997-176">對等的參數也已新增至 VS 套件管理員主控台中使用的 PowerShell 命令。</span><span class="sxs-lookup"><span data-stu-id="ff997-176">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="ff997-177">EnableDetailedErrors 已傳回</span><span class="sxs-lookup"><span data-stu-id="ff997-177">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="ff997-178">基於效能考慮，從資料庫讀取值時，EF 不會執行額外的 null 檢查。</span><span class="sxs-lookup"><span data-stu-id="ff997-178">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span>
<span data-ttu-id="ff997-179">當遇到未預期的 null 時，這可能會導致難以造成根本原因的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ff997-179">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="ff997-180">使用 `EnableDetailedErrors` 會在查詢中加入額外的 null 檢查，如此一來，在小型的效能額外負荷下，這些錯誤就會更容易追蹤到根本原因。</span><span class="sxs-lookup"><span data-stu-id="ff997-180">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>  

<span data-ttu-id="ff997-181">例如：</span><span class="sxs-lookup"><span data-stu-id="ff997-181">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="ff997-182">檔是由問題[#955](https://github.com/dotnet/EntityFramework.Docs/issues/955)追蹤。</span><span class="sxs-lookup"><span data-stu-id="ff997-182">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="ff997-183">Cosmos 分割區索引鍵</span><span class="sxs-lookup"><span data-stu-id="ff997-183">Cosmos partition keys</span></span>

<span data-ttu-id="ff997-184">您現在可以在查詢中指定要用於給定查詢的分割區索引鍵。</span><span class="sxs-lookup"><span data-stu-id="ff997-184">The partition key to use for a given query can now be specified in the query.</span></span>
<span data-ttu-id="ff997-185">例如：</span><span class="sxs-lookup"><span data-stu-id="ff997-185">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="ff997-186">檔是由問題[#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199)追蹤。</span><span class="sxs-lookup"><span data-stu-id="ff997-186">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="ff997-187">支援 SQL Server DATALENGTH 函數</span><span class="sxs-lookup"><span data-stu-id="ff997-187">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="ff997-188">這可使用新的方法來存取 `EF.Functions.DataLength` 。</span><span class="sxs-lookup"><span data-stu-id="ff997-188">This can be accessed using the new `EF.Functions.DataLength` method.</span></span>
<span data-ttu-id="ff997-189">例如：</span><span class="sxs-lookup"><span data-stu-id="ff997-189">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a><span data-ttu-id="ff997-190">Preview 2</span><span class="sxs-lookup"><span data-stu-id="ff997-190">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="ff997-191">使用 c # 屬性指定屬性支援欄位</span><span class="sxs-lookup"><span data-stu-id="ff997-191">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="ff997-192">C # 屬性現在可以用來指定屬性的支援欄位。</span><span class="sxs-lookup"><span data-stu-id="ff997-192">A C# attribute can now be used to specify the backing field for a property.</span></span>
<span data-ttu-id="ff997-193">即使無法自動找到支援欄位，此屬性仍然可以讓 EF Core 仍會如往常般寫入和讀取支援欄位。</span><span class="sxs-lookup"><span data-stu-id="ff997-193">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span>
<span data-ttu-id="ff997-194">例如：</span><span class="sxs-lookup"><span data-stu-id="ff997-194">For example:</span></span>

```CSharp
public class Blog
{
    private string _mainTitle;

    public int Id { get; set; }

    [BackingField(nameof(_mainTitle))]
    public string Title
    {
        get => _mainTitle;
        set => _mainTitle = value;
    }
}
```

<span data-ttu-id="ff997-195">檔是由問題[#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230)追蹤。</span><span class="sxs-lookup"><span data-stu-id="ff997-195">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="ff997-196">完成鑒別子對應</span><span class="sxs-lookup"><span data-stu-id="ff997-196">Complete discriminator mapping</span></span>

<span data-ttu-id="ff997-197">EF Core 針對[繼承階層的 TPH 對應](/ef/core/modeling/inheritance)使用鑒別子資料行。</span><span class="sxs-lookup"><span data-stu-id="ff997-197">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span>
<span data-ttu-id="ff997-198">只要 EF Core 知道鑒別子的所有可能值，就可能會有一些效能增強功能。</span><span class="sxs-lookup"><span data-stu-id="ff997-198">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span>
<span data-ttu-id="ff997-199">EF Core 5.0 現在會實行這些增強功能。</span><span class="sxs-lookup"><span data-stu-id="ff997-199">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="ff997-200">例如，舊版的 EF Core 一律會針對傳回階層中所有類型的查詢產生此 SQL：</span><span class="sxs-lookup"><span data-stu-id="ff997-200">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="ff997-201">當設定完整的鑒別子對應時，EF Core 5.0 現在會產生下列內容：</span><span class="sxs-lookup"><span data-stu-id="ff997-201">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="ff997-202">這會是從 preview 3 開始的預設行為。</span><span class="sxs-lookup"><span data-stu-id="ff997-202">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="ff997-203">Microsoft 中的效能改進。 Sqlite</span><span class="sxs-lookup"><span data-stu-id="ff997-203">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="ff997-204">我們對 SQLIte 進行了兩項效能改善：</span><span class="sxs-lookup"><span data-stu-id="ff997-204">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="ff997-205">藉由使用 SqliteBlob 和資料流程，以 GetBytes、GetChars 和 GetTextReader 來抓取二進位和字串資料現在更有效率。</span><span class="sxs-lookup"><span data-stu-id="ff997-205">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="ff997-206">SqliteConnection 的初始化現在是延遲的。</span><span class="sxs-lookup"><span data-stu-id="ff997-206">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="ff997-207">這些改良功能位於 ADO.NET 的 Microsoft. Sqlite 提供者，因此也會改善 EF Core 之外的效能。</span><span class="sxs-lookup"><span data-stu-id="ff997-207">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="ff997-208">Preview 1</span><span class="sxs-lookup"><span data-stu-id="ff997-208">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="ff997-209">簡單記錄</span><span class="sxs-lookup"><span data-stu-id="ff997-209">Simple logging</span></span>

<span data-ttu-id="ff997-210">這項功能會 `Database.Log` 在 EF6 中新增類似的功能。</span><span class="sxs-lookup"><span data-stu-id="ff997-210">This feature adds functionality similar to `Database.Log` in EF6.</span></span>
<span data-ttu-id="ff997-211">也就是說，它提供簡單的方法，讓您不需要設定任何類型的外部記錄架構，即可從 EF Core 取得記錄。</span><span class="sxs-lookup"><span data-stu-id="ff997-211">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="ff997-212">[2019 年12月5日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)會包含初稿檔。</span><span class="sxs-lookup"><span data-stu-id="ff997-212">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="ff997-213">其他檔則是由問題[#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085)追蹤。</span><span class="sxs-lookup"><span data-stu-id="ff997-213">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="ff997-214">取得產生之 SQL 的簡單方式</span><span class="sxs-lookup"><span data-stu-id="ff997-214">Simple way to get generated SQL</span></span>

<span data-ttu-id="ff997-215">EF Core 5.0 引進了 `ToQueryString` 擴充方法，這會傳回在執行 LINQ 查詢時，EF Core 將產生的 SQL。</span><span class="sxs-lookup"><span data-stu-id="ff997-215">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="ff997-216">初稿檔包含在[2020 年1月9日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)。</span><span class="sxs-lookup"><span data-stu-id="ff997-216">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="ff997-217">其他檔則是由問題[#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331)追蹤。</span><span class="sxs-lookup"><span data-stu-id="ff997-217">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="ff997-218">使用 c # 屬性來表示實體沒有索引鍵</span><span class="sxs-lookup"><span data-stu-id="ff997-218">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="ff997-219">現在可以使用新的，將實體類型設定為沒有任何索引鍵 `KeylessAttribute` 。</span><span class="sxs-lookup"><span data-stu-id="ff997-219">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span>
<span data-ttu-id="ff997-220">例如：</span><span class="sxs-lookup"><span data-stu-id="ff997-220">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="ff997-221">檔是由問題[#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186)追蹤。</span><span class="sxs-lookup"><span data-stu-id="ff997-221">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="ff997-222">連接或連接字串可以在初始化的 DbCoNtext 上變更</span><span class="sxs-lookup"><span data-stu-id="ff997-222">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="ff997-223">現在您可以更輕鬆地建立 DbCoNtext 實例，而不需要任何連接或連接字串。</span><span class="sxs-lookup"><span data-stu-id="ff997-223">It is now easier to create a DbContext instance without any connection or connection string.</span></span>
<span data-ttu-id="ff997-224">此外，連接或連接字串現在可以在內容實例上進行變化。</span><span class="sxs-lookup"><span data-stu-id="ff997-224">Also, the connection or connection string can now be mutated on the context instance.</span></span>
<span data-ttu-id="ff997-225">這項功能可讓相同的內容實例動態連接到不同的資料庫。</span><span class="sxs-lookup"><span data-stu-id="ff997-225">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="ff997-226">檔是由問題[#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075)追蹤。</span><span class="sxs-lookup"><span data-stu-id="ff997-226">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="ff997-227">變更追蹤 proxy</span><span class="sxs-lookup"><span data-stu-id="ff997-227">Change-tracking proxies</span></span>

<span data-ttu-id="ff997-228">EF Core 現在可以產生自動執行[INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1)和[INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1)的執行時間 proxy。</span><span class="sxs-lookup"><span data-stu-id="ff997-228">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span>
<span data-ttu-id="ff997-229">這些之後，實體屬性上的這些值會直接變更為 EF Core，避免需要掃描變更。</span><span class="sxs-lookup"><span data-stu-id="ff997-229">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span>
<span data-ttu-id="ff997-230">不過，proxy 會有自己的一組限制，因此不適合所有人。</span><span class="sxs-lookup"><span data-stu-id="ff997-230">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="ff997-231">檔是由問題[#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076)追蹤。</span><span class="sxs-lookup"><span data-stu-id="ff997-231">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="ff997-232">增強的調試視圖</span><span class="sxs-lookup"><span data-stu-id="ff997-232">Enhanced debug views</span></span>

<span data-ttu-id="ff997-233">「偵錯工具」是一種簡單的方式，可在偵錯工具發生問題時查看 EF Core 的內部。</span><span class="sxs-lookup"><span data-stu-id="ff997-233">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span>
<span data-ttu-id="ff997-234">模型的「偵錯工具」視圖已在一段時間前完成。</span><span class="sxs-lookup"><span data-stu-id="ff997-234">A debug view for the Model was implemented some time ago.</span></span>
<span data-ttu-id="ff997-235">針對 EF Core 5.0，我們讓模型視圖更容易閱讀，並在狀態管理員中為追蹤的實體新增新的 [偵錯工具]。</span><span class="sxs-lookup"><span data-stu-id="ff997-235">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="ff997-236">[2019 年12月12日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)會包含初稿檔。</span><span class="sxs-lookup"><span data-stu-id="ff997-236">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="ff997-237">其他檔則是由問題[#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086)追蹤。</span><span class="sxs-lookup"><span data-stu-id="ff997-237">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="ff997-238">改善資料庫 null 語義的處理</span><span class="sxs-lookup"><span data-stu-id="ff997-238">Improved handling of database null semantics</span></span>

<span data-ttu-id="ff997-239">關係資料庫通常會將 Null 視為未知的值，因此不等於任何其他 Null。</span><span class="sxs-lookup"><span data-stu-id="ff997-239">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span>
<span data-ttu-id="ff997-240">雖然 c # 會將 null 視為已定義的值，但其比較會等於任何其他 null。</span><span class="sxs-lookup"><span data-stu-id="ff997-240">While C# treats null as a defined value, which compares equal to any other null.</span></span>
<span data-ttu-id="ff997-241">EF Core 預設會轉譯查詢，使其使用 c # null 語義。</span><span class="sxs-lookup"><span data-stu-id="ff997-241">EF Core by default translates queries so that they use C# null semantics.</span></span>
<span data-ttu-id="ff997-242">EF Core 5.0 大幅提升了這些翻譯的效率。</span><span class="sxs-lookup"><span data-stu-id="ff997-242">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="ff997-243">檔是由問題[#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612)追蹤。</span><span class="sxs-lookup"><span data-stu-id="ff997-243">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="ff997-244">索引子屬性</span><span class="sxs-lookup"><span data-stu-id="ff997-244">Indexer properties</span></span>

<span data-ttu-id="ff997-245">EF Core 5.0 支援 c # 索引子屬性的對應。</span><span class="sxs-lookup"><span data-stu-id="ff997-245">EF Core 5.0 supports mapping of C# indexer properties.</span></span>
<span data-ttu-id="ff997-246">這些屬性可讓實體作為屬性包，其中的資料行會對應到包中的命名屬性。</span><span class="sxs-lookup"><span data-stu-id="ff997-246">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="ff997-247">檔是由問題[#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018)追蹤。</span><span class="sxs-lookup"><span data-stu-id="ff997-247">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="ff997-248">列舉對應的檢查條件約束產生</span><span class="sxs-lookup"><span data-stu-id="ff997-248">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="ff997-249">EF Core 5.0 遷移現在可以產生 enum 屬性對應的檢查條件約束。</span><span class="sxs-lookup"><span data-stu-id="ff997-249">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span>
<span data-ttu-id="ff997-250">例如：</span><span class="sxs-lookup"><span data-stu-id="ff997-250">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="ff997-251">檔是由問題[#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082)追蹤。</span><span class="sxs-lookup"><span data-stu-id="ff997-251">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="ff997-252">IsRelational</span><span class="sxs-lookup"><span data-stu-id="ff997-252">IsRelational</span></span>

<span data-ttu-id="ff997-253">`IsRelational`除了現有的、和之外，還加入了新的方法 `IsSqlServer` `IsSqlite` `IsInMemory` 。</span><span class="sxs-lookup"><span data-stu-id="ff997-253">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span>
<span data-ttu-id="ff997-254">這個方法可以用來測試 DbCoNtext 是否使用任何關係資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="ff997-254">This method can be used to test if the DbContext is using any relational database provider.</span></span>
<span data-ttu-id="ff997-255">例如：</span><span class="sxs-lookup"><span data-stu-id="ff997-255">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="ff997-256">檔是由問題[#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185)追蹤。</span><span class="sxs-lookup"><span data-stu-id="ff997-256">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="ff997-257">使用 Etag Cosmos 開放式平行存取</span><span class="sxs-lookup"><span data-stu-id="ff997-257">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="ff997-258">Azure Cosmos DB 資料庫提供者現在支援使用 Etag 的開放式平行存取。</span><span class="sxs-lookup"><span data-stu-id="ff997-258">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span>
<span data-ttu-id="ff997-259">使用 OnModelCreating 中的模型產生器來設定 ETag：</span><span class="sxs-lookup"><span data-stu-id="ff997-259">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="ff997-260">接著，SaveChanges 會擲回 `DbUpdateConcurrencyException` 並行衝突，以[處理](https://docs.microsoft.com/ef/core/saving/concurrency)以執行重試等。</span><span class="sxs-lookup"><span data-stu-id="ff997-260">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](https://docs.microsoft.com/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="ff997-261">檔是由問題[#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099)追蹤。</span><span class="sxs-lookup"><span data-stu-id="ff997-261">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="ff997-262">更多日期時間結構的查詢翻譯</span><span class="sxs-lookup"><span data-stu-id="ff997-262">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="ff997-263">包含新日期時間結構的查詢現在已轉譯。</span><span class="sxs-lookup"><span data-stu-id="ff997-263">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="ff997-264">此外，現在已對應下列 SQL Server 函式：</span><span class="sxs-lookup"><span data-stu-id="ff997-264">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="ff997-265">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="ff997-265">DateDiffWeek</span></span>
* <span data-ttu-id="ff997-266">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="ff997-266">DateFromParts</span></span>

<span data-ttu-id="ff997-267">例如：</span><span class="sxs-lookup"><span data-stu-id="ff997-267">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="ff997-268">檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="ff997-268">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="ff997-269">更多位元組陣列結構的查詢翻譯</span><span class="sxs-lookup"><span data-stu-id="ff997-269">Query translations for more byte array constructs</span></span>

<span data-ttu-id="ff997-270">在 byte [] 屬性上使用 Contains、Length、Enumerable.sequenceequal 等的查詢現在會轉譯為 SQL。</span><span class="sxs-lookup"><span data-stu-id="ff997-270">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="ff997-271">[2019 年12月5日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)會包含初稿檔。</span><span class="sxs-lookup"><span data-stu-id="ff997-271">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="ff997-272">其他檔則是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="ff997-272">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="ff997-273">反向的查詢轉譯</span><span class="sxs-lookup"><span data-stu-id="ff997-273">Query translation for Reverse</span></span>

<span data-ttu-id="ff997-274">使用 `Reverse` 的查詢現在已轉譯。</span><span class="sxs-lookup"><span data-stu-id="ff997-274">Queries using `Reverse` are now translated.</span></span>
<span data-ttu-id="ff997-275">例如：</span><span class="sxs-lookup"><span data-stu-id="ff997-275">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="ff997-276">檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="ff997-276">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="ff997-277">位運算子的查詢轉譯</span><span class="sxs-lookup"><span data-stu-id="ff997-277">Query translation for bitwise operators</span></span>

<span data-ttu-id="ff997-278">使用位運算子的查詢現在會在更多情況下轉譯，例如：</span><span class="sxs-lookup"><span data-stu-id="ff997-278">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="ff997-279">檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="ff997-279">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="ff997-280">Cosmos 上字串的查詢轉譯</span><span class="sxs-lookup"><span data-stu-id="ff997-280">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="ff997-281">使用 Azure Cosmos DB 提供者時，現在會轉譯使用字串方法 Contains、StartsWith 和 EndsWith 的查詢。</span><span class="sxs-lookup"><span data-stu-id="ff997-281">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="ff997-282">檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="ff997-282">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
