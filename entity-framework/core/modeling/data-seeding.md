---
title: 資料植入的 EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/02/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/data-seeding
ms.openlocfilehash: 1c450b142573368d043430f55a3144b6696a8691
ms.sourcegitcommit: b4a5ed177b86bf7f81602106dab6b4acc18dfc18
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2019
ms.locfileid: "54316630"
---
# <a name="data-seeding"></a><span data-ttu-id="dac4f-102">資料植入</span><span class="sxs-lookup"><span data-stu-id="dac4f-102">Data Seeding</span></span>

<span data-ttu-id="dac4f-103">資料植入是填入一組初始的資料與資料庫的程序。</span><span class="sxs-lookup"><span data-stu-id="dac4f-103">Data seeding is the process of populating a database with an initial set of data.</span></span>

<span data-ttu-id="dac4f-104">有數種方式，這可在 EF Core:</span><span class="sxs-lookup"><span data-stu-id="dac4f-104">There are several ways this can be accomplished in EF Core:</span></span>
* <span data-ttu-id="dac4f-105">模型種子資料</span><span class="sxs-lookup"><span data-stu-id="dac4f-105">Model seed data</span></span>
* <span data-ttu-id="dac4f-106">手動移轉自訂</span><span class="sxs-lookup"><span data-stu-id="dac4f-106">Manual migration customization</span></span>
* <span data-ttu-id="dac4f-107">自訂的初始化邏輯</span><span class="sxs-lookup"><span data-stu-id="dac4f-107">Custom initialization logic</span></span>

## <a name="model-seed-data"></a><span data-ttu-id="dac4f-108">模型種子資料</span><span class="sxs-lookup"><span data-stu-id="dac4f-108">Model seed data</span></span>

> [!NOTE]
> <span data-ttu-id="dac4f-109">這項功能是在 EF Core 2.1 中新功能。</span><span class="sxs-lookup"><span data-stu-id="dac4f-109">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="dac4f-110">不同於 EF6，EF core 中植入資料可以是相關聯的模型組態一部分的實體類型。</span><span class="sxs-lookup"><span data-stu-id="dac4f-110">Unlike in EF6, in EF Core, seeding data can be associated with an entity type as part of the model configuration.</span></span> <span data-ttu-id="dac4f-111">然後 EF Core[移轉](xref:core/managing-schemas/migrations/index)可以自動計算項目插入、 更新或刪除作業必須升級至新版的模型資料庫時套用。</span><span class="sxs-lookup"><span data-stu-id="dac4f-111">Then EF Core [migrations](xref:core/managing-schemas/migrations/index) can automatically compute what insert, update or delete operations need to be applied when upgrading the database to a new version of the model.</span></span>

> [!NOTE]
> <span data-ttu-id="dac4f-112">判斷應該執行哪一個作業，以取得種子資料到期望的狀態時，移轉只會將模型變更。</span><span class="sxs-lookup"><span data-stu-id="dac4f-112">Migrations only considers model changes when determining what operation should be performed to get the seed data into the desired state.</span></span> <span data-ttu-id="dac4f-113">因此資料移轉之外執行的任何變更可能會遺失或造成錯誤。</span><span class="sxs-lookup"><span data-stu-id="dac4f-113">Thus any changes to the data performed outside of migrations might be lost or cause an error.</span></span>

