---
title: 連接恢復功能和重試邏輯-EF6
description: Entity Framework 6 中的連接恢復功能和重試邏輯
author: AndriySvyryd
ms.date: 11/20/2019
uid: ef6/fundamentals/connection-resiliency/retry-logic
ms.openlocfilehash: 3e71e973be5a23c86f873e9adf1bf00f4b6def58
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073184"
---
# <a name="connection-resiliency-and-retry-logic"></a>連接恢復功能和重試邏輯
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。  

連線到資料庫伺服器的應用程式一律會因為後端失敗和網路不穩定的連接中斷而受到影響。 不過，在以 LAN 為基礎的環境中，針對專用的資料庫伺服器，這些錯誤很罕見，因此通常不需要額外的邏輯來處理這些失敗。 隨著雲端式資料庫伺服器的增加（例如 Windows Azure SQL Database）以及較不可靠的網路連線，現在更常發生連接中斷的情況。 這可能是因為雲端資料庫用來確保服務公平（例如連線節流）或網路不穩定造成間歇性的錯誤和其他暫時性錯誤的防禦技巧。  

連接復原是指 EF 能夠自動重試因這些連接中斷而失敗的任何命令。  

## <a name="execution-strategies"></a>執行策略  

連接重試由 >idbexecutionstrategy 介面的執行負責處理。 >idbexecutionstrategy 的實施將負責接受作業，並在發生例外狀況時，判斷重試是否適當，以及是否要重試。 EF 隨附四個執行策略：  

1. **DefaultExecutionStrategy**：此執行策略不會重試任何作業，它是 sql server 以外資料庫的預設值。  
2. **>defaultsqlexecutionstrategy**：這是預設使用的內部執行策略。 不過，這項策略並不會重試，但它會包裝任何可能暫時性的例外狀況，以通知使用者可能會想要啟用連接復原。  
3. **DbExecutionStrategy**：這個類別適合做為其他執行策略的基類，包括您自己的自訂專案。 它會執行指數重試原則，其中初始重試會以零延遲進行，而延遲會以指數方式增加，直到達到最大重試次數為止。 此類別具有抽象 ShouldRetryOn 方法，可在衍生的執行策略中執行，以控制應該重試的例外狀況。  
4. **>sqlazureexecutionstrategy**：此執行策略繼承自 DbExecutionStrategy，並且會在使用 Azure SQL Database 時已知可能暫時性的例外狀況上重試。

> [!NOTE]
> 執行策略2和4包含在 EF 隨附的 Sql Server 提供者中，該提供者位於 EntityFramework 中，而且是設計來搭配 SQL Server 使用。  

## <a name="enabling-an-execution-strategy"></a>啟用執行策略  

要告訴 EF 使用執行策略，最簡單的方式就是使用 [>dbconfiguration](xref:ef6/fundamentals/configuring/code-based) 類別的 SetExecutionStrategy 方法：  

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
    }
}
```  

這段程式碼會告知 EF 在連接到 SQL Server 時使用 >sqlazureexecutionstrategy。  

## <a name="configuring-the-execution-strategy"></a>設定執行策略  

>sqlazureexecutionstrategy 的函式可以接受兩個參數： MaxRetryCount 和 MaxDelay。 MaxRetry count 是策略將重試的最大次數。 MaxDelay 是一個 TimeSpan，代表執行策略將使用的重試之間的延遲上限。  

若要將重試次數上限設為1，並將最大延遲設定為30秒，請執行下列動作：  

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetExecutionStrategy(
            "System.Data.SqlClient",
            () => new SqlAzureExecutionStrategy(1, TimeSpan.FromSeconds(30)));
    }
}
```  

>sqlazureexecutionstrategy 會在發生暫時性失敗時立即重試，但會在每次重試之間延遲一段時間，直到超過最大重試限制，或總時間達到延遲上限為止。  

