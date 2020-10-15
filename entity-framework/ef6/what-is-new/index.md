---
title: 新功能 - EF6
description: Entity Framework 6 的新功能
author: ajcvickers
ms.date: 09/12/2019
uid: ef6/what-is-new/index
ms.openlocfilehash: 3c588475268ea063433f55fdb2f994d70f8c51ae
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064298"
---
# <a name="whats-new-in-ef6"></a><span data-ttu-id="5b155-103">EF6 的新功能</span><span class="sxs-lookup"><span data-stu-id="5b155-103">What's new in EF6</span></span>

<span data-ttu-id="5b155-104">強烈建議您使用 Entity Framework 的最新發行版本，以確保您取得最新的功能和最高的穩定性。</span><span class="sxs-lookup"><span data-stu-id="5b155-104">We highly recommend that you use the latest released version of Entity Framework to ensure you get the latest features and the highest stability.</span></span>
<span data-ttu-id="5b155-105">不過，我們了解您可能需要使用先前的版本，或者可能想要體驗最新發行前版本中的新改善。</span><span class="sxs-lookup"><span data-stu-id="5b155-105">However, we realize that you may need to use a previous version, or that you may want to experiment with new improvements in the latest pre-release.</span></span>
<span data-ttu-id="5b155-106">若要安裝特定版本的 EF，請參閱[取得 Entity Framework](xref:ef6/fundamentals/install)。</span><span class="sxs-lookup"><span data-stu-id="5b155-106">To install specific versions of EF, see [Get Entity Framework](xref:ef6/fundamentals/install).</span></span>

## <a name="ef-640"></a><span data-ttu-id="5b155-107">EF 6.4.0</span><span class="sxs-lookup"><span data-stu-id="5b155-107">EF 6.4.0</span></span>

