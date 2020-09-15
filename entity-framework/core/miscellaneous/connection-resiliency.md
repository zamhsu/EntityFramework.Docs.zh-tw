---
title: 連接復原-EF Core
description: 使用連接復原功能，利用 Entity Framework Core 自動重試失敗的命令
author: rowanmiller
ms.date: 11/15/2016
uid: core/miscellaneous/connection-resiliency
ms.openlocfilehash: f4077672a9234bf66dc488d7450f437452e542b6
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071702"
---
# <a name="connection-resiliency"></a><span data-ttu-id="0a2da-103">連接恢復功能</span><span class="sxs-lookup"><span data-stu-id="0a2da-103">Connection Resiliency</span></span>

<span data-ttu-id="0a2da-104">連接恢復功能會自動重試失敗的資料庫命令。</span><span class="sxs-lookup"><span data-stu-id="0a2da-104">Connection resiliency automatically retries failed database commands.</span></span> <span data-ttu-id="0a2da-105">這項功能可搭配任何資料庫使用，方法是提供「執行策略」，此策略會封裝偵測失敗和重試命令所需的邏輯。</span><span class="sxs-lookup"><span data-stu-id="0a2da-105">The feature can be used with any database by supplying an "execution strategy", which encapsulates the logic necessary to detect failures and retry commands.</span></span> <span data-ttu-id="0a2da-106">EF Core 提供者可以針對其特定資料庫失敗狀況和最佳重試原則，提供量身訂做的執行策略。</span><span class="sxs-lookup"><span data-stu-id="0a2da-106">EF Core providers can supply execution strategies tailored to their specific database failure conditions and optimal retry policies.</span></span>

<span data-ttu-id="0a2da-107">例如，SQL Server 提供者包含專門針對 SQL Server (（包括 SQL Azure) ）量身打造的執行策略。</span><span class="sxs-lookup"><span data-stu-id="0a2da-107">As an example, the SQL Server provider includes an execution strategy that is specifically tailored to SQL Server (including SQL Azure).</span></span> <span data-ttu-id="0a2da-108">它知道可以重試的例外狀況類型，而且有合理的預設值可重試最大值、重試之間的延遲等等。</span><span class="sxs-lookup"><span data-stu-id="0a2da-108">It is aware of the exception types that can be retried and has sensible defaults for maximum retries, delay between retries, etc.</span></span>

<span data-ttu-id="0a2da-109">設定內容的選項時，會指定執行策略。</span><span class="sxs-lookup"><span data-stu-id="0a2da-109">An execution strategy is specified when configuring the options for your context.</span></span> <span data-ttu-id="0a2da-110">這通常會在 `OnConfiguring` 您衍生內容的方法中：</span><span class="sxs-lookup"><span data-stu-id="0a2da-110">This is typically in the `OnConfiguring` method of your derived context:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#OnConfiguring)]

<span data-ttu-id="0a2da-111">或中 `Startup.cs` 的 ASP.NET Core 應用程式：</span><span class="sxs-lookup"><span data-stu-id="0a2da-111">or in `Startup.cs` for an ASP.NET Core application:</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<PicnicContext>(
        options => options.UseSqlServer(
            "<connection string>",
            providerOptions => providerOptions.EnableRetryOnFailure()));
}
```

## <a name="custom-execution-strategy"></a><span data-ttu-id="0a2da-112">自訂執行策略</span><span class="sxs-lookup"><span data-stu-id="0a2da-112">Custom execution strategy</span></span>

<span data-ttu-id="0a2da-113">如果您想要變更任何預設值，有一種機制可註冊您自己的自訂執行策略。</span><span class="sxs-lookup"><span data-stu-id="0a2da-113">There is a mechanism to register a custom execution strategy of your own if you wish to change any of the defaults.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseMyProvider(
            "<connection string>",
            options => options.ExecutionStrategy(...));
}
```

## <a name="execution-strategies-and-transactions"></a><span data-ttu-id="0a2da-114">執行策略和交易</span><span class="sxs-lookup"><span data-stu-id="0a2da-114">Execution strategies and transactions</span></span>

<span data-ttu-id="0a2da-115">自動重試失敗的執行策略，必須能夠在失敗的重試區塊中播放每項作業。</span><span class="sxs-lookup"><span data-stu-id="0a2da-115">An execution strategy that automatically retries on failures needs to be able to play back each operation in a retry block that fails.</span></span> <span data-ttu-id="0a2da-116">啟用重試時，您透過 EF Core 執行的每個作業都會變成其本身的可重試作業。</span><span class="sxs-lookup"><span data-stu-id="0a2da-116">When retries are enabled, each operation you perform via EF Core becomes its own retriable operation.</span></span> <span data-ttu-id="0a2da-117">也就是說，如果發生暫時性失敗，則每個查詢和每個呼叫 `SaveChanges()` 都會以一個單位來重試。</span><span class="sxs-lookup"><span data-stu-id="0a2da-117">That is, each query and each call to `SaveChanges()` will be retried as a unit if a transient failure occurs.</span></span>

