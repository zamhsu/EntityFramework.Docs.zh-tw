---
title: 從舊版升級至 EF Core 2-EF Core
description: 升級至 Entity Framework Core 2.0 的指示和注意事項
author: divega
ms.date: 08/13/2017
ms.assetid: 8BD43C8C-63D9-4F3A-B954-7BC518A1B7DB
uid: core/what-is-new/ef-core-2.0/upgrade
ms.openlocfilehash: 2bd88c0c52bcef8d5a9777f2cbd546a2787d67a9
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620734"
---
# <a name="upgrading-applications-from-previous-versions-to-ef-core-20"></a><span data-ttu-id="a4276-103">將繼承應用程式升級至 EF Core 2。0</span><span class="sxs-lookup"><span data-stu-id="a4276-103">Upgrading applications from previous versions to EF Core 2.0</span></span>

<span data-ttu-id="a4276-104">我們已在2.0 中大幅調整現有 Api 和行為的機會。</span><span class="sxs-lookup"><span data-stu-id="a4276-104">We have taken the opportunity to significantly refine our existing APIs and behaviors in 2.0.</span></span> <span data-ttu-id="a4276-105">有幾項改進可能需要修改現有的應用程式程式碼，雖然我們認為對大部分的應用程式來說，影響會很低，但在大部分情況下，只需要重新編譯和基本的引導式變更來取代淘汰的 Api。</span><span class="sxs-lookup"><span data-stu-id="a4276-105">There are a few improvements that can require modifying existing application code, although we believe that for the majority of applications the impact will be low, in most cases requiring just recompilation and minimal guided changes to replace obsolete APIs.</span></span>

<span data-ttu-id="a4276-106">將現有的應用程式更新為 EF Core 2.0 可能需要：</span><span class="sxs-lookup"><span data-stu-id="a4276-106">Updating an existing application to EF Core 2.0 may require:</span></span>

1. <span data-ttu-id="a4276-107">將應用程式的目標 .NET 執行升級為支援 .NET Standard 2.0 的應用程式。</span><span class="sxs-lookup"><span data-stu-id="a4276-107">Upgrading the target .NET implementation of the application to one that supports .NET Standard 2.0.</span></span> <span data-ttu-id="a4276-108">如需詳細資料，請參閱 [支援的 .net](xref:core/platforms/index) 執行。</span><span class="sxs-lookup"><span data-stu-id="a4276-108">See [Supported .NET Implementations](xref:core/platforms/index) for more details.</span></span>

