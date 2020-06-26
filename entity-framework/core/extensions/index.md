---
title: 工具和延伸模組 - EF Core
author: ErikEJ
ms.date: 04/11/2020
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
uid: core/extensions/index
ms.openlocfilehash: 47a5601e996afab9c0aa0538a2a2b47f204f7753
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370352"
---
# <a name="ef-core-tools--extensions"></a><span data-ttu-id="172d3-102">EF Core 工具和延伸模組</span><span class="sxs-lookup"><span data-stu-id="172d3-102">EF Core Tools & Extensions</span></span>

<span data-ttu-id="172d3-103">這些工具和延伸模組可為 Entity Framework Core 2.1 及更新版本提供額外的功能。</span><span class="sxs-lookup"><span data-stu-id="172d3-103">These tools and extensions provide additional functionality for Entity Framework Core 2.1 and later.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="172d3-104">延伸模組以各種來源建置而成，且不屬於 Entity Framework Core 專案維護的一部份。</span><span class="sxs-lookup"><span data-stu-id="172d3-104">Extensions are built by a variety of sources and aren't maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="172d3-105">考慮使用協力廠商延伸模組時，請務必評估其品質、授權、相容性、支援等，確保其符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="172d3-105">When considering a third party extension, be sure to evaluate its quality, licensing, compatibility, support, etc. to ensure it meets your requirements.</span></span> <span data-ttu-id="172d3-106">特別是針對舊版 EF Core 所建置的延伸模組，可能需要予以更新，才能搭配最新版本使用。</span><span class="sxs-lookup"><span data-stu-id="172d3-106">In particular, an extension built for an older version of EF Core may need updating before it will work with the latest versions.</span></span>

## <a name="tools"></a><span data-ttu-id="172d3-107">工具</span><span class="sxs-lookup"><span data-stu-id="172d3-107">Tools</span></span>

### <a name="llblgen-pro"></a><span data-ttu-id="172d3-108">LLBLGen Pro</span><span class="sxs-lookup"><span data-stu-id="172d3-108">LLBLGen Pro</span></span>

<span data-ttu-id="172d3-109">LLBLGen Pro 是實體模型化解決方案，具備 Entity Framework 與 Entity Framework Core 的支援。</span><span class="sxs-lookup"><span data-stu-id="172d3-109">LLBLGen Pro is an entity modeling solution with support for Entity Framework and Entity Framework Core.</span></span> <span data-ttu-id="172d3-110">您可加以利用來輕鬆定義實體模型，並將其對應至資料庫，優先使用資料庫或模型後，即可開始直接撰寫查詢。</span><span class="sxs-lookup"><span data-stu-id="172d3-110">It lets you easily define your entity model and map it to your database, using database first or model first, so you can get started writing queries right away.</span></span> <span data-ttu-id="172d3-111">適用於 EF Core：2、3</span><span class="sxs-lookup"><span data-stu-id="172d3-111">For EF Core: 2, 3</span></span>

