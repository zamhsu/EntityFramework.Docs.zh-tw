---
title: 相依性解析-EF6
description: Entity Framework 6 中的相依性解析
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/dependency-resolution
ms.openlocfilehash: 24e932646f0c44f642bff3f6db321590abae1c8d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070545"
---
# <a name="dependency-resolution"></a>相依性解析
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。  

從 EF6 開始，Entity Framework 包含一般用途的機制，可取得所需的服務的實現。 也就是說，當 EF 使用某個介面或基類的實例時，會要求要使用的介面或基類的具體執行。 使用 IDbDependencyResolver 介面可達成此目的：  

``` csharp
public interface IDbDependencyResolver
{
    object GetService(Type type, object key);
}
```  

GetService 方法通常是由 EF 呼叫，並且是由 EF 或應用程式所提供的 IDbDependencyResolver 執行所處理。 當呼叫時，型別引數是所要求之服務的介面或基類型別，而索引鍵物件為 null 或提供有關所要求服務之內容資訊的物件。  

除非另有說明，否則傳回的任何物件都必須是安全線程，因為它可以當做 singleton 使用。 在許多情況下，傳回的物件是 factory，在此情況下，factory 本身必須是安全線程，但從處理站傳回的物件不需要是安全線程，因為從處理站要求新的實例以供每個使用。

本文不包含如何執行 IDbDependencyResolver 的完整詳細資料，而是做為服務類型的參考 (也就是，其 EF 會呼叫 GetService 的介面和基類) 類型，以及每個呼叫的索引鍵物件的語法。

## <a name="systemdataentityidatabaseinitializertcontext"></a>IDatabaseInitializer<的 >tcoNtext>\>  

**引進的版本**： ef 6.0。0  

**傳回的物件**：指定之內容類型的資料庫初始化運算式  

機**碼**：未使用;將會是 null  

## <a name="funcsystemdataentitymigrationssqlmigrationsqlgenerator"></a><的 Func。 MigrationSqlGenerator\>  

**引進的版本**： ef 6.0。0

**傳回的物件**：建立 SQL 產生器的處理站，可用來進行遷移，以及其他導致建立資料庫的動作，例如使用資料庫初始化運算式建立資料庫。  

索引**鍵**：包含 ADO.NET 提供者非變異名稱的字串，指定將產生 SQL 的資料庫類型。 例如，系統會針對機碼 "SqlClient" 傳回 SQL Server SQL 產生器。  

>[!NOTE]
> 如需 EF6 中提供者相關服務的詳細資訊，請參閱 [EF6 提供者模型](xref:ef6/fundamentals/providers/provider-model) 一節。  

## <a name="systemdataentitycorecommondbproviderservices"></a>System.string. Common. Dbproviderservices.createdatabase  

**引進的版本**： ef 6.0。0  

**傳回的物件**：用於指定提供者非變異名稱的 EF 提供者  

索引**鍵**：包含 ADO.NET 提供者非變異名稱的字串，指定需要提供者的資料庫類型。 例如，系統會針對機碼 "SqlClient" 傳回 SQL Server 提供者。  

>[!NOTE]
> 如需 EF6 中提供者相關服務的詳細資訊，請參閱 [EF6 提供者模型](xref:ef6/fundamentals/providers/provider-model) 一節。  

## <a name="systemdataentityinfrastructureidbconnectionfactory"></a>System.string. IDbConnectionFactory。  

**引進的版本**： ef 6.0。0  

**傳回的物件**：當 EF 依照慣例建立資料庫連接時，將會使用的連接 factory。 也就是說，當沒有任何連接或連接字串給 EF，而且在 app.config 或 web.config 中找不到任何連接字串時，就會使用此服務依慣例建立連接。 變更連線處理站可讓 EF 使用不同類型的資料庫 (例如，預設為 SQL Server Compact Edition) 。  

機**碼**：未使用;將會是 null  

>[!NOTE]
> 如需 EF6 中提供者相關服務的詳細資訊，請參閱 [EF6 提供者模型](xref:ef6/fundamentals/providers/provider-model) 一節。  

## <a name="systemdataentityinfrastructureimanifesttokenservice"></a>System.string. IManifestTokenService。  

**引進的版本**： ef 6.0。0  

