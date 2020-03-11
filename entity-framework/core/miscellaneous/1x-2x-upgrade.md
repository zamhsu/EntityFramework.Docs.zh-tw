---
title: 從舊版升級至 EF Core 2 EF Core
author: divega
ms.date: 08/13/2017
ms.assetid: 8BD43C8C-63D9-4F3A-B954-7BC518A1B7DB
uid: core/miscellaneous/1x-2x-upgrade
ms.openlocfilehash: b27c09fdb6210dd7c6aa0c8bc912a8bd183c16b9
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416753"
---
# <a name="upgrading-applications-from-previous-versions-to-ef-core-20"></a><span data-ttu-id="12137-102">將繼承應用程式升級至 EF Core 2。0</span><span class="sxs-lookup"><span data-stu-id="12137-102">Upgrading applications from previous versions to EF Core 2.0</span></span>

<span data-ttu-id="12137-103">我們已在2.0 中大幅縮小現有 Api 和行為的機會。</span><span class="sxs-lookup"><span data-stu-id="12137-103">We have taken the opportunity to significantly refine our existing APIs and behaviors in 2.0.</span></span> <span data-ttu-id="12137-104">有一些改良功能可能需要修改現有的應用程式程式碼，但我們相信大部分的應用程式都會造成影響，而在大部分情況下，只需要重新編譯和最小的引導式變更來取代已淘汰的 Api。</span><span class="sxs-lookup"><span data-stu-id="12137-104">There are a few improvements that can require modifying existing application code, although we believe that for the majority of applications the impact will be low, in most cases requiring just recompilation and minimal guided changes to replace obsolete APIs.</span></span>

<span data-ttu-id="12137-105">將現有的應用程式更新為 EF Core 2.0 可能需要：</span><span class="sxs-lookup"><span data-stu-id="12137-105">Updating an existing application to EF Core 2.0 may require:</span></span>

1. <span data-ttu-id="12137-106">將應用程式的目標 .NET 執行升級為支援 .NET Standard 2.0 的其中一個。</span><span class="sxs-lookup"><span data-stu-id="12137-106">Upgrading the target .NET implementation of the application to one that supports .NET Standard 2.0.</span></span> <span data-ttu-id="12137-107">如需詳細資訊，請參閱[支援的 .Net 部署](xref:core/platforms/index)。</span><span class="sxs-lookup"><span data-stu-id="12137-107">See [Supported .NET Implementations](xref:core/platforms/index) for more details.</span></span>

