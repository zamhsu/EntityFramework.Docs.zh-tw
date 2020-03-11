---
title: 相依性解析-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 32d19ac6-9186-4ae1-8655-64ee49da55d0
ms.openlocfilehash: 6082124481f5795bbcb62fff2bb6a58ecdcb48e4
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417859"
---
# <a name="dependency-resolution"></a><span data-ttu-id="e9cf2-102">相依性解析</span><span class="sxs-lookup"><span data-stu-id="e9cf2-102">Dependency resolution</span></span>
> [!NOTE]
> <span data-ttu-id="e9cf2-103">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="e9cf2-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="e9cf2-105">從 EF6 開始，Entity Framework 包含一般用途的機制，可取得所需服務的執行。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-105">Starting with EF6, Entity Framework contains a general-purpose mechanism for obtaining implementations of services that it requires.</span></span> <span data-ttu-id="e9cf2-106">也就是說，當 EF 使用某些介面或基類的實例時，它會要求介面或基類的具體執行方式來使用。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-106">That is, when EF uses an instance of some interfaces or base classes it will ask for a concrete implementation of the interface or base class to use.</span></span> <span data-ttu-id="e9cf2-107">這可透過使用 IDbDependencyResolver 介面來達成：</span><span class="sxs-lookup"><span data-stu-id="e9cf2-107">This is achieved through use of the IDbDependencyResolver interface:</span></span>  

``` csharp
public interface IDbDependencyResolver
{
    object GetService(Type type, object key);
}
```  

<span data-ttu-id="e9cf2-108">GetService 方法通常是由 EF 呼叫，而且是由 EF 或應用程式所提供的 IDbDependencyResolver 執行所處理。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-108">The GetService method is typically called by EF and is handled by an implementation of IDbDependencyResolver provided either by EF or by the application.</span></span> <span data-ttu-id="e9cf2-109">當呼叫時，型別引數是所要求之服務的介面或基類型別，而索引鍵物件則是 null 或物件，提供所要求之服務的相關內容資訊。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-109">When called, the type argument is the interface or base class type of the service being requested, and the key object is either null or an object providing contextual information about the requested service.</span></span>  

<span data-ttu-id="e9cf2-110">除非另有指定，否則傳回的任何物件都必須是安全線程，因為它可以當做單一實例使用。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-110">Unless otherwise stated any object returned must be thread-safe since it can be used as a singleton.</span></span> <span data-ttu-id="e9cf2-111">在許多情況下，傳回的物件是 factory，在這種情況下，factory 本身必須是安全線程，但從處理站傳回的物件不需要是安全線程，因為會從處理站要求新的實例供每次使用。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-111">In many cases the object returned is a factory in which case the factory itself must be thread-safe but the object returned from the factory does not need to be thread-safe since a new instance is requested from the factory for each use.</span></span>

<span data-ttu-id="e9cf2-112">本文不包含如何執行 IDbDependencyResolver 的完整詳細資料，而是做為 EF 呼叫 GetService 之服務類型（也就是介面和基類類型）的參考，以及每個這些專案的索引鍵物件的語義撥號.</span><span class="sxs-lookup"><span data-stu-id="e9cf2-112">This article does not contain full details on how to implement IDbDependencyResolver, but instead acts as a reference for the service types (that is, the interface and base class types) for which EF calls GetService and the semantics of the key object for each of these calls.</span></span>

## <a name="systemdataentityidatabaseinitializertcontext"></a><span data-ttu-id="e9cf2-113">IDatabaseInitializer < TCoNtext\></span><span class="sxs-lookup"><span data-stu-id="e9cf2-113">System.Data.Entity.IDatabaseInitializer<TContext\></span></span>  

<span data-ttu-id="e9cf2-114">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e9cf2-114">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="e9cf2-115">**傳回的物件**：指定內容類型的資料庫初始化運算式</span><span class="sxs-lookup"><span data-stu-id="e9cf2-115">**Object returned**: A database initializer for the given context type</span></span>  

<span data-ttu-id="e9cf2-116">索引**鍵**：未使用;將為 null</span><span class="sxs-lookup"><span data-stu-id="e9cf2-116">**Key**: Not used; will be null</span></span>  

