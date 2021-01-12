---
title: 規劃 Entity Framework Core 5。0
description: 規劃 Entity Framework Core 5.0 的功能
author: ajcvickers
ms.date: 08/22/2020
uid: core/what-is-new/ef-core-5.0/plan
ms.openlocfilehash: ba56a5f3c79dacc25b3402be114c57067da49433
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129040"
---
# <a name="plan-for-entity-framework-core-50"></a><span data-ttu-id="d66fb-103">規劃 Entity Framework Core 5。0</span><span class="sxs-lookup"><span data-stu-id="d66fb-103">Plan for Entity Framework Core 5.0</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d66fb-104">[現在已發行](xref:core/what-is-new/index)EF Core 5.0。</span><span class="sxs-lookup"><span data-stu-id="d66fb-104">EF Core 5.0 [has now been released](xref:core/what-is-new/index).</span></span> <span data-ttu-id="d66fb-105">此頁面會保留為方案的歷程記錄。</span><span class="sxs-lookup"><span data-stu-id="d66fb-105">This page remains as a historical record of the plan.</span></span>

<span data-ttu-id="d66fb-106">如 [規劃](xref:core/what-is-new/release-planning)程式中所述，我們已將專案關係人的輸入收集到 EF Core 5.0 版的暫時計畫。</span><span class="sxs-lookup"><span data-stu-id="d66fb-106">As described in the [planning process](xref:core/what-is-new/release-planning), we have gathered input from stakeholders into a tentative plan for the EF Core 5.0 release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d66fb-107">此計畫仍是進行中的工作。</span><span class="sxs-lookup"><span data-stu-id="d66fb-107">This plan is still a work-in-progress.</span></span> <span data-ttu-id="d66fb-108">這裡沒有任何承諾。</span><span class="sxs-lookup"><span data-stu-id="d66fb-108">Nothing here is a commitment.</span></span> <span data-ttu-id="d66fb-109">此計畫是將隨著我們深入瞭解而演進的起點。</span><span class="sxs-lookup"><span data-stu-id="d66fb-109">This plan is a starting point that will evolve as we learn more.</span></span> <span data-ttu-id="d66fb-110">目前未規劃5.0 的某些專案可能會被提取。</span><span class="sxs-lookup"><span data-stu-id="d66fb-110">Some things not currently planned for 5.0 may get pulled in.</span></span> <span data-ttu-id="d66fb-111">目前已規劃5.0 的某些專案可能會一度。</span><span class="sxs-lookup"><span data-stu-id="d66fb-111">Some things currently planned for 5.0 may get punted out.</span></span>

## <a name="general-information"></a><span data-ttu-id="d66fb-112">一般資訊</span><span class="sxs-lookup"><span data-stu-id="d66fb-112">General information</span></span>

### <a name="version-number-and-release-date"></a><span data-ttu-id="d66fb-113">版本號碼和發行日期</span><span class="sxs-lookup"><span data-stu-id="d66fb-113">Version number and release date</span></span>

