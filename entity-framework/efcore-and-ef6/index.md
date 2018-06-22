---
title: 比較 EF Core 與 EF6
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: a6b9cd22-6803-4c6c-a4d4-21147c0a81cb
uid: efcore-and-ef6/index
ms.openlocfilehash: 4609ecbc9e24d8a359694d256523c64141b5ff62
ms.sourcegitcommit: 2ef0a4a90b01edd22b9206f8729b8de459ef8cab
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2018
ms.locfileid: "30002752"
---
# <a name="compare-ef-core--ef6"></a><span data-ttu-id="f6072-102">比較 EF Core 與 EF6</span><span class="sxs-lookup"><span data-stu-id="f6072-102">Compare EF Core & EF6</span></span>

<span data-ttu-id="f6072-103">Entity Framework 分為 Entity Framework Core 及 Entity Framework 6 兩種版本。</span><span class="sxs-lookup"><span data-stu-id="f6072-103">There are two different versions of Entity Framework, Entity Framework Core and Entity Framework 6.</span></span>

## <a name="entity-framework-6"></a><span data-ttu-id="f6072-104">Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="f6072-104">Entity Framework 6</span></span>

<span data-ttu-id="f6072-105">Entity Framework 6 (EF6) 是已經過試用並測試的資料存取技術，具有多年的功能和穩定性。</span><span class="sxs-lookup"><span data-stu-id="f6072-105">Entity Framework 6 (EF6) is a tried and tested data access technology with many years of features and stabilization.</span></span> <span data-ttu-id="f6072-106">它是在 2008 年當成 .NET Framework 3.5 SP1 和 Visual Studio 2008 SP1 的一部分第一次發行。</span><span class="sxs-lookup"><span data-stu-id="f6072-106">It first released in 2008, as part of .NET Framework 3.5 SP1 and Visual Studio 2008 SP1.</span></span> <span data-ttu-id="f6072-107">自 EF4.1 版開始，此產品即以 [EntityFramework NuGet 套件](https://www.nuget.org/packages/EntityFramework/)之名出貨，這是 NuGet.org 上目前最受歡迎的套件之一。</span><span class="sxs-lookup"><span data-stu-id="f6072-107">Starting with the EF4.1 release it has shipped as the [EntityFramework NuGet package](https://www.nuget.org/packages/EntityFramework/) - currently one of the most popular packages on NuGet.org.</span></span>

<span data-ttu-id="f6072-108">EF6 仍然是受支援的產品，在未來的某個時間仍會看到 Bug 修正和小幅度的改善。</span><span class="sxs-lookup"><span data-stu-id="f6072-108">EF6 continues to be a supported product, and will continue to see bug fixes and minor improvements for some time to come.</span></span>

## <a name="entity-framework-core"></a><span data-ttu-id="f6072-109">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="f6072-109">Entity Framework Core</span></span>

<span data-ttu-id="f6072-110">Entity Framework Core (EF Core) 是 Entity Framework 的輕量型、可擴充且跨平台版本。</span><span class="sxs-lookup"><span data-stu-id="f6072-110">Entity Framework Core (EF Core) is a lightweight, extensible, and cross-platform version of Entity Framework.</span></span> <span data-ttu-id="f6072-111">與 EF6 相較之下，EF Core 引進許多改善和新功能。</span><span class="sxs-lookup"><span data-stu-id="f6072-111">EF Core introduces many improvements and new features when compared with EF6.</span></span> <span data-ttu-id="f6072-112">同時，EF Core 是新的程式碼基底，不像 EF6 一樣成熟。</span><span class="sxs-lookup"><span data-stu-id="f6072-112">At the same time, EF Core is a new code base and not as mature as EF6.</span></span>

<span data-ttu-id="f6072-113">EF Core 保有 EF6 的開發人員體驗，而且大部分的最上層 API 也都相同；因此，使用 EF6 的人員會覺得 EF Core 十分熟悉。</span><span class="sxs-lookup"><span data-stu-id="f6072-113">EF Core keeps the developer experience from EF6, and most of the top-level APIs remain the same too, so EF Core will feel very familiar to folks who have used EF6.</span></span> <span data-ttu-id="f6072-114">同時，EF Core 是透過一組全新的核心元件所建置。</span><span class="sxs-lookup"><span data-stu-id="f6072-114">At the same time, EF Core is built over a completely new set of core components.</span></span> <span data-ttu-id="f6072-115">這表示 EF Core 不會自動繼承 EF6 的所有功能。</span><span class="sxs-lookup"><span data-stu-id="f6072-115">This means EF Core doesn't automatically inherit all the features from EF6.</span></span> <span data-ttu-id="f6072-116">這些功能中有一些會繼續在未來的版本中出現，但有一些比較不常用的功能將不會出現在 EF Core 中。</span><span class="sxs-lookup"><span data-stu-id="f6072-116">While some of these features will show up in future releases, other less commonly used features will not be implemented in EF Core.</span></span>

<span data-ttu-id="f6072-117">我們也可透過這款可擴充輕量型新核心，為 EF Core 新增一些 EF6 沒有的功能 (例如替代鍵、批次更新，以及 LINQ 查詣中的混合用戶端/資料庫評估)。</span><span class="sxs-lookup"><span data-stu-id="f6072-117">The new, extensible, and lightweight core has also allowed us to add some features to EF Core that will not be implemented in EF6 (such as alternate keys, batch updates, and mixed client/database evaluation in LINQ queries).</span></span>
