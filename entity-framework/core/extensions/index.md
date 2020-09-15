---
title: 工具和延伸模組 - EF Core
description: Entity Framework Core 的外部工具與延伸模組
author: ErikEJ
ms.date: 04/11/2020
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
uid: core/extensions/index
ms.openlocfilehash: e8198e0b0a51968336be97ea7d7a4b65e856d32f
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618016"
---
# <a name="ef-core-tools--extensions"></a><span data-ttu-id="74d19-103">EF Core 工具和延伸模組</span><span class="sxs-lookup"><span data-stu-id="74d19-103">EF Core Tools & Extensions</span></span>

<span data-ttu-id="74d19-104">這些工具和延伸模組可為 Entity Framework Core 2.1 及更新版本提供額外的功能。</span><span class="sxs-lookup"><span data-stu-id="74d19-104">These tools and extensions provide additional functionality for Entity Framework Core 2.1 and later.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="74d19-105">延伸模組以各種來源建置而成，且不屬於 Entity Framework Core 專案維護的一部份。</span><span class="sxs-lookup"><span data-stu-id="74d19-105">Extensions are built by a variety of sources and aren't maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="74d19-106">考慮使用協力廠商延伸模組時，請務必評估其品質、授權、相容性、支援等，確保其符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="74d19-106">When considering a third party extension, be sure to evaluate its quality, licensing, compatibility, support, etc. to ensure it meets your requirements.</span></span> <span data-ttu-id="74d19-107">特別是針對舊版 EF Core 所建置的延伸模組，可能需要予以更新，才能搭配最新版本使用。</span><span class="sxs-lookup"><span data-stu-id="74d19-107">In particular, an extension built for an older version of EF Core may need updating before it will work with the latest versions.</span></span>

## <a name="tools"></a><span data-ttu-id="74d19-108">工具</span><span class="sxs-lookup"><span data-stu-id="74d19-108">Tools</span></span>

### <a name="llblgen-pro"></a><span data-ttu-id="74d19-109">LLBLGen Pro</span><span class="sxs-lookup"><span data-stu-id="74d19-109">LLBLGen Pro</span></span>

<span data-ttu-id="74d19-110">LLBLGen Pro 是實體模型化解決方案，具備 Entity Framework 與 Entity Framework Core 的支援。</span><span class="sxs-lookup"><span data-stu-id="74d19-110">LLBLGen Pro is an entity modeling solution with support for Entity Framework and Entity Framework Core.</span></span> <span data-ttu-id="74d19-111">您可加以利用來輕鬆定義實體模型，並將其對應至資料庫，優先使用資料庫或模型後，即可開始直接撰寫查詢。</span><span class="sxs-lookup"><span data-stu-id="74d19-111">It lets you easily define your entity model and map it to your database, using database first or model first, so you can get started writing queries right away.</span></span> <span data-ttu-id="74d19-112">適用於 EF Core：2、3</span><span class="sxs-lookup"><span data-stu-id="74d19-112">For EF Core: 2, 3</span></span>

