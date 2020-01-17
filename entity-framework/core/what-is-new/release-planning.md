---
title: EF Core 版本規劃
author: ajcvickers
ms.date: 01/14/2020
uid: core/what-is-new/release_planning.md
ms.openlocfilehash: c60040aa4acc33ba8b5a54b619539b275690f70e
ms.sourcegitcommit: f2a38c086291699422d8b28a72d9611d1b24ad0d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2020
ms.locfileid: "76125393"
---
# <a name="release-planning-process"></a><span data-ttu-id="7dd6f-102">發行計劃程序</span><span class="sxs-lookup"><span data-stu-id="7dd6f-102">Release planning process</span></span>

<span data-ttu-id="7dd6f-103">在我們收到的問題中，經常有人問起我們如何決定將某功能加入特定的版本。</span><span class="sxs-lookup"><span data-stu-id="7dd6f-103">We often get questions about how we choose specific features to go into a particular release.</span></span>
<span data-ttu-id="7dd6f-104">我們的待辦項目絕不必然等同於發行計劃。</span><span class="sxs-lookup"><span data-stu-id="7dd6f-104">Our backlog certainly doesn't automatically translate into release plans.</span></span>
<span data-ttu-id="7dd6f-105">EF6 中若存在某功能，也並不代表該功能必須施行於 EF Core 中。</span><span class="sxs-lookup"><span data-stu-id="7dd6f-105">The presence of a feature in EF6 also doesn't automatically mean that the feature needs to be implemented in EF Core.</span></span>

<span data-ttu-id="7dd6f-106">很難詳述我們計劃發行所遵循的整個程序。</span><span class="sxs-lookup"><span data-stu-id="7dd6f-106">It's difficult to detail the whole process we follow to plan a release.</span></span>
<span data-ttu-id="7dd6f-107">其中大部分是在討論特定的功能、商機與優先順序，且程序本身也會隨著每次發行而演進。</span><span class="sxs-lookup"><span data-stu-id="7dd6f-107">Much of it is discussing the specific features, opportunities and priorities, and the process itself also evolves with every release.</span></span>
<span data-ttu-id="7dd6f-108">但在我們決定下一步該做什麼時，可以先總結我們試圖回答的常見問題：</span><span class="sxs-lookup"><span data-stu-id="7dd6f-108">However, we can summarize the common questions we try to answer when deciding what to work on next:</span></span>

1. <span data-ttu-id="7dd6f-109">**我們認為多少開發人員會使用此功能，而它讓他們的應用程式或體驗改善多少？**</span><span class="sxs-lookup"><span data-stu-id="7dd6f-109">**How many developers we think will use the feature and how much better will it make their applications or experience?**</span></span> <span data-ttu-id="7dd6f-110">為了回答這個問題，我們從許多來源收集意見反應 — 問題的留言與投票是那些來源之一。</span><span class="sxs-lookup"><span data-stu-id="7dd6f-110">To answer this question, we collect feedback from many sources — Comments and votes on issues is one of those sources.</span></span>

2. <span data-ttu-id="7dd6f-111">**如果我們尚未實作這項功能，那有哪些因應措施可用？**</span><span class="sxs-lookup"><span data-stu-id="7dd6f-111">**What are the workarounds people can use if we don't implement this feature yet?**</span></span> <span data-ttu-id="7dd6f-112">舉例來說，許多開發人員在缺少原生多對多支援時，都會想到運用聯合資料表作為因應措施。</span><span class="sxs-lookup"><span data-stu-id="7dd6f-112">For example, many developers can map a join table to work around lack of native many-to-many support.</span></span> <span data-ttu-id="7dd6f-113">很明顯地，並非所有開發人員都想這麼做，但其中許多人都做得到，而這也是我們決策中所考量的因素。</span><span class="sxs-lookup"><span data-stu-id="7dd6f-113">Obviously, not all developers want to do it, but many can, and that counts as a factor in our decision.</span></span>

3. <span data-ttu-id="7dd6f-114">**實作此功能是否會涉及 EF Core 的架構，使我們更加需要實作其他功能？**</span><span class="sxs-lookup"><span data-stu-id="7dd6f-114">**Does implementing this feature evolve the architecture of EF Core such that it moves us closer to implementing other features?**</span></span> <span data-ttu-id="7dd6f-115">我們偏好可作為其他功能建置組塊的功能。</span><span class="sxs-lookup"><span data-stu-id="7dd6f-115">We tend to favor features that act as building blocks for other features.</span></span> <span data-ttu-id="7dd6f-116">比方說，屬性包實體可以協助我們邁向多對多的支援，而實體建構函式則促成了消極式載入支援。</span><span class="sxs-lookup"><span data-stu-id="7dd6f-116">For instance, property bag entities can help us move towards many-to-many support, and entity constructors enabled our lazy loading support.</span></span>

4. <span data-ttu-id="7dd6f-117">**這項功能是擴充點嗎？**</span><span class="sxs-lookup"><span data-stu-id="7dd6f-117">**Is the feature an extensibility point?**</span></span> <span data-ttu-id="7dd6f-118">因為擴充點使開發人員能夠輕鬆地依自己的習慣使用，並填補任何功能上的缺失，所以我們偏好擴充點勝過於一般功能。</span><span class="sxs-lookup"><span data-stu-id="7dd6f-118">We tend to favor extensibility points over normal features because they enable developers to hook their own behaviors and compensate for any missing functionality.</span></span>

