---
title: 管理遷移-EF Core
description: 新增、移除及以其他方式管理使用 Entity Framework Core 的資料庫架構遷移
author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/managing
ms.openlocfilehash: fdfda6f3dea306fbbc343c1be3f4d5754d1f65c4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062057"
---
# <a name="managing-migrations"></a><span data-ttu-id="2e7ab-103">管理遷移</span><span class="sxs-lookup"><span data-stu-id="2e7ab-103">Managing Migrations</span></span>

<span data-ttu-id="2e7ab-104">當您的模型變更時，會在一般開發過程中加入和移除遷移，並將遷移檔案簽入您專案的原始檔控制中。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-104">As your model changes, migrations are added and removed as part of normal development, and the migration files are checked into your project's source control.</span></span> <span data-ttu-id="2e7ab-105">若要管理遷移，您必須先安裝 [EF Core 命令列工具](xref:core/miscellaneous/cli/index)。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-105">To manage migrations, you must first install the [EF Core command-line tools](xref:core/miscellaneous/cli/index).</span></span>

> [!TIP]
> <span data-ttu-id="2e7ab-106">如果 `DbContext` 與啟始專案位於不同的組件中，您可以在[套件管理員主控台工具](xref:core/miscellaneous/cli/powershell#target-and-startup-project)或 [.NET Core CLI 工具](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project)中明確指定目標和啟始專案。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-106">If the `DbContext` is in a different assembly than the startup project, you can explicitly specify the target and startup projects in either the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell#target-and-startup-project) or the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="2e7ab-107">新增移轉</span><span class="sxs-lookup"><span data-stu-id="2e7ab-107">Add a migration</span></span>

<span data-ttu-id="2e7ab-108">變更您的模型之後，您可以新增該變更的遷移：</span><span class="sxs-lookup"><span data-stu-id="2e7ab-108">After your model has been changed, you can add a migration for that change:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="2e7ab-109">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2e7ab-109">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

### <a name="visual-studio"></a>[<span data-ttu-id="2e7ab-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e7ab-110">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration AddBlogCreatedTimestamp
```

***

<span data-ttu-id="2e7ab-111">您能夠以類似版本控制系統中認可訊息的方式來使用移轉名稱。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-111">The migration name can be used like a commit message in a version control system.</span></span> <span data-ttu-id="2e7ab-112">例如，如果變更是您的實體上的新屬性，您可以選擇 *AddBlogCreatedTimestamp* 之類的名稱 `CreatedTimestamp` `Blog` 。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-112">For example, you might choose a name like *AddBlogCreatedTimestamp* if the change is a new `CreatedTimestamp` property on your `Blog` entity.</span></span>

<span data-ttu-id="2e7ab-113">會新增三個檔案到您**移轉**目錄下的專案：</span><span class="sxs-lookup"><span data-stu-id="2e7ab-113">Three files are added to your project under the **Migrations** directory:</span></span>

* <span data-ttu-id="2e7ab-114">**XXXXXXXXXXXXXX_AddCreatedTimestamp .cs**--主要的遷移檔。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-114">**XXXXXXXXXXXXXX_AddCreatedTimestamp.cs**--The main migrations file.</span></span> <span data-ttu-id="2e7ab-115">包含套用移轉 (在 `Up` 中) 及予以反轉 (在 `Down` 中) 的必要作業。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-115">Contains the operations necessary to apply the migration (in `Up`) and to revert it (in `Down`).</span></span>
* <span data-ttu-id="2e7ab-116">**XXXXXXXXXXXXXX_AddCreatedTimestamp .cs**--遷移中繼資料檔案。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-116">**XXXXXXXXXXXXXX_AddCreatedTimestamp.Designer.cs**--The migrations metadata file.</span></span> <span data-ttu-id="2e7ab-117">包含 EF 使用的資訊。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-117">Contains information used by EF.</span></span>
* <span data-ttu-id="2e7ab-118">**MyContextModelSnapshot.cs** - 您目前模型的快照集。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-118">**MyContextModelSnapshot.cs**--A snapshot of your current model.</span></span> <span data-ttu-id="2e7ab-119">用來決定新增下一個移轉時所要變更的項目。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-119">Used to determine what changed when adding the next migration.</span></span>

<span data-ttu-id="2e7ab-120">檔案名稱中的時間戳記有助於使其依時間先後順序排列，以便您查看變更的進展。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-120">The timestamp in the filename helps keep them ordered chronologically so you can see the progression of changes.</span></span>

### <a name="namespaces"></a><span data-ttu-id="2e7ab-121">命名空間</span><span class="sxs-lookup"><span data-stu-id="2e7ab-121">Namespaces</span></span>

<span data-ttu-id="2e7ab-122">您可以自由地手動移動移轉檔案及變更其命名空間。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-122">You are free to move Migrations files and change their namespace manually.</span></span> <span data-ttu-id="2e7ab-123">新的移轉會作為最後一個移轉的同層級建立。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-123">New migrations are created as siblings of the last migration.</span></span> <span data-ttu-id="2e7ab-124">或者，您可以在產生時期指定命名空間，如下所示：</span><span class="sxs-lookup"><span data-stu-id="2e7ab-124">Alternatively, you can specify the namespace at generation time as follows:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="2e7ab-125">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2e7ab-125">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --namespace Your.Namespace
```

### <a name="visual-studio"></a>[<span data-ttu-id="2e7ab-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e7ab-126">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration InitialCreate -Namespace Your.Namespace
```

***

## <a name="customize-migration-code"></a><span data-ttu-id="2e7ab-127">自訂移轉程式碼</span><span class="sxs-lookup"><span data-stu-id="2e7ab-127">Customize migration code</span></span>

<span data-ttu-id="2e7ab-128">EF Core 通常會建立精確的遷移，您應該一律檢查程式碼，並確定其對應至所需的變更;在某些情況下，甚至必須這樣做。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-128">While EF Core generally creates accurate migrations, you should always review the code and make sure it corresponds to the desired change; in some cases, it is even necessary to do so.</span></span>

### <a name="column-renames"></a><span data-ttu-id="2e7ab-129">資料行重新命名</span><span class="sxs-lookup"><span data-stu-id="2e7ab-129">Column renames</span></span>

<span data-ttu-id="2e7ab-130">其中一個值得注意的範例是，當您重新命名屬性時，需要進行自訂的遷移。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-130">One notable example where customizing migrations is required is when renaming a property.</span></span> <span data-ttu-id="2e7ab-131">例如，如果您將屬性從重新命名 `Name` 為 `FullName` ，EF Core 將會產生下列遷移：</span><span class="sxs-lookup"><span data-stu-id="2e7ab-131">For example, if you rename a property from `Name` to `FullName`, EF Core will generate the following migration:</span></span>

```csharp
migrationBuilder.DropColumn(
    name: "Name",
    table: "Customers");

migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customers",
    nullable: true);
