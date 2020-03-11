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
# <a name="customizing-the-migrations-history-table"></a><span data-ttu-id="dc2fd-102">自訂遷移記錄資料表</span><span class="sxs-lookup"><span data-stu-id="dc2fd-102">Customizing the migrations history table</span></span>
> [!NOTE]
> <span data-ttu-id="dc2fd-103">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="dc2fd-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="dc2fd-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="dc2fd-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

> [!NOTE]
> <span data-ttu-id="dc2fd-105">本文假設您知道如何在基本案例中使用 Code First 移轉。</span><span class="sxs-lookup"><span data-stu-id="dc2fd-105">This article assumes you know how to use Code First Migrations in basic scenarios.</span></span> <span data-ttu-id="dc2fd-106">如果您沒有這麼做，則必須先閱讀[Code First 移轉](~/ef6/modeling/code-first/migrations/index.md)，才能繼續進行。</span><span class="sxs-lookup"><span data-stu-id="dc2fd-106">If you don’t, then you’ll need to read [Code First Migrations](~/ef6/modeling/code-first/migrations/index.md) before continuing.</span></span>

## <a name="what-is-migrations-history-table"></a><span data-ttu-id="dc2fd-107">什麼是遷移歷程記錄資料表？</span><span class="sxs-lookup"><span data-stu-id="dc2fd-107">What is Migrations History Table?</span></span>

<span data-ttu-id="dc2fd-108">[遷移記錄資料表] 是 Code First 移轉用來儲存套用至資料庫之遷移相關詳細資料的資料表。</span><span class="sxs-lookup"><span data-stu-id="dc2fd-108">Migrations history table is a table used by Code First Migrations to store details about migrations applied to the database.</span></span> <span data-ttu-id="dc2fd-109">根據預設，資料庫中的資料表名稱會 \_\_MigrationHistory，而且會在將第一次遷移至資料庫時建立。</span><span class="sxs-lookup"><span data-stu-id="dc2fd-109">By default the name of the table in the database is \_\_MigrationHistory and it is created when applying the first migration to the database.</span></span> <span data-ttu-id="dc2fd-110">在 Entity Framework 5 中，如果應用程式使用 Microsoft Sql Server 資料庫，則此資料表是系統資料表。</span><span class="sxs-lookup"><span data-stu-id="dc2fd-110">In Entity Framework 5 this table was a system table if the application used Microsoft Sql Server database.</span></span> <span data-ttu-id="dc2fd-111">這在 Entity Framework 6 中已經變更，而且遷移記錄資料表不再標記為系統資料表。</span><span class="sxs-lookup"><span data-stu-id="dc2fd-111">This has changed in Entity Framework 6 however and the migrations history table is no longer marked a system table.</span></span>

## <a name="why-customize-migrations-history-table"></a><span data-ttu-id="dc2fd-112">為何要自訂遷移記錄資料表？</span><span class="sxs-lookup"><span data-stu-id="dc2fd-112">Why customize Migrations History Table?</span></span>

<span data-ttu-id="dc2fd-113">遷移歷程記錄資料表應該僅供 Code First 移轉使用，而手動變更可能會中斷遷移。</span><span class="sxs-lookup"><span data-stu-id="dc2fd-113">Migrations history table is supposed to be used solely by Code First Migrations and changing it manually can break migrations.</span></span> <span data-ttu-id="dc2fd-114">不過，有時候預設設定並不適合，而且必須自訂資料表，例如：</span><span class="sxs-lookup"><span data-stu-id="dc2fd-114">However sometimes the default configuration is not suitable and the table needs to be customized, for instance:</span></span>

