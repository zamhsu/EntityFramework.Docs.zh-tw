---
title: 安裝 Entity Framework Core - EF Core
author: divega
ms.date: 08/06/2017
ms.assetid: 608cc774-c570-4809-8a3e-cd2c8446b8b2
uid: core/get-started/install/index
ms.openlocfilehash: b4ae13ae1b22bb78c2c0407c0b3da64ee12ff2c1
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181207"
---
# <a name="installing-entity-framework-core"></a>安裝 Entity Framework Core

## <a name="prerequisites"></a>必要條件

* 因為 EF Core 是 [.NET Standard 2.1](/dotnet/standard/net-standard) 程式庫。 所以 EF Core 需要支援 .NET Standard 2.1 的 .NET 實作才可執行。 其他 .NET Standard 2.1 程式庫也可以參考 EF Core。 

* 例如，您可以使用 EF Core 開發以 .NET Core 為目標的應用程式。 建置 .NET Core 應用程式需要 [.NET Core SDK](https://dotnet.microsoft.com/download)。 此外，您也可以使用 [Visual Studio](https://visualstudio.microsoft.com/vs)、[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac) 或 [Visual Studio Code](https://code.visualstudio.com) 等開發環境。 如需詳細資訊，請參閱 [.NET Core 使用者入門](/dotnet/core/get-started)。

* 您可以與 Visual Studio 搭配使用 EF Core 來開發 Windows 上的應用程式。 建議使用最新版的 [Visual Studio](https://visualstudio.microsoft.com/vs)。

* EF Core 可在其他的 .NET 實作上執行，像是 [Xamarin](https://dotnet.microsoft.com/apps/xamarin) 及 .NET Native。 但實踐這些實作有執行階段的限制，其可能影響 EF Core 在應用程式上的運作。 如需詳細資訊，請參閱 [EF Core 支援的 .NET 實作](xref:core/platforms/index)。

* 最後，不同的資料庫提供者可能需要特定的資料庫引擎版本、.NET 實作或作業系統。 請確認有可用且能為您應用程式支援正確環境的 [EF Core 資料庫提供者](xref:core/providers/index)可用。

## <a name="get-the-entity-framework-core-runtime"></a>取得 Entity Framework Core 執行階段

若要將 EF Core 新增至應用程式，請安裝您欲使用資料庫提供者適用的 NuGet 套件。

若您要建置 ASP.NET Core 應用程式，就不必安裝記憶體內部及 SQL Server 提供者。 目前版本的 ASP.NET Core 包含這些提供者以及 EF Core 執行階段。  

若要安裝或更新 NuGet 套件，您可以使用 .NET Core 命令列介面 (CLI)、Visual Studio 套件管理員對話方塊，或是 Visual Studio 套件管理員主控台。

### <a name="net-core-cli"></a>.NET Core CLI

* 請從作業系統的命令列使用以下 .NET Core CLI 命令，來安裝或更新 EF Core SQL Server 提供者：

  ``` Console
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  ```

* 您可在 `dotnet add package` 命令列使用 `-v` 修飾詞來指定特定版本。 例如，若要安裝 EF Core 2.2.0 套件，請將 `-v 2.2.0` 附加至命令。

如需詳細資訊，請參閱 [.NET Core 命令列介面 (CLI) 工具](/dotnet/core/tools/)。

### <a name="visual-studio-nuget-package-manager-dialog"></a>Visual Studio NuGet 套件管理員對話方塊

* 從 Visual Studio 功能表選取 [專案] > [管理 NuGet 套件] 

* 按一下 [瀏覽]  或 [更新]  索引標籤

* 若要安裝或更新 SQL Server 提供者，請選取 `Microsoft.EntityFrameworkCore.SqlServer` 套件並確認。

如需詳細資訊，請參閱 [NuGet 套件管理員對話方塊](/nuget/tools/package-manager-ui)。

### <a name="visual-studio-nuget-package-manager-console"></a>Visual Studio NuGet 套件管理員主控台

* 從 Visual Studio 功能表選取 [工具] > [NuGet 套件管理員] > [套件管理員主控台] 

* 若要安裝 SQL Server 提供者，請在套件管理員主控台中執行以下命令：

  ``` PowerShell  
  Install-Package Microsoft.EntityFrameworkCore.SqlServer
  ```
* 若要更新提供者，請使用 `Update-Package` 命令。

* 若要指定特定版本，請使用 `-Version` 修飾詞。 例如，若要安裝 EF Core 2.2.0 套件，請將 `-Version 2.2.0` 附加至命令

如需詳細資訊，請參閱[套件管理員主控台](/nuget/tools/package-manager-console)。

## <a name="get-the-entity-framework-core-tools"></a>取得 Entity Framework Core 工具

您可以安裝工具以處理專案中與 EF Core 相關的工作，像是建立和套用資料庫移轉，或依據現有的資料庫建立 EF Core 模型。

有兩組工具可用：

* [.NET Core 命令列介面 (CLI) 工具](xref:core/miscellaneous/cli/dotnet)可在 Windows、Linux 或 macOS 上使用。 這些命令會以 `dotnet ef` 開頭。 

* [套件管理員主控台 (PMC) 工具](xref:core/miscellaneous/cli/powershell)可在 Windows 上的 Visual Studio 中執行。 這些命令會以動詞開頭，例如 `Add-Migration` 和 `Update-Database`。

雖然您也可從套件管理員主控台使用 `dotnet ef` 命令，但在使用 Visual Studio 時，建議使用套件管理員主控台工具：

* 它們會自動使用 Visual Studio 中 PMC 內目前的所選專案，而不必手動切換目錄。  

* 在命令完成之後，它們會在 Visual Studio 中自動開啟命令所產生的檔案。

<a name="cli"></a>

### <a name="get-the-net-core-cli-tools"></a>取得 .NET Core CLI 工具

.NET Core CLI 工具需要先前在[必要條件](#prerequisites)中提到的 .NET Core SDK。

目前版本的 .NET Core SDK 雖包含 `dotnet ef` 命令，但您必須安裝 `Microsoft.EntityFrameworkCore.Design` 套件才能啟用特殊專案上的命令：

``` Console 
dotnet add package Microsoft.EntityFrameworkCore.Design 
``` 

若為 ASP.NET Core 應用程式，則已自動包含此套件。

> [!IMPORTANT]      
> 一律使用符合執行階段套件主要版本的工具套件版本。

### <a name="get-the-package-manager-console-tools"></a>取得套件管理員主控台套件

若要取得 EF Core 的套件管理員主控台工具，請安裝 `Microsoft.EntityFrameworkCore.Tools` 套件。 例如，從 Visual Studio 安裝：

``` PowerShell  
Install-Package Microsoft.EntityFrameworkCore.Tools
``` 

若為 ASP.NET Core 應用程式，則已自動包含此套件。

## <a name="upgrading-to-the-latest-ef-core"></a>升級為最新的 EF Core

* 每當我們發行新版本的 EF Core，同時也會發行屬於 EF Core 專案一部分的新版本提供者，像是 Microsoft.EntityFrameworkCore.SqlServer、Microsoft.EntityFrameworkCore.Sqlite 和 Microsoft.EntityFrameworkCore.InMemory。 您可以僅升級為新版本的提供者，來取得所有改善。 

* EF Core、SQL Server 以及記憶體內部提供者都包含於目前版本的 ASP.NET Core 中。 若要將現有的 ASP.NET Core 應用程式升級為 EF Core 的較新版本，請持續升級 ASP.NET Core 的版本。

* 如果您需要更新的應用程式是使用協力廠商資料庫提供者，則請務必檢查與您要使用之 EF Core 版本相容的提供者更新。 例如，舊版的資料庫提供者與 2.0 版的 EF Core 執行階段不相容。

* EF Core 的協力廠商提供者通常不會同時發行修補程式版本與 EF Core 執行階段。 若要將使用協力廠商提供者的應用程式升級為 EF Core 的修補程式版本，您可能必須新增個別 EF Core 執行階段元件的直接參考，像是 Microsoft.EntityFrameworkCore 及 Microsoft.EntityFrameworkCore.Relational。

* 若您要將現有的應用程式升級為 EF Core 的最新版本，則可能需要手動移除部分舊 EF Core 套件的參考：

  * EF Core 2.0 及更新版本中不再需要或支援如 `Microsoft.EntityFrameworkCore.SqlServer.Design` 等資料庫提供者設計階段套件，但在升級其他套件時不會自動將其移除。

  * 從 2.1 版起，.NET CLI 工具都包含在 .NET SDK 中，因此您可以從專案檔移除該套件的參考：
    ```xml
    <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="2.0.0" />
    ```

