---
title: 資料植入-EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/02/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/data-seeding
ms.openlocfilehash: 0b11b6b3104b74e09c60c9c455e22f164df493c7
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655759"
---
# <a name="data-seeding"></a><span data-ttu-id="77ac1-102">資料植入</span><span class="sxs-lookup"><span data-stu-id="77ac1-102">Data Seeding</span></span>

<span data-ttu-id="77ac1-103">資料植入是以初始資料集填入資料庫的程式。</span><span class="sxs-lookup"><span data-stu-id="77ac1-103">Data seeding is the process of populating a database with an initial set of data.</span></span>

<span data-ttu-id="77ac1-104">有數種方式可以在 EF Core 中完成：</span><span class="sxs-lookup"><span data-stu-id="77ac1-104">There are several ways this can be accomplished in EF Core:</span></span>

* <span data-ttu-id="77ac1-105">模型種子資料</span><span class="sxs-lookup"><span data-stu-id="77ac1-105">Model seed data</span></span>
* <span data-ttu-id="77ac1-106">手動遷移自訂</span><span class="sxs-lookup"><span data-stu-id="77ac1-106">Manual migration customization</span></span>
* <span data-ttu-id="77ac1-107">自訂初始化邏輯</span><span class="sxs-lookup"><span data-stu-id="77ac1-107">Custom initialization logic</span></span>

## <a name="model-seed-data"></a><span data-ttu-id="77ac1-108">模型種子資料</span><span class="sxs-lookup"><span data-stu-id="77ac1-108">Model seed data</span></span>

> [!NOTE]
> <span data-ttu-id="77ac1-109">此功能是 EF Core 2.1 中的新功能。</span><span class="sxs-lookup"><span data-stu-id="77ac1-109">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="77ac1-110">不同于 EF6，在 EF Core 中，植入資料可以與實體類型相關聯，做為模型設定的一部分。</span><span class="sxs-lookup"><span data-stu-id="77ac1-110">Unlike in EF6, in EF Core, seeding data can be associated with an entity type as part of the model configuration.</span></span> <span data-ttu-id="77ac1-111">然後 EF Core[遷移](xref:core/managing-schemas/migrations/index)可以自動計算將資料庫升級至新版本的模型時，需要套用的插入、更新或刪除作業。</span><span class="sxs-lookup"><span data-stu-id="77ac1-111">Then EF Core [migrations](xref:core/managing-schemas/migrations/index) can automatically compute what insert, update or delete operations need to be applied when upgrading the database to a new version of the model.</span></span>

> [!NOTE]
> <span data-ttu-id="77ac1-112">只有在決定應該執行哪一種作業以將種子資料轉換成所需的狀態時，遷移才會考慮模型變更。</span><span class="sxs-lookup"><span data-stu-id="77ac1-112">Migrations only considers model changes when determining what operation should be performed to get the seed data into the desired state.</span></span> <span data-ttu-id="77ac1-113">因此，在遷移外部執行之資料的任何變更可能會遺失或造成錯誤。</span><span class="sxs-lookup"><span data-stu-id="77ac1-113">Thus any changes to the data performed outside of migrations might be lost or cause an error.</span></span>

<span data-ttu-id="77ac1-114">例如，這會在 `OnModelCreating`中設定 `Blog` 的種子資料：</span><span class="sxs-lookup"><span data-stu-id="77ac1-114">As an example, this will configure seed data for a `Blog` in `OnModelCreating`:</span></span>

