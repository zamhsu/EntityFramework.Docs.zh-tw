---
title: 處理交易認可失敗-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 5b1f7a7d-1b24-4645-95ec-5608a31ef577
ms.openlocfilehash: 27e75e6a1919ee2300fe76cfcdf67cceaad887b3
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417367"
---
# <a name="handling-transaction-commit-failures"></a><span data-ttu-id="679ac-102">處理交易認可失敗</span><span class="sxs-lookup"><span data-stu-id="679ac-102">Handling transaction commit failures</span></span>
> [!NOTE]
> <span data-ttu-id="679ac-103">**僅限 ef 6.1** ，此頁面中所討論的功能、api 等已在 Entity Framework 6.1 中引進。</span><span class="sxs-lookup"><span data-stu-id="679ac-103">**EF6.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="679ac-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="679ac-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="679ac-105">在6.1 中，我們為 EF 引進了新的連接復原功能：當暫時性連接失敗影響交易認可的認可時，自動偵測並復原的能力。</span><span class="sxs-lookup"><span data-stu-id="679ac-105">As part of 6.1 we are introducing a new connection resiliency feature for EF: the ability to detect and recover automatically when transient connection failures affect the acknowledgement of transaction commits.</span></span> <span data-ttu-id="679ac-106">案例的完整詳細資料會在[SQL Database 連線和等冪性問題](https://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx)的 blog 文章中描述。</span><span class="sxs-lookup"><span data-stu-id="679ac-106">The full details of the scenario are best described in the blog post [SQL Database Connectivity and the Idempotency Issue](https://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx).</span></span>  <span data-ttu-id="679ac-107">總而言之，案例是在交易認可期間引發例外狀況時，有兩個可能的原因：</span><span class="sxs-lookup"><span data-stu-id="679ac-107">In summary, the scenario is that when an exception is raised during a transaction commit there are two possible causes:</span></span>  

1. <span data-ttu-id="679ac-108">伺服器上的交易認可失敗</span><span class="sxs-lookup"><span data-stu-id="679ac-108">The transaction commit failed on the server</span></span>
2. <span data-ttu-id="679ac-109">伺服器上的交易認可成功，但連線問題導致成功通知無法到達用戶端</span><span class="sxs-lookup"><span data-stu-id="679ac-109">The transaction commit succeeded on the server but a connectivity issue prevented the success notification from reaching the client</span></span>  

<span data-ttu-id="679ac-110">當第一次發生這種情況時，應用程式或使用者可以重試此作業，但在第二次發生問題時，應該避免重試，而且應用程式可以自動復原。</span><span class="sxs-lookup"><span data-stu-id="679ac-110">When the first situation happens the application or the user can retry the operation, but when the second situation occurs retries should be avoided and the application could recover automatically.</span></span> <span data-ttu-id="679ac-111">挑戰在於，如果沒有偵測到在認可期間回報例外狀況的實際原因，應用程式就無法選擇正確的動作。</span><span class="sxs-lookup"><span data-stu-id="679ac-111">The challenge is that without the ability to detect what was the actual reason an exception was reported during commit, the application cannot choose the right course of action.</span></span> <span data-ttu-id="679ac-112">EF 6.1 中的新功能可讓 EF 在交易成功時再次檢查資料庫，並以透明的方式採取適當的動作。</span><span class="sxs-lookup"><span data-stu-id="679ac-112">The new feature in EF 6.1 allows EF to double-check with the database if the transaction succeeded and take the right course of action transparently.</span></span>  

## <a name="using-the-feature"></a><span data-ttu-id="679ac-113">使用此功能</span><span class="sxs-lookup"><span data-stu-id="679ac-113">Using the feature</span></span>  

<span data-ttu-id="679ac-114">若要啟用這項功能，您需要在**DbConfiguration**的函式中包含對[SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx)的呼叫。</span><span class="sxs-lookup"><span data-stu-id="679ac-114">In order to enable the feature you need include a call to [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) in the constructor of your **DbConfiguration**.</span></span> <span data-ttu-id="679ac-115">如果您不熟悉**DbConfiguration**，請參閱以程式[代碼為基礎](~/ef6/fundamentals/configuring/code-based.md)的設定。</span><span class="sxs-lookup"><span data-stu-id="679ac-115">If you are unfamiliar with **DbConfiguration**, see [Code Based Configuration](~/ef6/fundamentals/configuring/code-based.md).</span></span> <span data-ttu-id="679ac-116">這項功能可與我們在 EF6 中引進的自動重試搭配使用，這有助於在交易實際因暫時性失敗而無法在伺服器上認可的情況下進行：</span><span class="sxs-lookup"><span data-stu-id="679ac-116">This feature can be used in combination with the automatic retries we introduced in EF6, which help in the situation in which the transaction actually failed to commit on the server due to a transient failure:</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.SqlServer;

public class MyConfiguration : DbConfiguration  
{
  public MyConfiguration()  
  {  
    SetTransactionHandler(SqlProviderServices.ProviderInvariantName, () => new CommitFailureHandler());  
    SetExecutionStrategy(SqlProviderServices.ProviderInvariantName, () => new SqlAzureExecutionStrategy());  
  }  
}
```  

## <a name="how-transactions-are-tracked"></a><span data-ttu-id="679ac-117">追蹤交易的方式</span><span class="sxs-lookup"><span data-stu-id="679ac-117">How transactions are tracked</span></span>  

<span data-ttu-id="679ac-118">啟用此功能時，EF 會自動將新的資料表加入至名為 **__Transactions**的資料庫。</span><span class="sxs-lookup"><span data-stu-id="679ac-118">When the feature is enabled, EF will automatically add a new table to the database called **__Transactions**.</span></span> <span data-ttu-id="679ac-119">每當 EF 建立交易時，就會在此資料表中插入新的資料列，而且在認可期間發生交易失敗時，會檢查該資料列是否存在。</span><span class="sxs-lookup"><span data-stu-id="679ac-119">A new row is inserted in this table every time a transaction is created by EF and that row is checked for existence if a transaction failure occurs during commit.</span></span>  

<span data-ttu-id="679ac-120">雖然在不再需要時，EF 會盡力剪除資料表中的資料列，但如果應用程式過早結束，則資料表可能會成長，因此，在某些情況下您可能需要手動清除資料表。</span><span class="sxs-lookup"><span data-stu-id="679ac-120">Although EF will do a best effort to prune rows from the table when they aren’t needed anymore, the table can grow if the application exits prematurely and for that reason you may need to purge the table manually in some cases.</span></span>  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a><span data-ttu-id="679ac-121">如何處理先前版本的認可失敗</span><span class="sxs-lookup"><span data-stu-id="679ac-121">How to handle commit failures with previous Versions</span></span>

<span data-ttu-id="679ac-122">在 EF 6.1 之前，沒有機制可處理 EF 產品中的認可失敗。</span><span class="sxs-lookup"><span data-stu-id="679ac-122">Before EF 6.1 there was not mechanism to handle commit failures in the EF product.</span></span> <span data-ttu-id="679ac-123">有數種方式可以處理這種情況，這種情況適用于舊版的 EF6：</span><span class="sxs-lookup"><span data-stu-id="679ac-123">There are several ways to dealing with this situation that can be applied to previous versions of EF6:</span></span>  

* <span data-ttu-id="679ac-124">選項 1-不執行任何動作</span><span class="sxs-lookup"><span data-stu-id="679ac-124">Option 1 - Do nothing</span></span>  

  <span data-ttu-id="679ac-125">在交易認可期間發生連接失敗的可能性很低，因此如果發生此情況，您的應用程式就可能會失敗。</span><span class="sxs-lookup"><span data-stu-id="679ac-125">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>  

* <span data-ttu-id="679ac-126">選項 2-使用資料庫來重設狀態</span><span class="sxs-lookup"><span data-stu-id="679ac-126">Option 2 - Use the database to reset state</span></span>  

  1. <span data-ttu-id="679ac-127">捨棄目前的 DbCoNtext</span><span class="sxs-lookup"><span data-stu-id="679ac-127">Discard the current DbContext</span></span>  
  2. <span data-ttu-id="679ac-128">建立新的 DbCoNtext，並從資料庫還原應用程式的狀態</span><span class="sxs-lookup"><span data-stu-id="679ac-128">Create a new DbContext and restore the state of your application from the database</span></span>  
  3. <span data-ttu-id="679ac-129">通知使用者最近一次作業可能未順利完成</span><span class="sxs-lookup"><span data-stu-id="679ac-129">Inform the user that the last operation might not have been completed successfully</span></span>  

* <span data-ttu-id="679ac-130">選項 3-手動追蹤交易</span><span class="sxs-lookup"><span data-stu-id="679ac-130">Option 3 - Manually track the transaction</span></span>  

  1. <span data-ttu-id="679ac-131">將非追蹤資料表加入至用來追蹤交易狀態的資料庫。</span><span class="sxs-lookup"><span data-stu-id="679ac-131">Add a non-tracked table to the database used to track the status of the transactions.</span></span>  
  2. <span data-ttu-id="679ac-132">在每個交易開頭的資料表中插入一個資料列。</span><span class="sxs-lookup"><span data-stu-id="679ac-132">Insert a row into the table at the beginning of each transaction.</span></span>  
  3. <span data-ttu-id="679ac-133">如果在認可期間連接失敗，請檢查資料庫中對應的資料列是否存在。</span><span class="sxs-lookup"><span data-stu-id="679ac-133">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>  
     - <span data-ttu-id="679ac-134">如果資料列存在，請在交易認可成功時繼續正常執行</span><span class="sxs-lookup"><span data-stu-id="679ac-134">If the row is present, continue normally, as the transaction was committed successfully</span></span>  
     - <span data-ttu-id="679ac-135">如果資料列不存在，請使用執行策略來重試目前的作業。</span><span class="sxs-lookup"><span data-stu-id="679ac-135">If the row is absent, use an execution strategy to retry the current operation.</span></span>  
  4. <span data-ttu-id="679ac-136">如果認可成功，請刪除對應的資料列，以避免資料表的成長。</span><span class="sxs-lookup"><span data-stu-id="679ac-136">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>  

<span data-ttu-id="679ac-137">[此 blog 文章](https://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx)包含在 SQL Azure 上完成此操作的範例程式碼。</span><span class="sxs-lookup"><span data-stu-id="679ac-137">[This blog post](https://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx) contains sample code for accomplishing this on SQL Azure.</span></span>  
