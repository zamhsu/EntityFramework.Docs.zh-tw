---
title: 取得 Entity Framework 的 EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 122c38a2-f9e8-4ecc-9c72-a83bc9af7814
ms.openlocfilehash: 91b78e56f60edf7ebc8769b1c385f8547f63cd3d
ms.sourcegitcommit: 0cef7d448e1e47bdb333002e2254ed42d57b45b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2018
ms.locfileid: "43152384"
---
# <a name="get-entity-framework"></a>取得 Entity Framework
Entity Framework 是組成的 EF 工具適用於 Visual Studio 和 EF 執行階段。

## <a name="ef-tools-for-visual-studio"></a>適用於 Visual Studio 的 EF 工具

Entity Framework Tools for Visual Studio 包含 EF 設計工具和 EF 模型精靈 和所需的資料庫的第一次，並建立第一個工作流程的模型。 EF 工具包含在所有的最新版本的 Visual Studio。 如果您執行自訂安裝的 Visual Studio，您必須確定項目選擇包含它的工作負載，或藉由選取為 個別元件，會選取 「 Entity Framework 6 工具 」。

針對某些舊版的 Visual Studio 中，更新的 EF 工具是可供下載。 請參閱[Visual Studio 版本](~/ef6/what-is-new/visual-studio.md)如需如何取得最新版的 EF 工具可以使用您的 Visual Studio 版本適用的指引。

## <a name="ef-runtime"></a>EF 執行階段

最新版的 Entity Framework 可從[EntityFramework NuGet 套件](http://nuget.org/packages/EntityFramework/)。 如果您還不熟悉如何使用 NuGet 封裝管理員中，我們鼓勵您閱讀[NuGet 概觀](https://docs.microsoft.com/nuget/consume-packages/overview-and-workflow)。

### <a name="installing-the-ef-nuget-package"></a>安裝 EF NuGet 套件

您可以安裝 EntityFramework 套件上按一下滑鼠右鍵**參考**專案的資料夾，然後選取**管理 NuGet 套件...**

![ManageNuGetPackages](~/ef6/media/managenugetpackages.png)

### <a name="installing-from-package-manager-console"></a>從 套件管理員主控台安裝

或者，您可以在中執行下列命令安裝 EntityFramework [Package Manager Console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console)。

``` powershell
Install-Package EntityFramework
```

## <a name="installing-a-specific-version-of-ef"></a>安裝特定版本的 EF

從 EF 4.1 及更新版本的 EF 執行階段的新版本已發行過[EntityFramework NuGet 套件](https://www.nuget.org/packages/EntityFramework/)。 這些版本其中任何一個可以在 Visual Studio 中執行下列命令新增至.NET Framework 為基礎的專案[Package Manager Console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console):

``` powershell
Install-Package EntityFramework -Version <number>
```

請注意，`<number>`代表特定版本的 EF 來安裝。 比方說，6.2.0 是 EF 6.2 的數字的版本。   

4.1 之前的 EF 執行階段的.NET Framework 的一部分，無法個別安裝。

### <a name="installing-the-latest-preview"></a>安裝最新的預覽版

上述方法可讓您最新完整支援 Entity framework 的版本。 通常是發行前版本的 Entity Framework 提供我們由衷歡迎您試用並提供我們意見反應。

若要安裝的 EntityFramework 您可以選取最新的預覽版**包含發行前版本**在 [管理 NuGet 封裝] 視窗。 如果不有可用的任何發行前版本將會自動取得最新版的 Entity Framework 的完整支援的版本。

![IncludePreRelease](~/ef6/media/includeprerelease.png)

或者，您可以執行下列命令[Package Manager Console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console)。

``` powershell
Install-Package EntityFramework -Pre
```
