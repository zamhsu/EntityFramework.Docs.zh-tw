---
title: 使用交易-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 0d0f1824-d781-4cb3-8fda-b7eaefced1cd
ms.openlocfilehash: 20b63c88c41c10b5a69660d5027097c647c7eedd
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997547"
---
# <a name="working-with-transactions"></a><span data-ttu-id="22ed8-102">使用交易</span><span class="sxs-lookup"><span data-stu-id="22ed8-102">Working with Transactions</span></span>
> [!NOTE]
> <span data-ttu-id="22ed8-103">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="22ed8-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="22ed8-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="22ed8-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="22ed8-105">本文件將說明如何使用交易在 EF6 中包括 EF5 以方便使用的交易之後，我們已新增的增強功能。</span><span class="sxs-lookup"><span data-stu-id="22ed8-105">This document will describe using transactions in EF6 including the enhancements we have added since EF5 to make working with transactions easy.</span></span>  

## <a name="what-ef-does-by-default"></a><span data-ttu-id="22ed8-106">依預設沒有的 EF</span><span class="sxs-lookup"><span data-stu-id="22ed8-106">What EF does by default</span></span>  

<span data-ttu-id="22ed8-107">在所有版本的 Entity Framework 中，每當您執行**savechanges （)** 來插入、 更新或刪除資料庫架構會在交易中包裝該作業。</span><span class="sxs-lookup"><span data-stu-id="22ed8-107">In all versions of Entity Framework, whenever you execute **SaveChanges()** to insert, update or delete on the database the framework will wrap that operation in a transaction.</span></span> <span data-ttu-id="22ed8-108">此交易執行的作業持續時間，僅足夠的時間，並再完成。</span><span class="sxs-lookup"><span data-stu-id="22ed8-108">This transaction lasts only long enough to execute the operation and then completes.</span></span> <span data-ttu-id="22ed8-109">當您執行這類另一項作業會啟動新的交易。</span><span class="sxs-lookup"><span data-stu-id="22ed8-109">When you execute another such operation a new transaction is started.</span></span>  

<span data-ttu-id="22ed8-110">從 EF6 **Database.ExecuteSqlCommand()** 依預設會自動換行命令在交易中如果其中一個已不存在。</span><span class="sxs-lookup"><span data-stu-id="22ed8-110">Starting with EF6 **Database.ExecuteSqlCommand()** by default will wrap the command in a transaction if one was not already present.</span></span> <span data-ttu-id="22ed8-111">有這個方法可讓您覆寫這個行為，如果您想要的多載。</span><span class="sxs-lookup"><span data-stu-id="22ed8-111">There are overloads of this method that allow you to override this behavior if you wish.</span></span> <span data-ttu-id="22ed8-112">也在 EF6 執行，例如透過 Api 模型中包含的預存程序**ObjectContext.ExecuteFunction()** 會執行相同，差別在於無法目前會覆寫預設行為）。</span><span class="sxs-lookup"><span data-stu-id="22ed8-112">Also in EF6 execution of stored procedures included in the model through APIs such as **ObjectContext.ExecuteFunction()** does the same (except that the default behavior cannot at the moment be overridden).</span></span>  

<span data-ttu-id="22ed8-113">在任一情況下，交易的隔離等級會是任何隔離等級的資料庫提供者會考慮其預設值。</span><span class="sxs-lookup"><span data-stu-id="22ed8-113">In either case, the isolation level of the transaction is whatever isolation level the database provider considers its default setting.</span></span> <span data-ttu-id="22ed8-114">根據預設，比方說，在 SQL Server 這是 READ COMMITTED。</span><span class="sxs-lookup"><span data-stu-id="22ed8-114">By default, for instance, on SQL Server this is READ COMMITTED.</span></span>  

<span data-ttu-id="22ed8-115">Entity Framework 不會包裝在交易中的查詢。</span><span class="sxs-lookup"><span data-stu-id="22ed8-115">Entity Framework does not wrap queries in a transaction.</span></span>  

<span data-ttu-id="22ed8-116">這項功能預設是適合大量的使用者，和如果因此就不需要執行什麼工作在 EF6;如往常一般，只需撰寫程式碼。</span><span class="sxs-lookup"><span data-stu-id="22ed8-116">This default functionality is suitable for a lot of users and if so there is no need to do anything different in EF6; just write the code as you always did.</span></span>  