<span data-ttu-id="d66fb-114">目前已排程與 [.net 5.0](https://devblogs.microsoft.com/dotnet/introducing-net-5/)同時發行 EF Core 5.0。</span><span class="sxs-lookup"><span data-stu-id="d66fb-114">EF Core 5.0 is currently scheduled for release at [the same time as .NET 5.0](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span> <span data-ttu-id="d66fb-115">選擇的 "5.0" 版本與 .NET 5.0 一致。</span><span class="sxs-lookup"><span data-stu-id="d66fb-115">The version "5.0" was chosen to align with .NET 5.0.</span></span>

### <a name="supported-platforms"></a><span data-ttu-id="d66fb-116">支援的平台</span><span class="sxs-lookup"><span data-stu-id="d66fb-116">Supported platforms</span></span>

<span data-ttu-id="d66fb-117">EF Core 5.0 計畫在任何 .NET Standard 2.1 平臺上執行，包括 .NET 5.0。</span><span class="sxs-lookup"><span data-stu-id="d66fb-117">EF Core 5.0 is planned to run on any .NET Standard 2.1 platform, including .NET 5.0.</span></span> <span data-ttu-id="d66fb-118">這是較常見的 .NET 將 [平臺聚合至 .Net Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/)的一部分。</span><span class="sxs-lookup"><span data-stu-id="d66fb-118">This is part of the more general .NET wide [convergence of platforms to .NET Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span>

<span data-ttu-id="d66fb-119">EF Core 5.0 不會在 .NET Framework 上執行。</span><span class="sxs-lookup"><span data-stu-id="d66fb-119">EF Core 5.0 will not run on .NET Framework.</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="d66fb-120">重大變更</span><span class="sxs-lookup"><span data-stu-id="d66fb-120">Breaking changes</span></span>

<span data-ttu-id="d66fb-121">EF Core 5.0 將包含一些 [重大變更](xref:core/what-is-new/ef-core-5.0/breaking-changes)，但在 EF Core 3.0 的情況下，這些變更將會比以往更低。</span><span class="sxs-lookup"><span data-stu-id="d66fb-121">EF Core 5.0 will contain some [breaking changes](xref:core/what-is-new/ef-core-5.0/breaking-changes), but these will be much less severe than was the case for EF Core 3.0.</span></span> <span data-ttu-id="d66fb-122">我們的目標是要讓大部分的應用程式在不中斷的情況下進行更新。</span><span class="sxs-lookup"><span data-stu-id="d66fb-122">Our goal is to allow the vast majority of applications to update without breaking.</span></span>

<span data-ttu-id="d66fb-123">資料庫提供者應該會有一些重大變更，尤其是針對 TPT 支援。</span><span class="sxs-lookup"><span data-stu-id="d66fb-123">It is expected that there will be some breaking changes for database providers, especially around TPT support.</span></span> <span data-ttu-id="d66fb-124">不過，我們預期針對5.0 更新提供者的工作，將會少於更新3.0 所需的時間。</span><span class="sxs-lookup"><span data-stu-id="d66fb-124">However, we expect the work to update a provider for 5.0 will be less than was required to update for 3.0.</span></span>

## <a name="themes"></a><span data-ttu-id="d66fb-125">佈景主題</span><span class="sxs-lookup"><span data-stu-id="d66fb-125">Themes</span></span>

<span data-ttu-id="d66fb-126">我們已將一些主要區域或主題解壓縮，以構成 EF Core 5.0 中的大型投資基礎。</span><span class="sxs-lookup"><span data-stu-id="d66fb-126">We have extracted a few major areas or themes which will form the basis for the large investments in EF Core 5.0.</span></span>

## <a name="fully-transparent-many-to-many-mapping-by-convention"></a><span data-ttu-id="d66fb-127">依慣例完全透明的多對多對應</span><span class="sxs-lookup"><span data-stu-id="d66fb-127">Fully transparent many-to-many mapping by convention</span></span>

<span data-ttu-id="d66fb-128">領導開發人員： @smitpatel 、 @AndriySvyryd 和 @lajones</span><span class="sxs-lookup"><span data-stu-id="d66fb-128">Lead developers: @smitpatel, @AndriySvyryd, and @lajones</span></span>

<span data-ttu-id="d66fb-129">追蹤 [#10508](https://github.com/dotnet/efcore/issues/10508)</span><span class="sxs-lookup"><span data-stu-id="d66fb-129">Tracked by [#10508](https://github.com/dotnet/efcore/issues/10508)</span></span>

<span data-ttu-id="d66fb-130">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="d66fb-130">T-shirt size: L</span></span>

<span data-ttu-id="d66fb-131">狀態：完成</span><span class="sxs-lookup"><span data-stu-id="d66fb-131">Status: Done</span></span>

<span data-ttu-id="d66fb-132">「多對多」是 [最常要求的功能](https://github.com/dotnet/efcore/issues/1368) (~ 506 投票) 在 GitHub 待處理專案上。</span><span class="sxs-lookup"><span data-stu-id="d66fb-132">Many-to-many is the [most requested feature](https://github.com/dotnet/efcore/issues/1368) (~506 votes) on the GitHub backlog.</span></span>

<span data-ttu-id="d66fb-133">多對多關聯性的支援分成三個主要區域：</span><span class="sxs-lookup"><span data-stu-id="d66fb-133">Support for many-to-many relationships can be broken down into three major areas:</span></span>

* <span data-ttu-id="d66fb-134">略過導覽屬性--下一個主題所涵蓋的內容。</span><span class="sxs-lookup"><span data-stu-id="d66fb-134">Skip navigation properties--covered by the next theme.</span></span>
* <span data-ttu-id="d66fb-135">屬性包實體類型。</span><span class="sxs-lookup"><span data-stu-id="d66fb-135">Property-bag entity types.</span></span> <span data-ttu-id="d66fb-136">這些可讓標準的 CLR 型別 (例如 `Dictionary`) 用於實體實例，因此每個實體類型都不需要明確的 clr 型別。</span><span class="sxs-lookup"><span data-stu-id="d66fb-136">These allow a standard CLR type (e.g. `Dictionary`) to be used for entity instances such that an explicit CLR type is not needed for each entity type.</span></span> <span data-ttu-id="d66fb-137">由 [#9914](https://github.com/dotnet/efcore/issues/9914)追蹤。</span><span class="sxs-lookup"><span data-stu-id="d66fb-137">Tracked by [#9914](https://github.com/dotnet/efcore/issues/9914).</span></span>
* <span data-ttu-id="d66fb-138">方便設定多對多關聯性的方便。</span><span class="sxs-lookup"><span data-stu-id="d66fb-138">Sugar for easy configuration of many-to-many relationships.</span></span>

<span data-ttu-id="d66fb-139">除了略過流覽支援以外，我們現在會將多對多的其他區域提取至 EF Core 5.0，以提供完整的體驗。</span><span class="sxs-lookup"><span data-stu-id="d66fb-139">In addition to the skip navigation support, we are now pulling these other areas of many-to-many into EF Core 5.0 so as to provide a complete experience.</span></span>

## <a name="many-to-many-navigation-properties-aka-skip-navigations"></a><span data-ttu-id="d66fb-140">多對多導覽屬性 (. k. a "skip 導覽" ) </span><span class="sxs-lookup"><span data-stu-id="d66fb-140">Many-to-many navigation properties (a.k.a "skip navigations")</span></span>

<span data-ttu-id="d66fb-141">領導開發人員： @smitpatel 和 @AndriySvyryd</span><span class="sxs-lookup"><span data-stu-id="d66fb-141">Lead developers: @smitpatel and @AndriySvyryd</span></span>

<span data-ttu-id="d66fb-142">追蹤 [#19003](https://github.com/dotnet/efcore/issues/19003)</span><span class="sxs-lookup"><span data-stu-id="d66fb-142">Tracked by [#19003](https://github.com/dotnet/efcore/issues/19003)</span></span>

<span data-ttu-id="d66fb-143">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="d66fb-143">T-shirt size: L</span></span>

<span data-ttu-id="d66fb-144">狀態：完成</span><span class="sxs-lookup"><span data-stu-id="d66fb-144">Status: Done</span></span>

<span data-ttu-id="d66fb-145">如第一個主題所述，多對多支援有許多方面。</span><span class="sxs-lookup"><span data-stu-id="d66fb-145">As described in the first theme, many-to-many support has multiple aspects.</span></span>
<span data-ttu-id="d66fb-146">本主題專門追蹤略過導覽的使用。</span><span class="sxs-lookup"><span data-stu-id="d66fb-146">This theme specifically tracks use of skip navigations.</span></span>
<span data-ttu-id="d66fb-147">我們相信，需要多對多支援的最重要封鎖程式，無法在商務邏輯（例如查詢）中使用「自然」關聯性，而不需要參考聯結資料表。</span><span class="sxs-lookup"><span data-stu-id="d66fb-147">We believe that the most significant blocker for those wanting many-to-many support is not being able to use the "natural" relationships, without referring to the join table, in business logic such as queries.</span></span>
<span data-ttu-id="d66fb-148">聯結資料表實體類型可能仍存在，但不應該以商務邏輯的方式取得。</span><span class="sxs-lookup"><span data-stu-id="d66fb-148">The join table entity type may still exist, but it should not get in the way of business logic.</span></span>

## <a name="table-per-type-tpt-inheritance-mapping"></a><span data-ttu-id="d66fb-149">每一類型的資料表 (TPT) 繼承對應</span><span class="sxs-lookup"><span data-stu-id="d66fb-149">Table-per-type (TPT) inheritance mapping</span></span>

<span data-ttu-id="d66fb-150">首席開發人員： @AndriySvyryd 和 @smitpatel</span><span class="sxs-lookup"><span data-stu-id="d66fb-150">Lead developer: @AndriySvyryd and @smitpatel</span></span>

<span data-ttu-id="d66fb-151">追蹤 [#2266](https://github.com/dotnet/efcore/issues/2266)</span><span class="sxs-lookup"><span data-stu-id="d66fb-151">Tracked by [#2266](https://github.com/dotnet/efcore/issues/2266)</span></span>

<span data-ttu-id="d66fb-152">T 恤尺寸： XL</span><span class="sxs-lookup"><span data-stu-id="d66fb-152">T-shirt size: XL</span></span>

<span data-ttu-id="d66fb-153">狀態：完成</span><span class="sxs-lookup"><span data-stu-id="d66fb-153">Status: Done</span></span>

<span data-ttu-id="d66fb-154">我們正在進行 TPT，因為它是高度要求的功能 (~ 289 的投票;第三個整體) ，因為它需要一些低層級的變更，因此我們覺得適用于整體 .NET 5 計畫的基礎本質。</span><span class="sxs-lookup"><span data-stu-id="d66fb-154">We're doing TPT because it is both a highly requested feature (~289 votes; 3rd overall) and because it requires some low-level changes that we feel are appropriate for the foundational nature of the overall .NET 5 plan.</span></span> <span data-ttu-id="d66fb-155">我們預期這會造成資料庫提供者的重大變更，但這應該比3.0 所需的變更更不嚴重。</span><span class="sxs-lookup"><span data-stu-id="d66fb-155">We expect this to result in breaking changes for database providers, although these should be much less severe than the changes required for 3.0.</span></span>

## <a name="filtered-include"></a><span data-ttu-id="d66fb-156">篩選的包含</span><span class="sxs-lookup"><span data-stu-id="d66fb-156">Filtered Include</span></span>

<span data-ttu-id="d66fb-157">首席開發人員： @maumar</span><span class="sxs-lookup"><span data-stu-id="d66fb-157">Lead developer: @maumar</span></span>

<span data-ttu-id="d66fb-158">追蹤 [#1833](https://github.com/dotnet/efcore/issues/1833)</span><span class="sxs-lookup"><span data-stu-id="d66fb-158">Tracked by [#1833](https://github.com/dotnet/efcore/issues/1833)</span></span>

<span data-ttu-id="d66fb-159">T 恤尺寸： M</span><span class="sxs-lookup"><span data-stu-id="d66fb-159">T-shirt size: M</span></span>

<span data-ttu-id="d66fb-160">狀態：完成</span><span class="sxs-lookup"><span data-stu-id="d66fb-160">Status: Done</span></span>

<span data-ttu-id="d66fb-161">篩選包含高度要求的功能 (~ 376 的投票;第2個整體) 不是大量的工作，而且我們相信您會在目前需要模型層級篩選或更複雜查詢的情況下，解除封鎖或簡化許多案例。</span><span class="sxs-lookup"><span data-stu-id="d66fb-161">Filtered Include is a highly-requested feature (~376 votes; 2nd overall) that isn't a huge amount of work, and that we believe will unblock or make easier many scenarios that currently require model-level filters or more complex queries.</span></span>

## <a name="split-include"></a><span data-ttu-id="d66fb-162">分割包含</span><span class="sxs-lookup"><span data-stu-id="d66fb-162">Split Include</span></span>

<span data-ttu-id="d66fb-163">首席開發人員： @smitpatel</span><span class="sxs-lookup"><span data-stu-id="d66fb-163">Lead developer: @smitpatel</span></span>

<span data-ttu-id="d66fb-164">追蹤 [#20892](https://github.com/dotnet/efcore/issues/20892)</span><span class="sxs-lookup"><span data-stu-id="d66fb-164">Tracked by [#20892](https://github.com/dotnet/efcore/issues/20892)</span></span>

<span data-ttu-id="d66fb-165">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="d66fb-165">T-shirt size: L</span></span>

<span data-ttu-id="d66fb-166">狀態：完成</span><span class="sxs-lookup"><span data-stu-id="d66fb-166">Status: Done</span></span>

<span data-ttu-id="d66fb-167">EF Core 3.0 變更了預設行為，以針對指定的 LINQ 查詢建立單一 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="d66fb-167">EF Core 3.0 changed the default behavior to create a single SQL query for a given LINQ query.</span></span>
<span data-ttu-id="d66fb-168">這會對使用包含多個集合的查詢造成大型效能衰退。</span><span class="sxs-lookup"><span data-stu-id="d66fb-168">This caused large performance regressions for queries that use Include for multiple collections.</span></span>

<span data-ttu-id="d66fb-169">在 EF Core 5.0 中，我們會保留新的預設行為。</span><span class="sxs-lookup"><span data-stu-id="d66fb-169">In EF Core 5.0, we are retaining the new default behavior.</span></span>
<span data-ttu-id="d66fb-170">不過，EF Core 5.0 現在會允許產生多個集合的查詢，其中包含單一查詢導致效能不佳的情況。</span><span class="sxs-lookup"><span data-stu-id="d66fb-170">However, EF Core 5.0 will now allow generation of multiple queries for collection Includes where having a single query is causing bad performance.</span></span>

## <a name="required-one-to-one-dependents"></a><span data-ttu-id="d66fb-171">需要一對一相依項</span><span class="sxs-lookup"><span data-stu-id="d66fb-171">Required one-to-one dependents</span></span>

<span data-ttu-id="d66fb-172">領導開發人員： @AndriySvyryd 和 @smitpatel</span><span class="sxs-lookup"><span data-stu-id="d66fb-172">Lead developers: @AndriySvyryd and @smitpatel</span></span>

<span data-ttu-id="d66fb-173">追蹤 [#12100](https://github.com/dotnet/efcore/issues/12100)</span><span class="sxs-lookup"><span data-stu-id="d66fb-173">Tracked by [#12100](https://github.com/dotnet/efcore/issues/12100)</span></span>

<span data-ttu-id="d66fb-174">T 恤尺寸： M</span><span class="sxs-lookup"><span data-stu-id="d66fb-174">T-shirt size: M</span></span>

<span data-ttu-id="d66fb-175">狀態：完成</span><span class="sxs-lookup"><span data-stu-id="d66fb-175">Status: Done</span></span>

<span data-ttu-id="d66fb-176">在 EF Core 3.0 中，包括擁有的類型在內的所有相依性都是選擇性的 (例如，Person 可以是 null) 。</span><span class="sxs-lookup"><span data-stu-id="d66fb-176">In EF Core 3.0, all dependents, including owned types are optional (e.g. Person.Address can be null).</span></span> <span data-ttu-id="d66fb-177">在 EF Core 5.0 中，可以視需要設定相依項。</span><span class="sxs-lookup"><span data-stu-id="d66fb-177">In EF Core 5.0, dependents can be configured as required.</span></span>

## <a name="rationalize-totable-toquery-toview-fromsql-etc"></a><span data-ttu-id="d66fb-178">將 ToTable、ToQuery、ToView、FromSql 等合理化</span><span class="sxs-lookup"><span data-stu-id="d66fb-178">Rationalize ToTable, ToQuery, ToView, FromSql, etc</span></span>

<span data-ttu-id="d66fb-179">領導開發人員： @AndriySvyryd 和 @smitpatel</span><span class="sxs-lookup"><span data-stu-id="d66fb-179">Lead developers: @AndriySvyryd and @smitpatel</span></span>

<span data-ttu-id="d66fb-180">追蹤 [#17270](https://github.com/dotnet/efcore/issues/17270)</span><span class="sxs-lookup"><span data-stu-id="d66fb-180">Tracked by [#17270](https://github.com/dotnet/efcore/issues/17270)</span></span>

<span data-ttu-id="d66fb-181">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="d66fb-181">T-shirt size: L</span></span>

<span data-ttu-id="d66fb-182">狀態：完成</span><span class="sxs-lookup"><span data-stu-id="d66fb-182">Status: Done</span></span>

<span data-ttu-id="d66fb-183">在舊版中，我們已在支援原始 SQL、無索引鍵類型和相關區域的過程中進行了進展。</span><span class="sxs-lookup"><span data-stu-id="d66fb-183">We have made progress in previous releases towards supporting raw SQL, keyless types, and related areas.</span></span> <span data-ttu-id="d66fb-184">不過，所有專案整體運作的方式都有差距和不一致的情況。</span><span class="sxs-lookup"><span data-stu-id="d66fb-184">However, there are both gaps and inconsistencies in the way everything works together as a whole.</span></span> <span data-ttu-id="d66fb-185">5.0 的目標是要修正這些問題，並建立良好的體驗來定義、遷移和使用不同類型的實體及其相關聯的查詢和資料庫成品。</span><span class="sxs-lookup"><span data-stu-id="d66fb-185">The goal for 5.0 is to fix these and create a good experience for defining, migrating, and using different types of entities and their associated queries and database artifacts.</span></span> <span data-ttu-id="d66fb-186">這也可能牽涉到已編譯查詢 API 的更新。</span><span class="sxs-lookup"><span data-stu-id="d66fb-186">This may also involve updates to the compiled query API.</span></span>

<span data-ttu-id="d66fb-187">請注意，此專案可能會導致一些應用層級的重大變更，因為我們目前所擁有的一些功能過於寬鬆，因此可以快速地讓人們進入失敗的 pits。</span><span class="sxs-lookup"><span data-stu-id="d66fb-187">Note that this item may result in some application-level breaking changes since some of the functionality we currently have is too permissive such that it can quickly lead people into pits of failure.</span></span> <span data-ttu-id="d66fb-188">我們最後可能會封鎖這其中一些功能，以及應如何取代的指引。</span><span class="sxs-lookup"><span data-stu-id="d66fb-188">We will likely end up blocking some of this functionality together with guidance on what to do instead.</span></span>

## <a name="general-query-enhancements"></a><span data-ttu-id="d66fb-189">一般查詢增強功能</span><span class="sxs-lookup"><span data-stu-id="d66fb-189">General query enhancements</span></span>

<span data-ttu-id="d66fb-190">領導開發人員： @smitpatel 和 @maumar</span><span class="sxs-lookup"><span data-stu-id="d66fb-190">Lead developers: @smitpatel and @maumar</span></span>

<span data-ttu-id="d66fb-191">由[ `area-query` 5.0 里程碑中標記](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)為的問題追蹤</span><span class="sxs-lookup"><span data-stu-id="d66fb-191">Tracked by [issues labeled with `area-query` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="d66fb-192">T 恤尺寸： XL</span><span class="sxs-lookup"><span data-stu-id="d66fb-192">T-shirt size: XL</span></span>

<span data-ttu-id="d66fb-193">狀態：完成</span><span class="sxs-lookup"><span data-stu-id="d66fb-193">Status: Done</span></span>

<span data-ttu-id="d66fb-194">已針對 EF Core 3.0 大量重寫查詢轉譯程式碼。</span><span class="sxs-lookup"><span data-stu-id="d66fb-194">The query translation code was extensively rewritten for EF Core 3.0.</span></span> <span data-ttu-id="d66fb-195">由於這個原因，查詢程式碼通常會以更健全的狀態進行。</span><span class="sxs-lookup"><span data-stu-id="d66fb-195">The query code is generally in a much more robust state because of this.</span></span> <span data-ttu-id="d66fb-196">針對5.0，我們不打算在支援 TPT 和略過導覽屬性所需的外部進行主要查詢變更。</span><span class="sxs-lookup"><span data-stu-id="d66fb-196">For 5.0 we aren't planning on making major query changes, outside those needed to support TPT and skip navigation properties.</span></span> <span data-ttu-id="d66fb-197">不過，仍有很多工作需要修正從3.0 的檢修中留下的一些技術債務。</span><span class="sxs-lookup"><span data-stu-id="d66fb-197">However, there is still significant work needed to fix some technical debt left over from the 3.0 overhaul.</span></span> <span data-ttu-id="d66fb-198">我們也計畫修正許多 bug，並執行小規模的增強功能，進一步改善整體查詢體驗。</span><span class="sxs-lookup"><span data-stu-id="d66fb-198">We also plan to fix many bugs and implement small enhancements to further improve the overall query experience.</span></span>

## <a name="migrations-and-deployment-experience"></a><span data-ttu-id="d66fb-199">遷移和部署體驗</span><span class="sxs-lookup"><span data-stu-id="d66fb-199">Migrations and deployment experience</span></span>

<span data-ttu-id="d66fb-200">首席開發人員： @bricelam</span><span class="sxs-lookup"><span data-stu-id="d66fb-200">Lead developers: @bricelam</span></span>

<span data-ttu-id="d66fb-201">追蹤 [#19587](https://github.com/dotnet/efcore/issues/19587)</span><span class="sxs-lookup"><span data-stu-id="d66fb-201">Tracked by [#19587](https://github.com/dotnet/efcore/issues/19587)</span></span>

<span data-ttu-id="d66fb-202">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="d66fb-202">T-shirt size: L</span></span>

<span data-ttu-id="d66fb-203">狀態：限域/完成</span><span class="sxs-lookup"><span data-stu-id="d66fb-203">Status: Scoped/Done</span></span>

<span data-ttu-id="d66fb-204">範圍： [遷移組合功能](https://github.com/dotnet/efcore/issues/19693) 已延後，直到 EF Core 5.0 版本為止。</span><span class="sxs-lookup"><span data-stu-id="d66fb-204">Scoping: The [migrations bundles feature](https://github.com/dotnet/efcore/issues/19693) has been deferred until after the EF Core 5.0 release.</span></span> <span data-ttu-id="d66fb-205">不過， [與遷移相關](https://github.com/dotnet/efcore/issues/19587#issuecomment-668794460) 的數個其他目標改進將包含在 EF Core 5。0</span><span class="sxs-lookup"><span data-stu-id="d66fb-205">However, several other [targeted improvements related to migrations](https://github.com/dotnet/efcore/issues/19587#issuecomment-668794460) will be included in EF Core 5.0</span></span>

<span data-ttu-id="d66fb-206">目前，許多開發人員會在應用程式啟動時遷移其資料庫。</span><span class="sxs-lookup"><span data-stu-id="d66fb-206">Currently, many developers migrate their databases at application startup time.</span></span> <span data-ttu-id="d66fb-207">這很簡單，但不建議使用，因為：</span><span class="sxs-lookup"><span data-stu-id="d66fb-207">This is easy but is not recommended because:</span></span>

* <span data-ttu-id="d66fb-208">多個執行緒/進程/伺服器可能會嘗試同時遷移資料庫</span><span class="sxs-lookup"><span data-stu-id="d66fb-208">Multiple threads/processes/servers may attempt to migrate the database concurrently</span></span>
* <span data-ttu-id="d66fb-209">當發生這種情況時，應用程式可能會嘗試存取不一致的狀態</span><span class="sxs-lookup"><span data-stu-id="d66fb-209">Applications may try to access inconsistent state while this is happening</span></span>
* <span data-ttu-id="d66fb-210">通常不應針對應用程式執行，授與修改架構的資料庫許可權</span><span class="sxs-lookup"><span data-stu-id="d66fb-210">Usually the database permissions to modify the schema should not be granted for application execution</span></span>
* <span data-ttu-id="d66fb-211">如果發生錯誤，則很難還原為乾淨狀態</span><span class="sxs-lookup"><span data-stu-id="d66fb-211">It's hard to revert back to a clean state if something goes wrong</span></span>

<span data-ttu-id="d66fb-212">我們想要在此提供更好的體驗，讓您在部署時可以輕鬆地遷移資料庫。</span><span class="sxs-lookup"><span data-stu-id="d66fb-212">We want to deliver a better experience here that allows an easy way to migrate the database at deployment time.</span></span> <span data-ttu-id="d66fb-213">這應該是：</span><span class="sxs-lookup"><span data-stu-id="d66fb-213">This should:</span></span>

* <span data-ttu-id="d66fb-214">在 Linux、Mac 和 Windows 上工作</span><span class="sxs-lookup"><span data-stu-id="d66fb-214">Work on Linux, Mac, and Windows</span></span>
* <span data-ttu-id="d66fb-215">在命令列上提供絕佳的體驗</span><span class="sxs-lookup"><span data-stu-id="d66fb-215">Be a good experience on the command line</span></span>
* <span data-ttu-id="d66fb-216">使用容器的支援案例</span><span class="sxs-lookup"><span data-stu-id="d66fb-216">Support scenarios with containers</span></span>
* <span data-ttu-id="d66fb-217">使用常用的真實世界部署工具/流程</span><span class="sxs-lookup"><span data-stu-id="d66fb-217">Work with commonly used real-world deployment tools/flows</span></span>
* <span data-ttu-id="d66fb-218">至少整合到 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d66fb-218">Integrate into at least Visual Studio</span></span>

<span data-ttu-id="d66fb-219">結果可能是 EF Core 的許多小改良 (例如，在 SQLite) 上進行更佳的遷移，以及與其他小組的指引和長期共同作業，以改善超越 EF 的端對端體驗。</span><span class="sxs-lookup"><span data-stu-id="d66fb-219">The result is likely to be many small improvements in EF Core (for example, better Migrations on SQLite), together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

## <a name="ef-core-platforms-experience"></a><span data-ttu-id="d66fb-220">EF Core 平臺體驗</span><span class="sxs-lookup"><span data-stu-id="d66fb-220">EF Core platforms experience</span></span>

<span data-ttu-id="d66fb-221">領導開發人員： @roji 和 @bricelam</span><span class="sxs-lookup"><span data-stu-id="d66fb-221">Lead developers: @roji and @bricelam</span></span>

<span data-ttu-id="d66fb-222">追蹤 [#19588](https://github.com/dotnet/efcore/issues/19588)</span><span class="sxs-lookup"><span data-stu-id="d66fb-222">Tracked by [#19588](https://github.com/dotnet/efcore/issues/19588)</span></span>

<span data-ttu-id="d66fb-223">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="d66fb-223">T-shirt size: L</span></span>

<span data-ttu-id="d66fb-224">狀態：範圍/完成</span><span class="sxs-lookup"><span data-stu-id="d66fb-224">Status: Scope/Done</span></span>

<span data-ttu-id="d66fb-225">範圍：針對 Blazor、Xamarin、WinForms 和 WPF 發佈平臺指引和範例。</span><span class="sxs-lookup"><span data-stu-id="d66fb-225">Scoping: Platform guidance and samples are published for Blazor, Xamarin, WinForms, and WPF.</span></span> <span data-ttu-id="d66fb-226">現在已針對 EF Core 6.0 規劃 Xamarin 和其他 AOT/連結器工作。</span><span class="sxs-lookup"><span data-stu-id="d66fb-226">Xamarin and other AOT/linker work is now planned for EF Core 6.0.</span></span>

<span data-ttu-id="d66fb-227">對於在傳統的 MVC web 應用程式中使用 EF Core，我們有良好的指引。</span><span class="sxs-lookup"><span data-stu-id="d66fb-227">We have good guidance for using EF Core in traditional MVC-like web applications.</span></span> <span data-ttu-id="d66fb-228">其他平臺和應用程式模型的指引已遺失或已過期。</span><span class="sxs-lookup"><span data-stu-id="d66fb-228">Guidance for other platforms and application models is either missing or out-of-date.</span></span> <span data-ttu-id="d66fb-229">針對 EF Core 5.0，我們計畫調查、改進和記錄搭配使用 EF Core 的體驗：</span><span class="sxs-lookup"><span data-stu-id="d66fb-229">For EF Core 5.0, we plan to investigate, improve, and document the experience of using EF Core with:</span></span>

* <span data-ttu-id="d66fb-230">Blazor</span><span class="sxs-lookup"><span data-stu-id="d66fb-230">Blazor</span></span>
* <span data-ttu-id="d66fb-231">Xamarin，包括使用 AOT/連結器案例</span><span class="sxs-lookup"><span data-stu-id="d66fb-231">Xamarin, including using the AOT/linker story</span></span>
* <span data-ttu-id="d66fb-232">WinForms/WPF/WinUI，可能還有其他 U.I。</span><span class="sxs-lookup"><span data-stu-id="d66fb-232">WinForms/WPF/WinUI and possibly other U.I.</span></span> <span data-ttu-id="d66fb-233">frameworks</span><span class="sxs-lookup"><span data-stu-id="d66fb-233">frameworks</span></span>

<span data-ttu-id="d66fb-234">這可能是 EF Core 中的許多小改良，以及與其他小組的指引和長期共同作業，以改善超越 EF 的端對端體驗。</span><span class="sxs-lookup"><span data-stu-id="d66fb-234">This is likely to be many small improvements in EF Core, together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

<span data-ttu-id="d66fb-235">我們打算查看的特定區域包括：</span><span class="sxs-lookup"><span data-stu-id="d66fb-235">Specific areas we plan to look at are:</span></span>

* <span data-ttu-id="d66fb-236">部署，包括使用 EF 工具（例如遷移）的體驗</span><span class="sxs-lookup"><span data-stu-id="d66fb-236">Deployment, including the experience for using EF tooling such as for Migrations</span></span>
* <span data-ttu-id="d66fb-237">應用程式模型（包括 Xamarin 和 Blazor），也可能是其他模型</span><span class="sxs-lookup"><span data-stu-id="d66fb-237">Application models, including Xamarin and Blazor, and probably others</span></span>
* <span data-ttu-id="d66fb-238">SQLite 體驗，包括空間體驗和資料表重建</span><span class="sxs-lookup"><span data-stu-id="d66fb-238">SQLite experiences, including the spatial experience and table rebuilds</span></span>
* <span data-ttu-id="d66fb-239">AOT 和連結體驗</span><span class="sxs-lookup"><span data-stu-id="d66fb-239">AOT and linking experiences</span></span>
* <span data-ttu-id="d66fb-240">診斷整合，包括效能計數器</span><span class="sxs-lookup"><span data-stu-id="d66fb-240">Diagnostics integration, including perf counters</span></span>

## <a name="performance"></a><span data-ttu-id="d66fb-241">效能</span><span class="sxs-lookup"><span data-stu-id="d66fb-241">Performance</span></span>

<span data-ttu-id="d66fb-242">首席開發人員： @roji</span><span class="sxs-lookup"><span data-stu-id="d66fb-242">Lead developer: @roji</span></span>

<span data-ttu-id="d66fb-243">由[ `area-perf` 5.0 里程碑中標記](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)為的問題追蹤</span><span class="sxs-lookup"><span data-stu-id="d66fb-243">Tracked by [issues labeled with `area-perf` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="d66fb-244">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="d66fb-244">T-shirt size: L</span></span>

<span data-ttu-id="d66fb-245">狀態：限域/完成</span><span class="sxs-lookup"><span data-stu-id="d66fb-245">Status: Scoped/Done</span></span>

<span data-ttu-id="d66fb-246">範圍： Npgsql 提供者的主要效能改進已完成。</span><span class="sxs-lookup"><span data-stu-id="d66fb-246">Scoping: Major performance improvements in the Npgsql provider are complete.</span></span> <span data-ttu-id="d66fb-247">現在已針對 EF Core 6.0 規劃其他效能工作。</span><span class="sxs-lookup"><span data-stu-id="d66fb-247">Other performance work is now planned for EF Core 6.0.</span></span>

<span data-ttu-id="d66fb-248">針對 EF Core，我們計畫改善效能基準測試套件，並對執行時間進行導向的效能改進。</span><span class="sxs-lookup"><span data-stu-id="d66fb-248">For EF Core, we plan to improve our suite of performance benchmarks and make directed performance improvements to the runtime.</span></span> <span data-ttu-id="d66fb-249">此外，我們計畫在3.0 發行週期期間，完成新的 ADO.NET 批次處理 API。</span><span class="sxs-lookup"><span data-stu-id="d66fb-249">In addition, we plan to complete the new ADO.NET batching API which was prototyped during the 3.0 release cycle.</span></span> <span data-ttu-id="d66fb-250">此外，在 ADO.NET 層，我們會為 Npgsql 提供者規劃額外的效能改進。</span><span class="sxs-lookup"><span data-stu-id="d66fb-250">Also at the ADO.NET layer, we plan additional performance improvements to the Npgsql provider.</span></span>

<span data-ttu-id="d66fb-251">在這項工作中，我們也計畫在適當的情況下新增 ADO.NET/EF 核心效能計數器和其他診斷。</span><span class="sxs-lookup"><span data-stu-id="d66fb-251">As part of this work we also plan to add ADO.NET/EF Core performance counters and other diagnostics as appropriate.</span></span>

## <a name="architecturalcontributor-documentation"></a><span data-ttu-id="d66fb-252">架構/參與者檔</span><span class="sxs-lookup"><span data-stu-id="d66fb-252">Architectural/contributor documentation</span></span>

<span data-ttu-id="d66fb-253">主管文檔： @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="d66fb-253">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="d66fb-254">追蹤 [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)</span><span class="sxs-lookup"><span data-stu-id="d66fb-254">Tracked by [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)</span></span>

<span data-ttu-id="d66fb-255">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="d66fb-255">T-shirt size: L</span></span>

<span data-ttu-id="d66fb-256">狀態：剪下</span><span class="sxs-lookup"><span data-stu-id="d66fb-256">Status: Cut</span></span>

<span data-ttu-id="d66fb-257">這裡的目的是要讓您更輕鬆地瞭解 EF Core 內部的進展。</span><span class="sxs-lookup"><span data-stu-id="d66fb-257">The idea here is to make it easier to understand what is going on in the internals of EF Core.</span></span> <span data-ttu-id="d66fb-258">這對任何使用 EF Core 的人都很有用，但主要動機是讓外部人員更輕鬆地：</span><span class="sxs-lookup"><span data-stu-id="d66fb-258">This can be useful to anyone using EF Core, but the primary motivation is to make it easier for external people to:</span></span>

* <span data-ttu-id="d66fb-259">參與 EF Core 程式碼</span><span class="sxs-lookup"><span data-stu-id="d66fb-259">Contribute to the EF Core code</span></span>
* <span data-ttu-id="d66fb-260">建立資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="d66fb-260">Create database providers</span></span>
* <span data-ttu-id="d66fb-261">建立其他延伸模組</span><span class="sxs-lookup"><span data-stu-id="d66fb-261">Build other extensions</span></span>

<span data-ttu-id="d66fb-262">更新：很可惜，此計畫太確保建構完善。</span><span class="sxs-lookup"><span data-stu-id="d66fb-262">Update: Unfortunately, this plan was too ambitious.</span></span>
<span data-ttu-id="d66fb-263">我們仍然認為這很重要，但不幸的是，它不會 EF Core 5.0。</span><span class="sxs-lookup"><span data-stu-id="d66fb-263">We still believe this is important, but unfortunately it won't land with EF Core 5.0.</span></span>

## <a name="microsoftdatasqlite-documentation"></a><span data-ttu-id="d66fb-264">Microsoft. Sqlite 檔</span><span class="sxs-lookup"><span data-stu-id="d66fb-264">Microsoft.Data.Sqlite documentation</span></span>

<span data-ttu-id="d66fb-265">主管文檔： @bricelam</span><span class="sxs-lookup"><span data-stu-id="d66fb-265">Lead documenter: @bricelam</span></span>

<span data-ttu-id="d66fb-266">追蹤 [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)</span><span class="sxs-lookup"><span data-stu-id="d66fb-266">Tracked by [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)</span></span>

<span data-ttu-id="d66fb-267">T 恤尺寸： M</span><span class="sxs-lookup"><span data-stu-id="d66fb-267">T-shirt size: M</span></span>

<span data-ttu-id="d66fb-268">狀態：已完成。</span><span class="sxs-lookup"><span data-stu-id="d66fb-268">Status: Completed.</span></span> <span data-ttu-id="d66fb-269">新的檔位於 [Microsoft 檔網站上](/dotnet/standard/data/sqlite/?tabs=netcore-cli)。</span><span class="sxs-lookup"><span data-stu-id="d66fb-269">The new documentation is [live on the Microsoft docs site](/dotnet/standard/data/sqlite/?tabs=netcore-cli).</span></span>

<span data-ttu-id="d66fb-270">EF 小組也擁有 ADO.NET 提供者。</span><span class="sxs-lookup"><span data-stu-id="d66fb-270">The EF Team also owns the Microsoft.Data.Sqlite ADO.NET provider.</span></span> <span data-ttu-id="d66fb-271">我們計畫在5.0 版中完整記錄此提供者。</span><span class="sxs-lookup"><span data-stu-id="d66fb-271">We plan to fully document this provider as part of the 5.0 release.</span></span>

## <a name="general-documentation"></a><span data-ttu-id="d66fb-272">一般檔</span><span class="sxs-lookup"><span data-stu-id="d66fb-272">General documentation</span></span>

<span data-ttu-id="d66fb-273">主管文檔： @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="d66fb-273">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="d66fb-274">由[5.0 里程碑的](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)檔存放庫中的問題所追蹤</span><span class="sxs-lookup"><span data-stu-id="d66fb-274">Tracked by [issues in the docs repo in the 5.0 milestone](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="d66fb-275">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="d66fb-275">T-shirt size: L</span></span>

<span data-ttu-id="d66fb-276">狀態：進行中</span><span class="sxs-lookup"><span data-stu-id="d66fb-276">Status: In-progress</span></span>

<span data-ttu-id="d66fb-277">我們已在更新3.0 和3.1 版的檔集。</span><span class="sxs-lookup"><span data-stu-id="d66fb-277">We are already in the process of updating documentation for the 3.0 and 3.1 releases.</span></span> <span data-ttu-id="d66fb-278">我們也在努力：</span><span class="sxs-lookup"><span data-stu-id="d66fb-278">We are also working on:</span></span>

* <span data-ttu-id="d66fb-279">深入瞭解使用者入門檔，使其更平易近人/更容易遵循</span><span class="sxs-lookup"><span data-stu-id="d66fb-279">An overhaul of the getting started docs to make them more approachable/easier to follow</span></span>
* <span data-ttu-id="d66fb-280">重新組織檔，讓您更容易尋找及新增交叉參考</span><span class="sxs-lookup"><span data-stu-id="d66fb-280">Reorganization of docs to make things easier to find and to add cross-references</span></span>
* <span data-ttu-id="d66fb-281">新增更多詳細資料並對現有檔進行說明</span><span class="sxs-lookup"><span data-stu-id="d66fb-281">Adding more details and clarifications to existing docs</span></span>
* <span data-ttu-id="d66fb-282">更新範例和新增更多範例</span><span class="sxs-lookup"><span data-stu-id="d66fb-282">Updating the samples and adding more examples</span></span>

## <a name="fixing-bugs"></a><span data-ttu-id="d66fb-283">修正 bug</span><span class="sxs-lookup"><span data-stu-id="d66fb-283">Fixing bugs</span></span>

<span data-ttu-id="d66fb-284">由[ `type-bug` 5.0 里程碑中標記](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)為的問題追蹤</span><span class="sxs-lookup"><span data-stu-id="d66fb-284">Tracked by [issues labeled with `type-bug` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)</span></span>

<span data-ttu-id="d66fb-285">開發人員： @roji 、 @maumar 、 @bricelam 、 @smitpatel 、 @AndriySvyryd 、 @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="d66fb-285">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="d66fb-286">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="d66fb-286">T-shirt size: L</span></span>

<span data-ttu-id="d66fb-287">狀態：進行中</span><span class="sxs-lookup"><span data-stu-id="d66fb-287">Status: In-progress</span></span>

<span data-ttu-id="d66fb-288">在撰寫本文時，我們會將135的 bug 分級為在5.0 版 (中修正（62已) 修正），但是上述的 _一般查詢增強功能_ 一節會有顯著的重迭。</span><span class="sxs-lookup"><span data-stu-id="d66fb-288">At the time of writing, we have 135 bugs triaged to be fixed in the 5.0 release (with 62 already fixed), but there is significant overlap with the _General query enhancements_ section above.</span></span>

<span data-ttu-id="d66fb-289">傳入的速率 (的問題最後是里程碑的工作) 在3.0 版本的過程中，每個月大約23個問題。</span><span class="sxs-lookup"><span data-stu-id="d66fb-289">The incoming rate (issues that end up as work in a milestone) was about 23 issues per month over the course of the 3.0 release.</span></span> <span data-ttu-id="d66fb-290">並非所有這些都必須在5.0 中修正。</span><span class="sxs-lookup"><span data-stu-id="d66fb-290">Not all of these will need to be fixed in 5.0.</span></span> <span data-ttu-id="d66fb-291">大致上，我們打算修正5.0 時間範圍中的額外150問題。</span><span class="sxs-lookup"><span data-stu-id="d66fb-291">As a rough estimate we plan to fix an additional 150 issues in the 5.0 time frame.</span></span>

## <a name="small-enhancements"></a><span data-ttu-id="d66fb-292">小型增強功能</span><span class="sxs-lookup"><span data-stu-id="d66fb-292">Small enhancements</span></span>

<span data-ttu-id="d66fb-293">由[ `type-enhancement` 5.0 里程碑中標記](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)為的問題追蹤</span><span class="sxs-lookup"><span data-stu-id="d66fb-293">Tracked by [issues labeled with `type-enhancement` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)</span></span>

<span data-ttu-id="d66fb-294">開發人員： @roji 、 @maumar 、 @bricelam 、 @smitpatel 、 @AndriySvyryd 、 @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="d66fb-294">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="d66fb-295">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="d66fb-295">T-shirt size: L</span></span>

<span data-ttu-id="d66fb-296">狀態：完成</span><span class="sxs-lookup"><span data-stu-id="d66fb-296">Status: Done</span></span>

<span data-ttu-id="d66fb-297">除了上面所述的更大功能之外，我們也為5.0 排定了許多較小的改進，以修正「紙張-剪下」。</span><span class="sxs-lookup"><span data-stu-id="d66fb-297">In addition to the bigger features outlined above, we also have many smaller improvements scheduled for 5.0 to fix "paper-cuts".</span></span> <span data-ttu-id="d66fb-298">請注意，上述的許多增強功能也都包含在上面所述的一般主題中。</span><span class="sxs-lookup"><span data-stu-id="d66fb-298">Note that many of these enhancements are also covered by the more general themes outlined above.</span></span>

## <a name="below-the-line"></a><span data-ttu-id="d66fb-299">以下-行</span><span class="sxs-lookup"><span data-stu-id="d66fb-299">Below-the-line</span></span>

<span data-ttu-id="d66fb-300">由[標記為的 `consider-for-next-release` 問題](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)追蹤</span><span class="sxs-lookup"><span data-stu-id="d66fb-300">Tracked by [issues labeled with `consider-for-next-release`](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span></span>

<span data-ttu-id="d66fb-301">這些是目前 **尚未** 針對5.0 版本排程的 bug 修正和增強功能，但我們將根據上述工作的進度來查看延展目標。</span><span class="sxs-lookup"><span data-stu-id="d66fb-301">These are bug fixes and enhancements that are **not** currently scheduled for the 5.0 release, but we will look at as stretch goals depending on the progress made on the work above.</span></span>

<span data-ttu-id="d66fb-302">此外，我們在規劃時，一律會考慮 [最投票的問題](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) 。</span><span class="sxs-lookup"><span data-stu-id="d66fb-302">In addition, we always consider the [most voted issues](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) when planning.</span></span> <span data-ttu-id="d66fb-303">從版本中剪下這些問題一直都是很麻煩的，但我們的確需要針對我們所擁有的資源進行實際的規劃。</span><span class="sxs-lookup"><span data-stu-id="d66fb-303">Cutting any of these issues from a release is always painful, but we do need a realistic plan for the resources we have.</span></span>

## <a name="suggestions"></a><span data-ttu-id="d66fb-304">建議</span><span class="sxs-lookup"><span data-stu-id="d66fb-304">Suggestions</span></span>

<span data-ttu-id="d66fb-305">您對計劃的意見反應很重要。</span><span class="sxs-lookup"><span data-stu-id="d66fb-305">Your feedback on planning is important.</span></span> <span data-ttu-id="d66fb-306">若要指出問題的重要性，最佳方式是在 GitHub 上針對該問題投票 (大拇指)。</span><span class="sxs-lookup"><span data-stu-id="d66fb-306">The best way to indicate the importance of an issue is to vote (thumbs-up) for that issue on GitHub.</span></span> <span data-ttu-id="d66fb-307">然後，此資料會送入下一版的 [規劃](xref:core/what-is-new/release-planning) 程式。</span><span class="sxs-lookup"><span data-stu-id="d66fb-307">This data will then feed into the [planning process](xref:core/what-is-new/release-planning) for the next release.</span></span>
