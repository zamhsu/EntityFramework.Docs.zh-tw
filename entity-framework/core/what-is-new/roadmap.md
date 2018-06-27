---
title: Entity Framework Core 藍圖
author: divega
ms.author: divega
ms.date: 02/20/2018
ms.assetid: 834C9729-7F6E-4355-917D-DE3EE9FE149E
ms.technology: entity-framework-core
uid: core/what-is-new/roadmap
ms.openlocfilehash: e23f5d7b1ff95bead310fa8e618a88c161a4e10c
ms.sourcegitcommit: 72e59e6af86b568653e1b29727529dfd7f65d312
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2018
ms.locfileid: "34754440"
---
# <a name="entity-framework-core-roadmap"></a><span data-ttu-id="cc431-102">Entity Framework Core 藍圖</span><span class="sxs-lookup"><span data-stu-id="cc431-102">Entity Framework Core Roadmap</span></span>

> [!IMPORTANT]
> <span data-ttu-id="cc431-103">請注意，後續版本的功能集與上市時間，隨時可能會變動，此外，即使我們試圖將此頁面保持在最新狀態，可能還是無法隨時反映我們最新的計劃。</span><span class="sxs-lookup"><span data-stu-id="cc431-103">Please note that the feature sets and schedules of future releases are always subject to change, and although we will try to keep this page up to date, it may not reflect our latest plans at all times.</span></span>

<span data-ttu-id="cc431-104">2018 年 5 月 30 日發行的穩定 EF Core 2.1 版本。</span><span class="sxs-lookup"><span data-stu-id="cc431-104">The stable version of EF Core 2.1 was released on May 30, 2018.</span></span> <span data-ttu-id="cc431-105">您可於 [EF Core 2.1 的新功能](xref:core/what-is-new/ef-core-2.1)找到此版本的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="cc431-105">You can find more information about this release in [What is new in EF Core 2.1](xref:core/what-is-new/ef-core-2.1).</span></span>

