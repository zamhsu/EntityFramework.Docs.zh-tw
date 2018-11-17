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
# <a name="spatial-data"></a><span data-ttu-id="b62ac-102">空間資料</span><span class="sxs-lookup"><span data-stu-id="b62ac-102">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="b62ac-103">這項功能是在 EF Core 2.2 的新功能。</span><span class="sxs-lookup"><span data-stu-id="b62ac-103">This feature is new in EF Core 2.2.</span></span>

<span data-ttu-id="b62ac-104">空間資料代表的實體位置和物件的形狀。</span><span class="sxs-lookup"><span data-stu-id="b62ac-104">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="b62ac-105">多個資料庫提供支援這種類型的資料，因此可以編製索引和查詢及其他資料。</span><span class="sxs-lookup"><span data-stu-id="b62ac-105">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="b62ac-106">常見的案例包括查詢的位置，從指定距離內的物件，或選取的物件，其框線包含指定的位置。</span><span class="sxs-lookup"><span data-stu-id="b62ac-106">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="b62ac-107">EF Core 支援使用的空間資料類型對應[NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite)空間程式庫。</span><span class="sxs-lookup"><span data-stu-id="b62ac-107">EF Core supports mapping to spatial data types using the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="b62ac-108">安裝</span><span class="sxs-lookup"><span data-stu-id="b62ac-108">Installing</span></span>

<span data-ttu-id="b62ac-109">若要使用的空間資料與 EF Core，您需要安裝適當的支援 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="b62ac-109">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="b62ac-110">您需要安裝哪些套件取決於您使用的提供者。</span><span class="sxs-lookup"><span data-stu-id="b62ac-110">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="b62ac-111">EF Core 提供者</span><span class="sxs-lookup"><span data-stu-id="b62ac-111">EF Core Provider</span></span>                        | <span data-ttu-id="b62ac-112">空間的 NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="b62ac-112">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="b62ac-113">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="b62ac-113">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="b62ac-114">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="b62ac-114">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="b62ac-115">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="b62ac-115">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="b62ac-116">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="b62ac-116">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="b62ac-117">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="b62ac-117">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="b62ac-118">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="b62ac-118">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="b62ac-119">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="b62ac-119">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="b62ac-120">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="b62ac-120">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a><span data-ttu-id="b62ac-121">反向工程</span><span class="sxs-lookup"><span data-stu-id="b62ac-121">Reverse engineering</span></span>

<span data-ttu-id="b62ac-122">空間的 NuGet 封裝也啟用[反向工程](../managing-schemas/scaffolding.md)模型空間的屬性，但您需要安裝套件***之前***執行`Scaffold-DbContext`或`dotnet ef dbcontext scaffold`。</span><span class="sxs-lookup"><span data-stu-id="b62ac-122">The spatial NuGet packages also enable [reverse engineering](../managing-schemas/scaffolding.md) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="b62ac-123">如果不這麼做，您會收到有關未針對資料行中尋找型別對應的警告，並將略過資料行。</span><span class="sxs-lookup"><span data-stu-id="b62ac-123">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="nettopologysuite-nts"></a><span data-ttu-id="b62ac-124">NetTopologySuite (NTS)</span><span class="sxs-lookup"><span data-stu-id="b62ac-124">NetTopologySuite (NTS)</span></span>

<span data-ttu-id="b62ac-125">NetTopologySuite 是適用於.NET 的空間程式庫。</span><span class="sxs-lookup"><span data-stu-id="b62ac-125">NetTopologySuite is a spatial library for .NET.</span></span> <span data-ttu-id="b62ac-126">EF Core 可讓空間資料類型對應資料庫中藉由在您的模型中使用 NTS 類型。</span><span class="sxs-lookup"><span data-stu-id="b62ac-126">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="b62ac-127">若要啟用透過 NTS 空間類型的對應，請在提供者的 DbContext 選項產生器呼叫 UseNetTopologySuite; 方法。</span><span class="sxs-lookup"><span data-stu-id="b62ac-127">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="b62ac-128">比方說，SQL Server 與您會呼叫它像這樣。</span><span class="sxs-lookup"><span data-stu-id="b62ac-128">For example, with SQL Server you'd call it like this.</span></span>

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

<span data-ttu-id="b62ac-129">有數種空間資料類型。</span><span class="sxs-lookup"><span data-stu-id="b62ac-129">There are several spatial data types.</span></span> <span data-ttu-id="b62ac-130">您使用哪種類型取決於您想要允許的圖案的類型。</span><span class="sxs-lookup"><span data-stu-id="b62ac-130">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="b62ac-131">以下是您可以在模型中使用屬性的 NTS 類型的階層架構。</span><span class="sxs-lookup"><span data-stu-id="b62ac-131">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="b62ac-132">它們位於`NetTopologySuite.Geometries`命名空間。</span><span class="sxs-lookup"><span data-stu-id="b62ac-132">They're located within the `NetTopologySuite.Geometries` namespace.</span></span> <span data-ttu-id="b62ac-133">GeoAPI 封裝中對應的介面 (`GeoAPI.Geometries`命名空間) 也可以使用。</span><span class="sxs-lookup"><span data-stu-id="b62ac-133">Corresponding interfaces in the GeoAPI package (`GeoAPI.Geometries` namespace) can also be used.</span></span>

