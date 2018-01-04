---
title: "移轉具有多個專案的 EF 核心"
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
ms.technology: entity-framework-core
ms.openlocfilehash: 3684e86cce0005056380d89604d038c734054d14
ms.sourcegitcommit: ced2637bf8cc5964c6daa6c7fcfce501bf9ef6e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2017
---
<a name="using-a-separate-project"></a><span data-ttu-id="1674c-102">使用個別的專案</span><span class="sxs-lookup"><span data-stu-id="1674c-102">Using a Separate Project</span></span>
========================
<span data-ttu-id="1674c-103">要儲存於一個包含不同的組件中的移轉您`DbContext`。</span><span class="sxs-lookup"><span data-stu-id="1674c-103">You may want to store your migrations in a different assembly than the one containing your `DbContext`.</span></span> <span data-ttu-id="1674c-104">您也可以使用這項策略來維護多個集合的移轉，例如，一個用於開發，另一個版本的升級。</span><span class="sxs-lookup"><span data-stu-id="1674c-104">You can also use this strategy to maintain multiple sets of migrations, for example, one for development and another for release-to-release upgrades.</span></span>

<span data-ttu-id="1674c-105">若要執行相關作業…</span><span class="sxs-lookup"><span data-stu-id="1674c-105">To do this...</span></span>

1. <span data-ttu-id="1674c-106">建立新的類別庫。</span><span class="sxs-lookup"><span data-stu-id="1674c-106">Create a new class library.</span></span>

2. <span data-ttu-id="1674c-107">加入 DbContext 組件的參考。</span><span class="sxs-lookup"><span data-stu-id="1674c-107">Add a reference to your DbContext assembly.</span></span>

3. <span data-ttu-id="1674c-108">將移轉和模型的快照集檔案移至 類別庫中。</span><span class="sxs-lookup"><span data-stu-id="1674c-108">Move the migrations and model snapshot files to the class library.</span></span>
   * <span data-ttu-id="1674c-109">如果您尚未加入任何，加入至 DbContext 專案，然後將它移。</span><span class="sxs-lookup"><span data-stu-id="1674c-109">If you haven't added any, add one to the DbContext project then move it.</span></span>

4. <span data-ttu-id="1674c-110">設定移轉組件：</span><span class="sxs-lookup"><span data-stu-id="1674c-110">Configure the migrations assembly:</span></span>

   ``` csharp
   options.UseSqlServer(
       connectionString,
       x => x.MigrationsAssembly("MyApp.Migrations"));
   ```

5. <span data-ttu-id="1674c-111">新增您移轉的組件中的啟動組件的參考。</span><span class="sxs-lookup"><span data-stu-id="1674c-111">Add a reference to your migrations assembly from the startup assembly.</span></span>
   * <span data-ttu-id="1674c-112">如果這導致循環相依性時，更新的類別庫的輸出路徑：</span><span class="sxs-lookup"><span data-stu-id="1674c-112">If this causes a circular dependency, update the output path of the class library:</span></span>

     ``` xml
     <PropertyGroup>
       <OutputPath>..\MyStarupProject\bin\$(Configuration)\</OutputPath>
     </PropertyGroup>
     ```

<span data-ttu-id="1674c-113">如果您所執行的所有項目正確，您應該能夠專案中加入新的移轉。</span><span class="sxs-lookup"><span data-stu-id="1674c-113">If you did everything correctly, you should be able to add new migrations to the project.</span></span>

``` powershell
Add-Migration NewMigration -Project MyApp.Migrations
```
``` Console
dotnet ef migrations add NewMigration --project MyApp.Migrations
```
