---
title: EF Core 5.0 的新功能
description: EF Core 5.0 的新功能總覽
author: ajcvickers
ms.date: 07/20/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: f822e3ae776778749a654377cbd9d9814ca40972
ms.sourcegitcommit: 12d257db4786487a0c28e9ddd79f176f7cf6edb1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89043593"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="a3e80-103">EF Core 5.0 的新功能</span><span class="sxs-lookup"><span data-stu-id="a3e80-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="a3e80-104">EF Core 5.0 目前正在開發中。</span><span class="sxs-lookup"><span data-stu-id="a3e80-104">EF Core 5.0 is currently in development.</span></span> <span data-ttu-id="a3e80-105">此頁面將包含每個預覽中所引進之有趣變更的總覽。</span><span class="sxs-lookup"><span data-stu-id="a3e80-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="a3e80-106">此頁面不會複製 [EF Core 5.0 的方案](xref:core/what-is-new/ef-core-5.0/plan)。</span><span class="sxs-lookup"><span data-stu-id="a3e80-106">This page does not duplicate the [plan for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan).</span></span> <span data-ttu-id="a3e80-107">此計畫描述 EF Core 5.0 的整體主題，包括我們在出貨最終發行版本之前打算包含的所有專案。</span><span class="sxs-lookup"><span data-stu-id="a3e80-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="a3e80-108">我們會在發行時，將連結從這裡新增至官方檔。</span><span class="sxs-lookup"><span data-stu-id="a3e80-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-8"></a><span data-ttu-id="a3e80-109">Preview 8</span><span class="sxs-lookup"><span data-stu-id="a3e80-109">Preview 8</span></span>

### <a name="table-per-type-tpt-mapping"></a><span data-ttu-id="a3e80-110">每一類型的 (TPT) 對應的資料表</span><span class="sxs-lookup"><span data-stu-id="a3e80-110">Table-per-type (TPT) mapping</span></span>

<span data-ttu-id="a3e80-111">根據預設，EF Core 會將 .NET 類型的繼承階層對應到單一資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="a3e80-111">By default, EF Core maps an inheritance hierarchy of .NET types to a single database table.</span></span> <span data-ttu-id="a3e80-112">這稱為每一階層的資料表 (TPH) 對應。</span><span class="sxs-lookup"><span data-stu-id="a3e80-112">This is known as table-per-hierarchy (TPH) mapping.</span></span> <span data-ttu-id="a3e80-113">EF Core 5.0 也可將繼承階層架構中的每個 .NET 類型對應至不同的資料庫資料表;稱為每一類型的資料表 (TPT) 對應。</span><span class="sxs-lookup"><span data-stu-id="a3e80-113">EF Core 5.0 also allows mapping each .NET type in an inheritance hierarchy to a different database table; known as table-per-type (TPT) mapping.</span></span>

<span data-ttu-id="a3e80-114">例如，請考慮使用對應階層的這個模型：</span><span class="sxs-lookup"><span data-stu-id="a3e80-114">For example, consider this model with a mapped hierarchy:</span></span>

```c#
public class Animal
{
    public int Id { get; set; }
    public string Species { get; set; }
}

public class Pet : Animal
{
    public string Name { get; set; }
}

public class Cat : Pet
{
    public string EdcuationLevel { get; set; }
}

public class Dog : Pet
{
    public string FavoriteToy { get; set; }
}
```

<span data-ttu-id="a3e80-115">根據預設，EF Core 會將此對應到單一資料表：</span><span class="sxs-lookup"><span data-stu-id="a3e80-115">By default, EF Core will map this to a single table:</span></span>

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Species] nvarchar(max) NULL,
    [Discriminator] nvarchar(max) NOT NULL,
    [Name] nvarchar(max) NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);
```

<span data-ttu-id="a3e80-116">不過，將每個實體類型對應至不同的資料表，會改為每個類型產生一個資料表：</span><span class="sxs-lookup"><span data-stu-id="a3e80-116">However, mapping each entity type to a different table will instead result in one table per type:</span></span>

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Species] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);

CREATE TABLE [Pets] (
    [Id] int NOT NULL,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Pets] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Pets_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION
);

CREATE TABLE [Cats] (
    [Id] int NOT NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    CONSTRAINT [PK_Cats] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Cats_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
    CONSTRAINT [FK_Cats_Pets_Id] FOREIGN KEY ([Id]) REFERENCES [Pets] ([Id]) ON DELETE NO ACTION
);

CREATE TABLE [Dogs] (
    [Id] int NOT NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Dogs] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Dogs_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
    CONSTRAINT [FK_Dogs_Pets_Id] FOREIGN KEY ([Id]) REFERENCES [Pets] ([Id]) ON DELETE NO ACTION
);
```

<span data-ttu-id="a3e80-117">請注意，在將 preview 8 的程式碼分支出來之後，會新增上述的外鍵條件約束的建立。</span><span class="sxs-lookup"><span data-stu-id="a3e80-117">Note that creation of the foreign key constraints shown above were added after branching the code for preview 8.</span></span>

<span data-ttu-id="a3e80-118">您可以使用對應屬性，將實體類型對應至不同的資料表：</span><span class="sxs-lookup"><span data-stu-id="a3e80-118">Entity types can be mapped to different tables using mapping attributes:</span></span>

```c#
[Table("Animals")]
public class Animal
{
    public int Id { get; set; }
    public string Species { get; set; }
}

[Table("Pets")]
public class Pet : Animal
{
    public string Name { get; set; }
}

[Table("Cats")]
public class Cat : Pet
{
    public string EdcuationLevel { get; set; }
}

[Table("Dogs")]
public class Dog : Pet
{
    public string FavoriteToy { get; set; }
}
```

<span data-ttu-id="a3e80-119">或使用設定 `ModelBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="a3e80-119">Or using `ModelBuilder` configuration:</span></span>

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Animal>().ToTable("Animals");
    modelBuilder.Entity<Pet>().ToTable("Pets");
    modelBuilder.Entity<Cat>().ToTable("Cats");
    modelBuilder.Entity<Dog>().ToTable("Dogs");
}
```