## <a name="funcsystemdataentitymigrationssqlmigrationsqlgenerator"></a><span data-ttu-id="e9cf2-117">MigrationSqlGenerator\> 中的 Func <。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-117">Func<System.Data.Entity.Migrations.Sql.MigrationSqlGenerator\></span></span>  

<span data-ttu-id="e9cf2-118">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e9cf2-118">**Version introduced**: EF6.0.0</span></span>

<span data-ttu-id="e9cf2-119">**傳回的物件**：用來建立 SQL 產生器的處理站，可用於遷移，以及導致建立資料庫的其他動作，例如使用資料庫初始化運算式建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-119">**Object returned**: A factory to create a SQL generator that can be used for Migrations and other actions that cause a database to be created, such as database creation with database initializers.</span></span>  

<span data-ttu-id="e9cf2-120">索引**鍵**：包含 ADO.NET 提供者非變異名稱的字串，指定將產生 SQL 的資料庫類型。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-120">**Key**: A string containing the ADO.NET provider invariant name specifying the type of database for which SQL will be generated.</span></span> <span data-ttu-id="e9cf2-121">例如，針對 SqlClient 機碼 "system.string"，會傳回 SQL Server SQL 產生器。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-121">For example, the SQL Server SQL generator is returned for the key "System.Data.SqlClient".</span></span>  

>[!NOTE]
> <span data-ttu-id="e9cf2-122">如需 EF6 中提供者相關服務的詳細資訊，請參閱[EF6 提供者模型](~/ef6/fundamentals/providers/provider-model.md)一節。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-122">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

## <a name="systemdataentitycorecommondbproviderservices"></a><span data-ttu-id="e9cf2-123">System.web. Common. Dbproviderservices.createdatabase</span><span class="sxs-lookup"><span data-stu-id="e9cf2-123">System.Data.Entity.Core.Common.DbProviderServices</span></span>  

<span data-ttu-id="e9cf2-124">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e9cf2-124">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="e9cf2-125">**傳回的物件**：用於指定提供者不變名稱的 EF 提供者</span><span class="sxs-lookup"><span data-stu-id="e9cf2-125">**Object returned**: The EF provider to use for a given provider invariant name</span></span>  

<span data-ttu-id="e9cf2-126">索引**鍵**：包含 ADO.NET 提供者非變異名稱的字串，指定需要提供者的資料庫類型。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-126">**Key**: A string containing the ADO.NET provider invariant name specifying the type of database for which a provider is needed.</span></span> <span data-ttu-id="e9cf2-127">例如，SqlClient 機碼的會傳回 SQL Server 提供者。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-127">For example, the SQL Server provider is returned for the key "System.Data.SqlClient".</span></span>  

>[!NOTE]
> <span data-ttu-id="e9cf2-128">如需 EF6 中提供者相關服務的詳細資訊，請參閱[EF6 提供者模型](~/ef6/fundamentals/providers/provider-model.md)一節。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-128">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

## <a name="systemdataentityinfrastructureidbconnectionfactory"></a><span data-ttu-id="e9cf2-129">System.web. Entity. IDbConnectionFactory</span><span class="sxs-lookup"><span data-stu-id="e9cf2-129">System.Data.Entity.Infrastructure.IDbConnectionFactory</span></span>  

<span data-ttu-id="e9cf2-130">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e9cf2-130">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="e9cf2-131">**傳回的物件**：當 EF 依照慣例建立資料庫連接時，將使用的連接 factory。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-131">**Object returned**: The connection factory that will be used when EF creates a database connection by convention.</span></span> <span data-ttu-id="e9cf2-132">也就是說，當 EF 沒有連接或連接字串，而且在 app.config 或 web.config 中找不到連接字串時，就會使用此服務依慣例建立連接。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-132">That is, when no connection or connection string is given to EF, and no connection string can be found in the app.config or web.config, then this service is used to create a connection by convention.</span></span> <span data-ttu-id="e9cf2-133">根據預設，變更連線 factory 可以讓 EF 使用不同類型的資料庫（例如，SQL Server Compact 版本）。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-133">Changing the connection factory can allow EF to use a different type of database (for example, SQL Server Compact Edition) by default.</span></span>  

