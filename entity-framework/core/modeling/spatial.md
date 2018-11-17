---
title: 空間資料-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
ms.assetid: 2BDE29FC-4161-41A0-841E-69F51CCD9341
uid: core/modeling/spatial
ms.openlocfilehash: cf488c6b7d94ca19018efe1c23ff410fe7eb594b
ms.sourcegitcommit: 81c53ac43d8f15b900f117294ec71dc49fe028fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2018
ms.locfileid: "51817906"
---
# <a name="spatial-data"></a>空間資料

> [!NOTE]
> 這項功能是在 EF Core 2.2 的新功能。

空間資料代表的實體位置和物件的形狀。 多個資料庫提供支援這種類型的資料，因此可以編製索引和查詢及其他資料。 常見的案例包括查詢的位置，從指定距離內的物件，或選取的物件，其框線包含指定的位置。 EF Core 支援使用的空間資料類型對應[NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite)空間程式庫。

## <a name="installing"></a>安裝

若要使用的空間資料與 EF Core，您需要安裝適當的支援 NuGet 套件。 您需要安裝哪些套件取決於您使用的提供者。

EF Core 提供者                        | 空間的 NuGet 套件
--------------------------------------- | ---------------------
Microsoft.EntityFrameworkCore.SqlServer | [Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
Microsoft.EntityFrameworkCore.Sqlite    | [Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
Microsoft.EntityFrameworkCore.InMemory  | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite)
Npgsql.EntityFrameworkCore.PostgreSQL   | [Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a>反向工程

空間的 NuGet 封裝也啟用[反向工程](../managing-schemas/scaffolding.md)模型空間的屬性，但您需要安裝套件***之前***執行`Scaffold-DbContext`或`dotnet ef dbcontext scaffold`。 如果不這麼做，您會收到有關未針對資料行中尋找型別對應的警告，並將略過資料行。

## <a name="nettopologysuite-nts"></a>NetTopologySuite (NTS)

NetTopologySuite 是適用於.NET 的空間程式庫。 EF Core 可讓空間資料類型對應資料庫中藉由在您的模型中使用 NTS 類型。

若要啟用透過 NTS 空間類型的對應，請在提供者的 DbContext 選項產生器呼叫 UseNetTopologySuite; 方法。 比方說，SQL Server 與您會呼叫它像這樣。

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

有數種空間資料類型。 您使用哪種類型取決於您想要允許的圖案的類型。 以下是您可以在模型中使用屬性的 NTS 類型的階層架構。 它們位於`NetTopologySuite.Geometries`命名空間。 GeoAPI 封裝中對應的介面 (`GeoAPI.Geometries`命名空間) 也可以使用。

* 幾何
  * 點
  * LineString
  * 多邊形
  * GeometryCollection
    * MultiPoint
    * MultiLineString
    * MultiPolygon

> [!WARNING]
> CircularString、 CompoundCurve、 和 CurePolygon NTS 不支援。

使用基底的幾何類型可讓任何類型的屬性所指定的圖形。

下列的實體類別可以用來對應至資料表中[Wide World Importers 範例資料庫](http://go.microsoft.com/fwlink/?LinkID=800630)。

``` csharp
[Table("Cities", Schema = "Application"))]
class City
{
    public int CityID { get; set; }

    public string CityName { get; set; }

    public IPoint Location { get; set; }
}

[Table("Countries", Schema = "Application"))]
class Country
{
    public int CountryID { get; set; }

    public string CountryName { get; set; }

    // Database includes both Polygon and MultiPolygon values
    public IGeometry Border { get; set; }
}
```

### <a name="creating-values"></a>正在建立的值

您可以使用建構函式來建立 geometry 物件;不過，NTS 建議改為使用 geometry factory。 這可讓您指定的預設 SRID （座標所使用的空間參考系統），並可讓您控制更進階例如精確度模型 （用於計算期間） 與座標 （決定哪些協調-維度的順序而量值）。

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(-122.121512, 47.6739882);
```

> [!NOTE]
> 4326 指 WGS 84，GPS 和其他地理系統中使用的標準。

### <a name="longitude-and-latitude"></a>經度和緯度

NTS 中的座標會根據 X 和 Y 值。 若要表示經度和緯度，可使用經度和 Y X 緯度。 請注意，這**回溯**從`latitude, longitude`中您通常會看到這些值的格式。

### <a name="srid-ignored-during-client-operations"></a>在用戶端作業期間略過的 SRID

NTS 會在作業期間，略過的 SRID 值。 它假設有一個平面座標系統。 這表示，如果您指定經度和緯度，例如距離、 長度和區域會以度數為單位，不公尺某些用戶端評估值的座標。 如需更有意義的值，您必須先專案要使用等程式庫的另一個座標系統的座標[ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI)然後才計算這些值。

如果作業是由 EF Core 透過 SQL server 評估，會決定結果的單位，由資料庫。

以下是使用 ProjNet4GeoAPI 來計算兩個城市之間的距離的範例。

``` csharp
static class GeometryExtensions
{
    static readonly IGeometryServices _geometryServices = NtsGeometryServices.Instance;
    static readonly ICoordinateSystemServices _coordinateSystemServices
        = new CoordinateSystemServices(
            new CoordinateSystemFactory(),
            new CoordinateTransformationFactory(),
            new Dictionary<int, string>
            {
                // Coordinate systems:

                // (3857 and 4326 included automatically)

                // This coordinate system covers the area of our data.
                // Different data requires a different coordinate system.
                [2855] =
                @"
                    PROJCS[""NAD83(HARN) / Washington North"",
                        GEOGCS[""NAD83(HARN)"",
                            DATUM[""NAD83_High_Accuracy_Regional_Network"",
                                SPHEROID[""GRS 1980"",6378137,298.257222101,
                                    AUTHORITY[""EPSG"",""7019""]],
                                AUTHORITY[""EPSG"",""6152""]],
                            PRIMEM[""Greenwich"",0,
                                AUTHORITY[""EPSG"",""8901""]],
                            UNIT[""degree"",0.01745329251994328,
                                AUTHORITY[""EPSG"",""9122""]],
                            AUTHORITY[""EPSG"",""4152""]],
                        PROJECTION[""Lambert_Conformal_Conic_2SP""],
                        PARAMETER[""standard_parallel_1"",48.73333333333333],
                        PARAMETER[""standard_parallel_2"",47.5],
                        PARAMETER[""latitude_of_origin"",47],
                        PARAMETER[""central_meridian"",-120.8333333333333],
                        PARAMETER[""false_easting"",500000],
                        PARAMETER[""false_northing"",0],
                        UNIT[""metre"",1,
                            AUTHORITY[""EPSG"",""9001""]],
                        AUTHORITY[""EPSG"",""2855""]]
                "
            });

    public static IGeometry ProjectTo(this IGeometry geometry, int srid)
    {
        var geometryFactory = _geometryServices.CreateGeometryFactory(srid);
        var transformation = _coordinateSystemServices.CreateTransformation(geometry.SRID, srid);

        return GeometryTransform.TransformGeometry(
            geometryFactory,
            geometry,
            transformation.MathTransform);
    }
}
```

``` csharp
var seattle = new Point(-122.333056, 47.609722) { SRID = 4326 };
var redmond = new Point(-122.123889, 47.669444) { SRID = 4326 };

var distance = seattle.ProjectTo(2855).Distance(redmond.ProjectTo(2855));
```

## <a name="querying-data"></a>查詢資料

LINQ，NTS 可用方法與屬性做為資料庫函式會轉譯為 SQL。 比方說，距離與 Contains 方法會轉譯下列查詢中。 在這篇文章結尾處表顯示各種 EF Core 提供者所支援成員。

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a>SQL Server

如果您使用 SQL Server，有一些您應該要注意的其他項目。

### <a name="geography-or-geometry"></a>地理或幾何

根據預設，空間的屬性對應到`geography`SQL Server 中的資料行。 若要使用`geometry`，[設定資料行類型](xref:core/modeling/relational/data-types)模型中。

### <a name="geography-polygon-rings"></a>地理多邊形環

當使用`geography`資料行類型的 SQL Server 會施加額外的需求，在外部環形 （或 shell） 和內部環形 （或漏洞）。 外部環形以逆時針方向必須導向，而且內部環形順時針旋轉。 NTS 會驗證此值傳送至資料庫之前。

### <a name="fullglobe"></a>FullGlobe

SQL Server 有非標準的幾何類型使用時，表示完整的地球`geography`資料行類型。 它也有方法來表示完整的地球 （不含一個外部環形） 為基礎的多邊形。 這些都不會受到 NTS。

> [!WARNING]
> NTS 不支援 FullGlobe 和以它為基礎的多邊形。

## <a name="sqlite"></a>SQLite

以下是一些使用 SQLite 的其他資訊。

### <a name="installing-spatialite"></a>安裝 SpatiaLite

在 Windows 中，原生 mod_spatialite 程式庫散發為 NuGet 套件相依性。 其他平台，就需要另外安裝。 這通常是使用軟體套件管理員。 比方說，您也可以在 Ubuntu 和 Homebrew 在 MacOS 上的使用 APT。

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

### <a name="configuring-srid"></a>設定的 SRID

在 SpatiaLite，資料行需要指定每個資料行的 SRID。 預設 SRID 為`0`。 指定使用 ForSqliteHasSrid 方法不同的 SRID。

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a>維度

類似於 SRID，資料行的維度 （或協調） 也指定成資料行的一部分。 預設的協調是 X 及 y 啟用其他協調 （Z 和 M） 使用 ForSqliteHasDimension 方法。

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a>轉譯的作業

下表顯示哪些 NTS 成員時，會轉譯成 SQL 上，每個 EF Core 提供者。

NetTopologySuite | SQL Server (geometry) | SQL Server (geography) | SQLite | Npgsql
--- |:---:|:---:|:---:|:---:
Geometry.Area | ✔ | ✔ | ✔ | ✔
Geometry.AsBinary() | ✔ | ✔ | ✔ | ✔
Geometry.AsText() | ✔ | ✔ | ✔ | ✔
Geometry.Boundary | ✔ | | ✔ | ✔
Geometry.Buffer(double) | ✔ | ✔ | ✔ | ✔
Geometry.Buffer (double，int) | | | ✔
Geometry.Centroid | ✔ | | ✔ | ✔
Geometry.Contains(Geometry) | ✔ | ✔ | ✔ | ✔
Geometry.ConvexHull() | ✔ | ✔ | ✔ | ✔
Geometry.CoveredBy(Geometry) | | | ✔ | ✔
Geometry.Covers(Geometry) | | | ✔ | ✔
Geometry.Crosses(Geometry) | ✔ | | ✔ | ✔
Geometry.Difference(Geometry) | ✔ | ✔ | ✔ | ✔
Geometry.Dimension | ✔ | ✔ | ✔ | ✔
Geometry.Disjoint(Geometry) | ✔ | ✔ | ✔ | ✔
Geometry.Distance(Geometry) | ✔ | ✔ | ✔ | ✔
Geometry.Envelope | ✔ | | ✔ | ✔
Geometry.EqualsExact(Geometry) | | | | ✔
Geometry.EqualsTopologically(Geometry) | ✔ | ✔ | ✔ | ✔
Geometry.GeometryType | ✔ | ✔ | ✔ | ✔
Geometry.GetGeometryN(int) | ✔ | | ✔ | ✔
Geometry.InteriorPoint | ✔ | | ✔
Geometry.Intersection(Geometry) | ✔ | ✔ | ✔ | ✔
Geometry.Intersects(Geometry) | ✔ | ✔ | ✔ | ✔
Geometry.IsEmpty | ✔ | ✔ | ✔ | ✔
Geometry.IsSimple | ✔ | | ✔ | ✔
Geometry.IsValid | ✔ | ✔ | ✔ | ✔
Geometry.IsWithinDistance (Geometry，double) | ✔ | | ✔
Geometry.Length | ✔ | ✔ | ✔ | ✔
Geometry.NumGeometries | ✔ | ✔ | ✔ | ✔
Geometry.NumPoints | ✔ | ✔ | ✔ | ✔
Geometry.OgcGeometryType | ✔ | ✔ | ✔
Geometry.Overlaps(Geometry) | ✔ | ✔ | ✔ | ✔
Geometry.PointOnSurface | ✔ | | ✔ | ✔
Geometry.Relate (Geometry，字串) | ✔ | | ✔ | ✔
Geometry.Reverse() | | | ✔ | ✔
Geometry.SRID | ✔ | ✔ | ✔ | ✔
Geometry.SymmetricDifference(Geometry) | ✔ | ✔ | ✔ | ✔
Geometry.ToBinary() | ✔ | ✔ | ✔ | ✔
Geometry.ToText() | ✔ | ✔ | ✔ | ✔
Geometry.Touches(Geometry) | ✔ | | ✔ | ✔
Geometry.Union() | | | ✔
Geometry.Union(Geometry) | ✔ | ✔ | ✔ | ✔
Geometry.Within(Geometry) | ✔ | ✔ | ✔ | ✔
GeometryCollection.Count | ✔ | ✔ | ✔ | ✔
GeometryCollection [int] | ✔ | ✔ | ✔ | ✔
LineString.Count | ✔ | ✔ | ✔ | ✔
LineString.EndPoint | ✔ | ✔ | ✔ | ✔
LineString.GetPointN(int) | ✔ | ✔ | ✔ | ✔
LineString.IsClosed | ✔ | ✔ | ✔ | ✔
LineString.IsRing | ✔ | | ✔ | ✔
LineString.StartPoint | ✔ | ✔ | ✔ | ✔
MultiLineString.IsClosed | ✔ | ✔ | ✔ | ✔
Point.M | ✔ | ✔ | ✔ | ✔
Point.X | ✔ | ✔ | ✔ | ✔
Point.Y | ✔ | ✔ | ✔ | ✔
Point.Z | ✔ | ✔ | ✔ | ✔
Polygon.ExteriorRing | ✔ | ✔ | ✔ | ✔
Polygon.GetInteriorRingN(int) | ✔ | ✔ | ✔ | ✔
Polygon.NumInteriorRings | ✔ | ✔ | ✔ | ✔

## <a name="additional-resources"></a>其他資源

* [SQL Server 中的空間資料](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [SpatiaLite 首頁](https://www.gaia-gis.it/fossil/libspatialite)
* [Npgsql 空間的文件](http://www.npgsql.org/efcore/mapping/nts.html)
* [PostGIS 文件](http://postgis.net/documentation/)
