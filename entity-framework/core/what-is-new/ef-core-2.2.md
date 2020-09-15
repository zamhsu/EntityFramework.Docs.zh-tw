---
title: EF Core 2.2 的新功能 - EF Core
description: Entity Framework Core 2.2 的變更和改進
author: divega
ms.date: 11/14/2018
uid: core/what-is-new/ef-core-2.2
ms.openlocfilehash: 68e3cbd5c7345330a47f1457c9b096fee5dd49e9
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072325"
---
# <a name="new-features-in-ef-core-22"></a><span data-ttu-id="1a3e3-103">EF Core 2.2 中的新功能</span><span class="sxs-lookup"><span data-stu-id="1a3e3-103">New features in EF Core 2.2</span></span>

## <a name="spatial-data-support"></a><span data-ttu-id="1a3e3-104">空間資料支援</span><span class="sxs-lookup"><span data-stu-id="1a3e3-104">Spatial data support</span></span>

<span data-ttu-id="1a3e3-105">空間資料可用於代表物件的實際位置與圖形。</span><span class="sxs-lookup"><span data-stu-id="1a3e3-105">Spatial data can be used to represent the physical location and shape of objects.</span></span>
<span data-ttu-id="1a3e3-106">許多資料庫皆可以原生方式儲存、索引及查詢空間資料。</span><span class="sxs-lookup"><span data-stu-id="1a3e3-106">Many databases can natively store, index, and query spatial data.</span></span>
<span data-ttu-id="1a3e3-107">常見的案例包括，查詢指定距離內的物件，和測試多邊形是否包含指定的位置。</span><span class="sxs-lookup"><span data-stu-id="1a3e3-107">Common scenarios include querying for objects within a given distance, and testing if a polygon contains a given location.</span></span>
<span data-ttu-id="1a3e3-108">EF Core 2.2 現已支援使用來自多個資料庫 (使用來自 [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) (NTS) 程式庫的類型) 的空間資料。</span><span class="sxs-lookup"><span data-stu-id="1a3e3-108">EF Core 2.2 now supports working with spatial data from various databases using types from the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) (NTS) library.</span></span>

