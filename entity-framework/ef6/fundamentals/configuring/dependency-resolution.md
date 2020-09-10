---
title: 相依性解析-EF6
description: Entity Framework 6 中的相依性解析
author: divega
ms.date: 10/23/2016
ms.assetid: 32d19ac6-9186-4ae1-8655-64ee49da55d0
uid: ef6/fundamentals/configuring/dependency-resolution
ms.openlocfilehash: c23253dc5a413077e3980fcfa18ea83b5fc3970e
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618419"
---
# <a name="dependency-resolution"></a><span data-ttu-id="1e545-103">相依性解析</span><span class="sxs-lookup"><span data-stu-id="1e545-103">Dependency resolution</span></span>
> [!NOTE]
> <span data-ttu-id="1e545-104">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="1e545-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="1e545-105">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="1e545-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="1e545-106">從 EF6 開始，Entity Framework 包含一般用途的機制，可取得所需的服務的實現。</span><span class="sxs-lookup"><span data-stu-id="1e545-106">Starting with EF6, Entity Framework contains a general-purpose mechanism for obtaining implementations of services that it requires.</span></span> <span data-ttu-id="1e545-107">也就是說，當 EF 使用某個介面或基類的實例時，會要求要使用的介面或基類的具體執行。</span><span class="sxs-lookup"><span data-stu-id="1e545-107">That is, when EF uses an instance of some interfaces or base classes it will ask for a concrete implementation of the interface or base class to use.</span></span> <span data-ttu-id="1e545-108">使用 IDbDependencyResolver 介面可達成此目的：</span><span class="sxs-lookup"><span data-stu-id="1e545-108">This is achieved through use of the IDbDependencyResolver interface:</span></span>  

``` csharp
public interface IDbDependencyResolver
{
    object GetService(Type type, object key);
}
```  

<span data-ttu-id="1e545-109">GetService 方法通常是由 EF 呼叫，並且是由 EF 或應用程式所提供的 IDbDependencyResolver 執行所處理。</span><span class="sxs-lookup"><span data-stu-id="1e545-109">The GetService method is typically called by EF and is handled by an implementation of IDbDependencyResolver provided either by EF or by the application.</span></span> <span data-ttu-id="1e545-110">當呼叫時，型別引數是所要求之服務的介面或基類型別，而索引鍵物件為 null 或提供有關所要求服務之內容資訊的物件。</span><span class="sxs-lookup"><span data-stu-id="1e545-110">When called, the type argument is the interface or base class type of the service being requested, and the key object is either null or an object providing contextual information about the requested service.</span></span>  

<span data-ttu-id="1e545-111">除非另有說明，否則傳回的任何物件都必須是安全線程，因為它可以當做 singleton 使用。</span><span class="sxs-lookup"><span data-stu-id="1e545-111">Unless otherwise stated any object returned must be thread-safe since it can be used as a singleton.</span></span> <span data-ttu-id="1e545-112">在許多情況下，傳回的物件是 factory，在此情況下，factory 本身必須是安全線程，但從處理站傳回的物件不需要是安全線程，因為從處理站要求新的實例以供每個使用。</span><span class="sxs-lookup"><span data-stu-id="1e545-112">In many cases the object returned is a factory in which case the factory itself must be thread-safe but the object returned from the factory does not need to be thread-safe since a new instance is requested from the factory for each use.</span></span>

<span data-ttu-id="1e545-113">本文不包含如何執行 IDbDependencyResolver 的完整詳細資料，而是做為服務類型的參考 (也就是，其 EF 會呼叫 GetService 的介面和基類) 類型，以及每個呼叫的索引鍵物件的語法。</span><span class="sxs-lookup"><span data-stu-id="1e545-113">This article does not contain full details on how to implement IDbDependencyResolver, but instead acts as a reference for the service types (that is, the interface and base class types) for which EF calls GetService and the semantics of the key object for each of these calls.</span></span>

