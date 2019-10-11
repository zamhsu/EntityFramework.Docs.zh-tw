---
title: SQLite 資料庫提供者-限制-EF Core
author: rowanmiller
ms.date: 04/09/2017
ms.assetid: 94ab4800-c460-4caa-a5e8-acdfee6e6ce2
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 2f80dc195265787318ac4925dd937da45ffad011
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72179776"
---
# <a name="sqlite-ef-core-database-provider-limitations"></a><span data-ttu-id="18481-102">SQLite EF Core 資料庫提供者的限制</span><span class="sxs-lookup"><span data-stu-id="18481-102">SQLite EF Core Database Provider Limitations</span></span>

<span data-ttu-id="18481-103">SQLite 提供者有許多遷移限制。</span><span class="sxs-lookup"><span data-stu-id="18481-103">The SQLite provider has a number of migrations limitations.</span></span> <span data-ttu-id="18481-104">這些限制大多是基礎 SQLite 資料庫引擎限制的結果，而不是 EF 特有的。</span><span class="sxs-lookup"><span data-stu-id="18481-104">Most of these limitations are a result of limitations in the underlying SQLite database engine and are not specific to EF.</span></span>

## <a name="modeling-limitations"></a><span data-ttu-id="18481-105">模型限制</span><span class="sxs-lookup"><span data-stu-id="18481-105">Modeling limitations</span></span>

<span data-ttu-id="18481-106">通用關聯式程式庫（由 Entity Framework 關係資料庫提供者共用）定義了適用于大部分關係資料庫引擎通用概念的 Api。</span><span class="sxs-lookup"><span data-stu-id="18481-106">The common relational library (shared by Entity Framework relational database providers) defines APIs for modelling concepts that are common to most relational database engines.</span></span> <span data-ttu-id="18481-107">SQLite 提供者不支援其中幾個概念。</span><span class="sxs-lookup"><span data-stu-id="18481-107">A couple of these concepts are not supported by the SQLite provider.</span></span>

* <span data-ttu-id="18481-108">結構描述</span><span class="sxs-lookup"><span data-stu-id="18481-108">Schemas</span></span>
* <span data-ttu-id="18481-109">序列</span><span class="sxs-lookup"><span data-stu-id="18481-109">Sequences</span></span>
* <span data-ttu-id="18481-110">計算資料行</span><span class="sxs-lookup"><span data-stu-id="18481-110">Computed columns</span></span>

## <a name="query-limitations"></a><span data-ttu-id="18481-111">查詢限制</span><span class="sxs-lookup"><span data-stu-id="18481-111">Query limitations</span></span>

<span data-ttu-id="18481-112">SQLite 原本就不支援下列資料類型。</span><span class="sxs-lookup"><span data-stu-id="18481-112">SQLite doesn't natively support the following data types.</span></span> <span data-ttu-id="18481-113">EF Core 可以讀取和寫入這些類型的值，也支援查詢是否相等（`where e.Property == value`）。</span><span class="sxs-lookup"><span data-stu-id="18481-113">EF Core can read and write values of these types, and querying for equality (`where e.Property == value`) is also supported.</span></span> <span data-ttu-id="18481-114">不過，其他作業（例如比較和順序）將需要用戶端上的評估。</span><span class="sxs-lookup"><span data-stu-id="18481-114">Other operations, however, like comparison and ordering will require evaluation on the client.</span></span>

* <span data-ttu-id="18481-115">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="18481-115">DateTimeOffset</span></span>
* <span data-ttu-id="18481-116">Decimal</span><span class="sxs-lookup"><span data-stu-id="18481-116">Decimal</span></span>
* <span data-ttu-id="18481-117">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="18481-117">TimeSpan</span></span>
* <span data-ttu-id="18481-118">UInt64</span><span class="sxs-lookup"><span data-stu-id="18481-118">UInt64</span></span>

<span data-ttu-id="18481-119">我們建議使用 DateTime 值，而不是 `DateTimeOffset`。</span><span class="sxs-lookup"><span data-stu-id="18481-119">Instead of `DateTimeOffset`, we recommend using DateTime values.</span></span> <span data-ttu-id="18481-120">處理多個時區時，建議您先將值轉換成 UTC，再儲存，然後再轉換回適當的時區。</span><span class="sxs-lookup"><span data-stu-id="18481-120">When handling multiple time zones, we recommend converting the values to UTC before saving and then converting back to the appropriate time zone.</span></span>

