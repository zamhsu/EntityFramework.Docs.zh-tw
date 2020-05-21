---
title: 5.0 EF Core 的新功能
description: EF Core 5.0 中的新功能總覽
author: ajcvickers
ms.date: 05/11/2020
uid: core/what-is-new/ef-core-5.0/whatsnew.md
ms.openlocfilehash: fcb2eb8df99a06eaf3459835347a4027a363b86b
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672859"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="99b85-103">5.0 EF Core 的新功能</span><span class="sxs-lookup"><span data-stu-id="99b85-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="99b85-104">EF Core 5.0 目前正在開發中。</span><span class="sxs-lookup"><span data-stu-id="99b85-104">EF Core 5.0 is currently in development.</span></span>
<span data-ttu-id="99b85-105">此頁面將包含每個預覽中所引進之有趣變更的總覽。</span><span class="sxs-lookup"><span data-stu-id="99b85-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="99b85-106">此頁面不會複製[EF Core 5.0 的計畫](plan.md)。</span><span class="sxs-lookup"><span data-stu-id="99b85-106">This page does not duplicate the [plan for EF Core 5.0](plan.md).</span></span>
<span data-ttu-id="99b85-107">此計畫描述 EF Core 5.0 的整體主題，包括我們打算在交付最終版本之前包含的所有專案。</span><span class="sxs-lookup"><span data-stu-id="99b85-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="99b85-108">我們會將這裡的連結新增至正式檔，因為它已發佈。</span><span class="sxs-lookup"><span data-stu-id="99b85-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-4"></a><span data-ttu-id="99b85-109">Preview 4</span><span class="sxs-lookup"><span data-stu-id="99b85-109">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="99b85-110">在模型中設定資料庫精確度/規模</span><span class="sxs-lookup"><span data-stu-id="99b85-110">Configure database precision/scale in model</span></span>

<span data-ttu-id="99b85-111">屬性的有效位數和小數位數現在可以使用模型產生器來指定。</span><span class="sxs-lookup"><span data-stu-id="99b85-111">Precision and scale for a property can now be specified using the model builder.</span></span>
<span data-ttu-id="99b85-112">例如：</span><span class="sxs-lookup"><span data-stu-id="99b85-112">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="99b85-113">精確度和小數位數仍然可以透過完整資料庫類型來設定，例如 "decimal （16，4）"。</span><span class="sxs-lookup"><span data-stu-id="99b85-113">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span> 