<span data-ttu-id="22ed8-117">不過有些使用者需要更充分地掌控其交易 – 這會涵蓋下列各節。</span><span class="sxs-lookup"><span data-stu-id="22ed8-117">However some users require greater control over their transactions – this is covered in the following sections.</span></span>  

## <a name="how-the-apis-work"></a><span data-ttu-id="22ed8-118">Api 的運作方式</span><span class="sxs-lookup"><span data-stu-id="22ed8-118">How the APIs work</span></span>  

<span data-ttu-id="22ed8-119">在 EF6 Entity Framework 之前堅持開啟資料庫連接本身 （如果它已傳遞原本就已經開啟的連接，它發生例外狀況）。</span><span class="sxs-lookup"><span data-stu-id="22ed8-119">Prior to EF6 Entity Framework insisted on opening the database connection itself (it threw an exception if it was passed a connection that was already open).</span></span> <span data-ttu-id="22ed8-120">因為只能在開啟的連接上啟動交易，這表示使用者無法包裝成一筆交易的數個作業的唯一方式是使用[TransactionScope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx) ，或使用**ObjectContext.Connection**屬性，並開始呼叫**open （)** 並**BeginTransaction()** 直接對傳回**EntityConnection**物件。</span><span class="sxs-lookup"><span data-stu-id="22ed8-120">Since a transaction can only be started on an open connection, this meant that the only way a user could wrap several operations into one transaction was either to use a [TransactionScope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx) or use the **ObjectContext.Connection** property and start calling **Open()** and **BeginTransaction()** directly on the returned **EntityConnection** object.</span></span> <span data-ttu-id="22ed8-121">此外，連絡資料庫 API 呼叫也會失敗，如果您已在自己的基礎資料庫連接上開始交易。</span><span class="sxs-lookup"><span data-stu-id="22ed8-121">In addition, API calls which contacted the database would fail if you had started a transaction on the underlying database connection on your own.</span></span>  

> [!NOTE]
> <span data-ttu-id="22ed8-122">Entity Framework 6 已移除的只接受已關閉的連線限制。</span><span class="sxs-lookup"><span data-stu-id="22ed8-122">The limitation of only accepting closed connections was removed in Entity Framework 6.</span></span> <span data-ttu-id="22ed8-123">如需詳細資訊，請參閱 <<c0> [ 連線的管理](~/ef6/fundamentals/connection-management.md)。</span><span class="sxs-lookup"><span data-stu-id="22ed8-123">For details, see [Connection Management](~/ef6/fundamentals/connection-management.md).</span></span>  

<span data-ttu-id="22ed8-124">從 EF6 framework 現在提供：</span><span class="sxs-lookup"><span data-stu-id="22ed8-124">Starting with EF6 the framework now provides:</span></span>  

1. <span data-ttu-id="22ed8-125">**Database.BeginTransaction()** ： 啟動與完成在現有的 DbContext – 可讓數個作業結合在相同交易內的交易本身使用者更容易的方法，因此所有認可或全部回復為其中一個。</span><span class="sxs-lookup"><span data-stu-id="22ed8-125">**Database.BeginTransaction()** : An easier method for a user to start and complete transactions themselves within an existing DbContext – allowing several operations to be combined within the same transaction and hence either all committed or all rolled back as one.</span></span> <span data-ttu-id="22ed8-126">它也可讓使用者更輕鬆地指定交易的隔離等級。</span><span class="sxs-lookup"><span data-stu-id="22ed8-126">It also allows the user to more easily specify the isolation level for the transaction.</span></span>  
2. <span data-ttu-id="22ed8-127">**Database.UseTransaction()** ： 可讓使用 Entity Framework 之外啟動的交易 DbContext。</span><span class="sxs-lookup"><span data-stu-id="22ed8-127">**Database.UseTransaction()** : which allows the DbContext to use a transaction which was started outside of the Entity Framework.</span></span>  

### <a name="combining-several-operations-into-one-transaction-within-the-same-context"></a><span data-ttu-id="22ed8-128">結合到一個異動相同內容中的數個作業</span><span class="sxs-lookup"><span data-stu-id="22ed8-128">Combining several operations into one transaction within the same context</span></span>  