<span data-ttu-id="cc431-106">我們尚未完成 2.1 之後下一個版本的[發行計劃程序](#release-planning-process)。</span><span class="sxs-lookup"><span data-stu-id="cc431-106">We have not completed the [release planning process](#release-planning-process) for the next release after 2.1.</span></span>

## <a name="schedule"></a><span data-ttu-id="cc431-107">排程</span><span class="sxs-lookup"><span data-stu-id="cc431-107">Schedule</span></span>

<span data-ttu-id="cc431-108">EF Core 的發行時間與 [.NET Core 發行時間](https://github.com/dotnet/core/blob/master/roadmap.md)及 [ASP.NET Core 發行時間](https://github.com/aspnet/Home/wiki/Roadmap)保持同步。</span><span class="sxs-lookup"><span data-stu-id="cc431-108">The schedule for EF Core is in-sync with the [.NET Core schedule](https://github.com/dotnet/core/blob/master/roadmap.md) and [ASP.NET Core schedule](https://github.com/aspnet/Home/wiki/Roadmap).</span></span>

## <a name="backlog"></a><span data-ttu-id="cc431-109">待辦項目</span><span class="sxs-lookup"><span data-stu-id="cc431-109">Backlog</span></span>

<span data-ttu-id="cc431-110">我們在問題追蹤程式內採用了[待辦項目里程碑](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog+sort%3Areactions-%2B1-desc)，來保留詳細的問題與功能清單。</span><span class="sxs-lookup"><span data-stu-id="cc431-110">We use the [Backlog Milestone](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog+sort%3Areactions-%2B1-desc) in our issue tracker to maintain a detailed list of issues and features.</span></span> <span data-ttu-id="cc431-111">客戶可在這些項目上留言和附議。</span><span class="sxs-lookup"><span data-stu-id="cc431-111">Customers can comment and up-vote these.</span></span>

<span data-ttu-id="cc431-112">對於我們合理預期將於某個時間點解決，或是能夠由社群成員處理的問題，我們偏好將問題開放而不結案。但這並不表示我們傾向於在特定時間範圍內予以解決，除非我們在[發行計劃程序](#release-planning-process)中為問題指派特定的里程碑。</span><span class="sxs-lookup"><span data-stu-id="cc431-112">We tend to leave issues open that we reasonably expect we will work on at some point, or that someone from the community could tackle, but that does not imply the intent to resolve them in a specific timeframe until we assign them to a specific milestone as part of our [release planning process](#release-planning-process).</span></span>

<span data-ttu-id="cc431-113">如果我們不打算實作某項功能，就可能會關閉該問題。</span><span class="sxs-lookup"><span data-stu-id="cc431-113">If we do not plan to ever implement a feature, we will likely close the issue.</span></span> <span data-ttu-id="cc431-114">若我們收到問題的新資訊，也可能在日後重新評估已經結案的問題。</span><span class="sxs-lookup"><span data-stu-id="cc431-114">An issue that we closed can be reconsidered at a later point if we obtain new information about it.</span></span>

<span data-ttu-id="cc431-115">話雖如此，我們目前對於未來所握有的資訊並不充足，無法斷言「我們將於 Y 時間/版本推出 X 功能」。因為所有軟體專案、優先順序、發行時間和可用的資源，都可能隨時會變動。</span><span class="sxs-lookup"><span data-stu-id="cc431-115">All that said, we don’t have enough information about the future to be able to say that feature X will be resolved by time/release Y. As in all software projects, priorities, release schedules, and available resources can change at any point.</span></span>

## <a name="release-planning-process"></a><span data-ttu-id="cc431-116">發行計劃程序</span><span class="sxs-lookup"><span data-stu-id="cc431-116">Release planning process</span></span>

<span data-ttu-id="cc431-117">在我們收到的問題中，經常有人問起我們如何決定將某功能加入特定的版本。</span><span class="sxs-lookup"><span data-stu-id="cc431-117">We often get questions about how we choose specific features to go into a particular release.</span></span> <span data-ttu-id="cc431-118">我們的待辦項目絕不必然等同發行計劃。</span><span class="sxs-lookup"><span data-stu-id="cc431-118">Our backlog certainly does not automatically translate into release plans.</span></span> <span data-ttu-id="cc431-119">EF6 中若存在某功能，也並不代表該功能必定會施行於 EF Core 中。</span><span class="sxs-lookup"><span data-stu-id="cc431-119">The presence of a feature in EF6 also does not automatically mean that the feature needs to be implemented in EF Core.</span></span>

<span data-ttu-id="cc431-120">我們很難在此詳細描述規劃發行的整個程序，部分原因是重點幾乎都集中在商談特定功能、商機與優先順序上，而另一部分則是因為程序本身會隨著每次發行而有所變動。</span><span class="sxs-lookup"><span data-stu-id="cc431-120">It is difficult to detail here the whole process we follow to plan a release, partly because a lot of it is discussing the specific features, opportunities and priorities, and partly because the process itself usually evolves with every release.</span></span> <span data-ttu-id="cc431-121">不過，在我們決定下一步該做什麼時，總結我們試圖回答的常見問題可就相對簡單多了：</span><span class="sxs-lookup"><span data-stu-id="cc431-121">However, it is relatively easy to summarize the common questions we try to answer when deciding what to work on next:</span></span>

1. <span data-ttu-id="cc431-122">**我們認為有多少開發人員會使用此功能，而該功能會使其應用程式/體驗獲得多少改善？**</span><span class="sxs-lookup"><span data-stu-id="cc431-122">**How many developers we think will use the feature and how much better will it make their applications/experience?**</span></span> <span data-ttu-id="cc431-123">我們會彙總各式來源的意見反應來做出結論，其中一項就包括了對問題的留言與投票。</span><span class="sxs-lookup"><span data-stu-id="cc431-123">We aggregate feedback from many sources into this — Comments and votes on issues is one of those sources.</span></span>

2. <span data-ttu-id="cc431-124">**如果我們尚未實作這項功能，有哪些因應措施可用？**</span><span class="sxs-lookup"><span data-stu-id="cc431-124">**What are the workarounds people can use if we don’t implement this feature yet?**</span></span> <span data-ttu-id="cc431-125">舉例來說，許多開發人員在缺少原生多對多支援時，都會想到運用聯合資料表以作為因應措施。</span><span class="sxs-lookup"><span data-stu-id="cc431-125">For example, many developers are able to map a join table in order to work around lack of native many-to-many support.</span></span> <span data-ttu-id="cc431-126">很顯然並非所有開發人員都能這麼做，但大多數人都可以，而這就是我們會列入的考量。</span><span class="sxs-lookup"><span data-stu-id="cc431-126">Obviously, not all developers can do this, but many can, and this is a factor which counts.</span></span>

3. <span data-ttu-id="cc431-127">**實作此功能是否會涉及 EF Core 的架構，使我們更加需要實作其他功能？**</span><span class="sxs-lookup"><span data-stu-id="cc431-127">**Does implementing this feature evolve the architecture of EF Core such that it moves us closer to implementing other features?**</span></span> <span data-ttu-id="cc431-128">我們傾向於加入其他功能有建置組塊用途的功能，例如為擁有之類型所製作的資料表分割功能，即可協助我們更可進行 TPT 支援。</span><span class="sxs-lookup"><span data-stu-id="cc431-128">We tend to favor features that act as building blocks for other features—for example, the table splitting that was done for owned types helps us move towards TPT support.</span></span>

4. <span data-ttu-id="cc431-129">**這項功能是擴充點嗎？**</span><span class="sxs-lookup"><span data-stu-id="cc431-129">**Is the feature an extensibility point?**</span></span> <span data-ttu-id="cc431-130">我們傾向於加入擴充點，原因是擴充點能使開發人員更輕鬆地運用自己的習慣，並以該方式取得某些缺少的功能。</span><span class="sxs-lookup"><span data-stu-id="cc431-130">We tend to favor extensibility points because they enable developers to more easily hook in their own behaviors and get some of the missing functionality that way.</span></span> <span data-ttu-id="cc431-131">我們規劃在消極式載入作業開始前，加入一些擴充點。</span><span class="sxs-lookup"><span data-stu-id="cc431-131">We’re planning to do some of this as a start to the lazy loading work.</span></span>

5. <span data-ttu-id="cc431-132">**此功能與其他產品搭配使用時的綜效如何？**</span><span class="sxs-lookup"><span data-stu-id="cc431-132">**What is the synergy of the feature when used in combination with other products?**</span></span> <span data-ttu-id="cc431-133">我們傾向於加入可讓 EF Core 能其他產品搭配使用或大幅改善使用其他產品 (例如.NET Core、最新版的 Visual Studio、Microsoft Azure 等) 之體驗的功能。</span><span class="sxs-lookup"><span data-stu-id="cc431-133">We tend to favor features that allow EF Core to be used with other products or to significantly improve the experience of using other products, such as .NET Core, the latest version of Visual Studio, Microsoft Azure, etc.</span></span>

6. <span data-ttu-id="cc431-134">**可參與功能開發的人員素質如何，以及如何善用這些資源？**</span><span class="sxs-lookup"><span data-stu-id="cc431-134">**What are the capabilities of the people available to work on a feature, and how to best leverage these resources?**</span></span> <span data-ttu-id="cc431-135">每位 EF 小組的成員以至於我們的社群參與者，在不同領域各有不同程度的經驗，而我們必須據此進行規劃。</span><span class="sxs-lookup"><span data-stu-id="cc431-135">Each member of the EF team and even our community contributors have different levels of experience in different areas and we have to plan accordingly.</span></span> <span data-ttu-id="cc431-136">即使我們希望將所有人力皆集中在像是 GroupBy 翻譯或多對多關聯性等特殊功能上，也無法實際付諸行動。</span><span class="sxs-lookup"><span data-stu-id="cc431-136">Even if we wanted to have “all hands on deck” to work on a specific feature like GroupBy translations, or many-to-many, that wouldn’t be practical.</span></span>

<span data-ttu-id="cc431-137">如上所述，此程序會隨著每次發行而變動，而我們也希望在未來為開發人員社群的成員提供更多機會，以為發行計劃投入更多心力，例如將對功能及發行計劃本身之建議草稿的檢閱過程簡化。</span><span class="sxs-lookup"><span data-stu-id="cc431-137">As mentioned before, this process evolves on every release, and in the future we would like to add more opportunities for members of the developer community to provide inputs into release plans, e.g. by making it easier to review proposed drafts of the features and of the release plan itself.</span></span>
