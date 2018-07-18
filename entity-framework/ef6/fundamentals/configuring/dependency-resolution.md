---
title: 相依性解析-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 32d19ac6-9186-4ae1-8655-64ee49da55d0
caps.latest.revision: 3
ms.openlocfilehash: 88fd859b4f9a8069eeb08f32bb1d35ddcd21aec5
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "39120567"
---
# <a name="dependency-resolution"></a>相依性解析
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。  

從 EF6 開始，Entity Framework，包含一般用途的機制，來取得它所需要的服務的實作。 也就是說，EF 使用某些介面或基底類別的執行個體時它會要求提供的介面或基底類別的具體實作使用。 這是透過使用 IDbDependencyResolver 介面來達成：  

``` csharp
public interface IDbDependencyResolver
{
    object GetService(Type type, object key);
}
```  

GetService 方法通常會呼叫 EF，以及處理 IDbDependencyResolver 由 EF 或應用程式所提供的實作。 呼叫時，型別引數是所要求之服務的介面或基底類別型別和索引鍵的物件為 null 或這個物件提供內容資訊要求的服務。  

這篇文章不包含有關如何實作 IDbDependencyResolver，完整詳細資料，但會做為其 EF 呼叫 GetService 和語意索引鍵物件的每一種服務類型 （也就是，介面和基底類別類型） 的參考呼叫。 這份文件會保持最新狀態新增其他服務。  

## <a name="services-resolved"></a>判斷已解決的服務  

除非另有指明傳回任何物件必須是安全執行緒，因為它可用來當做單一值。 在許多情況下，在此情況下是處理站時，傳回的物件 factory 本身必須是安全執行緒，但不需要從 factory 所傳回的物件，這是為安全執行緒，因為每次使用的處理站所要求的新執行個體。  

### <a name="systemdataentityidatabaseinitializertcontext"></a>System.Data.Entity.IDatabaseInitializer < TContext\>  

**版本導入**: EF6.0.0  

**傳回物件**： 指定的內容類型的資料庫初始設定式  

**索引鍵**： 無法使用，將會是 null  

### <a name="funcsystemdataentitymigrationssqlmigrationsqlgenerator"></a>L o c k < System.Data.Entity.Migrations.Sql.MigrationSqlGenerator\>  

**版本導入**: EF6.0.0

**傳回物件**： 若要建立可用來移轉和其他動作，會造成要建立的例如資料庫初始設定式與資料庫建立資料庫的 SQL 產生器的 factory。  

**索引鍵**： 字串，包含指定的資料庫會為其產生 SQL 類型的 ADO.NET 的提供者非變異名稱。 例如，SQL Server SQL 產生器會傳回"System.Data.SqlClient"索引鍵。  

>[!NOTE]
> 如需在 EF6 中的提供者相關的服務的詳細資訊，請參閱[EF6 提供者模型](~/ef6/fundamentals/providers/provider-model.md)一節。  

### <a name="systemdataentitycorecommondbproviderservices"></a>System.Data.Entity.Core.Common.DbProviderServices  

**版本導入**: EF6.0.0  

**傳回物件**: EF 的提供者，以使用指定的提供者非變異名稱  

**索引鍵**： 字串，包含 ADO.NET 提供者非變異名稱指定的資料庫提供者所需的類型。 例如，SQL Server 提供者會傳回"System.Data.SqlClient"索引鍵。  

>[!NOTE]
> 如需在 EF6 中的提供者相關的服務的詳細資訊，請參閱[EF6 提供者模型](~/ef6/fundamentals/providers/provider-model.md)一節。  

### <a name="systemdataentityinfrastructureidbconnectionfactory"></a>System.Data.Entity.Infrastructure.IDbConnectionFactory  

**版本導入**: EF6.0.0  

**傳回物件**： 將會依照慣例使用時，EF 會建立資料庫連接的連接 factory。 也就是當沒有連接或連接字串提供給 EF，和任何連接字串可在 app.config 或 web.config，然後此服務建立連線依照慣例會使用。 變更連線處理站可以預設為允許 EF 來使用不同類型的資料庫 (例如，SQL Server Compact Edition)。  

**索引鍵**： 無法使用，將會是 null  

>[!NOTE]
> 如需在 EF6 中的提供者相關的服務的詳細資訊，請參閱[EF6 提供者模型](~/ef6/fundamentals/providers/provider-model.md)一節。  

### <a name="systemdataentityinfrastructureimanifesttokenservice"></a>System.Data.Entity.Infrastructure.IManifestTokenService  

