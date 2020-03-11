---
title: 連接管理-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: ecaa5a27-b19e-4bf9-8142-a3fb00642270
ms.openlocfilehash: a6352bbbc38c38bd5f30536736ec969056df2c7d
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417984"
---
# <a name="connection-management"></a>連線管理
此頁面描述將連接傳遞至內容和**Database. Open （）** API 的功能時，Entity Framework 的行為。  

## <a name="passing-connections-to-the-context"></a>傳遞連接至內容  

### <a name="behavior-for-ef5-and-earlier-versions"></a>EF5 和較早版本的行為  

有兩個接受連接的函式：  

``` csharp
public DbContext(DbConnection existingConnection, bool contextOwnsConnection)
public DbContext(DbConnection existingConnection, DbCompiledModel model, bool contextOwnsConnection)
```  

您可以使用這些方法，但您必須解決幾個限制：  

1. 如果您將開啟的連接傳遞給其中一個，則當架構第一次嘗試使用它時，就會擲回 InvalidOperationException，指出它無法重新開啟已開啟的連接。  
2. CoNtextOwnsConnection 旗標會解讀為表示在處置內容時是否應處置基礎存放區連接。 但是，不論該設定為何，當處置內容時，存放區連接一律會關閉。 因此，如果您有一個以上的 DbCoNtext 具有相同的連線，則會先將其處置，否則會關閉連接（同樣地，如果您已使用 DbCoNtext 混合現有的 ADO.NET 連線，DbCoNtext 將一律會在處置時關閉連接）.  

藉由傳遞已關閉的連接，而且只執行會在建立所有內容之後開啟它的程式碼，就可以解決上述第一項限制：  

``` csharp
using System.Collections.Generic;
using System.Data.Common;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.EntityClient;
using System.Linq;

namespace ConnectionManagementExamples
{
    class ConnectionManagementExampleEF5
    {         
        public static void TwoDbContextsOneConnection()
        {
            using (var context1 = new BloggingContext())
            {
                var conn =
                    ((EntityConnection)  
                        ((IObjectContextAdapter)context1).ObjectContext.Connection)  
                            .StoreConnection;

                using (var context2 = new BloggingContext(conn, contextOwnsConnection: false))
                {
                    context2.Database.ExecuteSqlCommand(
                        @"UPDATE Blogs SET Rating = 5" +
                        " WHERE Name LIKE '%Entity Framework%'");

                    var query = context1.Posts.Where(p => p.Blog.Rating > 5);
                    foreach (var post in query)
                    {
                        post.Title += "[Cool Blog]";
                    }
                    context1.SaveChanges();
                }
            }
        }
    }
}
```  

第二個限制只是表示您需要避免處置任何 DbCoNtext 物件，直到您準備好讓連接關閉為止。  

### <a name="behavior-in-ef6-and-future-versions"></a>EF6 和未來版本中的行為  

