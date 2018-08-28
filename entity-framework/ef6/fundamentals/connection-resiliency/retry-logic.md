---
title: 連接恢復功能，然後重試邏輯-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 47d68ac1-927e-4842-ab8c-ed8c8698dff2
ms.openlocfilehash: 47181292873009c7bce2047787503258ffa35d9d
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997481"
---
# <a name="connection-resiliency-and-retry-logic"></a><span data-ttu-id="b87fa-102">連接恢復功能，然後重試邏輯</span><span class="sxs-lookup"><span data-stu-id="b87fa-102">Connection resiliency and retry logic</span></span>
> [!NOTE]
> <span data-ttu-id="b87fa-103">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="b87fa-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="b87fa-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="b87fa-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="b87fa-105">連接至資料庫伺服器的應用程式一直容易因為後端失敗和網路不穩定的連線中斷。</span><span class="sxs-lookup"><span data-stu-id="b87fa-105">Applications connecting to a database server have always been vulnerable to connection breaks due to back-end failures and network instability.</span></span> <span data-ttu-id="b87fa-106">不過，在基礎的 LAN 環境中針對專用的資料庫伺服器使用這些錯誤很少，額外的邏輯來處理這些失敗通常並不必要的。</span><span class="sxs-lookup"><span data-stu-id="b87fa-106">However, in a LAN based environment working against dedicated database servers these errors are rare enough that extra logic to handle those failures is not often required.</span></span> <span data-ttu-id="b87fa-107">雲端的興起是以基礎資料庫伺服器，例如 Windows Azure SQL Database 和連線現在是較常見的連線中斷，進行較不可靠的網路。</span><span class="sxs-lookup"><span data-stu-id="b87fa-107">With the rise of cloud based database servers such as Windows Azure SQL Database and connections over less reliable networks it is now more common for connection breaks to occur.</span></span> <span data-ttu-id="b87fa-108">這可能是服務的因為防禦性技術，雲端資料庫使用，以確保公平性，例如連線節流，或造成間歇性的逾時和其他暫時性錯誤的網路不穩定。</span><span class="sxs-lookup"><span data-stu-id="b87fa-108">This could be due to defensive techniques that cloud databases use to ensure fairness of service, such as connection throttling, or to instability in the network causing intermittent timeouts and other transient errors.</span></span>  

<span data-ttu-id="b87fa-109">連接恢復功能是指自動重試失敗，因為這些連線中斷的任何命令的 ef 的能力。</span><span class="sxs-lookup"><span data-stu-id="b87fa-109">Connection Resiliency refers to the ability for EF to automatically retry any commands that fail due to these connection breaks.</span></span>  

## <a name="execution-strategies"></a><span data-ttu-id="b87fa-110">執行策略</span><span class="sxs-lookup"><span data-stu-id="b87fa-110">Execution Strategies</span></span>  

<span data-ttu-id="b87fa-111">連接重試是由負責 IDbExecutionStrategy 介面的實作。</span><span class="sxs-lookup"><span data-stu-id="b87fa-111">Connection retry is taken care of by an implementation of the IDbExecutionStrategy interface.</span></span> <span data-ttu-id="b87fa-112">IDbExecutionStrategy 的實作會負責接受作業，並發生例外狀況時，判斷是否適合重試它是否正在重試。</span><span class="sxs-lookup"><span data-stu-id="b87fa-112">Implementations of the IDbExecutionStrategy will be responsible for accepting an operation and, if an exception occurs, determining if a retry is appropriate and retrying if it is.</span></span> <span data-ttu-id="b87fa-113">有四個隨附於 EF 的執行策略：</span><span class="sxs-lookup"><span data-stu-id="b87fa-113">There are four execution strategies that ship with EF:</span></span>  

