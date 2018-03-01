---
title: "從 EF Core 1.0 RC1 升級至 RC2-EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 6d75b229-cc79-4d08-88cd-3a1c1b24d88f
ms.technology: entity-framework-core
uid: core/miscellaneous/rc1-rc2-upgrade
ms.openlocfilehash: e76886729248101ccac024568cf9abcd945fca33
ms.sourcegitcommit: b2d94cebdc32edad4fecb07e53fece66437d1b04
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="upgrading-from-ef-core-10-rc1-to-10-rc2"></a><span data-ttu-id="4cbed-102">從 EF Core 1.0 RC1 升級至 1.0 RC2</span><span class="sxs-lookup"><span data-stu-id="4cbed-102">Upgrading from EF Core 1.0 RC1 to 1.0 RC2</span></span>

<span data-ttu-id="4cbed-103">本文章提供移動至 RC2 RC1 封裝建置的應用程式的指引。</span><span class="sxs-lookup"><span data-stu-id="4cbed-103">This article provides guidance for moving an application built with the RC1 packages to RC2.</span></span>

## <a name="package-names-and-versions"></a><span data-ttu-id="4cbed-104">封裝名稱和版本</span><span class="sxs-lookup"><span data-stu-id="4cbed-104">Package Names and Versions</span></span>

