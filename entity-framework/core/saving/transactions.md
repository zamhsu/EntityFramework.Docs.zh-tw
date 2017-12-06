---
title: "交易的 EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: d3e6515b-8181-482c-a790-c4a6778748c1
ms.technology: entity-framework-core
uid: core/saving/transactions
ms.openlocfilehash: a2f890c0af1e83cbcc1d40d68540ff7132a9bafd
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="using-transactions"></a>使用交易

交易可讓數個資料庫作業，以不可部分完成的方式處理。 認可交易時，如果所有的作業會成功套用至資料庫中。 如果回復交易，所有作業都不會套用至資料庫。

> [!TIP]  
> 您可以檢視這篇文章[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Transactions/)GitHub 上。

## <a name="default-transaction-behavior"></a>預設的交易行為

根據預設，如果資料庫提供者支援交易，所有變更的單一呼叫中`SaveChanges()`會套用在交易中。 如果有任何變更失敗，然後回復交易，並變更不會套用至資料庫。 這表示`SaveChanges()`保證完全成功，或讓資料庫發生錯誤時，不想修改。

對於大部分的應用程式，此預設行為已足夠。 如果應用程式的需求覺得有必要，您應該僅以手動方式控制交易。

## <a name="controlling-transactions"></a>控制交易

您可以使用`DbContext.Database`API 來開始、 認可和回復交易。 下列範例示範兩個`SaveChanges()`作業和 LINQ 查詢在單一交易中執行。

並非所有的資料庫提供者支援交易。 某些提供者可能會擲回或無作業 Api 呼叫交易時。

<!-- [!code-csharp[Main](samples/core/Saving/Saving/Transactions/ControllingTransaction/Sample.cs?highlight=3,17,18,19)] -->
``` csharp
        using (var context = new BloggingContext())
        {
            using (var transaction = context.Database.BeginTransaction())
            {
                try
                {
                    context.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/dotnet" });
                    context.SaveChanges();

                    context.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/visualstudio" });
                    context.SaveChanges();

                    var blogs = context.Blogs
                        .OrderBy(b => b.Url)
                        .ToList();

                    // Commit transaction if all commands succeed, transaction will auto-rollback
                    // when disposed if either commands fails
                    transaction.Commit();
                }
                catch (Exception)
                {
                    // TODO: Handle failure
                }
            }
        }
```

## <a name="cross-context-transaction-relational-databases-only"></a>跨內容交易 （只有關聯式資料庫）

您也可以在多個內容執行個體之間共用的交易。 這項功能時才使用關聯式資料庫提供者，因為它需要使用`DbTransaction`和`DbConnection`，這專屬於關聯式資料庫。

若要共用交易，內容必須共用兩者`DbConnection`和`DbTransaction`。

### <a name="allow-connection-to-be-externally-provided"></a>允許從外部提供的連線

共用`DbConnection`需要建構它時，將連接傳遞到內容的功能。

允許的最簡單方式`DbConnection`外部提供，是要停止使用`DbContext.OnConfiguring`方法來設定內容和外部建立`DbContextOptions`並將其傳遞至內容的建構函式。

> [!TIP]  
> `DbContextOptionsBuilder`是您在中使用的 API`DbContext.OnConfiguring`若要設定內容，您現在要建立外部使用`DbContextOptions`。

<!-- [!code-csharp[Main](samples/core/Saving/Saving/Transactions/SharingTransaction/Sample.cs?highlight=3,4,5)] -->
``` csharp
    public class BloggingContext : DbContext
    {
        public BloggingContext(DbContextOptions<BloggingContext> options)
            : base(options)
        { }

        public DbSet<Blog> Blogs { get; set; }
    }
```

若要繼續使用替代方法是`DbContext.OnConfiguring`，但接受`DbConnection`，是儲存並接著用於`DbContext.OnConfiguring`。

``` csharp
public class BloggingContext : DbContext
{
    private DbConnection _connection;

    public BloggingContext(DbConnection connection)
    {
      _connection = connection;
    }

    public DbSet<Blog> Blogs { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer(_connection);
    }
}
```

### <a name="share-connection-and-transaction"></a>共用連接及交易

您現在可以建立多個共用相同連接的內容執行個體。 然後使用`DbContext.Database.UseTransaction(DbTransaction)`API 來編列這兩個相同交易中的內容。

<!-- [!code-csharp[Main](samples/core/Saving/Saving/Transactions/SharingTransaction/Sample.cs?highlight=1,2,3,7,16,23,24,25)] -->
``` csharp
        var options = new DbContextOptionsBuilder<BloggingContext>()
            .UseSqlServer(new SqlConnection(connectionString))
            .Options;

        using (var context1 = new BloggingContext(options))
        {
            using (var transaction = context1.Database.BeginTransaction())
            {
                try
                {
                    context1.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/dotnet" });
                    context1.SaveChanges();

                    using (var context2 = new BloggingContext(options))
                    {
                        context2.Database.UseTransaction(transaction.GetDbTransaction());

                        var blogs = context2.Blogs
                            .OrderBy(b => b.Url)
                            .ToList();
                    }

                    // Commit transaction if all commands succeed, transaction will auto-rollback
                    // when disposed if either commands fails
                    transaction.Commit();
                }
                catch (Exception)
                {
                    // TODO: Handle failure
                }
            }
        }
```

## <a name="using-external-dbtransactions-relational-databases-only"></a>使用外部 DbTransactions （只有關聯式資料庫）

如果您使用多個資料存取技術來存取關聯式資料庫，您可能想要共用這些不同的技術所執行作業之間的交易。

下列範例中，示範如何在相同交易中執行 ADO.NET SqlClient 和 Entity Framework Core 作業。

<!-- [!code-csharp[Main](samples/core/Saving/Saving/Transactions/ExternalDbTransaction/Sample.cs?highlight=4,10,21,26,27,28)] -->
``` csharp
        var connection = new SqlConnection(connectionString);
        connection.Open();

        using (var transaction = connection.BeginTransaction())
        {
            try
            {
                // Run raw ADO.NET command in the transaction
                var command = connection.CreateCommand();
                command.Transaction = transaction;
                command.CommandText = "DELETE FROM dbo.Blogs";
                command.ExecuteNonQuery();

                // Run an EF Core command in the transaction
                var options = new DbContextOptionsBuilder<BloggingContext>()
                    .UseSqlServer(connection)
                    .Options;

                using (var context = new BloggingContext(options))
                {
                    context.Database.UseTransaction(transaction);
                    context.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/dotnet" });
                    context.SaveChanges();
                }

                // Commit transaction if all commands succeed, transaction will auto-rollback
                // when disposed if either commands fails
                transaction.Commit();
            }
            catch (System.Exception)
            {
                // TODO: Handle failure
            }
```
