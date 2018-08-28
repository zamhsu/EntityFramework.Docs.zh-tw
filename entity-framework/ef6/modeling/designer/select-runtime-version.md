---
title: EF 設計工具模型 EF6 的選取 Entity Framework 執行階段版本
author: divega
ms.date: 2016-10-23
ms.assetid: 7ace90a6-46f8-4f55-a88c-7cad9620085c
ms.openlocfilehash: 8864bb8166a7c16455d5c3bebe91e2ce8d142685
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42998238"
---
# <a name="selecting-entity-framework-runtime-version-for-ef-designer-models"></a><span data-ttu-id="dfd5c-102">選取 Entity Framework 執行階段版本的 EF 設計工具模型</span><span class="sxs-lookup"><span data-stu-id="dfd5c-102">Selecting Entity Framework Runtime Version for EF Designer Models</span></span>
> [!NOTE]
> <span data-ttu-id="dfd5c-103">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="dfd5c-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="dfd5c-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="dfd5c-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="dfd5c-105">從 EF6 開始 EF 設計工具，可讓您選取您想要建立模型時，目標執行階段版本已加入下列的畫面。</span><span class="sxs-lookup"><span data-stu-id="dfd5c-105">Starting with EF6 the following screen was added to the EF Designer to allow you to select the version of the runtime you wish to target when creating a model.</span></span> <span data-ttu-id="dfd5c-106">當 Entity Framework 的最新版本尚未安裝在專案中，會顯示畫面。</span><span class="sxs-lookup"><span data-stu-id="dfd5c-106">The screen will appear when the latest version of Entity Framework is not already installed in the project.</span></span> <span data-ttu-id="dfd5c-107">如果已安裝最新版預設將只會使用。</span><span class="sxs-lookup"><span data-stu-id="dfd5c-107">If the latest version is already installed it will just be used by default.</span></span>

![螢幕](~/ef6/media/screen.png)


## <a name="targeting-ef6x"></a><span data-ttu-id="dfd5c-109">目標 EF6.x</span><span class="sxs-lookup"><span data-stu-id="dfd5c-109">Targeting EF6.x</span></span>

<span data-ttu-id="dfd5c-110">您可以從 [選擇您版本 '] 畫面將 EF6 執行階段新增至您的專案選擇 EF6。</span><span class="sxs-lookup"><span data-stu-id="dfd5c-110">You can choose EF6 from the 'Choose Your Version' screen to add the EF6 runtime to your project.</span></span> <span data-ttu-id="dfd5c-111">一旦您已新增 EF6，您將停止看到這個畫面中目前的專案。</span><span class="sxs-lookup"><span data-stu-id="dfd5c-111">Once you've added EF6, you’ll stop seeing this screen in the current project.</span></span>

<span data-ttu-id="dfd5c-112">如果您已經有較舊版本的 EF 安裝 （因為您無法將目標從相同的專案執行階段的多個版本），將會停用 EF6。</span><span class="sxs-lookup"><span data-stu-id="dfd5c-112">EF6 will be disabled if you already have an older version of EF installed (since you can't target multiple versions of the runtime from the same project).</span></span> <span data-ttu-id="dfd5c-113">如果這裡未啟用 EF6 選項，請遵循這些步驟來升級您的專案 ef6:</span><span class="sxs-lookup"><span data-stu-id="dfd5c-113">If EF6 option is not enabled here, follow these steps to upgrade your project to EF6:</span></span>

1.  <span data-ttu-id="dfd5c-114">以滑鼠右鍵按一下方案總管 中的專案，然後選取**管理 NuGet 套件...**</span><span class="sxs-lookup"><span data-stu-id="dfd5c-114">Right-click on your project in Solution Explorer and select **Manage NuGet Packages...**</span></span>
2.  <span data-ttu-id="dfd5c-115">選取**更新**</span><span class="sxs-lookup"><span data-stu-id="dfd5c-115">Select **Updates**</span></span>
3.  <span data-ttu-id="dfd5c-116">選取  **EntityFramework** （請確定它會將其更新為您想要的版本）</span><span class="sxs-lookup"><span data-stu-id="dfd5c-116">Select **EntityFramework** (make sure it is going to update it to the version you want)</span></span>
4.  <span data-ttu-id="dfd5c-117">按一下 **更新**</span><span class="sxs-lookup"><span data-stu-id="dfd5c-117">Click **Update**</span></span>

 

## <a name="targeting-ef5x"></a><span data-ttu-id="dfd5c-118">目標 EF5.x</span><span class="sxs-lookup"><span data-stu-id="dfd5c-118">Targeting EF5.x</span></span>

<span data-ttu-id="dfd5c-119">您可以選擇 EF5 從 EF5 執行階段加入您的專案的 [選擇您 Version'] 畫面。</span><span class="sxs-lookup"><span data-stu-id="dfd5c-119">You can choose EF5 from the 'Choose Your Version' screen to add the EF5 runtime to your project.</span></span> <span data-ttu-id="dfd5c-120">一旦您已新增 EF5，您還是會看到畫面使用 EF6 選項停用。</span><span class="sxs-lookup"><span data-stu-id="dfd5c-120">Once you've added EF5, you’ll still see the screen with the EF6 option disabled.</span></span>

<span data-ttu-id="dfd5c-121">如果您有已安裝的執行階段 EF4.x 版本，您會看到畫面，而不是 EF5 中所列的 EF 該的版本。</span><span class="sxs-lookup"><span data-stu-id="dfd5c-121">If you have an EF4.x version of the runtime already installed then you will see that version of EF listed in the screen rather than EF5.</span></span> <span data-ttu-id="dfd5c-122">在此情況下，您可以升級至 EF5 使用下列步驟：</span><span class="sxs-lookup"><span data-stu-id="dfd5c-122">In this situation you can upgrade to EF5 using the following steps:</span></span>

1.  <span data-ttu-id="dfd5c-123">選取 [**工具]-&gt;程式庫套件管理員-&gt;套件管理員主控台**</span><span class="sxs-lookup"><span data-stu-id="dfd5c-123">Select **Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
2.  <span data-ttu-id="dfd5c-124">執行**Install-package EntityFramework-5.0.0 版**</span><span class="sxs-lookup"><span data-stu-id="dfd5c-124">Run **Install-Package EntityFramework -version 5.0.0**</span></span>

 

## <a name="targeting-ef4x"></a><span data-ttu-id="dfd5c-125">目標 EF4.x</span><span class="sxs-lookup"><span data-stu-id="dfd5c-125">Targeting EF4.x</span></span>

<span data-ttu-id="dfd5c-126">您可以安裝 EF4.x 執行階段，以您的專案使用下列步驟：</span><span class="sxs-lookup"><span data-stu-id="dfd5c-126">You can install the EF4.x runtime to your project using the following steps:</span></span>

1.  <span data-ttu-id="dfd5c-127">選取 [**工具]-&gt;程式庫套件管理員-&gt;套件管理員主控台**</span><span class="sxs-lookup"><span data-stu-id="dfd5c-127">Select **Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
2.  <span data-ttu-id="dfd5c-128">執行**Install-package EntityFramework-4.3.0 版**</span><span class="sxs-lookup"><span data-stu-id="dfd5c-128">Run **Install-Package EntityFramework -version 4.3.0**</span></span>
