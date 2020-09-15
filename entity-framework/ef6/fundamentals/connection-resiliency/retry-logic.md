---
title: 連接恢復功能和重試邏輯-EF6
description: Entity Framework 6 中的連接恢復功能和重試邏輯
author: AndriySvyryd
ms.date: 11/20/2019
uid: ef6/fundamentals/connection-resiliency/retry-logic
ms.openlocfilehash: 3e71e973be5a23c86f873e9adf1bf00f4b6def58
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073184"
---
# <a name="connection-resiliency-and-retry-logic"></a><span data-ttu-id="32fc6-103">連接恢復功能和重試邏輯</span><span class="sxs-lookup"><span data-stu-id="32fc6-103">Connection resiliency and retry logic</span></span>
> [!NOTE]
> <span data-ttu-id="32fc6-104">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="32fc6-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="32fc6-105">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="32fc6-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="32fc6-106">連線到資料庫伺服器的應用程式一律會因為後端失敗和網路不穩定的連接中斷而受到影響。</span><span class="sxs-lookup"><span data-stu-id="32fc6-106">Applications connecting to a database server have always been vulnerable to connection breaks due to back-end failures and network instability.</span></span> <span data-ttu-id="32fc6-107">不過，在以 LAN 為基礎的環境中，針對專用的資料庫伺服器，這些錯誤很罕見，因此通常不需要額外的邏輯來處理這些失敗。</span><span class="sxs-lookup"><span data-stu-id="32fc6-107">However, in a LAN based environment working against dedicated database servers these errors are rare enough that extra logic to handle those failures is not often required.</span></span> <span data-ttu-id="32fc6-108">隨著雲端式資料庫伺服器的增加（例如 Windows Azure SQL Database）以及較不可靠的網路連線，現在更常發生連接中斷的情況。</span><span class="sxs-lookup"><span data-stu-id="32fc6-108">With the rise of cloud based database servers such as Windows Azure SQL Database and connections over less reliable networks it is now more common for connection breaks to occur.</span></span> <span data-ttu-id="32fc6-109">這可能是因為雲端資料庫用來確保服務公平（例如連線節流）或網路不穩定造成間歇性的錯誤和其他暫時性錯誤的防禦技巧。</span><span class="sxs-lookup"><span data-stu-id="32fc6-109">This could be due to defensive techniques that cloud databases use to ensure fairness of service, such as connection throttling, or to instability in the network causing intermittent timeouts and other transient errors.</span></span>  

<span data-ttu-id="32fc6-110">連接復原是指 EF 能夠自動重試因這些連接中斷而失敗的任何命令。</span><span class="sxs-lookup"><span data-stu-id="32fc6-110">Connection Resiliency refers to the ability for EF to automatically retry any commands that fail due to these connection breaks.</span></span>  

## <a name="execution-strategies"></a><span data-ttu-id="32fc6-111">執行策略</span><span class="sxs-lookup"><span data-stu-id="32fc6-111">Execution Strategies</span></span>  

<span data-ttu-id="32fc6-112">連接重試由 >idbexecutionstrategy 介面的執行負責處理。</span><span class="sxs-lookup"><span data-stu-id="32fc6-112">Connection retry is taken care of by an implementation of the IDbExecutionStrategy interface.</span></span> <span data-ttu-id="32fc6-113">>idbexecutionstrategy 的實施將負責接受作業，並在發生例外狀況時，判斷重試是否適當，以及是否要重試。</span><span class="sxs-lookup"><span data-stu-id="32fc6-113">Implementations of the IDbExecutionStrategy will be responsible for accepting an operation and, if an exception occurs, determining if a retry is appropriate and retrying if it is.</span></span> <span data-ttu-id="32fc6-114">EF 隨附四個執行策略：</span><span class="sxs-lookup"><span data-stu-id="32fc6-114">There are four execution strategies that ship with EF:</span></span>  

