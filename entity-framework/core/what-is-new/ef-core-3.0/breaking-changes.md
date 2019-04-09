---
title: EF Core 3.0 的中斷性變更 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: EE2878C9-71F9-4FA5-9BC4-60517C7C9830
uid: core/what-is-new/ef-core-3.0/breaking-changes
ms.openlocfilehash: fd593b2832a5a6ffe27cd4493127b5d405f684ba
ms.sourcegitcommit: ce44f85a5bce32ef2d3d09b7682108d3473511b3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2019
ms.locfileid: "58914123"
---
# <a name="breaking-changes-included-in-ef-core-30-currently-in-preview"></a><span data-ttu-id="fd92e-102">EF Core 3.0 (目前為預覽版) 包含的中斷性變更</span><span class="sxs-lookup"><span data-stu-id="fd92e-102">Breaking changes included in EF Core 3.0 (currently in preview)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="fd92e-103">請注意，後續版本的功能集與上市時間，隨時可能會變動，此外，即使我們試圖將此頁面保持在最新狀態，可能還是無法隨時反映我們最新的計劃。</span><span class="sxs-lookup"><span data-stu-id="fd92e-103">Please note that the feature sets and schedules of future releases are always subject to change, and although we will try to keep this page up to date, it may not reflect our latest plans at all times.</span></span>

<span data-ttu-id="fd92e-104">當您將針對 EF Core 2.2.x 開發的應用程式升級為 3.0.0 時，下列 API 和行為變更可能會中斷這些應用程式。</span><span class="sxs-lookup"><span data-stu-id="fd92e-104">The following API and behavior changes have the potential to break applications developed for EF Core 2.2.x when upgrading them to 3.0.0.</span></span>
<span data-ttu-id="fd92e-105">這些變更預期只會影響[提供者變更](../../providers/provider-log.md)底下記載的資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="fd92e-105">Changes that we expect to only impact database providers are documented under [provider changes](../../providers/provider-log.md).</span></span>
<span data-ttu-id="fd92e-106">本文不會記載從某一 3.0 預覽版引進另一個 3.0 預覽版的新功能中斷。</span><span class="sxs-lookup"><span data-stu-id="fd92e-106">Breaks in new features introduced from one 3.0 preview to another 3.0 preview aren't documented here.</span></span>

## <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a><span data-ttu-id="fd92e-107">不會再於用戶端評估 LINQ 查詢</span><span class="sxs-lookup"><span data-stu-id="fd92e-107">LINQ queries are no longer evaluated on the client</span></span>