<span data-ttu-id="22ed8-129">**Database.BeginTransaction()** 有兩個覆寫 – 其中一個會採用明確[IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx) ，另一個會採用任何引數，並使用從基礎資料庫提供者的 IsolationLevel 預設值。</span><span class="sxs-lookup"><span data-stu-id="22ed8-129">**Database.BeginTransaction()** has two overrides – one which takes an explicit [IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx) and one which takes no arguments and uses the default IsolationLevel from the underlying database provider.</span></span> <span data-ttu-id="22ed8-130">傳回兩個覆寫**DbContextTransaction**物件，其提供**commit （)** 並**rollback （)** 對基礎存放區中的認可和回復方法交易。</span><span class="sxs-lookup"><span data-stu-id="22ed8-130">Both overrides return a **DbContextTransaction** object which provides **Commit()** and **Rollback()** methods which perform commit and rollback on the underlying store transaction.</span></span>  

<span data-ttu-id="22ed8-131">**DbContextTransaction**要認可或回復之後加以處置。</span><span class="sxs-lookup"><span data-stu-id="22ed8-131">The **DbContextTransaction** is meant to be disposed once it has been committed or rolled back.</span></span> <span data-ttu-id="22ed8-132">其中一個簡便的方式完成這項作業是**using(...){...}**</span><span class="sxs-lookup"><span data-stu-id="22ed8-132">One easy way to accomplish this is the **using(…) {…}**</span></span> <span data-ttu-id="22ed8-133">語法會自動呼叫這**dispose （)** using 區塊時完成：</span><span class="sxs-lookup"><span data-stu-id="22ed8-133">syntax which will automatically call **Dispose()** when the using block completes:</span></span>  

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
> <span data-ttu-id="22ed8-134">開始交易需要基礎存放區連接已開啟。</span><span class="sxs-lookup"><span data-stu-id="22ed8-134">Beginning a transaction requires that the underlying store connection is open.</span></span> <span data-ttu-id="22ed8-135">如果尚未開啟，因此，呼叫 Database.BeginTransaction() 會開啟連接。</span><span class="sxs-lookup"><span data-stu-id="22ed8-135">So calling Database.BeginTransaction() will open the connection  if it is not already opened.</span></span> <span data-ttu-id="22ed8-136">如果 DbContextTransaction 開啟連接然後它會關閉它呼叫 dispose （） 時。</span><span class="sxs-lookup"><span data-stu-id="22ed8-136">If DbContextTransaction opened the connection then it will close it when Dispose() is called.</span></span>  

### <a name="passing-an-existing-transaction-to-the-context"></a><span data-ttu-id="22ed8-137">將現有的交易傳遞至內容</span><span class="sxs-lookup"><span data-stu-id="22ed8-137">Passing an existing transaction to the context</span></span>  

<span data-ttu-id="22ed8-138">有時您想要的交易，甚至會更廣範圍內且完全包含作業相同的資料庫上，但在 EF 外部。</span><span class="sxs-lookup"><span data-stu-id="22ed8-138">Sometimes you would like a transaction which is even broader in scope and which includes operations on the same database but outside of EF completely.</span></span> <span data-ttu-id="22ed8-139">若要這麼做必須開啟連接和自行啟動交易，然後告知 EF a） 若要使用已開啟的資料庫連接，以及 b） 若要在該連接上使用現有的交易。</span><span class="sxs-lookup"><span data-stu-id="22ed8-139">To accomplish this you must open the connection and start the transaction yourself and then tell EF a) to use the already-opened database connection, and b) to use the existing transaction on that connection.</span></span>  

<span data-ttu-id="22ed8-140">若要這樣做，您必須定義，並使用您的內容類別繼承自一個 DbContext 建構函式需要 i） 現有的連線參數和 ii) contextOwnsConnection 布林建構函式。</span><span class="sxs-lookup"><span data-stu-id="22ed8-140">To do this you must define and use a constructor on your context class which inherits from one of the DbContext constructors which take i) an existing connection parameter and ii) the contextOwnsConnection boolean.</span></span>  

