---
title: 使用交易-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 0d0f1824-d781-4cb3-8fda-b7eaefced1cd
ms.openlocfilehash: 26473e1e52a6044babc717d5b158ad73aac5c738
ms.sourcegitcommit: 0d36e8ff0892b7f034b765b15e041f375f88579a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2018
ms.locfileid: "44250604"
---
# <a name="working-with-transactions"></a>使用交易
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。  

本文件將說明如何使用交易在 EF6 中包括 EF5 以方便使用的交易之後，我們已新增的增強功能。  

## <a name="what-ef-does-by-default"></a>依預設沒有的 EF  

在所有版本的 Entity Framework 中，每當您執行**savechanges （)** 來插入、 更新或刪除資料庫架構會在交易中包裝該作業。 此交易執行的作業持續時間，僅足夠的時間，並再完成。 當您執行這類另一項作業會啟動新的交易。  

從 EF6 **Database.ExecuteSqlCommand()** 依預設會自動換行命令在交易中如果其中一個已不存在。 有這個方法可讓您覆寫這個行為，如果您想要的多載。 也在 EF6 執行，例如透過 Api 模型中包含的預存程序**ObjectContext.ExecuteFunction()** 會執行相同，差別在於無法目前會覆寫預設行為）。  

在任一情況下，交易的隔離等級會是任何隔離等級的資料庫提供者會考慮其預設值。 根據預設，比方說，在 SQL Server 這是 READ COMMITTED。  

Entity Framework 不會包裝在交易中的查詢。  

這項功能預設是適合大量的使用者，和如果因此就不需要執行什麼工作在 EF6;如往常一般，只需撰寫程式碼。  

不過有些使用者需要更充分地掌控其交易 – 這會涵蓋下列各節。  

## <a name="how-the-apis-work"></a>Api 的運作方式  

在 EF6 Entity Framework 之前堅持開啟資料庫連接本身 （如果它已傳遞原本就已經開啟的連接，它發生例外狀況）。 因為只能在開啟的連接上啟動交易，這表示使用者無法包裝成一筆交易的數個作業的唯一方式是使用[TransactionScope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx) ，或使用**ObjectContext.Connection**屬性，並開始呼叫**open （)** 並**BeginTransaction()** 直接對傳回**EntityConnection**物件。 此外，連絡資料庫 API 呼叫也會失敗，如果您已在自己的基礎資料庫連接上開始交易。  

> [!NOTE]
> Entity Framework 6 已移除的只接受已關閉的連線限制。 如需詳細資訊，請參閱 <<c0> [ 連線的管理](~/ef6/fundamentals/connection-management.md)。  

從 EF6 framework 現在提供：  

1. **Database.BeginTransaction()** ： 啟動與完成在現有的 DbContext – 可讓數個作業結合在相同交易內的交易本身使用者更容易的方法，因此所有認可或全部回復為其中一個。 它也可讓使用者更輕鬆地指定交易的隔離等級。  
2. **Database.UseTransaction()** ： 可讓使用 Entity Framework 之外啟動的交易 DbContext。  

### <a name="combining-several-operations-into-one-transaction-within-the-same-context"></a>結合到一個異動相同內容中的數個作業  

**Database.BeginTransaction()** 有兩個覆寫 – 其中一個會採用明確[IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx) ，另一個會採用任何引數，並使用從基礎資料庫提供者的 IsolationLevel 預設值。 傳回兩個覆寫**DbContextTransaction**物件，其提供**commit （)** 並**rollback （)** 對基礎存放區中的認可和回復方法交易。  

**DbContextTransaction**要認可或回復之後加以處置。 其中一個簡便的方式完成這項作業是**using(...){...}** 語法會自動呼叫這**dispose （)** using 區塊時完成：  

``` csharp
using System;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace TransactionsExamples
{
    class TransactionsExample
    {
        static void StartOwnTransactionWithinContext()
        {
            using (var context = new BloggingContext())
            {
                using (var dbContextTransaction = context.Database.BeginTransaction())
                {
                    try
                    {
                        context.Database.ExecuteSqlCommand(
                            @"UPDATE Blogs SET Rating = 5" +
                                " WHERE Name LIKE '%Entity Framework%'"
                            );

                        var query = context.Posts.Where(p => p.Blog.Rating >= 5);
                        foreach (var post in query)
                        {
                            post.Title += "[Cool Blog]";
                        }

                        context.SaveChanges();

                        dbContextTransaction.Commit();
                    }
                    catch (Exception)
                    {
                        dbContextTransaction.Rollback();
                    }
                }
            }
        }
    }
}
```  

