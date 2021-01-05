---
title: 移轉概觀 - EF Core
description: 透過 Entity Framework Core 使用移轉來管理資料庫結構描述的概觀
author: bricelam
ms.date: 10/28/2020
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: b9547298714af59453aeae6d05742a03c067708b
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2020
ms.locfileid: "95003389"
---
# <a name="migrations-overview"></a><span data-ttu-id="cd855-103">移轉概觀</span><span class="sxs-lookup"><span data-stu-id="cd855-103">Migrations Overview</span></span>

<span data-ttu-id="cd855-104">在實際專案中，資料模型會隨著功能的實作 (新增和移除新的實體或屬性) 而變更，而且必須據以變更資料庫結構描述，使其與應用程式保持同步。</span><span class="sxs-lookup"><span data-stu-id="cd855-104">In real world projects, data models change as features get implemented: new entities or properties are added and removed, and database schemas needs to be changed accordingly to be kept in sync with the application.</span></span> <span data-ttu-id="cd855-105">EF Core 中的移轉功能讓您能夠以累加方式來更新資料庫結構描述，讓它與應用程式的資料模型保持同步，同時將現有的資料保留在資料庫中。</span><span class="sxs-lookup"><span data-stu-id="cd855-105">The migrations feature in EF Core provides a way to incrementally update the database schema to keep it in sync with the application's data model while preserving existing data in the database.</span></span>

<span data-ttu-id="cd855-106">概括而言，移轉的運作方式如下：</span><span class="sxs-lookup"><span data-stu-id="cd855-106">At a high level, migrations function in the following way:</span></span>

* <span data-ttu-id="cd855-107">引進資料模型變更時，開發人員會使用 EF Core 工具來加入對應的移轉，描述保持資料庫結構描述同步所需的更新。EF Core 將目前的模型與舊模型的快照集進行比較以判斷差異，並產生移轉來源檔案；您可以在專案的原始程式碼控制中追蹤檔案，就像任何其他原始程式檔一樣。</span><span class="sxs-lookup"><span data-stu-id="cd855-107">When a data model change is introduced, the developer uses EF Core tools to add a corresponding migration describing the updates necessary to keep the database schema in sync. EF Core compares the current model against a snapshot of the old model to determine the differences, and generates migration source files; the files can be tracked in your project's source control like any other source file.</span></span>
* <span data-ttu-id="cd855-108">一旦產生新的移轉，就可以用各種方式將其套用到資料庫。</span><span class="sxs-lookup"><span data-stu-id="cd855-108">Once a new migration has been generated, it can be applied to a database in various ways.</span></span> <span data-ttu-id="cd855-109">EF Core 會在特殊歷程記錄資料表中記錄所有套用的移轉，因此可知道已套用與尚未套用的移轉。</span><span class="sxs-lookup"><span data-stu-id="cd855-109">EF Core records all applied migrations in a special history table, allowing it to know which migrations have been applied and which haven't.</span></span>

<span data-ttu-id="cd855-110">此頁面的其餘部分是使用移轉的逐步入門指南。</span><span class="sxs-lookup"><span data-stu-id="cd855-110">The rest of this page is a step-by-step beginner's guide for using migrations.</span></span> <span data-ttu-id="cd855-111">如需更深入的資訊，請參閱此節中的其他頁面。</span><span class="sxs-lookup"><span data-stu-id="cd855-111">Consult the other pages in this section for more in-depth information.</span></span>

## <a name="getting-started"></a><span data-ttu-id="cd855-112">開始使用</span><span class="sxs-lookup"><span data-stu-id="cd855-112">Getting started</span></span>