**傳回的物件**：可從連接產生提供者資訊清單權杖的服務。 這項服務通常會以兩種方式使用。 首先，它可以用來避免在建立模型時 Code First 連接至資料庫。 其次，它可以用來強制 Code First 建立特定資料庫版本的模型，例如，即使有時候使用 SQL Server 2008，也可以強制 SQL Server 2005 的模型。  

**物件存留期**： Singleton--相同的物件可多次使用，並由不同的執行緒同時使用  

機**碼**：未使用;將會是 null  

## <a name="systemdataentityinfrastructureidbproviderfactoryservice"></a>System.string. IDbProviderFactoryService。  

**引進的版本**： ef 6.0。0  

**傳回的物件**：可以從指定連接取得提供者 factory 的服務。 在 .NET 4.5 上，提供者可從連接公開存取。 在 .NET 4 中，此服務的預設執行會使用一些啟發學習法來尋找相符的提供者。 如果這些失敗，則可以註冊此服務的新實作為，以提供適當的解決方式。  

機**碼**：未使用;將會是 null  

## <a name="funcdbcontext-systemdataentityinfrastructureidbmodelcachekey"></a>Func<DbCoNtext （IDbModelCacheKey）\>  

**引進的版本**： ef 6.0。0  

**傳回的物件**：會產生指定內容之模型快取索引鍵的 factory。 根據預設，EF 會為每個提供者的每個 DbCoNtext 類型快取一個模型。 這項服務的不同實行可以用來將其他資訊（例如架構名稱）新增至快取索引鍵。  

機**碼**：未使用;將會是 null  

## <a name="systemdataentityspatialdbspatialservices"></a>DbSpatialServices 的資料。  

**引進的版本**： ef 6.0。0  

**傳回的物件**： EF 空間提供者，可將支援新增至 geography 和 geometry 空間類型的基本 EF 提供者。  

**Key**： DbSptialServices 的要求方式有兩種。 首先，系統會使用 DbProviderInfo 物件來要求提供者專屬的空間服務， (其中包含非變異名稱和資訊清單標記) 作為索引鍵。 其次，DbSpatialServices 可以要求沒有金鑰的要求。 這是用來解析建立獨立 DbGeography 或 DbGeometry 類型時所使用的「全域空間提供者」。  

>[!NOTE]
> 如需 EF6 中提供者相關服務的詳細資訊，請參閱 [EF6 提供者模型](xref:ef6/fundamentals/providers/provider-model) 一節。  

## <a name="funcsystemdataentityinfrastructureidbexecutionstrategy"></a>Func<system.string. >idbexecutionstrategy\>  

**引進的版本**： ef 6.0。0  

**傳回的物件**：建立服務的 factory，可讓提供者在對資料庫執行查詢和命令時，執行重試或其他行為。 如果未提供任何執行，則 EF 只會執行命令並傳播任何擲回的例外狀況。 針對 SQL Server，此服務會用來提供重試原則，此原則在針對雲端式資料庫伺服器（例如 SQL Azure）執行時特別有用。  

索引**鍵**： ExecutionStrategyKey 物件，其中包含提供者的非變異名稱，以及選擇性地將使用執行策略的伺服器名稱。  

>[!NOTE]
> 如需 EF6 中提供者相關服務的詳細資訊，請參閱 [EF6 提供者模型](xref:ef6/fundamentals/providers/provider-model) 一節。  

## <a name="funcdbconnection-string-systemdataentitymigrationshistoryhistorycontext"></a>Func<DbConnection，string，HistoryCoNtext 的資料。\>  

**引進的版本**： ef 6.0。0  

**傳回的物件**： factory，可讓提供者將 HistoryCoNtext 對應設定為 `__MigrationHistory` EF 遷移所使用的資料表。 HistoryCoNtext 是 Code First 的 DbCoNtext，而且可以使用一般的流暢 API 來設定，以變更資料表名稱和資料行對應規格等專案。  

機**碼**：未使用;將會是 null  

>[!NOTE]
> 如需 EF6 中提供者相關服務的詳細資訊，請參閱 [EF6 提供者模型](xref:ef6/fundamentals/providers/provider-model) 一節。  

## <a name="systemdatacommondbproviderfactory"></a>DbProviderFactory。  

