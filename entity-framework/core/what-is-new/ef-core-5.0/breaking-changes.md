---
title: EF Core 5.0-EF Core 的重大變更
description: Entity Framework Core 5.0 中引進的重大變更完整清單
author: bricelam
ms.date: 09/08/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: bc6db48edcd7406b31ec2b4369cabf5d55fb4578
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618683"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="ad414-103">EF Core 5.0 中的重大變更</span><span class="sxs-lookup"><span data-stu-id="ad414-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="ad414-104">下列 API 和行為變更可能會中斷現有應用程式更新為 EF Core 5.0.0。</span><span class="sxs-lookup"><span data-stu-id="ad414-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="ad414-105">摘要</span><span class="sxs-lookup"><span data-stu-id="ad414-105">Summary</span></span>

| <span data-ttu-id="ad414-106">**重大變更**</span><span class="sxs-lookup"><span data-stu-id="ad414-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="ad414-107">**影響**</span><span class="sxs-lookup"><span data-stu-id="ad414-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="ad414-108">從主體到相依的導覽上的必要項具有不同的語義</span><span class="sxs-lookup"><span data-stu-id="ad414-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="ad414-109">中</span><span class="sxs-lookup"><span data-stu-id="ad414-109">Medium</span></span>     |
| [<span data-ttu-id="ad414-110">已從 SQLite NTS 擴充功能移除 HasGeometricDimension 方法</span><span class="sxs-lookup"><span data-stu-id="ad414-110">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="ad414-111">低</span><span class="sxs-lookup"><span data-stu-id="ad414-111">Low</span></span>        |
| [<span data-ttu-id="ad414-112">當實體狀態從已卸離變更為未變更、已更新或已刪除時，會呼叫值產生器</span><span class="sxs-lookup"><span data-stu-id="ad414-112">Value generators are called when the entity state is changed from Detached to Unchanged, Updated or Deleted</span></span>](#non-added-generation)  | <span data-ttu-id="ad414-113">低</span><span class="sxs-lookup"><span data-stu-id="ad414-113">Low</span></span>        |
| [<span data-ttu-id="ad414-114">IMigrationsModelDiffer 現在使用 IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="ad414-114">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="ad414-115">低</span><span class="sxs-lookup"><span data-stu-id="ad414-115">Low</span></span>        |
| [<span data-ttu-id="ad414-116">鑒別子是唯讀的</span><span class="sxs-lookup"><span data-stu-id="ad414-116">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="ad414-117">低</span><span class="sxs-lookup"><span data-stu-id="ad414-117">Low</span></span>        |

<a name="geometric-sqlite"></a>
### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="ad414-118">已從 SQLite NTS 擴充功能移除 HasGeometricDimension 方法</span><span class="sxs-lookup"><span data-stu-id="ad414-118">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="ad414-119">追蹤問題 #14257</span><span class="sxs-lookup"><span data-stu-id="ad414-119">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="ad414-120">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="ad414-120">**Old behavior**</span></span>

<span data-ttu-id="ad414-121">HasGeometricDimension 用來在幾何資料行上啟用 (Z 和 M) 的其他維度。</span><span class="sxs-lookup"><span data-stu-id="ad414-121">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="ad414-122">但是，它只會影響資料庫的建立。</span><span class="sxs-lookup"><span data-stu-id="ad414-122">However, it only ever affected database creation.</span></span> <span data-ttu-id="ad414-123">不需要將它指定為使用其他維度來查詢值。</span><span class="sxs-lookup"><span data-stu-id="ad414-123">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="ad414-124">使用額外的維度插入或更新值時，也無法正確運作 ([請參閱 #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)) 。</span><span class="sxs-lookup"><span data-stu-id="ad414-124">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="ad414-125">**新行為**</span><span class="sxs-lookup"><span data-stu-id="ad414-125">**New behavior**</span></span>

<span data-ttu-id="ad414-126">若要啟用以其他維度插入和更新幾何值 (Z 和 M) ，必須將維度指定為數據行類型名稱的一部分。</span><span class="sxs-lookup"><span data-stu-id="ad414-126">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="ad414-127">這比起 SpatiaLite 的 AddGeometryColumn 函式的基礎行為更緊密。</span><span class="sxs-lookup"><span data-stu-id="ad414-127">This more closely matches the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="ad414-128">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="ad414-128">**Why**</span></span>

<span data-ttu-id="ad414-129">在資料行類型中指定維度之後使用 HasGeometricDimension 是不必要且多餘的，因此我們會完全移除 HasGeometricDimension。</span><span class="sxs-lookup"><span data-stu-id="ad414-129">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="ad414-130">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="ad414-130">**Mitigations**</span></span>

<span data-ttu-id="ad414-131">使用 `HasColumnType` 指定維度：</span><span class="sxs-lookup"><span data-stu-id="ad414-131">Use `HasColumnType` to specify the dimension:</span></span>

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
### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="ad414-132">從主體到相依的導覽上的必要項具有不同的語義</span><span class="sxs-lookup"><span data-stu-id="ad414-132">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="ad414-133">追蹤問題 #17286</span><span class="sxs-lookup"><span data-stu-id="ad414-133">Tracking Issue #17286</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

<span data-ttu-id="ad414-134">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="ad414-134">**Old behavior**</span></span>

<span data-ttu-id="ad414-135">只有導覽至主體可設定為必要。</span><span class="sxs-lookup"><span data-stu-id="ad414-135">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="ad414-136">因此， `RequiredAttribute` 在相依 (的實體上使用，包含外鍵的實體) 會改為在定義實體類型上建立外鍵。</span><span class="sxs-lookup"><span data-stu-id="ad414-136">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

<span data-ttu-id="ad414-137">**新行為**</span><span class="sxs-lookup"><span data-stu-id="ad414-137">**New behavior**</span></span>

<span data-ttu-id="ad414-138">由於已新增對必要相依性的支援，現在可以視需要將任何參考導覽標示出來，這表示在上方顯示的情況下，外鍵將會定義于關聯性的另一端，而且屬性不會標示為必要項。</span><span class="sxs-lookup"><span data-stu-id="ad414-138">With the added support for required dependents it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="ad414-139">`IsRequired`在指定相依端點之前呼叫，現在是不明確的：</span><span class="sxs-lookup"><span data-stu-id="ad414-139">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

<span data-ttu-id="ad414-140">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="ad414-140">**Why**</span></span>

<span data-ttu-id="ad414-141">若要啟用必要相依性的支援，必須要有新的行為 ([請參閱 #12100](https://github.com/dotnet/efcore/issues/12100)) 。</span><span class="sxs-lookup"><span data-stu-id="ad414-141">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

<span data-ttu-id="ad414-142">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="ad414-142">**Mitigations**</span></span>

<span data-ttu-id="ad414-143">`RequiredAttribute`從導覽中移除相依性，並將其放在主體的導覽上，或在中設定關聯性 `OnModelCreating` ：</span><span class="sxs-lookup"><span data-stu-id="ad414-143">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="non-added-generation"></a>
### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="ad414-144">當實體狀態從已卸離變更為未變更、已更新或已刪除時，會呼叫值產生器</span><span class="sxs-lookup"><span data-stu-id="ad414-144">Value generators are called when the entity state is changed from Detached to Unchanged, Updated or Deleted</span></span>

[<span data-ttu-id="ad414-145">追蹤問題 #15289</span><span class="sxs-lookup"><span data-stu-id="ad414-145">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="ad414-146">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="ad414-146">**Old behavior**</span></span>

<span data-ttu-id="ad414-147">只有當實體狀態變更為 [已加入] 時，才會呼叫值產生器。</span><span class="sxs-lookup"><span data-stu-id="ad414-147">Value generators were only called when the entity state changed to Added.</span></span>

<span data-ttu-id="ad414-148">**新行為**</span><span class="sxs-lookup"><span data-stu-id="ad414-148">**New behavior**</span></span>

<span data-ttu-id="ad414-149">現在當實體狀態從卸離變更為未變更、已更新或已刪除，而且屬性包含預設值時，就會呼叫值產生器。</span><span class="sxs-lookup"><span data-stu-id="ad414-149">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated or Deleted and the property contains the default values.</span></span>

<span data-ttu-id="ad414-150">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="ad414-150">**Why**</span></span>

<span data-ttu-id="ad414-151">需要進行這種變更，才能使用不會保存到資料存放區的屬性，以及在用戶端上永遠產生其值的方式，來改善體驗。</span><span class="sxs-lookup"><span data-stu-id="ad414-151">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

<span data-ttu-id="ad414-152">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="ad414-152">**Mitigations**</span></span>

<span data-ttu-id="ad414-153">若要防止呼叫值產生器，請在狀態變更之前將非預設值指派給屬性。</span><span class="sxs-lookup"><span data-stu-id="ad414-153">To prevent the value generator from being called assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>
### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="ad414-154">IMigrationsModelDiffer 現在使用 IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="ad414-154">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="ad414-155">追蹤問題 #20305</span><span class="sxs-lookup"><span data-stu-id="ad414-155">Tracking Issue #20305</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

<span data-ttu-id="ad414-156">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="ad414-156">**Old behavior**</span></span>

<span data-ttu-id="ad414-157">`IMigrationsModelDiffer` API 的定義使用 `IModel` 。</span><span class="sxs-lookup"><span data-stu-id="ad414-157">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

<span data-ttu-id="ad414-158">**新行為**</span><span class="sxs-lookup"><span data-stu-id="ad414-158">**New behavior**</span></span>

<span data-ttu-id="ad414-159">`IMigrationsModelDiffer` API 現在會使用 `IRelationalModel` 。</span><span class="sxs-lookup"><span data-stu-id="ad414-159">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="ad414-160">不過，模型快照集仍會包含， `IModel` 因為此程式碼是應用程式的一部分，而且 Entity Framework 無法變更它，而不會進行較大的中斷變更。</span><span class="sxs-lookup"><span data-stu-id="ad414-160">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

<span data-ttu-id="ad414-161">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="ad414-161">**Why**</span></span>

<span data-ttu-id="ad414-162">`IRelationalModel` 這是新加入的資料庫架構標記法。</span><span class="sxs-lookup"><span data-stu-id="ad414-162">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="ad414-163">使用它來尋找差異會更快且更精確。</span><span class="sxs-lookup"><span data-stu-id="ad414-163">Using it to find differences is faster and more accurate.</span></span>

<span data-ttu-id="ad414-164">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="ad414-164">**Mitigations**</span></span>

<span data-ttu-id="ad414-165">您可以使用下列程式碼，將模型 `snapshot` 與模型進行比較， `context` 如下所示：</span><span class="sxs-lookup"><span data-stu-id="ad414-165">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

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

<span data-ttu-id="ad414-166">我們打算在 6.0 ([查看 #22031](https://github.com/dotnet/efcore/issues/22031) 的這項體驗) </span><span class="sxs-lookup"><span data-stu-id="ad414-166">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>
### <a name="discriminators-are-read-only"></a><span data-ttu-id="ad414-167">鑒別子是唯讀的</span><span class="sxs-lookup"><span data-stu-id="ad414-167">Discriminators are read-only</span></span>

[<span data-ttu-id="ad414-168">追蹤問題 #21154</span><span class="sxs-lookup"><span data-stu-id="ad414-168">Tracking Issue #21154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

<span data-ttu-id="ad414-169">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="ad414-169">**Old behavior**</span></span>

<span data-ttu-id="ad414-170">您可以在呼叫之前變更鑒別子值 `SaveChanges`</span><span class="sxs-lookup"><span data-stu-id="ad414-170">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

<span data-ttu-id="ad414-171">**新行為**</span><span class="sxs-lookup"><span data-stu-id="ad414-171">**New behavior**</span></span>

<span data-ttu-id="ad414-172">在上述案例中，將會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ad414-172">An exception will be throws in the above case.</span></span>

<span data-ttu-id="ad414-173">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="ad414-173">**Why**</span></span>

<span data-ttu-id="ad414-174">EF 不會預期實體類型在仍在追蹤時變更，因此變更鑒別子值會使內容處於不一致的狀態，而這可能會導致非預期的行為。</span><span class="sxs-lookup"><span data-stu-id="ad414-174">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state which might result in unexpected behavior.</span></span>

<span data-ttu-id="ad414-175">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="ad414-175">**Mitigations**</span></span>

<span data-ttu-id="ad414-176">如果需要變更鑒別子值，而且在呼叫鑒別子之後，會立即處置內容 `SaveChanges` ：</span><span class="sxs-lookup"><span data-stu-id="ad414-176">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges` the discriminator can be made mutable:</span></span>

```cs
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```
