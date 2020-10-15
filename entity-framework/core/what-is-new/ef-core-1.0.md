---
title: EF Core 1.0 中的新增功能 - EF Core
description: Entity Framework Core 1.0 的變更和改進
author: ajcvickers
ms.date: 10/27/2016
uid: core/what-is-new/ef-core-1.0
ms.openlocfilehash: 4066dced67a54770664b9b1348adb6ac2622ce1a
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062928"
---
# <a name="features-included-in-ef-core-10"></a><span data-ttu-id="10caa-103">EF Core 1.0 中包含的功能</span><span class="sxs-lookup"><span data-stu-id="10caa-103">Features included in EF Core 1.0</span></span>

## <a name="platforms"></a><span data-ttu-id="10caa-104">平台</span><span class="sxs-lookup"><span data-stu-id="10caa-104">Platforms</span></span>

### <a name="net-framework-451"></a><span data-ttu-id="10caa-105">.NET Framework 4.5.1</span><span class="sxs-lookup"><span data-stu-id="10caa-105">.NET Framework 4.5.1</span></span>

<span data-ttu-id="10caa-106">包括主控台、 WPF、WinForms、ASP.NET 4 等。</span><span class="sxs-lookup"><span data-stu-id="10caa-106">Includes Console, WPF, WinForms, ASP.NET 4, etc.</span></span>

### <a name="net-standard-13"></a><span data-ttu-id="10caa-107">.NET Standard 1.3</span><span class="sxs-lookup"><span data-stu-id="10caa-107">.NET Standard 1.3</span></span>

<span data-ttu-id="10caa-108">包括以 Windows、OSX 和 Linux 上的 .NET Framework 與 .NET Core 兩者為目標的 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="10caa-108">Including ASP.NET Core targeting both .NET Framework and .NET Core on Windows, OSX, and Linux.</span></span>

## <a name="modelling"></a><span data-ttu-id="10caa-109">模型建構</span><span class="sxs-lookup"><span data-stu-id="10caa-109">Modelling</span></span>

### <a name="basic-modelling"></a><span data-ttu-id="10caa-110">基本模型建構</span><span class="sxs-lookup"><span data-stu-id="10caa-110">Basic modelling</span></span>

<span data-ttu-id="10caa-111">以具有常見純量型別 (`int`、`string` 等) get/set 屬性的 POCO 實體為基礎。</span><span class="sxs-lookup"><span data-stu-id="10caa-111">Based on POCO entities with get/set properties of common scalar types (`int`, `string`, etc.).</span></span>

### <a name="relationships-and-navigation-properties"></a><span data-ttu-id="10caa-112">關聯性與瀏覽屬性</span><span class="sxs-lookup"><span data-stu-id="10caa-112">Relationships and navigation properties</span></span>

<span data-ttu-id="10caa-113">您可以在以外部索引鍵為基礎的模型中指定一對多與一對零或一對一關聯性。</span><span class="sxs-lookup"><span data-stu-id="10caa-113">One-to-many and One-to-zero-or-one relationships can be specified in the model based on a foreign key.</span></span> <span data-ttu-id="10caa-114">簡單集合或參考型別的瀏覽屬性可以與這些關聯性關聯。</span><span class="sxs-lookup"><span data-stu-id="10caa-114">Navigation properties of simple collection or reference types can be associated with these relationships.</span></span>

### <a name="built-in-conventions"></a><span data-ttu-id="10caa-115">內建慣例</span><span class="sxs-lookup"><span data-stu-id="10caa-115">Built-in conventions</span></span>

<span data-ttu-id="10caa-116">這些會根據實體類別的圖形建置初始模型。</span><span class="sxs-lookup"><span data-stu-id="10caa-116">These build an initial model based on the shape of the entity classes.</span></span>

### <a name="fluent-api"></a><span data-ttu-id="10caa-117">Fluent API</span><span class="sxs-lookup"><span data-stu-id="10caa-117">Fluent API</span></span>

<span data-ttu-id="10caa-118">可讓您在您的上下文中覆寫 `OnModelCreating` 方法，以進一步設定由慣例所探索到的模型。</span><span class="sxs-lookup"><span data-stu-id="10caa-118">Allows you to override the `OnModelCreating` method on your context to further configure the model that was discovered by convention.</span></span>

### <a name="data-annotations"></a><span data-ttu-id="10caa-119">資料註解</span><span class="sxs-lookup"><span data-stu-id="10caa-119">Data annotations</span></span>