**版本導入**: EF6.0.0  

**傳回物件**： 一項服務，可以產生從連接的提供者資訊清單語彙基元。 這項服務通常用於兩種方式。 首先，它可以用來避免 Code First 建立模型時，連接到資料庫。 第二，它可以用來強制執行 Code First 至強制適用於 SQL Server 2005 的模型，不論是否使用 SQL Server 2008 的有時建置特定的資料庫版本--例如，模型。  

**物件存留期**： 單一-相同的物件可能會使用多個時間，而且同時由不同的執行緒  

**索引鍵**： 無法使用，將會是 null  

### <a name="systemdataentityinfrastructureidbproviderfactoryservice"></a>System.Data.Entity.Infrastructure.IDbProviderFactoryService  

**版本導入**: EF6.0.0  

**傳回物件**： 可以從指定的連接中取得提供者 factory 的服務。 在.NET 4.5 提供者是可公開存取的連接。 在.NET 4 上這項服務的預設實作會使用一些啟發學習法，來尋找相符的提供者。 如果這些然後失敗，此服務的新實作，都可以註冊以提供適當的解決。  

**索引鍵**： 無法使用，將會是 null  

### <a name="funcdbcontext-systemdataentityinfrastructureidbmodelcachekey"></a>L o c k < DbContext、 System.Data.Entity.Infrastructure.IDbModelCacheKey\>  

**版本導入**: EF6.0.0  

**傳回物件**： 將會產生指定的內容模型快取索引鍵的處理站。 根據預設，EF 會快取每個 DbContext 類型，每個提供者的另一個模型。 這項服務的不同實作可用來將其他資訊，例如結構描述名稱新增至快取索引鍵。  

**索引鍵**： 無法使用，將會是 null  

### <a name="systemdataentityspatialdbspatialservices"></a>System.Data.Entity.Spatial.DbSpatialServices  

**版本導入**: EF6.0.0  

**傳回物件**: EF 空間加入提供者，支援之基本的 EF 提供者的 geography 和 geometry 空間類型。  

**索引鍵**: DbSptialServices 要求有兩種。 首先，提供者特定的空間服務要求使用 DbProviderInfo 物件 (其中包含非變異名稱和資訊清單語彙基元) 做為索引鍵。 第二，DbSpatialServices 可要求沒有索引鍵。 這用來解決 「 全域空間提供者 」 來建立獨立的 DbGeography 或 DbGeometry 型別時使用。  

>[!NOTE]
> 如需在 EF6 中的提供者相關的服務的詳細資訊，請參閱[EF6 提供者模型](~/ef6/fundamentals/providers/provider-model.md)一節。  

### <a name="funcsystemdataentityinfrastructureidbexecutionstrategy"></a>L o c k < System.Data.Entity.Infrastructure.IDbExecutionStrategy\>  

**版本導入**: EF6.0.0  

**傳回物件**： 若要建立服務，可讓提供者實作重試或其他行為，對資料庫執行查詢和命令時的處理站。 如果不提供實作，然後 EF 將只執行命令，並傳播擲回任何例外狀況。 適用於 SQL Server 會提供重試原則，也就是特別有用，例如 SQL Azure 的雲端式資料庫伺服器上執行時使用此服務。  

**索引鍵**: ExecutionStrategyKey 物件，包含提供者非變異名稱和選擇性的執行策略會使用伺服器名稱。  

>[!NOTE]
> 如需在 EF6 中的提供者相關的服務的詳細資訊，請參閱[EF6 提供者模型](~/ef6/fundamentals/providers/provider-model.md)一節。  

### <a name="funcdbconnection-string-systemdataentitymigrationshistoryhistorycontext"></a>L o c k < DbConnection，System.Data.Entity.Migrations.History.HistoryContext 的字串\>  

**版本導入**: EF6.0.0  

**傳回的物件**： 允許設定至 HistoryContext 對應提供者 factory`__MigrationHistory`使用 EF 移轉的資料表。 HistoryContext 是程式碼的第一個 DbContext，可以使用一般的 fluent API 來變更資料表和資料行對應規格的名稱等項目進行設定。  

**索引鍵**： 無法使用，將會是 null  

>[!NOTE]
> 如需在 EF6 中的提供者相關的服務的詳細資訊，請參閱[EF6 提供者模型](~/ef6/fundamentals/providers/provider-model.md)一節。  

### <a name="systemdatacommondbproviderfactory"></a>System.Data.Common.DbProviderFactory  

