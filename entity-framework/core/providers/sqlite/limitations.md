---
title: SQLite 資料庫提供者-限制-EF Core
author: rowanmiller
ms.date: 04/09/2017
ms.assetid: 94ab4800-c460-4caa-a5e8-acdfee6e6ce2
uid: core/providers/sqlite/limitations
ms.openlocfilehash: eaa7d5b1496172e4f3821433a1cd098ee7e8b737
ms.sourcegitcommit: 9bd64a1a71b7f7aeb044aeecc7c4785b57db1ec9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/26/2019
ms.locfileid: "67394807"
---
# <a name="sqlite-ef-core-database-provider-limitations"></a><span data-ttu-id="af84e-102">SQLite EF Core 資料庫提供者的限制</span><span class="sxs-lookup"><span data-stu-id="af84e-102">SQLite EF Core Database Provider Limitations</span></span>

<span data-ttu-id="af84e-103">SQLite 提供者有幾項移轉限制。</span><span class="sxs-lookup"><span data-stu-id="af84e-103">The SQLite provider has a number of migrations limitations.</span></span> <span data-ttu-id="af84e-104">這些限制大部分都是基礎的 SQLite 資料庫引擎中的限制的結果，並不是特別針對 EF。</span><span class="sxs-lookup"><span data-stu-id="af84e-104">Most of these limitations are a result of limitations in the underlying SQLite database engine and are not specific to EF.</span></span>

## <a name="modeling-limitations"></a><span data-ttu-id="af84e-105">模型限制</span><span class="sxs-lookup"><span data-stu-id="af84e-105">Modeling limitations</span></span>

<span data-ttu-id="af84e-106">（Entity Framework 的關聯式資料庫提供者所共用） 的通用關聯式程式庫會定義 Api 模型化概念通用於大部分的關聯式資料庫引擎。</span><span class="sxs-lookup"><span data-stu-id="af84e-106">The common relational library (shared by Entity Framework relational database providers) defines APIs for modelling concepts that are common to most relational database engines.</span></span> <span data-ttu-id="af84e-107">SQLite 提供者不支援幾個這些概念。</span><span class="sxs-lookup"><span data-stu-id="af84e-107">A couple of these concepts are not supported by the SQLite provider.</span></span>

* <span data-ttu-id="af84e-108">結構描述</span><span class="sxs-lookup"><span data-stu-id="af84e-108">Schemas</span></span>
* <span data-ttu-id="af84e-109">序列</span><span class="sxs-lookup"><span data-stu-id="af84e-109">Sequences</span></span>
* <span data-ttu-id="af84e-110">計算資料行</span><span class="sxs-lookup"><span data-stu-id="af84e-110">Computed columns</span></span>

## <a name="query-limitations"></a><span data-ttu-id="af84e-111">查詢限制</span><span class="sxs-lookup"><span data-stu-id="af84e-111">Query limitations</span></span>

<span data-ttu-id="af84e-112">SQLite 原生不支援下列資料類型。</span><span class="sxs-lookup"><span data-stu-id="af84e-112">SQLite doesn't natively support the following data types.</span></span> <span data-ttu-id="af84e-113">EF Core 可以讀取和寫入這些類型，並查詢是否相等的值 (`where e.Property == value`) 也是支援。</span><span class="sxs-lookup"><span data-stu-id="af84e-113">EF Core can read and write values of these types, and querying for equality (`where e.Property == value`) is also support.</span></span> <span data-ttu-id="af84e-114">其他作業，不過，例如比較和排序會需要用戶端上的評估。</span><span class="sxs-lookup"><span data-stu-id="af84e-114">Other operations, however, like comparison and ordering will require evaluation on the client.</span></span>

* <span data-ttu-id="af84e-115">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="af84e-115">DateTimeOffset</span></span>
* <span data-ttu-id="af84e-116">Decimal</span><span class="sxs-lookup"><span data-stu-id="af84e-116">Decimal</span></span>
* <span data-ttu-id="af84e-117">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="af84e-117">TimeSpan</span></span>
* <span data-ttu-id="af84e-118">UInt64</span><span class="sxs-lookup"><span data-stu-id="af84e-118">UInt64</span></span>