## <a name="systemdataentityidatabaseinitializertcontext"></a><span data-ttu-id="1e545-114">IDatabaseInitializer<的 >tcoNtext>\></span><span class="sxs-lookup"><span data-stu-id="1e545-114">System.Data.Entity.IDatabaseInitializer<TContext\></span></span>  

<span data-ttu-id="1e545-115">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="1e545-115">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="1e545-116">**傳回的物件**：指定之內容類型的資料庫初始化運算式</span><span class="sxs-lookup"><span data-stu-id="1e545-116">**Object returned**: A database initializer for the given context type</span></span>  

<span data-ttu-id="1e545-117">機**碼**：未使用;將會是 null</span><span class="sxs-lookup"><span data-stu-id="1e545-117">**Key**: Not used; will be null</span></span>  

## <a name="funcsystemdataentitymigrationssqlmigrationsqlgenerator"></a><span data-ttu-id="1e545-118"><的 Func。 MigrationSqlGenerator\></span><span class="sxs-lookup"><span data-stu-id="1e545-118">Func<System.Data.Entity.Migrations.Sql.MigrationSqlGenerator\></span></span>  

<span data-ttu-id="1e545-119">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="1e545-119">**Version introduced**: EF6.0.0</span></span>

<span data-ttu-id="1e545-120">**傳回的物件**：建立 SQL 產生器的處理站，可用來進行遷移，以及其他導致建立資料庫的動作，例如使用資料庫初始化運算式建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="1e545-120">**Object returned**: A factory to create a SQL generator that can be used for Migrations and other actions that cause a database to be created, such as database creation with database initializers.</span></span>  

<span data-ttu-id="1e545-121">索引**鍵**：包含 ADO.NET 提供者非變異名稱的字串，指定將產生 SQL 的資料庫類型。</span><span class="sxs-lookup"><span data-stu-id="1e545-121">**Key**: A string containing the ADO.NET provider invariant name specifying the type of database for which SQL will be generated.</span></span> <span data-ttu-id="1e545-122">例如，系統會針對機碼 "SqlClient" 傳回 SQL Server SQL 產生器。</span><span class="sxs-lookup"><span data-stu-id="1e545-122">For example, the SQL Server SQL generator is returned for the key "System.Data.SqlClient".</span></span>  

>[!NOTE]
> <span data-ttu-id="1e545-123">如需 EF6 中提供者相關服務的詳細資訊，請參閱 [EF6 提供者模型](xref:ef6/fundamentals/providers/provider-model) 一節。</span><span class="sxs-lookup"><span data-stu-id="1e545-123">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentitycorecommondbproviderservices"></a><span data-ttu-id="1e545-124">System.string. Common. Dbproviderservices.createdatabase</span><span class="sxs-lookup"><span data-stu-id="1e545-124">System.Data.Entity.Core.Common.DbProviderServices</span></span>  

<span data-ttu-id="1e545-125">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="1e545-125">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="1e545-126">**傳回的物件**：用於指定提供者非變異名稱的 EF 提供者</span><span class="sxs-lookup"><span data-stu-id="1e545-126">**Object returned**: The EF provider to use for a given provider invariant name</span></span>  

<span data-ttu-id="1e545-127">索引**鍵**：包含 ADO.NET 提供者非變異名稱的字串，指定需要提供者的資料庫類型。</span><span class="sxs-lookup"><span data-stu-id="1e545-127">**Key**: A string containing the ADO.NET provider invariant name specifying the type of database for which a provider is needed.</span></span> <span data-ttu-id="1e545-128">例如，系統會針對機碼 "SqlClient" 傳回 SQL Server 提供者。</span><span class="sxs-lookup"><span data-stu-id="1e545-128">For example, the SQL Server provider is returned for the key "System.Data.SqlClient".</span></span>  

>[!NOTE]
> <span data-ttu-id="1e545-129">如需 EF6 中提供者相關服務的詳細資訊，請參閱 [EF6 提供者模型](xref:ef6/fundamentals/providers/provider-model) 一節。</span><span class="sxs-lookup"><span data-stu-id="1e545-129">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentityinfrastructureidbconnectionfactory"></a><span data-ttu-id="1e545-130">System.string. IDbConnectionFactory。</span><span class="sxs-lookup"><span data-stu-id="1e545-130">System.Data.Entity.Infrastructure.IDbConnectionFactory</span></span>  

