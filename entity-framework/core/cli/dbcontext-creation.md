---
title: 設計階段 DbCoNtext 建立-EF Core
description: 使用 Entity Framework Core 建立設計階段 DbCoNtext 的策略
author: bricelam
ms.date: 10/27/2020
uid: core/cli/dbcontext-creation
ms.openlocfilehash: 1a2c0e853047cf4ab54a320d0bef413a114e90bc
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543402"
---
# <a name="design-time-dbcontext-creation"></a><span data-ttu-id="e4f55-103">設計階段 DbContext 建立</span><span class="sxs-lookup"><span data-stu-id="e4f55-103">Design-time DbContext Creation</span></span>

<span data-ttu-id="e4f55-104">某些 EF Core 工具命令 (例如，) 需要在設計階段建立 [衍生的][1] 實例，才能 `DbContext` 收集有關應用程式之實體類型的詳細資料，以及它們如何對應到資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="e4f55-104">Some of the EF Core Tools commands (for example, the [Migrations][1] commands) require a derived `DbContext` instance to be created at design time in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="e4f55-105">在大部分的情況下，最好是以 `DbContext` 類似于 [執行時間設定][2]它的方式來設定建立。</span><span class="sxs-lookup"><span data-stu-id="e4f55-105">In most cases, it is desirable that the `DbContext` thereby created is configured in a similar way to how it would be [configured at run time][2].</span></span>

<span data-ttu-id="e4f55-106">工具會嘗試建立下列各種方式 `DbContext` ：</span><span class="sxs-lookup"><span data-stu-id="e4f55-106">There are various ways the tools try to create the `DbContext`:</span></span>

## <a name="from-application-services"></a><span data-ttu-id="e4f55-107">從應用程式服務</span><span class="sxs-lookup"><span data-stu-id="e4f55-107">From application services</span></span>

<span data-ttu-id="e4f55-108">如果您的啟始專案使用 [ASP.NET Core Web 主機][3] 或 [.Net Core 泛型主機][4]，則工具會嘗試從應用程式的服務提供者取得 DbCoNtext 物件。</span><span class="sxs-lookup"><span data-stu-id="e4f55-108">If your startup project uses the [ASP.NET Core Web Host][3] or [.NET Core Generic Host][4], the tools try to obtain the DbContext object from the application's service provider.</span></span>

<span data-ttu-id="e4f55-109">這些工具會先藉由 `Program.CreateHostBuilder()` 叫用、呼叫 `Build()` ，然後存取屬性來嘗試取得服務提供者 `Services` 。</span><span class="sxs-lookup"><span data-stu-id="e4f55-109">The tools first try to obtain the service provider by invoking `Program.CreateHostBuilder()`, calling `Build()`, then accessing the `Services` property.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/CommandLine/ApplicationService.cs#ApplicationService)]

> [!NOTE]
> <span data-ttu-id="e4f55-110">當您建立新的 ASP.NET Core 應用程式時，預設會包含此攔截程式。</span><span class="sxs-lookup"><span data-stu-id="e4f55-110">When you create a new ASP.NET Core application, this hook is included by default.</span></span>

<span data-ttu-id="e4f55-111">`DbContext`本身和其任何程式中的任何相依性都必須註冊為應用程式服務提供者中的服務。</span><span class="sxs-lookup"><span data-stu-id="e4f55-111">The `DbContext` itself and any dependencies in its constructor need to be registered as services in the application's service provider.</span></span> <span data-ttu-id="e4f55-112">只要將的函[ `DbContext` 式 `DbContextOptions<TContext>` 作為引數][5]，然後使用[ `AddDbContext<TContext>` 方法][6]，就可以輕鬆達成這個目的。</span><span class="sxs-lookup"><span data-stu-id="e4f55-112">This can be easily achieved by having [a constructor on the `DbContext` that takes an instance of `DbContextOptions<TContext>` as an argument][5] and using the [`AddDbContext<TContext>` method][6].</span></span>

## <a name="using-a-constructor-with-no-parameters"></a><span data-ttu-id="e4f55-113">使用沒有參數的函式</span><span class="sxs-lookup"><span data-stu-id="e4f55-113">Using a constructor with no parameters</span></span>

<span data-ttu-id="e4f55-114">如果無法從應用程式服務提供者取得 DbCoNtext，工具會尋找專案內的衍生 `DbContext` 類型。</span><span class="sxs-lookup"><span data-stu-id="e4f55-114">If the DbContext can't be obtained from the application service provider, the tools look for the derived `DbContext` type inside the project.</span></span> <span data-ttu-id="e4f55-115">然後，他們會嘗試使用不含參數的函式來建立實例。</span><span class="sxs-lookup"><span data-stu-id="e4f55-115">Then they try to create an instance using a constructor with no parameters.</span></span> <span data-ttu-id="e4f55-116">如果是使用方法進行設定，這可以是預設的函式 `DbContext` [`OnConfiguring`][7] 。</span><span class="sxs-lookup"><span data-stu-id="e4f55-116">This can be the default constructor if the `DbContext` is configured using the [`OnConfiguring`][7] method.</span></span>

