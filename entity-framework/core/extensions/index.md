---
title: 工具和延伸模組 - EF Core
author: ErikEJ
ms.date: 12/17/2019
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
uid: core/extensions/index
ms.openlocfilehash: 99f59153a452a2f4aad5811110ebc5b5da7717ef
ms.sourcegitcommit: b3cf5d2e3cb170b9916795d1d8c88678269639b1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2020
ms.locfileid: "76888050"
---
# <a name="ef-core-tools--extensions"></a><span data-ttu-id="27f1e-102">EF Core 工具和延伸模組</span><span class="sxs-lookup"><span data-stu-id="27f1e-102">EF Core Tools & Extensions</span></span>

<span data-ttu-id="27f1e-103">這些工具和延伸模組可為 Entity Framework Core 2.1 及更新版本提供額外的功能。</span><span class="sxs-lookup"><span data-stu-id="27f1e-103">These tools and extensions provide additional functionality for Entity Framework Core 2.1 and later.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="27f1e-104">延伸模組以各種來源建置而成，且不屬於 Entity Framework Core 專案維護的一部份。</span><span class="sxs-lookup"><span data-stu-id="27f1e-104">Extensions are built by a variety of sources and aren't maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="27f1e-105">考慮使用協力廠商延伸模組時，請務必評估其品質、授權、相容性、支援等，確保其符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="27f1e-105">When considering a third party extension, be sure to evaluate its quality, licensing, compatibility, support, etc. to ensure it meets your requirements.</span></span> <span data-ttu-id="27f1e-106">特別是針對舊版 EF Core 所建置的延伸模組，可能需要予以更新，才能搭配最新版本使用。</span><span class="sxs-lookup"><span data-stu-id="27f1e-106">In particular, an extension built for an older version of EF Core may need updating before it will work with the latest versions.</span></span>

## <a name="tools"></a><span data-ttu-id="27f1e-107">工具</span><span class="sxs-lookup"><span data-stu-id="27f1e-107">Tools</span></span>

### <a name="llblgen-pro"></a><span data-ttu-id="27f1e-108">LLBLGen Pro</span><span class="sxs-lookup"><span data-stu-id="27f1e-108">LLBLGen Pro</span></span>

<span data-ttu-id="27f1e-109">LLBLGen Pro 是實體模型化解決方案，具備 Entity Framework 與 Entity Framework Core 的支援。</span><span class="sxs-lookup"><span data-stu-id="27f1e-109">LLBLGen Pro is an entity modeling solution with support for Entity Framework and Entity Framework Core.</span></span> <span data-ttu-id="27f1e-110">您可加以利用來輕鬆定義實體模型，並將其對應至資料庫，優先使用資料庫或模型後，即可開始直接撰寫查詢。</span><span class="sxs-lookup"><span data-stu-id="27f1e-110">It lets you easily define your entity model and map it to your database, using database first or model first, so you can get started writing queries right away.</span></span> <span data-ttu-id="27f1e-111">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="27f1e-111">For EF Core: 2.</span></span>