1. <span data-ttu-id="32fc6-115">**DefaultExecutionStrategy**：此執行策略不會重試任何作業，它是 sql server 以外資料庫的預設值。</span><span class="sxs-lookup"><span data-stu-id="32fc6-115">**DefaultExecutionStrategy**: this execution strategy does not retry any operations, it is the default for databases other than sql server.</span></span>  
2. <span data-ttu-id="32fc6-116">**>defaultsqlexecutionstrategy**：這是預設使用的內部執行策略。</span><span class="sxs-lookup"><span data-stu-id="32fc6-116">**DefaultSqlExecutionStrategy**: this is an internal execution strategy that is used by default.</span></span> <span data-ttu-id="32fc6-117">不過，這項策略並不會重試，但它會包裝任何可能暫時性的例外狀況，以通知使用者可能會想要啟用連接復原。</span><span class="sxs-lookup"><span data-stu-id="32fc6-117">This strategy does not retry at all, however, it will wrap any exceptions that could be transient to inform users that they might want to enable connection resiliency.</span></span>  
3. <span data-ttu-id="32fc6-118">**DbExecutionStrategy**：這個類別適合做為其他執行策略的基類，包括您自己的自訂專案。</span><span class="sxs-lookup"><span data-stu-id="32fc6-118">**DbExecutionStrategy**: this class is suitable as a base class for other execution strategies, including your own custom ones.</span></span> <span data-ttu-id="32fc6-119">它會執行指數重試原則，其中初始重試會以零延遲進行，而延遲會以指數方式增加，直到達到最大重試次數為止。</span><span class="sxs-lookup"><span data-stu-id="32fc6-119">It implements an exponential retry policy, where the initial retry happens with zero delay and the delay increases exponentially until the maximum retry count is hit.</span></span> <span data-ttu-id="32fc6-120">此類別具有抽象 ShouldRetryOn 方法，可在衍生的執行策略中執行，以控制應該重試的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="32fc6-120">This class has an abstract ShouldRetryOn method that can be implemented in derived execution strategies to control which exceptions should be retried.</span></span>  
4. <span data-ttu-id="32fc6-121">**>sqlazureexecutionstrategy**：此執行策略繼承自 DbExecutionStrategy，並且會在使用 Azure SQL Database 時已知可能暫時性的例外狀況上重試。</span><span class="sxs-lookup"><span data-stu-id="32fc6-121">**SqlAzureExecutionStrategy**: this execution strategy inherits from DbExecutionStrategy and will retry on exceptions that are known to be possibly transient when working with Azure SQL Database.</span></span>

> [!NOTE]
> <span data-ttu-id="32fc6-122">執行策略2和4包含在 EF 隨附的 Sql Server 提供者中，該提供者位於 EntityFramework 中，而且是設計來搭配 SQL Server 使用。</span><span class="sxs-lookup"><span data-stu-id="32fc6-122">Execution strategies 2 and 4 are included in the Sql Server provider that ships with EF, which is in the EntityFramework.SqlServer assembly and are designed to work with SQL Server.</span></span>  

## <a name="enabling-an-execution-strategy"></a><span data-ttu-id="32fc6-123">啟用執行策略</span><span class="sxs-lookup"><span data-stu-id="32fc6-123">Enabling an Execution Strategy</span></span>  

