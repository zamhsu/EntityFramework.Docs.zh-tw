---
title: 安裝 Entity Framework Core - EF Core
description: Entity Framework Core 的安裝指示
author: divega
ms.date: 08/06/2017
ms.assetid: 608cc774-c570-4809-8a3e-cd2c8446b8b2
uid: core/get-started/install/index
ms.openlocfilehash: 1cc6f8f73739e61adefb375ee37a830933903470
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617995"
---
# <a name="installing-entity-framework-core"></a><span data-ttu-id="cfa58-103">安裝 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="cfa58-103">Installing Entity Framework Core</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cfa58-104">必要條件</span><span class="sxs-lookup"><span data-stu-id="cfa58-104">Prerequisites</span></span>

* <span data-ttu-id="cfa58-105">因為 EF Core 是 [.NET Standard 2.0](/dotnet/standard/net-standard) 程式庫。</span><span class="sxs-lookup"><span data-stu-id="cfa58-105">EF Core is a [.NET Standard 2.0](/dotnet/standard/net-standard) library.</span></span> <span data-ttu-id="cfa58-106">所以 EF Core 需要支援 .NET Standard 2.0 的 .NET 實作才可執行。</span><span class="sxs-lookup"><span data-stu-id="cfa58-106">So EF Core requires a .NET implementation that supports .NET Standard 2.0 to run.</span></span> <span data-ttu-id="cfa58-107">其他 .NET Standard 2.0 程式庫也可以參考 EF Core。</span><span class="sxs-lookup"><span data-stu-id="cfa58-107">EF Core can also be referenced by other .NET Standard 2.0 libraries.</span></span>

