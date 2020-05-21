---
title: SQLite 資料庫提供者-限制-EF Core
author: rowanmiller
ms.date: 04/09/2017
ms.assetid: 94ab4800-c460-4caa-a5e8-acdfee6e6ce2
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 17e97da9dfffefeb507fde744b710e6936bff69b
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672783"
---
# <a name="sqlite-ef-core-database-provider-limitations"></a><span data-ttu-id="1970a-102">SQLite EF Core 資料庫提供者限制</span><span class="sxs-lookup"><span data-stu-id="1970a-102">SQLite EF Core Database Provider Limitations</span></span>

<span data-ttu-id="1970a-103">SQLite 提供者有許多遷移限制。</span><span class="sxs-lookup"><span data-stu-id="1970a-103">The SQLite provider has a number of migrations limitations.</span></span> <span data-ttu-id="1970a-104">這些限制大多是基礎 SQLite 資料庫引擎限制的結果，而不是 EF 特有的。</span><span class="sxs-lookup"><span data-stu-id="1970a-104">Most of these limitations are a result of limitations in the underlying SQLite database engine and are not specific to EF.</span></span>

## <a name="modeling-limitations"></a><span data-ttu-id="1970a-105">模型限制</span><span class="sxs-lookup"><span data-stu-id="1970a-105">Modeling limitations</span></span>

<span data-ttu-id="1970a-106">通用關聯式程式庫（由 Entity Framework 關係資料庫提供者共用）定義了適用于大部分關係資料庫引擎通用概念的 Api。</span><span class="sxs-lookup"><span data-stu-id="1970a-106">The common relational library (shared by Entity Framework relational database providers) defines APIs for modelling concepts that are common to most relational database engines.</span></span> <span data-ttu-id="1970a-107">SQLite 提供者不支援其中幾個概念。</span><span class="sxs-lookup"><span data-stu-id="1970a-107">A couple of these concepts are not supported by the SQLite provider.</span></span>

* <span data-ttu-id="1970a-108">結構描述</span><span class="sxs-lookup"><span data-stu-id="1970a-108">Schemas</span></span>
* <span data-ttu-id="1970a-109">序列</span><span class="sxs-lookup"><span data-stu-id="1970a-109">Sequences</span></span>

## <a name="query-limitations"></a><span data-ttu-id="1970a-110">查詢限制</span><span class="sxs-lookup"><span data-stu-id="1970a-110">Query limitations</span></span>

<span data-ttu-id="1970a-111">SQLite 原本就不支援下列資料類型。</span><span class="sxs-lookup"><span data-stu-id="1970a-111">SQLite doesn't natively support the following data types.</span></span> <span data-ttu-id="1970a-112">EF Core 可以讀取和寫入這些類型的值，也支援查詢是否相等（ `where e.Property == value` ）。</span><span class="sxs-lookup"><span data-stu-id="1970a-112">EF Core can read and write values of these types, and querying for equality (`where e.Property == value`) is also supported.</span></span> <span data-ttu-id="1970a-113">不過，其他作業（例如比較和順序）將需要用戶端上的評估。</span><span class="sxs-lookup"><span data-stu-id="1970a-113">Other operations, however, like comparison and ordering will require evaluation on the client.</span></span>

* <span data-ttu-id="1970a-114">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="1970a-114">DateTimeOffset</span></span>
* <span data-ttu-id="1970a-115">Decimal</span><span class="sxs-lookup"><span data-stu-id="1970a-115">Decimal</span></span>
* <span data-ttu-id="1970a-116">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="1970a-116">TimeSpan</span></span>
* <span data-ttu-id="1970a-117">UInt64</span><span class="sxs-lookup"><span data-stu-id="1970a-117">UInt64</span></span>

<span data-ttu-id="1970a-118">`DateTimeOffset`我們建議使用 DateTime 值，而不是。</span><span class="sxs-lookup"><span data-stu-id="1970a-118">Instead of `DateTimeOffset`, we recommend using DateTime values.</span></span> <span data-ttu-id="1970a-119">處理多個時區時，建議您先將值轉換成 UTC，再儲存，然後再轉換回適當的時區。</span><span class="sxs-lookup"><span data-stu-id="1970a-119">When handling multiple time zones, we recommend converting the values to UTC before saving and then converting back to the appropriate time zone.</span></span>

