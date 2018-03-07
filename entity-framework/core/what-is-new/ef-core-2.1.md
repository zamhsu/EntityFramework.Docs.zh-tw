---
title: "EF Core 2.1 中的新功能 - EF Core"
author: divega
ms.author: divega
ms.date: 2/20/2018
ms.assetid: 585F90A3-4D5A-4DD1-92D8-5243B14E0FEC
ms.technology: entity-framework-core
uid: core/what-is-new/ef-core-2.1
ms.openlocfilehash: 1e5e9839bae1e5da4082d90c02d098bb3b2b43bd
ms.sourcegitcommit: 4b7d3d3e258b0d9cb778bb45a9f4a33c0792e38e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="new-features-in-ef-core-21"></a>EF Core 2.1 中的新功能
> [!NOTE]  
> 這個版本仍處於預覽狀態。

除了許多微幅改進及超過一百個產品的 Bug 修正，EF Core 2.1 還包含幾項新功能：

## <a name="lazy-loading"></a>消極式載入
EF Core 現在包含必要的建置組塊，可讓任何人撰寫實體類別，以視需要載入其導覽屬性。 我們也已建立新套件 Microsoft.EntityFrameworkCore.Proxies，該套件利用這些建置組塊來產生以最小修改之實體類別為基礎的消極式載入 Proxy 類別 (例如具有虛擬導覽屬性的類別)。