5. <span data-ttu-id="7dd6f-119">**此功能與其他產品搭配使用時的綜效如何？**</span><span class="sxs-lookup"><span data-stu-id="7dd6f-119">**What is the synergy of the feature when used in combination with other products?**</span></span> <span data-ttu-id="7dd6f-120">我們偏好能促成或大幅改善 EF Core 搭配其他產品使用體驗的功能，例如 .NET Core、最新版本的 Visual Studio、Microsoft Azure 等等。</span><span class="sxs-lookup"><span data-stu-id="7dd6f-120">We favor features that enable or significantly improve the experience of using EF Core with other products, such as .NET Core, the latest version of Visual Studio, Microsoft Azure, and so on.</span></span>

6. <span data-ttu-id="7dd6f-121">**可參與功能開發的人員技能如何，以及如何善用這些資源？**</span><span class="sxs-lookup"><span data-stu-id="7dd6f-121">**What are the skills of the people available to work on a feature, and how to best leverage these resources?**</span></span> <span data-ttu-id="7dd6f-122">每位 EF 小組的成員及我們社群的參與者，在不同領域都各有不同程度的經驗，所以我們必須據此進行規劃。</span><span class="sxs-lookup"><span data-stu-id="7dd6f-122">Each member of the EF team and our community contributors has different levels of experience in different areas, so we have to plan accordingly.</span></span> <span data-ttu-id="7dd6f-123">即使我們希望將「所有人力」集中在像是 GroupBy 翻譯，或多對多關聯性等特定功能上，也無法實際付諸行動。</span><span class="sxs-lookup"><span data-stu-id="7dd6f-123">Even if we wanted to have "all hands on deck" to work on a specific feature like GroupBy translations, or many-to-many, that wouldn't be practical.</span></span>

<span data-ttu-id="7dd6f-124">如先前所說，程序會隨著每次發行而演進。</span><span class="sxs-lookup"><span data-stu-id="7dd6f-124">As mentioned before, the process evolves on every release.</span></span>
<span data-ttu-id="7dd6f-125">在未來，我們會嘗試新增更多機會，讓社群的成員能提供對我們發行計劃的投入。</span><span class="sxs-lookup"><span data-stu-id="7dd6f-125">In the future, we'll try to add more opportunities for members of the community to provide inputs into our release plans.</span></span>
<span data-ttu-id="7dd6f-126">例如，我們希望讓檢閱功能的草稿設計與發行計劃本身更加容易。</span><span class="sxs-lookup"><span data-stu-id="7dd6f-126">For example, we would like to make it easier to review draft designs of the features and of the release plan itself.</span></span>

## <a name="backlog"></a><span data-ttu-id="7dd6f-127">Backlog</span><span class="sxs-lookup"><span data-stu-id="7dd6f-127">Backlog</span></span>

<span data-ttu-id="7dd6f-128">我們問題追蹤器中的[待處理項目里程碑](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog+sort%3Areactions-%2B1-desc)包含我們預期某天要處理的問題，或是我們認為社群成員可以處理的問題。</span><span class="sxs-lookup"><span data-stu-id="7dd6f-128">The [Backlog Milestone](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog+sort%3Areactions-%2B1-desc) in our issue tracker contains issues that we either expect to work on someday, or we think someone from the community could tackle.</span></span>
<span data-ttu-id="7dd6f-129">歡迎客戶提交意見，並針對這些問題投票。</span><span class="sxs-lookup"><span data-stu-id="7dd6f-129">Customers are welcome to submit comments and votes on these issues.</span></span>
<span data-ttu-id="7dd6f-130">我們鼓勵有意願處理這些問題參與者，先針對如何開始著手進行討論。</span><span class="sxs-lookup"><span data-stu-id="7dd6f-130">Contributors looking to work on any of these issues are encouraged to first start a discussion on how to approach them.</span></span>

<span data-ttu-id="7dd6f-131">我們絕未保證我們會在特定 EF Core 版本處理任何指定的功能。</span><span class="sxs-lookup"><span data-stu-id="7dd6f-131">There's never a guarantee that we'll work on any given feature in a specific version of EF Core.</span></span>
<span data-ttu-id="7dd6f-132">就如同所有軟體專案、優先順序、發行排程及可用資源，都可能隨時變更。</span><span class="sxs-lookup"><span data-stu-id="7dd6f-132">As in all software projects, priorities, release schedules, and available resources can change at any point.</span></span>
<span data-ttu-id="7dd6f-133">但是，如果我們想要在特定的時間範圍內解決問題，我們會將它指派給發行里程碑，而不是待辦專案里程碑。</span><span class="sxs-lookup"><span data-stu-id="7dd6f-133">But if we intend to resolve an issue in a specific time-frame, we'll assign it to a release milestone instead of the backlog milestone.</span></span>

<span data-ttu-id="7dd6f-134">若我們預計不會處理此問題，很可能就會結束此問題。</span><span class="sxs-lookup"><span data-stu-id="7dd6f-134">We'll likely close an issue if we don't plan to ever address it.</span></span>
<span data-ttu-id="7dd6f-135">若我們取得先前已結束問題的新資訊，就會重新評估該問題。</span><span class="sxs-lookup"><span data-stu-id="7dd6f-135">But we can reconsider an issue that we previously closed if we get new information about it.</span></span>