<span data-ttu-id="fd92e-108">[追蹤問題 #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[另請參閱問題 #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span><span class="sxs-lookup"><span data-stu-id="fd92e-108">[Tracking Issue #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[Also see issue #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span></span>

<span data-ttu-id="fd92e-109">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="fd92e-109">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="fd92e-110">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-110">Old behavior</span></span>**

<span data-ttu-id="fd92e-111">3.0 以前，在 EF Core 無法將屬於查詢的運算式轉換成 SQL 或參數時，它會自動在用戶端評估運算式。</span><span class="sxs-lookup"><span data-stu-id="fd92e-111">Before 3.0, when EF Core couldn't convert an expression that was part of a query to either SQL or a parameter, it automatically evaluated the expression on the client.</span></span>
<span data-ttu-id="fd92e-112">根據預設，對可能相當耗費資源的運算式進行用戶端評估只會觸發警告。</span><span class="sxs-lookup"><span data-stu-id="fd92e-112">By default, client evaluation of potentially expensive expressions only triggered a warning.</span></span>

**<span data-ttu-id="fd92e-113">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-113">New behavior</span></span>**

<span data-ttu-id="fd92e-114">從 3.0 開始，EF Core 只允許在用戶端評估最上層投影的運算式 (查詢中的最後一個 `Select()` 呼叫)。</span><span class="sxs-lookup"><span data-stu-id="fd92e-114">Starting with 3.0, EF Core only allows expressions in the top-level projection (the last `Select()` call in the query) to be evaluated on the client.</span></span>
<span data-ttu-id="fd92e-115">當其他查詢部分中的運算式無法轉換成 SQL 或參數時，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="fd92e-115">When expressions in any other part of the query can't be converted to either SQL or a parameter, an exception is thrown.</span></span>

**<span data-ttu-id="fd92e-116">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-116">Why</span></span>**

<span data-ttu-id="fd92e-117">查詢的自動用戶端評估可執行許多查詢，即使無法轉譯查詢的重要部分也一樣。</span><span class="sxs-lookup"><span data-stu-id="fd92e-117">Automatic client evaluation of queries allows many queries to be executed even if important parts of them can't be translated.</span></span>
<span data-ttu-id="fd92e-118">此行為可能會導致非預期且可能造成傷害的行為，而且該行為可能只會出現在生產環境中。</span><span class="sxs-lookup"><span data-stu-id="fd92e-118">This behavior can result in unexpected and potentially damaging behavior that may only become evident in production.</span></span>
<span data-ttu-id="fd92e-119">例如，`Where()` 呼叫中無法轉譯的條件可能會導致資料表中的所有資料列從資料庫伺服器移轉，並在用戶端套用篩選。</span><span class="sxs-lookup"><span data-stu-id="fd92e-119">For example, a condition in a `Where()` call which can't be translated can cause all rows from the table to be transferred from the database server, and the filter to be applied on the client.</span></span>
<span data-ttu-id="fd92e-120">如果資料表只包含幾個開發中的資料列，此情況可能很容易未被察覺；但當應用程式移至生產環境時，由於資料表可能包含數百萬個資料列，因此影響會很大。</span><span class="sxs-lookup"><span data-stu-id="fd92e-120">This situation can easily go undetected if the table contains only a few rows in development, but hit hard when the application moves to production, where the table may contain millions of rows.</span></span>
<span data-ttu-id="fd92e-121">用戶端評估警告也證明很容易在開發期間遭到忽略。</span><span class="sxs-lookup"><span data-stu-id="fd92e-121">Client evaluation warnings also proved too easy to ignore during development.</span></span>

<span data-ttu-id="fd92e-122">此外，自動用戶端評估可能會導致改善特定運算式的查詢轉譯造成版本間非預期的中斷性變更問題。</span><span class="sxs-lookup"><span data-stu-id="fd92e-122">Besides this, automatic client evaluation can lead to issues in which improving query translation for specific expressions caused unintended breaking changes between releases.</span></span>

**<span data-ttu-id="fd92e-123">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-123">Mitigations</span></span>**

<span data-ttu-id="fd92e-124">如果無法完整轉譯查詢，請以可轉譯的格式來重寫查詢，或是使用 `AsEnumerable()`、`ToList()` 或類似函數來明確將資料帶回用戶端，以便接著使用 LINQ-to-Objects 加以處理。</span><span class="sxs-lookup"><span data-stu-id="fd92e-124">If a query can't be fully translated, then either rewrite the query in a form that can be translated, or use `AsEnumerable()`, `ToList()`, or similar to explicitly bring data back to the client where it can then be further processed using LINQ-to-Objects.</span></span>

## <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a><span data-ttu-id="fd92e-125">Entity Framework Core 不再屬於 ASP.NET Core 共用架構</span><span class="sxs-lookup"><span data-stu-id="fd92e-125">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>

[<span data-ttu-id="fd92e-126">追蹤問題公告 #325</span><span class="sxs-lookup"><span data-stu-id="fd92e-126">Tracking Issue Announcements#325</span></span>](https://github.com/aspnet/Announcements/issues/325)

<span data-ttu-id="fd92e-127">這項變更已於 ASP.NET Core 3.0-preview 1 推出。</span><span class="sxs-lookup"><span data-stu-id="fd92e-127">This change was introduced in ASP.NET Core 3.0-preview 1.</span></span> 

**<span data-ttu-id="fd92e-128">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-128">Old behavior</span></span>**

<span data-ttu-id="fd92e-129">在 ASP.NET Core 3.0 以前，當您新增 `Microsoft.AspNetCore.App` 或 `Microsoft.AspNetCore.All` 的套件參考時，它會包含 EF Core 及部分 EF Core 資料提供者 (例如 SQL Server 提供者)。</span><span class="sxs-lookup"><span data-stu-id="fd92e-129">Before ASP.NET Core 3.0, when you added a package reference to `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All`, it would include EF Core and some of the EF Core data providers like the SQL Server provider.</span></span>

**<span data-ttu-id="fd92e-130">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-130">New behavior</span></span>**

<span data-ttu-id="fd92e-131">從 3.0 開始，ASP.NET Core 共用架構不會包含 EF Core 或任何 EF Core 資料提供者。</span><span class="sxs-lookup"><span data-stu-id="fd92e-131">Starting in 3.0, the ASP.NET Core shared framework doesn't include EF Core or any EF Core data providers.</span></span>

**<span data-ttu-id="fd92e-132">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-132">Why</span></span>**

<span data-ttu-id="fd92e-133">在這項變更之前，取得 EF Core 會根據應用程式是否以 ASP.NET Core 和 SQL Server 為目標而需要不同的步驟。</span><span class="sxs-lookup"><span data-stu-id="fd92e-133">Before this change, getting EF Core required different steps depending on whether the application targeted ASP.NET Core and SQL Server or not.</span></span> <span data-ttu-id="fd92e-134">此外，升級 ASP.NET Core 會強制升級 EF Core 和 SQL Server 提供者，這不一定符合需求。</span><span class="sxs-lookup"><span data-stu-id="fd92e-134">Also, upgrading ASP.NET Core forced the upgrade of EF Core and the SQL Server provider, which isn't always desirable.</span></span>

<span data-ttu-id="fd92e-135">透過這項變更，取得 EF Core 的體驗對所有提供者、支援的 .NET 實作和應用程式類型都相同。</span><span class="sxs-lookup"><span data-stu-id="fd92e-135">With this change, the experience of getting EF Core is the same across all providers, supported .NET implementations and application types.</span></span>
<span data-ttu-id="fd92e-136">開發人員現在也可以精確控制何時升級 EF Core 和 EF Core 資料提供者。</span><span class="sxs-lookup"><span data-stu-id="fd92e-136">Developers can also now control exactly when EF Core and EF Core data providers are upgraded.</span></span>

**<span data-ttu-id="fd92e-137">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-137">Mitigations</span></span>**

<span data-ttu-id="fd92e-138">若要在 ASP.NET Core 3.0 應用程式或其他支援的應用程式中使用 EF Core，請明確將套件參考加入應用程式會使用的 EF Core 資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="fd92e-138">To use EF Core in an ASP.NET Core 3.0 application or any other supported application, explicitly add a package reference to the EF Core database provider that your application will use.</span></span>

## <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a><span data-ttu-id="fd92e-139">FromSql、ExecuteSql 和 ExecuteSqlAsync 已重新命名</span><span class="sxs-lookup"><span data-stu-id="fd92e-139">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>

[<span data-ttu-id="fd92e-140">追蹤問題 #10996</span><span class="sxs-lookup"><span data-stu-id="fd92e-140">Tracking Issue #10996</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10996)

<span data-ttu-id="fd92e-141">這項變更已於 EF Core 3.0-preview 4 推出。</span><span class="sxs-lookup"><span data-stu-id="fd92e-141">This change was introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="fd92e-142">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-142">Old behavior</span></span>**

<span data-ttu-id="fd92e-143">在 EF Core 3.0 之前，這些方法名稱已多載以使用一般字串，或應插入至 SQL 和參數的字串。</span><span class="sxs-lookup"><span data-stu-id="fd92e-143">Before EF Core 3.0, these method names were overloaded to work with either a normal string or a string that should be interpolated into SQL and parameters.</span></span>

**<span data-ttu-id="fd92e-144">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-144">New behavior</span></span>**

<span data-ttu-id="fd92e-145">從 EF Core 3.0 開始，請使用 `FromSqlRaw`、`ExecuteSqlRaw` 和 `ExecuteSqlRawAsync` 建立參數化查詢，其中參數會分別從查詢字串傳遞。</span><span class="sxs-lookup"><span data-stu-id="fd92e-145">Starting with EF Core 3.0, use `FromSqlRaw`, `ExecuteSqlRaw`, and `ExecuteSqlRawAsync` to create a parameterized query where the parameters are passed separately from the query string.</span></span>
<span data-ttu-id="fd92e-146">例如：</span><span class="sxs-lookup"><span data-stu-id="fd92e-146">For example:</span></span>

```C#
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

<span data-ttu-id="fd92e-147">使用 `FromSqlInterpolated`、`ExecuteSqlInterpolated` 和 `ExecuteSqlInterpolatedAsync` 建立參數化查詢，其中參數會作為插入查詢字串的一部分傳回。</span><span class="sxs-lookup"><span data-stu-id="fd92e-147">Use `FromSqlInterpolated`, `ExecuteSqlInterpolated`, and `ExecuteSqlInterpolatedAsync` to create a parameterized query where the parameters are passed as part of an interpolated query string.</span></span>
<span data-ttu-id="fd92e-148">例如：</span><span class="sxs-lookup"><span data-stu-id="fd92e-148">For example:</span></span>

```C#
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

<span data-ttu-id="fd92e-149">請注意，上述兩個查詢都會產生具有相同 SQL 參數的相同參數化 SQL。</span><span class="sxs-lookup"><span data-stu-id="fd92e-149">Note that both of the queries above will produce the same parameterized SQL with the same SQL parameters.</span></span>

**<span data-ttu-id="fd92e-150">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-150">Why</span></span>**

<span data-ttu-id="fd92e-151">類似這類的方法多載，會在意圖呼叫插入字串方法時，容易不小心呼叫原始字串方法，反之亦然。</span><span class="sxs-lookup"><span data-stu-id="fd92e-151">Method overloads like this make it very easy to accidentally call the raw srting method when the intent was to call the interpolated string method, and the other way around.</span></span>
<span data-ttu-id="fd92e-152">這可能會導致查詢在應該參數化時不進行參數化。</span><span class="sxs-lookup"><span data-stu-id="fd92e-152">This could result in queries not being parameterized when they should have been.</span></span>

**<span data-ttu-id="fd92e-153">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-153">Mitigations</span></span>**

<span data-ttu-id="fd92e-154">切換至使用新的方法名稱。</span><span class="sxs-lookup"><span data-stu-id="fd92e-154">Switch to use the new method names.</span></span>

## <a name="query-execution-is-logged-at-debug-level"></a><span data-ttu-id="fd92e-155">查詢執行會在 Debug 層級記錄</span><span class="sxs-lookup"><span data-stu-id="fd92e-155">Query execution is logged at Debug level</span></span>

[<span data-ttu-id="fd92e-156">追蹤問題 #14523</span><span class="sxs-lookup"><span data-stu-id="fd92e-156">Tracking Issue #14523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

<span data-ttu-id="fd92e-157">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="fd92e-157">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="fd92e-158">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-158">Old behavior</span></span>**

<span data-ttu-id="fd92e-159">在 EF Core 3.0 以前，查詢和其他命令的執行會在 `Info` 層級記錄。</span><span class="sxs-lookup"><span data-stu-id="fd92e-159">Before EF Core 3.0, execution of queries and other commands was logged at the `Info` level.</span></span>

**<span data-ttu-id="fd92e-160">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-160">New behavior</span></span>**

<span data-ttu-id="fd92e-161">從 EF Core 3.0 開始，命令/SQL 執行的記錄是在 `Debug` 層級。</span><span class="sxs-lookup"><span data-stu-id="fd92e-161">Starting with EF Core 3.0, logging of command/SQL execution is at the `Debug` level.</span></span>

**<span data-ttu-id="fd92e-162">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-162">Why</span></span>**

<span data-ttu-id="fd92e-163">這項變更的目的是為了減少 `Info` 記錄層級的干擾。</span><span class="sxs-lookup"><span data-stu-id="fd92e-163">This change was made to reduce the noise at the `Info` log level.</span></span>

**<span data-ttu-id="fd92e-164">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-164">Mitigations</span></span>**

<span data-ttu-id="fd92e-165">此記錄事件是由 `RelationalEventId.CommandExecuting` 定義，事件識別碼為 20100。</span><span class="sxs-lookup"><span data-stu-id="fd92e-165">This logging event is defined by `RelationalEventId.CommandExecuting` with event ID 20100.</span></span>
<span data-ttu-id="fd92e-166">若要重新在 `Info` 層級記錄 SQL，請明確在 `OnConfiguring` 或 `AddDbContext` 中設定層級。</span><span class="sxs-lookup"><span data-stu-id="fd92e-166">To log SQL at the `Info` level again, explicitly configure the level in `OnConfiguring` or `AddDbContext`.</span></span>
<span data-ttu-id="fd92e-167">例如：</span><span class="sxs-lookup"><span data-stu-id="fd92e-167">For example:</span></span>
```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Info)));
```

## <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a><span data-ttu-id="fd92e-168">實體執行個體上不會再設定暫存索引鍵值</span><span class="sxs-lookup"><span data-stu-id="fd92e-168">Temporary key values are no longer set onto entity instances</span></span>

[<span data-ttu-id="fd92e-169">追蹤問題 #12378</span><span class="sxs-lookup"><span data-stu-id="fd92e-169">Tracking Issue #12378</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

<span data-ttu-id="fd92e-170">這項變更已於 EF Core 3.0-preview 2 推出。</span><span class="sxs-lookup"><span data-stu-id="fd92e-170">This change was introduced in EF Core 3.0-preview 2.</span></span>

**<span data-ttu-id="fd92e-171">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-171">Old behavior</span></span>**

<span data-ttu-id="fd92e-172">在 EF Core 3.0 以前，會對所有索引鍵屬性指派暫存值，這些屬性稍後會有資料庫產生的實值。</span><span class="sxs-lookup"><span data-stu-id="fd92e-172">Before EF Core 3.0, temporary values were assigned to all key properties that would later have a real value generated by the database.</span></span>
<span data-ttu-id="fd92e-173">這些暫存值通常是龐大的負值。</span><span class="sxs-lookup"><span data-stu-id="fd92e-173">Usually these temporary values were large negative numbers.</span></span>

**<span data-ttu-id="fd92e-174">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-174">New behavior</span></span>**

<span data-ttu-id="fd92e-175">從 3.0 開始，EF Core 會將暫存索引鍵值儲存為實體追蹤資訊的一部分，至於索引鍵屬性本身則保持不變。</span><span class="sxs-lookup"><span data-stu-id="fd92e-175">Starting with 3.0, EF Core stores the temporary key value as part of the entity's tracking information, and leaves the key property itself unchanged.</span></span>

**<span data-ttu-id="fd92e-176">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-176">Why</span></span>**

<span data-ttu-id="fd92e-177">這項變更的目的是為了防止在將某個 `DbContext` 執行個體先前追蹤的實體移至不同的 `DbContext` 執行個體時，錯誤地把暫存索引鍵值變成永久值。</span><span class="sxs-lookup"><span data-stu-id="fd92e-177">This change was made to prevent temporary key values from erroneously becoming permanent when an entity that has been previously tracked by some `DbContext` instance is moved to a different `DbContext` instance.</span></span> 

**<span data-ttu-id="fd92e-178">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-178">Mitigations</span></span>**

<span data-ttu-id="fd92e-179">若應用程式會將主索引鍵指派給外部索引鍵以形成實體間關聯，則可能會在主索引鍵是由存放區產生並屬於 `Added` 狀態的實體時採用舊行為。</span><span class="sxs-lookup"><span data-stu-id="fd92e-179">Applications that assign primary key values onto foreign keys to form associations between entities may depend on the old behavior if the primary keys are store-generated and belong to entities in the `Added` state.</span></span>
<span data-ttu-id="fd92e-180">這可透過下列方式來避免：</span><span class="sxs-lookup"><span data-stu-id="fd92e-180">This can be avoided by:</span></span>
* <span data-ttu-id="fd92e-181">不使用存放區產生的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="fd92e-181">Not using store-generated keys.</span></span>
* <span data-ttu-id="fd92e-182">設定導覽屬性以形成關聯性，而不是設定外部索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="fd92e-182">Setting navigation properties to form relationships instead of setting foreign key values.</span></span>
* <span data-ttu-id="fd92e-183">從實體的追蹤資訊取得實際暫存索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="fd92e-183">Obtain the actual temporary key values from the entity's tracking information.</span></span>
<span data-ttu-id="fd92e-184">例如，`context.Entry(blog).Property(e => e.Id).CurrentValue` 會傳回暫存值，即使尚未設定 `blog.Id` 本身也一樣。</span><span class="sxs-lookup"><span data-stu-id="fd92e-184">For example, `context.Entry(blog).Property(e => e.Id).CurrentValue` will return the temporary value even though `blog.Id` itself hasn't been set.</span></span>

## <a name="detectchanges-honors-store-generated-key-values"></a><span data-ttu-id="fd92e-185">DetectChanges 接受存放區產生的索引鍵值</span><span class="sxs-lookup"><span data-stu-id="fd92e-185">DetectChanges honors store-generated key values</span></span>

[<span data-ttu-id="fd92e-186">追蹤問題 #14616</span><span class="sxs-lookup"><span data-stu-id="fd92e-186">Tracking Issue #14616</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

<span data-ttu-id="fd92e-187">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="fd92e-187">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="fd92e-188">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-188">Old behavior</span></span>**

<span data-ttu-id="fd92e-189">在 EF Core 3.0 以前，`DetectChanges` 所發現未被追蹤的實體會以 `Added` 狀態追蹤，並在呼叫 `SaveChanges` 時以新的資料列插入。</span><span class="sxs-lookup"><span data-stu-id="fd92e-189">Before EF Core 3.0, an untracked entity found by `DetectChanges` would be tracked in the `Added` state and inserted as a new row when `SaveChanges` is called.</span></span>

**<span data-ttu-id="fd92e-190">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-190">New behavior</span></span>**

<span data-ttu-id="fd92e-191">從 EF Core 3.0 開始，如果實體使用產生的索引鍵值並已設定一些索引鍵值，則實體會以 `Modified` 狀態追蹤。</span><span class="sxs-lookup"><span data-stu-id="fd92e-191">Starting with EF Core 3.0, if an entity is using generated key values and some key value is set, then the entity will be tracked in the `Modified` state.</span></span>
<span data-ttu-id="fd92e-192">這表示實體的資料列假設存在，而且會在呼叫 `SaveChanges` 時更新。</span><span class="sxs-lookup"><span data-stu-id="fd92e-192">This means that a row for the entity is assumed to exist and it will be updated when `SaveChanges` is called.</span></span>
<span data-ttu-id="fd92e-193">如果未設定索引鍵值，或者如果實體類型未使用產生的索引鍵，則新的實體仍會如同舊版以 `Added` 追蹤。</span><span class="sxs-lookup"><span data-stu-id="fd92e-193">If the key value isn't set, or if the entity type isn't using generated keys, then the new entity will still be tracked as `Added` as in previous versions.</span></span>

**<span data-ttu-id="fd92e-194">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-194">Why</span></span>**

<span data-ttu-id="fd92e-195">這項變更的目的是為了更輕鬆一致地使用中斷連接的實體圖形，同時使用存放區產生的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="fd92e-195">This change was made to make it easier and more consistent to work with disconnected entity graphs while using store-generated keys.</span></span>

**<span data-ttu-id="fd92e-196">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-196">Mitigations</span></span>**

<span data-ttu-id="fd92e-197">如果實體類型已設定為使用產生的索引鍵，但針對新的執行個體明確設定了索引鍵值，這項變更可能會中斷應用程式。</span><span class="sxs-lookup"><span data-stu-id="fd92e-197">This change can break an application if an entity type is configured to use generated keys but key values are explicitly set for new instances.</span></span>
<span data-ttu-id="fd92e-198">修正方法是明確設定索引鍵屬性不使用產生的值。</span><span class="sxs-lookup"><span data-stu-id="fd92e-198">The fix is to explicitly configure the key properties to not use generated values.</span></span>
<span data-ttu-id="fd92e-199">例如，使用 Fluent API：</span><span class="sxs-lookup"><span data-stu-id="fd92e-199">For example, with the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

<span data-ttu-id="fd92e-200">或者，使用資料註解：</span><span class="sxs-lookup"><span data-stu-id="fd92e-200">Or with data annotations:</span></span>

```C#
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```

## <a name="cascade-deletions-now-happen-immediately-by-default"></a><span data-ttu-id="fd92e-201">現在預設會立即發生串聯刪除</span><span class="sxs-lookup"><span data-stu-id="fd92e-201">Cascade deletions now happen immediately by default</span></span>

[<span data-ttu-id="fd92e-202">追蹤問題 #10114</span><span class="sxs-lookup"><span data-stu-id="fd92e-202">Tracking Issue #10114</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

<span data-ttu-id="fd92e-203">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="fd92e-203">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="fd92e-204">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-204">Old behavior</span></span>**

<span data-ttu-id="fd92e-205">在 3.0 以前，除非呼叫 SaveChanges，否則 EF Core 不會套用串聯動作 (刪除必要主體或提供必要主體關聯性時刪除相依實體)。</span><span class="sxs-lookup"><span data-stu-id="fd92e-205">Before 3.0, EF Core applied cascading actions (deleting dependent entities when a required principal is deleted or when the relationship to a required principal is severed) did not happen until SaveChanges was called.</span></span>

**<span data-ttu-id="fd92e-206">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-206">New behavior</span></span>**

<span data-ttu-id="fd92e-207">從 3.0 開始，EF Core 會在偵測到觸發條件時立即套用串聯動作。</span><span class="sxs-lookup"><span data-stu-id="fd92e-207">Starting with 3.0, EF Core applies cascading actions as soon as the triggering condition is detected.</span></span>
<span data-ttu-id="fd92e-208">例如，呼叫 `context.Remove()` 刪除主要實體會導致所有追蹤的相關必要相依項目也會立即設定為 `Deleted`。</span><span class="sxs-lookup"><span data-stu-id="fd92e-208">For example, calling `context.Remove()` to delete a principal entity will result in all tracked related required dependents also being set to `Deleted` immediately.</span></span>

**<span data-ttu-id="fd92e-209">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-209">Why</span></span>**

<span data-ttu-id="fd92e-210">這項變更的目的是為了改善資料繫結和稽核情節的體驗，在這些情節中了解呼叫 `SaveChanges`「之前」將刪除哪些實體是很重要的。</span><span class="sxs-lookup"><span data-stu-id="fd92e-210">This change was made to improve the experience for data binding and auditing scenarios where it is important to understand which entities will be deleted _before_ `SaveChanges` is called.</span></span>

**<span data-ttu-id="fd92e-211">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-211">Mitigations</span></span>**

<span data-ttu-id="fd92e-212">透過設定 `context.ChangedTracker` 可以還原舊行為。</span><span class="sxs-lookup"><span data-stu-id="fd92e-212">The previous behavior can be restored through settings on `context.ChangedTracker`.</span></span>
<span data-ttu-id="fd92e-213">例如：</span><span class="sxs-lookup"><span data-stu-id="fd92e-213">For example:</span></span>

```C#
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```

## <a name="query-types-are-consolidated-with-entity-types"></a><span data-ttu-id="fd92e-214">查詢類型會與實體類型合併</span><span class="sxs-lookup"><span data-stu-id="fd92e-214">Query types are consolidated with entity types</span></span>

[<span data-ttu-id="fd92e-215">追蹤問題 #14194</span><span class="sxs-lookup"><span data-stu-id="fd92e-215">Tracking Issue #14194</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

<span data-ttu-id="fd92e-216">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="fd92e-216">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="fd92e-217">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-217">Old behavior</span></span>**

<span data-ttu-id="fd92e-218">在 EF Core 3.0 以前，[查詢類型](xref:core/modeling/query-types)可讓您查詢未以結構化方式定義主索引鍵的資料。</span><span class="sxs-lookup"><span data-stu-id="fd92e-218">Before EF Core 3.0, [query types](xref:core/modeling/query-types) were a means to query data that doesn't define a primary key in a structured way.</span></span>
<span data-ttu-id="fd92e-219">換句話說，查詢類型是用於對應沒有索引鍵的實體類型 (較有可能來自檢視，但也有可能來自資料表)，而一般實體類型是用於索引鍵可供使用時 (較有可能來自資料表，但也有可能來自檢視)。</span><span class="sxs-lookup"><span data-stu-id="fd92e-219">That is, a query type was used for mapping entity types without keys (more likely from a view, but possibly from a table) while a regular entity type was used when a key was available (more likely from a table, but possibly from a view).</span></span>

**<span data-ttu-id="fd92e-220">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-220">New behavior</span></span>**

<span data-ttu-id="fd92e-221">查詢類型現在會成為沒有主索引鍵的實體類型。</span><span class="sxs-lookup"><span data-stu-id="fd92e-221">A query type now becomes just an entity type without a primary key.</span></span>
<span data-ttu-id="fd92e-222">無索引鍵的實體類型功能與舊版查詢類型相同。</span><span class="sxs-lookup"><span data-stu-id="fd92e-222">Keyless entity types have the same functionality as query types in previous versions.</span></span>

**<span data-ttu-id="fd92e-223">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-223">Why</span></span>**

<span data-ttu-id="fd92e-224">這項變更的目的是為了降低查詢類型用途的混淆。</span><span class="sxs-lookup"><span data-stu-id="fd92e-224">This change was made to reduce the confusion around the purpose of query types.</span></span>
<span data-ttu-id="fd92e-225">具體來說，它們是無索引鍵的實體類型，因此本質上是唯讀的，但不應該只因為實體類型必須是唯讀就使用。</span><span class="sxs-lookup"><span data-stu-id="fd92e-225">Specifically, they are keyless entity types and they are inherently read-only because of this, but they should not be used just because an entity type needs to be read-only.</span></span>
<span data-ttu-id="fd92e-226">同樣地，它們通常會對應至檢視，但這只是因為檢視通常未定義索引鍵。</span><span class="sxs-lookup"><span data-stu-id="fd92e-226">Likewise, they are often mapped to views, but this is only because views often don't define keys.</span></span>

**<span data-ttu-id="fd92e-227">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-227">Mitigations</span></span>**

<span data-ttu-id="fd92e-228">API 的下列組件現已淘汰：</span><span class="sxs-lookup"><span data-stu-id="fd92e-228">The following parts of the API are now obsolete:</span></span>
* <span data-ttu-id="fd92e-229">**`ModelBuilder.Query<>()`** - 必須改為呼叫 `ModelBuilder.Entity<>().HasNoKey()` 將實體類型標示為沒有索引鍵。</span><span class="sxs-lookup"><span data-stu-id="fd92e-229">**`ModelBuilder.Query<>()`** - Instead `ModelBuilder.Entity<>().HasNoKey()` needs to be called to mark an entity type as having no keys.</span></span>
<span data-ttu-id="fd92e-230">為了避免在必須有主索引鍵但不符合慣例時設定錯誤，目前仍未將此設定為慣例。</span><span class="sxs-lookup"><span data-stu-id="fd92e-230">This would still not be configured by convention to avoid misconfiguration when a primary key is expected, but doesn't match the convention.</span></span>
* <span data-ttu-id="fd92e-231">**`DbQuery<>`** - 應改用 `DbSet<>`。</span><span class="sxs-lookup"><span data-stu-id="fd92e-231">**`DbQuery<>`** - Instead `DbSet<>` should be used.</span></span>
* <span data-ttu-id="fd92e-232">**`DbContext.Query<>()`** - 應改用 `DbContext.Set<>()`。</span><span class="sxs-lookup"><span data-stu-id="fd92e-232">**`DbContext.Query<>()`** - Instead `DbContext.Set<>()` should be used.</span></span>

## <a name="configuration-api-for-owned-type-relationships-has-changed"></a><span data-ttu-id="fd92e-233">自有類型關聯性的設定 API 已變更</span><span class="sxs-lookup"><span data-stu-id="fd92e-233">Configuration API for owned type relationships has changed</span></span>

<span data-ttu-id="fd92e-234">[追蹤問題 #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[追蹤問題 #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[追蹤問題 #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span><span class="sxs-lookup"><span data-stu-id="fd92e-234">[Tracking Issue #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[Tracking Issue #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[Tracking Issue #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span></span>

<span data-ttu-id="fd92e-235">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="fd92e-235">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="fd92e-236">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-236">Old behavior</span></span>**

<span data-ttu-id="fd92e-237">在 EF Core 3.0 以前，會在呼叫 `OwnsOne` 或 `OwnsMany` 之後直接執行自有關聯性的設定。</span><span class="sxs-lookup"><span data-stu-id="fd92e-237">Before EF Core 3.0, configuration of the owned relationship was performed directly after the `OwnsOne` or `OwnsMany` call.</span></span> 

**<span data-ttu-id="fd92e-238">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-238">New behavior</span></span>**

<span data-ttu-id="fd92e-239">從 EF Core 3.0 開始，現在會有 Fluent API 使用 `WithOwner()` 將導覽屬性設定為擁有者。</span><span class="sxs-lookup"><span data-stu-id="fd92e-239">Starting with EF Core 3.0, there is now fluent API to configure a navigation property to the owner using `WithOwner()`.</span></span>
<span data-ttu-id="fd92e-240">例如：</span><span class="sxs-lookup"><span data-stu-id="fd92e-240">For example:</span></span>

```C#
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

<span data-ttu-id="fd92e-241">擁有者與自有之間關聯性的相關設定現在應該在 `WithOwner()` 之後鏈結，類似於其他關聯性的設定方式。</span><span class="sxs-lookup"><span data-stu-id="fd92e-241">The configuration related to the relationship between owner and owned should now be chained after `WithOwner()` similarly to how other relationships are configured.</span></span>
<span data-ttu-id="fd92e-242">但自有類型本身的設定仍會在 `OwnsOne()/OwnsMany()` 之後鏈結。</span><span class="sxs-lookup"><span data-stu-id="fd92e-242">While the configuration for the owned type itself would still be chained after `OwnsOne()/OwnsMany()`.</span></span>
<span data-ttu-id="fd92e-243">例如：</span><span class="sxs-lookup"><span data-stu-id="fd92e-243">For example:</span></span>

```C#
modelBuilder.Entity<Order>.OwnsOne(e => e.Details, eb =>
    {
        eb.WithOwner()
            .HasForeignKey(e => e.AlternateId)
            .HasConstraintName("FK_OrderDetails");
            
        eb.ToTable("OrderDetails");
        eb.HasKey(e => e.AlternateId);
        eb.HasIndex(e => e.Id);

        eb.HasOne(e => e.Customer).WithOne();

        eb.HasData(
            new OrderDetails
            {
                AlternateId = 1,
                Id = -1
            });
    });
```

<span data-ttu-id="fd92e-244">此外，使用自有類型目標呼叫 `Entity()`、`HasOne()` 或 `Set()` 現在會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="fd92e-244">Additionally calling `Entity()`, `HasOne()`, or `Set()` with an owned type target will now throw an exception.</span></span>

**<span data-ttu-id="fd92e-245">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-245">Why</span></span>**

<span data-ttu-id="fd92e-246">這項變更的目的是為了更清楚地劃分設定自有類型本身，以及設定自有類型的「關聯性」。</span><span class="sxs-lookup"><span data-stu-id="fd92e-246">This change was made to create a cleaner separation between configuring the owned type itself and the _relationship to_ the owned type.</span></span>
<span data-ttu-id="fd92e-247">如此可避免 `HasForeignKey` 等方法的模稜兩可和混淆。</span><span class="sxs-lookup"><span data-stu-id="fd92e-247">This in turn removes ambiguity and confusion around methods like `HasForeignKey`.</span></span>

**<span data-ttu-id="fd92e-248">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-248">Mitigations</span></span>**

<span data-ttu-id="fd92e-249">將自有類型關聯性的設定變更為使用新的 API 介面，如上述範例所示。</span><span class="sxs-lookup"><span data-stu-id="fd92e-249">Change configuration of owned type relationships to use the new API surface as shown in the example above.</span></span>

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="fd92e-250">現在可選用以主體來共用資料表的相依實體</span><span class="sxs-lookup"><span data-stu-id="fd92e-250">Dependent entities sharing the table with the principal are now optional</span></span>

[<span data-ttu-id="fd92e-251">追蹤問題 #9005</span><span class="sxs-lookup"><span data-stu-id="fd92e-251">Tracking Issue #9005</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9005)

<span data-ttu-id="fd92e-252">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="fd92e-252">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="fd92e-253">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-253">Old behavior</span></span>**

<span data-ttu-id="fd92e-254">請考慮下列模型：</span><span class="sxs-lookup"><span data-stu-id="fd92e-254">Consider the following model:</span></span>
```C#
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```
<span data-ttu-id="fd92e-255">在 EF Core 3.0 之前，如果 `OrderDetails` 由 `Order` 擁有，或明確對應至相同的資料表，則在新增新的 `Order` 時一律需要 `OrderDetails` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="fd92e-255">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then an `OrderDetails` instance was always required when adding a new `Order`.</span></span>


**<span data-ttu-id="fd92e-256">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-256">New behavior</span></span>**

<span data-ttu-id="fd92e-257">從 3.0 開始，EF 允許新增 `Order` 而不需要 `OrderDetails`，並會對應所有 `OrderDetails` 屬性，除了可為 Null 之資料行的主索引鍵以外。</span><span class="sxs-lookup"><span data-stu-id="fd92e-257">Starting with 3.0, EF Core allows to add an `Order` without an `OrderDetails` and maps all of the `OrderDetails` properties except the primary key to nullable columns.</span></span>
<span data-ttu-id="fd92e-258">查詢時，如果任何必要的屬性不具有值，或如果其具有主索引鍵以外的不必要屬性，且所有屬性都是 `null`，則 EF Core 會將 `OrderDetails` 設為 `null`。</span><span class="sxs-lookup"><span data-stu-id="fd92e-258">When querying EF Core sets `OrderDetails` to `null` if any of its required properties doesn't have a value or if it has no required properties besides the primary key and all properties are `null`.</span></span>

**<span data-ttu-id="fd92e-259">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-259">Mitigations</span></span>**

<span data-ttu-id="fd92e-260">如果您的模型具有與所有選擇性資料行共用相依資料表，但指向該資料表的導覽不預期為 `null`，則應修改應用程式，以處理當導覽為 `null` 時的情況。</span><span class="sxs-lookup"><span data-stu-id="fd92e-260">If your model has a table sharing dependent with all optional columns, but the navigation pointing to it is not expected to be `null` then the application should be modified to handle cases when the navigation is `null`.</span></span> <span data-ttu-id="fd92e-261">如果這不可行，則應將必要屬性新增至實體類型，或至少應有一個屬性指派其非 `null` 的值。</span><span class="sxs-lookup"><span data-stu-id="fd92e-261">If this is not possible a required property should be added to the entity type or at least one property should have a non-`null` value assigned to it.</span></span>

## <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a><span data-ttu-id="fd92e-262">共用具有並行語彙基元資料行的所有實體，都必須將其對應至屬性</span><span class="sxs-lookup"><span data-stu-id="fd92e-262">All entities sharing a table with a concurrency token column have to map it to a property</span></span>

[<span data-ttu-id="fd92e-263">追蹤問題 #14154</span><span class="sxs-lookup"><span data-stu-id="fd92e-263">Tracking Issue #14154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14154)

<span data-ttu-id="fd92e-264">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="fd92e-264">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="fd92e-265">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-265">Old behavior</span></span>**

<span data-ttu-id="fd92e-266">請考慮下列模型：</span><span class="sxs-lookup"><span data-stu-id="fd92e-266">Consider the following model:</span></span>
```C#
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public byte[] Version { get; set; }
    public OrderDetails Details { get; set; }
}

public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Order>()
        .Property(o => o.Version).IsRowVersion().HasColumnName("Version");
}
```
<span data-ttu-id="fd92e-267">在 EF Core 3.0之前，如果 `OrderDetails` 由 `Order` 擁有，或明確對應至相同資料表，那麼僅更新 `OrderDetails` 將不會更新用戶端上的 `Version` 值，且下一次更新將會失敗。</span><span class="sxs-lookup"><span data-stu-id="fd92e-267">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then updating just `OrderDetails` will not update `Version` value on client and the next update will fail.</span></span>


**<span data-ttu-id="fd92e-268">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-268">New behavior</span></span>**

<span data-ttu-id="fd92e-269">從 3.0 開始，EF Core 會將新的 `Version` 值傳播至 `Order` (如果其擁有 `OrderDetails`)。</span><span class="sxs-lookup"><span data-stu-id="fd92e-269">Starting with 3.0, EF Core propagates the new `Version` value to `Order` if it owns `OrderDetails`.</span></span> <span data-ttu-id="fd92e-270">否則，在模型驗證期間會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="fd92e-270">Otherwise an exception is thrown during model validation.</span></span>

**<span data-ttu-id="fd92e-271">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-271">Why</span></span>**

<span data-ttu-id="fd92e-272">這項變更的目的，是為了避免在僅更新對應至相同資料表的其中一個實體時，出現過時的並行語彙基元值。</span><span class="sxs-lookup"><span data-stu-id="fd92e-272">This change was made to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

**<span data-ttu-id="fd92e-273">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-273">Mitigations</span></span>**

<span data-ttu-id="fd92e-274">共用資料表的所有實體，都必須包含對應至並行語彙基元資料行的屬性。</span><span class="sxs-lookup"><span data-stu-id="fd92e-274">All entities sharing the table have to include a property that is mapped to the concurrency token column.</span></span> <span data-ttu-id="fd92e-275">在陰影狀態中建立一個是可能的：</span><span class="sxs-lookup"><span data-stu-id="fd92e-275">It's possible the create one in shadow-state:</span></span>
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

## <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a><span data-ttu-id="fd92e-276">未對應類型的繼承屬性，現在會對應至所有衍生類型的單一資料行</span><span class="sxs-lookup"><span data-stu-id="fd92e-276">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>

[<span data-ttu-id="fd92e-277">追蹤問題 #13998</span><span class="sxs-lookup"><span data-stu-id="fd92e-277">Tracking Issue #13998</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13998)

<span data-ttu-id="fd92e-278">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="fd92e-278">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="fd92e-279">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-279">Old behavior</span></span>**

<span data-ttu-id="fd92e-280">請考慮下列模型：</span><span class="sxs-lookup"><span data-stu-id="fd92e-280">Consider the following model:</span></span>
```C#
public abstract class EntityBase
{
    public int Id { get; set; }
}

public abstract class OrderBase : EntityBase
{
    public int ShippingAddress { get; set; }
}

public class BulkOrder : OrderBase
{
}

public class Order : OrderBase
{
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Ignore<OrderBase>();
    modelBuilder.Entity<EntityBase>();
    modelBuilder.Entity<BulkOrder>();
    modelBuilder.Entity<Order>();
}
```

<span data-ttu-id="fd92e-281">在 EF Core 3.0 之前，`ShippingAddress` 屬性會根據預設，為 `BulkOrder` 和 `Order` 對應至個別資料行。</span><span class="sxs-lookup"><span data-stu-id="fd92e-281">Before EF Core 3.0, the `ShippingAddress` property would be mapped to separate columns for `BulkOrder` and `Order` by default.</span></span>

**<span data-ttu-id="fd92e-282">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-282">New behavior</span></span>**

<span data-ttu-id="fd92e-283">從 3.0 開始，EF Core 只會為 `ShippingAddress` 建立一個資料行。</span><span class="sxs-lookup"><span data-stu-id="fd92e-283">Starting with 3.0, EF Core only creates one column for `ShippingAddress`.</span></span>

**<span data-ttu-id="fd92e-284">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-284">Why</span></span>**

<span data-ttu-id="fd92e-285">舊行為是非預期的。</span><span class="sxs-lookup"><span data-stu-id="fd92e-285">The old behavoir was unexpected.</span></span>

**<span data-ttu-id="fd92e-286">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-286">Mitigations</span></span>**

<span data-ttu-id="fd92e-287">屬性仍可以在衍生類型上明確對應至個別資料行：</span><span class="sxs-lookup"><span data-stu-id="fd92e-287">The property can still be explicitly mapped to separate column on the derived types:</span></span>

```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Ignore<OrderBase>();
    modelBuilder.Entity<EntityBase>();
    modelBuilder.Entity<BulkOrder>()
        .Property(o => o.ShippingAddress).HasColumnName("BulkShippingAddress");
    modelBuilder.Entity<Order>()
        .Property(o => o.ShippingAddress).HasColumnName("ShippingAddress");
}
```

## <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a><span data-ttu-id="fd92e-288">外部索引鍵屬性慣例不會再比對與主體屬性相同的名稱</span><span class="sxs-lookup"><span data-stu-id="fd92e-288">The foreign key property convention no longer matches same name as the principal property</span></span>

[<span data-ttu-id="fd92e-289">追蹤問題 #13274</span><span class="sxs-lookup"><span data-stu-id="fd92e-289">Tracking Issue #13274</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

<span data-ttu-id="fd92e-290">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="fd92e-290">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="fd92e-291">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-291">Old behavior</span></span>**

<span data-ttu-id="fd92e-292">請考慮下列模型：</span><span class="sxs-lookup"><span data-stu-id="fd92e-292">Consider the following model:</span></span>
```C#
public class Customer
{
    public int CustomerId { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```
<span data-ttu-id="fd92e-293">在 EF Core 3.0 以前，`CustomerId` 屬性依照慣例會用於外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="fd92e-293">Before EF Core 3.0, the `CustomerId` property would be used for the foreign key by convention.</span></span>
<span data-ttu-id="fd92e-294">不過，如果 `Order` 是自有類型，則這也會將 `CustomerId` 設為主索引鍵，而這通常不符合預期。</span><span class="sxs-lookup"><span data-stu-id="fd92e-294">However, if `Order` is an owned type, then this would also make `CustomerId` the primary key and this isn't usually the expectation.</span></span>

**<span data-ttu-id="fd92e-295">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-295">New behavior</span></span>**

<span data-ttu-id="fd92e-296">從 3.0 開始，如果屬性的名稱與主體屬性相同，依照慣例，EF Core 不會嘗試將屬性用於外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="fd92e-296">Starting with 3.0, EF Core doesn't try to use properties for foreign keys by convention if they have the same name as the principal property.</span></span>
<span data-ttu-id="fd92e-297">但仍會比對與主體屬性名稱串連的主體類型名稱，以及與主體屬性名稱模式串連的導覽名稱。</span><span class="sxs-lookup"><span data-stu-id="fd92e-297">Principal type name concatenated with principal property name, and navigation name concatenated with principal property name patterns are still matched.</span></span>
<span data-ttu-id="fd92e-298">例如：</span><span class="sxs-lookup"><span data-stu-id="fd92e-298">For example:</span></span>

```C#
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```

```C#
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int BuyerId { get; set; }
    public Customer Buyer { get; set; }
}
```

**<span data-ttu-id="fd92e-299">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-299">Why</span></span>**

<span data-ttu-id="fd92e-300">這項變更的目的是為了避免錯誤地在自有類型上定義主索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="fd92e-300">This change was made to avoid erroneously defining a primary key property on the owned type.</span></span>

**<span data-ttu-id="fd92e-301">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-301">Mitigations</span></span>**

<span data-ttu-id="fd92e-302">如果屬性預定會作為外部索引鍵，並因此成為主索引鍵的一部分，請明確進行這類設定。</span><span class="sxs-lookup"><span data-stu-id="fd92e-302">If the property was intended to be the foreign key, and hence part of the primary key, then explicitly configure it as such.</span></span>

## <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a><span data-ttu-id="fd92e-303">如果在 TransactionScope 完成之前未再使用，則資料庫連線現在已關閉</span><span class="sxs-lookup"><span data-stu-id="fd92e-303">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>

[<span data-ttu-id="fd92e-304">追蹤問題 #14218</span><span class="sxs-lookup"><span data-stu-id="fd92e-304">Tracking Issue #14218</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14218)

<span data-ttu-id="fd92e-305">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="fd92e-305">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="fd92e-306">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-306">Old behavior</span></span>**

<span data-ttu-id="fd92e-307">在 EF Core 3.0 之前，如果內容在 `TransactionScope` 內開啟連線，則當目前 `TransactionScope` 處於作用中時，連線將保持開啟。</span><span class="sxs-lookup"><span data-stu-id="fd92e-307">Before EF Core 3.0, if the context opens the connection inside a `TransactionScope`, the connection remains open while the current `TransactionScope` is active.</span></span>

```C#
using (new TransactionScope())
{
    using (AdventureWorks context = new AdventureWorks())
    {
        context.ProductCategories.Add(new ProductCategory());
        context.SaveChanges();

        // Old behavior: Connection is still open at this point
        
        var categories = context.ProductCategories().ToList();
    }
}
```

**<span data-ttu-id="fd92e-308">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-308">New behavior</span></span>**

<span data-ttu-id="fd92e-309">從 3.0 開始，EF Core 在使用完連線後會將其關閉。</span><span class="sxs-lookup"><span data-stu-id="fd92e-309">Starting with 3.0, EF Core closes the connection as soon as it's done using it.</span></span>

**<span data-ttu-id="fd92e-310">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-310">Why</span></span>**

<span data-ttu-id="fd92e-311">此變更允許在相同 `TransactionScope` 中使用多個內容。</span><span class="sxs-lookup"><span data-stu-id="fd92e-311">This change allows to use multiple contexts in the same `TransactionScope`.</span></span> <span data-ttu-id="fd92e-312">新行為也與 EF6 相符。</span><span class="sxs-lookup"><span data-stu-id="fd92e-312">The new behavior alose matches EF6.</span></span>

**<span data-ttu-id="fd92e-313">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-313">Mitigations</span></span>**

<span data-ttu-id="fd92e-314">如果連線需要保持開啟，則明確呼叫 `OpenConnection()` 可確保 EF Core 不會過早將其關閉：</span><span class="sxs-lookup"><span data-stu-id="fd92e-314">If the connection needs to remain open explicit call to `OpenConnection()` will ensure that EF Core doesn't close it prematurely:</span></span>

```C#
using (new TransactionScope())
{
    using (AdventureWorks context = new AdventureWorks())
    {
        context.Database.OpenConnection();
        context.ProductCategories.Add(new ProductCategory());
        context.SaveChanges();
        
        var categories = context.ProductCategories().ToList();
        context.Database.CloseConnection();
    }
}
```

## <a name="each-property-uses-independent-in-memory-integer-key-generation"></a><span data-ttu-id="fd92e-315">每個屬性會使用獨立的記憶體內部整數索引鍵產生</span><span class="sxs-lookup"><span data-stu-id="fd92e-315">Each property uses independent in-memory integer key generation</span></span>

[<span data-ttu-id="fd92e-316">追蹤問題 #6872</span><span class="sxs-lookup"><span data-stu-id="fd92e-316">Tracking Issue #6872</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

<span data-ttu-id="fd92e-317">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="fd92e-317">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="fd92e-318">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-318">Old behavior</span></span>**

<span data-ttu-id="fd92e-319">在 EF Core 3.0 以前，會針對所有記憶體內部整數索引鍵屬性使用一個共用值產生器。</span><span class="sxs-lookup"><span data-stu-id="fd92e-319">Before EF Core 3.0, one shared value generator was used for all in-memory integer key properties.</span></span>

**<span data-ttu-id="fd92e-320">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-320">New behavior</span></span>**

<span data-ttu-id="fd92e-321">從 EF Core 3.0 開始，當使用記憶體內部資料庫時，每個整數索引鍵屬性都會取得自己的值產生器。</span><span class="sxs-lookup"><span data-stu-id="fd92e-321">Starting with EF Core 3.0, each integer key property gets its own value generator when using the in-memory database.</span></span>
<span data-ttu-id="fd92e-322">此外，如果已刪除資料庫，則會重設所有資料表的索引鍵產生。</span><span class="sxs-lookup"><span data-stu-id="fd92e-322">Also, if the database is deleted, then key generation is reset for all tables.</span></span>

**<span data-ttu-id="fd92e-323">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-323">Why</span></span>**

<span data-ttu-id="fd92e-324">這項變更的目的是為了讓記憶體內部索引鍵產生與實際資料庫索引鍵產生更加一致，並改善在使用記憶體內部資料庫時隔離個別測試的能力。</span><span class="sxs-lookup"><span data-stu-id="fd92e-324">This change was made to align in-memory key generation more closely to real database key generation and to improve the ability to isolate tests from each other when using the in-memory database.</span></span>

**<span data-ttu-id="fd92e-325">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-325">Mitigations</span></span>**

<span data-ttu-id="fd92e-326">這可能會中斷需要設定特定記憶體內部索引鍵值的應用程式。</span><span class="sxs-lookup"><span data-stu-id="fd92e-326">This can break an application that is relying on specific in-memory key values to be set.</span></span>
<span data-ttu-id="fd92e-327">請考慮改為不依賴特定索引鍵值，或更新以符合新行為。</span><span class="sxs-lookup"><span data-stu-id="fd92e-327">Consider instead not relying on specific key values, or updating to match the new behavior.</span></span>

## <a name="backing-fields-are-used-by-default"></a><span data-ttu-id="fd92e-328">預設會使用支援欄位</span><span class="sxs-lookup"><span data-stu-id="fd92e-328">Backing fields are used by default</span></span>

[<span data-ttu-id="fd92e-329">追蹤問題 #12430</span><span class="sxs-lookup"><span data-stu-id="fd92e-329">Tracking Issue #12430</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

<span data-ttu-id="fd92e-330">這項變更已於 EF Core 3.0-preview 2 推出。</span><span class="sxs-lookup"><span data-stu-id="fd92e-330">This change was introduced in EF Core 3.0-preview 2.</span></span>

**<span data-ttu-id="fd92e-331">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-331">Old behavior</span></span>**

<span data-ttu-id="fd92e-332">在 3.0 以前，即使屬性的支援欄位已知，EF Core 預設仍會使用屬性 getter 和 setter 方法來讀取和寫入屬性值。</span><span class="sxs-lookup"><span data-stu-id="fd92e-332">Before 3.0, even if the backing field for a property was known, EF Core would still by default read and write the property value using the property getter and setter methods.</span></span>
<span data-ttu-id="fd92e-333">例外是查詢執行，其中如果支援欄位已知，則會直接設定。</span><span class="sxs-lookup"><span data-stu-id="fd92e-333">The exception to this was query execution, where the backing field would be set directly if known.</span></span>

**<span data-ttu-id="fd92e-334">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-334">New behavior</span></span>**

<span data-ttu-id="fd92e-335">從 EF Core 3.0 開始，如果屬性的支援欄位已知，則一律會使用支援欄位來讀取和寫入該屬性。</span><span class="sxs-lookup"><span data-stu-id="fd92e-335">Starting with EF Core 3.0, if the backing field for a property is known, then will always read and write that property using the backing field.</span></span>
<span data-ttu-id="fd92e-336">如果應用程式需要將額外的行為編碼到 getter 或 setter 方法中，這可能會導致應用程式中斷。</span><span class="sxs-lookup"><span data-stu-id="fd92e-336">This could cause an application break if the application is relying on additional behavior coded into the getter or setter methods.</span></span>

**<span data-ttu-id="fd92e-337">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-337">Why</span></span>**

<span data-ttu-id="fd92e-338">這項變更的目的是為了防止 EF Core 預設在執行涉及實體的資料庫作業時，錯誤地觸發商務邏輯。</span><span class="sxs-lookup"><span data-stu-id="fd92e-338">This change was made to prevent EF Core from erroneously triggering business logic by default when performing database operations involving the entities.</span></span>

**<span data-ttu-id="fd92e-339">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-339">Mitigations</span></span>**

<span data-ttu-id="fd92e-340">透過在 modelBuilder Fluent API 中設定屬性存取模式可以還原 3.0 以前的行為。</span><span class="sxs-lookup"><span data-stu-id="fd92e-340">The pre-3.0 behavior can be restored through configuration of the property access mode in the modelBuilder fluent API.</span></span>
<span data-ttu-id="fd92e-341">例如：</span><span class="sxs-lookup"><span data-stu-id="fd92e-341">For example:</span></span>

```C#
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

## <a name="throw-if-multiple-compatible-backing-fields-are-found"></a><span data-ttu-id="fd92e-342">找到多個相容的支援欄位時擲回</span><span class="sxs-lookup"><span data-stu-id="fd92e-342">Throw if multiple compatible backing fields are found</span></span>

[<span data-ttu-id="fd92e-343">追蹤問題 #12523</span><span class="sxs-lookup"><span data-stu-id="fd92e-343">Tracking Issue #12523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

<span data-ttu-id="fd92e-344">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="fd92e-344">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="fd92e-345">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-345">Old behavior</span></span>**

<span data-ttu-id="fd92e-346">在 EF Core 3.0 以前，如果有多個欄位符合尋找屬性支援欄位的規則，則會根據特定優先順序來選擇一個欄位。</span><span class="sxs-lookup"><span data-stu-id="fd92e-346">Before EF Core 3.0, if multiple fields matched the rules for finding the backing field of a property, then one field would be chosen based on some precedence order.</span></span>
<span data-ttu-id="fd92e-347">這可能會導致在模稜兩可的情況下使用錯誤的欄位。</span><span class="sxs-lookup"><span data-stu-id="fd92e-347">This could cause the wrong field to be used in ambiguous cases.</span></span>

**<span data-ttu-id="fd92e-348">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-348">New behavior</span></span>**

<span data-ttu-id="fd92e-349">從 EF Core 3.0 開始，如果有多個欄位符合相同的屬性，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="fd92e-349">Starting with EF Core 3.0, if multiple fields are matched to the same property, then an exception is thrown.</span></span>

**<span data-ttu-id="fd92e-350">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-350">Why</span></span>**

<span data-ttu-id="fd92e-351">這項變更的目的是為了避免在只能有一個正確欄位的情況下，自動使用某個欄位而非另一個欄位。</span><span class="sxs-lookup"><span data-stu-id="fd92e-351">This change was made to avoid silently using one field over another when only one can be correct.</span></span>

**<span data-ttu-id="fd92e-352">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-352">Mitigations</span></span>**

<span data-ttu-id="fd92e-353">若屬性的支援欄位模稜兩可，則必須明確指定要使用的欄位。</span><span class="sxs-lookup"><span data-stu-id="fd92e-353">Properties with ambiguous backing fields must have the field to use specified explicitly.</span></span>
<span data-ttu-id="fd92e-354">例如，使用 Fluent API：</span><span class="sxs-lookup"><span data-stu-id="fd92e-354">For example, using the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

## <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a><span data-ttu-id="fd92e-355">AddDbContext/AddDbContextPool 再也不會呼叫 AddLogging 與 AddMemoryCache</span><span class="sxs-lookup"><span data-stu-id="fd92e-355">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>

[<span data-ttu-id="fd92e-356">追蹤問題 #14756</span><span class="sxs-lookup"><span data-stu-id="fd92e-356">Tracking Issue #14756</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14756)

<span data-ttu-id="fd92e-357">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="fd92e-357">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="fd92e-358">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-358">Old behavior</span></span>**

<span data-ttu-id="fd92e-359">在 EF Core 3.0 之前，呼叫 `AddDbContext` 或 `AddDbContextPool` 也會透過呼叫 [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) 與 [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) 來向 DI 註冊記錄與記憶體快取服務。</span><span class="sxs-lookup"><span data-stu-id="fd92e-359">Before EF Core 3.0, calling `AddDbContext` or `AddDbContextPool` would also register logging and memory caching services with D.I through calls to [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) and [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

**<span data-ttu-id="fd92e-360">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-360">New behavior</span></span>**

<span data-ttu-id="fd92e-361">從 EF Core 3.0 開始，`AddDbContext` 與 `AddDbContextPool` 再也不會向相依性插入 (DI) 註冊這些服務。</span><span class="sxs-lookup"><span data-stu-id="fd92e-361">Starting with EF Core 3.0, `AddDbContext` and `AddDbContextPool` will no longer register these services with Dependency Injection (DI).</span></span>

**<span data-ttu-id="fd92e-362">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-362">Why</span></span>**

<span data-ttu-id="fd92e-363">EF Core 3.0 不會要求這些服務必須存在於應用程式的 DI 容器中。</span><span class="sxs-lookup"><span data-stu-id="fd92e-363">EF Core 3.0 does not require that these services are in the application's DI cotainer.</span></span> <span data-ttu-id="fd92e-364">不過，若 `ILoggerFactory` 已在應用程式的 DI 容器中註冊，則它仍會被 EF Core 使用。</span><span class="sxs-lookup"><span data-stu-id="fd92e-364">However, if `ILoggerFactory` is registered in the application's DI container, then it will still be used by EF Core.</span></span>

**<span data-ttu-id="fd92e-365">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-365">Mitigations</span></span>**

<span data-ttu-id="fd92e-366">若您的應用程式需要這些服務，請使用  [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) 或 [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) 明確地向 DI 容器註冊它們。</span><span class="sxs-lookup"><span data-stu-id="fd92e-366">If your application needs these services, then register them explicitly with the DI container using  [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) or [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

## <a name="dbcontextentry-now-performs-a-local-detectchanges"></a><span data-ttu-id="fd92e-367">DbContext.Entry 現在會執行本機 DetectChanges</span><span class="sxs-lookup"><span data-stu-id="fd92e-367">DbContext.Entry now performs a local DetectChanges</span></span>

[<span data-ttu-id="fd92e-368">追蹤問題 #13552</span><span class="sxs-lookup"><span data-stu-id="fd92e-368">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="fd92e-369">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="fd92e-369">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="fd92e-370">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-370">Old behavior</span></span>**

<span data-ttu-id="fd92e-371">在 EF Core 3.0 以前，呼叫 `DbContext.Entry` 會導致偵測所有追蹤實體的變更。</span><span class="sxs-lookup"><span data-stu-id="fd92e-371">Before EF Core 3.0, calling `DbContext.Entry` would cause changes to be detected for all tracked entities.</span></span>
<span data-ttu-id="fd92e-372">這可確保在 `EntityEntry` 中公開的狀態為最新狀態。</span><span class="sxs-lookup"><span data-stu-id="fd92e-372">This ensured that the state exposed in the `EntityEntry` was up-to-date.</span></span>

**<span data-ttu-id="fd92e-373">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-373">New behavior</span></span>**

<span data-ttu-id="fd92e-374">從 EF Core 3.0 開始，呼叫 `DbContext.Entry` 現在只會嘗試在指定實體及其相關的任何追蹤主要實體中偵測變更。</span><span class="sxs-lookup"><span data-stu-id="fd92e-374">Starting with EF Core 3.0, calling `DbContext.Entry` will now only attempt to detect changes in the given entity and any tracked principal entities related to it.</span></span>
<span data-ttu-id="fd92e-375">這表示呼叫此方法可能還無法偵測到其他位置的變更，因此可能會影響應用程式狀態。</span><span class="sxs-lookup"><span data-stu-id="fd92e-375">This means that changes elsewhere may not have been detected by calling this method, which could have implications on application state.</span></span>

<span data-ttu-id="fd92e-376">請注意，如果 `ChangeTracker.AutoDetectChangesEnabled` 設定為 `false`，甚至是此本機變更偵測都會停用。</span><span class="sxs-lookup"><span data-stu-id="fd92e-376">Note that if `ChangeTracker.AutoDetectChangesEnabled` is set to `false` then even this local change detection will be disabled.</span></span>

<span data-ttu-id="fd92e-377">其他導致變更偵測的方法 (例如 `ChangeTracker.Entries` 和 `SaveChanges`) 仍會對所有追蹤實體進行完整的 `DetectChanges`。</span><span class="sxs-lookup"><span data-stu-id="fd92e-377">Other methods that cause change detection--for example `ChangeTracker.Entries` and `SaveChanges`--still cause a full `DetectChanges` of all tracked entities.</span></span>

**<span data-ttu-id="fd92e-378">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-378">Why</span></span>**

<span data-ttu-id="fd92e-379">這項變更的目的是為了改善使用 `context.Entry` 的預設效能。</span><span class="sxs-lookup"><span data-stu-id="fd92e-379">This change was made to improve the default performance of using `context.Entry`.</span></span>

**<span data-ttu-id="fd92e-380">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-380">Mitigations</span></span>**

<span data-ttu-id="fd92e-381">在呼叫 `Entry` 之前明確呼叫 `ChgangeTracker.DetectChanges()` 可確保 3.0 以前的行為。</span><span class="sxs-lookup"><span data-stu-id="fd92e-381">Call `ChgangeTracker.DetectChanges()` explicitly before calling `Entry` to ensure the pre-3.0 behavior.</span></span>

## <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a><span data-ttu-id="fd92e-382">字串和位元組陣列索引鍵預設不是由用戶端產生</span><span class="sxs-lookup"><span data-stu-id="fd92e-382">String and byte array keys are not client-generated by default</span></span>

[<span data-ttu-id="fd92e-383">追蹤問題 #14617</span><span class="sxs-lookup"><span data-stu-id="fd92e-383">Tracking Issue #14617</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

<span data-ttu-id="fd92e-384">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="fd92e-384">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="fd92e-385">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-385">Old behavior</span></span>**

<span data-ttu-id="fd92e-386">在 EF Core 3.0 以前，可以使用 `string` 和 `byte[]` 索引鍵屬性，而不需要明確設定非 Null 值。</span><span class="sxs-lookup"><span data-stu-id="fd92e-386">Before EF Core 3.0, `string` and `byte[]` key properties could be used without explicitly setting a non-null value.</span></span>
<span data-ttu-id="fd92e-387">在此情況下，會在用戶端以 GUID 形式產生索引鍵值，再序列化為 `byte[]` 的位元組。</span><span class="sxs-lookup"><span data-stu-id="fd92e-387">In such a case, the key value would be generated on the client as a GUID, serialized to bytes for `byte[]`.</span></span>

**<span data-ttu-id="fd92e-388">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-388">New behavior</span></span>**

<span data-ttu-id="fd92e-389">從 EF Core 3.0 開始，系統會擲回例外狀況，指出尚未設定任何索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="fd92e-389">Starting with EF Core 3.0 an exception will be thrown indicating that no key value has been set.</span></span>

**<span data-ttu-id="fd92e-390">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-390">Why</span></span>**

<span data-ttu-id="fd92e-391">這項變更是因為用戶端產生的 `string`/`byte[]` 值通常不太有用，而且預設行為使它很難以一般方式來推論產生的索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="fd92e-391">This change was made because client-generated `string`/`byte[]` values generally aren't useful, and the default behavior made it hard to reason about generated key values in a common way.</span></span>

**<span data-ttu-id="fd92e-392">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-392">Mitigations</span></span>**

<span data-ttu-id="fd92e-393">藉由明確指定索引鍵屬性應該在未設定其他非 Null 值時使用產生的值，即可取得 3.0 以前的行為。</span><span class="sxs-lookup"><span data-stu-id="fd92e-393">The pre-3.0 behavior can be obtained by explicitly specifying that the key properties should use generated values if no other non-null value is set.</span></span>
<span data-ttu-id="fd92e-394">例如，使用 Fluent API：</span><span class="sxs-lookup"><span data-stu-id="fd92e-394">For example, with the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

<span data-ttu-id="fd92e-395">或者，使用資料註解：</span><span class="sxs-lookup"><span data-stu-id="fd92e-395">Or with data annotations:</span></span>

```C#
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

## <a name="iloggerfactory-is-now-a-scoped-service"></a><span data-ttu-id="fd92e-396">ILoggerFactory 現在是限定範圍的服務</span><span class="sxs-lookup"><span data-stu-id="fd92e-396">ILoggerFactory is now a scoped service</span></span>

[<span data-ttu-id="fd92e-397">追蹤問題 #14698</span><span class="sxs-lookup"><span data-stu-id="fd92e-397">Tracking Issue #14698</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

<span data-ttu-id="fd92e-398">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="fd92e-398">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="fd92e-399">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-399">Old behavior</span></span>**

<span data-ttu-id="fd92e-400">在 EF Core 3.0 以前，`ILoggerFactory` 會註冊為單一服務。</span><span class="sxs-lookup"><span data-stu-id="fd92e-400">Before EF Core 3.0, `ILoggerFactory` was registered as a singleton service.</span></span>

**<span data-ttu-id="fd92e-401">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-401">New behavior</span></span>**

<span data-ttu-id="fd92e-402">從 EF Core 3.0 開始，`ILoggerFactory` 現在會註冊為限定範圍。</span><span class="sxs-lookup"><span data-stu-id="fd92e-402">Starting with EF Core 3.0, `ILoggerFactory` is now registered as scoped.</span></span>

**<span data-ttu-id="fd92e-403">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-403">Why</span></span>**

<span data-ttu-id="fd92e-404">這項變更的目的是為了允許記錄器與 `DbContext` 執行個體產生關聯，這可啟用其他功能，並避免某些異常行為案例，例如內部服務提供者遽增。</span><span class="sxs-lookup"><span data-stu-id="fd92e-404">This change was made to allow association of a logger with a `DbContext` instance, which enables other functionality and removes some cases of pathological behavior such as an explosion of internal service providers.</span></span>

**<span data-ttu-id="fd92e-405">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-405">Mitigations</span></span>**

<span data-ttu-id="fd92e-406">這項變更不應影響應用程式程式碼，除非在 EF Core 內部服務提供者上使用自訂服務註冊該程式碼。</span><span class="sxs-lookup"><span data-stu-id="fd92e-406">This change should not impact application code unless it is registering and using custom services on the EF Core internal service provider.</span></span>
<span data-ttu-id="fd92e-407">但這並不常見。</span><span class="sxs-lookup"><span data-stu-id="fd92e-407">This isn't common.</span></span>
<span data-ttu-id="fd92e-408">在這些情況下，大部分的項目仍會運作，但相依於 `ILoggerFactory` 的任何單一服務需要變更，才能以不同方式取得 `ILoggerFactory`。</span><span class="sxs-lookup"><span data-stu-id="fd92e-408">In these cases, most things will still work, but any singleton service that was depending on `ILoggerFactory` will need to be changed to obtain the `ILoggerFactory` in a different way.</span></span>

<span data-ttu-id="fd92e-409">如果您遇到上述情況，請在 [EF Core GitHub 問題追蹤器](https://github.com/aspnet/EntityFrameworkCore/issues)上提出問題，讓我們知道您使用 `ILoggerFactory` 的方式，以便進一步了解未來如何才不會再次中斷。</span><span class="sxs-lookup"><span data-stu-id="fd92e-409">If you run into situations like this, please file an issue at on the [EF Core GitHub issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues) to let us know how you are using `ILoggerFactory` such that we can better understand how not to break this again in the future.</span></span>

## <a name="idbcontextoptionsextensionwithdebuginfo-merged-into-idbcontextoptionsextension"></a><span data-ttu-id="fd92e-410">IDbContextOptionsExtensionWithDebugInfo 已合併到 IDbContextOptionsExtension 中</span><span class="sxs-lookup"><span data-stu-id="fd92e-410">IDbContextOptionsExtensionWithDebugInfo merged into IDbContextOptionsExtension</span></span>

[<span data-ttu-id="fd92e-411">追蹤問題 #13552</span><span class="sxs-lookup"><span data-stu-id="fd92e-411">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="fd92e-412">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="fd92e-412">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="fd92e-413">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-413">Old behavior</span></span>**

`IDbContextOptionsExtensionWithDebugInfo` <span data-ttu-id="fd92e-414">是從 `IDbContextOptionsExtension` 擴充的額外選擇性介面，以避免在 2.x 發行週期內對介面進行中斷性變更。</span><span class="sxs-lookup"><span data-stu-id="fd92e-414">was an additional optional interface extended from `IDbContextOptionsExtension` to avoid making a breaking change to the interface during the 2.x release cycle.</span></span>

**<span data-ttu-id="fd92e-415">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-415">New behavior</span></span>**

<span data-ttu-id="fd92e-416">這些介面現在會一起合併到 `IDbContextOptionsExtension` 中。</span><span class="sxs-lookup"><span data-stu-id="fd92e-416">The interfaces are now merged together into `IDbContextOptionsExtension`.</span></span>

**<span data-ttu-id="fd92e-417">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-417">Why</span></span>**

<span data-ttu-id="fd92e-418">這項變更是因為這些介面在概念上是一個介面。</span><span class="sxs-lookup"><span data-stu-id="fd92e-418">This change was made because the interfaces are conceptually one.</span></span>

**<span data-ttu-id="fd92e-419">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-419">Mitigations</span></span>**

<span data-ttu-id="fd92e-420">您必須更新 `IDbContextOptionsExtension` 的所有實作，才能支援新成員。</span><span class="sxs-lookup"><span data-stu-id="fd92e-420">Any implementations of `IDbContextOptionsExtension` will need to be updated to support the new member.</span></span>

## <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a><span data-ttu-id="fd92e-421">消極式載入 Proxy 停止假設導覽屬性已完全載入</span><span class="sxs-lookup"><span data-stu-id="fd92e-421">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>

[<span data-ttu-id="fd92e-422">追蹤問題 #12780</span><span class="sxs-lookup"><span data-stu-id="fd92e-422">Tracking Issue #12780</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

<span data-ttu-id="fd92e-423">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="fd92e-423">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="fd92e-424">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-424">Old behavior</span></span>**

<span data-ttu-id="fd92e-425">在 EF Core 3.0 以前，一旦處置 `DbContext` 之後，就無從得知實體上取自該內容的指定導覽屬性是否已完全載入。</span><span class="sxs-lookup"><span data-stu-id="fd92e-425">Before EF Core 3.0, once a `DbContext` was disposed there was no way of knowing if a given navigation property on an entity obtained from that context was fully loaded or not.</span></span>
<span data-ttu-id="fd92e-426">Proxy 會改為假設如有非 Null 值，會載入參考導覽；如果不是空的，則會載入集合導覽。</span><span class="sxs-lookup"><span data-stu-id="fd92e-426">Proxies would instead assume that a reference navigation is loaded if it has a non-null value, and that a collection navigation is loaded if it isn't empty.</span></span>
<span data-ttu-id="fd92e-427">在這些情況下，嘗試消極式載入不會執行任何作業。</span><span class="sxs-lookup"><span data-stu-id="fd92e-427">In these cases, attempting to lazy-load would be a no-op.</span></span>

**<span data-ttu-id="fd92e-428">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-428">New behavior</span></span>**

<span data-ttu-id="fd92e-429">從 EF Core 3.0 開始，Proxy 會追蹤是否載入導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="fd92e-429">Starting with EF Core 3.0, proxies keep track of whether or not a navigation property is loaded.</span></span>
<span data-ttu-id="fd92e-430">這表示嘗試存取在處置內容之後載入的導覽屬性一律不會執行任何作業，即使已載入的導覽是空的或 Null 也一樣。</span><span class="sxs-lookup"><span data-stu-id="fd92e-430">This means attempting to access a navigation property that is loaded after the context has been disposed will always be a no-op, even when the loaded navigation is empty or null.</span></span>
<span data-ttu-id="fd92e-431">相反地，如果在處置內容之後嘗試存取未載入的導覽屬性，則會擲回例外狀況，即使導覽屬性不是空集合也一樣。</span><span class="sxs-lookup"><span data-stu-id="fd92e-431">Conversely, attempting to access a navigation property that isn't loaded will throw an exception if the context is disposed even if the navigation property is a non-empty collection.</span></span>
<span data-ttu-id="fd92e-432">如果發生這種情況，則表示應用程式程式碼嘗試在無效的時間使用消極式載入，應用程式應該變更為不要這麼做。</span><span class="sxs-lookup"><span data-stu-id="fd92e-432">If this situation arises, it means the application code is attempting to use lazy-loading at an invalid time, and the application should be changed to not do this.</span></span>

**<span data-ttu-id="fd92e-433">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-433">Why</span></span>**

<span data-ttu-id="fd92e-434">這項變更的目的是為了在已處置的 `DbContext` 執行個體上嘗試消極式載入時，使行為一致且正確。</span><span class="sxs-lookup"><span data-stu-id="fd92e-434">This change was made to make the behavior consistent and correct when attempting to lazy-load on a disposed `DbContext` instance.</span></span>

**<span data-ttu-id="fd92e-435">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-435">Mitigations</span></span>**

<span data-ttu-id="fd92e-436">將應用程式程式碼更新為不要嘗試對已處置的內容進行消極式載入，或將此設定為不執行任何作業，如例外狀況訊息中所述。</span><span class="sxs-lookup"><span data-stu-id="fd92e-436">Update application code to not attempt lazy-loading with a disposed context, or configure this to be a no-op as described in the exception message.</span></span>

## <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a><span data-ttu-id="fd92e-437">過度建立內部服務提供者現在預設是錯誤</span><span class="sxs-lookup"><span data-stu-id="fd92e-437">Excessive creation of internal service providers is now an error by default</span></span>

[<span data-ttu-id="fd92e-438">追蹤問題 #10236</span><span class="sxs-lookup"><span data-stu-id="fd92e-438">Tracking Issue #10236</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

<span data-ttu-id="fd92e-439">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="fd92e-439">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="fd92e-440">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-440">Old behavior</span></span>**

<span data-ttu-id="fd92e-441">在 EF Core 3.0 以前，當應用程式建立異常數目的內部服務提供者時，會記錄一則警告。</span><span class="sxs-lookup"><span data-stu-id="fd92e-441">Before EF Core 3.0, a warning would be logged for an application creating a pathological number of internal service providers.</span></span>

**<span data-ttu-id="fd92e-442">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-442">New behavior</span></span>**

<span data-ttu-id="fd92e-443">從 EF Core 3.0 開始，此警告現在會視為錯誤，並會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="fd92e-443">Starting with EF Core 3.0, this warning is now considered and error and an exception is thrown.</span></span> 

**<span data-ttu-id="fd92e-444">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-444">Why</span></span>**

<span data-ttu-id="fd92e-445">這項變更的目的是為了透過更明確公開此異常案例，藉以開發更完善的應用程式程式碼。</span><span class="sxs-lookup"><span data-stu-id="fd92e-445">This change was made to drive better application code through exposing this pathological case more explicitly.</span></span>

**<span data-ttu-id="fd92e-446">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-446">Mitigations</span></span>**

<span data-ttu-id="fd92e-447">遇到此錯誤時的最適當動作是了解根本原因，並停止建立這麼多的內部服務提供者。</span><span class="sxs-lookup"><span data-stu-id="fd92e-447">The most appropriate cause of action on encountering this error is to understand the root cause and stop creating so many internal service providers.</span></span>
<span data-ttu-id="fd92e-448">不過，透過設定 `DbContextOptionsBuilder` 可以將錯誤轉換回警告。</span><span class="sxs-lookup"><span data-stu-id="fd92e-448">However, the error can be converted back to a warning (or ignored) via configuration on the `DbContextOptionsBuilder`.</span></span>
<span data-ttu-id="fd92e-449">例如：</span><span class="sxs-lookup"><span data-stu-id="fd92e-449">For example:</span></span>

```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

## <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a><span data-ttu-id="fd92e-450">使用單一字串呼叫之 HasOne/HasMany 的新行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-450">New behavior for HasOne/HasMany called with a single string</span></span>

[<span data-ttu-id="fd92e-451">追蹤問題 #9171</span><span class="sxs-lookup"><span data-stu-id="fd92e-451">Tracking Issue #9171</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9171)

<span data-ttu-id="fd92e-452">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="fd92e-452">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="fd92e-453">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-453">Old behavior</span></span>**

<span data-ttu-id="fd92e-454">在 EF Core 3.0 之前，使用單一字串呼叫 `HasOne` 或 `HasMany` 的程式碼會以令人困惑的方式解譯。</span><span class="sxs-lookup"><span data-stu-id="fd92e-454">Before EF Core 3.0, code calling `HasOne` or `HasMany` with a single string was interpretted in a confusing way.</span></span>
<span data-ttu-id="fd92e-455">例如：</span><span class="sxs-lookup"><span data-stu-id="fd92e-455">For example:</span></span>
```C#
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

<span data-ttu-id="fd92e-456">程式碼看起來像是它使用 `Entrance` 瀏覽屬性將 `Samurai` 與一些其他實體類型相關，這可能是私用屬性。</span><span class="sxs-lookup"><span data-stu-id="fd92e-456">The code looks like it is relating `Samurai` to some other entity type using the `Entrance` navigation property, which may be private.</span></span>

<span data-ttu-id="fd92e-457">在現實中，此程式碼會在不使用瀏覽屬性的情況下嘗試建立與一些實體 (稱為 `Entrance`) 的關係。</span><span class="sxs-lookup"><span data-stu-id="fd92e-457">In reality, this code attempts to create a relationship to some entity type called `Entrance` with no navigation property.</span></span>

**<span data-ttu-id="fd92e-458">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-458">New behavior</span></span>**

<span data-ttu-id="fd92e-459">從 EF Core 3.0 開始，上述程式碼現在會執行像以前一樣的動作。</span><span class="sxs-lookup"><span data-stu-id="fd92e-459">Starting with EF Core 3.0, the code above now does what it looked like it should have been doing before.</span></span>

**<span data-ttu-id="fd92e-460">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-460">Why</span></span>**

<span data-ttu-id="fd92e-461">舊行為令人非常困惑，特別是當讀取設定程式碼與尋找錯誤時。</span><span class="sxs-lookup"><span data-stu-id="fd92e-461">The old behavior was very confusing, especially when reading the configuration code and looking for errors.</span></span>

**<span data-ttu-id="fd92e-462">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-462">Mitigations</span></span>**

<span data-ttu-id="fd92e-463">這只會造成已明確針對類型名稱使用字串設定關係，而未明確指定瀏覽屬性的應用程式中斷。</span><span class="sxs-lookup"><span data-stu-id="fd92e-463">This will only break applications that are explicitly configuring relationships using strings for type names and without specifying the navigation property explicitly.</span></span>
<span data-ttu-id="fd92e-464">這不是常見情況。</span><span class="sxs-lookup"><span data-stu-id="fd92e-464">This is not common.</span></span>
<span data-ttu-id="fd92e-465">先前的行為可透過明確地傳遞瀏覽屬性名稱的 `null` 來取得。</span><span class="sxs-lookup"><span data-stu-id="fd92e-465">The previous behavior can be obtained through explicitly passing `null` for the navigation property name.</span></span>
<span data-ttu-id="fd92e-466">例如：</span><span class="sxs-lookup"><span data-stu-id="fd92e-466">For example:</span></span>

```C#
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

## <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a><span data-ttu-id="fd92e-467">數個非同步方法的傳回類型已從 Task 變更為 ValueTask</span><span class="sxs-lookup"><span data-stu-id="fd92e-467">The return type for several async methods has been changed from Task to ValueTask</span></span>

[<span data-ttu-id="fd92e-468">追蹤問題 #15184</span><span class="sxs-lookup"><span data-stu-id="fd92e-468">Tracking Issue #15184</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15184)

<span data-ttu-id="fd92e-469">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="fd92e-469">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="fd92e-470">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-470">Old behavior</span></span>**

<span data-ttu-id="fd92e-471">下列非同步方法先前傳回了 `Task<T>`：</span><span class="sxs-lookup"><span data-stu-id="fd92e-471">The following async methods previously returned a `Task<T>`:</span></span>

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* `ValueGenerator.NextValueAsync()` <span data-ttu-id="fd92e-472">(和衍生類別)</span><span class="sxs-lookup"><span data-stu-id="fd92e-472">(and deriving classes)</span></span>

**<span data-ttu-id="fd92e-473">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-473">New behavior</span></span>**

<span data-ttu-id="fd92e-474">上述方法現在會透過相同的 `T` 傳回 `ValueTask<T>`，如同以前一樣。</span><span class="sxs-lookup"><span data-stu-id="fd92e-474">The aforementioned methods now return a `ValueTask<T>` over the same `T` as before.</span></span>

**<span data-ttu-id="fd92e-475">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-475">Why</span></span>**

<span data-ttu-id="fd92e-476">這項變更可降低叫用這些方法時產生的堆積配置數目，可改善一般效能。</span><span class="sxs-lookup"><span data-stu-id="fd92e-476">This change reduces the number of heap allocations incurred when invoking these methods, improving general performance.</span></span>

**<span data-ttu-id="fd92e-477">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-477">Mitigations</span></span>**

<span data-ttu-id="fd92e-478">僅等待上述 API 的應用程式只需要重新編譯，而不需要變更來源。</span><span class="sxs-lookup"><span data-stu-id="fd92e-478">Applications simply awaiting the above APIs only need to be recompiled - no source changes are necessary.</span></span>
<span data-ttu-id="fd92e-479">更複雜的使用方式 (例如將傳回的 `Task` 傳遞給 `Task.WhenAny()`) 通常需要藉由呼叫 `AsTask()` 將傳回的 `ValueTask<T>` 轉換為 `Task<T>`。</span><span class="sxs-lookup"><span data-stu-id="fd92e-479">A more complex usage (e.g. passing the returned `Task` to `Task.WhenAny()`) typically require that the returned `ValueTask<T>` be converted to a `Task<T>` by calling `AsTask()` on it.</span></span>
<span data-ttu-id="fd92e-480">請注意，這會抵消這項變更所帶來的配置減少。</span><span class="sxs-lookup"><span data-stu-id="fd92e-480">Note that this negates the allocation reduction that this change brings.</span></span>

## <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a><span data-ttu-id="fd92e-481">Relational:TypeMapping 註解現僅為 TypeMapping</span><span class="sxs-lookup"><span data-stu-id="fd92e-481">The Relational:TypeMapping annotation is now just TypeMapping</span></span>

[<span data-ttu-id="fd92e-482">追蹤問題 #9913</span><span class="sxs-lookup"><span data-stu-id="fd92e-482">Tracking Issue #9913</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

<span data-ttu-id="fd92e-483">這項變更已於 EF Core 3.0-preview 2 推出。</span><span class="sxs-lookup"><span data-stu-id="fd92e-483">This change was introduced in EF Core 3.0-preview 2.</span></span>

**<span data-ttu-id="fd92e-484">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-484">Old behavior</span></span>**

<span data-ttu-id="fd92e-485">類型對應註解的註解名稱之前是 "Relational:TypeMapping"。</span><span class="sxs-lookup"><span data-stu-id="fd92e-485">The annotation name for type mapping annotations was "Relational:TypeMapping".</span></span>

**<span data-ttu-id="fd92e-486">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-486">New behavior</span></span>**

<span data-ttu-id="fd92e-487">類型對應註解的註解名稱現在是 "TypeMapping"。</span><span class="sxs-lookup"><span data-stu-id="fd92e-487">The annotation name for type mapping annotations is now "TypeMapping".</span></span>

**<span data-ttu-id="fd92e-488">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-488">Why</span></span>**

<span data-ttu-id="fd92e-489">類型對應現在不只用於關聯式資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="fd92e-489">Type mappings are now used for more than just relational database providers.</span></span>

**<span data-ttu-id="fd92e-490">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-490">Mitigations</span></span>**

<span data-ttu-id="fd92e-491">這只會中斷直接將類型對應當做註解存取的應用程式，但這並不常見。</span><span class="sxs-lookup"><span data-stu-id="fd92e-491">This will only break applications that access the type mapping directly as an annotation, which isn't common.</span></span>
<span data-ttu-id="fd92e-492">最適當的修正動作是使用 API 介面存取類型對應，而不是直接使用註解。</span><span class="sxs-lookup"><span data-stu-id="fd92e-492">The most appropriate action to fix is to use API surface to access type mappings rather than using the annotation directly.</span></span>

## <a name="totable-on-a-derived-type-throws-an-exception"></a><span data-ttu-id="fd92e-493">衍生類型上的 ToTable 會擲回例外狀況</span><span class="sxs-lookup"><span data-stu-id="fd92e-493">ToTable on a derived type throws an exception</span></span> 

[<span data-ttu-id="fd92e-494">追蹤問題 #11811</span><span class="sxs-lookup"><span data-stu-id="fd92e-494">Tracking Issue #11811</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

<span data-ttu-id="fd92e-495">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="fd92e-495">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="fd92e-496">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-496">Old behavior</span></span>**

<span data-ttu-id="fd92e-497">在 EF Core 3.0 以前，會忽略衍生類型上呼叫的 `ToTable()`，因為唯一的繼承對應策略是對此案例無效的 TPH。</span><span class="sxs-lookup"><span data-stu-id="fd92e-497">Before EF Core 3.0, `ToTable()` called on a derived type would be ignored since only inheritance mapping strategy was TPH where this isn't valid.</span></span> 

**<span data-ttu-id="fd92e-498">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-498">New behavior</span></span>**

<span data-ttu-id="fd92e-499">從 EF Core 3.0 開始，以及在更新版本中準備新增 TPT 和 TPC 支援時，在衍生類型上呼叫的 `ToTable()` 現在會擲回例外狀況，以避免未來發生非預期的對應變更。</span><span class="sxs-lookup"><span data-stu-id="fd92e-499">Starting with EF Core 3.0 and in preparation for adding TPT and TPC support in a later release, `ToTable()` called on a derived type will now throw an exception to avoid an unexpected mapping change in the future.</span></span>

**<span data-ttu-id="fd92e-500">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-500">Why</span></span>**

<span data-ttu-id="fd92e-501">目前無法將衍生類型對應至不同的資料表。</span><span class="sxs-lookup"><span data-stu-id="fd92e-501">Currently it isn't valid to map a derived type to a different table.</span></span>
<span data-ttu-id="fd92e-502">這項變更可避免未來有效執行時的中斷情況。</span><span class="sxs-lookup"><span data-stu-id="fd92e-502">This change avoids breaking in the future when it becomes a valid thing to do.</span></span>

**<span data-ttu-id="fd92e-503">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-503">Mitigations</span></span>**

<span data-ttu-id="fd92e-504">避免嘗試將衍生類型對應至其他資料表。</span><span class="sxs-lookup"><span data-stu-id="fd92e-504">Remove any attempts to map derived types to other tables.</span></span>

## <a name="forsqlserverhasindex-replaced-with-hasindex"></a><span data-ttu-id="fd92e-505">ForSqlServerHasIndex 已取代為 HasIndex</span><span class="sxs-lookup"><span data-stu-id="fd92e-505">ForSqlServerHasIndex replaced with HasIndex</span></span> 

[<span data-ttu-id="fd92e-506">追蹤問題 #12366</span><span class="sxs-lookup"><span data-stu-id="fd92e-506">Tracking Issue #12366</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

<span data-ttu-id="fd92e-507">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="fd92e-507">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="fd92e-508">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-508">Old behavior</span></span>**

<span data-ttu-id="fd92e-509">在 EF Core 3.0 以前，`ForSqlServerHasIndex().ForSqlServerInclude()` 可讓您設定搭配 `INCLUDE` 使用的資料行。</span><span class="sxs-lookup"><span data-stu-id="fd92e-509">Before EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` provided a way to configure columns used with `INCLUDE`.</span></span>

**<span data-ttu-id="fd92e-510">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-510">New behavior</span></span>**

<span data-ttu-id="fd92e-511">從 EF Core 3.0 開始，關聯式層級現在支援對索引使用 `Include`。</span><span class="sxs-lookup"><span data-stu-id="fd92e-511">Starting with EF Core 3.0, using `Include` on an index is now supported at the relational level.</span></span>
<span data-ttu-id="fd92e-512">使用 `HasIndex().ForSqlServerInclude()`。</span><span class="sxs-lookup"><span data-stu-id="fd92e-512">Use `HasIndex().ForSqlServerInclude()`.</span></span>

**<span data-ttu-id="fd92e-513">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-513">Why</span></span>**

<span data-ttu-id="fd92e-514">這項變更的目的是為了能夠使用 `Includes` 將所有資料庫提供者的索引 API 合併到一個位置。</span><span class="sxs-lookup"><span data-stu-id="fd92e-514">This change was made to consolidate the API for indexes with `Includes` into one place for all database providers.</span></span>

**<span data-ttu-id="fd92e-515">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-515">Mitigations</span></span>**

<span data-ttu-id="fd92e-516">使用新的 API，如上所示。</span><span class="sxs-lookup"><span data-stu-id="fd92e-516">Use the new API, as shown above.</span></span>

## <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a><span data-ttu-id="fd92e-517">EF Core 不會再傳送 SQLite FK 強制的 pragma</span><span class="sxs-lookup"><span data-stu-id="fd92e-517">EF Core no longer sends pragma for SQLite FK enforcement</span></span>

[<span data-ttu-id="fd92e-518">追蹤問題 #12151</span><span class="sxs-lookup"><span data-stu-id="fd92e-518">Tracking Issue #12151</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

<span data-ttu-id="fd92e-519">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="fd92e-519">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="fd92e-520">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-520">Old behavior</span></span>**

<span data-ttu-id="fd92e-521">在 EF Core 3.0 以前，當開啟 SQLite 連線時，EF Core 會傳送 `PRAGMA foreign_keys = 1`。</span><span class="sxs-lookup"><span data-stu-id="fd92e-521">Before EF Core 3.0, EF Core would send `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

**<span data-ttu-id="fd92e-522">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-522">New behavior</span></span>**

<span data-ttu-id="fd92e-523">從 EF Core 3.0 開始，當開啟 SQLite 連線時，EF Core 不會再傳送 `PRAGMA foreign_keys = 1`。</span><span class="sxs-lookup"><span data-stu-id="fd92e-523">Starting with EF Core 3.0, EF Core no longer sends `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

**<span data-ttu-id="fd92e-524">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-524">Why</span></span>**

<span data-ttu-id="fd92e-525">這項變更是因為 EF Core 預設會使用 `SQLitePCLRaw.bundle_e_sqlite3`，這也表示預設會開啟 FK 強制，而不需要在每次開啟連線時明確啟用。</span><span class="sxs-lookup"><span data-stu-id="fd92e-525">This change was made because EF Core uses `SQLitePCLRaw.bundle_e_sqlite3` by default, which in turn means that FK enforcement is switched on by default and doesn't need to be explicitly enabled each time a connection is opened.</span></span>

**<span data-ttu-id="fd92e-526">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-526">Mitigations</span></span>**

<span data-ttu-id="fd92e-527">根據預設，會在預設用於 EF Core 的 SQLitePCLRaw.bundle_e_sqlite3 中啟用外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="fd92e-527">Foreign keys are enabled by default in SQLitePCLRaw.bundle_e_sqlite3, which is used by default for EF Core.</span></span>
<span data-ttu-id="fd92e-528">在其他情況下，則可以藉由在您的連接字串中指定 `Foreign Keys=True` 來啟用外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="fd92e-528">For other cases, foreign keys can be enabled by specifying `Foreign Keys=True` in your connection string.</span></span>

## <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundleesqlite3"></a><span data-ttu-id="fd92e-529">Microsoft.EntityFrameworkCore.Sqlite 現在相依於 SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="fd92e-529">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>

**<span data-ttu-id="fd92e-530">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-530">Old behavior</span></span>**

<span data-ttu-id="fd92e-531">在 EF Core 3.0 以前，EF Core 會使用 `SQLitePCLRaw.bundle_green`。</span><span class="sxs-lookup"><span data-stu-id="fd92e-531">Before EF Core 3.0, EF Core used `SQLitePCLRaw.bundle_green`.</span></span>

**<span data-ttu-id="fd92e-532">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-532">New behavior</span></span>**

<span data-ttu-id="fd92e-533">從 EF Core 3.0 開始，EF Core 會使用 `SQLitePCLRaw.bundle_e_sqlite3`。</span><span class="sxs-lookup"><span data-stu-id="fd92e-533">Starting with EF Core 3.0, EF Core uses `SQLitePCLRaw.bundle_e_sqlite3`.</span></span>

**<span data-ttu-id="fd92e-534">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-534">Why</span></span>**

<span data-ttu-id="fd92e-535">這項變更的目的是為了讓用於 iOS 的 SQLite 版本與其他平台一致。</span><span class="sxs-lookup"><span data-stu-id="fd92e-535">This change was made so that the version of SQLite used on iOS consistent with other platforms.</span></span>

**<span data-ttu-id="fd92e-536">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-536">Mitigations</span></span>**

<span data-ttu-id="fd92e-537">若要在 iOS 上使用原生 SQLite 版本，請設定 `Microsoft.Data.Sqlite` 使用不同的 `SQLitePCLRaw` 套件組合。</span><span class="sxs-lookup"><span data-stu-id="fd92e-537">To use the native SQLite version on iOS, configure `Microsoft.Data.Sqlite` to use a different `SQLitePCLRaw` bundle.</span></span>

## <a name="guid-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="fd92e-538">GUID 值現在於 SQLite 上的儲存形式為 TEXT</span><span class="sxs-lookup"><span data-stu-id="fd92e-538">Guid values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="fd92e-539">追蹤問題 #15078</span><span class="sxs-lookup"><span data-stu-id="fd92e-539">Tracking Issue #15078</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15078)

<span data-ttu-id="fd92e-540">這項變更已於 EF Core 3.0-preview 4 推出。</span><span class="sxs-lookup"><span data-stu-id="fd92e-540">This change was introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="fd92e-541">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-541">Old behavior</span></span>**

<span data-ttu-id="fd92e-542">GUID 值先前在 SQLite 上的儲存形式為 BLOB 值。</span><span class="sxs-lookup"><span data-stu-id="fd92e-542">Guid values were previously sored as BLOB values on SQLite.</span></span>

**<span data-ttu-id="fd92e-543">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-543">New behavior</span></span>**

<span data-ttu-id="fd92e-544">GUID 值現在的儲存形式為 TEXT。</span><span class="sxs-lookup"><span data-stu-id="fd92e-544">Guid values are now sotred as TEXT.</span></span>

**<span data-ttu-id="fd92e-545">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-545">Why</span></span>**

<span data-ttu-id="fd92e-546">GUID 的二進位格式未標準化。</span><span class="sxs-lookup"><span data-stu-id="fd92e-546">The binary format of Guids is not standardized.</span></span> <span data-ttu-id="fd92e-547">以 TEXT 的形式儲存值會提高資料庫與其他技術的相容性。</span><span class="sxs-lookup"><span data-stu-id="fd92e-547">Storing the values as TEXT makes the database more compatible with other technologies.</span></span>

**<span data-ttu-id="fd92e-548">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-548">Mitigations</span></span>**

<span data-ttu-id="fd92e-549">您可以參考以下方式執行 SQL，來將現有的資料庫移轉至新的格式。</span><span class="sxs-lookup"><span data-stu-id="fd92e-549">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

``` sql
UPDATE MyTable
SET GuidColumn = hex(substr(GuidColumn, 4, 1)) ||
                 hex(substr(GuidColumn, 3, 1)) ||
                 hex(substr(GuidColumn, 2, 1)) ||
                 hex(substr(GuidColumn, 1, 1)) || '-' ||
                 hex(substr(GuidColumn, 6, 1)) ||
                 hex(substr(GuidColumn, 5, 1)) || '-' ||
                 hex(substr(GuidColumn, 8, 1)) ||
                 hex(substr(GuidColumn, 7, 1)) || '-' ||
                 hex(substr(GuidColumn, 9, 2)) || '-' ||
                 hex(substr(GuidColumn, 11, 6))
WHERE typeof(GuidColumn) == 'blob';
```

<span data-ttu-id="fd92e-550">在 EF Core 中，您也可以在這些屬性上設定值轉換器來繼續使用原本的行為。</span><span class="sxs-lookup"><span data-stu-id="fd92e-550">In EF Core, you could also continue using the previous behavior by configuirng a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

<span data-ttu-id="fd92e-551">Microsoft.Data.Sqlite 依然可以同時從 BLOB 及 TEXT 資料行讀取 GUID 值；但因為參數和常數的預設格式已變更，所以您可能需要對多數涉及 GUID 的案例採取動作。</span><span class="sxs-lookup"><span data-stu-id="fd92e-551">Microsoft.Data.Sqlite remains capable of reading Guid values from both BLOB and TEXT columns; however, since the default format for parameters and constants has changed you'll likely need to take action for most scenarios involving Guids.</span></span>

## <a name="char-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="fd92e-552">Char 值現在於 SQLite 上會儲存為文字</span><span class="sxs-lookup"><span data-stu-id="fd92e-552">Char values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="fd92e-553">追蹤問題 #15020</span><span class="sxs-lookup"><span data-stu-id="fd92e-553">Tracking Issue #15020</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15020)

<span data-ttu-id="fd92e-554">這項變更已於 EF Core 3.0-preview 4 推出。</span><span class="sxs-lookup"><span data-stu-id="fd92e-554">This change was introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="fd92e-555">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-555">Old behavior</span></span>**

<span data-ttu-id="fd92e-556">Char 值原先在 SQLite 上儲存為整數值。</span><span class="sxs-lookup"><span data-stu-id="fd92e-556">Char values were previously sored as INTEGER values on SQLite.</span></span> <span data-ttu-id="fd92e-557">舉例來說，char 值 *A* 原先會儲存為整數值 65。</span><span class="sxs-lookup"><span data-stu-id="fd92e-557">For example, a char value of *A* was stored as the integer value 65.</span></span>

**<span data-ttu-id="fd92e-558">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-558">New behavior</span></span>**

<span data-ttu-id="fd92e-559">Char 值現在會儲存為 TEXT。</span><span class="sxs-lookup"><span data-stu-id="fd92e-559">Char values are now sotred as TEXT.</span></span>

**<span data-ttu-id="fd92e-560">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-560">Why</span></span>**

<span data-ttu-id="fd92e-561">將值儲存為 TEXT 不但更加自然，也使資料庫與其他技術的相容性更高。</span><span class="sxs-lookup"><span data-stu-id="fd92e-561">Storing the values as TEXT is more natural and makes the database more compatible with other technologies.</span></span>

**<span data-ttu-id="fd92e-562">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-562">Mitigations</span></span>**

<span data-ttu-id="fd92e-563">您可以參考以下方式執行 SQL，來將現有的資料庫移轉至新的格式。</span><span class="sxs-lookup"><span data-stu-id="fd92e-563">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

``` sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

<span data-ttu-id="fd92e-564">在 EF Core 中，您也可以在這些屬性上設定值轉換器來繼續使用原本的行為。</span><span class="sxs-lookup"><span data-stu-id="fd92e-564">In EF Core, you could also continue using the previous behavior by configuirng a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

<span data-ttu-id="fd92e-565">Microsoft.Data.Sqlite 也保留了讀取 INTEGER 和 TEXT 欄位字元值的功能，所以部分案例可能不需要任何動作。</span><span class="sxs-lookup"><span data-stu-id="fd92e-565">Microsoft.Data.Sqlite also remains capable of reading character values from both INTEGER and TEXT columns, so certain scenarios may not require any action.</span></span>

## <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a><span data-ttu-id="fd92e-566">移轉識別碼現在會使用不因文化特性而異的行事曆產生</span><span class="sxs-lookup"><span data-stu-id="fd92e-566">Migration IDs are now generated using the invariant culture's calendar</span></span>

[<span data-ttu-id="fd92e-567">追蹤問題 #12978</span><span class="sxs-lookup"><span data-stu-id="fd92e-567">Tracking Issue #12978</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12978)

<span data-ttu-id="fd92e-568">這項變更已於 EF Core 3.0-preview 4 推出。</span><span class="sxs-lookup"><span data-stu-id="fd92e-568">This change was introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="fd92e-569">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-569">Old behavior</span></span>**

<span data-ttu-id="fd92e-570">移轉識別碼原先會使用目前文化特性 (Culture) 的行事曆產生。</span><span class="sxs-lookup"><span data-stu-id="fd92e-570">Migration IDs were inadvertantly generated using the currret culture's calendar.</span></span>

**<span data-ttu-id="fd92e-571">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-571">New behavior</span></span>**

<span data-ttu-id="fd92e-572">移轉識別碼現在一律會使用不因文化特性而異的行事曆 (西曆) 產生。</span><span class="sxs-lookup"><span data-stu-id="fd92e-572">Migration IDs are now always generated using the invariant culture's calendar (Gregorian).</span></span>

**<span data-ttu-id="fd92e-573">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-573">Why</span></span>**

<span data-ttu-id="fd92e-574">更新資料庫或解決合併衝突時，移轉的順序相當重要。</span><span class="sxs-lookup"><span data-stu-id="fd92e-574">The order of migrations is important when updating the database or resolving merge conflicts.</span></span> <span data-ttu-id="fd92e-575">使用無差異的行事曆可避免順序問題，使小組成員系統行事曆不同的問題不會發生。</span><span class="sxs-lookup"><span data-stu-id="fd92e-575">Using the invariant calendar avoids ordering issues that can result from team members having different system calendars.</span></span>

**<span data-ttu-id="fd92e-576">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-576">Mitigations</span></span>**

<span data-ttu-id="fd92e-577">這項變更會影響年份大於西曆行事曆的非西曆行事曆使用者 (例如泰國佛曆)。</span><span class="sxs-lookup"><span data-stu-id="fd92e-577">This change affects anyone using a non-Gregorian calender where the year is greater than the Gregorian calendar (like the Thai Buddhist calendar).</span></span> <span data-ttu-id="fd92e-578">現有的移轉識別碼必須更新，以使新的移轉會在現有的移轉之後排序。</span><span class="sxs-lookup"><span data-stu-id="fd92e-578">Existing migration IDs will need to be updated so that new migrations are ordered after existing migrations.</span></span>

<span data-ttu-id="fd92e-579">您可在移轉設計工具檔案的移轉屬性中找到移轉識別碼。</span><span class="sxs-lookup"><span data-stu-id="fd92e-579">The migration ID can be found in the Migration attribute in the migrations' designer files.</span></span>

``` diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

<span data-ttu-id="fd92e-580">移轉歷程記錄資料表也必須更新。</span><span class="sxs-lookup"><span data-stu-id="fd92e-580">The Migrations history table also needs to be updated.</span></span>

``` sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

## <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a><span data-ttu-id="fd92e-581">已重新命名 LogQueryPossibleExceptionWithAggregateOperator</span><span class="sxs-lookup"><span data-stu-id="fd92e-581">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>

[<span data-ttu-id="fd92e-582">追蹤問題 #10985</span><span class="sxs-lookup"><span data-stu-id="fd92e-582">Tracking Issue #10985</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10985)

<span data-ttu-id="fd92e-583">這項變更已於 EF Core 3.0-preview 4 推出。</span><span class="sxs-lookup"><span data-stu-id="fd92e-583">This change was introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="fd92e-584">變更</span><span class="sxs-lookup"><span data-stu-id="fd92e-584">Change</span></span>**

`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` <span data-ttu-id="fd92e-585">已重新命名為 `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`。</span><span class="sxs-lookup"><span data-stu-id="fd92e-585">has been renamed to `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span></span>

**<span data-ttu-id="fd92e-586">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-586">Why</span></span>**

<span data-ttu-id="fd92e-587">使這個警告事件的命名與其他所有警告事件一致。</span><span class="sxs-lookup"><span data-stu-id="fd92e-587">Aligns the naming of this warning event with all other warning events.</span></span>

**<span data-ttu-id="fd92e-588">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-588">Mitigations</span></span>**

<span data-ttu-id="fd92e-589">使用新的名稱。</span><span class="sxs-lookup"><span data-stu-id="fd92e-589">Use the new name.</span></span> <span data-ttu-id="fd92e-590">(注意，事件識別碼未變更。)</span><span class="sxs-lookup"><span data-stu-id="fd92e-590">(Note that the event ID number has not changed.)</span></span>

## <a name="clarify-api-for-foreign-key-constraint-names"></a><span data-ttu-id="fd92e-591">讓 API 的外部索引鍵限制式名稱更清楚</span><span class="sxs-lookup"><span data-stu-id="fd92e-591">Clarify API for foreign key constraint names</span></span>

[<span data-ttu-id="fd92e-592">追蹤問題 #10730</span><span class="sxs-lookup"><span data-stu-id="fd92e-592">Tracking Issue #10730</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10730)

<span data-ttu-id="fd92e-593">這項變更已於 EF Core 3.0-preview 4 推出。</span><span class="sxs-lookup"><span data-stu-id="fd92e-593">This change was introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="fd92e-594">舊的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-594">Old behavior</span></span>**

<span data-ttu-id="fd92e-595">在 EF Core 3.0 前，外部索引鍵限制式名稱僅為 "name"。</span><span class="sxs-lookup"><span data-stu-id="fd92e-595">Before EF Core 3.0, foreign key constraint names were referred to as simply the "name".</span></span> <span data-ttu-id="fd92e-596">例如：</span><span class="sxs-lookup"><span data-stu-id="fd92e-596">For example:</span></span>

```C#
var constraintName = myForeignKey.Name;
```

**<span data-ttu-id="fd92e-597">新的行為</span><span class="sxs-lookup"><span data-stu-id="fd92e-597">New behavior</span></span>**

<span data-ttu-id="fd92e-598">從 EF Core 3.0 開始，外部索引鍵限制式名稱現為 "constaint name"。</span><span class="sxs-lookup"><span data-stu-id="fd92e-598">Starting with EF Core 3.0, foreign key constraint names are now referred to as the "constaint name".</span></span> <span data-ttu-id="fd92e-599">例如：</span><span class="sxs-lookup"><span data-stu-id="fd92e-599">For example:</span></span>

```C#
var constraintName = myForeignKey.ConstraintName;
```

**<span data-ttu-id="fd92e-600">原因</span><span class="sxs-lookup"><span data-stu-id="fd92e-600">Why</span></span>**

<span data-ttu-id="fd92e-601">這項變更可讓此領域中的命名一致，同時清楚指出這是外部索引鍵限制式的名稱，而非定義外部索引鍵的資料行或屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="fd92e-601">This change brings consistency to naming in this area, and also clarifies that this is the name of the foreign key constaint, and not the column or property name that the foreign key is defined on.</span></span>

**<span data-ttu-id="fd92e-602">風險降低</span><span class="sxs-lookup"><span data-stu-id="fd92e-602">Mitigations</span></span>**

<span data-ttu-id="fd92e-603">使用新的名稱。</span><span class="sxs-lookup"><span data-stu-id="fd92e-603">Use the new name.</span></span>
