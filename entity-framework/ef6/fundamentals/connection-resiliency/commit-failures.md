---
title: 處理交易認可失敗-EF6
description: 處理 Entity Framework 6 中的交易認可失敗
author: divega
ms.date: 10/23/2016
ms.assetid: 5b1f7a7d-1b24-4645-95ec-5608a31ef577
uid: ef6/fundamentals/connection-resiliency/commit-failures
ms.openlocfilehash: 10cb67837264bea37a0621aa078e1753af954c2f
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616287"
---
# <a name="handling-transaction-commit-failures"></a>處理交易認可失敗

> [!NOTE]
> **僅限 ef 6.1 （僅限** ），本頁面所討論的功能、api 等已于 Entity Framework 6.1 中引進。 如果您使用的是較早版本，則不適用部分或全部的資訊。  

在6.1 中，我們引進了 EF 的新連接復原功能：當暫時性連接失敗影響交易認可的認可時，能夠自動偵測和復原。 有關此案例的完整詳細資料，請參閱 [SQL Database 連線能力和等冪性問題](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue)的 blog 文章。  總而言之，案例是在交易認可期間引發例外狀況時，有兩個可能的原因：  

1. 伺服器上的交易認可失敗
2. 交易認可在伺服器上成功，但連線能力問題導致成功通知無法抵達用戶端  

當第一次發生問題時，應用程式或使用者可以重試此作業，但在第二種情況下，應該避免重試，而且應用程式可以自動復原。 這項挑戰是，若沒有偵測在認可期間回報例外狀況的實際原因，應用程式就無法選擇正確的動作。 EF 6.1 中的新功能可讓 EF 在交易成功時再次檢查資料庫，並以透明的方式採取正確的動作。  

## <a name="using-the-feature"></a>使用此功能  

若要啟用您需要的功能，請在 **>dbconfiguration**的函式中加入[SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx)的呼叫。 如果您不熟悉 **>dbconfiguration**，請參閱以程式 [代碼為基礎](xref:ef6/fundamentals/configuring/code-based)的設定。 這項功能可與我們在 EF6 中引進的自動重試搭配使用，這有助於在交易實際因暫時性失敗而無法在伺服器上認可的情況下：  

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

## <a name="how-transactions-are-tracked"></a>如何追蹤交易  

啟用此功能時，EF 會自動將新的資料表加入至名為 **__Transactions**的資料庫。 每次由 EF 建立交易時，就會在此資料表中插入新的資料列，而且在認可期間發生交易失敗時，會檢查該資料列是否存在。  

雖然 EF 在不再需要資料表時，會盡力剪除資料表中的資料列，但如果應用程式提前結束，而且您可能需要在某些情況下手動清除資料表，則資料表可能會成長。  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a>如何處理舊版的認可失敗

EF 6.1 之前沒有機制可處理 EF 產品中的認可失敗。 有幾種方式可以處理此情況，以套用至舊版的 EF6：  

* 選項 1-不執行任何動作  

  在交易認可期間連接失敗的可能性很低，因此您的應用程式可能會在此情況真的發生時失敗。  

* 選項 2-使用資料庫來重設狀態  

  1. 捨棄目前的 DbCoNtext  
  2. 建立新的 DbCoNtext，並從資料庫還原應用程式的狀態  
  3. 通知使用者上一次作業可能未成功完成  

* 選項 3-手動追蹤交易  

  1. 將非追蹤的資料表加入至用來追蹤交易狀態的資料庫。  
  2. 在每個交易開始時，將資料列插入資料表中。  
  3. 如果在認可期間連接失敗，請檢查資料庫中對應的資料列是否存在。  
     * 如果資料列存在，請在交易認可成功時繼續正常執行  
     * 如果資料列不存在，請使用執行策略來重試目前的作業。  
  4. 如果認可成功，請刪除對應的資料列，以避免資料表的成長。  

[此 blog 文章](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) 包含在 SQL Azure 上完成此操作的範例程式碼。  
