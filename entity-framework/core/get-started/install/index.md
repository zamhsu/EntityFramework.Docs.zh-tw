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
# <a name="installing-entity-framework-core"></a>安裝 Entity Framework Core

## <a name="prerequisites"></a>必要條件

* 若要開發以 .NET Core 2.1 為目標的應用程式，請安裝 [.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core)。 即便您有最新版本的 Visual Studio 2017 也必須安裝該 SDK。

* 若要使用 Visual Studio 開發以 .NET Core 2.1 為目標的應用程式，請安裝 Visual Studio 2017 15.7 版或更新版本。

* 若要在 ASP.NET Core 應用程式中使用 Entity Framework 2.1，請使用 ASP.NET Core 2.1。 使用舊版 ASP.NET Core 的應用程式須更新至 2.1。

* 若為以 NET Framework 4.6.1 或更新版本為目標的應用程式，您可以使用 Visual Studio 2015。 但是您需要能感知 .NET Standard 2.0 及其相容 Framework 的 NuGet 版本。 若要在 Visual Studio 2015 中取得該項目，[請將 NuGet 用戶端升級至 3.6.0 版](https://www.nuget.org/downloads)。

## <a name="get-the-entity-framework-core-runtime"></a>取得 Entity Framework Core 執行階段

若要將 EF Core 執行階段程式庫新增至應用程式，請安裝您欲使用資料庫提供者適用的 NuGet 套件。 如需支援的提供者和其 NuGet 套件名稱的清單，請查看[資料庫提供者](../../providers/index.md)。

若要安裝或更新 NuGet 套件，請使用 .NET Core CLI、Visual Studio 套件管理員對話方塊，或是 Visual Studio 套件管理員主控台。

若為 ASP.NET Core 2.1 應用程式，則會自動包含記憶體內部和 SQL Server 提供者，因此無須個別安裝。

> [!TIP]  
> 如果您需要更新的應用程式是使用協力廠商資料庫提供者，則請務必檢查與您要使用之 EF Core 版本相容的提供者更新。 例如，舊版的資料庫提供者與 2.1 版的 EF Core 執行階段不相容。  

### <a name="net-core-cli"></a>.NET Core CLI

下列 .NET Core CLI 命令會安裝或更新 SQL Server 提供者：

``` Console
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

您可在 `dotnet add package` 命令列使用 `-v` 修飾詞來指定特定版本。 例如，若要安裝 EF Core 2.1.0 套件，請在命令附加 `-v 2.1.0`。

### <a name="visual-studio-nuget-package-manager-dialog"></a>Visual Studio NuGet 套件管理員對話方塊

* 從功能表選取 [專案] > [管理 NuGet 套件]

* 按一下 [瀏覽] 或 [更新] 索引標籤

* 若要安裝或更新 SQL Server 提供者，請選取 `Microsoft.EntityFrameworkCore.SqlServer` 套件並確認。

如需詳細資訊，請參閱 [NuGet 套件管理員對話方塊](https://docs.microsoft.com/nuget/tools/package-manager-ui)。

### <a name="visual-studio-nuget-package-manager-console"></a>Visual Studio NuGet 套件管理員主控台

* 從功能表選取 [工具] > [NuGet 套件管理員] > [套件管理員主控台]

* 若要安裝 SQL Server 提供者，請在套件管理員主控台中執行以下命令：

  ``` PowerShell  
  Install-Package Microsoft.EntityFrameworkCore.SqlServer
  ```
* 若要更新提供者，請使用 `Update-Package` 命令。

* 若要指定特定版本，請使用 `-Version` 修飾詞。 例如，若要安裝 EF Core 2.1.0 套件，請將 `-Version 2.1.0` 附加至命令

如需詳細資訊，請參閱[套件管理員主控台](https://docs.microsoft.com/nuget/tools/package-manager-console)。

## <a name="get-entity-framework-core-tools"></a>取得 Entity Framework Core 工具

除了執行階段程式庫以外，您還可以安裝其他工具，在專案的設計期間中執行部分與 EF Core 相關的工作。 例如，您可以建立移轉、套用移轉，以及利用現有的資料庫為基礎建立模型。

有兩組工具可用：
* .NET Core [命令列介面 (CLI) 工具](../../miscellaneous/cli/dotnet.md)適用於 Windows、Linux 和 macOS。 這些命令會以 `dotnet ef` 開頭。 
* [套件管理員主控台工具](../../miscellaneous/cli/powershell.md)可在 Windows 上的 Visual Studio 2017 中執行。 這些命令會以動詞開頭，例如 `Add-Migration` 和 `Update-Database`。

雖然您可從套件管理員主控台使用 `dotnet ef` 命令，但在使用 Visual Studio 時使用套件管理員主控台工具更加方便：
* 它們會自動適用於目前在套件管理員主控台中選取的專案，而不需要您手動切換目錄。  
* 在命令完成之後，它們會在 Visual Studio 中自動開啟命令所產生的檔案。

<a name="cli"></a>

### <a name="get-the-cli-tools"></a>取得 CLI 工具

.NET Core SDK 雖包含 `dotnet ef` 命令，但您必須安裝 `Microsoft.EntityFrameworkCore.Design` 套件才能啟用命令：

 ``` Console    
dotnet add package Microsoft.EntityFrameworkCore.Design 
``` 

若為 ASP.NET Core 2.1 應用程式，則已自動包含此套件。

如同先前[必要條件](#prerequisites)中所述，您也必須安裝 .NET Core 2.1 SDK。

> [!IMPORTANT]      
> 一律使用符合執行階段套件主要版本的工具套件版本。

### <a name="get-the-package-manager-console-tools"></a>取得套件管理員主控台套件

若要取得 EF Core 的套件管理員主控台工具，請安裝 `Microsoft.EntityFrameworkCore.Tools` 套件：

 ``` Console    
dotnet add package Microsoft.EntityFrameworkCore.Tools
``` 

若為 ASP.NET Core 2.1 應用程式，則已自動包含此套件。

## <a name="upgrading-to-ef-core-21"></a>升級至 EF Core 2.1

若您要將現有的應用程式升級至 EF Core 2.1，則可能需要手動移除部分舊 EF Core 套件的參考：

* EF Core 2.1 中不再需要或支援 `Microsoft.EntityFrameworkCore.SqlServer.Design` 等資料庫提供者設計階段套件，但在升級其他套件時不會自動將其移除。

* .NET CLI 工具現在包含在 .NET SDK 中，因此您可以從 *.csproj* 檔案移除對該套件的參考：

  ```
  <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="2.0.0" />
  ```

若應用程式的目標為 .NET Framework 且由舊版 Visual Studio 建立，請確認它們與 .NET Standard 2.0 程式庫相容：

  * 編輯專案檔，並確定下列項目出現在初始屬性群組中：

    ``` xml
    <AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
    ```

  * 針對測試專案，也請確定下列項目存在：

    ``` xml
    <GenerateBindingRedirectsOutputType>true</GenerateBindingRedirectsOutputType>
    ```
