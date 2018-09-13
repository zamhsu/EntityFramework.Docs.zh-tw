---
title: 自訂的移轉歷程記錄資料表 EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/07/2017
ms.openlocfilehash: 1a253972a8f4e410421ec8a77c079e588d368819
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45488812"
---
<a name="custom-migrations-history-table"></a>自訂的移轉歷程記錄資料表
===============================
根據預設，EF Core 追蹤的哪些移轉已經套用至資料庫所記錄的資料表中的這些`__EFMigrationsHistory`。 基於各種原因，您可能想要自訂此資料表，使其更符合您的需求。

> [!IMPORTANT]
> 如果您自訂的移轉歷程記錄資料表*之後*套用移轉，您必須負責更新資料庫中現有的資料表。

<a name="schema-and-table-name"></a>結構描述和資料表名稱
----------------------
您可以變更結構描述和資料表名稱使用`MigrationsHistoryTable()`方法中的`OnConfiguring()`(或`ConfigureServices()`ASP.NET Core 上)。 以下是使用 SQL Server EF Core 提供者的範例。

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options.UseSqlServer(
        connectionString,
        x => x.MigrationsHistoryTable("__MyMigrationsHistory", "mySchema"));
```

<a name="other-changes"></a>其他變更
-------------
若要設定資料表的其他層面，覆寫並取代為提供者特定`IHistoryRepository`服務。 以下是變更的 MigrationId 資料行名稱的範例*識別碼*SQL Server 上。

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options
        .UseSqlServer(connectionString)
        .ReplaceService<IHistoryRepository, MyHistoryRepository>();
```

> [!WARNING]
> `SqlServerHistoryRepository` 內部的命名空間內，並可能在未來版本中變更。

``` csharp
class MyHistoryRepository : SqlServerHistoryRepository
{
    public MyHistoryRepository(HistoryRepositoryDependencies dependencies)
        : base(dependencies)
    {
    }

    protected override void ConfigureTable(EntityTypeBuilder<HistoryRow> history)
    {
        base.ConfigureTable(history);

        history.Property(h => h.MigrationId).HasColumnName("Id");
    }
}
```