## <a name="from-a-design-time-factory"></a><span data-ttu-id="e4f55-117">從設計階段工廠</span><span class="sxs-lookup"><span data-stu-id="e4f55-117">From a design-time factory</span></span>

<span data-ttu-id="e4f55-118">您也可以藉由執行介面，告訴工具如何建立您的 DbCoNtext `IDesignTimeDbContextFactory<TContext>` ：如果在相同的專案中找到實作為此介面的類別 `DbContext` ，或在應用程式的啟始專案中找到這個介面，則工具會略過其他建立 DbCoNtext 的方法，並改為使用設計階段 factory。</span><span class="sxs-lookup"><span data-stu-id="e4f55-118">You can also tell the tools how to create your DbContext by implementing the `IDesignTimeDbContextFactory<TContext>` interface: If a class implementing this interface is found in either the same project as the derived `DbContext` or in the application's startup project, the tools bypass the other ways of creating the DbContext and use the design-time factory instead.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/CommandLine/BloggingContextFactory.cs#BloggingContextFactory)]

> [!NOTE]
> <span data-ttu-id="e4f55-119">在 EFCore 5.0 之前， `args` 參數未使用 () 會看到 [此問題][8] 。</span><span class="sxs-lookup"><span data-stu-id="e4f55-119">Prior to EFCore 5.0 the `args` parameter was unused (see [this issue][8]).</span></span>
> <span data-ttu-id="e4f55-120">這在 EFCore 5.0 中已修正，而且任何額外的設計階段引數都會透過該參數傳遞至應用程式。</span><span class="sxs-lookup"><span data-stu-id="e4f55-120">This is fixed in EFCore 5.0 and any additional design-time arguments are passed into the application through that parameter.</span></span>

<span data-ttu-id="e4f55-121">如果您需要在 `DbContext` 設計階段與執行時間不同的情況下設定不同的設計階段，則如果在 `DbContext` di 中使用其他參數，則如果您完全不使用 di，或如果基於某些原因，您不想在 `CreateHostBuilder` ASP.NET Core 應用程式的類別中擁有方法，設計階段處理站可能會特別有用 `Main` 。</span><span class="sxs-lookup"><span data-stu-id="e4f55-121">A design-time factory can be especially useful if you need to configure the `DbContext` differently for design time than at run time, if the `DbContext` constructor takes additional parameters are not registered in DI, if you are not using DI at all, or if for some reason you prefer not to have a `CreateHostBuilder` method in your ASP.NET Core application's `Main` class.</span></span>

## <a name="args"></a><span data-ttu-id="e4f55-122">Args</span><span class="sxs-lookup"><span data-stu-id="e4f55-122">Args</span></span>

<span data-ttu-id="e4f55-123"><xref:Microsoft.EntityFrameworkCore.Design.IDesignTimeDbContextFactory%601.CreateDbContext%2A?displayProperty=nameWithType>和都 `Program.CreateHostBuilder` 接受命令列引數。</span><span class="sxs-lookup"><span data-stu-id="e4f55-123">Both <xref:Microsoft.EntityFrameworkCore.Design.IDesignTimeDbContextFactory%601.CreateDbContext%2A?displayProperty=nameWithType> and `Program.CreateHostBuilder` accept command line arguments.</span></span>

<span data-ttu-id="e4f55-124">從 EF Core 5.0 開始，您可以從工具指定這些引數：</span><span class="sxs-lookup"><span data-stu-id="e4f55-124">Starting in EF Core 5.0, you can specify these arguments from the tools:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="e4f55-125">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="e4f55-125">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update -- --environment Production
```

<span data-ttu-id="e4f55-126">`--`標記會指示 `dotnet ef` 將後面的所有專案視為引數，而不會嘗試將它們剖析為選項。</span><span class="sxs-lookup"><span data-stu-id="e4f55-126">The `--` token directs `dotnet ef` to treat everything that follows as an argument and not try to parse them as options.</span></span> <span data-ttu-id="e4f55-127">未使用的任何額外引數 `dotnet ef` 都會轉送至應用程式。</span><span class="sxs-lookup"><span data-stu-id="e4f55-127">Any extra arguments not used by `dotnet ef` are forwarded to the app.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="e4f55-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e4f55-128">Visual Studio</span></span>](#tab/vs)

```powershell
Update-Database -Args '--environment Production'
```

***

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/dbcontext-configuration/index
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/dbcontext-configuration/index#constructor-argument
  [6]: xref:core/dbcontext-configuration/index#using-dbcontext-with-dependency-injection
  [7]: xref:core/dbcontext-configuration/index#onconfiguring
  [8]: https://github.com/dotnet/efcore/issues/8332
