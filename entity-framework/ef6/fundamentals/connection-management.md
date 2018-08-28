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
# <a name="connection-management"></a><span data-ttu-id="ddac6-102">連接管理</span><span class="sxs-lookup"><span data-stu-id="ddac6-102">Connection management</span></span>
<span data-ttu-id="ddac6-103">此頁面描述 Entity Framework 的行為，將連接傳遞至內容和功能方面**Database.Connection.Open()** API。</span><span class="sxs-lookup"><span data-stu-id="ddac6-103">This page describes the behavior of Entity Framework with regard to passing connections to the context and the functionality of the **Database.Connection.Open()** API.</span></span>  

## <a name="passing-connections-to-the-context"></a><span data-ttu-id="ddac6-104">傳遞至內容的連線</span><span class="sxs-lookup"><span data-stu-id="ddac6-104">Passing Connections to the Context</span></span>  

### <a name="behavior-for-ef5-and-earlier-versions"></a><span data-ttu-id="ddac6-105">EF5 和較早版本的行為</span><span class="sxs-lookup"><span data-stu-id="ddac6-105">Behavior for EF5 and earlier versions</span></span>  

<span data-ttu-id="ddac6-106">有兩個建構函式可接受的連線：</span><span class="sxs-lookup"><span data-stu-id="ddac6-106">There are two constructors which accept connections:</span></span>  

``` csharp
public DbContext(DbConnection existingConnection, bool contextOwnsConnection)
public DbContext(DbConnection existingConnection, DbCompiledModel model, bool contextOwnsConnection)
```  

<span data-ttu-id="ddac6-107">您可使用這些項目，但您必須解決幾項限制：</span><span class="sxs-lookup"><span data-stu-id="ddac6-107">It is possible to use these but you have to work around a couple of limitations:</span></span>  

1. <span data-ttu-id="ddac6-108">如果您傳遞的開啟連接至任一種然後第一次這個架構嘗試使用它就會擲回 InvalidOperationException，指出它無法重新開啟已經開啟的連線。</span><span class="sxs-lookup"><span data-stu-id="ddac6-108">If you pass an open connection to either of these then the first time the framework attempts to use it an InvalidOperationException is thrown saying it cannot re-open an already open connection.</span></span>  
2. <span data-ttu-id="ddac6-109">ContextOwnsConnection 旗標則解譯成表示應該在處置內容時，處置基礎存放區連接。</span><span class="sxs-lookup"><span data-stu-id="ddac6-109">The contextOwnsConnection flag is interpreted to mean whether or not the underlying store connection should be disposed when the context is disposed.</span></span> <span data-ttu-id="ddac6-110">但是，不論這項設定，存放區連接，一律會關閉在處置內容時。</span><span class="sxs-lookup"><span data-stu-id="ddac6-110">But, regardless of that setting, the store connection is always closed when the context is disposed.</span></span> <span data-ttu-id="ddac6-111">因此如果您有多個 DbContext 利用相同連接處置任何內容，是第一次會關閉連線 （同樣地如果您有現有的 ADO.NET 連接與 DbContext，DbContext 會一律關閉連接處置時）.</span><span class="sxs-lookup"><span data-stu-id="ddac6-111">So if you have more than one DbContext with the same connection whichever context is disposed first will close the connection (similarly if you have mixed an existing ADO.NET connection with a DbContext, DbContext will always close the connection when it is disposed).</span></span>  

<span data-ttu-id="ddac6-112">您可解決上述的第一個限制，藉由傳遞關閉的連接，並只執行程式碼會建立所有的內容之後開啟它：</span><span class="sxs-lookup"><span data-stu-id="ddac6-112">It is possible to work around the first limitation above by passing a closed connection and only executing code that would open it once all contexts have been created:</span></span>  

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

<span data-ttu-id="ddac6-113">第二項限制只是表示您需要避免處置 DbContext 物件的任何，直到您準備好進行會關閉的連接。</span><span class="sxs-lookup"><span data-stu-id="ddac6-113">The second limitation just means you need to refrain from disposing any of your DbContext objects until you are ready for the connection to be closed.</span></span>  

### <a name="behavior-in-ef6-and-future-versions"></a><span data-ttu-id="ddac6-114">EF6 和未來的版本中的行為</span><span class="sxs-lookup"><span data-stu-id="ddac6-114">Behavior in EF6 and future versions</span></span>  

<span data-ttu-id="ddac6-115">在 EF6 與未來的版本 DbContext 有相同的兩個建構函式，但已不再需要接收時，會關閉連接傳遞至建構函式。</span><span class="sxs-lookup"><span data-stu-id="ddac6-115">In EF6 and future versions the DbContext has the same two constructors but no longer requires that the connection passed to the constructor be closed when it is received.</span></span> <span data-ttu-id="ddac6-116">這就是現在可以：</span><span class="sxs-lookup"><span data-stu-id="ddac6-116">So this is now possible:</span></span>  

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

