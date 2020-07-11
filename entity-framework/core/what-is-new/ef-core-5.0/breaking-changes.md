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
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="dd29a-102">EF Core 5.0 中的重大變更</span><span class="sxs-lookup"><span data-stu-id="dd29a-102">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="dd29a-103">下列 API 和行為變更可能會中斷現有的應用程式更新為 EF Core 5.0.0。</span><span class="sxs-lookup"><span data-stu-id="dd29a-103">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="dd29a-104">摘要</span><span class="sxs-lookup"><span data-stu-id="dd29a-104">Summary</span></span>

| <span data-ttu-id="dd29a-105">**重大變更**</span><span class="sxs-lookup"><span data-stu-id="dd29a-105">**Breaking change**</span></span>                                                                                                               | <span data-ttu-id="dd29a-106">**影響**</span><span class="sxs-lookup"><span data-stu-id="dd29a-106">**Impact**</span></span> |
|:----------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="dd29a-107">已從 SQLite NTS 擴充功能移除 HasGeometricDimension 方法</span><span class="sxs-lookup"><span data-stu-id="dd29a-107">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#removed-hasgeometricdimension-method-from-sqlite-nts-extension) | <span data-ttu-id="dd29a-108">低</span><span class="sxs-lookup"><span data-stu-id="dd29a-108">Low</span></span>        |

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="dd29a-109">已從 SQLite NTS 擴充功能移除 HasGeometricDimension 方法</span><span class="sxs-lookup"><span data-stu-id="dd29a-109">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="dd29a-110">追蹤問題 #14257</span><span class="sxs-lookup"><span data-stu-id="dd29a-110">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="dd29a-111">**舊的行為**</span><span class="sxs-lookup"><span data-stu-id="dd29a-111">**Old behavior**</span></span>

<span data-ttu-id="dd29a-112">HasGeometricDimension 是用來在幾何資料行上啟用其他維度（Z 和 M）。</span><span class="sxs-lookup"><span data-stu-id="dd29a-112">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="dd29a-113">不過，它只會影響資料庫的建立。</span><span class="sxs-lookup"><span data-stu-id="dd29a-113">However, it only ever affected database creation.</span></span> <span data-ttu-id="dd29a-114">不需要指定它來查詢具有其他維度的值。</span><span class="sxs-lookup"><span data-stu-id="dd29a-114">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="dd29a-115">當您插入或更新具有其他維度的值時，它也無法正確運作（[請參閱 #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)）。</span><span class="sxs-lookup"><span data-stu-id="dd29a-115">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="dd29a-116">**新的行為**</span><span class="sxs-lookup"><span data-stu-id="dd29a-116">**New behavior**</span></span>

<span data-ttu-id="dd29a-117">若要啟用插入和更新具有其他維度（Z 和 M）的 geometry 值，必須將維度指定為數據行類型名稱的一部分。</span><span class="sxs-lookup"><span data-stu-id="dd29a-117">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="dd29a-118">這更符合 SpatiaLite 的 AddGeometryColumn 函數的基礎行為。</span><span class="sxs-lookup"><span data-stu-id="dd29a-118">This more closely matches the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="dd29a-119">**因此**</span><span class="sxs-lookup"><span data-stu-id="dd29a-119">**Why**</span></span>

<span data-ttu-id="dd29a-120">在資料行類型中指定維度之後使用 HasGeometricDimension 是不必要和多餘的，因此我們已完全移除 HasGeometricDimension。</span><span class="sxs-lookup"><span data-stu-id="dd29a-120">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="dd29a-121">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="dd29a-121">**Mitigations**</span></span>

<span data-ttu-id="dd29a-122">使用 `HasColumnType` 來指定維度：</span><span class="sxs-lookup"><span data-stu-id="dd29a-122">Use `HasColumnType` to specify the dimension:</span></span>

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