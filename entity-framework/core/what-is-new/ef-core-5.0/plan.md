---
title: 規劃 Entity Framework Core 5。0
author: ajcvickers
ms.date: 01/14/2020
uid: core/what-is-new/ef-core-5.0/plan.md
ms.openlocfilehash: c5b7300c61c2f668b6f9393ae51bf9ebddf330a7
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417873"
---
# <a name="plan-for-entity-framework-core-50"></a><span data-ttu-id="bd8af-102">規劃 Entity Framework Core 5。0</span><span class="sxs-lookup"><span data-stu-id="bd8af-102">Plan for Entity Framework Core 5.0</span></span>

<span data-ttu-id="bd8af-103">如[規劃](../release-planning.md)程式中所述，我們已將專案關係人的輸入收集到 EF Core 5.0 版本的暫時計畫。</span><span class="sxs-lookup"><span data-stu-id="bd8af-103">As described in the [planning process](../release-planning.md), we have gathered input from stakeholders into a tentative plan for the EF Core 5.0 release.</span></span>

> [!IMPORTANT] 
> <span data-ttu-id="bd8af-104">此計畫仍然是進行中的工作。</span><span class="sxs-lookup"><span data-stu-id="bd8af-104">This plan is still a work-in-progress.</span></span> <span data-ttu-id="bd8af-105">這裡沒有任何承諾。</span><span class="sxs-lookup"><span data-stu-id="bd8af-105">Nothing here is a commitment.</span></span> <span data-ttu-id="bd8af-106">此計畫是一個起點，會隨著我們深入瞭解而演變。</span><span class="sxs-lookup"><span data-stu-id="bd8af-106">This plan is a starting point that will evolve as we learn more.</span></span> <span data-ttu-id="bd8af-107">目前未計畫5.0 的某些專案可能會提取。</span><span class="sxs-lookup"><span data-stu-id="bd8af-107">Some things not currently planned for 5.0 may get pulled in.</span></span> <span data-ttu-id="bd8af-108">目前規劃5.0 的某些專案可能會一度。</span><span class="sxs-lookup"><span data-stu-id="bd8af-108">Some things currently planned for 5.0 may get punted out.</span></span>

### <a name="version-number-and-release-date"></a><span data-ttu-id="bd8af-109">版本號碼和發行日期。</span><span class="sxs-lookup"><span data-stu-id="bd8af-109">Version number and release date.</span></span>

