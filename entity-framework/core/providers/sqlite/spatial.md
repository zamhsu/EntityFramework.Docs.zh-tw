---
title: SQLite 資料庫提供者-空間資料-EF Core
description: 使用空間資料搭配 Entity Framework Core SQLite 資料庫提供者
author: bricelam
ms.date: 10/02/2020
uid: core/providers/sqlite/spatial
ms.openlocfilehash: aaf0dcd7dc4b670d56ac99567e2e129313427e40
ms.sourcegitcommit: 196ebb726d99c2fa3f702d599f4bdae5e938cb1f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2021
ms.locfileid: "106165941"
---
# <a name="spatial-data-in-the-sqlite-ef-core-provider"></a><span data-ttu-id="9c7cb-103">SQLite EF Core 提供者中的空間資料</span><span class="sxs-lookup"><span data-stu-id="9c7cb-103">Spatial Data in the SQLite EF Core Provider</span></span>

<span data-ttu-id="9c7cb-104">此頁面包含有關搭配 SQLite 資料庫提供者使用空間資料的其他資訊。</span><span class="sxs-lookup"><span data-stu-id="9c7cb-104">This page includes additional information about using spatial data with the SQLite database provider.</span></span> <span data-ttu-id="9c7cb-105">如需在 EF Core 中使用空間資料的一般資訊，請參閱主要 [空間資料](xref:core/modeling/spatial) 檔集。</span><span class="sxs-lookup"><span data-stu-id="9c7cb-105">For general information about using spatial data in EF Core, see the main [Spatial Data](xref:core/modeling/spatial) documentation.</span></span>

## <a name="installing-spatialite"></a><span data-ttu-id="9c7cb-106">安裝 SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="9c7cb-106">Installing SpatiaLite</span></span>

<span data-ttu-id="9c7cb-107">在 Windows 上，原生 mod_spatialite 程式庫會以 NuGet 套件相依性的形式散發。</span><span class="sxs-lookup"><span data-stu-id="9c7cb-107">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="9c7cb-108">其他平臺則需要另外安裝。</span><span class="sxs-lookup"><span data-stu-id="9c7cb-108">Other platforms need to install it separately.</span></span> <span data-ttu-id="9c7cb-109">這通常是使用軟體套件管理員來完成。</span><span class="sxs-lookup"><span data-stu-id="9c7cb-109">This is typically done using a software package manager.</span></span> <span data-ttu-id="9c7cb-110">例如，您可以在 Debian 和 Ubuntu 上使用 APT;MacOS 上的 Homebrew。</span><span class="sxs-lookup"><span data-stu-id="9c7cb-110">For example, you can use APT on Debian and Ubuntu; and Homebrew on MacOS.</span></span>

```bash
# Debian/Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

<span data-ttu-id="9c7cb-111">可惜的是，較新版本的 PROJ (SpatiaLite) 的相依性與 EF 的預設 [SQLitePCLRaw](/dotnet/standard/data/sqlite/custom-versions#bundles)組合不相容。</span><span class="sxs-lookup"><span data-stu-id="9c7cb-111">Unfortunately, newer versions of PROJ (a dependency of SpatiaLite) are incompatible with EF's default [SQLitePCLRaw bundle](/dotnet/standard/data/sqlite/custom-versions#bundles).</span></span> <span data-ttu-id="9c7cb-112">您可以改為使用系統 SQLite 程式庫來解決此問題。</span><span class="sxs-lookup"><span data-stu-id="9c7cb-112">You can work around this by using the system SQLite library instead.</span></span>

```xml
<ItemGroup>
  <!-- Use bundle_sqlite3 instead with SpatiaLite on macOS and Linux -->
  <!--<PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite" Version="3.1.0" />-->
  <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite.Core" Version="3.1.0" />
  <PackageReference Include="SQLitePCLRaw.bundle_sqlite3" Version="2.0.4" />

  <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite" Version="3.1.0" />
