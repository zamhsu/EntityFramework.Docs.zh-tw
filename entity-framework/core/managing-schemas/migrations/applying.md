---
title: 套用遷移-EF Core
description: 使用 Entity Framework Core 將架構遷移套用至生產和開發資料庫的策略
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/applying
ms.openlocfilehash: cde83a944e1e698a7f8a00c4692c0ce08a87b5ab
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2020
ms.locfileid: "91210402"
---
# <a name="applying-migrations"></a><span data-ttu-id="06338-103">套用遷移</span><span class="sxs-lookup"><span data-stu-id="06338-103">Applying Migrations</span></span>

<span data-ttu-id="06338-104">新增您的遷移之後，您必須將其部署並套用至您的資料庫。</span><span class="sxs-lookup"><span data-stu-id="06338-104">Once your migrations have been added, they need to be deployed and applied to your databases.</span></span> <span data-ttu-id="06338-105">有各種不同的策略可執行此操作，而某些策略更適合生產環境，其他則適用于開發週期。</span><span class="sxs-lookup"><span data-stu-id="06338-105">There are various strategies for doing this, with some being more appropriate for production environments, and others for the development lifecycle.</span></span>

> [!NOTE]
> <span data-ttu-id="06338-106">無論您的部署策略為何，一律先檢查產生的遷移，並測試它們，再套用至生產環境資料庫。</span><span class="sxs-lookup"><span data-stu-id="06338-106">Whatever your deployment strategy, always inspect the generated migrations and test them before applying to a production database.</span></span> <span data-ttu-id="06338-107">當意圖要重新命名時，遷移可能會卸載資料行，或可能會因為套用至資料庫的各種原因而失敗。</span><span class="sxs-lookup"><span data-stu-id="06338-107">A migration may drop a column when the intent was to rename it, or may fail for various reasons when applied to a database.</span></span>

## <a name="sql-scripts"></a><span data-ttu-id="06338-108">SQL 腳本</span><span class="sxs-lookup"><span data-stu-id="06338-108">SQL scripts</span></span>

<span data-ttu-id="06338-109">部署到生產環境資料庫的建議方式是產生 SQL 腳本。</span><span class="sxs-lookup"><span data-stu-id="06338-109">The recommended way to deploy migrations to a production database is by generating SQL scripts.</span></span> <span data-ttu-id="06338-110">這項策略的優點包括下列各項：</span><span class="sxs-lookup"><span data-stu-id="06338-110">The advantages of this strategy include the following:</span></span>

* <span data-ttu-id="06338-111">您可以檢查 SQL 腳本的精確度;這點很重要，因為將架構變更套用到生產資料庫是可能會造成資料遺失的潛在危險作業。</span><span class="sxs-lookup"><span data-stu-id="06338-111">SQL scripts can be reviewed for accuracy; this is important since applying schema changes to production databases is a potentially dangerous operation that could involve data loss.</span></span>
* <span data-ttu-id="06338-112">在某些情況下，可以調整腳本以符合生產資料庫的特定需求。</span><span class="sxs-lookup"><span data-stu-id="06338-112">In some cases, the scripts can be tuned to fit the specific needs of a production database.</span></span>
* <span data-ttu-id="06338-113">SQL 腳本可以與部署技術搭配使用，甚至可以在您的 CI 程式中產生。</span><span class="sxs-lookup"><span data-stu-id="06338-113">SQL scripts can be used in conjunction with a deployment technology, and can even be generated as part of your CI process.</span></span>
* <span data-ttu-id="06338-114">您可以將 SQL 腳本提供給 DBA，並且可以個別管理和封存。</span><span class="sxs-lookup"><span data-stu-id="06338-114">SQL scripts can be provided to a DBA, and can be managed and archived separately.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="06338-115">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="06338-115">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

#### <a name="basic-usage"></a><span data-ttu-id="06338-116">基本使用方式</span><span class="sxs-lookup"><span data-stu-id="06338-116">Basic Usage</span></span>