<span data-ttu-id="a3e80-120">檔是由問題 [#1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-120">Documentation is tracked by issue [#1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979).</span></span>

### <a name="migrations-rebuild-sqlite-tables"></a><span data-ttu-id="a3e80-121">遷移：重建 SQLite 資料表</span><span class="sxs-lookup"><span data-stu-id="a3e80-121">Migrations: Rebuild SQLite tables</span></span>

<span data-ttu-id="a3e80-122">相較于其他資料庫，SQLite 的架構操作功能相對受限。</span><span class="sxs-lookup"><span data-stu-id="a3e80-122">Compared to other database, SQLite is relatively limited in its schema manipulation capabilities.</span></span> <span data-ttu-id="a3e80-123">例如，從現有的資料表卸載資料行時，必須卸載並重新建立整個資料表。</span><span class="sxs-lookup"><span data-stu-id="a3e80-123">For example, dropping a column from an existing table requires that the entire table be dropped and re-created.</span></span> <span data-ttu-id="a3e80-124">EF Core 5.0 遷移現在支援自動重建資料表，以進行需要的架構變更。</span><span class="sxs-lookup"><span data-stu-id="a3e80-124">EF Core 5.0 Migrations now supports automatic rebuilding the table for schema changes that require it.</span></span>

<span data-ttu-id="a3e80-125">例如，假設我們有一個 `Unicorns` 針對實體類型建立的資料表 `Unicorn` ：</span><span class="sxs-lookup"><span data-stu-id="a3e80-125">For example, imagine we have a `Unicorns` table created for a `Unicorn` entity type:</span></span>

```c#
public class Unicorn
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
}
```

```sql
CREATE TABLE "Unicorns" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_Unicorns" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL,
    "Age" INTEGER NOT NULL
);
```

<span data-ttu-id="a3e80-126">接下來，我們將瞭解如何將獨角獸的年齡視為非常不一樣，因此讓我們移除該屬性、新增新的遷移，並更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="a3e80-126">We then learn that storing the age of a unicorn is considered very rude, so let's remove that property, add a new migration, and update the database.</span></span> <span data-ttu-id="a3e80-127">使用 EF Core 3.1 時，此更新將會失敗，因為無法卸載資料行。</span><span class="sxs-lookup"><span data-stu-id="a3e80-127">This update will fail when using EF Core 3.1 because the column cannot be dropped.</span></span> <span data-ttu-id="a3e80-128">在 EF Core 5.0 中，遷移會改為重建資料表：</span><span class="sxs-lookup"><span data-stu-id="a3e80-128">In EF Core 5.0, Migrations will instead rebuild the table:</span></span>

```sql
CREATE TABLE "ef_temp_Unicorns" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_Unicorns" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL
);

INSERT INTO "ef_temp_Unicorns" ("Id", "Name")
SELECT "Id", "Name"
FROM Unicorns;

PRAGMA foreign_keys = 0;

DROP TABLE "Unicorns";

ALTER TABLE "ef_temp_Unicorns" RENAME TO "Unicorns";

PRAGMA foreign_keys = 1;
```

<span data-ttu-id="a3e80-129">請注意：</span><span class="sxs-lookup"><span data-stu-id="a3e80-129">Notice that:</span></span>
* <span data-ttu-id="a3e80-130">使用新資料表所需的架構來建立臨時表</span><span class="sxs-lookup"><span data-stu-id="a3e80-130">A temporary table is created with the desired schema for the new table</span></span>
* <span data-ttu-id="a3e80-131">將資料從目前資料表複製到臨時表</span><span class="sxs-lookup"><span data-stu-id="a3e80-131">Data is copied from the current table into the temporary table</span></span>
* <span data-ttu-id="a3e80-132">外鍵強制切換關閉</span><span class="sxs-lookup"><span data-stu-id="a3e80-132">Foreign key enforcement is switched off</span></span>
* <span data-ttu-id="a3e80-133">刪除目前的資料表</span><span class="sxs-lookup"><span data-stu-id="a3e80-133">The current table is dropped</span></span>
* <span data-ttu-id="a3e80-134">臨時表重新命名為新的資料表</span><span class="sxs-lookup"><span data-stu-id="a3e80-134">The temporary table is renamed to be the new table</span></span>

<span data-ttu-id="a3e80-135">檔是由問題 [#2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-135">Documentation is tracked by issue [#2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583).</span></span>

### <a name="table-valued-functions"></a><span data-ttu-id="a3e80-136">資料表值函式</span><span class="sxs-lookup"><span data-stu-id="a3e80-136">Table-valued functions</span></span>

<span data-ttu-id="a3e80-137">這項功能是由「社區」所貢獻 [@pmiddleton](https://github.com/pmiddleton) 。</span><span class="sxs-lookup"><span data-stu-id="a3e80-137">This feature was contributed from the community by [@pmiddleton](https://github.com/pmiddleton).</span></span> <span data-ttu-id="a3e80-138">許多人都感謝您的貢獻！</span><span class="sxs-lookup"><span data-stu-id="a3e80-138">Many thanks for the contribution!</span></span>

<span data-ttu-id="a3e80-139">EF Core 5.0 包含將 .NET 方法對應至資料表值函式 (Tvf) 的第一級支援。</span><span class="sxs-lookup"><span data-stu-id="a3e80-139">EF Core 5.0 includes first-class support for mapping .NET methods to table-valued functions (TVFs).</span></span> <span data-ttu-id="a3e80-140">然後，這些函式可以在 LINQ 查詢中使用，而函式結果的其他組合也會轉譯為 SQL。</span><span class="sxs-lookup"><span data-stu-id="a3e80-140">These functions can then be used in LINQ queries where additional composition on the results of the function will also be translated to SQL.</span></span>

<span data-ttu-id="a3e80-141">例如，請考慮在 SQL Server 資料庫中定義的 TVF：</span><span class="sxs-lookup"><span data-stu-id="a3e80-141">For example, consider this TVF defined in a SQL Server database:</span></span>

```sql
CREATE FUNCTION GetReports(@employeeId int)
RETURNS @reports TABLE
(
    Name nvarchar(50) NOT NULL,
    IsDeveloper bit NOT NULL
)
AS
BEGIN
    WITH cteEmployees AS
    (
        SELECT Id, Name, ManagerId, IsDeveloper
        FROM Employees
        WHERE Id = @employeeId
        UNION ALL
        SELECT e.Id, e.Name, e.ManagerId, e.IsDeveloper
        FROM Employees e
        INNER JOIN cteEmployees cteEmp ON cteEmp.Id = e.ManagerId
    )
    INSERT INTO @reports
    SELECT Name, IsDeveloper
    FROM cteEmployees
    WHERE Id != @employeeId

    RETURN
END
```

<span data-ttu-id="a3e80-142">EF Core 模型需要兩個實體類型才能使用此 TVF：</span><span class="sxs-lookup"><span data-stu-id="a3e80-142">The EF Core model requires two entity types to use this TVF:</span></span>
* <span data-ttu-id="a3e80-143">以 `Employee` 正常方式對應至 Employees 資料表的類型</span><span class="sxs-lookup"><span data-stu-id="a3e80-143">An `Employee` type that maps to the Employees table in the normal way</span></span>
* <span data-ttu-id="a3e80-144">`Report`符合 TVF 所傳回之圖形的類型</span><span class="sxs-lookup"><span data-stu-id="a3e80-144">A `Report` type that matches the shape returned by the TVF</span></span>

```c#
public class Employee
{
    public int Id { get; set; }
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }

    public int? ManagerId { get; set; }
    public virtual Employee Manager { get; set; }
}
```

```c#
public class Report
{
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }
}
```

<span data-ttu-id="a3e80-145">這些類型必須包含在 EF Core 模型中：</span><span class="sxs-lookup"><span data-stu-id="a3e80-145">These types must be included in the EF Core model:</span></span>

```c#
modelBuilder.Entity<Employee>();
modelBuilder.Entity(typeof(Report)).HasNoKey();
```

<span data-ttu-id="a3e80-146">請注意，沒有 `Report` 主鍵，因此必須設定為。</span><span class="sxs-lookup"><span data-stu-id="a3e80-146">Notice that `Report` has no primary key and so must be configured as such.</span></span>

<span data-ttu-id="a3e80-147">最後，.NET 方法必須對應到資料庫中的 TVF。</span><span class="sxs-lookup"><span data-stu-id="a3e80-147">Finally, a .NET method must be mapped to the TVF in the database.</span></span> <span data-ttu-id="a3e80-148">您可以使用新的方法，在 DbCoNtext 上定義這個方法 `FromExpression` ：</span><span class="sxs-lookup"><span data-stu-id="a3e80-148">This method can be defined on the DbContext using the new `FromExpression` method:</span></span>

```c#
public IQueryable<Report> GetReports(int managerId)
    => FromExpression(() => GetReports(managerId));
```

<span data-ttu-id="a3e80-149">這個方法會使用符合上述定義之 TVF 的參數和傳回型別。</span><span class="sxs-lookup"><span data-stu-id="a3e80-149">This method uses a parameter and return type that match the TVF defined above.</span></span> <span data-ttu-id="a3e80-150">然後，方法會新增至 OnModelCreating 中的 EF Core 模型：</span><span class="sxs-lookup"><span data-stu-id="a3e80-150">The method is then added to the EF Core model in OnModelCreating:</span></span>

```c#
modelBuilder.HasDbFunction(() => GetReports(default));
```

<span data-ttu-id="a3e80-151">使用 lambda 的 (是將傳送至 EF Core 的簡單方法 `MethodInfo` 。</span><span class="sxs-lookup"><span data-stu-id="a3e80-151">(Using a lambda here is an easy way to pass the `MethodInfo` to EF Core.</span></span> <span data-ttu-id="a3e80-152">傳遞給方法的引數會被忽略。 ) </span><span class="sxs-lookup"><span data-stu-id="a3e80-152">The arguments passed to the method are ignored.)</span></span>

<span data-ttu-id="a3e80-153">我們現在可以撰寫 `GetReports` 在結果上呼叫和撰寫的查詢。</span><span class="sxs-lookup"><span data-stu-id="a3e80-153">We can now write queries that call `GetReports` and compose over the results.</span></span> <span data-ttu-id="a3e80-154">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-154">For example:</span></span>

```c#
from e in context.Employees
from rc in context.GetReports(e.Id)
where rc.IsDeveloper == true
select new
{
  ManagerName = e.Name,
  EmployeeName = rc.Name,
})
```

<span data-ttu-id="a3e80-155">在 SQL Server 上，這會轉譯為：</span><span class="sxs-lookup"><span data-stu-id="a3e80-155">On SQL Server, this translates to:</span></span>

```sql
SELECT [e].[Name] AS [ManagerName], [g].[Name] AS [EmployeeName]
FROM [Employees] AS [e]
CROSS APPLY [dbo].[GetReports]([e].[Id]) AS [g]
WHERE [g].[IsDeveloper] = CAST(1 AS bit)
```

<span data-ttu-id="a3e80-156">請注意，SQL 的根目錄是在 `Employees` 資料表中呼叫 `GetReports` ，然後在函式的結果上加入額外的 WHERE 子句。</span><span class="sxs-lookup"><span data-stu-id="a3e80-156">Notice that the SQL is rooted in the `Employees` table, calls `GetReports`, and then adds an additional WHERE clause on the results of the function.</span></span>

### <a name="flexible-queryupdate-mapping"></a><span data-ttu-id="a3e80-157">彈性查詢/更新對應</span><span class="sxs-lookup"><span data-stu-id="a3e80-157">Flexible query/update mapping</span></span>

<span data-ttu-id="a3e80-158">EF Core 5.0 可將相同的實體類型對應至不同的資料庫物件。</span><span class="sxs-lookup"><span data-stu-id="a3e80-158">EF Core 5.0 allows mapping the same entity type to different database objects.</span></span> <span data-ttu-id="a3e80-159">這些物件可以是資料表、views 或函數。</span><span class="sxs-lookup"><span data-stu-id="a3e80-159">These objects may be tables, views, or functions.</span></span>

<span data-ttu-id="a3e80-160">例如，實體類型可以對應至資料庫和資料庫資料表：</span><span class="sxs-lookup"><span data-stu-id="a3e80-160">For example, an entity type can be mapped to both a database view and a database table:</span></span>

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

<span data-ttu-id="a3e80-161">根據預設，EF Core 會從 view 查詢並將更新傳送至資料表。</span><span class="sxs-lookup"><span data-stu-id="a3e80-161">By default, EF Core will then query from the view and send updates to the table.</span></span> <span data-ttu-id="a3e80-162">例如，執行下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="a3e80-162">For example, executing the following code:</span></span>

```c#
var blog = context.Set<Blog>().Single(e => e.Name == "One Unicorn");

blog.Name = "1unicorn2";

context.SaveChanges();
```

<span data-ttu-id="a3e80-163">針對視圖產生查詢，然後對資料表進行更新：</span><span class="sxs-lookup"><span data-stu-id="a3e80-163">Results in a query against the view, and then an update to the table:</span></span>

```sql
SELECT TOP(2) [b].[Id], [b].[Name], [b].[Url]
FROM [BlogsView] AS [b]
WHERE [b].[Name] = N'One Unicorn'

SET NOCOUNT ON;
UPDATE [Blogs] SET [Name] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="context-wide-split-query-configuration"></a><span data-ttu-id="a3e80-164">整個環境的分割查詢設定</span><span class="sxs-lookup"><span data-stu-id="a3e80-164">Context-wide split-query configuration</span></span>

<span data-ttu-id="a3e80-165">分割查詢 (看到下面) 現在可以設定為 DbCoNtext 所執行之任何查詢的預設值。</span><span class="sxs-lookup"><span data-stu-id="a3e80-165">Split queries (see below) can now be configured as the default for any query executed by the DbContext.</span></span> <span data-ttu-id="a3e80-166">這項設定僅適用于關聯式提供者，因此必須指定為設定的一部分 `UseProvider` 。</span><span class="sxs-lookup"><span data-stu-id="a3e80-166">This configuration is only available for relational providers, and so must be specified as part of the `UseProvider` configuration.</span></span> <span data-ttu-id="a3e80-167">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-167">For example:</span></span>

```c#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(
            Your.SqlServerConnectionString,
            b => b.UseQuerySplittingBehavior(QuerySplittingBehavior.SplitQuery));
```

<span data-ttu-id="a3e80-168">檔是由問題 [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-168">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="physicaladdress-mapping"></a><span data-ttu-id="a3e80-169">PhysicalAddress 對應</span><span class="sxs-lookup"><span data-stu-id="a3e80-169">PhysicalAddress mapping</span></span>

<span data-ttu-id="a3e80-170">這項功能是由「社區」所貢獻 [@ralmsdeveloper](https://github.com/ralmsdeveloper) 。</span><span class="sxs-lookup"><span data-stu-id="a3e80-170">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="a3e80-171">許多人都感謝您的貢獻！</span><span class="sxs-lookup"><span data-stu-id="a3e80-171">Many thanks for the contribution!</span></span>

<span data-ttu-id="a3e80-172">標準的 .NET [PhysicalAddress 類別](/dotnet/api/system.net.networkinformation.physicaladdress) 現在會自動對應到尚未具有原生支援之資料庫的字串資料行。</span><span class="sxs-lookup"><span data-stu-id="a3e80-172">The standard .NET [PhysicalAddress class](/dotnet/api/system.net.networkinformation.physicaladdress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="a3e80-173">如需詳細資訊，請參閱下面的範例 `IPAddress` 。</span><span class="sxs-lookup"><span data-stu-id="a3e80-173">For more information, see the examples for `IPAddress` below.</span></span>

## <a name="preview-7"></a><span data-ttu-id="a3e80-174">Preview 7</span><span class="sxs-lookup"><span data-stu-id="a3e80-174">Preview 7</span></span>

### <a name="dbcontextfactory"></a><span data-ttu-id="a3e80-175">DbCoNtextFactory</span><span class="sxs-lookup"><span data-stu-id="a3e80-175">DbContextFactory</span></span>

<span data-ttu-id="a3e80-176">EF Core 5.0 引進 `AddDbContextFactory` 並 `AddPooledDbContextFactory` 註冊 factory，以在應用程式的相依性插入 (D.I. ) 容器中建立 DbCoNtext 實例。</span><span class="sxs-lookup"><span data-stu-id="a3e80-176">EF Core 5.0 introduces `AddDbContextFactory` and `AddPooledDbContextFactory` to register a factory for creating DbContext instances in the application's dependency injection (D.I.) container.</span></span> <span data-ttu-id="a3e80-177">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-177">For example:</span></span>

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

<span data-ttu-id="a3e80-178">然後，應用程式服務（例如 ASP.NET Core 控制器）可相依于 `IDbContextFactory<TContext>` 服務的函式中。</span><span class="sxs-lookup"><span data-stu-id="a3e80-178">Application services such as ASP.NET Core controllers can then depend on `IDbContextFactory<TContext>` in the service constructor.</span></span> <span data-ttu-id="a3e80-179">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-179">For example:</span></span>

```csharp
public class MyController
{
    private readonly IDbContextFactory<SomeDbContext> _contextFactory;

    public MyController(IDbContextFactory<SomeDbContext> contextFactory)
    {
        _contextFactory = contextFactory;
    }
}
```

<span data-ttu-id="a3e80-180">然後，您可以視需要建立和使用 DbCoNtext 實例。</span><span class="sxs-lookup"><span data-stu-id="a3e80-180">DbContext instances can then be created and used as needed.</span></span> <span data-ttu-id="a3e80-181">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-181">For example:</span></span>

```csharp
public void DoSomeThing()
{
    using (var context = _contextFactory.CreateDbContext())
    {
        // ...
    }
}
```

<span data-ttu-id="a3e80-182">請注意，以這種方式建立的 DbCoNtext 實例 _不_ 會由應用程式的服務提供者來管理，因此必須由應用程式處置。</span><span class="sxs-lookup"><span data-stu-id="a3e80-182">Note that the DbContext instances created in this way are _not_ managed by the application's service provider and therefore must be disposed by the application.</span></span> <span data-ttu-id="a3e80-183">這種分離功能對於 Blazor 應用程式非常有用， `IDbContextFactory` 但建議使用，但在其他情況下也可能很有用。</span><span class="sxs-lookup"><span data-stu-id="a3e80-183">This decoupling is very useful for Blazor applications, where using `IDbContextFactory` is recommended, but may also be useful in other scenarios.</span></span>

<span data-ttu-id="a3e80-184">您可以藉由呼叫來將 DbCoNtext 實例集區 `AddPooledDbContextFactory` 。</span><span class="sxs-lookup"><span data-stu-id="a3e80-184">DbContext instances can be pooled by calling `AddPooledDbContextFactory`.</span></span> <span data-ttu-id="a3e80-185">此共用的運作方式與相同 `AddDbContextPool` ，而且也具有相同的限制。</span><span class="sxs-lookup"><span data-stu-id="a3e80-185">This pooling works the same way as for `AddDbContextPool`, and also has the same limitations.</span></span>

<span data-ttu-id="a3e80-186">檔是由問題 [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-186">Documentation is tracked by issue [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).</span></span>

### <a name="reset-dbcontext-state"></a><span data-ttu-id="a3e80-187">重設 DbCoNtext 狀態</span><span class="sxs-lookup"><span data-stu-id="a3e80-187">Reset DbContext state</span></span>

<span data-ttu-id="a3e80-188">EF Core 5.0 引進了哪些專案會 `ChangeTracker.Clear()` 清除所有已追蹤實體的 DbCoNtext。</span><span class="sxs-lookup"><span data-stu-id="a3e80-188">EF Core 5.0 introduces `ChangeTracker.Clear()` which clears the DbContext of all tracked entities.</span></span> <span data-ttu-id="a3e80-189">針對每個工作單位，使用建立新的短期內容實例的最佳作法時，通常不需要這麼做。</span><span class="sxs-lookup"><span data-stu-id="a3e80-189">This should usually not be needed when using the best practice of creating a new, short-lived context instance for each unit-of-work.</span></span> <span data-ttu-id="a3e80-190">但是，如果需要重設 DbCoNtext 實例的狀態，則使用新的 `Clear()` 方法會比大量卸離所有實體更具效能和強大。</span><span class="sxs-lookup"><span data-stu-id="a3e80-190">However, if there is a need to reset the state of a DbContext instance, then using the new `Clear()` method is more performant and robust than mass-detaching all entities.</span></span>

<span data-ttu-id="a3e80-191">檔是由問題 [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-191">Documentation is tracked by issue [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span></span>

### <a name="new-pattern-for-store-generated-defaults"></a><span data-ttu-id="a3e80-192">存放區產生之預設值的新模式</span><span class="sxs-lookup"><span data-stu-id="a3e80-192">New pattern for store-generated defaults</span></span>

<span data-ttu-id="a3e80-193">EF Core 允許針對可能也有預設值條件約束的資料行設定明確值。</span><span class="sxs-lookup"><span data-stu-id="a3e80-193">EF Core allows an explicit value to be set for a column that may also have default value constraint.</span></span> <span data-ttu-id="a3e80-194">EF Core 使用類型屬性類型的 CLR 預設值做為這個的 sentinel;如果值不是 CLR 預設值，則會將其插入，否則會使用資料庫預設值。</span><span class="sxs-lookup"><span data-stu-id="a3e80-194">EF Core uses the CLR default of type property type as a sentinel for this; if the value is not the CLR default, then it is inserted, otherwise the database default is used.</span></span>

<span data-ttu-id="a3e80-195">這會為 CLR 預設不是良好 sentinel 的型別（最值得注意的是屬性）建立問題 `bool` 。</span><span class="sxs-lookup"><span data-stu-id="a3e80-195">This creates problems for types where the CLR default is not a good sentinel--most notably, `bool` properties.</span></span> <span data-ttu-id="a3e80-196">EF Core 5.0 現在可讓支援欄位可針對像這樣的案例提供可為 null。</span><span class="sxs-lookup"><span data-stu-id="a3e80-196">EF Core 5.0 now allows the backing field to be nullable for cases like this.</span></span> <span data-ttu-id="a3e80-197">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-197">For example:</span></span>

```csharp
public class Blog
{
    private bool? _isValid;

    public bool IsValid
    {
        get => _isValid ?? false;
        set => _isValid = value;
    }
}
```

<span data-ttu-id="a3e80-198">請注意，支援欄位可為 null，但公開的屬性則不是。</span><span class="sxs-lookup"><span data-stu-id="a3e80-198">Note that the backing field is nullable, but the publicly exposed property is not.</span></span> <span data-ttu-id="a3e80-199">這可讓 sentinel 值不會 `null` 影響實體類型的公用介面。</span><span class="sxs-lookup"><span data-stu-id="a3e80-199">This allows the sentinel value to be `null` without impacting the public surface of the entity type.</span></span> <span data-ttu-id="a3e80-200">在此情況下，如果 `IsValid` 從未設定，則會使用資料庫預設值，因為支援欄位會維持 null。</span><span class="sxs-lookup"><span data-stu-id="a3e80-200">In this case, if the `IsValid` is never set, then the database default will be used since the backing field remains null.</span></span> <span data-ttu-id="a3e80-201">如果 `true` `false` 設定了或，則此值會明確地儲存至資料庫。</span><span class="sxs-lookup"><span data-stu-id="a3e80-201">If either `true` or `false` are set, then this value is saved explicitly to the database.</span></span>

<span data-ttu-id="a3e80-202">檔是由問題 [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-202">Documentation is tracked by issue [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="a3e80-203">Cosmos 分割區索引鍵</span><span class="sxs-lookup"><span data-stu-id="a3e80-203">Cosmos partition keys</span></span>

<span data-ttu-id="a3e80-204">EF Core 允許 EF 模型中包含 Cosmos 分割區索引鍵。</span><span class="sxs-lookup"><span data-stu-id="a3e80-204">EF Core allows the Cosmos partition key is included in the EF model.</span></span> <span data-ttu-id="a3e80-205">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-205">For example:</span></span>

```csharp
modelBuilder.Entity<Customer>().HasPartitionKey(b => b.AlternateKey)
```

<span data-ttu-id="a3e80-206">從 preview 7 開始，資料分割索引鍵會包含在實體類型的 PK 中，用來改善某些查詢的效能。</span><span class="sxs-lookup"><span data-stu-id="a3e80-206">Starting with preview 7, the partition key is included in the entity type's PK and is used to improved performance in some queries.</span></span>

<span data-ttu-id="a3e80-207">檔是由問題 [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-207">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="cosmos-configuration"></a><span data-ttu-id="a3e80-208">Cosmos 設定</span><span class="sxs-lookup"><span data-stu-id="a3e80-208">Cosmos configuration</span></span>

<span data-ttu-id="a3e80-209">EF Core 5.0 可改善 Cosmos 和 Cosmos 連接的設定。</span><span class="sxs-lookup"><span data-stu-id="a3e80-209">EF Core 5.0 improves configuration of Cosmos and Cosmos connections.</span></span>

<span data-ttu-id="a3e80-210">先前，EF Core 需要在連接至 Cosmos 資料庫時明確指定端點和金鑰。</span><span class="sxs-lookup"><span data-stu-id="a3e80-210">Previously, EF Core required the end-point and key to be specified explicitly when connecting to a Cosmos database.</span></span> <span data-ttu-id="a3e80-211">EF Core 5.0 允許改用連接字串。</span><span class="sxs-lookup"><span data-stu-id="a3e80-211">EF Core 5.0 allows use of a connection string instead.</span></span> <span data-ttu-id="a3e80-212">此外，EF Core 5.0 允許明確設定 WebProxy 實例。</span><span class="sxs-lookup"><span data-stu-id="a3e80-212">In addition, EF Core 5.0 allows the WebProxy instance to be explicitly set.</span></span> <span data-ttu-id="a3e80-213">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-213">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.WebProxy(myProxyInstance);
            });
```

<span data-ttu-id="a3e80-214">現在也可以設定許多其他的超時值、限制等等。</span><span class="sxs-lookup"><span data-stu-id="a3e80-214">Many other timeout values, limits, etc. can now also be configured.</span></span> <span data-ttu-id="a3e80-215">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-215">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.LimitToEndpoint();
                cosmosOptionsBuilder.RequestTimeout(requestTimeout);
                cosmosOptionsBuilder.OpenTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.IdleTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.GatewayModeMaxConnectionLimit(connectionLimit);
                cosmosOptionsBuilder.MaxTcpConnectionsPerEndpoint(connectionLimit);
                cosmosOptionsBuilder.MaxRequestsPerTcpConnection(requestLimit);
            });