<span data-ttu-id="10caa-120">這些屬性可新增到您的實體類別/屬性，且會影響 EF 模型。</span><span class="sxs-lookup"><span data-stu-id="10caa-120">Are attributes that can be added to your entity classes/properties and will influence the EF model.</span></span> <span data-ttu-id="10caa-121">例如，新增 `[Required]` 可讓 EF 知道該屬性為必要。</span><span class="sxs-lookup"><span data-stu-id="10caa-121">For example, adding `[Required]` will let EF know that a property is required.</span></span>

### <a name="relational-table-mapping"></a><span data-ttu-id="10caa-122">關聯式資料表對應</span><span class="sxs-lookup"><span data-stu-id="10caa-122">Relational Table mapping</span></span>

<span data-ttu-id="10caa-123">可讓實體對應到資料表/資料行。</span><span class="sxs-lookup"><span data-stu-id="10caa-123">Allows entities to be mapped to tables/columns.</span></span>

### <a name="key-value-generation"></a><span data-ttu-id="10caa-124">索引鍵值產生</span><span class="sxs-lookup"><span data-stu-id="10caa-124">Key value generation</span></span>

<span data-ttu-id="10caa-125">包括用戶端產生與資料庫產生。</span><span class="sxs-lookup"><span data-stu-id="10caa-125">Including client-side generation and database generation.</span></span>

### <a name="database-generated-values"></a><span data-ttu-id="10caa-126">資料庫產生的值</span><span class="sxs-lookup"><span data-stu-id="10caa-126">Database generated values</span></span>

<span data-ttu-id="10caa-127">可讓值由資料庫在插入時產生 (預設值) 或在更新時產生 (計算資料行)。</span><span class="sxs-lookup"><span data-stu-id="10caa-127">Allows for values to be generated by the database on insert (default values) or update (computed columns).</span></span>

### <a name="sequences-in-sql-server"></a><span data-ttu-id="10caa-128">SQL Server 中的序列</span><span class="sxs-lookup"><span data-stu-id="10caa-128">Sequences in SQL Server</span></span>

<span data-ttu-id="10caa-129">可讓您在模型中定義序列物件。</span><span class="sxs-lookup"><span data-stu-id="10caa-129">Allows for sequence objects to be defined in the model.</span></span>

### <a name="unique-constraints"></a><span data-ttu-id="10caa-130">UNIQUE 條件約束</span><span class="sxs-lookup"><span data-stu-id="10caa-130">Unique constraints</span></span>

<span data-ttu-id="10caa-131">可讓您定義替代索引鍵，並讓您定義以該索引鍵為目標的關聯性。</span><span class="sxs-lookup"><span data-stu-id="10caa-131">Allows for the definition of alternate keys and the ability to define relationships that target that key.</span></span>

### <a name="indexes"></a><span data-ttu-id="10caa-132">索引</span><span class="sxs-lookup"><span data-stu-id="10caa-132">Indexes</span></span>

<span data-ttu-id="10caa-133">在模型中定義索引會自動在資料庫中引進索引。</span><span class="sxs-lookup"><span data-stu-id="10caa-133">Defining indexes in the model automatically introduces indexes in the database.</span></span> <span data-ttu-id="10caa-134">也支援唯一索引。</span><span class="sxs-lookup"><span data-stu-id="10caa-134">Unique indexes are also supported.</span></span>

### <a name="shadow-state-properties"></a><span data-ttu-id="10caa-135">陰影狀態屬性</span><span class="sxs-lookup"><span data-stu-id="10caa-135">Shadow state properties</span></span>

<span data-ttu-id="10caa-136">可讓您在未宣告且未儲存在 .NET 類別中但可由 EF Core 追蹤並更新的模型中定義屬性。</span><span class="sxs-lookup"><span data-stu-id="10caa-136">Allows for properties to be defined in the model that are not declared and are not stored in the .NET class but can be tracked and updated by EF Core.</span></span> <span data-ttu-id="10caa-137">通常用於外部索引鍵屬性 (當不想要在物件中公開這些時)。</span><span class="sxs-lookup"><span data-stu-id="10caa-137">Commonly used for foreign key properties when exposing these in the object is not desired.</span></span>

### <a name="table-per-hierarchy-inheritance-pattern"></a><span data-ttu-id="10caa-138">每一階層資料表繼承模式</span><span class="sxs-lookup"><span data-stu-id="10caa-138">Table-Per-Hierarchy inheritance pattern</span></span>

