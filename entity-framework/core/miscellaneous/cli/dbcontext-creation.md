---
title: 設計階段 DbContext 建立-EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/27/2017
ms.technology: entity-framework-core
uid: core/miscellaneous/cli/dbcontext-creation
ms.openlocfilehash: 8b38d300d31038bdf5cd877aa3c42b7f5f97eabc
ms.sourcegitcommit: 7113e8675f26cbb546200824512078bf360225df
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
ms.locfileid: "30202480"
---
<a name="design-time-dbcontext-creation"></a><span data-ttu-id="707c9-102">設計階段 DbContext 建立</span><span class="sxs-lookup"><span data-stu-id="707c9-102">Design-time DbContext Creation</span></span>
==============================
<span data-ttu-id="707c9-103">某些 EF Core工具命令 (例如，[移轉][ 1]命令) 需要衍生`DbContext`以收集應用程式的相關詳細資料，在設計階段建立的執行個體實體類型和它們如何對應到資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="707c9-103">Some of the EF Core Tools commands (for example, the [Migrations][1] commands) require a derived `DbContext` instance to be created at design time in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="707c9-104">在大部分情況下，最好的`DbContext`藉此建立要如何將以類似方式設定[設定在執行階段][2]。</span><span class="sxs-lookup"><span data-stu-id="707c9-104">In most cases, it is desirable that the `DbContext` thereby created is configured in a similar way to how it would be [configured at run time][2].</span></span>

<span data-ttu-id="707c9-105">工具會嘗試建立可以使用各種方式`DbContext`:</span><span class="sxs-lookup"><span data-stu-id="707c9-105">There are various ways the tools try to create the `DbContext`:</span></span>

<a name="from-application-services"></a><span data-ttu-id="707c9-106">從應用程式服務</span><span class="sxs-lookup"><span data-stu-id="707c9-106">From application services</span></span>
-------------------------
<span data-ttu-id="707c9-107">如果您的啟始專案的 ASP.NET Core 應用程式，工具會嘗試從應用程式的服務提供者取得 DbContext 物件。</span><span class="sxs-lookup"><span data-stu-id="707c9-107">If your startup project is an ASP.NET Core app, the tools try to obtain the DbContext object from the application's service provider.</span></span>

<span data-ttu-id="707c9-108">此工具第一次嘗試叫用來取得服務提供者`Program.BuildWebHost()`和存取`IWebHost.Services`屬性。</span><span class="sxs-lookup"><span data-stu-id="707c9-108">The tool first try to obtain the service provider by invoking `Program.BuildWebHost()` and accessing the `IWebHost.Services` property.</span></span>

> [!NOTE]
> <span data-ttu-id="707c9-109">當您建立新的 ASP.NET Core 2.0 應用程式時，預設會包含此攔截程序。</span><span class="sxs-lookup"><span data-stu-id="707c9-109">When you create a new ASP.NET Core 2.0 application, this hook is included by default.</span></span> <span data-ttu-id="707c9-110">在舊版的 EF Core 與 ASP.NET Core，工具會嘗試叫用`Startup.ConfigureServices`直接為了取得應用程式的服務提供者，但這個模式無法再正常運作中 ASP.NET Core 2.0 應用程式。</span><span class="sxs-lookup"><span data-stu-id="707c9-110">In previous versions of EF Core and ASP.NET Core, the tools try to invoke `Startup.ConfigureServices` directly in order to obtain the application's service provider, but this pattern no longer works correctly in ASP.NET Core 2.0 applications.</span></span> <span data-ttu-id="707c9-111">如果您要升級為 2.0 的 ASP.NET Core 1.x 應用程式，您可以[修改您`Program`類別，以符合新模式][3]。</span><span class="sxs-lookup"><span data-stu-id="707c9-111">If you are upgrading an ASP.NET Core 1.x application to 2.0, you can [modify your `Program` class to follow the new pattern][3].</span></span>

<span data-ttu-id="707c9-112">`DbContext`本身和任何相依性，其建構函式中的需要註冊應用程式的服務提供者服務的身分。</span><span class="sxs-lookup"><span data-stu-id="707c9-112">The `DbContext` itself and any dependencies in its constructor need to be registered as services in the application's service provider.</span></span> <span data-ttu-id="707c9-113">這可以輕易地達成具有[建構函式`DbContext`的執行個體的`DbContextOptions<TContext>`做為引數][ 4]和使用[ `AddDbContext<TContext>` 方法][5].</span><span class="sxs-lookup"><span data-stu-id="707c9-113">This can be easily achieved by having [a constructor on the `DbContext` that takes an instance of `DbContextOptions<TContext>` as a argument][4] and using the [`AddDbContext<TContext>` method][5].</span></span>