<span data-ttu-id="e9cf2-134">索引**鍵**：未使用;將為 null</span><span class="sxs-lookup"><span data-stu-id="e9cf2-134">**Key**: Not used; will be null</span></span>  

>[!NOTE]
> <span data-ttu-id="e9cf2-135">如需 EF6 中提供者相關服務的詳細資訊，請參閱[EF6 提供者模型](~/ef6/fundamentals/providers/provider-model.md)一節。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-135">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

## <a name="systemdataentityinfrastructureimanifesttokenservice"></a><span data-ttu-id="e9cf2-136">System.web. Entity. IManifestTokenService</span><span class="sxs-lookup"><span data-stu-id="e9cf2-136">System.Data.Entity.Infrastructure.IManifestTokenService</span></span>  

<span data-ttu-id="e9cf2-137">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e9cf2-137">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="e9cf2-138">**傳回的物件**：可從連接產生提供者資訊清單 token 的服務。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-138">**Object returned**: A service that can generate a provider manifest token from a connection.</span></span> <span data-ttu-id="e9cf2-139">這項服務通常會以兩種方式使用。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-139">This service is typically used in two ways.</span></span> <span data-ttu-id="e9cf2-140">首先，它可以用來避免 Code First 在建立模型時連接到資料庫。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-140">First, it can be used to avoid Code First connecting to the database when building a model.</span></span> <span data-ttu-id="e9cf2-141">其次，它可以用來強制 Code First 建立特定資料庫版本的模型，例如，即使使用 SQL Server 2008，還是會強制 SQL Server 2005 的模型。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-141">Second, it can be used to force Code First to build a model for a specific database version -- for example, to force a model for SQL Server 2005 even if sometimes SQL Server 2008 is used.</span></span>  

<span data-ttu-id="e9cf2-142">**物件存留期**： Singleton--相同的物件可以多次使用，並由不同的執行緒同時執行</span><span class="sxs-lookup"><span data-stu-id="e9cf2-142">**Object lifetime**: Singleton -- the same object may be used multiple times and concurrently by different threads</span></span>  

<span data-ttu-id="e9cf2-143">索引**鍵**：未使用;將為 null</span><span class="sxs-lookup"><span data-stu-id="e9cf2-143">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructureidbproviderfactoryservice"></a><span data-ttu-id="e9cf2-144">System.web. Entity. IDbProviderFactoryService</span><span class="sxs-lookup"><span data-stu-id="e9cf2-144">System.Data.Entity.Infrastructure.IDbProviderFactoryService</span></span>  

<span data-ttu-id="e9cf2-145">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e9cf2-145">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="e9cf2-146">**傳回的物件**：可以從指定的連接取得提供者 factory 的服務。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-146">**Object returned**: A service that can obtain a provider factory from a given connection.</span></span> <span data-ttu-id="e9cf2-147">在 .NET 4.5 上，提供者可從連接公開存取。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-147">On .NET 4.5 the provider is publicly accessible from the connection.</span></span> <span data-ttu-id="e9cf2-148">在 .NET 4 中，此服務的預設執行會使用一些啟發學習法來尋找相符的提供者。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-148">On .NET 4 the default implementation of this service uses some heuristics to find the matching provider.</span></span> <span data-ttu-id="e9cf2-149">如果這些失敗，則可以註冊此服務的新執行，以提供適當的解決方式。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-149">If these fail then a new implementation of this service can be registered to provide an appropriate resolution.</span></span>  

<span data-ttu-id="e9cf2-150">索引**鍵**：未使用;將為 null</span><span class="sxs-lookup"><span data-stu-id="e9cf2-150">**Key**: Not used; will be null</span></span>  

## <a name="funcdbcontext-systemdataentityinfrastructureidbmodelcachekey"></a><span data-ttu-id="e9cf2-151">DbCoNtext、IDbModelCacheKey\> 的 Func <</span><span class="sxs-lookup"><span data-stu-id="e9cf2-151">Func<DbContext, System.Data.Entity.Infrastructure.IDbModelCacheKey\></span></span>  