> [!NOTE]
> <span data-ttu-id="22ed8-141">ContextOwnsConnection 旗標必須設定為 false，在此案例中呼叫時。</span><span class="sxs-lookup"><span data-stu-id="22ed8-141">The contextOwnsConnection flag must be set to false when called in this scenario.</span></span> <span data-ttu-id="22ed8-142">這十分重要，因為它會通知 Entity Framework，它應該不關閉連線時執行的動作 （例如，請參閱下列 4 行）：</span><span class="sxs-lookup"><span data-stu-id="22ed8-142">This is important as it informs Entity Framework that it should not close the connection when it is done with it (for example, see line 4 below):</span></span>  

``` csharp
using (var conn = new SqlConnection("..."))
{
    conn.Open();
    using (var context = new BloggingContext(conn, contextOwnsConnection: false))
    {
    }
}
```  

<span data-ttu-id="22ed8-143">此外，您必須自行 （如果您想要避免預設設定，包括 IsolationLevel） 啟動交易，並可讓 Entity Framework 知道現有的交易已經開始在此連接上 （請參閱列下方的 33）。</span><span class="sxs-lookup"><span data-stu-id="22ed8-143">Furthermore, you must start the transaction yourself (including the IsolationLevel if you want to avoid the default setting) and let Entity Framework know that there is an existing transaction already started on the connection (see line 33 below).</span></span>  

<span data-ttu-id="22ed8-144">然後，您可自行直接 SqlConnection 本身，或在 DbContext 上執行資料庫作業。</span><span class="sxs-lookup"><span data-stu-id="22ed8-144">Then you are free to execute database operations either directly on the SqlConnection itself, or on the DbContext.</span></span> <span data-ttu-id="22ed8-145">一筆交易內執行所有這類作業。</span><span class="sxs-lookup"><span data-stu-id="22ed8-145">All such operations are executed within one transaction.</span></span> <span data-ttu-id="22ed8-146">您負責認可或回復交易並呼叫 dispose （），以及關閉和處置的資料庫連接。</span><span class="sxs-lookup"><span data-stu-id="22ed8-146">You take responsibility for committing or rolling back the transaction and for calling Dispose() on it, as well as for closing and disposing the database connection.</span></span> <span data-ttu-id="22ed8-147">例如: </span><span class="sxs-lookup"><span data-stu-id="22ed8-147">For example:</span></span>  

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

### <a name="clearing-up-the-transaction"></a><span data-ttu-id="22ed8-148">清除交易</span><span class="sxs-lookup"><span data-stu-id="22ed8-148">Clearing up the transaction</span></span>

<span data-ttu-id="22ed8-149">您可以傳遞 null 給 Database.UseTransaction() 以清除目前交易的 Entity Framework 的知識。</span><span class="sxs-lookup"><span data-stu-id="22ed8-149">You can pass null to Database.UseTransaction() to clear Entity Framework’s knowledge of the current transaction.</span></span> <span data-ttu-id="22ed8-150">Entity Framework 會將未認可或復原現有的交易時這樣做，因此請小心使用，並只有當您確定這是您要做什麼。</span><span class="sxs-lookup"><span data-stu-id="22ed8-150">Entity Framework will neither commit nor rollback the existing transaction when you do this, so use with care and only if you’re sure this is what you want to do.</span></span>  

### <a name="errors-in-usetransaction"></a><span data-ttu-id="22ed8-151">UseTransaction 中的錯誤</span><span class="sxs-lookup"><span data-stu-id="22ed8-151">Errors in UseTransaction</span></span>

<span data-ttu-id="22ed8-152">如果您傳遞的交易，您會看到從 Database.UseTransaction() 例外狀況時：</span><span class="sxs-lookup"><span data-stu-id="22ed8-152">You will see an exception from Database.UseTransaction() if you pass a transaction when:</span></span>  
- <span data-ttu-id="22ed8-153">Entity Framework 已有現有的交易</span><span class="sxs-lookup"><span data-stu-id="22ed8-153">Entity Framework already has an existing transaction</span></span>  
- <span data-ttu-id="22ed8-154">TransactionScope 內已運作 entity Framework</span><span class="sxs-lookup"><span data-stu-id="22ed8-154">Entity Framework is already operating within a TransactionScope</span></span>  
- <span data-ttu-id="22ed8-155">傳遞的交易中的連接物件為 null。</span><span class="sxs-lookup"><span data-stu-id="22ed8-155">The connection object in the transaction passed is null.</span></span> <span data-ttu-id="22ed8-156">也就是因為交易並未與連接相關聯，– 這通常是表示該交易已完成</span><span class="sxs-lookup"><span data-stu-id="22ed8-156">That is, the transaction is not associated with a connection – usually this is a sign that that transaction has already completed</span></span>  
- <span data-ttu-id="22ed8-157">連接物件中傳遞的交易與 Entity Framework 的連線不相符。</span><span class="sxs-lookup"><span data-stu-id="22ed8-157">The connection object in the transaction passed does not match the Entity Framework’s connection.</span></span>  

