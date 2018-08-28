---
title: 取得 Entity Framework 的 EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 122c38a2-f9e8-4ecc-9c72-a83bc9af7814
ms.openlocfilehash: 78ef1e7b20bd879c972870552c8f692e153b7abb
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996561"
---
# <a name="get-entity-framework"></a><span data-ttu-id="a5ef8-102">取得 Entity Framework</span><span class="sxs-lookup"><span data-stu-id="a5ef8-102">Get Entity Framework</span></span>
<span data-ttu-id="a5ef8-103">Entity Framework 是組成的 EF 工具適用於 Visual Studio 和 EF 執行階段。</span><span class="sxs-lookup"><span data-stu-id="a5ef8-103">Entity Framework is made up of the EF Tools for Visual Studio and the EF Runtime.</span></span>

## <a name="ef-tools-for-visual-studio"></a><span data-ttu-id="a5ef8-104">適用於 Visual Studio 的 EF 工具</span><span class="sxs-lookup"><span data-stu-id="a5ef8-104">EF Tools for Visual Studio</span></span>

<span data-ttu-id="a5ef8-105">Entity Framework Tools for Visual Studio 包含 EF 設計工具和 EF 模型精靈 和所需的資料庫的第一次，並建立第一個工作流程的模型。</span><span class="sxs-lookup"><span data-stu-id="a5ef8-105">The Entity Framework Tools for Visual Studio include the EF Designer and the EF Model Wizard and are required for the database first and model first workflows.</span></span> <span data-ttu-id="a5ef8-106">EF 工具包含在所有的最新版本的 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="a5ef8-106">EF Tools are included in all recent versions of Visual Studio.</span></span> <span data-ttu-id="a5ef8-107">如果您執行自訂安裝的 Visual Studio，您必須確定項目選擇包含它的工作負載，或藉由選取為 個別元件，會選取 「 Entity Framework 6 工具 」。</span><span class="sxs-lookup"><span data-stu-id="a5ef8-107">If you perform a custom install of Visual Studio you will need to ensure that the item "Entity Framework 6 Tools" is selected by either choosing a workload that includes it or by selecting it as an individual component.</span></span>

<span data-ttu-id="a5ef8-108">針對某些舊版的 Visual Studio 中，更新的 EF 工具是可供下載。</span><span class="sxs-lookup"><span data-stu-id="a5ef8-108">For some past versions of Visual Studio, updated EF Tools are available as a download.</span></span> <span data-ttu-id="a5ef8-109">請參閱[Visual Studio 版本](~/ef6/what-is-new/visual-studio.md)如需如何取得最新版的 EF 工具可以使用您的 Visual Studio 版本適用的指引。</span><span class="sxs-lookup"><span data-stu-id="a5ef8-109">See [Visual Studio Versions](~/ef6/what-is-new/visual-studio.md) for guidance on how to get the latest version of EF Tools available for your version of Visual Studio.</span></span>

## <a name="ef-runtime"></a><span data-ttu-id="a5ef8-110">EF 執行階段</span><span class="sxs-lookup"><span data-stu-id="a5ef8-110">EF Runtime</span></span>

