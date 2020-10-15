---
title: Entity Framework 6 提供者模型-EF6
description: Entity Framework 6 中的 Entity Framework 6 提供者模型
author: ajcvickers
ms.date: 06/27/2018
uid: ef6/fundamentals/providers/provider-model
ms.openlocfilehash: 15b5443ff05b5c8704f80d4f2f83b4ed20edd1c0
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063188"
---
# <a name="the-entity-framework-6-provider-model"></a>Entity Framework 6 提供者模型

Entity Framework 提供者模型可讓 Entity Framework 搭配不同類型的資料庫伺服器使用。 例如，您可以插入一個提供者，以允許 EF 用於 Microsoft SQL Server，而另一個提供者則可以插入，以允許 EF 用於 Microsoft SQL Server Compact 版本。 我們知道的 EF6 提供者可在 [ [Entity Framework 提供者](xref:ef6/fundamentals/providers/index) ] 頁面上找到。

EF 與提供者互動的方式需要進行某些變更，才能讓 EF 以開放原始碼授權發行。 這些變更需要針對 EF6 元件重建 EF 提供者，以及註冊提供者的新機制。

## <a name="rebuilding"></a>重建

EF6 先前一部分 .NET Framework 的核心程式代碼，現在會以頻外 (OOB) 元件的形式傳送。 如需有關如何針對 EF6 建立應用程式的詳細資訊，請參閱 [EF6 的更新應用程式](xref:ef6/what-is-new/upgrading-to-ef6) 頁面。 您也必須使用這些指示來重建提供者。

## <a name="provider-types-overview"></a>提供者類型總覽

