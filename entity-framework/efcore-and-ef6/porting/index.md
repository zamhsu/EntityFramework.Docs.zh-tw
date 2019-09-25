---
title: 從 EF6 移植至 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 826b58bd-77b0-4bbc-bfcd-24d1ed3a8f38
uid: efcore-and-ef6/porting/index
ms.openlocfilehash: 42e40ce769a67a987883027e1807ec7eaeb4ad7a
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71198022"
---
# <a name="porting-from-ef6-to-ef-core"></a><span data-ttu-id="467e9-102">從 EF6 移植至 EF Core</span><span class="sxs-lookup"><span data-stu-id="467e9-102">Porting from EF6 to EF Core</span></span>

<span data-ttu-id="467e9-103">因為 EF Core 在本質上有所變更，我們不建議嘗試將 EF6 應用程式移至 EF Core，除非您有充足的理由進行該變更。</span><span class="sxs-lookup"><span data-stu-id="467e9-103">Because of the fundamental changes in EF Core we do not recommend attempting to move an EF6 application to EF Core unless you have a compelling reason to make the change.</span></span>
<span data-ttu-id="467e9-104">您應該將從 EF6 移至 EF Core 視為移植，而非升級。</span><span class="sxs-lookup"><span data-stu-id="467e9-104">You should view the move from EF6 to EF Core as a port rather than an upgrade.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="467e9-105">在開始移植程序之前，請務必確認 EF Core 符合應用程式的資料存取需求。</span><span class="sxs-lookup"><span data-stu-id="467e9-105">Before you start the porting process it is important to validate that EF Core meets the data access requirements for your application.</span></span>

## <a name="missing-features"></a><span data-ttu-id="467e9-106">遺漏的功能</span><span class="sxs-lookup"><span data-stu-id="467e9-106">Missing features</span></span>

<span data-ttu-id="467e9-107">請確定 EF Core 具有應用程式中所需的所有功能。</span><span class="sxs-lookup"><span data-stu-id="467e9-107">Make sure that EF Core has all the features you need to use in your application.</span></span> <span data-ttu-id="467e9-108">如需 EF Core 中的功能集與 EF6 相比的詳細比較，請參閱[功能比較](xref:efcore-and-ef6/index)。</span><span class="sxs-lookup"><span data-stu-id="467e9-108">See [Feature Comparison](xref:efcore-and-ef6/index) for a detailed comparison of how the feature set in EF Core compares to EF6.</span></span> <span data-ttu-id="467e9-109">如果遺漏任何必要的功能，請確保在移植到 EF Core 之前可以彌補這些功能的不足。</span><span class="sxs-lookup"><span data-stu-id="467e9-109">If any required features are missing, ensure that you can compensate for the lack of these features before porting to EF Core.</span></span>

## <a name="behavior-changes"></a><span data-ttu-id="467e9-110">行為變更</span><span class="sxs-lookup"><span data-stu-id="467e9-110">Behavior changes</span></span>

<span data-ttu-id="467e9-111">這是 EF6 和 EF Core 之間一些行為變更的不完整清單。</span><span class="sxs-lookup"><span data-stu-id="467e9-111">This is a non-exhaustive list of some changes in behavior between EF6 and EF Core.</span></span> <span data-ttu-id="467e9-112">在移植應用程式時，請務必牢記這些問題，因為它們可能會變更應用程式的行為方式，但在切換到 EF Core 之後，不會顯示為編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="467e9-112">It is important to keep these in mind as your port your application as they may change the way your application behaves, but will not show up as compilation errors after swapping to EF Core.</span></span>

### <a name="dbsetaddattach-and-graph-behavior"></a><span data-ttu-id="467e9-113">DbSet.Add/附加和圖形行為</span><span class="sxs-lookup"><span data-stu-id="467e9-113">DbSet.Add/Attach and graph behavior</span></span>

<span data-ttu-id="467e9-114">在 EF6 中，在實體上呼叫 `DbSet.Add()` 會導致以遞迴方式搜尋其導覽屬性中參考的所有實體。</span><span class="sxs-lookup"><span data-stu-id="467e9-114">In EF6, calling `DbSet.Add()` on an entity results in a recursive search for all entities referenced in its navigation properties.</span></span> <span data-ttu-id="467e9-115">任何找到的實體 (而且尚未執行內容追蹤)，也會標示為已加入。</span><span class="sxs-lookup"><span data-stu-id="467e9-115">Any entities that are found, and are not already tracked by the context, are also marked as added.</span></span> <span data-ttu-id="467e9-116">`DbSet.Attach()` 行為相同，不同之處在於所有實體都會標示為未變更。</span><span class="sxs-lookup"><span data-stu-id="467e9-116">`DbSet.Attach()` behaves the same, except all entities are marked as unchanged.</span></span>

<span data-ttu-id="467e9-117">**EF Core 會執行類似的遞迴搜尋，但有一些略有不同的規則。**</span><span class="sxs-lookup"><span data-stu-id="467e9-117">**EF Core performs a similar recursive search, but with some slightly different rules.**</span></span>

*  <span data-ttu-id="467e9-118">根實體一律處於要求的狀態 (針對 `DbSet.Add` 進行新增，針對 `DbSet.Attach` 則未變更)。</span><span class="sxs-lookup"><span data-stu-id="467e9-118">The root entity is always in the requested state (added for `DbSet.Add` and unchanged for `DbSet.Attach`).</span></span>

