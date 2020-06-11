---
title: EF Core 5.0 中的重大變更-EF Core
author: bricelam
ms.date: 06/05/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 0ffe62d9b21dd901c2cdaf0ea5d416d14938497f
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/10/2020
ms.locfileid: "84666192"
---
# <a name="breaking-changes-in-ef-core-50"></a>EF Core 5.0 中的重大變更

下列 API 和行為變更可能會中斷現有的應用程式更新為 EF Core 5.0.0。

## <a name="summary"></a>摘要

| **重大變更**                                                                                                               | **影響** |
|:----------------------------------------------------------------------------------------------------------------------------------|------------|
| [已從 SQLite NTS 擴充功能移除 HasGeometricDimension 方法](#removed-hasgeometricdimension-method-from-sqlite-nts-extension) | 低        |

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a>已從 SQLite NTS 擴充功能移除 HasGeometricDimension 方法

[追蹤問題 #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

**舊的行為**

HasGeometricDimension 是用來在幾何資料行上啟用其他維度（Z 和 M）。 不過，它只會影響資料庫的建立。 不需要指定它來查詢具有其他維度的值。 當您插入或更新具有其他維度的值時，它也無法正確運作（[請參閱 #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)）。

**新的行為**

若要啟用插入和更新具有其他維度（Z 和 M）的 geometry 值，必須將維度指定為數據行類型名稱的一部分。 這更符合 SpatiaLite 的 AddGeometryColumn 函數的基礎行為。

**因此**

在資料行類型中指定維度之後使用 HasGeometricDimension 是不必要和多餘的，因此我們已完全移除 HasGeometricDimension。

**風險降低**

使用 `HasColumnType` 來指定維度：

```cs
modelBuilder.Entity<GeoEntity>(
    x =>
    {
        // Allow any GEOMETRY value with optional Z and M values
        x.Property(e => e.Geometry).HasColumnType("GEOMETRYZM");

        // Allow only POINT values with an optional Z value
        x.Property(e => e.Point).HasColumnType("POINTZ");
    });
```
