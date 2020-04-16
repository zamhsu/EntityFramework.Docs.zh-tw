---
title: 處理交易提交失敗 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 5b1f7a7d-1b24-4645-95ec-5608a31ef577
ms.openlocfilehash: cf2722496e207a8ecaa9cfaa4ca61e7248e5e58f
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434132"
---
# <a name="handling-transaction-commit-failures"></a><span data-ttu-id="16c9d-102">處理交易提交失敗</span><span class="sxs-lookup"><span data-stu-id="16c9d-102">Handling transaction commit failures</span></span>
> [!NOTE]
> <span data-ttu-id="16c9d-103">**僅 EF6.1 繼續**- 本頁中討論的功能、API 等在實體框架 6.1 中介紹。</span><span class="sxs-lookup"><span data-stu-id="16c9d-103">**EF6.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="16c9d-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="16c9d-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="16c9d-105">作為 6.1 的一部分,我們引入了 EF 的新連接恢復功能:當瞬態連接故障影響事務提交確認時自動檢測和恢復的能力。</span><span class="sxs-lookup"><span data-stu-id="16c9d-105">As part of 6.1 we are introducing a new connection resiliency feature for EF: the ability to detect and recover automatically when transient connection failures affect the acknowledgement of transaction commits.</span></span> <span data-ttu-id="16c9d-106">場景的完整細節最好在博客文章[SQL 資料庫連接和陽萎問題](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue)中描述。</span><span class="sxs-lookup"><span data-stu-id="16c9d-106">The full details of the scenario are best described in the blog post [SQL Database Connectivity and the Idempotency Issue](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue).</span></span>  <span data-ttu-id="16c9d-107">總之,當在事務提交期間引發異常時,有兩個可能的原因:</span><span class="sxs-lookup"><span data-stu-id="16c9d-107">In summary, the scenario is that when an exception is raised during a transaction commit there are two possible causes:</span></span>  

1. <span data-ttu-id="16c9d-108">伺服器上的交易提交失敗</span><span class="sxs-lookup"><span data-stu-id="16c9d-108">The transaction commit failed on the server</span></span>
2. <span data-ttu-id="16c9d-109">交易提交在伺服器上成功,但連接問題阻止成功通知到達用戶端</span><span class="sxs-lookup"><span data-stu-id="16c9d-109">The transaction commit succeeded on the server but a connectivity issue prevented the success notification from reaching the client</span></span>  

<span data-ttu-id="16c9d-110">當第一種情況發生時,應用程式或使用者可以重試操作,但當第二種情況發生時,應避免重試,並且應用程式可以自動恢復。</span><span class="sxs-lookup"><span data-stu-id="16c9d-110">When the first situation happens the application or the user can retry the operation, but when the second situation occurs retries should be avoided and the application could recover automatically.</span></span> <span data-ttu-id="16c9d-111">挑戰在於,如果沒有能力檢測在提交過程中報告異常的實際原因,應用程式無法選擇正確的操作方案。</span><span class="sxs-lookup"><span data-stu-id="16c9d-111">The challenge is that without the ability to detect what was the actual reason an exception was reported during commit, the application cannot choose the right course of action.</span></span> <span data-ttu-id="16c9d-112">EF 6.1 中的新功能允許 EF 在事務成功時與資料庫進行仔細檢查,並透明地執行正確的操作過程。</span><span class="sxs-lookup"><span data-stu-id="16c9d-112">The new feature in EF 6.1 allows EF to double-check with the database if the transaction succeeded and take the right course of action transparently.</span></span>  

## <a name="using-the-feature"></a><span data-ttu-id="16c9d-113">使用此功能</span><span class="sxs-lookup"><span data-stu-id="16c9d-113">Using the feature</span></span>  

<span data-ttu-id="16c9d-114">為了啟用該功能,您需要在**Db 配置**的建構函數中調用[SetTransactionHandler。](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx)</span><span class="sxs-lookup"><span data-stu-id="16c9d-114">In order to enable the feature you need include a call to [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) in the constructor of your **DbConfiguration**.</span></span> <span data-ttu-id="16c9d-115">如果您不熟悉 Db**設定**,請參考[基於代碼的設定](~/ef6/fundamentals/configuring/code-based.md)。</span><span class="sxs-lookup"><span data-stu-id="16c9d-115">If you are unfamiliar with **DbConfiguration**, see [Code Based Configuration](~/ef6/fundamentals/configuring/code-based.md).</span></span> <span data-ttu-id="16c9d-116">此功能可與我們在 EF6 中引入的自動重試結合使用,這有助於在事務因暫時性故障而實際無法在伺服器上提交的情況:</span><span class="sxs-lookup"><span data-stu-id="16c9d-116">This feature can be used in combination with the automatic retries we introduced in EF6, which help in the situation in which the transaction actually failed to commit on the server due to a transient failure:</span></span>  

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

## <a name="how-transactions-are-tracked"></a><span data-ttu-id="16c9d-117">如何追蹤交易記錄</span><span class="sxs-lookup"><span data-stu-id="16c9d-117">How transactions are tracked</span></span>  

