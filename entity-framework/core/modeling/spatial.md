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
# <a name="spatial-data"></a><span data-ttu-id="e71fc-102">空間資料</span><span class="sxs-lookup"><span data-stu-id="e71fc-102">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="e71fc-103">這項功能已在 EF Core 2.2 中新增。</span><span class="sxs-lookup"><span data-stu-id="e71fc-103">This feature was added in EF Core 2.2.</span></span>

<span data-ttu-id="e71fc-104">空間資料代表實體位置和物件的形狀。</span><span class="sxs-lookup"><span data-stu-id="e71fc-104">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="e71fc-105">許多資料庫都提供這種資料類型的支援，因此可以與其他資料一起編制索引及查詢。</span><span class="sxs-lookup"><span data-stu-id="e71fc-105">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="e71fc-106">常見的案例包括從位置查詢給定距離內的物件，或選取其框線包含指定位置的物件。</span><span class="sxs-lookup"><span data-stu-id="e71fc-106">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="e71fc-107">EF Core 支援使用[NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite)空間程式庫來對應到空間資料類型。</span><span class="sxs-lookup"><span data-stu-id="e71fc-107">EF Core supports mapping to spatial data types using the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="e71fc-108">安裝</span><span class="sxs-lookup"><span data-stu-id="e71fc-108">Installing</span></span>

<span data-ttu-id="e71fc-109">若要將空間資料與 EF Core 搭配使用，您需要安裝適當的支援 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="e71fc-109">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="e71fc-110">您需要安裝的套件取決於您所使用的提供者。</span><span class="sxs-lookup"><span data-stu-id="e71fc-110">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="e71fc-111">EF Core 提供者</span><span class="sxs-lookup"><span data-stu-id="e71fc-111">EF Core Provider</span></span>                        | <span data-ttu-id="e71fc-112">空間 NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="e71fc-112">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="e71fc-113">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="e71fc-113">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="e71fc-114">Microsoft.entityframeworkcore SqlServer. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="e71fc-114">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="e71fc-115">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="e71fc-115">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="e71fc-116">Microsoft.entityframeworkcore. Sqlite. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="e71fc-116">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="e71fc-117">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="e71fc-117">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="e71fc-118">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="e71fc-118">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="e71fc-119">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="e71fc-119">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="e71fc-120">Npgsql. Microsoft.entityframeworkcore. 于 postgresql. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="e71fc-120">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a><span data-ttu-id="e71fc-121">反向工程</span><span class="sxs-lookup"><span data-stu-id="e71fc-121">Reverse engineering</span></span>

<span data-ttu-id="e71fc-122">空間 NuGet 套件也會啟用具有空間屬性的[反向工程](../managing-schemas/scaffolding.md)模型，但您必須先安裝封裝，***才能***執行 `Scaffold-DbContext` 或 `dotnet ef dbcontext scaffold`。</span><span class="sxs-lookup"><span data-stu-id="e71fc-122">The spatial NuGet packages also enable [reverse engineering](../managing-schemas/scaffolding.md) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="e71fc-123">如果沒有，您會收到關於找不到資料行之類型對應的警告，而且將會略過資料行。</span><span class="sxs-lookup"><span data-stu-id="e71fc-123">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="nettopologysuite-nts"></a><span data-ttu-id="e71fc-124">NetTopologySuite （NTS）</span><span class="sxs-lookup"><span data-stu-id="e71fc-124">NetTopologySuite (NTS)</span></span>

<span data-ttu-id="e71fc-125">NetTopologySuite 是適用于 .NET 的空間程式庫。</span><span class="sxs-lookup"><span data-stu-id="e71fc-125">NetTopologySuite is a spatial library for .NET.</span></span> <span data-ttu-id="e71fc-126">EF Core 使用模型中的 NTS 類型，來對應至資料庫中的空間資料類型。</span><span class="sxs-lookup"><span data-stu-id="e71fc-126">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="e71fc-127">若要透過 NTS 啟用空間類型的對應，請在提供者的 DbCoNtext 選項產生器上呼叫 UseNetTopologySuite 方法。</span><span class="sxs-lookup"><span data-stu-id="e71fc-127">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="e71fc-128">例如，您可以使用 SQL Server 呼叫它，如下所示。</span><span class="sxs-lookup"><span data-stu-id="e71fc-128">For example, with SQL Server you'd call it like this.</span></span>

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

<span data-ttu-id="e71fc-129">有數個空間資料類型。</span><span class="sxs-lookup"><span data-stu-id="e71fc-129">There are several spatial data types.</span></span> <span data-ttu-id="e71fc-130">您所使用的類型取決於您想要允許的圖形類型。</span><span class="sxs-lookup"><span data-stu-id="e71fc-130">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="e71fc-131">以下是您可以在模型中用於屬性的 NTS 類型階層。</span><span class="sxs-lookup"><span data-stu-id="e71fc-131">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="e71fc-132">它們位於 `NetTopologySuite.Geometries` 命名空間內。</span><span class="sxs-lookup"><span data-stu-id="e71fc-132">They're located within the `NetTopologySuite.Geometries` namespace.</span></span>

