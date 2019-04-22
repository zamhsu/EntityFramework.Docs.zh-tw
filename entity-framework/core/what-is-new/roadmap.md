---
title: Entity Framework Core 藍圖
author: divega
ms.date: 02/20/2018
ms.assetid: 834C9729-7F6E-4355-917D-DE3EE9FE149E
uid: core/what-is-new/roadmap
ms.openlocfilehash: e9d1a707c643036fa15b1e0e46730eadefd65518
ms.sourcegitcommit: 8f801993c9b8cd8a8fbfa7134818a8edca79e31a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2019
ms.locfileid: "59562464"
---
# <a name="entity-framework-core-roadmap"></a><span data-ttu-id="0e8ef-102">Entity Framework Core 藍圖</span><span class="sxs-lookup"><span data-stu-id="0e8ef-102">Entity Framework Core Roadmap</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0e8ef-103">請注意，後續版本的功能集與上市時間，隨時可能會變動，此外，即使我們試圖將此頁面保持在最新狀態，可能還是無法隨時反映我們最新的計劃。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-103">Please note that the feature sets and schedules of future releases are always subject to change, and although we will try to keep this page up to date, it may not reflect our latest plans at all times.</span></span>

## <a name="ef-core-30"></a><span data-ttu-id="0e8ef-104">EF Core 3.0</span><span class="sxs-lookup"><span data-stu-id="0e8ef-104">EF Core 3.0</span></span>

<span data-ttu-id="0e8ef-105">EF Core 2.2 已然問世，我們現在的主要焦點為 EF Core 3.0。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-105">With EF Core 2.2 out the door, our main focus is now EF Core 3.0.</span></span>
<span data-ttu-id="0e8ef-106">如需該版本中包含規劃的[新功能](xref:core/what-is-new/ef-core-3.0/features)和目的性的[中斷性變更](xref:core/what-is-new/ef-core-3.0/breaking-changes)等資訊，請參閱 [EF Core 3.0 的新功能](xref:core/what-is-new/ef-core-3.0/index)。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-106">See [What's new in EF Core 3.0](xref:core/what-is-new/ef-core-3.0/index) for information on planned [new features](xref:core/what-is-new/ef-core-3.0/features) and intentional [breaking changes](xref:core/what-is-new/ef-core-3.0/breaking-changes) included in the release.</span></span>

## <a name="schedule"></a><span data-ttu-id="0e8ef-107">排程</span><span class="sxs-lookup"><span data-stu-id="0e8ef-107">Schedule</span></span>