## <a name="using-transactions-with-other-features"></a><span data-ttu-id="22ed8-158">使用其他功能的交易</span><span class="sxs-lookup"><span data-stu-id="22ed8-158">Using transactions with other features</span></span>  

<span data-ttu-id="22ed8-159">本節將詳細說明上述的交易與互動的方式：</span><span class="sxs-lookup"><span data-stu-id="22ed8-159">This section details how the above transactions interact with:</span></span>  

- <span data-ttu-id="22ed8-160">連線恢復功能</span><span class="sxs-lookup"><span data-stu-id="22ed8-160">Connection resiliency</span></span>  
- <span data-ttu-id="22ed8-161">非同步方法</span><span class="sxs-lookup"><span data-stu-id="22ed8-161">Asynchronous methods</span></span>  
- <span data-ttu-id="22ed8-162">TransactionScope 交易</span><span class="sxs-lookup"><span data-stu-id="22ed8-162">TransactionScope transactions</span></span>  

### <a name="connection-resiliency"></a><span data-ttu-id="22ed8-163">連線復原能力</span><span class="sxs-lookup"><span data-stu-id="22ed8-163">Connection Resiliency</span></span>  

<span data-ttu-id="22ed8-164">新的連接恢復功能不適用於使用者起始交易。</span><span class="sxs-lookup"><span data-stu-id="22ed8-164">The new Connection Resiliency feature does not work with user-initiated transactions.</span></span> <span data-ttu-id="22ed8-165">如需詳細資訊，請參閱 <<c0> [ 重試執行策略限制](~/ef6/fundamentals/connection-resiliency/retry-logic.md#limitations)。</span><span class="sxs-lookup"><span data-stu-id="22ed8-165">For details, see [Limitations with Retrying Execution Strategies](~/ef6/fundamentals/connection-resiliency/retry-logic.md#limitations).</span></span>  

### <a name="asynchronous-programming"></a><span data-ttu-id="22ed8-166">非同步程式設計</span><span class="sxs-lookup"><span data-stu-id="22ed8-166">Asynchronous Programming</span></span>  

<span data-ttu-id="22ed8-167">前幾節所述的方法不需要進一步選項或設定值來處理[非同步查詢並儲存方法](~/ef6/fundamentals/async.md
)。</span><span class="sxs-lookup"><span data-stu-id="22ed8-167">The approach outlined in the previous sections needs no further options or settings to work with the [asynchronous query and save methods](~/ef6/fundamentals/async.md
).</span></span> <span data-ttu-id="22ed8-168">但請注意，根據您執行的非同步方法內，這可能會導致長時間執行交易-這可能又會導致死結或封鎖的不好的整體應用程式的效能。</span><span class="sxs-lookup"><span data-stu-id="22ed8-168">But be aware that, depending on what you do within the asynchronous methods, this may result in long-running transactions – which can in turn cause deadlocks or blocking which is bad for the performance of the overall application.</span></span>  

### <a name="transactionscope-transactions"></a><span data-ttu-id="22ed8-169">TransactionScope 交易</span><span class="sxs-lookup"><span data-stu-id="22ed8-169">TransactionScope Transactions</span></span>  

<span data-ttu-id="22ed8-170">在 EF6 之前提供較大範圍的交易的建議的方式是使用 TransactionScope 物件：</span><span class="sxs-lookup"><span data-stu-id="22ed8-170">Prior to EF6 the recommended way of providing larger scope transactions was to use a TransactionScope object:</span></span>  

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

<span data-ttu-id="22ed8-171">SqlConnection 和 Entity Framework 會同時使用環境的 TransactionScope 交易，因此會認可在一起。</span><span class="sxs-lookup"><span data-stu-id="22ed8-171">The SqlConnection and Entity Framework would both use the ambient TransactionScope transaction and hence be committed together.</span></span>  

<span data-ttu-id="22ed8-172">開始使用.NET 4.5.1 TransactionScope 已更新為也適用於非同步方法，透過使用[TransactionScopeAsyncFlowOption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx)列舉型別：</span><span class="sxs-lookup"><span data-stu-id="22ed8-172">Starting with .NET 4.5.1 TransactionScope has been updated to also work with asynchronous methods via the use of the [TransactionScopeAsyncFlowOption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx) enumeration:</span></span>  

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

<span data-ttu-id="22ed8-173">仍有一些限制，TransactionScope 方法：</span><span class="sxs-lookup"><span data-stu-id="22ed8-173">There are still some limitations to the TransactionScope approach:</span></span>  

- <span data-ttu-id="22ed8-174">需要.NET 4.5.1 或更新版本使用非同步方法。</span><span class="sxs-lookup"><span data-stu-id="22ed8-174">Requires .NET 4.5.1 or greater to work with asynchronous methods.</span></span>  
- <span data-ttu-id="22ed8-175">它不能在雲端案例除非您確定您有只有一個連線 （雲端案例不支援分散式的交易）。</span><span class="sxs-lookup"><span data-stu-id="22ed8-175">It cannot be used in cloud scenarios unless you are sure you have one and only one connection (cloud scenarios do not support distributed transactions).</span></span>  
- <span data-ttu-id="22ed8-176">它不能結合前一節的 Database.UseTransaction() 方法。</span><span class="sxs-lookup"><span data-stu-id="22ed8-176">It cannot be combined with the Database.UseTransaction() approach of the previous sections.</span></span>  
- <span data-ttu-id="22ed8-177">如果發出的任何 DLL，而且您尚未啟用透過 MSDTC 服務的分散式的交易，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="22ed8-177">It will throw exceptions if you issue any DDL and have not enabled distributed transactions through the MSDTC Service.</span></span>  

<span data-ttu-id="22ed8-178">TransactionScope 方法的優點：</span><span class="sxs-lookup"><span data-stu-id="22ed8-178">Advantages of the TransactionScope approach:</span></span>  

- <span data-ttu-id="22ed8-179">它將會自動升級本機交易為分散式交易如果您讓多個連接到指定的資料庫，或結合在相同交易內的不同資料庫的連接中的一個資料庫的連接 (請注意： 您必須擁有MSDTC 服務設定為允許針對此目的的分散式的交易）。</span><span class="sxs-lookup"><span data-stu-id="22ed8-179">It will automatically upgrade a local transaction to a distributed transaction if you make more than one connection to a given database or combine a connection to one database with a connection to a different database within the same transaction (note: you must have the MSDTC service configured to allow distributed transactions for this to work).</span></span>  
- <span data-ttu-id="22ed8-180">撰寫程式碼簡化。</span><span class="sxs-lookup"><span data-stu-id="22ed8-180">Ease of coding.</span></span> <span data-ttu-id="22ed8-181">如果您想為環境並使用隱含地在背景發出交易，而不是明確地在您控制然後 TransactionScope 方法可能比較適合您好。</span><span class="sxs-lookup"><span data-stu-id="22ed8-181">If you prefer the transaction to be ambient and dealt with implicitly in the background rather than explicitly under you control then the TransactionScope approach may suit you better.</span></span>  

<span data-ttu-id="22ed8-182">總而言之，使用新的 Database.BeginTransaction() 和上述的 Database.UseTransaction() Api TransactionScope 方法不再需要的大部分的使用者。</span><span class="sxs-lookup"><span data-stu-id="22ed8-182">In summary, with the new Database.BeginTransaction() and Database.UseTransaction() APIs above, the TransactionScope approach is no longer necessary for most users.</span></span> <span data-ttu-id="22ed8-183">如果您不要繼續使用 TransactionScope 則是注意上述限制。</span><span class="sxs-lookup"><span data-stu-id="22ed8-183">If you do continue to use TransactionScope then be aware of the above limitations.</span></span> <span data-ttu-id="22ed8-184">我們建議使用在先前章節中改為描述，可能的方法。</span><span class="sxs-lookup"><span data-stu-id="22ed8-184">We recommend using the approach outlined in the previous sections instead where possible.</span></span>  
