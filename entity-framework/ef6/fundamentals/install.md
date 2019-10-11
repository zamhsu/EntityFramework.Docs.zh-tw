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
# <a name="get-entity-framework"></a><span data-ttu-id="61320-102">取得 Entity Framework</span><span class="sxs-lookup"><span data-stu-id="61320-102">Get Entity Framework</span></span>
<span data-ttu-id="61320-103">Entity Framework 是由適用于 Visual Studio 和 EF 執行時間的 EF 工具所組成。</span><span class="sxs-lookup"><span data-stu-id="61320-103">Entity Framework is made up of the EF Tools for Visual Studio and the EF Runtime.</span></span>

## <a name="ef-tools-for-visual-studio"></a><span data-ttu-id="61320-104">適用于 Visual Studio 的 EF 工具</span><span class="sxs-lookup"><span data-stu-id="61320-104">EF Tools for Visual Studio</span></span>

<span data-ttu-id="61320-105">Visual Studio 的 Entity Framework Tools 包含 EF 設計工具和 EF 模型 Wizard，而且是資料庫 first 和 Model first 工作流程的必要參數。</span><span class="sxs-lookup"><span data-stu-id="61320-105">The Entity Framework Tools for Visual Studio include the EF Designer and the EF Model Wizard and are required for the database first and model first workflows.</span></span> <span data-ttu-id="61320-106">所有最新版本的 Visual Studio 都包含 EF 工具。</span><span class="sxs-lookup"><span data-stu-id="61320-106">EF Tools are included in all recent versions of Visual Studio.</span></span> <span data-ttu-id="61320-107">如果您執行 Visual Studio 的自訂安裝，則必須選擇包含該專案的工作負載，或將其選取為個別元件，以確保選取「Entity Framework 6 工具」。</span><span class="sxs-lookup"><span data-stu-id="61320-107">If you perform a custom install of Visual Studio you will need to ensure that the item "Entity Framework 6 Tools" is selected by either choosing a workload that includes it or by selecting it as an individual component.</span></span>

<span data-ttu-id="61320-108">針對某些過去版本的 Visual Studio，更新的 EF 工具可供下載。</span><span class="sxs-lookup"><span data-stu-id="61320-108">For some past versions of Visual Studio, updated EF Tools are available as a download.</span></span> <span data-ttu-id="61320-109">如需如何取得適用于您的 Visual Studio 版本之 EF 工具的最新版本指引，請參閱[Visual Studio 版本](~/ef6/what-is-new/visual-studio.md)。</span><span class="sxs-lookup"><span data-stu-id="61320-109">See [Visual Studio Versions](~/ef6/what-is-new/visual-studio.md) for guidance on how to get the latest version of EF Tools available for your version of Visual Studio.</span></span>

## <a name="ef-runtime"></a><span data-ttu-id="61320-110">EF 執行時間</span><span class="sxs-lookup"><span data-stu-id="61320-110">EF Runtime</span></span>

