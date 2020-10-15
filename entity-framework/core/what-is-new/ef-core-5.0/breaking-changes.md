---
title: EF Core 5.0-EF Core 的重大變更
description: Entity Framework Core 5.0 中引進的重大變更完整清單
author: bricelam
ms.date: 09/24/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: e64f2b387d236e96d0451f3d55b3241daaba32d8
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065637"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="ba9d7-103">EF Core 5.0 中的重大變更</span><span class="sxs-lookup"><span data-stu-id="ba9d7-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="ba9d7-104">下列 API 和行為變更可能會中斷現有應用程式更新為 EF Core 5.0.0。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="ba9d7-105">摘要</span><span class="sxs-lookup"><span data-stu-id="ba9d7-105">Summary</span></span>

| <span data-ttu-id="ba9d7-106">**重大變更**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="ba9d7-107">**影響**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="ba9d7-108">從主體到相依的導覽上的必要項具有不同的語義</span><span class="sxs-lookup"><span data-stu-id="ba9d7-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="ba9d7-109">中</span><span class="sxs-lookup"><span data-stu-id="ba9d7-109">Medium</span></span>     |
| [<span data-ttu-id="ba9d7-110">定義查詢會取代為提供者特定的方法</span><span class="sxs-lookup"><span data-stu-id="ba9d7-110">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="ba9d7-111">中</span><span class="sxs-lookup"><span data-stu-id="ba9d7-111">Medium</span></span>     |
| [<span data-ttu-id="ba9d7-112">已從 SQLite NTS 擴充功能移除 HasGeometricDimension 方法</span><span class="sxs-lookup"><span data-stu-id="ba9d7-112">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="ba9d7-113">低</span><span class="sxs-lookup"><span data-stu-id="ba9d7-113">Low</span></span>        |
| [<span data-ttu-id="ba9d7-114">Cosmos：現在已將資料分割索引鍵新增至主要索引鍵</span><span class="sxs-lookup"><span data-stu-id="ba9d7-114">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="ba9d7-115">低</span><span class="sxs-lookup"><span data-stu-id="ba9d7-115">Low</span></span>        |
| [<span data-ttu-id="ba9d7-116">Cosmos： `id` 屬性已重新命名為 `__id`</span><span class="sxs-lookup"><span data-stu-id="ba9d7-116">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="ba9d7-117">低</span><span class="sxs-lookup"><span data-stu-id="ba9d7-117">Low</span></span>        |
| <span data-ttu-id="ba9d7-118">[Cosmos： byte [] 現在會儲存為 base64 字串，而不是數位陣列](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="ba9d7-118">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="ba9d7-119">低</span><span class="sxs-lookup"><span data-stu-id="ba9d7-119">Low</span></span>        |
| [<span data-ttu-id="ba9d7-120">Cosmos： GetPropertyName 和 SetPropertyName 已重新命名</span><span class="sxs-lookup"><span data-stu-id="ba9d7-120">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="ba9d7-121">低</span><span class="sxs-lookup"><span data-stu-id="ba9d7-121">Low</span></span>        |
| [<span data-ttu-id="ba9d7-122">當實體狀態從卸離變更為未變更、更新或刪除時，會呼叫值產生器</span><span class="sxs-lookup"><span data-stu-id="ba9d7-122">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="ba9d7-123">低</span><span class="sxs-lookup"><span data-stu-id="ba9d7-123">Low</span></span>        |
| [<span data-ttu-id="ba9d7-124">IMigrationsModelDiffer 現在使用 IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="ba9d7-124">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="ba9d7-125">低</span><span class="sxs-lookup"><span data-stu-id="ba9d7-125">Low</span></span>        |
| [<span data-ttu-id="ba9d7-126">鑒別子是唯讀的</span><span class="sxs-lookup"><span data-stu-id="ba9d7-126">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="ba9d7-127">低</span><span class="sxs-lookup"><span data-stu-id="ba9d7-127">Low</span></span>        |
| [<span data-ttu-id="ba9d7-128">提供者特定的 EF。InMemory 提供者的函數方法擲回</span><span class="sxs-lookup"><span data-stu-id="ba9d7-128">Provider-specific EF.Functions methods throw for InMemory provider</span></span>](#no-client-methods)                                              | <span data-ttu-id="ba9d7-129">低</span><span class="sxs-lookup"><span data-stu-id="ba9d7-129">Low</span></span>        |
| [<span data-ttu-id="ba9d7-130">IndexBuilder. HasName 現已淘汰</span><span class="sxs-lookup"><span data-stu-id="ba9d7-130">IndexBuilder.HasName is now obsolete</span></span>](#index-obsolete)                                                                               | <span data-ttu-id="ba9d7-131">低</span><span class="sxs-lookup"><span data-stu-id="ba9d7-131">Low</span></span>        |
| [<span data-ttu-id="ba9d7-132">Pluarlizer 現已包含給樣板反轉工程模型</span><span class="sxs-lookup"><span data-stu-id="ba9d7-132">A pluarlizer is now included for scaffolding reverse engineered models</span></span>](#pluralizer)                                                 | <span data-ttu-id="ba9d7-133">低</span><span class="sxs-lookup"><span data-stu-id="ba9d7-133">Low</span></span>        |

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="ba9d7-134">已從 SQLite NTS 擴充功能移除 HasGeometricDimension 方法</span><span class="sxs-lookup"><span data-stu-id="ba9d7-134">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="ba9d7-135">追蹤問題 #14257</span><span class="sxs-lookup"><span data-stu-id="ba9d7-135">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="ba9d7-136">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-136">**Old behavior**</span></span>

<span data-ttu-id="ba9d7-137">HasGeometricDimension 用來在幾何資料行上啟用 (Z 和 M) 的其他維度。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-137">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="ba9d7-138">但是，它只會影響資料庫的建立。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-138">However, it only ever affected database creation.</span></span> <span data-ttu-id="ba9d7-139">不需要將它指定為使用其他維度來查詢值。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-139">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="ba9d7-140">使用額外的維度插入或更新值時，也無法正確運作 ([請參閱 #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)) 。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-140">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="ba9d7-141">**新的行為**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-141">**New behavior**</span></span>

<span data-ttu-id="ba9d7-142">若要啟用以其他維度插入和更新幾何值 (Z 和 M) ，必須將維度指定為數據行類型名稱的一部分。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-142">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="ba9d7-143">此 API 更接近 SpatiaLite 的 AddGeometryColumn 函式的基礎行為。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-143">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="ba9d7-144">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-144">**Why**</span></span>

<span data-ttu-id="ba9d7-145">在資料行類型中指定維度之後使用 HasGeometricDimension 是不必要且多餘的，因此我們會完全移除 HasGeometricDimension。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-145">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="ba9d7-146">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-146">**Mitigations**</span></span>

<span data-ttu-id="ba9d7-147">使用 `HasColumnType` 指定維度：</span><span class="sxs-lookup"><span data-stu-id="ba9d7-147">Use `HasColumnType` to specify the dimension:</span></span>

```csharp
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

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="ba9d7-148">從主體到相依的導覽上的必要項具有不同的語義</span><span class="sxs-lookup"><span data-stu-id="ba9d7-148">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="ba9d7-149">追蹤問題 #17286</span><span class="sxs-lookup"><span data-stu-id="ba9d7-149">Tracking Issue #17286</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

<span data-ttu-id="ba9d7-150">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-150">**Old behavior**</span></span>

<span data-ttu-id="ba9d7-151">只有導覽至主體可設定為必要。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-151">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="ba9d7-152">因此， `RequiredAttribute` 在相依 (的實體上使用，包含外鍵的實體) 會改為在定義實體類型上建立外鍵。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-152">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

<span data-ttu-id="ba9d7-153">**新的行為**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-153">**New behavior**</span></span>

<span data-ttu-id="ba9d7-154">由於已新增對必要相依性的支援，現在可以視需要將任何參考導覽標記，這表示在上方顯示的情況下，外鍵將會定義于關聯性的另一端，而且屬性不會標示為必要項。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-154">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="ba9d7-155">`IsRequired`在指定相依端點之前呼叫，現在是不明確的：</span><span class="sxs-lookup"><span data-stu-id="ba9d7-155">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

<span data-ttu-id="ba9d7-156">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-156">**Why**</span></span>

<span data-ttu-id="ba9d7-157">若要啟用必要相依性的支援，必須要有新的行為 ([請參閱 #12100](https://github.com/dotnet/efcore/issues/12100)) 。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-157">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

<span data-ttu-id="ba9d7-158">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-158">**Mitigations**</span></span>

<span data-ttu-id="ba9d7-159">`RequiredAttribute`從導覽中移除相依性，並將其放在主體的導覽上，或在中設定關聯性 `OnModelCreating` ：</span><span class="sxs-lookup"><span data-stu-id="ba9d7-159">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="ba9d7-160">Cosmos：現在已將資料分割索引鍵新增至主要索引鍵</span><span class="sxs-lookup"><span data-stu-id="ba9d7-160">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="ba9d7-161">追蹤問題 #15289</span><span class="sxs-lookup"><span data-stu-id="ba9d7-161">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="ba9d7-162">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-162">**Old behavior**</span></span>

<span data-ttu-id="ba9d7-163">分割區索引鍵屬性只會新增至包含的替代索引鍵 `id` 。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-163">The partition key property was only added to the alternate key that includes `id`.</span></span>

<span data-ttu-id="ba9d7-164">**新的行為**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-164">**New behavior**</span></span>

<span data-ttu-id="ba9d7-165">依慣例，資料分割索引鍵屬性現在也會依慣例新增至主要索引鍵。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-165">The partition key property is now also added to the primary key by convention.</span></span>

<span data-ttu-id="ba9d7-166">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-166">**Why**</span></span>

<span data-ttu-id="ba9d7-167">這項變更可讓模型更符合 Azure Cosmos DB 的語義，並改善 `Find` 和某些查詢的效能。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-167">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

<span data-ttu-id="ba9d7-168">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-168">**Mitigations**</span></span>

<span data-ttu-id="ba9d7-169">若要避免將分割區索引鍵屬性加入至主鍵，請在中進行設定 `OnModelCreating` 。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-169">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="ba9d7-170">Cosmos： `id` 屬性已重新命名為 `__id`</span><span class="sxs-lookup"><span data-stu-id="ba9d7-170">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="ba9d7-171">追蹤問題 #17751</span><span class="sxs-lookup"><span data-stu-id="ba9d7-171">Tracking Issue #17751</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17751)

<span data-ttu-id="ba9d7-172">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-172">**Old behavior**</span></span>

<span data-ttu-id="ba9d7-173">對應至 JSON 屬性的陰影屬性 `id` 也稱為 `id` 。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-173">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

<span data-ttu-id="ba9d7-174">**新的行為**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-174">**New behavior**</span></span>

<span data-ttu-id="ba9d7-175">慣例所建立的陰影屬性現在已命名 `__id` 。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-175">The shadow property created by convention is now named `__id`.</span></span>

<span data-ttu-id="ba9d7-176">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-176">**Why**</span></span>

<span data-ttu-id="ba9d7-177">這種變更可讓 `id` 屬性與實體型別上的現有屬性不衝突。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-177">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

<span data-ttu-id="ba9d7-178">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-178">**Mitigations**</span></span>

<span data-ttu-id="ba9d7-179">若要返回3.x 行為，請 `id` 在中設定屬性 `OnModelCreating` 。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-179">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="ba9d7-180">Cosmos： byte [] 現在會儲存為 base64 字串，而不是數位陣列</span><span class="sxs-lookup"><span data-stu-id="ba9d7-180">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="ba9d7-181">追蹤問題 #17306</span><span class="sxs-lookup"><span data-stu-id="ba9d7-181">Tracking Issue #17306</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17306)

<span data-ttu-id="ba9d7-182">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-182">**Old behavior**</span></span>

<span data-ttu-id="ba9d7-183">Byte [] 類型的屬性已儲存為數字陣列。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-183">Properties of type byte[] were stored as a number array.</span></span>

<span data-ttu-id="ba9d7-184">**新的行為**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-184">**New behavior**</span></span>

<span data-ttu-id="ba9d7-185">Byte [] 類型的屬性現在會儲存為 base64 字串。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-185">Properties of type byte[] are now stored as a base64 string.</span></span>

<span data-ttu-id="ba9d7-186">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-186">**Why**</span></span>

<span data-ttu-id="ba9d7-187">Byte [] 的這個標記法符合預期，而且是主要 JSON 序列化程式庫的預設行為。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-187">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

<span data-ttu-id="ba9d7-188">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-188">**Mitigations**</span></span>

<span data-ttu-id="ba9d7-189">以數位陣列形式儲存的現有資料仍會正確地進行查詢，但目前不支援變更插入行為的方式。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-189">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="ba9d7-190">如果這項限制會封鎖您的案例，請針對[此問題](https://github.com/aspnet/EntityFrameworkCore/issues/17306)進行批註</span><span class="sxs-lookup"><span data-stu-id="ba9d7-190">If this limitation is blocking your scenario, comment on [this issue](https://github.com/aspnet/EntityFrameworkCore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="ba9d7-191">Cosmos： GetPropertyName 和 SetPropertyName 已重新命名</span><span class="sxs-lookup"><span data-stu-id="ba9d7-191">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="ba9d7-192">追蹤問題 #17874</span><span class="sxs-lookup"><span data-stu-id="ba9d7-192">Tracking Issue #17874</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17874)

<span data-ttu-id="ba9d7-193">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-193">**Old behavior**</span></span>

<span data-ttu-id="ba9d7-194">先前已呼叫擴充方法 `GetPropertyName` ，而且 `SetPropertyName`</span><span class="sxs-lookup"><span data-stu-id="ba9d7-194">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

<span data-ttu-id="ba9d7-195">**新的行為**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-195">**New behavior**</span></span>

<span data-ttu-id="ba9d7-196">移除舊的 API 並新增新 `GetJsonPropertyName` 方法： `SetJsonPropertyName`</span><span class="sxs-lookup"><span data-stu-id="ba9d7-196">The old API was removed and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

<span data-ttu-id="ba9d7-197">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-197">**Why**</span></span>

<span data-ttu-id="ba9d7-198">這項變更會移除這些方法所設定的混淆。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-198">This change removes the ambiguity around what these methods are configuring.</span></span>

<span data-ttu-id="ba9d7-199">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-199">**Mitigations**</span></span>

<span data-ttu-id="ba9d7-200">使用新的 API。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-200">Use the new API.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="ba9d7-201">當實體狀態從卸離變更為未變更、更新或刪除時，會呼叫值產生器</span><span class="sxs-lookup"><span data-stu-id="ba9d7-201">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="ba9d7-202">追蹤問題 #15289</span><span class="sxs-lookup"><span data-stu-id="ba9d7-202">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="ba9d7-203">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-203">**Old behavior**</span></span>

<span data-ttu-id="ba9d7-204">只有當實體狀態變更為 [已加入] 時，才會呼叫值產生器。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-204">Value generators were only called when the entity state changed to Added.</span></span>

<span data-ttu-id="ba9d7-205">**新的行為**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-205">**New behavior**</span></span>

<span data-ttu-id="ba9d7-206">現在當實體狀態從卸離變更為未變更、已更新或已刪除，且屬性包含預設值時，就會呼叫值產生器。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-206">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

<span data-ttu-id="ba9d7-207">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-207">**Why**</span></span>

<span data-ttu-id="ba9d7-208">需要進行這種變更，才能使用不會保存到資料存放區的屬性，以及在用戶端上永遠產生其值的方式，來改善體驗。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-208">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

<span data-ttu-id="ba9d7-209">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-209">**Mitigations**</span></span>

<span data-ttu-id="ba9d7-210">若要防止呼叫值產生器，請在狀態變更之前，將非預設值指派給屬性。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-210">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="ba9d7-211">IMigrationsModelDiffer 現在使用 IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="ba9d7-211">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="ba9d7-212">追蹤問題 #20305</span><span class="sxs-lookup"><span data-stu-id="ba9d7-212">Tracking Issue #20305</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

<span data-ttu-id="ba9d7-213">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-213">**Old behavior**</span></span>

<span data-ttu-id="ba9d7-214">`IMigrationsModelDiffer` API 的定義使用 `IModel` 。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-214">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

<span data-ttu-id="ba9d7-215">**新的行為**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-215">**New behavior**</span></span>

<span data-ttu-id="ba9d7-216">`IMigrationsModelDiffer` API 現在會使用 `IRelationalModel` 。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-216">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="ba9d7-217">不過，模型快照集仍會包含， `IModel` 因為此程式碼是應用程式的一部分，而且 Entity Framework 無法變更它，而不會進行較大的中斷變更。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-217">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

<span data-ttu-id="ba9d7-218">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-218">**Why**</span></span>

<span data-ttu-id="ba9d7-219">`IRelationalModel` 這是新加入的資料庫架構標記法。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-219">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="ba9d7-220">使用它來尋找差異會更快且更精確。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-220">Using it to find differences is faster and more accurate.</span></span>

<span data-ttu-id="ba9d7-221">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-221">**Mitigations**</span></span>

<span data-ttu-id="ba9d7-222">您可以使用下列程式碼，將模型 `snapshot` 與模型進行比較， `context` 如下所示：</span><span class="sxs-lookup"><span data-stu-id="ba9d7-222">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

```csharp
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

<span data-ttu-id="ba9d7-223">我們打算在 6.0 ([查看 #22031](https://github.com/dotnet/efcore/issues/22031) 的這項體驗) </span><span class="sxs-lookup"><span data-stu-id="ba9d7-223">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="ba9d7-224">鑒別子是唯讀的</span><span class="sxs-lookup"><span data-stu-id="ba9d7-224">Discriminators are read-only</span></span>

[<span data-ttu-id="ba9d7-225">追蹤問題 #21154</span><span class="sxs-lookup"><span data-stu-id="ba9d7-225">Tracking Issue #21154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

<span data-ttu-id="ba9d7-226">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-226">**Old behavior**</span></span>

<span data-ttu-id="ba9d7-227">您可以在呼叫之前變更鑒別子值 `SaveChanges`</span><span class="sxs-lookup"><span data-stu-id="ba9d7-227">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

<span data-ttu-id="ba9d7-228">**新的行為**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-228">**New behavior**</span></span>

<span data-ttu-id="ba9d7-229">在上述案例中，將會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-229">An exception will be throws in the above case.</span></span>

<span data-ttu-id="ba9d7-230">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-230">**Why**</span></span>

<span data-ttu-id="ba9d7-231">EF 不會預期實體類型在仍在追蹤時變更，因此變更鑒別子值會使內容處於不一致的狀態，而這可能會導致非預期的行為。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-231">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

<span data-ttu-id="ba9d7-232">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-232">**Mitigations**</span></span>

<span data-ttu-id="ba9d7-233">如果需要變更鑒別子值，而且會在呼叫後立即處置內容 `SaveChanges` ，則可以將鑒別子設為可變：</span><span class="sxs-lookup"><span data-stu-id="ba9d7-233">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```csharp
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="ba9d7-234">定義查詢會取代為提供者特定的方法</span><span class="sxs-lookup"><span data-stu-id="ba9d7-234">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="ba9d7-235">追蹤問題 #18903</span><span class="sxs-lookup"><span data-stu-id="ba9d7-235">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

<span data-ttu-id="ba9d7-236">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-236">**Old behavior**</span></span>

<span data-ttu-id="ba9d7-237">實體類型已對應至在核心層級定義查詢。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-237">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="ba9d7-238">任何時候，實體類型的查詢根目錄中使用的實體類型已由任何提供者的定義查詢所取代。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-238">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

<span data-ttu-id="ba9d7-239">**新的行為**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-239">**New behavior**</span></span>

<span data-ttu-id="ba9d7-240">用於定義查詢的 Api 已被取代。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-240">APIs for defining query are deprecated.</span></span> <span data-ttu-id="ba9d7-241">引進了新的提供者特定 Api。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-241">New provider-specific APIs were introduced.</span></span>

<span data-ttu-id="ba9d7-242">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-242">**Why**</span></span>

<span data-ttu-id="ba9d7-243">當查詢中使用查詢根時，定義查詢會實作為取代查詢，但有幾個問題：</span><span class="sxs-lookup"><span data-stu-id="ba9d7-243">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="ba9d7-244">如果定義查詢是使用 in 方法投射實體型別 `new { ... }` `Select` ，則將其識別為實體需要額外的工作，並使其與 EF Core 在查詢中處理名義類型的方式不一致。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-244">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="ba9d7-245">針對關聯式提供者， `FromSql` 仍需要以 LINQ 運算式形式傳遞 SQL 字串。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-245">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="ba9d7-246">最初定義查詢時，是以用戶端視圖的形式導入，以用於無索引鍵實體的 In-Memory 提供者 (類似于關係資料庫) 中的資料庫檢視。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-246">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="ba9d7-247">這類定義可讓您輕鬆地針對記憶體中資料庫測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-247">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="ba9d7-248">之後，這些應用程式很有用，但卻變得不一致，而且難以理解的行為。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-248">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="ba9d7-249">所以我們決定簡化這個概念。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-249">So we decided to simplify the concept.</span></span> <span data-ttu-id="ba9d7-250">我們對 In-Memory 提供者進行以 LINQ 為基礎的定義查詢，並以不同的方式加以處理。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-250">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="ba9d7-251">如需詳細資訊，請 [參閱此問題](https://github.com/dotnet/efcore/issues/20023)。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-251">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

<span data-ttu-id="ba9d7-252">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-252">**Mitigations**</span></span>

<span data-ttu-id="ba9d7-253">若為關聯式提供者，請使用 `ToSqlQuery` 中的方法 `OnModelCreating` 並傳入要用於實體類型的 SQL 字串。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-253">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="ba9d7-254">針對 In-Memory 提供者，請使用 `ToInMemoryQuery` 中的方法， `OnModelCreating` 並傳入 LINQ 查詢以用於實體型別。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-254">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a><span data-ttu-id="ba9d7-255">提供者特定的 EF。InMemory 提供者的函數方法擲回</span><span class="sxs-lookup"><span data-stu-id="ba9d7-255">Provider-specific EF.Functions methods throw for InMemory provider</span></span>

[<span data-ttu-id="ba9d7-256">追蹤問題 #20294</span><span class="sxs-lookup"><span data-stu-id="ba9d7-256">Tracking Issue #20294</span></span>](https://github.com/dotnet/efcore/issues/20294)

<span data-ttu-id="ba9d7-257">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-257">**Old behavior**</span></span>

<span data-ttu-id="ba9d7-258">提供者特定的 EF。函數方法包含用戶端執行的執行，可讓它們在 InMemory 提供者上執行。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-258">Provider-specific EF.Functions methods contained implementation for client execution, which allowed them to be executed on the InMemory provider.</span></span> <span data-ttu-id="ba9d7-259">例如， `EF.Functions.DateDiffDay` 是一種 Sql Server 特定的方法，它是在 InMemory 提供者上處理。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-259">For example, `EF.Functions.DateDiffDay` is a Sql Server specific method, which worked on InMemory provider.</span></span>

<span data-ttu-id="ba9d7-260">**新的行為**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-260">**New behavior**</span></span>

<span data-ttu-id="ba9d7-261">提供者特定方法已更新，在其方法主體中擲回例外狀況，以封鎖在用戶端上進行評估。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-261">Provider-specific methods have been updated to throw an exception in their method body to block evaluating them on client side.</span></span>

<span data-ttu-id="ba9d7-262">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-262">**Why**</span></span>

<span data-ttu-id="ba9d7-263">提供者特定的方法會對應至資料庫函式。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-263">Provider-specific methods map to a database function.</span></span> <span data-ttu-id="ba9d7-264">對應資料庫函式所完成的計算無法永遠在 LINQ 的用戶端上複寫。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-264">The computation done by the mapped database function can't always be replicated on the client side in LINQ.</span></span> <span data-ttu-id="ba9d7-265">在用戶端上執行相同的方法時，可能會導致伺服器的結果不同。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-265">It may cause the result from the server to differ when executing the same method on client.</span></span> <span data-ttu-id="ba9d7-266">因為這些方法是在 LINQ 中用來轉譯為特定的資料庫函式，所以不需要在用戶端上進行評估。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-266">Since these methods are used in LINQ to translate to specific database functions, they don't need to be evaluated on client side.</span></span> <span data-ttu-id="ba9d7-267">因為 InMemory 提供者是不同的 *資料庫*，所以這些方法不適用於此提供者。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-267">As InMemory provider is a different *database*, these methods aren't available for this provider.</span></span> <span data-ttu-id="ba9d7-268">嘗試針對 InMemory 提供者或任何其他未轉譯這些方法的提供者執行這些專案時，會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-268">Trying to execute them for InMemory provider, or any other provider that doesn't translate these methods, throws an exception.</span></span>

<span data-ttu-id="ba9d7-269">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-269">**Mitigations**</span></span>

<span data-ttu-id="ba9d7-270">因為無法準確模擬資料庫函式的行為，所以您應該針對與生產環境中相同類型的資料庫，測試包含它們的查詢。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-270">Since there's no way to mimic behavior of database functions accurately, you should test the queries containing them against same kind of database as in production.</span></span>

<a name="index-obsolete"></a>

### <a name="indexbuilderhasname-is-now-obsolete"></a><span data-ttu-id="ba9d7-271">IndexBuilder. HasName 現已淘汰</span><span class="sxs-lookup"><span data-stu-id="ba9d7-271">IndexBuilder.HasName is now obsolete</span></span>

[<span data-ttu-id="ba9d7-272">追蹤問題 #21089</span><span class="sxs-lookup"><span data-stu-id="ba9d7-272">Tracking Issue #21089</span></span>](https://github.com/dotnet/efcore/issues/21089)

<span data-ttu-id="ba9d7-273">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-273">**Old behavior**</span></span>

<span data-ttu-id="ba9d7-274">之前，只能在一組指定的屬性上定義一個索引。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-274">Previously, only one index could be defined over a given set of properties.</span></span> <span data-ttu-id="ba9d7-275">使用 IndexBuilder. HasName 設定索引的資料庫名稱。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-275">The database name of an index was configured using IndexBuilder.HasName.</span></span>

<span data-ttu-id="ba9d7-276">**新的行為**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-276">**New behavior**</span></span>

<span data-ttu-id="ba9d7-277">相同的集合或屬性現在允許多個索引。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-277">Multiple indexes are now allowed on the same set or properties.</span></span> <span data-ttu-id="ba9d7-278">這些索引現在是由模型中的名稱來區分。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-278">These indexes are now distinguished by a name in the model.</span></span> <span data-ttu-id="ba9d7-279">依照慣例，會使用模型名稱做為資料庫名稱;不過，您也可以使用 HasDatabaseName 獨立設定它。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-279">By convention, the model name is used as the database name; however it can also be configured independently using HasDatabaseName.</span></span>

<span data-ttu-id="ba9d7-280">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-280">**Why**</span></span>

<span data-ttu-id="ba9d7-281">未來，我們想要在相同的屬性集上啟用遞增和遞減索引，或具有不同定序的索引。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-281">In the future, we'd like to enable both ascending and descending indexes or indexes with different collations on the same set of properties.</span></span> <span data-ttu-id="ba9d7-282">這項變更會將另一個步驟移至該方向。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-282">This change moves us another step in that direction.</span></span>

<span data-ttu-id="ba9d7-283">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-283">**Mitigations**</span></span>

<span data-ttu-id="ba9d7-284">先前呼叫 IndexBuilder 的任何程式碼都應該更新為改為呼叫 HasDatabaseName。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-284">Any code that was previously calling IndexBuilder.HasName should be updated to call HasDatabaseName instead.</span></span>

<span data-ttu-id="ba9d7-285">如果您的專案包含 EF Core 版本2.0.0 之前產生的遷移，您可以放心地忽略這些檔案中的警告，並藉由新增來隱藏它 `#pragma warning disable 612, 618` 。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-285">If your project includes migrations generated prior to EF Core version 2.0.0, you can safely ignore the warning in those files and suppress it by adding `#pragma warning disable 612, 618`.</span></span>

<a name="pluralizer"></a>

### <a name="a-pluarlizer-is-now-included-for-scaffolding-reverse-engineered-models"></a><span data-ttu-id="ba9d7-286">Pluarlizer 現已包含給樣板反轉工程模型</span><span class="sxs-lookup"><span data-stu-id="ba9d7-286">A pluarlizer is now included for scaffolding reverse engineered models</span></span>

[<span data-ttu-id="ba9d7-287">追蹤問題 #11160</span><span class="sxs-lookup"><span data-stu-id="ba9d7-287">Tracking Issue #11160</span></span>](https://github.com/dotnet/efcore/issues/11160)

<span data-ttu-id="ba9d7-288">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-288">**Old behavior**</span></span>

<span data-ttu-id="ba9d7-289">先前，您必須安裝個別的 pluralizer 封裝，才能在 scaffoding DbCoNtext 和實體類型時，透過反向工程資料庫架構來複數化 DbSet 和集合導覽名稱和單數化資料表名稱。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-289">Previously, you had to install a separate pluralizer package in order to pluralize DbSet and collection navigation names and singularize table names when scaffoding a DbContext and entity types by reverse engineering a database schema.</span></span>

<span data-ttu-id="ba9d7-290">**新的行為**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-290">**New behavior**</span></span>

<span data-ttu-id="ba9d7-291">EF Core 現在包含使用 [Humanizer](https://github.com/Humanizr/Humanizer) 程式庫的 pluralizer。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-291">EF Core now includes a pluralizer that uses the [Humanizer](https://github.com/Humanizr/Humanizer) library.</span></span> <span data-ttu-id="ba9d7-292">這是 Visual Studio 用來建議變數名稱的相同程式庫。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-292">This is the same library Visual Studio uses to recommend variable names.</span></span>

<span data-ttu-id="ba9d7-293">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-293">**Why**</span></span>

<span data-ttu-id="ba9d7-294">針對集合屬性使用複數形式的單字以及類型和參考屬性的單數形式，在 .NET 中慣用。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-294">Using plural forms of words for collection properties and singular forms for types and reference properties is idiomatic in .NET.</span></span>

<span data-ttu-id="ba9d7-295">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="ba9d7-295">**Mitigations**</span></span>

<span data-ttu-id="ba9d7-296">若要停用 pluralizer，請使用 `--no-pluralize` 上的選項 `dotnet ef dbcontext scaffold` 或開啟上的 `-NoPluralize` 參數 `Scaffold-DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="ba9d7-296">To disable the pluralizer, use the `--no-pluralize` option on `dotnet ef dbcontext scaffold` or the `-NoPluralize` switch on `Scaffold-DbContext`.</span></span>
