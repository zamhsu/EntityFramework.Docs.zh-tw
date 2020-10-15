---
title: 自訂遷移歷程記錄資料表-EF6
description: 在 Entity Framework 6 中自訂遷移歷程記錄資料表
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/migrations/history-customization
ms.openlocfilehash: f0f474507659d5fbe43ecf2ed7216fb5d1a86779
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066391"
---
# <a name="customizing-the-migrations-history-table"></a>自訂遷移歷程記錄資料表
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。

> [!NOTE]
> 本文假設您知道如何在基本案例中使用 Code First 移轉。 如果您沒有這麼做，就必須先閱讀 [Code First 移轉](xref:ef6/modeling/code-first/migrations/index) ，再繼續進行操作。

## <a name="what-is-migrations-history-table"></a>什麼是遷移歷程記錄資料表？

「遷移歷程記錄資料表」是 Code First 移轉用來儲存套用至資料庫之遷移詳細資料的資料表。 根據預設，資料庫中的資料表名稱是 \_ \_ MigrationHistory，而且會在套用第一次遷移至資料庫時建立。 在 Entity Framework 5 中，如果應用程式使用 Microsoft Sql Server 資料庫，則此資料表是系統資料表。 不過，這在 Entity Framework 6 中已經變更，而且遷移歷程記錄資料表不再標示系統資料表。

## <a name="why-customize-migrations-history-table"></a>為何要自訂遷移歷程記錄資料表？

遷移歷程記錄資料表應該僅供 Code First 移轉使用，並以手動方式進行變更，以便中斷遷移。 但是，有時候預設設定並不適合，而且必須自訂資料表，例如：

-   您必須變更資料行的名稱和/或 facet，才能啟用3部<sup>rd</sup> 合作物件遷移提供者
-   您想要變更資料表的名稱
-   您必須針對 \_ MigrationHistory 資料表使用非預設的架構 \_
-   您需要針對特定版本的內容儲存額外的資料，因此您需要將其他資料行加入至資料表

## <a name="words-of-precaution"></a>預防措施的單字

變更遷移歷程記錄資料表的功能強大，但您必須小心不要濫用這個方法。 EF runtime 目前不會檢查自訂的遷移歷程記錄資料表是否與執行時間相容。 如果不是您的應用程式，可能會在執行時間中斷，或以無法預期的方式運作。 如果您對每個資料庫使用多個內容，這會更重要，因為在這種情況下，多個內容可以使用相同的遷移歷程記錄資料表來儲存有關遷移的資訊。

## <a name="how-to-customize-migrations-history-table"></a>如何自訂遷移歷程記錄資料表？

開始之前，您必須先知道您只能自訂「遷移歷程記錄」資料表，然後再套用第一個遷移。 現在到程式碼。

首先，您必須建立一個衍生自 HistoryCoNtext 類別的類別（class）。 HistoryCoNtext 類別衍生自 DbCoNtext 類別，因此設定遷移歷程記錄資料表與使用流暢 API 來設定 EF 模型非常類似。 您只需要覆寫 OnModelCreating 方法，並使用流暢的 API 來設定資料表。

>[!NOTE]
> 一般來說，當您設定 EF 模型時，您不需要呼叫 base。從覆寫的 OnModelCreating 方法中 OnModelCreating ( # A1，因為 DbCoNtext. OnModelCreating ( # A3 有空白主體。 這不是設定遷移歷程記錄資料表的情況。 在此案例中，第一件事是在 OnModelCreating ( # A1 覆寫是實際呼叫基底。OnModelCreating ( # A3。 這會以您接著在覆寫方法中進行調整的預設方式來設定遷移歷程記錄資料表。

假設您想要重新命名遷移歷程記錄資料表，並將它放入名為 "admin" 的自訂架構。 此外，您的 DBA 希望您將 MigrationId 資料行重新命名為「遷移 \_ 識別碼」。 您可以藉由建立下列衍生自 HistoryCoNtext 的類別來達成此目的：

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

當您的自訂 HistoryCoNtext 就緒之後，您必須透過以 [程式碼為基礎](https://msdn.com/data/jj680699)的設定註冊，讓 EF 知道它：

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

這就是這麼多。 現在您可以移至封裝管理員主控台、啟用-遷移、Add-Migration 和最後更新資料庫。 這應該會導致依據您在 HistoryCoNtext 衍生類別中指定的詳細資料，將已設定的遷移歷程記錄資料表加入至資料庫。

![遷移歷程記錄資料表](~/ef6/media/database.png)