</ItemGroup>
```

<span data-ttu-id="9c7cb-113">在 **macOS** 上，您也需要先設定環境變數，然後再執行您的應用程式，使其使用 Homebrew 的 SQLite 版本。</span><span class="sxs-lookup"><span data-stu-id="9c7cb-113">On **macOS**, you'll also need set an environment variable before running your app so it uses Homebrew's version of SQLite.</span></span> <span data-ttu-id="9c7cb-114">在 Visual Studio for Mac 中，您可以在 [專案] > 專案選項下設定此專案， **> 執行 > 設定 > 預設**</span><span class="sxs-lookup"><span data-stu-id="9c7cb-114">In Visual Studio for Mac, you can set this under **Project > Project Options > Run > Configurations > Default**</span></span>

```bash
DYLD_LIBRARY_PATH=/usr/local/opt/sqlite/lib
```

## <a name="configuring-srid"></a><span data-ttu-id="9c7cb-115">設定 SRID</span><span class="sxs-lookup"><span data-stu-id="9c7cb-115">Configuring SRID</span></span>

<span data-ttu-id="9c7cb-116">在 SpatiaLite 中，資料行需要指定每個資料行的 SRID。</span><span class="sxs-lookup"><span data-stu-id="9c7cb-116">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="9c7cb-117">預設 SRID 為 `0` 。</span><span class="sxs-lookup"><span data-stu-id="9c7cb-117">The default SRID is `0`.</span></span> <span data-ttu-id="9c7cb-118">使用 HasSrid 方法指定不同的 SRID。</span><span class="sxs-lookup"><span data-stu-id="9c7cb-118">Specify a different SRID using the HasSrid method.</span></span>

```csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .HasSrid(4326);
```

> [!NOTE]
> <span data-ttu-id="9c7cb-119">4326是指 WGS 84，這是在 GPS 和其他地理系統中使用的標準。</span><span class="sxs-lookup"><span data-stu-id="9c7cb-119">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

## <a name="dimension"></a><span data-ttu-id="9c7cb-120">維度</span><span class="sxs-lookup"><span data-stu-id="9c7cb-120">Dimension</span></span>

<span data-ttu-id="9c7cb-121">資料行的預設維度 (或座標) 是 X 和 Y。若要啟用其他座標（例如 Z 或 M），請設定資料行類型。</span><span class="sxs-lookup"><span data-stu-id="9c7cb-121">The default dimension (or ordinates) of a column is X and Y. To enable additional ordinates like Z or M, configure the column type.</span></span>

```csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .HasColumnType("POINTZ");
```

## <a name="spatial-function-mappings"></a><span data-ttu-id="9c7cb-122">空間函數對應</span><span class="sxs-lookup"><span data-stu-id="9c7cb-122">Spatial function mappings</span></span>

<span data-ttu-id="9c7cb-123">下表顯示哪些 [NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) 成員會轉譯成哪些 SQL 函數。</span><span class="sxs-lookup"><span data-stu-id="9c7cb-123">This table shows which [NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) members are translated into which SQL functions.</span></span>

<span data-ttu-id="9c7cb-124">.NET</span><span class="sxs-lookup"><span data-stu-id="9c7cb-124">.NET</span></span>                                        | <span data-ttu-id="9c7cb-125">SQL</span><span class="sxs-lookup"><span data-stu-id="9c7cb-125">SQL</span></span>
------------------------------------------- | ---
<span data-ttu-id="9c7cb-126">幾何。地區</span><span class="sxs-lookup"><span data-stu-id="9c7cb-126">geometry.Area</span></span>                               | <span data-ttu-id="9c7cb-127">區域 (@geometry) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-127">Area(@geometry)</span></span>
<span data-ttu-id="9c7cb-128">幾何。AsBinary () </span><span class="sxs-lookup"><span data-stu-id="9c7cb-128">geometry.AsBinary()</span></span>                         | <span data-ttu-id="9c7cb-129">AsBinary (@geometry) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-129">AsBinary(@geometry)</span></span>
<span data-ttu-id="9c7cb-130">幾何。AsText () </span><span class="sxs-lookup"><span data-stu-id="9c7cb-130">geometry.AsText()</span></span>                           | <span data-ttu-id="9c7cb-131">AsText (@geometry) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-131">AsText(@geometry)</span></span>
<span data-ttu-id="9c7cb-132">幾何。邊界</span><span class="sxs-lookup"><span data-stu-id="9c7cb-132">geometry.Boundary</span></span>                           | <span data-ttu-id="9c7cb-133">界限 (@geometry) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-133">Boundary(@geometry)</span></span>
<span data-ttu-id="9c7cb-134">幾何。緩衝區 (距離) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-134">geometry.Buffer(distance)</span></span>                   | <span data-ttu-id="9c7cb-135">緩衝區 (@geometry ， @distance) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-135">Buffer(@geometry, @distance)</span></span>
<span data-ttu-id="9c7cb-136">幾何。緩衝區 (距離，quadrantSegments) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-136">geometry.Buffer(distance, quadrantSegments)</span></span> | <span data-ttu-id="9c7cb-137">緩衝區 (@geometry 、 @distance 、 @quadrantSegments) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-137">Buffer(@geometry, @distance, @quadrantSegments)</span></span>
<span data-ttu-id="9c7cb-138">幾何。質心</span><span class="sxs-lookup"><span data-stu-id="9c7cb-138">geometry.Centroid</span></span>                           | <span data-ttu-id="9c7cb-139">距心 (@geometry) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-139">Centroid(@geometry)</span></span>
<span data-ttu-id="9c7cb-140">幾何。包含 (g) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-140">geometry.Contains(g)</span></span>                        | <span data-ttu-id="9c7cb-141">包含 (@geometry 、 @g) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-141">Contains(@geometry, @g)</span></span>
<span data-ttu-id="9c7cb-142">幾何。ConvexHull () </span><span class="sxs-lookup"><span data-stu-id="9c7cb-142">geometry.ConvexHull()</span></span>                       | <span data-ttu-id="9c7cb-143">ConvexHull (@geometry) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-143">ConvexHull(@geometry)</span></span>
<span data-ttu-id="9c7cb-144">幾何。CoveredBy (g) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-144">geometry.CoveredBy(g)</span></span>                       | <span data-ttu-id="9c7cb-145">CoveredBy (@geometry ， @g) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-145">CoveredBy(@geometry, @g)</span></span>
<span data-ttu-id="9c7cb-146">幾何。涵蓋 (g) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-146">geometry.Covers(g)</span></span>                          | <span data-ttu-id="9c7cb-147">涵蓋 (@geometry 、 @g) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-147">Covers(@geometry, @g)</span></span>
<span data-ttu-id="9c7cb-148">幾何。交叉 (g) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-148">geometry.Crosses(g)</span></span>                         | <span data-ttu-id="9c7cb-149">交叉 (@geometry ， @g) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-149">Crosses(@geometry, @g)</span></span>
<span data-ttu-id="9c7cb-150">幾何。 (其他) 的差異</span><span class="sxs-lookup"><span data-stu-id="9c7cb-150">geometry.Difference(other)</span></span>                  | <span data-ttu-id="9c7cb-151">差異 (@geometry ， @other) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-151">Difference(@geometry, @other)</span></span>
<span data-ttu-id="9c7cb-152">幾何。維 度</span><span class="sxs-lookup"><span data-stu-id="9c7cb-152">geometry.Dimension</span></span>                          | <span data-ttu-id="9c7cb-153">維度 (@geometry) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-153">Dimension(@geometry)</span></span>
<span data-ttu-id="9c7cb-154">幾何。無交集的 (g) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-154">geometry.Disjoint(g)</span></span>                        | <span data-ttu-id="9c7cb-155">無交集 @geometry 的 (， @g) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-155">Disjoint(@geometry, @g)</span></span>
<span data-ttu-id="9c7cb-156">幾何。距離 (g) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-156">geometry.Distance(g)</span></span>                        | <span data-ttu-id="9c7cb-157">距離 (@geometry 、 @g) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-157">Distance(@geometry, @g)</span></span>
<span data-ttu-id="9c7cb-158">幾何。信封</span><span class="sxs-lookup"><span data-stu-id="9c7cb-158">geometry.Envelope</span></span>                           | <span data-ttu-id="9c7cb-159">信封 (@geometry) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-159">Envelope(@geometry)</span></span>
<span data-ttu-id="9c7cb-160">幾何。EqualsTopologically (g) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-160">geometry.EqualsTopologically(g)</span></span>             | <span data-ttu-id="9c7cb-161">等於 (@geometry ， @g) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-161">Equals(@geometry, @g)</span></span>
<span data-ttu-id="9c7cb-162">幾何。GeometryType</span><span class="sxs-lookup"><span data-stu-id="9c7cb-162">geometry.GeometryType</span></span>                       | <span data-ttu-id="9c7cb-163">GeometryType (@geometry) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-163">GeometryType(@geometry)</span></span>
<span data-ttu-id="9c7cb-164">幾何。GetGeometryN (n) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-164">geometry.GetGeometryN(n)</span></span>                    | <span data-ttu-id="9c7cb-165">GeometryN (@geometry 、 @n + 1) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-165">GeometryN(@geometry, @n + 1)</span></span>
<span data-ttu-id="9c7cb-166">幾何。InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="9c7cb-166">geometry.InteriorPoint</span></span>                      | <span data-ttu-id="9c7cb-167">PointOnSurface (@geometry) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-167">PointOnSurface(@geometry)</span></span>
<span data-ttu-id="9c7cb-168">幾何。 (其他) 的交集</span><span class="sxs-lookup"><span data-stu-id="9c7cb-168">geometry.Intersection(other)</span></span>                | <span data-ttu-id="9c7cb-169">交集 (@geometry 、 @other) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-169">Intersection(@geometry, @other)</span></span>
<span data-ttu-id="9c7cb-170">幾何。 (g) 交集</span><span class="sxs-lookup"><span data-stu-id="9c7cb-170">geometry.Intersects(g)</span></span>                      | <span data-ttu-id="9c7cb-171"> (@geometry 的交集， @g) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-171">Intersects(@geometry, @g)</span></span>
<span data-ttu-id="9c7cb-172">幾何。IsEmpty</span><span class="sxs-lookup"><span data-stu-id="9c7cb-172">geometry.IsEmpty</span></span>                            | <span data-ttu-id="9c7cb-173">IsEmpty (@geometry) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-173">IsEmpty(@geometry)</span></span>
<span data-ttu-id="9c7cb-174">幾何。IsSimple</span><span class="sxs-lookup"><span data-stu-id="9c7cb-174">geometry.IsSimple</span></span>                           | <span data-ttu-id="9c7cb-175">IsSimple (@geometry) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-175">IsSimple(@geometry)</span></span>
<span data-ttu-id="9c7cb-176">幾何。IsValid</span><span class="sxs-lookup"><span data-stu-id="9c7cb-176">geometry.IsValid</span></span>                            | <span data-ttu-id="9c7cb-177">IsValid (@geometry) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-177">IsValid(@geometry)</span></span>
<span data-ttu-id="9c7cb-178">幾何。IsWithinDistance (幾何、距離) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-178">geometry.IsWithinDistance(geom, distance)</span></span>   | <span data-ttu-id="9c7cb-179">距離 (@geometry 、 @geom) <= @distance</span><span class="sxs-lookup"><span data-stu-id="9c7cb-179">Distance(@geometry, @geom) <= @distance</span></span>
<span data-ttu-id="9c7cb-180">幾何。長度</span><span class="sxs-lookup"><span data-stu-id="9c7cb-180">geometry.Length</span></span>                             | <span data-ttu-id="9c7cb-181">GLength (@geometry) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-181">GLength(@geometry)</span></span>
<span data-ttu-id="9c7cb-182">幾何。NumGeometries</span><span class="sxs-lookup"><span data-stu-id="9c7cb-182">geometry.NumGeometries</span></span>                      | <span data-ttu-id="9c7cb-183">NumGeometries (@geometry) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-183">NumGeometries(@geometry)</span></span>
<span data-ttu-id="9c7cb-184">幾何。X.numpoints</span><span class="sxs-lookup"><span data-stu-id="9c7cb-184">geometry.NumPoints</span></span>                          | <span data-ttu-id="9c7cb-185">X.numpoints (@geometry) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-185">NumPoints(@geometry)</span></span>
<span data-ttu-id="9c7cb-186">幾何。OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="9c7cb-186">geometry.OgcGeometryType</span></span>                    | <span data-ttu-id="9c7cb-187">案例 GeometryType (@geometry) 當 ' POINT ' THEN 1 .。。結束</span><span class="sxs-lookup"><span data-stu-id="9c7cb-187">CASE GeometryType(@geometry) WHEN 'POINT' THEN 1 ... END</span></span>
<span data-ttu-id="9c7cb-188">幾何。 (g) 重迭</span><span class="sxs-lookup"><span data-stu-id="9c7cb-188">geometry.Overlaps(g)</span></span>                        | <span data-ttu-id="9c7cb-189">重迭 (@geometry ， @g) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-189">Overlaps(@geometry, @g)</span></span>
<span data-ttu-id="9c7cb-190">幾何。PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="9c7cb-190">geometry.PointOnSurface</span></span>                     | <span data-ttu-id="9c7cb-191">PointOnSurface (@geometry) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-191">PointOnSurface(@geometry)</span></span>
<span data-ttu-id="9c7cb-192">幾何。 (g、intersectionPattern) 建立關聯</span><span class="sxs-lookup"><span data-stu-id="9c7cb-192">geometry.Relate(g, intersectionPattern)</span></span>     | <span data-ttu-id="9c7cb-193"> (@geometry 、 @g 、 @intersectionPattern) 的關聯</span><span class="sxs-lookup"><span data-stu-id="9c7cb-193">Relate(@geometry, @g, @intersectionPattern)</span></span>
<span data-ttu-id="9c7cb-194">幾何。反向 () </span><span class="sxs-lookup"><span data-stu-id="9c7cb-194">geometry.Reverse()</span></span>                          | <span data-ttu-id="9c7cb-195">ST_Reverse (@geometry) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-195">ST_Reverse(@geometry)</span></span>
<span data-ttu-id="9c7cb-196">幾何。SRID</span><span class="sxs-lookup"><span data-stu-id="9c7cb-196">geometry.SRID</span></span>                               | <span data-ttu-id="9c7cb-197">SRID (@geometry) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-197">SRID(@geometry)</span></span>
<span data-ttu-id="9c7cb-198">幾何。SymmetricDifference (其他) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-198">geometry.SymmetricDifference(other)</span></span>         | <span data-ttu-id="9c7cb-199">SymDifference (@geometry ， @other) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-199">SymDifference(@geometry, @other)</span></span>
<span data-ttu-id="9c7cb-200">幾何。ToBinary () </span><span class="sxs-lookup"><span data-stu-id="9c7cb-200">geometry.ToBinary()</span></span>                         | <span data-ttu-id="9c7cb-201">AsBinary (@geometry) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-201">AsBinary(@geometry)</span></span>
<span data-ttu-id="9c7cb-202">幾何。Duration.totext () </span><span class="sxs-lookup"><span data-stu-id="9c7cb-202">geometry.ToText()</span></span>                           | <span data-ttu-id="9c7cb-203">AsText (@geometry) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-203">AsText(@geometry)</span></span>
<span data-ttu-id="9c7cb-204">幾何。接觸 (g) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-204">geometry.Touches(g)</span></span>                         | <span data-ttu-id="9c7cb-205">接觸 (@geometry ， @g) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-205">Touches(@geometry, @g)</span></span>
<span data-ttu-id="9c7cb-206">幾何。Union () </span><span class="sxs-lookup"><span data-stu-id="9c7cb-206">geometry.Union()</span></span>                            | <span data-ttu-id="9c7cb-207">UnaryUnion (@geometry) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-207">UnaryUnion(@geometry)</span></span>
<span data-ttu-id="9c7cb-208">幾何。Union (其他) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-208">geometry.Union(other)</span></span>                       | <span data-ttu-id="9c7cb-209">GUnion (@geometry ， @other) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-209">GUnion(@geometry, @other)</span></span>
<span data-ttu-id="9c7cb-210">幾何。在 (g) 內</span><span class="sxs-lookup"><span data-stu-id="9c7cb-210">geometry.Within(g)</span></span>                          | <span data-ttu-id="9c7cb-211">在 (中 @geometry ， @g) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-211">Within(@geometry, @g)</span></span>
<span data-ttu-id="9c7cb-212">>geometrycollection [i]</span><span class="sxs-lookup"><span data-stu-id="9c7cb-212">geometryCollection[i]</span></span>                       | <span data-ttu-id="9c7cb-213">GeometryN (@geometryCollection 、 @i + 1) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-213">GeometryN(@geometryCollection, @i + 1)</span></span>
<span data-ttu-id="9c7cb-214">>geometrycollection 計數</span><span class="sxs-lookup"><span data-stu-id="9c7cb-214">geometryCollection.Count</span></span>                    | <span data-ttu-id="9c7cb-215">NumGeometries (@geometryCollection) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-215">NumGeometries(@geometryCollection)</span></span>
<span data-ttu-id="9c7cb-216">lineString 計數</span><span class="sxs-lookup"><span data-stu-id="9c7cb-216">lineString.Count</span></span>                            | <span data-ttu-id="9c7cb-217">X.numpoints (@lineString) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-217">NumPoints(@lineString)</span></span>
<span data-ttu-id="9c7cb-218">lineString 端點</span><span class="sxs-lookup"><span data-stu-id="9c7cb-218">lineString.EndPoint</span></span>                         | <span data-ttu-id="9c7cb-219">端點 (@lineString) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-219">EndPoint(@lineString)</span></span>
<span data-ttu-id="9c7cb-220">lineString. GetPointN (n) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-220">lineString.GetPointN(n)</span></span>                     | <span data-ttu-id="9c7cb-221">PointN (@lineString 、 @n + 1) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-221">PointN(@lineString, @n + 1)</span></span>
<span data-ttu-id="9c7cb-222">lineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="9c7cb-222">lineString.IsClosed</span></span>                         | <span data-ttu-id="9c7cb-223">IsClosed (@lineString) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-223">IsClosed(@lineString)</span></span>
<span data-ttu-id="9c7cb-224">lineString. IsRing</span><span class="sxs-lookup"><span data-stu-id="9c7cb-224">lineString.IsRing</span></span>                           | <span data-ttu-id="9c7cb-225">IsRing (@lineString) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-225">IsRing(@lineString)</span></span>
<span data-ttu-id="9c7cb-226">lineString. StartPoint</span><span class="sxs-lookup"><span data-stu-id="9c7cb-226">lineString.StartPoint</span></span>                       | <span data-ttu-id="9c7cb-227">StartPoint (@lineString) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-227">StartPoint(@lineString)</span></span>
<span data-ttu-id="9c7cb-228">multiLineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="9c7cb-228">multiLineString.IsClosed</span></span>                    | <span data-ttu-id="9c7cb-229">IsClosed (@multiLineString) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-229">IsClosed(@multiLineString)</span></span>
<span data-ttu-id="9c7cb-230">點。M</span><span class="sxs-lookup"><span data-stu-id="9c7cb-230">point.M</span></span>                                     | <span data-ttu-id="9c7cb-231">M (@point) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-231">M(@point)</span></span>
<span data-ttu-id="9c7cb-232">點。X</span><span class="sxs-lookup"><span data-stu-id="9c7cb-232">point.X</span></span>                                     | <span data-ttu-id="9c7cb-233">X (@point) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-233">X(@point)</span></span>
<span data-ttu-id="9c7cb-234">點。Y</span><span class="sxs-lookup"><span data-stu-id="9c7cb-234">point.Y</span></span>                                     | <span data-ttu-id="9c7cb-235">Y (@point) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-235">Y(@point)</span></span>
<span data-ttu-id="9c7cb-236">點。Z</span><span class="sxs-lookup"><span data-stu-id="9c7cb-236">point.Z</span></span>                                     | <span data-ttu-id="9c7cb-237">Z (@point) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-237">Z(@point)</span></span>
<span data-ttu-id="9c7cb-238">多邊形。ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="9c7cb-238">polygon.ExteriorRing</span></span>                        | <span data-ttu-id="9c7cb-239">ExteriorRing (@polygon) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-239">ExteriorRing(@polygon)</span></span>
<span data-ttu-id="9c7cb-240">多邊形。GetInteriorRingN (n) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-240">polygon.GetInteriorRingN(n)</span></span>                 | <span data-ttu-id="9c7cb-241">InteriorRingN (@polygon 、 @n + 1) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-241">InteriorRingN(@polygon, @n + 1)</span></span>
<span data-ttu-id="9c7cb-242">多邊形。NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="9c7cb-242">polygon.NumInteriorRings</span></span>                    | <span data-ttu-id="9c7cb-243">NumInteriorRing (@polygon) </span><span class="sxs-lookup"><span data-stu-id="9c7cb-243">NumInteriorRing(@polygon)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9c7cb-244">其他資源</span><span class="sxs-lookup"><span data-stu-id="9c7cb-244">Additional resources</span></span>

* [<span data-ttu-id="9c7cb-245">SpatiaLite 首頁</span><span class="sxs-lookup"><span data-stu-id="9c7cb-245">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="9c7cb-246">NetTopologySuite 檔</span><span class="sxs-lookup"><span data-stu-id="9c7cb-246">NetTopologySuite Docs</span></span>](https://nettopologysuite.github.io/NetTopologySuite/)
