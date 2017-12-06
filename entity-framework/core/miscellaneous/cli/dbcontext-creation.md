---
title: "設計階段 DbContext 建立-EF 核心"
author: bricelam
ms.author: bricelam
ms.date: 10/27/2017
ms.technology: entity-framework-core
ms.openlocfilehash: 5fcd9e362d76127e7acadd9e552ef3ac90967a37
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
<a name="design-time-dbcontext-creation"></a><span data-ttu-id="40bc7-102">設計階段 DbContext 建立</span><span class="sxs-lookup"><span data-stu-id="40bc7-102">Design-time DbContext Creation</span></span>
==============================
<span data-ttu-id="40bc7-103">有些 EF 工具命令需要在設計上建立的 DbContext 執行個體的時間 （例如，當執行移轉命令）。</span><span class="sxs-lookup"><span data-stu-id="40bc7-103">Some of the EF Tools commands require a DbContext instance to be created at design time (for example, when running Migrations commands).</span></span> <span data-ttu-id="40bc7-104">有許多種，工具會嘗試建立它。</span><span class="sxs-lookup"><span data-stu-id="40bc7-104">There are various ways the tools try to create it.</span></span>

<a name="from-application-services"></a><span data-ttu-id="40bc7-105">從應用程式服務</span><span class="sxs-lookup"><span data-stu-id="40bc7-105">From application services</span></span>
-------------------------
<span data-ttu-id="40bc7-106">如果您的啟始專案的 ASP.NET Core 應用程式，工具會嘗試從應用程式的服務提供者取得 DbContext 物件。</span><span class="sxs-lookup"><span data-stu-id="40bc7-106">If your startup project is an ASP.NET Core app, the tools try to obtain the DbContext object from the application's service provider.</span></span> <span data-ttu-id="40bc7-107">他們取得叫用`Program.BuildWebHost()`和存取`IWebHost.Services`屬性。</span><span class="sxs-lookup"><span data-stu-id="40bc7-107">They obtain it by invoking `Program.BuildWebHost()` and accessing the `IWebHost.Services` property.</span></span> <span data-ttu-id="40bc7-108">使用註冊任何 DbContext`IServiceCollection.AddDbContext<TContext>()`可以找到並以此方式建立。</span><span class="sxs-lookup"><span data-stu-id="40bc7-108">Any DbContext registered using `IServiceCollection.AddDbContext<TContext>()` can be found and created this way.</span></span> <span data-ttu-id="40bc7-109">此模式已[ASP.NET Core 2.0 中導入][1]</span><span class="sxs-lookup"><span data-stu-id="40bc7-109">This pattern was [introduced in ASP.NET Core 2.0][1]</span></span>

<a name="using-the-default-constructor"></a><span data-ttu-id="40bc7-110">使用預設建構函式</span><span class="sxs-lookup"><span data-stu-id="40bc7-110">Using the default constructor</span></span>
-----------------------------
<span data-ttu-id="40bc7-111">如果無法從應用程式服務提供者取得 DbContext，工具會尋找專案內的 DbContext 類型。</span><span class="sxs-lookup"><span data-stu-id="40bc7-111">If the DbContext can't be obtained from the application service provider, the tools look for the DbContext type inside the project.</span></span> <span data-ttu-id="40bc7-112">嘗試使用其預設建構函式加以建立。</span><span class="sxs-lookup"><span data-stu-id="40bc7-112">They try to create it using its default constructor.</span></span>

<a name="from-a-design-time-factory"></a><span data-ttu-id="40bc7-113">從設計階段 factory</span><span class="sxs-lookup"><span data-stu-id="40bc7-113">From a design-time factory</span></span>
--------------------------
<span data-ttu-id="40bc7-114">您也可以分辨工具如何實作來建立您的 DbContext `IDesignTimeDbContextFactory`。</span><span class="sxs-lookup"><span data-stu-id="40bc7-114">You can also tell the tools how to create your DbContext by implementing `IDesignTimeDbContextFactory`.</span></span> <span data-ttu-id="40bc7-115">如果您的專案內找到實作此介面的類別，則工具會略過之其他方式建立的 DbContext。</span><span class="sxs-lookup"><span data-stu-id="40bc7-115">If a class implementing this interface is found inside your project, the tools bypass the other ways of creating the DbContext.</span></span>
<span data-ttu-id="40bc7-116">它們一律會使用在設計階段的處理站。</span><span class="sxs-lookup"><span data-stu-id="40bc7-116">They always use the factory at design time.</span></span> <span data-ttu-id="40bc7-117">此處理站是特別有用，如果您需要在執行階段，於設計階段的不同的方式設定 DbContext。</span><span class="sxs-lookup"><span data-stu-id="40bc7-117">A factory is especially useful if you need to configure the DbContext differently for design time than at runtime.</span></span>

``` csharp
using Microsoft.EntityFrameworkCore;
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
> <span data-ttu-id="40bc7-118">`args`參數是目前未使用。</span><span class="sxs-lookup"><span data-stu-id="40bc7-118">The `args` parameter is currently unused.</span></span> <span data-ttu-id="40bc7-119">沒有[問題][ 2]追蹤指定設計階段工具的引數的能力。</span><span class="sxs-lookup"><span data-stu-id="40bc7-119">There is [an issue][2] tracking the ability to specify design-time arguments from the tools.</span></span>

  [1]: https://docs.microsoft.com/aspnet/core/migration/1x-to-2x/#update-main-method-in-programcs
  [2]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