如需此主題的詳細資訊，請閱讀[消極式載入](xref:core/querying/related-data#lazy-loading)一節。

## <a name="parameters-in-entity-constructors"></a>實體建構函式中的參數
因為是消極式載入所需要建置組塊之一，所以我們允許建立實體，由其建構函式來接受參數。 您可以使用這些參數來插入屬性值、消極式載入委派和服務。

如需此主題的詳細資訊，請閱讀[含參數的實體建構函式](xref:core/modeling/constructors)一節。

## <a name="value-conversions"></a>值轉換
到目前為止，EF Core 只能對應基礎資料庫提供者原生支援類型的屬性。 這些值會在資料行與屬性之間來回複製，無須進行任何轉換。 從 EF Core 2.1 開始，您可以套用值轉換來轉換從資料行取得的值，再將這些值套用至屬性，反之亦然。 我們有一些可以依照慣例視需要套用的轉換，以及允許在資料行與屬性之間註冊自訂轉換的明確組態 API。 這項功能的一些應用包括：

- 將列舉儲存為字串
- 使用 SQL Server 對應不帶正負號的整數
- 屬性值的自動加密和解密

如需此主題的詳細資訊，請閱讀[值轉換](xref:core/modeling/value-conversions)一節。  

## <a name="linq-groupby-translation"></a>LINQ GroupBy 轉譯
在 2.1 版之前，EF Core 中的 GroupBy LINQ 運算子永遠會在記憶體內部評估。 我們現在支援在最常見的案例中將它轉譯成 SQL GROUP BY 子句。

此範例會顯示使用 GroupBy 來計算各種彙總函式的查詢：

``` csharp
var query = context.Orders
    .GroupBy(o => new { o.CustomerId, o.EmployeeId })
    .Select(g => new
        {
          g.Key.CustomerId,
          g.Key.EmployeeId,
          Sum = g.Sum(o => o.Amount),
          Min = g.Min(o => o.Amount),
          Max = g.Max(o => o.Amount),
          Avg = g.Average(o => Amount)
        });
```

對應的 SQL 轉譯如下所示：

``` SQL
SELECT [o].[CustomerId], [o].[EmployeeId],
    SUM([o].[Amount]), MIN([o].[Amount]), MAX([o].[Amount]), AVG([o].[Amount])
FROM [Orders] AS [o]
GROUP BY [o].[CustomerId], [o].[EmployeeId];
```

## <a name="data-seeding"></a>資料植入
在此新版本中，您可以提供初始資料來填入資料庫。 不同於 EF6，植入資料與屬於模型組態一部分的實體類型相關聯。 然後，將資料庫升級至新版模型時，EF Core 移轉可以自動計算需要套用的插入、更新或刪除作業。

例如，您可以使用此選項，為 `OnModelCreating` 中的 Post 設定種子資料：

``` csharp
modelBuilder.Entity<Post>().SeedData(new Post{ Id = 1, Text = "Hello World!" });
```

如需此主題的詳細資訊，請閱讀[資料植入](xref:core/modeling/data-seeding)一節。  

## <a name="query-types"></a>查詢類型
EF Core 模型現在可以包含查詢類型。 不同於實體類型，查詢類型並未定義索引鍵，而且無法插入、刪除或更新 (亦即這些類型是唯讀的)，但可以直接由查詢傳回。 查詢類型的一些使用方式情節包括：

- 對應至不含主索引鍵的檢視表
- 對應至不含主索引鍵的資料表
- 對應至模型中已定義的查詢
- 作為 `FromSql()` 查詢的傳回型別

如需此主題的詳細資訊，請閱讀[查詢類型](xref:core/modeling/query-types)一節。

## <a name="include-for-derived-types"></a>隨附於衍生類型
現在，當您為 `Include` 方法撰寫運算式時，您可以指定只在衍生類型上定義的導覽屬性。 針對 `Include` 的強型別版本，我們支援使用明確轉換或 `as` 運算子。 我們現在也支援參考 `Include` 的字串版本中衍生類型上所定義的導覽屬性名稱：

``` csharp
var option1 = context.People.Include(p => ((Student)p).School);
var option2 = context.People.Include(p => (p as Student).School);
var option3 = context.People.Include("School");
```

如需此主題的詳細資訊，請閱讀[隨附於衍生類型](xref:core/querying/related-data#include-on-derived-types)一節。

## <a name="systemtransactions-support"></a>System.Transactions 支援
我們新增了使用 System.Transactions 功能的能力，例如 TransactionScope。 使用支援的資料庫提供者時，這會適用於 .NET Framework 和 .NET Core。

如需此主題的詳細資訊，請閱讀 [System.Transactions](xref:core/saving/transactions#using-systemtransactions) 一節。

## <a name="better-column-ordering-in-initial-migration"></a>改進初始移轉中的資料行排序
根據客戶的意見反應，我們已更新移轉，一開始就依類別中宣告屬性的相同順序來產生資料表的資料行。 請注意，若在初始資料表建立之後新增成員，EF Core 就無法變更順序。

## <a name="optimization-of-correlated-subqueries"></a>相互關聯子查詢的最佳化
我們已改善查詢轉譯，避免在許多常見情節中執行 "N + 1" SQL 查詢，在這些情節中，於投影中使用導覽屬性會導致根查詢中的資料與相互關聯子查詢中的資料相聯結。 最佳化需要緩衝處理形成子查詢的結果，而且我們需要您修改查詢以加入新的行為。

例如，下列查詢通常會轉譯成一個用於客戶的查詢，再加上 N (其中 "N" 是傳回的客戶數目) 個用於訂單的個別查詢：

``` csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount));
```

藉由在正確位置包含 `ToList()`，您可以指出緩衝處理適用於訂單，因此可啟用最佳化：

``` csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount).ToList());
```

請注意這項查詢只會轉譯成兩個 SQL 查詢：一個用於客戶，另一個用於訂單。

### <a name="ownedattribute"></a>OwnedAttribute

您現在可以直接對類型標註 `[Owned]`，然後確定擁有者實體已新增至模型，來設定[擁有的實體類型](xref:core/modeling/owned-entities)：

``` csharp
[Owned]
public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public StreetAddress ShippingAddress { get; set; }
}
```

## <a name="database-provider-compatibility"></a>資料庫提供者相容性

EF Core 2.1 已設計成與專為 EF Core 2.0 建立的資料庫提供者相容。 雖然上述某些功能 (例如值轉換) 需要更新的提供者，其他功能 (例如消極式載入) 則受到現有提供者的支援。

> [!TIP]
> 如果您在新功能中發現未預期的不相容或任何問題，或者如果您有相關意見反應，請使用[我們的問題追蹤程式](https://github.com/aspnet/EntityFrameworkCore/issues/new)進行回報。
