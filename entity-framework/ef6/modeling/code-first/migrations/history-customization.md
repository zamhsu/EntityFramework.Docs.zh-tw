---
title: 自訂的移轉歷程記錄資料表-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: ed5518f0-a9a6-454e-9e98-a4fa7748c8d0
ms.openlocfilehash: eb19f367611a86f685557a6741a5f2f0bad6b718
ms.sourcegitcommit: e66745c9f91258b2cacf5ff263141be3cba4b09e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2019
ms.locfileid: "54058743"
---
# <a name="customizing-the-migrations-history-table"></a>自訂的移轉歷程記錄資料表
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。

> [!NOTE]
> 本文假設您知道如何在基本案例中使用 Code First 移轉。 如果不這麼做，則您將需要閱讀[Code First 移轉](~/ef6/modeling/code-first/migrations/index.md)再繼續進行。

## <a name="what-is-migrations-history-table"></a>什麼是移轉歷程記錄資料表？

移轉歷程記錄資料表是使用 Code First 移轉來儲存移轉套用至資料庫的詳細資料的資料表。 在資料庫中資料表的名稱是預設\_ \_MigrationHistory，而且它會建立時套用到資料庫的第一次移轉。 Entity Framework 5 在這個資料表會是系統資料表，如果應用程式使用 Microsoft Sql Server 資料庫。 這已但是變更在 Entity Framework 6 中，移轉歷程記錄資料表不再標示為系統資料表。

## <a name="why-customize-migrations-history-table"></a>為何要進行自訂的移轉歷程記錄資料表嗎？

移轉歷程記錄資料表應該僅由 Code First 移轉，並將它手動變更可能會中斷移轉。 不過有時候預設設定不適合，資料表需要進行自訂，例如：

-   您需要變更名稱和 （或） 要啟用 3 的資料行的 facet<sup>rd</sup>合作對象移轉提供者
-   您想要變更資料表的名稱
-   您需要使用非預設結構描述\_ \_MigrationHistory 資料表
-   您需要儲存內容的特定版本的其他資料，因此您需要將額外的資料行加入至資料表

## <a name="words-of-precaution"></a>字組的預防措施

變更移轉歷程記錄資料表是功能強大，但您需要小心不濫用。 EF 執行階段目前不會檢查自訂的移轉歷程記錄資料表是否與執行階段相容。 如果不是您的應用程式可能會在執行階段中斷或非預期的方式運作。 這是更重要的是如果您使用每個資料庫的多個內容在這種情況下多個內容可以使用相同的移轉歷程記錄資料表來儲存移轉的相關資訊。

## <a name="how-to-customize-migrations-history-table"></a>如何自訂移轉歷程記錄資料表？

開始之前要知道，只會在套用第一次移轉之前，您可以自訂的移轉歷程記錄資料表。 現在，程式碼所示。

首先，您必須建立衍生自 System.Data.Entity.Migrations.History.HistoryContext 類別的類別。 HistoryContext 類別被衍生自 DbContext 類別中，所以設定移轉歷程記錄資料表非常類似於使用 fluent API 設定 EF 模型。 您只需要覆寫在 OnModelCreating 方法，並使用 fluent API 來設定資料表。

>[!NOTE]
> 通常當您設定 EF 模型不需要呼叫基底。從覆寫的 OnModelCreating 方法因為 DbContext.OnModelCreating() 具有空白主體的 onmodelcreating （)。 這不是大小寫設定的移轉歷程記錄資料表時。 在此情況下第一個 onmodelcreating （） 覆寫中的做法是呼叫基底。在 onmodelcreating （)。 這會設定移轉歷程記錄資料表，然後調整覆寫方法中的預設方式。

例如，假設您想要重新命名移轉歷程記錄資料表，並將它放至稱為"admin"的自訂結構描述。 此外，想要您重新命名 MigrationId 資料行移轉至您的 DBA\_識別碼。  您可以建立下列類別衍生自 HistoryContext 來達到此目的設定：

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

您自訂的 HistoryContext 準備就緒後，您需要讓 EF 知道它來註冊它透過[程式碼為基礎的組態](https://msdn.com/data/jj680699):

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

就差不多就是這麼簡單。 現在您可以移至 Package Manager Console Enable-migrations，新增移轉並最後更新資料庫。 這應該會導致將加入資料庫的移轉歷程記錄資料表設定根據您指定 HistoryContext 衍生類別中的詳細資料。

![資料庫](~/ef6/media/database.png)
