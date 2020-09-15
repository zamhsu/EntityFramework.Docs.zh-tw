---
title: 空間資料-EF Core
description: 使用 Entity Framework Core 模型中的空間資料
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
uid: core/modeling/spatial
ms.openlocfilehash: 8c08835f2d6211e6be5852b58b35f003f823bded
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071429"
---
# <a name="spatial-data"></a><span data-ttu-id="b0fd0-103">空間資料</span><span class="sxs-lookup"><span data-stu-id="b0fd0-103">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="b0fd0-104">這項功能已在 EF Core 2.2 中新增。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-104">This feature was added in EF Core 2.2.</span></span>

<span data-ttu-id="b0fd0-105">空間資料代表實體位置和物件的形狀。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-105">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="b0fd0-106">許多資料庫都支援這種類型的資料，因此可與其他資料一起進行編制索引和查詢。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-106">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="b0fd0-107">常見的案例包括從位置查詢指定距離內的物件，或選取其框線包含指定位置的物件。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-107">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="b0fd0-108">EF Core 支援使用 [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) 空間程式庫對應到空間資料類型。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-108">EF Core supports mapping to spatial data types using the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="b0fd0-109">安裝</span><span class="sxs-lookup"><span data-stu-id="b0fd0-109">Installing</span></span>

<span data-ttu-id="b0fd0-110">為了搭配 EF Core 使用空間資料，您需要安裝適當的支援 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-110">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="b0fd0-111">您需要安裝的套件取決於您所使用的提供者。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-111">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="b0fd0-112">EF Core 提供者</span><span class="sxs-lookup"><span data-stu-id="b0fd0-112">EF Core Provider</span></span>                        | <span data-ttu-id="b0fd0-113">空間 NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="b0fd0-113">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="b0fd0-114">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="b0fd0-114">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="b0fd0-115">Microsoft.entityframeworkcore SqlServer. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="b0fd0-115">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="b0fd0-116">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="b0fd0-116">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="b0fd0-117">Microsoft.entityframeworkcore. Sqlite. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="b0fd0-117">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="b0fd0-118">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="b0fd0-118">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="b0fd0-119">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="b0fd0-119">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="b0fd0-120">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="b0fd0-120">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="b0fd0-121">Npgsql. Microsoft.entityframeworkcore. 于 postgresql. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="b0fd0-121">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a><span data-ttu-id="b0fd0-122">反向工程</span><span class="sxs-lookup"><span data-stu-id="b0fd0-122">Reverse engineering</span></span>

<span data-ttu-id="b0fd0-123">空間 NuGet 套件也會啟用具有空間屬性的 [反轉工程](xref:core/managing-schemas/scaffolding) 模型，但您必須先安裝封裝， ***才能*** 執行 `Scaffold-DbContext` 或 `dotnet ef dbcontext scaffold` 。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-123">The spatial NuGet packages also enable [reverse engineering](xref:core/managing-schemas/scaffolding) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="b0fd0-124">如果沒有，您將會收到關於找不到資料行之類型對應的警告，而且將略過資料行。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-124">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="nettopologysuite-nts"></a><span data-ttu-id="b0fd0-125">NetTopologySuite (NTS) </span><span class="sxs-lookup"><span data-stu-id="b0fd0-125">NetTopologySuite (NTS)</span></span>

<span data-ttu-id="b0fd0-126">NetTopologySuite 是適用于 .NET 的空間程式庫。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-126">NetTopologySuite is a spatial library for .NET.</span></span> <span data-ttu-id="b0fd0-127">EF Core 在您的模型中使用 NTS 類型，即可對應至資料庫中的空間資料類型。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-127">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="b0fd0-128">若要透過 NTS 來啟用空間類型的對應，請在提供者的 DbCoNtext options builder 上呼叫 UseNetTopologySuite 方法。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-128">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="b0fd0-129">例如，您可以使用 SQL Server 來呼叫它，如下所示。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-129">For example, with SQL Server you'd call it like this.</span></span>

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

<span data-ttu-id="b0fd0-130">有數種空間資料類型。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-130">There are several spatial data types.</span></span> <span data-ttu-id="b0fd0-131">您所使用的類型取決於您想要允許的圖形類型。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-131">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="b0fd0-132">以下是您可以在模型中用於屬性的 NTS 類型階層。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-132">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="b0fd0-133">它們位於 `NetTopologySuite.Geometries` 命名空間內。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-133">They're located within the `NetTopologySuite.Geometries` namespace.</span></span>

* <span data-ttu-id="b0fd0-134">幾何形狀</span><span class="sxs-lookup"><span data-stu-id="b0fd0-134">Geometry</span></span>
  * <span data-ttu-id="b0fd0-135">Point</span><span class="sxs-lookup"><span data-stu-id="b0fd0-135">Point</span></span>
  * <span data-ttu-id="b0fd0-136">LineString</span><span class="sxs-lookup"><span data-stu-id="b0fd0-136">LineString</span></span>
  * <span data-ttu-id="b0fd0-137">Polygon</span><span class="sxs-lookup"><span data-stu-id="b0fd0-137">Polygon</span></span>
  * <span data-ttu-id="b0fd0-138">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="b0fd0-138">GeometryCollection</span></span>
    * <span data-ttu-id="b0fd0-139">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="b0fd0-139">MultiPoint</span></span>
    * <span data-ttu-id="b0fd0-140">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="b0fd0-140">MultiLineString</span></span>
    * <span data-ttu-id="b0fd0-141">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="b0fd0-141">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="b0fd0-142">NTS 不支援 CircularString、CompoundCurve 和 CurePolygon。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-142">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="b0fd0-143">使用基底 Geometry 型別可讓屬性指定任何類型的圖形。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-143">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