<a name="using-a-constructor-with-no-parameters"></a><span data-ttu-id="707c9-114">使用不含任何參數的建構函式</span><span class="sxs-lookup"><span data-stu-id="707c9-114">Using a constructor with no parameters</span></span>
--------------------------------------
<span data-ttu-id="707c9-115">如果無法從應用程式服務提供者取得 DbContext，工具尋找衍生`DbContext`專案內的類型。</span><span class="sxs-lookup"><span data-stu-id="707c9-115">If the DbContext can't be obtained from the application service provider, the tools look for the derived `DbContext` type inside the project.</span></span> <span data-ttu-id="707c9-116">然後嘗試建立使用不含任何參數的建構函式的執行個體。</span><span class="sxs-lookup"><span data-stu-id="707c9-116">Then they try to create an instance using a constructor with no parameters.</span></span> <span data-ttu-id="707c9-117">這可以是預設建構函式，如果`DbContext`已設定為使用[ `OnConfiguring` ] [ 6]方法。</span><span class="sxs-lookup"><span data-stu-id="707c9-117">This can be the default constructor if the `DbContext` is configured using the [`OnConfiguring`][6] method.</span></span>

<a name="from-a-design-time-factory"></a><span data-ttu-id="707c9-118">從設計階段 factory</span><span class="sxs-lookup"><span data-stu-id="707c9-118">From a design-time factory</span></span>
--------------------------
<span data-ttu-id="707c9-119">您也可以分辨工具如何實作來建立您的 DbContext`IDesignTimeDbContextFactory<TContext>`介面： 如果實作此介面的類別中找到 衍生的專案`DbContext`或在應用程式的啟始專案，這些工具略過以其他方式改為建立的 DbContext 和使用的設計階段處理站。</span><span class="sxs-lookup"><span data-stu-id="707c9-119">You can also tell the tools how to create your DbContext by implementing the `IDesignTimeDbContextFactory<TContext>` interface: If a class implementing this interface is found in either the same project as the derived `DbContext` or in the application's startup project, the tools bypass the other ways of creating the DbContext and use the design-time factory instead.</span></span>

``` csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Design;
using Microsoft.EntityFrameworkCore.Infrastructure;

namespace MyProject
{
    public class BloggingContextFactory : IDesignTimeDbContextFactory<BloggingContext>
    {
        public BloggingContext CreateDbContext(string[] args)
        {
            var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
            optionsBuilder.UseSqlite("Data Source=blog.db");

            return new BloggingContext(optionsBuilder.Options);
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="707c9-120">`args`參數是目前未使用。</span><span class="sxs-lookup"><span data-stu-id="707c9-120">The `args` parameter is currently unused.</span></span> <span data-ttu-id="707c9-121">沒有[問題][ 7]追蹤指定設計階段工具的引數的能力。</span><span class="sxs-lookup"><span data-stu-id="707c9-121">There is [an issue][7] tracking the ability to specify design-time arguments from the tools.</span></span>

<span data-ttu-id="707c9-122">設計階段處理站會特別有用，如果您需要以不同的方式設定的設計階段和執行階段的 DbContext 如果`DbContext`建構函式會採用額外的參數未登錄在 DI，如果您未使用 DI 完全或部分原因不想要有`BuildWebHost`中 ASP.NET Core 應用程式的方法</span><span class="sxs-lookup"><span data-stu-id="707c9-122">A design-time factory can be especially useful if you need to configure the DbContext differently for design time than at run time, if the `DbContext` constructor takes additional parameters are not registered in DI, if you are not using DI at all, or if for some reason you prefer not to have a `BuildWebHost` method in your ASP.NET Core application's</span></span>  
<span data-ttu-id="707c9-123">`Main` 類別。</span><span class="sxs-lookup"><span data-stu-id="707c9-123">`Main` class.</span></span>

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: https://docs.microsoft.com/aspnet/core/migration/1x-to-2x/#update-main-method-in-programcs
  [4]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [5]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [6]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [7]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
