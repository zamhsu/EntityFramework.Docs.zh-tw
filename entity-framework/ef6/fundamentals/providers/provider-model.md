---
title: Entity Framework 6 提供者模型-EF6
author: divega
ms.date: 06/27/2018
ms.assetid: 066832F0-D51B-4655-8BE7-C983C557E0E4
ms.openlocfilehash: 8bda3f51e8934f2add862c30e60f1185f068c515
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78419357"
---
# <a name="the-entity-framework-6-provider-model"></a><span data-ttu-id="ed69c-102">Entity Framework 6 提供者模型</span><span class="sxs-lookup"><span data-stu-id="ed69c-102">The Entity Framework 6 provider model</span></span>

<span data-ttu-id="ed69c-103">Entity Framework 提供者模型可讓 Entity Framework 與不同類型的資料庫伺服器搭配使用。</span><span class="sxs-lookup"><span data-stu-id="ed69c-103">The Entity Framework provider model allows Entity Framework to be used with different types of database server.</span></span> <span data-ttu-id="ed69c-104">例如，可以插入一個提供者，以允許 EF 用於 Microsoft SQL Server，而另一個提供者則可插入以允許 EF 用於 Microsoft SQL Server Compact Edition。</span><span class="sxs-lookup"><span data-stu-id="ed69c-104">For example, one provider can be plugged in to allow EF to be used against Microsoft SQL Server, while another provider can be plugged into to allow EF to be used against Microsoft SQL Server Compact Edition.</span></span> <span data-ttu-id="ed69c-105">我們知道的 EF6 提供者可以在 [ [Entity Framework 提供者](~/ef6/fundamentals/providers/index.md)] 頁面上找到。</span><span class="sxs-lookup"><span data-stu-id="ed69c-105">The providers for EF6 that we are aware of can be found on the [Entity Framework providers](~/ef6/fundamentals/providers/index.md) page.</span></span>

<span data-ttu-id="ed69c-106">EF 與提供者互動的方式需要進行某些變更，以允許在開放原始碼授權下發行 EF。</span><span class="sxs-lookup"><span data-stu-id="ed69c-106">Certain changes were required to the way EF interacts with providers to allow EF to be released under an open source license.</span></span> <span data-ttu-id="ed69c-107">這些變更需要為 EF6 元件重建 EF 提供者，以及註冊提供者的新機制。</span><span class="sxs-lookup"><span data-stu-id="ed69c-107">These changes require rebuilding of EF providers against the EF6 assemblies together with new mechanisms for registration of the provider.</span></span>

## <a name="rebuilding"></a><span data-ttu-id="ed69c-108">重建</span><span class="sxs-lookup"><span data-stu-id="ed69c-108">Rebuilding</span></span>

<span data-ttu-id="ed69c-109">在 EF6 中，先前屬於 .NET Framework 的核心程式代碼現在會以頻外（OOB）元件的形式出貨。</span><span class="sxs-lookup"><span data-stu-id="ed69c-109">With EF6 the core code that was previously part of the .NET Framework is now being shipped as out-of-band (OOB) assemblies.</span></span> <span data-ttu-id="ed69c-110">如需如何針對 EF6 建立應用程式的詳細資訊，請參閱[更新 EF6 的應用程式](~/ef6/what-is-new/upgrading-to-ef6.md)頁面。</span><span class="sxs-lookup"><span data-stu-id="ed69c-110">Details on how to build applications against EF6 can be found on the [Updating applications for EF6](~/ef6/what-is-new/upgrading-to-ef6.md) page.</span></span> <span data-ttu-id="ed69c-111">提供者也必須使用這些指示重建。</span><span class="sxs-lookup"><span data-stu-id="ed69c-111">Providers will also need to be rebuilt using these instructions.</span></span>

## <a name="provider-types-overview"></a><span data-ttu-id="ed69c-112">提供者類型總覽</span><span class="sxs-lookup"><span data-stu-id="ed69c-112">Provider types overview</span></span>

<span data-ttu-id="ed69c-113">EF 提供者其實是一組由 CLR 型別所定義的提供者專屬服務，這些服務是由這些服務從（針對基類）延伸，或是針對介面來執行。</span><span class="sxs-lookup"><span data-stu-id="ed69c-113">An EF provider is really a collection of provider-specific services defined by CLR types that these services extend from (for a base class) or implement (for an interface).</span></span> <span data-ttu-id="ed69c-114">這些服務的其中兩個是基本的，而且 EF 必須能夠運作。</span><span class="sxs-lookup"><span data-stu-id="ed69c-114">Two of these services are fundamental and necessary for EF to function at all.</span></span> <span data-ttu-id="ed69c-115">有些則是選擇性的，而且只有在需要特定功能時才需要執行，而且（或）這些服務的預設執行不適用於目標的特定資料庫伺服器。</span><span class="sxs-lookup"><span data-stu-id="ed69c-115">Others are optional and only need to be implemented if specific functionality is required and/or the default implementations of these services does not work for the specific database server being targeted.</span></span>

## <a name="fundamental-provider-types"></a><span data-ttu-id="ed69c-116">基本提供者類型</span><span class="sxs-lookup"><span data-stu-id="ed69c-116">Fundamental provider types</span></span>

### <a name="dbproviderfactory"></a><span data-ttu-id="ed69c-117">DbProviderFactory</span><span class="sxs-lookup"><span data-stu-id="ed69c-117">DbProviderFactory</span></span>