<span data-ttu-id="0e8ef-108">EF Core 發行排程與 [.NET Core 發行排程](https://github.com/dotnet/core/blob/master/roadmap.md)保持同步。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-108">The release schedule for EF Core is in-sync with the [.NET Core release schedule](https://github.com/dotnet/core/blob/master/roadmap.md).</span></span>

## <a name="backlog"></a><span data-ttu-id="0e8ef-109">Backlog</span><span class="sxs-lookup"><span data-stu-id="0e8ef-109">Backlog</span></span>

<span data-ttu-id="0e8ef-110">我們問題追蹤器中的[待辦項目里程碑](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog+sort%3Areactions-%2B1-desc)，包含我們希望有朝一日能努力解決，或認為社群中有人能處理的問題。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-110">The [Backlog Milestone](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog+sort%3Areactions-%2B1-desc) in our issue tracker contains issues that we expect to work on someday, or that we think someone from the community could tackle.</span></span>
<span data-ttu-id="0e8ef-111">歡迎客戶提交意見，並針對這些問題投票。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-111">Customers are welcome to submit comments and votes on these issues.</span></span>
<span data-ttu-id="0e8ef-112">我們鼓勵有意願處理這些問題參與者，先針對如何開始著手進行討論。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-112">Contributors looking to work on any of these issues are encouraged to first start a discussion on how to approach them.</span></span>

<span data-ttu-id="0e8ef-113">我們並不保證會針對特定 EF Core 版本中的任何指定功能進行處理。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-113">There is never a guarantee that we will work on any given feature in a specific version of EF Core.</span></span>
<span data-ttu-id="0e8ef-114">就如同所有軟體專案、優先順序、發行排程及可用資源，都可能隨時變更。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-114">As in all software projects, priorities, release schedules, and available resources can change at any point.</span></span>
<span data-ttu-id="0e8ef-115">但是，若我們想要解決特定時間範圍內的問題，我們會將其指派到發行里程碑，而非指派到待辦項目里程碑。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-115">But if we intend to resolve an issue in a specific timeframe, we'll assign it to a release milestone instead of the backlog milestone.</span></span>
<span data-ttu-id="0e8ef-116">我們會定期將問題在待辦項目里程碑與發行里程碑之間移動，作為我們的[發行計劃程序](#release-planning-process)。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-116">We routinely move issues between the backlog and release milestones as part of our [release planning process](#release-planning-process).</span></span>

<span data-ttu-id="0e8ef-117">若我們預計不會處理此問題，很可能就會結束此問題。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-117">We'll likely close an issue if we don't plan to ever address it.</span></span>
<span data-ttu-id="0e8ef-118">若我們取得先前已結束問題的新資訊，就會重新評估該問題。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-118">But we can reconsider an issue that we previously closed if we get new information about it.</span></span>

## <a name="release-planning-process"></a><span data-ttu-id="0e8ef-119">發行計劃程序</span><span class="sxs-lookup"><span data-stu-id="0e8ef-119">Release planning process</span></span>

<span data-ttu-id="0e8ef-120">在我們收到的問題中，經常有人問起我們如何決定將某功能加入特定的版本。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-120">We often get questions about how we choose specific features to go into a particular release.</span></span>
<span data-ttu-id="0e8ef-121">我們的待辦項目絕不必然等同於發行計劃。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-121">Our backlog certainly doesn't automatically translate into release plans.</span></span>
<span data-ttu-id="0e8ef-122">EF6 中若存在某功能，也並不代表該功能必須施行於 EF Core 中。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-122">The presence of a feature in EF6 also doesn't automatically mean that the feature needs to be implemented in EF Core.</span></span>

<span data-ttu-id="0e8ef-123">很難詳述我們計劃發行所遵循的整個程序。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-123">It's difficult to detail the whole process we follow to plan a release.</span></span>
<span data-ttu-id="0e8ef-124">其中大部分是在討論特定的功能、商機與優先順序，且程序本身也會隨著每次發行而演進。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-124">Much of it is discussing the specific features, opportunities and priorities, and the process itself also evolves with every release.</span></span>
<span data-ttu-id="0e8ef-125">但在我們決定下一步該做什麼時，可以先總結我們試圖回答的常見問題：</span><span class="sxs-lookup"><span data-stu-id="0e8ef-125">However, we can summarize the common questions we try to answer when deciding what to work on next:</span></span>

1. <span data-ttu-id="0e8ef-126">**我們認為有多少開發人員會使用此功能，而該功能會使其應用程式/體驗獲得多少改善？**</span><span class="sxs-lookup"><span data-stu-id="0e8ef-126">**How many developers we think will use the feature and how much better will it make their applications/experience?**</span></span> <span data-ttu-id="0e8ef-127">若要解答此問題，我們會從各種來源收集意見，其中一個來源就包括了對問題的留言與投票。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-127">To answer this, we collect feedback from many sources — Comments and votes on issues is one of those sources.</span></span>

2. <span data-ttu-id="0e8ef-128">**如果我們尚未實作這項功能，那有哪些因應措施可用？**</span><span class="sxs-lookup"><span data-stu-id="0e8ef-128">**What are the workarounds people can use if we don't implement this feature yet?**</span></span> <span data-ttu-id="0e8ef-129">舉例來說，許多開發人員在缺少原生多對多支援時，都會想到運用聯合資料表作為因應措施。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-129">For example, many developers can map a join table to work around lack of native many-to-many support.</span></span> <span data-ttu-id="0e8ef-130">很明顯地，並非所有開發人員都想這麼做，但其中許多人都做得到，而這也是我們決策中所考量的因素。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-130">Obviously, not all developers want to do it, but many can, and that counts as a factor in our decision.</span></span>

3. <span data-ttu-id="0e8ef-131">**實作此功能是否會涉及 EF Core 的架構，使我們更加需要實作其他功能？**</span><span class="sxs-lookup"><span data-stu-id="0e8ef-131">**Does implementing this feature evolve the architecture of EF Core such that it moves us closer to implementing other features?**</span></span> <span data-ttu-id="0e8ef-132">我們偏好可作為其他功能建置組塊的功能。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-132">We tend to favor features that act as building blocks for other features.</span></span> <span data-ttu-id="0e8ef-133">例如，屬性包實體可協助我們往多對多支援邁進，且實體建構函式讓我們的消極式載入支援變得可行。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-133">For example, property bag entities can help us move towards many-to-many support, and entity constructors enabled our lazy loading support.</span></span>

4. <span data-ttu-id="0e8ef-134">**這項功能是擴充點嗎？**</span><span class="sxs-lookup"><span data-stu-id="0e8ef-134">**Is the feature an extensibility point?**</span></span> <span data-ttu-id="0e8ef-135">因為擴充點使開發人員能夠輕鬆地依自己的習慣使用，並填補任何功能上的缺失，所以我們偏好擴充點勝過於一般功能。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-135">We tend to favor extensibility points over normal features because they enable developers to hook their own behaviors and compensate for any missing functionality.</span></span>

5. <span data-ttu-id="0e8ef-136">**此功能與其他產品搭配使用時的綜效如何？**</span><span class="sxs-lookup"><span data-stu-id="0e8ef-136">**What is the synergy of the feature when used in combination with other products?**</span></span> <span data-ttu-id="0e8ef-137">我們偏好讓 EF Core 能與其他產品搭配使用，或大幅改善與其他產品 (例如 .NET Core、最新版的 Visual Studio、Microsoft Azure 等) 一併使用的體驗之功能。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-137">We favor features that enable or significantly improve the experience of using EF Core with other products, such as .NET Core, the latest version of Visual Studio, Microsoft Azure, etc.</span></span>

6. <span data-ttu-id="0e8ef-138">**可參與功能開發的人員技能如何，以及如何善用這些資源？**</span><span class="sxs-lookup"><span data-stu-id="0e8ef-138">**What are the skills of the people available to work on a feature, and how to best leverage these resources?**</span></span> <span data-ttu-id="0e8ef-139">每位 EF 小組的成員及我們社群的參與者，在不同領域都各有不同程度的經驗，所以我們必須據此進行規劃。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-139">Each member of the EF team and our community contributors has different levels of experience in different areas, so we have to plan accordingly.</span></span> <span data-ttu-id="0e8ef-140">即使我們希望將「所有人力」集中在像是 GroupBy 翻譯，或多對多關聯性等特定功能上，也無法實際付諸行動。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-140">Even if we wanted to have "all hands on deck" to work on a specific feature like GroupBy translations, or many-to-many, that wouldn't be practical.</span></span>

<span data-ttu-id="0e8ef-141">如先前所說，程序會隨著每次發行而演進。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-141">As mentioned before, the process evolves on every release.</span></span>
<span data-ttu-id="0e8ef-142">未來我們會嘗試為社群的成員新增更多機會，以促進我們的發行計劃。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-142">In the future we'll try to add more opportunities for members of the community to provide inputs into our release plans.</span></span>
<span data-ttu-id="0e8ef-143">例如，我們希望讓檢閱功能的草稿設計與發行計劃本身更加容易。</span><span class="sxs-lookup"><span data-stu-id="0e8ef-143">For example, we would like to make it easier to review draft designs of the features and of the release plan itself.</span></span>
