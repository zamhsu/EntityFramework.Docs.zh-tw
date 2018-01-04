---
title: "從舊版升級至 EF 核心 2 EF 核心"
author: divega
ms.author: divega
ms.date: 8/13/2017
ms.assetid: 8BD43C8C-63D9-4F3A-B954-7BC518A1B7DB
ms.technology: entity-framework-core
uid: core/miscellaneous/1x-2x-upgrade
ms.openlocfilehash: 380f27c9f00943a2909ec7b876e151572a67dc37
ms.sourcegitcommit: ced2637bf8cc5964c6daa6c7fcfce501bf9ef6e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2017
---
# <a name="upgrading-applications-from-previous-versions-to-ef-core-20"></a><span data-ttu-id="884aa-102">應用程式從舊版升級 EF 核心 2.0</span><span class="sxs-lookup"><span data-stu-id="884aa-102">Upgrading applications from previous versions to EF Core 2.0</span></span>

## <a name="procedures-common-to-all-applications"></a><span data-ttu-id="884aa-103">通用的所有應用程式的程序</span><span class="sxs-lookup"><span data-stu-id="884aa-103">Procedures Common to All Applications</span></span>

<span data-ttu-id="884aa-104">可能需要更新現有的應用程式為 EF 核心 2.0:</span><span class="sxs-lookup"><span data-stu-id="884aa-104">Updating an existing application to EF Core 2.0 may require:</span></span>

1. <span data-ttu-id="884aa-105">升級至支援的.NET 標準 2.0 應用程式的目標.NET 平台。</span><span class="sxs-lookup"><span data-stu-id="884aa-105">Upgrading the target .NET platform of the application to one that supports .NET Standard 2.0.</span></span> <span data-ttu-id="884aa-106">請參閱[支援的平台](../platforms/index.md)如需詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="884aa-106">See [Supported Platforms](../platforms/index.md) for more details.</span></span>

