---
title: EF 核心 5.0 中的新增功能
description: EF Core 5.0 中新功能概述
author: ajcvickers
ms.date: 03/30/2020
uid: core/what-is-new/ef-core-5.0/whatsnew.md
ms.openlocfilehash: c047a308cadf44eea577dcab29b68b36942a50df
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80634273"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="6b174-103">EF 核心 5.0 中的新增功能</span><span class="sxs-lookup"><span data-stu-id="6b174-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="6b174-104">EF 核心 5.0 目前正在開發中。</span><span class="sxs-lookup"><span data-stu-id="6b174-104">EF Core 5.0 is currently in development.</span></span>
<span data-ttu-id="6b174-105">此頁將包含每個預覽中引入的有趣更改的概述。</span><span class="sxs-lookup"><span data-stu-id="6b174-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="6b174-106">此頁面不重複 EF [Core 5.0 的計劃](plan.md)。</span><span class="sxs-lookup"><span data-stu-id="6b174-106">This page does not duplicate the [plan for EF Core 5.0](plan.md).</span></span>
<span data-ttu-id="6b174-107">該計劃描述了 EF Core 5.0 的總體主題,包括我們在發佈最終版本之前計劃包括的所有內容。</span><span class="sxs-lookup"><span data-stu-id="6b174-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="6b174-108">我們將在此處添加連結到官方文檔,因為它被發佈。</span><span class="sxs-lookup"><span data-stu-id="6b174-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-2"></a><span data-ttu-id="6b174-109">Preview 2</span><span class="sxs-lookup"><span data-stu-id="6b174-109">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="6b174-110">使用 C# 屬性指定屬性支援欄位</span><span class="sxs-lookup"><span data-stu-id="6b174-110">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="6b174-111">C# 屬性現在可用於指定屬性的後備欄位。</span><span class="sxs-lookup"><span data-stu-id="6b174-111">A C# attribute can now be used to specify the backing field for a property.</span></span>
<span data-ttu-id="6b174-112">此屬性允許 EF Core 仍像通常發生的情況一樣寫入和讀取從背襯欄位,即使無法自動找到背襯欄位也是如此。</span><span class="sxs-lookup"><span data-stu-id="6b174-112">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span>
<span data-ttu-id="6b174-113">例如：</span><span class="sxs-lookup"><span data-stu-id="6b174-113">For example:</span></span>

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