<span data-ttu-id="06338-117">下列會從空白資料庫產生 SQL 腳本至最新的遷移：</span><span class="sxs-lookup"><span data-stu-id="06338-117">The following generates a SQL script from a blank database to the latest migration:</span></span>

```dotnetcli
dotnet ef migrations script
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="06338-118">使用 From (隱含)</span><span class="sxs-lookup"><span data-stu-id="06338-118">With From (to implied)</span></span>

<span data-ttu-id="06338-119">下列會從指定的遷移產生 SQL 腳本至最新的遷移。</span><span class="sxs-lookup"><span data-stu-id="06338-119">The following generates a SQL script from the given migration to the latest migration.</span></span>

```dotnetcli
dotnet ef migrations script AddNewTables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="06338-120">使用 From 與 To</span><span class="sxs-lookup"><span data-stu-id="06338-120">With From and To</span></span>

<span data-ttu-id="06338-121">下列會從指定的遷移產生 SQL 腳本 `from` 至指定的 `to` 遷移。</span><span class="sxs-lookup"><span data-stu-id="06338-121">The following generates a SQL script from the specified `from` migration to the specified `to` migration.</span></span>

```dotnetcli
dotnet ef migrations script AddNewTables AddAuditTable
```

<span data-ttu-id="06338-122">您可以使用比 `to` 新的 `from` 來產生復原指令碼。</span><span class="sxs-lookup"><span data-stu-id="06338-122">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span>

> [!WARNING]
> <span data-ttu-id="06338-123">請注意，可能會發生資料遺失的狀況。</span><span class="sxs-lookup"><span data-stu-id="06338-123">Please take note of potential data loss scenarios.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="06338-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="06338-124">Visual Studio</span></span>](#tab/vs)

#### <a name="basic-usage"></a><span data-ttu-id="06338-125">基本使用方式</span><span class="sxs-lookup"><span data-stu-id="06338-125">Basic Usage</span></span>

<span data-ttu-id="06338-126">下列會從空白資料庫產生 SQL 腳本至最新的遷移：</span><span class="sxs-lookup"><span data-stu-id="06338-126">The following generates a SQL script from a blank database to the latest migration:</span></span>

``` powershell
Script-Migration
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="06338-127">使用 From (隱含)</span><span class="sxs-lookup"><span data-stu-id="06338-127">With From (to implied)</span></span>

<span data-ttu-id="06338-128">下列會從指定的遷移產生 SQL 腳本至最新的遷移。</span><span class="sxs-lookup"><span data-stu-id="06338-128">The following generates a SQL script from the given migration to the latest migration.</span></span>

```powershell
Script-Migration AddNewTables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="06338-129">使用 From 與 To</span><span class="sxs-lookup"><span data-stu-id="06338-129">With From and To</span></span>

<span data-ttu-id="06338-130">下列會從指定的遷移產生 SQL 腳本 `from` 至指定的 `to` 遷移。</span><span class="sxs-lookup"><span data-stu-id="06338-130">The following generates a SQL script from the specified `from` migration to the specified `to` migration.</span></span>

```powershell
Script-Migration AddNewTables AddAuditTable
```
<span data-ttu-id="06338-131">您可以使用比 `to` 新的 `from` 來產生復原指令碼。</span><span class="sxs-lookup"><span data-stu-id="06338-131">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span> <span data-ttu-id="06338-132">*請注意，可能會發生資料遺失的狀況。*</span><span class="sxs-lookup"><span data-stu-id="06338-132">*Please take note of potential data loss scenarios.*</span></span>

***

<span data-ttu-id="06338-133">腳本產生會接受下列兩個引數，指出應產生的遷移範圍：</span><span class="sxs-lookup"><span data-stu-id="06338-133">Script generation accepts the following two arguments in indicate which range of migrations should be generated:</span></span>

