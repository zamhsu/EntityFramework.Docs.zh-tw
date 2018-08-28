---
title: 移轉具有多個提供者的 EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/8/2017
ms.openlocfilehash: 7ae695037992323337a780cda29d8c8ed8a13458
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997969"
---
<a name="migrations-with-multiple-providers"></a>移轉具有多個提供者
==================================
[EF Core 工具][ 1]只 scaffold 移轉為作用中的提供者。 有時候，不過，您可能想要使用您的 DbContext 中的多個提供者 （例如 Microsoft SQL Server 和 SQLite）。 有兩種方式可使用移轉處理這部分。 您可以維護兩個集合的移轉，另一個用於每個提供者--或合併到單一設定，可以在兩者上工作。

<a name="two-migration-sets"></a>兩個移轉集合
------------------
在第一個方法中，您可以產生兩個移轉的每個模型變更。

這是輸入每個移轉一組其中一種方式[分開的組件][ 2]和手動切換作用中的提供者 （和移轉的組件） 加入兩個移轉。

更輕鬆使用這些工具的另一種方法是建立新的型別衍生自您的 DbContext，並會覆寫作用中的提供者。 此類型可用於在設計時新增，或套用移轉的時間。

``` csharp
class MySqliteDbContext : MyDbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

> [!NOTE]
> 由於每個移轉組會使用自己的 DbContext 類型，這個方法不需要使用不同的移轉組件。

當新增新的移轉，來指定內容類型。

``` powershell
Add-Migration InitialCreate -Context MyDbContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context MySqliteDbContext -OutputDir Migrations\SqliteMigrations
```
``` Console
dotnet ef migrations add InitialCreate --context MyDbContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context MySqliteDbContext --output-dir Migrations/SqliteMigrations
```

> [!TIP]
> 您不需要指定輸出目錄進行後續的移轉，因為它們會建立為與最後一個同層級。

<a name="one-migration-set"></a>一個移轉組
-----------------
如果您不喜歡有兩個集合的移轉，可以手動將它們結合成一組可套用至這兩個提供者。

註解可以同時存在，因為提供者會忽略它並不了解任何註解。 例如，適用於 Microsoft SQL Server 和 SQLite 的主索引鍵資料行可能如下所示。

``` csharp
Id = table.Column<int>(nullable: false)
    .Annotation("SqlServer:ValueGenerationStrategy",
        SqlServerValueGenerationStrategy.IdentityColumn)
    .Annotation("Sqlite:Autoincrement", true),
```

如果作業只能套用一個提供者 （或它們有不同的提供者之間），使用`ActiveProvider`告知哪一個提供者為作用中的屬性。

``` csharp
if (migrationBuilder.ActiveProvider == "Microsoft.EntityFrameworkCore.SqlServer")
{
    migrationBuilder.CreateSequence(
        name: "EntityFrameworkHiLoSequence");
}
```


  [1]: ../../miscellaneous/cli/index.md
  [2]: projects.md
