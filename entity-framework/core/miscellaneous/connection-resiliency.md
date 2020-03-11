---
title: 連接恢復功能-EF Core
author: rowanmiller
ms.date: 11/15/2016
ms.assetid: e079d4af-c455-4a14-8e15-a8471516d748
uid: core/miscellaneous/connection-resiliency
ms.openlocfilehash: 07646e6ead845c38537945a03367ac7f50784236
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416638"
---
# <a name="connection-resiliency"></a>連接恢復功能

連接恢復功能會自動重試失敗的資料庫命令。 這項功能可與任何資料庫搭配使用，方法是提供「執行策略」，以封裝偵測失敗和重試命令所需的邏輯。 EF Core 提供者可以針對其特定資料庫失敗狀況和最佳的重試原則，提供量身打造的執行策略。

例如，SQL Server 提供者包含專為 SQL Server （包括 SQL Azure）量身打造的執行策略。 它知道可以重試的例外狀況類型，而且有合理的重試次數、重試之間的延遲等等。

設定內容的選項時，會指定執行策略。 這通常是在您衍生內容的 `OnConfiguring` 方法中：

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#OnConfiguring)]

或在 ASP.NET Core 應用程式的 `Startup.cs` 中：

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<PicnicContext>(
        options => options.UseSqlServer(
            "<connection string>",
            providerOptions => providerOptions.EnableRetryOnFailure()));
}
```

## <a name="custom-execution-strategy"></a>自訂執行策略

如果您想要變更任何預設值，有一種機制可以註冊您自己的自訂執行策略。

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

自動重試失敗的執行策略必須能夠在失敗的重試區塊中播放每個作業。 當啟用重試時，您透過 EF Core 執行的每項作業都會變成其本身的可重試作業。 也就是說，如果發生暫時性失敗，每個查詢和每個 `SaveChanges()` 呼叫都會以一個單位來重試。

不過，如果您的程式碼使用 `BeginTransaction()` 來起始交易，而您要定義自己的作業群組，而這些作業必須視為一個單位來處理，則交易內的所有專案都必須重新播放，才會發生失敗。 如果您在使用執行策略時嘗試這麼做，您會收到類似下面的例外狀況：

> InvalidOperationException：設定的執行策略 ' SqlServerRetryingExecutionStrategy ' 不支援使用者起始的交易。 使用 'DbContext.Database.CreateExecutionStrategy()' 所傳回的執行策略，將異動中的所有作業當做一個可重試的單位來執行。

解決方法是使用代表需要執行之所有專案的委派來手動叫用執行策略。 如果發生暫時性失敗，執行策略會再次叫用委派。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#ManualTransaction)]

這個方法也可以與環境交易搭配使用。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#AmbientTransaction)]

## <a name="transaction-commit-failure-and-the-idempotency-issue"></a>交易認可失敗和等冪性問題

一般而言，當發生連接失敗時，會回復目前的交易。 不過，如果在認可交易時中斷連接，則交易的產生狀態為 unknown。 

根據預設，執行策略會重試作業，就像交易已回復一樣，但如果不是，這會導致例外狀況（如果新的資料庫狀態不相容，或如果作業不依賴特定狀態，則可能會導致**資料損毀**），例如，在使用自動產生的索引鍵值插入新資料列時。

有數種方式可以處理這種情況。

### <a name="option-1---do-almost-nothing"></a>選項 1-Do （幾乎）無

在交易認可期間發生連接失敗的可能性很低，因此如果發生此情況，您的應用程式就可能會失敗。

不過，您必須避免使用存放區產生的索引鍵，以確保會擲回例外狀況，而不是新增重複的資料列。 請考慮使用用戶端產生的 GUID 值或用戶端值產生器。

### <a name="option-2---rebuild-application-state"></a>選項 2-重建應用程式狀態

1. 捨棄目前的 `DbContext`。
2. 建立新的 `DbContext`，並從資料庫還原應用程式的狀態。
3. 通知使用者最近一次作業可能未順利完成。

### <a name="option-3---add-state-verification"></a>選項 3-新增狀態驗證

對於大部分變更資料庫狀態的作業，可以加入程式碼來檢查是否成功。 EF 提供的擴充方法可讓您更輕鬆地 `IExecutionStrategy.ExecuteInTransaction`。

這個方法會開始並認可交易，也會接受在交易認可期間發生暫時性錯誤時所叫用之 `verifySucceeded` 參數中的函式。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Verification)]

> [!NOTE]
> 在這裡，會叫用 `acceptAllChangesOnSuccess` 設定為 `false` 的 `SaveChanges`，以避免在 `Unchanged` 成功時，將 `Blog` 實體的狀態變更為 [`SaveChanges`]。 如果認可失敗且交易已回復，這可讓重試相同的作業。

### <a name="option-4---manually-track-the-transaction"></a>選項 4-手動追蹤交易

如果您需要使用存放區產生的索引鍵，或需要一般方式來處理不相依于執行之作業的認可失敗，則可能會將認可失敗時所檢查的識別碼指派給每個交易。

1. 將資料表加入至用來追蹤交易狀態的資料庫。
2. 在每個交易開頭的資料表中插入一個資料列。
3. 如果在認可期間連接失敗，請檢查資料庫中對應的資料列是否存在。
4. 如果認可成功，請刪除對應的資料列，以避免資料表的成長。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Tracking)]

> [!NOTE]
> 請確定用於驗證的內容具有已定義的執行策略，因為如果在交易認可期間失敗，連接可能會在驗證期間再次失敗。
