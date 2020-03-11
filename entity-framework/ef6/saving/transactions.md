---
title: 使用交易-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 0d0f1824-d781-4cb3-8fda-b7eaefced1cd
ms.openlocfilehash: 7030dc675993339f72c935f6b430cead85fecb7f
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78419682"
---
# <a name="working-with-transactions"></a><span data-ttu-id="a9ff5-102">使用交易</span><span class="sxs-lookup"><span data-stu-id="a9ff5-102">Working with Transactions</span></span>
> [!NOTE]
> <span data-ttu-id="a9ff5-103">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="a9ff5-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="a9ff5-105">本檔將說明如何在 EF6 中使用交易，包括自 EF5 以來新增的增強功能，讓您輕鬆處理交易。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-105">This document will describe using transactions in EF6 including the enhancements we have added since EF5 to make working with transactions easy.</span></span>  

## <a name="what-ef-does-by-default"></a><span data-ttu-id="a9ff5-106">EF 的預設功能</span><span class="sxs-lookup"><span data-stu-id="a9ff5-106">What EF does by default</span></span>  

<span data-ttu-id="a9ff5-107">在所有版本的 Entity Framework 中，每當您在資料庫上執行**SaveChanges （）** 以進行插入、更新或刪除時，架構就會將該作業包裝在交易中。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-107">In all versions of Entity Framework, whenever you execute **SaveChanges()** to insert, update or delete on the database the framework will wrap that operation in a transaction.</span></span> <span data-ttu-id="a9ff5-108">此交易的持續時間只夠長，足以執行作業，然後完成。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-108">This transaction lasts only long enough to execute the operation and then completes.</span></span> <span data-ttu-id="a9ff5-109">當您執行其他這類作業時，就會啟動新的交易。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-109">When you execute another such operation a new transaction is started.</span></span>  

<span data-ttu-id="a9ff5-110">從 EF6 資料庫開始， **ExecuteSqlCommand （）** 預設會將命令包裝在交易中（如果尚未存在的話）。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-110">Starting with EF6 **Database.ExecuteSqlCommand()** by default will wrap the command in a transaction if one was not already present.</span></span> <span data-ttu-id="a9ff5-111">有這個方法的多載可讓您視需要覆寫此行為。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-111">There are overloads of this method that allow you to override this behavior if you wish.</span></span> <span data-ttu-id="a9ff5-112">此外，在 EF6 中，透過**ExecuteFunction （）** 之類的 api 包含在模型中的預存程式，也會執行相同的動作（但不會覆寫預設行為）。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-112">Also in EF6 execution of stored procedures included in the model through APIs such as **ObjectContext.ExecuteFunction()** does the same (except that the default behavior cannot at the moment be overridden).</span></span>  

<span data-ttu-id="a9ff5-113">在任一情況下，交易的隔離等級就是資料庫提供者視為其預設設定的任何隔離等級。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-113">In either case, the isolation level of the transaction is whatever isolation level the database provider considers its default setting.</span></span> <span data-ttu-id="a9ff5-114">例如，根據預設，SQL Server 這是讀取認可。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-114">By default, for instance, on SQL Server this is READ COMMITTED.</span></span>  

<span data-ttu-id="a9ff5-115">Entity Framework 不會將查詢包裝在交易中。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-115">Entity Framework does not wrap queries in a transaction.</span></span>  

<span data-ttu-id="a9ff5-116">這項預設功能適用于許多使用者，如果有，則不需要在 EF6 中進行任何不同的動作;只要像往常一樣撰寫程式碼即可。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-116">This default functionality is suitable for a lot of users and if so there is no need to do anything different in EF6; just write the code as you always did.</span></span>  

<span data-ttu-id="a9ff5-117">不過，某些使用者需要對其交易有更大的控制權，這會在下列各節中討論。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-117">However some users require greater control over their transactions – this is covered in the following sections.</span></span>  

## <a name="how-the-apis-work"></a><span data-ttu-id="a9ff5-118">Api 的工作方式</span><span class="sxs-lookup"><span data-stu-id="a9ff5-118">How the APIs work</span></span>  

