---
title: 從舊版升級至 EF Core 2 EF Core
author: divega
ms.date: 08/13/2017
ms.assetid: 8BD43C8C-63D9-4F3A-B954-7BC518A1B7DB
uid: core/miscellaneous/1x-2x-upgrade
ms.openlocfilehash: 1222f10811914f65822a49e18522c287ece12174
ms.sourcegitcommit: c9c3e00c2d445b784423469838adc071a946e7c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68306493"
---
# <a name="upgrading-applications-from-previous-versions-to-ef-core-20"></a><span data-ttu-id="01484-102">應用程式從舊版升級 EF Core 2.0</span><span class="sxs-lookup"><span data-stu-id="01484-102">Upgrading applications from previous versions to EF Core 2.0</span></span>

<span data-ttu-id="01484-103">我們已在2.0 中大幅縮小現有 Api 和行為的機會。</span><span class="sxs-lookup"><span data-stu-id="01484-103">We have taken the opportunity to significantly refine our existing APIs and behaviors in 2.0.</span></span> <span data-ttu-id="01484-104">有一些改良功能可能需要修改現有的應用程式程式碼, 但我們相信大部分的應用程式都會造成影響, 而在大部分情況下, 只需要重新編譯和最小的引導式變更來取代已淘汰的 Api。</span><span class="sxs-lookup"><span data-stu-id="01484-104">There are a few improvements that can require modifying existing application code, although we believe that for the majority of applications the impact will be low, in most cases requiring just recompilation and minimal guided changes to replace obsolete APIs.</span></span>

<span data-ttu-id="01484-105">可能需要更新現有的應用程式為 EF Core 2.0:</span><span class="sxs-lookup"><span data-stu-id="01484-105">Updating an existing application to EF Core 2.0 may require:</span></span>

1. <span data-ttu-id="01484-106">將應用程式的目標 .NET 執行升級為支援 .NET Standard 2.0 的其中一個。</span><span class="sxs-lookup"><span data-stu-id="01484-106">Upgrading the target .NET implementation of the application to one that supports .NET Standard 2.0.</span></span> <span data-ttu-id="01484-107">如需詳細資訊, 請參閱[支援的 .Net 部署](../platforms/index.md)。</span><span class="sxs-lookup"><span data-stu-id="01484-107">See [Supported .NET Implementations](../platforms/index.md) for more details.</span></span>