EF 提供者實際上是由 CLR 型別所定義之提供者專屬服務的集合，這些服務會從基類的 (延伸這些服務) 或針對介面) 執行 (。 其中兩個服務是 EF 正常運作的基本和必要服務。 其他則是選擇性的，只有在需要特定功能，且（或）這些服務的預設執行無法用於目標的特定資料庫伺服器時，才需要執行。

## <a name="fundamental-provider-types"></a>基本提供者類型

### <a name="dbproviderfactory"></a>DbProviderFactory

EF 相依于具有衍生自 [DbProviderFactory](https://msdn.microsoft.com/library/system.data.common.dbproviderfactory.aspx) 的類型，以執行所有低層級的資料庫存取。 DbProviderFactory 實際上不是 EF 的一部分，而是 .NET Framework 中的類別，可提供 ADO.NET 提供者的進入點，供 EF、其他 O/RMs 或應用程式直接使用，以提供者中立的方式取得連接、命令、參數和其他 ADO.NET 抽象的實例。 如需 DbProviderFactory 的詳細資訊，請參閱 [MSDN 檔中的 ADO.NET](https://msdn.microsoft.com/library/a6cd7c08.aspx)。

### <a name="dbproviderservices"></a>Dbproviderservices.createdatabase

EF 相依于具有衍生自 Dbproviderservices.createdatabase 的類型，以便在 ADO.NET 提供者所提供的功能之上提供 EF 所需的額外功能。 在舊版 EF 中，Dbproviderservices.createdatabase 類別是 .NET Framework 的一部分，而且是在 system.string 命名空間中找到。 從 EF6 開始，這個類別現在是 EntityFramework.dll 的一部分，而且位於 system.string. Common 命名空間中。

您可以在 [MSDN](https://msdn.microsoft.com/library/ee789835.aspx)上找到有關 dbproviderservices.createdatabase 執行基礎功能的詳細資料。 不過請注意，在撰寫本文時，這項資訊不會更新為 EF6，不過大部分的概念仍然是有效的。 Dbproviderservices.createdatabase 的 SQL Server 和 SQL Server Compact 執行也會簽入 [開放原始碼程式碼基](https://github.com/aspnet/EntityFramework6/) 底，並可作為其他實作為的實用參考。

在較舊版本的 EF 中，直接從 ADO.NET 提供者取得要使用的 Dbproviderservices.createdatabase 實行。 這是藉由將 DbProviderFactory 轉換為 IServiceProvider 和呼叫 GetService 方法來完成。 這會緊密結合 EF 提供者與 DbProviderFactory。 這項結合封鎖的 EF 無法從 .NET Framework 移出，因此 EF6 這項緊密結合已移除，而且現在會將 Dbproviderservices.createdatabase 的執行方式直接註冊到應用程式的設定檔或以程式碼為基礎的設定，如下面的 _註冊 dbproviderservices.createdatabase_ 一節中所述。

## <a name="additional-services"></a>其他服務

除了上面所述的基本服務之外，EF 也有許多其他服務，也就是永遠或有時提供者特定的服務。 Dbproviderservices.createdatabase 實行可提供這些服務的預設提供者特定的實值。 應用程式也可以覆寫這些服務的執行方式，或在 Dbproviderservices.createdatabase 類型未提供預設值時提供執行。 以下將更詳細說明下文的「 _解決其他服務_ 」一節。

提供者可能會對提供者感興趣的其他服務類型如下所示。 您可以在 API 檔中找到每個服務類型的詳細資料。

### <a name="idbexecutionstrategy"></a>>idbexecutionstrategy

這是選擇性的服務，可讓提供者在對資料庫執行查詢和命令時，執行重試或其他行為。 如果未提供任何執行，則 EF 只會執行命令並傳播任何擲回的例外狀況。 針對 SQL Server，此服務會用來提供重試原則，此原則在針對雲端式資料庫伺服器（例如 SQL Azure）執行時特別有用。

### <a name="idbconnectionfactory"></a>IDbConnectionFactory

這是選擇性的服務，可讓提供者依慣例，在只提供資料庫名稱時建立 DbConnection 物件。 請注意，雖然這項服務可由 EF 4.1 的 Dbproviderservices.createdatabase 執行解決，而且也可以在設定檔或程式碼中明確設定。 提供者只有在註冊為預設提供者時，才有機會解決此服務 (請參閱下方 _的預設提供者_) ，以及是否未在其他地方設定預設的連接 factory。

### <a name="dbspatialservices"></a>DbSpatialServices

這是選擇性的服務，可讓提供者加入地理位置和幾何空間類型的支援。 必須提供此服務的執行，應用程式才能搭配使用 EF 與空間類型。 系統會以兩種方式要求 DbSptialServices。 首先，系統會使用 DbProviderInfo 物件要求提供者專屬的空間服務， (其中包含非變異名稱和資訊清單 token) 做為索引鍵。 其次，DbSpatialServices 可以要求沒有金鑰的要求。 這是用來解析建立獨立 DbGeography 或 DbGeometry 類型時所使用的「全域空間提供者」。

### <a name="migrationsqlgenerator"></a>MigrationSqlGenerator

這是選用的服務，可讓您在 Code First 建立和修改資料庫架構時，使用 EF 遷移來產生 SQL。 需要執行才能支援遷移。 如果有提供執行，則在使用資料庫初始化運算式或資料庫建立資料庫時，也會使用此方法。

### <a name="funcdbconnection-string-historycontextfactory"></a>Func<DbConnection，string，HistoryCoNtextFactory>

這是選擇性的服務，可讓提供者將 HistoryCoNtext 對應設定為 `__MigrationHistory` EF 遷移所使用的資料表。 HistoryCoNtext 是 Code First 的 DbCoNtext，而且可以使用一般的流暢 API 來設定，以變更資料表名稱和資料行對應規格等專案。 如果該提供者支援所有預設資料表和資料行對應，則所有提供者的 EF 所傳回的此服務預設執行可能適用于指定的資料庫伺服器。 在這種情況下，提供者不需要提供此服務的執行。

### <a name="idbproviderfactoryresolver"></a>IDbProviderFactoryResolver

這是選擇性的服務，可從指定的 DbConnection 物件取得正確的 DbProviderFactory。 由 EF 針對所有提供者所傳回的這項服務預設執行，適用于所有提供者。 不過，在 .NET 4 上執行時，如果 DbProviderFactory 的 DbConnections，則無法從一個公用程式公開存取。 因此，EF 會使用一些啟發學習法來搜尋已註冊的提供者，以尋找相符項。 某些提供者可能會導致這些啟發學習法失敗，而且在這種情況下，提供者應該提供新的執行。

## <a name="registering-dbproviderservices"></a>註冊 Dbproviderservices.createdatabase

要使用的 Dbproviderservices.createdatabase 實行可以在應用程式的設定檔中註冊 ( # A0 或 web.config) 或使用以程式碼為基礎的設定。 在任一種情況下，註冊會使用提供者的「非變異名稱」作為索引鍵。 這可讓您在單一應用程式中註冊和使用多個提供者。 用於 EF 註冊的非變異名稱，與用於 ADO.NET 提供者註冊和連接字串的非變異名稱相同。 例如，針對 SQL Server 會使用不變的名稱 "SqlClient"。

### <a name="config-file-registration"></a>組態檔註冊

要使用的 Dbproviderservices.createdatabase 類型會在應用程式佈建檔的 [entityFramework] 區段的 [提供者] 清單中註冊為提供者元素。 例如︰

``` xml
<entityFramework>
  <providers>
    <provider invariantName="My.Invariant.Name" type="MyProvider.MyProviderServices, MyAssembly" />
  </providers>
</entityFramework>
```

_類型_字串必須是要使用之 dbproviderservices.createdatabase 的實作為元件限定型別名稱。

### <a name="code-based-registration"></a>程式碼架構註冊

從 EF6 提供者開始也可以使用程式碼進行註冊。 這允許使用 EF 提供者，而不需要變更應用程式的設定檔。 若要使用以程式碼為基礎的設定，應用程式應建立 >dbconfiguration 類別，如以程式 [代碼為基礎](https://msdn.com/data/jj680699)的設定檔中所述。 然後，>dbconfiguration 類別的函式應該呼叫 SetProviderServices 以註冊 EF 提供者。 例如︰

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

如同上述的「 _提供者類型總覽_ 」一節所述，dbproviderservices.createdatabase 類別也可用來解析其他服務。 這是可能的，因為 Dbproviderservices.createdatabase 會執行 IDbDependencyResolver，而且每個已註冊的 Dbproviderservices.createdatabase 類型都會新增為「預設解析程式」。 相依性 [解析](xref:ef6/fundamentals/configuring/dependency-resolution)會更詳細地說明 IDbDpendencyResolver 機制。 不過，您不需要瞭解此規格中的所有概念，就可以解析提供者中的其他服務。

提供者解決其他服務的最常見方式，是針對 Dbproviderservices.createdatabase 類別的函式中的每個服務呼叫 Dbproviderservices.createdatabase. AddDependencyResolver。 例如，SqlProviderServices (SQL Server) 的 EF 提供者具有類似以下的程式碼來進行初始化：

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

這個函式會使用下列 helper 類別：

*   SingletonDependencyResolver：提供簡單的方法來解決單一服務，也就是每次呼叫 GetService 時都會傳回相同實例的服務。 暫時性服務通常會註冊為單一處理站，用來視需要建立暫時性實例。
*   ExecutionStrategyResolver：傳回 IExecutionStrategy 執行的特定解析程式。

除了使用 Dbproviderservices.createdatabase 之外，您也可以覆寫 Dbproviderservices.createdatabase GetService 並直接解析其他服務。 當 EF 需要特定類型所定義的服務，而在某些情況下，則會呼叫這個方法，以指定的索引鍵。 方法應該會傳回服務（若有的話），或傳回 null 以選擇不傳回服務，而改為允許另一個類別加以解析。 例如，若要解決預設的連接 factory，GetService 中的程式碼可能看起來像這樣：

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

在應用程式的設定檔中註冊多個 Dbproviderservices.createdatabase 執行時，系統會依照列出的順序將它們新增為次要解析程式。 由於解析程式一律會新增至次要解析程式鏈的頂端，這表示清單結尾的提供者有機會先解析相依性，再進行其他工作。  (這在一開始看起來有點直覺，但如果您想像出每個提供者都不在清單中，並在現有提供者上將它堆疊，就會有意義。 ) 

這種排序通常不重要，因為大部分的提供者服務都是提供者專屬的，而且是由提供者的非變異名稱 不過，針對未以提供者非變異名稱或某些其他提供者特定索引鍵進行索引的服務，將會根據此順序來解析服務。 例如，如果沒有在其他地方明確設定，則預設的連接 factory 會來自鏈中最上層的提供者。

## <a name="additional-config-file-registrations"></a>其他設定檔註冊

您可以直接在應用程式的設定檔中，明確地註冊上述的部分其他提供者服務。 完成此作業時，將會使用設定檔中的註冊，而不是 Dbproviderservices.createdatabase 執行的 GetService 方法所傳回的任何事物。

### <a name="registering-the-default-connection-factory"></a>註冊預設連接 factory

從 EF5 開始，EntityFramework NuGet 套件會自動在設定檔中註冊 SQL Express 連接處理站或 LocalDb 連接 factory。

例如︰

``` xml
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework" >
</entityFramework>
```

_類型_是預設連接 factory 的元件限定類型名稱，必須執行 IDbConnectionFactory。

建議您在安裝時，提供者 NuGet 套件以這種方式設定預設的連接 factory。 請參閱下面 _提供者的 NuGet 套件_ 。

## <a name="additional-ef6-provider-changes"></a>其他 EF6 提供者變更

### <a name="spatial-provider-changes"></a>空間提供者變更

支援空間類型的提供者現在必須在衍生自 DbSpatialDataReader 的類別上執行一些額外的方法：

*   `public abstract bool IsGeographyColumn(int ordinal)`
*   `public abstract bool IsGeometryColumn(int ordinal)`

此外，也有新的非同步版本的現有方法，建議您將其覆寫為同步方法的預設執行委派，因此不會以非同步方式執行：

*   `public virtual Task<DbGeography> GetGeographyAsync(int ordinal, CancellationToken cancellationToken)`
*   `public virtual Task<DbGeometry> GetGeometryAsync(int ordinal, CancellationToken cancellationToken)`

### <a name="native-support-for-enumerablecontains"></a>可列舉的原生支援。包含

EF6 導入了新的運算式類型 DbInExpression，其已加入以解決使用可列舉之的效能問題。包含在 LINQ 查詢中。 DbProviderManifest 類別具有新的虛擬方法 SupportsInExpression，由 EF 呼叫以判斷提供者是否處理新的運算式類型。 為了與現有提供者的實現相容，此方法會傳回 false。 為了受益于這項改進，EF6 提供者可以加入程式碼來處理 DbInExpression 和覆寫 SupportsInExpression，以傳回 true。 您可以藉由呼叫 DbExpressionBuilder.In 方法來建立 DbInExpression 的實例。 DbInExpression 實例是由 DbExpression 所組成，通常代表資料表資料行，以及要測試是否相符的 DbConstantExpression 清單。

## <a name="nuget-packages-for-providers"></a>提供者的 NuGet 套件

提供 EF6 提供者的其中一種方式是將它發行為 NuGet 套件。 使用 NuGet 套件具有下列優點：

*   使用 NuGet 將提供者註冊新增到應用程式的設定檔中很容易
*   您可以對設定檔進行額外的變更，以設定預設連線處理站，讓依慣例進行的連接會使用已註冊的提供者
*   NuGet 會處理新增系結重新導向，因此即使在發行新的 EF 套件之後，EF6 提供者仍應繼續運作

其中一個範例是包含在 [開放原始碼程式碼基](https://github.com/aspnet/entityframework6)底中的 EntityFramework. entityframework.sqlservercompact 套件。 此套件提供建立 EF provider NuGet 套件的良好範本。

### <a name="powershell-commands"></a>PowerShell 命令

安裝 EntityFramework NuGet 套件時，它會註冊 PowerShell 模組，其中包含兩個對提供者套件非常實用的命令：

*   Add-EFProvider 會在目標專案的設定檔中新增提供者的新實體，並確定它位於已註冊之提供者清單的結尾。
*   Add-EFDefaultConnectionFactory 在目標專案的設定檔中新增或更新 defaultConnectionFactory 註冊。

這兩個命令會負責將 entityFramework 區段新增至設定檔，並視需要新增提供者集合。

其目的是要從 install.ps1 NuGet 腳本呼叫這些命令。 例如，SQL Compact 提供者的 install.ps1 看起來像這樣：

``` powershell
param($installPath, $toolsPath, $package, $project)
Add-EFDefaultConnectionFactory $project 'System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework' -ConstructorArguments 'System.Data.SqlServerCe.4.0'
Add-EFProvider $project 'System.Data.SqlServerCe.4.0' 'System.Data.Entity.SqlServerCompact.SqlCeProviderServices, EntityFramework.SqlServerCompact'</pre>
```

您可以在封裝管理員主控台視窗中使用 get-help 來取得這些命令的詳細資訊。

## <a name="wrapping-providers"></a>包裝提供者

包裝提供者是一種 EF 和/或 ADO.NET 提供者，它會包裝現有的提供者，以使用其他功能（例如分析或追蹤功能）進行擴充。 包裝提供者可以用一般方式註冊，但是在執行時間，藉由攔截提供者相關服務的解決方式，在執行時間設定包裝提供者通常會更方便。 您可以使用 >dbconfiguration 類別上的靜態事件 OnLockingConfiguration 來進行此作業。

在 EF 判斷出應用程式網域的所有 EF 設定，但在它被鎖定以供使用之前，會呼叫 OnLockingConfiguration。 在應用程式啟動時 (使用 EF 之前) 應用程式應該註冊這個事件的事件處理常式。  (我們正在考慮在設定檔中新增登錄此處理程式的支援，但目前尚不支援此程式。 ) 事件處理常式應該對需要包裝的每個服務呼叫 ReplaceService。  

例如，若要包裝 IDbConnectionFactory 和 DbProviderService，則應該註冊像這樣的處理常式：

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

已解決的服務，現在應該與用來解析服務的金鑰一起包裝在一起，並傳遞給處理常式。 然後，此處理程式可以包裝此服務，並以包裝版本取代傳回的服務。

## <a name="resolving-a-dbproviderfactory-with-ef"></a>使用 EF 解析 DbProviderFactory

DbProviderFactory 是 EF 所需的其中一個基本提供者類型，如上述的 _提供者類型總覽_ 一節所述。 如先前所述，它不是 EF 型別，而且註冊通常不是 EF 設定的一部分，而是在 machine.config 檔和/或應用程式的設定檔中註冊一般的 ADO.NET 提供者。

儘管在尋找要使用的 DbProviderFactory 時，此 EF 仍使用其一般相依性解析機制。 預設解析程式會在設定檔中使用一般 ADO.NET 註冊，因此這通常是透明的。 但由於使用的是一般的相依性解析機制，因此 IDbDependencyResolver 可以用來解析 DbProviderFactory，即使尚未完成一般的 ADO.NET 註冊也是一樣。

以這種方式解決 DbProviderFactory 有幾個含意：

*   使用以程式碼為基礎之設定的應用程式可以在其 >dbconfiguration 類別中加入呼叫，以註冊適當的 DbProviderFactory。 對於不想 (或根本無法) 利用任何檔案型設定的應用程式而言，這特別有用。
*   您可以使用 ReplaceService 包裝或取代此服務，如上面的 _包裝提供者_ 一節所述
*   理論上，Dbproviderservices.createdatabase 的執行可以解析 DbProviderFactory。

要注意的是，要注意的一點是，它們只會影響 EF 的 DbProviderFactory 查閱。 其他非 EF 程式碼可能仍預期以一般方式註冊 ADO.NET 提供者，如果找不到註冊，可能會失敗。 基於這個理由，通常較適合以一般 ADO.NET 方式註冊 DbProviderFactory。

### <a name="related-services"></a>相關服務

如果使用 EF 來解析 DbProviderFactory，則它也應該解析 IProviderInvariantName 和 IDbProviderFactoryResolver 服務。

IProviderInvariantName 是一種服務，可用來判斷指定 DbProviderFactory 類型的提供者非變異名稱。 這項服務的預設執行會使用 ADO.NET 提供者註冊。 這表示，如果 ADO.NET 提供者未以正常方式註冊，因為 EF 正在解析 DbProviderFactory，則也需要解析這項服務。 請注意，使用 >dbconfiguration. SetProviderFactory 方法時，會自動新增這項服務的解析程式。

如上面的「 _提供者類型總覽_ 」一節所述，IDbProviderFactoryResolver 是用來從指定的 DbConnection 物件取得正確的 DbProviderFactory。 在 .NET 4 上執行時，此服務的預設執行會使用 ADO.NET 提供者註冊。 這表示，如果 ADO.NET 提供者未以正常方式註冊，因為 EF 正在解析 DbProviderFactory，則也需要解析這項服務。
