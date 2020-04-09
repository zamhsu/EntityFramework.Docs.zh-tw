---
title: 實體框架核心 5.0 規劃
author: ajcvickers
ms.date: 01/14/2020
uid: core/what-is-new/ef-core-5.0/plan.md
ms.openlocfilehash: 8b4ca32524869019c04d5a4d4d55967f68181cd7
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80136226"
---
# <a name="plan-for-entity-framework-core-50"></a><span data-ttu-id="eddca-102">實體框架核心 5.0 規劃</span><span class="sxs-lookup"><span data-stu-id="eddca-102">Plan for Entity Framework Core 5.0</span></span>

<span data-ttu-id="eddca-103">如[規劃過程中](../release-planning.md)所述,我們已經收集了利益相關者對 EF Core 5.0 版本的暫定計劃的投入。</span><span class="sxs-lookup"><span data-stu-id="eddca-103">As described in the [planning process](../release-planning.md), we have gathered input from stakeholders into a tentative plan for the EF Core 5.0 release.</span></span>

> [!IMPORTANT] 
> <span data-ttu-id="eddca-104">這個計劃仍在進行中。</span><span class="sxs-lookup"><span data-stu-id="eddca-104">This plan is still a work-in-progress.</span></span> <span data-ttu-id="eddca-105">這裡沒有什麼是承諾。</span><span class="sxs-lookup"><span data-stu-id="eddca-105">Nothing here is a commitment.</span></span> <span data-ttu-id="eddca-106">這個計劃是一個起點,隨著我們學習到更多,它將不斷演變。</span><span class="sxs-lookup"><span data-stu-id="eddca-106">This plan is a starting point that will evolve as we learn more.</span></span> <span data-ttu-id="eddca-107">一些目前未計劃為 5.0 的事情可能會被拉進去。</span><span class="sxs-lookup"><span data-stu-id="eddca-107">Some things not currently planned for 5.0 may get pulled in.</span></span> <span data-ttu-id="eddca-108">目前計劃為 5.0 的一些事情可能會被罰出場。</span><span class="sxs-lookup"><span data-stu-id="eddca-108">Some things currently planned for 5.0 may get punted out.</span></span>

### <a name="version-number-and-release-date"></a><span data-ttu-id="eddca-109">版本號和發佈日期。</span><span class="sxs-lookup"><span data-stu-id="eddca-109">Version number and release date.</span></span>

