---
title: 空間資料-EF Core
description: 使用 Entity Framework Core 模型中的空間資料
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
ms.assetid: 2BDE29FC-4161-41A0-841E-69F51CCD9341
uid: core/modeling/spatial
ms.openlocfilehash: 42386fb132f135d725a2068d91dc49c7f613e277
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616690"
---
# <a name="spatial-data"></a>空間資料

> [!NOTE]
> 這項功能已在 EF Core 2.2 中新增。

空間資料代表實體位置和物件的形狀。 許多資料庫都支援這種類型的資料，因此可與其他資料一起進行編制索引和查詢。 常見的案例包括從位置查詢指定距離內的物件，或選取其框線包含指定位置的物件。 EF Core 支援使用 [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) 空間程式庫對應到空間資料類型。

## <a name="installing"></a>安裝

為了搭配 EF Core 使用空間資料，您需要安裝適當的支援 NuGet 套件。 您需要安裝的套件取決於您所使用的提供者。

EF Core 提供者                        | 空間 NuGet 套件
--------------------------------------- | ---------------------
Microsoft.EntityFrameworkCore.SqlServer | [Microsoft.entityframeworkcore SqlServer. NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
Microsoft.EntityFrameworkCore.Sqlite    | [Microsoft.entityframeworkcore. Sqlite. NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
Microsoft.EntityFrameworkCore.InMemory  | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite)
Npgsql.EntityFrameworkCore.PostgreSQL   | [Npgsql. Microsoft.entityframeworkcore. 于 postgresql. NetTopologySuite](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a>反向工程

空間 NuGet 套件也會啟用具有空間屬性的 [反轉工程](xref:core/managing-schemas/scaffolding) 模型，但您必須先安裝封裝， ***才能*** 執行 `Scaffold-DbContext` 或 `dotnet ef dbcontext scaffold` 。 如果沒有，您將會收到關於找不到資料行之類型對應的警告，而且將略過資料行。

## <a name="nettopologysuite-nts"></a>NetTopologySuite (NTS) 

NetTopologySuite 是適用于 .NET 的空間程式庫。 EF Core 在您的模型中使用 NTS 類型，即可對應至資料庫中的空間資料類型。

若要透過 NTS 來啟用空間類型的對應，請在提供者的 DbCoNtext options builder 上呼叫 UseNetTopologySuite 方法。 例如，您可以使用 SQL Server 來呼叫它，如下所示。

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

有數種空間資料類型。 您所使用的類型取決於您想要允許的圖形類型。 以下是您可以在模型中用於屬性的 NTS 類型階層。 它們位於 `NetTopologySuite.Geometries` 命名空間內。

* 幾何形狀
  * Point
  * LineString
  * Polygon
  * GeometryCollection
    * MultiPoint
    * MultiLineString
    * MultiPolygon

> [!WARNING]
> NTS 不支援 CircularString、CompoundCurve 和 CurePolygon。

使用基底 Geometry 型別可讓屬性指定任何類型的圖形。

下列實體類別可用來對應至 [Wide World 匯入工具範例資料庫](https://go.microsoft.com/fwlink/?LinkID=800630)中的資料表。

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

您可以使用函式來建立 geometry 物件;不過，NTS 建議改用 geometry factory。 這可讓您指定預設 SRID， () 座標所使用的空間參考系統，並可讓您控制更高階的專案，例如) 計算期間所 (的精確度模型以及座標序列 (判斷可) 使用的座標順序（維度和量值）。

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(new Coordinate(-122.121512, 47.6739882));
```

> [!NOTE]
> 4326是指 WGS 84，這是在 GPS 和其他地理系統中使用的標準。

### <a name="longitude-and-latitude"></a>經度和緯度

NTS 中的座標是以 X 和 Y 值為依據。 若要表示經度和緯度，請使用 X 作為經度，Y 用於緯度。 請注意，這 **是** `latitude, longitude` 您通常會看到這些值的格式。

### <a name="srid-ignored-during-client-operations"></a>用戶端作業期間忽略 SRID

NTS 會在作業期間忽略 SRID 值。 它會假設平面座標系統。 這表示，如果您在經度和緯度方面指定座標，某些用戶端評估值（例如距離、長度和區域）會以度為單位，而非計量。 如需更有意義的值，您必須先使用 [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) 之類的程式庫，將座標投影至另一個座標系統，然後再計算這些值。

如果作業是由 EF Core 透過 SQL 評估，則結果的單位將由資料庫決定。

以下範例使用 ProjNet4GeoAPI 來計算兩個城市之間的距離。

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

在 LINQ 中，可做為資料庫函數的 NTS 方法和屬性會轉譯為 SQL。 例如，在下列查詢中，會轉譯距離和包含方法。 本文結尾處的表格說明各種 EF Core 提供者所支援的成員。

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a>SQL Server

如果您是使用 SQL Server，還有一些您應該注意的其他事項。

### <a name="geography-or-geometry"></a>Geography 或 geometry

根據預設，空間屬性會對應到 `geography` SQL Server 中的資料行。 若要使用 `geometry` ，請在您的模型中 [設定資料行類型](xref:core/modeling/entity-properties#column-data-types) 。

### <a name="geography-polygon-rings"></a>地理多邊形環形

使用資料 `geography` 行類型時，SQL Server 會在外部環形 (或 shell) 和內部環形 (或孔) 強加額外的需求。 外部環形必須以逆時針方向和內部環形為方向。 NTS 會在將值傳送至資料庫之前進行驗證。

### <a name="fullglobe"></a>FullGlobe

使用資料行類型時，SQL Server 具有非標準幾何類型來代表完整的地球 `geography` 。 它也有一種方法，可根據完整的地球 (來表示多邊形，而不需要) 的外部環形。 NTS 不支援這兩種方法。

> [!WARNING]
> NTS 不支援以 FullGlobe 為基礎的多邊形和多邊形。

## <a name="sqlite"></a>SQLite

以下是使用 SQLite 的一些其他資訊。

### <a name="installing-spatialite"></a>安裝 SpatiaLite

在 Windows 上，原生 mod_spatialite 程式庫會以 NuGet 套件相依性的形式散發。 其他平臺則需要另外安裝。 這通常是使用軟體套件管理員來完成。 例如，您可以在 Ubuntu 上使用 APT，並在 MacOS 上使用 Homebrew。

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

可惜的是，較新版本的 PROJ (SpatiaLite) 的相依性與 EF 的預設 [SQLitePCLRaw](/dotnet/standard/data/sqlite/custom-versions#bundles)組合不相容。 若要解決此問題，您可以建立使用系統 SQLite 程式庫的自訂 [SQLitePCLRaw 提供者](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) ，也可以安裝 SpatiaLite 停用 PROJ 支援的自訂群組建。

``` sh
curl https://www.gaia-gis.it/gaia-sins/libspatialite-4.3.0a.tar.gz | tar -xz
cd libspatialite-4.3.0a

if [[ `uname -s` == Darwin* ]]; then
    # Mac OS requires some minor patching
    sed -i "" "s/shrext_cmds='\`test \\.\$module = .yes && echo .so \\|\\| echo \\.dylib\`'/shrext_cmds='.dylib'/g" configure
fi

./configure --disable-proj
make
make install
```

### <a name="configuring-srid"></a>設定 SRID

在 SpatiaLite 中，資料行需要指定每個資料行的 SRID。 預設 SRID 為 `0` 。 使用 ForSqliteHasSrid 方法指定不同的 SRID。

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a>尺寸

類似于 SRID，資料行的維度 (或座標) 也會指定為數據行的一部分。 預設的座標是 X 和 Y。請使用 ForSqliteHasDimension 方法來啟用其他座標 (Z 和 M) 。

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a>轉譯的作業

下表顯示每個 EF Core 提供者將哪些 NTS 成員轉譯成 SQL。

NetTopologySuite | SQL Server (幾何)  | SQL Server (geography)  | SQLite | Npgsql
--- |:---:|:---:|:---:|:---:
幾何. 區域 | ✔ | ✔ | ✔ | ✔
AsBinary ( # A1 | ✔ | ✔ | ✔ | ✔
AsText ( # A1 | ✔ | ✔ | ✔ | ✔
Geometry | ✔ | | ✔ | ✔
 (雙重) 的 Geometry 緩衝區 | ✔ | ✔ | ✔ | ✔
Geometry (double、int)  | | | ✔ | ✔
距心 | ✔ | | ✔ | ✔
Geometry。包含 (幾何)  | ✔ | ✔ | ✔ | ✔
ConvexHull ( # A1 | ✔ | ✔ | ✔ | ✔
CoveredBy (Geometry)  | | | ✔ | ✔
Geometry。涵蓋 (幾何)  | | | ✔ | ✔
幾何. (幾何的交叉)  | ✔ | | ✔ | ✔
Geometry。差異 (幾何)  | ✔ | ✔ | ✔ | ✔
Geometry. Dimension | ✔ | ✔ | ✔ | ✔
Geometry：非交集的 (幾何)  | ✔ | ✔ | ✔ | ✔
Geometry (幾何)  | ✔ | ✔ | ✔ | ✔
Geometry。信封 | ✔ | | ✔ | ✔
EqualsExact (Geometry)  | | | | ✔
EqualsTopologically (Geometry)  | ✔ | ✔ | ✔ | ✔
GeometryType | ✔ | ✔ | ✔ | ✔
GetGeometryN (int)  | ✔ | | ✔ | ✔
InteriorPoint | ✔ | | ✔ | ✔
Geometry (幾何的交集)  | ✔ | ✔ | ✔ | ✔
Geometry (幾何的交集)  | ✔ | ✔ | ✔ | ✔
IsEmpty | ✔ | ✔ | ✔ | ✔
IsSimple | ✔ | | ✔ | ✔
Geometry. IsValid | ✔ | ✔ | ✔ | ✔
IsWithinDistance (Geometry，double)  | ✔ | | ✔ | ✔
Geometry。長度 | ✔ | ✔ | ✔ | ✔
NumGeometries | ✔ | ✔ | ✔ | ✔
X.numpoints | ✔ | ✔ | ✔ | ✔
OgcGeometryType | ✔ | ✔ | ✔ | ✔
Geometry (幾何的重迭)  | ✔ | ✔ | ✔ | ✔
PointOnSurface | ✔ | | ✔ | ✔
Geometry。 (Geometry、string) 的關聯 | ✔ | | ✔ | ✔
Geometry。反向 ( # A1 | | | ✔ | ✔
SRID | ✔ | ✔ | ✔ | ✔
SymmetricDifference (Geometry)  | ✔ | ✔ | ✔ | ✔
ToBinary ( # A1 | ✔ | ✔ | ✔ | ✔
Duration.totext ( # A1 | ✔ | ✔ | ✔ | ✔
Geometry (幾何的修飾)  | ✔ | | ✔ | ✔
Geometry ( # A1 | | | ✔ | ✔
Geometry) 的聯集 ( | ✔ | ✔ | ✔ | ✔
幾何。在 (幾何內)  | ✔ | ✔ | ✔ | ✔
>geometrycollection 計數 | ✔ | ✔ | ✔ | ✔
>geometrycollection [int] | ✔ | ✔ | ✔ | ✔
LineString 計數 | ✔ | ✔ | ✔ | ✔
LineString 端點 | ✔ | ✔ | ✔ | ✔
LineString. GetPointN (int)  | ✔ | ✔ | ✔ | ✔
LineString. IsClosed | ✔ | ✔ | ✔ | ✔
LineString. IsRing | ✔ | | ✔ | ✔
LineString. StartPoint | ✔ | ✔ | ✔ | ✔
MultiLineString. IsClosed | ✔ | ✔ | ✔ | ✔
點. M | ✔ | ✔ | ✔ | ✔
Point. X | ✔ | ✔ | ✔ | ✔
Point. Y | ✔ | ✔ | ✔ | ✔
點 Z | ✔ | ✔ | ✔ | ✔
多邊形. ExteriorRing | ✔ | ✔ | ✔ | ✔
GetInteriorRingN (int)  | ✔ | ✔ | ✔ | ✔
多邊形. NumInteriorRings | ✔ | ✔ | ✔ | ✔

## <a name="additional-resources"></a>其他資源

* [SQL Server 中的空間資料](/sql/relational-databases/spatial/spatial-data-sql-server)
* [SpatiaLite 首頁](https://www.gaia-gis.it/fossil/libspatialite)
* [Npgsql 空間檔](https://www.npgsql.org/efcore/mapping/nts.html)
* [PostGIS 檔](https://postgis.net/documentation/)