[<span data-ttu-id="27f1e-112">網站</span><span class="sxs-lookup"><span data-stu-id="27f1e-112">Website</span></span>](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a><span data-ttu-id="27f1e-113">Devart Entity Developer</span><span class="sxs-lookup"><span data-stu-id="27f1e-113">Devart Entity Developer</span></span>

<span data-ttu-id="27f1e-114">Devart Entity 是功能強大的 ORM 設計工具，適用於 ADO.NET Entity Framework、NHibernate、LinqConnect、Telerik Data Access、LINQ 到 SQL。</span><span class="sxs-lookup"><span data-stu-id="27f1e-114">Entity Developer is a powerful ORM designer for ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access, and LINQ to SQL.</span></span> <span data-ttu-id="27f1e-115">它支援使用 model first 或 database first 方法，以及 C# 或 Visual Basic 程式碼產生，以視覺化的方式設計 EF Core 模型。</span><span class="sxs-lookup"><span data-stu-id="27f1e-115">It supports designing EF Core models visually, using model first or database first approaches, and C# or Visual Basic code generation.</span></span> <span data-ttu-id="27f1e-116">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="27f1e-116">For EF Core: 2.</span></span>

[<span data-ttu-id="27f1e-117">網站</span><span class="sxs-lookup"><span data-stu-id="27f1e-117">Website</span></span>](https://www.devart.com/entitydeveloper/)

### <a name="nhydrate-orm-for-entity-framework"></a><span data-ttu-id="27f1e-118">Entity Framework 的 nHydrate ORM</span><span class="sxs-lookup"><span data-stu-id="27f1e-118">nHydrate ORM for Entity Framework</span></span>

<span data-ttu-id="27f1e-119">為 Entity Framework 建立可延伸類別的強型別 ORM。</span><span class="sxs-lookup"><span data-stu-id="27f1e-119">An ORM that creates strongly-typed, extendable classes for Entity Framework.</span></span> <span data-ttu-id="27f1e-120">產生的程式碼為 Entity Framework Core。</span><span class="sxs-lookup"><span data-stu-id="27f1e-120">The generated code is Entity Framework Core.</span></span> <span data-ttu-id="27f1e-121">因此兩者沒有任何不同。</span><span class="sxs-lookup"><span data-stu-id="27f1e-121">There is no difference.</span></span> <span data-ttu-id="27f1e-122">這不能取代 EF 或自訂 ORM。</span><span class="sxs-lookup"><span data-stu-id="27f1e-122">This is not a replacement for EF or a custom ORM.</span></span> <span data-ttu-id="27f1e-123">這是一種視覺化的模型化層，可讓小組管理複雜的資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="27f1e-123">It is a visual, modeling layer that allows a team to manage complex database schemas.</span></span> <span data-ttu-id="27f1e-124">適用於 Git 這類的 SCM 軟體，允許多使用者存取模型，但具有最少的衝突。</span><span class="sxs-lookup"><span data-stu-id="27f1e-124">It works well with SCM software like Git, allowing multi-user access to your model with minimal conflicts.</span></span> <span data-ttu-id="27f1e-125">安裝程式會追蹤模型變更並建立升級指令碼。</span><span class="sxs-lookup"><span data-stu-id="27f1e-125">The installer tracks model changes and creates upgrade scripts.</span></span> <span data-ttu-id="27f1e-126">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="27f1e-126">For EF Core: 3.</span></span>

[<span data-ttu-id="27f1e-127">Github 網站</span><span class="sxs-lookup"><span data-stu-id="27f1e-127">Github site</span></span>](https://github.com/nHydrate/nHydrate)

### <a name="ef-core-power-tools"></a><span data-ttu-id="27f1e-128">EF Core Power Tools</span><span class="sxs-lookup"><span data-stu-id="27f1e-128">EF Core Power Tools</span></span>

<span data-ttu-id="27f1e-129">EF Core Power Tools 是 Visual Studio 延伸模組，在簡單使用者介面中公開各種設計階段工作。</span><span class="sxs-lookup"><span data-stu-id="27f1e-129">EF Core Power Tools is a Visual Studio extension that exposes various EF Core design-time tasks in a simple user interface.</span></span> <span data-ttu-id="27f1e-130">包括現有資料庫和 [SQL Server DACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) 之 DbContext 及實體類別的反向工程、資料庫移轉的管理，以及模型視覺效果。</span><span class="sxs-lookup"><span data-stu-id="27f1e-130">It includes reverse engineering of DbContext and entity classes from existing databases and [SQL Server DACPACs](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications), management of database migrations, and model visualizations.</span></span> <span data-ttu-id="27f1e-131">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="27f1e-131">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="27f1e-132">GitHub Wiki</span><span class="sxs-lookup"><span data-stu-id="27f1e-132">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a><span data-ttu-id="27f1e-133">Entity Framework 視覺效果編輯器</span><span class="sxs-lookup"><span data-stu-id="27f1e-133">Entity Framework Visual Editor</span></span>

<span data-ttu-id="27f1e-134">Entity Framework 視覺效果編輯器是 Visual Studio 延伸模組，為 EF 6 和 EF Core 類別的視覺效果設計新增 ORM 設計工具。</span><span class="sxs-lookup"><span data-stu-id="27f1e-134">Entity Framework Visual Editor is a Visual Studio extension that adds an ORM designer for visual design of EF 6, and EF Core classes.</span></span> <span data-ttu-id="27f1e-135">因為程式碼會透過 T4 範本產生，所以能加以自訂來滿足各種需求。</span><span class="sxs-lookup"><span data-stu-id="27f1e-135">Code is generated using T4 templates so can be customized to suit any needs.</span></span> <span data-ttu-id="27f1e-136">它支援繼承、單向及雙向關聯、列舉，還能夠對類別進行色彩編碼以及新增文字區塊，以說明設計中可能較不易懂的部分。</span><span class="sxs-lookup"><span data-stu-id="27f1e-136">It supports inheritance, unidirectional and bidirectional associations, enumerations, and the ability to color-code your classes and add text blocks to explain potentially arcane parts of your design.</span></span> <span data-ttu-id="27f1e-137">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="27f1e-137">For EF Core: 2.</span></span>

<span data-ttu-id="27f1e-138">[Marketplace](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="27f1e-138">[Marketplace](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)</span></span>

### <a name="catfactory"></a><span data-ttu-id="27f1e-139">CatFactory</span><span class="sxs-lookup"><span data-stu-id="27f1e-139">CatFactory</span></span>

<span data-ttu-id="27f1e-140">CatFactory 是 .NET Core 的 Scaffolding 引擎，可以自動從 SQL Server 資料庫產生 DbContext 類別、實體、對應組態及存放庫類別。</span><span class="sxs-lookup"><span data-stu-id="27f1e-140">CatFactory is a scaffolding engine for .NET Core that can automate the generation of DbContext classes, entities, mapping configurations, and repository classes from a SQL Server database.</span></span> <span data-ttu-id="27f1e-141">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="27f1e-141">For EF Core: 2.</span></span>

[<span data-ttu-id="27f1e-142">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="27f1e-142">GitHub repository</span></span>](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a><span data-ttu-id="27f1e-143">LoreSoft 的 Entity Framework Core Generator</span><span class="sxs-lookup"><span data-stu-id="27f1e-143">LoreSoft's Entity Framework Core Generator</span></span>

<span data-ttu-id="27f1e-144">Entity Framework Core 產生器 (efg) 是 .NET Core CLI 工具，可以從現有資料庫產生 EF Core 模型，與 `dotnet ef dbcontext scaffold` 十分相似，但它也透過區域取代或剖析對應檔案，支援安全的程式碼[重新產生](https://efg.loresoft.com/en/latest/regeneration/)。</span><span class="sxs-lookup"><span data-stu-id="27f1e-144">Entity Framework Core Generator (efg) is a .NET Core CLI tool that can generate EF Core models from an existing database, much like `dotnet ef dbcontext scaffold`, but it also supports safe code [regeneration](https://efg.loresoft.com/en/latest/regeneration/) via region replacement or by parsing mapping files.</span></span> <span data-ttu-id="27f1e-145">這項工具也支援產生檢視模型、驗證及物件對應程式程式碼。</span><span class="sxs-lookup"><span data-stu-id="27f1e-145">This tool supports generating view models, validation, and object mapper code.</span></span> <span data-ttu-id="27f1e-146">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="27f1e-146">For EF Core: 2.</span></span>

<span data-ttu-id="27f1e-147">[教學課程](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[文件](https://efg.loresoft.com/en/latest/)</span><span class="sxs-lookup"><span data-stu-id="27f1e-147">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span></span>

## <a name="extensions"></a><span data-ttu-id="27f1e-148">延伸模組</span><span class="sxs-lookup"><span data-stu-id="27f1e-148">Extensions</span></span>

### <a name="microsoftentityframeworkcoreautohistory"></a><span data-ttu-id="27f1e-149">Microsoft.EntityFrameworkCore.AutoHistory</span><span class="sxs-lookup"><span data-stu-id="27f1e-149">Microsoft.EntityFrameworkCore.AutoHistory</span></span>

<span data-ttu-id="27f1e-150">外掛程式庫，能夠自動將 EF Core 執行的資料變更記錄到記錄資料表中。</span><span class="sxs-lookup"><span data-stu-id="27f1e-150">A plugin library that enables automatically recording the data changes performed by EF Core into a history table.</span></span> <span data-ttu-id="27f1e-151">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="27f1e-151">For EF Core: 2.</span></span>

[<span data-ttu-id="27f1e-152">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="27f1e-152">GitHub repository</span></span>](https://github.com/Arch/AutoHistory/)

### <a name="efsecondlevelcachecore"></a><span data-ttu-id="27f1e-153">EFSecondLevelCache.Core</span><span class="sxs-lookup"><span data-stu-id="27f1e-153">EFSecondLevelCache.Core</span></span>

<span data-ttu-id="27f1e-154">將 EF Core 查詢的結果儲存至第二層快取的延伸模組，如此一來可避免後續執行相同查詢時，直接從快取存取資料庫及擷取資料。</span><span class="sxs-lookup"><span data-stu-id="27f1e-154">An extension that enables storing the results of EF Core queries into a second-level cache, so that subsequent executions of the same queries can avoid accessing the database and retrieve the data directly from the cache.</span></span> <span data-ttu-id="27f1e-155">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="27f1e-155">For EF Core: 2.</span></span>

[<span data-ttu-id="27f1e-156">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="27f1e-156">GitHub repository</span></span>](https://github.com/VahidN/EFSecondLevelCache.Core/)

### <a name="geco"></a><span data-ttu-id="27f1e-157">Geco</span><span class="sxs-lookup"><span data-stu-id="27f1e-157">Geco</span></span>

<span data-ttu-id="27f1e-158">Geco (產生器主控台) 是以主控台專案為基礎的簡單程式碼產生器，在 .NET Core 上執行，並使用插入 C# 的字串產生程式碼。</span><span class="sxs-lookup"><span data-stu-id="27f1e-158">Geco (Generator Console) is a simple code generator based on a console project, that runs on .NET Core and uses C# interpolated strings for code generation.</span></span> <span data-ttu-id="27f1e-159">Geco 包括適用於 EF Core 的反向模型產生器，支援複數、單數和可編輯範本。</span><span class="sxs-lookup"><span data-stu-id="27f1e-159">Geco includes a reverse model generator for EF Core with support for pluralization, singularization, and editable templates.</span></span> <span data-ttu-id="27f1e-160">它也提供種子資料指令碼產生器、指令碼執行器和資料庫清除工具。</span><span class="sxs-lookup"><span data-stu-id="27f1e-160">It also provides a seed data script generator, a script runner, and a database cleaner.</span></span> <span data-ttu-id="27f1e-161">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="27f1e-161">For EF Core: 2.</span></span>

[<span data-ttu-id="27f1e-162">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="27f1e-162">GitHub repository</span></span>](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a><span data-ttu-id="27f1e-163">EntityFrameworkCore.Scaffolding.Handlebars</span><span class="sxs-lookup"><span data-stu-id="27f1e-163">EntityFrameworkCore.Scaffolding.Handlebars</span></span> 

<span data-ttu-id="27f1e-164">可讓您搭配 Handlebars 範本使用 Entity Framework Core 工具鏈，自訂從現有資料庫進行反向工程的類別。</span><span class="sxs-lookup"><span data-stu-id="27f1e-164">Allows customization of classes reverse engineered from an existing database using the Entity Framework Core toolchain with Handlebars templates.</span></span> <span data-ttu-id="27f1e-165">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="27f1e-165">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="27f1e-166">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="27f1e-166">GitHub repository</span></span>](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a><span data-ttu-id="27f1e-167">NeinLinq.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="27f1e-167">NeinLinq.EntityFrameworkCore</span></span> 

<span data-ttu-id="27f1e-168">NeinLinq 擴充了 LINQ 提供者 (例如 Entity Framework)，並提供以下功能：重複使用函式、重寫查詢，以及使用可翻譯的述詞和選取器建置動態查詢。</span><span class="sxs-lookup"><span data-stu-id="27f1e-168">NeinLinq extends LINQ providers such as Entity Framework to enable reusing functions, rewriting queries, and building dynamic queries using translatable predicates and selectors.</span></span> <span data-ttu-id="27f1e-169">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="27f1e-169">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="27f1e-170">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="27f1e-170">GitHub repository</span></span>](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a><span data-ttu-id="27f1e-171">Microsoft.EntityFrameworkCore.UnitOfWork</span><span class="sxs-lookup"><span data-stu-id="27f1e-171">Microsoft.EntityFrameworkCore.UnitOfWork</span></span>

<span data-ttu-id="27f1e-172">Microsoft.EntityFrameworkCore 的外掛程式，可支援存放庫、工作模式的單位，以及支援分散式交易的多個資料庫。</span><span class="sxs-lookup"><span data-stu-id="27f1e-172">A plugin for Microsoft.EntityFrameworkCore to support repository, unit of work patterns, and multiple databases with distributed transaction supported.</span></span> <span data-ttu-id="27f1e-173">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="27f1e-173">For EF Core: 2.</span></span>

[<span data-ttu-id="27f1e-174">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="27f1e-174">GitHub repository</span></span>](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a><span data-ttu-id="27f1e-175">EFCore.BulkExtensions</span><span class="sxs-lookup"><span data-stu-id="27f1e-175">EFCore.BulkExtensions</span></span>

<span data-ttu-id="27f1e-176">適用於大量作業 (插入、更新、刪除) 的 EF Core 延伸模組。</span><span class="sxs-lookup"><span data-stu-id="27f1e-176">EF Core extensions for Bulk operations (Insert, Update, Delete).</span></span> <span data-ttu-id="27f1e-177">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="27f1e-177">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="27f1e-178">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="27f1e-178">GitHub repository</span></span>](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a><span data-ttu-id="27f1e-179">Bricelam.EntityFrameworkCore.Pluralizer</span><span class="sxs-lookup"><span data-stu-id="27f1e-179">Bricelam.EntityFrameworkCore.Pluralizer</span></span>

<span data-ttu-id="27f1e-180">新增設計階段複數表示。</span><span class="sxs-lookup"><span data-stu-id="27f1e-180">Adds design-time pluralization.</span></span> <span data-ttu-id="27f1e-181">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="27f1e-181">For EF Core: 2.</span></span>

[<span data-ttu-id="27f1e-182">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="27f1e-182">GitHub repository</span></span>](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a><span data-ttu-id="27f1e-183">Toolbelt.EntityFrameworkCore.IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="27f1e-183">Toolbelt.EntityFrameworkCore.IndexAttribute</span></span>

<span data-ttu-id="27f1e-184">[Index] 屬性的復興 (附有模型組建的延伸模組)。</span><span class="sxs-lookup"><span data-stu-id="27f1e-184">Revival of [Index] attribute (with extension for model building).</span></span> <span data-ttu-id="27f1e-185">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="27f1e-185">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="27f1e-186">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="27f1e-186">GitHub repository</span></span>](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a><span data-ttu-id="27f1e-187">EfCore.InMemoryHelpers</span><span class="sxs-lookup"><span data-stu-id="27f1e-187">EfCore.InMemoryHelpers</span></span>

<span data-ttu-id="27f1e-188">提供 EF Core 記憶體內部資料庫提供者的包裝函式。</span><span class="sxs-lookup"><span data-stu-id="27f1e-188">Provides a wrapper around the EF Core In-Memory Database Provider.</span></span> <span data-ttu-id="27f1e-189">使它的運作更接近關聯式提供者。</span><span class="sxs-lookup"><span data-stu-id="27f1e-189">Makes it act more like a relational provider.</span></span> <span data-ttu-id="27f1e-190">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="27f1e-190">For EF Core: 2.</span></span>

[<span data-ttu-id="27f1e-191">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="27f1e-191">GitHub repository</span></span>](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a><span data-ttu-id="27f1e-192">EFCore.TemporalSupport</span><span class="sxs-lookup"><span data-stu-id="27f1e-192">EFCore.TemporalSupport</span></span>

<span data-ttu-id="27f1e-193">時態性支援的實作。</span><span class="sxs-lookup"><span data-stu-id="27f1e-193">An implementation of temporal support.</span></span> <span data-ttu-id="27f1e-194">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="27f1e-194">For EF Core: 2.</span></span>

[<span data-ttu-id="27f1e-195">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="27f1e-195">GitHub repository</span></span>](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a><span data-ttu-id="27f1e-196">EfCoreTemporalTable</span><span class="sxs-lookup"><span data-stu-id="27f1e-196">EfCoreTemporalTable</span></span>

<span data-ttu-id="27f1e-197">使用下列引進的擴充方法，輕鬆地在慣用的資料庫上執行時態性查詢：`AsTemporalAll()`、`AsTemporalAsOf(date)`、`AsTemporalFrom(startDate, endDate)`、`AsTemporalBetween(startDate, endDate)`、`AsTemporalContained(startDate, endDate)`。</span><span class="sxs-lookup"><span data-stu-id="27f1e-197">Easily perform temporal queries on your favourite database using introduced extension methods: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span></span> <span data-ttu-id="27f1e-198">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="27f1e-198">For EF Core: 3.</span></span>

[<span data-ttu-id="27f1e-199">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="27f1e-199">GitHub repository</span></span>](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="efcoretimetraveler"></a><span data-ttu-id="27f1e-200">EFCore.TimeTraveler</span><span class="sxs-lookup"><span data-stu-id="27f1e-200">EFCore.TimeTraveler</span></span>

<span data-ttu-id="27f1e-201">允許使用已定義的 EF Core 程式碼、實體和對應，對 [SQL Server 時態性記錄](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel)進行完整功能的 Entity Framework Core 查詢。</span><span class="sxs-lookup"><span data-stu-id="27f1e-201">Allow full-featured Entity Framework Core queries against [SQL Server Temporal History](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel) using the EF Core code, entities, and mappings you already have defined.</span></span>  <span data-ttu-id="27f1e-202">藉由將程式碼包裝在 `using (TemporalQuery.AsOf(targetDateTime)) {...}` 中來移動時間。</span><span class="sxs-lookup"><span data-stu-id="27f1e-202">Travel through time by wrapping your code in `using (TemporalQuery.AsOf(targetDateTime)) {...}`.</span></span> <span data-ttu-id="27f1e-203">適用於 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="27f1e-203">For EF Core: 3.</span></span>

[<span data-ttu-id="27f1e-204">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="27f1e-204">GitHub repository</span></span>](https://github.com/VantageSoftware/EFCore.TimeTraveler)


### <a name="entityframeworkcoretemporaltables"></a><span data-ttu-id="27f1e-205">EntityFrameworkCore.TemporalTables</span><span class="sxs-lookup"><span data-stu-id="27f1e-205">EntityFrameworkCore.TemporalTables</span></span>

<span data-ttu-id="27f1e-206">Entity Framework Core 的延伸模組程式庫，可讓使用 SQL Server 的開發人員輕鬆地使用時態表。</span><span class="sxs-lookup"><span data-stu-id="27f1e-206">Extension library for Entity Framework Core which allows developers who use SQL Server to easily use temporal tables.</span></span> <span data-ttu-id="27f1e-207">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="27f1e-207">For EF Core: 2.</span></span>

[<span data-ttu-id="27f1e-208">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="27f1e-208">GitHub repository</span></span>](https://github.com/findulov/EntityFrameworkCore.TemporalTables)


### <a name="entityframeworkcorecacheable"></a><span data-ttu-id="27f1e-209">EntityFrameworkCore.Cacheable</span><span class="sxs-lookup"><span data-stu-id="27f1e-209">EntityFrameworkCore.Cacheable</span></span>

<span data-ttu-id="27f1e-210">高效能第二層查詢快取。</span><span class="sxs-lookup"><span data-stu-id="27f1e-210">A high-performance second-level query cache.</span></span> <span data-ttu-id="27f1e-211">適用於 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="27f1e-211">For EF Core: 2.</span></span>

[<span data-ttu-id="27f1e-212">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="27f1e-212">GitHub repository</span></span>](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entity-framework-plus"></a><span data-ttu-id="27f1e-213">Entity Framework Plus</span><span class="sxs-lookup"><span data-stu-id="27f1e-213">Entity Framework Plus</span></span>

<span data-ttu-id="27f1e-214">使用下功能延伸您的 DbContext：包括篩選、稽核、快取、查詢未來、批次刪除、批次更新等。</span><span class="sxs-lookup"><span data-stu-id="27f1e-214">Extends your DbContext with features such as: Include Filter, Auditing, Caching, Query Future, Batch Delete, Batch Update, and more.</span></span> <span data-ttu-id="27f1e-215">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="27f1e-215">For EF Core: 2, 3.</span></span>

<span data-ttu-id="27f1e-216">[網站](https://entityframework-plus.net/)
[GitHub 存放庫](https://github.com/zzzprojects/EntityFramework-Plus)</span><span class="sxs-lookup"><span data-stu-id="27f1e-216">[Website](https://entityframework-plus.net/)
[GitHub repository](https://github.com/zzzprojects/EntityFramework-Plus)</span></span>

### <a name="entity-framework-extensions"></a><span data-ttu-id="27f1e-217">Entity Framework 擴充功能</span><span class="sxs-lookup"><span data-stu-id="27f1e-217">Entity Framework Extensions</span></span>

<span data-ttu-id="27f1e-218">使用高效能批次作業延伸您的 DbContext：BulkSaveChanges、BulkInsert、BulkUpdate、BulkDelete、BulkMerge 等。</span><span class="sxs-lookup"><span data-stu-id="27f1e-218">Extends your DbContext with high-performance bulk operations: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge, and more.</span></span> <span data-ttu-id="27f1e-219">適用於 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="27f1e-219">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="27f1e-220">網站</span><span class="sxs-lookup"><span data-stu-id="27f1e-220">Website</span></span>](https://entityframework-extensions.net/)

### <a name="expressionify"></a><span data-ttu-id="27f1e-221">Expressionify</span><span class="sxs-lookup"><span data-stu-id="27f1e-221">Expressionify</span></span>

<span data-ttu-id="27f1e-222">新增在 linq Lambda 中呼叫擴充方法的支援。</span><span class="sxs-lookup"><span data-stu-id="27f1e-222">Add support for calling extension methods in linq lambdas.</span></span> <span data-ttu-id="27f1e-223">適用於 EF Core：3.1</span><span class="sxs-lookup"><span data-stu-id="27f1e-223">For EF Core: 3.1</span></span>

[<span data-ttu-id="27f1e-224">GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="27f1e-224">GitHub repository</span></span>](https://github.com/ClaveConsulting/Expressionify)