```

<span data-ttu-id="a3e80-216">最後，預設的連接模式現在 `ConnectionMode.Gateway` 更相容。</span><span class="sxs-lookup"><span data-stu-id="a3e80-216">Finally, the default connection mode is now `ConnectionMode.Gateway`, which is generally more compatible.</span></span>

<span data-ttu-id="a3e80-217">檔是由問題 [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-217">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="scaffold-dbcontext-now-singularizes"></a><span data-ttu-id="a3e80-218">Scaffold-DbCoNtext now singularizes</span><span class="sxs-lookup"><span data-stu-id="a3e80-218">Scaffold-DbContext now singularizes</span></span>

<span data-ttu-id="a3e80-219">先前當從現有的資料庫 DbCoNtext 樣板時，EF Core 將會建立符合資料庫中資料表名稱的實體類型名稱。</span><span class="sxs-lookup"><span data-stu-id="a3e80-219">Previously when scaffolding a DbContext from an existing database, EF Core will create entity type names that match the table names in the database.</span></span> <span data-ttu-id="a3e80-220">例如，資料表 `People` 並 `Addresses` 產生名為和的實體 `People` 類型 `Addresses` 。</span><span class="sxs-lookup"><span data-stu-id="a3e80-220">For example, tables `People` and `Addresses` resulted in entity types named `People` and `Addresses`.</span></span>

<span data-ttu-id="a3e80-221">在先前的版本中，這項行為可透過註冊複數表示服務來設定。</span><span class="sxs-lookup"><span data-stu-id="a3e80-221">In previous releases, this behavior was configurable through registration of a pluralization service.</span></span> <span data-ttu-id="a3e80-222">現在在 EF Core 5.0 中，會使用 [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) 套件作為預設複數表示服務。</span><span class="sxs-lookup"><span data-stu-id="a3e80-222">Now in EF Core 5.0, the [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) package is used as a default pluralization service.</span></span> <span data-ttu-id="a3e80-223">這表示資料表 `People` 和 `Addresses` 現在會對名為和的實體類型進行反向工程 `Person` `Address` 。</span><span class="sxs-lookup"><span data-stu-id="a3e80-223">This means tables `People` and `Addresses` will now be reverse engineered to entity types named `Person` and `Address`.</span></span>

### <a name="savepoints"></a><span data-ttu-id="a3e80-224">點</span><span class="sxs-lookup"><span data-stu-id="a3e80-224">Savepoints</span></span>

<span data-ttu-id="a3e80-225">EF Core 現在支援儲存 [點](/SQL/t-sql/language-elements/save-transaction-transact-sql?view=sql-server-ver15#remarks) ，以便更充分掌控執行多個作業的交易。</span><span class="sxs-lookup"><span data-stu-id="a3e80-225">EF Core now supports [savepoints](/SQL/t-sql/language-elements/save-transaction-transact-sql?view=sql-server-ver15#remarks) for greater control over transactions that execute multiple operations.</span></span>

<span data-ttu-id="a3e80-226">您可以手動建立、釋放和復原儲存點。</span><span class="sxs-lookup"><span data-stu-id="a3e80-226">Savepoints can be manually created, released, and rolled back.</span></span> <span data-ttu-id="a3e80-227">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-227">For example:</span></span>

```csharp
context.Database.CreateSavepoint("MySavePoint");
```

<span data-ttu-id="a3e80-228">此外，EF Core 現在會在執行失敗時復原到最後一個儲存點 `SaveChanges` 。</span><span class="sxs-lookup"><span data-stu-id="a3e80-228">In addition, EF Core will now roll back to the last savepoint when executing `SaveChanges` fails.</span></span> <span data-ttu-id="a3e80-229">這可讓 SaveChanges 重新嘗試，而不需要重新嘗試整個交易。</span><span class="sxs-lookup"><span data-stu-id="a3e80-229">This allows SaveChanges to be re-tried without re-trying the entire transaction.</span></span>

<span data-ttu-id="a3e80-230">檔是由問題 [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-230">Documentation is tracked by issue [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span></span>

## <a name="preview-6"></a><span data-ttu-id="a3e80-231">Preview 6</span><span class="sxs-lookup"><span data-stu-id="a3e80-231">Preview 6</span></span>

### <a name="split-queries-for-related-collections"></a><span data-ttu-id="a3e80-232">分割相關集合的查詢</span><span class="sxs-lookup"><span data-stu-id="a3e80-232">Split queries for related collections</span></span>

<span data-ttu-id="a3e80-233">從 EF Core 3.0 開始，EF Core 一律會為每個 LINQ 查詢產生單一 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="a3e80-233">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="a3e80-234">這樣可確保在使用中交易模式的條件約束內所傳回的資料一致。</span><span class="sxs-lookup"><span data-stu-id="a3e80-234">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="a3e80-235">不過，當查詢使用 `Include` 或投射回多個相關的集合時，這可能會變得很慢。</span><span class="sxs-lookup"><span data-stu-id="a3e80-235">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="a3e80-236">EF Core 5.0 現在允許單一 LINQ 查詢，包括要分割成多個 SQL 查詢的相關集合。</span><span class="sxs-lookup"><span data-stu-id="a3e80-236">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="a3e80-237">這可以大幅提升效能，但如果兩個查詢之間的資料有所變更，則可能導致傳回的結果不一致。</span><span class="sxs-lookup"><span data-stu-id="a3e80-237">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="a3e80-238">可序列化或快照集交易可用來降低此問題，並與分割查詢達成一致性，但這可能會帶來其他效能成本和行為差異。</span><span class="sxs-lookup"><span data-stu-id="a3e80-238">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

#### <a name="split-queries-with-include"></a><span data-ttu-id="a3e80-239">使用 Include 分割查詢</span><span class="sxs-lookup"><span data-stu-id="a3e80-239">Split queries with Include</span></span>

<span data-ttu-id="a3e80-240">例如，假設有一個查詢會使用下列程式提取兩個層級的相關集合 `Include` ：</span><span class="sxs-lookup"><span data-stu-id="a3e80-240">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```CSharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="a3e80-241">根據預設，EF Core 會在使用 SQLite 提供者時產生下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="a3e80-241">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name", "t0"."Id", "t0"."ArtistId", "t0"."Title", "t0"."Id0", "t0"."AlbumId", "t0"."Name"
FROM "Artists" AS "a"
LEFT JOIN (
    SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "t"."Id" AS "Id0", "t"."AlbumId", "t"."Name"
    FROM "Album" AS "a0"
    LEFT JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
) AS "t0" ON "a"."Id" = "t0"."ArtistId"
ORDER BY "a"."Id", "t0"."Id", "t0"."Id0"
```

<span data-ttu-id="a3e80-242">新的 `AsSplitQuery` API 可以用來變更此行為。</span><span class="sxs-lookup"><span data-stu-id="a3e80-242">The new `AsSplitQuery` API can be used to change this behavior.</span></span> <span data-ttu-id="a3e80-243">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-243">For example:</span></span>

```CSharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="a3e80-244">AsSplitQuery 適用于所有關系資料庫提供者，而且可以在查詢中的任何位置使用，就像 AsNoTracking 一樣。</span><span class="sxs-lookup"><span data-stu-id="a3e80-244">AsSplitQuery is available for all relational database providers and can be used anywhere in the query, just like AsNoTracking.</span></span> <span data-ttu-id="a3e80-245">EF Core 現在會產生下列三個 SQL 查詢：</span><span class="sxs-lookup"><span data-stu-id="a3e80-245">EF Core will now generate the following three SQL queries:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id", "a0"."Id"

