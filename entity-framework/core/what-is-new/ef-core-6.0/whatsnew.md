---
title: EF Core 6.0 的新功能
description: EF Core 6.0 的新功能總覽
author: ajcvickers
ms.date: 01/28/2021
uid: core/what-is-new/ef-core-6.0/whatsnew
ms.openlocfilehash: bcc2b3ce9047a2c6b5a89e99b96919914bcf42fe
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543194"
---
# <a name="whats-new-in-ef-core-60"></a><span data-ttu-id="bccd5-103">EF Core 6.0 的新功能</span><span class="sxs-lookup"><span data-stu-id="bccd5-103">What's New in EF Core 6.0</span></span>

<span data-ttu-id="bccd5-104">EF Core 6.0 目前正在開發中。</span><span class="sxs-lookup"><span data-stu-id="bccd5-104">EF Core 6.0 is currently in development.</span></span> <span data-ttu-id="bccd5-105">這包含每個預覽中所引進之有趣變更的總覽。</span><span class="sxs-lookup"><span data-stu-id="bccd5-105">This contains an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="bccd5-106">此頁面不會複製 [EF Core 6.0 的方案](xref:core/what-is-new/ef-core-6.0/plan)。</span><span class="sxs-lookup"><span data-stu-id="bccd5-106">This page does not duplicate the [plan for EF Core 6.0](xref:core/what-is-new/ef-core-6.0/plan).</span></span> <span data-ttu-id="bccd5-107">此計畫描述 EF Core 6.0 的整體主題，包括我們在出貨最終發行版本之前打算包含的所有專案。</span><span class="sxs-lookup"><span data-stu-id="bccd5-107">The plan describes the overall themes for EF Core 6.0, including everything we are planning to include before shipping the final release.</span></span>

## <a name="ef-core-60-preview-1"></a><span data-ttu-id="bccd5-108">EF Core 6.0 Preview 1</span><span class="sxs-lookup"><span data-stu-id="bccd5-108">EF Core 6.0 Preview 1</span></span>

> [!TIP]
> <span data-ttu-id="bccd5-109">您可以 [從 GitHub 下載範例程式碼](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/NewInEFCore6)，以執行並偵測到下列顯示的所有 preview 1 範例。</span><span class="sxs-lookup"><span data-stu-id="bccd5-109">You can run and debug into all the preview 1 samples shown below by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/NewInEFCore6).</span></span>

### <a name="unicodeattribute"></a><span data-ttu-id="bccd5-110">UnicodeAttribute</span><span class="sxs-lookup"><span data-stu-id="bccd5-110">UnicodeAttribute</span></span>