<span data-ttu-id="e9cf2-152">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e9cf2-152">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="e9cf2-153">**傳回的物件**：將為給定內容產生模型快取索引鍵的 factory。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-153">**Object returned**: A factory that will generate a model cache key for a given context.</span></span> <span data-ttu-id="e9cf2-154">根據預設，EF 會針對每個提供者每個 DbCoNtext 類型快取一個模型。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-154">By default, EF caches one model per DbContext type per provider.</span></span> <span data-ttu-id="e9cf2-155">此服務的不同執行可以用來將其他資訊（例如架構名稱）新增至快取索引鍵。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-155">A different implementation of this service can be used to add other information, such as schema name, to the cache key.</span></span>  

<span data-ttu-id="e9cf2-156">索引**鍵**：未使用;將為 null</span><span class="sxs-lookup"><span data-stu-id="e9cf2-156">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityspatialdbspatialservices"></a><span data-ttu-id="e9cf2-157">System.web. Entity. DbSpatialServices</span><span class="sxs-lookup"><span data-stu-id="e9cf2-157">System.Data.Entity.Spatial.DbSpatialServices</span></span>  

<span data-ttu-id="e9cf2-158">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e9cf2-158">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="e9cf2-159">**傳回的物件**： EF 空間提供者，可將支援加入 geography 和 geometry 空間類型的基本 EF 提供者。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-159">**Object returned**: An EF spatial provider that adds support to the basic EF provider for geography and geometry spatial types.</span></span>  

<span data-ttu-id="e9cf2-160">**金鑰**：系統會以兩種方式要求 DbSptialServices。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-160">**Key**: DbSptialServices is asked for in two ways.</span></span> <span data-ttu-id="e9cf2-161">首先，會使用 DbProviderInfo 物件（其中包含不變的名稱和資訊清單 token）來要求提供者專屬的空間服務，做為索引鍵。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-161">First, provider-specific spatial services are requested using a DbProviderInfo object (which contains invariant name and manifest token) as the key.</span></span> <span data-ttu-id="e9cf2-162">第二，您可以要求 DbSpatialServices，而不需要金鑰。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-162">Second, DbSpatialServices can be asked for with no key.</span></span> <span data-ttu-id="e9cf2-163">這是用來解析建立獨立 DbGeography 或 DbGeometry 類型時所使用的「全域空間提供者」。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-163">This is used to resolve the "global spatial provider" which is used when creating stand-alone DbGeography or DbGeometry types.</span></span>  

>[!NOTE]
> <span data-ttu-id="e9cf2-164">如需 EF6 中提供者相關服務的詳細資訊，請參閱[EF6 提供者模型](~/ef6/fundamentals/providers/provider-model.md)一節。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-164">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

## <a name="funcsystemdataentityinfrastructureidbexecutionstrategy"></a><span data-ttu-id="e9cf2-165">IDbExecutionStrategy\> 中的 Func <</span><span class="sxs-lookup"><span data-stu-id="e9cf2-165">Func<System.Data.Entity.Infrastructure.IDbExecutionStrategy\></span></span>  

<span data-ttu-id="e9cf2-166">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e9cf2-166">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="e9cf2-167">**傳回的物件**：用來建立服務的 factory，可讓提供者在針對資料庫執行查詢和命令時，執行重試或其他行為。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-167">**Object returned**: A factory to create a service that allows a provider to implement retries or other behavior when queries and commands are executed against the database.</span></span> <span data-ttu-id="e9cf2-168">如果未提供任何實作為，則 EF 只會執行命令，並傳播擲回的任何例外狀況。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-168">If no implementation is provided, then EF will simply execute the commands and propagate any exceptions thrown.</span></span> <span data-ttu-id="e9cf2-169">針對 SQL Server 此服務可用來提供重試原則，這在針對雲端式資料庫伺服器（例如 SQL Azure）執行時特別有用。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-169">For SQL Server this service is used to provide a retry policy which is especially useful when running against cloud-based database servers such as SQL Azure.</span></span>  