<span data-ttu-id="1970a-120">`Decimal`型別提供高程度的精確度。</span><span class="sxs-lookup"><span data-stu-id="1970a-120">The `Decimal` type provides a high level of precision.</span></span> <span data-ttu-id="1970a-121">不過，如果您不需要該有效位數層級，我們建議您改為使用 double。</span><span class="sxs-lookup"><span data-stu-id="1970a-121">If you don't need that level of precision, however, we recommend using double instead.</span></span> <span data-ttu-id="1970a-122">您可以使用[值轉換器](../../modeling/value-conversions.md)，在您的類別中繼續使用 decimal。</span><span class="sxs-lookup"><span data-stu-id="1970a-122">You can use a [value converter](../../modeling/value-conversions.md) to continue using decimal in your classes.</span></span>

``` csharp
modelBuilder.Entity<MyEntity>()
    .Property(e => e.DecimalProperty)
    .HasConversion<double>();
```

## <a name="migrations-limitations"></a><span data-ttu-id="1970a-123">遷移限制</span><span class="sxs-lookup"><span data-stu-id="1970a-123">Migrations limitations</span></span>

<span data-ttu-id="1970a-124">SQLite 資料庫引擎不支援大多數其他關係資料庫所支援的許多架構作業。</span><span class="sxs-lookup"><span data-stu-id="1970a-124">The SQLite database engine does not support a number of schema operations that are supported by the majority of other relational databases.</span></span> <span data-ttu-id="1970a-125">如果您嘗試將其中一個不支援的作業套用至 SQLite 資料庫， `NotSupportedException` 將會擲回。</span><span class="sxs-lookup"><span data-stu-id="1970a-125">If you attempt to apply one of the unsupported operations to a SQLite database then a `NotSupportedException` will be thrown.</span></span>