**版本導入**: EF6.0.0  

**傳回物件**: ADO.NET 提供者，以使用指定的提供者非變異名稱。  

**索引鍵**： 包含 ADO.NET 提供者非變異名稱的字串  

>[!NOTE]
> 這項服務是不通常直接變更因為預設實作會使用一般的 ADO.NET 提供者註冊。 如需在 EF6 中的提供者相關的服務的詳細資訊，請參閱[EF6 提供者模型](~/ef6/fundamentals/providers/provider-model.md)一節。  

### <a name="systemdataentityinfrastructureiproviderinvariantname"></a>System.Data.Entity.Infrastructure.IProviderInvariantName  

**版本導入**: EF6.0.0  

**傳回物件**： 一項服務，用來判斷 DbProviderFactory 指定類型的提供者非變異名稱。 這項服務的預設實作會使用 ADO.NET 提供者註冊。 這表示，如果因為 EF 所解析的 DbProviderFactory，ADO.NET 提供者不會以一般方式註冊，則它也會解決這項服務所需的。  

**索引鍵**: DbProviderFactory 的非變異名稱是必要的執行個體。  

>[!NOTE]
> 如需在 EF6 中的提供者相關的服務的詳細資訊，請參閱[EF6 提供者模型](~/ef6/fundamentals/providers/provider-model.md)一節。  

### <a name="systemdataentitycoremappingviewgenerationiviewassemblycache"></a>System.Data.Entity.Core.Mapping.ViewGeneration.IViewAssemblyCache  

**版本導入**: EF6.0.0  

**傳回物件**： 包含預先產生的檢視組件快取。 替代項目通常用來讓 EF 知道哪些組件包含預先產生的檢視，而不進行任何探索。  

**索引鍵**： 無法使用，將會是 null  

### <a name="systemdataentityinfrastructurepluralizationipluralizationservice"></a>System.Data.Entity.Infrastructure.Pluralization.IPluralizationService

**版本導入**: EF6.0.0  

**傳回物件**: EF 用複數化和單數化名稱的服務。 根據預設會使用英文的複數表示服務。  

**索引鍵**： 無法使用，將會是 null  

### <a name="systemdataentityinfrastructureinterceptionidbinterceptor"></a>System.Data.Entity.Infrastructure.Interception.IDbInterceptor  

**版本導入**: EF6.0.0

**傳回物件**： 應該在應用程式啟動時註冊任何攔截器。 請注意，這些物件會要求使用 GetServices 呼叫和任何相依性解析程式所傳回的所有攔截器會登錄。

**索引鍵**： 無法使用，將會是 null。  

### <a name="funcsystemdataentitydbcontext-actionstring-systemdataentityinfrastructureinterceptiondatabaselogformatter"></a>L o c k < System.Data.Entity.DbContext、 動作 < 字串\>，System.Data.Entity.Infrastructure.Interception.DatabaseLogFormatter\>  

**版本導入**: EF6.0.0  

**傳回物件**： 將建立資料庫記錄檔格式器將會使用的處理站時所使用的內容。指定的內容上設定 Database.Log 屬性。  

**索引鍵**： 無法使用，將會是 null。  

### <a name="funcsystemdataentitydbcontext"></a>L o c k < System.Data.Entity.DbContext\>  

**版本導入**: EF6.1.0  

**傳回物件**： 用以建立移轉的內容執行個體，當內容並沒有可存取的無參數建構函式的處理站。  

**索引鍵**： 需要原廠的衍生 dbcontext 類型的型別物件。  

### <a name="funcsystemdataentitycoremetadataedmimetadataannotationserializer"></a>L o c k < System.Data.Entity.Core.Metadata.Edm.IMetadataAnnotationSerializer\>  

**版本導入**: EF6.1.0  

**傳回物件**： 用以建立強型別自訂註釋序列化的序列化程式，使它們可以序列化，並且用於 Code First 移轉 desterilized 成 XML 的處理站。  

**索引鍵**： 正在註釋名稱序列化或還原序列化。  

### <a name="funcsystemdataentityinfrastructuretransactionhandler"></a>L o c k < System.Data.Entity.Infrastructure.TransactionHandler\>  

**版本導入**: EF6.1.0  

**傳回物件**： 用以建立交易的處理常式，以便可以套用特殊處理，例如處理認可失敗的情況下的處理站。  

**索引鍵**: ExecutionStrategyKey 物件，包含提供者非變異名稱和選擇性地將使用的交易處理常式的伺服器名稱。  
