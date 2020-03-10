---
title: 新功能 - EF6
author: divega
ms.date: 09/12/2019
ms.assetid: 41d1f86b-ce66-4bf2-8963-48514406fb4c
uid: ef6/what-is-new/index
ms.openlocfilehash: 9daae787d0cec0ca536413e6263bb363ba76ff2c
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413413"
---
# <a name="whats-new-in-ef6"></a><span data-ttu-id="1e4a5-102">EF6 的新功能</span><span class="sxs-lookup"><span data-stu-id="1e4a5-102">What's new in EF6</span></span>

<span data-ttu-id="1e4a5-103">強烈建議您使用 Entity Framework 的最新發行版本，以確保您取得最新的功能和最高的穩定性。</span><span class="sxs-lookup"><span data-stu-id="1e4a5-103">We highly recommend that you use the latest released version of Entity Framework to ensure you get the latest features and the highest stability.</span></span>
<span data-ttu-id="1e4a5-104">不過，我們了解您可能需要使用先前的版本，或者可能想要體驗最新發行前版本中的新改善。</span><span class="sxs-lookup"><span data-stu-id="1e4a5-104">However, we realize that you may need to use a previous version, or that you may want to experiment with new improvements in the latest pre-release.</span></span>
<span data-ttu-id="1e4a5-105">若要安裝特定版本的 EF，請參閱[取得 Entity Framework](~/ef6/fundamentals/install.md)。</span><span class="sxs-lookup"><span data-stu-id="1e4a5-105">To install specific versions of EF, see [Get Entity Framework](~/ef6/fundamentals/install.md).</span></span>

## <a name="ef-630"></a><span data-ttu-id="1e4a5-106">EF 6.3.0</span><span class="sxs-lookup"><span data-stu-id="1e4a5-106">EF 6.3.0</span></span>

