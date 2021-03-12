---
title: 規劃 Entity Framework Core 6。0
description: 針對 EF Core 6.0 規劃的主題和功能
author: ajcvickers
ms.date: 01/12/2021
uid: core/what-is-new/ef-core-6.0/plan
ms.openlocfilehash: d6d4f957a57d8ab6a11232b66a5b2a1238df59a3
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023480"
---
# <a name="plan-for-entity-framework-core-60"></a><span data-ttu-id="123d0-103">規劃 Entity Framework Core 6。0</span><span class="sxs-lookup"><span data-stu-id="123d0-103">Plan for Entity Framework Core 6.0</span></span>

<span data-ttu-id="123d0-104">如 [規劃](xref:core/what-is-new/release-planning)程式中所述，我們已將專案關係人的輸入收集到 Entity Framework CORE (EF Core) 6.0 版的方案。</span><span class="sxs-lookup"><span data-stu-id="123d0-104">As described in the [planning process](xref:core/what-is-new/release-planning), we have gathered input from stakeholders into a plan for the Entity Framework Core (EF Core) 6.0 release.</span></span>

<span data-ttu-id="123d0-105">與舊版不同的是，此計畫不會嘗試涵蓋6.0 版本的所有工作。</span><span class="sxs-lookup"><span data-stu-id="123d0-105">Unlike previous releases, this plan does not attempt to cover all work for the 6.0 release.</span></span> <span data-ttu-id="123d0-106">相反地，它會指出我們要在此版本中投資的位置和方式，但在我們收集意見反應並學習發行時，您可以彈性地調整範圍或提取新的工作。</span><span class="sxs-lookup"><span data-stu-id="123d0-106">Instead, it indicates where and how we intend to invest in this release, but with flexibility to adjust scope or pull in new work as we gather feedback and learn while working on the release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="123d0-107">此計畫不是承諾用量。</span><span class="sxs-lookup"><span data-stu-id="123d0-107">This plan is not a commitment.</span></span> <span data-ttu-id="123d0-108">它是將隨著我們深入瞭解而演進的起點。</span><span class="sxs-lookup"><span data-stu-id="123d0-108">It is a starting point that will evolve as we learn more.</span></span> <span data-ttu-id="123d0-109">目前未規劃6.0 的某些專案可能會被提取。</span><span class="sxs-lookup"><span data-stu-id="123d0-109">Some things not currently planned for 6.0 may get pulled in.</span></span> <span data-ttu-id="123d0-110">目前已規劃6.0 的某些專案可能會一度。</span><span class="sxs-lookup"><span data-stu-id="123d0-110">Some things currently planned for 6.0 may get punted out.</span></span>

## <a name="general-information"></a><span data-ttu-id="123d0-111">一般資訊</span><span class="sxs-lookup"><span data-stu-id="123d0-111">General information</span></span>

### <a name="version-number-and-release-date"></a><span data-ttu-id="123d0-112">版本號碼和發行日期</span><span class="sxs-lookup"><span data-stu-id="123d0-112">Version number and release date</span></span>

<span data-ttu-id="123d0-113">EF core 6.0 是 EF Core 5.0 之後的下一個版本，目前已排程在11月2021與 .NET 6 相同的時間內發行。</span><span class="sxs-lookup"><span data-stu-id="123d0-113">EF Core 6.0 is the next release after EF Core 5.0 and is currently scheduled for release in November 2021 at the same time as .NET 6.</span></span>

### <a name="supported-platforms"></a><span data-ttu-id="123d0-114">支援的平台</span><span class="sxs-lookup"><span data-stu-id="123d0-114">Supported platforms</span></span>

<span data-ttu-id="123d0-115">EF Core 6.0 目前以 .NET 5 為目標。</span><span class="sxs-lookup"><span data-stu-id="123d0-115">EF Core 6.0 currently targets .NET 5.</span></span> <span data-ttu-id="123d0-116">這可能會隨著我們附近的版本而更新為 .NET 6。</span><span class="sxs-lookup"><span data-stu-id="123d0-116">This will likely be updated to .NET 6 as we near the release.</span></span> <span data-ttu-id="123d0-117">EF Core 6.0 不以任何 .NET Standard 版本為目標;如需詳細資訊，請參閱 [.Net Standard 的未來](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/)。</span><span class="sxs-lookup"><span data-stu-id="123d0-117">EF Core 6.0 does not target any .NET Standard version; for more information see [the future of .NET Standard](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/).</span></span>

<span data-ttu-id="123d0-118">EF Core 6.0 不會在 .NET Framework 上執行。</span><span class="sxs-lookup"><span data-stu-id="123d0-118">EF Core 6.0 will not run on .NET Framework.</span></span>

