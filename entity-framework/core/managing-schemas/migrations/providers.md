---
title: 使用多個提供者進行遷移-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/08/2017
uid: core/managing-schemas/migrations/providers
ms.openlocfilehash: efe95893f7dbfc8e5c4775e86d58abb32eee3c83
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416784"
---
# <a name="migrations-with-multiple-providers"></a>使用多個提供者進行遷移

[EF Core 工具][1]僅 scaffold 作用中提供者的遷移。 不過，有時候您可能會想要使用一個以上的提供者（例如 Microsoft SQL Server 和 SQLite）來搭配 DbCoNtext。 有兩種方式可以透過遷移來處理這種情況。 您可以維護兩組遷移（每個提供者一個），或將它們合併成可同時在兩者上使用的單一集合。

## <a name="two-migration-sets"></a>兩個遷移集

在第一個方法中，您會針對每個模型變更產生兩個遷移。

其中一種方法是將每個遷移集放[在不同的元件中][2]，並在新增兩個遷移的過程中手動切換使用中的提供者（和遷移元件）。

使用工具更容易的另一種方法，就是建立一個衍生自 DbCoNtext 的新型別，並覆寫使用中的提供者。 此類型是在設計階段用於新增或套用遷移時使用。

``` csharp
class MySqliteDbContext : MyDbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

> [!NOTE]
> 由於每個遷移集都使用自己的 DbCoNtext 類型，因此這種方法不需要使用個別的遷移元件。

加入新的遷移時，請指定內容類型。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --context MyDbContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context MySqliteDbContext --output-dir Migrations/SqliteMigrations
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration InitialCreate -Context MyDbContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context MySqliteDbContext -OutputDir Migrations\SqliteMigrations
```

***

> [!TIP]
> 您不需要指定輸出目錄來進行後續的遷移，因為它們會建立為最後一個的同級。

## <a name="one-migration-set"></a>一個遷移集

如果您不喜歡兩組遷移，可以手動將它們結合成可以套用至這兩個提供者的單一集合。

批註可以並存，因為提供者會忽略它不了解的任何注釋。 例如，同時使用 Microsoft SQL Server 和 SQLite 的主鍵資料行，可能如下所示。

``` csharp
Id = table.Column<int>(nullable: false)
    .Annotation("SqlServer:ValueGenerationStrategy",
        SqlServerValueGenerationStrategy.IdentityColumn)
    .Annotation("Sqlite:Autoincrement", true),
```

如果作業只能套用至一個提供者（或在提供者之間不同），請使用 `ActiveProvider` 屬性來分辨哪個提供者為作用中。

``` csharp
if (migrationBuilder.ActiveProvider == "Microsoft.EntityFrameworkCore.SqlServer")
{
    migrationBuilder.CreateSequence(
        name: "EntityFrameworkHiLoSequence");
}
```

  [1]: ../../miscellaneous/cli/index.md
  [2]: projects.md