1. <span data-ttu-id="b87fa-114">**DefaultExecutionStrategy**： 此執行策略不會重試任何作業，它是 sql server 以外的資料庫的預設值。</span><span class="sxs-lookup"><span data-stu-id="b87fa-114">**DefaultExecutionStrategy**: this execution strategy does not retry any operations, it is the default for databases other than sql server.</span></span>  
2. <span data-ttu-id="b87fa-115">**DefaultSqlExecutionStrategy**： 這是預設會使用內部執行策略。</span><span class="sxs-lookup"><span data-stu-id="b87fa-115">**DefaultSqlExecutionStrategy**: this is an internal execution strategy that is used by default.</span></span> <span data-ttu-id="b87fa-116">此策略不會重試根本，不過，它會換行可能是暫時性，通知使用者他們可能會想要啟用連線恢復功能的任何例外狀況。</span><span class="sxs-lookup"><span data-stu-id="b87fa-116">This strategy does not retry at all, however, it will wrap any exceptions that could be transient to inform users that they might want to enable connection resiliency.</span></span>  
3. <span data-ttu-id="b87fa-117">**DbExecutionStrategy**： 這個類別是適合的基底類別做為其他的執行策略，包括您自己自訂的。</span><span class="sxs-lookup"><span data-stu-id="b87fa-117">**DbExecutionStrategy**: this class is suitable as a base class for other execution strategies, including your own custom ones.</span></span> <span data-ttu-id="b87fa-118">它會實作指數重試原則，其中初始的重試發生零延遲和延遲會增加以指數方式叫用的最大重試計數為止。</span><span class="sxs-lookup"><span data-stu-id="b87fa-118">It implements an exponential retry policy, where the initial retry happens with zero delay and the delay increases exponentially until the maximum retry count is hit.</span></span> <span data-ttu-id="b87fa-119">這個類別具有一種抽象的 ShouldRetryOn 方法可以實作在衍生的執行策略，以控制應該重試的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="b87fa-119">This class has an abstract ShouldRetryOn method that can be implemented in derived execution strategies to control which exceptions should be retried.</span></span>  
4. <span data-ttu-id="b87fa-120">**SqlAzureExecutionStrategy**： 繼承自 DbExecutionStrategy 此執行策略，且將重新嘗試已知是可能是暫時性的使用 Azure SQL Database 時的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="b87fa-120">**SqlAzureExecutionStrategy**: this execution strategy inherits from DbExecutionStrategy and will retry on exceptions that are known to be possibly transient when working with Azure SQL Database.</span></span>

> [!NOTE]
> <span data-ttu-id="b87fa-121">2 和 4 的執行策略會包含在 EF，EntityFramework.SqlServer 組件中所隨附的 Sql Server 提供者，而且設計用於搭配 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="b87fa-121">Execution strategies 2 and 4 are included in the Sql Server provider that ships with EF, which is in the EntityFramework.SqlServer assembly and are designed to work with SQL Server.</span></span>  

## <a name="enabling-an-execution-strategy"></a><span data-ttu-id="b87fa-122">啟用執行策略</span><span class="sxs-lookup"><span data-stu-id="b87fa-122">Enabling an Execution Strategy</span></span>  