* <span data-ttu-id="b62ac-134">幾何</span><span class="sxs-lookup"><span data-stu-id="b62ac-134">Geometry</span></span>
  * <span data-ttu-id="b62ac-135">點</span><span class="sxs-lookup"><span data-stu-id="b62ac-135">Point</span></span>
  * <span data-ttu-id="b62ac-136">LineString</span><span class="sxs-lookup"><span data-stu-id="b62ac-136">LineString</span></span>
  * <span data-ttu-id="b62ac-137">多邊形</span><span class="sxs-lookup"><span data-stu-id="b62ac-137">Polygon</span></span>
  * <span data-ttu-id="b62ac-138">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="b62ac-138">GeometryCollection</span></span>
    * <span data-ttu-id="b62ac-139">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="b62ac-139">MultiPoint</span></span>
    * <span data-ttu-id="b62ac-140">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="b62ac-140">MultiLineString</span></span>
    * <span data-ttu-id="b62ac-141">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="b62ac-141">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="b62ac-142">CircularString、 CompoundCurve、 和 CurePolygon NTS 不支援。</span><span class="sxs-lookup"><span data-stu-id="b62ac-142">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="b62ac-143">使用基底的幾何類型可讓任何類型的屬性所指定的圖形。</span><span class="sxs-lookup"><span data-stu-id="b62ac-143">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

