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
# <a name="migrations-with-multiple-providers"></a>使用多個提供者進行遷移

[EF Core 的工具](xref:core/cli/index)只會 scaffold 作用中提供者的遷移。 不過，有時候您可能會想要使用一個以上的提供者 (例如 Microsoft SQL Server 和 SQLite) DbCoNtext）。 藉由維護多個遷移集（每個提供者各一個），並為每個模型變更新增每個的遷移，來處理此情況。

## <a name="using-multiple-context-types"></a>使用多個內容類型

建立多個遷移集的其中一種方式是為每個提供者使用一個 DbCoNtext 類型。

```csharp
class SqliteBlogContext : BlogContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

新增新的遷移時，請指定內容類型。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --context BlogContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context SqliteBlogContext --output-dir Migrations/SqliteMigrations
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration InitialCreate -Context BlogContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context SqliteBlogContext -OutputDir Migrations\SqliteMigrations
```

***

> [!TIP]
> 您不需要指定輸出目錄來進行後續的遷移，因為它們會建立為最後一個的同級。

## <a name="using-one-context-type"></a>使用一個內容類型

也可以使用一個 DbCoNtext 類型。 這目前需要將遷移移至另一個元件。 如需設定專案的指示，請參閱 [使用個別的遷移專案](xref:core/managing-schemas/migrations/projects) 。

> [!TIP]
> 您可以檢視本文中的 [GitHut 範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Schemas/TwoProjectMigrations)。

從 EF Core 5.0 開始，您可以從工具將引數傳遞至應用程式。 這可以讓更簡化的工作流程，避免在執行工具時手動變更專案。

以下是使用 [泛型主機](/dotnet/core/extensions/generic-host)時運作正常的一個模式。

[!code-csharp[](../../../../samples/core/Schemas/TwoProjectMigrations/WorkerService1/Program.cs#snippet_CreateHostBuilder)]

由於預設主機產生器會從命令列引數讀取設定，因此您可以在執行工具時指定提供者。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add MyMigration --project ../SqlServerMigrations -- --provider SqlServer
dotnet ef migrations add MyMigration --project ../SqliteMigrations -- --provider Sqlite
```

> [!TIP]
> `--`標記會指示 `dotnet ef` 將後面的所有專案視為引數，而不會嘗試將它們剖析為選項。 未使用的任何額外引數 `dotnet ef` 都會轉送至應用程式。

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration MyMigration -Args "--provider SqlServer"
Add-Migration MyMigration -Args "--provider Sqlite"
```

***

> [!NOTE]
> 在 EF Core 5.0 中新增了為應用程式指定額外引數的功能。 如果您使用的是較舊的版本，請改為使用環境變數來指定設定值。
