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
# <a name="spatial-data"></a><span data-ttu-id="54c33-103">空間資料</span><span class="sxs-lookup"><span data-stu-id="54c33-103">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="54c33-104">這項功能已在 EF Core 2.2 中新增。</span><span class="sxs-lookup"><span data-stu-id="54c33-104">This feature was added in EF Core 2.2.</span></span>

<span data-ttu-id="54c33-105">空間資料代表實體位置和物件的形狀。</span><span class="sxs-lookup"><span data-stu-id="54c33-105">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="54c33-106">許多資料庫都支援這種類型的資料，因此可與其他資料一起進行編制索引和查詢。</span><span class="sxs-lookup"><span data-stu-id="54c33-106">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="54c33-107">常見的案例包括從位置查詢指定距離內的物件，或選取其框線包含指定位置的物件。</span><span class="sxs-lookup"><span data-stu-id="54c33-107">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="54c33-108">EF Core 支援使用 NetTopologySuite 空間程式庫對應到空間資料類型。</span><span class="sxs-lookup"><span data-stu-id="54c33-108">EF Core supports mapping to spatial data types using the NetTopologySuite spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="54c33-109">安裝</span><span class="sxs-lookup"><span data-stu-id="54c33-109">Installing</span></span>

<span data-ttu-id="54c33-110">為了搭配 EF Core 使用空間資料，您需要安裝適當的支援 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="54c33-110">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="54c33-111">您需要安裝的套件取決於您所使用的提供者。</span><span class="sxs-lookup"><span data-stu-id="54c33-111">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="54c33-112">EF Core 提供者</span><span class="sxs-lookup"><span data-stu-id="54c33-112">EF Core Provider</span></span>                        | <span data-ttu-id="54c33-113">空間 NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="54c33-113">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="54c33-114">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="54c33-114">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="54c33-115">Microsoft.entityframeworkcore SqlServer. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="54c33-115">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="54c33-116">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="54c33-116">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="54c33-117">Microsoft.entityframeworkcore. Sqlite. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="54c33-117">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="54c33-118">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="54c33-118">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="54c33-119">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="54c33-119">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="54c33-120">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="54c33-120">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="54c33-121">Npgsql. Microsoft.entityframeworkcore. 于 postgresql. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="54c33-121">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)
<span data-ttu-id="54c33-122">Pomelo.EntityFrameworkCore.MySql</span><span class="sxs-lookup"><span data-stu-id="54c33-122">Pomelo.EntityFrameworkCore.MySql</span></span>        | [<span data-ttu-id="54c33-123">Pomelo. Microsoft.entityframeworkcore. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="54c33-123">Pomelo.EntityFrameworkCore.MySql.NetTopologySuite</span></span>](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql.NetTopologySuite)
<span data-ttu-id="54c33-124">Devart.Data.MySql.EFCore</span><span class="sxs-lookup"><span data-stu-id="54c33-124">Devart.Data.MySql.EFCore</span></span>                | [<span data-ttu-id="54c33-125">Devart. EFCore. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="54c33-125">Devart.Data.MySql.EFCore.NetTopologySuite</span></span>](https://www.nuget.org/packages/Devart.Data.MySql.EFCore.NetTopologySuite)
<span data-ttu-id="54c33-126">Devart.Data.PostgreSql.EFCore</span><span class="sxs-lookup"><span data-stu-id="54c33-126">Devart.Data.PostgreSql.EFCore</span></span>           | [<span data-ttu-id="54c33-127">Devart. 于 postgresql. EFCore. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="54c33-127">Devart.Data.PostgreSql.EFCore.NetTopologySuite</span></span>](https://www.nuget.org/packages/Devart.Data.PostgreSql.EFCore.NetTopologySuite)
<span data-ttu-id="54c33-128">Devart.Data.SQLite.EFCore</span><span class="sxs-lookup"><span data-stu-id="54c33-128">Devart.Data.SQLite.EFCore</span></span>               | [<span data-ttu-id="54c33-129">Devart. EFCore. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="54c33-129">Devart.Data.SQLite.EFCore.NetTopologySuite</span></span>](https://www.nuget.org/packages/Devart.Data.SQLite.EFCore.NetTopologySuite)
<span data-ttu-id="54c33-130">Teradata.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="54c33-130">Teradata.EntityFrameworkCore</span></span>            | [<span data-ttu-id="54c33-131">Teradata. Microsoft.entityframeworkcore. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="54c33-131">Teradata.EntityFrameworkCore.NetTopologySuite</span></span>](https://www.nuget.org/packages/Teradata.EntityFrameworkCore.NetTopologySuite)

## <a name="nettopologysuite"></a><span data-ttu-id="54c33-132">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="54c33-132">NetTopologySuite</span></span>

<span data-ttu-id="54c33-133">[NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) 是適用于 .net 的空間程式庫。</span><span class="sxs-lookup"><span data-stu-id="54c33-133">[NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) is a spatial library for .NET.</span></span> <span data-ttu-id="54c33-134">EF Core 在您的模型中使用 NTS 類型，即可對應至資料庫中的空間資料類型。</span><span class="sxs-lookup"><span data-stu-id="54c33-134">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="54c33-135">若要透過 NTS 來啟用空間類型的對應，請在提供者的 DbCoNtext options builder 上呼叫 UseNetTopologySuite 方法。</span><span class="sxs-lookup"><span data-stu-id="54c33-135">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="54c33-136">例如，您可以使用 SQL Server 來呼叫它，如下所示。</span><span class="sxs-lookup"><span data-stu-id="54c33-136">For example, with SQL Server you'd call it like this.</span></span>

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/WideWorldImportersContext.cs?name=snippet_UseNetTopologySuite)]

<span data-ttu-id="54c33-137">有數種空間資料類型。</span><span class="sxs-lookup"><span data-stu-id="54c33-137">There are several spatial data types.</span></span> <span data-ttu-id="54c33-138">您所使用的類型取決於您想要允許的圖形類型。</span><span class="sxs-lookup"><span data-stu-id="54c33-138">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="54c33-139">以下是您可以在模型中用於屬性的 NTS 類型階層。</span><span class="sxs-lookup"><span data-stu-id="54c33-139">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="54c33-140">它們位於 `NetTopologySuite.Geometries` 命名空間內。</span><span class="sxs-lookup"><span data-stu-id="54c33-140">They're located within the `NetTopologySuite.Geometries` namespace.</span></span>

* <span data-ttu-id="54c33-141">幾何形狀</span><span class="sxs-lookup"><span data-stu-id="54c33-141">Geometry</span></span>
  * <span data-ttu-id="54c33-142">Point</span><span class="sxs-lookup"><span data-stu-id="54c33-142">Point</span></span>
  * <span data-ttu-id="54c33-143">LineString</span><span class="sxs-lookup"><span data-stu-id="54c33-143">LineString</span></span>
  * <span data-ttu-id="54c33-144">多邊形</span><span class="sxs-lookup"><span data-stu-id="54c33-144">Polygon</span></span>
  * <span data-ttu-id="54c33-145">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="54c33-145">GeometryCollection</span></span>
    * <span data-ttu-id="54c33-146">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="54c33-146">MultiPoint</span></span>
    * <span data-ttu-id="54c33-147">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="54c33-147">MultiLineString</span></span>
    * <span data-ttu-id="54c33-148">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="54c33-148">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="54c33-149">NTS 不支援 CircularString、CompoundCurve 和 CurePolygon。</span><span class="sxs-lookup"><span data-stu-id="54c33-149">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="54c33-150">使用基底 Geometry 型別可讓屬性指定任何類型的圖形。</span><span class="sxs-lookup"><span data-stu-id="54c33-150">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

## <a name="longitude-and-latitude"></a><span data-ttu-id="54c33-151">經度和緯度</span><span class="sxs-lookup"><span data-stu-id="54c33-151">Longitude and Latitude</span></span>

<span data-ttu-id="54c33-152">NTS 中的座標是以 X 和 Y 值為依據。</span><span class="sxs-lookup"><span data-stu-id="54c33-152">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="54c33-153">若要表示經度和緯度，請使用 X 作為經度，Y 用於緯度。</span><span class="sxs-lookup"><span data-stu-id="54c33-153">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="54c33-154">請注意，這 **是** `latitude, longitude` 您通常會看到這些值的格式。</span><span class="sxs-lookup"><span data-stu-id="54c33-154">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

## <a name="querying-data"></a><span data-ttu-id="54c33-155">查詢資料</span><span class="sxs-lookup"><span data-stu-id="54c33-155">Querying Data</span></span>

<span data-ttu-id="54c33-156">下列實體類別可用來對應至 [Wide World 匯入工具範例資料庫](https://go.microsoft.com/fwlink/?LinkID=800630)中的資料表。</span><span class="sxs-lookup"><span data-stu-id="54c33-156">The following entity classes could be used to map to tables in the [Wide World Importers sample database](https://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/City.cs?name=snippet_City)]

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/Country.cs?name=snippet_Country)]

<span data-ttu-id="54c33-157">在 LINQ 中，可做為資料庫函數的 NTS 方法和屬性會轉譯為 SQL。</span><span class="sxs-lookup"><span data-stu-id="54c33-157">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="54c33-158">例如，在下列查詢中，會轉譯距離和包含方法。</span><span class="sxs-lookup"><span data-stu-id="54c33-158">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="54c33-159">請參閱提供者的檔，以瞭解支援的方法。</span><span class="sxs-lookup"><span data-stu-id="54c33-159">See your provider's documentation for which methods are supported.</span></span>

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Program.cs?name=snippet_Distance)]

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Program.cs?name=snippet_Contains)]

