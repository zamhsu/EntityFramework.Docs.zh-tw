---
title: "移轉具有多個提供者的 EF 核心"
author: bricelam
ms.author: bricelam
ms.date: 11/8/2017
ms.technology: entity-framework-core
ms.openlocfilehash: d950e74ed4cef7d4274aabcf3eda7b0b735574c6
ms.sourcegitcommit: 2ef0a4a90b01edd22b9206f8729b8de459ef8cab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2018
---
<a name="migrations-with-multiple-providers"></a><span data-ttu-id="0a945-102">移轉具有多個提供者</span><span class="sxs-lookup"><span data-stu-id="0a945-102">Migrations with Multiple Providers</span></span>
==================================
<span data-ttu-id="0a945-103">[EF 核心工具][ 1]只 scaffold 移轉為作用中的提供者。</span><span class="sxs-lookup"><span data-stu-id="0a945-103">The [EF Core Tools][1] only scaffold migrations for the active provider.</span></span> <span data-ttu-id="0a945-104">有時候，不過，您可能想要搭配您 DbContext 使用一個以上的提供者 （例如 Microsoft SQL Server 和 SQLite）。</span><span class="sxs-lookup"><span data-stu-id="0a945-104">Sometimes, however, you may want to use more than one provider (for example Microsoft SQL Server and SQLite) with your DbContext.</span></span> <span data-ttu-id="0a945-105">有兩種方式來處理此情形的移轉。</span><span class="sxs-lookup"><span data-stu-id="0a945-105">There are two ways to handle this with Migrations.</span></span> <span data-ttu-id="0a945-106">您可以維護兩個集合的移轉-一個用於每個提供者--或合併至單一設定，可以在同時工作。</span><span class="sxs-lookup"><span data-stu-id="0a945-106">You can maintain two sets of migrations--one for each provider--or merge them into a single set that can work on both.</span></span>

<a name="two-migration-sets"></a><span data-ttu-id="0a945-107">兩個移轉集合</span><span class="sxs-lookup"><span data-stu-id="0a945-107">Two migration sets</span></span>
------------------
<span data-ttu-id="0a945-108">在第一種方法，您會產生兩個移轉的每個模型變更。</span><span class="sxs-lookup"><span data-stu-id="0a945-108">In the first approach, you generate two migrations for each model change.</span></span>

<span data-ttu-id="0a945-109">這是將每個移轉集執行方式之一[分開的組件][ 2]手動加入兩個移轉之間進行切換使用中的提供者 （和移轉組件）。</span><span class="sxs-lookup"><span data-stu-id="0a945-109">One way to do this is to put each migration set [in a separate assembly][2] and manually switch the active provider (and migrations assembly) between adding the two migrations.</span></span>

<span data-ttu-id="0a945-110">容易使用工具的另一種方法是建立新的類型，衍生自您 DbContext 和覆寫使用中的提供者。</span><span class="sxs-lookup"><span data-stu-id="0a945-110">Another approach that makes working with the tools easier is to create a new type that derives from your DbContext and overrides the active provider.</span></span> <span data-ttu-id="0a945-111">此類型用在設計時新增或套用移轉的時間。</span><span class="sxs-lookup"><span data-stu-id="0a945-111">This type is used at design time when adding or applying migrations.</span></span>

``` csharp
class MySqliteDbContext : MyDbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

> [!NOTE]
> <span data-ttu-id="0a945-112">因為每個移轉組會使用它自己的 DbContext 類型，這個方法不需要使用個別移轉組件。</span><span class="sxs-lookup"><span data-stu-id="0a945-112">Since each migration set uses its own DbContext types, this approach doesn't require using a separate migrations assembly.</span></span>

<span data-ttu-id="0a945-113">當加入新的移轉，會指定內容類型。</span><span class="sxs-lookup"><span data-stu-id="0a945-113">When adding new migration, specify the context types.</span></span>

``` powershell
Add-Migration InitialCreate -Context MyDbContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context MySqliteDbContext -OutputDir Migrations\SqliteMigrations
```
``` Console
dotnet ef migrations add InitialCreate --context MyDbContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context MySqliteDbContext --output-dir Migrations/SqliteMigrations
```

> [!TIP]
> <span data-ttu-id="0a945-114">您不需要指定後續的移轉作業的輸出目錄，因為它們會建立為最後一個同層級。</span><span class="sxs-lookup"><span data-stu-id="0a945-114">You don't need to specify the output directory for subsequent migrations since they are created as siblings to the last one.</span></span>

<a name="one-migration-set"></a><span data-ttu-id="0a945-115">移轉設定</span><span class="sxs-lookup"><span data-stu-id="0a945-115">One migration set</span></span>
-----------------
<span data-ttu-id="0a945-116">如果您不喜歡具有兩個集合的移轉，可以手動將它們結合成單一集合可套用至兩個提供者。</span><span class="sxs-lookup"><span data-stu-id="0a945-116">If you don't like having two sets of migrations, you can manually combine them into a single set that can be applied to both providers.</span></span>

<span data-ttu-id="0a945-117">因為提供者會忽略不了解任何附註，註解可以同時存在。</span><span class="sxs-lookup"><span data-stu-id="0a945-117">Annotations can coexist since a provider ignores any annotations that it doesn't understand.</span></span> <span data-ttu-id="0a945-118">例如，適用於 Microsoft SQL Server 和 SQLite 主索引鍵資料行可能如下所示。</span><span class="sxs-lookup"><span data-stu-id="0a945-118">For example, a primary key column that works with both Microsoft SQL Server and SQLite might look like this.</span></span>

``` csharp
Id = table.Column<int>(nullable: false)
    .Annotation("SqlServer:ValueGenerationStrategy",
        SqlServerValueGenerationStrategy.IdentityColumn)
    .Annotation("Sqlite:Autoincrement", true),
```

<span data-ttu-id="0a945-119">如果作業只能套用一個提供者 （或它們以不同的方式提供者之間），使用`ActiveProvider`分辨哪一個提供者為作用中的屬性。</span><span class="sxs-lookup"><span data-stu-id="0a945-119">If operations can only be applied on one provider (or they're differently between providers), use the `ActiveProvider` property to tell which provider is active.</span></span>

``` csharp
if (migrationBuilder.ActiveProvider == "Microsoft.EntityFrameworkCore.SqlServer")
{
    migrationBuilder.CreateSequence(
        name: "EntityFrameworkHiLoSequence");
}
```


  [1]: ../../miscellaneous/cli/index.md
  [2]: projects.md
