---
title: "自訂的移轉歷程記錄資料表 EF 核心"
author: bricelam
ms.author: bricelam
ms.date: 11/7/2017
ms.technology: entity-framework-core
ms.openlocfilehash: cb9892241f3d7f1fae6293bd60a8a5c3e7120969
ms.sourcegitcommit: b467368cc350e6059fdc0949e042a41cb11e61d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
<a name="custom-migrations-history-table"></a><span data-ttu-id="67536-102">自訂的移轉歷程記錄資料表</span><span class="sxs-lookup"><span data-stu-id="67536-102">Custom Migrations History Table</span></span>
===============================
<span data-ttu-id="67536-103">根據預設，EF 核心追蹤的哪些移轉已經套用至資料庫所記錄的資料表中的這些`__EFMigrationsHistory`。</span><span class="sxs-lookup"><span data-stu-id="67536-103">By default, EF Core keeps track of which migrations have been applied to the database by recording them in a table named `__EFMigrationsHistory`.</span></span> <span data-ttu-id="67536-104">基於各種原因，您可能想要自訂此資料表，使其更符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="67536-104">For various reasons, you may want to customize this table to better suit your needs.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="67536-105">如果您自訂的移轉歷程記錄資料表*之後*套用移轉，您必須負責更新資料庫中現有的資料表。</span><span class="sxs-lookup"><span data-stu-id="67536-105">If you customize the Migrations history table *after* applying migrations, you are responsible for updating the existing table in the database.</span></span>

<a name="schema-and-table-name"></a><span data-ttu-id="67536-106">結構描述和資料表名稱</span><span class="sxs-lookup"><span data-stu-id="67536-106">Schema and table name</span></span>
----------------------
<span data-ttu-id="67536-107">您可以變更結構描述和資料表名稱使用`MigrationsHistoryTable()`方法中的`OnConfiguring()`(或`ConfigureServices()`ASP.NET Core 上)。</span><span class="sxs-lookup"><span data-stu-id="67536-107">You can change the schema and table name using the `MigrationsHistoryTable()` method in `OnConfiguring()` (or `ConfigureServices()` on ASP.NET Core).</span></span> <span data-ttu-id="67536-108">以下是使用 SQL Server EF 核心提供者的範例。</span><span class="sxs-lookup"><span data-stu-id="67536-108">Here is an example using the SQL Server EF Core provider.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options.UseSqlServer(
        connectionString,
        x => x.MigrationsHistoryTable("__MyMigrationsHistory", "mySchema"));
```

<a name="other-changes"></a><span data-ttu-id="67536-109">其他變更</span><span class="sxs-lookup"><span data-stu-id="67536-109">Other changes</span></span>
-------------
<span data-ttu-id="67536-110">若要設定資料表的其他層面，覆寫並取代特定提供者`IHistoryRepository`服務。</span><span class="sxs-lookup"><span data-stu-id="67536-110">To configure additional aspects of the table, override and replace the provider-specific `IHistoryRepository` service.</span></span> <span data-ttu-id="67536-111">以下是變更 MigrationId 資料行名稱的範例*識別碼*SQL Server 上。</span><span class="sxs-lookup"><span data-stu-id="67536-111">Here is an example of changing the MigrationId column name to *Id* on SQL Server.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options
        .UseSqlServer(connectionString)
        .ReplaceService<IHistoryRepository, MyHistoryRepository>();
```

> [!WARNING]
> <span data-ttu-id="67536-112">`SqlServerHistoryRepository`內部的命名空間內，且可能在未來版本中變更。</span><span class="sxs-lookup"><span data-stu-id="67536-112">`SqlServerHistoryRepository` is inside an internal namespace and may change in future releases.</span></span>

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