<span data-ttu-id="1e545-131">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="1e545-131">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="1e545-132">**傳回的物件**：當 EF 依照慣例建立資料庫連接時，將會使用的連接 factory。</span><span class="sxs-lookup"><span data-stu-id="1e545-132">**Object returned**: The connection factory that will be used when EF creates a database connection by convention.</span></span> <span data-ttu-id="1e545-133">也就是說，當沒有任何連接或連接字串給 EF，而且在 app.config 或 web.config 中找不到任何連接字串時，就會使用此服務依慣例建立連接。</span><span class="sxs-lookup"><span data-stu-id="1e545-133">That is, when no connection or connection string is given to EF, and no connection string can be found in the app.config or web.config, then this service is used to create a connection by convention.</span></span> <span data-ttu-id="1e545-134">變更連線處理站可讓 EF 使用不同類型的資料庫 (例如，預設為 SQL Server Compact Edition) 。</span><span class="sxs-lookup"><span data-stu-id="1e545-134">Changing the connection factory can allow EF to use a different type of database (for example, SQL Server Compact Edition) by default.</span></span>  

<span data-ttu-id="1e545-135">機**碼**：未使用;將會是 null</span><span class="sxs-lookup"><span data-stu-id="1e545-135">**Key**: Not used; will be null</span></span>  

>[!NOTE]
> <span data-ttu-id="1e545-136">如需 EF6 中提供者相關服務的詳細資訊，請參閱 [EF6 提供者模型](xref:ef6/fundamentals/providers/provider-model) 一節。</span><span class="sxs-lookup"><span data-stu-id="1e545-136">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentityinfrastructureimanifesttokenservice"></a><span data-ttu-id="1e545-137">System.string. IManifestTokenService。</span><span class="sxs-lookup"><span data-stu-id="1e545-137">System.Data.Entity.Infrastructure.IManifestTokenService</span></span>  

<span data-ttu-id="1e545-138">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="1e545-138">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="1e545-139">**傳回的物件**：可從連接產生提供者資訊清單權杖的服務。</span><span class="sxs-lookup"><span data-stu-id="1e545-139">**Object returned**: A service that can generate a provider manifest token from a connection.</span></span> <span data-ttu-id="1e545-140">這項服務通常會以兩種方式使用。</span><span class="sxs-lookup"><span data-stu-id="1e545-140">This service is typically used in two ways.</span></span> <span data-ttu-id="1e545-141">首先，它可以用來避免在建立模型時 Code First 連接至資料庫。</span><span class="sxs-lookup"><span data-stu-id="1e545-141">First, it can be used to avoid Code First connecting to the database when building a model.</span></span> <span data-ttu-id="1e545-142">其次，它可以用來強制 Code First 建立特定資料庫版本的模型，例如，即使有時候使用 SQL Server 2008，也可以強制 SQL Server 2005 的模型。</span><span class="sxs-lookup"><span data-stu-id="1e545-142">Second, it can be used to force Code First to build a model for a specific database version -- for example, to force a model for SQL Server 2005 even if sometimes SQL Server 2008 is used.</span></span>  

<span data-ttu-id="1e545-143">**物件存留期**： Singleton--相同的物件可多次使用，並由不同的執行緒同時使用</span><span class="sxs-lookup"><span data-stu-id="1e545-143">**Object lifetime**: Singleton -- the same object may be used multiple times and concurrently by different threads</span></span>  

<span data-ttu-id="1e545-144">機**碼**：未使用;將會是 null</span><span class="sxs-lookup"><span data-stu-id="1e545-144">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructureidbproviderfactoryservice"></a><span data-ttu-id="1e545-145">System.string. IDbProviderFactoryService。</span><span class="sxs-lookup"><span data-stu-id="1e545-145">System.Data.Entity.Infrastructure.IDbProviderFactoryService</span></span>  

