---
title: SQLite 資料庫提供者-限制-EF Core
description: 與其他提供者相較之下，Entity Framework Core SQLite 資料庫提供者的限制
author: bricelam
ms.date: 09/24/2020
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 3d696474d401e8fd6c26a78067d292f0bb97a457
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062733"
---
# <a name="sqlite-ef-core-database-provider-limitations"></a><span data-ttu-id="ae787-103">SQLite EF Core 資料庫提供者限制</span><span class="sxs-lookup"><span data-stu-id="ae787-103">SQLite EF Core Database Provider Limitations</span></span>

<span data-ttu-id="ae787-104">SQLite 提供者有許多的遷移限制。</span><span class="sxs-lookup"><span data-stu-id="ae787-104">The SQLite provider has a number of migrations limitations.</span></span> <span data-ttu-id="ae787-105">這些限制大多是基礎 SQLite 資料庫引擎中的限制所造成的結果，而且不是 EF 特有的。</span><span class="sxs-lookup"><span data-stu-id="ae787-105">Most of these limitations are a result of limitations in the underlying SQLite database engine and are not specific to EF.</span></span>

## <a name="modeling-limitations"></a><span data-ttu-id="ae787-106">模型限制</span><span class="sxs-lookup"><span data-stu-id="ae787-106">Modeling limitations</span></span>

<span data-ttu-id="ae787-107">Entity Framework 關係資料庫提供者所共用的通用關聯式程式庫 () 會定義 Api 來模型化大部分關係資料庫引擎的常見概念。</span><span class="sxs-lookup"><span data-stu-id="ae787-107">The common relational library (shared by Entity Framework relational database providers) defines APIs for modelling concepts that are common to most relational database engines.</span></span> <span data-ttu-id="ae787-108">SQLite 提供者不支援其中的一些概念。</span><span class="sxs-lookup"><span data-stu-id="ae787-108">A couple of these concepts are not supported by the SQLite provider.</span></span>

* <span data-ttu-id="ae787-109">結構描述</span><span class="sxs-lookup"><span data-stu-id="ae787-109">Schemas</span></span>
* <span data-ttu-id="ae787-110">序列</span><span class="sxs-lookup"><span data-stu-id="ae787-110">Sequences</span></span>

## <a name="query-limitations"></a><span data-ttu-id="ae787-111">查詢限制</span><span class="sxs-lookup"><span data-stu-id="ae787-111">Query limitations</span></span>

<span data-ttu-id="ae787-112">SQLite 本身並不支援下列資料類型。</span><span class="sxs-lookup"><span data-stu-id="ae787-112">SQLite doesn't natively support the following data types.</span></span> <span data-ttu-id="ae787-113">EF Core 可以讀取和寫入這些類型的值，也支援查詢是否相等 (`where e.Property == value`) 。</span><span class="sxs-lookup"><span data-stu-id="ae787-113">EF Core can read and write values of these types, and querying for equality (`where e.Property == value`) is also supported.</span></span> <span data-ttu-id="ae787-114">不過，其他作業（例如比較和排序）將需要在用戶端上進行評估。</span><span class="sxs-lookup"><span data-stu-id="ae787-114">Other operations, however, like comparison and ordering will require evaluation on the client.</span></span>

* <span data-ttu-id="ae787-115">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="ae787-115">DateTimeOffset</span></span>
* <span data-ttu-id="ae787-116">Decimal</span><span class="sxs-lookup"><span data-stu-id="ae787-116">Decimal</span></span>
* <span data-ttu-id="ae787-117">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="ae787-117">TimeSpan</span></span>
* <span data-ttu-id="ae787-118">UInt64</span><span class="sxs-lookup"><span data-stu-id="ae787-118">UInt64</span></span>

<span data-ttu-id="ae787-119">`DateTimeOffset`我們建議您不要使用日期時間值。</span><span class="sxs-lookup"><span data-stu-id="ae787-119">Instead of `DateTimeOffset`, we recommend using DateTime values.</span></span> <span data-ttu-id="ae787-120">處理多個時區時，建議您先將值轉換成 UTC，再儲存，然後再轉換回適當的時區。</span><span class="sxs-lookup"><span data-stu-id="ae787-120">When handling multiple time zones, we recommend converting the values to UTC before saving and then converting back to the appropriate time zone.</span></span>