執行策略只會重試有限數目的例外狀況，這些例外狀況通常是暫時性的，您仍然需要處理其他錯誤，並在錯誤不是暫時性或需要太久才能解決的情況下攔截 RetryLimitExceeded 例外狀況。  

使用重試執行策略時有一些已知的限制：  

## <a name="streaming-queries-are-not-supported"></a>不支援串流查詢  

根據預設，EF6 和更新版本會緩衝處理查詢結果，而不是串流處理。 如果您想要以資料流程處理結果，您可以使用 AsStreaming 方法，將 LINQ to Entities 查詢變更為串流處理。  

``` csharp
using (var db = new BloggingContext())
{
    var query = (from b in db.Blogs
                orderby b.Url
                select b).AsStreaming();
    }
}
```  

當註冊重試執行策略時，不支援串流處理。 這項限制存在的原因是因為連接可能會在傳回的結果中捨棄。 發生這種情況時，EF 需要重新執行整個查詢，但無法可靠地知道哪些結果已經傳回 (資料在傳送初始查詢之後可能已經變更、結果可能會以不同的順序傳回、結果可能沒有唯一的識別碼等 ) 。  

## <a name="user-initiated-transactions-are-not-supported"></a>不支援使用者起始的交易  

當您已設定導致重試的執行策略時，使用交易有一些限制。  

根據預設，EF 會在交易內執行任何資料庫更新。 您不需要執行任何動作來啟用此功能，而且 EF 一律會自動執行此動作。  

例如，在下列程式碼中，SaveChanges 會自動在交易內執行。 如果 SaveChanges 在插入其中一個新網站之後失敗，則會回復交易，而不會對資料庫套用任何變更。 內容也會留在狀態，讓 SaveChanges 再次呼叫，以重試套用變更。  

``` csharp
using (var db = new BloggingContext())
{
    db.Blogs.Add(new Site { Url = "http://msdn.com/data/ef" });
    db.Blogs.Add(new Site { Url = "http://blogs.msdn.com/adonet" });
    db.SaveChanges();
}
```  

若未使用重試執行策略，您可以在單一交易中包裝多個作業。 例如，下列程式碼會將兩個 SaveChanges 呼叫包裝在單一交易中。 如果任一作業的任何部分失敗，則不會套用任何變更。  

``` csharp
using (var db = new BloggingContext())
{
    using (var trn = db.Database.BeginTransaction())
    {
        db.Blogs.Add(new Site { Url = "http://msdn.com/data/ef" });
        db.Blogs.Add(new Site { Url = "http://blogs.msdn.com/adonet" });
        db.SaveChanges();

        db.Blogs.Add(new Site { Url = "http://twitter.com/efmagicunicorns" });
        db.SaveChanges();

        trn.Commit();
    }
}
```  

使用重試執行策略時，不支援這種做法，因為 EF 不知道任何先前的作業，以及如何重試它們。 例如，如果第二個 SaveChanges 失敗，則 EF 不再具有重試第一個 SaveChanges 呼叫所需的資訊。  

### <a name="solution-manually-call-execution-strategy"></a>解決方案：手動呼叫執行策略  

解決方法是手動使用執行策略，並為它提供要執行的整組邏輯，以便在其中一個作業失敗時重試所有專案。 當衍生自 DbExecutionStrategy 的執行策略執行時，它會暫止 SaveChanges 中使用的隱含執行策略。  

請注意，任何內容都應該在要重試的程式碼區塊內進行。 這可確保每次重試都會以乾淨狀態開始。  

``` csharp
var executionStrategy = new SqlAzureExecutionStrategy();

executionStrategy.Execute(
    () =>
    {
        using (var db = new BloggingContext())
        {
            using (var trn = db.Database.BeginTransaction())
            {
                db.Blogs.Add(new Blog { Url = "http://msdn.com/data/ef" });
                db.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/adonet" });
                db.SaveChanges();

                db.Blogs.Add(new Blog { Url = "http://twitter.com/efmagicunicorns" });
                db.SaveChanges();

                trn.Commit();
            }
        }
    });
```  
