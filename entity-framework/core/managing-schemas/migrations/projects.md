---
title: 使用個別的遷移專案-EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/projects
ms.openlocfilehash: 0082b0af2905fe9e5c3c6509516f622c9d4f8370
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72812040"
---
# <a name="using-a-separate-migrations-project"></a><span data-ttu-id="48045-102">使用個別的遷移專案</span><span class="sxs-lookup"><span data-stu-id="48045-102">Using a Separate Migrations Project</span></span>

<span data-ttu-id="48045-103">您可能想要將您的遷移儲存在與包含您的 `DbContext`不同的元件中。</span><span class="sxs-lookup"><span data-stu-id="48045-103">You may want to store your migrations in a different assembly than the one containing your `DbContext`.</span></span> <span data-ttu-id="48045-104">您也可以使用此策略來維護多組遷移，例如，一個用於開發，另一個用於發行至發行升級。</span><span class="sxs-lookup"><span data-stu-id="48045-104">You can also use this strategy to maintain multiple sets of migrations, for example, one for development and another for release-to-release upgrades.</span></span>

<span data-ttu-id="48045-105">若要執行相關作業…</span><span class="sxs-lookup"><span data-stu-id="48045-105">To do this...</span></span>

1. <span data-ttu-id="48045-106">建立新的類別庫。</span><span class="sxs-lookup"><span data-stu-id="48045-106">Create a new class library.</span></span>

2. <span data-ttu-id="48045-107">新增 DbCoNtext 元件的參考。</span><span class="sxs-lookup"><span data-stu-id="48045-107">Add a reference to your DbContext assembly.</span></span>

3. <span data-ttu-id="48045-108">將「遷移」和「模型快照集」檔案移至類別庫。</span><span class="sxs-lookup"><span data-stu-id="48045-108">Move the migrations and model snapshot files to the class library.</span></span>
   > [!TIP]
   > <span data-ttu-id="48045-109">如果您沒有任何現有的遷移，請在包含 DbCoNtext 的專案中產生一個，然後將它移動。</span><span class="sxs-lookup"><span data-stu-id="48045-109">If you have no existing migrations, generate one in the project containing the DbContext then move it.</span></span>
   > <span data-ttu-id="48045-110">這很重要，因為如果遷移元件不包含現有的遷移，則新增遷移命令將找不到 DbCoNtext。</span><span class="sxs-lookup"><span data-stu-id="48045-110">This is important because if the migrations assembly does not contain an existing migration, the Add-Migration command will be unable to find the DbContext.</span></span>

4. <span data-ttu-id="48045-111">設定遷移元件：</span><span class="sxs-lookup"><span data-stu-id="48045-111">Configure the migrations assembly:</span></span>

   ``` csharp
   options.UseSqlServer(
       connectionString,
       x => x.MigrationsAssembly("MyApp.Migrations"));
   ```

5. <span data-ttu-id="48045-112">從啟動元件新增對您的遷移元件的參考。</span><span class="sxs-lookup"><span data-stu-id="48045-112">Add a reference to your migrations assembly from the startup assembly.</span></span>
   * <span data-ttu-id="48045-113">如果這會造成迴圈相依性，請更新類別庫的輸出路徑：</span><span class="sxs-lookup"><span data-stu-id="48045-113">If this causes a circular dependency, update the output path of the class library:</span></span>

     ``` xml
     <PropertyGroup>
       <OutputPath>..\MyStartupProject\bin\$(Configuration)\</OutputPath>
     </PropertyGroup>
     ```

<span data-ttu-id="48045-114">如果您已正確執行所有工作，您應該能夠將新的遷移新增至專案。</span><span class="sxs-lookup"><span data-stu-id="48045-114">If you did everything correctly, you should be able to add new migrations to the project.</span></span>

``` powershell
Add-Migration NewMigration -Project MyApp.Migrations
```

``` Console
dotnet ef migrations add NewMigration --project MyApp.Migrations
```
