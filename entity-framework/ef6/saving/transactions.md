---
title: 使用交易-EF6
description: 使用 Entity Framework 6 中的交易
author: divega
ms.date: 10/23/2016
uid: ef6/saving/transactions
ms.openlocfilehash: 15cc2171338defe482767114c58afe16cc1ffe21
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073713"
---
# <a name="working-with-transactions"></a><span data-ttu-id="74249-103">使用交易</span><span class="sxs-lookup"><span data-stu-id="74249-103">Working with Transactions</span></span>
> [!NOTE]
> <span data-ttu-id="74249-104">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="74249-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="74249-105">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="74249-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="74249-106">本檔將說明如何使用 EF6 中的交易，包括自 EF5 以來加入的增強功能，讓您輕鬆處理交易。</span><span class="sxs-lookup"><span data-stu-id="74249-106">This document will describe using transactions in EF6 including the enhancements we have added since EF5 to make working with transactions easy.</span></span>  

## <a name="what-ef-does-by-default"></a><span data-ttu-id="74249-107">EF 預設的功能</span><span class="sxs-lookup"><span data-stu-id="74249-107">What EF does by default</span></span>  

<span data-ttu-id="74249-108">在所有版本的 Entity Framework 中，每當您執行 \*\*SaveChanges ( # B1 \*\* 來插入、更新或刪除資料庫時，架構會將該作業包裝在交易中。</span><span class="sxs-lookup"><span data-stu-id="74249-108">In all versions of Entity Framework, whenever you execute **SaveChanges()** to insert, update or delete on the database the framework will wrap that operation in a transaction.</span></span> <span data-ttu-id="74249-109">此交易的持續時間只有足夠的時間可執行作業，然後完成。</span><span class="sxs-lookup"><span data-stu-id="74249-109">This transaction lasts only long enough to execute the operation and then completes.</span></span> <span data-ttu-id="74249-110">當您執行其他這類作業時，就會啟動新的交易。</span><span class="sxs-lookup"><span data-stu-id="74249-110">When you execute another such operation a new transaction is started.</span></span>  

<span data-ttu-id="74249-111">從 EF6 開始 \*\*Database.ExecuteSqlCommand ( # B1 \*\* 預設會將命令包裝在交易中（如果尚未存在的話）。</span><span class="sxs-lookup"><span data-stu-id="74249-111">Starting with EF6 **Database.ExecuteSqlCommand()** by default will wrap the command in a transaction if one was not already present.</span></span> <span data-ttu-id="74249-112">這種方法有多載，可讓您視需要覆寫此行為。</span><span class="sxs-lookup"><span data-stu-id="74249-112">There are overloads of this method that allow you to override this behavior if you wish.</span></span> <span data-ttu-id="74249-113">此外，透過 Api （例如 \*\*ObjectContext.ExecuteFunction ( # B1 \*\* ）在模型中包含的預存程式執行也會執行相同的 (，但目前無法覆寫) 的預設行為。</span><span class="sxs-lookup"><span data-stu-id="74249-113">Also in EF6 execution of stored procedures included in the model through APIs such as **ObjectContext.ExecuteFunction()** does the same (except that the default behavior cannot at the moment be overridden).</span></span>  

<span data-ttu-id="74249-114">在任一種情況下，交易的隔離等級就是資料庫提供者視為其預設設定的任何隔離等級。</span><span class="sxs-lookup"><span data-stu-id="74249-114">In either case, the isolation level of the transaction is whatever isolation level the database provider considers its default setting.</span></span> <span data-ttu-id="74249-115">例如，根據預設，在 SQL Server 此為讀取認可。</span><span class="sxs-lookup"><span data-stu-id="74249-115">By default, for instance, on SQL Server this is READ COMMITTED.</span></span>  

<span data-ttu-id="74249-116">Entity Framework 不會將查詢包裝在交易中。</span><span class="sxs-lookup"><span data-stu-id="74249-116">Entity Framework does not wrap queries in a transaction.</span></span>  

<span data-ttu-id="74249-117">這項預設功能適合許多使用者，如果有，就不需要在 EF6 中進行任何不同的動作;只要像往常一樣撰寫程式碼即可。</span><span class="sxs-lookup"><span data-stu-id="74249-117">This default functionality is suitable for a lot of users and if so there is no need to do anything different in EF6; just write the code as you always did.</span></span>  

<span data-ttu-id="74249-118">不過，某些使用者需要對其交易有更大的控制權–這會在下列各節中說明。</span><span class="sxs-lookup"><span data-stu-id="74249-118">However some users require greater control over their transactions – this is covered in the following sections.</span></span>  

## <a name="how-the-apis-work"></a><span data-ttu-id="74249-119">Api 的運作方式</span><span class="sxs-lookup"><span data-stu-id="74249-119">How the APIs work</span></span>  

<span data-ttu-id="74249-120">在 EF6 之前 Entity Framework 堅持認為開啟資料庫連接本身時 (它會在傳遞已) 開啟的連接時擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="74249-120">Prior to EF6 Entity Framework insisted on opening the database connection itself (it threw an exception if it was passed a connection that was already open).</span></span> <span data-ttu-id="74249-121">由於交易只能在開啟的連接上啟動，這表示使用者唯一能夠將數個作業包裝成一個交易的方式，就是使用[TransactionScope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx)或使用**ObjectCoNtext. connection**屬性，並開始直接在傳回的**EntityConnection**物件上呼叫**open ( # B1**和**BeginTransaction ( # B3** 。</span><span class="sxs-lookup"><span data-stu-id="74249-121">Since a transaction can only be started on an open connection, this meant that the only way a user could wrap several operations into one transaction was either to use a [TransactionScope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx) or use the **ObjectContext.Connection** property and start calling **Open()** and **BeginTransaction()** directly on the returned **EntityConnection** object.</span></span> <span data-ttu-id="74249-122">此外，如果您已在基礎資料庫連接上啟動交易，則與資料庫聯繫的 API 呼叫將會失敗。</span><span class="sxs-lookup"><span data-stu-id="74249-122">In addition, API calls which contacted the database would fail if you had started a transaction on the underlying database connection on your own.</span></span>  

> [!NOTE]
> <span data-ttu-id="74249-123">Entity Framework 6 中已移除僅接受已關閉連接的限制。</span><span class="sxs-lookup"><span data-stu-id="74249-123">The limitation of only accepting closed connections was removed in Entity Framework 6.</span></span> <span data-ttu-id="74249-124">如需詳細資訊，請參閱 [連接管理](xref:ef6/fundamentals/connection-management)。</span><span class="sxs-lookup"><span data-stu-id="74249-124">For details, see [Connection Management](xref:ef6/fundamentals/connection-management).</span></span>  

<span data-ttu-id="74249-125">從 EF6 開始，架構現在提供：</span><span class="sxs-lookup"><span data-stu-id="74249-125">Starting with EF6 the framework now provides:</span></span>  

1. <span data-ttu-id="74249-126">\*\*BeginTransaction ( # B1 \*\* ：更簡單的方法，讓使用者在現有的 DbCoNtext 內自行啟動及完成交易，允許在相同的交易內結合數項作業，因此全部認可或全部回復為一個。</span><span class="sxs-lookup"><span data-stu-id="74249-126">**Database.BeginTransaction()** : An easier method for a user to start and complete transactions themselves within an existing DbContext – allowing several operations to be combined within the same transaction and hence either all committed or all rolled back as one.</span></span> <span data-ttu-id="74249-127">它也可讓使用者更輕鬆地指定交易的隔離等級。</span><span class="sxs-lookup"><span data-stu-id="74249-127">It also allows the user to more easily specify the isolation level for the transaction.</span></span>  
2. <span data-ttu-id="74249-128">\*\*UseTransaction ( # B1 \*\* ：這可讓 DbCoNtext 使用在 Entity Framework 以外啟動的交易。</span><span class="sxs-lookup"><span data-stu-id="74249-128">**Database.UseTransaction()** : which allows the DbContext to use a transaction which was started outside of the Entity Framework.</span></span>  

### <a name="combining-several-operations-into-one-transaction-within-the-same-context"></a><span data-ttu-id="74249-129">將數個作業結合到相同內容中的一個交易</span><span class="sxs-lookup"><span data-stu-id="74249-129">Combining several operations into one transaction within the same context</span></span>  

<span data-ttu-id="74249-130">\*\*BeginTransaction ( # B1 \*\* 有兩個覆寫–一個接受明確的 [IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx) ，另一個則不接受任何引數，並且會使用基礎資料庫提供者的預設 IsolationLevel。</span><span class="sxs-lookup"><span data-stu-id="74249-130">**Database.BeginTransaction()** has two overrides – one which takes an explicit [IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx) and one which takes no arguments and uses the default IsolationLevel from the underlying database provider.</span></span> <span data-ttu-id="74249-131">這兩個覆寫會傳回 **DbCoNtextTransaction** 物件，此物件提供 \*\*Commit ( # B1 \*\* 和 \*\*Rollback ( # B3 \*\* 方法，這些方法會在基礎存放區交易上執行認可和回復。</span><span class="sxs-lookup"><span data-stu-id="74249-131">Both overrides return a **DbContextTransaction** object which provides **Commit()** and **Rollback()** methods which perform commit and rollback on the underlying store transaction.</span></span>  

<span data-ttu-id="74249-132">**DbCoNtextTransaction**的目的是要在認可或回復之後處置。</span><span class="sxs-lookup"><span data-stu-id="74249-132">The **DbContextTransaction** is meant to be disposed once it has been committed or rolled back.</span></span> <span data-ttu-id="74249-133">完成這項工作的一個簡單方式是 **使用 ( ... ) {...}**</span><span class="sxs-lookup"><span data-stu-id="74249-133">One easy way to accomplish this is the **using(…) {…}**</span></span> <span data-ttu-id="74249-134">當 using 區塊完成時，將會自動呼叫 \*\*Dispose ( # B1 \*\* 的語法：</span><span class="sxs-lookup"><span data-stu-id="74249-134">syntax which will automatically call **Dispose()** when the using block completes:</span></span>  

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
> <span data-ttu-id="74249-135">開始交易時，必須開啟基礎存放區連接。</span><span class="sxs-lookup"><span data-stu-id="74249-135">Beginning a transaction requires that the underlying store connection is open.</span></span> <span data-ttu-id="74249-136">因此，呼叫 BeginTransaction ( # A1 會開啟連接（如果尚未開啟）。</span><span class="sxs-lookup"><span data-stu-id="74249-136">So calling Database.BeginTransaction() will open the connection  if it is not already opened.</span></span> <span data-ttu-id="74249-137">如果 DbCoNtextTransaction 開啟連接，則會在呼叫 Dispose ( # A1 時將它關閉。</span><span class="sxs-lookup"><span data-stu-id="74249-137">If DbContextTransaction opened the connection then it will close it when Dispose() is called.</span></span>  

### <a name="passing-an-existing-transaction-to-the-context"></a><span data-ttu-id="74249-138">將現有交易傳遞至內容</span><span class="sxs-lookup"><span data-stu-id="74249-138">Passing an existing transaction to the context</span></span>  

<span data-ttu-id="74249-139">有時候您會想要在範圍內更廣泛的交易，包括在相同資料庫上的作業，但完全不包含在 EF 的外部。</span><span class="sxs-lookup"><span data-stu-id="74249-139">Sometimes you would like a transaction which is even broader in scope and which includes operations on the same database but outside of EF completely.</span></span> <span data-ttu-id="74249-140">若要完成此動作，您必須開啟連接並自行啟動交易，然後告訴 EF a) 使用已經開啟的資料庫連接，b) 使用該連接上的現有交易。</span><span class="sxs-lookup"><span data-stu-id="74249-140">To accomplish this you must open the connection and start the transaction yourself and then tell EF a) to use the already-opened database connection, and b) to use the existing transaction on that connection.</span></span>  

<span data-ttu-id="74249-141">若要這樣做，您必須在內容類別上定義和使用函式，而該函式繼承自其中一個 DbCoNtext 的函式，而該函式會) 現有的連接參數，而 ii) coNtextOwnsConnection 布林值。</span><span class="sxs-lookup"><span data-stu-id="74249-141">To do this you must define and use a constructor on your context class which inherits from one of the DbContext constructors which take i) an existing connection parameter and ii) the contextOwnsConnection boolean.</span></span>  