<span data-ttu-id="123d0-119">EF Core 6.0 將與 .NET 6 一致 [ (LTS) 版本的長期支援](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="123d0-119">EF Core 6.0 will align with .NET 6 as a [long-term support (LTS) release](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="123d0-120">重大變更</span><span class="sxs-lookup"><span data-stu-id="123d0-120">Breaking changes</span></span>

<span data-ttu-id="123d0-121">EF Core 6.0 將包含少量的 [重大變更](xref:core/what-is-new/ef-core-6.0/breaking-changes) ，因為我們會持續演進 EF core 和 .net 平臺。</span><span class="sxs-lookup"><span data-stu-id="123d0-121">EF Core 6.0 will contain a small number of [breaking changes](xref:core/what-is-new/ef-core-6.0/breaking-changes) as we continue to evolve both EF Core and the .NET platform.</span></span> <span data-ttu-id="123d0-122">我們的目標是要讓大部分的應用程式在不中斷的情況下進行更新。</span><span class="sxs-lookup"><span data-stu-id="123d0-122">Our goal is to allow the vast majority of applications to update without breaking.</span></span>

## <a name="themes"></a><span data-ttu-id="123d0-123">佈景主題</span><span class="sxs-lookup"><span data-stu-id="123d0-123">Themes</span></span>

<span data-ttu-id="123d0-124">下欄區域將構成 EF Core 6.0 中的大型投資基礎。</span><span class="sxs-lookup"><span data-stu-id="123d0-124">The following areas will form the basis for the large investments in EF Core 6.0.</span></span>

## <a name="highly-requested-features"></a><span data-ttu-id="123d0-125">高度要求的功能</span><span class="sxs-lookup"><span data-stu-id="123d0-125">Highly requested features</span></span>

<span data-ttu-id="123d0-126">同樣地，在 [規劃過程](xref:core/what-is-new/release-planning) 中，主要的輸入是來自 [ 👍 GitHub 上功能的投票 () ](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc)。</span><span class="sxs-lookup"><span data-stu-id="123d0-126">As always, a major input into the [planning process](xref:core/what-is-new/release-planning) comes from the [voting (👍) for features on GitHub](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc).</span></span> <span data-ttu-id="123d0-127">針對 EF Core 6.0，我們計畫使用下列高度要求的功能：</span><span class="sxs-lookup"><span data-stu-id="123d0-127">For EF Core 6.0 we plan to work on the following highly requested features:</span></span>

### <a name="sql-server-temporal-tables"></a><span data-ttu-id="123d0-128">SQL Server 時態表</span><span class="sxs-lookup"><span data-stu-id="123d0-128">SQL Server temporal tables</span></span>

<span data-ttu-id="123d0-129">追蹤 [#4693](https://github.com/dotnet/efcore/issues/4693)</span><span class="sxs-lookup"><span data-stu-id="123d0-129">Tracked by [#4693](https://github.com/dotnet/efcore/issues/4693)</span></span>

<span data-ttu-id="123d0-130">狀態：未啟動</span><span class="sxs-lookup"><span data-stu-id="123d0-130">Status: Not started</span></span>

<span data-ttu-id="123d0-131">T 恤尺寸：大型</span><span class="sxs-lookup"><span data-stu-id="123d0-131">T-shirt size: Large</span></span>

<span data-ttu-id="123d0-132">時態表支援在 _任何時間點_，查詢儲存在資料表中的資料，而不只是一般資料表所儲存的最新資料。</span><span class="sxs-lookup"><span data-stu-id="123d0-132">Temporal tables support queries for data stored in the table at _any point in time_, as opposed to only the most recent data stored as is the case for normal tables.</span></span> <span data-ttu-id="123d0-133">EF Core 6.0 可讓您透過遷移來建立時態表，並允許透過 LINQ 查詢來存取資料。</span><span class="sxs-lookup"><span data-stu-id="123d0-133">EF Core 6.0 will allow temporal tables to be created via Migrations, as well as allowing access to the data through LINQ queries.</span></span>

<span data-ttu-id="123d0-134">這項工作一開始的範圍是此 [問題的描述](https://github.com/dotnet/efcore/issues/4693#issuecomment-625048974)。</span><span class="sxs-lookup"><span data-stu-id="123d0-134">This work is initially scoped as [described on the issue](https://github.com/dotnet/efcore/issues/4693#issuecomment-625048974).</span></span> <span data-ttu-id="123d0-135">我們可能會根據發行期間的意見反應，提取其他支援。</span><span class="sxs-lookup"><span data-stu-id="123d0-135">We may pull in additional support based on feedback during the release.</span></span>

### <a name="json-columns"></a><span data-ttu-id="123d0-136">JSON 資料行</span><span class="sxs-lookup"><span data-stu-id="123d0-136">JSON columns</span></span>

<span data-ttu-id="123d0-137">追蹤 [#4021](https://github.com/dotnet/efcore/issues/4021)</span><span class="sxs-lookup"><span data-stu-id="123d0-137">Tracked by [#4021](https://github.com/dotnet/efcore/issues/4021)</span></span>

<span data-ttu-id="123d0-138">狀態：未啟動</span><span class="sxs-lookup"><span data-stu-id="123d0-138">Status: Not started</span></span>

<span data-ttu-id="123d0-139">T 恤尺寸：中</span><span class="sxs-lookup"><span data-stu-id="123d0-139">T-shirt size: Medium</span></span>

<span data-ttu-id="123d0-140">這項功能會介紹可由任何資料庫提供者所執行之 JSON 支援的常見機制和模式。</span><span class="sxs-lookup"><span data-stu-id="123d0-140">This feature will introduce a common mechanism and patterns for JSON support that can be implemented by any database provider.</span></span> <span data-ttu-id="123d0-141">我們將與此社區合作，將 [Npgsql](https://github.com/npgsql/efcore.pg) 和 [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)的現有執行保持一致，並同時新增對 SQL Server 和 SQLite 的支援。</span><span class="sxs-lookup"><span data-stu-id="123d0-141">We will work with the community to align existing implementations for [Npgsql](https://github.com/npgsql/efcore.pg) and [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql), and also add support for SQL Server and SQLite.</span></span>

### <a name="columnattributeorder"></a><span data-ttu-id="123d0-142">ColumnAttribute。訂單</span><span class="sxs-lookup"><span data-stu-id="123d0-142">ColumnAttribute.Order</span></span>

<span data-ttu-id="123d0-143">追蹤 [#10059](https://github.com/dotnet/efcore/issues/10059)</span><span class="sxs-lookup"><span data-stu-id="123d0-143">Tracked by [#10059](https://github.com/dotnet/efcore/issues/10059)</span></span>

<span data-ttu-id="123d0-144">狀態：未啟動</span><span class="sxs-lookup"><span data-stu-id="123d0-144">Status: Not started</span></span>

<span data-ttu-id="123d0-145">T 恤尺寸：小型</span><span class="sxs-lookup"><span data-stu-id="123d0-145">T-shirt size: Small</span></span>

<span data-ttu-id="123d0-146">使用遷移或 **建立資料表** 時，這項功能將允許任意順序的資料行 `EnsureCreated` 。</span><span class="sxs-lookup"><span data-stu-id="123d0-146">This feature will allow arbitrary ordering of columns when **creating a table** with Migrations or `EnsureCreated`.</span></span> <span data-ttu-id="123d0-147">請注意，變更現有資料表中的資料行順序需要重建資料表，但這不是我們計畫在任何 EF Core 版本中支援的功能。</span><span class="sxs-lookup"><span data-stu-id="123d0-147">Note that changing the order of columns in an existing tables requires that the table be rebuilt, and this is not something that we plan to support in any EF Core release.</span></span>

## <a name="performance"></a><span data-ttu-id="123d0-148">效能</span><span class="sxs-lookup"><span data-stu-id="123d0-148">Performance</span></span>

<span data-ttu-id="123d0-149">雖然 EF Core 的速度通常比 EF6 還快，但仍有一些可能會大幅改善效能的區域。</span><span class="sxs-lookup"><span data-stu-id="123d0-149">While EF Core is generally faster than EF6, there are still areas where significant improvements in performance are possible.</span></span> <span data-ttu-id="123d0-150">我們計畫在 EF Core 6.0 中處理數個區域，同時改善我們的效能基礎結構和測試。</span><span class="sxs-lookup"><span data-stu-id="123d0-150">We plan to tackle several of these areas in EF Core 6.0, while also improving our perf infrastructure and testing.</span></span>

<span data-ttu-id="123d0-151">本主題將牽涉到許多反復調查，以通知我們將資源放在哪裡。</span><span class="sxs-lookup"><span data-stu-id="123d0-151">This theme will involve a lot of iterative investigation, which will inform where we focus resources.</span></span> <span data-ttu-id="123d0-152">我們計畫開始：</span><span class="sxs-lookup"><span data-stu-id="123d0-152">We plan to begin with:</span></span>

### <a name="performance-infrastructure-and-new-tests"></a><span data-ttu-id="123d0-153">效能基礎結構和新的測試</span><span class="sxs-lookup"><span data-stu-id="123d0-153">Performance infrastructure and new tests</span></span>

<span data-ttu-id="123d0-154">狀態：未啟動</span><span class="sxs-lookup"><span data-stu-id="123d0-154">Status: Not started</span></span>

<span data-ttu-id="123d0-155">T 恤尺寸：中</span><span class="sxs-lookup"><span data-stu-id="123d0-155">T-shirt size: Medium</span></span>

<span data-ttu-id="123d0-156">EF Core 程式碼基底已包含一組每日執行的效能基準測試。</span><span class="sxs-lookup"><span data-stu-id="123d0-156">The EF Core codebase already contains a set of performance benchmarks that are executed every day.</span></span> <span data-ttu-id="123d0-157">針對6.0，我們計畫改善這些測試的基礎結構，以及加入新的測試。</span><span class="sxs-lookup"><span data-stu-id="123d0-157">For 6.0, we plan to improve the infrastructure for these tests as well as adding new tests.</span></span> <span data-ttu-id="123d0-158">我們也會分析主線效能案例，並修正找到的任何低度水果。</span><span class="sxs-lookup"><span data-stu-id="123d0-158">We will also profile mainline perf scenarios and fix any low-hanging fruit found.</span></span>

### <a name="compiled-models"></a><span data-ttu-id="123d0-159">編譯的模型</span><span class="sxs-lookup"><span data-stu-id="123d0-159">Compiled models</span></span>

<span data-ttu-id="123d0-160">追蹤 [#1906](https://github.com/dotnet/efcore/issues/1906)</span><span class="sxs-lookup"><span data-stu-id="123d0-160">Tracked by [#1906](https://github.com/dotnet/efcore/issues/1906)</span></span>

<span data-ttu-id="123d0-161">狀態：未啟動</span><span class="sxs-lookup"><span data-stu-id="123d0-161">Status: Not started</span></span>

<span data-ttu-id="123d0-162">T 恤尺寸： X-大型</span><span class="sxs-lookup"><span data-stu-id="123d0-162">T-shirt size: X-Large</span></span>

<span data-ttu-id="123d0-163">編譯的模型可讓您產生已編譯的 EF 模型形式。</span><span class="sxs-lookup"><span data-stu-id="123d0-163">Compiled models will allow the generation of a compiled form of the EF model.</span></span> <span data-ttu-id="123d0-164">這會提供更好的啟動效能，以及存取模型時的效能通常較佳。</span><span class="sxs-lookup"><span data-stu-id="123d0-164">This will provide both better startup performance, as well as generally better performance when accessing the model.</span></span>

### <a name="techempower-fortunes"></a><span data-ttu-id="123d0-165">TechEmpower Fortunes</span><span class="sxs-lookup"><span data-stu-id="123d0-165">TechEmpower Fortunes</span></span>

<span data-ttu-id="123d0-166">追蹤 [#23611](https://github.com/dotnet/efcore/issues/23611)</span><span class="sxs-lookup"><span data-stu-id="123d0-166">Tracked by [#23611](https://github.com/dotnet/efcore/issues/23611)</span></span>

<span data-ttu-id="123d0-167">狀態：未啟動</span><span class="sxs-lookup"><span data-stu-id="123d0-167">Status: Not started</span></span>

<span data-ttu-id="123d0-168">T 恤尺寸： X-大型</span><span class="sxs-lookup"><span data-stu-id="123d0-168">T-shirt size: X-Large</span></span>

<span data-ttu-id="123d0-169">我們已針對于 postgresql 資料庫在 .NET 上執行業界標準的 [TechEmpower 基準](https://www.techempower.com/benchmarks/) 檢驗，多年來。</span><span class="sxs-lookup"><span data-stu-id="123d0-169">We have been running the industry standard [TechEmpower benchmarks](https://www.techempower.com/benchmarks/) on .NET against a PostgreSQL database for several years.</span></span> <span data-ttu-id="123d0-170">[Fortunes 基準測試](https://www.techempower.com/benchmarks/#section=data-r19&hw=ph&test=fortune)與 EF 案例特別相關。</span><span class="sxs-lookup"><span data-stu-id="123d0-170">The [Fortunes benchmark](https://www.techempower.com/benchmarks/#section=data-r19&hw=ph&test=fortune) is particularly relevant to EF scenarios.</span></span> <span data-ttu-id="123d0-171">我們有多個此基準測試的變化，包括：</span><span class="sxs-lookup"><span data-stu-id="123d0-171">We have multiple variations of this benchmark, including:</span></span>

- <span data-ttu-id="123d0-172">直接使用 ADO.NET 的執行。</span><span class="sxs-lookup"><span data-stu-id="123d0-172">An implementation that uses ADO.NET directly.</span></span> <span data-ttu-id="123d0-173">這是此處所列三個最快的執行。</span><span class="sxs-lookup"><span data-stu-id="123d0-173">This is the fastest implementation of the three listed here.</span></span>
- <span data-ttu-id="123d0-174">使用 [Dapper](https://www.nuget.org/packages/Dapper/)的執行。</span><span class="sxs-lookup"><span data-stu-id="123d0-174">An implementation that uses [Dapper](https://www.nuget.org/packages/Dapper/).</span></span> <span data-ttu-id="123d0-175">這比直接使用 ADO.NET 慢，但仍然快速。</span><span class="sxs-lookup"><span data-stu-id="123d0-175">This is slower than using ADO.NET directly, but still fast.</span></span>
- <span data-ttu-id="123d0-176">使用 EF Core 的實作為。</span><span class="sxs-lookup"><span data-stu-id="123d0-176">An implementation that uses EF Core.</span></span> <span data-ttu-id="123d0-177">這是目前最慢的三個執行。</span><span class="sxs-lookup"><span data-stu-id="123d0-177">This is currently the slowest implementation of the three.</span></span>

<span data-ttu-id="123d0-178">EF Core 6.0 的目標是要讓 EF Core 效能符合 TechEmpower Fortunes 基準測試上的 Dapper。</span><span class="sxs-lookup"><span data-stu-id="123d0-178">The goal for EF Core 6.0 is to get the EF Core performance to match that of Dapper on the TechEmpower Fortunes benchmark.</span></span> <span data-ttu-id="123d0-179"> (這是很大的挑戰，但我們最好盡可能找出最接近的外觀。 ) </span><span class="sxs-lookup"><span data-stu-id="123d0-179">(This is a significant challenge but we will do our best to get as close as we can.)</span></span>

### <a name="linkeraot"></a><span data-ttu-id="123d0-180">連結器/AOT</span><span class="sxs-lookup"><span data-stu-id="123d0-180">Linker/AOT</span></span>

<span data-ttu-id="123d0-181">追蹤 [#10963](https://github.com/dotnet/efcore/issues/10963)</span><span class="sxs-lookup"><span data-stu-id="123d0-181">Tracked by [#10963](https://github.com/dotnet/efcore/issues/10963)</span></span>

<span data-ttu-id="123d0-182">狀態：未啟動</span><span class="sxs-lookup"><span data-stu-id="123d0-182">Status: Not started</span></span>

<span data-ttu-id="123d0-183">T 恤尺寸：中</span><span class="sxs-lookup"><span data-stu-id="123d0-183">T-shirt size: Medium</span></span>

<span data-ttu-id="123d0-184">EF Core 會執行大量的執行時間程式碼產生。</span><span class="sxs-lookup"><span data-stu-id="123d0-184">EF Core performs large amounts of runtime code generation.</span></span> <span data-ttu-id="123d0-185">這對相依于連結器樹狀結構（例如 Xamarin 和 Blazor）的應用程式模型，以及不允許動態編譯的平臺（例如 iOS）來說相當困難。</span><span class="sxs-lookup"><span data-stu-id="123d0-185">This is challenging for app models that depend on linker tree shaking, such as Xamarin and Blazor, and platforms that don't allow dynamic compilation, such as iOS.</span></span> <span data-ttu-id="123d0-186">我們將繼續在此空間中調查 EF Core 6.0 的一部分，並以我們可以的方式進行目標改進。</span><span class="sxs-lookup"><span data-stu-id="123d0-186">We will continue investigating in this space as part of EF Core 6.0 and make targeted improvements as we can.</span></span> <span data-ttu-id="123d0-187">但是，我們不希望完全關閉6.0 時間範圍內的間距。</span><span class="sxs-lookup"><span data-stu-id="123d0-187">However, we do not expect to fully close the gap in the 6.0 time frame.</span></span>

## <a name="migrations-and-deployment"></a><span data-ttu-id="123d0-188">遷移和部署</span><span class="sxs-lookup"><span data-stu-id="123d0-188">Migrations and deployment</span></span>

<span data-ttu-id="123d0-189">從 [針對 EF Core 5.0 完成的調查](xref:core/what-is-new/ef-core-5.0/plan#migrations-and-deployment-experience)中開始，我們計畫改進對管理遷移和部署資料庫的支援。</span><span class="sxs-lookup"><span data-stu-id="123d0-189">Following on from the [investigations done for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan#migrations-and-deployment-experience), we plan to introduce improved support for managing migrations and deploying databases.</span></span> <span data-ttu-id="123d0-190">這包括兩個主要區域：</span><span class="sxs-lookup"><span data-stu-id="123d0-190">This includes two major areas:</span></span>

### <a name="migrations-bundles"></a><span data-ttu-id="123d0-191">遷移套件組合</span><span class="sxs-lookup"><span data-stu-id="123d0-191">Migrations bundles</span></span>

<span data-ttu-id="123d0-192">追蹤 [#19693](https://github.com/dotnet/efcore/issues/19693)</span><span class="sxs-lookup"><span data-stu-id="123d0-192">Tracked by [#19693](https://github.com/dotnet/efcore/issues/19693)</span></span>

<span data-ttu-id="123d0-193">狀態：未啟動</span><span class="sxs-lookup"><span data-stu-id="123d0-193">Status: Not started</span></span>

<span data-ttu-id="123d0-194">T 恤尺寸：中</span><span class="sxs-lookup"><span data-stu-id="123d0-194">T-shirt size: Medium</span></span>

<span data-ttu-id="123d0-195">遷移套件組合是獨立的可執行檔，可將遷移套用至實際執行的資料庫。</span><span class="sxs-lookup"><span data-stu-id="123d0-195">A migrations bundle is a self-contained executable that applies migrations to a production database.</span></span> <span data-ttu-id="123d0-196">此行為會比對 `dotnet ef database update` ，但應讓 SSH/Docker/PowerShell 部署更容易，因為所有需要的專案都包含在單一可執行檔中。</span><span class="sxs-lookup"><span data-stu-id="123d0-196">The behavior will match `dotnet ef database update`, but should make SSH/Docker/PowerShell deployment much easier, since everything needed is contained in the single executable.</span></span>

### <a name="managing-migrations"></a><span data-ttu-id="123d0-197">管理移轉</span><span class="sxs-lookup"><span data-stu-id="123d0-197">Managing migrations</span></span>

<span data-ttu-id="123d0-198">追蹤 [#22945](https://github.com/dotnet/efcore/issues/22945)</span><span class="sxs-lookup"><span data-stu-id="123d0-198">Tracked by [#22945](https://github.com/dotnet/efcore/issues/22945)</span></span>

<span data-ttu-id="123d0-199">狀態：未啟動</span><span class="sxs-lookup"><span data-stu-id="123d0-199">Status: Not started</span></span>

<span data-ttu-id="123d0-200">T 恤尺寸：大型</span><span class="sxs-lookup"><span data-stu-id="123d0-200">T-shirt size: Large</span></span>

<span data-ttu-id="123d0-201">針對應用程式所建立的遷移數目可能會變得很負擔。</span><span class="sxs-lookup"><span data-stu-id="123d0-201">The number of migrations created for an application can grow to become a burden.</span></span> <span data-ttu-id="123d0-202">此外，這些遷移通常會與應用程式一起部署，即使這不是必要的。</span><span class="sxs-lookup"><span data-stu-id="123d0-202">In addition, these migrations are frequently deployed with the application even when this is not needed.</span></span> <span data-ttu-id="123d0-203">在 EF Core 6.0 中，我們計畫透過更好的工具和專案/元件管理來改善此項。</span><span class="sxs-lookup"><span data-stu-id="123d0-203">In EF Core 6.0, we plan to improve this through better tooling and project/assembly management.</span></span> <span data-ttu-id="123d0-204">我們打算解決的兩個特定問題是 [squash 許多的遷移](https://github.com/dotnet/efcore/issues/2174) ，並 [重新產生乾淨的模型快照](https://github.com/dotnet/efcore/issues/18557)集。</span><span class="sxs-lookup"><span data-stu-id="123d0-204">Two specific issues we plan to address are [squash many migrations into one](https://github.com/dotnet/efcore/issues/2174) and [regenerate a clean model snapshot](https://github.com/dotnet/efcore/issues/18557).</span></span>

## <a name="improve-existing-features-and-fix-bugs"></a><span data-ttu-id="123d0-205">改善現有的功能並修正 bug</span><span class="sxs-lookup"><span data-stu-id="123d0-205">Improve existing features and fix bugs</span></span>

<span data-ttu-id="123d0-206">此版本目前已規劃任何 [指派給6.0.0 里程碑的問題或 bug](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0) 。</span><span class="sxs-lookup"><span data-stu-id="123d0-206">Any [issue or bug assigned to the 6.0.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0) is currently planned for this release.</span></span> <span data-ttu-id="123d0-207">這包括許多小型增強功能和 bug 修正。</span><span class="sxs-lookup"><span data-stu-id="123d0-207">This includes many small enhancements and bug fixes.</span></span>

### <a name="ef6-query-parity"></a><span data-ttu-id="123d0-208">EF6 查詢同位檢查</span><span class="sxs-lookup"><span data-stu-id="123d0-208">EF6 query parity</span></span>

<span data-ttu-id="123d0-209">由[標示為 ' ef6 ' 的問題和6.0 里程碑的問題](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aef6-parity+milestone%3A6.0.0)追蹤</span><span class="sxs-lookup"><span data-stu-id="123d0-209">Tracked by [issues labeled with 'ef6-parity' and in the 6.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aef6-parity+milestone%3A6.0.0)</span></span>

<span data-ttu-id="123d0-210">狀態：未啟動</span><span class="sxs-lookup"><span data-stu-id="123d0-210">Status: Not started</span></span>

<span data-ttu-id="123d0-211">T 恤尺寸：大型</span><span class="sxs-lookup"><span data-stu-id="123d0-211">T-shirt size: Large</span></span>

<span data-ttu-id="123d0-212">EF Core 5.0 除了 EF6 不支援的模式之外，還支援 EF6 支援的大部分查詢模式。</span><span class="sxs-lookup"><span data-stu-id="123d0-212">EF Core 5.0 supports most query patterns supported by EF6, in addition to patterns not supported in EF6.</span></span> <span data-ttu-id="123d0-213">針對 EF Core 6.0，我們計畫關閉差距，並讓支援的 EF Core 查詢成為支援的 EF6 查詢的真正超集合。</span><span class="sxs-lookup"><span data-stu-id="123d0-213">For EF Core 6.0, we plan to close the gap and make supported EF Core queries a true superset of supported EF6 queries.</span></span> <span data-ttu-id="123d0-214">這會藉由調查間距來推動，但已包含 GroupBy 問題，例如，[平移 groupby 後面接著 FirstOrDefault](https://github.com/dotnet/efcore/issues/12088) [，以及](https://github.com/dotnet/efcore/issues/10753)[基本](https://github.com/dotnet/efcore/issues/11624)和未對應類型的原始 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="123d0-214">This will be driven by investigation of the gaps, but already includes GroupBy issues such as [translate GroupBy followed by FirstOrDefault](https://github.com/dotnet/efcore/issues/12088), and raw SQL queries for [primitive](https://github.com/dotnet/efcore/issues/11624) and [unmapped](https://github.com/dotnet/efcore/issues/10753) types.</span></span>  

### <a name="value-objects"></a><span data-ttu-id="123d0-215">值物件</span><span class="sxs-lookup"><span data-stu-id="123d0-215">Value objects</span></span>

<span data-ttu-id="123d0-216">追蹤 [#9906](https://github.com/dotnet/efcore/issues/9906)</span><span class="sxs-lookup"><span data-stu-id="123d0-216">Tracked by [#9906](https://github.com/dotnet/efcore/issues/9906)</span></span>

<span data-ttu-id="123d0-217">狀態：未啟動</span><span class="sxs-lookup"><span data-stu-id="123d0-217">Status: Not started</span></span>

<span data-ttu-id="123d0-218">T 恤尺寸：中</span><span class="sxs-lookup"><span data-stu-id="123d0-218">T-shirt size: Medium</span></span>

<span data-ttu-id="123d0-219">它先前是小組視圖，其擁有的實體（預定用於 [匯總支援](https://www.martinfowler.com/bliki/DDD_Aggregate.html)）也是 [值物件](https://www.martinfowler.com/bliki/ValueObject.html)的合理近似值。</span><span class="sxs-lookup"><span data-stu-id="123d0-219">It was previously the team view that owned entities, intended for [aggregate support](https://www.martinfowler.com/bliki/DDD_Aggregate.html), would also be a reasonable approximation to [value objects](https://www.martinfowler.com/bliki/ValueObject.html).</span></span> <span data-ttu-id="123d0-220">體驗已顯示為不是這種情況。</span><span class="sxs-lookup"><span data-stu-id="123d0-220">Experience has shown this not to be the case.</span></span> <span data-ttu-id="123d0-221">因此，在 EF Core 6.0 中，我們計畫提供更佳的體驗，著重于領域導向設計中的價值物件需求。</span><span class="sxs-lookup"><span data-stu-id="123d0-221">Therefore, in EF Core 6.0 we plan to introduce a better experience focused on the needs of value objects in domain-driven design.</span></span> <span data-ttu-id="123d0-222">這種方法會以值轉換器為基礎，而非擁有的實體。</span><span class="sxs-lookup"><span data-stu-id="123d0-222">This approach will be based on value converters rather than owned entities.</span></span>

<span data-ttu-id="123d0-223">這項工作一開始的範圍是允許 [對應到多個資料行的值轉換器](https://github.com/dotnet/efcore/issues/13947)。</span><span class="sxs-lookup"><span data-stu-id="123d0-223">This work is initially scoped to allow [value converters which map to multiple columns](https://github.com/dotnet/efcore/issues/13947).</span></span> <span data-ttu-id="123d0-224">我們可能會根據發行期間的意見反應，提取其他支援。</span><span class="sxs-lookup"><span data-stu-id="123d0-224">We may pull in additional support based on feedback during the release.</span></span>

### <a name="cosmos-database-provider"></a><span data-ttu-id="123d0-225">Cosmos 資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="123d0-225">Cosmos database provider</span></span>

<span data-ttu-id="123d0-226">由[標示為 ' cosmos ' 和6.0 里程碑的問題](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Aarea-cosmos)追蹤</span><span class="sxs-lookup"><span data-stu-id="123d0-226">Tracked by [issues labeled with 'area-cosmos' and in the 6.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Aarea-cosmos)</span></span>

<span data-ttu-id="123d0-227">狀態：未啟動</span><span class="sxs-lookup"><span data-stu-id="123d0-227">Status: Not started</span></span>

<span data-ttu-id="123d0-228">T 恤尺寸：大型</span><span class="sxs-lookup"><span data-stu-id="123d0-228">T-shirt size: Large</span></span>

<span data-ttu-id="123d0-229">我們會主動收集在 EF Core 6.0 中對 Cosmos 提供者所做的改善意見反應。</span><span class="sxs-lookup"><span data-stu-id="123d0-229">We are actively gathering feedback on which improvements to make to the Cosmos provider in EF Core 6.0.</span></span> <span data-ttu-id="123d0-230">當我們深入瞭解時，我們將會更新這份檔。</span><span class="sxs-lookup"><span data-stu-id="123d0-230">We will update this document as we learn more.</span></span> <span data-ttu-id="123d0-231">目前，請務必針對 👍 您需要的 Cosmos 功能投票 () 。</span><span class="sxs-lookup"><span data-stu-id="123d0-231">For now, please make sure to vote (👍) for the Cosmos features that you need.</span></span>

### <a name="expose-model-building-conventions-to-applications"></a><span data-ttu-id="123d0-232">將模型建立慣例公開給應用程式</span><span class="sxs-lookup"><span data-stu-id="123d0-232">Expose model building conventions to applications</span></span>

<span data-ttu-id="123d0-233">追蹤 [#214](https://github.com/dotnet/efcore/issues/214)</span><span class="sxs-lookup"><span data-stu-id="123d0-233">Tracked by [#214](https://github.com/dotnet/efcore/issues/214)</span></span>

<span data-ttu-id="123d0-234">狀態：未啟動</span><span class="sxs-lookup"><span data-stu-id="123d0-234">Status: Not started</span></span>

<span data-ttu-id="123d0-235">T 恤尺寸：中</span><span class="sxs-lookup"><span data-stu-id="123d0-235">T-shirt size: Medium</span></span>

<span data-ttu-id="123d0-236">EF Core 會使用一組用來從 .NET 類型建立模型的慣例。</span><span class="sxs-lookup"><span data-stu-id="123d0-236">EF Core uses a set of conventions for building a model from .NET types.</span></span> <span data-ttu-id="123d0-237">這些慣例目前由資料庫提供者控制。</span><span class="sxs-lookup"><span data-stu-id="123d0-237">These conventions are currently controlled by the database provider.</span></span> <span data-ttu-id="123d0-238">在 EF Core 6.0 中，我們想要讓應用程式連結並變更這些慣例。</span><span class="sxs-lookup"><span data-stu-id="123d0-238">In EF Core 6.0, we intend to allow applications to hook into and change these conventions.</span></span>

### <a name="zero-bug-balance-zbb"></a><span data-ttu-id="123d0-239">零 (ZBB) 的錯誤餘額</span><span class="sxs-lookup"><span data-stu-id="123d0-239">Zero bug balance (ZBB)</span></span>

<span data-ttu-id="123d0-240">由[ `type-bug` 6.0 里程碑中標記](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A6.0.0+label%3Atype-bug+)為的問題追蹤</span><span class="sxs-lookup"><span data-stu-id="123d0-240">Tracked by [issues labeled with `type-bug` in the 6.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A6.0.0+label%3Atype-bug+)</span></span>

<span data-ttu-id="123d0-241">狀態：進行中</span><span class="sxs-lookup"><span data-stu-id="123d0-241">Status: In-progress</span></span>

<span data-ttu-id="123d0-242">T 恤尺寸：大型</span><span class="sxs-lookup"><span data-stu-id="123d0-242">T-shirt size: Large</span></span>

<span data-ttu-id="123d0-243">我們計畫修正 EF Core 6.0 時間範圍內所有未解決的 bug。</span><span class="sxs-lookup"><span data-stu-id="123d0-243">We plan to fix all outstanding bugs during the EF Core 6.0 time frame.</span></span> <span data-ttu-id="123d0-244">請注意下列事項：</span><span class="sxs-lookup"><span data-stu-id="123d0-244">Some things to keep in mind:</span></span>

- <span data-ttu-id="123d0-245">這特別適用于標示為「 [類型-bug」的](https://github.com/dotnet/efcore/issues?q=is%3Aissue+label%3Atype-bug)問題。</span><span class="sxs-lookup"><span data-stu-id="123d0-245">This specifically applies to issues labeled [type-bug](https://github.com/dotnet/efcore/issues?q=is%3Aissue+label%3Atype-bug).</span></span>
- <span data-ttu-id="123d0-246">有一些例外狀況，例如 bug 需要設計變更或新功能才能正常修正。</span><span class="sxs-lookup"><span data-stu-id="123d0-246">There will be exceptions, such as when the bug requires a design change or new feature to fix properly.</span></span> <span data-ttu-id="123d0-247">這些問題將會以標籤標示 `blocked` 。</span><span class="sxs-lookup"><span data-stu-id="123d0-247">These issues will be marked with the `blocked` label.</span></span>
- <span data-ttu-id="123d0-248">當需要時，我們會根據風險來回報 bug，因為我們已接近 GA/RTM 版本。</span><span class="sxs-lookup"><span data-stu-id="123d0-248">We will punt bugs based on risk when needed as is normal as we get close to a GA/RTM release.</span></span>

### <a name="miscellaneous-features"></a><span data-ttu-id="123d0-249">其他功能</span><span class="sxs-lookup"><span data-stu-id="123d0-249">Miscellaneous features</span></span>

<span data-ttu-id="123d0-250">由[ `type-enhancement` 6.0 里程碑中標記](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement)為的問題追蹤</span><span class="sxs-lookup"><span data-stu-id="123d0-250">Tracked by [issues labeled with `type-enhancement` in the 6.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement)</span></span>

<span data-ttu-id="123d0-251">狀態：進行中</span><span class="sxs-lookup"><span data-stu-id="123d0-251">Status: In-progress</span></span>

<span data-ttu-id="123d0-252">T 恤尺寸：大型</span><span class="sxs-lookup"><span data-stu-id="123d0-252">T-shirt size: Large</span></span>

<span data-ttu-id="123d0-253">針對 EF 6.0 規劃的其他功能包括（但不限於）：</span><span class="sxs-lookup"><span data-stu-id="123d0-253">Miscellaneous features planned for EF 6.0 include, but are not limited to:</span></span>

- [<span data-ttu-id="123d0-254">非導覽集合的分割查詢</span><span class="sxs-lookup"><span data-stu-id="123d0-254">Split query for non-navigation collections</span></span>](https://github.com/dotnet/efcore/issues/21234)
- [<span data-ttu-id="123d0-255">偵測反向工程中的簡單聯結資料表，並建立多對多關聯性</span><span class="sxs-lookup"><span data-stu-id="123d0-255">Detect simple join tables in reverse engineering and create many-to-many relationships</span></span>](https://github.com/dotnet/efcore/issues/22475)
- [<span data-ttu-id="123d0-256">在 SQLite 和 SQL Server 上完成完整/自由文字搜尋</span><span class="sxs-lookup"><span data-stu-id="123d0-256">Complete full/free-text search on SQLite and SQL Server</span></span>](https://github.com/dotnet/efcore/issues/4823)
- [<span data-ttu-id="123d0-257">SQL Server 空間索引</span><span class="sxs-lookup"><span data-stu-id="123d0-257">SQL Server spatial indexes</span></span>](https://github.com/dotnet/efcore/issues/12538)
- [<span data-ttu-id="123d0-258">機制/API，針對模型中指定之類型的任何屬性指定預設轉換</span><span class="sxs-lookup"><span data-stu-id="123d0-258">Mechanism/API to specify a default conversion for any property of a given type in the model</span></span>](https://github.com/dotnet/efcore/issues/10784)
- [<span data-ttu-id="123d0-259">從 ADO.NET 使用新的批次處理 API</span><span class="sxs-lookup"><span data-stu-id="123d0-259">Use the new batching API from ADO.NET</span></span>](https://github.com/dotnet/efcore/issues/18990)

## <a name="net-integration"></a><span data-ttu-id="123d0-260">.NET 整合</span><span class="sxs-lookup"><span data-stu-id="123d0-260">.NET integration</span></span>

<span data-ttu-id="123d0-261">EF Core 小組也適用于數個相關但獨立的技術。</span><span class="sxs-lookup"><span data-stu-id="123d0-261">The EF Core team also works on several related but independent technologies.</span></span> <span data-ttu-id="123d0-262">尤其是，我們打算處理：</span><span class="sxs-lookup"><span data-stu-id="123d0-262">In particular, we plan to work on:</span></span>

### <a name="enhancements-to-systemdata"></a><span data-ttu-id="123d0-263">System. 資料的增強功能</span><span class="sxs-lookup"><span data-stu-id="123d0-263">Enhancements to System.Data</span></span>

<span data-ttu-id="123d0-264">Dotnet\runtime 存放庫[中標示為 `area-System.Data` 6.0 里程碑的問題](https://github.com/dotnet/runtime/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-System.Data+milestone%3A6.0.0)追蹤</span><span class="sxs-lookup"><span data-stu-id="123d0-264">Tracked by [issues in the dotnet\runtime repo labeled with `area-System.Data` in the 6.0 milestone](https://github.com/dotnet/runtime/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-System.Data+milestone%3A6.0.0)</span></span>

<span data-ttu-id="123d0-265">狀態：未啟動</span><span class="sxs-lookup"><span data-stu-id="123d0-265">Status: Not started</span></span>

<span data-ttu-id="123d0-266">T 恤尺寸：大型</span><span class="sxs-lookup"><span data-stu-id="123d0-266">T-shirt size: Large</span></span>

<span data-ttu-id="123d0-267">此工作包括：</span><span class="sxs-lookup"><span data-stu-id="123d0-267">This work includes:</span></span>

- <span data-ttu-id="123d0-268">新的 [批次處理 API](https://github.com/dotnet/runtime/issues/28633)的實作為。</span><span class="sxs-lookup"><span data-stu-id="123d0-268">Implementation of the new [batching API](https://github.com/dotnet/runtime/issues/28633).</span></span>
- <span data-ttu-id="123d0-269">繼續與其他 .NET 小組和小組合作，以瞭解併發展 ADO.NET。</span><span class="sxs-lookup"><span data-stu-id="123d0-269">Continued work with other .NET teams and the community to understand and evolve ADO.NET.</span></span>
- <span data-ttu-id="123d0-270">[針對 DiagnosticSource 中的追蹤進行標準化，以追蹤元件中的資料](https://github.com/dotnet/runtime/issues/22336)。</span><span class="sxs-lookup"><span data-stu-id="123d0-270">[Standardize on DiagnosticSource for tracing in System.Data.\* components](https://github.com/dotnet/runtime/issues/22336).</span></span>

### <a name="enhancements-to-microsoftdatasqlite"></a><span data-ttu-id="123d0-271">對資料 Sqlite 的增強功能</span><span class="sxs-lookup"><span data-stu-id="123d0-271">Enhancements to Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="123d0-272">由在[ `type-enhancement` `area-adonet-sqlite` 6.0 里程碑中以和標示的問題](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement+label%3Aarea-adonet-sqlite)追蹤</span><span class="sxs-lookup"><span data-stu-id="123d0-272">Tracked by [issues labeled with `type-enhancement` and `area-adonet-sqlite` in the 6.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement+label%3Aarea-adonet-sqlite)</span></span>

<span data-ttu-id="123d0-273">狀態：進行中</span><span class="sxs-lookup"><span data-stu-id="123d0-273">Status: In-progress</span></span>

<span data-ttu-id="123d0-274">T 恤尺寸：中</span><span class="sxs-lookup"><span data-stu-id="123d0-274">T-shirt size: Medium</span></span>

<span data-ttu-id="123d0-275">我們規劃了數個小型的改進，包括 [連接](https://github.com/dotnet/efcore/issues/13837) 共用以及效能的備妥 [語句](https://github.com/dotnet/efcore/issues/14044) 。</span><span class="sxs-lookup"><span data-stu-id="123d0-275">Several small improvements are planned for the Microsoft.Data.Sqlite, including [connection pooling](https://github.com/dotnet/efcore/issues/13837) and [prepared statements](https://github.com/dotnet/efcore/issues/14044) for performance.</span></span>

### <a name="nullable-reference-types"></a><span data-ttu-id="123d0-276">可為 Null 的參考型別</span><span class="sxs-lookup"><span data-stu-id="123d0-276">Nullable reference types</span></span>

<span data-ttu-id="123d0-277">追蹤 [#14150](https://github.com/dotnet/efcore/issues/14150)</span><span class="sxs-lookup"><span data-stu-id="123d0-277">Tracked by [#14150](https://github.com/dotnet/efcore/issues/14150)</span></span>

<span data-ttu-id="123d0-278">狀態：進行中</span><span class="sxs-lookup"><span data-stu-id="123d0-278">Status: In-progress</span></span>

<span data-ttu-id="123d0-279">T 恤尺寸：大型</span><span class="sxs-lookup"><span data-stu-id="123d0-279">T-shirt size: Large</span></span>

<span data-ttu-id="123d0-280">我們將批註 EF Core 程式碼以使用 [可為 null 的參考型別](/dotnet/csharp/nullable-references)。</span><span class="sxs-lookup"><span data-stu-id="123d0-280">We will annotate the EF Core code to use [nullable reference types](/dotnet/csharp/nullable-references).</span></span>

## <a name="experiments-and-investigations"></a><span data-ttu-id="123d0-281">實驗和調查</span><span class="sxs-lookup"><span data-stu-id="123d0-281">Experiments and investigations</span></span>

<span data-ttu-id="123d0-282">EF team 計畫在 EF Core 6.0 時間範圍內進行時間實驗，並在兩方面進行調查。</span><span class="sxs-lookup"><span data-stu-id="123d0-282">The EF team is planning to invest time during the EF Core 6.0 timeframe experimenting and investigating in two areas.</span></span> <span data-ttu-id="123d0-283">這是一個學習程式，因此不會針對6.0 版本規劃任何具體的交付專案。</span><span class="sxs-lookup"><span data-stu-id="123d0-283">This is a learning process and as such no concrete deliverables are planned for the 6.0 release.</span></span>

### <a name="sqlservercore"></a><span data-ttu-id="123d0-284">SqlServer. 核心</span><span class="sxs-lookup"><span data-stu-id="123d0-284">SqlServer.Core</span></span>

<span data-ttu-id="123d0-285">在[.Net Data Lab](https://github.com/dotnet/datalab/)存放庫中追蹤</span><span class="sxs-lookup"><span data-stu-id="123d0-285">Tracked in the [.NET Data Lab repo](https://github.com/dotnet/datalab/)</span></span>

<span data-ttu-id="123d0-286">狀態：未啟動</span><span class="sxs-lookup"><span data-stu-id="123d0-286">Status: Not started</span></span>

<span data-ttu-id="123d0-287">T 恤尺寸：進行中</span><span class="sxs-lookup"><span data-stu-id="123d0-287">T-shirt size: Ongoing</span></span>

<span data-ttu-id="123d0-288">[SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) 是適用于 SQL Server 的全功能 ADO.NET 資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="123d0-288">[Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) is a fully-featured ADO.NET database provider for SQL Server.</span></span> <span data-ttu-id="123d0-289">它可支援 .NET Core 和 .NET Framework 上廣泛的 SQL Server 功能。</span><span class="sxs-lookup"><span data-stu-id="123d0-289">It supports a broad range of SQL Server features on both .NET Core and .NET Framework.</span></span> <span data-ttu-id="123d0-290">不過，它也是大型和舊的程式碼基底，其行為之間有許多複雜的互動。</span><span class="sxs-lookup"><span data-stu-id="123d0-290">However, it is also a large and old codebase with many complex interactions between its behaviors.</span></span> <span data-ttu-id="123d0-291">這會讓您難以使用較新的 .NET Core 功能來調查可能的收益。</span><span class="sxs-lookup"><span data-stu-id="123d0-291">This makes it difficult to investigate the potential gains the could be made using newer .NET Core features.</span></span> <span data-ttu-id="123d0-292">因此，我們會開始與小組共同作業的實驗，以判斷適用于 .NET 的高度執行 SQL Server 驅動程式有何潛力。</span><span class="sxs-lookup"><span data-stu-id="123d0-292">Therefore, we are starting an experiment in collaboration with the community to determine what potential there is for a highly performing SQL Server driver for .NET.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="123d0-293">SqlClient 的投資不會改變。</span><span class="sxs-lookup"><span data-stu-id="123d0-293">Investment in Microsoft.Data.SqlClient is not changing.</span></span> <span data-ttu-id="123d0-294">它會繼續是連接到 SQL Server 和 SQL Azure 的建議方式（不論是否使用 EF Core）。</span><span class="sxs-lookup"><span data-stu-id="123d0-294">It will continue to be the recommended way to connect to SQL Server and SQL Azure, both with and without EF Core.</span></span> <span data-ttu-id="123d0-295">它會繼續支援新推出的 SQL Server 功能。</span><span class="sxs-lookup"><span data-stu-id="123d0-295">It will continue to support new SQL Server features as they are introduced.</span></span>

### <a name="graphql"></a><span data-ttu-id="123d0-296">GraphQL</span><span class="sxs-lookup"><span data-stu-id="123d0-296">GraphQL</span></span>

<span data-ttu-id="123d0-297">狀態：未啟動</span><span class="sxs-lookup"><span data-stu-id="123d0-297">Status: Not started</span></span>

<span data-ttu-id="123d0-298">T 恤尺寸：進行中</span><span class="sxs-lookup"><span data-stu-id="123d0-298">T-shirt size: Ongoing</span></span>

<span data-ttu-id="123d0-299">過去幾年來， [GraphQL](https://graphql.org/)一直都在各種不同的平臺上。</span><span class="sxs-lookup"><span data-stu-id="123d0-299">[GraphQL](https://graphql.org/) has been gaining traction over the last few years across a variety of platforms.</span></span> <span data-ttu-id="123d0-300">我們計畫調查空間，並找出使用 .NET 改善體驗的方式。</span><span class="sxs-lookup"><span data-stu-id="123d0-300">We plan to investigate the space and find ways to improve the experience with .NET.</span></span> <span data-ttu-id="123d0-301">這將牽涉到與社區合作，瞭解和支援現有的生態系統。</span><span class="sxs-lookup"><span data-stu-id="123d0-301">This will involve working with the community on understanding and supporting the existing ecosystem.</span></span> <span data-ttu-id="123d0-302">它可能也牽涉到 Microsoft 的特定投資，其形式為現有工作的投稿或開發 Microsoft stack 中的免費作品。</span><span class="sxs-lookup"><span data-stu-id="123d0-302">It may also involve specific investment from Microsoft, either in the form of contributions to existing work or in developing complimentary pieces in the Microsoft stack.</span></span>

### <a name="dataverse-formerly-common-data-services"></a><span data-ttu-id="123d0-303">DataVerse (先前的 Common Data Service) </span><span class="sxs-lookup"><span data-stu-id="123d0-303">DataVerse (formerly Common Data Services)</span></span>

<span data-ttu-id="123d0-304">狀態：未啟動</span><span class="sxs-lookup"><span data-stu-id="123d0-304">Status: Not started</span></span>

<span data-ttu-id="123d0-305">T 恤尺寸：進行中</span><span class="sxs-lookup"><span data-stu-id="123d0-305">T-shirt size: Ongoing</span></span>

<span data-ttu-id="123d0-306">[DataVerse](/powerapps/maker/data-platform/data-platform-intro) 是以資料行為基礎的資料存放區，專為快速開發商務應用程式所設計。</span><span class="sxs-lookup"><span data-stu-id="123d0-306">[DataVerse](/powerapps/maker/data-platform/data-platform-intro) is a column-based data store designed for rapid development of business applications.</span></span> <span data-ttu-id="123d0-307">它會自動處理複雜的資料類型，例如 (Blob 的二進位物件) 而且具有內建的實體和關聯性，例如組織和連絡人。</span><span class="sxs-lookup"><span data-stu-id="123d0-307">It automatically handles complex data types like binary objects (BLOBs) and has built-in entities and relationships like organizations and contacts.</span></span> <span data-ttu-id="123d0-308">有一個 SDK，但開發人員可能會受益于讓 EF Core 提供者使用先進的 LINQ 查詢，利用工作單位並擁有一致的資料存取 API。</span><span class="sxs-lookup"><span data-stu-id="123d0-308">An SDK exists but developers may benefit from having an EF Core provider to use advanced LINQ queries, take advantage of unit of work and have a consistent data access API.</span></span> <span data-ttu-id="123d0-309">小組會考慮不同的方法來改善連接至 DataVerse 的 .NET 開發人員體驗。</span><span class="sxs-lookup"><span data-stu-id="123d0-309">The team will consider different ways to improve the .NET developer experience connecting to DataVerse.</span></span>

## <a name="below-the-cut-line"></a><span data-ttu-id="123d0-310">低於-剪下行</span><span class="sxs-lookup"><span data-stu-id="123d0-310">Below-the-cut-line</span></span>

<span data-ttu-id="123d0-311">由[標記為的 `consider-for-current-release` 問題](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-current-release)追蹤</span><span class="sxs-lookup"><span data-stu-id="123d0-311">Tracked by [issues labeled with `consider-for-current-release`](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-current-release)</span></span>

<span data-ttu-id="123d0-312">這些是目前 **尚未** 針對6.0 版本排程的 bug 修正和增強功能，但我們將會根據整個版本中的意見反應，以及上述工作的進度進行探討。</span><span class="sxs-lookup"><span data-stu-id="123d0-312">These are bug fixes and enhancements that are **not** currently scheduled for the 6.0 release, but we will look at based on feedback throughout the release together with progress made on the work above.</span></span> <span data-ttu-id="123d0-313">這些問題可能會提取到 EF Core 6.0，並自動成為下一版的候選項目。</span><span class="sxs-lookup"><span data-stu-id="123d0-313">These issues may be pulled in to EF Core 6.0, and automatically become candidates for the next release.</span></span>

<span data-ttu-id="123d0-314">此外，我們在規劃時，一律會考慮 [最投票的問題](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) 。</span><span class="sxs-lookup"><span data-stu-id="123d0-314">In addition, we always consider the [most voted issues](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) when planning.</span></span> <span data-ttu-id="123d0-315">從版本中剪下這些問題一直都是很麻煩的，但我們的確需要針對我們所擁有的資源進行實際的規劃。</span><span class="sxs-lookup"><span data-stu-id="123d0-315">Cutting any of these issues from a release is always painful, but we do need a realistic plan for the resources we have.</span></span> <span data-ttu-id="123d0-316">確定您已投票 (👍 所需功能的) 。</span><span class="sxs-lookup"><span data-stu-id="123d0-316">Make sure you have voted (👍) for the features you need.</span></span>

## <a name="suggestions"></a><span data-ttu-id="123d0-317">建議</span><span class="sxs-lookup"><span data-stu-id="123d0-317">Suggestions</span></span>

<span data-ttu-id="123d0-318">您對計劃的意見反應很重要。</span><span class="sxs-lookup"><span data-stu-id="123d0-318">Your feedback on planning is important.</span></span> <span data-ttu-id="123d0-319">指出問題重要性的最佳方式是 👍 在 GitHub 上針對該問題投票 () 。</span><span class="sxs-lookup"><span data-stu-id="123d0-319">The best way to indicate the importance of an issue is to vote (👍) for that issue on GitHub.</span></span> <span data-ttu-id="123d0-320">然後，此資料會送入下一版的 [規劃](xref:core/what-is-new/release-planning) 程式。</span><span class="sxs-lookup"><span data-stu-id="123d0-320">This data will then feed into the [planning process](xref:core/what-is-new/release-planning) for the next release.</span></span>