<span data-ttu-id="bccd5-111">GitHub 問題： [#19794](https://github.com/dotnet/efcore/issues/19794)。</span><span class="sxs-lookup"><span data-stu-id="bccd5-111">GitHub Issue: [#19794](https://github.com/dotnet/efcore/issues/19794).</span></span> <span data-ttu-id="bccd5-112">這項功能是由所貢獻 [@RaymondHuy](https://github.com/RaymondHuy) 。</span><span class="sxs-lookup"><span data-stu-id="bccd5-112">This feature was contributed by [@RaymondHuy](https://github.com/RaymondHuy).</span></span>

<span data-ttu-id="bccd5-113">從 EF Core 6.0 開始，您現在可以使用對應屬性，將字串屬性對應到非 Unicode 資料行， _而不需要直接指定資料庫類型_。</span><span class="sxs-lookup"><span data-stu-id="bccd5-113">Starting with EF Core 6.0, a string property can now be mapped to a non-Unicode column using a mapping attribute _without specifying the database type directly_.</span></span> <span data-ttu-id="bccd5-114">例如，假設有一個 `Book` 實體類型具有 [國際標準書籍編號的屬性， (isbn) ](https://en.wikipedia.org/wiki/International_Standard_Book_Number) 格式為 "isbn 978-3-16-148410-0"：</span><span class="sxs-lookup"><span data-stu-id="bccd5-114">For example, consider a `Book` entity type with a property for the [International Standard Book Number (ISBN)](https://en.wikipedia.org/wiki/International_Standard_Book_Number) in the form "ISBN 978-3-16-148410-0":</span></span>

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

<span data-ttu-id="bccd5-115">由於 ISBNs 不能包含任何非 unicode 字元，因此 `Unicode` 屬性會導致使用非 unicode 字串類型。</span><span class="sxs-lookup"><span data-stu-id="bccd5-115">Since ISBNs cannot contain any non-unicode characters, the `Unicode` attribute will cause a non-Unicode string type to be used.</span></span> <span data-ttu-id="bccd5-116">此外， `MaxLength` 也會用來限制資料庫資料行的大小。</span><span class="sxs-lookup"><span data-stu-id="bccd5-116">In addition, `MaxLength` is used to limit the size of the database column.</span></span> <span data-ttu-id="bccd5-117">例如，使用 SQL Server 時，會產生下列的資料庫資料行 `varchar(22)` ：</span><span class="sxs-lookup"><span data-stu-id="bccd5-117">For example, when using SQL Server, this results in a database column of `varchar(22)`:</span></span>

```sql
CREATE TABLE [Book] (
    [Id] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Isbn] varchar(22) NULL,
    CONSTRAINT [PK_Book] PRIMARY KEY ([Id]));
```

> [!NOTE]
> <span data-ttu-id="bccd5-118">EF Core 預設會將字串屬性對應至 Unicode 資料行。</span><span class="sxs-lookup"><span data-stu-id="bccd5-118">EF Core maps string properties to Unicode columns by default.</span></span> <span data-ttu-id="bccd5-119">`UnicodeAttribute` 當資料庫系統只支援 Unicode 類型時，會忽略。</span><span class="sxs-lookup"><span data-stu-id="bccd5-119">`UnicodeAttribute` is ignored when the database system supports only Unicode types.</span></span>

### <a name="precisionattribute"></a><span data-ttu-id="bccd5-120">PrecisionAttribute</span><span class="sxs-lookup"><span data-stu-id="bccd5-120">PrecisionAttribute</span></span>

<span data-ttu-id="bccd5-121">GitHub 問題： [#17914](https://github.com/dotnet/efcore/issues/17914)。</span><span class="sxs-lookup"><span data-stu-id="bccd5-121">GitHub Issue: [#17914](https://github.com/dotnet/efcore/issues/17914).</span></span> <span data-ttu-id="bccd5-122">這項功能是由所貢獻 [@RaymondHuy](https://github.com/RaymondHuy) 。</span><span class="sxs-lookup"><span data-stu-id="bccd5-122">This feature was contributed by [@RaymondHuy](https://github.com/RaymondHuy).</span></span>

<span data-ttu-id="bccd5-123">您現在可以使用對應屬性來設定資料庫資料行的有效位數和小數位數， _而不需要直接指定資料庫類型_。</span><span class="sxs-lookup"><span data-stu-id="bccd5-123">The precision and scale of a database column can now be configured using mapping attributes _without specifying the database type directly_.</span></span> <span data-ttu-id="bccd5-124">例如，假設有一個 `Product` 具有 decimal 屬性的實體類型 `Price` ：</span><span class="sxs-lookup"><span data-stu-id="bccd5-124">For example, consider a `Product` entity type with a decimal `Price` property:</span></span>

<!--
    public class Product
    {
        public int Id { get; set; }

        [Precision(precision: 10, scale: 2)]
        public decimal Price { get; set; }
    }
-->
[!code-csharp[ProductEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/PrecisionAttributeSample.cs?name=ProductEntityType)]

<span data-ttu-id="bccd5-125">EF Core 會將此屬性對應至精確度為10且小數位數為2的資料庫資料行。</span><span class="sxs-lookup"><span data-stu-id="bccd5-125">EF Core will map this property to a database column with precision 10 and scale 2.</span></span> <span data-ttu-id="bccd5-126">例如，在 SQL Server：</span><span class="sxs-lookup"><span data-stu-id="bccd5-126">For example, on SQL Server:</span></span>

```sql
CREATE TABLE [Product] (
    [Id] int NOT NULL IDENTITY,
    [Price] decimal(10,2) NOT NULL,
    CONSTRAINT [PK_Product] PRIMARY KEY ([Id]));
```

### <a name="entitytypeconfigurationattribute"></a><span data-ttu-id="bccd5-127">EntityTypeConfigurationAttribute</span><span class="sxs-lookup"><span data-stu-id="bccd5-127">EntityTypeConfigurationAttribute</span></span>

<span data-ttu-id="bccd5-128">GitHub 問題： [#23163](https://github.com/dotnet/efcore/issues/23163)。</span><span class="sxs-lookup"><span data-stu-id="bccd5-128">GitHub Issue: [#23163](https://github.com/dotnet/efcore/issues/23163).</span></span> <span data-ttu-id="bccd5-129">這項功能是由所貢獻 [@KaloyanIT](https://github.com/KaloyanIT) 。</span><span class="sxs-lookup"><span data-stu-id="bccd5-129">This feature was contributed by [@KaloyanIT](https://github.com/KaloyanIT).</span></span>

<span data-ttu-id="bccd5-130"><xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601> 實例可讓 <xref:Microsoft.EntityFrameworkCore.ModelBuilder> 每個實體類型的設定都包含在自己的設定類別中。</span><span class="sxs-lookup"><span data-stu-id="bccd5-130"><xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601> instances allow <xref:Microsoft.EntityFrameworkCore.ModelBuilder> configuration for a each entity type to be contained in its own configuration class.</span></span> <span data-ttu-id="bccd5-131">例如：</span><span class="sxs-lookup"><span data-stu-id="bccd5-131">For example:</span></span>

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

<span data-ttu-id="bccd5-132">一般來說，此設定類別必須具現化，並從中呼叫 <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="bccd5-132">Normally, this configuration class must be instantiated and called into from <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="bccd5-133">例如：</span><span class="sxs-lookup"><span data-stu-id="bccd5-133">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    new BookConfiguration().Configure(modelBuilder.Entity<Book>());
}
```

<span data-ttu-id="bccd5-134">從 EF Core 6.0 開始， `EntityTypeConfigurationAttribute` 可以在實體類型上放置，讓 EF Core 可以尋找及使用適當的設定。</span><span class="sxs-lookup"><span data-stu-id="bccd5-134">Starting with EF Core 6.0, an `EntityTypeConfigurationAttribute` can be placed on the entity type such that EF Core can find and use appropriate configuration.</span></span> <span data-ttu-id="bccd5-135">例如：</span><span class="sxs-lookup"><span data-stu-id="bccd5-135">For example:</span></span>

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

<span data-ttu-id="bccd5-136">這個屬性工作表示 `IEntityTypeConfiguration` 當 `Book` 模型中包含實體類型時，EF Core 將會使用指定的實作為。</span><span class="sxs-lookup"><span data-stu-id="bccd5-136">This attribute means that EF Core will use the specified `IEntityTypeConfiguration` implementation whenever the `Book` entity type is included in a model.</span></span> <span data-ttu-id="bccd5-137">實體類型會包含在模型中，使用其中一個一般機制。</span><span class="sxs-lookup"><span data-stu-id="bccd5-137">The entity type is included in a model using one of the normal mechanisms.</span></span> <span data-ttu-id="bccd5-138">例如，藉由建立 <xref:Microsoft.EntityFrameworkCore.DbSet%601> 實體類型的屬性：</span><span class="sxs-lookup"><span data-stu-id="bccd5-138">For example, by creating a <xref:Microsoft.EntityFrameworkCore.DbSet%601> property for the entity type:</span></span>

<!--
public class BooksContext : DbContext
{
    public DbSet<Book> Books { get; set; }

    //...
-->
[!code-csharp[DbContext](../../../../samples/core/Miscellaneous/NewInEFCore6/EntityTypeConfigurationAttributeSample.cs?name=DbContext)]

<span data-ttu-id="bccd5-139">或者，在中註冊它 <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> ：</span><span class="sxs-lookup"><span data-stu-id="bccd5-139">Or by registering it in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Book>();
}
```

> [!NOTE]
> <span data-ttu-id="bccd5-140">`EntityTypeConfigurationAttribute` 元件中將不會自動探索類型。</span><span class="sxs-lookup"><span data-stu-id="bccd5-140">`EntityTypeConfigurationAttribute` types will not be automatically discovered in an assembly.</span></span> <span data-ttu-id="bccd5-141">實體類型必須加入至模型，然後才會在該實體類型上探索屬性。</span><span class="sxs-lookup"><span data-stu-id="bccd5-141">Entity types must be added to the model before the attribute will be discovered on that entity type.</span></span>

### <a name="translate-tostring-on-sqlite"></a><span data-ttu-id="bccd5-142">在 SQLite 上轉譯 ToString</span><span class="sxs-lookup"><span data-stu-id="bccd5-142">Translate ToString on SQLite</span></span>

<span data-ttu-id="bccd5-143">GitHub 問題： [#17223](https://github.com/dotnet/efcore/issues/17223)。</span><span class="sxs-lookup"><span data-stu-id="bccd5-143">GitHub Issue: [#17223](https://github.com/dotnet/efcore/issues/17223).</span></span> <span data-ttu-id="bccd5-144">這項功能是由所貢獻 [@ralmsdeveloper](https://github.com/ralmsdeveloper) 。</span><span class="sxs-lookup"><span data-stu-id="bccd5-144">This feature was contributed by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span>

<span data-ttu-id="bccd5-145"><xref:System.Object.ToString>使用 SQLite 資料庫提供者時，的呼叫現在會轉譯為 SQL。</span><span class="sxs-lookup"><span data-stu-id="bccd5-145">Calls to <xref:System.Object.ToString> are now translated to SQL when using the SQLite database provider.</span></span> <span data-ttu-id="bccd5-146">這適用于涉及非字串資料行的文字搜尋。</span><span class="sxs-lookup"><span data-stu-id="bccd5-146">This can be useful for text searches involving non-string columns.</span></span> <span data-ttu-id="bccd5-147">例如，假設將 `User` 電話號碼儲存為數值的實體類型：</span><span class="sxs-lookup"><span data-stu-id="bccd5-147">For example, consider a `User` entity type that stores phone numbers as numeric values:</span></span>

<!--
    public class User
    {
        public int Id { get; set; }
        public string Username { get; set; }
        public long PhoneNumber { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/ToStringTranslationSample.cs?name=UserEntityType)]

<span data-ttu-id="bccd5-148">`ToString` 可以用來將數位轉換成資料庫中的字串。</span><span class="sxs-lookup"><span data-stu-id="bccd5-148">`ToString` can be used to convert the number to a string in the database.</span></span> <span data-ttu-id="bccd5-149">接著，我們可以使用此字串搭配函式（例如） `LIKE` 來尋找符合模式的數位。</span><span class="sxs-lookup"><span data-stu-id="bccd5-149">We can then use this string with a function such as `LIKE` to find numbers that match a pattern.</span></span> <span data-ttu-id="bccd5-150">例如，若要尋找包含555的所有數位：</span><span class="sxs-lookup"><span data-stu-id="bccd5-150">For example, to find all numbers containing 555:</span></span>

<!--
var users = context.Users.Where(u => EF.Functions.Like(u.PhoneNumber.ToString(), "%555%")).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/ToStringTranslationSample.cs?name=Query)]

<span data-ttu-id="bccd5-151">使用 SQLite 資料庫時，這會轉譯成下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="bccd5-151">This translates to the following SQL when using a SQLite database:</span></span>

```sql
SELECT COUNT(*)
FROM "Users" AS "u"
WHERE CAST("u"."PhoneNumber" AS TEXT) LIKE '%555%'
```

<span data-ttu-id="bccd5-152">請注意， <xref:System.Object.ToString> EF Core 5.0 中已支援 SQL Server 的翻譯，其他資料庫提供者也可能支援此功能。</span><span class="sxs-lookup"><span data-stu-id="bccd5-152">Note that translation of <xref:System.Object.ToString> for SQL Server is already supported in EF Core 5.0, and may also be supported by other database providers.</span></span>

### <a name="effunctionsrandom"></a><span data-ttu-id="bccd5-153">英 孚。函數。 Random</span><span class="sxs-lookup"><span data-stu-id="bccd5-153">EF.Functions.Random</span></span>

<span data-ttu-id="bccd5-154">GitHub 問題： [#16141](https://github.com/dotnet/efcore/issues/16141)。</span><span class="sxs-lookup"><span data-stu-id="bccd5-154">GitHub Issue: [#16141](https://github.com/dotnet/efcore/issues/16141).</span></span> <span data-ttu-id="bccd5-155">這項功能是由所貢獻 [@RaymondHuy](https://github.com/RaymondHuy) 。</span><span class="sxs-lookup"><span data-stu-id="bccd5-155">This feature was contributed by [@RaymondHuy](https://github.com/RaymondHuy).</span></span>

<span data-ttu-id="bccd5-156">`EF.Functions.Random` 對應至資料庫函式，其會傳回介於0和1之間的虛擬亂數。</span><span class="sxs-lookup"><span data-stu-id="bccd5-156">`EF.Functions.Random` maps to a database function returning a pseudo-random number between 0 and 1 exclusive.</span></span> <span data-ttu-id="bccd5-157">SQL Server、SQLite 和 Cosmos 的 EF Core 存放庫中已實行翻譯。</span><span class="sxs-lookup"><span data-stu-id="bccd5-157">Translations have been implemented in the EF Core repo for SQL Server, SQLite, and Cosmos.</span></span> <span data-ttu-id="bccd5-158">例如，假設有一個 `User` 具有屬性的實體類型 `Popularity` ：</span><span class="sxs-lookup"><span data-stu-id="bccd5-158">For example, consider a `User` entity type with a `Popularity` property:</span></span>

<!--
    public class User
    {
        public int Id { get; set; }
        public string Username { get; set; }
        public int Popularity { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/RandomFunctionSample.cs?name=UserEntityType)]

<span data-ttu-id="bccd5-159">`Popularity` 可以有1到5（含）的值。</span><span class="sxs-lookup"><span data-stu-id="bccd5-159">`Popularity` can have values from 1 to 5 inclusive.</span></span> <span data-ttu-id="bccd5-160">使用 `EF.Functions.Random` 我們可以撰寫查詢，以傳回具有隨機播放熱門程度的所有使用者：</span><span class="sxs-lookup"><span data-stu-id="bccd5-160">Using `EF.Functions.Random` we can write a query to return all users with a randomly chosen popularity:</span></span>

<!--
var users = context.Users.Where(u => u.Popularity == (int)(EF.Functions.Random() * 5.0) + 1).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/RandomFunctionSample.cs?name=Query)]

<span data-ttu-id="bccd5-161">使用 SQL Server 資料庫時，這會轉譯成下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="bccd5-161">This translates to the following SQL when using a SQL Server database:</span></span>

```sql
SELECT [u].[Id], [u].[Popularity], [u].[Username]
FROM [Users] AS [u]
WHERE [u].[Popularity] = (CAST((RAND() * 5.0E0) AS int) + 1)
```

### <a name="support-for-sql-server-sparse-columns"></a><span data-ttu-id="bccd5-162">支援 SQL Server 的稀疏資料行</span><span class="sxs-lookup"><span data-stu-id="bccd5-162">Support for SQL Server sparse columns</span></span>

<span data-ttu-id="bccd5-163">GitHub 問題： [#8023](https://github.com/dotnet/efcore/issues/8023)。</span><span class="sxs-lookup"><span data-stu-id="bccd5-163">GitHub Issue: [#8023](https://github.com/dotnet/efcore/issues/8023).</span></span>

<span data-ttu-id="bccd5-164">SQL Server [稀疏資料行](/sql/relational-databases/tables/use-sparse-columns) 是經過優化以儲存 null 值的一般資料行。</span><span class="sxs-lookup"><span data-stu-id="bccd5-164">SQL Server [sparse columns](/sql/relational-databases/tables/use-sparse-columns) are ordinary columns that are optimized to store null values.</span></span> <span data-ttu-id="bccd5-165">這在使用 [TPH 繼承對應](xref:core/modeling/inheritance) 時很有用，因為很少使用的子類型屬性會導致資料表中大部分資料列的 null 資料行值。</span><span class="sxs-lookup"><span data-stu-id="bccd5-165">This can be useful when using [TPH inheritance mapping](xref:core/modeling/inheritance) where properties of a rarely used subtype will result in null column values for most rows in the table.</span></span> <span data-ttu-id="bccd5-166">例如，假設有一個 `ForumModerator` 延伸自的類別 `ForumUser` ：</span><span class="sxs-lookup"><span data-stu-id="bccd5-166">For example, consider a `ForumModerator` class that extends from `ForumUser`:</span></span>

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

<span data-ttu-id="bccd5-167">可能有數百萬個使用者，只有少數的使用者是仲裁者。</span><span class="sxs-lookup"><span data-stu-id="bccd5-167">There may be millions of users, with only a handful of these being moderators.</span></span> <span data-ttu-id="bccd5-168">這表示在這裡將對應 `ForumName` 為稀疏可能有意義。</span><span class="sxs-lookup"><span data-stu-id="bccd5-168">This means mapping the `ForumName` as sparse might make sense here.</span></span> <span data-ttu-id="bccd5-169">這現在可以使用 `IsSparse` 中的來設定 <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> 。</span><span class="sxs-lookup"><span data-stu-id="bccd5-169">This can now be configured using `IsSparse` in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="bccd5-170">例如：</span><span class="sxs-lookup"><span data-stu-id="bccd5-170">For example:</span></span>

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

<span data-ttu-id="bccd5-171">EF Core 的遷移會將資料行標示為「稀疏」。</span><span class="sxs-lookup"><span data-stu-id="bccd5-171">EF Core migrations will then mark the column as sparse.</span></span> <span data-ttu-id="bccd5-172">例如：</span><span class="sxs-lookup"><span data-stu-id="bccd5-172">For example:</span></span>

```sql
CREATE TABLE [ForumUser] (
    [Id] int NOT NULL IDENTITY,
    [Username] nvarchar(max) NULL,
    [Discriminator] nvarchar(max) NOT NULL,
    [ForumName] nvarchar(max) SPARSE NULL,
    CONSTRAINT [PK_ForumUser] PRIMARY KEY ([Id]));
```

> [!NOTE]
> <span data-ttu-id="bccd5-173">稀疏資料行有限制。</span><span class="sxs-lookup"><span data-stu-id="bccd5-173">Sparse columns have limitations.</span></span> <span data-ttu-id="bccd5-174">請務必閱讀 SQL Server 的 [稀疏資料行檔](/sql/relational-databases/tables/use-sparse-columns) ，以確保稀疏資料行是您案例的正確選擇。</span><span class="sxs-lookup"><span data-stu-id="bccd5-174">Make sure to read the [SQL Server sparse columns documentation](/sql/relational-databases/tables/use-sparse-columns) to ensure that sparse columns are the right choice for your scenario.</span></span>

### <a name="in-memory-database-validate-required-properties-are-not-null"></a><span data-ttu-id="bccd5-175">記憶體內部資料庫：驗證必要的屬性不是 null</span><span class="sxs-lookup"><span data-stu-id="bccd5-175">In-memory database: validate required properties are not null</span></span>

<span data-ttu-id="bccd5-176">GitHub 問題： [#10613](https://github.com/dotnet/efcore/issues/10613)。</span><span class="sxs-lookup"><span data-stu-id="bccd5-176">GitHub Issue: [#10613](https://github.com/dotnet/efcore/issues/10613).</span></span> <span data-ttu-id="bccd5-177">這項功能是由所貢獻 [@fagnercarvalho](https://github.com/fagnercarvalho) 。</span><span class="sxs-lookup"><span data-stu-id="bccd5-177">This feature was contributed by [@fagnercarvalho](https://github.com/fagnercarvalho).</span></span>

<span data-ttu-id="bccd5-178">如果嘗試針對標示為必要的屬性儲存 null 值，則記憶體內部資料庫的 EF Core 將會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="bccd5-178">The EF Core in-memory database will now throw an exception if an attempt is made to save a null value for a property marked as required.</span></span> <span data-ttu-id="bccd5-179">例如，假設有一個 `User` 具有必要屬性的型別 `Username` ：</span><span class="sxs-lookup"><span data-stu-id="bccd5-179">For example, consider a `User` type with a required `Username` property:</span></span>

<!--
    public class User
    {
        public int Id { get; set; }

        [Required]
        public string Username { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/InMemoryRequiredPropertiesSample.cs?name=UserEntityType)]

<span data-ttu-id="bccd5-180">嘗試以 null 儲存實體 `Username` 將會導致下列例外狀況：</span><span class="sxs-lookup"><span data-stu-id="bccd5-180">Attempting to save an entity with a null `Username` will result in the following exception:</span></span>

> <span data-ttu-id="bccd5-181">Microsoft.entityframeworkcore. 就 dbupdateexception：具有索引鍵值 ' {Id： 1} ' 的實體類型 ' User ' 實例缺少必要屬性 ' {' Username '} '。</span><span class="sxs-lookup"><span data-stu-id="bccd5-181">Microsoft.EntityFrameworkCore.DbUpdateException: Required properties '{'Username'}' are missing for the instance of entity type 'User' with the key value '{Id: 1}'.</span></span>

<span data-ttu-id="bccd5-182">如有必要，可以停用此驗證。</span><span class="sxs-lookup"><span data-stu-id="bccd5-182">This validation can be disabled if necessary.</span></span> <span data-ttu-id="bccd5-183">例如：</span><span class="sxs-lookup"><span data-stu-id="bccd5-183">For example:</span></span>

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

### <a name="improved-sql-server-translation-for-isnullorwhitespace"></a><span data-ttu-id="bccd5-184">改進 IsNullOrWhitespace SQL Server 轉譯</span><span class="sxs-lookup"><span data-stu-id="bccd5-184">Improved SQL Server translation for IsNullOrWhitespace</span></span>

<span data-ttu-id="bccd5-185">GitHub 問題： [#22916](https://github.com/dotnet/efcore/issues/22916)。</span><span class="sxs-lookup"><span data-stu-id="bccd5-185">GitHub Issue: [#22916](https://github.com/dotnet/efcore/issues/22916).</span></span> <span data-ttu-id="bccd5-186">這項功能是由所貢獻 [@Marusyk](https://github.com/Marusyk) 。</span><span class="sxs-lookup"><span data-stu-id="bccd5-186">This feature was contributed by [@Marusyk](https://github.com/Marusyk).</span></span>

<span data-ttu-id="bccd5-187">請考慮下列查詢：</span><span class="sxs-lookup"><span data-stu-id="bccd5-187">Consider the following query:</span></span>

<!--
        var users = context.Users.Where(
            e => string.IsNullOrWhiteSpace(e.FirstName)
                 || string.IsNullOrWhiteSpace(e.LastName)).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/IsNullOrWhitespaceSample.cs?name=Query)]

<span data-ttu-id="bccd5-188">在 EF Core 6.0 之前，這會在 SQL Server 上轉譯為下列內容：</span><span class="sxs-lookup"><span data-stu-id="bccd5-188">Before EF Core 6.0, this was translated to the following on SQL Server:</span></span>

```sql
SELECT [u].[Id], [u].[FirstName], [u].[LastName]
FROM [Users] AS [u]
WHERE ([u].[FirstName] IS NULL OR (LTRIM(RTRIM([u].[FirstName])) = N'')) OR ([u].[LastName] IS NULL OR (LTRIM(RTRIM([u].[LastName])) = N''))
```

<span data-ttu-id="bccd5-189">這種翻譯已針對 EF Core 6.0 改進為：</span><span class="sxs-lookup"><span data-stu-id="bccd5-189">This translation has been improved for EF Core 6.0 to:</span></span>

```sql
SELECT [u].[Id], [u].[FirstName], [u].[LastName]
FROM [Users] AS [u]
WHERE ([u].[FirstName] IS NULL OR ([u].[FirstName] = N'')) OR ([u].[LastName] IS NULL OR ([u].[LastName] = N''))
```

### <a name="database-comments-are-scaffolded-to-code-comments"></a><span data-ttu-id="bccd5-190">資料庫批註 scaffold 至程式碼批註</span><span class="sxs-lookup"><span data-stu-id="bccd5-190">Database comments are scaffolded to code comments</span></span>

<span data-ttu-id="bccd5-191">GitHub 問題： [#19113](https://github.com/dotnet/efcore/issues/19113)。</span><span class="sxs-lookup"><span data-stu-id="bccd5-191">GitHub Issue: [#19113](https://github.com/dotnet/efcore/issues/19113).</span></span> <span data-ttu-id="bccd5-192">這項功能是由所貢獻 [@ErikEJ](https://github.com/ErikEJ) 。</span><span class="sxs-lookup"><span data-stu-id="bccd5-192">This feature was contributed by [@ErikEJ](https://github.com/ErikEJ).</span></span>

<span data-ttu-id="bccd5-193">SQL 資料表和資料行上的批註現在 scaffold 至從現有 SQL Server 資料庫 [對 EF Core 模型進行反向工程](xref:core/managing-schemas/scaffolding) 時所建立的實體類型。</span><span class="sxs-lookup"><span data-stu-id="bccd5-193">Comments on SQL tables and columns are now scaffolded into the entity types created when [reverse-engineering an EF Core model](xref:core/managing-schemas/scaffolding) from an existing SQL Server database.</span></span> <span data-ttu-id="bccd5-194">例如：</span><span class="sxs-lookup"><span data-stu-id="bccd5-194">For example:</span></span>

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

## <a name="microsoftdatasqlite-60-preview-1"></a><span data-ttu-id="bccd5-195">Microsoft. Sqlite 6.0 Preview 1</span><span class="sxs-lookup"><span data-stu-id="bccd5-195">Microsoft.Data.Sqlite 6.0 Preview 1</span></span>

> [!TIP]
> <span data-ttu-id="bccd5-196">您可以 [從 GitHub 下載範例程式碼](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/NewInEFCore6)，以執行並偵測到下列顯示的所有 preview 1 範例。</span><span class="sxs-lookup"><span data-stu-id="bccd5-196">You can run and debug into all the preview 1 samples shown below by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/NewInEFCore6).</span></span>

### <a name="savepoints-api"></a><span data-ttu-id="bccd5-197">儲存點 API</span><span class="sxs-lookup"><span data-stu-id="bccd5-197">Savepoints API</span></span>

<span data-ttu-id="bccd5-198">GitHub 問題： [#20228](https://github.com/dotnet/efcore/issues/20228)。</span><span class="sxs-lookup"><span data-stu-id="bccd5-198">GitHub Issue: [#20228](https://github.com/dotnet/efcore/issues/20228).</span></span>

<span data-ttu-id="bccd5-199">我們已在 [ADO.NET 提供者中對儲存點的通用 API 進行](https://github.com/dotnet/runtime/issues/33397)標準化。</span><span class="sxs-lookup"><span data-stu-id="bccd5-199">We have been standardizing on [a common API for savepoints in ADO.NET providers](https://github.com/dotnet/runtime/issues/33397).</span></span> <span data-ttu-id="bccd5-200">Microsoft Data Sqlite 現在支援此 API，包括：</span><span class="sxs-lookup"><span data-stu-id="bccd5-200">Microsoft.Data.Sqlite now supports this API, including:</span></span>

- <span data-ttu-id="bccd5-201"><xref:System.Data.Common.DbTransaction.Save(System.String)> 在交易中建立儲存點</span><span class="sxs-lookup"><span data-stu-id="bccd5-201"><xref:System.Data.Common.DbTransaction.Save(System.String)> to create a savepoint in the transaction</span></span>
- <span data-ttu-id="bccd5-202"><xref:System.Data.Common.DbTransaction.Rollback(System.String)> 復原至上一個儲存點</span><span class="sxs-lookup"><span data-stu-id="bccd5-202"><xref:System.Data.Common.DbTransaction.Rollback(System.String)> to roll back to a previous savepoint</span></span>
- <span data-ttu-id="bccd5-203"><xref:System.Data.Common.DbTransaction.Release(System.String)> 釋放儲存點</span><span class="sxs-lookup"><span data-stu-id="bccd5-203"><xref:System.Data.Common.DbTransaction.Release(System.String)> to release a savepoint</span></span>

<span data-ttu-id="bccd5-204">使用儲存點可讓交易的一部分回復，而不會回復整個交易。</span><span class="sxs-lookup"><span data-stu-id="bccd5-204">Using a savepoint allows part of a transaction to be rolled back without rolling back the entire transaction.</span></span> <span data-ttu-id="bccd5-205">例如，下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="bccd5-205">For example, the code below:</span></span>

- <span data-ttu-id="bccd5-206">建立交易</span><span class="sxs-lookup"><span data-stu-id="bccd5-206">Creates a transaction</span></span>
- <span data-ttu-id="bccd5-207">將更新傳送至資料庫</span><span class="sxs-lookup"><span data-stu-id="bccd5-207">Sends an update to the database</span></span>
- <span data-ttu-id="bccd5-208">建立儲存點</span><span class="sxs-lookup"><span data-stu-id="bccd5-208">Creates a savepoint</span></span>
- <span data-ttu-id="bccd5-209">將其他更新傳送至資料庫</span><span class="sxs-lookup"><span data-stu-id="bccd5-209">Sends another update to the database</span></span>
- <span data-ttu-id="bccd5-210">復原至先前建立的儲存點</span><span class="sxs-lookup"><span data-stu-id="bccd5-210">Rolls back to the savepoint previous created</span></span>
- <span data-ttu-id="bccd5-211">認可交易</span><span class="sxs-lookup"><span data-stu-id="bccd5-211">Commits the transaction</span></span>

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

<span data-ttu-id="bccd5-212">這會導致第一個更新認可至資料庫，而第二個更新則不會認可，因為儲存點是在認可交易之前復原。</span><span class="sxs-lookup"><span data-stu-id="bccd5-212">This will result in the first update being committed to the database, while the second update is not committed since the savepoint was rolled back before committing the transaction.</span></span>

### <a name="command-timeout-in-the-connection-string"></a><span data-ttu-id="bccd5-213">連接字串中的命令逾時</span><span class="sxs-lookup"><span data-stu-id="bccd5-213">Command timeout in the connection string</span></span>

<span data-ttu-id="bccd5-214">GitHub 問題： [#22505](https://github.com/dotnet/efcore/issues/22505)。</span><span class="sxs-lookup"><span data-stu-id="bccd5-214">GitHub Issue: [#22505](https://github.com/dotnet/efcore/issues/22505).</span></span> <span data-ttu-id="bccd5-215">這項功能是由所貢獻 [@nmichels](https://github.com/nmichels) 。</span><span class="sxs-lookup"><span data-stu-id="bccd5-215">This feature was contributed by [@nmichels](https://github.com/nmichels).</span></span>

<span data-ttu-id="bccd5-216">ADO.NET 提供者支援兩種不同的超時：</span><span class="sxs-lookup"><span data-stu-id="bccd5-216">ADO.NET providers support two distinct timeouts:</span></span>

- <span data-ttu-id="bccd5-217">連接逾時值，決定建立資料庫連接時等待的最長時間。</span><span class="sxs-lookup"><span data-stu-id="bccd5-217">The connection timeout, which determines the maximum time to wait when making a connection to the database.</span></span>
- <span data-ttu-id="bccd5-218">命令逾時，決定等候命令完成執行的最長時間。</span><span class="sxs-lookup"><span data-stu-id="bccd5-218">The command timeout, which determines the maximum time to wait for a command to complete executing.</span></span>

<span data-ttu-id="bccd5-219">您可以使用程式碼設定命令逾時 <xref:System.Data.Common.DbCommand.CommandTimeout?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="bccd5-219">The command timeout can be set from code using <xref:System.Data.Common.DbCommand.CommandTimeout?displayProperty=nameWithType>.</span></span> <span data-ttu-id="bccd5-220">許多提供者現在也會在連接字串中公開此命令逾時。</span><span class="sxs-lookup"><span data-stu-id="bccd5-220">Many providers are now also exposing this command timeout in the connection string.</span></span> <span data-ttu-id="bccd5-221">請使用 `Command Timeout` 連接字串關鍵字來遵循此趨勢。</span><span class="sxs-lookup"><span data-stu-id="bccd5-221">Microsoft.Data.Sqlite is following this trend with the `Command Timeout` connection string keyword.</span></span> <span data-ttu-id="bccd5-222">例如， `"Command Timeout=60;DataSource=test.db"` 會使用60秒作為連接所建立命令的預設超時。</span><span class="sxs-lookup"><span data-stu-id="bccd5-222">For example, `"Command Timeout=60;DataSource=test.db"` will use 60 seconds as the default timeout for commands created by the connection.</span></span>

> [!TIP]
> <span data-ttu-id="bccd5-223">Sqlite `Default Timeout` 會視為的同義字 `Command Timeout` ，因此可以改為使用（如果偏好的話）。</span><span class="sxs-lookup"><span data-stu-id="bccd5-223">Sqlite treats `Default Timeout` as a synonym for `Command Timeout` and so can be used instead if preferred.</span></span>
