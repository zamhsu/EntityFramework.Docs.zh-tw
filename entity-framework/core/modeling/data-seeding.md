---
title: 資料植入-EF Core
description: 使用資料植入來以 Entity Framework Core 的初始資料集填入資料庫
author: AndriySvyryd
ms.date: 11/02/2018
uid: core/modeling/data-seeding
ms.openlocfilehash: fefa6232496cd250d52a436971251f59af09f5c6
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429672"
---
# <a name="data-seeding"></a><span data-ttu-id="1e07a-103">資料植入</span><span class="sxs-lookup"><span data-stu-id="1e07a-103">Data Seeding</span></span>

<span data-ttu-id="1e07a-104">資料植入是以初始資料集填入資料庫的程式。</span><span class="sxs-lookup"><span data-stu-id="1e07a-104">Data seeding is the process of populating a database with an initial set of data.</span></span>

<span data-ttu-id="1e07a-105">有幾種方式可以在 EF Core 中完成：</span><span class="sxs-lookup"><span data-stu-id="1e07a-105">There are several ways this can be accomplished in EF Core:</span></span>

* <span data-ttu-id="1e07a-106">模型種子資料</span><span class="sxs-lookup"><span data-stu-id="1e07a-106">Model seed data</span></span>
* <span data-ttu-id="1e07a-107">手動遷移自訂</span><span class="sxs-lookup"><span data-stu-id="1e07a-107">Manual migration customization</span></span>
* <span data-ttu-id="1e07a-108">自訂初始化邏輯</span><span class="sxs-lookup"><span data-stu-id="1e07a-108">Custom initialization logic</span></span>

## <a name="model-seed-data"></a><span data-ttu-id="1e07a-109">模型種子資料</span><span class="sxs-lookup"><span data-stu-id="1e07a-109">Model seed data</span></span>

<span data-ttu-id="1e07a-110">不同于 EF6，在 EF Core 中，植入資料可以與實體類型相關聯，做為模型設定的一部分。</span><span class="sxs-lookup"><span data-stu-id="1e07a-110">Unlike in EF6, in EF Core, seeding data can be associated with an entity type as part of the model configuration.</span></span> <span data-ttu-id="1e07a-111">然後 EF Core 的 [遷移](xref:core/managing-schemas/migrations/index) 可以自動計算將資料庫升級至新版本的模型時，必須套用的插入、更新或刪除作業。</span><span class="sxs-lookup"><span data-stu-id="1e07a-111">Then EF Core [migrations](xref:core/managing-schemas/migrations/index) can automatically compute what insert, update or delete operations need to be applied when upgrading the database to a new version of the model.</span></span>

> [!NOTE]
> <span data-ttu-id="1e07a-112">當您決定應該執行哪一種作業來讓種子資料進入預期狀態時，遷移只會考慮模型變更。</span><span class="sxs-lookup"><span data-stu-id="1e07a-112">Migrations only considers model changes when determining what operation should be performed to get the seed data into the desired state.</span></span> <span data-ttu-id="1e07a-113">因此，在遷移外部所執行之資料的任何變更都可能遺失或造成錯誤。</span><span class="sxs-lookup"><span data-stu-id="1e07a-113">Thus any changes to the data performed outside of migrations might be lost or cause an error.</span></span>

<span data-ttu-id="1e07a-114">例如，這會在中設定的種子資料 `Blog` `OnModelCreating` ：</span><span class="sxs-lookup"><span data-stu-id="1e07a-114">As an example, this will configure seed data for a `Blog` in `OnModelCreating`:</span></span>