<span data-ttu-id="cd855-113">假設您剛完成第一個 EF Core 應用程式，其中包含下列簡單的模型：</span><span class="sxs-lookup"><span data-stu-id="cd855-113">Let's assume you've just completed your first EF Core application, which contains the following simple model:</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```

<span data-ttu-id="cd855-114">在開發期間，您可能已使用[建立及卸除 API](xref:core/managing-schemas/ensure-created) 來快速逐一查看，並視需要變更您的模型；但既然您的應用程式即將進入生產階段，您需要一種方法來安全地使您的結構描述進化，而不需卸載整個資料庫。</span><span class="sxs-lookup"><span data-stu-id="cd855-114">During development, you may have used the [Create and Drop APIs](xref:core/managing-schemas/ensure-created) to iterate quickly, changing your model as needed; but now that your application is going to production, you need a way to safely evolve the schema without dropping the entire database.</span></span>

### <a name="install-the-tools"></a><span data-ttu-id="cd855-115">安裝工具</span><span class="sxs-lookup"><span data-stu-id="cd855-115">Install the tools</span></span>

<span data-ttu-id="cd855-116">首先，您必須安裝 [EF Core 命令列工具](xref:core/cli/index)：</span><span class="sxs-lookup"><span data-stu-id="cd855-116">First, you'll have to install the [EF Core command-line tools](xref:core/cli/index):</span></span>

* <span data-ttu-id="cd855-117">我們通常建議使用 [.NET Core CLI 工具](xref:core/cli/dotnet)，適用於所有平台。</span><span class="sxs-lookup"><span data-stu-id="cd855-117">We generally recommend using the [.NET Core CLI tools](xref:core/cli/dotnet), which work on all platforms.</span></span>
* <span data-ttu-id="cd855-118">如果更熟悉 Visual Studio 或有 EF6 移轉的經驗，您也可以使用 [套件管理員主控台工具](xref:core/cli/powershell)。</span><span class="sxs-lookup"><span data-stu-id="cd855-118">If you're more comfortable working inside Visual Studio or have experience with EF6 migrations, you can also use the [Package Manager Console tools](xref:core/cli/powershell).</span></span>

### <a name="create-your-first-migration"></a><span data-ttu-id="cd855-119">建立第一個移轉</span><span class="sxs-lookup"><span data-stu-id="cd855-119">Create your first migration</span></span>

<span data-ttu-id="cd855-120">您現在已準備好加入第一個移轉！</span><span class="sxs-lookup"><span data-stu-id="cd855-120">You're now ready to add your first migration!</span></span> <span data-ttu-id="cd855-121">指示 EF Core 建立名為 **InitialCreate** 的移轉：</span><span class="sxs-lookup"><span data-stu-id="cd855-121">Instruct EF Core to create a migration named **InitialCreate**:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="cd855-122">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cd855-122">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate
```

#### <a name="visual-studio"></a>[<span data-ttu-id="cd855-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd855-123">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration InitialCreate
```

<span data-ttu-id="cd855-124">\*\*_</span><span class="sxs-lookup"><span data-stu-id="cd855-124">\*\*_</span></span>

<span data-ttu-id="cd855-125">EF Core 將會在您的專案中建立一個名為 _ [\*遷移\*\*] 的目錄，並產生一些檔案。</span><span class="sxs-lookup"><span data-stu-id="cd855-125">EF Core will create a directory called _ *Migrations*\* in your project, and generate some files.</span></span> <span data-ttu-id="cd855-126">最好的作法是檢查 EF Core 產生的確切項目，並視需要加以修改，但我們現在會跳過此程序。</span><span class="sxs-lookup"><span data-stu-id="cd855-126">It's a good idea to inspect what exactly EF Core generated - and possibly amend it - but we'll skip over that for now.</span></span>

### <a name="create-your-database-and-schema"></a><span data-ttu-id="cd855-127">建立您的資料庫與結構描述</span><span class="sxs-lookup"><span data-stu-id="cd855-127">Create your database and schema</span></span>

<span data-ttu-id="cd855-128">此時，您可以讓 EF 建立您的資料庫，並從移轉建立您的結構描述。</span><span class="sxs-lookup"><span data-stu-id="cd855-128">At this point you can have EF create your database and create your schema from the migration.</span></span> <span data-ttu-id="cd855-129">這可以透過下列項目來完成：</span><span class="sxs-lookup"><span data-stu-id="cd855-129">This can be done via the following:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="cd855-130">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cd855-130">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```

