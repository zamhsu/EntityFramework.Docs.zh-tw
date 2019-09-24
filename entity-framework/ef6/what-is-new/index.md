---
title: 新功能 - EF6
author: divega
ms.date: 09/12/2019
ms.assetid: 41d1f86b-ce66-4bf2-8963-48514406fb4c
ms.openlocfilehash: 568790d9c9bb7dd2213907bef8fa090710cd3ba0
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149120"
---
# <a name="whats-new-in-ef6"></a><span data-ttu-id="89cf0-102">EF6 的新功能</span><span class="sxs-lookup"><span data-stu-id="89cf0-102">What's New in EF6</span></span>

<span data-ttu-id="89cf0-103">強烈建議您使用 Entity Framework 的最新發行版本，以確保您取得最新的功能和最高的穩定性。</span><span class="sxs-lookup"><span data-stu-id="89cf0-103">We highly recommend that you use the latest released version of Entity Framework to ensure you get the latest features and the highest stability.</span></span>
<span data-ttu-id="89cf0-104">不過，我們了解您可能需要使用先前的版本，或者可能想要體驗最新發行前版本中的新改善。</span><span class="sxs-lookup"><span data-stu-id="89cf0-104">However, we realize that you may need to use a previous version, or that you may want to experiment with new improvements in the latest pre-release.</span></span>
<span data-ttu-id="89cf0-105">若要安裝特定版本的 EF，請參閱[取得 Entity Framework](~/ef6/fundamentals/install.md)。</span><span class="sxs-lookup"><span data-stu-id="89cf0-105">To install specific versions of EF, see [Get Entity Framework](~/ef6/fundamentals/install.md).</span></span>

## <a name="ef-630"></a><span data-ttu-id="89cf0-106">EF 6.3.0</span><span class="sxs-lookup"><span data-stu-id="89cf0-106">EF 6.3.0</span></span>

<span data-ttu-id="89cf0-107">EF 6.3.0 執行階段已於 2019 年 9 月發行至 NuGet。</span><span class="sxs-lookup"><span data-stu-id="89cf0-107">The EF 6.3.0 runtime was released to NuGet in September 2019.</span></span> <span data-ttu-id="89cf0-108">此版本主要目標為將使用 EF 6 的現有應用程式促進移轉至 .NET Core 3.0。</span><span class="sxs-lookup"><span data-stu-id="89cf0-108">The main goal of this release was to facilitate migrating existing applications that use EF 6 to .NET Core 3.0.</span></span> <span data-ttu-id="89cf0-109">社群也貢獻了一些 Bug 修正和增強功能。</span><span class="sxs-lookup"><span data-stu-id="89cf0-109">The community has also contributed several bug fixes and enhancements.</span></span> <span data-ttu-id="89cf0-110">有關詳細資料，請參閱每個 6.3.0 [里程碑](https://github.com/aspnet/EntityFramework6/milestones?state=closed)中已關閉的問題。</span><span class="sxs-lookup"><span data-stu-id="89cf0-110">See the issues closed in each 6.3.0 [milestone](https://github.com/aspnet/EntityFramework6/milestones?state=closed) for details.</span></span> <span data-ttu-id="89cf0-111">以下是一些較需注意的事項：</span><span class="sxs-lookup"><span data-stu-id="89cf0-111">Here are some of the more notable ones:</span></span>

- <span data-ttu-id="89cf0-112">支援 .NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="89cf0-112">Support for .NET Core 3.0</span></span>
  - <span data-ttu-id="89cf0-113">現在，除了 .NET Framework 4.x 以外，EntityFramework 套件也針對 .NET Standard 2.1</span><span class="sxs-lookup"><span data-stu-id="89cf0-113">The EntityFramework package now targets .NET Standard 2.1 in addition to .NET Framework 4.x</span></span>
  - <span data-ttu-id="89cf0-114">移轉命令已重寫為在程序外執行，並可使用 SDK 樣式的專案</span><span class="sxs-lookup"><span data-stu-id="89cf0-114">The migrations commands have been rewritten to execute out of process and work with SDK-style projects</span></span>
- <span data-ttu-id="89cf0-115">支援 SQL Server HierarchyId</span><span class="sxs-lookup"><span data-stu-id="89cf0-115">Support for SQL Server HierarchyId</span></span>
- <span data-ttu-id="89cf0-116">已改善與 Roslyn 和 NuGet PackageReference 的相容性</span><span class="sxs-lookup"><span data-stu-id="89cf0-116">Improved compatibility with Roslyn and NuGet PackageReference</span></span>
- <span data-ttu-id="89cf0-117">已新增 ef6.exe，用於從組件啟用、新增、撰寫指令碼並套用移轉。</span><span class="sxs-lookup"><span data-stu-id="89cf0-117">Added ef6.exe for enabling, adding, scripting, and applying migrations from assemblies.</span></span> <span data-ttu-id="89cf0-118">此項目取代 migrate.exe</span><span class="sxs-lookup"><span data-stu-id="89cf0-118">This replaces migrate.exe</span></span>

## <a name="past-releases"></a><span data-ttu-id="89cf0-119">過去版本</span><span class="sxs-lookup"><span data-stu-id="89cf0-119">Past Releases</span></span>

<span data-ttu-id="89cf0-120">[過去版本](past-releases.md)頁面包含所有舊版 EF 與每一版所引進之主要功能的封存。</span><span class="sxs-lookup"><span data-stu-id="89cf0-120">The [Past Releases](past-releases.md) page contains an archive of all previous versions of EF and the major features that were introduced on each release.</span></span>
