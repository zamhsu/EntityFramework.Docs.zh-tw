---
title: Entity Framework 6 提供者模型-EF6
author: divega
ms.date: 2018-06-27
ms.assetid: 066832F0-D51B-4655-8BE7-C983C557E0E4
ms.openlocfilehash: e8b0552ec083d8ab276aa9de109650f423160269
ms.sourcegitcommit: a81aed575372637997b18a0f9466d8fefb33350a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2018
ms.locfileid: "43821383"
---
# <a name="the-entity-framework-6-provider-model"></a>Entity Framework 6 提供者模型

Entity Framework 提供者模型可讓 Entity Framework，可搭配不同類型的資料庫伺服器。 例如，一個提供者可以插入允許使用於 Microsoft SQL Server，而另一個提供者可以插入允許使用於 Microsoft SQL Server Compact Edition EF 的 EF。 我們已了解的 EF6 的提供者都位於[Entity Framework 提供者](~/ef6/fundamentals/providers/index.md)頁面。

特定變更都必須與提供者，以允許在開放原始碼授權下發行 EF 的 EF 互動的方式。 這些變更需要重建的 EF 對 EF6 組件，以及新的機制，來註冊提供者的提供者。

## <a name="rebuilding"></a>重建

與 EF6 先前已在.NET framework 的核心程式碼現在正在隨附為頻外 (OOB) 組件。 如何建置應用程式與 EF6 的詳細資料可於[更新應用程式的 EF6](~/ef6/what-is-new/upgrading-to-ef6.md)頁面。 提供者也必須重建使用這些指示。

## <a name="provider-types-overview"></a>提供者類型概觀

EF 提供者其實這些服務 （適用於基底類別），從擴充或實作 （適用於介面） 的 CLR 型別所定義的提供者特定服務的集合。 這些服務的兩個是基本和所需的 EF 完全運作。 有些則是選擇性的只需要特定的功能是必要和/或目標的特定資料庫伺服器無法使用這些服務的預設實作會實作。

### <a name="fundamental-provider-types"></a>基本的提供者類型

#### <a name="dbproviderfactory"></a>DbProviderFactory

