---
title: EF Core 5.0-EF Core 的重大變更
description: Entity Framework Core 5.0 中引進的重大變更完整清單
author: bricelam
ms.date: 11/07/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 3a00c2fdce51ff8b7d76b34f7003dc8ec0618830
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429282"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="2a28b-103">EF Core 5.0 中的重大變更</span><span class="sxs-lookup"><span data-stu-id="2a28b-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="2a28b-104">下列 API 和行為變更可能會中斷現有應用程式更新為 EF Core 5.0.0。</span><span class="sxs-lookup"><span data-stu-id="2a28b-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="2a28b-105">總結</span><span class="sxs-lookup"><span data-stu-id="2a28b-105">Summary</span></span>

| <span data-ttu-id="2a28b-106">**重大變更**</span><span class="sxs-lookup"><span data-stu-id="2a28b-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="2a28b-107">**影響**</span><span class="sxs-lookup"><span data-stu-id="2a28b-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="2a28b-108">從主體到相依的導覽上的必要項具有不同的語義</span><span class="sxs-lookup"><span data-stu-id="2a28b-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="2a28b-109">中型</span><span class="sxs-lookup"><span data-stu-id="2a28b-109">Medium</span></span>     |
| [<span data-ttu-id="2a28b-110">定義查詢會取代為提供者特定的方法</span><span class="sxs-lookup"><span data-stu-id="2a28b-110">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="2a28b-111">中型</span><span class="sxs-lookup"><span data-stu-id="2a28b-111">Medium</span></span>     |
| [<span data-ttu-id="2a28b-112">查詢不會覆寫非 null 的參考導覽</span><span class="sxs-lookup"><span data-stu-id="2a28b-112">Non-null reference navigations are not overwritten by queries</span></span>](#nonnullreferences)                                                   | <span data-ttu-id="2a28b-113">中型</span><span class="sxs-lookup"><span data-stu-id="2a28b-113">Medium</span></span>     |
| [<span data-ttu-id="2a28b-114">已從 SQLite NTS 擴充功能移除 HasGeometricDimension 方法</span><span class="sxs-lookup"><span data-stu-id="2a28b-114">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="2a28b-115">低</span><span class="sxs-lookup"><span data-stu-id="2a28b-115">Low</span></span>        |
| [<span data-ttu-id="2a28b-116">Cosmos：現在已將資料分割索引鍵新增至主要索引鍵</span><span class="sxs-lookup"><span data-stu-id="2a28b-116">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="2a28b-117">低</span><span class="sxs-lookup"><span data-stu-id="2a28b-117">Low</span></span>        |
| [<span data-ttu-id="2a28b-118">Cosmos： `id` 屬性已重新命名為 `__id`</span><span class="sxs-lookup"><span data-stu-id="2a28b-118">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="2a28b-119">低</span><span class="sxs-lookup"><span data-stu-id="2a28b-119">Low</span></span>        |
| <span data-ttu-id="2a28b-120">[Cosmos： byte [] 現在會儲存為 base64 字串，而不是數位陣列](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="2a28b-120">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="2a28b-121">低</span><span class="sxs-lookup"><span data-stu-id="2a28b-121">Low</span></span>        |
| [<span data-ttu-id="2a28b-122">Cosmos： GetPropertyName 和 SetPropertyName 已重新命名</span><span class="sxs-lookup"><span data-stu-id="2a28b-122">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="2a28b-123">低</span><span class="sxs-lookup"><span data-stu-id="2a28b-123">Low</span></span>        |
| [<span data-ttu-id="2a28b-124">當實體狀態從卸離變更為未變更、更新或刪除時，會呼叫值產生器</span><span class="sxs-lookup"><span data-stu-id="2a28b-124">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="2a28b-125">低</span><span class="sxs-lookup"><span data-stu-id="2a28b-125">Low</span></span>        |
| [<span data-ttu-id="2a28b-126">IMigrationsModelDiffer 現在使用 IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="2a28b-126">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="2a28b-127">低</span><span class="sxs-lookup"><span data-stu-id="2a28b-127">Low</span></span>        |
| [<span data-ttu-id="2a28b-128">鑒別子是唯讀的</span><span class="sxs-lookup"><span data-stu-id="2a28b-128">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="2a28b-129">低</span><span class="sxs-lookup"><span data-stu-id="2a28b-129">Low</span></span>        |
| [<span data-ttu-id="2a28b-130">提供者特定的 EF。InMemory 提供者的函數方法擲回</span><span class="sxs-lookup"><span data-stu-id="2a28b-130">Provider-specific EF.Functions methods throw for InMemory provider</span></span>](#no-client-methods)                                              | <span data-ttu-id="2a28b-131">低</span><span class="sxs-lookup"><span data-stu-id="2a28b-131">Low</span></span>        |
| [<span data-ttu-id="2a28b-132">IndexBuilder. HasName 現已淘汰</span><span class="sxs-lookup"><span data-stu-id="2a28b-132">IndexBuilder.HasName is now obsolete</span></span>](#index-obsolete)                                                                               | <span data-ttu-id="2a28b-133">低</span><span class="sxs-lookup"><span data-stu-id="2a28b-133">Low</span></span>        |
| [<span data-ttu-id="2a28b-134">Pluarlizer 現已包含給樣板反轉工程模型</span><span class="sxs-lookup"><span data-stu-id="2a28b-134">A pluarlizer is now included for scaffolding reverse engineered models</span></span>](#pluralizer)                                                 | <span data-ttu-id="2a28b-135">低</span><span class="sxs-lookup"><span data-stu-id="2a28b-135">Low</span></span>        |
| [<span data-ttu-id="2a28b-136">INavigationBase 取代某些 Api 中的 INavigation，以支援略過導覽</span><span class="sxs-lookup"><span data-stu-id="2a28b-136">INavigationBase replaces INavigation in some APIs to support skip navigations</span></span>](#inavigationbase)                                     | <span data-ttu-id="2a28b-137">低</span><span class="sxs-lookup"><span data-stu-id="2a28b-137">Low</span></span>        |
| [<span data-ttu-id="2a28b-138">某些使用 `Distinct` 或已不再支援之相互關聯集合的查詢 `GroupBy`</span><span class="sxs-lookup"><span data-stu-id="2a28b-138">Some queries with correlated collection that also use `Distinct` or `GroupBy` are no longer supported</span></span>](#collection-distinct-groupby) | <span data-ttu-id="2a28b-139">低</span><span class="sxs-lookup"><span data-stu-id="2a28b-139">Low</span></span>        |
| [<span data-ttu-id="2a28b-140">不支援在投射中使用可查詢型別的集合</span><span class="sxs-lookup"><span data-stu-id="2a28b-140">Using a collection of Queryable type in projection is not supported</span></span>](#queryable-projection)                                          | <span data-ttu-id="2a28b-141">低</span><span class="sxs-lookup"><span data-stu-id="2a28b-141">Low</span></span>        |

## <a name="medium-impact-changes"></a><span data-ttu-id="2a28b-142">中度影響變更</span><span class="sxs-lookup"><span data-stu-id="2a28b-142">Medium-impact changes</span></span>

<a name="required-dependent"></a>

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="2a28b-143">從主體到相依的導覽上的必要項具有不同的語義</span><span class="sxs-lookup"><span data-stu-id="2a28b-143">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="2a28b-144">追蹤問題 #17286</span><span class="sxs-lookup"><span data-stu-id="2a28b-144">Tracking Issue #17286</span></span>](https://github.com/dotnet/efcore/issues/17286)

#### <a name="old-behavior"></a><span data-ttu-id="2a28b-145">舊的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-145">Old behavior</span></span>

<span data-ttu-id="2a28b-146">只有導覽至主體可設定為必要。</span><span class="sxs-lookup"><span data-stu-id="2a28b-146">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="2a28b-147">因此， `RequiredAttribute` 在相依 (的實體上使用，包含外鍵的實體) 會改為在定義實體類型上建立外鍵。</span><span class="sxs-lookup"><span data-stu-id="2a28b-147">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2a28b-148">新的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-148">New behavior</span></span>

<span data-ttu-id="2a28b-149">由於已新增對必要相依性的支援，現在可以視需要將任何參考導覽標記，這表示在上方顯示的情況下，外鍵將會定義于關聯性的另一端，而且屬性不會標示為必要項。</span><span class="sxs-lookup"><span data-stu-id="2a28b-149">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="2a28b-150">`IsRequired`在指定相依端點之前呼叫，現在是不明確的：</span><span class="sxs-lookup"><span data-stu-id="2a28b-150">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

#### <a name="why"></a><span data-ttu-id="2a28b-151">原因</span><span class="sxs-lookup"><span data-stu-id="2a28b-151">Why</span></span>

<span data-ttu-id="2a28b-152">若要啟用必要相依性的支援，必須要有新的行為 ([請參閱 #12100](https://github.com/dotnet/efcore/issues/12100)) 。</span><span class="sxs-lookup"><span data-stu-id="2a28b-152">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2a28b-153">風險降低</span><span class="sxs-lookup"><span data-stu-id="2a28b-153">Mitigations</span></span>

<span data-ttu-id="2a28b-154">`RequiredAttribute`從導覽中移除相依性，並將其放在主體的導覽上，或在中設定關聯性 `OnModelCreating` ：</span><span class="sxs-lookup"><span data-stu-id="2a28b-154">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="2a28b-155">定義查詢會取代為提供者特定的方法</span><span class="sxs-lookup"><span data-stu-id="2a28b-155">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="2a28b-156">追蹤問題 #18903</span><span class="sxs-lookup"><span data-stu-id="2a28b-156">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

#### <a name="old-behavior"></a><span data-ttu-id="2a28b-157">舊的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-157">Old behavior</span></span>

<span data-ttu-id="2a28b-158">實體類型已對應至在核心層級定義查詢。</span><span class="sxs-lookup"><span data-stu-id="2a28b-158">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="2a28b-159">任何時候，實體類型的查詢根目錄中使用的實體類型已由任何提供者的定義查詢所取代。</span><span class="sxs-lookup"><span data-stu-id="2a28b-159">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2a28b-160">新的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-160">New behavior</span></span>

<span data-ttu-id="2a28b-161">用於定義查詢的 Api 已被取代。</span><span class="sxs-lookup"><span data-stu-id="2a28b-161">APIs for defining query are deprecated.</span></span> <span data-ttu-id="2a28b-162">引進了新的提供者特定 Api。</span><span class="sxs-lookup"><span data-stu-id="2a28b-162">New provider-specific APIs were introduced.</span></span>

#### <a name="why"></a><span data-ttu-id="2a28b-163">原因</span><span class="sxs-lookup"><span data-stu-id="2a28b-163">Why</span></span>

<span data-ttu-id="2a28b-164">當查詢中使用查詢根時，定義查詢會實作為取代查詢，但有幾個問題：</span><span class="sxs-lookup"><span data-stu-id="2a28b-164">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="2a28b-165">如果定義查詢是使用 in 方法投射實體型別 `new { ... }` `Select` ，則將其識別為實體需要額外的工作，並使其與 EF Core 在查詢中處理名義類型的方式不一致。</span><span class="sxs-lookup"><span data-stu-id="2a28b-165">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="2a28b-166">針對關聯式提供者， `FromSql` 仍需要以 LINQ 運算式形式傳遞 SQL 字串。</span><span class="sxs-lookup"><span data-stu-id="2a28b-166">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="2a28b-167">最初定義查詢時，是以用戶端視圖的形式導入，以用於無索引鍵實體的 In-Memory 提供者 (類似于關係資料庫) 中的資料庫檢視。</span><span class="sxs-lookup"><span data-stu-id="2a28b-167">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="2a28b-168">這類定義可讓您輕鬆地針對記憶體中資料庫測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="2a28b-168">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="2a28b-169">之後，這些應用程式很有用，但卻變得不一致，而且難以理解的行為。</span><span class="sxs-lookup"><span data-stu-id="2a28b-169">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="2a28b-170">所以我們決定簡化這個概念。</span><span class="sxs-lookup"><span data-stu-id="2a28b-170">So we decided to simplify the concept.</span></span> <span data-ttu-id="2a28b-171">我們對 In-Memory 提供者進行以 LINQ 為基礎的定義查詢，並以不同的方式加以處理。</span><span class="sxs-lookup"><span data-stu-id="2a28b-171">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="2a28b-172">如需詳細資訊，請 [參閱此問題](https://github.com/dotnet/efcore/issues/20023)。</span><span class="sxs-lookup"><span data-stu-id="2a28b-172">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2a28b-173">風險降低</span><span class="sxs-lookup"><span data-stu-id="2a28b-173">Mitigations</span></span>

<span data-ttu-id="2a28b-174">若為關聯式提供者，請使用 `ToSqlQuery` 中的方法 `OnModelCreating` 並傳入要用於實體類型的 SQL 字串。</span><span class="sxs-lookup"><span data-stu-id="2a28b-174">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="2a28b-175">針對 In-Memory 提供者，請使用 `ToInMemoryQuery` 中的方法， `OnModelCreating` 並傳入 LINQ 查詢以用於實體型別。</span><span class="sxs-lookup"><span data-stu-id="2a28b-175">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>

<a name="nonnullreferences"></a>

### <a name="non-null-reference-navigations-are-not-overwritten-by-queries"></a><span data-ttu-id="2a28b-176">查詢不會覆寫非 null 的參考導覽</span><span class="sxs-lookup"><span data-stu-id="2a28b-176">Non-null reference navigations are not overwritten by queries</span></span>

[<span data-ttu-id="2a28b-177">追蹤問題 #2693</span><span class="sxs-lookup"><span data-stu-id="2a28b-177">Tracking Issue #2693</span></span>](https://github.com/dotnet/EntityFramework.Docs/issues/2693)

#### <a name="old-behavior"></a><span data-ttu-id="2a28b-178">舊的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-178">Old behavior</span></span>

<span data-ttu-id="2a28b-179">在 EF Core 3.1 中，已初始化為非 null 值的參考導覽立即，有時會由資料庫中的實體實例覆寫，而不論索引鍵值是否相符。</span><span class="sxs-lookup"><span data-stu-id="2a28b-179">In EF Core 3.1, reference navigations eagerly initialized to non-null values would sometimes be overwritten by entity instances from the database, regardless of whether or not key values matched.</span></span> <span data-ttu-id="2a28b-180">不過，在其他情況下，EF Core 3.1 會相反地，並保留現有的非 null 值。</span><span class="sxs-lookup"><span data-stu-id="2a28b-180">However, in other cases, EF Core 3.1 would do the opposite and leave the existing non-null value.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2a28b-181">新的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-181">New behavior</span></span>

<span data-ttu-id="2a28b-182">從 EF Core 5.0 開始，查詢所傳回的實例永遠不會覆寫非 null 的參考導覽。</span><span class="sxs-lookup"><span data-stu-id="2a28b-182">Starting with EF Core 5.0, non-null reference navigations are never overwritten by instances returned from a query.</span></span>

<span data-ttu-id="2a28b-183">請注意，仍支援對空集合進行 _集合_ 導覽的積極式初始化。</span><span class="sxs-lookup"><span data-stu-id="2a28b-183">Note that eager initialization of a _collection_ navigation to an empty collection is still supported.</span></span>

#### <a name="why"></a><span data-ttu-id="2a28b-184">原因</span><span class="sxs-lookup"><span data-stu-id="2a28b-184">Why</span></span>

<span data-ttu-id="2a28b-185">將參考導覽屬性初始化為「空白」實體實例，會導致不明確的狀態。</span><span class="sxs-lookup"><span data-stu-id="2a28b-185">Initialization of a reference navigation property to an "empty" entity instance results in an ambiguous state.</span></span> <span data-ttu-id="2a28b-186">例如：</span><span class="sxs-lookup"><span data-stu-id="2a28b-186">For example:</span></span>

```csharp
public class Blog
{
     public int Id { get; set; }
     public Author Author { get; set; ) = new Author();
}
```

<span data-ttu-id="2a28b-187">通常，Blog 和作者的查詢會先建立 `Blog` 實例，然後 `Author` 根據資料庫所傳回的資料，設定適當的實例。</span><span class="sxs-lookup"><span data-stu-id="2a28b-187">Normally a query for Blogs and Authors will first create `Blog` instances and then set the appropriate `Author` instances based on the data returned from the database.</span></span> <span data-ttu-id="2a28b-188">不過，在此情況下，每個 `Blog.Author` 屬性都已初始化為空的 `Author` 。</span><span class="sxs-lookup"><span data-stu-id="2a28b-188">However, in this case every `Blog.Author` property is already initialized to an empty `Author`.</span></span> <span data-ttu-id="2a28b-189">除了 EF Core 無法得知此實例是否為「空白」。</span><span class="sxs-lookup"><span data-stu-id="2a28b-189">Except EF Core has no way to know that this instance is "empty".</span></span> <span data-ttu-id="2a28b-190">因此，覆寫此實例可能會以無訊息方式擲回有效的 `Author` 。</span><span class="sxs-lookup"><span data-stu-id="2a28b-190">So overwriting this instance could potentially silently throw away a valid `Author`.</span></span> <span data-ttu-id="2a28b-191">因此，EF Core 5.0 現在一致地不會覆寫已初始化的導覽。</span><span class="sxs-lookup"><span data-stu-id="2a28b-191">Therefore, EF Core 5.0 now consistently does not overwrite a navigation that is already initialized.</span></span>

<span data-ttu-id="2a28b-192">在大部分的情況下，這項新行為也會與 EF6 的行為一致，不過在調查時，我們也發現 EF6 中有一些不一致的情況。</span><span class="sxs-lookup"><span data-stu-id="2a28b-192">This new behavior also aligns with the behavior of EF6 in most cases, although upon investigation we also found some cases of inconsistency in EF6.</span></span>  

#### <a name="mitigations"></a><span data-ttu-id="2a28b-193">風險降低</span><span class="sxs-lookup"><span data-stu-id="2a28b-193">Mitigations</span></span>

<span data-ttu-id="2a28b-194">如果遇到此中斷，則修正程式是停止立即初始化參考導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="2a28b-194">If this break is encountered, then the fix is to stop eagerly initializing reference navigation properties.</span></span>

## <a name="low-impact-changes"></a><span data-ttu-id="2a28b-195">低影響的變更</span><span class="sxs-lookup"><span data-stu-id="2a28b-195">Low-impact changes</span></span>

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="2a28b-196">已從 SQLite NTS 擴充功能移除 HasGeometricDimension 方法</span><span class="sxs-lookup"><span data-stu-id="2a28b-196">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="2a28b-197">追蹤問題 #14257</span><span class="sxs-lookup"><span data-stu-id="2a28b-197">Tracking Issue #14257</span></span>](https://github.com/dotnet/efcore/issues/14257)

#### <a name="old-behavior"></a><span data-ttu-id="2a28b-198">舊的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-198">Old behavior</span></span>

<span data-ttu-id="2a28b-199">HasGeometricDimension 用來在幾何資料行上啟用 (Z 和 M) 的其他維度。</span><span class="sxs-lookup"><span data-stu-id="2a28b-199">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="2a28b-200">但是，它只會影響資料庫的建立。</span><span class="sxs-lookup"><span data-stu-id="2a28b-200">However, it only ever affected database creation.</span></span> <span data-ttu-id="2a28b-201">不需要將它指定為使用其他維度來查詢值。</span><span class="sxs-lookup"><span data-stu-id="2a28b-201">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="2a28b-202">使用額外的維度插入或更新值時，也無法正確運作 ([請參閱 #14257](https://github.com/dotnet/efcore/issues/14257)) 。</span><span class="sxs-lookup"><span data-stu-id="2a28b-202">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/dotnet/efcore/issues/14257)).</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2a28b-203">新的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-203">New behavior</span></span>

<span data-ttu-id="2a28b-204">若要啟用以其他維度插入和更新幾何值 (Z 和 M) ，必須將維度指定為數據行類型名稱的一部分。</span><span class="sxs-lookup"><span data-stu-id="2a28b-204">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="2a28b-205">此 API 更接近 SpatiaLite 的 AddGeometryColumn 函式的基礎行為。</span><span class="sxs-lookup"><span data-stu-id="2a28b-205">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

#### <a name="why"></a><span data-ttu-id="2a28b-206">原因</span><span class="sxs-lookup"><span data-stu-id="2a28b-206">Why</span></span>

<span data-ttu-id="2a28b-207">在資料行類型中指定維度之後使用 HasGeometricDimension 是不必要且多餘的，因此我們會完全移除 HasGeometricDimension。</span><span class="sxs-lookup"><span data-stu-id="2a28b-207">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2a28b-208">風險降低</span><span class="sxs-lookup"><span data-stu-id="2a28b-208">Mitigations</span></span>

<span data-ttu-id="2a28b-209">使用 `HasColumnType` 指定維度：</span><span class="sxs-lookup"><span data-stu-id="2a28b-209">Use `HasColumnType` to specify the dimension:</span></span>

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

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="2a28b-210">Cosmos：現在已將資料分割索引鍵新增至主要索引鍵</span><span class="sxs-lookup"><span data-stu-id="2a28b-210">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="2a28b-211">追蹤問題 #15289</span><span class="sxs-lookup"><span data-stu-id="2a28b-211">Tracking Issue #15289</span></span>](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a><span data-ttu-id="2a28b-212">舊的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-212">Old behavior</span></span>

<span data-ttu-id="2a28b-213">分割區索引鍵屬性只會新增至包含的替代索引鍵 `id` 。</span><span class="sxs-lookup"><span data-stu-id="2a28b-213">The partition key property was only added to the alternate key that includes `id`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2a28b-214">新的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-214">New behavior</span></span>

<span data-ttu-id="2a28b-215">依慣例，資料分割索引鍵屬性現在也會依慣例新增至主要索引鍵。</span><span class="sxs-lookup"><span data-stu-id="2a28b-215">The partition key property is now also added to the primary key by convention.</span></span>

#### <a name="why"></a><span data-ttu-id="2a28b-216">原因</span><span class="sxs-lookup"><span data-stu-id="2a28b-216">Why</span></span>

<span data-ttu-id="2a28b-217">這項變更可讓模型更符合 Azure Cosmos DB 的語義，並改善 `Find` 和某些查詢的效能。</span><span class="sxs-lookup"><span data-stu-id="2a28b-217">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2a28b-218">風險降低</span><span class="sxs-lookup"><span data-stu-id="2a28b-218">Mitigations</span></span>

<span data-ttu-id="2a28b-219">若要避免將分割區索引鍵屬性加入至主鍵，請在中進行設定 `OnModelCreating` 。</span><span class="sxs-lookup"><span data-stu-id="2a28b-219">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="2a28b-220">Cosmos： `id` 屬性已重新命名為 `__id`</span><span class="sxs-lookup"><span data-stu-id="2a28b-220">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="2a28b-221">追蹤問題 #17751</span><span class="sxs-lookup"><span data-stu-id="2a28b-221">Tracking Issue #17751</span></span>](https://github.com/dotnet/efcore/issues/17751)

#### <a name="old-behavior"></a><span data-ttu-id="2a28b-222">舊的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-222">Old behavior</span></span>

<span data-ttu-id="2a28b-223">對應至 JSON 屬性的陰影屬性 `id` 也稱為 `id` 。</span><span class="sxs-lookup"><span data-stu-id="2a28b-223">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2a28b-224">新的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-224">New behavior</span></span>

<span data-ttu-id="2a28b-225">慣例所建立的陰影屬性現在已命名 `__id` 。</span><span class="sxs-lookup"><span data-stu-id="2a28b-225">The shadow property created by convention is now named `__id`.</span></span>

#### <a name="why"></a><span data-ttu-id="2a28b-226">原因</span><span class="sxs-lookup"><span data-stu-id="2a28b-226">Why</span></span>

<span data-ttu-id="2a28b-227">這種變更可讓 `id` 屬性與實體型別上的現有屬性不衝突。</span><span class="sxs-lookup"><span data-stu-id="2a28b-227">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2a28b-228">風險降低</span><span class="sxs-lookup"><span data-stu-id="2a28b-228">Mitigations</span></span>

<span data-ttu-id="2a28b-229">若要返回3.x 行為，請 `id` 在中設定屬性 `OnModelCreating` 。</span><span class="sxs-lookup"><span data-stu-id="2a28b-229">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="2a28b-230">Cosmos： byte [] 現在會儲存為 base64 字串，而不是數位陣列</span><span class="sxs-lookup"><span data-stu-id="2a28b-230">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="2a28b-231">追蹤問題 #17306</span><span class="sxs-lookup"><span data-stu-id="2a28b-231">Tracking Issue #17306</span></span>](https://github.com/dotnet/efcore/issues/17306)

#### <a name="old-behavior"></a><span data-ttu-id="2a28b-232">舊的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-232">Old behavior</span></span>

<span data-ttu-id="2a28b-233">Byte [] 類型的屬性已儲存為數字陣列。</span><span class="sxs-lookup"><span data-stu-id="2a28b-233">Properties of type byte[] were stored as a number array.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2a28b-234">新的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-234">New behavior</span></span>

<span data-ttu-id="2a28b-235">Byte [] 類型的屬性現在會儲存為 base64 字串。</span><span class="sxs-lookup"><span data-stu-id="2a28b-235">Properties of type byte[] are now stored as a base64 string.</span></span>

#### <a name="why"></a><span data-ttu-id="2a28b-236">原因</span><span class="sxs-lookup"><span data-stu-id="2a28b-236">Why</span></span>

<span data-ttu-id="2a28b-237">Byte [] 的這個標記法符合預期，而且是主要 JSON 序列化程式庫的預設行為。</span><span class="sxs-lookup"><span data-stu-id="2a28b-237">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2a28b-238">風險降低</span><span class="sxs-lookup"><span data-stu-id="2a28b-238">Mitigations</span></span>

<span data-ttu-id="2a28b-239">以數位陣列形式儲存的現有資料仍會正確地進行查詢，但目前不支援變更插入行為的方式。</span><span class="sxs-lookup"><span data-stu-id="2a28b-239">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="2a28b-240">如果這項限制會封鎖您的案例，請針對[此問題](https://github.com/dotnet/efcore/issues/17306)進行批註</span><span class="sxs-lookup"><span data-stu-id="2a28b-240">If this limitation is blocking your scenario, comment on [this issue](https://github.com/dotnet/efcore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="2a28b-241">Cosmos： GetPropertyName 和 SetPropertyName 已重新命名</span><span class="sxs-lookup"><span data-stu-id="2a28b-241">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="2a28b-242">追蹤問題 #17874</span><span class="sxs-lookup"><span data-stu-id="2a28b-242">Tracking Issue #17874</span></span>](https://github.com/dotnet/efcore/issues/17874)

#### <a name="old-behavior"></a><span data-ttu-id="2a28b-243">舊的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-243">Old behavior</span></span>

<span data-ttu-id="2a28b-244">先前已呼叫擴充方法 `GetPropertyName` ，而且 `SetPropertyName`</span><span class="sxs-lookup"><span data-stu-id="2a28b-244">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2a28b-245">新的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-245">New behavior</span></span>

<span data-ttu-id="2a28b-246">移除舊的 API 並新增新 `GetJsonPropertyName` 方法： `SetJsonPropertyName`</span><span class="sxs-lookup"><span data-stu-id="2a28b-246">The old API was removed and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

#### <a name="why"></a><span data-ttu-id="2a28b-247">原因</span><span class="sxs-lookup"><span data-stu-id="2a28b-247">Why</span></span>

<span data-ttu-id="2a28b-248">這項變更會移除這些方法所設定的混淆。</span><span class="sxs-lookup"><span data-stu-id="2a28b-248">This change removes the ambiguity around what these methods are configuring.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2a28b-249">風險降低</span><span class="sxs-lookup"><span data-stu-id="2a28b-249">Mitigations</span></span>

<span data-ttu-id="2a28b-250">使用新的 API。</span><span class="sxs-lookup"><span data-stu-id="2a28b-250">Use the new API.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="2a28b-251">當實體狀態從卸離變更為未變更、更新或刪除時，會呼叫值產生器</span><span class="sxs-lookup"><span data-stu-id="2a28b-251">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="2a28b-252">追蹤問題 #15289</span><span class="sxs-lookup"><span data-stu-id="2a28b-252">Tracking Issue #15289</span></span>](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a><span data-ttu-id="2a28b-253">舊的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-253">Old behavior</span></span>

<span data-ttu-id="2a28b-254">只有當實體狀態變更為 [已加入] 時，才會呼叫值產生器。</span><span class="sxs-lookup"><span data-stu-id="2a28b-254">Value generators were only called when the entity state changed to Added.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2a28b-255">新的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-255">New behavior</span></span>

<span data-ttu-id="2a28b-256">現在當實體狀態從卸離變更為未變更、已更新或已刪除，且屬性包含預設值時，就會呼叫值產生器。</span><span class="sxs-lookup"><span data-stu-id="2a28b-256">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

#### <a name="why"></a><span data-ttu-id="2a28b-257">原因</span><span class="sxs-lookup"><span data-stu-id="2a28b-257">Why</span></span>

<span data-ttu-id="2a28b-258">需要進行這種變更，才能使用不會保存到資料存放區的屬性，以及在用戶端上永遠產生其值的方式，來改善體驗。</span><span class="sxs-lookup"><span data-stu-id="2a28b-258">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2a28b-259">風險降低</span><span class="sxs-lookup"><span data-stu-id="2a28b-259">Mitigations</span></span>

<span data-ttu-id="2a28b-260">若要防止呼叫值產生器，請在狀態變更之前，將非預設值指派給屬性。</span><span class="sxs-lookup"><span data-stu-id="2a28b-260">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="2a28b-261">IMigrationsModelDiffer 現在使用 IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="2a28b-261">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="2a28b-262">追蹤問題 #20305</span><span class="sxs-lookup"><span data-stu-id="2a28b-262">Tracking Issue #20305</span></span>](https://github.com/dotnet/efcore/issues/20305)

#### <a name="old-behavior"></a><span data-ttu-id="2a28b-263">舊的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-263">Old behavior</span></span>

<span data-ttu-id="2a28b-264">`IMigrationsModelDiffer` API 的定義使用 `IModel` 。</span><span class="sxs-lookup"><span data-stu-id="2a28b-264">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2a28b-265">新的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-265">New behavior</span></span>

<span data-ttu-id="2a28b-266">`IMigrationsModelDiffer` API 現在會使用 `IRelationalModel` 。</span><span class="sxs-lookup"><span data-stu-id="2a28b-266">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="2a28b-267">不過，模型快照集仍會包含， `IModel` 因為此程式碼是應用程式的一部分，而且 Entity Framework 無法變更它，而不會進行較大的中斷變更。</span><span class="sxs-lookup"><span data-stu-id="2a28b-267">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

#### <a name="why"></a><span data-ttu-id="2a28b-268">原因</span><span class="sxs-lookup"><span data-stu-id="2a28b-268">Why</span></span>

<span data-ttu-id="2a28b-269">`IRelationalModel` 這是新加入的資料庫架構標記法。</span><span class="sxs-lookup"><span data-stu-id="2a28b-269">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="2a28b-270">使用它來尋找差異會更快且更精確。</span><span class="sxs-lookup"><span data-stu-id="2a28b-270">Using it to find differences is faster and more accurate.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2a28b-271">風險降低</span><span class="sxs-lookup"><span data-stu-id="2a28b-271">Mitigations</span></span>

<span data-ttu-id="2a28b-272">您可以使用下列程式碼，將模型 `snapshot` 與模型進行比較， `context` 如下所示：</span><span class="sxs-lookup"><span data-stu-id="2a28b-272">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

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

<span data-ttu-id="2a28b-273">我們打算在 6.0 ([查看 #22031](https://github.com/dotnet/efcore/issues/22031) 的這項體驗) </span><span class="sxs-lookup"><span data-stu-id="2a28b-273">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="2a28b-274">鑒別子是唯讀的</span><span class="sxs-lookup"><span data-stu-id="2a28b-274">Discriminators are read-only</span></span>

[<span data-ttu-id="2a28b-275">追蹤問題 #21154</span><span class="sxs-lookup"><span data-stu-id="2a28b-275">Tracking Issue #21154</span></span>](https://github.com/dotnet/efcore/issues/21154)

#### <a name="old-behavior"></a><span data-ttu-id="2a28b-276">舊的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-276">Old behavior</span></span>

<span data-ttu-id="2a28b-277">您可以在呼叫之前變更鑒別子值 `SaveChanges`</span><span class="sxs-lookup"><span data-stu-id="2a28b-277">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2a28b-278">新的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-278">New behavior</span></span>

<span data-ttu-id="2a28b-279">在上述案例中，將會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="2a28b-279">An exception will be throws in the above case.</span></span>

#### <a name="why"></a><span data-ttu-id="2a28b-280">原因</span><span class="sxs-lookup"><span data-stu-id="2a28b-280">Why</span></span>

<span data-ttu-id="2a28b-281">EF 不會預期實體類型在仍在追蹤時變更，因此變更鑒別子值會使內容處於不一致的狀態，而這可能會導致非預期的行為。</span><span class="sxs-lookup"><span data-stu-id="2a28b-281">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2a28b-282">風險降低</span><span class="sxs-lookup"><span data-stu-id="2a28b-282">Mitigations</span></span>

<span data-ttu-id="2a28b-283">如果需要變更鑒別子值，而且會在呼叫後立即處置內容 `SaveChanges` ，則可以將鑒別子設為可變：</span><span class="sxs-lookup"><span data-stu-id="2a28b-283">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```csharp
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a><span data-ttu-id="2a28b-284">提供者特定的 EF。InMemory 提供者的函數方法擲回</span><span class="sxs-lookup"><span data-stu-id="2a28b-284">Provider-specific EF.Functions methods throw for InMemory provider</span></span>

[<span data-ttu-id="2a28b-285">追蹤問題 #20294</span><span class="sxs-lookup"><span data-stu-id="2a28b-285">Tracking Issue #20294</span></span>](https://github.com/dotnet/efcore/issues/20294)

#### <a name="old-behavior"></a><span data-ttu-id="2a28b-286">舊的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-286">Old behavior</span></span>

<span data-ttu-id="2a28b-287">提供者特定的 EF。函數方法包含用戶端執行的執行，可讓它們在 InMemory 提供者上執行。</span><span class="sxs-lookup"><span data-stu-id="2a28b-287">Provider-specific EF.Functions methods contained implementation for client execution, which allowed them to be executed on the InMemory provider.</span></span> <span data-ttu-id="2a28b-288">例如， `EF.Functions.DateDiffDay` 是一種 Sql Server 特定的方法，它是在 InMemory 提供者上處理。</span><span class="sxs-lookup"><span data-stu-id="2a28b-288">For example, `EF.Functions.DateDiffDay` is a Sql Server specific method, which worked on InMemory provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2a28b-289">新的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-289">New behavior</span></span>

<span data-ttu-id="2a28b-290">提供者特定方法已更新，在其方法主體中擲回例外狀況，以封鎖在用戶端上進行評估。</span><span class="sxs-lookup"><span data-stu-id="2a28b-290">Provider-specific methods have been updated to throw an exception in their method body to block evaluating them on client side.</span></span>

#### <a name="why"></a><span data-ttu-id="2a28b-291">原因</span><span class="sxs-lookup"><span data-stu-id="2a28b-291">Why</span></span>

<span data-ttu-id="2a28b-292">提供者特定的方法會對應至資料庫函式。</span><span class="sxs-lookup"><span data-stu-id="2a28b-292">Provider-specific methods map to a database function.</span></span> <span data-ttu-id="2a28b-293">對應資料庫函式所完成的計算無法永遠在 LINQ 的用戶端上複寫。</span><span class="sxs-lookup"><span data-stu-id="2a28b-293">The computation done by the mapped database function can't always be replicated on the client side in LINQ.</span></span> <span data-ttu-id="2a28b-294">在用戶端上執行相同的方法時，可能會導致伺服器的結果不同。</span><span class="sxs-lookup"><span data-stu-id="2a28b-294">It may cause the result from the server to differ when executing the same method on client.</span></span> <span data-ttu-id="2a28b-295">因為這些方法是在 LINQ 中用來轉譯為特定的資料庫函式，所以不需要在用戶端上進行評估。</span><span class="sxs-lookup"><span data-stu-id="2a28b-295">Since these methods are used in LINQ to translate to specific database functions, they don't need to be evaluated on client side.</span></span> <span data-ttu-id="2a28b-296">因為 InMemory 提供者是不同的 *資料庫* ，所以這些方法不適用於此提供者。</span><span class="sxs-lookup"><span data-stu-id="2a28b-296">As InMemory provider is a different *database* , these methods aren't available for this provider.</span></span> <span data-ttu-id="2a28b-297">嘗試針對 InMemory 提供者或任何其他未轉譯這些方法的提供者執行這些專案時，會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="2a28b-297">Trying to execute them for InMemory provider, or any other provider that doesn't translate these methods, throws an exception.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2a28b-298">風險降低</span><span class="sxs-lookup"><span data-stu-id="2a28b-298">Mitigations</span></span>

<span data-ttu-id="2a28b-299">因為無法準確模擬資料庫函式的行為，所以您應該針對與生產環境中相同類型的資料庫，測試包含它們的查詢。</span><span class="sxs-lookup"><span data-stu-id="2a28b-299">Since there's no way to mimic behavior of database functions accurately, you should test the queries containing them against same kind of database as in production.</span></span>

<a name="index-obsolete"></a>

### <a name="indexbuilderhasname-is-now-obsolete"></a><span data-ttu-id="2a28b-300">IndexBuilder. HasName 現已淘汰</span><span class="sxs-lookup"><span data-stu-id="2a28b-300">IndexBuilder.HasName is now obsolete</span></span>

[<span data-ttu-id="2a28b-301">追蹤問題 #21089</span><span class="sxs-lookup"><span data-stu-id="2a28b-301">Tracking Issue #21089</span></span>](https://github.com/dotnet/efcore/issues/21089)

#### <a name="old-behavior"></a><span data-ttu-id="2a28b-302">舊的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-302">Old behavior</span></span>

<span data-ttu-id="2a28b-303">之前，只能在一組指定的屬性上定義一個索引。</span><span class="sxs-lookup"><span data-stu-id="2a28b-303">Previously, only one index could be defined over a given set of properties.</span></span> <span data-ttu-id="2a28b-304">使用 IndexBuilder. HasName 設定索引的資料庫名稱。</span><span class="sxs-lookup"><span data-stu-id="2a28b-304">The database name of an index was configured using IndexBuilder.HasName.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2a28b-305">新的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-305">New behavior</span></span>

<span data-ttu-id="2a28b-306">相同的集合或屬性現在允許多個索引。</span><span class="sxs-lookup"><span data-stu-id="2a28b-306">Multiple indexes are now allowed on the same set or properties.</span></span> <span data-ttu-id="2a28b-307">這些索引現在是由模型中的名稱來區分。</span><span class="sxs-lookup"><span data-stu-id="2a28b-307">These indexes are now distinguished by a name in the model.</span></span> <span data-ttu-id="2a28b-308">依照慣例，會使用模型名稱做為資料庫名稱;不過，您也可以使用 HasDatabaseName 獨立設定它。</span><span class="sxs-lookup"><span data-stu-id="2a28b-308">By convention, the model name is used as the database name; however it can also be configured independently using HasDatabaseName.</span></span>

#### <a name="why"></a><span data-ttu-id="2a28b-309">原因</span><span class="sxs-lookup"><span data-stu-id="2a28b-309">Why</span></span>

<span data-ttu-id="2a28b-310">未來，我們想要在相同的屬性集上啟用遞增和遞減索引，或具有不同定序的索引。</span><span class="sxs-lookup"><span data-stu-id="2a28b-310">In the future, we'd like to enable both ascending and descending indexes or indexes with different collations on the same set of properties.</span></span> <span data-ttu-id="2a28b-311">這項變更會將另一個步驟移至該方向。</span><span class="sxs-lookup"><span data-stu-id="2a28b-311">This change moves us another step in that direction.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2a28b-312">風險降低</span><span class="sxs-lookup"><span data-stu-id="2a28b-312">Mitigations</span></span>

<span data-ttu-id="2a28b-313">先前呼叫 IndexBuilder 的任何程式碼都應該更新為改為呼叫 HasDatabaseName。</span><span class="sxs-lookup"><span data-stu-id="2a28b-313">Any code that was previously calling IndexBuilder.HasName should be updated to call HasDatabaseName instead.</span></span>

<span data-ttu-id="2a28b-314">如果您的專案包含 EF Core 版本2.0.0 之前產生的遷移，您可以放心地忽略這些檔案中的警告，並藉由新增來隱藏它 `#pragma warning disable 612, 618` 。</span><span class="sxs-lookup"><span data-stu-id="2a28b-314">If your project includes migrations generated prior to EF Core version 2.0.0, you can safely ignore the warning in those files and suppress it by adding `#pragma warning disable 612, 618`.</span></span>

<a name="pluralizer"></a>

### <a name="a-pluralizer-is-now-included-for-scaffolding-reverse-engineered-models"></a><span data-ttu-id="2a28b-315">Pluralizer 現已包含給樣板反轉工程模型</span><span class="sxs-lookup"><span data-stu-id="2a28b-315">A pluralizer is now included for scaffolding reverse engineered models</span></span>

[<span data-ttu-id="2a28b-316">追蹤問題 #11160</span><span class="sxs-lookup"><span data-stu-id="2a28b-316">Tracking Issue #11160</span></span>](https://github.com/dotnet/efcore/issues/11160)

#### <a name="old-behavior"></a><span data-ttu-id="2a28b-317">舊的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-317">Old behavior</span></span>

<span data-ttu-id="2a28b-318">先前，您必須安裝個別的 pluralizer 套件，才能在將資料庫架構還原為 DbCoNtext 和實體類型時，複數化 DbSet 和集合導覽名稱和單數化資料表名稱。</span><span class="sxs-lookup"><span data-stu-id="2a28b-318">Previously, you had to install a separate pluralizer package in order to pluralize DbSet and collection navigation names and singularize table names when scaffolding a DbContext and entity types by reverse engineering a database schema.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2a28b-319">新的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-319">New behavior</span></span>

<span data-ttu-id="2a28b-320">EF Core 現在包含使用 [Humanizer](https://github.com/Humanizr/Humanizer) 程式庫的 pluralizer。</span><span class="sxs-lookup"><span data-stu-id="2a28b-320">EF Core now includes a pluralizer that uses the [Humanizer](https://github.com/Humanizr/Humanizer) library.</span></span> <span data-ttu-id="2a28b-321">這是 Visual Studio 用來建議變數名稱的相同程式庫。</span><span class="sxs-lookup"><span data-stu-id="2a28b-321">This is the same library Visual Studio uses to recommend variable names.</span></span>

#### <a name="why"></a><span data-ttu-id="2a28b-322">原因</span><span class="sxs-lookup"><span data-stu-id="2a28b-322">Why</span></span>

<span data-ttu-id="2a28b-323">針對集合屬性使用複數形式的單字以及類型和參考屬性的單數形式，在 .NET 中慣用。</span><span class="sxs-lookup"><span data-stu-id="2a28b-323">Using plural forms of words for collection properties and singular forms for types and reference properties is idiomatic in .NET.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2a28b-324">風險降低</span><span class="sxs-lookup"><span data-stu-id="2a28b-324">Mitigations</span></span>

<span data-ttu-id="2a28b-325">若要停用 pluralizer，請使用 `--no-pluralize` 上的選項 `dotnet ef dbcontext scaffold` 或開啟上的 `-NoPluralize` 參數 `Scaffold-DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="2a28b-325">To disable the pluralizer, use the `--no-pluralize` option on `dotnet ef dbcontext scaffold` or the `-NoPluralize` switch on `Scaffold-DbContext`.</span></span>

<a name="inavigationbase"></a>

### <a name="inavigationbase-replaces-inavigation-in-some-apis-to-support-skip-navigations"></a><span data-ttu-id="2a28b-326">INavigationBase 取代某些 Api 中的 INavigation，以支援略過導覽</span><span class="sxs-lookup"><span data-stu-id="2a28b-326">INavigationBase replaces INavigation in some APIs to support skip navigations</span></span>

[<span data-ttu-id="2a28b-327">追蹤問題 #2568</span><span class="sxs-lookup"><span data-stu-id="2a28b-327">Tracking Issue #2568</span></span>](https://github.com/dotnet/EntityFramework.Docs/issues/2568)

#### <a name="old-behavior"></a><span data-ttu-id="2a28b-328">舊的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-328">Old behavior</span></span>

<span data-ttu-id="2a28b-329">5.0 之前的 EF Core 只支援一個以介面表示的導覽屬性形式 `INavigation` 。</span><span class="sxs-lookup"><span data-stu-id="2a28b-329">EF Core prior to 5.0 supported only one form of navigation property, represented by the `INavigation` interface.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2a28b-330">新的行為</span><span class="sxs-lookup"><span data-stu-id="2a28b-330">New behavior</span></span>

<span data-ttu-id="2a28b-331">EF Core 5.0 引進了使用「略過導覽」的多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="2a28b-331">EF Core 5.0 introduces many-to-many relationships which use "skip navigations".</span></span> <span data-ttu-id="2a28b-332">這些是由介面表示 `ISkipNavigation` ，而的大部分功能 `INavigation` 都已向下推送至通用基底介面： `INavigationBase` 。</span><span class="sxs-lookup"><span data-stu-id="2a28b-332">These are represented by the `ISkipNavigation` interface, and most of the functionality of `INavigation` has been pushed down to a common base interface: `INavigationBase`.</span></span>

#### <a name="why"></a><span data-ttu-id="2a28b-333">原因</span><span class="sxs-lookup"><span data-stu-id="2a28b-333">Why</span></span>

<span data-ttu-id="2a28b-334">一般與略過導覽之間的大部分功能都相同。</span><span class="sxs-lookup"><span data-stu-id="2a28b-334">Most of the functionality between normal and skip navigations is the same.</span></span> <span data-ttu-id="2a28b-335">不過，略過導覽與外鍵的關聯性與一般導覽不同，因為所涉及的 Fk 不會直接在關聯性的任一端，而是在聯結實體中。</span><span class="sxs-lookup"><span data-stu-id="2a28b-335">However, skip navigations have a different relationship to foreign keys than normal navigations, since the FKs involved are not directly on either end of the relationship, but rather in the join entity.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2a28b-336">風險降低</span><span class="sxs-lookup"><span data-stu-id="2a28b-336">Mitigations</span></span>

<span data-ttu-id="2a28b-337">在許多情況下，應用程式可以切換為使用新的基底介面，而不需要進行其他變更。</span><span class="sxs-lookup"><span data-stu-id="2a28b-337">In many cases applications can switch to using the new base interface with no other changes.</span></span> <span data-ttu-id="2a28b-338">不過，在使用導覽來存取外鍵屬性的情況下，應用程式程式碼應該只限于一般導覽，或更新為一般和略過導覽的適當事物。</span><span class="sxs-lookup"><span data-stu-id="2a28b-338">However, in cases where the navigation is used to access foreign key properties, application code should either be constrained to only normal navigations, or updated to do the appropriate thing for both normal and skip navigations.</span></span>

<a name="collection-distinct-groupby"></a>

### <a name="some-queries-with-correlated-collection-that-also-use-distinct-or-groupby-are-no-longer-supported"></a><span data-ttu-id="2a28b-339">某些使用 `Distinct` 或已不再支援之相互關聯集合的查詢 `GroupBy`</span><span class="sxs-lookup"><span data-stu-id="2a28b-339">Some queries with correlated collection that also use `Distinct` or `GroupBy` are no longer supported</span></span>

[<span data-ttu-id="2a28b-340">追蹤問題 #15873</span><span class="sxs-lookup"><span data-stu-id="2a28b-340">Tracking Issue #15873</span></span>](https://github.com/dotnet/efcore/issues/15873)

<span data-ttu-id="2a28b-341">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="2a28b-341">**Old behavior**</span></span>

<span data-ttu-id="2a28b-342">先前的查詢牽涉到相關聯的集合，後面接著 `GroupBy` ，以及使用 `Distinct` 我們允許執行的一些查詢。</span><span class="sxs-lookup"><span data-stu-id="2a28b-342">Previously, queries involving correlated collections followed by `GroupBy`, as well as some queries using `Distinct` we allowed to execute.</span></span>

<span data-ttu-id="2a28b-343">GroupBy 範例：</span><span class="sxs-lookup"><span data-stu-id="2a28b-343">GroupBy example:</span></span>

```csharp
context.Parents
    .Select(p => p.Children
        .GroupBy(c => c.School)
        .Select(g => g.Key))
```

<span data-ttu-id="2a28b-344">`Distinct` 範例-明確 `Distinct` 查詢內部集合投射未包含主鍵的查詢：</span><span class="sxs-lookup"><span data-stu-id="2a28b-344">`Distinct` example - specifically `Distinct` queries where inner collection projection doesn't contain the primary key:</span></span>

```csharp
context.Parents
    .Select(p => p.Children
        .Select(c => c.School)
        .Distinct())
```

<span data-ttu-id="2a28b-345">如果內部集合包含任何重複專案，這些查詢可能會傳回不正確的結果，但如果內部集合中的所有專案都是唯一的，則這些查詢會正確運作。</span><span class="sxs-lookup"><span data-stu-id="2a28b-345">These queries could return incorrect results if the inner collection contained any duplicates, but worked correctly if all the elements in the inner collection were unique.</span></span>

<span data-ttu-id="2a28b-346">**新的行為**</span><span class="sxs-lookup"><span data-stu-id="2a28b-346">**New behavior**</span></span>

<span data-ttu-id="2a28b-347">這些查詢並不是 loger 的支援。</span><span class="sxs-lookup"><span data-stu-id="2a28b-347">These queries are no loger suppored.</span></span> <span data-ttu-id="2a28b-348">擲回例外狀況，表示我們沒有足夠的資訊來正確建立結果。</span><span class="sxs-lookup"><span data-stu-id="2a28b-348">Exception is thrown indicating that we don't have enough information to correctly build the results.</span></span>

<span data-ttu-id="2a28b-349">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="2a28b-349">**Why**</span></span>

<span data-ttu-id="2a28b-350">針對相互關聯的集合案例，我們需要知道實體的主鍵，才能將集合實體指派給正確的父系。</span><span class="sxs-lookup"><span data-stu-id="2a28b-350">For correlated collection scenarios we need to know entity's primary key in order to assign collection entities to the correct parent.</span></span> <span data-ttu-id="2a28b-351">當內部集合不使用 `GroupBy` 或時 `Distinct` ，遺漏的主鍵可以直接加入投影中。</span><span class="sxs-lookup"><span data-stu-id="2a28b-351">When inner collection doesn't use `GroupBy` or `Distinct`, the missing primary key can simply be added to the projection.</span></span> <span data-ttu-id="2a28b-352">但是，如果是 `GroupBy` ， `Distinct` 則無法完成，因為它會變更 `GroupBy` 或運算的結果 `Distinct` 。</span><span class="sxs-lookup"><span data-stu-id="2a28b-352">However in case of `GroupBy` and `Distinct` it can't be done because it would change the result of `GroupBy` or `Distinct` operation.</span></span>

<span data-ttu-id="2a28b-353">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="2a28b-353">**Mitigations**</span></span>

<span data-ttu-id="2a28b-354">請將查詢重寫為不使用 `GroupBy` `Distinct` 內部集合上的或作業，並改為在用戶端上執行這些作業。</span><span class="sxs-lookup"><span data-stu-id="2a28b-354">Rewrite the query to not use `GroupBy` or `Distinct` operations on the inner collection, and perform these operations on the client instead.</span></span>

```csharp
context.Parents
    .Select(p => p.Children.Select(c => c.School))
    .ToList()
    .Select(x => x.GroupBy(c => c).Select(g => g.Key))
```

```csharp
context.Parents
    .Select(p => p.Children.Select(c => c.School))
    .ToList()
    .Select(x => x.Distinct())
```

<a name="queryable-projection"></a>

### <a name="using-a-collection-of-queryable-type-in-projection-is-not-supported"></a><span data-ttu-id="2a28b-355">不支援在投射中使用可查詢型別的集合</span><span class="sxs-lookup"><span data-stu-id="2a28b-355">Using a collection of Queryable type in projection is not supported</span></span>

[<span data-ttu-id="2a28b-356">追蹤問題 #16314</span><span class="sxs-lookup"><span data-stu-id="2a28b-356">Tracking Issue #16314</span></span>](https://github.com/dotnet/efcore/issues/16314)

<span data-ttu-id="2a28b-357">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="2a28b-357">**Old behavior**</span></span>

<span data-ttu-id="2a28b-358">先前，在某些情況下，可以在投射內使用可查詢型別的集合，例如作為函式的引數 `List<T>` ：</span><span class="sxs-lookup"><span data-stu-id="2a28b-358">Previously, it was possible to use collection of a Queryable type inside the projection in some cases, for example as an argument to a `List<T>` constructor:</span></span>

```csharp
context.Blogs
    .Select(b => new List<Post>(context.Posts.Where(p => p.BlogId == b.Id)))
```

<span data-ttu-id="2a28b-359">**新的行為**</span><span class="sxs-lookup"><span data-stu-id="2a28b-359">**New behavior**</span></span>

<span data-ttu-id="2a28b-360">這些查詢並不是 loger 的支援。</span><span class="sxs-lookup"><span data-stu-id="2a28b-360">These queries are no loger suppored.</span></span> <span data-ttu-id="2a28b-361">擲回例外狀況，表示我們無法建立可查詢型別的物件，並建議如何修正此問題。</span><span class="sxs-lookup"><span data-stu-id="2a28b-361">Exception is thrown indicating that we can't create an object of Queryable type and suggesting how this could be fixed.</span></span>

<span data-ttu-id="2a28b-362">**為什麼**</span><span class="sxs-lookup"><span data-stu-id="2a28b-362">**Why**</span></span>

<span data-ttu-id="2a28b-363">我們無法具體化可查詢型別的物件，因此會自動使用型別來建立它們 `List<T>` 。</span><span class="sxs-lookup"><span data-stu-id="2a28b-363">We can't materialize an object of a Queryable type, so they would automatically be created using `List<T>` type instead.</span></span> <span data-ttu-id="2a28b-364">這通常會造成例外狀況，因為類型不符，但不太清楚，而且可能會對某些使用者感到驚訝。</span><span class="sxs-lookup"><span data-stu-id="2a28b-364">This would often cause an exception due to type mismatch which was not very clear and could be surprising to some users.</span></span> <span data-ttu-id="2a28b-365">我們決定要辨識模式，並擲回更有意義的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="2a28b-365">We decided to recognize the pattern and throw a more meaningful exception.</span></span>

<span data-ttu-id="2a28b-366">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="2a28b-366">**Mitigations**</span></span>

<span data-ttu-id="2a28b-367">在 `ToList()` 投射中的可查詢物件之後加入呼叫：</span><span class="sxs-lookup"><span data-stu-id="2a28b-367">Add `ToList()` call after the Queryable object in the projection:</span></span>

```csharp
context.Blogs.Select(b => context.Posts.Where(p => p.BlogId == b.Id).ToList())
```