* <span data-ttu-id="06338-134">執行指令碼之前，**from** 移轉應該是套用到資料庫的最後一個移轉。</span><span class="sxs-lookup"><span data-stu-id="06338-134">The **from** migration should be the last migration applied to the database before running the script.</span></span> <span data-ttu-id="06338-135">若未套用任何移轉，請指定 `0` (此為預設)。</span><span class="sxs-lookup"><span data-stu-id="06338-135">If no migrations have been applied, specify `0` (this is the default).</span></span>
* <span data-ttu-id="06338-136">執行指令碼之後，**to** 移轉是套用到資料庫的最後一個移轉。</span><span class="sxs-lookup"><span data-stu-id="06338-136">The **to** migration is the last migration that will be applied to the database after running the script.</span></span> <span data-ttu-id="06338-137">預設為您專案中的最後一個移轉。</span><span class="sxs-lookup"><span data-stu-id="06338-137">This defaults to the last migration in your project.</span></span>

## <a name="idempotent-sql-scripts"></a><span data-ttu-id="06338-138">等冪 SQL 腳本</span><span class="sxs-lookup"><span data-stu-id="06338-138">Idempotent SQL scripts</span></span>

<span data-ttu-id="06338-139">上述產生的 SQL 腳本只能套用至將架構從一個遷移變更為另一個：您必須負責適當地套用腳本，而且只能在資料庫中正確的遷移狀態。</span><span class="sxs-lookup"><span data-stu-id="06338-139">The SQL scripts generated above can only be applied to change your schema from one migration to another; it is your responsibility to apply the script appropriately, and only to database in the correct migration state.</span></span> <span data-ttu-id="06338-140">EF Core 也支援產生 **等冪** 腳本，其會在內部檢查哪些遷移已透過) 的「遷移歷程記錄」資料表 (套用，而且只會套用遺漏的遷移。</span><span class="sxs-lookup"><span data-stu-id="06338-140">EF Core also supports generating **idempotent** scripts, which internally check which migrations have already been applied (via the migrations history table), and only apply missing ones.</span></span> <span data-ttu-id="06338-141">如果您不完全知道套用至資料庫的最後一次遷移，或您要部署到多個資料庫，且每個資料庫都有不同的遷移方式，這會很有用。</span><span class="sxs-lookup"><span data-stu-id="06338-141">This is useful if you don't exactly know what the last migration applied to the database was, or if you are deploying to multiple databases that may each be at a different migration.</span></span>

<span data-ttu-id="06338-142">下列會產生等冪的遷移：</span><span class="sxs-lookup"><span data-stu-id="06338-142">The following generates idempotent migrations:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="06338-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="06338-143">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations script --idempotent
```

#### <a name="visual-studio"></a>[<span data-ttu-id="06338-144">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="06338-144">Visual Studio</span></span>](#tab/vs)

``` powershell
Script-Migration -Idempotent
```

***

## <a name="command-line-tools"></a><span data-ttu-id="06338-145">命令列工具</span><span class="sxs-lookup"><span data-stu-id="06338-145">Command-line tools</span></span>

<span data-ttu-id="06338-146">EF 命令列工具可以用來將遷移套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="06338-146">The EF command-line tools can be used to apply migrations to a database.</span></span> <span data-ttu-id="06338-147">雖然能在本機開發和測試遷移時保持生產力，但這種方法並不適合用來管理生產資料庫：</span><span class="sxs-lookup"><span data-stu-id="06338-147">While productive for local development and testing of migrations, this approach isn't ideal for managing production databases:</span></span>

* <span data-ttu-id="06338-148">此工具會直接套用 SQL 命令，而不會讓開發人員有機會檢查或修改它們。</span><span class="sxs-lookup"><span data-stu-id="06338-148">The SQL commands are applied directly by the tool, without giving the developer a chance to inspect or modify them.</span></span> <span data-ttu-id="06338-149">這在生產環境中可能會有危險。</span><span class="sxs-lookup"><span data-stu-id="06338-149">This can be dangerous in a production environment.</span></span>
* <span data-ttu-id="06338-150">實際執行伺服器上必須安裝 .NET SDK 和 EF 工具。</span><span class="sxs-lookup"><span data-stu-id="06338-150">The .NET SDK and the EF tool must be installed on production servers.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="06338-151">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="06338-151">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

<span data-ttu-id="06338-152">下列程式會將您的資料庫更新至最新的遷移：</span><span class="sxs-lookup"><span data-stu-id="06338-152">The following updates your database to the latest migration:</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="06338-153">下列程式會將您的資料庫更新為指定的遷移：</span><span class="sxs-lookup"><span data-stu-id="06338-153">The following updates your database to a given migration:</span></span>

```dotnetcli
dotnet ef database update AddNewTables
```

<span data-ttu-id="06338-154">請注意，這也可以用來復原至先前的遷移。</span><span class="sxs-lookup"><span data-stu-id="06338-154">Note that this can be used to roll back to an earlier migration as well.</span></span>

> [!WARNING]
> <span data-ttu-id="06338-155">請注意，可能會發生資料遺失的狀況。</span><span class="sxs-lookup"><span data-stu-id="06338-155">Please take note of potential data loss scenarios.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="06338-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="06338-156">Visual Studio</span></span>](#tab/vs)

<span data-ttu-id="06338-157">下列程式會將您的資料庫更新至最新的遷移：</span><span class="sxs-lookup"><span data-stu-id="06338-157">The following updates your database to the latest migration:</span></span>

``` powershell
Update-Database
```

<span data-ttu-id="06338-158">下列程式會將您的資料庫更新為指定的遷移：</span><span class="sxs-lookup"><span data-stu-id="06338-158">The following updates your database to a given migration:</span></span>

``` powershell
Update-Database AddNewTables
```

<span data-ttu-id="06338-159">請注意，這也可以用來復原至先前的遷移。</span><span class="sxs-lookup"><span data-stu-id="06338-159">Note that this can be used to roll back to an earlier migration as well.</span></span>

> [!WARNING]
> <span data-ttu-id="06338-160">請注意，可能會發生資料遺失的狀況。</span><span class="sxs-lookup"><span data-stu-id="06338-160">Please take note of potential data loss scenarios.</span></span>

***

<span data-ttu-id="06338-161">如需透過命令列工具套用遷移的詳細資訊，請參閱 [EF Core 工具參考](xref:core/miscellaneous/cli/index)。</span><span class="sxs-lookup"><span data-stu-id="06338-161">For more information on applying migrations via the command-line tools, see the [EF Core tools reference](xref:core/miscellaneous/cli/index).</span></span>

## <a name="apply-migrations-at-runtime"></a><span data-ttu-id="06338-162">在執行階段套用移轉</span><span class="sxs-lookup"><span data-stu-id="06338-162">Apply migrations at runtime</span></span>

<span data-ttu-id="06338-163">應用程式本身可能會以程式設計方式套用遷移，通常是在啟動期間進行。</span><span class="sxs-lookup"><span data-stu-id="06338-163">It's possible for the application itself to apply migrations programmatically, typically during startup.</span></span> <span data-ttu-id="06338-164">雖然在本機開發和測試遷移方面都有生產力，但這種方法並不適合用來管理生產資料庫，原因如下：</span><span class="sxs-lookup"><span data-stu-id="06338-164">While productive for local development and testing of migrations, this approach is inappropriate for managing production databases, for the following reasons:</span></span>

* <span data-ttu-id="06338-165">如果您的應用程式有多個實例正在執行，這兩個應用程式都可以嘗試同時套用遷移，而 (或更糟的情況下，會造成資料損毀) 。</span><span class="sxs-lookup"><span data-stu-id="06338-165">If multiple instances of your application are running, both applications could attempt to apply the migration concurrently and fail (or worse, cause data corruption).</span></span>
* <span data-ttu-id="06338-166">同樣地，如果應用程式在另一個應用程式遷移時存取資料庫，這可能會造成嚴重的問題。</span><span class="sxs-lookup"><span data-stu-id="06338-166">Similarly, if an application is accessing the database while another application migrates it, this can cause severe issues.</span></span>
* <span data-ttu-id="06338-167">應用程式必須具有較高的存取權，才能修改資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="06338-167">The application must have elevated access to modify the database schema.</span></span> <span data-ttu-id="06338-168">一般來說，限制應用程式在生產環境中的資料庫許可權是很好的作法。</span><span class="sxs-lookup"><span data-stu-id="06338-168">It's generally good practice to limit the application's database permissions in production.</span></span>
* <span data-ttu-id="06338-169">在發生問題時，必須能夠復原已套用的遷移。</span><span class="sxs-lookup"><span data-stu-id="06338-169">It's important to be able to roll back an applied migration in case of an issue.</span></span> <span data-ttu-id="06338-170">其他策略則可輕鬆且現成地提供。</span><span class="sxs-lookup"><span data-stu-id="06338-170">The other strategies provide this easily and out of the box.</span></span>
* <span data-ttu-id="06338-171">SQL 命令會直接套用到程式，而不會讓開發人員有機會檢查或修改它們。</span><span class="sxs-lookup"><span data-stu-id="06338-171">The SQL commands are applied directly by the program, without giving the developer a chance to inspect or modify them.</span></span> <span data-ttu-id="06338-172">這在生產環境中可能會有危險。</span><span class="sxs-lookup"><span data-stu-id="06338-172">This can be dangerous in a production environment.</span></span>

<span data-ttu-id="06338-173">若要以程式設計方式套用遷移，請呼叫 `context.Database.Migrate()` 。</span><span class="sxs-lookup"><span data-stu-id="06338-173">To apply migrations programmatically, call `context.Database.Migrate()`.</span></span> <span data-ttu-id="06338-174">例如，一般的 ASP.NET 應用程式可以執行下列作業：</span><span class="sxs-lookup"><span data-stu-id="06338-174">For example, a typical ASP.NET application can do the following:</span></span>

```c#
public static void Main(string[] args)
{
    var host = CreateHostBuilder(args).Build();

    using (var scope = host.Services.CreateScope())
    {
        var db = scope.ServiceProvider.GetRequiredService<ApplicationDbContext>();
        db.Database.Migrate();
    }

    host.Run();
}
```

<span data-ttu-id="06338-175">請注意， `Migrate()` 建置於服務之上 `IMigrator` ，可用於更先進的案例。</span><span class="sxs-lookup"><span data-stu-id="06338-175">Note that `Migrate()` builds on top of the `IMigrator` service, which can be used for more advanced scenarios.</span></span> <span data-ttu-id="06338-176">您可以使用 `myDbContext.GetInfrastructure().GetService<IMigrator>()` 來加以存取。</span><span class="sxs-lookup"><span data-stu-id="06338-176">Use `myDbContext.GetInfrastructure().GetService<IMigrator>()` to access it.</span></span>

> [!WARNING]
>
> * <span data-ttu-id="06338-177">在生產環境中使用這種方法之前，請先仔細考慮。</span><span class="sxs-lookup"><span data-stu-id="06338-177">Carefully consider before using this approach in production.</span></span> <span data-ttu-id="06338-178">經驗證明，這項部署策略的簡潔性是由它所建立的問題所遠遠超過。</span><span class="sxs-lookup"><span data-stu-id="06338-178">Experience has shown that the simplicity of this deployment strategy is outweighed by the issues it creates.</span></span> <span data-ttu-id="06338-179">請考慮改為從遷移產生 SQL 腳本。</span><span class="sxs-lookup"><span data-stu-id="06338-179">Consider generating SQL scripts from migrations instead.</span></span>
> * <span data-ttu-id="06338-180">請勿在 `Migrate()` 之前呼叫 `EnsureCreated()`。</span><span class="sxs-lookup"><span data-stu-id="06338-180">Don't call `EnsureCreated()` before `Migrate()`.</span></span> <span data-ttu-id="06338-181">`EnsureCreated()` 會略過移轉而建立結構描述，因此造成 `Migrate()` 失敗。</span><span class="sxs-lookup"><span data-stu-id="06338-181">`EnsureCreated()` bypasses Migrations to create the schema, which causes `Migrate()` to fail.</span></span>
