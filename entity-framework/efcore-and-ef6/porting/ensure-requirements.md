---
title: 從 EF6 移植到 EF Core -驗證需求
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d3b66f3c-9d10-4974-a090-8ad093c9a53d
uid: efcore-and-ef6/porting/ensure-requirements
ms.openlocfilehash: 07caa39e8a56db71e493331ea9f0286309abc52a
ms.sourcegitcommit: 7b7f774a5966b20d2aed5435a672a1edbe73b6fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2019
ms.locfileid: "69565353"
---
# <a name="before-porting-from-ef6-to-ef-core-validate-your-applications-requirements"></a><span data-ttu-id="e24b5-102">從 EF6 移植到 EF Core 之前:驗證您應用程式的需求</span><span class="sxs-lookup"><span data-stu-id="e24b5-102">Before porting from EF6 to EF Core: Validate your Application's Requirements</span></span>

<span data-ttu-id="e24b5-103">在開始移轉程序之前務必驗證 EF Core 符合您的應用程式的資料存取需求。</span><span class="sxs-lookup"><span data-stu-id="e24b5-103">Before you start the porting process it is important to validate that EF Core meets the data access requirements for your application.</span></span>

## <a name="missing-features"></a><span data-ttu-id="e24b5-104">遺漏的功能</span><span class="sxs-lookup"><span data-stu-id="e24b5-104">Missing features</span></span>

<span data-ttu-id="e24b5-105">請確定 EF Core 包含您需要在您的應用程式中使用的所有功能。</span><span class="sxs-lookup"><span data-stu-id="e24b5-105">Make sure that EF Core has all the features you need to use in your application.</span></span> <span data-ttu-id="e24b5-106">請參閱[功能比較](../features.md)的 EF Core 中設定的功能如何與 EF6 比較的詳細比較。</span><span class="sxs-lookup"><span data-stu-id="e24b5-106">See [Feature Comparison](../features.md) for a detailed comparison of how the feature set in EF Core compares to EF6.</span></span> <span data-ttu-id="e24b5-107">如果遺漏任何必要的功能，請確定您可以先移植到 EF Core 補償缺少這些功能。</span><span class="sxs-lookup"><span data-stu-id="e24b5-107">If any required features are missing, ensure that you can compensate for the lack of these features before porting to EF Core.</span></span>

## <a name="behavior-changes"></a><span data-ttu-id="e24b5-108">行為變更</span><span class="sxs-lookup"><span data-stu-id="e24b5-108">Behavior changes</span></span>

<span data-ttu-id="e24b5-109">這是 EF6 和 EF Core 之間的行為中的某些變更非完整清單。</span><span class="sxs-lookup"><span data-stu-id="e24b5-109">This is a non-exhaustive list of some changes in behavior between EF6 and EF Core.</span></span> <span data-ttu-id="e24b5-110">請務必牢記您的連接埠為您的應用程式因為它們可能會變更您的應用程式的行為，但是不會顯示以編譯錯誤互換之後到 EF Core 的方式。</span><span class="sxs-lookup"><span data-stu-id="e24b5-110">It is important to keep these in mind as your port your application as they may change the way your application behaves, but will not show up as compilation errors after swapping to EF Core.</span></span>

### <a name="dbsetaddattach-and-graph-behavior"></a><span data-ttu-id="e24b5-111">DbSet。新增/附加和圖形行為</span><span class="sxs-lookup"><span data-stu-id="e24b5-111">DbSet.Add/Attach and graph behavior</span></span>

<span data-ttu-id="e24b5-112">在 EF6 中, `DbSet.Add()`在實體上呼叫會導致以遞迴方式搜尋其導覽屬性中參考的所有實體。</span><span class="sxs-lookup"><span data-stu-id="e24b5-112">In EF6, calling `DbSet.Add()` on an entity results in a recursive search for all entities referenced in its navigation properties.</span></span> <span data-ttu-id="e24b5-113">任何找到的實體, 而且尚未被內容追蹤, 也會標示為已加入。</span><span class="sxs-lookup"><span data-stu-id="e24b5-113">Any entities that are found, and are not already tracked by the context, are also marked as added.</span></span> <span data-ttu-id="e24b5-114">`DbSet.Attach()`行為相同, 不同之處在于所有實體都會標示為未變更。</span><span class="sxs-lookup"><span data-stu-id="e24b5-114">`DbSet.Attach()` behaves the same, except all entities are marked as unchanged.</span></span>

<span data-ttu-id="e24b5-115">**EF Core 執行類似的遞迴搜尋，但有一些稍有不同的規則。**</span><span class="sxs-lookup"><span data-stu-id="e24b5-115">**EF Core performs a similar recursive search, but with some slightly different rules.**</span></span>

*  <span data-ttu-id="e24b5-116">根實體一律處於要求的狀態 (針對進行`DbSet.Add`新增, 且`DbSet.Attach`不變更)。</span><span class="sxs-lookup"><span data-stu-id="e24b5-116">The root entity is always in the requested state (added for `DbSet.Add` and unchanged for `DbSet.Attach`).</span></span>

