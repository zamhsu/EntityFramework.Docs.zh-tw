---
title: 連接恢復功能的 EF Core
author: rowanmiller
ms.author: divega
ms.date: 11/15/2016
ms.assetid: e079d4af-c455-4a14-8e15-a8471516d748
ms.technology: entity-framework-core
uid: core/miscellaneous/connection-resiliency
ms.openlocfilehash: 34ca1908257ed5544f2e134fa7686c9802fcebea
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37949293"
---
# <a name="connection-resiliency"></a>連接恢復功能

連接恢復功能會自動重試失敗的資料庫命令。 此功能可以搭配任何資料庫，藉由提供"執行策略 」，封裝的邏輯才能偵測到失敗，然後重試命令。 EF Core 提供者可以提供專門針對其特定的資料庫失敗狀況和最佳的重試原則的執行策略。

例如，SQL Server 提供者會包含專為 SQL Server （包括 SQL Azure） 的執行策略。 它會知道可以重試例外狀況類型，並有合理的預設值的最大重試，重試等之間的延遲。

設定您內容的選項時，將指定的執行策略。 這是通常位於`OnConfiguring`方法在衍生的內容中，或在`Startup.cs`ASP.NET Core 應用程式。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#OnConfiguring)]

## <a name="custom-execution-strategy"></a>自訂的執行策略

沒有註冊您自己，如果您想要變更預設值的任何自訂的執行策略的機制。

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseMyProvider(
            "<connection string>",
            options => options.ExecutionStrategy(...));
}
```

## <a name="execution-strategies-and-transactions"></a>執行策略和交易

會自動重試失敗的執行策略必須要能夠在失敗時重試區塊播放每個作業。 重試啟用時，您透過 EF Core 執行每個作業都會變成其本身可重試作業。 也就是說，每個查詢和每次呼叫`SaveChanges()`將重試做為一個單位發生暫時性失敗。

不過，如果您的程式碼會啟動交易，使用`BeginTransaction()`您正在定義您自己的群組作業，也必須被視為一個單位，而且在交易內的所有項目則需要播放應該會發生失敗。 如果您嘗試這樣做，使用執行策略時，您會收到類似下列的例外狀況：

> InvalidOperationException： 設定的執行策略 'SqlServerRetryingExecutionStrategy' 不支援使用者起始的異動。 使用 'DbContext.Database.CreateExecutionStrategy()' 所傳回的執行策略，將異動中的所有作業當做一個可重試的單位來執行。

解決方法是以代表所有項目需要執行的委派，以手動方式叫用的執行策略。 如果發生暫時性失敗，執行策略會再叫用委派一次。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#ManualTransaction)]

## <a name="transaction-commit-failure-and-the-idempotency-issue"></a>交易認可失敗且等冪性問題

一般情況下，連接失敗時目前交易已回復。 不過，如果交易時，會中斷連線的認可產生交易的狀態不明。 請參閱此[部落格文章](http://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx)如需詳細資訊。

根據預設，執行策略會重試此作業視為交易已回復，但如果不是這會導致例外狀況如果新的資料庫狀態不相容，或可能會導致**資料損毀**如果作業不需要特定的狀態，例如，當插入新的資料列，以自動產生索引鍵的值。

有數種方式來解決這個問題。

### <a name="option-1---do-almost-nothing"></a>選項 1-執行 （幾乎） 任何項目

在交易認可期間連線失敗的可能性很低，因此可能會接受您的應用程式如果實際上會發生此狀況就會失敗。

不過，您需要避免使用存放區產生的索引鍵，以確保例外狀況會擲回，而不是新增重複的資料列。 請考慮使用用戶端產生的 GUID 值或用戶端值產生器。

### <a name="option-2---rebuild-application-state"></a>選項 2-重建應用程式狀態

1. 捨棄目前`DbContext`。
2. 建立新`DbContext`並還原您的應用程式的狀態從資料庫。
3. 通知使用者，最後一個作業可能無法順利完成。

### <a name="option-3---add-state-verification"></a>選項 3-新增狀態驗證

適用於大部分的作業，變更資料庫狀態，就可以新增會檢查它是否成功的程式碼。 EF 提供擴充方法更加容易- `IExecutionStrategy.ExecuteInTransaction`。

這個方法會開始及認可交易，也接受中的函式`verifySucceeded`在交易認可期間發生暫時性錯誤時叫用的參數。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Verification)]

> [!NOTE]
> 以下`SaveChanges`叫用`acceptAllChangesOnSuccess`設為`false`若要避免變更的狀態`Blog`實體`Unchanged`如果`SaveChanges`成功。 這可讓重試相同的作業，如果認可失敗，且會回復交易。

### <a name="option-4---manually-track-the-transaction"></a>選項 4-手動追蹤交易

如果您需要使用存放區所產生的金鑰，或需要一般的方式來處理認可失敗不會相依於執行的作業，每一筆交易，可以指派認可失敗時，會檢查的識別碼。

1. 將資料表加入用來追蹤交易狀態的資料庫。
2. 將資料列插入資料表中每一筆交易的開頭。
3. 如果連線失敗的認可，則會檢查資料庫中對應的資料列存在。
4. 如果認可成功，刪除對應的資料列，以避免資料表的成長。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Tracking)]

> [!NOTE]
> 請確定用來驗證的內容具有已定義連接時可能會失敗一次在驗證期間，如果它無法在交易認可時執行策略。
