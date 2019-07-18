---
title: 連接復原和重試邏輯-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 47d68ac1-927e-4842-ab8c-ed8c8698dff2
ms.openlocfilehash: a01216c3399ca4a04943563435eacd0047337a5f
ms.sourcegitcommit: c9c3e00c2d445b784423469838adc071a946e7c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68306580"
---
# <a name="connection-resiliency-and-retry-logic"></a><span data-ttu-id="47c87-102">連接復原和重試邏輯</span><span class="sxs-lookup"><span data-stu-id="47c87-102">Connection resiliency and retry logic</span></span>
> [!NOTE]
> <span data-ttu-id="47c87-103">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="47c87-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="47c87-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="47c87-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="47c87-105">連接到資料庫伺服器的應用程式一律會因為後端失敗和網路不穩定而受到連線中斷的影響。</span><span class="sxs-lookup"><span data-stu-id="47c87-105">Applications connecting to a database server have always been vulnerable to connection breaks due to back-end failures and network instability.</span></span> <span data-ttu-id="47c87-106">不過, 在以 LAN 為基礎的環境中, 針對專用的資料庫伺服器運作時, 這些錯誤很罕見, 但通常不需要額外的邏輯來處理這些失敗。</span><span class="sxs-lookup"><span data-stu-id="47c87-106">However, in a LAN based environment working against dedicated database servers these errors are rare enough that extra logic to handle those failures is not often required.</span></span> <span data-ttu-id="47c87-107">隨著以雲端為基礎的資料庫伺服器 (例如 Windows Azure SQL Database 和連線較不可靠的網路) 的增加, 現在更常發生連接中斷的情況。</span><span class="sxs-lookup"><span data-stu-id="47c87-107">With the rise of cloud based database servers such as Windows Azure SQL Database and connections over less reliable networks it is now more common for connection breaks to occur.</span></span> <span data-ttu-id="47c87-108">這可能是因為雲端資料庫用來確保服務公平的防禦技術, 例如連線節流, 或網路中的不穩定造成間歇的超時和其他暫時性錯誤。</span><span class="sxs-lookup"><span data-stu-id="47c87-108">This could be due to defensive techniques that cloud databases use to ensure fairness of service, such as connection throttling, or to instability in the network causing intermittent timeouts and other transient errors.</span></span>  

<span data-ttu-id="47c87-109">「連接復原」是指 EF 自動重試因這些連接中斷而失敗的任何命令的能力。</span><span class="sxs-lookup"><span data-stu-id="47c87-109">Connection Resiliency refers to the ability for EF to automatically retry any commands that fail due to these connection breaks.</span></span>  

## <a name="execution-strategies"></a><span data-ttu-id="47c87-110">執行策略</span><span class="sxs-lookup"><span data-stu-id="47c87-110">Execution Strategies</span></span>  

<span data-ttu-id="47c87-111">IDbExecutionStrategy 介面的執行會負責連接重試。</span><span class="sxs-lookup"><span data-stu-id="47c87-111">Connection retry is taken care of by an implementation of the IDbExecutionStrategy interface.</span></span> <span data-ttu-id="47c87-112">IDbExecutionStrategy 的執行會負責接受作業, 如果發生例外狀況, 則判斷重試是否適當, 並在其為時重試。</span><span class="sxs-lookup"><span data-stu-id="47c87-112">Implementations of the IDbExecutionStrategy will be responsible for accepting an operation and, if an exception occurs, determining if a retry is appropriate and retrying if it is.</span></span> <span data-ttu-id="47c87-113">EF 隨附四個執行策略:</span><span class="sxs-lookup"><span data-stu-id="47c87-113">There are four execution strategies that ship with EF:</span></span>  

