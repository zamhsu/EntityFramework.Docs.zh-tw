---
title: 連接恢復功能的 EF Core
author: rowanmiller
ms.author: divega
ms.date: 11/15/2016
ms.assetid: e079d4af-c455-4a14-8e15-a8471516d748
ms.technology: entity-framework-core
uid: core/miscellaneous/connection-resiliency
ms.openlocfilehash: dae646e39b4dbd96b34f47582f9b2aa531cf88a7
ms.sourcegitcommit: 902257be9c63c427dc793750a2b827d6feb8e38c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2018
ms.locfileid: "39614333"
---
# <a name="connection-resiliency"></a><span data-ttu-id="07b57-102">連線復原能力</span><span class="sxs-lookup"><span data-stu-id="07b57-102">Connection Resiliency</span></span>

<span data-ttu-id="07b57-103">連接恢復功能會自動重試失敗的資料庫命令。</span><span class="sxs-lookup"><span data-stu-id="07b57-103">Connection resiliency automatically retries failed database commands.</span></span> <span data-ttu-id="07b57-104">此功能可以搭配任何資料庫，藉由提供"執行策略 」，封裝的邏輯才能偵測到失敗，然後重試命令。</span><span class="sxs-lookup"><span data-stu-id="07b57-104">The feature can be used with any database by supplying an "execution strategy", which encapsulates the logic necessary to detect failures and retry commands.</span></span> <span data-ttu-id="07b57-105">EF Core 提供者可以提供專門針對其特定的資料庫失敗狀況和最佳的重試原則的執行策略。</span><span class="sxs-lookup"><span data-stu-id="07b57-105">EF Core providers can supply execution strategies tailored to their specific database failure conditions and optimal retry policies.</span></span>

<span data-ttu-id="07b57-106">例如，SQL Server 提供者會包含專為 SQL Server （包括 SQL Azure） 的執行策略。</span><span class="sxs-lookup"><span data-stu-id="07b57-106">As an example, the SQL Server provider includes an execution strategy that is specifically tailored to SQL Server (including SQL Azure).</span></span> <span data-ttu-id="07b57-107">它會知道可以重試例外狀況類型，並有合理的預設值的最大重試，重試等之間的延遲。</span><span class="sxs-lookup"><span data-stu-id="07b57-107">It is aware of the exception types that can be retried and has sensible defaults for maximum retries, delay between retries, etc.</span></span>

<span data-ttu-id="07b57-108">設定您內容的選項時，將指定的執行策略。</span><span class="sxs-lookup"><span data-stu-id="07b57-108">An execution strategy is specified when configuring the options for your context.</span></span> <span data-ttu-id="07b57-109">這是通常位於`OnConfiguring`方法在衍生的內容中，或在`Startup.cs`ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="07b57-109">This is typically in the `OnConfiguring` method of your derived context, or in `Startup.cs` for an ASP.NET Core application.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#OnConfiguring)]

## <a name="custom-execution-strategy"></a><span data-ttu-id="07b57-110">自訂的執行策略</span><span class="sxs-lookup"><span data-stu-id="07b57-110">Custom execution strategy</span></span>