<span data-ttu-id="af84e-119">而不是`DateTimeOffset`，我們建議使用日期時間值。</span><span class="sxs-lookup"><span data-stu-id="af84e-119">Instead of `DateTimeOffset`, we recommend using DateTime values.</span></span> <span data-ttu-id="af84e-120">當處理多個時區，我們建議將值轉換成 UTC，然後再儲存，並再轉換回適當的時區。</span><span class="sxs-lookup"><span data-stu-id="af84e-120">When handling multiple time zones, we recommend converting the values to UTC before saving and then converting back to the appropriate time zone.</span></span>

<span data-ttu-id="af84e-121">`Decimal`類型提供高層級的有效位數。</span><span class="sxs-lookup"><span data-stu-id="af84e-121">The `Decimal` type provides a high level of precision.</span></span> <span data-ttu-id="af84e-122">如果您不需要該層級的有效位數，不過，建議改為使用雙引號。</span><span class="sxs-lookup"><span data-stu-id="af84e-122">If you don't need that level of precision, however, we recommend using double instead.</span></span> <span data-ttu-id="af84e-123">您可以使用[值轉換器](../../modeling/value-conversions.md)繼續使用在類別中的十進位。</span><span class="sxs-lookup"><span data-stu-id="af84e-123">You can use a [value converter](../../modeling/value-conversions.md) to continue using decimal in your classes.</span></span>

``` csharp
modelBuilder.Entity<MyEntity>()
    .Property(e => e.DecimalProperty)
    .HasConversion<double>();
```

## <a name="migrations-limitations"></a><span data-ttu-id="af84e-124">移轉限制</span><span class="sxs-lookup"><span data-stu-id="af84e-124">Migrations limitations</span></span>

<span data-ttu-id="af84e-125">SQLite 資料庫引擎不支援結構描述作業大部分的其他關聯式資料庫所支援的數量。</span><span class="sxs-lookup"><span data-stu-id="af84e-125">The SQLite database engine does not support a number of schema operations that are supported by the majority of other relational databases.</span></span> <span data-ttu-id="af84e-126">如果您嘗試將其中一個不支援的作業套用至 SQLite 資料庫則`NotSupportedException`就會擲回。</span><span class="sxs-lookup"><span data-stu-id="af84e-126">If you attempt to apply one of the unsupported operations to a SQLite database then a `NotSupportedException` will be thrown.</span></span>

