---
title: 設計階段 DbCoNtext 建立-EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/16/2019
uid: core/miscellaneous/cli/dbcontext-creation
ms.openlocfilehash: f44f0648678af5a70e5171d69692bde1c1d5e0eb
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416739"
---
# <a name="design-time-dbcontext-creation"></a><span data-ttu-id="d0891-102">設計階段 DbContext 建立</span><span class="sxs-lookup"><span data-stu-id="d0891-102">Design-time DbContext Creation</span></span>

<span data-ttu-id="d0891-103">某些 EF Core 工具命令（例如，[遷移][1]命令）需要在設計階段建立衍生的 `DbContext` 實例，才能收集應用程式實體類型的詳細資料，以及它們如何對應到資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="d0891-103">Some of the EF Core Tools commands (for example, the [Migrations][1] commands) require a derived `DbContext` instance to be created at design time in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="d0891-104">在大部分的情況下，最好是以類似于[在執行時間設定][2]它的方式來設定建立 `DbContext`。</span><span class="sxs-lookup"><span data-stu-id="d0891-104">In most cases, it is desirable that the `DbContext` thereby created is configured in a similar way to how it would be [configured at run time][2].</span></span>

<span data-ttu-id="d0891-105">工具有各種方式可嘗試建立 `DbContext`：</span><span class="sxs-lookup"><span data-stu-id="d0891-105">There are various ways the tools try to create the `DbContext`:</span></span>

## <a name="from-application-services"></a><span data-ttu-id="d0891-106">從應用程式服務</span><span class="sxs-lookup"><span data-stu-id="d0891-106">From application services</span></span>

<span data-ttu-id="d0891-107">如果您的啟始專案使用[ASP.NET Core Web 主機][3]或[.Net Core 泛型主機][4]，則工具會嘗試從應用程式的服務提供者取得 DbCoNtext 物件。</span><span class="sxs-lookup"><span data-stu-id="d0891-107">If your startup project uses the [ASP.NET Core Web Host][3] or [.NET Core Generic Host][4], the tools try to obtain the DbContext object from the application's service provider.</span></span>

<span data-ttu-id="d0891-108">這些工具會先嘗試叫用 `Program.CreateHostBuilder()`、呼叫 `Build()`，然後存取 `Services` 屬性，藉以取得服務提供者。</span><span class="sxs-lookup"><span data-stu-id="d0891-108">The tools first try to obtain the service provider by invoking `Program.CreateHostBuilder()`, calling `Build()`, then accessing the `Services` property.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/ApplicationService.cs)]

> [!NOTE]
> <span data-ttu-id="d0891-109">當您建立新的 ASP.NET Core 應用程式時，預設會包含此攔截。</span><span class="sxs-lookup"><span data-stu-id="d0891-109">When you create a new ASP.NET Core application, this hook is included by default.</span></span>

<span data-ttu-id="d0891-110">`DbContext` 本身和其任何函式中的任何相依性，都必須在應用程式的服務提供者中註冊為服務。</span><span class="sxs-lookup"><span data-stu-id="d0891-110">The `DbContext` itself and any dependencies in its constructor need to be registered as services in the application's service provider.</span></span> <span data-ttu-id="d0891-111">在[接受 `DbContextOptions<TContext>` 實例做為引數][5]並使用[`AddDbContext<TContext>` 方法][6]的 `DbContext` 上，可以輕鬆達成此目的。</span><span class="sxs-lookup"><span data-stu-id="d0891-111">This can be easily achieved by having [a constructor on the `DbContext` that takes an instance of `DbContextOptions<TContext>` as an argument][5] and using the [`AddDbContext<TContext>` method][6].</span></span>

## <a name="using-a-constructor-with-no-parameters"></a><span data-ttu-id="d0891-112">使用不含參數的函式</span><span class="sxs-lookup"><span data-stu-id="d0891-112">Using a constructor with no parameters</span></span>

<span data-ttu-id="d0891-113">如果無法從應用程式服務提供者取得 DbCoNtext，工具會在專案內尋找衍生的 `DbContext` 型別。</span><span class="sxs-lookup"><span data-stu-id="d0891-113">If the DbContext can't be obtained from the application service provider, the tools look for the derived `DbContext` type inside the project.</span></span> <span data-ttu-id="d0891-114">然後，他們會嘗試使用不含參數的函式來建立實例。</span><span class="sxs-lookup"><span data-stu-id="d0891-114">Then they try to create an instance using a constructor with no parameters.</span></span> <span data-ttu-id="d0891-115">如果 `DbContext` 是使用[`OnConfiguring`][7]方法設定的，這可以是預設的函式。</span><span class="sxs-lookup"><span data-stu-id="d0891-115">This can be the default constructor if the `DbContext` is configured using the [`OnConfiguring`][7] method.</span></span>

## <a name="from-a-design-time-factory"></a><span data-ttu-id="d0891-116">從設計階段 factory</span><span class="sxs-lookup"><span data-stu-id="d0891-116">From a design-time factory</span></span>

<span data-ttu-id="d0891-117">您也可以藉由執行 `IDesignTimeDbContextFactory<TContext>` 介面，告訴工具如何建立您的 DbCoNtext：如果在與衍生 `DbContext` 相同的專案或在應用程式的啟始專案中找到執行此介面的類別，則工具會略過建立 DbCoNtext 並改用設計階段 factory 的其他方式。</span><span class="sxs-lookup"><span data-stu-id="d0891-117">You can also tell the tools how to create your DbContext by implementing the `IDesignTimeDbContextFactory<TContext>` interface: If a class implementing this interface is found in either the same project as the derived `DbContext` or in the application's startup project, the tools bypass the other ways of creating the DbContext and use the design-time factory instead.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/BloggingContextFactory.cs)]

> [!NOTE]
> <span data-ttu-id="d0891-118">`args` 參數目前未使用。</span><span class="sxs-lookup"><span data-stu-id="d0891-118">The `args` parameter is currently unused.</span></span> <span data-ttu-id="d0891-119">追蹤從工具指定設計階段引數的能力時發生[問題][8]。</span><span class="sxs-lookup"><span data-stu-id="d0891-119">There is [an issue][8] tracking the ability to specify design-time arguments from the tools.</span></span>

<span data-ttu-id="d0891-120">如果您需要在設計階段與執行時間不同的情況下設定 DbCoNtext，則設計階段 factory 特別有用，如果 `DbContext` 的分析程式接受其他參數，而不是在 DI 中註冊，如果您不是使用 DI，或基於某些原因，您不想在 ASP.NET Core 應用程式的 `Main` 類別中有 `BuildWebHost` 方法。</span><span class="sxs-lookup"><span data-stu-id="d0891-120">A design-time factory can be especially useful if you need to configure the DbContext differently for design time than at run time, if the `DbContext` constructor takes additional parameters are not registered in DI, if you are not using DI at all, or if for some reason you prefer not to have a `BuildWebHost` method in your ASP.NET Core application's `Main` class.</span></span>

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [6]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [7]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [8]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