<span data-ttu-id="10caa-139">可讓您使用鑑別子資料行來識別資料庫中指定資料列的實體類型，將繼承階層中的實體儲存到單一資料表。</span><span class="sxs-lookup"><span data-stu-id="10caa-139">Allows entities in an inheritance hierarchy to be saved to a single table using a discriminator column to identify they entity type for a given record in the database.</span></span>

### <a name="model-validation"></a><span data-ttu-id="10caa-140">模型驗證</span><span class="sxs-lookup"><span data-stu-id="10caa-140">Model validation</span></span>

<span data-ttu-id="10caa-141">偵測模型中的無效模式並提供實用的錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="10caa-141">Detects invalid patterns in the model and provides helpful error messages.</span></span>

## <a name="change-tracking"></a><span data-ttu-id="10caa-142">變更追蹤</span><span class="sxs-lookup"><span data-stu-id="10caa-142">Change tracking</span></span>

### <a name="snapshot-change-tracking"></a><span data-ttu-id="10caa-143">快照集變更追蹤</span><span class="sxs-lookup"><span data-stu-id="10caa-143">Snapshot change tracking</span></span>

<span data-ttu-id="10caa-144">可讓您透過比較目前狀態與原始狀態的複本 (快照集)，來自動偵測實體中的變更。</span><span class="sxs-lookup"><span data-stu-id="10caa-144">Allows changes in entities to be detected automatically by comparing current state against a copy (snapshot) of the original state.</span></span>

### <a name="notification-change-tracking"></a><span data-ttu-id="10caa-145">通知變更追蹤</span><span class="sxs-lookup"><span data-stu-id="10caa-145">Notification change tracking</span></span>

<span data-ttu-id="10caa-146">可讓您的實體在屬性值被修改時通知變更追蹤者。</span><span class="sxs-lookup"><span data-stu-id="10caa-146">Allows your entities to notify the change tracker when property values are modified.</span></span>

### <a name="accessing-tracked-state"></a><span data-ttu-id="10caa-147">存取追蹤狀態</span><span class="sxs-lookup"><span data-stu-id="10caa-147">Accessing tracked state</span></span>

<span data-ttu-id="10caa-148">透過 `DbContext.Entry` 與 `DbContext.ChangeTracker`。</span><span class="sxs-lookup"><span data-stu-id="10caa-148">Via `DbContext.Entry` and `DbContext.ChangeTracker`.</span></span>

### <a name="attaching-detached-entitiesgraphs"></a><span data-ttu-id="10caa-149">附加取消附加的實體/圖表</span><span class="sxs-lookup"><span data-stu-id="10caa-149">Attaching detached entities/graphs</span></span>

<span data-ttu-id="10caa-150">新的 `DbContext.AttachGraph` API 有助於將實體重新附加到上下文，以減少使用新的/已修改的實體。</span><span class="sxs-lookup"><span data-stu-id="10caa-150">The new `DbContext.AttachGraph` API helps re-attach entities to a context in order to save new/modified entities.</span></span>

## <a name="saving-data"></a><span data-ttu-id="10caa-151">儲存資料</span><span class="sxs-lookup"><span data-stu-id="10caa-151">Saving data</span></span>

### <a name="basic-save-functionality"></a><span data-ttu-id="10caa-152">基本儲存功能</span><span class="sxs-lookup"><span data-stu-id="10caa-152">Basic save functionality</span></span>

<span data-ttu-id="10caa-153">可讓您將對實體執行個體所做的變更儲存到資料庫中。</span><span class="sxs-lookup"><span data-stu-id="10caa-153">Allows changes to entity instances to be persisted to the database.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="10caa-154">開放式並行存取</span><span class="sxs-lookup"><span data-stu-id="10caa-154">Optimistic Concurrency</span></span>

<span data-ttu-id="10caa-155">保護資料避免被使用者所進行的變更覆寫，因為資料是從資料庫所擷取的。</span><span class="sxs-lookup"><span data-stu-id="10caa-155">Protects against overwriting changes made by another user since data was fetched from the database.</span></span>

### <a name="async-savechanges"></a><span data-ttu-id="10caa-156">非同步儲存變更</span><span class="sxs-lookup"><span data-stu-id="10caa-156">Async SaveChanges</span></span>

