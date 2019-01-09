---
title: 工具和延伸模組 - EF Core
author: ErikEJ
ms.date: 01/07/2019
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
uid: core/extensions/index
ms.openlocfilehash: 414773284df7c208b9a2acf0536fda459bdf775b
ms.sourcegitcommit: 7bde8e6ad3c4565a4638646ce04bcf5e66f7b5fd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2019
ms.locfileid: "54069226"
---
# <a name="ef-core-tools--extensions"></a><span data-ttu-id="279f4-102">EF Core 工具和延伸模組</span><span class="sxs-lookup"><span data-stu-id="279f4-102">EF Core Tools & Extensions</span></span>

<span data-ttu-id="279f4-103">這些工具和延伸模組可為 Entity Framework Core 2.0 及更新版本提供額外的功能。</span><span class="sxs-lookup"><span data-stu-id="279f4-103">These tools and extensions provide additional functionality for Entity Framework Core 2.0 and later.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="279f4-104">延伸模組以各種來源建置而成，且不屬於 Entity Framework Core 專案維護的一部份。</span><span class="sxs-lookup"><span data-stu-id="279f4-104">Extensions are built by a variety of sources and aren't maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="279f4-105">考慮使用協力廠商延伸模組時，請務必評估其品質、授權、相容性、支援等，確保其符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="279f4-105">When considering a third party extension, be sure to evaluate its quality, licensing, compatibility, support, etc. to ensure it meets your requirements.</span></span>

## <a name="tools"></a><span data-ttu-id="279f4-106">工具</span><span class="sxs-lookup"><span data-stu-id="279f4-106">Tools</span></span>

### <a name="llblgen-pro"></a><span data-ttu-id="279f4-107">LLBLGen Pro</span><span class="sxs-lookup"><span data-stu-id="279f4-107">LLBLGen Pro</span></span>

<span data-ttu-id="279f4-108">LLBLGen Pro 是實體模型化解決方案，具備 Entity Framework 與 Entity Framework Core 的支援。</span><span class="sxs-lookup"><span data-stu-id="279f4-108">LLBLGen Pro is an entity modeling solution with support for Entity Framework and Entity Framework Core.</span></span> <span data-ttu-id="279f4-109">您可加以利用來輕鬆定義實體模型，並將其對應至資料庫，優先使用資料庫或模型後，即可開始直接撰寫查詢。</span><span class="sxs-lookup"><span data-stu-id="279f4-109">It lets you easily define your entity model and map it to your database, using database first or model first, so you can get started writing queries right away.</span></span>

