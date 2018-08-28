---
title: 移轉 - EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: 4a5d6f3798c7af7597f95cebea1aeb9e5e58d277
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996518"
---
<a name="migrations"></a><span data-ttu-id="7d0d0-102">移轉</span><span class="sxs-lookup"><span data-stu-id="7d0d0-102">Migrations</span></span>
==========
<span data-ttu-id="7d0d0-103">移轉提供的方式可讓您以累加的方式將結構描述變更套用到資料庫，使其能夠與您的 EF Core 模型保持同步，同時將現有的資料保留在資料庫中。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-103">Migrations provide a way to incrementally apply schema changes to the database to keep it in sync with your EF Core model while preserving existing data in the database.</span></span>

<a name="creating-the-database"></a><span data-ttu-id="7d0d0-104">建立資料庫</span><span class="sxs-lookup"><span data-stu-id="7d0d0-104">Creating the database</span></span>
---------------------
<span data-ttu-id="7d0d0-105">當您[定義起始模型][1]後，即可開始建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-105">After you've [defined your initial model][1], it's time to create the database.</span></span> <span data-ttu-id="7d0d0-106">若要這麼做，請新增起始移轉。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-106">To do this, add an initial migration.</span></span>
<span data-ttu-id="7d0d0-107">安裝 [EF Core 工具][2]並執行適當的命令。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-107">Install the [EF Core Tools][2] and run the appropriate command.</span></span>