<span data-ttu-id="4cbed-105">之間 RC1 和 RC2，我們變更為 「 Entity Framework Core 」 從 「 Entity Framework 7 」。</span><span class="sxs-lookup"><span data-stu-id="4cbed-105">Between RC1 and RC2, we changed from "Entity Framework 7" to "Entity Framework Core".</span></span> <span data-ttu-id="4cbed-106">閱讀更多有關的變更原因[由 Scott Hanselman 這篇文章](http://www.hanselman.com/blog/ASPNET5IsDeadIntroducingASPNETCore10AndNETCore10.aspx)。</span><span class="sxs-lookup"><span data-stu-id="4cbed-106">You can read more about the reasons for the change in [this post by Scott Hanselman](http://www.hanselman.com/blog/ASPNET5IsDeadIntroducingASPNETCore10AndNETCore10.aspx).</span></span> <span data-ttu-id="4cbed-107">這項變更，因為我們套件名稱會變更從`EntityFramework.*`至`Microsoft.EntityFrameworkCore.*`和我們版本`7.0.0-rc1-final`至`1.0.0-rc2-final`(或`1.0.0-preview1-final`工具)。</span><span class="sxs-lookup"><span data-stu-id="4cbed-107">Because of this change, our package names changed from `EntityFramework.*` to `Microsoft.EntityFrameworkCore.*` and our versions from `7.0.0-rc1-final` to `1.0.0-rc2-final` (or `1.0.0-preview1-final` for tooling).</span></span>

<span data-ttu-id="4cbed-108">**您要完全移除 RC1 封裝，然後安裝 RC2 的。**</span><span class="sxs-lookup"><span data-stu-id="4cbed-108">**You will need to completely remove the RC1 packages and then install the RC2 ones.**</span></span> <span data-ttu-id="4cbed-109">以下是一些常見的封裝的對應。</span><span class="sxs-lookup"><span data-stu-id="4cbed-109">Here is the mapping for some common packages.</span></span>

| <span data-ttu-id="4cbed-110">RC1 封裝</span><span class="sxs-lookup"><span data-stu-id="4cbed-110">RC1 Package</span></span>                                               | <span data-ttu-id="4cbed-111">RC2 的對等項目</span><span class="sxs-lookup"><span data-stu-id="4cbed-111">RC2 Equivalent</span></span>                                                       |
|:----------------------------------------------------------|:---------------------------------------------------------------------|
| <span data-ttu-id="4cbed-112">EntityFramework.MicrosoftSqlServer        7.0.0-rc1-final</span><span class="sxs-lookup"><span data-stu-id="4cbed-112">EntityFramework.MicrosoftSqlServer        7.0.0-rc1-final</span></span> | <span data-ttu-id="4cbed-113">Microsoft.EntityFrameworkCore.SqlServer         1.0.0-rc2-final</span><span class="sxs-lookup"><span data-stu-id="4cbed-113">Microsoft.EntityFrameworkCore.SqlServer         1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="4cbed-114">EntityFramework.SQLite                    7.0.0-rc1-final</span><span class="sxs-lookup"><span data-stu-id="4cbed-114">EntityFramework.SQLite                    7.0.0-rc1-final</span></span> | <span data-ttu-id="4cbed-115">Microsoft.EntityFrameworkCore.Sqlite            1.0.0-rc2-final</span><span class="sxs-lookup"><span data-stu-id="4cbed-115">Microsoft.EntityFrameworkCore.Sqlite            1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="4cbed-116">EntityFramework7.Npgsql                   3.1.0-rc1-3</span><span class="sxs-lookup"><span data-stu-id="4cbed-116">EntityFramework7.Npgsql                   3.1.0-rc1-3</span></span>     | <span data-ttu-id="4cbed-117">NpgSql.EntityFrameworkCore.Postgres             <to be advised></span><span class="sxs-lookup"><span data-stu-id="4cbed-117">NpgSql.EntityFrameworkCore.Postgres             <to be advised></span></span>      |
| <span data-ttu-id="4cbed-118">EntityFramework.SqlServerCompact35        7.0.0-rc1-final</span><span class="sxs-lookup"><span data-stu-id="4cbed-118">EntityFramework.SqlServerCompact35        7.0.0-rc1-final</span></span> | <span data-ttu-id="4cbed-119">EntityFrameworkCore.SqlServerCompact35          1.0.0-rc2-final</span><span class="sxs-lookup"><span data-stu-id="4cbed-119">EntityFrameworkCore.SqlServerCompact35          1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="4cbed-120">EntityFramework.SqlServerCompact40        7.0.0-rc1-final</span><span class="sxs-lookup"><span data-stu-id="4cbed-120">EntityFramework.SqlServerCompact40        7.0.0-rc1-final</span></span> | <span data-ttu-id="4cbed-121">EntityFrameworkCore.SqlServerCompact40          1.0.0-rc2-final</span><span class="sxs-lookup"><span data-stu-id="4cbed-121">EntityFrameworkCore.SqlServerCompact40          1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="4cbed-122">EntityFramework.InMemory                  7.0.0-rc1-final</span><span class="sxs-lookup"><span data-stu-id="4cbed-122">EntityFramework.InMemory                  7.0.0-rc1-final</span></span> | <span data-ttu-id="4cbed-123">Microsoft.EntityFrameworkCore.InMemory          1.0.0-rc2-final</span><span class="sxs-lookup"><span data-stu-id="4cbed-123">Microsoft.EntityFrameworkCore.InMemory          1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="4cbed-124">EntityFramework.IBMDataServer             7.0.0-beta1</span><span class="sxs-lookup"><span data-stu-id="4cbed-124">EntityFramework.IBMDataServer             7.0.0-beta1</span></span>     | <span data-ttu-id="4cbed-125">尚無法使用 rc2</span><span class="sxs-lookup"><span data-stu-id="4cbed-125">Not yet available for RC2</span></span>                                            |
| <span data-ttu-id="4cbed-126">EntityFramework.Commands                  7.0.0-rc1-final</span><span class="sxs-lookup"><span data-stu-id="4cbed-126">EntityFramework.Commands                  7.0.0-rc1-final</span></span> | <span data-ttu-id="4cbed-127">Microsoft.EntityFrameworkCore.Tools             1.0.0-preview1-final</span><span class="sxs-lookup"><span data-stu-id="4cbed-127">Microsoft.EntityFrameworkCore.Tools             1.0.0-preview1-final</span></span> |
| <span data-ttu-id="4cbed-128">EntityFramework.MicrosoftSqlServer.Design 7.0.0-rc1-final</span><span class="sxs-lookup"><span data-stu-id="4cbed-128">EntityFramework.MicrosoftSqlServer.Design 7.0.0-rc1-final</span></span> | <span data-ttu-id="4cbed-129">Microsoft.EntityFrameworkCore.SqlServer.Design  1.0.0-rc2-final</span><span class="sxs-lookup"><span data-stu-id="4cbed-129">Microsoft.EntityFrameworkCore.SqlServer.Design  1.0.0-rc2-final</span></span>      |

## <a name="namespaces"></a><span data-ttu-id="4cbed-130">命名空間</span><span class="sxs-lookup"><span data-stu-id="4cbed-130">Namespaces</span></span>

<span data-ttu-id="4cbed-131">套件名稱以及命名空間已從`Microsoft.Data.Entity.*`至`Microsoft.EntityFrameworkCore.*`。</span><span class="sxs-lookup"><span data-stu-id="4cbed-131">Along with package names, namespaces changed from `Microsoft.Data.Entity.*` to `Microsoft.EntityFrameworkCore.*`.</span></span> <span data-ttu-id="4cbed-132">您可以處理這項變更，以尋找/取代的`using Microsoft.Data.Entity`與`using Microsoft.EntityFrameworkCore`。</span><span class="sxs-lookup"><span data-stu-id="4cbed-132">You can handle this change with a find/replace of `using Microsoft.Data.Entity` with `using Microsoft.EntityFrameworkCore`.</span></span>

## <a name="table-naming-convention-changes"></a><span data-ttu-id="4cbed-133">命名慣例變更的資料表</span><span class="sxs-lookup"><span data-stu-id="4cbed-133">Table Naming Convention Changes</span></span>

<span data-ttu-id="4cbed-134">若要使用的名稱是重大的功能性變更，我們採用 rc2`DbSet<TEntity>`對指定的實體做為資料表名稱的屬性則會對應至，而不是類別名稱。</span><span class="sxs-lookup"><span data-stu-id="4cbed-134">A significant functional change we took in RC2 was to use the name of the `DbSet<TEntity>` property for a given entity as the table name it maps to, rather than just the class name.</span></span> <span data-ttu-id="4cbed-135">閱讀更多有關這項變更中[相關的公告問題](https://github.com/aspnet/Announcements/issues/167)。</span><span class="sxs-lookup"><span data-stu-id="4cbed-135">You can read more about this change in [the related announcement issue](https://github.com/aspnet/Announcements/issues/167).</span></span>

<span data-ttu-id="4cbed-136">對於現有 RC1 應用程式，我們建議將下列程式碼加入至開頭您`OnModelCreating`方法，以保留 RC1 命名策略：</span><span class="sxs-lookup"><span data-stu-id="4cbed-136">For existing RC1 applications, we recommend adding the following code to the start of your `OnModelCreating` method to keep the RC1 naming strategy:</span></span>

``` csharp
foreach (var entity in modelBuilder.Model.GetEntityTypes())
{
    entity.Relational().TableName = entity.DisplayName();
}
```

<span data-ttu-id="4cbed-137">如果您想要採用新的命名策略，我們會建議順利完成其餘的升級步驟以及移除的程式碼和移轉至套用資料表重新命名。</span><span class="sxs-lookup"><span data-stu-id="4cbed-137">If you want to adopt the new naming strategy, we would recommend successfully completing the rest of the upgrade steps and then removing the code and creating a migration to apply the table renames.</span></span>

## <a name="adddbcontext--startupcs-changes-aspnet-core-projects-only"></a><span data-ttu-id="4cbed-138">AddDbContext / Startup.cs 變更 （ASP.NET Core 專案）</span><span class="sxs-lookup"><span data-stu-id="4cbed-138">AddDbContext / Startup.cs Changes (ASP.NET Core Projects Only)</span></span>

<span data-ttu-id="4cbed-139">在 RC1，您必須在加入應用程式服務提供者的 Entity Framework 服務`Startup.ConfigureServices(...)`:</span><span class="sxs-lookup"><span data-stu-id="4cbed-139">In RC1, you had to add Entity Framework services to the application service provider - in `Startup.ConfigureServices(...)`:</span></span>

``` csharp
services.AddEntityFramework()
  .AddSqlServer()
  .AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

<span data-ttu-id="4cbed-140">在 RC2 中，您可以移除呼叫`AddEntityFramework()`，`AddSqlServer()`等等。:</span><span class="sxs-lookup"><span data-stu-id="4cbed-140">In RC2, you can remove the calls to `AddEntityFramework()`, `AddSqlServer()`, etc.:</span></span>

``` csharp
services.AddDbContext<ApplicationDbContext>(options =>
  options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

<span data-ttu-id="4cbed-141">您也需要加入建構函式，為您衍生內容，取得內容的選項，並將其傳遞給基底建構函式。</span><span class="sxs-lookup"><span data-stu-id="4cbed-141">You also need to add a constructor, to your derived context, that takes context options and passes them to the base constructor.</span></span> <span data-ttu-id="4cbed-142">這被必要由於我們移除了偷偷在幕後嚇人最特別的部分：</span><span class="sxs-lookup"><span data-stu-id="4cbed-142">This is needed because we removed some of the scary magic that snuck them in behind the scenes:</span></span>

``` csharp
public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
    : base(options)
{
}
```

## <a name="passing-in-an-iserviceprovider"></a><span data-ttu-id="4cbed-143">IServiceProvider 中傳遞</span><span class="sxs-lookup"><span data-stu-id="4cbed-143">Passing in an IServiceProvider</span></span>

<span data-ttu-id="4cbed-144">如果您有 RC1 通過認證的程式碼`IServiceProvider`至內容，這現在已移到`DbContextOptions`，而不會出現不同的建構函式參數。</span><span class="sxs-lookup"><span data-stu-id="4cbed-144">If you have RC1 code that passes an `IServiceProvider` to the context, this has now moved to `DbContextOptions`, rather than being a separate constructor parameter.</span></span> <span data-ttu-id="4cbed-145">使用`DbContextOptionsBuilder.UseInternalServiceProvider(...)`設定服務提供者。</span><span class="sxs-lookup"><span data-stu-id="4cbed-145">Use `DbContextOptionsBuilder.UseInternalServiceProvider(...)` to set the service provider.</span></span>

### <a name="testing"></a><span data-ttu-id="4cbed-146">測試</span><span class="sxs-lookup"><span data-stu-id="4cbed-146">Testing</span></span>

<span data-ttu-id="4cbed-147">執行此作業最常見的案例是測試時，控制 InMemory 資料庫的範圍。</span><span class="sxs-lookup"><span data-stu-id="4cbed-147">The most common scenario for doing this was to control the scope of an InMemory database when testing.</span></span> <span data-ttu-id="4cbed-148">請參閱更新[測試](testing/index.md)rc2 這麼做的範例為發行項。</span><span class="sxs-lookup"><span data-stu-id="4cbed-148">See the updated [Testing](testing/index.md) article for an example of doing this with RC2.</span></span>

### <a name="resolving-internal-services-from-application-service-provider-aspnet-core-projects-only"></a><span data-ttu-id="4cbed-149">解析內部的服務，從應用程式服務提供者 （ASP.NET Core 專案）</span><span class="sxs-lookup"><span data-stu-id="4cbed-149">Resolving Internal Services from Application Service Provider (ASP.NET Core Projects Only)</span></span>

<span data-ttu-id="4cbed-150">如果您有 ASP.NET Core 應用程式，您想要用來解析內部服務應用程式服務提供者的 EF 沒有多載`AddDbContext`，可讓您進行此設定：</span><span class="sxs-lookup"><span data-stu-id="4cbed-150">If you have an ASP.NET Core application and you want EF to resolve internal services from the application service provider, there is an overload of `AddDbContext` that allows you to configure this:</span></span>

``` csharp
services.AddEntityFrameworkSqlServer()
  .AddDbContext<ApplicationDbContext>((serviceProvider, options) =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"])
           .UseInternalServiceProvider(serviceProvider)); );
```

> [!WARNING]  
> <span data-ttu-id="4cbed-151">我們建議您允許 EF，在內部管理自己的服務，除非您有理由要將內部的 EF 服務結合成您的應用程式服務提供者。</span><span class="sxs-lookup"><span data-stu-id="4cbed-151">We recommend allowing EF to internally manage its own services, unless you have a reason to combine the internal EF services into your application service provider.</span></span> <span data-ttu-id="4cbed-152">您可能想要執行這項操作的主要原因是要用於您的應用程式服務提供者取代 EF 會在內部使用的服務</span><span class="sxs-lookup"><span data-stu-id="4cbed-152">The main reason you may want to do this is to use your application service provider to replace services that EF uses internally</span></span>

## <a name="dnx-commands--net-cli-aspnet-core-projects-only"></a><span data-ttu-id="4cbed-153">DNX 命令 = >.NET CLI （ASP.NET Core 專案）</span><span class="sxs-lookup"><span data-stu-id="4cbed-153">DNX Commands => .NET CLI (ASP.NET Core Projects Only)</span></span>

<span data-ttu-id="4cbed-154">如果您先前使用`dnx ef`命令 ASP.NET 5 專案，這些現在已轉換成`dotnet ef`命令。</span><span class="sxs-lookup"><span data-stu-id="4cbed-154">If you previously used the `dnx ef` commands for ASP.NET 5 projects, these have now moved to `dotnet ef` commands.</span></span> <span data-ttu-id="4cbed-155">仍適用於相同的命令語法。</span><span class="sxs-lookup"><span data-stu-id="4cbed-155">The same command syntax still applies.</span></span> <span data-ttu-id="4cbed-156">您可以使用`dotnet ef --help`如語法的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="4cbed-156">You can use `dotnet ef --help` for syntax information.</span></span>

<span data-ttu-id="4cbed-157">註冊命令的方式已變更 rc2，因為.NET CLI 要取代的 DNX。</span><span class="sxs-lookup"><span data-stu-id="4cbed-157">The way commands are registered has changed in RC2, due to DNX being replaced by .NET CLI.</span></span> <span data-ttu-id="4cbed-158">現在在註冊命令`tools`一節中`project.json`:</span><span class="sxs-lookup"><span data-stu-id="4cbed-158">Commands are now registered in a `tools` section in `project.json`:</span></span>

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
> <span data-ttu-id="4cbed-159">如果您使用 Visual Studio，您現在可以使用 Package Manager Console 來執行 ASP.NET Core 專案 （這不支援在 RC1 中） 的 EF 命令。</span><span class="sxs-lookup"><span data-stu-id="4cbed-159">If you use Visual Studio, you can now use Package Manager Console to run EF commands for ASP.NET Core projects (this was not supported in RC1).</span></span> <span data-ttu-id="4cbed-160">您仍然需要登錄中的命令`tools`區段`project.json`若要這樣做。</span><span class="sxs-lookup"><span data-stu-id="4cbed-160">You still need to register the commands in the `tools` section of `project.json` to do this.</span></span>

## <a name="package-manager-commands-require-powershell-5"></a><span data-ttu-id="4cbed-161">封裝管理員命令需要 PowerShell 5</span><span class="sxs-lookup"><span data-stu-id="4cbed-161">Package Manager Commands Require PowerShell 5</span></span>

<span data-ttu-id="4cbed-162">如果您在 Visual Studio 中的封裝管理員主控台中使用 Entity Framework 命令，則您必須確定您已安裝的 PowerShell 5。</span><span class="sxs-lookup"><span data-stu-id="4cbed-162">If you use the Entity Framework commands in Package Manager Console in Visual Studio, then you will need to ensure you have PowerShell 5 installed.</span></span> <span data-ttu-id="4cbed-163">這是中的下一個版本將移除的暫時需求 (請參閱[發出 #5327](https://github.com/aspnet/EntityFramework/issues/5327)如需詳細資訊)。</span><span class="sxs-lookup"><span data-stu-id="4cbed-163">This is a temporary requirement that will be removed in the next release (see [issue #5327](https://github.com/aspnet/EntityFramework/issues/5327) for more details).</span></span>

## <a name="using-imports-in-projectjson"></a><span data-ttu-id="4cbed-164">在 project.json 中使用 「 匯入 」</span><span class="sxs-lookup"><span data-stu-id="4cbed-164">Using "imports" in project.json</span></span>

<span data-ttu-id="4cbed-165">其中一些 EF 核心相依性不支援.NET 標準尚未。</span><span class="sxs-lookup"><span data-stu-id="4cbed-165">Some of EF Core's dependencies do not support .NET Standard yet.</span></span> <span data-ttu-id="4cbed-166">EF 核心標準.NET 和.NET Core 專案中的，您可能需要加入 「 匯入 「 加入 project.json 暫時的解決方法。</span><span class="sxs-lookup"><span data-stu-id="4cbed-166">EF Core in .NET Standard and .NET Core projects may require adding "imports" to project.json as a temporary workaround.</span></span>

<span data-ttu-id="4cbed-167">加入時 EF，NuGet 還原 」 會顯示這則錯誤訊息：</span><span class="sxs-lookup"><span data-stu-id="4cbed-167">When adding EF, NuGet restore will display this error message:</span></span>

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

<span data-ttu-id="4cbed-168">因應措施是手動匯入 「 可攜式 net451 + win8"的可攜式設定檔。</span><span class="sxs-lookup"><span data-stu-id="4cbed-168">The workaround is to manually import the portable profile "portable-net451+win8".</span></span> <span data-ttu-id="4cbed-169">此強制將與此二進位檔的 NuGet 提供為以.NET 標準相容的 framework，即使它們不是。</span><span class="sxs-lookup"><span data-stu-id="4cbed-169">This forces NuGet to treat this binaries that match this provide as a compatible framework with .NET Standard, even though they are not.</span></span> <span data-ttu-id="4cbed-170">雖然"可攜式 net451 + win8"不是與標準.NET 相容的 100%，它是相容的 PCL 從轉換為.NET 標準。</span><span class="sxs-lookup"><span data-stu-id="4cbed-170">Although "portable-net451+win8" is not 100% compatible with .NET Standard, it is compatible enough for the transition from PCL to .NET Standard.</span></span> <span data-ttu-id="4cbed-171">當以.NET 標準最後升級 EF 的相依性，則可以移除匯入。</span><span class="sxs-lookup"><span data-stu-id="4cbed-171">Imports can be removed when EF's dependencies eventually upgrade to .NET Standard.</span></span>

<span data-ttu-id="4cbed-172">在陣列語法時，可以加入多個架構 」 匯入 」。</span><span class="sxs-lookup"><span data-stu-id="4cbed-172">Multiple frameworks can be added to "imports" in array syntax.</span></span> <span data-ttu-id="4cbed-173">其他匯入可能需要額外的程式庫加入您的專案。</span><span class="sxs-lookup"><span data-stu-id="4cbed-173">Other imports may be necessary if you add additional libraries to your project.</span></span>

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

<span data-ttu-id="4cbed-174">請參閱[發出 #5176](https://github.com/aspnet/EntityFramework/issues/5176)。</span><span class="sxs-lookup"><span data-stu-id="4cbed-174">See [Issue #5176](https://github.com/aspnet/EntityFramework/issues/5176).</span></span>