1. <span data-ttu-id="47c87-114">**DefaultExecutionStrategy**: 此執行策略不會重試任何作業, 而是 sql server 以外的資料庫的預設值。</span><span class="sxs-lookup"><span data-stu-id="47c87-114">**DefaultExecutionStrategy**: this execution strategy does not retry any operations, it is the default for databases other than sql server.</span></span>  
2. <span data-ttu-id="47c87-115">**DefaultSqlExecutionStrategy**: 這是預設使用的內部執行策略。</span><span class="sxs-lookup"><span data-stu-id="47c87-115">**DefaultSqlExecutionStrategy**: this is an internal execution strategy that is used by default.</span></span> <span data-ttu-id="47c87-116">這種策略根本不會重試, 不過, 它會包裝任何可能是暫時性的例外狀況, 以通知使用者他們可能想要啟用連接恢復功能。</span><span class="sxs-lookup"><span data-stu-id="47c87-116">This strategy does not retry at all, however, it will wrap any exceptions that could be transient to inform users that they might want to enable connection resiliency.</span></span>  
3. <span data-ttu-id="47c87-117">**DbExecutionStrategy**: 這個類別適合做為其他執行策略的基類, 包括您自己的自訂。</span><span class="sxs-lookup"><span data-stu-id="47c87-117">**DbExecutionStrategy**: this class is suitable as a base class for other execution strategies, including your own custom ones.</span></span> <span data-ttu-id="47c87-118">它會執行指數重試原則, 其中會以零延遲進行初始重試, 而延遲會以指數方式增加, 直到達到最大重試次數為止。</span><span class="sxs-lookup"><span data-stu-id="47c87-118">It implements an exponential retry policy, where the initial retry happens with zero delay and the delay increases exponentially until the maximum retry count is hit.</span></span> <span data-ttu-id="47c87-119">這個類別具有抽象的 ShouldRetryOn 方法, 可在衍生的執行策略中執行, 以控制應該重試的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="47c87-119">This class has an abstract ShouldRetryOn method that can be implemented in derived execution strategies to control which exceptions should be retried.</span></span>  
4. <span data-ttu-id="47c87-120">**SqlAzureExecutionStrategy**: 此執行策略會繼承自 DbExecutionStrategy, 並會在使用 Azure SQL Database 時, 在已知可能會暫時性的例外狀況上重試。</span><span class="sxs-lookup"><span data-stu-id="47c87-120">**SqlAzureExecutionStrategy**: this execution strategy inherits from DbExecutionStrategy and will retry on exceptions that are known to be possibly transient when working with Azure SQL Database.</span></span>

> [!NOTE]
> <span data-ttu-id="47c87-121">執行策略2和4包含在 EF 隨附的 Sql Server 提供者中, 這是在 EntityFramework 中, 而且是設計用來與 SQL Server 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="47c87-121">Execution strategies 2 and 4 are included in the Sql Server provider that ships with EF, which is in the EntityFramework.SqlServer assembly and are designed to work with SQL Server.</span></span>  

## <a name="enabling-an-execution-strategy"></a><span data-ttu-id="47c87-122">啟用執行策略</span><span class="sxs-lookup"><span data-stu-id="47c87-122">Enabling an Execution Strategy</span></span>  