<span data-ttu-id="a5ef8-111">最新版的 Entity Framework 可從[EntityFramework NuGet 套件](http://nuget.org/packages/EntityFramework/)。</span><span class="sxs-lookup"><span data-stu-id="a5ef8-111">The latest version of Entity Framework is available as the [EntityFramework NuGet package](http://nuget.org/packages/EntityFramework/).</span></span> <span data-ttu-id="a5ef8-112">如果您還不熟悉如何使用 NuGet 封裝管理員中，我們鼓勵您閱讀[NuGet 概觀](https://docs.microsoft.com/nuget/consume-packages/overview-and-workflow)。</span><span class="sxs-lookup"><span data-stu-id="a5ef8-112">If you are not familiar with the NuGet Package Manager, we encourage you to read the [NuGet Overview](https://docs.microsoft.com/nuget/consume-packages/overview-and-workflow).</span></span>

### <a name="installing-the-ef-nuget-package"></a><span data-ttu-id="a5ef8-113">安裝 EF NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="a5ef8-113">Installing the EF NuGet Package</span></span>

<span data-ttu-id="a5ef8-114">您可以安裝 EntityFramework 套件上按一下滑鼠右鍵**參考**專案的資料夾，然後選取**管理 NuGet 套件...**</span><span class="sxs-lookup"><span data-stu-id="a5ef8-114">You can install the EntityFramework package by right-clicking on the **References** folder of your project and selecting **Manage NuGet Packages…**</span></span>

![ManageNuGetPackages](~/ef6/media/managenugetpackages.png)

### <a name="installing-from-package-manager-console"></a><span data-ttu-id="a5ef8-116">從 套件管理員主控台安裝</span><span class="sxs-lookup"><span data-stu-id="a5ef8-116">Installing from Package Manager Console</span></span>

<span data-ttu-id="a5ef8-117">或者，您可以在中執行下列命令安裝 EntityFramework [Package Manager Console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console)。</span><span class="sxs-lookup"><span data-stu-id="a5ef8-117">Alternatively, you can install EntityFramework by running the following command in the [Package Manager Console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span></span>

``` powershell
Install-Package EntityFramework
```

## <a name="installing-a-specific-version-of-ef"></a><span data-ttu-id="a5ef8-118">安裝特定版本的 EF</span><span class="sxs-lookup"><span data-stu-id="a5ef8-118">Installing a specific version of EF</span></span>

<span data-ttu-id="a5ef8-119">從 EF 4.1 及更新版本的 EF 執行階段的新版本已發行過[EntityFramework NuGet 套件](https://www.nuget.org/packages/EntityFramework/)。</span><span class="sxs-lookup"><span data-stu-id="a5ef8-119">From EF 4.1 onwards, new versions of the EF runtime have been released as the [EntityFramework NuGet Pacakge](https://www.nuget.org/packages/EntityFramework/).</span></span> <span data-ttu-id="a5ef8-120">這些版本其中任何一個可以在 Visual Studio 中執行下列命令新增至.NET Framework 為基礎的專案[Package Manager Console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console):</span><span class="sxs-lookup"><span data-stu-id="a5ef8-120">Any of those versions can be added to a .NET Framework-based project by running the following command in Visual Studio's [Package Manager Console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console):</span></span>

``` powershell
Install-Package EntityFramework -Version <number>
```

<span data-ttu-id="a5ef8-121">請注意，`<number>`代表特定版本的 EF 來安裝。</span><span class="sxs-lookup"><span data-stu-id="a5ef8-121">Note that `<number>` represents the specific version of EF to install.</span></span> <span data-ttu-id="a5ef8-122">比方說，6.2.0 是 EF 6.2 的數字的版本。</span><span class="sxs-lookup"><span data-stu-id="a5ef8-122">For example, 6.2.0 is the version of number for EF 6.2.</span></span>   

<span data-ttu-id="a5ef8-123">4.1 之前的 EF 執行階段的.NET Framework 的一部分，無法個別安裝。</span><span class="sxs-lookup"><span data-stu-id="a5ef8-123">EF runtimes before 4.1 were part of .NET Framework and cannot be installed separately.</span></span>

### <a name="installing-the-latest-preview"></a><span data-ttu-id="a5ef8-124">安裝最新的預覽版</span><span class="sxs-lookup"><span data-stu-id="a5ef8-124">Installing the Latest Preview</span></span>

<span data-ttu-id="a5ef8-125">上述方法可讓您最新完整支援 Entity framework 的版本。</span><span class="sxs-lookup"><span data-stu-id="a5ef8-125">The above methods will give you the latest fully supported release of Entity Framework.</span></span> <span data-ttu-id="a5ef8-126">通常是發行前版本的 Entity Framework 提供我們由衷歡迎您試用並提供我們意見反應。</span><span class="sxs-lookup"><span data-stu-id="a5ef8-126">There are often prerelease versions of Entity Framework available that we would love you to try out and give us feedback on.</span></span>

<span data-ttu-id="a5ef8-127">若要安裝的 EntityFramework 您可以選取最新的預覽版**包含發行前版本**在 [管理 NuGet 封裝] 視窗。</span><span class="sxs-lookup"><span data-stu-id="a5ef8-127">To install the latest preview of EntityFramework you can select **Include Prerelease** in the Manage NuGet Packages window.</span></span> <span data-ttu-id="a5ef8-128">如果不有可用的任何發行前版本將會自動取得最新版的 Entity Framework 的完整支援的版本。</span><span class="sxs-lookup"><span data-stu-id="a5ef8-128">If no prerelease versions are available you will automatically get the latest fully supported version of Entity Framework.</span></span>

![IncludePreRelease](~/ef6/media/includeprerelease.png)

<span data-ttu-id="a5ef8-130">或者，您可以執行下列命令[Package Manager Console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console)。</span><span class="sxs-lookup"><span data-stu-id="a5ef8-130">Alternatively, you can run the following command in the [Package Manager Console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span></span>

``` powershell
Install-Package EntityFramework -Pre
```