<span data-ttu-id="b87fa-123">告知 EF 來使用執行策略的最簡單方式是使用 SetExecutionStrategy 方法[DbConfiguration](~/ef6/fundamentals/configuring/code-based.md)類別：</span><span class="sxs-lookup"><span data-stu-id="b87fa-123">The easiest way to tell EF to use an execution strategy is with the SetExecutionStrategy method of the [DbConfiguration](~/ef6/fundamentals/configuring/code-based.md) class:</span></span>  

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
    }
}
```  

<span data-ttu-id="b87fa-124">此程式碼會告知 EF 來連接到 SQL Server 時，使用 SqlAzureExecutionStrategy。</span><span class="sxs-lookup"><span data-stu-id="b87fa-124">This code tells EF to use the SqlAzureExecutionStrategy when connecting to SQL Server.</span></span>  

## <a name="configuring-the-execution-strategy"></a><span data-ttu-id="b87fa-125">設定執行策略</span><span class="sxs-lookup"><span data-stu-id="b87fa-125">Configuring the Execution Strategy</span></span>  

<span data-ttu-id="b87fa-126">SqlAzureExecutionStrategy 的建構函式可以接受兩個參數，MaxRetryCount 和 MaxDelay。</span><span class="sxs-lookup"><span data-stu-id="b87fa-126">The constructor of SqlAzureExecutionStrategy can accept two parameters, MaxRetryCount and MaxDelay.</span></span> <span data-ttu-id="b87fa-127">MaxRetry 計數已重試策略的最大次數。</span><span class="sxs-lookup"><span data-stu-id="b87fa-127">MaxRetry count is the maximum number of times that the strategy will retry.</span></span> <span data-ttu-id="b87fa-128">MaxDelay 是 TimeSpan，代表將會使用執行策略的重試之間的延遲上限。</span><span class="sxs-lookup"><span data-stu-id="b87fa-128">The MaxDelay is a TimeSpan representing the maximum delay between retries that the execution strategy will use.</span></span>  

<span data-ttu-id="b87fa-129">若要設為 1 和最大延遲為 30 秒的重試次數上限，您需要下列 execue:</span><span class="sxs-lookup"><span data-stu-id="b87fa-129">To set the maximum number of retries to 1 and the maximum delay to 30 seconds you would execue the following:</span></span>  

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

<span data-ttu-id="b87fa-130">SqlAzureExecutionStrategy 會重試，立即超出暫時性失敗的情況，但將會再延遲之間每次重試之前的最大重試限制在第一次，或總時間達到最大延遲。</span><span class="sxs-lookup"><span data-stu-id="b87fa-130">The SqlAzureExecutionStrategy will retry instantly the first time a transient failure occurs, but will delay longer between each retry until either the max retry limit is exceeded or the total time hits the max delay.</span></span>  

<span data-ttu-id="b87fa-131">執行策略只會重試的例外狀況，通常是 tansient 數量有限，仍必須處理其他錯誤，以及攔截 RetryLimitExceeded 例外狀況，若要解決錯誤並非暫時性或花太多時間的位置案例它本身。</span><span class="sxs-lookup"><span data-stu-id="b87fa-131">The execution strategies will only retry a limited number of exceptions that are usually tansient, you will still need to handle other errors as well as catching the RetryLimitExceeded exception for the case where an error is not transient or takes too long to resolve itself.</span></span>  

## <a name="limitations"></a><span data-ttu-id="b87fa-132">限制</span><span class="sxs-lookup"><span data-stu-id="b87fa-132">Limitations</span></span>  

<span data-ttu-id="b87fa-133">使用重試的執行策略時，有一些已知的限制：</span><span class="sxs-lookup"><span data-stu-id="b87fa-133">There are some known of limitations when using a retrying execution strategy:</span></span>  

### <a name="streaming-queries-are-not-supported"></a><span data-ttu-id="b87fa-134">不支援資料流的查詢</span><span class="sxs-lookup"><span data-stu-id="b87fa-134">Streaming queries are not supported</span></span>  

<span data-ttu-id="b87fa-135">根據預設，EF6 和更新版本將查詢結果，而不是這些資料流緩衝區。</span><span class="sxs-lookup"><span data-stu-id="b87fa-135">By default, EF6 and later version will buffer query results rather than streaming them.</span></span> <span data-ttu-id="b87fa-136">如果您想要有結果串流處理您可以使用 AsStreaming 方法來變更 LINQ to Entities 查詢，為資料流。</span><span class="sxs-lookup"><span data-stu-id="b87fa-136">If you want to have results streamed you can use the AsStreaming method to change a LINQ to Entities query to streaming.</span></span>  

``` csharp
using (var db = new BloggingContext())
{
    var query = (from b in db.Blogs
                orderby b.Url
                select b).AsStreaming();
    }
}
```  

<span data-ttu-id="b87fa-137">不支援資料流，註冊時的重試執行策略。</span><span class="sxs-lookup"><span data-stu-id="b87fa-137">Streaming is not supported when a retrying execution strategy is registered.</span></span> <span data-ttu-id="b87fa-138">因為執行所傳回的結果的 「 連線無法卸除部分，就會有這項限制。</span><span class="sxs-lookup"><span data-stu-id="b87fa-138">This limitation exists because the connection could drop part way through the results being returned.</span></span> <span data-ttu-id="b87fa-139">當發生這種情況時，EF 必須重新執行整個查詢，但有沒有可靠的方式就知道已傳回的結果 （資料可能已變更，因為傳送初始查詢，結果可能的傳回順序不同，結果可能不需要的唯一識別碼等等。)。</span><span class="sxs-lookup"><span data-stu-id="b87fa-139">When this occurs, EF needs to re-run the entire query but has no reliable way of knowing which results have already been returned (data may have changed since the initial query was sent, results may come back in a different order, results may not have a unique identifier, etc.).</span></span>  

### <a name="user-initiated-transactions-not-supported"></a><span data-ttu-id="b87fa-140">使用者起始的交易不支援</span><span class="sxs-lookup"><span data-stu-id="b87fa-140">User initiated transactions not supported</span></span>  

<span data-ttu-id="b87fa-141">當您已設定導致重試執行策略時，有一些限制，有關使用交易。</span><span class="sxs-lookup"><span data-stu-id="b87fa-141">When you have configured an execution strategy that results in retries, there are some limitations around the use of transactions.</span></span>  

#### <a name="whats-supported-efs-default-transaction-behavior"></a><span data-ttu-id="b87fa-142">支援的項目： EF 的預設交易行為</span><span class="sxs-lookup"><span data-stu-id="b87fa-142">What's Supported: EF's default transaction behavior</span></span>  

<span data-ttu-id="b87fa-143">根據預設，EF 會執行交易內的任何資料庫更新。</span><span class="sxs-lookup"><span data-stu-id="b87fa-143">By default, EF will perform any database updates within a transaction.</span></span> <span data-ttu-id="b87fa-144">您不需要採取任何動作來啟用此功能，EF 一律自動執行這個工作。</span><span class="sxs-lookup"><span data-stu-id="b87fa-144">You don’t need to do anything to enable this, EF always does this automatically.</span></span>  

<span data-ttu-id="b87fa-145">例如，下列程式碼 SaveChanges 是自動在交易中執行。</span><span class="sxs-lookup"><span data-stu-id="b87fa-145">For example, in the following code SaveChanges is automatically performed within a transaction.</span></span> <span data-ttu-id="b87fa-146">如果 SaveChanges 後插入的新站台的其中一個，則會復原交易而且不需要變更套用至資料庫失敗。</span><span class="sxs-lookup"><span data-stu-id="b87fa-146">If SaveChanges were to fail after inserting one of the new Site’s then the transaction would be rolled back and no changes applied to the database.</span></span> <span data-ttu-id="b87fa-147">內容也是處於允許套用所做的變更重試一次呼叫 SaveChanges 的狀態。</span><span class="sxs-lookup"><span data-stu-id="b87fa-147">The context is also left in a state that allows SaveChanges to be called again to retry applying the changes.</span></span>  

``` csharp
using (var db = new BloggingContext())
{
    db.Blogs.Add(new Site { Url = "http://msdn.com/data/ef" });
    db.Blogs.Add(new Site { Url = "http://blogs.msdn.com/adonet" });
    db.SaveChanges();
}
```  

#### <a name="whats-not-supported-user-initiated-transactions"></a><span data-ttu-id="b87fa-148">不支援的功能： 使用者啟動的交易</span><span class="sxs-lookup"><span data-stu-id="b87fa-148">What’s not supported: User initiated transactions</span></span>  

<span data-ttu-id="b87fa-149">在不使用重試執行策略，您可以將多個作業包裝在單一交易中。</span><span class="sxs-lookup"><span data-stu-id="b87fa-149">When not using a retrying execution strategy you can wrap multiple operations in a single transaction.</span></span> <span data-ttu-id="b87fa-150">比方說，下列程式碼會包裝在單一交易中兩個 SaveChanges 呼叫。</span><span class="sxs-lookup"><span data-stu-id="b87fa-150">For example, the following code wraps two SaveChanges calls in a single transaction.</span></span> <span data-ttu-id="b87fa-151">如果任一項操作的任何部分再失敗的變更都不會套用。</span><span class="sxs-lookup"><span data-stu-id="b87fa-151">If any part of either operation fails then none of the changes are applied.</span></span>  

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

<span data-ttu-id="b87fa-152">不支援時使用的重試執行策略，因為 EF 不知道的任何先前的作業，以及如何重試它們。</span><span class="sxs-lookup"><span data-stu-id="b87fa-152">This is not supported when using a retrying execution strategy because EF isn’t aware of any previous operations and how to retry them.</span></span> <span data-ttu-id="b87fa-153">例如，如果第二個 SaveChanges 失敗然後 EF 不會再有重試第一次的 SaveChanges 呼叫所需的資訊。</span><span class="sxs-lookup"><span data-stu-id="b87fa-153">For example, if the second SaveChanges failed then EF no longer has the required information to retry the first SaveChanges call.</span></span>  

#### <a name="possible-workarounds"></a><span data-ttu-id="b87fa-154">可能的因應措施</span><span class="sxs-lookup"><span data-stu-id="b87fa-154">Possible workarounds</span></span>  

##### <a name="suspend-execution-strategy"></a><span data-ttu-id="b87fa-155">暫停執行策略</span><span class="sxs-lookup"><span data-stu-id="b87fa-155">Suspend Execution Strategy</span></span>  

<span data-ttu-id="b87fa-156">一個可能的因應措施是暫止正在重試執行策略需要使用使用者的程式碼片段的起始交易。</span><span class="sxs-lookup"><span data-stu-id="b87fa-156">One possible workaround is to suspend the retrying execution strategy for the piece of code that needs to use a user initiated transaction.</span></span> <span data-ttu-id="b87fa-157">若要這樣做最簡單的方式是加入 SuspendExecutionStrategy 旗標，以您的程式碼基礎組態類別，並將變更執行策略 lambda，來設定旗標時，傳回的預設值 (非 retying) 執行策略。</span><span class="sxs-lookup"><span data-stu-id="b87fa-157">The easiest way to do this is to add a SuspendExecutionStrategy flag to your code based configuration class and change the execution strategy lambda to return the default (non-retying) execution strategy when the flag is set.</span></span>  

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
                return (bool?)CallContext.LogicalGetData("SuspendExecutionStrategy")  false;
            }
            set
            {
                CallContext.LogicalSetData("SuspendExecutionStrategy", value);
            }
        }
    }
}
```  