*  <span data-ttu-id="e24b5-117">**針對在遞迴搜尋導覽屬性期間找到的實體:**</span><span class="sxs-lookup"><span data-stu-id="e24b5-117">**For entities that are found during the recursive search of navigation properties:**</span></span>

    *  <span data-ttu-id="e24b5-118">**如果實體的主要金鑰是存放區產生的**</span><span class="sxs-lookup"><span data-stu-id="e24b5-118">**If the primary key of the entity is store generated**</span></span>

        * <span data-ttu-id="e24b5-119">如果主要金鑰未設定為值, 則狀態會設定為 [已加入]。</span><span class="sxs-lookup"><span data-stu-id="e24b5-119">If the primary key is not set to a value, the state is set to added.</span></span> <span data-ttu-id="e24b5-120">如果主要金鑰值被指派屬性類型的`0` CLR 預設值 (例如, for `int`、 `null` for `string`等等), 則會將其視為「未設定」。</span><span class="sxs-lookup"><span data-stu-id="e24b5-120">The primary key value is considered "not set" if it is assigned the CLR default value for the property type (for example, `0` for `int`, `null` for `string`, etc.).</span></span>

        * <span data-ttu-id="e24b5-121">如果主要金鑰設定為值, 則狀態會設定為 [未變更]。</span><span class="sxs-lookup"><span data-stu-id="e24b5-121">If the primary key is set to a value, the state is set to unchanged.</span></span>

    *  <span data-ttu-id="e24b5-122">如果主要索引鍵不是資料庫產生的, 實體會置於與根相同的狀態。</span><span class="sxs-lookup"><span data-stu-id="e24b5-122">If the primary key is not database generated, the entity is put in the same state as the root.</span></span>

### <a name="code-first-database-initialization"></a><span data-ttu-id="e24b5-123">Code First 資料庫初始化</span><span class="sxs-lookup"><span data-stu-id="e24b5-123">Code First database initialization</span></span>

<span data-ttu-id="e24b5-124">**EF6 在選取資料庫連接和初始化資料庫方面, 有相當大的神奇之處。其中一些規則包括:**</span><span class="sxs-lookup"><span data-stu-id="e24b5-124">**EF6 has a significant amount of magic it performs around selecting the database connection and initializing the database. Some of these rules include:**</span></span>

* <span data-ttu-id="e24b5-125">如果沒有執行任何設定, EF6 會選取 SQL Express 或 LocalDb 上的資料庫。</span><span class="sxs-lookup"><span data-stu-id="e24b5-125">If no configuration is performed, EF6 will select a database on SQL Express or LocalDb.</span></span>

* <span data-ttu-id="e24b5-126">如果與內容同名的連接字串位於應用程式`App/Web.config`檔案中, 則會使用此連接。</span><span class="sxs-lookup"><span data-stu-id="e24b5-126">If a connection string with the same name as the context is in the applications `App/Web.config` file, this connection will be used.</span></span>

* <span data-ttu-id="e24b5-127">如果資料庫不存在, 則會加以建立。</span><span class="sxs-lookup"><span data-stu-id="e24b5-127">If the database does not exist, it is created.</span></span>

* <span data-ttu-id="e24b5-128">如果模型中沒有任何資料表存在於資料庫中, 則會將目前模型的架構加入至資料庫。</span><span class="sxs-lookup"><span data-stu-id="e24b5-128">If none of the tables from the model exist in the database, the schema for the current model is added to the database.</span></span> <span data-ttu-id="e24b5-129">如果已啟用遷移, 則會使用它們來建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="e24b5-129">If migrations are enabled, then they are used to create the database.</span></span>

* <span data-ttu-id="e24b5-130">如果資料庫存在, 而且 EF6 先前已建立架構, 則會檢查架構是否與目前的模型相容。</span><span class="sxs-lookup"><span data-stu-id="e24b5-130">If the database exists and EF6 had previously created the schema, then the schema is checked for compatibility with the current model.</span></span> <span data-ttu-id="e24b5-131">如果在建立架構之後, 模型已經變更, 就會擲回例外狀況 (exception)。</span><span class="sxs-lookup"><span data-stu-id="e24b5-131">An exception is thrown if the model has changed since the schema was created.</span></span>

<span data-ttu-id="e24b5-132">**EF Core 不會執行任何此識別常數。**</span><span class="sxs-lookup"><span data-stu-id="e24b5-132">**EF Core does not perform any of this magic.**</span></span>

* <span data-ttu-id="e24b5-133">資料庫連接必須在程式碼中明確設定。</span><span class="sxs-lookup"><span data-stu-id="e24b5-133">The database connection must be explicitly configured in code.</span></span>

* <span data-ttu-id="e24b5-134">不會執行任何初始化。</span><span class="sxs-lookup"><span data-stu-id="e24b5-134">No initialization is performed.</span></span> <span data-ttu-id="e24b5-135">您必須使用`DbContext.Database.Migrate()`來套用遷移 (或`DbContext.Database.EnsureCreated()`和`EnsureDeleted()` , 以建立/刪除資料庫, 而不使用遷移)。</span><span class="sxs-lookup"><span data-stu-id="e24b5-135">You must use `DbContext.Database.Migrate()` to apply migrations (or `DbContext.Database.EnsureCreated()` and `EnsureDeleted()` to create/delete the database without using migrations).</span></span>

### <a name="code-first-table-naming-convention"></a><span data-ttu-id="e24b5-136">Code First 資料表命名慣例</span><span class="sxs-lookup"><span data-stu-id="e24b5-136">Code First table naming convention</span></span>

<span data-ttu-id="e24b5-137">EF6 會透過複數表示服務來執行實體類別名稱, 以計算實體所對應的預設資料表名稱。</span><span class="sxs-lookup"><span data-stu-id="e24b5-137">EF6 runs the entity class name through a pluralization service to calculate the default table name that the entity is mapped to.</span></span>

<span data-ttu-id="e24b5-138">EF Core 使用的名稱`DbSet`實體中公開衍生內容的屬性。</span><span class="sxs-lookup"><span data-stu-id="e24b5-138">EF Core uses the name of the `DbSet` property that the entity is exposed in on the derived context.</span></span> <span data-ttu-id="e24b5-139">如果實體`DbSet`沒有屬性, 則會使用類別名稱。</span><span class="sxs-lookup"><span data-stu-id="e24b5-139">If the entity does not have a `DbSet` property, then the class name is used.</span></span>