SELECT "t"."Id", "t"."AlbumId", "t"."Name", "a"."Id", "a0"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
INNER JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
ORDER BY "a"."Id", "a0"."Id"
```

<span data-ttu-id="a3e80-246">支援查詢根目錄上的所有作業。</span><span class="sxs-lookup"><span data-stu-id="a3e80-246">All operations on the query root are supported.</span></span> <span data-ttu-id="a3e80-247">這包括 OrderBy/Skip/Take、聯結作業、FirstOrDefault，以及選取作業的類似單一結果。</span><span class="sxs-lookup"><span data-stu-id="a3e80-247">This includes OrderBy/Skip/Take, Join operations, FirstOrDefault and similar single result selecting operations.</span></span>

<span data-ttu-id="a3e80-248">請注意，preview 6 中不支援 OrderBy/Skip/Take 的篩選包含，但在每日組建中都有提供，而且將包含在 preview 7 中。</span><span class="sxs-lookup"><span data-stu-id="a3e80-248">Note that filtered Includes with OrderBy/Skip/Take are not supported in preview 6, but are available in the daily builds and will be included in preview 7.</span></span>

#### <a name="split-queries-with-collection-projections"></a><span data-ttu-id="a3e80-249">使用集合投影來分割查詢</span><span class="sxs-lookup"><span data-stu-id="a3e80-249">Split queries with collection projections</span></span>

<span data-ttu-id="a3e80-250">`AsSplitQuery` 當集合在投影中載入時，也可以使用。</span><span class="sxs-lookup"><span data-stu-id="a3e80-250">`AsSplitQuery` can also be used when collections are loaded in projections.</span></span> <span data-ttu-id="a3e80-251">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-251">For example:</span></span>

```CSharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

<span data-ttu-id="a3e80-252">使用 SQLite 提供者時，這個 LINQ 查詢會產生下列兩個 SQL 查詢：</span><span class="sxs-lookup"><span data-stu-id="a3e80-252">This LINQ query generates the following two SQL queries when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