<span data-ttu-id="47c87-123">告訴 EF 使用執行策略最簡單的方式, 就是使用[DbConfiguration](~/ef6/fundamentals/configuring/code-based.md)類別的 SetExecutionStrategy 方法:</span><span class="sxs-lookup"><span data-stu-id="47c87-123">The easiest way to tell EF to use an execution strategy is with the SetExecutionStrategy method of the [DbConfiguration](~/ef6/fundamentals/configuring/code-based.md) class:</span></span>  

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
    }
}
```  

<span data-ttu-id="47c87-124">此程式碼會指示 EF 在連接到 SQL Server 時使用 SqlAzureExecutionStrategy。</span><span class="sxs-lookup"><span data-stu-id="47c87-124">This code tells EF to use the SqlAzureExecutionStrategy when connecting to SQL Server.</span></span>  

## <a name="configuring-the-execution-strategy"></a><span data-ttu-id="47c87-125">設定執行策略</span><span class="sxs-lookup"><span data-stu-id="47c87-125">Configuring the Execution Strategy</span></span>  

<span data-ttu-id="47c87-126">SqlAzureExecutionStrategy 的構造函式可以接受兩個參數: MaxRetryCount 和 MaxDelay。</span><span class="sxs-lookup"><span data-stu-id="47c87-126">The constructor of SqlAzureExecutionStrategy can accept two parameters, MaxRetryCount and MaxDelay.</span></span> <span data-ttu-id="47c87-127">MaxRetry count 是此策略將重試的次數上限。</span><span class="sxs-lookup"><span data-stu-id="47c87-127">MaxRetry count is the maximum number of times that the strategy will retry.</span></span> <span data-ttu-id="47c87-128">MaxDelay 是一個 TimeSpan, 代表執行策略將使用的重試之間的最大延遲。</span><span class="sxs-lookup"><span data-stu-id="47c87-128">The MaxDelay is a TimeSpan representing the maximum delay between retries that the execution strategy will use.</span></span>  

<span data-ttu-id="47c87-129">若要將重試次數上限設為 1, 並將最大延遲設定為30秒, 您可以執行下列動作:</span><span class="sxs-lookup"><span data-stu-id="47c87-129">To set the maximum number of retries to 1 and the maximum delay to 30 seconds you would execute the following:</span></span>  

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

<span data-ttu-id="47c87-130">SqlAzureExecutionStrategy 將會在第一次發生暫時性失敗時立即重試, 但會延遲每次重試之間的時間, 直到超過最大重試次數或總延遲數上限為止。</span><span class="sxs-lookup"><span data-stu-id="47c87-130">The SqlAzureExecutionStrategy will retry instantly the first time a transient failure occurs, but will delay longer between each retry until either the max retry limit is exceeded or the total time hits the max delay.</span></span>  

<span data-ttu-id="47c87-131">執行策略只會重試有限數目的例外狀況, 通常是暫時性的, 您仍然需要處理其他錯誤, 並在錯誤不是暫時性或需要太久的情況下攔截例外狀況 (RetryLimitExceeded exception)直接.</span><span class="sxs-lookup"><span data-stu-id="47c87-131">The execution strategies will only retry a limited number of exceptions that are usually transient, you will still need to handle other errors as well as catching the RetryLimitExceeded exception for the case where an error is not transient or takes too long to resolve itself.</span></span>  

<span data-ttu-id="47c87-132">使用重試執行策略時, 有一些已知的限制:</span><span class="sxs-lookup"><span data-stu-id="47c87-132">There are some known of limitations when using a retrying execution strategy:</span></span>  

## <a name="streaming-queries-are-not-supported"></a><span data-ttu-id="47c87-133">不支援串流查詢</span><span class="sxs-lookup"><span data-stu-id="47c87-133">Streaming queries are not supported</span></span>  

<span data-ttu-id="47c87-134">根據預設, EF6 和更新版本會緩衝查詢結果, 而不是串流處理。</span><span class="sxs-lookup"><span data-stu-id="47c87-134">By default, EF6 and later version will buffer query results rather than streaming them.</span></span> <span data-ttu-id="47c87-135">如果您想要將結果串流處理, 您可以使用 AsStreaming 方法, 將 LINQ to Entities 查詢變更為資料流程。</span><span class="sxs-lookup"><span data-stu-id="47c87-135">If you want to have results streamed you can use the AsStreaming method to change a LINQ to Entities query to streaming.</span></span>  

``` csharp
using (var db = new BloggingContext())
{
    var query = (from b in db.Blogs
                orderby b.Url
                select b).AsStreaming();
    }
}
```  

<span data-ttu-id="47c87-136">註冊重試執行策略時, 不支援串流處理。</span><span class="sxs-lookup"><span data-stu-id="47c87-136">Streaming is not supported when a retrying execution strategy is registered.</span></span> <span data-ttu-id="47c87-137">這項限制存在的原因是, 連接可能會在傳回的結果中捨棄部分方式。</span><span class="sxs-lookup"><span data-stu-id="47c87-137">This limitation exists because the connection could drop part way through the results being returned.</span></span> <span data-ttu-id="47c87-138">發生這種情況時, EF 需要重新執行整個查詢, 但沒有可靠的方法可以得知已經傳回的結果 (資料可能會在傳送初始查詢後變更, 結果可能會以不同的順序傳回, 結果可能不會有唯一識別碼等等)。</span><span class="sxs-lookup"><span data-stu-id="47c87-138">When this occurs, EF needs to re-run the entire query but has no reliable way of knowing which results have already been returned (data may have changed since the initial query was sent, results may come back in a different order, results may not have a unique identifier, etc.).</span></span>  

## <a name="user-initiated-transactions-are-not-supported"></a><span data-ttu-id="47c87-139">不支援使用者起始的交易</span><span class="sxs-lookup"><span data-stu-id="47c87-139">User initiated transactions are not supported</span></span>  

<span data-ttu-id="47c87-140">當您設定了會導致重試的執行策略時, 使用交易時, 會有一些限制。</span><span class="sxs-lookup"><span data-stu-id="47c87-140">When you have configured an execution strategy that results in retries, there are some limitations around the use of transactions.</span></span>  

<span data-ttu-id="47c87-141">根據預設, EF 會在交易內執行任何資料庫更新。</span><span class="sxs-lookup"><span data-stu-id="47c87-141">By default, EF will perform any database updates within a transaction.</span></span> <span data-ttu-id="47c87-142">您不需要採取任何動作來啟用此功能, 因此 EF 一律會自動執行此作業。</span><span class="sxs-lookup"><span data-stu-id="47c87-142">You don’t need to do anything to enable this, EF always does this automatically.</span></span>  

<span data-ttu-id="47c87-143">例如, 在下列程式碼中, 會在交易內自動執行 SaveChanges。</span><span class="sxs-lookup"><span data-stu-id="47c87-143">For example, in the following code SaveChanges is automatically performed within a transaction.</span></span> <span data-ttu-id="47c87-144">如果 SaveChanges 在插入其中一個新網站之後失敗, 則會回復交易, 而且不會對資料庫套用任何變更。</span><span class="sxs-lookup"><span data-stu-id="47c87-144">If SaveChanges were to fail after inserting one of the new Site’s then the transaction would be rolled back and no changes applied to the database.</span></span> <span data-ttu-id="47c87-145">內容也會停留在允許再次呼叫 SaveChanges 以重試套用變更的狀態。</span><span class="sxs-lookup"><span data-stu-id="47c87-145">The context is also left in a state that allows SaveChanges to be called again to retry applying the changes.</span></span>  

``` csharp
using (var db = new BloggingContext())
{
    db.Blogs.Add(new Site { Url = "http://msdn.com/data/ef" });
    db.Blogs.Add(new Site { Url = "http://blogs.msdn.com/adonet" });
    db.SaveChanges();
}
```  

<span data-ttu-id="47c87-146">當不使用重試執行策略時, 您可以在單一交易中包裝多個作業。</span><span class="sxs-lookup"><span data-stu-id="47c87-146">When not using a retrying execution strategy you can wrap multiple operations in a single transaction.</span></span> <span data-ttu-id="47c87-147">例如, 下列程式碼會將兩個 SaveChanges 呼叫包裝在單一交易中。</span><span class="sxs-lookup"><span data-stu-id="47c87-147">For example, the following code wraps two SaveChanges calls in a single transaction.</span></span> <span data-ttu-id="47c87-148">如果任一作業的任何部分失敗, 則不會套用任何變更。</span><span class="sxs-lookup"><span data-stu-id="47c87-148">If any part of either operation fails then none of the changes are applied.</span></span>  

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

<span data-ttu-id="47c87-149">使用重試執行策略時, 不支援這種作法, 因為 EF 並不知道任何先前的作業, 以及如何重試它們。</span><span class="sxs-lookup"><span data-stu-id="47c87-149">This is not supported when using a retrying execution strategy because EF isn’t aware of any previous operations and how to retry them.</span></span> <span data-ttu-id="47c87-150">例如, 如果第二個 SaveChanges 失敗, 則 EF 不再具有重試第一個 SaveChanges 呼叫所需的資訊。</span><span class="sxs-lookup"><span data-stu-id="47c87-150">For example, if the second SaveChanges failed then EF no longer has the required information to retry the first SaveChanges call.</span></span>  

### <a name="workaround-suspend-execution-strategy"></a><span data-ttu-id="47c87-151">因應措施：暫停執行策略</span><span class="sxs-lookup"><span data-stu-id="47c87-151">Workaround: Suspend Execution Strategy</span></span>  

<span data-ttu-id="47c87-152">其中一個可行的因應措施是針對需要使用使用者起始之交易的程式碼段, 暫停重試執行策略。</span><span class="sxs-lookup"><span data-stu-id="47c87-152">One possible workaround is to suspend the retrying execution strategy for the piece of code that needs to use a user initiated transaction.</span></span> <span data-ttu-id="47c87-153">若要這麼做, 最簡單的方法是將 SuspendExecutionStrategy 旗標新增至您的程式碼型設定類別, 並變更執行策略 lambda, 以在設定旗標時傳回預設 (非 retying) 執行策略。</span><span class="sxs-lookup"><span data-stu-id="47c87-153">The easiest way to do this is to add a SuspendExecutionStrategy flag to your code based configuration class and change the execution strategy lambda to return the default (non-retying) execution strategy when the flag is set.</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.SqlServer;
using System.Runtime.Remoting.Messaging;

namespace Demo
{
    public class MyConfiguration : DbConfiguration
    {
        public MyConfiguration()
        {
            this.SetExecutionStrategy("System.Data.SqlClient", () => SuspendExecutionStrategy
              ? (IDbExecutionStrategy)new DefaultExecutionStrategy()
              : new SqlAzureExecutionStrategy());
        }

        public static bool SuspendExecutionStrategy
        {
            get
            {
                return (bool?)CallContext.LogicalGetData("SuspendExecutionStrategy") ?? false;
            }
            set
            {
                CallContext.LogicalSetData("SuspendExecutionStrategy", value);
            }
        }
    }
}
```  

