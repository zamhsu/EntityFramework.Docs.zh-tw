---
title: 規劃 Entity Framework Core 5。0
author: ajcvickers
ms.date: 06/11/2020
uid: core/what-is-new/ef-core-5.0/plan
ms.openlocfilehash: 249560bc14f72fd524be91bb1670dbaf78ae6b60
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370574"
---
# <a name="plan-for-entity-framework-core-50"></a><span data-ttu-id="963fc-102">規劃 Entity Framework Core 5。0</span><span class="sxs-lookup"><span data-stu-id="963fc-102">Plan for Entity Framework Core 5.0</span></span>

<span data-ttu-id="963fc-103">如[規劃](xref:core/what-is-new/release_planning)程式中所述，我們已將專案關係人的輸入收集到 EF Core 5.0 版本的暫時計畫。</span><span class="sxs-lookup"><span data-stu-id="963fc-103">As described in the [planning process](xref:core/what-is-new/release_planning), we have gathered input from stakeholders into a tentative plan for the EF Core 5.0 release.</span></span>

> [!IMPORTANT] 
> <span data-ttu-id="963fc-104">此計畫仍然是進行中的工作。</span><span class="sxs-lookup"><span data-stu-id="963fc-104">This plan is still a work-in-progress.</span></span> <span data-ttu-id="963fc-105">這裡沒有任何承諾。</span><span class="sxs-lookup"><span data-stu-id="963fc-105">Nothing here is a commitment.</span></span> <span data-ttu-id="963fc-106">此計畫是一個起點，會隨著我們深入瞭解而演變。</span><span class="sxs-lookup"><span data-stu-id="963fc-106">This plan is a starting point that will evolve as we learn more.</span></span> <span data-ttu-id="963fc-107">目前未計畫5.0 的某些專案可能會提取。</span><span class="sxs-lookup"><span data-stu-id="963fc-107">Some things not currently planned for 5.0 may get pulled in.</span></span> <span data-ttu-id="963fc-108">目前規劃5.0 的某些專案可能會一度。</span><span class="sxs-lookup"><span data-stu-id="963fc-108">Some things currently planned for 5.0 may get punted out.</span></span>

### <a name="version-number-and-release-date"></a><span data-ttu-id="963fc-109">版本號碼和發行日期。</span><span class="sxs-lookup"><span data-stu-id="963fc-109">Version number and release date.</span></span>