<span data-ttu-id="bd8af-110">EF Core 5.0 目前排定與[.net 5.0](https://devblogs.microsoft.com/dotnet/introducing-net-5/)同時發行。</span><span class="sxs-lookup"><span data-stu-id="bd8af-110">EF Core 5.0 is currently scheduled for release at [the same time as .NET 5.0](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span> <span data-ttu-id="bd8af-111">已選擇版本 "5.0" 以配合 .NET 5.0。</span><span class="sxs-lookup"><span data-stu-id="bd8af-111">The version "5.0" was chosen to align with .NET 5.0.</span></span>

### <a name="supported-platforms"></a><span data-ttu-id="bd8af-112">支援的平台</span><span class="sxs-lookup"><span data-stu-id="bd8af-112">Supported platforms</span></span>

<span data-ttu-id="bd8af-113">EF Core 5.0 計畫在任何 .NET 5.0 平臺上執行，其以[這些平臺與 .Net Core 的交集為](https://devblogs.microsoft.com/dotnet/introducing-net-5/)基礎。</span><span class="sxs-lookup"><span data-stu-id="bd8af-113">EF Core 5.0 is planned to run on any .NET 5.0 platform based on the [convergence of these platforms to .NET Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span> <span data-ttu-id="bd8af-114">這代表 .NET Standard 的意義，而實際使用的 TFM 仍然是 TBD。</span><span class="sxs-lookup"><span data-stu-id="bd8af-114">What this means in terms of .NET Standard and the actual TFM used is still TBD.</span></span>

<span data-ttu-id="bd8af-115">EF Core 5.0 不會在 .NET Framework 上執行。</span><span class="sxs-lookup"><span data-stu-id="bd8af-115">EF Core 5.0 will not run on .NET Framework.</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="bd8af-116">重大變更</span><span class="sxs-lookup"><span data-stu-id="bd8af-116">Breaking changes</span></span>

<span data-ttu-id="bd8af-117">EF Core 5.0 將包含一些重大變更，但它們的嚴重程度比 EF Core 3.0 的情況低。</span><span class="sxs-lookup"><span data-stu-id="bd8af-117">EF Core 5.0 will contain some breaking changes, but these will be much less severe than was the case for EF Core 3.0.</span></span> <span data-ttu-id="bd8af-118">我們的目標是讓大部分的應用程式都能在不中斷的情況下進行更新。</span><span class="sxs-lookup"><span data-stu-id="bd8af-118">Our goal is to allow the vast majority of applications to update without breaking.</span></span>

<span data-ttu-id="bd8af-119">資料庫提供者應該會有一些重大變更，特別是關於 TPT 支援。</span><span class="sxs-lookup"><span data-stu-id="bd8af-119">It is expected that there will be some breaking changes for database providers, especially around TPT support.</span></span> <span data-ttu-id="bd8af-120">不過，我們預期更新5.0 提供者的工作將會小於更新3.0 所需的時間。</span><span class="sxs-lookup"><span data-stu-id="bd8af-120">However, we expect the work to update a provider for 5.0 will be less than was required to update for 3.0.</span></span>

## <a name="themes"></a><span data-ttu-id="bd8af-121">主題</span><span class="sxs-lookup"><span data-stu-id="bd8af-121">Themes</span></span>

<span data-ttu-id="bd8af-122">我們已解壓縮幾個主要區域或主題，這會構成 EF Core 5.0 中大型投資的基礎。</span><span class="sxs-lookup"><span data-stu-id="bd8af-122">We have extracted a few major areas or themes which will form the basis for the large investments in EF Core 5.0.</span></span>

## <a name="many-to-many-navigation-properties-aka-skip-navigations"></a><span data-ttu-id="bd8af-123">多對多導覽屬性（即「略過流覽」）</span><span class="sxs-lookup"><span data-stu-id="bd8af-123">Many-to-many navigation properties (a.k.a "skip navigations")</span></span>

<span data-ttu-id="bd8af-124">潛在客戶開發人員： @smitpatel 和 @AndriySvyryd</span><span class="sxs-lookup"><span data-stu-id="bd8af-124">Lead developers: @smitpatel and @AndriySvyryd</span></span>

<span data-ttu-id="bd8af-125">由[#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)追蹤</span><span class="sxs-lookup"><span data-stu-id="bd8af-125">Tracked by [#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)</span></span>

<span data-ttu-id="bd8af-126">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="bd8af-126">T-shirt size: L</span></span>

<span data-ttu-id="bd8af-127">狀態：進行中</span><span class="sxs-lookup"><span data-stu-id="bd8af-127">Status: In-progress</span></span>

<span data-ttu-id="bd8af-128">「多對多」是 GitHub 待處理專案上[最常要求的功能](https://github.com/aspnet/EntityFrameworkCore/issues/1368)（~ 407 投票）。</span><span class="sxs-lookup"><span data-stu-id="bd8af-128">Many-to-many is the [most requested feature](https://github.com/aspnet/EntityFrameworkCore/issues/1368) (~407 votes) on the GitHub backlog.</span></span>

<span data-ttu-id="bd8af-129">整體支援多對多關聯性會以[#10508](https://github.com/aspnet/EntityFrameworkCore/issues/10508)的方式進行追蹤。</span><span class="sxs-lookup"><span data-stu-id="bd8af-129">Support for many-to-many relationships in their entirety is tracked as [#10508](https://github.com/aspnet/EntityFrameworkCore/issues/10508).</span></span> <span data-ttu-id="bd8af-130">這可分為三個主要區域：</span><span class="sxs-lookup"><span data-stu-id="bd8af-130">This can be broken down into three major areas:</span></span>

* <span data-ttu-id="bd8af-131">略過導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="bd8af-131">Skip navigation properties.</span></span> <span data-ttu-id="bd8af-132">這些可讓模型用於查詢等，而不需要參考基礎聯結資料表實體。</span><span class="sxs-lookup"><span data-stu-id="bd8af-132">These allow the model to be used for queries, etc. without reference to the underlying join table entity.</span></span> <span data-ttu-id="bd8af-133">（[#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)）</span><span class="sxs-lookup"><span data-stu-id="bd8af-133">([#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003))</span></span>
* <span data-ttu-id="bd8af-134">屬性包實體類型。</span><span class="sxs-lookup"><span data-stu-id="bd8af-134">Property-bag entity types.</span></span> <span data-ttu-id="bd8af-135">這些專案允許將標準 CLR 型別（例如 `Dictionary`）用於實體實例，因此每個實體型別都不需要明確的 CLR 型別。</span><span class="sxs-lookup"><span data-stu-id="bd8af-135">These allow a standard CLR type (e.g. `Dictionary`) to be used for entity instances such that an explicit CLR type is not needed for each entity type.</span></span> <span data-ttu-id="bd8af-136">（5.0 的 Stretch： [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914)）。</span><span class="sxs-lookup"><span data-stu-id="bd8af-136">(Stretch for 5.0: [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914).)</span></span>
* <span data-ttu-id="bd8af-137">方便設定多對多關聯性的便利。</span><span class="sxs-lookup"><span data-stu-id="bd8af-137">Sugar for easy configuration of many-to-many relationships.</span></span> <span data-ttu-id="bd8af-138">（5.0 的延展）。</span><span class="sxs-lookup"><span data-stu-id="bd8af-138">(Stretch for 5.0.)</span></span>

<span data-ttu-id="bd8af-139">我們相信，想要多對多支援的最重要封鎖程式，在商務邏輯（例如查詢）中無法使用「自然」關聯性，而不需要參考聯結資料表。</span><span class="sxs-lookup"><span data-stu-id="bd8af-139">We believe that the most significant blocker for those wanting many-to-many support is not being able to use the "natural" relationships, without referring to the join table, in business logic such as queries.</span></span> <span data-ttu-id="bd8af-140">聯結資料表實體類型可能仍然存在，但不應該取得商務邏輯的方式。</span><span class="sxs-lookup"><span data-stu-id="bd8af-140">The join table entity type may still exist, but it should not get in the way of business logic.</span></span> <span data-ttu-id="bd8af-141">這就是我們選擇解決 [略過 5.0] 的流覽屬性的原因。</span><span class="sxs-lookup"><span data-stu-id="bd8af-141">This is why we have chosen to tackle skip navigation properties for 5.0.</span></span>

<span data-ttu-id="bd8af-142">此時，多對多的其他部分會被視為 EF Core 5.0 的延展目標。</span><span class="sxs-lookup"><span data-stu-id="bd8af-142">At this time the other parts of many-to-many are being pursued as a stretch goal for EF Core 5.0.</span></span> <span data-ttu-id="bd8af-143">這表示它們目前不在計畫中5.0，但如果一切順利，我們希望能夠將其提取到。</span><span class="sxs-lookup"><span data-stu-id="bd8af-143">This means they are not currently in the plan for 5.0, but if things go well we hope to pull them in.</span></span>

## <a name="table-per-type-tpt-inheritance-mapping"></a><span data-ttu-id="bd8af-144">每一類型的資料表（TPT）繼承對應</span><span class="sxs-lookup"><span data-stu-id="bd8af-144">Table-per-type (TPT) inheritance mapping</span></span>

<span data-ttu-id="bd8af-145">首席開發人員： @AndriySvyryd</span><span class="sxs-lookup"><span data-stu-id="bd8af-145">Lead developer: @AndriySvyryd</span></span>

<span data-ttu-id="bd8af-146">由[#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266)追蹤</span><span class="sxs-lookup"><span data-stu-id="bd8af-146">Tracked by [#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266)</span></span>

<span data-ttu-id="bd8af-147">T 恤尺寸： XL</span><span class="sxs-lookup"><span data-stu-id="bd8af-147">T-shirt size: XL</span></span>

<span data-ttu-id="bd8af-148">狀態：未啟動</span><span class="sxs-lookup"><span data-stu-id="bd8af-148">Status: Not started</span></span>

<span data-ttu-id="bd8af-149">我們正在進行 TPT，因為這兩者都是高度要求的功能（~ 254 的投票; 第三個），因為它需要一些低層級的變更，我們覺得這適用于整體 .NET 5 計畫的基本本質。</span><span class="sxs-lookup"><span data-stu-id="bd8af-149">We're doing TPT because it is both a highly requested feature (~254 votes; 3rd overall) and because it requires some low-level changes that we feel are appropriate for the foundational nature of the overall .NET 5 plan.</span></span> <span data-ttu-id="bd8af-150">我們預期這會導致資料庫提供者的中斷性變更，但這應該比3.0 所需的變更少很多。</span><span class="sxs-lookup"><span data-stu-id="bd8af-150">We expect this to result in breaking changes for database providers, although these should be much less severe than the changes required for 3.0.</span></span>

## <a name="filtered-include"></a><span data-ttu-id="bd8af-151">篩選的包含</span><span class="sxs-lookup"><span data-stu-id="bd8af-151">Filtered Include</span></span>

<span data-ttu-id="bd8af-152">首席開發人員： @maumar</span><span class="sxs-lookup"><span data-stu-id="bd8af-152">Lead developer: @maumar</span></span>

<span data-ttu-id="bd8af-153">由[#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833)追蹤</span><span class="sxs-lookup"><span data-stu-id="bd8af-153">Tracked by [#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833)</span></span>

<span data-ttu-id="bd8af-154">T 恤尺寸： M</span><span class="sxs-lookup"><span data-stu-id="bd8af-154">T-shirt size: M</span></span>

<span data-ttu-id="bd8af-155">狀態：未啟動</span><span class="sxs-lookup"><span data-stu-id="bd8af-155">Status: Not started</span></span>

<span data-ttu-id="bd8af-156">篩選的「包含」是一種高度要求的功能（大約317的投票; 第二個整體），這不是大量的工作，因此我們認為會解除封鎖或更輕鬆地進行目前需要模型層級篩選或更複雜查詢的許多案例。</span><span class="sxs-lookup"><span data-stu-id="bd8af-156">Filtered Include is a highly-requested feature (~317 votes; 2nd overall) that isn't a huge amount of work, and that we believe will unblock or make easier many scenarios that currently require model-level filters or more complex queries.</span></span>

## <a name="rationalize-totable-toquery-toview-fromsql-etc"></a><span data-ttu-id="bd8af-157">合理化 ToTable、ToQuery、ToView、FromSql 等。</span><span class="sxs-lookup"><span data-stu-id="bd8af-157">Rationalize ToTable, ToQuery, ToView, FromSql, etc.</span></span>

<span data-ttu-id="bd8af-158">潛在客戶開發人員： @maumar 和 @smitpatel</span><span class="sxs-lookup"><span data-stu-id="bd8af-158">Lead developers: @maumar and @smitpatel</span></span>

<span data-ttu-id="bd8af-159">由[#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270)追蹤</span><span class="sxs-lookup"><span data-stu-id="bd8af-159">Tracked by [#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270)</span></span>

<span data-ttu-id="bd8af-160">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="bd8af-160">T-shirt size: L</span></span>

<span data-ttu-id="bd8af-161">狀態：未啟動</span><span class="sxs-lookup"><span data-stu-id="bd8af-161">Status: Not started</span></span>

<span data-ttu-id="bd8af-162">我們已在舊版中進行了支援原始 SQL、無索引鍵類型和相關區域的進度。</span><span class="sxs-lookup"><span data-stu-id="bd8af-162">We have made progress in previous releases towards supporting raw SQL, keyless types, and related areas.</span></span> <span data-ttu-id="bd8af-163">不過，所有專案的整體運作方式都有差距和不一致的情況。</span><span class="sxs-lookup"><span data-stu-id="bd8af-163">However, there are both gaps and inconsistencies in the way everything works together as a whole.</span></span> <span data-ttu-id="bd8af-164">5\.0 的目標是要修正這些問題，並建立定義、遷移和使用不同類型實體及其相關聯查詢和資料庫成品的絕佳體驗。</span><span class="sxs-lookup"><span data-stu-id="bd8af-164">The goal for 5.0 is to fix these and create a good experience for defining, migrating, and using different types of entities and their associated queries and database artifacts.</span></span> <span data-ttu-id="bd8af-165">這也可能牽涉到已編譯查詢 API 的更新。</span><span class="sxs-lookup"><span data-stu-id="bd8af-165">This may also involve updates to the compiled query API.</span></span>

<span data-ttu-id="bd8af-166">請注意，這個專案可能會導致某些應用層級的中斷性變更，因為我們目前擁有的一些功能過於寬鬆，所以可能很快就會導致使用者 pits 失敗。</span><span class="sxs-lookup"><span data-stu-id="bd8af-166">Note that this item may result in some application-level breaking changes since some of the functionality we currently have is too permissive such that it can quickly lead people into pits of failure.</span></span> <span data-ttu-id="bd8af-167">我們可能會同時封鎖這部分功能，並提供有關該怎麼做的指引。</span><span class="sxs-lookup"><span data-stu-id="bd8af-167">We will likely end up blocking some of this functionality together with guidance on what to do instead.</span></span>

## <a name="general-query-enhancements"></a><span data-ttu-id="bd8af-168">一般查詢增強功能</span><span class="sxs-lookup"><span data-stu-id="bd8af-168">General query enhancements</span></span>

<span data-ttu-id="bd8af-169">潛在客戶開發人員： @smitpatel 和 @maumar</span><span class="sxs-lookup"><span data-stu-id="bd8af-169">Lead developers: @smitpatel and @maumar</span></span>

<span data-ttu-id="bd8af-170">由[5.0 里程碑中標示為 `area-query` 的問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)所追蹤</span><span class="sxs-lookup"><span data-stu-id="bd8af-170">Tracked by [issues labeled with `area-query` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="bd8af-171">T 恤尺寸： XL</span><span class="sxs-lookup"><span data-stu-id="bd8af-171">T-shirt size: XL</span></span>

<span data-ttu-id="bd8af-172">狀態：進行中</span><span class="sxs-lookup"><span data-stu-id="bd8af-172">Status: In-progress</span></span>

<span data-ttu-id="bd8af-173">已針對 EF Core 3.0 廣泛重寫查詢轉譯程式碼。</span><span class="sxs-lookup"><span data-stu-id="bd8af-173">The query translation code was extensively rewritten for EF Core 3.0.</span></span> <span data-ttu-id="bd8af-174">這種情況下，查詢程式碼通常會有更健全的狀態。</span><span class="sxs-lookup"><span data-stu-id="bd8af-174">The query code is generally in a much more robust state because of this.</span></span> <span data-ttu-id="bd8af-175">針對5.0，我們不打算在支援 TPT 和略過導覽屬性所需的範圍內進行主要查詢變更。</span><span class="sxs-lookup"><span data-stu-id="bd8af-175">For 5.0 we aren't planning on making major query changes, outside those needed to support TPT and skip navigation properties.</span></span> <span data-ttu-id="bd8af-176">不過，仍然需要進行一些工作，以修正3.0 檢修中剩餘的技術債務。</span><span class="sxs-lookup"><span data-stu-id="bd8af-176">However, there is still significant work needed to fix some technical debt left over from the 3.0 overhaul.</span></span> <span data-ttu-id="bd8af-177">我們也計畫修正許多 bug，並執行小型的增強功能，以進一步改善整體的查詢體驗。</span><span class="sxs-lookup"><span data-stu-id="bd8af-177">We also plan to fix many bugs and implement small enhancements to further improve the overall query experience.</span></span>

## <a name="migrations-and-deployment-experience"></a><span data-ttu-id="bd8af-178">遷移與部署體驗</span><span class="sxs-lookup"><span data-stu-id="bd8af-178">Migrations and deployment experience</span></span>

<span data-ttu-id="bd8af-179">潛在客戶開發人員： @bricelam</span><span class="sxs-lookup"><span data-stu-id="bd8af-179">Lead developers: @bricelam</span></span>

<span data-ttu-id="bd8af-180">由[#19587](https://github.com/dotnet/efcore/issues/19587)追蹤</span><span class="sxs-lookup"><span data-stu-id="bd8af-180">Tracked by [#19587](https://github.com/dotnet/efcore/issues/19587)</span></span>

<span data-ttu-id="bd8af-181">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="bd8af-181">T-shirt size: L</span></span>

<span data-ttu-id="bd8af-182">狀態：進行中</span><span class="sxs-lookup"><span data-stu-id="bd8af-182">Status: In-progress</span></span>

<span data-ttu-id="bd8af-183">目前，許多開發人員會在應用程式啟動時遷移其資料庫。</span><span class="sxs-lookup"><span data-stu-id="bd8af-183">Currently, many developers migrate their databases at application startup time.</span></span> <span data-ttu-id="bd8af-184">這很簡單，但不建議這麼做，因為：</span><span class="sxs-lookup"><span data-stu-id="bd8af-184">This is easy but is not recommended because:</span></span>

* <span data-ttu-id="bd8af-185">多個執行緒/進程/伺服器可能會嘗試同時遷移資料庫</span><span class="sxs-lookup"><span data-stu-id="bd8af-185">Multiple threads/processes/servers may attempt to migrate the database concurrently</span></span>
* <span data-ttu-id="bd8af-186">應用程式可能會在發生這種情況時，嘗試存取不一致的狀態</span><span class="sxs-lookup"><span data-stu-id="bd8af-186">Applications may try to access inconsistent state while this is happening</span></span>
* <span data-ttu-id="bd8af-187">通常不應授與修改架構的資料庫許可權以執行應用程式</span><span class="sxs-lookup"><span data-stu-id="bd8af-187">Usually the database permissions to modify the schema should not be granted for application execution</span></span>
* <span data-ttu-id="bd8af-188">如果發生錯誤，很難還原為「清除」狀態</span><span class="sxs-lookup"><span data-stu-id="bd8af-188">It's hard to revert back to a clean state if something goes wrong</span></span>

<span data-ttu-id="bd8af-189">我們想要在此提供更好的體驗，讓您可以輕鬆地在部署時遷移資料庫。</span><span class="sxs-lookup"><span data-stu-id="bd8af-189">We want to deliver a better experience here that allows an easy way to migrate the database at deployment time.</span></span> <span data-ttu-id="bd8af-190">這應該：</span><span class="sxs-lookup"><span data-stu-id="bd8af-190">This should:</span></span>

* <span data-ttu-id="bd8af-191">在 Linux、Mac 和 Windows 上工作</span><span class="sxs-lookup"><span data-stu-id="bd8af-191">Work on Linux, Mac, and Windows</span></span>
* <span data-ttu-id="bd8af-192">在命令列上是不錯的體驗</span><span class="sxs-lookup"><span data-stu-id="bd8af-192">Be a good experience on the command line</span></span>
* <span data-ttu-id="bd8af-193">容器的支援案例</span><span class="sxs-lookup"><span data-stu-id="bd8af-193">Support scenarios with containers</span></span>
* <span data-ttu-id="bd8af-194">使用常用的真實世界部署工具/流程</span><span class="sxs-lookup"><span data-stu-id="bd8af-194">Work with commonly used real-world deployment tools/flows</span></span>
* <span data-ttu-id="bd8af-195">至少整合到 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd8af-195">Integrate into at least Visual Studio</span></span>

<span data-ttu-id="bd8af-196">結果可能是 EF Core 中的許多小改良（例如，SQLite 上較佳的遷移），並提供與其他小組的指引和更長期的共同作業，以改善超越 EF 的端對端體驗。</span><span class="sxs-lookup"><span data-stu-id="bd8af-196">The result is likely to be many small improvements in EF Core (for example, better Migrations on SQLite), together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

## <a name="ef-core-platforms-experience"></a><span data-ttu-id="bd8af-197">EF Core 平臺體驗</span><span class="sxs-lookup"><span data-stu-id="bd8af-197">EF Core platforms experience</span></span> 

<span data-ttu-id="bd8af-198">潛在客戶開發人員： @roji 和 @bricelam</span><span class="sxs-lookup"><span data-stu-id="bd8af-198">Lead developers: @roji and @bricelam</span></span>

<span data-ttu-id="bd8af-199">由[#19588](https://github.com/dotnet/efcore/issues/19588)追蹤</span><span class="sxs-lookup"><span data-stu-id="bd8af-199">Tracked by [#19588](https://github.com/dotnet/efcore/issues/19588)</span></span>

<span data-ttu-id="bd8af-200">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="bd8af-200">T-shirt size: L</span></span>

<span data-ttu-id="bd8af-201">狀態：未啟動</span><span class="sxs-lookup"><span data-stu-id="bd8af-201">Status: Not started</span></span>

<span data-ttu-id="bd8af-202">我們有在傳統的 MVC web 應用程式中使用 EF Core 的絕佳指引。</span><span class="sxs-lookup"><span data-stu-id="bd8af-202">We have good guidance for using EF Core in traditional MVC-like web applications.</span></span> <span data-ttu-id="bd8af-203">其他平臺和應用程式模型的指引可能遺失或過期。</span><span class="sxs-lookup"><span data-stu-id="bd8af-203">Guidance for other platforms and application models is either missing or out-of-date.</span></span> <span data-ttu-id="bd8af-204">針對 EF Core 5.0，我們計畫調查、改善及記載搭配 EF Core 使用的體驗：</span><span class="sxs-lookup"><span data-stu-id="bd8af-204">For EF Core 5.0, we plan to investigate, improve, and document the experience of using EF Core with:</span></span>

* <span data-ttu-id="bd8af-205">Blazor</span><span class="sxs-lookup"><span data-stu-id="bd8af-205">Blazor</span></span>
* <span data-ttu-id="bd8af-206">Xamarin，包括使用 AOT/連結器案例</span><span class="sxs-lookup"><span data-stu-id="bd8af-206">Xamarin, including using the AOT/linker story</span></span>
* <span data-ttu-id="bd8af-207">WinForms/WPF/WinUI，可能還有其他 U.I。</span><span class="sxs-lookup"><span data-stu-id="bd8af-207">WinForms/WPF/WinUI and possibly other U.I.</span></span> <span data-ttu-id="bd8af-208">frameworks</span><span class="sxs-lookup"><span data-stu-id="bd8af-208">frameworks</span></span>

<span data-ttu-id="bd8af-209">這可能是 EF Core 中的許多小改進，以及與其他小組的指引和更長期的共同作業，以改善超越 EF 的端對端體驗。</span><span class="sxs-lookup"><span data-stu-id="bd8af-209">This is likely to be many small improvements in EF Core, together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

<span data-ttu-id="bd8af-210">我們打算查看的特定領域包括：</span><span class="sxs-lookup"><span data-stu-id="bd8af-210">Specific areas we plan to look at are:</span></span>

* <span data-ttu-id="bd8af-211">部署，包括使用 EF 工具（例如）進行遷移的經驗</span><span class="sxs-lookup"><span data-stu-id="bd8af-211">Deployment, including the experience for using EF tooling such as for Migrations</span></span>
* <span data-ttu-id="bd8af-212">應用程式模型，包括 Xamarin 和 Blazor，還有其他可能</span><span class="sxs-lookup"><span data-stu-id="bd8af-212">Application models, including Xamarin and Blazor, and probably others</span></span>
* <span data-ttu-id="bd8af-213">SQLite 體驗，包括空間體驗和資料表重建</span><span class="sxs-lookup"><span data-stu-id="bd8af-213">SQLite experiences, including the spatial experience and table rebuilds</span></span>
* <span data-ttu-id="bd8af-214">AOT 和連結體驗</span><span class="sxs-lookup"><span data-stu-id="bd8af-214">AOT and linking experiences</span></span>
* <span data-ttu-id="bd8af-215">診斷整合，包括效能計數器</span><span class="sxs-lookup"><span data-stu-id="bd8af-215">Diagnostics integration, including perf counters</span></span>

## <a name="performance"></a><span data-ttu-id="bd8af-216">效能</span><span class="sxs-lookup"><span data-stu-id="bd8af-216">Performance</span></span>

<span data-ttu-id="bd8af-217">首席開發人員： @roji</span><span class="sxs-lookup"><span data-stu-id="bd8af-217">Lead developer: @roji</span></span>

<span data-ttu-id="bd8af-218">由[5.0 里程碑中標示為 `area-perf` 的問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)所追蹤</span><span class="sxs-lookup"><span data-stu-id="bd8af-218">Tracked by [issues labeled with `area-perf` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="bd8af-219">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="bd8af-219">T-shirt size: L</span></span>

<span data-ttu-id="bd8af-220">狀態：進行中</span><span class="sxs-lookup"><span data-stu-id="bd8af-220">Status: In-progress</span></span>

<span data-ttu-id="bd8af-221">針對 EF Core，我們計畫改善我們的效能基準測試套件，並對執行時間進行有引導的效能改進。</span><span class="sxs-lookup"><span data-stu-id="bd8af-221">For EF Core, we plan to improve our suite of performance benchmarks and make directed performance improvements to the runtime.</span></span> <span data-ttu-id="bd8af-222">此外，我們計畫在3.0 發行週期內完成新的 ADO.NET 批次處理 API，這是原型。</span><span class="sxs-lookup"><span data-stu-id="bd8af-222">In addition, we plan to complete the new ADO.NET batching API which was prototyped during the 3.0 release cycle.</span></span> <span data-ttu-id="bd8af-223">此外，在 ADO.NET 層，我們也為 Npgsql 提供者規劃了額外的效能改進。</span><span class="sxs-lookup"><span data-stu-id="bd8af-223">Also at the ADO.NET layer, we plan additional performance improvements to the Npgsql provider.</span></span>

<span data-ttu-id="bd8af-224">在這項工作中，我們也計畫在適當的情況下新增 ADO.NET/EF 核心效能計數器和其他診斷。</span><span class="sxs-lookup"><span data-stu-id="bd8af-224">As part of this work we also plan to add ADO.NET/EF Core performance counters and other diagnostics as appropriate.</span></span>

## <a name="architecturalcontributor-documentation"></a><span data-ttu-id="bd8af-225">架構/參與者檔</span><span class="sxs-lookup"><span data-stu-id="bd8af-225">Architectural/contributor documentation</span></span>

<span data-ttu-id="bd8af-226">潛在客戶文檔： @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="bd8af-226">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="bd8af-227">由[#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)追蹤</span><span class="sxs-lookup"><span data-stu-id="bd8af-227">Tracked by [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)</span></span>

<span data-ttu-id="bd8af-228">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="bd8af-228">T-shirt size: L</span></span>

<span data-ttu-id="bd8af-229">狀態：未啟動</span><span class="sxs-lookup"><span data-stu-id="bd8af-229">Status: Not started</span></span>

<span data-ttu-id="bd8af-230">這裡的概念是要讓您更輕鬆地瞭解 EF Core 內部的狀況。</span><span class="sxs-lookup"><span data-stu-id="bd8af-230">The idea here is to make it easier to understand what is going on in the internals of EF Core.</span></span> <span data-ttu-id="bd8af-231">這對任何使用 EF Core 的人來說都很有用，但主要動機是讓外部人員更容易：</span><span class="sxs-lookup"><span data-stu-id="bd8af-231">This can be useful to anyone using EF Core, but the primary motivation is to make it easier for external people to:</span></span>

* <span data-ttu-id="bd8af-232">參與 EF Core 程式碼</span><span class="sxs-lookup"><span data-stu-id="bd8af-232">Contribute to the EF Core code</span></span>
* <span data-ttu-id="bd8af-233">建立資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="bd8af-233">Create database providers</span></span>
* <span data-ttu-id="bd8af-234">建立其他擴充功能</span><span class="sxs-lookup"><span data-stu-id="bd8af-234">Build other extensions</span></span>

## <a name="microsoftdatasqlite-documentation"></a><span data-ttu-id="bd8af-235">Microsoft. Sqlite 檔</span><span class="sxs-lookup"><span data-stu-id="bd8af-235">Microsoft.Data.Sqlite documentation</span></span>

<span data-ttu-id="bd8af-236">潛在客戶文檔： @bricelam</span><span class="sxs-lookup"><span data-stu-id="bd8af-236">Lead documenter: @bricelam</span></span>

<span data-ttu-id="bd8af-237">由[#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)追蹤</span><span class="sxs-lookup"><span data-stu-id="bd8af-237">Tracked by [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)</span></span>

<span data-ttu-id="bd8af-238">T 恤尺寸： M</span><span class="sxs-lookup"><span data-stu-id="bd8af-238">T-shirt size: M</span></span>

<span data-ttu-id="bd8af-239">狀態：已完成。</span><span class="sxs-lookup"><span data-stu-id="bd8af-239">Status: Completed.</span></span> <span data-ttu-id="bd8af-240">新的檔已[上線于 Microsoft 檔網站](https://docs.microsoft.com/dotnet/standard/data/sqlite/?tabs=netcore-cli)。</span><span class="sxs-lookup"><span data-stu-id="bd8af-240">The new documentation is [live on the Microsoft docs site](https://docs.microsoft.com/dotnet/standard/data/sqlite/?tabs=netcore-cli).</span></span>

<span data-ttu-id="bd8af-241">EF 小組也擁有 ADO.NET 提供者。</span><span class="sxs-lookup"><span data-stu-id="bd8af-241">The EF Team also owns the Microsoft.Data.Sqlite ADO.NET provider.</span></span> <span data-ttu-id="bd8af-242">我們計畫將此提供者完整記錄為5.0 版本的一部分。</span><span class="sxs-lookup"><span data-stu-id="bd8af-242">We plan to fully document this provider as part of the 5.0 release.</span></span>

## <a name="general-documentation"></a><span data-ttu-id="bd8af-243">一般檔</span><span class="sxs-lookup"><span data-stu-id="bd8af-243">General documentation</span></span>

<span data-ttu-id="bd8af-244">潛在客戶文檔： @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="bd8af-244">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="bd8af-245">[5.0 里程碑的](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)檔存放庫中的問題追蹤</span><span class="sxs-lookup"><span data-stu-id="bd8af-245">Tracked by [issues in the docs repo in the 5.0 milestone](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="bd8af-246">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="bd8af-246">T-shirt size: L</span></span>

<span data-ttu-id="bd8af-247">狀態：進行中</span><span class="sxs-lookup"><span data-stu-id="bd8af-247">Status: In-progress</span></span>

<span data-ttu-id="bd8af-248">我們已在更新3.0 和3.1 版本的檔。</span><span class="sxs-lookup"><span data-stu-id="bd8af-248">We are already in the process of updating documentation for the 3.0 and 3.1 releases.</span></span> <span data-ttu-id="bd8af-249">我們也在努力：</span><span class="sxs-lookup"><span data-stu-id="bd8af-249">We are also working on:</span></span>
  * <span data-ttu-id="bd8af-250">深入瞭解開始使用檔，使其更平易近人/更容易遵循</span><span class="sxs-lookup"><span data-stu-id="bd8af-250">An overhaul of the getting started docs to make them more approachable/easier to follow</span></span>
  * <span data-ttu-id="bd8af-251">重新組織檔，使其更容易尋找及新增交互參考</span><span class="sxs-lookup"><span data-stu-id="bd8af-251">Reorganization of docs to make things easier to find and to add cross-references</span></span>
  * <span data-ttu-id="bd8af-252">新增更多詳細資料和對現有檔的說明</span><span class="sxs-lookup"><span data-stu-id="bd8af-252">Adding more details and clarifications to existing docs</span></span>
  * <span data-ttu-id="bd8af-253">更新範例並新增更多範例</span><span class="sxs-lookup"><span data-stu-id="bd8af-253">Updating the samples and adding more examples</span></span>

## <a name="fixing-bugs"></a><span data-ttu-id="bd8af-254">修正 bug</span><span class="sxs-lookup"><span data-stu-id="bd8af-254">Fixing bugs</span></span>

<span data-ttu-id="bd8af-255">由[5.0 里程碑中標示為 `type-bug` 的問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)所追蹤</span><span class="sxs-lookup"><span data-stu-id="bd8af-255">Tracked by [issues labeled with `type-bug` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)</span></span>

<span data-ttu-id="bd8af-256">開發人員： @roji、@maumar、@bricelam、@smitpatel、@AndriySvyryd、@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="bd8af-256">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="bd8af-257">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="bd8af-257">T-shirt size: L</span></span>

<span data-ttu-id="bd8af-258">狀態：進行中</span><span class="sxs-lookup"><span data-stu-id="bd8af-258">Status: In-progress</span></span>

<span data-ttu-id="bd8af-259">在撰寫本文時，我們會在5.0 版本（已修正62）中修正 135 bug，但與上面的_一般查詢增強功能_相比，有相當大的重迭。</span><span class="sxs-lookup"><span data-stu-id="bd8af-259">At the time of writing, we have 135 bugs triaged to be fixed in the 5.0 release (with 62 already fixed), but there is significant overlap with the _General query enhancements_ section above.</span></span>

<span data-ttu-id="bd8af-260">傳入的速率（最後在里程碑中運作的問題）是3.0 發行期間每月大約23個問題。</span><span class="sxs-lookup"><span data-stu-id="bd8af-260">The incoming rate (issues that end up as work in a milestone) was about 23 issues per month over the course of the 3.0 release.</span></span> <span data-ttu-id="bd8af-261">並非所有這些都必須在5.0 中修正。</span><span class="sxs-lookup"><span data-stu-id="bd8af-261">Not all of these will need to be fixed in 5.0.</span></span> <span data-ttu-id="bd8af-262">我們打算在5.0 時間範圍內修正其他150問題，這是粗略的估計。</span><span class="sxs-lookup"><span data-stu-id="bd8af-262">As a rough estimate we plan to fix an additional 150 issues in the 5.0 time frame.</span></span>

## <a name="small-enhancements"></a><span data-ttu-id="bd8af-263">小型增強功能</span><span class="sxs-lookup"><span data-stu-id="bd8af-263">Small enhancements</span></span>

<span data-ttu-id="bd8af-264">由[5.0 里程碑中標示為 `type-enhancement` 的問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)所追蹤</span><span class="sxs-lookup"><span data-stu-id="bd8af-264">Tracked by [issues labeled with `type-enhancement` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)</span></span>

<span data-ttu-id="bd8af-265">開發人員： @roji、@maumar、@bricelam、@smitpatel、@AndriySvyryd、@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="bd8af-265">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="bd8af-266">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="bd8af-266">T-shirt size: L</span></span>

<span data-ttu-id="bd8af-267">狀態：進行中</span><span class="sxs-lookup"><span data-stu-id="bd8af-267">Status: In-progress</span></span>

<span data-ttu-id="bd8af-268">除了上面所述的更大功能之外，我們也針對5.0 排定了許多較小的改進，以修正「紙張切割」。</span><span class="sxs-lookup"><span data-stu-id="bd8af-268">In addition to the bigger features outlined above, we also have many smaller improvements scheduled for 5.0 to fix "paper-cuts".</span></span> <span data-ttu-id="bd8af-269">請注意，上述的許多增強功能也都涵蓋在上面所述的一般主題。</span><span class="sxs-lookup"><span data-stu-id="bd8af-269">Note that many of these enhancements are also covered by the more general themes outlined above.</span></span>

## <a name="below-the-line"></a><span data-ttu-id="bd8af-270">底下-行</span><span class="sxs-lookup"><span data-stu-id="bd8af-270">Below-the-line</span></span>

<span data-ttu-id="bd8af-271">由[標示為 `consider-for-next-release`的問題](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)所追蹤</span><span class="sxs-lookup"><span data-stu-id="bd8af-271">Tracked by [issues labeled with `consider-for-next-release`](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span></span>

<span data-ttu-id="bd8af-272">這些是目前**未**針對5.0 版本排程的錯誤修正和增強功能，但我們會根據上述工作的進度，查看是否為延展目標。</span><span class="sxs-lookup"><span data-stu-id="bd8af-272">These are bug fixes and enhancements that are **not** currently scheduled for the 5.0 release, but we will look at as stretch goals depending on the progress made on the work above.</span></span>

<span data-ttu-id="bd8af-273">此外，我們在規劃時，一律會考慮[最投票的問題](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc)。</span><span class="sxs-lookup"><span data-stu-id="bd8af-273">In addition, we always consider the [most voted issues](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) when planning.</span></span> <span data-ttu-id="bd8af-274">從版本中去除這些問題總是很麻煩，但我們對我們所擁有的資源需要有實際的計畫。</span><span class="sxs-lookup"><span data-stu-id="bd8af-274">Cutting any of these issues from a release is always painful, but we do need a realistic plan for the resources we have.</span></span>

## <a name="feedback"></a><span data-ttu-id="bd8af-275">意見反應</span><span class="sxs-lookup"><span data-stu-id="bd8af-275">Feedback</span></span>

<span data-ttu-id="bd8af-276">您對計劃的意見反應很重要。</span><span class="sxs-lookup"><span data-stu-id="bd8af-276">Your feedback on planning is important.</span></span> <span data-ttu-id="bd8af-277">若要指出問題的重要性，最佳方式是在 GitHub 上針對該問題投票 (大拇指)。</span><span class="sxs-lookup"><span data-stu-id="bd8af-277">The best way to indicate the importance of an issue is to vote (thumbs-up) for that issue on GitHub.</span></span> <span data-ttu-id="bd8af-278">此資料接著會饋送至下一版的[規劃](../release-planning.md)程式。</span><span class="sxs-lookup"><span data-stu-id="bd8af-278">This data will then feed into the [planning process](../release-planning.md) for the next release.</span></span>