<span data-ttu-id="a9ff5-119">在 Entity Framework EF6 之前，請先開啟資料庫連接本身（如果傳遞已經開啟的連接，就會擲回例外狀況）。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-119">Prior to EF6 Entity Framework insisted on opening the database connection itself (it threw an exception if it was passed a connection that was already open).</span></span> <span data-ttu-id="a9ff5-120">因為交易只能在開啟的連接上啟動，這表示使用者可以將數個作業包裝成一個交易的唯一方式，就是使用[TransactionScope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx)或使用**ObjectCoNtext 連接**屬性，然後直接在傳回的**EntityConnection**物件上呼叫**open （）** 和**BeginTransaction （）** 。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-120">Since a transaction can only be started on an open connection, this meant that the only way a user could wrap several operations into one transaction was either to use a [TransactionScope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx) or use the **ObjectContext.Connection** property and start calling **Open()** and **BeginTransaction()** directly on the returned **EntityConnection** object.</span></span> <span data-ttu-id="a9ff5-121">此外，如果您已在基礎資料庫連接上啟動交易，則與資料庫聯繫的 API 呼叫將會失敗。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-121">In addition, API calls which contacted the database would fail if you had started a transaction on the underlying database connection on your own.</span></span>  

> [!NOTE]
> <span data-ttu-id="a9ff5-122">Entity Framework 6 中已移除只接受已關閉連接的限制。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-122">The limitation of only accepting closed connections was removed in Entity Framework 6.</span></span> <span data-ttu-id="a9ff5-123">如需詳細資訊，請參閱[連接管理](~/ef6/fundamentals/connection-management.md)。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-123">For details, see [Connection Management](~/ef6/fundamentals/connection-management.md).</span></span>  

<span data-ttu-id="a9ff5-124">從 EF6 開始，架構現在提供：</span><span class="sxs-lookup"><span data-stu-id="a9ff5-124">Starting with EF6 the framework now provides:</span></span>  

1. <span data-ttu-id="a9ff5-125">**BeginTransaction （）** ：使用者可以更輕鬆地在現有的 DbCoNtext 內啟動和完成交易本身–允許在相同的交易內結合數個作業，因此全部認可或全部復原為一個。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-125">**Database.BeginTransaction()** : An easier method for a user to start and complete transactions themselves within an existing DbContext – allowing several operations to be combined within the same transaction and hence either all committed or all rolled back as one.</span></span> <span data-ttu-id="a9ff5-126">它也可以讓使用者更輕鬆地指定交易的隔離等級。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-126">It also allows the user to more easily specify the isolation level for the transaction.</span></span>  
2. <span data-ttu-id="a9ff5-127">**UseTransaction （）** ：可讓 DbCoNtext 使用在 Entity Framework 外部啟動的交易。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-127">**Database.UseTransaction()** : which allows the DbContext to use a transaction which was started outside of the Entity Framework.</span></span>  

### <a name="combining-several-operations-into-one-transaction-within-the-same-context"></a><span data-ttu-id="a9ff5-128">將數個作業結合成相同內容中的一個交易</span><span class="sxs-lookup"><span data-stu-id="a9ff5-128">Combining several operations into one transaction within the same context</span></span>  

<span data-ttu-id="a9ff5-129">**BeginTransaction （）** 有兩個覆寫：一個會接受明確的[IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx) ，另一個則會採用沒有引數，並使用基礎資料庫提供者的預設 IsolationLevel。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-129">**Database.BeginTransaction()** has two overrides – one which takes an explicit [IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx) and one which takes no arguments and uses the default IsolationLevel from the underlying database provider.</span></span> <span data-ttu-id="a9ff5-130">這兩個覆寫都會傳回**DbCoNtextTransaction**物件，它會提供**commit （）** 和**rollback （）** 方法，以在基礎存放區交易上執行 commit 和 rollback。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-130">Both overrides return a **DbContextTransaction** object which provides **Commit()** and **Rollback()** methods which perform commit and rollback on the underlying store transaction.</span></span>  

<span data-ttu-id="a9ff5-131">**DbCoNtextTransaction**的目的是要在認可或回復之後加以處置。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-131">The **DbContextTransaction** is meant to be disposed once it has been committed or rolled back.</span></span> <span data-ttu-id="a9ff5-132">完成這項工作的一個簡單方法是**使用（...） {...}**</span><span class="sxs-lookup"><span data-stu-id="a9ff5-132">One easy way to accomplish this is the **using(…) {…}**</span></span> <span data-ttu-id="a9ff5-133">當 using 區塊完成時，會自動呼叫**Dispose （）** 的語法：</span><span class="sxs-lookup"><span data-stu-id="a9ff5-133">syntax which will automatically call **Dispose()** when the using block completes:</span></span>  

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
> <span data-ttu-id="a9ff5-134">開始交易時，必須開啟基礎存放區連接。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-134">Beginning a transaction requires that the underlying store connection is open.</span></span> <span data-ttu-id="a9ff5-135">因此，如果尚未開啟連接，則呼叫 BeginTransaction （）將會開啟它。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-135">So calling Database.BeginTransaction() will open the connection  if it is not already opened.</span></span> <span data-ttu-id="a9ff5-136">如果 DbCoNtextTransaction 開啟了連接，則會在呼叫 Dispose （）時將它關閉。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-136">If DbContextTransaction opened the connection then it will close it when Dispose() is called.</span></span>  