<span data-ttu-id="5b155-108">EF 6.4.0 執行階段已於 2019 年 12 月發行至 NuGet。</span><span class="sxs-lookup"><span data-stu-id="5b155-108">The EF 6.4.0 runtime was released to NuGet in December  2019.</span></span> <span data-ttu-id="5b155-109">EF 6.4 的主要目標在改進 EF 6.3 提供的功能與案例。</span><span class="sxs-lookup"><span data-stu-id="5b155-109">The primary goal of EF 6.4 is to polish the features and scenarios that was delivered in EF 6.3.</span></span> <span data-ttu-id="5b155-110">請參閱 Github 的 [重要修正清單](https://github.com/dotnet/ef6/milestone/14?closed=1)。</span><span class="sxs-lookup"><span data-stu-id="5b155-110">See [list of important fixes](https://github.com/dotnet/ef6/milestone/14?closed=1) on Github.</span></span>

## <a name="ef-630"></a><span data-ttu-id="5b155-111">EF 6.3.0</span><span class="sxs-lookup"><span data-stu-id="5b155-111">EF 6.3.0</span></span>

<span data-ttu-id="5b155-112">EF 6.3.0 執行階段已於 2019 年 9 月發行至 NuGet。</span><span class="sxs-lookup"><span data-stu-id="5b155-112">The EF 6.3.0 runtime was released to NuGet in September 2019.</span></span> <span data-ttu-id="5b155-113">此版本主要目標為將使用 EF 6 的現有應用程式促進移轉至 .NET Core 3.0。</span><span class="sxs-lookup"><span data-stu-id="5b155-113">The main goal of this release was to facilitate migrating existing applications that use EF 6 to .NET Core 3.0.</span></span> <span data-ttu-id="5b155-114">社群也貢獻了一些 Bug 修正和增強功能。</span><span class="sxs-lookup"><span data-stu-id="5b155-114">The community has also contributed several bug fixes and enhancements.</span></span> <span data-ttu-id="5b155-115">有關詳細資料，請參閱每個 6.3.0 [里程碑](https://github.com/aspnet/EntityFramework6/milestones?state=closed)中已關閉的問題。</span><span class="sxs-lookup"><span data-stu-id="5b155-115">See the issues closed in each 6.3.0 [milestone](https://github.com/aspnet/EntityFramework6/milestones?state=closed) for details.</span></span> <span data-ttu-id="5b155-116">以下是一些較需注意的事項：</span><span class="sxs-lookup"><span data-stu-id="5b155-116">Here are some of the more notable ones:</span></span>

- <span data-ttu-id="5b155-117">支援 .NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="5b155-117">Support for .NET Core 3.0</span></span>
  - <span data-ttu-id="5b155-118">現在除了 .NET Framework 4.x 以外，EntityFramework 套件也以 .NET Standard 2.1 為目標。</span><span class="sxs-lookup"><span data-stu-id="5b155-118">The EntityFramework package now targets .NET Standard 2.1 in addition to .NET Framework 4.x.</span></span>
  - <span data-ttu-id="5b155-119">這表示 EF 6.3 可以跨平台，在 Windows 以外的其他作業系統上獲得支援，例如 Linux 和 macOS。</span><span class="sxs-lookup"><span data-stu-id="5b155-119">This means that EF 6.3 is cross-platform and supported on other operating systems besides Windows, like Linux and macOS.</span></span>
  - <span data-ttu-id="5b155-120">移轉命令已經過重寫，改為在處理序外執行，並使用 SDK 樣式的專案。</span><span class="sxs-lookup"><span data-stu-id="5b155-120">The migrations commands have been rewritten to execute out of process and work with SDK-style projects.</span></span>
- <span data-ttu-id="5b155-121">對 SQL Server HierarchyId 的支援。</span><span class="sxs-lookup"><span data-stu-id="5b155-121">Support for SQL Server HierarchyId.</span></span>
- <span data-ttu-id="5b155-122">已改善與 Roslyn 和 NuGet PackageReference 的相容性。</span><span class="sxs-lookup"><span data-stu-id="5b155-122">Improved compatibility with Roslyn and NuGet PackageReference.</span></span>
- <span data-ttu-id="5b155-123">已新增 `ef6.exe` 公用程式，用於從組件啟用、新增、撰寫指令碼並套用移轉。</span><span class="sxs-lookup"><span data-stu-id="5b155-123">Added `ef6.exe` utility for enabling, adding, scripting, and applying migrations from assemblies.</span></span> <span data-ttu-id="5b155-124">這取代了 `migrate.exe`。</span><span class="sxs-lookup"><span data-stu-id="5b155-124">This replaces `migrate.exe`.</span></span>

### <a name="ef-designer-support"></a><span data-ttu-id="5b155-125">EF 設計工具支援</span><span class="sxs-lookup"><span data-stu-id="5b155-125">EF designer support</span></span>

<span data-ttu-id="5b155-126">目前不支援在 .NET Core、.NET Standard 專案或 SDK 樣式的 .NET Framework 專案中，直接使用 EF 設計工具。</span><span class="sxs-lookup"><span data-stu-id="5b155-126">There's currently no support for using the EF designer directly on .NET Core or .NET Standard projects or on an SDK-style .NET Framework project.</span></span> 

<span data-ttu-id="5b155-127">您可以透過新增 EDMX 檔案與實體的產生類別和 DbContext 作為連結的檔案至相同方案中的 .NET Core 3.0 或 .NET Standard 2.1 專案來作為因應措施。</span><span class="sxs-lookup"><span data-stu-id="5b155-127">You can work around this limitation by adding the EDMX file and the generated classes for the entities and the DbContext as linked files to a .NET Core 3.0 or .NET Standard 2.1 project in the same solution.</span></span>

<span data-ttu-id="5b155-128">在專案檔中，連結的檔案看起來像下面這樣：</span><span class="sxs-lookup"><span data-stu-id="5b155-128">The linked files will look like this in the project file:</span></span>

``` csproj 
<ItemGroup>
  <EntityDeploy Include="..\EdmxDesignHost\Entities.edmx" Link="Model\Entities.edmx" />
  <Compile Include="..\EdmxDesignHost\Entities.Context.cs" Link="Model\Entities.Context.cs" />
  <Compile Include="..\EdmxDesignHost\Thing.cs" Link="Model\Thing.cs" />
  <Compile Include="..\EdmxDesignHost\Person.cs" Link="Model\Person.cs" />
</ItemGroup>
```

<span data-ttu-id="5b155-129">請注意，EDMX 檔案是使用 EntityDeploy 建置動作連結的。</span><span class="sxs-lookup"><span data-stu-id="5b155-129">Note that the EDMX file is linked with the EntityDeploy build action.</span></span> <span data-ttu-id="5b155-130">這是特殊 MSBuild 工作 (現在包括在 EF 6.3 套件中)，它會負責新增 EF 模組到目標組件作為內嵌資源 (或將它複製為輸出資料夾中的檔案，視 EDMX 中的中繼資料成品處理設定而定)。</span><span class="sxs-lookup"><span data-stu-id="5b155-130">This is a special MSBuild task (now included in the EF 6.3 package) that takes care of adding the EF model into the target assembly as embedded resources (or copying it as files in the output folder, depending on the Metadata Artifact Processing setting in the EDMX).</span></span> <span data-ttu-id="5b155-131">如需有關如何設定的詳細資訊，請參閱我們的 [EDMX .NET Core 範例](https://aka.ms/EdmxDotNetCoreSample)。</span><span class="sxs-lookup"><span data-stu-id="5b155-131">For more details on how to get this set up, see our [EDMX .NET Core sample](https://aka.ms/EdmxDotNetCoreSample).</span></span>

<span data-ttu-id="5b155-132">警告：請確認採用舊樣式 (亦即非 SDK 樣式) 的 .NET Framework 專案，_先_定義「實際」的 .edmx 檔案，再於 .sln 檔案中定義連結。</span><span class="sxs-lookup"><span data-stu-id="5b155-132">Warning: make sure the old style (i.e. non-SDK-style) .NET Framework project defining the "real" .edmx file comes _before_ the project defining the link inside the .sln file.</span></span> <span data-ttu-id="5b155-133">否則，當您在設計工具中開啟 .edmx 檔案時，將會出現下列錯誤訊息：「在目前針對專案指定的目標 Framework 中，無法使用 Entity Framework。</span><span class="sxs-lookup"><span data-stu-id="5b155-133">Otherwise, when you open the .edmx file in the designer, you see the error message "The Entity Framework is not available in the target framework currently specified for the project.</span></span> <span data-ttu-id="5b155-134">您可以變更專案的目標 Framework，或在 XmlEditor 中編輯模型。」。</span><span class="sxs-lookup"><span data-stu-id="5b155-134">You can change the target framework of the project or edit the model in the XmlEditor".</span></span>

## <a name="past-releases"></a><span data-ttu-id="5b155-135">過去版本</span><span class="sxs-lookup"><span data-stu-id="5b155-135">Past Releases</span></span>

<span data-ttu-id="5b155-136">[過去版本](xref:ef6/what-is-new/past-releases)頁面包含所有舊版 EF 與每一版所引進之主要功能的封存。</span><span class="sxs-lookup"><span data-stu-id="5b155-136">The [Past Releases](xref:ef6/what-is-new/past-releases) page contains an archive of all previous versions of EF and the major features that were introduced on each release.</span></span>
