---
title: 連接恢復功能，然後重試邏輯-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 47d68ac1-927e-4842-ab8c-ed8c8698dff2
ms.openlocfilehash: d7e58abfa17c5537cdc9b0068e7c2a3c2e390038
ms.sourcegitcommit: 0d36e8ff0892b7f034b765b15e041f375f88579a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2018
ms.locfileid: "44250513"
---
# <a name="connection-resiliency-and-retry-logic"></a>連接恢復功能，然後重試邏輯
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。  

連接至資料庫伺服器的應用程式一直容易因為後端失敗和網路不穩定的連線中斷。 不過，在基礎的 LAN 環境中針對專用的資料庫伺服器使用這些錯誤很少，額外的邏輯來處理這些失敗通常並不必要的。 雲端的興起是以基礎資料庫伺服器，例如 Windows Azure SQL Database 和連線現在是較常見的連線中斷，進行較不可靠的網路。 這可能是服務的因為防禦性技術，雲端資料庫使用，以確保公平性，例如連線節流，或造成間歇性的逾時和其他暫時性錯誤的網路不穩定。  

連接恢復功能是指自動重試失敗，因為這些連線中斷的任何命令的 ef 的能力。  

## <a name="execution-strategies"></a>執行策略  

連接重試是由負責 IDbExecutionStrategy 介面的實作。 IDbExecutionStrategy 的實作會負責接受作業，並發生例外狀況時，判斷是否適合重試它是否正在重試。 有四個隨附於 EF 的執行策略：  

1. **DefaultExecutionStrategy**： 此執行策略不會重試任何作業，它是 sql server 以外的資料庫的預設值。  
2. **DefaultSqlExecutionStrategy**： 這是預設會使用內部執行策略。 此策略不會重試根本，不過，它會換行可能是暫時性，通知使用者他們可能會想要啟用連線恢復功能的任何例外狀況。  
3. **DbExecutionStrategy**： 這個類別是適合的基底類別做為其他的執行策略，包括您自己自訂的。 它會實作指數重試原則，其中初始的重試發生零延遲和延遲會增加以指數方式叫用的最大重試計數為止。 這個類別具有一種抽象的 ShouldRetryOn 方法可以實作在衍生的執行策略，以控制應該重試的例外狀況。  
4. **SqlAzureExecutionStrategy**： 繼承自 DbExecutionStrategy 此執行策略，且將重新嘗試已知是可能是暫時性的使用 Azure SQL Database 時的例外狀況。

> [!NOTE]
> 2 和 4 的執行策略會包含在 EF，EntityFramework.SqlServer 組件中所隨附的 Sql Server 提供者，而且設計用於搭配 SQL Server。  

## <a name="enabling-an-execution-strategy"></a>啟用執行策略  

