---
title: 安裝 Entity Framework Core
author: divega
ms.date: 08/06/2017
ms.assetid: 608cc774-c570-4809-8a3e-cd2c8446b8b2
uid: core/get-started/install/index
ms.openlocfilehash: 7831e6a54e885cf0b89ef3eef2cd81a9292df606
ms.sourcegitcommit: 0d36e8ff0892b7f034b765b15e041f375f88579a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2018
ms.locfileid: "44250318"
---
# <a name="installing-entity-framework-core"></a><span data-ttu-id="84d3b-102">安裝 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="84d3b-102">Installing Entity Framework Core</span></span>

## <a name="prerequisites"></a><span data-ttu-id="84d3b-103">必要條件</span><span class="sxs-lookup"><span data-stu-id="84d3b-103">Prerequisites</span></span>

* <span data-ttu-id="84d3b-104">若要開發以 .NET Core 2.1 為目標的應用程式，請安裝 [.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core)。</span><span class="sxs-lookup"><span data-stu-id="84d3b-104">To develop apps that target .NET Core 2.1, install [the .NET Core 2.1 SDK](https://www.microsoft.com/net/download/core).</span></span> <span data-ttu-id="84d3b-105">即便您有最新版本的 Visual Studio 2017 也必須安裝該 SDK。</span><span class="sxs-lookup"><span data-stu-id="84d3b-105">The SDK has to be installed even if you have the latest version of Visual Studio 2017.</span></span>

* <span data-ttu-id="84d3b-106">若要使用 Visual Studio 開發以 .NET Core 2.1 為目標的應用程式，請安裝 Visual Studio 2017 15.7 版或更新版本。</span><span class="sxs-lookup"><span data-stu-id="84d3b-106">To use Visual Studio for development of apps that target .NET Core 2.1, install Visual Studio 2017 version 15.7 or later.</span></span>

* <span data-ttu-id="84d3b-107">若要在 ASP.NET Core 應用程式中使用 Entity Framework 2.1，請使用 ASP.NET Core 2.1。</span><span class="sxs-lookup"><span data-stu-id="84d3b-107">To use Entity Framework 2.1 in ASP.NET Core applications, use ASP.NET Core 2.1.</span></span> <span data-ttu-id="84d3b-108">使用舊版 ASP.NET Core 的應用程式須更新至 2.1。</span><span class="sxs-lookup"><span data-stu-id="84d3b-108">Applications that use earlier versions of ASP.NET Core must be updated to 2.1.</span></span>

* <span data-ttu-id="84d3b-109">若為以 NET Framework 4.6.1 或更新版本為目標的應用程式，您可以使用 Visual Studio 2015。</span><span class="sxs-lookup"><span data-stu-id="84d3b-109">You can use Visual Studio 2015 for apps that target the .NET Framework 4.6.1 or later.</span></span> <span data-ttu-id="84d3b-110">但是您需要能感知 .NET Standard 2.0 及其相容 Framework 的 NuGet 版本。</span><span class="sxs-lookup"><span data-stu-id="84d3b-110">But you need a version of NuGet that is aware of the .NET Standard 2.0 and its compatible frameworks.</span></span> <span data-ttu-id="84d3b-111">若要在 Visual Studio 2015 中取得該項目，[請將 NuGet 用戶端升級至 3.6.0 版](https://www.nuget.org/downloads)。</span><span class="sxs-lookup"><span data-stu-id="84d3b-111">To get that in Visual Studio 2015, [upgrade the NuGet client to version 3.6.0](https://www.nuget.org/downloads).</span></span>

## <a name="get-the-entity-framework-core-runtime"></a><span data-ttu-id="84d3b-112">取得 Entity Framework Core 執行階段</span><span class="sxs-lookup"><span data-stu-id="84d3b-112">Get the Entity Framework Core runtime</span></span>

<span data-ttu-id="84d3b-113">若要將 EF Core 執行階段程式庫新增至應用程式，請安裝您欲使用資料庫提供者適用的 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="84d3b-113">To add EF Core runtime libraries to an application, install the NuGet package for the database provider you want to use.</span></span> <span data-ttu-id="84d3b-114">如需支援的提供者和其 NuGet 套件名稱的清單，請查看[資料庫提供者](../../providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="84d3b-114">For a list of supported providers and their NuGet package names, see [Database providers](../../providers/index.md).</span></span>

<span data-ttu-id="84d3b-115">若要安裝或更新 NuGet 套件，請使用 .NET Core CLI、Visual Studio 套件管理員對話方塊，或是 Visual Studio 套件管理員主控台。</span><span class="sxs-lookup"><span data-stu-id="84d3b-115">To install or update NuGet packages, use the .NET Core CLI, the Visual Studio Package Manager Dialog, or the Visual Studio Package Manager Console.</span></span>

<span data-ttu-id="84d3b-116">若為 ASP.NET Core 2.1 應用程式，則會自動包含記憶體內部和 SQL Server 提供者，因此無須個別安裝。</span><span class="sxs-lookup"><span data-stu-id="84d3b-116">For ASP.NET Core 2.1 applications, the in-memory and SQL Server providers are automatically included, so there's no need to install them separately.</span></span>

> [!TIP]  
> <span data-ttu-id="84d3b-117">如果您需要更新的應用程式是使用協力廠商資料庫提供者，則請務必檢查與您要使用之 EF Core 版本相容的提供者更新。</span><span class="sxs-lookup"><span data-stu-id="84d3b-117">If you need to update an application that is using a third-party database provider, always check for an update of the provider that is compatible with the version of EF Core you want to use.</span></span> <span data-ttu-id="84d3b-118">例如，舊版的資料庫提供者與 2.1 版的 EF Core 執行階段不相容。</span><span class="sxs-lookup"><span data-stu-id="84d3b-118">For example, database providers for previous versions are not compatible with version 2.1 of the EF Core runtime.</span></span>  

### <a name="net-core-cli"></a><span data-ttu-id="84d3b-119">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="84d3b-119">.NET Core CLI</span></span>

<span data-ttu-id="84d3b-120">下列 .NET Core CLI 命令會安裝或更新 SQL Server 提供者：</span><span class="sxs-lookup"><span data-stu-id="84d3b-120">The following .NET Core CLI command installs or updates the SQL Server provider:</span></span>

``` Console
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="84d3b-121">您可在 `dotnet add package` 命令列使用 `-v` 修飾詞來指定特定版本。</span><span class="sxs-lookup"><span data-stu-id="84d3b-121">You can indicate a specific version in the `dotnet add package` command, using the `-v` modifier.</span></span> <span data-ttu-id="84d3b-122">例如，若要安裝 EF Core 2.1.0 套件，請在命令附加 `-v 2.1.0`。</span><span class="sxs-lookup"><span data-stu-id="84d3b-122">For example, to install EF Core 2.1.0 packages, append `-v 2.1.0` to the command.</span></span>

### <a name="visual-studio-nuget-package-manager-dialog"></a><span data-ttu-id="84d3b-123">Visual Studio NuGet 套件管理員對話方塊</span><span class="sxs-lookup"><span data-stu-id="84d3b-123">Visual Studio NuGet Package Manager Dialog</span></span>

* <span data-ttu-id="84d3b-124">從功能表選取 [專案] > [管理 NuGet 套件]</span><span class="sxs-lookup"><span data-stu-id="84d3b-124">From the menu, select **Project > Manage NuGet Packages**</span></span>

* <span data-ttu-id="84d3b-125">按一下 [瀏覽] 或 [更新] 索引標籤</span><span class="sxs-lookup"><span data-stu-id="84d3b-125">Click on the **Browse** or the **Updates** tab</span></span>

* <span data-ttu-id="84d3b-126">若要安裝或更新 SQL Server 提供者，請選取 `Microsoft.EntityFrameworkCore.SqlServer` 套件並確認。</span><span class="sxs-lookup"><span data-stu-id="84d3b-126">To install or update the SQL Server provider, select the `Microsoft.EntityFrameworkCore.SqlServer` package, and confirm.</span></span>

<span data-ttu-id="84d3b-127">如需詳細資訊，請參閱 [NuGet 套件管理員對話方塊](https://docs.microsoft.com/nuget/tools/package-manager-ui)。</span><span class="sxs-lookup"><span data-stu-id="84d3b-127">For more information, see [NuGet Package Manager Dialog](https://docs.microsoft.com/nuget/tools/package-manager-ui).</span></span>

### <a name="visual-studio-nuget-package-manager-console"></a><span data-ttu-id="84d3b-128">Visual Studio NuGet 套件管理員主控台</span><span class="sxs-lookup"><span data-stu-id="84d3b-128">Visual Studio NuGet Package Manager Console</span></span>

* <span data-ttu-id="84d3b-129">從功能表選取 [工具] > [NuGet 套件管理員] > [套件管理員主控台]</span><span class="sxs-lookup"><span data-stu-id="84d3b-129">From the menu, select **Tools > NuGet Package Manager > Package Manager Console**</span></span>

* <span data-ttu-id="84d3b-130">若要安裝 SQL Server 提供者，請在套件管理員主控台中執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="84d3b-130">To install the SQL Server provider, run the following command in the Package Manager Console:</span></span>

  ``` PowerShell  
  Install-Package Microsoft.EntityFrameworkCore.SqlServer
  ```
* <span data-ttu-id="84d3b-131">若要更新提供者，請使用 `Update-Package` 命令。</span><span class="sxs-lookup"><span data-stu-id="84d3b-131">To update the provider, use the `Update-Package` command.</span></span>

* <span data-ttu-id="84d3b-132">若要指定特定版本，請使用 `-Version` 修飾詞。</span><span class="sxs-lookup"><span data-stu-id="84d3b-132">To specify a specific version, use the `-Version` modifier.</span></span> <span data-ttu-id="84d3b-133">例如，若要安裝 EF Core 2.1.0 套件，請將 `-Version 2.1.0` 附加至命令</span><span class="sxs-lookup"><span data-stu-id="84d3b-133">For example, to install EF Core 2.1.0 packages, append `-Version 2.1.0` to the commands</span></span>

<span data-ttu-id="84d3b-134">如需詳細資訊，請參閱[套件管理員主控台](https://docs.microsoft.com/nuget/tools/package-manager-console)。</span><span class="sxs-lookup"><span data-stu-id="84d3b-134">For more information, see [Package Manager Console](https://docs.microsoft.com/nuget/tools/package-manager-console).</span></span>

## <a name="get-entity-framework-core-tools"></a><span data-ttu-id="84d3b-135">取得 Entity Framework Core 工具</span><span class="sxs-lookup"><span data-stu-id="84d3b-135">Get Entity Framework Core tools</span></span>

<span data-ttu-id="84d3b-136">除了執行階段程式庫以外，您還可以安裝其他工具，在專案的設計期間中執行部分與 EF Core 相關的工作。</span><span class="sxs-lookup"><span data-stu-id="84d3b-136">Besides the runtime libraries, you can install tools that can perform some EF Core-related tasks in your project at design time.</span></span> <span data-ttu-id="84d3b-137">例如，您可以建立移轉、套用移轉，以及利用現有的資料庫為基礎建立模型。</span><span class="sxs-lookup"><span data-stu-id="84d3b-137">For example, you can create migrations, apply migrations, and create a model based on an existing database.</span></span>

<span data-ttu-id="84d3b-138">有兩組工具可用：</span><span class="sxs-lookup"><span data-stu-id="84d3b-138">Two sets of tools are available:</span></span>
* <span data-ttu-id="84d3b-139">.NET Core [命令列介面 (CLI) 工具](../../miscellaneous/cli/dotnet.md)適用於 Windows、Linux 和 macOS。</span><span class="sxs-lookup"><span data-stu-id="84d3b-139">The .NET Core [command-line interface (CLI) tools](../../miscellaneous/cli/dotnet.md) can be used on Windows, Linux, or macOS.</span></span> <span data-ttu-id="84d3b-140">這些命令會以 `dotnet ef` 開頭。</span><span class="sxs-lookup"><span data-stu-id="84d3b-140">These commands begin with `dotnet ef`.</span></span> 
* <span data-ttu-id="84d3b-141">[套件管理員主控台工具](../../miscellaneous/cli/powershell.md)可在 Windows 上的 Visual Studio 2017 中執行。</span><span class="sxs-lookup"><span data-stu-id="84d3b-141">The [Package Manager Console tools](../../miscellaneous/cli/powershell.md)  run in Visual Studio 2017 on Windows.</span></span> <span data-ttu-id="84d3b-142">這些命令會以動詞開頭，例如 `Add-Migration` 和 `Update-Database`。</span><span class="sxs-lookup"><span data-stu-id="84d3b-142">These commands start with a verb, for example `Add-Migration`, `Update-Database`.</span></span>

<span data-ttu-id="84d3b-143">雖然您可從套件管理員主控台使用 `dotnet ef` 命令，但在使用 Visual Studio 時使用套件管理員主控台工具更加方便：</span><span class="sxs-lookup"><span data-stu-id="84d3b-143">Although you can use the `dotnet ef` commands from the Package Manager Console, it's more convenient to use the Package Manager Console tools when you're using Visual Studio:</span></span>
* <span data-ttu-id="84d3b-144">它們會自動適用於目前在套件管理員主控台中選取的專案，而不需要您手動切換目錄。</span><span class="sxs-lookup"><span data-stu-id="84d3b-144">They automatically work with the current project selected in the Package Manager Console without requiring manually switching directories.</span></span>  
* <span data-ttu-id="84d3b-145">在命令完成之後，它們會在 Visual Studio 中自動開啟命令所產生的檔案。</span><span class="sxs-lookup"><span data-stu-id="84d3b-145">They automatically open files generated by the commands in Visual Studio after the command is completed.</span></span>

<a name="cli"></a>

### <a name="get-the-cli-tools"></a><span data-ttu-id="84d3b-146">取得 CLI 工具</span><span class="sxs-lookup"><span data-stu-id="84d3b-146">Get the CLI tools</span></span>

<span data-ttu-id="84d3b-147">.NET Core SDK 雖包含 `dotnet ef` 命令，但您必須安裝 `Microsoft.EntityFrameworkCore.Design` 套件才能啟用命令：</span><span class="sxs-lookup"><span data-stu-id="84d3b-147">The `dotnet ef` commands are included in the .NET Core SDK, but to enable the commands you have to install the `Microsoft.EntityFrameworkCore.Design` package:</span></span>

 ``` Console    
dotnet add package Microsoft.EntityFrameworkCore.Design 
``` 

<span data-ttu-id="84d3b-148">若為 ASP.NET Core 2.1 應用程式，則已自動包含此套件。</span><span class="sxs-lookup"><span data-stu-id="84d3b-148">For ASP.NET Core 2.1 apps, this package is included automatically.</span></span>

<span data-ttu-id="84d3b-149">如同先前[必要條件](#prerequisites)中所述，您也必須安裝 .NET Core 2.1 SDK。</span><span class="sxs-lookup"><span data-stu-id="84d3b-149">As explained earlier in [Prerequisites](#prerequisites), you also need to install the .NET Core 2.1 SDK.</span></span>

> [!IMPORTANT]      
> <span data-ttu-id="84d3b-150">一律使用符合執行階段套件主要版本的工具套件版本。</span><span class="sxs-lookup"><span data-stu-id="84d3b-150">Always use the version of the tools package that matches the major version of the runtime packages.</span></span>

### <a name="get-the-package-manager-console-tools"></a><span data-ttu-id="84d3b-151">取得套件管理員主控台套件</span><span class="sxs-lookup"><span data-stu-id="84d3b-151">Get the Package Manager Console tools</span></span>

<span data-ttu-id="84d3b-152">若要取得 EF Core 的套件管理員主控台工具，請安裝 `Microsoft.EntityFrameworkCore.Tools` 套件：</span><span class="sxs-lookup"><span data-stu-id="84d3b-152">To get the Package Manager Console tools for EF Core, install the `Microsoft.EntityFrameworkCore.Tools` package:</span></span>

 ``` Console    
dotnet add package Microsoft.EntityFrameworkCore.Tools
``` 

<span data-ttu-id="84d3b-153">若為 ASP.NET Core 2.1 應用程式，則已自動包含此套件。</span><span class="sxs-lookup"><span data-stu-id="84d3b-153">For ASP.NET Core 2.1 apps, this package is included automatically.</span></span>

## <a name="upgrading-to-ef-core-21"></a><span data-ttu-id="84d3b-154">升級至 EF Core 2.1</span><span class="sxs-lookup"><span data-stu-id="84d3b-154">Upgrading to EF Core 2.1</span></span>

<span data-ttu-id="84d3b-155">若您要將現有的應用程式升級至 EF Core 2.1，則可能需要手動移除部分舊 EF Core 套件的參考：</span><span class="sxs-lookup"><span data-stu-id="84d3b-155">If you're upgrading an existing application to EF Core 2.1, some references to older EF Core packages may need to be removed manually:</span></span>

* <span data-ttu-id="84d3b-156">EF Core 2.1 中不再需要或支援 `Microsoft.EntityFrameworkCore.SqlServer.Design` 等資料庫提供者設計階段套件，但在升級其他套件時不會自動將其移除。</span><span class="sxs-lookup"><span data-stu-id="84d3b-156">Database provider design-time packages such as `Microsoft.EntityFrameworkCore.SqlServer.Design` are no longer required or supported in EF Core 2.1, but aren't automatically removed when upgrading the other packages.</span></span>

* <span data-ttu-id="84d3b-157">.NET CLI 工具現在包含在 .NET SDK 中，因此您可以從 *.csproj* 檔案移除對該套件的參考：</span><span class="sxs-lookup"><span data-stu-id="84d3b-157">The .NET CLI tools are now included in the .NET SDK, so the reference to that package can be removed from the *.csproj* file:</span></span>

  ```
  <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="2.0.0" />
  ```

<span data-ttu-id="84d3b-158">若應用程式的目標為 .NET Framework 且由舊版 Visual Studio 建立，請確認它們與 .NET Standard 2.0 程式庫相容：</span><span class="sxs-lookup"><span data-stu-id="84d3b-158">For applications that target the .NET Framework and were created by earlier versions of Visual Studio, make sure that they are compatible with .NET Standard 2.0 libraries:</span></span>

  * <span data-ttu-id="84d3b-159">編輯專案檔，並確定下列項目出現在初始屬性群組中：</span><span class="sxs-lookup"><span data-stu-id="84d3b-159">Edit the project file and make sure the following entry appears in the initial property group:</span></span>

    ``` xml
    <AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
    ```

  * <span data-ttu-id="84d3b-160">針對測試專案，也請確定下列項目存在：</span><span class="sxs-lookup"><span data-stu-id="84d3b-160">For test projects, also make sure the following entry is present:</span></span>

    ``` xml
    <GenerateBindingRedirectsOutputType>true</GenerateBindingRedirectsOutputType>
    ```
