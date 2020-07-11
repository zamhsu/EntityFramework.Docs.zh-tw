---
title: 套用遷移-EF Core
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/applying
ms.openlocfilehash: f83a014651fdf2262a603fb91da7984672690197
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238747"
---
# <a name="applying-migrations"></a><span data-ttu-id="f06e0-102">套用遷移</span><span class="sxs-lookup"><span data-stu-id="f06e0-102">Applying Migrations</span></span>

<span data-ttu-id="f06e0-103">新增您的遷移之後，必須將其部署並套用至您的資料庫。</span><span class="sxs-lookup"><span data-stu-id="f06e0-103">Once your migrations have been added, they need to be deployed and applied to your databases.</span></span> <span data-ttu-id="f06e0-104">執行此作業的策略有很多種，有些則更適合用於生產環境，而其他則適用于開發週期。</span><span class="sxs-lookup"><span data-stu-id="f06e0-104">There are various strategies for doing this, with some being more appropriate for production environments, and others for the development lifecycle.</span></span>

> [!NOTE]
> <span data-ttu-id="f06e0-105">無論您的部署策略為何，在套用至實際執行資料庫之前，一律會檢查產生的遷移並加以測試。</span><span class="sxs-lookup"><span data-stu-id="f06e0-105">Whatever your deployment strategy, always inspect the generated migrations and test them before applying to a production database.</span></span> <span data-ttu-id="f06e0-106">當目的是要將資料行重新命名時，遷移可能會卸載它，或在套用至資料庫時，可能會因為各種原因而失敗。</span><span class="sxs-lookup"><span data-stu-id="f06e0-106">A migration may drop a column when the intent was to rename it, or may fail for various reasons when applied to a database.</span></span>

## <a name="sql-scripts"></a><span data-ttu-id="f06e0-107">SQL 腳本</span><span class="sxs-lookup"><span data-stu-id="f06e0-107">SQL scripts</span></span>

<span data-ttu-id="f06e0-108">將遷移部署到實際執行資料庫的建議方式是產生 SQL 腳本。</span><span class="sxs-lookup"><span data-stu-id="f06e0-108">The recommended way to deploy migrations to a production database is by generating SQL scripts.</span></span> <span data-ttu-id="f06e0-109">此策略的優點包括下列各項：</span><span class="sxs-lookup"><span data-stu-id="f06e0-109">The advantages of this strategy include the following:</span></span>

* <span data-ttu-id="f06e0-110">可以檢查 SQL 腳本的正確性;這很重要，因為將架構變更套用至生產環境資料庫是可能會造成資料遺失的潛在危險作業。</span><span class="sxs-lookup"><span data-stu-id="f06e0-110">SQL scripts can be reviewed for accuracy; this is important since applying schema changes to production databases is a potentially dangerous operation that could involve data loss.</span></span>
* <span data-ttu-id="f06e0-111">在某些情況下，可以微調腳本以符合生產資料庫的特定需求。</span><span class="sxs-lookup"><span data-stu-id="f06e0-111">In some cases, the scripts can be tuned to fit the specific needs of a production database.</span></span>
* <span data-ttu-id="f06e0-112">SQL 腳本可與部署技術搭配使用，甚至可以在 CI 程式中產生。</span><span class="sxs-lookup"><span data-stu-id="f06e0-112">SQL scripts can be used in conjunction with a deployment technology, and can even be generated as part of your CI process.</span></span>
* <span data-ttu-id="f06e0-113">SQL 腳本可以提供給 DBA，而且可以分開管理和封存。</span><span class="sxs-lookup"><span data-stu-id="f06e0-113">SQL scripts can be provided to a DBA, and can be managed and archived separately.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="f06e0-114">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f06e0-114">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

#### <a name="basic-usage"></a><span data-ttu-id="f06e0-115">基本使用方式</span><span class="sxs-lookup"><span data-stu-id="f06e0-115">Basic Usage</span></span>

<span data-ttu-id="f06e0-116">下列會從空白資料庫產生 SQL 腳本至最新的遷移：</span><span class="sxs-lookup"><span data-stu-id="f06e0-116">The following generates a SQL script from a blank database to the latest migration:</span></span>