| <span data-ttu-id="1970a-126">作業</span><span class="sxs-lookup"><span data-stu-id="1970a-126">Operation</span></span>            | <span data-ttu-id="1970a-127">是否支援？</span><span class="sxs-lookup"><span data-stu-id="1970a-127">Supported?</span></span> | <span data-ttu-id="1970a-128">需要版本</span><span class="sxs-lookup"><span data-stu-id="1970a-128">Requires version</span></span> |
|:---------------------|:-----------|:-----------------|
| <span data-ttu-id="1970a-129">AddColumn</span><span class="sxs-lookup"><span data-stu-id="1970a-129">AddColumn</span></span>            | <span data-ttu-id="1970a-130">✔</span><span class="sxs-lookup"><span data-stu-id="1970a-130">✔</span></span>          | <span data-ttu-id="1970a-131">1.0</span><span class="sxs-lookup"><span data-stu-id="1970a-131">1.0</span></span>              |
| <span data-ttu-id="1970a-132">AddForeignKey</span><span class="sxs-lookup"><span data-stu-id="1970a-132">AddForeignKey</span></span>        | <span data-ttu-id="1970a-133">✗</span><span class="sxs-lookup"><span data-stu-id="1970a-133">✗</span></span>          |                  |
| <span data-ttu-id="1970a-134">AddPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="1970a-134">AddPrimaryKey</span></span>        | <span data-ttu-id="1970a-135">✗</span><span class="sxs-lookup"><span data-stu-id="1970a-135">✗</span></span>          |                  |
| <span data-ttu-id="1970a-136">AddUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="1970a-136">AddUniqueConstraint</span></span>  | <span data-ttu-id="1970a-137">✗</span><span class="sxs-lookup"><span data-stu-id="1970a-137">✗</span></span>          |                  |
| <span data-ttu-id="1970a-138">AlterColumn</span><span class="sxs-lookup"><span data-stu-id="1970a-138">AlterColumn</span></span>          | <span data-ttu-id="1970a-139">✗</span><span class="sxs-lookup"><span data-stu-id="1970a-139">✗</span></span>          |                  |
| <span data-ttu-id="1970a-140">CreateIndex</span><span class="sxs-lookup"><span data-stu-id="1970a-140">CreateIndex</span></span>          | <span data-ttu-id="1970a-141">✔</span><span class="sxs-lookup"><span data-stu-id="1970a-141">✔</span></span>          | <span data-ttu-id="1970a-142">1.0</span><span class="sxs-lookup"><span data-stu-id="1970a-142">1.0</span></span>              |
| <span data-ttu-id="1970a-143">CreateTable</span><span class="sxs-lookup"><span data-stu-id="1970a-143">CreateTable</span></span>          | <span data-ttu-id="1970a-144">✔</span><span class="sxs-lookup"><span data-stu-id="1970a-144">✔</span></span>          | <span data-ttu-id="1970a-145">1.0</span><span class="sxs-lookup"><span data-stu-id="1970a-145">1.0</span></span>              |
| <span data-ttu-id="1970a-146">DropColumn</span><span class="sxs-lookup"><span data-stu-id="1970a-146">DropColumn</span></span>           | <span data-ttu-id="1970a-147">✗</span><span class="sxs-lookup"><span data-stu-id="1970a-147">✗</span></span>          |                  |
| <span data-ttu-id="1970a-148">DropForeignKey</span><span class="sxs-lookup"><span data-stu-id="1970a-148">DropForeignKey</span></span>       | <span data-ttu-id="1970a-149">✗</span><span class="sxs-lookup"><span data-stu-id="1970a-149">✗</span></span>          |                  |
| <span data-ttu-id="1970a-150">DropIndex</span><span class="sxs-lookup"><span data-stu-id="1970a-150">DropIndex</span></span>            | <span data-ttu-id="1970a-151">✔</span><span class="sxs-lookup"><span data-stu-id="1970a-151">✔</span></span>          | <span data-ttu-id="1970a-152">1.0</span><span class="sxs-lookup"><span data-stu-id="1970a-152">1.0</span></span>              |
| <span data-ttu-id="1970a-153">DropPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="1970a-153">DropPrimaryKey</span></span>       | <span data-ttu-id="1970a-154">✗</span><span class="sxs-lookup"><span data-stu-id="1970a-154">✗</span></span>          |                  |
| <span data-ttu-id="1970a-155">DropTable</span><span class="sxs-lookup"><span data-stu-id="1970a-155">DropTable</span></span>            | <span data-ttu-id="1970a-156">✔</span><span class="sxs-lookup"><span data-stu-id="1970a-156">✔</span></span>          | <span data-ttu-id="1970a-157">1.0</span><span class="sxs-lookup"><span data-stu-id="1970a-157">1.0</span></span>              |
| <span data-ttu-id="1970a-158">DropUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="1970a-158">DropUniqueConstraint</span></span> | <span data-ttu-id="1970a-159">✗</span><span class="sxs-lookup"><span data-stu-id="1970a-159">✗</span></span>          |                  |
| <span data-ttu-id="1970a-160">RenameColumn</span><span class="sxs-lookup"><span data-stu-id="1970a-160">RenameColumn</span></span>         | <span data-ttu-id="1970a-161">✔</span><span class="sxs-lookup"><span data-stu-id="1970a-161">✔</span></span>          | <span data-ttu-id="1970a-162">2.2.2</span><span class="sxs-lookup"><span data-stu-id="1970a-162">2.2.2</span></span>            |
| <span data-ttu-id="1970a-163">RenameIndex</span><span class="sxs-lookup"><span data-stu-id="1970a-163">RenameIndex</span></span>          | <span data-ttu-id="1970a-164">✔</span><span class="sxs-lookup"><span data-stu-id="1970a-164">✔</span></span>          | <span data-ttu-id="1970a-165">2.1</span><span class="sxs-lookup"><span data-stu-id="1970a-165">2.1</span></span>              |
| <span data-ttu-id="1970a-166">RenameTable</span><span class="sxs-lookup"><span data-stu-id="1970a-166">RenameTable</span></span>          | <span data-ttu-id="1970a-167">✔</span><span class="sxs-lookup"><span data-stu-id="1970a-167">✔</span></span>          | <span data-ttu-id="1970a-168">1.0</span><span class="sxs-lookup"><span data-stu-id="1970a-168">1.0</span></span>              |
| <span data-ttu-id="1970a-169">EnsureSchema</span><span class="sxs-lookup"><span data-stu-id="1970a-169">EnsureSchema</span></span>         | <span data-ttu-id="1970a-170">✔（無 op）</span><span class="sxs-lookup"><span data-stu-id="1970a-170">✔ (no-op)</span></span>  | <span data-ttu-id="1970a-171">2.0</span><span class="sxs-lookup"><span data-stu-id="1970a-171">2.0</span></span>              |
| <span data-ttu-id="1970a-172">DropSchema</span><span class="sxs-lookup"><span data-stu-id="1970a-172">DropSchema</span></span>           | <span data-ttu-id="1970a-173">✔（無 op）</span><span class="sxs-lookup"><span data-stu-id="1970a-173">✔ (no-op)</span></span>  | <span data-ttu-id="1970a-174">2.0</span><span class="sxs-lookup"><span data-stu-id="1970a-174">2.0</span></span>              |
| <span data-ttu-id="1970a-175">插入</span><span class="sxs-lookup"><span data-stu-id="1970a-175">Insert</span></span>               | <span data-ttu-id="1970a-176">✔</span><span class="sxs-lookup"><span data-stu-id="1970a-176">✔</span></span>          | <span data-ttu-id="1970a-177">2.0</span><span class="sxs-lookup"><span data-stu-id="1970a-177">2.0</span></span>              |
| <span data-ttu-id="1970a-178">更新</span><span class="sxs-lookup"><span data-stu-id="1970a-178">Update</span></span>               | <span data-ttu-id="1970a-179">✔</span><span class="sxs-lookup"><span data-stu-id="1970a-179">✔</span></span>          | <span data-ttu-id="1970a-180">2.0</span><span class="sxs-lookup"><span data-stu-id="1970a-180">2.0</span></span>              |
| <span data-ttu-id="1970a-181">刪除</span><span class="sxs-lookup"><span data-stu-id="1970a-181">Delete</span></span>               | <span data-ttu-id="1970a-182">✔</span><span class="sxs-lookup"><span data-stu-id="1970a-182">✔</span></span>          | <span data-ttu-id="1970a-183">2.0</span><span class="sxs-lookup"><span data-stu-id="1970a-183">2.0</span></span>              |

