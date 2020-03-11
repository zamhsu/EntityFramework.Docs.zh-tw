---
title: 選取 EF 設計工具模型 Entity Framework 執行階段版本-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 7ace90a6-46f8-4f55-a88c-7cad9620085c
ms.openlocfilehash: 40ad05c1f015e6a51150d04eee8d9aa581d72c33
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78418144"
---
# <a name="selecting-entity-framework-runtime-version-for-ef-designer-models"></a><span data-ttu-id="4a21d-102">選取 EF 設計工具模型 Entity Framework 執行階段版本</span><span class="sxs-lookup"><span data-stu-id="4a21d-102">Selecting Entity Framework Runtime Version for EF Designer Models</span></span>
> [!NOTE]
> <span data-ttu-id="4a21d-103">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="4a21d-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="4a21d-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="4a21d-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="4a21d-105">從 EF6 開始，會在 EF 設計工具中加入下列畫面，讓您在建立模型時，選取您想要設為目標的執行階段版本。</span><span class="sxs-lookup"><span data-stu-id="4a21d-105">Starting with EF6 the following screen was added to the EF Designer to allow you to select the version of the runtime you wish to target when creating a model.</span></span> <span data-ttu-id="4a21d-106">當專案中尚未安裝最新版本的 Entity Framework 時，畫面將會出現。</span><span class="sxs-lookup"><span data-stu-id="4a21d-106">The screen will appear when the latest version of Entity Framework is not already installed in the project.</span></span> <span data-ttu-id="4a21d-107">如果已安裝最新版本，則預設會使用它。</span><span class="sxs-lookup"><span data-stu-id="4a21d-107">If the latest version is already installed it will just be used by default.</span></span>

![畫面](~/ef6/media/screen.png)


## <a name="targeting-ef6x"></a><span data-ttu-id="4a21d-109">以 EF6 為目標</span><span class="sxs-lookup"><span data-stu-id="4a21d-109">Targeting EF6.x</span></span>

<span data-ttu-id="4a21d-110">您可以從 [選擇您的版本] 畫面中選擇 [EF6]，將 EF6 執行時間新增至您的專案。</span><span class="sxs-lookup"><span data-stu-id="4a21d-110">You can choose EF6 from the 'Choose Your Version' screen to add the EF6 runtime to your project.</span></span> <span data-ttu-id="4a21d-111">加入 EF6 之後，您將會在目前的專案中停止看到此畫面。</span><span class="sxs-lookup"><span data-stu-id="4a21d-111">Once you've added EF6, you’ll stop seeing this screen in the current project.</span></span>

<span data-ttu-id="4a21d-112">如果您已安裝較舊版本的 EF，EF6 將會停用（因為您無法從相同的專案以多個版本的執行時間為目標）。</span><span class="sxs-lookup"><span data-stu-id="4a21d-112">EF6 will be disabled if you already have an older version of EF installed (since you can't target multiple versions of the runtime from the same project).</span></span> <span data-ttu-id="4a21d-113">如果此處未啟用 EF6 選項，請遵循下列步驟將您的專案升級至 EF6：</span><span class="sxs-lookup"><span data-stu-id="4a21d-113">If EF6 option is not enabled here, follow these steps to upgrade your project to EF6:</span></span>

1.  <span data-ttu-id="4a21d-114">以滑鼠右鍵按一下方案總管中的專案，然後選取 [**管理 NuGet 套件 ...** ]</span><span class="sxs-lookup"><span data-stu-id="4a21d-114">Right-click on your project in Solution Explorer and select **Manage NuGet Packages...**</span></span>
2.  <span data-ttu-id="4a21d-115">選取**更新**</span><span class="sxs-lookup"><span data-stu-id="4a21d-115">Select **Updates**</span></span>
3.  <span data-ttu-id="4a21d-116">選取 [ **EntityFramework** ] （請確定它會將它更新為您想要的版本）</span><span class="sxs-lookup"><span data-stu-id="4a21d-116">Select **EntityFramework** (make sure it is going to update it to the version you want)</span></span>
4.  <span data-ttu-id="4a21d-117">按一下 [更新]</span><span class="sxs-lookup"><span data-stu-id="4a21d-117">Click **Update**</span></span>

 

## <a name="targeting-ef5x"></a><span data-ttu-id="4a21d-118">以 EF5 為目標</span><span class="sxs-lookup"><span data-stu-id="4a21d-118">Targeting EF5.x</span></span>

<span data-ttu-id="4a21d-119">您可以從 [選擇您的版本] 畫面中選擇 [EF5]，將 EF5 執行時間新增至您的專案。</span><span class="sxs-lookup"><span data-stu-id="4a21d-119">You can choose EF5 from the 'Choose Your Version' screen to add the EF5 runtime to your project.</span></span> <span data-ttu-id="4a21d-120">新增 EF5 之後，您仍會看到 [EF6] 選項已停用的畫面。</span><span class="sxs-lookup"><span data-stu-id="4a21d-120">Once you've added EF5, you’ll still see the screen with the EF6 option disabled.</span></span>

<span data-ttu-id="4a21d-121">如果您已安裝 EF4. x 版的執行時間，則會看到該版本的 EF 列在畫面中，而不是 EF5。</span><span class="sxs-lookup"><span data-stu-id="4a21d-121">If you have an EF4.x version of the runtime already installed then you will see that version of EF listed in the screen rather than EF5.</span></span> <span data-ttu-id="4a21d-122">在此情況下，您可以使用下列步驟來升級至 EF5：</span><span class="sxs-lookup"><span data-stu-id="4a21d-122">In this situation you can upgrade to EF5 using the following steps:</span></span>

1.  <span data-ttu-id="4a21d-123">選取**工具-&gt; 程式庫套件管理員-&gt; 套件管理員主控台**</span><span class="sxs-lookup"><span data-stu-id="4a21d-123">Select **Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
2.  <span data-ttu-id="4a21d-124">執行**安裝-Package EntityFramework-5.0.0 版**</span><span class="sxs-lookup"><span data-stu-id="4a21d-124">Run **Install-Package EntityFramework -version 5.0.0**</span></span>

 

## <a name="targeting-ef4x"></a><span data-ttu-id="4a21d-125">以 EF4 為目標</span><span class="sxs-lookup"><span data-stu-id="4a21d-125">Targeting EF4.x</span></span>

<span data-ttu-id="4a21d-126">您可以使用下列步驟，將 EF4 執行時間安裝到您的專案：</span><span class="sxs-lookup"><span data-stu-id="4a21d-126">You can install the EF4.x runtime to your project using the following steps:</span></span>

1.  <span data-ttu-id="4a21d-127">選取**工具-&gt; 程式庫套件管理員-&gt; 套件管理員主控台**</span><span class="sxs-lookup"><span data-stu-id="4a21d-127">Select **Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
2.  <span data-ttu-id="4a21d-128">執行**安裝-Package EntityFramework-version 4.3.0**</span><span class="sxs-lookup"><span data-stu-id="4a21d-128">Run **Install-Package EntityFramework -version 4.3.0**</span></span>