<span data-ttu-id="47c87-154">請注意, 我們會使用 CallCoNtext 來儲存旗標值。</span><span class="sxs-lookup"><span data-stu-id="47c87-154">Note that we are using CallContext to store the flag value.</span></span> <span data-ttu-id="47c87-155">這可提供與執行緒本機儲存體類似的功能, 但可安全地與非同步程式碼搭配使用, 包括非同步查詢和使用 Entity Framework 儲存。</span><span class="sxs-lookup"><span data-stu-id="47c87-155">This provides similar functionality to thread local storage but is safe to use with asynchronous code - including async query and save with Entity Framework.</span></span>  

<span data-ttu-id="47c87-156">我們現在可以暫止使用使用者起始的交易之程式碼區段的執行策略。</span><span class="sxs-lookup"><span data-stu-id="47c87-156">We can now suspend the execution strategy for the section of code that uses a user initiated transaction.</span></span>  

``` csharp
using (var db = new BloggingContext())
{
    MyConfiguration.SuspendExecutionStrategy = true;

    using (var trn = db.Database.BeginTransaction())
    {
        db.Blogs.Add(new Blog { Url = "http://msdn.com/data/ef" });
        db.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/adonet" });
        db.SaveChanges();

        db.Blogs.Add(new Blog { Url = "http://twitter.com/efmagicunicorns" });
        db.SaveChanges();

        trn.Commit();
    }

    MyConfiguration.SuspendExecutionStrategy = false;
}
```  

