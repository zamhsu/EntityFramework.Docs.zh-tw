---
title: 連接管理-EF6
description: Entity Framework 6 中的連接管理
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/connection-management
ms.openlocfilehash: e8c5c3615b4019be4c1915d3acaa91d60a55c85d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063292"
---
# <a name="connection-management"></a>連線管理
此頁面描述將連接傳遞至內容的行為，以及連接到資料庫的功能時的 Entity Framework 行為 **。開啟 ( # B1 ** API。  

## <a name="passing-connections-to-the-context"></a>傳遞內容的連接  

### <a name="behavior-for-ef5-and-earlier-versions"></a>EF5 和較早版本的行為  

有兩個接受連接的函式：  

``` csharp
public DbContext(DbConnection existingConnection, bool contextOwnsConnection)
public DbContext(DbConnection existingConnection, DbCompiledModel model, bool contextOwnsConnection)
```  

您可以使用這些功能，但您必須解決一些限制：  

1. 如果您將開啟的連接傳遞到其中一種，則當 framework 第一次嘗試使用它時，會擲回 InvalidOperationException，指出它無法重新開啟已開啟的連接。  
2. 當處置內容時，會將 coNtextOwnsConnection 旗標解釋為表示是否應該處置基礎存放區連接。 但是，不論該設定為何，當處置內容時，存放區連接一律會關閉。 因此，如果您有多個 DbCoNtext 具有相同的連接，則 (會先處置其中的任何內容。如果您已將現有的 ADO.NET 連接與 DbCoNtext 混合，則 DbCoNtext 將會在) 處置時一律關閉連接。  

您可以藉由傳遞關閉的連線，並只執行在建立所有內容之後開啟它的程式碼，來解決上述第一項限制：  

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

第二個限制只是表示您必須先避免處置任何 DbCoNtext 物件，直到您準備好要關閉連接為止。  

### <a name="behavior-in-ef6-and-future-versions"></a>EF6 和未來版本中的行為  

在 EF6 和未來版本中，DbCoNtext 具有相同的兩個函式，但不再要求傳遞至函式的連接在收到時關閉。 這現在是可行的：  

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

此外，coNtextOwnsConnection 旗標現在也會控制在處置 DbCoNtext 時是否關閉和處置連接。 因此，在上述範例中，當 (行 32) 處置內容時，不會關閉連線，就像在舊版 EF 中處置一樣，當連接本身 (行 40) 時，也不會關閉連接。  

當然，DbCoNtext 還是有可能掌控連接 (只要將 coNtextOwnsConnection 設為 true，或使用其中一個其他的函式) 如果您想要的話。  

> [!NOTE]
> 使用此新模型時，有一些其他考慮。 如需詳細資訊，請參閱 [使用交易](xref:ef6/saving/transactions)。  

## <a name="databaseconnectionopen"></a>連接。開啟 ( # A1  

### <a name="behavior-for-ef5-and-earlier-versions"></a>EF5 和較早版本的行為  

在 EF5 和較早的版本中，有一個錯誤（bug），因此不會更新 **ObjectCoNtext** 以反映基礎存放區連接的真正狀態。 例如，如果您執行了下列程式碼，即使基礎存放區連接已**開啟**，也可以將狀態視為**已關閉**。  

``` csharp
((IObjectContextAdapter)context).ObjectContext.Connection.State
```  

另外，如果您藉由呼叫連接來開啟資料庫連接，請開啟 ( # A1，直到您下一次執行查詢或呼叫需要資料庫 (連接的任何作業時，才會開啟它（例如 SaveChanges ( # A4 # A5，但之後將會關閉基礎存放區連接）。 當需要另一個資料庫作業時，內容就會重新開啟並重新關閉連接：  

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

針對 EF6 和未來的版本，我們採用的方法是，如果呼叫程式碼選擇透過呼叫內容來開啟連接。連接。開啟 ( # A1 之後，它會有很好的理由，而且架構會假設它想要控制連接的開啟和關閉，且不會再自動關閉連接。  

> [!NOTE]
> 這可能會導致長時間開啟的連接，因此請小心使用。  

我們也已更新程式碼，讓 ObjectCoNtext 連接。狀態現在會正確地追蹤基礎連接的狀態。  

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