* <span data-ttu-id="e71fc-133">幾何</span><span class="sxs-lookup"><span data-stu-id="e71fc-133">Geometry</span></span>
  * <span data-ttu-id="e71fc-134">點</span><span class="sxs-lookup"><span data-stu-id="e71fc-134">Point</span></span>
  * <span data-ttu-id="e71fc-135">LineString</span><span class="sxs-lookup"><span data-stu-id="e71fc-135">LineString</span></span>
  * <span data-ttu-id="e71fc-136">多邊形</span><span class="sxs-lookup"><span data-stu-id="e71fc-136">Polygon</span></span>
  * <span data-ttu-id="e71fc-137">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="e71fc-137">GeometryCollection</span></span>
    * <span data-ttu-id="e71fc-138">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="e71fc-138">MultiPoint</span></span>
    * <span data-ttu-id="e71fc-139">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="e71fc-139">MultiLineString</span></span>
    * <span data-ttu-id="e71fc-140">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="e71fc-140">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="e71fc-141">NTS 不支援 CircularString、CompoundCurve 和 CurePolygon。</span><span class="sxs-lookup"><span data-stu-id="e71fc-141">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="e71fc-142">使用基底 Geometry 類型，可讓屬性指定任何類型的圖形。</span><span class="sxs-lookup"><span data-stu-id="e71fc-142">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

