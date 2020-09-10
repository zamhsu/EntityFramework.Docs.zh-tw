---
title: 開始使用 Entity Framework 6 - EF6
description: 開始使用 Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 66ce9113-81d2-480f-8c16-d00ec405b2f7
uid: ef6/get-started
ms.openlocfilehash: a03fa403bedc260def3f8110a028e972824cb756
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618197"
---
# <a name="get-started-with-entity-framework-6"></a><span data-ttu-id="89cd4-103">開始使用 Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="89cd4-103">Get started with Entity Framework 6</span></span>

<span data-ttu-id="89cd4-104">本指南包含一系列精選文件文章、逐步解說及影片的連結，以協助您快速開始使用。</span><span class="sxs-lookup"><span data-stu-id="89cd4-104">This guide contains a collection of links to selected documentation articles, walkthroughs and videos that can help you get started quickly.</span></span>

## <a name="fundamentals"></a><span data-ttu-id="89cd4-105">基礎</span><span class="sxs-lookup"><span data-stu-id="89cd4-105">Fundamentals</span></span>

* [<span data-ttu-id="89cd4-106">取得 Entity Framework</span><span class="sxs-lookup"><span data-stu-id="89cd4-106">Get Entity Framework</span></span>](xref:ef6/fundamentals/install)

  <span data-ttu-id="89cd4-107">您在這裡會學到如何將 Entity Framework 新增到應用程式，而您如果想使用 EF Designer，務必在 Visual Studio 中加以安裝。</span><span class="sxs-lookup"><span data-stu-id="89cd4-107">Here you will learn how to add Entity Framework to your applications and, if you want to use the EF Designer, make sure you get it installed in Visual Studio.</span></span>

* [<span data-ttu-id="89cd4-108">建立模型：Code First、EF Designer 及 EF 工作流程</span><span class="sxs-lookup"><span data-stu-id="89cd4-108">Creating a Model: Code First, the EF Designer, and the EF Workflows</span></span>](xref:ef6/modeling/index)

  <span data-ttu-id="89cd4-109">您偏好指定撰寫程式碼還是建構輪廓的 EF 模型？</span><span class="sxs-lookup"><span data-stu-id="89cd4-109">Do you prefer to specify your EF model writing code or drawing boxes and lines?</span></span>
<span data-ttu-id="89cd4-110">您要使用 EF 將物件對應到現有的資料庫，還是希望 EF 建立專為物件打造的資料庫？</span><span class="sxs-lookup"><span data-stu-id="89cd4-110">Are you going to use EF to map your objects to an existing database or would you like EF to create a database tailored for your objects?</span></span>
<span data-ttu-id="89cd4-111">在這裡，您將瞭解使用 EF6 的兩種不同方法： EF 設計工具及 Code First。</span><span class="sxs-lookup"><span data-stu-id="89cd4-111">Here you learn about two different approaches to use EF6: EF Designer and Code First.</span></span>
<span data-ttu-id="89cd4-112">請務必追蹤討論，並觀看有關差異的影片。</span><span class="sxs-lookup"><span data-stu-id="89cd4-112">Make sure you follow the discussion and watch the video about the difference.</span></span>

* [<span data-ttu-id="89cd4-113">使用 DbContext</span><span class="sxs-lookup"><span data-stu-id="89cd4-113">Working with DbContext</span></span>](xref:ef6/fundamentals/working-with-dbcontext)

  <span data-ttu-id="89cd4-114">DbContext 是第一個也最重要的 EF 類型，值得您學習如何使用。</span><span class="sxs-lookup"><span data-stu-id="89cd4-114">DbContext is the first and most important EF type that you need to learn how to use.</span></span> <span data-ttu-id="89cd4-115">其用途是資料庫查詢的啟動控制區，並會持續追蹤您對物件進行的變更，以便存回資料庫。</span><span class="sxs-lookup"><span data-stu-id="89cd4-115">It serves as the launchpad for database queries and keeps track of changes you make to objects so that they can be persisted back to the database.</span></span>

