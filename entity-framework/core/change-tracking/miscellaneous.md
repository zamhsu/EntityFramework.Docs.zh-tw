---
title: 其他變更追蹤功能-EF Core
description: 涉及 EF Core 變更追蹤的其他功能和案例
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/miscellaneous
ms.openlocfilehash: 9eb3186f4eef300e4824dc86700497444ece4a2c
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543415"
---
# <a name="additional-change-tracking-features"></a><span data-ttu-id="ffc10-103">其他變更追蹤功能</span><span class="sxs-lookup"><span data-stu-id="ffc10-103">Additional Change Tracking Features</span></span>

<span data-ttu-id="ffc10-104">本檔涵蓋涉及變更追蹤的其他功能和案例。</span><span class="sxs-lookup"><span data-stu-id="ffc10-104">This document covers miscellaneous features and scenarios involving change tracking.</span></span>

> [!TIP]
> <span data-ttu-id="ffc10-105">本檔假設您已瞭解實體狀態以及 EF Core 變更追蹤的基本概念。</span><span class="sxs-lookup"><span data-stu-id="ffc10-105">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="ffc10-106">如需有關這些主題的詳細資訊，請參閱 [EF Core 中的變更追蹤](xref:core/change-tracking/index) 。</span><span class="sxs-lookup"><span data-stu-id="ffc10-106">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="ffc10-107">您可以 [從 GitHub 下載範例程式碼](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/AdditionalChangeTrackingFeatures)，以執行並偵測到本檔中的所有程式碼。</span><span class="sxs-lookup"><span data-stu-id="ffc10-107">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/AdditionalChangeTrackingFeatures).</span></span>

## <a name="add-versus-addasync"></a><span data-ttu-id="ffc10-108">`Add` 與 `AddAsync`</span><span class="sxs-lookup"><span data-stu-id="ffc10-108">`Add` versus `AddAsync`</span></span>

<span data-ttu-id="ffc10-109">Entity Framework Core (EF Core) 會在使用該方法時提供非同步方法，可能會導致資料庫互動。</span><span class="sxs-lookup"><span data-stu-id="ffc10-109">Entity Framework Core (EF Core) provides async methods whenever using that method may result in a database interaction.</span></span> <span data-ttu-id="ffc10-110">也提供同步方法，以避免使用不支援高效能非同步存取的資料庫時的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="ffc10-110">Synchronous methods are also provided to avoid overhead when using databases that do not support high performance asynchronous access.</span></span>

<span data-ttu-id="ffc10-111"><xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> 而且 <xref:Microsoft.EntityFrameworkCore.DbSet%601.Add%2A?displayProperty=nameWithType> 通常不會存取資料庫，因為這些方法原本就是開始追蹤實體。</span><span class="sxs-lookup"><span data-stu-id="ffc10-111"><xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.DbSet%601.Add%2A?displayProperty=nameWithType> do not normally access the database, since these methods inherently just start tracking entities.</span></span> <span data-ttu-id="ffc10-112">不過，某些形式的值產生 _可能會_ 存取資料庫，以便產生索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="ffc10-112">However, some forms of value generation _may_ access the database in order to generate a key value.</span></span> <span data-ttu-id="ffc10-113">唯一的值產生器會執行這項工作並隨附于 EF Core <xref:Microsoft.EntityFrameworkCore.ValueGeneration.HiLoValueGenerator%601> 。</span><span class="sxs-lookup"><span data-stu-id="ffc10-113">The only value generator that does this and ships with EF Core is <xref:Microsoft.EntityFrameworkCore.ValueGeneration.HiLoValueGenerator%601>.</span></span> <span data-ttu-id="ffc10-114">使用這個產生器並不常見;預設不會設定它。</span><span class="sxs-lookup"><span data-stu-id="ffc10-114">Using this generator is uncommon; it is never configured by default.</span></span> <span data-ttu-id="ffc10-115">這表示大部分的應用程式都應該使用 `Add` ，而不是 `AddAsync` 。</span><span class="sxs-lookup"><span data-stu-id="ffc10-115">This means that the vast majority of applications should use `Add`, and not `AddAsync`.</span></span>

<span data-ttu-id="ffc10-116">和、和等其他類似的方法並 `Update` `Attach` 沒有非同步多載 `Remove` ，因為它們絕對不會產生新的索引鍵值，因此永遠不需要存取資料庫。</span><span class="sxs-lookup"><span data-stu-id="ffc10-116">Other similar methods like `Update`, `Attach`, and `Remove` do not have async overloads because they never generate new key values, and hence never need to access the database.</span></span>

## <a name="addrange-updaterange-attachrange-and-removerange"></a><span data-ttu-id="ffc10-117">`AddRange`、`UpdateRange`、`AttachRange` 和 `RemoveRange`</span><span class="sxs-lookup"><span data-stu-id="ffc10-117">`AddRange`, `UpdateRange`, `AttachRange`, and `RemoveRange`</span></span>

<span data-ttu-id="ffc10-118"><xref:Microsoft.EntityFrameworkCore.DbSet%601> 以及 <xref:Microsoft.EntityFrameworkCore.DbContext> 提供 `Add` `Update` `Attach` `Remove` 可在單一呼叫中接受多個實例的、、和的替代版本。</span><span class="sxs-lookup"><span data-stu-id="ffc10-118"><xref:Microsoft.EntityFrameworkCore.DbSet%601> and <xref:Microsoft.EntityFrameworkCore.DbContext> provide alternate versions of `Add`, `Update`, `Attach`, and `Remove` that accept multiple instances in a single call.</span></span> <span data-ttu-id="ffc10-119">這些方法 <xref:Microsoft.EntityFrameworkCore.DbSet%601.AddRange%2A> 分別為、、 <xref:Microsoft.EntityFrameworkCore.DbSet%601.UpdateRange%2A> <xref:Microsoft.EntityFrameworkCore.DbSet%601.AttachRange%2A> 和 <xref:Microsoft.EntityFrameworkCore.DbSet%601.RemoveRange%2A> 。</span><span class="sxs-lookup"><span data-stu-id="ffc10-119">These methods are <xref:Microsoft.EntityFrameworkCore.DbSet%601.AddRange%2A>, <xref:Microsoft.EntityFrameworkCore.DbSet%601.UpdateRange%2A>, <xref:Microsoft.EntityFrameworkCore.DbSet%601.AttachRange%2A>, and <xref:Microsoft.EntityFrameworkCore.DbSet%601.RemoveRange%2A> respectively.</span></span>