> [!NOTE]
> 開始交易需要基礎存放區連接已開啟。 如果尚未開啟，因此，呼叫 Database.BeginTransaction() 會開啟連接。 如果 DbContextTransaction 開啟連接然後它會關閉它呼叫 dispose （） 時。  

### <a name="passing-an-existing-transaction-to-the-context"></a>將現有的交易傳遞至內容  

有時您想要的交易，甚至會更廣範圍內且完全包含作業相同的資料庫上，但在 EF 外部。 若要這麼做必須開啟連接和自行啟動交易，然後告知 EF a） 若要使用已開啟的資料庫連接，以及 b） 若要在該連接上使用現有的交易。  

若要這樣做，您必須定義，並使用您的內容類別繼承自一個 DbContext 建構函式需要 i） 現有的連線參數和 ii) contextOwnsConnection 布林建構函式。  

> [!NOTE]
> ContextOwnsConnection 旗標必須設定為 false，在此案例中呼叫時。 這十分重要，因為它會通知 Entity Framework，它應該不關閉連線時執行的動作 （例如，請參閱下列 4 行）：  

``` csharp
using (var conn = new SqlConnection("..."))
{
    conn.Open();
    using (var context = new BloggingContext(conn, contextOwnsConnection: false))
    {
    }
}
```  

此外，您必須自行 （如果您想要避免預設設定，包括 IsolationLevel） 啟動交易，並可讓 Entity Framework 知道現有的交易已經開始在此連接上 （請參閱列下方的 33）。  

然後，您可自行直接 SqlConnection 本身，或在 DbContext 上執行資料庫作業。 一筆交易內執行所有這類作業。 您負責認可或回復交易並呼叫 dispose （），以及關閉和處置的資料庫連接。 例如:   

``` csharp
using System;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
sing System.Transactions;

namespace TransactionsExamples
{
     class TransactionsExample
     {
        static void UsingExternalTransaction()
        {
            using (var conn = new SqlConnection("..."))
            {
               conn.Open();

               using (var sqlTxn = conn.BeginTransaction(System.Data.IsolationLevel.Snapshot))
               {
                   try
                   {
                       var sqlCommand = new SqlCommand();
                       sqlCommand.Connection = conn;
                       sqlCommand.Transaction = sqlTxn;
                       sqlCommand.CommandText =
                           @"UPDATE Blogs SET Rating = 5" +
                            " WHERE Name LIKE '%Entity Framework%'";
                       sqlCommand.ExecuteNonQuery();

                       using (var context =  
                         new BloggingContext(conn, contextOwnsConnection: false))
                        {
                            context.Database.UseTransaction(sqlTxn);

                            var query =  context.Posts.Where(p => p.Blog.Rating >= 5);
                            foreach (var post in query)
                            {
                                post.Title += "[Cool Blog]";
                            }
                           context.SaveChanges();
                        }

                        sqlTxn.Commit();
                    }
                    catch (Exception)
                    {
                        sqlTxn.Rollback();
                    }
                }
            }
        }
    }
}
```  

### <a name="clearing-up-the-transaction"></a>清除交易

您可以傳遞 null 給 Database.UseTransaction() 以清除目前交易的 Entity Framework 的知識。 Entity Framework 會將未認可或復原現有的交易時這樣做，因此請小心使用，並只有當您確定這是您要做什麼。  

### <a name="errors-in-usetransaction"></a>UseTransaction 中的錯誤

如果您傳遞的交易，您會看到從 Database.UseTransaction() 例外狀況時：  
- Entity Framework 已有現有的交易  
- TransactionScope 內已運作 entity Framework  
- 傳遞的交易中的連接物件為 null。 也就是因為交易並未與連接相關聯，– 這通常是表示該交易已完成  
- 連接物件中傳遞的交易與 Entity Framework 的連線不相符。  

## <a name="using-transactions-with-other-features"></a>使用其他功能的交易  

本節將詳細說明上述的交易與互動的方式：  