### <a name="workaround-manually-call-execution-strategy"></a><span data-ttu-id="47c87-157">因應措施：手動呼叫執行策略</span><span class="sxs-lookup"><span data-stu-id="47c87-157">Workaround: Manually Call Execution Strategy</span></span>  

<span data-ttu-id="47c87-158">另一個選項是手動使用執行策略, 並為它提供要執行的整組邏輯, 讓它可以在其中一個作業失敗時重試所有動作。</span><span class="sxs-lookup"><span data-stu-id="47c87-158">Another option is to manually use the execution strategy and give it the entire set of logic to be run, so that it can retry everything if one of the operations fails.</span></span> <span data-ttu-id="47c87-159">我們仍然需要使用上述技術來暫停執行策略, 以便可重試的程式碼區塊內使用的任何內容都不會嘗試重試。</span><span class="sxs-lookup"><span data-stu-id="47c87-159">We still need to suspend the execution strategy - using the technique shown above - so that any contexts used inside the retryable code block do not attempt to retry.</span></span>  

<span data-ttu-id="47c87-160">請注意, 應該在要重試的程式碼區塊內建立任何內容。</span><span class="sxs-lookup"><span data-stu-id="47c87-160">Note that any contexts should be constructed within the code block to be retried.</span></span> <span data-ttu-id="47c87-161">這可確保每次重試都會以全新狀態開始。</span><span class="sxs-lookup"><span data-stu-id="47c87-161">This ensures that we are starting with a clean state for each retry.</span></span>  

``` csharp
var executionStrategy = new SqlAzureExecutionStrategy();

MyConfiguration.SuspendExecutionStrategy = true;

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

MyConfiguration.SuspendExecutionStrategy = false;
```  
