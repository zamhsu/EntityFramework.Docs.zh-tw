---
title: 使用交易-EF6
description: 使用 Entity Framework 6 中的交易
author: divega
ms.date: 10/23/2016
ms.assetid: 0d0f1824-d781-4cb3-8fda-b7eaefced1cd
uid: ef6/saving/transactions
ms.openlocfilehash: 65eebd82d4f9c583885af72d5b3cffd79fedf623
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619849"
---
# <a name="working-with-transactions"></a>使用交易
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。  

本檔將說明如何使用 EF6 中的交易，包括自 EF5 以來加入的增強功能，讓您輕鬆處理交易。  

## <a name="what-ef-does-by-default"></a>EF 預設的功能  

在所有版本的 Entity Framework 中，每當您執行 **SaveChanges ( # B1 ** 來插入、更新或刪除資料庫時，架構會將該作業包裝在交易中。 此交易的持續時間只有足夠的時間可執行作業，然後完成。 當您執行其他這類作業時，就會啟動新的交易。  

從 EF6 開始 **Database.ExecuteSqlCommand ( # B1 ** 預設會將命令包裝在交易中（如果尚未存在的話）。 這種方法有多載，可讓您視需要覆寫此行為。 此外，透過 Api （例如 **ObjectContext.ExecuteFunction ( # B1 ** ）在模型中包含的預存程式執行也會執行相同的 (，但目前無法覆寫) 的預設行為。  

在任一種情況下，交易的隔離等級就是資料庫提供者視為其預設設定的任何隔離等級。 例如，根據預設，在 SQL Server 此為讀取認可。  

Entity Framework 不會將查詢包裝在交易中。  

這項預設功能適合許多使用者，如果有，就不需要在 EF6 中進行任何不同的動作;只要像往常一樣撰寫程式碼即可。  

不過，某些使用者需要對其交易有更大的控制權–這會在下列各節中說明。  

## <a name="how-the-apis-work"></a>Api 的運作方式  

在 EF6 之前 Entity Framework 堅持認為開啟資料庫連接本身時 (它會在傳遞已) 開啟的連接時擲回例外狀況。 由於交易只能在開啟的連接上啟動，這表示使用者唯一能夠將數個作業包裝成一個交易的方式，就是使用[TransactionScope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx)或使用**ObjectCoNtext. connection**屬性，並開始直接在傳回的**EntityConnection**物件上呼叫**open ( # B1**和**BeginTransaction ( # B3** 。 此外，如果您已在基礎資料庫連接上啟動交易，則與資料庫聯繫的 API 呼叫將會失敗。  

> [!NOTE]
> Entity Framework 6 中已移除僅接受已關閉連接的限制。 如需詳細資訊，請參閱 [連接管理](xref:ef6/fundamentals/connection-management)。  

從 EF6 開始，架構現在提供：  

1. **BeginTransaction ( # B1 ** ：更簡單的方法，讓使用者在現有的 DbCoNtext 內自行啟動及完成交易，允許在相同的交易內結合數項作業，因此全部認可或全部回復為一個。 它也可讓使用者更輕鬆地指定交易的隔離等級。  
2. **UseTransaction ( # B1 ** ：這可讓 DbCoNtext 使用在 Entity Framework 以外啟動的交易。  

### <a name="combining-several-operations-into-one-transaction-within-the-same-context"></a>將數個作業結合到相同內容中的一個交易  

**BeginTransaction ( # B1 ** 有兩個覆寫–一個接受明確的 [IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx) ，另一個則不接受任何引數，並且會使用基礎資料庫提供者的預設 IsolationLevel。 這兩個覆寫會傳回 **DbCoNtextTransaction** 物件，此物件提供 **Commit ( # B1 ** 和 **Rollback ( # B3 ** 方法，這些方法會在基礎存放區交易上執行認可和回復。  

**DbCoNtextTransaction**的目的是要在認可或回復之後處置。 完成這項工作的一個簡單方式是 **使用 ( ... ) {...}** 當 using 區塊完成時，將會自動呼叫 **Dispose ( # B1 ** 的語法：  

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
            }
        }
    }
}
```  

> [!NOTE]
> 開始交易時，必須開啟基礎存放區連接。 因此，呼叫 BeginTransaction ( # A1 會開啟連接（如果尚未開啟）。 如果 DbCoNtextTransaction 開啟連接，則會在呼叫 Dispose ( # A1 時將它關閉。  

### <a name="passing-an-existing-transaction-to-the-context"></a>將現有交易傳遞至內容  

有時候您會想要在範圍內更廣泛的交易，包括在相同資料庫上的作業，但完全不包含在 EF 的外部。 若要完成此動作，您必須開啟連接並自行啟動交易，然後告訴 EF a) 使用已經開啟的資料庫連接，b) 使用該連接上的現有交易。  

若要這樣做，您必須在內容類別上定義和使用函式，而該函式繼承自其中一個 DbCoNtext 的函式，而該函式會) 現有的連接參數，而 ii) coNtextOwnsConnection 布林值。  

> [!NOTE]
> 在此案例中呼叫 coNtextOwnsConnection 旗標時，必須設定為 false。 這點很重要，因為它會通知 Entity Framework 它在完成時不應該關閉連接 (例如，請參閱下面的第4行) ：  

``` csharp
using (var conn = new SqlConnection("..."))
{
    conn.Open();
    using (var context = new BloggingContext(conn, contextOwnsConnection: false))
    {
    }
}
```  

此外，您必須自行啟動交易 (包括 IsolationLevel 如果您想要避免預設設定) 並讓 Entity Framework 知道連線上已啟動現有的交易 (請參閱以下) 的行33。  

然後，您可以直接在 SqlConnection 本身或 DbCoNtext 上執行資料庫作業。 所有這類作業都是在一個交易內執行。 您必須負責認可或回復交易，以及在其上呼叫 Dispose ( # A1，以及關閉和處置資料庫連接。 例如：  

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
        static void UsingExternalTransaction()
        {
            using (var conn = new SqlConnection("..."))
            {
               conn.Open();

               using (var sqlTxn = conn.BeginTransaction(System.Data.IsolationLevel.Snapshot))
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
            }
        }
    }
}
```  

### <a name="clearing-up-the-transaction"></a>清除交易

您可以將 null 傳遞給 UseTransaction ( # A1，以清除 Entity Framework 對目前交易的知識。 當您這樣做時，Entity Framework 將不會認可或回復現有的交易，因此請小心使用，並確定這是您想要執行的動作。  

### <a name="errors-in-usetransaction"></a>UseTransaction 中的錯誤

如果您在下列情況中傳遞交易，則會看到 UseTransaction ( # A1 的例外狀況：  
- Entity Framework 已經有現有交易  
- Entity Framework 已在 TransactionScope 內運作  
- 傳遞的交易中的連線物件為 null。 也就是說，交易不會與連接產生關聯–這通常是該交易已完成的正負號  
- 傳遞的交易中的連線物件不符合 Entity Framework 的連接。  

## <a name="using-transactions-with-other-features"></a>搭配其他功能使用交易  

本節將詳細說明上述交易與之互動的方式：  

- 恢復連線  
- 非同步方法  
- TransactionScope 交易  

### <a name="connection-resiliency"></a>連接恢復功能  

新的連接復原功能不適用於使用者起始的交易。 如需詳細資訊，請參閱 [重試執行策略](xref:ef6/fundamentals/connection-resiliency/retry-logic#user-initiated-transactions-are-not-supported)。  

### <a name="asynchronous-programming"></a>非同步程式設計  

上述各節所述的方法，不需要進一步的選項或設定，就能使用 [非同步查詢和儲存方法](xref:ef6/fundamentals/async
)。 但是請注意，根據您在非同步方法中所做的動作，這可能會導致長時間執行的交易，而這可能會導致鎖死或封鎖，而這對整體應用程式效能來說是不好的。  

### <a name="transactionscope-transactions"></a>TransactionScope 交易  

在 EF6 之前，建議的方法是使用 TransactionScope 物件來提供較大型的範圍交易：  

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

SqlConnection 和 Entity Framework 都會使用環境 TransactionScope 交易，因此會一起認可。  

從 .NET 4.5.1 TransactionScope 開始更新，也可以透過使用 [TransactionScopeAsyncFlowOption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx) 列舉來處理非同步方法：  

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

TransactionScope 方法仍有一些限制：  

- 需要 .NET 4.5.1 或更新版本才能使用非同步方法。  
- 除非您確定只有一個連線 (雲端案例不支援) 的分散式交易，否則無法在雲端案例中使用它。  
- 它無法與 UseTransaction 的上一節中的 ( # A1 方法合併。  
- 如果您發出任何 DDL，而且尚未透過 MSDTC 服務啟用分散式交易，則會擲回例外狀況。  

TransactionScope 方法的優點：  

- 如果您對指定的資料庫進行一個以上的連接，或將連接與同一個交易中不同資料庫的連接合並到一個資料庫，則它會自動將本機交易升級為分散式交易 (注意：您必須將 MSDTC 服務設定為允許這項作業) 的分散式交易。  
- 輕鬆撰寫程式碼。 如果您希望交易具有環境，並隱含地在背景中處理，而不是明確地在您控制之下，則 TransactionScope 方法可能會符合您的需要。  

總而言之，有了新的資料庫. BeginTransaction ( # A1 和 UseTransaction ( # A3 Api 以上，大部分的使用者已不再需要 TransactionScope 方法。 如果您繼續使用 TransactionScope，請留意上述限制。 建議您盡可能使用先前章節中所述的方法。  
