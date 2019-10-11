---
title: EF Core 2.2 的新功能 - EF Core
author: divega
ms.date: 11/14/2018
ms.assetid: 998C04F3-676A-4FCF-8450-CFB0457B4198
uid: core/what-is-new/ef-core-2.2
ms.openlocfilehash: 5fcf7c6dfb4d8cb7928ef974af6deb52df7c63eb
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181380"
---
# <a name="new-features-in-ef-core-22"></a>EF Core 2.2 中的新功能

## <a name="spatial-data-support"></a>空間資料支援

空間資料可用於代表物件的實際位置與圖形。
許多資料庫皆可以原生方式儲存、索引及查詢空間資料。 常見的案例包括，查詢指定距離內的物件，和測試多邊形是否包含指定的位置。
EF Core 2.2 現已支援使用來自多個資料庫 (使用來自 [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) (NTS) 程式庫的類型) 的空間資料。

空間資料支援會以提供者專屬延伸模組套件系列的方式實作。
這些套件中的每個套件，都會提供 NTS 類型與方法的對應，以及資料庫中對應的空間類型與函式。
這類的提供者延伸模組現已供 [SQL Server](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite/)、[SQLite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite/) 及 [PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite/) (來自 [Npgsql 專案](https://www.npgsql.org/)) 使用。
空間類型可搭配 [EF Core 記憶體內部提供者](https://docs.microsoft.com/en-us/ef/core/providers/in-memory/)直接使用，且不需要額外的延伸模組。

安裝提供者延伸模組之後，您可將支援類型的屬性，新增至您的實體。 例如:

``` csharp
using NetTopologySuite.Geometries;

namespace MyApp
{
  public class Friend
  {
    [Key]
    public string Name { get; set; }
  
    [Required]
    public Point Location { get; set; }
  }
}
``` 

接下來可以保存具有空間資料的實體：

``` csharp
using (var context = new MyDbContext())
{
    context.Add(
        new Friend
        {
            Name = "Bill",
            Location = new Point(-122.34877, 47.6233355) {SRID = 4326 }
        });
    context.SaveChanges();
}
```
還可以依據空間資料及作業，執行資料庫查詢：

``` csharp
  var nearestFriends =
      (from f in context.Friends
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

如需此功能的詳細資訊，請參閱[空間類型文件](xref:core/modeling/spatial)。 

## <a name="collections-of-owned-entities"></a>自有實體的集合

EF Core 2.0 已將功能新增至一對一關聯中的模型擁有權。
EF Core 2.2 將傳送擁有權的功能，擴展到一對多關聯。
擁有權有助於限制實體的使用方式。

例如，自有實體：
- 自有實體僅能出現在其他實體類型的導覽屬性。 
- 自有實體會自動載入，且僅能由搭配其擁有者的 DbContext 追蹤。

在關聯式資料庫中，就如同一般的一對多關係，所擁有的集合會對應至擁有者的個別資料表。
但在文件導向的資料庫中，我們計劃將相同文件內的自有實體 (在所擁有的集合或參考中) 構築為擁有者。

您可透過呼叫新的 OwnsMany() API 使用此功能：

``` csharp
modelBuilder.Entity<Customer>().OwnsMany(c => c.Addresses);
```

如需詳細資訊，請參閱[更新自有實體文件](xref:core/modeling/owned-entities#collections-of-owned-types)。

## <a name="query-tags"></a>查詢標籤

此功能可簡化程式碼中 LINQ 查詢與記錄檔中所擷取產生 SQL 查詢的相互關聯。

若要利用查詢標籤，您可以使用新的 TagWith() 方法，標註 LINQ 查詢。
使用先前範例的空間查詢：

``` csharp
  var nearestFriends =
      (from f in context.Friends.TagWith(@"This is my spatial query!")
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

此 LINQ 查詢會產生下列 SQL 輸出：

``` sql
-- This is my spatial query!

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

如需詳細資訊，請參閱[查詢標籤文件](xref:core/querying/tags)。 