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
# <a name="connection-management"></a><span data-ttu-id="fe31e-102">連線管理</span><span class="sxs-lookup"><span data-stu-id="fe31e-102">Connection management</span></span>
<span data-ttu-id="fe31e-103">此頁面描述將連接傳遞至內容和**Database. Open （）** API 的功能時，Entity Framework 的行為。</span><span class="sxs-lookup"><span data-stu-id="fe31e-103">This page describes the behavior of Entity Framework with regard to passing connections to the context and the functionality of the **Database.Connection.Open()** API.</span></span>  

## <a name="passing-connections-to-the-context"></a><span data-ttu-id="fe31e-104">傳遞連接至內容</span><span class="sxs-lookup"><span data-stu-id="fe31e-104">Passing Connections to the Context</span></span>  

### <a name="behavior-for-ef5-and-earlier-versions"></a><span data-ttu-id="fe31e-105">EF5 和較早版本的行為</span><span class="sxs-lookup"><span data-stu-id="fe31e-105">Behavior for EF5 and earlier versions</span></span>  

<span data-ttu-id="fe31e-106">有兩個接受連接的函式：</span><span class="sxs-lookup"><span data-stu-id="fe31e-106">There are two constructors which accept connections:</span></span>  

``` csharp
public DbContext(DbConnection existingConnection, bool contextOwnsConnection)
public DbContext(DbConnection existingConnection, DbCompiledModel model, bool contextOwnsConnection)
```  

<span data-ttu-id="fe31e-107">您可以使用這些方法，但您必須解決幾個限制：</span><span class="sxs-lookup"><span data-stu-id="fe31e-107">It is possible to use these but you have to work around a couple of limitations:</span></span>  

1. <span data-ttu-id="fe31e-108">如果您將開啟的連接傳遞給其中一個，則當架構第一次嘗試使用它時，就會擲回 InvalidOperationException，指出它無法重新開啟已開啟的連接。</span><span class="sxs-lookup"><span data-stu-id="fe31e-108">If you pass an open connection to either of these then the first time the framework attempts to use it an InvalidOperationException is thrown saying it cannot re-open an already open connection.</span></span>  
2. <span data-ttu-id="fe31e-109">CoNtextOwnsConnection 旗標會解讀為表示在處置內容時是否應處置基礎存放區連接。</span><span class="sxs-lookup"><span data-stu-id="fe31e-109">The contextOwnsConnection flag is interpreted to mean whether or not the underlying store connection should be disposed when the context is disposed.</span></span> <span data-ttu-id="fe31e-110">但是，不論該設定為何，當處置內容時，存放區連接一律會關閉。</span><span class="sxs-lookup"><span data-stu-id="fe31e-110">But, regardless of that setting, the store connection is always closed when the context is disposed.</span></span> <span data-ttu-id="fe31e-111">因此，如果您有一個以上的 DbCoNtext 具有相同的連線，則會先將其處置，否則會關閉連接（同樣地，如果您已使用 DbCoNtext 混合現有的 ADO.NET 連線，DbCoNtext 將一律會在處置時關閉連接）.</span><span class="sxs-lookup"><span data-stu-id="fe31e-111">So if you have more than one DbContext with the same connection whichever context is disposed first will close the connection (similarly if you have mixed an existing ADO.NET connection with a DbContext, DbContext will always close the connection when it is disposed).</span></span>  

<span data-ttu-id="fe31e-112">藉由傳遞已關閉的連接，而且只執行會在建立所有內容之後開啟它的程式碼，就可以解決上述第一項限制：</span><span class="sxs-lookup"><span data-stu-id="fe31e-112">It is possible to work around the first limitation above by passing a closed connection and only executing code that would open it once all contexts have been created:</span></span>  

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

<span data-ttu-id="fe31e-113">第二個限制只是表示您需要避免處置任何 DbCoNtext 物件，直到您準備好讓連接關閉為止。</span><span class="sxs-lookup"><span data-stu-id="fe31e-113">The second limitation just means you need to refrain from disposing any of your DbContext objects until you are ready for the connection to be closed.</span></span>  

### <a name="behavior-in-ef6-and-future-versions"></a><span data-ttu-id="fe31e-114">EF6 和未來版本中的行為</span><span class="sxs-lookup"><span data-stu-id="fe31e-114">Behavior in EF6 and future versions</span></span>  

<span data-ttu-id="fe31e-115">在 EF6 和未來版本中，DbCoNtext 具有相同的兩個函式，但不再要求傳遞至該函式的連接在收到時關閉。</span><span class="sxs-lookup"><span data-stu-id="fe31e-115">In EF6 and future versions the DbContext has the same two constructors but no longer requires that the connection passed to the constructor be closed when it is received.</span></span> <span data-ttu-id="fe31e-116">這現在是可行的：</span><span class="sxs-lookup"><span data-stu-id="fe31e-116">So this is now possible:</span></span>  

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

