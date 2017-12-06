---
title: "連接恢復功能的 EF 核心"
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
---
# <a name="connection-resiliency"></a>連接恢復功能

連接恢復功能會自動重試失敗的資料庫命令。 此功能可以搭配任何資料庫，藉由提供的 「 執行策略"，封裝偵測失敗，然後重試命令所需的邏輯。 EF 核心提供者可以提供專門針對其特定的資料庫失敗狀況和最佳的重試原則的執行策略。

例如，SQL Server 提供者會包含專為 SQL Server （包括 SQL Azure） 的執行策略。 它可感知重試的例外狀況類型，而且有合理的預設值的最大重試，重試次數等之間的延遲。

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

會自動重試失敗的執行策略必須失敗的重試區塊中播放每個作業。 重試啟用時，您透過 EF 核心執行每項作業會成為它自己的重試作業，也就是每個查詢，每次呼叫`SaveChanges()`將重試做為一個單位發生暫時性失敗。

不過，如果您的程式碼會啟動交易，使用`BeginTransaction()`您正在定義您自己的作業需要被視為一個單位的群組，也就是在交易內的所有項目需要用來播放應該發生失敗。 如果您嘗試這樣做，使用的執行策略時，您會收到類似下面的例外狀況。

> InvalidOperationException： 設定的執行策略 'SqlServerRetryingExecutionStrategy' 不支援使用者起始的交易。 在做為可重試單位的交易中執行所有作業中使用 'DbContext.Database.CreateExecutionStrategy()' 所傳回的執行策略。

解決方法是手動叫用的委派代表的所有項目需要執行的執行策略。 如果發生暫時性中斷，執行策略會叫用委派一次。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#ManualTransaction)]

## <a name="transaction-commit-failure-and-the-idempotency-issue"></a>交易認可失敗，以及等冪性問題

一般情況下，當連線失敗目前交易已回復。 不過，如果交易時，會中斷連線將會認可所產生的交易的狀態不明。 請參閱此[部落格文章](http://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx)如需詳細資訊。

根據預設，執行策略會重試作業視為已回復交易，但如果不是這會導致例外狀況，如果新的資料庫狀態不相容，或可能會導致**資料損毀**如果作業不會依賴特定的狀態，例如，當插入新的資料列，以自動產生索引鍵的值。

有數種方式可解決這個問題。

### <a name="option-1---do-almost-nothing"></a>選項 1-不要 nothing （幾乎）

因此，可能會接受您的應用程式如果確實發生這種情況只會失敗，交易認可期間連線失敗的可能性會很低。

不過，您需要避免使用存放區產生的金鑰，才能確保例外狀況會擲回，而非新增重複的資料列。 請考慮使用用戶端產生的 GUID 值或用戶端值產生器。

### <a name="option-2---rebuild-application-state"></a>選項 2-重建應用程式狀態

1. 捨棄目前`DbContext`。
2. 建立新`DbContext`，並從資料庫還原應用程式的狀態。
3. 通知使用者，最後一項作業可能無法順利完成。

### <a name="option-3---add-state-verification"></a>選項 3-新增狀態驗證

對於大多數的變更資料庫狀態的作業是可以加入程式碼，檢查是否成功。 EF 提供擴充方法來進行簡化- `IExecutionStrategy.ExecuteInTransaction`。

這個方法會開始及認可交易，也接受中的函式`verifySucceeded`暫時性錯誤發生的交易認可時，會叫用的參數。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Verification)]

> [!NOTE]
> 這裡`SaveChanges`用來叫用`acceptAllChangesOnSuccess`設`false`若要避免變更的狀態`Blog`實體`Unchanged`如果`SaveChanges`成功。 這可讓則認可會失敗並回復交易時，重試相同的作業。

### <a name="option-4---manually-track-the-transaction"></a>選項 4-手動追蹤的交易

如果您需要使用存放區產生的索引鍵，或需要執行的作業不相依的處理認可失敗的泛型方法的每個交易無法指派則認可會失敗時，會檢查的識別碼。

1. 將資料表加入至資料庫用來追蹤交易狀態。
2. 將資料列插入資料表中每個交易的開頭。
3. 如果連接失敗的認可，請檢查資料庫中對應的資料列存在。
4. 如果認可成功，刪除對應的資料列，以避免資料表成長。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Tracking)]

> [!NOTE]
> 請確定用於驗證的內容具有已定義連接時可能會失敗一次在驗證期間，如果它無法在交易認可時的執行策略。
