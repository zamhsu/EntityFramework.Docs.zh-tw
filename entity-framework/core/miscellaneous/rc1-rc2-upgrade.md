---
title: 從 EF Core 1.0 RC1 升級至 RC2-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 6d75b229-cc79-4d08-88cd-3a1c1b24d88f
uid: core/miscellaneous/rc1-rc2-upgrade
ms.openlocfilehash: 5500addc98a0c0eb314c4d9f9401fa301ce5c6f6
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370522"
---
# <a name="upgrading-from-ef-core-10-rc1-to-10-rc2"></a><span data-ttu-id="979e3-102">從 EF Core 1.0 RC1 升級至 1.0 RC2</span><span class="sxs-lookup"><span data-stu-id="979e3-102">Upgrading from EF Core 1.0 RC1 to 1.0 RC2</span></span>

<span data-ttu-id="979e3-103">本文提供將 RC1 套件建立的應用程式移至 RC2 的指引。</span><span class="sxs-lookup"><span data-stu-id="979e3-103">This article provides guidance for moving an application built with the RC1 packages to RC2.</span></span>

## <a name="package-names-and-versions"></a><span data-ttu-id="979e3-104">套件名稱和版本</span><span class="sxs-lookup"><span data-stu-id="979e3-104">Package Names and Versions</span></span>

