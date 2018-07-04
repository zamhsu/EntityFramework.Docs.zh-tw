---
title: 連接恢復功能的 EF Core
author: rowanmiller
ms.author: divega
ms.date: 11/15/2016
ms.assetid: e079d4af-c455-4a14-8e15-a8471516d748
ms.technology: entity-framework-core
uid: core/miscellaneous/connection-resiliency
ms.openlocfilehash: aec69577cd4b19fdebedb33ed6fd8f2665b0a032
ms.sourcegitcommit: 860ec5d047342fbc4063a0de881c9861cc1f8813
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2017
ms.locfileid: "26053528"
---
# <a name="connection-resiliency"></a><span data-ttu-id="e0915-102">連接恢復功能</span><span class="sxs-lookup"><span data-stu-id="e0915-102">Connection Resiliency</span></span>

<span data-ttu-id="e0915-103">連接恢復功能會自動重試失敗的資料庫命令。</span><span class="sxs-lookup"><span data-stu-id="e0915-103">Connection resiliency automatically retries failed database commands.</span></span> <span data-ttu-id="e0915-104">此功能可以搭配任何資料庫，藉由提供的 「 執行策略"，封裝偵測失敗，然後重試命令所需的邏輯。</span><span class="sxs-lookup"><span data-stu-id="e0915-104">The feature can be used with any database by supplying an "execution strategy", which encapsulates the logic necessary to detect failures and retry commands.</span></span> <span data-ttu-id="e0915-105">EF Core提供者可以提供專門針對其特定的資料庫失敗狀況和最佳的重試原則的執行策略。</span><span class="sxs-lookup"><span data-stu-id="e0915-105">EF Core providers can supply execution strategies tailored to their specific database failure conditions and optimal retry policies.</span></span>

<span data-ttu-id="e0915-106">例如，SQL Server 提供者會包含專為 SQL Server （包括 SQL Azure） 的執行策略。</span><span class="sxs-lookup"><span data-stu-id="e0915-106">As an example, the SQL Server provider includes an execution strategy that is specifically tailored to SQL Server (including SQL Azure).</span></span> <span data-ttu-id="e0915-107">它可感知重試的例外狀況類型，而且有合理的預設值的最大重試，重試次數等之間的延遲。</span><span class="sxs-lookup"><span data-stu-id="e0915-107">It is aware of the exception types that can be retried and has sensible defaults for maximum retries, delay between retries, etc.</span></span>

<span data-ttu-id="e0915-108">設定您內容的選項時，將指定的執行策略。</span><span class="sxs-lookup"><span data-stu-id="e0915-108">An execution strategy is specified when configuring the options for your context.</span></span> <span data-ttu-id="e0915-109">這是通常位於`OnConfiguring`方法在衍生的內容中，或在`Startup.cs`ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="e0915-109">This is typically in the `OnConfiguring` method of your derived context, or in `Startup.cs` for an ASP.NET Core application.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#OnConfiguring)]

## <a name="custom-execution-strategy"></a><span data-ttu-id="e0915-110">自訂的執行策略</span><span class="sxs-lookup"><span data-stu-id="e0915-110">Custom execution strategy</span></span>

