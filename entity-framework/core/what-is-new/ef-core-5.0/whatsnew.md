---
title: 5\.0 EF Core 的新功能
author: ajcvickers
ms.date: 01/29/2020
uid: core/what-is-new/ef-core-5.0/whatsnew.md
ms.openlocfilehash: 65d7bd43e8a00c77fd6091a74c677635710d03e3
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417963"
---
# <a name="whats-new-in-ef-core-50"></a>5\.0 EF Core 的新功能

EF Core 5.0 目前正在開發中。
此頁面將包含每個預覽中所引進之有趣變更的總覽。
第一季（2020）中的 EF Core 5.0 的第一次預覽是暫時預期的。

此頁面不會複製[EF Core 5.0 的計畫](plan.md)。
此計畫描述 EF Core 5.0 的整體主題，包括我們打算在交付最終版本之前包含的所有專案。

我們會將這裡的連結新增至正式檔，因為它已發佈。

## <a name="preview-1-not-yet-shipped"></a>Preview 1 （尚未出貨）

### <a name="simple-logging"></a>簡單記錄

這項功能會新增類似于 EF6 中 `Database.Log` 的功能。
也就是說，它提供簡單的方法，讓您不需要設定任何類型的外部記錄架構，即可從 EF Core 取得記錄。

[2019 年12月5日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)會包含初稿檔。

其他檔則是由問題[#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085)追蹤。

### <a name="simple-way-to-get-generated-sql"></a>取得產生之 SQL 的簡單方式

EF Core 5.0 引進 `ToQueryString` 擴充方法，其會傳回執行 LINQ 查詢時 EF Core 將產生的 SQL。

初稿檔包含在[2020 年1月9日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)。

其他檔則是由問題[#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331)追蹤。

### <a name="enhanced-debug-views"></a>增強的調試視圖

「偵錯工具」是一種簡單的方式，可在偵錯工具發生問題時查看 EF Core 的內部。
模型的「偵錯工具」視圖已在一段時間前完成。
針對 EF Core 5.0，我們讓模型視圖更容易閱讀，並在狀態管理員中為追蹤的實體新增新的 [偵錯工具]。

[2019 年12月12日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)會包含初稿檔。

其他檔則是由問題[#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086)追蹤。

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a>連接或連接字串可以在初始化的 DbCoNtext 上變更

現在您可以更輕鬆地建立 DbCoNtext 實例，而不需要任何連接或連接字串。
此外，連接或連接字串現在可以在內容實例上進行變化。
這可讓相同的內容實例動態連接到不同的資料庫。

檔是由問題[#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075)追蹤。

### <a name="change-tracking-proxies"></a>變更追蹤 proxy

EF Core 現在可以產生自動執行[INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1)和[INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1)的執行時間 proxy。
這些之後，實體屬性上的這些值會直接變更為 EF Core，避免需要掃描變更。
不過，proxy 會有自己的一組限制，因此不適合所有人。

檔是由問題[#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076)追蹤。

### <a name="improved-handling-of-database-null-semantics"></a>改善資料庫 null 語義的處理

關係資料庫通常會將 Null 視為未知的值，因此不等於任何其他 Null。
C#相反地，會將 null 視為已定義的值，其比較是否等於任何其他 null。
EF Core 預設會轉譯查詢，使其使用C# null 的語義。
EF Core 5.0 大幅提升了這些翻譯的效率。

檔是由問題[#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612)追蹤。

### <a name="indexer-properties"></a>索引子屬性

EF Core 5.0 支援索引子C#屬性的對應。
這可讓實體作為屬性包，其中的資料行會對應到包中的命名屬性。

檔是由問題[#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018)追蹤。

### <a name="generation-of-check-constraints-for-enum-mappings"></a>列舉對應的檢查條件約束產生

EF Core 5.0 遷移現在可以產生 enum 屬性對應的檢查條件約束。
例如：

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN('Useful', 'Useless', 'Unknown'))
```

檔是由問題[#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082)追蹤。

### <a name="query-translations-for-more-datetime-constructs"></a>更多日期時間結構的查詢翻譯

包含新 DataTime 結構的查詢現在已轉譯。
此外，SQL Server 函數 DateDiffWeek 現在已經對應。

檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。

### <a name="query-translations-for-more-byte-array-constructs"></a>更多位元組陣列結構的查詢翻譯

在 byte [] 屬性上使用 Contains、Length、Enumerable.sequenceequal 等的查詢現在會轉譯為 SQL。

[2019 年12月5日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)會包含初稿檔。

其他檔則是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。

### <a name="query-translation-for-reverse"></a>反向的查詢轉譯

現在會轉譯使用 `Reverse` 的查詢。
例如：

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。

### <a name="query-translation-for-bitwise-operators"></a>位運算子的查詢轉譯

使用位運算子的查詢現在會在更多情況下轉譯，例如：

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。

### <a name="query-translation-for-strings-on-cosmos"></a>Cosmos 上字串的查詢轉譯

使用 Azure Cosmos DB 提供者時，現在會轉譯使用字串方法 Contains、StartsWith 和 EndsWith 的查詢。

檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。
