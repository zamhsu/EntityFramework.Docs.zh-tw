---
title: 移轉具有多個專案的 EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
ms.openlocfilehash: 76e88dd486b1c53dc69a24e35710511bf9cb673b
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997983"
---
<a name="using-a-separate-project"></a><span data-ttu-id="a009f-102">使用個別的專案</span><span class="sxs-lookup"><span data-stu-id="a009f-102">Using a Separate Project</span></span>
========================
<span data-ttu-id="a009f-103">您可以將移轉儲存在不同的組件，比一個包含您`DbContext`。</span><span class="sxs-lookup"><span data-stu-id="a009f-103">You may want to store your migrations in a different assembly than the one containing your `DbContext`.</span></span> <span data-ttu-id="a009f-104">您也可以使用此策略，以維護多組移轉，例如，一個用於開發，另一個版本到版本升級。</span><span class="sxs-lookup"><span data-stu-id="a009f-104">You can also use this strategy to maintain multiple sets of migrations, for example, one for development and another for release-to-release upgrades.</span></span>

<span data-ttu-id="a009f-105">若要執行相關作業…</span><span class="sxs-lookup"><span data-stu-id="a009f-105">To do this...</span></span>

1. <span data-ttu-id="a009f-106">建立新的類別庫。</span><span class="sxs-lookup"><span data-stu-id="a009f-106">Create a new class library.</span></span>

2. <span data-ttu-id="a009f-107">加入您的 DbContext 組件的參考。</span><span class="sxs-lookup"><span data-stu-id="a009f-107">Add a reference to your DbContext assembly.</span></span>

3. <span data-ttu-id="a009f-108">將移轉與模型快照集檔案移至 類別庫中。</span><span class="sxs-lookup"><span data-stu-id="a009f-108">Move the migrations and model snapshot files to the class library.</span></span>
   > [!TIP]
   > <span data-ttu-id="a009f-109">如果您不有任何現有的移轉時，產生一個專案中包含 DbContext，則會移動它。</span><span class="sxs-lookup"><span data-stu-id="a009f-109">If you have no existing migrations, generate one in the project containing the DbContext then move it.</span></span> <span data-ttu-id="a009f-110">這很重要，因為如果移轉組件不包含現有的移轉，會找不到 DbContext 新增移轉命令。</span><span class="sxs-lookup"><span data-stu-id="a009f-110">This is important because if the migrations assembly does not contain an existing migration, the Add-Migration command will be unable to find the DbContext.</span></span>

4. <span data-ttu-id="a009f-111">設定移轉組件：</span><span class="sxs-lookup"><span data-stu-id="a009f-111">Configure the migrations assembly:</span></span>

   ``` csharp
   options.UseSqlServer(
       connectionString,
       x => x.MigrationsAssembly("MyApp.Migrations"));
   ```

5. <span data-ttu-id="a009f-112">新增您移轉的組件中的啟動組件的參考。</span><span class="sxs-lookup"><span data-stu-id="a009f-112">Add a reference to your migrations assembly from the startup assembly.</span></span>
   * <span data-ttu-id="a009f-113">如果這導致循環相依性時，更新類別庫的輸出的路徑：</span><span class="sxs-lookup"><span data-stu-id="a009f-113">If this causes a circular dependency, update the output path of the class library:</span></span>

     ``` xml
     <PropertyGroup>
       <OutputPath>..\MyStartupProject\bin\$(Configuration)\</OutputPath>
     </PropertyGroup>
     ```

<span data-ttu-id="a009f-114">如果您正確做到，您應該能夠將新的移轉新增至專案。</span><span class="sxs-lookup"><span data-stu-id="a009f-114">If you did everything correctly, you should be able to add new migrations to the project.</span></span>

``` powershell
Add-Migration NewMigration -Project MyApp.Migrations
```
``` Console
dotnet ef migrations add NewMigration --project MyApp.Migrations
```
