---
title: Entity Framework 6 提供者模型-EF6
author: divega
ms.date: 06/27/2018
ms.assetid: 066832F0-D51B-4655-8BE7-C983C557E0E4
ms.openlocfilehash: d07a8689fe968bb1512095a59a61abc7ac346a31
ms.sourcegitcommit: 5e11125c9b838ce356d673ef5504aec477321724
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50022320"
---
# <a name="the-entity-framework-6-provider-model"></a><span data-ttu-id="47d77-102">Entity Framework 6 提供者模型</span><span class="sxs-lookup"><span data-stu-id="47d77-102">The Entity Framework 6 provider model</span></span>

<span data-ttu-id="47d77-103">Entity Framework 提供者模型可讓 Entity Framework，可搭配不同類型的資料庫伺服器。</span><span class="sxs-lookup"><span data-stu-id="47d77-103">The Entity Framework provider model allows Entity Framework to be used with different types of database server.</span></span> <span data-ttu-id="47d77-104">例如，一個提供者可以插入允許使用於 Microsoft SQL Server，而另一個提供者可以插入允許使用於 Microsoft SQL Server Compact Edition EF 的 EF。</span><span class="sxs-lookup"><span data-stu-id="47d77-104">For example, one provider can be plugged in to allow EF to be used against Microsoft SQL Server, while another provider can be plugged into to allow EF to be used against Microsoft SQL Server Compact Edition.</span></span> <span data-ttu-id="47d77-105">我們已了解的 EF6 的提供者都位於[Entity Framework 提供者](~/ef6/fundamentals/providers/index.md)頁面。</span><span class="sxs-lookup"><span data-stu-id="47d77-105">The providers for EF6 that we are aware of can be found on the [Entity Framework providers](~/ef6/fundamentals/providers/index.md) page.</span></span>

<span data-ttu-id="47d77-106">特定變更都必須與提供者，以允許在開放原始碼授權下發行 EF 的 EF 互動的方式。</span><span class="sxs-lookup"><span data-stu-id="47d77-106">Certain changes were required to the way EF interacts with providers to allow EF to be released under an open source license.</span></span> <span data-ttu-id="47d77-107">這些變更需要重建的 EF 對 EF6 組件，以及新的機制，來註冊提供者的提供者。</span><span class="sxs-lookup"><span data-stu-id="47d77-107">These changes require rebuilding of EF providers against the EF6 assemblies together with new mechanisms for registration of the provider.</span></span>

## <a name="rebuilding"></a><span data-ttu-id="47d77-108">重建</span><span class="sxs-lookup"><span data-stu-id="47d77-108">Rebuilding</span></span>

<span data-ttu-id="47d77-109">與 EF6 先前已在.NET framework 的核心程式碼現在正在隨附為頻外 (OOB) 組件。</span><span class="sxs-lookup"><span data-stu-id="47d77-109">With EF6 the core code that was previously part of the .NET Framework is now being shipped as out-of-band (OOB) assemblies.</span></span> <span data-ttu-id="47d77-110">如何建置應用程式與 EF6 的詳細資料可於[更新應用程式的 EF6](~/ef6/what-is-new/upgrading-to-ef6.md)頁面。</span><span class="sxs-lookup"><span data-stu-id="47d77-110">Details on how to build applications against EF6 can be found on the [Updating applications for EF6](~/ef6/what-is-new/upgrading-to-ef6.md) page.</span></span> <span data-ttu-id="47d77-111">提供者也必須重建使用這些指示。</span><span class="sxs-lookup"><span data-stu-id="47d77-111">Providers will also need to be rebuilt using these instructions.</span></span>

## <a name="provider-types-overview"></a><span data-ttu-id="47d77-112">提供者類型概觀</span><span class="sxs-lookup"><span data-stu-id="47d77-112">Provider types overview</span></span>

<span data-ttu-id="47d77-113">EF 提供者其實這些服務 （適用於基底類別），從擴充或實作 （適用於介面） 的 CLR 型別所定義的提供者特定服務的集合。</span><span class="sxs-lookup"><span data-stu-id="47d77-113">An EF provider is really a collection of provider-specific services defined by CLR types that these services extend from (for a base class) or implement (for an interface).</span></span> <span data-ttu-id="47d77-114">這些服務的兩個是基本和所需的 EF 完全運作。</span><span class="sxs-lookup"><span data-stu-id="47d77-114">Two of these services are fundamental and necessary for EF to function at all.</span></span> <span data-ttu-id="47d77-115">有些則是選擇性的只需要特定的功能是必要和/或目標的特定資料庫伺服器無法使用這些服務的預設實作會實作。</span><span class="sxs-lookup"><span data-stu-id="47d77-115">Others are optional and only need to be implemented if specific functionality is required and/or the default implementations of these services does not work for the specific database server being targeted.</span></span>

## <a name="fundamental-provider-types"></a><span data-ttu-id="47d77-116">基本的提供者類型</span><span class="sxs-lookup"><span data-stu-id="47d77-116">Fundamental provider types</span></span>

### <a name="dbproviderfactory"></a><span data-ttu-id="47d77-117">DbProviderFactory</span><span class="sxs-lookup"><span data-stu-id="47d77-117">DbProviderFactory</span></span>

