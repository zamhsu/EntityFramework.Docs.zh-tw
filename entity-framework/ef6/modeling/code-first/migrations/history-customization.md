---
title: 自訂遷移歷程記錄資料表-EF6
description: 在 Entity Framework 6 中自訂遷移歷程記錄資料表
author: divega
ms.date: 10/23/2016
ms.assetid: ed5518f0-a9a6-454e-9e98-a4fa7748c8d0
uid: ef6/modeling/code-first/migrations/history-customization
ms.openlocfilehash: a6cd27f39c648d35d2e0238a10f8a6b351cc1220
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618069"
---
# <a name="customizing-the-migrations-history-table"></a><span data-ttu-id="72bc2-103">自訂遷移歷程記錄資料表</span><span class="sxs-lookup"><span data-stu-id="72bc2-103">Customizing the migrations history table</span></span>
> [!NOTE]
> <span data-ttu-id="72bc2-104">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="72bc2-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="72bc2-105">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="72bc2-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

> [!NOTE]
> <span data-ttu-id="72bc2-106">本文假設您知道如何在基本案例中使用 Code First 移轉。</span><span class="sxs-lookup"><span data-stu-id="72bc2-106">This article assumes you know how to use Code First Migrations in basic scenarios.</span></span> <span data-ttu-id="72bc2-107">如果您沒有這麼做，就必須先閱讀 [Code First 移轉](xref:ef6/modeling/code-first/migrations/index) ，再繼續進行操作。</span><span class="sxs-lookup"><span data-stu-id="72bc2-107">If you don’t, then you’ll need to read [Code First Migrations](xref:ef6/modeling/code-first/migrations/index) before continuing.</span></span>

## <a name="what-is-migrations-history-table"></a><span data-ttu-id="72bc2-108">什麼是遷移歷程記錄資料表？</span><span class="sxs-lookup"><span data-stu-id="72bc2-108">What is Migrations History Table?</span></span>

<span data-ttu-id="72bc2-109">「遷移歷程記錄資料表」是 Code First 移轉用來儲存套用至資料庫之遷移詳細資料的資料表。</span><span class="sxs-lookup"><span data-stu-id="72bc2-109">Migrations history table is a table used by Code First Migrations to store details about migrations applied to the database.</span></span> <span data-ttu-id="72bc2-110">根據預設，資料庫中的資料表名稱是 \_ \_ MigrationHistory，而且會在套用第一次遷移至資料庫時建立。</span><span class="sxs-lookup"><span data-stu-id="72bc2-110">By default the name of the table in the database is \_\_MigrationHistory and it is created when applying the first migration to the database.</span></span> <span data-ttu-id="72bc2-111">在 Entity Framework 5 中，如果應用程式使用 Microsoft Sql Server 資料庫，則此資料表是系統資料表。</span><span class="sxs-lookup"><span data-stu-id="72bc2-111">In Entity Framework 5 this table was a system table if the application used Microsoft Sql Server database.</span></span> <span data-ttu-id="72bc2-112">不過，這在 Entity Framework 6 中已經變更，而且遷移歷程記錄資料表不再標示系統資料表。</span><span class="sxs-lookup"><span data-stu-id="72bc2-112">This has changed in Entity Framework 6 however and the migrations history table is no longer marked a system table.</span></span>

## <a name="why-customize-migrations-history-table"></a><span data-ttu-id="72bc2-113">為何要自訂遷移歷程記錄資料表？</span><span class="sxs-lookup"><span data-stu-id="72bc2-113">Why customize Migrations History Table?</span></span>

<span data-ttu-id="72bc2-114">遷移歷程記錄資料表應該僅供 Code First 移轉使用，並以手動方式進行變更，以便中斷遷移。</span><span class="sxs-lookup"><span data-stu-id="72bc2-114">Migrations history table is supposed to be used solely by Code First Migrations and changing it manually can break migrations.</span></span> <span data-ttu-id="72bc2-115">但是，有時候預設設定並不適合，而且必須自訂資料表，例如：</span><span class="sxs-lookup"><span data-stu-id="72bc2-115">However sometimes the default configuration is not suitable and the table needs to be customized, for instance:</span></span>