<span data-ttu-id="e9cf2-170">索引**鍵**： ExecutionStrategyKey 物件，其中包含提供者不變名稱，以及選擇性地使用執行策略的伺服器名稱。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-170">**Key**: An ExecutionStrategyKey object that contains the provider invariant name and optionally a server name for which the execution strategy will be used.</span></span>  

>[!NOTE]
> <span data-ttu-id="e9cf2-171">如需 EF6 中提供者相關服務的詳細資訊，請參閱[EF6 提供者模型](~/ef6/fundamentals/providers/provider-model.md)一節。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-171">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

## <a name="funcdbconnection-string-systemdataentitymigrationshistoryhistorycontext"></a><span data-ttu-id="e9cf2-172">Func < DbConnection，string，HistoryCoNtext\> 的資訊</span><span class="sxs-lookup"><span data-stu-id="e9cf2-172">Func<DbConnection, string, System.Data.Entity.Migrations.History.HistoryContext\></span></span>  

<span data-ttu-id="e9cf2-173">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e9cf2-173">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="e9cf2-174">**傳回的物件**：一個 factory，可讓提供者設定 HISTORYCONTEXT 與 EF 遷移所使用之 `__MigrationHistory` 資料表的對應。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-174">**Object returned**: A factory that allows a provider to configure the mapping of the HistoryContext to the `__MigrationHistory` table used by EF Migrations.</span></span> <span data-ttu-id="e9cf2-175">HistoryCoNtext 是 Code First 的 DbCoNtext，而且可以使用正常的 Fluent API 來設定，以變更資料表名稱和資料行對應規格之類的專案。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-175">The HistoryContext is a Code First DbContext and can be configured using the normal fluent API to change things like the name of the table and the column mapping specifications.</span></span>  

<span data-ttu-id="e9cf2-176">索引**鍵**：未使用;將為 null</span><span class="sxs-lookup"><span data-stu-id="e9cf2-176">**Key**: Not used; will be null</span></span>  

>[!NOTE]
> <span data-ttu-id="e9cf2-177">如需 EF6 中提供者相關服務的詳細資訊，請參閱[EF6 提供者模型](~/ef6/fundamentals/providers/provider-model.md)一節。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-177">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

## <a name="systemdatacommondbproviderfactory"></a><span data-ttu-id="e9cf2-178">System.web. DbProviderFactory</span><span class="sxs-lookup"><span data-stu-id="e9cf2-178">System.Data.Common.DbProviderFactory</span></span>  

<span data-ttu-id="e9cf2-179">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e9cf2-179">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="e9cf2-180">**傳回的物件**：要用於指定提供者不變名稱的 ADO.NET 提供者。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-180">**Object returned**: The ADO.NET provider to use for a given provider invariant name.</span></span>  

<span data-ttu-id="e9cf2-181">索引**鍵**：包含 ADO.NET 提供者不變名稱的字串</span><span class="sxs-lookup"><span data-stu-id="e9cf2-181">**Key**: A string containing the ADO.NET provider invariant name</span></span>  

>[!NOTE]
> <span data-ttu-id="e9cf2-182">此服務通常不會直接變更，因為預設的執行會使用一般 ADO.NET 提供者註冊。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-182">This service is not usually changed directly since the default implementation uses the normal ADO.NET provider registration.</span></span> <span data-ttu-id="e9cf2-183">如需 EF6 中提供者相關服務的詳細資訊，請參閱[EF6 提供者模型](~/ef6/fundamentals/providers/provider-model.md)一節。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-183">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

## <a name="systemdataentityinfrastructureiproviderinvariantname"></a><span data-ttu-id="e9cf2-184">System.web. Entity. IProviderInvariantName</span><span class="sxs-lookup"><span data-stu-id="e9cf2-184">System.Data.Entity.Infrastructure.IProviderInvariantName</span></span>  