#### <a name="visual-studio"></a>[<span data-ttu-id="cd855-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd855-131">Visual Studio</span></span>](#tab/vs)

```powershell
Update-Database
```

<span data-ttu-id="cd855-132">\*\*_</span><span class="sxs-lookup"><span data-stu-id="cd855-132">\*\*_</span></span>

<span data-ttu-id="cd855-133">就是這麼簡單，您的應用程式已準備好在新的資料庫上執行，而且您不需要撰寫任何一行 SQL。</span><span class="sxs-lookup"><span data-stu-id="cd855-133">That's all there is to it - your application is ready to run on your new database, and you didn't need to write a single line of SQL.</span></span> <span data-ttu-id="cd855-134">請注意，這種套用移轉的方式適用於本機開發，但較不適合用於生產環境。如需詳細資訊，請參閱[套用移轉頁面](xref:core/managing-schemas/migrations/applying)。</span><span class="sxs-lookup"><span data-stu-id="cd855-134">Note that this way of applying migrations is ideal for local development, but is less suitable for production environments - see the [Applying Migrations page](xref:core/managing-schemas/migrations/applying) for more info.</span></span>

### <a name="evolving-your-model"></a><span data-ttu-id="cd855-135">發展您的模型</span><span class="sxs-lookup"><span data-stu-id="cd855-135">Evolving your model</span></span>

<span data-ttu-id="cd855-136">幾天過去了，而且有人要求您將建立時間戳記新增至您的部落格。</span><span class="sxs-lookup"><span data-stu-id="cd855-136">A few days have passed, and you're asked to add a creation timestamp to your blogs.</span></span> <span data-ttu-id="cd855-137">您已完成對應用程式進行的必要變更，而且您的模型現在看起來像這樣：</span><span class="sxs-lookup"><span data-stu-id="cd855-137">You've done the necessary changes to your application, and your model now looks like this:</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTime CreatedTimestamp { get; set; }
}
```

<span data-ttu-id="cd855-138">您的模型與生產資料庫現在已不同步，我們必須將新的資料行加入您的資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="cd855-138">Your model and your production database are now out of sync - we must add a new column to your database schema.</span></span> <span data-ttu-id="cd855-139">讓我們為此建立新的移轉：</span><span class="sxs-lookup"><span data-stu-id="cd855-139">Let's create a new migration for this:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="cd855-140">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cd855-140">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

#### <a name="visual-studio"></a>[<span data-ttu-id="cd855-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd855-141">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration AddBlogCreatedTimestamp
```

_*_

<span data-ttu-id="cd855-142">請注意，我們會為移轉提供描述性名稱，讓您稍後可以更輕鬆地了解專案歷程記錄。</span><span class="sxs-lookup"><span data-stu-id="cd855-142">Note that we give migrations a descriptive name, to make it easier to understand the project history later.</span></span>

<span data-ttu-id="cd855-143">由於這不是專案的第一次移轉，因此在加入資料行之前，EF Core 現在會將已更新的模型與舊模型的快照集進行比較；模型快照集是當您加入移轉時 EF Core 所產生的其中一個檔案，而且會簽入原始程式碼控制中。</span><span class="sxs-lookup"><span data-stu-id="cd855-143">Since this isn't the project's first migration, EF Core now compares your updated model against a snapshot of the old model, before the column was added; the model snapshot is one of the files generated by EF Core when you add a migration, and is checked into source control.</span></span> <span data-ttu-id="cd855-144">根據該比較，EF Core 會偵測到已新增資料行，並新增適當的移轉。</span><span class="sxs-lookup"><span data-stu-id="cd855-144">Based on that comparison, EF Core detects that a column has been added, and adds the appropriate migration.</span></span>

<span data-ttu-id="cd855-145">您現在可以像之前一樣套用移轉：</span><span class="sxs-lookup"><span data-stu-id="cd855-145">You can now apply your migration as before:</span></span>

<!--markdownlint-disable MD024-->