<span data-ttu-id="b0fd0-144">下列實體類別可用來對應至 [Wide World 匯入工具範例資料庫](https://go.microsoft.com/fwlink/?LinkID=800630)中的資料表。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-144">The following entity classes could be used to map to tables in the [Wide World Importers sample database](https://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

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

### <a name="creating-values"></a><span data-ttu-id="b0fd0-145">建立值</span><span class="sxs-lookup"><span data-stu-id="b0fd0-145">Creating values</span></span>

<span data-ttu-id="b0fd0-146">您可以使用函式來建立 geometry 物件;不過，NTS 建議改用 geometry factory。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-146">You can use constructors to create geometry objects; however, NTS recommends using a geometry factory instead.</span></span> <span data-ttu-id="b0fd0-147">這可讓您指定預設 SRID， () 座標所使用的空間參考系統，並可讓您控制更高階的專案，例如) 計算期間所 (的精確度模型以及座標序列 (判斷可) 使用的座標順序（維度和量值）。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-147">This lets you specify a default SRID (the spatial reference system used by the coordinates) and gives you control over more advanced things like the precision model (used during calculations) and the coordinate sequence (determines which ordinates--dimensions and measures--are available).</span></span>

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(new Coordinate(-122.121512, 47.6739882));
```

> [!NOTE]
> <span data-ttu-id="b0fd0-148">4326是指 WGS 84，這是在 GPS 和其他地理系統中使用的標準。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-148">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

### <a name="longitude-and-latitude"></a><span data-ttu-id="b0fd0-149">經度和緯度</span><span class="sxs-lookup"><span data-stu-id="b0fd0-149">Longitude and Latitude</span></span>

<span data-ttu-id="b0fd0-150">NTS 中的座標是以 X 和 Y 值為依據。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-150">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="b0fd0-151">若要表示經度和緯度，請使用 X 作為經度，Y 用於緯度。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-151">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="b0fd0-152">請注意，這 **是** `latitude, longitude` 您通常會看到這些值的格式。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-152">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

### <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="b0fd0-153">用戶端作業期間忽略 SRID</span><span class="sxs-lookup"><span data-stu-id="b0fd0-153">SRID Ignored during client operations</span></span>

<span data-ttu-id="b0fd0-154">NTS 會在作業期間忽略 SRID 值。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-154">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="b0fd0-155">它會假設平面座標系統。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-155">It assumes a planar coordinate system.</span></span> <span data-ttu-id="b0fd0-156">這表示，如果您在經度和緯度方面指定座標，某些用戶端評估值（例如距離、長度和區域）會以度為單位，而非計量。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-156">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="b0fd0-157">如需更有意義的值，您必須先使用 [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) 之類的程式庫，將座標投影至另一個座標系統，然後再計算這些值。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-157">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="b0fd0-158">如果作業是由 EF Core 透過 SQL 評估，則結果的單位將由資料庫決定。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-158">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="b0fd0-159">以下範例使用 ProjNet4GeoAPI 來計算兩個城市之間的距離。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-159">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

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

## <a name="querying-data"></a><span data-ttu-id="b0fd0-160">查詢資料</span><span class="sxs-lookup"><span data-stu-id="b0fd0-160">Querying Data</span></span>

<span data-ttu-id="b0fd0-161">在 LINQ 中，可做為資料庫函數的 NTS 方法和屬性會轉譯為 SQL。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-161">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="b0fd0-162">例如，在下列查詢中，會轉譯距離和包含方法。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-162">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="b0fd0-163">本文結尾處的表格說明各種 EF Core 提供者所支援的成員。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-163">The table at the end of this article shows which members are supported by various EF Core providers.</span></span>

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a><span data-ttu-id="b0fd0-164">SQL Server</span><span class="sxs-lookup"><span data-stu-id="b0fd0-164">SQL Server</span></span>

<span data-ttu-id="b0fd0-165">如果您是使用 SQL Server，還有一些您應該注意的其他事項。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-165">If you're using SQL Server, there are some additional things you should be aware of.</span></span>

### <a name="geography-or-geometry"></a><span data-ttu-id="b0fd0-166">Geography 或 geometry</span><span class="sxs-lookup"><span data-stu-id="b0fd0-166">Geography or geometry</span></span>

<span data-ttu-id="b0fd0-167">根據預設，空間屬性會對應到 `geography` SQL Server 中的資料行。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-167">By default, spatial properties are mapped to `geography` columns in SQL Server.</span></span> <span data-ttu-id="b0fd0-168">若要使用 `geometry` ，請在您的模型中 [設定資料行類型](xref:core/modeling/entity-properties#column-data-types) 。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-168">To use `geometry`, [configure the column type](xref:core/modeling/entity-properties#column-data-types) in your model.</span></span>

### <a name="geography-polygon-rings"></a><span data-ttu-id="b0fd0-169">地理多邊形環形</span><span class="sxs-lookup"><span data-stu-id="b0fd0-169">Geography polygon rings</span></span>

<span data-ttu-id="b0fd0-170">使用資料 `geography` 行類型時，SQL Server 會在外部環形 (或 shell) 和內部環形 (或孔) 強加額外的需求。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-170">When using the `geography` column type, SQL Server imposes additional requirements on the exterior ring (or shell) and interior rings (or holes).</span></span> <span data-ttu-id="b0fd0-171">外部環形必須以逆時針方向和內部環形為方向。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-171">The exterior ring must be oriented counterclockwise and the interior rings clockwise.</span></span> <span data-ttu-id="b0fd0-172">NTS 會在將值傳送至資料庫之前進行驗證。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-172">NTS validates this before sending values to the database.</span></span>

### <a name="fullglobe"></a><span data-ttu-id="b0fd0-173">FullGlobe</span><span class="sxs-lookup"><span data-stu-id="b0fd0-173">FullGlobe</span></span>

<span data-ttu-id="b0fd0-174">使用資料行類型時，SQL Server 具有非標準幾何類型來代表完整的地球 `geography` 。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-174">SQL Server has a non-standard geometry type to represent the full globe when using the `geography` column type.</span></span> <span data-ttu-id="b0fd0-175">它也有一種方法，可根據完整的地球 (來表示多邊形，而不需要) 的外部環形。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-175">It also has a way to represent polygons based on the full globe (without an exterior ring).</span></span> <span data-ttu-id="b0fd0-176">NTS 不支援這兩種方法。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-176">Neither of these are supported by NTS.</span></span>

> [!WARNING]
> <span data-ttu-id="b0fd0-177">NTS 不支援以 FullGlobe 為基礎的多邊形和多邊形。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-177">FullGlobe and polygons based on it aren't supported by NTS.</span></span>

## <a name="sqlite"></a><span data-ttu-id="b0fd0-178">SQLite</span><span class="sxs-lookup"><span data-stu-id="b0fd0-178">SQLite</span></span>

<span data-ttu-id="b0fd0-179">以下是使用 SQLite 的一些其他資訊。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-179">Here is some additional information for those using SQLite.</span></span>

### <a name="installing-spatialite"></a><span data-ttu-id="b0fd0-180">安裝 SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="b0fd0-180">Installing SpatiaLite</span></span>

<span data-ttu-id="b0fd0-181">在 Windows 上，原生 mod_spatialite 程式庫會以 NuGet 套件相依性的形式散發。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-181">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="b0fd0-182">其他平臺則需要另外安裝。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-182">Other platforms need to install it separately.</span></span> <span data-ttu-id="b0fd0-183">這通常是使用軟體套件管理員來完成。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-183">This is typically done using a software package manager.</span></span> <span data-ttu-id="b0fd0-184">例如，您可以在 Ubuntu 上使用 APT，並在 MacOS 上使用 Homebrew。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-184">For example, you can use APT on Ubuntu and Homebrew on MacOS.</span></span>

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

<span data-ttu-id="b0fd0-185">可惜的是，較新版本的 PROJ (SpatiaLite) 的相依性與 EF 的預設 [SQLitePCLRaw](/dotnet/standard/data/sqlite/custom-versions#bundles)組合不相容。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-185">Unfortunately, newer versions of PROJ (a dependency of SpatiaLite) are incompatible with EF's default [SQLitePCLRaw bundle](/dotnet/standard/data/sqlite/custom-versions#bundles).</span></span> <span data-ttu-id="b0fd0-186">若要解決此問題，您可以建立使用系統 SQLite 程式庫的自訂 [SQLitePCLRaw 提供者](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) ，也可以安裝 SpatiaLite 停用 PROJ 支援的自訂群組建。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-186">You can work around this by either creating a custom [SQLitePCLRaw provider](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) that uses the system SQLite library, or you can install a custom build of SpatiaLite disabling PROJ support.</span></span>

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

### <a name="configuring-srid"></a><span data-ttu-id="b0fd0-187">設定 SRID</span><span class="sxs-lookup"><span data-stu-id="b0fd0-187">Configuring SRID</span></span>

<span data-ttu-id="b0fd0-188">在 SpatiaLite 中，資料行需要指定每個資料行的 SRID。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-188">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="b0fd0-189">預設 SRID 為 `0` 。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-189">The default SRID is `0`.</span></span> <span data-ttu-id="b0fd0-190">使用 ForSqliteHasSrid 方法指定不同的 SRID。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-190">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a><span data-ttu-id="b0fd0-191">尺寸</span><span class="sxs-lookup"><span data-stu-id="b0fd0-191">Dimension</span></span>

<span data-ttu-id="b0fd0-192">類似于 SRID，資料行的維度 (或座標) 也會指定為數據行的一部分。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-192">Similar to SRID, a column's dimension (or ordinates) is also specified as part of the column.</span></span> <span data-ttu-id="b0fd0-193">預設的座標是 X 和 Y。請使用 ForSqliteHasDimension 方法來啟用其他座標 (Z 和 M) 。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-193">The default ordinates are X and Y. Enable additional ordinates (Z and M) using the ForSqliteHasDimension method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a><span data-ttu-id="b0fd0-194">轉譯的作業</span><span class="sxs-lookup"><span data-stu-id="b0fd0-194">Translated Operations</span></span>

<span data-ttu-id="b0fd0-195">下表顯示每個 EF Core 提供者將哪些 NTS 成員轉譯成 SQL。</span><span class="sxs-lookup"><span data-stu-id="b0fd0-195">This table shows which NTS members are translated into SQL by each EF Core provider.</span></span>

<span data-ttu-id="b0fd0-196">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="b0fd0-196">NetTopologySuite</span></span> | <span data-ttu-id="b0fd0-197">SQL Server (幾何) </span><span class="sxs-lookup"><span data-stu-id="b0fd0-197">SQL Server (geometry)</span></span> | <span data-ttu-id="b0fd0-198">SQL Server (geography) </span><span class="sxs-lookup"><span data-stu-id="b0fd0-198">SQL Server (geography)</span></span> | <span data-ttu-id="b0fd0-199">SQLite</span><span class="sxs-lookup"><span data-stu-id="b0fd0-199">SQLite</span></span> | <span data-ttu-id="b0fd0-200">Npgsql</span><span class="sxs-lookup"><span data-stu-id="b0fd0-200">Npgsql</span></span>
--- |:---:|:---:|:---:|:---:
<span data-ttu-id="b0fd0-201">幾何. 區域</span><span class="sxs-lookup"><span data-stu-id="b0fd0-201">Geometry.Area</span></span> | <span data-ttu-id="b0fd0-202">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-202">✔</span></span> | <span data-ttu-id="b0fd0-203">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-203">✔</span></span> | <span data-ttu-id="b0fd0-204">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-204">✔</span></span> | <span data-ttu-id="b0fd0-205">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-205">✔</span></span>
<span data-ttu-id="b0fd0-206">AsBinary ( # A1</span><span class="sxs-lookup"><span data-stu-id="b0fd0-206">Geometry.AsBinary()</span></span> | <span data-ttu-id="b0fd0-207">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-207">✔</span></span> | <span data-ttu-id="b0fd0-208">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-208">✔</span></span> | <span data-ttu-id="b0fd0-209">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-209">✔</span></span> | <span data-ttu-id="b0fd0-210">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-210">✔</span></span>
<span data-ttu-id="b0fd0-211">AsText ( # A1</span><span class="sxs-lookup"><span data-stu-id="b0fd0-211">Geometry.AsText()</span></span> | <span data-ttu-id="b0fd0-212">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-212">✔</span></span> | <span data-ttu-id="b0fd0-213">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-213">✔</span></span> | <span data-ttu-id="b0fd0-214">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-214">✔</span></span> | <span data-ttu-id="b0fd0-215">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-215">✔</span></span>
<span data-ttu-id="b0fd0-216">Geometry</span><span class="sxs-lookup"><span data-stu-id="b0fd0-216">Geometry.Boundary</span></span> | <span data-ttu-id="b0fd0-217">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-217">✔</span></span> | | <span data-ttu-id="b0fd0-218">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-218">✔</span></span> | <span data-ttu-id="b0fd0-219">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-219">✔</span></span>
<span data-ttu-id="b0fd0-220"> (雙重) 的 Geometry 緩衝區</span><span class="sxs-lookup"><span data-stu-id="b0fd0-220">Geometry.Buffer(double)</span></span> | <span data-ttu-id="b0fd0-221">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-221">✔</span></span> | <span data-ttu-id="b0fd0-222">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-222">✔</span></span> | <span data-ttu-id="b0fd0-223">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-223">✔</span></span> | <span data-ttu-id="b0fd0-224">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-224">✔</span></span>
<span data-ttu-id="b0fd0-225">Geometry (double、int) </span><span class="sxs-lookup"><span data-stu-id="b0fd0-225">Geometry.Buffer(double, int)</span></span> | | | <span data-ttu-id="b0fd0-226">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-226">✔</span></span> | <span data-ttu-id="b0fd0-227">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-227">✔</span></span>
<span data-ttu-id="b0fd0-228">距心</span><span class="sxs-lookup"><span data-stu-id="b0fd0-228">Geometry.Centroid</span></span> | <span data-ttu-id="b0fd0-229">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-229">✔</span></span> | | <span data-ttu-id="b0fd0-230">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-230">✔</span></span> | <span data-ttu-id="b0fd0-231">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-231">✔</span></span>
<span data-ttu-id="b0fd0-232">Geometry。包含 (幾何) </span><span class="sxs-lookup"><span data-stu-id="b0fd0-232">Geometry.Contains(Geometry)</span></span> | <span data-ttu-id="b0fd0-233">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-233">✔</span></span> | <span data-ttu-id="b0fd0-234">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-234">✔</span></span> | <span data-ttu-id="b0fd0-235">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-235">✔</span></span> | <span data-ttu-id="b0fd0-236">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-236">✔</span></span>
<span data-ttu-id="b0fd0-237">ConvexHull ( # A1</span><span class="sxs-lookup"><span data-stu-id="b0fd0-237">Geometry.ConvexHull()</span></span> | <span data-ttu-id="b0fd0-238">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-238">✔</span></span> | <span data-ttu-id="b0fd0-239">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-239">✔</span></span> | <span data-ttu-id="b0fd0-240">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-240">✔</span></span> | <span data-ttu-id="b0fd0-241">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-241">✔</span></span>
<span data-ttu-id="b0fd0-242">CoveredBy (Geometry) </span><span class="sxs-lookup"><span data-stu-id="b0fd0-242">Geometry.CoveredBy(Geometry)</span></span> | | | <span data-ttu-id="b0fd0-243">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-243">✔</span></span> | <span data-ttu-id="b0fd0-244">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-244">✔</span></span>
<span data-ttu-id="b0fd0-245">Geometry。涵蓋 (幾何) </span><span class="sxs-lookup"><span data-stu-id="b0fd0-245">Geometry.Covers(Geometry)</span></span> | | | <span data-ttu-id="b0fd0-246">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-246">✔</span></span> | <span data-ttu-id="b0fd0-247">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-247">✔</span></span>
<span data-ttu-id="b0fd0-248">幾何. (幾何的交叉) </span><span class="sxs-lookup"><span data-stu-id="b0fd0-248">Geometry.Crosses(Geometry)</span></span> | <span data-ttu-id="b0fd0-249">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-249">✔</span></span> | | <span data-ttu-id="b0fd0-250">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-250">✔</span></span> | <span data-ttu-id="b0fd0-251">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-251">✔</span></span>
<span data-ttu-id="b0fd0-252">Geometry。差異 (幾何) </span><span class="sxs-lookup"><span data-stu-id="b0fd0-252">Geometry.Difference(Geometry)</span></span> | <span data-ttu-id="b0fd0-253">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-253">✔</span></span> | <span data-ttu-id="b0fd0-254">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-254">✔</span></span> | <span data-ttu-id="b0fd0-255">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-255">✔</span></span> | <span data-ttu-id="b0fd0-256">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-256">✔</span></span>
<span data-ttu-id="b0fd0-257">Geometry. Dimension</span><span class="sxs-lookup"><span data-stu-id="b0fd0-257">Geometry.Dimension</span></span> | <span data-ttu-id="b0fd0-258">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-258">✔</span></span> | <span data-ttu-id="b0fd0-259">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-259">✔</span></span> | <span data-ttu-id="b0fd0-260">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-260">✔</span></span> | <span data-ttu-id="b0fd0-261">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-261">✔</span></span>
<span data-ttu-id="b0fd0-262">Geometry：非交集的 (幾何) </span><span class="sxs-lookup"><span data-stu-id="b0fd0-262">Geometry.Disjoint(Geometry)</span></span> | <span data-ttu-id="b0fd0-263">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-263">✔</span></span> | <span data-ttu-id="b0fd0-264">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-264">✔</span></span> | <span data-ttu-id="b0fd0-265">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-265">✔</span></span> | <span data-ttu-id="b0fd0-266">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-266">✔</span></span>
<span data-ttu-id="b0fd0-267">Geometry (幾何) </span><span class="sxs-lookup"><span data-stu-id="b0fd0-267">Geometry.Distance(Geometry)</span></span> | <span data-ttu-id="b0fd0-268">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-268">✔</span></span> | <span data-ttu-id="b0fd0-269">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-269">✔</span></span> | <span data-ttu-id="b0fd0-270">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-270">✔</span></span> | <span data-ttu-id="b0fd0-271">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-271">✔</span></span>
<span data-ttu-id="b0fd0-272">Geometry。信封</span><span class="sxs-lookup"><span data-stu-id="b0fd0-272">Geometry.Envelope</span></span> | <span data-ttu-id="b0fd0-273">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-273">✔</span></span> | | <span data-ttu-id="b0fd0-274">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-274">✔</span></span> | <span data-ttu-id="b0fd0-275">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-275">✔</span></span>
<span data-ttu-id="b0fd0-276">EqualsExact (Geometry) </span><span class="sxs-lookup"><span data-stu-id="b0fd0-276">Geometry.EqualsExact(Geometry)</span></span> | | | | <span data-ttu-id="b0fd0-277">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-277">✔</span></span>
<span data-ttu-id="b0fd0-278">EqualsTopologically (Geometry) </span><span class="sxs-lookup"><span data-stu-id="b0fd0-278">Geometry.EqualsTopologically(Geometry)</span></span> | <span data-ttu-id="b0fd0-279">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-279">✔</span></span> | <span data-ttu-id="b0fd0-280">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-280">✔</span></span> | <span data-ttu-id="b0fd0-281">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-281">✔</span></span> | <span data-ttu-id="b0fd0-282">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-282">✔</span></span>
<span data-ttu-id="b0fd0-283">GeometryType</span><span class="sxs-lookup"><span data-stu-id="b0fd0-283">Geometry.GeometryType</span></span> | <span data-ttu-id="b0fd0-284">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-284">✔</span></span> | <span data-ttu-id="b0fd0-285">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-285">✔</span></span> | <span data-ttu-id="b0fd0-286">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-286">✔</span></span> | <span data-ttu-id="b0fd0-287">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-287">✔</span></span>
<span data-ttu-id="b0fd0-288">GetGeometryN (int) </span><span class="sxs-lookup"><span data-stu-id="b0fd0-288">Geometry.GetGeometryN(int)</span></span> | <span data-ttu-id="b0fd0-289">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-289">✔</span></span> | | <span data-ttu-id="b0fd0-290">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-290">✔</span></span> | <span data-ttu-id="b0fd0-291">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-291">✔</span></span>
<span data-ttu-id="b0fd0-292">InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="b0fd0-292">Geometry.InteriorPoint</span></span> | <span data-ttu-id="b0fd0-293">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-293">✔</span></span> | | <span data-ttu-id="b0fd0-294">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-294">✔</span></span> | <span data-ttu-id="b0fd0-295">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-295">✔</span></span>
<span data-ttu-id="b0fd0-296">Geometry (幾何的交集) </span><span class="sxs-lookup"><span data-stu-id="b0fd0-296">Geometry.Intersection(Geometry)</span></span> | <span data-ttu-id="b0fd0-297">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-297">✔</span></span> | <span data-ttu-id="b0fd0-298">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-298">✔</span></span> | <span data-ttu-id="b0fd0-299">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-299">✔</span></span> | <span data-ttu-id="b0fd0-300">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-300">✔</span></span>
<span data-ttu-id="b0fd0-301">Geometry (幾何的交集) </span><span class="sxs-lookup"><span data-stu-id="b0fd0-301">Geometry.Intersects(Geometry)</span></span> | <span data-ttu-id="b0fd0-302">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-302">✔</span></span> | <span data-ttu-id="b0fd0-303">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-303">✔</span></span> | <span data-ttu-id="b0fd0-304">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-304">✔</span></span> | <span data-ttu-id="b0fd0-305">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-305">✔</span></span>
<span data-ttu-id="b0fd0-306">IsEmpty</span><span class="sxs-lookup"><span data-stu-id="b0fd0-306">Geometry.IsEmpty</span></span> | <span data-ttu-id="b0fd0-307">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-307">✔</span></span> | <span data-ttu-id="b0fd0-308">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-308">✔</span></span> | <span data-ttu-id="b0fd0-309">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-309">✔</span></span> | <span data-ttu-id="b0fd0-310">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-310">✔</span></span>
<span data-ttu-id="b0fd0-311">IsSimple</span><span class="sxs-lookup"><span data-stu-id="b0fd0-311">Geometry.IsSimple</span></span> | <span data-ttu-id="b0fd0-312">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-312">✔</span></span> | | <span data-ttu-id="b0fd0-313">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-313">✔</span></span> | <span data-ttu-id="b0fd0-314">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-314">✔</span></span>
<span data-ttu-id="b0fd0-315">Geometry. IsValid</span><span class="sxs-lookup"><span data-stu-id="b0fd0-315">Geometry.IsValid</span></span> | <span data-ttu-id="b0fd0-316">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-316">✔</span></span> | <span data-ttu-id="b0fd0-317">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-317">✔</span></span> | <span data-ttu-id="b0fd0-318">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-318">✔</span></span> | <span data-ttu-id="b0fd0-319">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-319">✔</span></span>
<span data-ttu-id="b0fd0-320">IsWithinDistance (Geometry，double) </span><span class="sxs-lookup"><span data-stu-id="b0fd0-320">Geometry.IsWithinDistance(Geometry, double)</span></span> | <span data-ttu-id="b0fd0-321">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-321">✔</span></span> | | <span data-ttu-id="b0fd0-322">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-322">✔</span></span> | <span data-ttu-id="b0fd0-323">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-323">✔</span></span>
<span data-ttu-id="b0fd0-324">Geometry。長度</span><span class="sxs-lookup"><span data-stu-id="b0fd0-324">Geometry.Length</span></span> | <span data-ttu-id="b0fd0-325">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-325">✔</span></span> | <span data-ttu-id="b0fd0-326">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-326">✔</span></span> | <span data-ttu-id="b0fd0-327">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-327">✔</span></span> | <span data-ttu-id="b0fd0-328">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-328">✔</span></span>
<span data-ttu-id="b0fd0-329">NumGeometries</span><span class="sxs-lookup"><span data-stu-id="b0fd0-329">Geometry.NumGeometries</span></span> | <span data-ttu-id="b0fd0-330">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-330">✔</span></span> | <span data-ttu-id="b0fd0-331">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-331">✔</span></span> | <span data-ttu-id="b0fd0-332">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-332">✔</span></span> | <span data-ttu-id="b0fd0-333">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-333">✔</span></span>
<span data-ttu-id="b0fd0-334">X.numpoints</span><span class="sxs-lookup"><span data-stu-id="b0fd0-334">Geometry.NumPoints</span></span> | <span data-ttu-id="b0fd0-335">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-335">✔</span></span> | <span data-ttu-id="b0fd0-336">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-336">✔</span></span> | <span data-ttu-id="b0fd0-337">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-337">✔</span></span> | <span data-ttu-id="b0fd0-338">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-338">✔</span></span>
<span data-ttu-id="b0fd0-339">OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="b0fd0-339">Geometry.OgcGeometryType</span></span> | <span data-ttu-id="b0fd0-340">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-340">✔</span></span> | <span data-ttu-id="b0fd0-341">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-341">✔</span></span> | <span data-ttu-id="b0fd0-342">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-342">✔</span></span> | <span data-ttu-id="b0fd0-343">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-343">✔</span></span>
<span data-ttu-id="b0fd0-344">Geometry (幾何的重迭) </span><span class="sxs-lookup"><span data-stu-id="b0fd0-344">Geometry.Overlaps(Geometry)</span></span> | <span data-ttu-id="b0fd0-345">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-345">✔</span></span> | <span data-ttu-id="b0fd0-346">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-346">✔</span></span> | <span data-ttu-id="b0fd0-347">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-347">✔</span></span> | <span data-ttu-id="b0fd0-348">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-348">✔</span></span>
<span data-ttu-id="b0fd0-349">PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="b0fd0-349">Geometry.PointOnSurface</span></span> | <span data-ttu-id="b0fd0-350">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-350">✔</span></span> | | <span data-ttu-id="b0fd0-351">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-351">✔</span></span> | <span data-ttu-id="b0fd0-352">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-352">✔</span></span>
<span data-ttu-id="b0fd0-353">Geometry。 (Geometry、string) 的關聯</span><span class="sxs-lookup"><span data-stu-id="b0fd0-353">Geometry.Relate(Geometry, string)</span></span> | <span data-ttu-id="b0fd0-354">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-354">✔</span></span> | | <span data-ttu-id="b0fd0-355">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-355">✔</span></span> | <span data-ttu-id="b0fd0-356">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-356">✔</span></span>
<span data-ttu-id="b0fd0-357">Geometry。反向 ( # A1</span><span class="sxs-lookup"><span data-stu-id="b0fd0-357">Geometry.Reverse()</span></span> | | | <span data-ttu-id="b0fd0-358">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-358">✔</span></span> | <span data-ttu-id="b0fd0-359">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-359">✔</span></span>
<span data-ttu-id="b0fd0-360">SRID</span><span class="sxs-lookup"><span data-stu-id="b0fd0-360">Geometry.SRID</span></span> | <span data-ttu-id="b0fd0-361">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-361">✔</span></span> | <span data-ttu-id="b0fd0-362">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-362">✔</span></span> | <span data-ttu-id="b0fd0-363">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-363">✔</span></span> | <span data-ttu-id="b0fd0-364">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-364">✔</span></span>
<span data-ttu-id="b0fd0-365">SymmetricDifference (Geometry) </span><span class="sxs-lookup"><span data-stu-id="b0fd0-365">Geometry.SymmetricDifference(Geometry)</span></span> | <span data-ttu-id="b0fd0-366">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-366">✔</span></span> | <span data-ttu-id="b0fd0-367">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-367">✔</span></span> | <span data-ttu-id="b0fd0-368">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-368">✔</span></span> | <span data-ttu-id="b0fd0-369">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-369">✔</span></span>
<span data-ttu-id="b0fd0-370">ToBinary ( # A1</span><span class="sxs-lookup"><span data-stu-id="b0fd0-370">Geometry.ToBinary()</span></span> | <span data-ttu-id="b0fd0-371">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-371">✔</span></span> | <span data-ttu-id="b0fd0-372">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-372">✔</span></span> | <span data-ttu-id="b0fd0-373">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-373">✔</span></span> | <span data-ttu-id="b0fd0-374">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-374">✔</span></span>
<span data-ttu-id="b0fd0-375">Duration.totext ( # A1</span><span class="sxs-lookup"><span data-stu-id="b0fd0-375">Geometry.ToText()</span></span> | <span data-ttu-id="b0fd0-376">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-376">✔</span></span> | <span data-ttu-id="b0fd0-377">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-377">✔</span></span> | <span data-ttu-id="b0fd0-378">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-378">✔</span></span> | <span data-ttu-id="b0fd0-379">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-379">✔</span></span>
<span data-ttu-id="b0fd0-380">Geometry (幾何的修飾) </span><span class="sxs-lookup"><span data-stu-id="b0fd0-380">Geometry.Touches(Geometry)</span></span> | <span data-ttu-id="b0fd0-381">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-381">✔</span></span> | | <span data-ttu-id="b0fd0-382">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-382">✔</span></span> | <span data-ttu-id="b0fd0-383">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-383">✔</span></span>
<span data-ttu-id="b0fd0-384">Geometry ( # A1</span><span class="sxs-lookup"><span data-stu-id="b0fd0-384">Geometry.Union()</span></span> | | | <span data-ttu-id="b0fd0-385">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-385">✔</span></span> | <span data-ttu-id="b0fd0-386">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-386">✔</span></span>
<span data-ttu-id="b0fd0-387">Geometry) 的聯集 (</span><span class="sxs-lookup"><span data-stu-id="b0fd0-387">Geometry.Union(Geometry)</span></span> | <span data-ttu-id="b0fd0-388">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-388">✔</span></span> | <span data-ttu-id="b0fd0-389">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-389">✔</span></span> | <span data-ttu-id="b0fd0-390">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-390">✔</span></span> | <span data-ttu-id="b0fd0-391">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-391">✔</span></span>
<span data-ttu-id="b0fd0-392">幾何。在 (幾何內) </span><span class="sxs-lookup"><span data-stu-id="b0fd0-392">Geometry.Within(Geometry)</span></span> | <span data-ttu-id="b0fd0-393">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-393">✔</span></span> | <span data-ttu-id="b0fd0-394">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-394">✔</span></span> | <span data-ttu-id="b0fd0-395">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-395">✔</span></span> | <span data-ttu-id="b0fd0-396">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-396">✔</span></span>
<span data-ttu-id="b0fd0-397">>geometrycollection 計數</span><span class="sxs-lookup"><span data-stu-id="b0fd0-397">GeometryCollection.Count</span></span> | <span data-ttu-id="b0fd0-398">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-398">✔</span></span> | <span data-ttu-id="b0fd0-399">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-399">✔</span></span> | <span data-ttu-id="b0fd0-400">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-400">✔</span></span> | <span data-ttu-id="b0fd0-401">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-401">✔</span></span>
<span data-ttu-id="b0fd0-402">>geometrycollection [int]</span><span class="sxs-lookup"><span data-stu-id="b0fd0-402">GeometryCollection[int]</span></span> | <span data-ttu-id="b0fd0-403">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-403">✔</span></span> | <span data-ttu-id="b0fd0-404">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-404">✔</span></span> | <span data-ttu-id="b0fd0-405">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-405">✔</span></span> | <span data-ttu-id="b0fd0-406">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-406">✔</span></span>
<span data-ttu-id="b0fd0-407">LineString 計數</span><span class="sxs-lookup"><span data-stu-id="b0fd0-407">LineString.Count</span></span> | <span data-ttu-id="b0fd0-408">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-408">✔</span></span> | <span data-ttu-id="b0fd0-409">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-409">✔</span></span> | <span data-ttu-id="b0fd0-410">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-410">✔</span></span> | <span data-ttu-id="b0fd0-411">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-411">✔</span></span>
<span data-ttu-id="b0fd0-412">LineString 端點</span><span class="sxs-lookup"><span data-stu-id="b0fd0-412">LineString.EndPoint</span></span> | <span data-ttu-id="b0fd0-413">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-413">✔</span></span> | <span data-ttu-id="b0fd0-414">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-414">✔</span></span> | <span data-ttu-id="b0fd0-415">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-415">✔</span></span> | <span data-ttu-id="b0fd0-416">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-416">✔</span></span>
<span data-ttu-id="b0fd0-417">LineString. GetPointN (int) </span><span class="sxs-lookup"><span data-stu-id="b0fd0-417">LineString.GetPointN(int)</span></span> | <span data-ttu-id="b0fd0-418">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-418">✔</span></span> | <span data-ttu-id="b0fd0-419">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-419">✔</span></span> | <span data-ttu-id="b0fd0-420">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-420">✔</span></span> | <span data-ttu-id="b0fd0-421">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-421">✔</span></span>
<span data-ttu-id="b0fd0-422">LineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="b0fd0-422">LineString.IsClosed</span></span> | <span data-ttu-id="b0fd0-423">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-423">✔</span></span> | <span data-ttu-id="b0fd0-424">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-424">✔</span></span> | <span data-ttu-id="b0fd0-425">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-425">✔</span></span> | <span data-ttu-id="b0fd0-426">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-426">✔</span></span>
<span data-ttu-id="b0fd0-427">LineString. IsRing</span><span class="sxs-lookup"><span data-stu-id="b0fd0-427">LineString.IsRing</span></span> | <span data-ttu-id="b0fd0-428">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-428">✔</span></span> | | <span data-ttu-id="b0fd0-429">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-429">✔</span></span> | <span data-ttu-id="b0fd0-430">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-430">✔</span></span>
<span data-ttu-id="b0fd0-431">LineString. StartPoint</span><span class="sxs-lookup"><span data-stu-id="b0fd0-431">LineString.StartPoint</span></span> | <span data-ttu-id="b0fd0-432">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-432">✔</span></span> | <span data-ttu-id="b0fd0-433">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-433">✔</span></span> | <span data-ttu-id="b0fd0-434">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-434">✔</span></span> | <span data-ttu-id="b0fd0-435">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-435">✔</span></span>
<span data-ttu-id="b0fd0-436">MultiLineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="b0fd0-436">MultiLineString.IsClosed</span></span> | <span data-ttu-id="b0fd0-437">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-437">✔</span></span> | <span data-ttu-id="b0fd0-438">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-438">✔</span></span> | <span data-ttu-id="b0fd0-439">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-439">✔</span></span> | <span data-ttu-id="b0fd0-440">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-440">✔</span></span>
<span data-ttu-id="b0fd0-441">點. M</span><span class="sxs-lookup"><span data-stu-id="b0fd0-441">Point.M</span></span> | <span data-ttu-id="b0fd0-442">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-442">✔</span></span> | <span data-ttu-id="b0fd0-443">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-443">✔</span></span> | <span data-ttu-id="b0fd0-444">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-444">✔</span></span> | <span data-ttu-id="b0fd0-445">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-445">✔</span></span>
<span data-ttu-id="b0fd0-446">Point. X</span><span class="sxs-lookup"><span data-stu-id="b0fd0-446">Point.X</span></span> | <span data-ttu-id="b0fd0-447">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-447">✔</span></span> | <span data-ttu-id="b0fd0-448">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-448">✔</span></span> | <span data-ttu-id="b0fd0-449">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-449">✔</span></span> | <span data-ttu-id="b0fd0-450">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-450">✔</span></span>
<span data-ttu-id="b0fd0-451">Point. Y</span><span class="sxs-lookup"><span data-stu-id="b0fd0-451">Point.Y</span></span> | <span data-ttu-id="b0fd0-452">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-452">✔</span></span> | <span data-ttu-id="b0fd0-453">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-453">✔</span></span> | <span data-ttu-id="b0fd0-454">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-454">✔</span></span> | <span data-ttu-id="b0fd0-455">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-455">✔</span></span>
<span data-ttu-id="b0fd0-456">點 Z</span><span class="sxs-lookup"><span data-stu-id="b0fd0-456">Point.Z</span></span> | <span data-ttu-id="b0fd0-457">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-457">✔</span></span> | <span data-ttu-id="b0fd0-458">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-458">✔</span></span> | <span data-ttu-id="b0fd0-459">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-459">✔</span></span> | <span data-ttu-id="b0fd0-460">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-460">✔</span></span>
<span data-ttu-id="b0fd0-461">多邊形. ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="b0fd0-461">Polygon.ExteriorRing</span></span> | <span data-ttu-id="b0fd0-462">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-462">✔</span></span> | <span data-ttu-id="b0fd0-463">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-463">✔</span></span> | <span data-ttu-id="b0fd0-464">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-464">✔</span></span> | <span data-ttu-id="b0fd0-465">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-465">✔</span></span>
<span data-ttu-id="b0fd0-466">GetInteriorRingN (int) </span><span class="sxs-lookup"><span data-stu-id="b0fd0-466">Polygon.GetInteriorRingN(int)</span></span> | <span data-ttu-id="b0fd0-467">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-467">✔</span></span> | <span data-ttu-id="b0fd0-468">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-468">✔</span></span> | <span data-ttu-id="b0fd0-469">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-469">✔</span></span> | <span data-ttu-id="b0fd0-470">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-470">✔</span></span>
<span data-ttu-id="b0fd0-471">多邊形. NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="b0fd0-471">Polygon.NumInteriorRings</span></span> | <span data-ttu-id="b0fd0-472">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-472">✔</span></span> | <span data-ttu-id="b0fd0-473">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-473">✔</span></span> | <span data-ttu-id="b0fd0-474">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-474">✔</span></span> | <span data-ttu-id="b0fd0-475">✔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-475">✔</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b0fd0-476">其他資源</span><span class="sxs-lookup"><span data-stu-id="b0fd0-476">Additional resources</span></span>

* [<span data-ttu-id="b0fd0-477">SQL Server 中的空間資料</span><span class="sxs-lookup"><span data-stu-id="b0fd0-477">Spatial Data in SQL Server</span></span>](/sql/relational-databases/spatial/spatial-data-sql-server)
* [<span data-ttu-id="b0fd0-478">SpatiaLite 首頁</span><span class="sxs-lookup"><span data-stu-id="b0fd0-478">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="b0fd0-479">Npgsql 空間檔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-479">Npgsql Spatial Documentation</span></span>](https://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="b0fd0-480">PostGIS 檔</span><span class="sxs-lookup"><span data-stu-id="b0fd0-480">PostGIS Documentation</span></span>](https://postgis.net/documentation/)
