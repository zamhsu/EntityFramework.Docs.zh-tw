---
title: 空間資料-EF Core
description: 使用 Entity Framework Core 模型中的空間資料
author: bricelam
ms.date: 10/02/2020
uid: core/modeling/spatial
ms.openlocfilehash: 2c0cd7a8acf7e4b58eadf8805afa1fe4a1d6e949
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063929"
---
# <a name="spatial-data"></a>空間資料

> [!NOTE]
> 這項功能已在 EF Core 2.2 中新增。

空間資料代表實體位置和物件的形狀。 許多資料庫都支援這種類型的資料，因此可與其他資料一起進行編制索引和查詢。 常見的案例包括從位置查詢指定距離內的物件，或選取其框線包含指定位置的物件。 EF Core 支援使用 NetTopologySuite 空間程式庫對應到空間資料類型。

## <a name="installing"></a>安裝

為了搭配 EF Core 使用空間資料，您需要安裝適當的支援 NuGet 套件。 您需要安裝的套件取決於您所使用的提供者。

EF Core 提供者                        | 空間 NuGet 套件
--------------------------------------- | ---------------------
Microsoft.EntityFrameworkCore.SqlServer | [Microsoft.entityframeworkcore SqlServer. NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
Microsoft.EntityFrameworkCore.Sqlite    | [Microsoft.entityframeworkcore. Sqlite. NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
Microsoft.EntityFrameworkCore.InMemory  | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite)
Npgsql.EntityFrameworkCore.PostgreSQL   | [Npgsql. Microsoft.entityframeworkcore. 于 postgresql. NetTopologySuite](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)
Pomelo.EntityFrameworkCore.MySql        | [Pomelo. Microsoft.entityframeworkcore. NetTopologySuite](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql.NetTopologySuite)
Devart.Data.MySql.EFCore                | [Devart. EFCore. NetTopologySuite](https://www.nuget.org/packages/Devart.Data.MySql.EFCore.NetTopologySuite)
Devart.Data.PostgreSql.EFCore           | [Devart. 于 postgresql. EFCore. NetTopologySuite](https://www.nuget.org/packages/Devart.Data.PostgreSql.EFCore.NetTopologySuite)
Devart.Data.SQLite.EFCore               | [Devart. EFCore. NetTopologySuite](https://www.nuget.org/packages/Devart.Data.SQLite.EFCore.NetTopologySuite)
Teradata.EntityFrameworkCore            | [Teradata. Microsoft.entityframeworkcore. NetTopologySuite](https://www.nuget.org/packages/Teradata.EntityFrameworkCore.NetTopologySuite)

## <a name="nettopologysuite"></a>NetTopologySuite

[NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) 是適用于 .net 的空間程式庫。 EF Core 在您的模型中使用 NTS 類型，即可對應至資料庫中的空間資料類型。

若要透過 NTS 來啟用空間類型的對應，請在提供者的 DbCoNtext options builder 上呼叫 UseNetTopologySuite 方法。 例如，您可以使用 SQL Server 來呼叫它，如下所示。

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/WideWorldImportersContext.cs?name=snippet_UseNetTopologySuite)]

有數種空間資料類型。 您所使用的類型取決於您想要允許的圖形類型。 以下是您可以在模型中用於屬性的 NTS 類型階層。 它們位於 `NetTopologySuite.Geometries` 命名空間內。

* 幾何形狀
  * Point
  * LineString
  * 多邊形
  * GeometryCollection
    * MultiPoint
    * MultiLineString
    * MultiPolygon

> [!WARNING]
> NTS 不支援 CircularString、CompoundCurve 和 CurePolygon。

使用基底 Geometry 型別可讓屬性指定任何類型的圖形。

## <a name="longitude-and-latitude"></a>經度和緯度

NTS 中的座標是以 X 和 Y 值為依據。 若要表示經度和緯度，請使用 X 作為經度，Y 用於緯度。 請注意，這 **是** `latitude, longitude` 您通常會看到這些值的格式。

## <a name="querying-data"></a>查詢資料

下列實體類別可用來對應至 [Wide World 匯入工具範例資料庫](https://go.microsoft.com/fwlink/?LinkID=800630)中的資料表。

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/City.cs?name=snippet_City)]

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/Country.cs?name=snippet_Country)]

在 LINQ 中，可做為資料庫函數的 NTS 方法和屬性會轉譯為 SQL。 例如，在下列查詢中，會轉譯距離和包含方法。 請參閱提供者的檔，以瞭解支援的方法。

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Program.cs?name=snippet_Distance)]

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Program.cs?name=snippet_Contains)]

## <a name="reverse-engineering"></a>反向工程

空間 NuGet 套件也會啟用具有空間屬性的 [反轉工程](xref:core/managing-schemas/scaffolding) 模型，但您必須先安裝封裝， ***才能*** 執行 `Scaffold-DbContext` 或 `dotnet ef dbcontext scaffold` 。 如果沒有，您將會收到關於找不到資料行之類型對應的警告，而且將略過資料行。

## <a name="srid-ignored-during-client-operations"></a>用戶端作業期間忽略 SRID

NTS 會在作業期間忽略 SRID 值。 它會假設平面座標系統。 這表示，如果您在經度和緯度方面指定座標，某些用戶端評估值（例如距離、長度和區域）會以度為單位，而非計量。 如需更有意義的值，您必須先使用 [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) 之類的程式庫，將座標投影至另一個座標系統，然後再計算這些值。

如果作業是由 EF Core 透過 SQL 評估，則結果的單位將由資料庫決定。

以下範例使用 ProjNet4GeoAPI 來計算兩個城市之間的距離。

[!code-csharp[](../../../samples/core/Spatial/Projections/GeometryExtensions.cs?name=snippet_GeometryExtensions)]

[!code-csharp[](../../../samples/core/Spatial/Projections/Program.cs?name=snippet_ProjectTo)]

> [!NOTE]
> 4326是指 WGS 84，這是在 GPS 和其他地理系統中使用的標準。

## <a name="additional-resources"></a>其他資源

請務必閱讀提供者的檔，以取得處理空間資料的其他資訊。

* [SQL Server 提供者中的空間資料](xref:core/providers/sql-server/spatial)
* [SQLite 提供者中的空間資料](xref:core/providers/sqlite/spatial)
* [Npgsql 提供者中的空間資料](https://www.npgsql.org/efcore/mapping/nts.html)
* [NetTopologySuite 檔](https://nettopologysuite.github.io/NetTopologySuite/)