**引進的版本**： ef 6.0。0  

**傳回的物件**：用於指定提供者非變異名稱的 ADO.NET 提供者。  

索引**鍵**：包含 ADO.NET 提供者非變異名稱的字串  

>[!NOTE]
> 這項服務通常不會直接變更，因為預設的執行會使用一般的 ADO.NET 提供者註冊。 如需 EF6 中提供者相關服務的詳細資訊，請參閱 [EF6 提供者模型](xref:ef6/fundamentals/providers/provider-model) 一節。  

## <a name="systemdataentityinfrastructureiproviderinvariantname"></a>System.string. IProviderInvariantName。  

**引進的版本**： ef 6.0。0  

**傳回的物件**：用來判斷指定之 DbProviderFactory 類型的提供者不變名稱的服務。 這項服務的預設執行會使用 ADO.NET 提供者註冊。 這表示，如果 ADO.NET 提供者未以正常方式註冊，因為 EF 正在解析 DbProviderFactory，則也需要解析這項服務。  

**Key**：需要非變異名稱的 DbProviderFactory 實例。  

>[!NOTE]
> 如需 EF6 中提供者相關服務的詳細資訊，請參閱 [EF6 提供者模型](xref:ef6/fundamentals/providers/provider-model) 一節。  

## <a name="systemdataentitycoremappingviewgenerationiviewassemblycache"></a>ViewGeneration. IViewAssemblyCache （資料）。  

**引進的版本**： ef 6.0。0  

**傳回的物件**：包含預先產生之視圖的元件快取。 取代通常用來讓 EF 知道哪些元件包含預先產生的視圖，而不進行任何探索。  

機**碼**：未使用;將會是 null  

## <a name="systemdataentityinfrastructurepluralizationipluralizationservice"></a>System.string. 複數表示. IPluralizationService

**引進的版本**： ef 6.0。0  

**傳回的物件**： EF 用來複數化和單數化名稱的服務。 預設會使用英文複數表示服務。  

機**碼**：未使用;將會是 null  

## <a name="systemdataentityinfrastructureinterceptionidbinterceptor"></a>System.string... IDbInterceptor  

**引進的版本**： ef 6.0。0

**傳回的物件**：應在應用程式啟動時註冊的任何攔截器。 請注意，這些物件是使用 GetServices 呼叫所要求，而且任何相依性解析程式所傳回的所有攔截器都會註冊。

機**碼**：未使用;將會是 null。  

## <a name="funcsystemdataentitydbcontext-actionstring-systemdataentityinfrastructureinterceptiondatabaselogformatter"></a>Func<DbCoNtext，Action<字串，DatabaseLogFormatter 的 Func. （ \> 資料）。\>  

**引進的版本**： ef 6.0。0  

**傳回的物件**：將用來建立資料庫記錄格式器的 factory，此格式器將在內容時使用。指定的內容上已設定 Database .Log 屬性。  

機**碼**：未使用;將會是 null。  

## <a name="funcsystemdataentitydbcontext"></a>Func<system.string. DbCoNtext\>  

**引進的版本**： ef 6.1。0  

**傳回的物件**：當內容沒有可存取的無參數函式時，將用來建立內容實例以進行遷移的 factory。  

索引**鍵**：需要 factory 之衍生 DbCoNtext 型別的型別物件。  

## <a name="funcsystemdataentitycoremetadataedmimetadataannotationserializer"></a>Func<system.string. IMetadataAnnotationSerializer 的資料。\>  

**引進的版本**： ef 6.1。0  

**傳回的物件**：將用來建立序列化程式以序列化強型別自訂批註的 factory，以便將它們序列化並 DESTERILIZED 至 XML 以用於 Code First 移轉。  

**Key**：正在序列化或還原序列化的批註名稱。  

## <a name="funcsystemdataentityinfrastructuretransactionhandler"></a>Func<system.string. TransactionHandler\>  

**引進的版本**： ef 6.1。0  

**傳回的物件**：將用來建立交易處理常式的 factory，以便可以針對處理認可失敗的情況套用特殊處理。  

**Key**： ExecutionStrategyKey 物件，其中包含提供者的非變異名稱，以及選擇性地使用交易處理常式的伺服器名稱。  