<span data-ttu-id="ae787-121">此 `Decimal` 類型提供高等級的精確度。</span><span class="sxs-lookup"><span data-stu-id="ae787-121">The `Decimal` type provides a high level of precision.</span></span> <span data-ttu-id="ae787-122">但是，如果您不需要該層級的精確度，我們建議您改為使用 double。</span><span class="sxs-lookup"><span data-stu-id="ae787-122">If you don't need that level of precision, however, we recommend using double instead.</span></span> <span data-ttu-id="ae787-123">您可以使用 [值轉換器](xref:core/modeling/value-conversions) 來繼續在您的類別中使用 decimal。</span><span class="sxs-lookup"><span data-stu-id="ae787-123">You can use a [value converter](xref:core/modeling/value-conversions) to continue using decimal in your classes.</span></span>

```csharp
modelBuilder.Entity<MyEntity>()
    .Property(e => e.DecimalProperty)
    .HasConversion<double>();
```

## <a name="migrations-limitations"></a><span data-ttu-id="ae787-124">遷移限制</span><span class="sxs-lookup"><span data-stu-id="ae787-124">Migrations limitations</span></span>

<span data-ttu-id="ae787-125">SQLite 資料庫引擎不支援大部分其他關係資料庫都支援的一些架構作業。</span><span class="sxs-lookup"><span data-stu-id="ae787-125">The SQLite database engine does not support a number of schema operations that are supported by the majority of other relational databases.</span></span> <span data-ttu-id="ae787-126">如果您嘗試將其中一項不支援的作業套用至 SQLite 資料庫， `NotSupportedException` 將會擲回。</span><span class="sxs-lookup"><span data-stu-id="ae787-126">If you attempt to apply one of the unsupported operations to a SQLite database then a `NotSupportedException` will be thrown.</span></span>

<span data-ttu-id="ae787-127">將會嘗試重建，以便執行某些作業。</span><span class="sxs-lookup"><span data-stu-id="ae787-127">A rebuild will be attempted in order to perform certain operations.</span></span> <span data-ttu-id="ae787-128">只有屬於 EF Core 模型一部分的資料庫成品才能重建。</span><span class="sxs-lookup"><span data-stu-id="ae787-128">Rebuilds are only possible for database artifacts that are part of your EF Core model.</span></span> <span data-ttu-id="ae787-129">如果資料庫成品不是模型的一部分，例如，如果是在遷移內部手動建立的，則 `NotSupportedException` 仍會擲回。</span><span class="sxs-lookup"><span data-stu-id="ae787-129">If a database artifact isn't part of the model--for example, if it was created manually inside a migration--then a `NotSupportedException` is still thrown.</span></span>