-   <span data-ttu-id="72bc2-116">您必須變更資料行的名稱和/或 facet，才能啟用3部<sup>rd</sup> 合作物件遷移提供者</span><span class="sxs-lookup"><span data-stu-id="72bc2-116">You need to change names and/or facets of the columns to enable a 3<sup>rd</sup> party Migrations provider</span></span>
-   <span data-ttu-id="72bc2-117">您想要變更資料表的名稱</span><span class="sxs-lookup"><span data-stu-id="72bc2-117">You want to change the name of the table</span></span>
-   <span data-ttu-id="72bc2-118">您必須針對 \_ MigrationHistory 資料表使用非預設的架構 \_</span><span class="sxs-lookup"><span data-stu-id="72bc2-118">You need to use a non-default schema for the \_\_MigrationHistory table</span></span>
-   <span data-ttu-id="72bc2-119">您需要針對特定版本的內容儲存額外的資料，因此您需要將其他資料行加入至資料表</span><span class="sxs-lookup"><span data-stu-id="72bc2-119">You need to store additional data for a given version of the context and therefore you need to add an additional column to the table</span></span>

## <a name="words-of-precaution"></a><span data-ttu-id="72bc2-120">預防措施的單字</span><span class="sxs-lookup"><span data-stu-id="72bc2-120">Words of precaution</span></span>

<span data-ttu-id="72bc2-121">變更遷移歷程記錄資料表的功能強大，但您必須小心不要濫用這個方法。</span><span class="sxs-lookup"><span data-stu-id="72bc2-121">Changing the migration history table is powerful but you need to be careful to not overdo it.</span></span> <span data-ttu-id="72bc2-122">EF runtime 目前不會檢查自訂的遷移歷程記錄資料表是否與執行時間相容。</span><span class="sxs-lookup"><span data-stu-id="72bc2-122">EF runtime currently does not check whether the customized migrations history table is compatible with the runtime.</span></span> <span data-ttu-id="72bc2-123">如果不是您的應用程式，可能會在執行時間中斷，或以無法預期的方式運作。</span><span class="sxs-lookup"><span data-stu-id="72bc2-123">If it is not your application may break at runtime or behave in unpredictable ways.</span></span> <span data-ttu-id="72bc2-124">如果您對每個資料庫使用多個內容，這會更重要，因為在這種情況下，多個內容可以使用相同的遷移歷程記錄資料表來儲存有關遷移的資訊。</span><span class="sxs-lookup"><span data-stu-id="72bc2-124">This is even more important if you use multiple contexts per database in which case multiple contexts can use the same migration history table to store information about migrations.</span></span>

## <a name="how-to-customize-migrations-history-table"></a><span data-ttu-id="72bc2-125">如何自訂遷移歷程記錄資料表？</span><span class="sxs-lookup"><span data-stu-id="72bc2-125">How to customize Migrations History Table?</span></span>

<span data-ttu-id="72bc2-126">開始之前，您必須先知道您只能自訂「遷移歷程記錄」資料表，然後再套用第一個遷移。</span><span class="sxs-lookup"><span data-stu-id="72bc2-126">Before you start you need to know that you can customize the migrations history table only before you apply the first migration.</span></span> <span data-ttu-id="72bc2-127">現在到程式碼。</span><span class="sxs-lookup"><span data-stu-id="72bc2-127">Now, to the code.</span></span>

<span data-ttu-id="72bc2-128">首先，您必須建立一個衍生自 HistoryCoNtext 類別的類別（class）。</span><span class="sxs-lookup"><span data-stu-id="72bc2-128">First, you will need to create a class derived from System.Data.Entity.Migrations.History.HistoryContext class.</span></span> <span data-ttu-id="72bc2-129">HistoryCoNtext 類別衍生自 DbCoNtext 類別，因此設定遷移歷程記錄資料表與使用流暢 API 來設定 EF 模型非常類似。</span><span class="sxs-lookup"><span data-stu-id="72bc2-129">The HistoryContext class is derived from the DbContext class so configuring the migrations history table is very similar to configuring EF models with fluent API.</span></span> <span data-ttu-id="72bc2-130">您只需要覆寫 OnModelCreating 方法，並使用流暢的 API 來設定資料表。</span><span class="sxs-lookup"><span data-stu-id="72bc2-130">You just need to override the OnModelCreating method and use fluent API to configure the table.</span></span>