* <span data-ttu-id="cfa58-108">例如，您可以使用 EF Core 開發以 .NET Core 為目標的應用程式。</span><span class="sxs-lookup"><span data-stu-id="cfa58-108">For example, you can use EF Core to develop apps that target .NET Core.</span></span> <span data-ttu-id="cfa58-109">建置 .NET Core 應用程式需要 [.NET Core SDK](https://dotnet.microsoft.com/download)。</span><span class="sxs-lookup"><span data-stu-id="cfa58-109">Building .NET Core apps requires the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="cfa58-110">此外，您也可以使用 [Visual Studio](https://visualstudio.microsoft.com/vs)、[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac) 或 [Visual Studio Code](https://code.visualstudio.com) 等開發環境。</span><span class="sxs-lookup"><span data-stu-id="cfa58-110">Optionally, you can also use a development environment like [Visual Studio](https://visualstudio.microsoft.com/vs), [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac), or [Visual Studio Code](https://code.visualstudio.com).</span></span> <span data-ttu-id="cfa58-111">如需詳細資訊，請參閱 [.NET Core 使用者入門](/dotnet/core/get-started)。</span><span class="sxs-lookup"><span data-stu-id="cfa58-111">For more information, check [Getting Started with .NET Core](/dotnet/core/get-started).</span></span>

* <span data-ttu-id="cfa58-112">您可以與 Visual Studio 搭配使用 EF Core 來開發 Windows 上的應用程式。</span><span class="sxs-lookup"><span data-stu-id="cfa58-112">You can use EF Core to develop applications on Windows using Visual Studio.</span></span> <span data-ttu-id="cfa58-113">建議使用最新版的 [Visual Studio](https://visualstudio.microsoft.com/vs)。</span><span class="sxs-lookup"><span data-stu-id="cfa58-113">The latest version of [Visual Studio](https://visualstudio.microsoft.com/vs) is recommended.</span></span>

* <span data-ttu-id="cfa58-114">EF Core 可在其他的 .NET 實作上執行，像是 [Xamarin](https://dotnet.microsoft.com/apps/xamarin) 及 .NET Native。</span><span class="sxs-lookup"><span data-stu-id="cfa58-114">EF Core can run on other .NET implementations like [Xamarin](https://dotnet.microsoft.com/apps/xamarin) and .NET Native.</span></span> <span data-ttu-id="cfa58-115">但實踐這些實作有執行階段的限制，其可能影響 EF Core 在應用程式上的運作。</span><span class="sxs-lookup"><span data-stu-id="cfa58-115">But in practice those implementations have runtime limitations that may affect how well EF Core works on your app.</span></span> <span data-ttu-id="cfa58-116">如需詳細資訊，請參閱 [EF Core 支援的 .NET 實作](xref:core/platforms/index)。</span><span class="sxs-lookup"><span data-stu-id="cfa58-116">For more information, see [.NET implementations supported by EF Core](xref:core/platforms/index).</span></span>

* <span data-ttu-id="cfa58-117">最後，不同的資料庫提供者可能需要特定的資料庫引擎版本、.NET 實作或作業系統。</span><span class="sxs-lookup"><span data-stu-id="cfa58-117">Finally, different database providers may require specific database engine versions, .NET implementations, or operating systems.</span></span> <span data-ttu-id="cfa58-118">請確認有可用且能為您應用程式支援正確環境的 [EF Core 資料庫提供者](xref:core/providers/index)可用。</span><span class="sxs-lookup"><span data-stu-id="cfa58-118">Make sure an [EF Core database provider](xref:core/providers/index) is available that supports the right environment for your application.</span></span>

## <a name="get-the-entity-framework-core-runtime"></a><span data-ttu-id="cfa58-119">取得 Entity Framework Core 執行階段</span><span class="sxs-lookup"><span data-stu-id="cfa58-119">Get the Entity Framework Core runtime</span></span>

<span data-ttu-id="cfa58-120">若要將 EF Core 新增至應用程式，請安裝您欲使用資料庫提供者適用的 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="cfa58-120">To add EF Core to an application, install the NuGet package for the database provider you want to use.</span></span>

<span data-ttu-id="cfa58-121">若您要建置 ASP.NET Core 應用程式，就不必安裝記憶體內部及 SQL Server 提供者。</span><span class="sxs-lookup"><span data-stu-id="cfa58-121">If you're building an ASP.NET Core application, you don't need to install the in-memory and SQL Server providers.</span></span> <span data-ttu-id="cfa58-122">目前版本的 ASP.NET Core 包含這些提供者以及 EF Core 執行階段。</span><span class="sxs-lookup"><span data-stu-id="cfa58-122">Those providers are included in current versions of ASP.NET Core, alongside the EF Core runtime.</span></span>  

<span data-ttu-id="cfa58-123">若要安裝或更新 NuGet 套件，您可以使用 .NET Core 命令列介面 (CLI)、Visual Studio 套件管理員對話方塊，或是 Visual Studio 套件管理員主控台。</span><span class="sxs-lookup"><span data-stu-id="cfa58-123">To install or update NuGet packages, you can use the .NET Core command-line interface (CLI), the Visual Studio Package Manager Dialog, or the Visual Studio Package Manager Console.</span></span>

### <a name="net-core-cli"></a><span data-ttu-id="cfa58-124">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cfa58-124">.NET Core CLI</span></span>

* <span data-ttu-id="cfa58-125">請從作業系統的命令列使用以下 .NET Core CLI 命令，來安裝或更新 EF Core SQL Server 提供者：</span><span class="sxs-lookup"><span data-stu-id="cfa58-125">Use the following .NET Core CLI command from the operating system's command line to install or update the EF Core SQL Server provider:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  ```

* <span data-ttu-id="cfa58-126">您可在 `dotnet add package` 命令列使用 `-v` 修飾詞來指定特定版本。</span><span class="sxs-lookup"><span data-stu-id="cfa58-126">You can indicate a specific version in the `dotnet add package` command, using the `-v` modifier.</span></span> <span data-ttu-id="cfa58-127">例如，若要安裝 EF Core 2.2.0 套件，請將 `-v 2.2.0` 附加至命令。</span><span class="sxs-lookup"><span data-stu-id="cfa58-127">For example, to install EF Core 2.2.0 packages, append `-v 2.2.0` to the command.</span></span>

<span data-ttu-id="cfa58-128">如需詳細資訊，請參閱 [.NET Core 命令列介面 (CLI) 工具](/dotnet/core/tools/)。</span><span class="sxs-lookup"><span data-stu-id="cfa58-128">For more information, see [.NET command-line interface (CLI) tools](/dotnet/core/tools/).</span></span>

### <a name="visual-studio-nuget-package-manager-dialog"></a><span data-ttu-id="cfa58-129">Visual Studio NuGet 套件管理員對話方塊</span><span class="sxs-lookup"><span data-stu-id="cfa58-129">Visual Studio NuGet Package Manager Dialog</span></span>

* <span data-ttu-id="cfa58-130">從 Visual Studio 功能表選取 [專案] > [管理 NuGet 套件]\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="cfa58-130">From the Visual Studio menu, select **Project > Manage NuGet Packages**</span></span>

* <span data-ttu-id="cfa58-131">按一下 [瀏覽]\*\*\*\* 或 [更新]\*\*\*\* 索引標籤</span><span class="sxs-lookup"><span data-stu-id="cfa58-131">Click on the **Browse** or the **Updates** tab</span></span>

* <span data-ttu-id="cfa58-132">若要安裝或更新 SQL Server 提供者，請選取 `Microsoft.EntityFrameworkCore.SqlServer` 套件並確認。</span><span class="sxs-lookup"><span data-stu-id="cfa58-132">To install or update the SQL Server provider, select the `Microsoft.EntityFrameworkCore.SqlServer` package, and confirm.</span></span>

<span data-ttu-id="cfa58-133">如需詳細資訊，請參閱 [NuGet 套件管理員對話方塊](/nuget/tools/package-manager-ui)。</span><span class="sxs-lookup"><span data-stu-id="cfa58-133">For more information, see [NuGet Package Manager Dialog](/nuget/tools/package-manager-ui).</span></span>

### <a name="visual-studio-nuget-package-manager-console"></a><span data-ttu-id="cfa58-134">Visual Studio NuGet 套件管理員主控台</span><span class="sxs-lookup"><span data-stu-id="cfa58-134">Visual Studio NuGet Package Manager Console</span></span>

* <span data-ttu-id="cfa58-135">從 Visual Studio 功能表選取 [工具] > [NuGet 套件管理員] > [套件管理員主控台]\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="cfa58-135">From the Visual Studio menu, select **Tools > NuGet Package Manager > Package Manager Console**</span></span>

* <span data-ttu-id="cfa58-136">若要安裝 SQL Server 提供者，請在套件管理員主控台中執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="cfa58-136">To install the SQL Server provider, run the following command in the Package Manager Console:</span></span>

  ``` PowerShell  
  Install-Package Microsoft.EntityFrameworkCore.SqlServer
  ```

* <span data-ttu-id="cfa58-137">若要更新提供者，請使用 `Update-Package` 命令。</span><span class="sxs-lookup"><span data-stu-id="cfa58-137">To update the provider, use the `Update-Package` command.</span></span>

* <span data-ttu-id="cfa58-138">若要指定特定版本，請使用 `-Version` 修飾詞。</span><span class="sxs-lookup"><span data-stu-id="cfa58-138">To specify a specific version, use the `-Version` modifier.</span></span> <span data-ttu-id="cfa58-139">例如，若要安裝 EF Core 2.2.0 套件，請將 `-Version 2.2.0` 附加至命令</span><span class="sxs-lookup"><span data-stu-id="cfa58-139">For example, to install EF Core 2.2.0 packages, append `-Version 2.2.0` to the commands</span></span>

<span data-ttu-id="cfa58-140">如需詳細資訊，請參閱[套件管理員主控台](/nuget/tools/package-manager-console)。</span><span class="sxs-lookup"><span data-stu-id="cfa58-140">For more information, see [Package Manager Console](/nuget/tools/package-manager-console).</span></span>

## <a name="get-the-entity-framework-core-tools"></a><span data-ttu-id="cfa58-141">取得 Entity Framework Core 工具</span><span class="sxs-lookup"><span data-stu-id="cfa58-141">Get the Entity Framework Core tools</span></span>

<span data-ttu-id="cfa58-142">您可以安裝工具以處理專案中與 EF Core 相關的工作，像是建立和套用資料庫移轉，或依據現有的資料庫建立 EF Core 模型。</span><span class="sxs-lookup"><span data-stu-id="cfa58-142">You can install tools to carry out EF Core-related tasks in your project, like creating and applying database migrations, or creating an EF Core model based on an existing database.</span></span>

<span data-ttu-id="cfa58-143">有兩組工具可用：</span><span class="sxs-lookup"><span data-stu-id="cfa58-143">Two sets of tools are available:</span></span>

* <span data-ttu-id="cfa58-144">[.NET Core 命令列介面 (CLI) 工具](xref:core/miscellaneous/cli/dotnet)可在 Windows、Linux 或 macOS 上使用。</span><span class="sxs-lookup"><span data-stu-id="cfa58-144">The [.NET Core command-line interface (CLI) tools](xref:core/miscellaneous/cli/dotnet) can be used on Windows, Linux, or macOS.</span></span> <span data-ttu-id="cfa58-145">這些命令會以 `dotnet ef` 開頭。</span><span class="sxs-lookup"><span data-stu-id="cfa58-145">These commands begin with `dotnet ef`.</span></span>

* <span data-ttu-id="cfa58-146">[套件管理員主控台 (PMC) 工具](xref:core/miscellaneous/cli/powershell)可在 Windows 上的 Visual Studio 中執行。</span><span class="sxs-lookup"><span data-stu-id="cfa58-146">The [Package Manager Console (PMC) tools](xref:core/miscellaneous/cli/powershell) run in Visual Studio on Windows.</span></span> <span data-ttu-id="cfa58-147">這些命令會以動詞開頭，例如 `Add-Migration` 和 `Update-Database`。</span><span class="sxs-lookup"><span data-stu-id="cfa58-147">These commands start with a verb, for example `Add-Migration`, `Update-Database`.</span></span>

<span data-ttu-id="cfa58-148">雖然您也可從套件管理員主控台使用 `dotnet ef` 命令，但在使用 Visual Studio 時，建議使用套件管理員主控台工具：</span><span class="sxs-lookup"><span data-stu-id="cfa58-148">Although you can also use the `dotnet ef` commands from the Package Manager Console, it's recommended to use the Package Manager Console tools when you're using Visual Studio:</span></span>

* <span data-ttu-id="cfa58-149">它們會自動使用 Visual Studio 中 PMC 內目前的所選專案，而不必手動切換目錄。</span><span class="sxs-lookup"><span data-stu-id="cfa58-149">They automatically work with the current project selected in the PMC in Visual Studio, without requiring manually switching directories.</span></span>  

* <span data-ttu-id="cfa58-150">在命令完成之後，它們會在 Visual Studio 中自動開啟命令所產生的檔案。</span><span class="sxs-lookup"><span data-stu-id="cfa58-150">They automatically open files generated by the commands in Visual Studio after the command is completed.</span></span>

<a name="cli"></a>

### <a name="get-the-net-core-cli-tools"></a><span data-ttu-id="cfa58-151">取得 .NET Core CLI 工具</span><span class="sxs-lookup"><span data-stu-id="cfa58-151">Get the .NET Core CLI tools</span></span>

<span data-ttu-id="cfa58-152">.NET Core CLI 工具需要先前在[必要條件](#prerequisites)中提到的 .NET Core SDK。</span><span class="sxs-lookup"><span data-stu-id="cfa58-152">.NET Core CLI tools require the .NET Core SDK, mentioned earlier in [Prerequisites](#prerequisites).</span></span>

<span data-ttu-id="cfa58-153">目前版本的 .NET Core SDK 雖包含 `dotnet ef` 命令，但您必須安裝 `Microsoft.EntityFrameworkCore.Design` 套件才能啟用特殊專案上的命令：</span><span class="sxs-lookup"><span data-stu-id="cfa58-153">The `dotnet ef` commands are included in current versions of the .NET Core SDK, but to enable the commands on a specific project, you have to install the `Microsoft.EntityFrameworkCore.Design` package:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Design
```

> [!IMPORTANT]
> <span data-ttu-id="cfa58-154">一律使用符合執行階段套件主要版本的工具套件版本。</span><span class="sxs-lookup"><span data-stu-id="cfa58-154">Always use the version of the tools package that matches the major version of the runtime packages.</span></span>

### <a name="get-the-package-manager-console-tools"></a><span data-ttu-id="cfa58-155">取得套件管理員主控台套件</span><span class="sxs-lookup"><span data-stu-id="cfa58-155">Get the Package Manager Console tools</span></span>

<span data-ttu-id="cfa58-156">若要取得 EF Core 的套件管理員主控台工具，請安裝 `Microsoft.EntityFrameworkCore.Tools` 套件。</span><span class="sxs-lookup"><span data-stu-id="cfa58-156">To get the Package Manager Console tools for EF Core, install the `Microsoft.EntityFrameworkCore.Tools` package.</span></span> <span data-ttu-id="cfa58-157">例如，從 Visual Studio 安裝：</span><span class="sxs-lookup"><span data-stu-id="cfa58-157">For example, from Visual Studio:</span></span>

``` PowerShell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="cfa58-158">若為 ASP.NET Core 應用程式，則已自動包含此套件。</span><span class="sxs-lookup"><span data-stu-id="cfa58-158">For ASP.NET Core apps, this package is included automatically.</span></span>

## <a name="upgrading-to-the-latest-ef-core"></a><span data-ttu-id="cfa58-159">升級為最新的 EF Core</span><span class="sxs-lookup"><span data-stu-id="cfa58-159">Upgrading to the latest EF Core</span></span>

* <span data-ttu-id="cfa58-160">每當我們發行新版本的 EF Core，同時也會發行屬於 EF Core 專案一部分的新版本提供者，像是 Microsoft.EntityFrameworkCore.SqlServer、Microsoft.EntityFrameworkCore.Sqlite 和 Microsoft.EntityFrameworkCore.InMemory。</span><span class="sxs-lookup"><span data-stu-id="cfa58-160">Any time we release a new version of EF Core, we also release a new version of the providers that are part of the EF Core project, like Microsoft.EntityFrameworkCore.SqlServer, Microsoft.EntityFrameworkCore.Sqlite, and Microsoft.EntityFrameworkCore.InMemory.</span></span> <span data-ttu-id="cfa58-161">您可以僅升級為新版本的提供者，來取得所有改善。</span><span class="sxs-lookup"><span data-stu-id="cfa58-161">You can just upgrade to the new version of the provider to get all the improvements.</span></span>

* <span data-ttu-id="cfa58-162">EF Core、SQL Server 以及記憶體內部提供者都包含於目前版本的 ASP.NET Core 中。</span><span class="sxs-lookup"><span data-stu-id="cfa58-162">EF Core, together with the SQL Server and the in-memory providers are included in current versions of ASP.NET Core.</span></span> <span data-ttu-id="cfa58-163">若要將現有的 ASP.NET Core 應用程式升級為 EF Core 的較新版本，請持續升級 ASP.NET Core 的版本。</span><span class="sxs-lookup"><span data-stu-id="cfa58-163">To upgrade an existing ASP.NET Core application to a newer version of EF Core, always upgrade the version of ASP.NET Core.</span></span>

* <span data-ttu-id="cfa58-164">如果您需要更新的應用程式是使用協力廠商資料庫提供者，則請務必檢查與您要使用之 EF Core 版本相容的提供者更新。</span><span class="sxs-lookup"><span data-stu-id="cfa58-164">If you need to update an application that is using a third-party database provider, always check for an update of the provider that is compatible with the version of EF Core you want to use.</span></span> <span data-ttu-id="cfa58-165">例如，舊版的資料庫提供者與 2.0 版的 EF Core 執行階段不相容。</span><span class="sxs-lookup"><span data-stu-id="cfa58-165">For example, database providers for previous versions are not compatible with version 2.0 of the EF Core runtime.</span></span>

* <span data-ttu-id="cfa58-166">EF Core 的協力廠商提供者通常不會同時發行修補程式版本與 EF Core 執行階段。</span><span class="sxs-lookup"><span data-stu-id="cfa58-166">Third-party providers for EF Core usually don't release patch versions alongside the EF Core runtime.</span></span> <span data-ttu-id="cfa58-167">若要將使用協力廠商提供者的應用程式升級為 EF Core 的修補程式版本，您可能必須新增個別 EF Core 執行階段元件的直接參考，像是 Microsoft.EntityFrameworkCore 及 Microsoft.EntityFrameworkCore.Relational。</span><span class="sxs-lookup"><span data-stu-id="cfa58-167">To upgrade an application that uses a third-party provider to a patch version of EF Core, you may need to add a direct reference to individual EF Core runtime components, such as Microsoft.EntityFrameworkCore, and Microsoft.EntityFrameworkCore.Relational.</span></span>

* <span data-ttu-id="cfa58-168">若您要將現有的應用程式升級為 EF Core 的最新版本，則可能需要手動移除部分舊 EF Core 套件的參考：</span><span class="sxs-lookup"><span data-stu-id="cfa58-168">If you're upgrading an existing application to the latest version of EF Core, some references to older EF Core packages may need to be removed manually:</span></span>

  * <span data-ttu-id="cfa58-169">EF Core 2.0 及更新版本中不再需要或支援如 `Microsoft.EntityFrameworkCore.SqlServer.Design` 等資料庫提供者設計階段套件，但在升級其他套件時不會自動將其移除。</span><span class="sxs-lookup"><span data-stu-id="cfa58-169">Database provider design-time packages such as `Microsoft.EntityFrameworkCore.SqlServer.Design` are no longer required or supported from EF Core 2.0 and later, but aren't automatically removed when upgrading the other packages.</span></span>

  * <span data-ttu-id="cfa58-170">從 2.1 版起，.NET CLI 工具都包含在 .NET SDK 中，因此您可以從專案檔移除該套件的參考：</span><span class="sxs-lookup"><span data-stu-id="cfa58-170">The .NET CLI tools are included in the .NET SDK since version 2.1, so the reference to that package can be removed from the project file:</span></span>

    ``` xml
    <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="2.0.0" />
    ```