<span data-ttu-id="e71fc-143">下列實體類別可用來對應至[Wide World 匯入工具範例資料庫](https://go.microsoft.com/fwlink/?LinkID=800630)中的資料表。</span><span class="sxs-lookup"><span data-stu-id="e71fc-143">The following entity classes could be used to map to tables in the [Wide World Importers sample database](https://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

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

### <a name="creating-values"></a><span data-ttu-id="e71fc-144">建立值</span><span class="sxs-lookup"><span data-stu-id="e71fc-144">Creating values</span></span>

<span data-ttu-id="e71fc-145">您可以使用構造函式來建立 geometry 物件;不過，NTS 建議改為使用 geometry factory。</span><span class="sxs-lookup"><span data-stu-id="e71fc-145">You can use constructors to create geometry objects; however, NTS recommends using a geometry factory instead.</span></span> <span data-ttu-id="e71fc-146">這可讓您指定預設 SRID （座標所使用的空間參考系統），並可讓您控制更先進的事物，例如精確度模型（在計算期間使用）和座標序列（判斷哪些座標--維度和量值--可供使用）。</span><span class="sxs-lookup"><span data-stu-id="e71fc-146">This lets you specify a default SRID (the spatial reference system used by the coordinates) and gives you control over more advanced things like the precision model (used during calculations) and the coordinate sequence (determines which ordinates--dimensions and measures--are available).</span></span>

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(-122.121512, 47.6739882);
```

> [!NOTE]
> <span data-ttu-id="e71fc-147">4326指的是 WGS 84，這是 GPS 和其他地理系統中所使用的標準。</span><span class="sxs-lookup"><span data-stu-id="e71fc-147">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

### <a name="longitude-and-latitude"></a><span data-ttu-id="e71fc-148">經度和緯度</span><span class="sxs-lookup"><span data-stu-id="e71fc-148">Longitude and Latitude</span></span>

<span data-ttu-id="e71fc-149">NTS 中的座標是以 X 和 Y 值為依據。</span><span class="sxs-lookup"><span data-stu-id="e71fc-149">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="e71fc-150">若要代表經度和緯度，請將 X 用於經度，並針對緯度使用 Y。</span><span class="sxs-lookup"><span data-stu-id="e71fc-150">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="e71fc-151">請注意，這是您通常會看到這些值的 `latitude, longitude` 格式的**反向**。</span><span class="sxs-lookup"><span data-stu-id="e71fc-151">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

### <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="e71fc-152">在用戶端操作期間忽略 SRID</span><span class="sxs-lookup"><span data-stu-id="e71fc-152">SRID Ignored during client operations</span></span>

<span data-ttu-id="e71fc-153">NTS 會在作業期間忽略 SRID 值。</span><span class="sxs-lookup"><span data-stu-id="e71fc-153">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="e71fc-154">它假設是平面座標系統。</span><span class="sxs-lookup"><span data-stu-id="e71fc-154">It assumes a planar coordinate system.</span></span> <span data-ttu-id="e71fc-155">這表示，如果您在經度和緯度方面指定座標，某些用戶端評估的值（例如距離、長度和區域）將會以度為單位，而不是計量。</span><span class="sxs-lookup"><span data-stu-id="e71fc-155">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="e71fc-156">如需更有意義的值，您必須先使用[ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI)之類的程式庫，將座標投影至另一個座標系統，然後再計算這些值。</span><span class="sxs-lookup"><span data-stu-id="e71fc-156">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="e71fc-157">如果作業是由 EF Core 透過 SQL 進行評估，則結果的單位將由資料庫決定。</span><span class="sxs-lookup"><span data-stu-id="e71fc-157">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="e71fc-158">以下是使用 ProjNet4GeoAPI 來計算兩個城市之間距離的範例。</span><span class="sxs-lookup"><span data-stu-id="e71fc-158">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

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

## <a name="querying-data"></a><span data-ttu-id="e71fc-159">查詢資料</span><span class="sxs-lookup"><span data-stu-id="e71fc-159">Querying Data</span></span>

<span data-ttu-id="e71fc-160">在 LINQ 中，當做資料庫函數使用的 NTS 方法和屬性將會轉譯為 SQL。</span><span class="sxs-lookup"><span data-stu-id="e71fc-160">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="e71fc-161">例如，「距離」和「包含」方法會在下列查詢中轉譯。</span><span class="sxs-lookup"><span data-stu-id="e71fc-161">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="e71fc-162">本文結尾的表格說明各種 EF Core 提供者所支援的成員。</span><span class="sxs-lookup"><span data-stu-id="e71fc-162">The table at the end of this article shows which members are supported by various EF Core providers.</span></span>

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a><span data-ttu-id="e71fc-163">[SQL Server]</span><span class="sxs-lookup"><span data-stu-id="e71fc-163">SQL Server</span></span>

<span data-ttu-id="e71fc-164">如果您使用 SQL Server，還有一些您應該注意的其他事項。</span><span class="sxs-lookup"><span data-stu-id="e71fc-164">If you're using SQL Server, there are some additional things you should be aware of.</span></span>

### <a name="geography-or-geometry"></a><span data-ttu-id="e71fc-165">Geography 或 geometry</span><span class="sxs-lookup"><span data-stu-id="e71fc-165">Geography or geometry</span></span>

<span data-ttu-id="e71fc-166">根據預設，空間屬性會對應至 SQL Server 中的 @no__t 0 資料行。</span><span class="sxs-lookup"><span data-stu-id="e71fc-166">By default, spatial properties are mapped to `geography` columns in SQL Server.</span></span> <span data-ttu-id="e71fc-167">若要使用 `geometry`，請在您的模型中[設定資料行類型](xref:core/modeling/relational/data-types)。</span><span class="sxs-lookup"><span data-stu-id="e71fc-167">To use `geometry`, [configure the column type](xref:core/modeling/relational/data-types) in your model.</span></span>

### <a name="geography-polygon-rings"></a><span data-ttu-id="e71fc-168">地理多邊形環形</span><span class="sxs-lookup"><span data-stu-id="e71fc-168">Geography polygon rings</span></span>

<span data-ttu-id="e71fc-169">使用 [`geography`] 資料行類型時，SQL Server 會對外部環形（或 shell）和內部環形（或孔）施加額外的需求。</span><span class="sxs-lookup"><span data-stu-id="e71fc-169">When using the `geography` column type, SQL Server imposes additional requirements on the exterior ring (or shell) and interior rings (or holes).</span></span> <span data-ttu-id="e71fc-170">外部環形必須以逆時針方向和內部環形。</span><span class="sxs-lookup"><span data-stu-id="e71fc-170">The exterior ring must be oriented counterclockwise and the interior rings clockwise.</span></span> <span data-ttu-id="e71fc-171">NTS 會在將值傳送至資料庫之前進行驗證。</span><span class="sxs-lookup"><span data-stu-id="e71fc-171">NTS validates this before sending values to the database.</span></span>

### <a name="fullglobe"></a><span data-ttu-id="e71fc-172">FullGlobe</span><span class="sxs-lookup"><span data-stu-id="e71fc-172">FullGlobe</span></span>

<span data-ttu-id="e71fc-173">使用 `geography` 資料行類型時，SQL Server 具有非標準 geometry 類型來代表完整的地球。</span><span class="sxs-lookup"><span data-stu-id="e71fc-173">SQL Server has a non-standard geometry type to represent the full globe when using the `geography` column type.</span></span> <span data-ttu-id="e71fc-174">它也可以根據全地球（不含外部環形）來呈現多邊形。</span><span class="sxs-lookup"><span data-stu-id="e71fc-174">It also has a way to represent polygons based on the full globe (without an exterior ring).</span></span> <span data-ttu-id="e71fc-175">NTS 不支援這兩種方法。</span><span class="sxs-lookup"><span data-stu-id="e71fc-175">Neither of these are supported by NTS.</span></span>

> [!WARNING]
> <span data-ttu-id="e71fc-176">NTS 不支援以其為基礎的 FullGlobe 和多邊形。</span><span class="sxs-lookup"><span data-stu-id="e71fc-176">FullGlobe and polygons based on it aren't supported by NTS.</span></span>

## <a name="sqlite"></a><span data-ttu-id="e71fc-177">SQLite</span><span class="sxs-lookup"><span data-stu-id="e71fc-177">SQLite</span></span>

<span data-ttu-id="e71fc-178">以下是使用 SQLite 的其他資訊。</span><span class="sxs-lookup"><span data-stu-id="e71fc-178">Here is some additional information for those using SQLite.</span></span>

### <a name="installing-spatialite"></a><span data-ttu-id="e71fc-179">安裝 SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="e71fc-179">Installing SpatiaLite</span></span>

<span data-ttu-id="e71fc-180">在 Windows 上，native mod_spatialite 程式庫會以 NuGet 套件相依性的形式散發。</span><span class="sxs-lookup"><span data-stu-id="e71fc-180">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="e71fc-181">其他平臺則需要另外安裝。</span><span class="sxs-lookup"><span data-stu-id="e71fc-181">Other platforms need to install it separately.</span></span> <span data-ttu-id="e71fc-182">這通常是使用軟體套件管理員來完成。</span><span class="sxs-lookup"><span data-stu-id="e71fc-182">This is typically done using a software package manager.</span></span> <span data-ttu-id="e71fc-183">例如，您可以使用 APT on Ubuntu 和 Homebrew on MacOS。</span><span class="sxs-lookup"><span data-stu-id="e71fc-183">For example, you can use APT on Ubuntu and Homebrew on MacOS.</span></span>

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

### <a name="configuring-srid"></a><span data-ttu-id="e71fc-184">設定 SRID</span><span class="sxs-lookup"><span data-stu-id="e71fc-184">Configuring SRID</span></span>

<span data-ttu-id="e71fc-185">在 SpatiaLite 中，資料行必須指定每個資料行的 SRID。</span><span class="sxs-lookup"><span data-stu-id="e71fc-185">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="e71fc-186">預設 SRID 為 `0`。</span><span class="sxs-lookup"><span data-stu-id="e71fc-186">The default SRID is `0`.</span></span> <span data-ttu-id="e71fc-187">使用 ForSqliteHasSrid 方法指定不同的 SRID。</span><span class="sxs-lookup"><span data-stu-id="e71fc-187">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a><span data-ttu-id="e71fc-188">維度</span><span class="sxs-lookup"><span data-stu-id="e71fc-188">Dimension</span></span>

<span data-ttu-id="e71fc-189">類似于 SRID，資料行的維度（或座標）也會指定為數據行的一部分。</span><span class="sxs-lookup"><span data-stu-id="e71fc-189">Similar to SRID, a column's dimension (or ordinates) is also specified as part of the column.</span></span> <span data-ttu-id="e71fc-190">預設的座標是 X 和 Y。使用 ForSqliteHasDimension 方法啟用額外的座標（Z 和 M）。</span><span class="sxs-lookup"><span data-stu-id="e71fc-190">The default ordinates are X and Y. Enable additional ordinates (Z and M) using the ForSqliteHasDimension method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a><span data-ttu-id="e71fc-191">轉譯的作業</span><span class="sxs-lookup"><span data-stu-id="e71fc-191">Translated Operations</span></span>

<span data-ttu-id="e71fc-192">下表顯示每個 EF Core 提供者將哪些 NTS 成員轉譯為 SQL。</span><span class="sxs-lookup"><span data-stu-id="e71fc-192">This table shows which NTS members are translated into SQL by each EF Core provider.</span></span>

<span data-ttu-id="e71fc-193">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="e71fc-193">NetTopologySuite</span></span> | <span data-ttu-id="e71fc-194">SQL Server （geometry）</span><span class="sxs-lookup"><span data-stu-id="e71fc-194">SQL Server (geometry)</span></span> | <span data-ttu-id="e71fc-195">SQL Server （geography）</span><span class="sxs-lookup"><span data-stu-id="e71fc-195">SQL Server (geography)</span></span> | <span data-ttu-id="e71fc-196">SQLite</span><span class="sxs-lookup"><span data-stu-id="e71fc-196">SQLite</span></span> | <span data-ttu-id="e71fc-197">Npgsql</span><span class="sxs-lookup"><span data-stu-id="e71fc-197">Npgsql</span></span>
--- |:---:|:---:|:---:|:---:
<span data-ttu-id="e71fc-198">Geometry。區域</span><span class="sxs-lookup"><span data-stu-id="e71fc-198">Geometry.Area</span></span> | <span data-ttu-id="e71fc-199">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-199">✔</span></span> | <span data-ttu-id="e71fc-200">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-200">✔</span></span> | <span data-ttu-id="e71fc-201">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-201">✔</span></span> | <span data-ttu-id="e71fc-202">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-202">✔</span></span>
<span data-ttu-id="e71fc-203">Geometry. AsBinary （）</span><span class="sxs-lookup"><span data-stu-id="e71fc-203">Geometry.AsBinary()</span></span> | <span data-ttu-id="e71fc-204">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-204">✔</span></span> | <span data-ttu-id="e71fc-205">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-205">✔</span></span> | <span data-ttu-id="e71fc-206">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-206">✔</span></span> | <span data-ttu-id="e71fc-207">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-207">✔</span></span>
<span data-ttu-id="e71fc-208">Geometry. AsText （）</span><span class="sxs-lookup"><span data-stu-id="e71fc-208">Geometry.AsText()</span></span> | <span data-ttu-id="e71fc-209">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-209">✔</span></span> | <span data-ttu-id="e71fc-210">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-210">✔</span></span> | <span data-ttu-id="e71fc-211">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-211">✔</span></span> | <span data-ttu-id="e71fc-212">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-212">✔</span></span>
<span data-ttu-id="e71fc-213">Geometry。界限</span><span class="sxs-lookup"><span data-stu-id="e71fc-213">Geometry.Boundary</span></span> | <span data-ttu-id="e71fc-214">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-214">✔</span></span> | | <span data-ttu-id="e71fc-215">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-215">✔</span></span> | <span data-ttu-id="e71fc-216">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-216">✔</span></span>
<span data-ttu-id="e71fc-217">Geometry： Buffer （雙精度浮點數）</span><span class="sxs-lookup"><span data-stu-id="e71fc-217">Geometry.Buffer(double)</span></span> | <span data-ttu-id="e71fc-218">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-218">✔</span></span> | <span data-ttu-id="e71fc-219">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-219">✔</span></span> | <span data-ttu-id="e71fc-220">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-220">✔</span></span> | <span data-ttu-id="e71fc-221">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-221">✔</span></span>
<span data-ttu-id="e71fc-222">Geometry （double，int）</span><span class="sxs-lookup"><span data-stu-id="e71fc-222">Geometry.Buffer(double, int)</span></span> | | | <span data-ttu-id="e71fc-223">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-223">✔</span></span>
<span data-ttu-id="e71fc-224">Geometry. 距心</span><span class="sxs-lookup"><span data-stu-id="e71fc-224">Geometry.Centroid</span></span> | <span data-ttu-id="e71fc-225">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-225">✔</span></span> | | <span data-ttu-id="e71fc-226">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-226">✔</span></span> | <span data-ttu-id="e71fc-227">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-227">✔</span></span>
<span data-ttu-id="e71fc-228">Geometry。 Contains （Geometry）</span><span class="sxs-lookup"><span data-stu-id="e71fc-228">Geometry.Contains(Geometry)</span></span> | <span data-ttu-id="e71fc-229">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-229">✔</span></span> | <span data-ttu-id="e71fc-230">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-230">✔</span></span> | <span data-ttu-id="e71fc-231">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-231">✔</span></span> | <span data-ttu-id="e71fc-232">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-232">✔</span></span>
<span data-ttu-id="e71fc-233">Geometry. ConvexHull （）</span><span class="sxs-lookup"><span data-stu-id="e71fc-233">Geometry.ConvexHull()</span></span> | <span data-ttu-id="e71fc-234">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-234">✔</span></span> | <span data-ttu-id="e71fc-235">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-235">✔</span></span> | <span data-ttu-id="e71fc-236">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-236">✔</span></span> | <span data-ttu-id="e71fc-237">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-237">✔</span></span>
<span data-ttu-id="e71fc-238">CoveredBy （Geometry）</span><span class="sxs-lookup"><span data-stu-id="e71fc-238">Geometry.CoveredBy(Geometry)</span></span> | | | <span data-ttu-id="e71fc-239">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-239">✔</span></span> | <span data-ttu-id="e71fc-240">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-240">✔</span></span>
<span data-ttu-id="e71fc-241">Geometry. 封面（Geometry）</span><span class="sxs-lookup"><span data-stu-id="e71fc-241">Geometry.Covers(Geometry)</span></span> | | | <span data-ttu-id="e71fc-242">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-242">✔</span></span> | <span data-ttu-id="e71fc-243">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-243">✔</span></span>
<span data-ttu-id="e71fc-244">Geometry. 交叉（Geometry）</span><span class="sxs-lookup"><span data-stu-id="e71fc-244">Geometry.Crosses(Geometry)</span></span> | <span data-ttu-id="e71fc-245">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-245">✔</span></span> | | <span data-ttu-id="e71fc-246">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-246">✔</span></span> | <span data-ttu-id="e71fc-247">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-247">✔</span></span>
<span data-ttu-id="e71fc-248">Geometry 差（Geometry）</span><span class="sxs-lookup"><span data-stu-id="e71fc-248">Geometry.Difference(Geometry)</span></span> | <span data-ttu-id="e71fc-249">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-249">✔</span></span> | <span data-ttu-id="e71fc-250">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-250">✔</span></span> | <span data-ttu-id="e71fc-251">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-251">✔</span></span> | <span data-ttu-id="e71fc-252">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-252">✔</span></span>
<span data-ttu-id="e71fc-253">Geometry。維度</span><span class="sxs-lookup"><span data-stu-id="e71fc-253">Geometry.Dimension</span></span> | <span data-ttu-id="e71fc-254">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-254">✔</span></span> | <span data-ttu-id="e71fc-255">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-255">✔</span></span> | <span data-ttu-id="e71fc-256">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-256">✔</span></span> | <span data-ttu-id="e71fc-257">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-257">✔</span></span>
<span data-ttu-id="e71fc-258">非交集（Geometry）</span><span class="sxs-lookup"><span data-stu-id="e71fc-258">Geometry.Disjoint(Geometry)</span></span> | <span data-ttu-id="e71fc-259">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-259">✔</span></span> | <span data-ttu-id="e71fc-260">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-260">✔</span></span> | <span data-ttu-id="e71fc-261">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-261">✔</span></span> | <span data-ttu-id="e71fc-262">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-262">✔</span></span>
<span data-ttu-id="e71fc-263">Geometry. 距離（Geometry）</span><span class="sxs-lookup"><span data-stu-id="e71fc-263">Geometry.Distance(Geometry)</span></span> | <span data-ttu-id="e71fc-264">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-264">✔</span></span> | <span data-ttu-id="e71fc-265">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-265">✔</span></span> | <span data-ttu-id="e71fc-266">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-266">✔</span></span> | <span data-ttu-id="e71fc-267">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-267">✔</span></span>
<span data-ttu-id="e71fc-268">Geometry. 信封</span><span class="sxs-lookup"><span data-stu-id="e71fc-268">Geometry.Envelope</span></span> | <span data-ttu-id="e71fc-269">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-269">✔</span></span> | | <span data-ttu-id="e71fc-270">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-270">✔</span></span> | <span data-ttu-id="e71fc-271">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-271">✔</span></span>
<span data-ttu-id="e71fc-272">EqualsExact （Geometry）</span><span class="sxs-lookup"><span data-stu-id="e71fc-272">Geometry.EqualsExact(Geometry)</span></span> | | | | <span data-ttu-id="e71fc-273">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-273">✔</span></span>
<span data-ttu-id="e71fc-274">EqualsTopologically （Geometry）</span><span class="sxs-lookup"><span data-stu-id="e71fc-274">Geometry.EqualsTopologically(Geometry)</span></span> | <span data-ttu-id="e71fc-275">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-275">✔</span></span> | <span data-ttu-id="e71fc-276">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-276">✔</span></span> | <span data-ttu-id="e71fc-277">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-277">✔</span></span> | <span data-ttu-id="e71fc-278">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-278">✔</span></span>
<span data-ttu-id="e71fc-279">Geometry. GeometryType</span><span class="sxs-lookup"><span data-stu-id="e71fc-279">Geometry.GeometryType</span></span> | <span data-ttu-id="e71fc-280">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-280">✔</span></span> | <span data-ttu-id="e71fc-281">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-281">✔</span></span> | <span data-ttu-id="e71fc-282">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-282">✔</span></span> | <span data-ttu-id="e71fc-283">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-283">✔</span></span>
<span data-ttu-id="e71fc-284">GetGeometryN （int）</span><span class="sxs-lookup"><span data-stu-id="e71fc-284">Geometry.GetGeometryN(int)</span></span> | <span data-ttu-id="e71fc-285">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-285">✔</span></span> | | <span data-ttu-id="e71fc-286">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-286">✔</span></span> | <span data-ttu-id="e71fc-287">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-287">✔</span></span>
<span data-ttu-id="e71fc-288">Geometry. InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="e71fc-288">Geometry.InteriorPoint</span></span> | <span data-ttu-id="e71fc-289">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-289">✔</span></span> | | <span data-ttu-id="e71fc-290">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-290">✔</span></span>
<span data-ttu-id="e71fc-291">Geometry. 交集（Geometry）</span><span class="sxs-lookup"><span data-stu-id="e71fc-291">Geometry.Intersection(Geometry)</span></span> | <span data-ttu-id="e71fc-292">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-292">✔</span></span> | <span data-ttu-id="e71fc-293">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-293">✔</span></span> | <span data-ttu-id="e71fc-294">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-294">✔</span></span> | <span data-ttu-id="e71fc-295">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-295">✔</span></span>
<span data-ttu-id="e71fc-296">Geometry. 交集（Geometry）</span><span class="sxs-lookup"><span data-stu-id="e71fc-296">Geometry.Intersects(Geometry)</span></span> | <span data-ttu-id="e71fc-297">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-297">✔</span></span> | <span data-ttu-id="e71fc-298">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-298">✔</span></span> | <span data-ttu-id="e71fc-299">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-299">✔</span></span> | <span data-ttu-id="e71fc-300">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-300">✔</span></span>
<span data-ttu-id="e71fc-301">Geometry. IsEmpty</span><span class="sxs-lookup"><span data-stu-id="e71fc-301">Geometry.IsEmpty</span></span> | <span data-ttu-id="e71fc-302">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-302">✔</span></span> | <span data-ttu-id="e71fc-303">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-303">✔</span></span> | <span data-ttu-id="e71fc-304">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-304">✔</span></span> | <span data-ttu-id="e71fc-305">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-305">✔</span></span>
<span data-ttu-id="e71fc-306">Geometry. IsSimple</span><span class="sxs-lookup"><span data-stu-id="e71fc-306">Geometry.IsSimple</span></span> | <span data-ttu-id="e71fc-307">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-307">✔</span></span> | | <span data-ttu-id="e71fc-308">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-308">✔</span></span> | <span data-ttu-id="e71fc-309">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-309">✔</span></span>
<span data-ttu-id="e71fc-310">Geometry</span><span class="sxs-lookup"><span data-stu-id="e71fc-310">Geometry.IsValid</span></span> | <span data-ttu-id="e71fc-311">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-311">✔</span></span> | <span data-ttu-id="e71fc-312">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-312">✔</span></span> | <span data-ttu-id="e71fc-313">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-313">✔</span></span> | <span data-ttu-id="e71fc-314">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-314">✔</span></span>
<span data-ttu-id="e71fc-315">IsWithinDistance （Geometry，double）</span><span class="sxs-lookup"><span data-stu-id="e71fc-315">Geometry.IsWithinDistance(Geometry, double)</span></span> | <span data-ttu-id="e71fc-316">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-316">✔</span></span> | | <span data-ttu-id="e71fc-317">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-317">✔</span></span>
<span data-ttu-id="e71fc-318">Geometry。長度</span><span class="sxs-lookup"><span data-stu-id="e71fc-318">Geometry.Length</span></span> | <span data-ttu-id="e71fc-319">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-319">✔</span></span> | <span data-ttu-id="e71fc-320">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-320">✔</span></span> | <span data-ttu-id="e71fc-321">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-321">✔</span></span> | <span data-ttu-id="e71fc-322">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-322">✔</span></span>
<span data-ttu-id="e71fc-323">Geometry. NumGeometries</span><span class="sxs-lookup"><span data-stu-id="e71fc-323">Geometry.NumGeometries</span></span> | <span data-ttu-id="e71fc-324">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-324">✔</span></span> | <span data-ttu-id="e71fc-325">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-325">✔</span></span> | <span data-ttu-id="e71fc-326">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-326">✔</span></span> | <span data-ttu-id="e71fc-327">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-327">✔</span></span>
<span data-ttu-id="e71fc-328">Geometry. X.numpoints</span><span class="sxs-lookup"><span data-stu-id="e71fc-328">Geometry.NumPoints</span></span> | <span data-ttu-id="e71fc-329">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-329">✔</span></span> | <span data-ttu-id="e71fc-330">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-330">✔</span></span> | <span data-ttu-id="e71fc-331">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-331">✔</span></span> | <span data-ttu-id="e71fc-332">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-332">✔</span></span>
<span data-ttu-id="e71fc-333">Geometry. OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="e71fc-333">Geometry.OgcGeometryType</span></span> | <span data-ttu-id="e71fc-334">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-334">✔</span></span> | <span data-ttu-id="e71fc-335">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-335">✔</span></span> | <span data-ttu-id="e71fc-336">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-336">✔</span></span>
<span data-ttu-id="e71fc-337">Geometry. 重迭（Geometry）</span><span class="sxs-lookup"><span data-stu-id="e71fc-337">Geometry.Overlaps(Geometry)</span></span> | <span data-ttu-id="e71fc-338">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-338">✔</span></span> | <span data-ttu-id="e71fc-339">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-339">✔</span></span> | <span data-ttu-id="e71fc-340">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-340">✔</span></span> | <span data-ttu-id="e71fc-341">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-341">✔</span></span>
<span data-ttu-id="e71fc-342">Geometry. PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="e71fc-342">Geometry.PointOnSurface</span></span> | <span data-ttu-id="e71fc-343">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-343">✔</span></span> | | <span data-ttu-id="e71fc-344">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-344">✔</span></span> | <span data-ttu-id="e71fc-345">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-345">✔</span></span>
<span data-ttu-id="e71fc-346">Geometry。關聯（Geometry、string）</span><span class="sxs-lookup"><span data-stu-id="e71fc-346">Geometry.Relate(Geometry, string)</span></span> | <span data-ttu-id="e71fc-347">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-347">✔</span></span> | | <span data-ttu-id="e71fc-348">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-348">✔</span></span> | <span data-ttu-id="e71fc-349">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-349">✔</span></span>
<span data-ttu-id="e71fc-350">Geometry。 Reverse （）</span><span class="sxs-lookup"><span data-stu-id="e71fc-350">Geometry.Reverse()</span></span> | | | <span data-ttu-id="e71fc-351">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-351">✔</span></span> | <span data-ttu-id="e71fc-352">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-352">✔</span></span>
<span data-ttu-id="e71fc-353">Geometry. SRID</span><span class="sxs-lookup"><span data-stu-id="e71fc-353">Geometry.SRID</span></span> | <span data-ttu-id="e71fc-354">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-354">✔</span></span> | <span data-ttu-id="e71fc-355">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-355">✔</span></span> | <span data-ttu-id="e71fc-356">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-356">✔</span></span> | <span data-ttu-id="e71fc-357">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-357">✔</span></span>
<span data-ttu-id="e71fc-358">SymmetricDifference （Geometry）</span><span class="sxs-lookup"><span data-stu-id="e71fc-358">Geometry.SymmetricDifference(Geometry)</span></span> | <span data-ttu-id="e71fc-359">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-359">✔</span></span> | <span data-ttu-id="e71fc-360">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-360">✔</span></span> | <span data-ttu-id="e71fc-361">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-361">✔</span></span> | <span data-ttu-id="e71fc-362">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-362">✔</span></span>
<span data-ttu-id="e71fc-363">Geometry. ToBinary （）</span><span class="sxs-lookup"><span data-stu-id="e71fc-363">Geometry.ToBinary()</span></span> | <span data-ttu-id="e71fc-364">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-364">✔</span></span> | <span data-ttu-id="e71fc-365">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-365">✔</span></span> | <span data-ttu-id="e71fc-366">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-366">✔</span></span> | <span data-ttu-id="e71fc-367">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-367">✔</span></span>
<span data-ttu-id="e71fc-368">Geometry. ToText （）</span><span class="sxs-lookup"><span data-stu-id="e71fc-368">Geometry.ToText()</span></span> | <span data-ttu-id="e71fc-369">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-369">✔</span></span> | <span data-ttu-id="e71fc-370">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-370">✔</span></span> | <span data-ttu-id="e71fc-371">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-371">✔</span></span> | <span data-ttu-id="e71fc-372">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-372">✔</span></span>
<span data-ttu-id="e71fc-373">幾何。接觸（Geometry）</span><span class="sxs-lookup"><span data-stu-id="e71fc-373">Geometry.Touches(Geometry)</span></span> | <span data-ttu-id="e71fc-374">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-374">✔</span></span> | | <span data-ttu-id="e71fc-375">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-375">✔</span></span> | <span data-ttu-id="e71fc-376">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-376">✔</span></span>
<span data-ttu-id="e71fc-377">Geometry 聯集（）</span><span class="sxs-lookup"><span data-stu-id="e71fc-377">Geometry.Union()</span></span> | | | <span data-ttu-id="e71fc-378">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-378">✔</span></span>
<span data-ttu-id="e71fc-379">Geometry （Geometry）</span><span class="sxs-lookup"><span data-stu-id="e71fc-379">Geometry.Union(Geometry)</span></span> | <span data-ttu-id="e71fc-380">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-380">✔</span></span> | <span data-ttu-id="e71fc-381">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-381">✔</span></span> | <span data-ttu-id="e71fc-382">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-382">✔</span></span> | <span data-ttu-id="e71fc-383">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-383">✔</span></span>
<span data-ttu-id="e71fc-384">Geometry. 內（Geometry）</span><span class="sxs-lookup"><span data-stu-id="e71fc-384">Geometry.Within(Geometry)</span></span> | <span data-ttu-id="e71fc-385">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-385">✔</span></span> | <span data-ttu-id="e71fc-386">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-386">✔</span></span> | <span data-ttu-id="e71fc-387">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-387">✔</span></span> | <span data-ttu-id="e71fc-388">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-388">✔</span></span>
<span data-ttu-id="e71fc-389">GeometryCollection。計數</span><span class="sxs-lookup"><span data-stu-id="e71fc-389">GeometryCollection.Count</span></span> | <span data-ttu-id="e71fc-390">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-390">✔</span></span> | <span data-ttu-id="e71fc-391">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-391">✔</span></span> | <span data-ttu-id="e71fc-392">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-392">✔</span></span> | <span data-ttu-id="e71fc-393">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-393">✔</span></span>
<span data-ttu-id="e71fc-394">GeometryCollection [int]</span><span class="sxs-lookup"><span data-stu-id="e71fc-394">GeometryCollection[int]</span></span> | <span data-ttu-id="e71fc-395">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-395">✔</span></span> | <span data-ttu-id="e71fc-396">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-396">✔</span></span> | <span data-ttu-id="e71fc-397">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-397">✔</span></span> | <span data-ttu-id="e71fc-398">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-398">✔</span></span>
<span data-ttu-id="e71fc-399">LineString。計數</span><span class="sxs-lookup"><span data-stu-id="e71fc-399">LineString.Count</span></span> | <span data-ttu-id="e71fc-400">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-400">✔</span></span> | <span data-ttu-id="e71fc-401">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-401">✔</span></span> | <span data-ttu-id="e71fc-402">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-402">✔</span></span> | <span data-ttu-id="e71fc-403">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-403">✔</span></span>
<span data-ttu-id="e71fc-404">LineString 端點</span><span class="sxs-lookup"><span data-stu-id="e71fc-404">LineString.EndPoint</span></span> | <span data-ttu-id="e71fc-405">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-405">✔</span></span> | <span data-ttu-id="e71fc-406">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-406">✔</span></span> | <span data-ttu-id="e71fc-407">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-407">✔</span></span> | <span data-ttu-id="e71fc-408">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-408">✔</span></span>
<span data-ttu-id="e71fc-409">LineString. GetPointN （int）</span><span class="sxs-lookup"><span data-stu-id="e71fc-409">LineString.GetPointN(int)</span></span> | <span data-ttu-id="e71fc-410">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-410">✔</span></span> | <span data-ttu-id="e71fc-411">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-411">✔</span></span> | <span data-ttu-id="e71fc-412">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-412">✔</span></span> | <span data-ttu-id="e71fc-413">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-413">✔</span></span>
<span data-ttu-id="e71fc-414">LineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="e71fc-414">LineString.IsClosed</span></span> | <span data-ttu-id="e71fc-415">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-415">✔</span></span> | <span data-ttu-id="e71fc-416">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-416">✔</span></span> | <span data-ttu-id="e71fc-417">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-417">✔</span></span> | <span data-ttu-id="e71fc-418">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-418">✔</span></span>
<span data-ttu-id="e71fc-419">LineString. IsRing</span><span class="sxs-lookup"><span data-stu-id="e71fc-419">LineString.IsRing</span></span> | <span data-ttu-id="e71fc-420">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-420">✔</span></span> | | <span data-ttu-id="e71fc-421">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-421">✔</span></span> | <span data-ttu-id="e71fc-422">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-422">✔</span></span>
<span data-ttu-id="e71fc-423">LineString. StartPoint</span><span class="sxs-lookup"><span data-stu-id="e71fc-423">LineString.StartPoint</span></span> | <span data-ttu-id="e71fc-424">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-424">✔</span></span> | <span data-ttu-id="e71fc-425">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-425">✔</span></span> | <span data-ttu-id="e71fc-426">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-426">✔</span></span> | <span data-ttu-id="e71fc-427">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-427">✔</span></span>
<span data-ttu-id="e71fc-428">MultiLineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="e71fc-428">MultiLineString.IsClosed</span></span> | <span data-ttu-id="e71fc-429">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-429">✔</span></span> | <span data-ttu-id="e71fc-430">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-430">✔</span></span> | <span data-ttu-id="e71fc-431">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-431">✔</span></span> | <span data-ttu-id="e71fc-432">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-432">✔</span></span>
<span data-ttu-id="e71fc-433">Point. M</span><span class="sxs-lookup"><span data-stu-id="e71fc-433">Point.M</span></span> | <span data-ttu-id="e71fc-434">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-434">✔</span></span> | <span data-ttu-id="e71fc-435">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-435">✔</span></span> | <span data-ttu-id="e71fc-436">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-436">✔</span></span> | <span data-ttu-id="e71fc-437">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-437">✔</span></span>
<span data-ttu-id="e71fc-438">Point. X</span><span class="sxs-lookup"><span data-stu-id="e71fc-438">Point.X</span></span> | <span data-ttu-id="e71fc-439">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-439">✔</span></span> | <span data-ttu-id="e71fc-440">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-440">✔</span></span> | <span data-ttu-id="e71fc-441">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-441">✔</span></span> | <span data-ttu-id="e71fc-442">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-442">✔</span></span>
<span data-ttu-id="e71fc-443">Point. Y</span><span class="sxs-lookup"><span data-stu-id="e71fc-443">Point.Y</span></span> | <span data-ttu-id="e71fc-444">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-444">✔</span></span> | <span data-ttu-id="e71fc-445">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-445">✔</span></span> | <span data-ttu-id="e71fc-446">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-446">✔</span></span> | <span data-ttu-id="e71fc-447">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-447">✔</span></span>
<span data-ttu-id="e71fc-448">點. Z</span><span class="sxs-lookup"><span data-stu-id="e71fc-448">Point.Z</span></span> | <span data-ttu-id="e71fc-449">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-449">✔</span></span> | <span data-ttu-id="e71fc-450">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-450">✔</span></span> | <span data-ttu-id="e71fc-451">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-451">✔</span></span> | <span data-ttu-id="e71fc-452">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-452">✔</span></span>
<span data-ttu-id="e71fc-453">多邊形。 ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="e71fc-453">Polygon.ExteriorRing</span></span> | <span data-ttu-id="e71fc-454">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-454">✔</span></span> | <span data-ttu-id="e71fc-455">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-455">✔</span></span> | <span data-ttu-id="e71fc-456">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-456">✔</span></span> | <span data-ttu-id="e71fc-457">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-457">✔</span></span>
<span data-ttu-id="e71fc-458">多邊形. GetInteriorRingN （int）</span><span class="sxs-lookup"><span data-stu-id="e71fc-458">Polygon.GetInteriorRingN(int)</span></span> | <span data-ttu-id="e71fc-459">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-459">✔</span></span> | <span data-ttu-id="e71fc-460">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-460">✔</span></span> | <span data-ttu-id="e71fc-461">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-461">✔</span></span> | <span data-ttu-id="e71fc-462">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-462">✔</span></span>
<span data-ttu-id="e71fc-463">多邊形。 NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="e71fc-463">Polygon.NumInteriorRings</span></span> | <span data-ttu-id="e71fc-464">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-464">✔</span></span> | <span data-ttu-id="e71fc-465">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-465">✔</span></span> | <span data-ttu-id="e71fc-466">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-466">✔</span></span> | <span data-ttu-id="e71fc-467">✔</span><span class="sxs-lookup"><span data-stu-id="e71fc-467">✔</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e71fc-468">其他資源</span><span class="sxs-lookup"><span data-stu-id="e71fc-468">Additional resources</span></span>

* [<span data-ttu-id="e71fc-469">SQL Server 中的空間資料</span><span class="sxs-lookup"><span data-stu-id="e71fc-469">Spatial Data in SQL Server</span></span>](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [<span data-ttu-id="e71fc-470">SpatiaLite 首頁</span><span class="sxs-lookup"><span data-stu-id="e71fc-470">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="e71fc-471">Npgsql 空間檔</span><span class="sxs-lookup"><span data-stu-id="e71fc-471">Npgsql Spatial Documentation</span></span>](https://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="e71fc-472">PostGIS 檔</span><span class="sxs-lookup"><span data-stu-id="e71fc-472">PostGIS Documentation</span></span>](https://postgis.net/documentation/)