2. <span data-ttu-id="a4276-109">識別與 EF Core 2.0 相容的目標資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="a4276-109">Identify a provider for the target database which is compatible with EF Core 2.0.</span></span> <span data-ttu-id="a4276-110">請參閱下列 [EF Core 2.0 需要2.0 資料庫提供者](#ef-core-20-requires-a-20-database-provider) 。</span><span class="sxs-lookup"><span data-stu-id="a4276-110">See [EF Core 2.0 requires a 2.0 database provider](#ef-core-20-requires-a-20-database-provider) below.</span></span>

3. <span data-ttu-id="a4276-111">將 (執行時間和工具) 的所有 EF Core 套件升級為2.0。</span><span class="sxs-lookup"><span data-stu-id="a4276-111">Upgrading all the EF Core packages (runtime and tooling) to 2.0.</span></span> <span data-ttu-id="a4276-112">如需詳細資訊，請參閱 [安裝 EF Core](xref:core/get-started/install/index) 。</span><span class="sxs-lookup"><span data-stu-id="a4276-112">Refer to [Installing EF Core](xref:core/get-started/install/index) for more details.</span></span>

4. <span data-ttu-id="a4276-113">進行任何必要的程式碼變更，以補償本檔其餘部分所述的重大變更。</span><span class="sxs-lookup"><span data-stu-id="a4276-113">Make any necessary code changes to compensate for the breaking changes described in the rest of this document.</span></span>

## <a name="aspnet-core-now-includes-ef-core"></a><span data-ttu-id="a4276-114">ASP.NET Core 現在包含 EF Core</span><span class="sxs-lookup"><span data-stu-id="a4276-114">ASP.NET Core now includes EF Core</span></span>

<span data-ttu-id="a4276-115">除了協力廠商資料庫提供者之外，將目標設為 ASP.NET Core 2.0 的應用程式還可以使用 EF Core 2.0，而且沒有其他相依性。</span><span class="sxs-lookup"><span data-stu-id="a4276-115">Applications targeting ASP.NET Core 2.0 can use EF Core 2.0 without additional dependencies besides third party database providers.</span></span> <span data-ttu-id="a4276-116">不過，以舊版 ASP.NET Core 為目標的應用程式需要升級至 ASP.NET Core 2.0，才能使用 EF Core 2.0。</span><span class="sxs-lookup"><span data-stu-id="a4276-116">However, applications targeting previous versions of ASP.NET Core need to upgrade to ASP.NET Core 2.0 in order to use EF Core 2.0.</span></span> <span data-ttu-id="a4276-117">如需將 ASP.NET Core 應用程式升級至2.0 的詳細資訊，請參閱 [主題的 ASP.NET Core 檔](/aspnet/core/migration/1x-to-2x/)。</span><span class="sxs-lookup"><span data-stu-id="a4276-117">For more details on upgrading ASP.NET Core applications to 2.0 see [the ASP.NET Core documentation on the subject](/aspnet/core/migration/1x-to-2x/).</span></span>

## <a name="new-way-of-getting-application-services-in-aspnet-core"></a><span data-ttu-id="a4276-118">在 ASP.NET Core 中取得應用程式服務的新方法</span><span class="sxs-lookup"><span data-stu-id="a4276-118">New way of getting application services in ASP.NET Core</span></span>

<span data-ttu-id="a4276-119">針對 ASP.NET Core 的 web 應用程式，建議的模式已針對2.0 進行更新，而該方法中斷了1.x 中使用的設計階段邏輯 EF Core。</span><span class="sxs-lookup"><span data-stu-id="a4276-119">The recommended pattern for ASP.NET Core web applications has been updated for 2.0 in a way that broke the design-time logic EF Core used in 1.x.</span></span> <span data-ttu-id="a4276-120">先前在設計階段，EF Core 會嘗試直接叫用，以便 `Startup.ConfigureServices` 存取應用程式的服務提供者。</span><span class="sxs-lookup"><span data-stu-id="a4276-120">Previously at design-time, EF Core would try to invoke `Startup.ConfigureServices` directly in order to access the application's service provider.</span></span> <span data-ttu-id="a4276-121">在 ASP.NET Core 2.0 中，設定會在類別之外初始化 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="a4276-121">In ASP.NET Core 2.0, Configuration is initialized outside of the `Startup` class.</span></span> <span data-ttu-id="a4276-122">使用 EF Core 的應用程式通常會從設定存取其連接字串，因此 `Startup` 本身已不再足夠。</span><span class="sxs-lookup"><span data-stu-id="a4276-122">Applications using EF Core typically access their connection string from Configuration, so `Startup` by itself is no longer sufficient.</span></span> <span data-ttu-id="a4276-123">如果您升級 ASP.NET Core 1.x 應用程式，使用 EF Core 工具時，可能會收到下列錯誤。</span><span class="sxs-lookup"><span data-stu-id="a4276-123">If you upgrade an ASP.NET Core 1.x application, you may receive the following error when using the EF Core tools.</span></span>

> <span data-ttu-id="a4276-124">在 ' ApplicationCoNtext ' 上找不到無參數的函數。</span><span class="sxs-lookup"><span data-stu-id="a4276-124">No parameterless constructor was found on 'ApplicationContext'.</span></span> <span data-ttu-id="a4276-125">請在 ' ApplicationCoNtext ' 中新增無參數的函式，或在與 &lt; &gt; ' ApplicationCoNtext ' 相同的元件中新增 ' IDesignTimeDbCoNtextFactory ApplicationCoNtext ' 的實作為</span><span class="sxs-lookup"><span data-stu-id="a4276-125">Either add a parameterless constructor to 'ApplicationContext' or add an implementation of 'IDesignTimeDbContextFactory&lt;ApplicationContext&gt;' in the same assembly as 'ApplicationContext'</span></span>

<span data-ttu-id="a4276-126">ASP.NET Core 2.0 的預設範本中已加入新的設計階段勾點。</span><span class="sxs-lookup"><span data-stu-id="a4276-126">A new design-time hook has been added in ASP.NET Core 2.0's default template.</span></span> <span data-ttu-id="a4276-127">靜態 `Program.BuildWebHost` 方法可讓 EF Core 在設計階段存取應用程式的服務提供者。</span><span class="sxs-lookup"><span data-stu-id="a4276-127">The static `Program.BuildWebHost` method enables EF Core to access the application's service provider at design time.</span></span> <span data-ttu-id="a4276-128">如果您要升級 ASP.NET Core 1.x 應用程式，則必須將 `Program` 類別更新為如下所示。</span><span class="sxs-lookup"><span data-stu-id="a4276-128">If you are upgrading an ASP.NET Core 1.x application, you will need to update the `Program` class to resemble the following.</span></span>

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

<span data-ttu-id="a4276-129">強烈建議您在將應用程式更新為2.0 時採用這個新模式，因為產品功能（例如 Entity Framework Core 的遷移）必須是必要的。</span><span class="sxs-lookup"><span data-stu-id="a4276-129">The adoption of this new pattern when updating applications to 2.0 is highly recommended and is required in order for product features like Entity Framework Core Migrations to work.</span></span> <span data-ttu-id="a4276-130">另一個常見的替代方式[是*執行 \<TContext> IDesignTimeDbCoNtextFactory*](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory)。</span><span class="sxs-lookup"><span data-stu-id="a4276-130">The other common alternative is to [implement *IDesignTimeDbContextFactory\<TContext>*](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory).</span></span>

## <a name="idbcontextfactory-renamed"></a><span data-ttu-id="a4276-131">IDbCoNtextFactory 已重新命名</span><span class="sxs-lookup"><span data-stu-id="a4276-131">IDbContextFactory renamed</span></span>

<span data-ttu-id="a4276-132">為了支援各種不同的應用程式模式，並讓使用者能夠更充分掌控其 `DbContext` 在設計階段的使用方式，我們在過去提供了 `IDbContextFactory<TContext>` 介面。</span><span class="sxs-lookup"><span data-stu-id="a4276-132">In order to support diverse application patterns and give users more control over how their `DbContext` is used at design time, we have, in the past, provided the `IDbContextFactory<TContext>` interface.</span></span> <span data-ttu-id="a4276-133">在設計階段，EF Core 工具將會在您的專案中探索此介面的執行，並使用它來建立 `DbContext` 物件。</span><span class="sxs-lookup"><span data-stu-id="a4276-133">At design-time, the EF Core tools will discover implementations of this interface in your project and use it to create `DbContext` objects.</span></span>

<span data-ttu-id="a4276-134">此介面有一個非常一般的名稱，會誤導某些使用者嘗試重複使用它來進行其他 `DbContext` 建立的案例。</span><span class="sxs-lookup"><span data-stu-id="a4276-134">This interface had a very general name which mislead some users to try re-using it for other `DbContext`-creating scenarios.</span></span> <span data-ttu-id="a4276-135">當 EF Tools 接著嘗試在設計階段使用其執行，並導致命令（例如或）失敗時，就會攔截到它們 `Update-Database` `dotnet ef database update` 。</span><span class="sxs-lookup"><span data-stu-id="a4276-135">They were caught off guard when the EF Tools then tried to use their implementation at design-time and caused commands like `Update-Database` or `dotnet ef database update` to fail.</span></span>

<span data-ttu-id="a4276-136">為了傳達這個介面的強大設計階段語義，我們已將它重新命名為 `IDesignTimeDbContextFactory<TContext>` 。</span><span class="sxs-lookup"><span data-stu-id="a4276-136">In order to communicate the strong design-time semantics of this interface, we have renamed it to `IDesignTimeDbContextFactory<TContext>`.</span></span>

<span data-ttu-id="a4276-137">2.0 版本的 `IDbContextFactory<TContext>` 仍存在，但標示為已淘汰。</span><span class="sxs-lookup"><span data-stu-id="a4276-137">For the 2.0 release the `IDbContextFactory<TContext>` still exists but is marked as obsolete.</span></span>

## <a name="dbcontextfactoryoptions-removed"></a><span data-ttu-id="a4276-138">DbCoNtextFactoryOptions 已移除</span><span class="sxs-lookup"><span data-stu-id="a4276-138">DbContextFactoryOptions removed</span></span>

<span data-ttu-id="a4276-139">由於上述的 ASP.NET Core 2.0 變更，我們發現 `DbContextFactoryOptions` 新介面不再需要 `IDesignTimeDbContextFactory<TContext>` 。</span><span class="sxs-lookup"><span data-stu-id="a4276-139">Because of the ASP.NET Core 2.0 changes described above, we found that `DbContextFactoryOptions` was no longer needed on the new `IDesignTimeDbContextFactory<TContext>` interface.</span></span> <span data-ttu-id="a4276-140">以下是您應該改為使用的替代方案。</span><span class="sxs-lookup"><span data-stu-id="a4276-140">Here are the alternatives you should be using instead.</span></span>

| <span data-ttu-id="a4276-141">DbCoNtextFactoryOptions</span><span class="sxs-lookup"><span data-stu-id="a4276-141">DbContextFactoryOptions</span></span> | <span data-ttu-id="a4276-142">替代函式</span><span class="sxs-lookup"><span data-stu-id="a4276-142">Alternative</span></span>                                                  |
|:------------------------|:-------------------------------------------------------------|
| <span data-ttu-id="a4276-143">ApplicationBasePath</span><span class="sxs-lookup"><span data-stu-id="a4276-143">ApplicationBasePath</span></span>     | <span data-ttu-id="a4276-144">AppCoNtext. BaseDirectory</span><span class="sxs-lookup"><span data-stu-id="a4276-144">AppContext.BaseDirectory</span></span>                                     |
| <span data-ttu-id="a4276-145">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="a4276-145">ContentRootPath</span></span>         | <span data-ttu-id="a4276-146">GetCurrentDirectory ( # A1</span><span class="sxs-lookup"><span data-stu-id="a4276-146">Directory.GetCurrentDirectory()</span></span>                              |
| <span data-ttu-id="a4276-147">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="a4276-147">EnvironmentName</span></span>         | <span data-ttu-id="a4276-148">GetEnvironmentVariable ( "ASPNETCORE_ENVIRONMENT" ) </span><span class="sxs-lookup"><span data-stu-id="a4276-148">Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT")</span></span> |

## <a name="design-time-working-directory-changed"></a><span data-ttu-id="a4276-149">設計階段工作目錄已變更</span><span class="sxs-lookup"><span data-stu-id="a4276-149">Design-time working directory changed</span></span>

<span data-ttu-id="a4276-150">ASP.NET Core 2.0 變更也需要用 `dotnet ef` 來與執行您的應用程式時所 Visual Studio 使用的工作目錄一致的工作目錄。</span><span class="sxs-lookup"><span data-stu-id="a4276-150">The ASP.NET Core 2.0 changes also required the working directory used by `dotnet ef` to align with the working directory used by Visual Studio when running your application.</span></span> <span data-ttu-id="a4276-151">其中一個可觀察的副作用是，SQLite 檔案名現在會相對於專案目錄，而不是像過去一樣的輸出目錄。</span><span class="sxs-lookup"><span data-stu-id="a4276-151">One observable side effect of this is that SQLite filenames are now relative to the project directory and not the output directory like they used to be.</span></span>

## <a name="ef-core-20-requires-a-20-database-provider"></a><span data-ttu-id="a4276-152">EF Core 2.0 需要2.0 資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="a4276-152">EF Core 2.0 requires a 2.0 database provider</span></span>

<span data-ttu-id="a4276-153">在 EF Core 2.0 中，我們已對資料庫提供者的運作方式進行了許多簡化和改進。</span><span class="sxs-lookup"><span data-stu-id="a4276-153">For EF Core 2.0 we have made many simplifications and improvements in the way database providers work.</span></span> <span data-ttu-id="a4276-154">這表示 1.0. x 和 1.1. x 提供者將無法使用 EF Core 2.0。</span><span class="sxs-lookup"><span data-stu-id="a4276-154">This means that 1.0.x and 1.1.x providers will not work with EF Core 2.0.</span></span>

<span data-ttu-id="a4276-155">EF team 隨附的 SQL Server 和 SQLite 提供者，2.0 版將提供2.0 版的一部分。</span><span class="sxs-lookup"><span data-stu-id="a4276-155">The SQL Server and SQLite providers are shipped by the EF team and 2.0 versions will be available as part of the 2.0 release.</span></span> <span data-ttu-id="a4276-156">適用于 [SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)、 [于 postgresql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)和 [MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) 的開放原始碼協力廠商提供者正在更新2.0。</span><span class="sxs-lookup"><span data-stu-id="a4276-156">The open-source third party providers for [SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact), [PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), and [MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) are being updated for 2.0.</span></span> <span data-ttu-id="a4276-157">若為所有其他提供者，請洽詢提供者寫入器。</span><span class="sxs-lookup"><span data-stu-id="a4276-157">For all other providers, please contact the provider writer.</span></span>

## <a name="logging-and-diagnostics-events-have-changed"></a><span data-ttu-id="a4276-158">記錄和診斷事件已變更</span><span class="sxs-lookup"><span data-stu-id="a4276-158">Logging and Diagnostics events have changed</span></span>

<span data-ttu-id="a4276-159">注意：這些變更應該不會影響大部分的應用程式程式碼。</span><span class="sxs-lookup"><span data-stu-id="a4276-159">Note: these changes should not impact most application code.</span></span>

<span data-ttu-id="a4276-160">傳送至 [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) 之訊息的事件識別碼在2.0 中已變更。</span><span class="sxs-lookup"><span data-stu-id="a4276-160">The event IDs for messages sent to an [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) have changed in 2.0.</span></span> <span data-ttu-id="a4276-161">在 EF Core 程式碼中，事件識別碼現在是唯一的。</span><span class="sxs-lookup"><span data-stu-id="a4276-161">The event IDs are now unique across EF Core code.</span></span> <span data-ttu-id="a4276-162">例如，這些訊息現在也遵循 MVC 所使用結構化記錄的標準模式。</span><span class="sxs-lookup"><span data-stu-id="a4276-162">These messages now also follow the standard pattern for structured logging used by, for example, MVC.</span></span>

<span data-ttu-id="a4276-163">記錄器類別也已經變更。</span><span class="sxs-lookup"><span data-stu-id="a4276-163">Logger categories have also changed.</span></span> <span data-ttu-id="a4276-164">現在已有一組類別可透過 [DbLoggerCategory](https://github.com/aspnet/EntityFrameworkCore/blob/rel/2.0.0/src/EFCore/DbLoggerCategory.cs) 進行存取。</span><span class="sxs-lookup"><span data-stu-id="a4276-164">There is now a well-known set of categories accessed through [DbLoggerCategory](https://github.com/aspnet/EntityFrameworkCore/blob/rel/2.0.0/src/EFCore/DbLoggerCategory.cs).</span></span>

<span data-ttu-id="a4276-165">[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) 事件現在會使用相同的事件識別碼名稱作為對應的 `ILogger` 訊息。</span><span class="sxs-lookup"><span data-stu-id="a4276-165">[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) events now use the same event ID names as the corresponding `ILogger` messages.</span></span> <span data-ttu-id="a4276-166">事件承載是衍生自 [EventData](/dotnet/api/microsoft.entityframeworkcore.diagnostics.eventdata)的所有名義類型。</span><span class="sxs-lookup"><span data-stu-id="a4276-166">The event payloads are all nominal types derived from [EventData](/dotnet/api/microsoft.entityframeworkcore.diagnostics.eventdata).</span></span>

<span data-ttu-id="a4276-167">事件識別碼、承載類型和類別記錄在 [CoreEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.coreeventid) 和 [RelationalEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.relationaleventid) 類別中。</span><span class="sxs-lookup"><span data-stu-id="a4276-167">Event IDs, payload types, and categories are documented in the [CoreEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.coreeventid) and the [RelationalEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.relationaleventid) classes.</span></span>

<span data-ttu-id="a4276-168">識別碼也會從 Microsoft.entityframeworkcore 移至新的 Microsoft.entityframeworkcore 命名空間。</span><span class="sxs-lookup"><span data-stu-id="a4276-168">IDs have also moved from Microsoft.EntityFrameworkCore.Infrastructure to the new Microsoft.EntityFrameworkCore.Diagnostics namespace.</span></span>

## <a name="ef-core-relational-metadata-api-changes"></a><span data-ttu-id="a4276-169">EF Core 關聯式中繼資料 API 變更</span><span class="sxs-lookup"><span data-stu-id="a4276-169">EF Core relational metadata API changes</span></span>

<span data-ttu-id="a4276-170">EF Core 2.0 現在會為使用的每個不同提供者建置不同的 [IModel](/dotnet/api/microsoft.entityframeworkcore.metadata.imodel)。</span><span class="sxs-lookup"><span data-stu-id="a4276-170">EF Core 2.0 will now build a different [IModel](/dotnet/api/microsoft.entityframeworkcore.metadata.imodel) for each different provider being used.</span></span> <span data-ttu-id="a4276-171">應用程式通常可以看到這項作業。</span><span class="sxs-lookup"><span data-stu-id="a4276-171">This is usually transparent to the application.</span></span> <span data-ttu-id="a4276-172">這項功能簡化了較低層級中繼資料 Api 的簡化，讓 _一般關聯式中繼資料概念_ 的任何存取一律透過呼叫 `.Relational` 而非 `.SqlServer` 、等等進行 `.Sqlite` 。例如，像這樣的 1.1. x 程式碼：</span><span class="sxs-lookup"><span data-stu-id="a4276-172">This has facilitated a simplification of lower-level metadata APIs such that any access to _common relational metadata concepts_ is always made through a call to `.Relational` instead of `.SqlServer`, `.Sqlite`, etc. For example, 1.1.x code like this:</span></span>

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).SqlServer().TableName;
```

<span data-ttu-id="a4276-173">現在應該如下撰寫：</span><span class="sxs-lookup"><span data-stu-id="a4276-173">Should now be written like this:</span></span>

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).Relational().TableName;
```

<span data-ttu-id="a4276-174">`ForSqlServerToTable`擴充方法現在可以用來根據目前使用中的提供者來撰寫條件式程式碼，而不是使用類似的方法。</span><span class="sxs-lookup"><span data-stu-id="a4276-174">Instead of using methods like `ForSqlServerToTable`, extension methods are now available to write conditional code based on the current provider in use.</span></span> <span data-ttu-id="a4276-175">例如：</span><span class="sxs-lookup"><span data-stu-id="a4276-175">For example:</span></span>

```csharp
modelBuilder.Entity<User>().ToTable(
    Database.IsSqlServer() ? "SqlServerName" : "OtherName");
```

<span data-ttu-id="a4276-176">請注意，這項變更僅適用于針對 _所有_ 關聯式提供者定義的 api/中繼資料。</span><span class="sxs-lookup"><span data-stu-id="a4276-176">Note that this change only applies to APIs/metadata that is defined for _all_ relational providers.</span></span> <span data-ttu-id="a4276-177">當 API 和中繼資料僅適用于單一提供者時，它會維持不變。</span><span class="sxs-lookup"><span data-stu-id="a4276-177">The API and metadata remains the same when it is specific to only a single provider.</span></span> <span data-ttu-id="a4276-178">例如，叢集索引是 SQL server 專屬的，因此， `ForSqlServerIsClustered` 您  `.SqlServer().IsClustered()` 仍然必須使用。</span><span class="sxs-lookup"><span data-stu-id="a4276-178">For example, clustered indexes are specific to SQL Sever, so `ForSqlServerIsClustered` and  `.SqlServer().IsClustered()` must still be used.</span></span>

## <a name="dont-take-control-of-the-ef-service-provider"></a><span data-ttu-id="a4276-179">不要掌控 EF 服務提供者</span><span class="sxs-lookup"><span data-stu-id="a4276-179">Don’t take control of the EF service provider</span></span>

<span data-ttu-id="a4276-180">EF Core 使用內部 (相依性 `IServiceProvider` 插入容器) 進行內部執行。</span><span class="sxs-lookup"><span data-stu-id="a4276-180">EF Core uses an internal `IServiceProvider` (a dependency injection container) for its internal implementation.</span></span> <span data-ttu-id="a4276-181">應用程式應該允許 EF Core 建立和管理此提供者，但在特殊情況下除外。</span><span class="sxs-lookup"><span data-stu-id="a4276-181">Applications should allow EF Core to create and manage this provider except in special cases.</span></span> <span data-ttu-id="a4276-182">強烈考慮移除對的任何呼叫 `UseInternalServiceProvider` 。</span><span class="sxs-lookup"><span data-stu-id="a4276-182">Strongly consider removing any calls to `UseInternalServiceProvider`.</span></span> <span data-ttu-id="a4276-183">如果應用程式需要呼叫 `UseInternalServiceProvider` ，請考慮提出 [問題](https://github.com/dotnet/efcore/Issues) ，讓我們可以調查其他處理您案例的方式。</span><span class="sxs-lookup"><span data-stu-id="a4276-183">If an application does need to call `UseInternalServiceProvider`, then please consider [filing an issue](https://github.com/dotnet/efcore/Issues) so we can investigate other ways to handle your scenario.</span></span>

<span data-ttu-id="a4276-184">`AddEntityFramework` `AddEntityFrameworkSqlServer` 除非 `UseInternalServiceProvider` 也呼叫，否則應用程式程式碼不需要呼叫、等等。</span><span class="sxs-lookup"><span data-stu-id="a4276-184">Calling `AddEntityFramework`, `AddEntityFrameworkSqlServer`, etc. is not required by application code unless `UseInternalServiceProvider` is also called.</span></span> <span data-ttu-id="a4276-185">移除對或的任何現有呼叫 `AddEntityFramework` `AddEntityFrameworkSqlServer` 等等。 `AddDbContext` 仍應以與之前相同的方式使用。</span><span class="sxs-lookup"><span data-stu-id="a4276-185">Remove any existing calls to `AddEntityFramework` or `AddEntityFrameworkSqlServer`, etc. `AddDbContext` should still be used in the same way as before.</span></span>

## <a name="in-memory-databases-must-be-named"></a><span data-ttu-id="a4276-186">記憶體內部資料庫必須命名為</span><span class="sxs-lookup"><span data-stu-id="a4276-186">In-memory databases must be named</span></span>

<span data-ttu-id="a4276-187">全域未命名的記憶體中資料庫已移除，而不是所有記憶體中的資料庫都必須命名為。</span><span class="sxs-lookup"><span data-stu-id="a4276-187">The global unnamed in-memory database has been removed and instead all in-memory databases must be named.</span></span> <span data-ttu-id="a4276-188">例如：</span><span class="sxs-lookup"><span data-stu-id="a4276-188">For example:</span></span>

``` csharp
optionsBuilder.UseInMemoryDatabase("MyDatabase");
```

<span data-ttu-id="a4276-189">這會建立/使用名稱為 "MyDatabase" 的資料庫。</span><span class="sxs-lookup"><span data-stu-id="a4276-189">This creates/uses a database with the name “MyDatabase”.</span></span> <span data-ttu-id="a4276-190">如果 `UseInMemoryDatabase` 使用相同的名稱再次呼叫，則會使用相同的記憶體中資料庫，讓多個內容實例共用它。</span><span class="sxs-lookup"><span data-stu-id="a4276-190">If `UseInMemoryDatabase` is called again with the same name, then the same in-memory database will be used, allowing it to be shared by multiple context instances.</span></span>

## <a name="read-only-api-changes"></a><span data-ttu-id="a4276-191">唯讀 API 變更</span><span class="sxs-lookup"><span data-stu-id="a4276-191">Read-only API changes</span></span>

<span data-ttu-id="a4276-192">`IsReadOnlyBeforeSave`、 `IsReadOnlyAfterSave` 和已淘汰 `IsStoreGeneratedAlways` 並取代為 [BeforeSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.beforesavebehavior) 和 [AfterSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.aftersavebehavior)。</span><span class="sxs-lookup"><span data-stu-id="a4276-192">`IsReadOnlyBeforeSave`, `IsReadOnlyAfterSave`, and `IsStoreGeneratedAlways` have been obsoleted and replaced with [BeforeSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.beforesavebehavior) and [AfterSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.aftersavebehavior).</span></span> <span data-ttu-id="a4276-193">這些行為會套用至任何屬性 (但不只是存放區產生的屬性) ，而且會決定當插入資料庫資料列 (`BeforeSaveBehavior`) 或更新現有的資料庫資料列 () 時，應如何使用屬性的值 `AfterSaveBehavior` 。</span><span class="sxs-lookup"><span data-stu-id="a4276-193">These behaviors apply to any property (not only store-generated properties) and determine how the value of the property should be used when inserting into a database row (`BeforeSaveBehavior`) or when updating an existing database row (`AfterSaveBehavior`).</span></span>

<span data-ttu-id="a4276-194">標記為 [ValueGenerated. OnAddOrUpdate](/dotnet/api/microsoft.entityframeworkcore.metadata.valuegenerated) (例如，針對計算資料行，) 預設會忽略目前在屬性上設定的任何值。</span><span class="sxs-lookup"><span data-stu-id="a4276-194">Properties marked as [ValueGenerated.OnAddOrUpdate](/dotnet/api/microsoft.entityframeworkcore.metadata.valuegenerated) (for example, for computed columns) will by default ignore any value currently set on the property.</span></span> <span data-ttu-id="a4276-195">這表示無論是否已在追蹤的實體上設定或修改任何值，一律會取得存放區產生的值。</span><span class="sxs-lookup"><span data-stu-id="a4276-195">This means that a store-generated value will always be obtained regardless of whether any value has been set or modified on the tracked entity.</span></span> <span data-ttu-id="a4276-196">這可以藉由設定不同的方式進行變更 `Before\AfterSaveBehavior` 。</span><span class="sxs-lookup"><span data-stu-id="a4276-196">This can be changed by setting a different `Before\AfterSaveBehavior`.</span></span>

## <a name="new-clientsetnull-delete-behavior"></a><span data-ttu-id="a4276-197">新的 Deletebehavior.clientsetnull 刪除行為</span><span class="sxs-lookup"><span data-stu-id="a4276-197">New ClientSetNull delete behavior</span></span>

<span data-ttu-id="a4276-198">在先前的版本中， [deletebehavior.restrict](/dotnet/api/microsoft.entityframeworkcore.deletebehavior) 會針對內容所追蹤的實體，具有更關閉的相符 `SetNull` 語義。</span><span class="sxs-lookup"><span data-stu-id="a4276-198">In previous releases, [DeleteBehavior.Restrict](/dotnet/api/microsoft.entityframeworkcore.deletebehavior) had a behavior for entities tracked by the context that more closed matched `SetNull` semantics.</span></span> <span data-ttu-id="a4276-199">在 EF Core 2.0 中，引進了新 `ClientSetNull` 的行為做為選擇性關聯性的預設值。</span><span class="sxs-lookup"><span data-stu-id="a4276-199">In EF Core 2.0, a new `ClientSetNull` behavior has been introduced as the default for optional relationships.</span></span> <span data-ttu-id="a4276-200">此行為具有 `SetNull` 追蹤實體的語義，以及 `Restrict` 使用 EF Core 建立之資料庫的行為。</span><span class="sxs-lookup"><span data-stu-id="a4276-200">This behavior has `SetNull` semantics for tracked entities and `Restrict` behavior for databases created using EF Core.</span></span> <span data-ttu-id="a4276-201">在我們的經驗中，這些是追蹤實體和資料庫的最預期/有用行為。</span><span class="sxs-lookup"><span data-stu-id="a4276-201">In our experience, these are the most expected/useful behaviors for tracked entities and the database.</span></span> <span data-ttu-id="a4276-202">`DeleteBehavior.Restrict` 當針對選擇性關聯性設定時，現在會接受追蹤實體的。</span><span class="sxs-lookup"><span data-stu-id="a4276-202">`DeleteBehavior.Restrict` is now honored for tracked entities when set for optional relationships.</span></span>

## <a name="provider-design-time-packages-removed"></a><span data-ttu-id="a4276-203">已移除提供者設計階段套件</span><span class="sxs-lookup"><span data-stu-id="a4276-203">Provider design-time packages removed</span></span>

<span data-ttu-id="a4276-204">已 `Microsoft.EntityFrameworkCore.Relational.Design` 移除套件。</span><span class="sxs-lookup"><span data-stu-id="a4276-204">The `Microsoft.EntityFrameworkCore.Relational.Design` package has been removed.</span></span> <span data-ttu-id="a4276-205">其內容已合併到 `Microsoft.EntityFrameworkCore.Relational` 和 `Microsoft.EntityFrameworkCore.Design` 中。</span><span class="sxs-lookup"><span data-stu-id="a4276-205">It's contents were consolidated into `Microsoft.EntityFrameworkCore.Relational` and `Microsoft.EntityFrameworkCore.Design`.</span></span>

<span data-ttu-id="a4276-206">這會傳播至提供者設計階段套件。</span><span class="sxs-lookup"><span data-stu-id="a4276-206">This propagates into the provider design-time packages.</span></span> <span data-ttu-id="a4276-207">這些套件 (`Microsoft.EntityFrameworkCore.Sqlite.Design` 、 `Microsoft.EntityFrameworkCore.SqlServer.Design` 等 ) 已移除，且其內容會合並到主要提供者套件中。</span><span class="sxs-lookup"><span data-stu-id="a4276-207">Those packages (`Microsoft.EntityFrameworkCore.Sqlite.Design`, `Microsoft.EntityFrameworkCore.SqlServer.Design`, etc.) were removed and their contents consolidated into the main provider packages.</span></span>

<span data-ttu-id="a4276-208">若要 `Scaffold-DbContext` `dotnet ef dbcontext scaffold` 在 EF Core 2.0 中啟用或，您只需要參考單一提供者套件：</span><span class="sxs-lookup"><span data-stu-id="a4276-208">To enable `Scaffold-DbContext` or `dotnet ef dbcontext scaffold` in EF Core 2.0, you only need to reference the single provider package:</span></span>

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer"
    Version="2.0.0" />
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools"
    Version="2.0.0"
    PrivateAssets="All" />
<DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
    Version="2.0.0" />
```