<span data-ttu-id="1e545-146">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="1e545-146">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="1e545-147">**傳回的物件**：可以從指定連接取得提供者 factory 的服務。</span><span class="sxs-lookup"><span data-stu-id="1e545-147">**Object returned**: A service that can obtain a provider factory from a given connection.</span></span> <span data-ttu-id="1e545-148">在 .NET 4.5 上，提供者可從連接公開存取。</span><span class="sxs-lookup"><span data-stu-id="1e545-148">On .NET 4.5 the provider is publicly accessible from the connection.</span></span> <span data-ttu-id="1e545-149">在 .NET 4 中，此服務的預設執行會使用一些啟發學習法來尋找相符的提供者。</span><span class="sxs-lookup"><span data-stu-id="1e545-149">On .NET 4 the default implementation of this service uses some heuristics to find the matching provider.</span></span> <span data-ttu-id="1e545-150">如果這些失敗，則可以註冊此服務的新實作為，以提供適當的解決方式。</span><span class="sxs-lookup"><span data-stu-id="1e545-150">If these fail then a new implementation of this service can be registered to provide an appropriate resolution.</span></span>  

<span data-ttu-id="1e545-151">機**碼**：未使用;將會是 null</span><span class="sxs-lookup"><span data-stu-id="1e545-151">**Key**: Not used; will be null</span></span>  

## <a name="funcdbcontext-systemdataentityinfrastructureidbmodelcachekey"></a><span data-ttu-id="1e545-152">Func<DbCoNtext （IDbModelCacheKey）\></span><span class="sxs-lookup"><span data-stu-id="1e545-152">Func<DbContext, System.Data.Entity.Infrastructure.IDbModelCacheKey\></span></span>  

<span data-ttu-id="1e545-153">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="1e545-153">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="1e545-154">**傳回的物件**：會產生指定內容之模型快取索引鍵的 factory。</span><span class="sxs-lookup"><span data-stu-id="1e545-154">**Object returned**: A factory that will generate a model cache key for a given context.</span></span> <span data-ttu-id="1e545-155">根據預設，EF 會為每個提供者的每個 DbCoNtext 類型快取一個模型。</span><span class="sxs-lookup"><span data-stu-id="1e545-155">By default, EF caches one model per DbContext type per provider.</span></span> <span data-ttu-id="1e545-156">這項服務的不同實行可以用來將其他資訊（例如架構名稱）新增至快取索引鍵。</span><span class="sxs-lookup"><span data-stu-id="1e545-156">A different implementation of this service can be used to add other information, such as schema name, to the cache key.</span></span>  

<span data-ttu-id="1e545-157">機**碼**：未使用;將會是 null</span><span class="sxs-lookup"><span data-stu-id="1e545-157">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityspatialdbspatialservices"></a><span data-ttu-id="1e545-158">DbSpatialServices 的資料。</span><span class="sxs-lookup"><span data-stu-id="1e545-158">System.Data.Entity.Spatial.DbSpatialServices</span></span>  

<span data-ttu-id="1e545-159">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="1e545-159">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="1e545-160">**傳回的物件**： EF 空間提供者，可將支援新增至 geography 和 geometry 空間類型的基本 EF 提供者。</span><span class="sxs-lookup"><span data-stu-id="1e545-160">**Object returned**: An EF spatial provider that adds support to the basic EF provider for geography and geometry spatial types.</span></span>  