-   <span data-ttu-id="dc2fd-115">您需要變更資料行的名稱和/或 facet，以啟用3個<sup>rd</sup>合作物件的遷移提供者</span><span class="sxs-lookup"><span data-stu-id="dc2fd-115">You need to change names and/or facets of the columns to enable a 3<sup>rd</sup> party Migrations provider</span></span>
-   <span data-ttu-id="dc2fd-116">您想要變更資料表的名稱</span><span class="sxs-lookup"><span data-stu-id="dc2fd-116">You want to change the name of the table</span></span>
-   <span data-ttu-id="dc2fd-117">您必須針對 \_\_MigrationHistory 資料表使用非預設的架構</span><span class="sxs-lookup"><span data-stu-id="dc2fd-117">You need to use a non-default schema for the \_\_MigrationHistory table</span></span>
-   <span data-ttu-id="dc2fd-118">您必須針對特定版本的內容儲存額外的資料，因此您需要在資料表中加入額外的資料行</span><span class="sxs-lookup"><span data-stu-id="dc2fd-118">You need to store additional data for a given version of the context and therefore you need to add an additional column to the table</span></span>

## <a name="words-of-precaution"></a><span data-ttu-id="dc2fd-119">預防措施的單字</span><span class="sxs-lookup"><span data-stu-id="dc2fd-119">Words of precaution</span></span>

<span data-ttu-id="dc2fd-120">變更「遷移記錄」資料表的功能強大，但您必須小心不要濫用這個方法它。</span><span class="sxs-lookup"><span data-stu-id="dc2fd-120">Changing the migration history table is powerful but you need to be careful to not overdo it.</span></span> <span data-ttu-id="dc2fd-121">EF 執行時間目前不會檢查自訂的遷移記錄資料表是否與執行時間相容。</span><span class="sxs-lookup"><span data-stu-id="dc2fd-121">EF runtime currently does not check whether the customized migrations history table is compatible with the runtime.</span></span> <span data-ttu-id="dc2fd-122">如果不是，您的應用程式可能會在執行時間中斷，或以無法預期的方式運作。</span><span class="sxs-lookup"><span data-stu-id="dc2fd-122">If it is not your application may break at runtime or behave in unpredictable ways.</span></span> <span data-ttu-id="dc2fd-123">如果您使用每個資料庫的多個內容，這會更重要，因為在這種情況下，多個內容可以使用相同的遷移記錄資料表來儲存有關遷移的資訊。</span><span class="sxs-lookup"><span data-stu-id="dc2fd-123">This is even more important if you use multiple contexts per database in which case multiple contexts can use the same migration history table to store information about migrations.</span></span>

## <a name="how-to-customize-migrations-history-table"></a><span data-ttu-id="dc2fd-124">如何自訂遷移記錄資料表？</span><span class="sxs-lookup"><span data-stu-id="dc2fd-124">How to customize Migrations History Table?</span></span>

<span data-ttu-id="dc2fd-125">在您開始之前，您必須先知道只能在套用第一次遷移之前，自訂遷移記錄資料表。</span><span class="sxs-lookup"><span data-stu-id="dc2fd-125">Before you start you need to know that you can customize the migrations history table only before you apply the first migration.</span></span> <span data-ttu-id="dc2fd-126">現在，到程式碼。</span><span class="sxs-lookup"><span data-stu-id="dc2fd-126">Now, to the code.</span></span>

<span data-ttu-id="dc2fd-127">首先，您必須建立一個衍生自 HistoryCoNtext 類別的類別，以供您使用。</span><span class="sxs-lookup"><span data-stu-id="dc2fd-127">First, you will need to create a class derived from System.Data.Entity.Migrations.History.HistoryContext class.</span></span> <span data-ttu-id="dc2fd-128">HistoryCoNtext 類別衍生自 DbCoNtext 類別，因此設定遷移記錄資料表非常類似于使用 Fluent API 來設定 EF 模型。</span><span class="sxs-lookup"><span data-stu-id="dc2fd-128">The HistoryContext class is derived from the DbContext class so configuring the migrations history table is very similar to configuring EF models with fluent API.</span></span> <span data-ttu-id="dc2fd-129">您只需要覆寫 OnModelCreating 方法，並使用 Fluent API 來設定資料表。</span><span class="sxs-lookup"><span data-stu-id="dc2fd-129">You just need to override the OnModelCreating method and use fluent API to configure the table.</span></span>

