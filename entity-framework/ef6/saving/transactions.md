---
title: 使用交易-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 0d0f1824-d781-4cb3-8fda-b7eaefced1cd
ms.openlocfilehash: 7030dc675993339f72c935f6b430cead85fecb7f
ms.sourcegitcommit: c9c3e00c2d445b784423469838adc071a946e7c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68306517"
---
# <a name="working-with-transactions"></a>使用交易
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。  

本檔將說明如何在 EF6 中使用交易, 包括自 EF5 以來新增的增強功能, 讓您輕鬆處理交易。  

## <a name="what-ef-does-by-default"></a>EF 的預設功能  

在所有版本的 Entity Framework 中, 每當您在資料庫上執行**SaveChanges ()** 以進行插入、更新或刪除時, 架構就會將該作業包裝在交易中。 此交易的持續時間只夠長, 足以執行作業, 然後完成。 當您執行其他這類作業時, 就會啟動新的交易。  

從 EF6 資料庫開始, **ExecuteSqlCommand ()** 預設會將命令包裝在交易中 (如果尚未存在的話)。 有這個方法的多載可讓您視需要覆寫此行為。 此外, 在 EF6 中, 透過**ExecuteFunction ()** 之類的 api 包含在模型中的預存程式, 也會執行相同的動作 (但不會覆寫預設行為)。  

在任一情況下, 交易的隔離等級就是資料庫提供者視為其預設設定的任何隔離等級。 例如, 根據預設, SQL Server 這是讀取認可。  

Entity Framework 不會將查詢包裝在交易中。  

這項預設功能適用于許多使用者, 如果有, 則不需要在 EF6 中進行任何不同的動作;只要像往常一樣撰寫程式碼即可。  

不過, 某些使用者需要對其交易有更大的控制權, 這會在下列各節中討論。  

## <a name="how-the-apis-work"></a>Api 的工作方式  

在 Entity Framework EF6 之前, 請先開啟資料庫連接本身 (如果傳遞已經開啟的連接, 就會擲回例外狀況)。 因為交易只能在開啟的連接上啟動, 這表示使用者可以將數個作業包裝成一個交易的唯一方式, 就是使用[TransactionScope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx)或使用**ObjectCoNtext 連接**屬性, 然後啟動直接在傳回的**EntityConnection**物件上呼叫**Open ()** 和**BeginTransaction ()** 。 此外, 如果您已在基礎資料庫連接上啟動交易, 則與資料庫聯繫的 API 呼叫將會失敗。  

> [!NOTE]
> Entity Framework 6 中已移除只接受已關閉連接的限制。 如需詳細資訊, 請參閱[連接管理](~/ef6/fundamentals/connection-management.md)。  

從 EF6 開始, 架構現在提供:  

1. **BeginTransaction ()** :在現有的 DbCoNtext 中, 使用者可以更輕鬆地啟動及完成交易本身–允許在相同的交易內結合數個作業, 因此全部認可或全部復原為一。 它也可以讓使用者更輕鬆地指定交易的隔離等級。  
2. **UseTransaction ()** : 可讓 DbCoNtext 使用在 Entity Framework 外部啟動的交易。  

### <a name="combining-several-operations-into-one-transaction-within-the-same-context"></a>將數個作業結合成相同內容中的一個交易  

**BeginTransaction ()** 有兩個覆寫: 一個會接受明確的[IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx) , 另一個則會採用沒有引數, 並使用基礎資料庫提供者的預設 IsolationLevel。 這兩個覆寫都會傳回**DbCoNtextTransaction**物件, 它會提供**commit ()** 和**rollback ()** 方法, 以在基礎存放區交易上執行 commit 和 rollback。  

**DbCoNtextTransaction**的目的是要在認可或回復之後加以處置。 完成這項工作的一個簡單方法是**使用 (...){...}** 當 using 區塊完成時, 會自動呼叫**Dispose ()** 的語法:  

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
> 開始交易時, 必須開啟基礎存放區連接。 因此, 如果尚未開啟連接, 則呼叫 BeginTransaction () 將會開啟它。 如果 DbCoNtextTransaction 開啟了連接, 則會在呼叫 Dispose () 時將它關閉。  

### <a name="passing-an-existing-transaction-to-the-context"></a>將現有的交易傳遞至內容  

有時候, 您會想要在範圍中更廣泛的交易, 以及包含在相同資料庫上但完全不在 EF 外部的作業。 若要完成此動作, 您必須開啟連接並自行啟動交易, 然後告訴 EF a) 使用已開啟的資料庫連接, 以及 b) 來使用該連接上的現有交易。  

