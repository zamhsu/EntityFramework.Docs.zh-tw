---
title: 使用多個提供者進行遷移-EF Core
description: 使用 Entity Framework Core 以多個資料庫提供者為目標時，使用遷移來管理資料庫架構
author: bricelam
ms.date: 11/08/2017
uid: core/managing-schemas/migrations/providers
ms.openlocfilehash: f44abb5156ea3a175c68c1a0ec23ff41a9d13452
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92061979"
---
# <a name="migrations-with-multiple-providers"></a><span data-ttu-id="d5706-103">使用多個提供者進行遷移</span><span class="sxs-lookup"><span data-stu-id="d5706-103">Migrations with Multiple Providers</span></span>

<span data-ttu-id="d5706-104">[EF Core 的工具][1]只會 scaffold 作用中提供者的遷移。</span><span class="sxs-lookup"><span data-stu-id="d5706-104">The [EF Core Tools][1] only scaffold migrations for the active provider.</span></span> <span data-ttu-id="d5706-105">不過，有時候您可能會想要使用一個以上的提供者 (例如 Microsoft SQL Server 和 SQLite) DbCoNtext）。</span><span class="sxs-lookup"><span data-stu-id="d5706-105">Sometimes, however, you may want to use more than one provider (for example Microsoft SQL Server and SQLite) with your DbContext.</span></span> <span data-ttu-id="d5706-106">有兩種方式可以使用遷移來處理此情況。</span><span class="sxs-lookup"><span data-stu-id="d5706-106">There are two ways to handle this with Migrations.</span></span> <span data-ttu-id="d5706-107">您可以維護兩組遷移（每個提供者各一個），或將它們合併成可同時在兩者上運作的單一集合。</span><span class="sxs-lookup"><span data-stu-id="d5706-107">You can maintain two sets of migrations--one for each provider--or merge them into a single set that can work on both.</span></span>

## <a name="two-migration-sets"></a><span data-ttu-id="d5706-108">兩個遷移集</span><span class="sxs-lookup"><span data-stu-id="d5706-108">Two migration sets</span></span>

<span data-ttu-id="d5706-109">在第一種方法中，您會針對每個模型變更產生兩個遷移。</span><span class="sxs-lookup"><span data-stu-id="d5706-109">In the first approach, you generate two migrations for each model change.</span></span>

<span data-ttu-id="d5706-110">其中一個方法是將每個遷移集放 [在不同的元件中][2] ，並手動切換使用中的提供者 (以及在新增兩個遷移之間) 遷移元件。</span><span class="sxs-lookup"><span data-stu-id="d5706-110">One way to do this is to put each migration set [in a separate assembly][2] and manually switch the active provider (and migrations assembly) between adding the two migrations.</span></span>

<span data-ttu-id="d5706-111">另一種可讓您更輕鬆使用工具的方法，就是建立衍生自 DbCoNtext 的新型別，並覆寫作用中的提供者。</span><span class="sxs-lookup"><span data-stu-id="d5706-111">Another approach that makes working with the tools easier is to create a new type that derives from your DbContext and overrides the active provider.</span></span> <span data-ttu-id="d5706-112">這種類型是在設計階段于新增或套用遷移時使用。</span><span class="sxs-lookup"><span data-stu-id="d5706-112">This type is used at design time when adding or applying migrations.</span></span>

```csharp
class MySqliteDbContext : MyDbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

> [!NOTE]
> <span data-ttu-id="d5706-113">因為每個遷移集都使用自己的 DbCoNtext 類型，所以此方法不需要使用個別的遷移元件。</span><span class="sxs-lookup"><span data-stu-id="d5706-113">Since each migration set uses its own DbContext types, this approach doesn't require using a separate migrations assembly.</span></span>

<span data-ttu-id="d5706-114">加入新的遷移時，請指定內容類型。</span><span class="sxs-lookup"><span data-stu-id="d5706-114">When adding new migration, specify the context types.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="d5706-115">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d5706-115">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --context MyDbContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context MySqliteDbContext --output-dir Migrations/SqliteMigrations
```

### <a name="visual-studio"></a>[<span data-ttu-id="d5706-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5706-116">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration InitialCreate -Context MyDbContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context MySqliteDbContext -OutputDir Migrations\SqliteMigrations
```

***

> [!TIP]
> <span data-ttu-id="d5706-117">您不需要指定輸出目錄來進行後續的遷移，因為它們會建立為最後一個的同級。</span><span class="sxs-lookup"><span data-stu-id="d5706-117">You don't need to specify the output directory for subsequent migrations since they are created as siblings to the last one.</span></span>

## <a name="one-migration-set"></a><span data-ttu-id="d5706-118">一個遷移集</span><span class="sxs-lookup"><span data-stu-id="d5706-118">One migration set</span></span>

<span data-ttu-id="d5706-119">如果您不喜歡有兩組的遷移，您可以手動將它們合併成可同時套用至兩個提供者的單一集合。</span><span class="sxs-lookup"><span data-stu-id="d5706-119">If you don't like having two sets of migrations, you can manually combine them into a single set that can be applied to both providers.</span></span>

<span data-ttu-id="d5706-120">批註可以並存，因為提供者會忽略它不了解的任何批註。</span><span class="sxs-lookup"><span data-stu-id="d5706-120">Annotations can coexist since a provider ignores any annotations that it doesn't understand.</span></span> <span data-ttu-id="d5706-121">例如，同時使用 Microsoft SQL Server 和 SQLite 的主要索引鍵資料行看起來可能會像這樣。</span><span class="sxs-lookup"><span data-stu-id="d5706-121">For example, a primary key column that works with both Microsoft SQL Server and SQLite might look like this.</span></span>

```csharp
Id = table.Column<int>(nullable: false)
    .Annotation("SqlServer:ValueGenerationStrategy",
        SqlServerValueGenerationStrategy.IdentityColumn)
    .Annotation("Sqlite:Autoincrement", true),
```

<span data-ttu-id="d5706-122">如果只能對一個提供者套用作業，或在提供者之間有不同的作業，請使用 `ActiveProvider` 屬性來判斷哪個提供者為作用中：</span><span class="sxs-lookup"><span data-stu-id="d5706-122">If operations can be applied only for one provider, or they're different between providers, use the `ActiveProvider` property to determine which provider is active:</span></span>

```csharp
if (migrationBuilder.ActiveProvider == "Microsoft.EntityFrameworkCore.SqlServer")
{
    migrationBuilder.CreateSequence(
        name: "EntityFrameworkHiLoSequence");
}
```

  [1]: xref:core/miscellaneous/cli/index
  [2]: xref:core/managing-schemas/migrations/projects
