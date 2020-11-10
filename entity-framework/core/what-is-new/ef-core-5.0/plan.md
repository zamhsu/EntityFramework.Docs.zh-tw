---
title: 規劃 Entity Framework Core 5。0
description: 規劃 Entity Framework Core 5.0 的功能
author: ajcvickers
ms.date: 08/22/2020
uid: core/what-is-new/ef-core-5.0/plan
ms.openlocfilehash: f2639e41499f3bfca5942d613922fd97212fc2b9
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429230"
---
# <a name="plan-for-entity-framework-core-50"></a><span data-ttu-id="9e376-103">規劃 Entity Framework Core 5。0</span><span class="sxs-lookup"><span data-stu-id="9e376-103">Plan for Entity Framework Core 5.0</span></span>

<span data-ttu-id="9e376-104">如 [規劃](xref:core/what-is-new/release-planning)程式中所述，我們已將專案關係人的輸入收集到 EF Core 5.0 版的暫時計畫。</span><span class="sxs-lookup"><span data-stu-id="9e376-104">As described in the [planning process](xref:core/what-is-new/release-planning), we have gathered input from stakeholders into a tentative plan for the EF Core 5.0 release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="9e376-105">此計畫仍是進行中的工作。</span><span class="sxs-lookup"><span data-stu-id="9e376-105">This plan is still a work-in-progress.</span></span> <span data-ttu-id="9e376-106">這裡沒有任何承諾。</span><span class="sxs-lookup"><span data-stu-id="9e376-106">Nothing here is a commitment.</span></span> <span data-ttu-id="9e376-107">此計畫是將隨著我們深入瞭解而演進的起點。</span><span class="sxs-lookup"><span data-stu-id="9e376-107">This plan is a starting point that will evolve as we learn more.</span></span> <span data-ttu-id="9e376-108">目前未規劃5.0 的某些專案可能會被提取。</span><span class="sxs-lookup"><span data-stu-id="9e376-108">Some things not currently planned for 5.0 may get pulled in.</span></span> <span data-ttu-id="9e376-109">目前已規劃5.0 的某些專案可能會一度。</span><span class="sxs-lookup"><span data-stu-id="9e376-109">Some things currently planned for 5.0 may get punted out.</span></span>

## <a name="general-information"></a><span data-ttu-id="9e376-110">一般資訊</span><span class="sxs-lookup"><span data-stu-id="9e376-110">General information</span></span>

### <a name="version-number-and-release-date"></a><span data-ttu-id="9e376-111">版本號碼和發行日期</span><span class="sxs-lookup"><span data-stu-id="9e376-111">Version number and release date</span></span>