<span data-ttu-id="1e545-161">**Key**： DbSptialServices 的要求方式有兩種。</span><span class="sxs-lookup"><span data-stu-id="1e545-161">**Key**: DbSptialServices is asked for in two ways.</span></span> <span data-ttu-id="1e545-162">首先，系統會使用 DbProviderInfo 物件來要求提供者專屬的空間服務， (其中包含非變異名稱和資訊清單標記) 作為索引鍵。</span><span class="sxs-lookup"><span data-stu-id="1e545-162">First, provider-specific spatial services are requested using a DbProviderInfo object (which contains invariant name and manifest token) as the key.</span></span> <span data-ttu-id="1e545-163">其次，DbSpatialServices 可以要求沒有金鑰的要求。</span><span class="sxs-lookup"><span data-stu-id="1e545-163">Second, DbSpatialServices can be asked for with no key.</span></span> <span data-ttu-id="1e545-164">這是用來解析建立獨立 DbGeography 或 DbGeometry 類型時所使用的「全域空間提供者」。</span><span class="sxs-lookup"><span data-stu-id="1e545-164">This is used to resolve the "global spatial provider" which is used when creating stand-alone DbGeography or DbGeometry types.</span></span>  

>[!NOTE]
> <span data-ttu-id="1e545-165">如需 EF6 中提供者相關服務的詳細資訊，請參閱 [EF6 提供者模型](xref:ef6/fundamentals/providers/provider-model) 一節。</span><span class="sxs-lookup"><span data-stu-id="1e545-165">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="funcsystemdataentityinfrastructureidbexecutionstrategy"></a><span data-ttu-id="1e545-166">Func<system.string. >idbexecutionstrategy\></span><span class="sxs-lookup"><span data-stu-id="1e545-166">Func<System.Data.Entity.Infrastructure.IDbExecutionStrategy\></span></span>  

<span data-ttu-id="1e545-167">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="1e545-167">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="1e545-168">**傳回的物件**：建立服務的 factory，可讓提供者在對資料庫執行查詢和命令時，執行重試或其他行為。</span><span class="sxs-lookup"><span data-stu-id="1e545-168">**Object returned**: A factory to create a service that allows a provider to implement retries or other behavior when queries and commands are executed against the database.</span></span> <span data-ttu-id="1e545-169">如果未提供任何執行，則 EF 只會執行命令並傳播任何擲回的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="1e545-169">If no implementation is provided, then EF will simply execute the commands and propagate any exceptions thrown.</span></span> <span data-ttu-id="1e545-170">針對 SQL Server，此服務會用來提供重試原則，此原則在針對雲端式資料庫伺服器（例如 SQL Azure）執行時特別有用。</span><span class="sxs-lookup"><span data-stu-id="1e545-170">For SQL Server this service is used to provide a retry policy which is especially useful when running against cloud-based database servers such as SQL Azure.</span></span>  

<span data-ttu-id="1e545-171">索引**鍵**： ExecutionStrategyKey 物件，其中包含提供者的非變異名稱，以及選擇性地將使用執行策略的伺服器名稱。</span><span class="sxs-lookup"><span data-stu-id="1e545-171">**Key**: An ExecutionStrategyKey object that contains the provider invariant name and optionally a server name for which the execution strategy will be used.</span></span>  

>[!NOTE]
> <span data-ttu-id="1e545-172">如需 EF6 中提供者相關服務的詳細資訊，請參閱 [EF6 提供者模型](xref:ef6/fundamentals/providers/provider-model) 一節。</span><span class="sxs-lookup"><span data-stu-id="1e545-172">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="funcdbconnection-string-systemdataentitymigrationshistoryhistorycontext"></a><span data-ttu-id="1e545-173">Func<DbConnection，string，HistoryCoNtext 的資料。\></span><span class="sxs-lookup"><span data-stu-id="1e545-173">Func<DbConnection, string, System.Data.Entity.Migrations.History.HistoryContext\></span></span>  

<span data-ttu-id="1e545-174">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="1e545-174">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="1e545-175">**傳回的物件**： factory，可讓提供者將 HistoryCoNtext 對應設定為 `__MigrationHistory` EF 遷移所使用的資料表。</span><span class="sxs-lookup"><span data-stu-id="1e545-175">**Object returned**: A factory that allows a provider to configure the mapping of the HistoryContext to the `__MigrationHistory` table used by EF Migrations.</span></span> <span data-ttu-id="1e545-176">HistoryCoNtext 是 Code First 的 DbCoNtext，而且可以使用一般的流暢 API 來設定，以變更資料表名稱和資料行對應規格等專案。</span><span class="sxs-lookup"><span data-stu-id="1e545-176">The HistoryContext is a Code First DbContext and can be configured using the normal fluent API to change things like the name of the table and the column mapping specifications.</span></span>  