## <a name="reverse-engineering"></a><span data-ttu-id="54c33-160">反向工程</span><span class="sxs-lookup"><span data-stu-id="54c33-160">Reverse engineering</span></span>

<span data-ttu-id="54c33-161">空間 NuGet 套件也會啟用具有空間屬性的 [反轉工程](xref:core/managing-schemas/scaffolding) 模型，但您必須先安裝封裝， ***才能*** 執行 `Scaffold-DbContext` 或 `dotnet ef dbcontext scaffold` 。</span><span class="sxs-lookup"><span data-stu-id="54c33-161">The spatial NuGet packages also enable [reverse engineering](xref:core/managing-schemas/scaffolding) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="54c33-162">如果沒有，您將會收到關於找不到資料行之類型對應的警告，而且將略過資料行。</span><span class="sxs-lookup"><span data-stu-id="54c33-162">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="54c33-163">用戶端作業期間忽略 SRID</span><span class="sxs-lookup"><span data-stu-id="54c33-163">SRID Ignored during client operations</span></span>

<span data-ttu-id="54c33-164">NTS 會在作業期間忽略 SRID 值。</span><span class="sxs-lookup"><span data-stu-id="54c33-164">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="54c33-165">它會假設平面座標系統。</span><span class="sxs-lookup"><span data-stu-id="54c33-165">It assumes a planar coordinate system.</span></span> <span data-ttu-id="54c33-166">這表示，如果您在經度和緯度方面指定座標，某些用戶端評估值（例如距離、長度和區域）會以度為單位，而非計量。</span><span class="sxs-lookup"><span data-stu-id="54c33-166">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="54c33-167">如需更有意義的值，您必須先使用 [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) 之類的程式庫，將座標投影至另一個座標系統，然後再計算這些值。</span><span class="sxs-lookup"><span data-stu-id="54c33-167">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="54c33-168">如果作業是由 EF Core 透過 SQL 評估，則結果的單位將由資料庫決定。</span><span class="sxs-lookup"><span data-stu-id="54c33-168">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="54c33-169">以下範例使用 ProjNet4GeoAPI 來計算兩個城市之間的距離。</span><span class="sxs-lookup"><span data-stu-id="54c33-169">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

