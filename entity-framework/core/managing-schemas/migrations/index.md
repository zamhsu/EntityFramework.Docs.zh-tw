---
title: 遷移總覽-EF Core
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: 8539a8da6f0051d3737efc583f0adfaf05fb2d3d
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238225"
---
# <a name="migrations-overview"></a><span data-ttu-id="ba9fb-102">遷移總覽</span><span class="sxs-lookup"><span data-stu-id="ba9fb-102">Migrations Overview</span></span>

<span data-ttu-id="ba9fb-103">在實際的專案中，資料模型會隨著功能的執行而變更：新增和移除新的實體或屬性，而且必須據此變更資料庫架構，使其與應用程式保持同步。</span><span class="sxs-lookup"><span data-stu-id="ba9fb-103">In real world projects, data models change as features get implemented: new entities or properties are added and removed, and database schemas needs to be changed accordingly to be kept in sync with the application.</span></span> <span data-ttu-id="ba9fb-104">EF Core 中的移轉功能讓您能夠以累加方式來更新資料庫結構描述，讓它與應用程式的資料模型保持同步，同時將現有的資料保留在資料庫中。</span><span class="sxs-lookup"><span data-stu-id="ba9fb-104">The migrations feature in EF Core provides a way to incrementally update the database schema to keep it in sync with the application's data model while preserving existing data in the database.</span></span>

<span data-ttu-id="ba9fb-105">概括而言，遷移的運作方式如下：</span><span class="sxs-lookup"><span data-stu-id="ba9fb-105">At a high level, migrations function in the following way:</span></span>

* <span data-ttu-id="ba9fb-106">引進資料模型變更時，開發人員會使用 EF Core 工具來加入對應的遷移，以描述保持資料庫架構同步所需的更新。EF Core 會比較目前的模型與舊模型的快照集，以判斷差異，並產生遷移來源檔案;您可以在專案的原始檔控制中追蹤檔案，就像任何其他原始程式檔一樣。</span><span class="sxs-lookup"><span data-stu-id="ba9fb-106">When a data model change is introduced, the developer uses EF Core tools to add a corresponding migration describing the updates necessary to keep the database schema in sync. EF Core compares the current model against a snapshot of the old model to determine the differences, and generates migration source files; the files can be tracked in your project's source control like any other source file.</span></span>
* <span data-ttu-id="ba9fb-107">一旦產生新的遷移之後，就可以用各種方式將它套用到資料庫。</span><span class="sxs-lookup"><span data-stu-id="ba9fb-107">Once a new migration has been generated, it can be applied to a database in various ways.</span></span> <span data-ttu-id="ba9fb-108">EF Core 會記錄特殊歷程記錄資料表中所有套用的遷移，讓 it 知道已套用但尚未套用的遷移。</span><span class="sxs-lookup"><span data-stu-id="ba9fb-108">EF Core records all applied migrations in a special history table, allowing it to know which migrations have been applied and which haven't.</span></span>

<span data-ttu-id="ba9fb-109">本頁面的其餘部分是使用遷移的逐步入門指南。</span><span class="sxs-lookup"><span data-stu-id="ba9fb-109">The rest of this page is a step-by-step beginner's guide for using migrations.</span></span> <span data-ttu-id="ba9fb-110">如需更深入的資訊，請參閱本節中的其他頁面。</span><span class="sxs-lookup"><span data-stu-id="ba9fb-110">Consult the other pages in this section for more in-depth information.</span></span>

## <a name="getting-started"></a><span data-ttu-id="ba9fb-111">開始使用</span><span class="sxs-lookup"><span data-stu-id="ba9fb-111">Getting started</span></span>

<span data-ttu-id="ba9fb-112">假設您剛完成第一個 EF Core 應用程式，其中包含下列簡單的模型：</span><span class="sxs-lookup"><span data-stu-id="ba9fb-112">Let's assume you've just completed your first EF Core application, which contains the following simple model:</span></span>

```c#
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```

<span data-ttu-id="ba9fb-113">在開發期間，您可能已使用[建立和卸載 api](xref:core/managing-schemas/ensure-created)來快速反復執行，並視需要變更您的模型;但現在您的應用程式即將進入生產階段，您需要一種方法來安全地演進架構，而不需卸載整個資料庫。</span><span class="sxs-lookup"><span data-stu-id="ba9fb-113">During development, you may have used the [Create and Drop APIs](xref:core/managing-schemas/ensure-created) to iterate quickly, changing your model as needed; but now that your application is going to production, you need a way to safely evolve the schema without dropping the entire database.</span></span>

### <a name="install-the-tools"></a><span data-ttu-id="ba9fb-114">安裝工具</span><span class="sxs-lookup"><span data-stu-id="ba9fb-114">Install the tools</span></span>