## <a name="migrations-limitations-workaround"></a><span data-ttu-id="1970a-184">遷移限制解決方法</span><span class="sxs-lookup"><span data-stu-id="1970a-184">Migrations limitations workaround</span></span>

<span data-ttu-id="1970a-185">您可以藉由在您的遷移中手動撰寫程式碼來執行資料表重建，以解決其中一些限制。</span><span class="sxs-lookup"><span data-stu-id="1970a-185">You can workaround some of these limitations by manually writing code in your migrations to perform a table rebuild.</span></span> <span data-ttu-id="1970a-186">重建資料表需要重新命名現有的資料表、建立新的資料表、將資料複製到新的資料表，以及卸除舊的資料表。</span><span class="sxs-lookup"><span data-stu-id="1970a-186">A table rebuild involves renaming the existing table, creating a new table, copying data to the new table, and dropping the old table.</span></span> <span data-ttu-id="1970a-187">您將需要使用 `Sql(string)` 方法來執行其中一些步驟。</span><span class="sxs-lookup"><span data-stu-id="1970a-187">You will need to use the `Sql(string)` method to perform some of these steps.</span></span>

<span data-ttu-id="1970a-188">如需詳細資訊，請參閱 SQLite 檔中的[建立其他類型的資料表架構變更](https://sqlite.org/lang_altertable.html#otheralter)。</span><span class="sxs-lookup"><span data-stu-id="1970a-188">See [Making Other Kinds Of Table Schema Changes](https://sqlite.org/lang_altertable.html#otheralter) in the SQLite documentation for more details.</span></span>

<span data-ttu-id="1970a-189">在未來，EF 可能會使用部分的資料表重建方法來支援其中某些作業。</span><span class="sxs-lookup"><span data-stu-id="1970a-189">In the future, EF may support some of these operations by using the table rebuild approach under the covers.</span></span> <span data-ttu-id="1970a-190">您可以[在我們的 GitHub 專案上追蹤這項功能](https://github.com/aspnet/EntityFrameworkCore/issues/329)。</span><span class="sxs-lookup"><span data-stu-id="1970a-190">You can [track this feature on our GitHub project](https://github.com/aspnet/EntityFrameworkCore/issues/329).</span></span>