<span data-ttu-id="32fc6-124">要告訴 EF 使用執行策略，最簡單的方式就是使用 [>dbconfiguration](xref:ef6/fundamentals/configuring/code-based) 類別的 SetExecutionStrategy 方法：</span><span class="sxs-lookup"><span data-stu-id="32fc6-124">The easiest way to tell EF to use an execution strategy is with the SetExecutionStrategy method of the [DbConfiguration](xref:ef6/fundamentals/configuring/code-based) class:</span></span>  

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
    }
}
```  

<span data-ttu-id="32fc6-125">這段程式碼會告知 EF 在連接到 SQL Server 時使用 >sqlazureexecutionstrategy。</span><span class="sxs-lookup"><span data-stu-id="32fc6-125">This code tells EF to use the SqlAzureExecutionStrategy when connecting to SQL Server.</span></span>  

## <a name="configuring-the-execution-strategy"></a><span data-ttu-id="32fc6-126">設定執行策略</span><span class="sxs-lookup"><span data-stu-id="32fc6-126">Configuring the Execution Strategy</span></span>  

<span data-ttu-id="32fc6-127">>sqlazureexecutionstrategy 的函式可以接受兩個參數： MaxRetryCount 和 MaxDelay。</span><span class="sxs-lookup"><span data-stu-id="32fc6-127">The constructor of SqlAzureExecutionStrategy can accept two parameters, MaxRetryCount and MaxDelay.</span></span> <span data-ttu-id="32fc6-128">MaxRetry count 是策略將重試的最大次數。</span><span class="sxs-lookup"><span data-stu-id="32fc6-128">MaxRetry count is the maximum number of times that the strategy will retry.</span></span> <span data-ttu-id="32fc6-129">MaxDelay 是一個 TimeSpan，代表執行策略將使用的重試之間的延遲上限。</span><span class="sxs-lookup"><span data-stu-id="32fc6-129">The MaxDelay is a TimeSpan representing the maximum delay between retries that the execution strategy will use.</span></span>  

<span data-ttu-id="32fc6-130">若要將重試次數上限設為1，並將最大延遲設定為30秒，請執行下列動作：</span><span class="sxs-lookup"><span data-stu-id="32fc6-130">To set the maximum number of retries to 1 and the maximum delay to 30 seconds you would execute the following:</span></span>  

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetExecutionStrategy(
            "System.Data.SqlClient",
            () => new SqlAzureExecutionStrategy(1, TimeSpan.FromSeconds(30)));
    }
}
```  

<span data-ttu-id="32fc6-131">>sqlazureexecutionstrategy 會在發生暫時性失敗時立即重試，但會在每次重試之間延遲一段時間，直到超過最大重試限制，或總時間達到延遲上限為止。</span><span class="sxs-lookup"><span data-stu-id="32fc6-131">The SqlAzureExecutionStrategy will retry instantly the first time a transient failure occurs, but will delay longer between each retry until either the max retry limit is exceeded or the total time hits the max delay.</span></span>  

<span data-ttu-id="32fc6-132">執行策略只會重試有限數目的例外狀況，這些例外狀況通常是暫時性的，您仍然需要處理其他錯誤，並在錯誤不是暫時性或需要太久才能解決的情況下攔截 RetryLimitExceeded 例外狀況。</span><span class="sxs-lookup"><span data-stu-id="32fc6-132">The execution strategies will only retry a limited number of exceptions that are usually transient, you will still need to handle other errors as well as catching the RetryLimitExceeded exception for the case where an error is not transient or takes too long to resolve itself.</span></span>  

<span data-ttu-id="32fc6-133">使用重試執行策略時有一些已知的限制：</span><span class="sxs-lookup"><span data-stu-id="32fc6-133">There are some known of limitations when using a retrying execution strategy:</span></span>  

## <a name="streaming-queries-are-not-supported"></a><span data-ttu-id="32fc6-134">不支援串流查詢</span><span class="sxs-lookup"><span data-stu-id="32fc6-134">Streaming queries are not supported</span></span>  

<span data-ttu-id="32fc6-135">根據預設，EF6 和更新版本會緩衝處理查詢結果，而不是串流處理。</span><span class="sxs-lookup"><span data-stu-id="32fc6-135">By default, EF6 and later version will buffer query results rather than streaming them.</span></span> <span data-ttu-id="32fc6-136">如果您想要以資料流程處理結果，您可以使用 AsStreaming 方法，將 LINQ to Entities 查詢變更為串流處理。</span><span class="sxs-lookup"><span data-stu-id="32fc6-136">If you want to have results streamed you can use the AsStreaming method to change a LINQ to Entities query to streaming.</span></span>  

