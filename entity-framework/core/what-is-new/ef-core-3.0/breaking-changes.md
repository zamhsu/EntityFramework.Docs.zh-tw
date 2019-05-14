---
title: EF Core 3.0 的中斷性變更 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: EE2878C9-71F9-4FA5-9BC4-60517C7C9830
uid: core/what-is-new/ef-core-3.0/breaking-changes
ms.openlocfilehash: b1b5e286e08a8b6b4efe225a176e76023f9fdd20
ms.sourcegitcommit: 960e42a01b3a2f76da82e074f64f52252a8afecc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2019
ms.locfileid: "65405236"
---
# <a name="breaking-changes-included-in-ef-core-30-currently-in-preview"></a><span data-ttu-id="f3375-102">EF Core 3.0 (目前為預覽版) 包含的中斷性變更</span><span class="sxs-lookup"><span data-stu-id="f3375-102">Breaking changes included in EF Core 3.0 (currently in preview)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f3375-103">請注意，後續版本的功能集與上市時間，隨時可能會變動，此外，即使我們試圖將此頁面保持在最新狀態，可能還是無法隨時反映我們最新的計劃。</span><span class="sxs-lookup"><span data-stu-id="f3375-103">Please note that the feature sets and schedules of future releases are always subject to change, and although we will try to keep this page up to date, it may not reflect our latest plans at all times.</span></span>

<span data-ttu-id="f3375-104">當您將針對 EF Core 2.2.x 開發的應用程式升級為 3.0.0 時，下列 API 和行為變更可能會中斷這些應用程式。</span><span class="sxs-lookup"><span data-stu-id="f3375-104">The following API and behavior changes have the potential to break applications developed for EF Core 2.2.x when upgrading them to 3.0.0.</span></span>
<span data-ttu-id="f3375-105">這些變更預期只會影響[提供者變更](../../providers/provider-log.md)底下記載的資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="f3375-105">Changes that we expect to only impact database providers are documented under [provider changes](../../providers/provider-log.md).</span></span>
<span data-ttu-id="f3375-106">本文不會記載從某一 3.0 預覽版引進另一個 3.0 預覽版的新功能中斷。</span><span class="sxs-lookup"><span data-stu-id="f3375-106">Breaks in new features introduced from one 3.0 preview to another 3.0 preview aren't documented here.</span></span>

## <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a><span data-ttu-id="f3375-107">不會再於用戶端評估 LINQ 查詢</span><span class="sxs-lookup"><span data-stu-id="f3375-107">LINQ queries are no longer evaluated on the client</span></span>