### <a name="passing-an-existing-transaction-to-the-context"></a><span data-ttu-id="a9ff5-137">將現有的交易傳遞至內容</span><span class="sxs-lookup"><span data-stu-id="a9ff5-137">Passing an existing transaction to the context</span></span>  

<span data-ttu-id="a9ff5-138">有時候，您會想要在範圍中更廣泛的交易，以及包含在相同資料庫上但完全不在 EF 外部的作業。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-138">Sometimes you would like a transaction which is even broader in scope and which includes operations on the same database but outside of EF completely.</span></span> <span data-ttu-id="a9ff5-139">若要完成此動作，您必須開啟連接並自行啟動交易，然後告訴 EF a）使用已開啟的資料庫連接，以及 b）來使用該連接上的現有交易。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-139">To accomplish this you must open the connection and start the transaction yourself and then tell EF a) to use the already-opened database connection, and b) to use the existing transaction on that connection.</span></span>  

<span data-ttu-id="a9ff5-140">若要這麼做，您必須在您的內容類別上定義和使用一個函式，而該類別繼承自其中一個 DbCoNtext 的函數（接受 i）現有的連接參數和 ii） coNtextOwnsConnection 的布林值。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-140">To do this you must define and use a constructor on your context class which inherits from one of the DbContext constructors which take i) an existing connection parameter and ii) the contextOwnsConnection boolean.</span></span>  

> [!NOTE]
> <span data-ttu-id="a9ff5-141">在此案例中呼叫 coNtextOwnsConnection 旗標時，必須將它設定為 false。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-141">The contextOwnsConnection flag must be set to false when called in this scenario.</span></span> <span data-ttu-id="a9ff5-142">這很重要，因為它會通知 Entity Framework 它在完成時不應關閉連接（例如，請參閱下面的第4行）：</span><span class="sxs-lookup"><span data-stu-id="a9ff5-142">This is important as it informs Entity Framework that it should not close the connection when it is done with it (for example, see line 4 below):</span></span>  

``` csharp
using (var conn = new SqlConnection("..."))
{
    conn.Open();
    using (var context = new BloggingContext(conn, contextOwnsConnection: false))
    {
    }
}
```  

<span data-ttu-id="a9ff5-143">此外，您必須自行啟動交易（包括 IsolationLevel，如果您想要避免預設值），並讓 Entity Framework 知道連線上已啟動現有的交易（請參閱下面的第33行）。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-143">Furthermore, you must start the transaction yourself (including the IsolationLevel if you want to avoid the default setting) and let Entity Framework know that there is an existing transaction already started on the connection (see line 33 below).</span></span>  

<span data-ttu-id="a9ff5-144">然後您就可以自由地直接在 SqlConnection 本身或在 DbCoNtext 上執行資料庫作業。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-144">Then you are free to execute database operations either directly on the SqlConnection itself, or on the DbContext.</span></span> <span data-ttu-id="a9ff5-145">所有這類作業都是在單一交易中執行。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-145">All such operations are executed within one transaction.</span></span> <span data-ttu-id="a9ff5-146">您必須負責認可或回復交易，以及在其上呼叫 Dispose （），以及用來關閉和處置資料庫連接。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-146">You take responsibility for committing or rolling back the transaction and for calling Dispose() on it, as well as for closing and disposing the database connection.</span></span> <span data-ttu-id="a9ff5-147">例如：</span><span class="sxs-lookup"><span data-stu-id="a9ff5-147">For example:</span></span>  

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

### <a name="clearing-up-the-transaction"></a><span data-ttu-id="a9ff5-148">清除交易</span><span class="sxs-lookup"><span data-stu-id="a9ff5-148">Clearing up the transaction</span></span>

<span data-ttu-id="a9ff5-149">您可以將 null 傳遞給 UseTransaction （），以清除 Entity Framework 對目前交易的瞭解。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-149">You can pass null to Database.UseTransaction() to clear Entity Framework’s knowledge of the current transaction.</span></span> <span data-ttu-id="a9ff5-150">當您這麼做時，Entity Framework 不會認可或回復現有的交易，因此請小心使用，而且只有在您確定這是您想要執行的動作時。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-150">Entity Framework will neither commit nor rollback the existing transaction when you do this, so use with care and only if you’re sure this is what you want to do.</span></span>  