<span data-ttu-id="9e376-112">目前已排程與 [.net 5.0](https://devblogs.microsoft.com/dotnet/introducing-net-5/)同時發行 EF Core 5.0。</span><span class="sxs-lookup"><span data-stu-id="9e376-112">EF Core 5.0 is currently scheduled for release at [the same time as .NET 5.0](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span> <span data-ttu-id="9e376-113">選擇的 "5.0" 版本與 .NET 5.0 一致。</span><span class="sxs-lookup"><span data-stu-id="9e376-113">The version "5.0" was chosen to align with .NET 5.0.</span></span>

### <a name="supported-platforms"></a><span data-ttu-id="9e376-114">支援的平台</span><span class="sxs-lookup"><span data-stu-id="9e376-114">Supported platforms</span></span>

<span data-ttu-id="9e376-115">EF Core 5.0 計畫在任何 .NET Standard 2.1 平臺上執行，包括 .NET 5.0。</span><span class="sxs-lookup"><span data-stu-id="9e376-115">EF Core 5.0 is planned to run on any .NET Standard 2.1 platform, including .NET 5.0.</span></span> <span data-ttu-id="9e376-116">這是較常見的 .NET 將 [平臺聚合至 .Net Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/)的一部分。</span><span class="sxs-lookup"><span data-stu-id="9e376-116">This is part of the more general .NET wide [convergence of platforms to .NET Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span>

<span data-ttu-id="9e376-117">EF Core 5.0 不會在 .NET Framework 上執行。</span><span class="sxs-lookup"><span data-stu-id="9e376-117">EF Core 5.0 will not run on .NET Framework.</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="9e376-118">重大變更</span><span class="sxs-lookup"><span data-stu-id="9e376-118">Breaking changes</span></span>

<span data-ttu-id="9e376-119">EF Core 5.0 將包含一些 [重大變更](xref:core/what-is-new/ef-core-5.0/breaking-changes)，但在 EF Core 3.0 的情況下，這些變更將會比以往更低。</span><span class="sxs-lookup"><span data-stu-id="9e376-119">EF Core 5.0 will contain some [breaking changes](xref:core/what-is-new/ef-core-5.0/breaking-changes), but these will be much less severe than was the case for EF Core 3.0.</span></span> <span data-ttu-id="9e376-120">我們的目標是要讓大部分的應用程式在不中斷的情況下進行更新。</span><span class="sxs-lookup"><span data-stu-id="9e376-120">Our goal is to allow the vast majority of applications to update without breaking.</span></span>

<span data-ttu-id="9e376-121">資料庫提供者應該會有一些重大變更，尤其是針對 TPT 支援。</span><span class="sxs-lookup"><span data-stu-id="9e376-121">It is expected that there will be some breaking changes for database providers, especially around TPT support.</span></span> <span data-ttu-id="9e376-122">不過，我們預期針對5.0 更新提供者的工作，將會少於更新3.0 所需的時間。</span><span class="sxs-lookup"><span data-stu-id="9e376-122">However, we expect the work to update a provider for 5.0 will be less than was required to update for 3.0.</span></span>

## <a name="themes"></a><span data-ttu-id="9e376-123">佈景主題</span><span class="sxs-lookup"><span data-stu-id="9e376-123">Themes</span></span>

<span data-ttu-id="9e376-124">我們已將一些主要區域或主題解壓縮，以構成 EF Core 5.0 中的大型投資基礎。</span><span class="sxs-lookup"><span data-stu-id="9e376-124">We have extracted a few major areas or themes which will form the basis for the large investments in EF Core 5.0.</span></span>

## <a name="fully-transparent-many-to-many-mapping-by-convention"></a><span data-ttu-id="9e376-125">依慣例完全透明的多對多對應</span><span class="sxs-lookup"><span data-stu-id="9e376-125">Fully transparent many-to-many mapping by convention</span></span>

<span data-ttu-id="9e376-126">領導開發人員： @smitpatel 、 @AndriySvyryd 和 @lajones</span><span class="sxs-lookup"><span data-stu-id="9e376-126">Lead developers: @smitpatel, @AndriySvyryd, and @lajones</span></span>

<span data-ttu-id="9e376-127">追蹤 [#10508](https://github.com/dotnet/efcore/issues/10508)</span><span class="sxs-lookup"><span data-stu-id="9e376-127">Tracked by [#10508](https://github.com/dotnet/efcore/issues/10508)</span></span>

<span data-ttu-id="9e376-128">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="9e376-128">T-shirt size: L</span></span>

<span data-ttu-id="9e376-129">狀態：完成</span><span class="sxs-lookup"><span data-stu-id="9e376-129">Status: Done</span></span>

<span data-ttu-id="9e376-130">「多對多」是 [最常要求的功能](https://github.com/dotnet/efcore/issues/1368) (~ 506 投票) 在 GitHub 待處理專案上。</span><span class="sxs-lookup"><span data-stu-id="9e376-130">Many-to-many is the [most requested feature](https://github.com/dotnet/efcore/issues/1368) (~506 votes) on the GitHub backlog.</span></span>

<span data-ttu-id="9e376-131">多對多關聯性的支援分成三個主要區域：</span><span class="sxs-lookup"><span data-stu-id="9e376-131">Support for many-to-many relationships can be broken down into three major areas:</span></span>

* <span data-ttu-id="9e376-132">略過導覽屬性--下一個主題所涵蓋的內容。</span><span class="sxs-lookup"><span data-stu-id="9e376-132">Skip navigation properties--covered by the next theme.</span></span>
* <span data-ttu-id="9e376-133">屬性包實體類型。</span><span class="sxs-lookup"><span data-stu-id="9e376-133">Property-bag entity types.</span></span> <span data-ttu-id="9e376-134">這些可讓標準的 CLR 型別 (例如 `Dictionary`) 用於實體實例，因此每個實體類型都不需要明確的 clr 型別。</span><span class="sxs-lookup"><span data-stu-id="9e376-134">These allow a standard CLR type (e.g. `Dictionary`) to be used for entity instances such that an explicit CLR type is not needed for each entity type.</span></span> <span data-ttu-id="9e376-135">由 [#9914](https://github.com/dotnet/efcore/issues/9914)追蹤。</span><span class="sxs-lookup"><span data-stu-id="9e376-135">Tracked by [#9914](https://github.com/dotnet/efcore/issues/9914).</span></span>
* <span data-ttu-id="9e376-136">方便設定多對多關聯性的方便。</span><span class="sxs-lookup"><span data-stu-id="9e376-136">Sugar for easy configuration of many-to-many relationships.</span></span>

<span data-ttu-id="9e376-137">除了略過流覽支援以外，我們現在會將多對多的其他區域提取至 EF Core 5.0，以提供完整的體驗。</span><span class="sxs-lookup"><span data-stu-id="9e376-137">In addition to the skip navigation support, we are now pulling these other areas of many-to-many into EF Core 5.0 so as to provide a complete experience.</span></span>

## <a name="many-to-many-navigation-properties-aka-skip-navigations"></a><span data-ttu-id="9e376-138">多對多導覽屬性 (. k. a "skip 導覽" ) </span><span class="sxs-lookup"><span data-stu-id="9e376-138">Many-to-many navigation properties (a.k.a "skip navigations")</span></span>

<span data-ttu-id="9e376-139">領導開發人員： @smitpatel 和 @AndriySvyryd</span><span class="sxs-lookup"><span data-stu-id="9e376-139">Lead developers: @smitpatel and @AndriySvyryd</span></span>

<span data-ttu-id="9e376-140">追蹤 [#19003](https://github.com/dotnet/efcore/issues/19003)</span><span class="sxs-lookup"><span data-stu-id="9e376-140">Tracked by [#19003](https://github.com/dotnet/efcore/issues/19003)</span></span>

<span data-ttu-id="9e376-141">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="9e376-141">T-shirt size: L</span></span>

<span data-ttu-id="9e376-142">狀態：完成</span><span class="sxs-lookup"><span data-stu-id="9e376-142">Status: Done</span></span>

<span data-ttu-id="9e376-143">如第一個主題所述，多對多支援有許多方面。</span><span class="sxs-lookup"><span data-stu-id="9e376-143">As described in the first theme, many-to-many support has multiple aspects.</span></span>
<span data-ttu-id="9e376-144">本主題專門追蹤略過導覽的使用。</span><span class="sxs-lookup"><span data-stu-id="9e376-144">This theme specifically tracks use of skip navigations.</span></span>
<span data-ttu-id="9e376-145">我們相信，需要多對多支援的最重要封鎖程式，無法在商務邏輯（例如查詢）中使用「自然」關聯性，而不需要參考聯結資料表。</span><span class="sxs-lookup"><span data-stu-id="9e376-145">We believe that the most significant blocker for those wanting many-to-many support is not being able to use the "natural" relationships, without referring to the join table, in business logic such as queries.</span></span>
<span data-ttu-id="9e376-146">聯結資料表實體類型可能仍存在，但不應該以商務邏輯的方式取得。</span><span class="sxs-lookup"><span data-stu-id="9e376-146">The join table entity type may still exist, but it should not get in the way of business logic.</span></span>

## <a name="table-per-type-tpt-inheritance-mapping"></a><span data-ttu-id="9e376-147">每一類型的資料表 (TPT) 繼承對應</span><span class="sxs-lookup"><span data-stu-id="9e376-147">Table-per-type (TPT) inheritance mapping</span></span>

<span data-ttu-id="9e376-148">首席開發人員： @AndriySvyryd 和 @smitpatel</span><span class="sxs-lookup"><span data-stu-id="9e376-148">Lead developer: @AndriySvyryd and @smitpatel</span></span>

<span data-ttu-id="9e376-149">追蹤 [#2266](https://github.com/dotnet/efcore/issues/2266)</span><span class="sxs-lookup"><span data-stu-id="9e376-149">Tracked by [#2266](https://github.com/dotnet/efcore/issues/2266)</span></span>

<span data-ttu-id="9e376-150">T 恤尺寸： XL</span><span class="sxs-lookup"><span data-stu-id="9e376-150">T-shirt size: XL</span></span>

<span data-ttu-id="9e376-151">狀態：完成</span><span class="sxs-lookup"><span data-stu-id="9e376-151">Status: Done</span></span>

<span data-ttu-id="9e376-152">我們正在進行 TPT，因為它是高度要求的功能 (~ 289 的投票;第三個整體) ，因為它需要一些低層級的變更，因此我們覺得適用于整體 .NET 5 計畫的基礎本質。</span><span class="sxs-lookup"><span data-stu-id="9e376-152">We're doing TPT because it is both a highly requested feature (~289 votes; 3rd overall) and because it requires some low-level changes that we feel are appropriate for the foundational nature of the overall .NET 5 plan.</span></span> <span data-ttu-id="9e376-153">我們預期這會造成資料庫提供者的重大變更，但這應該比3.0 所需的變更更不嚴重。</span><span class="sxs-lookup"><span data-stu-id="9e376-153">We expect this to result in breaking changes for database providers, although these should be much less severe than the changes required for 3.0.</span></span>

## <a name="filtered-include"></a><span data-ttu-id="9e376-154">篩選的包含</span><span class="sxs-lookup"><span data-stu-id="9e376-154">Filtered Include</span></span>

<span data-ttu-id="9e376-155">首席開發人員： @maumar</span><span class="sxs-lookup"><span data-stu-id="9e376-155">Lead developer: @maumar</span></span>

<span data-ttu-id="9e376-156">追蹤 [#1833](https://github.com/dotnet/efcore/issues/1833)</span><span class="sxs-lookup"><span data-stu-id="9e376-156">Tracked by [#1833](https://github.com/dotnet/efcore/issues/1833)</span></span>

<span data-ttu-id="9e376-157">T 恤尺寸： M</span><span class="sxs-lookup"><span data-stu-id="9e376-157">T-shirt size: M</span></span>

<span data-ttu-id="9e376-158">狀態：完成</span><span class="sxs-lookup"><span data-stu-id="9e376-158">Status: Done</span></span>

<span data-ttu-id="9e376-159">篩選包含高度要求的功能 (~ 376 的投票;第2個整體) 不是大量的工作，而且我們相信您會在目前需要模型層級篩選或更複雜查詢的情況下，解除封鎖或簡化許多案例。</span><span class="sxs-lookup"><span data-stu-id="9e376-159">Filtered Include is a highly-requested feature (~376 votes; 2nd overall) that isn't a huge amount of work, and that we believe will unblock or make easier many scenarios that currently require model-level filters or more complex queries.</span></span>

## <a name="split-include"></a><span data-ttu-id="9e376-160">分割包含</span><span class="sxs-lookup"><span data-stu-id="9e376-160">Split Include</span></span>

<span data-ttu-id="9e376-161">首席開發人員： @smitpatel</span><span class="sxs-lookup"><span data-stu-id="9e376-161">Lead developer: @smitpatel</span></span>

<span data-ttu-id="9e376-162">追蹤 [#20892](https://github.com/dotnet/efcore/issues/20892)</span><span class="sxs-lookup"><span data-stu-id="9e376-162">Tracked by [#20892](https://github.com/dotnet/efcore/issues/20892)</span></span>

<span data-ttu-id="9e376-163">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="9e376-163">T-shirt size: L</span></span>

<span data-ttu-id="9e376-164">狀態：完成</span><span class="sxs-lookup"><span data-stu-id="9e376-164">Status: Done</span></span>

<span data-ttu-id="9e376-165">EF Core 3.0 變更了預設行為，以針對指定的 LINQ 查詢建立單一 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="9e376-165">EF Core 3.0 changed the default behavior to create a single SQL query for a given LINQ query.</span></span>
<span data-ttu-id="9e376-166">這會對使用包含多個集合的查詢造成大型效能衰退。</span><span class="sxs-lookup"><span data-stu-id="9e376-166">This caused large performance regressions for queries that use Include for multiple collections.</span></span>

<span data-ttu-id="9e376-167">在 EF Core 5.0 中，我們會保留新的預設行為。</span><span class="sxs-lookup"><span data-stu-id="9e376-167">In EF Core 5.0, we are retaining the new default behavior.</span></span>
<span data-ttu-id="9e376-168">不過，EF Core 5.0 現在會允許產生多個集合的查詢，其中包含單一查詢導致效能不佳的情況。</span><span class="sxs-lookup"><span data-stu-id="9e376-168">However, EF Core 5.0 will now allow generation of multiple queries for collection Includes where having a single query is causing bad performance.</span></span>

## <a name="required-one-to-one-dependents"></a><span data-ttu-id="9e376-169">需要一對一相依項</span><span class="sxs-lookup"><span data-stu-id="9e376-169">Required one-to-one dependents</span></span>

<span data-ttu-id="9e376-170">領導開發人員： @AndriySvyryd 和 @smitpatel</span><span class="sxs-lookup"><span data-stu-id="9e376-170">Lead developers: @AndriySvyryd and @smitpatel</span></span>

<span data-ttu-id="9e376-171">追蹤 [#12100](https://github.com/dotnet/efcore/issues/12100)</span><span class="sxs-lookup"><span data-stu-id="9e376-171">Tracked by [#12100](https://github.com/dotnet/efcore/issues/12100)</span></span>

<span data-ttu-id="9e376-172">T 恤尺寸： M</span><span class="sxs-lookup"><span data-stu-id="9e376-172">T-shirt size: M</span></span>

<span data-ttu-id="9e376-173">狀態：完成</span><span class="sxs-lookup"><span data-stu-id="9e376-173">Status: Done</span></span>

<span data-ttu-id="9e376-174">在 EF Core 3.0 中，包括擁有的類型在內的所有相依性都是選擇性的 (例如，Person 可以是 null) 。</span><span class="sxs-lookup"><span data-stu-id="9e376-174">In EF Core 3.0, all dependents, including owned types are optional (e.g. Person.Address can be null).</span></span> <span data-ttu-id="9e376-175">在 EF Core 5.0 中，可以視需要設定相依項。</span><span class="sxs-lookup"><span data-stu-id="9e376-175">In EF Core 5.0, dependents can be configured as required.</span></span>

## <a name="rationalize-totable-toquery-toview-fromsql-etc"></a><span data-ttu-id="9e376-176">將 ToTable、ToQuery、ToView、FromSql 等合理化</span><span class="sxs-lookup"><span data-stu-id="9e376-176">Rationalize ToTable, ToQuery, ToView, FromSql, etc</span></span>

<span data-ttu-id="9e376-177">領導開發人員： @AndriySvyryd 和 @smitpatel</span><span class="sxs-lookup"><span data-stu-id="9e376-177">Lead developers: @AndriySvyryd and @smitpatel</span></span>

<span data-ttu-id="9e376-178">追蹤 [#17270](https://github.com/dotnet/efcore/issues/17270)</span><span class="sxs-lookup"><span data-stu-id="9e376-178">Tracked by [#17270](https://github.com/dotnet/efcore/issues/17270)</span></span>

<span data-ttu-id="9e376-179">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="9e376-179">T-shirt size: L</span></span>

<span data-ttu-id="9e376-180">狀態：完成</span><span class="sxs-lookup"><span data-stu-id="9e376-180">Status: Done</span></span>

<span data-ttu-id="9e376-181">在舊版中，我們已在支援原始 SQL、無索引鍵類型和相關區域的過程中進行了進展。</span><span class="sxs-lookup"><span data-stu-id="9e376-181">We have made progress in previous releases towards supporting raw SQL, keyless types, and related areas.</span></span> <span data-ttu-id="9e376-182">不過，所有專案整體運作的方式都有差距和不一致的情況。</span><span class="sxs-lookup"><span data-stu-id="9e376-182">However, there are both gaps and inconsistencies in the way everything works together as a whole.</span></span> <span data-ttu-id="9e376-183">5.0 的目標是要修正這些問題，並建立良好的體驗來定義、遷移和使用不同類型的實體及其相關聯的查詢和資料庫成品。</span><span class="sxs-lookup"><span data-stu-id="9e376-183">The goal for 5.0 is to fix these and create a good experience for defining, migrating, and using different types of entities and their associated queries and database artifacts.</span></span> <span data-ttu-id="9e376-184">這也可能牽涉到已編譯查詢 API 的更新。</span><span class="sxs-lookup"><span data-stu-id="9e376-184">This may also involve updates to the compiled query API.</span></span>

<span data-ttu-id="9e376-185">請注意，此專案可能會導致一些應用層級的重大變更，因為我們目前所擁有的一些功能過於寬鬆，因此可以快速地讓人們進入失敗的 pits。</span><span class="sxs-lookup"><span data-stu-id="9e376-185">Note that this item may result in some application-level breaking changes since some of the functionality we currently have is too permissive such that it can quickly lead people into pits of failure.</span></span> <span data-ttu-id="9e376-186">我們最後可能會封鎖這其中一些功能，以及應如何取代的指引。</span><span class="sxs-lookup"><span data-stu-id="9e376-186">We will likely end up blocking some of this functionality together with guidance on what to do instead.</span></span>

## <a name="general-query-enhancements"></a><span data-ttu-id="9e376-187">一般查詢增強功能</span><span class="sxs-lookup"><span data-stu-id="9e376-187">General query enhancements</span></span>

<span data-ttu-id="9e376-188">領導開發人員： @smitpatel 和 @maumar</span><span class="sxs-lookup"><span data-stu-id="9e376-188">Lead developers: @smitpatel and @maumar</span></span>

<span data-ttu-id="9e376-189">由[ `area-query` 5.0 里程碑中標記](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)為的問題追蹤</span><span class="sxs-lookup"><span data-stu-id="9e376-189">Tracked by [issues labeled with `area-query` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="9e376-190">T 恤尺寸： XL</span><span class="sxs-lookup"><span data-stu-id="9e376-190">T-shirt size: XL</span></span>

<span data-ttu-id="9e376-191">狀態：完成</span><span class="sxs-lookup"><span data-stu-id="9e376-191">Status: Done</span></span>

<span data-ttu-id="9e376-192">已針對 EF Core 3.0 大量重寫查詢轉譯程式碼。</span><span class="sxs-lookup"><span data-stu-id="9e376-192">The query translation code was extensively rewritten for EF Core 3.0.</span></span> <span data-ttu-id="9e376-193">由於這個原因，查詢程式碼通常會以更健全的狀態進行。</span><span class="sxs-lookup"><span data-stu-id="9e376-193">The query code is generally in a much more robust state because of this.</span></span> <span data-ttu-id="9e376-194">針對5.0，我們不打算在支援 TPT 和略過導覽屬性所需的外部進行主要查詢變更。</span><span class="sxs-lookup"><span data-stu-id="9e376-194">For 5.0 we aren't planning on making major query changes, outside those needed to support TPT and skip navigation properties.</span></span> <span data-ttu-id="9e376-195">不過，仍有很多工作需要修正從3.0 的檢修中留下的一些技術債務。</span><span class="sxs-lookup"><span data-stu-id="9e376-195">However, there is still significant work needed to fix some technical debt left over from the 3.0 overhaul.</span></span> <span data-ttu-id="9e376-196">我們也計畫修正許多 bug，並執行小規模的增強功能，進一步改善整體查詢體驗。</span><span class="sxs-lookup"><span data-stu-id="9e376-196">We also plan to fix many bugs and implement small enhancements to further improve the overall query experience.</span></span>

## <a name="migrations-and-deployment-experience"></a><span data-ttu-id="9e376-197">遷移和部署體驗</span><span class="sxs-lookup"><span data-stu-id="9e376-197">Migrations and deployment experience</span></span>

<span data-ttu-id="9e376-198">首席開發人員： @bricelam</span><span class="sxs-lookup"><span data-stu-id="9e376-198">Lead developers: @bricelam</span></span>

<span data-ttu-id="9e376-199">追蹤 [#19587](https://github.com/dotnet/efcore/issues/19587)</span><span class="sxs-lookup"><span data-stu-id="9e376-199">Tracked by [#19587](https://github.com/dotnet/efcore/issues/19587)</span></span>

<span data-ttu-id="9e376-200">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="9e376-200">T-shirt size: L</span></span>

<span data-ttu-id="9e376-201">狀態：限域/完成</span><span class="sxs-lookup"><span data-stu-id="9e376-201">Status: Scoped/Done</span></span>

<span data-ttu-id="9e376-202">範圍： [遷移組合功能](https://github.com/dotnet/efcore/issues/19693) 已延後，直到 EF Core 5.0 版本為止。</span><span class="sxs-lookup"><span data-stu-id="9e376-202">Scoping: The [migrations bundles feature](https://github.com/dotnet/efcore/issues/19693) has been deferred until after the EF Core 5.0 release.</span></span> <span data-ttu-id="9e376-203">不過， [與遷移相關](https://github.com/dotnet/efcore/issues/19587#issuecomment-668794460) 的數個其他目標改進將包含在 EF Core 5。0</span><span class="sxs-lookup"><span data-stu-id="9e376-203">However, several other [targeted improvements related to migrations](https://github.com/dotnet/efcore/issues/19587#issuecomment-668794460) will be included in EF Core 5.0</span></span>

<span data-ttu-id="9e376-204">目前，許多開發人員會在應用程式啟動時遷移其資料庫。</span><span class="sxs-lookup"><span data-stu-id="9e376-204">Currently, many developers migrate their databases at application startup time.</span></span> <span data-ttu-id="9e376-205">這很簡單，但不建議使用，因為：</span><span class="sxs-lookup"><span data-stu-id="9e376-205">This is easy but is not recommended because:</span></span>

* <span data-ttu-id="9e376-206">多個執行緒/進程/伺服器可能會嘗試同時遷移資料庫</span><span class="sxs-lookup"><span data-stu-id="9e376-206">Multiple threads/processes/servers may attempt to migrate the database concurrently</span></span>
* <span data-ttu-id="9e376-207">當發生這種情況時，應用程式可能會嘗試存取不一致的狀態</span><span class="sxs-lookup"><span data-stu-id="9e376-207">Applications may try to access inconsistent state while this is happening</span></span>
* <span data-ttu-id="9e376-208">通常不應針對應用程式執行，授與修改架構的資料庫許可權</span><span class="sxs-lookup"><span data-stu-id="9e376-208">Usually the database permissions to modify the schema should not be granted for application execution</span></span>
* <span data-ttu-id="9e376-209">如果發生錯誤，則很難還原為乾淨狀態</span><span class="sxs-lookup"><span data-stu-id="9e376-209">It's hard to revert back to a clean state if something goes wrong</span></span>

<span data-ttu-id="9e376-210">我們想要在此提供更好的體驗，讓您在部署時可以輕鬆地遷移資料庫。</span><span class="sxs-lookup"><span data-stu-id="9e376-210">We want to deliver a better experience here that allows an easy way to migrate the database at deployment time.</span></span> <span data-ttu-id="9e376-211">這應該是：</span><span class="sxs-lookup"><span data-stu-id="9e376-211">This should:</span></span>

* <span data-ttu-id="9e376-212">在 Linux、Mac 和 Windows 上工作</span><span class="sxs-lookup"><span data-stu-id="9e376-212">Work on Linux, Mac, and Windows</span></span>
* <span data-ttu-id="9e376-213">在命令列上提供絕佳的體驗</span><span class="sxs-lookup"><span data-stu-id="9e376-213">Be a good experience on the command line</span></span>
* <span data-ttu-id="9e376-214">使用容器的支援案例</span><span class="sxs-lookup"><span data-stu-id="9e376-214">Support scenarios with containers</span></span>
* <span data-ttu-id="9e376-215">使用常用的真實世界部署工具/流程</span><span class="sxs-lookup"><span data-stu-id="9e376-215">Work with commonly used real-world deployment tools/flows</span></span>
* <span data-ttu-id="9e376-216">至少整合到 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9e376-216">Integrate into at least Visual Studio</span></span>

<span data-ttu-id="9e376-217">結果可能是 EF Core 的許多小改良 (例如，在 SQLite) 上進行更佳的遷移，以及與其他小組的指引和長期共同作業，以改善超越 EF 的端對端體驗。</span><span class="sxs-lookup"><span data-stu-id="9e376-217">The result is likely to be many small improvements in EF Core (for example, better Migrations on SQLite), together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

## <a name="ef-core-platforms-experience"></a><span data-ttu-id="9e376-218">EF Core 平臺體驗</span><span class="sxs-lookup"><span data-stu-id="9e376-218">EF Core platforms experience</span></span>

<span data-ttu-id="9e376-219">領導開發人員： @roji 和 @bricelam</span><span class="sxs-lookup"><span data-stu-id="9e376-219">Lead developers: @roji and @bricelam</span></span>

<span data-ttu-id="9e376-220">追蹤 [#19588](https://github.com/dotnet/efcore/issues/19588)</span><span class="sxs-lookup"><span data-stu-id="9e376-220">Tracked by [#19588](https://github.com/dotnet/efcore/issues/19588)</span></span>

<span data-ttu-id="9e376-221">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="9e376-221">T-shirt size: L</span></span>

<span data-ttu-id="9e376-222">狀態：範圍/完成</span><span class="sxs-lookup"><span data-stu-id="9e376-222">Status: Scope/Done</span></span>

<span data-ttu-id="9e376-223">範圍：針對 Blazor、Xamarin、WinForms 和 WPF 發佈平臺指引和範例。</span><span class="sxs-lookup"><span data-stu-id="9e376-223">Scoping: Platform guidance and samples are published for Blazor, Xamarin, WinForms, and WPF.</span></span> <span data-ttu-id="9e376-224">現在已針對 EF Core 6.0 規劃 Xamarin 和其他 AOT/連結器工作。</span><span class="sxs-lookup"><span data-stu-id="9e376-224">Xamarin and other AOT/linker work is now planned for EF Core 6.0.</span></span>

<span data-ttu-id="9e376-225">對於在傳統的 MVC web 應用程式中使用 EF Core，我們有良好的指引。</span><span class="sxs-lookup"><span data-stu-id="9e376-225">We have good guidance for using EF Core in traditional MVC-like web applications.</span></span> <span data-ttu-id="9e376-226">其他平臺和應用程式模型的指引已遺失或已過期。</span><span class="sxs-lookup"><span data-stu-id="9e376-226">Guidance for other platforms and application models is either missing or out-of-date.</span></span> <span data-ttu-id="9e376-227">針對 EF Core 5.0，我們計畫調查、改進和記錄搭配使用 EF Core 的體驗：</span><span class="sxs-lookup"><span data-stu-id="9e376-227">For EF Core 5.0, we plan to investigate, improve, and document the experience of using EF Core with:</span></span>

* <span data-ttu-id="9e376-228">Blazor</span><span class="sxs-lookup"><span data-stu-id="9e376-228">Blazor</span></span>
* <span data-ttu-id="9e376-229">Xamarin，包括使用 AOT/連結器案例</span><span class="sxs-lookup"><span data-stu-id="9e376-229">Xamarin, including using the AOT/linker story</span></span>
* <span data-ttu-id="9e376-230">WinForms/WPF/WinUI，可能還有其他 U.I。</span><span class="sxs-lookup"><span data-stu-id="9e376-230">WinForms/WPF/WinUI and possibly other U.I.</span></span> <span data-ttu-id="9e376-231">frameworks</span><span class="sxs-lookup"><span data-stu-id="9e376-231">frameworks</span></span>

<span data-ttu-id="9e376-232">這可能是 EF Core 中的許多小改良，以及與其他小組的指引和長期共同作業，以改善超越 EF 的端對端體驗。</span><span class="sxs-lookup"><span data-stu-id="9e376-232">This is likely to be many small improvements in EF Core, together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

<span data-ttu-id="9e376-233">我們打算查看的特定區域包括：</span><span class="sxs-lookup"><span data-stu-id="9e376-233">Specific areas we plan to look at are:</span></span>

* <span data-ttu-id="9e376-234">部署，包括使用 EF 工具（例如遷移）的體驗</span><span class="sxs-lookup"><span data-stu-id="9e376-234">Deployment, including the experience for using EF tooling such as for Migrations</span></span>
* <span data-ttu-id="9e376-235">應用程式模型（包括 Xamarin 和 Blazor），也可能是其他模型</span><span class="sxs-lookup"><span data-stu-id="9e376-235">Application models, including Xamarin and Blazor, and probably others</span></span>
* <span data-ttu-id="9e376-236">SQLite 體驗，包括空間體驗和資料表重建</span><span class="sxs-lookup"><span data-stu-id="9e376-236">SQLite experiences, including the spatial experience and table rebuilds</span></span>
* <span data-ttu-id="9e376-237">AOT 和連結體驗</span><span class="sxs-lookup"><span data-stu-id="9e376-237">AOT and linking experiences</span></span>
* <span data-ttu-id="9e376-238">診斷整合，包括效能計數器</span><span class="sxs-lookup"><span data-stu-id="9e376-238">Diagnostics integration, including perf counters</span></span>

## <a name="performance"></a><span data-ttu-id="9e376-239">效能</span><span class="sxs-lookup"><span data-stu-id="9e376-239">Performance</span></span>

<span data-ttu-id="9e376-240">首席開發人員： @roji</span><span class="sxs-lookup"><span data-stu-id="9e376-240">Lead developer: @roji</span></span>

<span data-ttu-id="9e376-241">由[ `area-perf` 5.0 里程碑中標記](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)為的問題追蹤</span><span class="sxs-lookup"><span data-stu-id="9e376-241">Tracked by [issues labeled with `area-perf` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="9e376-242">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="9e376-242">T-shirt size: L</span></span>

<span data-ttu-id="9e376-243">狀態：限域/完成</span><span class="sxs-lookup"><span data-stu-id="9e376-243">Status: Scoped/Done</span></span>

<span data-ttu-id="9e376-244">範圍： Npgsql 提供者的主要效能改進已完成。</span><span class="sxs-lookup"><span data-stu-id="9e376-244">Scoping: Major performance improvements in the Npgsql provider are complete.</span></span> <span data-ttu-id="9e376-245">現在已針對 EF Core 6.0 規劃其他效能工作。</span><span class="sxs-lookup"><span data-stu-id="9e376-245">Other performance work is now planned for EF Core 6.0.</span></span>

<span data-ttu-id="9e376-246">針對 EF Core，我們計畫改善效能基準測試套件，並對執行時間進行導向的效能改進。</span><span class="sxs-lookup"><span data-stu-id="9e376-246">For EF Core, we plan to improve our suite of performance benchmarks and make directed performance improvements to the runtime.</span></span> <span data-ttu-id="9e376-247">此外，我們計畫在3.0 發行週期期間，完成新的 ADO.NET 批次處理 API。</span><span class="sxs-lookup"><span data-stu-id="9e376-247">In addition, we plan to complete the new ADO.NET batching API which was prototyped during the 3.0 release cycle.</span></span> <span data-ttu-id="9e376-248">此外，在 ADO.NET 層，我們會為 Npgsql 提供者規劃額外的效能改進。</span><span class="sxs-lookup"><span data-stu-id="9e376-248">Also at the ADO.NET layer, we plan additional performance improvements to the Npgsql provider.</span></span>

<span data-ttu-id="9e376-249">在這項工作中，我們也計畫在適當的情況下新增 ADO.NET/EF 核心效能計數器和其他診斷。</span><span class="sxs-lookup"><span data-stu-id="9e376-249">As part of this work we also plan to add ADO.NET/EF Core performance counters and other diagnostics as appropriate.</span></span>

## <a name="architecturalcontributor-documentation"></a><span data-ttu-id="9e376-250">架構/參與者檔</span><span class="sxs-lookup"><span data-stu-id="9e376-250">Architectural/contributor documentation</span></span>

<span data-ttu-id="9e376-251">主管文檔： @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="9e376-251">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="9e376-252">追蹤 [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)</span><span class="sxs-lookup"><span data-stu-id="9e376-252">Tracked by [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)</span></span>

<span data-ttu-id="9e376-253">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="9e376-253">T-shirt size: L</span></span>

<span data-ttu-id="9e376-254">狀態：剪下</span><span class="sxs-lookup"><span data-stu-id="9e376-254">Status: Cut</span></span>

<span data-ttu-id="9e376-255">這裡的目的是要讓您更輕鬆地瞭解 EF Core 內部的進展。</span><span class="sxs-lookup"><span data-stu-id="9e376-255">The idea here is to make it easier to understand what is going on in the internals of EF Core.</span></span> <span data-ttu-id="9e376-256">這對任何使用 EF Core 的人都很有用，但主要動機是讓外部人員更輕鬆地：</span><span class="sxs-lookup"><span data-stu-id="9e376-256">This can be useful to anyone using EF Core, but the primary motivation is to make it easier for external people to:</span></span>

* <span data-ttu-id="9e376-257">參與 EF Core 程式碼</span><span class="sxs-lookup"><span data-stu-id="9e376-257">Contribute to the EF Core code</span></span>
* <span data-ttu-id="9e376-258">建立資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="9e376-258">Create database providers</span></span>
* <span data-ttu-id="9e376-259">建立其他延伸模組</span><span class="sxs-lookup"><span data-stu-id="9e376-259">Build other extensions</span></span>

<span data-ttu-id="9e376-260">更新：很可惜，此計畫太確保建構完善。</span><span class="sxs-lookup"><span data-stu-id="9e376-260">Update: Unfortunately, this plan was too ambitious.</span></span>
<span data-ttu-id="9e376-261">我們仍然認為這很重要，但不幸的是，它不會 EF Core 5.0。</span><span class="sxs-lookup"><span data-stu-id="9e376-261">We still believe this is important, but unfortunately it won't land with EF Core 5.0.</span></span>

## <a name="microsoftdatasqlite-documentation"></a><span data-ttu-id="9e376-262">Microsoft. Sqlite 檔</span><span class="sxs-lookup"><span data-stu-id="9e376-262">Microsoft.Data.Sqlite documentation</span></span>

<span data-ttu-id="9e376-263">主管文檔： @bricelam</span><span class="sxs-lookup"><span data-stu-id="9e376-263">Lead documenter: @bricelam</span></span>

<span data-ttu-id="9e376-264">追蹤 [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)</span><span class="sxs-lookup"><span data-stu-id="9e376-264">Tracked by [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)</span></span>

<span data-ttu-id="9e376-265">T 恤尺寸： M</span><span class="sxs-lookup"><span data-stu-id="9e376-265">T-shirt size: M</span></span>

<span data-ttu-id="9e376-266">狀態：已完成。</span><span class="sxs-lookup"><span data-stu-id="9e376-266">Status: Completed.</span></span> <span data-ttu-id="9e376-267">新的檔位於 [Microsoft 檔網站上](/dotnet/standard/data/sqlite/?tabs=netcore-cli)。</span><span class="sxs-lookup"><span data-stu-id="9e376-267">The new documentation is [live on the Microsoft docs site](/dotnet/standard/data/sqlite/?tabs=netcore-cli).</span></span>

<span data-ttu-id="9e376-268">EF 小組也擁有 ADO.NET 提供者。</span><span class="sxs-lookup"><span data-stu-id="9e376-268">The EF Team also owns the Microsoft.Data.Sqlite ADO.NET provider.</span></span> <span data-ttu-id="9e376-269">我們計畫在5.0 版中完整記錄此提供者。</span><span class="sxs-lookup"><span data-stu-id="9e376-269">We plan to fully document this provider as part of the 5.0 release.</span></span>

## <a name="general-documentation"></a><span data-ttu-id="9e376-270">一般檔</span><span class="sxs-lookup"><span data-stu-id="9e376-270">General documentation</span></span>

<span data-ttu-id="9e376-271">主管文檔： @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="9e376-271">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="9e376-272">由[5.0 里程碑的](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)檔存放庫中的問題所追蹤</span><span class="sxs-lookup"><span data-stu-id="9e376-272">Tracked by [issues in the docs repo in the 5.0 milestone](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="9e376-273">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="9e376-273">T-shirt size: L</span></span>

<span data-ttu-id="9e376-274">狀態：進行中</span><span class="sxs-lookup"><span data-stu-id="9e376-274">Status: In-progress</span></span>

<span data-ttu-id="9e376-275">我們已在更新3.0 和3.1 版的檔集。</span><span class="sxs-lookup"><span data-stu-id="9e376-275">We are already in the process of updating documentation for the 3.0 and 3.1 releases.</span></span> <span data-ttu-id="9e376-276">我們也在努力：</span><span class="sxs-lookup"><span data-stu-id="9e376-276">We are also working on:</span></span>

* <span data-ttu-id="9e376-277">深入瞭解使用者入門檔，使其更平易近人/更容易遵循</span><span class="sxs-lookup"><span data-stu-id="9e376-277">An overhaul of the getting started docs to make them more approachable/easier to follow</span></span>
* <span data-ttu-id="9e376-278">重新組織檔，讓您更容易尋找及新增交叉參考</span><span class="sxs-lookup"><span data-stu-id="9e376-278">Reorganization of docs to make things easier to find and to add cross-references</span></span>
* <span data-ttu-id="9e376-279">新增更多詳細資料並對現有檔進行說明</span><span class="sxs-lookup"><span data-stu-id="9e376-279">Adding more details and clarifications to existing docs</span></span>
* <span data-ttu-id="9e376-280">更新範例和新增更多範例</span><span class="sxs-lookup"><span data-stu-id="9e376-280">Updating the samples and adding more examples</span></span>

## <a name="fixing-bugs"></a><span data-ttu-id="9e376-281">修正 bug</span><span class="sxs-lookup"><span data-stu-id="9e376-281">Fixing bugs</span></span>

<span data-ttu-id="9e376-282">由[ `type-bug` 5.0 里程碑中標記](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)為的問題追蹤</span><span class="sxs-lookup"><span data-stu-id="9e376-282">Tracked by [issues labeled with `type-bug` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)</span></span>

<span data-ttu-id="9e376-283">開發人員： @roji 、 @maumar 、 @bricelam 、 @smitpatel 、 @AndriySvyryd 、 @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="9e376-283">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="9e376-284">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="9e376-284">T-shirt size: L</span></span>

<span data-ttu-id="9e376-285">狀態：進行中</span><span class="sxs-lookup"><span data-stu-id="9e376-285">Status: In-progress</span></span>

<span data-ttu-id="9e376-286">在撰寫本文時，我們會將135的 bug 分級為在5.0 版 (中修正（62已) 修正），但是上述的 _一般查詢增強功能_ 一節會有顯著的重迭。</span><span class="sxs-lookup"><span data-stu-id="9e376-286">At the time of writing, we have 135 bugs triaged to be fixed in the 5.0 release (with 62 already fixed), but there is significant overlap with the _General query enhancements_ section above.</span></span>

<span data-ttu-id="9e376-287">傳入的速率 (的問題最後是里程碑的工作) 在3.0 版本的過程中，每個月大約23個問題。</span><span class="sxs-lookup"><span data-stu-id="9e376-287">The incoming rate (issues that end up as work in a milestone) was about 23 issues per month over the course of the 3.0 release.</span></span> <span data-ttu-id="9e376-288">並非所有這些都必須在5.0 中修正。</span><span class="sxs-lookup"><span data-stu-id="9e376-288">Not all of these will need to be fixed in 5.0.</span></span> <span data-ttu-id="9e376-289">大致上，我們打算修正5.0 時間範圍中的額外150問題。</span><span class="sxs-lookup"><span data-stu-id="9e376-289">As a rough estimate we plan to fix an additional 150 issues in the 5.0 time frame.</span></span>

## <a name="small-enhancements"></a><span data-ttu-id="9e376-290">小型增強功能</span><span class="sxs-lookup"><span data-stu-id="9e376-290">Small enhancements</span></span>

<span data-ttu-id="9e376-291">由[ `type-enhancement` 5.0 里程碑中標記](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)為的問題追蹤</span><span class="sxs-lookup"><span data-stu-id="9e376-291">Tracked by [issues labeled with `type-enhancement` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)</span></span>

<span data-ttu-id="9e376-292">開發人員： @roji 、 @maumar 、 @bricelam 、 @smitpatel 、 @AndriySvyryd 、 @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="9e376-292">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="9e376-293">T 恤尺寸： L</span><span class="sxs-lookup"><span data-stu-id="9e376-293">T-shirt size: L</span></span>

<span data-ttu-id="9e376-294">狀態：完成</span><span class="sxs-lookup"><span data-stu-id="9e376-294">Status: Done</span></span>

<span data-ttu-id="9e376-295">除了上面所述的更大功能之外，我們也為5.0 排定了許多較小的改進，以修正「紙張-剪下」。</span><span class="sxs-lookup"><span data-stu-id="9e376-295">In addition to the bigger features outlined above, we also have many smaller improvements scheduled for 5.0 to fix "paper-cuts".</span></span> <span data-ttu-id="9e376-296">請注意，上述的許多增強功能也都包含在上面所述的一般主題中。</span><span class="sxs-lookup"><span data-stu-id="9e376-296">Note that many of these enhancements are also covered by the more general themes outlined above.</span></span>

## <a name="below-the-line"></a><span data-ttu-id="9e376-297">以下-行</span><span class="sxs-lookup"><span data-stu-id="9e376-297">Below-the-line</span></span>

<span data-ttu-id="9e376-298">由[標記為的 `consider-for-next-release` 問題](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)追蹤</span><span class="sxs-lookup"><span data-stu-id="9e376-298">Tracked by [issues labeled with `consider-for-next-release`](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span></span>

<span data-ttu-id="9e376-299">這些是目前 **尚未** 針對5.0 版本排程的 bug 修正和增強功能，但我們將根據上述工作的進度來查看延展目標。</span><span class="sxs-lookup"><span data-stu-id="9e376-299">These are bug fixes and enhancements that are **not** currently scheduled for the 5.0 release, but we will look at as stretch goals depending on the progress made on the work above.</span></span>

<span data-ttu-id="9e376-300">此外，我們在規劃時，一律會考慮 [最投票的問題](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) 。</span><span class="sxs-lookup"><span data-stu-id="9e376-300">In addition, we always consider the [most voted issues](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) when planning.</span></span> <span data-ttu-id="9e376-301">從版本中剪下這些問題一直都是很麻煩的，但我們的確需要針對我們所擁有的資源進行實際的規劃。</span><span class="sxs-lookup"><span data-stu-id="9e376-301">Cutting any of these issues from a release is always painful, but we do need a realistic plan for the resources we have.</span></span>

## <a name="suggestions"></a><span data-ttu-id="9e376-302">建議</span><span class="sxs-lookup"><span data-stu-id="9e376-302">Suggestions</span></span>

<span data-ttu-id="9e376-303">您對計劃的意見反應很重要。</span><span class="sxs-lookup"><span data-stu-id="9e376-303">Your feedback on planning is important.</span></span> <span data-ttu-id="9e376-304">若要指出問題的重要性，最佳方式是在 GitHub 上針對該問題投票 (大拇指)。</span><span class="sxs-lookup"><span data-stu-id="9e376-304">The best way to indicate the importance of an issue is to vote (thumbs-up) for that issue on GitHub.</span></span> <span data-ttu-id="9e376-305">然後，此資料會送入下一版的 [規劃](xref:core/what-is-new/release-planning) 程式。</span><span class="sxs-lookup"><span data-stu-id="9e376-305">This data will then feed into the [planning process](xref:core/what-is-new/release-planning) for the next release.</span></span>