``` csharp
using (var db = new BloggingContext())
{
    var query = (from b in db.Blogs
                orderby b.Url
                select b).AsStreaming();
    }
}
```  

<span data-ttu-id="32fc6-137">當註冊重試執行策略時，不支援串流處理。</span><span class="sxs-lookup"><span data-stu-id="32fc6-137">Streaming is not supported when a retrying execution strategy is registered.</span></span> <span data-ttu-id="32fc6-138">這項限制存在的原因是因為連接可能會在傳回的結果中捨棄。</span><span class="sxs-lookup"><span data-stu-id="32fc6-138">This limitation exists because the connection could drop part way through the results being returned.</span></span> <span data-ttu-id="32fc6-139">發生這種情況時，EF 需要重新執行整個查詢，但無法可靠地知道哪些結果已經傳回 (資料在傳送初始查詢之後可能已經變更、結果可能會以不同的順序傳回、結果可能沒有唯一的識別碼等 ) 。</span><span class="sxs-lookup"><span data-stu-id="32fc6-139">When this occurs, EF needs to re-run the entire query but has no reliable way of knowing which results have already been returned (data may have changed since the initial query was sent, results may come back in a different order, results may not have a unique identifier, etc.).</span></span>  

## <a name="user-initiated-transactions-are-not-supported"></a><span data-ttu-id="32fc6-140">不支援使用者起始的交易</span><span class="sxs-lookup"><span data-stu-id="32fc6-140">User initiated transactions are not supported</span></span>  

<span data-ttu-id="32fc6-141">當您已設定導致重試的執行策略時，使用交易有一些限制。</span><span class="sxs-lookup"><span data-stu-id="32fc6-141">When you have configured an execution strategy that results in retries, there are some limitations around the use of transactions.</span></span>  

<span data-ttu-id="32fc6-142">根據預設，EF 會在交易內執行任何資料庫更新。</span><span class="sxs-lookup"><span data-stu-id="32fc6-142">By default, EF will perform any database updates within a transaction.</span></span> <span data-ttu-id="32fc6-143">您不需要執行任何動作來啟用此功能，而且 EF 一律會自動執行此動作。</span><span class="sxs-lookup"><span data-stu-id="32fc6-143">You don’t need to do anything to enable this, EF always does this automatically.</span></span>  

<span data-ttu-id="32fc6-144">例如，在下列程式碼中，SaveChanges 會自動在交易內執行。</span><span class="sxs-lookup"><span data-stu-id="32fc6-144">For example, in the following code SaveChanges is automatically performed within a transaction.</span></span> <span data-ttu-id="32fc6-145">如果 SaveChanges 在插入其中一個新網站之後失敗，則會回復交易，而不會對資料庫套用任何變更。</span><span class="sxs-lookup"><span data-stu-id="32fc6-145">If SaveChanges were to fail after inserting one of the new Site’s then the transaction would be rolled back and no changes applied to the database.</span></span> <span data-ttu-id="32fc6-146">內容也會留在狀態，讓 SaveChanges 再次呼叫，以重試套用變更。</span><span class="sxs-lookup"><span data-stu-id="32fc6-146">The context is also left in a state that allows SaveChanges to be called again to retry applying the changes.</span></span>  

``` csharp
using (var db = new BloggingContext())
{
    db.Blogs.Add(new Site { Url = "http://msdn.com/data/ef" });
    db.Blogs.Add(new Site { Url = "http://blogs.msdn.com/adonet" });
    db.SaveChanges();
}
```  