### <a name="errors-in-usetransaction"></a><span data-ttu-id="a9ff5-151">UseTransaction 中的錯誤</span><span class="sxs-lookup"><span data-stu-id="a9ff5-151">Errors in UseTransaction</span></span>

<span data-ttu-id="a9ff5-152">如果您在下列情況中傳遞交易，您將會看到 UseTransaction （）的例外狀況：</span><span class="sxs-lookup"><span data-stu-id="a9ff5-152">You will see an exception from Database.UseTransaction() if you pass a transaction when:</span></span>  
- <span data-ttu-id="a9ff5-153">Entity Framework 已經有現有的交易</span><span class="sxs-lookup"><span data-stu-id="a9ff5-153">Entity Framework already has an existing transaction</span></span>  
- <span data-ttu-id="a9ff5-154">Entity Framework 已在 TransactionScope 內運作</span><span class="sxs-lookup"><span data-stu-id="a9ff5-154">Entity Framework is already operating within a TransactionScope</span></span>  
- <span data-ttu-id="a9ff5-155">交易中傳遞的連線物件為 null。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-155">The connection object in the transaction passed is null.</span></span> <span data-ttu-id="a9ff5-156">也就是說，交易不會與連接相關聯–通常這是交易已完成的正負號</span><span class="sxs-lookup"><span data-stu-id="a9ff5-156">That is, the transaction is not associated with a connection – usually this is a sign that that transaction has already completed</span></span>  
- <span data-ttu-id="a9ff5-157">交易中傳遞的連線物件與 Entity Framework 的連接不符。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-157">The connection object in the transaction passed does not match the Entity Framework’s connection.</span></span>  

## <a name="using-transactions-with-other-features"></a><span data-ttu-id="a9ff5-158">搭配其他功能使用交易</span><span class="sxs-lookup"><span data-stu-id="a9ff5-158">Using transactions with other features</span></span>  

<span data-ttu-id="a9ff5-159">本節將詳細說明上述交易如何與進行互動：</span><span class="sxs-lookup"><span data-stu-id="a9ff5-159">This section details how the above transactions interact with:</span></span>  

- <span data-ttu-id="a9ff5-160">恢復連線</span><span class="sxs-lookup"><span data-stu-id="a9ff5-160">Connection resiliency</span></span>  
- <span data-ttu-id="a9ff5-161">非同步方法</span><span class="sxs-lookup"><span data-stu-id="a9ff5-161">Asynchronous methods</span></span>  
- <span data-ttu-id="a9ff5-162">TransactionScope 交易</span><span class="sxs-lookup"><span data-stu-id="a9ff5-162">TransactionScope transactions</span></span>  

### <a name="connection-resiliency"></a><span data-ttu-id="a9ff5-163">連接恢復功能</span><span class="sxs-lookup"><span data-stu-id="a9ff5-163">Connection Resiliency</span></span>  

<span data-ttu-id="a9ff5-164">新的連接復原功能無法與使用者起始的交易搭配使用。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-164">The new Connection Resiliency feature does not work with user-initiated transactions.</span></span> <span data-ttu-id="a9ff5-165">如需詳細資訊，請參閱[重試執行策略](~/ef6/fundamentals/connection-resiliency/retry-logic.md#user-initiated-transactions-are-not-supported)。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-165">For details, see [Retrying Execution Strategies](~/ef6/fundamentals/connection-resiliency/retry-logic.md#user-initiated-transactions-are-not-supported).</span></span>  

### <a name="asynchronous-programming"></a><span data-ttu-id="a9ff5-166">非同步程式設計</span><span class="sxs-lookup"><span data-stu-id="a9ff5-166">Asynchronous Programming</span></span>  

<span data-ttu-id="a9ff5-167">前面幾節所述的方法，不需要進一步的選項或設定，就能使用[非同步查詢和儲存方法](~/ef6/fundamentals/async.md
)。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-167">The approach outlined in the previous sections needs no further options or settings to work with the [asynchronous query and save methods](~/ef6/fundamentals/async.md
).</span></span> <span data-ttu-id="a9ff5-168">但請注意，根據您在非同步方法中所執行的動作而定，這可能會導致長時間執行的交易，進而導致鎖死或封鎖，而造成整體應用程式的效能不佳。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-168">But be aware that, depending on what you do within the asynchronous methods, this may result in long-running transactions – which can in turn cause deadlocks or blocking which is bad for the performance of the overall application.</span></span>  

