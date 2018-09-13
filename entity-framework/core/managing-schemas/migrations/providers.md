---
title: 移轉具有多個提供者的 EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/08/2017
ms.openlocfilehash: 75c055221609679db3f00016b9cb44c6c8c6e473
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45488773"
---
<a name="migrations-with-multiple-providers"></a><span data-ttu-id="a40e7-102">移轉具有多個提供者</span><span class="sxs-lookup"><span data-stu-id="a40e7-102">Migrations with Multiple Providers</span></span>
==================================
<span data-ttu-id="a40e7-103">[EF Core 工具][ 1]只 scaffold 移轉為作用中的提供者。</span><span class="sxs-lookup"><span data-stu-id="a40e7-103">The [EF Core Tools][1] only scaffold migrations for the active provider.</span></span> <span data-ttu-id="a40e7-104">有時候，不過，您可能想要使用您的 DbContext 中的多個提供者 （例如 Microsoft SQL Server 和 SQLite）。</span><span class="sxs-lookup"><span data-stu-id="a40e7-104">Sometimes, however, you may want to use more than one provider (for example Microsoft SQL Server and SQLite) with your DbContext.</span></span> <span data-ttu-id="a40e7-105">有兩種方式可使用移轉處理這部分。</span><span class="sxs-lookup"><span data-stu-id="a40e7-105">There are two ways to handle this with Migrations.</span></span> <span data-ttu-id="a40e7-106">您可以維護兩個集合的移轉，另一個用於每個提供者--或合併到單一設定，可以在兩者上工作。</span><span class="sxs-lookup"><span data-stu-id="a40e7-106">You can maintain two sets of migrations--one for each provider--or merge them into a single set that can work on both.</span></span>

<a name="two-migration-sets"></a><span data-ttu-id="a40e7-107">兩個移轉集合</span><span class="sxs-lookup"><span data-stu-id="a40e7-107">Two migration sets</span></span>
------------------
<span data-ttu-id="a40e7-108">在第一個方法中，您可以產生兩個移轉的每個模型變更。</span><span class="sxs-lookup"><span data-stu-id="a40e7-108">In the first approach, you generate two migrations for each model change.</span></span>

<span data-ttu-id="a40e7-109">這是輸入每個移轉一組其中一種方式[分開的組件][ 2]和手動切換作用中的提供者 （和移轉的組件） 加入兩個移轉。</span><span class="sxs-lookup"><span data-stu-id="a40e7-109">One way to do this is to put each migration set [in a separate assembly][2] and manually switch the active provider (and migrations assembly) between adding the two migrations.</span></span>

<span data-ttu-id="a40e7-110">更輕鬆使用這些工具的另一種方法是建立新的型別衍生自您的 DbContext，並會覆寫作用中的提供者。</span><span class="sxs-lookup"><span data-stu-id="a40e7-110">Another approach that makes working with the tools easier is to create a new type that derives from your DbContext and overrides the active provider.</span></span> <span data-ttu-id="a40e7-111">此類型可用於在設計時新增，或套用移轉的時間。</span><span class="sxs-lookup"><span data-stu-id="a40e7-111">This type is used at design time when adding or applying migrations.</span></span>

``` csharp
class MySqliteDbContext : MyDbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

> [!NOTE]
> <span data-ttu-id="a40e7-112">由於每個移轉組會使用自己的 DbContext 類型，這個方法不需要使用不同的移轉組件。</span><span class="sxs-lookup"><span data-stu-id="a40e7-112">Since each migration set uses its own DbContext types, this approach doesn't require using a separate migrations assembly.</span></span>

<span data-ttu-id="a40e7-113">當新增新的移轉，來指定內容類型。</span><span class="sxs-lookup"><span data-stu-id="a40e7-113">When adding new migration, specify the context types.</span></span>

``` powershell
Add-Migration InitialCreate -Context MyDbContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context MySqliteDbContext -OutputDir Migrations\SqliteMigrations
```
``` Console
dotnet ef migrations add InitialCreate --context MyDbContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context MySqliteDbContext --output-dir Migrations/SqliteMigrations
```

> [!TIP]
> <span data-ttu-id="a40e7-114">您不需要指定輸出目錄進行後續的移轉，因為它們會建立為與最後一個同層級。</span><span class="sxs-lookup"><span data-stu-id="a40e7-114">You don't need to specify the output directory for subsequent migrations since they are created as siblings to the last one.</span></span>

<a name="one-migration-set"></a><span data-ttu-id="a40e7-115">一個移轉組</span><span class="sxs-lookup"><span data-stu-id="a40e7-115">One migration set</span></span>
-----------------
<span data-ttu-id="a40e7-116">如果您不喜歡有兩個集合的移轉，可以手動將它們結合成一組可套用至這兩個提供者。</span><span class="sxs-lookup"><span data-stu-id="a40e7-116">If you don't like having two sets of migrations, you can manually combine them into a single set that can be applied to both providers.</span></span>

<span data-ttu-id="a40e7-117">註解可以同時存在，因為提供者會忽略它並不了解任何註解。</span><span class="sxs-lookup"><span data-stu-id="a40e7-117">Annotations can coexist since a provider ignores any annotations that it doesn't understand.</span></span> <span data-ttu-id="a40e7-118">例如，適用於 Microsoft SQL Server 和 SQLite 的主索引鍵資料行可能如下所示。</span><span class="sxs-lookup"><span data-stu-id="a40e7-118">For example, a primary key column that works with both Microsoft SQL Server and SQLite might look like this.</span></span>

``` csharp
Id = table.Column<int>(nullable: false)
    .Annotation("SqlServer:ValueGenerationStrategy",
        SqlServerValueGenerationStrategy.IdentityColumn)
    .Annotation("Sqlite:Autoincrement", true),
```

<span data-ttu-id="a40e7-119">如果作業只能套用一個提供者 （或它們有不同的提供者之間），使用`ActiveProvider`告知哪一個提供者為作用中的屬性。</span><span class="sxs-lookup"><span data-stu-id="a40e7-119">If operations can only be applied on one provider (or they're differently between providers), use the `ActiveProvider` property to tell which provider is active.</span></span>

``` csharp
if (migrationBuilder.ActiveProvider == "Microsoft.EntityFrameworkCore.SqlServer")
{
    migrationBuilder.CreateSequence(
        name: "EntityFrameworkHiLoSequence");
}
```


  [1]: ../../miscellaneous/cli/index.md
  [2]: projects.md