<span data-ttu-id="1e545-177">機**碼**：未使用;將會是 null</span><span class="sxs-lookup"><span data-stu-id="1e545-177">**Key**: Not used; will be null</span></span>  

>[!NOTE]
> <span data-ttu-id="1e545-178">如需 EF6 中提供者相關服務的詳細資訊，請參閱 [EF6 提供者模型](xref:ef6/fundamentals/providers/provider-model) 一節。</span><span class="sxs-lookup"><span data-stu-id="1e545-178">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdatacommondbproviderfactory"></a><span data-ttu-id="1e545-179">DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="1e545-179">System.Data.Common.DbProviderFactory</span></span>  

<span data-ttu-id="1e545-180">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="1e545-180">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="1e545-181">**傳回的物件**：用於指定提供者非變異名稱的 ADO.NET 提供者。</span><span class="sxs-lookup"><span data-stu-id="1e545-181">**Object returned**: The ADO.NET provider to use for a given provider invariant name.</span></span>  

<span data-ttu-id="1e545-182">索引**鍵**：包含 ADO.NET 提供者非變異名稱的字串</span><span class="sxs-lookup"><span data-stu-id="1e545-182">**Key**: A string containing the ADO.NET provider invariant name</span></span>  

>[!NOTE]
> <span data-ttu-id="1e545-183">這項服務通常不會直接變更，因為預設的執行會使用一般的 ADO.NET 提供者註冊。</span><span class="sxs-lookup"><span data-stu-id="1e545-183">This service is not usually changed directly since the default implementation uses the normal ADO.NET provider registration.</span></span> <span data-ttu-id="1e545-184">如需 EF6 中提供者相關服務的詳細資訊，請參閱 [EF6 提供者模型](xref:ef6/fundamentals/providers/provider-model) 一節。</span><span class="sxs-lookup"><span data-stu-id="1e545-184">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentityinfrastructureiproviderinvariantname"></a><span data-ttu-id="1e545-185">System.string. IProviderInvariantName。</span><span class="sxs-lookup"><span data-stu-id="1e545-185">System.Data.Entity.Infrastructure.IProviderInvariantName</span></span>  

<span data-ttu-id="1e545-186">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="1e545-186">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="1e545-187">**傳回的物件**：用來判斷指定之 DbProviderFactory 類型的提供者不變名稱的服務。</span><span class="sxs-lookup"><span data-stu-id="1e545-187">**Object returned**: a service that is used to determine a provider invariant name for a given type of DbProviderFactory.</span></span> <span data-ttu-id="1e545-188">這項服務的預設執行會使用 ADO.NET 提供者註冊。</span><span class="sxs-lookup"><span data-stu-id="1e545-188">The default implementation of this service uses the ADO.NET provider registration.</span></span> <span data-ttu-id="1e545-189">這表示，如果 ADO.NET 提供者未以正常方式註冊，因為 EF 正在解析 DbProviderFactory，則也需要解析這項服務。</span><span class="sxs-lookup"><span data-stu-id="1e545-189">This means that if the ADO.NET provider is not registered in the normal way because DbProviderFactory is being resolved by EF, then it will also be necessary to resolve this service.</span></span>  

<span data-ttu-id="1e545-190">**Key**：需要非變異名稱的 DbProviderFactory 實例。</span><span class="sxs-lookup"><span data-stu-id="1e545-190">**Key**: The DbProviderFactory instance for which an invariant name is required.</span></span>  