<span data-ttu-id="ffc10-120">提供這些方法是為了方便起見。</span><span class="sxs-lookup"><span data-stu-id="ffc10-120">These methods are provided as a convenience.</span></span> <span data-ttu-id="ffc10-121">使用「範圍」方法的功能與對等的非範圍方法的多個呼叫相同。</span><span class="sxs-lookup"><span data-stu-id="ffc10-121">Using a "range" method has the same functionality as multiple calls to the equivalent non-range method.</span></span> <span data-ttu-id="ffc10-122">這兩種方法之間並沒有顯著的效能差異。</span><span class="sxs-lookup"><span data-stu-id="ffc10-122">There is no significant performance difference between the two approaches.</span></span>

> [!NOTE]
> <span data-ttu-id="ffc10-123">這不同于 EF6、 `AddRange` `Add` 會自動呼叫 `DetectChanges` ，但呼叫多次會導致多次呼叫 `Add` DetectChanges，而不是呼叫一次。</span><span class="sxs-lookup"><span data-stu-id="ffc10-123">This is different from EF6, where `AddRange` and `Add` both automatically called `DetectChanges`, but calling `Add` multiple times caused DetectChanges to be called multiple times instead of once.</span></span> <span data-ttu-id="ffc10-124">這 `AddRange` 在 EF6 時更有效率。</span><span class="sxs-lookup"><span data-stu-id="ffc10-124">This made `AddRange` more efficient in EF6.</span></span> <span data-ttu-id="ffc10-125">在 EF Core 中，這些方法都不會自動呼叫 `DetectChanges` 。</span><span class="sxs-lookup"><span data-stu-id="ffc10-125">In EF Core, neither of these methods automatically call `DetectChanges`.</span></span>

## <a name="dbcontext-versus-dbset-methods"></a><span data-ttu-id="ffc10-126">DbCoNtext 和 DbSet 方法</span><span class="sxs-lookup"><span data-stu-id="ffc10-126">DbContext versus DbSet methods</span></span>

<span data-ttu-id="ffc10-127">許多方法（包括 `Add` 、 `Update` 、 `Attach` 和 `Remove` ）都有 <xref:Microsoft.EntityFrameworkCore.DbSet%601> 和 <xref:Microsoft.EntityFrameworkCore.DbContext> 的實作為。</span><span class="sxs-lookup"><span data-stu-id="ffc10-127">Many methods, including `Add`, `Update`, `Attach`, and `Remove`, have implementations on both <xref:Microsoft.EntityFrameworkCore.DbSet%601> and <xref:Microsoft.EntityFrameworkCore.DbContext>.</span></span> <span data-ttu-id="ffc10-128">這些方法對一般實體類型具有 _完全相同的行為_ 。</span><span class="sxs-lookup"><span data-stu-id="ffc10-128">These methods have _exactly the same behavior_ for normal entity types.</span></span> <span data-ttu-id="ffc10-129">這是因為實體的 CLR 型別會對應到 EF Core 模型中的單一實體類型。</span><span class="sxs-lookup"><span data-stu-id="ffc10-129">This is because the CLR type of the entity is mapped onto one and only one entity type in the EF Core model.</span></span> <span data-ttu-id="ffc10-130">因此，CLR 型別會完整定義實體放在模型中的位置，因此可以隱含地判斷要使用的 DbSet。</span><span class="sxs-lookup"><span data-stu-id="ffc10-130">Therefore, the CLR type fully defines where the entity fits in the model, and so the DbSet to use can be determined implicitly.</span></span>

<span data-ttu-id="ffc10-131">這項規則的例外狀況是，使用在 EF Core 5.0 引進的共用類型實體類型時，主要是針對多對多聯結實體。</span><span class="sxs-lookup"><span data-stu-id="ffc10-131">The exception to this rule is when using shared-type entity types, which were introduced in EF Core 5.0, primarily for many-to-many join entities.</span></span> <span data-ttu-id="ffc10-132">使用共用類型實體類型時，必須先針對正在使用的 EF Core 模型類型建立 DbSet。</span><span class="sxs-lookup"><span data-stu-id="ffc10-132">When using a shared-type entity type, a DbSet must first be created for the EF Core model type that is being used.</span></span> <span data-ttu-id="ffc10-133">`Add` `Update` `Attach` 然後，可以在 DbSet 上使用、、和等方法， `Remove` 而不會因為使用哪一個 EF Core 模型類型而產生任何混淆。</span><span class="sxs-lookup"><span data-stu-id="ffc10-133">Methods like `Add`, `Update`, `Attach`, and `Remove` can then be used on the DbSet without any ambiguity as to which EF Core model type is being used.</span></span>

<span data-ttu-id="ffc10-134">針對多對多關聯性中的聯結實體，預設會使用共用類型的實體類型。</span><span class="sxs-lookup"><span data-stu-id="ffc10-134">Shared-type entity types are used by default for the join entities in many-to-many relationships.</span></span> <span data-ttu-id="ffc10-135">您也可以明確地設定共用類型實體類型，以便在多對多關聯性中使用。</span><span class="sxs-lookup"><span data-stu-id="ffc10-135">A shared-type entity type can also be explicitly configured for use in a many-to-many relationship.</span></span> <span data-ttu-id="ffc10-136">例如，下列程式碼會設定 `Dictionary<string, int>` 為聯結實體類型：</span><span class="sxs-lookup"><span data-stu-id="ffc10-136">For example, the code below configures `Dictionary<string, int>` as a join entity type:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .SharedTypeEntity<Dictionary<string, int>>(
                    "PostTag",
                    b =>
                        {
                            b.IndexerProperty<int>("TagId");
                            b.IndexerProperty<int>("PostId");
                        });

            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<Dictionary<string, int>>(
                    "PostTag",
                    j => j.HasOne<Tag>().WithMany(),
                    j => j.HasOne<Post>().WithMany());
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=OnModelCreating)]