- 連線恢復功能  
- 非同步方法  
- TransactionScope 交易  

### <a name="connection-resiliency"></a>連線復原能力  

新的連接恢復功能不適用於使用者起始交易。 如需詳細資訊，請參閱 <<c0> [ 重試執行策略](~/ef6/fundamentals/connection-resiliency/retry-logic.md#user-initiated-transactions-are-not-supported)。  

### <a name="asynchronous-programming"></a>非同步程式設計  

前幾節所述的方法不需要進一步選項或設定值來處理[非同步查詢並儲存方法](~/ef6/fundamentals/async.md
)。 但請注意，根據您執行的非同步方法內，這可能會導致長時間執行交易-這可能又會導致死結或封鎖的不好的整體應用程式的效能。  

### <a name="transactionscope-transactions"></a>TransactionScope 交易  

在 EF6 之前提供較大範圍的交易的建議的方式是使用 TransactionScope 物件：  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace TransactionsExamples
{
    class TransactionsExample
    {
        static void UsingTransactionScope()
        {
            using (var scope = new TransactionScope(TransactionScopeOption.Required))
            {
                using (var conn = new SqlConnection("..."))
                {
                    conn.Open();

                    var sqlCommand = new SqlCommand();
                    sqlCommand.Connection = conn;
                    sqlCommand.CommandText =
                        @"UPDATE Blogs SET Rating = 5" +
                            " WHERE Name LIKE '%Entity Framework%'";
                    sqlCommand.ExecuteNonQuery();

                    using (var context =
                        new BloggingContext(conn, contextOwnsConnection: false))
                    {
                        var query = context.Posts.Where(p => p.Blog.Rating > 5);
                        foreach (var post in query)
                        {
                            post.Title += "[Cool Blog]";
                        }
                        context.SaveChanges();
                    }
                }

                scope.Complete();
            }
        }
    }
}
```  

SqlConnection 和 Entity Framework 會同時使用環境的 TransactionScope 交易，因此會認可在一起。  

開始使用.NET 4.5.1 TransactionScope 已更新為也適用於非同步方法，透過使用[TransactionScopeAsyncFlowOption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx)列舉型別：  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace TransactionsExamples
{
    class TransactionsExample
    {
        public static void AsyncTransactionScope()
        {
            using (var scope = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled))
            {
                using (var conn = new SqlConnection("..."))
                {
                    await conn.OpenAsync();

                    var sqlCommand = new SqlCommand();
                    sqlCommand.Connection = conn;
                    sqlCommand.CommandText =
                        @"UPDATE Blogs SET Rating = 5" +
                            " WHERE Name LIKE '%Entity Framework%'";
                    await sqlCommand.ExecuteNonQueryAsync();

                    using (var context = new BloggingContext(conn, contextOwnsConnection: false))
                    {
                        var query = context.Posts.Where(p => p.Blog.Rating > 5);
                        foreach (var post in query)
                        {
                            post.Title += "[Cool Blog]";
                        }

                        await context.SaveChangesAsync();
                    }
                }
            }
        }
    }
}
```  

仍有一些限制，TransactionScope 方法：  

- 需要.NET 4.5.1 或更新版本使用非同步方法。  
- 它不能在雲端案例除非您確定您有只有一個連線 （雲端案例不支援分散式的交易）。  
- 它不能結合前一節的 Database.UseTransaction() 方法。  
- 如果發出的任何 DLL，而且您尚未啟用透過 MSDTC 服務的分散式的交易，則會擲回例外狀況。  

TransactionScope 方法的優點：  

- 它將會自動升級本機交易為分散式交易如果您讓多個連接到指定的資料庫，或結合在相同交易內的不同資料庫的連接中的一個資料庫的連接 (請注意： 您必須擁有MSDTC 服務設定為允許針對此目的的分散式的交易）。  
- 撰寫程式碼簡化。 如果您想為環境並使用隱含地在背景發出交易，而不是明確地在您控制然後 TransactionScope 方法可能比較適合您好。  

總而言之，使用新的 Database.BeginTransaction() 和上述的 Database.UseTransaction() Api TransactionScope 方法不再需要的大部分的使用者。 如果您不要繼續使用 TransactionScope 則是注意上述限制。 我們建議使用在先前章節中改為描述，可能的方法。  