<span data-ttu-id="b62ac-144">下列的實體類別可以用來對應至資料表中[Wide World Importers 範例資料庫](http://go.microsoft.com/fwlink/?LinkID=800630)。</span><span class="sxs-lookup"><span data-stu-id="b62ac-144">The following entity classes could be used to map to tables in the [Wide World Importers sample database](http://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

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

### <a name="creating-values"></a><span data-ttu-id="b62ac-145">正在建立的值</span><span class="sxs-lookup"><span data-stu-id="b62ac-145">Creating values</span></span>

<span data-ttu-id="b62ac-146">您可以使用建構函式來建立 geometry 物件;不過，NTS 建議改為使用 geometry factory。</span><span class="sxs-lookup"><span data-stu-id="b62ac-146">You can use constructors to create geometry objects; however, NTS recommends using a geometry factory instead.</span></span> <span data-ttu-id="b62ac-147">這可讓您指定的預設 SRID （座標所使用的空間參考系統），並可讓您控制更進階例如精確度模型 （用於計算期間） 與座標 （決定哪些協調-維度的順序而量值）。</span><span class="sxs-lookup"><span data-stu-id="b62ac-147">This lets you specify a default SRID (the spatial reference system used by the coordinates) and gives you control over more advanced things like the precision model (used during calculations) and the coordinate sequence (determines which ordinates--dimensions and measures--are available).</span></span>

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(-122.121512, 47.6739882);
```

> [!NOTE]
> <span data-ttu-id="b62ac-148">4326 指 WGS 84，GPS 和其他地理系統中使用的標準。</span><span class="sxs-lookup"><span data-stu-id="b62ac-148">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

### <a name="longitude-and-latitude"></a><span data-ttu-id="b62ac-149">經度和緯度</span><span class="sxs-lookup"><span data-stu-id="b62ac-149">Longitude and Latitude</span></span>

<span data-ttu-id="b62ac-150">NTS 中的座標會根據 X 和 Y 值。</span><span class="sxs-lookup"><span data-stu-id="b62ac-150">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="b62ac-151">若要表示經度和緯度，可使用經度和 Y X 緯度。</span><span class="sxs-lookup"><span data-stu-id="b62ac-151">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="b62ac-152">請注意，這**回溯**從`latitude, longitude`中您通常會看到這些值的格式。</span><span class="sxs-lookup"><span data-stu-id="b62ac-152">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

### <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="b62ac-153">在用戶端作業期間略過的 SRID</span><span class="sxs-lookup"><span data-stu-id="b62ac-153">SRID Ignored during client operations</span></span>

<span data-ttu-id="b62ac-154">NTS 會在作業期間，略過的 SRID 值。</span><span class="sxs-lookup"><span data-stu-id="b62ac-154">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="b62ac-155">它假設有一個平面座標系統。</span><span class="sxs-lookup"><span data-stu-id="b62ac-155">It assumes a planar coordinate system.</span></span> <span data-ttu-id="b62ac-156">這表示，如果您指定經度和緯度，例如距離、 長度和區域會以度數為單位，不公尺某些用戶端評估值的座標。</span><span class="sxs-lookup"><span data-stu-id="b62ac-156">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="b62ac-157">如需更有意義的值，您必須先專案要使用等程式庫的另一個座標系統的座標[ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI)然後才計算這些值。</span><span class="sxs-lookup"><span data-stu-id="b62ac-157">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="b62ac-158">如果作業是由 EF Core 透過 SQL server 評估，會決定結果的單位，由資料庫。</span><span class="sxs-lookup"><span data-stu-id="b62ac-158">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="b62ac-159">以下是使用 ProjNet4GeoAPI 來計算兩個城市之間的距離的範例。</span><span class="sxs-lookup"><span data-stu-id="b62ac-159">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

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

## <a name="querying-data"></a><span data-ttu-id="b62ac-160">查詢資料</span><span class="sxs-lookup"><span data-stu-id="b62ac-160">Querying Data</span></span>

<span data-ttu-id="b62ac-161">LINQ，NTS 可用方法與屬性做為資料庫函式會轉譯為 SQL。</span><span class="sxs-lookup"><span data-stu-id="b62ac-161">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="b62ac-162">比方說，距離與 Contains 方法會轉譯下列查詢中。</span><span class="sxs-lookup"><span data-stu-id="b62ac-162">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="b62ac-163">在這篇文章結尾處表顯示各種 EF Core 提供者所支援成員。</span><span class="sxs-lookup"><span data-stu-id="b62ac-163">The table at the end of this article shows which members are supported by various EF Core providers.</span></span>

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a><span data-ttu-id="b62ac-164">SQL Server</span><span class="sxs-lookup"><span data-stu-id="b62ac-164">SQL Server</span></span>

<span data-ttu-id="b62ac-165">如果您使用 SQL Server，有一些您應該要注意的其他項目。</span><span class="sxs-lookup"><span data-stu-id="b62ac-165">If you're using SQL Server, there are some additional things you should be aware of.</span></span>

### <a name="geography-or-geometry"></a><span data-ttu-id="b62ac-166">地理或幾何</span><span class="sxs-lookup"><span data-stu-id="b62ac-166">Geography or geometry</span></span>

<span data-ttu-id="b62ac-167">根據預設，空間的屬性對應到`geography`SQL Server 中的資料行。</span><span class="sxs-lookup"><span data-stu-id="b62ac-167">By default, spatial properties are mapped to `geography` columns in SQL Server.</span></span> <span data-ttu-id="b62ac-168">若要使用`geometry`，[設定資料行類型](xref:core/modeling/relational/data-types)模型中。</span><span class="sxs-lookup"><span data-stu-id="b62ac-168">To use `geometry`, [configure the column type](xref:core/modeling/relational/data-types) in your model.</span></span>

### <a name="geography-polygon-rings"></a><span data-ttu-id="b62ac-169">地理多邊形環</span><span class="sxs-lookup"><span data-stu-id="b62ac-169">Geography polygon rings</span></span>

<span data-ttu-id="b62ac-170">當使用`geography`資料行類型的 SQL Server 會施加額外的需求，在外部環形 （或 shell） 和內部環形 （或漏洞）。</span><span class="sxs-lookup"><span data-stu-id="b62ac-170">When using the `geography` column type, SQL Server imposes additional requirements on the exterior ring (or shell) and interior rings (or holes).</span></span> <span data-ttu-id="b62ac-171">外部環形以逆時針方向必須導向，而且內部環形順時針旋轉。</span><span class="sxs-lookup"><span data-stu-id="b62ac-171">The exterior ring must be oriented counterclockwise and the interior rings clockwise.</span></span> <span data-ttu-id="b62ac-172">NTS 會驗證此值傳送至資料庫之前。</span><span class="sxs-lookup"><span data-stu-id="b62ac-172">NTS validates this before sending values to the database.</span></span>

### <a name="fullglobe"></a><span data-ttu-id="b62ac-173">FullGlobe</span><span class="sxs-lookup"><span data-stu-id="b62ac-173">FullGlobe</span></span>

<span data-ttu-id="b62ac-174">SQL Server 有非標準的幾何類型使用時，表示完整的地球`geography`資料行類型。</span><span class="sxs-lookup"><span data-stu-id="b62ac-174">SQL Server has a non-standard geometry type to represent the full globe when using the `geography` column type.</span></span> <span data-ttu-id="b62ac-175">它也有方法來表示完整的地球 （不含一個外部環形） 為基礎的多邊形。</span><span class="sxs-lookup"><span data-stu-id="b62ac-175">It also has a way to represent polygons based on the full globe (without an exterior ring).</span></span> <span data-ttu-id="b62ac-176">這些都不會受到 NTS。</span><span class="sxs-lookup"><span data-stu-id="b62ac-176">Neither of these are supported by NTS.</span></span>

> [!WARNING]
> <span data-ttu-id="b62ac-177">NTS 不支援 FullGlobe 和以它為基礎的多邊形。</span><span class="sxs-lookup"><span data-stu-id="b62ac-177">FullGlobe and polygons based on it aren't supported by NTS.</span></span>

## <a name="sqlite"></a><span data-ttu-id="b62ac-178">SQLite</span><span class="sxs-lookup"><span data-stu-id="b62ac-178">SQLite</span></span>

<span data-ttu-id="b62ac-179">以下是一些使用 SQLite 的其他資訊。</span><span class="sxs-lookup"><span data-stu-id="b62ac-179">Here is some additional information for those using SQLite.</span></span>

### <a name="installing-spatialite"></a><span data-ttu-id="b62ac-180">安裝 SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="b62ac-180">Installing SpatiaLite</span></span>

<span data-ttu-id="b62ac-181">在 Windows 中，原生 mod_spatialite 程式庫散發為 NuGet 套件相依性。</span><span class="sxs-lookup"><span data-stu-id="b62ac-181">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="b62ac-182">其他平台，就需要另外安裝。</span><span class="sxs-lookup"><span data-stu-id="b62ac-182">Other platforms need to install it separately.</span></span> <span data-ttu-id="b62ac-183">這通常是使用軟體套件管理員。</span><span class="sxs-lookup"><span data-stu-id="b62ac-183">This is typically done using a software package manager.</span></span> <span data-ttu-id="b62ac-184">比方說，您也可以在 Ubuntu 和 Homebrew 在 MacOS 上的使用 APT。</span><span class="sxs-lookup"><span data-stu-id="b62ac-184">For example, you can use APT on Ubuntu and Homebrew on MacOS.</span></span>

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

### <a name="configuring-srid"></a><span data-ttu-id="b62ac-185">設定的 SRID</span><span class="sxs-lookup"><span data-stu-id="b62ac-185">Configuring SRID</span></span>

<span data-ttu-id="b62ac-186">在 SpatiaLite，資料行需要指定每個資料行的 SRID。</span><span class="sxs-lookup"><span data-stu-id="b62ac-186">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="b62ac-187">預設 SRID 為`0`。</span><span class="sxs-lookup"><span data-stu-id="b62ac-187">The default SRID is `0`.</span></span> <span data-ttu-id="b62ac-188">指定使用 ForSqliteHasSrid 方法不同的 SRID。</span><span class="sxs-lookup"><span data-stu-id="b62ac-188">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a><span data-ttu-id="b62ac-189">維度</span><span class="sxs-lookup"><span data-stu-id="b62ac-189">Dimension</span></span>

<span data-ttu-id="b62ac-190">類似於 SRID，資料行的維度 （或協調） 也指定成資料行的一部分。</span><span class="sxs-lookup"><span data-stu-id="b62ac-190">Similar to SRID, a column's dimension (or ordinates) is also specified as part of the column.</span></span> <span data-ttu-id="b62ac-191">預設的協調是 X 及 y 啟用其他協調 （Z 和 M） 使用 ForSqliteHasDimension 方法。</span><span class="sxs-lookup"><span data-stu-id="b62ac-191">The default ordinates are X and Y. Enable additional ordinates (Z and M) using the ForSqliteHasDimension method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a><span data-ttu-id="b62ac-192">轉譯的作業</span><span class="sxs-lookup"><span data-stu-id="b62ac-192">Translated Operations</span></span>

<span data-ttu-id="b62ac-193">下表顯示哪些 NTS 成員時，會轉譯成 SQL 上，每個 EF Core 提供者。</span><span class="sxs-lookup"><span data-stu-id="b62ac-193">This table shows which NTS members are translated into SQL by each EF Core provider.</span></span>

<span data-ttu-id="b62ac-194">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="b62ac-194">NetTopologySuite</span></span> | <span data-ttu-id="b62ac-195">SQL Server (geometry)</span><span class="sxs-lookup"><span data-stu-id="b62ac-195">SQL Server (geometry)</span></span> | <span data-ttu-id="b62ac-196">SQL Server (geography)</span><span class="sxs-lookup"><span data-stu-id="b62ac-196">SQL Server (geography)</span></span> | <span data-ttu-id="b62ac-197">SQLite</span><span class="sxs-lookup"><span data-stu-id="b62ac-197">SQLite</span></span> | <span data-ttu-id="b62ac-198">Npgsql</span><span class="sxs-lookup"><span data-stu-id="b62ac-198">Npgsql</span></span>
--- |:---:|:---:|:---:|:---:
<span data-ttu-id="b62ac-199">Geometry.Area</span><span class="sxs-lookup"><span data-stu-id="b62ac-199">Geometry.Area</span></span> | <span data-ttu-id="b62ac-200">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-200">✔</span></span> | <span data-ttu-id="b62ac-201">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-201">✔</span></span> | <span data-ttu-id="b62ac-202">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-202">✔</span></span> | <span data-ttu-id="b62ac-203">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-203">✔</span></span>
<span data-ttu-id="b62ac-204">Geometry.AsBinary()</span><span class="sxs-lookup"><span data-stu-id="b62ac-204">Geometry.AsBinary()</span></span> | <span data-ttu-id="b62ac-205">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-205">✔</span></span> | <span data-ttu-id="b62ac-206">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-206">✔</span></span> | <span data-ttu-id="b62ac-207">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-207">✔</span></span> | <span data-ttu-id="b62ac-208">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-208">✔</span></span>
<span data-ttu-id="b62ac-209">Geometry.AsText()</span><span class="sxs-lookup"><span data-stu-id="b62ac-209">Geometry.AsText()</span></span> | <span data-ttu-id="b62ac-210">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-210">✔</span></span> | <span data-ttu-id="b62ac-211">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-211">✔</span></span> | <span data-ttu-id="b62ac-212">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-212">✔</span></span> | <span data-ttu-id="b62ac-213">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-213">✔</span></span>
<span data-ttu-id="b62ac-214">Geometry.Boundary</span><span class="sxs-lookup"><span data-stu-id="b62ac-214">Geometry.Boundary</span></span> | <span data-ttu-id="b62ac-215">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-215">✔</span></span> | | <span data-ttu-id="b62ac-216">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-216">✔</span></span> | <span data-ttu-id="b62ac-217">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-217">✔</span></span>
<span data-ttu-id="b62ac-218">Geometry.Buffer(double)</span><span class="sxs-lookup"><span data-stu-id="b62ac-218">Geometry.Buffer(double)</span></span> | <span data-ttu-id="b62ac-219">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-219">✔</span></span> | <span data-ttu-id="b62ac-220">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-220">✔</span></span> | <span data-ttu-id="b62ac-221">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-221">✔</span></span> | <span data-ttu-id="b62ac-222">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-222">✔</span></span>
<span data-ttu-id="b62ac-223">Geometry.Buffer (double，int)</span><span class="sxs-lookup"><span data-stu-id="b62ac-223">Geometry.Buffer(double, int)</span></span> | | | <span data-ttu-id="b62ac-224">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-224">✔</span></span>
<span data-ttu-id="b62ac-225">Geometry.Centroid</span><span class="sxs-lookup"><span data-stu-id="b62ac-225">Geometry.Centroid</span></span> | <span data-ttu-id="b62ac-226">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-226">✔</span></span> | | <span data-ttu-id="b62ac-227">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-227">✔</span></span> | <span data-ttu-id="b62ac-228">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-228">✔</span></span>
<span data-ttu-id="b62ac-229">Geometry.Contains(Geometry)</span><span class="sxs-lookup"><span data-stu-id="b62ac-229">Geometry.Contains(Geometry)</span></span> | <span data-ttu-id="b62ac-230">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-230">✔</span></span> | <span data-ttu-id="b62ac-231">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-231">✔</span></span> | <span data-ttu-id="b62ac-232">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-232">✔</span></span> | <span data-ttu-id="b62ac-233">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-233">✔</span></span>
<span data-ttu-id="b62ac-234">Geometry.ConvexHull()</span><span class="sxs-lookup"><span data-stu-id="b62ac-234">Geometry.ConvexHull()</span></span> | <span data-ttu-id="b62ac-235">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-235">✔</span></span> | <span data-ttu-id="b62ac-236">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-236">✔</span></span> | <span data-ttu-id="b62ac-237">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-237">✔</span></span> | <span data-ttu-id="b62ac-238">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-238">✔</span></span>
<span data-ttu-id="b62ac-239">Geometry.CoveredBy(Geometry)</span><span class="sxs-lookup"><span data-stu-id="b62ac-239">Geometry.CoveredBy(Geometry)</span></span> | | | <span data-ttu-id="b62ac-240">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-240">✔</span></span> | <span data-ttu-id="b62ac-241">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-241">✔</span></span>
<span data-ttu-id="b62ac-242">Geometry.Covers(Geometry)</span><span class="sxs-lookup"><span data-stu-id="b62ac-242">Geometry.Covers(Geometry)</span></span> | | | <span data-ttu-id="b62ac-243">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-243">✔</span></span> | <span data-ttu-id="b62ac-244">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-244">✔</span></span>
<span data-ttu-id="b62ac-245">Geometry.Crosses(Geometry)</span><span class="sxs-lookup"><span data-stu-id="b62ac-245">Geometry.Crosses(Geometry)</span></span> | <span data-ttu-id="b62ac-246">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-246">✔</span></span> | | <span data-ttu-id="b62ac-247">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-247">✔</span></span> | <span data-ttu-id="b62ac-248">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-248">✔</span></span>
<span data-ttu-id="b62ac-249">Geometry.Difference(Geometry)</span><span class="sxs-lookup"><span data-stu-id="b62ac-249">Geometry.Difference(Geometry)</span></span> | <span data-ttu-id="b62ac-250">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-250">✔</span></span> | <span data-ttu-id="b62ac-251">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-251">✔</span></span> | <span data-ttu-id="b62ac-252">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-252">✔</span></span> | <span data-ttu-id="b62ac-253">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-253">✔</span></span>
<span data-ttu-id="b62ac-254">Geometry.Dimension</span><span class="sxs-lookup"><span data-stu-id="b62ac-254">Geometry.Dimension</span></span> | <span data-ttu-id="b62ac-255">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-255">✔</span></span> | <span data-ttu-id="b62ac-256">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-256">✔</span></span> | <span data-ttu-id="b62ac-257">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-257">✔</span></span> | <span data-ttu-id="b62ac-258">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-258">✔</span></span>
<span data-ttu-id="b62ac-259">Geometry.Disjoint(Geometry)</span><span class="sxs-lookup"><span data-stu-id="b62ac-259">Geometry.Disjoint(Geometry)</span></span> | <span data-ttu-id="b62ac-260">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-260">✔</span></span> | <span data-ttu-id="b62ac-261">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-261">✔</span></span> | <span data-ttu-id="b62ac-262">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-262">✔</span></span> | <span data-ttu-id="b62ac-263">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-263">✔</span></span>
<span data-ttu-id="b62ac-264">Geometry.Distance(Geometry)</span><span class="sxs-lookup"><span data-stu-id="b62ac-264">Geometry.Distance(Geometry)</span></span> | <span data-ttu-id="b62ac-265">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-265">✔</span></span> | <span data-ttu-id="b62ac-266">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-266">✔</span></span> | <span data-ttu-id="b62ac-267">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-267">✔</span></span> | <span data-ttu-id="b62ac-268">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-268">✔</span></span>
<span data-ttu-id="b62ac-269">Geometry.Envelope</span><span class="sxs-lookup"><span data-stu-id="b62ac-269">Geometry.Envelope</span></span> | <span data-ttu-id="b62ac-270">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-270">✔</span></span> | | <span data-ttu-id="b62ac-271">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-271">✔</span></span> | <span data-ttu-id="b62ac-272">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-272">✔</span></span>
<span data-ttu-id="b62ac-273">Geometry.EqualsExact(Geometry)</span><span class="sxs-lookup"><span data-stu-id="b62ac-273">Geometry.EqualsExact(Geometry)</span></span> | | | | <span data-ttu-id="b62ac-274">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-274">✔</span></span>
<span data-ttu-id="b62ac-275">Geometry.EqualsTopologically(Geometry)</span><span class="sxs-lookup"><span data-stu-id="b62ac-275">Geometry.EqualsTopologically(Geometry)</span></span> | <span data-ttu-id="b62ac-276">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-276">✔</span></span> | <span data-ttu-id="b62ac-277">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-277">✔</span></span> | <span data-ttu-id="b62ac-278">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-278">✔</span></span> | <span data-ttu-id="b62ac-279">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-279">✔</span></span>
<span data-ttu-id="b62ac-280">Geometry.GeometryType</span><span class="sxs-lookup"><span data-stu-id="b62ac-280">Geometry.GeometryType</span></span> | <span data-ttu-id="b62ac-281">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-281">✔</span></span> | <span data-ttu-id="b62ac-282">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-282">✔</span></span> | <span data-ttu-id="b62ac-283">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-283">✔</span></span> | <span data-ttu-id="b62ac-284">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-284">✔</span></span>
<span data-ttu-id="b62ac-285">Geometry.GetGeometryN(int)</span><span class="sxs-lookup"><span data-stu-id="b62ac-285">Geometry.GetGeometryN(int)</span></span> | <span data-ttu-id="b62ac-286">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-286">✔</span></span> | | <span data-ttu-id="b62ac-287">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-287">✔</span></span> | <span data-ttu-id="b62ac-288">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-288">✔</span></span>
<span data-ttu-id="b62ac-289">Geometry.InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="b62ac-289">Geometry.InteriorPoint</span></span> | <span data-ttu-id="b62ac-290">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-290">✔</span></span> | | <span data-ttu-id="b62ac-291">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-291">✔</span></span>
<span data-ttu-id="b62ac-292">Geometry.Intersection(Geometry)</span><span class="sxs-lookup"><span data-stu-id="b62ac-292">Geometry.Intersection(Geometry)</span></span> | <span data-ttu-id="b62ac-293">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-293">✔</span></span> | <span data-ttu-id="b62ac-294">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-294">✔</span></span> | <span data-ttu-id="b62ac-295">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-295">✔</span></span> | <span data-ttu-id="b62ac-296">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-296">✔</span></span>
<span data-ttu-id="b62ac-297">Geometry.Intersects(Geometry)</span><span class="sxs-lookup"><span data-stu-id="b62ac-297">Geometry.Intersects(Geometry)</span></span> | <span data-ttu-id="b62ac-298">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-298">✔</span></span> | <span data-ttu-id="b62ac-299">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-299">✔</span></span> | <span data-ttu-id="b62ac-300">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-300">✔</span></span> | <span data-ttu-id="b62ac-301">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-301">✔</span></span>
<span data-ttu-id="b62ac-302">Geometry.IsEmpty</span><span class="sxs-lookup"><span data-stu-id="b62ac-302">Geometry.IsEmpty</span></span> | <span data-ttu-id="b62ac-303">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-303">✔</span></span> | <span data-ttu-id="b62ac-304">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-304">✔</span></span> | <span data-ttu-id="b62ac-305">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-305">✔</span></span> | <span data-ttu-id="b62ac-306">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-306">✔</span></span>
<span data-ttu-id="b62ac-307">Geometry.IsSimple</span><span class="sxs-lookup"><span data-stu-id="b62ac-307">Geometry.IsSimple</span></span> | <span data-ttu-id="b62ac-308">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-308">✔</span></span> | | <span data-ttu-id="b62ac-309">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-309">✔</span></span> | <span data-ttu-id="b62ac-310">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-310">✔</span></span>
<span data-ttu-id="b62ac-311">Geometry.IsValid</span><span class="sxs-lookup"><span data-stu-id="b62ac-311">Geometry.IsValid</span></span> | <span data-ttu-id="b62ac-312">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-312">✔</span></span> | <span data-ttu-id="b62ac-313">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-313">✔</span></span> | <span data-ttu-id="b62ac-314">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-314">✔</span></span> | <span data-ttu-id="b62ac-315">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-315">✔</span></span>
<span data-ttu-id="b62ac-316">Geometry.IsWithinDistance (Geometry，double)</span><span class="sxs-lookup"><span data-stu-id="b62ac-316">Geometry.IsWithinDistance(Geometry, double)</span></span> | <span data-ttu-id="b62ac-317">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-317">✔</span></span> | | <span data-ttu-id="b62ac-318">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-318">✔</span></span>
<span data-ttu-id="b62ac-319">Geometry.Length</span><span class="sxs-lookup"><span data-stu-id="b62ac-319">Geometry.Length</span></span> | <span data-ttu-id="b62ac-320">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-320">✔</span></span> | <span data-ttu-id="b62ac-321">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-321">✔</span></span> | <span data-ttu-id="b62ac-322">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-322">✔</span></span> | <span data-ttu-id="b62ac-323">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-323">✔</span></span>
<span data-ttu-id="b62ac-324">Geometry.NumGeometries</span><span class="sxs-lookup"><span data-stu-id="b62ac-324">Geometry.NumGeometries</span></span> | <span data-ttu-id="b62ac-325">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-325">✔</span></span> | <span data-ttu-id="b62ac-326">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-326">✔</span></span> | <span data-ttu-id="b62ac-327">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-327">✔</span></span> | <span data-ttu-id="b62ac-328">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-328">✔</span></span>
<span data-ttu-id="b62ac-329">Geometry.NumPoints</span><span class="sxs-lookup"><span data-stu-id="b62ac-329">Geometry.NumPoints</span></span> | <span data-ttu-id="b62ac-330">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-330">✔</span></span> | <span data-ttu-id="b62ac-331">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-331">✔</span></span> | <span data-ttu-id="b62ac-332">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-332">✔</span></span> | <span data-ttu-id="b62ac-333">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-333">✔</span></span>
<span data-ttu-id="b62ac-334">Geometry.OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="b62ac-334">Geometry.OgcGeometryType</span></span> | <span data-ttu-id="b62ac-335">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-335">✔</span></span> | <span data-ttu-id="b62ac-336">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-336">✔</span></span> | <span data-ttu-id="b62ac-337">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-337">✔</span></span>
<span data-ttu-id="b62ac-338">Geometry.Overlaps(Geometry)</span><span class="sxs-lookup"><span data-stu-id="b62ac-338">Geometry.Overlaps(Geometry)</span></span> | <span data-ttu-id="b62ac-339">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-339">✔</span></span> | <span data-ttu-id="b62ac-340">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-340">✔</span></span> | <span data-ttu-id="b62ac-341">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-341">✔</span></span> | <span data-ttu-id="b62ac-342">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-342">✔</span></span>
<span data-ttu-id="b62ac-343">Geometry.PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="b62ac-343">Geometry.PointOnSurface</span></span> | <span data-ttu-id="b62ac-344">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-344">✔</span></span> | | <span data-ttu-id="b62ac-345">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-345">✔</span></span> | <span data-ttu-id="b62ac-346">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-346">✔</span></span>
<span data-ttu-id="b62ac-347">Geometry.Relate (Geometry，字串)</span><span class="sxs-lookup"><span data-stu-id="b62ac-347">Geometry.Relate(Geometry, string)</span></span> | <span data-ttu-id="b62ac-348">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-348">✔</span></span> | | <span data-ttu-id="b62ac-349">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-349">✔</span></span> | <span data-ttu-id="b62ac-350">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-350">✔</span></span>
<span data-ttu-id="b62ac-351">Geometry.Reverse()</span><span class="sxs-lookup"><span data-stu-id="b62ac-351">Geometry.Reverse()</span></span> | | | <span data-ttu-id="b62ac-352">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-352">✔</span></span> | <span data-ttu-id="b62ac-353">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-353">✔</span></span>
<span data-ttu-id="b62ac-354">Geometry.SRID</span><span class="sxs-lookup"><span data-stu-id="b62ac-354">Geometry.SRID</span></span> | <span data-ttu-id="b62ac-355">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-355">✔</span></span> | <span data-ttu-id="b62ac-356">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-356">✔</span></span> | <span data-ttu-id="b62ac-357">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-357">✔</span></span> | <span data-ttu-id="b62ac-358">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-358">✔</span></span>
<span data-ttu-id="b62ac-359">Geometry.SymmetricDifference(Geometry)</span><span class="sxs-lookup"><span data-stu-id="b62ac-359">Geometry.SymmetricDifference(Geometry)</span></span> | <span data-ttu-id="b62ac-360">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-360">✔</span></span> | <span data-ttu-id="b62ac-361">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-361">✔</span></span> | <span data-ttu-id="b62ac-362">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-362">✔</span></span> | <span data-ttu-id="b62ac-363">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-363">✔</span></span>
<span data-ttu-id="b62ac-364">Geometry.ToBinary()</span><span class="sxs-lookup"><span data-stu-id="b62ac-364">Geometry.ToBinary()</span></span> | <span data-ttu-id="b62ac-365">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-365">✔</span></span> | <span data-ttu-id="b62ac-366">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-366">✔</span></span> | <span data-ttu-id="b62ac-367">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-367">✔</span></span> | <span data-ttu-id="b62ac-368">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-368">✔</span></span>
<span data-ttu-id="b62ac-369">Geometry.ToText()</span><span class="sxs-lookup"><span data-stu-id="b62ac-369">Geometry.ToText()</span></span> | <span data-ttu-id="b62ac-370">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-370">✔</span></span> | <span data-ttu-id="b62ac-371">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-371">✔</span></span> | <span data-ttu-id="b62ac-372">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-372">✔</span></span> | <span data-ttu-id="b62ac-373">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-373">✔</span></span>
<span data-ttu-id="b62ac-374">Geometry.Touches(Geometry)</span><span class="sxs-lookup"><span data-stu-id="b62ac-374">Geometry.Touches(Geometry)</span></span> | <span data-ttu-id="b62ac-375">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-375">✔</span></span> | | <span data-ttu-id="b62ac-376">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-376">✔</span></span> | <span data-ttu-id="b62ac-377">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-377">✔</span></span>
<span data-ttu-id="b62ac-378">Geometry.Union()</span><span class="sxs-lookup"><span data-stu-id="b62ac-378">Geometry.Union()</span></span> | | | <span data-ttu-id="b62ac-379">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-379">✔</span></span>
<span data-ttu-id="b62ac-380">Geometry.Union(Geometry)</span><span class="sxs-lookup"><span data-stu-id="b62ac-380">Geometry.Union(Geometry)</span></span> | <span data-ttu-id="b62ac-381">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-381">✔</span></span> | <span data-ttu-id="b62ac-382">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-382">✔</span></span> | <span data-ttu-id="b62ac-383">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-383">✔</span></span> | <span data-ttu-id="b62ac-384">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-384">✔</span></span>
<span data-ttu-id="b62ac-385">Geometry.Within(Geometry)</span><span class="sxs-lookup"><span data-stu-id="b62ac-385">Geometry.Within(Geometry)</span></span> | <span data-ttu-id="b62ac-386">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-386">✔</span></span> | <span data-ttu-id="b62ac-387">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-387">✔</span></span> | <span data-ttu-id="b62ac-388">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-388">✔</span></span> | <span data-ttu-id="b62ac-389">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-389">✔</span></span>
<span data-ttu-id="b62ac-390">GeometryCollection.Count</span><span class="sxs-lookup"><span data-stu-id="b62ac-390">GeometryCollection.Count</span></span> | <span data-ttu-id="b62ac-391">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-391">✔</span></span> | <span data-ttu-id="b62ac-392">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-392">✔</span></span> | <span data-ttu-id="b62ac-393">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-393">✔</span></span> | <span data-ttu-id="b62ac-394">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-394">✔</span></span>
<span data-ttu-id="b62ac-395">GeometryCollection [int]</span><span class="sxs-lookup"><span data-stu-id="b62ac-395">GeometryCollection[int]</span></span> | <span data-ttu-id="b62ac-396">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-396">✔</span></span> | <span data-ttu-id="b62ac-397">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-397">✔</span></span> | <span data-ttu-id="b62ac-398">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-398">✔</span></span> | <span data-ttu-id="b62ac-399">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-399">✔</span></span>
<span data-ttu-id="b62ac-400">LineString.Count</span><span class="sxs-lookup"><span data-stu-id="b62ac-400">LineString.Count</span></span> | <span data-ttu-id="b62ac-401">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-401">✔</span></span> | <span data-ttu-id="b62ac-402">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-402">✔</span></span> | <span data-ttu-id="b62ac-403">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-403">✔</span></span> | <span data-ttu-id="b62ac-404">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-404">✔</span></span>
<span data-ttu-id="b62ac-405">LineString.EndPoint</span><span class="sxs-lookup"><span data-stu-id="b62ac-405">LineString.EndPoint</span></span> | <span data-ttu-id="b62ac-406">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-406">✔</span></span> | <span data-ttu-id="b62ac-407">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-407">✔</span></span> | <span data-ttu-id="b62ac-408">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-408">✔</span></span> | <span data-ttu-id="b62ac-409">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-409">✔</span></span>
<span data-ttu-id="b62ac-410">LineString.GetPointN(int)</span><span class="sxs-lookup"><span data-stu-id="b62ac-410">LineString.GetPointN(int)</span></span> | <span data-ttu-id="b62ac-411">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-411">✔</span></span> | <span data-ttu-id="b62ac-412">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-412">✔</span></span> | <span data-ttu-id="b62ac-413">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-413">✔</span></span> | <span data-ttu-id="b62ac-414">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-414">✔</span></span>
<span data-ttu-id="b62ac-415">LineString.IsClosed</span><span class="sxs-lookup"><span data-stu-id="b62ac-415">LineString.IsClosed</span></span> | <span data-ttu-id="b62ac-416">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-416">✔</span></span> | <span data-ttu-id="b62ac-417">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-417">✔</span></span> | <span data-ttu-id="b62ac-418">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-418">✔</span></span> | <span data-ttu-id="b62ac-419">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-419">✔</span></span>
<span data-ttu-id="b62ac-420">LineString.IsRing</span><span class="sxs-lookup"><span data-stu-id="b62ac-420">LineString.IsRing</span></span> | <span data-ttu-id="b62ac-421">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-421">✔</span></span> | | <span data-ttu-id="b62ac-422">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-422">✔</span></span> | <span data-ttu-id="b62ac-423">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-423">✔</span></span>
<span data-ttu-id="b62ac-424">LineString.StartPoint</span><span class="sxs-lookup"><span data-stu-id="b62ac-424">LineString.StartPoint</span></span> | <span data-ttu-id="b62ac-425">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-425">✔</span></span> | <span data-ttu-id="b62ac-426">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-426">✔</span></span> | <span data-ttu-id="b62ac-427">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-427">✔</span></span> | <span data-ttu-id="b62ac-428">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-428">✔</span></span>
<span data-ttu-id="b62ac-429">MultiLineString.IsClosed</span><span class="sxs-lookup"><span data-stu-id="b62ac-429">MultiLineString.IsClosed</span></span> | <span data-ttu-id="b62ac-430">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-430">✔</span></span> | <span data-ttu-id="b62ac-431">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-431">✔</span></span> | <span data-ttu-id="b62ac-432">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-432">✔</span></span> | <span data-ttu-id="b62ac-433">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-433">✔</span></span>
<span data-ttu-id="b62ac-434">Point.M</span><span class="sxs-lookup"><span data-stu-id="b62ac-434">Point.M</span></span> | <span data-ttu-id="b62ac-435">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-435">✔</span></span> | <span data-ttu-id="b62ac-436">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-436">✔</span></span> | <span data-ttu-id="b62ac-437">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-437">✔</span></span> | <span data-ttu-id="b62ac-438">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-438">✔</span></span>
<span data-ttu-id="b62ac-439">Point.X</span><span class="sxs-lookup"><span data-stu-id="b62ac-439">Point.X</span></span> | <span data-ttu-id="b62ac-440">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-440">✔</span></span> | <span data-ttu-id="b62ac-441">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-441">✔</span></span> | <span data-ttu-id="b62ac-442">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-442">✔</span></span> | <span data-ttu-id="b62ac-443">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-443">✔</span></span>
<span data-ttu-id="b62ac-444">Point.Y</span><span class="sxs-lookup"><span data-stu-id="b62ac-444">Point.Y</span></span> | <span data-ttu-id="b62ac-445">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-445">✔</span></span> | <span data-ttu-id="b62ac-446">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-446">✔</span></span> | <span data-ttu-id="b62ac-447">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-447">✔</span></span> | <span data-ttu-id="b62ac-448">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-448">✔</span></span>
<span data-ttu-id="b62ac-449">Point.Z</span><span class="sxs-lookup"><span data-stu-id="b62ac-449">Point.Z</span></span> | <span data-ttu-id="b62ac-450">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-450">✔</span></span> | <span data-ttu-id="b62ac-451">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-451">✔</span></span> | <span data-ttu-id="b62ac-452">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-452">✔</span></span> | <span data-ttu-id="b62ac-453">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-453">✔</span></span>
<span data-ttu-id="b62ac-454">Polygon.ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="b62ac-454">Polygon.ExteriorRing</span></span> | <span data-ttu-id="b62ac-455">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-455">✔</span></span> | <span data-ttu-id="b62ac-456">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-456">✔</span></span> | <span data-ttu-id="b62ac-457">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-457">✔</span></span> | <span data-ttu-id="b62ac-458">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-458">✔</span></span>
<span data-ttu-id="b62ac-459">Polygon.GetInteriorRingN(int)</span><span class="sxs-lookup"><span data-stu-id="b62ac-459">Polygon.GetInteriorRingN(int)</span></span> | <span data-ttu-id="b62ac-460">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-460">✔</span></span> | <span data-ttu-id="b62ac-461">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-461">✔</span></span> | <span data-ttu-id="b62ac-462">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-462">✔</span></span> | <span data-ttu-id="b62ac-463">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-463">✔</span></span>
<span data-ttu-id="b62ac-464">Polygon.NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="b62ac-464">Polygon.NumInteriorRings</span></span> | <span data-ttu-id="b62ac-465">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-465">✔</span></span> | <span data-ttu-id="b62ac-466">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-466">✔</span></span> | <span data-ttu-id="b62ac-467">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-467">✔</span></span> | <span data-ttu-id="b62ac-468">✔</span><span class="sxs-lookup"><span data-stu-id="b62ac-468">✔</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b62ac-469">其他資源</span><span class="sxs-lookup"><span data-stu-id="b62ac-469">Additional resources</span></span>

* [<span data-ttu-id="b62ac-470">SQL Server 中的空間資料</span><span class="sxs-lookup"><span data-stu-id="b62ac-470">Spatial Data in SQL Server</span></span>](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [<span data-ttu-id="b62ac-471">SpatiaLite 首頁</span><span class="sxs-lookup"><span data-stu-id="b62ac-471">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="b62ac-472">Npgsql 空間的文件</span><span class="sxs-lookup"><span data-stu-id="b62ac-472">Npgsql Spatial Documentation</span></span>](http://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="b62ac-473">PostGIS 文件</span><span class="sxs-lookup"><span data-stu-id="b62ac-473">PostGIS Documentation</span></span>](http://postgis.net/documentation/)