<span data-ttu-id="6b174-114">文件按問題[#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230)追蹤。</span><span class="sxs-lookup"><span data-stu-id="6b174-114">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="6b174-115">完整的鑒別器對應</span><span class="sxs-lookup"><span data-stu-id="6b174-115">Complete discriminator mapping</span></span>

<span data-ttu-id="6b174-116">EF Core 使用區分列進行[繼承層次結構的 TPH 對應](/ef/core/modeling/inheritance)。</span><span class="sxs-lookup"><span data-stu-id="6b174-116">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span>
<span data-ttu-id="6b174-117">只要 EF Core 知道鑒別器的所有可能值,就可以進行某些性能增強。</span><span class="sxs-lookup"><span data-stu-id="6b174-117">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span>
<span data-ttu-id="6b174-118">EF Core 5.0 現在實現了這些增強功能。</span><span class="sxs-lookup"><span data-stu-id="6b174-118">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="6b174-119">例如,EF Core 的早期版本將始終為返回層次結構中所有類型的查詢生成此 SQL:</span><span class="sxs-lookup"><span data-stu-id="6b174-119">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="6b174-120">設定完整的鑒別器對應時,EF Core 5.0 現在將產生以下內容:</span><span class="sxs-lookup"><span data-stu-id="6b174-120">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="6b174-121">它將是從預覽 3 開始的默認行為。</span><span class="sxs-lookup"><span data-stu-id="6b174-121">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="6b174-122">微軟的效能改進.Data.Sqlite</span><span class="sxs-lookup"><span data-stu-id="6b174-122">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="6b174-123">我們對 SQLIte 進行了兩次性能改進:</span><span class="sxs-lookup"><span data-stu-id="6b174-123">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="6b174-124">使用 GetBytes、GetChars 和 GetTextReader 檢索二進位和字串資料現在透過使用 SqliteBlob 和流變得更加高效。</span><span class="sxs-lookup"><span data-stu-id="6b174-124">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="6b174-125">SqliteConnection 的初始化現在很懶。</span><span class="sxs-lookup"><span data-stu-id="6b174-125">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="6b174-126">這些改進ADO.NET微軟.Data.Sqlite 提供程式,因此也提高了 EF Core 以外的性能。</span><span class="sxs-lookup"><span data-stu-id="6b174-126">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="6b174-127">Preview 1</span><span class="sxs-lookup"><span data-stu-id="6b174-127">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="6b174-128">簡單記錄記錄</span><span class="sxs-lookup"><span data-stu-id="6b174-128">Simple logging</span></span>

<span data-ttu-id="6b174-129">此功能添加了類似於`Database.Log`EF6 的功能。</span><span class="sxs-lookup"><span data-stu-id="6b174-129">This feature adds functionality similar to `Database.Log` in EF6.</span></span>
<span data-ttu-id="6b174-130">也就是說,它提供了一種從 EF Core 獲取日誌的簡單方法,而無需配置任何類型的外部日誌記錄框架。</span><span class="sxs-lookup"><span data-stu-id="6b174-130">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="6b174-131">初步檔包含在[2019 年 12 月 5 日的 EF 每周狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)中。</span><span class="sxs-lookup"><span data-stu-id="6b174-131">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="6b174-132">其他文件按問題[#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085)追蹤。</span><span class="sxs-lookup"><span data-stu-id="6b174-132">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="6b174-133">取得產生的 SQL 的簡單方法</span><span class="sxs-lookup"><span data-stu-id="6b174-133">Simple way to get generated SQL</span></span>

<span data-ttu-id="6b174-134">EF Core 5.0`ToQueryString`引入了擴充方法,該方法將返回 EF Core 在執行 LINQ 查詢時將生成的 SQL。</span><span class="sxs-lookup"><span data-stu-id="6b174-134">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="6b174-135">初步檔包含在[2020 年 1 月 9 日的 EF 每周狀態](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)中。</span><span class="sxs-lookup"><span data-stu-id="6b174-135">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="6b174-136">其他文件按問題[#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331)跟蹤。</span><span class="sxs-lookup"><span data-stu-id="6b174-136">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="6b174-137">使用 C# 屬性指示實體沒有金鑰</span><span class="sxs-lookup"><span data-stu-id="6b174-137">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="6b174-138">實體類型現在可以配置為沒有使用`KeylessAttribute`new 的密鑰。</span><span class="sxs-lookup"><span data-stu-id="6b174-138">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span>
<span data-ttu-id="6b174-139">例如：</span><span class="sxs-lookup"><span data-stu-id="6b174-139">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="6b174-140">文件按問題[#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186)跟蹤。</span><span class="sxs-lookup"><span data-stu-id="6b174-140">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="6b174-141">初始化 DbContext 連線或連接字串</span><span class="sxs-lookup"><span data-stu-id="6b174-141">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="6b174-142">現在,創建沒有任何連接或連接字串的 DbContext 實例變得更加容易。</span><span class="sxs-lookup"><span data-stu-id="6b174-142">It is now easier to create a DbContext instance without any connection or connection string.</span></span>
<span data-ttu-id="6b174-143">此外,連接或連接字串現在可以在上下文實例上發生突變。</span><span class="sxs-lookup"><span data-stu-id="6b174-143">Also, the connection or connection string can now be mutated on the context instance.</span></span>
<span data-ttu-id="6b174-144">此功能允許同一上下文實例動態連接到不同的資料庫。</span><span class="sxs-lookup"><span data-stu-id="6b174-144">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="6b174-145">文檔按[問題#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075)跟蹤。</span><span class="sxs-lookup"><span data-stu-id="6b174-145">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="6b174-146">變更追蹤代理程式</span><span class="sxs-lookup"><span data-stu-id="6b174-146">Change-tracking proxies</span></span>

<span data-ttu-id="6b174-147">EF Core 現在可以產生執行時代理,自動實現[NotifyNotify 屬性更改](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1)與[通知屬性變更](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1)。</span><span class="sxs-lookup"><span data-stu-id="6b174-147">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span>
<span data-ttu-id="6b174-148">然後,這些將實體屬性的值更改直接報告給 EF Core,從而避免掃描更改的需要。</span><span class="sxs-lookup"><span data-stu-id="6b174-148">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span>
<span data-ttu-id="6b174-149">但是,代理有他們自己的一套限制,因此它們並不適合所有人。</span><span class="sxs-lookup"><span data-stu-id="6b174-149">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="6b174-150">文件按問題[#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076)跟蹤。</span><span class="sxs-lookup"><span data-stu-id="6b174-150">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="6b174-151">增強的除錯檢視</span><span class="sxs-lookup"><span data-stu-id="6b174-151">Enhanced debug views</span></span>

<span data-ttu-id="6b174-152">除錯檢視是在除錯問題時查看 EF Core 的內部的一種簡單方法。</span><span class="sxs-lookup"><span data-stu-id="6b174-152">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span>
<span data-ttu-id="6b174-153">模型的調試視圖在一段時間前已實現。</span><span class="sxs-lookup"><span data-stu-id="6b174-153">A debug view for the Model was implemented some time ago.</span></span>
<span data-ttu-id="6b174-154">對於 EF Core 5.0,我們使模型檢視更易於閱讀,並為狀態管理器中的跟蹤實體添加了新的調試視圖。</span><span class="sxs-lookup"><span data-stu-id="6b174-154">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="6b174-155">初步檔包含在[2019 年 12 月 12 日的 EF 每周狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)中。</span><span class="sxs-lookup"><span data-stu-id="6b174-155">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="6b174-156">其他文件按問題[#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086)跟蹤。</span><span class="sxs-lookup"><span data-stu-id="6b174-156">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="6b174-157">改進資料庫空語的處理</span><span class="sxs-lookup"><span data-stu-id="6b174-157">Improved handling of database null semantics</span></span>

<span data-ttu-id="6b174-158">關係資料庫通常將 NULL 視為未知值,因此不等於任何其他 NULL。</span><span class="sxs-lookup"><span data-stu-id="6b174-158">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span>
<span data-ttu-id="6b174-159">C# 將 null 視為定義值,該值與任何其他空值進行比較。</span><span class="sxs-lookup"><span data-stu-id="6b174-159">While C# treats null as a defined value, which compares equal to any other null.</span></span>
<span data-ttu-id="6b174-160">預設情況下,EF Core 會轉換查詢,以便它們使用 C# 空語義。</span><span class="sxs-lookup"><span data-stu-id="6b174-160">EF Core by default translates queries so that they use C# null semantics.</span></span>
<span data-ttu-id="6b174-161">EF Core 5.0 大大提高了這些翻譯的效率。</span><span class="sxs-lookup"><span data-stu-id="6b174-161">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="6b174-162">文件按問題[#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612)跟蹤。</span><span class="sxs-lookup"><span data-stu-id="6b174-162">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="6b174-163">索引器屬性</span><span class="sxs-lookup"><span data-stu-id="6b174-163">Indexer properties</span></span>

<span data-ttu-id="6b174-164">EF Core 5.0 支援 C# 索引器屬性的對應。</span><span class="sxs-lookup"><span data-stu-id="6b174-164">EF Core 5.0 supports mapping of C# indexer properties.</span></span>
<span data-ttu-id="6b174-165">這些屬性允許實體充當屬性包,其中列映射到包中的命名屬性。</span><span class="sxs-lookup"><span data-stu-id="6b174-165">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="6b174-166">文件按問題[#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018)跟蹤。</span><span class="sxs-lookup"><span data-stu-id="6b174-166">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="6b174-167">產生枚舉對應的檢查約束</span><span class="sxs-lookup"><span data-stu-id="6b174-167">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="6b174-168">EF Core 5.0 遷移現在可以生成用於枚舉屬性映射的 CHECK 約束。</span><span class="sxs-lookup"><span data-stu-id="6b174-168">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span>
<span data-ttu-id="6b174-169">例如：</span><span class="sxs-lookup"><span data-stu-id="6b174-169">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="6b174-170">文檔按[問題#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082)跟蹤。</span><span class="sxs-lookup"><span data-stu-id="6b174-170">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="6b174-171">是關係</span><span class="sxs-lookup"><span data-stu-id="6b174-171">IsRelational</span></span>

<span data-ttu-id="6b174-172">除了現有的`IsRelational``IsSqlServer`和`IsSqlite`之外,還添加了一個新`IsInMemory`方法 。</span><span class="sxs-lookup"><span data-stu-id="6b174-172">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span>
<span data-ttu-id="6b174-173">此方法可用於測試 DbContext 是否使用任何關係資料庫提供程式。</span><span class="sxs-lookup"><span data-stu-id="6b174-173">This method can be used to test if the DbContext is using any relational database provider.</span></span>
<span data-ttu-id="6b174-174">例如：</span><span class="sxs-lookup"><span data-stu-id="6b174-174">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="6b174-175">文件按問題[#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185)跟蹤。</span><span class="sxs-lookup"><span data-stu-id="6b174-175">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="6b174-176">宇宙樂觀與ETags併發</span><span class="sxs-lookup"><span data-stu-id="6b174-176">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="6b174-177">Azure Cosmos 資料庫資料庫提供程式現在支援使用 ETags 的樂觀併發。</span><span class="sxs-lookup"><span data-stu-id="6b174-177">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span>
<span data-ttu-id="6b174-178">使用 OnModel 建立模型產生器設定 ETag:</span><span class="sxs-lookup"><span data-stu-id="6b174-178">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="6b174-179">然後`DbUpdateConcurrencyException`,SaveChanges 將引發併發衝突,[可以處理](https://docs.microsoft.com/ef/core/saving/concurrency)該衝突以實現重試等。</span><span class="sxs-lookup"><span data-stu-id="6b174-179">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](https://docs.microsoft.com/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="6b174-180">文件按問題[#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099)跟蹤。</span><span class="sxs-lookup"><span data-stu-id="6b174-180">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="6b174-181">查詢更多日期時間建構的轉換</span><span class="sxs-lookup"><span data-stu-id="6b174-181">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="6b174-182">包含新 DateTime 建構的查詢現已翻譯。</span><span class="sxs-lookup"><span data-stu-id="6b174-182">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="6b174-183">此外,現在映射了以下 SQL Server 函數:</span><span class="sxs-lookup"><span data-stu-id="6b174-183">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="6b174-184">日期差異週</span><span class="sxs-lookup"><span data-stu-id="6b174-184">DateDiffWeek</span></span>
* <span data-ttu-id="6b174-185">日期從部分</span><span class="sxs-lookup"><span data-stu-id="6b174-185">DateFromParts</span></span>

<span data-ttu-id="6b174-186">例如：</span><span class="sxs-lookup"><span data-stu-id="6b174-186">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="6b174-187">文件按問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="6b174-187">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="6b174-188">查詢更多位元組建構的轉換</span><span class="sxs-lookup"><span data-stu-id="6b174-188">Query translations for more byte array constructs</span></span>

<span data-ttu-id="6b174-189">使用位元組屬性上的「包含」、長度、序列等查詢現在轉換為 SQL。</span><span class="sxs-lookup"><span data-stu-id="6b174-189">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="6b174-190">初步檔包含在[2019 年 12 月 5 日的 EF 每周狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)中。</span><span class="sxs-lookup"><span data-stu-id="6b174-190">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="6b174-191">其他文件按問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)跟蹤。</span><span class="sxs-lookup"><span data-stu-id="6b174-191">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="6b174-192">反向的查詢轉換</span><span class="sxs-lookup"><span data-stu-id="6b174-192">Query translation for Reverse</span></span>

<span data-ttu-id="6b174-193">使用的`Reverse`查詢現已翻譯。</span><span class="sxs-lookup"><span data-stu-id="6b174-193">Queries using `Reverse` are now translated.</span></span>
<span data-ttu-id="6b174-194">例如：</span><span class="sxs-lookup"><span data-stu-id="6b174-194">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="6b174-195">文件按問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="6b174-195">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="6b174-196">依位運算子查詢轉換</span><span class="sxs-lookup"><span data-stu-id="6b174-196">Query translation for bitwise operators</span></span>

<span data-ttu-id="6b174-197">使用位運算子查詢現在被翻譯在更多情況下 例如:</span><span class="sxs-lookup"><span data-stu-id="6b174-197">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="6b174-198">文件按問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="6b174-198">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="6b174-199">查詢參數上的字串翻譯</span><span class="sxs-lookup"><span data-stu-id="6b174-199">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="6b174-200">使用字串方法"包含、開始使用"和"結束"的查詢現在在使用 Azure Cosmos DB 提供程式時已翻譯。</span><span class="sxs-lookup"><span data-stu-id="6b174-200">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="6b174-201">文件按問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="6b174-201">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