<span data-ttu-id="ffc10-137">[變更外鍵和](xref:core/change-tracking/relationship-changes) 導覽會顯示如何藉由追蹤新的聯結實體實例來關聯兩個實體。</span><span class="sxs-lookup"><span data-stu-id="ffc10-137">[Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) shows how to associate two entities by tracking a new join entity instance.</span></span> <span data-ttu-id="ffc10-138">下列程式碼會針對 `Dictionary<string, int>` 用於聯結實體的共用類型實體類型執行此動作：</span><span class="sxs-lookup"><span data-stu-id="ffc10-138">The code below does this for the `Dictionary<string, int>` shared-type entity type used for the join entity:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            var joinEntitySet = context.Set<Dictionary<string, int>>("PostTag");
            var joinEntity = new Dictionary<string, int>
            {
                ["PostId"] = post.Id,
                ["TagId"] = tag.Id
            };
            joinEntitySet.Add(joinEntity);

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();
-->
[!code-csharp[DbContext_versus_DbSet_methods_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=DbContext_versus_DbSet_methods_1)]

<span data-ttu-id="ffc10-139">請注意，這 <xref:Microsoft.EntityFrameworkCore.DbContext.Set%60%601(System.String)?displayProperty=nameWithType> 是用來建立 `PostTag` 實體類型的 DbSet。</span><span class="sxs-lookup"><span data-stu-id="ffc10-139">Notice that <xref:Microsoft.EntityFrameworkCore.DbContext.Set%60%601(System.String)?displayProperty=nameWithType> is used to create a DbSet for the `PostTag` entity type.</span></span> <span data-ttu-id="ffc10-140">然後可以使用這個 DbSet，以 `Add` 新的聯結實體實例來呼叫。</span><span class="sxs-lookup"><span data-stu-id="ffc10-140">This DbSet can then be used to call `Add` with the new join entity instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ffc10-141">依慣例用於聯結實體類型的 CLR 型別，在未來的版本中可能會變更，以改善效能。</span><span class="sxs-lookup"><span data-stu-id="ffc10-141">The CLR type used for join entity types by convention may change in future releases to improve performance.</span></span> <span data-ttu-id="ffc10-142">除非已明確設定為在上述程式碼中完成，否則請勿相依于任何特定的聯結實體類型 `Dictionary<string, int>` 。</span><span class="sxs-lookup"><span data-stu-id="ffc10-142">Do not depend on any specific join entity type unless it has been explicitly configured as is done for `Dictionary<string, int>` in the code above.</span></span>

## <a name="property-versus-field-access"></a><span data-ttu-id="ffc10-143">屬性與欄位存取</span><span class="sxs-lookup"><span data-stu-id="ffc10-143">Property versus field access</span></span>

<span data-ttu-id="ffc10-144">從 EF Core 3.0 開始，實體屬性的存取預設會使用屬性的支援欄位。</span><span class="sxs-lookup"><span data-stu-id="ffc10-144">Starting with EF Core 3.0, access to entity properties uses the backing field of the property by default.</span></span> <span data-ttu-id="ffc10-145">這是有效率的，可避免觸發呼叫屬性 getter 和 setter 的副作用。</span><span class="sxs-lookup"><span data-stu-id="ffc10-145">This is efficient and avoids triggering side effects from calling property getters and setters.</span></span> <span data-ttu-id="ffc10-146">例如，這是消極式載入能夠避免觸發無限迴圈的方式。</span><span class="sxs-lookup"><span data-stu-id="ffc10-146">For example, this is how lazy-loading is able to avoid triggering infinite loops.</span></span> <span data-ttu-id="ffc10-147">如需有關在模型中設定支援欄位的詳細資訊，請參閱 [支援欄位](xref:core/modeling/backing-field) 。</span><span class="sxs-lookup"><span data-stu-id="ffc10-147">See [Backing Fields](xref:core/modeling/backing-field) for more information on configuring backing fields in the model.</span></span>

<span data-ttu-id="ffc10-148">有時候在修改屬性值時，EF Core 可能會產生副作用。</span><span class="sxs-lookup"><span data-stu-id="ffc10-148">Sometimes it may be desirable for EF Core to generate side-effects when it modifies property values.</span></span> <span data-ttu-id="ffc10-149">例如，將資料系結至實體時，設定屬性可能會產生通知至 U.I。</span><span class="sxs-lookup"><span data-stu-id="ffc10-149">For example, when data binding to entities, setting a property may generate notifications to the U.I.</span></span> <span data-ttu-id="ffc10-150">直接設定欄位時不會發生這種情況。</span><span class="sxs-lookup"><span data-stu-id="ffc10-150">which do not happen when setting the field directly.</span></span> <span data-ttu-id="ffc10-151">您可以藉由變更的來達成此目的 <xref:Microsoft.EntityFrameworkCore.PropertyAccessMode> ：</span><span class="sxs-lookup"><span data-stu-id="ffc10-151">This can be achieved by changing the <xref:Microsoft.EntityFrameworkCore.PropertyAccessMode> for:</span></span>

- <span data-ttu-id="ffc10-152">模型中的所有實體類型（使用 <xref:Microsoft.EntityFrameworkCore.ModelBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span><span class="sxs-lookup"><span data-stu-id="ffc10-152">All entity types in the model using <xref:Microsoft.EntityFrameworkCore.ModelBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span></span>
- <span data-ttu-id="ffc10-153">特定實體類型的所有屬性和導覽（使用 <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder%601.UsePropertyAccessMode%2A?displayProperty=nameWithType></span><span class="sxs-lookup"><span data-stu-id="ffc10-153">All properties and navigations of a specific entity type using <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder%601.UsePropertyAccessMode%2A?displayProperty=nameWithType></span></span>
- <span data-ttu-id="ffc10-154">使用的特定屬性 <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span><span class="sxs-lookup"><span data-stu-id="ffc10-154">A specific property using <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span></span>
- <span data-ttu-id="ffc10-155">使用的特定流覽 <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.NavigationBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span><span class="sxs-lookup"><span data-stu-id="ffc10-155">A specific navigation using <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.NavigationBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span></span>

<span data-ttu-id="ffc10-156">屬性存取模式 `Field` ， `PreferField` 會導致 EF Core 透過其支援欄位來存取屬性值。</span><span class="sxs-lookup"><span data-stu-id="ffc10-156">Property access modes `Field` and `PreferField` will cause EF Core to access the property value through its backing field.</span></span> <span data-ttu-id="ffc10-157">同樣 `Property` 地，和 `PreferProperty` 會導致 EF Core 透過其 getter 和 setter 存取屬性值。</span><span class="sxs-lookup"><span data-stu-id="ffc10-157">Likewise, `Property` and `PreferProperty` will cause EF Core to access the property value through its getter and setter.</span></span>