<span data-ttu-id="61320-111">最新版的 Entity Framework 是以[EntityFramework NuGet 套件](https://nuget.org/packages/EntityFramework/)的形式提供。</span><span class="sxs-lookup"><span data-stu-id="61320-111">The latest version of Entity Framework is available as the [EntityFramework NuGet package](https://nuget.org/packages/EntityFramework/).</span></span> <span data-ttu-id="61320-112">如果您不熟悉 NuGet 套件管理員，我們鼓勵您閱讀[Nuget 總覽](https://docs.microsoft.com/nuget/consume-packages/overview-and-workflow)。</span><span class="sxs-lookup"><span data-stu-id="61320-112">If you are not familiar with the NuGet Package Manager, we encourage you to read the [NuGet Overview](https://docs.microsoft.com/nuget/consume-packages/overview-and-workflow).</span></span>

### <a name="installing-the-ef-nuget-package"></a><span data-ttu-id="61320-113">安裝 EF NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="61320-113">Installing the EF NuGet Package</span></span>

<span data-ttu-id="61320-114">您可以在專案的 [**參考**] 資料夾上按一下滑鼠右鍵，然後選取 [**管理 NuGet 套件 ...** ]，以安裝 EntityFramework 套件。</span><span class="sxs-lookup"><span data-stu-id="61320-114">You can install the EntityFramework package by right-clicking on the **References** folder of your project and selecting **Manage NuGet Packages…**</span></span>

![管理 NuGet 套件](~/ef6/media/managenugetpackages.png)

### <a name="installing-from-package-manager-console"></a><span data-ttu-id="61320-116">從套件管理員主控台安裝</span><span class="sxs-lookup"><span data-stu-id="61320-116">Installing from Package Manager Console</span></span>

<span data-ttu-id="61320-117">或者，您也可以在 [[套件管理員主控台](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)] 中執行下列命令來安裝 EntityFramework。</span><span class="sxs-lookup"><span data-stu-id="61320-117">Alternatively, you can install EntityFramework by running the following command in the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span></span>

``` powershell
Install-Package EntityFramework
```

## <a name="installing-a-specific-version-of-ef"></a><span data-ttu-id="61320-118">安裝特定版本的 EF</span><span class="sxs-lookup"><span data-stu-id="61320-118">Installing a specific version of EF</span></span>

<span data-ttu-id="61320-119">從 EF 4.1 開始，新版本的 EF 執行時間已發行為[EntityFramework NuGet 套件](https://www.nuget.org/packages/EntityFramework/)。</span><span class="sxs-lookup"><span data-stu-id="61320-119">From EF 4.1 onwards, new versions of the EF runtime have been released as the [EntityFramework NuGet Package](https://www.nuget.org/packages/EntityFramework/).</span></span> <span data-ttu-id="61320-120">您可以在 Visual Studio 的[套件管理員主控台](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)中執行下列命令，以將任何版本新增至 .NET Framework 專案：</span><span class="sxs-lookup"><span data-stu-id="61320-120">Any of those versions can be added to a .NET Framework-based project by running the following command in Visual Studio's [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):</span></span>

``` powershell
Install-Package EntityFramework -Version <number>
```

<span data-ttu-id="61320-121">請注意，`<number>` 代表要安裝的 EF 特定版本。</span><span class="sxs-lookup"><span data-stu-id="61320-121">Note that `<number>` represents the specific version of EF to install.</span></span> <span data-ttu-id="61320-122">例如，6.2.0 是 EF 6.2 的數位版本。</span><span class="sxs-lookup"><span data-stu-id="61320-122">For example, 6.2.0 is the version of number for EF 6.2.</span></span>   

<span data-ttu-id="61320-123">4\.1 之前的 EF 執行時間是 .NET Framework 的一部分，無法個別安裝。</span><span class="sxs-lookup"><span data-stu-id="61320-123">EF runtimes before 4.1 were part of .NET Framework and cannot be installed separately.</span></span>

### <a name="installing-the-latest-preview"></a><span data-ttu-id="61320-124">安裝最新的預覽</span><span class="sxs-lookup"><span data-stu-id="61320-124">Installing the Latest Preview</span></span>

<span data-ttu-id="61320-125">上述方法會提供您最新的 Entity Framework 完整支援版本。</span><span class="sxs-lookup"><span data-stu-id="61320-125">The above methods will give you the latest fully supported release of Entity Framework.</span></span> <span data-ttu-id="61320-126">Entity Framework 可以使用發行前版本，我們希望您可以試用並提供意見反應給我們。</span><span class="sxs-lookup"><span data-stu-id="61320-126">There are often prerelease versions of Entity Framework available that we would love you to try out and give us feedback on.</span></span>

<span data-ttu-id="61320-127">若要安裝最新的 EntityFramework 預覽，您可以在 [管理 NuGet 套件] 視窗中選取 [**包含發行**前版本]。</span><span class="sxs-lookup"><span data-stu-id="61320-127">To install the latest preview of EntityFramework you can select **Include Prerelease** in the Manage NuGet Packages window.</span></span> <span data-ttu-id="61320-128">如果沒有可用的發行前版本，您將會自動取得 Entity Framework 的最新完整支援版本。</span><span class="sxs-lookup"><span data-stu-id="61320-128">If no prerelease versions are available you will automatically get the latest fully supported version of Entity Framework.</span></span>

![包含發行前版本](~/ef6/media/includeprerelease.png)

<span data-ttu-id="61320-130">或者，您可以在 [[套件管理員主控台](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)] 中執行下列命令。</span><span class="sxs-lookup"><span data-stu-id="61320-130">Alternatively, you can run the following command in the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span></span>

``` powershell
Install-Package EntityFramework -Pre
```
