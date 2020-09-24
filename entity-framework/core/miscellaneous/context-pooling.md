---
title: DbCoNtext 共用
description: Entity Framework Core 中的 DbCoNtext 共用
author: rick-anderson
ms.author: riande
ms.date: 9/19/2020
uid: core/miscellaneous/context-pooling
ms.openlocfilehash: fd5f53ff97a73895f0c4239439730dd8cb3ecc29
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2020
ms.locfileid: "91215595"
---
# <a name="dbcontext-pooling"></a><span data-ttu-id="ce59f-103">DbContext 共用</span><span class="sxs-lookup"><span data-stu-id="ce59f-103">DbContext pooling</span></span>

<span data-ttu-id="ce59f-104">`AddDbContextPool` 啟用實例的共用 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="ce59f-104">`AddDbContextPool` enables pooling of `DbContext` instances.</span></span> <span data-ttu-id="ce59f-105">內容共用可以藉由重複使用內容實例來增加高規模案例的輸送量，例如 web 伺服器，而不是為每個要求建立新的實例。</span><span class="sxs-lookup"><span data-stu-id="ce59f-105">Context pooling can increase throughput in high-scale scenarios such as web servers by reusing context instances, rather than creating new instances for each request.</span></span>

<span data-ttu-id="ce59f-106">使用 EF Core 的 ASP.NET Core 應用程式中的一般模式牽涉到將自訂類型註冊至相依性 <xref:Microsoft.EntityFrameworkCore.DbContext> [插入](/aspnet/core/fundamentals/dependency-injection) 容器，並透過控制器或 Razor Pages 中的函式參數取得該類型的實例。</span><span class="sxs-lookup"><span data-stu-id="ce59f-106">The typical pattern in an ASP.NET Core app using EF Core involves registering a custom <xref:Microsoft.EntityFrameworkCore.DbContext> type into the [dependency injection](/aspnet/core/fundamentals/dependency-injection) container and obtaining instances of that type through constructor parameters in controllers or Razor Pages.</span></span> <span data-ttu-id="ce59f-107">使用函式插入時，會為每個要求建立新的內容實例。</span><span class="sxs-lookup"><span data-stu-id="ce59f-107">Using constructor injection, a new context instance is created for each request.</span></span>

<span data-ttu-id="ce59f-108"><xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> 啟用可重複使用的內容實例集區。</span><span class="sxs-lookup"><span data-stu-id="ce59f-108"><xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> enables a pool of reusable context instances.</span></span> <span data-ttu-id="ce59f-109">若要使用內容共用，請使用 `AddDbContextPool` 方法，而不是 `AddDbContext` 在服務註冊期間：</span><span class="sxs-lookup"><span data-stu-id="ce59f-109">To use context pooling, use the `AddDbContextPool` method instead of `AddDbContext` during service registration:</span></span>

``` csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

<span data-ttu-id="ce59f-110">`AddDbContextPool`使用時，在要求內容實例時，EF 會先檢查集區中是否有可用的實例。</span><span class="sxs-lookup"><span data-stu-id="ce59f-110">When `AddDbContextPool` is used, at the time a context instance is requested, EF first checks if there is an instance available in the pool.</span></span> <span data-ttu-id="ce59f-111">要求處理完成之後，會重設執行個體上的任何狀態，並將執行個體本身傳回到集區。</span><span class="sxs-lookup"><span data-stu-id="ce59f-111">Once the request processing finalizes, any state on the instance is reset and the instance is itself returned to the pool.</span></span>

<span data-ttu-id="ce59f-112">這在概念上類似于連接共用在 ADO.NET 提供者中的運作方式，而且具有節省部分初始化內容實例成本的優點。</span><span class="sxs-lookup"><span data-stu-id="ce59f-112">This is conceptually similar to how connection pooling operates in ADO.NET providers and has the advantage of saving some of the cost of initialization of the context instance.</span></span>

<span data-ttu-id="ce59f-113">的 `poolSize` 參數會 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> 設定集區保留的實例數目上限。</span><span class="sxs-lookup"><span data-stu-id="ce59f-113">The `poolSize` parameter of <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> sets the maximum number of instances retained by the pool.</span></span> <span data-ttu-id="ce59f-114">一旦 `poolSize` 超過此限制，就不會快取新的內容實例，而且 EF 會回到依需求建立實例的非共用行為。</span><span class="sxs-lookup"><span data-stu-id="ce59f-114">Once `poolSize` is exceeded, new context instances are not cached and  EF falls back to the non-pooling behavior of creating instances on demand.</span></span>

## <a name="limitations"></a><span data-ttu-id="ce59f-115">限制</span><span class="sxs-lookup"><span data-stu-id="ce59f-115">Limitations</span></span>

<span data-ttu-id="ce59f-116">應用程式應該進行程式碼剖析和測試，以顯示內容初始化是相當大的成本。</span><span class="sxs-lookup"><span data-stu-id="ce59f-116">Apps should be profiled and tested to show that context initialization is a significant cost.</span></span>

<span data-ttu-id="ce59f-117">`AddDbContextPool` 有幾項限制可在內容的方法中完成 `OnConfiguring` 。</span><span class="sxs-lookup"><span data-stu-id="ce59f-117">`AddDbContextPool` has a few limitations on what can be done in the `OnConfiguring` method of the context.</span></span>

> [!WARNING]  
> <span data-ttu-id="ce59f-118">避免在維護狀態的應用程式中使用內容共用。</span><span class="sxs-lookup"><span data-stu-id="ce59f-118">Avoid using context pooling in apps that maintain state.</span></span> <span data-ttu-id="ce59f-119">例如，內容中不應該跨要求共用的私用欄位。</span><span class="sxs-lookup"><span data-stu-id="ce59f-119">For example, private fields in the context that shouldn't be shared across requests.</span></span> <span data-ttu-id="ce59f-120">EF Core 只會在將內容實例新增至集區之前，重設其感知的狀態。</span><span class="sxs-lookup"><span data-stu-id="ce59f-120">EF Core only resets the state that it is aware of before adding a context instance to the pool.</span></span>

<span data-ttu-id="ce59f-121">內容共用的運作方式是跨要求重複使用相同的內容實例。</span><span class="sxs-lookup"><span data-stu-id="ce59f-121">Context pooling works by reusing the same context instance across requests.</span></span> <span data-ttu-id="ce59f-122">這表示它實際上是以實例本身的 [Singleton](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) 來註冊，以便能夠保存。</span><span class="sxs-lookup"><span data-stu-id="ce59f-122">This means that it's effectively registered as a [Singleton](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) in terms of the instance itself so that it's able to persist.</span></span>

<span data-ttu-id="ce59f-123">內容共用適用于內容設定（包括已解決的服務）在要求之間固定的案例。</span><span class="sxs-lookup"><span data-stu-id="ce59f-123">Context pooling is intended for scenarios where the context configuration, which includes services resolved, is fixed between requests.</span></span> <span data-ttu-id="ce59f-124">針對需要 [範圍](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) 服務的情況，或需要變更設定時，請勿使用共用。</span><span class="sxs-lookup"><span data-stu-id="ce59f-124">For cases where [Scoped](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) services are required, or configuration needs to be changed, don't use pooling.</span></span> <span data-ttu-id="ce59f-125">除了在高度優化的案例中，來自共用的效能提升通常是可忽略的。</span><span class="sxs-lookup"><span data-stu-id="ce59f-125">The performance gain from pooling is usually negligible except in highly optimized scenarios.</span></span>