>[!NOTE]
> <span data-ttu-id="72bc2-131">一般來說，當您設定 EF 模型時，您不需要呼叫 base。從覆寫的 OnModelCreating 方法中 OnModelCreating ( # A1，因為 DbCoNtext. OnModelCreating ( # A3 有空白主體。</span><span class="sxs-lookup"><span data-stu-id="72bc2-131">Typically when you configure EF models you don’t need to call base.OnModelCreating() from the overridden OnModelCreating method since the DbContext.OnModelCreating() has empty body.</span></span> <span data-ttu-id="72bc2-132">這不是設定遷移歷程記錄資料表的情況。</span><span class="sxs-lookup"><span data-stu-id="72bc2-132">This is not the case when configuring the migrations history table.</span></span> <span data-ttu-id="72bc2-133">在此案例中，第一件事是在 OnModelCreating ( # A1 覆寫是實際呼叫基底。OnModelCreating ( # A3。</span><span class="sxs-lookup"><span data-stu-id="72bc2-133">In this case the first thing to do in your OnModelCreating() override is to actually call base.OnModelCreating().</span></span> <span data-ttu-id="72bc2-134">這會以您接著在覆寫方法中進行調整的預設方式來設定遷移歷程記錄資料表。</span><span class="sxs-lookup"><span data-stu-id="72bc2-134">This will configure the migrations history table in the default way which you then tweak in the overriding method.</span></span>

<span data-ttu-id="72bc2-135">假設您想要重新命名遷移歷程記錄資料表，並將它放入名為 "admin" 的自訂架構。</span><span class="sxs-lookup"><span data-stu-id="72bc2-135">Let’s say you want to rename the migrations history table and put it to a custom schema called “admin”.</span></span> <span data-ttu-id="72bc2-136">此外，您的 DBA 希望您將 MigrationId 資料行重新命名為「遷移 \_ 識別碼」。</span><span class="sxs-lookup"><span data-stu-id="72bc2-136">In addition your DBA would like you to rename the MigrationId column to Migration\_ID.</span></span> <span data-ttu-id="72bc2-137">您可以藉由建立下列衍生自 HistoryCoNtext 的類別來達成此目的：</span><span class="sxs-lookup"><span data-stu-id="72bc2-137"> You could achieve this by creating the following class derived from HistoryContext:</span></span>

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

<span data-ttu-id="72bc2-138">當您的自訂 HistoryCoNtext 就緒之後，您必須透過以 [程式碼為基礎](https://msdn.com/data/jj680699)的設定註冊，讓 EF 知道它：</span><span class="sxs-lookup"><span data-stu-id="72bc2-138">Once your custom HistoryContext is ready you need to make EF aware of it by registering it via [code-based configuration](https://msdn.com/data/jj680699):</span></span>

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

<span data-ttu-id="72bc2-139">這就是這麼多。</span><span class="sxs-lookup"><span data-stu-id="72bc2-139">That’s pretty much it.</span></span> <span data-ttu-id="72bc2-140">現在您可以移至封裝管理員主控台、啟用-遷移、新增-遷移和最後更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="72bc2-140">Now you can go to the Package Manager Console, Enable-Migrations, Add-Migration and finally Update-Database.</span></span> <span data-ttu-id="72bc2-141">這應該會導致依據您在 HistoryCoNtext 衍生類別中指定的詳細資料，將已設定的遷移歷程記錄資料表加入至資料庫。</span><span class="sxs-lookup"><span data-stu-id="72bc2-141">This should result in adding to the database a migrations history table configured according to the details you specified in your HistoryContext derived class.</span></span>

![遷移歷程記錄資料表](~/ef6/media/database.png)