<span data-ttu-id="47d77-118">EF 取決於衍生自特定型別[System.Data.Common.DbProviderFactory](https://msdn.microsoft.com/library/system.data.common.dbproviderfactory.aspx)執行所有的低層級的資料庫存取權。</span><span class="sxs-lookup"><span data-stu-id="47d77-118">EF depends on having a type derived from [System.Data.Common.DbProviderFactory](https://msdn.microsoft.com/library/system.data.common.dbproviderfactory.aspx) for performing all low-level database access.</span></span> <span data-ttu-id="47d77-119">DbProviderFactory 不是實際的 EF 的一部分，而是改為在.NET Framework 中的 ADO.NET 提供者做為進入點的類別可以使用 EF，其他的 O/RMs 或直接由應用程式來取得執行個體的連接、 命令、 參數和其他的 ADO.NET 抽象概念，在提供者無關的方式。</span><span class="sxs-lookup"><span data-stu-id="47d77-119">DbProviderFactory is not actually part of EF but is instead a class in the .NET Framework that serves an entry point for ADO.NET providers that can be used by EF, other O/RMs or directly by an application to obtain instances of connections, commands, parameters and other ADO.NET abstractions in a provider agnostic way.</span></span> <span data-ttu-id="47d77-120">DbProviderFactory 的詳細資訊位於[ADO.NET 的 MSDN 文件](https://msdn.microsoft.com/library/a6cd7c08.aspx)。</span><span class="sxs-lookup"><span data-stu-id="47d77-120">More information about DbProviderFactory an be found in the [MSDN documentation for ADO.NET](https://msdn.microsoft.com/library/a6cd7c08.aspx).</span></span>

### <a name="dbproviderservices"></a><span data-ttu-id="47d77-121">DbProviderServices</span><span class="sxs-lookup"><span data-stu-id="47d77-121">DbProviderServices</span></span>

<span data-ttu-id="47d77-122">EF 取決於特定的型別衍生自 DbProviderServices 提供額外的功能所需的 EF，上方已 ADO.NET 提供者所提供的功能。</span><span class="sxs-lookup"><span data-stu-id="47d77-122">EF depends on having a type derived from DbProviderServices for providing additional functionality needed by EF on top of the functionality already provided by the ADO.NET provider.</span></span> <span data-ttu-id="47d77-123">在較舊版本的 EF DbProviderServices 類別是.NET Framework 的一部分，並 System.Data.Common 命名空間中找不到。</span><span class="sxs-lookup"><span data-stu-id="47d77-123">In older versions of EF the DbProviderServices class was part of the .NET Framework and was found in the System.Data.Common namespace.</span></span> <span data-ttu-id="47d77-124">開始使用 EF6 這個類別現在是 EntityFramework.dll 的一部分，並在 System.Data.Entity.Core.Common 命名空間。</span><span class="sxs-lookup"><span data-stu-id="47d77-124">Starting with EF6 this class is now part of EntityFramework.dll and is in the System.Data.Entity.Core.Common namespace.</span></span>

<span data-ttu-id="47d77-125">更多詳細的 DbProviderServices 實作基本的功能都位於[MSDN](https://msdn.microsoft.com/library/ee789835.aspx)。</span><span class="sxs-lookup"><span data-stu-id="47d77-125">More details about the fundamental functionality of a DbProviderServices implementation can be found on [MSDN](https://msdn.microsoft.com/library/ee789835.aspx).</span></span> <span data-ttu-id="47d77-126">不過請注意，截至撰寫這項資訊不會更新 EF6 雖然大部分的概念是仍然有效。</span><span class="sxs-lookup"><span data-stu-id="47d77-126">However, note that as of the time of writing this information is not updated for EF6 although most of the concepts are still valid.</span></span> <span data-ttu-id="47d77-127">SQL Server 和 SQL Server Compact 的 DbProviderServices 實作也已簽入至[開放原始碼程式碼基底](https://github.com/aspnet/EntityFramework6/)，可做為其他實作的實用參考。</span><span class="sxs-lookup"><span data-stu-id="47d77-127">The SQL Server and SQL Server Compact implementations of DbProviderServices are also checked into to the [open-source codebase](https://github.com/aspnet/EntityFramework6/) and can serve as useful references for other implementations.</span></span>

<span data-ttu-id="47d77-128">在較舊版本的 EF 從 ADO.NET 提供者直接取得要使用的 DbProviderServices 實作。</span><span class="sxs-lookup"><span data-stu-id="47d77-128">In older versions of EF the DbProviderServices implementation to use was obtained directly from an ADO.NET provider.</span></span> <span data-ttu-id="47d77-129">這是由轉換為 IServiceProvider DbProviderFactory，呼叫 GetService 方法。</span><span class="sxs-lookup"><span data-stu-id="47d77-129">This was done by casting DbProviderFactory to IServiceProvider and calling the GetService method.</span></span> <span data-ttu-id="47d77-130">這與緊密結合 EF 提供者的 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="47d77-130">This tightly coupled the EF provider to the DbProviderFactory.</span></span> <span data-ttu-id="47d77-131">此結合禁止 EF 中移出.NET Framework，因此 EF6 的這個緊密結合已移除，並直接在應用程式的組態檔或程式碼為基礎，現在已註冊的 DbProviderServices 實作更多詳細資料中所述的組態_註冊的 DbProviderServices_下一節。</span><span class="sxs-lookup"><span data-stu-id="47d77-131">This coupling blocked EF from being moved out of the .NET Framework and therefore for EF6 this tight coupling has been removed and an implementation of DbProviderServices is now registered directly in the application’s configuration file or in code-based configuration as described in more detail the _Registering DbProviderServices_ section below.</span></span>

## <a name="additional-services"></a><span data-ttu-id="47d77-132">其他服務</span><span class="sxs-lookup"><span data-stu-id="47d77-132">Additional services</span></span>

<span data-ttu-id="47d77-133">除了上面所述的基本服務還有許多其他服務 EF 使用，也就是一律或有時提供者專屬。</span><span class="sxs-lookup"><span data-stu-id="47d77-133">In addition to the fundamental services described above there are also many other services used by EF which are either always or sometimes provider-specific.</span></span> <span data-ttu-id="47d77-134">DbProviderServices 實作可以提供這些服務的預設提供者特定實作。</span><span class="sxs-lookup"><span data-stu-id="47d77-134">Default provider-specific implementations of these services can be supplied by a DbProviderServices implementation.</span></span> <span data-ttu-id="47d77-135">應用程式也可以覆寫的實作，這些服務，或提供實作，當 DbProviderServices 類型不會提供預設值。</span><span class="sxs-lookup"><span data-stu-id="47d77-135">Applications can also override the implementations of these services, or provide implementations when a DbProviderServices type does not provide a default.</span></span> <span data-ttu-id="47d77-136">這描述更詳細地_解析其他服務_下一節。</span><span class="sxs-lookup"><span data-stu-id="47d77-136">This is described in more detail in the _Resolving additional services_ section below.</span></span>

<span data-ttu-id="47d77-137">以下列出提供者可能會感興趣的提供者的其他服務類型。</span><span class="sxs-lookup"><span data-stu-id="47d77-137">The additional service types that a provider may be of interest to a provider are listed below.</span></span> <span data-ttu-id="47d77-138">每個服務類型的更多詳細可以找到 API 文件中。</span><span class="sxs-lookup"><span data-stu-id="47d77-138">More details about each of these service types can be found in the API documentation.</span></span>

### <a name="idbexecutionstrategy"></a><span data-ttu-id="47d77-139">IDbExecutionStrategy</span><span class="sxs-lookup"><span data-stu-id="47d77-139">IDbExecutionStrategy</span></span>

<span data-ttu-id="47d77-140">這是選擇性的服務，可讓提供者，以針對資料庫執行查詢和命令時，實作重試或其他行為。</span><span class="sxs-lookup"><span data-stu-id="47d77-140">This is an optional service that allows a provider to implement retries or other behavior when queries and commands are executed against the database.</span></span> <span data-ttu-id="47d77-141">如果不提供實作，然後 EF 將只執行命令，並傳播擲回任何例外狀況。</span><span class="sxs-lookup"><span data-stu-id="47d77-141">If no implementation is provided, then EF will simply execute the commands and propagate any exceptions thrown.</span></span> <span data-ttu-id="47d77-142">適用於 SQL Server 會提供重試原則，也就是特別有用，例如 SQL Azure 的雲端式資料庫伺服器上執行時使用此服務。</span><span class="sxs-lookup"><span data-stu-id="47d77-142">For SQL Server this service is used to provide a retry policy which is especially useful when running against cloud-based database servers such as SQL Azure.</span></span>

### <a name="idbconnectionfactory"></a><span data-ttu-id="47d77-143">IDbConnectionFactory</span><span class="sxs-lookup"><span data-stu-id="47d77-143">IDbConnectionFactory</span></span>

<span data-ttu-id="47d77-144">這是選擇性的服務，可讓提供者來指定資料庫名稱時的慣例來建立 DbConnection 物件。</span><span class="sxs-lookup"><span data-stu-id="47d77-144">This is an optional service that allows a provider to create DbConnection objects by convention when given only a database name.</span></span> <span data-ttu-id="47d77-145">請注意，雖然這項服務的 DbProviderServices 實作，它已因為 EF 4.1 存在，而且也可以明確地設定組態檔中或在程式碼，即可解決。</span><span class="sxs-lookup"><span data-stu-id="47d77-145">Note that while this service can be resolved by a DbProviderServices implementation it has been present since EF 4.1 and can also be explicitly set in either the config file or in code.</span></span> <span data-ttu-id="47d77-146">提供者只會有機會解決這項服務，如果它註冊為預設的提供者 (請參閱_的預設提供者_下方)，如果預設連接 factory 尚未設定其他位置。</span><span class="sxs-lookup"><span data-stu-id="47d77-146">The provider will only get a chance to resolve this service if it registered as the default provider (see _The default provider_ below) and if a default connection factory has not been set elsewhere.</span></span>

### <a name="dbspatialservices"></a><span data-ttu-id="47d77-147">DbSpatialServices</span><span class="sxs-lookup"><span data-stu-id="47d77-147">DbSpatialServices</span></span>

<span data-ttu-id="47d77-148">這是選擇性的服務，可讓提供者將 geography 和 geometry 空間類型的支援。</span><span class="sxs-lookup"><span data-stu-id="47d77-148">This is an optional services that allows a provider to add support for geography and geometry spatial types.</span></span> <span data-ttu-id="47d77-149">為了讓應用程式使用 EF 使用的空間類型，必須提供這項服務的實作。</span><span class="sxs-lookup"><span data-stu-id="47d77-149">An implementation of this service must be supplied in order for an application to use EF with spatial types.</span></span> <span data-ttu-id="47d77-150">DbSptialServices 要求有兩種。</span><span class="sxs-lookup"><span data-stu-id="47d77-150">DbSptialServices is asked for in two ways.</span></span> <span data-ttu-id="47d77-151">首先，提供者特定的空間服務要求使用 DbProviderInfo 物件 (其中包含非變異名稱和資訊清單語彙基元) 做為索引鍵。</span><span class="sxs-lookup"><span data-stu-id="47d77-151">First, provider-specific spatial services are requested using a DbProviderInfo object (which contains invariant name and manifest token) as key.</span></span> <span data-ttu-id="47d77-152">第二，DbSpatialServices 可要求沒有索引鍵。</span><span class="sxs-lookup"><span data-stu-id="47d77-152">Second, DbSpatialServices can be asked for with no key.</span></span> <span data-ttu-id="47d77-153">這用來解決 「 全域空間提供者 」 時，建立獨立的 DbGeography 或 DbGeometry 類型使用。</span><span class="sxs-lookup"><span data-stu-id="47d77-153">This is used to resolve the “global spatial provider” that is used when creating stand-alone DbGeography or DbGeometry types.</span></span>

### <a name="migrationsqlgenerator"></a><span data-ttu-id="47d77-154">MigrationSqlGenerator</span><span class="sxs-lookup"><span data-stu-id="47d77-154">MigrationSqlGenerator</span></span>

<span data-ttu-id="47d77-155">這是選擇性的服務，可讓用於 Code First 所建立和修改資料庫結構描述中使用 SQL 產生的 EF 移轉。</span><span class="sxs-lookup"><span data-stu-id="47d77-155">This is an optional service that allows EF Migrations to be used for the generation of SQL used in creating and modifying database schemas by Code First.</span></span> <span data-ttu-id="47d77-156">實作，才能支援移轉。</span><span class="sxs-lookup"><span data-stu-id="47d77-156">An implementation is required in order to support Migrations.</span></span> <span data-ttu-id="47d77-157">如果未提供實作，然後它會也可用時使用資料庫初始設定式或 Database.Create; 方法建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="47d77-157">If an implementation is provided then it will also be used when databases are created using database initializers or the Database.Create method.</span></span>

### <a name="funcdbconnection-string-historycontextfactory"></a><span data-ttu-id="47d77-158">L o c k < DbConnection HistoryContextFactory 的字串 ></span><span class="sxs-lookup"><span data-stu-id="47d77-158">Func<DbConnection, string, HistoryContextFactory></span></span>

<span data-ttu-id="47d77-159">這是選擇性的服務，可讓提供者來設定對應至 HistoryContext`__MigrationHistory`使用 EF 移轉的資料表。</span><span class="sxs-lookup"><span data-stu-id="47d77-159">This is an optional service that allows a provider to configure the mapping of the HistoryContext to the `__MigrationHistory` table used by EF Migrations.</span></span> <span data-ttu-id="47d77-160">HistoryContext 是程式碼的第一個 DbContext，可以使用一般的 fluent API 來變更資料表和資料行對應規格的名稱等項目進行設定。</span><span class="sxs-lookup"><span data-stu-id="47d77-160">The HistoryContext is a Code First DbContext and can be configured using the normal fluent API to change things like the name of the table and the column mapping specifications.</span></span> <span data-ttu-id="47d77-161">如果該提供者支援所有的預設資料表和資料行對應，可能會針對給定的資料庫伺服器運作這項服務由 EF 傳回所有提供者的預設實作。</span><span class="sxs-lookup"><span data-stu-id="47d77-161">The default implementation of this service returned by EF for all providers may work for a given database server if all the default table and column mappings are supported by that provider.</span></span> <span data-ttu-id="47d77-162">在此情況下提供者不會不需要提供此服務的實作。</span><span class="sxs-lookup"><span data-stu-id="47d77-162">In such a case the provider does not need to supply an implementation of this service.</span></span>

### <a name="idbproviderfactoryresolver"></a><span data-ttu-id="47d77-163">IDbProviderFactoryResolver</span><span class="sxs-lookup"><span data-stu-id="47d77-163">IDbProviderFactoryResolver</span></span>

<span data-ttu-id="47d77-164">這是選擇性的服務，從指定的 DbConnection 物件取得正確的 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="47d77-164">This is an optional service for obtaining the correct DbProviderFactory from a given DbConnection object.</span></span> <span data-ttu-id="47d77-165">這項服務由 EF 傳回所有提供者的預設實作是適用於所有提供者。</span><span class="sxs-lookup"><span data-stu-id="47d77-165">The default implementation of this service returned by EF for all providers is intended to work for all providers.</span></span> <span data-ttu-id="47d77-166">不過，.NET 4 上執行時，DbProviderFactory 不是可公開存取一個如果其 DbConnections。</span><span class="sxs-lookup"><span data-stu-id="47d77-166">However, when running on .NET 4, the DbProviderFactory is not publicly accessible from one if its DbConnections.</span></span> <span data-ttu-id="47d77-167">因此，EF 會使用一些啟發學習法來搜尋已註冊的提供者，以尋找相符項目。</span><span class="sxs-lookup"><span data-stu-id="47d77-167">Therefore, EF uses some heuristics to search the registered providers to find a match.</span></span> <span data-ttu-id="47d77-168">很可能對於某些提供者將會失敗的這些啟發學習法，而在此情況下，提供者應該提供新的實作。</span><span class="sxs-lookup"><span data-stu-id="47d77-168">It is possible that for some providers these heuristics will fail and in such situations the provider should supply a new implementation.</span></span>

## <a name="registering-dbproviderservices"></a><span data-ttu-id="47d77-169">註冊 DbProviderServices</span><span class="sxs-lookup"><span data-stu-id="47d77-169">Registering DbProviderServices</span></span>

<span data-ttu-id="47d77-170">在應用程式的組態檔 （app.config 或 web.config） 中或使用程式碼為基礎的組態，您可以註冊要使用的 DbProviderServices 實作。</span><span class="sxs-lookup"><span data-stu-id="47d77-170">The DbProviderServices implementation to use can be registered either in the application’s configuration file (app.config or web.config) or using code-based configuration.</span></span> <span data-ttu-id="47d77-171">在任一情況下註冊會使用提供者的 「 非變異名稱 」 做為索引鍵。</span><span class="sxs-lookup"><span data-stu-id="47d77-171">In either case the registration uses the provider’s “invariant name” as a key.</span></span> <span data-ttu-id="47d77-172">這可讓多個提供者註冊和單一應用程式中使用。</span><span class="sxs-lookup"><span data-stu-id="47d77-172">This allows multiple providers to be registered and used in a single application.</span></span> <span data-ttu-id="47d77-173">使用 EF 註冊的非變異名稱等同於使用 ADO.NET 提供者註冊和連接字串的非變異名稱。</span><span class="sxs-lookup"><span data-stu-id="47d77-173">The invariant name used for EF registrations is the same as the invariant name used for ADO.NET provider registration and connection strings.</span></span> <span data-ttu-id="47d77-174">例如，適用於 SQL Server 的非變異名稱會使用"System.Data.SqlClient"。</span><span class="sxs-lookup"><span data-stu-id="47d77-174">For example, for SQL Server the invariant name “System.Data.SqlClient” is used.</span></span>

### <a name="config-file-registration"></a><span data-ttu-id="47d77-175">組態檔註冊</span><span class="sxs-lookup"><span data-stu-id="47d77-175">Config file registration</span></span>

<span data-ttu-id="47d77-176">要使用的 DbProviderServices 類型會註冊為提供者中的項目 entityFramework 應用程式的組態檔區段的提供者清單。</span><span class="sxs-lookup"><span data-stu-id="47d77-176">The DbProviderServices type to use is registered as a provider element in the providers list of the entityFramework section of the application’s config file.</span></span> <span data-ttu-id="47d77-177">例如: </span><span class="sxs-lookup"><span data-stu-id="47d77-177">For example:</span></span>

``` xml
<entityFramework>
  <providers>
    <provider invariantName="My.Invariant.Name" type="MyProvider.MyProviderServices, MyAssembly" />
  </providers>
</entityFramework>
```

<span data-ttu-id="47d77-178">_型別_字串必須是要使用的 DbProviderServices 實作的組件限定的型別名稱。</span><span class="sxs-lookup"><span data-stu-id="47d77-178">The _type_ string must be the assembly-qualified type name of the DbProviderServices implementation to use.</span></span>

### <a name="code-based-registration"></a><span data-ttu-id="47d77-179">程式碼架構註冊</span><span class="sxs-lookup"><span data-stu-id="47d77-179">Code-based registration</span></span>

<span data-ttu-id="47d77-180">從 EF6 的提供者可以也使用註冊的程式碼。</span><span class="sxs-lookup"><span data-stu-id="47d77-180">Starting with EF6 providers can also be registered using code.</span></span> <span data-ttu-id="47d77-181">這可讓 EF 用不需要變更應用程式的組態檔的提供者。</span><span class="sxs-lookup"><span data-stu-id="47d77-181">This allows an EF provider to be used without any change to the application’s configuration file.</span></span> <span data-ttu-id="47d77-182">若要使用的程式碼為基礎的組態應用程式應該建立 DbConfiguration 類別中所述[程式碼為基礎的設定文件](https://msdn.com/data/jj680699)。</span><span class="sxs-lookup"><span data-stu-id="47d77-182">To use code-based configuration an application should create a DbConfiguration class as described in the [code-based configuration documentation](https://msdn.com/data/jj680699).</span></span> <span data-ttu-id="47d77-183">DbConfiguration 類別的建構函式應該都會接著呼叫 SetProviderServices 註冊 EF 提供者。</span><span class="sxs-lookup"><span data-stu-id="47d77-183">The constructor of the DbConfiguration class should then call SetProviderServices to register the EF provider.</span></span> <span data-ttu-id="47d77-184">例如: </span><span class="sxs-lookup"><span data-stu-id="47d77-184">For example:</span></span>

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetProviderServices("My.New.Provider", new MyProviderServices());
    }
}
```

## <a name="resolving-additional-services"></a><span data-ttu-id="47d77-185">解析其他服務</span><span class="sxs-lookup"><span data-stu-id="47d77-185">Resolving additional services</span></span>

<span data-ttu-id="47d77-186">如上方所述_提供者型別概觀_區段，DbProviderServices 類別也可用來解析其他服務。</span><span class="sxs-lookup"><span data-stu-id="47d77-186">As mentioned above in the _Provider types overview_ section, a DbProviderServices class can also be used to resolve additional services.</span></span> <span data-ttu-id="47d77-187">這可能是因為 DbProviderServices 實作 IDbDependencyResolver 和每個已註冊的 DbProviderServices 型別會新增為 「 預設的解析程式 」。</span><span class="sxs-lookup"><span data-stu-id="47d77-187">This is possible because DbProviderServices implements IDbDependencyResolver and each registered DbProviderServices type is added as a “default resolver”.</span></span> <span data-ttu-id="47d77-188">更詳細地說明 IDbDpendencyResolver 機制[相依性解析](~/ef6/fundamentals/configuring/dependency-resolution.md)。</span><span class="sxs-lookup"><span data-stu-id="47d77-188">The IDbDpendencyResolver mechanism is described in more detail in [Dependency Resolution](~/ef6/fundamentals/configuring/dependency-resolution.md).</span></span> <span data-ttu-id="47d77-189">不過，不需要了解在這個規格來解析其他服務提供者中的所有概念。</span><span class="sxs-lookup"><span data-stu-id="47d77-189">However, it is not necessary to understand all the concepts in this specification to resolve additional services in a provider.</span></span>

<span data-ttu-id="47d77-190">若要解決其他服務提供者的最常見方式是 DbProviderServices 類別的建構函式中每個服務呼叫 DbProviderServices.AddDependencyResolver。</span><span class="sxs-lookup"><span data-stu-id="47d77-190">The most common way for a provider to resolve additional services is to call DbProviderServices.AddDependencyResolver for each service in the constructor of the DbProviderServices class.</span></span> <span data-ttu-id="47d77-191">比方說，SqlProviderServices （SQL Server EF 提供者） 會有程式碼如下所示的初始設定：</span><span class="sxs-lookup"><span data-stu-id="47d77-191">For example, SqlProviderServices (the EF provider for SQL Server) has code similar to this for initialization:</span></span>

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

<span data-ttu-id="47d77-192">這個建構函式會使用下列協助程式類別：</span><span class="sxs-lookup"><span data-stu-id="47d77-192">This constructor uses the following helper classes:</span></span>

*   <span data-ttu-id="47d77-193">SingletonDependencyResolver： 提供簡單的方式來解決單一服務，亦即，相同的執行個體傳回每次稱為 GetService 的服務。</span><span class="sxs-lookup"><span data-stu-id="47d77-193">SingletonDependencyResolver: provides a simple way to resolve Singleton services—that is, services for which the same instance is returned each time that GetService is called.</span></span> <span data-ttu-id="47d77-194">暫時性服務通常會註冊為單一處理站將用於視需要建立暫時性的執行個體中。</span><span class="sxs-lookup"><span data-stu-id="47d77-194">Transient services are often registered as a singleton factory that will be used to create transient instances on demand.</span></span>
*   <span data-ttu-id="47d77-195">ExecutionStrategyResolver: 解析程式傳回 IExecutionStrategy 實作特定。</span><span class="sxs-lookup"><span data-stu-id="47d77-195">ExecutionStrategyResolver: a resolver specific to returning IExecutionStrategy implementations.</span></span>

<span data-ttu-id="47d77-196">而不是使用 DbProviderServices.AddDependencyResolver 它，也可以覆寫 DbProviderServices.GetService，並直接解決其他服務。</span><span class="sxs-lookup"><span data-stu-id="47d77-196">Instead of using DbProviderServices.AddDependencyResolver it is also possible to override DbProviderServices.GetService and resolve additional services directly.</span></span> <span data-ttu-id="47d77-197">當 EF 必須由特定型別，並在某些情況下，指定索引鍵所定義的服務時，會呼叫這個方法。</span><span class="sxs-lookup"><span data-stu-id="47d77-197">This method will be called when EF needs a service defined by a certain type and, in some cases, for a given key.</span></span> <span data-ttu-id="47d77-198">如果可以或傳回 null 以退出傳回服務並改為允許以解決問題的另一個類別，這個方法應傳回服務。</span><span class="sxs-lookup"><span data-stu-id="47d77-198">The method should return the service if it can, or return null to opt-out of returning the service and instead allow another class to resolve it.</span></span> <span data-ttu-id="47d77-199">例如，若要解決的預設連接 factory GetService 中的程式碼可能會看起來像這樣：</span><span class="sxs-lookup"><span data-stu-id="47d77-199">For example, to resolve the default connection factory the code in GetService might look something like this:</span></span>

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

### <a name="registration-order"></a><span data-ttu-id="47d77-200">註冊順序</span><span class="sxs-lookup"><span data-stu-id="47d77-200">Registration order</span></span>

<span data-ttu-id="47d77-201">當多個的 DbProviderServices 實作已註冊的應用程式組態檔中會新增為次要的解析程式所列出的順序。</span><span class="sxs-lookup"><span data-stu-id="47d77-201">When multiple DbProviderServices implementations are registered in an application’s config file they will be added as secondary resolvers in the order that they are listed.</span></span> <span data-ttu-id="47d77-202">因為解析程式會固定加入到次要的解析程式鏈結頂端這表示，在清單結尾處的提供者就有機會解決之前的其他相依性。</span><span class="sxs-lookup"><span data-stu-id="47d77-202">Since resolvers are always added to the top of the secondary resolver chain this means that the provider at the end of the list will get a chance to resolve dependencies before the others.</span></span> <span data-ttu-id="47d77-203">（可能會感到一點違反直覺一開始，但它才有意義您想像從清單的每個提供者並堆疊頂端的現有的提供者）。</span><span class="sxs-lookup"><span data-stu-id="47d77-203">(This can seem a little counter-intuitive at first, but it makes sense if you imagine taking each provider out of the list and stacking it on top of the existing providers.)</span></span>

<span data-ttu-id="47d77-204">這種排序通常並不重要由於大部分的提供者服務是特定提供者，並依提供者非變異名稱索引鍵。</span><span class="sxs-lookup"><span data-stu-id="47d77-204">This ordering usually doesn’t matter because most provider services are provider-specific and keyed by provider invariant name.</span></span> <span data-ttu-id="47d77-205">不過，服務，都不做為索引鍵提供者非變異名稱或某些其他提供者專屬的金鑰，服務就會解析根據這種排序。</span><span class="sxs-lookup"><span data-stu-id="47d77-205">However, for services that are not keyed by provider invariant name or some other provider-specific key the service will be resolved based on this ordering.</span></span> <span data-ttu-id="47d77-206">例如，如果未明確設定不同地方 else，預設連接 factory 會來自於鏈結中最上層的提供者。</span><span class="sxs-lookup"><span data-stu-id="47d77-206">For example, if it is not explicitly set differently somewhere else, then the default connection factory will come from the topmost provider in the chain.</span></span>

## <a name="additional-config-file-registrations"></a><span data-ttu-id="47d77-207">其他的組態檔註冊</span><span class="sxs-lookup"><span data-stu-id="47d77-207">Additional config file registrations</span></span>

<span data-ttu-id="47d77-208">可以明確註冊一些上面所述，直接在應用程式的組態檔中的其他提供者服務。</span><span class="sxs-lookup"><span data-stu-id="47d77-208">It is possible to explicitly register some of the additional provider services described above directly in an application’s config file.</span></span> <span data-ttu-id="47d77-209">這是在組態檔中的註冊時將會使用而不是任何的 DbProviderServices 實作的 GetService 方法所傳回的項目。</span><span class="sxs-lookup"><span data-stu-id="47d77-209">When this is done the registration in the config file will be used instead of anything returned by the GetService method of the DbProviderServices implementation.</span></span>

### <a name="registering-the-default-connection-factory"></a><span data-ttu-id="47d77-210">註冊預設的連接 factory</span><span class="sxs-lookup"><span data-stu-id="47d77-210">Registering the default connection factory</span></span>

<span data-ttu-id="47d77-211">開始 EF5 EntityFramework NuGet 套件會自動在組態檔中註冊的 SQL Express 連線處理站或 LocalDb 的連接 factory。</span><span class="sxs-lookup"><span data-stu-id="47d77-211">Starting with EF5 the EntityFramework NuGet package automatically registered either the SQL Express connection factory or the LocalDb connection factory in the config file.</span></span>

<span data-ttu-id="47d77-212">例如: </span><span class="sxs-lookup"><span data-stu-id="47d77-212">For example:</span></span>

``` xml
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework" >
</entityFramework>
```

<span data-ttu-id="47d77-213">_型別_是預設連線處理站，必須實作 IDbConnectionFactory 的組件限定類型名稱。</span><span class="sxs-lookup"><span data-stu-id="47d77-213">The _type_ is the assembly-qualified type name for the default connection factory, which must implement IDbConnectionFactory.</span></span>

<span data-ttu-id="47d77-214">建議的提供者 NuGet 套件安裝時，這種方式設定預設連接 factory。</span><span class="sxs-lookup"><span data-stu-id="47d77-214">It is recommended that a provider NuGet package set the default connection factory in this way when installed.</span></span> <span data-ttu-id="47d77-215">請參閱_提供者的 NuGet 套件_如下。</span><span class="sxs-lookup"><span data-stu-id="47d77-215">See _NuGet Packages for providers_ below.</span></span>

## <a name="additional-ef6-provider-changes"></a><span data-ttu-id="47d77-216">EF6 的其他提供者變更</span><span class="sxs-lookup"><span data-stu-id="47d77-216">Additional EF6 provider changes</span></span>

### <a name="spatial-provider-changes"></a><span data-ttu-id="47d77-217">空間的提供者的變更</span><span class="sxs-lookup"><span data-stu-id="47d77-217">Spatial provider changes</span></span>

<span data-ttu-id="47d77-218">支援空間型別提供者，現在必須從 DbSpatialDataReader 衍生的類別上實作一些其他方法：</span><span class="sxs-lookup"><span data-stu-id="47d77-218">Providers that support spatial types must now implement some additional methods on classes deriving from DbSpatialDataReader:</span></span>

*   `public abstract bool IsGeographyColumn(int ordinal)`
*   `public abstract bool IsGeometryColumn(int ordinal)`

<span data-ttu-id="47d77-219">另外還有新的非同步版本，建議您以覆寫的預設實作會委派給同步的方法，並因此不會執行非同步的現有方法的：</span><span class="sxs-lookup"><span data-stu-id="47d77-219">There are also new asynchronous versions of existing methods that are recommended to be overridden as the default implementations delegate to the synchronous methods and therefore do not execute asynchronously:</span></span>

*   `public virtual Task<DbGeography> GetGeographyAsync(int ordinal, CancellationToken cancellationToken)`
*   `public virtual Task<DbGeometry> GetGeometryAsync(int ordinal, CancellationToken cancellationToken)`

### <a name="native-support-for-enumerablecontains"></a><span data-ttu-id="47d77-220">Enumerable.Contains 的原生支援</span><span class="sxs-lookup"><span data-stu-id="47d77-220">Native support for Enumerable.Contains</span></span>

<span data-ttu-id="47d77-221">EF6 導入了新的運算式型別，DbInExpression，已新增 LINQ 查詢中使用 Enumerable.Contains 周圍解決效能問題。</span><span class="sxs-lookup"><span data-stu-id="47d77-221">EF6 introduces a new expression type, DbInExpression, which was added to address performance issues around use of Enumerable.Contains in LINQ queries.</span></span> <span data-ttu-id="47d77-222">DbProviderManifest 類別具有新的虛擬方法，稱為 SupportsInExpression，由 EF 來判斷是否提供者會處理新的運算式型別呼叫。</span><span class="sxs-lookup"><span data-stu-id="47d77-222">The DbProviderManifest class has a new virtual method, SupportsInExpression, which is called by EF to determine if a provider handles the new expression type.</span></span> <span data-ttu-id="47d77-223">與現有的提供者實作的相容性的方法會傳回 false。</span><span class="sxs-lookup"><span data-stu-id="47d77-223">For compatibility with existing provider implementations the method returns false.</span></span> <span data-ttu-id="47d77-224">受益於這項改善，EF6 提供者可以加入程式碼來處理 DbInExpression，並覆寫 SupportsInExpression 傳回 true。</span><span class="sxs-lookup"><span data-stu-id="47d77-224">To benefit from this improvement, an EF6 provider can add code to handle DbInExpression and override SupportsInExpression to return true.</span></span> <span data-ttu-id="47d77-225">藉由呼叫 DbExpressionBuilder.In 方法，就可以建立 DbInExpression 的執行個體。</span><span class="sxs-lookup"><span data-stu-id="47d77-225">An instance of DbInExpression can be created by calling the DbExpressionBuilder.In method.</span></span> <span data-ttu-id="47d77-226">DbInExpression 執行個體所組成的 DbExpression，通常代表資料表資料行，以及一份要進行相符測試 DbConstantExpression。</span><span class="sxs-lookup"><span data-stu-id="47d77-226">A DbInExpression instance is composed of a DbExpression, usually representing a table column, and a list of DbConstantExpression to test for a match.</span></span>

## <a name="nuget-packages-for-providers"></a><span data-ttu-id="47d77-227">提供者的 NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="47d77-227">NuGet packages for providers</span></span>

<span data-ttu-id="47d77-228">將 EF6 提供者提供一個方式是發行 NuGet 套件的形式。</span><span class="sxs-lookup"><span data-stu-id="47d77-228">One way to make an EF6 provider available is to release it as a NuGet package.</span></span> <span data-ttu-id="47d77-229">使用 NuGet 套件有下列優點：</span><span class="sxs-lookup"><span data-stu-id="47d77-229">Using a NuGet package has the following advantages:</span></span>

*   <span data-ttu-id="47d77-230">它是簡單易用的 NuGet，以加入應用程式的組態檔中的提供者註冊</span><span class="sxs-lookup"><span data-stu-id="47d77-230">It is easy to use NuGet to add the provider registration to the application’s config file</span></span>
*   <span data-ttu-id="47d77-231">可以在組態檔來進行其他變更，若要設定預設連接 factory，以便依照慣例建立的連接將會使用已註冊的提供者</span><span class="sxs-lookup"><span data-stu-id="47d77-231">Additional changes can be made to the config file to set the default connection factory so that connections made by convention will use the registered provider</span></span>
*   <span data-ttu-id="47d77-232">新增繫結重新導向，使 EF6 提供者應該繼續運作，甚至在發行新的 EF 封裝之後，才會處理的 NuGet</span><span class="sxs-lookup"><span data-stu-id="47d77-232">NuGet handles adding binding redirects so that the EF6 provider should continue to work even after a new EF package is released</span></span>

<span data-ttu-id="47d77-233">這個範例是 EntityFramework.SqlServerCompact 套件隨附於[開放原始碼程式碼基底](http://github.com/aspnet/entityframework6)。</span><span class="sxs-lookup"><span data-stu-id="47d77-233">An example of this is the EntityFramework.SqlServerCompact package which is included in the [open source codebase](http://github.com/aspnet/entityframework6).</span></span> <span data-ttu-id="47d77-234">此套件會提供良好的範本來建立 EF 提供者 NuGet 封裝。</span><span class="sxs-lookup"><span data-stu-id="47d77-234">This package provides a good template for creating EF provider NuGet packages.</span></span>

### <a name="powershell-commands"></a><span data-ttu-id="47d77-235">PowerShell 命令</span><span class="sxs-lookup"><span data-stu-id="47d77-235">PowerShell commands</span></span>

<span data-ttu-id="47d77-236">安裝 EntityFramework NuGet 套件時它會註冊 PowerShell 模組，包含非常適合用來提供者套件的兩個命令：</span><span class="sxs-lookup"><span data-stu-id="47d77-236">When the EntityFramework NuGet package is installed it registers a PowerShell module that contains two commands that are very useful for provider packages:</span></span>

*   <span data-ttu-id="47d77-237">新增 EFProvider 目標專案的組態檔中的提供者將新的實體，並確保它是已註冊的提供者清單的結尾。</span><span class="sxs-lookup"><span data-stu-id="47d77-237">Add-EFProvider adds a new entity for the provider in the target project’s configuration file and makes sure it is at the end of the list of registered providers.</span></span>
*   <span data-ttu-id="47d77-238">新增 EFDefaultConnectionFactory 新增或更新目標專案的組態檔中的 defaultConnectionFactory 註冊。</span><span class="sxs-lookup"><span data-stu-id="47d77-238">Add-EFDefaultConnectionFactory either adds or updates the defaultConnectionFactory registration in the target project’s configuration file.</span></span>

<span data-ttu-id="47d77-239">這兩種指令會負責將 entityFramework 區段新增至設定檔及新增提供者集合，如有必要。</span><span class="sxs-lookup"><span data-stu-id="47d77-239">Both these commands take care of adding an entityFramework section to the config file and adding a providers collection if necessary.</span></span>

<span data-ttu-id="47d77-240">它可能是這些命令，從 install.ps1 NuGet 指令碼呼叫。</span><span class="sxs-lookup"><span data-stu-id="47d77-240">It is intended that these commands be called from the install.ps1 NuGet script.</span></span> <span data-ttu-id="47d77-241">例如，SQL Compact 提供者的 install.ps1 看起來會如下所示：</span><span class="sxs-lookup"><span data-stu-id="47d77-241">For example, install.ps1 for the SQL Compact provider looks similar to this:</span></span>

``` powershell
param($installPath, $toolsPath, $package, $project)
Add-EFDefaultConnectionFactory $project 'System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework' -ConstructorArguments 'System.Data.SqlServerCe.4.0'
Add-EFProvider $project 'System.Data.SqlServerCe.4.0' 'System.Data.Entity.SqlServerCompact.SqlCeProviderServices, EntityFramework.SqlServerCompact'</pre>
```

<span data-ttu-id="47d77-242">可以取得有關這些命令的詳細資訊，藉由使用套件管理員主控台視窗中的 取得說明。</span><span class="sxs-lookup"><span data-stu-id="47d77-242">More information about these commands can be obtained by using get-help in the Package Manager Console window.</span></span>

## <a name="wrapping-providers"></a><span data-ttu-id="47d77-243">文繞圖提供者</span><span class="sxs-lookup"><span data-stu-id="47d77-243">Wrapping providers</span></span>

<span data-ttu-id="47d77-244">文繞圖提供者會包裝現有的提供者來擴充其他功能，例如分析或追蹤功能，使用 EF 和/或 ADO.NET 提供者。</span><span class="sxs-lookup"><span data-stu-id="47d77-244">A wrapping provider is an EF and/or ADO.NET provider that wraps an existing provider to extend it with other functionality such as profiling or tracing capabilities.</span></span> <span data-ttu-id="47d77-245">包裝提供者可以註冊以一般方式，但它通常會較方便攔截的解析度的提供者相關的服務來設定在執行階段包裝提供者。</span><span class="sxs-lookup"><span data-stu-id="47d77-245">Wrapping providers can be registered in the normal way, but it is often more convenient to setup the wrapping provider at runtime by intercepting the resolution of provider-related services.</span></span> <span data-ttu-id="47d77-246">靜態事件 OnLockingConfiguration DbConfiguration 類別可用來執行這項操作。</span><span class="sxs-lookup"><span data-stu-id="47d77-246">The static event OnLockingConfiguration on the DbConfiguration class can be used to do this.</span></span>

<span data-ttu-id="47d77-247">OnLockingConfiguration 稱為 EF 判定應用程式定義域的所有 EF 設定將從都取得後，但它已鎖定可供使用。</span><span class="sxs-lookup"><span data-stu-id="47d77-247">OnLockingConfiguration is called after EF has determined where all EF configuration for the app domain will be obtained from but before it is locked for use.</span></span> <span data-ttu-id="47d77-248">在應用程式啟動 （EF 用之前） 的應用程式應該註冊這個事件的事件處理常式。</span><span class="sxs-lookup"><span data-stu-id="47d77-248">At app startup (before EF is used) the app should register an event handler for this event.</span></span> <span data-ttu-id="47d77-249">（我們想要新增支援在組態檔中註冊這個處理常式，但這還不支援）。然後，事件處理常式應該要 ReplaceService 需要包裝每個服務的呼叫。</span><span class="sxs-lookup"><span data-stu-id="47d77-249">(We are considering adding support for registering this handler in the config file but this is not yet supported.) The event handler should then make a call to ReplaceService for every service that needs to be wrapped.</span></span>  

<span data-ttu-id="47d77-250">比方說，換行 IDbConnectionFactory 和 DbProviderService，應註冊處理常式，如下所示：</span><span class="sxs-lookup"><span data-stu-id="47d77-250">For example, to wrap IDbConnectionFactory and DbProviderService, a handler something like this should be registered:</span></span>

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

<span data-ttu-id="47d77-251">已經解決，而且現在應該包裝以及金鑰用來解析服務的服務會傳遞至處理常式。</span><span class="sxs-lookup"><span data-stu-id="47d77-251">The service that has been resolved and should now be wrapped together with the key that was used to resolve the service are passed to the handler.</span></span> <span data-ttu-id="47d77-252">這個處理常式然後可以包裝此服務，並傳回的服務取代的已包裝的版本。</span><span class="sxs-lookup"><span data-stu-id="47d77-252">The handler can then wrap this service and replace the returned service with the wrapped version.</span></span>

## <a name="resolving-a-dbproviderfactory-with-ef"></a><span data-ttu-id="47d77-253">解決使用 EF DbProviderFactory</span><span class="sxs-lookup"><span data-stu-id="47d77-253">Resolving a DbProviderFactory with EF</span></span>

<span data-ttu-id="47d77-254">DbProviderFactory 是其中一個基本提供者型別中所述，所需的 EF_提供者型別概觀_上一節。</span><span class="sxs-lookup"><span data-stu-id="47d77-254">DbProviderFactory is one of the fundamental provider types needed by EF as described in the _Provider types overview_ section above.</span></span> <span data-ttu-id="47d77-255">如先前所述，它不是 EF 類型和註冊通常不是 EF 組態的一部分，但會改為在 machine.config 檔案及/或應用程式的組態檔的一般 ADO.NET 提供者註冊。</span><span class="sxs-lookup"><span data-stu-id="47d77-255">As already mentioned, It is not an EF type and registration is usually not part of EF configuration, but is instead the normal ADO.NET provider registration in the machine.config file and/or application’s config file.</span></span>

<span data-ttu-id="47d77-256">儘管此 EF 仍會使用其一般的相依性解析機制尋找 DbProviderFactory 時使用。</span><span class="sxs-lookup"><span data-stu-id="47d77-256">Despite this EF still uses its normal dependency resolution mechanism when looking for a DbProviderFactory to use.</span></span> <span data-ttu-id="47d77-257">預設解析程式組態檔中使用標準 ADO.NET 註冊，因此這是通常是透明的。</span><span class="sxs-lookup"><span data-stu-id="47d77-257">The default resolver uses the normal ADO.NET registration in the config files and so this is usually transparent.</span></span> <span data-ttu-id="47d77-258">因為標準相依性解析使用機制，但這表示 IDbDependencyResolver 可用來解決 DbProviderFactory，即使未完成一般 ADO.NET 註冊。</span><span class="sxs-lookup"><span data-stu-id="47d77-258">But because of the normal dependency resolution mechanism is used it means that an IDbDependencyResolver can be used to resolve a DbProviderFactory even when normal ADO.NET registration has not been done.</span></span>

<span data-ttu-id="47d77-259">在這種方式解決 DbProviderFactory 有幾個含意：</span><span class="sxs-lookup"><span data-stu-id="47d77-259">Resolving DbProviderFactory in this way has several implications:</span></span>

*   <span data-ttu-id="47d77-260">使用程式碼為基礎的組態的應用程式可以呼叫類別中新增其 DbConfiguration 註冊適當的 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="47d77-260">An application using code-based configuration can add calls in their DbConfiguration class to register the appropriate DbProviderFactory.</span></span> <span data-ttu-id="47d77-261">這會特別有用的應用程式，不想要 （或無法） 進行完全使用的任何檔案為基礎的組態。</span><span class="sxs-lookup"><span data-stu-id="47d77-261">This is especially useful for applications that do not want to (or cannot) make use of any file-based configuration at all.</span></span>
*   <span data-ttu-id="47d77-262">服務可以換行，或取代中所述，使用 ReplaceService_包裝提供者_上一節</span><span class="sxs-lookup"><span data-stu-id="47d77-262">The service can be wrapped or replaced using ReplaceService as described in the _Wrapping providers_ section above</span></span>
*   <span data-ttu-id="47d77-263">理論上來說，DbProviderServices 實作無法解析的 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="47d77-263">Theoretically, a DbProviderServices implementation could resolve a DbProviderFactory.</span></span>

<span data-ttu-id="47d77-264">關於執行任何這些項目，請注意重要的一點是，它們只會影響 EF 所 DbProviderFactory 查閱。</span><span class="sxs-lookup"><span data-stu-id="47d77-264">The important point to note about doing any of these things is that they will only affect the lookup of DbProviderFactory by EF.</span></span> <span data-ttu-id="47d77-265">其他非 EF 程式碼可能仍然會預期以一般方式註冊的 ADO.NET 提供者，如果找不到註冊可能會失敗。</span><span class="sxs-lookup"><span data-stu-id="47d77-265">Other non-EF code may still expect the ADO.NET provider to be registered in the normal way and may fail if the registration is not found.</span></span> <span data-ttu-id="47d77-266">基於這個理由是通常更適合用來在一般的 ADO.NET 方法中註冊的 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="47d77-266">For this reason it is normally better for a DbProviderFactory to be registered in the normal ADO.NET way.</span></span>

### <a name="related-services"></a><span data-ttu-id="47d77-267">相關的服務</span><span class="sxs-lookup"><span data-stu-id="47d77-267">Related services</span></span>

<span data-ttu-id="47d77-268">如果使用 EF 來解決 DbProviderFactory，它應該也解決 IProviderInvariantName 和 IDbProviderFactoryResolver 服務。</span><span class="sxs-lookup"><span data-stu-id="47d77-268">If EF is used to resolve a DbProviderFactory, then it should also resolve the IProviderInvariantName and IDbProviderFactoryResolver services.</span></span>

<span data-ttu-id="47d77-269">IProviderInvariantName 是一項服務，用來判斷 DbProviderFactory 指定類型的提供者非變異名稱。</span><span class="sxs-lookup"><span data-stu-id="47d77-269">IProviderInvariantName is a service that is used to determine a provider invariant name for a given type of DbProviderFactory.</span></span> <span data-ttu-id="47d77-270">這項服務的預設實作會使用 ADO.NET 提供者註冊。</span><span class="sxs-lookup"><span data-stu-id="47d77-270">The default implementation of this service uses the ADO.NET provider registration.</span></span> <span data-ttu-id="47d77-271">這表示，如果因為 EF 所解析的 DbProviderFactory，ADO.NET 提供者不會以一般方式註冊，則它也會解決這項服務所需的。</span><span class="sxs-lookup"><span data-stu-id="47d77-271">This means that if the ADO.NET provider is not registered in the normal way because DbProviderFactory is being resolved by EF, then it will also be necessary to resolve this service.</span></span> <span data-ttu-id="47d77-272">請注意使用 DbConfiguration.SetProviderFactory 方法時，會自動加入此服務的解析程式。</span><span class="sxs-lookup"><span data-stu-id="47d77-272">Note that a resolver for this service is automatically added when using the DbConfiguration.SetProviderFactory method.</span></span>

<span data-ttu-id="47d77-273">中所述_提供者型別概觀_上方區段中，IDbProviderFactoryResolver 用來從指定的 DbConnection 物件取得正確的 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="47d77-273">As described in the _Provider types overview_ section above, the IDbProviderFactoryResolver is used to obtain the correct DbProviderFactory from a given DbConnection object.</span></span> <span data-ttu-id="47d77-274">在.NET 4 上執行時，此服務的預設實作會使用 ADO.NET 提供者註冊。</span><span class="sxs-lookup"><span data-stu-id="47d77-274">The default implementation of this service when running on .NET 4 uses the ADO.NET provider registration.</span></span> <span data-ttu-id="47d77-275">這表示，如果因為 EF 所解析的 DbProviderFactory，ADO.NET 提供者不會以一般方式註冊，則它也會解決這項服務所需的。</span><span class="sxs-lookup"><span data-stu-id="47d77-275">This means that if the ADO.NET provider is not registered in the normal way because DbProviderFactory is being resolved by EF, then it will also be necessary to resolve this service.</span></span>