| <span data-ttu-id="af84e-127">運算</span><span class="sxs-lookup"><span data-stu-id="af84e-127">Operation</span></span>            | <span data-ttu-id="af84e-128">支援？</span><span class="sxs-lookup"><span data-stu-id="af84e-128">Supported?</span></span> | <span data-ttu-id="af84e-129">需要版本</span><span class="sxs-lookup"><span data-stu-id="af84e-129">Requires version</span></span> |
|:---------------------|:-----------|:-----------------|
| <span data-ttu-id="af84e-130">AddColumn</span><span class="sxs-lookup"><span data-stu-id="af84e-130">AddColumn</span></span>            | <span data-ttu-id="af84e-131">✔</span><span class="sxs-lookup"><span data-stu-id="af84e-131">✔</span></span>          | <span data-ttu-id="af84e-132">1.0</span><span class="sxs-lookup"><span data-stu-id="af84e-132">1.0</span></span>              |
| <span data-ttu-id="af84e-133">AddForeignKey</span><span class="sxs-lookup"><span data-stu-id="af84e-133">AddForeignKey</span></span>        | <span data-ttu-id="af84e-134">✗</span><span class="sxs-lookup"><span data-stu-id="af84e-134">✗</span></span>          |                  |
| <span data-ttu-id="af84e-135">AddPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="af84e-135">AddPrimaryKey</span></span>        | <span data-ttu-id="af84e-136">✗</span><span class="sxs-lookup"><span data-stu-id="af84e-136">✗</span></span>          |                  |
| <span data-ttu-id="af84e-137">AddUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="af84e-137">AddUniqueConstraint</span></span>  | <span data-ttu-id="af84e-138">✗</span><span class="sxs-lookup"><span data-stu-id="af84e-138">✗</span></span>          |                  |
| <span data-ttu-id="af84e-139">AlterColumn</span><span class="sxs-lookup"><span data-stu-id="af84e-139">AlterColumn</span></span>          | <span data-ttu-id="af84e-140">✗</span><span class="sxs-lookup"><span data-stu-id="af84e-140">✗</span></span>          |                  |
| <span data-ttu-id="af84e-141">CreateIndex</span><span class="sxs-lookup"><span data-stu-id="af84e-141">CreateIndex</span></span>          | <span data-ttu-id="af84e-142">✔</span><span class="sxs-lookup"><span data-stu-id="af84e-142">✔</span></span>          | <span data-ttu-id="af84e-143">1.0</span><span class="sxs-lookup"><span data-stu-id="af84e-143">1.0</span></span>              |
| <span data-ttu-id="af84e-144">CreateTable</span><span class="sxs-lookup"><span data-stu-id="af84e-144">CreateTable</span></span>          | <span data-ttu-id="af84e-145">✔</span><span class="sxs-lookup"><span data-stu-id="af84e-145">✔</span></span>          | <span data-ttu-id="af84e-146">1.0</span><span class="sxs-lookup"><span data-stu-id="af84e-146">1.0</span></span>              |
| <span data-ttu-id="af84e-147">DropColumn</span><span class="sxs-lookup"><span data-stu-id="af84e-147">DropColumn</span></span>           | <span data-ttu-id="af84e-148">✗</span><span class="sxs-lookup"><span data-stu-id="af84e-148">✗</span></span>          |                  |
| <span data-ttu-id="af84e-149">DropForeignKey</span><span class="sxs-lookup"><span data-stu-id="af84e-149">DropForeignKey</span></span>       | <span data-ttu-id="af84e-150">✗</span><span class="sxs-lookup"><span data-stu-id="af84e-150">✗</span></span>          |                  |
| <span data-ttu-id="af84e-151">DropIndex</span><span class="sxs-lookup"><span data-stu-id="af84e-151">DropIndex</span></span>            | <span data-ttu-id="af84e-152">✔</span><span class="sxs-lookup"><span data-stu-id="af84e-152">✔</span></span>          | <span data-ttu-id="af84e-153">1.0</span><span class="sxs-lookup"><span data-stu-id="af84e-153">1.0</span></span>              |
| <span data-ttu-id="af84e-154">DropPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="af84e-154">DropPrimaryKey</span></span>       | <span data-ttu-id="af84e-155">✗</span><span class="sxs-lookup"><span data-stu-id="af84e-155">✗</span></span>          |                  |
| <span data-ttu-id="af84e-156">DropTable</span><span class="sxs-lookup"><span data-stu-id="af84e-156">DropTable</span></span>            | <span data-ttu-id="af84e-157">✔</span><span class="sxs-lookup"><span data-stu-id="af84e-157">✔</span></span>          | <span data-ttu-id="af84e-158">1.0</span><span class="sxs-lookup"><span data-stu-id="af84e-158">1.0</span></span>              |
| <span data-ttu-id="af84e-159">DropUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="af84e-159">DropUniqueConstraint</span></span> | <span data-ttu-id="af84e-160">✗</span><span class="sxs-lookup"><span data-stu-id="af84e-160">✗</span></span>          |                  |
| <span data-ttu-id="af84e-161">RenameColumn</span><span class="sxs-lookup"><span data-stu-id="af84e-161">RenameColumn</span></span>         | <span data-ttu-id="af84e-162">✔</span><span class="sxs-lookup"><span data-stu-id="af84e-162">✔</span></span>          | <span data-ttu-id="af84e-163">2.2.2</span><span class="sxs-lookup"><span data-stu-id="af84e-163">2.2.2</span></span>            |
| <span data-ttu-id="af84e-164">RenameIndex</span><span class="sxs-lookup"><span data-stu-id="af84e-164">RenameIndex</span></span>          | <span data-ttu-id="af84e-165">✔</span><span class="sxs-lookup"><span data-stu-id="af84e-165">✔</span></span>          | <span data-ttu-id="af84e-166">2.1</span><span class="sxs-lookup"><span data-stu-id="af84e-166">2.1</span></span>              |
| <span data-ttu-id="af84e-167">RenameTable</span><span class="sxs-lookup"><span data-stu-id="af84e-167">RenameTable</span></span>          | <span data-ttu-id="af84e-168">✔</span><span class="sxs-lookup"><span data-stu-id="af84e-168">✔</span></span>          | <span data-ttu-id="af84e-169">1.0</span><span class="sxs-lookup"><span data-stu-id="af84e-169">1.0</span></span>              |
| <span data-ttu-id="af84e-170">EnsureSchema</span><span class="sxs-lookup"><span data-stu-id="af84e-170">EnsureSchema</span></span>         | <span data-ttu-id="af84e-171">✔ (no-op)</span><span class="sxs-lookup"><span data-stu-id="af84e-171">✔ (no-op)</span></span>  | <span data-ttu-id="af84e-172">2.0</span><span class="sxs-lookup"><span data-stu-id="af84e-172">2.0</span></span>              |
| <span data-ttu-id="af84e-173">DropSchema</span><span class="sxs-lookup"><span data-stu-id="af84e-173">DropSchema</span></span>           | <span data-ttu-id="af84e-174">✔ (no-op)</span><span class="sxs-lookup"><span data-stu-id="af84e-174">✔ (no-op)</span></span>  | <span data-ttu-id="af84e-175">2.0</span><span class="sxs-lookup"><span data-stu-id="af84e-175">2.0</span></span>              |
| <span data-ttu-id="af84e-176">Insert</span><span class="sxs-lookup"><span data-stu-id="af84e-176">Insert</span></span>               | <span data-ttu-id="af84e-177">✔</span><span class="sxs-lookup"><span data-stu-id="af84e-177">✔</span></span>          | <span data-ttu-id="af84e-178">2.0</span><span class="sxs-lookup"><span data-stu-id="af84e-178">2.0</span></span>              |
| <span data-ttu-id="af84e-179">更新</span><span class="sxs-lookup"><span data-stu-id="af84e-179">Update</span></span>               | <span data-ttu-id="af84e-180">✔</span><span class="sxs-lookup"><span data-stu-id="af84e-180">✔</span></span>          | <span data-ttu-id="af84e-181">2.0</span><span class="sxs-lookup"><span data-stu-id="af84e-181">2.0</span></span>              |
| <span data-ttu-id="af84e-182">刪除</span><span class="sxs-lookup"><span data-stu-id="af84e-182">Delete</span></span>               | <span data-ttu-id="af84e-183">✔</span><span class="sxs-lookup"><span data-stu-id="af84e-183">✔</span></span>          | <span data-ttu-id="af84e-184">2.0</span><span class="sxs-lookup"><span data-stu-id="af84e-184">2.0</span></span>              |