>[!NOTE]
> <span data-ttu-id="1e545-191">如需 EF6 中提供者相關服務的詳細資訊，請參閱 [EF6 提供者模型](xref:ef6/fundamentals/providers/provider-model) 一節。</span><span class="sxs-lookup"><span data-stu-id="1e545-191">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentitycoremappingviewgenerationiviewassemblycache"></a><span data-ttu-id="1e545-192">ViewGeneration. IViewAssemblyCache （資料）。</span><span class="sxs-lookup"><span data-stu-id="1e545-192">System.Data.Entity.Core.Mapping.ViewGeneration.IViewAssemblyCache</span></span>  

<span data-ttu-id="1e545-193">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="1e545-193">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="1e545-194">**傳回的物件**：包含預先產生之視圖的元件快取。</span><span class="sxs-lookup"><span data-stu-id="1e545-194">**Object returned**: a cache of the assemblies that contain pre-generated views.</span></span> <span data-ttu-id="1e545-195">取代通常用來讓 EF 知道哪些元件包含預先產生的視圖，而不進行任何探索。</span><span class="sxs-lookup"><span data-stu-id="1e545-195">A replacement is typically used to let EF know which assemblies contain pre-generated views without doing any discovery.</span></span>  

<span data-ttu-id="1e545-196">機**碼**：未使用;將會是 null</span><span class="sxs-lookup"><span data-stu-id="1e545-196">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructurepluralizationipluralizationservice"></a><span data-ttu-id="1e545-197">System.string. 複數表示. IPluralizationService</span><span class="sxs-lookup"><span data-stu-id="1e545-197">System.Data.Entity.Infrastructure.Pluralization.IPluralizationService</span></span>

<span data-ttu-id="1e545-198">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="1e545-198">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="1e545-199">**傳回的物件**： EF 用來複數化和單數化名稱的服務。</span><span class="sxs-lookup"><span data-stu-id="1e545-199">**Object returned**: a service used by EF to pluralize and singularize names.</span></span> <span data-ttu-id="1e545-200">預設會使用英文複數表示服務。</span><span class="sxs-lookup"><span data-stu-id="1e545-200">By default an English pluralization service is used.</span></span>  

<span data-ttu-id="1e545-201">機**碼**：未使用;將會是 null</span><span class="sxs-lookup"><span data-stu-id="1e545-201">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructureinterceptionidbinterceptor"></a><span data-ttu-id="1e545-202">System.string... IDbInterceptor</span><span class="sxs-lookup"><span data-stu-id="1e545-202">System.Data.Entity.Infrastructure.Interception.IDbInterceptor</span></span>  

<span data-ttu-id="1e545-203">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="1e545-203">**Version introduced**: EF6.0.0</span></span>

<span data-ttu-id="1e545-204">**傳回的物件**：應在應用程式啟動時註冊的任何攔截器。</span><span class="sxs-lookup"><span data-stu-id="1e545-204">**Objects returned**: Any interceptors that should be registered when the application starts.</span></span> <span data-ttu-id="1e545-205">請注意，這些物件是使用 GetServices 呼叫所要求，而且任何相依性解析程式所傳回的所有攔截器都會註冊。</span><span class="sxs-lookup"><span data-stu-id="1e545-205">Note that these objects are requested using the GetServices call and all interceptors returned by any dependency resolver will registered.</span></span>

<span data-ttu-id="1e545-206">機**碼**：未使用;將會是 null。</span><span class="sxs-lookup"><span data-stu-id="1e545-206">**Key**: Not used; will be null.</span></span>  

## <a name="funcsystemdataentitydbcontext-actionstring-systemdataentityinfrastructureinterceptiondatabaselogformatter"></a><span data-ttu-id="1e545-207">Func<DbCoNtext，Action<字串，DatabaseLogFormatter 的 Func. （ \> 資料）。\></span><span class="sxs-lookup"><span data-stu-id="1e545-207">Func<System.Data.Entity.DbContext, Action<string\>, System.Data.Entity.Infrastructure.Interception.DatabaseLogFormatter\></span></span>  