<span data-ttu-id="fe31e-117">此外，coNtextOwnsConnection 旗標現在會控制是否要在處置 DbCoNtext 時關閉和處置連接。</span><span class="sxs-lookup"><span data-stu-id="fe31e-117">Also the contextOwnsConnection flag now controls whether or not the connection is both closed and disposed when the DbContext is disposed.</span></span> <span data-ttu-id="fe31e-118">因此，在上述範例中，當內容被處置（行32）時，不會關閉連接，因為它在舊版 EF 中已經存在，而是在處理連接本身時（行40）。</span><span class="sxs-lookup"><span data-stu-id="fe31e-118">So in the above example the connection is not closed when the context is disposed (line 32) as it would have been in previous versions of EF, but rather when the connection itself is disposed (line 40).</span></span>  

<span data-ttu-id="fe31e-119">當然，DbCoNtext 還是可以控制連接（只要將 coNtextOwnsConnection 設為 true，或使用其中一個其他的函式）。</span><span class="sxs-lookup"><span data-stu-id="fe31e-119">Of course it is still possible for the DbContext to take control of the connection (just set contextOwnsConnection to true or use one of the other constructors) if you so wish.</span></span>  

> [!NOTE]
> <span data-ttu-id="fe31e-120">在此新模型中使用交易時，還有一些額外的考慮。</span><span class="sxs-lookup"><span data-stu-id="fe31e-120">There are some additional considerations when using transactions with this new model.</span></span> <span data-ttu-id="fe31e-121">如需詳細資訊，請參閱[使用交易](~/ef6/saving/transactions.md)。</span><span class="sxs-lookup"><span data-stu-id="fe31e-121">For details see [Working with Transactions](~/ef6/saving/transactions.md).</span></span>  

## <a name="databaseconnectionopen"></a><span data-ttu-id="fe31e-122">連接。開啟（）</span><span class="sxs-lookup"><span data-stu-id="fe31e-122">Database.Connection.Open()</span></span>  

### <a name="behavior-for-ef5-and-earlier-versions"></a><span data-ttu-id="fe31e-123">EF5 和較早版本的行為</span><span class="sxs-lookup"><span data-stu-id="fe31e-123">Behavior for EF5 and earlier versions</span></span>  

<span data-ttu-id="fe31e-124">在 EF5 和較舊版本中，有一個 bug，讓**ObjectCoNtext**不會更新，以反映基礎存放區連接的真正狀態。</span><span class="sxs-lookup"><span data-stu-id="fe31e-124">In EF5 and earlier versions there is a bug such that the **ObjectContext.Connection.State** was not updated to reflect the true state of the underlying store connection.</span></span> <span data-ttu-id="fe31e-125">例如，如果您執行下列程式碼，您可以傳回狀態 [**已關閉**]，即使基礎存放區連接已**開啟**也是一樣。</span><span class="sxs-lookup"><span data-stu-id="fe31e-125">For example, if you executed the following code you can be returned the status **Closed** even though in fact the underlying store connection is **Open**.</span></span>  

``` csharp
((IObjectContextAdapter)context).ObjectContext.Connection.State
```  

<span data-ttu-id="fe31e-126">另外，如果您藉由呼叫 [資料庫] 開啟資料庫連接，請開啟（），直到下一次執行查詢或呼叫需要資料庫連接的任何專案時（例如 SaveChanges （）），但在基礎存放區之後，才會開啟連接將會關閉。</span><span class="sxs-lookup"><span data-stu-id="fe31e-126">Separately, if you open the database connection by calling Database.Connection.Open() it will be open until the next time you execute a query or call anything which requires a database connection (for example, SaveChanges()) but after that the underlying store connection will be closed.</span></span> <span data-ttu-id="fe31e-127">然後，每當需要另一個資料庫作業時，內容就會重新開啟並重新關閉連接：</span><span class="sxs-lookup"><span data-stu-id="fe31e-127">The context will then re-open and re-close the connection any time another database operation is required:</span></span>  

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

### <a name="behavior-in-ef6-and-future-versions"></a><span data-ttu-id="fe31e-128">EF6 和未來版本中的行為</span><span class="sxs-lookup"><span data-stu-id="fe31e-128">Behavior in EF6 and future versions</span></span>  

<span data-ttu-id="fe31e-129">在 EF6 和未來版本中，如果呼叫程式碼選擇藉由呼叫內容來開啟連接，我們就採取了方法。連接. Open （），這樣做有一個很好的理由，而且架構會假設它想要控制連接的開啟和關閉，而且不會再自動關閉連接。</span><span class="sxs-lookup"><span data-stu-id="fe31e-129">For EF6 and future versions we have taken the approach that if the calling code chooses to open the connection by calling context.Database.Connection.Open() then it has a good reason for doing so and the framework will assume that it wants control over opening and closing of the connection and will no longer close the connection automatically.</span></span>  

> [!NOTE]
> <span data-ttu-id="fe31e-130">這可能會導致長時間開啟的連線，因此請小心使用。</span><span class="sxs-lookup"><span data-stu-id="fe31e-130">This can potentially lead to connections which are open for a long time so use with care.</span></span>  

<span data-ttu-id="fe31e-131">我們也更新了程式碼，讓 ObjectCoNtext 的狀態能夠正確追蹤基礎連接的狀態。</span><span class="sxs-lookup"><span data-stu-id="fe31e-131">We also updated the code so that ObjectContext.Connection.State now keeps track of the state of the underlying connection correctly.</span></span>  

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