<span data-ttu-id="32fc6-147">若未使用重試執行策略，您可以在單一交易中包裝多個作業。</span><span class="sxs-lookup"><span data-stu-id="32fc6-147">When not using a retrying execution strategy you can wrap multiple operations in a single transaction.</span></span> <span data-ttu-id="32fc6-148">例如，下列程式碼會將兩個 SaveChanges 呼叫包裝在單一交易中。</span><span class="sxs-lookup"><span data-stu-id="32fc6-148">For example, the following code wraps two SaveChanges calls in a single transaction.</span></span> <span data-ttu-id="32fc6-149">如果任一作業的任何部分失敗，則不會套用任何變更。</span><span class="sxs-lookup"><span data-stu-id="32fc6-149">If any part of either operation fails then none of the changes are applied.</span></span>  

``` csharp
using (var db = new BloggingContext())
{
    using (var trn = db.Database.BeginTransaction())
    {
        db.Blogs.Add(new Site { Url = "http://msdn.com/data/ef" });
        db.Blogs.Add(new Site { Url = "http://blogs.msdn.com/adonet" });
        db.SaveChanges();

        db.Blogs.Add(new Site { Url = "http://twitter.com/efmagicunicorns" });
        db.SaveChanges();

        trn.Commit();
    }
}
```  

<span data-ttu-id="32fc6-150">使用重試執行策略時，不支援這種做法，因為 EF 不知道任何先前的作業，以及如何重試它們。</span><span class="sxs-lookup"><span data-stu-id="32fc6-150">This is not supported when using a retrying execution strategy because EF isn’t aware of any previous operations and how to retry them.</span></span> <span data-ttu-id="32fc6-151">例如，如果第二個 SaveChanges 失敗，則 EF 不再具有重試第一個 SaveChanges 呼叫所需的資訊。</span><span class="sxs-lookup"><span data-stu-id="32fc6-151">For example, if the second SaveChanges failed then EF no longer has the required information to retry the first SaveChanges call.</span></span>  

### <a name="solution-manually-call-execution-strategy"></a><span data-ttu-id="32fc6-152">解決方案：手動呼叫執行策略</span><span class="sxs-lookup"><span data-stu-id="32fc6-152">Solution: Manually Call Execution Strategy</span></span>  

<span data-ttu-id="32fc6-153">解決方法是手動使用執行策略，並為它提供要執行的整組邏輯，以便在其中一個作業失敗時重試所有專案。</span><span class="sxs-lookup"><span data-stu-id="32fc6-153">The solution is to manually use the execution strategy and give it the entire set of logic to be run, so that it can retry everything if one of the operations fails.</span></span> <span data-ttu-id="32fc6-154">當衍生自 DbExecutionStrategy 的執行策略執行時，它會暫止 SaveChanges 中使用的隱含執行策略。</span><span class="sxs-lookup"><span data-stu-id="32fc6-154">When an execution strategy derived from DbExecutionStrategy is running it will suspend the implicit execution strategy used in SaveChanges.</span></span>  

<span data-ttu-id="32fc6-155">請注意，任何內容都應該在要重試的程式碼區塊內進行。</span><span class="sxs-lookup"><span data-stu-id="32fc6-155">Note that any contexts should be constructed within the code block to be retried.</span></span> <span data-ttu-id="32fc6-156">這可確保每次重試都會以乾淨狀態開始。</span><span class="sxs-lookup"><span data-stu-id="32fc6-156">This ensures that we are starting with a clean state for each retry.</span></span>  

``` csharp
var executionStrategy = new SqlAzureExecutionStrategy();

executionStrategy.Execute(
    () =>
    {
        using (var db = new BloggingContext())
        {
            using (var trn = db.Database.BeginTransaction())
            {
                db.Blogs.Add(new Blog { Url = "http://msdn.com/data/ef" });
                db.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/adonet" });
                db.SaveChanges();

                db.Blogs.Add(new Blog { Url = "http://twitter.com/efmagicunicorns" });
                db.SaveChanges();

                trn.Commit();
            }
        }
    });
```  
