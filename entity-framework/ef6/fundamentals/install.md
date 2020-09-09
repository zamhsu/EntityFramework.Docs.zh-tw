---
title: 取得 Entity Framework-EF6
description: 取得 Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 122c38a2-f9e8-4ecc-9c72-a83bc9af7814
uid: ef6/fundamentals/install
ms.openlocfilehash: bfd016a93de73ada4487a454ec7abd9251aeecfe
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616244"
---
# <a name="get-entity-framework"></a>取得 Entity Framework
Entity Framework 是由適用于 Visual Studio 和 EF Runtime 的 EF 工具所組成。

## <a name="ef-tools-for-visual-studio"></a>適用于 Visual Studio 的 EF 工具

Visual Studio 的 Entity Framework Tools 包括 EF 設計工具和 EF 模型 Wizard，並且是資料庫 first 和模型優先工作流程的必要參數。 EF 工具組含在 Visual Studio 的所有最新版本中。 如果您執行 Visual Studio 的自訂安裝，您必須確定已選取包含該專案的工作負載，或是選取它作為個別元件，藉以選取 [Entity Framework 6 工具] 專案。

針對某些舊版的 Visual Studio，更新的 EF 工具可供下載。 請參閱 [Visual Studio 版本](xref:ef6/what-is-new/visual-studio) ，以取得如何取得適用于您的 Visual Studio 版本的最新 EF Tools 版本的指引。

## <a name="ef-runtime"></a>EF Runtime

Entity Framework 的最新版本會以 [EntityFramework NuGet 套件](https://nuget.org/packages/EntityFramework/)的形式提供。 如果您不熟悉 NuGet 封裝管理員，我們建議您閱讀 [Nuget 總覽](/nuget/consume-packages/overview-and-workflow)。

### <a name="installing-the-ef-nuget-package"></a>安裝 EF NuGet 套件

您可以在專案的 [**參考**] 資料夾上按一下滑鼠右鍵，然後選取 [**管理 NuGet 套件 ...** ]，以安裝 EntityFramework 套件。

![Manage NuGet Packages](~/ef6/media/managenugetpackages.png)

### <a name="installing-from-package-manager-console"></a>從封裝管理員主控台安裝

或者，您也可以在 [封裝管理員主控台](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)中執行下列命令來安裝 EntityFramework。

``` powershell
Install-Package EntityFramework
```

## <a name="installing-a-specific-version-of-ef"></a>安裝特定版本的 EF

從 EF 4.1 開始，新版本的 EF runtime 已發行為 [EntityFramework NuGet 套件](https://www.nuget.org/packages/EntityFramework/)。 您可以在 Visual Studio 的 [封裝管理員主控台](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)中執行下列命令，將這些版本的任何一個新增至 .NET Framework 專案：

``` powershell
Install-Package EntityFramework -Version <number>
```

請注意， `<number>` 代表要安裝之 EF 的特定版本。 例如，6.2.0 是 EF 6.2 的數位版本。   

4.1 之前的 EF 執行時間是 .NET Framework 的一部分，而且無法另外安裝。

### <a name="installing-the-latest-preview"></a>安裝最新的預覽

上述方法會提供 Entity Framework 的最新完整支援版本。 您可以使用 Entity Framework 的發行前版本，我們希望您可以試用並提供意見反應給我們。

若要安裝最新的 EntityFramework 預覽，您可以在 [管理 NuGet 封裝] 視窗中選取 [ **包含發行** 前版本]。 如果沒有可用的發行前版本，您會自動取得 Entity Framework 的最新完整支援版本。

![包含發行前版本](~/ef6/media/includeprerelease.png)

或者，您也可以在 [封裝管理員主控台](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)中執行下列命令。

``` powershell
Install-Package EntityFramework -Pre
```