<span data-ttu-id="b87fa-158">請注意，我們使用 CallContext 儲存旗標值。</span><span class="sxs-lookup"><span data-stu-id="b87fa-158">Note that we are using CallContext to store the flag value.</span></span> <span data-ttu-id="b87fa-159">這會提供類似執行緒區域儲存區的功能，但會安全地使用非同步程式碼-包含非同步查詢並儲存使用 Entity Framework。</span><span class="sxs-lookup"><span data-stu-id="b87fa-159">This provides similar functionality to thread local storage but is safe to use with asynchronous code - including async query and save with Entity Framework.</span></span>  

<span data-ttu-id="b87fa-160">我們現在可以暫停執行策略，使用使用者起始的交易的程式碼區段。</span><span class="sxs-lookup"><span data-stu-id="b87fa-160">We can now suspend the execution strategy for the section of code that uses a user initiated transaction.</span></span>  

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

##### <a name="manually-call-execution-strategy"></a><span data-ttu-id="b87fa-161">以手動方式呼叫執行策略</span><span class="sxs-lookup"><span data-stu-id="b87fa-161">Manually Call Execution Strategy</span></span>  

<span data-ttu-id="b87fa-162">另一個選項是邏輯的手動使用執行策略，並提供完整來執行，以便它可以重試一次的所有項目如果其中一個作業失敗。</span><span class="sxs-lookup"><span data-stu-id="b87fa-162">Another option is to manually use the execution strategy and give it the entire set of logic to be run, so that it can retry everything if one of the operations fails.</span></span> <span data-ttu-id="b87fa-163">我們仍需要暫停執行策略-使用此技術-如上所示，如此可重試程式碼區塊內使用的任何內容不會嘗試重試一次。</span><span class="sxs-lookup"><span data-stu-id="b87fa-163">We still need to suspend the execution strategy - using the technique shown above - so that any contexts used inside the retryable code block do not attempt to retry.</span></span>  

<span data-ttu-id="b87fa-164">請注意，應重試的程式碼區塊內建構任何內容。</span><span class="sxs-lookup"><span data-stu-id="b87fa-164">Note that any contexts should be constructed within the code block to be retried.</span></span> <span data-ttu-id="b87fa-165">這可確保，我們已開始初始狀態，以便每次重試。</span><span class="sxs-lookup"><span data-stu-id="b87fa-165">This ensures that we are starting with a clean state for each retry.</span></span>  

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
