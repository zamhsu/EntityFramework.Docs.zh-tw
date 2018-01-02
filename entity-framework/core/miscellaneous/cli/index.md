---
title: "命令列參考 - EF Core"
author: bricelam
ms.author: bricelam
ms.date: 11/6/2017
ms.technology: entity-framework-core
ms.openlocfilehash: 076e9251850ba10df323cd25922aa8b95b3a5491
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
<a name="entity-framework-core-tools"></a><span data-ttu-id="b98af-102">Entity Framework Core 工具</span><span class="sxs-lookup"><span data-stu-id="b98af-102">Entity Framework Core Tools</span></span>
===========================
<span data-ttu-id="b98af-103">Entity Framework Core 工具可在 EF Core 應用程式開發期間協助您。</span><span class="sxs-lookup"><span data-stu-id="b98af-103">The Entity Framework Core Tools help you during the development of EF Core apps.</span></span> <span data-ttu-id="b98af-104">它們主要用來將資料庫結構描述反向工程以支援 DbContext 和實體類型，以及管理「移轉」。</span><span class="sxs-lookup"><span data-stu-id="b98af-104">They're primarily used to scaffold a DbContext and entity types by reverse engineering the schema of a database, and to manage Migrations.</span></span>

<span data-ttu-id="b98af-105">[EF Core 套件管理員主控台 (PMC) 工具][1]提供 Visual Studio 內的更好經驗。</span><span class="sxs-lookup"><span data-stu-id="b98af-105">The [EF Core Package Manager Console (PMC) Tools][1] provide a superior experience inside Visual Studio.</span></span> <span data-ttu-id="b98af-106">使用 NuGet 的[套件管理員主控台][2]來執行它們。</span><span class="sxs-lookup"><span data-stu-id="b98af-106">Run them using NuGet's [Package Manager Console][2].</span></span> <span data-ttu-id="b98af-107">這些工具會使用 .NET Framework 和 .NET Core 專案。</span><span class="sxs-lookup"><span data-stu-id="b98af-107">These tools work with both .NET Framework and .NET Core projects.</span></span>

<span data-ttu-id="b98af-108">[EF Core .NET 命令列工具][3]是 [.NET Core 命令列介面 (CLI) 工具][4]的延伸模組，而後者跨平台，而且可以在 Visual Studio 外部執行。</span><span class="sxs-lookup"><span data-stu-id="b98af-108">The [EF Core .NET Command-line Tools][3] are an extension to the [.NET Core command-line interface (CLI) tools][4] that are cross-platform and can run outside of Visual Studio.</span></span> <span data-ttu-id="b98af-109">這些工具需要 .NET Core SDK 專案 (專案檔中含 `Sdk="Microsoft.NET.Sdk"` 或類似項目的專案)。</span><span class="sxs-lookup"><span data-stu-id="b98af-109">These tools require a .NET Core SDK project (one with `Sdk="Microsoft.NET.Sdk"` or similar in the project file).</span></span>

<span data-ttu-id="b98af-110">這兩個工具都會公開相同的功能。</span><span class="sxs-lookup"><span data-stu-id="b98af-110">Both tools expose the same functionality.</span></span> <span data-ttu-id="b98af-111">如果您要在 Visual Studio 中進行開發，則建議使用 PMC 工具，因為它們提供更加整合的體驗。</span><span class="sxs-lookup"><span data-stu-id="b98af-111">If you're developing in Visual Studio, we recommend using the PMC Tools since they provide a more integrated experience.</span></span>

<a name="frameworks"></a><span data-ttu-id="b98af-112">架構</span><span class="sxs-lookup"><span data-stu-id="b98af-112">Frameworks</span></span>
----------
<span data-ttu-id="b98af-113">這些工具還支援目標設為 .NET Framework 或 .NET Core 的專案。</span><span class="sxs-lookup"><span data-stu-id="b98af-113">The tools support projects targeting .NET Framework or .NET Core.</span></span>

<span data-ttu-id="b98af-114">如果您專案的目標設為另一個架構 (例如，通用 Windows 或 Xamarin)，則建議建立所支援架構的不同 .NET Standard 專案和跨目標專案。</span><span class="sxs-lookup"><span data-stu-id="b98af-114">If your project targets another framework (for example, Universal Windows or Xamarin), we recommend creating a separate .NET Standard project and cross-targeting one of the supported frameworks.</span></span>

<span data-ttu-id="b98af-115">若要將 .NET Core 設為跨目標 (例如，以滑鼠右鍵按一下專案，然後選取 [編輯 \*.csproj]。</span><span class="sxs-lookup"><span data-stu-id="b98af-115">To cross-target .NET Core, for example, right-click on the project and select **Edit \*.csproj**.</span></span> <span data-ttu-id="b98af-116">更新 `TargetFramework` 屬性，如下所示 </span><span class="sxs-lookup"><span data-stu-id="b98af-116">Update the `TargetFramework` property as follows.</span></span> <span data-ttu-id="b98af-117">(請注意，屬性名稱會變成複數)。</span><span class="sxs-lookup"><span data-stu-id="b98af-117">(Note, the property name becomes plural.)</span></span>

``` xml
<TargetFrameworks>netcoreapp2.0;netstandard2.0</TargetFrameworks>
```

<span data-ttu-id="b98af-118">如果您要使用 .NET Standard 類別庫，則您的啟始專案目標設為 .NET Framework 或 .NET Core 時不需要跨目標。</span><span class="sxs-lookup"><span data-stu-id="b98af-118">If you're using a .NET Standard class library, you don't need to cross-target if your startup project targets .NET Framework or .NET Core.</span></span>

<a name="startup-and-target-projects"></a><span data-ttu-id="b98af-119">啟始和目標專案</span><span class="sxs-lookup"><span data-stu-id="b98af-119">Startup and Target Projects</span></span>
---------------------------
<span data-ttu-id="b98af-120">只要您叫用命令，就會包含兩個專案：目標專案和啟始專案。</span><span class="sxs-lookup"><span data-stu-id="b98af-120">Whenever you invoke a command, there are two projects involved: the target project and the startup project.</span></span>

<span data-ttu-id="b98af-121">目標專案就是新增 (在某些情況下會移除) 任何檔案的位置。</span><span class="sxs-lookup"><span data-stu-id="b98af-121">The target project is where any files are added (or in some cases removed).</span></span>

<span data-ttu-id="b98af-122">啟始專案是工具在執行您的專案程式碼時所模擬的專案。</span><span class="sxs-lookup"><span data-stu-id="b98af-122">The startup project is the one emulated by the tools when executing your project's code.</span></span>

<span data-ttu-id="b98af-123">目標專案和啟始專案可以相同。</span><span class="sxs-lookup"><span data-stu-id="b98af-123">Both the target project and the startup project can be the same.</span></span>


  [1]: powershell.md
  [2]: https://docs.microsoft.com/nuget/tools/package-manager-console
  [3]: dotnet.md
  [4]: https://docs.microsoft.com/dotnet/core/tools/