[<span data-ttu-id="74d19-113">網站</span><span class="sxs-lookup"><span data-stu-id="74d19-113">Website</span></span>](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a><span data-ttu-id="74d19-114">Devart Entity Developer</span><span class="sxs-lookup"><span data-stu-id="74d19-114">Devart Entity Developer</span></span>

<span data-ttu-id="74d19-115">Devart Entity 是功能強大的 ORM 設計工具，適用於 ADO.NET Entity Framework、NHibernate、LinqConnect、Telerik Data Access、LINQ 到 SQL。</span><span class="sxs-lookup"><span data-stu-id="74d19-115">Entity Developer is a powerful ORM designer for ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access, and LINQ to SQL.</span></span> <span data-ttu-id="74d19-116">它支援使用 model first 或 database first 方法，以及 C# 或 Visual Basic 程式碼產生，以視覺化的方式設計 EF Core 模型。</span><span class="sxs-lookup"><span data-stu-id="74d19-116">It supports designing EF Core models visually, using model first or database first approaches, and C# or Visual Basic code generation.</span></span> <span data-ttu-id="74d19-117">適用於 EF Core：1、2、3、5。</span><span class="sxs-lookup"><span data-stu-id="74d19-117">For EF Core: 1, 2, 3, 5.</span></span>

[<span data-ttu-id="74d19-118">網站</span><span class="sxs-lookup"><span data-stu-id="74d19-118">Website</span></span>](https://www.devart.com/entitydeveloper/)

### <a name="nhydrate-orm-for-entity-framework"></a><span data-ttu-id="74d19-119">Entity Framework 的 nHydrate ORM</span><span class="sxs-lookup"><span data-stu-id="74d19-119">nHydrate ORM for Entity Framework</span></span>

<span data-ttu-id="74d19-120">為 Entity Framework 建立可延伸類別的強型別 ORM。</span><span class="sxs-lookup"><span data-stu-id="74d19-120">An ORM that creates strongly-typed, extendable classes for Entity Framework.</span></span> <span data-ttu-id="74d19-121">產生的程式碼為 Entity Framework Core。</span><span class="sxs-lookup"><span data-stu-id="74d19-121">The generated code is Entity Framework Core.</span></span> <span data-ttu-id="74d19-122">因此兩者沒有任何不同。</span><span class="sxs-lookup"><span data-stu-id="74d19-122">There is no difference.</span></span> <span data-ttu-id="74d19-123">這不能取代 EF 或自訂 ORM。</span><span class="sxs-lookup"><span data-stu-id="74d19-123">This is not a replacement for EF or a custom ORM.</span></span> <span data-ttu-id="74d19-124">這是一種視覺化的模型化層，可讓小組管理複雜的資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="74d19-124">It is a visual, modeling layer that allows a team to manage complex database schemas.</span></span> <span data-ttu-id="74d19-125">適用於 Git 這類的 SCM 軟體，允許多使用者存取模型，但具有最少的衝突。</span><span class="sxs-lookup"><span data-stu-id="74d19-125">It works well with SCM software like Git, allowing multi-user access to your model with minimal conflicts.</span></span> <span data-ttu-id="74d19-126">安裝程式會追蹤模型變更並建立升級指令碼。</span><span class="sxs-lookup"><span data-stu-id="74d19-126">The installer tracks model changes and creates upgrade scripts.</span></span> <span data-ttu-id="74d19-127">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="74d19-127">For EF Core: 3.</span></span>

[<span data-ttu-id="74d19-128">Github 網站</span><span class="sxs-lookup"><span data-stu-id="74d19-128">Github site</span></span>](https://github.com/nHydrate/nHydrate)

### <a name="ef-core-power-tools"></a><span data-ttu-id="74d19-129">EF Core Power Tools</span><span class="sxs-lookup"><span data-stu-id="74d19-129">EF Core Power Tools</span></span>

<span data-ttu-id="74d19-130">EF Core Power Tools 是 Visual Studio 延伸模組，在簡單使用者介面中公開各種設計階段工作。</span><span class="sxs-lookup"><span data-stu-id="74d19-130">EF Core Power Tools is a Visual Studio extension that exposes various EF Core design-time tasks in a simple user interface.</span></span> <span data-ttu-id="74d19-131">包括現有資料庫和 [SQL Server DACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications) 之 DbContext 及實體類別的反向工程、資料庫移轉的管理，以及模型視覺效果。</span><span class="sxs-lookup"><span data-stu-id="74d19-131">It includes reverse engineering of DbContext and entity classes from existing databases and [SQL Server DACPACs](/sql/relational-databases/data-tier-applications/data-tier-applications), management of database migrations, and model visualizations.</span></span> <span data-ttu-id="74d19-132">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="74d19-132">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="74d19-133">GitHub Wiki</span><span class="sxs-lookup"><span data-stu-id="74d19-133">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a><span data-ttu-id="74d19-134">Entity Framework 視覺效果編輯器</span><span class="sxs-lookup"><span data-stu-id="74d19-134">Entity Framework Visual Editor</span></span>

<span data-ttu-id="74d19-135">Entity Framework 視覺效果編輯器是 Visual Studio 延伸模組，為 EF 6 和 EF Core 類別的視覺效果設計新增 ORM 設計工具。</span><span class="sxs-lookup"><span data-stu-id="74d19-135">Entity Framework Visual Editor is a Visual Studio extension that adds an ORM designer for visual design of EF 6, and EF Core classes.</span></span> <span data-ttu-id="74d19-136">因為程式碼會透過 T4 範本產生，所以能加以自訂來滿足各種需求。</span><span class="sxs-lookup"><span data-stu-id="74d19-136">Code is generated using T4 templates so can be customized to suit any needs.</span></span> <span data-ttu-id="74d19-137">它支援繼承、單向及雙向關聯、列舉，還能夠對類別進行色彩編碼以及新增文字區塊，以說明設計中可能較不易懂的部分。</span><span class="sxs-lookup"><span data-stu-id="74d19-137">It supports inheritance, unidirectional and bidirectional associations, enumerations, and the ability to color-code your classes and add text blocks to explain potentially arcane parts of your design.</span></span> <span data-ttu-id="74d19-138">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="74d19-138">For EF Core: 2.</span></span>

<span data-ttu-id="74d19-139">[Marketplace](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="74d19-139">[Marketplace](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)</span></span>

### <a name="catfactory"></a><span data-ttu-id="74d19-140">CatFactory</span><span class="sxs-lookup"><span data-stu-id="74d19-140">CatFactory</span></span>

<span data-ttu-id="74d19-141">CatFactory 是 .NET Core 的 Scaffolding 引擎，可以自動從 SQL Server 資料庫產生 DbContext 類別、實體、對應組態及存放庫類別。</span><span class="sxs-lookup"><span data-stu-id="74d19-141">CatFactory is a scaffolding engine for .NET Core that can automate the generation of DbContext classes, entities, mapping configurations, and repository classes from a SQL Server database.</span></span> <span data-ttu-id="74d19-142">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="74d19-142">For EF Core: 2.</span></span>

[<span data-ttu-id="74d19-143">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="74d19-143">GitHub repository</span></span>](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a><span data-ttu-id="74d19-144">LoreSoft 的 Entity Framework Core Generator</span><span class="sxs-lookup"><span data-stu-id="74d19-144">LoreSoft's Entity Framework Core Generator</span></span>

<span data-ttu-id="74d19-145">Entity Framework Core 產生器 (efg) 是 .NET Core CLI 工具，可以從現有資料庫產生 EF Core 模型，與 `dotnet ef dbcontext scaffold` 十分相似，但它也透過區域取代或剖析對應檔案，支援安全的程式碼[重新產生](https://efg.loresoft.com/en/latest/regeneration/)。</span><span class="sxs-lookup"><span data-stu-id="74d19-145">Entity Framework Core Generator (efg) is a .NET Core CLI tool that can generate EF Core models from an existing database, much like `dotnet ef dbcontext scaffold`, but it also supports safe code [regeneration](https://efg.loresoft.com/en/latest/regeneration/) via region replacement or by parsing mapping files.</span></span> <span data-ttu-id="74d19-146">這項工具也支援產生檢視模型、驗證及物件對應程式程式碼。</span><span class="sxs-lookup"><span data-stu-id="74d19-146">This tool supports generating view models, validation, and object mapper code.</span></span> <span data-ttu-id="74d19-147">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="74d19-147">For EF Core: 2.</span></span>

<span data-ttu-id="74d19-148">[教學課程](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[文件](https://efg.loresoft.com/en/latest/)</span><span class="sxs-lookup"><span data-stu-id="74d19-148">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span></span>

## <a name="extensions"></a><span data-ttu-id="74d19-149">延伸模組</span><span class="sxs-lookup"><span data-stu-id="74d19-149">Extensions</span></span>

### <a name="microsoftentityframeworkcoreautohistory"></a><span data-ttu-id="74d19-150">Microsoft.EntityFrameworkCore.AutoHistory</span><span class="sxs-lookup"><span data-stu-id="74d19-150">Microsoft.EntityFrameworkCore.AutoHistory</span></span>

<span data-ttu-id="74d19-151">外掛程式庫，能夠自動將 EF Core 執行的資料變更記錄到記錄資料表中。</span><span class="sxs-lookup"><span data-stu-id="74d19-151">A plugin library that enables automatically recording the data changes performed by EF Core into a history table.</span></span> <span data-ttu-id="74d19-152">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="74d19-152">For EF Core: 2.</span></span>

[<span data-ttu-id="74d19-153">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="74d19-153">GitHub repository</span></span>](https://github.com/Arch/AutoHistory/)

### <a name="efcoresecondlevelcacheinterceptor"></a><span data-ttu-id="74d19-154">EFCoreSecondLevelCacheInterceptor</span><span class="sxs-lookup"><span data-stu-id="74d19-154">EFCoreSecondLevelCacheInterceptor</span></span>

<span data-ttu-id="74d19-155">第二個層級快取是查詢快取。</span><span class="sxs-lookup"><span data-stu-id="74d19-155">Second level caching is a query cache.</span></span> <span data-ttu-id="74d19-156">EF 命令的結果會存放在快取中，使相同的 EF 命令會從快取擷取其資料，而不必再次向資料庫再次執行命令。</span><span class="sxs-lookup"><span data-stu-id="74d19-156">The results of EF commands will be stored in the cache, so that the same EF commands will retrieve their data from the cache rather than executing them against the database again.</span></span> <span data-ttu-id="74d19-157">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="74d19-157">For EF Core: 3.</span></span>

[<span data-ttu-id="74d19-158">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="74d19-158">GitHub repository</span></span>](https://github.com/VahidN/EFCoreSecondLevelCacheInterceptor)

### <a name="geco"></a><span data-ttu-id="74d19-159">Geco</span><span class="sxs-lookup"><span data-stu-id="74d19-159">Geco</span></span>

<span data-ttu-id="74d19-160">Geco (產生器主控台) 是以主控台專案為基礎的簡單程式碼產生器，在 .NET Core 上執行，並使用插入 C# 的字串產生程式碼。</span><span class="sxs-lookup"><span data-stu-id="74d19-160">Geco (Generator Console) is a simple code generator based on a console project, that runs on .NET Core and uses C# interpolated strings for code generation.</span></span> <span data-ttu-id="74d19-161">Geco 包括適用於 EF Core 的反向模型產生器，支援複數、單數和可編輯範本。</span><span class="sxs-lookup"><span data-stu-id="74d19-161">Geco includes a reverse model generator for EF Core with support for pluralization, singularization, and editable templates.</span></span> <span data-ttu-id="74d19-162">它也提供種子資料指令碼產生器、指令碼執行器和資料庫清除工具。</span><span class="sxs-lookup"><span data-stu-id="74d19-162">It also provides a seed data script generator, a script runner, and a database cleaner.</span></span> <span data-ttu-id="74d19-163">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="74d19-163">For EF Core: 2.</span></span>

[<span data-ttu-id="74d19-164">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="74d19-164">GitHub repository</span></span>](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a><span data-ttu-id="74d19-165">EntityFrameworkCore.Scaffolding.Handlebars</span><span class="sxs-lookup"><span data-stu-id="74d19-165">EntityFrameworkCore.Scaffolding.Handlebars</span></span> 

<span data-ttu-id="74d19-166">可讓您搭配 Handlebars 範本使用 Entity Framework Core 工具鏈，自訂從現有資料庫進行反向工程的類別。</span><span class="sxs-lookup"><span data-stu-id="74d19-166">Allows customization of classes reverse engineered from an existing database using the Entity Framework Core toolchain with Handlebars templates.</span></span> <span data-ttu-id="74d19-167">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="74d19-167">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="74d19-168">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="74d19-168">GitHub repository</span></span>](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a><span data-ttu-id="74d19-169">NeinLinq.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="74d19-169">NeinLinq.EntityFrameworkCore</span></span> 

<span data-ttu-id="74d19-170">NeinLinq 擴充了 LINQ 提供者 (例如 Entity Framework)，並提供以下功能：重複使用函式、重寫查詢，以及使用可翻譯的述詞和選取器建置動態查詢。</span><span class="sxs-lookup"><span data-stu-id="74d19-170">NeinLinq extends LINQ providers such as Entity Framework to enable reusing functions, rewriting queries, and building dynamic queries using translatable predicates and selectors.</span></span> <span data-ttu-id="74d19-171">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="74d19-171">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="74d19-172">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="74d19-172">GitHub repository</span></span>](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a><span data-ttu-id="74d19-173">Microsoft.EntityFrameworkCore.UnitOfWork</span><span class="sxs-lookup"><span data-stu-id="74d19-173">Microsoft.EntityFrameworkCore.UnitOfWork</span></span>

<span data-ttu-id="74d19-174">Microsoft.EntityFrameworkCore 的外掛程式，可支援存放庫、工作模式的單位，以及支援分散式交易的多個資料庫。</span><span class="sxs-lookup"><span data-stu-id="74d19-174">A plugin for Microsoft.EntityFrameworkCore to support repository, unit of work patterns, and multiple databases with distributed transaction supported.</span></span> <span data-ttu-id="74d19-175">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="74d19-175">For EF Core: 2.</span></span>

[<span data-ttu-id="74d19-176">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="74d19-176">GitHub repository</span></span>](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a><span data-ttu-id="74d19-177">EFCore.BulkExtensions</span><span class="sxs-lookup"><span data-stu-id="74d19-177">EFCore.BulkExtensions</span></span>

<span data-ttu-id="74d19-178">適用於大量作業 (插入、更新、刪除) 的 EF Core 延伸模組。</span><span class="sxs-lookup"><span data-stu-id="74d19-178">EF Core extensions for Bulk operations (Insert, Update, Delete).</span></span> <span data-ttu-id="74d19-179">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="74d19-179">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="74d19-180">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="74d19-180">GitHub repository</span></span>](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a><span data-ttu-id="74d19-181">Bricelam.EntityFrameworkCore.Pluralizer</span><span class="sxs-lookup"><span data-stu-id="74d19-181">Bricelam.EntityFrameworkCore.Pluralizer</span></span>

<span data-ttu-id="74d19-182">新增設計階段複數表示。</span><span class="sxs-lookup"><span data-stu-id="74d19-182">Adds design-time pluralization.</span></span> <span data-ttu-id="74d19-183">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="74d19-183">For EF Core: 2.</span></span>

[<span data-ttu-id="74d19-184">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="74d19-184">GitHub repository</span></span>](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a><span data-ttu-id="74d19-185">Toolbelt.EntityFrameworkCore.IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="74d19-185">Toolbelt.EntityFrameworkCore.IndexAttribute</span></span>

<span data-ttu-id="74d19-186">[Index] 屬性的復興 (附有模型組建的延伸模組)。</span><span class="sxs-lookup"><span data-stu-id="74d19-186">Revival of [Index] attribute (with extension for model building).</span></span> <span data-ttu-id="74d19-187">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="74d19-187">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="74d19-188">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="74d19-188">GitHub repository</span></span>](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a><span data-ttu-id="74d19-189">EfCore.InMemoryHelpers</span><span class="sxs-lookup"><span data-stu-id="74d19-189">EfCore.InMemoryHelpers</span></span>

<span data-ttu-id="74d19-190">提供 EF Core 記憶體內部資料庫提供者的包裝函式。</span><span class="sxs-lookup"><span data-stu-id="74d19-190">Provides a wrapper around the EF Core In-Memory Database Provider.</span></span> <span data-ttu-id="74d19-191">使它的運作更接近關聯式提供者。</span><span class="sxs-lookup"><span data-stu-id="74d19-191">Makes it act more like a relational provider.</span></span> <span data-ttu-id="74d19-192">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="74d19-192">For EF Core: 2.</span></span>

[<span data-ttu-id="74d19-193">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="74d19-193">GitHub repository</span></span>](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a><span data-ttu-id="74d19-194">EFCore.TemporalSupport</span><span class="sxs-lookup"><span data-stu-id="74d19-194">EFCore.TemporalSupport</span></span>

<span data-ttu-id="74d19-195">時態性支援的實作。</span><span class="sxs-lookup"><span data-stu-id="74d19-195">An implementation of temporal support.</span></span> <span data-ttu-id="74d19-196">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="74d19-196">For EF Core: 2.</span></span>

[<span data-ttu-id="74d19-197">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="74d19-197">GitHub repository</span></span>](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a><span data-ttu-id="74d19-198">EfCoreTemporalTable</span><span class="sxs-lookup"><span data-stu-id="74d19-198">EfCoreTemporalTable</span></span>

<span data-ttu-id="74d19-199">使用下列引進的擴充方法，輕鬆地在慣用的資料庫上執行時態性查詢：`AsTemporalAll()`、`AsTemporalAsOf(date)`、`AsTemporalFrom(startDate, endDate)`、`AsTemporalBetween(startDate, endDate)`、`AsTemporalContained(startDate, endDate)`。</span><span class="sxs-lookup"><span data-stu-id="74d19-199">Easily perform temporal queries on your favourite database using introduced extension methods: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span></span> <span data-ttu-id="74d19-200">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="74d19-200">For EF Core: 3.</span></span>

[<span data-ttu-id="74d19-201">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="74d19-201">GitHub repository</span></span>](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="efcoretimetraveler"></a><span data-ttu-id="74d19-202">EFCore.TimeTraveler</span><span class="sxs-lookup"><span data-stu-id="74d19-202">EFCore.TimeTraveler</span></span>

<span data-ttu-id="74d19-203">允許使用已定義的 EF Core 程式碼、實體和對應，對 [SQL Server 時態性記錄](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel)進行完整功能的 Entity Framework Core 查詢。</span><span class="sxs-lookup"><span data-stu-id="74d19-203">Allow full-featured Entity Framework Core queries against [SQL Server Temporal History](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel) using the EF Core code, entities, and mappings you already have defined.</span></span>  <span data-ttu-id="74d19-204">藉由將程式碼包裝在 `using (TemporalQuery.AsOf(targetDateTime)) {...}` 中來移動時間。</span><span class="sxs-lookup"><span data-stu-id="74d19-204">Travel through time by wrapping your code in `using (TemporalQuery.AsOf(targetDateTime)) {...}`.</span></span> <span data-ttu-id="74d19-205">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="74d19-205">For EF Core: 3.</span></span>

[<span data-ttu-id="74d19-206">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="74d19-206">GitHub repository</span></span>](https://github.com/VantageSoftware/EFCore.TimeTraveler)


### <a name="entityframeworkcoretemporaltables"></a><span data-ttu-id="74d19-207">EntityFrameworkCore.TemporalTables</span><span class="sxs-lookup"><span data-stu-id="74d19-207">EntityFrameworkCore.TemporalTables</span></span>

<span data-ttu-id="74d19-208">Entity Framework Core 的延伸模組程式庫，可讓使用 SQL Server 的開發人員輕鬆地使用時態表。</span><span class="sxs-lookup"><span data-stu-id="74d19-208">Extension library for Entity Framework Core which allows developers who use SQL Server to easily use temporal tables.</span></span> <span data-ttu-id="74d19-209">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="74d19-209">For EF Core: 2.</span></span>

[<span data-ttu-id="74d19-210">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="74d19-210">GitHub repository</span></span>](https://github.com/findulov/EntityFrameworkCore.TemporalTables)


### <a name="entityframeworkcorecacheable"></a><span data-ttu-id="74d19-211">EntityFrameworkCore.Cacheable</span><span class="sxs-lookup"><span data-stu-id="74d19-211">EntityFrameworkCore.Cacheable</span></span>

<span data-ttu-id="74d19-212">高效能第二層查詢快取。</span><span class="sxs-lookup"><span data-stu-id="74d19-212">A high-performance second-level query cache.</span></span> <span data-ttu-id="74d19-213">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="74d19-213">For EF Core: 2.</span></span>

[<span data-ttu-id="74d19-214">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="74d19-214">GitHub repository</span></span>](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entityframeworkcorencache"></a><span data-ttu-id="74d19-215">EntityFrameworkCore.NCache</span><span class="sxs-lookup"><span data-stu-id="74d19-215">EntityFrameworkCore.NCache</span></span>

<span data-ttu-id="74d19-216">NCache Entity Framework Core Provider 是快取查詢結果的分散式第二層級快取提供者。</span><span class="sxs-lookup"><span data-stu-id="74d19-216">NCache Entity Framework Core Provider is a distributed second level cache provider for caching query results.</span></span> <span data-ttu-id="74d19-217">NCache 的分散式架構使其更具可擴縮性與高可用性。</span><span class="sxs-lookup"><span data-stu-id="74d19-217">The distributed architecture of NCache makes it more scalable and highly available.</span></span> <span data-ttu-id="74d19-218">適用於 EF Core 2。</span><span class="sxs-lookup"><span data-stu-id="74d19-218">For EF Core 2.</span></span>

[<span data-ttu-id="74d19-219">網站</span><span class="sxs-lookup"><span data-stu-id="74d19-219">Website</span></span>](https://www.alachisoft.com/ncache/ef-core-cache.html)

### <a name="entity-framework-plus"></a><span data-ttu-id="74d19-220">Entity Framework Plus</span><span class="sxs-lookup"><span data-stu-id="74d19-220">Entity Framework Plus</span></span>

<span data-ttu-id="74d19-221">使用下功能延伸您的 DbContext：包括篩選、稽核、快取、查詢未來、批次刪除、批次更新等。</span><span class="sxs-lookup"><span data-stu-id="74d19-221">Extends your DbContext with features such as: Include Filter, Auditing, Caching, Query Future, Batch Delete, Batch Update, and more.</span></span> <span data-ttu-id="74d19-222">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="74d19-222">For EF Core: 2, 3.</span></span>

<span data-ttu-id="74d19-223">[網站](https://entityframework-plus.net/)
[GitHub 存放庫](https://github.com/zzzprojects/EntityFramework-Plus)</span><span class="sxs-lookup"><span data-stu-id="74d19-223">[Website](https://entityframework-plus.net/)
[GitHub repository](https://github.com/zzzprojects/EntityFramework-Plus)</span></span>

### <a name="entity-framework-extensions"></a><span data-ttu-id="74d19-224">Entity Framework 擴充功能</span><span class="sxs-lookup"><span data-stu-id="74d19-224">Entity Framework Extensions</span></span>

<span data-ttu-id="74d19-225">使用高效能批次作業延伸您的 DbContext：BulkSaveChanges、BulkInsert、BulkUpdate、BulkDelete、BulkMerge 等。</span><span class="sxs-lookup"><span data-stu-id="74d19-225">Extends your DbContext with high-performance bulk operations: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge, and more.</span></span> <span data-ttu-id="74d19-226">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="74d19-226">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="74d19-227">網站</span><span class="sxs-lookup"><span data-stu-id="74d19-227">Website</span></span>](https://entityframework-extensions.net/)

### <a name="expressionify"></a><span data-ttu-id="74d19-228">Expressionify</span><span class="sxs-lookup"><span data-stu-id="74d19-228">Expressionify</span></span>

<span data-ttu-id="74d19-229">新增在 LINQ Lambda 中呼叫擴充方法的支援。</span><span class="sxs-lookup"><span data-stu-id="74d19-229">Add support for calling extension methods in LINQ lambdas.</span></span> <span data-ttu-id="74d19-230">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="74d19-230">For EF Core: 3.</span></span>

[<span data-ttu-id="74d19-231">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="74d19-231">GitHub repository</span></span>](https://github.com/ClaveConsulting/Expressionify)

### <a name="xlinq"></a><span data-ttu-id="74d19-232">XLinq</span><span class="sxs-lookup"><span data-stu-id="74d19-232">XLinq</span></span>

<span data-ttu-id="74d19-233">關聯式資料的 Language-integrated Query (LINQ) 技術。</span><span class="sxs-lookup"><span data-stu-id="74d19-233">Language Integrated Query (LINQ) technology for relational databases.</span></span> <span data-ttu-id="74d19-234">可供使用 C# 來撰寫強型別查詢。</span><span class="sxs-lookup"><span data-stu-id="74d19-234">It allows you to use C# to write strongly typed queries.</span></span> <span data-ttu-id="74d19-235">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="74d19-235">For EF Core: 3.</span></span>

- <span data-ttu-id="74d19-236">查詢建立的完整 C# 支援：Lambda 內部的多個陳述式、變數、函式等。</span><span class="sxs-lookup"><span data-stu-id="74d19-236">Full C# support for query creation: multiple statements inside lambda, variables, functions, etc.</span></span>
- <span data-ttu-id="74d19-237">與 SQL 沒有語意隔閡。</span><span class="sxs-lookup"><span data-stu-id="74d19-237">No semantic gap with SQL.</span></span> <span data-ttu-id="74d19-238">XLinq 會將 SQL 陳述式 (例如 `SELECT`、`FROM`、`WHERE`) 宣告為第一級 C# 方法，以結合熟悉的語法與 IntelliSense、型別安全和重構。</span><span class="sxs-lookup"><span data-stu-id="74d19-238">XLinq declares SQL statements (like `SELECT`, `FROM`, `WHERE`) as first class C# methods, combining familiar syntax with intellisense, type safety and refactoring.</span></span>

<span data-ttu-id="74d19-239">因此，SQL 會變成在本機公開其 API 的「另一個」類別庫，實際上就是「整合語言的 SQL」  。</span><span class="sxs-lookup"><span data-stu-id="74d19-239">As a result SQL becomes just "another" class library exposing its API locally, literally *"Language Integrated SQL"*.</span></span>

[<span data-ttu-id="74d19-240">網站</span><span class="sxs-lookup"><span data-stu-id="74d19-240">Website</span></span>](http://xlinq.live/)

### <a name="ramses"></a><span data-ttu-id="74d19-241">Ramses</span><span class="sxs-lookup"><span data-stu-id="74d19-241">Ramses</span></span>

<span data-ttu-id="74d19-242">生命週期勾點 (呼叫 SaveChanges)。</span><span class="sxs-lookup"><span data-stu-id="74d19-242">Lifecycle hooks (for SaveChanges).</span></span> <span data-ttu-id="74d19-243">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="74d19-243">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="74d19-244">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="74d19-244">GitHub repository</span></span>](https://github.com/JValck/Ramses)

### <a name="efcorenamingconventions"></a><span data-ttu-id="74d19-245">EFCore.NamingConventions</span><span class="sxs-lookup"><span data-stu-id="74d19-245">EFCore.NamingConventions</span></span>

<span data-ttu-id="74d19-246">這會自動使所有資料表與資料行名稱採用全為大寫或小寫字母，並以底線作為空格的格式。</span><span class="sxs-lookup"><span data-stu-id="74d19-246">This will automatically make all your table and column names have snake_case, all UPPER or all lower case naming.</span></span> <span data-ttu-id="74d19-247">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="74d19-247">For EF Core: 3.</span></span>

[<span data-ttu-id="74d19-248">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="74d19-248">GitHub repository</span></span>](https://github.com/efcore/EFCore.NamingConventions)

### <a name="simplersoftwareentityframeworkcoresqlservernodatime"></a><span data-ttu-id="74d19-249">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span><span class="sxs-lookup"><span data-stu-id="74d19-249">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span></span>

<span data-ttu-id="74d19-250">針對 NodaTime 類型，將原生支援新增至 SQL Server 的 EntityFrameworkCore。</span><span class="sxs-lookup"><span data-stu-id="74d19-250">Adds native support to EntityFrameworkCore for SQL Server for the NodaTime types.</span></span> <span data-ttu-id="74d19-251">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="74d19-251">For EF Core: 3.</span></span>

[<span data-ttu-id="74d19-252">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="74d19-252">GitHub repository</span></span>](https://github.com/StevenRasmussen/EFCore.SqlServer.NodaTime)

### <a name="dabbleentityframeworkcoretemporalquery"></a><span data-ttu-id="74d19-253">Dabble.EntityFrameworkCore.Temporal.Query</span><span class="sxs-lookup"><span data-stu-id="74d19-253">Dabble.EntityFrameworkCore.Temporal.Query</span></span>

<span data-ttu-id="74d19-254">Entity Framework Core 3.1 的 LINQ 延伸模組，以支援 Microsoft SQL Server 時態表查詢。</span><span class="sxs-lookup"><span data-stu-id="74d19-254">LINQ extensions to Entity Framework Core 3.1 to support Microsoft SQL Server Temporal Table Querying.</span></span> <span data-ttu-id="74d19-255">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="74d19-255">For EF Core: 3.</span></span>

[<span data-ttu-id="74d19-256">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="74d19-256">GitHub repository</span></span>](https://github.com/Adam-Langley/efcore-temporal-query)

### <a name="entityframeworkcoresqlserverhierarchyid"></a><span data-ttu-id="74d19-257">EntityFrameworkCore.SqlServer.HierarchyId</span><span class="sxs-lookup"><span data-stu-id="74d19-257">EntityFrameworkCore.SqlServer.HierarchyId</span></span>

<span data-ttu-id="74d19-258">為 SQL Server EF Core 提供者新增 hierarchyid 支援。</span><span class="sxs-lookup"><span data-stu-id="74d19-258">Adds hierarchyid support to the SQL Server EF Core provider.</span></span> <span data-ttu-id="74d19-259">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="74d19-259">For EF Core: 3.</span></span>

[<span data-ttu-id="74d19-260">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="74d19-260">GitHub repository</span></span>](https://github.com/efcore/EFCore.SqlServer.HierarchyId)

### <a name="linq2dbentityframeworkcore"></a><span data-ttu-id="74d19-261">linq2db.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="74d19-261">linq2db.EntityFrameworkCore</span></span>

<span data-ttu-id="74d19-262">將 LINQ 查詢轉換為 SQL 運算式的替代轉譯程式。</span><span class="sxs-lookup"><span data-stu-id="74d19-262">Alternative translator of LINQ queries to SQL expressions.</span></span> <span data-ttu-id="74d19-263">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="74d19-263">For EF Core: 3.</span></span>

<span data-ttu-id="74d19-264">包括對進階 SQL 功能的支援，例如 CTE、大量複製、資料表提示、視窗型函式、暫存資料表，以及資料庫端的建立/更新/刪除作業。</span><span class="sxs-lookup"><span data-stu-id="74d19-264">Includes support for advanced SQL features such as CTEs, bulk copy, table hints, windowed functions, temporary tables, and database-side create/update/delete operations.</span></span>

[<span data-ttu-id="74d19-265">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="74d19-265">GitHub repository</span></span>](https://github.com/linq2db/linq2db.EntityFrameworkCore)

### <a name="efcoresoftdelete"></a><span data-ttu-id="74d19-266">EFCore.SoftDelete</span><span class="sxs-lookup"><span data-stu-id="74d19-266">EFCore.SoftDelete</span></span>

<span data-ttu-id="74d19-267">虛刪除實體的實作。</span><span class="sxs-lookup"><span data-stu-id="74d19-267">An implementation for soft deleting entities.</span></span> <span data-ttu-id="74d19-268">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="74d19-268">For EF Core: 3.</span></span>

[<span data-ttu-id="74d19-269">NuGet</span><span class="sxs-lookup"><span data-stu-id="74d19-269">NuGet</span></span>](https://www.nuget.org/packages/EFCore.SoftDelete)