2. <span data-ttu-id="884aa-107">識別目標資料庫的 EF 核心 2.0 相容的提供者。</span><span class="sxs-lookup"><span data-stu-id="884aa-107">Identify a provider for the target database which is compatible with EF Core 2.0.</span></span> <span data-ttu-id="884aa-108">請參閱[EF 核心 2.0 需要 2.0 資料庫提供者](#ef-core-20-requires-a-20-database-provider)下方。</span><span class="sxs-lookup"><span data-stu-id="884aa-108">See [EF Core 2.0 requires a 2.0 database provider](#ef-core-20-requires-a-20-database-provider) below.</span></span>

3. <span data-ttu-id="884aa-109">將所有 EF 核心封裝 （執行階段和工具） 都升級為 2.0。</span><span class="sxs-lookup"><span data-stu-id="884aa-109">Upgrading all the EF Core packages (runtime and tooling) to 2.0.</span></span> <span data-ttu-id="884aa-110">請參閱[安裝 EF 核心](../get-started/install/index.md)如需詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="884aa-110">Refer to [Installing EF Core](../get-started/install/index.md) for more details.</span></span>

4. <span data-ttu-id="884aa-111">進行任何必要的程式碼變更，以彌補重大變更。</span><span class="sxs-lookup"><span data-stu-id="884aa-111">Make any necessary code changes to compensate for breaking changes.</span></span> <span data-ttu-id="884aa-112">請參閱[的重大變更](#breaking-changes)下面章節以取得詳細資料。</span><span class="sxs-lookup"><span data-stu-id="884aa-112">See the [Breaking Changes](#breaking-changes) section below for more details.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="884aa-113">ASP.NET Core 應用程式</span><span class="sxs-lookup"><span data-stu-id="884aa-113">ASP.NET Core applications</span></span>

1. <span data-ttu-id="884aa-114">請特別參閱[初始化應用程式的服務提供者的新模式](#new-way-of-getting-application-services)如下所述。</span><span class="sxs-lookup"><span data-stu-id="884aa-114">See in particular the [new pattern for initializing the application's service provider](#new-way-of-getting-application-services) described below.</span></span>

> [!TIP]  
> <span data-ttu-id="884aa-115">這個新模式，當更新應用程式為 2.0 強烈建議您，而且在產品功能，例如 Entity Framework Core 移轉工作的順序必要的採用狀況。</span><span class="sxs-lookup"><span data-stu-id="884aa-115">The adoption of this new pattern when updating applications to 2.0 is highly recommended and is required in order for product features like Entity Framework Core Migrations to work.</span></span> <span data-ttu-id="884aa-116">其他一般的替代方式是[實作*IDesignTimeDbContextFactory\<TContext >*](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory)。</span><span class="sxs-lookup"><span data-stu-id="884aa-116">The other common alternative is to [implement *IDesignTimeDbContextFactory\<TContext>*](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory).</span></span>

2. <span data-ttu-id="884aa-117">除了協力廠商資料庫提供者之外，將目標設為 ASP.NET Core 2.0 的應用程式還可以使用 EF Core 2.0，而且沒有其他相依性。</span><span class="sxs-lookup"><span data-stu-id="884aa-117">Applications targeting ASP.NET Core 2.0 can use EF Core 2.0 without additional dependencies besides third party database providers.</span></span> <span data-ttu-id="884aa-118">不過，目標為舊版的 ASP.NET Core 應用程式必須升級至 ASP.NET Core 2.0，才能使用 EF 核心 2.0。</span><span class="sxs-lookup"><span data-stu-id="884aa-118">However, applications targeting previous versions of ASP.NET Core need to upgrade to ASP.NET Core 2.0 in order to use EF Core 2.0.</span></span> <span data-ttu-id="884aa-119">如需升級為 2.0 的 ASP.NET Core 應用程式的詳細資訊，請參閱[ASP.NET Core 上的文件主體](https://docs.microsoft.com/aspnet/core/migration/1x-to-2x/)。</span><span class="sxs-lookup"><span data-stu-id="884aa-119">For more details on upgrading ASP.NET Core applications to 2.0 see [the ASP.NET Core documentation on the subject](https://docs.microsoft.com/aspnet/core/migration/1x-to-2x/).</span></span>

## <a name="breaking-changes"></a><span data-ttu-id="884aa-120">重大變更</span><span class="sxs-lookup"><span data-stu-id="884aa-120">Breaking Changes</span></span>

<span data-ttu-id="884aa-121">我們已有機會大幅改善我們的現有應用程式開發介面和 2.0 中的行為。</span><span class="sxs-lookup"><span data-stu-id="884aa-121">We have taken the opportunity to significantly refine our existing APIs and behaviors in 2.0.</span></span> <span data-ttu-id="884aa-122">有幾個可能需要修改現有的應用程式程式碼的增強功能，雖然我們相信大部分的應用程式的影響會很低時，在大部分情況下，需要只重新編譯和幾乎不需要 「 引導式的變更，以取代過時的 Api。</span><span class="sxs-lookup"><span data-stu-id="884aa-122">There are a few improvements that can require modifying existing application code, although we believe that for the majority of applications the impact will be low, in most cases requiring just recompilation and minimal guided changes to replace obsolete APIs.</span></span>

### <a name="new-way-of-getting-application-services"></a><span data-ttu-id="884aa-123">新方法來取得應用程式服務</span><span class="sxs-lookup"><span data-stu-id="884aa-123">New way of getting application services</span></span>

<span data-ttu-id="884aa-124">已更新 ASP.NET Core web 應用程式的建議的模式 2.0 中斷 1.x 中使用的 EF 核心的設計階段邏輯的方式。</span><span class="sxs-lookup"><span data-stu-id="884aa-124">The recommended pattern for ASP.NET Core web applications has been updated for 2.0 in a way that broke the design-time logic EF Core used in 1.x.</span></span> <span data-ttu-id="884aa-125">先前在設計階段 EF 核心會嘗試叫用`Startup.ConfigureServices`直接才能存取應用程式的服務提供者。</span><span class="sxs-lookup"><span data-stu-id="884aa-125">Previously at design-time, EF Core would try to invoke `Startup.ConfigureServices` directly in order to access the application's service provider.</span></span> <span data-ttu-id="884aa-126">在 ASP.NET Core 2.0 中，設定初始化之外`Startup`類別。</span><span class="sxs-lookup"><span data-stu-id="884aa-126">In ASP.NET Core 2.0, Configuration is initialized outside of the `Startup` class.</span></span> <span data-ttu-id="884aa-127">通常使用 EF 核心應用程式存取其連接字串從組態中，因此`Startup`本身已足夠。</span><span class="sxs-lookup"><span data-stu-id="884aa-127">Applications using EF Core typically access their connection string from Configuration, so `Startup` by itself is no longer sufficient.</span></span> <span data-ttu-id="884aa-128">如果您升級 ASP.NET Core 1.x 應用程式，您可能會收到下列錯誤時使用的 EF 核心工具。</span><span class="sxs-lookup"><span data-stu-id="884aa-128">If you upgrade an ASP.NET Core 1.x application, you may receive the following error when using the EF Core tools.</span></span>

> <span data-ttu-id="884aa-129">'ApplicationContext' 上找不到沒有無參數建構函式。</span><span class="sxs-lookup"><span data-stu-id="884aa-129">No parameterless constructor was found on 'ApplicationContext'.</span></span> <span data-ttu-id="884aa-130">請加入 'ApplicationContext' 參數的建構函式，或新增的實作 'IDesignTimeDbContextFactory&lt;ApplicationContext&gt;' 中 'ApplicationContext' 相同的組件</span><span class="sxs-lookup"><span data-stu-id="884aa-130">Either add a parameterless constructor to 'ApplicationContext' or add an implementation of 'IDesignTimeDbContextFactory&lt;ApplicationContext&gt;' in the same assembly as 'ApplicationContext'</span></span>

<span data-ttu-id="884aa-131">新的設計階段攔截已加入 ASP.NET Core 2.0 的預設範本中。</span><span class="sxs-lookup"><span data-stu-id="884aa-131">A new design-time hook has been added in ASP.NET Core 2.0's default template.</span></span> <span data-ttu-id="884aa-132">靜態`Program.BuildWebHost`方法可讓在設計階段存取應用程式的服務提供者的 EF 核心。</span><span class="sxs-lookup"><span data-stu-id="884aa-132">The static `Program.BuildWebHost` method enables EF Core to access the application's service provider at design time.</span></span> <span data-ttu-id="884aa-133">如果您要升級的 ASP.NET Core 1.x 應用程式，您必須更新您`Program`類別，如下所示。</span><span class="sxs-lookup"><span data-stu-id="884aa-133">If you are upgrading an ASP.NET Core 1.x application, you will need to update you `Program` class to resemble the following.</span></span>

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

### <a name="idbcontextfactory-renamed"></a><span data-ttu-id="884aa-134">重新命名的 IDbContextFactory</span><span class="sxs-lookup"><span data-stu-id="884aa-134">IDbContextFactory renamed</span></span>

<span data-ttu-id="884aa-135">為了支援各種不同的應用程式模式，讓使用者更充分掌控如何其`DbContext`會使用在設計階段，我們有，在過去，提供`IDbContextFactory<TContext>`介面。</span><span class="sxs-lookup"><span data-stu-id="884aa-135">In order to support diverse application patterns and give users more control over how their `DbContext` is used at design time, we have, in the past, provided the `IDbContextFactory<TContext>` interface.</span></span> <span data-ttu-id="884aa-136">EF 核心工具將在設計階段探索實作這個介面，在您的專案，並使用它來建立`DbContext`物件。</span><span class="sxs-lookup"><span data-stu-id="884aa-136">At design-time, the EF Core tools will discover implementations of this interface in your project and use it to create `DbContext` objects.</span></span>

<span data-ttu-id="884aa-137">這個介面有誤導嘗試重新使用它的其他某些使用者的一般名稱`DbContext`-建立案例。</span><span class="sxs-lookup"><span data-stu-id="884aa-137">This interface had a very general name which mislead some users to try re-using it for other `DbContext`-creating scenarios.</span></span> <span data-ttu-id="884aa-138">當 EF 工具，則嘗試使用它們的實作設計階段和導致命令，例如它們`Update-Database`或`dotnet ef database update`失敗。</span><span class="sxs-lookup"><span data-stu-id="884aa-138">They were caught off guard when the EF Tools then tried to use their implementation at design-time and caused commands like `Update-Database` or `dotnet ef database update` to fail.</span></span>

<span data-ttu-id="884aa-139">為了傳達這個介面的強式設計階段語意，我們已經重新命名以`IDesignTimeDbContextFactory<TContext>`。</span><span class="sxs-lookup"><span data-stu-id="884aa-139">In order to communicate the strong design-time semantics of this interface, we have renamed it to `IDesignTimeDbContextFactory<TContext>`.</span></span>

<span data-ttu-id="884aa-140">2.0 版`IDbContextFactory<TContext>`仍然存在，但已標記為過時。</span><span class="sxs-lookup"><span data-stu-id="884aa-140">For the 2.0 release the `IDbContextFactory<TContext>` still exists but is marked as obsolete.</span></span>

### <a name="dbcontextfactoryoptions-removed"></a><span data-ttu-id="884aa-141">DbContextFactoryOptions 移除</span><span class="sxs-lookup"><span data-stu-id="884aa-141">DbContextFactoryOptions removed</span></span>

<span data-ttu-id="884aa-142">由於 ASP.NET Core 2.0 變更上面所述的情況下，我們發現`DbContextFactoryOptions`已不再需要針對新`IDesignTimeDbContextFactory<TContext>`介面。</span><span class="sxs-lookup"><span data-stu-id="884aa-142">Because of the ASP.NET Core 2.0 changes described above, we found that `DbContextFactoryOptions` was no longer needed on the new `IDesignTimeDbContextFactory<TContext>` interface.</span></span> <span data-ttu-id="884aa-143">以下是您應該改為使用替代項目。</span><span class="sxs-lookup"><span data-stu-id="884aa-143">Here are the alternatives you should be using instead.</span></span>

<span data-ttu-id="884aa-144">DbContextFactoryOptions</span><span class="sxs-lookup"><span data-stu-id="884aa-144">DbContextFactoryOptions</span></span> | <span data-ttu-id="884aa-145">替代函式</span><span class="sxs-lookup"><span data-stu-id="884aa-145">Alternative</span></span>
--- | ---
<span data-ttu-id="884aa-146">ApplicationBasePath</span><span class="sxs-lookup"><span data-stu-id="884aa-146">ApplicationBasePath</span></span> | <span data-ttu-id="884aa-147">AppContext.BaseDirectory</span><span class="sxs-lookup"><span data-stu-id="884aa-147">AppContext.BaseDirectory</span></span>
<span data-ttu-id="884aa-148">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="884aa-148">ContentRootPath</span></span> | <span data-ttu-id="884aa-149">Directory.GetCurrentDirectory()</span><span class="sxs-lookup"><span data-stu-id="884aa-149">Directory.GetCurrentDirectory()</span></span>
<span data-ttu-id="884aa-150">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="884aa-150">EnvironmentName</span></span> | <span data-ttu-id="884aa-151">Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT")</span><span class="sxs-lookup"><span data-stu-id="884aa-151">Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT")</span></span>

### <a name="design-time-working-directory-changed"></a><span data-ttu-id="884aa-152">變更設計階段工作目錄</span><span class="sxs-lookup"><span data-stu-id="884aa-152">Design-time working directory changed</span></span>

<span data-ttu-id="884aa-153">ASP.NET Core 2.0 變更也會需要所使用的工作目錄`dotnet ef`能配合執行您的應用程式時，Visual Studio 所用的工作目錄。</span><span class="sxs-lookup"><span data-stu-id="884aa-153">The ASP.NET Core 2.0 changes also required the working directory used by `dotnet ef` to align with the working directory used by Visual Studio when running your application.</span></span> <span data-ttu-id="884aa-154">這一個可觀察的副作用是該 SQLite 檔案名稱現在是相對於專案目錄，而非輸出目錄像以往一樣。</span><span class="sxs-lookup"><span data-stu-id="884aa-154">One observable side effect of this is that SQLite filenames are now relative to the project directory and not the output directory like they used to be.</span></span>

### <a name="ef-core-20-requires-a-20-database-provider"></a><span data-ttu-id="884aa-155">EF 核心 2.0 需要 2.0 資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="884aa-155">EF Core 2.0 requires a 2.0 database provider</span></span>

<span data-ttu-id="884aa-156">EF 核心 2.0 我們進行了許多簡單化和增強功能的方式資料庫提供者中運作。</span><span class="sxs-lookup"><span data-stu-id="884aa-156">For EF Core 2.0 we have made many simplifications and improvements in the way database providers work.</span></span> <span data-ttu-id="884aa-157">這表示 EF 核心 2.0 1.0.x 和 1.1.x 提供者將無法運作。</span><span class="sxs-lookup"><span data-stu-id="884aa-157">This means that 1.0.x and 1.1.x providers will not work with EF Core 2.0.</span></span>

<span data-ttu-id="884aa-158">在 SQL Server 和 SQLite 提供者所隨附的 EF 小組和 2.0 版則會一部分在 2.0 版。</span><span class="sxs-lookup"><span data-stu-id="884aa-158">The SQL Server and SQLite providers are shipped by the EF team and 2.0 versions will be available as part of the 2.0 release.</span></span> <span data-ttu-id="884aa-159">開放原始碼協力廠商提供者[SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)， [PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)，和[MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) 2.0 正在更新。</span><span class="sxs-lookup"><span data-stu-id="884aa-159">The open-source third party providers for [SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact), [PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), and [MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) are being updated for 2.0.</span></span> <span data-ttu-id="884aa-160">對於所有其他的提供者，請連絡提供者寫入器。</span><span class="sxs-lookup"><span data-stu-id="884aa-160">For all other providers, please contact the provider writer.</span></span>

### <a name="logging-and-diagnostics-events-have-changed"></a><span data-ttu-id="884aa-161">記錄和診斷事件已變更</span><span class="sxs-lookup"><span data-stu-id="884aa-161">Logging and Diagnostics events have changed</span></span>

<span data-ttu-id="884aa-162">注意： 這些變更應該不會影響大部分的應用程式程式碼。</span><span class="sxs-lookup"><span data-stu-id="884aa-162">Note: these changes should not impact most application code.</span></span>

<span data-ttu-id="884aa-163">若要傳送之訊息的事件識別碼[ILogger](https://github.com/aspnet/Logging/blob/dev/src/Microsoft.Extensions.Logging.Abstractions/ILogger.cs) 2.0 中已變更。</span><span class="sxs-lookup"><span data-stu-id="884aa-163">The event IDs for messages sent to an [ILogger](https://github.com/aspnet/Logging/blob/dev/src/Microsoft.Extensions.Logging.Abstractions/ILogger.cs) have changed in 2.0.</span></span> <span data-ttu-id="884aa-164">在 EF Core 程式碼中，事件識別碼現在是唯一的。</span><span class="sxs-lookup"><span data-stu-id="884aa-164">The event IDs are now unique across EF Core code.</span></span> <span data-ttu-id="884aa-165">例如，這些訊息現在也遵循 MVC 所使用結構化記錄的標準模式。</span><span class="sxs-lookup"><span data-stu-id="884aa-165">These messages now also follow the standard pattern for structured logging used by, for example, MVC.</span></span>

<span data-ttu-id="884aa-166">記錄器類別也已經變更。</span><span class="sxs-lookup"><span data-stu-id="884aa-166">Logger categories have also changed.</span></span> <span data-ttu-id="884aa-167">現在已有一組類別可透過 [DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/DbLoggerCategory.cs) 進行存取。</span><span class="sxs-lookup"><span data-stu-id="884aa-167">There is now a well-known set of categories accessed through [DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/DbLoggerCategory.cs).</span></span>

<span data-ttu-id="884aa-168">[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)事件現在會使用相同的事件識別碼名稱為對應`ILogger`訊息。</span><span class="sxs-lookup"><span data-stu-id="884aa-168">[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) events now use the same event ID names as the corresponding `ILogger` messages.</span></span> <span data-ttu-id="884aa-169">事件裝載是所有名義型別衍生自[EventData](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Diagnostics/EventData.cs)。</span><span class="sxs-lookup"><span data-stu-id="884aa-169">The event payloads are all nominal types derived from [EventData](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Diagnostics/EventData.cs).</span></span>

<span data-ttu-id="884aa-170">事件識別碼、 內容類型和類別目錄會記載於[CoreEventId](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Diagnostics/CoreEventId.cs)和[RelationalEventId](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore.Relational/Diagnostics/RelationalEventId.cs)類別。</span><span class="sxs-lookup"><span data-stu-id="884aa-170">Event IDs, payload types, and categories are documented in the [CoreEventId](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Diagnostics/CoreEventId.cs) and the [RelationalEventId](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore.Relational/Diagnostics/RelationalEventId.cs) classes.</span></span>

<span data-ttu-id="884aa-171">識別碼也已從 Microsoft.EntityFrameworkCore.Infraestructure 轉換成新的 Microsoft.EntityFrameworkCore.Diagnostics 命名空間。</span><span class="sxs-lookup"><span data-stu-id="884aa-171">IDs have also moved from Microsoft.EntityFrameworkCore.Infraestructure to the new Microsoft.EntityFrameworkCore.Diagnostics namespace.</span></span>

### <a name="ef-core-relational-metadata-api-changes"></a><span data-ttu-id="884aa-172">EF 核心關聯式中繼資料 API 變更</span><span class="sxs-lookup"><span data-stu-id="884aa-172">EF Core relational metadata API changes</span></span>

<span data-ttu-id="884aa-173">EF Core 2.0 現在會為使用的每個不同提供者建置不同的 [IModel](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IModel.cs)。</span><span class="sxs-lookup"><span data-stu-id="884aa-173">EF Core 2.0 will now build a different [IModel](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IModel.cs) for each different provider being used.</span></span> <span data-ttu-id="884aa-174">應用程式通常可以看到這項作業。</span><span class="sxs-lookup"><span data-stu-id="884aa-174">This is usually transparent to the application.</span></span> <span data-ttu-id="884aa-175">這已加速簡化較低階中繼資料 API；因此，任何對_一般關聯式中繼資料概念_的存取一律是透過 `.Relational` 呼叫來進行，而非 `.SqlServer`、`.Sqlite` 等等。例如，1.1.x 如下的程式碼：</span><span class="sxs-lookup"><span data-stu-id="884aa-175">This has facilitated a simplification of lower-level metadata APIs such that any access to _common relational metadata concepts_ is always made through a call to `.Relational` instead of `.SqlServer`, `.Sqlite`, etc. For example, 1.1.x code like this:</span></span>

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).SqlServer().TableName;
```

<span data-ttu-id="884aa-176">現在應該撰寫如下：</span><span class="sxs-lookup"><span data-stu-id="884aa-176">Should now be written like this:</span></span>

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).Relational().TableName;
```

<span data-ttu-id="884aa-177">而不是使用類似的方法`ForSqlServerToTable`，擴充方法現在已可供條件式根據撰寫程式碼中使用目前的提供者。</span><span class="sxs-lookup"><span data-stu-id="884aa-177">Instead of using methods like `ForSqlServerToTable`, extension methods are now available to write conditional code based on the current provider in use.</span></span> <span data-ttu-id="884aa-178">例如: </span><span class="sxs-lookup"><span data-stu-id="884aa-178">For example:</span></span>

```C#
modelBuilder.Entity<User>().ToTable(
    Database.IsSqlServer() ? "SqlServerName" : "OtherName");
```

<span data-ttu-id="884aa-179">請注意，這項變更只適用於應用程式開發介面/中繼資料定義的_所有_關聯式的提供者。</span><span class="sxs-lookup"><span data-stu-id="884aa-179">Note that this change only applies to APIs/metadata that is defined for _all_ relational providers.</span></span> <span data-ttu-id="884aa-180">應用程式開發介面和中繼資料保持不變時特有的單一提供者。</span><span class="sxs-lookup"><span data-stu-id="884aa-180">The API and metadata remains the same when it is specific to only a single provider.</span></span> <span data-ttu-id="884aa-181">例如，叢集的索引是特定 SQL Sever，因此`ForSqlServerIsClustered`和`.SqlServer().IsClustered()`仍可使用。</span><span class="sxs-lookup"><span data-stu-id="884aa-181">For example, clustered indexes are specific to SQL Sever, so `ForSqlServerIsClustered` and  `.SqlServer().IsClustered()` must still be used.</span></span>

### <a name="dont-take-control-of-the-ef-service-provider"></a><span data-ttu-id="884aa-182">不會控制 EF 服務提供者</span><span class="sxs-lookup"><span data-stu-id="884aa-182">Don’t take control of the EF service provider</span></span>

<span data-ttu-id="884aa-183">EF 核心會使用內部`IServiceProvider`（也就是相依性插入容器） 的內部實作。</span><span class="sxs-lookup"><span data-stu-id="884aa-183">EF Core uses an internal `IServiceProvider` (i.e. a dependency injection container) for its internal implementation.</span></span> <span data-ttu-id="884aa-184">應用程式應該允許建立和管理在特殊情況下除外此提供者的 EF 核心。</span><span class="sxs-lookup"><span data-stu-id="884aa-184">Applications should allow EF Core to create and manage this provider except in special cases.</span></span> <span data-ttu-id="884aa-185">強烈建議您移除的任何呼叫`UseInternalServiceProvider`。</span><span class="sxs-lookup"><span data-stu-id="884aa-185">Strongly consider removing any calls to `UseInternalServiceProvider`.</span></span> <span data-ttu-id="884aa-186">如果應用程式需要呼叫`UseInternalServiceProvider`，請考慮[提出問題](https://github.com/aspnet/EntityFramework/Issues)讓我們可以調查其他方式來處理您的案例。</span><span class="sxs-lookup"><span data-stu-id="884aa-186">If an application does need to call `UseInternalServiceProvider`, then please consider [filing an issue](https://github.com/aspnet/EntityFramework/Issues) so we can investigate other ways to handle your scenario.</span></span>

<span data-ttu-id="884aa-187">呼叫`AddEntityFramework`， `AddEntityFrameworkSqlServer`，除非，應用程式碼不需要等`UseInternalServiceProvider`也稱為。</span><span class="sxs-lookup"><span data-stu-id="884aa-187">Calling `AddEntityFramework`, `AddEntityFrameworkSqlServer`, etc. is not required by application code unless `UseInternalServiceProvider` is also called.</span></span> <span data-ttu-id="884aa-188">移除任何現有的呼叫`AddEntityFramework`或`AddEntityFrameworkSqlServer`等等`AddDbContext`應該仍可用於相同的方式與之前。</span><span class="sxs-lookup"><span data-stu-id="884aa-188">Remove any existing calls to `AddEntityFramework` or `AddEntityFrameworkSqlServer`, etc. `AddDbContext` should still be used in the same way as before.</span></span>

### <a name="in-memory-databases-must-be-named"></a><span data-ttu-id="884aa-189">記憶體中資料庫必須命名為</span><span class="sxs-lookup"><span data-stu-id="884aa-189">In-memory databases must be named</span></span>

<span data-ttu-id="884aa-190">已移除全域未命名的記憶體中資料庫，而是必須命名為記憶體中的所有資料庫。</span><span class="sxs-lookup"><span data-stu-id="884aa-190">The global unnamed in-memory database has been removed and instead all in-memory databases must be named.</span></span> <span data-ttu-id="884aa-191">例如: </span><span class="sxs-lookup"><span data-stu-id="884aa-191">For example:</span></span>

``` csharp
optionsBuilder.UseInMemoryDatabase("MyDatabase");
```

<span data-ttu-id="884aa-192">這會建立/使用名稱為"MyDatabase"的資料庫。</span><span class="sxs-lookup"><span data-stu-id="884aa-192">This creates/uses a database with the name “MyDatabase”.</span></span> <span data-ttu-id="884aa-193">如果`UseInMemoryDatabase`會再次呼叫相同的名稱，與相同的記憶體中資料庫則會使用，讓它可以由多個內容執行個體共用。</span><span class="sxs-lookup"><span data-stu-id="884aa-193">If `UseInMemoryDatabase` is called again with the same name, then the same in-memory database will be used, allowing it to be shared by multiple context instances.</span></span>

### <a name="read-only-api-changes"></a><span data-ttu-id="884aa-194">唯讀應用程式開發介面變更</span><span class="sxs-lookup"><span data-stu-id="884aa-194">Read-only API changes</span></span>

<span data-ttu-id="884aa-195">`IsReadOnlyBeforeSave``IsReadOnlyAferSave`，和`IsStoreGeneratedAlways`認定為過時並取代[BeforeSaveBehavior](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IProperty.cs#L39)和[AfterSaveBehavior](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IProperty.cs#L55)。</span><span class="sxs-lookup"><span data-stu-id="884aa-195">`IsReadOnlyBeforeSave`, `IsReadOnlyAferSave`, and `IsStoreGeneratedAlways` have been obsoleted and replaced with [BeforeSaveBehavior](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IProperty.cs#L39) and [AfterSaveBehavior](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IProperty.cs#L55).</span></span> <span data-ttu-id="884aa-196">這些行為套用至任何屬性 （不只由存放區產生的屬性），並判斷插入資料庫的資料列時如何使用屬性的值 (`BeforeSaveBehavior`) 或更新現有資料庫的資料列時 (`AfterSaveBehavior`)。</span><span class="sxs-lookup"><span data-stu-id="884aa-196">These behaviors apply to any property (not only store-generated properties) and determine how the value of the property should be used when inserting into a database row (`BeforeSaveBehavior`) or when updating an existing database row (`AfterSaveBehavior`).</span></span>

<span data-ttu-id="884aa-197">屬性標記為[ValueGenerated.OnAddOrUpdate](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/ValueGenerated.cs) （例如，用於計算資料行） 將會依預設忽略目前的屬性上設定任何值。</span><span class="sxs-lookup"><span data-stu-id="884aa-197">Properties marked as [ValueGenerated.OnAddOrUpdate](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/ValueGenerated.cs) (e.g. for computed columns) will by default ignore any value currently set on the property.</span></span> <span data-ttu-id="884aa-198">這表示存放區產生的值一律取得不論是否已設定或修改追蹤的實體上的任何值。</span><span class="sxs-lookup"><span data-stu-id="884aa-198">This means that a store-generated value will always be obtained regardless of whether any value has been set or modified on the tracked entity.</span></span> <span data-ttu-id="884aa-199">可以變更此設定不同`Before\AfterSaveBehavior`。</span><span class="sxs-lookup"><span data-stu-id="884aa-199">This can be changed by setting a different `Before\AfterSaveBehavior`.</span></span>

### <a name="new-clientsetnull-delete-behavior"></a><span data-ttu-id="884aa-200">新的 ClientSetNull 刪除行為</span><span class="sxs-lookup"><span data-stu-id="884aa-200">New ClientSetNull delete behavior</span></span>

<span data-ttu-id="884aa-201">在舊版中， [DeleteBehavior.Restrict](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/DeleteBehavior.cs)有實體行為受到內容追蹤多個關閉相符`SetNull`語意。</span><span class="sxs-lookup"><span data-stu-id="884aa-201">In previous releases, [DeleteBehavior.Restrict](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/DeleteBehavior.cs) had a behavior for entities tracked by the context that more closed matched `SetNull` semantics.</span></span> <span data-ttu-id="884aa-202">在 EF 核心 2.0 中，新`ClientSetNull`為選擇性的關聯性的預設值已經導入行為。</span><span class="sxs-lookup"><span data-stu-id="884aa-202">In EF Core 2.0, a new `ClientSetNull` behavior has been introduced as the default for optional relationships.</span></span> <span data-ttu-id="884aa-203">這種行為有`SetNull`追蹤實體的語意和`Restrict`建立使用 EF 核心資料庫的行為。</span><span class="sxs-lookup"><span data-stu-id="884aa-203">This behavior has `SetNull` semantics for tracked entities and `Restrict` behavior for databases created using EF Core.</span></span> <span data-ttu-id="884aa-204">在我們的經驗，這些是追蹤的實體和資料庫必須是/實用的行為。</span><span class="sxs-lookup"><span data-stu-id="884aa-204">In our experience, these are the most expected/useful behaviors for tracked entities and the database.</span></span> <span data-ttu-id="884aa-205">`DeleteBehavior.Restrict`現在接受一個選擇性的關聯性設定時的追蹤實體。</span><span class="sxs-lookup"><span data-stu-id="884aa-205">`DeleteBehavior.Restrict` is now honored for tracked entities when set for optional relationships.</span></span>

### <a name="provider-design-time-packages-removed"></a><span data-ttu-id="884aa-206">提供者的設計階段套件移除</span><span class="sxs-lookup"><span data-stu-id="884aa-206">Provider design-time packages removed</span></span>

<span data-ttu-id="884aa-207">`Microsoft.EntityFrameworkCore.Relational.Design`已移除套件。</span><span class="sxs-lookup"><span data-stu-id="884aa-207">The `Microsoft.EntityFrameworkCore.Relational.Design` package has been removed.</span></span> <span data-ttu-id="884aa-208">它的內容已合併到`Microsoft.EntityFrameworkCore.Relational`和`Microsoft.EntityFrameworkCore.Design`。</span><span class="sxs-lookup"><span data-stu-id="884aa-208">It's contents were consolidated into `Microsoft.EntityFrameworkCore.Relational` and `Microsoft.EntityFrameworkCore.Design`.</span></span>

<span data-ttu-id="884aa-209">這會傳播到提供者的設計階段套件。</span><span class="sxs-lookup"><span data-stu-id="884aa-209">This propagates into the provider design-time packages.</span></span> <span data-ttu-id="884aa-210">這些封裝 (`Microsoft.EntityFrameworkCore.Sqlite.Design`，`Microsoft.EntityFrameworkCore.SqlServer.Design`等等) 已移除成員和其內容合併到主要提供者的封裝。</span><span class="sxs-lookup"><span data-stu-id="884aa-210">Those packages (`Microsoft.EntityFrameworkCore.Sqlite.Design`, `Microsoft.EntityFrameworkCore.SqlServer.Design`, etc.) were removed and their contents consolidated into the main provider packages.</span></span>

<span data-ttu-id="884aa-211">若要啟用`Scaffold-DbContext`或`dotnet ef dbcontext scaffold`在 EF 核心 2.0 中，您只需要參考單一提供者封裝：</span><span class="sxs-lookup"><span data-stu-id="884aa-211">To enable `Scaffold-DbContext` or `dotnet ef dbcontext scaffold` in EF Core 2.0, you only need to reference the single provider package:</span></span>

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer"
    Version="2.0.0" />
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools"
    Version="2.0.0"
    PrivateAssets="All" />
<DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
    Version="2.0.0" />
```
