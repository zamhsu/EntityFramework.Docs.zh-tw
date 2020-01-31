---
title: EF Core 3.0 的中斷性變更 - EF Core
author: ajcvickers
ms.date: 12/03/2019
uid: core/what-is-new/ef-core-3.0/breaking-changes
ms.openlocfilehash: 6e0c17a22b56b206f18e47f678e3e237d5c42375
ms.sourcegitcommit: b3cf5d2e3cb170b9916795d1d8c88678269639b1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2020
ms.locfileid: "76888105"
---
# <a name="breaking-changes-included-in-ef-core-30"></a><span data-ttu-id="7a4f1-102">EF Core 3.0 中包含的重大變更</span><span class="sxs-lookup"><span data-stu-id="7a4f1-102">Breaking changes included in EF Core 3.0</span></span>

<span data-ttu-id="7a4f1-103">下列 API 和行為變更可能會在將現有的應用程式升級至3.0.0 時中斷。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-103">The following API and behavior changes have the potential to break existing applications when upgrading them to 3.0.0.</span></span>
<span data-ttu-id="7a4f1-104">這些變更預期只會影響[提供者變更](xref:core/providers/provider-log)底下記載的資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-104">Changes that we expect to only impact database providers are documented under [provider changes](xref:core/providers/provider-log).</span></span>

## <a name="summary"></a><span data-ttu-id="7a4f1-105">總結</span><span class="sxs-lookup"><span data-stu-id="7a4f1-105">Summary</span></span>