*  <span data-ttu-id="467e9-119">**針對在遞迴搜尋導覽屬性期間找到的實體：**</span><span class="sxs-lookup"><span data-stu-id="467e9-119">**For entities that are found during the recursive search of navigation properties:**</span></span>

    *  <span data-ttu-id="467e9-120">**如果實體的主索引鍵是存放區產生的**</span><span class="sxs-lookup"><span data-stu-id="467e9-120">**If the primary key of the entity is store generated**</span></span>

        * <span data-ttu-id="467e9-121">如果主索引鍵未設定為值，則狀態會設定為「已加入」。</span><span class="sxs-lookup"><span data-stu-id="467e9-121">If the primary key is not set to a value, the state is set to added.</span></span> <span data-ttu-id="467e9-122">如果為主索引鍵指派了屬性類型的 CLR 預設值 (例如，`0` 代表 `int`，`null` 代表 `string` 等)，則會將它視為「未設定」。</span><span class="sxs-lookup"><span data-stu-id="467e9-122">The primary key value is considered "not set" if it is assigned the CLR default value for the property type (for example, `0` for `int`, `null` for `string`, etc.).</span></span>

        * <span data-ttu-id="467e9-123">如果主索引鍵設定為值，則狀態會設定為「未變更」。</span><span class="sxs-lookup"><span data-stu-id="467e9-123">If the primary key is set to a value, the state is set to unchanged.</span></span>

    *  <span data-ttu-id="467e9-124">如果主索引鍵不是資料庫產生的，實體會置於與根相同的狀態。</span><span class="sxs-lookup"><span data-stu-id="467e9-124">If the primary key is not database generated, the entity is put in the same state as the root.</span></span>

### <a name="code-first-database-initialization"></a><span data-ttu-id="467e9-125">Code First 資料庫初始化</span><span class="sxs-lookup"><span data-stu-id="467e9-125">Code First database initialization</span></span>

<span data-ttu-id="467e9-126">**EF6 在選取資料庫連接和初始化資料庫方面，有非常神奇之處。其中一些規則包括：**</span><span class="sxs-lookup"><span data-stu-id="467e9-126">**EF6 has a significant amount of magic it performs around selecting the database connection and initializing the database. Some of these rules include:**</span></span>

* <span data-ttu-id="467e9-127">如果沒有執行任何設定，EF6 將在 SQL Express 或 LocalDb 上選取一個資料庫。</span><span class="sxs-lookup"><span data-stu-id="467e9-127">If no configuration is performed, EF6 will select a database on SQL Express or LocalDb.</span></span>

* <span data-ttu-id="467e9-128">如果與執行內容同名的連接字串位於應用程式 `App/Web.config` 檔案中，則會使用此連線。</span><span class="sxs-lookup"><span data-stu-id="467e9-128">If a connection string with the same name as the context is in the applications `App/Web.config` file, this connection will be used.</span></span>

* <span data-ttu-id="467e9-129">如果資料庫不存在，則會建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="467e9-129">If the database does not exist, it is created.</span></span>

* <span data-ttu-id="467e9-130">如果模型中沒有任何資料表存在於資料庫中，則會將目前模型的結構描述新增至資料庫。</span><span class="sxs-lookup"><span data-stu-id="467e9-130">If none of the tables from the model exist in the database, the schema for the current model is added to the database.</span></span> <span data-ttu-id="467e9-131">如果已啟用移轉，則會使用它們來建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="467e9-131">If migrations are enabled, then they are used to create the database.</span></span>

* <span data-ttu-id="467e9-132">如果資料庫存在，而且 EF6 先前已建立結構描述，則會檢查結構描述是否與目前的模型相容。</span><span class="sxs-lookup"><span data-stu-id="467e9-132">If the database exists and EF6 had previously created the schema, then the schema is checked for compatibility with the current model.</span></span> <span data-ttu-id="467e9-133">如果模型在建立結構描述之後已變更，就會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="467e9-133">An exception is thrown if the model has changed since the schema was created.</span></span>

<span data-ttu-id="467e9-134">**EF Core 無法執行任何此類操作。**</span><span class="sxs-lookup"><span data-stu-id="467e9-134">**EF Core does not perform any of this magic.**</span></span>

* <span data-ttu-id="467e9-135">資料庫連接必須在程式碼中明確設定。</span><span class="sxs-lookup"><span data-stu-id="467e9-135">The database connection must be explicitly configured in code.</span></span>

* <span data-ttu-id="467e9-136">不會執行任何初始化。</span><span class="sxs-lookup"><span data-stu-id="467e9-136">No initialization is performed.</span></span> <span data-ttu-id="467e9-137">您必須使用 `DbContext.Database.Migrate()` 來套用移轉 (或 `DbContext.Database.EnsureCreated()` 和 `EnsureDeleted()`，以建立/刪除資料庫，而不使用移轉)。</span><span class="sxs-lookup"><span data-stu-id="467e9-137">You must use `DbContext.Database.Migrate()` to apply migrations (or `DbContext.Database.EnsureCreated()` and `EnsureDeleted()` to create/delete the database without using migrations).</span></span>

### <a name="code-first-table-naming-convention"></a><span data-ttu-id="467e9-138">Code First 資料表命名慣例</span><span class="sxs-lookup"><span data-stu-id="467e9-138">Code First table naming convention</span></span>

<span data-ttu-id="467e9-139">EF6 會透過複數服務執行實體類別名稱，以計算實體所對應的預設資料表名稱。</span><span class="sxs-lookup"><span data-stu-id="467e9-139">EF6 runs the entity class name through a pluralization service to calculate the default table name that the entity is mapped to.</span></span>

<span data-ttu-id="467e9-140">EF Core 會使用在衍生內容上公開實體的 `DbSet` 屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="467e9-140">EF Core uses the name of the `DbSet` property that the entity is exposed in on the derived context.</span></span> <span data-ttu-id="467e9-141">如果實體沒有 `DbSet` 屬性，則會使用類別名稱。</span><span class="sxs-lookup"><span data-stu-id="467e9-141">If the entity does not have a `DbSet` property, then the class name is used.</span></span>