<span data-ttu-id="ffc10-158">如果 `Field` 使用或， `Property` 而且 EF Core 無法分別透過 field 或 property getter/setter 存取值，則 EF Core 會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ffc10-158">If `Field` or `Property` are used and EF Core cannot access the value through the field or property getter/setter respectively, then EF Core will throw an exception.</span></span> <span data-ttu-id="ffc10-159">這可確保當您認為 EF Core 一律使用欄位/屬性存取。</span><span class="sxs-lookup"><span data-stu-id="ffc10-159">This ensures EF Core is always using field/property access when you think it is.</span></span>

<span data-ttu-id="ffc10-160">另一方面， `PreferField` `PreferProperty` 如果無法使用慣用的存取，和模式將會切換回使用屬性或支援欄位。</span><span class="sxs-lookup"><span data-stu-id="ffc10-160">On the other hand, the `PreferField` and `PreferProperty` modes will fall back to using the property or backing field respectively if it is not possible to use the preferred access.</span></span> <span data-ttu-id="ffc10-161">`PreferField` 是 EF Core 3.0 的預設值。</span><span class="sxs-lookup"><span data-stu-id="ffc10-161">`PreferField` is the default from EF Core 3.0 onwards.</span></span> <span data-ttu-id="ffc10-162">這表示 EF Core 會在有欄位時使用欄位，但如果屬性必須透過 getter 或 setter 來存取，則不會失敗。</span><span class="sxs-lookup"><span data-stu-id="ffc10-162">This means EF Core will use fields whenever it can, but will not fail if a property must be accessed through its getter or setter instead.</span></span>

<span data-ttu-id="ffc10-163">`FieldDuringConstruction` 而且 `PreferFieldDuringConstruction` _只有在建立實體實例時，才_ 將 EF Core 設定為使用支援欄位。</span><span class="sxs-lookup"><span data-stu-id="ffc10-163">`FieldDuringConstruction` and `PreferFieldDuringConstruction` configure EF Core to use of backing fields _only when creating entity instances_.</span></span> <span data-ttu-id="ffc10-164">這可讓您在沒有 getter 和 setter 副作用的情況下執行查詢，而 EF Core 稍後的屬性變更將會造成這些副作用。</span><span class="sxs-lookup"><span data-stu-id="ffc10-164">This allows queries to be executed without getter and setter side effects, while later property changes by EF Core will cause these side effects.</span></span> <span data-ttu-id="ffc10-165">`PreferFieldDuringConstruction` 是 EF Core 3.0 之前的預設值。</span><span class="sxs-lookup"><span data-stu-id="ffc10-165">`PreferFieldDuringConstruction` was the default prior to EF Core 3.0.</span></span>

<span data-ttu-id="ffc10-166">下表摘要說明不同的屬性存取模式：</span><span class="sxs-lookup"><span data-stu-id="ffc10-166">The different property access modes are summarized in the following table:</span></span>

| <span data-ttu-id="ffc10-167">PropertyAccessMode</span><span class="sxs-lookup"><span data-stu-id="ffc10-167">PropertyAccessMode</span></span>              | <span data-ttu-id="ffc10-168">喜好設定</span><span class="sxs-lookup"><span data-stu-id="ffc10-168">Preference</span></span> | <span data-ttu-id="ffc10-169">建立實體的喜好設定</span><span class="sxs-lookup"><span data-stu-id="ffc10-169">Preference creating entities</span></span> | <span data-ttu-id="ffc10-170">後援</span><span class="sxs-lookup"><span data-stu-id="ffc10-170">Fallback</span></span> | <span data-ttu-id="ffc10-171">建立實體的 Fallback</span><span class="sxs-lookup"><span data-stu-id="ffc10-171">Fallback creating entities</span></span>
|:--------------------------------|------------|------------------------------|----------|---------------------------
| `Field`                         | <span data-ttu-id="ffc10-172">欄位</span><span class="sxs-lookup"><span data-stu-id="ffc10-172">Field</span></span>      | <span data-ttu-id="ffc10-173">欄位</span><span class="sxs-lookup"><span data-stu-id="ffc10-173">Field</span></span>                        | <span data-ttu-id="ffc10-174">擲回</span><span class="sxs-lookup"><span data-stu-id="ffc10-174">Throws</span></span>   | <span data-ttu-id="ffc10-175">擲回</span><span class="sxs-lookup"><span data-stu-id="ffc10-175">Throws</span></span>
| `Property`                      | <span data-ttu-id="ffc10-176">屬性</span><span class="sxs-lookup"><span data-stu-id="ffc10-176">Property</span></span>   | <span data-ttu-id="ffc10-177">屬性</span><span class="sxs-lookup"><span data-stu-id="ffc10-177">Property</span></span>                     | <span data-ttu-id="ffc10-178">擲回</span><span class="sxs-lookup"><span data-stu-id="ffc10-178">Throws</span></span>   | <span data-ttu-id="ffc10-179">擲回</span><span class="sxs-lookup"><span data-stu-id="ffc10-179">Throws</span></span>
| `PreferField`                   | <span data-ttu-id="ffc10-180">欄位</span><span class="sxs-lookup"><span data-stu-id="ffc10-180">Field</span></span>      | <span data-ttu-id="ffc10-181">欄位</span><span class="sxs-lookup"><span data-stu-id="ffc10-181">Field</span></span>                        | <span data-ttu-id="ffc10-182">屬性</span><span class="sxs-lookup"><span data-stu-id="ffc10-182">Property</span></span> | <span data-ttu-id="ffc10-183">屬性</span><span class="sxs-lookup"><span data-stu-id="ffc10-183">Property</span></span>
| `PreferProperty`                | <span data-ttu-id="ffc10-184">屬性</span><span class="sxs-lookup"><span data-stu-id="ffc10-184">Property</span></span>   | <span data-ttu-id="ffc10-185">屬性</span><span class="sxs-lookup"><span data-stu-id="ffc10-185">Property</span></span>                     | <span data-ttu-id="ffc10-186">欄位</span><span class="sxs-lookup"><span data-stu-id="ffc10-186">Field</span></span>    | <span data-ttu-id="ffc10-187">欄位</span><span class="sxs-lookup"><span data-stu-id="ffc10-187">Field</span></span>
| `FieldDuringConstruction`       | <span data-ttu-id="ffc10-188">屬性</span><span class="sxs-lookup"><span data-stu-id="ffc10-188">Property</span></span>   | <span data-ttu-id="ffc10-189">欄位</span><span class="sxs-lookup"><span data-stu-id="ffc10-189">Field</span></span>                        | <span data-ttu-id="ffc10-190">欄位</span><span class="sxs-lookup"><span data-stu-id="ffc10-190">Field</span></span>    | <span data-ttu-id="ffc10-191">擲回</span><span class="sxs-lookup"><span data-stu-id="ffc10-191">Throws</span></span>
| `PreferFieldDuringConstruction` | <span data-ttu-id="ffc10-192">屬性</span><span class="sxs-lookup"><span data-stu-id="ffc10-192">Property</span></span>   | <span data-ttu-id="ffc10-193">欄位</span><span class="sxs-lookup"><span data-stu-id="ffc10-193">Field</span></span>                        | <span data-ttu-id="ffc10-194">欄位</span><span class="sxs-lookup"><span data-stu-id="ffc10-194">Field</span></span>    | <span data-ttu-id="ffc10-195">屬性</span><span class="sxs-lookup"><span data-stu-id="ffc10-195">Property</span></span>