<span data-ttu-id="99b85-114">檔是由問題[#527](https://github.com/dotnet/EntityFramework.Docs/issues/527)追蹤。</span><span class="sxs-lookup"><span data-stu-id="99b85-114">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="99b85-115">指定 SQL Server 索引填滿因數</span><span class="sxs-lookup"><span data-stu-id="99b85-115">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="99b85-116">在 SQL Server 上建立索引時，現在可以指定填滿因數。</span><span class="sxs-lookup"><span data-stu-id="99b85-116">The fill factor can now be specified when creating an index on SQL Server.</span></span>
<span data-ttu-id="99b85-117">例如：</span><span class="sxs-lookup"><span data-stu-id="99b85-117">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

<span data-ttu-id="99b85-118">檔是由問題[#2378](https://github.com/dotnet/EntityFramework.Docs/issues/2378)追蹤。</span><span class="sxs-lookup"><span data-stu-id="99b85-118">Documentation is tracked by issue [#2378](https://github.com/dotnet/EntityFramework.Docs/issues/2378).</span></span>

## <a name="preview-3"></a><span data-ttu-id="99b85-119">Preview 3</span><span class="sxs-lookup"><span data-stu-id="99b85-119">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="99b85-120">篩選的包含</span><span class="sxs-lookup"><span data-stu-id="99b85-120">Filtered Include</span></span>

<span data-ttu-id="99b85-121">Include 方法現在支援篩選包含的實體。</span><span class="sxs-lookup"><span data-stu-id="99b85-121">The Include method now supports filtering of the entities included.</span></span>
<span data-ttu-id="99b85-122">例如：</span><span class="sxs-lookup"><span data-stu-id="99b85-122">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="99b85-123">此查詢將會連同每個相關聯的貼文一起傳回 blog，但只有在貼文標題包含「乳酪」時。</span><span class="sxs-lookup"><span data-stu-id="99b85-123">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="99b85-124">Skip 和 Take 也可以用來減少包含的實體數目。</span><span class="sxs-lookup"><span data-stu-id="99b85-124">Skip and Take can also be used to reduce the number of included entities.</span></span>
<span data-ttu-id="99b85-125">例如：</span><span class="sxs-lookup"><span data-stu-id="99b85-125">For example:</span></span>
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="99b85-126">此查詢將會傳回包含每個 blog 中最多五篇文章的 blog。</span><span class="sxs-lookup"><span data-stu-id="99b85-126">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="99b85-127">如需完整詳細資料，請參閱[包含檔](xref:core/querying/related-data#filtered-include)。</span><span class="sxs-lookup"><span data-stu-id="99b85-127">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="99b85-128">導覽屬性的新 ModelBuilder API</span><span class="sxs-lookup"><span data-stu-id="99b85-128">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="99b85-129">導覽屬性主要是在[定義關聯](xref:core/modeling/relationships)性時設定。</span><span class="sxs-lookup"><span data-stu-id="99b85-129">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span>
<span data-ttu-id="99b85-130">不過，在 `Navigation` 導覽屬性需要其他設定的情況下，可以使用新的方法。</span><span class="sxs-lookup"><span data-stu-id="99b85-130">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span>
<span data-ttu-id="99b85-131">例如，當依照慣例找不到欄位時，設定導覽的支援欄位：</span><span class="sxs-lookup"><span data-stu-id="99b85-131">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="99b85-132">請注意， `Navigation` API 不會取代關聯性設定。</span><span class="sxs-lookup"><span data-stu-id="99b85-132">Note that the `Navigation` API does not replace relationship configuration.</span></span>
<span data-ttu-id="99b85-133">相反地，它允許在已經探索或定義的關聯性中，額外設定導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="99b85-133">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="99b85-134">請參閱設定[導覽屬性檔](xref:core/modeling/relationships#configuring-navigation-properties)。</span><span class="sxs-lookup"><span data-stu-id="99b85-134">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="99b85-135">命名空間和連接字串的新命令列參數</span><span class="sxs-lookup"><span data-stu-id="99b85-135">New command-line parameters for namespaces and connection strings</span></span> 

<span data-ttu-id="99b85-136">現在，遷移和架構可讓您在命令列上指定命名空間。</span><span class="sxs-lookup"><span data-stu-id="99b85-136">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span>
<span data-ttu-id="99b85-137">例如，若要對資料庫進行反向工程，將內容和模型類別放在不同的命名空間中：</span><span class="sxs-lookup"><span data-stu-id="99b85-137">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span> 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="99b85-138">如需完整詳細資料，請參閱[遷移](xref:core/managing-schemas/migrations/index#namespaces)和[還原工程](xref:core/managing-schemas/scaffolding#directories-and-namespaces)檔。</span><span class="sxs-lookup"><span data-stu-id="99b85-138">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="99b85-139">此外，連接字串現在可以傳遞至 `database-update` 命令：</span><span class="sxs-lookup"><span data-stu-id="99b85-139">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="99b85-140">如需完整詳細資料，請參閱[工具檔](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update)。</span><span class="sxs-lookup"><span data-stu-id="99b85-140">See the [Tools documentation](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="99b85-141">對等的參數也已新增至 VS 套件管理員主控台中使用的 PowerShell 命令。</span><span class="sxs-lookup"><span data-stu-id="99b85-141">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="99b85-142">EnableDetailedErrors 已傳回</span><span class="sxs-lookup"><span data-stu-id="99b85-142">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="99b85-143">基於效能考慮，從資料庫讀取值時，EF 不會執行額外的 null 檢查。</span><span class="sxs-lookup"><span data-stu-id="99b85-143">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span>
<span data-ttu-id="99b85-144">當遇到未預期的 null 時，這可能會導致難以造成根本原因的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="99b85-144">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="99b85-145">使用 `EnableDetailedErrors` 會在查詢中加入額外的 null 檢查，如此一來，在小型的效能額外負荷下，這些錯誤就會更容易追蹤到根本原因。</span><span class="sxs-lookup"><span data-stu-id="99b85-145">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>  

<span data-ttu-id="99b85-146">例如：</span><span class="sxs-lookup"><span data-stu-id="99b85-146">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="99b85-147">檔是由問題[#955](https://github.com/dotnet/EntityFramework.Docs/issues/955)追蹤。</span><span class="sxs-lookup"><span data-stu-id="99b85-147">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="99b85-148">Cosmos 分割區索引鍵</span><span class="sxs-lookup"><span data-stu-id="99b85-148">Cosmos partition keys</span></span>

<span data-ttu-id="99b85-149">您現在可以在查詢中指定要用於給定查詢的分割區索引鍵。</span><span class="sxs-lookup"><span data-stu-id="99b85-149">The partition key to use for a given query can now be specified in the query.</span></span>
<span data-ttu-id="99b85-150">例如：</span><span class="sxs-lookup"><span data-stu-id="99b85-150">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="99b85-151">檔是由問題[#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199)追蹤。</span><span class="sxs-lookup"><span data-stu-id="99b85-151">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="99b85-152">支援 SQL Server DATALENGTH 函數</span><span class="sxs-lookup"><span data-stu-id="99b85-152">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="99b85-153">這可使用新的方法來存取 `EF.Functions.DataLength` 。</span><span class="sxs-lookup"><span data-stu-id="99b85-153">This can be accessed using the new `EF.Functions.DataLength` method.</span></span>
<span data-ttu-id="99b85-154">例如：</span><span class="sxs-lookup"><span data-stu-id="99b85-154">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a><span data-ttu-id="99b85-155">Preview 2</span><span class="sxs-lookup"><span data-stu-id="99b85-155">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="99b85-156">使用 c # 屬性指定屬性支援欄位</span><span class="sxs-lookup"><span data-stu-id="99b85-156">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="99b85-157">C # 屬性現在可以用來指定屬性的支援欄位。</span><span class="sxs-lookup"><span data-stu-id="99b85-157">A C# attribute can now be used to specify the backing field for a property.</span></span>
<span data-ttu-id="99b85-158">即使無法自動找到支援欄位，此屬性仍然可以讓 EF Core 仍會如往常般寫入和讀取支援欄位。</span><span class="sxs-lookup"><span data-stu-id="99b85-158">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span>
<span data-ttu-id="99b85-159">例如：</span><span class="sxs-lookup"><span data-stu-id="99b85-159">For example:</span></span>

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

<span data-ttu-id="99b85-160">檔是由問題[#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230)追蹤。</span><span class="sxs-lookup"><span data-stu-id="99b85-160">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="99b85-161">完成鑒別子對應</span><span class="sxs-lookup"><span data-stu-id="99b85-161">Complete discriminator mapping</span></span>

<span data-ttu-id="99b85-162">EF Core 針對[繼承階層的 TPH 對應](/ef/core/modeling/inheritance)使用鑒別子資料行。</span><span class="sxs-lookup"><span data-stu-id="99b85-162">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span>
<span data-ttu-id="99b85-163">只要 EF Core 知道鑒別子的所有可能值，就可能會有一些效能增強功能。</span><span class="sxs-lookup"><span data-stu-id="99b85-163">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span>
<span data-ttu-id="99b85-164">EF Core 5.0 現在會實行這些增強功能。</span><span class="sxs-lookup"><span data-stu-id="99b85-164">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="99b85-165">例如，舊版的 EF Core 一律會針對傳回階層中所有類型的查詢產生此 SQL：</span><span class="sxs-lookup"><span data-stu-id="99b85-165">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="99b85-166">當設定完整的鑒別子對應時，EF Core 5.0 現在會產生下列內容：</span><span class="sxs-lookup"><span data-stu-id="99b85-166">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="99b85-167">這會是從 preview 3 開始的預設行為。</span><span class="sxs-lookup"><span data-stu-id="99b85-167">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="99b85-168">Microsoft 中的效能改進。 Sqlite</span><span class="sxs-lookup"><span data-stu-id="99b85-168">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="99b85-169">我們對 SQLIte 進行了兩項效能改善：</span><span class="sxs-lookup"><span data-stu-id="99b85-169">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="99b85-170">藉由使用 SqliteBlob 和資料流程，以 GetBytes、GetChars 和 GetTextReader 來抓取二進位和字串資料現在更有效率。</span><span class="sxs-lookup"><span data-stu-id="99b85-170">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="99b85-171">SqliteConnection 的初始化現在是延遲的。</span><span class="sxs-lookup"><span data-stu-id="99b85-171">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="99b85-172">這些改良功能位於 ADO.NET 的 Microsoft. Sqlite 提供者，因此也會改善 EF Core 之外的效能。</span><span class="sxs-lookup"><span data-stu-id="99b85-172">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="99b85-173">Preview 1</span><span class="sxs-lookup"><span data-stu-id="99b85-173">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="99b85-174">簡單記錄</span><span class="sxs-lookup"><span data-stu-id="99b85-174">Simple logging</span></span>

<span data-ttu-id="99b85-175">這項功能會 `Database.Log` 在 EF6 中新增類似的功能。</span><span class="sxs-lookup"><span data-stu-id="99b85-175">This feature adds functionality similar to `Database.Log` in EF6.</span></span>
<span data-ttu-id="99b85-176">也就是說，它提供簡單的方法，讓您不需要設定任何類型的外部記錄架構，即可從 EF Core 取得記錄。</span><span class="sxs-lookup"><span data-stu-id="99b85-176">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="99b85-177">[2019 年12月5日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)會包含初稿檔。</span><span class="sxs-lookup"><span data-stu-id="99b85-177">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="99b85-178">其他檔則是由問題[#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085)追蹤。</span><span class="sxs-lookup"><span data-stu-id="99b85-178">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="99b85-179">取得產生之 SQL 的簡單方式</span><span class="sxs-lookup"><span data-stu-id="99b85-179">Simple way to get generated SQL</span></span>

<span data-ttu-id="99b85-180">EF Core 5.0 引進了 `ToQueryString` 擴充方法，這會傳回在執行 LINQ 查詢時，EF Core 將產生的 SQL。</span><span class="sxs-lookup"><span data-stu-id="99b85-180">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="99b85-181">初稿檔包含在[2020 年1月9日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)。</span><span class="sxs-lookup"><span data-stu-id="99b85-181">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="99b85-182">其他檔則是由問題[#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331)追蹤。</span><span class="sxs-lookup"><span data-stu-id="99b85-182">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="99b85-183">使用 c # 屬性來表示實體沒有索引鍵</span><span class="sxs-lookup"><span data-stu-id="99b85-183">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="99b85-184">現在可以使用新的，將實體類型設定為沒有任何索引鍵 `KeylessAttribute` 。</span><span class="sxs-lookup"><span data-stu-id="99b85-184">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span>
<span data-ttu-id="99b85-185">例如：</span><span class="sxs-lookup"><span data-stu-id="99b85-185">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="99b85-186">檔是由問題[#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186)追蹤。</span><span class="sxs-lookup"><span data-stu-id="99b85-186">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="99b85-187">連接或連接字串可以在初始化的 DbCoNtext 上變更</span><span class="sxs-lookup"><span data-stu-id="99b85-187">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="99b85-188">現在您可以更輕鬆地建立 DbCoNtext 實例，而不需要任何連接或連接字串。</span><span class="sxs-lookup"><span data-stu-id="99b85-188">It is now easier to create a DbContext instance without any connection or connection string.</span></span>
<span data-ttu-id="99b85-189">此外，連接或連接字串現在可以在內容實例上進行變化。</span><span class="sxs-lookup"><span data-stu-id="99b85-189">Also, the connection or connection string can now be mutated on the context instance.</span></span>
<span data-ttu-id="99b85-190">這項功能可讓相同的內容實例動態連接到不同的資料庫。</span><span class="sxs-lookup"><span data-stu-id="99b85-190">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="99b85-191">檔是由問題[#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075)追蹤。</span><span class="sxs-lookup"><span data-stu-id="99b85-191">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="99b85-192">變更追蹤 proxy</span><span class="sxs-lookup"><span data-stu-id="99b85-192">Change-tracking proxies</span></span>

<span data-ttu-id="99b85-193">EF Core 現在可以產生自動執行[INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1)和[INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1)的執行時間 proxy。</span><span class="sxs-lookup"><span data-stu-id="99b85-193">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span>
<span data-ttu-id="99b85-194">這些之後，實體屬性上的這些值會直接變更為 EF Core，避免需要掃描變更。</span><span class="sxs-lookup"><span data-stu-id="99b85-194">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span>
<span data-ttu-id="99b85-195">不過，proxy 會有自己的一組限制，因此不適合所有人。</span><span class="sxs-lookup"><span data-stu-id="99b85-195">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="99b85-196">檔是由問題[#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076)追蹤。</span><span class="sxs-lookup"><span data-stu-id="99b85-196">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="99b85-197">增強的調試視圖</span><span class="sxs-lookup"><span data-stu-id="99b85-197">Enhanced debug views</span></span>

<span data-ttu-id="99b85-198">「偵錯工具」是一種簡單的方式，可在偵錯工具發生問題時查看 EF Core 的內部。</span><span class="sxs-lookup"><span data-stu-id="99b85-198">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span>
<span data-ttu-id="99b85-199">模型的「偵錯工具」視圖已在一段時間前完成。</span><span class="sxs-lookup"><span data-stu-id="99b85-199">A debug view for the Model was implemented some time ago.</span></span>
<span data-ttu-id="99b85-200">針對 EF Core 5.0，我們讓模型視圖更容易閱讀，並在狀態管理員中為追蹤的實體新增新的 [偵錯工具]。</span><span class="sxs-lookup"><span data-stu-id="99b85-200">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="99b85-201">[2019 年12月12日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)會包含初稿檔。</span><span class="sxs-lookup"><span data-stu-id="99b85-201">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="99b85-202">其他檔則是由問題[#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086)追蹤。</span><span class="sxs-lookup"><span data-stu-id="99b85-202">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="99b85-203">改善資料庫 null 語義的處理</span><span class="sxs-lookup"><span data-stu-id="99b85-203">Improved handling of database null semantics</span></span>

<span data-ttu-id="99b85-204">關係資料庫通常會將 Null 視為未知的值，因此不等於任何其他 Null。</span><span class="sxs-lookup"><span data-stu-id="99b85-204">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span>
<span data-ttu-id="99b85-205">雖然 c # 會將 null 視為已定義的值，但其比較會等於任何其他 null。</span><span class="sxs-lookup"><span data-stu-id="99b85-205">While C# treats null as a defined value, which compares equal to any other null.</span></span>
<span data-ttu-id="99b85-206">EF Core 預設會轉譯查詢，使其使用 c # null 語義。</span><span class="sxs-lookup"><span data-stu-id="99b85-206">EF Core by default translates queries so that they use C# null semantics.</span></span>
<span data-ttu-id="99b85-207">EF Core 5.0 大幅提升了這些翻譯的效率。</span><span class="sxs-lookup"><span data-stu-id="99b85-207">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="99b85-208">檔是由問題[#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612)追蹤。</span><span class="sxs-lookup"><span data-stu-id="99b85-208">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="99b85-209">索引子屬性</span><span class="sxs-lookup"><span data-stu-id="99b85-209">Indexer properties</span></span>

<span data-ttu-id="99b85-210">EF Core 5.0 支援 c # 索引子屬性的對應。</span><span class="sxs-lookup"><span data-stu-id="99b85-210">EF Core 5.0 supports mapping of C# indexer properties.</span></span>
<span data-ttu-id="99b85-211">這些屬性可讓實體作為屬性包，其中的資料行會對應到包中的命名屬性。</span><span class="sxs-lookup"><span data-stu-id="99b85-211">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="99b85-212">檔是由問題[#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018)追蹤。</span><span class="sxs-lookup"><span data-stu-id="99b85-212">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="99b85-213">列舉對應的檢查條件約束產生</span><span class="sxs-lookup"><span data-stu-id="99b85-213">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="99b85-214">EF Core 5.0 遷移現在可以產生 enum 屬性對應的檢查條件約束。</span><span class="sxs-lookup"><span data-stu-id="99b85-214">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span>
<span data-ttu-id="99b85-215">例如：</span><span class="sxs-lookup"><span data-stu-id="99b85-215">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="99b85-216">檔是由問題[#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082)追蹤。</span><span class="sxs-lookup"><span data-stu-id="99b85-216">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="99b85-217">IsRelational</span><span class="sxs-lookup"><span data-stu-id="99b85-217">IsRelational</span></span>

<span data-ttu-id="99b85-218">`IsRelational`除了現有的、和之外，還加入了新的方法 `IsSqlServer` `IsSqlite` `IsInMemory` 。</span><span class="sxs-lookup"><span data-stu-id="99b85-218">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span>
<span data-ttu-id="99b85-219">這個方法可以用來測試 DbCoNtext 是否使用任何關係資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="99b85-219">This method can be used to test if the DbContext is using any relational database provider.</span></span>
<span data-ttu-id="99b85-220">例如：</span><span class="sxs-lookup"><span data-stu-id="99b85-220">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="99b85-221">檔是由問題[#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185)追蹤。</span><span class="sxs-lookup"><span data-stu-id="99b85-221">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="99b85-222">使用 Etag Cosmos 開放式平行存取</span><span class="sxs-lookup"><span data-stu-id="99b85-222">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="99b85-223">Azure Cosmos DB 資料庫提供者現在支援使用 Etag 的開放式平行存取。</span><span class="sxs-lookup"><span data-stu-id="99b85-223">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span>
<span data-ttu-id="99b85-224">使用 OnModelCreating 中的模型產生器來設定 ETag：</span><span class="sxs-lookup"><span data-stu-id="99b85-224">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="99b85-225">接著，SaveChanges 會擲回 `DbUpdateConcurrencyException` 並行衝突，以[處理](https://docs.microsoft.com/ef/core/saving/concurrency)以執行重試等。</span><span class="sxs-lookup"><span data-stu-id="99b85-225">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](https://docs.microsoft.com/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="99b85-226">檔是由問題[#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099)追蹤。</span><span class="sxs-lookup"><span data-stu-id="99b85-226">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="99b85-227">更多日期時間結構的查詢翻譯</span><span class="sxs-lookup"><span data-stu-id="99b85-227">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="99b85-228">包含新日期時間結構的查詢現在已轉譯。</span><span class="sxs-lookup"><span data-stu-id="99b85-228">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="99b85-229">此外，現在已對應下列 SQL Server 函式：</span><span class="sxs-lookup"><span data-stu-id="99b85-229">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="99b85-230">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="99b85-230">DateDiffWeek</span></span>
* <span data-ttu-id="99b85-231">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="99b85-231">DateFromParts</span></span>

<span data-ttu-id="99b85-232">例如：</span><span class="sxs-lookup"><span data-stu-id="99b85-232">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="99b85-233">檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="99b85-233">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="99b85-234">更多位元組陣列結構的查詢翻譯</span><span class="sxs-lookup"><span data-stu-id="99b85-234">Query translations for more byte array constructs</span></span>

<span data-ttu-id="99b85-235">在 byte [] 屬性上使用 Contains、Length、Enumerable.sequenceequal 等的查詢現在會轉譯為 SQL。</span><span class="sxs-lookup"><span data-stu-id="99b85-235">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="99b85-236">[2019 年12月5日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)會包含初稿檔。</span><span class="sxs-lookup"><span data-stu-id="99b85-236">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="99b85-237">其他檔則是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="99b85-237">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="99b85-238">反向的查詢轉譯</span><span class="sxs-lookup"><span data-stu-id="99b85-238">Query translation for Reverse</span></span>

<span data-ttu-id="99b85-239">使用 `Reverse` 的查詢現在已轉譯。</span><span class="sxs-lookup"><span data-stu-id="99b85-239">Queries using `Reverse` are now translated.</span></span>
<span data-ttu-id="99b85-240">例如：</span><span class="sxs-lookup"><span data-stu-id="99b85-240">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="99b85-241">檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="99b85-241">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="99b85-242">位運算子的查詢轉譯</span><span class="sxs-lookup"><span data-stu-id="99b85-242">Query translation for bitwise operators</span></span>

<span data-ttu-id="99b85-243">使用位運算子的查詢現在會在更多情況下轉譯，例如：</span><span class="sxs-lookup"><span data-stu-id="99b85-243">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="99b85-244">檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="99b85-244">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="99b85-245">Cosmos 上字串的查詢轉譯</span><span class="sxs-lookup"><span data-stu-id="99b85-245">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="99b85-246">使用 Azure Cosmos DB 提供者時，現在會轉譯使用字串方法 Contains、StartsWith 和 EndsWith 的查詢。</span><span class="sxs-lookup"><span data-stu-id="99b85-246">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="99b85-247">檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="99b85-247">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