<span data-ttu-id="ba9fb-115">首先，您必須安裝[EF Core 命令列工具](xref:core/miscellaneous/cli/index)：</span><span class="sxs-lookup"><span data-stu-id="ba9fb-115">First, you'll have to install the [EF Core command-line tools](xref:core/miscellaneous/cli/index):</span></span>

* <span data-ttu-id="ba9fb-116">我們通常建議使用可在所有平臺上運作的[.NET Core CLI 工具](xref:core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="ba9fb-116">We generally recommend using the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet), which work on all platforms.</span></span>
* <span data-ttu-id="ba9fb-117">如果您更熟悉 Visual Studio 或擁有 EF6 遷移的經驗，您也可以使用[套件管理員主控台工具](xref:core/miscellaneous/cli/powershell)。</span><span class="sxs-lookup"><span data-stu-id="ba9fb-117">If you're more comfortable working inside Visual Studio or have experience with EF6 migrations, you can also use the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell).</span></span>

### <a name="create-your-first-migration"></a><span data-ttu-id="ba9fb-118">建立您的第一次遷移</span><span class="sxs-lookup"><span data-stu-id="ba9fb-118">Create your first migration</span></span>

<span data-ttu-id="ba9fb-119">您現在已準備好加入您的第一次遷移！</span><span class="sxs-lookup"><span data-stu-id="ba9fb-119">You're now ready to add your first migration!</span></span> <span data-ttu-id="ba9fb-120">指示 EF Core 建立名為**InitialCreate**的遷移：</span><span class="sxs-lookup"><span data-stu-id="ba9fb-120">Instruct EF Core to create a migration named **InitialCreate**:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="ba9fb-121">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ba9fb-121">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate
```

#### <a name="visual-studio"></a>[<span data-ttu-id="ba9fb-122">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ba9fb-122">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration InitialCreate
```

***

<span data-ttu-id="ba9fb-123">EF Core 會在專案中建立名為「**遷移**」的目錄，並產生一些檔案。</span><span class="sxs-lookup"><span data-stu-id="ba9fb-123">EF Core will create a directory called **Migrations** in your project, and generate some files.</span></span> <span data-ttu-id="ba9fb-124">最好的做法是檢查產生的 EF Core，而且可能會修改它，但我們現在會跳過該程式。</span><span class="sxs-lookup"><span data-stu-id="ba9fb-124">It's a good idea to inspect what exactly EF Core generated - and possibly amend it - but we'll skip over that for now.</span></span>

### <a name="create-your-database-and-schema"></a><span data-ttu-id="ba9fb-125">建立您的資料庫和架構</span><span class="sxs-lookup"><span data-stu-id="ba9fb-125">Create your database and schema</span></span>

<span data-ttu-id="ba9fb-126">此時，您可以讓 EF 建立您的資料庫，並從遷移建立您的架構。</span><span class="sxs-lookup"><span data-stu-id="ba9fb-126">At this point you can have EF create your database and create your schema from the migration.</span></span> <span data-ttu-id="ba9fb-127">這可以透過下列方式來完成：</span><span class="sxs-lookup"><span data-stu-id="ba9fb-127">This can be done via the following:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="ba9fb-128">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ba9fb-128">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```
#### <a name="visual-studio"></a>[<span data-ttu-id="ba9fb-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ba9fb-129">Visual Studio</span></span>](#tab/vs)

``` powershell
Update-Database
```

***

<span data-ttu-id="ba9fb-130">就是這麼簡單-您的應用程式已準備好在新的資料庫上執行，而且您不需要撰寫任何一行的 SQL。</span><span class="sxs-lookup"><span data-stu-id="ba9fb-130">That's all there is to it - your application is ready to run on your new database, and you didn't need to write a single line of SQL.</span></span> <span data-ttu-id="ba9fb-131">請注意，這種套用遷移的方式適用于本機開發，但較不適合用于生產環境-如需詳細資訊，請參閱[應用程式遷移頁面](xref:core/managing-schemas/migrations/applying)。</span><span class="sxs-lookup"><span data-stu-id="ba9fb-131">Note that this way of applying migrations is ideal for local development, but is less suitable for production environments - see the [Applying Migrations page](xref:core/managing-schemas/migrations/applying) for more info.</span></span>

### <a name="evolving-your-model"></a><span data-ttu-id="ba9fb-132">發展您的模型</span><span class="sxs-lookup"><span data-stu-id="ba9fb-132">Evolving your model</span></span>