<span data-ttu-id="eddca-110">EF Core 5.0 目前計劃與[.NET 5.0 同時](https://devblogs.microsoft.com/dotnet/introducing-net-5/)發佈。</span><span class="sxs-lookup"><span data-stu-id="eddca-110">EF Core 5.0 is currently scheduled for release at [the same time as .NET 5.0](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span> <span data-ttu-id="eddca-111">選擇版本"5.0"與 .NET 5.0 對齊。</span><span class="sxs-lookup"><span data-stu-id="eddca-111">The version "5.0" was chosen to align with .NET 5.0.</span></span>

### <a name="supported-platforms"></a><span data-ttu-id="eddca-112">支援的平台</span><span class="sxs-lookup"><span data-stu-id="eddca-112">Supported platforms</span></span>

<span data-ttu-id="eddca-113">EF Core 5.0 計劃基於[這些平臺與 .NET Core 的融合](https://devblogs.microsoft.com/dotnet/introducing-net-5/),在任何 .NET 5.0 平臺上運行。</span><span class="sxs-lookup"><span data-stu-id="eddca-113">EF Core 5.0 is planned to run on any .NET 5.0 platform based on the [convergence of these platforms to .NET Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span> <span data-ttu-id="eddca-114">就 .NET 標準和實際使用的 TFM 而言,這意味著什麼是 TBD。</span><span class="sxs-lookup"><span data-stu-id="eddca-114">What this means in terms of .NET Standard and the actual TFM used is still TBD.</span></span>

<span data-ttu-id="eddca-115">EF 核心 5.0 將不會在 .NET 框架上運行。</span><span class="sxs-lookup"><span data-stu-id="eddca-115">EF Core 5.0 will not run on .NET Framework.</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="eddca-116">重大變更</span><span class="sxs-lookup"><span data-stu-id="eddca-116">Breaking changes</span></span>

<span data-ttu-id="eddca-117">EF Core 5.0 將包含一些重大更改,但這些變化將比 EF Core 3.0 的嚴重程度要小得多。</span><span class="sxs-lookup"><span data-stu-id="eddca-117">EF Core 5.0 will contain some breaking changes, but these will be much less severe than was the case for EF Core 3.0.</span></span> <span data-ttu-id="eddca-118">我們的目標是允許絕大多數應用程式在不中斷的情況下進行更新。</span><span class="sxs-lookup"><span data-stu-id="eddca-118">Our goal is to allow the vast majority of applications to update without breaking.</span></span>

<span data-ttu-id="eddca-119">預計資料庫提供程式會有一些重大更改,尤其是在 TPT 支援方面。</span><span class="sxs-lookup"><span data-stu-id="eddca-119">It is expected that there will be some breaking changes for database providers, especially around TPT support.</span></span> <span data-ttu-id="eddca-120">但是,我們預計為 5.0 更新提供程式的工作將小於 3.0 更新所需的工作。</span><span class="sxs-lookup"><span data-stu-id="eddca-120">However, we expect the work to update a provider for 5.0 will be less than was required to update for 3.0.</span></span>

## <a name="themes"></a><span data-ttu-id="eddca-121">主題</span><span class="sxs-lookup"><span data-stu-id="eddca-121">Themes</span></span>

<span data-ttu-id="eddca-122">我們已經提取了幾個主要領域或主題,這些領域或主題將成為 EF Core 5.0 大型投資的基礎。</span><span class="sxs-lookup"><span data-stu-id="eddca-122">We have extracted a few major areas or themes which will form the basis for the large investments in EF Core 5.0.</span></span>

## <a name="many-to-many-navigation-properties-aka-skip-navigations"></a><span data-ttu-id="eddca-123">多對多導航屬性(即"跳過導航")</span><span class="sxs-lookup"><span data-stu-id="eddca-123">Many-to-many navigation properties (a.k.a "skip navigations")</span></span>

<span data-ttu-id="eddca-124">首席開發人員:@smitpatel和@AndriySvyryd</span><span class="sxs-lookup"><span data-stu-id="eddca-124">Lead developers: @smitpatel and @AndriySvyryd</span></span>

<span data-ttu-id="eddca-125">由[#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)追蹤</span><span class="sxs-lookup"><span data-stu-id="eddca-125">Tracked by [#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)</span></span>

<span data-ttu-id="eddca-126">T恤尺寸:L</span><span class="sxs-lookup"><span data-stu-id="eddca-126">T-shirt size: L</span></span>

<span data-ttu-id="eddca-127">狀態:正在進行</span><span class="sxs-lookup"><span data-stu-id="eddca-127">Status: In-progress</span></span>

<span data-ttu-id="eddca-128">多對多是 GitHub 積壓工作[中請求最多的功能](https://github.com/aspnet/EntityFrameworkCore/issues/1368)(+407 票)。</span><span class="sxs-lookup"><span data-stu-id="eddca-128">Many-to-many is the [most requested feature](https://github.com/aspnet/EntityFrameworkCore/issues/1368) (~407 votes) on the GitHub backlog.</span></span>

<span data-ttu-id="eddca-129">對多對多關係的支援被跟蹤為[#10508。](https://github.com/aspnet/EntityFrameworkCore/issues/10508)</span><span class="sxs-lookup"><span data-stu-id="eddca-129">Support for many-to-many relationships in their entirety is tracked as [#10508](https://github.com/aspnet/EntityFrameworkCore/issues/10508).</span></span> <span data-ttu-id="eddca-130">這可以分為三個主要領域:</span><span class="sxs-lookup"><span data-stu-id="eddca-130">This can be broken down into three major areas:</span></span>

* <span data-ttu-id="eddca-131">跳過導航屬性。</span><span class="sxs-lookup"><span data-stu-id="eddca-131">Skip navigation properties.</span></span> <span data-ttu-id="eddca-132">這些允許模型用於查詢等,而無需引用基礎聯接表實體。</span><span class="sxs-lookup"><span data-stu-id="eddca-132">These allow the model to be used for queries, etc. without reference to the underlying join table entity.</span></span> <span data-ttu-id="eddca-133">[(#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003))</span><span class="sxs-lookup"><span data-stu-id="eddca-133">([#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003))</span></span>
* <span data-ttu-id="eddca-134">屬性包實體類型。</span><span class="sxs-lookup"><span data-stu-id="eddca-134">Property-bag entity types.</span></span> <span data-ttu-id="eddca-135">這些允許將標準 CLR 類型`Dictionary`(例如 )用於實體實例,以便每個實體類型不需要顯式 CLR 類型。</span><span class="sxs-lookup"><span data-stu-id="eddca-135">These allow a standard CLR type (e.g. `Dictionary`) to be used for entity instances such that an explicit CLR type is not needed for each entity type.</span></span> <span data-ttu-id="eddca-136">(拉伸 5.0: [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914).)</span><span class="sxs-lookup"><span data-stu-id="eddca-136">(Stretch for 5.0: [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914).)</span></span>
* <span data-ttu-id="eddca-137">糖易於配置多對多關係。</span><span class="sxs-lookup"><span data-stu-id="eddca-137">Sugar for easy configuration of many-to-many relationships.</span></span> <span data-ttu-id="eddca-138">(拉伸為 5.0。</span><span class="sxs-lookup"><span data-stu-id="eddca-138">(Stretch for 5.0.)</span></span>

<span data-ttu-id="eddca-139">我們認為,對於那些需要多對多支援的人來說,最重要的阻止程式是不能在業務邏輯(如查詢)中使用"自然"關係,而不提及聯接表。</span><span class="sxs-lookup"><span data-stu-id="eddca-139">We believe that the most significant blocker for those wanting many-to-many support is not being able to use the "natural" relationships, without referring to the join table, in business logic such as queries.</span></span> <span data-ttu-id="eddca-140">聯接表實體類型可能仍然存在,但不應妨礙業務邏輯。</span><span class="sxs-lookup"><span data-stu-id="eddca-140">The join table entity type may still exist, but it should not get in the way of business logic.</span></span> <span data-ttu-id="eddca-141">這就是為什麼我們選擇解決 5.0 的跳過導航屬性的原因。</span><span class="sxs-lookup"><span data-stu-id="eddca-141">This is why we have chosen to tackle skip navigation properties for 5.0.</span></span>

<span data-ttu-id="eddca-142">此時,許多對多的其他部分正在被作為 EF Core 5.0 的延伸目標進行追求。</span><span class="sxs-lookup"><span data-stu-id="eddca-142">At this time the other parts of many-to-many are being pursued as a stretch goal for EF Core 5.0.</span></span> <span data-ttu-id="eddca-143">這意味著他們目前不在5.0的計劃中,但如果進展順利,我們希望能吸引他們。</span><span class="sxs-lookup"><span data-stu-id="eddca-143">This means they are not currently in the plan for 5.0, but if things go well we hope to pull them in.</span></span>

## <a name="table-per-type-tpt-inheritance-mapping"></a><span data-ttu-id="eddca-144">依類型表 (TPT) 繼承映射</span><span class="sxs-lookup"><span data-stu-id="eddca-144">Table-per-type (TPT) inheritance mapping</span></span>

<span data-ttu-id="eddca-145">首席開發人員:@AndriySvyryd</span><span class="sxs-lookup"><span data-stu-id="eddca-145">Lead developer: @AndriySvyryd</span></span>

<span data-ttu-id="eddca-146">由[#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266)追蹤</span><span class="sxs-lookup"><span data-stu-id="eddca-146">Tracked by [#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266)</span></span>

<span data-ttu-id="eddca-147">T恤尺寸:XL</span><span class="sxs-lookup"><span data-stu-id="eddca-147">T-shirt size: XL</span></span>

<span data-ttu-id="eddca-148">狀態:正在進行</span><span class="sxs-lookup"><span data-stu-id="eddca-148">Status: In-progress</span></span>

<span data-ttu-id="eddca-149">我們之所以做TPT,是因為它既是一個高度要求的功能(+254票;整體排名第三),而且它需要一些我們認為適合整體.NET 5計劃的基本性質的低級別更改。</span><span class="sxs-lookup"><span data-stu-id="eddca-149">We're doing TPT because it is both a highly requested feature (~254 votes; 3rd overall) and because it requires some low-level changes that we feel are appropriate for the foundational nature of the overall .NET 5 plan.</span></span> <span data-ttu-id="eddca-150">我們預計這將導致資料庫提供程式的突發更改,儘管這些更改應比3.0所需的更改嚴重得多。</span><span class="sxs-lookup"><span data-stu-id="eddca-150">We expect this to result in breaking changes for database providers, although these should be much less severe than the changes required for 3.0.</span></span>

## <a name="filtered-include"></a><span data-ttu-id="eddca-151">過濾器</span><span class="sxs-lookup"><span data-stu-id="eddca-151">Filtered Include</span></span>

<span data-ttu-id="eddca-152">首席開發人員:@maumar</span><span class="sxs-lookup"><span data-stu-id="eddca-152">Lead developer: @maumar</span></span>

<span data-ttu-id="eddca-153">由[#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833)追蹤</span><span class="sxs-lookup"><span data-stu-id="eddca-153">Tracked by [#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833)</span></span>

<span data-ttu-id="eddca-154">T恤尺寸:M</span><span class="sxs-lookup"><span data-stu-id="eddca-154">T-shirt size: M</span></span>

<span data-ttu-id="eddca-155">狀態:正在進行</span><span class="sxs-lookup"><span data-stu-id="eddca-155">Status: In-progress</span></span>

<span data-ttu-id="eddca-156">過濾包含是一個高度請求的功能(+317票;總體排名第二),它不是大量工作,我們相信這將取消阻止或使當前需要模型級篩選器或更複雜的查詢的許多方案更容易。</span><span class="sxs-lookup"><span data-stu-id="eddca-156">Filtered Include is a highly-requested feature (~317 votes; 2nd overall) that isn't a huge amount of work, and that we believe will unblock or make easier many scenarios that currently require model-level filters or more complex queries.</span></span>

## <a name="rationalize-totable-toquery-toview-fromsql-etc"></a><span data-ttu-id="eddca-157">合理化到表,查詢,查看,從Sql等。</span><span class="sxs-lookup"><span data-stu-id="eddca-157">Rationalize ToTable, ToQuery, ToView, FromSql, etc.</span></span>

<span data-ttu-id="eddca-158">首席開發人員:@maumar和@smitpatel</span><span class="sxs-lookup"><span data-stu-id="eddca-158">Lead developers: @maumar and @smitpatel</span></span>

<span data-ttu-id="eddca-159">由[#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270)追蹤</span><span class="sxs-lookup"><span data-stu-id="eddca-159">Tracked by [#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270)</span></span>

<span data-ttu-id="eddca-160">T恤尺寸:L</span><span class="sxs-lookup"><span data-stu-id="eddca-160">T-shirt size: L</span></span>

<span data-ttu-id="eddca-161">狀態:正在進行</span><span class="sxs-lookup"><span data-stu-id="eddca-161">Status: In-progress</span></span>

<span data-ttu-id="eddca-162">我們在以前的版本中在支援原始 SQL、無鑰匙類型和相關領域方面取得了進展。</span><span class="sxs-lookup"><span data-stu-id="eddca-162">We have made progress in previous releases towards supporting raw SQL, keyless types, and related areas.</span></span> <span data-ttu-id="eddca-163">然而,在一切工作方式上,一切事物都存在差距和不一致之處。</span><span class="sxs-lookup"><span data-stu-id="eddca-163">However, there are both gaps and inconsistencies in the way everything works together as a whole.</span></span> <span data-ttu-id="eddca-164">5.0 的目標是修復這些,並創建定義、遷移和使用不同類型的實體及其關聯查詢和資料庫專案的良好體驗。</span><span class="sxs-lookup"><span data-stu-id="eddca-164">The goal for 5.0 is to fix these and create a good experience for defining, migrating, and using different types of entities and their associated queries and database artifacts.</span></span> <span data-ttu-id="eddca-165">這還可能涉及對已編譯查詢 API 的更新。</span><span class="sxs-lookup"><span data-stu-id="eddca-165">This may also involve updates to the compiled query API.</span></span>

<span data-ttu-id="eddca-166">請注意,此專案可能會導致一些應用程式級的中斷更改,因為我們目前擁有的某些功能過於寬鬆,因此可以快速導致人們陷入失敗坑。</span><span class="sxs-lookup"><span data-stu-id="eddca-166">Note that this item may result in some application-level breaking changes since some of the functionality we currently have is too permissive such that it can quickly lead people into pits of failure.</span></span> <span data-ttu-id="eddca-167">我們最終可能會阻止某些功能,以及有關如何操作的指導。</span><span class="sxs-lookup"><span data-stu-id="eddca-167">We will likely end up blocking some of this functionality together with guidance on what to do instead.</span></span>

## <a name="general-query-enhancements"></a><span data-ttu-id="eddca-168">一般查詢增強功能</span><span class="sxs-lookup"><span data-stu-id="eddca-168">General query enhancements</span></span>

<span data-ttu-id="eddca-169">首席開發人員:@smitpatel和@maumar</span><span class="sxs-lookup"><span data-stu-id="eddca-169">Lead developers: @smitpatel and @maumar</span></span>

<span data-ttu-id="eddca-170">按[5.0`area-query`里程碑中標記的問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)進行追蹤</span><span class="sxs-lookup"><span data-stu-id="eddca-170">Tracked by [issues labeled with `area-query` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="eddca-171">T恤尺寸:XL</span><span class="sxs-lookup"><span data-stu-id="eddca-171">T-shirt size: XL</span></span>

<span data-ttu-id="eddca-172">狀態:正在進行</span><span class="sxs-lookup"><span data-stu-id="eddca-172">Status: In-progress</span></span>

<span data-ttu-id="eddca-173">為 EF Core 3.0 廣泛重寫了查詢轉換代碼。</span><span class="sxs-lookup"><span data-stu-id="eddca-173">The query translation code was extensively rewritten for EF Core 3.0.</span></span> <span data-ttu-id="eddca-174">因此,查詢代碼通常處於更加健壯的狀態。</span><span class="sxs-lookup"><span data-stu-id="eddca-174">The query code is generally in a much more robust state because of this.</span></span> <span data-ttu-id="eddca-175">對於 5.0,我們不打算在支援 TPT 和跳過導航屬性所需的外部進行重大查詢更改。</span><span class="sxs-lookup"><span data-stu-id="eddca-175">For 5.0 we aren't planning on making major query changes, outside those needed to support TPT and skip navigation properties.</span></span> <span data-ttu-id="eddca-176">然而,要修復3.0大修遺留下來的一些技術債務,仍有許多工作要做。</span><span class="sxs-lookup"><span data-stu-id="eddca-176">However, there is still significant work needed to fix some technical debt left over from the 3.0 overhaul.</span></span> <span data-ttu-id="eddca-177">我們還計劃修復許多 Bug 並實施小型增強功能,以進一步改善總體查詢體驗。</span><span class="sxs-lookup"><span data-stu-id="eddca-177">We also plan to fix many bugs and implement small enhancements to further improve the overall query experience.</span></span>

## <a name="migrations-and-deployment-experience"></a><span data-ttu-id="eddca-178">遷移及部署經驗</span><span class="sxs-lookup"><span data-stu-id="eddca-178">Migrations and deployment experience</span></span>

<span data-ttu-id="eddca-179">首席開發人員:@bricelam</span><span class="sxs-lookup"><span data-stu-id="eddca-179">Lead developers: @bricelam</span></span>

<span data-ttu-id="eddca-180">由[#19587](https://github.com/dotnet/efcore/issues/19587)追蹤</span><span class="sxs-lookup"><span data-stu-id="eddca-180">Tracked by [#19587](https://github.com/dotnet/efcore/issues/19587)</span></span>

<span data-ttu-id="eddca-181">T恤尺寸:L</span><span class="sxs-lookup"><span data-stu-id="eddca-181">T-shirt size: L</span></span>

<span data-ttu-id="eddca-182">狀態:正在進行</span><span class="sxs-lookup"><span data-stu-id="eddca-182">Status: In-progress</span></span>

<span data-ttu-id="eddca-183">目前,許多開發人員在應用程式啟動時遷移其資料庫。</span><span class="sxs-lookup"><span data-stu-id="eddca-183">Currently, many developers migrate their databases at application startup time.</span></span> <span data-ttu-id="eddca-184">這很容易,但不建議這樣做,因為:</span><span class="sxs-lookup"><span data-stu-id="eddca-184">This is easy but is not recommended because:</span></span>

* <span data-ttu-id="eddca-185">多個線程/行程/伺服器可能會嘗試同時移移資料庫</span><span class="sxs-lookup"><span data-stu-id="eddca-185">Multiple threads/processes/servers may attempt to migrate the database concurrently</span></span>
* <span data-ttu-id="eddca-186">應用程式可能會嘗試訪問不一致的狀態,而這種情況正在發生</span><span class="sxs-lookup"><span data-stu-id="eddca-186">Applications may try to access inconsistent state while this is happening</span></span>
* <span data-ttu-id="eddca-187">通常,不應授予資料庫許可權以修改架構以進行應用程式執行</span><span class="sxs-lookup"><span data-stu-id="eddca-187">Usually the database permissions to modify the schema should not be granted for application execution</span></span>
* <span data-ttu-id="eddca-188">如果出現問題,很難恢復到乾淨狀態</span><span class="sxs-lookup"><span data-stu-id="eddca-188">It's hard to revert back to a clean state if something goes wrong</span></span>

<span data-ttu-id="eddca-189">我們希望在這裡提供更好的體驗,從而在部署時輕鬆遷移資料庫。</span><span class="sxs-lookup"><span data-stu-id="eddca-189">We want to deliver a better experience here that allows an easy way to migrate the database at deployment time.</span></span> <span data-ttu-id="eddca-190">這應:</span><span class="sxs-lookup"><span data-stu-id="eddca-190">This should:</span></span>

* <span data-ttu-id="eddca-191">Linux、Mac 和 Windows 上工作</span><span class="sxs-lookup"><span data-stu-id="eddca-191">Work on Linux, Mac, and Windows</span></span>
* <span data-ttu-id="eddca-192">在命令列上做好體驗</span><span class="sxs-lookup"><span data-stu-id="eddca-192">Be a good experience on the command line</span></span>
* <span data-ttu-id="eddca-193">支援容器機制</span><span class="sxs-lookup"><span data-stu-id="eddca-193">Support scenarios with containers</span></span>
* <span data-ttu-id="eddca-194">使用常用的實際部署工具/串流</span><span class="sxs-lookup"><span data-stu-id="eddca-194">Work with commonly used real-world deployment tools/flows</span></span>
* <span data-ttu-id="eddca-195">至少整合到視覺化工作室</span><span class="sxs-lookup"><span data-stu-id="eddca-195">Integrate into at least Visual Studio</span></span>

<span data-ttu-id="eddca-196">其結果可能是 EF Core 的許多小改進(例如,在 SQLite 上更好的遷移),以及與其他團隊的指導和長期協作,以改善超越 EF 的端到端體驗。</span><span class="sxs-lookup"><span data-stu-id="eddca-196">The result is likely to be many small improvements in EF Core (for example, better Migrations on SQLite), together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

## <a name="ef-core-platforms-experience"></a><span data-ttu-id="eddca-197">EF 核心平台體驗</span><span class="sxs-lookup"><span data-stu-id="eddca-197">EF Core platforms experience</span></span> 

<span data-ttu-id="eddca-198">首席開發人員:@roji和@bricelam</span><span class="sxs-lookup"><span data-stu-id="eddca-198">Lead developers: @roji and @bricelam</span></span>

<span data-ttu-id="eddca-199">由[#19588](https://github.com/dotnet/efcore/issues/19588)追蹤</span><span class="sxs-lookup"><span data-stu-id="eddca-199">Tracked by [#19588](https://github.com/dotnet/efcore/issues/19588)</span></span>

<span data-ttu-id="eddca-200">T恤尺寸:L</span><span class="sxs-lookup"><span data-stu-id="eddca-200">T-shirt size: L</span></span>

<span data-ttu-id="eddca-201">狀態:未啟動</span><span class="sxs-lookup"><span data-stu-id="eddca-201">Status: Not started</span></span>

<span data-ttu-id="eddca-202">我們在傳統的 MVC 類似 Web 應用程式中使用 EF Core 有很好的指導。</span><span class="sxs-lookup"><span data-stu-id="eddca-202">We have good guidance for using EF Core in traditional MVC-like web applications.</span></span> <span data-ttu-id="eddca-203">其他平臺和應用模型的指導要麼丟失,要麼過期。</span><span class="sxs-lookup"><span data-stu-id="eddca-203">Guidance for other platforms and application models is either missing or out-of-date.</span></span> <span data-ttu-id="eddca-204">對於 EF Core 5.0,我們計劃調查、改進和記錄使用 EF Core 的經驗:</span><span class="sxs-lookup"><span data-stu-id="eddca-204">For EF Core 5.0, we plan to investigate, improve, and document the experience of using EF Core with:</span></span>

* <span data-ttu-id="eddca-205">Blazor</span><span class="sxs-lookup"><span data-stu-id="eddca-205">Blazor</span></span>
* <span data-ttu-id="eddca-206">Xamarin,包括使用 AOT/連結器故事</span><span class="sxs-lookup"><span data-stu-id="eddca-206">Xamarin, including using the AOT/linker story</span></span>
* <span data-ttu-id="eddca-207">WinForms/WPF/WinUI 可能還有其他 U.I.</span><span class="sxs-lookup"><span data-stu-id="eddca-207">WinForms/WPF/WinUI and possibly other U.I.</span></span> <span data-ttu-id="eddca-208">frameworks</span><span class="sxs-lookup"><span data-stu-id="eddca-208">frameworks</span></span>

<span data-ttu-id="eddca-209">這可能是 EF Core 的許多小改進,以及與其他團隊的指導和長期協作,以改善超越 EF 的端到端體驗。</span><span class="sxs-lookup"><span data-stu-id="eddca-209">This is likely to be many small improvements in EF Core, together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

<span data-ttu-id="eddca-210">我們計劃研究的具體領域包括:</span><span class="sxs-lookup"><span data-stu-id="eddca-210">Specific areas we plan to look at are:</span></span>

* <span data-ttu-id="eddca-211">部署,包括使用 EF 工具(如移轉)的經驗</span><span class="sxs-lookup"><span data-stu-id="eddca-211">Deployment, including the experience for using EF tooling such as for Migrations</span></span>
* <span data-ttu-id="eddca-212">應用程式模型,包括 Xamarin 和 Blazor,可能還有其他</span><span class="sxs-lookup"><span data-stu-id="eddca-212">Application models, including Xamarin and Blazor, and probably others</span></span>
* <span data-ttu-id="eddca-213">SQLite 體驗,包括空間體驗和桌面重建</span><span class="sxs-lookup"><span data-stu-id="eddca-213">SQLite experiences, including the spatial experience and table rebuilds</span></span>
* <span data-ttu-id="eddca-214">AOT 和連結體驗</span><span class="sxs-lookup"><span data-stu-id="eddca-214">AOT and linking experiences</span></span>
* <span data-ttu-id="eddca-215">診斷整合,包括 perf 計數器</span><span class="sxs-lookup"><span data-stu-id="eddca-215">Diagnostics integration, including perf counters</span></span>

## <a name="performance"></a><span data-ttu-id="eddca-216">效能</span><span class="sxs-lookup"><span data-stu-id="eddca-216">Performance</span></span>

<span data-ttu-id="eddca-217">首席開發人員:@roji</span><span class="sxs-lookup"><span data-stu-id="eddca-217">Lead developer: @roji</span></span>

<span data-ttu-id="eddca-218">按[5.0`area-perf`里程碑中標記的問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)進行追蹤</span><span class="sxs-lookup"><span data-stu-id="eddca-218">Tracked by [issues labeled with `area-perf` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="eddca-219">T恤尺寸:L</span><span class="sxs-lookup"><span data-stu-id="eddca-219">T-shirt size: L</span></span>

<span data-ttu-id="eddca-220">狀態:正在進行</span><span class="sxs-lookup"><span data-stu-id="eddca-220">Status: In-progress</span></span>

<span data-ttu-id="eddca-221">對於 EF Core,我們計劃改進我們的性能基準套件,並針對運行時進行定向性能改進。</span><span class="sxs-lookup"><span data-stu-id="eddca-221">For EF Core, we plan to improve our suite of performance benchmarks and make directed performance improvements to the runtime.</span></span> <span data-ttu-id="eddca-222">此外,我們還計劃完成在 3.0 發佈週期中原型設計的新 ADO.NET 批處理 API。</span><span class="sxs-lookup"><span data-stu-id="eddca-222">In addition, we plan to complete the new ADO.NET batching API which was prototyped during the 3.0 release cycle.</span></span> <span data-ttu-id="eddca-223">此外,在ADO.NET層,我們計劃對Npgsql提供程式進行其他性能改進。</span><span class="sxs-lookup"><span data-stu-id="eddca-223">Also at the ADO.NET layer, we plan additional performance improvements to the Npgsql provider.</span></span>

<span data-ttu-id="eddca-224">作為這項工作的一部分,我們還計劃根據需要添加ADO.NET/EF核心性能計數器和其他診斷。</span><span class="sxs-lookup"><span data-stu-id="eddca-224">As part of this work we also plan to add ADO.NET/EF Core performance counters and other diagnostics as appropriate.</span></span>

## <a name="architecturalcontributor-documentation"></a><span data-ttu-id="eddca-225">架構/貢獻者文件</span><span class="sxs-lookup"><span data-stu-id="eddca-225">Architectural/contributor documentation</span></span>

<span data-ttu-id="eddca-226">鉛文件記錄器:@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="eddca-226">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="eddca-227">由[#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)追蹤</span><span class="sxs-lookup"><span data-stu-id="eddca-227">Tracked by [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)</span></span>

<span data-ttu-id="eddca-228">T恤尺寸:L</span><span class="sxs-lookup"><span data-stu-id="eddca-228">T-shirt size: L</span></span>

<span data-ttu-id="eddca-229">狀態:正在進行</span><span class="sxs-lookup"><span data-stu-id="eddca-229">Status: In-progress</span></span>

<span data-ttu-id="eddca-230">這裡的想法是讓您更容易地瞭解 EF Core 的內部情況。</span><span class="sxs-lookup"><span data-stu-id="eddca-230">The idea here is to make it easier to understand what is going on in the internals of EF Core.</span></span> <span data-ttu-id="eddca-231">這對使用 EF Core 的任何人都很有用,但主要動機是讓外部人員更容易:</span><span class="sxs-lookup"><span data-stu-id="eddca-231">This can be useful to anyone using EF Core, but the primary motivation is to make it easier for external people to:</span></span>

* <span data-ttu-id="eddca-232">為 EF 核心代碼做出貢獻</span><span class="sxs-lookup"><span data-stu-id="eddca-232">Contribute to the EF Core code</span></span>
* <span data-ttu-id="eddca-233">建立資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="eddca-233">Create database providers</span></span>
* <span data-ttu-id="eddca-234">產生其他擴充</span><span class="sxs-lookup"><span data-stu-id="eddca-234">Build other extensions</span></span>

## <a name="microsoftdatasqlite-documentation"></a><span data-ttu-id="eddca-235">微軟.Data.Sqlite 文件</span><span class="sxs-lookup"><span data-stu-id="eddca-235">Microsoft.Data.Sqlite documentation</span></span>

<span data-ttu-id="eddca-236">鉛文件記錄器:@bricelam</span><span class="sxs-lookup"><span data-stu-id="eddca-236">Lead documenter: @bricelam</span></span>

<span data-ttu-id="eddca-237">由[#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)追蹤</span><span class="sxs-lookup"><span data-stu-id="eddca-237">Tracked by [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)</span></span>

<span data-ttu-id="eddca-238">T恤尺寸:M</span><span class="sxs-lookup"><span data-stu-id="eddca-238">T-shirt size: M</span></span>

<span data-ttu-id="eddca-239">狀態:已完成。</span><span class="sxs-lookup"><span data-stu-id="eddca-239">Status: Completed.</span></span> <span data-ttu-id="eddca-240">新的文檔[是微軟文檔網站上的](https://docs.microsoft.com/dotnet/standard/data/sqlite/?tabs=netcore-cli)即時。</span><span class="sxs-lookup"><span data-stu-id="eddca-240">The new documentation is [live on the Microsoft docs site](https://docs.microsoft.com/dotnet/standard/data/sqlite/?tabs=netcore-cli).</span></span>

<span data-ttu-id="eddca-241">EF 團隊還擁有 Microsoft.Data.Sqlite ADO.NET供應商。</span><span class="sxs-lookup"><span data-stu-id="eddca-241">The EF Team also owns the Microsoft.Data.Sqlite ADO.NET provider.</span></span> <span data-ttu-id="eddca-242">我們計劃作為 5.0 版本的一部分完整記錄此提供程式。</span><span class="sxs-lookup"><span data-stu-id="eddca-242">We plan to fully document this provider as part of the 5.0 release.</span></span>

## <a name="general-documentation"></a><span data-ttu-id="eddca-243">一般文件</span><span class="sxs-lookup"><span data-stu-id="eddca-243">General documentation</span></span>

<span data-ttu-id="eddca-244">鉛文件記錄器:@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="eddca-244">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="eddca-245">按[5.0 里程碑中文件回購中的問題](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)進行追蹤</span><span class="sxs-lookup"><span data-stu-id="eddca-245">Tracked by [issues in the docs repo in the 5.0 milestone](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="eddca-246">T恤尺寸:L</span><span class="sxs-lookup"><span data-stu-id="eddca-246">T-shirt size: L</span></span>

<span data-ttu-id="eddca-247">狀態:正在進行</span><span class="sxs-lookup"><span data-stu-id="eddca-247">Status: In-progress</span></span>

<span data-ttu-id="eddca-248">我們已經在更新 3.0 和 3.1 版本的文檔。</span><span class="sxs-lookup"><span data-stu-id="eddca-248">We are already in the process of updating documentation for the 3.0 and 3.1 releases.</span></span> <span data-ttu-id="eddca-249">我們還在致力於:</span><span class="sxs-lookup"><span data-stu-id="eddca-249">We are also working on:</span></span>
  * <span data-ttu-id="eddca-250">對入門文檔進行大修,使其更易近人/更易於遵循</span><span class="sxs-lookup"><span data-stu-id="eddca-250">An overhaul of the getting started docs to make them more approachable/easier to follow</span></span>
  * <span data-ttu-id="eddca-251">重整文件,使尋找更容易並新增交叉參考</span><span class="sxs-lookup"><span data-stu-id="eddca-251">Reorganization of docs to make things easier to find and to add cross-references</span></span>
  * <span data-ttu-id="eddca-252">新增額外資訊和說明</span><span class="sxs-lookup"><span data-stu-id="eddca-252">Adding more details and clarifications to existing docs</span></span>
  * <span data-ttu-id="eddca-253">更新範例並新增更多範例</span><span class="sxs-lookup"><span data-stu-id="eddca-253">Updating the samples and adding more examples</span></span>

## <a name="fixing-bugs"></a><span data-ttu-id="eddca-254">修復錯誤</span><span class="sxs-lookup"><span data-stu-id="eddca-254">Fixing bugs</span></span>

<span data-ttu-id="eddca-255">按[5.0`type-bug`里程碑中標記的問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)進行追蹤</span><span class="sxs-lookup"><span data-stu-id="eddca-255">Tracked by [issues labeled with `type-bug` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)</span></span>

<span data-ttu-id="eddca-256">開發人員: @roji @maumar @bricelam @smitpatel、 @AndriySvyryd、 、 、@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="eddca-256">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="eddca-257">T恤尺寸:L</span><span class="sxs-lookup"><span data-stu-id="eddca-257">T-shirt size: L</span></span>

<span data-ttu-id="eddca-258">狀態:正在進行</span><span class="sxs-lookup"><span data-stu-id="eddca-258">Status: In-progress</span></span>

<span data-ttu-id="eddca-259">在編寫本文時,我們有 135 個 bug 在 5.0 版本中被分對修復(已有 62 個 Bug 已修復),但與上面_的一般查詢增強部分_存在顯著重疊。</span><span class="sxs-lookup"><span data-stu-id="eddca-259">At the time of writing, we have 135 bugs triaged to be fixed in the 5.0 release (with 62 already fixed), but there is significant overlap with the _General query enhancements_ section above.</span></span>

<span data-ttu-id="eddca-260">在 3.0 版本中,傳入率(最終作為里程碑中工作的問題)每月大約 23 個問題。</span><span class="sxs-lookup"><span data-stu-id="eddca-260">The incoming rate (issues that end up as work in a milestone) was about 23 issues per month over the course of the 3.0 release.</span></span> <span data-ttu-id="eddca-261">並非所有這些都需要固定在 5.0 中。</span><span class="sxs-lookup"><span data-stu-id="eddca-261">Not all of these will need to be fixed in 5.0.</span></span> <span data-ttu-id="eddca-262">粗略估計,我們計劃在 5.0 個時間範圍內解決另外 150 個問題。</span><span class="sxs-lookup"><span data-stu-id="eddca-262">As a rough estimate we plan to fix an additional 150 issues in the 5.0 time frame.</span></span>

## <a name="small-enhancements"></a><span data-ttu-id="eddca-263">小型增強功能</span><span class="sxs-lookup"><span data-stu-id="eddca-263">Small enhancements</span></span>

<span data-ttu-id="eddca-264">按[5.0`type-enhancement`里程碑中標記的問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)進行追蹤</span><span class="sxs-lookup"><span data-stu-id="eddca-264">Tracked by [issues labeled with `type-enhancement` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)</span></span>

<span data-ttu-id="eddca-265">開發人員: @roji @maumar @bricelam @smitpatel、 @AndriySvyryd、 、 、@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="eddca-265">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="eddca-266">T恤尺寸:L</span><span class="sxs-lookup"><span data-stu-id="eddca-266">T-shirt size: L</span></span>

<span data-ttu-id="eddca-267">狀態:正在進行</span><span class="sxs-lookup"><span data-stu-id="eddca-267">Status: In-progress</span></span>

<span data-ttu-id="eddca-268">除了上面概述的更大功能外,我們還計劃對 5.0 進行許多較小的改進,以修復"剪紙"。</span><span class="sxs-lookup"><span data-stu-id="eddca-268">In addition to the bigger features outlined above, we also have many smaller improvements scheduled for 5.0 to fix "paper-cuts".</span></span> <span data-ttu-id="eddca-269">請注意,上述較一般的主題也涵蓋了其中許多增強功能。</span><span class="sxs-lookup"><span data-stu-id="eddca-269">Note that many of these enhancements are also covered by the more general themes outlined above.</span></span>

## <a name="below-the-line"></a><span data-ttu-id="eddca-270">線下</span><span class="sxs-lookup"><span data-stu-id="eddca-270">Below-the-line</span></span>

<span data-ttu-id="eddca-271">按[標籤為`consider-for-next-release`](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span><span class="sxs-lookup"><span data-stu-id="eddca-271">Tracked by [issues labeled with `consider-for-next-release`](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span></span>

<span data-ttu-id="eddca-272">這些是當前**未**為 5.0 版本安排的 Bug 修復和增強功能,但我們會根據上述工作的進度來考慮拉伸目標。</span><span class="sxs-lookup"><span data-stu-id="eddca-272">These are bug fixes and enhancements that are **not** currently scheduled for the 5.0 release, but we will look at as stretch goals depending on the progress made on the work above.</span></span>

<span data-ttu-id="eddca-273">此外,在規劃時,我們總是考慮[投票最多的問題](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc)。</span><span class="sxs-lookup"><span data-stu-id="eddca-273">In addition, we always consider the [most voted issues](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) when planning.</span></span> <span data-ttu-id="eddca-274">從發佈中削減這些問題總是痛苦的,但我們確實需要一個現實的計劃,以我認為我們擁有的資源。</span><span class="sxs-lookup"><span data-stu-id="eddca-274">Cutting any of these issues from a release is always painful, but we do need a realistic plan for the resources we have.</span></span>

## <a name="feedback"></a><span data-ttu-id="eddca-275">意見反應</span><span class="sxs-lookup"><span data-stu-id="eddca-275">Feedback</span></span>

<span data-ttu-id="eddca-276">您對計劃的意見反應很重要。</span><span class="sxs-lookup"><span data-stu-id="eddca-276">Your feedback on planning is important.</span></span> <span data-ttu-id="eddca-277">若要指出問題的重要性，最佳方式是在 GitHub 上針對該問題投票 (大拇指)。</span><span class="sxs-lookup"><span data-stu-id="eddca-277">The best way to indicate the importance of an issue is to vote (thumbs-up) for that issue on GitHub.</span></span> <span data-ttu-id="eddca-278">然後,此資料將饋入下一個版本[的規劃過程](../release-planning.md)。</span><span class="sxs-lookup"><span data-stu-id="eddca-278">This data will then feed into the [planning process](../release-planning.md) for the next release.</span></span>