<span data-ttu-id="ddac6-117">也 contextOwnsConnection 旗標現在會控制連線已關閉和處置 DbContext 處置時。</span><span class="sxs-lookup"><span data-stu-id="ddac6-117">Also the contextOwnsConnection flag now controls whether or not the connection is both closed and disposed when the DbContext is disposed.</span></span> <span data-ttu-id="ddac6-118">因此在上述範例中不會關閉連線時的內容是處置 （行 32），就會在舊版的 EF，但是在處置該連接本身時，而不是 （換行 40）。</span><span class="sxs-lookup"><span data-stu-id="ddac6-118">So in the above example the connection is not closed when the context is disposed (line 32) as it would have been in previous versions of EF, but rather when the connection itself is disposed (line 40).</span></span>  

<span data-ttu-id="ddac6-119">當然，仍然有可能對 DbContext，來控制連線 (只是 true，或使用其他建構函式之一組 contextOwnsConnection) 如果您這麼想。</span><span class="sxs-lookup"><span data-stu-id="ddac6-119">Of course it is still possible for the DbContext to take control of the connection (just set contextOwnsConnection to true or use one of the other constructors) if you so wish.</span></span>  

> [!NOTE]
> <span data-ttu-id="ddac6-120">當使用這個新的模型中的交易時，有一些其他考量。</span><span class="sxs-lookup"><span data-stu-id="ddac6-120">There are some additional considerations when using transactions with this new model.</span></span> <span data-ttu-id="ddac6-121">如需詳細資訊，請參閱[使用交易](~/ef6/saving/transactions.md)。</span><span class="sxs-lookup"><span data-stu-id="ddac6-121">For details see [Working with Transactions](~/ef6/saving/transactions.md).</span></span>  

## <a name="databaseconnectionopen"></a><span data-ttu-id="ddac6-122">Database.Connection.Open()</span><span class="sxs-lookup"><span data-stu-id="ddac6-122">Database.Connection.Open()</span></span>  

### <a name="behavior-for-ef5-and-earlier-versions"></a><span data-ttu-id="ddac6-123">EF5 和較早版本的行為</span><span class="sxs-lookup"><span data-stu-id="ddac6-123">Behavior for EF5 and earlier versions</span></span>  

<span data-ttu-id="ddac6-124">在 EF5 和更早版本中，沒有錯誤使得**ObjectContext.Connection.State**尚未更新來反映基礎存放區連接，則為 true 的狀態。</span><span class="sxs-lookup"><span data-stu-id="ddac6-124">In EF5 and earlier versions there is a bug such that the **ObjectContext.Connection.State** was not updated to reflect the true state of the underlying store connection.</span></span> <span data-ttu-id="ddac6-125">比方說，如果您執行下列程式碼您可能會傳回狀態**Closed**即使事實上基礎存放區連線**開啟**。</span><span class="sxs-lookup"><span data-stu-id="ddac6-125">For example, if you executed the following code you can be returned the status **Closed** even though in fact the underlying store connection is **Open**.</span></span>  

``` csharp
((IObjectContextAdapter)context).ObjectContext.Connection.State
```  

<span data-ttu-id="ddac6-126">另外，如果您藉由呼叫 Database.Connection.Open() 開啟資料庫連接它將會開啟直到下次您執行查詢，或呼叫需要資料庫連接的任何項目 (例如，但在 SaveChanges())，基礎存放區將會關閉連接。</span><span class="sxs-lookup"><span data-stu-id="ddac6-126">Separately, if you open the database connection by calling Database.Connection.Open() it will be open until the next time you execute a query or call anything which requires a database connection (for example, SaveChanges()) but after that the underlying store connection will be closed.</span></span> <span data-ttu-id="ddac6-127">內容將然後重新開啟，並重新關閉的每當另一個資料庫作業時所需的連接：</span><span class="sxs-lookup"><span data-stu-id="ddac6-127">The context will then re-open and re-close the connection any time another database operation is required:</span></span>  

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

### <a name="behavior-in-ef6-and-future-versions"></a><span data-ttu-id="ddac6-128">EF6 和未來的版本中的行為</span><span class="sxs-lookup"><span data-stu-id="ddac6-128">Behavior in EF6 and future versions</span></span>  

<span data-ttu-id="ddac6-129">EF6 和未來的版本我們已採用的方法，如果呼叫程式碼選擇來開啟連接所呼叫的內容。Database.Connection.Open() 則有這樣的好理由，架構會假設它想要控制開啟和關閉的連線，並且將不會再自動關閉連線。</span><span class="sxs-lookup"><span data-stu-id="ddac6-129">For EF6 and future versions we have taken the approach that if the calling code chooses to open the connection by calling context.Database.Connection.Open() then it has a good reason for doing so and the framework will assume that it wants control over opening and closing of the connection and will no longer close the connection automatically.</span></span>  

> [!NOTE]
> <span data-ttu-id="ddac6-130">這可能會導致已開啟，因此請小心使用長時間的連線。</span><span class="sxs-lookup"><span data-stu-id="ddac6-130">This can potentially lead to connections which are open for a long time so use with care.</span></span>  

<span data-ttu-id="ddac6-131">我們也更新程式碼，以便 ObjectContext.Connection.State 現在會追蹤的基礎連接的狀態正確。</span><span class="sxs-lookup"><span data-stu-id="ddac6-131">We also updated the code so that ObjectContext.Connection.State now keeps track of the state of the underlying connection correctly.</span></span>  

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
