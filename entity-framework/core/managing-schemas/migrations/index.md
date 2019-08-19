---
title: 移轉 - EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/05/2018
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: 7d97551044ae4a8fc42d1676199da884f3e2994d
ms.sourcegitcommit: 7b7f774a5966b20d2aed5435a672a1edbe73b6fb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2019
ms.locfileid: "69565254"
---
<a name="migrations"></a><span data-ttu-id="ee22b-102">移轉</span><span class="sxs-lookup"><span data-stu-id="ee22b-102">Migrations</span></span>
==========

<span data-ttu-id="ee22b-103">資料模型在開發期間變更，而且與資料庫不同步。</span><span class="sxs-lookup"><span data-stu-id="ee22b-103">A data model changes during development and gets out of sync with the database.</span></span> <span data-ttu-id="ee22b-104">您可以卸除資料庫，然後讓 EF 建立符合模型的新資料庫，但此程序會導致資料遺失。</span><span class="sxs-lookup"><span data-stu-id="ee22b-104">You can drop the database and let EF create a new one that matches the model, but this procedure results in the loss of data.</span></span> <span data-ttu-id="ee22b-105">EF Core 中的移轉功能讓您能夠以累加方式來更新資料庫結構描述，讓它與應用程式的資料模型保持同步，同時將現有的資料保留在資料庫中。</span><span class="sxs-lookup"><span data-stu-id="ee22b-105">The migrations feature in EF Core provides a way to incrementally update the database schema to keep it in sync with the application's data model while preserving existing data in the database.</span></span>

<span data-ttu-id="ee22b-106">移轉包含命令列工具和 API，可協助執行下列工作：</span><span class="sxs-lookup"><span data-stu-id="ee22b-106">Migrations includes command-line tools and APIs that help with the following tasks:</span></span>