[!code-csharp[](../../../samples/core/Spatial/Projections/GeometryExtensions.cs?name=snippet_GeometryExtensions)]

[!code-csharp[](../../../samples/core/Spatial/Projections/Program.cs?name=snippet_ProjectTo)]

> [!NOTE]
> <span data-ttu-id="54c33-170">4326是指 WGS 84，這是在 GPS 和其他地理系統中使用的標準。</span><span class="sxs-lookup"><span data-stu-id="54c33-170">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="54c33-171">其他資源</span><span class="sxs-lookup"><span data-stu-id="54c33-171">Additional resources</span></span>

<span data-ttu-id="54c33-172">請務必閱讀提供者的檔，以取得處理空間資料的其他資訊。</span><span class="sxs-lookup"><span data-stu-id="54c33-172">Be sure to read your provider's documentation for additional information on working with spatial data.</span></span>

* [<span data-ttu-id="54c33-173">SQL Server 提供者中的空間資料</span><span class="sxs-lookup"><span data-stu-id="54c33-173">Spatial Data in the SQL Server Provider</span></span>](xref:core/providers/sql-server/spatial)
* [<span data-ttu-id="54c33-174">SQLite 提供者中的空間資料</span><span class="sxs-lookup"><span data-stu-id="54c33-174">Spatial Data in the SQLite Provider</span></span>](xref:core/providers/sqlite/spatial)
* [<span data-ttu-id="54c33-175">Npgsql 提供者中的空間資料</span><span class="sxs-lookup"><span data-stu-id="54c33-175">Spatial Data in the Npgsql Provider</span></span>](https://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="54c33-176">NetTopologySuite 檔</span><span class="sxs-lookup"><span data-stu-id="54c33-176">NetTopologySuite Docs</span></span>](https://nettopologysuite.github.io/NetTopologySuite/)