[!code-csharp[BlogSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

<span data-ttu-id="1e07a-115">若要加入具有關聯性的實體，必須指定外鍵值：</span><span class="sxs-lookup"><span data-stu-id="1e07a-115">To add entities that have a relationship the foreign key values need to be specified:</span></span>

[!code-csharp[PostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=PostSeed)]

<span data-ttu-id="1e07a-116">如果實體類型在陰影狀態中有任何屬性，則可以使用匿名類別來提供這些值：</span><span class="sxs-lookup"><span data-stu-id="1e07a-116">If the entity type has any properties in shadow state an anonymous class can be used to provide the values:</span></span>

[!code-csharp[AnonymousPostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=AnonymousPostSeed)]

<span data-ttu-id="1e07a-117">擁有的實體類型可以用類似的方式植入：</span><span class="sxs-lookup"><span data-stu-id="1e07a-117">Owned entity types can be seeded in a similar fashion:</span></span>

[!code-csharp[OwnedTypeSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=OwnedTypeSeed)]

<span data-ttu-id="1e07a-118">請參閱 [完整的範例專案](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding) ，以取得更多內容。</span><span class="sxs-lookup"><span data-stu-id="1e07a-118">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding) for more context.</span></span>

<span data-ttu-id="1e07a-119">一旦將資料加入至模型之後，就應該使用 [遷移](xref:core/managing-schemas/migrations/index) 來套用變更。</span><span class="sxs-lookup"><span data-stu-id="1e07a-119">Once the data has been added to the model, [migrations](xref:core/managing-schemas/migrations/index) should be used to apply the changes.</span></span>

> [!TIP]
> <span data-ttu-id="1e07a-120">如果您需要將遷移套用為自動化部署的一部分，您可以建立可在執行前預覽的 [SQL 腳本](xref:core/managing-schemas/migrations/index#generate-sql-scripts) 。</span><span class="sxs-lookup"><span data-stu-id="1e07a-120">If you need to apply migrations as part of an automated deployment you can [create a SQL script](xref:core/managing-schemas/migrations/index#generate-sql-scripts) that can be previewed before execution.</span></span>

<span data-ttu-id="1e07a-121">或者，您可以使用 `context.Database.EnsureCreated()` 來建立包含種子資料的新資料庫，例如針對測試資料庫，或是在使用記憶體中提供者或任何非關聯資料庫時。</span><span class="sxs-lookup"><span data-stu-id="1e07a-121">Alternatively, you can use `context.Database.EnsureCreated()` to create a new database containing the seed data, for example for a test database or when using the in-memory provider or any non-relation database.</span></span> <span data-ttu-id="1e07a-122">請注意，如果資料庫已經存在， `EnsureCreated()` 將不會在資料庫中更新架構或植入資料。</span><span class="sxs-lookup"><span data-stu-id="1e07a-122">Note that if the database already exists, `EnsureCreated()` will neither update the schema nor seed data in the database.</span></span> <span data-ttu-id="1e07a-123">如果您打算使用遷移，則不應呼叫關係資料庫 `EnsureCreated()` 。</span><span class="sxs-lookup"><span data-stu-id="1e07a-123">For relational databases you shouldn't call `EnsureCreated()` if you plan to use Migrations.</span></span>

### <a name="limitations-of-model-seed-data"></a><span data-ttu-id="1e07a-124">模型種子資料的限制</span><span class="sxs-lookup"><span data-stu-id="1e07a-124">Limitations of model seed data</span></span>

<span data-ttu-id="1e07a-125">這種類型的種子資料是由遷移所管理，而且需要產生腳本來更新資料庫中已有的資料，而不需要連接到資料庫。</span><span class="sxs-lookup"><span data-stu-id="1e07a-125">This type of seed data is managed by migrations and the script to update the data that's already in the database needs to be generated without connecting to the database.</span></span> <span data-ttu-id="1e07a-126">這會造成一些限制：</span><span class="sxs-lookup"><span data-stu-id="1e07a-126">This imposes some restrictions:</span></span>

* <span data-ttu-id="1e07a-127">您必須指定主鍵值（即使它通常是由資料庫所產生）。</span><span class="sxs-lookup"><span data-stu-id="1e07a-127">The primary key value needs to be specified even if it's usually generated by the database.</span></span> <span data-ttu-id="1e07a-128">它將用來偵測遷移之間的資料變更。</span><span class="sxs-lookup"><span data-stu-id="1e07a-128">It will be used to detect data changes between migrations.</span></span>
* <span data-ttu-id="1e07a-129">如果主鍵以任何方式變更，則會移除先前植入的資料。</span><span class="sxs-lookup"><span data-stu-id="1e07a-129">Previously seeded data will be removed if the primary key is changed in any way.</span></span>

<span data-ttu-id="1e07a-130">因此，這項功能最適用于不需要在遷移之外變更的靜態資料，而且不會相依于資料庫中的任何其他資料，例如郵遞區號。</span><span class="sxs-lookup"><span data-stu-id="1e07a-130">Therefore this feature is most useful for static data that's not expected to change outside of migrations and does not depend on anything else in the database, for example ZIP codes.</span></span>

<span data-ttu-id="1e07a-131">如果您的案例包含下列任何一項，建議您使用上一節中所述的自訂初始化邏輯：</span><span class="sxs-lookup"><span data-stu-id="1e07a-131">If your scenario includes any of the following it is recommended to use custom initialization logic described in the last section:</span></span>

* <span data-ttu-id="1e07a-132">用於測試的暫存資料</span><span class="sxs-lookup"><span data-stu-id="1e07a-132">Temporary data for testing</span></span>
* <span data-ttu-id="1e07a-133">相依于資料庫狀態的資料</span><span class="sxs-lookup"><span data-stu-id="1e07a-133">Data that depends on database state</span></span>
* <span data-ttu-id="1e07a-134">大量 (植入資料的資料會在遷移快照集中捕捉，而大型資料可能很快會導致大型檔案和效能降低) 。</span><span class="sxs-lookup"><span data-stu-id="1e07a-134">Data that is large (seeding data gets captured in migration snapshots, and large data can quickly lead to huge files and degraded performance).</span></span>
* <span data-ttu-id="1e07a-135">需要資料庫產生的索引鍵值的資料，包括使用替代索引鍵做為身分識別的實體</span><span class="sxs-lookup"><span data-stu-id="1e07a-135">Data that needs key values to be generated by the database, including entities that use alternate keys as the identity</span></span>
* <span data-ttu-id="1e07a-136">需要自訂轉換 (的資料，不是由 [值轉換](xref:core/modeling/value-conversions)) 處理，例如一些密碼雜湊</span><span class="sxs-lookup"><span data-stu-id="1e07a-136">Data that requires custom transformation (that is not handled by [value conversions](xref:core/modeling/value-conversions)), such as some password hashing</span></span>
* <span data-ttu-id="1e07a-137">需要呼叫外部 API 的資料，例如 ASP.NET Core 身分識別角色和使用者建立</span><span class="sxs-lookup"><span data-stu-id="1e07a-137">Data that requires calls to external API, such as ASP.NET Core Identity roles and users creation</span></span>

## <a name="manual-migration-customization"></a><span data-ttu-id="1e07a-138">手動遷移自訂</span><span class="sxs-lookup"><span data-stu-id="1e07a-138">Manual migration customization</span></span>

<span data-ttu-id="1e07a-139">當您將所指定資料的變更轉換為、和的呼叫時，會將所指定之資料的變更 `HasData` 轉換成 `InsertData()` `UpdateData()` `DeleteData()` 。</span><span class="sxs-lookup"><span data-stu-id="1e07a-139">When a migration is added the changes to the data specified with `HasData` are transformed to calls to `InsertData()`, `UpdateData()`, and `DeleteData()`.</span></span> <span data-ttu-id="1e07a-140">解決某些限制的其中一種方式 `HasData` ，是手動將這些呼叫或 [自訂作業](xref:core/managing-schemas/migrations/operations) 加入至遷移作業。</span><span class="sxs-lookup"><span data-stu-id="1e07a-140">One way of working around some of the limitations of `HasData` is to manually add these calls or [custom operations](xref:core/managing-schemas/migrations/operations) to the migration instead.</span></span>

[!code-csharp[CustomInsert](../../../samples/core/Modeling/DataSeeding/Migrations/20181102235626_Initial.cs?name=CustomInsert)]

## <a name="custom-initialization-logic"></a><span data-ttu-id="1e07a-141">自訂初始化邏輯</span><span class="sxs-lookup"><span data-stu-id="1e07a-141">Custom initialization logic</span></span>

<span data-ttu-id="1e07a-142">執行資料植入的簡單、強大方式，是在 [`DbContext.SaveChanges()`](xref:core/saving/index) 主要應用程式邏輯開始執行之前使用。</span><span class="sxs-lookup"><span data-stu-id="1e07a-142">A straightforward and powerful way to perform data seeding is to use [`DbContext.SaveChanges()`](xref:core/saving/index) before the main application logic begins execution.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataSeeding/Program.cs?name=CustomSeeding)]