在 EF6 和未來版本中，DbCoNtext 具有相同的兩個函式，但不再要求傳遞至該函式的連接在收到時關閉。 這現在是可行的：  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace ConnectionManagementExamples
{
    class ConnectionManagementExample
    {
        public static void PassingAnOpenConnection()
        {
            using (var conn = new SqlConnection("{connectionString}"))
            {
                conn.Open();

                var sqlCommand = new SqlCommand();
                sqlCommand.Connection = conn;
                sqlCommand.CommandText =
                    @"UPDATE Blogs SET Rating = 5" +
                     " WHERE Name LIKE '%Entity Framework%'";
                sqlCommand.ExecuteNonQuery();

                using (var context = new BloggingContext(conn, contextOwnsConnection: false))
                {
                    var query = context.Posts.Where(p => p.Blog.Rating > 5);
                    foreach (var post in query)
                    {
                        post.Title += "[Cool Blog]";
                    }
                    context.SaveChanges();
                }

                var sqlCommand2 = new SqlCommand();
                sqlCommand2.Connection = conn;
                sqlCommand2.CommandText =
                    @"UPDATE Blogs SET Rating = 7" +
                     " WHERE Name LIKE '%Entity Framework Rocks%'";
                sqlCommand2.ExecuteNonQuery();
            }
        }
    }
}
```  

此外，coNtextOwnsConnection 旗標現在會控制是否要在處置 DbCoNtext 時關閉和處置連接。 因此，在上述範例中，當內容被處置（行32）時，不會關閉連接，因為它在舊版 EF 中已經存在，而是在處理連接本身時（行40）。  

當然，DbCoNtext 還是可以控制連接（只要將 coNtextOwnsConnection 設為 true，或使用其中一個其他的函式）。  

> [!NOTE]
> 在此新模型中使用交易時，還有一些額外的考慮。 如需詳細資訊，請參閱[使用交易](~/ef6/saving/transactions.md)。  

## <a name="databaseconnectionopen"></a>連接。開啟（）  

### <a name="behavior-for-ef5-and-earlier-versions"></a>EF5 和較早版本的行為  

在 EF5 和較舊版本中，有一個 bug，讓**ObjectCoNtext**不會更新，以反映基礎存放區連接的真正狀態。 例如，如果您執行下列程式碼，您可以傳回狀態 [**已關閉**]，即使基礎存放區連接已**開啟**也是一樣。  

``` csharp
((IObjectContextAdapter)context).ObjectContext.Connection.State
```  

另外，如果您藉由呼叫 [資料庫] 開啟資料庫連接，請開啟（），直到下一次執行查詢或呼叫需要資料庫連接的任何專案時（例如 SaveChanges （）），但在基礎存放區之後，才會開啟連接將會關閉。 然後，每當需要另一個資料庫作業時，內容就會重新開啟並重新關閉連接：  

``` csharp
using System;
using System.Data;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.EntityClient;

namespace ConnectionManagementExamples
{
    public class DatabaseOpenConnectionBehaviorEF5
    {
        public static void DatabaseOpenConnectionBehavior()
        {
            using (var context = new BloggingContext())
            {
                // At this point the underlying store connection is closed

                context.Database.Connection.Open();

                // Now the underlying store connection is open  
                // (though ObjectContext.Connection.State will report closed)

                var blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);

                // The underlying store connection is still open  

                context.SaveChanges();

                // After SaveChanges() the underlying store connection is closed  
                // Each SaveChanges() / query etc now opens and immediately closes
                // the underlying store connection

                blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);
                context.SaveChanges();
            }
        }
    }
}
```  

### <a name="behavior-in-ef6-and-future-versions"></a>EF6 和未來版本中的行為  

在 EF6 和未來版本中，如果呼叫程式碼選擇藉由呼叫內容來開啟連接，我們就採取了方法。連接. Open （），這樣做有一個很好的理由，而且架構會假設它想要控制連接的開啟和關閉，而且不會再自動關閉連接。  

> [!NOTE]
> 這可能會導致長時間開啟的連線，因此請小心使用。  

我們也更新了程式碼，讓 ObjectCoNtext 的狀態能夠正確追蹤基礎連接的狀態。  

``` csharp
using System;
using System.Data;
using System.Data.Entity;
using System.Data.Entity.Core.EntityClient;
using System.Data.Entity.Infrastructure;

namespace ConnectionManagementExamples
{
    internal class DatabaseOpenConnectionBehaviorEF6
    {
        public static void DatabaseOpenConnectionBehavior()
        {
            using (var context = new BloggingContext())
            {
                // At this point the underlying store connection is closed

                context.Database.Connection.Open();

                // Now the underlying store connection is open and the
                // ObjectContext.Connection.State correctly reports open too

                var blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);
                context.SaveChanges();

                // The underlying store connection remains open for the next operation  

                blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);
                context.SaveChanges();

                // The underlying store connection is still open

           } // The context is disposed – so now the underlying store connection is closed
        }
    }
}
```  
