---
title: EF Core 6.0 的新功能
description: EF Core 6.0 中的新功能總覽
author: ajcvickers
ms.date: 03/08/2021
uid: core/what-is-new/ef-core-6.0/whatsnew
ms.openlocfilehash: 15ab49f60d8831c60e599d8c06b3700aace74bda
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023519"
---
# <a name="whats-new-in-ef-core-60"></a><span data-ttu-id="45ad8-103">EF Core 6.0 的新功能</span><span class="sxs-lookup"><span data-stu-id="45ad8-103">What's New in EF Core 6.0</span></span>

<span data-ttu-id="45ad8-104">EF Core 6.0 目前正在開發中。</span><span class="sxs-lookup"><span data-stu-id="45ad8-104">EF Core 6.0 is currently in development.</span></span> <span data-ttu-id="45ad8-105">這包含每個預覽中所引進之有趣變更的總覽。</span><span class="sxs-lookup"><span data-stu-id="45ad8-105">This contains an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="45ad8-106">此頁面不會複製 [EF Core 6.0 的方案](xref:core/what-is-new/ef-core-6.0/plan)。</span><span class="sxs-lookup"><span data-stu-id="45ad8-106">This page does not duplicate the [plan for EF Core 6.0](xref:core/what-is-new/ef-core-6.0/plan).</span></span> <span data-ttu-id="45ad8-107">此計畫描述 EF Core 6.0 的整體主題，包括我們在出貨最終發行版本之前打算包含的所有專案。</span><span class="sxs-lookup"><span data-stu-id="45ad8-107">The plan describes the overall themes for EF Core 6.0, including everything we are planning to include before shipping the final release.</span></span>

> [!TIP]
> <span data-ttu-id="45ad8-108">您可以 [從 GitHub 下載範例程式碼](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/NewInEFCore6)，以執行並偵測到下列顯示的所有 preview 1 範例。</span><span class="sxs-lookup"><span data-stu-id="45ad8-108">You can run and debug into all the preview 1 samples shown below by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/NewInEFCore6).</span></span>

## <a name="ef-core-60-preview-2"></a><span data-ttu-id="45ad8-109">EF Core 6.0 Preview 2</span><span class="sxs-lookup"><span data-stu-id="45ad8-109">EF Core 6.0 Preview 2</span></span>

### <a name="preserve-synchronization-context-in-savechangesasync"></a><span data-ttu-id="45ad8-110">保留 SaveChangesAsync 中的同步處理內容</span><span class="sxs-lookup"><span data-stu-id="45ad8-110">Preserve synchronization context in SaveChangesAsync</span></span>