``` powershell
Add-Migration InitialCreate
```
``` Console
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="7d0d0-108">會新增三個檔案到您**移轉**目錄下的專案：</span><span class="sxs-lookup"><span data-stu-id="7d0d0-108">Three files are added to your project under the **Migrations** directory:</span></span>

* <span data-ttu-id="7d0d0-109">**00000000000000_InitialCreate.cs** - 主要移轉檔案。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-109">**00000000000000_InitialCreate.cs**--The main migrations file.</span></span> <span data-ttu-id="7d0d0-110">包含套用移轉 (在 `Up()` 中) 及予以反轉 (在 `Down()` 中) 的必要作業。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-110">Contains the operations necessary to apply the migration (in `Up()`) and to revert it (in `Down()`).</span></span>
* <span data-ttu-id="7d0d0-111">**00000000000000_InitialCreate.Designer.cs** - 移轉中繼資料檔案。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-111">**00000000000000_InitialCreate.Designer.cs**--The migrations metadata file.</span></span> <span data-ttu-id="7d0d0-112">包含 EF 使用的資訊。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-112">Contains information used by EF.</span></span>
* <span data-ttu-id="7d0d0-113">**MyContextModelSnapshot.cs** - 您目前模型的快照集。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-113">**MyContextModelSnapshot.cs**--A snapshot of your current model.</span></span> <span data-ttu-id="7d0d0-114">用來決定新增下一個移轉時所要變更的項目。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-114">Used to determine what changed when adding the next migration.</span></span>

<span data-ttu-id="7d0d0-115">檔案名稱中的時間戳記有助於使其依時間先後順序排列，以便您查看變更的進展。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-115">The timestamp in the filename helps keep them ordered chronologically so you can see the progression of changes.</span></span>

> [!TIP]
> <span data-ttu-id="7d0d0-116">您可以自由移動移轉檔案和變更其命名空間。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-116">You are free to move Migrations files and change their namespace.</span></span> <span data-ttu-id="7d0d0-117">新的移轉會作為最後一個移轉的同層級建立。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-117">New migrations are created as siblings of the last migration.</span></span>

<span data-ttu-id="7d0d0-118">接下來，將移轉套用到資料庫以建立結構描述。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-118">Next, apply the migration to the database to create the schema.</span></span>

``` powershell
Update-Database
```
``` Console
dotnet ef database update
```

<a name="adding-another-migration"></a><span data-ttu-id="7d0d0-119">新增另一個移轉</span><span class="sxs-lookup"><span data-stu-id="7d0d0-119">Adding another migration</span></span>
------------------------
<span data-ttu-id="7d0d0-120">對您的 EF Core 模型進行變更後，資料庫結構描述會失去同步。若要將其更新為最新狀態，請新增另一個移轉。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-120">After making changes to your EF Core model, the database schema will be out of sync. To bring it up to date, add another migration.</span></span> <span data-ttu-id="7d0d0-121">您能夠以類似版本控制系統中認可訊息的方式來使用移轉名稱。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-121">The migration name can be used like a commit message in a version control system.</span></span> <span data-ttu-id="7d0d0-122">例如，如果我進行變更以儲存客戶對產品的評論，我可能會選擇類似 *AddProductReviews* 的項目。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-122">For example, if I made changes to save customer reviews of products, I might choose something like *AddProductReviews*.</span></span>

``` powershell
Add-Migration AddProductReviews
```
``` Console
dotnet ef migrations add AddProductReviews
```

<span data-ttu-id="7d0d0-123">建構移轉後，您應該檢視其精確度，並新增任何其他必要作業以正確予以套用。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-123">Once the migration is scaffolded, you should review it for accuracy and add any additional operations required to apply it correctly.</span></span> <span data-ttu-id="7d0d0-124">例如，您的移轉可能包含下列作業：</span><span class="sxs-lookup"><span data-stu-id="7d0d0-124">For example, your migration might contain the following operations:</span></span>

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

<span data-ttu-id="7d0d0-125">雖然這些作業會使資料庫結構描述符合規範，但不會保存現有的客戶名稱。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-125">While these operations make the database schema compatible, they don't preserve the existing customer names.</span></span> <span data-ttu-id="7d0d0-126">若要加以改善，請以下列格式重寫。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-126">To make it better, rewrite it as follows.</span></span>

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
> <span data-ttu-id="7d0d0-127">當作業已建構且可能導致資料遺失時 (例如卸除資料行)，新增移轉會引發警告。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-127">Adding a new migration warns when an operation is scaffolded that may result in data loss (like dropping a column).</span></span> <span data-ttu-id="7d0d0-128">請務必確認這些移轉的正確性。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-128">Be sure to especially review these migrations for accuracy.</span></span>

<span data-ttu-id="7d0d0-129">使用適當的命令將移轉套用到資料庫。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-129">Apply the migration to the database using the appropriate command.</span></span>

``` powershell
Update-Database
```
``` Console
dotnet ef database update
```

<a name="removing-a-migration"></a><span data-ttu-id="7d0d0-130">移除移轉</span><span class="sxs-lookup"><span data-stu-id="7d0d0-130">Removing a migration</span></span>
--------------------
<span data-ttu-id="7d0d0-131">在您新增移轉時，有時候會發現您必須在套用 EF Core 模型之前對其進行其他變更。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-131">Sometimes you add a migration and realize you need to make additional changes to your EF Core model before applying it.</span></span>
<span data-ttu-id="7d0d0-132">若要移除上一個移轉，請使用此命令。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-132">To remove the last migration, use this command.</span></span>

``` powershell
Remove-Migration
```
``` Console
dotnet ef migrations remove
```

<span data-ttu-id="7d0d0-133">移除移轉後，您可以進行其他模型變更並再次予以新增。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-133">After removing it, you can make the additional model changes and add it again.</span></span>

<a name="reverting-a-migration"></a><span data-ttu-id="7d0d0-134">還原移轉</span><span class="sxs-lookup"><span data-stu-id="7d0d0-134">Reverting a migration</span></span>
---------------------
<span data-ttu-id="7d0d0-135">若您已經套用移轉 (或多個移轉) 到資料庫但需要還原，您可以使用相同命令來套用移轉，但必須指定所要復原到的移轉名稱。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-135">If you already applied a migration (or several migrations) to the database but need to revert it, you can use the same command to apply migrations, but specify the name of the migration you want to roll back to.</span></span>

``` powershell
Update-Database LastGoodMigration
```
``` Console
dotnet ef database update LastGoodMigration
```

<a name="empty-migrations"></a><span data-ttu-id="7d0d0-136">空白的移轉</span><span class="sxs-lookup"><span data-stu-id="7d0d0-136">Empty migrations</span></span>
----------------
<span data-ttu-id="7d0d0-137">新增移轉而不進行任何模型變更有時很實用。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-137">Sometimes it's useful to add a migration without making any model changes.</span></span> <span data-ttu-id="7d0d0-138">在這種情況下，新增新的移轉會建立一個空白的移轉。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-138">In this case, adding a new migration creates an empty one.</span></span> <span data-ttu-id="7d0d0-139">您可以自訂此移轉來自訂未與 EF Core 模型直接相關的作業。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-139">You can customize this migration to perform operations that don't directly relate to the EF Core model.</span></span>
<span data-ttu-id="7d0d0-140">您可能會想要以這種方式處理的項目如下：</span><span class="sxs-lookup"><span data-stu-id="7d0d0-140">Some things you might want to manage this way are:</span></span>

* <span data-ttu-id="7d0d0-141">全文檢索搜尋</span><span class="sxs-lookup"><span data-stu-id="7d0d0-141">Full-Text Search</span></span>
* <span data-ttu-id="7d0d0-142">函式</span><span class="sxs-lookup"><span data-stu-id="7d0d0-142">Functions</span></span>
* <span data-ttu-id="7d0d0-143">預存程序</span><span class="sxs-lookup"><span data-stu-id="7d0d0-143">Stored procedures</span></span>
* <span data-ttu-id="7d0d0-144">觸發程序</span><span class="sxs-lookup"><span data-stu-id="7d0d0-144">Triggers</span></span>
* <span data-ttu-id="7d0d0-145">檢視</span><span class="sxs-lookup"><span data-stu-id="7d0d0-145">Views</span></span>
* <span data-ttu-id="7d0d0-146">等諸如此類的事情。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-146">etc.</span></span>

<a name="generating-a-sql-script"></a><span data-ttu-id="7d0d0-147">產生 SQL 指令碼</span><span class="sxs-lookup"><span data-stu-id="7d0d0-147">Generating a SQL script</span></span>
-----------------------
<span data-ttu-id="7d0d0-148">對移轉進行偵錯或將其部署到生產資料庫時，產生 SQL 指令碼很實用。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-148">When debugging your migrations or deploying them to a production database, it's useful to generate a SQL script.</span></span> <span data-ttu-id="7d0d0-149">您可以進一步檢閱程式碼的精確度，並對其進行微調以符合生產資料庫的需求。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-149">The script can then be further reviewed for accuracy and tuned to fit the needs of a production database.</span></span> <span data-ttu-id="7d0d0-150">指令碼也可以搭配部署技術使用。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-150">The script can also be used in conjunction with a deployment technology.</span></span> <span data-ttu-id="7d0d0-151">基本命令如下。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-151">The basic command is as follows.</span></span>

``` powershell
Script-Migration
```
``` Console
dotnet ef migrations script
```

<span data-ttu-id="7d0d0-152">這個命令有多個選項。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-152">There are several options to this command.</span></span>

<span data-ttu-id="7d0d0-153">執行指令碼之前，**from** 移轉應該是套用到資料庫的最後一個移轉。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-153">The **from** migration should be the last migration applied to the database before running the script.</span></span> <span data-ttu-id="7d0d0-154">若未套用任何移轉，請指定 `0` (此為預設)。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-154">If no migrations have been applied, specify `0` (this is the default).</span></span>

<span data-ttu-id="7d0d0-155">執行指令碼之後，**to** 移轉是套用到資料庫的最後一個移轉。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-155">The **to** migration is the last migration that will be applied to the database after running the script.</span></span> <span data-ttu-id="7d0d0-156">預設為您專案中的最後一個移轉。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-156">This defaults to the last migration in your project.</span></span>

<span data-ttu-id="7d0d0-157">您可以選擇產生**等冪**指令碼。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-157">An **idempotent** script can optionally be generated.</span></span> <span data-ttu-id="7d0d0-158">當移轉尚未套用到資料庫時，此指令碼才適用於移轉。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-158">This script only applies migrations if they haven't already been applied to the database.</span></span> <span data-ttu-id="7d0d0-159">當您不確定套用到資料庫的上一個移轉是哪項，或是要部署到可能在不同移轉的多個資料庫時，這個方法很實用。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-159">This is useful if you don't exactly know what the last migration applied to the database was or if you are deploying to multiple databases that may each be at a different migration.</span></span>

<a name="applying-migrations-at-runtime"></a><span data-ttu-id="7d0d0-160">在執行階段套用移轉</span><span class="sxs-lookup"><span data-stu-id="7d0d0-160">Applying migrations at runtime</span></span>
------------------------------
<span data-ttu-id="7d0d0-161">某些應用程式在啟動或初次執行期間，可能會想要在執行階段套用移轉。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-161">Some apps may want to apply migrations at runtime during startup or first run.</span></span> <span data-ttu-id="7d0d0-162">請使用 `Migrate()` 方法來執行此動作。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-162">Do this using the `Migrate()` method.</span></span>

<span data-ttu-id="7d0d0-163">請注意，並非所有應用程式都適合此方法。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-163">Caution, this approach isn't for everyone.</span></span> <span data-ttu-id="7d0d0-164">雖然這相當適合具本機資料庫的應用程式，但大多數應用程式會需要更強固的部署策略，例如產生 SQL 指令碼。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-164">While it's great for apps with a local database, most applications will require more robust deployment strategy like generating SQL scripts.</span></span>

``` csharp
myDbContext.Database.Migrate();
```

> [!WARNING]
> <span data-ttu-id="7d0d0-165">請勿在 `Migrate()` 之前呼叫 `EnsureCreated()`。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-165">Don't call `EnsureCreated()` before `Migrate()`.</span></span> <span data-ttu-id="7d0d0-166">`EnsureCreated()` 會略過移轉而建立結構描述，因此造成 `Migrate()` 失敗。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-166">`EnsureCreated()` bypasses Migrations to create the schema, which causes `Migrate()` to fail.</span></span>

> [!NOTE]
> <span data-ttu-id="7d0d0-167">此方法的基礎建立在 `IMigrator` 服務之上，其可用於更進階的案例。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-167">This method builds on top of the `IMigrator` service, which can be used for more advanced scenarios.</span></span> <span data-ttu-id="7d0d0-168">您可以使用 `DbContext.GetService<IMigrator>()` 來加以存取。</span><span class="sxs-lookup"><span data-stu-id="7d0d0-168">Use `DbContext.GetService<IMigrator>()` to access it.</span></span>


  [1]: ../../modeling/index.md
  [2]: ../../miscellaneous/cli/index.md
