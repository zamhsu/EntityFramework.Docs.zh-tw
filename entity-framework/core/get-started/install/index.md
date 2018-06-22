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
# <a name="installing-ef-core"></a>安裝 EF Core

## <a name="prerequisites"></a>必要條件

若要開發 .NET Core 2.0 應用程式 (包含目標設為 .NET Core 的 ASP.NET Core 2.0 應用程式)，您將需要下載並安裝您平台適用的 [.NET Core 2.0 SDK](https://www.microsoft.com/net/download/core) 版本。 **即使您已安裝 Visual Studio 2017 15.3 版，也是如此。**

若要搭配使用 EF Core 2.0 或任何其他 .NET Standard 2.0 程式庫與 .NET 平台和 .NET Core 2.0 (例如與 .NET Framework 4.6.1 或更新版本)，您需要可辨識 .NET Standard 2.0 和其相容架構的 NuGet 版本。 以下是您可以取得這個項目的一些方法：

* 安裝 Visual Studio 2017 15.3 版
* 如果您使用 Visual Studio 2015，請[將 NuGet 用戶端下載並升級至 3.6.0 版](https://www.nuget.org/downloads)

使用舊版 Visual Studio 所建立且目標設為 .NET Framework 的專案可能需要進行額外修改，才能與 .NET Standard 2.0 程式庫相容：

* 編輯專案檔，並確定下列項目出現在初始屬性群組中：
  ``` xml
  <AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
  ```

* 針對測試專案，也請確定下列項目存在：
  ``` xml
  <GenerateBindingRedirectsOutputType>true</GenerateBindingRedirectsOutputType>
  ```

## <a name="getting-the-bits"></a>取得位元
將 EF Core 執行階段程式庫新增至應用程式的建議方式，是從 NuGet 安裝 EF Core 資料庫提供者。

除了執行階段程式庫之外，您還可以安裝工具；在設計階段，更輕鬆地於專案中執行數個 EF Core 相關工作，例如建立和套用移轉，以及根據現有資料庫來建立模型。

> [!TIP]  
> 如果您需要更新的應用程式是使用協力廠商資料庫提供者，則請務必檢查與您要使用之 EF Core 版本相容的提供者更新。 例如 舊版的資料庫提供者與 2.0 版的 EF Core 執行階段不相容。  

> [!TIP]  
> 除了協力廠商資料庫提供者之外，將目標設為 ASP.NET Core 2.0 的應用程式還可以使用 EF Core 2.0，而且沒有其他相依性。 將目標設為 ASP.NET Core 舊版本的應用程式需要升級至 ASP.NET Core 2.0，才能使用 EF Core 2.0。

<a name="cli"></a>
### <a name="cross-platform-development-using-the-net-core-command-line-interface-cli"></a>使用 .NET Core 命令列介面 (CLI) 的跨平台開發

若要開發目標設為 [.NET Core](https://www.microsoft.com/net/download/core) 的應用程式，您可以選擇搭配使用 [`dotnet` CLI 命令](https://docs.microsoft.com/dotnet/core/tools/) 與慣用的文字編輯器或整合式開發環境 (IDE) (例如 Visual Studio、Visual Studio for Mac 或 Visual Studio Code)。

> [!IMPORTANT]  
> 目標設為 .NET Core 的應用程式需要特定 Visual Studio 版本 (例如 .NET Core 1.x 開發需要 Visual Studio 2017)，而 .NET Core 2.0 開發需要 Visual Studio 2017 15.3 版。

若要在跨平台 .NET Core 應用程式中安裝或升級 SQL Server 提供者，請切換至應用程式的目錄，並在命令列中執行下列命令：

``` Console
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

您可以使用 `-v` 修飾詞，在 `dotnet add package` 命令中指出的特定版本安裝。 例如 安裝 EF Core 2.0 套件、將 `-v 2.0.0` 附加至命令。

EF Core 包含 [`dotnet` CLI 的一組其他命令](../../miscellaneous/cli/dotnet.md)，並從 `dotnet ef` 開始。 若要使用 `dotnet ef` CLI 命令，您應用程式的 `.csproj` 檔案必須包含下列項目：

``` xml
<ItemGroup>
  <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="2.0.0" />
</ItemGroup>
```

EF Core 的 .NET Core CLI 工具也需要稱為 Microsoft.EntityFrameworkCore.Design 的個別套件。 只需要使用下列命令，即可將它新增至專案：

``` Console
dotnet add package Microsoft.EntityFrameworkCore.Design
```

> [!IMPORTANT]  
> 一律使用符合執行階段套件主要版本的工具套件版本。

<a name="visual-studio"></a>
### <a name="visual-studio-development"></a>Visual Studio 開發

您可以使用 Visual Studio 開發許多不同類型的應用程式，而這些應用程式的目標設為 .NET Core、.NET Framework 或 EF Core 所支援的其他平台。

從 Visual Studio，您可以有兩種方式可以將 EF Core 資料庫提供者新增至應用程式：

#### <a name="using-nugets-package-manager-user-interfacehttpsdocsmicrosoftcomnugettoolspackage-manager-ui"></a>使用 NuGet 的[套件管理員使用者介面](https://docs.microsoft.com/nuget/tools/package-manager-ui)

* 在功能表上，選取 [專案] > [管理 NuGet 套件]

* 按一下 [瀏覽] 或 [更新] 索引標籤

* 選取 `Microsoft.EntityFrameworkCore.SqlServer` 套件和所需的版本，並確認

#### <a name="using-nugets-package-manager-console-pmchttpsdocsmicrosoftcomnugettoolspackage-manager-console"></a>使用 NuGet 的[套件管理員主控台 (PMC)](https://docs.microsoft.com/nuget/tools/package-manager-console)

* 在功能表上，選取 [工具] > [NuGet 套件管理員] > [套件管理員主控台]

* 在 PMC 中鍵入和執行下列命令：

  ``` PowerShell  
  Install-Package Microsoft.EntityFrameworkCore.SqlServer
  ```
* 您可以改成使用 `Update-Package` 命令來更新已安裝為較新版本的套件

* 若要指定特定版本，您可以使用 `-Version` 修飾詞；例如，安裝 EF Core 2.0 套件、將 `-Version 2.0.0` 附加至命令

#### <a name="tools"></a>工具

在 Visual Studio 中，也有 PowerShell 版本且在 [PMC 內執行的 EF Core 命令](../../miscellaneous/cli/powershell.md)，而其功能與 `dotnet ef` 命令類似。 若要使用這些項目，請使用套件管理員 UI 或 PMC 來安裝 `Microsoft.EntityFrameworkCore.Tools` 套件。

> [!IMPORTANT]  
> 一律使用符合執行階段套件主要版本的工具套件版本。

> [!TIP]  
> 在 Visual Studio 中，雖然可以從 PMC 使用 `dotnet ef` 命令，但是使用 PowerShell 版本更為方便：
> * 它們會自動使用 PMC 中目前選取的專案，而不需要手動切換目錄。  
> * 在命令完成之後，它們會在 Visual Studio 中自動開啟命令所產生的檔案。

> [!IMPORTANT]  
> **EF Core 2.0 中已被取代的套件**：如果您要將現有應用程式升級至 EF Core 2.0，則可能需要手動移除舊 EF Core 套件的一些參考。 特別的是，EF Core 2.0 不再需要或支援資料庫提供者設計階段套件 (例如 `Microsoft.EntityFrameworkCore.SqlServer.Design`)，但在升級其他套件時不會自動予以移除。