2. <span data-ttu-id="12137-108">識別與 EF Core 2.0 相容的目標資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="12137-108">Identify a provider for the target database which is compatible with EF Core 2.0.</span></span> <span data-ttu-id="12137-109">請參閱下面[的 EF Core 2.0 需要2.0 資料庫提供者](#ef-core-20-requires-a-20-database-provider)。</span><span class="sxs-lookup"><span data-stu-id="12137-109">See [EF Core 2.0 requires a 2.0 database provider](#ef-core-20-requires-a-20-database-provider) below.</span></span>

3. <span data-ttu-id="12137-110">將所有 EF Core 封裝（執行時間和工具）升級至2.0。</span><span class="sxs-lookup"><span data-stu-id="12137-110">Upgrading all the EF Core packages (runtime and tooling) to 2.0.</span></span> <span data-ttu-id="12137-111">如需詳細資訊，請參閱[安裝 EF Core](xref:core/get-started/install/index) 。</span><span class="sxs-lookup"><span data-stu-id="12137-111">Refer to [Installing EF Core](xref:core/get-started/install/index) for more details.</span></span>

4. <span data-ttu-id="12137-112">進行任何必要的程式碼變更，以補償本檔其餘部分所述的重大變更。</span><span class="sxs-lookup"><span data-stu-id="12137-112">Make any necessary code changes to compensate for the breaking changes described in the rest of this document.</span></span>

## <a name="aspnet-core-now-includes-ef-core"></a><span data-ttu-id="12137-113">ASP.NET Core 現在包含 EF Core</span><span class="sxs-lookup"><span data-stu-id="12137-113">ASP.NET Core now includes EF Core</span></span>

<span data-ttu-id="12137-114">除了協力廠商資料庫提供者之外，將目標設為 ASP.NET Core 2.0 的應用程式還可以使用 EF Core 2.0，而且沒有其他相依性。</span><span class="sxs-lookup"><span data-stu-id="12137-114">Applications targeting ASP.NET Core 2.0 can use EF Core 2.0 without additional dependencies besides third party database providers.</span></span> <span data-ttu-id="12137-115">不過，以舊版 ASP.NET Core 為目標的應用程式需要升級至 ASP.NET Core 2.0，才能使用 EF Core 2.0。</span><span class="sxs-lookup"><span data-stu-id="12137-115">However, applications targeting previous versions of ASP.NET Core need to upgrade to ASP.NET Core 2.0 in order to use EF Core 2.0.</span></span> <span data-ttu-id="12137-116">如需將 ASP.NET Core 應用程式升級為2.0 的詳細資訊，請參閱[主題的 ASP.NET Core 檔](/aspnet/core/migration/1x-to-2x/)。</span><span class="sxs-lookup"><span data-stu-id="12137-116">For more details on upgrading ASP.NET Core applications to 2.0 see [the ASP.NET Core documentation on the subject](/aspnet/core/migration/1x-to-2x/).</span></span>

## <a name="new-way-of-getting-application-services-in-aspnet-core"></a><span data-ttu-id="12137-117">在 ASP.NET Core 中取得應用程式服務的新方式</span><span class="sxs-lookup"><span data-stu-id="12137-117">New way of getting application services in ASP.NET Core</span></span>

<span data-ttu-id="12137-118">ASP.NET Core web 應用程式的建議模式已針對2.0 進行更新，這種方式會中斷在1.x 中使用的設計階段邏輯 EF Core。</span><span class="sxs-lookup"><span data-stu-id="12137-118">The recommended pattern for ASP.NET Core web applications has been updated for 2.0 in a way that broke the design-time logic EF Core used in 1.x.</span></span> <span data-ttu-id="12137-119">在設計階段，EF Core 會嘗試直接叫用 `Startup.ConfigureServices`，才能存取應用程式的服務提供者。</span><span class="sxs-lookup"><span data-stu-id="12137-119">Previously at design-time, EF Core would try to invoke `Startup.ConfigureServices` directly in order to access the application's service provider.</span></span> <span data-ttu-id="12137-120">在 ASP.NET Core 2.0 中，設定會在 `Startup` 類別外初始化。</span><span class="sxs-lookup"><span data-stu-id="12137-120">In ASP.NET Core 2.0, Configuration is initialized outside of the `Startup` class.</span></span> <span data-ttu-id="12137-121">使用 EF Core 的應用程式通常會從設定存取其連接字串，因此 `Startup` 本身已不再足夠。</span><span class="sxs-lookup"><span data-stu-id="12137-121">Applications using EF Core typically access their connection string from Configuration, so `Startup` by itself is no longer sufficient.</span></span> <span data-ttu-id="12137-122">如果您升級 ASP.NET Core 1.x 應用程式，當您使用 EF Core 工具時，可能會收到下列錯誤。</span><span class="sxs-lookup"><span data-stu-id="12137-122">If you upgrade an ASP.NET Core 1.x application, you may receive the following error when using the EF Core tools.</span></span>

> <span data-ttu-id="12137-123">' ApplicationCoNtext ' 上找不到無參數的函數。</span><span class="sxs-lookup"><span data-stu-id="12137-123">No parameterless constructor was found on 'ApplicationContext'.</span></span> <span data-ttu-id="12137-124">請將無參數的函式新增至 ' ApplicationCoNtext '，或在與 ' ApplicationCoNtext ' 相同的元件中加入 ' IDesignTimeDbCoNtextFactory&lt;ApplicationCoNtext&gt;' 的執行</span><span class="sxs-lookup"><span data-stu-id="12137-124">Either add a parameterless constructor to 'ApplicationContext' or add an implementation of 'IDesignTimeDbContextFactory&lt;ApplicationContext&gt;' in the same assembly as 'ApplicationContext'</span></span>

<span data-ttu-id="12137-125">ASP.NET Core 2.0 的預設範本中已加入新的設計階段勾點。</span><span class="sxs-lookup"><span data-stu-id="12137-125">A new design-time hook has been added in ASP.NET Core 2.0's default template.</span></span> <span data-ttu-id="12137-126">靜態 `Program.BuildWebHost` 方法可讓 EF Core 在設計階段存取應用程式的服務提供者。</span><span class="sxs-lookup"><span data-stu-id="12137-126">The static `Program.BuildWebHost` method enables EF Core to access the application's service provider at design time.</span></span> <span data-ttu-id="12137-127">如果您要升級 ASP.NET Core 1.x 應用程式，您將需要更新 `Program` 類別，如下所示。</span><span class="sxs-lookup"><span data-stu-id="12137-127">If you are upgrading an ASP.NET Core 1.x application, you will need to update the `Program` class to resemble the following.</span></span>

``` csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;

namespace AspNetCoreDotNetCore2._0App
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
}
```

<span data-ttu-id="12137-128">強烈建議您在將應用程式更新為2.0 時採用這個新模式，這是為了讓 Entity Framework Core 遷移的產品功能正常執行所需。</span><span class="sxs-lookup"><span data-stu-id="12137-128">The adoption of this new pattern when updating applications to 2.0 is highly recommended and is required in order for product features like Entity Framework Core Migrations to work.</span></span> <span data-ttu-id="12137-129">另一個常見的替代方法是[執行*IDesignTimeDbCoNtextFactory\<TCoNtext >* ](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory)。</span><span class="sxs-lookup"><span data-stu-id="12137-129">The other common alternative is to [implement *IDesignTimeDbContextFactory\<TContext>*](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory).</span></span>

## <a name="idbcontextfactory-renamed"></a><span data-ttu-id="12137-130">IDbCoNtextFactory 已重新命名</span><span class="sxs-lookup"><span data-stu-id="12137-130">IDbContextFactory renamed</span></span>

<span data-ttu-id="12137-131">為了支援不同的應用程式模式，並讓使用者更能控制其在設計階段的 `DbContext` 使用方式，我們在過去已提供 `IDbContextFactory<TContext>` 介面。</span><span class="sxs-lookup"><span data-stu-id="12137-131">In order to support diverse application patterns and give users more control over how their `DbContext` is used at design time, we have, in the past, provided the `IDbContextFactory<TContext>` interface.</span></span> <span data-ttu-id="12137-132">在設計階段，EF Core 工具會在您的專案中探索此介面的執行，並使用它來建立 `DbContext` 物件。</span><span class="sxs-lookup"><span data-stu-id="12137-132">At design-time, the EF Core tools will discover implementations of this interface in your project and use it to create `DbContext` objects.</span></span>

<span data-ttu-id="12137-133">此介面具有非常一般的名稱，會誤導某些使用者嘗試重新使用它來進行其他 `DbContext`建立案例。</span><span class="sxs-lookup"><span data-stu-id="12137-133">This interface had a very general name which mislead some users to try re-using it for other `DbContext`-creating scenarios.</span></span> <span data-ttu-id="12137-134">當 EF 工具嘗試在設計階段使用其實作為，並導致 `Update-Database` 或 `dotnet ef database update` 之類的命令失敗時，就會攔截到防護。</span><span class="sxs-lookup"><span data-stu-id="12137-134">They were caught off guard when the EF Tools then tried to use their implementation at design-time and caused commands like `Update-Database` or `dotnet ef database update` to fail.</span></span>

<span data-ttu-id="12137-135">為了傳達此介面的強式設計階段語義，我們已將它重新命名為 `IDesignTimeDbContextFactory<TContext>`。</span><span class="sxs-lookup"><span data-stu-id="12137-135">In order to communicate the strong design-time semantics of this interface, we have renamed it to `IDesignTimeDbContextFactory<TContext>`.</span></span>

<span data-ttu-id="12137-136">若為2.0 版本，`IDbContextFactory<TContext>` 仍然存在，但標示為過時。</span><span class="sxs-lookup"><span data-stu-id="12137-136">For the 2.0 release the `IDbContextFactory<TContext>` still exists but is marked as obsolete.</span></span>

## <a name="dbcontextfactoryoptions-removed"></a><span data-ttu-id="12137-137">DbCoNtextFactoryOptions 已移除</span><span class="sxs-lookup"><span data-stu-id="12137-137">DbContextFactoryOptions removed</span></span>

<span data-ttu-id="12137-138">基於上述 ASP.NET Core 2.0 變更，我們發現新的 `IDesignTimeDbContextFactory<TContext>` 介面上不再需要 `DbContextFactoryOptions`。</span><span class="sxs-lookup"><span data-stu-id="12137-138">Because of the ASP.NET Core 2.0 changes described above, we found that `DbContextFactoryOptions` was no longer needed on the new `IDesignTimeDbContextFactory<TContext>` interface.</span></span> <span data-ttu-id="12137-139">以下是您應該改為使用的替代方案。</span><span class="sxs-lookup"><span data-stu-id="12137-139">Here are the alternatives you should be using instead.</span></span>

| <span data-ttu-id="12137-140">DbCoNtextFactoryOptions</span><span class="sxs-lookup"><span data-stu-id="12137-140">DbContextFactoryOptions</span></span> | <span data-ttu-id="12137-141">替代函式</span><span class="sxs-lookup"><span data-stu-id="12137-141">Alternative</span></span>                                                  |
|:------------------------|:-------------------------------------------------------------|
| <span data-ttu-id="12137-142">ApplicationBasePath</span><span class="sxs-lookup"><span data-stu-id="12137-142">ApplicationBasePath</span></span>     | <span data-ttu-id="12137-143">AppCoNtext. BaseDirectory</span><span class="sxs-lookup"><span data-stu-id="12137-143">AppContext.BaseDirectory</span></span>                                     |
| <span data-ttu-id="12137-144">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="12137-144">ContentRootPath</span></span>         | <span data-ttu-id="12137-145">目錄. GetCurrentDirectory （）</span><span class="sxs-lookup"><span data-stu-id="12137-145">Directory.GetCurrentDirectory()</span></span>                              |
| <span data-ttu-id="12137-146">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="12137-146">EnvironmentName</span></span>         | <span data-ttu-id="12137-147">GetEnvironmentVariable （"ASPNETCORE_ENVIRONMENT"）</span><span class="sxs-lookup"><span data-stu-id="12137-147">Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT")</span></span> |

## <a name="design-time-working-directory-changed"></a><span data-ttu-id="12137-148">設計階段工作目錄已變更</span><span class="sxs-lookup"><span data-stu-id="12137-148">Design-time working directory changed</span></span>

<span data-ttu-id="12137-149">ASP.NET Core 2.0 變更也需要 `dotnet ef` 所使用的工作目錄，以配合 Visual Studio 執行應用程式時所使用的工作目錄。</span><span class="sxs-lookup"><span data-stu-id="12137-149">The ASP.NET Core 2.0 changes also required the working directory used by `dotnet ef` to align with the working directory used by Visual Studio when running your application.</span></span> <span data-ttu-id="12137-150">其中一個可觀察的副作用是，SQLite 檔案名現在是相對於專案目錄，而不是輸出目錄，如同它們的作用。</span><span class="sxs-lookup"><span data-stu-id="12137-150">One observable side effect of this is that SQLite filenames are now relative to the project directory and not the output directory like they used to be.</span></span>

## <a name="ef-core-20-requires-a-20-database-provider"></a><span data-ttu-id="12137-151">EF Core 2.0 需要2.0 資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="12137-151">EF Core 2.0 requires a 2.0 database provider</span></span>

<span data-ttu-id="12137-152">針對 EF Core 2.0，我們在資料庫提供者的使用方式方面做了許多簡化和改進。</span><span class="sxs-lookup"><span data-stu-id="12137-152">For EF Core 2.0 we have made many simplifications and improvements in the way database providers work.</span></span> <span data-ttu-id="12137-153">這表示 1.0. x 和 1.1. x 提供者不會與 EF Core 2.0 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="12137-153">This means that 1.0.x and 1.1.x providers will not work with EF Core 2.0.</span></span>

<span data-ttu-id="12137-154">SQL Server 和 SQLite 提供者都是由 EF 小組隨附，而2.0 版本則會在2.0 版本中提供。</span><span class="sxs-lookup"><span data-stu-id="12137-154">The SQL Server and SQLite providers are shipped by the EF team and 2.0 versions will be available as part of the 2.0 release.</span></span> <span data-ttu-id="12137-155">適用于[SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)、[于 postgresql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)和[MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)的開放原始碼協力廠商提供者正在更新2.0。</span><span class="sxs-lookup"><span data-stu-id="12137-155">The open-source third party providers for [SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact), [PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), and [MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) are being updated for 2.0.</span></span> <span data-ttu-id="12137-156">對於所有其他提供者，請洽詢提供者寫入器。</span><span class="sxs-lookup"><span data-stu-id="12137-156">For all other providers, please contact the provider writer.</span></span>

## <a name="logging-and-diagnostics-events-have-changed"></a><span data-ttu-id="12137-157">記錄和診斷事件已變更</span><span class="sxs-lookup"><span data-stu-id="12137-157">Logging and Diagnostics events have changed</span></span>

<span data-ttu-id="12137-158">注意：這些變更應該不會影響大部分的應用程式程式碼。</span><span class="sxs-lookup"><span data-stu-id="12137-158">Note: these changes should not impact most application code.</span></span>

<span data-ttu-id="12137-159">傳送至[ILogger](/dotnet/api/microsoft.extensions.logging.ilogger)的訊息事件識別碼在2.0 中已變更。</span><span class="sxs-lookup"><span data-stu-id="12137-159">The event IDs for messages sent to an [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) have changed in 2.0.</span></span> <span data-ttu-id="12137-160">在 EF Core 程式碼中，事件識別碼現在是唯一的。</span><span class="sxs-lookup"><span data-stu-id="12137-160">The event IDs are now unique across EF Core code.</span></span> <span data-ttu-id="12137-161">例如，這些訊息現在也遵循 MVC 所使用結構化記錄的標準模式。</span><span class="sxs-lookup"><span data-stu-id="12137-161">These messages now also follow the standard pattern for structured logging used by, for example, MVC.</span></span>

<span data-ttu-id="12137-162">記錄器類別也已經變更。</span><span class="sxs-lookup"><span data-stu-id="12137-162">Logger categories have also changed.</span></span> <span data-ttu-id="12137-163">現在已有一組類別可透過 [DbLoggerCategory](https://github.com/aspnet/EntityFrameworkCore/blob/rel/2.0.0/src/EFCore/DbLoggerCategory.cs) 進行存取。</span><span class="sxs-lookup"><span data-stu-id="12137-163">There is now a well-known set of categories accessed through [DbLoggerCategory](https://github.com/aspnet/EntityFrameworkCore/blob/rel/2.0.0/src/EFCore/DbLoggerCategory.cs).</span></span>

<span data-ttu-id="12137-164">[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)事件現在會使用與對應 `ILogger` 訊息相同的事件識別碼名稱。</span><span class="sxs-lookup"><span data-stu-id="12137-164">[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) events now use the same event ID names as the corresponding `ILogger` messages.</span></span> <span data-ttu-id="12137-165">事件裝載是衍生自[EventData](/dotnet/api/microsoft.entityframeworkcore.diagnostics.eventdata)的所有名義類型。</span><span class="sxs-lookup"><span data-stu-id="12137-165">The event payloads are all nominal types derived from [EventData](/dotnet/api/microsoft.entityframeworkcore.diagnostics.eventdata).</span></span>

<span data-ttu-id="12137-166">事件識別碼、裝載類型和類別記載于[CoreEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.coreeventid)和[RelationalEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.relationaleventid)類別中。</span><span class="sxs-lookup"><span data-stu-id="12137-166">Event IDs, payload types, and categories are documented in the [CoreEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.coreeventid) and the [RelationalEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.relationaleventid) classes.</span></span>

<span data-ttu-id="12137-167">識別碼也已從 Microsoft.entityframeworkcore 移至新的 Microsoft.entityframeworkcore. Diagnostics 命名空間。</span><span class="sxs-lookup"><span data-stu-id="12137-167">IDs have also moved from Microsoft.EntityFrameworkCore.Infrastructure to the new Microsoft.EntityFrameworkCore.Diagnostics namespace.</span></span>

## <a name="ef-core-relational-metadata-api-changes"></a><span data-ttu-id="12137-168">EF Core 關聯式中繼資料 API 變更</span><span class="sxs-lookup"><span data-stu-id="12137-168">EF Core relational metadata API changes</span></span>

<span data-ttu-id="12137-169">EF Core 2.0 現在會為使用的每個不同提供者建置不同的 [IModel](/dotnet/api/microsoft.entityframeworkcore.metadata.imodel)。</span><span class="sxs-lookup"><span data-stu-id="12137-169">EF Core 2.0 will now build a different [IModel](/dotnet/api/microsoft.entityframeworkcore.metadata.imodel) for each different provider being used.</span></span> <span data-ttu-id="12137-170">應用程式通常可以看到這項作業。</span><span class="sxs-lookup"><span data-stu-id="12137-170">This is usually transparent to the application.</span></span> <span data-ttu-id="12137-171">這簡化了較低層級的中繼資料 Api，讓_一般關聯式中繼資料概念_的任何存取一律透過呼叫 `.Relational`，而不是 `.SqlServer`、`.Sqlite`等等。例如，1.1. x 程式碼，如下所示：</span><span class="sxs-lookup"><span data-stu-id="12137-171">This has facilitated a simplification of lower-level metadata APIs such that any access to _common relational metadata concepts_ is always made through a call to `.Relational` instead of `.SqlServer`, `.Sqlite`, etc. For example, 1.1.x code like this:</span></span>

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).SqlServer().TableName;
```

<span data-ttu-id="12137-172">現在應如下所示撰寫：</span><span class="sxs-lookup"><span data-stu-id="12137-172">Should now be written like this:</span></span>

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).Relational().TableName;
```

<span data-ttu-id="12137-173">擴充方法現在可以根據目前使用中的提供者來撰寫條件式程式碼，而不是使用 `ForSqlServerToTable`之類的方法。</span><span class="sxs-lookup"><span data-stu-id="12137-173">Instead of using methods like `ForSqlServerToTable`, extension methods are now available to write conditional code based on the current provider in use.</span></span> <span data-ttu-id="12137-174">例如：</span><span class="sxs-lookup"><span data-stu-id="12137-174">For example:</span></span>

```csharp
modelBuilder.Entity<User>().ToTable(
    Database.IsSqlServer() ? "SqlServerName" : "OtherName");
```

<span data-ttu-id="12137-175">請注意，這種變更僅適用于針對_所有_關聯式提供者所定義的 api/中繼資料。</span><span class="sxs-lookup"><span data-stu-id="12137-175">Note that this change only applies to APIs/metadata that is defined for _all_ relational providers.</span></span> <span data-ttu-id="12137-176">當 API 和中繼資料僅適用于單一提供者時，它會維持不變。</span><span class="sxs-lookup"><span data-stu-id="12137-176">The API and metadata remains the same when it is specific to only a single provider.</span></span> <span data-ttu-id="12137-177">例如，叢集索引是 SQL server 特有的，因此 `ForSqlServerIsClustered` 和 `.SqlServer().IsClustered()` 仍然必須使用。</span><span class="sxs-lookup"><span data-stu-id="12137-177">For example, clustered indexes are specific to SQL Sever, so `ForSqlServerIsClustered` and  `.SqlServer().IsClustered()` must still be used.</span></span>

## <a name="dont-take-control-of-the-ef-service-provider"></a><span data-ttu-id="12137-178">不要接管 EF 服務提供者的控制權</span><span class="sxs-lookup"><span data-stu-id="12137-178">Don’t take control of the EF service provider</span></span>

<span data-ttu-id="12137-179">EF Core 使用內部的 `IServiceProvider` （相依性插入容器）來進行內部執行。</span><span class="sxs-lookup"><span data-stu-id="12137-179">EF Core uses an internal `IServiceProvider` (a dependency injection container) for its internal implementation.</span></span> <span data-ttu-id="12137-180">除非在特殊情況下，否則應用程式應該允許 EF Core 建立和管理此提供者。</span><span class="sxs-lookup"><span data-stu-id="12137-180">Applications should allow EF Core to create and manage this provider except in special cases.</span></span> <span data-ttu-id="12137-181">請考慮移除對 `UseInternalServiceProvider`的任何呼叫。</span><span class="sxs-lookup"><span data-stu-id="12137-181">Strongly consider removing any calls to `UseInternalServiceProvider`.</span></span> <span data-ttu-id="12137-182">如果應用程式需要呼叫 `UseInternalServiceProvider`，請考慮提出[問題](https://github.com/aspnet/EntityFramework/Issues)，讓我們可以調查其他處理您案例的方式。</span><span class="sxs-lookup"><span data-stu-id="12137-182">If an application does need to call `UseInternalServiceProvider`, then please consider [filing an issue](https://github.com/aspnet/EntityFramework/Issues) so we can investigate other ways to handle your scenario.</span></span>

<span data-ttu-id="12137-183">除非同時呼叫 `UseInternalServiceProvider`，否則應用程式代碼不需要呼叫 `AddEntityFramework`、`AddEntityFrameworkSqlServer`等等。</span><span class="sxs-lookup"><span data-stu-id="12137-183">Calling `AddEntityFramework`, `AddEntityFrameworkSqlServer`, etc. is not required by application code unless `UseInternalServiceProvider` is also called.</span></span> <span data-ttu-id="12137-184">移除 `AddEntityFramework` 或 `AddEntityFrameworkSqlServer`的任何現有呼叫等等。 `AddDbContext` 仍應使用與之前相同的方式。</span><span class="sxs-lookup"><span data-stu-id="12137-184">Remove any existing calls to `AddEntityFramework` or `AddEntityFrameworkSqlServer`, etc. `AddDbContext` should still be used in the same way as before.</span></span>

## <a name="in-memory-databases-must-be-named"></a><span data-ttu-id="12137-185">記憶體內部資料庫必須命名為</span><span class="sxs-lookup"><span data-stu-id="12137-185">In-memory databases must be named</span></span>

<span data-ttu-id="12137-186">已移除全域未命名的記憶體內部資料庫，而是必須將所有記憶體中的資料庫命名為。</span><span class="sxs-lookup"><span data-stu-id="12137-186">The global unnamed in-memory database has been removed and instead all in-memory databases must be named.</span></span> <span data-ttu-id="12137-187">例如：</span><span class="sxs-lookup"><span data-stu-id="12137-187">For example:</span></span>

``` csharp
optionsBuilder.UseInMemoryDatabase("MyDatabase");
```

<span data-ttu-id="12137-188">這會建立/使用名為 "MyDatabase" 的資料庫。</span><span class="sxs-lookup"><span data-stu-id="12137-188">This creates/uses a database with the name “MyDatabase”.</span></span> <span data-ttu-id="12137-189">如果使用相同的名稱再次呼叫 `UseInMemoryDatabase`，則會使用相同的記憶體內部資料庫，讓多個內容實例能夠共用它。</span><span class="sxs-lookup"><span data-stu-id="12137-189">If `UseInMemoryDatabase` is called again with the same name, then the same in-memory database will be used, allowing it to be shared by multiple context instances.</span></span>

## <a name="read-only-api-changes"></a><span data-ttu-id="12137-190">唯讀 API 變更</span><span class="sxs-lookup"><span data-stu-id="12137-190">Read-only API changes</span></span>

<span data-ttu-id="12137-191">`IsReadOnlyBeforeSave`、`IsReadOnlyAfterSave`和 `IsStoreGeneratedAlways` 已過時，並已取代為[BeforeSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.beforesavebehavior)和[AfterSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.aftersavebehavior)。</span><span class="sxs-lookup"><span data-stu-id="12137-191">`IsReadOnlyBeforeSave`, `IsReadOnlyAfterSave`, and `IsStoreGeneratedAlways` have been obsoleted and replaced with [BeforeSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.beforesavebehavior) and [AfterSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.aftersavebehavior).</span></span> <span data-ttu-id="12137-192">這些行為適用于任何屬性（而不只是存放區產生的屬性），並決定在插入至資料庫資料列（`BeforeSaveBehavior`）或更新現有的資料庫資料列（`AfterSaveBehavior`）時，應該如何使用屬性的值。</span><span class="sxs-lookup"><span data-stu-id="12137-192">These behaviors apply to any property (not only store-generated properties) and determine how the value of the property should be used when inserting into a database row (`BeforeSaveBehavior`) or when updating an existing database row (`AfterSaveBehavior`).</span></span>

<span data-ttu-id="12137-193">標記為[ValueGenerated. OnAddOrUpdate](/dotnet/api/microsoft.entityframeworkcore.metadata.valuegenerated) （例如，針對計算資料行）的屬性預設會忽略屬性上目前設定的任何值。</span><span class="sxs-lookup"><span data-stu-id="12137-193">Properties marked as [ValueGenerated.OnAddOrUpdate](/dotnet/api/microsoft.entityframeworkcore.metadata.valuegenerated) (for example, for computed columns) will by default ignore any value currently set on the property.</span></span> <span data-ttu-id="12137-194">這表示不論是否已在追蹤的實體上設定或修改任何值，一律會取得存放區產生的值。</span><span class="sxs-lookup"><span data-stu-id="12137-194">This means that a store-generated value will always be obtained regardless of whether any value has been set or modified on the tracked entity.</span></span> <span data-ttu-id="12137-195">這可以藉由設定不同的 `Before\AfterSaveBehavior`來變更。</span><span class="sxs-lookup"><span data-stu-id="12137-195">This can be changed by setting a different `Before\AfterSaveBehavior`.</span></span>

## <a name="new-clientsetnull-delete-behavior"></a><span data-ttu-id="12137-196">新的 Deletebehavior.clientsetnull 刪除行為</span><span class="sxs-lookup"><span data-stu-id="12137-196">New ClientSetNull delete behavior</span></span>

<span data-ttu-id="12137-197">在舊版中， [deletebehavior.setnull](/dotnet/api/microsoft.entityframeworkcore.deletebehavior)會對內容所追蹤的實體行為而言，這些實體會有更多的相符 `SetNull` 語義。</span><span class="sxs-lookup"><span data-stu-id="12137-197">In previous releases, [DeleteBehavior.Restrict](/dotnet/api/microsoft.entityframeworkcore.deletebehavior) had a behavior for entities tracked by the context that more closed matched `SetNull` semantics.</span></span> <span data-ttu-id="12137-198">在 EF Core 2.0 中，已將新的 `ClientSetNull` 行為引進為選擇性關聯性的預設值。</span><span class="sxs-lookup"><span data-stu-id="12137-198">In EF Core 2.0, a new `ClientSetNull` behavior has been introduced as the default for optional relationships.</span></span> <span data-ttu-id="12137-199">此行為具有追蹤實體的 `SetNull` 語義，以及使用 EF Core 建立之資料庫的 `Restrict` 行為。</span><span class="sxs-lookup"><span data-stu-id="12137-199">This behavior has `SetNull` semantics for tracked entities and `Restrict` behavior for databases created using EF Core.</span></span> <span data-ttu-id="12137-200">在我們的經驗中，這些是追蹤實體和資料庫最預期/實用的行為。</span><span class="sxs-lookup"><span data-stu-id="12137-200">In our experience, these are the most expected/useful behaviors for tracked entities and the database.</span></span> <span data-ttu-id="12137-201">針對選擇性關聯性設定時，已追蹤的實體現在會接受 `DeleteBehavior.Restrict`。</span><span class="sxs-lookup"><span data-stu-id="12137-201">`DeleteBehavior.Restrict` is now honored for tracked entities when set for optional relationships.</span></span>

## <a name="provider-design-time-packages-removed"></a><span data-ttu-id="12137-202">已移除提供者設計階段套件</span><span class="sxs-lookup"><span data-stu-id="12137-202">Provider design-time packages removed</span></span>

<span data-ttu-id="12137-203">已移除 `Microsoft.EntityFrameworkCore.Relational.Design` 套件。</span><span class="sxs-lookup"><span data-stu-id="12137-203">The `Microsoft.EntityFrameworkCore.Relational.Design` package has been removed.</span></span> <span data-ttu-id="12137-204">其內容已合併到 `Microsoft.EntityFrameworkCore.Relational` 和 `Microsoft.EntityFrameworkCore.Design`。</span><span class="sxs-lookup"><span data-stu-id="12137-204">It's contents were consolidated into `Microsoft.EntityFrameworkCore.Relational` and `Microsoft.EntityFrameworkCore.Design`.</span></span>

<span data-ttu-id="12137-205">這會傳播到提供者設計階段套件。</span><span class="sxs-lookup"><span data-stu-id="12137-205">This propagates into the provider design-time packages.</span></span> <span data-ttu-id="12137-206">這些套件（`Microsoft.EntityFrameworkCore.Sqlite.Design`、`Microsoft.EntityFrameworkCore.SqlServer.Design`等）已移除，且其內容會合並到主要提供者套件中。</span><span class="sxs-lookup"><span data-stu-id="12137-206">Those packages (`Microsoft.EntityFrameworkCore.Sqlite.Design`, `Microsoft.EntityFrameworkCore.SqlServer.Design`, etc.) were removed and their contents consolidated into the main provider packages.</span></span>

<span data-ttu-id="12137-207">若要在 EF Core 2.0 中啟用 `Scaffold-DbContext` 或 `dotnet ef dbcontext scaffold`，您只需要參考單一提供者套件：</span><span class="sxs-lookup"><span data-stu-id="12137-207">To enable `Scaffold-DbContext` or `dotnet ef dbcontext scaffold` in EF Core 2.0, you only need to reference the single provider package:</span></span>

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer"
    Version="2.0.0" />
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools"
    Version="2.0.0"
    PrivateAssets="All" />
<DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
    Version="2.0.0" />
```
