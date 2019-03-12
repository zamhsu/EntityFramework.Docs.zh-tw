---
title: EF Core 3.0 的新功能 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: 2EBE2CCC-E52D-483F-834C-8877F5EB0C0C
uid: core/what-is-new/ef-core-3.0/features
ms.openlocfilehash: cf0d2cf032b9aa319fe706aece5b1ea66a5d6251
ms.sourcegitcommit: a013e243a14f384999ceccaf9c779b8c1ae3b936
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2019
ms.locfileid: "57463354"
---
# <a name="new-features-included-in-ef-core-30-currently-in-preview"></a><span data-ttu-id="838be-102">EF Core 3.0 (目前為預覽版) 中包含的新功能</span><span class="sxs-lookup"><span data-stu-id="838be-102">New features included in EF Core 3.0 (currently in preview)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="838be-103">請注意，後續版本的功能集與上市時間，隨時可能會變動，此外，即使我們試圖將此頁面保持在最新狀態，可能還是無法隨時反映我們最新的計劃。</span><span class="sxs-lookup"><span data-stu-id="838be-103">Please note that the feature sets and schedules of future releases are always subject to change, and although we will try to keep this page up to date, it may not reflect our latest plans at all times.</span></span>

<span data-ttu-id="838be-104">下列清單包含為 EF Core 3.0 新規劃的重要功能。</span><span class="sxs-lookup"><span data-stu-id="838be-104">The following list includes the major new features planned for EF Core 3.0.</span></span>
<span data-ttu-id="838be-105">大部分功能並未包含在目前的預覽版中，但隨著逐步往 RTM 發展，多數功能終將能夠使用。</span><span class="sxs-lookup"><span data-stu-id="838be-105">Most of these features are not included in the current preview, but will become available as we make progress towards RTM.</span></span>

<span data-ttu-id="838be-106">原因是在發行的一開始，我們即著重於實作規劃的[中斷性變更](xref:core/what-is-new/ef-core-3.0/breaking-changes)。</span><span class="sxs-lookup"><span data-stu-id="838be-106">The reason is that at the beginning of the release we are focusing on implementing planned [breaking changes](xref:core/what-is-new/ef-core-3.0/breaking-changes).</span></span>
<span data-ttu-id="838be-107">許多中斷性變更本身使得 EF Core 有所改善。</span><span class="sxs-lookup"><span data-stu-id="838be-107">Many of these breaking changes are improvements to EF Core on their own.</span></span>
<span data-ttu-id="838be-108">此外，還需要更多重大變更，以便改善能更加詳盡。</span><span class="sxs-lookup"><span data-stu-id="838be-108">Many others are required to unblock further improvements.</span></span> 

<span data-ttu-id="838be-109">如需 Bug 修正和開發中增強功能的完整清單，您可以在[我們的問題追蹤中查看此查詢](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3A3.0.0+sort%3Areactions-%2B1-desc)。</span><span class="sxs-lookup"><span data-stu-id="838be-109">For a complete list of bug fixes and enhancements underway, you can see [this query in our issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3A3.0.0+sort%3Areactions-%2B1-desc).</span></span>

## <a name="linq-improvements"></a><span data-ttu-id="838be-110">LINQ 改善</span><span class="sxs-lookup"><span data-stu-id="838be-110">LINQ improvements</span></span> 