<span data-ttu-id="45ad8-111">GitHub 問題： [#23971](https://github.com/dotnet/efcore/issues/23971)。</span><span class="sxs-lookup"><span data-stu-id="45ad8-111">GitHub Issue: [#23971](https://github.com/dotnet/efcore/issues/23971).</span></span>

<span data-ttu-id="45ad8-112">我們 [已將5.0 版中的 EF Core 程式碼變更](https://github.com/dotnet/efcore/issues/10164) 為 <xref:System.Threading.Tasks.Task.ConfigureAwait%2A?displayProperty=nameWithType> `false` 在我們非同步程式碼的所有位置中設定為 `await` 。</span><span class="sxs-lookup"><span data-stu-id="45ad8-112">We [changed the EF Core code in the 5.0 release](https://github.com/dotnet/efcore/issues/10164) to set <xref:System.Threading.Tasks.Task.ConfigureAwait%2A?displayProperty=nameWithType> to `false` in all places where we `await` async code.</span></span> <span data-ttu-id="45ad8-113">這通常是 EF Core 使用方式的較佳選擇。</span><span class="sxs-lookup"><span data-stu-id="45ad8-113">This is generally a better choice for EF Core usage.</span></span> <span data-ttu-id="45ad8-114">不過，這 <xref:System.Data.Entity.DbContext.SaveChangesAsync%2A> 是特殊案例，因為 EF Core 會在非同步資料庫作業完成之後，將產生的值設定為追蹤的實體。</span><span class="sxs-lookup"><span data-stu-id="45ad8-114">However, <xref:System.Data.Entity.DbContext.SaveChangesAsync%2A> is a special case because EF Core will set generated values into tracked entities after the async database operation is complete.</span></span> <span data-ttu-id="45ad8-115">這些變更可能會觸發通知，例如，可能必須在 U.I. 上執行</span><span class="sxs-lookup"><span data-stu-id="45ad8-115">These changes may then trigger notifications which, for example, may have to run on the U.I.</span></span> <span data-ttu-id="45ad8-116">執行緒。</span><span class="sxs-lookup"><span data-stu-id="45ad8-116">thread.</span></span> <span data-ttu-id="45ad8-117">因此，我們只會在此方法的 EF Core 6.0 中還原這項變更 <xref:System.Data.Entity.DbContext.SaveChangesAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="45ad8-117">Therefore, we are reverting this change in EF Core 6.0 for the <xref:System.Data.Entity.DbContext.SaveChangesAsync%2A> method only.</span></span>

### <a name="translate-stringconcat-with-multiple-arguments"></a><span data-ttu-id="45ad8-118">使用多個引數來轉譯 String. Concat</span><span class="sxs-lookup"><span data-stu-id="45ad8-118">Translate String.Concat with multiple arguments</span></span>

<span data-ttu-id="45ad8-119">GitHub 問題： [#23859](https://github.com/dotnet/efcore/issues/23859)。</span><span class="sxs-lookup"><span data-stu-id="45ad8-119">GitHub Issue: [#23859](https://github.com/dotnet/efcore/issues/23859).</span></span> <span data-ttu-id="45ad8-120">這項功能是由所貢獻 [@wmeints](https://github.com/wmeints) 。</span><span class="sxs-lookup"><span data-stu-id="45ad8-120">This feature was contributed by [@wmeints](https://github.com/wmeints).</span></span>

<span data-ttu-id="45ad8-121">從 EF Core 6.0 開始， <xref:System.String.Concat%2A?displayProperty=nameWithType> 具有多個引數的呼叫現在會轉譯為 SQL。</span><span class="sxs-lookup"><span data-stu-id="45ad8-121">Starting with EF Core 6.0, calls to <xref:System.String.Concat%2A?displayProperty=nameWithType> with multiple arguments are now translated to SQL.</span></span> <span data-ttu-id="45ad8-122">例如，下列查詢：</span><span class="sxs-lookup"><span data-stu-id="45ad8-122">For example, the following query:</span></span>

<!--
        var shards = context.Shards
            .Where(e => string.Concat(e.Token1, e.Token2, e.Token3) != e.TokensProcessed).ToList();
-->
[!code-csharp[StringConcat](../../../../samples/core/Miscellaneous/NewInEFCore6/StringConcatSample.cs?name=StringConcat)]

<span data-ttu-id="45ad8-123">使用 SQL Server 時，將會轉譯為下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="45ad8-123">Will be translated to the following SQL when using SQL Server:</span></span>

```sql
SELECT [s].[Id], [s].[Token1], [s].[Token2], [s].[Token3], [s].[TokensProcessed]
FROM [Shards] AS [s]
WHERE ((COALESCE([s].[Token1], N'') + (COALESCE([s].[Token2], N'') + COALESCE([s].[Token3], N''))) <> [s].[TokensProcessed]) OR [s].[TokensProcessed] IS NULL
```

### <a name="smoother-integration-with-systemlinqasync"></a><span data-ttu-id="45ad8-124">與 System.object 的整合更加順暢</span><span class="sxs-lookup"><span data-stu-id="45ad8-124">Smoother integration with System.Linq.Async</span></span>

<span data-ttu-id="45ad8-125">GitHub 問題： [#24041](https://github.com/dotnet/efcore/issues/24041)。</span><span class="sxs-lookup"><span data-stu-id="45ad8-125">GitHub Issue: [#24041](https://github.com/dotnet/efcore/issues/24041).</span></span>

<span data-ttu-id="45ad8-126">System.string [封裝會](https://www.nuget.org/packages/System.Linq.Async/) 加入用戶端非同步 Linq 處理。</span><span class="sxs-lookup"><span data-stu-id="45ad8-126">The [System.Linq.Async](https://www.nuget.org/packages/System.Linq.Async/) package adds client-side async LINQ processing.</span></span> <span data-ttu-id="45ad8-127">將此套件與舊版 EF Core 搭配使用，會因為非同步 LINQ 方法的命名空間衝突而繁瑣。</span><span class="sxs-lookup"><span data-stu-id="45ad8-127">Using this package with previous versions of EF Core was cumbersome due to a namespace clash for the async LINQ methods.</span></span> <span data-ttu-id="45ad8-128">在 EF Core 6.0 中，我們已利用 c # 模式比對，讓 <xref:System.Collections.Generic.IAsyncEnumerable%601> 公開的 EF Core 不 <xref:Microsoft.EntityFrameworkCore.DbSet%601> 需要直接執行介面。</span><span class="sxs-lookup"><span data-stu-id="45ad8-128">In EF Core 6.0 we have taken advantage of C# pattern matching for <xref:System.Collections.Generic.IAsyncEnumerable%601> such that the exposed EF Core <xref:Microsoft.EntityFrameworkCore.DbSet%601> does not need to implement the interface directly.</span></span>

<span data-ttu-id="45ad8-129">請注意，大部分的應用程式不需要使用 system.string，因為 EF Core 查詢通常會在伺服器上完整轉譯。</span><span class="sxs-lookup"><span data-stu-id="45ad8-129">Note that most applications do not need to use System.Linq.Async since EF Core queries are usually fully translated on the server.</span></span>

### <a name="more-flexible-free-text-search"></a><span data-ttu-id="45ad8-130">更具彈性的自由文字搜尋</span><span class="sxs-lookup"><span data-stu-id="45ad8-130">More flexible free-text search</span></span>

<span data-ttu-id="45ad8-131">GitHub 問題： [#23921](https://github.com/dotnet/efcore/issues/23921)。</span><span class="sxs-lookup"><span data-stu-id="45ad8-131">GitHub Issue: [#23921](https://github.com/dotnet/efcore/issues/23921).</span></span>

<span data-ttu-id="45ad8-132">在 EF Core 6.0 中，我們已放寬和的參數 <xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.FreeText(Microsoft.EntityFrameworkCore.DbFunctions,System.String,System.String)> 需求 <xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.Contains%2A> 。</span><span class="sxs-lookup"><span data-stu-id="45ad8-132">In EF Core 6.0, we have relaxed the parameter requirements for <xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.FreeText(Microsoft.EntityFrameworkCore.DbFunctions,System.String,System.String)> and <xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.Contains%2A>.</span></span> <span data-ttu-id="45ad8-133">這可讓這些函式搭配二進位資料行使用，或與使用值轉換器對應的資料行搭配使用。</span><span class="sxs-lookup"><span data-stu-id="45ad8-133">This allows these functions to be used with binary columns, or with columns mapped using a value converter.</span></span> <span data-ttu-id="45ad8-134">例如，假設某個實體類型的 `Name` 屬性定義為值物件：</span><span class="sxs-lookup"><span data-stu-id="45ad8-134">For example, consider an entity type with a `Name` property defined as a value object:</span></span>

<!--
    public class Customer
    {
        public int Id { get; set; }

        public Name Name{ get; set; }
    }

    public class Name
    {
        public string First { get; set; }
        public string MiddleInitial { get; set; }
        public string Last { get; set; }
    }
-->
[!code-csharp[EntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/ContainsFreeTextSample.cs?name=EntityType)]

<span data-ttu-id="45ad8-135">這會對應到資料庫中的 JSON：</span><span class="sxs-lookup"><span data-stu-id="45ad8-135">This is mapped to JSON in the database:</span></span>

<!--
            modelBuilder.Entity<Customer>()
                .Property(e => e.Name)
                .HasConversion(
                    v => JsonSerializer.Serialize(v, null),
                    v => JsonSerializer.Deserialize<Name>(v, null));
-->
[!code-csharp[ConfigureCompositeValueObject](../../../../samples/core/Miscellaneous/NewInEFCore6/ContainsFreeTextSample.cs?name=ConfigureCompositeValueObject)]

<span data-ttu-id="45ad8-136">您現在可以使用 `Contains` 或， `FreeText` 即使屬性的類型不是，也可以執行 `Name` 查詢 `string` 。</span><span class="sxs-lookup"><span data-stu-id="45ad8-136">A query can now be executed using `Contains` or `FreeText` even though the type of the property is `Name` not `string`.</span></span> <span data-ttu-id="45ad8-137">例如：</span><span class="sxs-lookup"><span data-stu-id="45ad8-137">For example:</span></span>

<!--
        var result = context.Customers.Where(e => EF.Functions.Contains(e.Name, "Martin")).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/ContainsFreeTextSample.cs?name=Query)]

<span data-ttu-id="45ad8-138">使用 SQL Server 時，這會產生下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="45ad8-138">This generates the following SQL, when using SQL Server:</span></span>

```sql
SELECT [c].[Id], [c].[Name]
FROM [Customers] AS [c]
WHERE CONTAINS([c].[Name], N'Martin')
```

## <a name="ef-core-60-preview-1"></a><span data-ttu-id="45ad8-139">EF Core 6.0 Preview 1</span><span class="sxs-lookup"><span data-stu-id="45ad8-139">EF Core 6.0 Preview 1</span></span>

### <a name="unicodeattribute"></a><span data-ttu-id="45ad8-140">UnicodeAttribute</span><span class="sxs-lookup"><span data-stu-id="45ad8-140">UnicodeAttribute</span></span>

<span data-ttu-id="45ad8-141">GitHub 問題： [#19794](https://github.com/dotnet/efcore/issues/19794)。</span><span class="sxs-lookup"><span data-stu-id="45ad8-141">GitHub Issue: [#19794](https://github.com/dotnet/efcore/issues/19794).</span></span> <span data-ttu-id="45ad8-142">這項功能是由所貢獻 [@RaymondHuy](https://github.com/RaymondHuy) 。</span><span class="sxs-lookup"><span data-stu-id="45ad8-142">This feature was contributed by [@RaymondHuy](https://github.com/RaymondHuy).</span></span>

<span data-ttu-id="45ad8-143">從 EF Core 6.0 開始，您現在可以使用對應屬性，將字串屬性對應到非 Unicode 資料行， _而不需要直接指定資料庫類型_。</span><span class="sxs-lookup"><span data-stu-id="45ad8-143">Starting with EF Core 6.0, a string property can now be mapped to a non-Unicode column using a mapping attribute _without specifying the database type directly_.</span></span> <span data-ttu-id="45ad8-144">例如，假設有一個 `Book` 實體類型具有 [國際標準書籍編號的屬性， (isbn) ](https://en.wikipedia.org/wiki/International_Standard_Book_Number) 格式為 "isbn 978-3-16-148410-0"：</span><span class="sxs-lookup"><span data-stu-id="45ad8-144">For example, consider a `Book` entity type with a property for the [International Standard Book Number (ISBN)](https://en.wikipedia.org/wiki/International_Standard_Book_Number) in the form "ISBN 978-3-16-148410-0":</span></span>

<!--
    public class Book
    {
        public int Id { get; set; }
        public string Title { get; set; }

        [Unicode(false)]
        [MaxLength(22)]
        public string Isbn { get; set; }
    }
-->
[!code-csharp[BookEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/UnicodeAttributeSample.cs?name=BookEntityType)]

<span data-ttu-id="45ad8-145">由於 ISBNs 不能包含任何非 unicode 字元，因此 `Unicode` 屬性會導致使用非 unicode 字串類型。</span><span class="sxs-lookup"><span data-stu-id="45ad8-145">Since ISBNs cannot contain any non-unicode characters, the `Unicode` attribute will cause a non-Unicode string type to be used.</span></span> <span data-ttu-id="45ad8-146">此外， `MaxLength` 也會用來限制資料庫資料行的大小。</span><span class="sxs-lookup"><span data-stu-id="45ad8-146">In addition, `MaxLength` is used to limit the size of the database column.</span></span> <span data-ttu-id="45ad8-147">例如，使用 SQL Server 時，這會產生的資料庫資料行 `varchar(22)` ：</span><span class="sxs-lookup"><span data-stu-id="45ad8-147">For example, when using SQL Server, this results in a database column of `varchar(22)`:</span></span>

```sql
CREATE TABLE [Book] (
    [Id] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Isbn] varchar(22) NULL,
    CONSTRAINT [PK_Book] PRIMARY KEY ([Id]));
```

> [!NOTE]
> <span data-ttu-id="45ad8-148">EF Core 預設會將字串屬性對應至 Unicode 資料行。</span><span class="sxs-lookup"><span data-stu-id="45ad8-148">EF Core maps string properties to Unicode columns by default.</span></span> <span data-ttu-id="45ad8-149">`UnicodeAttribute` 當資料庫系統只支援 Unicode 類型時，會忽略。</span><span class="sxs-lookup"><span data-stu-id="45ad8-149">`UnicodeAttribute` is ignored when the database system supports only Unicode types.</span></span>

### <a name="precisionattribute"></a><span data-ttu-id="45ad8-150">PrecisionAttribute</span><span class="sxs-lookup"><span data-stu-id="45ad8-150">PrecisionAttribute</span></span>

<span data-ttu-id="45ad8-151">GitHub 問題： [#17914](https://github.com/dotnet/efcore/issues/17914)。</span><span class="sxs-lookup"><span data-stu-id="45ad8-151">GitHub Issue: [#17914](https://github.com/dotnet/efcore/issues/17914).</span></span> <span data-ttu-id="45ad8-152">這項功能是由所貢獻 [@RaymondHuy](https://github.com/RaymondHuy) 。</span><span class="sxs-lookup"><span data-stu-id="45ad8-152">This feature was contributed by [@RaymondHuy](https://github.com/RaymondHuy).</span></span>

<span data-ttu-id="45ad8-153">您現在可以使用對應屬性來設定資料庫資料行的有效位數和小數位數， _而不需要直接指定資料庫類型_。</span><span class="sxs-lookup"><span data-stu-id="45ad8-153">The precision and scale of a database column can now be configured using mapping attributes _without specifying the database type directly_.</span></span> <span data-ttu-id="45ad8-154">例如，假設有一個 `Product` 具有 decimal 屬性的實體類型 `Price` ：</span><span class="sxs-lookup"><span data-stu-id="45ad8-154">For example, consider a `Product` entity type with a decimal `Price` property:</span></span>

<!--
    public class Product
    {
        public int Id { get; set; }

        [Precision(precision: 10, scale: 2)]
        public decimal Price { get; set; }
    }
-->
[!code-csharp[ProductEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/PrecisionAttributeSample.cs?name=ProductEntityType)]

<span data-ttu-id="45ad8-155">EF Core 會將此屬性對應至精確度為10且小數位數為2的資料庫資料行。</span><span class="sxs-lookup"><span data-stu-id="45ad8-155">EF Core will map this property to a database column with precision 10 and scale 2.</span></span> <span data-ttu-id="45ad8-156">例如，在 SQL Server 上：</span><span class="sxs-lookup"><span data-stu-id="45ad8-156">For example, on SQL Server:</span></span>

```sql
CREATE TABLE [Product] (
    [Id] int NOT NULL IDENTITY,
    [Price] decimal(10,2) NOT NULL,
    CONSTRAINT [PK_Product] PRIMARY KEY ([Id]));
```

### <a name="entitytypeconfigurationattribute"></a><span data-ttu-id="45ad8-157">EntityTypeConfigurationAttribute</span><span class="sxs-lookup"><span data-stu-id="45ad8-157">EntityTypeConfigurationAttribute</span></span>

<span data-ttu-id="45ad8-158">GitHub 問題： [#23163](https://github.com/dotnet/efcore/issues/23163)。</span><span class="sxs-lookup"><span data-stu-id="45ad8-158">GitHub Issue: [#23163](https://github.com/dotnet/efcore/issues/23163).</span></span> <span data-ttu-id="45ad8-159">這項功能是由所貢獻 [@KaloyanIT](https://github.com/KaloyanIT) 。</span><span class="sxs-lookup"><span data-stu-id="45ad8-159">This feature was contributed by [@KaloyanIT](https://github.com/KaloyanIT).</span></span>

<span data-ttu-id="45ad8-160"><xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601> 實例可讓 <xref:Microsoft.EntityFrameworkCore.ModelBuilder> 每個實體類型的設定都包含在自己的設定類別中。</span><span class="sxs-lookup"><span data-stu-id="45ad8-160"><xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601> instances allow <xref:Microsoft.EntityFrameworkCore.ModelBuilder> configuration for a each entity type to be contained in its own configuration class.</span></span> <span data-ttu-id="45ad8-161">例如：</span><span class="sxs-lookup"><span data-stu-id="45ad8-161">For example:</span></span>

<!--
public class BookConfiguration : IEntityTypeConfiguration<Book>
{
    public void Configure(EntityTypeBuilder<Book> builder)
    {
        builder
            .Property(e => e.Isbn)
            .IsUnicode(false)
            .HasMaxLength(22);
    }
}
-->
[!code-csharp[BookConfiguration](../../../../samples/core/Miscellaneous/NewInEFCore6/EntityTypeConfigurationAttributeSample.cs?name=BookConfiguration)]

<span data-ttu-id="45ad8-162">一般來說，此設定類別必須具現化，並從中呼叫 <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="45ad8-162">Normally, this configuration class must be instantiated and called into from <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="45ad8-163">例如：</span><span class="sxs-lookup"><span data-stu-id="45ad8-163">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    new BookConfiguration().Configure(modelBuilder.Entity<Book>());
}
```

<span data-ttu-id="45ad8-164">從 EF Core 6.0 開始， `EntityTypeConfigurationAttribute` 可以在實體類型上放置，讓 EF Core 可以尋找並使用適當的設定。</span><span class="sxs-lookup"><span data-stu-id="45ad8-164">Starting with EF Core 6.0, an `EntityTypeConfigurationAttribute` can be placed on the entity type such that EF Core can find and use appropriate configuration.</span></span> <span data-ttu-id="45ad8-165">例如：</span><span class="sxs-lookup"><span data-stu-id="45ad8-165">For example:</span></span>

<!--
[EntityTypeConfiguration(typeof(BookConfiguration))]
public class Book
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Isbn { get; set; }
}
-->
[!code-csharp[BookEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/EntityTypeConfigurationAttributeSample.cs?name=BookEntityType)]

<span data-ttu-id="45ad8-166">這個屬性工作表示 `IEntityTypeConfiguration` 當 `Book` 模型中包含實體類型時，EF Core 將會使用指定的實作為。</span><span class="sxs-lookup"><span data-stu-id="45ad8-166">This attribute means that EF Core will use the specified `IEntityTypeConfiguration` implementation whenever the `Book` entity type is included in a model.</span></span> <span data-ttu-id="45ad8-167">實體類型會包含在模型中，使用其中一個一般機制。</span><span class="sxs-lookup"><span data-stu-id="45ad8-167">The entity type is included in a model using one of the normal mechanisms.</span></span> <span data-ttu-id="45ad8-168">例如，藉由建立 <xref:Microsoft.EntityFrameworkCore.DbSet%601> 實體類型的屬性：</span><span class="sxs-lookup"><span data-stu-id="45ad8-168">For example, by creating a <xref:Microsoft.EntityFrameworkCore.DbSet%601> property for the entity type:</span></span>

<!--
public class BooksContext : DbContext
{
    public DbSet<Book> Books { get; set; }

    //...
-->
[!code-csharp[DbContext](../../../../samples/core/Miscellaneous/NewInEFCore6/EntityTypeConfigurationAttributeSample.cs?name=DbContext)]

<span data-ttu-id="45ad8-169">或者，在中註冊它 <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> ：</span><span class="sxs-lookup"><span data-stu-id="45ad8-169">Or by registering it in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Book>();
}
```

> [!NOTE]
> <span data-ttu-id="45ad8-170">`EntityTypeConfigurationAttribute` 元件中將不會自動探索類型。</span><span class="sxs-lookup"><span data-stu-id="45ad8-170">`EntityTypeConfigurationAttribute` types will not be automatically discovered in an assembly.</span></span> <span data-ttu-id="45ad8-171">實體類型必須加入至模型，然後才會在該實體類型上探索屬性。</span><span class="sxs-lookup"><span data-stu-id="45ad8-171">Entity types must be added to the model before the attribute will be discovered on that entity type.</span></span>

### <a name="translate-tostring-on-sqlite"></a><span data-ttu-id="45ad8-172">在 SQLite 上轉譯 ToString</span><span class="sxs-lookup"><span data-stu-id="45ad8-172">Translate ToString on SQLite</span></span>

<span data-ttu-id="45ad8-173">GitHub 問題： [#17223](https://github.com/dotnet/efcore/issues/17223)。</span><span class="sxs-lookup"><span data-stu-id="45ad8-173">GitHub Issue: [#17223](https://github.com/dotnet/efcore/issues/17223).</span></span> <span data-ttu-id="45ad8-174">這項功能是由所貢獻 [@ralmsdeveloper](https://github.com/ralmsdeveloper) 。</span><span class="sxs-lookup"><span data-stu-id="45ad8-174">This feature was contributed by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span>

<span data-ttu-id="45ad8-175"><xref:System.Object.ToString>使用 SQLite 資料庫提供者時，的呼叫現在會轉譯為 SQL。</span><span class="sxs-lookup"><span data-stu-id="45ad8-175">Calls to <xref:System.Object.ToString> are now translated to SQL when using the SQLite database provider.</span></span> <span data-ttu-id="45ad8-176">這適用于涉及非字串資料行的文字搜尋。</span><span class="sxs-lookup"><span data-stu-id="45ad8-176">This can be useful for text searches involving non-string columns.</span></span> <span data-ttu-id="45ad8-177">例如，假設將 `User` 電話號碼儲存為數值的實體類型：</span><span class="sxs-lookup"><span data-stu-id="45ad8-177">For example, consider a `User` entity type that stores phone numbers as numeric values:</span></span>

<!--
    public class User
    {
        public int Id { get; set; }
        public string Username { get; set; }
        public long PhoneNumber { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/ToStringTranslationSample.cs?name=UserEntityType)]

<span data-ttu-id="45ad8-178">`ToString` 可以用來將數位轉換成資料庫中的字串。</span><span class="sxs-lookup"><span data-stu-id="45ad8-178">`ToString` can be used to convert the number to a string in the database.</span></span> <span data-ttu-id="45ad8-179">接著，我們可以使用此字串搭配函式（例如） `LIKE` 來尋找符合模式的數位。</span><span class="sxs-lookup"><span data-stu-id="45ad8-179">We can then use this string with a function such as `LIKE` to find numbers that match a pattern.</span></span> <span data-ttu-id="45ad8-180">例如，若要尋找包含555的所有數位：</span><span class="sxs-lookup"><span data-stu-id="45ad8-180">For example, to find all numbers containing 555:</span></span>

<!--
var users = context.Users.Where(u => EF.Functions.Like(u.PhoneNumber.ToString(), "%555%")).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/ToStringTranslationSample.cs?name=Query)]

<span data-ttu-id="45ad8-181">使用 SQLite 資料庫時，這會轉譯成下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="45ad8-181">This translates to the following SQL when using a SQLite database:</span></span>

```sql
SELECT COUNT(*)
FROM "Users" AS "u"
WHERE CAST("u"."PhoneNumber" AS TEXT) LIKE '%555%'
```

<span data-ttu-id="45ad8-182">請注意， <xref:System.Object.ToString> EF Core 5.0 已支援 FOR SQL Server 的翻譯，其他資料庫提供者也可能支援此功能。</span><span class="sxs-lookup"><span data-stu-id="45ad8-182">Note that translation of <xref:System.Object.ToString> for SQL Server is already supported in EF Core 5.0, and may also be supported by other database providers.</span></span>

### <a name="effunctionsrandom"></a><span data-ttu-id="45ad8-183">英 孚。函數。 Random</span><span class="sxs-lookup"><span data-stu-id="45ad8-183">EF.Functions.Random</span></span>

<span data-ttu-id="45ad8-184">GitHub 問題： [#16141](https://github.com/dotnet/efcore/issues/16141)。</span><span class="sxs-lookup"><span data-stu-id="45ad8-184">GitHub Issue: [#16141](https://github.com/dotnet/efcore/issues/16141).</span></span> <span data-ttu-id="45ad8-185">這項功能是由所貢獻 [@RaymondHuy](https://github.com/RaymondHuy) 。</span><span class="sxs-lookup"><span data-stu-id="45ad8-185">This feature was contributed by [@RaymondHuy](https://github.com/RaymondHuy).</span></span>

<span data-ttu-id="45ad8-186">`EF.Functions.Random` 對應至資料庫函式，其會傳回介於0和1之間的虛擬亂數。</span><span class="sxs-lookup"><span data-stu-id="45ad8-186">`EF.Functions.Random` maps to a database function returning a pseudo-random number between 0 and 1 exclusive.</span></span> <span data-ttu-id="45ad8-187">適用于 SQL Server、SQLite 和 Cosmos 的 EF Core 存放庫中已執行翻譯。</span><span class="sxs-lookup"><span data-stu-id="45ad8-187">Translations have been implemented in the EF Core repo for SQL Server, SQLite, and Cosmos.</span></span> <span data-ttu-id="45ad8-188">例如，假設有一個 `User` 具有屬性的實體類型 `Popularity` ：</span><span class="sxs-lookup"><span data-stu-id="45ad8-188">For example, consider a `User` entity type with a `Popularity` property:</span></span>

<!--
    public class User
    {
        public int Id { get; set; }
        public string Username { get; set; }
        public int Popularity { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/RandomFunctionSample.cs?name=UserEntityType)]

<span data-ttu-id="45ad8-189">`Popularity` 可以有1到5（含）的值。</span><span class="sxs-lookup"><span data-stu-id="45ad8-189">`Popularity` can have values from 1 to 5 inclusive.</span></span> <span data-ttu-id="45ad8-190">使用 `EF.Functions.Random` 我們可以撰寫查詢，以傳回具有隨機播放熱門程度的所有使用者：</span><span class="sxs-lookup"><span data-stu-id="45ad8-190">Using `EF.Functions.Random` we can write a query to return all users with a randomly chosen popularity:</span></span>

<!--
var users = context.Users.Where(u => u.Popularity == (int)(EF.Functions.Random() * 5.0) + 1).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/RandomFunctionSample.cs?name=Query)]

<span data-ttu-id="45ad8-191">使用 SQL Server 資料庫時，這會轉換成下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="45ad8-191">This translates to the following SQL when using a SQL Server database:</span></span>

```sql
SELECT [u].[Id], [u].[Popularity], [u].[Username]
FROM [Users] AS [u]
WHERE [u].[Popularity] = (CAST((RAND() * 5.0E0) AS int) + 1)
```

### <a name="support-for-sql-server-sparse-columns"></a><span data-ttu-id="45ad8-192">支援 SQL Server sparse 資料行</span><span class="sxs-lookup"><span data-stu-id="45ad8-192">Support for SQL Server sparse columns</span></span>

<span data-ttu-id="45ad8-193">GitHub 問題： [#8023](https://github.com/dotnet/efcore/issues/8023)。</span><span class="sxs-lookup"><span data-stu-id="45ad8-193">GitHub Issue: [#8023](https://github.com/dotnet/efcore/issues/8023).</span></span>

<span data-ttu-id="45ad8-194">SQL Server [稀疏資料行](/sql/relational-databases/tables/use-sparse-columns) 是經過優化以儲存 null 值的一般資料行。</span><span class="sxs-lookup"><span data-stu-id="45ad8-194">SQL Server [sparse columns](/sql/relational-databases/tables/use-sparse-columns) are ordinary columns that are optimized to store null values.</span></span> <span data-ttu-id="45ad8-195">這在使用 [TPH 繼承對應](xref:core/modeling/inheritance) 時很有用，因為很少使用的子類型屬性會導致資料表中大部分資料列的 null 資料行值。</span><span class="sxs-lookup"><span data-stu-id="45ad8-195">This can be useful when using [TPH inheritance mapping](xref:core/modeling/inheritance) where properties of a rarely used subtype will result in null column values for most rows in the table.</span></span> <span data-ttu-id="45ad8-196">例如，假設有一個 `ForumModerator` 延伸自的類別 `ForumUser` ：</span><span class="sxs-lookup"><span data-stu-id="45ad8-196">For example, consider a `ForumModerator` class that extends from `ForumUser`:</span></span>

<!--
    public class ForumUser
    {
        public int Id { get; set; }
        public string Username { get; set; }
    }

    public class ForumModerator : ForumUser
    {
        public string ForumName { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/SparseColumnsSample.cs?name=UserEntityType)]

<span data-ttu-id="45ad8-197">可能有數百萬個使用者，只有少數的使用者是仲裁者。</span><span class="sxs-lookup"><span data-stu-id="45ad8-197">There may be millions of users, with only a handful of these being moderators.</span></span> <span data-ttu-id="45ad8-198">這表示在這裡將對應 `ForumName` 為稀疏可能有意義。</span><span class="sxs-lookup"><span data-stu-id="45ad8-198">This means mapping the `ForumName` as sparse might make sense here.</span></span> <span data-ttu-id="45ad8-199">這現在可以使用 `IsSparse` 中的來設定 <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> 。</span><span class="sxs-lookup"><span data-stu-id="45ad8-199">This can now be configured using `IsSparse` in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="45ad8-200">例如：</span><span class="sxs-lookup"><span data-stu-id="45ad8-200">For example:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .Entity<ForumModerator>()
                .Property(e => e.ForumName)
                .IsSparse();
        }
-->
[!code-csharp[OnModelCreating](../../../../samples/core/Miscellaneous/NewInEFCore6/SparseColumnsSample.cs?name=OnModelCreating)]

<span data-ttu-id="45ad8-201">EF Core 遷移會將資料行標示為「稀疏」。</span><span class="sxs-lookup"><span data-stu-id="45ad8-201">EF Core migrations will then mark the column as sparse.</span></span> <span data-ttu-id="45ad8-202">例如：</span><span class="sxs-lookup"><span data-stu-id="45ad8-202">For example:</span></span>

```sql
CREATE TABLE [ForumUser] (
    [Id] int NOT NULL IDENTITY,
    [Username] nvarchar(max) NULL,
    [Discriminator] nvarchar(max) NOT NULL,
    [ForumName] nvarchar(max) SPARSE NULL,
    CONSTRAINT [PK_ForumUser] PRIMARY KEY ([Id]));
```

> [!NOTE]
> <span data-ttu-id="45ad8-203">稀疏資料行有限制。</span><span class="sxs-lookup"><span data-stu-id="45ad8-203">Sparse columns have limitations.</span></span> <span data-ttu-id="45ad8-204">請務必閱讀 [SQL Server sparse 資料行檔](/sql/relational-databases/tables/use-sparse-columns) ，以確保稀疏資料行是您案例的正確選擇。</span><span class="sxs-lookup"><span data-stu-id="45ad8-204">Make sure to read the [SQL Server sparse columns documentation](/sql/relational-databases/tables/use-sparse-columns) to ensure that sparse columns are the right choice for your scenario.</span></span>

### <a name="in-memory-database-validate-required-properties-are-not-null"></a><span data-ttu-id="45ad8-205">記憶體內部資料庫：驗證必要的屬性不是 null</span><span class="sxs-lookup"><span data-stu-id="45ad8-205">In-memory database: validate required properties are not null</span></span>

<span data-ttu-id="45ad8-206">GitHub 問題： [#10613](https://github.com/dotnet/efcore/issues/10613)。</span><span class="sxs-lookup"><span data-stu-id="45ad8-206">GitHub Issue: [#10613](https://github.com/dotnet/efcore/issues/10613).</span></span> <span data-ttu-id="45ad8-207">這項功能是由所貢獻 [@fagnercarvalho](https://github.com/fagnercarvalho) 。</span><span class="sxs-lookup"><span data-stu-id="45ad8-207">This feature was contributed by [@fagnercarvalho](https://github.com/fagnercarvalho).</span></span>

<span data-ttu-id="45ad8-208">如果嘗試針對標示為必要的屬性儲存 null 值，則 EF Core 記憶體內部資料庫現在會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="45ad8-208">The EF Core in-memory database will now throw an exception if an attempt is made to save a null value for a property marked as required.</span></span> <span data-ttu-id="45ad8-209">例如，假設有一個 `User` 具有必要屬性的型別 `Username` ：</span><span class="sxs-lookup"><span data-stu-id="45ad8-209">For example, consider a `User` type with a required `Username` property:</span></span>

<!--
    public class User
    {
        public int Id { get; set; }

        [Required]
        public string Username { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/InMemoryRequiredPropertiesSample.cs?name=UserEntityType)]

<span data-ttu-id="45ad8-210">嘗試以 null 儲存實體 `Username` 將會導致下列例外狀況：</span><span class="sxs-lookup"><span data-stu-id="45ad8-210">Attempting to save an entity with a null `Username` will result in the following exception:</span></span>

> <span data-ttu-id="45ad8-211">Microsoft.entityframeworkcore. 就 dbupdateexception：具有索引鍵值 ' {Id： 1} ' 的實體類型 ' User ' 實例缺少必要屬性 ' {' Username '} '。</span><span class="sxs-lookup"><span data-stu-id="45ad8-211">Microsoft.EntityFrameworkCore.DbUpdateException: Required properties '{'Username'}' are missing for the instance of entity type 'User' with the key value '{Id: 1}'.</span></span>

<span data-ttu-id="45ad8-212">如有必要，可以停用此驗證。</span><span class="sxs-lookup"><span data-stu-id="45ad8-212">This validation can be disabled if necessary.</span></span> <span data-ttu-id="45ad8-213">例如：</span><span class="sxs-lookup"><span data-stu-id="45ad8-213">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder
                .LogTo(Console.WriteLine, new[] { InMemoryEventId.ChangesSaved })
                .UseInMemoryDatabase("UserContextWithNullCheckingDisabled")
                .EnableNullabilityCheck(false);
        }
-->
[!code-csharp[OnConfiguring](../../../../samples/core/Miscellaneous/NewInEFCore6/InMemoryRequiredPropertiesSample.cs?name=OnConfiguring)]

### <a name="improved-sql-server-translation-for-isnullorwhitespace"></a><span data-ttu-id="45ad8-214">改進適用于 IsNullOrWhitespace 的 SQL Server 轉譯</span><span class="sxs-lookup"><span data-stu-id="45ad8-214">Improved SQL Server translation for IsNullOrWhitespace</span></span>

<span data-ttu-id="45ad8-215">GitHub 問題： [#22916](https://github.com/dotnet/efcore/issues/22916)。</span><span class="sxs-lookup"><span data-stu-id="45ad8-215">GitHub Issue: [#22916](https://github.com/dotnet/efcore/issues/22916).</span></span> <span data-ttu-id="45ad8-216">這項功能是由所貢獻 [@Marusyk](https://github.com/Marusyk) 。</span><span class="sxs-lookup"><span data-stu-id="45ad8-216">This feature was contributed by [@Marusyk](https://github.com/Marusyk).</span></span>

<span data-ttu-id="45ad8-217">請考慮下列查詢：</span><span class="sxs-lookup"><span data-stu-id="45ad8-217">Consider the following query:</span></span>

<!--
        var users = context.Users.Where(
            e => string.IsNullOrWhiteSpace(e.FirstName)
                 || string.IsNullOrWhiteSpace(e.LastName)).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/IsNullOrWhitespaceSample.cs?name=Query)]

<span data-ttu-id="45ad8-218">在 EF Core 6.0 之前，這會在 SQL Server 上轉譯為下列內容：</span><span class="sxs-lookup"><span data-stu-id="45ad8-218">Before EF Core 6.0, this was translated to the following on SQL Server:</span></span>

```sql
SELECT [u].[Id], [u].[FirstName], [u].[LastName]
FROM [Users] AS [u]
WHERE ([u].[FirstName] IS NULL OR (LTRIM(RTRIM([u].[FirstName])) = N'')) OR ([u].[LastName] IS NULL OR (LTRIM(RTRIM([u].[LastName])) = N''))
```

<span data-ttu-id="45ad8-219">EF Core 6.0 的這種轉譯已經過改善，可讓您：</span><span class="sxs-lookup"><span data-stu-id="45ad8-219">This translation has been improved for EF Core 6.0 to:</span></span>

```sql
SELECT [u].[Id], [u].[FirstName], [u].[LastName]
FROM [Users] AS [u]
WHERE ([u].[FirstName] IS NULL OR ([u].[FirstName] = N'')) OR ([u].[LastName] IS NULL OR ([u].[LastName] = N''))
```

### <a name="database-comments-are-scaffolded-to-code-comments"></a><span data-ttu-id="45ad8-220">資料庫批註 scaffold 至程式碼批註</span><span class="sxs-lookup"><span data-stu-id="45ad8-220">Database comments are scaffolded to code comments</span></span>

<span data-ttu-id="45ad8-221">GitHub 問題： [#19113](https://github.com/dotnet/efcore/issues/19113)。</span><span class="sxs-lookup"><span data-stu-id="45ad8-221">GitHub Issue: [#19113](https://github.com/dotnet/efcore/issues/19113).</span></span> <span data-ttu-id="45ad8-222">這項功能是由所貢獻 [@ErikEJ](https://github.com/ErikEJ) 。</span><span class="sxs-lookup"><span data-stu-id="45ad8-222">This feature was contributed by [@ErikEJ](https://github.com/ErikEJ).</span></span>

<span data-ttu-id="45ad8-223">SQL 資料表和資料行的批註現在 scaffold 至從現有 SQL Server 資料庫 [對 EF Core 模型進行反向工程](xref:core/managing-schemas/scaffolding) 時所建立的實體類型。</span><span class="sxs-lookup"><span data-stu-id="45ad8-223">Comments on SQL tables and columns are now scaffolded into the entity types created when [reverse-engineering an EF Core model](xref:core/managing-schemas/scaffolding) from an existing SQL Server database.</span></span> <span data-ttu-id="45ad8-224">例如：</span><span class="sxs-lookup"><span data-stu-id="45ad8-224">For example:</span></span>

```csharp
/// <summary>
/// The Blog table.
/// </summary>
public partial class Blog
{
    /// <summary>
    /// The primary key.
    /// </summary>
    [Key]
    public int Id { get; set; }
}
```

## <a name="microsoftdatasqlite-60-preview-1"></a><span data-ttu-id="45ad8-225">Microsoft. Sqlite 6.0 Preview 1</span><span class="sxs-lookup"><span data-stu-id="45ad8-225">Microsoft.Data.Sqlite 6.0 Preview 1</span></span>

> [!TIP]
> <span data-ttu-id="45ad8-226">您可以 [從 GitHub 下載範例程式碼](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/NewInEFCore6)，以執行並偵測到下列顯示的所有 preview 1 範例。</span><span class="sxs-lookup"><span data-stu-id="45ad8-226">You can run and debug into all the preview 1 samples shown below by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/NewInEFCore6).</span></span>

### <a name="savepoints-api"></a><span data-ttu-id="45ad8-227">儲存點 API</span><span class="sxs-lookup"><span data-stu-id="45ad8-227">Savepoints API</span></span>

<span data-ttu-id="45ad8-228">GitHub 問題： [#20228](https://github.com/dotnet/efcore/issues/20228)。</span><span class="sxs-lookup"><span data-stu-id="45ad8-228">GitHub Issue: [#20228](https://github.com/dotnet/efcore/issues/20228).</span></span>

<span data-ttu-id="45ad8-229">我們已在 [ADO.NET 提供者中對儲存點的通用 API 進行](https://github.com/dotnet/runtime/issues/33397)標準化。</span><span class="sxs-lookup"><span data-stu-id="45ad8-229">We have been standardizing on [a common API for savepoints in ADO.NET providers](https://github.com/dotnet/runtime/issues/33397).</span></span> <span data-ttu-id="45ad8-230">Microsoft Data Sqlite 現在支援此 API，包括：</span><span class="sxs-lookup"><span data-stu-id="45ad8-230">Microsoft.Data.Sqlite now supports this API, including:</span></span>

- <span data-ttu-id="45ad8-231"><xref:System.Data.Common.DbTransaction.Save(System.String)> 在交易中建立儲存點</span><span class="sxs-lookup"><span data-stu-id="45ad8-231"><xref:System.Data.Common.DbTransaction.Save(System.String)> to create a savepoint in the transaction</span></span>
- <span data-ttu-id="45ad8-232"><xref:System.Data.Common.DbTransaction.Rollback(System.String)> 復原至上一個儲存點</span><span class="sxs-lookup"><span data-stu-id="45ad8-232"><xref:System.Data.Common.DbTransaction.Rollback(System.String)> to roll back to a previous savepoint</span></span>
- <span data-ttu-id="45ad8-233"><xref:System.Data.Common.DbTransaction.Release(System.String)> 釋放儲存點</span><span class="sxs-lookup"><span data-stu-id="45ad8-233"><xref:System.Data.Common.DbTransaction.Release(System.String)> to release a savepoint</span></span>

<span data-ttu-id="45ad8-234">使用儲存點可讓交易的一部分回復，而不會回復整個交易。</span><span class="sxs-lookup"><span data-stu-id="45ad8-234">Using a savepoint allows part of a transaction to be rolled back without rolling back the entire transaction.</span></span> <span data-ttu-id="45ad8-235">例如，下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="45ad8-235">For example, the code below:</span></span>

- <span data-ttu-id="45ad8-236">建立交易</span><span class="sxs-lookup"><span data-stu-id="45ad8-236">Creates a transaction</span></span>
- <span data-ttu-id="45ad8-237">將更新傳送至資料庫</span><span class="sxs-lookup"><span data-stu-id="45ad8-237">Sends an update to the database</span></span>
- <span data-ttu-id="45ad8-238">建立儲存點</span><span class="sxs-lookup"><span data-stu-id="45ad8-238">Creates a savepoint</span></span>
- <span data-ttu-id="45ad8-239">將其他更新傳送至資料庫</span><span class="sxs-lookup"><span data-stu-id="45ad8-239">Sends another update to the database</span></span>
- <span data-ttu-id="45ad8-240">復原至先前建立的儲存點</span><span class="sxs-lookup"><span data-stu-id="45ad8-240">Rolls back to the savepoint previous created</span></span>
- <span data-ttu-id="45ad8-241">認可交易</span><span class="sxs-lookup"><span data-stu-id="45ad8-241">Commits the transaction</span></span>

<!--
        using var connection = new SqliteConnection("DataSource=test.db");
        connection.Open();

        using var transaction = connection.BeginTransaction();

        using (var command = connection.CreateCommand())
        {
            command.CommandText = @"UPDATE Users SET Username = 'ajcvickers' WHERE Id = 1";
            command.ExecuteNonQuery();
        }

        transaction.Save("MySavepoint");

        using (var command = connection.CreateCommand())
        {
            command.CommandText = @"UPDATE Users SET Username = 'wfvickers' WHERE Id = 2";
            command.ExecuteNonQuery();
        }

        transaction.Rollback("MySavepoint");

        transaction.Commit();
-->
[!code-csharp[PerformUpdates](../../../../samples/core/Miscellaneous/NewInEFCore6/SqliteSamples.cs?name=PerformUpdates)]

<span data-ttu-id="45ad8-242">這會導致第一個更新認可至資料庫，而第二個更新則不會認可，因為儲存點是在認可交易之前復原。</span><span class="sxs-lookup"><span data-stu-id="45ad8-242">This will result in the first update being committed to the database, while the second update is not committed since the savepoint was rolled back before committing the transaction.</span></span>

### <a name="command-timeout-in-the-connection-string"></a><span data-ttu-id="45ad8-243">連接字串中的命令逾時</span><span class="sxs-lookup"><span data-stu-id="45ad8-243">Command timeout in the connection string</span></span>

<span data-ttu-id="45ad8-244">GitHub 問題： [#22505](https://github.com/dotnet/efcore/issues/22505)。</span><span class="sxs-lookup"><span data-stu-id="45ad8-244">GitHub Issue: [#22505](https://github.com/dotnet/efcore/issues/22505).</span></span> <span data-ttu-id="45ad8-245">這項功能是由所貢獻 [@nmichels](https://github.com/nmichels) 。</span><span class="sxs-lookup"><span data-stu-id="45ad8-245">This feature was contributed by [@nmichels](https://github.com/nmichels).</span></span>

<span data-ttu-id="45ad8-246">ADO.NET 提供者支援兩種不同的超時：</span><span class="sxs-lookup"><span data-stu-id="45ad8-246">ADO.NET providers support two distinct timeouts:</span></span>

- <span data-ttu-id="45ad8-247">連接逾時值，決定建立資料庫連接時等待的最長時間。</span><span class="sxs-lookup"><span data-stu-id="45ad8-247">The connection timeout, which determines the maximum time to wait when making a connection to the database.</span></span>
- <span data-ttu-id="45ad8-248">命令逾時，決定等候命令完成執行的最長時間。</span><span class="sxs-lookup"><span data-stu-id="45ad8-248">The command timeout, which determines the maximum time to wait for a command to complete executing.</span></span>

<span data-ttu-id="45ad8-249">您可以使用程式碼設定命令逾時 <xref:System.Data.Common.DbCommand.CommandTimeout?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="45ad8-249">The command timeout can be set from code using <xref:System.Data.Common.DbCommand.CommandTimeout?displayProperty=nameWithType>.</span></span> <span data-ttu-id="45ad8-250">許多提供者現在也會在連接字串中公開此命令逾時。</span><span class="sxs-lookup"><span data-stu-id="45ad8-250">Many providers are now also exposing this command timeout in the connection string.</span></span> <span data-ttu-id="45ad8-251">請使用 `Command Timeout` 連接字串關鍵字來遵循此趨勢。</span><span class="sxs-lookup"><span data-stu-id="45ad8-251">Microsoft.Data.Sqlite is following this trend with the `Command Timeout` connection string keyword.</span></span> <span data-ttu-id="45ad8-252">例如， `"Command Timeout=60;DataSource=test.db"` 會使用60秒作為連接所建立命令的預設超時。</span><span class="sxs-lookup"><span data-stu-id="45ad8-252">For example, `"Command Timeout=60;DataSource=test.db"` will use 60 seconds as the default timeout for commands created by the connection.</span></span>

> [!TIP]
> <span data-ttu-id="45ad8-253">Sqlite `Default Timeout` 會視為的同義字 `Command Timeout` ，因此可以改為使用（如果偏好的話）。</span><span class="sxs-lookup"><span data-stu-id="45ad8-253">Sqlite treats `Default Timeout` as a synonym for `Command Timeout` and so can be used instead if preferred.</span></span>
