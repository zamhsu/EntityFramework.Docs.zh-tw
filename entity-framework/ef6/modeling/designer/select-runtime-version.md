---
title: 選取 EF Designer 模型 Entity Framework 執行階段版本-EF6
description: 在 Entity Framework 6 中選取 EF Designer 模型 Entity Framework 執行階段版本
author: divega
ms.date: 10/23/2016
ms.openlocfilehash: 2025ca5e46c7d9a38ba596d57c023a2c1b224129
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073262"
---
# <a name="selecting-entity-framework-runtime-version-for-ef-designer-models"></a><span data-ttu-id="ecd23-103">選取 EF Designer 模型 Entity Framework 執行階段版本</span><span class="sxs-lookup"><span data-stu-id="ecd23-103">Selecting Entity Framework Runtime Version for EF Designer Models</span></span>
> [!NOTE]
> <span data-ttu-id="ecd23-104">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="ecd23-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="ecd23-105">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="ecd23-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="ecd23-106">從 EF6 開始，會將下列畫面新增至 EF 設計工具，讓您可以在建立模型時選取您想要設為目標的執行階段版本。</span><span class="sxs-lookup"><span data-stu-id="ecd23-106">Starting with EF6 the following screen was added to the EF Designer to allow you to select the version of the runtime you wish to target when creating a model.</span></span> <span data-ttu-id="ecd23-107">當專案中尚未安裝最新版本的 Entity Framework 時，畫面將會出現。</span><span class="sxs-lookup"><span data-stu-id="ecd23-107">The screen will appear when the latest version of Entity Framework is not already installed in the project.</span></span> <span data-ttu-id="ecd23-108">如果已安裝最新版本，則預設會使用它。</span><span class="sxs-lookup"><span data-stu-id="ecd23-108">If the latest version is already installed it will just be used by default.</span></span>

![選取執行階段版本](~/ef6/media/screen.png)

## <a name="targeting-ef6x"></a><span data-ttu-id="ecd23-110">目標為 EF6. x</span><span class="sxs-lookup"><span data-stu-id="ecd23-110">Targeting EF6.x</span></span>

<span data-ttu-id="ecd23-111">您可以選擇 [選擇您的版本] 畫面中的 [EF6]，將 EF6 執行時間新增至專案。</span><span class="sxs-lookup"><span data-stu-id="ecd23-111">You can choose EF6 from the 'Choose Your Version' screen to add the EF6 runtime to your project.</span></span> <span data-ttu-id="ecd23-112">新增 EF6 之後，您將會在目前的專案中停止看到此畫面。</span><span class="sxs-lookup"><span data-stu-id="ecd23-112">Once you've added EF6, you’ll stop seeing this screen in the current project.</span></span>

<span data-ttu-id="ecd23-113">如果您已 (安裝舊版的 EF，將會停用 EF6，因為您無法從相同的專案) ，將執行時間的多個版本設為目標。</span><span class="sxs-lookup"><span data-stu-id="ecd23-113">EF6 will be disabled if you already have an older version of EF installed (since you can't target multiple versions of the runtime from the same project).</span></span> <span data-ttu-id="ecd23-114">如果此處未啟用 EF6 選項，請依照下列步驟將您的專案升級至 EF6：</span><span class="sxs-lookup"><span data-stu-id="ecd23-114">If EF6 option is not enabled here, follow these steps to upgrade your project to EF6:</span></span>

1.  <span data-ttu-id="ecd23-115">在方案總管中的專案上按一下滑鼠右鍵，然後選取 [**管理 NuGet 套件 ...** ]</span><span class="sxs-lookup"><span data-stu-id="ecd23-115">Right-click on your project in Solution Explorer and select **Manage NuGet Packages...**</span></span>
2.  <span data-ttu-id="ecd23-116">選取 **更新**</span><span class="sxs-lookup"><span data-stu-id="ecd23-116">Select **Updates**</span></span>
3.  <span data-ttu-id="ecd23-117">選取 **EntityFramework** (確定它會將它更新為您想要的版本) </span><span class="sxs-lookup"><span data-stu-id="ecd23-117">Select **EntityFramework** (make sure it is going to update it to the version you want)</span></span>
4.  <span data-ttu-id="ecd23-118">按一下 [**更新**]</span><span class="sxs-lookup"><span data-stu-id="ecd23-118">Click **Update**</span></span>

 

## <a name="targeting-ef5x"></a><span data-ttu-id="ecd23-119">目標為 EF5. x</span><span class="sxs-lookup"><span data-stu-id="ecd23-119">Targeting EF5.x</span></span>

<span data-ttu-id="ecd23-120">您可以選擇 [選擇您的版本] 畫面中的 [EF5]，將 EF5 執行時間新增至專案。</span><span class="sxs-lookup"><span data-stu-id="ecd23-120">You can choose EF5 from the 'Choose Your Version' screen to add the EF5 runtime to your project.</span></span> <span data-ttu-id="ecd23-121">新增 EF5 之後，您仍會看到已停用 EF6 選項的畫面。</span><span class="sxs-lookup"><span data-stu-id="ecd23-121">Once you've added EF5, you’ll still see the screen with the EF6 option disabled.</span></span>

<span data-ttu-id="ecd23-122">如果您已安裝 EF4 的執行階段版本，則會看到該版本的 EF 列在畫面中，而不是 EF5。</span><span class="sxs-lookup"><span data-stu-id="ecd23-122">If you have an EF4.x version of the runtime already installed then you will see that version of EF listed in the screen rather than EF5.</span></span> <span data-ttu-id="ecd23-123">在此情況下，您可以使用下列步驟來升級至 EF5：</span><span class="sxs-lookup"><span data-stu-id="ecd23-123">In this situation you can upgrade to EF5 using the following steps:</span></span>

1.  <span data-ttu-id="ecd23-124">選取 **工具-連結 &gt; 庫封裝管理員- &gt; 封裝管理員主控台**</span><span class="sxs-lookup"><span data-stu-id="ecd23-124">Select **Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
2.  <span data-ttu-id="ecd23-125">執行 **Install-Package EntityFramework-第5.0.0 版**</span><span class="sxs-lookup"><span data-stu-id="ecd23-125">Run **Install-Package EntityFramework -version 5.0.0**</span></span>

 

## <a name="targeting-ef4x"></a><span data-ttu-id="ecd23-126">目標為 EF4. x</span><span class="sxs-lookup"><span data-stu-id="ecd23-126">Targeting EF4.x</span></span>

<span data-ttu-id="ecd23-127">您可以使用下列步驟，將 EF4 執行時間安裝至您的專案：</span><span class="sxs-lookup"><span data-stu-id="ecd23-127">You can install the EF4.x runtime to your project using the following steps:</span></span>

1.  <span data-ttu-id="ecd23-128">選取 **工具-連結 &gt; 庫封裝管理員- &gt; 封裝管理員主控台**</span><span class="sxs-lookup"><span data-stu-id="ecd23-128">Select **Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
2.  <span data-ttu-id="ecd23-129">執行 **Install-Package EntityFramework-version 4.3.0**</span><span class="sxs-lookup"><span data-stu-id="ecd23-129">Run **Install-Package EntityFramework -version 4.3.0**</span></span>
