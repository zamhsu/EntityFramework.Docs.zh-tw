---
title: EF Core 3.0 的中斷性變更 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: EE2878C9-71F9-4FA5-9BC4-60517C7C9830
uid: core/what-is-new/ef-core-3.0/breaking-changes
ms.openlocfilehash: a7e1a03bf1131cd53123f5cc39b07bed94619b44
ms.sourcegitcommit: a013e243a14f384999ceccaf9c779b8c1ae3b936
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2019
ms.locfileid: "57463358"
---
# <a name="breaking-changes-included-in-ef-core-30-currently-in-preview"></a><span data-ttu-id="d1bfd-102">EF Core 3.0 (目前為預覽版) 包含的中斷性變更</span><span class="sxs-lookup"><span data-stu-id="d1bfd-102">Breaking changes included in EF Core 3.0 (currently in preview)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d1bfd-103">請注意，後續版本的功能集與上市時間，隨時可能會變動，此外，即使我們試圖將此頁面保持在最新狀態，可能還是無法隨時反映我們最新的計劃。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-103">Please note that the feature sets and schedules of future releases are always subject to change, and although we will try to keep this page up to date, it may not reflect our latest plans at all times.</span></span>

<span data-ttu-id="d1bfd-104">當您將針對 EF Core 2.2.x 開發的應用程式升級為 3.0.0 時，下列 API 和行為變更可能會中斷這些應用程式。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-104">The following API and behavior changes have the potential to break applications developed for EF Core 2.2.x when upgrading them to 3.0.0.</span></span>
<span data-ttu-id="d1bfd-105">這些變更預期只會影響[提供者變更](../../providers/provider-log.md)底下記載的資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-105">Changes that we expect to only impact database providers are documented under [provider changes](../../providers/provider-log.md).</span></span>
<span data-ttu-id="d1bfd-106">本文不會記載從某一 3.0 預覽版引進另一個 3.0 預覽版的新功能中斷。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-106">Breaks in new features introduced from one 3.0 preview to another 3.0 preview aren't documented here.</span></span>

## <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a><span data-ttu-id="d1bfd-107">不會再於用戶端評估 LINQ 查詢</span><span class="sxs-lookup"><span data-stu-id="d1bfd-107">LINQ queries are no longer evaluated on the client</span></span>

