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
# <a name="connection-resiliency"></a>連接恢復功能

連接恢復功能會自動重試失敗的資料庫命令。 這項功能可搭配任何資料庫使用，方法是提供「執行策略」，此策略會封裝偵測失敗和重試命令所需的邏輯。 EF Core 提供者可以針對其特定資料庫失敗狀況和最佳重試原則，提供量身訂做的執行策略。

例如，SQL Server 提供者包含專門針對 SQL Server (（包括 SQL Azure) ）量身打造的執行策略。 它知道可以重試的例外狀況類型，而且有合理的預設值可重試最大值、重試之間的延遲等等。

設定內容的選項時，會指定執行策略。 這通常會在 `OnConfiguring` 您衍生內容的方法中：

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#OnConfiguring)]

或中 `Startup.cs` 的 ASP.NET Core 應用程式：

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

如果您想要變更任何預設值，有一種機制可註冊您自己的自訂執行策略。

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

自動重試失敗的執行策略，必須能夠在失敗的重試區塊中播放每項作業。 啟用重試時，您透過 EF Core 執行的每個作業都會變成其本身的可重試作業。 也就是說，如果發生暫時性失敗，則每個查詢和每個呼叫 `SaveChanges()` 都會以一個單位來重試。

但是，如果您的程式碼使用您來起始交易 `BeginTransaction()` ，而您要定義一組需要視為一個單位的作業，而且必須播放交易內的所有專案，就會發生失敗。 如果您在使用執行策略時嘗試這麼做，就會收到類似下面的例外狀況：

> InvalidOperationException：設定的執行策略 ' SqlServerRetryingExecutionStrategy ' 不支援使用者起始的交易。 使用 'DbContext.Database.CreateExecutionStrategy()' 所傳回的執行策略，將異動中的所有作業當做一個可重試的單位來執行。

解決方法是使用委派（代表需要執行的所有專案）手動叫用執行策略。 如果發生暫時性失敗，執行策略會再次叫用委派。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#ManualTransaction)]

這種方法也可以搭配環境交易使用。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#AmbientTransaction)]

## <a name="transaction-commit-failure-and-the-idempotency-issue"></a>交易認可失敗和等冪性問題

一般情況下，當發生連接失敗時，就會回復目前的交易。 但是，如果在認可交易時中斷連接，則交易的結果狀態是未知的。 

根據預設，執行策略會重試此作業，就像交易已回復一樣，但如果不是如此，則如果新的資料庫狀態不相容，或如果作業不依賴特定狀態（例如，使用自動產生的索引鍵值插入新的資料列時），可能會導致 **資料損毀** ，這會導致例外狀況。

有幾種方式可以處理這種情況。

### <a name="option-1---do-almost-nothing"></a>選項 1- (幾乎) nothing

在交易認可期間連接失敗的可能性很低，因此您的應用程式可能會在此情況真的發生時失敗。

但是，您必須避免使用存放區產生的索引鍵，以確保會擲回例外狀況，而不是加入重複的資料列。 請考慮使用用戶端產生的 GUID 值或用戶端值產生器。

### <a name="option-2---rebuild-application-state"></a>選項 2-重建應用程式狀態

1. 捨棄目前的 `DbContext` 。
2. 建立新的 `DbContext` ，並從資料庫還原應用程式的狀態。
3. 通知使用者，最後一個作業可能未順利完成。

### <a name="option-3---add-state-verification"></a>選項 3-新增狀態驗證

針對變更資料庫狀態的大部分作業，可以加入程式碼來檢查是否成功。 EF 提供延伸方法讓您更輕鬆 `IExecutionStrategy.ExecuteInTransaction` 。

這個方法會開始和認可交易，也會接受在 `verifySucceeded` 交易認可期間發生暫時性錯誤時所叫用之參數中的函數。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Verification)]

> [!NOTE]
> 在此叫 `SaveChanges` 用，並 `acceptAllChangesOnSuccess` 將設定為 `false` ，以避免在 `Blog` 成功時將實體的狀態變更為 `Unchanged` `SaveChanges` 。 這可讓您在認可失敗並回復交易時，重試相同的作業。

### <a name="option-4---manually-track-the-transaction"></a>選項 4-手動追蹤交易

如果您需要使用存放區產生的索引鍵，或需要一般方式來處理不相依于所執行之作業的認可失敗，則可以將認可失敗時所檢查的識別碼指派給交易。

1. 將資料表加入至用來追蹤交易狀態的資料庫。
2. 在每個交易開始時，將資料列插入資料表中。
3. 如果在認可期間連接失敗，請檢查資料庫中對應的資料列是否存在。
4. 如果認可成功，請刪除對應的資料列，以避免資料表的成長。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Tracking)]

> [!NOTE]
> 請確定用於驗證的內容已定義執行策略，因為連接在交易認可期間失敗時，可能會在驗證期間再次失敗。
