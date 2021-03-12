---
title: 工具和延伸模組 - EF Core
description: Entity Framework Core 的外部工具與延伸模組
author: ErikEJ
ms.date: 02/21/2021
uid: core/extensions/index
ms.openlocfilehash: 67d078250d8bfdb845cf98949ee1412a8fe842bd
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024377"
---
# <a name="ef-core-tools--extensions"></a><span data-ttu-id="4917c-103">EF Core 工具和延伸模組</span><span class="sxs-lookup"><span data-stu-id="4917c-103">EF Core Tools & Extensions</span></span>

<span data-ttu-id="4917c-104">這些工具和延伸模組可為 Entity Framework Core 2.1 及更新版本提供額外的功能。</span><span class="sxs-lookup"><span data-stu-id="4917c-104">These tools and extensions provide additional functionality for Entity Framework Core 2.1 and later.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4917c-105">延伸模組以各種來源建置而成，且不屬於 Entity Framework Core 專案維護的一部份。</span><span class="sxs-lookup"><span data-stu-id="4917c-105">Extensions are built by a variety of sources and aren't maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="4917c-106">考慮使用協力廠商延伸模組時，請務必評估其品質、授權、相容性、支援等，確保其符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="4917c-106">When considering a third party extension, be sure to evaluate its quality, licensing, compatibility, support, etc. to ensure it meets your requirements.</span></span> <span data-ttu-id="4917c-107">特別是針對舊版 EF Core 所建置的延伸模組，可能需要予以更新，才能搭配最新版本使用。</span><span class="sxs-lookup"><span data-stu-id="4917c-107">In particular, an extension built for an older version of EF Core may need updating before it will work with the latest versions.</span></span>

## <a name="tools"></a><span data-ttu-id="4917c-108">工具</span><span class="sxs-lookup"><span data-stu-id="4917c-108">Tools</span></span>

### <a name="llblgen-pro"></a><span data-ttu-id="4917c-109">LLBLGen Pro</span><span class="sxs-lookup"><span data-stu-id="4917c-109">LLBLGen Pro</span></span>