<span data-ttu-id="1e545-208">**引進的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="1e545-208">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="1e545-209">**傳回的物件**：將用來建立資料庫記錄格式器的 factory，此格式器將在內容時使用。指定的內容上已設定 Database .Log 屬性。</span><span class="sxs-lookup"><span data-stu-id="1e545-209">**Object returned**: A factory that will be used to create the database log formatter that will be used when the context.Database.Log property is set on the given context.</span></span>  

<span data-ttu-id="1e545-210">機**碼**：未使用;將會是 null。</span><span class="sxs-lookup"><span data-stu-id="1e545-210">**Key**: Not used; will be null.</span></span>  

## <a name="funcsystemdataentitydbcontext"></a><span data-ttu-id="1e545-211">Func<system.string. DbCoNtext\></span><span class="sxs-lookup"><span data-stu-id="1e545-211">Func<System.Data.Entity.DbContext\></span></span>  

<span data-ttu-id="1e545-212">**引進的版本**： ef 6.1。0</span><span class="sxs-lookup"><span data-stu-id="1e545-212">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="1e545-213">**傳回的物件**：當內容沒有可存取的無參數函式時，將用來建立內容實例以進行遷移的 factory。</span><span class="sxs-lookup"><span data-stu-id="1e545-213">**Object returned**: A factory that will be used to create context instances for Migrations when the context does not have an accessible parameterless constructor.</span></span>  

<span data-ttu-id="1e545-214">索引**鍵**：需要 factory 之衍生 DbCoNtext 型別的型別物件。</span><span class="sxs-lookup"><span data-stu-id="1e545-214">**Key**: The Type object for the type of the derived DbContext for which a factory is needed.</span></span>  

## <a name="funcsystemdataentitycoremetadataedmimetadataannotationserializer"></a><span data-ttu-id="1e545-215">Func<system.string. IMetadataAnnotationSerializer 的資料。\></span><span class="sxs-lookup"><span data-stu-id="1e545-215">Func<System.Data.Entity.Core.Metadata.Edm.IMetadataAnnotationSerializer\></span></span>  

<span data-ttu-id="1e545-216">**引進的版本**： ef 6.1。0</span><span class="sxs-lookup"><span data-stu-id="1e545-216">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="1e545-217">**傳回的物件**：將用來建立序列化程式以序列化強型別自訂批註的 factory，以便將它們序列化並 DESTERILIZED 至 XML 以用於 Code First 移轉。</span><span class="sxs-lookup"><span data-stu-id="1e545-217">**Object returned**: A factory that will be used to create serializers for serialization of strongly-typed custom annotations such that they can be serialized and desterilized into XML for use in Code First Migrations.</span></span>  

<span data-ttu-id="1e545-218">**Key**：正在序列化或還原序列化的批註名稱。</span><span class="sxs-lookup"><span data-stu-id="1e545-218">**Key**: The name of the annotation that is being serialized or deserialized.</span></span>  

## <a name="funcsystemdataentityinfrastructuretransactionhandler"></a><span data-ttu-id="1e545-219">Func<system.string. TransactionHandler\></span><span class="sxs-lookup"><span data-stu-id="1e545-219">Func<System.Data.Entity.Infrastructure.TransactionHandler\></span></span>  

<span data-ttu-id="1e545-220">**引進的版本**： ef 6.1。0</span><span class="sxs-lookup"><span data-stu-id="1e545-220">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="1e545-221">**傳回的物件**：將用來建立交易處理常式的 factory，以便可以針對處理認可失敗的情況套用特殊處理。</span><span class="sxs-lookup"><span data-stu-id="1e545-221">**Object returned**: A factory that will be used to create handlers for transactions so that special handling can be applied for situations such as handling commit failures.</span></span>  

<span data-ttu-id="1e545-222">**Key**： ExecutionStrategyKey 物件，其中包含提供者的非變異名稱，以及選擇性地使用交易處理常式的伺服器名稱。</span><span class="sxs-lookup"><span data-stu-id="1e545-222">**Key**: An ExecutionStrategyKey object that contains the provider invariant name and optionally a server name for which the transaction handler will be used.</span></span>  
