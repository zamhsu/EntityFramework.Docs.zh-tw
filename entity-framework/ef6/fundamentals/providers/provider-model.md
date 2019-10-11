---
title: Entity Framework 6 提供者模型-EF6
author: divega
ms.date: 06/27/2018
ms.assetid: 066832F0-D51B-4655-8BE7-C983C557E0E4
ms.openlocfilehash: 8bda3f51e8934f2add862c30e60f1185f068c515
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181602"
---
# <a name="the-entity-framework-6-provider-model"></a>Entity Framework 6 提供者模型

Entity Framework 提供者模型可讓 Entity Framework 與不同類型的資料庫伺服器搭配使用。 例如，可以插入一個提供者，以允許 EF 用於 Microsoft SQL Server，而另一個提供者則可插入以允許 EF 用於 Microsoft SQL Server Compact Edition。 我們知道的 EF6 提供者可以在 [ [Entity Framework 提供者](~/ef6/fundamentals/providers/index.md)] 頁面上找到。

EF 與提供者互動的方式需要進行某些變更，以允許在開放原始碼授權下發行 EF。 這些變更需要為 EF6 元件重建 EF 提供者，以及註冊提供者的新機制。

## <a name="rebuilding"></a>重新

在 EF6 中，先前屬於 .NET Framework 的核心程式代碼現在會以頻外（OOB）元件的形式出貨。 如需如何針對 EF6 建立應用程式的詳細資訊，請參閱[更新 EF6 的應用程式](~/ef6/what-is-new/upgrading-to-ef6.md)頁面。 提供者也必須使用這些指示重建。

## <a name="provider-types-overview"></a>提供者類型總覽

EF 提供者其實是一組由 CLR 型別所定義的提供者專屬服務，這些服務是由這些服務從（針對基類）延伸，或是針對介面來執行。 這些服務的其中兩個是基本的，而且 EF 必須能夠運作。 有些則是選擇性的，而且只有在需要特定功能時才需要執行，而且（或）這些服務的預設執行不適用於目標的特定資料庫伺服器。

## <a name="fundamental-provider-types"></a>基本提供者類型

### <a name="dbproviderfactory"></a>DbProviderFactory