<span data-ttu-id="1e4a5-107">EF 6.3.0 執行階段已於 2019 年 9 月發行至 NuGet。</span><span class="sxs-lookup"><span data-stu-id="1e4a5-107">The EF 6.3.0 runtime was released to NuGet in September 2019.</span></span> <span data-ttu-id="1e4a5-108">此版本主要目標為將使用 EF 6 的現有應用程式促進移轉至 .NET Core 3.0。</span><span class="sxs-lookup"><span data-stu-id="1e4a5-108">The main goal of this release was to facilitate migrating existing applications that use EF 6 to .NET Core 3.0.</span></span> <span data-ttu-id="1e4a5-109">社群也貢獻了一些 Bug 修正和增強功能。</span><span class="sxs-lookup"><span data-stu-id="1e4a5-109">The community has also contributed several bug fixes and enhancements.</span></span> <span data-ttu-id="1e4a5-110">有關詳細資料，請參閱每個 6.3.0 [里程碑](https://github.com/aspnet/EntityFramework6/milestones?state=closed)中已關閉的問題。</span><span class="sxs-lookup"><span data-stu-id="1e4a5-110">See the issues closed in each 6.3.0 [milestone](https://github.com/aspnet/EntityFramework6/milestones?state=closed) for details.</span></span> <span data-ttu-id="1e4a5-111">以下是一些較需注意的事項：</span><span class="sxs-lookup"><span data-stu-id="1e4a5-111">Here are some of the more notable ones:</span></span>

- <span data-ttu-id="1e4a5-112">支援 .NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="1e4a5-112">Support for .NET Core 3.0</span></span>
  - <span data-ttu-id="1e4a5-113">現在除了 .NET Framework 4.x 以外，EntityFramework 套件也以 .NET Standard 2.1 為目標。</span><span class="sxs-lookup"><span data-stu-id="1e4a5-113">The EntityFramework package now targets .NET Standard 2.1 in addition to .NET Framework 4.x.</span></span>
  - <span data-ttu-id="1e4a5-114">這表示 EF 6.3 可以跨平台，在 Windows 以外的其他作業系統上獲得支援，例如 Linux 和 macOS。</span><span class="sxs-lookup"><span data-stu-id="1e4a5-114">This means that EF 6.3 is cross-platform and supported on other operating systems besides Windows, like Linux and macOS.</span></span>
  - <span data-ttu-id="1e4a5-115">移轉命令已經過重寫，改為在處理序外執行，並使用 SDK 樣式的專案。</span><span class="sxs-lookup"><span data-stu-id="1e4a5-115">The migrations commands have been rewritten to execute out of process and work with SDK-style projects.</span></span>
- <span data-ttu-id="1e4a5-116">對 SQL Server HierarchyId 的支援。</span><span class="sxs-lookup"><span data-stu-id="1e4a5-116">Support for SQL Server HierarchyId.</span></span>
- <span data-ttu-id="1e4a5-117">已改善與 Roslyn 和 NuGet PackageReference 的相容性。</span><span class="sxs-lookup"><span data-stu-id="1e4a5-117">Improved compatibility with Roslyn and NuGet PackageReference.</span></span>
- <span data-ttu-id="1e4a5-118">已新增 `ef6.exe` 公用程式，用於從組件啟用、新增、撰寫指令碼並套用移轉。</span><span class="sxs-lookup"><span data-stu-id="1e4a5-118">Added `ef6.exe` utility for enabling, adding, scripting, and applying migrations from assemblies.</span></span> <span data-ttu-id="1e4a5-119">這取代了 `migrate.exe`。</span><span class="sxs-lookup"><span data-stu-id="1e4a5-119">This replaces `migrate.exe`.</span></span>

### <a name="ef-designer-support"></a><span data-ttu-id="1e4a5-120">EF 設計工具支援</span><span class="sxs-lookup"><span data-stu-id="1e4a5-120">EF designer support</span></span>

<span data-ttu-id="1e4a5-121">目前不支援直接在 .NET Core 或 .NET Standard 專案上使用 EF 設計工具。</span><span class="sxs-lookup"><span data-stu-id="1e4a5-121">There's currently no support for using the EF designer directly on .NET Core or .NET Standard projects.</span></span> 

<span data-ttu-id="1e4a5-122">您可以透過新增 EDMX 檔案與實體的產生類別和 DbContext 作為連結的檔案至相同方案中的 .NET Core 3.0 或 .NET Standard 2.1 專案來作為因應措施。</span><span class="sxs-lookup"><span data-stu-id="1e4a5-122">You can work around this limitation by adding the EDMX file and the generated classes for the entities and the DbContext as linked files to a .NET Core 3.0 or .NET Standard 2.1 project in the same solution.</span></span>

<span data-ttu-id="1e4a5-123">在專案檔中，連結的檔案看起來像下面這樣：</span><span class="sxs-lookup"><span data-stu-id="1e4a5-123">The linked files will look like this in the project file:</span></span>

``` csproj 
<ItemGroup>
  <EntityDeploy Include="..\EdmxDesignHost\Entities.edmx" Link="Model\Entities.edmx" />
  <Compile Include="..\EdmxDesignHost\Entities.Context.cs" Link="Model\Entities.Context.cs" />
  <Compile Include="..\EdmxDesignHost\Thing.cs" Link="Model\Thing.cs" />
  <Compile Include="..\EdmxDesignHost\Person.cs" Link="Model\Person.cs" />
</ItemGroup>
```

<span data-ttu-id="1e4a5-124">請注意，EDMX 檔案是使用 EntityDeploy 建置動作連結的。</span><span class="sxs-lookup"><span data-stu-id="1e4a5-124">Note that the EDMX file is linked with the EntityDeploy build action.</span></span> <span data-ttu-id="1e4a5-125">這是特殊 MSBuild 工作 (現在包括在 EF 6.3 套件中)，它會負責新增 EF 模組到目標組件作為內嵌資源 (或將它複製為輸出資料夾中的檔案，視 EDMX 中的中繼資料成品處理設定而定)。</span><span class="sxs-lookup"><span data-stu-id="1e4a5-125">This is a special MSBuild task (now included in the EF 6.3 package) that takes care of adding the EF model into the target assembly as embedded resources (or copying it as files in the output folder, depending on the Metadata Artifact Processing setting in the EDMX).</span></span> <span data-ttu-id="1e4a5-126">如需有關如何設定的詳細資訊，請參閱我們的 [EDMX .NET Core 範例](https://aka.ms/EdmxDotNetCoreSample)。</span><span class="sxs-lookup"><span data-stu-id="1e4a5-126">For more details on how to get this set up, see our [EDMX .NET Core sample](https://aka.ms/EdmxDotNetCoreSample).</span></span>

## <a name="past-releases"></a><span data-ttu-id="1e4a5-127">過去版本</span><span class="sxs-lookup"><span data-stu-id="1e4a5-127">Past Releases</span></span>

<span data-ttu-id="1e4a5-128">[過去版本](past-releases.md)頁面包含所有舊版 EF 與每一版所引進之主要功能的封存。</span><span class="sxs-lookup"><span data-stu-id="1e4a5-128">The [Past Releases](past-releases.md) page contains an archive of all previous versions of EF and the major features that were introduced on each release.</span></span>