[<span data-ttu-id="172d3-112">網站</span><span class="sxs-lookup"><span data-stu-id="172d3-112">Website</span></span>](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a><span data-ttu-id="172d3-113">Devart Entity Developer</span><span class="sxs-lookup"><span data-stu-id="172d3-113">Devart Entity Developer</span></span>

<span data-ttu-id="172d3-114">Devart Entity 是功能強大的 ORM 設計工具，適用於 ADO.NET Entity Framework、NHibernate、LinqConnect、Telerik Data Access、LINQ 到 SQL。</span><span class="sxs-lookup"><span data-stu-id="172d3-114">Entity Developer is a powerful ORM designer for ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access, and LINQ to SQL.</span></span> <span data-ttu-id="172d3-115">它支援使用 model first 或 database first 方法，以及 C# 或 Visual Basic 程式碼產生，以視覺化的方式設計 EF Core 模型。</span><span class="sxs-lookup"><span data-stu-id="172d3-115">It supports designing EF Core models visually, using model first or database first approaches, and C# or Visual Basic code generation.</span></span> <span data-ttu-id="172d3-116">若為 EF Core：1、2、3、5。</span><span class="sxs-lookup"><span data-stu-id="172d3-116">For EF Core: 1, 2, 3, 5.</span></span>

[<span data-ttu-id="172d3-117">網站</span><span class="sxs-lookup"><span data-stu-id="172d3-117">Website</span></span>](https://www.devart.com/entitydeveloper/)

### <a name="nhydrate-orm-for-entity-framework"></a><span data-ttu-id="172d3-118">Entity Framework 的 nHydrate ORM</span><span class="sxs-lookup"><span data-stu-id="172d3-118">nHydrate ORM for Entity Framework</span></span>

<span data-ttu-id="172d3-119">為 Entity Framework 建立可延伸類別的強型別 ORM。</span><span class="sxs-lookup"><span data-stu-id="172d3-119">An ORM that creates strongly-typed, extendable classes for Entity Framework.</span></span> <span data-ttu-id="172d3-120">產生的程式碼為 Entity Framework Core。</span><span class="sxs-lookup"><span data-stu-id="172d3-120">The generated code is Entity Framework Core.</span></span> <span data-ttu-id="172d3-121">因此兩者沒有任何不同。</span><span class="sxs-lookup"><span data-stu-id="172d3-121">There is no difference.</span></span> <span data-ttu-id="172d3-122">這不能取代 EF 或自訂 ORM。</span><span class="sxs-lookup"><span data-stu-id="172d3-122">This is not a replacement for EF or a custom ORM.</span></span> <span data-ttu-id="172d3-123">這是一種視覺化的模型化層，可讓小組管理複雜的資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="172d3-123">It is a visual, modeling layer that allows a team to manage complex database schemas.</span></span> <span data-ttu-id="172d3-124">適用於 Git 這類的 SCM 軟體，允許多使用者存取模型，但具有最少的衝突。</span><span class="sxs-lookup"><span data-stu-id="172d3-124">It works well with SCM software like Git, allowing multi-user access to your model with minimal conflicts.</span></span> <span data-ttu-id="172d3-125">安裝程式會追蹤模型變更並建立升級指令碼。</span><span class="sxs-lookup"><span data-stu-id="172d3-125">The installer tracks model changes and creates upgrade scripts.</span></span> <span data-ttu-id="172d3-126">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="172d3-126">For EF Core: 3.</span></span>

[<span data-ttu-id="172d3-127">Github 網站</span><span class="sxs-lookup"><span data-stu-id="172d3-127">Github site</span></span>](https://github.com/nHydrate/nHydrate)

### <a name="ef-core-power-tools"></a><span data-ttu-id="172d3-128">EF Core Power Tools</span><span class="sxs-lookup"><span data-stu-id="172d3-128">EF Core Power Tools</span></span>

<span data-ttu-id="172d3-129">EF Core Power Tools 是 Visual Studio 延伸模組，在簡單使用者介面中公開各種設計階段工作。</span><span class="sxs-lookup"><span data-stu-id="172d3-129">EF Core Power Tools is a Visual Studio extension that exposes various EF Core design-time tasks in a simple user interface.</span></span> <span data-ttu-id="172d3-130">包括現有資料庫和 [SQL Server DACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) 之 DbContext 及實體類別的反向工程、資料庫移轉的管理，以及模型視覺效果。</span><span class="sxs-lookup"><span data-stu-id="172d3-130">It includes reverse engineering of DbContext and entity classes from existing databases and [SQL Server DACPACs](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications), management of database migrations, and model visualizations.</span></span> <span data-ttu-id="172d3-131">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="172d3-131">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="172d3-132">GitHub Wiki</span><span class="sxs-lookup"><span data-stu-id="172d3-132">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a><span data-ttu-id="172d3-133">Entity Framework 視覺效果編輯器</span><span class="sxs-lookup"><span data-stu-id="172d3-133">Entity Framework Visual Editor</span></span>

<span data-ttu-id="172d3-134">Entity Framework 視覺效果編輯器是 Visual Studio 延伸模組，為 EF 6 和 EF Core 類別的視覺效果設計新增 ORM 設計工具。</span><span class="sxs-lookup"><span data-stu-id="172d3-134">Entity Framework Visual Editor is a Visual Studio extension that adds an ORM designer for visual design of EF 6, and EF Core classes.</span></span> <span data-ttu-id="172d3-135">因為程式碼會透過 T4 範本產生，所以能加以自訂來滿足各種需求。</span><span class="sxs-lookup"><span data-stu-id="172d3-135">Code is generated using T4 templates so can be customized to suit any needs.</span></span> <span data-ttu-id="172d3-136">它支援繼承、單向及雙向關聯、列舉，還能夠對類別進行色彩編碼以及新增文字區塊，以說明設計中可能較不易懂的部分。</span><span class="sxs-lookup"><span data-stu-id="172d3-136">It supports inheritance, unidirectional and bidirectional associations, enumerations, and the ability to color-code your classes and add text blocks to explain potentially arcane parts of your design.</span></span> <span data-ttu-id="172d3-137">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="172d3-137">For EF Core: 2.</span></span>

<span data-ttu-id="172d3-138">[Marketplace](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="172d3-138">[Marketplace](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)</span></span>

### <a name="catfactory"></a><span data-ttu-id="172d3-139">CatFactory</span><span class="sxs-lookup"><span data-stu-id="172d3-139">CatFactory</span></span>

<span data-ttu-id="172d3-140">CatFactory 是 .NET Core 的 Scaffolding 引擎，可以自動從 SQL Server 資料庫產生 DbContext 類別、實體、對應組態及存放庫類別。</span><span class="sxs-lookup"><span data-stu-id="172d3-140">CatFactory is a scaffolding engine for .NET Core that can automate the generation of DbContext classes, entities, mapping configurations, and repository classes from a SQL Server database.</span></span> <span data-ttu-id="172d3-141">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="172d3-141">For EF Core: 2.</span></span>

[<span data-ttu-id="172d3-142">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="172d3-142">GitHub repository</span></span>](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a><span data-ttu-id="172d3-143">LoreSoft 的 Entity Framework Core Generator</span><span class="sxs-lookup"><span data-stu-id="172d3-143">LoreSoft's Entity Framework Core Generator</span></span>

<span data-ttu-id="172d3-144">Entity Framework Core 產生器 (efg) 是 .NET Core CLI 工具，可以從現有資料庫產生 EF Core 模型，與 `dotnet ef dbcontext scaffold` 十分相似，但它也透過區域取代或剖析對應檔案，支援安全的程式碼[重新產生](https://efg.loresoft.com/en/latest/regeneration/)。</span><span class="sxs-lookup"><span data-stu-id="172d3-144">Entity Framework Core Generator (efg) is a .NET Core CLI tool that can generate EF Core models from an existing database, much like `dotnet ef dbcontext scaffold`, but it also supports safe code [regeneration](https://efg.loresoft.com/en/latest/regeneration/) via region replacement or by parsing mapping files.</span></span> <span data-ttu-id="172d3-145">這項工具也支援產生檢視模型、驗證及物件對應程式程式碼。</span><span class="sxs-lookup"><span data-stu-id="172d3-145">This tool supports generating view models, validation, and object mapper code.</span></span> <span data-ttu-id="172d3-146">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="172d3-146">For EF Core: 2.</span></span>

<span data-ttu-id="172d3-147">[教學課程](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[文件](https://efg.loresoft.com/en/latest/)</span><span class="sxs-lookup"><span data-stu-id="172d3-147">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span></span>

## <a name="extensions"></a><span data-ttu-id="172d3-148">延伸模組</span><span class="sxs-lookup"><span data-stu-id="172d3-148">Extensions</span></span>

### <a name="microsoftentityframeworkcoreautohistory"></a><span data-ttu-id="172d3-149">Microsoft.EntityFrameworkCore.AutoHistory</span><span class="sxs-lookup"><span data-stu-id="172d3-149">Microsoft.EntityFrameworkCore.AutoHistory</span></span>

<span data-ttu-id="172d3-150">外掛程式庫，能夠自動將 EF Core 執行的資料變更記錄到記錄資料表中。</span><span class="sxs-lookup"><span data-stu-id="172d3-150">A plugin library that enables automatically recording the data changes performed by EF Core into a history table.</span></span> <span data-ttu-id="172d3-151">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="172d3-151">For EF Core: 2.</span></span>

[<span data-ttu-id="172d3-152">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="172d3-152">GitHub repository</span></span>](https://github.com/Arch/AutoHistory/)

### <a name="efcoresecondlevelcacheinterceptor"></a><span data-ttu-id="172d3-153">EFCoreSecondLevelCacheInterceptor</span><span class="sxs-lookup"><span data-stu-id="172d3-153">EFCoreSecondLevelCacheInterceptor</span></span>

<span data-ttu-id="172d3-154">第二個層級快取是查詢快取。</span><span class="sxs-lookup"><span data-stu-id="172d3-154">Second level caching is a query cache.</span></span> <span data-ttu-id="172d3-155">EF 命令的結果會存放在快取中，使相同的 EF 命令會從快取擷取其資料，而不必再次向資料庫再次執行命令。</span><span class="sxs-lookup"><span data-stu-id="172d3-155">The results of EF commands will be stored in the cache, so that the same EF commands will retrieve their data from the cache rather than executing them against the database again.</span></span> <span data-ttu-id="172d3-156">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="172d3-156">For EF Core: 3.</span></span>

[<span data-ttu-id="172d3-157">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="172d3-157">GitHub repository</span></span>](https://github.com/VahidN/EFCoreSecondLevelCacheInterceptor)

### <a name="geco"></a><span data-ttu-id="172d3-158">Geco</span><span class="sxs-lookup"><span data-stu-id="172d3-158">Geco</span></span>

<span data-ttu-id="172d3-159">Geco (產生器主控台) 是以主控台專案為基礎的簡單程式碼產生器，在 .NET Core 上執行，並使用插入 C# 的字串產生程式碼。</span><span class="sxs-lookup"><span data-stu-id="172d3-159">Geco (Generator Console) is a simple code generator based on a console project, that runs on .NET Core and uses C# interpolated strings for code generation.</span></span> <span data-ttu-id="172d3-160">Geco 包括適用於 EF Core 的反向模型產生器，支援複數、單數和可編輯範本。</span><span class="sxs-lookup"><span data-stu-id="172d3-160">Geco includes a reverse model generator for EF Core with support for pluralization, singularization, and editable templates.</span></span> <span data-ttu-id="172d3-161">它也提供種子資料指令碼產生器、指令碼執行器和資料庫清除工具。</span><span class="sxs-lookup"><span data-stu-id="172d3-161">It also provides a seed data script generator, a script runner, and a database cleaner.</span></span> <span data-ttu-id="172d3-162">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="172d3-162">For EF Core: 2.</span></span>

[<span data-ttu-id="172d3-163">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="172d3-163">GitHub repository</span></span>](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a><span data-ttu-id="172d3-164">EntityFrameworkCore.Scaffolding.Handlebars</span><span class="sxs-lookup"><span data-stu-id="172d3-164">EntityFrameworkCore.Scaffolding.Handlebars</span></span> 

<span data-ttu-id="172d3-165">可讓您搭配 Handlebars 範本使用 Entity Framework Core 工具鏈，自訂從現有資料庫進行反向工程的類別。</span><span class="sxs-lookup"><span data-stu-id="172d3-165">Allows customization of classes reverse engineered from an existing database using the Entity Framework Core toolchain with Handlebars templates.</span></span> <span data-ttu-id="172d3-166">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="172d3-166">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="172d3-167">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="172d3-167">GitHub repository</span></span>](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a><span data-ttu-id="172d3-168">NeinLinq.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="172d3-168">NeinLinq.EntityFrameworkCore</span></span> 

<span data-ttu-id="172d3-169">NeinLinq 擴充了 LINQ 提供者 (例如 Entity Framework)，並提供以下功能：重複使用函式、重寫查詢，以及使用可翻譯的述詞和選取器建置動態查詢。</span><span class="sxs-lookup"><span data-stu-id="172d3-169">NeinLinq extends LINQ providers such as Entity Framework to enable reusing functions, rewriting queries, and building dynamic queries using translatable predicates and selectors.</span></span> <span data-ttu-id="172d3-170">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="172d3-170">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="172d3-171">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="172d3-171">GitHub repository</span></span>](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a><span data-ttu-id="172d3-172">Microsoft.EntityFrameworkCore.UnitOfWork</span><span class="sxs-lookup"><span data-stu-id="172d3-172">Microsoft.EntityFrameworkCore.UnitOfWork</span></span>

<span data-ttu-id="172d3-173">Microsoft.EntityFrameworkCore 的外掛程式，可支援存放庫、工作模式的單位，以及支援分散式交易的多個資料庫。</span><span class="sxs-lookup"><span data-stu-id="172d3-173">A plugin for Microsoft.EntityFrameworkCore to support repository, unit of work patterns, and multiple databases with distributed transaction supported.</span></span> <span data-ttu-id="172d3-174">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="172d3-174">For EF Core: 2.</span></span>

[<span data-ttu-id="172d3-175">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="172d3-175">GitHub repository</span></span>](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a><span data-ttu-id="172d3-176">EFCore.BulkExtensions</span><span class="sxs-lookup"><span data-stu-id="172d3-176">EFCore.BulkExtensions</span></span>

<span data-ttu-id="172d3-177">適用於大量作業 (插入、更新、刪除) 的 EF Core 延伸模組。</span><span class="sxs-lookup"><span data-stu-id="172d3-177">EF Core extensions for Bulk operations (Insert, Update, Delete).</span></span> <span data-ttu-id="172d3-178">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="172d3-178">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="172d3-179">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="172d3-179">GitHub repository</span></span>](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a><span data-ttu-id="172d3-180">Bricelam.EntityFrameworkCore.Pluralizer</span><span class="sxs-lookup"><span data-stu-id="172d3-180">Bricelam.EntityFrameworkCore.Pluralizer</span></span>

<span data-ttu-id="172d3-181">新增設計階段複數表示。</span><span class="sxs-lookup"><span data-stu-id="172d3-181">Adds design-time pluralization.</span></span> <span data-ttu-id="172d3-182">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="172d3-182">For EF Core: 2.</span></span>

[<span data-ttu-id="172d3-183">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="172d3-183">GitHub repository</span></span>](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a><span data-ttu-id="172d3-184">Toolbelt.EntityFrameworkCore.IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="172d3-184">Toolbelt.EntityFrameworkCore.IndexAttribute</span></span>

<span data-ttu-id="172d3-185">[Index] 屬性的復興 (附有模型組建的延伸模組)。</span><span class="sxs-lookup"><span data-stu-id="172d3-185">Revival of [Index] attribute (with extension for model building).</span></span> <span data-ttu-id="172d3-186">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="172d3-186">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="172d3-187">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="172d3-187">GitHub repository</span></span>](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a><span data-ttu-id="172d3-188">EfCore.InMemoryHelpers</span><span class="sxs-lookup"><span data-stu-id="172d3-188">EfCore.InMemoryHelpers</span></span>

<span data-ttu-id="172d3-189">提供 EF Core 記憶體內部資料庫提供者的包裝函式。</span><span class="sxs-lookup"><span data-stu-id="172d3-189">Provides a wrapper around the EF Core In-Memory Database Provider.</span></span> <span data-ttu-id="172d3-190">使它的運作更接近關聯式提供者。</span><span class="sxs-lookup"><span data-stu-id="172d3-190">Makes it act more like a relational provider.</span></span> <span data-ttu-id="172d3-191">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="172d3-191">For EF Core: 2.</span></span>

[<span data-ttu-id="172d3-192">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="172d3-192">GitHub repository</span></span>](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a><span data-ttu-id="172d3-193">EFCore.TemporalSupport</span><span class="sxs-lookup"><span data-stu-id="172d3-193">EFCore.TemporalSupport</span></span>

<span data-ttu-id="172d3-194">時態性支援的實作。</span><span class="sxs-lookup"><span data-stu-id="172d3-194">An implementation of temporal support.</span></span> <span data-ttu-id="172d3-195">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="172d3-195">For EF Core: 2.</span></span>

[<span data-ttu-id="172d3-196">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="172d3-196">GitHub repository</span></span>](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a><span data-ttu-id="172d3-197">EfCoreTemporalTable</span><span class="sxs-lookup"><span data-stu-id="172d3-197">EfCoreTemporalTable</span></span>

<span data-ttu-id="172d3-198">使用下列引進的擴充方法，輕鬆地在慣用的資料庫上執行時態性查詢：`AsTemporalAll()`、`AsTemporalAsOf(date)`、`AsTemporalFrom(startDate, endDate)`、`AsTemporalBetween(startDate, endDate)`、`AsTemporalContained(startDate, endDate)`。</span><span class="sxs-lookup"><span data-stu-id="172d3-198">Easily perform temporal queries on your favourite database using introduced extension methods: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span></span> <span data-ttu-id="172d3-199">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="172d3-199">For EF Core: 3.</span></span>

[<span data-ttu-id="172d3-200">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="172d3-200">GitHub repository</span></span>](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="efcoretimetraveler"></a><span data-ttu-id="172d3-201">EFCore.TimeTraveler</span><span class="sxs-lookup"><span data-stu-id="172d3-201">EFCore.TimeTraveler</span></span>

<span data-ttu-id="172d3-202">允許使用已定義的 EF Core 程式碼、實體和對應，對 [SQL Server 時態性記錄](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel)進行完整功能的 Entity Framework Core 查詢。</span><span class="sxs-lookup"><span data-stu-id="172d3-202">Allow full-featured Entity Framework Core queries against [SQL Server Temporal History](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel) using the EF Core code, entities, and mappings you already have defined.</span></span>  <span data-ttu-id="172d3-203">藉由將程式碼包裝在 `using (TemporalQuery.AsOf(targetDateTime)) {...}` 中來移動時間。</span><span class="sxs-lookup"><span data-stu-id="172d3-203">Travel through time by wrapping your code in `using (TemporalQuery.AsOf(targetDateTime)) {...}`.</span></span> <span data-ttu-id="172d3-204">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="172d3-204">For EF Core: 3.</span></span>

[<span data-ttu-id="172d3-205">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="172d3-205">GitHub repository</span></span>](https://github.com/VantageSoftware/EFCore.TimeTraveler)


### <a name="entityframeworkcoretemporaltables"></a><span data-ttu-id="172d3-206">EntityFrameworkCore.TemporalTables</span><span class="sxs-lookup"><span data-stu-id="172d3-206">EntityFrameworkCore.TemporalTables</span></span>

<span data-ttu-id="172d3-207">Entity Framework Core 的延伸模組程式庫，可讓使用 SQL Server 的開發人員輕鬆地使用時態表。</span><span class="sxs-lookup"><span data-stu-id="172d3-207">Extension library for Entity Framework Core which allows developers who use SQL Server to easily use temporal tables.</span></span> <span data-ttu-id="172d3-208">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="172d3-208">For EF Core: 2.</span></span>

[<span data-ttu-id="172d3-209">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="172d3-209">GitHub repository</span></span>](https://github.com/findulov/EntityFrameworkCore.TemporalTables)


### <a name="entityframeworkcorecacheable"></a><span data-ttu-id="172d3-210">EntityFrameworkCore.Cacheable</span><span class="sxs-lookup"><span data-stu-id="172d3-210">EntityFrameworkCore.Cacheable</span></span>

<span data-ttu-id="172d3-211">高效能第二層查詢快取。</span><span class="sxs-lookup"><span data-stu-id="172d3-211">A high-performance second-level query cache.</span></span> <span data-ttu-id="172d3-212">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="172d3-212">For EF Core: 2.</span></span>

[<span data-ttu-id="172d3-213">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="172d3-213">GitHub repository</span></span>](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entity-framework-plus"></a><span data-ttu-id="172d3-214">Entity Framework Plus</span><span class="sxs-lookup"><span data-stu-id="172d3-214">Entity Framework Plus</span></span>

<span data-ttu-id="172d3-215">使用下功能延伸您的 DbContext：包括篩選、稽核、快取、查詢未來、批次刪除、批次更新等。</span><span class="sxs-lookup"><span data-stu-id="172d3-215">Extends your DbContext with features such as: Include Filter, Auditing, Caching, Query Future, Batch Delete, Batch Update, and more.</span></span> <span data-ttu-id="172d3-216">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="172d3-216">For EF Core: 2, 3.</span></span>

<span data-ttu-id="172d3-217">[網站](https://entityframework-plus.net/)
[GitHub 存放庫](https://github.com/zzzprojects/EntityFramework-Plus)</span><span class="sxs-lookup"><span data-stu-id="172d3-217">[Website](https://entityframework-plus.net/)
[GitHub repository](https://github.com/zzzprojects/EntityFramework-Plus)</span></span>

### <a name="entity-framework-extensions"></a><span data-ttu-id="172d3-218">Entity Framework 擴充功能</span><span class="sxs-lookup"><span data-stu-id="172d3-218">Entity Framework Extensions</span></span>

<span data-ttu-id="172d3-219">使用高效能批次作業延伸您的 DbContext：BulkSaveChanges、BulkInsert、BulkUpdate、BulkDelete、BulkMerge 等。</span><span class="sxs-lookup"><span data-stu-id="172d3-219">Extends your DbContext with high-performance bulk operations: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge, and more.</span></span> <span data-ttu-id="172d3-220">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="172d3-220">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="172d3-221">網站</span><span class="sxs-lookup"><span data-stu-id="172d3-221">Website</span></span>](https://entityframework-extensions.net/)

### <a name="expressionify"></a><span data-ttu-id="172d3-222">Expressionify</span><span class="sxs-lookup"><span data-stu-id="172d3-222">Expressionify</span></span>

<span data-ttu-id="172d3-223">新增在 LINQ Lambda 中呼叫擴充方法的支援。</span><span class="sxs-lookup"><span data-stu-id="172d3-223">Add support for calling extension methods in LINQ lambdas.</span></span> <span data-ttu-id="172d3-224">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="172d3-224">For EF Core: 3.</span></span>

[<span data-ttu-id="172d3-225">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="172d3-225">GitHub repository</span></span>](https://github.com/ClaveConsulting/Expressionify)

### <a name="xlinq"></a><span data-ttu-id="172d3-226">XLinq</span><span class="sxs-lookup"><span data-stu-id="172d3-226">XLinq</span></span>

<span data-ttu-id="172d3-227">關聯式資料的 Language-integrated Query (LINQ) 技術。</span><span class="sxs-lookup"><span data-stu-id="172d3-227">Language Integrated Query (LINQ) technology for relational databases.</span></span> <span data-ttu-id="172d3-228">可供使用 C# 來撰寫強型別查詢。</span><span class="sxs-lookup"><span data-stu-id="172d3-228">It allows you to use C# to write strongly typed queries.</span></span> <span data-ttu-id="172d3-229">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="172d3-229">For EF Core: 3.</span></span>

- <span data-ttu-id="172d3-230">查詢建立的完整 C# 支援：Lambda 內部的多個陳述式、變數、函式等。</span><span class="sxs-lookup"><span data-stu-id="172d3-230">Full C# support for query creation: multiple statements inside lambda, variables, functions, etc.</span></span>
- <span data-ttu-id="172d3-231">與 SQL 沒有語意隔閡。</span><span class="sxs-lookup"><span data-stu-id="172d3-231">No semantic gap with SQL.</span></span> <span data-ttu-id="172d3-232">XLinq 會將 SQL 陳述式 (例如 `SELECT`、`FROM`、`WHERE`) 宣告為第一級 C# 方法，以結合熟悉的語法與 IntelliSense、型別安全和重構。</span><span class="sxs-lookup"><span data-stu-id="172d3-232">XLinq declares SQL statements (like `SELECT`, `FROM`, `WHERE`) as first class C# methods, combining familiar syntax with intellisense, type safety and refactoring.</span></span>

<span data-ttu-id="172d3-233">因此，SQL 會變成在本機公開其 API 的「另一個」類別庫，實際上就是「整合語言的 SQL」  。</span><span class="sxs-lookup"><span data-stu-id="172d3-233">As a result SQL becomes just "another" class library exposing its API locally, literally *"Language Integrated SQL"*.</span></span>

[<span data-ttu-id="172d3-234">網站</span><span class="sxs-lookup"><span data-stu-id="172d3-234">Website</span></span>](http://xlinq.live/)

### <a name="ramses"></a><span data-ttu-id="172d3-235">Ramses</span><span class="sxs-lookup"><span data-stu-id="172d3-235">Ramses</span></span>

<span data-ttu-id="172d3-236">生命週期勾點 (呼叫 SaveChanges)。</span><span class="sxs-lookup"><span data-stu-id="172d3-236">Lifecycle hooks (for SaveChanges).</span></span> <span data-ttu-id="172d3-237">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="172d3-237">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="172d3-238">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="172d3-238">GitHub repository</span></span>](https://github.com/JValck/Ramses)

### <a name="efcorenamingconventions"></a><span data-ttu-id="172d3-239">EFCore.NamingConventions</span><span class="sxs-lookup"><span data-stu-id="172d3-239">EFCore.NamingConventions</span></span>

<span data-ttu-id="172d3-240">這會自動使所有資料表與資料行名稱採用全為大寫或小寫字母，並以底線作為空格的格式。</span><span class="sxs-lookup"><span data-stu-id="172d3-240">This will automatically make all your table and column names have snake_case, all UPPER or all lower case naming.</span></span> <span data-ttu-id="172d3-241">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="172d3-241">For EF Core: 3.</span></span>

[<span data-ttu-id="172d3-242">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="172d3-242">GitHub repository</span></span>](https://github.com/efcore/EFCore.NamingConventions)

### <a name="simplersoftwareentityframeworkcoresqlservernodatime"></a><span data-ttu-id="172d3-243">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span><span class="sxs-lookup"><span data-stu-id="172d3-243">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span></span>

<span data-ttu-id="172d3-244">針對 NodaTime 類型，將原生支援新增至 SQL Server 的 EntityFrameworkCore。</span><span class="sxs-lookup"><span data-stu-id="172d3-244">Adds native support to EntityFrameworkCore for SQL Server for the NodaTime types.</span></span> <span data-ttu-id="172d3-245">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="172d3-245">For EF Core: 3.</span></span>

[<span data-ttu-id="172d3-246">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="172d3-246">GitHub repository</span></span>](https://github.com/StevenRasmussen/EFCore.SqlServer.NodaTime)

### <a name="dabbleentityframeworkcoretemporalquery"></a><span data-ttu-id="172d3-247">Dabble.EntityFrameworkCore.Temporal.Query</span><span class="sxs-lookup"><span data-stu-id="172d3-247">Dabble.EntityFrameworkCore.Temporal.Query</span></span>

<span data-ttu-id="172d3-248">Entity Framework Core 3.1 的 LINQ 延伸模組，以支援 Microsoft SQL Server 時態表查詢。</span><span class="sxs-lookup"><span data-stu-id="172d3-248">LINQ extensions to Entity Framework Core 3.1 to support Microsoft SQL Server Temporal Table Querying.</span></span> <span data-ttu-id="172d3-249">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="172d3-249">For EF Core: 3.</span></span>

[<span data-ttu-id="172d3-250">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="172d3-250">GitHub repository</span></span>](https://github.com/Adam-Langley/efcore-temporal-query)

### <a name="entityframeworkcoresqlserverhierarchyid"></a><span data-ttu-id="172d3-251">EntityFrameworkCore.SqlServer.HierarchyId</span><span class="sxs-lookup"><span data-stu-id="172d3-251">EntityFrameworkCore.SqlServer.HierarchyId</span></span>

<span data-ttu-id="172d3-252">為 SQL Server EF Core 提供者新增 hierarchyid 支援。</span><span class="sxs-lookup"><span data-stu-id="172d3-252">Adds hierarchyid support to the SQL Server EF Core provider.</span></span> <span data-ttu-id="172d3-253">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="172d3-253">For EF Core: 3.</span></span>

[<span data-ttu-id="172d3-254">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="172d3-254">GitHub repository</span></span>](https://github.com/efcore/EFCore.SqlServer.HierarchyId)

### <a name="linq2dbentityframeworkcore"></a><span data-ttu-id="172d3-255">linq2db.Microsoft.entityframeworkcore</span><span class="sxs-lookup"><span data-stu-id="172d3-255">linq2db.EntityFrameworkCore</span></span>

<span data-ttu-id="172d3-256">將 LINQ 查詢轉換為 SQL 運算式的替代方法。</span><span class="sxs-lookup"><span data-stu-id="172d3-256">Alternative translator of LINQ queries to SQL expressions.</span></span> <span data-ttu-id="172d3-257">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="172d3-257">For EF Core: 3.</span></span>

<span data-ttu-id="172d3-258">包含支援先進的 SQL 功能，例如 Cte、大量複製、資料表提示、視窗型函數、臨時表，以及資料庫端的建立/更新/刪除作業。</span><span class="sxs-lookup"><span data-stu-id="172d3-258">Includes support for advanced SQL features such as CTEs, bulk copy, table hints, windowed functions, temporary tables, and database-side create/update/delete operations.</span></span>

[<span data-ttu-id="172d3-259">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="172d3-259">GitHub repository</span></span>](https://github.com/linq2db/linq2db.EntityFrameworkCore)