[<span data-ttu-id="279f4-110">網站</span><span class="sxs-lookup"><span data-stu-id="279f4-110">Website</span></span>](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a><span data-ttu-id="279f4-111">Devart Entity Developer</span><span class="sxs-lookup"><span data-stu-id="279f4-111">Devart Entity Developer</span></span>

<span data-ttu-id="279f4-112">Devart Entity 是功能強大的 ORM 設計工具，適用於 ADO.NET Entity Framework、NHibernate、LinqConnect、Telerik Data Access、LINQ 到 SQL。</span><span class="sxs-lookup"><span data-stu-id="279f4-112">Entity Developer is a powerful ORM designer for ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access, and LINQ to SQL.</span></span> <span data-ttu-id="279f4-113">它支援使用 model first 或 database first 方法，以及 C# 或 Visual Basic 程式碼產生，以視覺化的方式設計 EF Core 模型。</span><span class="sxs-lookup"><span data-stu-id="279f4-113">It supports designing EF Core models visually, using model first or database first approaches, and C# or Visual Basic code generation.</span></span> 

[<span data-ttu-id="279f4-114">網站</span><span class="sxs-lookup"><span data-stu-id="279f4-114">Website</span></span>](https://www.devart.com/entitydeveloper/)

### <a name="ef-core-power-tools"></a><span data-ttu-id="279f4-115">EF Core Power Tools</span><span class="sxs-lookup"><span data-stu-id="279f4-115">EF Core Power Tools</span></span>

<span data-ttu-id="279f4-116">EF Core Power Tools 是 Visual Studio 2017 延伸模組，在簡單使用者介面中新增各種設計階段工作。</span><span class="sxs-lookup"><span data-stu-id="279f4-116">EF Core Power Tools is a Visual Studio 2017 extension that exposes various EF Core design-time tasks in a simple user interface.</span></span> <span data-ttu-id="279f4-117">包括現有資料庫和 [SQL Server DACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) 之 DbContext 及實體類別的反向工程、資料庫移轉的管理，以及模型視覺效果。</span><span class="sxs-lookup"><span data-stu-id="279f4-117">It includes reverse engineering of DbContext and entity classes from existing databases and [SQL Server DACPACs](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications), management of database migrations, and model visualizations.</span></span>

[<span data-ttu-id="279f4-118">GitHub Wiki</span><span class="sxs-lookup"><span data-stu-id="279f4-118">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a><span data-ttu-id="279f4-119">Entity Framework 視覺效果編輯器</span><span class="sxs-lookup"><span data-stu-id="279f4-119">Entity Framework Visual Editor</span></span>

<span data-ttu-id="279f4-120">Entity Framework 視覺效果編輯器是 Visual Studio 2017 延伸模組，為 EF 6 和 EF Core 類別的視覺效果設計新增 ORM 設計工具。</span><span class="sxs-lookup"><span data-stu-id="279f4-120">Entity Framework Visual Editor is a Visual Studio 2017 extension that adds an ORM designer for visual design of EF 6, and EF Core classes.</span></span> <span data-ttu-id="279f4-121">因為程式碼會透過 T4 範本產生，所以能加以自訂來滿足各種需求。</span><span class="sxs-lookup"><span data-stu-id="279f4-121">Code is generated using T4 templates so can be customized to suit any needs.</span></span> <span data-ttu-id="279f4-122">它支援繼承、單向及雙向關聯、列舉，還能夠對類別進行色彩編碼以及新增文字區塊，以說明設計中可能較不易懂的部分。</span><span class="sxs-lookup"><span data-stu-id="279f4-122">It supports inheritance, unidirectional and bidirectional associations, enumerations, and the ability to color-code your classes and add text blocks to explain potentially arcane parts of your design.</span></span>

<span data-ttu-id="279f4-123">[Marketplace](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="279f4-123">[Marketplace](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)</span></span>

### <a name="catfactory"></a><span data-ttu-id="279f4-124">CatFactory</span><span class="sxs-lookup"><span data-stu-id="279f4-124">CatFactory</span></span>

<span data-ttu-id="279f4-125">CatFactory 是 .NET Core 的 Scaffolding 引擎，可以自動從 SQL Server 資料庫產生 DbContext 類別、實體、對應組態及存放庫類別。</span><span class="sxs-lookup"><span data-stu-id="279f4-125">CatFactory is a scaffolding engine for .NET Core that can automate the generation of DbContext classes, entities, mapping configurations, and repository classes from a SQL Server database.</span></span>

[<span data-ttu-id="279f4-126">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="279f4-126">GitHub repository</span></span>](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a><span data-ttu-id="279f4-127">LoreSoft 的 Entity Framework Core Generator</span><span class="sxs-lookup"><span data-stu-id="279f4-127">LoreSoft's Entity Framework Core Generator</span></span>

<span data-ttu-id="279f4-128">Entity Framework Core 產生器 (efg) 是 .NET Core CLI 工具，可以從現有資料庫產生 EF Core 模型，與 `dotnet ef dbcontext scaffold` 十分相似，但它也透過區域取代或剖析對應檔案，支援安全的程式碼[重新產生](https://efg.loresoft.com/en/latest/regeneration/)。</span><span class="sxs-lookup"><span data-stu-id="279f4-128">Entity Framework Core Generator (efg) is a .NET Core CLI tool that can generate EF Core models from an existing database, much like `dotnet ef dbcontext scaffold`, but it also supports safe code [regeneration](https://efg.loresoft.com/en/latest/regeneration/) via region replacement or by parsing mapping files.</span></span> <span data-ttu-id="279f4-129">這項工具也支援產生檢視模型、驗證及物件對應程式程式碼。</span><span class="sxs-lookup"><span data-stu-id="279f4-129">This tool supports generating view models, validation, and object mapper code.</span></span> 

<span data-ttu-id="279f4-130">[教學課程](http://www.loresoft.com/Generate-ASP-NET-Web-API)
[文件](https://efg.loresoft.com/en/latest/)</span><span class="sxs-lookup"><span data-stu-id="279f4-130">[Tutorial](http://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span></span>

## <a name="extensions"></a><span data-ttu-id="279f4-131">延伸模組</span><span class="sxs-lookup"><span data-stu-id="279f4-131">Extensions</span></span>

### <a name="microsoftentityframeworkcoreautohistory"></a><span data-ttu-id="279f4-132">Microsoft.EntityFrameworkCore.AutoHistory</span><span class="sxs-lookup"><span data-stu-id="279f4-132">Microsoft.EntityFrameworkCore.AutoHistory</span></span>

<span data-ttu-id="279f4-133">外掛程式庫，能夠自動將 EF Core 執行的資料變更記錄到記錄資料表中。</span><span class="sxs-lookup"><span data-stu-id="279f4-133">A plugin library that enables automatically recording the data changes performed by EF Core into a history table.</span></span>

[<span data-ttu-id="279f4-134">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="279f4-134">GitHub repository</span></span>](https://github.com/Arch/AutoHistory/)

### <a name="microsoftentityframeworkcoredynamiclinq"></a><span data-ttu-id="279f4-135">Microsoft.EntityFrameworkCore.DynamicLinq</span><span class="sxs-lookup"><span data-stu-id="279f4-135">Microsoft.EntityFrameworkCore.DynamicLinq</span></span>

<span data-ttu-id="279f4-136">System.Linq.Dynamic 的 .NET Core/.NET Standard 連接埠，包括 EF Core 的非同步支援。</span><span class="sxs-lookup"><span data-stu-id="279f4-136">A .NET Core / .NET Standard port of System.Linq.Dynamic that includes async support with EF Core.</span></span>
<span data-ttu-id="279f4-137">System.Linq.Dynamic 原為 Microsoft 範例，說明如何從字串運算式 (而非程式碼) 動態建構 LINQ 查詢。</span><span class="sxs-lookup"><span data-stu-id="279f4-137">System.Linq.Dynamic originated as a Microsoft sample that shows how to construct LINQ queries dynamically from string expressions rather than code.</span></span>

[<span data-ttu-id="279f4-138">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="279f4-138">GitHub repository</span></span>](https://github.com/StefH/System.Linq.Dynamic.Core/)

### <a name="efsecondlevelcachecore"></a><span data-ttu-id="279f4-139">EFSecondLevelCache.Core</span><span class="sxs-lookup"><span data-stu-id="279f4-139">EFSecondLevelCache.Core</span></span>

<span data-ttu-id="279f4-140">將 EF Core 查詢的結果儲存至第二層快取的延伸模組，如此一來可避免後續執行相同查詢時，直接從快取存取資料庫及擷取資料。</span><span class="sxs-lookup"><span data-stu-id="279f4-140">An extension that enables storing the results of EF Core queries into a second-level cache, so that subsequent executions of the same queries can avoid accessing the database and retrieve the data directly from the cache.</span></span>

[<span data-ttu-id="279f4-141">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="279f4-141">GitHub repository</span></span>](https://github.com/VahidN/EFSecondLevelCache.Core/)

### <a name="entityframeworkcoreprimarykey"></a><span data-ttu-id="279f4-142">EntityFrameworkCore.PrimaryKey</span><span class="sxs-lookup"><span data-stu-id="279f4-142">EntityFrameworkCore.PrimaryKey</span></span>

<span data-ttu-id="279f4-143">這個文件庫允許從任何實體將主索引鍵 (包括複合索引鍵) 的值擷取為字典。</span><span class="sxs-lookup"><span data-stu-id="279f4-143">This library allows retrieving the values of primary key (including composite keys) from any entity as a dictionary.</span></span>

[<span data-ttu-id="279f4-144">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="279f4-144">GitHub repository</span></span>](https://github.com/NickStrupat/EntityFramework.PrimaryKey/)

### <a name="entityframeworkcoretypedoriginalvalues"></a><span data-ttu-id="279f4-145">EntityFrameworkCore.TypedOriginalValues</span><span class="sxs-lookup"><span data-stu-id="279f4-145">EntityFrameworkCore.TypedOriginalValues</span></span>

<span data-ttu-id="279f4-146">這個文件庫提供實體屬性原始值的強型別存取。</span><span class="sxs-lookup"><span data-stu-id="279f4-146">This library enables strongly typed access to the original values of entity properties.</span></span> 

[<span data-ttu-id="279f4-147">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="279f4-147">GitHub repository</span></span>](https://github.com/NickStrupat/EntityFramework.TypedOriginalValues/)

### <a name="geco"></a><span data-ttu-id="279f4-148">Geco</span><span class="sxs-lookup"><span data-stu-id="279f4-148">Geco</span></span>

<span data-ttu-id="279f4-149">Geco (產生器主控台) 是以主控台專案為基礎的簡單程式碼產生器，在 .NET Core 上執行，並使用插入 C# 的字串產生程式碼。</span><span class="sxs-lookup"><span data-stu-id="279f4-149">Geco (Generator Console) is a simple code generator based on a console project, that runs on .NET Core and uses C# interpolated strings for code generation.</span></span> <span data-ttu-id="279f4-150">Geco 包括適用於 EF Core 的反向模型產生器，支援複數、單數和可編輯範本。</span><span class="sxs-lookup"><span data-stu-id="279f4-150">Geco includes a reverse model generator for EF Core with support for pluralization, singularization, and editable templates.</span></span> <span data-ttu-id="279f4-151">它也提供種子資料指令碼產生器、指令碼執行器和資料庫清除工具。</span><span class="sxs-lookup"><span data-stu-id="279f4-151">It also provides a seed data script generator, a script runner, and a database cleaner.</span></span>

[<span data-ttu-id="279f4-152">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="279f4-152">GitHub repository</span></span>](https://github.com/iQuarc/Geco)

### <a name="linqkitmicrosoftentityframeworkcore"></a><span data-ttu-id="279f4-153">LinqKit.Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="279f4-153">LinqKit.Microsoft.EntityFrameworkCore</span></span>

<span data-ttu-id="279f4-154">LinqKit.Microsoft.EntityFrameworkCore 是 LINQKit 文件庫之與 EF Core 相容的版本。</span><span class="sxs-lookup"><span data-stu-id="279f4-154">LinqKit.Microsoft.EntityFrameworkCore is an EF Core-compatible version of the LINQKit library.</span></span> <span data-ttu-id="279f4-155">LINQKit 是適用於 LINQ to SQL 和 Entity Framework 進階使用者的免費延伸模組組合。</span><span class="sxs-lookup"><span data-stu-id="279f4-155">LINQKit is a free set of extensions for LINQ to SQL and Entity Framework power users.</span></span> <span data-ttu-id="279f4-156">它提供了進階功能，例如述詞運算式的動態建置，以及在子查詢中使用運算式變數。</span><span class="sxs-lookup"><span data-stu-id="279f4-156">It enables advanced functionality like dynamic building of predicate expressions, and using expression variables in subqueries.</span></span>  

[<span data-ttu-id="279f4-157">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="279f4-157">GitHub repository</span></span>](https://github.com/scottksmith95/LINQKit/)

### <a name="neinlinqentityframeworkcore"></a><span data-ttu-id="279f4-158">NeinLinq.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="279f4-158">NeinLinq.EntityFrameworkCore</span></span>

<span data-ttu-id="279f4-159">NeinLinq 擴充了 LINQ 提供者 (例如 Entity Framework)，並提供以下功能：重複使用函式、重寫查詢，以及使用可翻譯的述詞和選取器建置動態查詢。</span><span class="sxs-lookup"><span data-stu-id="279f4-159">NeinLinq extends LINQ providers such as Entity Framework to enable reusing functions, rewriting queries, and building dynamic queries using translatable predicates and selectors.</span></span>

[<span data-ttu-id="279f4-160">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="279f4-160">GitHub repository</span></span>](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a><span data-ttu-id="279f4-161">Microsoft.EntityFrameworkCore.UnitOfWork</span><span class="sxs-lookup"><span data-stu-id="279f4-161">Microsoft.EntityFrameworkCore.UnitOfWork</span></span>

<span data-ttu-id="279f4-162">Microsoft.EntityFrameworkCore 的外掛程式，可支援存放庫、工作模式的單位，以及支援分散式交易的多個資料庫。</span><span class="sxs-lookup"><span data-stu-id="279f4-162">A plugin for Microsoft.EntityFrameworkCore to support repository, unit of work patterns, and multiple databases with distributed transaction supported.</span></span>

[<span data-ttu-id="279f4-163">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="279f4-163">GitHub repository</span></span>](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a><span data-ttu-id="279f4-164">EFCore.BulkExtensions</span><span class="sxs-lookup"><span data-stu-id="279f4-164">EFCore.BulkExtensions</span></span>

<span data-ttu-id="279f4-165">適用於大量作業 (插入、更新、刪除) 的 EF Core 延伸模組。</span><span class="sxs-lookup"><span data-stu-id="279f4-165">EF Core extensions for Bulk operations (Insert, Update, Delete).</span></span>

[<span data-ttu-id="279f4-166">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="279f4-166">GitHub repository</span></span>](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a><span data-ttu-id="279f4-167">Bricelam.EntityFrameworkCore.Pluralizer</span><span class="sxs-lookup"><span data-stu-id="279f4-167">Bricelam.EntityFrameworkCore.Pluralizer</span></span>

<span data-ttu-id="279f4-168">新增設計階段複數表示到 EF Core。</span><span class="sxs-lookup"><span data-stu-id="279f4-168">Adds design-time pluralization to EF Core.</span></span>

[<span data-ttu-id="279f4-169">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="279f4-169">GitHub repository</span></span>](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="pomelofoundationpomeloentityframeworkcoreextensionstosql"></a><span data-ttu-id="279f4-170">PomeloFoundation/Pomelo.EntityFrameworkCore.Extensions.ToSql</span><span class="sxs-lookup"><span data-stu-id="279f4-170">PomeloFoundation/Pomelo.EntityFrameworkCore.Extensions.ToSql</span></span>

<span data-ttu-id="279f4-171">簡單的擴充方法，可取得 EF Core 在簡單案例中為所指定 LINQ 查詢產生的 SQL 陳述式。</span><span class="sxs-lookup"><span data-stu-id="279f4-171">A simple extension method that obtains the SQL statement EF Core would generate for a given LINQ query in simple scenarios.</span></span> <span data-ttu-id="279f4-172">由於 EF Core 可以為單一 LINQ 查詢產生多個 SQL 陳述式，並視參數值產生不同的 SQL 陳述式，因此 ToSql 方法僅限於簡單案例。</span><span class="sxs-lookup"><span data-stu-id="279f4-172">The ToSql method is limited to simple scenarios because EF Core can generate more than one SQL statement for a single LINQ query, and different SQL statements depending on parameter values.</span></span>

[<span data-ttu-id="279f4-173">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="279f4-173">GitHub repository</span></span>](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.Extensions.ToSql)

### <a name="toolbeltentityframeworkcoreindexattribute"></a><span data-ttu-id="279f4-174">Toolbelt.EntityFrameworkCore.IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="279f4-174">Toolbelt.EntityFrameworkCore.IndexAttribute</span></span>

<span data-ttu-id="279f4-175">EF Core [Index] 屬性的復興 (附有模型建置的延伸模組)。</span><span class="sxs-lookup"><span data-stu-id="279f4-175">Revival of [Index] attribute for EF Core (with extension for model building).</span></span>

[<span data-ttu-id="279f4-176">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="279f4-176">GitHub repository</span></span>](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a><span data-ttu-id="279f4-177">EfCore.InMemoryHelpers</span><span class="sxs-lookup"><span data-stu-id="279f4-177">EfCore.InMemoryHelpers</span></span>

<span data-ttu-id="279f4-178">提供 EF Core 記憶體內部資料庫提供者的包裝函式。</span><span class="sxs-lookup"><span data-stu-id="279f4-178">Provides a wrapper around the EF Core In-Memory Database Provider.</span></span> <span data-ttu-id="279f4-179">使它的運作更接近關聯式提供者。</span><span class="sxs-lookup"><span data-stu-id="279f4-179">Makes it act more like a relational provider.</span></span>

[<span data-ttu-id="279f4-180">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="279f4-180">GitHub repository</span></span>](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a><span data-ttu-id="279f4-181">EFCore.TemporalSupport</span><span class="sxs-lookup"><span data-stu-id="279f4-181">EFCore.TemporalSupport</span></span>

<span data-ttu-id="279f4-182">EF Core 時態性支援的實作。</span><span class="sxs-lookup"><span data-stu-id="279f4-182">An implementation of temporal support for EF Core.</span></span>

[<span data-ttu-id="279f4-183">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="279f4-183">GitHub repository</span></span>](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="entityframeworkcorecacheable"></a><span data-ttu-id="279f4-184">EntityFrameworkCore.Cacheable</span><span class="sxs-lookup"><span data-stu-id="279f4-184">EntityFrameworkCore.Cacheable</span></span>

<span data-ttu-id="279f4-185">EF Core 的高效能第二層查詢快取。</span><span class="sxs-lookup"><span data-stu-id="279f4-185">A high-performance second-level query cache for EF Core.</span></span>

[<span data-ttu-id="279f4-186">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="279f4-186">GitHub repository</span></span>](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)
