---
title: EF Core 5.0-EF Core 的重大變更
description: Entity Framework Core 5.0 中引進的重大變更完整清單
author: bricelam
ms.date: 09/09/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 63fd1d1a01b7a72fd34bb9a0130191131306426c
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070792"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="2ae9e-103">EF Core 5.0 中的重大變更</span><span class="sxs-lookup"><span data-stu-id="2ae9e-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="2ae9e-104">下列 API 和行為變更可能會中斷現有應用程式更新為 EF Core 5.0.0。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="2ae9e-105">摘要</span><span class="sxs-lookup"><span data-stu-id="2ae9e-105">Summary</span></span>

| <span data-ttu-id="2ae9e-106">**重大變更**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="2ae9e-107">**影響**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="2ae9e-108">從主體到相依的導覽上的必要項具有不同的語義</span><span class="sxs-lookup"><span data-stu-id="2ae9e-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="2ae9e-109">中</span><span class="sxs-lookup"><span data-stu-id="2ae9e-109">Medium</span></span>     |
| [<span data-ttu-id="2ae9e-110">定義查詢會取代為提供者特定的方法</span><span class="sxs-lookup"><span data-stu-id="2ae9e-110">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="2ae9e-111">中</span><span class="sxs-lookup"><span data-stu-id="2ae9e-111">Medium</span></span>     |
| [<span data-ttu-id="2ae9e-112">已從 SQLite NTS 擴充功能移除 HasGeometricDimension 方法</span><span class="sxs-lookup"><span data-stu-id="2ae9e-112">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="2ae9e-113">低</span><span class="sxs-lookup"><span data-stu-id="2ae9e-113">Low</span></span>        |
| [<span data-ttu-id="2ae9e-114">Cosmos：現在已將資料分割索引鍵新增至主要索引鍵</span><span class="sxs-lookup"><span data-stu-id="2ae9e-114">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="2ae9e-115">低</span><span class="sxs-lookup"><span data-stu-id="2ae9e-115">Low</span></span>        |
| [<span data-ttu-id="2ae9e-116">Cosmos： `id` 屬性已重新命名為 `__id`</span><span class="sxs-lookup"><span data-stu-id="2ae9e-116">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="2ae9e-117">低</span><span class="sxs-lookup"><span data-stu-id="2ae9e-117">Low</span></span>        |
| <span data-ttu-id="2ae9e-118">[Cosmos： byte [] 現在會儲存為 base64 字串，而不是數位陣列](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="2ae9e-118">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="2ae9e-119">低</span><span class="sxs-lookup"><span data-stu-id="2ae9e-119">Low</span></span>        |
| [<span data-ttu-id="2ae9e-120">Cosmos： GetPropertyName 和 SetPropertyName 已重新命名</span><span class="sxs-lookup"><span data-stu-id="2ae9e-120">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="2ae9e-121">低</span><span class="sxs-lookup"><span data-stu-id="2ae9e-121">Low</span></span>        |
| [<span data-ttu-id="2ae9e-122">當實體狀態從卸離變更為未變更、更新或刪除時，會呼叫值產生器</span><span class="sxs-lookup"><span data-stu-id="2ae9e-122">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="2ae9e-123">低</span><span class="sxs-lookup"><span data-stu-id="2ae9e-123">Low</span></span>        |
| [<span data-ttu-id="2ae9e-124">IMigrationsModelDiffer 現在使用 IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="2ae9e-124">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="2ae9e-125">低</span><span class="sxs-lookup"><span data-stu-id="2ae9e-125">Low</span></span>        |
| [<span data-ttu-id="2ae9e-126">鑒別子是唯讀的</span><span class="sxs-lookup"><span data-stu-id="2ae9e-126">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="2ae9e-127">低</span><span class="sxs-lookup"><span data-stu-id="2ae9e-127">Low</span></span>        |

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="2ae9e-128">已從 SQLite NTS 擴充功能移除 HasGeometricDimension 方法</span><span class="sxs-lookup"><span data-stu-id="2ae9e-128">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="2ae9e-129">追蹤問題 #14257</span><span class="sxs-lookup"><span data-stu-id="2ae9e-129">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="2ae9e-130">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-130">**Old behavior**</span></span>

<span data-ttu-id="2ae9e-131">HasGeometricDimension 用來在幾何資料行上啟用 (Z 和 M) 的其他維度。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-131">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="2ae9e-132">但是，它只會影響資料庫的建立。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-132">However, it only ever affected database creation.</span></span> <span data-ttu-id="2ae9e-133">不需要將它指定為使用其他維度來查詢值。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-133">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="2ae9e-134">使用額外的維度插入或更新值時，也無法正確運作 ([請參閱 #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)) 。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-134">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="2ae9e-135">**新行為**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-135">**New behavior**</span></span>

<span data-ttu-id="2ae9e-136">若要啟用以其他維度插入和更新幾何值 (Z 和 M) ，必須將維度指定為數據行類型名稱的一部分。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-136">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="2ae9e-137">此 API 更接近 SpatiaLite 的 AddGeometryColumn 函式的基礎行為。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-137">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="2ae9e-138">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-138">**Why**</span></span>

<span data-ttu-id="2ae9e-139">在資料行類型中指定維度之後使用 HasGeometricDimension 是不必要且多餘的，因此我們會完全移除 HasGeometricDimension。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-139">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="2ae9e-140">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-140">**Mitigations**</span></span>

<span data-ttu-id="2ae9e-141">使用 `HasColumnType` 指定維度：</span><span class="sxs-lookup"><span data-stu-id="2ae9e-141">Use `HasColumnType` to specify the dimension:</span></span>

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

<a name="required-dependent"></a>

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="2ae9e-142">從主體到相依的導覽上的必要項具有不同的語義</span><span class="sxs-lookup"><span data-stu-id="2ae9e-142">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="2ae9e-143">追蹤問題 #17286</span><span class="sxs-lookup"><span data-stu-id="2ae9e-143">Tracking Issue #17286</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

<span data-ttu-id="2ae9e-144">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-144">**Old behavior**</span></span>

<span data-ttu-id="2ae9e-145">只有導覽至主體可設定為必要。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-145">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="2ae9e-146">因此， `RequiredAttribute` 在相依 (的實體上使用，包含外鍵的實體) 會改為在定義實體類型上建立外鍵。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-146">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

<span data-ttu-id="2ae9e-147">**新行為**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-147">**New behavior**</span></span>

<span data-ttu-id="2ae9e-148">由於已新增對必要相依性的支援，現在可以視需要將任何參考導覽標記，這表示在上方顯示的情況下，外鍵將會定義于關聯性的另一端，而且屬性不會標示為必要項。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-148">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="2ae9e-149">`IsRequired`在指定相依端點之前呼叫，現在是不明確的：</span><span class="sxs-lookup"><span data-stu-id="2ae9e-149">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

<span data-ttu-id="2ae9e-150">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-150">**Why**</span></span>

<span data-ttu-id="2ae9e-151">若要啟用必要相依性的支援，必須要有新的行為 ([請參閱 #12100](https://github.com/dotnet/efcore/issues/12100)) 。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-151">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

<span data-ttu-id="2ae9e-152">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-152">**Mitigations**</span></span>

<span data-ttu-id="2ae9e-153">`RequiredAttribute`從導覽中移除相依性，並將其放在主體的導覽上，或在中設定關聯性 `OnModelCreating` ：</span><span class="sxs-lookup"><span data-stu-id="2ae9e-153">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="2ae9e-154">Cosmos：現在已將資料分割索引鍵新增至主要索引鍵</span><span class="sxs-lookup"><span data-stu-id="2ae9e-154">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="2ae9e-155">追蹤問題 #15289</span><span class="sxs-lookup"><span data-stu-id="2ae9e-155">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="2ae9e-156">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-156">**Old behavior**</span></span>

<span data-ttu-id="2ae9e-157">分割區索引鍵屬性只會新增至包含的替代索引鍵 `id` 。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-157">The partition key property was only added to the alternate key that includes `id`.</span></span>

<span data-ttu-id="2ae9e-158">**新行為**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-158">**New behavior**</span></span>

<span data-ttu-id="2ae9e-159">依慣例，資料分割索引鍵屬性現在也會依慣例新增至主要索引鍵。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-159">The partition key property is now also added to the primary key by convention.</span></span>

<span data-ttu-id="2ae9e-160">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-160">**Why**</span></span>

<span data-ttu-id="2ae9e-161">這項變更可讓模型更符合 Azure Cosmos DB 的語義，並改善 `Find` 和某些查詢的效能。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-161">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

<span data-ttu-id="2ae9e-162">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-162">**Mitigations**</span></span>

<span data-ttu-id="2ae9e-163">若要避免將分割區索引鍵屬性加入至主鍵，請在中進行設定 `OnModelCreating` 。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-163">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="2ae9e-164">Cosmos： `id` 屬性已重新命名為 `__id`</span><span class="sxs-lookup"><span data-stu-id="2ae9e-164">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="2ae9e-165">追蹤問題 #17751</span><span class="sxs-lookup"><span data-stu-id="2ae9e-165">Tracking Issue #17751</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17751)

<span data-ttu-id="2ae9e-166">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-166">**Old behavior**</span></span>

<span data-ttu-id="2ae9e-167">對應至 JSON 屬性的陰影屬性 `id` 也稱為 `id` 。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-167">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

<span data-ttu-id="2ae9e-168">**新行為**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-168">**New behavior**</span></span>

<span data-ttu-id="2ae9e-169">慣例所建立的陰影屬性現在已命名 `__id` 。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-169">The shadow property created by convention is now named `__id`.</span></span>

<span data-ttu-id="2ae9e-170">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-170">**Why**</span></span>

<span data-ttu-id="2ae9e-171">這種變更可讓 `id` 屬性與實體型別上的現有屬性不衝突。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-171">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

<span data-ttu-id="2ae9e-172">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-172">**Mitigations**</span></span>

<span data-ttu-id="2ae9e-173">若要返回3.x 行為，請 `id` 在中設定屬性 `OnModelCreating` 。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-173">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```cs
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="2ae9e-174">Cosmos： byte [] 現在會儲存為 base64 字串，而不是數位陣列</span><span class="sxs-lookup"><span data-stu-id="2ae9e-174">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="2ae9e-175">追蹤問題 #17306</span><span class="sxs-lookup"><span data-stu-id="2ae9e-175">Tracking Issue #17306</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17306)

<span data-ttu-id="2ae9e-176">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-176">**Old behavior**</span></span>

<span data-ttu-id="2ae9e-177">Byte [] 類型的屬性已儲存為數字陣列。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-177">Properties of type byte[] were stored as a number array.</span></span>

<span data-ttu-id="2ae9e-178">**新行為**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-178">**New behavior**</span></span>

<span data-ttu-id="2ae9e-179">Byte [] 類型的屬性現在會儲存為 base64 字串。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-179">Properties of type byte[] are now stored as a base64 string.</span></span>

<span data-ttu-id="2ae9e-180">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-180">**Why**</span></span>

<span data-ttu-id="2ae9e-181">Byte [] 的這個標記法符合預期，而且是主要 JSON 序列化程式庫的預設行為。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-181">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

<span data-ttu-id="2ae9e-182">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-182">**Mitigations**</span></span>

<span data-ttu-id="2ae9e-183">以數位陣列形式儲存的現有資料仍會正確地進行查詢，但目前不支援變更插入行為的方式。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-183">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="2ae9e-184">如果這項限制會封鎖您的案例，請針對[此問題](https://github.com/aspnet/EntityFrameworkCore/issues/17306)進行批註</span><span class="sxs-lookup"><span data-stu-id="2ae9e-184">If this limitation is blocking your scenario, comment on [this issue](https://github.com/aspnet/EntityFrameworkCore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="2ae9e-185">Cosmos： GetPropertyName 和 SetPropertyName 已重新命名</span><span class="sxs-lookup"><span data-stu-id="2ae9e-185">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="2ae9e-186">追蹤問題 #17874</span><span class="sxs-lookup"><span data-stu-id="2ae9e-186">Tracking Issue #17874</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17874)

<span data-ttu-id="2ae9e-187">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-187">**Old behavior**</span></span>

<span data-ttu-id="2ae9e-188">先前已呼叫擴充方法 `GetPropertyName` ，而且 `SetPropertyName`</span><span class="sxs-lookup"><span data-stu-id="2ae9e-188">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

<span data-ttu-id="2ae9e-189">**新行為**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-189">**New behavior**</span></span>

<span data-ttu-id="2ae9e-190">舊的 API 已過時，並新增了新 `GetJsonPropertyName` 方法： `SetJsonPropertyName`</span><span class="sxs-lookup"><span data-stu-id="2ae9e-190">The old API was obsoleted and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

<span data-ttu-id="2ae9e-191">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-191">**Why**</span></span>

<span data-ttu-id="2ae9e-192">這項變更會移除這些方法所設定的混淆。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-192">This change removes the ambiguity around what these methods are configuring.</span></span>

<span data-ttu-id="2ae9e-193">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-193">**Mitigations**</span></span>

<span data-ttu-id="2ae9e-194">使用新的 API，或暫時暫停過時的警告。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-194">Use the new API or temporarily suspend the obsolete warnings.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="2ae9e-195">當實體狀態從卸離變更為未變更、更新或刪除時，會呼叫值產生器</span><span class="sxs-lookup"><span data-stu-id="2ae9e-195">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="2ae9e-196">追蹤問題 #15289</span><span class="sxs-lookup"><span data-stu-id="2ae9e-196">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="2ae9e-197">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-197">**Old behavior**</span></span>

<span data-ttu-id="2ae9e-198">只有當實體狀態變更為 [已加入] 時，才會呼叫值產生器。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-198">Value generators were only called when the entity state changed to Added.</span></span>

<span data-ttu-id="2ae9e-199">**新行為**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-199">**New behavior**</span></span>

<span data-ttu-id="2ae9e-200">現在當實體狀態從卸離變更為未變更、已更新或已刪除，且屬性包含預設值時，就會呼叫值產生器。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-200">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

<span data-ttu-id="2ae9e-201">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-201">**Why**</span></span>

<span data-ttu-id="2ae9e-202">需要進行這種變更，才能使用不會保存到資料存放區的屬性，以及在用戶端上永遠產生其值的方式，來改善體驗。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-202">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

<span data-ttu-id="2ae9e-203">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-203">**Mitigations**</span></span>

<span data-ttu-id="2ae9e-204">若要防止呼叫值產生器，請在狀態變更之前，將非預設值指派給屬性。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-204">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="2ae9e-205">IMigrationsModelDiffer 現在使用 IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="2ae9e-205">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="2ae9e-206">追蹤問題 #20305</span><span class="sxs-lookup"><span data-stu-id="2ae9e-206">Tracking Issue #20305</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

<span data-ttu-id="2ae9e-207">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-207">**Old behavior**</span></span>

<span data-ttu-id="2ae9e-208">`IMigrationsModelDiffer` API 的定義使用 `IModel` 。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-208">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

<span data-ttu-id="2ae9e-209">**新行為**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-209">**New behavior**</span></span>

<span data-ttu-id="2ae9e-210">`IMigrationsModelDiffer` API 現在會使用 `IRelationalModel` 。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-210">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="2ae9e-211">不過，模型快照集仍會包含， `IModel` 因為此程式碼是應用程式的一部分，而且 Entity Framework 無法變更它，而不會進行較大的中斷變更。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-211">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

<span data-ttu-id="2ae9e-212">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-212">**Why**</span></span>

<span data-ttu-id="2ae9e-213">`IRelationalModel` 這是新加入的資料庫架構標記法。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-213">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="2ae9e-214">使用它來尋找差異會更快且更精確。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-214">Using it to find differences is faster and more accurate.</span></span>

<span data-ttu-id="2ae9e-215">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-215">**Mitigations**</span></span>

<span data-ttu-id="2ae9e-216">您可以使用下列程式碼，將模型 `snapshot` 與模型進行比較， `context` 如下所示：</span><span class="sxs-lookup"><span data-stu-id="2ae9e-216">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

```cs
var dependencies = context.GetService<ProviderConventionSetBuilderDependencies>();
var relationalDependencies = context.GetService<RelationalConventionSetBuilderDependencies>();

var typeMappingConvention = new TypeMappingConvention(dependencies);
typeMappingConvention.ProcessModelFinalizing(((IConventionModel)modelSnapshot.Model).Builder, null);

var relationalModelConvention = new RelationalModelConvention(dependencies, relationalDependencies);
var sourceModel = relationalModelConvention.ProcessModelFinalized(snapshot.Model);

var modelDiffer = context.GetService<IMigrationsModelDiffer>();
var hasDifferences = modelDiffer.HasDifferences(
    ((IMutableModel)sourceModel).FinalizeModel().GetRelationalModel(),
    context.Model.GetRelationalModel());
```

<span data-ttu-id="2ae9e-217">我們打算在 6.0 ([查看 #22031](https://github.com/dotnet/efcore/issues/22031) 的這項體驗) </span><span class="sxs-lookup"><span data-stu-id="2ae9e-217">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="2ae9e-218">鑒別子是唯讀的</span><span class="sxs-lookup"><span data-stu-id="2ae9e-218">Discriminators are read-only</span></span>

[<span data-ttu-id="2ae9e-219">追蹤問題 #21154</span><span class="sxs-lookup"><span data-stu-id="2ae9e-219">Tracking Issue #21154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

<span data-ttu-id="2ae9e-220">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-220">**Old behavior**</span></span>

<span data-ttu-id="2ae9e-221">您可以在呼叫之前變更鑒別子值 `SaveChanges`</span><span class="sxs-lookup"><span data-stu-id="2ae9e-221">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

<span data-ttu-id="2ae9e-222">**新行為**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-222">**New behavior**</span></span>

<span data-ttu-id="2ae9e-223">在上述案例中，將會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-223">An exception will be throws in the above case.</span></span>

<span data-ttu-id="2ae9e-224">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-224">**Why**</span></span>

<span data-ttu-id="2ae9e-225">EF 不會預期實體類型在仍在追蹤時變更，因此變更鑒別子值會使內容處於不一致的狀態，而這可能會導致非預期的行為。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-225">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

<span data-ttu-id="2ae9e-226">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-226">**Mitigations**</span></span>

<span data-ttu-id="2ae9e-227">如果需要變更鑒別子值，而且會在呼叫後立即處置內容 `SaveChanges` ，則可以將鑒別子設為可變：</span><span class="sxs-lookup"><span data-stu-id="2ae9e-227">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```cs
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="2ae9e-228">定義查詢會取代為提供者特定的方法</span><span class="sxs-lookup"><span data-stu-id="2ae9e-228">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="2ae9e-229">追蹤問題 #18903</span><span class="sxs-lookup"><span data-stu-id="2ae9e-229">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

<span data-ttu-id="2ae9e-230">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-230">**Old behavior**</span></span>

<span data-ttu-id="2ae9e-231">實體類型已對應至在核心層級定義查詢。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-231">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="2ae9e-232">任何時候，實體類型的查詢根目錄中使用的實體類型已由任何提供者的定義查詢所取代。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-232">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

<span data-ttu-id="2ae9e-233">**新行為**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-233">**New behavior**</span></span>

<span data-ttu-id="2ae9e-234">用於定義查詢的 Api 已被取代。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-234">APIs for defining query are deprecated.</span></span> <span data-ttu-id="2ae9e-235">引進了新的提供者特定 Api。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-235">New provider-specific APIs were introduced.</span></span>

<span data-ttu-id="2ae9e-236">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-236">**Why**</span></span>

<span data-ttu-id="2ae9e-237">當查詢中使用查詢根時，定義查詢會實作為取代查詢，但有幾個問題：</span><span class="sxs-lookup"><span data-stu-id="2ae9e-237">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="2ae9e-238">如果定義查詢是使用 in 方法投射實體型別 `new { ... }` `Select` ，則將其識別為實體需要額外的工作，並使其與 EF Core 在查詢中處理名義類型的方式不一致。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-238">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="2ae9e-239">針對關聯式提供者， `FromSql` 仍需要以 LINQ 運算式形式傳遞 SQL 字串。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-239">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="2ae9e-240">最初定義查詢是以用戶端視圖的形式導入，以用於無索引鍵實體的記憶體內部提供者 (類似于關係資料庫) 中的資料庫檢視。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-240">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="2ae9e-241">這類定義可讓您輕鬆地針對記憶體中資料庫測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-241">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="2ae9e-242">之後，這些應用程式很有用，但卻變得不一致，而且難以理解的行為。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-242">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="2ae9e-243">所以我們決定簡化這個概念。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-243">So we decided to simplify the concept.</span></span> <span data-ttu-id="2ae9e-244">我們對記憶體內部提供者進行以 LINQ 為基礎的定義查詢，並以不同的方式加以處理。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-244">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="2ae9e-245">如需詳細資訊，請 [參閱此問題](https://github.com/dotnet/efcore/issues/20023)。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-245">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

<span data-ttu-id="2ae9e-246">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="2ae9e-246">**Mitigations**</span></span>

<span data-ttu-id="2ae9e-247">若為關聯式提供者，請使用 `ToSqlQuery` 中的方法 `OnModelCreating` 並傳入要用於實體類型的 SQL 字串。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-247">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="2ae9e-248">對於記憶體中的提供者，請使用 `ToInMemoryQuery` 中的方法， `OnModelCreating` 並傳入 LINQ 查詢以用於實體型別。</span><span class="sxs-lookup"><span data-stu-id="2ae9e-248">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>
