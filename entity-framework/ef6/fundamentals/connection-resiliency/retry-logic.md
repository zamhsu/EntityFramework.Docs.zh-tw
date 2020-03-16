---
title: 連接復原和重試邏輯-EF6
author: AndriySvyryd
ms.date: 11/20/2019
ms.assetid: 47d68ac1-927e-4842-ab8c-ed8c8698dff2
ms.openlocfilehash: 50e65bed32d0cfcf42746da0d632f9e990424b97
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/15/2020
ms.locfileid: "79402156"
---
# <a name="connection-resiliency-and-retry-logic"></a>連接復原和重試邏輯
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。  

連接到資料庫伺服器的應用程式一律會因為後端失敗和網路不穩定而受到連線中斷的影響。 不過，在以 LAN 為基礎的環境中，針對專用的資料庫伺服器運作時，這些錯誤很罕見，但通常不需要額外的邏輯來處理這些失敗。 隨著以雲端為基礎的資料庫伺服器（例如 Windows Azure SQL Database 和連線較不可靠的網路）的增加，現在更常發生連接中斷的情況。 這可能是因為雲端資料庫用來確保服務公平的防禦技術，例如連線節流，或網路中的不穩定造成間歇的超時和其他暫時性錯誤。  

「連接復原」是指 EF 自動重試因這些連接中斷而失敗的任何命令的能力。  

## <a name="execution-strategies"></a>執行策略  

IDbExecutionStrategy 介面的執行會負責連接重試。 IDbExecutionStrategy 的執行會負責接受作業，如果發生例外狀況，則判斷重試是否適當，並在其為時重試。 EF 隨附四個執行策略：  

1. **DefaultExecutionStrategy**：此執行策略不會重試任何作業，而是 sql server 以外的資料庫的預設值。  
2. **DefaultSqlExecutionStrategy**：這是預設使用的內部執行策略。 這種策略根本不會重試，不過，它會包裝任何可能是暫時性的例外狀況，以通知使用者他們可能想要啟用連接恢復功能。  
3. **DbExecutionStrategy**：這個類別適合做為其他執行策略的基類，包括您自己的自訂。 它會執行指數重試原則，其中會以零延遲進行初始重試，而延遲會以指數方式增加，直到達到最大重試次數為止。 這個類別具有抽象的 ShouldRetryOn 方法，可在衍生的執行策略中執行，以控制應該重試的例外狀況。  
4. **SqlAzureExecutionStrategy**：此執行策略會繼承自 DbExecutionStrategy，並會在使用 Azure SQL Database 時，在已知可能會暫時性的例外狀況上重試。

> [!NOTE]
> 執行策略2和4包含在 EF 隨附的 Sql Server 提供者中，這是在 EntityFramework 中，而且是設計用來與 SQL Server 搭配使用。  

## <a name="enabling-an-execution-strategy"></a>啟用執行策略  

告訴 EF 使用執行策略最簡單的方式，就是使用[DbConfiguration](~/ef6/fundamentals/configuring/code-based.md)類別的 SetExecutionStrategy 方法：  

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
    }
}
```  

此程式碼會指示 EF 在連接到 SQL Server 時使用 SqlAzureExecutionStrategy。  

## <a name="configuring-the-execution-strategy"></a>設定執行策略  

SqlAzureExecutionStrategy 的構造函式可以接受兩個參數： MaxRetryCount 和 MaxDelay。 MaxRetry count 是此策略將重試的次數上限。 MaxDelay 是一個 TimeSpan，代表執行策略將使用的重試之間的最大延遲。  

若要將重試次數上限設為1，並將最大延遲設定為30秒，您可以執行下列動作：  

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

SqlAzureExecutionStrategy 將會在第一次發生暫時性失敗時立即重試，但會延遲每次重試之間的時間，直到超過最大重試次數或總延遲數上限為止。  

執行策略只會重試有限數目的例外狀況，通常是暫時性的，您仍然需要處理其他錯誤，並在錯誤不是暫時性或需要太久的情況下攔截例外狀況（RetryLimitExceeded exception）直接.  

使用重試執行策略時，有一些已知的限制：  

## <a name="streaming-queries-are-not-supported"></a>不支援串流查詢  

根據預設，EF6 和更新版本會緩衝查詢結果，而不是串流處理。 如果您想要將結果串流處理，您可以使用 AsStreaming 方法，將 LINQ to Entities 查詢變更為資料流程。  

``` csharp
using (var db = new BloggingContext())
{
    var query = (from b in db.Blogs
                orderby b.Url
                select b).AsStreaming();
    }
}
```  

註冊重試執行策略時，不支援串流處理。 這項限制存在的原因是，連接可能會在傳回的結果中捨棄部分方式。 發生這種情況時，EF 需要重新執行整個查詢，但沒有可靠的方法可以得知已經傳回的結果（資料可能會在傳送初始查詢後變更，結果可能會以不同的順序傳回，結果可能不會有唯一識別碼等等）。  

## <a name="user-initiated-transactions-are-not-supported"></a>不支援使用者起始的交易  

當您設定了會導致重試的執行策略時，使用交易時，會有一些限制。  

根據預設，EF 會在交易內執行任何資料庫更新。 您不需要採取任何動作來啟用此功能，因此 EF 一律會自動執行此作業。  

例如，在下列程式碼中，會在交易內自動執行 SaveChanges。 如果 SaveChanges 在插入其中一個新網站之後失敗，則會回復交易，而且不會對資料庫套用任何變更。 內容也會停留在允許再次呼叫 SaveChanges 以重試套用變更的狀態。  

``` csharp
using (var db = new BloggingContext())
{
    db.Blogs.Add(new Site { Url = "http://msdn.com/data/ef" });
    db.Blogs.Add(new Site { Url = "http://blogs.msdn.com/adonet" });
    db.SaveChanges();
}
```  

當不使用重試執行策略時，您可以在單一交易中包裝多個作業。 例如，下列程式碼會將兩個 SaveChanges 呼叫包裝在單一交易中。 如果任一作業的任何部分失敗，則不會套用任何變更。  

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

使用重試執行策略時，不支援這種作法，因為 EF 並不知道任何先前的作業，以及如何重試它們。 例如，如果第二個 SaveChanges 失敗，則 EF 不再具有重試第一個 SaveChanges 呼叫所需的資訊。  

### <a name="solution-manually-call-execution-strategy"></a>解決方案：手動呼叫執行策略  

解決方法是手動使用執行策略，並為它提供要執行的整組邏輯，讓它可以在其中一個作業失敗時重試所有動作。 當衍生自 DbExecutionStrategy 的執行策略正在執行時，會暫停 SaveChanges 中使用的隱含執行策略。  

請注意，應該在要重試的程式碼區塊內建立任何內容。 這可確保每次重試都會以全新狀態開始。  

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