* <span data-ttu-id="ee22b-107">[建立移轉](#create-a-migration)。</span><span class="sxs-lookup"><span data-stu-id="ee22b-107">[Create a migration](#create-a-migration).</span></span> <span data-ttu-id="ee22b-108">產生程式碼，該程式碼可以更新資料庫，以便與一組模型變更同步。</span><span class="sxs-lookup"><span data-stu-id="ee22b-108">Generate code that can update the database to sync it with a set of model changes.</span></span>
* <span data-ttu-id="ee22b-109">[更新資料庫](#update-the-database)。</span><span class="sxs-lookup"><span data-stu-id="ee22b-109">[Update the database](#update-the-database).</span></span> <span data-ttu-id="ee22b-110">套用擱置移轉來更新資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="ee22b-110">Apply pending migrations to update the database schema.</span></span>
* <span data-ttu-id="ee22b-111">[自訂移轉程式碼](#customize-migration-code)。</span><span class="sxs-lookup"><span data-stu-id="ee22b-111">[Customize migration code](#customize-migration-code).</span></span> <span data-ttu-id="ee22b-112">有時候產生的程式碼需要修改或補充。</span><span class="sxs-lookup"><span data-stu-id="ee22b-112">Sometimes the generated code needs to be modified or supplemented.</span></span>
* <span data-ttu-id="ee22b-113">[移除移轉](#remove-a-migration)。</span><span class="sxs-lookup"><span data-stu-id="ee22b-113">[Remove a migration](#remove-a-migration).</span></span> <span data-ttu-id="ee22b-114">刪除產生的程式碼。</span><span class="sxs-lookup"><span data-stu-id="ee22b-114">Delete the generated code.</span></span>
* <span data-ttu-id="ee22b-115">[還原移轉](#revert-a-migration)。</span><span class="sxs-lookup"><span data-stu-id="ee22b-115">[Revert a migration](#revert-a-migration).</span></span> <span data-ttu-id="ee22b-116">復原資料庫變更。</span><span class="sxs-lookup"><span data-stu-id="ee22b-116">Undo the database changes.</span></span>
* <span data-ttu-id="ee22b-117">[產生 SQL 指令碼](#generate-sql-scripts)。</span><span class="sxs-lookup"><span data-stu-id="ee22b-117">[Generate SQL scripts](#generate-sql-scripts).</span></span> <span data-ttu-id="ee22b-118">您需要指令碼以更新生產環境資料庫，或者針對移轉程式碼進行疑難排解。</span><span class="sxs-lookup"><span data-stu-id="ee22b-118">You might need a script to update a production database or to troubleshoot migration code.</span></span>
* <span data-ttu-id="ee22b-119">[在執行階段套用移轉](#apply-migrations-at-runtime)。</span><span class="sxs-lookup"><span data-stu-id="ee22b-119">[Apply migrations at runtime](#apply-migrations-at-runtime).</span></span> <span data-ttu-id="ee22b-120">當設計階段更新以及執行指令碼都不是最佳選項時，請呼叫 `Migrate()` 方法。</span><span class="sxs-lookup"><span data-stu-id="ee22b-120">When design-time updates and running scripts aren't the best options, call the `Migrate()` method.</span></span>

<a name="install-the-tools"></a><span data-ttu-id="ee22b-121">安裝工具</span><span class="sxs-lookup"><span data-stu-id="ee22b-121">Install the tools</span></span>
-----------------

<span data-ttu-id="ee22b-122">安裝[命令列工具](xref:core/miscellaneous/cli/index)：</span><span class="sxs-lookup"><span data-stu-id="ee22b-122">Install the [command-line tools](xref:core/miscellaneous/cli/index):</span></span>
* <span data-ttu-id="ee22b-123">針對 Visual Studio，我們建議使用[套件管理員主控台工具](xref:core/miscellaneous/cli/powershell)。</span><span class="sxs-lookup"><span data-stu-id="ee22b-123">For Visual Studio, we recommend the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell).</span></span>
* <span data-ttu-id="ee22b-124">針對其他開發環境，請選擇 [.NET Core CLI 工具](xref:core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="ee22b-124">For other development environments, choose the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet).</span></span>

<a name="create-a-migration"></a><span data-ttu-id="ee22b-125">建立移轉</span><span class="sxs-lookup"><span data-stu-id="ee22b-125">Create a migration</span></span>
------------------

<span data-ttu-id="ee22b-126">在您[定義起始模型](xref:core/modeling/index)之後，即可開始建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="ee22b-126">After you've [defined your initial model](xref:core/modeling/index), it's time to create the database.</span></span> <span data-ttu-id="ee22b-127">若要新增初始移轉，請執行下列命令。</span><span class="sxs-lookup"><span data-stu-id="ee22b-127">To add an initial migration, run the following command.</span></span>

``` powershell
Add-Migration InitialCreate
```
``` Console
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="ee22b-128">會新增三個檔案到您**移轉**目錄下的專案：</span><span class="sxs-lookup"><span data-stu-id="ee22b-128">Three files are added to your project under the **Migrations** directory:</span></span>

* <span data-ttu-id="ee22b-129">**XXXXXXXXXXXXXX_InitialCreate.cs** - 主要移轉檔案。</span><span class="sxs-lookup"><span data-stu-id="ee22b-129">**XXXXXXXXXXXXXX_InitialCreate.cs**--The main migrations file.</span></span> <span data-ttu-id="ee22b-130">包含套用移轉 (在 `Up()` 中) 及予以反轉 (在 `Down()` 中) 的必要作業。</span><span class="sxs-lookup"><span data-stu-id="ee22b-130">Contains the operations necessary to apply the migration (in `Up()`) and to revert it (in `Down()`).</span></span>
* <span data-ttu-id="ee22b-131">**XXXXXXXXXXXXXX_InitialCreate.Designer.cs** - 移轉中繼資料檔案。</span><span class="sxs-lookup"><span data-stu-id="ee22b-131">**XXXXXXXXXXXXXX_InitialCreate.Designer.cs**--The migrations metadata file.</span></span> <span data-ttu-id="ee22b-132">包含 EF 使用的資訊。</span><span class="sxs-lookup"><span data-stu-id="ee22b-132">Contains information used by EF.</span></span>
* <span data-ttu-id="ee22b-133">**MyContextModelSnapshot.cs** - 您目前模型的快照集。</span><span class="sxs-lookup"><span data-stu-id="ee22b-133">**MyContextModelSnapshot.cs**--A snapshot of your current model.</span></span> <span data-ttu-id="ee22b-134">用來決定新增下一個移轉時所要變更的項目。</span><span class="sxs-lookup"><span data-stu-id="ee22b-134">Used to determine what changed when adding the next migration.</span></span>

<span data-ttu-id="ee22b-135">檔案名稱中的時間戳記有助於使其依時間先後順序排列，以便您查看變更的進展。</span><span class="sxs-lookup"><span data-stu-id="ee22b-135">The timestamp in the filename helps keep them ordered chronologically so you can see the progression of changes.</span></span>

> [!TIP]
> <span data-ttu-id="ee22b-136">您可以自由移動移轉檔案和變更其命名空間。</span><span class="sxs-lookup"><span data-stu-id="ee22b-136">You are free to move Migrations files and change their namespace.</span></span> <span data-ttu-id="ee22b-137">新的移轉會作為最後一個移轉的同層級建立。</span><span class="sxs-lookup"><span data-stu-id="ee22b-137">New migrations are created as siblings of the last migration.</span></span>

<a name="update-the-database"></a><span data-ttu-id="ee22b-138">更新資料庫</span><span class="sxs-lookup"><span data-stu-id="ee22b-138">Update the database</span></span>
-------------------

<span data-ttu-id="ee22b-139">接下來，將移轉套用到資料庫以建立結構描述。</span><span class="sxs-lookup"><span data-stu-id="ee22b-139">Next, apply the migration to the database to create the schema.</span></span>

``` powershell
Update-Database
```
``` Console
dotnet ef database update
```

<a name="customize-migration-code"></a><span data-ttu-id="ee22b-140">自訂移轉程式碼</span><span class="sxs-lookup"><span data-stu-id="ee22b-140">Customize migration code</span></span>
------------------------

<span data-ttu-id="ee22b-141">對您的 EF Core 模型進行變更後，資料庫結構描述會失去同步。若要將其更新為最新狀態，請新增另一個移轉。</span><span class="sxs-lookup"><span data-stu-id="ee22b-141">After making changes to your EF Core model, the database schema might be out of sync. To bring it up to date, add another migration.</span></span> <span data-ttu-id="ee22b-142">您能夠以類似版本控制系統中認可訊息的方式來使用移轉名稱。</span><span class="sxs-lookup"><span data-stu-id="ee22b-142">The migration name can be used like a commit message in a version control system.</span></span> <span data-ttu-id="ee22b-143">例如，如果變更是要檢閱的新實體類別，您可以選擇像是 AddProductReviews  的名稱。</span><span class="sxs-lookup"><span data-stu-id="ee22b-143">For example, you might choose a name like *AddProductReviews* if the change is a new entity class for reviews.</span></span>

``` powershell
Add-Migration AddProductReviews
```
``` Console
dotnet ef migrations add AddProductReviews
```

<span data-ttu-id="ee22b-144">一旦建立移轉 (為其產生程式碼) 後，請檢閱程式碼的正確性，以及視需要新增、移除或修改任何作業，以便正確套用。</span><span class="sxs-lookup"><span data-stu-id="ee22b-144">Once the migration is scaffolded (code generated for it), review the code for accuracy and add, remove or modify any operations required to apply it correctly.</span></span>

<span data-ttu-id="ee22b-145">例如，移轉可能包含下列作業：</span><span class="sxs-lookup"><span data-stu-id="ee22b-145">For example, a migration might contain the following operations:</span></span>

``` csharp
migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");

migrationBuilder.AddColumn<string>(
    name: "Name",
    table: "Customer",
    nullable: true);
```

<span data-ttu-id="ee22b-146">雖然這些作業會使資料庫結構描述符合規範，但不會保存現有的客戶名稱。</span><span class="sxs-lookup"><span data-stu-id="ee22b-146">While these operations make the database schema compatible, they don't preserve the existing customer names.</span></span> <span data-ttu-id="ee22b-147">若要加以改善，請以下列格式重寫。</span><span class="sxs-lookup"><span data-stu-id="ee22b-147">To make it better, rewrite it as follows.</span></span>

``` csharp
migrationBuilder.AddColumn<string>(
    name: "Name",
    table: "Customer",
    nullable: true);

migrationBuilder.Sql(
@"
    UPDATE Customer
    SET Name = FirstName + ' ' + LastName;
");

migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");
```

> [!TIP]
> <span data-ttu-id="ee22b-148">當作業可能導致資料遺失時 (例如卸除資料行)，移轉建立程序會引發警告。</span><span class="sxs-lookup"><span data-stu-id="ee22b-148">The migration scaffolding process warns when an operation might result in data loss (like dropping a column).</span></span> <span data-ttu-id="ee22b-149">如果您看到該警告，請務必檢閱移轉程式碼的正確性。</span><span class="sxs-lookup"><span data-stu-id="ee22b-149">If you see that warning, be especially sure to review the migrations code for accuracy.</span></span>

<span data-ttu-id="ee22b-150">使用適當的命令將移轉套用到資料庫。</span><span class="sxs-lookup"><span data-stu-id="ee22b-150">Apply the migration to the database using the appropriate command.</span></span>

``` powershell
Update-Database
```
``` Console
dotnet ef database update
```

### <a name="empty-migrations"></a><span data-ttu-id="ee22b-151">空白的移轉</span><span class="sxs-lookup"><span data-stu-id="ee22b-151">Empty migrations</span></span>

<span data-ttu-id="ee22b-152">新增移轉而不進行任何模型變更有時很實用。</span><span class="sxs-lookup"><span data-stu-id="ee22b-152">Sometimes it's useful to add a migration without making any model changes.</span></span> <span data-ttu-id="ee22b-153">在這種情況下，新增移轉會建立具有空白類別的程式碼檔案。</span><span class="sxs-lookup"><span data-stu-id="ee22b-153">In this case, adding a new migration creates code files with empty classes.</span></span> <span data-ttu-id="ee22b-154">您可以自訂此移轉來自訂未與 EF Core 模型直接相關的作業。</span><span class="sxs-lookup"><span data-stu-id="ee22b-154">You can customize this migration to perform operations that don't directly relate to the EF Core model.</span></span> <span data-ttu-id="ee22b-155">您可能會想要以這種方式處理的項目如下：</span><span class="sxs-lookup"><span data-stu-id="ee22b-155">Some things you might want to manage this way are:</span></span>

* <span data-ttu-id="ee22b-156">全文檢索搜尋</span><span class="sxs-lookup"><span data-stu-id="ee22b-156">Full-Text Search</span></span>
* <span data-ttu-id="ee22b-157">函式</span><span class="sxs-lookup"><span data-stu-id="ee22b-157">Functions</span></span>
* <span data-ttu-id="ee22b-158">預存程序</span><span class="sxs-lookup"><span data-stu-id="ee22b-158">Stored procedures</span></span>
* <span data-ttu-id="ee22b-159">觸發程序</span><span class="sxs-lookup"><span data-stu-id="ee22b-159">Triggers</span></span>
* <span data-ttu-id="ee22b-160">檢視</span><span class="sxs-lookup"><span data-stu-id="ee22b-160">Views</span></span>

<a name="remove-a-migration"></a><span data-ttu-id="ee22b-161">移除移轉</span><span class="sxs-lookup"><span data-stu-id="ee22b-161">Remove a migration</span></span>
------------------
<span data-ttu-id="ee22b-162">在您新增移轉時，有時候會發現您必須在套用 EF Core 模型之前對其進行其他變更。</span><span class="sxs-lookup"><span data-stu-id="ee22b-162">Sometimes you add a migration and realize you need to make additional changes to your EF Core model before applying it.</span></span> <span data-ttu-id="ee22b-163">若要移除上一個移轉，請使用此命令。</span><span class="sxs-lookup"><span data-stu-id="ee22b-163">To remove the last migration, use this command.</span></span>

``` powershell
Remove-Migration
```
``` Console
dotnet ef migrations remove
```

<span data-ttu-id="ee22b-164">移除移轉後，您可以進行其他模型變更並再次予以新增。</span><span class="sxs-lookup"><span data-stu-id="ee22b-164">After removing the migration, you can make the additional model changes and add it again.</span></span>

<a name="revert-a-migration"></a><span data-ttu-id="ee22b-165">還原移轉</span><span class="sxs-lookup"><span data-stu-id="ee22b-165">Revert a migration</span></span>
------------------
<span data-ttu-id="ee22b-166">若您已經套用移轉 (或多個移轉) 到資料庫但需要還原，您可以使用相同命令來套用移轉，但必須指定所要復原到的移轉名稱。</span><span class="sxs-lookup"><span data-stu-id="ee22b-166">If you already applied a migration (or several migrations) to the database but need to revert it, you can use the same command to apply migrations, but specify the name of the migration you want to roll back to.</span></span>

``` powershell
Update-Database LastGoodMigration
```
``` Console
dotnet ef database update LastGoodMigration
```

<a name="generate-sql-scripts"></a><span data-ttu-id="ee22b-167">產生 SQL 指令碼</span><span class="sxs-lookup"><span data-stu-id="ee22b-167">Generate SQL scripts</span></span>
--------------------
<span data-ttu-id="ee22b-168">對移轉進行偵錯或將其部署到生產資料庫時，產生 SQL 指令碼很實用。</span><span class="sxs-lookup"><span data-stu-id="ee22b-168">When debugging your migrations or deploying them to a production database, it's useful to generate a SQL script.</span></span> <span data-ttu-id="ee22b-169">您可以進一步檢閱程式碼的精確度，並對其進行微調以符合生產資料庫的需求。</span><span class="sxs-lookup"><span data-stu-id="ee22b-169">The script can then be further reviewed for accuracy and tuned to fit the needs of a production database.</span></span> <span data-ttu-id="ee22b-170">指令碼也可以搭配部署技術使用。</span><span class="sxs-lookup"><span data-stu-id="ee22b-170">The script can also be used in conjunction with a deployment technology.</span></span> <span data-ttu-id="ee22b-171">基本命令如下。</span><span class="sxs-lookup"><span data-stu-id="ee22b-171">The basic command is as follows.</span></span>

``` powershell
Script-Migration
```
``` Console
dotnet ef migrations script
```

<span data-ttu-id="ee22b-172">這個命令有多個選項。</span><span class="sxs-lookup"><span data-stu-id="ee22b-172">There are several options to this command.</span></span>

<span data-ttu-id="ee22b-173">執行指令碼之前，**from** 移轉應該是套用到資料庫的最後一個移轉。</span><span class="sxs-lookup"><span data-stu-id="ee22b-173">The **from** migration should be the last migration applied to the database before running the script.</span></span> <span data-ttu-id="ee22b-174">若未套用任何移轉，請指定 `0` (此為預設)。</span><span class="sxs-lookup"><span data-stu-id="ee22b-174">If no migrations have been applied, specify `0` (this is the default).</span></span>

<span data-ttu-id="ee22b-175">執行指令碼之後，**to** 移轉是套用到資料庫的最後一個移轉。</span><span class="sxs-lookup"><span data-stu-id="ee22b-175">The **to** migration is the last migration that will be applied to the database after running the script.</span></span> <span data-ttu-id="ee22b-176">預設為您專案中的最後一個移轉。</span><span class="sxs-lookup"><span data-stu-id="ee22b-176">This defaults to the last migration in your project.</span></span>

<span data-ttu-id="ee22b-177">您可以選擇產生**等冪**指令碼。</span><span class="sxs-lookup"><span data-stu-id="ee22b-177">An **idempotent** script can optionally be generated.</span></span> <span data-ttu-id="ee22b-178">當移轉尚未套用到資料庫時，此指令碼才適用於移轉。</span><span class="sxs-lookup"><span data-stu-id="ee22b-178">This script only applies migrations if they haven't already been applied to the database.</span></span> <span data-ttu-id="ee22b-179">當您不確定套用到資料庫的上一個移轉是哪項，或是要部署到可能在不同移轉的多個資料庫時，這個方法很實用。</span><span class="sxs-lookup"><span data-stu-id="ee22b-179">This is useful if you don't exactly know what the last migration applied to the database was or if you are deploying to multiple databases that may each be at a different migration.</span></span>

<a name="apply-migrations-at-runtime"></a><span data-ttu-id="ee22b-180">在執行階段套用移轉</span><span class="sxs-lookup"><span data-stu-id="ee22b-180">Apply migrations at runtime</span></span>
---------------------------
<span data-ttu-id="ee22b-181">某些應用程式在啟動或初次執行期間，可能會想要在執行階段套用移轉。</span><span class="sxs-lookup"><span data-stu-id="ee22b-181">Some apps may want to apply migrations at runtime during startup or first run.</span></span> <span data-ttu-id="ee22b-182">請使用 `Migrate()` 方法來執行此動作。</span><span class="sxs-lookup"><span data-stu-id="ee22b-182">Do this using the `Migrate()` method.</span></span>

<span data-ttu-id="ee22b-183">此方法的基礎建立在 `IMigrator` 服務之上，其可用於更進階的案例。</span><span class="sxs-lookup"><span data-stu-id="ee22b-183">This method builds on top of the `IMigrator` service, which can be used for more advanced scenarios.</span></span> <span data-ttu-id="ee22b-184">您可以使用 `myDbContext.GetInfrastructure().GetService<IMigrator>()` 來加以存取。</span><span class="sxs-lookup"><span data-stu-id="ee22b-184">Use `myDbContext.GetInfrastructure().GetService<IMigrator>()` to access it.</span></span>

``` csharp
myDbContext.Database.Migrate();
```

> [!WARNING]
> * <span data-ttu-id="ee22b-185">並非所有應用程式都適合此方法。</span><span class="sxs-lookup"><span data-stu-id="ee22b-185">This approach isn't for everyone.</span></span> <span data-ttu-id="ee22b-186">雖然這相當適合具本機資料庫的應用程式，但大多數應用程式會需要更強固的部署策略，例如產生 SQL 指令碼。</span><span class="sxs-lookup"><span data-stu-id="ee22b-186">While it's great for apps with a local database, most applications will require more robust deployment strategy like generating SQL scripts.</span></span>
> * <span data-ttu-id="ee22b-187">請勿在 `Migrate()` 之前呼叫 `EnsureCreated()`。</span><span class="sxs-lookup"><span data-stu-id="ee22b-187">Don't call `EnsureCreated()` before `Migrate()`.</span></span> <span data-ttu-id="ee22b-188">`EnsureCreated()` 會略過移轉而建立結構描述，因此造成 `Migrate()` 失敗。</span><span class="sxs-lookup"><span data-stu-id="ee22b-188">`EnsureCreated()` bypasses Migrations to create the schema, which causes `Migrate()` to fail.</span></span>

<a name="next-steps"></a><span data-ttu-id="ee22b-189">後續步驟</span><span class="sxs-lookup"><span data-stu-id="ee22b-189">Next steps</span></span>
----------

<span data-ttu-id="ee22b-190">如需詳細資訊，請參閱 <xref:core/miscellaneous/cli/index>。</span><span class="sxs-lookup"><span data-stu-id="ee22b-190">For more information, see <xref:core/miscellaneous/cli/index>.</span></span>
