---
title: 安裝 EF Core
author: divega
ms.author: divega
ms.date: 08/06/2017
ms.assetid: 608cc774-c570-4809-8a3e-cd2c8446b8b2
ms.technology: entity-framework-core
uid: core/get-started/install/index
ms.openlocfilehash: 31b96ebd0ae282b88be98988eff6263084dc5dd5
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
ms.locfileid: "26049240"
---
# <a name="installing-ef-core"></a><span data-ttu-id="c157b-102">安裝 EF Core</span><span class="sxs-lookup"><span data-stu-id="c157b-102">Installing EF Core</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c157b-103">必要條件</span><span class="sxs-lookup"><span data-stu-id="c157b-103">Prerequisites</span></span>

<span data-ttu-id="c157b-104">若要開發 .NET Core 2.0 應用程式 (包含目標設為 .NET Core 的 ASP.NET Core 2.0 應用程式)，您將需要下載並安裝您平台適用的 [.NET Core 2.0 SDK](https://www.microsoft.com/net/download/core) 版本。</span><span class="sxs-lookup"><span data-stu-id="c157b-104">In order to develop .NET Core 2.0 applications (including ASP.NET Core 2.0 applications that target .NET Core) you will need to download and install a version of the [.NET Core 2.0 SDK](https://www.microsoft.com/net/download/core) that is appropriate to your platform.</span></span> <span data-ttu-id="c157b-105">**即使您已安裝 Visual Studio 2017 15.3 版，也是如此。**</span><span class="sxs-lookup"><span data-stu-id="c157b-105">**This is true even if you have installed Visual Studio 2017 version 15.3.**</span></span>

<span data-ttu-id="c157b-106">若要搭配使用 EF Core 2.0 或任何其他 .NET Standard 2.0 程式庫與 .NET 平台和 .NET Core 2.0 (例如與 .NET Framework 4.6.1 或更新版本)，您需要可辨識 .NET Standard 2.0 和其相容架構的 NuGet 版本。</span><span class="sxs-lookup"><span data-stu-id="c157b-106">In order to use EF Core 2.0 or any other .NET Standard 2.0 library with a .NET platforms besides .NET Core 2.0 (e.g. with .NET Framework 4.6.1 or greater) you will need a version of NuGet that is aware of the .NET Standard 2.0 and its compatible frameworks.</span></span> <span data-ttu-id="c157b-107">以下是您可以取得這個項目的一些方法：</span><span class="sxs-lookup"><span data-stu-id="c157b-107">Here are a few ways you can obtain this:</span></span>

* <span data-ttu-id="c157b-108">安裝 Visual Studio 2017 15.3 版</span><span class="sxs-lookup"><span data-stu-id="c157b-108">Install Visual Studio 2017 version 15.3</span></span>
* <span data-ttu-id="c157b-109">如果您使用 Visual Studio 2015，請[將 NuGet 用戶端下載並升級至 3.6.0 版](https://www.nuget.org/downloads)</span><span class="sxs-lookup"><span data-stu-id="c157b-109">If you are using Visual Studio 2015, [download and upgrade NuGet client to version 3.6.0](https://www.nuget.org/downloads)</span></span>

<span data-ttu-id="c157b-110">使用舊版 Visual Studio 所建立且目標設為 .NET Framework 的專案可能需要進行額外修改，才能與 .NET Standard 2.0 程式庫相容：</span><span class="sxs-lookup"><span data-stu-id="c157b-110">Projects created with previous versions of Visual Studio and targeting .NET Framework may need additional modifications in order to be compatible with .NET Standard 2.0 libraries:</span></span>

* <span data-ttu-id="c157b-111">編輯專案檔，並確定下列項目出現在初始屬性群組中：</span><span class="sxs-lookup"><span data-stu-id="c157b-111">Edit the project file and make sure the following entry appears in the initial property group:</span></span>
  ``` xml
  <AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
  ```

* <span data-ttu-id="c157b-112">針對測試專案，也請確定下列項目存在：</span><span class="sxs-lookup"><span data-stu-id="c157b-112">For test projects, also make sure the following entry is present:</span></span>
  ``` xml
  <GenerateBindingRedirectsOutputType>true</GenerateBindingRedirectsOutputType>
  ```

## <a name="getting-the-bits"></a><span data-ttu-id="c157b-113">取得位元</span><span class="sxs-lookup"><span data-stu-id="c157b-113">Getting the bits</span></span>
<span data-ttu-id="c157b-114">將 EF Core 執行階段程式庫新增至應用程式的建議方式，是從 NuGet 安裝 EF Core 資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="c157b-114">The recommended way to add EF Core runtime libraries into an application is to install an EF Core database provider from NuGet.</span></span>

<span data-ttu-id="c157b-115">除了執行階段程式庫之外，您還可以安裝工具；在設計階段，更輕鬆地於專案中執行數個 EF Core 相關工作，例如建立和套用移轉，以及根據現有資料庫來建立模型。</span><span class="sxs-lookup"><span data-stu-id="c157b-115">Besides the runtime libraries, you can install tools which make it easier to perform several EF Core-related tasks in your project at design time, such as creating and applying migrations, and creating a model based on an existing database.</span></span>

> [!TIP]  
> <span data-ttu-id="c157b-116">如果您需要更新的應用程式是使用協力廠商資料庫提供者，則請務必檢查與您要使用之 EF Core 版本相容的提供者更新。</span><span class="sxs-lookup"><span data-stu-id="c157b-116">If you need to update an application that is using a third-party database provider, always check for an update of the provider that is compatible with the version of EF Core you want to use.</span></span> <span data-ttu-id="c157b-117">例如</span><span class="sxs-lookup"><span data-stu-id="c157b-117">E.g.</span></span> <span data-ttu-id="c157b-118">舊版的資料庫提供者與 2.0 版的 EF Core 執行階段不相容。</span><span class="sxs-lookup"><span data-stu-id="c157b-118">database providers for previous versions are not compatible with version 2.0 of the EF Core runtime.</span></span>  

> [!TIP]  
> <span data-ttu-id="c157b-119">除了協力廠商資料庫提供者之外，將目標設為 ASP.NET Core 2.0 的應用程式還可以使用 EF Core 2.0，而且沒有其他相依性。</span><span class="sxs-lookup"><span data-stu-id="c157b-119">Applications targeting ASP.NET Core 2.0 can use EF Core 2.0 without additional dependencies besides third party database providers.</span></span> <span data-ttu-id="c157b-120">將目標設為 ASP.NET Core 舊版本的應用程式需要升級至 ASP.NET Core 2.0，才能使用 EF Core 2.0。</span><span class="sxs-lookup"><span data-stu-id="c157b-120">Applications targeting previous versions of ASP.NET Core need to upgrade to ASP.NET Core 2.0 in order to use EF Core 2.0.</span></span>

<a name="cli"></a>
### <a name="cross-platform-development-using-the-net-core-command-line-interface-cli"></a><span data-ttu-id="c157b-121">使用 .NET Core 命令列介面 (CLI) 的跨平台開發</span><span class="sxs-lookup"><span data-stu-id="c157b-121">Cross-platform development using the .NET Core Command Line Interface (CLI)</span></span>

<span data-ttu-id="c157b-122">若要開發目標設為 [.NET Core](https://www.microsoft.com/net/download/core) 的應用程式，您可以選擇搭配使用 [`dotnet` CLI 命令](https://docs.microsoft.com/dotnet/core/tools/) 與慣用的文字編輯器或整合式開發環境 (IDE) (例如 Visual Studio、Visual Studio for Mac 或 Visual Studio Code)。</span><span class="sxs-lookup"><span data-stu-id="c157b-122">To develop applications that target [.NET Core](https://www.microsoft.com/net/download/core) you can choose to use the [`dotnet` CLI commands](https://docs.microsoft.com/dotnet/core/tools/) in combination with your favorite text editor, or an Integrated Development Environment (IDE) such as Visual Studio, Visual Studio for Mac or Visual Studio Code.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="c157b-123">目標設為 .NET Core 的應用程式需要特定 Visual Studio 版本 (例如 .NET Core 1.x 開發需要 Visual Studio 2017)，而 .NET Core 2.0 開發需要 Visual Studio 2017 15.3 版。</span><span class="sxs-lookup"><span data-stu-id="c157b-123">Applications that target .NET Core require specific versions of Visual Studio, e.g. .NET Core 1.x development requires Visual Studio 2017, while .NET Core 2.0 development requires Visual Studio 2017 version 15.3.</span></span>

<span data-ttu-id="c157b-124">若要在跨平台 .NET Core 應用程式中安裝或升級 SQL Server 提供者，請切換至應用程式的目錄，並在命令列中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="c157b-124">To install or upgrade the SQL Server provider in a cross-platform .NET Core application, switch to the application's directory and run the following in a command line:</span></span>

``` Console
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="c157b-125">您可以使用 `-v` 修飾詞，在 `dotnet add package` 命令中指出的特定版本安裝。</span><span class="sxs-lookup"><span data-stu-id="c157b-125">You can indicate a specific version install in the `dotnet add package` command, using the `-v` modifier.</span></span> <span data-ttu-id="c157b-126">例如</span><span class="sxs-lookup"><span data-stu-id="c157b-126">E.g.</span></span> <span data-ttu-id="c157b-127">安裝 EF Core 2.0 套件、將 `-v 2.0.0` 附加至命令。</span><span class="sxs-lookup"><span data-stu-id="c157b-127">to install EF Core 2.0 packages, append `-v 2.0.0` to the command.</span></span>

<span data-ttu-id="c157b-128">EF Core 包含 [`dotnet` CLI 的一組其他命令](../../miscellaneous/cli/dotnet.md)，並從 `dotnet ef` 開始。</span><span class="sxs-lookup"><span data-stu-id="c157b-128">EF Core includes a set of [additional commands for the `dotnet` CLI](../../miscellaneous/cli/dotnet.md), starting with `dotnet ef`.</span></span> <span data-ttu-id="c157b-129">若要使用 `dotnet ef` CLI 命令，您應用程式的 `.csproj` 檔案必須包含下列項目：</span><span class="sxs-lookup"><span data-stu-id="c157b-129">In order to use the `dotnet ef` CLI commands, your application’s `.csproj` file needs to contain the following entry:</span></span>

``` xml
<ItemGroup>
  <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="2.0.0" />
</ItemGroup>
```

<span data-ttu-id="c157b-130">EF Core 的 .NET Core CLI 工具也需要稱為 Microsoft.EntityFrameworkCore.Design 的個別套件。</span><span class="sxs-lookup"><span data-stu-id="c157b-130">The .NET Core CLI tools for EF Core also require a separate package called Microsoft.EntityFrameworkCore.Design.</span></span> <span data-ttu-id="c157b-131">只需要使用下列命令，即可將它新增至專案：</span><span class="sxs-lookup"><span data-stu-id="c157b-131">You can simply add it to the project using:</span></span>

``` Console
dotnet add package Microsoft.EntityFrameworkCore.Design
```

> [!IMPORTANT]  
> <span data-ttu-id="c157b-132">一律使用符合執行階段套件主要版本的工具套件版本。</span><span class="sxs-lookup"><span data-stu-id="c157b-132">Always use versions of the tools packages that match the major version of the runtime packages.</span></span>

<a name="visual-studio"></a>
### <a name="visual-studio-development"></a><span data-ttu-id="c157b-133">Visual Studio 開發</span><span class="sxs-lookup"><span data-stu-id="c157b-133">Visual Studio development</span></span>

<span data-ttu-id="c157b-134">您可以使用 Visual Studio 開發許多不同類型的應用程式，而這些應用程式的目標設為 .NET Core、.NET Framework 或 EF Core 所支援的其他平台。</span><span class="sxs-lookup"><span data-stu-id="c157b-134">You can develop many different types of applications that target .NET Core, .NET Framework, or other platforms supported by EF Core using Visual Studio.</span></span>

<span data-ttu-id="c157b-135">從 Visual Studio，您可以有兩種方式可以將 EF Core 資料庫提供者新增至應用程式：</span><span class="sxs-lookup"><span data-stu-id="c157b-135">There are two ways you can install an EF Core database provider in your application from Visual Studio:</span></span>

#### <a name="using-nugets-package-manager-user-interfacehttpsdocsmicrosoftcomnugettoolspackage-manager-ui"></a><span data-ttu-id="c157b-136">使用 NuGet 的[套件管理員使用者介面](https://docs.microsoft.com/nuget/tools/package-manager-ui)</span><span class="sxs-lookup"><span data-stu-id="c157b-136">Using NuGet's [Package Manager User Interface](https://docs.microsoft.com/nuget/tools/package-manager-ui)</span></span>

* <span data-ttu-id="c157b-137">在功能表上，選取 [專案] > [管理 NuGet 套件]</span><span class="sxs-lookup"><span data-stu-id="c157b-137">Select on the menu **Project > Manage NuGet Packages**</span></span>

* <span data-ttu-id="c157b-138">按一下 [瀏覽] 或 [更新] 索引標籤</span><span class="sxs-lookup"><span data-stu-id="c157b-138">Click on the **Browse** or the **Updates** tab</span></span>

* <span data-ttu-id="c157b-139">選取 `Microsoft.EntityFrameworkCore.SqlServer` 套件和所需的版本，並確認</span><span class="sxs-lookup"><span data-stu-id="c157b-139">Select the `Microsoft.EntityFrameworkCore.SqlServer` package and the desired version and confirm</span></span>

#### <a name="using-nugets-package-manager-console-pmchttpsdocsmicrosoftcomnugettoolspackage-manager-console"></a><span data-ttu-id="c157b-140">使用 NuGet 的[套件管理員主控台 (PMC)](https://docs.microsoft.com/nuget/tools/package-manager-console)</span><span class="sxs-lookup"><span data-stu-id="c157b-140">Using NuGet's [Package Manager Console (PMC)](https://docs.microsoft.com/nuget/tools/package-manager-console)</span></span>

* <span data-ttu-id="c157b-141">在功能表上，選取 [工具] > [NuGet 套件管理員] > [套件管理員主控台]</span><span class="sxs-lookup"><span data-stu-id="c157b-141">Select on the menu **Tools > NuGet Package Manager > Package Manager Console**</span></span>

* <span data-ttu-id="c157b-142">在 PMC 中鍵入和執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="c157b-142">Type and run the following command in the PMC:</span></span>

  ``` PowerShell  
  Install-Package Microsoft.EntityFrameworkCore.SqlServer
  ```
* <span data-ttu-id="c157b-143">您可以改成使用 `Update-Package` 命令來更新已安裝為較新版本的套件</span><span class="sxs-lookup"><span data-stu-id="c157b-143">You can use the `Update-Package` command instead to update a package that is already installed to a more recent  version</span></span>

* <span data-ttu-id="c157b-144">若要指定特定版本，您可以使用 `-Version` 修飾詞；例如，安裝 EF Core 2.0 套件、將 `-Version 2.0.0` 附加至命令</span><span class="sxs-lookup"><span data-stu-id="c157b-144">To specify a specific version, you can use the `-Version` modifier, e.g. to install EF Core 2.0 packages, append `-Version 2.0.0` to the commands</span></span>

#### <a name="tools"></a><span data-ttu-id="c157b-145">工具</span><span class="sxs-lookup"><span data-stu-id="c157b-145">Tools</span></span>

<span data-ttu-id="c157b-146">在 Visual Studio 中，也有 PowerShell 版本且在 [PMC 內執行的 EF Core 命令](../../miscellaneous/cli/powershell.md)，而其功能與 `dotnet ef` 命令類似。</span><span class="sxs-lookup"><span data-stu-id="c157b-146">There is also a PowerShell version of the [EF Core commands which run inside the PMC](../../miscellaneous/cli/powershell.md) in Visual Studio, with similar capabilities to the `dotnet ef` commands.</span></span> <span data-ttu-id="c157b-147">若要使用這些項目，請使用套件管理員 UI 或 PMC 來安裝 `Microsoft.EntityFrameworkCore.Tools` 套件。</span><span class="sxs-lookup"><span data-stu-id="c157b-147">In order to use these, install the `Microsoft.EntityFrameworkCore.Tools` package using either the Package Manager UI or the PMC.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="c157b-148">一律使用符合執行階段套件主要版本的工具套件版本。</span><span class="sxs-lookup"><span data-stu-id="c157b-148">Always use versions of the tools packages that match the major version of the runtime packages.</span></span>

> [!TIP]  
> <span data-ttu-id="c157b-149">在 Visual Studio 中，雖然可以從 PMC 使用 `dotnet ef` 命令，但是使用 PowerShell 版本更為方便：</span><span class="sxs-lookup"><span data-stu-id="c157b-149">Although it is possible to use the `dotnet ef` commands from the PMC in Visual Studio, it is far more convenient to use the PowerShell version:</span></span>
> * <span data-ttu-id="c157b-150">它們會自動使用 PMC 中目前選取的專案，而不需要手動切換目錄。</span><span class="sxs-lookup"><span data-stu-id="c157b-150">They automatically work with the current project selected in the PMC without requiring manually switching directories.</span></span>  
> * <span data-ttu-id="c157b-151">在命令完成之後，它們會在 Visual Studio 中自動開啟命令所產生的檔案。</span><span class="sxs-lookup"><span data-stu-id="c157b-151">They automatically open files generated by the commands in Visual Studio after the command is completed.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="c157b-152">**EF Core 2.0 中已被取代的套件**：如果您要將現有應用程式升級至 EF Core 2.0，則可能需要手動移除舊 EF Core 套件的一些參考。</span><span class="sxs-lookup"><span data-stu-id="c157b-152">**Deprecated packages in EF Core 2.0:** If you are upgrading an existing application to EF Core 2.0, some references to older EF Core packages may need to be removed manually.</span></span> <span data-ttu-id="c157b-153">特別的是，EF Core 2.0 不再需要或支援資料庫提供者設計階段套件 (例如 `Microsoft.EntityFrameworkCore.SqlServer.Design`)，但在升級其他套件時不會自動予以移除。</span><span class="sxs-lookup"><span data-stu-id="c157b-153">In particular, database provider design-time packages such as `Microsoft.EntityFrameworkCore.SqlServer.Design` are no longer required or supported in EF Core 2.0, but will not be automatically removed when upgrading the other packages.</span></span>