| <span data-ttu-id="7a4f1-106">**重大變更**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-106">**Breaking change**</span></span>                                                                                               | <span data-ttu-id="7a4f1-107">**影響**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-107">**Impact**</span></span> |
|:------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="7a4f1-108">不會再於用戶端評估 LINQ 查詢</span><span class="sxs-lookup"><span data-stu-id="7a4f1-108">LINQ queries are no longer evaluated on the client</span></span>](#linq-queries-are-no-longer-evaluated-on-the-client)         | <span data-ttu-id="7a4f1-109">High</span><span class="sxs-lookup"><span data-stu-id="7a4f1-109">High</span></span>       |
| [<span data-ttu-id="7a4f1-110">EF Core 3.0 以 .NET Standard 2.1 為目標，而非以 .NET Standard 2.0 為目標</span><span class="sxs-lookup"><span data-stu-id="7a4f1-110">EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0</span></span>](#netstandard21) | <span data-ttu-id="7a4f1-111">High</span><span class="sxs-lookup"><span data-stu-id="7a4f1-111">High</span></span>      |
| [<span data-ttu-id="7a4f1-112">EF Core 命令列工具 dotnet ef 不再是 .NET Core SDK 的一部分</span><span class="sxs-lookup"><span data-stu-id="7a4f1-112">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>](#dotnet-ef) | <span data-ttu-id="7a4f1-113">High</span><span class="sxs-lookup"><span data-stu-id="7a4f1-113">High</span></span>      |
| [<span data-ttu-id="7a4f1-114">DetectChanges 接受存放區產生的索引鍵值</span><span class="sxs-lookup"><span data-stu-id="7a4f1-114">DetectChanges honors store-generated key values</span></span>](#dc) | <span data-ttu-id="7a4f1-115">High</span><span class="sxs-lookup"><span data-stu-id="7a4f1-115">High</span></span>      |
| [<span data-ttu-id="7a4f1-116">FromSql、ExecuteSql 和 ExecuteSqlAsync 已重新命名</span><span class="sxs-lookup"><span data-stu-id="7a4f1-116">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>](#fromsql) | <span data-ttu-id="7a4f1-117">High</span><span class="sxs-lookup"><span data-stu-id="7a4f1-117">High</span></span>      |
| [<span data-ttu-id="7a4f1-118">查詢類型已與實體類型合併</span><span class="sxs-lookup"><span data-stu-id="7a4f1-118">Query types are consolidated with entity types</span></span>](#qt) | <span data-ttu-id="7a4f1-119">High</span><span class="sxs-lookup"><span data-stu-id="7a4f1-119">High</span></span>      |
| [<span data-ttu-id="7a4f1-120">Entity Framework Core 不再屬於 ASP.NET Core 共用架構</span><span class="sxs-lookup"><span data-stu-id="7a4f1-120">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>](#no-longer) | <span data-ttu-id="7a4f1-121">中等</span><span class="sxs-lookup"><span data-stu-id="7a4f1-121">Medium</span></span>      |
| [<span data-ttu-id="7a4f1-122">根據預設，串聯刪除現在會立即發生</span><span class="sxs-lookup"><span data-stu-id="7a4f1-122">Cascade deletions now happen immediately by default</span></span>](#cascade) | <span data-ttu-id="7a4f1-123">中等</span><span class="sxs-lookup"><span data-stu-id="7a4f1-123">Medium</span></span>      |
| [<span data-ttu-id="7a4f1-124">相關實體的積極式載入現在會出現在單一查詢中</span><span class="sxs-lookup"><span data-stu-id="7a4f1-124">Eager loading of related entities now happens in a single query</span></span>](#eager-loading-single-query) | <span data-ttu-id="7a4f1-125">中等</span><span class="sxs-lookup"><span data-stu-id="7a4f1-125">Medium</span></span>      |
| [<span data-ttu-id="7a4f1-126">DeleteBehavior.Restrict 具有更簡潔的語意</span><span class="sxs-lookup"><span data-stu-id="7a4f1-126">DeleteBehavior.Restrict has cleaner semantics</span></span>](#deletebehavior) | <span data-ttu-id="7a4f1-127">中等</span><span class="sxs-lookup"><span data-stu-id="7a4f1-127">Medium</span></span>      |
| [<span data-ttu-id="7a4f1-128">自有類型關聯性的設定 API 已變更</span><span class="sxs-lookup"><span data-stu-id="7a4f1-128">Configuration API for owned type relationships has changed</span></span>](#config) | <span data-ttu-id="7a4f1-129">中等</span><span class="sxs-lookup"><span data-stu-id="7a4f1-129">Medium</span></span>      |
| [<span data-ttu-id="7a4f1-130">各個屬性會使用獨立的記憶體內部整數索引鍵產生</span><span class="sxs-lookup"><span data-stu-id="7a4f1-130">Each property uses independent in-memory integer key generation</span></span>](#each) | <span data-ttu-id="7a4f1-131">中等</span><span class="sxs-lookup"><span data-stu-id="7a4f1-131">Medium</span></span>      |
| [<span data-ttu-id="7a4f1-132">無追蹤查詢已不再執行身分識別解析</span><span class="sxs-lookup"><span data-stu-id="7a4f1-132">No-tracking queries no longer perform identity resolution</span></span>](#notrackingresolution) | <span data-ttu-id="7a4f1-133">中等</span><span class="sxs-lookup"><span data-stu-id="7a4f1-133">Medium</span></span>      |
| [<span data-ttu-id="7a4f1-134">中繼資料 API 變更</span><span class="sxs-lookup"><span data-stu-id="7a4f1-134">Metadata API changes</span></span>](#metadata-api-changes) | <span data-ttu-id="7a4f1-135">中等</span><span class="sxs-lookup"><span data-stu-id="7a4f1-135">Medium</span></span>      |
| [<span data-ttu-id="7a4f1-136">提供者獨有的中繼資料 API 變更</span><span class="sxs-lookup"><span data-stu-id="7a4f1-136">Provider-specific Metadata API changes</span></span>](#provider) | <span data-ttu-id="7a4f1-137">中等</span><span class="sxs-lookup"><span data-stu-id="7a4f1-137">Medium</span></span>      |
| [<span data-ttu-id="7a4f1-138">已移除 UseRowNumberForPaging</span><span class="sxs-lookup"><span data-stu-id="7a4f1-138">UseRowNumberForPaging has been removed</span></span>](#urn) | <span data-ttu-id="7a4f1-139">中等</span><span class="sxs-lookup"><span data-stu-id="7a4f1-139">Medium</span></span>      |
| [<span data-ttu-id="7a4f1-140">無法撰寫與預存程式搭配使用時的 FromSql 方法</span><span class="sxs-lookup"><span data-stu-id="7a4f1-140">FromSql method when used with stored procedure cannot be composed</span></span>](#fromsqlsproc) | <span data-ttu-id="7a4f1-141">中等</span><span class="sxs-lookup"><span data-stu-id="7a4f1-141">Medium</span></span>      |
| [<span data-ttu-id="7a4f1-142">FromSql 方法只能在查詢根目錄上指定</span><span class="sxs-lookup"><span data-stu-id="7a4f1-142">FromSql methods can only be specified on query roots</span></span>](#fromsql) | <span data-ttu-id="7a4f1-143">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-143">Low</span></span>      |
| [<span data-ttu-id="7a4f1-144">~~查詢執行會在偵錯層級記錄~~已還原</span><span class="sxs-lookup"><span data-stu-id="7a4f1-144">~~Query execution is logged at Debug level~~ Reverted</span></span>](#qe) | <span data-ttu-id="7a4f1-145">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-145">Low</span></span>      |
| [<span data-ttu-id="7a4f1-146">實體執行個體上不會再設定暫存索引鍵值</span><span class="sxs-lookup"><span data-stu-id="7a4f1-146">Temporary key values are no longer set onto entity instances</span></span>](#tkv) | <span data-ttu-id="7a4f1-147">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-147">Low</span></span>      |
| [<span data-ttu-id="7a4f1-148">現在可以選用與主體共用資料表的相依實體</span><span class="sxs-lookup"><span data-stu-id="7a4f1-148">Dependent entities sharing the table with the principal are now optional</span></span>](#de) | <span data-ttu-id="7a4f1-149">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-149">Low</span></span>      |
| [<span data-ttu-id="7a4f1-150">所有與並行語彙基元資料行共用資料表的實體，都必須將其對應到屬性</span><span class="sxs-lookup"><span data-stu-id="7a4f1-150">All entities sharing a table with a concurrency token column have to map it to a property</span></span>](#aes) | <span data-ttu-id="7a4f1-151">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-151">Low</span></span>      |
| [<span data-ttu-id="7a4f1-152">在沒有擁有者的情況下，無法使用追蹤查詢來查詢擁有的實體</span><span class="sxs-lookup"><span data-stu-id="7a4f1-152">Owned entities cannot be queried without the owner using a tracking query</span></span>](#owned-query) | <span data-ttu-id="7a4f1-153">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-153">Low</span></span>      |
| [<span data-ttu-id="7a4f1-154">未對應類型的繼承屬性，現在會對應到所有衍生類型的單一資料行</span><span class="sxs-lookup"><span data-stu-id="7a4f1-154">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>](#ip) | <span data-ttu-id="7a4f1-155">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-155">Low</span></span>      |
| [<span data-ttu-id="7a4f1-156">外部索引鍵屬性慣例不會再比對與主體屬性相同的名稱</span><span class="sxs-lookup"><span data-stu-id="7a4f1-156">The foreign key property convention no longer matches same name as the principal property</span></span>](#fkp) | <span data-ttu-id="7a4f1-157">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-157">Low</span></span>      |
| [<span data-ttu-id="7a4f1-158">如果在 TransactionScope 完成之前未再使用，資料庫連線現在會關閉</span><span class="sxs-lookup"><span data-stu-id="7a4f1-158">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>](#dbc) | <span data-ttu-id="7a4f1-159">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-159">Low</span></span>      |
| [<span data-ttu-id="7a4f1-160">根據預設，會使用支援欄位</span><span class="sxs-lookup"><span data-stu-id="7a4f1-160">Backing fields are used by default</span></span>](#backing-fields-are-used-by-default) | <span data-ttu-id="7a4f1-161">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-161">Low</span></span>      |
| [<span data-ttu-id="7a4f1-162">找到多個相容的支援欄位時擲回</span><span class="sxs-lookup"><span data-stu-id="7a4f1-162">Throw if multiple compatible backing fields are found</span></span>](#throw-if-multiple-compatible-backing-fields-are-found) | <span data-ttu-id="7a4f1-163">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-163">Low</span></span>      |
| [<span data-ttu-id="7a4f1-164">僅欄位的屬性名稱應與欄位名稱相符</span><span class="sxs-lookup"><span data-stu-id="7a4f1-164">Field-only property names should match the field name</span></span>](#field-only-property-names-should-match-the-field-name) | <span data-ttu-id="7a4f1-165">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-165">Low</span></span>      |
| [<span data-ttu-id="7a4f1-166">AddDbContext/AddDbContextPool 再也不會呼叫 AddLogging 與 AddMemoryCache</span><span class="sxs-lookup"><span data-stu-id="7a4f1-166">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>](#adddbc) | <span data-ttu-id="7a4f1-167">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-167">Low</span></span>      |
| [<span data-ttu-id="7a4f1-168">AddEntityFramework \* 新增具有大小限制的 IMemoryCache</span><span class="sxs-lookup"><span data-stu-id="7a4f1-168">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>](#addentityframework-adds-imemorycache-with-a-size-limit) | <span data-ttu-id="7a4f1-169">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-169">Low</span></span>      |
| [<span data-ttu-id="7a4f1-170">DbContext.Entry 現在會執行本機 DetectChanges</span><span class="sxs-lookup"><span data-stu-id="7a4f1-170">DbContext.Entry now performs a local DetectChanges</span></span>](#dbe) | <span data-ttu-id="7a4f1-171">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-171">Low</span></span>      |
| [<span data-ttu-id="7a4f1-172">根據預設，字串和位元組陣列索引鍵不會由用戶端產生</span><span class="sxs-lookup"><span data-stu-id="7a4f1-172">String and byte array keys are not client-generated by default</span></span>](#string-and-byte-array-keys-are-not-client-generated-by-default) | <span data-ttu-id="7a4f1-173">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-173">Low</span></span>      |
| [<span data-ttu-id="7a4f1-174">ILoggerFactory 現在是限定範圍的服務</span><span class="sxs-lookup"><span data-stu-id="7a4f1-174">ILoggerFactory is now a scoped service</span></span>](#ilf) | <span data-ttu-id="7a4f1-175">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-175">Low</span></span>      |
| [<span data-ttu-id="7a4f1-176">消極式載入 Proxy 不再假設導覽屬性已完全載入</span><span class="sxs-lookup"><span data-stu-id="7a4f1-176">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>](#lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded) | <span data-ttu-id="7a4f1-177">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-177">Low</span></span>      |
| [<span data-ttu-id="7a4f1-178">現在根據預設，過度建立內部服務提供者會是錯誤</span><span class="sxs-lookup"><span data-stu-id="7a4f1-178">Excessive creation of internal service providers is now an error by default</span></span>](#excessive-creation-of-internal-service-providers-is-now-an-error-by-default) | <span data-ttu-id="7a4f1-179">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-179">Low</span></span>      |
| [<span data-ttu-id="7a4f1-180">使用單一字串呼叫的 HasOne/HasMany 新行為</span><span class="sxs-lookup"><span data-stu-id="7a4f1-180">New behavior for HasOne/HasMany called with a single string</span></span>](#nbh) | <span data-ttu-id="7a4f1-181">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-181">Low</span></span>      |
| [<span data-ttu-id="7a4f1-182">數個非同步方法的傳回型別已從 Task 變更為 ValueTask</span><span class="sxs-lookup"><span data-stu-id="7a4f1-182">The return type for several async methods has been changed from Task to ValueTask</span></span>](#rtnt) | <span data-ttu-id="7a4f1-183">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-183">Low</span></span>      |
| [<span data-ttu-id="7a4f1-184">Relational:TypeMapping 註解現在變成只有 TypeMapping</span><span class="sxs-lookup"><span data-stu-id="7a4f1-184">The Relational:TypeMapping annotation is now just TypeMapping</span></span>](#rtt) | <span data-ttu-id="7a4f1-185">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-185">Low</span></span>      |
| [<span data-ttu-id="7a4f1-186">衍生類型上的 ToTable 會擲回例外狀況</span><span class="sxs-lookup"><span data-stu-id="7a4f1-186">ToTable on a derived type throws an exception</span></span>](#totable-on-a-derived-type-throws-an-exception) | <span data-ttu-id="7a4f1-187">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-187">Low</span></span>      |
| [<span data-ttu-id="7a4f1-188">EF Core 不會再為 SQLite FK 強制傳送 pragma</span><span class="sxs-lookup"><span data-stu-id="7a4f1-188">EF Core no longer sends pragma for SQLite FK enforcement</span></span>](#pragma) | <span data-ttu-id="7a4f1-189">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-189">Low</span></span>      |
| [<span data-ttu-id="7a4f1-190">Microsoft.EntityFrameworkCore.Sqlite 現在相依於 SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="7a4f1-190">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>](#sqlite3) | <span data-ttu-id="7a4f1-191">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-191">Low</span></span>      |
| [<span data-ttu-id="7a4f1-192">GUID 值現在於 SQLite 上的儲存形式為 TEXT</span><span class="sxs-lookup"><span data-stu-id="7a4f1-192">Guid values are now stored as TEXT on SQLite</span></span>](#guid) | <span data-ttu-id="7a4f1-193">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-193">Low</span></span>      |
| [<span data-ttu-id="7a4f1-194">Char 值現在於 SQLite 上的儲存形式為 TEXT</span><span class="sxs-lookup"><span data-stu-id="7a4f1-194">Char values are now stored as TEXT on SQLite</span></span>](#char) | <span data-ttu-id="7a4f1-195">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-195">Low</span></span>      |
| [<span data-ttu-id="7a4f1-196">移轉識別碼現在會使用不因文化特性而異的行事曆來產生</span><span class="sxs-lookup"><span data-stu-id="7a4f1-196">Migration IDs are now generated using the invariant culture's calendar</span></span>](#migid) | <span data-ttu-id="7a4f1-197">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-197">Low</span></span>      |
| [<span data-ttu-id="7a4f1-198">已從 IDbContextOptionsExtension 移除延伸模組資訊/中繼資料</span><span class="sxs-lookup"><span data-stu-id="7a4f1-198">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>](#xinfo) | <span data-ttu-id="7a4f1-199">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-199">Low</span></span>      |
| [<span data-ttu-id="7a4f1-200">已為 LogQueryPossibleExceptionWithAggregateOperator 重新命名</span><span class="sxs-lookup"><span data-stu-id="7a4f1-200">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>](#lqpe) | <span data-ttu-id="7a4f1-201">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-201">Low</span></span>      |
| [<span data-ttu-id="7a4f1-202">讓 API 的外部索引鍵限制式名稱更清楚</span><span class="sxs-lookup"><span data-stu-id="7a4f1-202">Clarify API for foreign key constraint names</span></span>](#clarify) | <span data-ttu-id="7a4f1-203">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-203">Low</span></span>      |
| [<span data-ttu-id="7a4f1-204">IRelationalDatabaseCreator.HasTables/HasTablesAsync 已設為公用</span><span class="sxs-lookup"><span data-stu-id="7a4f1-204">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>](#irdc2) | <span data-ttu-id="7a4f1-205">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-205">Low</span></span>      |
| [<span data-ttu-id="7a4f1-206">Microsoft.EntityFrameworkCore.Design 現在是 DevelopmentDependency 套件</span><span class="sxs-lookup"><span data-stu-id="7a4f1-206">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>](#dip) | <span data-ttu-id="7a4f1-207">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-207">Low</span></span>      |
| [<span data-ttu-id="7a4f1-208">SQLitePCL.raw 已更新為 2.0.0 版</span><span class="sxs-lookup"><span data-stu-id="7a4f1-208">SQLitePCL.raw updated to version 2.0.0</span></span>](#SQLitePCL) | <span data-ttu-id="7a4f1-209">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-209">Low</span></span>      |
| [<span data-ttu-id="7a4f1-210">NetTopologySuite 已更新為 2.0.0 版</span><span class="sxs-lookup"><span data-stu-id="7a4f1-210">NetTopologySuite updated to version 2.0.0</span></span>](#NetTopologySuite) | <span data-ttu-id="7a4f1-211">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-211">Low</span></span>      |
| [<span data-ttu-id="7a4f1-212">SqlClient 是用來取代 SqlClient 的資料。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-212">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>](#SqlClient) | <span data-ttu-id="7a4f1-213">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-213">Low</span></span>      |
| [<span data-ttu-id="7a4f1-214">必須設定多個不明確的自我參考關聯性</span><span class="sxs-lookup"><span data-stu-id="7a4f1-214">Multiple ambiguous self-referencing relationships must be configured</span></span>](#mersa) | <span data-ttu-id="7a4f1-215">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-215">Low</span></span>      |
| [<span data-ttu-id="7a4f1-216">DbFunction。架構為 null 或空字串，將其設定為模型的預設架構</span><span class="sxs-lookup"><span data-stu-id="7a4f1-216">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>](#udf-empty-string) | <span data-ttu-id="7a4f1-217">低</span><span class="sxs-lookup"><span data-stu-id="7a4f1-217">Low</span></span>      |

### <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a><span data-ttu-id="7a4f1-218">不會再於用戶端評估 LINQ 查詢</span><span class="sxs-lookup"><span data-stu-id="7a4f1-218">LINQ queries are no longer evaluated on the client</span></span>

<span data-ttu-id="7a4f1-219">[追蹤問題 #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[另請參閱問題 #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span><span class="sxs-lookup"><span data-stu-id="7a4f1-219">[Tracking Issue #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[Also see issue #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span></span>

<span data-ttu-id="7a4f1-220">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-220">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-221">3\.0 以前，在 EF Core 無法將屬於查詢的運算式轉換成 SQL 或參數時，它會自動在用戶端評估運算式。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-221">Before 3.0, when EF Core couldn't convert an expression that was part of a query to either SQL or a parameter, it automatically evaluated the expression on the client.</span></span>
<span data-ttu-id="7a4f1-222">根據預設，對可能相當耗費資源的運算式進行用戶端評估只會觸發警告。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-222">By default, client evaluation of potentially expensive expressions only triggered a warning.</span></span>

<span data-ttu-id="7a4f1-223">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-223">**New behavior**</span></span>

<span data-ttu-id="7a4f1-224">從 3.0 開始，EF Core 只允許在用戶端評估最上層投影的運算式 (查詢中的最後一個 `Select()` 呼叫)。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-224">Starting with 3.0, EF Core only allows expressions in the top-level projection (the last `Select()` call in the query) to be evaluated on the client.</span></span>
<span data-ttu-id="7a4f1-225">當其他查詢部分中的運算式無法轉換成 SQL 或參數時，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-225">When expressions in any other part of the query can't be converted to either SQL or a parameter, an exception is thrown.</span></span>

<span data-ttu-id="7a4f1-226">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-226">**Why**</span></span>

<span data-ttu-id="7a4f1-227">查詢的自動用戶端評估可執行許多查詢，即使無法轉譯查詢的重要部分也一樣。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-227">Automatic client evaluation of queries allows many queries to be executed even if important parts of them can't be translated.</span></span>
<span data-ttu-id="7a4f1-228">此行為可能會導致非預期且可能造成傷害的行為，而且該行為可能只會出現在生產環境中。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-228">This behavior can result in unexpected and potentially damaging behavior that may only become evident in production.</span></span>
<span data-ttu-id="7a4f1-229">例如，`Where()` 呼叫中無法轉譯的條件可能會導致資料表中的所有資料列從資料庫伺服器移轉，並在用戶端套用篩選。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-229">For example, a condition in a `Where()` call which can't be translated can cause all rows from the table to be transferred from the database server, and the filter to be applied on the client.</span></span>
<span data-ttu-id="7a4f1-230">如果資料表只包含幾個開發中的資料列，此情況可能很容易未被察覺；但當應用程式移至生產環境時，由於資料表可能包含數百萬個資料列，因此影響會很大。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-230">This situation can easily go undetected if the table contains only a few rows in development, but hit hard when the application moves to production, where the table may contain millions of rows.</span></span>
<span data-ttu-id="7a4f1-231">用戶端評估警告也證明很容易在開發期間遭到忽略。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-231">Client evaluation warnings also proved too easy to ignore during development.</span></span>

<span data-ttu-id="7a4f1-232">此外，自動用戶端評估可能會導致改善特定運算式的查詢轉譯造成版本間非預期的中斷性變更問題。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-232">Besides this, automatic client evaluation can lead to issues in which improving query translation for specific expressions caused unintended breaking changes between releases.</span></span>

<span data-ttu-id="7a4f1-233">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-233">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-234">如果無法完整轉譯查詢，請以可轉譯的格式來重寫查詢，或是使用 `AsEnumerable()`、`ToList()` 或類似函數來明確將資料帶回用戶端，以便接著使用 LINQ-to-Objects 加以處理。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-234">If a query can't be fully translated, then either rewrite the query in a form that can be translated, or use `AsEnumerable()`, `ToList()`, or similar to explicitly bring data back to the client where it can then be further processed using LINQ-to-Objects.</span></span>

<a name="netstandard21"></a>
### <a name="ef-core-30-targets-net-standard-21-rather-than-net-standard-20"></a><span data-ttu-id="7a4f1-235">EF Core 3.0 以 .NET Standard 2.1 為目標，而非以 .NET Standard 2.0 為目標</span><span class="sxs-lookup"><span data-stu-id="7a4f1-235">EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0</span></span>

[<span data-ttu-id="7a4f1-236">追蹤問題 #15498</span><span class="sxs-lookup"><span data-stu-id="7a4f1-236">Tracking Issue #15498</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15498)

> [!IMPORTANT] 
> <span data-ttu-id="7a4f1-237">EF Core 3.1 的目標 .NET Standard 2.0。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-237">EF Core 3.1 targets .NET Standard 2.0 again.</span></span> <span data-ttu-id="7a4f1-238">這會傳回 .NET Framework 的支援。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-238">This brings back support for .NET Framework.</span></span>

<span data-ttu-id="7a4f1-239">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-239">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-240">在 3.0 之前，EF Core 以 .NET Standard 2.0 為目標，且執行於支援該標準的所有平台上，包括 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-240">Before 3.0, EF Core targeted .NET Standard 2.0 and would run on all platforms that support that standard, including .NET Framework.</span></span>

<span data-ttu-id="7a4f1-241">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-241">**New behavior**</span></span>

<span data-ttu-id="7a4f1-242">從 3.0 開始，EF Core 以 .NET Standard 2.1 為目標，且執行於支援此標準的所有平台上。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-242">Starting with 3.0, EF Core targets .NET Standard 2.1 and will run on all platforms that support this standard.</span></span> <span data-ttu-id="7a4f1-243">這不包括 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-243">This does not include .NET Framework.</span></span>

<span data-ttu-id="7a4f1-244">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-244">**Why**</span></span>

<span data-ttu-id="7a4f1-245">此為跨 .NET 技術的策略性決策之一部分，著重於 .NET Core 與其他現代化 .NET 平台 (例如 Xamarin) 的能力。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-245">This is part of a strategic decision across .NET technologies to focus energy on .NET Core and other modern .NET platforms, such as Xamarin.</span></span>

<span data-ttu-id="7a4f1-246">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-246">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-247">使用 EF Core 3.1。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-247">Use EF Core 3.1.</span></span>

<a name="no-longer"></a>
### <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a><span data-ttu-id="7a4f1-248">Entity Framework Core 不再屬於 ASP.NET Core 共用架構</span><span class="sxs-lookup"><span data-stu-id="7a4f1-248">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>

[<span data-ttu-id="7a4f1-249">追蹤問題 Announcements#325</span><span class="sxs-lookup"><span data-stu-id="7a4f1-249">Tracking Issue Announcements#325</span></span>](https://github.com/aspnet/Announcements/issues/325)

<span data-ttu-id="7a4f1-250">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-250">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-251">在 ASP.NET Core 3.0 以前，當您新增 `Microsoft.AspNetCore.App` 或 `Microsoft.AspNetCore.All` 的套件參考時，它會包含 EF Core 及部分 EF Core 資料提供者 (例如 SQL Server 提供者)。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-251">Before ASP.NET Core 3.0, when you added a package reference to `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All`, it would include EF Core and some of the EF Core data providers like the SQL Server provider.</span></span>

<span data-ttu-id="7a4f1-252">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-252">**New behavior**</span></span>

<span data-ttu-id="7a4f1-253">從 3.0 開始，ASP.NET Core 共用架構不會包含 EF Core 或任何 EF Core 資料提供者。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-253">Starting in 3.0, the ASP.NET Core shared framework doesn't include EF Core or any EF Core data providers.</span></span>

<span data-ttu-id="7a4f1-254">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-254">**Why**</span></span>

<span data-ttu-id="7a4f1-255">在這項變更之前，取得 EF Core 會根據應用程式是否以 ASP.NET Core 和 SQL Server 為目標而需要不同的步驟。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-255">Before this change, getting EF Core required different steps depending on whether the application targeted ASP.NET Core and SQL Server or not.</span></span> <span data-ttu-id="7a4f1-256">此外，升級 ASP.NET Core 會強制升級 EF Core 和 SQL Server 提供者，這不一定符合需求。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-256">Also, upgrading ASP.NET Core forced the upgrade of EF Core and the SQL Server provider, which isn't always desirable.</span></span>

<span data-ttu-id="7a4f1-257">透過這項變更，取得 EF Core 的體驗對所有提供者、支援的 .NET 實作和應用程式類型都相同。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-257">With this change, the experience of getting EF Core is the same across all providers, supported .NET implementations and application types.</span></span>
<span data-ttu-id="7a4f1-258">開發人員現在也可以精確控制何時升級 EF Core 和 EF Core 資料提供者。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-258">Developers can also now control exactly when EF Core and EF Core data providers are upgraded.</span></span>

<span data-ttu-id="7a4f1-259">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-259">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-260">若要在 ASP.NET Core 3.0 應用程式或其他支援的應用程式中使用 EF Core，請明確將套件參考加入應用程式會使用的 EF Core 資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-260">To use EF Core in an ASP.NET Core 3.0 application or any other supported application, explicitly add a package reference to the EF Core database provider that your application will use.</span></span>

<a name="dotnet-ef"></a>
### <a name="the-ef-core-command-line-tool-dotnet-ef-is-no-longer-part-of-the-net-core-sdk"></a><span data-ttu-id="7a4f1-261">EF Core 命令列工具 dotnet ef 不再是 .NET Core SDK 的一部分</span><span class="sxs-lookup"><span data-stu-id="7a4f1-261">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>

[<span data-ttu-id="7a4f1-262">追蹤問題 #14016</span><span class="sxs-lookup"><span data-stu-id="7a4f1-262">Tracking Issue #14016</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14016)

<span data-ttu-id="7a4f1-263">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-263">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-264">在 3.0 之前，`dotnet ef` 工具包含在 .NET Core SDK，並可以輕易地從任何專案的命令列使用，而不需要額外步驟。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-264">Before 3.0, the `dotnet ef` tool was included in the .NET Core SDK and was readily available to use from the command line from any project without requiring extra steps.</span></span> 

<span data-ttu-id="7a4f1-265">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-265">**New behavior**</span></span>

<span data-ttu-id="7a4f1-266">從 3.0 開始，.NET SDK 不包含 `dotnet ef` 工具，因此您必須明確地將它安裝為本機或全域工具才能使用。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-266">Starting in 3.0, the .NET SDK does not include the `dotnet ef` tool, so before you can use it you have to explicitly install it as a local or global tool.</span></span> 

<span data-ttu-id="7a4f1-267">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-267">**Why**</span></span>

<span data-ttu-id="7a4f1-268">這項變更可讓我們將 `dotnet ef` 當作 NuGet 上一般的 .NET CLI 工具來散發和更新，這點與 EF Core 3.0 一律當作 NuGet 套件散發的事實一致。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-268">This change allows us to distribute and update `dotnet ef` as a regular .NET CLI tool on NuGet, consistent with the fact that the EF Core 3.0 is also always distributed as a NuGet package.</span></span>

<span data-ttu-id="7a4f1-269">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-269">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-270">若要能夠管理移轉或支撐 `DbContext`，請安裝 `dotnet-ef` 作為全域工具：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-270">To be able to manage migrations or scaffold a `DbContext`, install `dotnet-ef` as a global tool:</span></span>

  ``` console
    $ dotnet tool install --global dotnet-ef
  ```

<span data-ttu-id="7a4f1-271">您也可以在還原專案相依性時取得它作為本機工具 (該專案是使用[工具資訊清單檔](https://github.com/dotnet/cli/issues/10288)將它宣告為工具相依性)。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-271">You can also obtain it a local tool when you restore the dependencies of a project that declares it as a tooling dependency using a [tool manifest file](https://github.com/dotnet/cli/issues/10288).</span></span>

<a name="fromsql"></a>
### <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a><span data-ttu-id="7a4f1-272">FromSql、ExecuteSql 和 ExecuteSqlAsync 已重新命名</span><span class="sxs-lookup"><span data-stu-id="7a4f1-272">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>

[<span data-ttu-id="7a4f1-273">追蹤問題 #10996</span><span class="sxs-lookup"><span data-stu-id="7a4f1-273">Tracking Issue #10996</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10996)

<span data-ttu-id="7a4f1-274">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-274">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-275">在 EF Core 3.0 之前，這些方法名稱已多載以使用一般字串，或應插入至 SQL 和參數的字串。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-275">Before EF Core 3.0, these method names were overloaded to work with either a normal string or a string that should be interpolated into SQL and parameters.</span></span>

<span data-ttu-id="7a4f1-276">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-276">**New behavior**</span></span>

<span data-ttu-id="7a4f1-277">從 EF Core 3.0 開始，請使用 `FromSqlRaw`、`ExecuteSqlRaw` 和 `ExecuteSqlRawAsync` 建立參數化查詢，其中參數會分別從查詢字串傳遞。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-277">Starting with EF Core 3.0, use `FromSqlRaw`, `ExecuteSqlRaw`, and `ExecuteSqlRawAsync` to create a parameterized query where the parameters are passed separately from the query string.</span></span>
<span data-ttu-id="7a4f1-278">例如：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-278">For example:</span></span>

```csharp
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

<span data-ttu-id="7a4f1-279">使用 `FromSqlInterpolated`、`ExecuteSqlInterpolated` 和 `ExecuteSqlInterpolatedAsync` 建立參數化查詢，其中參數會作為插入查詢字串的一部分傳回。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-279">Use `FromSqlInterpolated`, `ExecuteSqlInterpolated`, and `ExecuteSqlInterpolatedAsync` to create a parameterized query where the parameters are passed as part of an interpolated query string.</span></span>
<span data-ttu-id="7a4f1-280">例如：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-280">For example:</span></span>

```csharp
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

<span data-ttu-id="7a4f1-281">請注意，上述兩個查詢都會產生具有相同 SQL 參數的相同參數化 SQL。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-281">Note that both of the queries above will produce the same parameterized SQL with the same SQL parameters.</span></span>

<span data-ttu-id="7a4f1-282">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-282">**Why**</span></span>

<span data-ttu-id="7a4f1-283">像這樣的方法多載，使得原本要呼叫插入字串方法很容易意外呼叫原始字串方法，或反之。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-283">Method overloads like this make it very easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span>
<span data-ttu-id="7a4f1-284">這可能會導致查詢在應該參數化時不進行參數化。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-284">This could result in queries not being parameterized when they should have been.</span></span>

<span data-ttu-id="7a4f1-285">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-285">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-286">切換至使用新的方法名稱。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-286">Switch to use the new method names.</span></span>

<a name="fromsqlsproc"></a>
### <a name="fromsql-method-when-used-with-stored-procedure-cannot-be-composed"></a><span data-ttu-id="7a4f1-287">無法撰寫與預存程式搭配使用時的 FromSql 方法</span><span class="sxs-lookup"><span data-stu-id="7a4f1-287">FromSql method when used with stored procedure cannot be composed</span></span>

[<span data-ttu-id="7a4f1-288">追蹤問題 #15392</span><span class="sxs-lookup"><span data-stu-id="7a4f1-288">Tracking Issue #15392</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15392)

<span data-ttu-id="7a4f1-289">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-289">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-290">在 EF Core 3.0 之前，FromSql 方法會嘗試偵測是否可以根據傳遞的 SQL 來進行撰寫。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-290">Before EF Core 3.0, FromSql method tried to detect if the passed SQL can be composed upon.</span></span> <span data-ttu-id="7a4f1-291">當 SQL 不是可組合的，如同預存程式，它會進行用戶端評估。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-291">It did client evaluation when the SQL was non-composable like a stored procedure.</span></span> <span data-ttu-id="7a4f1-292">下列查詢的運作方式是在伺服器上執行預存程式，並在用戶端上進行 FirstOrDefault。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-292">The following query worked by running the stored procedure on the server and doing FirstOrDefault on the client side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").FirstOrDefault();
```

<span data-ttu-id="7a4f1-293">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-293">**New behavior**</span></span>

<span data-ttu-id="7a4f1-294">從 EF Core 3.0 開始，EF Core 不會嘗試剖析 SQL。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-294">Starting with EF Core 3.0, EF Core will not try to parse the SQL.</span></span> <span data-ttu-id="7a4f1-295">因此，如果您在 FromSqlRaw/FromSqlInterpolated 之後撰寫，則 EF Core 會藉由導致子查詢來撰寫 SQL。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-295">So if you are composing after FromSqlRaw/FromSqlInterpolated, then EF Core will compose the SQL by causing sub query.</span></span> <span data-ttu-id="7a4f1-296">因此，如果您使用具有組合的預存程式，則會收到無效 SQL 語法的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-296">So if you are using a stored procedure with composition then you will get an exception for invalid SQL syntax.</span></span>

<span data-ttu-id="7a4f1-297">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-297">**Why**</span></span>

<span data-ttu-id="7a4f1-298">EF Core 3.0 不支援自動用戶端評估，因為它容易發生錯誤，如[這裡](#linq-queries-are-no-longer-evaluated-on-the-client)所述。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-298">EF Core 3.0 does not support automatic client evaluation, since it was error prone as explained [here](#linq-queries-are-no-longer-evaluated-on-the-client).</span></span>

<span data-ttu-id="7a4f1-299">**緩解**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-299">**Mitigation**</span></span>

<span data-ttu-id="7a4f1-300">如果您使用 FromSqlRaw/FromSqlInterpolated 中的預存程式，您就知道它無法由撰寫，因此您可以在 FromSql 方法呼叫之後加入__enumerable.asenumerable/AsAsyncEnumerable__ ，以避免伺服器端上的任何組合。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-300">If you are using a stored procedure in FromSqlRaw/FromSqlInterpolated, you know that it cannot be composed upon, so you can add __AsEnumerable/AsAsyncEnumerable__ right after the FromSql method call to avoid any composition on server side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").AsEnumerable().FirstOrDefault();
```

<a name="fromsql"></a>

### <a name="fromsql-methods-can-only-be-specified-on-query-roots"></a><span data-ttu-id="7a4f1-301">FromSql 方法只能在查詢根目錄上指定</span><span class="sxs-lookup"><span data-stu-id="7a4f1-301">FromSql methods can only be specified on query roots</span></span>

[<span data-ttu-id="7a4f1-302">追蹤問題 #15704</span><span class="sxs-lookup"><span data-stu-id="7a4f1-302">Tracking Issue #15704</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15704)

<span data-ttu-id="7a4f1-303">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-303">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-304">在 EF Core 3.0 之前，可以在查詢中的任何位置指定 `FromSql` 方法。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-304">Before EF Core 3.0, the `FromSql` method could be specified anywhere in the query.</span></span>

<span data-ttu-id="7a4f1-305">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-305">**New behavior**</span></span>

<span data-ttu-id="7a4f1-306">從 EF Core 3.0 開始，新的 `FromSqlRaw` 與 `FromSqlInterpolated` 方法 (取代 `FromSql`) 只能在查詢根目錄上指定，亦即直接在 `DbSet<>` 上指定。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-306">Starting with EF Core 3.0, the new `FromSqlRaw` and `FromSqlInterpolated` methods (which replace `FromSql`) can only be specified on query roots, i.e. directly on the `DbSet<>`.</span></span> <span data-ttu-id="7a4f1-307">嘗試在其他任何位置指定它們，將會導致編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-307">Attempting to specify them anywhere else will result in a compilation error.</span></span>

<span data-ttu-id="7a4f1-308">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-308">**Why**</span></span>

<span data-ttu-id="7a4f1-309">在 `DbSet` 以外的任何地方指定 `FromSql` 沒有新增的意義或附加價值，而且在某些情況下可能會導致模稜兩可。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-309">Specifying `FromSql` anywhere other than on a `DbSet` had no added meaning or added value, and could cause ambiguity in certain scenarios.</span></span>

<span data-ttu-id="7a4f1-310">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-310">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-311">`FromSql` 引動過程應該直接移至它們適用的 `DbSet`。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-311">`FromSql` invocations should be moved to be directly on the `DbSet` to which they apply.</span></span>

<a name="notrackingresolution"></a>
### <a name="no-tracking-queries-no-longer-perform-identity-resolution"></a><span data-ttu-id="7a4f1-312">無追蹤查詢已不再執行身分識別解析</span><span class="sxs-lookup"><span data-stu-id="7a4f1-312">No-tracking queries no longer perform identity resolution</span></span>

[<span data-ttu-id="7a4f1-313">追蹤問題 #13518</span><span class="sxs-lookup"><span data-stu-id="7a4f1-313">Tracking Issue #13518</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13518)

<span data-ttu-id="7a4f1-314">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-314">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-315">在 EF Core 3.0 之前，每次出現具有給定類型與識別碼的實體時，皆會使用相同的實體執行個體。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-315">Before EF Core 3.0, the same entity instance would be used for every occurrence of an entity with a given type and ID.</span></span> <span data-ttu-id="7a4f1-316">如此符合追蹤查詢的行為。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-316">This matches the behavior of tracking queries.</span></span> <span data-ttu-id="7a4f1-317">例如，下列查詢：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-317">For example, this query:</span></span>

```csharp
var results = context.Products.Include(e => e.Category).AsNoTracking().ToList();
```
<span data-ttu-id="7a4f1-318">會為每個與給定類別相關聯的 `Product`，傳回相同的 `Category` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-318">would return the same `Category` instance for each `Product` that is associated with the given category.</span></span>

<span data-ttu-id="7a4f1-319">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-319">**New behavior**</span></span>

<span data-ttu-id="7a4f1-320">從 EF Core 3.0 開始，當具有給定類型與識別碼的實體，出現在傳回圖形的不同位置時，將會建立不同的實體執行個體。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-320">Starting with EF Core 3.0, different entity instances will be created when an entity with a given type and ID is encountered at different places in the returned graph.</span></span> <span data-ttu-id="7a4f1-321">例如，即使當兩個產品與相同的類別相關聯，上述查詢現在會為每個 `Category` 傳回新的 `Product` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-321">For example, the query above will now return a new `Category` instance for each `Product` even when two products are associated with the same category.</span></span>

<span data-ttu-id="7a4f1-322">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-322">**Why**</span></span>

<span data-ttu-id="7a4f1-323">身分識別解析 (也就是，決定實體與之前所發生的實體具有相同的類型與識別碼) 會加入額外的效能與記憶體負荷。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-323">Identity resolution (that is, determining that an entity has the same type and ID as a previously encountered entity) adds additional performance and memory overhead.</span></span> <span data-ttu-id="7a4f1-324">這通常會執行為何一開始就使用無追蹤查詢的計數器。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-324">This usually runs counter to why no-tracking queries are used in the first place.</span></span> <span data-ttu-id="7a4f1-325">此外，雖然身分識別解析有時非常有用，但若實體要序列化並會傳送給用戶端 (對無追蹤查詢而言很常見)，則不需要。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-325">Also, while identity resolution can sometimes be useful, it is not needed if the entities are to be serialized and sent to a client, which is common for no-tracking queries.</span></span>

<span data-ttu-id="7a4f1-326">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-326">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-327">若需要身分識別解析，請使用追蹤查詢。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-327">Use a tracking query if identity resolution is required.</span></span>

<a name="qe"></a>

### <a name="query-execution-is-logged-at-debug-level-reverted"></a><span data-ttu-id="7a4f1-328">~~查詢執行會在偵錯層級記錄~~已還原</span><span class="sxs-lookup"><span data-stu-id="7a4f1-328">~~Query execution is logged at Debug level~~ Reverted</span></span>

[<span data-ttu-id="7a4f1-329">追蹤問題 #14523</span><span class="sxs-lookup"><span data-stu-id="7a4f1-329">Tracking Issue #14523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

<span data-ttu-id="7a4f1-330">我們還原此變更的原因是 EF Core 3.0 中的新設定允許應用程式指定任何事件的記錄層級。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-330">We reverted this change because new configuration in EF Core 3.0 allows the log level for any event to be specified by the application.</span></span> <span data-ttu-id="7a4f1-331">例如，若要將 SQL 的記錄切換到 `Debug`，請明確地在 `OnConfiguring` 或 `AddDbContext` 中設定層級：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-331">For example, to switch logging of SQL to `Debug`, explicitly configure the level in `OnConfiguring` or `AddDbContext`:</span></span>
```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Debug)));
```

<a name="tkv"></a>

### <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a><span data-ttu-id="7a4f1-332">實體執行個體上不會再設定暫存索引鍵值</span><span class="sxs-lookup"><span data-stu-id="7a4f1-332">Temporary key values are no longer set onto entity instances</span></span>

[<span data-ttu-id="7a4f1-333">追蹤問題 #12378</span><span class="sxs-lookup"><span data-stu-id="7a4f1-333">Tracking Issue #12378</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

<span data-ttu-id="7a4f1-334">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-334">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-335">在 EF Core 3.0 以前，會對所有索引鍵屬性指派暫存值，這些屬性稍後會有資料庫產生的實值。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-335">Before EF Core 3.0, temporary values were assigned to all key properties that would later have a real value generated by the database.</span></span>
<span data-ttu-id="7a4f1-336">這些暫存值通常是龐大的負值。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-336">Usually these temporary values were large negative numbers.</span></span>

<span data-ttu-id="7a4f1-337">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-337">**New behavior**</span></span>

<span data-ttu-id="7a4f1-338">從 3.0 開始，EF Core 會將暫存索引鍵值儲存為實體追蹤資訊的一部分，至於索引鍵屬性本身則保持不變。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-338">Starting with 3.0, EF Core stores the temporary key value as part of the entity's tracking information, and leaves the key property itself unchanged.</span></span>

<span data-ttu-id="7a4f1-339">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-339">**Why**</span></span>

<span data-ttu-id="7a4f1-340">這項變更的目的是為了防止在將某個 `DbContext` 執行個體先前追蹤的實體移至不同的 `DbContext` 執行個體時，錯誤地把暫存索引鍵值變成永久值。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-340">This change was made to prevent temporary key values from erroneously becoming permanent when an entity that has been previously tracked by some `DbContext` instance is moved to a different `DbContext` instance.</span></span> 

<span data-ttu-id="7a4f1-341">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-341">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-342">若應用程式會將主索引鍵指派給外部索引鍵以形成實體間關聯，則可能會在主索引鍵是由存放區產生並屬於 `Added` 狀態的實體時採用舊行為。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-342">Applications that assign primary key values onto foreign keys to form associations between entities may depend on the old behavior if the primary keys are store-generated and belong to entities in the `Added` state.</span></span>
<span data-ttu-id="7a4f1-343">這可透過下列方式來避免：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-343">This can be avoided by:</span></span>
* <span data-ttu-id="7a4f1-344">不使用存放區產生的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-344">Not using store-generated keys.</span></span>
* <span data-ttu-id="7a4f1-345">設定導覽屬性以形成關聯性，而不是設定外部索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-345">Setting navigation properties to form relationships instead of setting foreign key values.</span></span>
* <span data-ttu-id="7a4f1-346">從實體的追蹤資訊取得實際暫存索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-346">Obtain the actual temporary key values from the entity's tracking information.</span></span>
<span data-ttu-id="7a4f1-347">例如，`context.Entry(blog).Property(e => e.Id).CurrentValue` 會傳回暫存值，即使尚未設定 `blog.Id` 本身也一樣。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-347">For example, `context.Entry(blog).Property(e => e.Id).CurrentValue` will return the temporary value even though `blog.Id` itself hasn't been set.</span></span>

<a name="dc"></a>

### <a name="detectchanges-honors-store-generated-key-values"></a><span data-ttu-id="7a4f1-348">DetectChanges 接受存放區產生的索引鍵值</span><span class="sxs-lookup"><span data-stu-id="7a4f1-348">DetectChanges honors store-generated key values</span></span>

[<span data-ttu-id="7a4f1-349">追蹤問題 #14616</span><span class="sxs-lookup"><span data-stu-id="7a4f1-349">Tracking Issue #14616</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

<span data-ttu-id="7a4f1-350">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-350">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-351">在 EF Core 3.0 以前，`DetectChanges` 所發現未被追蹤的實體會以 `Added` 狀態追蹤，並在呼叫 `SaveChanges` 時以新的資料列插入。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-351">Before EF Core 3.0, an untracked entity found by `DetectChanges` would be tracked in the `Added` state and inserted as a new row when `SaveChanges` is called.</span></span>

<span data-ttu-id="7a4f1-352">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-352">**New behavior**</span></span>

<span data-ttu-id="7a4f1-353">從 EF Core 3.0 開始，如果實體使用產生的索引鍵值並已設定一些索引鍵值，則實體會以 `Modified` 狀態追蹤。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-353">Starting with EF Core 3.0, if an entity is using generated key values and some key value is set, then the entity will be tracked in the `Modified` state.</span></span>
<span data-ttu-id="7a4f1-354">這表示實體的資料列假設存在，而且會在呼叫 `SaveChanges` 時更新。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-354">This means that a row for the entity is assumed to exist and it will be updated when `SaveChanges` is called.</span></span>
<span data-ttu-id="7a4f1-355">如果未設定索引鍵值，或者如果實體類型未使用產生的索引鍵，則新的實體仍會如同舊版以 `Added` 追蹤。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-355">If the key value isn't set, or if the entity type isn't using generated keys, then the new entity will still be tracked as `Added` as in previous versions.</span></span>

<span data-ttu-id="7a4f1-356">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-356">**Why**</span></span>

<span data-ttu-id="7a4f1-357">這項變更的目的是為了更輕鬆一致地使用中斷連接的實體圖形，同時使用存放區產生的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-357">This change was made to make it easier and more consistent to work with disconnected entity graphs while using store-generated keys.</span></span>

<span data-ttu-id="7a4f1-358">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-358">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-359">如果實體類型已設定為使用產生的索引鍵，但針對新的執行個體明確設定了索引鍵值，這項變更可能會中斷應用程式。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-359">This change can break an application if an entity type is configured to use generated keys but key values are explicitly set for new instances.</span></span>
<span data-ttu-id="7a4f1-360">修正方法是明確設定索引鍵屬性不使用產生的值。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-360">The fix is to explicitly configure the key properties to not use generated values.</span></span>
<span data-ttu-id="7a4f1-361">例如，使用 Fluent API：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-361">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

<span data-ttu-id="7a4f1-362">或者，使用資料註解：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-362">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```
<a name="cascade"></a>
### <a name="cascade-deletions-now-happen-immediately-by-default"></a><span data-ttu-id="7a4f1-363">現在預設會立即發生串聯刪除</span><span class="sxs-lookup"><span data-stu-id="7a4f1-363">Cascade deletions now happen immediately by default</span></span>

[<span data-ttu-id="7a4f1-364">追蹤問題 #10114</span><span class="sxs-lookup"><span data-stu-id="7a4f1-364">Tracking Issue #10114</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

<span data-ttu-id="7a4f1-365">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-365">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-366">在 3.0 以前，除非呼叫 SaveChanges，否則 EF Core 不會套用串聯動作 (刪除必要主體或提供必要主體關聯性時刪除相依實體)。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-366">Before 3.0, EF Core applied cascading actions (deleting dependent entities when a required principal is deleted or when the relationship to a required principal is severed) did not happen until SaveChanges was called.</span></span>

<span data-ttu-id="7a4f1-367">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-367">**New behavior**</span></span>

<span data-ttu-id="7a4f1-368">從 3.0 開始，EF Core 會在偵測到觸發條件時立即套用串聯動作。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-368">Starting with 3.0, EF Core applies cascading actions as soon as the triggering condition is detected.</span></span>
<span data-ttu-id="7a4f1-369">例如，呼叫 `context.Remove()` 刪除主要實體會導致所有追蹤的相關必要相依項目也會立即設定為 `Deleted`。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-369">For example, calling `context.Remove()` to delete a principal entity will result in all tracked related required dependents also being set to `Deleted` immediately.</span></span>

<span data-ttu-id="7a4f1-370">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-370">**Why**</span></span>

<span data-ttu-id="7a4f1-371">這項變更是為了改善資料系結和審核案例的體驗，在此您必須瞭解在呼叫 `SaveChanges`_之前_將刪除哪些實體。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-371">This change was made to improve the experience for data binding and auditing scenarios where it is important to understand which entities will be deleted _before_ `SaveChanges` is called.</span></span>

<span data-ttu-id="7a4f1-372">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-372">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-373">透過設定 `context.ChangeTracker` 可以還原舊行為。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-373">The previous behavior can be restored through settings on `context.ChangeTracker`.</span></span>
<span data-ttu-id="7a4f1-374">例如：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-374">For example:</span></span>

```csharp
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```
<a name="eager-loading-single-query"></a>
### <a name="eager-loading-of-related-entities-now-happens-in-a-single-query"></a><span data-ttu-id="7a4f1-375">相關實體的積極式載入現在會出現在單一查詢中</span><span class="sxs-lookup"><span data-stu-id="7a4f1-375">Eager loading of related entities now happens in a single query</span></span>

[<span data-ttu-id="7a4f1-376">追蹤問題 #18022</span><span class="sxs-lookup"><span data-stu-id="7a4f1-376">Tracking issue #18022</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/18022)

<span data-ttu-id="7a4f1-377">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-377">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-378">在3.0 之前，立即透過 `Include` 運算子載入集合導覽，會導致在關係資料庫上產生多個查詢，每個相關實體類型各一個。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-378">Before 3.0, eagerly loading collection navigations via `Include` operators caused multiple queries to be generated on relational database, one for each related entity type.</span></span>

<span data-ttu-id="7a4f1-379">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-379">**New behavior**</span></span>

<span data-ttu-id="7a4f1-380">從3.0 開始，EF Core 會在關係資料庫上產生具有聯結的單一查詢。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-380">Starting with 3.0, EF Core generates a single query with JOINs on relational databases.</span></span>

<span data-ttu-id="7a4f1-381">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-381">**Why**</span></span>

<span data-ttu-id="7a4f1-382">發出多個查詢來執行單一 LINQ 查詢，會造成許多問題，包括負面效能，因為需要多個資料庫往返，而當每個查詢可能觀察到資料庫的不同狀態時，就會發生資料一致性問題。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-382">Issuing multiple queries to implement a single LINQ query caused numerous issues, including negative performance as multiple database roundtrips were necessary, and data coherency issues as each query could observe a different state of the database.</span></span>

<span data-ttu-id="7a4f1-383">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-383">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-384">雖然技術上來說這不是重大變更，但當單一查詢在集合導覽上包含大量的 `Include` 運算子時，可能會對應用程式效能造成相當大的影響。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-384">While technically this is not a breaking change, it could have a considerable effect on application performance when a single query contains a large number of `Include` operator on collection navigations.</span></span> <span data-ttu-id="7a4f1-385">如需詳細資訊和以更有效率的方式重寫查詢，[請參閱此批註](https://github.com/aspnet/EntityFrameworkCore/issues/18022#issuecomment-542397085)。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-385">[See this comment](https://github.com/aspnet/EntityFrameworkCore/issues/18022#issuecomment-542397085) for more information and for rewriting queries in a more efficient way.</span></span>

**

<a name="deletebehavior"></a>
### <a name="deletebehaviorrestrict-has-cleaner-semantics"></a><span data-ttu-id="7a4f1-386">DeleteBehavior.Restrict 具有更簡潔的語意</span><span class="sxs-lookup"><span data-stu-id="7a4f1-386">DeleteBehavior.Restrict has cleaner semantics</span></span>

[<span data-ttu-id="7a4f1-387">追蹤問題 #12661</span><span class="sxs-lookup"><span data-stu-id="7a4f1-387">Tracking Issue #12661</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12661)

<span data-ttu-id="7a4f1-388">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-388">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-389">在 3.0 以前，`DeleteBehavior.Restrict` 使用 `Restrict` 語意在資料庫中建立外部索引鍵，但也以不明顯的方式變更內部修復。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-389">Before 3.0, `DeleteBehavior.Restrict` created foreign keys in the database with `Restrict` semantics, but also changed internal fixup in a non-obvious way.</span></span>

<span data-ttu-id="7a4f1-390">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-390">**New behavior**</span></span>

<span data-ttu-id="7a4f1-391">從 3.0 開始，`DeleteBehavior.Restrict` 會確保外部索引鍵使用 `Restrict` 語意來建立 (也就是不會有重疊顯示，拋出條件約束違規)，不會影響 EF 內部修復。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-391">Starting with 3.0, `DeleteBehavior.Restrict` ensures that foreign keys are created with `Restrict` semantics--that is, no cascades; throw on constraint violation--without also impacting EF internal fixup.</span></span>

<span data-ttu-id="7a4f1-392">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-392">**Why**</span></span>

<span data-ttu-id="7a4f1-393">這項變更可藉由直覺方式提升使用 `DeleteBehavior` 的體驗，而不會發生非預期的副作用。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-393">This change was made to improve the experience for using `DeleteBehavior` in an intuitive manner, without unexpected side-effects.</span></span>

<span data-ttu-id="7a4f1-394">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-394">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-395">使用 `DeleteBehavior.ClientNoAction` 可以還原舊行為。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-395">The previous behavior can be restored by using `DeleteBehavior.ClientNoAction`.</span></span>

<a name="qt"></a>
### <a name="query-types-are-consolidated-with-entity-types"></a><span data-ttu-id="7a4f1-396">查詢類型會與實體類型合併</span><span class="sxs-lookup"><span data-stu-id="7a4f1-396">Query types are consolidated with entity types</span></span>

[<span data-ttu-id="7a4f1-397">追蹤問題 #14194</span><span class="sxs-lookup"><span data-stu-id="7a4f1-397">Tracking Issue #14194</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

<span data-ttu-id="7a4f1-398">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-398">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-399">在 EF Core 3.0 以前，[查詢類型](xref:core/modeling/keyless-entity-types)可讓您查詢未以結構化方式定義主索引鍵的資料。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-399">Before EF Core 3.0, [query types](xref:core/modeling/keyless-entity-types) were a means to query data that doesn't define a primary key in a structured way.</span></span>
<span data-ttu-id="7a4f1-400">換句話說，查詢類型是用於對應沒有索引鍵的實體類型 (較有可能來自檢視，但也有可能來自資料表)，而一般實體類型是用於索引鍵可供使用時 (較有可能來自資料表，但也有可能來自檢視)。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-400">That is, a query type was used for mapping entity types without keys (more likely from a view, but possibly from a table) while a regular entity type was used when a key was available (more likely from a table, but possibly from a view).</span></span>

<span data-ttu-id="7a4f1-401">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-401">**New behavior**</span></span>

<span data-ttu-id="7a4f1-402">查詢類型現在會成為沒有主索引鍵的實體類型。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-402">A query type now becomes just an entity type without a primary key.</span></span>
<span data-ttu-id="7a4f1-403">無索引鍵的實體類型功能與舊版查詢類型相同。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-403">Keyless entity types have the same functionality as query types in previous versions.</span></span>

<span data-ttu-id="7a4f1-404">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-404">**Why**</span></span>

<span data-ttu-id="7a4f1-405">這項變更的目的是為了降低查詢類型用途的混淆。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-405">This change was made to reduce the confusion around the purpose of query types.</span></span>
<span data-ttu-id="7a4f1-406">具體來說，它們是無索引鍵的實體類型，因此本質上是唯讀的，但不應該只因為實體類型必須是唯讀就使用。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-406">Specifically, they are keyless entity types and they are inherently read-only because of this, but they should not be used just because an entity type needs to be read-only.</span></span>
<span data-ttu-id="7a4f1-407">同樣地，它們通常會對應至檢視，但這只是因為檢視通常未定義索引鍵。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-407">Likewise, they are often mapped to views, but this is only because views often don't define keys.</span></span>

<span data-ttu-id="7a4f1-408">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-408">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-409">API 的下列組件現已淘汰：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-409">The following parts of the API are now obsolete:</span></span>
* <span data-ttu-id="7a4f1-410">**`ModelBuilder.Query<>()`** - 必須改為呼叫 `ModelBuilder.Entity<>().HasNoKey()` 將實體類型標示為沒有索引鍵。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-410">**`ModelBuilder.Query<>()`** - Instead `ModelBuilder.Entity<>().HasNoKey()` needs to be called to mark an entity type as having no keys.</span></span>
<span data-ttu-id="7a4f1-411">為了避免在必須有主索引鍵但不符合慣例時設定錯誤，目前仍未將此設定為慣例。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-411">This would still not be configured by convention to avoid misconfiguration when a primary key is expected, but doesn't match the convention.</span></span>
* <span data-ttu-id="7a4f1-412">**`DbQuery<>`** - 應改用 `DbSet<>`。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-412">**`DbQuery<>`** - Instead `DbSet<>` should be used.</span></span>
* <span data-ttu-id="7a4f1-413">**`DbContext.Query<>()`** - 應改用 `DbContext.Set<>()`。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-413">**`DbContext.Query<>()`** - Instead `DbContext.Set<>()` should be used.</span></span>

<a name="config"></a>
### <a name="configuration-api-for-owned-type-relationships-has-changed"></a><span data-ttu-id="7a4f1-414">自有類型關聯性的設定 API 已變更</span><span class="sxs-lookup"><span data-stu-id="7a4f1-414">Configuration API for owned type relationships has changed</span></span>

<span data-ttu-id="7a4f1-415">[追蹤問題 #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[追蹤問題 #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[追蹤問題 #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span><span class="sxs-lookup"><span data-stu-id="7a4f1-415">[Tracking Issue #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[Tracking Issue #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[Tracking Issue #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span></span>

<span data-ttu-id="7a4f1-416">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-416">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-417">在 EF Core 3.0 以前，會在呼叫 `OwnsOne` 或 `OwnsMany` 之後直接執行自有關聯性的設定。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-417">Before EF Core 3.0, configuration of the owned relationship was performed directly after the `OwnsOne` or `OwnsMany` call.</span></span> 

<span data-ttu-id="7a4f1-418">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-418">**New behavior**</span></span>

<span data-ttu-id="7a4f1-419">從 EF Core 3.0 開始，現在會有 Fluent API 使用 `WithOwner()` 將導覽屬性設定為擁有者。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-419">Starting with EF Core 3.0, there is now fluent API to configure a navigation property to the owner using `WithOwner()`.</span></span>
<span data-ttu-id="7a4f1-420">例如：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-420">For example:</span></span>

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

<span data-ttu-id="7a4f1-421">擁有者與自有之間關聯性的相關設定現在應該在 `WithOwner()` 之後鏈結，類似於其他關聯性的設定方式。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-421">The configuration related to the relationship between owner and owned should now be chained after `WithOwner()` similarly to how other relationships are configured.</span></span>
<span data-ttu-id="7a4f1-422">但自有類型本身的設定仍會在 `OwnsOne()/OwnsMany()` 之後鏈結。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-422">While the configuration for the owned type itself would still be chained after `OwnsOne()/OwnsMany()`.</span></span>
<span data-ttu-id="7a4f1-423">例如：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-423">For example:</span></span>

```csharp
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

<span data-ttu-id="7a4f1-424">此外，使用自有類型目標呼叫 `Entity()`、`HasOne()` 或 `Set()` 現在會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-424">Additionally calling `Entity()`, `HasOne()`, or `Set()` with an owned type target will now throw an exception.</span></span>

<span data-ttu-id="7a4f1-425">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-425">**Why**</span></span>

<span data-ttu-id="7a4f1-426">這項變更的目的是為了更清楚地劃分設定自有類型本身，以及設定自有類型的「關聯性」。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-426">This change was made to create a cleaner separation between configuring the owned type itself and the _relationship to_ the owned type.</span></span>
<span data-ttu-id="7a4f1-427">如此可避免 `HasForeignKey` 等方法的模稜兩可和混淆。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-427">This in turn removes ambiguity and confusion around methods like `HasForeignKey`.</span></span>

<span data-ttu-id="7a4f1-428">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-428">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-429">將自有類型關聯性的設定變更為使用新的 API 介面，如上述範例所示。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-429">Change configuration of owned type relationships to use the new API surface as shown in the example above.</span></span>

<a name="de"></a>

### <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="7a4f1-430">現在可選用以主體來共用資料表的相依實體</span><span class="sxs-lookup"><span data-stu-id="7a4f1-430">Dependent entities sharing the table with the principal are now optional</span></span>

[<span data-ttu-id="7a4f1-431">追蹤問題 #9005</span><span class="sxs-lookup"><span data-stu-id="7a4f1-431">Tracking Issue #9005</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9005)

<span data-ttu-id="7a4f1-432">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-432">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-433">請考慮下列模型：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-433">Consider the following model:</span></span>
```csharp
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
<span data-ttu-id="7a4f1-434">在 EF Core 3.0 之前，如果 `OrderDetails` 由 `Order` 擁有，或明確對應至相同的資料表，則在新增新的 `Order` 時一律需要 `OrderDetails` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-434">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then an `OrderDetails` instance was always required when adding a new `Order`.</span></span>


<span data-ttu-id="7a4f1-435">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-435">**New behavior**</span></span>

<span data-ttu-id="7a4f1-436">從 3.0 開始，EF 允許新增 `Order` 而不需要 `OrderDetails`，並會對應所有 `OrderDetails` 屬性，除了可為 Null 之資料行的主索引鍵以外。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-436">Starting with 3.0, EF Core allows to add an `Order` without an `OrderDetails` and maps all of the `OrderDetails` properties except the primary key to nullable columns.</span></span>
<span data-ttu-id="7a4f1-437">查詢時，如果任何必要的屬性不具有值，或如果其具有主索引鍵以外的不必要屬性，且所有屬性都是 `null`，則 EF Core 會將 `OrderDetails` 設為 `null`。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-437">When querying EF Core sets `OrderDetails` to `null` if any of its required properties doesn't have a value or if it has no required properties besides the primary key and all properties are `null`.</span></span>

<span data-ttu-id="7a4f1-438">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-438">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-439">如果您的模型具有與所有選擇性資料行共用相依資料表，但指向該資料表的導覽不預期為 `null`，則應修改應用程式，以處理當導覽為 `null` 時的情況。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-439">If your model has a table sharing dependent with all optional columns, but the navigation pointing to it is not expected to be `null` then the application should be modified to handle cases when the navigation is `null`.</span></span> <span data-ttu-id="7a4f1-440">如果這不可行，則應將必要屬性新增至實體類型，或至少應有一個屬性指派其非 `null` 的值。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-440">If this is not possible a required property should be added to the entity type or at least one property should have a non-`null` value assigned to it.</span></span>

<a name="aes"></a>

### <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a><span data-ttu-id="7a4f1-441">共用具有並行語彙基元資料行的所有實體，都必須將其對應至屬性</span><span class="sxs-lookup"><span data-stu-id="7a4f1-441">All entities sharing a table with a concurrency token column have to map it to a property</span></span>

[<span data-ttu-id="7a4f1-442">追蹤問題 #14154</span><span class="sxs-lookup"><span data-stu-id="7a4f1-442">Tracking Issue #14154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14154)

<span data-ttu-id="7a4f1-443">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-443">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-444">請考慮下列模型：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-444">Consider the following model:</span></span>
```csharp
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
<span data-ttu-id="7a4f1-445">在 EF Core 3.0之前，如果 `OrderDetails` 由 `Order` 擁有，或明確對應至相同資料表，那麼僅更新 `OrderDetails` 將不會更新用戶端上的 `Version` 值，且下一次更新將會失敗。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-445">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then updating just `OrderDetails` will not update `Version` value on client and the next update will fail.</span></span>


<span data-ttu-id="7a4f1-446">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-446">**New behavior**</span></span>

<span data-ttu-id="7a4f1-447">從 3.0 開始，EF Core 會將新的 `Version` 值傳播至 `Order` (如果其擁有 `OrderDetails`)。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-447">Starting with 3.0, EF Core propagates the new `Version` value to `Order` if it owns `OrderDetails`.</span></span> <span data-ttu-id="7a4f1-448">否則，在模型驗證期間會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-448">Otherwise an exception is thrown during model validation.</span></span>

<span data-ttu-id="7a4f1-449">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-449">**Why**</span></span>

<span data-ttu-id="7a4f1-450">這項變更的目的，是為了避免在僅更新對應至相同資料表的其中一個實體時，出現過時的並行語彙基元值。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-450">This change was made to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

<span data-ttu-id="7a4f1-451">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-451">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-452">共用資料表的所有實體，都必須包含對應至並行語彙基元資料行的屬性。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-452">All entities sharing the table have to include a property that is mapped to the concurrency token column.</span></span> <span data-ttu-id="7a4f1-453">在陰影狀態中建立一個是可能的：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-453">It's possible the create one in shadow-state:</span></span>
```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

<a name="owned-query"></a>

### <a name="owned-entities-cannot-be-queried-without-the-owner-using-a-tracking-query"></a><span data-ttu-id="7a4f1-454">在沒有擁有者的情況下，無法使用追蹤查詢來查詢擁有的實體</span><span class="sxs-lookup"><span data-stu-id="7a4f1-454">Owned entities cannot be queried without the owner using a tracking query</span></span>

[<span data-ttu-id="7a4f1-455">追蹤問題 #18876</span><span class="sxs-lookup"><span data-stu-id="7a4f1-455">Tracking Issue #18876</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/18876)

<span data-ttu-id="7a4f1-456">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-456">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-457">在 EF Core 3.0 之前，您可以將擁有的實體當做任何其他導覽來查詢。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-457">Before EF Core 3.0, the owned entities could be queried as any other navigation.</span></span>

```csharp
context.People.Select(p => p.Address);
```

<span data-ttu-id="7a4f1-458">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-458">**New behavior**</span></span>

<span data-ttu-id="7a4f1-459">從3.0 開始，如果追蹤查詢在沒有擁有者的情況下投射擁有的實體，EF Core 將會擲回。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-459">Starting with 3.0, EF Core will throw if a tracking query projects an owned entity without the owner.</span></span>

<span data-ttu-id="7a4f1-460">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-460">**Why**</span></span>

<span data-ttu-id="7a4f1-461">擁有的實體無法在沒有擁有者的情況下操作，因此在大部分的情況下，以這種方式查詢它們是一項錯誤。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-461">Owned entities cannot be manipulated without the owner, so in the vast majority of cases querying them in this way is an error.</span></span>

<span data-ttu-id="7a4f1-462">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-462">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-463">如果必須追蹤所擁有的實體，以便在稍後進行修改，則擁有者應該包含在查詢中。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-463">If the owned entity should be tracked to be modified in any way later then the owner should be included in the query.</span></span>

<span data-ttu-id="7a4f1-464">否則，請新增 `AsNoTracking()` 呼叫：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-464">Otherwise add an `AsNoTracking()` call:</span></span>

```csharp
context.People.Select(p => p.Address).AsNoTracking();
```

<a name="ip"></a>

### <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a><span data-ttu-id="7a4f1-465">未對應類型的繼承屬性，現在會對應至所有衍生類型的單一資料行</span><span class="sxs-lookup"><span data-stu-id="7a4f1-465">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>

[<span data-ttu-id="7a4f1-466">追蹤問題 #13998</span><span class="sxs-lookup"><span data-stu-id="7a4f1-466">Tracking Issue #13998</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13998)

<span data-ttu-id="7a4f1-467">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-467">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-468">請考慮下列模型：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-468">Consider the following model:</span></span>
```csharp
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

<span data-ttu-id="7a4f1-469">在 EF Core 3.0 之前，`ShippingAddress` 屬性會根據預設，為 `BulkOrder` 和 `Order` 對應至個別資料行。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-469">Before EF Core 3.0, the `ShippingAddress` property would be mapped to separate columns for `BulkOrder` and `Order` by default.</span></span>

<span data-ttu-id="7a4f1-470">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-470">**New behavior**</span></span>

<span data-ttu-id="7a4f1-471">從 3.0 開始，EF Core 只會為 `ShippingAddress` 建立一個資料行。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-471">Starting with 3.0, EF Core only creates one column for `ShippingAddress`.</span></span>

<span data-ttu-id="7a4f1-472">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-472">**Why**</span></span>

<span data-ttu-id="7a4f1-473">舊行為是非預期的。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-473">The old behavoir was unexpected.</span></span>

<span data-ttu-id="7a4f1-474">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-474">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-475">屬性仍可以在衍生類型上明確對應至個別資料行：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-475">The property can still be explicitly mapped to separate column on the derived types:</span></span>

```csharp
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

<a name="fkp"></a>

### <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a><span data-ttu-id="7a4f1-476">外部索引鍵屬性慣例不會再比對與主體屬性相同的名稱</span><span class="sxs-lookup"><span data-stu-id="7a4f1-476">The foreign key property convention no longer matches same name as the principal property</span></span>

[<span data-ttu-id="7a4f1-477">追蹤問題 #13274</span><span class="sxs-lookup"><span data-stu-id="7a4f1-477">Tracking Issue #13274</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

<span data-ttu-id="7a4f1-478">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-478">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-479">請考慮下列模型：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-479">Consider the following model:</span></span>
```csharp
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
<span data-ttu-id="7a4f1-480">在 EF Core 3.0 以前，`CustomerId` 屬性依照慣例會用於外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-480">Before EF Core 3.0, the `CustomerId` property would be used for the foreign key by convention.</span></span>
<span data-ttu-id="7a4f1-481">不過，如果 `Order` 是自有類型，則這也會將 `CustomerId` 設為主索引鍵，而這通常不符合預期。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-481">However, if `Order` is an owned type, then this would also make `CustomerId` the primary key and this isn't usually the expectation.</span></span>

<span data-ttu-id="7a4f1-482">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-482">**New behavior**</span></span>

<span data-ttu-id="7a4f1-483">從 3.0 開始，如果屬性的名稱與主體屬性相同，依照慣例，EF Core 不會嘗試將屬性用於外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-483">Starting with 3.0, EF Core doesn't try to use properties for foreign keys by convention if they have the same name as the principal property.</span></span>
<span data-ttu-id="7a4f1-484">但仍會比對與主體屬性名稱串連的主體類型名稱，以及與主體屬性名稱模式串連的導覽名稱。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-484">Principal type name concatenated with principal property name, and navigation name concatenated with principal property name patterns are still matched.</span></span>
<span data-ttu-id="7a4f1-485">例如：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-485">For example:</span></span>

```csharp
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

```csharp
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

<span data-ttu-id="7a4f1-486">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-486">**Why**</span></span>

<span data-ttu-id="7a4f1-487">這項變更的目的是為了避免錯誤地在自有類型上定義主索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-487">This change was made to avoid erroneously defining a primary key property on the owned type.</span></span>

<span data-ttu-id="7a4f1-488">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-488">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-489">如果屬性預定會作為外部索引鍵，並因此成為主索引鍵的一部分，請明確進行這類設定。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-489">If the property was intended to be the foreign key, and hence part of the primary key, then explicitly configure it as such.</span></span>

<a name="dbc"></a>

### <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a><span data-ttu-id="7a4f1-490">如果在 TransactionScope 完成之前未再使用，則資料庫連線現在已關閉</span><span class="sxs-lookup"><span data-stu-id="7a4f1-490">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>

[<span data-ttu-id="7a4f1-491">追蹤問題 #14218</span><span class="sxs-lookup"><span data-stu-id="7a4f1-491">Tracking Issue #14218</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14218)

<span data-ttu-id="7a4f1-492">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-492">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-493">在 EF Core 3.0 之前，如果內容在 `TransactionScope` 內開啟連線，則當目前 `TransactionScope` 處於作用中時，連線將保持開啟。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-493">Before EF Core 3.0, if the context opens the connection inside a `TransactionScope`, the connection remains open while the current `TransactionScope` is active.</span></span>

```csharp
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

<span data-ttu-id="7a4f1-494">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-494">**New behavior**</span></span>

<span data-ttu-id="7a4f1-495">從 3.0 開始，EF Core 在使用完連線後會將其關閉。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-495">Starting with 3.0, EF Core closes the connection as soon as it's done using it.</span></span>

<span data-ttu-id="7a4f1-496">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-496">**Why**</span></span>

<span data-ttu-id="7a4f1-497">此變更允許在相同 `TransactionScope` 中使用多個內容。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-497">This change allows to use multiple contexts in the same `TransactionScope`.</span></span> <span data-ttu-id="7a4f1-498">新的行為也符合 EF6。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-498">The new behavior also matches EF6.</span></span>

<span data-ttu-id="7a4f1-499">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-499">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-500">如果連線需要保持開啟，則明確呼叫 `OpenConnection()` 可確保 EF Core 不會過早將其關閉：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-500">If the connection needs to remain open explicit call to `OpenConnection()` will ensure that EF Core doesn't close it prematurely:</span></span>

```csharp
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

<a name="each"></a>

### <a name="each-property-uses-independent-in-memory-integer-key-generation"></a><span data-ttu-id="7a4f1-501">每個屬性會使用獨立的記憶體內部整數索引鍵產生</span><span class="sxs-lookup"><span data-stu-id="7a4f1-501">Each property uses independent in-memory integer key generation</span></span>

[<span data-ttu-id="7a4f1-502">追蹤問題 #6872</span><span class="sxs-lookup"><span data-stu-id="7a4f1-502">Tracking Issue #6872</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

<span data-ttu-id="7a4f1-503">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-503">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-504">在 EF Core 3.0 以前，會針對所有記憶體內部整數索引鍵屬性使用一個共用值產生器。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-504">Before EF Core 3.0, one shared value generator was used for all in-memory integer key properties.</span></span>

<span data-ttu-id="7a4f1-505">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-505">**New behavior**</span></span>

<span data-ttu-id="7a4f1-506">從 EF Core 3.0 開始，當使用記憶體內部資料庫時，每個整數索引鍵屬性都會取得自己的值產生器。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-506">Starting with EF Core 3.0, each integer key property gets its own value generator when using the in-memory database.</span></span>
<span data-ttu-id="7a4f1-507">此外，如果已刪除資料庫，則會重設所有資料表的索引鍵產生。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-507">Also, if the database is deleted, then key generation is reset for all tables.</span></span>

<span data-ttu-id="7a4f1-508">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-508">**Why**</span></span>

<span data-ttu-id="7a4f1-509">這項變更的目的是為了讓記憶體內部索引鍵產生與實際資料庫索引鍵產生更加一致，並改善在使用記憶體內部資料庫時隔離個別測試的能力。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-509">This change was made to align in-memory key generation more closely to real database key generation and to improve the ability to isolate tests from each other when using the in-memory database.</span></span>

<span data-ttu-id="7a4f1-510">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-510">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-511">這可能會中斷需要設定特定記憶體內部索引鍵值的應用程式。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-511">This can break an application that is relying on specific in-memory key values to be set.</span></span>
<span data-ttu-id="7a4f1-512">請考慮改為不依賴特定索引鍵值，或更新以符合新行為。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-512">Consider instead not relying on specific key values, or updating to match the new behavior.</span></span>

### <a name="backing-fields-are-used-by-default"></a><span data-ttu-id="7a4f1-513">預設會使用支援欄位</span><span class="sxs-lookup"><span data-stu-id="7a4f1-513">Backing fields are used by default</span></span>

[<span data-ttu-id="7a4f1-514">追蹤問題 #12430</span><span class="sxs-lookup"><span data-stu-id="7a4f1-514">Tracking Issue #12430</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

<span data-ttu-id="7a4f1-515">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-515">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-516">在 3.0 以前，即使屬性的支援欄位已知，EF Core 預設仍會使用屬性 getter 和 setter 方法來讀取和寫入屬性值。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-516">Before 3.0, even if the backing field for a property was known, EF Core would still by default read and write the property value using the property getter and setter methods.</span></span>
<span data-ttu-id="7a4f1-517">例外是查詢執行，其中如果支援欄位已知，則會直接設定。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-517">The exception to this was query execution, where the backing field would be set directly if known.</span></span>

<span data-ttu-id="7a4f1-518">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-518">**New behavior**</span></span>

<span data-ttu-id="7a4f1-519">從 EF Core 3.0 開始，如果屬性的支援欄位已知，則 EF Core 會一律使用支援欄位來讀取和寫入該屬性。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-519">Starting with EF Core 3.0, if the backing field for a property is known, then EF Core will always read and write that property using the backing field.</span></span>
<span data-ttu-id="7a4f1-520">如果應用程式需要將額外的行為編碼到 getter 或 setter 方法中，這可能會導致應用程式中斷。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-520">This could cause an application break if the application is relying on additional behavior coded into the getter or setter methods.</span></span>

<span data-ttu-id="7a4f1-521">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-521">**Why**</span></span>

<span data-ttu-id="7a4f1-522">這項變更的目的是為了防止 EF Core 預設在執行涉及實體的資料庫作業時，錯誤地觸發商務邏輯。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-522">This change was made to prevent EF Core from erroneously triggering business logic by default when performing database operations involving the entities.</span></span>

<span data-ttu-id="7a4f1-523">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-523">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-524">透過在 `ModelBuilder` 上設定屬性存取模式可以還原 3.0 以前的行為。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-524">The pre-3.0 behavior can be restored through configuration of the property access mode on `ModelBuilder`.</span></span>
<span data-ttu-id="7a4f1-525">例如：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-525">For example:</span></span>

```csharp
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

### <a name="throw-if-multiple-compatible-backing-fields-are-found"></a><span data-ttu-id="7a4f1-526">找到多個相容的支援欄位時擲回</span><span class="sxs-lookup"><span data-stu-id="7a4f1-526">Throw if multiple compatible backing fields are found</span></span>

[<span data-ttu-id="7a4f1-527">追蹤問題 #12523</span><span class="sxs-lookup"><span data-stu-id="7a4f1-527">Tracking Issue #12523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

<span data-ttu-id="7a4f1-528">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-528">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-529">在 EF Core 3.0 以前，如果有多個欄位符合尋找屬性支援欄位的規則，則會根據特定優先順序來選擇一個欄位。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-529">Before EF Core 3.0, if multiple fields matched the rules for finding the backing field of a property, then one field would be chosen based on some precedence order.</span></span>
<span data-ttu-id="7a4f1-530">這可能會導致在模稜兩可的情況下使用錯誤的欄位。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-530">This could cause the wrong field to be used in ambiguous cases.</span></span>

<span data-ttu-id="7a4f1-531">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-531">**New behavior**</span></span>

<span data-ttu-id="7a4f1-532">從 EF Core 3.0 開始，如果有多個欄位符合相同的屬性，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-532">Starting with EF Core 3.0, if multiple fields are matched to the same property, then an exception is thrown.</span></span>

<span data-ttu-id="7a4f1-533">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-533">**Why**</span></span>

<span data-ttu-id="7a4f1-534">這項變更的目的是為了避免在只能有一個正確欄位的情況下，自動使用某個欄位而非另一個欄位。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-534">This change was made to avoid silently using one field over another when only one can be correct.</span></span>

<span data-ttu-id="7a4f1-535">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-535">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-536">若屬性的支援欄位模稜兩可，則必須明確指定要使用的欄位。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-536">Properties with ambiguous backing fields must have the field to use specified explicitly.</span></span>
<span data-ttu-id="7a4f1-537">例如，使用 Fluent API：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-537">For example, using the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

### <a name="field-only-property-names-should-match-the-field-name"></a><span data-ttu-id="7a4f1-538">僅限欄位的屬性名稱應與欄位名稱相符</span><span class="sxs-lookup"><span data-stu-id="7a4f1-538">Field-only property names should match the field name</span></span>

<span data-ttu-id="7a4f1-539">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-539">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-540">在 EF Core 3.0 之前，屬性可以由字串值指定，而且如果在 .NET 類型上找不到具有該名稱的屬性，則 EF Core 會嘗試使用慣例規則將它與欄位進行比對。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-540">Before EF Core 3.0, a property could be specified by a string value and if no property with that name was found on the .NET type then EF Core would try to match it to a field using convention rules.</span></span>

```csharp
private class Blog
{
    private int _id;
    public string Name { get; set; }
}
```

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id");
```

<span data-ttu-id="7a4f1-541">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-541">**New behavior**</span></span>

<span data-ttu-id="7a4f1-542">從 EF Core 3.0 開始，僅限欄位的屬性必須與欄位名稱完全相符。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-542">Starting with EF Core 3.0, a field-only property must match the field name exactly.</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("_id");
```

<span data-ttu-id="7a4f1-543">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-543">**Why**</span></span>

<span data-ttu-id="7a4f1-544">此變更是為了避免對兩個名稱相似的屬性使用相同欄位，也使僅限欄位屬性之比對規則與對應至 CLR 屬性的屬性相同。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-544">This change was made to avoid using the same field for two properties named similarly, it also makes the matching rules for field-only properties the same as for properties mapped to CLR properties.</span></span>

<span data-ttu-id="7a4f1-545">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-545">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-546">僅限欄位屬性必須命名為與其所對應欄位相同的名稱。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-546">Field-only properties must be named the same as the field they are mapped to.</span></span>
<span data-ttu-id="7a4f1-547">在3.0 以後的 EF Core 版本中，我們計畫重新啟用明確設定與屬性名稱不同的功能變數名稱（請參閱問題[#15307](https://github.com/aspnet/EntityFrameworkCore/issues/15307)）：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-547">In a future release of EF Core after 3.0, we plan to re-enable explicitly configuring a field name that is different from the property name (see issue [#15307](https://github.com/aspnet/EntityFrameworkCore/issues/15307)):</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id")
    .HasField("_id");
```

<a name="adddbc"></a>

### <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a><span data-ttu-id="7a4f1-548">AddDbContext/AddDbContextPool 再也不會呼叫 AddLogging 與 AddMemoryCache</span><span class="sxs-lookup"><span data-stu-id="7a4f1-548">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>

[<span data-ttu-id="7a4f1-549">追蹤問題 #14756</span><span class="sxs-lookup"><span data-stu-id="7a4f1-549">Tracking Issue #14756</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14756)

<span data-ttu-id="7a4f1-550">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-550">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-551">在 EF Core 3.0 之前，呼叫 `AddDbContext` 或 `AddDbContextPool` 也會透過對[AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging)和[AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache)的呼叫，向 DI 註冊記錄和記憶體快取服務。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-551">Before EF Core 3.0, calling `AddDbContext` or `AddDbContextPool` would also register logging and memory caching services with DI through calls to [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) and [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<span data-ttu-id="7a4f1-552">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-552">**New behavior**</span></span>

<span data-ttu-id="7a4f1-553">從 EF Core 3.0 開始，`AddDbContext` 與 `AddDbContextPool` 再也不會向相依性插入 (DI) 註冊這些服務。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-553">Starting with EF Core 3.0, `AddDbContext` and `AddDbContextPool` will no longer register these services with Dependency Injection (DI).</span></span>

<span data-ttu-id="7a4f1-554">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-554">**Why**</span></span>

<span data-ttu-id="7a4f1-555">EF Core 3.0 不會要求這些服務必須存在於應用程式的 DI 容器中。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-555">EF Core 3.0 does not require that these services are in the application's DI container.</span></span> <span data-ttu-id="7a4f1-556">不過，若 `ILoggerFactory` 已在應用程式的 DI 容器中註冊，則它仍會被 EF Core 使用。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-556">However, if `ILoggerFactory` is registered in the application's DI container, then it will still be used by EF Core.</span></span>

<span data-ttu-id="7a4f1-557">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-557">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-558">若您的應用程式需要這些服務，請使用  [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) 或 [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) 明確地向 DI 容器註冊它們。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-558">If your application needs these services, then register them explicitly with the DI container using  [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) or [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

### <a name="addentityframework-adds-imemorycache-with-a-size-limit"></a><span data-ttu-id="7a4f1-559">AddEntityFramework \* 新增具有大小限制的 IMemoryCache</span><span class="sxs-lookup"><span data-stu-id="7a4f1-559">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>

[<span data-ttu-id="7a4f1-560">追蹤問題 #12905</span><span class="sxs-lookup"><span data-stu-id="7a4f1-560">Tracking Issue #12905</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12905)

<span data-ttu-id="7a4f1-561">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-561">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-562">在 EF Core 3.0 之前，呼叫 `AddEntityFramework*` 方法也會在沒有大小限制的情況下，向 DI 註冊記憶體快取服務。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-562">Before EF Core 3.0, calling `AddEntityFramework*` methods would also register memory caching services with DI without a size limit.</span></span>

<span data-ttu-id="7a4f1-563">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-563">**New behavior**</span></span>

<span data-ttu-id="7a4f1-564">從 EF Core 3.0 開始，`AddEntityFramework*` 會註冊具有大小限制的 IMemoryCache 服務。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-564">Starting with EF Core 3.0, `AddEntityFramework*` will register an IMemoryCache service with a size limit.</span></span> <span data-ttu-id="7a4f1-565">如果之後新增的任何其他服務相依于 IMemoryCache，他們可以快速達到預設限制，導致例外狀況或效能降低。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-565">If any other services added afterwards depend on IMemoryCache they can quickly reach the default limit causing exceptions or degraded performance.</span></span>

<span data-ttu-id="7a4f1-566">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-566">**Why**</span></span>

<span data-ttu-id="7a4f1-567">如果查詢快取邏輯中有錯誤，或動態產生查詢，則使用沒有限制的 IMemoryCache 可能會導致無法控制記憶體使用量。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-567">Using IMemoryCache without a limit could result in uncontrolled memory usage if there is a bug in query caching logic or the queries are generated dynamically.</span></span> <span data-ttu-id="7a4f1-568">具有預設限制可減少潛在的 DoS 攻擊。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-568">Having a default limit mitigates a potential DoS attack.</span></span>

<span data-ttu-id="7a4f1-569">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-569">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-570">在大多數情況下，如果同時呼叫 `AddDbContext` 或 `AddDbContextPool`，則不需要呼叫 `AddEntityFramework*`。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-570">In most cases calling `AddEntityFramework*` is not necessary if `AddDbContext` or `AddDbContextPool` is called as well.</span></span> <span data-ttu-id="7a4f1-571">因此，最好的緩和措施是移除 `AddEntityFramework*` 呼叫。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-571">Therefore, the best mitigation is to remove the `AddEntityFramework*` call.</span></span>

<span data-ttu-id="7a4f1-572">如果您的應用程式需要這些服務，請事先使用[AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache)，以直接向 DI 容器註冊 IMemoryCache 的執行。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-572">If your application needs these services, then register a IMemoryCache implementation explicitly with the DI container beforehand using [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<a name="dbe"></a>

### <a name="dbcontextentry-now-performs-a-local-detectchanges"></a><span data-ttu-id="7a4f1-573">DbContext.Entry 現在會執行本機 DetectChanges</span><span class="sxs-lookup"><span data-stu-id="7a4f1-573">DbContext.Entry now performs a local DetectChanges</span></span>

[<span data-ttu-id="7a4f1-574">追蹤問題 #13552</span><span class="sxs-lookup"><span data-stu-id="7a4f1-574">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="7a4f1-575">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-575">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-576">在 EF Core 3.0 以前，呼叫 `DbContext.Entry` 會導致偵測所有追蹤實體的變更。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-576">Before EF Core 3.0, calling `DbContext.Entry` would cause changes to be detected for all tracked entities.</span></span>
<span data-ttu-id="7a4f1-577">這可確保在 `EntityEntry` 中公開的狀態為最新狀態。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-577">This ensured that the state exposed in the `EntityEntry` was up-to-date.</span></span>

<span data-ttu-id="7a4f1-578">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-578">**New behavior**</span></span>

<span data-ttu-id="7a4f1-579">從 EF Core 3.0 開始，呼叫 `DbContext.Entry` 現在只會嘗試在指定實體及其相關的任何追蹤主要實體中偵測變更。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-579">Starting with EF Core 3.0, calling `DbContext.Entry` will now only attempt to detect changes in the given entity and any tracked principal entities related to it.</span></span>
<span data-ttu-id="7a4f1-580">這表示呼叫此方法可能還無法偵測到其他位置的變更，因此可能會影響應用程式狀態。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-580">This means that changes elsewhere may not have been detected by calling this method, which could have implications on application state.</span></span>

<span data-ttu-id="7a4f1-581">請注意，如果 `ChangeTracker.AutoDetectChangesEnabled` 設定為 `false`，甚至是此本機變更偵測都會停用。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-581">Note that if `ChangeTracker.AutoDetectChangesEnabled` is set to `false` then even this local change detection will be disabled.</span></span>

<span data-ttu-id="7a4f1-582">其他導致變更偵測的方法 (例如 `ChangeTracker.Entries` 和 `SaveChanges`) 仍會對所有追蹤實體進行完整的 `DetectChanges`。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-582">Other methods that cause change detection--for example `ChangeTracker.Entries` and `SaveChanges`--still cause a full `DetectChanges` of all tracked entities.</span></span>

<span data-ttu-id="7a4f1-583">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-583">**Why**</span></span>

<span data-ttu-id="7a4f1-584">這項變更的目的是為了改善使用 `context.Entry` 的預設效能。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-584">This change was made to improve the default performance of using `context.Entry`.</span></span>

<span data-ttu-id="7a4f1-585">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-585">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-586">在呼叫 `Entry` 之前明確呼叫 `ChangeTracker.DetectChanges()` 可確保 3.0 以前的行為。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-586">Call `ChangeTracker.DetectChanges()` explicitly before calling `Entry` to ensure the pre-3.0 behavior.</span></span>

### <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a><span data-ttu-id="7a4f1-587">字串和位元組陣列索引鍵預設不是由用戶端產生</span><span class="sxs-lookup"><span data-stu-id="7a4f1-587">String and byte array keys are not client-generated by default</span></span>

[<span data-ttu-id="7a4f1-588">追蹤問題 #14617</span><span class="sxs-lookup"><span data-stu-id="7a4f1-588">Tracking Issue #14617</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

<span data-ttu-id="7a4f1-589">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-589">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-590">在 EF Core 3.0 以前，可以使用 `string` 和 `byte[]` 索引鍵屬性，而不需要明確設定非 Null 值。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-590">Before EF Core 3.0, `string` and `byte[]` key properties could be used without explicitly setting a non-null value.</span></span>
<span data-ttu-id="7a4f1-591">在此情況下，會在用戶端以 GUID 形式產生索引鍵值，再序列化為 `byte[]` 的位元組。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-591">In such a case, the key value would be generated on the client as a GUID, serialized to bytes for `byte[]`.</span></span>

<span data-ttu-id="7a4f1-592">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-592">**New behavior**</span></span>

<span data-ttu-id="7a4f1-593">從 EF Core 3.0 開始，系統會擲回例外狀況，指出尚未設定任何索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-593">Starting with EF Core 3.0 an exception will be thrown indicating that no key value has been set.</span></span>

<span data-ttu-id="7a4f1-594">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-594">**Why**</span></span>

<span data-ttu-id="7a4f1-595">這項變更是因為用戶端產生的 `string`/`byte[]` 值通常不太有用，而且預設行為使它很難以一般方式來推論產生的索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-595">This change was made because client-generated `string`/`byte[]` values generally aren't useful, and the default behavior made it hard to reason about generated key values in a common way.</span></span>

<span data-ttu-id="7a4f1-596">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-596">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-597">藉由明確指定索引鍵屬性應該在未設定其他非 Null 值時使用產生的值，即可取得 3.0 以前的行為。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-597">The pre-3.0 behavior can be obtained by explicitly specifying that the key properties should use generated values if no other non-null value is set.</span></span>
<span data-ttu-id="7a4f1-598">例如，使用 Fluent API：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-598">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

<span data-ttu-id="7a4f1-599">或者，使用資料註解：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-599">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

<a name="ilf"></a>

### <a name="iloggerfactory-is-now-a-scoped-service"></a><span data-ttu-id="7a4f1-600">ILoggerFactory 現在是限定範圍的服務</span><span class="sxs-lookup"><span data-stu-id="7a4f1-600">ILoggerFactory is now a scoped service</span></span>

[<span data-ttu-id="7a4f1-601">追蹤問題 #14698</span><span class="sxs-lookup"><span data-stu-id="7a4f1-601">Tracking Issue #14698</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

<span data-ttu-id="7a4f1-602">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-602">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-603">在 EF Core 3.0 以前，`ILoggerFactory` 會註冊為單一服務。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-603">Before EF Core 3.0, `ILoggerFactory` was registered as a singleton service.</span></span>

<span data-ttu-id="7a4f1-604">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-604">**New behavior**</span></span>

<span data-ttu-id="7a4f1-605">從 EF Core 3.0 開始，`ILoggerFactory` 現在會註冊為限定範圍。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-605">Starting with EF Core 3.0, `ILoggerFactory` is now registered as scoped.</span></span>

<span data-ttu-id="7a4f1-606">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-606">**Why**</span></span>

<span data-ttu-id="7a4f1-607">這項變更的目的是為了允許記錄器與 `DbContext` 執行個體產生關聯，這可啟用其他功能，並避免某些異常行為案例，例如內部服務提供者遽增。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-607">This change was made to allow association of a logger with a `DbContext` instance, which enables other functionality and removes some cases of pathological behavior such as an explosion of internal service providers.</span></span>

<span data-ttu-id="7a4f1-608">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-608">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-609">這項變更不應影響應用程式程式碼，除非在 EF Core 內部服務提供者上使用自訂服務註冊該程式碼。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-609">This change should not impact application code unless it is registering and using custom services on the EF Core internal service provider.</span></span>
<span data-ttu-id="7a4f1-610">但這並不常見。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-610">This isn't common.</span></span>
<span data-ttu-id="7a4f1-611">在這些情況下，大部分的項目仍會運作，但相依於 `ILoggerFactory` 的任何單一服務需要變更，才能以不同方式取得 `ILoggerFactory`。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-611">In these cases, most things will still work, but any singleton service that was depending on `ILoggerFactory` will need to be changed to obtain the `ILoggerFactory` in a different way.</span></span>

<span data-ttu-id="7a4f1-612">如果您遇到上述情況，請在 [EF Core GitHub 問題追蹤器](https://github.com/aspnet/EntityFrameworkCore/issues)上提出問題，讓我們知道您使用 `ILoggerFactory` 的方式，以便進一步了解未來如何才不會再次中斷。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-612">If you run into situations like this, please file an issue at on the [EF Core GitHub issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues) to let us know how you are using `ILoggerFactory` such that we can better understand how not to break this again in the future.</span></span>

### <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a><span data-ttu-id="7a4f1-613">消極式載入 Proxy 停止假設導覽屬性已完全載入</span><span class="sxs-lookup"><span data-stu-id="7a4f1-613">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>

[<span data-ttu-id="7a4f1-614">追蹤問題 #12780</span><span class="sxs-lookup"><span data-stu-id="7a4f1-614">Tracking Issue #12780</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

<span data-ttu-id="7a4f1-615">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-615">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-616">在 EF Core 3.0 以前，一旦處置 `DbContext` 之後，就無從得知實體上取自該內容的指定導覽屬性是否已完全載入。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-616">Before EF Core 3.0, once a `DbContext` was disposed there was no way of knowing if a given navigation property on an entity obtained from that context was fully loaded or not.</span></span>
<span data-ttu-id="7a4f1-617">Proxy 會改為假設如有非 Null 值，會載入參考導覽；如果不是空的，則會載入集合導覽。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-617">Proxies would instead assume that a reference navigation is loaded if it has a non-null value, and that a collection navigation is loaded if it isn't empty.</span></span>
<span data-ttu-id="7a4f1-618">在這些情況下，嘗試消極式載入不會執行任何作業。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-618">In these cases, attempting to lazy-load would be a no-op.</span></span>

<span data-ttu-id="7a4f1-619">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-619">**New behavior**</span></span>

<span data-ttu-id="7a4f1-620">從 EF Core 3.0 開始，Proxy 會追蹤是否載入導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-620">Starting with EF Core 3.0, proxies keep track of whether or not a navigation property is loaded.</span></span>
<span data-ttu-id="7a4f1-621">這表示嘗試存取在處置內容之後載入的導覽屬性一律不會執行任何作業，即使已載入的導覽是空的或 Null 也一樣。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-621">This means attempting to access a navigation property that is loaded after the context has been disposed will always be a no-op, even when the loaded navigation is empty or null.</span></span>
<span data-ttu-id="7a4f1-622">相反地，如果在處置內容之後嘗試存取未載入的導覽屬性，則會擲回例外狀況，即使導覽屬性不是空集合也一樣。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-622">Conversely, attempting to access a navigation property that isn't loaded will throw an exception if the context is disposed even if the navigation property is a non-empty collection.</span></span>
<span data-ttu-id="7a4f1-623">如果發生這種情況，則表示應用程式程式碼嘗試在無效的時間使用消極式載入，應用程式應該變更為不要這麼做。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-623">If this situation arises, it means the application code is attempting to use lazy-loading at an invalid time, and the application should be changed to not do this.</span></span>

<span data-ttu-id="7a4f1-624">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-624">**Why**</span></span>

<span data-ttu-id="7a4f1-625">這項變更的目的是為了在已處置的 `DbContext` 執行個體上嘗試消極式載入時，使行為一致且正確。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-625">This change was made to make the behavior consistent and correct when attempting to lazy-load on a disposed `DbContext` instance.</span></span>

<span data-ttu-id="7a4f1-626">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-626">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-627">將應用程式程式碼更新為不要嘗試對已處置的內容進行消極式載入，或將此設定為不執行任何作業，如例外狀況訊息中所述。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-627">Update application code to not attempt lazy-loading with a disposed context, or configure this to be a no-op as described in the exception message.</span></span>

### <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a><span data-ttu-id="7a4f1-628">過度建立內部服務提供者現在預設是錯誤</span><span class="sxs-lookup"><span data-stu-id="7a4f1-628">Excessive creation of internal service providers is now an error by default</span></span>

[<span data-ttu-id="7a4f1-629">追蹤問題 #10236</span><span class="sxs-lookup"><span data-stu-id="7a4f1-629">Tracking Issue #10236</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

<span data-ttu-id="7a4f1-630">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-630">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-631">在 EF Core 3.0 以前，當應用程式建立異常數目的內部服務提供者時，會記錄一則警告。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-631">Before EF Core 3.0, a warning would be logged for an application creating a pathological number of internal service providers.</span></span>

<span data-ttu-id="7a4f1-632">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-632">**New behavior**</span></span>

<span data-ttu-id="7a4f1-633">從 EF Core 3.0 開始，此警告現在會視為錯誤，並會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-633">Starting with EF Core 3.0, this warning is now considered and error and an exception is thrown.</span></span> 

<span data-ttu-id="7a4f1-634">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-634">**Why**</span></span>

<span data-ttu-id="7a4f1-635">這項變更的目的是為了透過更明確公開此異常案例，藉以開發更完善的應用程式程式碼。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-635">This change was made to drive better application code through exposing this pathological case more explicitly.</span></span>

<span data-ttu-id="7a4f1-636">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-636">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-637">遇到此錯誤時的最適當動作是了解根本原因，並停止建立這麼多的內部服務提供者。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-637">The most appropriate cause of action on encountering this error is to understand the root cause and stop creating so many internal service providers.</span></span>
<span data-ttu-id="7a4f1-638">不過，透過設定 `DbContextOptionsBuilder` 可以將錯誤轉換回警告。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-638">However, the error can be converted back to a warning (or ignored) via configuration on the `DbContextOptionsBuilder`.</span></span>
<span data-ttu-id="7a4f1-639">例如：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-639">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

<a name="nbh"></a>

### <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a><span data-ttu-id="7a4f1-640">使用單一字串呼叫之 HasOne/HasMany 的新行為</span><span class="sxs-lookup"><span data-stu-id="7a4f1-640">New behavior for HasOne/HasMany called with a single string</span></span>

[<span data-ttu-id="7a4f1-641">追蹤問題 #9171</span><span class="sxs-lookup"><span data-stu-id="7a4f1-641">Tracking Issue #9171</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9171)

<span data-ttu-id="7a4f1-642">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-642">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-643">在 EF Core 3.0 之前，使用單一字串呼叫 `HasOne` 或 `HasMany` 的程式碼會以令人困惑的方式解譯。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-643">Before EF Core 3.0, code calling `HasOne` or `HasMany` with a single string was interpreted in a confusing way.</span></span>
<span data-ttu-id="7a4f1-644">例如：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-644">For example:</span></span>
```csharp
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

<span data-ttu-id="7a4f1-645">程式碼看起來像是它使用 `Entrance` 瀏覽屬性將 `Samurai` 與一些其他實體類型相關，這可能是私用屬性。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-645">The code looks like it is relating `Samurai` to some other entity type using the `Entrance` navigation property, which may be private.</span></span>

<span data-ttu-id="7a4f1-646">在現實中，此程式碼會在不使用瀏覽屬性的情況下嘗試建立與一些實體 (稱為 `Entrance`) 的關係。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-646">In reality, this code attempts to create a relationship to some entity type called `Entrance` with no navigation property.</span></span>

<span data-ttu-id="7a4f1-647">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-647">**New behavior**</span></span>

<span data-ttu-id="7a4f1-648">從 EF Core 3.0 開始，上述程式碼現在會執行像以前一樣的動作。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-648">Starting with EF Core 3.0, the code above now does what it looked like it should have been doing before.</span></span>

<span data-ttu-id="7a4f1-649">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-649">**Why**</span></span>

<span data-ttu-id="7a4f1-650">舊行為令人非常困惑，特別是當讀取設定程式碼與尋找錯誤時。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-650">The old behavior was very confusing, especially when reading the configuration code and looking for errors.</span></span>

<span data-ttu-id="7a4f1-651">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-651">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-652">這只會造成已明確針對類型名稱使用字串設定關係，而未明確指定瀏覽屬性的應用程式中斷。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-652">This will only break applications that are explicitly configuring relationships using strings for type names and without specifying the navigation property explicitly.</span></span>
<span data-ttu-id="7a4f1-653">這不是常見情況。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-653">This is not common.</span></span>
<span data-ttu-id="7a4f1-654">先前的行為可透過明確地傳遞瀏覽屬性名稱的 `null` 來取得。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-654">The previous behavior can be obtained through explicitly passing `null` for the navigation property name.</span></span>
<span data-ttu-id="7a4f1-655">例如：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-655">For example:</span></span>

```csharp
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

<a name="rtnt"></a>

### <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a><span data-ttu-id="7a4f1-656">數個非同步方法的傳回類型已從 Task 變更為 ValueTask</span><span class="sxs-lookup"><span data-stu-id="7a4f1-656">The return type for several async methods has been changed from Task to ValueTask</span></span>

[<span data-ttu-id="7a4f1-657">追蹤問題 #15184</span><span class="sxs-lookup"><span data-stu-id="7a4f1-657">Tracking Issue #15184</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15184)

<span data-ttu-id="7a4f1-658">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-658">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-659">下列非同步方法先前傳回了 `Task<T>`：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-659">The following async methods previously returned a `Task<T>`:</span></span>

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* <span data-ttu-id="7a4f1-660">`ValueGenerator.NextValueAsync()` (和衍生類別)</span><span class="sxs-lookup"><span data-stu-id="7a4f1-660">`ValueGenerator.NextValueAsync()` (and deriving classes)</span></span>

<span data-ttu-id="7a4f1-661">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-661">**New behavior**</span></span>

<span data-ttu-id="7a4f1-662">上述方法現在會透過相同的 `T` 傳回 `ValueTask<T>`，如同以前一樣。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-662">The aforementioned methods now return a `ValueTask<T>` over the same `T` as before.</span></span>

<span data-ttu-id="7a4f1-663">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-663">**Why**</span></span>

<span data-ttu-id="7a4f1-664">這項變更可降低叫用這些方法時產生的堆積配置數目，可改善一般效能。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-664">This change reduces the number of heap allocations incurred when invoking these methods, improving general performance.</span></span>

<span data-ttu-id="7a4f1-665">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-665">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-666">僅等待上述 API 的應用程式只需要重新編譯，而不需要變更來源。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-666">Applications simply awaiting the above APIs only need to be recompiled - no source changes are necessary.</span></span>
<span data-ttu-id="7a4f1-667">更複雜的使用方式 (例如將傳回的 `Task` 傳遞給 `Task.WhenAny()`) 通常需要藉由呼叫 `AsTask()` 將傳回的 `ValueTask<T>` 轉換為 `Task<T>`。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-667">A more complex usage (e.g. passing the returned `Task` to `Task.WhenAny()`) typically require that the returned `ValueTask<T>` be converted to a `Task<T>` by calling `AsTask()` on it.</span></span>
<span data-ttu-id="7a4f1-668">請注意，這會抵消這項變更所帶來的配置減少。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-668">Note that this negates the allocation reduction that this change brings.</span></span>

<a name="rtt"></a>

### <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a><span data-ttu-id="7a4f1-669">Relational:TypeMapping 註解現僅為 TypeMapping</span><span class="sxs-lookup"><span data-stu-id="7a4f1-669">The Relational:TypeMapping annotation is now just TypeMapping</span></span>

[<span data-ttu-id="7a4f1-670">追蹤問題 #9913</span><span class="sxs-lookup"><span data-stu-id="7a4f1-670">Tracking Issue #9913</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

<span data-ttu-id="7a4f1-671">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-671">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-672">類型對應註解的註解名稱之前是 "Relational:TypeMapping"。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-672">The annotation name for type mapping annotations was "Relational:TypeMapping".</span></span>

<span data-ttu-id="7a4f1-673">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-673">**New behavior**</span></span>

<span data-ttu-id="7a4f1-674">類型對應註解的註解名稱現在是 "TypeMapping"。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-674">The annotation name for type mapping annotations is now "TypeMapping".</span></span>

<span data-ttu-id="7a4f1-675">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-675">**Why**</span></span>

<span data-ttu-id="7a4f1-676">類型對應現在不只用於關聯式資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-676">Type mappings are now used for more than just relational database providers.</span></span>

<span data-ttu-id="7a4f1-677">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-677">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-678">這只會中斷直接將類型對應當做註解存取的應用程式，但這並不常見。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-678">This will only break applications that access the type mapping directly as an annotation, which isn't common.</span></span>
<span data-ttu-id="7a4f1-679">最適當的修正動作是使用 API 介面存取類型對應，而不是直接使用註解。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-679">The most appropriate action to fix is to use API surface to access type mappings rather than using the annotation directly.</span></span>

### <a name="totable-on-a-derived-type-throws-an-exception"></a><span data-ttu-id="7a4f1-680">衍生類型上的 ToTable 會擲回例外狀況</span><span class="sxs-lookup"><span data-stu-id="7a4f1-680">ToTable on a derived type throws an exception</span></span> 

[<span data-ttu-id="7a4f1-681">追蹤問題 #11811</span><span class="sxs-lookup"><span data-stu-id="7a4f1-681">Tracking Issue #11811</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

<span data-ttu-id="7a4f1-682">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-682">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-683">在 EF Core 3.0 以前，會忽略衍生類型上呼叫的 `ToTable()`，因為唯一的繼承對應策略是對此案例無效的 TPH。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-683">Before EF Core 3.0, `ToTable()` called on a derived type would be ignored since only inheritance mapping strategy was TPH where this isn't valid.</span></span> 

<span data-ttu-id="7a4f1-684">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-684">**New behavior**</span></span>

<span data-ttu-id="7a4f1-685">從 EF Core 3.0 開始，以及在更新版本中準備新增 TPT 和 TPC 支援時，在衍生類型上呼叫的 `ToTable()` 現在會擲回例外狀況，以避免未來發生非預期的對應變更。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-685">Starting with EF Core 3.0 and in preparation for adding TPT and TPC support in a later release, `ToTable()` called on a derived type will now throw an exception to avoid an unexpected mapping change in the future.</span></span>

<span data-ttu-id="7a4f1-686">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-686">**Why**</span></span>

<span data-ttu-id="7a4f1-687">目前無法將衍生類型對應至不同的資料表。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-687">Currently it isn't valid to map a derived type to a different table.</span></span>
<span data-ttu-id="7a4f1-688">這項變更可避免未來有效執行時的中斷情況。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-688">This change avoids breaking in the future when it becomes a valid thing to do.</span></span>

<span data-ttu-id="7a4f1-689">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-689">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-690">避免嘗試將衍生類型對應至其他資料表。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-690">Remove any attempts to map derived types to other tables.</span></span>

### <a name="forsqlserverhasindex-replaced-with-hasindex"></a><span data-ttu-id="7a4f1-691">ForSqlServerHasIndex 已取代為 HasIndex</span><span class="sxs-lookup"><span data-stu-id="7a4f1-691">ForSqlServerHasIndex replaced with HasIndex</span></span> 

[<span data-ttu-id="7a4f1-692">追蹤問題 #12366</span><span class="sxs-lookup"><span data-stu-id="7a4f1-692">Tracking Issue #12366</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

<span data-ttu-id="7a4f1-693">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-693">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-694">在 EF Core 3.0 以前，`ForSqlServerHasIndex().ForSqlServerInclude()` 可讓您設定搭配 `INCLUDE` 使用的資料行。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-694">Before EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` provided a way to configure columns used with `INCLUDE`.</span></span>

<span data-ttu-id="7a4f1-695">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-695">**New behavior**</span></span>

<span data-ttu-id="7a4f1-696">從 EF Core 3.0 開始，關聯式層級現在支援對索引使用 `Include`。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-696">Starting with EF Core 3.0, using `Include` on an index is now supported at the relational level.</span></span>
<span data-ttu-id="7a4f1-697">使用 `HasIndex().ForSqlServerInclude()`。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-697">Use `HasIndex().ForSqlServerInclude()`.</span></span>

<span data-ttu-id="7a4f1-698">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-698">**Why**</span></span>

<span data-ttu-id="7a4f1-699">這項變更的目的是為了能夠使用 `Include` 將所有資料庫提供者的索引 API 合併到一個位置。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-699">This change was made to consolidate the API for indexes with `Include` into one place for all database providers.</span></span>

<span data-ttu-id="7a4f1-700">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-700">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-701">使用新的 API，如上所示。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-701">Use the new API, as shown above.</span></span>

### <a name="metadata-api-changes"></a><span data-ttu-id="7a4f1-702">中繼資料 API 變更</span><span class="sxs-lookup"><span data-stu-id="7a4f1-702">Metadata API changes</span></span>

[<span data-ttu-id="7a4f1-703">追蹤問題 #214</span><span class="sxs-lookup"><span data-stu-id="7a4f1-703">Tracking Issue #214</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/214)

<span data-ttu-id="7a4f1-704">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-704">**New behavior**</span></span>

<span data-ttu-id="7a4f1-705">下列屬性已轉換為擴充方法：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-705">The following properties were converted to extension methods:</span></span>

* `IEntityType.QueryFilter` -> `GetQueryFilter()`
* `IEntityType.DefiningQuery` -> `GetDefiningQuery()`
* `IProperty.IsShadowProperty` -> `IsShadowProperty()`
* `IProperty.BeforeSaveBehavior` -> `GetBeforeSaveBehavior()`
* `IProperty.AfterSaveBehavior` -> `GetAfterSaveBehavior()`

<span data-ttu-id="7a4f1-706">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-706">**Why**</span></span>

<span data-ttu-id="7a4f1-707">這項變更可簡化上述介面的實作。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-707">This change simplifies the implementation of the aforementioned interfaces.</span></span>

<span data-ttu-id="7a4f1-708">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-708">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-709">使用新的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-709">Use the new extension methods.</span></span>

<a name="provider"></a>

### <a name="provider-specific-metadata-api-changes"></a><span data-ttu-id="7a4f1-710">提供者特定的中繼資料 API 變更</span><span class="sxs-lookup"><span data-stu-id="7a4f1-710">Provider-specific Metadata API changes</span></span>

[<span data-ttu-id="7a4f1-711">追蹤問題 #214</span><span class="sxs-lookup"><span data-stu-id="7a4f1-711">Tracking Issue #214</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/214)

<span data-ttu-id="7a4f1-712">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-712">**New behavior**</span></span>

<span data-ttu-id="7a4f1-713">提供者特定的擴充方法會壓平合併：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-713">The provider-specific extension methods will be flattened out:</span></span>

* `IProperty.Relational().ColumnName` -> `IProperty.GetColumnName()`
* `IEntityType.SqlServer().IsMemoryOptimized` -> `IEntityType.IsMemoryOptimized()`
* `PropertyBuilder.UseSqlServerIdentityColumn()` -> `PropertyBuilder.UseIdentityColumn()`

<span data-ttu-id="7a4f1-714">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-714">**Why**</span></span>

<span data-ttu-id="7a4f1-715">此變更可簡化上述延伸方法的實作。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-715">This change simplifies the implementation of the aforementioned extension methods.</span></span>

<span data-ttu-id="7a4f1-716">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-716">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-717">使用新的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-717">Use the new extension methods.</span></span>

<a name="pragma"></a>

### <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a><span data-ttu-id="7a4f1-718">EF Core 不會再傳送 SQLite FK 強制的 pragma</span><span class="sxs-lookup"><span data-stu-id="7a4f1-718">EF Core no longer sends pragma for SQLite FK enforcement</span></span>

[<span data-ttu-id="7a4f1-719">追蹤問題 #12151</span><span class="sxs-lookup"><span data-stu-id="7a4f1-719">Tracking Issue #12151</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

<span data-ttu-id="7a4f1-720">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-720">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-721">在 EF Core 3.0 以前，當開啟 SQLite 連線時，EF Core 會傳送 `PRAGMA foreign_keys = 1`。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-721">Before EF Core 3.0, EF Core would send `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="7a4f1-722">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-722">**New behavior**</span></span>

<span data-ttu-id="7a4f1-723">從 EF Core 3.0 開始，當開啟 SQLite 連線時，EF Core 不會再傳送 `PRAGMA foreign_keys = 1`。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-723">Starting with EF Core 3.0, EF Core no longer sends `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="7a4f1-724">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-724">**Why**</span></span>

<span data-ttu-id="7a4f1-725">這項變更是因為 EF Core 預設會使用 `SQLitePCLRaw.bundle_e_sqlite3`，這也表示預設會開啟 FK 強制，而不需要在每次開啟連線時明確啟用。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-725">This change was made because EF Core uses `SQLitePCLRaw.bundle_e_sqlite3` by default, which in turn means that FK enforcement is switched on by default and doesn't need to be explicitly enabled each time a connection is opened.</span></span>

<span data-ttu-id="7a4f1-726">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-726">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-727">根據預設，會在預設用於 EF Core 的 SQLitePCLRaw.bundle_e_sqlite3 中啟用外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-727">Foreign keys are enabled by default in SQLitePCLRaw.bundle_e_sqlite3, which is used by default for EF Core.</span></span>
<span data-ttu-id="7a4f1-728">在其他情況下，則可以藉由在您的連接字串中指定 `Foreign Keys=True` 來啟用外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-728">For other cases, foreign keys can be enabled by specifying `Foreign Keys=True` in your connection string.</span></span>

<a name="sqlite3"></a>

### <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundle_e_sqlite3"></a><span data-ttu-id="7a4f1-729">Microsoft.EntityFrameworkCore.Sqlite 現在相依於 SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="7a4f1-729">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>

<span data-ttu-id="7a4f1-730">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-730">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-731">在 EF Core 3.0 以前，EF Core 會使用 `SQLitePCLRaw.bundle_green`。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-731">Before EF Core 3.0, EF Core used `SQLitePCLRaw.bundle_green`.</span></span>

<span data-ttu-id="7a4f1-732">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-732">**New behavior**</span></span>

<span data-ttu-id="7a4f1-733">從 EF Core 3.0 開始，EF Core 會使用 `SQLitePCLRaw.bundle_e_sqlite3`。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-733">Starting with EF Core 3.0, EF Core uses `SQLitePCLRaw.bundle_e_sqlite3`.</span></span>

<span data-ttu-id="7a4f1-734">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-734">**Why**</span></span>

<span data-ttu-id="7a4f1-735">這項變更的目的是為了讓用於 iOS 的 SQLite 版本與其他平台一致。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-735">This change was made so that the version of SQLite used on iOS consistent with other platforms.</span></span>

<span data-ttu-id="7a4f1-736">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-736">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-737">若要在 iOS 上使用原生 SQLite 版本，請設定 `Microsoft.Data.Sqlite` 使用不同的 `SQLitePCLRaw` 套件組合。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-737">To use the native SQLite version on iOS, configure `Microsoft.Data.Sqlite` to use a different `SQLitePCLRaw` bundle.</span></span>

<a name="guid"></a>

### <a name="guid-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="7a4f1-738">GUID 值現在於 SQLite 上的儲存形式為 TEXT</span><span class="sxs-lookup"><span data-stu-id="7a4f1-738">Guid values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="7a4f1-739">追蹤問題 #15078</span><span class="sxs-lookup"><span data-stu-id="7a4f1-739">Tracking Issue #15078</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15078)

<span data-ttu-id="7a4f1-740">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-740">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-741">GUID 值先前在 SQLite 上的儲存形式為 BLOB 值。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-741">Guid values were previously stored as BLOB values on SQLite.</span></span>

<span data-ttu-id="7a4f1-742">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-742">**New behavior**</span></span>

<span data-ttu-id="7a4f1-743">GUID 值現在會儲存為 TEXT。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-743">Guid values are now stored as TEXT.</span></span>

<span data-ttu-id="7a4f1-744">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-744">**Why**</span></span>

<span data-ttu-id="7a4f1-745">GUID 的二進位格式未標準化。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-745">The binary format of Guids is not standardized.</span></span> <span data-ttu-id="7a4f1-746">以 TEXT 的形式儲存值會提高資料庫與其他技術的相容性。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-746">Storing the values as TEXT makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="7a4f1-747">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-747">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-748">您可以參考以下方式執行 SQL，來將現有的資料庫移轉至新的格式。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-748">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

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

<span data-ttu-id="7a4f1-749">在 EF Core 中，您也可以在這些屬性上設定值轉換器來繼續使用原本的行為。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-749">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

<span data-ttu-id="7a4f1-750">Microsoft.Data.Sqlite 依然可以同時從 BLOB 及 TEXT 資料行讀取 GUID 值；但因為參數和常數的預設格式已變更，所以您可能需要對多數涉及 GUID 的案例採取動作。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-750">Microsoft.Data.Sqlite remains capable of reading Guid values from both BLOB and TEXT columns; however, since the default format for parameters and constants has changed you'll likely need to take action for most scenarios involving Guids.</span></span>

<a name="char"></a>

### <a name="char-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="7a4f1-751">Char 值現在於 SQLite 上會儲存為文字</span><span class="sxs-lookup"><span data-stu-id="7a4f1-751">Char values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="7a4f1-752">追蹤問題 #15020</span><span class="sxs-lookup"><span data-stu-id="7a4f1-752">Tracking Issue #15020</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15020)

<span data-ttu-id="7a4f1-753">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-753">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-754">Char 值原先在 SQLite 上儲存為整數值。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-754">Char values were previously sored as INTEGER values on SQLite.</span></span> <span data-ttu-id="7a4f1-755">舉例來說，char 值 *A* 原先會儲存為整數值 65。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-755">For example, a char value of *A* was stored as the integer value 65.</span></span>

<span data-ttu-id="7a4f1-756">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-756">**New behavior**</span></span>

<span data-ttu-id="7a4f1-757">Char 值現在會儲存為 TEXT。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-757">Char values are now stored as TEXT.</span></span>

<span data-ttu-id="7a4f1-758">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-758">**Why**</span></span>

<span data-ttu-id="7a4f1-759">將值儲存為 TEXT 不但更加自然，也使資料庫與其他技術的相容性更高。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-759">Storing the values as TEXT is more natural and makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="7a4f1-760">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-760">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-761">您可以參考以下方式執行 SQL，來將現有的資料庫移轉至新的格式。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-761">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

``` sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

<span data-ttu-id="7a4f1-762">在 EF Core 中，您也可以在這些屬性上設定值轉換器來繼續使用原本的行為。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-762">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

<span data-ttu-id="7a4f1-763">Microsoft.Data.Sqlite 也保留了讀取 INTEGER 和 TEXT 欄位字元值的功能，所以部分案例可能不需要任何動作。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-763">Microsoft.Data.Sqlite also remains capable of reading character values from both INTEGER and TEXT columns, so certain scenarios may not require any action.</span></span>

<a name="migid"></a>

### <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a><span data-ttu-id="7a4f1-764">移轉識別碼現在會使用不因文化特性而異的行事曆產生</span><span class="sxs-lookup"><span data-stu-id="7a4f1-764">Migration IDs are now generated using the invariant culture's calendar</span></span>

[<span data-ttu-id="7a4f1-765">追蹤問題 #12978</span><span class="sxs-lookup"><span data-stu-id="7a4f1-765">Tracking Issue #12978</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12978)

<span data-ttu-id="7a4f1-766">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-766">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-767">移轉識別碼原先會使用目前文化特性 (Culture) 的行事曆產生。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-767">Migration IDs were inadvertently generated using the current culture's calendar.</span></span>

<span data-ttu-id="7a4f1-768">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-768">**New behavior**</span></span>

<span data-ttu-id="7a4f1-769">移轉識別碼現在一律會使用不因文化特性而異的行事曆 (西曆) 產生。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-769">Migration IDs are now always generated using the invariant culture's calendar (Gregorian).</span></span>

<span data-ttu-id="7a4f1-770">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-770">**Why**</span></span>

<span data-ttu-id="7a4f1-771">更新資料庫或解決合併衝突時，移轉的順序相當重要。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-771">The order of migrations is important when updating the database or resolving merge conflicts.</span></span> <span data-ttu-id="7a4f1-772">使用無差異的行事曆可避免順序問題，使小組成員系統行事曆不同的問題不會發生。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-772">Using the invariant calendar avoids ordering issues that can result from team members having different system calendars.</span></span>

<span data-ttu-id="7a4f1-773">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-773">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-774">此變更會影響年份大於西曆行事曆的非西曆行事曆使用者 (例如泰國佛曆)。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-774">This change affects anyone using a non-Gregorian calendar where the year is greater than the Gregorian calendar (like the Thai Buddhist calendar).</span></span> <span data-ttu-id="7a4f1-775">現有的移轉識別碼必須更新，以使新的移轉會在現有的移轉之後排序。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-775">Existing migration IDs will need to be updated so that new migrations are ordered after existing migrations.</span></span>

<span data-ttu-id="7a4f1-776">您可在移轉設計工具檔案的移轉屬性中找到移轉識別碼。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-776">The migration ID can be found in the Migration attribute in the migrations' designer files.</span></span>

``` diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

<span data-ttu-id="7a4f1-777">移轉歷程記錄資料表也必須更新。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-777">The Migrations history table also needs to be updated.</span></span>

``` sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

<a name="urn"></a>

### <a name="userownumberforpaging-has-been-removed"></a><span data-ttu-id="7a4f1-778">已移除 UseRowNumberForPaging</span><span class="sxs-lookup"><span data-stu-id="7a4f1-778">UseRowNumberForPaging has been removed</span></span>

[<span data-ttu-id="7a4f1-779">追蹤問題 #16400</span><span class="sxs-lookup"><span data-stu-id="7a4f1-779">Tracking Issue #16400</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/16400)

<span data-ttu-id="7a4f1-780">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-780">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-781">在 EF Core 3.0 之前，`UseRowNumberForPaging` 可用來問與 SQL Server 2008 相容的分頁產生 SQL。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-781">Before EF Core 3.0, `UseRowNumberForPaging` could be used to generate SQL for paging that is compatible with SQL Server 2008.</span></span>

<span data-ttu-id="7a4f1-782">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-782">**New behavior**</span></span>

<span data-ttu-id="7a4f1-783">從 EF Core 3.0 開始，EF 將只會針對與新版 SQL Server 相容的分頁產生 SQL。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-783">Starting with EF Core 3.0, EF will only generate SQL for paging that is only compatible with later SQL Server versions.</span></span> 

<span data-ttu-id="7a4f1-784">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-784">**Why**</span></span>

<span data-ttu-id="7a4f1-785">我們正在進行此變更，因為 [SQL Server 2008 不再是支援的產品](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) \(英文\) 且更新此功能以搭配 EF Core 3.0 中的查詢變更使用是一個大工程。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-785">We are making this change because [SQL Server 2008 is no longer a supported product](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) and updating this feature to work with the query changes made in EF Core 3.0 is significant work.</span></span>

<span data-ttu-id="7a4f1-786">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-786">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-787">我們建議更新為新版 SQL Server，或使用較高的相容性層級，以支援產生的 SQL。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-787">We recommend updating to a newer version of SQL Server, or using a higher compatibility level, so that the generated SQL is supported.</span></span> <span data-ttu-id="7a4f1-788">儘管如此，若您無法這樣做，請[在追蹤問題下註解](https://github.com/aspnet/EntityFrameworkCore/issues/16400)並提供詳細資料。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-788">That being said, if you are unable to do this, then please [comment on the tracking issue](https://github.com/aspnet/EntityFrameworkCore/issues/16400) with details.</span></span> <span data-ttu-id="7a4f1-789">我們可能會根據意見反應重新審視此決定。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-789">We may revisit this decision based on feedback.</span></span>

<a name="xinfo"></a>

### <a name="extension-infometadata-has-been-removed-from-idbcontextoptionsextension"></a><span data-ttu-id="7a4f1-790">已從 IDbContextOptionsExtension 移除延伸模組資訊/中繼資料</span><span class="sxs-lookup"><span data-stu-id="7a4f1-790">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>

[<span data-ttu-id="7a4f1-791">追蹤問題 #16119</span><span class="sxs-lookup"><span data-stu-id="7a4f1-791">Tracking Issue #16119</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/16119)

<span data-ttu-id="7a4f1-792">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-792">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-793">用於提供有關延伸模組織中繼資料的 `IDbContextOptionsExtension` 包含方法。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-793">`IDbContextOptionsExtension` contained methods for providing metadata about the extension.</span></span>

<span data-ttu-id="7a4f1-794">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-794">**New behavior**</span></span>

<span data-ttu-id="7a4f1-795">這些方法已移動到新的 `DbContextOptionsExtensionInfo` 抽象基底類別，這是從新的 `IDbContextOptionsExtension.Info` 屬性傳回的。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-795">These methods have been moved onto a new `DbContextOptionsExtensionInfo` abstract base class, which is returned from a new `IDbContextOptionsExtension.Info` property.</span></span>

<span data-ttu-id="7a4f1-796">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-796">**Why**</span></span>

<span data-ttu-id="7a4f1-797">在從 2.0 升級到 3.0 的程序中，我們必須新增或變更這些方法數次。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-797">Over the releases from 2.0 to 3.0 we needed to add to or change these methods several times.</span></span>
<span data-ttu-id="7a4f1-798">將它們分成新的抽象基底類別可讓我們更輕鬆地在不變更現有延伸模組的情況下進行此類變更。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-798">Breaking them out into a new abstract base class will make it easier to make these kind of changes without breaking existing extensions.</span></span>

<span data-ttu-id="7a4f1-799">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-799">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-800">更新延伸模組以遵循新模式。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-800">Update extensions to follow the new pattern.</span></span>
<span data-ttu-id="7a4f1-801">您可以在 EF Core 原始程式碼中各種不同延伸模組之 `IDbContextOptionsExtension` 的許多實作中找到範例。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-801">Examples are found in the many implementations of `IDbContextOptionsExtension` for different kinds of extensions in the EF Core source code.</span></span>

<a name="lqpe"></a>

### <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a><span data-ttu-id="7a4f1-802">已重新命名 LogQueryPossibleExceptionWithAggregateOperator</span><span class="sxs-lookup"><span data-stu-id="7a4f1-802">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>

[<span data-ttu-id="7a4f1-803">追蹤問題 #10985</span><span class="sxs-lookup"><span data-stu-id="7a4f1-803">Tracking Issue #10985</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10985)

<span data-ttu-id="7a4f1-804">**變更**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-804">**Change**</span></span>

<span data-ttu-id="7a4f1-805">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` 已經重新命名為 `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-805">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` has been renamed to `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span></span>

<span data-ttu-id="7a4f1-806">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-806">**Why**</span></span>

<span data-ttu-id="7a4f1-807">使這個警告事件的命名與其他所有警告事件一致。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-807">Aligns the naming of this warning event with all other warning events.</span></span>

<span data-ttu-id="7a4f1-808">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-808">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-809">使用新的名稱。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-809">Use the new name.</span></span> <span data-ttu-id="7a4f1-810">(注意，事件識別碼未變更。)</span><span class="sxs-lookup"><span data-stu-id="7a4f1-810">(Note that the event ID number has not changed.)</span></span>

<a name="clarify"></a>

### <a name="clarify-api-for-foreign-key-constraint-names"></a><span data-ttu-id="7a4f1-811">讓 API 的外部索引鍵限制式名稱更清楚</span><span class="sxs-lookup"><span data-stu-id="7a4f1-811">Clarify API for foreign key constraint names</span></span>

[<span data-ttu-id="7a4f1-812">追蹤問題 #10730</span><span class="sxs-lookup"><span data-stu-id="7a4f1-812">Tracking Issue #10730</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10730)

<span data-ttu-id="7a4f1-813">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-813">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-814">在 EF Core 3.0 前，外部索引鍵限制式名稱僅為 "name"。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-814">Before EF Core 3.0, foreign key constraint names were referred to as simply the "name".</span></span> <span data-ttu-id="7a4f1-815">例如：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-815">For example:</span></span>

```csharp
var constraintName = myForeignKey.Name;
```

<span data-ttu-id="7a4f1-816">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-816">**New behavior**</span></span>

<span data-ttu-id="7a4f1-817">從 EF Core 3.0 開始，外部索引鍵限制式名稱現為 "constraint name"。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-817">Starting with EF Core 3.0, foreign key constraint names are now referred to as the "constraint name".</span></span> <span data-ttu-id="7a4f1-818">例如：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-818">For example:</span></span>

```csharp
var constraintName = myForeignKey.ConstraintName;
```

<span data-ttu-id="7a4f1-819">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-819">**Why**</span></span>

<span data-ttu-id="7a4f1-820">此變更可讓此領域中的命名一致，同時清楚指出這是外部索引鍵限制式的名稱，而非定義外部索引鍵的資料行或屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-820">This change brings consistency to naming in this area, and also clarifies that this is the name of the foreign key constraint, and not the column or property name that the foreign key is defined on.</span></span>

<span data-ttu-id="7a4f1-821">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-821">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-822">使用新的名稱。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-822">Use the new name.</span></span>

<a name="irdc2"></a>

### <a name="irelationaldatabasecreatorhastableshastablesasync-have-been-made-public"></a><span data-ttu-id="7a4f1-823">IRelationalDatabaseCreator.HasTables/HasTablesAsync 已設定為公用</span><span class="sxs-lookup"><span data-stu-id="7a4f1-823">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>

[<span data-ttu-id="7a4f1-824">追蹤問題 #15997</span><span class="sxs-lookup"><span data-stu-id="7a4f1-824">Tracking Issue #15997</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15997)

<span data-ttu-id="7a4f1-825">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-825">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-826">在 EF Core 3.0 之前，這些方法已受保護。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-826">Before EF Core 3.0, these methods were protected.</span></span>

<span data-ttu-id="7a4f1-827">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-827">**New behavior**</span></span>

<span data-ttu-id="7a4f1-828">從 EF Core 3.0 開始，這些方法為公用。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-828">Starting with EF Core 3.0, these methods are public.</span></span>

<span data-ttu-id="7a4f1-829">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-829">**Why**</span></span>

<span data-ttu-id="7a4f1-830">這些方法是由 EF 用來判斷資料庫是否已建立但為空資料庫。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-830">These methods are used by EF to determine if a database is created but empty.</span></span> <span data-ttu-id="7a4f1-831">當判斷是否要套用移轉時，這在 EF 外部也很實用。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-831">This can also be useful from outside EF when determining whether or not to apply migrations.</span></span>

<span data-ttu-id="7a4f1-832">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-832">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-833">變更任何覆寫的可存取性。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-833">Change the accessibility of any overrides.</span></span>

<a name="dip"></a>

### <a name="microsoftentityframeworkcoredesign-is-now-a-developmentdependency-package"></a><span data-ttu-id="7a4f1-834">Microsoft.EntityFrameworkCore.Design 現在是 DevelopmentDependency 套件</span><span class="sxs-lookup"><span data-stu-id="7a4f1-834">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>

[<span data-ttu-id="7a4f1-835">追蹤問題 #11506</span><span class="sxs-lookup"><span data-stu-id="7a4f1-835">Tracking Issue #11506</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11506)

<span data-ttu-id="7a4f1-836">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-836">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-837">在 EF Core 3.0 之前，Microsoft.EntityFrameworkCore.Design 是標準 NuGet 套件，其組件可由相依於它的的專案參考。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-837">Before EF Core 3.0, Microsoft.EntityFrameworkCore.Design was a regular NuGet package whose assembly could be referenced by projects that depended on it.</span></span>

<span data-ttu-id="7a4f1-838">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-838">**New behavior**</span></span>

<span data-ttu-id="7a4f1-839">從 EF Core 3.0 開始，它是 DevelopmentDependency 套件。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-839">Starting with EF Core 3.0, it is a DevelopmentDependency package.</span></span> <span data-ttu-id="7a4f1-840">這表示相依性不會傳遞至其他專案，而且根據預設，您無法再參考其元件。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-840">This means that the dependency won't flow transitively into other projects, and that you can no longer, by default, reference its assembly.</span></span>

<span data-ttu-id="7a4f1-841">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-841">**Why**</span></span>

<span data-ttu-id="7a4f1-842">此套件旨在用於設計階段。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-842">This package is only intended to be used at design time.</span></span> <span data-ttu-id="7a4f1-843">部署的應用程式不應該參考它。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-843">Deployed applications shouldn't reference it.</span></span> <span data-ttu-id="7a4f1-844">將套件設定為 DevelopmentDependency 會加強此建議。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-844">Making the package a DevelopmentDependency reinforces this recommendation.</span></span>

<span data-ttu-id="7a4f1-845">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-845">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-846">如果您需要參考此封裝來覆寫 EF Core 的設計階段行為，則可以更新專案中的 PackageReference 專案中繼資料。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-846">If you need to reference this package to override EF Core's design-time behavior, then you can update PackageReference item metadata in your project.</span></span>

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.0.0">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

<span data-ttu-id="7a4f1-847">若以可轉移方式透過 Microsoft.EntityFrameworkCore.Tools 參考該套件，您將必須新增明確的 PackageReference 到該套件以變更其中繼資料。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-847">If the package is being referenced transitively via Microsoft.EntityFrameworkCore.Tools, you will need to add an explicit PackageReference to the package to change its metadata.</span></span> <span data-ttu-id="7a4f1-848">這類明確參考必須加入至需要封裝之類型的任何專案中。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-848">Such an explicit reference must be added to any project where the types from the package are needed.</span></span>

<a name="SQLitePCL"></a>

### <a name="sqlitepclraw-updated-to-version-200"></a><span data-ttu-id="7a4f1-849">SQLitePCL.raw 已更新為 2.0.0 版</span><span class="sxs-lookup"><span data-stu-id="7a4f1-849">SQLitePCL.raw updated to version 2.0.0</span></span>

[<span data-ttu-id="7a4f1-850">追蹤問題 #14824</span><span class="sxs-lookup"><span data-stu-id="7a4f1-850">Tracking Issue #14824</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14824)

<span data-ttu-id="7a4f1-851">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-851">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-852">Microsoft.EntityFrameworkCore.Sqlite 先前相依於 SQLitePCL.raw 的 1.1.12 版。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-852">Microsoft.EntityFrameworkCore.Sqlite previously depended on version 1.1.12 of SQLitePCL.raw.</span></span>

<span data-ttu-id="7a4f1-853">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-853">**New behavior**</span></span>

<span data-ttu-id="7a4f1-854">我們已更新套件，以相依于版本2.0.0。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-854">We've updated our package to depend on version 2.0.0.</span></span>

<span data-ttu-id="7a4f1-855">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-855">**Why**</span></span>

<span data-ttu-id="7a4f1-856">2\.0.0 版的 SQLitePCL.raw 以 .NET Standard 2.0 為目標。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-856">Version 2.0.0 of SQLitePCL.raw targets .NET Standard 2.0.</span></span> <span data-ttu-id="7a4f1-857">它先前以 .NET Standard 1.1 為目標，這需要大量的大量的可轉移套件才能運作。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-857">It previously targeted .NET Standard 1.1 which required a large closure of transitive packages to work.</span></span>

<span data-ttu-id="7a4f1-858">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-858">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-859">SQLitePCL.raw version 2.0.0 包括一些中斷性變更。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-859">SQLitePCL.raw version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="7a4f1-860">如需詳細資訊，請參閱[版本資訊](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-860">See the [release notes](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) for details.</span></span>

<a name="NetTopologySuite"></a>

### <a name="nettopologysuite-updated-to-version-200"></a><span data-ttu-id="7a4f1-861">NetTopologySuite 已更新為 2.0.0 版</span><span class="sxs-lookup"><span data-stu-id="7a4f1-861">NetTopologySuite updated to version 2.0.0</span></span>

[<span data-ttu-id="7a4f1-862">追蹤問題 #14825</span><span class="sxs-lookup"><span data-stu-id="7a4f1-862">Tracking Issue #14825</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14825)

<span data-ttu-id="7a4f1-863">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-863">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-864">空間套件先前相依於 NetTopologySuite 1.15.1 版。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-864">The spatial packages previously depended on version 1.15.1 of NetTopologySuite.</span></span>

<span data-ttu-id="7a4f1-865">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-865">**New behavior**</span></span>

<span data-ttu-id="7a4f1-866">我們已更新我們的套件以相依於 2.0.0 版。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-866">We've update our package to depend on version 2.0.0.</span></span>

<span data-ttu-id="7a4f1-867">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-867">**Why**</span></span>

<span data-ttu-id="7a4f1-868">NetTopologySuite 2.0.0 版旨在解決 EF Core 使用者遇到的數個可用性問題。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-868">Version 2.0.0 of NetTopologySuite aims to address several usability issues encountered by EF Core users.</span></span>

<span data-ttu-id="7a4f1-869">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-869">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-870">NetTopologySuite 2.0.0 版包括一些中斷性變更。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-870">NetTopologySuite version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="7a4f1-871">如需詳細資訊，請參閱[版本資訊](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-871">See the [release notes](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) for details.</span></span>

<a name="SqlClient"></a>

### <a name="microsoftdatasqlclient-is-used-instead-of-systemdatasqlclient"></a><span data-ttu-id="7a4f1-872">SqlClient 是用來取代 SqlClient 的資料。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-872">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>

[<span data-ttu-id="7a4f1-873">追蹤問題 #15636</span><span class="sxs-lookup"><span data-stu-id="7a4f1-873">Tracking Issue #15636</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15636)

<span data-ttu-id="7a4f1-874">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-874">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-875">Microsoft.entityframeworkcore 先前的相依于 SqlClient。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-875">Microsoft.EntityFrameworkCore.SqlServer previously depended on System.Data.SqlClient.</span></span>

<span data-ttu-id="7a4f1-876">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-876">**New behavior**</span></span>

<span data-ttu-id="7a4f1-877">我們已更新套件，以相依于 SqlClient。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-877">We've updated our package to depend on Microsoft.Data.SqlClient.</span></span>

<span data-ttu-id="7a4f1-878">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-878">**Why**</span></span>

<span data-ttu-id="7a4f1-879">SqlClient 是用於 SQL Server 的旗艦版資料存取驅動程式，而 SqlClient 不再是開發的重點。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-879">Microsoft.Data.SqlClient is the flagship data access driver for SQL Server going forward, and System.Data.SqlClient no longer be the focus of development.</span></span>
<span data-ttu-id="7a4f1-880">某些重要功能（例如 Always Encrypted）僅適用于 SqlClient。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-880">Some important features, such as Always Encrypted, are only available on Microsoft.Data.SqlClient.</span></span>

<span data-ttu-id="7a4f1-881">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-881">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-882">如果您的程式碼會直接相依于 SqlClient，您必須將它變更為參考 SqlClient。因為這兩個套件會維持非常高程度的 API 相容性，所以這應該只是簡單的封裝和命名空間變更。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-882">If your code takes a direct dependency on System.Data.SqlClient, you must change it to reference Microsoft.Data.SqlClient instead; as the two packages maintain a very high degree of API compatibility, this should only be a simple package and namespace change.</span></span>

<a name="mersa"></a>

### <a name="multiple-ambiguous-self-referencing-relationships-must-be-configured"></a><span data-ttu-id="7a4f1-883">必須設定多個不明確的自我參考關聯性</span><span class="sxs-lookup"><span data-stu-id="7a4f1-883">Multiple ambiguous self-referencing relationships must be configured</span></span> 

[<span data-ttu-id="7a4f1-884">追蹤問題 #13573</span><span class="sxs-lookup"><span data-stu-id="7a4f1-884">Tracking Issue #13573</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13573)

<span data-ttu-id="7a4f1-885">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-885">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-886">具有多個自我參考單向導覽屬性和相符 FK 的實體類型，不當設定為單一關聯性。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-886">An entity type with multiple self-referencing uni-directional navigation properties and matching FKs was incorrectly configured as a single relationship.</span></span> <span data-ttu-id="7a4f1-887">例如：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-887">For example:</span></span>

```csharp
public class User 
{
        public Guid Id { get; set; }
        public User CreatedBy { get; set; }
        public User UpdatedBy { get; set; }
        public Guid CreatedById { get; set; }
        public Guid? UpdatedById { get; set; }
}
```

<span data-ttu-id="7a4f1-888">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-888">**New behavior**</span></span>

<span data-ttu-id="7a4f1-889">這種情況現在會在模型建立過程中偵測到，而且會擲回例外狀況，指出模型不明確。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-889">This scenario is now detected in model building and an exception is thrown indicating that the model is ambiguous.</span></span>

<span data-ttu-id="7a4f1-890">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-890">**Why**</span></span>

<span data-ttu-id="7a4f1-891">產生的模型不明確，在這種情況下通常會有錯誤。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-891">The resultant model was ambiguous and will likely usually be wrong for this case.</span></span>

<span data-ttu-id="7a4f1-892">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-892">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-893">使用關聯性的完整設定。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-893">Use full configuration of the relationship.</span></span> <span data-ttu-id="7a4f1-894">例如：</span><span class="sxs-lookup"><span data-stu-id="7a4f1-894">For example:</span></span>

```csharp
modelBuilder
     .Entity<User>()
     .HasOne(e => e.CreatedBy)
     .WithMany();
 
 modelBuilder
     .Entity<User>()
     .HasOne(e => e.UpdatedBy)
     .WithMany();
```

<a name="udf-empty-string"></a>
### <a name="dbfunctionschema-being-null-or-empty-string-configures-it-to-be-in-models-default-schema"></a><span data-ttu-id="7a4f1-895">DbFunction。架構為 null 或空字串，將其設定為模型的預設架構</span><span class="sxs-lookup"><span data-stu-id="7a4f1-895">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>

[<span data-ttu-id="7a4f1-896">追蹤問題 #12757</span><span class="sxs-lookup"><span data-stu-id="7a4f1-896">Tracking Issue #12757</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12757)

<span data-ttu-id="7a4f1-897">**舊行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-897">**Old behavior**</span></span>

<span data-ttu-id="7a4f1-898">以架構為空字串所設定的 DbFunction，在沒有架構的情況下被視為內建函數。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-898">A DbFunction configured with schema as an empty string was treated as built-in function without a schema.</span></span> <span data-ttu-id="7a4f1-899">例如，下列程式碼會將 `DatePart` CLR 函數對應至 SqlServer 上 `DATEPART` 內建函數。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-899">For example following code will map `DatePart` CLR function to `DATEPART` built-in function on SqlServer.</span></span>

```csharp
[DbFunction("DATEPART", Schema = "")]
public static int? DatePart(string datePartArg, DateTime? date) => throw new Exception();

```

<span data-ttu-id="7a4f1-900">**新行為**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-900">**New behavior**</span></span>

<span data-ttu-id="7a4f1-901">所有的 DbFunction 對應都會被視為對應至使用者定義的函數。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-901">All DbFunction mappings are considered to be mapped to user defined functions.</span></span> <span data-ttu-id="7a4f1-902">因此，空的字串值會將函數放在模型的預設架構內。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-902">Hence empty string value would put the function inside the default schema for the model.</span></span> <span data-ttu-id="7a4f1-903">這可能是透過 Fluent API `modelBuilder.HasDefaultSchema()` 明確設定的架構，否則為 `dbo`。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-903">Which could be the schema configured explicitly via fluent API `modelBuilder.HasDefaultSchema()` or `dbo` otherwise.</span></span>

<span data-ttu-id="7a4f1-904">**原因**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-904">**Why**</span></span>

<span data-ttu-id="7a4f1-905">先前的架構是空的，這是將該函式內建的方法，但該邏輯僅適用于 SqlServer，其中內建函數不屬於任何架構。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-905">Previously schema being empty was a way to treat that function is built-in but that logic is only applicable for SqlServer where built-in functions do not belong to any schema.</span></span>

<span data-ttu-id="7a4f1-906">**風險降低**</span><span class="sxs-lookup"><span data-stu-id="7a4f1-906">**Mitigations**</span></span>

<span data-ttu-id="7a4f1-907">手動設定 DbFunction 的轉譯，以將其對應至內建函數。</span><span class="sxs-lookup"><span data-stu-id="7a4f1-907">Configure DbFunction's translation manually to map it to a built-in function.</span></span>

```csharp
modelBuilder
    .HasDbFunction(typeof(MyContext).GetMethod(nameof(MyContext.DatePart)))
    .HasTranslation(args => SqlFunctionExpression.Create("DatePart", args, typeof(int?), null));
```
