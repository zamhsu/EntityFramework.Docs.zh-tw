---
title: 使用個別的遷移專案-EF Core
description: 使用不同的遷移專案來管理具有 Entity Framework Core 的資料庫架構
author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/projects
ms.openlocfilehash: 6f28027c320f0d1c13d13bef7d8227b2bb68df91
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062486"
---
# <a name="using-a-separate-migrations-project"></a><span data-ttu-id="2d4c5-103">使用個別的遷移專案</span><span class="sxs-lookup"><span data-stu-id="2d4c5-103">Using a Separate Migrations Project</span></span>

<span data-ttu-id="2d4c5-104">您可能會想要將您的遷移儲存在與包含的元件不同的元件中 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="2d4c5-104">You may want to store your migrations in a different assembly than the one containing your `DbContext`.</span></span> <span data-ttu-id="2d4c5-105">您也可以使用這項策略來維護多個遷移集，例如一個用於開發，另一個用於發行到發行的升級。</span><span class="sxs-lookup"><span data-stu-id="2d4c5-105">You can also use this strategy to maintain multiple sets of migrations, for example, one for development and another for release-to-release upgrades.</span></span>

<span data-ttu-id="2d4c5-106">若要執行相關作業…</span><span class="sxs-lookup"><span data-stu-id="2d4c5-106">To do this...</span></span>

1. <span data-ttu-id="2d4c5-107">建立新的類別庫。</span><span class="sxs-lookup"><span data-stu-id="2d4c5-107">Create a new class library.</span></span>

2. <span data-ttu-id="2d4c5-108">新增 DbCoNtext 元件的參考。</span><span class="sxs-lookup"><span data-stu-id="2d4c5-108">Add a reference to your DbContext assembly.</span></span>

3. <span data-ttu-id="2d4c5-109">將遷移和模型快照集檔案移至類別庫。</span><span class="sxs-lookup"><span data-stu-id="2d4c5-109">Move the migrations and model snapshot files to the class library.</span></span>
   > [!TIP]
   > <span data-ttu-id="2d4c5-110">如果您沒有任何現有的遷移，請在包含 DbCoNtext 的專案中產生一個，然後將它移動。</span><span class="sxs-lookup"><span data-stu-id="2d4c5-110">If you have no existing migrations, generate one in the project containing the DbContext then move it.</span></span>
   > <span data-ttu-id="2d4c5-111">這點很重要，因為如果遷移元件不包含現有的遷移，Add-Migration 命令將找不到 DbCoNtext。</span><span class="sxs-lookup"><span data-stu-id="2d4c5-111">This is important because if the migrations assembly does not contain an existing migration, the Add-Migration command will be unable to find the DbContext.</span></span>

4. <span data-ttu-id="2d4c5-112">設定遷移元件：</span><span class="sxs-lookup"><span data-stu-id="2d4c5-112">Configure the migrations assembly:</span></span>

   ```csharp
   options.UseSqlServer(
       connectionString,
       x => x.MigrationsAssembly("MyApp.Migrations"));
   ```

5. <span data-ttu-id="2d4c5-113">從啟動元件加入您的遷移元件的參考。</span><span class="sxs-lookup"><span data-stu-id="2d4c5-113">Add a reference to your migrations assembly from the startup assembly.</span></span>
   * <span data-ttu-id="2d4c5-114">如果這會造成迴圈相依性，請更新類別庫的輸出路徑：</span><span class="sxs-lookup"><span data-stu-id="2d4c5-114">If this causes a circular dependency, update the output path of the class library:</span></span>

     ```xml
     <PropertyGroup>
       <OutputPath>..\MyStartupProject\bin\$(Configuration)\</OutputPath>
     </PropertyGroup>
     ```

<span data-ttu-id="2d4c5-115">如果一切都正確，您應該能夠將新的遷移新增至專案。</span><span class="sxs-lookup"><span data-stu-id="2d4c5-115">If you did everything correctly, you should be able to add new migrations to the project.</span></span>

## <a name="net-core-cli"></a>[<span data-ttu-id="2d4c5-116">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2d4c5-116">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add NewMigration --project MyApp.Migrations
```

## <a name="visual-studio"></a>[<span data-ttu-id="2d4c5-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2d4c5-117">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration NewMigration -Project MyApp.Migrations
```

***
