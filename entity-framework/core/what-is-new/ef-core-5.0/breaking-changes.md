---
title: EF Core 5.0-EF Core 的重大變更
description: Entity Framework Core 5.0 中引進的重大變更完整清單
author: bricelam
ms.date: 09/09/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 8e9df4e2ff81e20cf5a36855247c5aff89ea2394
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2020
ms.locfileid: "91210363"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="e6931-103">EF Core 5.0 中的重大變更</span><span class="sxs-lookup"><span data-stu-id="e6931-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="e6931-104">下列 API 和行為變更可能會中斷現有應用程式更新為 EF Core 5.0.0。</span><span class="sxs-lookup"><span data-stu-id="e6931-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="e6931-105">摘要</span><span class="sxs-lookup"><span data-stu-id="e6931-105">Summary</span></span>

| <span data-ttu-id="e6931-106">**重大變更**</span><span class="sxs-lookup"><span data-stu-id="e6931-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="e6931-107">**影響**</span><span class="sxs-lookup"><span data-stu-id="e6931-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="e6931-108">從主體到相依的導覽上的必要項具有不同的語義</span><span class="sxs-lookup"><span data-stu-id="e6931-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="e6931-109">中</span><span class="sxs-lookup"><span data-stu-id="e6931-109">Medium</span></span>     |
| [<span data-ttu-id="e6931-110">定義查詢會取代為提供者特定的方法</span><span class="sxs-lookup"><span data-stu-id="e6931-110">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="e6931-111">中</span><span class="sxs-lookup"><span data-stu-id="e6931-111">Medium</span></span>     |
| [<span data-ttu-id="e6931-112">已從 SQLite NTS 擴充功能移除 HasGeometricDimension 方法</span><span class="sxs-lookup"><span data-stu-id="e6931-112">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="e6931-113">低度</span><span class="sxs-lookup"><span data-stu-id="e6931-113">Low</span></span>        |
| [<span data-ttu-id="e6931-114">Cosmos：現在已將資料分割索引鍵新增至主要索引鍵</span><span class="sxs-lookup"><span data-stu-id="e6931-114">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="e6931-115">低度</span><span class="sxs-lookup"><span data-stu-id="e6931-115">Low</span></span>        |
| [<span data-ttu-id="e6931-116">Cosmos： `id` 屬性已重新命名為 `__id`</span><span class="sxs-lookup"><span data-stu-id="e6931-116">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="e6931-117">低度</span><span class="sxs-lookup"><span data-stu-id="e6931-117">Low</span></span>        |
| <span data-ttu-id="e6931-118">[Cosmos： byte [] 現在會儲存為 base64 字串，而不是數位陣列](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="e6931-118">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="e6931-119">低度</span><span class="sxs-lookup"><span data-stu-id="e6931-119">Low</span></span>        |
| [<span data-ttu-id="e6931-120">Cosmos： GetPropertyName 和 SetPropertyName 已重新命名</span><span class="sxs-lookup"><span data-stu-id="e6931-120">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="e6931-121">低度</span><span class="sxs-lookup"><span data-stu-id="e6931-121">Low</span></span>        |
| [<span data-ttu-id="e6931-122">當實體狀態從卸離變更為未變更、更新或刪除時，會呼叫值產生器</span><span class="sxs-lookup"><span data-stu-id="e6931-122">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="e6931-123">低度</span><span class="sxs-lookup"><span data-stu-id="e6931-123">Low</span></span>        |
| [<span data-ttu-id="e6931-124">IMigrationsModelDiffer 現在使用 IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="e6931-124">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="e6931-125">低度</span><span class="sxs-lookup"><span data-stu-id="e6931-125">Low</span></span>        |
| [<span data-ttu-id="e6931-126">鑒別子是唯讀的</span><span class="sxs-lookup"><span data-stu-id="e6931-126">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="e6931-127">低度</span><span class="sxs-lookup"><span data-stu-id="e6931-127">Low</span></span>        |
| [<span data-ttu-id="e6931-128">提供者特定的 EF。InMemory 提供者的函數方法擲回</span><span class="sxs-lookup"><span data-stu-id="e6931-128">Provider-specific EF.Functions methods throw for InMemory provider</span></span>](#no-client-methods)                                              | <span data-ttu-id="e6931-129">低度</span><span class="sxs-lookup"><span data-stu-id="e6931-129">Low</span></span>        |

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="e6931-130">已從 SQLite NTS 擴充功能移除 HasGeometricDimension 方法</span><span class="sxs-lookup"><span data-stu-id="e6931-130">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="e6931-131">追蹤問題 #14257</span><span class="sxs-lookup"><span data-stu-id="e6931-131">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="e6931-132">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="e6931-132">**Old behavior**</span></span>

<span data-ttu-id="e6931-133">HasGeometricDimension 用來在幾何資料行上啟用 (Z 和 M) 的其他維度。</span><span class="sxs-lookup"><span data-stu-id="e6931-133">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="e6931-134">但是，它只會影響資料庫的建立。</span><span class="sxs-lookup"><span data-stu-id="e6931-134">However, it only ever affected database creation.</span></span> <span data-ttu-id="e6931-135">不需要將它指定為使用其他維度來查詢值。</span><span class="sxs-lookup"><span data-stu-id="e6931-135">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="e6931-136">使用額外的維度插入或更新值時，也無法正確運作 ([請參閱 #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)) 。</span><span class="sxs-lookup"><span data-stu-id="e6931-136">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="e6931-137">**新的行為**</span><span class="sxs-lookup"><span data-stu-id="e6931-137">**New behavior**</span></span>

<span data-ttu-id="e6931-138">若要啟用以其他維度插入和更新幾何值 (Z 和 M) ，必須將維度指定為數據行類型名稱的一部分。</span><span class="sxs-lookup"><span data-stu-id="e6931-138">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="e6931-139">此 API 更接近 SpatiaLite 的 AddGeometryColumn 函式的基礎行為。</span><span class="sxs-lookup"><span data-stu-id="e6931-139">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="e6931-140">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="e6931-140">**Why**</span></span>

<span data-ttu-id="e6931-141">在資料行類型中指定維度之後使用 HasGeometricDimension 是不必要且多餘的，因此我們會完全移除 HasGeometricDimension。</span><span class="sxs-lookup"><span data-stu-id="e6931-141">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="e6931-142">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="e6931-142">**Mitigations**</span></span>

<span data-ttu-id="e6931-143">使用 `HasColumnType` 指定維度：</span><span class="sxs-lookup"><span data-stu-id="e6931-143">Use `HasColumnType` to specify the dimension:</span></span>

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

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="e6931-144">從主體到相依的導覽上的必要項具有不同的語義</span><span class="sxs-lookup"><span data-stu-id="e6931-144">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="e6931-145">追蹤問題 #17286</span><span class="sxs-lookup"><span data-stu-id="e6931-145">Tracking Issue #17286</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

<span data-ttu-id="e6931-146">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="e6931-146">**Old behavior**</span></span>

<span data-ttu-id="e6931-147">只有導覽至主體可設定為必要。</span><span class="sxs-lookup"><span data-stu-id="e6931-147">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="e6931-148">因此， `RequiredAttribute` 在相依 (的實體上使用，包含外鍵的實體) 會改為在定義實體類型上建立外鍵。</span><span class="sxs-lookup"><span data-stu-id="e6931-148">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

<span data-ttu-id="e6931-149">**新的行為**</span><span class="sxs-lookup"><span data-stu-id="e6931-149">**New behavior**</span></span>

<span data-ttu-id="e6931-150">由於已新增對必要相依性的支援，現在可以視需要將任何參考導覽標記，這表示在上方顯示的情況下，外鍵將會定義于關聯性的另一端，而且屬性不會標示為必要項。</span><span class="sxs-lookup"><span data-stu-id="e6931-150">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="e6931-151">`IsRequired`在指定相依端點之前呼叫，現在是不明確的：</span><span class="sxs-lookup"><span data-stu-id="e6931-151">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

<span data-ttu-id="e6931-152">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="e6931-152">**Why**</span></span>

<span data-ttu-id="e6931-153">若要啟用必要相依性的支援，必須要有新的行為 ([請參閱 #12100](https://github.com/dotnet/efcore/issues/12100)) 。</span><span class="sxs-lookup"><span data-stu-id="e6931-153">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

<span data-ttu-id="e6931-154">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="e6931-154">**Mitigations**</span></span>

<span data-ttu-id="e6931-155">`RequiredAttribute`從導覽中移除相依性，並將其放在主體的導覽上，或在中設定關聯性 `OnModelCreating` ：</span><span class="sxs-lookup"><span data-stu-id="e6931-155">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="e6931-156">Cosmos：現在已將資料分割索引鍵新增至主要索引鍵</span><span class="sxs-lookup"><span data-stu-id="e6931-156">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="e6931-157">追蹤問題 #15289</span><span class="sxs-lookup"><span data-stu-id="e6931-157">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="e6931-158">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="e6931-158">**Old behavior**</span></span>

<span data-ttu-id="e6931-159">分割區索引鍵屬性只會新增至包含的替代索引鍵 `id` 。</span><span class="sxs-lookup"><span data-stu-id="e6931-159">The partition key property was only added to the alternate key that includes `id`.</span></span>

<span data-ttu-id="e6931-160">**新的行為**</span><span class="sxs-lookup"><span data-stu-id="e6931-160">**New behavior**</span></span>

<span data-ttu-id="e6931-161">依慣例，資料分割索引鍵屬性現在也會依慣例新增至主要索引鍵。</span><span class="sxs-lookup"><span data-stu-id="e6931-161">The partition key property is now also added to the primary key by convention.</span></span>

<span data-ttu-id="e6931-162">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="e6931-162">**Why**</span></span>

<span data-ttu-id="e6931-163">這項變更可讓模型更符合 Azure Cosmos DB 的語義，並改善 `Find` 和某些查詢的效能。</span><span class="sxs-lookup"><span data-stu-id="e6931-163">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

<span data-ttu-id="e6931-164">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="e6931-164">**Mitigations**</span></span>

<span data-ttu-id="e6931-165">若要避免將分割區索引鍵屬性加入至主鍵，請在中進行設定 `OnModelCreating` 。</span><span class="sxs-lookup"><span data-stu-id="e6931-165">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="e6931-166">Cosmos： `id` 屬性已重新命名為 `__id`</span><span class="sxs-lookup"><span data-stu-id="e6931-166">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="e6931-167">追蹤問題 #17751</span><span class="sxs-lookup"><span data-stu-id="e6931-167">Tracking Issue #17751</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17751)

<span data-ttu-id="e6931-168">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="e6931-168">**Old behavior**</span></span>

<span data-ttu-id="e6931-169">對應至 JSON 屬性的陰影屬性 `id` 也稱為 `id` 。</span><span class="sxs-lookup"><span data-stu-id="e6931-169">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

<span data-ttu-id="e6931-170">**新的行為**</span><span class="sxs-lookup"><span data-stu-id="e6931-170">**New behavior**</span></span>

<span data-ttu-id="e6931-171">慣例所建立的陰影屬性現在已命名 `__id` 。</span><span class="sxs-lookup"><span data-stu-id="e6931-171">The shadow property created by convention is now named `__id`.</span></span>

<span data-ttu-id="e6931-172">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="e6931-172">**Why**</span></span>

<span data-ttu-id="e6931-173">這種變更可讓 `id` 屬性與實體型別上的現有屬性不衝突。</span><span class="sxs-lookup"><span data-stu-id="e6931-173">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

<span data-ttu-id="e6931-174">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="e6931-174">**Mitigations**</span></span>

<span data-ttu-id="e6931-175">若要返回3.x 行為，請 `id` 在中設定屬性 `OnModelCreating` 。</span><span class="sxs-lookup"><span data-stu-id="e6931-175">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```cs
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="e6931-176">Cosmos： byte [] 現在會儲存為 base64 字串，而不是數位陣列</span><span class="sxs-lookup"><span data-stu-id="e6931-176">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="e6931-177">追蹤問題 #17306</span><span class="sxs-lookup"><span data-stu-id="e6931-177">Tracking Issue #17306</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17306)

<span data-ttu-id="e6931-178">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="e6931-178">**Old behavior**</span></span>

<span data-ttu-id="e6931-179">Byte [] 類型的屬性已儲存為數字陣列。</span><span class="sxs-lookup"><span data-stu-id="e6931-179">Properties of type byte[] were stored as a number array.</span></span>

<span data-ttu-id="e6931-180">**新的行為**</span><span class="sxs-lookup"><span data-stu-id="e6931-180">**New behavior**</span></span>

<span data-ttu-id="e6931-181">Byte [] 類型的屬性現在會儲存為 base64 字串。</span><span class="sxs-lookup"><span data-stu-id="e6931-181">Properties of type byte[] are now stored as a base64 string.</span></span>

<span data-ttu-id="e6931-182">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="e6931-182">**Why**</span></span>

<span data-ttu-id="e6931-183">Byte [] 的這個標記法符合預期，而且是主要 JSON 序列化程式庫的預設行為。</span><span class="sxs-lookup"><span data-stu-id="e6931-183">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

<span data-ttu-id="e6931-184">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="e6931-184">**Mitigations**</span></span>

<span data-ttu-id="e6931-185">以數位陣列形式儲存的現有資料仍會正確地進行查詢，但目前不支援變更插入行為的方式。</span><span class="sxs-lookup"><span data-stu-id="e6931-185">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="e6931-186">如果這項限制會封鎖您的案例，請針對[此問題](https://github.com/aspnet/EntityFrameworkCore/issues/17306)進行批註</span><span class="sxs-lookup"><span data-stu-id="e6931-186">If this limitation is blocking your scenario, comment on [this issue](https://github.com/aspnet/EntityFrameworkCore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="e6931-187">Cosmos： GetPropertyName 和 SetPropertyName 已重新命名</span><span class="sxs-lookup"><span data-stu-id="e6931-187">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="e6931-188">追蹤問題 #17874</span><span class="sxs-lookup"><span data-stu-id="e6931-188">Tracking Issue #17874</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17874)

<span data-ttu-id="e6931-189">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="e6931-189">**Old behavior**</span></span>

<span data-ttu-id="e6931-190">先前已呼叫擴充方法 `GetPropertyName` ，而且 `SetPropertyName`</span><span class="sxs-lookup"><span data-stu-id="e6931-190">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

<span data-ttu-id="e6931-191">**新的行為**</span><span class="sxs-lookup"><span data-stu-id="e6931-191">**New behavior**</span></span>

<span data-ttu-id="e6931-192">移除舊的 API 並新增新 `GetJsonPropertyName` 方法： `SetJsonPropertyName`</span><span class="sxs-lookup"><span data-stu-id="e6931-192">The old API was removed and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

<span data-ttu-id="e6931-193">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="e6931-193">**Why**</span></span>

<span data-ttu-id="e6931-194">這項變更會移除這些方法所設定的混淆。</span><span class="sxs-lookup"><span data-stu-id="e6931-194">This change removes the ambiguity around what these methods are configuring.</span></span>

<span data-ttu-id="e6931-195">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="e6931-195">**Mitigations**</span></span>

<span data-ttu-id="e6931-196">使用新的 API。</span><span class="sxs-lookup"><span data-stu-id="e6931-196">Use the new API.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="e6931-197">當實體狀態從卸離變更為未變更、更新或刪除時，會呼叫值產生器</span><span class="sxs-lookup"><span data-stu-id="e6931-197">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="e6931-198">追蹤問題 #15289</span><span class="sxs-lookup"><span data-stu-id="e6931-198">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="e6931-199">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="e6931-199">**Old behavior**</span></span>

<span data-ttu-id="e6931-200">只有當實體狀態變更為 [已加入] 時，才會呼叫值產生器。</span><span class="sxs-lookup"><span data-stu-id="e6931-200">Value generators were only called when the entity state changed to Added.</span></span>

<span data-ttu-id="e6931-201">**新的行為**</span><span class="sxs-lookup"><span data-stu-id="e6931-201">**New behavior**</span></span>

<span data-ttu-id="e6931-202">現在當實體狀態從卸離變更為未變更、已更新或已刪除，且屬性包含預設值時，就會呼叫值產生器。</span><span class="sxs-lookup"><span data-stu-id="e6931-202">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

<span data-ttu-id="e6931-203">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="e6931-203">**Why**</span></span>

<span data-ttu-id="e6931-204">需要進行這種變更，才能使用不會保存到資料存放區的屬性，以及在用戶端上永遠產生其值的方式，來改善體驗。</span><span class="sxs-lookup"><span data-stu-id="e6931-204">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

<span data-ttu-id="e6931-205">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="e6931-205">**Mitigations**</span></span>

<span data-ttu-id="e6931-206">若要防止呼叫值產生器，請在狀態變更之前，將非預設值指派給屬性。</span><span class="sxs-lookup"><span data-stu-id="e6931-206">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="e6931-207">IMigrationsModelDiffer 現在使用 IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="e6931-207">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="e6931-208">追蹤問題 #20305</span><span class="sxs-lookup"><span data-stu-id="e6931-208">Tracking Issue #20305</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

<span data-ttu-id="e6931-209">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="e6931-209">**Old behavior**</span></span>

<span data-ttu-id="e6931-210">`IMigrationsModelDiffer` API 的定義使用 `IModel` 。</span><span class="sxs-lookup"><span data-stu-id="e6931-210">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

<span data-ttu-id="e6931-211">**新的行為**</span><span class="sxs-lookup"><span data-stu-id="e6931-211">**New behavior**</span></span>

<span data-ttu-id="e6931-212">`IMigrationsModelDiffer` API 現在會使用 `IRelationalModel` 。</span><span class="sxs-lookup"><span data-stu-id="e6931-212">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="e6931-213">不過，模型快照集仍會包含， `IModel` 因為此程式碼是應用程式的一部分，而且 Entity Framework 無法變更它，而不會進行較大的中斷變更。</span><span class="sxs-lookup"><span data-stu-id="e6931-213">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

<span data-ttu-id="e6931-214">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="e6931-214">**Why**</span></span>

<span data-ttu-id="e6931-215">`IRelationalModel` 這是新加入的資料庫架構標記法。</span><span class="sxs-lookup"><span data-stu-id="e6931-215">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="e6931-216">使用它來尋找差異會更快且更精確。</span><span class="sxs-lookup"><span data-stu-id="e6931-216">Using it to find differences is faster and more accurate.</span></span>

<span data-ttu-id="e6931-217">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="e6931-217">**Mitigations**</span></span>

<span data-ttu-id="e6931-218">您可以使用下列程式碼，將模型 `snapshot` 與模型進行比較， `context` 如下所示：</span><span class="sxs-lookup"><span data-stu-id="e6931-218">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

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

<span data-ttu-id="e6931-219">我們打算在 6.0 ([查看 #22031](https://github.com/dotnet/efcore/issues/22031) 的這項體驗) </span><span class="sxs-lookup"><span data-stu-id="e6931-219">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="e6931-220">鑒別子是唯讀的</span><span class="sxs-lookup"><span data-stu-id="e6931-220">Discriminators are read-only</span></span>

[<span data-ttu-id="e6931-221">追蹤問題 #21154</span><span class="sxs-lookup"><span data-stu-id="e6931-221">Tracking Issue #21154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

<span data-ttu-id="e6931-222">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="e6931-222">**Old behavior**</span></span>

<span data-ttu-id="e6931-223">您可以在呼叫之前變更鑒別子值 `SaveChanges`</span><span class="sxs-lookup"><span data-stu-id="e6931-223">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

<span data-ttu-id="e6931-224">**新的行為**</span><span class="sxs-lookup"><span data-stu-id="e6931-224">**New behavior**</span></span>

<span data-ttu-id="e6931-225">在上述案例中，將會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="e6931-225">An exception will be throws in the above case.</span></span>

<span data-ttu-id="e6931-226">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="e6931-226">**Why**</span></span>

<span data-ttu-id="e6931-227">EF 不會預期實體類型在仍在追蹤時變更，因此變更鑒別子值會使內容處於不一致的狀態，而這可能會導致非預期的行為。</span><span class="sxs-lookup"><span data-stu-id="e6931-227">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

<span data-ttu-id="e6931-228">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="e6931-228">**Mitigations**</span></span>

<span data-ttu-id="e6931-229">如果需要變更鑒別子值，而且會在呼叫後立即處置內容 `SaveChanges` ，則可以將鑒別子設為可變：</span><span class="sxs-lookup"><span data-stu-id="e6931-229">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```cs
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="e6931-230">定義查詢會取代為提供者特定的方法</span><span class="sxs-lookup"><span data-stu-id="e6931-230">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="e6931-231">追蹤問題 #18903</span><span class="sxs-lookup"><span data-stu-id="e6931-231">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

<span data-ttu-id="e6931-232">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="e6931-232">**Old behavior**</span></span>

<span data-ttu-id="e6931-233">實體類型已對應至在核心層級定義查詢。</span><span class="sxs-lookup"><span data-stu-id="e6931-233">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="e6931-234">任何時候，實體類型的查詢根目錄中使用的實體類型已由任何提供者的定義查詢所取代。</span><span class="sxs-lookup"><span data-stu-id="e6931-234">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

<span data-ttu-id="e6931-235">**新的行為**</span><span class="sxs-lookup"><span data-stu-id="e6931-235">**New behavior**</span></span>

<span data-ttu-id="e6931-236">用於定義查詢的 Api 已被取代。</span><span class="sxs-lookup"><span data-stu-id="e6931-236">APIs for defining query are deprecated.</span></span> <span data-ttu-id="e6931-237">引進了新的提供者特定 Api。</span><span class="sxs-lookup"><span data-stu-id="e6931-237">New provider-specific APIs were introduced.</span></span>

<span data-ttu-id="e6931-238">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="e6931-238">**Why**</span></span>

<span data-ttu-id="e6931-239">當查詢中使用查詢根時，定義查詢會實作為取代查詢，但有幾個問題：</span><span class="sxs-lookup"><span data-stu-id="e6931-239">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="e6931-240">如果定義查詢是使用 in 方法投射實體型別 `new { ... }` `Select` ，則將其識別為實體需要額外的工作，並使其與 EF Core 在查詢中處理名義類型的方式不一致。</span><span class="sxs-lookup"><span data-stu-id="e6931-240">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="e6931-241">針對關聯式提供者， `FromSql` 仍需要以 LINQ 運算式形式傳遞 SQL 字串。</span><span class="sxs-lookup"><span data-stu-id="e6931-241">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="e6931-242">最初定義查詢是以用戶端視圖的形式導入，以用於無索引鍵實體的記憶體內部提供者 (類似于關係資料庫) 中的資料庫檢視。</span><span class="sxs-lookup"><span data-stu-id="e6931-242">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="e6931-243">這類定義可讓您輕鬆地針對記憶體中資料庫測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="e6931-243">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="e6931-244">之後，這些應用程式很有用，但卻變得不一致，而且難以理解的行為。</span><span class="sxs-lookup"><span data-stu-id="e6931-244">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="e6931-245">所以我們決定簡化這個概念。</span><span class="sxs-lookup"><span data-stu-id="e6931-245">So we decided to simplify the concept.</span></span> <span data-ttu-id="e6931-246">我們對記憶體內部提供者進行以 LINQ 為基礎的定義查詢，並以不同的方式加以處理。</span><span class="sxs-lookup"><span data-stu-id="e6931-246">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="e6931-247">如需詳細資訊，請 [參閱此問題](https://github.com/dotnet/efcore/issues/20023)。</span><span class="sxs-lookup"><span data-stu-id="e6931-247">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

<span data-ttu-id="e6931-248">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="e6931-248">**Mitigations**</span></span>

<span data-ttu-id="e6931-249">若為關聯式提供者，請使用 `ToSqlQuery` 中的方法 `OnModelCreating` 並傳入要用於實體類型的 SQL 字串。</span><span class="sxs-lookup"><span data-stu-id="e6931-249">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="e6931-250">對於記憶體中的提供者，請使用 `ToInMemoryQuery` 中的方法， `OnModelCreating` 並傳入 LINQ 查詢以用於實體型別。</span><span class="sxs-lookup"><span data-stu-id="e6931-250">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a><span data-ttu-id="e6931-251">提供者特定的 EF。InMemory 提供者的函數方法擲回</span><span class="sxs-lookup"><span data-stu-id="e6931-251">Provider-specific EF.Functions methods throw for InMemory provider</span></span>

[<span data-ttu-id="e6931-252">追蹤問題 #20294</span><span class="sxs-lookup"><span data-stu-id="e6931-252">Tracking Issue #20294</span></span>](https://github.com/dotnet/efcore/issues/20294)

<span data-ttu-id="e6931-253">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="e6931-253">**Old behavior**</span></span>

<span data-ttu-id="e6931-254">提供者特定的 EF。函數方法包含用戶端執行的執行，可讓它們在 InMemory 提供者上執行。</span><span class="sxs-lookup"><span data-stu-id="e6931-254">Provider-specific EF.Functions methods contained implementation for client execution, which allowed them to be executed on the InMemory provider.</span></span> <span data-ttu-id="e6931-255">例如， `EF.Functions.DateDiffDay` 是一種 Sql Server 特定的方法，它是在 InMemory 提供者上處理。</span><span class="sxs-lookup"><span data-stu-id="e6931-255">For example, `EF.Functions.DateDiffDay` is a Sql Server specific method, which worked on InMemory provider.</span></span>

<span data-ttu-id="e6931-256">**新的行為**</span><span class="sxs-lookup"><span data-stu-id="e6931-256">**New behavior**</span></span>

<span data-ttu-id="e6931-257">提供者特定方法已更新，在其方法主體中擲回例外狀況，以封鎖在用戶端上進行評估。</span><span class="sxs-lookup"><span data-stu-id="e6931-257">Provider-specific methods have been updated to throw an exception in their method body to block evaluating them on client side.</span></span>

<span data-ttu-id="e6931-258">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="e6931-258">**Why**</span></span>

<span data-ttu-id="e6931-259">提供者特定的方法會對應至資料庫函式。</span><span class="sxs-lookup"><span data-stu-id="e6931-259">Provider-specific methods map to a database function.</span></span> <span data-ttu-id="e6931-260">對應資料庫函式所完成的計算無法永遠在 LINQ 的用戶端上複寫。</span><span class="sxs-lookup"><span data-stu-id="e6931-260">The computation done by the mapped database function can't always be replicated on the client side in LINQ.</span></span> <span data-ttu-id="e6931-261">在用戶端上執行相同的方法時，可能會導致伺服器的結果不同。</span><span class="sxs-lookup"><span data-stu-id="e6931-261">It may cause the result from the server to differ when executing the same method on client.</span></span> <span data-ttu-id="e6931-262">因為這些方法是在 LINQ 中用來轉譯為特定的資料庫函式，所以不需要在用戶端上進行評估。</span><span class="sxs-lookup"><span data-stu-id="e6931-262">Since these methods are used in LINQ to translate to specific database functions, they don't need to be evaluated on client side.</span></span> <span data-ttu-id="e6931-263">因為 InMemory 提供者是不同的 *資料庫*，所以這些方法不適用於此提供者。</span><span class="sxs-lookup"><span data-stu-id="e6931-263">As InMemory provider is a different *database*, these methods aren't available for this provider.</span></span> <span data-ttu-id="e6931-264">嘗試針對 InMemory 提供者或任何其他未轉譯這些方法的提供者執行這些專案時，會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="e6931-264">Trying to execute them for InMemory provider, or any other provider that doesn't translate these methods, throws an exception.</span></span>

<span data-ttu-id="e6931-265">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="e6931-265">**Mitigations**</span></span>

<span data-ttu-id="e6931-266">因為無法準確模擬資料庫函式的行為，所以您應該針對與生產環境中相同類型的資料庫，測試包含它們的查詢。</span><span class="sxs-lookup"><span data-stu-id="e6931-266">Since there's no way to mimic behavior of database functions accurately, you should test the queries containing them against same kind of database as in production.</span></span>
