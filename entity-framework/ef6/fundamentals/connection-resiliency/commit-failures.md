---
title: 處理交易認可失敗 EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 5b1f7a7d-1b24-4645-95ec-5608a31ef577
caps.latest.revision: 3
ms.openlocfilehash: 95ac69a005a70f31086bd4d3c0088bd3e82d28e2
ms.sourcegitcommit: 390f3a37bc55105ed7cc5b0e0925b7f9c9e80ba6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2018
ms.locfileid: "39120459"
---
# <a name="handling-transaction-commit-failures"></a>處理交易認可失敗
> [!NOTE]
> **EF6.1 及更新版本僅**-功能、 Api、 Entity Framework 6.1 中導入等本頁所述。 如果您使用的是較早版本，則不適用部分或全部的資訊。  

6.1 的過程中，我們引進新的連接恢復功能的 EF： 偵測和暫時性的連線失敗影響認可的交易認可時自動復原的能力。 此案例的完整詳細資料最中所述的部落格文章[SQL Database 連接性 」 和 「 等冪性問題](http://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx)。  在 [摘要] 的案例是當交易認可期間引發例外狀況有兩個可能的原因：  

1. 伺服器上的交易認可失敗
2. 在伺服器上的交易認可成功，但有連線問題導致無法成功通知，使其無法到達用戶端  

第一種情況發生在應用程式或使用者可以重試此作業，但第二種情況發生時應避免重試次數和應用程式無法自動復原。 所面臨的挑戰是動作的，而不需要偵測哪些項目為已回報例外狀況認可、 應用程式無法選擇正確採取的實際原因的能力。 EF 6.1 」 中的新功能可讓 EF 來與資料庫再次檢查，如果交易成功且無障礙地採取正確採取的動作。  

## <a name="using-the-feature"></a>使用功能  

若要啟用此功能，您需要包含呼叫[SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx)建構函式中您**DbConfiguration**。 如果您熟悉**DbConfiguration**，請參閱[程式碼基礎組態](~/ef6/fundamentals/configuring/code-based.md)。 這項功能可以用於搭配自動重試於 EF6，我們引進了可在其中交易實際上無法認可對伺服器造成暫時性失敗的情況下幫助：  

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

啟用此功能時，EF 會自動加入新的資料表名的資料庫 **__Transactions**。 此資料表中插入新的資料列，每次 EF 所建立的交易，並在認可期間發生交易失敗時，該資料列會檢查存在。  

雖然 EF 就盡全力來剪除資料表中的資料列，當不再需要它們時，資料表可以成長，如果過早，因此您可能需要清除的資料表，以手動方式在某些情況下，應用程式結束。  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a>如何處理與舊版的認可失敗

EF 6.1 之前沒有機制可用來處理 EF 產品中的認可失敗。 有數種方式來處理這種情況下，可以套用到舊版的 EF6:  

### <a name="option-1---do-nothing"></a>選項 1-不執行任何動作  

在交易認可期間連線失敗的可能性很低，因此可能會接受您的應用程式如果實際上會發生此狀況就會失敗。  

## <a name="option-2---use-the-database-to-reset-state"></a>選項 2-使用資料庫來重設狀態  

1. 捨棄目前的 DbContext  
2. 建立新的 DbContext，並從資料庫中還原應用程式的狀態  
3. 通知使用者，最後一個作業可能無法順利完成  

## <a name="option-3---manually-track-the-transaction"></a>選項 3-手動追蹤交易  

1. 加入用來追蹤交易狀態的資料庫中的非追蹤資料表。  
2. 將資料列插入資料表中每一筆交易的開頭。  
3. 如果連線失敗的認可，則會檢查資料庫中對應的資料列存在。  
    - 如果資料列存在時，會繼續正常執行，因為交易已成功認可  
    - 如果資料列不存在，請重試目前的作業使用執行策略。  
4. 如果認可成功，刪除對應的資料列，以避免資料表的成長。  

[此部落格文章](http://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx)包含範例程式碼在 SQL Azure 上完成這項作業。  
