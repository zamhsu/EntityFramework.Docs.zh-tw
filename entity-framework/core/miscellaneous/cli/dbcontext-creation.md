---
title: 設計階段 DbCoNtext 建立-EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/16/2019
uid: core/miscellaneous/cli/dbcontext-creation
ms.openlocfilehash: 0b0271dcabea63a2529c091cc14cb9059d56ac8d
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672954"
---
# <a name="design-time-dbcontext-creation"></a><span data-ttu-id="9ef4c-102">設計階段 DbContext 建立</span><span class="sxs-lookup"><span data-stu-id="9ef4c-102">Design-time DbContext Creation</span></span>

<span data-ttu-id="9ef4c-103">某些 EF Core 工具命令（例如，[遷移][1]命令）需要 `DbContext` 在設計階段建立衍生實例，才能收集應用程式實體類型的詳細資料，以及它們如何對應到資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="9ef4c-103">Some of the EF Core Tools commands (for example, the [Migrations][1] commands) require a derived `DbContext` instance to be created at design time in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="9ef4c-104">在大部分的情況下，最好是以 `DbContext` 類似于[在執行時間設定][2]它的方式來設定建立的。</span><span class="sxs-lookup"><span data-stu-id="9ef4c-104">In most cases, it is desirable that the `DbContext` thereby created is configured in a similar way to how it would be [configured at run time][2].</span></span>

<span data-ttu-id="9ef4c-105">工具有各種方式可嘗試建立 `DbContext` ：</span><span class="sxs-lookup"><span data-stu-id="9ef4c-105">There are various ways the tools try to create the `DbContext`:</span></span>

## <a name="from-application-services"></a><span data-ttu-id="9ef4c-106">從應用程式服務</span><span class="sxs-lookup"><span data-stu-id="9ef4c-106">From application services</span></span>

<span data-ttu-id="9ef4c-107">如果您的啟始專案使用[ASP.NET Core Web 主機][3]或[.Net Core 泛型主機][4]，則工具會嘗試從應用程式的服務提供者取得 DbCoNtext 物件。</span><span class="sxs-lookup"><span data-stu-id="9ef4c-107">If your startup project uses the [ASP.NET Core Web Host][3] or [.NET Core Generic Host][4], the tools try to obtain the DbContext object from the application's service provider.</span></span>

<span data-ttu-id="9ef4c-108">這些工具會先藉由 `Program.CreateHostBuilder()` 叫用、呼叫 `Build()` ，然後存取屬性，來嘗試取得服務提供者 `Services` 。</span><span class="sxs-lookup"><span data-stu-id="9ef4c-108">The tools first try to obtain the service provider by invoking `Program.CreateHostBuilder()`, calling `Build()`, then accessing the `Services` property.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/ApplicationService.cs)]

> [!NOTE]
> <span data-ttu-id="9ef4c-109">當您建立新的 ASP.NET Core 應用程式時，預設會包含此攔截。</span><span class="sxs-lookup"><span data-stu-id="9ef4c-109">When you create a new ASP.NET Core application, this hook is included by default.</span></span>

<span data-ttu-id="9ef4c-110">`DbContext`本身和其任何函式中的任何相依性都必須在應用程式的服務提供者中註冊為服務。</span><span class="sxs-lookup"><span data-stu-id="9ef4c-110">The `DbContext` itself and any dependencies in its constructor need to be registered as services in the application's service provider.</span></span> <span data-ttu-id="9ef4c-111">[在 `DbContext` 接受實例 `DbContextOptions<TContext>` 做為引數][5]並使用[ `AddDbContext<TContext>` 方法][6]的上，就可以輕鬆達成此目的。</span><span class="sxs-lookup"><span data-stu-id="9ef4c-111">This can be easily achieved by having [a constructor on the `DbContext` that takes an instance of `DbContextOptions<TContext>` as an argument][5] and using the [`AddDbContext<TContext>` method][6].</span></span>

## <a name="using-a-constructor-with-no-parameters"></a><span data-ttu-id="9ef4c-112">使用不含參數的函式</span><span class="sxs-lookup"><span data-stu-id="9ef4c-112">Using a constructor with no parameters</span></span>

<span data-ttu-id="9ef4c-113">如果無法從應用程式服務提供者取得 DbCoNtext，工具會在專案內尋找衍生的 `DbContext` 型別。</span><span class="sxs-lookup"><span data-stu-id="9ef4c-113">If the DbContext can't be obtained from the application service provider, the tools look for the derived `DbContext` type inside the project.</span></span> <span data-ttu-id="9ef4c-114">然後，他們會嘗試使用不含參數的函式來建立實例。</span><span class="sxs-lookup"><span data-stu-id="9ef4c-114">Then they try to create an instance using a constructor with no parameters.</span></span> <span data-ttu-id="9ef4c-115">如果 `DbContext` 是使用方法設定，這可以是預設的函式 [`OnConfiguring`][7] 。</span><span class="sxs-lookup"><span data-stu-id="9ef4c-115">This can be the default constructor if the `DbContext` is configured using the [`OnConfiguring`][7] method.</span></span>

## <a name="from-a-design-time-factory"></a><span data-ttu-id="9ef4c-116">從設計階段 factory</span><span class="sxs-lookup"><span data-stu-id="9ef4c-116">From a design-time factory</span></span>

<span data-ttu-id="9ef4c-117">您也可以藉由執行介面，告訴工具如何建立您的 DbCoNtext `IDesignTimeDbContextFactory<TContext>` ：如果在與所衍生或應用程式啟始專案相同的專案中找到執行此介面的類別 `DbContext` ，則工具會略過建立 DbCoNtext 的其他方式，並改用設計階段 factory。</span><span class="sxs-lookup"><span data-stu-id="9ef4c-117">You can also tell the tools how to create your DbContext by implementing the `IDesignTimeDbContextFactory<TContext>` interface: If a class implementing this interface is found in either the same project as the derived `DbContext` or in the application's startup project, the tools bypass the other ways of creating the DbContext and use the design-time factory instead.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/BloggingContextFactory.cs)]

> [!NOTE]
> <span data-ttu-id="9ef4c-118">在 EFCore 5.0 之前， `args` 參數未使用（請參閱[此問題][8]）。</span><span class="sxs-lookup"><span data-stu-id="9ef4c-118">Prior to EFCore 5.0 the `args` parameter was unused (see [this issue][8]).</span></span>
> <span data-ttu-id="9ef4c-119">這在 EFCore 5.0 中是固定的，而且任何其他設計階段引數都會透過該參數傳遞至應用程式。</span><span class="sxs-lookup"><span data-stu-id="9ef4c-119">This is fixed in EFCore 5.0 and any additional design-time arguments are passed into the application through that parameter.</span></span>

<span data-ttu-id="9ef4c-120">如果您需要以不同的方式設定設計階段的 DbCoNtext，而不是在執行時間時，如果 `DbContext` 您不是使用 di，或基於某些原因，您不想在 `BuildWebHost` ASP.NET Core 應用程式的類別中有方法，設計階段 factory 會特別有用 `Main` 。</span><span class="sxs-lookup"><span data-stu-id="9ef4c-120">A design-time factory can be especially useful if you need to configure the DbContext differently for design time than at run time, if the `DbContext` constructor takes additional parameters are not registered in DI, if you are not using DI at all, or if for some reason you prefer not to have a `BuildWebHost` method in your ASP.NET Core application's `Main` class.</span></span>

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [6]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [7]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [8]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