<span data-ttu-id="e9cf2-185">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e9cf2-185">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="e9cf2-186">**傳回的物件**：用來判斷指定之 DbProviderFactory 類型的提供者不變名稱的服務。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-186">**Object returned**: a service that is used to determine a provider invariant name for a given type of DbProviderFactory.</span></span> <span data-ttu-id="e9cf2-187">此服務的預設執行會使用 ADO.NET 提供者註冊。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-187">The default implementation of this service uses the ADO.NET provider registration.</span></span> <span data-ttu-id="e9cf2-188">這表示，如果 ADO.NET 提供者未以一般方式註冊，因為 DbProviderFactory 正由 EF 解決，則也需要解析此服務。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-188">This means that if the ADO.NET provider is not registered in the normal way because DbProviderFactory is being resolved by EF, then it will also be necessary to resolve this service.</span></span>  

<span data-ttu-id="e9cf2-189">索引**鍵**：需要不變名稱的 DbProviderFactory 實例。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-189">**Key**: The DbProviderFactory instance for which an invariant name is required.</span></span>  

>[!NOTE]
> <span data-ttu-id="e9cf2-190">如需 EF6 中提供者相關服務的詳細資訊，請參閱[EF6 提供者模型](~/ef6/fundamentals/providers/provider-model.md)一節。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-190">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

## <a name="systemdataentitycoremappingviewgenerationiviewassemblycache"></a><span data-ttu-id="e9cf2-191">ViewGeneration. IViewAssemblyCache 的資料。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-191">System.Data.Entity.Core.Mapping.ViewGeneration.IViewAssemblyCache</span></span>  

<span data-ttu-id="e9cf2-192">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e9cf2-192">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="e9cf2-193">**傳回的物件**：包含預先產生之視圖的元件快取。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-193">**Object returned**: a cache of the assemblies that contain pre-generated views.</span></span> <span data-ttu-id="e9cf2-194">取代通常用來讓 EF 知道哪些元件包含預先產生的視圖，而不進行任何探索。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-194">A replacement is typically used to let EF know which assemblies contain pre-generated views without doing any discovery.</span></span>  

<span data-ttu-id="e9cf2-195">索引**鍵**：未使用;將為 null</span><span class="sxs-lookup"><span data-stu-id="e9cf2-195">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructurepluralizationipluralizationservice"></a><span data-ttu-id="e9cf2-196">複數表示. Entity. IPluralizationService</span><span class="sxs-lookup"><span data-stu-id="e9cf2-196">System.Data.Entity.Infrastructure.Pluralization.IPluralizationService</span></span>

<span data-ttu-id="e9cf2-197">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e9cf2-197">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="e9cf2-198">**傳回的物件**： EF 用來將複數化和單數化名稱的服務。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-198">**Object returned**: a service used by EF to pluralize and singularize names.</span></span> <span data-ttu-id="e9cf2-199">預設會使用英文複數表示服務。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-199">By default an English pluralization service is used.</span></span>  

<span data-ttu-id="e9cf2-200">索引**鍵**：未使用;將為 null</span><span class="sxs-lookup"><span data-stu-id="e9cf2-200">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructureinterceptionidbinterceptor"></a><span data-ttu-id="e9cf2-201">System.web. IDbInterceptor 的資料</span><span class="sxs-lookup"><span data-stu-id="e9cf2-201">System.Data.Entity.Infrastructure.Interception.IDbInterceptor</span></span>  

<span data-ttu-id="e9cf2-202">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e9cf2-202">**Version introduced**: EF6.0.0</span></span>

<span data-ttu-id="e9cf2-203">**傳回的物件**：應用程式啟動時應註冊的任何攔截器。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-203">**Objects returned**: Any interceptors that should be registered when the application starts.</span></span> <span data-ttu-id="e9cf2-204">請注意，這些物件是使用 GetServices 呼叫來要求，而任何相依性解析程式所傳回的所有攔截器都會註冊。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-204">Note that these objects are requested using the GetServices call and all interceptors returned by any dependency resolver will registered.</span></span>

<span data-ttu-id="e9cf2-205">索引**鍵**：未使用;將為 null。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-205">**Key**: Not used; will be null.</span></span>  