<span data-ttu-id="4917c-110">LLBLGen Pro 是實體模型化解決方案，具備 Entity Framework 與 Entity Framework Core 的支援。</span><span class="sxs-lookup"><span data-stu-id="4917c-110">LLBLGen Pro is an entity modeling solution with support for Entity Framework and Entity Framework Core.</span></span> <span data-ttu-id="4917c-111">您可加以利用來輕鬆定義實體模型，並將其對應至資料庫，優先使用資料庫或模型後，即可開始直接撰寫查詢。</span><span class="sxs-lookup"><span data-stu-id="4917c-111">It lets you easily define your entity model and map it to your database, using database first or model first, so you can get started writing queries right away.</span></span> <span data-ttu-id="4917c-112">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="4917c-112">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="4917c-113">網站</span><span class="sxs-lookup"><span data-stu-id="4917c-113">Website</span></span>](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a><span data-ttu-id="4917c-114">Devart Entity Developer</span><span class="sxs-lookup"><span data-stu-id="4917c-114">Devart Entity Developer</span></span>

<span data-ttu-id="4917c-115">Entity Developer 是一種功能強大的 O/RM 設計工具，可用於 ADO.NET Entity Framework、NHibernate、LinqConnect、Telerik 資料存取和 LINQ to SQL。</span><span class="sxs-lookup"><span data-stu-id="4917c-115">Entity Developer is a powerful O/RM designer for ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access, and LINQ to SQL.</span></span> <span data-ttu-id="4917c-116">它支援使用 model first 或 database first 方法，以及 C# 或 Visual Basic 程式碼產生，以視覺化的方式設計 EF Core 模型。</span><span class="sxs-lookup"><span data-stu-id="4917c-116">It supports designing EF Core models visually, using model first or database first approaches, and C# or Visual Basic code generation.</span></span> <span data-ttu-id="4917c-117">EF Core：2，3，5。</span><span class="sxs-lookup"><span data-stu-id="4917c-117">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="4917c-118">網站</span><span class="sxs-lookup"><span data-stu-id="4917c-118">Website</span></span>](https://www.devart.com/entitydeveloper/)

### <a name="nhydrate-orm-for-entity-framework"></a><span data-ttu-id="4917c-119">Entity Framework 的 nHydrate ORM</span><span class="sxs-lookup"><span data-stu-id="4917c-119">nHydrate ORM for Entity Framework</span></span>

<span data-ttu-id="4917c-120">為 Entity Framework 建立強型別擴充類別的 O/RM。</span><span class="sxs-lookup"><span data-stu-id="4917c-120">An O/RM that creates strongly-typed, extendable classes for Entity Framework.</span></span> <span data-ttu-id="4917c-121">產生的程式碼為 Entity Framework Core。</span><span class="sxs-lookup"><span data-stu-id="4917c-121">The generated code is Entity Framework Core.</span></span> <span data-ttu-id="4917c-122">因此兩者沒有任何不同。</span><span class="sxs-lookup"><span data-stu-id="4917c-122">There is no difference.</span></span> <span data-ttu-id="4917c-123">這不是 EF 或自訂 O/RM 的替代方案。</span><span class="sxs-lookup"><span data-stu-id="4917c-123">This is not a replacement for EF or a custom O/RM.</span></span> <span data-ttu-id="4917c-124">這是一種視覺化的模型化層，可讓小組管理複雜的資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="4917c-124">It is a visual, modeling layer that allows a team to manage complex database schemas.</span></span> <span data-ttu-id="4917c-125">適用於 Git 這類的 SCM 軟體，允許多使用者存取模型，但具有最少的衝突。</span><span class="sxs-lookup"><span data-stu-id="4917c-125">It works well with SCM software like Git, allowing multi-user access to your model with minimal conflicts.</span></span> <span data-ttu-id="4917c-126">安裝程式會追蹤模型變更並建立升級指令碼。</span><span class="sxs-lookup"><span data-stu-id="4917c-126">The installer tracks model changes and creates upgrade scripts.</span></span> <span data-ttu-id="4917c-127">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="4917c-127">For EF Core: 3.</span></span>

[<span data-ttu-id="4917c-128">Github 儲存機制</span><span class="sxs-lookup"><span data-stu-id="4917c-128">Github repository</span></span>](https://github.com/nHydrate/nHydrate)

### <a name="ef-core-power-tools"></a><span data-ttu-id="4917c-129">EF Core Power Tools</span><span class="sxs-lookup"><span data-stu-id="4917c-129">EF Core Power Tools</span></span>

<span data-ttu-id="4917c-130">EF Core Power Tools 是 Visual Studio 延伸模組，在簡單使用者介面中公開各種設計階段工作。</span><span class="sxs-lookup"><span data-stu-id="4917c-130">EF Core Power Tools is a Visual Studio extension that exposes various EF Core design-time tasks in a simple user interface.</span></span> <span data-ttu-id="4917c-131">包括現有資料庫和 [SQL Server DACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications) 之 DbContext 及實體類別的反向工程、資料庫移轉的管理，以及模型視覺效果。</span><span class="sxs-lookup"><span data-stu-id="4917c-131">It includes reverse engineering of DbContext and entity classes from existing databases and [SQL Server DACPACs](/sql/relational-databases/data-tier-applications/data-tier-applications), management of database migrations, and model visualizations.</span></span> <span data-ttu-id="4917c-132">EF Core：3，5。</span><span class="sxs-lookup"><span data-stu-id="4917c-132">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="4917c-133">GitHub Wiki</span><span class="sxs-lookup"><span data-stu-id="4917c-133">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a><span data-ttu-id="4917c-134">Entity Framework 視覺效果編輯器</span><span class="sxs-lookup"><span data-stu-id="4917c-134">Entity Framework Visual Editor</span></span>

<span data-ttu-id="4917c-135">Entity Framework 視覺效果編輯器是 Visual Studio 延伸模組，為 EF 6 和 EF Core 類別的視覺效果設計新增了 O/RM 設計工具。</span><span class="sxs-lookup"><span data-stu-id="4917c-135">Entity Framework Visual Editor is a Visual Studio extension that adds an O/RM designer for visual design of EF 6, and EF Core classes.</span></span> <span data-ttu-id="4917c-136">因為程式碼會透過 T4 範本產生，所以能加以自訂來滿足各種需求。</span><span class="sxs-lookup"><span data-stu-id="4917c-136">Code is generated using T4 templates so can be customized to suit any needs.</span></span> <span data-ttu-id="4917c-137">它支援繼承、單向及雙向關聯、列舉，還能夠對類別進行色彩編碼以及新增文字區塊，以說明設計中可能較不易懂的部分。</span><span class="sxs-lookup"><span data-stu-id="4917c-137">It supports inheritance, unidirectional and bidirectional associations, enumerations, and the ability to color-code your classes and add text blocks to explain potentially arcane parts of your design.</span></span> <span data-ttu-id="4917c-138">EF Core：2，3，5。</span><span class="sxs-lookup"><span data-stu-id="4917c-138">For EF Core: 2, 3, 5.</span></span>

<span data-ttu-id="4917c-139">[Marketplace](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="4917c-139">[Marketplace](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)</span></span>

### <a name="catfactory"></a><span data-ttu-id="4917c-140">CatFactory</span><span class="sxs-lookup"><span data-stu-id="4917c-140">CatFactory</span></span>

<span data-ttu-id="4917c-141">CatFactory 是 .NET Core 的 Scaffolding 引擎，可以自動從 SQL Server 資料庫產生 DbContext 類別、實體、對應組態及存放庫類別。</span><span class="sxs-lookup"><span data-stu-id="4917c-141">CatFactory is a scaffolding engine for .NET Core that can automate the generation of DbContext classes, entities, mapping configurations, and repository classes from a SQL Server database.</span></span> <span data-ttu-id="4917c-142">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="4917c-142">For EF Core: 2.</span></span>

[<span data-ttu-id="4917c-143">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="4917c-143">GitHub repository</span></span>](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a><span data-ttu-id="4917c-144">LoreSoft 的 Entity Framework Core Generator</span><span class="sxs-lookup"><span data-stu-id="4917c-144">LoreSoft's Entity Framework Core Generator</span></span>

<span data-ttu-id="4917c-145">Entity Framework Core 產生器 (efg) 是 .NET Core CLI 工具，可以從現有資料庫產生 EF Core 模型，與 `dotnet ef dbcontext scaffold` 十分相似，但它也透過區域取代或剖析對應檔案，支援安全的程式碼[重新產生](https://efg.loresoft.com/en/latest/regeneration/)。</span><span class="sxs-lookup"><span data-stu-id="4917c-145">Entity Framework Core Generator (efg) is a .NET Core CLI tool that can generate EF Core models from an existing database, much like `dotnet ef dbcontext scaffold`, but it also supports safe code [regeneration](https://efg.loresoft.com/en/latest/regeneration/) via region replacement or by parsing mapping files.</span></span> <span data-ttu-id="4917c-146">這項工具也支援產生檢視模型、驗證及物件對應程式程式碼。</span><span class="sxs-lookup"><span data-stu-id="4917c-146">This tool supports generating view models, validation, and object mapper code.</span></span> <span data-ttu-id="4917c-147">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="4917c-147">For EF Core: 2.</span></span>

<span data-ttu-id="4917c-148">[教學課程](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[文件](https://efg.loresoft.com/en/latest/)</span><span class="sxs-lookup"><span data-stu-id="4917c-148">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span></span>

## <a name="extensions"></a><span data-ttu-id="4917c-149">延伸模組</span><span class="sxs-lookup"><span data-stu-id="4917c-149">Extensions</span></span>

### <a name="microsoftentityframeworkcoreautohistory"></a><span data-ttu-id="4917c-150">Microsoft.EntityFrameworkCore.AutoHistory</span><span class="sxs-lookup"><span data-stu-id="4917c-150">Microsoft.EntityFrameworkCore.AutoHistory</span></span>

<span data-ttu-id="4917c-151">外掛程式庫，能夠自動將 EF Core 執行的資料變更記錄到記錄資料表中。</span><span class="sxs-lookup"><span data-stu-id="4917c-151">A plugin library that enables automatically recording the data changes performed by EF Core into a history table.</span></span> <span data-ttu-id="4917c-152">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="4917c-152">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="4917c-153">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="4917c-153">GitHub repository</span></span>](https://github.com/Arch/AutoHistory/)

### <a name="efcoresecondlevelcacheinterceptor"></a><span data-ttu-id="4917c-154">EFCoreSecondLevelCacheInterceptor</span><span class="sxs-lookup"><span data-stu-id="4917c-154">EFCoreSecondLevelCacheInterceptor</span></span>

<span data-ttu-id="4917c-155">第二個層級快取是查詢快取。</span><span class="sxs-lookup"><span data-stu-id="4917c-155">Second level caching is a query cache.</span></span> <span data-ttu-id="4917c-156">EF 命令的結果會存放在快取中，使相同的 EF 命令會從快取擷取其資料，而不必再次向資料庫再次執行命令。</span><span class="sxs-lookup"><span data-stu-id="4917c-156">The results of EF commands will be stored in the cache, so that the same EF commands will retrieve their data from the cache rather than executing them against the database again.</span></span> <span data-ttu-id="4917c-157">EF Core：3，5。</span><span class="sxs-lookup"><span data-stu-id="4917c-157">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="4917c-158">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="4917c-158">GitHub repository</span></span>](https://github.com/VahidN/EFCoreSecondLevelCacheInterceptor)

### <a name="geco"></a><span data-ttu-id="4917c-159">Geco</span><span class="sxs-lookup"><span data-stu-id="4917c-159">Geco</span></span>

<span data-ttu-id="4917c-160">Geco (產生器主控台) 是以主控台專案為基礎的簡單程式碼產生器，在 .NET Core 上執行，並使用插入 C# 的字串產生程式碼。</span><span class="sxs-lookup"><span data-stu-id="4917c-160">Geco (Generator Console) is a simple code generator based on a console project, that runs on .NET Core and uses C# interpolated strings for code generation.</span></span> <span data-ttu-id="4917c-161">Geco 包括適用於 EF Core 的反向模型產生器，支援複數、單數和可編輯範本。</span><span class="sxs-lookup"><span data-stu-id="4917c-161">Geco includes a reverse model generator for EF Core with support for pluralization, singularization, and editable templates.</span></span> <span data-ttu-id="4917c-162">它也提供種子資料指令碼產生器、指令碼執行器和資料庫清除工具。</span><span class="sxs-lookup"><span data-stu-id="4917c-162">It also provides a seed data script generator, a script runner, and a database cleaner.</span></span> <span data-ttu-id="4917c-163">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="4917c-163">For EF Core: 2.</span></span>

[<span data-ttu-id="4917c-164">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="4917c-164">GitHub repository</span></span>](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a><span data-ttu-id="4917c-165">EntityFrameworkCore.Scaffolding.Handlebars</span><span class="sxs-lookup"><span data-stu-id="4917c-165">EntityFrameworkCore.Scaffolding.Handlebars</span></span>

<span data-ttu-id="4917c-166">可讓您搭配 Handlebars 範本使用 Entity Framework Core 工具鏈，自訂從現有資料庫進行反向工程的類別。</span><span class="sxs-lookup"><span data-stu-id="4917c-166">Allows customization of classes reverse engineered from an existing database using the Entity Framework Core toolchain with Handlebars templates.</span></span> <span data-ttu-id="4917c-167">EF Core：2，3，5。</span><span class="sxs-lookup"><span data-stu-id="4917c-167">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="4917c-168">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="4917c-168">GitHub repository</span></span>](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a><span data-ttu-id="4917c-169">NeinLinq.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="4917c-169">NeinLinq.EntityFrameworkCore</span></span>

<span data-ttu-id="4917c-170">NeinLinq 擴充了 LINQ 提供者 (例如 Entity Framework)，並提供以下功能：重複使用函式、重寫查詢，以及使用可翻譯的述詞和選取器建置動態查詢。</span><span class="sxs-lookup"><span data-stu-id="4917c-170">NeinLinq extends LINQ providers such as Entity Framework to enable reusing functions, rewriting queries, and building dynamic queries using translatable predicates and selectors.</span></span> <span data-ttu-id="4917c-171">EF Core：2，3，5。</span><span class="sxs-lookup"><span data-stu-id="4917c-171">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="4917c-172">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="4917c-172">GitHub repository</span></span>](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a><span data-ttu-id="4917c-173">Microsoft.EntityFrameworkCore.UnitOfWork</span><span class="sxs-lookup"><span data-stu-id="4917c-173">Microsoft.EntityFrameworkCore.UnitOfWork</span></span>

<span data-ttu-id="4917c-174">Microsoft.EntityFrameworkCore 的外掛程式，可支援存放庫、工作模式的單位，以及支援分散式交易的多個資料庫。</span><span class="sxs-lookup"><span data-stu-id="4917c-174">A plugin for Microsoft.EntityFrameworkCore to support repository, unit of work patterns, and multiple databases with distributed transaction supported.</span></span> <span data-ttu-id="4917c-175">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="4917c-175">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="4917c-176">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="4917c-176">GitHub repository</span></span>](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a><span data-ttu-id="4917c-177">EFCore.BulkExtensions</span><span class="sxs-lookup"><span data-stu-id="4917c-177">EFCore.BulkExtensions</span></span>

<span data-ttu-id="4917c-178">適用於大量作業 (插入、更新、刪除) 的 EF Core 延伸模組。</span><span class="sxs-lookup"><span data-stu-id="4917c-178">EF Core extensions for Bulk operations (Insert, Update, Delete).</span></span> <span data-ttu-id="4917c-179">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="4917c-179">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="4917c-180">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="4917c-180">GitHub repository</span></span>](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a><span data-ttu-id="4917c-181">Bricelam.EntityFrameworkCore.Pluralizer</span><span class="sxs-lookup"><span data-stu-id="4917c-181">Bricelam.EntityFrameworkCore.Pluralizer</span></span>

<span data-ttu-id="4917c-182">新增設計階段複數表示。</span><span class="sxs-lookup"><span data-stu-id="4917c-182">Adds design-time pluralization.</span></span> <span data-ttu-id="4917c-183">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="4917c-183">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="4917c-184">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="4917c-184">GitHub repository</span></span>](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a><span data-ttu-id="4917c-185">Toolbelt.EntityFrameworkCore.IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="4917c-185">Toolbelt.EntityFrameworkCore.IndexAttribute</span></span>

<span data-ttu-id="4917c-186">[Index] 屬性的復興 (附有模型組建的延伸模組)。</span><span class="sxs-lookup"><span data-stu-id="4917c-186">Revival of [Index] attribute (with extension for model building).</span></span> <span data-ttu-id="4917c-187">EF Core：2，3，5。</span><span class="sxs-lookup"><span data-stu-id="4917c-187">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="4917c-188">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="4917c-188">GitHub repository</span></span>](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="verifyentityframework"></a><span data-ttu-id="4917c-189">確認 EntityFramework。</span><span class="sxs-lookup"><span data-stu-id="4917c-189">Verify.EntityFramework</span></span>

<span data-ttu-id="4917c-190">擴充 [驗證](https://github.com/VerifyTests/Verify) 以允許使用 EntityFramework 進行快照集測試。</span><span class="sxs-lookup"><span data-stu-id="4917c-190">Extends [Verify](https://github.com/VerifyTests/Verify) to allow snapshot testing with EntityFramework.</span></span> <span data-ttu-id="4917c-191">EF Core：3，5。</span><span class="sxs-lookup"><span data-stu-id="4917c-191">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="4917c-192">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="4917c-192">GitHub repository</span></span>](https://github.com/VerifyTests/Verify.EntityFramework)

### <a name="localdb"></a><span data-ttu-id="4917c-193">LocalDb</span><span class="sxs-lookup"><span data-stu-id="4917c-193">LocalDb</span></span>

<span data-ttu-id="4917c-194">提供 [SQL Server Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb) 周圍的包裝函式，以簡化對 Entity Framework 執行測試的程式。</span><span class="sxs-lookup"><span data-stu-id="4917c-194">Provides a wrapper around [SQL Server Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb) to simplify running tests against Entity Framework.</span></span> <span data-ttu-id="4917c-195">EF Core：3，5。</span><span class="sxs-lookup"><span data-stu-id="4917c-195">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="4917c-196">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="4917c-196">GitHub repository</span></span>](https://github.com/SimonCropp/LocalDb)

### <a name="effluentvalidation"></a><span data-ttu-id="4917c-197">EfFluentValidation</span><span class="sxs-lookup"><span data-stu-id="4917c-197">EfFluentValidation</span></span>

<span data-ttu-id="4917c-198">將 [FluentValidation](https://fluentvalidation.net/) 支援新增至 Entity Framework。</span><span class="sxs-lookup"><span data-stu-id="4917c-198">Adds [FluentValidation](https://fluentvalidation.net/) support to Entity Framework.</span></span> <span data-ttu-id="4917c-199">EF Core：3，5。</span><span class="sxs-lookup"><span data-stu-id="4917c-199">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="4917c-200">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="4917c-200">GitHub repository</span></span>](https://github.com/SimonCropp/EfFluentValidation)

### <a name="efcoretemporalsupport"></a><span data-ttu-id="4917c-201">EFCore.TemporalSupport</span><span class="sxs-lookup"><span data-stu-id="4917c-201">EFCore.TemporalSupport</span></span>

<span data-ttu-id="4917c-202">時態性支援的實作。</span><span class="sxs-lookup"><span data-stu-id="4917c-202">An implementation of temporal support.</span></span> <span data-ttu-id="4917c-203">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="4917c-203">For EF Core: 2.</span></span>

[<span data-ttu-id="4917c-204">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="4917c-204">GitHub repository</span></span>](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a><span data-ttu-id="4917c-205">EfCoreTemporalTable</span><span class="sxs-lookup"><span data-stu-id="4917c-205">EfCoreTemporalTable</span></span>

<span data-ttu-id="4917c-206">使用下列引進的擴充方法，輕鬆地在慣用的資料庫上執行時態性查詢：`AsTemporalAll()`、`AsTemporalAsOf(date)`、`AsTemporalFrom(startDate, endDate)`、`AsTemporalBetween(startDate, endDate)`、`AsTemporalContained(startDate, endDate)`。</span><span class="sxs-lookup"><span data-stu-id="4917c-206">Easily perform temporal queries on your favourite database using introduced extension methods: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span></span> <span data-ttu-id="4917c-207">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="4917c-207">For EF Core: 3.</span></span>

[<span data-ttu-id="4917c-208">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="4917c-208">GitHub repository</span></span>](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="entityframeworkcoretemporaltables"></a><span data-ttu-id="4917c-209">EntityFrameworkCore.TemporalTables</span><span class="sxs-lookup"><span data-stu-id="4917c-209">EntityFrameworkCore.TemporalTables</span></span>

<span data-ttu-id="4917c-210">Entity Framework Core 的延伸模組程式庫，可讓使用 SQL Server 的開發人員輕鬆地使用時態表。</span><span class="sxs-lookup"><span data-stu-id="4917c-210">Extension library for Entity Framework Core which allows developers who use SQL Server to easily use temporal tables.</span></span> <span data-ttu-id="4917c-211">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="4917c-211">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="4917c-212">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="4917c-212">GitHub repository</span></span>](https://github.com/findulov/EntityFrameworkCore.TemporalTables)

### <a name="entityframeworkcorecacheable"></a><span data-ttu-id="4917c-213">EntityFrameworkCore.Cacheable</span><span class="sxs-lookup"><span data-stu-id="4917c-213">EntityFrameworkCore.Cacheable</span></span>

<span data-ttu-id="4917c-214">高效能第二層查詢快取。</span><span class="sxs-lookup"><span data-stu-id="4917c-214">A high-performance second-level query cache.</span></span> <span data-ttu-id="4917c-215">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="4917c-215">For EF Core: 2.</span></span>

[<span data-ttu-id="4917c-216">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="4917c-216">GitHub repository</span></span>](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entityframeworkcorencache"></a><span data-ttu-id="4917c-217">EntityFrameworkCore.NCache</span><span class="sxs-lookup"><span data-stu-id="4917c-217">EntityFrameworkCore.NCache</span></span>

<span data-ttu-id="4917c-218">NCache Entity Framework Core Provider 是快取查詢結果的分散式第二層級快取提供者。</span><span class="sxs-lookup"><span data-stu-id="4917c-218">NCache Entity Framework Core Provider is a distributed second level cache provider for caching query results.</span></span> <span data-ttu-id="4917c-219">NCache 的分散式架構使其更具可擴縮性與高可用性。</span><span class="sxs-lookup"><span data-stu-id="4917c-219">The distributed architecture of NCache makes it more scalable and highly available.</span></span> <span data-ttu-id="4917c-220">適用于 EF Core 2、3。</span><span class="sxs-lookup"><span data-stu-id="4917c-220">For EF Core 2, 3.</span></span>

[<span data-ttu-id="4917c-221">網站</span><span class="sxs-lookup"><span data-stu-id="4917c-221">Website</span></span>](https://www.alachisoft.com/ncache/ef-core-cache.html)

### <a name="entityframeworkcoretriggered"></a><span data-ttu-id="4917c-222">Microsoft.entityframeworkcore 觸發</span><span class="sxs-lookup"><span data-stu-id="4917c-222">EntityFrameworkCore.Triggered</span></span>

<span data-ttu-id="4917c-223">EF Core 的觸發程式。</span><span class="sxs-lookup"><span data-stu-id="4917c-223">Triggers for EF Core.</span></span> <span data-ttu-id="4917c-224">在認可至資料庫之前和之後，回應 DbCoNtext 中的變更。</span><span class="sxs-lookup"><span data-stu-id="4917c-224">Respond to changes in your DbContext before and after they are committed to the database.</span></span> <span data-ttu-id="4917c-225">觸發程式是完全非同步，且支援相依性插入、繼承、串聯等。</span><span class="sxs-lookup"><span data-stu-id="4917c-225">Triggers are fully asynchronous and support dependency injection, inheritance, cascading and more.</span></span> <span data-ttu-id="4917c-226">EF Core：3，5。</span><span class="sxs-lookup"><span data-stu-id="4917c-226">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="4917c-227">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="4917c-227">GitHub repository</span></span>](https://github.com/koenbeuk/EntityFrameworkCore.Triggered)

### <a name="entity-framework-plus"></a><span data-ttu-id="4917c-228">Entity Framework Plus</span><span class="sxs-lookup"><span data-stu-id="4917c-228">Entity Framework Plus</span></span>

<span data-ttu-id="4917c-229">使用下功能延伸您的 DbContext：包括篩選、稽核、快取、查詢未來、批次刪除、批次更新等。</span><span class="sxs-lookup"><span data-stu-id="4917c-229">Extends your DbContext with features such as: Include Filter, Auditing, Caching, Query Future, Batch Delete, Batch Update, and more.</span></span> <span data-ttu-id="4917c-230">EF Core：2，3，5。</span><span class="sxs-lookup"><span data-stu-id="4917c-230">For EF Core: 2, 3, 5.</span></span>

<span data-ttu-id="4917c-231">[網站](https://entityframework-plus.net/)
[GitHub 存放庫](https://github.com/zzzprojects/EntityFramework-Plus)</span><span class="sxs-lookup"><span data-stu-id="4917c-231">[Website](https://entityframework-plus.net/)
[GitHub repository](https://github.com/zzzprojects/EntityFramework-Plus)</span></span>

### <a name="entity-framework-extensions"></a><span data-ttu-id="4917c-232">Entity Framework 擴充功能</span><span class="sxs-lookup"><span data-stu-id="4917c-232">Entity Framework Extensions</span></span>

<span data-ttu-id="4917c-233">使用高效能批次作業延伸您的 DbContext：BulkSaveChanges、BulkInsert、BulkUpdate、BulkDelete、BulkMerge 等。</span><span class="sxs-lookup"><span data-stu-id="4917c-233">Extends your DbContext with high-performance bulk operations: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge, and more.</span></span> <span data-ttu-id="4917c-234">EF Core：2，3，5。</span><span class="sxs-lookup"><span data-stu-id="4917c-234">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="4917c-235">網站</span><span class="sxs-lookup"><span data-stu-id="4917c-235">Website</span></span>](https://entityframework-extensions.net/)

### <a name="expressionify"></a><span data-ttu-id="4917c-236">Expressionify</span><span class="sxs-lookup"><span data-stu-id="4917c-236">Expressionify</span></span>

<span data-ttu-id="4917c-237">新增在 LINQ Lambda 中呼叫擴充方法的支援。</span><span class="sxs-lookup"><span data-stu-id="4917c-237">Add support for calling extension methods in LINQ lambdas.</span></span> <span data-ttu-id="4917c-238">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="4917c-238">For EF Core: 3.</span></span>

[<span data-ttu-id="4917c-239">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="4917c-239">GitHub repository</span></span>](https://github.com/ClaveConsulting/Expressionify)

### <a name="elinq"></a><span data-ttu-id="4917c-240">ELinq</span><span class="sxs-lookup"><span data-stu-id="4917c-240">ELinq</span></span>

<span data-ttu-id="4917c-241">關聯式資料的 Language-integrated Query (LINQ) 技術。</span><span class="sxs-lookup"><span data-stu-id="4917c-241">Language Integrated Query (LINQ) technology for relational databases.</span></span> <span data-ttu-id="4917c-242">可供使用 C# 來撰寫強型別查詢。</span><span class="sxs-lookup"><span data-stu-id="4917c-242">It allows you to use C# to write strongly typed queries.</span></span> <span data-ttu-id="4917c-243">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="4917c-243">For EF Core: 3.</span></span>

- <span data-ttu-id="4917c-244">查詢建立的完整 C# 支援：Lambda 內部的多個陳述式、變數、函式等。</span><span class="sxs-lookup"><span data-stu-id="4917c-244">Full C# support for query creation: multiple statements inside lambda, variables, functions, etc.</span></span>
- <span data-ttu-id="4917c-245">與 SQL 沒有語意隔閡。</span><span class="sxs-lookup"><span data-stu-id="4917c-245">No semantic gap with SQL.</span></span> <span data-ttu-id="4917c-246">ELinq 宣告 SQL 語句 (例如 `SELECT` ， `FROM` `WHERE`) 為第一個類別的 c # 方法，結合熟悉的語法與 intellisense，型別安全和重構。</span><span class="sxs-lookup"><span data-stu-id="4917c-246">ELinq declares SQL statements (like `SELECT`, `FROM`, `WHERE`) as first class C# methods, combining familiar syntax with intellisense, type safety and refactoring.</span></span>

<span data-ttu-id="4917c-247">因此，SQL 會變成在本機公開其 API 的「另一個」類別庫，實際上就是「整合語言的 SQL」  。</span><span class="sxs-lookup"><span data-stu-id="4917c-247">As a result SQL becomes just "another" class library exposing its API locally, literally *"Language Integrated SQL"*.</span></span>

[<span data-ttu-id="4917c-248">網站</span><span class="sxs-lookup"><span data-stu-id="4917c-248">Website</span></span>](https://entitylinq.com/)

### <a name="ramses"></a><span data-ttu-id="4917c-249">Ramses</span><span class="sxs-lookup"><span data-stu-id="4917c-249">Ramses</span></span>

<span data-ttu-id="4917c-250">生命週期勾點 (呼叫 SaveChanges)。</span><span class="sxs-lookup"><span data-stu-id="4917c-250">Lifecycle hooks (for SaveChanges).</span></span> <span data-ttu-id="4917c-251">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="4917c-251">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="4917c-252">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="4917c-252">GitHub repository</span></span>](https://github.com/JValck/Ramses)

### <a name="efcorenamingconventions"></a><span data-ttu-id="4917c-253">EFCore.NamingConventions</span><span class="sxs-lookup"><span data-stu-id="4917c-253">EFCore.NamingConventions</span></span>

<span data-ttu-id="4917c-254">這會自動使所有資料表與資料行名稱採用全為大寫或小寫字母，並以底線作為空格的格式。</span><span class="sxs-lookup"><span data-stu-id="4917c-254">This will automatically make all your table and column names have snake_case, all UPPER or all lower case naming.</span></span> <span data-ttu-id="4917c-255">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="4917c-255">For EF Core: 3.</span></span>

[<span data-ttu-id="4917c-256">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="4917c-256">GitHub repository</span></span>](https://github.com/efcore/EFCore.NamingConventions)

### <a name="simplersoftwareentityframeworkcoresqlservernodatime"></a><span data-ttu-id="4917c-257">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span><span class="sxs-lookup"><span data-stu-id="4917c-257">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span></span>

<span data-ttu-id="4917c-258">針對 NodaTime 類型，將原生支援新增至 SQL Server 的 EntityFrameworkCore。</span><span class="sxs-lookup"><span data-stu-id="4917c-258">Adds native support to EntityFrameworkCore for SQL Server for the NodaTime types.</span></span> <span data-ttu-id="4917c-259">EF Core：3，5。</span><span class="sxs-lookup"><span data-stu-id="4917c-259">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="4917c-260">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="4917c-260">GitHub repository</span></span>](https://github.com/StevenRasmussen/EFCore.SqlServer.NodaTime)

### <a name="dabbleentityframeworkcoretemporalquery"></a><span data-ttu-id="4917c-261">Dabble.EntityFrameworkCore.Temporal.Query</span><span class="sxs-lookup"><span data-stu-id="4917c-261">Dabble.EntityFrameworkCore.Temporal.Query</span></span>

<span data-ttu-id="4917c-262">Entity Framework Core 3.1 的 LINQ 延伸模組，以支援 Microsoft SQL Server 時態表查詢。</span><span class="sxs-lookup"><span data-stu-id="4917c-262">LINQ extensions to Entity Framework Core 3.1 to support Microsoft SQL Server Temporal Table Querying.</span></span> <span data-ttu-id="4917c-263">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="4917c-263">For EF Core: 3.</span></span>

[<span data-ttu-id="4917c-264">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="4917c-264">GitHub repository</span></span>](https://github.com/Adam-Langley/efcore-temporal-query)

### <a name="entityframeworkcoresqlserverhierarchyid"></a><span data-ttu-id="4917c-265">EntityFrameworkCore.SqlServer.HierarchyId</span><span class="sxs-lookup"><span data-stu-id="4917c-265">EntityFrameworkCore.SqlServer.HierarchyId</span></span>

<span data-ttu-id="4917c-266">為 SQL Server EF Core 提供者新增 hierarchyid 支援。</span><span class="sxs-lookup"><span data-stu-id="4917c-266">Adds hierarchyid support to the SQL Server EF Core provider.</span></span> <span data-ttu-id="4917c-267">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="4917c-267">For EF Core: 3.</span></span>

[<span data-ttu-id="4917c-268">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="4917c-268">GitHub repository</span></span>](https://github.com/efcore/EFCore.SqlServer.HierarchyId)

### <a name="linq2dbentityframeworkcore"></a><span data-ttu-id="4917c-269">linq2db.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="4917c-269">linq2db.EntityFrameworkCore</span></span>

<span data-ttu-id="4917c-270">將 LINQ 查詢轉換為 SQL 運算式的替代轉譯程式。</span><span class="sxs-lookup"><span data-stu-id="4917c-270">Alternative translator of LINQ queries to SQL expressions.</span></span> <span data-ttu-id="4917c-271">EF Core：3，5。</span><span class="sxs-lookup"><span data-stu-id="4917c-271">For EF Core: 3, 5.</span></span>

<span data-ttu-id="4917c-272">包括對進階 SQL 功能的支援，例如 CTE、大量複製、資料表提示、視窗型函式、暫存資料表，以及資料庫端的建立/更新/刪除作業。</span><span class="sxs-lookup"><span data-stu-id="4917c-272">Includes support for advanced SQL features such as CTEs, bulk copy, table hints, windowed functions, temporary tables, and database-side create/update/delete operations.</span></span>

[<span data-ttu-id="4917c-273">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="4917c-273">GitHub repository</span></span>](https://github.com/linq2db/linq2db.EntityFrameworkCore)

### <a name="efcoresoftdelete"></a><span data-ttu-id="4917c-274">EFCore.SoftDelete</span><span class="sxs-lookup"><span data-stu-id="4917c-274">EFCore.SoftDelete</span></span>

<span data-ttu-id="4917c-275">虛刪除實體的實作。</span><span class="sxs-lookup"><span data-stu-id="4917c-275">An implementation for soft deleting entities.</span></span> <span data-ttu-id="4917c-276">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="4917c-276">For EF Core: 3.</span></span>

[<span data-ttu-id="4917c-277">NuGet</span><span class="sxs-lookup"><span data-stu-id="4917c-277">NuGet</span></span>](https://www.nuget.org/packages/EFCore.SoftDelete)

### <a name="entityframeworkcoreconfigurationmanager"></a><span data-ttu-id="4917c-278">EntityFrameworkCore.ConfigurationManager</span><span class="sxs-lookup"><span data-stu-id="4917c-278">EntityFrameworkCore.ConfigurationManager</span></span>

<span data-ttu-id="4917c-279">擴充 EF Core 以解析 App.config 的連接字串。適用于 EF Core：3。</span><span class="sxs-lookup"><span data-stu-id="4917c-279">Extends EF Core to resolve connection strings from App.config. For EF Core: 3.</span></span>

[<span data-ttu-id="4917c-280">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="4917c-280">GitHub repository</span></span>](https://github.com/efcore/EFCore.ConfigurationManager)

### <a name="detached-mapper"></a><span data-ttu-id="4917c-281">卸離對應程式</span><span class="sxs-lookup"><span data-stu-id="4917c-281">Detached Mapper</span></span>

<span data-ttu-id="4917c-282">DTO-Entity 對應程式具有組合/匯總處理 (類似于 GraphDiff) 。</span><span class="sxs-lookup"><span data-stu-id="4917c-282">A DTO-Entity mapper with composition/aggregation handling (similar to GraphDiff).</span></span> <span data-ttu-id="4917c-283">EF Core：3，5。</span><span class="sxs-lookup"><span data-stu-id="4917c-283">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="4917c-284">NuGet</span><span class="sxs-lookup"><span data-stu-id="4917c-284">NuGet</span></span>](https://www.nuget.org/packages/Detached.Mappers.EntityFramework)

### <a name="entityframeworkcoresqlitenodatime"></a><span data-ttu-id="4917c-285">Microsoft.entityframeworkcore。 NodaTime</span><span class="sxs-lookup"><span data-stu-id="4917c-285">EntityFrameworkCore.Sqlite.NodaTime</span></span>

<span data-ttu-id="4917c-286">在使用[SQLite](https://sqlite.org)時新增對[NodaTime](https://nodatime.org)類型的支援。</span><span class="sxs-lookup"><span data-stu-id="4917c-286">Adds support for [NodaTime](https://nodatime.org) types when using [SQLite](https://sqlite.org).</span></span> <span data-ttu-id="4917c-287">適用于 EF Core：5。</span><span class="sxs-lookup"><span data-stu-id="4917c-287">For EF Core: 5.</span></span>

[<span data-ttu-id="4917c-288">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="4917c-288">GitHub repository</span></span>](https://github.com/khellang/EFCore.Sqlite.NodaTime)

### <a name="erikejentityframeworkcoresqlserverdacpac"></a><span data-ttu-id="4917c-289">ErikEJ. Microsoft.entityframeworkcore。 .Dacpac</span><span class="sxs-lookup"><span data-stu-id="4917c-289">ErikEJ.EntityFrameworkCore.SqlServer.Dacpac</span></span>

<span data-ttu-id="4917c-290">啟用從 SQL Server 資料層應用程式封裝的 EF Core 模型進行反向工程， ( .dacpac) 。</span><span class="sxs-lookup"><span data-stu-id="4917c-290">Enables reverse engineering an EF Core model from a SQL Server data-tier application package (.dacpac).</span></span> <span data-ttu-id="4917c-291">EF Core：3，5。</span><span class="sxs-lookup"><span data-stu-id="4917c-291">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="4917c-292">GitHub Wiki</span><span class="sxs-lookup"><span data-stu-id="4917c-292">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki/ErikEJ.EntityFrameworkCore.SqlServer.Dacpac)

### <a name="erikejentityframeworkcoredgmlbuilder"></a><span data-ttu-id="4917c-293">ErikEJ. Microsoft.entityframeworkcore. DgmlBuilder</span><span class="sxs-lookup"><span data-stu-id="4917c-293">ErikEJ.EntityFrameworkCore.DgmlBuilder</span></span>

<span data-ttu-id="4917c-294">產生 DGML (圖形) 內容，以視覺化方式呈現您的 DbCoNtext。</span><span class="sxs-lookup"><span data-stu-id="4917c-294">Generate DGML (Graph) content that visualizes your DbContext.</span></span> <span data-ttu-id="4917c-295">將 AsDgml () 擴充方法加入至 DbCoNtext 類別。</span><span class="sxs-lookup"><span data-stu-id="4917c-295">Adds the AsDgml() extension method to the DbContext class.</span></span> <span data-ttu-id="4917c-296">EF Core：3，5。</span><span class="sxs-lookup"><span data-stu-id="4917c-296">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="4917c-297">GitHub Wiki</span><span class="sxs-lookup"><span data-stu-id="4917c-297">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki/Inspect-your-DbContext-model)

### <a name="entityframeworkexceptions"></a><span data-ttu-id="4917c-298">EntityFramework。例外狀況</span><span class="sxs-lookup"><span data-stu-id="4917c-298">EntityFramework.Exceptions</span></span>

<span data-ttu-id="4917c-299">使用 Entity Framework Core 時，所有資料庫例外狀況都會包裝在就 dbupdateexception 中。</span><span class="sxs-lookup"><span data-stu-id="4917c-299">When using Entity Framework Core all database exceptions are wrapped in DbUpdateException.</span></span> <span data-ttu-id="4917c-300">EntityFramework 會處理所有資料庫特定的詳細資料，以找出違反了哪些條件約束，並可讓您在 `UniqueConstraintException` `CannotInsertNullException` `MaxLengthExceededException` `NumericOverflowException` `ReferenceConstraintException` 查詢違反資料庫條件約束時，使用具類型的例外狀況，例如、、、。</span><span class="sxs-lookup"><span data-stu-id="4917c-300">EntityFramework.Exceptions handles all the database-specific details to find which constraint was violated and allows you to use typed exceptions such as `UniqueConstraintException`, `CannotInsertNullException`, `MaxLengthExceededException`, `NumericOverflowException`, `ReferenceConstraintException` when your query violates database constraints.</span></span>

<span data-ttu-id="4917c-301">支援 SQL Server、Postgres、MySql、SQLite 和 Oracle</span><span class="sxs-lookup"><span data-stu-id="4917c-301">Supports SQL Server, Postgres, MySql, SQLite and Oracle</span></span>

<span data-ttu-id="4917c-302">EF Core：3，5。</span><span class="sxs-lookup"><span data-stu-id="4917c-302">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="4917c-303">GitHub 儲存機制</span><span class="sxs-lookup"><span data-stu-id="4917c-303">GitHub Repository</span></span>](https://github.com/Giorgi/EntityFramework.Exceptions)
