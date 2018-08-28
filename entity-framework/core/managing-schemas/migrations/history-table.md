---
title: 自訂的移轉歷程記錄資料表 EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/7/2017
ms.openlocfilehash: 7ee76cadd6fac4ec403918e88460e43067ae5815
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995692"
---
<a name="custom-migrations-history-table"></a><span data-ttu-id="10331-102">自訂的移轉歷程記錄資料表</span><span class="sxs-lookup"><span data-stu-id="10331-102">Custom Migrations History Table</span></span>
===============================
<span data-ttu-id="10331-103">根據預設，EF Core 追蹤的哪些移轉已經套用至資料庫所記錄的資料表中的這些`__EFMigrationsHistory`。</span><span class="sxs-lookup"><span data-stu-id="10331-103">By default, EF Core keeps track of which migrations have been applied to the database by recording them in a table named `__EFMigrationsHistory`.</span></span> <span data-ttu-id="10331-104">基於各種原因，您可能想要自訂此資料表，使其更符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="10331-104">For various reasons, you may want to customize this table to better suit your needs.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="10331-105">如果您自訂的移轉歷程記錄資料表*之後*套用移轉，您必須負責更新資料庫中現有的資料表。</span><span class="sxs-lookup"><span data-stu-id="10331-105">If you customize the Migrations history table *after* applying migrations, you are responsible for updating the existing table in the database.</span></span>

<a name="schema-and-table-name"></a><span data-ttu-id="10331-106">結構描述和資料表名稱</span><span class="sxs-lookup"><span data-stu-id="10331-106">Schema and table name</span></span>
----------------------
<span data-ttu-id="10331-107">您可以變更結構描述和資料表名稱使用`MigrationsHistoryTable()`方法中的`OnConfiguring()`(或`ConfigureServices()`ASP.NET Core 上)。</span><span class="sxs-lookup"><span data-stu-id="10331-107">You can change the schema and table name using the `MigrationsHistoryTable()` method in `OnConfiguring()` (or `ConfigureServices()` on ASP.NET Core).</span></span> <span data-ttu-id="10331-108">以下是使用 SQL Server EF Core 提供者的範例。</span><span class="sxs-lookup"><span data-stu-id="10331-108">Here is an example using the SQL Server EF Core provider.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options.UseSqlServer(
        connectionString,
        x => x.MigrationsHistoryTable("__MyMigrationsHistory", "mySchema"));
```

<a name="other-changes"></a><span data-ttu-id="10331-109">其他變更</span><span class="sxs-lookup"><span data-stu-id="10331-109">Other changes</span></span>
-------------
<span data-ttu-id="10331-110">若要設定資料表的其他層面，覆寫並取代為提供者特定`IHistoryRepository`服務。</span><span class="sxs-lookup"><span data-stu-id="10331-110">To configure additional aspects of the table, override and replace the provider-specific `IHistoryRepository` service.</span></span> <span data-ttu-id="10331-111">以下是變更的 MigrationId 資料行名稱的範例*識別碼*SQL Server 上。</span><span class="sxs-lookup"><span data-stu-id="10331-111">Here is an example of changing the MigrationId column name to *Id* on SQL Server.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options
        .UseSqlServer(connectionString)
        .ReplaceService<IHistoryRepository, MyHistoryRepository>();
```

> [!WARNING]
> <span data-ttu-id="10331-112">`SqlServerHistoryRepository` 內部的命名空間內，並可能在未來版本中變更。</span><span class="sxs-lookup"><span data-stu-id="10331-112">`SqlServerHistoryRepository` is inside an internal namespace and may change in future releases.</span></span>

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