[<span data-ttu-id="d1bfd-108">追蹤問題 #12795</span><span class="sxs-lookup"><span data-stu-id="d1bfd-108">Tracking Issue #12795</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12795)

> [!IMPORTANT]
> <span data-ttu-id="d1bfd-109">我們將預先宣佈這項中斷。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-109">We are pre-announcing this break.</span></span>
<span data-ttu-id="d1bfd-110">但尚未隨附於任何 3.0 預覽版。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-110">It has not yet shipped in any 3.0 preview.</span></span>

<span data-ttu-id="d1bfd-111">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-111">**Old behavior**</span></span>

<span data-ttu-id="d1bfd-112">3.0 以前，在 EF Core 無法將屬於查詢的運算式轉換成 SQL 或參數時，它會自動在用戶端評估運算式。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-112">Before 3.0, when EF Core couldn't convert an expression that was part of a query to either SQL or a parameter, it automatically evaluated the expression on the client.</span></span>
<span data-ttu-id="d1bfd-113">根據預設，對可能相當耗費資源的運算式進行用戶端評估只會觸發警告。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-113">By default, client evaluation of potentially expensive expressions only triggered a warning.</span></span>

<span data-ttu-id="d1bfd-114">**新行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-114">**New behavior**</span></span>

<span data-ttu-id="d1bfd-115">從 3.0 開始，EF Core 只允許在用戶端評估最上層投影的運算式 (查詢中的最後一個 `Select()` 呼叫)。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-115">Starting with 3.0, EF Core only allows expressions in the top-level projection (the last `Select()` call in the query) to be evaluated on the client.</span></span>
<span data-ttu-id="d1bfd-116">當其他查詢部分中的運算式無法轉換成 SQL 或參數時，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-116">When expressions in any other part of the query can't be converted to either SQL or a parameter, an exception is thrown.</span></span>

<span data-ttu-id="d1bfd-117">**原因**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-117">**Why**</span></span>

<span data-ttu-id="d1bfd-118">查詢的自動用戶端評估可執行許多查詢，即使無法轉譯查詢的重要部分也一樣。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-118">Automatic client evaluation of queries allows many queries to be executed even if important parts of them can't be translated.</span></span>
<span data-ttu-id="d1bfd-119">此行為可能會導致非預期且可能造成傷害的行為，而且該行為可能只會出現在生產環境中。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-119">This behavior can result in unexpected and potentially damaging behavior that may only become evident in production.</span></span>
<span data-ttu-id="d1bfd-120">例如，`Where()` 呼叫中無法轉譯的條件可能會導致資料表中的所有資料列從資料庫伺服器移轉，並在用戶端套用篩選。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-120">For example, a condition in a `Where()` call which can't be translated can cause all rows from the table to be transferred from the database server, and the filter to be applied on the client.</span></span>
<span data-ttu-id="d1bfd-121">如果資料表只包含幾個開發中的資料列，此情況可能很容易未被察覺；但當應用程式移至生產環境時，由於資料表可能包含數百萬個資料列，因此影響會很大。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-121">This situation can easily go undetected if the table contains only a few rows in development, but hit hard when the application moves to production, where the table may contain millions of rows.</span></span>
<span data-ttu-id="d1bfd-122">用戶端評估警告也證明很容易在開發期間遭到忽略。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-122">Client evaluation warnings also proved too easy to ignore during development.</span></span>

<span data-ttu-id="d1bfd-123">此外，自動用戶端評估可能會導致改善特定運算式的查詢轉譯造成版本間非預期的中斷性變更問題。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-123">Besides this, automatic client evaluation can lead to issues in which improving query translation for specific expressions caused unintended breaking changes between releases.</span></span>

<span data-ttu-id="d1bfd-124">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-124">**Mitigations**</span></span>

<span data-ttu-id="d1bfd-125">如果無法完整轉譯查詢，請以可轉譯的格式來重寫查詢，或是使用 `AsEnumerable()`、`ToList()` 或類似函數來明確將資料帶回用戶端，以便接著使用 LINQ-to-Objects 加以處理。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-125">If a query can't be fully translated, then either rewrite the query in a form that can be translated, or use `AsEnumerable()`, `ToList()`, or similar to explicitly bring data back to the client where it can then be further processed using LINQ-to-Objects.</span></span>

## <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a><span data-ttu-id="d1bfd-126">Entity Framework Core 不再屬於 ASP.NET Core 共用架構</span><span class="sxs-lookup"><span data-stu-id="d1bfd-126">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>

[<span data-ttu-id="d1bfd-127">追蹤問題 Announcements#325</span><span class="sxs-lookup"><span data-stu-id="d1bfd-127">Tracking Issue Announcements#325</span></span>](https://github.com/aspnet/Announcements/issues/325)

<span data-ttu-id="d1bfd-128">這項變更已於 ASP.NET Core 3.0-preview 1 推出。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-128">This change was introduced in ASP.NET Core 3.0-preview 1.</span></span> 

<span data-ttu-id="d1bfd-129">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-129">**Old behavior**</span></span>

<span data-ttu-id="d1bfd-130">在 ASP.NET Core 3.0 以前，當您新增 `Microsoft.AspNetCore.App` 或 `Microsoft.AspNetCore.All` 的套件參考時，它會包含 EF Core 及部分 EF Core 資料提供者 (例如 SQL Server 提供者)。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-130">Before ASP.NET Core 3.0, when you added a package reference to `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All`, it would include EF Core and some of the EF Core data providers like the SQL Server provider.</span></span>

<span data-ttu-id="d1bfd-131">**新行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-131">**New behavior**</span></span>

<span data-ttu-id="d1bfd-132">從 3.0 開始，ASP.NET Core 共用架構不會包含 EF Core 或任何 EF Core 資料提供者。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-132">Starting in 3.0, the ASP.NET Core shared framework doesn't include EF Core or any EF Core data providers.</span></span>

<span data-ttu-id="d1bfd-133">**原因**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-133">**Why**</span></span>

<span data-ttu-id="d1bfd-134">在這項變更之前，取得 EF Core 會根據應用程式是否以 ASP.NET Core 和 SQL Server 為目標而需要不同的步驟。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-134">Before this change, getting EF Core required different steps depending on whether the application targeted ASP.NET Core and SQL Server or not.</span></span> <span data-ttu-id="d1bfd-135">此外，升級 ASP.NET Core 會強制升級 EF Core 和 SQL Server 提供者，這不一定符合需求。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-135">Also, upgrading ASP.NET Core forced the upgrade of EF Core and the SQL Server provider, which isn't always desirable.</span></span>

<span data-ttu-id="d1bfd-136">透過這項變更，取得 EF Core 的體驗對所有提供者、支援的 .NET 實作和應用程式類型都相同。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-136">With this change, the experience of getting EF Core is the same across all providers, supported .NET implementations and application types.</span></span>
<span data-ttu-id="d1bfd-137">開發人員現在也可以精確控制何時升級 EF Core 和 EF Core 資料提供者。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-137">Developers can also now control exactly when EF Core and EF Core data providers are upgraded.</span></span>

<span data-ttu-id="d1bfd-138">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-138">**Mitigations**</span></span>

<span data-ttu-id="d1bfd-139">若要在 ASP.NET Core 3.0 應用程式或其他支援的應用程式中使用 EF Core，請明確將套件參考加入應用程式會使用的 EF Core 資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-139">To use EF Core in an ASP.NET Core 3.0 application or any other supported application, explicitly add a package reference to the EF Core database provider that your application will use.</span></span>

## <a name="query-execution-is-logged-at-debug-level"></a><span data-ttu-id="d1bfd-140">查詢執行會在 Debug 層級記錄</span><span class="sxs-lookup"><span data-stu-id="d1bfd-140">Query execution is logged at Debug level</span></span>

[<span data-ttu-id="d1bfd-141">追蹤問題 #14523</span><span class="sxs-lookup"><span data-stu-id="d1bfd-141">Tracking Issue #14523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

<span data-ttu-id="d1bfd-142">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-142">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="d1bfd-143">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-143">**Old behavior**</span></span>

<span data-ttu-id="d1bfd-144">在 EF Core 3.0 以前，查詢和其他命令的執行會在 `Info` 層級記錄。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-144">Before EF Core 3.0, execution of queries and other commands was logged at the `Info` level.</span></span>

<span data-ttu-id="d1bfd-145">**新行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-145">**New behavior**</span></span>

<span data-ttu-id="d1bfd-146">從 EF Core 3.0 開始，命令/SQL 執行的記錄是在 `Debug` 層級。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-146">Starting with EF Core 3.0, logging of command/SQL execution is at the `Debug` level.</span></span>

<span data-ttu-id="d1bfd-147">**原因**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-147">**Why**</span></span>

<span data-ttu-id="d1bfd-148">這項變更的目的是為了減少 `Info` 記錄層級的干擾。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-148">This change was made to reduce the noise at the `Info` log level.</span></span>

<span data-ttu-id="d1bfd-149">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-149">**Mitigations**</span></span>

<span data-ttu-id="d1bfd-150">此記錄事件是由 `RelationalEventId.CommandExecuting` 定義，事件識別碼為 20100。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-150">This logging event is defined by `RelationalEventId.CommandExecuting` with event ID 20100.</span></span>
<span data-ttu-id="d1bfd-151">若要再次於 `Info` 層級記錄 SQL，請在 `Debug` 層級開啟記錄並篩選到這個事件。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-151">To log SQL at the `Info` level again, switch on logging at the `Debug` level and filter to just this event.</span></span>


## <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a><span data-ttu-id="d1bfd-152">實體執行個體上不會再設定暫存索引鍵值</span><span class="sxs-lookup"><span data-stu-id="d1bfd-152">Temporary key values are no longer set onto entity instances</span></span>

[<span data-ttu-id="d1bfd-153">追蹤問題 #12378</span><span class="sxs-lookup"><span data-stu-id="d1bfd-153">Tracking Issue #12378</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

<span data-ttu-id="d1bfd-154">這項變更已於 EF Core 3.0-preview 2 推出。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-154">This change was introduced in EF Core 3.0-preview 2.</span></span>

<span data-ttu-id="d1bfd-155">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-155">**Old behavior**</span></span>

<span data-ttu-id="d1bfd-156">在 EF Core 3.0 以前，會對所有索引鍵屬性指派暫存值，這些屬性稍後會有資料庫產生的實值。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-156">Before EF Core 3.0, temporary values were assigned to all key properties that would later have a real value generated by the database.</span></span>
<span data-ttu-id="d1bfd-157">這些暫存值通常是龐大的負值。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-157">Usually these temporary values were large negative numbers.</span></span>

<span data-ttu-id="d1bfd-158">**新行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-158">**New behavior**</span></span>

<span data-ttu-id="d1bfd-159">從 3.0 開始，EF Core 會將暫存索引鍵值儲存為實體追蹤資訊的一部分，至於索引鍵屬性本身則保持不變。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-159">Starting with 3.0, EF Core stores the temporary key value as part of the entity's tracking information, and leaves the key property itself unchanged.</span></span>

<span data-ttu-id="d1bfd-160">**原因**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-160">**Why**</span></span>

<span data-ttu-id="d1bfd-161">這項變更的目的是為了防止在將某個 `DbContext` 執行個體先前追蹤的實體移至不同的 `DbContext` 執行個體時，錯誤地把暫存索引鍵值變成永久值。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-161">This change was made to prevent temporary key values from erroneously becoming permanent when an entity that has been previously tracked by some `DbContext` instance is moved to a different `DbContext` instance.</span></span> 

<span data-ttu-id="d1bfd-162">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-162">**Mitigations**</span></span>

<span data-ttu-id="d1bfd-163">若應用程式會將主索引鍵指派給外部索引鍵以形成實體間關聯，則可能會在主索引鍵是由存放區產生並屬於 `Added` 狀態的實體時採用舊行為。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-163">Applications that assign primary key values onto foreign keys to form associations between entities may depend on the old behavior if the primary keys are store-generated and belong to entities in the `Added` state.</span></span>
<span data-ttu-id="d1bfd-164">這可透過下列方式來避免：</span><span class="sxs-lookup"><span data-stu-id="d1bfd-164">This can be avoided by:</span></span>
* <span data-ttu-id="d1bfd-165">不使用存放區產生的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-165">Not using store-generated keys.</span></span>
* <span data-ttu-id="d1bfd-166">設定導覽屬性以形成關聯性，而不是設定外部索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-166">Setting navigation properties to form relationships instead of setting foreign key values.</span></span>
* <span data-ttu-id="d1bfd-167">從實體的追蹤資訊取得實際暫存索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-167">Obtain the actual temporary key values from the entity's tracking information.</span></span>
<span data-ttu-id="d1bfd-168">例如，`context.Entry(blog).Property(e => e.Id).CurrentValue` 會傳回暫存值，即使尚未設定 `blog.Id` 本身也一樣。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-168">For example, `context.Entry(blog).Property(e => e.Id).CurrentValue` will return the temporary value even though `blog.Id` itself hasn't been set.</span></span>

## <a name="detectchanges-honors-store-generated-key-values"></a><span data-ttu-id="d1bfd-169">DetectChanges 接受存放區產生的索引鍵值</span><span class="sxs-lookup"><span data-stu-id="d1bfd-169">DetectChanges honors store-generated key values</span></span>

[<span data-ttu-id="d1bfd-170">追蹤問題 #14616</span><span class="sxs-lookup"><span data-stu-id="d1bfd-170">Tracking Issue #14616</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

<span data-ttu-id="d1bfd-171">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-171">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="d1bfd-172">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-172">**Old behavior**</span></span>

<span data-ttu-id="d1bfd-173">在 EF Core 3.0 以前，`DetectChanges` 所發現未被追蹤的實體會以 `Added` 狀態追蹤，並在呼叫 `SaveChanges` 時以新的資料列插入。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-173">Before EF Core 3.0, an untracked entity found by `DetectChanges` would be tracked in the `Added` state and inserted as a new row when `SaveChanges` is called.</span></span>

<span data-ttu-id="d1bfd-174">**新行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-174">**New behavior**</span></span>

<span data-ttu-id="d1bfd-175">從 EF Core 3.0 開始，如果實體使用產生的索引鍵值並已設定一些索引鍵值，則實體會以 `Modified` 狀態追蹤。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-175">Starting with EF Core 3.0, if an entity is using generated key values and some key value is set, then the entity will be tracked in the `Modified` state.</span></span>
<span data-ttu-id="d1bfd-176">這表示實體的資料列假設存在，而且會在呼叫 `SaveChanges` 時更新。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-176">This means that a row for the entity is assumed to exist and it will be updated when `SaveChanges` is called.</span></span>
<span data-ttu-id="d1bfd-177">如果未設定索引鍵值，或者如果實體類型未使用產生的索引鍵，則新的實體仍會如同舊版以 `Added` 追蹤。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-177">If the key value isn't set, or if the entity type isn't using generated keys, then the new entity will still be tracked as `Added` as in previous versions.</span></span>

<span data-ttu-id="d1bfd-178">**原因**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-178">**Why**</span></span>

<span data-ttu-id="d1bfd-179">這項變更的目的是為了更輕鬆一致地使用中斷連接的實體圖形，同時使用存放區產生的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-179">This change was made to make it easier and more consistent to work with disconnected entity graphs while using store-generated keys.</span></span>

<span data-ttu-id="d1bfd-180">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-180">**Mitigations**</span></span>

<span data-ttu-id="d1bfd-181">如果實體類型已設定為使用產生的索引鍵，但針對新的執行個體明確設定了索引鍵值，這項變更可能會中斷應用程式。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-181">This change can break an application if an entity type is configured to use generated keys but key values are explicitly set for new instances.</span></span>
<span data-ttu-id="d1bfd-182">修正方法是明確設定索引鍵屬性不使用產生的值。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-182">The fix is to explicitly configure the key properties to not use generated values.</span></span>
<span data-ttu-id="d1bfd-183">例如，使用 Fluent API：</span><span class="sxs-lookup"><span data-stu-id="d1bfd-183">For example, with the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

<span data-ttu-id="d1bfd-184">或者，使用資料註解：</span><span class="sxs-lookup"><span data-stu-id="d1bfd-184">Or with data annotations:</span></span>

```C#
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```

## <a name="cascade-deletions-now-happen-immediately-by-default"></a><span data-ttu-id="d1bfd-185">現在預設會立即發生串聯刪除</span><span class="sxs-lookup"><span data-stu-id="d1bfd-185">Cascade deletions now happen immediately by default</span></span>

[<span data-ttu-id="d1bfd-186">追蹤問題 #10114</span><span class="sxs-lookup"><span data-stu-id="d1bfd-186">Tracking Issue #10114</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

<span data-ttu-id="d1bfd-187">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-187">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="d1bfd-188">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-188">**Old behavior**</span></span>

<span data-ttu-id="d1bfd-189">在 3.0 以前，除非呼叫 SaveChanges，否則 EF Core 不會套用串聯動作 (刪除必要主體或提供必要主體關聯性時刪除相依實體)。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-189">Before 3.0, EF Core applied cascading actions (deleting dependent entities when a required principal is deleted or when the relationship to a required principal is severed) did not happen until SaveChanges was called.</span></span>

<span data-ttu-id="d1bfd-190">**新行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-190">**New behavior**</span></span>

<span data-ttu-id="d1bfd-191">從 3.0 開始，EF Core 會在偵測到觸發條件時立即套用串聯動作。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-191">Starting with 3.0, EF Core applies cascading actions as soon as the triggering condition is detected.</span></span>
<span data-ttu-id="d1bfd-192">例如，呼叫 `context.Remove()` 刪除主要實體會導致所有追蹤的相關必要相依項目也會立即設定為 `Deleted`。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-192">For example, calling `context.Remove()` to delete a principal entity will result in all tracked related required dependents also being set to `Deleted` immediately.</span></span>

<span data-ttu-id="d1bfd-193">**原因**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-193">**Why**</span></span>

<span data-ttu-id="d1bfd-194">這項變更的目的是為了改善資料繫結和稽核情節的體驗，在這些情節中了解呼叫 `SaveChanges`「之前」將刪除哪些實體是很重要的。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-194">This change was made to improve the experience for data binding and auditing scenarios where it is important to understand which entities will be deleted _before_ `SaveChanges` is called.</span></span>

<span data-ttu-id="d1bfd-195">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-195">**Mitigations**</span></span>

<span data-ttu-id="d1bfd-196">透過設定 `context.ChangedTracker` 可以還原舊行為。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-196">The previous behavior can be restored through settings on `context.ChangedTracker`.</span></span>
<span data-ttu-id="d1bfd-197">例如：</span><span class="sxs-lookup"><span data-stu-id="d1bfd-197">For example:</span></span>

```C#
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```

## <a name="query-types-are-consolidated-with-entity-types"></a><span data-ttu-id="d1bfd-198">查詢類型會與實體類型合併</span><span class="sxs-lookup"><span data-stu-id="d1bfd-198">Query types are consolidated with entity types</span></span>

[<span data-ttu-id="d1bfd-199">追蹤問題 #14194</span><span class="sxs-lookup"><span data-stu-id="d1bfd-199">Tracking Issue #14194</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

<span data-ttu-id="d1bfd-200">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-200">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="d1bfd-201">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-201">**Old behavior**</span></span>

<span data-ttu-id="d1bfd-202">在 EF Core 3.0 以前，[查詢類型](xref:core/modeling/query-types)可讓您查詢未以結構化方式定義主索引鍵的資料。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-202">Before EF Core 3.0, [query types](xref:core/modeling/query-types) were a means to query data that doesn't define a primary key in a structured way.</span></span>
<span data-ttu-id="d1bfd-203">換句話說，查詢類型是用於對應沒有索引鍵的實體類型 (較有可能來自檢視，但也有可能來自資料表)，而一般實體類型是用於索引鍵可供使用時 (較有可能來自資料表，但也有可能來自檢視)。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-203">That is, a query type was used for mapping entity types without keys (more likely from a view, but possibly from a table) while a regular entity type was used when a key was available (more likely from a table, but possibly from a view).</span></span>

<span data-ttu-id="d1bfd-204">**新行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-204">**New behavior**</span></span>

<span data-ttu-id="d1bfd-205">查詢類型現在會成為沒有主索引鍵的實體類型。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-205">A query type now becomes just an entity type without a primary key.</span></span>
<span data-ttu-id="d1bfd-206">無索引鍵的實體類型功能與舊版查詢類型相同。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-206">Keyless entity types have the same functionality as query types in previous versions.</span></span>

<span data-ttu-id="d1bfd-207">**原因**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-207">**Why**</span></span>

<span data-ttu-id="d1bfd-208">這項變更的目的是為了降低查詢類型用途的混淆。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-208">This change was made to reduce the confusion around the purpose of query types.</span></span>
<span data-ttu-id="d1bfd-209">具體來說，它們是無索引鍵的實體類型，因此本質上是唯讀的，但不應該只因為實體類型必須是唯讀就使用。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-209">Specifically, they are keyless entity types and they are inherently read-only because of this, but they should not be used just because an entity type needs to be read-only.</span></span>
<span data-ttu-id="d1bfd-210">同樣地，它們通常會對應至檢視，但這只是因為檢視通常未定義索引鍵。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-210">Likewise, they are often mapped to views, but this is only because views often don't define keys.</span></span>

<span data-ttu-id="d1bfd-211">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-211">**Mitigations**</span></span>

<span data-ttu-id="d1bfd-212">API 的下列組件現已淘汰：</span><span class="sxs-lookup"><span data-stu-id="d1bfd-212">The following parts of the API are now obsolete:</span></span>
* <span data-ttu-id="d1bfd-213">**`ModelBuilder.Query<>()`** - 必須改為呼叫 `ModelBuilder.Entity<>().HasNoKey()` 將實體類型標示為沒有索引鍵。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-213">**`ModelBuilder.Query<>()`** - Instead `ModelBuilder.Entity<>().HasNoKey()` needs to be called to mark an entity type as having no keys.</span></span>
<span data-ttu-id="d1bfd-214">為了避免在必須有主索引鍵但不符合慣例時設定錯誤，目前仍未將此設定為慣例。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-214">This would still not be configured by convention to avoid misconfiguration when a primary key is expected, but doesn't match the convention.</span></span>
* <span data-ttu-id="d1bfd-215">**`DbQuery<>`** - 應改用 `DbSet<>`。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-215">**`DbQuery<>`** - Instead `DbSet<>` should be used.</span></span>
* <span data-ttu-id="d1bfd-216">**`DbContext.Query<>()`** - 應改用 `DbContext.Set<>()`。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-216">**`DbContext.Query<>()`** - Instead `DbContext.Set<>()` should be used.</span></span>

## <a name="configuration-api-for-owned-type-relationships-has-changed"></a><span data-ttu-id="d1bfd-217">自有類型關聯性的設定 API 已變更</span><span class="sxs-lookup"><span data-stu-id="d1bfd-217">Configuration API for owned type relationships has changed</span></span>

<span data-ttu-id="d1bfd-218">[追蹤問題 #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[追蹤問題 #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[追蹤問題 #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span><span class="sxs-lookup"><span data-stu-id="d1bfd-218">[Tracking Issue #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[Tracking Issue #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[Tracking Issue #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span></span>

<span data-ttu-id="d1bfd-219">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-219">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="d1bfd-220">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-220">**Old behavior**</span></span>

<span data-ttu-id="d1bfd-221">在 EF Core 3.0 以前，會在呼叫 `OwnsOne` 或 `OwnsMany` 之後直接執行自有關聯性的設定。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-221">Before EF Core 3.0, configuration of the owned relationship was performed directly after the `OwnsOne` or `OwnsMany` call.</span></span> 

<span data-ttu-id="d1bfd-222">**新行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-222">**New behavior**</span></span>

<span data-ttu-id="d1bfd-223">從 EF Core 3.0 開始，現在會有 Fluent API 使用 `WithOwner()` 將導覽屬性設定為擁有者。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-223">Starting with EF Core 3.0, there is now fluent API to configure a navigation property to the owner using `WithOwner()`.</span></span>
<span data-ttu-id="d1bfd-224">例如：</span><span class="sxs-lookup"><span data-stu-id="d1bfd-224">For example:</span></span>

```C#
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

<span data-ttu-id="d1bfd-225">擁有者與自有之間關聯性的相關設定現在應該在 `WithOwner()` 之後鏈結，類似於其他關聯性的設定方式。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-225">The configuration related to the relationship between owner and owned should now be chained after `WithOwner()` similarly to how other relationships are configured.</span></span>
<span data-ttu-id="d1bfd-226">但自有類型本身的設定仍會在 `OwnsOne()/OwnsMany()` 之後鏈結。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-226">While the configuration for the owned type itself would still be chained after `OwnsOne()/OwnsMany()`.</span></span>
<span data-ttu-id="d1bfd-227">例如：</span><span class="sxs-lookup"><span data-stu-id="d1bfd-227">For example:</span></span>

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

<span data-ttu-id="d1bfd-228">此外，使用自有類型目標呼叫 `Entity()`、`HasOne()` 或 `Set()` 現在會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-228">Additionally calling `Entity()`, `HasOne()`, or `Set()` with an owned type target will now throw an exception.</span></span>

<span data-ttu-id="d1bfd-229">**原因**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-229">**Why**</span></span>

<span data-ttu-id="d1bfd-230">這項變更的目的是為了更清楚地劃分設定自有類型本身，以及設定自有類型的「關聯性」。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-230">This change was made to create a cleaner separation between configuring the owned type itself and the _relationship to_ the owned type.</span></span>
<span data-ttu-id="d1bfd-231">如此可避免 `HasForeignKey` 等方法的模稜兩可和混淆。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-231">This in turn removes ambiguity and confusion around methods like `HasForeignKey`.</span></span>

<span data-ttu-id="d1bfd-232">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-232">**Mitigations**</span></span>

<span data-ttu-id="d1bfd-233">將自有類型關聯性的設定變更為使用新的 API 介面，如上述範例所示。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-233">Change configuration of owned type relationships to use the new API surface as shown in the example above.</span></span>

## <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a><span data-ttu-id="d1bfd-234">外部索引鍵屬性慣例不會再比對與主體屬性相同的名稱</span><span class="sxs-lookup"><span data-stu-id="d1bfd-234">The foreign key property convention no longer matches same name as the principal property</span></span>

[<span data-ttu-id="d1bfd-235">追蹤問題 #13274</span><span class="sxs-lookup"><span data-stu-id="d1bfd-235">Tracking Issue #13274</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

<span data-ttu-id="d1bfd-236">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-236">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="d1bfd-237">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-237">**Old behavior**</span></span>

<span data-ttu-id="d1bfd-238">請考慮下列模型：</span><span class="sxs-lookup"><span data-stu-id="d1bfd-238">Consider the following model:</span></span>
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
<span data-ttu-id="d1bfd-239">在 EF Core 3.0 以前，`CustomerId` 屬性依照慣例會用於外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-239">Before EF Core 3.0, the `CustomerId` property would be used for the foreign key by convention.</span></span>
<span data-ttu-id="d1bfd-240">不過，如果 `Order` 是自有類型，則這也會將 `CustomerId` 設為主索引鍵，而這通常不符合預期。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-240">However, if `Order` is an owned type, then this would also make `CustomerId` the primary key and this isn't usually the expectation.</span></span>

<span data-ttu-id="d1bfd-241">**新行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-241">**New behavior**</span></span>

<span data-ttu-id="d1bfd-242">從 3.0 開始，如果屬性的名稱與主體屬性相同，依照慣例，EF Core 不會嘗試將屬性用於外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-242">Starting with 3.0, EF Core won't try to use properties for foreign keys by convention if they have the same name as the principal property.</span></span>
<span data-ttu-id="d1bfd-243">但仍會比對與主體屬性名稱串連的主體類型名稱，以及與主體屬性名稱模式串連的導覽名稱。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-243">Principal type name concatenated with principal property name, and navigation name concatenated with principal property name patterns are still matched.</span></span>
<span data-ttu-id="d1bfd-244">例如：</span><span class="sxs-lookup"><span data-stu-id="d1bfd-244">For example:</span></span>

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

<span data-ttu-id="d1bfd-245">**原因**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-245">**Why**</span></span>

<span data-ttu-id="d1bfd-246">這項變更的目的是為了避免錯誤地在自有類型上定義主索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-246">This change was made to avoid erroneously defining a primary key property on the owned type.</span></span>

<span data-ttu-id="d1bfd-247">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-247">**Mitigations**</span></span>

<span data-ttu-id="d1bfd-248">如果屬性預定會作為外部索引鍵，並因此成為主索引鍵的一部分，請明確進行這類設定。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-248">If the property was intended to be the foreign key, and hence part of the primary key, then explicitly configure it as such.</span></span>

## <a name="each-property-uses-independent-in-memory-integer-key-generation"></a><span data-ttu-id="d1bfd-249">每個屬性會使用獨立的記憶體內部整數索引鍵產生</span><span class="sxs-lookup"><span data-stu-id="d1bfd-249">Each property uses independent in-memory integer key generation</span></span>

[<span data-ttu-id="d1bfd-250">追蹤問題 #6872</span><span class="sxs-lookup"><span data-stu-id="d1bfd-250">Tracking Issue #6872</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

<span data-ttu-id="d1bfd-251">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-251">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="d1bfd-252">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-252">**Old behavior**</span></span>

<span data-ttu-id="d1bfd-253">在 EF Core 3.0 以前，會針對所有記憶體內部整數索引鍵屬性使用一個共用值產生器。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-253">Before EF Core 3.0, one shared value generator was used for all in-memory integer key properties.</span></span>

<span data-ttu-id="d1bfd-254">**新行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-254">**New behavior**</span></span>

<span data-ttu-id="d1bfd-255">從 EF Core 3.0 開始，當使用記憶體內部資料庫時，每個整數索引鍵屬性都會取得自己的值產生器。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-255">Starting with EF Core 3.0, each integer key property gets its own value generator when using the in-memory database.</span></span>
<span data-ttu-id="d1bfd-256">此外，如果已刪除資料庫，則會重設所有資料表的索引鍵產生。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-256">Also, if the database is deleted, then key generation is reset for all tables.</span></span>

<span data-ttu-id="d1bfd-257">**原因**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-257">**Why**</span></span>

<span data-ttu-id="d1bfd-258">這項變更的目的是為了讓記憶體內部索引鍵產生與實際資料庫索引鍵產生更加一致，並改善在使用記憶體內部資料庫時隔離個別測試的能力。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-258">This change was made to align in-memory key generation more closely to real database key generation and to improve the ability to isolate tests from each other when using the in-memory database.</span></span>

<span data-ttu-id="d1bfd-259">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-259">**Mitigations**</span></span>

<span data-ttu-id="d1bfd-260">這可能會中斷需要設定特定記憶體內部索引鍵值的應用程式。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-260">This can break an application that is relying on specific in-memory key values to be set.</span></span>
<span data-ttu-id="d1bfd-261">請考慮改為不依賴特定索引鍵值，或更新以符合新行為。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-261">Consider instead not relying on specific key values, or updating to match the new behavior.</span></span>

## <a name="backing-fields-are-used-by-default"></a><span data-ttu-id="d1bfd-262">預設會使用支援欄位</span><span class="sxs-lookup"><span data-stu-id="d1bfd-262">Backing fields are used by default</span></span>

[<span data-ttu-id="d1bfd-263">追蹤問題 #12430</span><span class="sxs-lookup"><span data-stu-id="d1bfd-263">Tracking Issue #12430</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

<span data-ttu-id="d1bfd-264">這項變更已於 EF Core 3.0-preview 2 推出。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-264">This change was introduced in EF Core 3.0-preview 2.</span></span>

<span data-ttu-id="d1bfd-265">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-265">**Old behavior**</span></span>

<span data-ttu-id="d1bfd-266">在 3.0 以前，即使屬性的支援欄位已知，EF Core 預設仍會使用屬性 getter 和 setter 方法來讀取和寫入屬性值。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-266">Before 3.0, even if the backing field for a property was known, EF Core would still by default read and write the property value using the property getter and setter methods.</span></span>
<span data-ttu-id="d1bfd-267">例外是查詢執行，其中如果支援欄位已知，則會直接設定。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-267">The exception to this was query execution, where the backing field would be set directly if known.</span></span>

<span data-ttu-id="d1bfd-268">**新行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-268">**New behavior**</span></span>

<span data-ttu-id="d1bfd-269">從 EF Core 3.0 開始，如果屬性的支援欄位已知，則一律會使用支援欄位來讀取和寫入該屬性。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-269">Starting with EF Core 3.0, if the backing field for a property is known, then will always read and write that property using the backing field.</span></span>
<span data-ttu-id="d1bfd-270">如果應用程式需要將額外的行為編碼到 getter 或 setter 方法中，這可能會導致應用程式中斷。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-270">This could cause an application break if the application is relying on additional behavior coded into the getter or setter methods.</span></span>

<span data-ttu-id="d1bfd-271">**原因**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-271">**Why**</span></span>

<span data-ttu-id="d1bfd-272">這項變更的目的是為了防止 EF Core 預設在執行涉及實體的資料庫作業時，錯誤地觸發商務邏輯。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-272">This change was made to prevent EF Core from erroneously triggering business logic by default when performing database operations involving the entities.</span></span>

<span data-ttu-id="d1bfd-273">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-273">**Mitigations**</span></span>

<span data-ttu-id="d1bfd-274">透過在 modelBuilder Fluent API 中設定屬性存取模式可以還原 3.0 以前的行為。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-274">The pre-3.0 behavior can be restored through configuration of the property access mode in the modelBuilder fluent API.</span></span>
<span data-ttu-id="d1bfd-275">例如：</span><span class="sxs-lookup"><span data-stu-id="d1bfd-275">For example:</span></span>

```C#
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

## <a name="throw-if-multiple-compatible-backing-fields-are-found"></a><span data-ttu-id="d1bfd-276">找到多個相容的支援欄位時擲回</span><span class="sxs-lookup"><span data-stu-id="d1bfd-276">Throw if multiple compatible backing fields are found</span></span>

[<span data-ttu-id="d1bfd-277">追蹤問題 #12523</span><span class="sxs-lookup"><span data-stu-id="d1bfd-277">Tracking Issue #12523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

<span data-ttu-id="d1bfd-278">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-278">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="d1bfd-279">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-279">**Old behavior**</span></span>

<span data-ttu-id="d1bfd-280">在 EF Core 3.0 以前，如果有多個欄位符合尋找屬性支援欄位的規則，則會根據特定優先順序來選擇一個欄位。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-280">Before EF Core 3.0, if multiple fields matched the rules for finding the backing field of a property, then one field would be chosen based on some precedence order.</span></span>
<span data-ttu-id="d1bfd-281">這可能會導致在模稜兩可的情況下使用錯誤的欄位。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-281">This could cause the wrong field to be used in ambiguous cases.</span></span>

<span data-ttu-id="d1bfd-282">**新行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-282">**New behavior**</span></span>

<span data-ttu-id="d1bfd-283">從 EF Core 3.0 開始，如果有多個欄位符合相同的屬性，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-283">Starting with EF Core 3.0, if multiple fields are matched to the same property, then an exception is thrown.</span></span>

<span data-ttu-id="d1bfd-284">**原因**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-284">**Why**</span></span>

<span data-ttu-id="d1bfd-285">這項變更的目的是為了避免在只能有一個正確欄位的情況下，自動使用某個欄位而非另一個欄位。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-285">This change was made to avoid silently using one field over another when only one can be correct.</span></span>

<span data-ttu-id="d1bfd-286">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-286">**Mitigations**</span></span>

<span data-ttu-id="d1bfd-287">若屬性的支援欄位模稜兩可，則必須明確指定要使用的欄位。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-287">Properties with ambiguous backing fields must have the field to use specified explicitly.</span></span>
<span data-ttu-id="d1bfd-288">例如，使用 Fluent API：</span><span class="sxs-lookup"><span data-stu-id="d1bfd-288">For example, using the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

## <a name="dbcontextentry-now-performs-a-local-detectchanges"></a><span data-ttu-id="d1bfd-289">DbContext.Entry 現在會執行本機 DetectChanges</span><span class="sxs-lookup"><span data-stu-id="d1bfd-289">DbContext.Entry now performs a local DetectChanges</span></span>

[<span data-ttu-id="d1bfd-290">追蹤問題 #13552</span><span class="sxs-lookup"><span data-stu-id="d1bfd-290">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="d1bfd-291">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-291">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="d1bfd-292">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-292">**Old behavior**</span></span>

<span data-ttu-id="d1bfd-293">在 EF Core 3.0 以前，呼叫 `DbContext.Entry` 會導致偵測所有追蹤實體的變更。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-293">Before EF Core 3.0, calling `DbContext.Entry` would cause changes to be detected for all tracked entities.</span></span>
<span data-ttu-id="d1bfd-294">這可確保在 `EntityEntry` 中公開的狀態為最新狀態。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-294">This ensured that the state exposed in the `EntityEntry` was up-to-date.</span></span>

<span data-ttu-id="d1bfd-295">**新行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-295">**New behavior**</span></span>

<span data-ttu-id="d1bfd-296">從 EF Core 3.0 開始，呼叫 `DbContext.Entry` 現在只會嘗試在指定實體及其相關的任何追蹤主要實體中偵測變更。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-296">Starting with EF Core 3.0, calling `DbContext.Entry` will now only attempt to detect changes in the given entity and any tracked principal entities related to it.</span></span>
<span data-ttu-id="d1bfd-297">這表示呼叫此方法可能還無法偵測到其他位置的變更，因此可能會影響應用程式狀態。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-297">This means that changes elsewhere may not have been detected by calling this method, which could have implications on application state.</span></span>

<span data-ttu-id="d1bfd-298">請注意，如果 `ChangeTracker.AutoDetectChangesEnabled` 設定為 `false`，甚至是此本機變更偵測都會停用。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-298">Note that if `ChangeTracker.AutoDetectChangesEnabled` is set to `false` then even this local change detection will be disabled.</span></span>

<span data-ttu-id="d1bfd-299">其他導致變更偵測的方法 (例如 `ChangeTracker.Entries` 和 `SaveChanges`) 仍會對所有追蹤實體進行完整的 `DetectChanges`。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-299">Other methods that cause change detection--for example `ChangeTracker.Entries` and `SaveChanges`--still cause a full `DetectChanges` of all tracked entities.</span></span>

<span data-ttu-id="d1bfd-300">**原因**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-300">**Why**</span></span>

<span data-ttu-id="d1bfd-301">這項變更的目的是為了改善使用 `context.Entry` 的預設效能。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-301">This change was made to improve the default performance of using `context.Entry`.</span></span>

<span data-ttu-id="d1bfd-302">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-302">**Mitigations**</span></span>

<span data-ttu-id="d1bfd-303">在呼叫 `Entry` 之前明確呼叫 `ChgangeTracker.DetectChanges()` 可確保 3.0 以前的行為。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-303">Call `ChgangeTracker.DetectChanges()` explicitly before calling `Entry` to ensure the pre-3.0 behavior.</span></span>

## <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a><span data-ttu-id="d1bfd-304">字串和位元組陣列索引鍵預設不是由用戶端產生</span><span class="sxs-lookup"><span data-stu-id="d1bfd-304">String and byte array keys are not client-generated by default</span></span>

[<span data-ttu-id="d1bfd-305">追蹤問題 #14617</span><span class="sxs-lookup"><span data-stu-id="d1bfd-305">Tracking Issue #14617</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

<span data-ttu-id="d1bfd-306">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-306">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="d1bfd-307">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-307">**Old behavior**</span></span>

<span data-ttu-id="d1bfd-308">在 EF Core 3.0 以前，可以使用 `string` 和 `byte[]` 索引鍵屬性，而不需要明確設定非 Null 值。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-308">Before EF Core 3.0, `string` and `byte[]` key properties could be used without explicitly setting a non-null value.</span></span>
<span data-ttu-id="d1bfd-309">在此情況下，會在用戶端以 GUID 形式產生索引鍵值，再序列化為 `byte[]` 的位元組。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-309">In such a case, the key value would be generated on the client as a GUID, serialized to bytes for `byte[]`.</span></span>

<span data-ttu-id="d1bfd-310">**新行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-310">**New behavior**</span></span>

<span data-ttu-id="d1bfd-311">從 EF Core 3.0 開始，系統會擲回例外狀況，指出尚未設定任何索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-311">Starting with EF Core 3.0 an exception will be thrown indicating that no key value has been set.</span></span>

<span data-ttu-id="d1bfd-312">**原因**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-312">**Why**</span></span>

<span data-ttu-id="d1bfd-313">這項變更是因為用戶端產生的 `string`/`byte[]` 值通常不太有用，而且預設行為使它很難以一般方式來推論產生的索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-313">This change was made because client-generated `string`/`byte[]` values generally aren't useful, and the default behavior made it hard to reason about generated key values in a common way.</span></span>

<span data-ttu-id="d1bfd-314">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-314">**Mitigations**</span></span>

<span data-ttu-id="d1bfd-315">藉由明確指定索引鍵屬性應該在未設定其他非 Null 值時使用產生的值，即可取得 3.0 以前的行為。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-315">The pre-3.0 behavior can be obtained by explicitly specifying that the key properties should use generated values if no other non-null value is set.</span></span>
<span data-ttu-id="d1bfd-316">例如，使用 Fluent API：</span><span class="sxs-lookup"><span data-stu-id="d1bfd-316">For example, with the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

<span data-ttu-id="d1bfd-317">或者，使用資料註解：</span><span class="sxs-lookup"><span data-stu-id="d1bfd-317">Or with data annotations:</span></span>

```C#
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

## <a name="iloggerfactory-is-now-a-scoped-service"></a><span data-ttu-id="d1bfd-318">ILoggerFactory 現在是限定範圍的服務</span><span class="sxs-lookup"><span data-stu-id="d1bfd-318">ILoggerFactory is now a scoped service</span></span>

[<span data-ttu-id="d1bfd-319">追蹤問題 #14698</span><span class="sxs-lookup"><span data-stu-id="d1bfd-319">Tracking Issue #14698</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

<span data-ttu-id="d1bfd-320">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-320">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="d1bfd-321">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-321">**Old behavior**</span></span>

<span data-ttu-id="d1bfd-322">在 EF Core 3.0 以前，`ILoggerFactory` 會註冊為單一服務。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-322">Before EF Core 3.0, `ILoggerFactory` was registered as a singleton service.</span></span>

<span data-ttu-id="d1bfd-323">**新行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-323">**New behavior**</span></span>

<span data-ttu-id="d1bfd-324">從 EF Core 3.0 開始，`ILoggerFactory` 現在會註冊為限定範圍。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-324">Starting with EF Core 3.0, `ILoggerFactory` is now registered as scoped.</span></span>

<span data-ttu-id="d1bfd-325">**原因**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-325">**Why**</span></span>

<span data-ttu-id="d1bfd-326">這項變更的目的是為了允許記錄器與 `DbContext` 執行個體產生關聯，這可啟用其他功能，並避免某些異常行為案例，例如內部服務提供者遽增。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-326">This change was made to allow association of a logger with a `DbContext` instance, which enables other functionality and removes some cases of pathological behavior such as an explosion of internal service providers.</span></span>

<span data-ttu-id="d1bfd-327">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-327">**Mitigations**</span></span>

<span data-ttu-id="d1bfd-328">這項變更不應影響應用程式程式碼，除非在 EF Core 內部服務提供者上使用自訂服務註冊該程式碼。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-328">This change should not impact application code unless it is registering and using custom services on the EF Core internal service provider.</span></span>
<span data-ttu-id="d1bfd-329">但這並不常見。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-329">This isn't common.</span></span>
<span data-ttu-id="d1bfd-330">在這些情況下，大部分的項目仍會運作，但相依於 `ILoggerFactory` 的任何單一服務需要變更，才能以不同方式取得 `ILoggerFactory`。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-330">In these cases, most things will still work, but any singleton service that was depending on `ILoggerFactory` will need to be changed to obtain the `ILoggerFactory` in a different way.</span></span>

<span data-ttu-id="d1bfd-331">如果您遇到上述情況，請在 [EF Core GitHub 問題追蹤器](https://github.com/aspnet/EntityFrameworkCore/issues)上提出問題，讓我們知道您使用 `ILoggerFactory` 的方式，以便進一步了解未來如何才不會再次中斷。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-331">If you run into situations like this, please file an issue at on the [EF Core GitHub issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues) to let us know how you are using `ILoggerFactory` such that we can better understand how not to break this again in the future.</span></span>

## <a name="idbcontextoptionsextensionwithdebuginfo-merged-into-idbcontextoptionsextension"></a><span data-ttu-id="d1bfd-332">IDbContextOptionsExtensionWithDebugInfo 已合併到 IDbContextOptionsExtension 中</span><span class="sxs-lookup"><span data-stu-id="d1bfd-332">IDbContextOptionsExtensionWithDebugInfo merged into IDbContextOptionsExtension</span></span>

[<span data-ttu-id="d1bfd-333">追蹤問題 #13552</span><span class="sxs-lookup"><span data-stu-id="d1bfd-333">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="d1bfd-334">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-334">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="d1bfd-335">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-335">**Old behavior**</span></span>

<span data-ttu-id="d1bfd-336">`IDbContextOptionsExtensionWithDebugInfo` 是從 `IDbContextOptionsExtension` 擴充的額外選擇性介面，以避免在 2.x 發行週期內對介面進行中斷性變更。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-336">`IDbContextOptionsExtensionWithDebugInfo` was an additional optional interface extended from `IDbContextOptionsExtension` to avoid making a breaking change to the interface during the 2.x release cycle.</span></span>

<span data-ttu-id="d1bfd-337">**新行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-337">**New behavior**</span></span>

<span data-ttu-id="d1bfd-338">這些介面現在會一起合併到 `IDbContextOptionsExtension` 中。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-338">The interfaces are now merged together into `IDbContextOptionsExtension`.</span></span>

<span data-ttu-id="d1bfd-339">**原因**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-339">**Why**</span></span>

<span data-ttu-id="d1bfd-340">這項變更是因為這些介面在概念上是一個介面。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-340">This change was made because the interfaces are conceptually one.</span></span>

<span data-ttu-id="d1bfd-341">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-341">**Mitigations**</span></span>

<span data-ttu-id="d1bfd-342">您必須更新 `IDbContextOptionsExtension` 的所有實作，才能支援新成員。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-342">Any implementations of `IDbContextOptionsExtension` will need to be updated to support the new member.</span></span>

## <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a><span data-ttu-id="d1bfd-343">消極式載入 Proxy 停止假設導覽屬性已完全載入</span><span class="sxs-lookup"><span data-stu-id="d1bfd-343">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>

[<span data-ttu-id="d1bfd-344">追蹤問題 #12780</span><span class="sxs-lookup"><span data-stu-id="d1bfd-344">Tracking Issue #12780</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

<span data-ttu-id="d1bfd-345">這項變更將於 EF Core 3.0-preview 4 中引進。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-345">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="d1bfd-346">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-346">**Old behavior**</span></span>

<span data-ttu-id="d1bfd-347">在 EF Core 3.0 以前，一旦處置 `DbContext` 之後，就無從得知實體上取自該內容的指定導覽屬性是否已完全載入。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-347">Before EF Core 3.0, once a `DbContext` was disposed there was no way of knowing if a given navigation property on an entity obtained from that context was fully loaded or not.</span></span>
<span data-ttu-id="d1bfd-348">Proxy 會改為假設如有非 Null 值，會載入參考導覽；如果不是空的，則會載入集合導覽。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-348">Proxies would instead assume that a reference navigation is loaded if it has a non-null value, and that a collection navigation is loaded if it isn't empty.</span></span>
<span data-ttu-id="d1bfd-349">在這些情況下，嘗試消極式載入不會執行任何作業。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-349">In these cases, attempting to lazy-load would be a no-op.</span></span>

<span data-ttu-id="d1bfd-350">**新行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-350">**New behavior**</span></span>

<span data-ttu-id="d1bfd-351">從 EF Core 3.0 開始，Proxy 會追蹤是否載入導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-351">Starting with EF Core 3.0, proxies keep track of whether or not a navigation property is loaded.</span></span>
<span data-ttu-id="d1bfd-352">這表示嘗試存取在處置內容之後載入的導覽屬性一律不會執行任何作業，即使已載入的導覽是空的或 Null 也一樣。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-352">This means attempting to access a navigation property that is loaded after the context has been disposed will always be a no-op, even when the loaded navigation is empty or null.</span></span>
<span data-ttu-id="d1bfd-353">相反地，如果在處置內容之後嘗試存取未載入的導覽屬性，則會擲回例外狀況，即使導覽屬性不是空集合也一樣。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-353">Conversely, attempting to access a navigation property that isn't loaded will throw an exception if the context is disposed even if the navigation property is a non-empty collection.</span></span>
<span data-ttu-id="d1bfd-354">如果發生這種情況，則表示應用程式程式碼嘗試在無效的時間使用消極式載入，應用程式應該變更為不要這麼做。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-354">If this situation arises, it means the application code is attempting to use lazy-loading at an invalid time, and the application should be changed to not do this.</span></span>

<span data-ttu-id="d1bfd-355">**原因**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-355">**Why**</span></span>

<span data-ttu-id="d1bfd-356">這項變更的目的是為了在已處置的 `DbContext` 執行個體上嘗試消極式載入時，使行為一致且正確。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-356">This change was made to make the behavior consistent and correct when attempting to lazy-load on a disposed `DbContext` instance.</span></span>

<span data-ttu-id="d1bfd-357">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-357">**Mitigations**</span></span>

<span data-ttu-id="d1bfd-358">將應用程式程式碼更新為不要嘗試對已處置的內容進行消極式載入，或將此設定為不執行任何作業，如例外狀況訊息中所述。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-358">Update application code to not attempt lazy-loading with a disposed context, or configure this to be a no-op as described in the exception message.</span></span>

## <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a><span data-ttu-id="d1bfd-359">過度建立內部服務提供者現在預設是錯誤</span><span class="sxs-lookup"><span data-stu-id="d1bfd-359">Excessive creation of internal service providers is now an error by default</span></span>

[<span data-ttu-id="d1bfd-360">追蹤問題 #10236</span><span class="sxs-lookup"><span data-stu-id="d1bfd-360">Tracking Issue #10236</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

<span data-ttu-id="d1bfd-361">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-361">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="d1bfd-362">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-362">**Old behavior**</span></span>

<span data-ttu-id="d1bfd-363">在 EF Core 3.0 以前，當應用程式建立異常數目的內部服務提供者時，會記錄一則警告。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-363">Before EF Core 3.0, a warning would be logged for an application creating a pathological number of internal service providers.</span></span>

<span data-ttu-id="d1bfd-364">**新行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-364">**New behavior**</span></span>

<span data-ttu-id="d1bfd-365">從 EF Core 3.0 開始，此警告現在會視為錯誤，並會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-365">Starting with EF Core 3.0, this warning is now considered and error and an exception is thrown.</span></span> 

<span data-ttu-id="d1bfd-366">**原因**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-366">**Why**</span></span>

<span data-ttu-id="d1bfd-367">這項變更的目的是為了透過更明確公開此異常案例，藉以開發更完善的應用程式程式碼。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-367">This change was made to drive better application code through exposing this pathological case more explicitly.</span></span>

<span data-ttu-id="d1bfd-368">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-368">**Mitigations**</span></span>

<span data-ttu-id="d1bfd-369">遇到此錯誤時的最適當動作是了解根本原因，並停止建立這麼多的內部服務提供者。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-369">The most appropriate cause of action on encountering this error is to understand the root cause and stop creating so many internal service providers.</span></span>
<span data-ttu-id="d1bfd-370">不過，透過設定 `DbContextOptionsBuilder` 可以將錯誤轉換回警告。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-370">However, the error can be converted back to a warning (or ignored) via configuration on the `DbContextOptionsBuilder`.</span></span>
<span data-ttu-id="d1bfd-371">例如：</span><span class="sxs-lookup"><span data-stu-id="d1bfd-371">For example:</span></span>

```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

## <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a><span data-ttu-id="d1bfd-372">Relational:TypeMapping 註解現僅為 TypeMapping</span><span class="sxs-lookup"><span data-stu-id="d1bfd-372">The Relational:TypeMapping annotation is now just TypeMapping</span></span>

[<span data-ttu-id="d1bfd-373">追蹤問題 #9913</span><span class="sxs-lookup"><span data-stu-id="d1bfd-373">Tracking Issue #9913</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

<span data-ttu-id="d1bfd-374">這項變更已於 EF Core 3.0-preview 2 推出。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-374">This change was introduced in EF Core 3.0-preview 2.</span></span>

<span data-ttu-id="d1bfd-375">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-375">**Old behavior**</span></span>

<span data-ttu-id="d1bfd-376">類型對應註解的註解名稱之前是 "Relational:TypeMapping"。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-376">The annotation name for type mapping annotations was "Relational:TypeMapping".</span></span>

<span data-ttu-id="d1bfd-377">**新行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-377">**New behavior**</span></span>

<span data-ttu-id="d1bfd-378">類型對應註解的註解名稱現在是 "TypeMapping"。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-378">The annotation name for type mapping annotations is now "TypeMapping".</span></span>

<span data-ttu-id="d1bfd-379">**原因**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-379">**Why**</span></span>

<span data-ttu-id="d1bfd-380">類型對應現在不只用於關聯式資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-380">Type mappings are now used for more than just relational database providers.</span></span>

<span data-ttu-id="d1bfd-381">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-381">**Mitigations**</span></span>

<span data-ttu-id="d1bfd-382">這只會中斷直接將類型對應當做註解存取的應用程式，但這並不常見。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-382">This will only break applications that access the type mapping directly as an annotation, which isn't common.</span></span>
<span data-ttu-id="d1bfd-383">最適當的修正動作是使用 API 介面存取類型對應，而不是直接使用註解。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-383">The most appropriate action to fix is to use API surface to access type mappings rather than using the annotation directly.</span></span>

## <a name="totable-on-a-derived-type-throws-an-exception"></a><span data-ttu-id="d1bfd-384">衍生類型上的 ToTable 會擲回例外狀況</span><span class="sxs-lookup"><span data-stu-id="d1bfd-384">ToTable on a derived type throws an exception</span></span> 

[<span data-ttu-id="d1bfd-385">追蹤問題 #11811</span><span class="sxs-lookup"><span data-stu-id="d1bfd-385">Tracking Issue #11811</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

<span data-ttu-id="d1bfd-386">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-386">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="d1bfd-387">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-387">**Old behavior**</span></span>

<span data-ttu-id="d1bfd-388">在 EF Core 3.0 以前，會忽略衍生類型上呼叫的 `ToTable()`，因為唯一的繼承對應策略是對此案例無效的 TPH。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-388">Before EF Core 3.0, `ToTable()` called on a derived type would be ignored since only inheritance mapping strategy was TPH where this isn't valid.</span></span> 

<span data-ttu-id="d1bfd-389">**新行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-389">**New behavior**</span></span>

<span data-ttu-id="d1bfd-390">從 EF Core 3.0 開始，以及在更新版本中準備新增 TPT 和 TPC 支援時，在衍生類型上呼叫的 `ToTable()` 現在會擲回例外狀況，以避免未來發生非預期的對應變更。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-390">Starting with EF Core 3.0 and in preparation for adding TPT and TPC support in a later release, `ToTable()` called on a derived type will now throw an exception to avoid an unexpected mapping change in the future.</span></span>

<span data-ttu-id="d1bfd-391">**原因**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-391">**Why**</span></span>

<span data-ttu-id="d1bfd-392">目前無法將衍生類型對應至不同的資料表。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-392">Currently it isn't valid to map a derived type to a different table.</span></span>
<span data-ttu-id="d1bfd-393">這項變更可避免未來有效執行時的中斷情況。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-393">This change avoids breaking in the future when it becomes a valid thing to do.</span></span>

<span data-ttu-id="d1bfd-394">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-394">**Mitigations**</span></span>

<span data-ttu-id="d1bfd-395">避免嘗試將衍生類型對應至其他資料表。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-395">Remove any attempts to map derived types to other tables.</span></span>

## <a name="forsqlserverhasindex-replaced-with-hasindex"></a><span data-ttu-id="d1bfd-396">ForSqlServerHasIndex 已取代為 HasIndex</span><span class="sxs-lookup"><span data-stu-id="d1bfd-396">ForSqlServerHasIndex replaced with HasIndex</span></span> 

[<span data-ttu-id="d1bfd-397">追蹤問題 #12366</span><span class="sxs-lookup"><span data-stu-id="d1bfd-397">Tracking Issue #12366</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

<span data-ttu-id="d1bfd-398">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-398">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="d1bfd-399">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-399">**Old behavior**</span></span>

<span data-ttu-id="d1bfd-400">在 EF Core 3.0 以前，`ForSqlServerHasIndex().ForSqlServerInclude()` 可讓您設定搭配 `INCLUDE` 使用的資料行。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-400">Before EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` provided a way to configure columns used with `INCLUDE`.</span></span>

<span data-ttu-id="d1bfd-401">**新行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-401">**New behavior**</span></span>

<span data-ttu-id="d1bfd-402">從 EF Core 3.0 開始，關聯式層級現在支援對索引使用 `Include`。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-402">Starting with EF Core 3.0, using `Include` on an index is now supported at the relational level.</span></span>
<span data-ttu-id="d1bfd-403">使用 `HasIndex().ForSqlServerInclude()`。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-403">Use `HasIndex().ForSqlServerInclude()`.</span></span>

<span data-ttu-id="d1bfd-404">**原因**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-404">**Why**</span></span>

<span data-ttu-id="d1bfd-405">這項變更的目的是為了能夠使用 `Includes` 將所有資料庫提供者的索引 API 合併到一個位置。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-405">This change was made to consolidate the API for indexes with `Includes` into one place for all database providers.</span></span>

<span data-ttu-id="d1bfd-406">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-406">**Mitigations**</span></span>

<span data-ttu-id="d1bfd-407">使用新的 API，如上所示。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-407">Use the new API, as shown above.</span></span>

## <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a><span data-ttu-id="d1bfd-408">EF Core 不會再傳送 SQLite FK 強制的 pragma</span><span class="sxs-lookup"><span data-stu-id="d1bfd-408">EF Core no longer sends pragma for SQLite FK enforcement</span></span>

[<span data-ttu-id="d1bfd-409">追蹤問題 #12151</span><span class="sxs-lookup"><span data-stu-id="d1bfd-409">Tracking Issue #12151</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

<span data-ttu-id="d1bfd-410">這項變更已於 EF Core 3.0-preview 3 推出。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-410">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="d1bfd-411">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-411">**Old behavior**</span></span>

<span data-ttu-id="d1bfd-412">在 EF Core 3.0 以前，當開啟 SQLite 連線時，EF Core 會傳送 `PRAGMA foreign_keys = 1`。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-412">Before EF Core 3.0, EF Core would send `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="d1bfd-413">**新行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-413">**New behavior**</span></span>

<span data-ttu-id="d1bfd-414">從 EF Core 3.0 開始，當開啟 SQLite 連線時，EF Core 不會再傳送 `PRAGMA foreign_keys = 1`。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-414">Starting with EF Core 3.0, EF Core no longer sends `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="d1bfd-415">**原因**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-415">**Why**</span></span>

<span data-ttu-id="d1bfd-416">這項變更是因為 EF Core 預設會使用 `SQLitePCLRaw.bundle_e_sqlite3`，這也表示預設會開啟 FK 強制，而不需要在每次開啟連線時明確啟用。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-416">This change was made because EF Core uses `SQLitePCLRaw.bundle_e_sqlite3` by default, which in turn means that FK enforcement is switched on by default and doesn't need to be explicitly enabled each time a connection is opened.</span></span>

<span data-ttu-id="d1bfd-417">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-417">**Mitigations**</span></span>

<span data-ttu-id="d1bfd-418">根據預設，會在預設用於 EF Core 的 SQLitePCLRaw.bundle_e_sqlite3 中啟用外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-418">Foreign keys are enabled by default in SQLitePCLRaw.bundle_e_sqlite3, which is used by default for EF Core.</span></span>
<span data-ttu-id="d1bfd-419">在其他情況下，則可以藉由在您的連接字串中指定 `Foreign Keys=True` 來啟用外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-419">For other cases, foreign keys can be enabled by specifying `Foreign Keys=True` in your connection string.</span></span>

## <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundleesqlite3"></a><span data-ttu-id="d1bfd-420">Microsoft.EntityFrameworkCore.Sqlite 現在相依於 SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="d1bfd-420">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>

<span data-ttu-id="d1bfd-421">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-421">**Old behavior**</span></span>

<span data-ttu-id="d1bfd-422">在 EF Core 3.0 以前，EF Core 會使用 `SQLitePCLRaw.bundle_green`。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-422">Before EF Core 3.0, EF Core used `SQLitePCLRaw.bundle_green`.</span></span>

<span data-ttu-id="d1bfd-423">**新行為**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-423">**New behavior**</span></span>

<span data-ttu-id="d1bfd-424">從 EF Core 3.0 開始，EF Core 會使用 `SQLitePCLRaw.bundle_e_sqlite3`。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-424">Starting with EF Core 3.0, EF Core uses `SQLitePCLRaw.bundle_e_sqlite3`.</span></span>

<span data-ttu-id="d1bfd-425">**原因**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-425">**Why**</span></span>

<span data-ttu-id="d1bfd-426">這項變更的目的是為了讓用於 iOS 的 SQLite 版本與其他平台一致。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-426">This change was made so that the version of SQLite used on iOS consistent with other platforms.</span></span>

<span data-ttu-id="d1bfd-427">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="d1bfd-427">**Mitigations**</span></span>

<span data-ttu-id="d1bfd-428">若要在 iOS 上使用原生 SQLite 版本，請設定 `Microsoft.Data.Sqlite` 使用不同的 `SQLitePCLRaw` 套件組合。</span><span class="sxs-lookup"><span data-stu-id="d1bfd-428">To use the native SQLite version on iOS, configure `Microsoft.Data.Sqlite` to use a different `SQLitePCLRaw` bundle.</span></span>