> [!NOTE]
> <span data-ttu-id="74249-142">在此案例中呼叫 coNtextOwnsConnection 旗標時，必須設定為 false。</span><span class="sxs-lookup"><span data-stu-id="74249-142">The contextOwnsConnection flag must be set to false when called in this scenario.</span></span> <span data-ttu-id="74249-143">這點很重要，因為它會通知 Entity Framework 它在完成時不應該關閉連接 (例如，請參閱下面的第4行) ：</span><span class="sxs-lookup"><span data-stu-id="74249-143">This is important as it informs Entity Framework that it should not close the connection when it is done with it (for example, see line 4 below):</span></span>  

``` csharp
using (var conn = new SqlConnection("..."))
{
    conn.Open();
    using (var context = new BloggingContext(conn, contextOwnsConnection: false))
    {
    }
}
```  

<span data-ttu-id="74249-144">此外，您必須自行啟動交易 (包括 IsolationLevel 如果您想要避免預設設定) 並讓 Entity Framework 知道連線上已啟動現有的交易 (請參閱以下) 的行33。</span><span class="sxs-lookup"><span data-stu-id="74249-144">Furthermore, you must start the transaction yourself (including the IsolationLevel if you want to avoid the default setting) and let Entity Framework know that there is an existing transaction already started on the connection (see line 33 below).</span></span>  

