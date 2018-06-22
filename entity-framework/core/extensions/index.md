---
title: 工具和延伸模組 - EF Core
author: ErikEJ
ms.author: divega
ms.date: 7/3/2018
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
ms.technology: entity-framework-core
uid: core/extensions/index
ms.openlocfilehash: 6c8cb3e0d8552f274118e4020b7e2e8009af7e11
ms.sourcegitcommit: fc68321c211aca38f7b9dc3a75677c6ca1b2524b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2018
ms.locfileid: "29769435"
---
# <a name="ef-core-tools--extensions"></a><span data-ttu-id="67568-102">EF Core 工具和延伸模組</span><span class="sxs-lookup"><span data-stu-id="67568-102">EF Core Tools & Extensions</span></span>

<span data-ttu-id="67568-103">工具和延伸模組提供 Entity Framework Core 的額外功能。</span><span class="sxs-lookup"><span data-stu-id="67568-103">Tools and extensions provide additional functionality for Entity Framework Core.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="67568-104">延伸模組以各種來源建置而成，且不屬於 Entity Framework Core 專案維護的一部份。</span><span class="sxs-lookup"><span data-stu-id="67568-104">Extensions are built by a variety of sources and not maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="67568-105">考慮使用協力廠商延伸模組時，請務必評估品質、授權、合規性、支援等，確保其符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="67568-105">When considering a third party extension, be sure to evaluate quality, licensing, compatibility, support, etc. to ensure they meet your requirements.</span></span>

## <a name="tools"></a><span data-ttu-id="67568-106">工具</span><span class="sxs-lookup"><span data-stu-id="67568-106">Tools</span></span>

### <a name="llblgen-pro"></a><span data-ttu-id="67568-107">LLBLGen Pro</span><span class="sxs-lookup"><span data-stu-id="67568-107">LLBLGen Pro</span></span>

<span data-ttu-id="67568-108">LLBLGen Pro 是實體模型化解決方案，具備 Entity Framework 與 Entity Framework Core 的支援。</span><span class="sxs-lookup"><span data-stu-id="67568-108">LLBLGen Pro is an entity modeling solution with support for Entity Framework and Entity Framework Core.</span></span> <span data-ttu-id="67568-109">您可加以利用來輕鬆定義實體模型，並將其對應至資料庫，優先使用資料庫或模型後，即可開始直接撰寫查詢。</span><span class="sxs-lookup"><span data-stu-id="67568-109">It lets you easily define your entity model and map it to your database, using database first or model first, so you can get started writing queries right away.</span></span>