* [<span data-ttu-id="89cd4-116">詢問問題</span><span class="sxs-lookup"><span data-stu-id="89cd4-116">Ask a Question</span></span>](xref:ef6/resources/get-help)

  <span data-ttu-id="89cd4-117">了解如何取得專家的協助，以及對社群貢獻您自己的答案。</span><span class="sxs-lookup"><span data-stu-id="89cd4-117">Find out how to get help from the experts and contribute your own answers to the community.</span></span>

* [<span data-ttu-id="89cd4-118">貢獻</span><span class="sxs-lookup"><span data-stu-id="89cd4-118">Contribute</span></span>](https://github.com/aspnet/EntityFramework6/)

  <span data-ttu-id="89cd4-119">Entity Framework 6 使用開放的開發模型。</span><span class="sxs-lookup"><span data-stu-id="89cd4-119">Entity Framework 6 uses an open development model.</span></span> <span data-ttu-id="89cd4-120">歡迎瀏覽我們的 GitHub 存放庫，看看您可以如何讓 EF 更臻完美。</span><span class="sxs-lookup"><span data-stu-id="89cd4-120">Find out how you can help make EF even better by visiting our GitHub repository.</span></span>

## <a name="code-first-resources"></a><span data-ttu-id="89cd4-121">Code First 資源</span><span class="sxs-lookup"><span data-stu-id="89cd4-121">Code First resources</span></span>

  - [<span data-ttu-id="89cd4-122">現有資料庫工作流程的 Code First</span><span class="sxs-lookup"><span data-stu-id="89cd4-122">Code First to an Existing Database Workflow</span></span>](xref:ef6/modeling/code-first/workflows/existing-database)
  - [<span data-ttu-id="89cd4-123">新資料庫工作流程的 Code First</span><span class="sxs-lookup"><span data-stu-id="89cd4-123">Code First to a New Database Workflow</span></span>](xref:ef6/modeling/code-first/workflows/new-database)
  - [<span data-ttu-id="89cd4-124">使用 Code First 對應列舉</span><span class="sxs-lookup"><span data-stu-id="89cd4-124">Mapping Enums Using Code First</span></span>](xref:ef6/modeling/code-first/data-types/enums)
  - [<span data-ttu-id="89cd4-125">使用 Code First 對應空間類型</span><span class="sxs-lookup"><span data-stu-id="89cd4-125">Mapping Spatial Types Using Code First</span></span>](xref:ef6/modeling/code-first/data-types/spatial)
  - [<span data-ttu-id="89cd4-126">撰寫自訂 Code First 慣例</span><span class="sxs-lookup"><span data-stu-id="89cd4-126">Writing Custom Code First Conventions</span></span>](xref:ef6/modeling/code-first/conventions/custom)
  - [<span data-ttu-id="89cd4-127">搭配 Visual Basic 使用 Code First Fluent 組態</span><span class="sxs-lookup"><span data-stu-id="89cd4-127">Using Code First Fluent Configuration with Visual Basic</span></span>](xref:ef6/modeling/code-first/fluent/vb)
  - [<span data-ttu-id="89cd4-128">Code First 移轉</span><span class="sxs-lookup"><span data-stu-id="89cd4-128">Code First Migrations</span></span>](xref:ef6/modeling/code-first/migrations/index)
  - [<span data-ttu-id="89cd4-129">小組環境中的 Code First 移轉</span><span class="sxs-lookup"><span data-stu-id="89cd4-129">Code First Migrations in Team Environments</span></span>](xref:ef6/modeling/code-first/migrations/teams)
  - <span data-ttu-id="89cd4-130">[自動 Code First 移轉](xref:ef6/modeling/code-first/migrations/automatic) (不再建議使用)</span><span class="sxs-lookup"><span data-stu-id="89cd4-130">[Automatic Code First Migrations](xref:ef6/modeling/code-first/migrations/automatic) (This is no longer recommended)</span></span>

## <a name="ef-designer-resources"></a><span data-ttu-id="89cd4-131">EF Designer 資源</span><span class="sxs-lookup"><span data-stu-id="89cd4-131">EF Designer resources</span></span>
  - [<span data-ttu-id="89cd4-132">Database First 工作流程</span><span class="sxs-lookup"><span data-stu-id="89cd4-132">Database First Workflow</span></span>](xref:ef6/modeling/designer/workflows/database-first)
  - [<span data-ttu-id="89cd4-133">Model First 工作流程</span><span class="sxs-lookup"><span data-stu-id="89cd4-133">Model First Workflow</span></span>](xref:ef6/modeling/designer/workflows/model-first)
  - [<span data-ttu-id="89cd4-134">對應列舉</span><span class="sxs-lookup"><span data-stu-id="89cd4-134">Mapping Enums</span></span>](xref:ef6/modeling/designer/data-types/enums)
  - [<span data-ttu-id="89cd4-135">對應空間類型</span><span class="sxs-lookup"><span data-stu-id="89cd4-135">Mapping Spatial Types</span></span>](xref:ef6/modeling/designer/data-types/spatial)
  - [<span data-ttu-id="89cd4-136">單表繼承對應</span><span class="sxs-lookup"><span data-stu-id="89cd4-136">Table-Per Hierarchy Inheritance Mapping</span></span>](xref:ef6/modeling/designer/inheritance/tph)
  - [<span data-ttu-id="89cd4-137">一類一表繼承對應</span><span class="sxs-lookup"><span data-stu-id="89cd4-137">Table-Per Type Inheritance Mapping</span></span>](xref:ef6/modeling/designer/inheritance/tpt)
  - [<span data-ttu-id="89cd4-138">更新的預存程序對應</span><span class="sxs-lookup"><span data-stu-id="89cd4-138">Stored Procedure Mapping for Updates</span></span>](xref:ef6/modeling/designer/stored-procedures/cud)
  - [<span data-ttu-id="89cd4-139">查詢的預存程序對應</span><span class="sxs-lookup"><span data-stu-id="89cd4-139">Stored Procedure Mapping for Query</span></span>](xref:ef6/modeling/designer/stored-procedures/query)
  - [<span data-ttu-id="89cd4-140">實體分割</span><span class="sxs-lookup"><span data-stu-id="89cd4-140">Entity Splitting</span></span>](xref:ef6/modeling/designer/entity-splitting)
  - [<span data-ttu-id="89cd4-141">資料表分割</span><span class="sxs-lookup"><span data-stu-id="89cd4-141">Table Splitting</span></span>](xref:ef6/modeling/designer/table-splitting)
  - <span data-ttu-id="89cd4-142">[定義查詢](xref:ef6/modeling/designer/advanced/defining-query) (進階)</span><span class="sxs-lookup"><span data-stu-id="89cd4-142">[Defining Query](xref:ef6/modeling/designer/advanced/defining-query) (Advanced)</span></span>
  - <span data-ttu-id="89cd4-143">[資料表值函式](xref:ef6/modeling/designer/advanced/tvfs) (進階)</span><span class="sxs-lookup"><span data-stu-id="89cd4-143">[Table-Valued Functions](xref:ef6/modeling/designer/advanced/tvfs) (Advanced)</span></span>

## <a name="other-resources"></a><span data-ttu-id="89cd4-144">其他資源</span><span class="sxs-lookup"><span data-stu-id="89cd4-144">Other resources</span></span>
  - [<span data-ttu-id="89cd4-145">非同步查詢與儲存</span><span class="sxs-lookup"><span data-stu-id="89cd4-145">Async Query and Save</span></span>](xref:ef6/fundamentals/async)
  - [<span data-ttu-id="89cd4-146">使用 WinForms 的資料繫結</span><span class="sxs-lookup"><span data-stu-id="89cd4-146">Databinding with WinForms</span></span>](xref:ef6/fundamentals/databinding/winforms)
  - [<span data-ttu-id="89cd4-147">使用 WPF 的資料繫結</span><span class="sxs-lookup"><span data-stu-id="89cd4-147">Databinding with WPF</span></span>](xref:ef6/fundamentals/databinding/wpf)
  - <span data-ttu-id="89cd4-148">[自我追蹤實體的中斷連線案例](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough) (不再建議使用)</span><span class="sxs-lookup"><span data-stu-id="89cd4-148">[Disconnected scenarios with Self-Tracking Entities](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough) (This is no longer recommended)</span></span>
