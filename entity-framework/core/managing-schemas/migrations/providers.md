---
title: 使用多個提供者進行遷移-EF Core
description: 使用 Entity Framework Core 以多個資料庫提供者為目標時，使用遷移來管理資料庫架構
author: bricelam
ms.date: 10/29/2020
uid: core/managing-schemas/migrations/providers
ms.openlocfilehash: fb8c6121a4baccf573e57b52ebeb3fcd29fe2cba
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429776"
---
# <a name="migrations-with-multiple-providers"></a><span data-ttu-id="d316b-103">使用多個提供者進行遷移</span><span class="sxs-lookup"><span data-stu-id="d316b-103">Migrations with Multiple Providers</span></span>

<span data-ttu-id="d316b-104">[EF Core 的工具](xref:core/cli/index)只會 scaffold 作用中提供者的遷移。</span><span class="sxs-lookup"><span data-stu-id="d316b-104">The [EF Core Tools](xref:core/cli/index) only scaffold migrations for the active provider.</span></span> <span data-ttu-id="d316b-105">不過，有時候您可能會想要使用一個以上的提供者 (例如 Microsoft SQL Server 和 SQLite) DbCoNtext）。</span><span class="sxs-lookup"><span data-stu-id="d316b-105">Sometimes, however, you may want to use more than one provider (for example Microsoft SQL Server and SQLite) with your DbContext.</span></span> <span data-ttu-id="d316b-106">藉由維護多個遷移集（每個提供者各一個），並為每個模型變更新增每個的遷移，來處理此情況。</span><span class="sxs-lookup"><span data-stu-id="d316b-106">Handle this by maintaining multiple sets of migrations--one for each provider--and adding a migration to each for every model change.</span></span>

## <a name="using-multiple-context-types"></a><span data-ttu-id="d316b-107">使用多個內容類型</span><span class="sxs-lookup"><span data-stu-id="d316b-107">Using multiple context types</span></span>

<span data-ttu-id="d316b-108">建立多個遷移集的其中一種方式是為每個提供者使用一個 DbCoNtext 類型。</span><span class="sxs-lookup"><span data-stu-id="d316b-108">One way to create multiple migration sets is to use one DbContext type per provider.</span></span>

```csharp
class SqliteBlogContext : BlogContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

<span data-ttu-id="d316b-109">新增新的遷移時，請指定內容類型。</span><span class="sxs-lookup"><span data-stu-id="d316b-109">Specify the context type when adding new migrations.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="d316b-110">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d316b-110">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --context BlogContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context SqliteBlogContext --output-dir Migrations/SqliteMigrations
```

### <a name="visual-studio"></a>[<span data-ttu-id="d316b-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d316b-111">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration InitialCreate -Context BlogContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context SqliteBlogContext -OutputDir Migrations\SqliteMigrations
```

***

> [!TIP]
> <span data-ttu-id="d316b-112">您不需要指定輸出目錄來進行後續的遷移，因為它們會建立為最後一個的同級。</span><span class="sxs-lookup"><span data-stu-id="d316b-112">You don't need to specify the output directory for subsequent migrations since they are created as siblings to the last one.</span></span>

## <a name="using-one-context-type"></a><span data-ttu-id="d316b-113">使用一個內容類型</span><span class="sxs-lookup"><span data-stu-id="d316b-113">Using one context type</span></span>

<span data-ttu-id="d316b-114">也可以使用一個 DbCoNtext 類型。</span><span class="sxs-lookup"><span data-stu-id="d316b-114">It's also possible to use one DbContext type.</span></span> <span data-ttu-id="d316b-115">這目前需要將遷移移至另一個元件。</span><span class="sxs-lookup"><span data-stu-id="d316b-115">This currently requires moving the migrations into a separate assembly.</span></span> <span data-ttu-id="d316b-116">如需設定專案的指示，請參閱 [使用個別的遷移專案](xref:core/managing-schemas/migrations/projects) 。</span><span class="sxs-lookup"><span data-stu-id="d316b-116">Please refer to [Using a Separate Migrations Project](xref:core/managing-schemas/migrations/projects) for instructions on setting up your projects.</span></span>

> [!TIP]
> <span data-ttu-id="d316b-117">您可以檢視本文中的 [GitHut 範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Schemas/TwoProjectMigrations)。</span><span class="sxs-lookup"><span data-stu-id="d316b-117">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Schemas/TwoProjectMigrations).</span></span>

<span data-ttu-id="d316b-118">從 EF Core 5.0 開始，您可以從工具將引數傳遞至應用程式。</span><span class="sxs-lookup"><span data-stu-id="d316b-118">Starting in EF Core 5.0, you can pass arguments into the app from the tools.</span></span> <span data-ttu-id="d316b-119">這可以讓更簡化的工作流程，避免在執行工具時手動變更專案。</span><span class="sxs-lookup"><span data-stu-id="d316b-119">This can enable a more streamlined workflow that avoids having to make manual changes to the project when running the tools.</span></span>

<span data-ttu-id="d316b-120">以下是使用 [泛型主機](/dotnet/core/extensions/generic-host)時運作正常的一個模式。</span><span class="sxs-lookup"><span data-stu-id="d316b-120">Here's one pattern that works well when using a [Generic Host](/dotnet/core/extensions/generic-host).</span></span>

[!code-csharp[](../../../../samples/core/Schemas/TwoProjectMigrations/WorkerService1/Program.cs#snippet_CreateHostBuilder)]

<span data-ttu-id="d316b-121">由於預設主機產生器會從命令列引數讀取設定，因此您可以在執行工具時指定提供者。</span><span class="sxs-lookup"><span data-stu-id="d316b-121">Since the default host builder reads configuration from command-line arguments, you can specify the provider when running the tools.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="d316b-122">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d316b-122">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add MyMigration --project ../SqlServerMigrations -- --provider SqlServer
dotnet ef migrations add MyMigration --project ../SqliteMigrations -- --provider Sqlite
```

> [!TIP]
> <span data-ttu-id="d316b-123">`--`標記會指示 `dotnet ef` 將後面的所有專案視為引數，而不會嘗試將它們剖析為選項。</span><span class="sxs-lookup"><span data-stu-id="d316b-123">The `--` token directs `dotnet ef` to treat everything that follows as an argument and not try to parse them as options.</span></span> <span data-ttu-id="d316b-124">未使用的任何額外引數 `dotnet ef` 都會轉送至應用程式。</span><span class="sxs-lookup"><span data-stu-id="d316b-124">Any extra arguments not used by `dotnet ef` are forwarded to the app.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="d316b-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d316b-125">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration MyMigration -Args "--provider SqlServer"
Add-Migration MyMigration -Args "--provider Sqlite"
```

***

> [!NOTE]
> <span data-ttu-id="d316b-126">在 EF Core 5.0 中新增了為應用程式指定額外引數的功能。</span><span class="sxs-lookup"><span data-stu-id="d316b-126">The ability to specify additional arguments for the app was added in EF Core 5.0.</span></span> <span data-ttu-id="d316b-127">如果您使用的是較舊的版本，請改為使用環境變數來指定設定值。</span><span class="sxs-lookup"><span data-stu-id="d316b-127">If you're using an older version, specify configuration values with environment variables instead.</span></span>