| <span data-ttu-id="ae787-130">作業</span><span class="sxs-lookup"><span data-stu-id="ae787-130">Operation</span></span>            | <span data-ttu-id="ae787-131">是否支援？</span><span class="sxs-lookup"><span data-stu-id="ae787-131">Supported?</span></span>  | <span data-ttu-id="ae787-132">需要版本</span><span class="sxs-lookup"><span data-stu-id="ae787-132">Requires version</span></span> |
|:---------------------|:------------|:-----------------|
| <span data-ttu-id="ae787-133">AddCheckConstraint</span><span class="sxs-lookup"><span data-stu-id="ae787-133">AddCheckConstraint</span></span>   | <span data-ttu-id="ae787-134">✔ (重建) </span><span class="sxs-lookup"><span data-stu-id="ae787-134">✔ (rebuild)</span></span> | <span data-ttu-id="ae787-135">5.0</span><span class="sxs-lookup"><span data-stu-id="ae787-135">5.0</span></span>              |
| <span data-ttu-id="ae787-136">AddColumn</span><span class="sxs-lookup"><span data-stu-id="ae787-136">AddColumn</span></span>            | <span data-ttu-id="ae787-137">✔</span><span class="sxs-lookup"><span data-stu-id="ae787-137">✔</span></span>           |                  |
| <span data-ttu-id="ae787-138">AddForeignKey</span><span class="sxs-lookup"><span data-stu-id="ae787-138">AddForeignKey</span></span>        | <span data-ttu-id="ae787-139">✔ (重建) </span><span class="sxs-lookup"><span data-stu-id="ae787-139">✔ (rebuild)</span></span> | <span data-ttu-id="ae787-140">5.0</span><span class="sxs-lookup"><span data-stu-id="ae787-140">5.0</span></span>              |
| <span data-ttu-id="ae787-141">AddPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="ae787-141">AddPrimaryKey</span></span>        | <span data-ttu-id="ae787-142">✔ (重建) </span><span class="sxs-lookup"><span data-stu-id="ae787-142">✔ (rebuild)</span></span> | <span data-ttu-id="ae787-143">5.0</span><span class="sxs-lookup"><span data-stu-id="ae787-143">5.0</span></span>              |
| <span data-ttu-id="ae787-144">AddUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="ae787-144">AddUniqueConstraint</span></span>  | <span data-ttu-id="ae787-145">✔ (重建) </span><span class="sxs-lookup"><span data-stu-id="ae787-145">✔ (rebuild)</span></span> | <span data-ttu-id="ae787-146">5.0</span><span class="sxs-lookup"><span data-stu-id="ae787-146">5.0</span></span>              |
| <span data-ttu-id="ae787-147">AlterColumn</span><span class="sxs-lookup"><span data-stu-id="ae787-147">AlterColumn</span></span>          | <span data-ttu-id="ae787-148">✔ (重建) </span><span class="sxs-lookup"><span data-stu-id="ae787-148">✔ (rebuild)</span></span> | <span data-ttu-id="ae787-149">5.0</span><span class="sxs-lookup"><span data-stu-id="ae787-149">5.0</span></span>              |
| <span data-ttu-id="ae787-150">CreateIndex</span><span class="sxs-lookup"><span data-stu-id="ae787-150">CreateIndex</span></span>          | <span data-ttu-id="ae787-151">✔</span><span class="sxs-lookup"><span data-stu-id="ae787-151">✔</span></span>           |                  |
| <span data-ttu-id="ae787-152">CreateTable</span><span class="sxs-lookup"><span data-stu-id="ae787-152">CreateTable</span></span>          | <span data-ttu-id="ae787-153">✔</span><span class="sxs-lookup"><span data-stu-id="ae787-153">✔</span></span>           |                  |
| <span data-ttu-id="ae787-154">DropCheckConstraint</span><span class="sxs-lookup"><span data-stu-id="ae787-154">DropCheckConstraint</span></span>  | <span data-ttu-id="ae787-155">✔ (重建) </span><span class="sxs-lookup"><span data-stu-id="ae787-155">✔ (rebuild)</span></span> | <span data-ttu-id="ae787-156">5.0</span><span class="sxs-lookup"><span data-stu-id="ae787-156">5.0</span></span>              |
| <span data-ttu-id="ae787-157">DropColumn</span><span class="sxs-lookup"><span data-stu-id="ae787-157">DropColumn</span></span>           | <span data-ttu-id="ae787-158">✔ (重建) </span><span class="sxs-lookup"><span data-stu-id="ae787-158">✔ (rebuild)</span></span> | <span data-ttu-id="ae787-159">5.0</span><span class="sxs-lookup"><span data-stu-id="ae787-159">5.0</span></span>              |
| <span data-ttu-id="ae787-160">DropForeignKey</span><span class="sxs-lookup"><span data-stu-id="ae787-160">DropForeignKey</span></span>       | <span data-ttu-id="ae787-161">✔ (重建) </span><span class="sxs-lookup"><span data-stu-id="ae787-161">✔ (rebuild)</span></span> | <span data-ttu-id="ae787-162">5.0</span><span class="sxs-lookup"><span data-stu-id="ae787-162">5.0</span></span>              |
| <span data-ttu-id="ae787-163">DropIndex</span><span class="sxs-lookup"><span data-stu-id="ae787-163">DropIndex</span></span>            | <span data-ttu-id="ae787-164">✔</span><span class="sxs-lookup"><span data-stu-id="ae787-164">✔</span></span>           |                  |
| <span data-ttu-id="ae787-165">DropPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="ae787-165">DropPrimaryKey</span></span>       | <span data-ttu-id="ae787-166">✔ (重建) </span><span class="sxs-lookup"><span data-stu-id="ae787-166">✔ (rebuild)</span></span> | <span data-ttu-id="ae787-167">5.0</span><span class="sxs-lookup"><span data-stu-id="ae787-167">5.0</span></span>              |
| <span data-ttu-id="ae787-168">DropTable</span><span class="sxs-lookup"><span data-stu-id="ae787-168">DropTable</span></span>            | <span data-ttu-id="ae787-169">✔</span><span class="sxs-lookup"><span data-stu-id="ae787-169">✔</span></span>           |                  |
| <span data-ttu-id="ae787-170">DropUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="ae787-170">DropUniqueConstraint</span></span> | <span data-ttu-id="ae787-171">✔ (重建) </span><span class="sxs-lookup"><span data-stu-id="ae787-171">✔ (rebuild)</span></span> | <span data-ttu-id="ae787-172">5.0</span><span class="sxs-lookup"><span data-stu-id="ae787-172">5.0</span></span>              |
| <span data-ttu-id="ae787-173">RenameColumn</span><span class="sxs-lookup"><span data-stu-id="ae787-173">RenameColumn</span></span>         | <span data-ttu-id="ae787-174">✔</span><span class="sxs-lookup"><span data-stu-id="ae787-174">✔</span></span>           | <span data-ttu-id="ae787-175">2.2</span><span class="sxs-lookup"><span data-stu-id="ae787-175">2.2</span></span>              |
| <span data-ttu-id="ae787-176">RenameIndex</span><span class="sxs-lookup"><span data-stu-id="ae787-176">RenameIndex</span></span>          | <span data-ttu-id="ae787-177">✔ (重建) </span><span class="sxs-lookup"><span data-stu-id="ae787-177">✔ (rebuild)</span></span> |                  |
| <span data-ttu-id="ae787-178">RenameTable</span><span class="sxs-lookup"><span data-stu-id="ae787-178">RenameTable</span></span>          | <span data-ttu-id="ae787-179">✔</span><span class="sxs-lookup"><span data-stu-id="ae787-179">✔</span></span>           |                  |
| <span data-ttu-id="ae787-180">EnsureSchema</span><span class="sxs-lookup"><span data-stu-id="ae787-180">EnsureSchema</span></span>         | <span data-ttu-id="ae787-181">✔ (無 op) </span><span class="sxs-lookup"><span data-stu-id="ae787-181">✔ (no-op)</span></span>   |                  |
| <span data-ttu-id="ae787-182">DropSchema</span><span class="sxs-lookup"><span data-stu-id="ae787-182">DropSchema</span></span>           | <span data-ttu-id="ae787-183">✔ (無 op) </span><span class="sxs-lookup"><span data-stu-id="ae787-183">✔ (no-op)</span></span>   |                  |
| <span data-ttu-id="ae787-184">插入</span><span class="sxs-lookup"><span data-stu-id="ae787-184">Insert</span></span>               | <span data-ttu-id="ae787-185">✔</span><span class="sxs-lookup"><span data-stu-id="ae787-185">✔</span></span>           |                  |
| <span data-ttu-id="ae787-186">更新</span><span class="sxs-lookup"><span data-stu-id="ae787-186">Update</span></span>               | <span data-ttu-id="ae787-187">✔</span><span class="sxs-lookup"><span data-stu-id="ae787-187">✔</span></span>           |                  |
| <span data-ttu-id="ae787-188">刪除</span><span class="sxs-lookup"><span data-stu-id="ae787-188">Delete</span></span>               | <span data-ttu-id="ae787-189">✔</span><span class="sxs-lookup"><span data-stu-id="ae787-189">✔</span></span>           |                  |

