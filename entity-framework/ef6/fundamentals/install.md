---
title: 取得 Entity Framework EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 122c38a2-f9e8-4ecc-9c72-a83bc9af7814
ms.openlocfilehash: 2bdec6a9be228fbe934d0f46aa1bfafdfb2c971c
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181744"
---
# <a name="get-entity-framework"></a>取得 Entity Framework
Entity Framework 是由適用于 Visual Studio 和 EF 執行時間的 EF 工具所組成。

## <a name="ef-tools-for-visual-studio"></a>適用于 Visual Studio 的 EF 工具

Visual Studio 的 Entity Framework Tools 包含 EF 設計工具和 EF 模型 Wizard，而且是資料庫 first 和 Model first 工作流程的必要參數。 所有最新版本的 Visual Studio 都包含 EF 工具。 如果您執行 Visual Studio 的自訂安裝，則必須選擇包含該專案的工作負載，或將其選取為個別元件，以確保選取「Entity Framework 6 工具」。

針對某些過去版本的 Visual Studio，更新的 EF 工具可供下載。 如需如何取得適用于您的 Visual Studio 版本之 EF 工具的最新版本指引，請參閱[Visual Studio 版本](~/ef6/what-is-new/visual-studio.md)。

## <a name="ef-runtime"></a>EF 執行時間

最新版的 Entity Framework 是以[EntityFramework NuGet 套件](https://nuget.org/packages/EntityFramework/)的形式提供。 如果您不熟悉 NuGet 套件管理員，我們鼓勵您閱讀[Nuget 總覽](https://docs.microsoft.com/nuget/consume-packages/overview-and-workflow)。

### <a name="installing-the-ef-nuget-package"></a>安裝 EF NuGet 套件

您可以在專案的 [**參考**] 資料夾上按一下滑鼠右鍵，然後選取 [**管理 NuGet 套件 ...** ]，以安裝 EntityFramework 套件。

![管理 NuGet 套件](~/ef6/media/managenugetpackages.png)

### <a name="installing-from-package-manager-console"></a>從套件管理員主控台安裝

或者，您也可以在 [[套件管理員主控台](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)] 中執行下列命令來安裝 EntityFramework。

``` powershell
Install-Package EntityFramework
```

## <a name="installing-a-specific-version-of-ef"></a>安裝特定版本的 EF

從 EF 4.1 開始，新版本的 EF 執行時間已發行為[EntityFramework NuGet 套件](https://www.nuget.org/packages/EntityFramework/)。 您可以在 Visual Studio 的[套件管理員主控台](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)中執行下列命令，以將任何版本新增至 .NET Framework 專案：

``` powershell
Install-Package EntityFramework -Version <number>
```

請注意，`<number>` 代表要安裝的 EF 特定版本。 例如，6.2.0 是 EF 6.2 的數位版本。   

4\.1 之前的 EF 執行時間是 .NET Framework 的一部分，無法個別安裝。

### <a name="installing-the-latest-preview"></a>安裝最新的預覽

上述方法會提供您最新的 Entity Framework 完整支援版本。 Entity Framework 可以使用發行前版本，我們希望您可以試用並提供意見反應給我們。

若要安裝最新的 EntityFramework 預覽，您可以在 [管理 NuGet 套件] 視窗中選取 [**包含發行**前版本]。 如果沒有可用的發行前版本，您將會自動取得 Entity Framework 的最新完整支援版本。

![包含發行前版本](~/ef6/media/includeprerelease.png)

或者，您可以在 [[套件管理員主控台](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)] 中執行下列命令。

``` powershell
Install-Package EntityFramework -Pre
```
