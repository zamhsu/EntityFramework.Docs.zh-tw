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
# <a name="whats-new-in-ef-core-50"></a>EF 核心 5.0 中的新增功能

EF 核心 5.0 目前正在開發中。
此頁將包含每個預覽中引入的有趣更改的概述。

此頁面不重複 EF [Core 5.0 的計劃](plan.md)。
該計劃描述了 EF Core 5.0 的總體主題,包括我們在發佈最終版本之前計劃包括的所有內容。

我們將在此處添加連結到官方文檔,因為它被發佈。

## <a name="preview-2"></a>Preview 2

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a>使用 C# 屬性指定屬性支援欄位

C# 屬性現在可用於指定屬性的後備欄位。
此屬性允許 EF Core 仍像通常發生的情況一樣寫入和讀取從背襯欄位,即使無法自動找到背襯欄位也是如此。
例如：

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

文件按問題[#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230)追蹤。

### <a name="complete-discriminator-mapping"></a>完整的鑒別器對應

EF Core 使用區分列進行[繼承層次結構的 TPH 對應](/ef/core/modeling/inheritance)。
只要 EF Core 知道鑒別器的所有可能值,就可以進行某些性能增強。
EF Core 5.0 現在實現了這些增強功能。

例如,EF Core 的早期版本將始終為返回層次結構中所有類型的查詢生成此 SQL:

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

設定完整的鑒別器對應時,EF Core 5.0 現在將產生以下內容:

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

它將是從預覽 3 開始的默認行為。

### <a name="performance-improvements-in-microsoftdatasqlite"></a>微軟的效能改進.Data.Sqlite

我們對 SQLIte 進行了兩次性能改進:

* 使用 GetBytes、GetChars 和 GetTextReader 檢索二進位和字串資料現在透過使用 SqliteBlob 和流變得更加高效。
* SqliteConnection 的初始化現在很懶。

這些改進ADO.NET微軟.Data.Sqlite 提供程式,因此也提高了 EF Core 以外的性能。

## <a name="preview-1"></a>Preview 1

### <a name="simple-logging"></a>簡單記錄記錄

此功能添加了類似於`Database.Log`EF6 的功能。
也就是說,它提供了一種從 EF Core 獲取日誌的簡單方法,而無需配置任何類型的外部日誌記錄框架。

初步檔包含在[2019 年 12 月 5 日的 EF 每周狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)中。

其他文件按問題[#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085)追蹤。

### <a name="simple-way-to-get-generated-sql"></a>取得產生的 SQL 的簡單方法

EF Core 5.0`ToQueryString`引入了擴充方法,該方法將返回 EF Core 在執行 LINQ 查詢時將生成的 SQL。

初步檔包含在[2020 年 1 月 9 日的 EF 每周狀態](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)中。

其他文件按問題[#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331)跟蹤。

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a>使用 C# 屬性指示實體沒有金鑰

實體類型現在可以配置為沒有使用`KeylessAttribute`new 的密鑰。
例如：

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

文件按問題[#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186)跟蹤。

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a>初始化 DbContext 連線或連接字串

現在,創建沒有任何連接或連接字串的 DbContext 實例變得更加容易。
此外,連接或連接字串現在可以在上下文實例上發生突變。
此功能允許同一上下文實例動態連接到不同的資料庫。

文檔按[問題#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075)跟蹤。

### <a name="change-tracking-proxies"></a>變更追蹤代理程式

EF Core 現在可以產生執行時代理,自動實現[NotifyNotify 屬性更改](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1)與[通知屬性變更](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1)。
然後,這些將實體屬性的值更改直接報告給 EF Core,從而避免掃描更改的需要。
但是,代理有他們自己的一套限制,因此它們並不適合所有人。

文件按問題[#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076)跟蹤。

### <a name="enhanced-debug-views"></a>增強的除錯檢視

除錯檢視是在除錯問題時查看 EF Core 的內部的一種簡單方法。
模型的調試視圖在一段時間前已實現。
對於 EF Core 5.0,我們使模型檢視更易於閱讀,並為狀態管理器中的跟蹤實體添加了新的調試視圖。

初步檔包含在[2019 年 12 月 12 日的 EF 每周狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)中。

其他文件按問題[#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086)跟蹤。

### <a name="improved-handling-of-database-null-semantics"></a>改進資料庫空語的處理

關係資料庫通常將 NULL 視為未知值,因此不等於任何其他 NULL。
C# 將 null 視為定義值,該值與任何其他空值進行比較。
預設情況下,EF Core 會轉換查詢,以便它們使用 C# 空語義。
EF Core 5.0 大大提高了這些翻譯的效率。

文件按問題[#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612)跟蹤。

### <a name="indexer-properties"></a>索引器屬性

EF Core 5.0 支援 C# 索引器屬性的對應。
這些屬性允許實體充當屬性包,其中列映射到包中的命名屬性。

文件按問題[#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018)跟蹤。

### <a name="generation-of-check-constraints-for-enum-mappings"></a>產生枚舉對應的檢查約束

EF Core 5.0 遷移現在可以生成用於枚舉屬性映射的 CHECK 約束。
例如：

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

文檔按[問題#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082)跟蹤。

### <a name="isrelational"></a>是關係

除了現有的`IsRelational``IsSqlServer`和`IsSqlite`之外,還添加了一個新`IsInMemory`方法 。
此方法可用於測試 DbContext 是否使用任何關係資料庫提供程式。
例如：

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

文件按問題[#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185)跟蹤。

### <a name="cosmos-optimistic-concurrency-with-etags"></a>宇宙樂觀與ETags併發

Azure Cosmos 資料庫資料庫提供程式現在支援使用 ETags 的樂觀併發。
使用 OnModel 建立模型產生器設定 ETag:

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

然後`DbUpdateConcurrencyException`,SaveChanges 將引發併發衝突,[可以處理](https://docs.microsoft.com/ef/core/saving/concurrency)該衝突以實現重試等。

文件按問題[#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099)跟蹤。

### <a name="query-translations-for-more-datetime-constructs"></a>查詢更多日期時間建構的轉換

包含新 DateTime 建構的查詢現已翻譯。

此外,現在映射了以下 SQL Server 函數:

* 日期差異週
* 日期從部分

例如：

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

文件按問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。

### <a name="query-translations-for-more-byte-array-constructs"></a>查詢更多位元組建構的轉換

使用位元組屬性上的「包含」、長度、序列等查詢現在轉換為 SQL。

初步檔包含在[2019 年 12 月 5 日的 EF 每周狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)中。

其他文件按問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)跟蹤。

### <a name="query-translation-for-reverse"></a>反向的查詢轉換

使用的`Reverse`查詢現已翻譯。
例如：

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

文件按問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。

### <a name="query-translation-for-bitwise-operators"></a>依位運算子查詢轉換

使用位運算子查詢現在被翻譯在更多情況下 例如:

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

文件按問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。

### <a name="query-translation-for-strings-on-cosmos"></a>查詢參數上的字串翻譯

使用字串方法"包含、開始使用"和"結束"的查詢現在在使用 Azure Cosmos DB 提供程式時已翻譯。

文件按問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。