<span data-ttu-id="0a2da-118">但是，如果您的程式碼使用您來起始交易 `BeginTransaction()` ，而您要定義一組需要視為一個單位的作業，而且必須播放交易內的所有專案，就會發生失敗。</span><span class="sxs-lookup"><span data-stu-id="0a2da-118">However, if your code initiates a transaction using `BeginTransaction()` you are defining your own group of operations that need to be treated as a unit, and everything inside the transaction would need to be played back shall a failure occur.</span></span> <span data-ttu-id="0a2da-119">如果您在使用執行策略時嘗試這麼做，就會收到類似下面的例外狀況：</span><span class="sxs-lookup"><span data-stu-id="0a2da-119">You will receive an exception like the following if you attempt to do this when using an execution strategy:</span></span>

> <span data-ttu-id="0a2da-120">InvalidOperationException：設定的執行策略 ' SqlServerRetryingExecutionStrategy ' 不支援使用者起始的交易。</span><span class="sxs-lookup"><span data-stu-id="0a2da-120">InvalidOperationException: The configured execution strategy 'SqlServerRetryingExecutionStrategy' does not support user initiated transactions.</span></span> <span data-ttu-id="0a2da-121">使用 'DbContext.Database.CreateExecutionStrategy()' 所傳回的執行策略，將異動中的所有作業當做一個可重試的單位來執行。</span><span class="sxs-lookup"><span data-stu-id="0a2da-121">Use the execution strategy returned by 'DbContext.Database.CreateExecutionStrategy()' to execute all the operations in the transaction as a retriable unit.</span></span>

<span data-ttu-id="0a2da-122">解決方法是使用委派（代表需要執行的所有專案）手動叫用執行策略。</span><span class="sxs-lookup"><span data-stu-id="0a2da-122">The solution is to manually invoke the execution strategy with a delegate representing everything that needs to be executed.</span></span> <span data-ttu-id="0a2da-123">如果發生暫時性失敗，執行策略會再次叫用委派。</span><span class="sxs-lookup"><span data-stu-id="0a2da-123">If a transient failure occurs, the execution strategy will invoke the delegate again.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#ManualTransaction)]

<span data-ttu-id="0a2da-124">這種方法也可以搭配環境交易使用。</span><span class="sxs-lookup"><span data-stu-id="0a2da-124">This approach can also be used with ambient transactions.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#AmbientTransaction)]

## <a name="transaction-commit-failure-and-the-idempotency-issue"></a><span data-ttu-id="0a2da-125">交易認可失敗和等冪性問題</span><span class="sxs-lookup"><span data-stu-id="0a2da-125">Transaction commit failure and the idempotency issue</span></span>

<span data-ttu-id="0a2da-126">一般情況下，當發生連接失敗時，就會回復目前的交易。</span><span class="sxs-lookup"><span data-stu-id="0a2da-126">In general, when there is a connection failure the current transaction is rolled back.</span></span> <span data-ttu-id="0a2da-127">但是，如果在認可交易時中斷連接，則交易的結果狀態是未知的。</span><span class="sxs-lookup"><span data-stu-id="0a2da-127">However, if the connection is dropped while the transaction is being committed the resulting state of the transaction is unknown.</span></span> 

<span data-ttu-id="0a2da-128">根據預設，執行策略會重試此作業，就像交易已回復一樣，但如果不是如此，則如果新的資料庫狀態不相容，或如果作業不依賴特定狀態（例如，使用自動產生的索引鍵值插入新的資料列時），可能會導致 **資料損毀** ，這會導致例外狀況。</span><span class="sxs-lookup"><span data-stu-id="0a2da-128">By default, the execution strategy will retry the operation as if the transaction was rolled back, but if it's not the case this will result in an exception if the new database state is incompatible or could lead to **data corruption** if the operation does not rely on a particular state, for example when inserting a new row with auto-generated key values.</span></span>

<span data-ttu-id="0a2da-129">有幾種方式可以處理這種情況。</span><span class="sxs-lookup"><span data-stu-id="0a2da-129">There are several ways to deal with this.</span></span>

### <a name="option-1---do-almost-nothing"></a><span data-ttu-id="0a2da-130">選項 1- (幾乎) nothing</span><span class="sxs-lookup"><span data-stu-id="0a2da-130">Option 1 - Do (almost) nothing</span></span>

<span data-ttu-id="0a2da-131">在交易認可期間連接失敗的可能性很低，因此您的應用程式可能會在此情況真的發生時失敗。</span><span class="sxs-lookup"><span data-stu-id="0a2da-131">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>

<span data-ttu-id="0a2da-132">但是，您必須避免使用存放區產生的索引鍵，以確保會擲回例外狀況，而不是加入重複的資料列。</span><span class="sxs-lookup"><span data-stu-id="0a2da-132">However, you need to avoid using store-generated keys in order to ensure that an exception is thrown instead of adding a duplicate row.</span></span> <span data-ttu-id="0a2da-133">請考慮使用用戶端產生的 GUID 值或用戶端值產生器。</span><span class="sxs-lookup"><span data-stu-id="0a2da-133">Consider using a client-generated GUID value or a client-side value generator.</span></span>

