---
title: Entity Framework Core 藍圖
author: divega
ms.date: 02/20/2018
ms.assetid: 834C9729-7F6E-4355-917D-DE3EE9FE149E
uid: core/what-is-new/roadmap
ms.openlocfilehash: a12d628a28515f0c6710bfa59bc6dcdf41fcb58b
ms.sourcegitcommit: b3c2b34d5f006ee3b41d6668f16fe7dcad1b4317
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688585"
---
# <a name="entity-framework-core-roadmap"></a><span data-ttu-id="13e05-102">Entity Framework Core 藍圖</span><span class="sxs-lookup"><span data-stu-id="13e05-102">Entity Framework Core Roadmap</span></span>

> [!IMPORTANT]
> <span data-ttu-id="13e05-103">請注意，後續版本的功能集與上市時間，隨時可能會變動，此外，即使我們試圖將此頁面保持在最新狀態，可能還是無法隨時反映我們最新的計劃。</span><span class="sxs-lookup"><span data-stu-id="13e05-103">Please note that the feature sets and schedules of future releases are always subject to change, and although we will try to keep this page up to date, it may not reflect our latest plans at all times.</span></span>

### <a name="ef-core-22"></a><span data-ttu-id="13e05-104">EF Core 2.2</span><span class="sxs-lookup"><span data-stu-id="13e05-104">EF Core 2.2</span></span>