> [!WARNING]
> <span data-ttu-id="1e07a-143">植入程式碼不應該是一般應用程式執行的一部分，因為這可能會在多個實例正在執行時造成並行處理問題，而且也會要求應用程式具有修改資料庫架構的許可權。</span><span class="sxs-lookup"><span data-stu-id="1e07a-143">The seeding code should not be part of the normal app execution as this can cause concurrency issues when multiple instances are running and would also require the app having permission to modify the database schema.</span></span>

<span data-ttu-id="1e07a-144">根據您的部署條件約束，可以用不同的方式執行初始化程式碼：</span><span class="sxs-lookup"><span data-stu-id="1e07a-144">Depending on the constraints of your deployment the initialization code can be executed in different ways:</span></span>

* <span data-ttu-id="1e07a-145">在本機執行初始化應用程式</span><span class="sxs-lookup"><span data-stu-id="1e07a-145">Running the initialization app locally</span></span>
* <span data-ttu-id="1e07a-146">使用主應用程式部署初始化應用程式、叫用初始化常式，以及停用或移除初始化應用程式。</span><span class="sxs-lookup"><span data-stu-id="1e07a-146">Deploying the initialization app with the main app, invoking the initialization routine and disabling or removing the initialization app.</span></span>

<span data-ttu-id="1e07a-147">這通常可以使用 [發行設定檔](/aspnet/core/host-and-deploy/visual-studio-publish-profiles)進行自動化。</span><span class="sxs-lookup"><span data-stu-id="1e07a-147">This can usually be automated by using [publish profiles](/aspnet/core/host-and-deploy/visual-studio-publish-profiles).</span></span>