<span data-ttu-id="07b57-111">沒有註冊您自己，如果您想要變更預設值的任何自訂的執行策略的機制。</span><span class="sxs-lookup"><span data-stu-id="07b57-111">There is a mechanism to register a custom execution strategy of your own if you wish to change any of the defaults.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseMyProvider(
            "<connection string>",
            options => options.ExecutionStrategy(...));
}
```

## <a name="execution-strategies-and-transactions"></a><span data-ttu-id="07b57-112">執行策略和交易</span><span class="sxs-lookup"><span data-stu-id="07b57-112">Execution strategies and transactions</span></span>

<span data-ttu-id="07b57-113">會自動重試失敗的執行策略必須要能夠在失敗時的重試區塊中播放每個作業。</span><span class="sxs-lookup"><span data-stu-id="07b57-113">An execution strategy that automatically retries on failures needs to be able to play back each operation in a retry block that fails.</span></span> <span data-ttu-id="07b57-114">重試啟用時，您透過 EF Core 執行每個作業都會變成其本身可重試作業。</span><span class="sxs-lookup"><span data-stu-id="07b57-114">When retries are enabled, each operation you perform via EF Core becomes its own retriable operation.</span></span> <span data-ttu-id="07b57-115">也就是說，每個查詢和每次呼叫`SaveChanges()`將重試做為一個單位發生暫時性失敗。</span><span class="sxs-lookup"><span data-stu-id="07b57-115">That is, each query and each call to `SaveChanges()` will be retried as a unit if a transient failure occurs.</span></span>

<span data-ttu-id="07b57-116">不過，如果您的程式碼會啟動交易，使用`BeginTransaction()`您正在定義您自己的群組作業，也必須被視為一個單位，而且在交易內的所有項目則需要播放應該會發生失敗。</span><span class="sxs-lookup"><span data-stu-id="07b57-116">However, if your code initiates a transaction using `BeginTransaction()` you are defining your own group of operations that need to be treated as a unit, and everything inside the transaction would need to be played back shall a failure occur.</span></span> <span data-ttu-id="07b57-117">如果您嘗試這樣做，使用執行策略時，您會收到類似下列的例外狀況：</span><span class="sxs-lookup"><span data-stu-id="07b57-117">You will receive an exception like the following if you attempt to do this when using an execution strategy:</span></span>

> <span data-ttu-id="07b57-118">InvalidOperationException： 設定的執行策略 'SqlServerRetryingExecutionStrategy' 不支援使用者起始的異動。</span><span class="sxs-lookup"><span data-stu-id="07b57-118">InvalidOperationException: The configured execution strategy 'SqlServerRetryingExecutionStrategy' does not support user initiated transactions.</span></span> <span data-ttu-id="07b57-119">使用 'DbContext.Database.CreateExecutionStrategy()' 所傳回的執行策略，將異動中的所有作業當做一個可重試的單位來執行。</span><span class="sxs-lookup"><span data-stu-id="07b57-119">Use the execution strategy returned by 'DbContext.Database.CreateExecutionStrategy()' to execute all the operations in the transaction as a retriable unit.</span></span>

<span data-ttu-id="07b57-120">解決方法是以代表所有項目需要執行的委派，以手動方式叫用的執行策略。</span><span class="sxs-lookup"><span data-stu-id="07b57-120">The solution is to manually invoke the execution strategy with a delegate representing everything that needs to be executed.</span></span> <span data-ttu-id="07b57-121">如果發生暫時性失敗，執行策略會再叫用委派一次。</span><span class="sxs-lookup"><span data-stu-id="07b57-121">If a transient failure occurs, the execution strategy will invoke the delegate again.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#ManualTransaction)]

## <a name="transaction-commit-failure-and-the-idempotency-issue"></a><span data-ttu-id="07b57-122">交易認可失敗且等冪性問題</span><span class="sxs-lookup"><span data-stu-id="07b57-122">Transaction commit failure and the idempotency issue</span></span>

<span data-ttu-id="07b57-123">一般情況下，連接失敗時目前交易已回復。</span><span class="sxs-lookup"><span data-stu-id="07b57-123">In general, when there is a connection failure the current transaction is rolled back.</span></span> <span data-ttu-id="07b57-124">不過，如果交易時，會中斷連線的認可產生交易的狀態不明。</span><span class="sxs-lookup"><span data-stu-id="07b57-124">However, if the connection is dropped while the transaction is being committed the resulting state of the transaction is unknown.</span></span> <span data-ttu-id="07b57-125">請參閱此[部落格文章](http://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx)如需詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="07b57-125">See this [blog post](http://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx) for more details.</span></span>

<span data-ttu-id="07b57-126">根據預設，執行策略會重試此作業視為交易已回復，但如果不是這會導致例外狀況如果新的資料庫狀態不相容，或可能會導致**資料損毀**如果作業不需要特定的狀態，例如，當插入新的資料列，以自動產生索引鍵的值。</span><span class="sxs-lookup"><span data-stu-id="07b57-126">By default, the execution strategy will retry the operation as if the transaction was rolled back, but if it's not the case this will result in an exception if the new database state is incompatible or could lead to **data corruption** if the operation does not rely on a particular state, for example when inserting a new row with auto-generated key values.</span></span>

<span data-ttu-id="07b57-127">有數種方式來解決這個問題。</span><span class="sxs-lookup"><span data-stu-id="07b57-127">There are several ways to deal with this.</span></span>

### <a name="option-1---do-almost-nothing"></a><span data-ttu-id="07b57-128">選項 1-執行 （幾乎） 任何項目</span><span class="sxs-lookup"><span data-stu-id="07b57-128">Option 1 - Do (almost) nothing</span></span>

<span data-ttu-id="07b57-129">在交易認可期間連線失敗的可能性很低，因此可能會接受您的應用程式如果實際上會發生此狀況就會失敗。</span><span class="sxs-lookup"><span data-stu-id="07b57-129">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>

<span data-ttu-id="07b57-130">不過，您需要避免使用存放區產生的索引鍵，以確保例外狀況會擲回，而不是新增重複的資料列。</span><span class="sxs-lookup"><span data-stu-id="07b57-130">However, you need to avoid using store-generated keys in order to ensure that an exception is thrown instead of adding a duplicate row.</span></span> <span data-ttu-id="07b57-131">請考慮使用用戶端產生的 GUID 值或用戶端值產生器。</span><span class="sxs-lookup"><span data-stu-id="07b57-131">Consider using a client-generated GUID value or a client-side value generator.</span></span>

### <a name="option-2---rebuild-application-state"></a><span data-ttu-id="07b57-132">選項 2-重建應用程式狀態</span><span class="sxs-lookup"><span data-stu-id="07b57-132">Option 2 - Rebuild application state</span></span>

1. <span data-ttu-id="07b57-133">捨棄目前`DbContext`。</span><span class="sxs-lookup"><span data-stu-id="07b57-133">Discard the current `DbContext`.</span></span>
2. <span data-ttu-id="07b57-134">建立新`DbContext`並還原您的應用程式的狀態從資料庫。</span><span class="sxs-lookup"><span data-stu-id="07b57-134">Create a new `DbContext` and restore the state of your application from the database.</span></span>
3. <span data-ttu-id="07b57-135">通知使用者，最後一個作業可能無法順利完成。</span><span class="sxs-lookup"><span data-stu-id="07b57-135">Inform the user that the last operation might not have been completed successfully.</span></span>

### <a name="option-3---add-state-verification"></a><span data-ttu-id="07b57-136">選項 3-新增狀態驗證</span><span class="sxs-lookup"><span data-stu-id="07b57-136">Option 3 - Add state verification</span></span>

<span data-ttu-id="07b57-137">適用於大部分的作業，變更資料庫狀態，就可以新增會檢查它是否成功的程式碼。</span><span class="sxs-lookup"><span data-stu-id="07b57-137">For most of the operations that change the database state it is possible to add code that checks whether it succeeded.</span></span> <span data-ttu-id="07b57-138">EF 提供擴充方法更加容易- `IExecutionStrategy.ExecuteInTransaction`。</span><span class="sxs-lookup"><span data-stu-id="07b57-138">EF provides an extension method to make this easier - `IExecutionStrategy.ExecuteInTransaction`.</span></span>

<span data-ttu-id="07b57-139">這個方法會開始及認可交易，也接受中的函式`verifySucceeded`在交易認可期間發生暫時性錯誤時叫用的參數。</span><span class="sxs-lookup"><span data-stu-id="07b57-139">This method begins and commits a transaction and also accepts a function in the `verifySucceeded` parameter that is invoked when a transient error occurs during the transaction commit.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Verification)]

> [!NOTE]
> <span data-ttu-id="07b57-140">以下`SaveChanges`叫用`acceptAllChangesOnSuccess`設為`false`若要避免變更的狀態`Blog`實體`Unchanged`如果`SaveChanges`成功。</span><span class="sxs-lookup"><span data-stu-id="07b57-140">Here `SaveChanges` is invoked with `acceptAllChangesOnSuccess` set to `false` to avoid changing the state of the `Blog` entity to `Unchanged` if `SaveChanges` succeeds.</span></span> <span data-ttu-id="07b57-141">這可讓重試相同的作業，如果認可失敗，且會回復交易。</span><span class="sxs-lookup"><span data-stu-id="07b57-141">This allows to retry the same operation if the commit fails and the transaction is rolled back.</span></span>

### <a name="option-4---manually-track-the-transaction"></a><span data-ttu-id="07b57-142">選項 4-手動追蹤交易</span><span class="sxs-lookup"><span data-stu-id="07b57-142">Option 4 - Manually track the transaction</span></span>

<span data-ttu-id="07b57-143">如果您需要使用存放區所產生的金鑰，或需要一般的方式來處理認可失敗不會相依於執行的作業，每一筆交易，可以指派認可失敗時，會檢查的識別碼。</span><span class="sxs-lookup"><span data-stu-id="07b57-143">If you need to use store-generated keys or need a generic way of handling commit failures that doesn't depend on the operation performed each transaction could be assigned an ID that is checked when the commit fails.</span></span>

1. <span data-ttu-id="07b57-144">將資料表加入用來追蹤交易狀態的資料庫。</span><span class="sxs-lookup"><span data-stu-id="07b57-144">Add a table to the database used to track the status of the transactions.</span></span>
2. <span data-ttu-id="07b57-145">將資料列插入資料表中每一筆交易的開頭。</span><span class="sxs-lookup"><span data-stu-id="07b57-145">Insert a row into the table at the beginning of each transaction.</span></span>
3. <span data-ttu-id="07b57-146">如果連線失敗的認可，則會檢查資料庫中對應的資料列存在。</span><span class="sxs-lookup"><span data-stu-id="07b57-146">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>
4. <span data-ttu-id="07b57-147">如果認可成功，刪除對應的資料列，以避免資料表的成長。</span><span class="sxs-lookup"><span data-stu-id="07b57-147">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Tracking)]

> [!NOTE]
> <span data-ttu-id="07b57-148">請確定用來驗證的內容具有已定義連接時可能會失敗一次在驗證期間，如果它無法在交易認可時執行策略。</span><span class="sxs-lookup"><span data-stu-id="07b57-148">Make sure that the context used for the verification has an execution strategy defined as the connection is likely to fail again during verification if it failed during transaction commit.</span></span>