<span data-ttu-id="ba9fb-133">經過幾天後，系統會要求您將建立時間戳記新增至您的 blog。</span><span class="sxs-lookup"><span data-stu-id="ba9fb-133">A few days have passed, and you're asked to add a creation timestamp to your blogs.</span></span> <span data-ttu-id="ba9fb-134">您已完成對應用程式所做的必要變更，而您的模型現在看起來像這樣：</span><span class="sxs-lookup"><span data-stu-id="ba9fb-134">You've done the necessary changes to your application, and your model now looks like this:</span></span>

```c#
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTime CreatedTimestamp { get; set; }
}
```

<span data-ttu-id="ba9fb-135">您的模型和生產資料庫現在已不同步-我們必須將新的資料行加入至您的資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="ba9fb-135">Your model and your production database are now out of sync - we must add a new column to your database schema.</span></span> <span data-ttu-id="ba9fb-136">讓我們為此建立新的遷移：</span><span class="sxs-lookup"><span data-stu-id="ba9fb-136">Let's create a new migration for this:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="ba9fb-137">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ba9fb-137">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

#### <a name="visual-studio"></a>[<span data-ttu-id="ba9fb-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ba9fb-138">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration AddBlogCreatedTimestamp
```

***

<span data-ttu-id="ba9fb-139">請注意，我們會為遷移提供描述性的名稱，讓您稍後可以更輕鬆地瞭解專案歷程記錄。</span><span class="sxs-lookup"><span data-stu-id="ba9fb-139">Note that we give migrations a descriptive name, to make it easier to understand the project history later.</span></span>

<span data-ttu-id="ba9fb-140">由於這不是專案的第一次遷移，因此在加入資料行之前，EF Core 現在會比較已更新的模型與舊模型的快照集;模型快照集是當您加入遷移時 EF Core 所產生的其中一個檔案，而且會簽入原始檔控制中。</span><span class="sxs-lookup"><span data-stu-id="ba9fb-140">Since this isn't the project's first migration, EF Core now compares your updated model against a snapshot of the old model, before the column was added; the model snapshot is one of the files generated by EF Core when you add a migration, and is checked into source control.</span></span> <span data-ttu-id="ba9fb-141">根據該比較，EF Core 會偵測到已新增資料行，並新增適當的遷移。</span><span class="sxs-lookup"><span data-stu-id="ba9fb-141">Based on that comparison, EF Core detects that a column has been added, and adds the appropriate migration.</span></span>

<span data-ttu-id="ba9fb-142">您現在可以像之前一樣套用您的遷移：</span><span class="sxs-lookup"><span data-stu-id="ba9fb-142">You can now apply your migration as before:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="ba9fb-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ba9fb-143">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```
#### <a name="visual-studio"></a>[<span data-ttu-id="ba9fb-144">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ba9fb-144">Visual Studio</span></span>](#tab/vs)

``` powershell
Update-Database
```

***

<span data-ttu-id="ba9fb-145">請注意，這次 EF 會偵測到資料庫已經存在。</span><span class="sxs-lookup"><span data-stu-id="ba9fb-145">Note that this time, EF detects that the database already exists.</span></span> <span data-ttu-id="ba9fb-146">此外，當我們的第一次遷移已套用時，此事實會記錄在資料庫的特殊遷移歷程記錄資料表中;這可讓 EF 自動套用新的遷移。</span><span class="sxs-lookup"><span data-stu-id="ba9fb-146">In addition, when our first migration was applied above, this fact was recorded in a special migrations history table in your database; this allows EF to automatically apply only the new migration.</span></span>

### <a name="next-steps"></a><span data-ttu-id="ba9fb-147">後續步驟</span><span class="sxs-lookup"><span data-stu-id="ba9fb-147">Next steps</span></span>

<span data-ttu-id="ba9fb-148">以上只是簡單的遷移簡介。</span><span class="sxs-lookup"><span data-stu-id="ba9fb-148">The above was only a brief introduction to migrations.</span></span> <span data-ttu-id="ba9fb-149">請參閱其他檔頁面，以深入瞭解如何[管理遷移](xref:core/managing-schemas/migrations/managing)、套用[它們](xref:core/managing-schemas/migrations/applying)和其他層面。</span><span class="sxs-lookup"><span data-stu-id="ba9fb-149">Please consult the other documentation pages to learn more about [managing migrations](xref:core/managing-schemas/migrations/managing), [applying them](xref:core/managing-schemas/migrations/applying), and other aspects.</span></span> <span data-ttu-id="ba9fb-150">[.NET Core CLI 工具參考](xref:core/miscellaneous/cli/index)也包含不同命令的實用資訊</span><span class="sxs-lookup"><span data-stu-id="ba9fb-150">The [.NET Core CLI tool reference](xref:core/miscellaneous/cli/index) also contains useful information on the different commands</span></span>