## <a name="funcsystemdataentitydbcontext-actionstring-systemdataentityinfrastructureinterceptiondatabaselogformatter"></a><span data-ttu-id="e9cf2-206">Func < DbCoNtext，Action < string\>，DatabaseLogFormatter\> 中的資料結構化的資料。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-206">Func<System.Data.Entity.DbContext, Action<string\>, System.Data.Entity.Infrastructure.Interception.DatabaseLogFormatter\></span></span>  

<span data-ttu-id="e9cf2-207">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e9cf2-207">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="e9cf2-208">**傳回的物件**：將用來建立資料庫記錄檔格式器的 factory，將在內容時使用。在給定的內容上設定了 Database .Log 屬性。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-208">**Object returned**: A factory that will be used to create the database log formatter that will be used when the context.Database.Log property is set on the given context.</span></span>  

<span data-ttu-id="e9cf2-209">索引**鍵**：未使用;將為 null。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-209">**Key**: Not used; will be null.</span></span>  

## <a name="funcsystemdataentitydbcontext"></a><span data-ttu-id="e9cf2-210">Func < DbCoNtext\></span><span class="sxs-lookup"><span data-stu-id="e9cf2-210">Func<System.Data.Entity.DbContext\></span></span>  

<span data-ttu-id="e9cf2-211">**引進的版本**： ef 6.1。0</span><span class="sxs-lookup"><span data-stu-id="e9cf2-211">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="e9cf2-212">**傳回的物件**：當內容沒有可存取的無參數函式時，將用來建立用於遷移之內容實例的 factory。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-212">**Object returned**: A factory that will be used to create context instances for Migrations when the context does not have an accessible parameterless constructor.</span></span>  

<span data-ttu-id="e9cf2-213">索引**鍵**：需要 factory 之衍生 DbCoNtext 類型的類型物件。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-213">**Key**: The Type object for the type of the derived DbContext for which a factory is needed.</span></span>  

## <a name="funcsystemdataentitycoremetadataedmimetadataannotationserializer"></a><span data-ttu-id="e9cf2-214">IMetadataAnnotationSerializer\> 中的 Func < 的中繼資料。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-214">Func<System.Data.Entity.Core.Metadata.Edm.IMetadataAnnotationSerializer\></span></span>  

<span data-ttu-id="e9cf2-215">**引進的版本**： ef 6.1。0</span><span class="sxs-lookup"><span data-stu-id="e9cf2-215">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="e9cf2-216">**傳回的物件**：將用來建立序列化程式的處理站，以進行強型別自訂注釋的序列化，讓它們可以序列化並 DESTERILIZED 到 XML 中，以便在 Code First 移轉中使用。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-216">**Object returned**: A factory that will be used to create serializers for serialization of strongly-typed custom annotations such that they can be serialized and desterilized into XML for use in Code First Migrations.</span></span>  

<span data-ttu-id="e9cf2-217">索引**鍵**：要序列化或還原序列化之注釋的名稱。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-217">**Key**: The name of the annotation that is being serialized or deserialized.</span></span>  

## <a name="funcsystemdataentityinfrastructuretransactionhandler"></a><span data-ttu-id="e9cf2-218">TransactionHandler\> 中的 Func <</span><span class="sxs-lookup"><span data-stu-id="e9cf2-218">Func<System.Data.Entity.Infrastructure.TransactionHandler\></span></span>  

<span data-ttu-id="e9cf2-219">**引進的版本**： ef 6.1。0</span><span class="sxs-lookup"><span data-stu-id="e9cf2-219">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="e9cf2-220">**傳回的物件**：將用來建立交易之處理常式的 factory，以便在處理認可失敗之類的情況下套用特殊處理。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-220">**Object returned**: A factory that will be used to create handlers for transactions so that special handling can be applied for situations such as handling commit failures.</span></span>  

<span data-ttu-id="e9cf2-221">索引**鍵**： ExecutionStrategyKey 物件，其中包含提供者不變的名稱，以及將使用交易處理常式的伺服器名稱（選擇性）。</span><span class="sxs-lookup"><span data-stu-id="e9cf2-221">**Key**: An ExecutionStrategyKey object that contains the provider invariant name and optionally a server name for which the transaction handler will be used.</span></span>  