## <a name="temporary-values"></a><span data-ttu-id="ffc10-196">暫存值</span><span class="sxs-lookup"><span data-stu-id="ffc10-196">Temporary values</span></span>

<span data-ttu-id="ffc10-197">當呼叫 SaveChanges 時，當您追蹤將擁有資料庫所產生之實際索引鍵值的新實體時，EF Core 會建立暫存索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="ffc10-197">EF Core creates temporary key values when tracking new entities that will have real key values generated by the database when SaveChanges is called.</span></span> <span data-ttu-id="ffc10-198">如需如何使用這些暫存值的總覽，請參閱 [EF Core 中的變更追蹤](xref:core/change-tracking/index) 。</span><span class="sxs-lookup"><span data-stu-id="ffc10-198">See [Change Tracking in EF Core](xref:core/change-tracking/index) for an overview of how these temporary values are used.</span></span>

### <a name="accessing-temporary-values"></a><span data-ttu-id="ffc10-199">存取臨時值</span><span class="sxs-lookup"><span data-stu-id="ffc10-199">Accessing temporary values</span></span>

<span data-ttu-id="ffc10-200">從 EF Core 3.0 開始，暫存值會儲存在變更追蹤器中，而不會直接設定在實體實例上。</span><span class="sxs-lookup"><span data-stu-id="ffc10-200">Starting with EF Core 3.0, temporary values are stored in the change tracker and not set onto entity instances directly.</span></span> <span data-ttu-id="ffc10-201">不過，使用各種機制來 [存取追蹤的實體](xref:core/change-tracking/entity-entries)時，_會_ 公開這些暫存值。</span><span class="sxs-lookup"><span data-stu-id="ffc10-201">However, these temporary values _are_ exposed when using the various mechanisms for [Accessing Tracked Entities](xref:core/change-tracking/entity-entries).</span></span> <span data-ttu-id="ffc10-202">例如，下列程式碼會使用下列程式碼來存取暫存值 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.CurrentValues%2A?displayProperty=nameWithType> ：</span><span class="sxs-lookup"><span data-stu-id="ffc10-202">For example, the following code accesses a temporary value using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.CurrentValues%2A?displayProperty=nameWithType>:</span></span>

<!--
        using var context = new BlogsContext();

        var blog = new Blog { Name = ".NET Blog" };

        context.Add(blog);

        Console.WriteLine($"Blog.Id set on entity is {blog.Id}");
        Console.WriteLine($"Blog.Id tracked by EF is {context.Entry(blog).Property(e => e.Id).CurrentValue}");
-->
[!code-csharp[Temporary_values_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=Temporary_values_1)]

<span data-ttu-id="ffc10-203">此程式碼的輸出為：</span><span class="sxs-lookup"><span data-stu-id="ffc10-203">The output from this code is:</span></span>

```output
Blog.Id set on entity is 0
Blog.Id tracked by EF is -2147482643
```

<span data-ttu-id="ffc10-204"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A?displayProperty=nameWithType> 可以用來檢查臨時值。</span><span class="sxs-lookup"><span data-stu-id="ffc10-204"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A?displayProperty=nameWithType> can be used to check for temporary values.</span></span>

### <a name="manipulating-temporary-values"></a><span data-ttu-id="ffc10-205">操作暫存值</span><span class="sxs-lookup"><span data-stu-id="ffc10-205">Manipulating temporary values</span></span>

<span data-ttu-id="ffc10-206">明確地使用暫存值有時會很有用。</span><span class="sxs-lookup"><span data-stu-id="ffc10-206">It is sometimes useful to explicitly work with temporary values.</span></span> <span data-ttu-id="ffc10-207">例如，可能會在 web 用戶端上建立新實體的集合，然後再序列化回伺服器。</span><span class="sxs-lookup"><span data-stu-id="ffc10-207">For example, a collection of new entities might be created on a web client and then serialized back to the server.</span></span> <span data-ttu-id="ffc10-208">外鍵值是設定這些實體之間關聯性的一種方式。</span><span class="sxs-lookup"><span data-stu-id="ffc10-208">Foreign key values are one way to set up relationships between these entities.</span></span> <span data-ttu-id="ffc10-209">下列程式碼會使用這種方法，將新實體的圖形關聯到外鍵，同時仍然允許在呼叫 SaveChanges 時產生實際的索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="ffc10-209">The following code uses this approach to associate a graph of new entities by foreign key, while still allowing real key values to be generated when SaveChanges is called.</span></span>

