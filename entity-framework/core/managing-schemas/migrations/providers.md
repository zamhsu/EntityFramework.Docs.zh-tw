---
title: 移轉具有多個提供者的 EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/8/2017
ms.technology: entity-framework-core
ms.openlocfilehash: d950e74ed4cef7d4274aabcf3eda7b0b735574c6
ms.sourcegitcommit: 2ef0a4a90b01edd22b9206f8729b8de459ef8cab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2018
ms.locfileid: "30002801"
---
<a name="migrations-with-multiple-providers"></a>移轉具有多個提供者
==================================
[EF Core 工具][ 1]只 scaffold 移轉為作用中的提供者。 有時候，不過，您可能想要搭配您 DbContext 使用一個以上的提供者 （例如 Microsoft SQL Server 和 SQLite）。 有兩種方式來處理此情形的移轉。 您可以維護兩個集合的移轉-一個用於每個提供者--或合併至單一設定，可以在同時工作。

<a name="two-migration-sets"></a>兩個移轉集合
------------------
在第一種方法，您會產生兩個移轉的每個模型變更。

這是將每個移轉集執行方式之一[分開的組件][ 2]手動加入兩個移轉之間進行切換使用中的提供者 （和移轉組件）。

容易使用工具的另一種方法是建立新的類型，衍生自您 DbContext 和覆寫使用中的提供者。 此類型用在設計時新增或套用移轉的時間。

``` csharp
class MySqliteDbContext : MyDbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

> [!NOTE]
> 因為每個移轉組會使用它自己的 DbContext 類型，這個方法不需要使用個別移轉組件。

當加入新的移轉，會指定內容類型。

``` powershell
Add-Migration InitialCreate -Context MyDbContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context MySqliteDbContext -OutputDir Migrations\SqliteMigrations
```
``` Console
dotnet ef migrations add InitialCreate --context MyDbContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context MySqliteDbContext --output-dir Migrations/SqliteMigrations
```

> [!TIP]
> 您不需要指定後續的移轉作業的輸出目錄，因為它們會建立為最後一個同層級。

<a name="one-migration-set"></a>移轉設定
-----------------
如果您不喜歡具有兩個集合的移轉，可以手動將它們結合成單一集合可套用至兩個提供者。

因為提供者會忽略不了解任何附註，註解可以同時存在。 例如，適用於 Microsoft SQL Server 和 SQLite 主索引鍵資料行可能如下所示。

``` csharp
Id = table.Column<int>(nullable: false)
    .Annotation("SqlServer:ValueGenerationStrategy",
        SqlServerValueGenerationStrategy.IdentityColumn)
    .Annotation("Sqlite:Autoincrement", true),
```

如果作業只能套用一個提供者 （或它們以不同的方式提供者之間），使用`ActiveProvider`分辨哪一個提供者為作用中的屬性。

``` csharp
if (migrationBuilder.ActiveProvider == "Microsoft.EntityFrameworkCore.SqlServer")
{
    migrationBuilder.CreateSequence(
        name: "EntityFrameworkHiLoSequence");
}
```


  [1]: ../../miscellaneous/cli/index.md
  [2]: projects.md
