---
title: 連接管理-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: ecaa5a27-b19e-4bf9-8142-a3fb00642270
ms.openlocfilehash: dc405e1876edc850ae6e4ce4649da52635d316ae
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997901"
---
# <a name="connection-management"></a>連接管理
此頁面描述 Entity Framework 的行為，將連接傳遞至內容和功能方面**Database.Connection.Open()** API。  

## <a name="passing-connections-to-the-context"></a>傳遞至內容的連線  

### <a name="behavior-for-ef5-and-earlier-versions"></a>EF5 和較早版本的行為  

有兩個建構函式可接受的連線：  

``` csharp
public DbContext(DbConnection existingConnection, bool contextOwnsConnection)
public DbContext(DbConnection existingConnection, DbCompiledModel model, bool contextOwnsConnection)
```  

您可使用這些項目，但您必須解決幾項限制：  

1. 如果您傳遞的開啟連接至任一種然後第一次這個架構嘗試使用它就會擲回 InvalidOperationException，指出它無法重新開啟已經開啟的連線。  
2. ContextOwnsConnection 旗標則解譯成表示應該在處置內容時，處置基礎存放區連接。 但是，不論這項設定，存放區連接，一律會關閉在處置內容時。 因此如果您有多個 DbContext 利用相同連接處置任何內容，是第一次會關閉連線 （同樣地如果您有現有的 ADO.NET 連接與 DbContext，DbContext 會一律關閉連接處置時）.  

您可解決上述的第一個限制，藉由傳遞關閉的連接，並只執行程式碼會建立所有的內容之後開啟它：  

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

第二項限制只是表示您需要避免處置 DbContext 物件的任何，直到您準備好進行會關閉的連接。  

### <a name="behavior-in-ef6-and-future-versions"></a>EF6 和未來的版本中的行為  

在 EF6 與未來的版本 DbContext 有相同的兩個建構函式，但已不再需要接收時，會關閉連接傳遞至建構函式。 這就是現在可以：  

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

也 contextOwnsConnection 旗標現在會控制連線已關閉和處置 DbContext 處置時。 因此在上述範例中不會關閉連線時的內容是處置 （行 32），就會在舊版的 EF，但是在處置該連接本身時，而不是 （換行 40）。  

當然，仍然有可能對 DbContext，來控制連線 (只是 true，或使用其他建構函式之一組 contextOwnsConnection) 如果您這麼想。  

> [!NOTE]
> 當使用這個新的模型中的交易時，有一些其他考量。 如需詳細資訊，請參閱[使用交易](~/ef6/saving/transactions.md)。  

## <a name="databaseconnectionopen"></a>Database.Connection.Open()  

### <a name="behavior-for-ef5-and-earlier-versions"></a>EF5 和較早版本的行為  

在 EF5 和更早版本中，沒有錯誤使得**ObjectContext.Connection.State**尚未更新來反映基礎存放區連接，則為 true 的狀態。 比方說，如果您執行下列程式碼您可能會傳回狀態**Closed**即使事實上基礎存放區連線**開啟**。  

``` csharp
((IObjectContextAdapter)context).ObjectContext.Connection.State
```  

另外，如果您藉由呼叫 Database.Connection.Open() 開啟資料庫連接它將會開啟直到下次您執行查詢，或呼叫需要資料庫連接的任何項目 (例如，但在 SaveChanges())，基礎存放區將會關閉連接。 內容將然後重新開啟，並重新關閉的每當另一個資料庫作業時所需的連接：  

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

### <a name="behavior-in-ef6-and-future-versions"></a>EF6 和未來的版本中的行為  

EF6 和未來的版本我們已採用的方法，如果呼叫程式碼選擇來開啟連接所呼叫的內容。Database.Connection.Open() 則有這樣的好理由，架構會假設它想要控制開啟和關閉的連線，並且將不會再自動關閉連線。  

> [!NOTE]
> 這可能會導致已開啟，因此請小心使用長時間的連線。  

我們也更新程式碼，以便 ObjectContext.Connection.State 現在會追蹤的基礎連接的狀態正確。  

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