[<span data-ttu-id="67568-110">網站</span><span class="sxs-lookup"><span data-stu-id="67568-110">website</span></span>](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a><span data-ttu-id="67568-111">Devart Entity Developer</span><span class="sxs-lookup"><span data-stu-id="67568-111">Devart Entity Developer</span></span>

<span data-ttu-id="67568-112">Devart Entity 是功能強大的 ORM 設計工具，適用於 ADO.NET Entity Framework、NHibernate、LinqConnect、Telerik Data Access、LINQ 到 SQL。</span><span class="sxs-lookup"><span data-stu-id="67568-112">Entity Developer is a powerful ORM designer for ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access, and LINQ to SQL.</span></span> <span data-ttu-id="67568-113">您可使用模型優先和資料庫優先方法來設計您的 ORM 模型，並為其產生 C# 或 Visual Basic .NET 程式碼。</span><span class="sxs-lookup"><span data-stu-id="67568-113">You can use  Model-First and Database-First approaches to design your ORM model and generate C# or Visual Basic .NET code for it.</span></span> <span data-ttu-id="67568-114">其為您帶來新的 ORM 模型設計方式，這不僅提高了生產力，還有助於資料庫應用程式的開發。</span><span class="sxs-lookup"><span data-stu-id="67568-114">It introduces new approaches for designing ORM models, boosts productivity, and facilitates the development of database applications.</span></span>

[<span data-ttu-id="67568-115">網站</span><span class="sxs-lookup"><span data-stu-id="67568-115">website</span></span>](https://www.devart.com/entitydeveloper/)

### <a name="ef-core-power-tools"></a><span data-ttu-id="67568-116">EF Core Power Tools</span><span class="sxs-lookup"><span data-stu-id="67568-116">EF Core Power Tools</span></span>

<span data-ttu-id="67568-117">Visual Studio 2017+ 延伸模組。</span><span class="sxs-lookup"><span data-stu-id="67568-117">Visual Studio 2017+ extension.</span></span> <span data-ttu-id="67568-118">您可從現有的資料庫或 SQL Server Database 專案執行 DbContext 及 POCO 類別的還原工程，並透過各種方式對 DbContext 進行視覺化及檢查。</span><span class="sxs-lookup"><span data-stu-id="67568-118">You can reverse engineer of DbContext and POCO classes from an existing database or SQL Server Database project, and visualize and inspect your DbContext in various ways.</span></span>

[<span data-ttu-id="67568-119">GitHub Wiki</span><span class="sxs-lookup"><span data-stu-id="67568-119">GitHub wiki</span></span>](https://github.com/ErikEJ/SqlCeToolbox/wiki/EF-Core-Power-Tools)

## <a name="extensions"></a><span data-ttu-id="67568-120">延伸模組</span><span class="sxs-lookup"><span data-stu-id="67568-120">Extensions</span></span>

### <a name="microsoftentityframeworkcoreautohistory"></a><span data-ttu-id="67568-121">Microsoft.EntityFrameworkCore.AutoHistory</span><span class="sxs-lookup"><span data-stu-id="67568-121">Microsoft.EntityFrameworkCore.AutoHistory</span></span>

<span data-ttu-id="67568-122">支援自動記錄資料變更歷程記錄的 Microsoft.EntityFrameworkCore 外掛程式。</span><span class="sxs-lookup"><span data-stu-id="67568-122">A plugin for Microsoft.EntityFrameworkCore to support automatically recording data changes history.</span></span>

[<span data-ttu-id="67568-123">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="67568-123">GitHub repository</span></span>](https://github.com/Arch/AutoHistory/)

### <a name="microsoftentityframeworkcoredynamiclinq"></a><span data-ttu-id="67568-124">Microsoft.EntityFrameworkCore.DynamicLinq</span><span class="sxs-lookup"><span data-stu-id="67568-124">Microsoft.EntityFrameworkCore.DynamicLinq</span></span>

<span data-ttu-id="67568-125">Microsoft.EntityFrameworkCore 的動態 LINQ 延伸模組，可新增非同步支援</span><span class="sxs-lookup"><span data-stu-id="67568-125">Dynamic Linq extensions for Microsoft.EntityFrameworkCore which adds Async support</span></span>

 [<span data-ttu-id="67568-126">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="67568-126">GitHub repository</span></span>](https://github.com/StefH/System.Linq.Dynamic.Core/)

### <a name="efcorepractices"></a><span data-ttu-id="67568-127">EFCore.Practices</span><span class="sxs-lookup"><span data-stu-id="67568-127">EFCore.Practices</span></span>

<span data-ttu-id="67568-128">嘗試在支援測試的 API 中擷取中上等級的做法 - 包括可掃描 N+1 個查詢的小型架構。</span><span class="sxs-lookup"><span data-stu-id="67568-128">Attempt to capture some good or best practices in an API that supports testing – including a small framework to scan for N+1 queries.</span></span>

[<span data-ttu-id="67568-129">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="67568-129">GitHub repository</span></span>](https://github.com/riezebosch/efcore-practices/tree/master/src/EFCore.Practices/)

### <a name="efsecondlevelcachecore"></a><span data-ttu-id="67568-130">EFSecondLevelCache.Core</span><span class="sxs-lookup"><span data-stu-id="67568-130">EFSecondLevelCache.Core</span></span>

<span data-ttu-id="67568-131">第二個層級快取程式庫。</span><span class="sxs-lookup"><span data-stu-id="67568-131">Second Level Caching Library.</span></span> <span data-ttu-id="67568-132">第二個層級快取是查詢快取。</span><span class="sxs-lookup"><span data-stu-id="67568-132">Second level caching is a query cache.</span></span> <span data-ttu-id="67568-133">EF 命令的結果會存放在快取中，使相同的 EF 命令會從快取擷取其資料，而不必再次向資料庫再次執行命令。</span><span class="sxs-lookup"><span data-stu-id="67568-133">The results of EF commands will be stored in the cache, so that the same EF commands will retrieve their data from the cache rather than executing them against the database again.</span></span>

[<span data-ttu-id="67568-134">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="67568-134">GitHub repository</span></span>](https://github.com/VahidN/EFSecondLevelCache.Core/)

### <a name="detachedentityframework"></a><span data-ttu-id="67568-135">Detached.EntityFramework</span><span class="sxs-lookup"><span data-stu-id="67568-135">Detached.EntityFramework</span></span>

<span data-ttu-id="67568-136">載入並儲存整個已中斷連結的實體圖表 (具備子實體與清單的實體)。</span><span class="sxs-lookup"><span data-stu-id="67568-136">Loads and saves entire detached entity graphs (the entity with their child entities and lists).</span></span> <span data-ttu-id="67568-137">此想法出自於 [GraphDiff](https://github.com/refactorthis/GraphDiff/)。</span><span class="sxs-lookup"><span data-stu-id="67568-137">Inspired by [GraphDiff](https://github.com/refactorthis/GraphDiff/).</span></span> <span data-ttu-id="67568-138">此想法同時也新增了部分外掛程式，來減輕部分工作的重複性，例如稽核與分頁。</span><span class="sxs-lookup"><span data-stu-id="67568-138">The idea is also add some plugins to simplificate some repetitive tasks, like auditing and pagination.</span></span>

[<span data-ttu-id="67568-139">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="67568-139">GitHub repository</span></span>](https://github.com/leonardoporro/Detached/)

### <a name="entityframeworkcoreprimarykey"></a><span data-ttu-id="67568-140">EntityFrameworkCore.PrimaryKey</span><span class="sxs-lookup"><span data-stu-id="67568-140">EntityFrameworkCore.PrimaryKey</span></span>

<span data-ttu-id="67568-141">從任何實體將主索引鍵 (包含複合索引鍵) 擷取為字典。</span><span class="sxs-lookup"><span data-stu-id="67568-141">Retrieve the primary key (including composite keys) from any entity as a dictionary.</span></span>

[<span data-ttu-id="67568-142">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="67568-142">GitHub repository</span></span>](https://github.com/NickStrupat/EntityFramework.PrimaryKey/)

### <a name="entityframeworkcorerx"></a><span data-ttu-id="67568-143">EntityFrameworkCore.Rx</span><span class="sxs-lookup"><span data-stu-id="67568-143">EntityFrameworkCore.Rx</span></span>

<span data-ttu-id="67568-144">Entity Framework 實體之經常性可視項的被動延伸模組包裝函式。</span><span class="sxs-lookup"><span data-stu-id="67568-144">Reactive extension wrappers for hot observables of Entity Framework entities.</span></span>

[<span data-ttu-id="67568-145">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="67568-145">GitHub repository</span></span>](https://github.com/NickStrupat/EntityFramework.Rx/)

### <a name="entityframeworkcoretriggers"></a><span data-ttu-id="67568-146">EntityFrameworkCore.Triggers</span><span class="sxs-lookup"><span data-stu-id="67568-146">EntityFrameworkCore.Triggers</span></span>

<span data-ttu-id="67568-147">為您的實體新增插入、更新及刪除事件的觸發程序。</span><span class="sxs-lookup"><span data-stu-id="67568-147">Add triggers to your entities with insert, update, and delete events.</span></span> <span data-ttu-id="67568-148">以下有三種事件，分別為：失敗前、失敗後以及失敗時。</span><span class="sxs-lookup"><span data-stu-id="67568-148">There are three events for each: before, after, and upon failure.</span></span>

[<span data-ttu-id="67568-149">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="67568-149">GitHub repository</span></span>](https://github.com/NickStrupat/EntityFramework.Triggers/)

### <a name="entityframeworkcoretypedoriginalvalues"></a><span data-ttu-id="67568-150">EntityFrameworkCore.TypedOriginalValues</span><span class="sxs-lookup"><span data-stu-id="67568-150">EntityFrameworkCore.TypedOriginalValues</span></span>

<span data-ttu-id="67568-151">為您實體屬性的 OriginalValue 取得具類型的存取權。</span><span class="sxs-lookup"><span data-stu-id="67568-151">Get typed access to the OriginalValue of your entity properties.</span></span> <span data-ttu-id="67568-152">支援簡單與複雜的屬性，但不支援瀏覽/集合。</span><span class="sxs-lookup"><span data-stu-id="67568-152">Simple and complex properties are supported, navigation/collections are not.</span></span>

[<span data-ttu-id="67568-153">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="67568-153">GitHub repository</span></span>](https://github.com/NickStrupat/EntityFramework.TypedOriginalValues/)

### <a name="geco"></a><span data-ttu-id="67568-154">Geco</span><span class="sxs-lookup"><span data-stu-id="67568-154">Geco</span></span>

<span data-ttu-id="67568-155">Geco 提供反向模型產生器，並具有複數/單數支援、以 C# 6.0 插入字串為基礎的可編輯範本，並在 .Net Core 上執行。</span><span class="sxs-lookup"><span data-stu-id="67568-155">Geco provides a Reverse Model generator with support for Pluralization/Singularization and editable templates based on C# 6.0 interpolated strings and running on .Net Core.</span></span> <span data-ttu-id="67568-156">其也提供了種子指令碼產生器，具有 SQL 合併指令碼及指令碼執行器。</span><span class="sxs-lookup"><span data-stu-id="67568-156">It also provides an Seed script generator with SQL Merge scripts and an script runner.</span></span>

[<span data-ttu-id="67568-157">Github 存放庫</span><span class="sxs-lookup"><span data-stu-id="67568-157">Github repository</span></span>](https://github.com/iQuarc/Geco)

### <a name="linqkitmicrosoftentityframeworkcore"></a><span data-ttu-id="67568-158">LinqKit.Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="67568-158">LinqKit.Microsoft.EntityFrameworkCore</span></span>

<span data-ttu-id="67568-159">LinqKit.Microsoft.EntityFrameworkCore 是一組免費的延伸模組，適用於 LINQ、SQL 及 EntityFrameworkCore 進階使用者。</span><span class="sxs-lookup"><span data-stu-id="67568-159">LinqKit.Microsoft.EntityFrameworkCore is a free set of extensions for LINQ to SQL and EntityFrameworkCore power users.</span></span> <span data-ttu-id="67568-160">具備 Include(...) 與 IDbAsync 支援。</span><span class="sxs-lookup"><span data-stu-id="67568-160">With Include(...) and IDbAsync support.</span></span>

[<span data-ttu-id="67568-161">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="67568-161">GitHub repository</span></span>](https://github.com/scottksmith95/LINQKit/)

### <a name="neinlinqentityframeworkcore"></a><span data-ttu-id="67568-162">NeinLinq.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="67568-162">NeinLinq.EntityFrameworkCore</span></span>

<span data-ttu-id="67568-163">NeinLinq.EntityFrameworkCore 為使用 LINQ 提供者提供了實用的延伸模組，例如僅支援 .NET 函式少數子集的 Entity Framework、重複使用函式、重寫查詢、甚至能使 null 不出現，還能使用可翻譯的述詞與選取器來建置動態查詢。</span><span class="sxs-lookup"><span data-stu-id="67568-163">NeinLinq.EntityFrameworkCore provides helpful extensions for using LINQ providers such as Entity Framework that support only a minor subset of .NET functions, reusing functions, rewriting queries, even making them null-safe, and building dynamic queries using translatable predicates and selectors.</span></span>

[<span data-ttu-id="67568-164">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="67568-164">GitHub repository</span></span>](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a><span data-ttu-id="67568-165">Microsoft.EntityFrameworkCore.UnitOfWork</span><span class="sxs-lookup"><span data-stu-id="67568-165">Microsoft.EntityFrameworkCore.UnitOfWork</span></span>

<span data-ttu-id="67568-166">Microsoft.EntityFrameworkCore 的外掛程式，可支援存放庫、工作模式的單位，以及支援分散式交易的多個資料庫。</span><span class="sxs-lookup"><span data-stu-id="67568-166">A plugin for Microsoft.EntityFrameworkCore to support repository, unit of work patterns, and multiple database with distributed transaction supported.</span></span>

[<span data-ttu-id="67568-167">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="67568-167">GitHub repository</span></span>](https://github.com/Arch/UnitOfWork/)

### <a name="entityframeworklazyloading"></a><span data-ttu-id="67568-168">EntityFramework.LazyLoading</span><span class="sxs-lookup"><span data-stu-id="67568-168">EntityFramework.LazyLoading</span></span>

<span data-ttu-id="67568-169">EF Core 1.1 的消極式載入</span><span class="sxs-lookup"><span data-stu-id="67568-169">Lazy Loading for EF Core 1.1</span></span>

[<span data-ttu-id="67568-170">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="67568-170">GitHub repository</span></span>](https://github.com/darxis/EntityFramework.LazyLoading)

### <a name="efcorebulkextensions"></a><span data-ttu-id="67568-171">EFCore.BulkExtensions</span><span class="sxs-lookup"><span data-stu-id="67568-171">EFCore.BulkExtensions</span></span>

<span data-ttu-id="67568-172">適用於大量作業 (插入、更新、刪除) 的 EntityFrameworkCore 延伸模組。</span><span class="sxs-lookup"><span data-stu-id="67568-172">EntityFrameworkCore extensions for Bulk operations (Insert, Update, Delete).</span></span>

[<span data-ttu-id="67568-173">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="67568-173">GitHub repository</span></span>](https://github.com/borisdj/EFCore.BulkExtensions)
