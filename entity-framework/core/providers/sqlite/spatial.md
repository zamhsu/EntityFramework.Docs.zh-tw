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
# <a name="spatial-data-in-the-sqlite-ef-core-provider"></a>SQLite EF Core 提供者中的空間資料

此頁面包含有關搭配 SQLite 資料庫提供者使用空間資料的其他資訊。 如需在 EF Core 中使用空間資料的一般資訊，請參閱主要 [空間資料](xref:core/modeling/spatial) 檔集。

## <a name="installing-spatialite"></a>安裝 SpatiaLite

在 Windows 上，原生 mod_spatialite 程式庫會以 NuGet 套件相依性的形式散發。 其他平臺則需要另外安裝。 這通常是使用軟體套件管理員來完成。 例如，您可以在 Debian 和 Ubuntu 上使用 APT;MacOS 上的 Homebrew。

```bash
# Debian/Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

可惜的是，較新版本的 PROJ (SpatiaLite) 的相依性與 EF 的預設 [SQLitePCLRaw](/dotnet/standard/data/sqlite/custom-versions#bundles)組合不相容。 您可以改為使用系統 SQLite 程式庫來解決此問題。

```xml
<ItemGroup>
  <!-- Use bundle_sqlite3 instead with SpatiaLite on macOS and Linux -->
  <!--<PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite" Version="3.1.0" />-->
  <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite.Core" Version="3.1.0" />
  <PackageReference Include="SQLitePCLRaw.bundle_sqlite3" Version="2.0.4" />

  <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite" Version="3.1.0" />
</ItemGroup>
```

在 **macOS** 上，您也需要先設定環境變數，然後再執行您的應用程式，使其使用 Homebrew 的 SQLite 版本。 在 Visual Studio for Mac 中，您可以在 [專案] > 專案選項下設定此專案， **> 執行 > 設定 > 預設**

```bash
DYLD_LIBRARY_PATH=/usr/local/opt/sqlite/lib
```

## <a name="configuring-srid"></a>設定 SRID

在 SpatiaLite 中，資料行需要指定每個資料行的 SRID。 預設 SRID 為 `0` 。 使用 HasSrid 方法指定不同的 SRID。

```csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .HasSrid(4326);
```

> [!NOTE]
> 4326是指 WGS 84，這是在 GPS 和其他地理系統中使用的標準。

## <a name="dimension"></a>維度

資料行的預設維度 (或座標) 是 X 和 Y。若要啟用其他座標（例如 Z 或 M），請設定資料行類型。

```csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .HasColumnType("POINTZ");
```

## <a name="spatial-function-mappings"></a>空間函數對應

下表顯示哪些 [NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) 成員會轉譯成哪些 SQL 函數。

.NET                                        | SQL
------------------------------------------- | ---
幾何。地區                               | 區域 (@geometry) 
幾何。AsBinary ()                          | AsBinary (@geometry) 
幾何。AsText ()                            | AsText (@geometry) 
幾何。邊界                           | 界限 (@geometry) 
幾何。緩衝區 (距離)                    | 緩衝區 (@geometry ， @distance) 
幾何。緩衝區 (距離，quadrantSegments)  | 緩衝區 (@geometry 、 @distance 、 @quadrantSegments) 
幾何。質心                           | 距心 (@geometry) 
幾何。包含 (g)                         | 包含 (@geometry 、 @g) 
幾何。ConvexHull ()                        | ConvexHull (@geometry) 
幾何。CoveredBy (g)                        | CoveredBy (@geometry ， @g) 
幾何。涵蓋 (g)                           | 涵蓋 (@geometry 、 @g) 
幾何。交叉 (g)                          | 交叉 (@geometry ， @g) 
幾何。 (其他) 的差異                  | 差異 (@geometry ， @other) 
幾何。維 度                          | 維度 (@geometry) 
幾何。無交集的 (g)                         | 無交集 @geometry 的 (， @g) 
幾何。距離 (g)                         | 距離 (@geometry 、 @g) 
幾何。信封                           | 信封 (@geometry) 
幾何。EqualsTopologically (g)              | 等於 (@geometry ， @g) 
幾何。GeometryType                       | GeometryType (@geometry) 
幾何。GetGeometryN (n)                     | GeometryN (@geometry 、 @n + 1) 
幾何。InteriorPoint                      | PointOnSurface (@geometry) 
幾何。 (其他) 的交集                | 交集 (@geometry 、 @other) 
幾何。 (g) 交集                      |  (@geometry 的交集， @g) 
幾何。IsEmpty                            | IsEmpty (@geometry) 
幾何。IsSimple                           | IsSimple (@geometry) 
幾何。IsValid                            | IsValid (@geometry) 
幾何。IsWithinDistance (幾何、距離)    | 距離 (@geometry 、 @geom) <= @distance
幾何。長度                             | GLength (@geometry) 
幾何。NumGeometries                      | NumGeometries (@geometry) 
幾何。X.numpoints                          | X.numpoints (@geometry) 
幾何。OgcGeometryType                    | 案例 GeometryType (@geometry) 當 ' POINT ' THEN 1 .。。結束
幾何。 (g) 重迭                        | 重迭 (@geometry ， @g) 
幾何。PointOnSurface                     | PointOnSurface (@geometry) 
幾何。 (g、intersectionPattern) 建立關聯     |  (@geometry 、 @g 、 @intersectionPattern) 的關聯
幾何。反向 ()                           | ST_Reverse (@geometry) 
幾何。SRID                               | SRID (@geometry) 
幾何。SymmetricDifference (其他)          | SymDifference (@geometry ， @other) 
幾何。ToBinary ()                          | AsBinary (@geometry) 
幾何。Duration.totext ()                            | AsText (@geometry) 
幾何。接觸 (g)                          | 接觸 (@geometry ， @g) 
幾何。Union ()                             | UnaryUnion (@geometry) 
幾何。Union (其他)                        | GUnion (@geometry ， @other) 
幾何。在 (g) 內                          | 在 (中 @geometry ， @g) 
>geometrycollection [i]                       | GeometryN (@geometryCollection 、 @i + 1) 
>geometrycollection 計數                    | NumGeometries (@geometryCollection) 
lineString 計數                            | X.numpoints (@lineString) 
lineString 端點                         | 端點 (@lineString) 
lineString. GetPointN (n)                      | PointN (@lineString 、 @n + 1) 
lineString. IsClosed                         | IsClosed (@lineString) 
lineString. IsRing                           | IsRing (@lineString) 
lineString. StartPoint                       | StartPoint (@lineString) 
multiLineString. IsClosed                    | IsClosed (@multiLineString) 
點。M                                     | M (@point) 
點。X                                     | X (@point) 
點。Y                                     | Y (@point) 
點。Z                                     | Z (@point) 
多邊形。ExteriorRing                        | ExteriorRing (@polygon) 
多邊形。GetInteriorRingN (n)                  | InteriorRingN (@polygon 、 @n + 1) 
多邊形。NumInteriorRings                    | NumInteriorRing (@polygon) 

## <a name="additional-resources"></a>其他資源

* [SpatiaLite 首頁](https://www.gaia-gis.it/fossil/libspatialite)
* [NetTopologySuite 檔](https://nettopologysuite.github.io/NetTopologySuite/)