<span data-ttu-id="ed69c-118">EF 相依于具有衍生自[DbProviderFactory](https://msdn.microsoft.com/library/system.data.common.dbproviderfactory.aspx)的類型，以執行所有低層級的資料庫存取。</span><span class="sxs-lookup"><span data-stu-id="ed69c-118">EF depends on having a type derived from [System.Data.Common.DbProviderFactory](https://msdn.microsoft.com/library/system.data.common.dbproviderfactory.aspx) for performing all low-level database access.</span></span> <span data-ttu-id="ed69c-119">DbProviderFactory 實際上不是 EF 的一部分，而是 .NET Framework 中的類別，它會提供 ADO.NET 提供者的進入點，讓 EF、其他 O/RMs 或直接由應用程式使用，以提供者無從驗證的方式取得連接、命令、參數和其他 ADO.NET 抽象的實例。</span><span class="sxs-lookup"><span data-stu-id="ed69c-119">DbProviderFactory is not actually part of EF but is instead a class in the .NET Framework that serves an entry point for ADO.NET providers that can be used by EF, other O/RMs or directly by an application to obtain instances of connections, commands, parameters and other ADO.NET abstractions in a provider agnostic way.</span></span> <span data-ttu-id="ed69c-120">如需 DbProviderFactory 的詳細資訊，請參閱[MSDN 檔中的 ADO.NET](https://msdn.microsoft.com/library/a6cd7c08.aspx)。</span><span class="sxs-lookup"><span data-stu-id="ed69c-120">More information about DbProviderFactory can be found in the [MSDN documentation for ADO.NET](https://msdn.microsoft.com/library/a6cd7c08.aspx).</span></span>

### <a name="dbproviderservices"></a><span data-ttu-id="ed69c-121">Dbproviderservices.createdatabase</span><span class="sxs-lookup"><span data-stu-id="ed69c-121">DbProviderServices</span></span>

<span data-ttu-id="ed69c-122">EF 相依于擁有衍生自 Dbproviderservices.createdatabase 的型別，以便在 ADO.NET 提供者已提供的功能之上提供 EF 所需的額外功能。</span><span class="sxs-lookup"><span data-stu-id="ed69c-122">EF depends on having a type derived from DbProviderServices for providing additional functionality needed by EF on top of the functionality already provided by the ADO.NET provider.</span></span> <span data-ttu-id="ed69c-123">在較舊版本的 EF 中，Dbproviderservices.createdatabase 類別是 .NET Framework 的一部分，而且是在 System.web 命名空間中找到。</span><span class="sxs-lookup"><span data-stu-id="ed69c-123">In older versions of EF the DbProviderServices class was part of the .NET Framework and was found in the System.Data.Common namespace.</span></span> <span data-ttu-id="ed69c-124">從 EF6 開始，這個類別現在是 EntityFramework 的一部分，而且是在 Common 命名空間中。</span><span class="sxs-lookup"><span data-stu-id="ed69c-124">Starting with EF6 this class is now part of EntityFramework.dll and is in the System.Data.Entity.Core.Common namespace.</span></span>

<span data-ttu-id="ed69c-125">如需 Dbproviderservices.createdatabase 執行的基本功能的詳細資訊，請參閱[MSDN](https://msdn.microsoft.com/library/ee789835.aspx)。</span><span class="sxs-lookup"><span data-stu-id="ed69c-125">More details about the fundamental functionality of a DbProviderServices implementation can be found on [MSDN](https://msdn.microsoft.com/library/ee789835.aspx).</span></span> <span data-ttu-id="ed69c-126">不過，請注意，在撰寫本文時，不會針對 EF6 更新這項資訊，雖然大部分的概念仍然有效。</span><span class="sxs-lookup"><span data-stu-id="ed69c-126">However, note that as of the time of writing this information is not updated for EF6 although most of the concepts are still valid.</span></span> <span data-ttu-id="ed69c-127">Dbproviderservices.createdatabase 的 SQL Server 和 SQL Server Compact 實作用也會簽入到[開放原始碼程式碼基](https://github.com/aspnet/EntityFramework6/)底，而且可以做為其他執行的實用參考。</span><span class="sxs-lookup"><span data-stu-id="ed69c-127">The SQL Server and SQL Server Compact implementations of DbProviderServices are also checked into to the [open-source codebase](https://github.com/aspnet/EntityFramework6/) and can serve as useful references for other implementations.</span></span>

<span data-ttu-id="ed69c-128">在較舊版本的 EF 中，會直接從 ADO.NET 提供者取得要使用的 Dbproviderservices.createdatabase 執行。</span><span class="sxs-lookup"><span data-stu-id="ed69c-128">In older versions of EF the DbProviderServices implementation to use was obtained directly from an ADO.NET provider.</span></span> <span data-ttu-id="ed69c-129">這是藉由將 DbProviderFactory 轉換成 IServiceProvider 並呼叫 GetService 方法來完成。</span><span class="sxs-lookup"><span data-stu-id="ed69c-129">This was done by casting DbProviderFactory to IServiceProvider and calling the GetService method.</span></span> <span data-ttu-id="ed69c-130">這會將 EF 提供者緊密結合到 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="ed69c-130">This tightly coupled the EF provider to the DbProviderFactory.</span></span> <span data-ttu-id="ed69c-131">這項結合會使 EF 無法從 .NET Framework 移出，因此 EF6 此緊密結合已移除，而 Dbproviderservices.createdatabase 的執行現在會直接在應用程式的設定檔中註冊，或是以程式碼為基礎的設定進行，如下面的 _註冊 dbproviderservices.createdatabase一節中所述。_</span><span class="sxs-lookup"><span data-stu-id="ed69c-131">This coupling blocked EF from being moved out of the .NET Framework and therefore for EF6 this tight coupling has been removed and an implementation of DbProviderServices is now registered directly in the application’s configuration file or in code-based configuration as described in more detail the _Registering DbProviderServices_ section below.</span></span>

## <a name="additional-services"></a><span data-ttu-id="ed69c-132">其他服務</span><span class="sxs-lookup"><span data-stu-id="ed69c-132">Additional services</span></span>

<span data-ttu-id="ed69c-133">除了上面所述的基本服務以外，EF 也有許多其他服務，也就是一律或有時提供者特定的服務。</span><span class="sxs-lookup"><span data-stu-id="ed69c-133">In addition to the fundamental services described above there are also many other services used by EF which are either always or sometimes provider-specific.</span></span> <span data-ttu-id="ed69c-134">這些服務的預設提供者特定的執行，可由 Dbproviderservices.createdatabase 的實作為提供。</span><span class="sxs-lookup"><span data-stu-id="ed69c-134">Default provider-specific implementations of these services can be supplied by a DbProviderServices implementation.</span></span> <span data-ttu-id="ed69c-135">應用程式也可以覆寫這些服務的執行，或在 Dbproviderservices.createdatabase 類型未提供預設值時提供。</span><span class="sxs-lookup"><span data-stu-id="ed69c-135">Applications can also override the implementations of these services, or provide implementations when a DbProviderServices type does not provide a default.</span></span> <span data-ttu-id="ed69c-136">下面的_解決其他服務_一節會更詳細說明這一點。</span><span class="sxs-lookup"><span data-stu-id="ed69c-136">This is described in more detail in the _Resolving additional services_ section below.</span></span>

<span data-ttu-id="ed69c-137">提供者可能會對提供者感關注的其他服務類型如下所示。</span><span class="sxs-lookup"><span data-stu-id="ed69c-137">The additional service types that a provider may be of interest to a provider are listed below.</span></span> <span data-ttu-id="ed69c-138">如需這些服務類型的詳細資訊，請參閱 API 檔。</span><span class="sxs-lookup"><span data-stu-id="ed69c-138">More details about each of these service types can be found in the API documentation.</span></span>

### <a name="idbexecutionstrategy"></a><span data-ttu-id="ed69c-139">IDbExecutionStrategy</span><span class="sxs-lookup"><span data-stu-id="ed69c-139">IDbExecutionStrategy</span></span>

<span data-ttu-id="ed69c-140">這是選擇性的服務，可讓提供者在針對資料庫執行查詢和命令時，執行重試或其他行為。</span><span class="sxs-lookup"><span data-stu-id="ed69c-140">This is an optional service that allows a provider to implement retries or other behavior when queries and commands are executed against the database.</span></span> <span data-ttu-id="ed69c-141">如果未提供任何實作為，則 EF 只會執行命令，並傳播擲回的任何例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ed69c-141">If no implementation is provided, then EF will simply execute the commands and propagate any exceptions thrown.</span></span> <span data-ttu-id="ed69c-142">針對 SQL Server 此服務可用來提供重試原則，這在針對雲端式資料庫伺服器（例如 SQL Azure）執行時特別有用。</span><span class="sxs-lookup"><span data-stu-id="ed69c-142">For SQL Server this service is used to provide a retry policy which is especially useful when running against cloud-based database servers such as SQL Azure.</span></span>

### <a name="idbconnectionfactory"></a><span data-ttu-id="ed69c-143">IDbConnectionFactory</span><span class="sxs-lookup"><span data-stu-id="ed69c-143">IDbConnectionFactory</span></span>

<span data-ttu-id="ed69c-144">這是選擇性的服務，可讓提供者依照慣例建立 DbConnection 物件（僅提供資料庫名稱）。</span><span class="sxs-lookup"><span data-stu-id="ed69c-144">This is an optional service that allows a provider to create DbConnection objects by convention when given only a database name.</span></span> <span data-ttu-id="ed69c-145">請注意，雖然這項服務可以由 EF 4.1 所呈現的 Dbproviderservices.createdatabase 實作為解決，而且也可以在設定檔或程式碼中明確設定。</span><span class="sxs-lookup"><span data-stu-id="ed69c-145">Note that while this service can be resolved by a DbProviderServices implementation it has been present since EF 4.1 and can also be explicitly set in either the config file or in code.</span></span> <span data-ttu-id="ed69c-146">如果提供者註冊為預設提供者（請參閱下方_的預設提供者_），而且預設連接處理站尚未設定于其他位置，則此服務只會有機會解決這種情況。</span><span class="sxs-lookup"><span data-stu-id="ed69c-146">The provider will only get a chance to resolve this service if it registered as the default provider (see _The default provider_ below) and if a default connection factory has not been set elsewhere.</span></span>

### <a name="dbspatialservices"></a><span data-ttu-id="ed69c-147">DbSpatialServices</span><span class="sxs-lookup"><span data-stu-id="ed69c-147">DbSpatialServices</span></span>

<span data-ttu-id="ed69c-148">這是選擇性的服務，可讓提供者加入 geography 和 geometry 空間類型的支援。</span><span class="sxs-lookup"><span data-stu-id="ed69c-148">This is an optional services that allows a provider to add support for geography and geometry spatial types.</span></span> <span data-ttu-id="ed69c-149">必須提供此服務的執行，應用程式才能使用具有空間類型的 EF。</span><span class="sxs-lookup"><span data-stu-id="ed69c-149">An implementation of this service must be supplied in order for an application to use EF with spatial types.</span></span> <span data-ttu-id="ed69c-150">系統會以兩種方式要求 DbSptialServices。</span><span class="sxs-lookup"><span data-stu-id="ed69c-150">DbSptialServices is asked for in two ways.</span></span> <span data-ttu-id="ed69c-151">首先，會使用 DbProviderInfo 物件（其中包含不變的名稱和資訊清單 token）來要求提供者專屬的空間服務，做為索引鍵。</span><span class="sxs-lookup"><span data-stu-id="ed69c-151">First, provider-specific spatial services are requested using a DbProviderInfo object (which contains invariant name and manifest token) as key.</span></span> <span data-ttu-id="ed69c-152">第二，您可以要求 DbSpatialServices，而不需要金鑰。</span><span class="sxs-lookup"><span data-stu-id="ed69c-152">Second, DbSpatialServices can be asked for with no key.</span></span> <span data-ttu-id="ed69c-153">這是用來解析建立獨立 DbGeography 或 DbGeometry 類型時所使用的「全域空間提供者」。</span><span class="sxs-lookup"><span data-stu-id="ed69c-153">This is used to resolve the “global spatial provider” that is used when creating stand-alone DbGeography or DbGeometry types.</span></span>

### <a name="migrationsqlgenerator"></a><span data-ttu-id="ed69c-154">MigrationSqlGenerator</span><span class="sxs-lookup"><span data-stu-id="ed69c-154">MigrationSqlGenerator</span></span>

<span data-ttu-id="ed69c-155">這是選擇性的服務，可讓 EF 遷移用於建立和修改資料庫架構時所使用的 SQL 產生 Code First。</span><span class="sxs-lookup"><span data-stu-id="ed69c-155">This is an optional service that allows EF Migrations to be used for the generation of SQL used in creating and modifying database schemas by Code First.</span></span> <span data-ttu-id="ed69c-156">需要執行才能支援遷移。</span><span class="sxs-lookup"><span data-stu-id="ed69c-156">An implementation is required in order to support Migrations.</span></span> <span data-ttu-id="ed69c-157">如果已提供執行，則在使用資料庫初始化運算式或 Database. Create 方法建立資料庫時，也會使用它。</span><span class="sxs-lookup"><span data-stu-id="ed69c-157">If an implementation is provided then it will also be used when databases are created using database initializers or the Database.Create method.</span></span>

### <a name="funcdbconnection-string-historycontextfactory"></a><span data-ttu-id="ed69c-158">Func < DbConnection、string、HistoryCoNtextFactory ></span><span class="sxs-lookup"><span data-stu-id="ed69c-158">Func<DbConnection, string, HistoryContextFactory></span></span>

<span data-ttu-id="ed69c-159">這是選擇性的服務，可讓提供者設定 HistoryCoNtext 與 EF 遷移所使用之 `__MigrationHistory` 資料表的對應。</span><span class="sxs-lookup"><span data-stu-id="ed69c-159">This is an optional service that allows a provider to configure the mapping of the HistoryContext to the `__MigrationHistory` table used by EF Migrations.</span></span> <span data-ttu-id="ed69c-160">HistoryCoNtext 是 Code First 的 DbCoNtext，而且可以使用正常的 Fluent API 來設定，以變更資料表名稱和資料行對應規格之類的專案。</span><span class="sxs-lookup"><span data-stu-id="ed69c-160">The HistoryContext is a Code First DbContext and can be configured using the normal fluent API to change things like the name of the table and the column mapping specifications.</span></span> <span data-ttu-id="ed69c-161">如果該提供者支援所有預設資料表和資料行對應，則 EF 針對所有提供者所傳回之此服務的預設執行可能會適用于指定的資料庫伺服器。</span><span class="sxs-lookup"><span data-stu-id="ed69c-161">The default implementation of this service returned by EF for all providers may work for a given database server if all the default table and column mappings are supported by that provider.</span></span> <span data-ttu-id="ed69c-162">在這種情況下，提供者不需要提供此服務的執行。</span><span class="sxs-lookup"><span data-stu-id="ed69c-162">In such a case the provider does not need to supply an implementation of this service.</span></span>

### <a name="idbproviderfactoryresolver"></a><span data-ttu-id="ed69c-163">IDbProviderFactoryResolver</span><span class="sxs-lookup"><span data-stu-id="ed69c-163">IDbProviderFactoryResolver</span></span>

<span data-ttu-id="ed69c-164">這是選擇性的服務，可從指定的 DbConnection 物件取得正確的 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="ed69c-164">This is an optional service for obtaining the correct DbProviderFactory from a given DbConnection object.</span></span> <span data-ttu-id="ed69c-165">所有提供者的 EF 所傳回的此服務的預設執行，都適用于所有提供者。</span><span class="sxs-lookup"><span data-stu-id="ed69c-165">The default implementation of this service returned by EF for all providers is intended to work for all providers.</span></span> <span data-ttu-id="ed69c-166">不過，在 .NET 4 上執行時，如果 DbProviderFactory 的 DbConnections，則無法從一個公開存取。</span><span class="sxs-lookup"><span data-stu-id="ed69c-166">However, when running on .NET 4, the DbProviderFactory is not publicly accessible from one if its DbConnections.</span></span> <span data-ttu-id="ed69c-167">因此，EF 會使用一些啟發學習法來搜尋已註冊的提供者，以尋找相符的。</span><span class="sxs-lookup"><span data-stu-id="ed69c-167">Therefore, EF uses some heuristics to search the registered providers to find a match.</span></span> <span data-ttu-id="ed69c-168">某些提供者可能會因為這些啟發學習法而失敗，而在這種情況下，提供者應該提供新的執行。</span><span class="sxs-lookup"><span data-stu-id="ed69c-168">It is possible that for some providers these heuristics will fail and in such situations the provider should supply a new implementation.</span></span>

## <a name="registering-dbproviderservices"></a><span data-ttu-id="ed69c-169">註冊 Dbproviderservices.createdatabase</span><span class="sxs-lookup"><span data-stu-id="ed69c-169">Registering DbProviderServices</span></span>

<span data-ttu-id="ed69c-170">要使用的 Dbproviderservices.createdatabase 執行方式可以在應用程式的設定檔（app.config 或 web.config）中註冊，或使用以程式碼為基礎的設定。</span><span class="sxs-lookup"><span data-stu-id="ed69c-170">The DbProviderServices implementation to use can be registered either in the application’s configuration file (app.config or web.config) or using code-based configuration.</span></span> <span data-ttu-id="ed69c-171">在任一情況下，註冊都會使用提供者的「不變名稱」做為索引鍵。</span><span class="sxs-lookup"><span data-stu-id="ed69c-171">In either case the registration uses the provider’s “invariant name” as a key.</span></span> <span data-ttu-id="ed69c-172">這可讓您在單一應用程式中註冊和使用多個提供者。</span><span class="sxs-lookup"><span data-stu-id="ed69c-172">This allows multiple providers to be registered and used in a single application.</span></span> <span data-ttu-id="ed69c-173">用於 EF 註冊的不變名稱與用於 ADO.NET 提供者註冊和連接字串的非變異名稱相同。</span><span class="sxs-lookup"><span data-stu-id="ed69c-173">The invariant name used for EF registrations is the same as the invariant name used for ADO.NET provider registration and connection strings.</span></span> <span data-ttu-id="ed69c-174">例如，針對 SQL Server 會使用不變的名稱 "SqlClient"。</span><span class="sxs-lookup"><span data-stu-id="ed69c-174">For example, for SQL Server the invariant name “System.Data.SqlClient” is used.</span></span>

### <a name="config-file-registration"></a><span data-ttu-id="ed69c-175">組態檔註冊</span><span class="sxs-lookup"><span data-stu-id="ed69c-175">Config file registration</span></span>

<span data-ttu-id="ed69c-176">要使用的 Dbproviderservices.createdatabase 類型會在應用程式佈建檔之 entityFramework 區段的提供者清單中註冊為提供者元素。</span><span class="sxs-lookup"><span data-stu-id="ed69c-176">The DbProviderServices type to use is registered as a provider element in the providers list of the entityFramework section of the application’s config file.</span></span> <span data-ttu-id="ed69c-177">例如：</span><span class="sxs-lookup"><span data-stu-id="ed69c-177">For example:</span></span>

``` xml
<entityFramework>
  <providers>
    <provider invariantName="My.Invariant.Name" type="MyProvider.MyProviderServices, MyAssembly" />
  </providers>
</entityFramework>
```

<span data-ttu-id="ed69c-178">_類型_字串必須是要使用之 dbproviderservices.createdatabase 實作為元件限定的類型名稱。</span><span class="sxs-lookup"><span data-stu-id="ed69c-178">The _type_ string must be the assembly-qualified type name of the DbProviderServices implementation to use.</span></span>

### <a name="code-based-registration"></a><span data-ttu-id="ed69c-179">程式碼架構註冊</span><span class="sxs-lookup"><span data-stu-id="ed69c-179">Code-based registration</span></span>

<span data-ttu-id="ed69c-180">從開始，您也可以使用程式碼來註冊 EF6 提供者。</span><span class="sxs-lookup"><span data-stu-id="ed69c-180">Starting with EF6 providers can also be registered using code.</span></span> <span data-ttu-id="ed69c-181">這可讓 EF 提供者使用，而不需要變更應用程式的設定檔。</span><span class="sxs-lookup"><span data-stu-id="ed69c-181">This allows an EF provider to be used without any change to the application’s configuration file.</span></span> <span data-ttu-id="ed69c-182">若要使用以程式碼為基礎的設定，應用程式應該建立 DbConfiguration 類別，如以[程式碼為基礎](https://msdn.com/data/jj680699)的設定檔中所述。</span><span class="sxs-lookup"><span data-stu-id="ed69c-182">To use code-based configuration an application should create a DbConfiguration class as described in the [code-based configuration documentation](https://msdn.com/data/jj680699).</span></span> <span data-ttu-id="ed69c-183">然後，DbConfiguration 類別的函式應該呼叫 SetProviderServices 來註冊 EF 提供者。</span><span class="sxs-lookup"><span data-stu-id="ed69c-183">The constructor of the DbConfiguration class should then call SetProviderServices to register the EF provider.</span></span> <span data-ttu-id="ed69c-184">例如：</span><span class="sxs-lookup"><span data-stu-id="ed69c-184">For example:</span></span>

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetProviderServices("My.New.Provider", new MyProviderServices());
    }
}
```

## <a name="resolving-additional-services"></a><span data-ttu-id="ed69c-185">解析其他服務</span><span class="sxs-lookup"><span data-stu-id="ed69c-185">Resolving additional services</span></span>

<span data-ttu-id="ed69c-186">如先前在_提供者類型總覽_一節中所述，dbproviderservices.createdatabase 類別也可以用來解析其他服務。</span><span class="sxs-lookup"><span data-stu-id="ed69c-186">As mentioned above in the _Provider types overview_ section, a DbProviderServices class can also be used to resolve additional services.</span></span> <span data-ttu-id="ed69c-187">這是可行的，因為 Dbproviderservices.createdatabase 會執行 IDbDependencyResolver，而每個註冊的 Dbproviderservices.createdatabase 類型都會新增為「預設解析程式」。</span><span class="sxs-lookup"><span data-stu-id="ed69c-187">This is possible because DbProviderServices implements IDbDependencyResolver and each registered DbProviderServices type is added as a “default resolver”.</span></span> <span data-ttu-id="ed69c-188">相依性[解析](~/ef6/fundamentals/configuring/dependency-resolution.md)中會更詳細地說明 IDbDpendencyResolver 機制。</span><span class="sxs-lookup"><span data-stu-id="ed69c-188">The IDbDpendencyResolver mechanism is described in more detail in [Dependency Resolution](~/ef6/fundamentals/configuring/dependency-resolution.md).</span></span> <span data-ttu-id="ed69c-189">不過，不需要瞭解此規格中的所有概念，即可解析提供者中的其他服務。</span><span class="sxs-lookup"><span data-stu-id="ed69c-189">However, it is not necessary to understand all the concepts in this specification to resolve additional services in a provider.</span></span>

<span data-ttu-id="ed69c-190">提供者解決其他服務最常見的方式，是在 Dbproviderservices.createdatabase 類別的函式中呼叫每個服務的 Dbproviderservices.createdatabase AddDependencyResolver。</span><span class="sxs-lookup"><span data-stu-id="ed69c-190">The most common way for a provider to resolve additional services is to call DbProviderServices.AddDependencyResolver for each service in the constructor of the DbProviderServices class.</span></span> <span data-ttu-id="ed69c-191">例如，SqlProviderServices （適用于 SQL Server 的 EF 提供者）的程式碼類似于初始化：</span><span class="sxs-lookup"><span data-stu-id="ed69c-191">For example, SqlProviderServices (the EF provider for SQL Server) has code similar to this for initialization:</span></span>

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

<span data-ttu-id="ed69c-192">此函式會使用下列 helper 類別：</span><span class="sxs-lookup"><span data-stu-id="ed69c-192">This constructor uses the following helper classes:</span></span>

*   <span data-ttu-id="ed69c-193">SingletonDependencyResolver：提供簡單的方法來解析單一服務，也就是每次呼叫 GetService 時，都會傳回相同實例的服務。</span><span class="sxs-lookup"><span data-stu-id="ed69c-193">SingletonDependencyResolver: provides a simple way to resolve Singleton services—that is, services for which the same instance is returned each time that GetService is called.</span></span> <span data-ttu-id="ed69c-194">暫時性服務通常會註冊為單一處理站，以便在需要時用來建立暫時性實例。</span><span class="sxs-lookup"><span data-stu-id="ed69c-194">Transient services are often registered as a singleton factory that will be used to create transient instances on demand.</span></span>
*   <span data-ttu-id="ed69c-195">ExecutionStrategyResolver：傳回 IExecutionStrategy 實作為特定的解決器。</span><span class="sxs-lookup"><span data-stu-id="ed69c-195">ExecutionStrategyResolver: a resolver specific to returning IExecutionStrategy implementations.</span></span>

<span data-ttu-id="ed69c-196">不使用 Dbproviderservices.createdatabase. AddDependencyResolver，您也可以覆寫 Dbproviderservices.createdatabase GetService 並直接解析其他服務。</span><span class="sxs-lookup"><span data-stu-id="ed69c-196">Instead of using DbProviderServices.AddDependencyResolver it is also possible to override DbProviderServices.GetService and resolve additional services directly.</span></span> <span data-ttu-id="ed69c-197">當 EF 需要由特定類型所定義的服務，以及在某些情況下，針對指定的索引鍵，就會呼叫這個方法。</span><span class="sxs-lookup"><span data-stu-id="ed69c-197">This method will be called when EF needs a service defined by a certain type and, in some cases, for a given key.</span></span> <span data-ttu-id="ed69c-198">方法應該會傳回服務（如果可以的話），或傳回 null 以選擇不要傳回服務，而是允許另一個類別加以解析。</span><span class="sxs-lookup"><span data-stu-id="ed69c-198">The method should return the service if it can, or return null to opt-out of returning the service and instead allow another class to resolve it.</span></span> <span data-ttu-id="ed69c-199">例如，若要解決預設的連接處理站，GetService 中的程式碼可能看起來像這樣：</span><span class="sxs-lookup"><span data-stu-id="ed69c-199">For example, to resolve the default connection factory the code in GetService might look something like this:</span></span>

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

### <a name="registration-order"></a><span data-ttu-id="ed69c-200">註冊順序</span><span class="sxs-lookup"><span data-stu-id="ed69c-200">Registration order</span></span>

<span data-ttu-id="ed69c-201">在應用程式的設定檔中註冊多個 Dbproviderservices.createdatabase 的實作為時，系統會依照列出的順序將它們新增為次要解析程式。</span><span class="sxs-lookup"><span data-stu-id="ed69c-201">When multiple DbProviderServices implementations are registered in an application’s config file they will be added as secondary resolvers in the order that they are listed.</span></span> <span data-ttu-id="ed69c-202">由於解析程式一律會新增至次要解析程式鏈的頂端，這表示清單結尾的提供者將有機會先解析相依性，然後再進行其他處理。</span><span class="sxs-lookup"><span data-stu-id="ed69c-202">Since resolvers are always added to the top of the secondary resolver chain this means that the provider at the end of the list will get a chance to resolve dependencies before the others.</span></span> <span data-ttu-id="ed69c-203">（這在一開始看起來就有點直覺，但如果您認為每個提供者都不在清單中，並將它堆疊在現有的提供者之上，這就是合理的作法）。</span><span class="sxs-lookup"><span data-stu-id="ed69c-203">(This can seem a little counter-intuitive at first, but it makes sense if you imagine taking each provider out of the list and stacking it on top of the existing providers.)</span></span>

<span data-ttu-id="ed69c-204">此順序通常並不重要，因為大部分的提供者服務都是提供者特有的，而且是由提供者不變名稱所</span><span class="sxs-lookup"><span data-stu-id="ed69c-204">This ordering usually doesn’t matter because most provider services are provider-specific and keyed by provider invariant name.</span></span> <span data-ttu-id="ed69c-205">不過，針對不是由提供者非變異名稱或其他提供者特定索引鍵進行索引的服務，服務將會根據此順序解析。</span><span class="sxs-lookup"><span data-stu-id="ed69c-205">However, for services that are not keyed by provider invariant name or some other provider-specific key the service will be resolved based on this ordering.</span></span> <span data-ttu-id="ed69c-206">例如，如果未在其他地方明確設定，則預設連線 factory 會來自鏈中的最上層提供者。</span><span class="sxs-lookup"><span data-stu-id="ed69c-206">For example, if it is not explicitly set differently somewhere else, then the default connection factory will come from the topmost provider in the chain.</span></span>

## <a name="additional-config-file-registrations"></a><span data-ttu-id="ed69c-207">其他設定檔案註冊</span><span class="sxs-lookup"><span data-stu-id="ed69c-207">Additional config file registrations</span></span>

<span data-ttu-id="ed69c-208">您可以明確地在應用程式的設定檔中直接註冊上述一些額外的提供者服務。</span><span class="sxs-lookup"><span data-stu-id="ed69c-208">It is possible to explicitly register some of the additional provider services described above directly in an application’s config file.</span></span> <span data-ttu-id="ed69c-209">完成這項作業時，將會使用設定檔中的註冊，而不是 Dbproviderservices.createdatabase 實 GetService 方法所傳回的任何專案。</span><span class="sxs-lookup"><span data-stu-id="ed69c-209">When this is done the registration in the config file will be used instead of anything returned by the GetService method of the DbProviderServices implementation.</span></span>

### <a name="registering-the-default-connection-factory"></a><span data-ttu-id="ed69c-210">註冊預設連接 factory</span><span class="sxs-lookup"><span data-stu-id="ed69c-210">Registering the default connection factory</span></span>

<span data-ttu-id="ed69c-211">從 EF5 開始，EntityFramework NuGet 套件會自動在設定檔案中註冊 SQL Express 連線處理站或 LocalDb 連接 factory。</span><span class="sxs-lookup"><span data-stu-id="ed69c-211">Starting with EF5 the EntityFramework NuGet package automatically registered either the SQL Express connection factory or the LocalDb connection factory in the config file.</span></span>

<span data-ttu-id="ed69c-212">例如：</span><span class="sxs-lookup"><span data-stu-id="ed69c-212">For example:</span></span>

``` xml
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework" >
</entityFramework>
```

<span data-ttu-id="ed69c-213">此_類型_是預設連接 factory 的元件限定類型名稱，必須實 IDbConnectionFactory。</span><span class="sxs-lookup"><span data-stu-id="ed69c-213">The _type_ is the assembly-qualified type name for the default connection factory, which must implement IDbConnectionFactory.</span></span>

<span data-ttu-id="ed69c-214">建議提供者 NuGet 套件在安裝時以這種方式設定預設的連接 factory。</span><span class="sxs-lookup"><span data-stu-id="ed69c-214">It is recommended that a provider NuGet package set the default connection factory in this way when installed.</span></span> <span data-ttu-id="ed69c-215">請參閱下列_提供者的 NuGet 套件_。</span><span class="sxs-lookup"><span data-stu-id="ed69c-215">See _NuGet Packages for providers_ below.</span></span>

## <a name="additional-ef6-provider-changes"></a><span data-ttu-id="ed69c-216">其他 EF6 提供者變更</span><span class="sxs-lookup"><span data-stu-id="ed69c-216">Additional EF6 provider changes</span></span>

### <a name="spatial-provider-changes"></a><span data-ttu-id="ed69c-217">空間提供者變更</span><span class="sxs-lookup"><span data-stu-id="ed69c-217">Spatial provider changes</span></span>

<span data-ttu-id="ed69c-218">支援空間類型的提供者現在必須在衍生自 DbSpatialDataReader 的類別上，執行一些其他方法：</span><span class="sxs-lookup"><span data-stu-id="ed69c-218">Providers that support spatial types must now implement some additional methods on classes deriving from DbSpatialDataReader:</span></span>

*   `public abstract bool IsGeographyColumn(int ordinal)`
*   `public abstract bool IsGeometryColumn(int ordinal)`

<span data-ttu-id="ed69c-219">現有方法也有新的非同步版本，建議您將其覆寫為同步方法的預設實作為委派，因此不會以非同步方式執行：</span><span class="sxs-lookup"><span data-stu-id="ed69c-219">There are also new asynchronous versions of existing methods that are recommended to be overridden as the default implementations delegate to the synchronous methods and therefore do not execute asynchronously:</span></span>

*   `public virtual Task<DbGeography> GetGeographyAsync(int ordinal, CancellationToken cancellationToken)`
*   `public virtual Task<DbGeometry> GetGeometryAsync(int ordinal, CancellationToken cancellationToken)`

### <a name="native-support-for-enumerablecontains"></a><span data-ttu-id="ed69c-220">可列舉的原生支援。 Contains</span><span class="sxs-lookup"><span data-stu-id="ed69c-220">Native support for Enumerable.Contains</span></span>

<span data-ttu-id="ed69c-221">EF6 引進了新的運算式類型 DbInExpression，其已加入來解決使用可列舉之時的效能問題。包含在 LINQ 查詢中。</span><span class="sxs-lookup"><span data-stu-id="ed69c-221">EF6 introduces a new expression type, DbInExpression, which was added to address performance issues around use of Enumerable.Contains in LINQ queries.</span></span> <span data-ttu-id="ed69c-222">DbProviderManifest 類別具有新的虛擬方法 SupportsInExpression，由 EF 呼叫以判斷提供者是否處理新的運算式類型。</span><span class="sxs-lookup"><span data-stu-id="ed69c-222">The DbProviderManifest class has a new virtual method, SupportsInExpression, which is called by EF to determine if a provider handles the new expression type.</span></span> <span data-ttu-id="ed69c-223">為了與現有的提供者實現相容，此方法會傳回 false。</span><span class="sxs-lookup"><span data-stu-id="ed69c-223">For compatibility with existing provider implementations the method returns false.</span></span> <span data-ttu-id="ed69c-224">為了因應這項改進，EF6 提供者可以加入程式碼來處理 DbInExpression，並覆寫 SupportsInExpression 以傳回 true。</span><span class="sxs-lookup"><span data-stu-id="ed69c-224">To benefit from this improvement, an EF6 provider can add code to handle DbInExpression and override SupportsInExpression to return true.</span></span> <span data-ttu-id="ed69c-225">您可以藉由呼叫 DbExpressionBuilder.In 方法來建立 DbInExpression 的實例。</span><span class="sxs-lookup"><span data-stu-id="ed69c-225">An instance of DbInExpression can be created by calling the DbExpressionBuilder.In method.</span></span> <span data-ttu-id="ed69c-226">DbInExpression 實例是由 DbExpression 所組成，通常代表資料表資料行，而 DbConstantExpression 清單則是用來測試是否符合。</span><span class="sxs-lookup"><span data-stu-id="ed69c-226">A DbInExpression instance is composed of a DbExpression, usually representing a table column, and a list of DbConstantExpression to test for a match.</span></span>

## <a name="nuget-packages-for-providers"></a><span data-ttu-id="ed69c-227">提供者的 NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="ed69c-227">NuGet packages for providers</span></span>

<span data-ttu-id="ed69c-228">提供 EF6 提供者的其中一種方式是將它發行為 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="ed69c-228">One way to make an EF6 provider available is to release it as a NuGet package.</span></span> <span data-ttu-id="ed69c-229">使用 NuGet 套件有下列優點：</span><span class="sxs-lookup"><span data-stu-id="ed69c-229">Using a NuGet package has the following advantages:</span></span>

*   <span data-ttu-id="ed69c-230">使用 NuGet 將提供者註冊新增至應用程式的設定檔，是很容易的事</span><span class="sxs-lookup"><span data-stu-id="ed69c-230">It is easy to use NuGet to add the provider registration to the application’s config file</span></span>
*   <span data-ttu-id="ed69c-231">您可以對設定檔進行其他變更以設定預設連線處理站，讓慣例所建立的連線使用已註冊的提供者</span><span class="sxs-lookup"><span data-stu-id="ed69c-231">Additional changes can be made to the config file to set the default connection factory so that connections made by convention will use the registered provider</span></span>
*   <span data-ttu-id="ed69c-232">NuGet 會處理新增系結重新導向，如此一來，即使在發行新的 EF 封裝之後，EF6 提供者仍應繼續工作</span><span class="sxs-lookup"><span data-stu-id="ed69c-232">NuGet handles adding binding redirects so that the EF6 provider should continue to work even after a new EF package is released</span></span>

<span data-ttu-id="ed69c-233">其中一個範例是包含在[開放原始碼程式碼基](https://github.com/aspnet/entityframework6)底中的 EntityFramework entityframework.sqlservercompact 套件。</span><span class="sxs-lookup"><span data-stu-id="ed69c-233">An example of this is the EntityFramework.SqlServerCompact package which is included in the [open source codebase](https://github.com/aspnet/entityframework6).</span></span> <span data-ttu-id="ed69c-234">此套件提供建立 EF 提供者 NuGet 套件的絕佳範本。</span><span class="sxs-lookup"><span data-stu-id="ed69c-234">This package provides a good template for creating EF provider NuGet packages.</span></span>

### <a name="powershell-commands"></a><span data-ttu-id="ed69c-235">PowerShell 命令</span><span class="sxs-lookup"><span data-stu-id="ed69c-235">PowerShell commands</span></span>

<span data-ttu-id="ed69c-236">安裝 EntityFramework NuGet 套件時，它會註冊 PowerShell 模組，其中包含兩個對提供者套件非常有用的命令：</span><span class="sxs-lookup"><span data-stu-id="ed69c-236">When the EntityFramework NuGet package is installed it registers a PowerShell module that contains two commands that are very useful for provider packages:</span></span>

*   <span data-ttu-id="ed69c-237">EFProvider 會在目標專案的設定檔中加入提供者的新實體，並確定它位於已註冊之提供者清單的結尾。</span><span class="sxs-lookup"><span data-stu-id="ed69c-237">Add-EFProvider adds a new entity for the provider in the target project’s configuration file and makes sure it is at the end of the list of registered providers.</span></span>
*   <span data-ttu-id="ed69c-238">EFDefaultConnectionFactory 會在目標專案的設定檔中加入或更新 defaultConnectionFactory 註冊。</span><span class="sxs-lookup"><span data-stu-id="ed69c-238">Add-EFDefaultConnectionFactory either adds or updates the defaultConnectionFactory registration in the target project’s configuration file.</span></span>

<span data-ttu-id="ed69c-239">這兩個命令會負責將 entityFramework 區段新增至設定檔，並在必要時新增提供者集合。</span><span class="sxs-lookup"><span data-stu-id="ed69c-239">Both these commands take care of adding an entityFramework section to the config file and adding a providers collection if necessary.</span></span>

<span data-ttu-id="ed69c-240">其目的是要從 install NuGet 腳本呼叫這些命令。</span><span class="sxs-lookup"><span data-stu-id="ed69c-240">It is intended that these commands be called from the install.ps1 NuGet script.</span></span> <span data-ttu-id="ed69c-241">例如，針對 SQL Compact 提供者安裝. ps1 看起來像這樣：</span><span class="sxs-lookup"><span data-stu-id="ed69c-241">For example, install.ps1 for the SQL Compact provider looks similar to this:</span></span>

``` powershell
param($installPath, $toolsPath, $package, $project)
Add-EFDefaultConnectionFactory $project 'System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework' -ConstructorArguments 'System.Data.SqlServerCe.4.0'
Add-EFProvider $project 'System.Data.SqlServerCe.4.0' 'System.Data.Entity.SqlServerCompact.SqlCeProviderServices, EntityFramework.SqlServerCompact'</pre>
```

<span data-ttu-id="ed69c-242">您可以使用 [套件管理員主控台] 視窗中的 get-help 來取得有關這些命令的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="ed69c-242">More information about these commands can be obtained by using get-help in the Package Manager Console window.</span></span>

## <a name="wrapping-providers"></a><span data-ttu-id="ed69c-243">包裝提供者</span><span class="sxs-lookup"><span data-stu-id="ed69c-243">Wrapping providers</span></span>

<span data-ttu-id="ed69c-244">包裝提供者是一個 EF 和/或 ADO.NET 提供者，它會包裝現有的提供者，以利用其他功能（例如分析或追蹤功能）來擴充它。</span><span class="sxs-lookup"><span data-stu-id="ed69c-244">A wrapping provider is an EF and/or ADO.NET provider that wraps an existing provider to extend it with other functionality such as profiling or tracing capabilities.</span></span> <span data-ttu-id="ed69c-245">包裝提供者可以用一般方式註冊，但是在執行時間設定包裝提供者通常會藉由攔截提供者相關服務的解析來方便。</span><span class="sxs-lookup"><span data-stu-id="ed69c-245">Wrapping providers can be registered in the normal way, but it is often more convenient to setup the wrapping provider at runtime by intercepting the resolution of provider-related services.</span></span> <span data-ttu-id="ed69c-246">DbConfiguration 類別上的靜態事件 OnLockingConfiguration 可以用來執行此動作。</span><span class="sxs-lookup"><span data-stu-id="ed69c-246">The static event OnLockingConfiguration on the DbConfiguration class can be used to do this.</span></span>

<span data-ttu-id="ed69c-247">當 EF 判斷出應用程式域的所有 EF 設定會從何處取得，但在鎖定以供使用之前，會呼叫 OnLockingConfiguration。</span><span class="sxs-lookup"><span data-stu-id="ed69c-247">OnLockingConfiguration is called after EF has determined where all EF configuration for the app domain will be obtained from but before it is locked for use.</span></span> <span data-ttu-id="ed69c-248">在應用程式啟動時（使用 EF 之前），應用程式應該註冊此事件的事件處理常式。</span><span class="sxs-lookup"><span data-stu-id="ed69c-248">At app startup (before EF is used) the app should register an event handler for this event.</span></span> <span data-ttu-id="ed69c-249">（我們正在考慮新增在設定檔案中註冊此處理程式的支援，但尚未支援）。接著，事件處理常式應該對每個需要包裝的服務呼叫 ReplaceService。</span><span class="sxs-lookup"><span data-stu-id="ed69c-249">(We are considering adding support for registering this handler in the config file but this is not yet supported.) The event handler should then make a call to ReplaceService for every service that needs to be wrapped.</span></span>  

<span data-ttu-id="ed69c-250">例如，若要包裝 IDbConnectionFactory 和 DbProviderService，應該註冊處理常式，如下所示：</span><span class="sxs-lookup"><span data-stu-id="ed69c-250">For example, to wrap IDbConnectionFactory and DbProviderService, a handler something like this should be registered:</span></span>

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

<span data-ttu-id="ed69c-251">已解析的服務，現在應該與用來解析服務的金鑰一起包裝在一起，會傳遞至處理常式。</span><span class="sxs-lookup"><span data-stu-id="ed69c-251">The service that has been resolved and should now be wrapped together with the key that was used to resolve the service are passed to the handler.</span></span> <span data-ttu-id="ed69c-252">然後，處理常式可以包裝此服務，並以包裝的版本取代傳回的服務。</span><span class="sxs-lookup"><span data-stu-id="ed69c-252">The handler can then wrap this service and replace the returned service with the wrapped version.</span></span>

## <a name="resolving-a-dbproviderfactory-with-ef"></a><span data-ttu-id="ed69c-253">使用 EF 解析 DbProviderFactory</span><span class="sxs-lookup"><span data-stu-id="ed69c-253">Resolving a DbProviderFactory with EF</span></span>

<span data-ttu-id="ed69c-254">DbProviderFactory 是 EF 所需的其中一個基本提供者類型，如上述_提供者類型總覽_一節中所述。</span><span class="sxs-lookup"><span data-stu-id="ed69c-254">DbProviderFactory is one of the fundamental provider types needed by EF as described in the _Provider types overview_ section above.</span></span> <span data-ttu-id="ed69c-255">如先前所述，它不是 EF 型別，而且註冊通常不是 EF 設定的一部分，而是在 machine.config 檔案和/或應用程式的設定檔中註冊一般 ADO.NET 提供者。</span><span class="sxs-lookup"><span data-stu-id="ed69c-255">As already mentioned, It is not an EF type and registration is usually not part of EF configuration, but is instead the normal ADO.NET provider registration in the machine.config file and/or application’s config file.</span></span>

<span data-ttu-id="ed69c-256">儘管此 EF 在尋找要使用的 DbProviderFactory 時仍會使用其一般相依性解析機制。</span><span class="sxs-lookup"><span data-stu-id="ed69c-256">Despite this EF still uses its normal dependency resolution mechanism when looking for a DbProviderFactory to use.</span></span> <span data-ttu-id="ed69c-257">預設解析程式會使用設定檔中的一般 ADO.NET 註冊，因此這通常是透明的。</span><span class="sxs-lookup"><span data-stu-id="ed69c-257">The default resolver uses the normal ADO.NET registration in the config files and so this is usually transparent.</span></span> <span data-ttu-id="ed69c-258">但由於使用一般的相依性解析機制，因此即使尚未完成一般的 ADO.NET 註冊，也可以使用 IDbDependencyResolver 來解析 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="ed69c-258">But because of the normal dependency resolution mechanism is used it means that an IDbDependencyResolver can be used to resolve a DbProviderFactory even when normal ADO.NET registration has not been done.</span></span>

<span data-ttu-id="ed69c-259">以這種方式解析 DbProviderFactory 有幾個含意：</span><span class="sxs-lookup"><span data-stu-id="ed69c-259">Resolving DbProviderFactory in this way has several implications:</span></span>

*   <span data-ttu-id="ed69c-260">使用以程式碼為基礎之設定的應用程式可以在其 DbConfiguration 類別中新增呼叫，以註冊適當的 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="ed69c-260">An application using code-based configuration can add calls in their DbConfiguration class to register the appropriate DbProviderFactory.</span></span> <span data-ttu-id="ed69c-261">這特別適用于不想要（或無法）完全使用任何檔案型設定的應用程式。</span><span class="sxs-lookup"><span data-stu-id="ed69c-261">This is especially useful for applications that do not want to (or cannot) make use of any file-based configuration at all.</span></span>
*   <span data-ttu-id="ed69c-262">您可以使用 ReplaceService 來包裝或取代此服務，如上面的_包裝提供者_一節所述</span><span class="sxs-lookup"><span data-stu-id="ed69c-262">The service can be wrapped or replaced using ReplaceService as described in the _Wrapping providers_ section above</span></span>
*   <span data-ttu-id="ed69c-263">理論上，Dbproviderservices.createdatabase 的執行方式可以解析 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="ed69c-263">Theoretically, a DbProviderServices implementation could resolve a DbProviderFactory.</span></span>

<span data-ttu-id="ed69c-264">要注意的是，這些事項的重點在於，它們只會影響 EF 的 DbProviderFactory 查閱。</span><span class="sxs-lookup"><span data-stu-id="ed69c-264">The important point to note about doing any of these things is that they will only affect the lookup of DbProviderFactory by EF.</span></span> <span data-ttu-id="ed69c-265">其他非 EF 程式碼仍可能預期以正常方式註冊 ADO.NET 提供者，如果找不到註冊，可能會失敗。</span><span class="sxs-lookup"><span data-stu-id="ed69c-265">Other non-EF code may still expect the ADO.NET provider to be registered in the normal way and may fail if the registration is not found.</span></span> <span data-ttu-id="ed69c-266">基於這個理由，通常會更適合以一般 ADO.NET 方式來註冊 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="ed69c-266">For this reason it is normally better for a DbProviderFactory to be registered in the normal ADO.NET way.</span></span>

### <a name="related-services"></a><span data-ttu-id="ed69c-267">相關服務</span><span class="sxs-lookup"><span data-stu-id="ed69c-267">Related services</span></span>

<span data-ttu-id="ed69c-268">如果使用 EF 來解析 DbProviderFactory，則也應該解析 IProviderInvariantName 和 IDbProviderFactoryResolver 服務。</span><span class="sxs-lookup"><span data-stu-id="ed69c-268">If EF is used to resolve a DbProviderFactory, then it should also resolve the IProviderInvariantName and IDbProviderFactoryResolver services.</span></span>

<span data-ttu-id="ed69c-269">IProviderInvariantName 是一項服務，用來判斷特定 DbProviderFactory 類型的提供者不變名稱。</span><span class="sxs-lookup"><span data-stu-id="ed69c-269">IProviderInvariantName is a service that is used to determine a provider invariant name for a given type of DbProviderFactory.</span></span> <span data-ttu-id="ed69c-270">此服務的預設執行會使用 ADO.NET 提供者註冊。</span><span class="sxs-lookup"><span data-stu-id="ed69c-270">The default implementation of this service uses the ADO.NET provider registration.</span></span> <span data-ttu-id="ed69c-271">這表示，如果 ADO.NET 提供者未以一般方式註冊，因為 DbProviderFactory 正由 EF 解決，則也需要解析此服務。</span><span class="sxs-lookup"><span data-stu-id="ed69c-271">This means that if the ADO.NET provider is not registered in the normal way because DbProviderFactory is being resolved by EF, then it will also be necessary to resolve this service.</span></span> <span data-ttu-id="ed69c-272">請注意，使用 DbConfiguration. SetProviderFactory 方法時，會自動加入此服務的解析程式。</span><span class="sxs-lookup"><span data-stu-id="ed69c-272">Note that a resolver for this service is automatically added when using the DbConfiguration.SetProviderFactory method.</span></span>

<span data-ttu-id="ed69c-273">如上面的「_提供者類型總覽_」一節所述，IDbProviderFactoryResolver 是用來從指定的 DbConnection 物件取得正確的 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="ed69c-273">As described in the _Provider types overview_ section above, the IDbProviderFactoryResolver is used to obtain the correct DbProviderFactory from a given DbConnection object.</span></span> <span data-ttu-id="ed69c-274">在 .NET 4 上執行時，此服務的預設值會使用 ADO.NET 提供者註冊。</span><span class="sxs-lookup"><span data-stu-id="ed69c-274">The default implementation of this service when running on .NET 4 uses the ADO.NET provider registration.</span></span> <span data-ttu-id="ed69c-275">這表示，如果 ADO.NET 提供者未以一般方式註冊，因為 DbProviderFactory 正由 EF 解決，則也需要解析此服務。</span><span class="sxs-lookup"><span data-stu-id="ed69c-275">This means that if the ADO.NET provider is not registered in the normal way because DbProviderFactory is being resolved by EF, then it will also be necessary to resolve this service.</span></span>