<span data-ttu-id="963fc-110">EF Core 5.0 目前排定與[.net 5.0](https://devblogs.microsoft.com/dotnet/introducing-net-5/)同時發行。</span><span class="sxs-lookup"><span data-stu-id="963fc-110">EF Core 5.0 is currently scheduled for release at [the same time as .NET 5.0](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span> <span data-ttu-id="963fc-111">已選擇版本 "5.0" 以配合 .NET 5.0。</span><span class="sxs-lookup"><span data-stu-id="963fc-111">The version "5.0" was chosen to align with .NET 5.0.</span></span>

### <a name="supported-platforms"></a><span data-ttu-id="963fc-112">支援的平台</span><span class="sxs-lookup"><span data-stu-id="963fc-112">Supported platforms</span></span>

<span data-ttu-id="963fc-113">EF Core 5.0 計畫在任何 .NET 5.0 平臺上執行，其以[這些平臺與 .Net Core 的交集為](https://devblogs.microsoft.com/dotnet/introducing-net-5/)基礎。</span><span class="sxs-lookup"><span data-stu-id="963fc-113">EF Core 5.0 is planned to run on any .NET 5.0 platform based on the [convergence of these platforms to .NET Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span> <span data-ttu-id="963fc-114">這代表 .NET Standard 的意義，而實際使用的 TFM 仍然是 TBD。</span><span class="sxs-lookup"><span data-stu-id="963fc-114">What this means in terms of .NET Standard and the actual TFM used is still TBD.</span></span>

<span data-ttu-id="963fc-115">EF Core 5.0 不會在 .NET Framework 上執行。</span><span class="sxs-lookup"><span data-stu-id="963fc-115">EF Core 5.0 will not run on .NET Framework.</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="963fc-116">重大變更</span><span class="sxs-lookup"><span data-stu-id="963fc-116">Breaking changes</span></span>

<span data-ttu-id="963fc-117">EF Core 5.0 將包含一些[重大變更](xref:core/what-is-new/ef-core-5.0/breaking-changes)，但它們的嚴重程度比 EF Core 3.0 的情況低。</span><span class="sxs-lookup"><span data-stu-id="963fc-117">EF Core 5.0 will contain some [breaking changes](xref:core/what-is-new/ef-core-5.0/breaking-changes), but these will be much less severe than was the case for EF Core 3.0.</span></span> <span data-ttu-id="963fc-118">我們的目標是讓大部分的應用程式都能在不中斷的情況下進行更新。</span><span class="sxs-lookup"><span data-stu-id="963fc-118">Our goal is to allow the vast majority of applications to update without breaking.</span></span>

<span data-ttu-id="963fc-119">資料庫提供者應該會有一些重大變更，特別是關於 TPT 支援。</span><span class="sxs-lookup"><span data-stu-id="963fc-119">It is expected that there will be some breaking changes for database providers, especially around TPT support.</span></span> <span data-ttu-id="963fc-120">不過，我們預期更新5.0 提供者的工作將會小於更新3.0 所需的時間。</span><span class="sxs-lookup"><span data-stu-id="963fc-120">However, we expect the work to update a provider for 5.0 will be less than was required to update for 3.0.</span></span>

## <a name="themes"></a><span data-ttu-id="963fc-121">佈景主題</span><span class="sxs-lookup"><span data-stu-id="963fc-121">Themes</span></span>

<span data-ttu-id="963fc-122">我們已解壓縮幾個主要區域或主題，這會構成 EF Core 5.0 中大型投資的基礎。</span><span class="sxs-lookup"><span data-stu-id="963fc-122">We have extracted a few major areas or themes which will form the basis for the large investments in EF Core 5.0.</span></span>

## <a name="fully-transparent-many-to-many-mapping-by-convention"></a><span data-ttu-id="963fc-123">依慣例的完全透明多對多對應</span><span class="sxs-lookup"><span data-stu-id="963fc-123">Fully transparent many-to-many mapping by convention</span></span>

<span data-ttu-id="963fc-124">潛在客戶開發人員： @smitpatel 、 @AndriySvyryd 和@lajones</span><span class="sxs-lookup"><span data-stu-id="963fc-124">Lead developers: @smitpatel, @AndriySvyryd, and @lajones</span></span>

<span data-ttu-id="963fc-125">由[#10508](https://github.com/aspnet/EntityFrameworkCore/issues/10508)追蹤</span><span class="sxs-lookup"><span data-stu-id="963fc-125">Tracked by [#10508](https://github.com/aspnet/EntityFrameworkCore/issues/10508)</span></span>

<span data-ttu-id="963fc-126">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="963fc-126">T-shirt size: L</span></span>

<span data-ttu-id="963fc-127">狀態：進行中</span><span class="sxs-lookup"><span data-stu-id="963fc-127">Status: In-progress</span></span>

<span data-ttu-id="963fc-128">「多對多」是 GitHub 待處理專案上[最常要求的功能](https://github.com/aspnet/EntityFrameworkCore/issues/1368)（~ 506 投票）。</span><span class="sxs-lookup"><span data-stu-id="963fc-128">Many-to-many is the [most requested feature](https://github.com/aspnet/EntityFrameworkCore/issues/1368) (~506 votes) on the GitHub backlog.</span></span>

<span data-ttu-id="963fc-129">對多對多關聯性的支援可分為三個主要區域：</span><span class="sxs-lookup"><span data-stu-id="963fc-129">Support for many-to-many relationships can be broken down into three major areas:</span></span>

* <span data-ttu-id="963fc-130">略過導覽屬性--下一個主題所涵蓋的內容。</span><span class="sxs-lookup"><span data-stu-id="963fc-130">Skip navigation properties--covered by the next theme.</span></span>
* <span data-ttu-id="963fc-131">屬性包實體類型。</span><span class="sxs-lookup"><span data-stu-id="963fc-131">Property-bag entity types.</span></span> <span data-ttu-id="963fc-132">這些專案允許將標準 CLR 型別（例如 `Dictionary` ）用於實體實例，因此每個實體型別都不需要明確的 clr 型別。</span><span class="sxs-lookup"><span data-stu-id="963fc-132">These allow a standard CLR type (e.g. `Dictionary`) to be used for entity instances such that an explicit CLR type is not needed for each entity type.</span></span> <span data-ttu-id="963fc-133">由[#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914)追蹤。</span><span class="sxs-lookup"><span data-stu-id="963fc-133">Tracked by [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914).</span></span>
* <span data-ttu-id="963fc-134">方便設定多對多關聯性的便利。</span><span class="sxs-lookup"><span data-stu-id="963fc-134">Sugar for easy configuration of many-to-many relationships.</span></span>

<span data-ttu-id="963fc-135">除了略過流覽支援之外，我們現在還將多對多的其他區域提取到 EF Core 5.0，以提供完整的體驗。</span><span class="sxs-lookup"><span data-stu-id="963fc-135">In addition to the skip navigation support, we are now pulling these other areas of many-to-many into EF Core 5.0 so as to provide a complete experience.</span></span>

## <a name="many-to-many-navigation-properties-aka-skip-navigations"></a><span data-ttu-id="963fc-136">多對多導覽屬性（即「略過流覽」）</span><span class="sxs-lookup"><span data-stu-id="963fc-136">Many-to-many navigation properties (a.k.a "skip navigations")</span></span>

<span data-ttu-id="963fc-137">潛在客戶開發人員： @smitpatel 和@AndriySvyryd</span><span class="sxs-lookup"><span data-stu-id="963fc-137">Lead developers: @smitpatel and @AndriySvyryd</span></span>

<span data-ttu-id="963fc-138">由[#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)追蹤</span><span class="sxs-lookup"><span data-stu-id="963fc-138">Tracked by [#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)</span></span>

<span data-ttu-id="963fc-139">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="963fc-139">T-shirt size: L</span></span>

<span data-ttu-id="963fc-140">狀態：進行中</span><span class="sxs-lookup"><span data-stu-id="963fc-140">Status: In-progress</span></span>

<span data-ttu-id="963fc-141">如第一個主題中所述，多對多支援有多個層面。</span><span class="sxs-lookup"><span data-stu-id="963fc-141">As described in the first theme, many-to-many support has multiple aspects.</span></span>
<span data-ttu-id="963fc-142">本主題特別說明略過導覽的使用。</span><span class="sxs-lookup"><span data-stu-id="963fc-142">This theme specifically tracks use of skip navigations.</span></span>
<span data-ttu-id="963fc-143">我們相信，想要多對多支援的最重要封鎖程式，在商務邏輯（例如查詢）中無法使用「自然」關聯性，而不需要參考聯結資料表。</span><span class="sxs-lookup"><span data-stu-id="963fc-143">We believe that the most significant blocker for those wanting many-to-many support is not being able to use the "natural" relationships, without referring to the join table, in business logic such as queries.</span></span>
<span data-ttu-id="963fc-144">聯結資料表實體類型可能仍然存在，但不應該取得商務邏輯的方式。</span><span class="sxs-lookup"><span data-stu-id="963fc-144">The join table entity type may still exist, but it should not get in the way of business logic.</span></span>

## <a name="table-per-type-tpt-inheritance-mapping"></a><span data-ttu-id="963fc-145">每一類型的資料表（TPT）繼承對應</span><span class="sxs-lookup"><span data-stu-id="963fc-145">Table-per-type (TPT) inheritance mapping</span></span>

<span data-ttu-id="963fc-146">首席開發人員：@AndriySvyryd</span><span class="sxs-lookup"><span data-stu-id="963fc-146">Lead developer: @AndriySvyryd</span></span>

<span data-ttu-id="963fc-147">由[#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266)追蹤</span><span class="sxs-lookup"><span data-stu-id="963fc-147">Tracked by [#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266)</span></span>

<span data-ttu-id="963fc-148">T 恤尺寸： XL</span><span class="sxs-lookup"><span data-stu-id="963fc-148">T-shirt size: XL</span></span>

<span data-ttu-id="963fc-149">狀態：進行中</span><span class="sxs-lookup"><span data-stu-id="963fc-149">Status: In-progress</span></span>

<span data-ttu-id="963fc-150">我們正在進行 TPT，因為這兩者都是高度要求的功能（~ 289 的投票; 第三個），因為它需要一些低層級的變更，我們覺得這適用于整體 .NET 5 計畫的基本本質。</span><span class="sxs-lookup"><span data-stu-id="963fc-150">We're doing TPT because it is both a highly requested feature (~289 votes; 3rd overall) and because it requires some low-level changes that we feel are appropriate for the foundational nature of the overall .NET 5 plan.</span></span> <span data-ttu-id="963fc-151">我們預期這會導致資料庫提供者的中斷性變更，但這應該比3.0 所需的變更少很多。</span><span class="sxs-lookup"><span data-stu-id="963fc-151">We expect this to result in breaking changes for database providers, although these should be much less severe than the changes required for 3.0.</span></span>

## <a name="filtered-include"></a><span data-ttu-id="963fc-152">篩選的包含</span><span class="sxs-lookup"><span data-stu-id="963fc-152">Filtered Include</span></span>

<span data-ttu-id="963fc-153">首席開發人員：@maumar</span><span class="sxs-lookup"><span data-stu-id="963fc-153">Lead developer: @maumar</span></span>

<span data-ttu-id="963fc-154">由[#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833)追蹤</span><span class="sxs-lookup"><span data-stu-id="963fc-154">Tracked by [#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833)</span></span>

<span data-ttu-id="963fc-155">T 恤尺寸： M</span><span class="sxs-lookup"><span data-stu-id="963fc-155">T-shirt size: M</span></span>

<span data-ttu-id="963fc-156">狀態：進行中</span><span class="sxs-lookup"><span data-stu-id="963fc-156">Status: In-progress</span></span>

<span data-ttu-id="963fc-157">篩選的「包含」是一種高度要求的功能（大約376的投票; 第二個整體），這不是大量的工作，因此我們認為會解除封鎖或更輕鬆地進行目前需要模型層級篩選或更複雜查詢的許多案例。</span><span class="sxs-lookup"><span data-stu-id="963fc-157">Filtered Include is a highly-requested feature (~376 votes; 2nd overall) that isn't a huge amount of work, and that we believe will unblock or make easier many scenarios that currently require model-level filters or more complex queries.</span></span>

## <a name="split-include"></a><span data-ttu-id="963fc-158">分割包含</span><span class="sxs-lookup"><span data-stu-id="963fc-158">Split Include</span></span>

<span data-ttu-id="963fc-159">首席開發人員：@smitpatel</span><span class="sxs-lookup"><span data-stu-id="963fc-159">Lead developer: @smitpatel</span></span>

<span data-ttu-id="963fc-160">由[#20892](https://github.com/dotnet/efcore/issues/20892)追蹤</span><span class="sxs-lookup"><span data-stu-id="963fc-160">Tracked by [#20892](https://github.com/dotnet/efcore/issues/20892)</span></span>

<span data-ttu-id="963fc-161">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="963fc-161">T-shirt size: L</span></span>

<span data-ttu-id="963fc-162">狀態：進行中</span><span class="sxs-lookup"><span data-stu-id="963fc-162">Status: In-progress</span></span>

<span data-ttu-id="963fc-163">EF Core 3.0 已變更預設行為，以建立給定 LINQ 查詢的單一 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="963fc-163">EF Core 3.0 changed the default behavior to create a single SQL query for a given LINQ query.</span></span>
<span data-ttu-id="963fc-164">這會對使用包含多個集合的查詢造成大量的效能衰退。</span><span class="sxs-lookup"><span data-stu-id="963fc-164">This caused large performance regressions for queries that use Include for multiple collections.</span></span>

<span data-ttu-id="963fc-165">在 EF Core 5.0 中，我們會保留新的預設行為。</span><span class="sxs-lookup"><span data-stu-id="963fc-165">In EF Core 5.0, we are retaining the new default behavior.</span></span>
<span data-ttu-id="963fc-166">不過，EF Core 5.0 現在會允許產生多個集合查詢，包括有單一查詢導致效能不佳的情況。</span><span class="sxs-lookup"><span data-stu-id="963fc-166">However, EF Core 5.0 will now allow generation of multiple queries for collection Includes where having a single query is causing bad performance..</span></span> 

## <a name="rationalize-totable-toquery-toview-fromsql-etc"></a><span data-ttu-id="963fc-167">合理化 ToTable、ToQuery、ToView、FromSql 等。</span><span class="sxs-lookup"><span data-stu-id="963fc-167">Rationalize ToTable, ToQuery, ToView, FromSql, etc.</span></span>

<span data-ttu-id="963fc-168">潛在客戶開發人員： @maumar 和@smitpatel</span><span class="sxs-lookup"><span data-stu-id="963fc-168">Lead developers: @maumar and @smitpatel</span></span>

<span data-ttu-id="963fc-169">由[#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270)追蹤</span><span class="sxs-lookup"><span data-stu-id="963fc-169">Tracked by [#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270)</span></span>

<span data-ttu-id="963fc-170">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="963fc-170">T-shirt size: L</span></span>

<span data-ttu-id="963fc-171">狀態：進行中</span><span class="sxs-lookup"><span data-stu-id="963fc-171">Status: In-progress</span></span>

<span data-ttu-id="963fc-172">我們已在舊版中進行了支援原始 SQL、無索引鍵類型和相關區域的進度。</span><span class="sxs-lookup"><span data-stu-id="963fc-172">We have made progress in previous releases towards supporting raw SQL, keyless types, and related areas.</span></span> <span data-ttu-id="963fc-173">不過，所有專案的整體運作方式都有差距和不一致的情況。</span><span class="sxs-lookup"><span data-stu-id="963fc-173">However, there are both gaps and inconsistencies in the way everything works together as a whole.</span></span> <span data-ttu-id="963fc-174">5.0 的目標是要修正這些問題，並建立定義、遷移和使用不同類型實體及其相關聯查詢和資料庫成品的絕佳體驗。</span><span class="sxs-lookup"><span data-stu-id="963fc-174">The goal for 5.0 is to fix these and create a good experience for defining, migrating, and using different types of entities and their associated queries and database artifacts.</span></span> <span data-ttu-id="963fc-175">這也可能牽涉到已編譯查詢 API 的更新。</span><span class="sxs-lookup"><span data-stu-id="963fc-175">This may also involve updates to the compiled query API.</span></span>

<span data-ttu-id="963fc-176">請注意，這個專案可能會導致某些應用層級的中斷性變更，因為我們目前擁有的一些功能過於寬鬆，所以可能很快就會導致使用者 pits 失敗。</span><span class="sxs-lookup"><span data-stu-id="963fc-176">Note that this item may result in some application-level breaking changes since some of the functionality we currently have is too permissive such that it can quickly lead people into pits of failure.</span></span> <span data-ttu-id="963fc-177">我們可能會同時封鎖這部分功能，並提供有關該怎麼做的指引。</span><span class="sxs-lookup"><span data-stu-id="963fc-177">We will likely end up blocking some of this functionality together with guidance on what to do instead.</span></span>

## <a name="general-query-enhancements"></a><span data-ttu-id="963fc-178">一般查詢增強功能</span><span class="sxs-lookup"><span data-stu-id="963fc-178">General query enhancements</span></span>

<span data-ttu-id="963fc-179">潛在客戶開發人員： @smitpatel 和@maumar</span><span class="sxs-lookup"><span data-stu-id="963fc-179">Lead developers: @smitpatel and @maumar</span></span>

<span data-ttu-id="963fc-180">[ `area-query` 在5.0 里程碑中由標記為的問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)所追蹤</span><span class="sxs-lookup"><span data-stu-id="963fc-180">Tracked by [issues labeled with `area-query` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="963fc-181">T 恤尺寸： XL</span><span class="sxs-lookup"><span data-stu-id="963fc-181">T-shirt size: XL</span></span>

<span data-ttu-id="963fc-182">狀態：進行中</span><span class="sxs-lookup"><span data-stu-id="963fc-182">Status: In-progress</span></span>

<span data-ttu-id="963fc-183">已針對 EF Core 3.0 廣泛重寫查詢轉譯程式碼。</span><span class="sxs-lookup"><span data-stu-id="963fc-183">The query translation code was extensively rewritten for EF Core 3.0.</span></span> <span data-ttu-id="963fc-184">這種情況下，查詢程式碼通常會有更健全的狀態。</span><span class="sxs-lookup"><span data-stu-id="963fc-184">The query code is generally in a much more robust state because of this.</span></span> <span data-ttu-id="963fc-185">針對5.0，我們不打算在支援 TPT 和略過導覽屬性所需的範圍內進行主要查詢變更。</span><span class="sxs-lookup"><span data-stu-id="963fc-185">For 5.0 we aren't planning on making major query changes, outside those needed to support TPT and skip navigation properties.</span></span> <span data-ttu-id="963fc-186">不過，仍然需要進行一些工作，以修正3.0 檢修中剩餘的技術債務。</span><span class="sxs-lookup"><span data-stu-id="963fc-186">However, there is still significant work needed to fix some technical debt left over from the 3.0 overhaul.</span></span> <span data-ttu-id="963fc-187">我們也計畫修正許多 bug，並執行小型的增強功能，以進一步改善整體的查詢體驗。</span><span class="sxs-lookup"><span data-stu-id="963fc-187">We also plan to fix many bugs and implement small enhancements to further improve the overall query experience.</span></span>

## <a name="migrations-and-deployment-experience"></a><span data-ttu-id="963fc-188">遷移與部署體驗</span><span class="sxs-lookup"><span data-stu-id="963fc-188">Migrations and deployment experience</span></span>

<span data-ttu-id="963fc-189">潛在客戶開發人員：@bricelam</span><span class="sxs-lookup"><span data-stu-id="963fc-189">Lead developers: @bricelam</span></span>

<span data-ttu-id="963fc-190">由[#19587](https://github.com/dotnet/efcore/issues/19587)追蹤</span><span class="sxs-lookup"><span data-stu-id="963fc-190">Tracked by [#19587](https://github.com/dotnet/efcore/issues/19587)</span></span>

<span data-ttu-id="963fc-191">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="963fc-191">T-shirt size: L</span></span>

<span data-ttu-id="963fc-192">狀態：進行中</span><span class="sxs-lookup"><span data-stu-id="963fc-192">Status: In-progress</span></span>

<span data-ttu-id="963fc-193">目前，許多開發人員會在應用程式啟動時遷移其資料庫。</span><span class="sxs-lookup"><span data-stu-id="963fc-193">Currently, many developers migrate their databases at application startup time.</span></span> <span data-ttu-id="963fc-194">這很簡單，但不建議這麼做，因為：</span><span class="sxs-lookup"><span data-stu-id="963fc-194">This is easy but is not recommended because:</span></span>

* <span data-ttu-id="963fc-195">多個執行緒/進程/伺服器可能會嘗試同時遷移資料庫</span><span class="sxs-lookup"><span data-stu-id="963fc-195">Multiple threads/processes/servers may attempt to migrate the database concurrently</span></span>
* <span data-ttu-id="963fc-196">應用程式可能會在發生這種情況時，嘗試存取不一致的狀態</span><span class="sxs-lookup"><span data-stu-id="963fc-196">Applications may try to access inconsistent state while this is happening</span></span>
* <span data-ttu-id="963fc-197">通常不應授與修改架構的資料庫許可權以執行應用程式</span><span class="sxs-lookup"><span data-stu-id="963fc-197">Usually the database permissions to modify the schema should not be granted for application execution</span></span>
* <span data-ttu-id="963fc-198">如果發生錯誤，很難還原為「清除」狀態</span><span class="sxs-lookup"><span data-stu-id="963fc-198">It's hard to revert back to a clean state if something goes wrong</span></span>

<span data-ttu-id="963fc-199">我們想要在此提供更好的體驗，讓您可以輕鬆地在部署時遷移資料庫。</span><span class="sxs-lookup"><span data-stu-id="963fc-199">We want to deliver a better experience here that allows an easy way to migrate the database at deployment time.</span></span> <span data-ttu-id="963fc-200">這應該：</span><span class="sxs-lookup"><span data-stu-id="963fc-200">This should:</span></span>

* <span data-ttu-id="963fc-201">在 Linux、Mac 和 Windows 上工作</span><span class="sxs-lookup"><span data-stu-id="963fc-201">Work on Linux, Mac, and Windows</span></span>
* <span data-ttu-id="963fc-202">在命令列上是不錯的體驗</span><span class="sxs-lookup"><span data-stu-id="963fc-202">Be a good experience on the command line</span></span>
* <span data-ttu-id="963fc-203">容器的支援案例</span><span class="sxs-lookup"><span data-stu-id="963fc-203">Support scenarios with containers</span></span>
* <span data-ttu-id="963fc-204">使用常用的真實世界部署工具/流程</span><span class="sxs-lookup"><span data-stu-id="963fc-204">Work with commonly used real-world deployment tools/flows</span></span>
* <span data-ttu-id="963fc-205">至少整合到 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="963fc-205">Integrate into at least Visual Studio</span></span>

<span data-ttu-id="963fc-206">結果可能是 EF Core 中的許多小改良（例如，SQLite 上較佳的遷移），並提供與其他小組的指引和更長期的共同作業，以改善超越 EF 的端對端體驗。</span><span class="sxs-lookup"><span data-stu-id="963fc-206">The result is likely to be many small improvements in EF Core (for example, better Migrations on SQLite), together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

## <a name="ef-core-platforms-experience"></a><span data-ttu-id="963fc-207">EF Core 平臺體驗</span><span class="sxs-lookup"><span data-stu-id="963fc-207">EF Core platforms experience</span></span> 

<span data-ttu-id="963fc-208">潛在客戶開發人員： @roji 和@bricelam</span><span class="sxs-lookup"><span data-stu-id="963fc-208">Lead developers: @roji and @bricelam</span></span>

<span data-ttu-id="963fc-209">由[#19588](https://github.com/dotnet/efcore/issues/19588)追蹤</span><span class="sxs-lookup"><span data-stu-id="963fc-209">Tracked by [#19588](https://github.com/dotnet/efcore/issues/19588)</span></span>

<span data-ttu-id="963fc-210">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="963fc-210">T-shirt size: L</span></span>

<span data-ttu-id="963fc-211">狀態：未啟動</span><span class="sxs-lookup"><span data-stu-id="963fc-211">Status: Not started</span></span>

<span data-ttu-id="963fc-212">我們有在傳統的 MVC web 應用程式中使用 EF Core 的絕佳指引。</span><span class="sxs-lookup"><span data-stu-id="963fc-212">We have good guidance for using EF Core in traditional MVC-like web applications.</span></span> <span data-ttu-id="963fc-213">其他平臺和應用程式模型的指引可能遺失或過期。</span><span class="sxs-lookup"><span data-stu-id="963fc-213">Guidance for other platforms and application models is either missing or out-of-date.</span></span> <span data-ttu-id="963fc-214">針對 EF Core 5.0，我們計畫調查、改善及記載搭配 EF Core 使用的體驗：</span><span class="sxs-lookup"><span data-stu-id="963fc-214">For EF Core 5.0, we plan to investigate, improve, and document the experience of using EF Core with:</span></span>

* <span data-ttu-id="963fc-215">Blazor</span><span class="sxs-lookup"><span data-stu-id="963fc-215">Blazor</span></span>
* <span data-ttu-id="963fc-216">Xamarin，包括使用 AOT/連結器案例</span><span class="sxs-lookup"><span data-stu-id="963fc-216">Xamarin, including using the AOT/linker story</span></span>
* <span data-ttu-id="963fc-217">WinForms/WPF/WinUI，可能還有其他 U.I。</span><span class="sxs-lookup"><span data-stu-id="963fc-217">WinForms/WPF/WinUI and possibly other U.I.</span></span> <span data-ttu-id="963fc-218">frameworks</span><span class="sxs-lookup"><span data-stu-id="963fc-218">frameworks</span></span>

<span data-ttu-id="963fc-219">這可能是 EF Core 中的許多小改進，以及與其他小組的指引和更長期的共同作業，以改善超越 EF 的端對端體驗。</span><span class="sxs-lookup"><span data-stu-id="963fc-219">This is likely to be many small improvements in EF Core, together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

<span data-ttu-id="963fc-220">我們打算查看的特定領域包括：</span><span class="sxs-lookup"><span data-stu-id="963fc-220">Specific areas we plan to look at are:</span></span>

* <span data-ttu-id="963fc-221">部署，包括使用 EF 工具（例如）進行遷移的經驗</span><span class="sxs-lookup"><span data-stu-id="963fc-221">Deployment, including the experience for using EF tooling such as for Migrations</span></span>
* <span data-ttu-id="963fc-222">應用程式模型，包括 Xamarin 和 Blazor，還有其他可能</span><span class="sxs-lookup"><span data-stu-id="963fc-222">Application models, including Xamarin and Blazor, and probably others</span></span>
* <span data-ttu-id="963fc-223">SQLite 體驗，包括空間體驗和資料表重建</span><span class="sxs-lookup"><span data-stu-id="963fc-223">SQLite experiences, including the spatial experience and table rebuilds</span></span>
* <span data-ttu-id="963fc-224">AOT 和連結體驗</span><span class="sxs-lookup"><span data-stu-id="963fc-224">AOT and linking experiences</span></span>
* <span data-ttu-id="963fc-225">診斷整合，包括效能計數器</span><span class="sxs-lookup"><span data-stu-id="963fc-225">Diagnostics integration, including perf counters</span></span>

## <a name="performance"></a><span data-ttu-id="963fc-226">效能</span><span class="sxs-lookup"><span data-stu-id="963fc-226">Performance</span></span>

<span data-ttu-id="963fc-227">首席開發人員：@roji</span><span class="sxs-lookup"><span data-stu-id="963fc-227">Lead developer: @roji</span></span>

<span data-ttu-id="963fc-228">[ `area-perf` 在5.0 里程碑中由標記為的問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)所追蹤</span><span class="sxs-lookup"><span data-stu-id="963fc-228">Tracked by [issues labeled with `area-perf` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="963fc-229">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="963fc-229">T-shirt size: L</span></span>

<span data-ttu-id="963fc-230">狀態：進行中</span><span class="sxs-lookup"><span data-stu-id="963fc-230">Status: In-progress</span></span>

<span data-ttu-id="963fc-231">針對 EF Core，我們計畫改善我們的效能基準測試套件，並對執行時間進行有引導的效能改進。</span><span class="sxs-lookup"><span data-stu-id="963fc-231">For EF Core, we plan to improve our suite of performance benchmarks and make directed performance improvements to the runtime.</span></span> <span data-ttu-id="963fc-232">此外，我們計畫在3.0 發行週期內完成新的 ADO.NET 批次處理 API，這是原型。</span><span class="sxs-lookup"><span data-stu-id="963fc-232">In addition, we plan to complete the new ADO.NET batching API which was prototyped during the 3.0 release cycle.</span></span> <span data-ttu-id="963fc-233">此外，在 ADO.NET 層，我們也為 Npgsql 提供者規劃了額外的效能改進。</span><span class="sxs-lookup"><span data-stu-id="963fc-233">Also at the ADO.NET layer, we plan additional performance improvements to the Npgsql provider.</span></span>

<span data-ttu-id="963fc-234">在這項工作中，我們也計畫在適當的情況下新增 ADO.NET/EF 核心效能計數器和其他診斷。</span><span class="sxs-lookup"><span data-stu-id="963fc-234">As part of this work we also plan to add ADO.NET/EF Core performance counters and other diagnostics as appropriate.</span></span>

## <a name="architecturalcontributor-documentation"></a><span data-ttu-id="963fc-235">架構/參與者檔</span><span class="sxs-lookup"><span data-stu-id="963fc-235">Architectural/contributor documentation</span></span>

<span data-ttu-id="963fc-236">潛在客戶文件管理：@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="963fc-236">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="963fc-237">由[#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)追蹤</span><span class="sxs-lookup"><span data-stu-id="963fc-237">Tracked by [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)</span></span>

<span data-ttu-id="963fc-238">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="963fc-238">T-shirt size: L</span></span>

<span data-ttu-id="963fc-239">狀態：剪下</span><span class="sxs-lookup"><span data-stu-id="963fc-239">Status: Cut</span></span>

<span data-ttu-id="963fc-240">這裡的概念是要讓您更輕鬆地瞭解 EF Core 內部的狀況。</span><span class="sxs-lookup"><span data-stu-id="963fc-240">The idea here is to make it easier to understand what is going on in the internals of EF Core.</span></span> <span data-ttu-id="963fc-241">這對任何使用 EF Core 的人來說都很有用，但主要動機是讓外部人員更容易：</span><span class="sxs-lookup"><span data-stu-id="963fc-241">This can be useful to anyone using EF Core, but the primary motivation is to make it easier for external people to:</span></span>

* <span data-ttu-id="963fc-242">參與 EF Core 程式碼</span><span class="sxs-lookup"><span data-stu-id="963fc-242">Contribute to the EF Core code</span></span>
* <span data-ttu-id="963fc-243">建立資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="963fc-243">Create database providers</span></span>
* <span data-ttu-id="963fc-244">建立其他擴充功能</span><span class="sxs-lookup"><span data-stu-id="963fc-244">Build other extensions</span></span>

<span data-ttu-id="963fc-245">更新：可惜的是，此計畫太確保建構完善。</span><span class="sxs-lookup"><span data-stu-id="963fc-245">Update: Unfortunately, this plan was too ambitious.</span></span>
<span data-ttu-id="963fc-246">我們還是相信這一點很重要，但可惜的是，它不會 EF Core 5.0。</span><span class="sxs-lookup"><span data-stu-id="963fc-246">We still believe this is important, but unfortunately it won't land with EF Core 5.0.</span></span>

## <a name="microsoftdatasqlite-documentation"></a><span data-ttu-id="963fc-247">Microsoft. Sqlite 檔</span><span class="sxs-lookup"><span data-stu-id="963fc-247">Microsoft.Data.Sqlite documentation</span></span>

<span data-ttu-id="963fc-248">潛在客戶文件管理：@bricelam</span><span class="sxs-lookup"><span data-stu-id="963fc-248">Lead documenter: @bricelam</span></span>

<span data-ttu-id="963fc-249">由[#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)追蹤</span><span class="sxs-lookup"><span data-stu-id="963fc-249">Tracked by [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)</span></span>

<span data-ttu-id="963fc-250">T 恤尺寸： M</span><span class="sxs-lookup"><span data-stu-id="963fc-250">T-shirt size: M</span></span>

<span data-ttu-id="963fc-251">狀態：已完成。</span><span class="sxs-lookup"><span data-stu-id="963fc-251">Status: Completed.</span></span> <span data-ttu-id="963fc-252">新的檔已[上線于 Microsoft 檔網站](https://docs.microsoft.com/dotnet/standard/data/sqlite/?tabs=netcore-cli)。</span><span class="sxs-lookup"><span data-stu-id="963fc-252">The new documentation is [live on the Microsoft docs site](https://docs.microsoft.com/dotnet/standard/data/sqlite/?tabs=netcore-cli).</span></span>

<span data-ttu-id="963fc-253">EF 小組也擁有 ADO.NET 提供者。</span><span class="sxs-lookup"><span data-stu-id="963fc-253">The EF Team also owns the Microsoft.Data.Sqlite ADO.NET provider.</span></span> <span data-ttu-id="963fc-254">我們計畫將此提供者完整記錄為5.0 版本的一部分。</span><span class="sxs-lookup"><span data-stu-id="963fc-254">We plan to fully document this provider as part of the 5.0 release.</span></span>

## <a name="general-documentation"></a><span data-ttu-id="963fc-255">一般檔</span><span class="sxs-lookup"><span data-stu-id="963fc-255">General documentation</span></span>

<span data-ttu-id="963fc-256">潛在客戶文件管理：@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="963fc-256">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="963fc-257">[5.0 里程碑的](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)檔存放庫中的問題追蹤</span><span class="sxs-lookup"><span data-stu-id="963fc-257">Tracked by [issues in the docs repo in the 5.0 milestone](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="963fc-258">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="963fc-258">T-shirt size: L</span></span>

<span data-ttu-id="963fc-259">狀態：進行中</span><span class="sxs-lookup"><span data-stu-id="963fc-259">Status: In-progress</span></span>

<span data-ttu-id="963fc-260">我們已在更新3.0 和3.1 版本的檔。</span><span class="sxs-lookup"><span data-stu-id="963fc-260">We are already in the process of updating documentation for the 3.0 and 3.1 releases.</span></span> <span data-ttu-id="963fc-261">我們也在努力：</span><span class="sxs-lookup"><span data-stu-id="963fc-261">We are also working on:</span></span>
  * <span data-ttu-id="963fc-262">深入瞭解開始使用檔，使其更平易近人/更容易遵循</span><span class="sxs-lookup"><span data-stu-id="963fc-262">An overhaul of the getting started docs to make them more approachable/easier to follow</span></span>
  * <span data-ttu-id="963fc-263">重新組織檔，使其更容易尋找及新增交互參考</span><span class="sxs-lookup"><span data-stu-id="963fc-263">Reorganization of docs to make things easier to find and to add cross-references</span></span>
  * <span data-ttu-id="963fc-264">新增更多詳細資料和對現有檔的說明</span><span class="sxs-lookup"><span data-stu-id="963fc-264">Adding more details and clarifications to existing docs</span></span>
  * <span data-ttu-id="963fc-265">更新範例並新增更多範例</span><span class="sxs-lookup"><span data-stu-id="963fc-265">Updating the samples and adding more examples</span></span>

## <a name="fixing-bugs"></a><span data-ttu-id="963fc-266">修正 bug</span><span class="sxs-lookup"><span data-stu-id="963fc-266">Fixing bugs</span></span>

<span data-ttu-id="963fc-267">[ `type-bug` 在5.0 里程碑中由標記為的問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)所追蹤</span><span class="sxs-lookup"><span data-stu-id="963fc-267">Tracked by [issues labeled with `type-bug` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)</span></span>

<span data-ttu-id="963fc-268">開發人員： @roji 、 @maumar 、 @bricelam 、 @smitpatel 、 @AndriySvyryd 、@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="963fc-268">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="963fc-269">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="963fc-269">T-shirt size: L</span></span>

<span data-ttu-id="963fc-270">狀態：進行中</span><span class="sxs-lookup"><span data-stu-id="963fc-270">Status: In-progress</span></span>

<span data-ttu-id="963fc-271">在撰寫本文時，我們會在5.0 版本（已修正62）中修正 135 bug，但與上面的_一般查詢增強功能_相比，有相當大的重迭。</span><span class="sxs-lookup"><span data-stu-id="963fc-271">At the time of writing, we have 135 bugs triaged to be fixed in the 5.0 release (with 62 already fixed), but there is significant overlap with the _General query enhancements_ section above.</span></span>

<span data-ttu-id="963fc-272">傳入的速率（最後在里程碑中運作的問題）是3.0 發行期間每月大約23個問題。</span><span class="sxs-lookup"><span data-stu-id="963fc-272">The incoming rate (issues that end up as work in a milestone) was about 23 issues per month over the course of the 3.0 release.</span></span> <span data-ttu-id="963fc-273">並非所有這些都必須在5.0 中修正。</span><span class="sxs-lookup"><span data-stu-id="963fc-273">Not all of these will need to be fixed in 5.0.</span></span> <span data-ttu-id="963fc-274">我們打算在5.0 時間範圍內修正其他150問題，這是粗略的估計。</span><span class="sxs-lookup"><span data-stu-id="963fc-274">As a rough estimate we plan to fix an additional 150 issues in the 5.0 time frame.</span></span>

## <a name="small-enhancements"></a><span data-ttu-id="963fc-275">小型增強功能</span><span class="sxs-lookup"><span data-stu-id="963fc-275">Small enhancements</span></span>

<span data-ttu-id="963fc-276">[ `type-enhancement` 在5.0 里程碑中由標記為的問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)所追蹤</span><span class="sxs-lookup"><span data-stu-id="963fc-276">Tracked by [issues labeled with `type-enhancement` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)</span></span>

<span data-ttu-id="963fc-277">開發人員： @roji 、 @maumar 、 @bricelam 、 @smitpatel 、 @AndriySvyryd 、@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="963fc-277">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="963fc-278">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="963fc-278">T-shirt size: L</span></span>

<span data-ttu-id="963fc-279">狀態：進行中</span><span class="sxs-lookup"><span data-stu-id="963fc-279">Status: In-progress</span></span>

<span data-ttu-id="963fc-280">除了上面所述的更大功能之外，我們也針對5.0 排定了許多較小的改進，以修正「紙張切割」。</span><span class="sxs-lookup"><span data-stu-id="963fc-280">In addition to the bigger features outlined above, we also have many smaller improvements scheduled for 5.0 to fix "paper-cuts".</span></span> <span data-ttu-id="963fc-281">請注意，上述的許多增強功能也都涵蓋在上面所述的一般主題。</span><span class="sxs-lookup"><span data-stu-id="963fc-281">Note that many of these enhancements are also covered by the more general themes outlined above.</span></span>

## <a name="below-the-line"></a><span data-ttu-id="963fc-282">底下-行</span><span class="sxs-lookup"><span data-stu-id="963fc-282">Below-the-line</span></span>

<span data-ttu-id="963fc-283">由[標記 `consider-for-next-release` ](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)為的問題所追蹤</span><span class="sxs-lookup"><span data-stu-id="963fc-283">Tracked by [issues labeled with `consider-for-next-release`](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span></span>

<span data-ttu-id="963fc-284">這些是目前**未**針對5.0 版本排程的錯誤修正和增強功能，但我們會根據上述工作的進度，查看是否為延展目標。</span><span class="sxs-lookup"><span data-stu-id="963fc-284">These are bug fixes and enhancements that are **not** currently scheduled for the 5.0 release, but we will look at as stretch goals depending on the progress made on the work above.</span></span>

<span data-ttu-id="963fc-285">此外，我們在規劃時，一律會考慮[最投票的問題](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc)。</span><span class="sxs-lookup"><span data-stu-id="963fc-285">In addition, we always consider the [most voted issues](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) when planning.</span></span> <span data-ttu-id="963fc-286">從版本中去除這些問題總是很麻煩，但我們對我們所擁有的資源需要有實際的計畫。</span><span class="sxs-lookup"><span data-stu-id="963fc-286">Cutting any of these issues from a release is always painful, but we do need a realistic plan for the resources we have.</span></span>

## <a name="feedback"></a><span data-ttu-id="963fc-287">意見反應</span><span class="sxs-lookup"><span data-stu-id="963fc-287">Feedback</span></span>

<span data-ttu-id="963fc-288">您對計劃的意見反應很重要。</span><span class="sxs-lookup"><span data-stu-id="963fc-288">Your feedback on planning is important.</span></span> <span data-ttu-id="963fc-289">若要指出問題的重要性，最佳方式是在 GitHub 上針對該問題投票 (大拇指)。</span><span class="sxs-lookup"><span data-stu-id="963fc-289">The best way to indicate the importance of an issue is to vote (thumbs-up) for that issue on GitHub.</span></span> <span data-ttu-id="963fc-290">此資料接著會饋送至下一版的[規劃](xref:core/what-is-new/release_planning)程式。</span><span class="sxs-lookup"><span data-stu-id="963fc-290">This data will then feed into the [planning process](xref:core/what-is-new/release_planning) for the next release.</span></span>
