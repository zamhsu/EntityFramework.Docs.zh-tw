---
title: 開始使用 Entity Framework 6 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 66ce9113-81d2-480f-8c16-d00ec405b2f7
uid: ef6/get-started
ms.openlocfilehash: 729dea2c474c35f638ccaf6673550f76e88e2667
ms.sourcegitcommit: c3b8386071d64953ee68788ef9d951144881a6ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "80136272"
---
# <a name="get-started-with-entity-framework-6"></a><span data-ttu-id="5032c-102">開始使用 Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="5032c-102">Get started with Entity Framework 6</span></span>

<span data-ttu-id="5032c-103">本指南包含一系列精選文件文章、逐步解說及影片的連結，以協助您快速開始使用。</span><span class="sxs-lookup"><span data-stu-id="5032c-103">This guide contains a collection of links to selected documentation articles, walkthroughs and videos that can help you get started quickly.</span></span>

## <a name="fundamentals"></a><span data-ttu-id="5032c-104">基礎</span><span class="sxs-lookup"><span data-stu-id="5032c-104">Fundamentals</span></span>

* [<span data-ttu-id="5032c-105">取得 Entity Framework</span><span class="sxs-lookup"><span data-stu-id="5032c-105">Get Entity Framework</span></span>](~/ef6/fundamentals/install.md)

  <span data-ttu-id="5032c-106">您在這裡會學到如何將 Entity Framework 新增到應用程式，而您如果想使用 EF Designer，務必在 Visual Studio 中加以安裝。</span><span class="sxs-lookup"><span data-stu-id="5032c-106">Here you will learn how to add Entity Framework to your applications and, if you want to use the EF Designer, make sure you get it installed in Visual Studio.</span></span>

* [<span data-ttu-id="5032c-107">建立模型：Code First、EF Designer 及 EF 工作流程</span><span class="sxs-lookup"><span data-stu-id="5032c-107">Creating a Model: Code First, the EF Designer, and the EF Workflows</span></span>](~/ef6/modeling/index.md)

  <span data-ttu-id="5032c-108">您偏好指定撰寫程式碼還是建構輪廓的 EF 模型？</span><span class="sxs-lookup"><span data-stu-id="5032c-108">Do you prefer to specify your EF model writing code or drawing boxes and lines?</span></span>
<span data-ttu-id="5032c-109">您要使用 EF 將物件對應到現有的資料庫，還是希望 EF 建立專為物件打造的資料庫？</span><span class="sxs-lookup"><span data-stu-id="5032c-109">Are you going to use EF to map your objects to an existing database or would you like EF to create a database tailored for your objects?</span></span>
<span data-ttu-id="5032c-110">在這裡，您將瞭解使用 EF6 的兩種不同方法： EF Designer 和 Code First。</span><span class="sxs-lookup"><span data-stu-id="5032c-110">Here you learn about two different approaches to use EF6: EF Designer and Code First.</span></span>
<span data-ttu-id="5032c-111">請務必追蹤討論，並觀看有關差異的影片。</span><span class="sxs-lookup"><span data-stu-id="5032c-111">Make sure you follow the discussion and watch the video about the difference.</span></span>

* [<span data-ttu-id="5032c-112">使用 DbContext</span><span class="sxs-lookup"><span data-stu-id="5032c-112">Working with DbContext</span></span>](~/ef6/fundamentals/working-with-dbcontext.md)

  <span data-ttu-id="5032c-113">DbContext 是第一個也最重要的 EF 類型，值得您學習如何使用。</span><span class="sxs-lookup"><span data-stu-id="5032c-113">DbContext is the first and most important EF type that you need to learn how to use.</span></span> <span data-ttu-id="5032c-114">其用途是資料庫查詢的啟動控制區，並會持續追蹤您對物件進行的變更，以便存回資料庫。</span><span class="sxs-lookup"><span data-stu-id="5032c-114">It serves as the launchpad for database queries and keeps track of changes you make to objects so that they can be persisted back to the database.</span></span>