[!code-csharp[BlogSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

<span data-ttu-id="77ac1-115">若要加入具有關聯性的實體，必須指定外鍵值：</span><span class="sxs-lookup"><span data-stu-id="77ac1-115">To add entities that have a relationship the foreign key values need to be specified:</span></span>

[!code-csharp[PostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=PostSeed)]

<span data-ttu-id="77ac1-116">如果實體類型具有「陰影狀態」中的任何屬性，則可以使用匿名類別來提供值：</span><span class="sxs-lookup"><span data-stu-id="77ac1-116">If the entity type has any properties in shadow state an anonymous class can be used to provide the values:</span></span>

[!code-csharp[AnonymousPostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=AnonymousPostSeed)]

<span data-ttu-id="77ac1-117">擁有的實體類型可以用類似的方式植入：</span><span class="sxs-lookup"><span data-stu-id="77ac1-117">Owned entity types can be seeded in a similar fashion:</span></span>

[!code-csharp[OwnedTypeSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=OwnedTypeSeed)]

<span data-ttu-id="77ac1-118">如需詳細內容，請參閱[完整的範例專案](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding)。</span><span class="sxs-lookup"><span data-stu-id="77ac1-118">See the [full sample project](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding) for more context.</span></span>

<span data-ttu-id="77ac1-119">將資料加入至模型之後，應該使用[遷移](xref:core/managing-schemas/migrations/index)來套用變更。</span><span class="sxs-lookup"><span data-stu-id="77ac1-119">Once the data has been added to the model, [migrations](xref:core/managing-schemas/migrations/index) should be used to apply the changes.</span></span>

> [!TIP]
> <span data-ttu-id="77ac1-120">如果您需要將遷移套用為自動化部署的一部分，您可以建立可在執行前預覽的[SQL 腳本](xref:core/managing-schemas/migrations/index#generate-sql-scripts)。</span><span class="sxs-lookup"><span data-stu-id="77ac1-120">If you need to apply migrations as part of an automated deployment you can [create a SQL script](xref:core/managing-schemas/migrations/index#generate-sql-scripts) that can be previewed before execution.</span></span>

<span data-ttu-id="77ac1-121">或者，您可以使用 `context.Database.EnsureCreated()` 來建立包含種子資料的新資料庫（例如測試資料庫），或使用記憶體內部提供者或任何非關聯資料庫。</span><span class="sxs-lookup"><span data-stu-id="77ac1-121">Alternatively, you can use `context.Database.EnsureCreated()` to create a new database containing the seed data, for example for a test database or when using the in-memory provider or any non-relation database.</span></span> <span data-ttu-id="77ac1-122">請注意，如果資料庫已經存在，`EnsureCreated()` 將不會更新資料庫中的架構或植入資料。</span><span class="sxs-lookup"><span data-stu-id="77ac1-122">Note that if the database already exists, `EnsureCreated()` will neither update the schema nor seed data in the database.</span></span> <span data-ttu-id="77ac1-123">針對關係資料庫，如果您打算使用遷移，則不應呼叫 `EnsureCreated()`。</span><span class="sxs-lookup"><span data-stu-id="77ac1-123">For relational databases you shouldn't call `EnsureCreated()` if you plan to use Migrations.</span></span>

### <a name="limitations-of-model-seed-data"></a><span data-ttu-id="77ac1-124">模型種子資料的限制</span><span class="sxs-lookup"><span data-stu-id="77ac1-124">Limitations of model seed data</span></span>

<span data-ttu-id="77ac1-125">這種類型的種子資料是由遷移所管理，而且必須在不連接到資料庫的情況下，才會產生更新已在資料庫中之資料的腳本。</span><span class="sxs-lookup"><span data-stu-id="77ac1-125">This type of seed data is managed by migrations and the script to update the data that's already in the database needs to be generated without connecting to the database.</span></span> <span data-ttu-id="77ac1-126">這會造成一些限制：</span><span class="sxs-lookup"><span data-stu-id="77ac1-126">This imposes some restrictions:</span></span>

* <span data-ttu-id="77ac1-127">您必須指定主要金鑰值，即使它通常是由資料庫所產生。</span><span class="sxs-lookup"><span data-stu-id="77ac1-127">The primary key value needs to be specified even if it's usually generated by the database.</span></span> <span data-ttu-id="77ac1-128">它會用來偵測遷移之間的資料變更。</span><span class="sxs-lookup"><span data-stu-id="77ac1-128">It will be used to detect data changes between migrations.</span></span>
* <span data-ttu-id="77ac1-129">如果主要金鑰以任何方式變更，則會移除先前植入的資料。</span><span class="sxs-lookup"><span data-stu-id="77ac1-129">Previously seeded data will be removed if the primary key is changed in any way.</span></span>

<span data-ttu-id="77ac1-130">因此，這項功能最適用于不應在遷移之外變更的靜態資料，而且不會相依于資料庫中的任何其他專案，例如郵遞區號。</span><span class="sxs-lookup"><span data-stu-id="77ac1-130">Therefore this feature is most useful for static data that's not expected to change outside of migrations and does not depend on anything else in the database, for example ZIP codes.</span></span>

<span data-ttu-id="77ac1-131">如果您的案例包含下列任何一項，建議使用最後一節中所述的自訂初始化邏輯：</span><span class="sxs-lookup"><span data-stu-id="77ac1-131">If your scenario includes any of the following it is recommended to use custom initialization logic described in the last section:</span></span>

* <span data-ttu-id="77ac1-132">用於測試的暫存資料</span><span class="sxs-lookup"><span data-stu-id="77ac1-132">Temporary data for testing</span></span>
* <span data-ttu-id="77ac1-133">相依于資料庫狀態的資料</span><span class="sxs-lookup"><span data-stu-id="77ac1-133">Data that depends on database state</span></span>
* <span data-ttu-id="77ac1-134">需要由資料庫產生索引鍵值的資料，包括使用替代索引鍵做為身分識別的實體</span><span class="sxs-lookup"><span data-stu-id="77ac1-134">Data that needs key values to be generated by the database, including entities that use alternate keys as the identity</span></span>
* <span data-ttu-id="77ac1-135">需要自訂轉換（不會由[值轉換](xref:core/modeling/value-conversions)處理）的資料，例如一些密碼雜湊</span><span class="sxs-lookup"><span data-stu-id="77ac1-135">Data that requires custom transformation (that is not handled by [value conversions](xref:core/modeling/value-conversions)), such as some password hashing</span></span>
* <span data-ttu-id="77ac1-136">需要呼叫外部 API 的資料，例如 ASP.NET Core 身分識別角色和使用者建立</span><span class="sxs-lookup"><span data-stu-id="77ac1-136">Data that requires calls to external API, such as ASP.NET Core Identity roles and users creation</span></span>

## <a name="manual-migration-customization"></a><span data-ttu-id="77ac1-137">手動遷移自訂</span><span class="sxs-lookup"><span data-stu-id="77ac1-137">Manual migration customization</span></span>

<span data-ttu-id="77ac1-138">當遷移新增時，會將 `HasData` 所指定的資料變更轉換成 `InsertData()`、`UpdateData()`和 `DeleteData()`的呼叫。</span><span class="sxs-lookup"><span data-stu-id="77ac1-138">When a migration is added the changes to the data specified with `HasData` are transformed to calls to `InsertData()`, `UpdateData()`, and `DeleteData()`.</span></span> <span data-ttu-id="77ac1-139">解決 `HasData` 限制的其中一種方法，就是手動將這些呼叫或[自訂作業](xref:core/managing-schemas/migrations/operations)新增至遷移。</span><span class="sxs-lookup"><span data-stu-id="77ac1-139">One way of working around some of the limitations of `HasData` is to manually add these calls or [custom operations](xref:core/managing-schemas/migrations/operations) to the migration instead.</span></span>

[!code-csharp[CustomInsert](../../../samples/core/Modeling/DataSeeding/Migrations/20181102235626_Initial.cs?name=CustomInsert)]

## <a name="custom-initialization-logic"></a><span data-ttu-id="77ac1-140">自訂初始化邏輯</span><span class="sxs-lookup"><span data-stu-id="77ac1-140">Custom initialization logic</span></span>

<span data-ttu-id="77ac1-141">執行資料植入的簡單且功能強大的方式，就是在主要應用程式邏輯開始執行之前，先使用[`DbContext.SaveChanges()`](xref:core/saving/index) 。</span><span class="sxs-lookup"><span data-stu-id="77ac1-141">A straightforward and powerful way to perform data seeding is to use [`DbContext.SaveChanges()`](xref:core/saving/index) before the main application logic begins execution.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataSeeding/Program.cs?name=CustomSeeding)]

> [!WARNING]
> <span data-ttu-id="77ac1-142">植入程式碼不應該是正常應用程式執行的一部分，因為這可能會在多個實例正在執行時造成並行問題，而且也需要具有修改資料庫架構之許可權的應用程式。</span><span class="sxs-lookup"><span data-stu-id="77ac1-142">The seeding code should not be part of the normal app execution as this can cause concurrency issues when multiple instances are running and would also require the app having permission to modify the database schema.</span></span>

<span data-ttu-id="77ac1-143">視部署的條件約束而定，可以透過不同的方式來執行初始化程式碼：</span><span class="sxs-lookup"><span data-stu-id="77ac1-143">Depending on the constraints of your deployment the initialization code can be executed in different ways:</span></span>

* <span data-ttu-id="77ac1-144">在本機執行初始化應用程式</span><span class="sxs-lookup"><span data-stu-id="77ac1-144">Running the initialization app locally</span></span>
* <span data-ttu-id="77ac1-145">使用主要應用程式部署初始化應用程式、叫用初始化常式，以及停用或移除初始化應用程式。</span><span class="sxs-lookup"><span data-stu-id="77ac1-145">Deploying the initialization app with the main app, invoking the initialization routine and disabling or removing the initialization app.</span></span>

<span data-ttu-id="77ac1-146">這通常可以使用[發行設定檔](/aspnet/core/host-and-deploy/visual-studio-publish-profiles)來自動化。</span><span class="sxs-lookup"><span data-stu-id="77ac1-146">This can usually be automated by using [publish profiles](/aspnet/core/host-and-deploy/visual-studio-publish-profiles).</span></span>