<span data-ttu-id="f3375-108">[追蹤問題 #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[另請參閱問題 #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span><span class="sxs-lookup"><span data-stu-id="f3375-108">[Tracking Issue #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[Also see issue #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span></span>

<span data-ttu-id="f3375-109">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="f3375-109">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3375-110">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-110">**Old behavior**</span></span>

<span data-ttu-id="f3375-111">3.0 以前，在 EF Core 無法將屬於查詢的運算式轉換成 SQL 或參數時，它會自動在用戶端評估運算式。</span><span class="sxs-lookup"><span data-stu-id="f3375-111">Before 3.0, when EF Core couldn't convert an expression that was part of a query to either SQL or a parameter, it automatically evaluated the expression on the client.</span></span>
<span data-ttu-id="f3375-112">根據預設，對可能相當耗費資源的運算式進行用戶端評估只會觸發警告。</span><span class="sxs-lookup"><span data-stu-id="f3375-112">By default, client evaluation of potentially expensive expressions only triggered a warning.</span></span>

<span data-ttu-id="f3375-113">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-113">**New behavior**</span></span>

<span data-ttu-id="f3375-114">從 3.0 開始，EF Core 只允許在用戶端評估最上層投影的運算式 (查詢中的最後一個 `Select()` 呼叫)。</span><span class="sxs-lookup"><span data-stu-id="f3375-114">Starting with 3.0, EF Core only allows expressions in the top-level projection (the last `Select()` call in the query) to be evaluated on the client.</span></span>
<span data-ttu-id="f3375-115">當其他查詢部分中的運算式無法轉換成 SQL 或參數時，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="f3375-115">When expressions in any other part of the query can't be converted to either SQL or a parameter, an exception is thrown.</span></span>

<span data-ttu-id="f3375-116">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-116">**Why**</span></span>

<span data-ttu-id="f3375-117">查詢的自動用戶端評估可執行許多查詢，即使無法轉譯查詢的重要部分也一樣。</span><span class="sxs-lookup"><span data-stu-id="f3375-117">Automatic client evaluation of queries allows many queries to be executed even if important parts of them can't be translated.</span></span>
<span data-ttu-id="f3375-118">此行為可能會導致非預期且可能造成傷害的行為，而且該行為可能只會出現在生產環境中。</span><span class="sxs-lookup"><span data-stu-id="f3375-118">This behavior can result in unexpected and potentially damaging behavior that may only become evident in production.</span></span>
<span data-ttu-id="f3375-119">例如，`Where()` 呼叫中無法轉譯的條件可能會導致資料表中的所有資料列從資料庫伺服器移轉，並在用戶端套用篩選。</span><span class="sxs-lookup"><span data-stu-id="f3375-119">For example, a condition in a `Where()` call which can't be translated can cause all rows from the table to be transferred from the database server, and the filter to be applied on the client.</span></span>
<span data-ttu-id="f3375-120">如果資料表只包含幾個開發中的資料列，此情況可能很容易未被察覺；但當應用程式移至生產環境時，由於資料表可能包含數百萬個資料列，因此影響會很大。</span><span class="sxs-lookup"><span data-stu-id="f3375-120">This situation can easily go undetected if the table contains only a few rows in development, but hit hard when the application moves to production, where the table may contain millions of rows.</span></span>
<span data-ttu-id="f3375-121">用戶端評估警告也證明很容易在開發期間遭到忽略。</span><span class="sxs-lookup"><span data-stu-id="f3375-121">Client evaluation warnings also proved too easy to ignore during development.</span></span>

<span data-ttu-id="f3375-122">此外，自動用戶端評估可能會導致改善特定運算式的查詢轉譯造成版本間非預期的中斷性變更問題。</span><span class="sxs-lookup"><span data-stu-id="f3375-122">Besides this, automatic client evaluation can lead to issues in which improving query translation for specific expressions caused unintended breaking changes between releases.</span></span>

<span data-ttu-id="f3375-123">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-123">**Mitigations**</span></span>

<span data-ttu-id="f3375-124">如果無法完整轉譯查詢，請以可轉譯的格式來重寫查詢，或是使用 `AsEnumerable()`、`ToList()` 或類似函數來明確將資料帶回用戶端，以便接著使用 LINQ-to-Objects 加以處理。</span><span class="sxs-lookup"><span data-stu-id="f3375-124">If a query can't be fully translated, then either rewrite the query in a form that can be translated, or use `AsEnumerable()`, `ToList()`, or similar to explicitly bring data back to the client where it can then be further processed using LINQ-to-Objects.</span></span>

## <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a><span data-ttu-id="f3375-125">Entity Framework Core 不再屬於 ASP.NET Core 共用架構</span><span class="sxs-lookup"><span data-stu-id="f3375-125">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>

[<span data-ttu-id="f3375-126">追蹤問題 Announcements#325</span><span class="sxs-lookup"><span data-stu-id="f3375-126">Tracking Issue Announcements#325</span></span>](https://github.com/aspnet/Announcements/issues/325)

<span data-ttu-id="f3375-127">這項變更已於 ASP.NET Core 3.0-preview 1 推出。</span><span class="sxs-lookup"><span data-stu-id="f3375-127">This change was introduced in ASP.NET Core 3.0-preview 1.</span></span> 

<span data-ttu-id="f3375-128">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-128">**Old behavior**</span></span>

<span data-ttu-id="f3375-129">在 ASP.NET Core 3.0 以前，當您新增 `Microsoft.AspNetCore.App` 或 `Microsoft.AspNetCore.All` 的套件參考時，它會包含 EF Core 及部分 EF Core 資料提供者 (例如 SQL Server 提供者)。</span><span class="sxs-lookup"><span data-stu-id="f3375-129">Before ASP.NET Core 3.0, when you added a package reference to `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All`, it would include EF Core and some of the EF Core data providers like the SQL Server provider.</span></span>

<span data-ttu-id="f3375-130">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-130">**New behavior**</span></span>

<span data-ttu-id="f3375-131">從 3.0 開始，ASP.NET Core 共用架構不會包含 EF Core 或任何 EF Core 資料提供者。</span><span class="sxs-lookup"><span data-stu-id="f3375-131">Starting in 3.0, the ASP.NET Core shared framework doesn't include EF Core or any EF Core data providers.</span></span>

<span data-ttu-id="f3375-132">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-132">**Why**</span></span>

<span data-ttu-id="f3375-133">在這項變更之前，取得 EF Core 會根據應用程式是否以 ASP.NET Core 和 SQL Server 為目標而需要不同的步驟。</span><span class="sxs-lookup"><span data-stu-id="f3375-133">Before this change, getting EF Core required different steps depending on whether the application targeted ASP.NET Core and SQL Server or not.</span></span> <span data-ttu-id="f3375-134">此外，升級 ASP.NET Core 會強制升級 EF Core 和 SQL Server 提供者，這不一定符合需求。</span><span class="sxs-lookup"><span data-stu-id="f3375-134">Also, upgrading ASP.NET Core forced the upgrade of EF Core and the SQL Server provider, which isn't always desirable.</span></span>

<span data-ttu-id="f3375-135">透過這項變更，取得 EF Core 的體驗對所有提供者、支援的 .NET 實作和應用程式類型都相同。</span><span class="sxs-lookup"><span data-stu-id="f3375-135">With this change, the experience of getting EF Core is the same across all providers, supported .NET implementations and application types.</span></span>
<span data-ttu-id="f3375-136">開發人員現在也可以精確控制何時升級 EF Core 和 EF Core 資料提供者。</span><span class="sxs-lookup"><span data-stu-id="f3375-136">Developers can also now control exactly when EF Core and EF Core data providers are upgraded.</span></span>

<span data-ttu-id="f3375-137">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-137">**Mitigations**</span></span>

<span data-ttu-id="f3375-138">若要在 ASP.NET Core 3.0 應用程式或其他支援的應用程式中使用 EF Core，請明確將套件參考加入應用程式會使用的 EF Core 資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="f3375-138">To use EF Core in an ASP.NET Core 3.0 application or any other supported application, explicitly add a package reference to the EF Core database provider that your application will use.</span></span>

## <a name="the-ef-core-command-line-tool-dotnet-ef-is-no-longer-part-of-the-net-core-sdk"></a><span data-ttu-id="f3375-139">EF Core 命令列工具 dotnet ef 不再是 .NET Core SDK 的一部分</span><span class="sxs-lookup"><span data-stu-id="f3375-139">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>

[<span data-ttu-id="f3375-140">追蹤問題 #14016</span><span class="sxs-lookup"><span data-stu-id="f3375-140">Tracking Issue #14016</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14016)

<span data-ttu-id="f3375-141">EF Core 3.0 preview 4 和對應的 .NET Core SDK 版本中引進了這項變更。</span><span class="sxs-lookup"><span data-stu-id="f3375-141">This change was introduced in EF Core 3.0-preview 4 and the corresponding version of the .NET Core SDK.</span></span>

<span data-ttu-id="f3375-142">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-142">**Old behavior**</span></span>

<span data-ttu-id="f3375-143">在 3.0 之前，`dotnet ef` 工具包含在 .NET Core SDK，並可以輕易地從任何專案的命令列使用，而不需要額外步驟。</span><span class="sxs-lookup"><span data-stu-id="f3375-143">Before 3.0, the `dotnet ef` tool was included in the .NET Core SDK and was readily available to use from the command line from any project without requiring extra steps.</span></span> 

<span data-ttu-id="f3375-144">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-144">**New behavior**</span></span>

<span data-ttu-id="f3375-145">從 3.0 開始，.NET SDK 不包含 `dotnet ef` 工具，因此您必須明確地將它安裝為本機或全域工具才能使用。</span><span class="sxs-lookup"><span data-stu-id="f3375-145">Starting in 3.0, the .NET SDK does not incude the `dotnet ef` tool, so before you can use it you have to explicitly install it as a local or global tool.</span></span> 

<span data-ttu-id="f3375-146">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-146">**Why**</span></span>

<span data-ttu-id="f3375-147">這項變更可讓我們將 `dotnet ef` 當作 NuGet 上一般的 .NET CLI 工具來散發和更新，這點與 EF Core 3.0 一律當作 NuGet 套件散發的事實一致。</span><span class="sxs-lookup"><span data-stu-id="f3375-147">This change allows us to distribute and update `dotnet ef` as a regular .NET CLI tool on NuGet, consistent with the fact that the EF Core 3.0 is also always distributed as a NuGet package.</span></span>

<span data-ttu-id="f3375-148">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-148">**Mitigations**</span></span>

<span data-ttu-id="f3375-149">若要能夠管理移轉或支撐 `DbContext`，請使用 `dotnet tool install` 命令安裝 `dotnet-ef`。</span><span class="sxs-lookup"><span data-stu-id="f3375-149">To be able to manage migrations or scaffold a `DbContext`, install `dotnet-ef` using the `dotnet tool install` command.</span></span>
<span data-ttu-id="f3375-150">例如，若要將它安裝為全域工具，您可以鍵入這個命令：</span><span class="sxs-lookup"><span data-stu-id="f3375-150">For example, to install it as a global tool, you can type this command:</span></span>

  ``` console
  $ dotnet tool install --global dotnet-ef --version <exact-version>
  ```

<span data-ttu-id="f3375-151">您也可以在還原專案相依性時取得它作為本機工具 (該專案是使用[工具資訊清單檔](https://github.com/dotnet/cli/issues/10288)將它宣告為工具相依性)。</span><span class="sxs-lookup"><span data-stu-id="f3375-151">You can also obtain it a local tool when you restore the dependencies of a project that declares it as a tooling dependency using a [tool manifest file](https://github.com/dotnet/cli/issues/10288).</span></span>

## <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a><span data-ttu-id="f3375-152">FromSql、ExecuteSql 和 ExecuteSqlAsync 已重新命名</span><span class="sxs-lookup"><span data-stu-id="f3375-152">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>

[<span data-ttu-id="f3375-153">追蹤問題 #10996</span><span class="sxs-lookup"><span data-stu-id="f3375-153">Tracking Issue #10996</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10996)

<span data-ttu-id="f3375-154">這項變更已於 EF Core 3.0-preview 4 推出。</span><span class="sxs-lookup"><span data-stu-id="f3375-154">This change was introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3375-155">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-155">**Old behavior**</span></span>

<span data-ttu-id="f3375-156">在 EF Core 3.0 之前，這些方法名稱已多載以使用一般字串，或應插入至 SQL 和參數的字串。</span><span class="sxs-lookup"><span data-stu-id="f3375-156">Before EF Core 3.0, these method names were overloaded to work with either a normal string or a string that should be interpolated into SQL and parameters.</span></span>

<span data-ttu-id="f3375-157">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-157">**New behavior**</span></span>

<span data-ttu-id="f3375-158">從 EF Core 3.0 開始，請使用 `FromSqlRaw`、`ExecuteSqlRaw` 和 `ExecuteSqlRawAsync` 建立參數化查詢，其中參數會分別從查詢字串傳遞。</span><span class="sxs-lookup"><span data-stu-id="f3375-158">Starting with EF Core 3.0, use `FromSqlRaw`, `ExecuteSqlRaw`, and `ExecuteSqlRawAsync` to create a parameterized query where the parameters are passed separately from the query string.</span></span>
<span data-ttu-id="f3375-159">例如：</span><span class="sxs-lookup"><span data-stu-id="f3375-159">For example:</span></span>

```C#
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

<span data-ttu-id="f3375-160">使用 `FromSqlInterpolated`、`ExecuteSqlInterpolated` 和 `ExecuteSqlInterpolatedAsync` 建立參數化查詢，其中參數會作為插入查詢字串的一部分傳回。</span><span class="sxs-lookup"><span data-stu-id="f3375-160">Use `FromSqlInterpolated`, `ExecuteSqlInterpolated`, and `ExecuteSqlInterpolatedAsync` to create a parameterized query where the parameters are passed as part of an interpolated query string.</span></span>
<span data-ttu-id="f3375-161">例如：</span><span class="sxs-lookup"><span data-stu-id="f3375-161">For example:</span></span>

```C#
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

<span data-ttu-id="f3375-162">請注意，上述兩個查詢都會產生具有相同 SQL 參數的相同參數化 SQL。</span><span class="sxs-lookup"><span data-stu-id="f3375-162">Note that both of the queries above will produce the same parameterized SQL with the same SQL parameters.</span></span>

<span data-ttu-id="f3375-163">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-163">**Why**</span></span>

<span data-ttu-id="f3375-164">像這樣的方法多載，使得原本要呼叫插入字串方法很容易意外呼叫原始字串方法，或反之。</span><span class="sxs-lookup"><span data-stu-id="f3375-164">Method overloads like this make it very easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span>
<span data-ttu-id="f3375-165">這可能會導致查詢在應該參數化時不進行參數化。</span><span class="sxs-lookup"><span data-stu-id="f3375-165">This could result in queries not being parameterized when they should have been.</span></span>

<span data-ttu-id="f3375-166">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-166">**Mitigations**</span></span>

<span data-ttu-id="f3375-167">切換至使用新的方法名稱。</span><span class="sxs-lookup"><span data-stu-id="f3375-167">Switch to use the new method names.</span></span>

## <a name="query-execution-is-logged-at-debug-level"></a><span data-ttu-id="f3375-168">查詢執行會在 Debug 層級記錄</span><span class="sxs-lookup"><span data-stu-id="f3375-168">Query execution is logged at Debug level</span></span>

[<span data-ttu-id="f3375-169">追蹤問題 #14523</span><span class="sxs-lookup"><span data-stu-id="f3375-169">Tracking Issue #14523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

<span data-ttu-id="f3375-170">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="f3375-170">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="f3375-171">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-171">**Old behavior**</span></span>

<span data-ttu-id="f3375-172">在 EF Core 3.0 以前，查詢和其他命令的執行會在 `Info` 層級記錄。</span><span class="sxs-lookup"><span data-stu-id="f3375-172">Before EF Core 3.0, execution of queries and other commands was logged at the `Info` level.</span></span>

<span data-ttu-id="f3375-173">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-173">**New behavior**</span></span>

<span data-ttu-id="f3375-174">從 EF Core 3.0 開始，命令/SQL 執行的記錄是在 `Debug` 層級。</span><span class="sxs-lookup"><span data-stu-id="f3375-174">Starting with EF Core 3.0, logging of command/SQL execution is at the `Debug` level.</span></span>

<span data-ttu-id="f3375-175">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-175">**Why**</span></span>

<span data-ttu-id="f3375-176">這項變更的目的是為了減少 `Info` 記錄層級的干擾。</span><span class="sxs-lookup"><span data-stu-id="f3375-176">This change was made to reduce the noise at the `Info` log level.</span></span>

<span data-ttu-id="f3375-177">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-177">**Mitigations**</span></span>

<span data-ttu-id="f3375-178">此記錄事件是由 `RelationalEventId.CommandExecuting` 定義，事件識別碼為 20100。</span><span class="sxs-lookup"><span data-stu-id="f3375-178">This logging event is defined by `RelationalEventId.CommandExecuting` with event ID 20100.</span></span>
<span data-ttu-id="f3375-179">若要重新在 `Info` 層級記錄 SQL，請明確在 `OnConfiguring` 或 `AddDbContext` 中設定層級。</span><span class="sxs-lookup"><span data-stu-id="f3375-179">To log SQL at the `Info` level again, explicitly configure the level in `OnConfiguring` or `AddDbContext`.</span></span>
<span data-ttu-id="f3375-180">例如：</span><span class="sxs-lookup"><span data-stu-id="f3375-180">For example:</span></span>
```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Info)));
```

## <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a><span data-ttu-id="f3375-181">實體執行個體上不會再設定暫存索引鍵值</span><span class="sxs-lookup"><span data-stu-id="f3375-181">Temporary key values are no longer set onto entity instances</span></span>

[<span data-ttu-id="f3375-182">追蹤問題 #12378</span><span class="sxs-lookup"><span data-stu-id="f3375-182">Tracking Issue #12378</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

<span data-ttu-id="f3375-183">這項變更已於 EF Core 3.0-preview 2 推出。</span><span class="sxs-lookup"><span data-stu-id="f3375-183">This change was introduced in EF Core 3.0-preview 2.</span></span>

<span data-ttu-id="f3375-184">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-184">**Old behavior**</span></span>

<span data-ttu-id="f3375-185">在 EF Core 3.0 以前，會對所有索引鍵屬性指派暫存值，這些屬性稍後會有資料庫產生的實值。</span><span class="sxs-lookup"><span data-stu-id="f3375-185">Before EF Core 3.0, temporary values were assigned to all key properties that would later have a real value generated by the database.</span></span>
<span data-ttu-id="f3375-186">這些暫存值通常是龐大的負值。</span><span class="sxs-lookup"><span data-stu-id="f3375-186">Usually these temporary values were large negative numbers.</span></span>

<span data-ttu-id="f3375-187">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-187">**New behavior**</span></span>

<span data-ttu-id="f3375-188">從 3.0 開始，EF Core 會將暫存索引鍵值儲存為實體追蹤資訊的一部分，至於索引鍵屬性本身則保持不變。</span><span class="sxs-lookup"><span data-stu-id="f3375-188">Starting with 3.0, EF Core stores the temporary key value as part of the entity's tracking information, and leaves the key property itself unchanged.</span></span>

<span data-ttu-id="f3375-189">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-189">**Why**</span></span>

<span data-ttu-id="f3375-190">這項變更的目的是為了防止在將某個 `DbContext` 執行個體先前追蹤的實體移至不同的 `DbContext` 執行個體時，錯誤地把暫存索引鍵值變成永久值。</span><span class="sxs-lookup"><span data-stu-id="f3375-190">This change was made to prevent temporary key values from erroneously becoming permanent when an entity that has been previously tracked by some `DbContext` instance is moved to a different `DbContext` instance.</span></span> 

<span data-ttu-id="f3375-191">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-191">**Mitigations**</span></span>

<span data-ttu-id="f3375-192">若應用程式會將主索引鍵指派給外部索引鍵以形成實體間關聯，則可能會在主索引鍵是由存放區產生並屬於 `Added` 狀態的實體時採用舊行為。</span><span class="sxs-lookup"><span data-stu-id="f3375-192">Applications that assign primary key values onto foreign keys to form associations between entities may depend on the old behavior if the primary keys are store-generated and belong to entities in the `Added` state.</span></span>
<span data-ttu-id="f3375-193">這可透過下列方式來避免：</span><span class="sxs-lookup"><span data-stu-id="f3375-193">This can be avoided by:</span></span>
* <span data-ttu-id="f3375-194">不使用存放區產生的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="f3375-194">Not using store-generated keys.</span></span>
* <span data-ttu-id="f3375-195">設定導覽屬性以形成關聯性，而不是設定外部索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="f3375-195">Setting navigation properties to form relationships instead of setting foreign key values.</span></span>
* <span data-ttu-id="f3375-196">從實體的追蹤資訊取得實際暫存索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="f3375-196">Obtain the actual temporary key values from the entity's tracking information.</span></span>
<span data-ttu-id="f3375-197">例如，`context.Entry(blog).Property(e => e.Id).CurrentValue` 會傳回暫存值，即使尚未設定 `blog.Id` 本身也一樣。</span><span class="sxs-lookup"><span data-stu-id="f3375-197">For example, `context.Entry(blog).Property(e => e.Id).CurrentValue` will return the temporary value even though `blog.Id` itself hasn't been set.</span></span>

## <a name="detectchanges-honors-store-generated-key-values"></a><span data-ttu-id="f3375-198">DetectChanges 接受存放區產生的索引鍵值</span><span class="sxs-lookup"><span data-stu-id="f3375-198">DetectChanges honors store-generated key values</span></span>

[<span data-ttu-id="f3375-199">追蹤問題 #14616</span><span class="sxs-lookup"><span data-stu-id="f3375-199">Tracking Issue #14616</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

<span data-ttu-id="f3375-200">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="f3375-200">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="f3375-201">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-201">**Old behavior**</span></span>

<span data-ttu-id="f3375-202">在 EF Core 3.0 以前，`DetectChanges` 所發現未被追蹤的實體會以 `Added` 狀態追蹤，並在呼叫 `SaveChanges` 時以新的資料列插入。</span><span class="sxs-lookup"><span data-stu-id="f3375-202">Before EF Core 3.0, an untracked entity found by `DetectChanges` would be tracked in the `Added` state and inserted as a new row when `SaveChanges` is called.</span></span>

<span data-ttu-id="f3375-203">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-203">**New behavior**</span></span>

<span data-ttu-id="f3375-204">從 EF Core 3.0 開始，如果實體使用產生的索引鍵值並已設定一些索引鍵值，則實體會以 `Modified` 狀態追蹤。</span><span class="sxs-lookup"><span data-stu-id="f3375-204">Starting with EF Core 3.0, if an entity is using generated key values and some key value is set, then the entity will be tracked in the `Modified` state.</span></span>
<span data-ttu-id="f3375-205">這表示實體的資料列假設存在，而且會在呼叫 `SaveChanges` 時更新。</span><span class="sxs-lookup"><span data-stu-id="f3375-205">This means that a row for the entity is assumed to exist and it will be updated when `SaveChanges` is called.</span></span>
<span data-ttu-id="f3375-206">如果未設定索引鍵值，或者如果實體類型未使用產生的索引鍵，則新的實體仍會如同舊版以 `Added` 追蹤。</span><span class="sxs-lookup"><span data-stu-id="f3375-206">If the key value isn't set, or if the entity type isn't using generated keys, then the new entity will still be tracked as `Added` as in previous versions.</span></span>

<span data-ttu-id="f3375-207">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-207">**Why**</span></span>

<span data-ttu-id="f3375-208">這項變更的目的是為了更輕鬆一致地使用中斷連接的實體圖形，同時使用存放區產生的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="f3375-208">This change was made to make it easier and more consistent to work with disconnected entity graphs while using store-generated keys.</span></span>

<span data-ttu-id="f3375-209">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-209">**Mitigations**</span></span>

<span data-ttu-id="f3375-210">如果實體類型已設定為使用產生的索引鍵，但針對新的執行個體明確設定了索引鍵值，這項變更可能會中斷應用程式。</span><span class="sxs-lookup"><span data-stu-id="f3375-210">This change can break an application if an entity type is configured to use generated keys but key values are explicitly set for new instances.</span></span>
<span data-ttu-id="f3375-211">修正方法是明確設定索引鍵屬性不使用產生的值。</span><span class="sxs-lookup"><span data-stu-id="f3375-211">The fix is to explicitly configure the key properties to not use generated values.</span></span>
<span data-ttu-id="f3375-212">例如，使用 Fluent API：</span><span class="sxs-lookup"><span data-stu-id="f3375-212">For example, with the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

<span data-ttu-id="f3375-213">或者，使用資料註解：</span><span class="sxs-lookup"><span data-stu-id="f3375-213">Or with data annotations:</span></span>

```C#
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```

## <a name="cascade-deletions-now-happen-immediately-by-default"></a><span data-ttu-id="f3375-214">現在預設會立即發生串聯刪除</span><span class="sxs-lookup"><span data-stu-id="f3375-214">Cascade deletions now happen immediately by default</span></span>

[<span data-ttu-id="f3375-215">追蹤問題 #10114</span><span class="sxs-lookup"><span data-stu-id="f3375-215">Tracking Issue #10114</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

<span data-ttu-id="f3375-216">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="f3375-216">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="f3375-217">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-217">**Old behavior**</span></span>

<span data-ttu-id="f3375-218">在 3.0 以前，除非呼叫 SaveChanges，否則 EF Core 不會套用串聯動作 (刪除必要主體或提供必要主體關聯性時刪除相依實體)。</span><span class="sxs-lookup"><span data-stu-id="f3375-218">Before 3.0, EF Core applied cascading actions (deleting dependent entities when a required principal is deleted or when the relationship to a required principal is severed) did not happen until SaveChanges was called.</span></span>

<span data-ttu-id="f3375-219">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-219">**New behavior**</span></span>

<span data-ttu-id="f3375-220">從 3.0 開始，EF Core 會在偵測到觸發條件時立即套用串聯動作。</span><span class="sxs-lookup"><span data-stu-id="f3375-220">Starting with 3.0, EF Core applies cascading actions as soon as the triggering condition is detected.</span></span>
<span data-ttu-id="f3375-221">例如，呼叫 `context.Remove()` 刪除主要實體會導致所有追蹤的相關必要相依項目也會立即設定為 `Deleted`。</span><span class="sxs-lookup"><span data-stu-id="f3375-221">For example, calling `context.Remove()` to delete a principal entity will result in all tracked related required dependents also being set to `Deleted` immediately.</span></span>

<span data-ttu-id="f3375-222">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-222">**Why**</span></span>

<span data-ttu-id="f3375-223">這項變更的目的是為了改善資料繫結和稽核情節的體驗，在這些情節中了解呼叫 `SaveChanges`「之前」將刪除哪些實體是很重要的。</span><span class="sxs-lookup"><span data-stu-id="f3375-223">This change was made to improve the experience for data binding and auditing scenarios where it is important to understand which entities will be deleted _before_ `SaveChanges` is called.</span></span>

<span data-ttu-id="f3375-224">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-224">**Mitigations**</span></span>

<span data-ttu-id="f3375-225">透過設定 `context.ChangedTracker` 可以還原舊行為。</span><span class="sxs-lookup"><span data-stu-id="f3375-225">The previous behavior can be restored through settings on `context.ChangedTracker`.</span></span>
<span data-ttu-id="f3375-226">例如：</span><span class="sxs-lookup"><span data-stu-id="f3375-226">For example:</span></span>

```C#
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```

## <a name="deletebehaviorrestrict-has-cleaner-semantics"></a><span data-ttu-id="f3375-227">DeleteBehavior.Restrict 具有更簡潔的語意</span><span class="sxs-lookup"><span data-stu-id="f3375-227">DeleteBehavior.Restrict has cleaner semantics</span></span>

[<span data-ttu-id="f3375-228">追蹤問題 #12661</span><span class="sxs-lookup"><span data-stu-id="f3375-228">Tracking Issue #12661</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12661)

<span data-ttu-id="f3375-229">這項變更將於 EF Core 3.0-preview 5 中引進。</span><span class="sxs-lookup"><span data-stu-id="f3375-229">This change will be introduced in EF Core 3.0-preview 5.</span></span>

<span data-ttu-id="f3375-230">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-230">**Old behavior**</span></span>

<span data-ttu-id="f3375-231">在 3.0 以前，`DeleteBehavior.Restrict` 使用 `Restrict` 語意在資料庫中建立外部索引鍵，但也以不明顯的方式變更內部修復。</span><span class="sxs-lookup"><span data-stu-id="f3375-231">Before 3.0, `DeleteBehavior.Restrict` created foreign keys in the database with `Restrict` semantics, but also changed internal fixup in a non-obvious way.</span></span>

<span data-ttu-id="f3375-232">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-232">**New behavior**</span></span>

<span data-ttu-id="f3375-233">從 3.0 開始，`DeleteBehavior.Restrict` 會確保外部索引鍵使用 `Restrict` 語意來建立 (也就是不會有重疊顯示，拋出條件約束違規)，不會影響 EF 內部修復。</span><span class="sxs-lookup"><span data-stu-id="f3375-233">Starting with 3.0, `DeleteBehavior.Restrict` ensures that foreign keys are created with `Restrict` semantics--that is, no cascades; throw on constraint violation--without also impacting EF internal fixup.</span></span>

<span data-ttu-id="f3375-234">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-234">**Why**</span></span>

<span data-ttu-id="f3375-235">這項變更可藉由直覺方式提升使用 `DeleteBehavior` 的體驗，而不會發生非預期的副作用。</span><span class="sxs-lookup"><span data-stu-id="f3375-235">This change was made to improve the experience for using `DeleteBehavior` in an intuitive manner, without unexpected side-effects.</span></span>

<span data-ttu-id="f3375-236">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-236">**Mitigations**</span></span>

<span data-ttu-id="f3375-237">使用 `DeleteBehavior.ClientNoAction` 可以還原舊行為。</span><span class="sxs-lookup"><span data-stu-id="f3375-237">The previous behavior can be restored by using `DeleteBehavior.ClientNoAction`.</span></span>

## <a name="query-types-are-consolidated-with-entity-types"></a><span data-ttu-id="f3375-238">查詢類型會與實體類型合併</span><span class="sxs-lookup"><span data-stu-id="f3375-238">Query types are consolidated with entity types</span></span>

[<span data-ttu-id="f3375-239">追蹤問題 #14194</span><span class="sxs-lookup"><span data-stu-id="f3375-239">Tracking Issue #14194</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

<span data-ttu-id="f3375-240">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="f3375-240">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="f3375-241">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-241">**Old behavior**</span></span>

<span data-ttu-id="f3375-242">在 EF Core 3.0 以前，[查詢類型](xref:core/modeling/query-types)可讓您查詢未以結構化方式定義主索引鍵的資料。</span><span class="sxs-lookup"><span data-stu-id="f3375-242">Before EF Core 3.0, [query types](xref:core/modeling/query-types) were a means to query data that doesn't define a primary key in a structured way.</span></span>
<span data-ttu-id="f3375-243">換句話說，查詢類型是用於對應沒有索引鍵的實體類型 (較有可能來自檢視，但也有可能來自資料表)，而一般實體類型是用於索引鍵可供使用時 (較有可能來自資料表，但也有可能來自檢視)。</span><span class="sxs-lookup"><span data-stu-id="f3375-243">That is, a query type was used for mapping entity types without keys (more likely from a view, but possibly from a table) while a regular entity type was used when a key was available (more likely from a table, but possibly from a view).</span></span>

<span data-ttu-id="f3375-244">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-244">**New behavior**</span></span>

<span data-ttu-id="f3375-245">查詢類型現在會成為沒有主索引鍵的實體類型。</span><span class="sxs-lookup"><span data-stu-id="f3375-245">A query type now becomes just an entity type without a primary key.</span></span>
<span data-ttu-id="f3375-246">無索引鍵的實體類型功能與舊版查詢類型相同。</span><span class="sxs-lookup"><span data-stu-id="f3375-246">Keyless entity types have the same functionality as query types in previous versions.</span></span>

<span data-ttu-id="f3375-247">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-247">**Why**</span></span>

<span data-ttu-id="f3375-248">這項變更的目的是為了降低查詢類型用途的混淆。</span><span class="sxs-lookup"><span data-stu-id="f3375-248">This change was made to reduce the confusion around the purpose of query types.</span></span>
<span data-ttu-id="f3375-249">具體來說，它們是無索引鍵的實體類型，因此本質上是唯讀的，但不應該只因為實體類型必須是唯讀就使用。</span><span class="sxs-lookup"><span data-stu-id="f3375-249">Specifically, they are keyless entity types and they are inherently read-only because of this, but they should not be used just because an entity type needs to be read-only.</span></span>
<span data-ttu-id="f3375-250">同樣地，它們通常會對應至檢視，但這只是因為檢視通常未定義索引鍵。</span><span class="sxs-lookup"><span data-stu-id="f3375-250">Likewise, they are often mapped to views, but this is only because views often don't define keys.</span></span>

<span data-ttu-id="f3375-251">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-251">**Mitigations**</span></span>

<span data-ttu-id="f3375-252">API 的下列組件現已淘汰：</span><span class="sxs-lookup"><span data-stu-id="f3375-252">The following parts of the API are now obsolete:</span></span>
* <span data-ttu-id="f3375-253">**`ModelBuilder.Query<>()`** - 必須改為呼叫 `ModelBuilder.Entity<>().HasNoKey()` 將實體類型標示為沒有索引鍵。</span><span class="sxs-lookup"><span data-stu-id="f3375-253">**`ModelBuilder.Query<>()`** - Instead `ModelBuilder.Entity<>().HasNoKey()` needs to be called to mark an entity type as having no keys.</span></span>
<span data-ttu-id="f3375-254">為了避免在必須有主索引鍵但不符合慣例時設定錯誤，目前仍未將此設定為慣例。</span><span class="sxs-lookup"><span data-stu-id="f3375-254">This would still not be configured by convention to avoid misconfiguration when a primary key is expected, but doesn't match the convention.</span></span>
* <span data-ttu-id="f3375-255">**`DbQuery<>`** - 應改用 `DbSet<>`。</span><span class="sxs-lookup"><span data-stu-id="f3375-255">**`DbQuery<>`** - Instead `DbSet<>` should be used.</span></span>
* <span data-ttu-id="f3375-256">**`DbContext.Query<>()`** - 應改用 `DbContext.Set<>()`。</span><span class="sxs-lookup"><span data-stu-id="f3375-256">**`DbContext.Query<>()`** - Instead `DbContext.Set<>()` should be used.</span></span>

## <a name="configuration-api-for-owned-type-relationships-has-changed"></a><span data-ttu-id="f3375-257">自有類型關聯性的設定 API 已變更</span><span class="sxs-lookup"><span data-stu-id="f3375-257">Configuration API for owned type relationships has changed</span></span>

<span data-ttu-id="f3375-258">[追蹤問題 #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[追蹤問題 #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[追蹤問題 #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span><span class="sxs-lookup"><span data-stu-id="f3375-258">[Tracking Issue #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[Tracking Issue #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[Tracking Issue #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span></span>

<span data-ttu-id="f3375-259">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="f3375-259">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="f3375-260">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-260">**Old behavior**</span></span>

<span data-ttu-id="f3375-261">在 EF Core 3.0 以前，會在呼叫 `OwnsOne` 或 `OwnsMany` 之後直接執行自有關聯性的設定。</span><span class="sxs-lookup"><span data-stu-id="f3375-261">Before EF Core 3.0, configuration of the owned relationship was performed directly after the `OwnsOne` or `OwnsMany` call.</span></span> 

<span data-ttu-id="f3375-262">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-262">**New behavior**</span></span>

<span data-ttu-id="f3375-263">從 EF Core 3.0 開始，現在會有 Fluent API 使用 `WithOwner()` 將導覽屬性設定為擁有者。</span><span class="sxs-lookup"><span data-stu-id="f3375-263">Starting with EF Core 3.0, there is now fluent API to configure a navigation property to the owner using `WithOwner()`.</span></span>
<span data-ttu-id="f3375-264">例如：</span><span class="sxs-lookup"><span data-stu-id="f3375-264">For example:</span></span>

```C#
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

<span data-ttu-id="f3375-265">擁有者與自有之間關聯性的相關設定現在應該在 `WithOwner()` 之後鏈結，類似於其他關聯性的設定方式。</span><span class="sxs-lookup"><span data-stu-id="f3375-265">The configuration related to the relationship between owner and owned should now be chained after `WithOwner()` similarly to how other relationships are configured.</span></span>
<span data-ttu-id="f3375-266">但自有類型本身的設定仍會在 `OwnsOne()/OwnsMany()` 之後鏈結。</span><span class="sxs-lookup"><span data-stu-id="f3375-266">While the configuration for the owned type itself would still be chained after `OwnsOne()/OwnsMany()`.</span></span>
<span data-ttu-id="f3375-267">例如：</span><span class="sxs-lookup"><span data-stu-id="f3375-267">For example:</span></span>

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

<span data-ttu-id="f3375-268">此外，使用自有類型目標呼叫 `Entity()`、`HasOne()` 或 `Set()` 現在會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="f3375-268">Additionally calling `Entity()`, `HasOne()`, or `Set()` with an owned type target will now throw an exception.</span></span>

<span data-ttu-id="f3375-269">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-269">**Why**</span></span>

<span data-ttu-id="f3375-270">這項變更的目的是為了更清楚地劃分設定自有類型本身，以及設定自有類型的「關聯性」。</span><span class="sxs-lookup"><span data-stu-id="f3375-270">This change was made to create a cleaner separation between configuring the owned type itself and the _relationship to_ the owned type.</span></span>
<span data-ttu-id="f3375-271">如此可避免 `HasForeignKey` 等方法的模稜兩可和混淆。</span><span class="sxs-lookup"><span data-stu-id="f3375-271">This in turn removes ambiguity and confusion around methods like `HasForeignKey`.</span></span>

<span data-ttu-id="f3375-272">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-272">**Mitigations**</span></span>

<span data-ttu-id="f3375-273">將自有類型關聯性的設定變更為使用新的 API 介面，如上述範例所示。</span><span class="sxs-lookup"><span data-stu-id="f3375-273">Change configuration of owned type relationships to use the new API surface as shown in the example above.</span></span>

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="f3375-274">現在可選用以主體來共用資料表的相依實體</span><span class="sxs-lookup"><span data-stu-id="f3375-274">Dependent entities sharing the table with the principal are now optional</span></span>

[<span data-ttu-id="f3375-275">追蹤問題 #9005</span><span class="sxs-lookup"><span data-stu-id="f3375-275">Tracking Issue #9005</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9005)

<span data-ttu-id="f3375-276">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="f3375-276">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3375-277">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-277">**Old behavior**</span></span>

<span data-ttu-id="f3375-278">請考慮下列模型：</span><span class="sxs-lookup"><span data-stu-id="f3375-278">Consider the following model:</span></span>
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
<span data-ttu-id="f3375-279">在 EF Core 3.0 之前，如果 `OrderDetails` 由 `Order` 擁有，或明確對應至相同的資料表，則在新增新的 `Order` 時一律需要 `OrderDetails` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="f3375-279">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then an `OrderDetails` instance was always required when adding a new `Order`.</span></span>


<span data-ttu-id="f3375-280">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-280">**New behavior**</span></span>

<span data-ttu-id="f3375-281">從 3.0 開始，EF 允許新增 `Order` 而不需要 `OrderDetails`，並會對應所有 `OrderDetails` 屬性，除了可為 Null 之資料行的主索引鍵以外。</span><span class="sxs-lookup"><span data-stu-id="f3375-281">Starting with 3.0, EF Core allows to add an `Order` without an `OrderDetails` and maps all of the `OrderDetails` properties except the primary key to nullable columns.</span></span>
<span data-ttu-id="f3375-282">查詢時，如果任何必要的屬性不具有值，或如果其具有主索引鍵以外的不必要屬性，且所有屬性都是 `null`，則 EF Core 會將 `OrderDetails` 設為 `null`。</span><span class="sxs-lookup"><span data-stu-id="f3375-282">When querying EF Core sets `OrderDetails` to `null` if any of its required properties doesn't have a value or if it has no required properties besides the primary key and all properties are `null`.</span></span>

<span data-ttu-id="f3375-283">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-283">**Mitigations**</span></span>

<span data-ttu-id="f3375-284">如果您的模型具有與所有選擇性資料行共用相依資料表，但指向該資料表的導覽不預期為 `null`，則應修改應用程式，以處理當導覽為 `null` 時的情況。</span><span class="sxs-lookup"><span data-stu-id="f3375-284">If your model has a table sharing dependent with all optional columns, but the navigation pointing to it is not expected to be `null` then the application should be modified to handle cases when the navigation is `null`.</span></span> <span data-ttu-id="f3375-285">如果這不可行，則應將必要屬性新增至實體類型，或至少應有一個屬性指派其非 `null` 的值。</span><span class="sxs-lookup"><span data-stu-id="f3375-285">If this is not possible a required property should be added to the entity type or at least one property should have a non-`null` value assigned to it.</span></span>

## <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a><span data-ttu-id="f3375-286">共用具有並行語彙基元資料行的所有實體，都必須將其對應至屬性</span><span class="sxs-lookup"><span data-stu-id="f3375-286">All entities sharing a table with a concurrency token column have to map it to a property</span></span>

[<span data-ttu-id="f3375-287">追蹤問題 #14154</span><span class="sxs-lookup"><span data-stu-id="f3375-287">Tracking Issue #14154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14154)

<span data-ttu-id="f3375-288">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="f3375-288">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3375-289">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-289">**Old behavior**</span></span>

<span data-ttu-id="f3375-290">請考慮下列模型：</span><span class="sxs-lookup"><span data-stu-id="f3375-290">Consider the following model:</span></span>
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
<span data-ttu-id="f3375-291">在 EF Core 3.0之前，如果 `OrderDetails` 由 `Order` 擁有，或明確對應至相同資料表，那麼僅更新 `OrderDetails` 將不會更新用戶端上的 `Version` 值，且下一次更新將會失敗。</span><span class="sxs-lookup"><span data-stu-id="f3375-291">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then updating just `OrderDetails` will not update `Version` value on client and the next update will fail.</span></span>


<span data-ttu-id="f3375-292">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-292">**New behavior**</span></span>

<span data-ttu-id="f3375-293">從 3.0 開始，EF Core 會將新的 `Version` 值傳播至 `Order` (如果其擁有 `OrderDetails`)。</span><span class="sxs-lookup"><span data-stu-id="f3375-293">Starting with 3.0, EF Core propagates the new `Version` value to `Order` if it owns `OrderDetails`.</span></span> <span data-ttu-id="f3375-294">否則，在模型驗證期間會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="f3375-294">Otherwise an exception is thrown during model validation.</span></span>

<span data-ttu-id="f3375-295">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-295">**Why**</span></span>

<span data-ttu-id="f3375-296">這項變更的目的，是為了避免在僅更新對應至相同資料表的其中一個實體時，出現過時的並行語彙基元值。</span><span class="sxs-lookup"><span data-stu-id="f3375-296">This change was made to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

<span data-ttu-id="f3375-297">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-297">**Mitigations**</span></span>

<span data-ttu-id="f3375-298">共用資料表的所有實體，都必須包含對應至並行語彙基元資料行的屬性。</span><span class="sxs-lookup"><span data-stu-id="f3375-298">All entities sharing the table have to include a property that is mapped to the concurrency token column.</span></span> <span data-ttu-id="f3375-299">在陰影狀態中建立一個是可能的：</span><span class="sxs-lookup"><span data-stu-id="f3375-299">It's possible the create one in shadow-state:</span></span>
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

## <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a><span data-ttu-id="f3375-300">未對應類型的繼承屬性，現在會對應至所有衍生類型的單一資料行</span><span class="sxs-lookup"><span data-stu-id="f3375-300">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>

[<span data-ttu-id="f3375-301">追蹤問題 #13998</span><span class="sxs-lookup"><span data-stu-id="f3375-301">Tracking Issue #13998</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13998)

<span data-ttu-id="f3375-302">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="f3375-302">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3375-303">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-303">**Old behavior**</span></span>

<span data-ttu-id="f3375-304">請考慮下列模型：</span><span class="sxs-lookup"><span data-stu-id="f3375-304">Consider the following model:</span></span>
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

<span data-ttu-id="f3375-305">在 EF Core 3.0 之前，`ShippingAddress` 屬性會根據預設，為 `BulkOrder` 和 `Order` 對應至個別資料行。</span><span class="sxs-lookup"><span data-stu-id="f3375-305">Before EF Core 3.0, the `ShippingAddress` property would be mapped to separate columns for `BulkOrder` and `Order` by default.</span></span>

<span data-ttu-id="f3375-306">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-306">**New behavior**</span></span>

<span data-ttu-id="f3375-307">從 3.0 開始，EF Core 只會為 `ShippingAddress` 建立一個資料行。</span><span class="sxs-lookup"><span data-stu-id="f3375-307">Starting with 3.0, EF Core only creates one column for `ShippingAddress`.</span></span>

<span data-ttu-id="f3375-308">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-308">**Why**</span></span>

<span data-ttu-id="f3375-309">舊行為是非預期的。</span><span class="sxs-lookup"><span data-stu-id="f3375-309">The old behavoir was unexpected.</span></span>

<span data-ttu-id="f3375-310">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-310">**Mitigations**</span></span>

<span data-ttu-id="f3375-311">屬性仍可以在衍生類型上明確對應至個別資料行：</span><span class="sxs-lookup"><span data-stu-id="f3375-311">The property can still be explicitly mapped to separate column on the derived types:</span></span>

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

## <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a><span data-ttu-id="f3375-312">外部索引鍵屬性慣例不會再比對與主體屬性相同的名稱</span><span class="sxs-lookup"><span data-stu-id="f3375-312">The foreign key property convention no longer matches same name as the principal property</span></span>

[<span data-ttu-id="f3375-313">追蹤問題 #13274</span><span class="sxs-lookup"><span data-stu-id="f3375-313">Tracking Issue #13274</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

<span data-ttu-id="f3375-314">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="f3375-314">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="f3375-315">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-315">**Old behavior**</span></span>

<span data-ttu-id="f3375-316">請考慮下列模型：</span><span class="sxs-lookup"><span data-stu-id="f3375-316">Consider the following model:</span></span>
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
<span data-ttu-id="f3375-317">在 EF Core 3.0 以前，`CustomerId` 屬性依照慣例會用於外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="f3375-317">Before EF Core 3.0, the `CustomerId` property would be used for the foreign key by convention.</span></span>
<span data-ttu-id="f3375-318">不過，如果 `Order` 是自有類型，則這也會將 `CustomerId` 設為主索引鍵，而這通常不符合預期。</span><span class="sxs-lookup"><span data-stu-id="f3375-318">However, if `Order` is an owned type, then this would also make `CustomerId` the primary key and this isn't usually the expectation.</span></span>

<span data-ttu-id="f3375-319">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-319">**New behavior**</span></span>

<span data-ttu-id="f3375-320">從 3.0 開始，如果屬性的名稱與主體屬性相同，依照慣例，EF Core 不會嘗試將屬性用於外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="f3375-320">Starting with 3.0, EF Core doesn't try to use properties for foreign keys by convention if they have the same name as the principal property.</span></span>
<span data-ttu-id="f3375-321">但仍會比對與主體屬性名稱串連的主體類型名稱，以及與主體屬性名稱模式串連的導覽名稱。</span><span class="sxs-lookup"><span data-stu-id="f3375-321">Principal type name concatenated with principal property name, and navigation name concatenated with principal property name patterns are still matched.</span></span>
<span data-ttu-id="f3375-322">例如：</span><span class="sxs-lookup"><span data-stu-id="f3375-322">For example:</span></span>

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

<span data-ttu-id="f3375-323">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-323">**Why**</span></span>

<span data-ttu-id="f3375-324">這項變更的目的是為了避免錯誤地在自有類型上定義主索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="f3375-324">This change was made to avoid erroneously defining a primary key property on the owned type.</span></span>

<span data-ttu-id="f3375-325">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-325">**Mitigations**</span></span>

<span data-ttu-id="f3375-326">如果屬性預定會作為外部索引鍵，並因此成為主索引鍵的一部分，請明確進行這類設定。</span><span class="sxs-lookup"><span data-stu-id="f3375-326">If the property was intended to be the foreign key, and hence part of the primary key, then explicitly configure it as such.</span></span>

## <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a><span data-ttu-id="f3375-327">如果在 TransactionScope 完成之前未再使用，則資料庫連線現在已關閉</span><span class="sxs-lookup"><span data-stu-id="f3375-327">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>

[<span data-ttu-id="f3375-328">追蹤問題 #14218</span><span class="sxs-lookup"><span data-stu-id="f3375-328">Tracking Issue #14218</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14218)

<span data-ttu-id="f3375-329">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="f3375-329">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3375-330">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-330">**Old behavior**</span></span>

<span data-ttu-id="f3375-331">在 EF Core 3.0 之前，如果內容在 `TransactionScope` 內開啟連線，則當目前 `TransactionScope` 處於作用中時，連線將保持開啟。</span><span class="sxs-lookup"><span data-stu-id="f3375-331">Before EF Core 3.0, if the context opens the connection inside a `TransactionScope`, the connection remains open while the current `TransactionScope` is active.</span></span>

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

<span data-ttu-id="f3375-332">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-332">**New behavior**</span></span>

<span data-ttu-id="f3375-333">從 3.0 開始，EF Core 在使用完連線後會將其關閉。</span><span class="sxs-lookup"><span data-stu-id="f3375-333">Starting with 3.0, EF Core closes the connection as soon as it's done using it.</span></span>

<span data-ttu-id="f3375-334">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-334">**Why**</span></span>

<span data-ttu-id="f3375-335">此變更允許在相同 `TransactionScope` 中使用多個內容。</span><span class="sxs-lookup"><span data-stu-id="f3375-335">This change allows to use multiple contexts in the same `TransactionScope`.</span></span> <span data-ttu-id="f3375-336">新的行為也符合 EF6。</span><span class="sxs-lookup"><span data-stu-id="f3375-336">The new behavior also matches EF6.</span></span>

<span data-ttu-id="f3375-337">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-337">**Mitigations**</span></span>

<span data-ttu-id="f3375-338">如果連線需要保持開啟，則明確呼叫 `OpenConnection()` 可確保 EF Core 不會過早將其關閉：</span><span class="sxs-lookup"><span data-stu-id="f3375-338">If the connection needs to remain open explicit call to `OpenConnection()` will ensure that EF Core doesn't close it prematurely:</span></span>

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

## <a name="each-property-uses-independent-in-memory-integer-key-generation"></a><span data-ttu-id="f3375-339">每個屬性會使用獨立的記憶體內部整數索引鍵產生</span><span class="sxs-lookup"><span data-stu-id="f3375-339">Each property uses independent in-memory integer key generation</span></span>

[<span data-ttu-id="f3375-340">追蹤問題 #6872</span><span class="sxs-lookup"><span data-stu-id="f3375-340">Tracking Issue #6872</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

<span data-ttu-id="f3375-341">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="f3375-341">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3375-342">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-342">**Old behavior**</span></span>

<span data-ttu-id="f3375-343">在 EF Core 3.0 以前，會針對所有記憶體內部整數索引鍵屬性使用一個共用值產生器。</span><span class="sxs-lookup"><span data-stu-id="f3375-343">Before EF Core 3.0, one shared value generator was used for all in-memory integer key properties.</span></span>

<span data-ttu-id="f3375-344">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-344">**New behavior**</span></span>

<span data-ttu-id="f3375-345">從 EF Core 3.0 開始，當使用記憶體內部資料庫時，每個整數索引鍵屬性都會取得自己的值產生器。</span><span class="sxs-lookup"><span data-stu-id="f3375-345">Starting with EF Core 3.0, each integer key property gets its own value generator when using the in-memory database.</span></span>
<span data-ttu-id="f3375-346">此外，如果已刪除資料庫，則會重設所有資料表的索引鍵產生。</span><span class="sxs-lookup"><span data-stu-id="f3375-346">Also, if the database is deleted, then key generation is reset for all tables.</span></span>

<span data-ttu-id="f3375-347">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-347">**Why**</span></span>

<span data-ttu-id="f3375-348">這項變更的目的是為了讓記憶體內部索引鍵產生與實際資料庫索引鍵產生更加一致，並改善在使用記憶體內部資料庫時隔離個別測試的能力。</span><span class="sxs-lookup"><span data-stu-id="f3375-348">This change was made to align in-memory key generation more closely to real database key generation and to improve the ability to isolate tests from each other when using the in-memory database.</span></span>

<span data-ttu-id="f3375-349">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-349">**Mitigations**</span></span>

<span data-ttu-id="f3375-350">這可能會中斷需要設定特定記憶體內部索引鍵值的應用程式。</span><span class="sxs-lookup"><span data-stu-id="f3375-350">This can break an application that is relying on specific in-memory key values to be set.</span></span>
<span data-ttu-id="f3375-351">請考慮改為不依賴特定索引鍵值，或更新以符合新行為。</span><span class="sxs-lookup"><span data-stu-id="f3375-351">Consider instead not relying on specific key values, or updating to match the new behavior.</span></span>

## <a name="backing-fields-are-used-by-default"></a><span data-ttu-id="f3375-352">預設會使用支援欄位</span><span class="sxs-lookup"><span data-stu-id="f3375-352">Backing fields are used by default</span></span>

[<span data-ttu-id="f3375-353">追蹤問題 #12430</span><span class="sxs-lookup"><span data-stu-id="f3375-353">Tracking Issue #12430</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

<span data-ttu-id="f3375-354">這項變更已於 EF Core 3.0-preview 2 推出。</span><span class="sxs-lookup"><span data-stu-id="f3375-354">This change was introduced in EF Core 3.0-preview 2.</span></span>

<span data-ttu-id="f3375-355">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-355">**Old behavior**</span></span>

<span data-ttu-id="f3375-356">在 3.0 以前，即使屬性的支援欄位已知，EF Core 預設仍會使用屬性 getter 和 setter 方法來讀取和寫入屬性值。</span><span class="sxs-lookup"><span data-stu-id="f3375-356">Before 3.0, even if the backing field for a property was known, EF Core would still by default read and write the property value using the property getter and setter methods.</span></span>
<span data-ttu-id="f3375-357">例外是查詢執行，其中如果支援欄位已知，則會直接設定。</span><span class="sxs-lookup"><span data-stu-id="f3375-357">The exception to this was query execution, where the backing field would be set directly if known.</span></span>

<span data-ttu-id="f3375-358">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-358">**New behavior**</span></span>

<span data-ttu-id="f3375-359">從 EF Core 3.0 開始，如果屬性的支援欄位已知，則 EF Core 會一律使用支援欄位來讀取和寫入該屬性。</span><span class="sxs-lookup"><span data-stu-id="f3375-359">Starting with EF Core 3.0, if the backing field for a property is known, then EF Core will always read and write that property using the backing field.</span></span>
<span data-ttu-id="f3375-360">如果應用程式需要將額外的行為編碼到 getter 或 setter 方法中，這可能會導致應用程式中斷。</span><span class="sxs-lookup"><span data-stu-id="f3375-360">This could cause an application break if the application is relying on additional behavior coded into the getter or setter methods.</span></span>

<span data-ttu-id="f3375-361">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-361">**Why**</span></span>

<span data-ttu-id="f3375-362">這項變更的目的是為了防止 EF Core 預設在執行涉及實體的資料庫作業時，錯誤地觸發商務邏輯。</span><span class="sxs-lookup"><span data-stu-id="f3375-362">This change was made to prevent EF Core from erroneously triggering business logic by default when performing database operations involving the entities.</span></span>

<span data-ttu-id="f3375-363">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-363">**Mitigations**</span></span>

<span data-ttu-id="f3375-364">透過在 modelBuilder Fluent API 中設定屬性存取模式可以還原 3.0 以前的行為。</span><span class="sxs-lookup"><span data-stu-id="f3375-364">The pre-3.0 behavior can be restored through configuration of the property access mode in the modelBuilder fluent API.</span></span>
<span data-ttu-id="f3375-365">例如：</span><span class="sxs-lookup"><span data-stu-id="f3375-365">For example:</span></span>

```C#
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

## <a name="throw-if-multiple-compatible-backing-fields-are-found"></a><span data-ttu-id="f3375-366">找到多個相容的支援欄位時擲回</span><span class="sxs-lookup"><span data-stu-id="f3375-366">Throw if multiple compatible backing fields are found</span></span>

[<span data-ttu-id="f3375-367">追蹤問題 #12523</span><span class="sxs-lookup"><span data-stu-id="f3375-367">Tracking Issue #12523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

<span data-ttu-id="f3375-368">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="f3375-368">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3375-369">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-369">**Old behavior**</span></span>

<span data-ttu-id="f3375-370">在 EF Core 3.0 以前，如果有多個欄位符合尋找屬性支援欄位的規則，則會根據特定優先順序來選擇一個欄位。</span><span class="sxs-lookup"><span data-stu-id="f3375-370">Before EF Core 3.0, if multiple fields matched the rules for finding the backing field of a property, then one field would be chosen based on some precedence order.</span></span>
<span data-ttu-id="f3375-371">這可能會導致在模稜兩可的情況下使用錯誤的欄位。</span><span class="sxs-lookup"><span data-stu-id="f3375-371">This could cause the wrong field to be used in ambiguous cases.</span></span>

<span data-ttu-id="f3375-372">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-372">**New behavior**</span></span>

<span data-ttu-id="f3375-373">從 EF Core 3.0 開始，如果有多個欄位符合相同的屬性，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="f3375-373">Starting with EF Core 3.0, if multiple fields are matched to the same property, then an exception is thrown.</span></span>

<span data-ttu-id="f3375-374">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-374">**Why**</span></span>

<span data-ttu-id="f3375-375">這項變更的目的是為了避免在只能有一個正確欄位的情況下，自動使用某個欄位而非另一個欄位。</span><span class="sxs-lookup"><span data-stu-id="f3375-375">This change was made to avoid silently using one field over another when only one can be correct.</span></span>

<span data-ttu-id="f3375-376">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-376">**Mitigations**</span></span>

<span data-ttu-id="f3375-377">若屬性的支援欄位模稜兩可，則必須明確指定要使用的欄位。</span><span class="sxs-lookup"><span data-stu-id="f3375-377">Properties with ambiguous backing fields must have the field to use specified explicitly.</span></span>
<span data-ttu-id="f3375-378">例如，使用 Fluent API：</span><span class="sxs-lookup"><span data-stu-id="f3375-378">For example, using the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

## <a name="field-only-property-names-should-match-the-field-name"></a><span data-ttu-id="f3375-379">僅限欄位的屬性名稱應與欄位名稱相符</span><span class="sxs-lookup"><span data-stu-id="f3375-379">Field-only property names should match the field name</span></span>

<span data-ttu-id="f3375-380">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="f3375-380">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3375-381">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-381">**Old behavior**</span></span>

<span data-ttu-id="f3375-382">在 EF Core 3.0 以前，屬性可透過字串值指定，且如果在 CLR 類型上找不到具有該名稱的屬性，EF Core 會嘗試使用慣例規則將其與欄位比對。</span><span class="sxs-lookup"><span data-stu-id="f3375-382">Before EF Core 3.0, a property could be specified by a string value and if no property with that name was found on the CLR type then EF Core would try to match it to a field using convetion rules.</span></span>
```C#
private class Blog
{
    private int _id;
    public string Name { get; set; }
}
```
```C#
modelBuilder
    .Entity<Blog>()
    .Property("Id");
```

<span data-ttu-id="f3375-383">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-383">**New behavior**</span></span>

<span data-ttu-id="f3375-384">從 EF Core 3.0 開始，僅限欄位的屬性必須與欄位名稱完全相符。</span><span class="sxs-lookup"><span data-stu-id="f3375-384">Starting with EF Core 3.0, a field-only property must match the field name exactly.</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property("_id");
```

<span data-ttu-id="f3375-385">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-385">**Why**</span></span>

<span data-ttu-id="f3375-386">此變更是為了避免對兩個名稱相似的屬性使用相同欄位，也使僅限欄位屬性之比對規則與對應至 CLR 屬性的屬性相同。</span><span class="sxs-lookup"><span data-stu-id="f3375-386">This change was made to avoid using the same field for two properties named similarly, it also makes the matching rules for field-only properties the same as for properties mapped to CLR properties.</span></span>

<span data-ttu-id="f3375-387">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-387">**Mitigations**</span></span>

<span data-ttu-id="f3375-388">僅限欄位屬性必須命名為與其所對應欄位相同的名稱。</span><span class="sxs-lookup"><span data-stu-id="f3375-388">Field-only properties must be named the same as the field they are mapped to.</span></span>
<span data-ttu-id="f3375-389">在之後的 EF Core 3.0 預覽中，我們計劃重新啟用明確設定與屬性名稱不同的欄位名稱：</span><span class="sxs-lookup"><span data-stu-id="f3375-389">In a later preview of EF Core 3.0, we plan to re-enable explicitly configuring a field name that is different from the property name:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property("Id")
    .HasField("_id");
```

## <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a><span data-ttu-id="f3375-390">AddDbContext/AddDbContextPool 再也不會呼叫 AddLogging 與 AddMemoryCache</span><span class="sxs-lookup"><span data-stu-id="f3375-390">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>

[<span data-ttu-id="f3375-391">追蹤問題 #14756</span><span class="sxs-lookup"><span data-stu-id="f3375-391">Tracking Issue #14756</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14756)

<span data-ttu-id="f3375-392">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="f3375-392">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3375-393">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-393">**Old behavior**</span></span>

<span data-ttu-id="f3375-394">在 EF Core 3.0 之前，呼叫 `AddDbContext` 或 `AddDbContextPool` 也會透過呼叫 [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) 與 [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) 來向 DI 註冊記錄與記憶體快取服務。</span><span class="sxs-lookup"><span data-stu-id="f3375-394">Before EF Core 3.0, calling `AddDbContext` or `AddDbContextPool` would also register logging and memory caching services with D.I through calls to [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) and [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<span data-ttu-id="f3375-395">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-395">**New behavior**</span></span>

<span data-ttu-id="f3375-396">從 EF Core 3.0 開始，`AddDbContext` 與 `AddDbContextPool` 再也不會向相依性插入 (DI) 註冊這些服務。</span><span class="sxs-lookup"><span data-stu-id="f3375-396">Starting with EF Core 3.0, `AddDbContext` and `AddDbContextPool` will no longer register these services with Dependency Injection (DI).</span></span>

<span data-ttu-id="f3375-397">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-397">**Why**</span></span>

<span data-ttu-id="f3375-398">EF Core 3.0 不會要求這些服務必須存在於應用程式的 DI 容器中。</span><span class="sxs-lookup"><span data-stu-id="f3375-398">EF Core 3.0 does not require that these services are in the application's DI cotainer.</span></span> <span data-ttu-id="f3375-399">不過，若 `ILoggerFactory` 已在應用程式的 DI 容器中註冊，則它仍會被 EF Core 使用。</span><span class="sxs-lookup"><span data-stu-id="f3375-399">However, if `ILoggerFactory` is registered in the application's DI container, then it will still be used by EF Core.</span></span>

<span data-ttu-id="f3375-400">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-400">**Mitigations**</span></span>

<span data-ttu-id="f3375-401">若您的應用程式需要這些服務，請使用  [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) 或 [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) 明確地向 DI 容器註冊它們。</span><span class="sxs-lookup"><span data-stu-id="f3375-401">If your application needs these services, then register them explicitly with the DI container using  [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) or [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

## <a name="dbcontextentry-now-performs-a-local-detectchanges"></a><span data-ttu-id="f3375-402">DbContext.Entry 現在會執行本機 DetectChanges</span><span class="sxs-lookup"><span data-stu-id="f3375-402">DbContext.Entry now performs a local DetectChanges</span></span>

[<span data-ttu-id="f3375-403">追蹤問題 #13552</span><span class="sxs-lookup"><span data-stu-id="f3375-403">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="f3375-404">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="f3375-404">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="f3375-405">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-405">**Old behavior**</span></span>

<span data-ttu-id="f3375-406">在 EF Core 3.0 以前，呼叫 `DbContext.Entry` 會導致偵測所有追蹤實體的變更。</span><span class="sxs-lookup"><span data-stu-id="f3375-406">Before EF Core 3.0, calling `DbContext.Entry` would cause changes to be detected for all tracked entities.</span></span>
<span data-ttu-id="f3375-407">這可確保在 `EntityEntry` 中公開的狀態為最新狀態。</span><span class="sxs-lookup"><span data-stu-id="f3375-407">This ensured that the state exposed in the `EntityEntry` was up-to-date.</span></span>

<span data-ttu-id="f3375-408">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-408">**New behavior**</span></span>

<span data-ttu-id="f3375-409">從 EF Core 3.0 開始，呼叫 `DbContext.Entry` 現在只會嘗試在指定實體及其相關的任何追蹤主要實體中偵測變更。</span><span class="sxs-lookup"><span data-stu-id="f3375-409">Starting with EF Core 3.0, calling `DbContext.Entry` will now only attempt to detect changes in the given entity and any tracked principal entities related to it.</span></span>
<span data-ttu-id="f3375-410">這表示呼叫此方法可能還無法偵測到其他位置的變更，因此可能會影響應用程式狀態。</span><span class="sxs-lookup"><span data-stu-id="f3375-410">This means that changes elsewhere may not have been detected by calling this method, which could have implications on application state.</span></span>

<span data-ttu-id="f3375-411">請注意，如果 `ChangeTracker.AutoDetectChangesEnabled` 設定為 `false`，甚至是此本機變更偵測都會停用。</span><span class="sxs-lookup"><span data-stu-id="f3375-411">Note that if `ChangeTracker.AutoDetectChangesEnabled` is set to `false` then even this local change detection will be disabled.</span></span>

<span data-ttu-id="f3375-412">其他導致變更偵測的方法 (例如 `ChangeTracker.Entries` 和 `SaveChanges`) 仍會對所有追蹤實體進行完整的 `DetectChanges`。</span><span class="sxs-lookup"><span data-stu-id="f3375-412">Other methods that cause change detection--for example `ChangeTracker.Entries` and `SaveChanges`--still cause a full `DetectChanges` of all tracked entities.</span></span>

<span data-ttu-id="f3375-413">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-413">**Why**</span></span>

<span data-ttu-id="f3375-414">這項變更的目的是為了改善使用 `context.Entry` 的預設效能。</span><span class="sxs-lookup"><span data-stu-id="f3375-414">This change was made to improve the default performance of using `context.Entry`.</span></span>

<span data-ttu-id="f3375-415">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-415">**Mitigations**</span></span>

<span data-ttu-id="f3375-416">在呼叫 `Entry` 之前明確呼叫 `ChgangeTracker.DetectChanges()` 可確保 3.0 以前的行為。</span><span class="sxs-lookup"><span data-stu-id="f3375-416">Call `ChgangeTracker.DetectChanges()` explicitly before calling `Entry` to ensure the pre-3.0 behavior.</span></span>

## <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a><span data-ttu-id="f3375-417">字串和位元組陣列索引鍵預設不是由用戶端產生</span><span class="sxs-lookup"><span data-stu-id="f3375-417">String and byte array keys are not client-generated by default</span></span>

[<span data-ttu-id="f3375-418">追蹤問題 #14617</span><span class="sxs-lookup"><span data-stu-id="f3375-418">Tracking Issue #14617</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

<span data-ttu-id="f3375-419">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="f3375-419">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3375-420">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-420">**Old behavior**</span></span>

<span data-ttu-id="f3375-421">在 EF Core 3.0 以前，可以使用 `string` 和 `byte[]` 索引鍵屬性，而不需要明確設定非 Null 值。</span><span class="sxs-lookup"><span data-stu-id="f3375-421">Before EF Core 3.0, `string` and `byte[]` key properties could be used without explicitly setting a non-null value.</span></span>
<span data-ttu-id="f3375-422">在此情況下，會在用戶端以 GUID 形式產生索引鍵值，再序列化為 `byte[]` 的位元組。</span><span class="sxs-lookup"><span data-stu-id="f3375-422">In such a case, the key value would be generated on the client as a GUID, serialized to bytes for `byte[]`.</span></span>

<span data-ttu-id="f3375-423">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-423">**New behavior**</span></span>

<span data-ttu-id="f3375-424">從 EF Core 3.0 開始，系統會擲回例外狀況，指出尚未設定任何索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="f3375-424">Starting with EF Core 3.0 an exception will be thrown indicating that no key value has been set.</span></span>

<span data-ttu-id="f3375-425">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-425">**Why**</span></span>

<span data-ttu-id="f3375-426">這項變更是因為用戶端產生的 `string`/`byte[]` 值通常不太有用，而且預設行為使它很難以一般方式來推論產生的索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="f3375-426">This change was made because client-generated `string`/`byte[]` values generally aren't useful, and the default behavior made it hard to reason about generated key values in a common way.</span></span>

<span data-ttu-id="f3375-427">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-427">**Mitigations**</span></span>

<span data-ttu-id="f3375-428">藉由明確指定索引鍵屬性應該在未設定其他非 Null 值時使用產生的值，即可取得 3.0 以前的行為。</span><span class="sxs-lookup"><span data-stu-id="f3375-428">The pre-3.0 behavior can be obtained by explicitly specifying that the key properties should use generated values if no other non-null value is set.</span></span>
<span data-ttu-id="f3375-429">例如，使用 Fluent API：</span><span class="sxs-lookup"><span data-stu-id="f3375-429">For example, with the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

<span data-ttu-id="f3375-430">或者，使用資料註解：</span><span class="sxs-lookup"><span data-stu-id="f3375-430">Or with data annotations:</span></span>

```C#
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

## <a name="iloggerfactory-is-now-a-scoped-service"></a><span data-ttu-id="f3375-431">ILoggerFactory 現在是限定範圍的服務</span><span class="sxs-lookup"><span data-stu-id="f3375-431">ILoggerFactory is now a scoped service</span></span>

[<span data-ttu-id="f3375-432">追蹤問題 #14698</span><span class="sxs-lookup"><span data-stu-id="f3375-432">Tracking Issue #14698</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

<span data-ttu-id="f3375-433">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="f3375-433">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="f3375-434">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-434">**Old behavior**</span></span>

<span data-ttu-id="f3375-435">在 EF Core 3.0 以前，`ILoggerFactory` 會註冊為單一服務。</span><span class="sxs-lookup"><span data-stu-id="f3375-435">Before EF Core 3.0, `ILoggerFactory` was registered as a singleton service.</span></span>

<span data-ttu-id="f3375-436">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-436">**New behavior**</span></span>

<span data-ttu-id="f3375-437">從 EF Core 3.0 開始，`ILoggerFactory` 現在會註冊為限定範圍。</span><span class="sxs-lookup"><span data-stu-id="f3375-437">Starting with EF Core 3.0, `ILoggerFactory` is now registered as scoped.</span></span>

<span data-ttu-id="f3375-438">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-438">**Why**</span></span>

<span data-ttu-id="f3375-439">這項變更的目的是為了允許記錄器與 `DbContext` 執行個體產生關聯，這可啟用其他功能，並避免某些異常行為案例，例如內部服務提供者遽增。</span><span class="sxs-lookup"><span data-stu-id="f3375-439">This change was made to allow association of a logger with a `DbContext` instance, which enables other functionality and removes some cases of pathological behavior such as an explosion of internal service providers.</span></span>

<span data-ttu-id="f3375-440">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-440">**Mitigations**</span></span>

<span data-ttu-id="f3375-441">這項變更不應影響應用程式程式碼，除非在 EF Core 內部服務提供者上使用自訂服務註冊該程式碼。</span><span class="sxs-lookup"><span data-stu-id="f3375-441">This change should not impact application code unless it is registering and using custom services on the EF Core internal service provider.</span></span>
<span data-ttu-id="f3375-442">但這並不常見。</span><span class="sxs-lookup"><span data-stu-id="f3375-442">This isn't common.</span></span>
<span data-ttu-id="f3375-443">在這些情況下，大部分的項目仍會運作，但相依於 `ILoggerFactory` 的任何單一服務需要變更，才能以不同方式取得 `ILoggerFactory`。</span><span class="sxs-lookup"><span data-stu-id="f3375-443">In these cases, most things will still work, but any singleton service that was depending on `ILoggerFactory` will need to be changed to obtain the `ILoggerFactory` in a different way.</span></span>

<span data-ttu-id="f3375-444">如果您遇到上述情況，請在 [EF Core GitHub 問題追蹤器](https://github.com/aspnet/EntityFrameworkCore/issues)上提出問題，讓我們知道您使用 `ILoggerFactory` 的方式，以便進一步了解未來如何才不會再次中斷。</span><span class="sxs-lookup"><span data-stu-id="f3375-444">If you run into situations like this, please file an issue at on the [EF Core GitHub issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues) to let us know how you are using `ILoggerFactory` such that we can better understand how not to break this again in the future.</span></span>

## <a name="idbcontextoptionsextensionwithdebuginfo-merged-into-idbcontextoptionsextension"></a><span data-ttu-id="f3375-445">IDbContextOptionsExtensionWithDebugInfo 已合併到 IDbContextOptionsExtension 中</span><span class="sxs-lookup"><span data-stu-id="f3375-445">IDbContextOptionsExtensionWithDebugInfo merged into IDbContextOptionsExtension</span></span>

[<span data-ttu-id="f3375-446">追蹤問題 #13552</span><span class="sxs-lookup"><span data-stu-id="f3375-446">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="f3375-447">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="f3375-447">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="f3375-448">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-448">**Old behavior**</span></span>

<span data-ttu-id="f3375-449">`IDbContextOptionsExtensionWithDebugInfo` 是從 `IDbContextOptionsExtension` 擴充的額外選擇性介面，以避免在 2.x 發行週期內對介面進行中斷性變更。</span><span class="sxs-lookup"><span data-stu-id="f3375-449">`IDbContextOptionsExtensionWithDebugInfo` was an additional optional interface extended from `IDbContextOptionsExtension` to avoid making a breaking change to the interface during the 2.x release cycle.</span></span>

<span data-ttu-id="f3375-450">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-450">**New behavior**</span></span>

<span data-ttu-id="f3375-451">這些介面現在會一起合併到 `IDbContextOptionsExtension` 中。</span><span class="sxs-lookup"><span data-stu-id="f3375-451">The interfaces are now merged together into `IDbContextOptionsExtension`.</span></span>

<span data-ttu-id="f3375-452">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-452">**Why**</span></span>

<span data-ttu-id="f3375-453">這項變更是因為這些介面在概念上是一個介面。</span><span class="sxs-lookup"><span data-stu-id="f3375-453">This change was made because the interfaces are conceptually one.</span></span>

<span data-ttu-id="f3375-454">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-454">**Mitigations**</span></span>

<span data-ttu-id="f3375-455">您必須更新 `IDbContextOptionsExtension` 的所有實作，才能支援新成員。</span><span class="sxs-lookup"><span data-stu-id="f3375-455">Any implementations of `IDbContextOptionsExtension` will need to be updated to support the new member.</span></span>

## <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a><span data-ttu-id="f3375-456">消極式載入 Proxy 停止假設導覽屬性已完全載入</span><span class="sxs-lookup"><span data-stu-id="f3375-456">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>

[<span data-ttu-id="f3375-457">追蹤問題 #12780</span><span class="sxs-lookup"><span data-stu-id="f3375-457">Tracking Issue #12780</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

<span data-ttu-id="f3375-458">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="f3375-458">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3375-459">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-459">**Old behavior**</span></span>

<span data-ttu-id="f3375-460">在 EF Core 3.0 以前，一旦處置 `DbContext` 之後，就無從得知實體上取自該內容的指定導覽屬性是否已完全載入。</span><span class="sxs-lookup"><span data-stu-id="f3375-460">Before EF Core 3.0, once a `DbContext` was disposed there was no way of knowing if a given navigation property on an entity obtained from that context was fully loaded or not.</span></span>
<span data-ttu-id="f3375-461">Proxy 會改為假設如有非 Null 值，會載入參考導覽；如果不是空的，則會載入集合導覽。</span><span class="sxs-lookup"><span data-stu-id="f3375-461">Proxies would instead assume that a reference navigation is loaded if it has a non-null value, and that a collection navigation is loaded if it isn't empty.</span></span>
<span data-ttu-id="f3375-462">在這些情況下，嘗試消極式載入不會執行任何作業。</span><span class="sxs-lookup"><span data-stu-id="f3375-462">In these cases, attempting to lazy-load would be a no-op.</span></span>

<span data-ttu-id="f3375-463">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-463">**New behavior**</span></span>

<span data-ttu-id="f3375-464">從 EF Core 3.0 開始，Proxy 會追蹤是否載入導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="f3375-464">Starting with EF Core 3.0, proxies keep track of whether or not a navigation property is loaded.</span></span>
<span data-ttu-id="f3375-465">這表示嘗試存取在處置內容之後載入的導覽屬性一律不會執行任何作業，即使已載入的導覽是空的或 Null 也一樣。</span><span class="sxs-lookup"><span data-stu-id="f3375-465">This means attempting to access a navigation property that is loaded after the context has been disposed will always be a no-op, even when the loaded navigation is empty or null.</span></span>
<span data-ttu-id="f3375-466">相反地，如果在處置內容之後嘗試存取未載入的導覽屬性，則會擲回例外狀況，即使導覽屬性不是空集合也一樣。</span><span class="sxs-lookup"><span data-stu-id="f3375-466">Conversely, attempting to access a navigation property that isn't loaded will throw an exception if the context is disposed even if the navigation property is a non-empty collection.</span></span>
<span data-ttu-id="f3375-467">如果發生這種情況，則表示應用程式程式碼嘗試在無效的時間使用消極式載入，應用程式應該變更為不要這麼做。</span><span class="sxs-lookup"><span data-stu-id="f3375-467">If this situation arises, it means the application code is attempting to use lazy-loading at an invalid time, and the application should be changed to not do this.</span></span>

<span data-ttu-id="f3375-468">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-468">**Why**</span></span>

<span data-ttu-id="f3375-469">這項變更的目的是為了在已處置的 `DbContext` 執行個體上嘗試消極式載入時，使行為一致且正確。</span><span class="sxs-lookup"><span data-stu-id="f3375-469">This change was made to make the behavior consistent and correct when attempting to lazy-load on a disposed `DbContext` instance.</span></span>

<span data-ttu-id="f3375-470">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-470">**Mitigations**</span></span>

<span data-ttu-id="f3375-471">將應用程式程式碼更新為不要嘗試對已處置的內容進行消極式載入，或將此設定為不執行任何作業，如例外狀況訊息中所述。</span><span class="sxs-lookup"><span data-stu-id="f3375-471">Update application code to not attempt lazy-loading with a disposed context, or configure this to be a no-op as described in the exception message.</span></span>

## <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a><span data-ttu-id="f3375-472">過度建立內部服務提供者現在預設是錯誤</span><span class="sxs-lookup"><span data-stu-id="f3375-472">Excessive creation of internal service providers is now an error by default</span></span>

[<span data-ttu-id="f3375-473">追蹤問題 #10236</span><span class="sxs-lookup"><span data-stu-id="f3375-473">Tracking Issue #10236</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

<span data-ttu-id="f3375-474">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="f3375-474">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="f3375-475">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-475">**Old behavior**</span></span>

<span data-ttu-id="f3375-476">在 EF Core 3.0 以前，當應用程式建立異常數目的內部服務提供者時，會記錄一則警告。</span><span class="sxs-lookup"><span data-stu-id="f3375-476">Before EF Core 3.0, a warning would be logged for an application creating a pathological number of internal service providers.</span></span>

<span data-ttu-id="f3375-477">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-477">**New behavior**</span></span>

<span data-ttu-id="f3375-478">從 EF Core 3.0 開始，此警告現在會視為錯誤，並會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="f3375-478">Starting with EF Core 3.0, this warning is now considered and error and an exception is thrown.</span></span> 

<span data-ttu-id="f3375-479">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-479">**Why**</span></span>

<span data-ttu-id="f3375-480">這項變更的目的是為了透過更明確公開此異常案例，藉以開發更完善的應用程式程式碼。</span><span class="sxs-lookup"><span data-stu-id="f3375-480">This change was made to drive better application code through exposing this pathological case more explicitly.</span></span>

<span data-ttu-id="f3375-481">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-481">**Mitigations**</span></span>

<span data-ttu-id="f3375-482">遇到此錯誤時的最適當動作是了解根本原因，並停止建立這麼多的內部服務提供者。</span><span class="sxs-lookup"><span data-stu-id="f3375-482">The most appropriate cause of action on encountering this error is to understand the root cause and stop creating so many internal service providers.</span></span>
<span data-ttu-id="f3375-483">不過，透過設定 `DbContextOptionsBuilder` 可以將錯誤轉換回警告。</span><span class="sxs-lookup"><span data-stu-id="f3375-483">However, the error can be converted back to a warning (or ignored) via configuration on the `DbContextOptionsBuilder`.</span></span>
<span data-ttu-id="f3375-484">例如：</span><span class="sxs-lookup"><span data-stu-id="f3375-484">For example:</span></span>

```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

## <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a><span data-ttu-id="f3375-485">使用單一字串呼叫之 HasOne/HasMany 的新行為</span><span class="sxs-lookup"><span data-stu-id="f3375-485">New behavior for HasOne/HasMany called with a single string</span></span>

[<span data-ttu-id="f3375-486">追蹤問題 #9171</span><span class="sxs-lookup"><span data-stu-id="f3375-486">Tracking Issue #9171</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9171)

<span data-ttu-id="f3375-487">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="f3375-487">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3375-488">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-488">**Old behavior**</span></span>

<span data-ttu-id="f3375-489">在 EF Core 3.0 之前，使用單一字串呼叫 `HasOne` 或 `HasMany` 的程式碼會以令人困惑的方式解譯。</span><span class="sxs-lookup"><span data-stu-id="f3375-489">Before EF Core 3.0, code calling `HasOne` or `HasMany` with a single string was interpretted in a confusing way.</span></span>
<span data-ttu-id="f3375-490">例如：</span><span class="sxs-lookup"><span data-stu-id="f3375-490">For example:</span></span>
```C#
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

<span data-ttu-id="f3375-491">程式碼看起來像是它使用 `Entrance` 瀏覽屬性將 `Samurai` 與一些其他實體類型相關，這可能是私用屬性。</span><span class="sxs-lookup"><span data-stu-id="f3375-491">The code looks like it is relating `Samurai` to some other entity type using the `Entrance` navigation property, which may be private.</span></span>

<span data-ttu-id="f3375-492">在現實中，此程式碼會在不使用瀏覽屬性的情況下嘗試建立與一些實體 (稱為 `Entrance`) 的關係。</span><span class="sxs-lookup"><span data-stu-id="f3375-492">In reality, this code attempts to create a relationship to some entity type called `Entrance` with no navigation property.</span></span>

<span data-ttu-id="f3375-493">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-493">**New behavior**</span></span>

<span data-ttu-id="f3375-494">從 EF Core 3.0 開始，上述程式碼現在會執行像以前一樣的動作。</span><span class="sxs-lookup"><span data-stu-id="f3375-494">Starting with EF Core 3.0, the code above now does what it looked like it should have been doing before.</span></span>

<span data-ttu-id="f3375-495">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-495">**Why**</span></span>

<span data-ttu-id="f3375-496">舊行為令人非常困惑，特別是當讀取設定程式碼與尋找錯誤時。</span><span class="sxs-lookup"><span data-stu-id="f3375-496">The old behavior was very confusing, especially when reading the configuration code and looking for errors.</span></span>

<span data-ttu-id="f3375-497">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-497">**Mitigations**</span></span>

<span data-ttu-id="f3375-498">這只會造成已明確針對類型名稱使用字串設定關係，而未明確指定瀏覽屬性的應用程式中斷。</span><span class="sxs-lookup"><span data-stu-id="f3375-498">This will only break applications that are explicitly configuring relationships using strings for type names and without specifying the navigation property explicitly.</span></span>
<span data-ttu-id="f3375-499">這不是常見情況。</span><span class="sxs-lookup"><span data-stu-id="f3375-499">This is not common.</span></span>
<span data-ttu-id="f3375-500">先前的行為可透過明確地傳遞瀏覽屬性名稱的 `null` 來取得。</span><span class="sxs-lookup"><span data-stu-id="f3375-500">The previous behavior can be obtained through explicitly passing `null` for the navigation property name.</span></span>
<span data-ttu-id="f3375-501">例如：</span><span class="sxs-lookup"><span data-stu-id="f3375-501">For example:</span></span>

```C#
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

## <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a><span data-ttu-id="f3375-502">數個非同步方法的傳回類型已從 Task 變更為 ValueTask</span><span class="sxs-lookup"><span data-stu-id="f3375-502">The return type for several async methods has been changed from Task to ValueTask</span></span>

[<span data-ttu-id="f3375-503">追蹤問題 #15184</span><span class="sxs-lookup"><span data-stu-id="f3375-503">Tracking Issue #15184</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15184)

<span data-ttu-id="f3375-504">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="f3375-504">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3375-505">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-505">**Old behavior**</span></span>

<span data-ttu-id="f3375-506">下列非同步方法先前傳回了 `Task<T>`：</span><span class="sxs-lookup"><span data-stu-id="f3375-506">The following async methods previously returned a `Task<T>`:</span></span>

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* <span data-ttu-id="f3375-507">`ValueGenerator.NextValueAsync()` (和衍生類別)</span><span class="sxs-lookup"><span data-stu-id="f3375-507">`ValueGenerator.NextValueAsync()` (and deriving classes)</span></span>

<span data-ttu-id="f3375-508">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-508">**New behavior**</span></span>

<span data-ttu-id="f3375-509">上述方法現在會透過相同的 `T` 傳回 `ValueTask<T>`，如同以前一樣。</span><span class="sxs-lookup"><span data-stu-id="f3375-509">The aforementioned methods now return a `ValueTask<T>` over the same `T` as before.</span></span>

<span data-ttu-id="f3375-510">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-510">**Why**</span></span>

<span data-ttu-id="f3375-511">這項變更可降低叫用這些方法時產生的堆積配置數目，可改善一般效能。</span><span class="sxs-lookup"><span data-stu-id="f3375-511">This change reduces the number of heap allocations incurred when invoking these methods, improving general performance.</span></span>

<span data-ttu-id="f3375-512">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-512">**Mitigations**</span></span>

<span data-ttu-id="f3375-513">僅等待上述 API 的應用程式只需要重新編譯，而不需要變更來源。</span><span class="sxs-lookup"><span data-stu-id="f3375-513">Applications simply awaiting the above APIs only need to be recompiled - no source changes are necessary.</span></span>
<span data-ttu-id="f3375-514">更複雜的使用方式 (例如將傳回的 `Task` 傳遞給 `Task.WhenAny()`) 通常需要藉由呼叫 `AsTask()` 將傳回的 `ValueTask<T>` 轉換為 `Task<T>`。</span><span class="sxs-lookup"><span data-stu-id="f3375-514">A more complex usage (e.g. passing the returned `Task` to `Task.WhenAny()`) typically require that the returned `ValueTask<T>` be converted to a `Task<T>` by calling `AsTask()` on it.</span></span>
<span data-ttu-id="f3375-515">請注意，這會抵消這項變更所帶來的配置減少。</span><span class="sxs-lookup"><span data-stu-id="f3375-515">Note that this negates the allocation reduction that this change brings.</span></span>

## <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a><span data-ttu-id="f3375-516">Relational:TypeMapping 註解現僅為 TypeMapping</span><span class="sxs-lookup"><span data-stu-id="f3375-516">The Relational:TypeMapping annotation is now just TypeMapping</span></span>

[<span data-ttu-id="f3375-517">追蹤問題 #9913</span><span class="sxs-lookup"><span data-stu-id="f3375-517">Tracking Issue #9913</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

<span data-ttu-id="f3375-518">這項變更已於 EF Core 3.0-preview 2 推出。</span><span class="sxs-lookup"><span data-stu-id="f3375-518">This change was introduced in EF Core 3.0-preview 2.</span></span>

<span data-ttu-id="f3375-519">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-519">**Old behavior**</span></span>

<span data-ttu-id="f3375-520">類型對應註解的註解名稱之前是 "Relational:TypeMapping"。</span><span class="sxs-lookup"><span data-stu-id="f3375-520">The annotation name for type mapping annotations was "Relational:TypeMapping".</span></span>

<span data-ttu-id="f3375-521">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-521">**New behavior**</span></span>

<span data-ttu-id="f3375-522">類型對應註解的註解名稱現在是 "TypeMapping"。</span><span class="sxs-lookup"><span data-stu-id="f3375-522">The annotation name for type mapping annotations is now "TypeMapping".</span></span>

<span data-ttu-id="f3375-523">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-523">**Why**</span></span>

<span data-ttu-id="f3375-524">類型對應現在不只用於關聯式資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="f3375-524">Type mappings are now used for more than just relational database providers.</span></span>

<span data-ttu-id="f3375-525">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-525">**Mitigations**</span></span>

<span data-ttu-id="f3375-526">這只會中斷直接將類型對應當做註解存取的應用程式，但這並不常見。</span><span class="sxs-lookup"><span data-stu-id="f3375-526">This will only break applications that access the type mapping directly as an annotation, which isn't common.</span></span>
<span data-ttu-id="f3375-527">最適當的修正動作是使用 API 介面存取類型對應，而不是直接使用註解。</span><span class="sxs-lookup"><span data-stu-id="f3375-527">The most appropriate action to fix is to use API surface to access type mappings rather than using the annotation directly.</span></span>

## <a name="totable-on-a-derived-type-throws-an-exception"></a><span data-ttu-id="f3375-528">衍生類型上的 ToTable 會擲回例外狀況</span><span class="sxs-lookup"><span data-stu-id="f3375-528">ToTable on a derived type throws an exception</span></span> 

[<span data-ttu-id="f3375-529">追蹤問題 #11811</span><span class="sxs-lookup"><span data-stu-id="f3375-529">Tracking Issue #11811</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

<span data-ttu-id="f3375-530">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="f3375-530">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="f3375-531">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-531">**Old behavior**</span></span>

<span data-ttu-id="f3375-532">在 EF Core 3.0 以前，會忽略衍生類型上呼叫的 `ToTable()`，因為唯一的繼承對應策略是對此案例無效的 TPH。</span><span class="sxs-lookup"><span data-stu-id="f3375-532">Before EF Core 3.0, `ToTable()` called on a derived type would be ignored since only inheritance mapping strategy was TPH where this isn't valid.</span></span> 

<span data-ttu-id="f3375-533">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-533">**New behavior**</span></span>

<span data-ttu-id="f3375-534">從 EF Core 3.0 開始，以及在更新版本中準備新增 TPT 和 TPC 支援時，在衍生類型上呼叫的 `ToTable()` 現在會擲回例外狀況，以避免未來發生非預期的對應變更。</span><span class="sxs-lookup"><span data-stu-id="f3375-534">Starting with EF Core 3.0 and in preparation for adding TPT and TPC support in a later release, `ToTable()` called on a derived type will now throw an exception to avoid an unexpected mapping change in the future.</span></span>

<span data-ttu-id="f3375-535">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-535">**Why**</span></span>

<span data-ttu-id="f3375-536">目前無法將衍生類型對應至不同的資料表。</span><span class="sxs-lookup"><span data-stu-id="f3375-536">Currently it isn't valid to map a derived type to a different table.</span></span>
<span data-ttu-id="f3375-537">這項變更可避免未來有效執行時的中斷情況。</span><span class="sxs-lookup"><span data-stu-id="f3375-537">This change avoids breaking in the future when it becomes a valid thing to do.</span></span>

<span data-ttu-id="f3375-538">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-538">**Mitigations**</span></span>

<span data-ttu-id="f3375-539">避免嘗試將衍生類型對應至其他資料表。</span><span class="sxs-lookup"><span data-stu-id="f3375-539">Remove any attempts to map derived types to other tables.</span></span>

## <a name="forsqlserverhasindex-replaced-with-hasindex"></a><span data-ttu-id="f3375-540">ForSqlServerHasIndex 已取代為 HasIndex</span><span class="sxs-lookup"><span data-stu-id="f3375-540">ForSqlServerHasIndex replaced with HasIndex</span></span> 

[<span data-ttu-id="f3375-541">追蹤問題 #12366</span><span class="sxs-lookup"><span data-stu-id="f3375-541">Tracking Issue #12366</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

<span data-ttu-id="f3375-542">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="f3375-542">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="f3375-543">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-543">**Old behavior**</span></span>

<span data-ttu-id="f3375-544">在 EF Core 3.0 以前，`ForSqlServerHasIndex().ForSqlServerInclude()` 可讓您設定搭配 `INCLUDE` 使用的資料行。</span><span class="sxs-lookup"><span data-stu-id="f3375-544">Before EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` provided a way to configure columns used with `INCLUDE`.</span></span>

<span data-ttu-id="f3375-545">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-545">**New behavior**</span></span>

<span data-ttu-id="f3375-546">從 EF Core 3.0 開始，關聯式層級現在支援對索引使用 `Include`。</span><span class="sxs-lookup"><span data-stu-id="f3375-546">Starting with EF Core 3.0, using `Include` on an index is now supported at the relational level.</span></span>
<span data-ttu-id="f3375-547">使用 `HasIndex().ForSqlServerInclude()`。</span><span class="sxs-lookup"><span data-stu-id="f3375-547">Use `HasIndex().ForSqlServerInclude()`.</span></span>

<span data-ttu-id="f3375-548">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-548">**Why**</span></span>

<span data-ttu-id="f3375-549">這項變更的目的是為了能夠使用 `Include` 將所有資料庫提供者的索引 API 合併到一個位置。</span><span class="sxs-lookup"><span data-stu-id="f3375-549">This change was made to consolidate the API for indexes with `Include` into one place for all database providers.</span></span>

<span data-ttu-id="f3375-550">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-550">**Mitigations**</span></span>

<span data-ttu-id="f3375-551">使用新的 API，如上所示。</span><span class="sxs-lookup"><span data-stu-id="f3375-551">Use the new API, as shown above.</span></span>

## <a name="metadata-api-changes"></a><span data-ttu-id="f3375-552">中繼資料 API 變更</span><span class="sxs-lookup"><span data-stu-id="f3375-552">Metadata API changes</span></span>

[<span data-ttu-id="f3375-553">追蹤問題 #214</span><span class="sxs-lookup"><span data-stu-id="f3375-553">Tracking Issue #214</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/214)

<span data-ttu-id="f3375-554">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="f3375-554">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3375-555">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-555">**New behavior**</span></span>

<span data-ttu-id="f3375-556">下列屬性已轉換為擴充方法：</span><span class="sxs-lookup"><span data-stu-id="f3375-556">The following properties were converted to extension methods:</span></span>

* `IEntityType.QueryFilter` -> `GetQueryFilter()`
* `IEntityType.DefiningQuery` -> `GetDefiningQuery()`
* `IProperty.IsShadowProperty` -> `IsShadowProperty()`
* `IProperty.BeforeSaveBehavior` -> `GetBeforeSaveBehavior()`
* `IProperty.AfterSaveBehavior` -> `GetAfterSaveBehavior()`

<span data-ttu-id="f3375-557">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-557">**Why**</span></span>

<span data-ttu-id="f3375-558">這項變更可簡化上述介面的實作。</span><span class="sxs-lookup"><span data-stu-id="f3375-558">This change simplifies the implementation of the aforementioned interfaces.</span></span>

<span data-ttu-id="f3375-559">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-559">**Mitigations**</span></span>

<span data-ttu-id="f3375-560">使用新的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="f3375-560">Use the new extension methods.</span></span>

## <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a><span data-ttu-id="f3375-561">EF Core 不會再傳送 SQLite FK 強制的 pragma</span><span class="sxs-lookup"><span data-stu-id="f3375-561">EF Core no longer sends pragma for SQLite FK enforcement</span></span>

[<span data-ttu-id="f3375-562">追蹤問題 #12151</span><span class="sxs-lookup"><span data-stu-id="f3375-562">Tracking Issue #12151</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

<span data-ttu-id="f3375-563">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="f3375-563">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="f3375-564">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-564">**Old behavior**</span></span>

<span data-ttu-id="f3375-565">在 EF Core 3.0 以前，當開啟 SQLite 連線時，EF Core 會傳送 `PRAGMA foreign_keys = 1`。</span><span class="sxs-lookup"><span data-stu-id="f3375-565">Before EF Core 3.0, EF Core would send `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="f3375-566">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-566">**New behavior**</span></span>

<span data-ttu-id="f3375-567">從 EF Core 3.0 開始，當開啟 SQLite 連線時，EF Core 不會再傳送 `PRAGMA foreign_keys = 1`。</span><span class="sxs-lookup"><span data-stu-id="f3375-567">Starting with EF Core 3.0, EF Core no longer sends `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="f3375-568">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-568">**Why**</span></span>

<span data-ttu-id="f3375-569">這項變更是因為 EF Core 預設會使用 `SQLitePCLRaw.bundle_e_sqlite3`，這也表示預設會開啟 FK 強制，而不需要在每次開啟連線時明確啟用。</span><span class="sxs-lookup"><span data-stu-id="f3375-569">This change was made because EF Core uses `SQLitePCLRaw.bundle_e_sqlite3` by default, which in turn means that FK enforcement is switched on by default and doesn't need to be explicitly enabled each time a connection is opened.</span></span>

<span data-ttu-id="f3375-570">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-570">**Mitigations**</span></span>

<span data-ttu-id="f3375-571">根據預設，會在預設用於 EF Core 的 SQLitePCLRaw.bundle_e_sqlite3 中啟用外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="f3375-571">Foreign keys are enabled by default in SQLitePCLRaw.bundle_e_sqlite3, which is used by default for EF Core.</span></span>
<span data-ttu-id="f3375-572">在其他情況下，則可以藉由在您的連接字串中指定 `Foreign Keys=True` 來啟用外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="f3375-572">For other cases, foreign keys can be enabled by specifying `Foreign Keys=True` in your connection string.</span></span>

## <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundleesqlite3"></a><span data-ttu-id="f3375-573">Microsoft.EntityFrameworkCore.Sqlite 現在相依於 SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="f3375-573">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>

<span data-ttu-id="f3375-574">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-574">**Old behavior**</span></span>

<span data-ttu-id="f3375-575">在 EF Core 3.0 以前，EF Core 會使用 `SQLitePCLRaw.bundle_green`。</span><span class="sxs-lookup"><span data-stu-id="f3375-575">Before EF Core 3.0, EF Core used `SQLitePCLRaw.bundle_green`.</span></span>

<span data-ttu-id="f3375-576">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-576">**New behavior**</span></span>

<span data-ttu-id="f3375-577">從 EF Core 3.0 開始，EF Core 會使用 `SQLitePCLRaw.bundle_e_sqlite3`。</span><span class="sxs-lookup"><span data-stu-id="f3375-577">Starting with EF Core 3.0, EF Core uses `SQLitePCLRaw.bundle_e_sqlite3`.</span></span>

<span data-ttu-id="f3375-578">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-578">**Why**</span></span>

<span data-ttu-id="f3375-579">這項變更的目的是為了讓用於 iOS 的 SQLite 版本與其他平台一致。</span><span class="sxs-lookup"><span data-stu-id="f3375-579">This change was made so that the version of SQLite used on iOS consistent with other platforms.</span></span>

<span data-ttu-id="f3375-580">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-580">**Mitigations**</span></span>

<span data-ttu-id="f3375-581">若要在 iOS 上使用原生 SQLite 版本，請設定 `Microsoft.Data.Sqlite` 使用不同的 `SQLitePCLRaw` 套件組合。</span><span class="sxs-lookup"><span data-stu-id="f3375-581">To use the native SQLite version on iOS, configure `Microsoft.Data.Sqlite` to use a different `SQLitePCLRaw` bundle.</span></span>

## <a name="guid-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="f3375-582">GUID 值現在於 SQLite 上的儲存形式為 TEXT</span><span class="sxs-lookup"><span data-stu-id="f3375-582">Guid values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="f3375-583">追蹤問題 #15078</span><span class="sxs-lookup"><span data-stu-id="f3375-583">Tracking Issue #15078</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15078)

<span data-ttu-id="f3375-584">這項變更已於 EF Core 3.0-preview 4 推出。</span><span class="sxs-lookup"><span data-stu-id="f3375-584">This change was introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3375-585">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-585">**Old behavior**</span></span>

<span data-ttu-id="f3375-586">GUID 值先前在 SQLite 上的儲存形式為 BLOB 值。</span><span class="sxs-lookup"><span data-stu-id="f3375-586">Guid values were previously sored as BLOB values on SQLite.</span></span>

<span data-ttu-id="f3375-587">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-587">**New behavior**</span></span>

<span data-ttu-id="f3375-588">GUID 值現在的儲存形式為 TEXT。</span><span class="sxs-lookup"><span data-stu-id="f3375-588">Guid values are now sotred as TEXT.</span></span>

<span data-ttu-id="f3375-589">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-589">**Why**</span></span>

<span data-ttu-id="f3375-590">GUID 的二進位格式未標準化。</span><span class="sxs-lookup"><span data-stu-id="f3375-590">The binary format of Guids is not standardized.</span></span> <span data-ttu-id="f3375-591">以 TEXT 的形式儲存值會提高資料庫與其他技術的相容性。</span><span class="sxs-lookup"><span data-stu-id="f3375-591">Storing the values as TEXT makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="f3375-592">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-592">**Mitigations**</span></span>

<span data-ttu-id="f3375-593">您可以參考以下方式執行 SQL，來將現有的資料庫移轉至新的格式。</span><span class="sxs-lookup"><span data-stu-id="f3375-593">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

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

<span data-ttu-id="f3375-594">在 EF Core 中，您也可以在這些屬性上設定值轉換器來繼續使用原本的行為。</span><span class="sxs-lookup"><span data-stu-id="f3375-594">In EF Core, you could also continue using the previous behavior by configuirng a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

<span data-ttu-id="f3375-595">Microsoft.Data.Sqlite 依然可以同時從 BLOB 及 TEXT 資料行讀取 GUID 值；但因為參數和常數的預設格式已變更，所以您可能需要對多數涉及 GUID 的案例採取動作。</span><span class="sxs-lookup"><span data-stu-id="f3375-595">Microsoft.Data.Sqlite remains capable of reading Guid values from both BLOB and TEXT columns; however, since the default format for parameters and constants has changed you'll likely need to take action for most scenarios involving Guids.</span></span>

## <a name="char-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="f3375-596">Char 值現在於 SQLite 上會儲存為文字</span><span class="sxs-lookup"><span data-stu-id="f3375-596">Char values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="f3375-597">追蹤問題 #15020</span><span class="sxs-lookup"><span data-stu-id="f3375-597">Tracking Issue #15020</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15020)

<span data-ttu-id="f3375-598">這項變更已於 EF Core 3.0-preview 4 推出。</span><span class="sxs-lookup"><span data-stu-id="f3375-598">This change was introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3375-599">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-599">**Old behavior**</span></span>

<span data-ttu-id="f3375-600">Char 值原先在 SQLite 上儲存為整數值。</span><span class="sxs-lookup"><span data-stu-id="f3375-600">Char values were previously sored as INTEGER values on SQLite.</span></span> <span data-ttu-id="f3375-601">舉例來說，char 值 *A* 原先會儲存為整數值 65。</span><span class="sxs-lookup"><span data-stu-id="f3375-601">For example, a char value of *A* was stored as the integer value 65.</span></span>

<span data-ttu-id="f3375-602">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-602">**New behavior**</span></span>

<span data-ttu-id="f3375-603">Char 值現在會儲存為 TEXT。</span><span class="sxs-lookup"><span data-stu-id="f3375-603">Char values are now sotred as TEXT.</span></span>

<span data-ttu-id="f3375-604">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-604">**Why**</span></span>

<span data-ttu-id="f3375-605">將值儲存為 TEXT 不但更加自然，也使資料庫與其他技術的相容性更高。</span><span class="sxs-lookup"><span data-stu-id="f3375-605">Storing the values as TEXT is more natural and makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="f3375-606">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-606">**Mitigations**</span></span>

<span data-ttu-id="f3375-607">您可以參考以下方式執行 SQL，來將現有的資料庫移轉至新的格式。</span><span class="sxs-lookup"><span data-stu-id="f3375-607">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

``` sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

<span data-ttu-id="f3375-608">在 EF Core 中，您也可以在這些屬性上設定值轉換器來繼續使用原本的行為。</span><span class="sxs-lookup"><span data-stu-id="f3375-608">In EF Core, you could also continue using the previous behavior by configuirng a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

<span data-ttu-id="f3375-609">Microsoft.Data.Sqlite 也保留了讀取 INTEGER 和 TEXT 欄位字元值的功能，所以部分案例可能不需要任何動作。</span><span class="sxs-lookup"><span data-stu-id="f3375-609">Microsoft.Data.Sqlite also remains capable of reading character values from both INTEGER and TEXT columns, so certain scenarios may not require any action.</span></span>

## <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a><span data-ttu-id="f3375-610">移轉識別碼現在會使用不因文化特性而異的行事曆產生</span><span class="sxs-lookup"><span data-stu-id="f3375-610">Migration IDs are now generated using the invariant culture's calendar</span></span>

[<span data-ttu-id="f3375-611">追蹤問題 #12978</span><span class="sxs-lookup"><span data-stu-id="f3375-611">Tracking Issue #12978</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12978)

<span data-ttu-id="f3375-612">這項變更已於 EF Core 3.0-preview 4 推出。</span><span class="sxs-lookup"><span data-stu-id="f3375-612">This change was introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3375-613">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-613">**Old behavior**</span></span>

<span data-ttu-id="f3375-614">移轉識別碼原先會使用目前文化特性 (Culture) 的行事曆產生。</span><span class="sxs-lookup"><span data-stu-id="f3375-614">Migration IDs were inadvertantly generated using the currret culture's calendar.</span></span>

<span data-ttu-id="f3375-615">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-615">**New behavior**</span></span>

<span data-ttu-id="f3375-616">移轉識別碼現在一律會使用不因文化特性而異的行事曆 (西曆) 產生。</span><span class="sxs-lookup"><span data-stu-id="f3375-616">Migration IDs are now always generated using the invariant culture's calendar (Gregorian).</span></span>

<span data-ttu-id="f3375-617">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-617">**Why**</span></span>

<span data-ttu-id="f3375-618">更新資料庫或解決合併衝突時，移轉的順序相當重要。</span><span class="sxs-lookup"><span data-stu-id="f3375-618">The order of migrations is important when updating the database or resolving merge conflicts.</span></span> <span data-ttu-id="f3375-619">使用無差異的行事曆可避免順序問題，使小組成員系統行事曆不同的問題不會發生。</span><span class="sxs-lookup"><span data-stu-id="f3375-619">Using the invariant calendar avoids ordering issues that can result from team members having different system calendars.</span></span>

<span data-ttu-id="f3375-620">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-620">**Mitigations**</span></span>

<span data-ttu-id="f3375-621">這項變更會影響年份大於西曆行事曆的非西曆行事曆使用者 (例如泰國佛曆)。</span><span class="sxs-lookup"><span data-stu-id="f3375-621">This change affects anyone using a non-Gregorian calender where the year is greater than the Gregorian calendar (like the Thai Buddhist calendar).</span></span> <span data-ttu-id="f3375-622">現有的移轉識別碼必須更新，以使新的移轉會在現有的移轉之後排序。</span><span class="sxs-lookup"><span data-stu-id="f3375-622">Existing migration IDs will need to be updated so that new migrations are ordered after existing migrations.</span></span>

<span data-ttu-id="f3375-623">您可在移轉設計工具檔案的移轉屬性中找到移轉識別碼。</span><span class="sxs-lookup"><span data-stu-id="f3375-623">The migration ID can be found in the Migration attribute in the migrations' designer files.</span></span>

``` diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

<span data-ttu-id="f3375-624">移轉歷程記錄資料表也必須更新。</span><span class="sxs-lookup"><span data-stu-id="f3375-624">The Migrations history table also needs to be updated.</span></span>

``` sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

## <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a><span data-ttu-id="f3375-625">已重新命名 LogQueryPossibleExceptionWithAggregateOperator</span><span class="sxs-lookup"><span data-stu-id="f3375-625">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>

[<span data-ttu-id="f3375-626">追蹤問題 #10985</span><span class="sxs-lookup"><span data-stu-id="f3375-626">Tracking Issue #10985</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10985)

<span data-ttu-id="f3375-627">這項變更已於 EF Core 3.0-preview 4 推出。</span><span class="sxs-lookup"><span data-stu-id="f3375-627">This change was introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3375-628">**變更**</span><span class="sxs-lookup"><span data-stu-id="f3375-628">**Change**</span></span>

<span data-ttu-id="f3375-629">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` 已經重新命名為 `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`。</span><span class="sxs-lookup"><span data-stu-id="f3375-629">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` has been renamed to `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span></span>

<span data-ttu-id="f3375-630">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-630">**Why**</span></span>

<span data-ttu-id="f3375-631">使這個警告事件的命名與其他所有警告事件一致。</span><span class="sxs-lookup"><span data-stu-id="f3375-631">Aligns the naming of this warning event with all other warning events.</span></span>

<span data-ttu-id="f3375-632">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-632">**Mitigations**</span></span>

<span data-ttu-id="f3375-633">使用新的名稱。</span><span class="sxs-lookup"><span data-stu-id="f3375-633">Use the new name.</span></span> <span data-ttu-id="f3375-634">(注意，事件識別碼未變更。)</span><span class="sxs-lookup"><span data-stu-id="f3375-634">(Note that the event ID number has not changed.)</span></span>

## <a name="clarify-api-for-foreign-key-constraint-names"></a><span data-ttu-id="f3375-635">讓 API 的外部索引鍵限制式名稱更清楚</span><span class="sxs-lookup"><span data-stu-id="f3375-635">Clarify API for foreign key constraint names</span></span>

[<span data-ttu-id="f3375-636">追蹤問題 #10730</span><span class="sxs-lookup"><span data-stu-id="f3375-636">Tracking Issue #10730</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10730)

<span data-ttu-id="f3375-637">這項變更已於 EF Core 3.0-preview 4 推出。</span><span class="sxs-lookup"><span data-stu-id="f3375-637">This change was introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3375-638">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-638">**Old behavior**</span></span>

<span data-ttu-id="f3375-639">在 EF Core 3.0 前，外部索引鍵限制式名稱僅為 "name"。</span><span class="sxs-lookup"><span data-stu-id="f3375-639">Before EF Core 3.0, foreign key constraint names were referred to as simply the "name".</span></span> <span data-ttu-id="f3375-640">例如：</span><span class="sxs-lookup"><span data-stu-id="f3375-640">For example:</span></span>

```C#
var constraintName = myForeignKey.Name;
```

<span data-ttu-id="f3375-641">**新行為**</span><span class="sxs-lookup"><span data-stu-id="f3375-641">**New behavior**</span></span>

<span data-ttu-id="f3375-642">從 EF Core 3.0 開始，外部索引鍵限制式名稱現為 "constaint name"。</span><span class="sxs-lookup"><span data-stu-id="f3375-642">Starting with EF Core 3.0, foreign key constraint names are now referred to as the "constaint name".</span></span> <span data-ttu-id="f3375-643">例如：</span><span class="sxs-lookup"><span data-stu-id="f3375-643">For example:</span></span>

```C#
var constraintName = myForeignKey.ConstraintName;
```

<span data-ttu-id="f3375-644">**原因**</span><span class="sxs-lookup"><span data-stu-id="f3375-644">**Why**</span></span>

<span data-ttu-id="f3375-645">這項變更可讓此領域中的命名一致，同時清楚指出這是外部索引鍵限制式的名稱，而非定義外部索引鍵的資料行或屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="f3375-645">This change brings consistency to naming in this area, and also clarifies that this is the name of the foreign key constaint, and not the column or property name that the foreign key is defined on.</span></span>

<span data-ttu-id="f3375-646">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="f3375-646">**Mitigations**</span></span>

<span data-ttu-id="f3375-647">使用新的名稱。</span><span class="sxs-lookup"><span data-stu-id="f3375-647">Use the new name.</span></span>
