---
title: 處理交易認可失敗 EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 5b1f7a7d-1b24-4645-95ec-5608a31ef577
ms.openlocfilehash: 71d5649dd993bb95e24165a55d812c71a37f03f3
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45489384"
---
# <a name="handling-transaction-commit-failures"></a><span data-ttu-id="7851c-102">處理交易認可失敗</span><span class="sxs-lookup"><span data-stu-id="7851c-102">Handling transaction commit failures</span></span>
> [!NOTE]
> <span data-ttu-id="7851c-103">**EF6.1 及更新版本僅**-功能、 Api、 Entity Framework 6.1 中導入等本頁所述。</span><span class="sxs-lookup"><span data-stu-id="7851c-103">**EF6.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="7851c-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="7851c-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="7851c-105">6.1 的過程中，我們引進新的連接恢復功能的 EF： 偵測和暫時性的連線失敗影響認可的交易認可時自動復原的能力。</span><span class="sxs-lookup"><span data-stu-id="7851c-105">As part of 6.1 we are introducing a new connection resiliency feature for EF: the ability to detect and recover automatically when transient connection failures affect the acknowledgement of transaction commits.</span></span> <span data-ttu-id="7851c-106">此案例的完整詳細資料最中所述的部落格文章[SQL Database 連接性 」 和 「 等冪性問題](http://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx)。</span><span class="sxs-lookup"><span data-stu-id="7851c-106">The full details of the scenario are best described in the blog post [SQL Database Connectivity and the Idempotency Issue](http://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx).</span></span>  <span data-ttu-id="7851c-107">在 [摘要] 的案例是當交易認可期間引發例外狀況有兩個可能的原因：</span><span class="sxs-lookup"><span data-stu-id="7851c-107">In summary, the scenario is that when an exception is raised during a transaction commit there are two possible causes:</span></span>  

1. <span data-ttu-id="7851c-108">伺服器上的交易認可失敗</span><span class="sxs-lookup"><span data-stu-id="7851c-108">The transaction commit failed on the server</span></span>
2. <span data-ttu-id="7851c-109">在伺服器上的交易認可成功，但有連線問題導致無法成功通知，使其無法到達用戶端</span><span class="sxs-lookup"><span data-stu-id="7851c-109">The transaction commit succeeded on the server but a connectivity issue prevented the success notification from reaching the client</span></span>  

<span data-ttu-id="7851c-110">第一種情況發生在應用程式或使用者可以重試此作業，但第二種情況發生時應避免重試次數和應用程式無法自動復原。</span><span class="sxs-lookup"><span data-stu-id="7851c-110">When the first situation happens the application or the user can retry the operation, but when the second situation occurs retries should be avoided and the application could recover automatically.</span></span> <span data-ttu-id="7851c-111">所面臨的挑戰是動作的，而不需要偵測哪些項目為已回報例外狀況認可、 應用程式無法選擇正確採取的實際原因的能力。</span><span class="sxs-lookup"><span data-stu-id="7851c-111">The challenge is that without the ability to detect what was the actual reason an exception was reported during commit, the application cannot choose the right course of action.</span></span> <span data-ttu-id="7851c-112">EF 6.1 」 中的新功能可讓 EF 來與資料庫再次檢查，如果交易成功且無障礙地採取正確採取的動作。</span><span class="sxs-lookup"><span data-stu-id="7851c-112">The new feature in EF 6.1 allows EF to double-check with the database if the transaction succeeded and take the right course of action transparently.</span></span>  

## <a name="using-the-feature"></a><span data-ttu-id="7851c-113">使用功能</span><span class="sxs-lookup"><span data-stu-id="7851c-113">Using the feature</span></span>  

<span data-ttu-id="7851c-114">若要啟用此功能，您需要包含呼叫[SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx)建構函式中您**DbConfiguration**。</span><span class="sxs-lookup"><span data-stu-id="7851c-114">In order to enable the feature you need include a call to [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) in the constructor of your **DbConfiguration**.</span></span> <span data-ttu-id="7851c-115">如果您熟悉**DbConfiguration**，請參閱[程式碼基礎組態](~/ef6/fundamentals/configuring/code-based.md)。</span><span class="sxs-lookup"><span data-stu-id="7851c-115">If you are unfamiliar with **DbConfiguration**, see [Code Based Configuration](~/ef6/fundamentals/configuring/code-based.md).</span></span> <span data-ttu-id="7851c-116">這項功能可以用於搭配自動重試於 EF6，我們引進了可在其中交易實際上無法認可對伺服器造成暫時性失敗的情況下幫助：</span><span class="sxs-lookup"><span data-stu-id="7851c-116">This feature can be used in combination with the automatic retries we introduced in EF6, which help in the situation in which the transaction actually failed to commit on the server due to a transient failure:</span></span>  

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

## <a name="how-transactions-are-tracked"></a><span data-ttu-id="7851c-117">如何追蹤交易</span><span class="sxs-lookup"><span data-stu-id="7851c-117">How transactions are tracked</span></span>  

<span data-ttu-id="7851c-118">啟用此功能時，EF 會自動加入新的資料表名的資料庫 **__Transactions**。</span><span class="sxs-lookup"><span data-stu-id="7851c-118">When the feature is enabled, EF will automatically add a new table to the database called **__Transactions**.</span></span> <span data-ttu-id="7851c-119">此資料表中插入新的資料列，每次 EF 所建立的交易，並在認可期間發生交易失敗時，該資料列會檢查存在。</span><span class="sxs-lookup"><span data-stu-id="7851c-119">A new row is inserted in this table every time a transaction is created by EF and that row is checked for existence if a transaction failure occurs during commit.</span></span>  

<span data-ttu-id="7851c-120">雖然 EF 就盡全力來剪除資料表中的資料列，當不再需要它們時，資料表可以成長，如果過早，因此您可能需要清除的資料表，以手動方式在某些情況下，應用程式結束。</span><span class="sxs-lookup"><span data-stu-id="7851c-120">Although EF will do a best effort to prune rows from the table when they aren’t needed anymore, the table can grow if the application exits prematurely and for that reason you may need to purge the table manually in some cases.</span></span>  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a><span data-ttu-id="7851c-121">如何處理與舊版的認可失敗</span><span class="sxs-lookup"><span data-stu-id="7851c-121">How to handle commit failures with previous Versions</span></span>

<span data-ttu-id="7851c-122">EF 6.1 之前沒有機制可用來處理 EF 產品中的認可失敗。</span><span class="sxs-lookup"><span data-stu-id="7851c-122">Before EF 6.1 there was not mechanism to handle commit failures in the EF product.</span></span> <span data-ttu-id="7851c-123">有數種方式來處理這種情況下，可以套用到舊版的 EF6:</span><span class="sxs-lookup"><span data-stu-id="7851c-123">There are several ways to dealing with this situation that can be applied to previous versions of EF6:</span></span>  

* <span data-ttu-id="7851c-124">選項 1-不執行任何動作</span><span class="sxs-lookup"><span data-stu-id="7851c-124">Option 1 - Do nothing</span></span>  

  <span data-ttu-id="7851c-125">在交易認可期間連線失敗的可能性很低，因此可能會接受您的應用程式如果實際上會發生此狀況就會失敗。</span><span class="sxs-lookup"><span data-stu-id="7851c-125">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>  

* <span data-ttu-id="7851c-126">選項 2-使用資料庫來重設狀態</span><span class="sxs-lookup"><span data-stu-id="7851c-126">Option 2 - Use the database to reset state</span></span>  

  1. <span data-ttu-id="7851c-127">捨棄目前的 DbContext</span><span class="sxs-lookup"><span data-stu-id="7851c-127">Discard the current DbContext</span></span>  
  2. <span data-ttu-id="7851c-128">建立新的 DbContext，並從資料庫中還原應用程式的狀態</span><span class="sxs-lookup"><span data-stu-id="7851c-128">Create a new DbContext and restore the state of your application from the database</span></span>  
  3. <span data-ttu-id="7851c-129">通知使用者，最後一個作業可能無法順利完成</span><span class="sxs-lookup"><span data-stu-id="7851c-129">Inform the user that the last operation might not have been completed successfully</span></span>  

* <span data-ttu-id="7851c-130">選項 3-手動追蹤交易</span><span class="sxs-lookup"><span data-stu-id="7851c-130">Option 3 - Manually track the transaction</span></span>  

  1. <span data-ttu-id="7851c-131">加入用來追蹤交易狀態的資料庫中的非追蹤資料表。</span><span class="sxs-lookup"><span data-stu-id="7851c-131">Add a non-tracked table to the database used to track the status of the transactions.</span></span>  
  2. <span data-ttu-id="7851c-132">將資料列插入資料表中每一筆交易的開頭。</span><span class="sxs-lookup"><span data-stu-id="7851c-132">Insert a row into the table at the beginning of each transaction.</span></span>  
  3. <span data-ttu-id="7851c-133">如果連線失敗的認可，則會檢查資料庫中對應的資料列存在。</span><span class="sxs-lookup"><span data-stu-id="7851c-133">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>  
     - <span data-ttu-id="7851c-134">如果資料列存在時，會繼續正常執行，因為交易已成功認可</span><span class="sxs-lookup"><span data-stu-id="7851c-134">If the row is present, continue normally, as the transaction was committed successfully</span></span>  
     - <span data-ttu-id="7851c-135">如果資料列不存在，請重試目前的作業使用執行策略。</span><span class="sxs-lookup"><span data-stu-id="7851c-135">If the row is absent, use an execution strategy to retry the current operation.</span></span>  
  4. <span data-ttu-id="7851c-136">如果認可成功，刪除對應的資料列，以避免資料表的成長。</span><span class="sxs-lookup"><span data-stu-id="7851c-136">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>  

<span data-ttu-id="7851c-137">[此部落格文章](http://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx)包含範例程式碼在 SQL Azure 上完成這項作業。</span><span class="sxs-lookup"><span data-stu-id="7851c-137">[This blog post](http://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx) contains sample code for accomplishing this on SQL Azure.</span></span>  