#### <a name="net-core-cli"></a>[<span data-ttu-id="cd855-146">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cd855-146">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```

#### <a name="visual-studio"></a>[<span data-ttu-id="cd855-147">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd855-147">Visual Studio</span></span>](#tab/vs)

```powershell
Update-Database
```

<!--markdownlint-enable MD024-->

_*_

<span data-ttu-id="cd855-148">請注意，這次 EF 會偵測到資料庫已經存在。</span><span class="sxs-lookup"><span data-stu-id="cd855-148">Note that this time, EF detects that the database already exists.</span></span> <span data-ttu-id="cd855-149">此外，第一次套用上面的移轉時，此事實會記錄在資料庫的特殊移轉歷程記錄資料表中；這可讓 EF 僅自動套用新的移轉。</span><span class="sxs-lookup"><span data-stu-id="cd855-149">In addition, when our first migration was applied above, this fact was recorded in a special migrations history table in your database; this allows EF to automatically apply only the new migration.</span></span>

### <a name="excluding-parts-of-your-model"></a><span data-ttu-id="cd855-150">排除模型的各部分</span><span class="sxs-lookup"><span data-stu-id="cd855-150">Excluding parts of your model</span></span>

> [!NOTE]
> <span data-ttu-id="cd855-151">這項功能在核心5.0 中引進了 EF。</span><span class="sxs-lookup"><span data-stu-id="cd855-151">This feature was introduced EF in Core 5.0.</span></span>

<span data-ttu-id="cd855-152">有時您可能會想要從另一個 DbCoNtext 參考類型。</span><span class="sxs-lookup"><span data-stu-id="cd855-152">Sometimes you may want to reference types from another DbContext.</span></span> <span data-ttu-id="cd855-153">這可能會導致遷移衝突。</span><span class="sxs-lookup"><span data-stu-id="cd855-153">This can lead to migration conflicts.</span></span> <span data-ttu-id="cd855-154">若要避免這種情況，請從 DbcoNtext 的其中一項的遷移中排除類型。</span><span class="sxs-lookup"><span data-stu-id="cd855-154">To prevent this, exclude the type from the migrations of one of the DbContexts.</span></span>

[!code-csharp[](../../../../samples/core/Modeling/FluentAPI/TableExcludeFromMigrations.cs#TableExcludeFromMigrations)]

### <a name="next-steps"></a><span data-ttu-id="cd855-155">後續步驟</span><span class="sxs-lookup"><span data-stu-id="cd855-155">Next steps</span></span>

<span data-ttu-id="cd855-156">以上只是簡單的移轉簡介。</span><span class="sxs-lookup"><span data-stu-id="cd855-156">The above was only a brief introduction to migrations.</span></span> <span data-ttu-id="cd855-157">請參閱其他文件頁面，以深入了解[管理移轉](xref:core/managing-schemas/migrations/managing)、 [套用移轉](xref:core/managing-schemas/migrations/applying)與其他層面。</span><span class="sxs-lookup"><span data-stu-id="cd855-157">Please consult the other documentation pages to learn more about [managing migrations](xref:core/managing-schemas/migrations/managing), [applying them](xref:core/managing-schemas/migrations/applying), and other aspects.</span></span> <span data-ttu-id="cd855-158">[.NET Core CLI 工具參考](xref:core/cli/index)也包含不同命令的實用資訊</span><span class="sxs-lookup"><span data-stu-id="cd855-158">The [.NET Core CLI tool reference](xref:core/cli/index) also contains useful information on the different commands</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cd855-159">其他資源</span><span class="sxs-lookup"><span data-stu-id="cd855-159">Additional resources</span></span>

<span data-ttu-id="cd855-160">_ EF Core 在 EF Core 5.0 中的新遷移功能的 [站立會議課程](https://www.youtube.com/watch?v=mSsGERmrhnE&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=20) 。</span><span class="sxs-lookup"><span data-stu-id="cd855-160">_ [EF Core Community Standup session](https://www.youtube.com/watch?v=mSsGERmrhnE&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=20) going over new migration features in EF Core 5.0.</span></span>