* [<span data-ttu-id="5032c-115">提出問題</span><span class="sxs-lookup"><span data-stu-id="5032c-115">Ask a Question</span></span>](~/ef6/resources/get-help.md)

  <span data-ttu-id="5032c-116">了解如何取得專家的協助，以及對社群貢獻您自己的答案。</span><span class="sxs-lookup"><span data-stu-id="5032c-116">Find out how to get help from the experts and contribute your own answers to the community.</span></span>

* [<span data-ttu-id="5032c-117">參與</span><span class="sxs-lookup"><span data-stu-id="5032c-117">Contribute</span></span>](https://github.com/aspnet/EntityFramework6/)

  <span data-ttu-id="5032c-118">Entity Framework 6 使用開放的開發模型。</span><span class="sxs-lookup"><span data-stu-id="5032c-118">Entity Framework 6 uses an open development model.</span></span> <span data-ttu-id="5032c-119">歡迎瀏覽我們的 GitHub 存放庫，看看您可以如何讓 EF 更臻完美。</span><span class="sxs-lookup"><span data-stu-id="5032c-119">Find out how you can help make EF even better by visiting our GitHub repository.</span></span>

## <a name="code-first-resources"></a><span data-ttu-id="5032c-120">Code First 資源</span><span class="sxs-lookup"><span data-stu-id="5032c-120">Code First resources</span></span>

  - [<span data-ttu-id="5032c-121">現有資料庫工作流程的 Code First</span><span class="sxs-lookup"><span data-stu-id="5032c-121">Code First to an Existing Database Workflow</span></span>](~/ef6/modeling/code-first/workflows/existing-database.md)
  - [<span data-ttu-id="5032c-122">新資料庫工作流程的 Code First</span><span class="sxs-lookup"><span data-stu-id="5032c-122">Code First to a New Database Workflow</span></span>](~/ef6/modeling/code-first/workflows/new-database.md)
  - [<span data-ttu-id="5032c-123">使用 Code First 對應列舉</span><span class="sxs-lookup"><span data-stu-id="5032c-123">Mapping Enums Using Code First</span></span>](~/ef6/modeling/code-first/data-types/enums.md)
  - [<span data-ttu-id="5032c-124">使用 Code First 對應空間類型</span><span class="sxs-lookup"><span data-stu-id="5032c-124">Mapping Spatial Types Using Code First</span></span>](~/ef6/modeling/code-first/data-types/spatial.md)
  - [<span data-ttu-id="5032c-125">撰寫自訂 Code First 慣例</span><span class="sxs-lookup"><span data-stu-id="5032c-125">Writing Custom Code First Conventions</span></span>](~/ef6/modeling/code-first/conventions/custom.md)
  - [<span data-ttu-id="5032c-126">搭配 Visual Basic 使用 Code First Fluent 組態</span><span class="sxs-lookup"><span data-stu-id="5032c-126">Using Code First Fluent Configuration with Visual Basic</span></span>](~/ef6/modeling/code-first/fluent/vb.md)
  - [<span data-ttu-id="5032c-127">Code First 移轉</span><span class="sxs-lookup"><span data-stu-id="5032c-127">Code First Migrations</span></span>](~/ef6/modeling/code-first/migrations/index.md)
  - [<span data-ttu-id="5032c-128">小組環境中的 Code First 移轉</span><span class="sxs-lookup"><span data-stu-id="5032c-128">Code First Migrations in Team Environments</span></span>](~/ef6/modeling/code-first/migrations/teams.md)
  - <span data-ttu-id="5032c-129">[自動 Code First 移轉](~/ef6/modeling/code-first/migrations/automatic.md) (不再建議使用)</span><span class="sxs-lookup"><span data-stu-id="5032c-129">[Automatic Code First Migrations](~/ef6/modeling/code-first/migrations/automatic.md) (This is no longer recommended)</span></span>

## <a name="ef-designer-resources"></a><span data-ttu-id="5032c-130">EF Designer 資源</span><span class="sxs-lookup"><span data-stu-id="5032c-130">EF Designer resources</span></span>
  - [<span data-ttu-id="5032c-131">Database First 工作流程</span><span class="sxs-lookup"><span data-stu-id="5032c-131">Database First Workflow</span></span>](~/ef6/modeling/designer/workflows/database-first.md)
  - [<span data-ttu-id="5032c-132">Model First 工作流程</span><span class="sxs-lookup"><span data-stu-id="5032c-132">Model First Workflow</span></span>](~/ef6/modeling/designer/workflows/model-first.md)
  - [<span data-ttu-id="5032c-133">對應列舉</span><span class="sxs-lookup"><span data-stu-id="5032c-133">Mapping Enums</span></span>](~/ef6/modeling/designer/data-types/enums.md)
  - [<span data-ttu-id="5032c-134">對應空間類型</span><span class="sxs-lookup"><span data-stu-id="5032c-134">Mapping Spatial Types</span></span>](~/ef6/modeling/designer/data-types/spatial.md)
  - [<span data-ttu-id="5032c-135">單表繼承對應</span><span class="sxs-lookup"><span data-stu-id="5032c-135">Table-Per Hierarchy Inheritance Mapping</span></span>](~/ef6/modeling/designer/inheritance/tph.md)
  - [<span data-ttu-id="5032c-136">一類一表繼承對應</span><span class="sxs-lookup"><span data-stu-id="5032c-136">Table-Per Type Inheritance Mapping</span></span>](~/ef6/modeling/designer/inheritance/tpt.md)
  - [<span data-ttu-id="5032c-137">更新的預存程序對應</span><span class="sxs-lookup"><span data-stu-id="5032c-137">Stored Procedure Mapping for Updates</span></span>](~/ef6/modeling/designer/stored-procedures/cud.md)
  - [<span data-ttu-id="5032c-138">查詢的預存程序對應</span><span class="sxs-lookup"><span data-stu-id="5032c-138">Stored Procedure Mapping for Query</span></span>](~/ef6/modeling/designer/stored-procedures/query.md)
  - [<span data-ttu-id="5032c-139">實體分割</span><span class="sxs-lookup"><span data-stu-id="5032c-139">Entity Splitting</span></span>](~/ef6/modeling/designer/entity-splitting.md)
  - [<span data-ttu-id="5032c-140">資料表分割</span><span class="sxs-lookup"><span data-stu-id="5032c-140">Table Splitting</span></span>](~/ef6/modeling/designer/table-splitting.md)
  - <span data-ttu-id="5032c-141">[定義查詢](~/ef6/modeling/designer/advanced/defining-query.md) (進階)</span><span class="sxs-lookup"><span data-stu-id="5032c-141">[Defining Query](~/ef6/modeling/designer/advanced/defining-query.md) (Advanced)</span></span>
  - <span data-ttu-id="5032c-142">[資料表值函式](~/ef6/modeling/designer/advanced/tvfs.md) (進階)</span><span class="sxs-lookup"><span data-stu-id="5032c-142">[Table-Valued Functions](~/ef6/modeling/designer/advanced/tvfs.md) (Advanced)</span></span>

## <a name="other-resources"></a><span data-ttu-id="5032c-143">其他資源</span><span class="sxs-lookup"><span data-stu-id="5032c-143">Other resources</span></span>
  - [<span data-ttu-id="5032c-144">非同步查詢與儲存</span><span class="sxs-lookup"><span data-stu-id="5032c-144">Async Query and Save</span></span>](~/ef6/fundamentals/async.md)
  - [<span data-ttu-id="5032c-145">使用 WinForms 的資料繫結</span><span class="sxs-lookup"><span data-stu-id="5032c-145">Databinding with WinForms</span></span>](~/ef6/fundamentals/databinding/winforms.md)
  - [<span data-ttu-id="5032c-146">使用 WPF 的資料繫結</span><span class="sxs-lookup"><span data-stu-id="5032c-146">Databinding with WPF</span></span>](~/ef6/fundamentals/databinding/wpf.md)
  - <span data-ttu-id="5032c-147">[自我追蹤實體的中斷連線案例](~/ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough.md) (不再建議使用)</span><span class="sxs-lookup"><span data-stu-id="5032c-147">[Disconnected scenarios with Self-Tracking Entities](~/ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough.md) (This is no longer recommended)</span></span>