### <a name="option-2---rebuild-application-state"></a><span data-ttu-id="0a2da-134">選項 2-重建應用程式狀態</span><span class="sxs-lookup"><span data-stu-id="0a2da-134">Option 2 - Rebuild application state</span></span>

1. <span data-ttu-id="0a2da-135">捨棄目前的 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="0a2da-135">Discard the current `DbContext`.</span></span>
2. <span data-ttu-id="0a2da-136">建立新的 `DbContext` ，並從資料庫還原應用程式的狀態。</span><span class="sxs-lookup"><span data-stu-id="0a2da-136">Create a new `DbContext` and restore the state of your application from the database.</span></span>
3. <span data-ttu-id="0a2da-137">通知使用者，最後一個作業可能未順利完成。</span><span class="sxs-lookup"><span data-stu-id="0a2da-137">Inform the user that the last operation might not have been completed successfully.</span></span>

### <a name="option-3---add-state-verification"></a><span data-ttu-id="0a2da-138">選項 3-新增狀態驗證</span><span class="sxs-lookup"><span data-stu-id="0a2da-138">Option 3 - Add state verification</span></span>

<span data-ttu-id="0a2da-139">針對變更資料庫狀態的大部分作業，可以加入程式碼來檢查是否成功。</span><span class="sxs-lookup"><span data-stu-id="0a2da-139">For most of the operations that change the database state it is possible to add code that checks whether it succeeded.</span></span> <span data-ttu-id="0a2da-140">EF 提供延伸方法讓您更輕鬆 `IExecutionStrategy.ExecuteInTransaction` 。</span><span class="sxs-lookup"><span data-stu-id="0a2da-140">EF provides an extension method to make this easier - `IExecutionStrategy.ExecuteInTransaction`.</span></span>

<span data-ttu-id="0a2da-141">這個方法會開始和認可交易，也會接受在 `verifySucceeded` 交易認可期間發生暫時性錯誤時所叫用之參數中的函數。</span><span class="sxs-lookup"><span data-stu-id="0a2da-141">This method begins and commits a transaction and also accepts a function in the `verifySucceeded` parameter that is invoked when a transient error occurs during the transaction commit.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Verification)]

> [!NOTE]
> <span data-ttu-id="0a2da-142">在此叫 `SaveChanges` 用，並 `acceptAllChangesOnSuccess` 將設定為 `false` ，以避免在 `Blog` 成功時將實體的狀態變更為 `Unchanged` `SaveChanges` 。</span><span class="sxs-lookup"><span data-stu-id="0a2da-142">Here `SaveChanges` is invoked with `acceptAllChangesOnSuccess` set to `false` to avoid changing the state of the `Blog` entity to `Unchanged` if `SaveChanges` succeeds.</span></span> <span data-ttu-id="0a2da-143">這可讓您在認可失敗並回復交易時，重試相同的作業。</span><span class="sxs-lookup"><span data-stu-id="0a2da-143">This allows to retry the same operation if the commit fails and the transaction is rolled back.</span></span>

### <a name="option-4---manually-track-the-transaction"></a><span data-ttu-id="0a2da-144">選項 4-手動追蹤交易</span><span class="sxs-lookup"><span data-stu-id="0a2da-144">Option 4 - Manually track the transaction</span></span>

<span data-ttu-id="0a2da-145">如果您需要使用存放區產生的索引鍵，或需要一般方式來處理不相依于所執行之作業的認可失敗，則可以將認可失敗時所檢查的識別碼指派給交易。</span><span class="sxs-lookup"><span data-stu-id="0a2da-145">If you need to use store-generated keys or need a generic way of handling commit failures that doesn't depend on the operation performed each transaction could be assigned an ID that is checked when the commit fails.</span></span>

1. <span data-ttu-id="0a2da-146">將資料表加入至用來追蹤交易狀態的資料庫。</span><span class="sxs-lookup"><span data-stu-id="0a2da-146">Add a table to the database used to track the status of the transactions.</span></span>
2. <span data-ttu-id="0a2da-147">在每個交易開始時，將資料列插入資料表中。</span><span class="sxs-lookup"><span data-stu-id="0a2da-147">Insert a row into the table at the beginning of each transaction.</span></span>
3. <span data-ttu-id="0a2da-148">如果在認可期間連接失敗，請檢查資料庫中對應的資料列是否存在。</span><span class="sxs-lookup"><span data-stu-id="0a2da-148">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>
4. <span data-ttu-id="0a2da-149">如果認可成功，請刪除對應的資料列，以避免資料表的成長。</span><span class="sxs-lookup"><span data-stu-id="0a2da-149">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Tracking)]

> [!NOTE]
> <span data-ttu-id="0a2da-150">請確定用於驗證的內容已定義執行策略，因為連接在交易認可期間失敗時，可能會在驗證期間再次失敗。</span><span class="sxs-lookup"><span data-stu-id="0a2da-150">Make sure that the context used for the verification has an execution strategy defined as the connection is likely to fail again during verification if it failed during transaction commit.</span></span>