<span data-ttu-id="a3e80-253">請注意，只支援集合的具體化。</span><span class="sxs-lookup"><span data-stu-id="a3e80-253">Note that only materialization of the collection is supported.</span></span> <span data-ttu-id="a3e80-254">`e.Albums`在上述情況下的任何組合都不會產生分割查詢。</span><span class="sxs-lookup"><span data-stu-id="a3e80-254">Any composition after `e.Albums` in above case won't result in a split query.</span></span> <span data-ttu-id="a3e80-255">[#21234](https://github.com/dotnet/efcore/issues/21234)會追蹤此區域的改進。</span><span class="sxs-lookup"><span data-stu-id="a3e80-255">Improvements in this area are tracked by [#21234](https://github.com/dotnet/efcore/issues/21234).</span></span>

### <a name="indexattribute"></a><span data-ttu-id="a3e80-256">IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="a3e80-256">IndexAttribute</span></span>

<span data-ttu-id="a3e80-257">新的 IndexAttribute 可以放在實體類型上，以指定單一資料行的索引。</span><span class="sxs-lookup"><span data-stu-id="a3e80-257">The new IndexAttribute can be placed on an entity type to specify an index for a single column.</span></span> <span data-ttu-id="a3e80-258">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-258">For example:</span></span>

```CSharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(128)]
    public string FullName { get; set; }
}
```

<span data-ttu-id="a3e80-259">針對 SQL Server，遷移將會產生下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="a3e80-259">For SQL Server, Migrations will then generate the following SQL:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

<span data-ttu-id="a3e80-260">IndexAttribute 也可用來指定跨越多個資料行的索引。</span><span class="sxs-lookup"><span data-stu-id="a3e80-260">IndexAttribute can also be used to specify an index spanning multiple columns.</span></span> <span data-ttu-id="a3e80-261">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-261">For example:</span></span>

```CSharp
[Index(nameof(FirstName), nameof(LastName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(64)]
    public string FirstName { get; set; }

    [MaxLength(64)]
    public string LastName { get; set; }
}
```

<span data-ttu-id="a3e80-262">針對 SQL Server，這會導致：</span><span class="sxs-lookup"><span data-stu-id="a3e80-262">For SQL Server, this results in:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

<span data-ttu-id="a3e80-263">檔是由問題 [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-263">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="improved-query-translation-exceptions"></a><span data-ttu-id="a3e80-264">改進的查詢轉譯例外狀況</span><span class="sxs-lookup"><span data-stu-id="a3e80-264">Improved query translation exceptions</span></span>

<span data-ttu-id="a3e80-265">我們會持續改善查詢轉譯失敗時所產生的例外狀況訊息。</span><span class="sxs-lookup"><span data-stu-id="a3e80-265">We are continuing to improve the exception messages generated when query translation fails.</span></span> <span data-ttu-id="a3e80-266">例如，此查詢會使用未對應的屬性 `IsSigned` ：</span><span class="sxs-lookup"><span data-stu-id="a3e80-266">For example, this query uses the unmapped property `IsSigned`:</span></span>

```CSharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

<span data-ttu-id="a3e80-267">EF Core 會擲回下列例外狀況，表示轉譯失敗，因為未 `IsSigned` 對應：</span><span class="sxs-lookup"><span data-stu-id="a3e80-267">EF Core will throw the following exception indicating that translation failed because `IsSigned` is not mapped:</span></span>

> <span data-ttu-id="a3e80-268">未處理的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="a3e80-268">Unhandled exception.</span></span> <span data-ttu-id="a3e80-269">InvalidOperationException： LINQ 運算式 ' DbSet <Artist> ( # A2。無法轉譯 (a =>. IsSigned) '。</span><span class="sxs-lookup"><span data-stu-id="a3e80-269">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.IsSigned)' could not be translated.</span></span> <span data-ttu-id="a3e80-270">其他資訊：在實體類型 ' 演出者 ' 上轉譯成員 ' IsSigned ' 失敗。</span><span class="sxs-lookup"><span data-stu-id="a3e80-270">Additional information: Translation of member 'IsSigned' on entity type 'Artist' failed.</span></span> <span data-ttu-id="a3e80-271">可能是指定的成員未對應。</span><span class="sxs-lookup"><span data-stu-id="a3e80-271">Possibly the specified member is not mapped.</span></span> <span data-ttu-id="a3e80-272">請在可轉譯的表單中重寫查詢，或插入對 Enumerable.asenumerable ( # A1、AsAsyncEnumerable ( # A3、ToList ( # A5 或 ToListAsync ( # A7 的呼叫，以明確地切換至用戶端評估。</span><span class="sxs-lookup"><span data-stu-id="a3e80-272">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="a3e80-273">如需相關資訊，請參閱 https://go.microsoft.com/fwlink/?linkid=2101038 。</span><span class="sxs-lookup"><span data-stu-id="a3e80-273">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

<span data-ttu-id="a3e80-274">同樣地，當您嘗試翻譯具有文化特性相依語義的字串比較時，現在會產生更好的例外狀況訊息。</span><span class="sxs-lookup"><span data-stu-id="a3e80-274">Similarly, better exception messages are now generated when attempting to translate string comparisons with culture-dependent semantics.</span></span> <span data-ttu-id="a3e80-275">例如，此查詢嘗試使用 `StringComparison.CurrentCulture` ：</span><span class="sxs-lookup"><span data-stu-id="a3e80-275">For example, this query attempts to use `StringComparison.CurrentCulture`:</span></span>

```CSharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

<span data-ttu-id="a3e80-276">EF Core 現在會擲回下列例外狀況：</span><span class="sxs-lookup"><span data-stu-id="a3e80-276">EF Core will now throw the following exception:</span></span>

> <span data-ttu-id="a3e80-277">未處理的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="a3e80-277">Unhandled exception.</span></span> <span data-ttu-id="a3e80-278">InvalidOperationException： LINQ 運算式 ' DbSet <Artist> ( # A2。其中 (a => a. Equals ( 值： "獨角獸"，comparisonType： CurrentCulture) # A6 ' 無法轉譯。</span><span class="sxs-lookup"><span data-stu-id="a3e80-278">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.Name.Equals( value: "The Unicorns", comparisonType: CurrentCulture))' could not be translated.</span></span> <span data-ttu-id="a3e80-279">其他資訊： ' string ' 的翻譯。不支援採用 ' StringComparison ' 引數的 Equals ' 方法。</span><span class="sxs-lookup"><span data-stu-id="a3e80-279">Additional information: Translation of 'string.Equals' method which takes 'StringComparison' argument is not supported.</span></span> <span data-ttu-id="a3e80-280">如需相關資訊，請參閱 https://go.microsoft.com/fwlink/?linkid=2129535 。</span><span class="sxs-lookup"><span data-stu-id="a3e80-280">See https://go.microsoft.com/fwlink/?linkid=2129535 for more information.</span></span> <span data-ttu-id="a3e80-281">請在可轉譯的表單中重寫查詢，或插入對 Enumerable.asenumerable ( # A1、AsAsyncEnumerable ( # A3、ToList ( # A5 或 ToListAsync ( # A7 的呼叫，以明確地切換至用戶端評估。</span><span class="sxs-lookup"><span data-stu-id="a3e80-281">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="a3e80-282">如需相關資訊，請參閱 https://go.microsoft.com/fwlink/?linkid=2101038 。</span><span class="sxs-lookup"><span data-stu-id="a3e80-282">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

### <a name="specify-transaction-id"></a><span data-ttu-id="a3e80-283">指定交易識別碼</span><span class="sxs-lookup"><span data-stu-id="a3e80-283">Specify transaction ID</span></span>