<span data-ttu-id="16c9d-118">啟用此功能後,EF 將自動向名為 **__Transactions**的資料庫添加新錶。</span><span class="sxs-lookup"><span data-stu-id="16c9d-118">When the feature is enabled, EF will automatically add a new table to the database called **__Transactions**.</span></span> <span data-ttu-id="16c9d-119">每次 EF 創建事務時,都會在此表中插入一個新行,如果提交期間發生事務失敗,則檢查該行是否存在。</span><span class="sxs-lookup"><span data-stu-id="16c9d-119">A new row is inserted in this table every time a transaction is created by EF and that row is checked for existence if a transaction failure occurs during commit.</span></span>  

<span data-ttu-id="16c9d-120">儘管 EF 將盡力在不再需要表時修剪行,但如果應用程式過早退出,則該表可能會增長,因此在某些情況下您可能需要手動清除表。</span><span class="sxs-lookup"><span data-stu-id="16c9d-120">Although EF will do a best effort to prune rows from the table when they aren’t needed anymore, the table can grow if the application exits prematurely and for that reason you may need to purge the table manually in some cases.</span></span>  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a><span data-ttu-id="16c9d-121">如何處理具有早期版本的提交失敗</span><span class="sxs-lookup"><span data-stu-id="16c9d-121">How to handle commit failures with previous Versions</span></span>

<span data-ttu-id="16c9d-122">在 EF 6.1 之前,沒有處理 EF 產品中的提交故障的機制。</span><span class="sxs-lookup"><span data-stu-id="16c9d-122">Before EF 6.1 there was not mechanism to handle commit failures in the EF product.</span></span> <span data-ttu-id="16c9d-123">有幾種方法可以處理這種情況,可以應用於 EF6 的早期版本:</span><span class="sxs-lookup"><span data-stu-id="16c9d-123">There are several ways to dealing with this situation that can be applied to previous versions of EF6:</span></span>  

* <span data-ttu-id="16c9d-124">選項 1 - 不執行任何操作</span><span class="sxs-lookup"><span data-stu-id="16c9d-124">Option 1 - Do nothing</span></span>  

  <span data-ttu-id="16c9d-125">事務提交期間連接失敗的可能性很低,因此,如果實際發生此情況,應用程式可能會失敗。</span><span class="sxs-lookup"><span data-stu-id="16c9d-125">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>  

* <span data-ttu-id="16c9d-126">選項 2 - 使用資料庫重設狀態</span><span class="sxs-lookup"><span data-stu-id="16c9d-126">Option 2 - Use the database to reset state</span></span>  

  1. <span data-ttu-id="16c9d-127">放棄目前資料庫中文</span><span class="sxs-lookup"><span data-stu-id="16c9d-127">Discard the current DbContext</span></span>  
  2. <span data-ttu-id="16c9d-128">建立新的 DbContext 並從資料庫回復應用程式的狀態</span><span class="sxs-lookup"><span data-stu-id="16c9d-128">Create a new DbContext and restore the state of your application from the database</span></span>  
  3. <span data-ttu-id="16c9d-129">通知使用者上次操作可能尚未成功完成</span><span class="sxs-lookup"><span data-stu-id="16c9d-129">Inform the user that the last operation might not have been completed successfully</span></span>  

* <span data-ttu-id="16c9d-130">選項 3 - 手動追蹤交易</span><span class="sxs-lookup"><span data-stu-id="16c9d-130">Option 3 - Manually track the transaction</span></span>  

  1. <span data-ttu-id="16c9d-131">將非追蹤表添加到用於追蹤事務狀態的資料庫。</span><span class="sxs-lookup"><span data-stu-id="16c9d-131">Add a non-tracked table to the database used to track the status of the transactions.</span></span>  
  2. <span data-ttu-id="16c9d-132">在每個事務開始時將一行插入到表中。</span><span class="sxs-lookup"><span data-stu-id="16c9d-132">Insert a row into the table at the beginning of each transaction.</span></span>  
  3. <span data-ttu-id="16c9d-133">如果在提交過程中連接失敗,請檢查資料庫中是否存在相應的行。</span><span class="sxs-lookup"><span data-stu-id="16c9d-133">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>  
     - <span data-ttu-id="16c9d-134">如果行存在,請正常繼續,因為事務已成功提交</span><span class="sxs-lookup"><span data-stu-id="16c9d-134">If the row is present, continue normally, as the transaction was committed successfully</span></span>  
     - <span data-ttu-id="16c9d-135">如果行不存在,請使用執行策略重試當前操作。</span><span class="sxs-lookup"><span data-stu-id="16c9d-135">If the row is absent, use an execution strategy to retry the current operation.</span></span>  
  4. <span data-ttu-id="16c9d-136">如果提交成功,請刪除相應的行以避免表的增長。</span><span class="sxs-lookup"><span data-stu-id="16c9d-136">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>  

<span data-ttu-id="16c9d-137">[此部落格文章](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue)包含用於在 SQL Azure 上完成此任務的範例代碼。</span><span class="sxs-lookup"><span data-stu-id="16c9d-137">[This blog post](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) contains sample code for accomplishing this on SQL Azure.</span></span>  