若要這麼做, 您必須在您的內容類別上定義和使用一個函式, 而該類別繼承自其中一個 DbCoNtext 的函數 (接受 i) 現有的連接參數和 ii) coNtextOwnsConnection 的布林值。  

> [!NOTE]
> 在此案例中呼叫 coNtextOwnsConnection 旗標時, 必須將它設定為 false。 這很重要, 因為它會通知 Entity Framework 它在完成時不應關閉連接 (例如, 請參閱下面的第4行):  

``` csharp
using (var conn = new SqlConnection("..."))
{
    conn.Open();
    using (var context = new BloggingContext(conn, contextOwnsConnection: false))
    {
    }
}
```  

此外, 您必須自行啟動交易 (包括 IsolationLevel, 如果您想要避免預設值), 並讓 Entity Framework 知道連線上已啟動現有的交易 (請參閱下面的第33行)。  

然後您就可以自由地直接在 SqlConnection 本身或在 DbCoNtext 上執行資料庫作業。 所有這類作業都是在單一交易中執行。 您必須負責認可或回復交易, 以及在其上呼叫 Dispose (), 以及用來關閉和處置資料庫連接。 例如：  

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

您可以將 null 傳遞給 UseTransaction (), 以清除 Entity Framework 對目前交易的瞭解。 當您這麼做時, Entity Framework 不會認可或回復現有的交易, 因此請小心使用, 而且只有在您確定這是您想要執行的動作時。  

### <a name="errors-in-usetransaction"></a>UseTransaction 中的錯誤

如果您在下列情況中傳遞交易, 您將會看到 UseTransaction () 的例外狀況:  
- Entity Framework 已經有現有的交易  
- Entity Framework 已在 TransactionScope 內運作  
- 交易中傳遞的連線物件為 null。 也就是說, 交易不會與連接相關聯–通常這是交易已完成的正負號  
- 交易中傳遞的連線物件與 Entity Framework 的連接不符。  

## <a name="using-transactions-with-other-features"></a>搭配其他功能使用交易  

本節將詳細說明上述交易如何與進行互動:  

- 連線恢復功能  
- 非同步方法  
- TransactionScope 交易  

### <a name="connection-resiliency"></a>連線復原能力  

新的連接復原功能無法與使用者起始的交易搭配使用。 如需詳細資訊, 請參閱[重試執行策略](~/ef6/fundamentals/connection-resiliency/retry-logic.md#user-initiated-transactions-are-not-supported)。  

### <a name="asynchronous-programming"></a>非同步程式設計  

前面幾節所述的方法, 不需要進一步的選項或設定, 就[能使用非同步查詢和](~/ef6/fundamentals/async.md
)儲存方法。 但請注意, 根據您在非同步方法中所執行的動作而定, 這可能會導致長時間執行的交易, 進而導致鎖死或封鎖, 而造成整體應用程式的效能不佳。  

### <a name="transactionscope-transactions"></a>TransactionScope 交易  

在 EF6 之前, 提供較大範圍交易的建議方式是使用 TransactionScope 物件:  

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

SqlConnection 和 Entity Framework 都會使用環境 TransactionScope 交易, 因此會一起認可。  

從 .NET 4.5.1 TransactionScope 開始, 已更新為也會透過使用[TransactionScopeAsyncFlowOption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx)列舉來處理非同步方法:  

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

TransactionScope 方法仍有一些限制:  

- 需要 .NET 4.5.1 或更新版本才能使用非同步方法。  
- 除非您確定只有一個連線 (雲端案例不支援分散式交易), 否則無法在雲端案例中使用它。  
- 它無法與先前章節的 UseTransaction () 方法結合。  
- 如果您發出任何 DDL, 而且尚未透過 MSDTC 服務啟用分散式交易, 它就會擲回例外狀況。  

TransactionScope 方法的優點:  

- 如果您對指定的資料庫進行多個連接, 或將連接與相同交易內的不同資料庫連接結合到某個資料庫, 它就會自動將本機交易升級為分散式交易 (注意: 您必須擁有MSDTC 服務已設定為允許此作業使用分散式交易)。  
- 輕鬆撰寫程式碼。 如果您偏好在背景中以隱含的方式處理交易, 而不是明確地進行控制, 則 TransactionScope 方法可能會符合您的效果。  

總而言之, 使用上述的新 BeginTransaction () 和 UseTransaction () Api 時, 大部分的使用者不再需要 TransactionScope 方法。 如果您繼續使用 TransactionScope, 則請注意上述限制。 我們建議您盡可能改用先前章節中所述的方法。  