<span data-ttu-id="13e05-105">此版本將包含許多 Bug 修正，還有相對較少的新功能。</span><span class="sxs-lookup"><span data-stu-id="13e05-105">This release will include many bug fixes, and relatively small number of new features.</span></span> <span data-ttu-id="13e05-106">此版本預計於 2018 年年底推出。</span><span class="sxs-lookup"><span data-stu-id="13e05-106">This release is planned for end of year 2018.</span></span> <span data-ttu-id="13e05-107">[EF Core 2.2 的新功能](xref:core/what-is-new/ef-core-2.2)包含此版本的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="13e05-107">Details about this release are included in [What's new in EF Core 2.2](xref:core/what-is-new/ef-core-2.2).</span></span> 

### <a name="ef-core-30"></a><span data-ttu-id="13e05-108">EF Core 3.0</span><span class="sxs-lookup"><span data-stu-id="13e05-108">EF Core 3.0</span></span>

<span data-ttu-id="13e05-109">我們預計讓主要的 EF Core 版本與 .NET Core 3.0 及 ASP.NET 3.0 一致，但尚未完成其[發行計劃程序](#release-planning-process)。</span><span class="sxs-lookup"><span data-stu-id="13e05-109">We plan to have major EF Core release aligned with .NET Core 3.0 and ASP.NET 3.0, but we haven't completed the [release planning process](#release-planning-process) for it.</span></span>

<span data-ttu-id="13e05-110">使用[問題追蹤器中的這項查詢](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3A3.0.0+sort%3Areactions-%2B1-desc)，可查看暫時指派到 3.0 的工作項目。</span><span class="sxs-lookup"><span data-stu-id="13e05-110">Use [this query in our issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3A3.0.0+sort%3Areactions-%2B1-desc) to see work items tentatively assigned to 3.0.</span></span>

## <a name="schedule"></a><span data-ttu-id="13e05-111">排程</span><span class="sxs-lookup"><span data-stu-id="13e05-111">Schedule</span></span>

<span data-ttu-id="13e05-112">EF Core 的發行時間與 [.NET Core 發行時間](https://github.com/dotnet/core/blob/master/roadmap.md)及 [ASP.NET Core 發行時間](https://github.com/aspnet/Home/wiki/Roadmap)保持同步。</span><span class="sxs-lookup"><span data-stu-id="13e05-112">The schedule for EF Core is in-sync with the [.NET Core schedule](https://github.com/dotnet/core/blob/master/roadmap.md) and [ASP.NET Core schedule](https://github.com/aspnet/Home/wiki/Roadmap).</span></span>

## <a name="backlog"></a><span data-ttu-id="13e05-113">待辦項目</span><span class="sxs-lookup"><span data-stu-id="13e05-113">Backlog</span></span>

<span data-ttu-id="13e05-114">我們問題追蹤器中的[待辦項目里程碑](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog+sort%3Areactions-%2B1-desc)，包含我們希望有朝一日能努力解決，或認為社群中有人能處理的問題。</span><span class="sxs-lookup"><span data-stu-id="13e05-114">The [Backlog Milestone](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog+sort%3Areactions-%2B1-desc) in our issue tracker contains issues that we expect to work on someday, or that we think someone from the community could tackle.</span></span>
<span data-ttu-id="13e05-115">歡迎客戶提交意見，並針對這些問題投票。</span><span class="sxs-lookup"><span data-stu-id="13e05-115">Customers are welcome to submit comments and votes on these issues.</span></span>
<span data-ttu-id="13e05-116">我們鼓勵有意願處理這些問題參與者，先針對如何開始著手進行討論。</span><span class="sxs-lookup"><span data-stu-id="13e05-116">Contributors looking to work on any of these issues are encouraged to first start a discussion on how to approach them.</span></span>

<span data-ttu-id="13e05-117">我們並不保證會針對特定 EF Core 版本中的任何指定功能進行處理。</span><span class="sxs-lookup"><span data-stu-id="13e05-117">There is never a guarantee that we will work on any given feature in a specific version of EF Core.</span></span>
<span data-ttu-id="13e05-118">就如同所有軟體專案、優先順序、發行排程及可用資源，都可能隨時變更。</span><span class="sxs-lookup"><span data-stu-id="13e05-118">As in all software projects, priorities, release schedules, and available resources can change at any point.</span></span>
<span data-ttu-id="13e05-119">但是，若我們想要解決特定時間範圍內的問題，我們會將其指派到發行里程碑，而非指派到待辦項目里程碑。</span><span class="sxs-lookup"><span data-stu-id="13e05-119">But if we intend to resolve an issue in a specific timeframe, we'll assign it to a release milestone instead of the backlog milestone.</span></span>
<span data-ttu-id="13e05-120">我們會定期將問題在待辦項目里程碑與發行里程碑之間移動，作為我們的[發行計劃程序](#release-planning-process)。</span><span class="sxs-lookup"><span data-stu-id="13e05-120">We routinely move issues between the backlog and release milestones as part of our [release planning process](#release-planning-process).</span></span>

<span data-ttu-id="13e05-121">若我們預計不會處理此問題，很可能就會結束此問題。</span><span class="sxs-lookup"><span data-stu-id="13e05-121">We'll likely close an issue if we don't plan to ever address it.</span></span>
<span data-ttu-id="13e05-122">若我們取得先前已結束問題的新資訊，就會重新評估該問題。</span><span class="sxs-lookup"><span data-stu-id="13e05-122">But we can reconsider an issue that we previously closed if we get new information about it.</span></span>

## <a name="release-planning-process"></a><span data-ttu-id="13e05-123">發行計劃程序</span><span class="sxs-lookup"><span data-stu-id="13e05-123">Release planning process</span></span>

<span data-ttu-id="13e05-124">在我們收到的問題中，經常有人問起我們如何決定將某功能加入特定的版本。</span><span class="sxs-lookup"><span data-stu-id="13e05-124">We often get questions about how we choose specific features to go into a particular release.</span></span>
<span data-ttu-id="13e05-125">我們的待辦項目絕不必然等同於發行計劃。</span><span class="sxs-lookup"><span data-stu-id="13e05-125">Our backlog certainly doesn't automatically translate into release plans.</span></span>
<span data-ttu-id="13e05-126">EF6 中若存在某功能，也並不代表該功能必須施行於 EF Core 中。</span><span class="sxs-lookup"><span data-stu-id="13e05-126">The presence of a feature in EF6 also doesn't automatically mean that the feature needs to be implemented in EF Core.</span></span>

<span data-ttu-id="13e05-127">很難詳述我們計劃發行所遵循的整個程序。</span><span class="sxs-lookup"><span data-stu-id="13e05-127">It's difficult to detail the whole process we follow to plan a release.</span></span>
<span data-ttu-id="13e05-128">其中大部分是在討論特定的功能、商機與優先順序，且程序本身也會隨著每次發行而演進。</span><span class="sxs-lookup"><span data-stu-id="13e05-128">Much of it is discussing the specific features, opportunities and priorities, and the process itself also evolves with every release.</span></span>
<span data-ttu-id="13e05-129">但在我們決定下一步該做什麼時，可以先總結我們試圖回答的常見問題：</span><span class="sxs-lookup"><span data-stu-id="13e05-129">However, we can summarize the common questions we try to answer when deciding what to work on next:</span></span>

1. <span data-ttu-id="13e05-130">**我們認為有多少開發人員會使用此功能，而該功能會使其應用程式/體驗獲得多少改善？**</span><span class="sxs-lookup"><span data-stu-id="13e05-130">**How many developers we think will use the feature and how much better will it make their applications/experience?**</span></span> <span data-ttu-id="13e05-131">若要解答此問題，我們會從各種來源收集意見，其中一個來源就包括了對問題的留言與投票。</span><span class="sxs-lookup"><span data-stu-id="13e05-131">To answer this, we collect feedback from many sources — Comments and votes on issues is one of those sources.</span></span>

2. <span data-ttu-id="13e05-132">**如果我們尚未實作這項功能，那有哪些因應措施可用？**</span><span class="sxs-lookup"><span data-stu-id="13e05-132">**What are the workarounds people can use if we don't implement this feature yet?**</span></span> <span data-ttu-id="13e05-133">舉例來說，許多開發人員在缺少原生多對多支援時，都會想到運用聯合資料表作為因應措施。</span><span class="sxs-lookup"><span data-stu-id="13e05-133">For example, many developers can map a join table to work around lack of native many-to-many support.</span></span> <span data-ttu-id="13e05-134">很明顯地，並非所有開發人員都想這麼做，但其中許多人都做得到，而這也是我們決策中所考量的因素。</span><span class="sxs-lookup"><span data-stu-id="13e05-134">Obviously, not all developers want to do it, but many can, and that counts as a factor in our decision.</span></span>

3. <span data-ttu-id="13e05-135">**實作此功能是否會涉及 EF Core 的架構，使我們更加需要實作其他功能？**</span><span class="sxs-lookup"><span data-stu-id="13e05-135">**Does implementing this feature evolve the architecture of EF Core such that it moves us closer to implementing other features?**</span></span> <span data-ttu-id="13e05-136">我們偏好可作為其他功能建置組塊的功能。</span><span class="sxs-lookup"><span data-stu-id="13e05-136">We tend to favor features that act as building blocks for other features.</span></span> <span data-ttu-id="13e05-137">例如，屬性包實體可協助我們往多對多支援邁進，且實體建構函式讓我們的消極式載入支援變得可行。</span><span class="sxs-lookup"><span data-stu-id="13e05-137">For example, property bag entities can help us move towards many-to-many support, and entity constructors enabled our lazy loading support.</span></span> 

4. <span data-ttu-id="13e05-138">**這項功能是擴充點嗎？**</span><span class="sxs-lookup"><span data-stu-id="13e05-138">**Is the feature an extensibility point?**</span></span> <span data-ttu-id="13e05-139">因為擴充點使開發人員能夠輕鬆地依自己的習慣使用，並填補任何功能上的缺失，所以我們偏好擴充點勝過於一般功能。</span><span class="sxs-lookup"><span data-stu-id="13e05-139">We tend to favor extensibility points over normal features because they enable developers to hook their own behaviors and compensate for any missing functionality.</span></span> 

5. <span data-ttu-id="13e05-140">**此功能與其他產品搭配使用時的綜效如何？**</span><span class="sxs-lookup"><span data-stu-id="13e05-140">**What is the synergy of the feature when used in combination with other products?**</span></span> <span data-ttu-id="13e05-141">我們偏好讓 EF Core 能與其他產品搭配使用，或大幅改善與其他產品 (例如 .NET Core、最新版的 Visual Studio、Microsoft Azure 等) 一併使用的體驗之功能。</span><span class="sxs-lookup"><span data-stu-id="13e05-141">We favor features that enable or significantly improve the experience of using EF Core with other products, such as .NET Core, the latest version of Visual Studio, Microsoft Azure, etc.</span></span>

6. <span data-ttu-id="13e05-142">**可參與功能開發的人員技能如何，以及如何善用這些資源？**</span><span class="sxs-lookup"><span data-stu-id="13e05-142">**What are the skills of the people available to work on a feature, and how to best leverage these resources?**</span></span> <span data-ttu-id="13e05-143">每位 EF 小組的成員及我們社群的參與者，在不同領域都各有不同程度的經驗，所以我們必須據此進行規劃。</span><span class="sxs-lookup"><span data-stu-id="13e05-143">Each member of the EF team and our community contributors has different levels of experience in different areas, so we have to plan accordingly.</span></span> <span data-ttu-id="13e05-144">即使我們希望將「所有人力」集中在像是 GroupBy 翻譯，或多對多關聯性等特定功能上，也無法實際付諸行動。</span><span class="sxs-lookup"><span data-stu-id="13e05-144">Even if we wanted to have "all hands on deck" to work on a specific feature like GroupBy translations, or many-to-many, that wouldn't be practical.</span></span>

<span data-ttu-id="13e05-145">如先前所說，程序會隨著每次發行而演進。</span><span class="sxs-lookup"><span data-stu-id="13e05-145">As mentioned before, the process evolves on every release.</span></span>
<span data-ttu-id="13e05-146">未來我們會嘗試為社群的成員新增更多機會，以促進我們的發行計劃。</span><span class="sxs-lookup"><span data-stu-id="13e05-146">In the future we'll try to add more opportunities for members of the community to provide inputs into our release plans.</span></span>
<span data-ttu-id="13e05-147">例如，我們希望讓檢閱功能的草稿設計與發行計劃本身更加容易。</span><span class="sxs-lookup"><span data-stu-id="13e05-147">For example, we would like to make it easier to review draft designs of the features and of the release plan itself.</span></span>