<!--
            var blogs = new List<Blog>
            {
                new Blog { Id = -1, Name = ".NET Blog" },
                new Blog { Id = -2, Name = "Visual Studio Blog" }
            };

            var posts = new List<Post>()
            {
                new Post
                {
                    Id = -1,
                    BlogId = -1,
                    Title = "Announcing the Release of EF Core 5.0",
                    Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                },
                new Post
                {
                    Id = -2,
                    BlogId = -2,
                    Title = "Disassembly improvements for optimized managed debugging",
                    Content = "If you are focused on squeezing out the last bits of performance for your .NET service or..."
                }
            };

            using var context = new BlogsContext();

            foreach (var blog in blogs)
            {
                context.Add(blog).Property(e => e.Id).IsTemporary = true;
            }

            foreach (var post in posts)
            {
                context.Add(post).Property(e => e.Id).IsTemporary = true;
            }

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Temporary_values_2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=Temporary_values_2)]

<span data-ttu-id="ffc10-210">請注意：</span><span class="sxs-lookup"><span data-stu-id="ffc10-210">Notice that:</span></span>

- <span data-ttu-id="ffc10-211">負數會用來做為暫存索引鍵值;這並不是必要的，但這是防止金鑰衝突的常見慣例。</span><span class="sxs-lookup"><span data-stu-id="ffc10-211">Negative numbers are used as temporary key values; this is not required, but is a common convention to prevent key clashes.</span></span>
- <span data-ttu-id="ffc10-212">`Post.BlogId`FK 屬性會被指派與相關聯的 blog 的 PK 相同的負數值。</span><span class="sxs-lookup"><span data-stu-id="ffc10-212">The `Post.BlogId` FK property is assigned the same negative value as the PK of the associated blog.</span></span>
- <span data-ttu-id="ffc10-213">在 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A> 追蹤每個實體之後，會將 PK 值標示為暫時性。</span><span class="sxs-lookup"><span data-stu-id="ffc10-213">The PK values are marked as temporary by setting <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A> after each entity is tracked.</span></span> <span data-ttu-id="ffc10-214">這是必要的，因為應用程式所提供的任何索引鍵值都會假設為真正的索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="ffc10-214">This is necessary because any key value supplied by the application is assumed to be a real key value.</span></span>

<span data-ttu-id="ffc10-215">在呼叫 SaveChanges 之前查看 [變更追蹤器調試](xref:core/change-tracking/debug-views) 程式，會顯示 PK 值標示為暫時，而貼文會與正確的 blog 相關聯，包括瀏覽器的修正：</span><span class="sxs-lookup"><span data-stu-id="ffc10-215">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) before calling SaveChanges shows that the PK values are marked as temporary and posts are associated with the correct blogs, including fixup of navigations:</span></span>

```output
Blog {Id: -2} Added
  Id: -2 PK Temporary
  Name: 'Visual Studio Blog'
  Posts: [{Id: -2}]
Blog {Id: -1} Added
  Id: -1 PK Temporary
  Name: '.NET Blog'
  Posts: [{Id: -1}]
Post {Id: -2} Added
  Id: -2 PK Temporary
  BlogId: -2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: -2}
  Tags: []
Post {Id: -1} Added
  Id: -1 PK Temporary
  BlogId: -1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: -1}
```