<span data-ttu-id="10caa-157">在資料庫處理從 `SaveChanges` 發出的命令時，可以釋放目前的執行緒以處理其他要求。</span><span class="sxs-lookup"><span data-stu-id="10caa-157">Can free up the current thread to process other requests while the database processes the commands issued from `SaveChanges`.</span></span>

### <a name="database-transactions"></a><span data-ttu-id="10caa-158">資料庫交易</span><span class="sxs-lookup"><span data-stu-id="10caa-158">Database Transactions</span></span>

<span data-ttu-id="10caa-159">表示 `SaveChanges` 一律都是不可部分完成 (表示它會完全成功，或不會對資料庫做任何變更)。</span><span class="sxs-lookup"><span data-stu-id="10caa-159">Means that `SaveChanges` is always atomic (meaning it either completely succeeds, or no changes are made to the database).</span></span> <span data-ttu-id="10caa-160">也有交易相關 API 可在內容執行個體等之間共用交易。</span><span class="sxs-lookup"><span data-stu-id="10caa-160">There are also transaction related APIs to allow sharing transactions between context instances etc.</span></span>

### <a name="relational-batching-of-statements"></a><span data-ttu-id="10caa-161">關聯性：陳述式的批次處理</span><span class="sxs-lookup"><span data-stu-id="10caa-161">Relational: Batching of statements</span></span>

<span data-ttu-id="10caa-162">透過以批次方式在單一來回行程中對資料庫執行多個 INSERT/UPDATE/DELETE 命令。</span><span class="sxs-lookup"><span data-stu-id="10caa-162">Provides better performance by batching up multiple INSERT/UPDATE/DELETE commands into a single roundtrip to the database.</span></span>

## <a name="query"></a><span data-ttu-id="10caa-163">查詢</span><span class="sxs-lookup"><span data-stu-id="10caa-163">Query</span></span>

### <a name="basic-linq-support"></a><span data-ttu-id="10caa-164">基本 LINQ 支援</span><span class="sxs-lookup"><span data-stu-id="10caa-164">Basic LINQ support</span></span>

<span data-ttu-id="10caa-165">提供使用 LINQ 從資料庫擷取資料的功能。</span><span class="sxs-lookup"><span data-stu-id="10caa-165">Provides the ability to use LINQ to retrieve data from the database.</span></span>

### <a name="mixed-clientserver-evaluation"></a><span data-ttu-id="10caa-166">混合用戶端/伺服器評估</span><span class="sxs-lookup"><span data-stu-id="10caa-166">Mixed client/server evaluation</span></span>

<span data-ttu-id="10caa-167">可讓查詢包含無法在資料庫中評估因而必須在資料擷取到記憶體之後評估的邏輯。</span><span class="sxs-lookup"><span data-stu-id="10caa-167">Enables queries to contain logic that cannot be evaluated in the database, and must therefore be evaluated after the data is retrieved into memory.</span></span>

### <a name="notracking"></a><span data-ttu-id="10caa-168">NoTracking</span><span class="sxs-lookup"><span data-stu-id="10caa-168">NoTracking</span></span>

<span data-ttu-id="10caa-169">查詢可讓您在內容不需要監視對實體執行個體所做的變更 (這在唯讀結果的情況下非常實用) 時，有更快的查詢執行效能。</span><span class="sxs-lookup"><span data-stu-id="10caa-169">Queries enables quicker query execution when the context does not need to monitor for changes to the entity instances (this is useful if the results are read-only).</span></span>

### <a name="eager-loading"></a><span data-ttu-id="10caa-170">積極式載入</span><span class="sxs-lookup"><span data-stu-id="10caa-170">Eager loading</span></span>

<span data-ttu-id="10caa-171">提供 `Include` 與 `ThenInclude` 方法，以識別也應該在查詢時擷取的相關資料。</span><span class="sxs-lookup"><span data-stu-id="10caa-171">Provides the `Include` and `ThenInclude` methods to identify related data that should also be fetched when querying.</span></span>

### <a name="async-query"></a><span data-ttu-id="10caa-172">非同步查詢</span><span class="sxs-lookup"><span data-stu-id="10caa-172">Async query</span></span>