<span data-ttu-id="979e3-105">在 RC1 與 RC2 之間，我們已將 "Entity Framework 7" 變更為 "Entity Framework Core"。</span><span class="sxs-lookup"><span data-stu-id="979e3-105">Between RC1 and RC2, we changed from "Entity Framework 7" to "Entity Framework Core".</span></span> <span data-ttu-id="979e3-106">您可以透過 Scott Hanselman，深入瞭解[這篇文章](https://www.hanselman.com/blog/ASPNET5IsDeadIntroducingASPNETCore10AndNETCore10.aspx)中變更的原因。</span><span class="sxs-lookup"><span data-stu-id="979e3-106">You can read more about the reasons for the change in [this post by Scott Hanselman](https://www.hanselman.com/blog/ASPNET5IsDeadIntroducingASPNETCore10AndNETCore10.aspx).</span></span> <span data-ttu-id="979e3-107">由於這項變更，我們的套件名稱會從變更 `EntityFramework.*` 為， `Microsoft.EntityFrameworkCore.*` 而我們的版本會從轉換 `7.0.0-rc1-final` 成 `1.0.0-rc2-final` （或 `1.0.0-preview1-final` 用於工具）。</span><span class="sxs-lookup"><span data-stu-id="979e3-107">Because of this change, our package names changed from `EntityFramework.*` to `Microsoft.EntityFrameworkCore.*` and our versions from `7.0.0-rc1-final` to `1.0.0-rc2-final` (or `1.0.0-preview1-final` for tooling).</span></span>

<span data-ttu-id="979e3-108">**您必須完全移除 RC1 的套件，然後安裝 RC2。**</span><span class="sxs-lookup"><span data-stu-id="979e3-108">**You will need to completely remove the RC1 packages and then install the RC2 ones.**</span></span> <span data-ttu-id="979e3-109">以下是一些一般封裝的對應。</span><span class="sxs-lookup"><span data-stu-id="979e3-109">Here is the mapping for some common packages.</span></span>

| <span data-ttu-id="979e3-110">RC1 套件</span><span class="sxs-lookup"><span data-stu-id="979e3-110">RC1 Package</span></span>                                               | <span data-ttu-id="979e3-111">RC2 對等</span><span class="sxs-lookup"><span data-stu-id="979e3-111">RC2 Equivalent</span></span>                                                       |
|:----------------------------------------------------------|:---------------------------------------------------------------------|
| <span data-ttu-id="979e3-112">EntityFramework. Microsoft sql server 7.0.0-rc1-最終</span><span class="sxs-lookup"><span data-stu-id="979e3-112">EntityFramework.MicrosoftSqlServer        7.0.0-rc1-final</span></span> | <span data-ttu-id="979e3-113">Microsoft.entityframeworkcore SqlServer 1.0.0-rc2-最終</span><span class="sxs-lookup"><span data-stu-id="979e3-113">Microsoft.EntityFrameworkCore.SqlServer         1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="979e3-114">EntityFramework. SQLite 7.0.0-rc1-最終</span><span class="sxs-lookup"><span data-stu-id="979e3-114">EntityFramework.SQLite                    7.0.0-rc1-final</span></span> | <span data-ttu-id="979e3-115">Microsoft.entityframeworkcore Sqlite 1.0.0-rc2-最終</span><span class="sxs-lookup"><span data-stu-id="979e3-115">Microsoft.EntityFrameworkCore.Sqlite            1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="979e3-116">EntityFramework7. Npgsql 3.1.0-rc1-3</span><span class="sxs-lookup"><span data-stu-id="979e3-116">EntityFramework7.Npgsql                   3.1.0-rc1-3</span></span>     | <span data-ttu-id="979e3-117">NpgSql. Microsoft.entityframeworkcore. Postgres<to be advised></span><span class="sxs-lookup"><span data-stu-id="979e3-117">NpgSql.EntityFrameworkCore.Postgres             <to be advised></span></span>      |
| <span data-ttu-id="979e3-118">EntityFramework. SqlServerCompact35 7.0.0-rc1-最終</span><span class="sxs-lookup"><span data-stu-id="979e3-118">EntityFramework.SqlServerCompact35        7.0.0-rc1-final</span></span> | <span data-ttu-id="979e3-119">Microsoft.entityframeworkcore. SqlServerCompact35 1.0.0-rc2-最終</span><span class="sxs-lookup"><span data-stu-id="979e3-119">EntityFrameworkCore.SqlServerCompact35          1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="979e3-120">EntityFramework. SqlServerCompact40 7.0.0-rc1-最終</span><span class="sxs-lookup"><span data-stu-id="979e3-120">EntityFramework.SqlServerCompact40        7.0.0-rc1-final</span></span> | <span data-ttu-id="979e3-121">Microsoft.entityframeworkcore. SqlServerCompact40 1.0.0-rc2-最終</span><span class="sxs-lookup"><span data-stu-id="979e3-121">EntityFrameworkCore.SqlServerCompact40          1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="979e3-122">EntityFramework. InMemory 7.0.0-rc1-最終</span><span class="sxs-lookup"><span data-stu-id="979e3-122">EntityFramework.InMemory                  7.0.0-rc1-final</span></span> | <span data-ttu-id="979e3-123">Microsoft.entityframeworkcore。 InMemory 1.0.0-rc2-最終</span><span class="sxs-lookup"><span data-stu-id="979e3-123">Microsoft.EntityFrameworkCore.InMemory          1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="979e3-124">EntityFramework. IBMDataServer 7.0.0-Beta1</span><span class="sxs-lookup"><span data-stu-id="979e3-124">EntityFramework.IBMDataServer             7.0.0-beta1</span></span>     | <span data-ttu-id="979e3-125">尚未提供 RC2</span><span class="sxs-lookup"><span data-stu-id="979e3-125">Not yet available for RC2</span></span>                                            |
| <span data-ttu-id="979e3-126">EntityFramework。命令 7.0.0-rc1-最終</span><span class="sxs-lookup"><span data-stu-id="979e3-126">EntityFramework.Commands                  7.0.0-rc1-final</span></span> | <span data-ttu-id="979e3-127">Microsoft.entityframeworkcore。工具 1.0.0-preview1-最終</span><span class="sxs-lookup"><span data-stu-id="979e3-127">Microsoft.EntityFrameworkCore.Tools             1.0.0-preview1-final</span></span> |
| <span data-ttu-id="979e3-128">EntityFramework. Microsoft sql server. Design 7.0.0-rc1-最終</span><span class="sxs-lookup"><span data-stu-id="979e3-128">EntityFramework.MicrosoftSqlServer.Design 7.0.0-rc1-final</span></span> | <span data-ttu-id="979e3-129">Microsoft.entityframeworkcore Design 1.0.0-rc2-最終</span><span class="sxs-lookup"><span data-stu-id="979e3-129">Microsoft.EntityFrameworkCore.SqlServer.Design  1.0.0-rc2-final</span></span>      |

## <a name="namespaces"></a><span data-ttu-id="979e3-130">命名空間</span><span class="sxs-lookup"><span data-stu-id="979e3-130">Namespaces</span></span>

<span data-ttu-id="979e3-131">除了封裝名稱之外，命名空間也會從變更 `Microsoft.Data.Entity.*` 為 `Microsoft.EntityFrameworkCore.*` 。</span><span class="sxs-lookup"><span data-stu-id="979e3-131">Along with package names, namespaces changed from `Microsoft.Data.Entity.*` to `Microsoft.EntityFrameworkCore.*`.</span></span> <span data-ttu-id="979e3-132">您可以使用的 [尋找/取代] 來處理這項變更 `using Microsoft.Data.Entity` `using Microsoft.EntityFrameworkCore` 。</span><span class="sxs-lookup"><span data-stu-id="979e3-132">You can handle this change with a find/replace of `using Microsoft.Data.Entity` with `using Microsoft.EntityFrameworkCore`.</span></span>

## <a name="table-naming-convention-changes"></a><span data-ttu-id="979e3-133">資料表命名慣例變更</span><span class="sxs-lookup"><span data-stu-id="979e3-133">Table Naming Convention Changes</span></span>

<span data-ttu-id="979e3-134">我們在 RC2 中所做的重大功能變更，就是使用 `DbSet<TEntity>` 指定實體的屬性名稱做為它所對應的資料表名稱，而不只是類別名稱。</span><span class="sxs-lookup"><span data-stu-id="979e3-134">A significant functional change we took in RC2 was to use the name of the `DbSet<TEntity>` property for a given entity as the table name it maps to, rather than just the class name.</span></span> <span data-ttu-id="979e3-135">如需這項變更的詳細資訊，請參閱[相關公告問題](https://github.com/aspnet/Announcements/issues/167)。</span><span class="sxs-lookup"><span data-stu-id="979e3-135">You can read more about this change in [the related announcement issue](https://github.com/aspnet/Announcements/issues/167).</span></span>

<span data-ttu-id="979e3-136">針對現有的 RC1 應用程式，我們建議您將下列程式碼新增至方法的開頭， `OnModelCreating` 以保留 RC1 命名策略：</span><span class="sxs-lookup"><span data-stu-id="979e3-136">For existing RC1 applications, we recommend adding the following code to the start of your `OnModelCreating` method to keep the RC1 naming strategy:</span></span>

``` csharp
foreach (var entity in modelBuilder.Model.GetEntityTypes())
{
    entity.Relational().TableName = entity.DisplayName();
}
```

<span data-ttu-id="979e3-137">如果您想要採用新的命名策略，建議您成功完成其餘的升級步驟，然後移除程式碼並建立遷移來套用資料表重新命名。</span><span class="sxs-lookup"><span data-stu-id="979e3-137">If you want to adopt the new naming strategy, we would recommend successfully completing the rest of the upgrade steps and then removing the code and creating a migration to apply the table renames.</span></span>

## <a name="adddbcontext--startupcs-changes-aspnet-core-projects-only"></a><span data-ttu-id="979e3-138">AddDbCoNtext/Startup.cs 變更（僅限 ASP.NET Core 專案）</span><span class="sxs-lookup"><span data-stu-id="979e3-138">AddDbContext / Startup.cs Changes (ASP.NET Core Projects Only)</span></span>

<span data-ttu-id="979e3-139">在 RC1 中，您必須將 Entity Framework 服務新增至應用程式服務提供者-in `Startup.ConfigureServices(...)` ：</span><span class="sxs-lookup"><span data-stu-id="979e3-139">In RC1, you had to add Entity Framework services to the application service provider - in `Startup.ConfigureServices(...)`:</span></span>

``` csharp
services.AddEntityFramework()
  .AddSqlServer()
  .AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

<span data-ttu-id="979e3-140">在 RC2 中，您可以移除對 `AddEntityFramework()` 、等的呼叫 `AddSqlServer()` ：</span><span class="sxs-lookup"><span data-stu-id="979e3-140">In RC2, you can remove the calls to `AddEntityFramework()`, `AddSqlServer()`, etc.:</span></span>

``` csharp
services.AddDbContext<ApplicationDbContext>(options =>
  options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

<span data-ttu-id="979e3-141">您也需要在衍生內容中加入一個可接受內容選項並將它們傳遞至基底函式的函式。</span><span class="sxs-lookup"><span data-stu-id="979e3-141">You also need to add a constructor, to your derived context, that takes context options and passes them to the base constructor.</span></span> <span data-ttu-id="979e3-142">這是必要的，因為我們移除了在幕後 snuck 它們的一些可怕神奇之處：</span><span class="sxs-lookup"><span data-stu-id="979e3-142">This is needed because we removed some of the scary magic that snuck them in behind the scenes:</span></span>

``` csharp
public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
    : base(options)
{
}
```

## <a name="passing-in-an-iserviceprovider"></a><span data-ttu-id="979e3-143">傳入 IServiceProvider</span><span class="sxs-lookup"><span data-stu-id="979e3-143">Passing in an IServiceProvider</span></span>

<span data-ttu-id="979e3-144">如果您有將傳遞至內容的 RC1 程式碼 `IServiceProvider` ，這現在已移至 `DbContextOptions` ，而不是個別的函式參數。</span><span class="sxs-lookup"><span data-stu-id="979e3-144">If you have RC1 code that passes an `IServiceProvider` to the context, this has now moved to `DbContextOptions`, rather than being a separate constructor parameter.</span></span> <span data-ttu-id="979e3-145">使用 `DbContextOptionsBuilder.UseInternalServiceProvider(...)` 來設定服務提供者。</span><span class="sxs-lookup"><span data-stu-id="979e3-145">Use `DbContextOptionsBuilder.UseInternalServiceProvider(...)` to set the service provider.</span></span>

### <a name="testing"></a><span data-ttu-id="979e3-146">測試</span><span class="sxs-lookup"><span data-stu-id="979e3-146">Testing</span></span>

<span data-ttu-id="979e3-147">執行此作業最常見的案例是在測試時控制 InMemory 資料庫的範圍。</span><span class="sxs-lookup"><span data-stu-id="979e3-147">The most common scenario for doing this was to control the scope of an InMemory database when testing.</span></span> <span data-ttu-id="979e3-148">如需以 RC2 執行此作業的範例，請參閱更新的[測試](testing/index.md)文章。</span><span class="sxs-lookup"><span data-stu-id="979e3-148">See the updated [Testing](testing/index.md) article for an example of doing this with RC2.</span></span>

### <a name="resolving-internal-services-from-application-service-provider-aspnet-core-projects-only"></a><span data-ttu-id="979e3-149">解析來自應用程式服務提供者的內部服務（僅限 ASP.NET Core 專案）</span><span class="sxs-lookup"><span data-stu-id="979e3-149">Resolving Internal Services from Application Service Provider (ASP.NET Core Projects Only)</span></span>

<span data-ttu-id="979e3-150">如果您有 ASP.NET Core 應用程式，而且想要 EF 解析來自應用程式服務提供者的內部服務，則會有多載，可 `AddDbContext` 讓您進行這項設定：</span><span class="sxs-lookup"><span data-stu-id="979e3-150">If you have an ASP.NET Core application and you want EF to resolve internal services from the application service provider, there is an overload of `AddDbContext` that allows you to configure this:</span></span>

``` csharp
services.AddEntityFrameworkSqlServer()
  .AddDbContext<ApplicationDbContext>((serviceProvider, options) =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"])
           .UseInternalServiceProvider(serviceProvider));
```

> [!WARNING]  
> <span data-ttu-id="979e3-151">除非您有理由將內部 EF 服務結合到您的應用程式服務提供者，否則建議允許 EF 在內部管理自己的服務。</span><span class="sxs-lookup"><span data-stu-id="979e3-151">We recommend allowing EF to internally manage its own services, unless you have a reason to combine the internal EF services into your application service provider.</span></span> <span data-ttu-id="979e3-152">您可能想要這樣做的主要原因是使用您的應用程式服務提供者來取代 EF 在內部使用的服務</span><span class="sxs-lookup"><span data-stu-id="979e3-152">The main reason you may want to do this is to use your application service provider to replace services that EF uses internally</span></span>

## <a name="dnx-commands--net-core-cli-aspnet-core-projects-only"></a><span data-ttu-id="979e3-153">DNX 命令 => .NET Core CLI （僅限 ASP.NET Core 專案）</span><span class="sxs-lookup"><span data-stu-id="979e3-153">DNX Commands => .NET Core CLI (ASP.NET Core Projects Only)</span></span>

<span data-ttu-id="979e3-154">如果您先前已使用 `dnx ef` ASP.NET 5 個專案的命令，這些命令現在已移至 `dotnet ef` 命令。</span><span class="sxs-lookup"><span data-stu-id="979e3-154">If you previously used the `dnx ef` commands for ASP.NET 5 projects, these have now moved to `dotnet ef` commands.</span></span> <span data-ttu-id="979e3-155">相同的命令語法仍然適用。</span><span class="sxs-lookup"><span data-stu-id="979e3-155">The same command syntax still applies.</span></span> <span data-ttu-id="979e3-156">您可以使用 `dotnet ef --help` 來取得語法資訊。</span><span class="sxs-lookup"><span data-stu-id="979e3-156">You can use `dotnet ef --help` for syntax information.</span></span>

<span data-ttu-id="979e3-157">在 RC2 中登錄命令的方式已變更，因為 DNX 會被 .NET Core CLI 取代。</span><span class="sxs-lookup"><span data-stu-id="979e3-157">The way commands are registered has changed in RC2, due to DNX being replaced by the .NET Core CLI.</span></span> <span data-ttu-id="979e3-158">命令現在已在的 `tools` 區段中註冊 `project.json` ：</span><span class="sxs-lookup"><span data-stu-id="979e3-158">Commands are now registered in a `tools` section in `project.json`:</span></span>

``` json
"tools": {
  "Microsoft.EntityFrameworkCore.Tools": {
    "version": "1.0.0-preview1-final",
    "imports": [
      "portable-net45+win8+dnxcore50",
      "portable-net45+win8"
    ]
  }
}
```

> [!TIP]  
> <span data-ttu-id="979e3-159">如果您使用 Visual Studio，您現在可以使用套件管理員主控台來執行 ASP.NET Core 專案的 EF 命令（RC1 不支援這項作業）。</span><span class="sxs-lookup"><span data-stu-id="979e3-159">If you use Visual Studio, you can now use Package Manager Console to run EF commands for ASP.NET Core projects (this was not supported in RC1).</span></span> <span data-ttu-id="979e3-160">您仍然需要在中註冊 `tools` 一節中的命令 `project.json` 來執行這項操作。</span><span class="sxs-lookup"><span data-stu-id="979e3-160">You still need to register the commands in the `tools` section of `project.json` to do this.</span></span>

## <a name="package-manager-commands-require-powershell-5"></a><span data-ttu-id="979e3-161">套件管理員命令需要 PowerShell 5</span><span class="sxs-lookup"><span data-stu-id="979e3-161">Package Manager Commands Require PowerShell 5</span></span>

<span data-ttu-id="979e3-162">如果您在 Visual Studio 中使用 [套件管理員主控台] 中的 [Entity Framework] 命令，則必須確定已安裝 PowerShell 5。</span><span class="sxs-lookup"><span data-stu-id="979e3-162">If you use the Entity Framework commands in Package Manager Console in Visual Studio, then you will need to ensure you have PowerShell 5 installed.</span></span> <span data-ttu-id="979e3-163">這是在下一版中將會移除的暫時需求（如需詳細資訊，請參閱[問題 #5327](https://github.com/aspnet/EntityFramework/issues/5327) ）。</span><span class="sxs-lookup"><span data-stu-id="979e3-163">This is a temporary requirement that will be removed in the next release (see [issue #5327](https://github.com/aspnet/EntityFramework/issues/5327) for more details).</span></span>

## <a name="using-imports-in-projectjson"></a><span data-ttu-id="979e3-164">在 project.js中使用「匯入」</span><span class="sxs-lookup"><span data-stu-id="979e3-164">Using "imports" in project.json</span></span>

<span data-ttu-id="979e3-165">部分 EF Core 的相依性尚不支援 .NET Standard。</span><span class="sxs-lookup"><span data-stu-id="979e3-165">Some of EF Core's dependencies do not support .NET Standard yet.</span></span> <span data-ttu-id="979e3-166">.NET Standard 和 .NET Core 專案中的 EF Core 可能需要在 project.js上新增「匯入」，作為暫時的因應措施。</span><span class="sxs-lookup"><span data-stu-id="979e3-166">EF Core in .NET Standard and .NET Core projects may require adding "imports" to project.json as a temporary workaround.</span></span>

<span data-ttu-id="979e3-167">新增 EF 時，NuGet 還原將會顯示此錯誤訊息：</span><span class="sxs-lookup"><span data-stu-id="979e3-167">When adding EF, NuGet restore will display this error message:</span></span>

``` Console
Package Ix-Async 1.2.5 is not compatible with netcoreapp1.0 (.NETCoreApp,Version=v1.0). Package Ix-Async 1.2.5 supports:
  - net40 (.NETFramework,Version=v4.0)
  - net45 (.NETFramework,Version=v4.5)
  - portable-net45+win8+wp8 (.NETPortable,Version=v0.0,Profile=Profile78)
Package Remotion.Linq 2.0.2 is not compatible with netcoreapp1.0 (.NETCoreApp,Version=v1.0). Package Remotion.Linq 2.0.2 supports:
  - net35 (.NETFramework,Version=v3.5)
  - net40 (.NETFramework,Version=v4.0)
  - net45 (.NETFramework,Version=v4.5)
  - portable-net45+win8+wp8+wpa81 (.NETPortable,Version=v0.0,Profile=Profile259)
```

<span data-ttu-id="979e3-168">因應措施是手動匯入可移植的設定檔 "net451 + win8"。</span><span class="sxs-lookup"><span data-stu-id="979e3-168">The workaround is to manually import the portable profile "portable-net451+win8".</span></span> <span data-ttu-id="979e3-169">這會強制 NuGet 將符合此提供的二進位檔視為與 .NET Standard 相容的架構，即使不是如此。</span><span class="sxs-lookup"><span data-stu-id="979e3-169">This forces NuGet to treat this binaries that match this provide as a compatible framework with .NET Standard, even though they are not.</span></span> <span data-ttu-id="979e3-170">雖然 "net451 + win8" 並不是100% 與 .NET Standard 相容，但它與 PCL 之間的轉換十分相容，可 .NET Standard。</span><span class="sxs-lookup"><span data-stu-id="979e3-170">Although "portable-net451+win8" is not 100% compatible with .NET Standard, it is compatible enough for the transition from PCL to .NET Standard.</span></span> <span data-ttu-id="979e3-171">當 EF 的相依性最終升級至 .NET Standard 時，可以移除匯入。</span><span class="sxs-lookup"><span data-stu-id="979e3-171">Imports can be removed when EF's dependencies eventually upgrade to .NET Standard.</span></span>

<span data-ttu-id="979e3-172">在陣列語法中，可以將多個架構新增至「匯入」。</span><span class="sxs-lookup"><span data-stu-id="979e3-172">Multiple frameworks can be added to "imports" in array syntax.</span></span> <span data-ttu-id="979e3-173">如果您將其他程式庫新增至專案，則可能需要其他匯入。</span><span class="sxs-lookup"><span data-stu-id="979e3-173">Other imports may be necessary if you add additional libraries to your project.</span></span>

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

<span data-ttu-id="979e3-174">請參閱[問題 #5176](https://github.com/aspnet/EntityFramework/issues/5176)。</span><span class="sxs-lookup"><span data-stu-id="979e3-174">See [Issue #5176](https://github.com/aspnet/EntityFramework/issues/5176).</span></span>