>[!NOTE]
> <span data-ttu-id="dc2fd-130">通常當您設定 EF 模型時，您不需要呼叫 base。來自已覆寫 OnModelCreating 方法的 OnModelCreating （），因為 DbCoNtext OnModelCreating （）有空白的主體。</span><span class="sxs-lookup"><span data-stu-id="dc2fd-130">Typically when you configure EF models you don’t need to call base.OnModelCreating() from the overridden OnModelCreating method since the DbContext.OnModelCreating() has empty body.</span></span> <span data-ttu-id="dc2fd-131">這不是設定遷移記錄資料表的情況。</span><span class="sxs-lookup"><span data-stu-id="dc2fd-131">This is not the case when configuring the migrations history table.</span></span> <span data-ttu-id="dc2fd-132">在此情況下，您要在 OnModelCreating （）覆寫中進行的第一件事，就是實際呼叫 base。OnModelCreating （）。</span><span class="sxs-lookup"><span data-stu-id="dc2fd-132">In this case the first thing to do in your OnModelCreating() override is to actually call base.OnModelCreating().</span></span> <span data-ttu-id="dc2fd-133">這會以預設方式設定「遷移記錄」資料表，然後您可以在覆寫方法中進行調整。</span><span class="sxs-lookup"><span data-stu-id="dc2fd-133">This will configure the migrations history table in the default way which you then tweak in the overriding method.</span></span>

<span data-ttu-id="dc2fd-134">假設您想要重新命名「遷移歷程記錄」資料表，並將它放入名為「admin」的自訂架構。</span><span class="sxs-lookup"><span data-stu-id="dc2fd-134">Let’s say you want to rename the migrations history table and put it to a custom schema called “admin”.</span></span> <span data-ttu-id="dc2fd-135">此外，您的 DBA 也會想要將 MigrationId 資料行重新命名為遷移\_識別碼。</span><span class="sxs-lookup"><span data-stu-id="dc2fd-135">In addition your DBA would like you to rename the MigrationId column to Migration\_ID.</span></span> <span data-ttu-id="dc2fd-136"> 若要達到此目的，您可以建立下列衍生自 HistoryCoNtext 的類別：</span><span class="sxs-lookup"><span data-stu-id="dc2fd-136"> You could achieve this by creating the following class derived from HistoryContext:</span></span>

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

<span data-ttu-id="dc2fd-137">當您的自訂 HistoryCoNtext 就緒之後，您必須透過以[程式碼為基礎](https://msdn.com/data/jj680699)的設定來註冊它，才能讓 EF 知道它：</span><span class="sxs-lookup"><span data-stu-id="dc2fd-137">Once your custom HistoryContext is ready you need to make EF aware of it by registering it via [code-based configuration](https://msdn.com/data/jj680699):</span></span>

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

<span data-ttu-id="dc2fd-138">就是這麼多。</span><span class="sxs-lookup"><span data-stu-id="dc2fd-138">That’s pretty much it.</span></span> <span data-ttu-id="dc2fd-139">現在您可以移至 [套件管理員主控台]、[啟用-遷移]、[新增-遷移] 和最後 [更新資料庫]。</span><span class="sxs-lookup"><span data-stu-id="dc2fd-139">Now you can go to the Package Manager Console, Enable-Migrations, Add-Migration and finally Update-Database.</span></span> <span data-ttu-id="dc2fd-140">這應該會導致根據您在 HistoryCoNtext 衍生類別中指定的詳細資料，將已設定的遷移歷程記錄資料表新增至資料庫。</span><span class="sxs-lookup"><span data-stu-id="dc2fd-140">This should result in adding to the database a migrations history table configured according to the details you specified in your HistoryContext derived class.</span></span>

![資料庫](~/ef6/media/database.png)