<span data-ttu-id="e0915-111">沒有註冊您自己，如果您想要變更預設值的任何自訂的執行策略的機制。</span><span class="sxs-lookup"><span data-stu-id="e0915-111">There is a mechanism to register a custom execution strategy of your own if you wish to change any of the defaults.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseMyProvider(
            "<connection string>",
            options => options.ExecutionStrategy(...));
}
```

## <a name="execution-strategies-and-transactions"></a><span data-ttu-id="e0915-112">執行策略和交易</span><span class="sxs-lookup"><span data-stu-id="e0915-112">Execution strategies and transactions</span></span>

<span data-ttu-id="e0915-113">會自動重試失敗的執行策略必須失敗的重試區塊中播放每個作業。</span><span class="sxs-lookup"><span data-stu-id="e0915-113">An execution strategy that automatically retries on failures needs to be able to play back each operation in an retry block that fails.</span></span> <span data-ttu-id="e0915-114">重試啟用時，您透過 EF Core執行每項作業會成為它自己的重試作業，也就是每個查詢，每次呼叫`SaveChanges()`將重試做為一個單位發生暫時性失敗。</span><span class="sxs-lookup"><span data-stu-id="e0915-114">When retries are enabled, each operation you perform via EF Core becomes its own retriable operation, i.e. each query and each call to `SaveChanges()` will be retried as a unit if a transient failure occurs.</span></span>

<span data-ttu-id="e0915-115">不過，如果您的程式碼會啟動交易，使用`BeginTransaction()`您正在定義您自己的作業需要被視為一個單位的群組，也就是在交易內的所有項目需要用來播放應該發生失敗。</span><span class="sxs-lookup"><span data-stu-id="e0915-115">However, if your code initiates a transaction using `BeginTransaction()` you are defining your own group of operations that need to be treated as a unit, i.e. everything inside the transaction would need to be played back shall a failure occur.</span></span> <span data-ttu-id="e0915-116">如果您嘗試這樣做，使用的執行策略時，您會收到類似下面的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="e0915-116">You will receive an exception like the following if you attempt to do this when using an execution strategy.</span></span>

> <span data-ttu-id="e0915-117">InvalidOperationException： 設定的執行策略 'SqlServerRetryingExecutionStrategy' 不支援使用者起始的交易。</span><span class="sxs-lookup"><span data-stu-id="e0915-117">InvalidOperationException: The configured execution strategy 'SqlServerRetryingExecutionStrategy' does not support user initiated transactions.</span></span> <span data-ttu-id="e0915-118">在做為可重試單位的交易中執行所有作業中使用 'DbContext.Database.CreateExecutionStrategy()' 所傳回的執行策略。</span><span class="sxs-lookup"><span data-stu-id="e0915-118">Use the execution strategy returned by 'DbContext.Database.CreateExecutionStrategy()' to execute all the operations in the transaction as a retriable unit.</span></span>

<span data-ttu-id="e0915-119">解決方法是手動叫用的委派代表的所有項目需要執行的執行策略。</span><span class="sxs-lookup"><span data-stu-id="e0915-119">The solution is to manually invoke the execution strategy with a delegate representing everything that needs to be executed.</span></span> <span data-ttu-id="e0915-120">如果發生暫時性中斷，執行策略會叫用委派一次。</span><span class="sxs-lookup"><span data-stu-id="e0915-120">If a transient failure occurs, the execution strategy will invoke the delegate again.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#ManualTransaction)]

## <a name="transaction-commit-failure-and-the-idempotency-issue"></a><span data-ttu-id="e0915-121">交易認可失敗，以及等冪性問題</span><span class="sxs-lookup"><span data-stu-id="e0915-121">Transaction commit failure and the idempotency issue</span></span>

<span data-ttu-id="e0915-122">一般情況下，當連線失敗目前交易已回復。</span><span class="sxs-lookup"><span data-stu-id="e0915-122">In general, when there is a connection failure the current transaction is rolled back.</span></span> <span data-ttu-id="e0915-123">不過，如果交易時，會中斷連線將會認可所產生的交易的狀態不明。</span><span class="sxs-lookup"><span data-stu-id="e0915-123">However, if the connection is dropped while the transaction is being committed the resulting state of the transaction is unknown.</span></span> <span data-ttu-id="e0915-124">請參閱此[部落格文章](http://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx)如需詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="e0915-124">See this [blog post](http://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx) for more details.</span></span>

<span data-ttu-id="e0915-125">根據預設，執行策略會重試作業視為已回復交易，但如果不是這會導致例外狀況，如果新的資料庫狀態不相容，或可能會導致**資料損毀**如果作業不會依賴特定的狀態，例如，當插入新的資料列，以自動產生索引鍵的值。</span><span class="sxs-lookup"><span data-stu-id="e0915-125">By default, the execution strategy will retry the operation as if the transaction was rolled back, but if it's not the case this will result in an exception if the new database state is incompatible or could lead to **data corruption** if the operation does not rely on a particular state, for example when inserting a new row with auto-generated key values.</span></span>

<span data-ttu-id="e0915-126">有數種方式可解決這個問題。</span><span class="sxs-lookup"><span data-stu-id="e0915-126">There are several ways to deal with this.</span></span>

### <a name="option-1---do-almost-nothing"></a><span data-ttu-id="e0915-127">選項 1-不要 nothing （幾乎）</span><span class="sxs-lookup"><span data-stu-id="e0915-127">Option 1 - Do (almost) nothing</span></span>

<span data-ttu-id="e0915-128">因此，可能會接受您的應用程式如果確實發生這種情況只會失敗，交易認可期間連線失敗的可能性會很低。</span><span class="sxs-lookup"><span data-stu-id="e0915-128">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>

<span data-ttu-id="e0915-129">不過，您需要避免使用存放區產生的金鑰，才能確保例外狀況會擲回，而非新增重複的資料列。</span><span class="sxs-lookup"><span data-stu-id="e0915-129">However, you need to avoid using store-generated keys in order to ensure that an exception is thrown instead of adding a duplicate row.</span></span> <span data-ttu-id="e0915-130">請考慮使用用戶端產生的 GUID 值或用戶端值產生器。</span><span class="sxs-lookup"><span data-stu-id="e0915-130">Consider using a client-generated GUID value or a client-side value generator.</span></span>

### <a name="option-2---rebuild-application-state"></a><span data-ttu-id="e0915-131">選項 2-重建應用程式狀態</span><span class="sxs-lookup"><span data-stu-id="e0915-131">Option 2 - Rebuild application state</span></span>

1. <span data-ttu-id="e0915-132">捨棄目前`DbContext`。</span><span class="sxs-lookup"><span data-stu-id="e0915-132">Discard the current `DbContext`.</span></span>
2. <span data-ttu-id="e0915-133">建立新`DbContext`，並從資料庫還原應用程式的狀態。</span><span class="sxs-lookup"><span data-stu-id="e0915-133">Create a new `DbContext` and restore the state of your application from the database.</span></span>
3. <span data-ttu-id="e0915-134">通知使用者，最後一項作業可能無法順利完成。</span><span class="sxs-lookup"><span data-stu-id="e0915-134">Inform the user that the last operation might not have been completed successfully.</span></span>

### <a name="option-3---add-state-verification"></a><span data-ttu-id="e0915-135">選項 3-新增狀態驗證</span><span class="sxs-lookup"><span data-stu-id="e0915-135">Option 3 - Add state verification</span></span>

<span data-ttu-id="e0915-136">對於大多數的變更資料庫狀態的作業是可以加入程式碼，檢查是否成功。</span><span class="sxs-lookup"><span data-stu-id="e0915-136">For most of the operations that change the database state it is possible to add code that checks whether it succeeded.</span></span> <span data-ttu-id="e0915-137">EF 提供擴充方法來進行簡化- `IExecutionStrategy.ExecuteInTransaction`。</span><span class="sxs-lookup"><span data-stu-id="e0915-137">EF provides an extension method to make this easier - `IExecutionStrategy.ExecuteInTransaction`.</span></span>

<span data-ttu-id="e0915-138">這個方法會開始及認可交易，也接受中的函式`verifySucceeded`暫時性錯誤發生的交易認可時，會叫用的參數。</span><span class="sxs-lookup"><span data-stu-id="e0915-138">This method begins and commits a transaction and also accepts a function in the `verifySucceeded` parameter that is invoked when a transient error occurs during the transaction commit.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Verification)]

> [!NOTE]
> <span data-ttu-id="e0915-139">這裡`SaveChanges`用來叫用`acceptAllChangesOnSuccess`設`false`若要避免變更的狀態`Blog`實體`Unchanged`如果`SaveChanges`成功。</span><span class="sxs-lookup"><span data-stu-id="e0915-139">Here `SaveChanges` is invoked with `acceptAllChangesOnSuccess` set to `false` to avoid changing the state of the `Blog` entity to `Unchanged` if `SaveChanges` succeeds.</span></span> <span data-ttu-id="e0915-140">這可讓則認可會失敗並回復交易時，重試相同的作業。</span><span class="sxs-lookup"><span data-stu-id="e0915-140">This allows to retry the same operation if the commit fails and the transaction is rolled back.</span></span>

### <a name="option-4---manually-track-the-transaction"></a><span data-ttu-id="e0915-141">選項 4-手動追蹤的交易</span><span class="sxs-lookup"><span data-stu-id="e0915-141">Option 4 - Manually track the transaction</span></span>

<span data-ttu-id="e0915-142">如果您需要使用存放區產生的索引鍵，或需要執行的作業不相依的處理認可失敗的泛型方法的每個交易無法指派則認可會失敗時，會檢查的識別碼。</span><span class="sxs-lookup"><span data-stu-id="e0915-142">If you need to use store-generated keys or need a generic way of handling commit failures that doesn't depend on the operation performed each transaction could be assigned an ID that is checked when the commit fails.</span></span>

1. <span data-ttu-id="e0915-143">將資料表加入至資料庫用來追蹤交易狀態。</span><span class="sxs-lookup"><span data-stu-id="e0915-143">Add a table to the database used to track the status of the transactions.</span></span>
2. <span data-ttu-id="e0915-144">將資料列插入資料表中每個交易的開頭。</span><span class="sxs-lookup"><span data-stu-id="e0915-144">Insert a row into the table at the beginning of each transaction.</span></span>
3. <span data-ttu-id="e0915-145">如果連接失敗的認可，請檢查資料庫中對應的資料列存在。</span><span class="sxs-lookup"><span data-stu-id="e0915-145">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>
4. <span data-ttu-id="e0915-146">如果認可成功，刪除對應的資料列，以避免資料表成長。</span><span class="sxs-lookup"><span data-stu-id="e0915-146">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Tracking)]

> [!NOTE]
> <span data-ttu-id="e0915-147">請確定用於驗證的內容具有已定義連接時可能會失敗一次在驗證期間，如果它無法在交易認可時的執行策略。</span><span class="sxs-lookup"><span data-stu-id="e0915-147">Make sure that the context used for the verification has an execution strategy defined as the connection is likely to fail again during verification if it failed during transaction commit.</span></span>
