---
title: 自訂遷移歷程記錄資料表-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: ed5518f0-a9a6-454e-9e98-a4fa7748c8d0
ms.openlocfilehash: eb19f367611a86f685557a6741a5f2f0bad6b718
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78418976"
---
# <a name="customizing-the-migrations-history-table"></a>自訂遷移記錄資料表
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。

> [!NOTE]
> 本文假設您知道如何在基本案例中使用 Code First 移轉。 如果您沒有這麼做，則必須先閱讀[Code First 移轉](~/ef6/modeling/code-first/migrations/index.md)，才能繼續進行。

## <a name="what-is-migrations-history-table"></a>什麼是遷移歷程記錄資料表？

[遷移記錄資料表] 是 Code First 移轉用來儲存套用至資料庫之遷移相關詳細資料的資料表。 根據預設，資料庫中的資料表名稱會 \_\_MigrationHistory，而且會在將第一次遷移至資料庫時建立。 在 Entity Framework 5 中，如果應用程式使用 Microsoft Sql Server 資料庫，則此資料表是系統資料表。 這在 Entity Framework 6 中已經變更，而且遷移記錄資料表不再標記為系統資料表。

## <a name="why-customize-migrations-history-table"></a>為何要自訂遷移記錄資料表？

遷移歷程記錄資料表應該僅供 Code First 移轉使用，而手動變更可能會中斷遷移。 不過，有時候預設設定並不適合，而且必須自訂資料表，例如：

-   您需要變更資料行的名稱和/或 facet，以啟用3個<sup>rd</sup>合作物件的遷移提供者
-   您想要變更資料表的名稱
-   您必須針對 \_\_MigrationHistory 資料表使用非預設的架構
-   您必須針對特定版本的內容儲存額外的資料，因此您需要在資料表中加入額外的資料行

## <a name="words-of-precaution"></a>預防措施的單字

變更「遷移記錄」資料表的功能強大，但您必須小心不要濫用這個方法它。 EF 執行時間目前不會檢查自訂的遷移記錄資料表是否與執行時間相容。 如果不是，您的應用程式可能會在執行時間中斷，或以無法預期的方式運作。 如果您使用每個資料庫的多個內容，這會更重要，因為在這種情況下，多個內容可以使用相同的遷移記錄資料表來儲存有關遷移的資訊。

## <a name="how-to-customize-migrations-history-table"></a>如何自訂遷移記錄資料表？

在您開始之前，您必須先知道只能在套用第一次遷移之前，自訂遷移記錄資料表。 現在，到程式碼。

首先，您必須建立一個衍生自 HistoryCoNtext 類別的類別，以供您使用。 HistoryCoNtext 類別衍生自 DbCoNtext 類別，因此設定遷移記錄資料表非常類似于使用 Fluent API 來設定 EF 模型。 您只需要覆寫 OnModelCreating 方法，並使用 Fluent API 來設定資料表。

>[!NOTE]
> 通常當您設定 EF 模型時，您不需要呼叫 base。來自已覆寫 OnModelCreating 方法的 OnModelCreating （），因為 DbCoNtext OnModelCreating （）有空白的主體。 這不是設定遷移記錄資料表的情況。 在此情況下，您要在 OnModelCreating （）覆寫中進行的第一件事，就是實際呼叫 base。OnModelCreating （）。 這會以預設方式設定「遷移記錄」資料表，然後您可以在覆寫方法中進行調整。

假設您想要重新命名「遷移歷程記錄」資料表，並將它放入名為「admin」的自訂架構。 此外，您的 DBA 也會想要將 MigrationId 資料行重新命名為遷移\_識別碼。  若要達到此目的，您可以建立下列衍生自 HistoryCoNtext 的類別：

``` csharp
    using System.Data.Common;
    using System.Data.Entity;
    using System.Data.Entity.Migrations.History;

    namespace CustomizableMigrationsHistoryTableSample
    {
        public class MyHistoryContext : HistoryContext
        {
            public MyHistoryContext(DbConnection dbConnection, string defaultSchema)
                : base(dbConnection, defaultSchema)
            {
            }

            protected override void OnModelCreating(DbModelBuilder modelBuilder)
            {
                base.OnModelCreating(modelBuilder);
                modelBuilder.Entity<HistoryRow>().ToTable(tableName: "MigrationHistory", schemaName: "admin");
                modelBuilder.Entity<HistoryRow>().Property(p => p.MigrationId).HasColumnName("Migration_ID");
            }
        }
    }
```

當您的自訂 HistoryCoNtext 就緒之後，您必須透過以[程式碼為基礎](https://msdn.com/data/jj680699)的設定來註冊它，才能讓 EF 知道它：

``` csharp
    using System.Data.Entity;

    namespace CustomizableMigrationsHistoryTableSample
    {
        public class ModelConfiguration : DbConfiguration
        {
            public ModelConfiguration()
            {
                this.SetHistoryContext("System.Data.SqlClient",
                    (connection, defaultSchema) => new MyHistoryContext(connection, defaultSchema));
            }
        }
    }
```

就是這麼多。 現在您可以移至 [套件管理員主控台]、[啟用-遷移]、[新增-遷移] 和最後 [更新資料庫]。 這應該會導致根據您在 HistoryCoNtext 衍生類別中指定的詳細資料，將已設定的遷移歷程記錄資料表新增至資料庫。

![資料庫](~/ef6/media/database.png)