EF 相依于具有衍生自[DbProviderFactory](https://msdn.microsoft.com/library/system.data.common.dbproviderfactory.aspx)的類型，以執行所有低層級的資料庫存取。 DbProviderFactory 實際上不是 EF 的一部分，而是 .NET Framework 中的類別，它會為 ADO.NET 提供者的進入點，以供 EF、其他 O/RMs 使用，或直接由應用程式用來取得連接、命令、參數和的實例。提供者不可知的其他 ADO.NET 抽象概念。 如需 DbProviderFactory 的詳細資訊，請參閱[MSDN 檔中的 ADO.NET](https://msdn.microsoft.com/library/a6cd7c08.aspx)。

### <a name="dbproviderservices"></a>DbProviderServices

EF 相依于擁有衍生自 Dbproviderservices.createdatabase 的型別，以便在 ADO.NET 提供者已提供的功能之上提供 EF 所需的額外功能。 在較舊版本的 EF 中，Dbproviderservices.createdatabase 類別是 .NET Framework 的一部分，而且是在 System.web 命名空間中找到。 從 EF6 開始，這個類別現在是 EntityFramework 的一部分，而且是在 Common 命名空間中。

如需 Dbproviderservices.createdatabase 執行的基本功能的詳細資訊，請參閱[MSDN](https://msdn.microsoft.com/library/ee789835.aspx)。 不過，請注意，在撰寫本文時，不會針對 EF6 更新這項資訊，雖然大部分的概念仍然有效。 Dbproviderservices.createdatabase 的 SQL Server 和 SQL Server Compact 實作用也會簽入到[開放原始碼程式碼基](https://github.com/aspnet/EntityFramework6/)底，而且可以做為其他執行的實用參考。

在較舊版本的 EF 中，會直接從 ADO.NET 提供者取得要使用的 Dbproviderservices.createdatabase 執行。 這是藉由將 DbProviderFactory 轉換成 IServiceProvider 並呼叫 GetService 方法來完成。 這會將 EF 提供者緊密結合到 DbProviderFactory。 這項結合會阻止 EF 移出 .NET Framework，因此 EF6 這項緊密的結合已移除，而 Dbproviderservices.createdatabase 的執行現在已直接在應用程式的設定檔中註冊，或是以程式碼為基礎如需詳細資訊，請參閱下面的 <<c0>註冊 dbproviderservices.createdatabase一節。

## <a name="additional-services"></a>其他服務

除了上面所述的基本服務以外，EF 也有許多其他服務，也就是一律或有時提供者特定的服務。 這些服務的預設提供者特定的執行，可由 Dbproviderservices.createdatabase 的實作為提供。 應用程式也可以覆寫這些服務的執行，或在 Dbproviderservices.createdatabase 類型未提供預設值時提供。 下面的_解決其他服務_一節會更詳細說明這一點。

提供者可能會對提供者感關注的其他服務類型如下所示。 如需這些服務類型的詳細資訊，請參閱 API 檔。

### <a name="idbexecutionstrategy"></a>IDbExecutionStrategy

這是選擇性的服務，可讓提供者在針對資料庫執行查詢和命令時，執行重試或其他行為。 如果未提供任何實作為，則 EF 只會執行命令，並傳播擲回的任何例外狀況。 針對 SQL Server 此服務可用來提供重試原則，這在針對雲端式資料庫伺服器（例如 SQL Azure）執行時特別有用。

### <a name="idbconnectionfactory"></a>IDbConnectionFactory

這是選擇性的服務，可讓提供者依照慣例建立 DbConnection 物件（僅提供資料庫名稱）。 請注意，雖然這項服務可以由 EF 4.1 所呈現的 Dbproviderservices.createdatabase 實作為解決，而且也可以在設定檔或程式碼中明確設定。 如果提供者註冊為預設提供者（請參閱下方_的預設提供者_），而且預設連接處理站尚未設定于其他位置，則此服務只會有機會解決這種情況。

### <a name="dbspatialservices"></a>DbSpatialServices

這是選擇性的服務，可讓提供者加入 geography 和 geometry 空間類型的支援。 必須提供此服務的執行，應用程式才能使用具有空間類型的 EF。 系統會以兩種方式要求 DbSptialServices。 首先，會使用 DbProviderInfo 物件（其中包含不變的名稱和資訊清單 token）來要求提供者專屬的空間服務，做為索引鍵。 第二，您可以要求 DbSpatialServices，而不需要金鑰。 這是用來解析建立獨立 DbGeography 或 DbGeometry 類型時所使用的「全域空間提供者」。

### <a name="migrationsqlgenerator"></a>MigrationSqlGenerator

這是選擇性的服務，可讓 EF 遷移用於建立和修改資料庫架構時所使用的 SQL 產生 Code First。 需要執行才能支援遷移。 如果已提供執行，則在使用資料庫初始化運算式或 Database. Create 方法建立資料庫時，也會使用它。

### <a name="funcdbconnection-string-historycontextfactory"></a>Func < DbConnection、string、HistoryCoNtextFactory >

這是選擇性的服務，可讓提供者設定 HistoryCoNtext 與 EF 遷移所使用之 @no__t 0 資料表的對應。 HistoryCoNtext 是 Code First 的 DbCoNtext，而且可以使用正常的 Fluent API 來設定，以變更資料表名稱和資料行對應規格之類的專案。 如果該提供者支援所有預設資料表和資料行對應，則 EF 針對所有提供者所傳回之此服務的預設執行可能會適用于指定的資料庫伺服器。 在這種情況下，提供者不需要提供此服務的執行。

### <a name="idbproviderfactoryresolver"></a>IDbProviderFactoryResolver

這是選擇性的服務，可從指定的 DbConnection 物件取得正確的 DbProviderFactory。 所有提供者的 EF 所傳回的此服務的預設執行，都適用于所有提供者。 不過，在 .NET 4 上執行時，如果 DbProviderFactory 的 DbConnections，則無法從一個公開存取。 因此，EF 會使用一些啟發學習法來搜尋已註冊的提供者，以尋找相符的。 某些提供者可能會因為這些啟發學習法而失敗，而在這種情況下，提供者應該提供新的執行。

## <a name="registering-dbproviderservices"></a>註冊 Dbproviderservices.createdatabase

要使用的 Dbproviderservices.createdatabase 執行方式可以在應用程式的設定檔（app.config 或 web.config）中註冊，或使用以程式碼為基礎的設定。 在任一情況下，註冊都會使用提供者的「不變名稱」做為索引鍵。 這可讓您在單一應用程式中註冊和使用多個提供者。 用於 EF 註冊的不變名稱與用於 ADO.NET 提供者註冊和連接字串的非變異名稱相同。 例如，針對 SQL Server 會使用不變的名稱 "SqlClient"。

### <a name="config-file-registration"></a>組態檔註冊

要使用的 Dbproviderservices.createdatabase 類型會在應用程式佈建檔之 entityFramework 區段的提供者清單中註冊為提供者元素。 例如:

``` xml
<entityFramework>
  <providers>
    <provider invariantName="My.Invariant.Name" type="MyProvider.MyProviderServices, MyAssembly" />
  </providers>
</entityFramework>
```

_類型_字串必須是要使用之 dbproviderservices.createdatabase 實作為元件限定的類型名稱。

### <a name="code-based-registration"></a>程式碼架構註冊

從開始，您也可以使用程式碼來註冊 EF6 提供者。 這可讓 EF 提供者使用，而不需要變更應用程式的設定檔。 若要使用以程式碼為基礎的設定，應用程式應該建立 DbConfiguration 類別，如以[程式碼為基礎](https://msdn.com/data/jj680699)的設定檔中所述。 然後，DbConfiguration 類別的函式應該呼叫 SetProviderServices 來註冊 EF 提供者。 例如:

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

如先前在_提供者類型總覽_一節中所述，dbproviderservices.createdatabase 類別也可以用來解析其他服務。 這是可行的，因為 Dbproviderservices.createdatabase 會執行 IDbDependencyResolver，而每個註冊的 Dbproviderservices.createdatabase 類型都會新增為「預設解析程式」。 相依性[解析](~/ef6/fundamentals/configuring/dependency-resolution.md)中會更詳細地說明 IDbDpendencyResolver 機制。 不過，不需要瞭解此規格中的所有概念，即可解析提供者中的其他服務。

提供者解決其他服務最常見的方式，是在 Dbproviderservices.createdatabase 類別的函式中呼叫每個服務的 Dbproviderservices.createdatabase AddDependencyResolver。 例如，SqlProviderServices （適用于 SQL Server 的 EF 提供者）的程式碼類似于初始化：

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

此函式會使用下列 helper 類別：

*   SingletonDependencyResolver：提供簡單的方法來解析單一服務，也就是每次呼叫 GetService 時，都會傳回相同實例的服務。 暫時性服務通常會註冊為單一處理站，以便在需要時用來建立暫時性實例。
*   ExecutionStrategyResolver：傳回 IExecutionStrategy 實作為特定的解決器。

不使用 Dbproviderservices.createdatabase. AddDependencyResolver，您也可以覆寫 Dbproviderservices.createdatabase GetService 並直接解析其他服務。 當 EF 需要由特定類型所定義的服務，以及在某些情況下，針對指定的索引鍵，就會呼叫這個方法。 方法應該會傳回服務（如果可以的話），或傳回 null 以選擇不要傳回服務，而是允許另一個類別加以解析。 例如，若要解決預設的連接處理站，GetService 中的程式碼可能看起來像這樣：

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

在應用程式的設定檔中註冊多個 Dbproviderservices.createdatabase 的實作為時，系統會依照列出的順序將它們新增為次要解析程式。 由於解析程式一律會新增至次要解析程式鏈的頂端，這表示清單結尾的提供者將有機會先解析相依性，然後再進行其他處理。 （這在一開始看起來就有點直覺，但如果您認為每個提供者都不在清單中，並將它堆疊在現有的提供者之上，這就是合理的作法）。

此順序通常並不重要，因為大部分的提供者服務都是提供者特有的，而且是由提供者不變名稱所 不過，針對不是由提供者非變異名稱或其他提供者特定索引鍵進行索引的服務，服務將會根據此順序解析。 例如，如果未在其他地方明確設定，則預設連線 factory 會來自鏈中的最上層提供者。

## <a name="additional-config-file-registrations"></a>其他設定檔案註冊

您可以明確地在應用程式的設定檔中直接註冊上述一些額外的提供者服務。 完成這項作業時，將會使用設定檔中的註冊，而不是 Dbproviderservices.createdatabase 實 GetService 方法所傳回的任何專案。

### <a name="registering-the-default-connection-factory"></a>註冊預設連接 factory

從 EF5 開始，EntityFramework NuGet 套件會自動在設定檔案中註冊 SQL Express 連線處理站或 LocalDb 連接 factory。

例如:

``` xml
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework" >
</entityFramework>
```

此_類型_是預設連接 factory 的元件限定類型名稱，必須實 IDbConnectionFactory。

建議提供者 NuGet 套件在安裝時以這種方式設定預設的連接 factory。 請參閱下列_提供者的 NuGet 套件_。

## <a name="additional-ef6-provider-changes"></a>其他 EF6 提供者變更

### <a name="spatial-provider-changes"></a>空間提供者變更

支援空間類型的提供者現在必須在衍生自 DbSpatialDataReader 的類別上，執行一些其他方法：

*   `public abstract bool IsGeographyColumn(int ordinal)`
*   `public abstract bool IsGeometryColumn(int ordinal)`

現有方法也有新的非同步版本，建議您將其覆寫為同步方法的預設實作為委派，因此不會以非同步方式執行：

*   `public virtual Task<DbGeography> GetGeographyAsync(int ordinal, CancellationToken cancellationToken)`
*   `public virtual Task<DbGeometry> GetGeometryAsync(int ordinal, CancellationToken cancellationToken)`

### <a name="native-support-for-enumerablecontains"></a>可列舉的原生支援。 Contains

EF6 引進了新的運算式類型 DbInExpression，其已加入來解決使用可列舉之時的效能問題。包含在 LINQ 查詢中。 DbProviderManifest 類別具有新的虛擬方法 SupportsInExpression，由 EF 呼叫以判斷提供者是否處理新的運算式類型。 為了與現有的提供者實現相容，此方法會傳回 false。 為了因應這項改進，EF6 提供者可以加入程式碼來處理 DbInExpression，並覆寫 SupportsInExpression 以傳回 true。 您可以藉由呼叫 DbExpressionBuilder.In 方法來建立 DbInExpression 的實例。 DbInExpression 實例是由 DbExpression 所組成，通常代表資料表資料行，而 DbConstantExpression 清單則是用來測試是否符合。

## <a name="nuget-packages-for-providers"></a>提供者的 NuGet 套件

提供 EF6 提供者的其中一種方式是將它發行為 NuGet 套件。 使用 NuGet 套件有下列優點：

*   使用 NuGet 將提供者註冊新增至應用程式的設定檔，是很容易的事
*   您可以對設定檔進行其他變更以設定預設連線處理站，讓慣例所建立的連線使用已註冊的提供者
*   NuGet 會處理新增系結重新導向，如此一來，即使在發行新的 EF 封裝之後，EF6 提供者仍應繼續工作

其中一個範例是包含在[開放原始碼程式碼基](https://github.com/aspnet/entityframework6)底中的 EntityFramework entityframework.sqlservercompact 套件。 此套件提供建立 EF 提供者 NuGet 套件的絕佳範本。

### <a name="powershell-commands"></a>PowerShell 命令

安裝 EntityFramework NuGet 套件時，它會註冊 PowerShell 模組，其中包含兩個對提供者套件非常有用的命令：

*   EFProvider 會在目標專案的設定檔中加入提供者的新實體，並確定它位於已註冊之提供者清單的結尾。
*   EFDefaultConnectionFactory 會在目標專案的設定檔中加入或更新 defaultConnectionFactory 註冊。

這兩個命令會負責將 entityFramework 區段新增至設定檔，並在必要時新增提供者集合。

其目的是要從 install NuGet 腳本呼叫這些命令。 例如，針對 SQL Compact 提供者安裝. ps1 看起來像這樣：

``` powershell
param($installPath, $toolsPath, $package, $project)
Add-EFDefaultConnectionFactory $project 'System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework' -ConstructorArguments 'System.Data.SqlServerCe.4.0'
Add-EFProvider $project 'System.Data.SqlServerCe.4.0' 'System.Data.Entity.SqlServerCompact.SqlCeProviderServices, EntityFramework.SqlServerCompact'</pre>
```

您可以使用 [套件管理員主控台] 視窗中的 get-help 來取得有關這些命令的詳細資訊。

## <a name="wrapping-providers"></a>包裝提供者

包裝提供者是一個 EF 和/或 ADO.NET 提供者，它會包裝現有的提供者，以利用其他功能（例如分析或追蹤功能）來擴充它。 包裝提供者可以用一般方式註冊，但是在執行時間設定包裝提供者通常會藉由攔截提供者相關服務的解析來方便。 DbConfiguration 類別上的靜態事件 OnLockingConfiguration 可以用來執行此動作。

當 EF 判斷出應用程式域的所有 EF 設定會從何處取得，但在鎖定以供使用之前，會呼叫 OnLockingConfiguration。 在應用程式啟動時（使用 EF 之前），應用程式應該註冊此事件的事件處理常式。 （我們正在考慮新增在設定檔案中註冊此處理程式的支援，但尚未支援）。接著，事件處理常式應該對每個需要包裝的服務呼叫 ReplaceService。  

例如，若要包裝 IDbConnectionFactory 和 DbProviderService，應該註冊處理常式，如下所示：

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

已解析的服務，現在應該與用來解析服務的金鑰一起包裝在一起，會傳遞至處理常式。 然後，處理常式可以包裝此服務，並以包裝的版本取代傳回的服務。

## <a name="resolving-a-dbproviderfactory-with-ef"></a>使用 EF 解析 DbProviderFactory

DbProviderFactory 是 EF 所需的其中一個基本提供者類型，如上述_提供者類型總覽_一節中所述。 如先前所述，它不是 EF 型別，而且註冊通常不是 EF 設定的一部分，而是在 machine.config 檔案和/或應用程式的設定檔中註冊一般 ADO.NET 提供者。

儘管此 EF 在尋找要使用的 DbProviderFactory 時仍會使用其一般相依性解析機制。 預設解析程式會使用設定檔中的一般 ADO.NET 註冊，因此這通常是透明的。 但由於使用一般的相依性解析機制，因此即使尚未完成一般的 ADO.NET 註冊，也可以使用 IDbDependencyResolver 來解析 DbProviderFactory。

以這種方式解析 DbProviderFactory 有幾個含意：

*   使用以程式碼為基礎之設定的應用程式可以在其 DbConfiguration 類別中新增呼叫，以註冊適當的 DbProviderFactory。 這特別適用于不想要（或無法）完全使用任何檔案型設定的應用程式。
*   您可以使用 ReplaceService 來包裝或取代此服務，如上面的_包裝提供者_一節所述
*   理論上，Dbproviderservices.createdatabase 的執行方式可以解析 DbProviderFactory。

要注意的是，這些事項的重點在於，它們只會影響 EF 的 DbProviderFactory 查閱。 其他非 EF 程式碼仍可能預期以正常方式註冊 ADO.NET 提供者，如果找不到註冊，可能會失敗。 基於這個理由，通常會更適合以一般 ADO.NET 方式來註冊 DbProviderFactory。

### <a name="related-services"></a>相關服務

如果使用 EF 來解析 DbProviderFactory，則也應該解析 IProviderInvariantName 和 IDbProviderFactoryResolver 服務。

IProviderInvariantName 是一項服務，用來判斷特定 DbProviderFactory 類型的提供者不變名稱。 此服務的預設執行會使用 ADO.NET 提供者註冊。 這表示，如果 ADO.NET 提供者未以一般方式註冊，因為 DbProviderFactory 正由 EF 解決，則也需要解析此服務。 請注意，使用 DbConfiguration. SetProviderFactory 方法時，會自動加入此服務的解析程式。

如上面的「_提供者類型總覽_」一節所述，IDbProviderFactoryResolver 是用來從指定的 DbConnection 物件取得正確的 DbProviderFactory。 在 .NET 4 上執行時，此服務的預設值會使用 ADO.NET 提供者註冊。 這表示，如果 ADO.NET 提供者未以一般方式註冊，因為 DbProviderFactory 正由 EF 解決，則也需要解析此服務。