2. <span data-ttu-id="01484-108">識別目標資料庫的 EF Core 2.0 相容的提供者。</span><span class="sxs-lookup"><span data-stu-id="01484-108">Identify a provider for the target database which is compatible with EF Core 2.0.</span></span> <span data-ttu-id="01484-109">請參閱[EF Core 2.0 需要 2.0 資料庫提供者](#ef-core-20-requires-a-20-database-provider)下方。</span><span class="sxs-lookup"><span data-stu-id="01484-109">See [EF Core 2.0 requires a 2.0 database provider](#ef-core-20-requires-a-20-database-provider) below.</span></span>

3. <span data-ttu-id="01484-110">將所有 EF Core 封裝 （執行階段和工具） 都升級為 2.0。</span><span class="sxs-lookup"><span data-stu-id="01484-110">Upgrading all the EF Core packages (runtime and tooling) to 2.0.</span></span> <span data-ttu-id="01484-111">請參閱[安裝 EF Core](../get-started/install/index.md)如需詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="01484-111">Refer to [Installing EF Core](../get-started/install/index.md) for more details.</span></span>

4. <span data-ttu-id="01484-112">進行任何必要的程式碼變更, 以補償本檔其餘部分所述的重大變更。</span><span class="sxs-lookup"><span data-stu-id="01484-112">Make any necessary code changes to compensate for the breaking changes described in the rest of this document.</span></span>

## <a name="aspnet-core-now-includes-ef-core"></a><span data-ttu-id="01484-113">ASP.NET Core 現在包含 EF Core</span><span class="sxs-lookup"><span data-stu-id="01484-113">ASP.NET Core now includes EF Core</span></span>

<span data-ttu-id="01484-114">除了協力廠商資料庫提供者之外，將目標設為 ASP.NET Core 2.0 的應用程式還可以使用 EF Core 2.0，而且沒有其他相依性。</span><span class="sxs-lookup"><span data-stu-id="01484-114">Applications targeting ASP.NET Core 2.0 can use EF Core 2.0 without additional dependencies besides third party database providers.</span></span> <span data-ttu-id="01484-115">不過，目標為舊版的 ASP.NET Core 應用程式必須升級至 ASP.NET Core 2.0，才能使用 EF Core 2.0。</span><span class="sxs-lookup"><span data-stu-id="01484-115">However, applications targeting previous versions of ASP.NET Core need to upgrade to ASP.NET Core 2.0 in order to use EF Core 2.0.</span></span> <span data-ttu-id="01484-116">如需升級為 2.0 的 ASP.NET Core 應用程式的詳細資訊，請參閱[ASP.NET Core 上的文件主體](https://docs.microsoft.com/aspnet/core/migration/1x-to-2x/)。</span><span class="sxs-lookup"><span data-stu-id="01484-116">For more details on upgrading ASP.NET Core applications to 2.0 see [the ASP.NET Core documentation on the subject](https://docs.microsoft.com/aspnet/core/migration/1x-to-2x/).</span></span>

## <a name="new-way-of-getting-application-services-in-aspnet-core"></a><span data-ttu-id="01484-117">在 ASP.NET Core 中取得應用程式服務的新方式</span><span class="sxs-lookup"><span data-stu-id="01484-117">New way of getting application services in ASP.NET Core</span></span>

<span data-ttu-id="01484-118">已更新 ASP.NET Core web 應用程式的建議的模式 2.0 中斷 1.x 中使用的 EF Core 的設計階段邏輯的方式。</span><span class="sxs-lookup"><span data-stu-id="01484-118">The recommended pattern for ASP.NET Core web applications has been updated for 2.0 in a way that broke the design-time logic EF Core used in 1.x.</span></span> <span data-ttu-id="01484-119">先前在設計階段 EF Core 會嘗試叫用`Startup.ConfigureServices`直接才能存取應用程式的服務提供者。</span><span class="sxs-lookup"><span data-stu-id="01484-119">Previously at design-time, EF Core would try to invoke `Startup.ConfigureServices` directly in order to access the application's service provider.</span></span> <span data-ttu-id="01484-120">在 ASP.NET Core 2.0 中，設定初始化之外`Startup`類別。</span><span class="sxs-lookup"><span data-stu-id="01484-120">In ASP.NET Core 2.0, Configuration is initialized outside of the `Startup` class.</span></span> <span data-ttu-id="01484-121">通常使用 EF Core 應用程式存取其連接字串從組態中，因此`Startup`本身已足夠。</span><span class="sxs-lookup"><span data-stu-id="01484-121">Applications using EF Core typically access their connection string from Configuration, so `Startup` by itself is no longer sufficient.</span></span> <span data-ttu-id="01484-122">如果您升級 ASP.NET Core 1.x 應用程式，您可能會收到下列錯誤時使用的 EF Core 工具。</span><span class="sxs-lookup"><span data-stu-id="01484-122">If you upgrade an ASP.NET Core 1.x application, you may receive the following error when using the EF Core tools.</span></span>

> <span data-ttu-id="01484-123">' ApplicationCoNtext ' 上找不到無參數的函數。</span><span class="sxs-lookup"><span data-stu-id="01484-123">No parameterless constructor was found on 'ApplicationContext'.</span></span> <span data-ttu-id="01484-124">請將無參數的函式新增至 ' ApplicationCoNtext ', 或在與&lt;'&gt;ApplicationCoNtext ' 相同的元件中加入 ' IDesignTimeDbCoNtextFactory ApplicationCoNtext ' 的執行</span><span class="sxs-lookup"><span data-stu-id="01484-124">Either add a parameterless constructor to 'ApplicationContext' or add an implementation of 'IDesignTimeDbContextFactory&lt;ApplicationContext&gt;' in the same assembly as 'ApplicationContext'</span></span>

<span data-ttu-id="01484-125">新的設計階段攔截已加入 ASP.NET Core 2.0 的預設範本中。</span><span class="sxs-lookup"><span data-stu-id="01484-125">A new design-time hook has been added in ASP.NET Core 2.0's default template.</span></span> <span data-ttu-id="01484-126">靜態`Program.BuildWebHost`方法可讓在設計階段存取應用程式的服務提供者的 EF Core。</span><span class="sxs-lookup"><span data-stu-id="01484-126">The static `Program.BuildWebHost` method enables EF Core to access the application's service provider at design time.</span></span> <span data-ttu-id="01484-127">如果您要升級 ASP.NET Core 1.x 應用程式, 您將需要更新`Program`類別, 如下所示。</span><span class="sxs-lookup"><span data-stu-id="01484-127">If you are upgrading an ASP.NET Core 1.x application, you will need to update the `Program` class to resemble the following.</span></span>

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

<span data-ttu-id="01484-128">強烈建議您在將應用程式更新為2.0 時採用這個新模式, 這是為了讓 Entity Framework Core 遷移的產品功能正常執行所需。</span><span class="sxs-lookup"><span data-stu-id="01484-128">The adoption of this new pattern when updating applications to 2.0 is highly recommended and is required in order for product features like Entity Framework Core Migrations to work.</span></span> <span data-ttu-id="01484-129">另一個常見的替代方法[是*執行\<IDesignTimeDbCoNtextFactory TCoNtext >* ](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory)。</span><span class="sxs-lookup"><span data-stu-id="01484-129">The other common alternative is to [implement *IDesignTimeDbContextFactory\<TContext>*](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory).</span></span>

## <a name="idbcontextfactory-renamed"></a><span data-ttu-id="01484-130">IDbCoNtextFactory 已重新命名</span><span class="sxs-lookup"><span data-stu-id="01484-130">IDbContextFactory renamed</span></span>

<span data-ttu-id="01484-131">為了支援不同的應用程式模式, 並讓使用者更能控制其`DbContext`在設計階段的使用方式, 我們在過去已`IDbContextFactory<TContext>`提供介面。</span><span class="sxs-lookup"><span data-stu-id="01484-131">In order to support diverse application patterns and give users more control over how their `DbContext` is used at design time, we have, in the past, provided the `IDbContextFactory<TContext>` interface.</span></span> <span data-ttu-id="01484-132">EF Core 工具將在設計階段探索實作這個介面，在您的專案，並使用它來建立`DbContext`物件。</span><span class="sxs-lookup"><span data-stu-id="01484-132">At design-time, the EF Core tools will discover implementations of this interface in your project and use it to create `DbContext` objects.</span></span>

<span data-ttu-id="01484-133">此介面具有非常一般的名稱, 會誤導某些使用者嘗試重新使用它來進行其他`DbContext`建立的案例。</span><span class="sxs-lookup"><span data-stu-id="01484-133">This interface had a very general name which mislead some users to try re-using it for other `DbContext`-creating scenarios.</span></span> <span data-ttu-id="01484-134">當 EF 工具在設計階段嘗試使用其實作為, 並導致像或`Update-Database` `dotnet ef database update`之類的命令失敗時, 就會攔截到防護。</span><span class="sxs-lookup"><span data-stu-id="01484-134">They were caught off guard when the EF Tools then tried to use their implementation at design-time and caused commands like `Update-Database` or `dotnet ef database update` to fail.</span></span>

<span data-ttu-id="01484-135">為了傳達此介面的強式設計階段語義, 我們已將它重新命名為`IDesignTimeDbContextFactory<TContext>`。</span><span class="sxs-lookup"><span data-stu-id="01484-135">In order to communicate the strong design-time semantics of this interface, we have renamed it to `IDesignTimeDbContextFactory<TContext>`.</span></span>

<span data-ttu-id="01484-136">若為2.0 版本, `IDbContextFactory<TContext>`仍然存在, 但標示為過時。</span><span class="sxs-lookup"><span data-stu-id="01484-136">For the 2.0 release the `IDbContextFactory<TContext>` still exists but is marked as obsolete.</span></span>

## <a name="dbcontextfactoryoptions-removed"></a><span data-ttu-id="01484-137">DbCoNtextFactoryOptions 已移除</span><span class="sxs-lookup"><span data-stu-id="01484-137">DbContextFactoryOptions removed</span></span>

<span data-ttu-id="01484-138">由於 ASP.NET Core 2.0 變更上面所述的情況下，我們發現`DbContextFactoryOptions`已不再需要針對新`IDesignTimeDbContextFactory<TContext>`介面。</span><span class="sxs-lookup"><span data-stu-id="01484-138">Because of the ASP.NET Core 2.0 changes described above, we found that `DbContextFactoryOptions` was no longer needed on the new `IDesignTimeDbContextFactory<TContext>` interface.</span></span> <span data-ttu-id="01484-139">以下是您應該改為使用的替代方案。</span><span class="sxs-lookup"><span data-stu-id="01484-139">Here are the alternatives you should be using instead.</span></span>

| <span data-ttu-id="01484-140">DbContextFactoryOptions</span><span class="sxs-lookup"><span data-stu-id="01484-140">DbContextFactoryOptions</span></span> | <span data-ttu-id="01484-141">替代函式</span><span class="sxs-lookup"><span data-stu-id="01484-141">Alternative</span></span>                                                  |
|:------------------------|:-------------------------------------------------------------|
| <span data-ttu-id="01484-142">ApplicationBasePath</span><span class="sxs-lookup"><span data-stu-id="01484-142">ApplicationBasePath</span></span>     | <span data-ttu-id="01484-143">AppContext.BaseDirectory</span><span class="sxs-lookup"><span data-stu-id="01484-143">AppContext.BaseDirectory</span></span>                                     |
| <span data-ttu-id="01484-144">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="01484-144">ContentRootPath</span></span>         | <span data-ttu-id="01484-145">Directory.GetCurrentDirectory()</span><span class="sxs-lookup"><span data-stu-id="01484-145">Directory.GetCurrentDirectory()</span></span>                              |
| <span data-ttu-id="01484-146">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="01484-146">EnvironmentName</span></span>         | <span data-ttu-id="01484-147">Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT")</span><span class="sxs-lookup"><span data-stu-id="01484-147">Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT")</span></span> |

## <a name="design-time-working-directory-changed"></a><span data-ttu-id="01484-148">設計階段工作目錄已變更</span><span class="sxs-lookup"><span data-stu-id="01484-148">Design-time working directory changed</span></span>

<span data-ttu-id="01484-149">ASP.NET Core 2.0 變更也會需要所使用的工作目錄`dotnet ef`能配合執行您的應用程式時，Visual Studio 所用的工作目錄。</span><span class="sxs-lookup"><span data-stu-id="01484-149">The ASP.NET Core 2.0 changes also required the working directory used by `dotnet ef` to align with the working directory used by Visual Studio when running your application.</span></span> <span data-ttu-id="01484-150">其中一個可觀察的副作用是, SQLite 檔案名現在是相對於專案目錄, 而不是輸出目錄, 如同它們的作用。</span><span class="sxs-lookup"><span data-stu-id="01484-150">One observable side effect of this is that SQLite filenames are now relative to the project directory and not the output directory like they used to be.</span></span>

## <a name="ef-core-20-requires-a-20-database-provider"></a><span data-ttu-id="01484-151">EF Core 2.0 需要 2.0 資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="01484-151">EF Core 2.0 requires a 2.0 database provider</span></span>

<span data-ttu-id="01484-152">EF Core 2.0 我們進行了許多簡單化和增強功能的方式資料庫提供者中運作。</span><span class="sxs-lookup"><span data-stu-id="01484-152">For EF Core 2.0 we have made many simplifications and improvements in the way database providers work.</span></span> <span data-ttu-id="01484-153">這表示 EF Core 2.0 1.0.x 和 1.1.x 提供者將無法運作。</span><span class="sxs-lookup"><span data-stu-id="01484-153">This means that 1.0.x and 1.1.x providers will not work with EF Core 2.0.</span></span>

<span data-ttu-id="01484-154">SQL Server 和 SQLite 提供者都是由 EF 小組隨附, 而2.0 版本則會在2.0 版本中提供。</span><span class="sxs-lookup"><span data-stu-id="01484-154">The SQL Server and SQLite providers are shipped by the EF team and 2.0 versions will be available as part of the 2.0 release.</span></span> <span data-ttu-id="01484-155">適用于[SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)、[于 postgresql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)和[MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)的開放原始碼協力廠商提供者正在更新2.0。</span><span class="sxs-lookup"><span data-stu-id="01484-155">The open-source third party providers for [SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact), [PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), and [MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) are being updated for 2.0.</span></span> <span data-ttu-id="01484-156">對於所有其他提供者, 請洽詢提供者寫入器。</span><span class="sxs-lookup"><span data-stu-id="01484-156">For all other providers, please contact the provider writer.</span></span>

## <a name="logging-and-diagnostics-events-have-changed"></a><span data-ttu-id="01484-157">記錄和診斷事件已變更</span><span class="sxs-lookup"><span data-stu-id="01484-157">Logging and Diagnostics events have changed</span></span>

<span data-ttu-id="01484-158">注意: 這些變更應該不會影響大部分的應用程式程式碼。</span><span class="sxs-lookup"><span data-stu-id="01484-158">Note: these changes should not impact most application code.</span></span>

<span data-ttu-id="01484-159">傳送至[ILogger](https://github.com/aspnet/Logging/blob/dev/src/Microsoft.Extensions.Logging.Abstractions/ILogger.cs)的訊息事件識別碼在2.0 中已變更。</span><span class="sxs-lookup"><span data-stu-id="01484-159">The event IDs for messages sent to an [ILogger](https://github.com/aspnet/Logging/blob/dev/src/Microsoft.Extensions.Logging.Abstractions/ILogger.cs) have changed in 2.0.</span></span> <span data-ttu-id="01484-160">在 EF Core 程式碼中，事件識別碼現在是唯一的。</span><span class="sxs-lookup"><span data-stu-id="01484-160">The event IDs are now unique across EF Core code.</span></span> <span data-ttu-id="01484-161">例如，這些訊息現在也遵循 MVC 所使用結構化記錄的標準模式。</span><span class="sxs-lookup"><span data-stu-id="01484-161">These messages now also follow the standard pattern for structured logging used by, for example, MVC.</span></span>

<span data-ttu-id="01484-162">記錄器類別也已經變更。</span><span class="sxs-lookup"><span data-stu-id="01484-162">Logger categories have also changed.</span></span> <span data-ttu-id="01484-163">現在已有一組類別可透過 [DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/DbLoggerCategory.cs) 進行存取。</span><span class="sxs-lookup"><span data-stu-id="01484-163">There is now a well-known set of categories accessed through [DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/DbLoggerCategory.cs).</span></span>

<span data-ttu-id="01484-164">[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)事件現在會使用與對應`ILogger`訊息相同的事件識別碼名稱。</span><span class="sxs-lookup"><span data-stu-id="01484-164">[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) events now use the same event ID names as the corresponding `ILogger` messages.</span></span> <span data-ttu-id="01484-165">事件裝載是衍生自[EventData](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Diagnostics/EventData.cs)的所有名義類型。</span><span class="sxs-lookup"><span data-stu-id="01484-165">The event payloads are all nominal types derived from [EventData](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Diagnostics/EventData.cs).</span></span>

<span data-ttu-id="01484-166">事件識別碼、裝載類型和類別記載于[CoreEventId](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Diagnostics/CoreEventId.cs)和[RelationalEventId](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore.Relational/Diagnostics/RelationalEventId.cs)類別中。</span><span class="sxs-lookup"><span data-stu-id="01484-166">Event IDs, payload types, and categories are documented in the [CoreEventId](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Diagnostics/CoreEventId.cs) and the [RelationalEventId](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore.Relational/Diagnostics/RelationalEventId.cs) classes.</span></span>

<span data-ttu-id="01484-167">識別碼也已從 Microsoft.entityframeworkcore 移至新的 Microsoft.entityframeworkcore. Diagnostics 命名空間。</span><span class="sxs-lookup"><span data-stu-id="01484-167">IDs have also moved from Microsoft.EntityFrameworkCore.Infrastructure to the new Microsoft.EntityFrameworkCore.Diagnostics namespace.</span></span>

## <a name="ef-core-relational-metadata-api-changes"></a><span data-ttu-id="01484-168">EF Core 關聯式中繼資料 API 變更</span><span class="sxs-lookup"><span data-stu-id="01484-168">EF Core relational metadata API changes</span></span>

<span data-ttu-id="01484-169">EF Core 2.0 現在會為使用的每個不同提供者建置不同的 [IModel](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IModel.cs)。</span><span class="sxs-lookup"><span data-stu-id="01484-169">EF Core 2.0 will now build a different [IModel](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IModel.cs) for each different provider being used.</span></span> <span data-ttu-id="01484-170">應用程式通常可以看到這項作業。</span><span class="sxs-lookup"><span data-stu-id="01484-170">This is usually transparent to the application.</span></span> <span data-ttu-id="01484-171">這已加速簡化較低階中繼資料 API；因此，任何對_一般關聯式中繼資料概念_的存取一律是透過 `.Relational` 呼叫來進行，而非 `.SqlServer`、`.Sqlite` 等等。例如, 1.1. x 程式碼, 如下所示:</span><span class="sxs-lookup"><span data-stu-id="01484-171">This has facilitated a simplification of lower-level metadata APIs such that any access to _common relational metadata concepts_ is always made through a call to `.Relational` instead of `.SqlServer`, `.Sqlite`, etc. For example, 1.1.x code like this:</span></span>

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).SqlServer().TableName;
```

<span data-ttu-id="01484-172">現在應如下所示撰寫:</span><span class="sxs-lookup"><span data-stu-id="01484-172">Should now be written like this:</span></span>

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).Relational().TableName;
```

<span data-ttu-id="01484-173">擴充方法現在可以根據`ForSqlServerToTable`目前使用中的提供者來撰寫條件式程式碼, 而不是使用這類方法。</span><span class="sxs-lookup"><span data-stu-id="01484-173">Instead of using methods like `ForSqlServerToTable`, extension methods are now available to write conditional code based on the current provider in use.</span></span> <span data-ttu-id="01484-174">例如：</span><span class="sxs-lookup"><span data-stu-id="01484-174">For example:</span></span>

```C#
modelBuilder.Entity<User>().ToTable(
    Database.IsSqlServer() ? "SqlServerName" : "OtherName");
```

<span data-ttu-id="01484-175">請注意, 這種變更僅適用于針對_所有_關聯式提供者所定義的 api/中繼資料。</span><span class="sxs-lookup"><span data-stu-id="01484-175">Note that this change only applies to APIs/metadata that is defined for _all_ relational providers.</span></span> <span data-ttu-id="01484-176">當 API 和中繼資料僅適用于單一提供者時, 它會維持不變。</span><span class="sxs-lookup"><span data-stu-id="01484-176">The API and metadata remains the same when it is specific to only a single provider.</span></span> <span data-ttu-id="01484-177">例如, 叢集索引是 SQL server 特有的, 因此`ForSqlServerIsClustered`和`.SqlServer().IsClustered()`仍然必須使用。</span><span class="sxs-lookup"><span data-stu-id="01484-177">For example, clustered indexes are specific to SQL Sever, so `ForSqlServerIsClustered` and  `.SqlServer().IsClustered()` must still be used.</span></span>

## <a name="dont-take-control-of-the-ef-service-provider"></a><span data-ttu-id="01484-178">不要接管 EF 服務提供者的控制權</span><span class="sxs-lookup"><span data-stu-id="01484-178">Don’t take control of the EF service provider</span></span>

<span data-ttu-id="01484-179">EF Core 在內部執行`IServiceProvider`中使用內部 (相依性插入容器)。</span><span class="sxs-lookup"><span data-stu-id="01484-179">EF Core uses an internal `IServiceProvider` (a dependency injection container) for its internal implementation.</span></span> <span data-ttu-id="01484-180">應用程式應該允許建立和管理在特殊情況下除外此提供者的 EF Core。</span><span class="sxs-lookup"><span data-stu-id="01484-180">Applications should allow EF Core to create and manage this provider except in special cases.</span></span> <span data-ttu-id="01484-181">強烈建議您移除對`UseInternalServiceProvider`的任何呼叫。</span><span class="sxs-lookup"><span data-stu-id="01484-181">Strongly consider removing any calls to `UseInternalServiceProvider`.</span></span> <span data-ttu-id="01484-182">如果應用程式需要呼叫`UseInternalServiceProvider`, 請考慮提出[問題](https://github.com/aspnet/EntityFramework/Issues), 讓我們可以調查其他處理您案例的方式。</span><span class="sxs-lookup"><span data-stu-id="01484-182">If an application does need to call `UseInternalServiceProvider`, then please consider [filing an issue](https://github.com/aspnet/EntityFramework/Issues) so we can investigate other ways to handle your scenario.</span></span>

<span data-ttu-id="01484-183">除非`AddEntityFramework`也`AddEntityFrameworkSqlServer`呼叫, 否則`UseInternalServiceProvider`應用程式代碼不需要呼叫、等等。</span><span class="sxs-lookup"><span data-stu-id="01484-183">Calling `AddEntityFramework`, `AddEntityFrameworkSqlServer`, etc. is not required by application code unless `UseInternalServiceProvider` is also called.</span></span> <span data-ttu-id="01484-184">移除對`AddEntityFramework`或`AddEntityFrameworkSqlServer`的任何現有呼叫等等`AddDbContext` , 其使用方式仍應與之前相同。</span><span class="sxs-lookup"><span data-stu-id="01484-184">Remove any existing calls to `AddEntityFramework` or `AddEntityFrameworkSqlServer`, etc. `AddDbContext` should still be used in the same way as before.</span></span>

## <a name="in-memory-databases-must-be-named"></a><span data-ttu-id="01484-185">記憶體內部資料庫必須命名為</span><span class="sxs-lookup"><span data-stu-id="01484-185">In-memory databases must be named</span></span>

<span data-ttu-id="01484-186">已移除全域未命名的記憶體內部資料庫, 而是必須將所有記憶體中的資料庫命名為。</span><span class="sxs-lookup"><span data-stu-id="01484-186">The global unnamed in-memory database has been removed and instead all in-memory databases must be named.</span></span> <span data-ttu-id="01484-187">例如：</span><span class="sxs-lookup"><span data-stu-id="01484-187">For example:</span></span>

``` csharp
optionsBuilder.UseInMemoryDatabase("MyDatabase");
```

<span data-ttu-id="01484-188">這會建立/使用名為 "MyDatabase" 的資料庫。</span><span class="sxs-lookup"><span data-stu-id="01484-188">This creates/uses a database with the name “MyDatabase”.</span></span> <span data-ttu-id="01484-189">如果`UseInMemoryDatabase`使用相同的名稱再次呼叫, 則會使用相同的記憶體內部資料庫, 讓多個內容實例能夠共用它。</span><span class="sxs-lookup"><span data-stu-id="01484-189">If `UseInMemoryDatabase` is called again with the same name, then the same in-memory database will be used, allowing it to be shared by multiple context instances.</span></span>

## <a name="read-only-api-changes"></a><span data-ttu-id="01484-190">唯讀 API 變更</span><span class="sxs-lookup"><span data-stu-id="01484-190">Read-only API changes</span></span>

<span data-ttu-id="01484-191">`IsReadOnlyBeforeSave`、 `IsReadOnlyAfterSave`和`IsStoreGeneratedAlways`已過時, 並已取代為[BeforeSaveBehavior](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IProperty.cs#L39)和[AfterSaveBehavior](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IProperty.cs#L55)。</span><span class="sxs-lookup"><span data-stu-id="01484-191">`IsReadOnlyBeforeSave`, `IsReadOnlyAfterSave`, and `IsStoreGeneratedAlways` have been obsoleted and replaced with [BeforeSaveBehavior](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IProperty.cs#L39) and [AfterSaveBehavior](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IProperty.cs#L55).</span></span> <span data-ttu-id="01484-192">這些行為適用于任何屬性 (而不只是存放區產生的屬性), 並決定在插入至資料庫資料列 (`BeforeSaveBehavior`) 或更新現有的資料庫資料列 (`AfterSaveBehavior`) 時, 應該如何使用屬性的值。</span><span class="sxs-lookup"><span data-stu-id="01484-192">These behaviors apply to any property (not only store-generated properties) and determine how the value of the property should be used when inserting into a database row (`BeforeSaveBehavior`) or when updating an existing database row (`AfterSaveBehavior`).</span></span>

<span data-ttu-id="01484-193">標記為[ValueGenerated. OnAddOrUpdate](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/ValueGenerated.cs) (例如, 針對計算資料行) 的屬性預設會忽略屬性上目前設定的任何值。</span><span class="sxs-lookup"><span data-stu-id="01484-193">Properties marked as [ValueGenerated.OnAddOrUpdate](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/ValueGenerated.cs) (for example, for computed columns) will by default ignore any value currently set on the property.</span></span> <span data-ttu-id="01484-194">這表示不論是否已在追蹤的實體上設定或修改任何值, 一律會取得存放區產生的值。</span><span class="sxs-lookup"><span data-stu-id="01484-194">This means that a store-generated value will always be obtained regardless of whether any value has been set or modified on the tracked entity.</span></span> <span data-ttu-id="01484-195">這可以藉由設定不同`Before\AfterSaveBehavior`的來變更。</span><span class="sxs-lookup"><span data-stu-id="01484-195">This can be changed by setting a different `Before\AfterSaveBehavior`.</span></span>

## <a name="new-clientsetnull-delete-behavior"></a><span data-ttu-id="01484-196">新的 Deletebehavior.clientsetnull 刪除行為</span><span class="sxs-lookup"><span data-stu-id="01484-196">New ClientSetNull delete behavior</span></span>

<span data-ttu-id="01484-197">在舊版中, [deletebehavior.setnull](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/DeleteBehavior.cs)會針對內容所追蹤的實體行為, 這些專案會有更多`SetNull`的已關閉符合的語義。</span><span class="sxs-lookup"><span data-stu-id="01484-197">In previous releases, [DeleteBehavior.Restrict](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/DeleteBehavior.cs) had a behavior for entities tracked by the context that more closed matched `SetNull` semantics.</span></span> <span data-ttu-id="01484-198">在 EF Core 2.0 中，新`ClientSetNull`為選擇性的關聯性的預設值已經導入行為。</span><span class="sxs-lookup"><span data-stu-id="01484-198">In EF Core 2.0, a new `ClientSetNull` behavior has been introduced as the default for optional relationships.</span></span> <span data-ttu-id="01484-199">這種行為有`SetNull`追蹤實體的語意和`Restrict`建立使用 EF Core 資料庫的行為。</span><span class="sxs-lookup"><span data-stu-id="01484-199">This behavior has `SetNull` semantics for tracked entities and `Restrict` behavior for databases created using EF Core.</span></span> <span data-ttu-id="01484-200">在我們的經驗中, 這些是追蹤實體和資料庫最預期/實用的行為。</span><span class="sxs-lookup"><span data-stu-id="01484-200">In our experience, these are the most expected/useful behaviors for tracked entities and the database.</span></span> <span data-ttu-id="01484-201">`DeleteBehavior.Restrict`針對選擇性關聯性設定時, 現在會接受追蹤的實體。</span><span class="sxs-lookup"><span data-stu-id="01484-201">`DeleteBehavior.Restrict` is now honored for tracked entities when set for optional relationships.</span></span>

## <a name="provider-design-time-packages-removed"></a><span data-ttu-id="01484-202">已移除提供者設計階段套件</span><span class="sxs-lookup"><span data-stu-id="01484-202">Provider design-time packages removed</span></span>

<span data-ttu-id="01484-203">已`Microsoft.EntityFrameworkCore.Relational.Design`移除封裝。</span><span class="sxs-lookup"><span data-stu-id="01484-203">The `Microsoft.EntityFrameworkCore.Relational.Design` package has been removed.</span></span> <span data-ttu-id="01484-204">其內容已合併至`Microsoft.EntityFrameworkCore.Relational`和。 `Microsoft.EntityFrameworkCore.Design`</span><span class="sxs-lookup"><span data-stu-id="01484-204">It's contents were consolidated into `Microsoft.EntityFrameworkCore.Relational` and `Microsoft.EntityFrameworkCore.Design`.</span></span>

<span data-ttu-id="01484-205">這會傳播到提供者設計階段套件。</span><span class="sxs-lookup"><span data-stu-id="01484-205">This propagates into the provider design-time packages.</span></span> <span data-ttu-id="01484-206">這些套件 (`Microsoft.EntityFrameworkCore.Sqlite.Design`、 `Microsoft.EntityFrameworkCore.SqlServer.Design`等) 已移除, 且其內容會合並到主要提供者封裝中。</span><span class="sxs-lookup"><span data-stu-id="01484-206">Those packages (`Microsoft.EntityFrameworkCore.Sqlite.Design`, `Microsoft.EntityFrameworkCore.SqlServer.Design`, etc.) were removed and their contents consolidated into the main provider packages.</span></span>

<span data-ttu-id="01484-207">若要啟用`Scaffold-DbContext`或`dotnet ef dbcontext scaffold`在 EF Core 2.0 中，您只需要參考單一提供者封裝：</span><span class="sxs-lookup"><span data-stu-id="01484-207">To enable `Scaffold-DbContext` or `dotnet ef dbcontext scaffold` in EF Core 2.0, you only need to reference the single provider package:</span></span>

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer"
    Version="2.0.0" />
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools"
    Version="2.0.0"
    PrivateAssets="All" />
<DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
    Version="2.0.0" />
```
