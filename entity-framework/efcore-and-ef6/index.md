---
title: "比較 EF Core 與 EF6"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: a6b9cd22-6803-4c6c-a4d4-21147c0a81cb
uid: efcore-and-ef6/index
ms.openlocfilehash: 4442e6931327f6a07d98aee47211ddbeed51a467
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="compare-ef-core--ef6"></a><span data-ttu-id="ef176-102">比較 EF Core 與 EF6</span><span class="sxs-lookup"><span data-stu-id="ef176-102">Compare EF Core & EF6</span></span>

<span data-ttu-id="ef176-103">有兩種版本的 Entity Framework：Entity Framework Core 和 Entity Framework 6。</span><span class="sxs-lookup"><span data-stu-id="ef176-103">There are two versions of Entity Framework, Entity Framework Core and Entity Framework 6.</span></span>

## <a name="entity-framework-6"></a><span data-ttu-id="ef176-104">Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="ef176-104">Entity Framework 6</span></span>

<span data-ttu-id="ef176-105">Entity Framework 6 (EF6) 是已經過試用並測試的資料存取技術，具有多年的功能和穩定性。</span><span class="sxs-lookup"><span data-stu-id="ef176-105">Entity Framework 6 (EF6) is a tried and tested data access technology with many years of features and stabilization.</span></span> <span data-ttu-id="ef176-106">它是在 2008 年當成 .NET Framework 3.5 SP1 和 Visual Studio 2008 SP1 的一部分第一次發行。</span><span class="sxs-lookup"><span data-stu-id="ef176-106">It first released in 2008, as part of .NET Framework 3.5 SP1 and Visual Studio 2008 SP1.</span></span> <span data-ttu-id="ef176-107">從 EF4.1 版開始，它會出貨為 [EntityFramework NuGet 套件](https://www.nuget.org/packages/EntityFramework/)，這目前是 NuGet.org 上的最常見套件。</span><span class="sxs-lookup"><span data-stu-id="ef176-107">Starting with the EF4.1 release it has shipped as the [EntityFramework NuGet package](https://www.nuget.org/packages/EntityFramework/) - currently the most popular package on NuGet.org.</span></span>

<span data-ttu-id="ef176-108">EF6 仍然是受支援的產品，在未來的某個時間仍會看到 Bug 修正和小幅度的改善。</span><span class="sxs-lookup"><span data-stu-id="ef176-108">EF6 continues to be a supported product, and will continue to see bug fixes and minor improvements for some time to come.</span></span>

## <a name="entity-framework-core"></a><span data-ttu-id="ef176-109">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="ef176-109">Entity Framework Core</span></span>

<span data-ttu-id="ef176-110">Entity Framework Core (EF Core) 是 Entity Framework 的輕量型、可擴充且跨平台版本。</span><span class="sxs-lookup"><span data-stu-id="ef176-110">Entity Framework Core (EF Core) is a lightweight, extensible, and cross-platform version of Entity Framework.</span></span> <span data-ttu-id="ef176-111">與 EF6 相較之下，EF Core 引進許多改善和新功能。</span><span class="sxs-lookup"><span data-stu-id="ef176-111">EF Core introduces many improvements and new features when compared with EF6.</span></span> <span data-ttu-id="ef176-112">同時，EF Core 是新的程式碼基底，不像 EF6 一樣成熟。</span><span class="sxs-lookup"><span data-stu-id="ef176-112">At the same time, EF Core is a new code base and not as mature as EF6.</span></span>

<span data-ttu-id="ef176-113">EF Core 保有 EF6 的開發人員體驗，而且大部分的最上層 API 也都相同；因此，使用 EF6 的人員會覺得 EF Core 十分熟悉。</span><span class="sxs-lookup"><span data-stu-id="ef176-113">EF Core keeps the developer experience from EF6, and most of the top-level APIs remain the same too, so EF Core will feel very familiar to folks who have used EF6.</span></span> <span data-ttu-id="ef176-114">同時，EF Core 是透過一組全新的核心元件所建置。</span><span class="sxs-lookup"><span data-stu-id="ef176-114">At the same time, EF Core is built over a completely new set of core components.</span></span> <span data-ttu-id="ef176-115">這表示 EF Core 不會自動繼承 EF6 的所有功能。</span><span class="sxs-lookup"><span data-stu-id="ef176-115">This means EF Core doesn't automatically inherit all the features from EF6.</span></span> <span data-ttu-id="ef176-116">其中某些功能會顯示在未來版本中 (例如，消極式載入和連線恢復功能)，而在 EF Core 中將不會實作其他較不常用的功能。</span><span class="sxs-lookup"><span data-stu-id="ef176-116">Some of these features will show up in future releases (such as lazy loading and connection resiliency), other less commonly used features will not be implemented in EF Core.</span></span>

<span data-ttu-id="ef176-117">全新、可擴充和輕量型核心也可讓我們將一些功能新增至不會在 EF6 中實作的 EF Core (例如 LINQ 查詢中的替代索引鍵以及混合式用戶端/資料庫評估)。</span><span class="sxs-lookup"><span data-stu-id="ef176-117">The new, extensible, and lightweight core has also allowed us to add some features to EF Core that will not be implemented in EF6 (such as alternate keys and mixed client/database evaluation in LINQ queries).</span></span>