## <a name="migrations-limitations-workaround"></a><span data-ttu-id="af84e-185">移轉限制的因應措施</span><span class="sxs-lookup"><span data-stu-id="af84e-185">Migrations limitations workaround</span></span>

<span data-ttu-id="af84e-186">您可以因應措施是一些手動撰寫的程式碼在您移轉來執行資料表中的這些限制的重建。</span><span class="sxs-lookup"><span data-stu-id="af84e-186">You can workaround some of these limitations by manually writing code in your migrations to perform a table rebuild.</span></span> <span data-ttu-id="af84e-187">重建資料表需要重新命名現有的資料表、建立新的資料表、將資料複製到新的資料表，以及卸除舊的資料表。</span><span class="sxs-lookup"><span data-stu-id="af84e-187">A table rebuild involves renaming the existing table, creating a new table, copying data to the new table, and dropping the old table.</span></span> <span data-ttu-id="af84e-188">您必須使用`Sql(string)`來執行這些步驟的某些方法。</span><span class="sxs-lookup"><span data-stu-id="af84e-188">You will need to use the `Sql(string)` method to perform some of these steps.</span></span>

<span data-ttu-id="af84e-189">請參閱[進行其他類型的資料表結構描述變更](http://sqlite.org/lang_altertable.html#otheralter)SQLite 文件以取得詳細資料中。</span><span class="sxs-lookup"><span data-stu-id="af84e-189">See [Making Other Kinds Of Table Schema Changes](http://sqlite.org/lang_altertable.html#otheralter) in the SQLite documentation for more details.</span></span>

<span data-ttu-id="af84e-190">在未來，EF 可能支援這些作業的一些使用在幕後的資料表重建方法。</span><span class="sxs-lookup"><span data-stu-id="af84e-190">In the future, EF may support some of these operations by using the table rebuild approach under the covers.</span></span> <span data-ttu-id="af84e-191">您可以[追蹤這項功能，在我們的 GitHub 專案](https://github.com/aspnet/EntityFrameworkCore/issues/329)。</span><span class="sxs-lookup"><span data-stu-id="af84e-191">You can [track this feature on our GitHub project](https://github.com/aspnet/EntityFrameworkCore/issues/329).</span></span>