<span data-ttu-id="18481-121">@No__t-0 類型提供高的精確度。</span><span class="sxs-lookup"><span data-stu-id="18481-121">The `Decimal` type provides a high level of precision.</span></span> <span data-ttu-id="18481-122">不過，如果您不需要該有效位數層級，我們建議您改為使用 double。</span><span class="sxs-lookup"><span data-stu-id="18481-122">If you don't need that level of precision, however, we recommend using double instead.</span></span> <span data-ttu-id="18481-123">您可以使用[值轉換器](../../modeling/value-conversions.md)，在您的類別中繼續使用 decimal。</span><span class="sxs-lookup"><span data-stu-id="18481-123">You can use a [value converter](../../modeling/value-conversions.md) to continue using decimal in your classes.</span></span>

``` csharp
modelBuilder.Entity<MyEntity>()
    .Property(e => e.DecimalProperty)
    .HasConversion<double>();
```

## <a name="migrations-limitations"></a><span data-ttu-id="18481-124">遷移限制</span><span class="sxs-lookup"><span data-stu-id="18481-124">Migrations limitations</span></span>

<span data-ttu-id="18481-125">SQLite 資料庫引擎不支援大多數其他關係資料庫所支援的許多架構作業。</span><span class="sxs-lookup"><span data-stu-id="18481-125">The SQLite database engine does not support a number of schema operations that are supported by the majority of other relational databases.</span></span> <span data-ttu-id="18481-126">如果您嘗試將其中一個不支援的作業套用至 SQLite 資料庫，將會擲回 `NotSupportedException`。</span><span class="sxs-lookup"><span data-stu-id="18481-126">If you attempt to apply one of the unsupported operations to a SQLite database then a `NotSupportedException` will be thrown.</span></span>