### <a name="migrations-limitations-workaround"></a><span data-ttu-id="ae787-190">遷移限制解決方法</span><span class="sxs-lookup"><span data-stu-id="ae787-190">Migrations limitations workaround</span></span>

<span data-ttu-id="ae787-191">您可以藉由在您的遷移中手動撰寫程式碼來執行重建，以解決其中一些限制。</span><span class="sxs-lookup"><span data-stu-id="ae787-191">You can workaround some of these limitations by manually writing code in your migrations to perform a rebuild.</span></span> <span data-ttu-id="ae787-192">資料表重建牽涉到建立新的資料表、將資料複製到新的資料表、卸載舊的資料表、重新命名新的資料表。</span><span class="sxs-lookup"><span data-stu-id="ae787-192">Table rebuilds involve creating a new table, copying data to the new table, dropping the old table, renaming the new table.</span></span> <span data-ttu-id="ae787-193">您將需要使用 `Sql(string)` 方法來執行這些步驟的其中一部分。</span><span class="sxs-lookup"><span data-stu-id="ae787-193">You will need to use the `Sql(string)` method to perform some of these steps.</span></span>

<span data-ttu-id="ae787-194">如需詳細資訊，請參閱在 SQLite 檔中 [進行其他類型的資料表架構變更](https://sqlite.org/lang_altertable.html#otheralter) 。</span><span class="sxs-lookup"><span data-stu-id="ae787-194">See [Making Other Kinds Of Table Schema Changes](https://sqlite.org/lang_altertable.html#otheralter) in the SQLite documentation for more details.</span></span>

### <a name="idempotent-script-limitations"></a><span data-ttu-id="ae787-195">等冪腳本限制</span><span class="sxs-lookup"><span data-stu-id="ae787-195">Idempotent script limitations</span></span>

<span data-ttu-id="ae787-196">與其他資料庫不同的是，SQLite 不包含程式語言。</span><span class="sxs-lookup"><span data-stu-id="ae787-196">Unlike other databases, SQLite doesn't include a procedural language.</span></span> <span data-ttu-id="ae787-197">因此，沒有任何方法可以產生等冪性遷移腳本所需的 then 邏輯。</span><span class="sxs-lookup"><span data-stu-id="ae787-197">Because of this, there is no way to generate the if-then logic required by the idempotent migration scripts.</span></span>

<span data-ttu-id="ae787-198">如果您知道最後一個要套用到資料庫的遷移，您可以從該遷移到最新的遷移產生腳本。</span><span class="sxs-lookup"><span data-stu-id="ae787-198">If you know the last migration applied to a database, you can generate a script from that migration to the latest migration.</span></span>

```dotnetcli
dotnet ef migrations script CurrentMigration
```

<span data-ttu-id="ae787-199">否則，建議使用 `dotnet ef database update` 來套用遷移。</span><span class="sxs-lookup"><span data-stu-id="ae787-199">Otherwise, we recommend using `dotnet ef database update` to apply migrations.</span></span> <span data-ttu-id="ae787-200">從 EF Core 5.0 開始，您可以在執行命令時指定資料庫檔案。</span><span class="sxs-lookup"><span data-stu-id="ae787-200">Starting in EF Core 5.0, you can specify the database file when running the command.</span></span>

```dotnetcli
dotnet ef database update --connection "Data Source=My.db"
```

## <a name="see-also"></a><span data-ttu-id="ae787-201">另請參閱</span><span class="sxs-lookup"><span data-stu-id="ae787-201">See also</span></span>

* [<span data-ttu-id="ae787-202">Microsoft. Sqlite 非同步限制</span><span class="sxs-lookup"><span data-stu-id="ae787-202">Microsoft.Data.Sqlite Async Limitations</span></span>](/dotnet/standard/data/sqlite/async)
* [<span data-ttu-id="ae787-203">ADO.NET 限制</span><span class="sxs-lookup"><span data-stu-id="ae787-203">Microsoft.Data.Sqlite ADO.NET Limitations</span></span>](/dotnet/standard/data/sqlite/adonet-limitations)