<span data-ttu-id="dac4f-114">例如，這會設定種子資料，如`Blog`在`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="dac4f-114">As an example, this will configure seed data for a `Blog` in `OnModelCreating`:</span></span>

[!code-csharp[BlogSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

<span data-ttu-id="dac4f-115">若要新增具有關聯性外部索引鍵值的實體必須指定：</span><span class="sxs-lookup"><span data-stu-id="dac4f-115">To add entities that have a relationship the foreign key values need to be specified:</span></span>

[!code-csharp[PostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=PostSeed)]

<span data-ttu-id="dac4f-116">如果實體類型會有任何屬性，在匿名類別可用來提供值的陰影狀態：</span><span class="sxs-lookup"><span data-stu-id="dac4f-116">If the entity type has any properties in shadow state an anonymous class can be used to provide the values:</span></span>

[!code-csharp[AnonymousPostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=AnonymousPostSeed)]

<span data-ttu-id="dac4f-117">擁有的實體類型可以做為種子以類似的方式：</span><span class="sxs-lookup"><span data-stu-id="dac4f-117">Owned entity types can be seeded in a similar fashion:</span></span>

[!code-csharp[OwnedTypeSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=OwnedTypeSeed)]

<span data-ttu-id="dac4f-118">請參閱[完整的範例專案](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding)詳細內容。</span><span class="sxs-lookup"><span data-stu-id="dac4f-118">See the [full sample project](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding) for more context.</span></span>

<span data-ttu-id="dac4f-119">一旦資料加入至模型，[移轉](xref:core/managing-schemas/migrations/index)應該用來套用變更。</span><span class="sxs-lookup"><span data-stu-id="dac4f-119">Once the data has been added to the model, [migrations](xref:core/managing-schemas/migrations/index) should be used to apply the changes.</span></span>

> [!TIP]
> <span data-ttu-id="dac4f-120">如果您要自動化的部署過程中套用移轉即可[建立的 SQL 指令碼](xref:core/managing-schemas/migrations/index#generate-sql-scripts)，可以預覽之前執行。</span><span class="sxs-lookup"><span data-stu-id="dac4f-120">If you need to apply migrations as part of an automated deployment you can [create a SQL script](xref:core/managing-schemas/migrations/index#generate-sql-scripts) that can be previewed before execution.</span></span>

<span data-ttu-id="dac4f-121">或者，您可以使用`context.Database.EnsureCreated()`來建立新的資料庫包含種子資料，例如針對測試資料庫，或使用的記憶體提供者或任何非關聯性的資料庫。</span><span class="sxs-lookup"><span data-stu-id="dac4f-121">Alternatively, you can use `context.Database.EnsureCreated()` to create a new database containing the seed data, for example for a test database or when using the in-memory provider or any non-relation database.</span></span> <span data-ttu-id="dac4f-122">請注意，如果資料庫已經存在，`EnsureCreated()`都不會更新資料庫中的結構描述或種子資料。</span><span class="sxs-lookup"><span data-stu-id="dac4f-122">Note that if the database already exists, `EnsureCreated()` will neither update the schema nor seed data in the database.</span></span> <span data-ttu-id="dac4f-123">關聯式資料庫，您不應該呼叫`EnsureCreated()`如果您打算使用移轉。</span><span class="sxs-lookup"><span data-stu-id="dac4f-123">For relational databases you shouldn't call `EnsureCreated()` if you plan to use Migrations.</span></span>

### <a name="limitations-of-model-seed-data"></a><span data-ttu-id="dac4f-124">模型種子資料的限制</span><span class="sxs-lookup"><span data-stu-id="dac4f-124">Limitations of model seed data</span></span>

<span data-ttu-id="dac4f-125">這種類型的種子資料由管理移轉，需要產生而不需要連線到資料庫的指令碼，以更新資料庫中的資料。</span><span class="sxs-lookup"><span data-stu-id="dac4f-125">This type of seed data is managed by migrations and the script to update the data that's already in the database needs to be generated without connecting to the database.</span></span> <span data-ttu-id="dac4f-126">這具有一些限制︰</span><span class="sxs-lookup"><span data-stu-id="dac4f-126">This imposes some restrictions:</span></span>
* <span data-ttu-id="dac4f-127">主索引鍵值，就必須指定即使通常會由資料庫產生。</span><span class="sxs-lookup"><span data-stu-id="dac4f-127">The primary key value needs to be specified even if it's usually generated by the database.</span></span> <span data-ttu-id="dac4f-128">它會用來偵測之間移轉的資料變更。</span><span class="sxs-lookup"><span data-stu-id="dac4f-128">It will be used to detect data changes between migrations.</span></span>
* <span data-ttu-id="dac4f-129">先前植入的資料將會移除，如果主索引鍵以任何方式變更。</span><span class="sxs-lookup"><span data-stu-id="dac4f-129">Previously seeded data will be removed if the primary key is changed in any way.</span></span>

<span data-ttu-id="dac4f-130">因此這項功能是最適合用於靜態資料具有不應該變更移轉之外，不需依賴任何在資料庫中，例如郵遞區號的其他項目。</span><span class="sxs-lookup"><span data-stu-id="dac4f-130">Therefore this feature is most useful for static data that's not expected to change outside of migrations and does not depend on anything else in the database, for example ZIP codes.</span></span>

<span data-ttu-id="dac4f-131">如果您的案例包含下列任何一項建議使用自訂的初始化邏輯中的最後一節所述：</span><span class="sxs-lookup"><span data-stu-id="dac4f-131">If your scenario includes any of the following it is recommended to use custom initialization logic described in the last section:</span></span>
* <span data-ttu-id="dac4f-132">暫存資料來進行測試</span><span class="sxs-lookup"><span data-stu-id="dac4f-132">Temporary data for testing</span></span>
* <span data-ttu-id="dac4f-133">取決於資料庫狀態的資料</span><span class="sxs-lookup"><span data-stu-id="dac4f-133">Data that depends on database state</span></span>
* <span data-ttu-id="dac4f-134">需要資料庫，包括替代索引鍵做為身分識別的實體所產生的索引鍵值的資料</span><span class="sxs-lookup"><span data-stu-id="dac4f-134">Data that needs key values to be generated by the database, including entities that use alternate keys as the identity</span></span>
* <span data-ttu-id="dac4f-135">需要自訂的轉換的資料 (可不由[值轉換](xref:core/modeling/value-conversions))，例如某些密碼雜湊</span><span class="sxs-lookup"><span data-stu-id="dac4f-135">Data that requires custom transformation (that is not handled by [value conversions](xref:core/modeling/value-conversions)), such as some password hashing</span></span>
* <span data-ttu-id="dac4f-136">需要呼叫外部 API，例如 ASP.NET Core 身分識別的角色和使用者建立的資料</span><span class="sxs-lookup"><span data-stu-id="dac4f-136">Data that requires calls to external API, such as ASP.NET Core Identity roles and users creation</span></span>

## <a name="manual-migration-customization"></a><span data-ttu-id="dac4f-137">手動移轉自訂</span><span class="sxs-lookup"><span data-stu-id="dac4f-137">Manual migration customization</span></span>

<span data-ttu-id="dac4f-138">當移轉新增時所做的變更與指定的資料`HasData`會進行轉換，以呼叫`InsertData()`， `UpdateData()`，和`DeleteData()`。</span><span class="sxs-lookup"><span data-stu-id="dac4f-138">When a migration is added the changes to the data specified with `HasData` are transformed to calls to `InsertData()`, `UpdateData()`, and `DeleteData()`.</span></span> <span data-ttu-id="dac4f-139">其中一個方法解決某些限制，其中`HasData`是以手動方式新增這些呼叫或[自訂作業](xref:core/managing-schemas/migrations/operations)移轉至改。</span><span class="sxs-lookup"><span data-stu-id="dac4f-139">One way of working around some of the limitations of `HasData` is to manually add these calls or [custom operations](xref:core/managing-schemas/migrations/operations) to the migration instead.</span></span>

[!code-csharp[CustomInsert](../../../samples/core/Modeling/DataSeeding/Migrations/20181102235626_Initial.cs?name=CustomInsert)]

## <a name="custom-initialization-logic"></a><span data-ttu-id="dac4f-140">自訂的初始化邏輯</span><span class="sxs-lookup"><span data-stu-id="dac4f-140">Custom initialization logic</span></span>

<span data-ttu-id="dac4f-141">執行資料植入的簡單且功能強大的方法是使用[ `DbContext.SaveChanges()` ](xref:core/saving/index)邏輯開始執行之前將主應用程式。</span><span class="sxs-lookup"><span data-stu-id="dac4f-141">A straightforward and powerful way to perform data seeding is to use [`DbContext.SaveChanges()`](xref:core/saving/index) before the main application logic begins execution.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataSeeding/Program.cs?name=CustomSeeding)]

> [!WARNING]
> <span data-ttu-id="dac4f-142">植入的程式碼不應該在一般應用程式執行期間，當多個執行個體執行，而且也需要擁有修改資料庫結構描述的權限的應用程式時，這可能會造成並行處理問題。</span><span class="sxs-lookup"><span data-stu-id="dac4f-142">The seeding code should not be part of the normal app execution as this can cause concurrency issues when multiple instances are running and would also require the app having permission to modify the database schema.</span></span>

<span data-ttu-id="dac4f-143">根據您的部署的條件約束可執行的初始化程式碼以不同的方式：</span><span class="sxs-lookup"><span data-stu-id="dac4f-143">Depending on the constraints of your deployment the initialization code can be executed in different ways:</span></span>
* <span data-ttu-id="dac4f-144">在本機執行初始化應用程式</span><span class="sxs-lookup"><span data-stu-id="dac4f-144">Running the initialization app locally</span></span>
* <span data-ttu-id="dac4f-145">部署主要應用程式時，叫用的初始化常式和停用或移除初始化應用程式初始化應用程式。</span><span class="sxs-lookup"><span data-stu-id="dac4f-145">Deploying the initialization app with the main app, invoking the initialization routine and disabling or removing the initialization app.</span></span>

<span data-ttu-id="dac4f-146">這通常是使用自動化[發行設定檔](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/visual-studio-publish-profiles)。</span><span class="sxs-lookup"><span data-stu-id="dac4f-146">This can usually be automated by using [publish profiles](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/visual-studio-publish-profiles).</span></span>