<span data-ttu-id="ffc10-216">在呼叫之後 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> ，這些暫存值已由資料庫產生的實際值取代：</span><span class="sxs-lookup"><span data-stu-id="ffc10-216">After calling <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>, these temporary values have been replaced by real values generated by the database:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Posts: [{Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
```

## <a name="working-with-default-values"></a><span data-ttu-id="ffc10-217">使用預設值</span><span class="sxs-lookup"><span data-stu-id="ffc10-217">Working with default values</span></span>

<span data-ttu-id="ffc10-218">當呼叫時，EF Core 允許屬性從資料庫取得其預設值 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 。</span><span class="sxs-lookup"><span data-stu-id="ffc10-218">EF Core allows a property to get its default value from the database when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span> <span data-ttu-id="ffc10-219">如同產生的索引鍵值，EF Core 只有在未明確設定任何值的情況下，才會使用資料庫中的預設值。</span><span class="sxs-lookup"><span data-stu-id="ffc10-219">Like with generated key values, EF Core will only use a default from the database if no value has been explicitly set.</span></span> <span data-ttu-id="ffc10-220">例如，請考慮下列實體類型：</span><span class="sxs-lookup"><span data-stu-id="ffc10-220">For example, consider the following entity type:</span></span>

<!--
    public class Token
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public DateTime ValidFrom { get; set; }
    }
-->
[!code-csharp[Token](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Token)]

<span data-ttu-id="ffc10-221">`ValidFrom`屬性設定為從資料庫取得預設值：</span><span class="sxs-lookup"><span data-stu-id="ffc10-221">The `ValidFrom` property is configured to get a default value from the database:</span></span>

<!--
        modelBuilder
            .Entity<Token>()
            .Property(e => e.ValidFrom)
            .HasDefaultValueSql("CURRENT_TIMESTAMP");
-->
[!code-csharp[OnModelCreating_Token](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Token)]

<span data-ttu-id="ffc10-222">當您插入此類型的實體時，除非改為設定明確的值，否則 EF Core 會讓資料庫產生該值。</span><span class="sxs-lookup"><span data-stu-id="ffc10-222">When inserting an entity of this type, EF Core will let the database generate the value unless an explicit value has been set instead.</span></span> <span data-ttu-id="ffc10-223">例如：</span><span class="sxs-lookup"><span data-stu-id="ffc10-223">For example:</span></span>

<!--
            using var context = new BlogsContext();

            context.AddRange(
                new Token { Name = "A" },
                new Token { Name = "B", ValidFrom = new DateTime(1111, 11, 11, 11, 11, 11)});

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Working_with_default_values_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_1)]

<span data-ttu-id="ffc10-224">查看 [ [變更追蹤](xref:core/change-tracking/debug-views) 器] 偵錯工具，會顯示資料庫所產生的第一個 token `ValidFrom` ，而第二個權杖則使用明確設定的值：</span><span class="sxs-lookup"><span data-stu-id="ffc10-224">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) shows that the first token had `ValidFrom` generated by the database, while the second token used the value explicitly set:</span></span>

```output
Token {Id: 1} Unchanged
  Id: 1 PK
  Name: 'A'
  ValidFrom: '12/30/2020 6:36:06 PM'
Token {Id: 2} Unchanged
  Id: 2 PK
  Name: 'B'
  ValidFrom: '11/11/1111 11:11:11 AM'
```

> [!NOTE]
> <span data-ttu-id="ffc10-225">若要使用資料庫預設值，資料庫資料行必須已設定預設值條件約束。</span><span class="sxs-lookup"><span data-stu-id="ffc10-225">Using database default values requires that the database column has a default value constraint configured.</span></span> <span data-ttu-id="ffc10-226">這是在使用或時 EF Core 遷移自動 <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValueSql%2A> 完成 <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValue%2A> 。</span><span class="sxs-lookup"><span data-stu-id="ffc10-226">This is done automatically by EF Core migrations when using <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValueSql%2A> or <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValue%2A>.</span></span> <span data-ttu-id="ffc10-227">請勿使用 EF Core 的遷移時，請務必以其他方式在資料行上建立預設條件約束。</span><span class="sxs-lookup"><span data-stu-id="ffc10-227">Make sure to create the default constraint on the column in some other way when not using EF Core migrations.</span></span>

### <a name="using-nullable-properties"></a><span data-ttu-id="ffc10-228">使用可為 null 的屬性</span><span class="sxs-lookup"><span data-stu-id="ffc10-228">Using nullable properties</span></span>

<span data-ttu-id="ffc10-229">EF Core 可以藉由比較屬性值與該類型的 CLR 預設值，判斷是否已設定屬性。</span><span class="sxs-lookup"><span data-stu-id="ffc10-229">EF Core is able to determine whether or not a property has been set by comparing the property value to the CLR default for the that type.</span></span> <span data-ttu-id="ffc10-230">這在大多數情況下都能順利運作，但表示 CLR 預設值無法明確地插入資料庫中。</span><span class="sxs-lookup"><span data-stu-id="ffc10-230">This works well in most cases, but means that the CLR default cannot be explicitly inserted into the database.</span></span> <span data-ttu-id="ffc10-231">例如，假設有一個具有整數屬性的實體：</span><span class="sxs-lookup"><span data-stu-id="ffc10-231">For example, consider an entity with an integer property:</span></span>

<!--
public class Foo1
{
    public int Id { get; set; }
    public int Count { get; set; }
}
-->
[!code-csharp[Foo1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo1)]

<span data-ttu-id="ffc10-232">該屬性設定為具有-1 的資料庫預設值：</span><span class="sxs-lookup"><span data-stu-id="ffc10-232">Where that property is configured to have a database default of -1:</span></span>

<!--
        modelBuilder
            .Entity<Foo1>()
            .Property(e => e.Count)
            .HasDefaultValue(-1);
-->
[!code-csharp[OnModelCreating_Foo1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Foo1)]

<span data-ttu-id="ffc10-233">其目的是只要未設定明確的值，就會使用預設值-1。</span><span class="sxs-lookup"><span data-stu-id="ffc10-233">The intention is that the default of -1 will be used whenever an explicit value is not set.</span></span> <span data-ttu-id="ffc10-234">但是，將值設定為 0 () 的 CLR 預設值，無法區分 EF Core 不會設定任何值，這表示不可能為這個屬性插入0。</span><span class="sxs-lookup"><span data-stu-id="ffc10-234">However, setting the value to 0 (the CLR default for integers) is indistinguishable to EF Core from not setting any value, this means that it is not possible to insert 0 for this property.</span></span> <span data-ttu-id="ffc10-235">例如：</span><span class="sxs-lookup"><span data-stu-id="ffc10-235">For example:</span></span>

<!--
        using var context = new BlogsContext();

        var fooA = new Foo1 { Count = 10 };
        var fooB = new Foo1 { Count = 0 };
        var fooC = new Foo1 { };

        context.AddRange(fooA, fooB, fooC);
        context.SaveChanges();

        Debug.Assert(fooA.Count == 10);
        Debug.Assert(fooB.Count == -1); // Not what we want!
        Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_2)]

<span data-ttu-id="ffc10-236">請注意， `Count` 已明確設定為0的實例仍會從資料庫中取得預設值，這不是我們想要的值。</span><span class="sxs-lookup"><span data-stu-id="ffc10-236">Notice that the instance where `Count` was explicitly set to 0 is still gets the default value from the database, which is not what we intended.</span></span> <span data-ttu-id="ffc10-237">處理此情況的簡單方法是讓 `Count` 屬性變成可為 null：</span><span class="sxs-lookup"><span data-stu-id="ffc10-237">An easy way to deal with this is to make the `Count` property nullable:</span></span>

<!--
public class Foo2
{
    public int Id { get; set; }
    public int? Count { get; set; }
}
-->
[!code-csharp[Foo2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo2)]

<span data-ttu-id="ffc10-238">這會使 CLR 預設為 null，而不是0，這表示現在會在明確設定時插入0：</span><span class="sxs-lookup"><span data-stu-id="ffc10-238">This makes the CLR default null, instead of 0, which means 0 will now be inserted when explicitly set:</span></span>

<!--
        using var context = new BlogsContext();

        var fooA = new Foo2 { Count = 10 };
        var fooB = new Foo2 { Count = 0 };
        var fooC = new Foo2 { };

        context.AddRange(fooA, fooB, fooC);
        context.SaveChanges();

        Debug.Assert(fooA.Count == 10);
        Debug.Assert(fooB.Count == 0);
        Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_3](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_3)]

### <a name="using-nullable-backing-fields"></a><span data-ttu-id="ffc10-239">使用可為 null 的支援欄位</span><span class="sxs-lookup"><span data-stu-id="ffc10-239">Using nullable backing fields</span></span>

> [!NOTE]
> <span data-ttu-id="ffc10-240">EF Core 5.0 和更新版本支援這個可為 null 的支援欄位模式。</span><span class="sxs-lookup"><span data-stu-id="ffc10-240">This nullable backing field pattern is supported by EF Core 5.0 and later.</span></span>

<span data-ttu-id="ffc10-241">讓屬性成為可為 null 的問題，在領域模型中可能會變成可為 null。</span><span class="sxs-lookup"><span data-stu-id="ffc10-241">The problem with making the property nullable that it may not be conceptually nullable in the domain model.</span></span> <span data-ttu-id="ffc10-242">強制屬性可為 null，因此會危及模型。</span><span class="sxs-lookup"><span data-stu-id="ffc10-242">Forcing the property to be nullable therefore compromises the model.</span></span>

<span data-ttu-id="ffc10-243">從 EF Core 5.0 開始，此屬性可保留為不可為 null，而且只有支援欄位可為 null。</span><span class="sxs-lookup"><span data-stu-id="ffc10-243">Starting with EF Core 5.0, the property can be left non-nullable, with only the backing field being nullable.</span></span> <span data-ttu-id="ffc10-244">例如：</span><span class="sxs-lookup"><span data-stu-id="ffc10-244">For example:</span></span>

<!--
public class Foo3
{
    public int Id { get; set; }

    private int? _count;
    public int Count
    {
        get => _count ?? -1;
        set => _count = value;
    }
}
-->
[!code-csharp[Foo3](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo3)]

<span data-ttu-id="ffc10-245">如果將屬性明確設定為0，就可以插入 CLR 預設 (0) ，而不需要將屬性公開為網域模型中的可為 null。</span><span class="sxs-lookup"><span data-stu-id="ffc10-245">This allows the CLR default (0) to be inserted if the property is explicitly set to 0, while not needing to expose the property as nullable in the domain model.</span></span> <span data-ttu-id="ffc10-246">例如：</span><span class="sxs-lookup"><span data-stu-id="ffc10-246">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var fooA = new Foo3 { Count = 10 };
            var fooB = new Foo3 { Count = 0 };
            var fooC = new Foo3 { };

            context.AddRange(fooA, fooB, fooC);
            context.SaveChanges();

            Debug.Assert(fooA.Count == 10);
            Debug.Assert(fooB.Count == 0);
            Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_4](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_4)]

#### <a name="nullable-backing-fields-for-bool-properties"></a><span data-ttu-id="ffc10-247">Bool 屬性可為 null 的支援欄位</span><span class="sxs-lookup"><span data-stu-id="ffc10-247">Nullable backing fields for bool properties</span></span>

<span data-ttu-id="ffc10-248">當搭配使用 bool 屬性與存放區產生的預設值時，此模式特別有用。</span><span class="sxs-lookup"><span data-stu-id="ffc10-248">This pattern is especially useful when using bool properties with store-generated defaults.</span></span> <span data-ttu-id="ffc10-249">因為的 CLR 預設值 `bool` 為 "false"，表示無法使用一般模式明確插入 "false"。</span><span class="sxs-lookup"><span data-stu-id="ffc10-249">Since the CLR default for `bool` is "false", it means that "false" cannot be inserted explicitly using the normal pattern.</span></span> <span data-ttu-id="ffc10-250">例如，請考慮使用 `User` 實體類型：</span><span class="sxs-lookup"><span data-stu-id="ffc10-250">For example, consider a `User` entity type:</span></span>

<!--
public class User
{
    public int Id { get; set; }
    public string Name { get; set; }

    private bool? _isAuthorized;
    public bool IsAuthorized
    {
        get => _isAuthorized ?? true;
        set => _isAuthorized = value;
    }
}
-->
[!code-csharp[User](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=User)]

<span data-ttu-id="ffc10-251">`IsAuthorized`屬性的設定方式是將資料庫預設值設為 "true"：</span><span class="sxs-lookup"><span data-stu-id="ffc10-251">The `IsAuthorized` property is configured with a database default value of "true":</span></span>

<!--
        modelBuilder
            .Entity<User>()
            .Property(e => e.IsAuthorized)
            .HasDefaultValue(true);
-->
[!code-csharp[OnModelCreating_User](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_User)]

<span data-ttu-id="ffc10-252">在 `IsAuthorized` 插入之前，屬性可以明確地設定為 "true" 或 "false"，或者可以保留為未設定，在此情況下會使用資料庫預設值：</span><span class="sxs-lookup"><span data-stu-id="ffc10-252">The `IsAuthorized` property can be set to "true" or "false" explicitly before inserting, or can be left unset in which case the database default will be used:</span></span>

<!--
        using var context = new BlogsContext();

        var userA = new User { Name = "Mac" };
        var userB = new User { Name = "Alice", IsAuthorized = true };
        var userC = new User { Name = "Baxter", IsAuthorized = false }; // Always deny Baxter access!

        context.AddRange(userA, userB, userC);

        context.SaveChanges();
-->
[!code-csharp[Working_with_default_values_5](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_5)]

<span data-ttu-id="ffc10-253">使用 SQLite 時，SaveChanges 的輸出會顯示資料庫預設值用於 Mac，而明確值則是針對 Alice 和 Baxter 而設定：</span><span class="sxs-lookup"><span data-stu-id="ffc10-253">The output from SaveChanges when using SQLite shows that the database default is used for Mac, while explicit values are set for Alice and Baxter:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='Mac' (Size = 3)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("Name")
VALUES (@p0);
SELECT "Id", "IsAuthorized"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='True' (DbType = String), @p1='Alice' (Size = 5)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("IsAuthorized", "Name")
VALUES (@p0, @p1);
SELECT "Id"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='False' (DbType = String), @p1='Baxter' (Size = 6)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("IsAuthorized", "Name")
VALUES (@p0, @p1);
SELECT "Id"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

### <a name="schema-defaults-only"></a><span data-ttu-id="ffc10-254">僅限架構預設值</span><span class="sxs-lookup"><span data-stu-id="ffc10-254">Schema defaults only</span></span>

<span data-ttu-id="ffc10-255">有時候，在 EF Core 遷移所建立的資料庫架構中使用預設值，而不 EF Core 使用這些值來進行插入，會很有説明。</span><span class="sxs-lookup"><span data-stu-id="ffc10-255">Sometimes it is useful to have defaults in the database schema created by EF Core migrations without EF Core ever using these values for inserts.</span></span> <span data-ttu-id="ffc10-256">您可以藉由設定屬性來達成此目的， <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.ValueGeneratedNever%2A?displayProperty=nameWithType> 例如：</span><span class="sxs-lookup"><span data-stu-id="ffc10-256">This can be achieved by configuring the property as <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.ValueGeneratedNever%2A?displayProperty=nameWithType> For example:</span></span>

<!--
        modelBuilder
            .Entity<Bar>()
            .Property(e => e.Count)
            .HasDefaultValue(-1)
            .ValueGeneratedNever();
-->
[!code-csharp[OnModelCreating_Bar](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Bar)]