<span data-ttu-id="74249-145">然後，您可以直接在 SqlConnection 本身或 DbCoNtext 上執行資料庫作業。</span><span class="sxs-lookup"><span data-stu-id="74249-145">Then you are free to execute database operations either directly on the SqlConnection itself, or on the DbContext.</span></span> <span data-ttu-id="74249-146">所有這類作業都是在一個交易內執行。</span><span class="sxs-lookup"><span data-stu-id="74249-146">All such operations are executed within one transaction.</span></span> <span data-ttu-id="74249-147">您必須負責認可或回復交易，以及在其上呼叫 Dispose ( # A1，以及關閉和處置資料庫連接。</span><span class="sxs-lookup"><span data-stu-id="74249-147">You take responsibility for committing or rolling back the transaction and for calling Dispose() on it, as well as for closing and disposing the database connection.</span></span> <span data-ttu-id="74249-148">例如：</span><span class="sxs-lookup"><span data-stu-id="74249-148">For example:</span></span>  

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

### <a name="clearing-up-the-transaction"></a><span data-ttu-id="74249-149">清除交易</span><span class="sxs-lookup"><span data-stu-id="74249-149">Clearing up the transaction</span></span>

<span data-ttu-id="74249-150">您可以將 null 傳遞給 UseTransaction ( # A1，以清除 Entity Framework 對目前交易的知識。</span><span class="sxs-lookup"><span data-stu-id="74249-150">You can pass null to Database.UseTransaction() to clear Entity Framework’s knowledge of the current transaction.</span></span> <span data-ttu-id="74249-151">當您這樣做時，Entity Framework 將不會認可或回復現有的交易，因此請小心使用，並確定這是您想要執行的動作。</span><span class="sxs-lookup"><span data-stu-id="74249-151">Entity Framework will neither commit nor rollback the existing transaction when you do this, so use with care and only if you’re sure this is what you want to do.</span></span>  

### <a name="errors-in-usetransaction"></a><span data-ttu-id="74249-152">UseTransaction 中的錯誤</span><span class="sxs-lookup"><span data-stu-id="74249-152">Errors in UseTransaction</span></span>

<span data-ttu-id="74249-153">如果您在下列情況中傳遞交易，則會看到 UseTransaction ( # A1 的例外狀況：</span><span class="sxs-lookup"><span data-stu-id="74249-153">You will see an exception from Database.UseTransaction() if you pass a transaction when:</span></span>  
- <span data-ttu-id="74249-154">Entity Framework 已經有現有交易</span><span class="sxs-lookup"><span data-stu-id="74249-154">Entity Framework already has an existing transaction</span></span>  
- <span data-ttu-id="74249-155">Entity Framework 已在 TransactionScope 內運作</span><span class="sxs-lookup"><span data-stu-id="74249-155">Entity Framework is already operating within a TransactionScope</span></span>  
- <span data-ttu-id="74249-156">傳遞的交易中的連線物件為 null。</span><span class="sxs-lookup"><span data-stu-id="74249-156">The connection object in the transaction passed is null.</span></span> <span data-ttu-id="74249-157">也就是說，交易不會與連接產生關聯–這通常是該交易已完成的正負號</span><span class="sxs-lookup"><span data-stu-id="74249-157">That is, the transaction is not associated with a connection – usually this is a sign that that transaction has already completed</span></span>  
- <span data-ttu-id="74249-158">傳遞的交易中的連線物件不符合 Entity Framework 的連接。</span><span class="sxs-lookup"><span data-stu-id="74249-158">The connection object in the transaction passed does not match the Entity Framework’s connection.</span></span>  

## <a name="using-transactions-with-other-features"></a><span data-ttu-id="74249-159">搭配其他功能使用交易</span><span class="sxs-lookup"><span data-stu-id="74249-159">Using transactions with other features</span></span>  

<span data-ttu-id="74249-160">本節將詳細說明上述交易與之互動的方式：</span><span class="sxs-lookup"><span data-stu-id="74249-160">This section details how the above transactions interact with:</span></span>  

- <span data-ttu-id="74249-161">連線恢復功能</span><span class="sxs-lookup"><span data-stu-id="74249-161">Connection resiliency</span></span>  
- <span data-ttu-id="74249-162">非同步方法</span><span class="sxs-lookup"><span data-stu-id="74249-162">Asynchronous methods</span></span>  
- <span data-ttu-id="74249-163">TransactionScope 交易</span><span class="sxs-lookup"><span data-stu-id="74249-163">TransactionScope transactions</span></span>  

### <a name="connection-resiliency"></a><span data-ttu-id="74249-164">連接恢復功能</span><span class="sxs-lookup"><span data-stu-id="74249-164">Connection Resiliency</span></span>  

<span data-ttu-id="74249-165">新的連接復原功能不適用於使用者起始的交易。</span><span class="sxs-lookup"><span data-stu-id="74249-165">The new Connection Resiliency feature does not work with user-initiated transactions.</span></span> <span data-ttu-id="74249-166">如需詳細資訊，請參閱 [重試執行策略](xref:ef6/fundamentals/connection-resiliency/retry-logic#user-initiated-transactions-are-not-supported)。</span><span class="sxs-lookup"><span data-stu-id="74249-166">For details, see [Retrying Execution Strategies](xref:ef6/fundamentals/connection-resiliency/retry-logic#user-initiated-transactions-are-not-supported).</span></span>  

### <a name="asynchronous-programming"></a><span data-ttu-id="74249-167">非同步程式設計</span><span class="sxs-lookup"><span data-stu-id="74249-167">Asynchronous Programming</span></span>  

<span data-ttu-id="74249-168">上述各節所述的方法，不需要進一步的選項或設定，就能使用 [非同步查詢和儲存方法](xref:ef6/fundamentals/async
)。</span><span class="sxs-lookup"><span data-stu-id="74249-168">The approach outlined in the previous sections needs no further options or settings to work with the [asynchronous query and save methods](xref:ef6/fundamentals/async
).</span></span> <span data-ttu-id="74249-169">但是請注意，根據您在非同步方法中所做的動作，這可能會導致長時間執行的交易，而這可能會導致鎖死或封鎖，而這對整體應用程式效能來說是不好的。</span><span class="sxs-lookup"><span data-stu-id="74249-169">But be aware that, depending on what you do within the asynchronous methods, this may result in long-running transactions – which can in turn cause deadlocks or blocking which is bad for the performance of the overall application.</span></span>  

### <a name="transactionscope-transactions"></a><span data-ttu-id="74249-170">TransactionScope 交易</span><span class="sxs-lookup"><span data-stu-id="74249-170">TransactionScope Transactions</span></span>  

<span data-ttu-id="74249-171">在 EF6 之前，建議的方法是使用 TransactionScope 物件來提供較大型的範圍交易：</span><span class="sxs-lookup"><span data-stu-id="74249-171">Prior to EF6 the recommended way of providing larger scope transactions was to use a TransactionScope object:</span></span>  

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

<span data-ttu-id="74249-172">SqlConnection 和 Entity Framework 都會使用環境 TransactionScope 交易，因此會一起認可。</span><span class="sxs-lookup"><span data-stu-id="74249-172">The SqlConnection and Entity Framework would both use the ambient TransactionScope transaction and hence be committed together.</span></span>  

<span data-ttu-id="74249-173">從 .NET 4.5.1 TransactionScope 開始更新，也可以透過使用 [TransactionScopeAsyncFlowOption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx) 列舉來處理非同步方法：</span><span class="sxs-lookup"><span data-stu-id="74249-173">Starting with .NET 4.5.1 TransactionScope has been updated to also work with asynchronous methods via the use of the [TransactionScopeAsyncFlowOption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx) enumeration:</span></span>  

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

<span data-ttu-id="74249-174">TransactionScope 方法仍有一些限制：</span><span class="sxs-lookup"><span data-stu-id="74249-174">There are still some limitations to the TransactionScope approach:</span></span>  

- <span data-ttu-id="74249-175">需要 .NET 4.5.1 或更新版本才能使用非同步方法。</span><span class="sxs-lookup"><span data-stu-id="74249-175">Requires .NET 4.5.1 or greater to work with asynchronous methods.</span></span>  
- <span data-ttu-id="74249-176">除非您確定只有一個連線 (雲端案例不支援) 的分散式交易，否則無法在雲端案例中使用它。</span><span class="sxs-lookup"><span data-stu-id="74249-176">It cannot be used in cloud scenarios unless you are sure you have one and only one connection (cloud scenarios do not support distributed transactions).</span></span>  
- <span data-ttu-id="74249-177">它無法與 UseTransaction 的上一節中的 ( # A1 方法合併。</span><span class="sxs-lookup"><span data-stu-id="74249-177">It cannot be combined with the Database.UseTransaction() approach of the previous sections.</span></span>  
- <span data-ttu-id="74249-178">如果您發出任何 DDL，而且尚未透過 MSDTC 服務啟用分散式交易，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="74249-178">It will throw exceptions if you issue any DDL and have not enabled distributed transactions through the MSDTC Service.</span></span>  

<span data-ttu-id="74249-179">TransactionScope 方法的優點：</span><span class="sxs-lookup"><span data-stu-id="74249-179">Advantages of the TransactionScope approach:</span></span>  

- <span data-ttu-id="74249-180">如果您對指定的資料庫進行一個以上的連接，或將連接與同一個交易中不同資料庫的連接合並到一個資料庫，則它會自動將本機交易升級為分散式交易 (注意：您必須將 MSDTC 服務設定為允許這項作業) 的分散式交易。</span><span class="sxs-lookup"><span data-stu-id="74249-180">It will automatically upgrade a local transaction to a distributed transaction if you make more than one connection to a given database or combine a connection to one database with a connection to a different database within the same transaction (note: you must have the MSDTC service configured to allow distributed transactions for this to work).</span></span>  
- <span data-ttu-id="74249-181">輕鬆撰寫程式碼。</span><span class="sxs-lookup"><span data-stu-id="74249-181">Ease of coding.</span></span> <span data-ttu-id="74249-182">如果您希望交易具有環境，並隱含地在背景中處理，而不是明確地在您控制之下，則 TransactionScope 方法可能會符合您的需要。</span><span class="sxs-lookup"><span data-stu-id="74249-182">If you prefer the transaction to be ambient and dealt with implicitly in the background rather than explicitly under you control then the TransactionScope approach may suit you better.</span></span>  

<span data-ttu-id="74249-183">總而言之，有了新的資料庫. BeginTransaction ( # A1 和 UseTransaction ( # A3 Api 以上，大部分的使用者已不再需要 TransactionScope 方法。</span><span class="sxs-lookup"><span data-stu-id="74249-183">In summary, with the new Database.BeginTransaction() and Database.UseTransaction() APIs above, the TransactionScope approach is no longer necessary for most users.</span></span> <span data-ttu-id="74249-184">如果您繼續使用 TransactionScope，請留意上述限制。</span><span class="sxs-lookup"><span data-stu-id="74249-184">If you do continue to use TransactionScope then be aware of the above limitations.</span></span> <span data-ttu-id="74249-185">建議您盡可能使用先前章節中所述的方法。</span><span class="sxs-lookup"><span data-stu-id="74249-185">We recommend using the approach outlined in the previous sections instead where possible.</span></span>  
