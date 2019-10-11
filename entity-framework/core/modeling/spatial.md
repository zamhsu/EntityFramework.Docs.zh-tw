---
title: 空間資料-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
ms.assetid: 2BDE29FC-4161-41A0-841E-69F51CCD9341
uid: core/modeling/spatial
ms.openlocfilehash: cced53edadb890e4e86753ec2628218ffc4d1d5b
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181384"
---
# <a name="spatial-data"></a>空間資料

> [!NOTE]
> 這項功能已在 EF Core 2.2 中新增。

空間資料代表實體位置和物件的形狀。 許多資料庫都提供這種資料類型的支援，因此可以與其他資料一起編制索引及查詢。 常見的案例包括從位置查詢給定距離內的物件，或選取其框線包含指定位置的物件。 EF Core 支援使用[NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite)空間程式庫來對應到空間資料類型。

## <a name="installing"></a>安裝

若要將空間資料與 EF Core 搭配使用，您需要安裝適當的支援 NuGet 套件。 您需要安裝的套件取決於您所使用的提供者。

EF Core 提供者                        | 空間 NuGet 套件
--------------------------------------- | ---------------------
Microsoft.EntityFrameworkCore.SqlServer | [Microsoft.entityframeworkcore SqlServer. NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
Microsoft.EntityFrameworkCore.Sqlite    | [Microsoft.entityframeworkcore. Sqlite. NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
Microsoft.EntityFrameworkCore.InMemory  | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite)
Npgsql.EntityFrameworkCore.PostgreSQL   | [Npgsql. Microsoft.entityframeworkcore. 于 postgresql. NetTopologySuite](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a>反向工程

空間 NuGet 套件也會啟用具有空間屬性的[反向工程](../managing-schemas/scaffolding.md)模型，但您必須先安裝封裝，***才能***執行 `Scaffold-DbContext` 或 `dotnet ef dbcontext scaffold`。 如果沒有，您會收到關於找不到資料行之類型對應的警告，而且將會略過資料行。

## <a name="nettopologysuite-nts"></a>NetTopologySuite （NTS）

NetTopologySuite 是適用于 .NET 的空間程式庫。 EF Core 使用模型中的 NTS 類型，來對應至資料庫中的空間資料類型。

若要透過 NTS 啟用空間類型的對應，請在提供者的 DbCoNtext 選項產生器上呼叫 UseNetTopologySuite 方法。 例如，您可以使用 SQL Server 呼叫它，如下所示。

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

有數個空間資料類型。 您所使用的類型取決於您想要允許的圖形類型。 以下是您可以在模型中用於屬性的 NTS 類型階層。 它們位於 `NetTopologySuite.Geometries` 命名空間內。

* 幾何
  * 點
  * LineString
  * 多邊形
  * GeometryCollection
    * MultiPoint
    * MultiLineString
    * MultiPolygon

> [!WARNING]
> NTS 不支援 CircularString、CompoundCurve 和 CurePolygon。

使用基底 Geometry 類型，可讓屬性指定任何類型的圖形。

下列實體類別可用來對應至[Wide World 匯入工具範例資料庫](https://go.microsoft.com/fwlink/?LinkID=800630)中的資料表。

``` csharp
[Table("Cities", Schema = "Application"))]
class City
{
    public int CityID { get; set; }

    public string CityName { get; set; }

    public Point Location { get; set; }
}

[Table("Countries", Schema = "Application"))]
class Country
{
    public int CountryID { get; set; }

    public string CountryName { get; set; }

    // Database includes both Polygon and MultiPolygon values
    public Geometry Border { get; set; }
}
```

### <a name="creating-values"></a>建立值

您可以使用構造函式來建立 geometry 物件;不過，NTS 建議改為使用 geometry factory。 這可讓您指定預設 SRID （座標所使用的空間參考系統），並可讓您控制更先進的事物，例如精確度模型（在計算期間使用）和座標序列（判斷哪些座標--維度和量值--可供使用）。

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(-122.121512, 47.6739882);
```

> [!NOTE]
> 4326指的是 WGS 84，這是 GPS 和其他地理系統中所使用的標準。

### <a name="longitude-and-latitude"></a>經度和緯度

NTS 中的座標是以 X 和 Y 值為依據。 若要代表經度和緯度，請將 X 用於經度，並針對緯度使用 Y。 請注意，這是您通常會看到這些值的 `latitude, longitude` 格式的**反向**。

### <a name="srid-ignored-during-client-operations"></a>在用戶端操作期間忽略 SRID

NTS 會在作業期間忽略 SRID 值。 它假設是平面座標系統。 這表示，如果您在經度和緯度方面指定座標，某些用戶端評估的值（例如距離、長度和區域）將會以度為單位，而不是計量。 如需更有意義的值，您必須先使用[ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI)之類的程式庫，將座標投影至另一個座標系統，然後再計算這些值。

如果作業是由 EF Core 透過 SQL 進行評估，則結果的單位將由資料庫決定。

以下是使用 ProjNet4GeoAPI 來計算兩個城市之間距離的範例。

``` csharp
static class GeometryExtensions
{
    static readonly CoordinateSystemServices _coordinateSystemServices
        = new CoordinateSystemServices(
            new CoordinateSystemFactory(),
            new CoordinateTransformationFactory(),
            new Dictionary<int, string>
            {
                // Coordinate systems:

                [4326] = GeographicCoordinateSystem.WGS84.WKT,

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

    public static Geometry ProjectTo(this Geometry geometry, int srid)
    {
        var transformation = _coordinateSystemServices.CreateTransformation(geometry.SRID, srid);

        var result = geometry.Copy();
        result.Apply(new MathTransformFilter(transformation.MathTransform));

        return result;
    }

    class MathTransformFilter : ICoordinateSequenceFilter
    {
        readonly MathTransform _transform;

        public MathTransformFilter(MathTransform transform)
            => _transform = transform;

        public bool Done => false;
        public bool GeometryChanged => true;

        public void Filter(CoordinateSequence seq, int i)
        {
            var result = _transform.Transform(
                new[]
                {
                    seq.GetOrdinate(i, Ordinate.X),
                    seq.GetOrdinate(i, Ordinate.Y)
                });
            seq.SetOrdinate(i, Ordinate.X, result[0]);
            seq.SetOrdinate(i, Ordinate.Y, result[1]);
        }
    }
}
```

``` csharp
var seattle = new Point(-122.333056, 47.609722) { SRID = 4326 };
var redmond = new Point(-122.123889, 47.669444) { SRID = 4326 };

var distance = seattle.ProjectTo(2855).Distance(redmond.ProjectTo(2855));
```

## <a name="querying-data"></a>查詢資料

在 LINQ 中，當做資料庫函數使用的 NTS 方法和屬性將會轉譯為 SQL。 例如，「距離」和「包含」方法會在下列查詢中轉譯。 本文結尾的表格說明各種 EF Core 提供者所支援的成員。

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a>[SQL Server]

如果您使用 SQL Server，還有一些您應該注意的其他事項。

### <a name="geography-or-geometry"></a>Geography 或 geometry

根據預設，空間屬性會對應至 SQL Server 中的 @no__t 0 資料行。 若要使用 `geometry`，請在您的模型中[設定資料行類型](xref:core/modeling/relational/data-types)。

### <a name="geography-polygon-rings"></a>地理多邊形環形

使用 [`geography`] 資料行類型時，SQL Server 會對外部環形（或 shell）和內部環形（或孔）施加額外的需求。 外部環形必須以逆時針方向和內部環形。 NTS 會在將值傳送至資料庫之前進行驗證。

### <a name="fullglobe"></a>FullGlobe

使用 `geography` 資料行類型時，SQL Server 具有非標準 geometry 類型來代表完整的地球。 它也可以根據全地球（不含外部環形）來呈現多邊形。 NTS 不支援這兩種方法。

> [!WARNING]
> NTS 不支援以其為基礎的 FullGlobe 和多邊形。

## <a name="sqlite"></a>SQLite

以下是使用 SQLite 的其他資訊。

### <a name="installing-spatialite"></a>安裝 SpatiaLite

在 Windows 上，native mod_spatialite 程式庫會以 NuGet 套件相依性的形式散發。 其他平臺則需要另外安裝。 這通常是使用軟體套件管理員來完成。 例如，您可以使用 APT on Ubuntu 和 Homebrew on MacOS。

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

### <a name="configuring-srid"></a>設定 SRID

在 SpatiaLite 中，資料行必須指定每個資料行的 SRID。 預設 SRID 為 `0`。 使用 ForSqliteHasSrid 方法指定不同的 SRID。

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a>維度

類似于 SRID，資料行的維度（或座標）也會指定為數據行的一部分。 預設的座標是 X 和 Y。使用 ForSqliteHasDimension 方法啟用額外的座標（Z 和 M）。

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a>轉譯的作業

下表顯示每個 EF Core 提供者將哪些 NTS 成員轉譯為 SQL。

NetTopologySuite | SQL Server （geometry） | SQL Server （geography） | SQLite | Npgsql
--- |:---:|:---:|:---:|:---:
Geometry。區域 | ✔ | ✔ | ✔ | ✔
Geometry. AsBinary （） | ✔ | ✔ | ✔ | ✔
Geometry. AsText （） | ✔ | ✔ | ✔ | ✔
Geometry。界限 | ✔ | | ✔ | ✔
Geometry： Buffer （雙精度浮點數） | ✔ | ✔ | ✔ | ✔
Geometry （double，int） | | | ✔
Geometry. 距心 | ✔ | | ✔ | ✔
Geometry。 Contains （Geometry） | ✔ | ✔ | ✔ | ✔
Geometry. ConvexHull （） | ✔ | ✔ | ✔ | ✔
CoveredBy （Geometry） | | | ✔ | ✔
Geometry. 封面（Geometry） | | | ✔ | ✔
Geometry. 交叉（Geometry） | ✔ | | ✔ | ✔
Geometry 差（Geometry） | ✔ | ✔ | ✔ | ✔
Geometry。維度 | ✔ | ✔ | ✔ | ✔
非交集（Geometry） | ✔ | ✔ | ✔ | ✔
Geometry. 距離（Geometry） | ✔ | ✔ | ✔ | ✔
Geometry. 信封 | ✔ | | ✔ | ✔
EqualsExact （Geometry） | | | | ✔
EqualsTopologically （Geometry） | ✔ | ✔ | ✔ | ✔
Geometry. GeometryType | ✔ | ✔ | ✔ | ✔
GetGeometryN （int） | ✔ | | ✔ | ✔
Geometry. InteriorPoint | ✔ | | ✔
Geometry. 交集（Geometry） | ✔ | ✔ | ✔ | ✔
Geometry. 交集（Geometry） | ✔ | ✔ | ✔ | ✔
Geometry. IsEmpty | ✔ | ✔ | ✔ | ✔
Geometry. IsSimple | ✔ | | ✔ | ✔
Geometry | ✔ | ✔ | ✔ | ✔
IsWithinDistance （Geometry，double） | ✔ | | ✔
Geometry。長度 | ✔ | ✔ | ✔ | ✔
Geometry. NumGeometries | ✔ | ✔ | ✔ | ✔
Geometry. X.numpoints | ✔ | ✔ | ✔ | ✔
Geometry. OgcGeometryType | ✔ | ✔ | ✔
Geometry. 重迭（Geometry） | ✔ | ✔ | ✔ | ✔
Geometry. PointOnSurface | ✔ | | ✔ | ✔
Geometry。關聯（Geometry、string） | ✔ | | ✔ | ✔
Geometry。 Reverse （） | | | ✔ | ✔
Geometry. SRID | ✔ | ✔ | ✔ | ✔
SymmetricDifference （Geometry） | ✔ | ✔ | ✔ | ✔
Geometry. ToBinary （） | ✔ | ✔ | ✔ | ✔
Geometry. ToText （） | ✔ | ✔ | ✔ | ✔
幾何。接觸（Geometry） | ✔ | | ✔ | ✔
Geometry 聯集（） | | | ✔
Geometry （Geometry） | ✔ | ✔ | ✔ | ✔
Geometry. 內（Geometry） | ✔ | ✔ | ✔ | ✔
GeometryCollection。計數 | ✔ | ✔ | ✔ | ✔
GeometryCollection [int] | ✔ | ✔ | ✔ | ✔
LineString。計數 | ✔ | ✔ | ✔ | ✔
LineString 端點 | ✔ | ✔ | ✔ | ✔
LineString. GetPointN （int） | ✔ | ✔ | ✔ | ✔
LineString. IsClosed | ✔ | ✔ | ✔ | ✔
LineString. IsRing | ✔ | | ✔ | ✔
LineString. StartPoint | ✔ | ✔ | ✔ | ✔
MultiLineString. IsClosed | ✔ | ✔ | ✔ | ✔
Point. M | ✔ | ✔ | ✔ | ✔
Point. X | ✔ | ✔ | ✔ | ✔
Point. Y | ✔ | ✔ | ✔ | ✔
點. Z | ✔ | ✔ | ✔ | ✔
多邊形。 ExteriorRing | ✔ | ✔ | ✔ | ✔
多邊形. GetInteriorRingN （int） | ✔ | ✔ | ✔ | ✔
多邊形。 NumInteriorRings | ✔ | ✔ | ✔ | ✔

## <a name="additional-resources"></a>其他資源

* [SQL Server 中的空間資料](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [SpatiaLite 首頁](https://www.gaia-gis.it/fossil/libspatialite)
* [Npgsql 空間檔](https://www.npgsql.org/efcore/mapping/nts.html)
* [PostGIS 檔](https://postgis.net/documentation/)
