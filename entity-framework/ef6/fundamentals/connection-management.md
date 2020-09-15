---
title: 連接管理-EF6
description: Entity Framework 6 中的連接管理
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/connection-management
ms.openlocfilehash: e2097f5ed0471d309292625990cc5f9a598c5b2a
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070558"
---
# <a name="connection-management"></a><span data-ttu-id="51381-103">連線管理</span><span class="sxs-lookup"><span data-stu-id="51381-103">Connection management</span></span>
<span data-ttu-id="51381-104">此頁面描述將連接傳遞至內容的行為，以及連接到資料庫的功能時的 Entity Framework 行為 \*\*。開啟 ( # B1 \*\* API。</span><span class="sxs-lookup"><span data-stu-id="51381-104">This page describes the behavior of Entity Framework with regard to passing connections to the context and the functionality of the **Database.Connection.Open()** API.</span></span>  

## <a name="passing-connections-to-the-context"></a><span data-ttu-id="51381-105">傳遞內容的連接</span><span class="sxs-lookup"><span data-stu-id="51381-105">Passing Connections to the Context</span></span>  

### <a name="behavior-for-ef5-and-earlier-versions"></a><span data-ttu-id="51381-106">EF5 和較早版本的行為</span><span class="sxs-lookup"><span data-stu-id="51381-106">Behavior for EF5 and earlier versions</span></span>  

<span data-ttu-id="51381-107">有兩個接受連接的函式：</span><span class="sxs-lookup"><span data-stu-id="51381-107">There are two constructors which accept connections:</span></span>  

``` csharp
public DbContext(DbConnection existingConnection, bool contextOwnsConnection)
public DbContext(DbConnection existingConnection, DbCompiledModel model, bool contextOwnsConnection)
```  

<span data-ttu-id="51381-108">您可以使用這些功能，但您必須解決一些限制：</span><span class="sxs-lookup"><span data-stu-id="51381-108">It is possible to use these but you have to work around a couple of limitations:</span></span>  

1. <span data-ttu-id="51381-109">如果您將開啟的連接傳遞到其中一種，則當 framework 第一次嘗試使用它時，會擲回 InvalidOperationException，指出它無法重新開啟已開啟的連接。</span><span class="sxs-lookup"><span data-stu-id="51381-109">If you pass an open connection to either of these then the first time the framework attempts to use it an InvalidOperationException is thrown saying it cannot re-open an already open connection.</span></span>  
2. <span data-ttu-id="51381-110">當處置內容時，會將 coNtextOwnsConnection 旗標解釋為表示是否應該處置基礎存放區連接。</span><span class="sxs-lookup"><span data-stu-id="51381-110">The contextOwnsConnection flag is interpreted to mean whether or not the underlying store connection should be disposed when the context is disposed.</span></span> <span data-ttu-id="51381-111">但是，不論該設定為何，當處置內容時，存放區連接一律會關閉。</span><span class="sxs-lookup"><span data-stu-id="51381-111">But, regardless of that setting, the store connection is always closed when the context is disposed.</span></span> <span data-ttu-id="51381-112">因此，如果您有多個 DbCoNtext 具有相同的連接，則 (會先處置其中的任何內容。如果您已將現有的 ADO.NET 連接與 DbCoNtext 混合，則 DbCoNtext 將會在) 處置時一律關閉連接。</span><span class="sxs-lookup"><span data-stu-id="51381-112">So if you have more than one DbContext with the same connection whichever context is disposed first will close the connection (similarly if you have mixed an existing ADO.NET connection with a DbContext, DbContext will always close the connection when it is disposed).</span></span>  

<span data-ttu-id="51381-113">您可以藉由傳遞關閉的連線，並只執行在建立所有內容之後開啟它的程式碼，來解決上述第一項限制：</span><span class="sxs-lookup"><span data-stu-id="51381-113">It is possible to work around the first limitation above by passing a closed connection and only executing code that would open it once all contexts have been created:</span></span>  

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

<span data-ttu-id="51381-114">第二個限制只是表示您必須先避免處置任何 DbCoNtext 物件，直到您準備好要關閉連接為止。</span><span class="sxs-lookup"><span data-stu-id="51381-114">The second limitation just means you need to refrain from disposing any of your DbContext objects until you are ready for the connection to be closed.</span></span>  

### <a name="behavior-in-ef6-and-future-versions"></a><span data-ttu-id="51381-115">EF6 和未來版本中的行為</span><span class="sxs-lookup"><span data-stu-id="51381-115">Behavior in EF6 and future versions</span></span>  

<span data-ttu-id="51381-116">在 EF6 和未來版本中，DbCoNtext 具有相同的兩個函式，但不再要求傳遞至函式的連接在收到時關閉。</span><span class="sxs-lookup"><span data-stu-id="51381-116">In EF6 and future versions the DbContext has the same two constructors but no longer requires that the connection passed to the constructor be closed when it is received.</span></span> <span data-ttu-id="51381-117">這現在是可行的：</span><span class="sxs-lookup"><span data-stu-id="51381-117">So this is now possible:</span></span>  

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

<span data-ttu-id="51381-118">此外，coNtextOwnsConnection 旗標現在也會控制在處置 DbCoNtext 時是否關閉和處置連接。</span><span class="sxs-lookup"><span data-stu-id="51381-118">Also the contextOwnsConnection flag now controls whether or not the connection is both closed and disposed when the DbContext is disposed.</span></span> <span data-ttu-id="51381-119">因此，在上述範例中，當 (行 32) 處置內容時，不會關閉連線，就像在舊版 EF 中處置一樣，當連接本身 (行 40) 時，也不會關閉連接。</span><span class="sxs-lookup"><span data-stu-id="51381-119">So in the above example the connection is not closed when the context is disposed (line 32) as it would have been in previous versions of EF, but rather when the connection itself is disposed (line 40).</span></span>  

<span data-ttu-id="51381-120">當然，DbCoNtext 還是有可能掌控連接 (只要將 coNtextOwnsConnection 設為 true，或使用其中一個其他的函式) 如果您想要的話。</span><span class="sxs-lookup"><span data-stu-id="51381-120">Of course it is still possible for the DbContext to take control of the connection (just set contextOwnsConnection to true or use one of the other constructors) if you so wish.</span></span>  

> [!NOTE]
> <span data-ttu-id="51381-121">使用此新模型時，有一些其他考慮。</span><span class="sxs-lookup"><span data-stu-id="51381-121">There are some additional considerations when using transactions with this new model.</span></span> <span data-ttu-id="51381-122">如需詳細資訊，請參閱 [使用交易](xref:ef6/saving/transactions)。</span><span class="sxs-lookup"><span data-stu-id="51381-122">For details see [Working with Transactions](xref:ef6/saving/transactions).</span></span>  

## <a name="databaseconnectionopen"></a><span data-ttu-id="51381-123">連接。開啟 ( # A1</span><span class="sxs-lookup"><span data-stu-id="51381-123">Database.Connection.Open()</span></span>  

### <a name="behavior-for-ef5-and-earlier-versions"></a><span data-ttu-id="51381-124">EF5 和較早版本的行為</span><span class="sxs-lookup"><span data-stu-id="51381-124">Behavior for EF5 and earlier versions</span></span>  

<span data-ttu-id="51381-125">在 EF5 和較早的版本中，有一個錯誤（bug），因此不會更新 **ObjectCoNtext** 以反映基礎存放區連接的真正狀態。</span><span class="sxs-lookup"><span data-stu-id="51381-125">In EF5 and earlier versions there is a bug such that the **ObjectContext.Connection.State** was not updated to reflect the true state of the underlying store connection.</span></span> <span data-ttu-id="51381-126">例如，如果您執行了下列程式碼，即使基礎存放區連接已**開啟**，也可以將狀態視為**已關閉**。</span><span class="sxs-lookup"><span data-stu-id="51381-126">For example, if you executed the following code you can be returned the status **Closed** even though in fact the underlying store connection is **Open**.</span></span>  

``` csharp
((IObjectContextAdapter)context).ObjectContext.Connection.State
```  

<span data-ttu-id="51381-127">另外，如果您藉由呼叫連接來開啟資料庫連接，請開啟 ( # A1，直到您下一次執行查詢或呼叫需要資料庫 (連接的任何作業時，才會開啟它（例如 SaveChanges ( # A4 # A5，但之後將會關閉基礎存放區連接）。</span><span class="sxs-lookup"><span data-stu-id="51381-127">Separately, if you open the database connection by calling Database.Connection.Open() it will be open until the next time you execute a query or call anything which requires a database connection (for example, SaveChanges()) but after that the underlying store connection will be closed.</span></span> <span data-ttu-id="51381-128">當需要另一個資料庫作業時，內容就會重新開啟並重新關閉連接：</span><span class="sxs-lookup"><span data-stu-id="51381-128">The context will then re-open and re-close the connection any time another database operation is required:</span></span>  

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

### <a name="behavior-in-ef6-and-future-versions"></a><span data-ttu-id="51381-129">EF6 和未來版本中的行為</span><span class="sxs-lookup"><span data-stu-id="51381-129">Behavior in EF6 and future versions</span></span>  

<span data-ttu-id="51381-130">針對 EF6 和未來的版本，我們採用的方法是，如果呼叫程式碼選擇透過呼叫內容來開啟連接。連接。開啟 ( # A1 之後，它會有很好的理由，而且架構會假設它想要控制連接的開啟和關閉，且不會再自動關閉連接。</span><span class="sxs-lookup"><span data-stu-id="51381-130">For EF6 and future versions we have taken the approach that if the calling code chooses to open the connection by calling context.Database.Connection.Open() then it has a good reason for doing so and the framework will assume that it wants control over opening and closing of the connection and will no longer close the connection automatically.</span></span>  

> [!NOTE]
> <span data-ttu-id="51381-131">這可能會導致長時間開啟的連接，因此請小心使用。</span><span class="sxs-lookup"><span data-stu-id="51381-131">This can potentially lead to connections which are open for a long time so use with care.</span></span>  

<span data-ttu-id="51381-132">我們也已更新程式碼，讓 ObjectCoNtext 連接。狀態現在會正確地追蹤基礎連接的狀態。</span><span class="sxs-lookup"><span data-stu-id="51381-132">We also updated the code so that ObjectContext.Connection.State now keeps track of the state of the underlying connection correctly.</span></span>  

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