<span data-ttu-id="10caa-173">在資料庫處理查詢時，可以釋放目前的執行緒 (與其關聯資源) 以處理其他要求。</span><span class="sxs-lookup"><span data-stu-id="10caa-173">Can free up the current thread (and it's associated resources) to process other requests while the database processes the query.</span></span>

### <a name="raw-sql-queries"></a><span data-ttu-id="10caa-174">原始 SQL 查詢</span><span class="sxs-lookup"><span data-stu-id="10caa-174">Raw SQL queries</span></span>

<span data-ttu-id="10caa-175">提供 `DbSet.FromSql` 方法，以使用原始 SQL 查詢來擷取資料。</span><span class="sxs-lookup"><span data-stu-id="10caa-175">Provides the `DbSet.FromSql` method to use raw SQL queries to fetch data.</span></span> <span data-ttu-id="10caa-176">這些查詢也可以使用 LINQ 來撰寫。</span><span class="sxs-lookup"><span data-stu-id="10caa-176">These queries can also be composed on using LINQ.</span></span>

## <a name="database-schema-management"></a><span data-ttu-id="10caa-177">資料庫結構描述管理</span><span class="sxs-lookup"><span data-stu-id="10caa-177">Database schema management</span></span>

### <a name="database-creationdeletion-apis"></a><span data-ttu-id="10caa-178">資料庫建立/刪除 API</span><span class="sxs-lookup"><span data-stu-id="10caa-178">Database creation/deletion APIs</span></span>

<span data-ttu-id="10caa-179">大部分是針對您想要在不使用移轉的情況下快速建立/刪除資料庫的測試所設計。</span><span class="sxs-lookup"><span data-stu-id="10caa-179">Are mostly designed for testing where you want to quickly create/delete the database without using migrations.</span></span>

### <a name="relational-database-migrations"></a><span data-ttu-id="10caa-180">關聯式資料庫移轉</span><span class="sxs-lookup"><span data-stu-id="10caa-180">Relational database migrations</span></span>

<span data-ttu-id="10caa-181">可讓關聯式資料庫結構描述隨著您的模型變更而發展。</span><span class="sxs-lookup"><span data-stu-id="10caa-181">Allow a relational database schema to evolve overtime as your model changes.</span></span>

### <a name="reverse-engineer-from-database"></a><span data-ttu-id="10caa-182">資料庫還原工程</span><span class="sxs-lookup"><span data-stu-id="10caa-182">Reverse engineer from database</span></span>

<span data-ttu-id="10caa-183">根據現有的關聯式資料庫結構描述架構 EF 模型。</span><span class="sxs-lookup"><span data-stu-id="10caa-183">Scaffolds an EF model based on an existing relational database schema.</span></span>

## <a name="database-providers"></a><span data-ttu-id="10caa-184">資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="10caa-184">Database providers</span></span>

### <a name="sql-server"></a><span data-ttu-id="10caa-185">SQL Server</span><span class="sxs-lookup"><span data-stu-id="10caa-185">SQL Server</span></span>

<span data-ttu-id="10caa-186">連線到 Microsoft SQL Server 2008 與更新的版本。</span><span class="sxs-lookup"><span data-stu-id="10caa-186">Connects to Microsoft SQL Server 2008 onwards.</span></span>

### <a name="sqlite"></a><span data-ttu-id="10caa-187">SQLite</span><span class="sxs-lookup"><span data-stu-id="10caa-187">SQLite</span></span>

<span data-ttu-id="10caa-188">連線到 SQLite 3 資料庫。</span><span class="sxs-lookup"><span data-stu-id="10caa-188">Connects to a SQLite 3 database.</span></span>

### <a name="in-memory"></a><span data-ttu-id="10caa-189">記憶體內</span><span class="sxs-lookup"><span data-stu-id="10caa-189">In-Memory</span></span>

<span data-ttu-id="10caa-190">設計為在不需要連線到實際資料庫的情況下輕鬆進行測試。</span><span class="sxs-lookup"><span data-stu-id="10caa-190">Is designed to easily enable testing without connecting to a real database.</span></span>

### <a name="3rd-party-providers"></a><span data-ttu-id="10caa-191">協力廠商提供者</span><span class="sxs-lookup"><span data-stu-id="10caa-191">3rd party providers</span></span>

<span data-ttu-id="10caa-192">有數個提供者提供其他資料庫引擎。</span><span class="sxs-lookup"><span data-stu-id="10caa-192">Several providers are available for other database engines.</span></span> <span data-ttu-id="10caa-193">如需完整清單，請參閱[資料庫提供者](xref:core/providers/index)。</span><span class="sxs-lookup"><span data-stu-id="10caa-193">See [Database Providers](xref:core/providers/index) for a complete list.</span></span>