```

<span data-ttu-id="2e7ab-132">EF Core 通常無法知道何時要卸載資料行，並建立新的資料行 (兩個不同的變更) ，以及何時應重新命名資料行。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-132">EF Core is generally unable to know when the intention is to drop a column and create a new one (two separate changes), and when a column should be renamed.</span></span> <span data-ttu-id="2e7ab-133">如果上述的遷移依原樣套用，您所有的客戶名稱都會遺失。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-133">If the above migration is applied as-is, all your customer names will be lost.</span></span> <span data-ttu-id="2e7ab-134">若要重新命名資料行，請以下列方式取代上述產生的遷移：</span><span class="sxs-lookup"><span data-stu-id="2e7ab-134">To rename a column, replace the above generated migration with the following:</span></span>

```csharp
migrationBuilder.RenameColumn(
    name: "Name",
    table: "Customers",
    newName: "FullName");
```

> [!TIP]
> <span data-ttu-id="2e7ab-135">當作業可能導致資料遺失時 (例如卸除資料行)，移轉建立程序會引發警告。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-135">The migration scaffolding process warns when an operation might result in data loss (like dropping a column).</span></span> <span data-ttu-id="2e7ab-136">如果您看到該警告，請務必檢閱移轉程式碼的正確性。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-136">If you see that warning, be especially sure to review the migrations code for accuracy.</span></span>

### <a name="adding-raw-sql"></a><span data-ttu-id="2e7ab-137">新增原始 SQL</span><span class="sxs-lookup"><span data-stu-id="2e7ab-137">Adding raw SQL</span></span>

<span data-ttu-id="2e7ab-138">雖然您可以透過內建 API 來重新命名資料行，但在許多情況下都無法這麼做。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-138">While renaming a column can be achieved via a built-in API, in many cases that is not possible.</span></span> <span data-ttu-id="2e7ab-139">例如，我們可能想要 `FirstName` 使用單一的新屬性來取代現有和 `LastName` 屬性 `FullName` 。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-139">For example, we may want to replace existing `FirstName` and `LastName` properties with a single, new `FullName` property.</span></span> <span data-ttu-id="2e7ab-140">EF Core 所產生的遷移將如下所示：</span><span class="sxs-lookup"><span data-stu-id="2e7ab-140">The migration generated by EF Core will be the following:</span></span>

```csharp
migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");

migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customer",
    nullable: true);
```

<span data-ttu-id="2e7ab-141">同樣地，這會導致不必要的資料遺失。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-141">As before, this would cause unwanted data loss.</span></span> <span data-ttu-id="2e7ab-142">若要從舊的資料行傳送資料，我們會重新排列遷移，並引進原始 SQL 作業，如下所示：</span><span class="sxs-lookup"><span data-stu-id="2e7ab-142">To transfer the data from the old columns, we rearrange the migrations and introduce a raw SQL operation as follows:</span></span>

```csharp
migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customer",
    nullable: true);

migrationBuilder.Sql(
@"
    UPDATE Customer
    SET FullName = FirstName + ' ' + LastName;
");

migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");
```

### <a name="arbitrary-changes-via-raw-sql"></a><span data-ttu-id="2e7ab-143">透過原始 SQL 的任意變更</span><span class="sxs-lookup"><span data-stu-id="2e7ab-143">Arbitrary changes via raw SQL</span></span>

<span data-ttu-id="2e7ab-144">原始 SQL 也可以用來管理 EF Core 不知道的資料庫物件。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-144">Raw SQL can also be used to manage database objects that EF Core isn't aware of.</span></span> <span data-ttu-id="2e7ab-145">若要這樣做，請新增遷移，而不進行任何模型變更;將會產生空白的遷移，然後您可以使用原始的 SQL 作業來填入。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-145">To do this, add a migration without making any model change; an empty migration will be generated, which you can then populate with raw SQL operations.</span></span>

<span data-ttu-id="2e7ab-146">例如，下列遷移會建立 SQL Server 預存程式：</span><span class="sxs-lookup"><span data-stu-id="2e7ab-146">For example, the following migration creates a SQL Server stored procedure:</span></span>

```csharp
migrationBuilder.Sql(
@"
    EXEC ('CREATE PROCEDURE getFullName
        @LastName nvarchar(50),
        @FirstName nvarchar(50)
    AS
        RETURN @LastName + @FirstName;')");
```

<span data-ttu-id="2e7ab-147">這可以用來管理資料庫的任何層面，包括：</span><span class="sxs-lookup"><span data-stu-id="2e7ab-147">This can be used to manage any aspect of your database, including:</span></span>

* <span data-ttu-id="2e7ab-148">預存程序</span><span class="sxs-lookup"><span data-stu-id="2e7ab-148">Stored procedures</span></span>
* <span data-ttu-id="2e7ab-149">全文檢索搜尋</span><span class="sxs-lookup"><span data-stu-id="2e7ab-149">Full-Text Search</span></span>
* <span data-ttu-id="2e7ab-150">函式</span><span class="sxs-lookup"><span data-stu-id="2e7ab-150">Functions</span></span>
* <span data-ttu-id="2e7ab-151">觸發程序</span><span class="sxs-lookup"><span data-stu-id="2e7ab-151">Triggers</span></span>
* <span data-ttu-id="2e7ab-152">檢視</span><span class="sxs-lookup"><span data-stu-id="2e7ab-152">Views</span></span>

<span data-ttu-id="2e7ab-153">在大多數情況下，EF Core 會在套用遷移時，自動將每個遷移功能包裝在自己的交易中。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-153">In most cases, EF Core will automatically wrap each migration in its own transaction when applying migrations.</span></span> <span data-ttu-id="2e7ab-154">可惜的是，有些遷移作業無法在某些資料庫的交易中執行;在這些情況下，您可以藉由傳遞 `suppressTransaction: true` 到來退出交易 `migrationBuilder.Sql` 。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-154">Unfortunately, some migrations operations cannot be performed within a transaction in some databases; for these cases, you may opt out of the transaction by passing `suppressTransaction: true` to `migrationBuilder.Sql`.</span></span>

<span data-ttu-id="2e7ab-155">如果 `DbContext` 與啟始專案位於不同的組件中，您可以在[套件管理員主控台工具](xref:core/miscellaneous/cli/powershell#target-and-startup-project)或 [.NET Core CLI 工具](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project)中明確指定目標和啟始專案。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-155">If the `DbContext` is in a different assembly than the startup project, you can explicitly specify the target and startup projects in either the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell#target-and-startup-project) or the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).</span></span>

## <a name="remove-a-migration"></a><span data-ttu-id="2e7ab-156">移除移轉</span><span class="sxs-lookup"><span data-stu-id="2e7ab-156">Remove a migration</span></span>

<span data-ttu-id="2e7ab-157">在您新增移轉時，有時候會發現您必須在套用 EF Core 模型之前對其進行其他變更。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-157">Sometimes you add a migration and realize you need to make additional changes to your EF Core model before applying it.</span></span> <span data-ttu-id="2e7ab-158">若要移除上一個移轉，請使用此命令。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-158">To remove the last migration, use this command.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="2e7ab-159">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2e7ab-159">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations remove
```

### <a name="visual-studio"></a>[<span data-ttu-id="2e7ab-160">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e7ab-160">Visual Studio</span></span>](#tab/vs)

```powershell
Remove-Migration
```

***

<span data-ttu-id="2e7ab-161">移除移轉後，您可以進行其他模型變更並再次予以新增。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-161">After removing the migration, you can make the additional model changes and add it again.</span></span>

> [!WARNING]
> <span data-ttu-id="2e7ab-162">請小心不要移除任何已套用至生產資料庫的遷移。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-162">Take care not to remove any migrations which are already applied to production databases.</span></span> <span data-ttu-id="2e7ab-163">若未這麼做，將無法讓您還原它，而且可能會中斷後續遷移所做的假設。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-163">Not doing so will prevent you from being able to revert it, and may break the assumptions made by subsequent migrations.</span></span>

## <a name="listing-migrations"></a><span data-ttu-id="2e7ab-164">列出遷移</span><span class="sxs-lookup"><span data-stu-id="2e7ab-164">Listing migrations</span></span>

<span data-ttu-id="2e7ab-165">您可以列出所有現有的遷移，如下所示：</span><span class="sxs-lookup"><span data-stu-id="2e7ab-165">You can list all existing migrations as follows:</span></span>

```dotnetcli
dotnet ef migrations list
```

## <a name="resetting-all-migrations"></a><span data-ttu-id="2e7ab-166">正在重設所有遷移</span><span class="sxs-lookup"><span data-stu-id="2e7ab-166">Resetting all migrations</span></span>

<span data-ttu-id="2e7ab-167">在某些極端的情況下，可能需要移除所有的遷移並重新開始。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-167">In some extreme cases, it may be necessary to remove all migrations and start over.</span></span> <span data-ttu-id="2e7ab-168">這可以藉由刪除您的 [ **遷移** ] 資料夾並卸載您的資料庫來輕鬆完成;屆時，您可以建立新的初始遷移，其中將包含您目前的整個架構。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-168">This can be easily done by deleting your **Migrations** folder and dropping your database; at that point you can create a new initial migration, which will contain you entire current schema.</span></span>

<span data-ttu-id="2e7ab-169">您也可以重設所有遷移，並建立單一的遷移，而不會遺失您的資料。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-169">It's also possible to reset all migrations and create a single one without losing your data.</span></span> <span data-ttu-id="2e7ab-170">這有時稱為「抓」，並牽涉到一些手動工作：</span><span class="sxs-lookup"><span data-stu-id="2e7ab-170">This is sometimes called "squashing", and involves some manual work:</span></span>

* <span data-ttu-id="2e7ab-171">刪除您的 **遷移** 資料夾</span><span class="sxs-lookup"><span data-stu-id="2e7ab-171">Delete your **Migrations** folder</span></span>
* <span data-ttu-id="2e7ab-172">建立新的遷移，並為其產生 SQL 腳本</span><span class="sxs-lookup"><span data-stu-id="2e7ab-172">Create a new migration and generate a SQL script for it</span></span>
* <span data-ttu-id="2e7ab-173">在您的資料庫中，刪除「遷移記錄」資料表中的所有資料列</span><span class="sxs-lookup"><span data-stu-id="2e7ab-173">In your database, delete all rows from the migrations history table</span></span>
* <span data-ttu-id="2e7ab-174">將單一資料列插入至「遷移」記錄，以記錄已套用第一個遷移，因為您的資料表已經存在。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-174">Insert a single row into the migrations history, to record that the first migration has already been applied, since your tables are already there.</span></span> <span data-ttu-id="2e7ab-175">Insert SQL 是上面所產生之 SQL 腳本中的最後一個作業。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-175">The insert SQL is the last operation in the SQL script generated above.</span></span>

> [!WARNING]
> <span data-ttu-id="2e7ab-176">刪除**遷移**資料夾時，任何[自訂的遷移程式碼](#customize-migration-code)都將遺失。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-176">Any [custom migration code](#customize-migration-code) will be lost when the **Migrations** folder is deleted.</span></span>  <span data-ttu-id="2e7ab-177">您必須手動將任何自訂套用至新的初始遷移，才能予以保留。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-177">Any customizations must be applied to the new initial migration manually in order to be preserved.</span></span>