### <a name="transactionscope-transactions"></a><span data-ttu-id="a9ff5-169">TransactionScope 交易</span><span class="sxs-lookup"><span data-stu-id="a9ff5-169">TransactionScope Transactions</span></span>  

<span data-ttu-id="a9ff5-170">在 EF6 之前，提供較大範圍交易的建議方式是使用 TransactionScope 物件：</span><span class="sxs-lookup"><span data-stu-id="a9ff5-170">Prior to EF6 the recommended way of providing larger scope transactions was to use a TransactionScope object:</span></span>  

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

<span data-ttu-id="a9ff5-171">SqlConnection 和 Entity Framework 都會使用環境 TransactionScope 交易，因此會一起認可。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-171">The SqlConnection and Entity Framework would both use the ambient TransactionScope transaction and hence be committed together.</span></span>  

<span data-ttu-id="a9ff5-172">從 .NET 4.5.1 TransactionScope 開始，已更新為也會透過使用[TransactionScopeAsyncFlowOption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx)列舉來處理非同步方法：</span><span class="sxs-lookup"><span data-stu-id="a9ff5-172">Starting with .NET 4.5.1 TransactionScope has been updated to also work with asynchronous methods via the use of the [TransactionScopeAsyncFlowOption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx) enumeration:</span></span>  

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

<span data-ttu-id="a9ff5-173">TransactionScope 方法仍有一些限制：</span><span class="sxs-lookup"><span data-stu-id="a9ff5-173">There are still some limitations to the TransactionScope approach:</span></span>  

- <span data-ttu-id="a9ff5-174">需要 .NET 4.5.1 或更新版本才能使用非同步方法。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-174">Requires .NET 4.5.1 or greater to work with asynchronous methods.</span></span>  
- <span data-ttu-id="a9ff5-175">除非您確定只有一個連線（雲端案例不支援分散式交易），否則無法在雲端案例中使用它。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-175">It cannot be used in cloud scenarios unless you are sure you have one and only one connection (cloud scenarios do not support distributed transactions).</span></span>  
- <span data-ttu-id="a9ff5-176">它無法與先前章節的 UseTransaction （）方法結合。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-176">It cannot be combined with the Database.UseTransaction() approach of the previous sections.</span></span>  
- <span data-ttu-id="a9ff5-177">如果您發出任何 DDL，而且尚未透過 MSDTC 服務啟用分散式交易，它就會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-177">It will throw exceptions if you issue any DDL and have not enabled distributed transactions through the MSDTC Service.</span></span>  

<span data-ttu-id="a9ff5-178">TransactionScope 方法的優點：</span><span class="sxs-lookup"><span data-stu-id="a9ff5-178">Advantages of the TransactionScope approach:</span></span>  

- <span data-ttu-id="a9ff5-179">如果您對指定的資料庫進行多個連接，或將連接與相同交易內的不同資料庫連接結合到某個資料庫，它就會自動將本機交易升級為分散式交易（注意：您必須擁有MSDTC 服務已設定為允許此作業使用分散式交易）。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-179">It will automatically upgrade a local transaction to a distributed transaction if you make more than one connection to a given database or combine a connection to one database with a connection to a different database within the same transaction (note: you must have the MSDTC service configured to allow distributed transactions for this to work).</span></span>  
- <span data-ttu-id="a9ff5-180">輕鬆撰寫程式碼。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-180">Ease of coding.</span></span> <span data-ttu-id="a9ff5-181">如果您偏好在背景中以隱含的方式處理交易，而不是明確地進行控制，則 TransactionScope 方法可能會符合您的效果。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-181">If you prefer the transaction to be ambient and dealt with implicitly in the background rather than explicitly under you control then the TransactionScope approach may suit you better.</span></span>  

<span data-ttu-id="a9ff5-182">總而言之，使用上述的新 BeginTransaction （）和 UseTransaction （） Api 時，大部分的使用者不再需要 TransactionScope 方法。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-182">In summary, with the new Database.BeginTransaction() and Database.UseTransaction() APIs above, the TransactionScope approach is no longer necessary for most users.</span></span> <span data-ttu-id="a9ff5-183">如果您繼續使用 TransactionScope，則請注意上述限制。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-183">If you do continue to use TransactionScope then be aware of the above limitations.</span></span> <span data-ttu-id="a9ff5-184">我們建議您盡可能改用先前章節中所述的方法。</span><span class="sxs-lookup"><span data-stu-id="a9ff5-184">We recommend using the approach outlined in the previous sections instead where possible.</span></span>  
