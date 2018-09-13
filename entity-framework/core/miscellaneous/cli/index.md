---
title: 命令列參考 - EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/06/2017
ms.openlocfilehash: d43b01fc61bb1c9b678e12e41c27d7efe9a59fa5
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490359"
---
<a name="entity-framework-core-tools"></a><span data-ttu-id="c005f-102">Entity Framework Core 工具</span><span class="sxs-lookup"><span data-stu-id="c005f-102">Entity Framework Core Tools</span></span>
===========================
<span data-ttu-id="c005f-103">Entity Framework Core 工具可在 EF Core 應用程式開發期間協助您。</span><span class="sxs-lookup"><span data-stu-id="c005f-103">The Entity Framework Core Tools help you during the development of EF Core apps.</span></span> <span data-ttu-id="c005f-104">它們主要用來將資料庫結構描述反向工程以支援 DbContext 和實體類型，以及管理「移轉」。</span><span class="sxs-lookup"><span data-stu-id="c005f-104">They're primarily used to scaffold a DbContext and entity types by reverse engineering the schema of a database, and to manage Migrations.</span></span>

<span data-ttu-id="c005f-105">[EF Core 套件管理員主控台 (PMC) 工具][1]提供 Visual Studio 內的更好經驗。</span><span class="sxs-lookup"><span data-stu-id="c005f-105">The [EF Core Package Manager Console (PMC) Tools][1] provide a superior experience inside Visual Studio.</span></span> <span data-ttu-id="c005f-106">使用 NuGet 的[套件管理員主控台][2]來執行它們。</span><span class="sxs-lookup"><span data-stu-id="c005f-106">Run them using NuGet's [Package Manager Console][2].</span></span> <span data-ttu-id="c005f-107">這些工具會使用 .NET Framework 和 .NET Core 專案。</span><span class="sxs-lookup"><span data-stu-id="c005f-107">These tools work with both .NET Framework and .NET Core projects.</span></span>

<span data-ttu-id="c005f-108">[EF Core .NET 命令列工具][3]是 [.NET Core 命令列介面 (CLI) 工具][4]的延伸模組，而後者跨平台，而且可以在 Visual Studio 外部執行。</span><span class="sxs-lookup"><span data-stu-id="c005f-108">The [EF Core .NET Command-line Tools][3] are an extension to the [.NET Core command-line interface (CLI) tools][4] that are cross-platform and can run outside of Visual Studio.</span></span> <span data-ttu-id="c005f-109">這些工具需要 .NET Core SDK 專案 (專案檔中含 `Sdk="Microsoft.NET.Sdk"` 或類似項目的專案)。</span><span class="sxs-lookup"><span data-stu-id="c005f-109">These tools require a .NET Core SDK project (one with `Sdk="Microsoft.NET.Sdk"` or similar in the project file).</span></span>

<span data-ttu-id="c005f-110">這兩個工具都會公開相同的功能。</span><span class="sxs-lookup"><span data-stu-id="c005f-110">Both tools expose the same functionality.</span></span> <span data-ttu-id="c005f-111">如果您要在 Visual Studio 中進行開發，則建議使用 PMC 工具，因為它們提供更加整合的體驗。</span><span class="sxs-lookup"><span data-stu-id="c005f-111">If you're developing in Visual Studio, we recommend using the PMC Tools since they provide a more integrated experience.</span></span>

<a name="frameworks"></a><span data-ttu-id="c005f-112">架構</span><span class="sxs-lookup"><span data-stu-id="c005f-112">Frameworks</span></span>
----------
<span data-ttu-id="c005f-113">這些工具還支援目標設為 .NET Framework 或 .NET Core 的專案。</span><span class="sxs-lookup"><span data-stu-id="c005f-113">The tools support projects targeting .NET Framework or .NET Core.</span></span>

<span data-ttu-id="c005f-114">若您想要使用類別程式庫，請在可行的情況下使用 .NET Core 或 .NET Framework 類別程式庫。</span><span class="sxs-lookup"><span data-stu-id="c005f-114">If you want to use a class library, then consider using a .NET Core or .NET Framework class library if possible.</span></span> <span data-ttu-id="c005f-115">這會使 .NET 工具產生較少問題。</span><span class="sxs-lookup"><span data-stu-id="c005f-115">This will result in the least issues with .NET tooling.</span></span> <span data-ttu-id="c005f-116">而如果您想要使用 .NET Standard 類別程式庫，則需要以 .NET Framework 或 .Net Core 為目標的啟動專案，使工具擁有具體的目標平台來上傳您的類別程式庫。</span><span class="sxs-lookup"><span data-stu-id="c005f-116">If instead you wish to use a .NET Standard class library, then you will need to use a startup project that targets .NET Framework or .NET Core so that the tooling has a conrete target platform into which it can load your class library.</span></span> <span data-ttu-id="c005f-117">您可使用實際沒有程式碼的虛設專案作為啟動專案，此專案的目的僅是為工具提供目標。</span><span class="sxs-lookup"><span data-stu-id="c005f-117">This startup project can be a dummy project with no real code--it is only needed to provide a target for the tooling.</span></span>

<span data-ttu-id="c005f-118">若您的專案將另一個架構設為目標 (例如通用 Windows 或 Xamarin)，則必須建立個別的 .NET Standard 類別程式庫。</span><span class="sxs-lookup"><span data-stu-id="c005f-118">If your project targets another framework (for example, Universal Windows or Xamarin), then you will need to create a separate .NET Standard class library.</span></span> <span data-ttu-id="c005f-119">在此情況下，請遵循上述指導，並建立可供工具使用的啟動專案。</span><span class="sxs-lookup"><span data-stu-id="c005f-119">In this case, follow the guidance above to also create a startup project that can be used by the tooling.</span></span>

<a name="startup-and-target-projects"></a><span data-ttu-id="c005f-120">啟始和目標專案</span><span class="sxs-lookup"><span data-stu-id="c005f-120">Startup and Target Projects</span></span>
---------------------------
<span data-ttu-id="c005f-121">只要您叫用命令，就會包含兩個專案：目標專案和啟始專案。</span><span class="sxs-lookup"><span data-stu-id="c005f-121">Whenever you invoke a command, there are two projects involved: the target project and the startup project.</span></span>

<span data-ttu-id="c005f-122">目標專案就是新增 (在某些情況下會移除) 任何檔案的位置。</span><span class="sxs-lookup"><span data-stu-id="c005f-122">The target project is where any files are added (or in some cases removed).</span></span>

<span data-ttu-id="c005f-123">啟始專案是工具在執行您的專案程式碼時所模擬的專案。</span><span class="sxs-lookup"><span data-stu-id="c005f-123">The startup project is the one emulated by the tools when executing your project's code.</span></span>

<span data-ttu-id="c005f-124">目標專案和啟始專案可以相同。</span><span class="sxs-lookup"><span data-stu-id="c005f-124">Both the target project and the startup project can be the same.</span></span>


  [1]: powershell.md
  [2]: https://docs.microsoft.com/nuget/tools/package-manager-console
  [3]: dotnet.md
  [4]: https://docs.microsoft.com/dotnet/core/tools/