<span data-ttu-id="1a3e3-109">空間資料支援會以提供者專屬延伸模組套件系列的方式實作。</span><span class="sxs-lookup"><span data-stu-id="1a3e3-109">Spatial data support is implemented as a series of provider-specific extension packages.</span></span>
<span data-ttu-id="1a3e3-110">這些套件中的每個套件，都會提供 NTS 類型與方法的對應，以及資料庫中對應的空間類型與函式。</span><span class="sxs-lookup"><span data-stu-id="1a3e3-110">Each of these packages contributes mappings for NTS types and methods, and the corresponding spatial types and functions in the database.</span></span>
<span data-ttu-id="1a3e3-111">這類的提供者延伸模組現已供 [SQL Server](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite/)、[SQLite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite/) 及 [PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite/) (來自 [Npgsql 專案](https://www.npgsql.org/)) 使用。</span><span class="sxs-lookup"><span data-stu-id="1a3e3-111">Such provider extensions are now available for [SQL Server](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite/), [SQLite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite/), and [PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite/) (from the [Npgsql project](https://www.npgsql.org/)).</span></span>
<span data-ttu-id="1a3e3-112">空間類型可搭配 [EF Core 記憶體內部提供者](xref:core/providers/in-memory/index)直接使用，且不需要額外的延伸模組。</span><span class="sxs-lookup"><span data-stu-id="1a3e3-112">Spatial types can be used directly with the [EF Core in-memory provider](xref:core/providers/in-memory/index) without additional extensions.</span></span>

<span data-ttu-id="1a3e3-113">安裝提供者延伸模組之後，您可將支援類型的屬性，新增至您的實體。</span><span class="sxs-lookup"><span data-stu-id="1a3e3-113">Once the provider extension is installed, you can add properties of supported types to your entities.</span></span> <span data-ttu-id="1a3e3-114">例如：</span><span class="sxs-lookup"><span data-stu-id="1a3e3-114">For example:</span></span>

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

<span data-ttu-id="1a3e3-115">接下來可以保存具有空間資料的實體：</span><span class="sxs-lookup"><span data-stu-id="1a3e3-115">You can then persist entities with spatial data:</span></span>

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

<span data-ttu-id="1a3e3-116">還可以依據空間資料及作業，執行資料庫查詢：</span><span class="sxs-lookup"><span data-stu-id="1a3e3-116">And you can execute database queries based on spatial data and operations:</span></span>

``` csharp
  var nearestFriends =
      (from f in context.Friends
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

<span data-ttu-id="1a3e3-117">如需此功能的詳細資訊，請參閱[空間類型文件](xref:core/modeling/spatial)。</span><span class="sxs-lookup"><span data-stu-id="1a3e3-117">For more information on this feature, see the [spatial types documentation](xref:core/modeling/spatial).</span></span>

## <a name="collections-of-owned-entities"></a><span data-ttu-id="1a3e3-118">自有實體的集合</span><span class="sxs-lookup"><span data-stu-id="1a3e3-118">Collections of owned entities</span></span>

<span data-ttu-id="1a3e3-119">EF Core 2.0 已將功能新增至一對一關聯中的模型擁有權。</span><span class="sxs-lookup"><span data-stu-id="1a3e3-119">EF Core 2.0 added the ability to model ownership in one-to-one associations.</span></span>
<span data-ttu-id="1a3e3-120">EF Core 2.2 將傳送擁有權的功能，擴展到一對多關聯。</span><span class="sxs-lookup"><span data-stu-id="1a3e3-120">EF Core 2.2 extends the ability to express ownership to one-to-many associations.</span></span>
<span data-ttu-id="1a3e3-121">擁有權有助於限制實體的使用方式。</span><span class="sxs-lookup"><span data-stu-id="1a3e3-121">Ownership helps constrain how entities are used.</span></span>

<span data-ttu-id="1a3e3-122">例如，自有實體：</span><span class="sxs-lookup"><span data-stu-id="1a3e3-122">For example, owned entities:</span></span>

- <span data-ttu-id="1a3e3-123">自有實體僅能出現在其他實體類型的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="1a3e3-123">Can only ever appear on navigation properties of other entity types.</span></span>
- <span data-ttu-id="1a3e3-124">自有實體會自動載入，且僅能由搭配其擁有者的 DbContext 追蹤。</span><span class="sxs-lookup"><span data-stu-id="1a3e3-124">Are automatically loaded, and can only be tracked by a DbContext alongside their owner.</span></span>

<span data-ttu-id="1a3e3-125">在關聯式資料庫中，就如同一般的一對多關係，所擁有的集合會對應至擁有者的個別資料表。</span><span class="sxs-lookup"><span data-stu-id="1a3e3-125">In relational databases, owned collections are mapped to separate tables from the owner, just like regular one-to-many associations.</span></span>
<span data-ttu-id="1a3e3-126">但在文件導向的資料庫中，我們計劃將相同文件內的自有實體 (在所擁有的集合或參考中) 構築為擁有者。</span><span class="sxs-lookup"><span data-stu-id="1a3e3-126">But in document-oriented databases, we plan to nest owned entities (in owned collections or references) within the same document as the owner.</span></span>

<span data-ttu-id="1a3e3-127">您可透過呼叫新的 OwnsMany() API 使用此功能：</span><span class="sxs-lookup"><span data-stu-id="1a3e3-127">You can use the feature by calling the new OwnsMany() API:</span></span>

``` csharp
modelBuilder.Entity<Customer>().OwnsMany(c => c.Addresses);
```

<span data-ttu-id="1a3e3-128">如需詳細資訊，請參閱[更新自有實體文件](xref:core/modeling/owned-entities#collections-of-owned-types)。</span><span class="sxs-lookup"><span data-stu-id="1a3e3-128">For more information, see the [updated owned entities documentation](xref:core/modeling/owned-entities#collections-of-owned-types).</span></span>

## <a name="query-tags"></a><span data-ttu-id="1a3e3-129">查詢標籤</span><span class="sxs-lookup"><span data-stu-id="1a3e3-129">Query tags</span></span>

<span data-ttu-id="1a3e3-130">此功能可簡化程式碼中 LINQ 查詢與記錄檔中所擷取產生 SQL 查詢的相互關聯。</span><span class="sxs-lookup"><span data-stu-id="1a3e3-130">This feature simplifies the correlation of LINQ queries in code with generated SQL queries captured in logs.</span></span>

<span data-ttu-id="1a3e3-131">若要利用查詢標籤，您可以使用新的 TagWith() 方法，標註 LINQ 查詢。</span><span class="sxs-lookup"><span data-stu-id="1a3e3-131">To take advantage of query tags, you annotate a LINQ query using the new TagWith() method.</span></span>
<span data-ttu-id="1a3e3-132">使用先前範例的空間查詢：</span><span class="sxs-lookup"><span data-stu-id="1a3e3-132">Using the spatial query from a previous example:</span></span>

``` csharp
  var nearestFriends =
      (from f in context.Friends.TagWith(@"This is my spatial query!")
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

<span data-ttu-id="1a3e3-133">此 LINQ 查詢會產生下列 SQL 輸出：</span><span class="sxs-lookup"><span data-stu-id="1a3e3-133">This LINQ query will produce the following SQL output:</span></span>

``` sql
-- This is my spatial query!

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="1a3e3-134">如需詳細資訊，請參閱[查詢標籤文件](xref:core/querying/tags)。</span><span class="sxs-lookup"><span data-stu-id="1a3e3-134">For more information, see the [query tags documentation](xref:core/querying/tags).</span></span>