<span data-ttu-id="a3e80-284">這項功能是由「社區」所貢獻 [@Marusyk](https://github.com/Marusyk) 。</span><span class="sxs-lookup"><span data-stu-id="a3e80-284">This feature was contributed from the community by [@Marusyk](https://github.com/Marusyk).</span></span> <span data-ttu-id="a3e80-285">許多人都感謝您的貢獻！</span><span class="sxs-lookup"><span data-stu-id="a3e80-285">Many thanks for the contribution!</span></span>

<span data-ttu-id="a3e80-286">EF Core 公開交易的交易識別碼，以便跨呼叫進行交易的相互關聯。</span><span class="sxs-lookup"><span data-stu-id="a3e80-286">EF Core exposes a transaction ID for correlation of transactions across calls.</span></span> <span data-ttu-id="a3e80-287">此識別碼通常會在交易開始時由 EF Core 設定。</span><span class="sxs-lookup"><span data-stu-id="a3e80-287">This ID typically set by EF Core when a transaction is started.</span></span> <span data-ttu-id="a3e80-288">如果應用程式改為啟動交易，則這項功能可讓應用程式明確地設定交易識別碼，使其在使用它的任何地方都能正確地相互關聯。</span><span class="sxs-lookup"><span data-stu-id="a3e80-288">If the application starts the transaction instead, then this feature allows the application to explicitly set the transaction ID so it is correlated correctly everywhere it is used.</span></span> <span data-ttu-id="a3e80-289">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-289">For example:</span></span>

```CSharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a><span data-ttu-id="a3e80-290">IPAddress 對應</span><span class="sxs-lookup"><span data-stu-id="a3e80-290">IPAddress mapping</span></span>

<span data-ttu-id="a3e80-291">這項功能是由「社區」所貢獻 [@ralmsdeveloper](https://github.com/ralmsdeveloper) 。</span><span class="sxs-lookup"><span data-stu-id="a3e80-291">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="a3e80-292">許多人都感謝您的貢獻！</span><span class="sxs-lookup"><span data-stu-id="a3e80-292">Many thanks for the contribution!</span></span>

<span data-ttu-id="a3e80-293">標準 .NET [IPAddress 類別](/dotnet/api/system.net.ipaddress) 現在會自動對應至尚未具有原生支援之資料庫的字串資料行。</span><span class="sxs-lookup"><span data-stu-id="a3e80-293">The standard .NET [IPAddress class](/dotnet/api/system.net.ipaddress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="a3e80-294">例如，請考慮對應此實體類型：</span><span class="sxs-lookup"><span data-stu-id="a3e80-294">For example, consider mapping this entity type:</span></span>

```CSharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

<span data-ttu-id="a3e80-295">在 SQL Server 上，這會導致建立下表：</span><span class="sxs-lookup"><span data-stu-id="a3e80-295">On SQL Server, this will result in Migrations creating the following table:</span></span>

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
```

<span data-ttu-id="a3e80-296">然後可以用一般方式新增實體：</span><span class="sxs-lookup"><span data-stu-id="a3e80-296">Entities can then be added in the normal way:</span></span>

```CSharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
```

<span data-ttu-id="a3e80-297">而產生的 SQL 會插入正規化的 IPv4 或 IPv6 位址：</span><span class="sxs-lookup"><span data-stu-id="a3e80-297">And the resulting SQL will insert the normalized IPv4 or IPv6 address:</span></span>

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a><span data-ttu-id="a3e80-298">當樣板時排除 OnConfiguring</span><span class="sxs-lookup"><span data-stu-id="a3e80-298">Exclude OnConfiguring when scaffolding</span></span>

<span data-ttu-id="a3e80-299">從現有的資料庫 scaffold DbCoNtext 時，EF Core 預設會使用連接字串建立 OnConfiguring 多載，讓內容可以立即使用。</span><span class="sxs-lookup"><span data-stu-id="a3e80-299">When a DbContext is scaffolded from an existing database, EF Core by default creates an OnConfiguring overload with a connection string so that the context is immediately usable.</span></span> <span data-ttu-id="a3e80-300">但是，如果您已經有部分類別具有 OnConfiguring，或如果您以其他方式設定內容，這就不會很有用。</span><span class="sxs-lookup"><span data-stu-id="a3e80-300">However, this is not useful if you already have a partial class with OnConfiguring, or if you are configuring the context some other way.</span></span>

<span data-ttu-id="a3e80-301">若要解決此情況，現在可以指示可忽略 OnConfiguring 產生的基本程式命令。</span><span class="sxs-lookup"><span data-stu-id="a3e80-301">To address this, the scaffolding commands can now be instructed to omit generation of OnConfiguring.</span></span> <span data-ttu-id="a3e80-302">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-302">For example:</span></span>

```
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

<span data-ttu-id="a3e80-303">或者，在封裝管理員主控台中：</span><span class="sxs-lookup"><span data-stu-id="a3e80-303">Or in the Package Manager Console:</span></span>

```
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring
```

<span data-ttu-id="a3e80-304">請注意，我們建議使用 [已命名的連接字串和安全的儲存體，例如使用者密碼](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets)。</span><span class="sxs-lookup"><span data-stu-id="a3e80-304">Note that we recommend using [a named connection string and secure storage like User Secrets](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets).</span></span>

### <a name="translations-for-firstordefault-on-strings"></a><span data-ttu-id="a3e80-305">字串的 FirstOrDefault 翻譯</span><span class="sxs-lookup"><span data-stu-id="a3e80-305">Translations for FirstOrDefault on strings</span></span>

<span data-ttu-id="a3e80-306">這項功能是由「社區」所貢獻 [@dvoreckyaa](https://github.com/dvoreckyaa) 。</span><span class="sxs-lookup"><span data-stu-id="a3e80-306">This feature was contributed from the community by [@dvoreckyaa](https://github.com/dvoreckyaa).</span></span> <span data-ttu-id="a3e80-307">許多人都感謝您的貢獻！</span><span class="sxs-lookup"><span data-stu-id="a3e80-307">Many thanks for the contribution!</span></span>

<span data-ttu-id="a3e80-308">字串中字元的 FirstOrDefault 和類似運算子現在已轉譯。</span><span class="sxs-lookup"><span data-stu-id="a3e80-308">FirstOrDefault and similar operators for characters in strings are now translated.</span></span> <span data-ttu-id="a3e80-309">例如，這個 LINQ 查詢：</span><span class="sxs-lookup"><span data-stu-id="a3e80-309">For example, this LINQ query:</span></span>

```CSharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

<span data-ttu-id="a3e80-310">使用 SQL Server 時，將會轉譯為下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="a3e80-310">Will be translated to the following SQL when using SQL Server:</span></span>

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a><span data-ttu-id="a3e80-311">簡化案例區塊</span><span class="sxs-lookup"><span data-stu-id="a3e80-311">Simplify case blocks</span></span>

<span data-ttu-id="a3e80-312">EF Core 現在會以案例區塊產生更好的查詢。</span><span class="sxs-lookup"><span data-stu-id="a3e80-312">EF Core now generates better queries with CASE blocks.</span></span> <span data-ttu-id="a3e80-313">例如，這個 LINQ 查詢：</span><span class="sxs-lookup"><span data-stu-id="a3e80-313">For example, this LINQ query:</span></span>

```CSharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

<span data-ttu-id="a3e80-314">Was SQL Server 正式轉譯為：</span><span class="sxs-lookup"><span data-stu-id="a3e80-314">Was on SQL Server formally translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN (CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END = 0) AND CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

<span data-ttu-id="a3e80-315">但現在已轉譯為：</span><span class="sxs-lookup"><span data-stu-id="a3e80-315">But is now translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

## <a name="preview-5"></a><span data-ttu-id="a3e80-316">Preview 5</span><span class="sxs-lookup"><span data-stu-id="a3e80-316">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="a3e80-317">資料庫定序</span><span class="sxs-lookup"><span data-stu-id="a3e80-317">Database collations</span></span>

<span data-ttu-id="a3e80-318">您現在可以在 EF 模型中指定資料庫的預設定序。</span><span class="sxs-lookup"><span data-stu-id="a3e80-318">The default collation for a database can now be specified in the EF model.</span></span> <span data-ttu-id="a3e80-319">這會流經產生的遷移，以在建立資料庫時設定定序。</span><span class="sxs-lookup"><span data-stu-id="a3e80-319">This will flow through to generated migrations to set the collation when the database is created.</span></span> <span data-ttu-id="a3e80-320">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-320">For example:</span></span>

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="a3e80-321">然後，遷移會產生下列各項，以在 SQL Server 上建立資料庫：</span><span class="sxs-lookup"><span data-stu-id="a3e80-321">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="a3e80-322">您也可以指定要用於特定資料庫資料行的定序。</span><span class="sxs-lookup"><span data-stu-id="a3e80-322">The collation to use for specific database columns can also be specified.</span></span> <span data-ttu-id="a3e80-323">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-323">For example:</span></span>

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="a3e80-324">若未使用遷移，則在 DbCoNtext 架構時，定序現在會從資料庫進行反向工程。</span><span class="sxs-lookup"><span data-stu-id="a3e80-324">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="a3e80-325">最後， `EF.Functions.Collate()` 可以使用不同的定序來進行特定查詢。</span><span class="sxs-lookup"><span data-stu-id="a3e80-325">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span> <span data-ttu-id="a3e80-326">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-326">For example:</span></span>

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="a3e80-327">這會產生 SQL Server 的下列查詢：</span><span class="sxs-lookup"><span data-stu-id="a3e80-327">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="a3e80-328">請注意，特定定序應謹慎使用，因為它們可能會對資料庫效能造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="a3e80-328">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="a3e80-329">檔是由問題 [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-329">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="a3e80-330">IDesignTimeDbCoNtextFactory 的流程引數</span><span class="sxs-lookup"><span data-stu-id="a3e80-330">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="a3e80-331">引數現在會從命令列流向 `CreateDbContext` [IDesignTimeDbCoNtextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1)方法。</span><span class="sxs-lookup"><span data-stu-id="a3e80-331">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span></span> <span data-ttu-id="a3e80-332">例如，若要表示這是開發組建，您 `dev` 可以在命令列上傳遞自訂引數 (例如) ：</span><span class="sxs-lookup"><span data-stu-id="a3e80-332">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can be passed on the command line:</span></span>

```
dotnet ef migrations add two --verbose --dev
```

<span data-ttu-id="a3e80-333">然後，這個引數會流入處理站，可用來控制內容的建立與初始化方式。</span><span class="sxs-lookup"><span data-stu-id="a3e80-333">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span> <span data-ttu-id="a3e80-334">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-334">For example:</span></span>

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args)
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="a3e80-335">檔是由問題 [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-335">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="a3e80-336">沒有識別解析的追蹤查詢</span><span class="sxs-lookup"><span data-stu-id="a3e80-336">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="a3e80-337">現在可以設定無追蹤查詢來執行識別解析。</span><span class="sxs-lookup"><span data-stu-id="a3e80-337">No-tracking queries can now be configured to perform identity resolution.</span></span> <span data-ttu-id="a3e80-338">例如，下列查詢會為每個貼文建立新的 Blog 實例，即使每個 Blog 都有相同的主要金鑰。</span><span class="sxs-lookup"><span data-stu-id="a3e80-338">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span>

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="a3e80-339">不過，代價通常會稍微慢一點，且一律使用更多記憶體，因此可以變更此查詢，以確保只會建立單一的 Blog 實例：</span><span class="sxs-lookup"><span data-stu-id="a3e80-339">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="a3e80-340">請注意，這只適用于無追蹤查詢，因為所有追蹤查詢都已展示此行為。</span><span class="sxs-lookup"><span data-stu-id="a3e80-340">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="a3e80-341">此外，在遵循 API 審核之後，將會 `PerformIdentityResolution` 變更語法。</span><span class="sxs-lookup"><span data-stu-id="a3e80-341">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span> <span data-ttu-id="a3e80-342">請參閱 [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073)。</span><span class="sxs-lookup"><span data-stu-id="a3e80-342">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="a3e80-343">檔是由問題 [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-343">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="a3e80-344">儲存 (保存) 計算資料行</span><span class="sxs-lookup"><span data-stu-id="a3e80-344">Stored (persisted) computed columns</span></span>

<span data-ttu-id="a3e80-345">大部分的資料庫允許在計算後儲存計算資料行值。</span><span class="sxs-lookup"><span data-stu-id="a3e80-345">Most databases allow computed column values to be stored after computation.</span></span> <span data-ttu-id="a3e80-346">雖然這會佔用磁碟空間，但計算資料行只會在更新時計算一次，而不是每次抓取其值時。</span><span class="sxs-lookup"><span data-stu-id="a3e80-346">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span> <span data-ttu-id="a3e80-347">這也可讓某些資料庫的資料行編制索引。</span><span class="sxs-lookup"><span data-stu-id="a3e80-347">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="a3e80-348">EF Core 5.0 允許將計算資料行設定為已儲存。</span><span class="sxs-lookup"><span data-stu-id="a3e80-348">EF Core 5.0 allows computed columns to be configured as stored.</span></span> <span data-ttu-id="a3e80-349">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-349">For example:</span></span>

```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="a3e80-350">SQLite 計算資料行</span><span class="sxs-lookup"><span data-stu-id="a3e80-350">SQLite computed columns</span></span>

<span data-ttu-id="a3e80-351">EF Core 現在支援 SQLite 資料庫中的計算資料行。</span><span class="sxs-lookup"><span data-stu-id="a3e80-351">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="a3e80-352">Preview 4</span><span class="sxs-lookup"><span data-stu-id="a3e80-352">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="a3e80-353">在模型中設定資料庫 precision/scale</span><span class="sxs-lookup"><span data-stu-id="a3e80-353">Configure database precision/scale in model</span></span>

<span data-ttu-id="a3e80-354">您現在可以使用模型產生器來指定屬性的有效位數和小數位數。</span><span class="sxs-lookup"><span data-stu-id="a3e80-354">Precision and scale for a property can now be specified using the model builder.</span></span> <span data-ttu-id="a3e80-355">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-355">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="a3e80-356">精確度和小數位數仍然可以透過完整的資料庫類型來設定，例如 "decimal (16，4) "。</span><span class="sxs-lookup"><span data-stu-id="a3e80-356">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span>

<span data-ttu-id="a3e80-357">檔是由問題 [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-357">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="a3e80-358">指定 SQL Server 索引填滿因數</span><span class="sxs-lookup"><span data-stu-id="a3e80-358">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="a3e80-359">您現在可以在 SQL Server 上建立索引時指定填滿因數。</span><span class="sxs-lookup"><span data-stu-id="a3e80-359">The fill factor can now be specified when creating an index on SQL Server.</span></span> <span data-ttu-id="a3e80-360">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-360">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="a3e80-361">Preview 3</span><span class="sxs-lookup"><span data-stu-id="a3e80-361">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="a3e80-362">篩選的包含</span><span class="sxs-lookup"><span data-stu-id="a3e80-362">Filtered Include</span></span>

<span data-ttu-id="a3e80-363">Include 方法現在支援篩選包含的實體。</span><span class="sxs-lookup"><span data-stu-id="a3e80-363">The Include method now supports filtering of the entities included.</span></span> <span data-ttu-id="a3e80-364">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-364">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="a3e80-365">此查詢將會連同每個相關聯的貼文傳回 blog，但只有在貼文標題包含「乳酪」時。</span><span class="sxs-lookup"><span data-stu-id="a3e80-365">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="a3e80-366">Skip 和 Take 也可以用來減少包含的實體數目。</span><span class="sxs-lookup"><span data-stu-id="a3e80-366">Skip and Take can also be used to reduce the number of included entities.</span></span> <span data-ttu-id="a3e80-367">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-367">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="a3e80-368">此查詢會傳回包含每個 blog 最多五篇文章的 blog。</span><span class="sxs-lookup"><span data-stu-id="a3e80-368">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="a3e80-369">如需完整詳細資料，請參閱 [包含檔](xref:core/querying/related-data#filtered-include) 。</span><span class="sxs-lookup"><span data-stu-id="a3e80-369">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="a3e80-370">適用于導覽屬性的新 ModelBuilder API</span><span class="sxs-lookup"><span data-stu-id="a3e80-370">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="a3e80-371">導覽屬性主要是在 [定義關聯](xref:core/modeling/relationships)性時進行設定。</span><span class="sxs-lookup"><span data-stu-id="a3e80-371">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span> <span data-ttu-id="a3e80-372">不過，在 `Navigation` 導覽屬性需要額外設定的情況下，可以使用新的方法。</span><span class="sxs-lookup"><span data-stu-id="a3e80-372">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span> <span data-ttu-id="a3e80-373">例如，當依據慣例找不到欄位時，設定導覽的支援欄位：</span><span class="sxs-lookup"><span data-stu-id="a3e80-373">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="a3e80-374">請注意， `Navigation` API 不會取代關聯性設定。</span><span class="sxs-lookup"><span data-stu-id="a3e80-374">Note that the `Navigation` API does not replace relationship configuration.</span></span> <span data-ttu-id="a3e80-375">相反地，它允許在已探索或已定義的關聯性中，進行其他的導覽屬性設定。</span><span class="sxs-lookup"><span data-stu-id="a3e80-375">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="a3e80-376">請參閱設定 [導覽屬性檔](xref:core/modeling/relationships#configuring-navigation-properties)。</span><span class="sxs-lookup"><span data-stu-id="a3e80-376">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="a3e80-377">命名空間和連接字串的新命令列參數</span><span class="sxs-lookup"><span data-stu-id="a3e80-377">New command-line parameters for namespaces and connection strings</span></span>

<span data-ttu-id="a3e80-378">現在可讓您在命令列上指定命名空間。</span><span class="sxs-lookup"><span data-stu-id="a3e80-378">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span> <span data-ttu-id="a3e80-379">例如，若要對資料庫進行反向工程，將內容和模型類別放在不同的命名空間中：</span><span class="sxs-lookup"><span data-stu-id="a3e80-379">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span>

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="a3e80-380">如需完整的詳細資料，請參閱「 [遷移](xref:core/managing-schemas/migrations/index#namespaces) 和 [反向工程](xref:core/managing-schemas/scaffolding#directories-and-namespaces) 」檔。</span><span class="sxs-lookup"><span data-stu-id="a3e80-380">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="a3e80-381">此外，現在可以將連接字串傳遞至 `database-update` 命令：</span><span class="sxs-lookup"><span data-stu-id="a3e80-381">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="a3e80-382">如需完整的詳細資訊，請參閱 [工具檔](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) 。</span><span class="sxs-lookup"><span data-stu-id="a3e80-382">See the [Tools documentation](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="a3e80-383">對應的參數也已加入至 VS 封裝管理員主控台中使用的 PowerShell 命令。</span><span class="sxs-lookup"><span data-stu-id="a3e80-383">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="a3e80-384">EnableDetailedErrors 已傳回</span><span class="sxs-lookup"><span data-stu-id="a3e80-384">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="a3e80-385">基於效能考慮，從資料庫讀取值時，EF 不會執行額外的 null 檢查。</span><span class="sxs-lookup"><span data-stu-id="a3e80-385">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span> <span data-ttu-id="a3e80-386">這可能會導致在遇到未預期的 null 時，難以造成根本原因的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="a3e80-386">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="a3e80-387">使用 `EnableDetailedErrors` 會將額外的 null 檢查新增至查詢，如此一來，若要降低效能，您可以更輕鬆地追蹤至根本原因。</span><span class="sxs-lookup"><span data-stu-id="a3e80-387">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>

<span data-ttu-id="a3e80-388">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-388">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="a3e80-389">檔是由問題 [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-389">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="a3e80-390">Cosmos 分割區索引鍵</span><span class="sxs-lookup"><span data-stu-id="a3e80-390">Cosmos partition keys</span></span>

<span data-ttu-id="a3e80-391">您現在可以在查詢中指定要用於指定之查詢的資料分割索引鍵。</span><span class="sxs-lookup"><span data-stu-id="a3e80-391">The partition key to use for a given query can now be specified in the query.</span></span> <span data-ttu-id="a3e80-392">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-392">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="a3e80-393">檔是由問題 [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-393">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="a3e80-394">支援 SQL Server DATALENGTH 函數</span><span class="sxs-lookup"><span data-stu-id="a3e80-394">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="a3e80-395">這可以使用新的方法來存取 `EF.Functions.DataLength` 。</span><span class="sxs-lookup"><span data-stu-id="a3e80-395">This can be accessed using the new `EF.Functions.DataLength` method.</span></span> <span data-ttu-id="a3e80-396">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-396">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
```

## <a name="preview-2"></a><span data-ttu-id="a3e80-397">Preview 2</span><span class="sxs-lookup"><span data-stu-id="a3e80-397">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="a3e80-398">使用 c # 屬性來指定屬性支援欄位</span><span class="sxs-lookup"><span data-stu-id="a3e80-398">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="a3e80-399">C # 屬性現在可用來指定屬性的支援欄位。</span><span class="sxs-lookup"><span data-stu-id="a3e80-399">A C# attribute can now be used to specify the backing field for a property.</span></span> <span data-ttu-id="a3e80-400">即使無法自動找到支援欄位，這個屬性仍可讓 EF Core 繼續寫入和讀取支援欄位。</span><span class="sxs-lookup"><span data-stu-id="a3e80-400">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span> <span data-ttu-id="a3e80-401">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-401">For example:</span></span>

```CSharp
public class Blog
{
    private string _mainTitle;

    public int Id { get; set; }

    [BackingField(nameof(_mainTitle))]
    public string Title
    {
        get => _mainTitle;
        set => _mainTitle = value;
    }
}
```

<span data-ttu-id="a3e80-402">檔是由問題 [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-402">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="a3e80-403">完成鑒別子對應</span><span class="sxs-lookup"><span data-stu-id="a3e80-403">Complete discriminator mapping</span></span>

<span data-ttu-id="a3e80-404">EF Core 使用鑒別子資料行來進行 [繼承階層的 TPH 對應](/ef/core/modeling/inheritance)。</span><span class="sxs-lookup"><span data-stu-id="a3e80-404">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span> <span data-ttu-id="a3e80-405">只要 EF Core 知道鑒別子的所有可能值，就可能會有一些效能增強功能。</span><span class="sxs-lookup"><span data-stu-id="a3e80-405">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span> <span data-ttu-id="a3e80-406">EF Core 5.0 現在會實行這些增強功能。</span><span class="sxs-lookup"><span data-stu-id="a3e80-406">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="a3e80-407">例如，舊版的 EF Core 一律會為傳回階層中所有類型的查詢產生此 SQL：</span><span class="sxs-lookup"><span data-stu-id="a3e80-407">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="a3e80-408">當設定完整的鑒別子對應時，EF Core 5.0 現在會產生下列內容：</span><span class="sxs-lookup"><span data-stu-id="a3e80-408">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="a3e80-409">這會是從 preview 3 開始的預設行為。</span><span class="sxs-lookup"><span data-stu-id="a3e80-409">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="a3e80-410">Microsoft 的效能改進</span><span class="sxs-lookup"><span data-stu-id="a3e80-410">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="a3e80-411">我們對 SQLIte 進行了兩項效能改進：</span><span class="sxs-lookup"><span data-stu-id="a3e80-411">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="a3e80-412">使用 SqliteBlob 和資料流程，以 GetBytes、GetChars 和 GetTextReader 來取得二進位和字串資料，現在更有效率。</span><span class="sxs-lookup"><span data-stu-id="a3e80-412">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="a3e80-413">>sqliteconnection 的初始化現在是延遲的。</span><span class="sxs-lookup"><span data-stu-id="a3e80-413">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="a3e80-414">這些改良功能位於 ADO.NET 的 Microsoft. Sqlite 提供者中，因此也可改善 EF Core 之外的效能。</span><span class="sxs-lookup"><span data-stu-id="a3e80-414">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="a3e80-415">Preview 1</span><span class="sxs-lookup"><span data-stu-id="a3e80-415">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="a3e80-416">簡單記錄</span><span class="sxs-lookup"><span data-stu-id="a3e80-416">Simple logging</span></span>

<span data-ttu-id="a3e80-417">這項功能 `Database.Log` 在 EF6 中新增了類似的功能。</span><span class="sxs-lookup"><span data-stu-id="a3e80-417">This feature adds functionality similar to `Database.Log` in EF6.</span></span> <span data-ttu-id="a3e80-418">也就是說，它會提供簡單的方法來取得 EF Core 的記錄，而不需要設定任何種類的外部記錄架構。</span><span class="sxs-lookup"><span data-stu-id="a3e80-418">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="a3e80-419">[2019 年12月5日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)包含初稿檔。</span><span class="sxs-lookup"><span data-stu-id="a3e80-419">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="a3e80-420">其他檔是由問題 [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-420">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="a3e80-421">取得所產生 SQL 的簡單方式</span><span class="sxs-lookup"><span data-stu-id="a3e80-421">Simple way to get generated SQL</span></span>

<span data-ttu-id="a3e80-422">EF Core 5.0 引進了 `ToQueryString` 擴充方法，此方法會傳回 EF Core 將在執行 LINQ 查詢時產生的 SQL。</span><span class="sxs-lookup"><span data-stu-id="a3e80-422">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="a3e80-423">[2020 年1月9日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)包含初稿檔。</span><span class="sxs-lookup"><span data-stu-id="a3e80-423">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="a3e80-424">其他檔是由問題 [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-424">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="a3e80-425">使用 c # 屬性工作表示實體沒有索引鍵</span><span class="sxs-lookup"><span data-stu-id="a3e80-425">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="a3e80-426">實體類型現在可以設定為沒有使用新的索引鍵 `KeylessAttribute` 。</span><span class="sxs-lookup"><span data-stu-id="a3e80-426">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span> <span data-ttu-id="a3e80-427">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-427">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="a3e80-428">檔是由問題 [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-428">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="a3e80-429">可以在初始化的 DbCoNtext 上變更連接或連接字串</span><span class="sxs-lookup"><span data-stu-id="a3e80-429">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="a3e80-430">現在，您可以更輕鬆地建立沒有任何連接或連接字串的 DbCoNtext 實例。</span><span class="sxs-lookup"><span data-stu-id="a3e80-430">It is now easier to create a DbContext instance without any connection or connection string.</span></span> <span data-ttu-id="a3e80-431">此外，連接或連接字串現在可以在內容實例上進行變化。</span><span class="sxs-lookup"><span data-stu-id="a3e80-431">Also, the connection or connection string can now be mutated on the context instance.</span></span> <span data-ttu-id="a3e80-432">這項功能可讓相同的內容實例動態連接到不同的資料庫。</span><span class="sxs-lookup"><span data-stu-id="a3e80-432">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="a3e80-433">檔是由問題 [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-433">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="a3e80-434">變更追蹤 proxy</span><span class="sxs-lookup"><span data-stu-id="a3e80-434">Change-tracking proxies</span></span>

<span data-ttu-id="a3e80-435">EF Core 現在可以產生自動執行 [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) 和 [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1)的執行時間 proxy。</span><span class="sxs-lookup"><span data-stu-id="a3e80-435">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span> <span data-ttu-id="a3e80-436">然後，這些會將實體屬性的值變更直接回報給 EF Core，以避免需要掃描變更。</span><span class="sxs-lookup"><span data-stu-id="a3e80-436">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span> <span data-ttu-id="a3e80-437">不過，proxy 有自己的一組限制，因此不適合所有人。</span><span class="sxs-lookup"><span data-stu-id="a3e80-437">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="a3e80-438">檔是由問題 [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-438">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="a3e80-439">增強的調試視圖</span><span class="sxs-lookup"><span data-stu-id="a3e80-439">Enhanced debug views</span></span>

<span data-ttu-id="a3e80-440">偵錯工具是一種簡單的方法，可讓您在偵測到問題時，查看 EF Core 的內部。</span><span class="sxs-lookup"><span data-stu-id="a3e80-440">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span> <span data-ttu-id="a3e80-441">模型的偵錯工具是在一段時間前執行。</span><span class="sxs-lookup"><span data-stu-id="a3e80-441">A debug view for the Model was implemented some time ago.</span></span> <span data-ttu-id="a3e80-442">針對 EF Core 5.0，我們讓模型視圖更容易讀取，並在狀態管理員中為追蹤的實體新增了新的偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="a3e80-442">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="a3e80-443">[2019 年12月12日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)包含初稿檔。</span><span class="sxs-lookup"><span data-stu-id="a3e80-443">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="a3e80-444">其他檔是由問題 [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-444">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="a3e80-445">改進資料庫 null 語義的處理</span><span class="sxs-lookup"><span data-stu-id="a3e80-445">Improved handling of database null semantics</span></span>

<span data-ttu-id="a3e80-446">關係資料庫通常會將 Null 視為未知值，因此不等於任何其他 Null。</span><span class="sxs-lookup"><span data-stu-id="a3e80-446">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span> <span data-ttu-id="a3e80-447">雖然 c # 會將 null 視為已定義的值，但會比較是否等於任何其他 null。</span><span class="sxs-lookup"><span data-stu-id="a3e80-447">While C# treats null as a defined value, which compares equal to any other null.</span></span> <span data-ttu-id="a3e80-448">EF Core 預設會轉譯查詢，使其使用 c # null 語義。</span><span class="sxs-lookup"><span data-stu-id="a3e80-448">EF Core by default translates queries so that they use C# null semantics.</span></span> <span data-ttu-id="a3e80-449">EF Core 5.0 可大幅提升這些翻譯的效率。</span><span class="sxs-lookup"><span data-stu-id="a3e80-449">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="a3e80-450">檔是由問題 [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-450">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="a3e80-451">索引子屬性</span><span class="sxs-lookup"><span data-stu-id="a3e80-451">Indexer properties</span></span>

<span data-ttu-id="a3e80-452">EF Core 5.0 支援 c # 索引子屬性的對應。</span><span class="sxs-lookup"><span data-stu-id="a3e80-452">EF Core 5.0 supports mapping of C# indexer properties.</span></span> <span data-ttu-id="a3e80-453">這些屬性可讓實體當做屬性包，其中的資料行會對應至包中的命名屬性。</span><span class="sxs-lookup"><span data-stu-id="a3e80-453">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="a3e80-454">檔是由問題 [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-454">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="a3e80-455">列舉對應的檢查條件約束產生</span><span class="sxs-lookup"><span data-stu-id="a3e80-455">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="a3e80-456">EF Core 5.0 遷移現在可以產生列舉屬性對應的檢查條件約束。</span><span class="sxs-lookup"><span data-stu-id="a3e80-456">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span> <span data-ttu-id="a3e80-457">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-457">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="a3e80-458">檔是由問題 [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-458">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="a3e80-459">IsRelational</span><span class="sxs-lookup"><span data-stu-id="a3e80-459">IsRelational</span></span>

<span data-ttu-id="a3e80-460">`IsRelational`除了現有的、和之外，還加入了新的方法 `IsSqlServer` `IsSqlite` `IsInMemory` 。</span><span class="sxs-lookup"><span data-stu-id="a3e80-460">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span> <span data-ttu-id="a3e80-461">這個方法可以用來測試 DbCoNtext 是否使用任何關係資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="a3e80-461">This method can be used to test if the DbContext is using any relational database provider.</span></span> <span data-ttu-id="a3e80-462">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-462">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="a3e80-463">檔是由問題 [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-463">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="a3e80-464">使用 Etag Cosmos 開放式平行存取</span><span class="sxs-lookup"><span data-stu-id="a3e80-464">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="a3e80-465">Azure Cosmos DB 資料庫提供者現在支援使用 Etag 的開放式平行存取。</span><span class="sxs-lookup"><span data-stu-id="a3e80-465">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span> <span data-ttu-id="a3e80-466">使用 OnModelCreating 中的模型產生器來設定 ETag：</span><span class="sxs-lookup"><span data-stu-id="a3e80-466">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="a3e80-467">然後 SaveChanges 會擲回 `DbUpdateConcurrencyException` 並行衝突，以進行 [處理](/ef/core/saving/concurrency) 以執行重試等。</span><span class="sxs-lookup"><span data-stu-id="a3e80-467">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="a3e80-468">檔是由問題 [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-468">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="a3e80-469">更多日期時間結構的查詢翻譯</span><span class="sxs-lookup"><span data-stu-id="a3e80-469">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="a3e80-470">現在已翻譯包含新日期時間結構的查詢。</span><span class="sxs-lookup"><span data-stu-id="a3e80-470">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="a3e80-471">此外，現在已對應下列 SQL Server 函式：</span><span class="sxs-lookup"><span data-stu-id="a3e80-471">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="a3e80-472">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="a3e80-472">DateDiffWeek</span></span>
* <span data-ttu-id="a3e80-473">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="a3e80-473">DateFromParts</span></span>

<span data-ttu-id="a3e80-474">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-474">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="a3e80-475">檔是由問題 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-475">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="a3e80-476">更多位元組陣列結構的查詢翻譯</span><span class="sxs-lookup"><span data-stu-id="a3e80-476">Query translations for more byte array constructs</span></span>

<span data-ttu-id="a3e80-477">使用 Contains、Length、Enumerable.sequenceequal 等的查詢現在會轉譯為 SQL。</span><span class="sxs-lookup"><span data-stu-id="a3e80-477">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="a3e80-478">[2019 年12月5日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)包含初稿檔。</span><span class="sxs-lookup"><span data-stu-id="a3e80-478">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="a3e80-479">其他檔是由問題 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-479">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="a3e80-480">反向的查詢轉譯</span><span class="sxs-lookup"><span data-stu-id="a3e80-480">Query translation for Reverse</span></span>

<span data-ttu-id="a3e80-481">`Reverse`現在會轉譯使用的查詢。</span><span class="sxs-lookup"><span data-stu-id="a3e80-481">Queries using `Reverse` are now translated.</span></span> <span data-ttu-id="a3e80-482">例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-482">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="a3e80-483">檔是由問題 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-483">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="a3e80-484">位運算子的查詢轉譯</span><span class="sxs-lookup"><span data-stu-id="a3e80-484">Query translation for bitwise operators</span></span>

<span data-ttu-id="a3e80-485">使用位運算子的查詢現在會在更多情況下轉譯，例如：</span><span class="sxs-lookup"><span data-stu-id="a3e80-485">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="a3e80-486">檔是由問題 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-486">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="a3e80-487">Cosmos 上字串的查詢轉譯</span><span class="sxs-lookup"><span data-stu-id="a3e80-487">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="a3e80-488">使用字串方法的查詢現在會在使用 Azure Cosmos DB 提供者時轉譯。</span><span class="sxs-lookup"><span data-stu-id="a3e80-488">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="a3e80-489">檔是由問題 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="a3e80-489">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
