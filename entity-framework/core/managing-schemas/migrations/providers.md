---
title: 使用多個提供者進行遷移-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/08/2017
uid: core/managing-schemas/migrations/providers
ms.openlocfilehash: 384ad27e405adc2bccb5e96aae30e5bd7ac556be
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824717"
---
# <a name="migrations-with-multiple-providers"></a><span data-ttu-id="2f557-102">使用多個提供者進行遷移</span><span class="sxs-lookup"><span data-stu-id="2f557-102">Migrations with Multiple Providers</span></span>

<span data-ttu-id="2f557-103">[EF Core 工具][1]僅 scaffold 作用中提供者的遷移。</span><span class="sxs-lookup"><span data-stu-id="2f557-103">The [EF Core Tools][1] only scaffold migrations for the active provider.</span></span> <span data-ttu-id="2f557-104">不過，有時候您可能會想要使用一個以上的提供者（例如 Microsoft SQL Server 和 SQLite）來搭配 DbCoNtext。</span><span class="sxs-lookup"><span data-stu-id="2f557-104">Sometimes, however, you may want to use more than one provider (for example Microsoft SQL Server and SQLite) with your DbContext.</span></span> <span data-ttu-id="2f557-105">有兩種方式可以透過遷移來處理這種情況。</span><span class="sxs-lookup"><span data-stu-id="2f557-105">There are two ways to handle this with Migrations.</span></span> <span data-ttu-id="2f557-106">您可以維護兩組遷移（每個提供者一個），或將它們合併成可同時在兩者上使用的單一集合。</span><span class="sxs-lookup"><span data-stu-id="2f557-106">You can maintain two sets of migrations--one for each provider--or merge them into a single set that can work on both.</span></span>

## <a name="two-migration-sets"></a><span data-ttu-id="2f557-107">兩個遷移集</span><span class="sxs-lookup"><span data-stu-id="2f557-107">Two migration sets</span></span>

<span data-ttu-id="2f557-108">在第一個方法中，您會針對每個模型變更產生兩個遷移。</span><span class="sxs-lookup"><span data-stu-id="2f557-108">In the first approach, you generate two migrations for each model change.</span></span>

<span data-ttu-id="2f557-109">其中一種方法是將每個遷移集放[在不同的元件中][2]，並在新增兩個遷移的過程中手動切換使用中的提供者（和遷移元件）。</span><span class="sxs-lookup"><span data-stu-id="2f557-109">One way to do this is to put each migration set [in a separate assembly][2] and manually switch the active provider (and migrations assembly) between adding the two migrations.</span></span>

<span data-ttu-id="2f557-110">使用工具更容易的另一種方法，就是建立一個衍生自 DbCoNtext 的新型別，並覆寫使用中的提供者。</span><span class="sxs-lookup"><span data-stu-id="2f557-110">Another approach that makes working with the tools easier is to create a new type that derives from your DbContext and overrides the active provider.</span></span> <span data-ttu-id="2f557-111">此類型是在設計階段用於新增或套用遷移時使用。</span><span class="sxs-lookup"><span data-stu-id="2f557-111">This type is used at design time when adding or applying migrations.</span></span>

``` csharp
class MySqliteDbContext : MyDbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

> [!NOTE]
> <span data-ttu-id="2f557-112">由於每個遷移集都使用自己的 DbCoNtext 類型，因此這種方法不需要使用個別的遷移元件。</span><span class="sxs-lookup"><span data-stu-id="2f557-112">Since each migration set uses its own DbContext types, this approach doesn't require using a separate migrations assembly.</span></span>

<span data-ttu-id="2f557-113">加入新的遷移時，請指定內容類型。</span><span class="sxs-lookup"><span data-stu-id="2f557-113">When adding new migration, specify the context types.</span></span>

## <a name="net-core-clitabdotnet-core-cli"></a>[<span data-ttu-id="2f557-114">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2f557-114">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --context MyDbContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context MySqliteDbContext --output-dir Migrations/SqliteMigrations
```

## <a name="visual-studiotabvs"></a>[<span data-ttu-id="2f557-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f557-115">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration InitialCreate -Context MyDbContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context MySqliteDbContext -OutputDir Migrations\SqliteMigrations
```

***

> [!TIP]
> <span data-ttu-id="2f557-116">您不需要指定輸出目錄來進行後續的遷移，因為它們會建立為最後一個的同級。</span><span class="sxs-lookup"><span data-stu-id="2f557-116">You don't need to specify the output directory for subsequent migrations since they are created as siblings to the last one.</span></span>

## <a name="one-migration-set"></a><span data-ttu-id="2f557-117">一個遷移集</span><span class="sxs-lookup"><span data-stu-id="2f557-117">One migration set</span></span>

<span data-ttu-id="2f557-118">如果您不喜歡兩組遷移，可以手動將它們結合成可以套用至這兩個提供者的單一集合。</span><span class="sxs-lookup"><span data-stu-id="2f557-118">If you don't like having two sets of migrations, you can manually combine them into a single set that can be applied to both providers.</span></span>

<span data-ttu-id="2f557-119">批註可以並存，因為提供者會忽略它不了解的任何注釋。</span><span class="sxs-lookup"><span data-stu-id="2f557-119">Annotations can coexist since a provider ignores any annotations that it doesn't understand.</span></span> <span data-ttu-id="2f557-120">例如，同時使用 Microsoft SQL Server 和 SQLite 的主鍵資料行，可能如下所示。</span><span class="sxs-lookup"><span data-stu-id="2f557-120">For example, a primary key column that works with both Microsoft SQL Server and SQLite might look like this.</span></span>

``` csharp
Id = table.Column<int>(nullable: false)
    .Annotation("SqlServer:ValueGenerationStrategy",
        SqlServerValueGenerationStrategy.IdentityColumn)
    .Annotation("Sqlite:Autoincrement", true),
```

<span data-ttu-id="2f557-121">如果作業只能套用至一個提供者（或在提供者之間不同），請使用 `ActiveProvider` 屬性來分辨哪個提供者為作用中。</span><span class="sxs-lookup"><span data-stu-id="2f557-121">If operations can only be applied on one provider (or they're differently between providers), use the `ActiveProvider` property to tell which provider is active.</span></span>

``` csharp
if (migrationBuilder.ActiveProvider == "Microsoft.EntityFrameworkCore.SqlServer")
{
    migrationBuilder.CreateSequence(
        name: "EntityFrameworkHiLoSequence");
}
```

  [1]: ../../miscellaneous/cli/index.md
  [2]: projects.md