告知 EF 來使用執行策略的最簡單方式是使用 SetExecutionStrategy 方法[DbConfiguration](~/ef6/fundamentals/configuring/code-based.md)類別：  

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
    }
}
```  

此程式碼會告知 EF 來連接到 SQL Server 時，使用 SqlAzureExecutionStrategy。  

## <a name="configuring-the-execution-strategy"></a>設定執行策略  

SqlAzureExecutionStrategy 的建構函式可以接受兩個參數，MaxRetryCount 和 MaxDelay。 MaxRetry 計數已重試策略的最大次數。 MaxDelay 是 TimeSpan，代表將會使用執行策略的重試之間的延遲上限。  

若要設為 1 和最大延遲為 30 秒的重試次數上限，您需要下列 execue:  

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

SqlAzureExecutionStrategy 會重試，立即超出暫時性失敗的情況，但將會再延遲之間每次重試之前的最大重試限制在第一次，或總時間達到最大延遲。  

執行策略只會重試的例外狀況，通常是 tansient 數量有限，仍必須處理其他錯誤，以及攔截 RetryLimitExceeded 例外狀況，若要解決錯誤並非暫時性或花太多時間的位置案例它本身。  

使用重試的執行策略時，有一些已知的限制：  

## <a name="streaming-queries-are-not-supported"></a>不支援資料流的查詢  

根據預設，EF6 和更新版本將查詢結果，而不是這些資料流緩衝區。 如果您想要有結果串流處理您可以使用 AsStreaming 方法來變更 LINQ to Entities 查詢，為資料流。  

``` csharp
using (var db = new BloggingContext())
{
    var query = (from b in db.Blogs
                orderby b.Url
                select b).AsStreaming();
    }
}
```  

不支援資料流，註冊時的重試執行策略。 因為執行所傳回的結果的 「 連線無法卸除部分，就會有這項限制。 當發生這種情況時，EF 必須重新執行整個查詢，但有沒有可靠的方式就知道已傳回的結果 （資料可能已變更，因為傳送初始查詢，結果可能的傳回順序不同，結果可能不需要的唯一識別碼等等。)。  

## <a name="user-initiated-transactions-are-not-supported"></a>不支援使用者起始交易  

當您已設定導致重試執行策略時，有一些限制，有關使用交易。  

根據預設，EF 會執行交易內的任何資料庫更新。 您不需要採取任何動作來啟用此功能，EF 一律自動執行這個工作。  

例如，下列程式碼 SaveChanges 是自動在交易中執行。 如果 SaveChanges 後插入的新站台的其中一個，則會復原交易而且不需要變更套用至資料庫失敗。 內容也是處於允許套用所做的變更重試一次呼叫 SaveChanges 的狀態。  

``` csharp
using (var db = new BloggingContext())
{
    db.Blogs.Add(new Site { Url = "http://msdn.com/data/ef" });
    db.Blogs.Add(new Site { Url = "http://blogs.msdn.com/adonet" });
    db.SaveChanges();
}
```  

在不使用重試執行策略，您可以將多個作業包裝在單一交易中。 比方說，下列程式碼會包裝在單一交易中兩個 SaveChanges 呼叫。 如果任一項操作的任何部分再失敗的變更都不會套用。  

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

不支援時使用的重試執行策略，因為 EF 不知道的任何先前的作業，以及如何重試它們。 例如，如果第二個 SaveChanges 失敗然後 EF 不會再有重試第一次的 SaveChanges 呼叫所需的資訊。  

### <a name="workaround-suspend-execution-strategy"></a>因應措施： 暫停執行策略  

一個可能的因應措施是暫止正在重試執行策略需要使用使用者的程式碼片段的起始交易。 若要這樣做最簡單的方式是加入 SuspendExecutionStrategy 旗標，以您的程式碼基礎組態類別，並將變更執行策略 lambda，來設定旗標時，傳回的預設值 (非 retying) 執行策略。  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.SqlServer;
using System.Runtime.Remoting.Messaging;

namespace Demo
{
    public class MyConfiguration : DbConfiguration
    {
        public MyConfiguration()
        {
            this.SetExecutionStrategy("System.Data.SqlClient", () => SuspendExecutionStrategy
              ? (IDbExecutionStrategy)new DefaultExecutionStrategy()
              : new SqlAzureExecutionStrategy());
        }

        public static bool SuspendExecutionStrategy
        {
            get
            {
                return (bool?)CallContext.LogicalGetData("SuspendExecutionStrategy")  false;
            }
            set
            {
                CallContext.LogicalSetData("SuspendExecutionStrategy", value);
            }
        }
    }
}
```  

請注意，我們使用 CallContext 儲存旗標值。 這會提供類似執行緒區域儲存區的功能，但會安全地使用非同步程式碼-包含非同步查詢並儲存使用 Entity Framework。  

我們現在可以暫停執行策略，使用使用者起始的交易的程式碼區段。  

``` csharp
using (var db = new BloggingContext())
{
    MyConfiguration.SuspendExecutionStrategy = true;

    using (var trn = db.Database.BeginTransaction())
    {
        db.Blogs.Add(new Blog { Url = "http://msdn.com/data/ef" });
        db.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/adonet" });
        db.SaveChanges();

        db.Blogs.Add(new Blog { Url = "http://twitter.com/efmagicunicorns" });
        db.SaveChanges();

        trn.Commit();
    }

    MyConfiguration.SuspendExecutionStrategy = false;
}
```  

### <a name="workaround-manually-call-execution-strategy"></a>因應措施： 以手動方式呼叫的執行策略  

另一個選項是邏輯的手動使用執行策略，並提供完整來執行，以便它可以重試一次的所有項目如果其中一個作業失敗。 我們仍需要暫停執行策略-使用此技術-如上所示，如此可重試程式碼區塊內使用的任何內容不會嘗試重試一次。  

請注意，應重試的程式碼區塊內建構任何內容。 這可確保，我們已開始初始狀態，以便每次重試。  

``` csharp
var executionStrategy = new SqlAzureExecutionStrategy();

MyConfiguration.SuspendExecutionStrategy = true;

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

MyConfiguration.SuspendExecutionStrategy = false;
```  