```dotnetcli
dotnet ef migrations script
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="f06e0-117">使用 From (隱含)</span><span class="sxs-lookup"><span data-stu-id="f06e0-117">With From (to implied)</span></span>

<span data-ttu-id="f06e0-118">下列會產生從給定的遷移到最新的遷移的 SQL 腳本。</span><span class="sxs-lookup"><span data-stu-id="f06e0-118">The following generates a SQL script from the given migration to the latest migration.</span></span>

```dotnetcli
dotnet ef migrations script AddNewTables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="f06e0-119">使用 From 與 To</span><span class="sxs-lookup"><span data-stu-id="f06e0-119">With From and To</span></span>

<span data-ttu-id="f06e0-120">下列會從指定的遷移產生 SQL 腳本 `from` 到指定的 `to` 遷移。</span><span class="sxs-lookup"><span data-stu-id="f06e0-120">The following generates a SQL script from the specified `from` migration to the specified `to` migration.</span></span>

```dotnetcli
dotnet ef migrations script AddNewTables AddAuditTable
```

<span data-ttu-id="f06e0-121">您可以使用比 `to` 新的 `from` 來產生復原指令碼。</span><span class="sxs-lookup"><span data-stu-id="f06e0-121">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span>

> [!WARNING]
> <span data-ttu-id="f06e0-122">請注意，可能會發生資料遺失的狀況。</span><span class="sxs-lookup"><span data-stu-id="f06e0-122">Please take note of potential data loss scenarios.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="f06e0-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f06e0-123">Visual Studio</span></span>](#tab/vs)

#### <a name="basic-usage"></a><span data-ttu-id="f06e0-124">基本使用方式</span><span class="sxs-lookup"><span data-stu-id="f06e0-124">Basic Usage</span></span>

<span data-ttu-id="f06e0-125">下列會從空白資料庫產生 SQL 腳本至最新的遷移：</span><span class="sxs-lookup"><span data-stu-id="f06e0-125">The following generates a SQL script from a blank database to the latest migration:</span></span>

``` powershell
Script-Migration
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="f06e0-126">使用 From (隱含)</span><span class="sxs-lookup"><span data-stu-id="f06e0-126">With From (to implied)</span></span>

<span data-ttu-id="f06e0-127">下列會產生從給定的遷移到最新的遷移的 SQL 腳本。</span><span class="sxs-lookup"><span data-stu-id="f06e0-127">The following generates a SQL script from the given migration to the latest migration.</span></span>

```powershell
Script-Migration AddNewTables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="f06e0-128">使用 From 與 To</span><span class="sxs-lookup"><span data-stu-id="f06e0-128">With From and To</span></span>

<span data-ttu-id="f06e0-129">下列會從指定的遷移產生 SQL 腳本 `from` 到指定的 `to` 遷移。</span><span class="sxs-lookup"><span data-stu-id="f06e0-129">The following generates a SQL script from the specified `from` migration to the specified `to` migration.</span></span>

```powershell
Script-Migration AddNewTables AddAuditTable
```
<span data-ttu-id="f06e0-130">您可以使用比 `to` 新的 `from` 來產生復原指令碼。</span><span class="sxs-lookup"><span data-stu-id="f06e0-130">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span> <span data-ttu-id="f06e0-131">*請注意，可能會發生資料遺失的狀況。*</span><span class="sxs-lookup"><span data-stu-id="f06e0-131">*Please take note of potential data loss scenarios.*</span></span>

***

<span data-ttu-id="f06e0-132">腳本產生會接受下列兩個引數，指出應該產生的遷移範圍：</span><span class="sxs-lookup"><span data-stu-id="f06e0-132">Script generation accepts the following two arguments in indicate which range of migrations should be generated:</span></span>

* <span data-ttu-id="f06e0-133">執行指令碼之前，**from** 移轉應該是套用到資料庫的最後一個移轉。</span><span class="sxs-lookup"><span data-stu-id="f06e0-133">The **from** migration should be the last migration applied to the database before running the script.</span></span> <span data-ttu-id="f06e0-134">若未套用任何移轉，請指定 `0` (此為預設)。</span><span class="sxs-lookup"><span data-stu-id="f06e0-134">If no migrations have been applied, specify `0` (this is the default).</span></span>
* <span data-ttu-id="f06e0-135">執行指令碼之後，**to** 移轉是套用到資料庫的最後一個移轉。</span><span class="sxs-lookup"><span data-stu-id="f06e0-135">The **to** migration is the last migration that will be applied to the database after running the script.</span></span> <span data-ttu-id="f06e0-136">預設為您專案中的最後一個移轉。</span><span class="sxs-lookup"><span data-stu-id="f06e0-136">This defaults to the last migration in your project.</span></span>

## <a name="idempotent-sql-scripts"></a><span data-ttu-id="f06e0-137">等冪 SQL 腳本</span><span class="sxs-lookup"><span data-stu-id="f06e0-137">Idempotent SQL scripts</span></span>

<span data-ttu-id="f06e0-138">上述產生的 SQL 腳本僅適用于將您的架構從一個遷移變更為另一個。您必須負責適當地套用腳本，而僅限於正確遷移狀態下的資料庫。</span><span class="sxs-lookup"><span data-stu-id="f06e0-138">The SQL scripts generated above can only be applied to change your schema from one migration to another; it is your responsibility to apply the script appropriately, and only to database in the correct migration state.</span></span> <span data-ttu-id="f06e0-139">EF Core 也支援產生**等冪**腳本，這會在內部檢查哪些遷移已透過「遷移記錄」資料表)  (套用，而且只會套用遺漏的。</span><span class="sxs-lookup"><span data-stu-id="f06e0-139">EF Core also supports generating **idempotent** scripts, which internally check which migrations have already been applied (via the migrations history table), and only apply missing ones.</span></span> <span data-ttu-id="f06e0-140">如果您不完全知道套用到資料庫的最後一個遷移是什麼，或者您要部署到多個資料庫，而每一個都是在不同的遷移上，這就很有用。</span><span class="sxs-lookup"><span data-stu-id="f06e0-140">This is useful if you don't exactly know what the last migration applied to the database was, or if you are deploying to multiple databases that may each be at a different migration.</span></span>

<span data-ttu-id="f06e0-141">以下會產生等冪遷移：</span><span class="sxs-lookup"><span data-stu-id="f06e0-141">The following generates idempotent migrations:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="f06e0-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f06e0-142">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations script --idempotent
```

#### <a name="visual-studio"></a>[<span data-ttu-id="f06e0-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f06e0-143">Visual Studio</span></span>](#tab/vs)

``` powershell
Script-Migration -Idempotent
```

***

## <a name="command-line-tools"></a><span data-ttu-id="f06e0-144">命令列工具</span><span class="sxs-lookup"><span data-stu-id="f06e0-144">Command-line tools</span></span>

<span data-ttu-id="f06e0-145">EF 命令列工具可以用來將遷移套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="f06e0-145">The EF command-line tools can be used to apply migrations to a database.</span></span> <span data-ttu-id="f06e0-146">雖然在本機開發和測試遷移的生產力，但這種方法並不適合用來管理生產資料庫：</span><span class="sxs-lookup"><span data-stu-id="f06e0-146">While productive for local development and testing of migrations, this approach isn't ideal for managing production databases:</span></span>

* <span data-ttu-id="f06e0-147">此工具會直接套用 SQL 命令，而不會提供開發人員變更來檢查或修改它們。</span><span class="sxs-lookup"><span data-stu-id="f06e0-147">The SQL commands are applied directly by the tool, without giving the developer a change to inspect or modify them.</span></span> <span data-ttu-id="f06e0-148">這在生產環境中可能會有危險。</span><span class="sxs-lookup"><span data-stu-id="f06e0-148">This can be dangerous in a production environment.</span></span>
* <span data-ttu-id="f06e0-149">.NET SDK 和 EF 工具必須安裝在實際執行伺服器上。</span><span class="sxs-lookup"><span data-stu-id="f06e0-149">The .NET SDK and the EF tool must be installed on production servers.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="f06e0-150">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f06e0-150">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

<span data-ttu-id="f06e0-151">以下會將您的資料庫更新為最新的遷移：</span><span class="sxs-lookup"><span data-stu-id="f06e0-151">The following updates your database to the latest migration:</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="f06e0-152">以下會將您的資料庫更新為指定的遷移：</span><span class="sxs-lookup"><span data-stu-id="f06e0-152">The following updates your database to a given migration:</span></span>

```dotnetcli
dotnet ef database update AddNewTables
```

<span data-ttu-id="f06e0-153">請注意，這也可以用來回複到先前的遷移。</span><span class="sxs-lookup"><span data-stu-id="f06e0-153">Note that this can be used to roll back to an earlier migration as well.</span></span>

> [!WARNING]
> <span data-ttu-id="f06e0-154">請注意，可能會發生資料遺失的狀況。</span><span class="sxs-lookup"><span data-stu-id="f06e0-154">Please take note of potential data loss scenarios.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="f06e0-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f06e0-155">Visual Studio</span></span>](#tab/vs)

<span data-ttu-id="f06e0-156">以下會將您的資料庫更新為最新的遷移：</span><span class="sxs-lookup"><span data-stu-id="f06e0-156">The following updates your database to the latest migration:</span></span>

``` powershell
Update-Database
```

<span data-ttu-id="f06e0-157">以下會將您的資料庫更新為指定的遷移：</span><span class="sxs-lookup"><span data-stu-id="f06e0-157">The following updates your database to a given migration:</span></span>

``` powershell
Update-Database AddNewTables
```

<span data-ttu-id="f06e0-158">請注意，這也可以用來回複到先前的遷移。</span><span class="sxs-lookup"><span data-stu-id="f06e0-158">Note that this can be used to roll back to an earlier migration as well.</span></span>

> [!WARNING]
> <span data-ttu-id="f06e0-159">請注意，可能會發生資料遺失的狀況。</span><span class="sxs-lookup"><span data-stu-id="f06e0-159">Please take note of potential data loss scenarios.</span></span>

***

<span data-ttu-id="f06e0-160">如需透過命令列工具套用遷移的詳細資訊，請參閱[EF Core 工具參考](xref:core/miscellaneous/cli/index)。</span><span class="sxs-lookup"><span data-stu-id="f06e0-160">For more information on applying migrations via the command-line tools, see the [EF Core tools reference](xref:core/miscellaneous/cli/index).</span></span>

## <a name="apply-migrations-at-runtime"></a><span data-ttu-id="f06e0-161">在執行階段套用移轉</span><span class="sxs-lookup"><span data-stu-id="f06e0-161">Apply migrations at runtime</span></span>

<span data-ttu-id="f06e0-162">應用程式本身有可能以程式設計方式套用遷移，通常是在啟動期間執行。</span><span class="sxs-lookup"><span data-stu-id="f06e0-162">It's possible for the application itself to apply migrations programmatically, typically during startup.</span></span> <span data-ttu-id="f06e0-163">雖然在本機開發和測試遷移方面有生產力，但這種方法不適合用于管理生產資料庫，原因如下：</span><span class="sxs-lookup"><span data-stu-id="f06e0-163">While productive for local development and testing of migrations, this approach is inappropriate for managing production databases, for the following reasons:</span></span>

* <span data-ttu-id="f06e0-164">如果您的應用程式有多個實例正在執行，這兩個應用程式可能會嘗試同時套用遷移，而且 (或更糟的情況下，會造成資料損毀) 。</span><span class="sxs-lookup"><span data-stu-id="f06e0-164">If multiple instances of your application are running, both applications could attempt to apply the migration concurrently and fail (or worse, cause data corruption).</span></span>
* <span data-ttu-id="f06e0-165">同樣地，如果應用程式在另一個應用程式進行遷移時存取資料庫，這可能會造成嚴重的問題。</span><span class="sxs-lookup"><span data-stu-id="f06e0-165">Similarly, if an application is accessing the database while another application migrates it, this can cause severe issues.</span></span>
* <span data-ttu-id="f06e0-166">應用程式必須具有更高的存取權，才能修改資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="f06e0-166">The application must have elevated access to modify the database schema.</span></span> <span data-ttu-id="f06e0-167">在生產環境中限制應用程式的資料庫許可權通常是很好的作法。</span><span class="sxs-lookup"><span data-stu-id="f06e0-167">It's generally good practice to limit the application's database permissions in production.</span></span>
* <span data-ttu-id="f06e0-168">請務必能夠在發生問題時復原已套用的遷移。</span><span class="sxs-lookup"><span data-stu-id="f06e0-168">It's important to be able to roll back an applied migration in case of an issue.</span></span> <span data-ttu-id="f06e0-169">其他策略則提供這種簡單且現成的。</span><span class="sxs-lookup"><span data-stu-id="f06e0-169">The other strategies provide this easily and out of the box.</span></span>
* <span data-ttu-id="f06e0-170">SQL 命令是直接由程式套用，而不會讓開發人員進行變更來檢查或修改它們。</span><span class="sxs-lookup"><span data-stu-id="f06e0-170">The SQL commands are applied directly by the program, without giving the developer a change to inspect or modify them.</span></span> <span data-ttu-id="f06e0-171">這在生產環境中可能會有危險。</span><span class="sxs-lookup"><span data-stu-id="f06e0-171">This can be dangerous in a production environment.</span></span>

<span data-ttu-id="f06e0-172">若要以程式設計方式套用遷移，請呼叫 `context.Database.Migrate()` 。</span><span class="sxs-lookup"><span data-stu-id="f06e0-172">To apply migrations programmatically, call `context.Database.Migrate()`.</span></span> <span data-ttu-id="f06e0-173">例如，典型的 ASP.NET 應用程式可以執行下列動作：</span><span class="sxs-lookup"><span data-stu-id="f06e0-173">For example, a typical ASP.NET application can do the following:</span></span>

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

<span data-ttu-id="f06e0-174">請注意， `Migrate()` 建置於服務的最上層 `IMigrator` ，可用於更先進的案例。</span><span class="sxs-lookup"><span data-stu-id="f06e0-174">Note that `Migrate()` builds on top of the `IMigrator` service, which can be used for more advanced scenarios.</span></span> <span data-ttu-id="f06e0-175">您可以使用 `myDbContext.GetInfrastructure().GetService<IMigrator>()` 來加以存取。</span><span class="sxs-lookup"><span data-stu-id="f06e0-175">Use `myDbContext.GetInfrastructure().GetService<IMigrator>()` to access it.</span></span>

> [!WARNING]
>
> * <span data-ttu-id="f06e0-176">在生產環境中使用此方法之前，請先仔細考慮。</span><span class="sxs-lookup"><span data-stu-id="f06e0-176">Carefully consider before using this approach in production.</span></span> <span data-ttu-id="f06e0-177">經驗顯示，此部署策略的簡單性是由它所建立的問題所遠遠超過。</span><span class="sxs-lookup"><span data-stu-id="f06e0-177">Experience has shown that the simplicity of this deployment strategy is outweighed by the issues it creates.</span></span> <span data-ttu-id="f06e0-178">請考慮改為使用 SQL 腳本。</span><span class="sxs-lookup"><span data-stu-id="f06e0-178">Consider using SQL scripts instead.</span></span>
> * <span data-ttu-id="f06e0-179">請勿在 `Migrate()` 之前呼叫 `EnsureCreated()`。</span><span class="sxs-lookup"><span data-stu-id="f06e0-179">Don't call `EnsureCreated()` before `Migrate()`.</span></span> <span data-ttu-id="f06e0-180">`EnsureCreated()` 會略過移轉而建立結構描述，因此造成 `Migrate()` 失敗。</span><span class="sxs-lookup"><span data-stu-id="f06e0-180">`EnsureCreated()` bypasses Migrations to create the schema, which causes `Migrate()` to fail.</span></span>