| <span data-ttu-id="18481-127">運算</span><span class="sxs-lookup"><span data-stu-id="18481-127">Operation</span></span>            | <span data-ttu-id="18481-128">支援?</span><span class="sxs-lookup"><span data-stu-id="18481-128">Supported?</span></span> | <span data-ttu-id="18481-129">需要版本</span><span class="sxs-lookup"><span data-stu-id="18481-129">Requires version</span></span> |
|:---------------------|:-----------|:-----------------|
| <span data-ttu-id="18481-130">AddColumn</span><span class="sxs-lookup"><span data-stu-id="18481-130">AddColumn</span></span>            | <span data-ttu-id="18481-131">✔</span><span class="sxs-lookup"><span data-stu-id="18481-131">✔</span></span>          | <span data-ttu-id="18481-132">1.0</span><span class="sxs-lookup"><span data-stu-id="18481-132">1.0</span></span>              |
| <span data-ttu-id="18481-133">AddForeignKey</span><span class="sxs-lookup"><span data-stu-id="18481-133">AddForeignKey</span></span>        | <span data-ttu-id="18481-134">✗</span><span class="sxs-lookup"><span data-stu-id="18481-134">✗</span></span>          |                  |
| <span data-ttu-id="18481-135">AddPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="18481-135">AddPrimaryKey</span></span>        | <span data-ttu-id="18481-136">✗</span><span class="sxs-lookup"><span data-stu-id="18481-136">✗</span></span>          |                  |
| <span data-ttu-id="18481-137">AddUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="18481-137">AddUniqueConstraint</span></span>  | <span data-ttu-id="18481-138">✗</span><span class="sxs-lookup"><span data-stu-id="18481-138">✗</span></span>          |                  |
| <span data-ttu-id="18481-139">AlterColumn</span><span class="sxs-lookup"><span data-stu-id="18481-139">AlterColumn</span></span>          | <span data-ttu-id="18481-140">✗</span><span class="sxs-lookup"><span data-stu-id="18481-140">✗</span></span>          |                  |
| <span data-ttu-id="18481-141">CreateIndex</span><span class="sxs-lookup"><span data-stu-id="18481-141">CreateIndex</span></span>          | <span data-ttu-id="18481-142">✔</span><span class="sxs-lookup"><span data-stu-id="18481-142">✔</span></span>          | <span data-ttu-id="18481-143">1.0</span><span class="sxs-lookup"><span data-stu-id="18481-143">1.0</span></span>              |
| <span data-ttu-id="18481-144">CreateTable</span><span class="sxs-lookup"><span data-stu-id="18481-144">CreateTable</span></span>          | <span data-ttu-id="18481-145">✔</span><span class="sxs-lookup"><span data-stu-id="18481-145">✔</span></span>          | <span data-ttu-id="18481-146">1.0</span><span class="sxs-lookup"><span data-stu-id="18481-146">1.0</span></span>              |
| <span data-ttu-id="18481-147">DropColumn</span><span class="sxs-lookup"><span data-stu-id="18481-147">DropColumn</span></span>           | <span data-ttu-id="18481-148">✗</span><span class="sxs-lookup"><span data-stu-id="18481-148">✗</span></span>          |                  |
| <span data-ttu-id="18481-149">DropForeignKey</span><span class="sxs-lookup"><span data-stu-id="18481-149">DropForeignKey</span></span>       | <span data-ttu-id="18481-150">✗</span><span class="sxs-lookup"><span data-stu-id="18481-150">✗</span></span>          |                  |
| <span data-ttu-id="18481-151">DropIndex</span><span class="sxs-lookup"><span data-stu-id="18481-151">DropIndex</span></span>            | <span data-ttu-id="18481-152">✔</span><span class="sxs-lookup"><span data-stu-id="18481-152">✔</span></span>          | <span data-ttu-id="18481-153">1.0</span><span class="sxs-lookup"><span data-stu-id="18481-153">1.0</span></span>              |
| <span data-ttu-id="18481-154">DropPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="18481-154">DropPrimaryKey</span></span>       | <span data-ttu-id="18481-155">✗</span><span class="sxs-lookup"><span data-stu-id="18481-155">✗</span></span>          |                  |
| <span data-ttu-id="18481-156">DropTable</span><span class="sxs-lookup"><span data-stu-id="18481-156">DropTable</span></span>            | <span data-ttu-id="18481-157">✔</span><span class="sxs-lookup"><span data-stu-id="18481-157">✔</span></span>          | <span data-ttu-id="18481-158">1.0</span><span class="sxs-lookup"><span data-stu-id="18481-158">1.0</span></span>              |
| <span data-ttu-id="18481-159">DropUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="18481-159">DropUniqueConstraint</span></span> | <span data-ttu-id="18481-160">✗</span><span class="sxs-lookup"><span data-stu-id="18481-160">✗</span></span>          |                  |
| <span data-ttu-id="18481-161">RenameColumn</span><span class="sxs-lookup"><span data-stu-id="18481-161">RenameColumn</span></span>         | <span data-ttu-id="18481-162">✔</span><span class="sxs-lookup"><span data-stu-id="18481-162">✔</span></span>          | <span data-ttu-id="18481-163">2.2.2</span><span class="sxs-lookup"><span data-stu-id="18481-163">2.2.2</span></span>            |
| <span data-ttu-id="18481-164">RenameIndex</span><span class="sxs-lookup"><span data-stu-id="18481-164">RenameIndex</span></span>          | <span data-ttu-id="18481-165">✔</span><span class="sxs-lookup"><span data-stu-id="18481-165">✔</span></span>          | <span data-ttu-id="18481-166">2.1</span><span class="sxs-lookup"><span data-stu-id="18481-166">2.1</span></span>              |
| <span data-ttu-id="18481-167">RenameTable</span><span class="sxs-lookup"><span data-stu-id="18481-167">RenameTable</span></span>          | <span data-ttu-id="18481-168">✔</span><span class="sxs-lookup"><span data-stu-id="18481-168">✔</span></span>          | <span data-ttu-id="18481-169">1.0</span><span class="sxs-lookup"><span data-stu-id="18481-169">1.0</span></span>              |
| <span data-ttu-id="18481-170">EnsureSchema</span><span class="sxs-lookup"><span data-stu-id="18481-170">EnsureSchema</span></span>         | <span data-ttu-id="18481-171">✔ (no-op)</span><span class="sxs-lookup"><span data-stu-id="18481-171">✔ (no-op)</span></span>  | <span data-ttu-id="18481-172">2.0</span><span class="sxs-lookup"><span data-stu-id="18481-172">2.0</span></span>              |
| <span data-ttu-id="18481-173">DropSchema</span><span class="sxs-lookup"><span data-stu-id="18481-173">DropSchema</span></span>           | <span data-ttu-id="18481-174">✔ (no-op)</span><span class="sxs-lookup"><span data-stu-id="18481-174">✔ (no-op)</span></span>  | <span data-ttu-id="18481-175">2.0</span><span class="sxs-lookup"><span data-stu-id="18481-175">2.0</span></span>              |
| <span data-ttu-id="18481-176">Insert</span><span class="sxs-lookup"><span data-stu-id="18481-176">Insert</span></span>               | <span data-ttu-id="18481-177">✔</span><span class="sxs-lookup"><span data-stu-id="18481-177">✔</span></span>          | <span data-ttu-id="18481-178">2.0</span><span class="sxs-lookup"><span data-stu-id="18481-178">2.0</span></span>              |
| <span data-ttu-id="18481-179">Update</span><span class="sxs-lookup"><span data-stu-id="18481-179">Update</span></span>               | <span data-ttu-id="18481-180">✔</span><span class="sxs-lookup"><span data-stu-id="18481-180">✔</span></span>          | <span data-ttu-id="18481-181">2.0</span><span class="sxs-lookup"><span data-stu-id="18481-181">2.0</span></span>              |
| <span data-ttu-id="18481-182">DELETE</span><span class="sxs-lookup"><span data-stu-id="18481-182">Delete</span></span>               | <span data-ttu-id="18481-183">✔</span><span class="sxs-lookup"><span data-stu-id="18481-183">✔</span></span>          | <span data-ttu-id="18481-184">2.0</span><span class="sxs-lookup"><span data-stu-id="18481-184">2.0</span></span>              |

