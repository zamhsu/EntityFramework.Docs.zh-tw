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
# <a name="handling-transaction-commit-failures"></a>處理交易提交失敗
> [!NOTE]
> **僅 EF6.1 繼續**- 本頁中討論的功能、API 等在實體框架 6.1 中介紹。 如果您使用的是較早版本，則不適用部分或全部的資訊。  

作為 6.1 的一部分,我們引入了 EF 的新連接恢復功能:當瞬態連接故障影響事務提交確認時自動檢測和恢復的能力。 場景的完整細節最好在博客文章[SQL 資料庫連接和陽萎問題](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue)中描述。  總之,當在事務提交期間引發異常時,有兩個可能的原因:  

1. 伺服器上的交易提交失敗
2. 交易提交在伺服器上成功,但連接問題阻止成功通知到達用戶端  

當第一種情況發生時,應用程式或使用者可以重試操作,但當第二種情況發生時,應避免重試,並且應用程式可以自動恢復。 挑戰在於,如果沒有能力檢測在提交過程中報告異常的實際原因,應用程式無法選擇正確的操作方案。 EF 6.1 中的新功能允許 EF 在事務成功時與資料庫進行仔細檢查,並透明地執行正確的操作過程。  

## <a name="using-the-feature"></a>使用此功能  

為了啟用該功能,您需要在**Db 配置**的建構函數中調用[SetTransactionHandler。](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) 如果您不熟悉 Db**設定**,請參考[基於代碼的設定](~/ef6/fundamentals/configuring/code-based.md)。 此功能可與我們在 EF6 中引入的自動重試結合使用,這有助於在事務因暫時性故障而實際無法在伺服器上提交的情況:  

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

## <a name="how-transactions-are-tracked"></a>如何追蹤交易記錄  

啟用此功能後,EF 將自動向名為 **__Transactions**的資料庫添加新錶。 每次 EF 創建事務時,都會在此表中插入一個新行,如果提交期間發生事務失敗,則檢查該行是否存在。  

儘管 EF 將盡力在不再需要表時修剪行,但如果應用程式過早退出,則該表可能會增長,因此在某些情況下您可能需要手動清除表。  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a>如何處理具有早期版本的提交失敗

在 EF 6.1 之前,沒有處理 EF 產品中的提交故障的機制。 有幾種方法可以處理這種情況,可以應用於 EF6 的早期版本:  

* 選項 1 - 不執行任何操作  

  事務提交期間連接失敗的可能性很低,因此,如果實際發生此情況,應用程式可能會失敗。  

* 選項 2 - 使用資料庫重設狀態  

  1. 放棄目前資料庫中文  
  2. 建立新的 DbContext 並從資料庫回復應用程式的狀態  
  3. 通知使用者上次操作可能尚未成功完成  

* 選項 3 - 手動追蹤交易  

  1. 將非追蹤表添加到用於追蹤事務狀態的資料庫。  
  2. 在每個事務開始時將一行插入到表中。  
  3. 如果在提交過程中連接失敗,請檢查資料庫中是否存在相應的行。  
     - 如果行存在,請正常繼續,因為事務已成功提交  
     - 如果行不存在,請使用執行策略重試當前操作。  
  4. 如果提交成功,請刪除相應的行以避免表的增長。  

[此部落格文章](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue)包含用於在 SQL Azure 上完成此任務的範例代碼。  
