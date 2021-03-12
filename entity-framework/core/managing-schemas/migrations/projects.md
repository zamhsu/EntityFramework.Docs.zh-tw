---
title: 使用個別的遷移專案-EF Core
description: 使用不同的遷移專案來管理使用 Entity Framework Core 的資料庫架構
author: bricelam
ms.date: 11/06/2020
uid: core/managing-schemas/migrations/projects
ms.openlocfilehash: 9a6b8977f9d7bcdae0fb9aea6966a7eb43e9e7db
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024143"
---
# <a name="using-a-separate-migrations-project"></a><span data-ttu-id="3a673-103">使用個別的遷移專案</span><span class="sxs-lookup"><span data-stu-id="3a673-103">Using a Separate Migrations Project</span></span>

<span data-ttu-id="3a673-104">您可能會想要將您的遷移儲存在與包含的專案不同的專案中 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="3a673-104">You may want to store your migrations in a different project than the one containing your `DbContext`.</span></span> <span data-ttu-id="3a673-105">您也可以使用這項策略來維護多個遷移集，例如一個用於開發，另一個用於發行到發行的升級。</span><span class="sxs-lookup"><span data-stu-id="3a673-105">You can also use this strategy to maintain multiple sets of migrations, for example, one for development and another for release-to-release upgrades.</span></span>

> [!TIP]
> <span data-ttu-id="3a673-106">您可以檢視本文中的 [GitHut 範例](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Schemas/ThreeProjectMigrations)。</span><span class="sxs-lookup"><span data-stu-id="3a673-106">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Schemas/ThreeProjectMigrations).</span></span>

## <a name="steps"></a><span data-ttu-id="3a673-107">步驟</span><span class="sxs-lookup"><span data-stu-id="3a673-107">Steps</span></span>

1. <span data-ttu-id="3a673-108">建立新的類別庫。</span><span class="sxs-lookup"><span data-stu-id="3a673-108">Create a new class library.</span></span>

2. <span data-ttu-id="3a673-109">新增 DbCoNtext 專案的參考。</span><span class="sxs-lookup"><span data-stu-id="3a673-109">Add a reference to your DbContext project.</span></span>

3. <span data-ttu-id="3a673-110">將遷移和模型快照集檔案移至類別庫。</span><span class="sxs-lookup"><span data-stu-id="3a673-110">Move the migrations and model snapshot files to the class library.</span></span>
   > [!TIP]
   > <span data-ttu-id="3a673-111">如果您沒有任何現有的遷移，請在包含 DbCoNtext 的專案中產生一個，然後將它移動。</span><span class="sxs-lookup"><span data-stu-id="3a673-111">If you have no existing migrations, generate one in the project containing the DbContext then move it.</span></span>
   > <span data-ttu-id="3a673-112">這點很重要，因為如果遷移專案不包含現有的遷移，Add-Migration 命令將找不到 DbCoNtext。</span><span class="sxs-lookup"><span data-stu-id="3a673-112">This is important because if the migrations project does not contain an existing migration, the Add-Migration command will be unable to find the DbContext.</span></span>

4. <span data-ttu-id="3a673-113">設定遷移元件：</span><span class="sxs-lookup"><span data-stu-id="3a673-113">Configure the migrations assembly:</span></span>

   [!code-csharp[](../../../../samples/core/Schemas/ThreeProjectMigrations/WebApplication1/Startup.cs#snippet_MigrationsAssembly)]

5. <span data-ttu-id="3a673-114">從 **啟始** 專案加入您的遷移專案的參考。</span><span class="sxs-lookup"><span data-stu-id="3a673-114">Add a reference to your migrations project from the **startup** project.</span></span>

   ```xml
   <ItemGroup>
     <ProjectReference Include="..\WebApplication1.Migrations\WebApplication1.Migrations.csproj">
   </ItemGroup>
   ```

   <span data-ttu-id="3a673-115">如果這會造成迴圈相依性，您可以改為更新 **遷移** 專案的基底輸出路徑：</span><span class="sxs-lookup"><span data-stu-id="3a673-115">If this causes a circular dependency, you can update the base output path of the **migrations** project instead:</span></span>

   ```xml
   <PropertyGroup>
     <BaseOutputPath>..\WebApplication1\bin\</BaseOutputPath>
   </PropertyGroup>
   ```

<span data-ttu-id="3a673-116">如果一切都正確，您應該能夠將新的遷移新增至專案。</span><span class="sxs-lookup"><span data-stu-id="3a673-116">If you did everything correctly, you should be able to add new migrations to the project.</span></span>

## <a name="net-core-cli"></a>[<span data-ttu-id="3a673-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3a673-117">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add NewMigration --project WebApplication1.Migrations
```

## <a name="visual-studio"></a>[<span data-ttu-id="3a673-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3a673-118">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration NewMigration -Project WebApplication1.Migrations
```

***