EF 取決於衍生自特定型別[System.Data.Common.DbProviderFactory](http://msdn.microsoft.com/en-us/library/system.data.common.dbproviderfactory.aspx)執行所有的低層級的資料庫存取權。 DbProviderFactory 不是實際的 EF 的一部分，而是改為在.NET Framework 中的 ADO.NET 提供者做為進入點的類別可以使用 EF，其他的 O/RMs 或直接由應用程式來取得執行個體的連接、 命令、 參數和其他的 ADO.NET 抽象概念，在提供者無關的方式。 DbProviderFactory 的詳細資訊位於[ADO.NET 的 MSDN 文件](http://msdn.microsoft.com/en-us/library/a6cd7c08.aspx)。

#### <a name="dbproviderservices"></a>DbProviderServices

EF 取決於特定的型別衍生自 DbProviderServices 提供額外的功能所需的 EF，上方已 ADO.NET 提供者所提供的功能。 在較舊版本的 EF DbProviderServices 類別是.NET Framework 的一部分，並 System.Data.Common 命名空間中找不到。 開始使用 EF6 這個類別現在是 EntityFramework.dll 的一部分，並在 System.Data.Entity.Core.Common 命名空間。

更多詳細的 DbProviderServices 實作基本的功能都位於[MSDN](http://msdn.microsoft.com/en-us/library/ee789835.aspx)。 不過請注意，截至撰寫這項資訊不會更新 EF6 雖然大部分的概念是仍然有效。 SQL Server 和 SQL Server Compact 的 DbProviderServices 實作也已簽入至[開放原始碼程式碼基底](https://github.com/aspnet/EntityFramework6/)，可做為其他實作的實用參考。

在較舊版本的 EF 從 ADO.NET 提供者直接取得要使用的 DbProviderServices 實作。 這是由轉換為 IServiceProvider DbProviderFactory，呼叫 GetService 方法。 這與緊密結合 EF 提供者的 DbProviderFactory。 此結合禁止 EF 中移出.NET Framework，因此 EF6 的這個緊密結合已移除，並直接在應用程式的組態檔或程式碼為基礎，現在已註冊的 DbProviderServices 實作更多詳細資料中所述的組態_註冊的 DbProviderServices_下一節。

### <a name="additional-services"></a>其他服務

除了上面所述的基本服務還有許多其他服務 EF 使用，也就是一律或有時提供者專屬。 DbProviderServices 實作可以提供這些服務的預設提供者特定實作。 應用程式也可以覆寫的實作，這些服務，或提供實作，當 DbProviderServices 類型不會提供預設值。 這描述更詳細地_解析其他服務_下一節。

以下列出提供者可能會感興趣的提供者的其他服務類型。 每個服務類型的更多詳細可以找到 API 文件中。

#### <a name="idbexecutionstrategy"></a>IDbExecutionStrategy

這是選擇性的服務，可讓提供者，以針對資料庫執行查詢和命令時，實作重試或其他行為。 如果不提供實作，然後 EF 將只執行命令，並傳播擲回任何例外狀況。 適用於 SQL Server 會提供重試原則，也就是特別有用，例如 SQL Azure 的雲端式資料庫伺服器上執行時使用此服務。

#### <a name="idbconnectionfactory"></a>IDbConnectionFactory

這是選擇性的服務，可讓提供者來指定資料庫名稱時的慣例來建立 DbConnection 物件。 請注意，雖然這項服務的 DbProviderServices 實作，它已因為 EF 4.1 存在，而且也可以明確地設定組態檔中或在程式碼，即可解決。 提供者只會有機會解決這項服務，如果它註冊為預設的提供者 (請參閱_的預設提供者_下方)，如果預設連接 factory 尚未設定其他位置。

#### <a name="dbspatialservices"></a>DbSpatialServices

這是選擇性的服務，可讓提供者將 geography 和 geometry 空間類型的支援。 為了讓應用程式使用 EF 使用的空間類型，必須提供這項服務的實作。 DbSptialServices 要求有兩種。 首先，提供者特定的空間服務要求使用 DbProviderInfo 物件 (其中包含非變異名稱和資訊清單語彙基元) 做為索引鍵。 第二，DbSpatialServices 可要求沒有索引鍵。 這用來解決 「 全域空間提供者 」 時，建立獨立的 DbGeography 或 DbGeometry 類型使用。

#### <a name="migrationsqlgenerator"></a>MigrationSqlGenerator

這是選擇性的服務，可讓用於 Code First 所建立和修改資料庫結構描述中使用 SQL 產生的 EF 移轉。 實作，才能支援移轉。 如果未提供實作，然後它會也可用時使用資料庫初始設定式或 Database.Create; 方法建立資料庫。

#### <a name="funcdbconnection-string-historycontextfactory"></a>L o c k < DbConnection HistoryContextFactory 的字串 >

這是選擇性的服務，可讓提供者來設定對應至 HistoryContext`__MigrationHistory`使用 EF 移轉的資料表。 HistoryContext 是程式碼的第一個 DbContext，可以使用一般的 fluent API 來變更資料表和資料行對應規格的名稱等項目進行設定。 如果該提供者支援所有的預設資料表和資料行對應，可能會針對給定的資料庫伺服器運作這項服務由 EF 傳回所有提供者的預設實作。 在此情況下提供者不會不需要提供此服務的實作。

#### <a name="idbproviderfactoryresolver"></a>IDbProviderFactoryResolver

這是選擇性的服務，從指定的 DbConnection 物件取得正確的 DbProviderFactory。 這項服務由 EF 傳回所有提供者的預設實作是適用於所有提供者。 不過，.NET 4 上執行時，DbProviderFactory 不是可公開存取一個如果其 DbConnections。 因此，EF 會使用一些啟發學習法來搜尋已註冊的提供者，以尋找相符項目。 很可能對於某些提供者將會失敗的這些啟發學習法，而在此情況下，提供者應該提供新的實作。

## <a name="registering-dbproviderservices"></a>註冊 DbProviderServices

在應用程式的組態檔 （app.config 或 web.config） 中或使用程式碼為基礎的組態，您可以註冊要使用的 DbProviderServices 實作。 在任一情況下註冊會使用提供者的 「 非變異名稱 」 做為索引鍵。 這可讓多個提供者註冊和單一應用程式中使用。 使用 EF 註冊的非變異名稱等同於使用 ADO.NET 提供者註冊和連接字串的非變異名稱。 例如，適用於 SQL Server 的非變異名稱會使用"System.Data.SqlClient"。

### <a name="config-file-registration"></a>組態檔註冊

要使用的 DbProviderServices 類型會註冊為提供者中的項目 entityFramework 應用程式的組態檔區段的提供者清單。 例如: 

``` xml
<entityFramework>
  <providers>
    <provider invariantName="My.Invariant.Name" type="MyProvider.MyProviderServices, MyAssembly" />
  </providers>
</entityFramework>
```

_型別_字串必須是要使用的 DbProviderServices 實作的組件限定的型別名稱。

### <a name="code-based-registration"></a>程式碼架構註冊

從 EF6 的提供者可以也使用註冊的程式碼。 這可讓 EF 用不需要變更應用程式的組態檔的提供者。 若要使用的程式碼為基礎的組態應用程式應該建立 DbConfiguration 類別中所述[程式碼為基礎的設定文件](http://msdn.com/data/jj680699)。 DbConfiguration 類別的建構函式應該都會接著呼叫 SetProviderServices 註冊 EF 提供者。 例如: 

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetProviderServices("My.New.Provider", new MyProviderServices());
    }
}
```

## <a name="resolving-additional-services"></a>解析其他服務

如上方所述_提供者型別概觀_區段，DbProviderServices 類別也可用來解析其他服務。 這可能是因為 DbProviderServices 實作 IDbDependencyResolver 和每個已註冊的 DbProviderServices 型別會新增為 「 預設的解析程式 」。 更詳細地說明 IDbDpendencyResolver 機制[相依性解析](~/ef6/fundamentals/configuring/dependency-resolution.md)。 不過，不需要了解在這個規格來解析其他服務提供者中的所有概念。

若要解決其他服務提供者的最常見方式是 DbProviderServices 類別的建構函式中每個服務呼叫 DbProviderServices.AddDependencyResolver。 比方說，SqlProviderServices （SQL Server EF 提供者） 會有程式碼如下所示的初始設定：

``` csharp
private SqlProviderServices()
{
    AddDependencyResolver(new SingletonDependencyResolver<IDbConnectionFactory>(
        new SqlConnectionFactory()));

    AddDependencyResolver(new ExecutionStrategyResolver<DefaultSqlExecutionStrategy>(
        "System.data.SqlClient", null, () => new DefaultSqlExecutionStrategy()));

    AddDependencyResolver(new SingletonDependencyResolver<Func<MigrationSqlGenerator>>(
        () => new SqlServerMigrationSqlGenerator(), "System.data.SqlClient"));

    AddDependencyResolver(new SingletonDependencyResolver<DbSpatialServices>(
        SqlSpatialServices.Instance,
        k =>
        {
            var asSpatialKey = k as DbProviderInfo;
            return asSpatialKey == null
                || asSpatialKey.ProviderInvariantName == ProviderInvariantName;
        }));
}
```

這個建構函式會使用下列協助程式類別：

*   SingletonDependencyResolver： 提供簡單的方式來解決單一服務，亦即，相同的執行個體傳回每次稱為 GetService 的服務。 暫時性服務通常會註冊為單一處理站將用於視需要建立暫時性的執行個體中。
*   ExecutionStrategyResolver: 解析程式傳回 IExecutionStrategy 實作特定。

而不是使用 DbProviderServices.AddDependencyResolver 它，也可以覆寫 DbProviderServices.GetService，並直接解決其他服務。 當 EF 必須由特定型別，並在某些情況下，指定索引鍵所定義的服務時，會呼叫這個方法。 如果可以或傳回 null 以退出傳回服務並改為允許以解決問題的另一個類別，這個方法應傳回服務。 例如，若要解決的預設連接 factory GetService 中的程式碼可能會看起來像這樣：

``` csharp
public override object GetService(Type type, object key)
{
    if (type == typeof(IDbConnectionFactory))
    {
        return new SqlConnectionFactory();
    }
    return null;
}
```

### <a name="registration-order"></a>註冊順序

當多個的 DbProviderServices 實作已註冊的應用程式組態檔中會新增為次要的解析程式所列出的順序。 因為解析程式會固定加入到次要的解析程式鏈結頂端這表示，在清單結尾處的提供者就有機會解決之前的其他相依性。 （可能會感到一點違反直覺一開始，但它才有意義您想像從清單的每個提供者並堆疊頂端的現有的提供者）。

這種排序通常並不重要由於大部分的提供者服務是特定提供者，並依提供者非變異名稱索引鍵。 不過，服務，都不做為索引鍵提供者非變異名稱或某些其他提供者專屬的金鑰，服務就會解析根據這種排序。 例如，如果未明確設定不同地方 else，預設連接 factory 會來自於鏈結中最上層的提供者。

## <a name="additional-config-file-registrations"></a>其他的組態檔註冊

可以明確註冊一些上面所述，直接在應用程式的組態檔中的其他提供者服務。 這是在組態檔中的註冊時將會使用而不是任何的 DbProviderServices 實作的 GetService 方法所傳回的項目。

### <a name="registering-the-default-connection-factory"></a>註冊預設的連接 factory

開始 EF5 EntityFramework NuGet 套件會自動在組態檔中註冊的 SQL Express 連線處理站或 LocalDb 的連接 factory。

例如: 

``` xml
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework" >
</entityFramework>
```

_型別_是預設連線處理站，必須實作 IDbConnectionFactory 的組件限定類型名稱。

建議的提供者 NuGet 套件安裝時，這種方式設定預設連接 factory。 請參閱_提供者的 NuGet 套件_如下。

## <a name="additional-ef6-provider-changes"></a>EF6 的其他提供者變更

### <a name="spatial-provider-changes"></a>空間的提供者的變更

支援空間型別提供者，現在必須從 DbSpatialDataReader 衍生的類別上實作一些其他方法：

*   `public abstract bool IsGeographyColumn(int ordinal)`
*   `public abstract bool IsGeometryColumn(int ordinal)`

另外還有新的非同步版本，建議您以覆寫的預設實作會委派給同步的方法，並因此不會執行非同步的現有方法的：

*   `public virtual Task<DbGeography> GetGeographyAsync(int ordinal, CancellationToken cancellationToken)`
*   `public virtual Task<DbGeometry> GetGeometryAsync(int ordinal, CancellationToken cancellationToken)`

### <a name="native-support-for-enumerablecontains"></a>Enumerable.Contains 的原生支援

EF6 導入了新的運算式型別，DbInExpression，已新增 LINQ 查詢中使用 Enumerable.Contains 周圍解決效能問題。 DbProviderManifest 類別具有新的虛擬方法，稱為 SupportsInExpression，由 EF 來判斷是否提供者會處理新的運算式型別呼叫。 與現有的提供者實作的相容性的方法會傳回 false。 受益於這項改善，EF6 提供者可以加入程式碼來處理 DbInExpression，並覆寫 SupportsInExpression 傳回 true。 藉由呼叫 DbExpressionBuilder.In 方法，就可以建立 DbInExpression 的執行個體。 DbInExpression 執行個體所組成的 DbExpression，通常代表資料表資料行，以及一份要進行相符測試 DbConstantExpression。

## <a name="nuget-packages-for-providers"></a>提供者的 NuGet 套件

將 EF6 提供者提供一個方式是發行 NuGet 套件的形式。 使用 NuGet 套件有下列優點：

*   它是簡單易用的 NuGet，以加入應用程式的組態檔中的提供者註冊
*   可以在組態檔來進行其他變更，若要設定預設連接 factory，以便依照慣例建立的連接將會使用已註冊的提供者
*   新增繫結重新導向，使 EF6 提供者應該繼續運作，甚至在發行新的 EF 封裝之後，才會處理的 NuGet

這個範例是 EntityFramework.SqlServerCompact 套件隨附於[開放原始碼程式碼基底](http://github.com/aspnet/entityframework6)。 此套件會提供良好的範本來建立 EF 提供者 NuGet 封裝。

### <a name="powershell-commands"></a>PowerShell 命令

安裝 EntityFramework NuGet 套件時它會註冊 PowerShell 模組，包含非常適合用來提供者套件的兩個命令：

*   新增 EFProvider 目標專案的組態檔中的提供者將新的實體，並確保它是已註冊的提供者清單的結尾。
*   新增 EFDefaultConnectionFactory 新增或更新目標專案的組態檔中的 defaultConnectionFactory 註冊。

這兩種指令會負責將 entityFramework 區段新增至設定檔及新增提供者集合，如有必要。

它可能是這些命令，從 install.ps1 NuGet 指令碼呼叫。 例如，SQL Compact 提供者的 install.ps1 看起來會如下所示：

``` powershell
param($installPath, $toolsPath, $package, $project)
Add-EFDefaultConnectionFactory $project 'System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework' -ConstructorArguments 'System.Data.SqlServerCe.4.0'
Add-EFProvider $project 'System.Data.SqlServerCe.4.0' 'System.Data.Entity.SqlServerCompact.SqlCeProviderServices, EntityFramework.SqlServerCompact'</pre>
```

可以取得有關這些命令的詳細資訊，藉由使用套件管理員主控台視窗中的 取得說明。

## <a name="wrapping-providers"></a>文繞圖提供者

文繞圖提供者會包裝現有的提供者來擴充其他功能，例如分析或追蹤功能，使用 EF 和/或 ADO.NET 提供者。 包裝提供者可以註冊以一般方式，但它通常會較方便攔截的解析度的提供者相關的服務來設定在執行階段包裝提供者。 靜態事件 OnLockingConfiguration DbConfiguration 類別可用來執行這項操作。

OnLockingConfiguration 稱為 EF 判定應用程式定義域的所有 EF 設定將從都取得後，但它已鎖定可供使用。 在應用程式啟動 （EF 用之前） 的應用程式應該註冊這個事件的事件處理常式。 （我們想要新增支援在組態檔中註冊這個處理常式，但這還不支援）。然後，事件處理常式應該要 ReplaceService 需要包裝每個服務的呼叫。  

比方說，換行 IDbConnectionFactory 和 DbProviderService，應註冊處理常式，如下所示：

``` csharp
DbConfiguration.OnLockingConfiguration +=
    (_, a) =>
    {
        a.ReplaceService<DbProviderServices>(
            (s, k) => new MyWrappedProviderServices(s));

        a.ReplaceService<IDbConnectionFactory>(
            (s, k) => new MyWrappedConnectionFactory(s));
    };
```

已經解決，而且現在應該包裝以及金鑰用來解析服務的服務會傳遞至處理常式。 這個處理常式然後可以包裝此服務，並傳回的服務取代的已包裝的版本。

## <a name="resolving-a-dbproviderfactory-with-ef"></a>解決使用 EF DbProviderFactory

DbProviderFactory 是其中一個基本提供者型別中所述，所需的 EF_提供者型別概觀_上一節。 如先前所述，它不是 EF 類型和註冊通常不是 EF 組態的一部分，但會改為在 machine.config 檔案及/或應用程式的組態檔的一般 ADO.NET 提供者註冊。

儘管此 EF 仍會使用其一般的相依性解析機制尋找 DbProviderFactory 時使用。 預設解析程式組態檔中使用標準 ADO.NET 註冊，因此這是通常是透明的。 因為標準相依性解析使用機制，但這表示 IDbDependencyResolver 可用來解決 DbProviderFactory，即使未完成一般 ADO.NET 註冊。

在這種方式解決 DbProviderFactory 有幾個含意：

*   使用程式碼為基礎的組態的應用程式可以呼叫類別中新增其 DbConfiguration 註冊適當的 DbProviderFactory。 這會特別有用的應用程式，不想要 （或無法） 進行完全使用的任何檔案為基礎的組態。
*   服務可以換行，或取代中所述，使用 ReplaceService_包裝提供者_上一節
*   理論上來說，DbProviderServices 實作無法解析的 DbProviderFactory。

關於執行任何這些項目，請注意重要的一點是，它們只會影響 EF 所 DbProviderFactory 查閱。 其他非 EF 程式碼可能仍然會預期以一般方式註冊的 ADO.NET 提供者，如果找不到註冊可能會失敗。 基於這個理由是通常更適合用來在一般的 ADO.NET 方法中註冊的 DbProviderFactory。

### <a name="related-services"></a>相關的服務

如果使用 EF 來解決 DbProviderFactory，它應該也解決 IProviderInvariantName 和 IDbProviderFactoryResolver 服務。

IProviderInvariantName 是一項服務，用來判斷 DbProviderFactory 指定類型的提供者非變異名稱。 這項服務的預設實作會使用 ADO.NET 提供者註冊。 這表示，如果因為 EF 所解析的 DbProviderFactory，ADO.NET 提供者不會以一般方式註冊，則它也會解決這項服務所需的。 請注意使用 DbConfiguration.SetProviderFactory 方法時，會自動加入此服務的解析程式。

中所述_提供者型別概觀_上方區段中，IDbProviderFactoryResolver 用來從指定的 DbConnection 物件取得正確的 DbProviderFactory。 在.NET 4 上執行時，此服務的預設實作會使用 ADO.NET 提供者註冊。 這表示，如果因為 EF 所解析的 DbProviderFactory，ADO.NET 提供者不會以一般方式註冊，則它也會解決這項服務所需的。