[<span data-ttu-id="838be-111">追蹤問題 #12795</span><span class="sxs-lookup"><span data-stu-id="838be-111">Tracking Issue #12795</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12795)

<span data-ttu-id="838be-112">已著手進行這項功能的開發，但未包含在目前的預覽版中。</span><span class="sxs-lookup"><span data-stu-id="838be-112">Work on this feature has started but it isn't included in the current preview.</span></span>

<span data-ttu-id="838be-113">LINQ 可讓您以偏好的語言撰寫資料庫查詢，進而利用豐富的型別資訊優勢來取得 IntelliSense 與編譯階段型別檢查。</span><span class="sxs-lookup"><span data-stu-id="838be-113">LINQ enables you to write database queries without leaving your language of choice, taking advantage of rich type information to get IntelliSense and compile-time type checking.</span></span>
<span data-ttu-id="838be-114">但是 LINQ 也可以讓您撰寫不限數目的複雜查詢，這通常是 LINQ 提供者所面臨的艱難挑戰。</span><span class="sxs-lookup"><span data-stu-id="838be-114">But LINQ also enables you to write an unlimited number of complicated queries, and that has always been a huge challenge for LINQ providers.</span></span>
<span data-ttu-id="838be-115">在 EF Core 的前幾個版本中，我們透過搞清楚查詢的哪些部分可以轉譯為 SQL，接著允許查詢的剩餘部分在用戶端上的記憶體中執行來克服其中的一些問題。</span><span class="sxs-lookup"><span data-stu-id="838be-115">In the first few versions of EF Core, we solved that in part by figuring out what portions of a query could be translated to SQL, and then by allowing the rest of the query to execute in memory on the client.</span></span>
<span data-ttu-id="838be-116">這個用戶端執行在某些案例中是令人滿意的，但在其他案例中會導致無效率的查詢，而在應用程式部署到生產階段之前，您可能無法發現這種無效率的查詢。</span><span class="sxs-lookup"><span data-stu-id="838be-116">This client-side execution can be desirable in some situations, but in many other cases it can result in inefficient queries that may not be identified until an application is deployed to production.</span></span>
<span data-ttu-id="838be-117">在 EF Core 3.0 中，我們計劃在 LINQ 實作的運作方式及其測試方式進行重大變更。</span><span class="sxs-lookup"><span data-stu-id="838be-117">In EF Core 3.0, we're planning to make profound changes to how our LINQ implementation works, and how we test it.</span></span>
<span data-ttu-id="838be-118">目標是讓它更強固 (例如避免中斷修補程式版本中的查詢)、能夠將更多運算式正確地轉譯為 SQL、在更多案例中產生有效率的查詢，以及防止未偵測到無效率查詢的情況。</span><span class="sxs-lookup"><span data-stu-id="838be-118">The goals are to make it more robust (for example, to avoid breaking queries in patch releases), to enable translating more expressions correctly into SQL, to generate efficient queries in more cases, and to prevent inefficient queries from going undetected.</span></span>

## <a name="cosmos-db-support"></a><span data-ttu-id="838be-119">Cosmos DB 支援</span><span class="sxs-lookup"><span data-stu-id="838be-119">Cosmos DB support</span></span> 

[<span data-ttu-id="838be-120">追蹤問題 #8443</span><span class="sxs-lookup"><span data-stu-id="838be-120">Tracking Issue #8443</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/8443)

<span data-ttu-id="838be-121">這項功能包含在目前的預覽版中，但尚未完成。</span><span class="sxs-lookup"><span data-stu-id="838be-121">This feature is included in the current preview, but isn't complete yet.</span></span> 

<span data-ttu-id="838be-122">我們正在處理適用於 EF Core 的 Cosmos DB 提供者，以讓開發人員熟悉 EF 程式設計模型，以輕鬆地使用 Azure Cosmos DB 做為應用程式資料庫。</span><span class="sxs-lookup"><span data-stu-id="838be-122">We're working on a Cosmos DB provider for EF Core, to enable developers familiar with the EF programing model to easily target Azure Cosmos DB as an application database.</span></span>
<span data-ttu-id="838be-123">目標是讓 .NET 開發人員能更輕鬆地發揮 Cosmos DB 的一些優點，例如全域散發、"Always On" 可用性、彈性的延展性，以及低延遲。</span><span class="sxs-lookup"><span data-stu-id="838be-123">The goal is to make some of the advantages of Cosmos DB, like global distribution, "always on" availability, elastic scalability, and low latency, even more accessible to .NET developers.</span></span>
<span data-ttu-id="838be-124">提供者將啟用大部分的 EF Core 功能，例如自動變更追蹤、LINQ 與值轉換 (根據 Cosmos DB 中的 SQL API)。</span><span class="sxs-lookup"><span data-stu-id="838be-124">The provider will enable most EF Core features, like automatic change tracking, LINQ, and value conversions, against the SQL API in Cosmos DB.</span></span>
<span data-ttu-id="838be-125">我們在 EF Core 2.2 之前開始此工作，而且[我們已建立一些預覽版提供者供使用](https://blogs.msdn.microsoft.com/dotnet/2018/10/17/announcing-entity-framework-core-2-2-preview-3/)。</span><span class="sxs-lookup"><span data-stu-id="838be-125">We started this effort before EF Core 2.2, and [we have made some preview versions of the provider available](https://blogs.msdn.microsoft.com/dotnet/2018/10/17/announcing-entity-framework-core-2-2-preview-3/).</span></span>
<span data-ttu-id="838be-126">新的計畫是以 EF Core 3.0 為基礎繼續開發提供者。</span><span class="sxs-lookup"><span data-stu-id="838be-126">The new plan is to continue developing the provider alongside EF Core 3.0.</span></span> 

## <a name="c-80-support"></a><span data-ttu-id="838be-127">C# 8.0 支援</span><span class="sxs-lookup"><span data-stu-id="838be-127">C# 8.0 support</span></span>

<span data-ttu-id="838be-128">[追蹤問題 #12047](https://github.com/aspnet/EntityFrameworkCore/issues/12047)
[追蹤問題 #10347](https://github.com/aspnet/EntityFrameworkCore/issues/10347)</span><span class="sxs-lookup"><span data-stu-id="838be-128">[Tracking Issue #12047](https://github.com/aspnet/EntityFrameworkCore/issues/12047)
[Tracking Issue #10347](https://github.com/aspnet/EntityFrameworkCore/issues/10347)</span></span>

<span data-ttu-id="838be-129">已著手進行這項功能的開發，但未包含在目前的預覽版中。</span><span class="sxs-lookup"><span data-stu-id="838be-129">Work on this feature has started but it isn't included in the current preview.</span></span>

<span data-ttu-id="838be-130">我們想要讓客戶在使用 EF Core 時，能夠利用 [C# 8.0 即將推出的一些新功能](https://blogs.msdn.microsoft.com/dotnet/2018/11/12/building-c-8-0/)，例如非同步串流 (包括 `await foreach`) 與可為 Null 的參考型別。</span><span class="sxs-lookup"><span data-stu-id="838be-130">We want our customers to take advantage of some of the [new features coming in C# 8.0](https://blogs.msdn.microsoft.com/dotnet/2018/11/12/building-c-8-0/) like async streams (including `await foreach`) and nullable reference types while using EF Core.</span></span>

## <a name="reverse-engineering-of-database-views"></a><span data-ttu-id="838be-131">資料庫檢視的反向工程</span><span class="sxs-lookup"><span data-stu-id="838be-131">Reverse engineering of database views</span></span>

[<span data-ttu-id="838be-132">追蹤問題 #1679</span><span class="sxs-lookup"><span data-stu-id="838be-132">Tracking Issue #1679</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/1679)

<span data-ttu-id="838be-133">這項功能並未包含在目前的預覽版中。</span><span class="sxs-lookup"><span data-stu-id="838be-133">This feature isn't included in the current preview.</span></span>

<span data-ttu-id="838be-134">[查詢類型](xref:core/modeling/query-types)已於 EF Core 2.1 中推出，並在 EF Core 3.0 中視為沒有索引鍵的實體類型，這些類型代表可從資料庫讀取但無法更新的資料。</span><span class="sxs-lookup"><span data-stu-id="838be-134">[Query types](xref:core/modeling/query-types), introduced in EF Core 2.1 and considered entity types without keys in EF Core 3.0, represent data that can be read from the database, but cannot be updated.</span></span>
<span data-ttu-id="838be-135">此特性讓它們非常適用於大多時候的資料庫檢視，因此我們想要在對資料庫檢視進行反向工程時，將沒有索引鍵的實體類型建立作業自動化。</span><span class="sxs-lookup"><span data-stu-id="838be-135">This characteristic makes them an excellent fit for database views in most scenarios, so we plan to automate the creation of entity types without keys when reverse engineering database views.</span></span>

## <a name="property-bag-entities"></a><span data-ttu-id="838be-136">屬性包實體</span><span class="sxs-lookup"><span data-stu-id="838be-136">Property bag entities</span></span> 

<span data-ttu-id="838be-137">[追蹤問題 #13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) 和 [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914)</span><span class="sxs-lookup"><span data-stu-id="838be-137">[Tracking Issue #13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) and [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914)</span></span>

<span data-ttu-id="838be-138">已著手進行這項功能的開發，但未包含在目前的預覽版中。</span><span class="sxs-lookup"><span data-stu-id="838be-138">Work on this feature has started but it isn't included in the current preview.</span></span> 

<span data-ttu-id="838be-139">此功能是有關於啟用會在已編製索引之屬性內而非正常屬性內存放資料的實體，也有關於能夠使用相同.NET 類別的執行個體 (可能是像 `Dictionary<string, object>` 這麼簡單的東西) 以代表相同 EF Core 模型中的不同實體類型。</span><span class="sxs-lookup"><span data-stu-id="838be-139">This feature is about enabling entities that store data in indexed properties instead of regular properties, and also about being able to use instances of the same .NET class (potentially something as simple as a `Dictionary<string, object>`) to represent different entity types in the same EF Core model.</span></span>
<span data-ttu-id="838be-140">此功能是在不需要聯結實體的情況下支援多對多關聯性的墊腳石，這是使用者最希望擁有的 EF Core 改良功能。</span><span class="sxs-lookup"><span data-stu-id="838be-140">This feature is a stepping stone to support many-to-many relationships without a join entity, which is one of the most requested improvements for EF Core.</span></span>

## <a name="ef-63-on-net-core"></a><span data-ttu-id="838be-141">.NET Core 上的 EF 6.3</span><span class="sxs-lookup"><span data-stu-id="838be-141">EF 6.3 on .NET Core</span></span> 

[<span data-ttu-id="838be-142">追蹤問題 EF6#271</span><span class="sxs-lookup"><span data-stu-id="838be-142">Tracking Issue EF6#271</span></span>](https://github.com/aspnet/EntityFramework6/issues/271)

<span data-ttu-id="838be-143">已著手進行這項功能的開發，但未包含在目前的預覽版中。</span><span class="sxs-lookup"><span data-stu-id="838be-143">Work on this feature has started but it isn't included in the current preview.</span></span> 

<span data-ttu-id="838be-144">我們了解許多現有的應用程式都使用舊版 EF，而且因為要獲得 .NET Core 的優點而將它們移植到 EF Core 有時候需要非常多的精力。</span><span class="sxs-lookup"><span data-stu-id="838be-144">We understand that many existing applications use previous versions of EF, and that porting them to EF Core only to take advantage of .NET Core can sometimes require a significant effort.</span></span>
<span data-ttu-id="838be-145">因此，我們會讓下一個版本的 EF 6 能夠在 .NET Core 3.0 上直執行。</span><span class="sxs-lookup"><span data-stu-id="838be-145">For that reason, we will be adapting the next version of EF 6 to run on .NET Core 3.0.</span></span>
<span data-ttu-id="838be-146">我們這樣做的原因是要讓您能以變更最小的方式移植現有的應用程式。</span><span class="sxs-lookup"><span data-stu-id="838be-146">We are doing this to facilitate porting existing applications with minimal changes.</span></span>
<span data-ttu-id="838be-147">但免不了有些限制。</span><span class="sxs-lookup"><span data-stu-id="838be-147">There are going to be some limitations.</span></span> <span data-ttu-id="838be-148">例如：</span><span class="sxs-lookup"><span data-stu-id="838be-148">For example:</span></span>
- <span data-ttu-id="838be-149">除了 .NET Core 包含的 SQL Server 支援，新的提供者還必須使用其他資料庫</span><span class="sxs-lookup"><span data-stu-id="838be-149">It will require new providers to work with other databases besides the included SQL Server support on .NET Core</span></span>
- <span data-ttu-id="838be-150">SQL Server 的空間支援將不會啟用</span><span class="sxs-lookup"><span data-stu-id="838be-150">Spatial support with SQL Server won't be enabled</span></span>

<span data-ttu-id="838be-151">另請注意，EF 6 目前並未規劃任何新功能。</span><span class="sxs-lookup"><span data-stu-id="838be-151">Note also that there are no new features planned for EF 6 at this point.</span></span>