## <a name="migrations-limitations-workaround"></a><span data-ttu-id="18481-185">遷移限制解決方法</span><span class="sxs-lookup"><span data-stu-id="18481-185">Migrations limitations workaround</span></span>

<span data-ttu-id="18481-186">您可以藉由在您的遷移中手動撰寫程式碼來執行資料表重建，以解決其中一些限制。</span><span class="sxs-lookup"><span data-stu-id="18481-186">You can workaround some of these limitations by manually writing code in your migrations to perform a table rebuild.</span></span> <span data-ttu-id="18481-187">重建資料表需要重新命名現有的資料表、建立新的資料表、將資料複製到新的資料表，以及卸除舊的資料表。</span><span class="sxs-lookup"><span data-stu-id="18481-187">A table rebuild involves renaming the existing table, creating a new table, copying data to the new table, and dropping the old table.</span></span> <span data-ttu-id="18481-188">您將需要使用 `Sql(string)` 方法來執行其中一些步驟。</span><span class="sxs-lookup"><span data-stu-id="18481-188">You will need to use the `Sql(string)` method to perform some of these steps.</span></span>

<span data-ttu-id="18481-189">如需詳細資訊，請參閱 SQLite 檔中的[建立其他類型的資料表架構變更](https://sqlite.org/lang_altertable.html#otheralter)。</span><span class="sxs-lookup"><span data-stu-id="18481-189">See [Making Other Kinds Of Table Schema Changes](https://sqlite.org/lang_altertable.html#otheralter) in the SQLite documentation for more details.</span></span>

<span data-ttu-id="18481-190">在未來，EF 可能會使用部分的資料表重建方法來支援其中某些作業。</span><span class="sxs-lookup"><span data-stu-id="18481-190">In the future, EF may support some of these operations by using the table rebuild approach under the covers.</span></span> <span data-ttu-id="18481-191">您可以[在我們的 GitHub 專案上追蹤這項功能](https://github.com/aspnet/EntityFrameworkCore/issues/329)。</span><span class="sxs-lookup"><span data-stu-id="18481-191">You can [track this feature on our GitHub project](https://github.com/aspnet/EntityFrameworkCore/issues/329).</span></span>
