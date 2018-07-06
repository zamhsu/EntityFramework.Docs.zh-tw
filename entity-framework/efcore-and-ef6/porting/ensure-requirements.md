---
title: 從 EF6 移植到 EF Core-驗證需求
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: d3b66f3c-9d10-4974-a090-8ad093c9a53d
uid: efcore-and-ef6/porting/ensure-requirements
ms.openlocfilehash: 2f45039e63546d266ec6ce0bfa66ef7e9fb3d7e7
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052858"
---
# <a name="before-porting-from-ef6-to-ef-core-validate-your-applications-requirements"></a><span data-ttu-id="fd27c-102">之前從 EF6 移植到 EF Core： 驗證您的應用程式需求</span><span class="sxs-lookup"><span data-stu-id="fd27c-102">Before porting from EF6 to EF Core: Validate your Application's Requirements</span></span>

<span data-ttu-id="fd27c-103">在開始移轉程序之前務必驗證 EF Core 符合您的應用程式的資料存取需求。</span><span class="sxs-lookup"><span data-stu-id="fd27c-103">Before you start the porting process it is important to validate that EF Core meets the data access requirements for your application.</span></span>

## <a name="missing-features"></a><span data-ttu-id="fd27c-104">遺失的功能</span><span class="sxs-lookup"><span data-stu-id="fd27c-104">Missing features</span></span>

<span data-ttu-id="fd27c-105">請確定 EF Core 包含您需要在您的應用程式中使用的所有功能。</span><span class="sxs-lookup"><span data-stu-id="fd27c-105">Make sure that EF Core has all the features you need to use in your application.</span></span> <span data-ttu-id="fd27c-106">請參閱[功能比較](../features.md)的 EF Core 中設定的功能如何與 EF6 比較的詳細比較。</span><span class="sxs-lookup"><span data-stu-id="fd27c-106">See [Feature Comparison](../features.md) for a detailed comparison of how the feature set in EF Core compares to EF6.</span></span> <span data-ttu-id="fd27c-107">如果遺漏任何必要的功能，請確定您可以先移植到 EF Core 補償缺少這些功能。</span><span class="sxs-lookup"><span data-stu-id="fd27c-107">If any required features are missing, ensure that you can compensate for the lack of these features before porting to EF Core.</span></span>

## <a name="behavior-changes"></a><span data-ttu-id="fd27c-108">行為變更</span><span class="sxs-lookup"><span data-stu-id="fd27c-108">Behavior changes</span></span>

<span data-ttu-id="fd27c-109">這是 EF6 和 EF Core 之間的行為中的某些變更非完整清單。</span><span class="sxs-lookup"><span data-stu-id="fd27c-109">This is a non-exhaustive list of some changes in behavior between EF6 and EF Core.</span></span> <span data-ttu-id="fd27c-110">請務必牢記您的連接埠為您的應用程式因為它們可能會變更您的應用程式的行為，但是不會顯示以編譯錯誤互換之後到 EF Core 的方式。</span><span class="sxs-lookup"><span data-stu-id="fd27c-110">It is important to keep these in mind as your port your application as they may change the way your application behaves, but will not show up as compilation errors after swapping to EF Core.</span></span>

### <a name="dbsetaddattach-and-graph-behavior"></a><span data-ttu-id="fd27c-111">DbSet.Add/Attach 和圖形行為</span><span class="sxs-lookup"><span data-stu-id="fd27c-111">DbSet.Add/Attach and graph behavior</span></span>

<span data-ttu-id="fd27c-112">EF6，在呼叫`DbSet.Add()`中遞迴搜尋，它的導覽屬性中所參考的所有實體的實體結果。</span><span class="sxs-lookup"><span data-stu-id="fd27c-112">In EF6, calling `DbSet.Add()` on an entity results in a recursive search for all entities referenced in its navigation properties.</span></span> <span data-ttu-id="fd27c-113">任何實體，找不到，並會不已經受到內容追蹤時，也會被標記為加入。</span><span class="sxs-lookup"><span data-stu-id="fd27c-113">Any entities that are found, and are not already tracked by the context, are also be marked as added.</span></span> <span data-ttu-id="fd27c-114">`DbSet.Attach()`行為相同，除了所有實體會都標示為不變。</span><span class="sxs-lookup"><span data-stu-id="fd27c-114">`DbSet.Attach()` behaves the same, except all entities are marked as unchanged.</span></span>

<span data-ttu-id="fd27c-115">**EF Core 執行類似的遞迴搜尋，但有一些稍有不同的規則。**</span><span class="sxs-lookup"><span data-stu-id="fd27c-115">**EF Core performs a similar recursive search, but with some slightly different rules.**</span></span>

*  <span data-ttu-id="fd27c-116">根實體一律是所要求的狀態 (針對加入`DbSet.Add`和不變`DbSet.Attach`)。</span><span class="sxs-lookup"><span data-stu-id="fd27c-116">The root entity is always in the requested state (added for `DbSet.Add` and unchanged for `DbSet.Attach`).</span></span>

*  <span data-ttu-id="fd27c-117">**導覽屬性的遞迴搜尋期間找到的實體：**</span><span class="sxs-lookup"><span data-stu-id="fd27c-117">**For entities that are found during the recursive search of navigation properties:**</span></span>

    *  <span data-ttu-id="fd27c-118">**如果實體的主索引鍵是產生的存放區**</span><span class="sxs-lookup"><span data-stu-id="fd27c-118">**If the primary key of the entity is store generated**</span></span>

        * <span data-ttu-id="fd27c-119">如果未設定的主索引鍵的值，狀態會設為加入的。</span><span class="sxs-lookup"><span data-stu-id="fd27c-119">If the primary key is not set to a value, the state is set to added.</span></span> <span data-ttu-id="fd27c-120">主索引鍵值被視為 「 未設定 」 如果它被指派為屬性類型的 CLR 預設值 (也就是`0`如`int`，`null`如`string`等。)。</span><span class="sxs-lookup"><span data-stu-id="fd27c-120">The primary key value is considered "not set" if it is assigned the CLR default value for the property type (i.e. `0` for `int`, `null` for `string`, etc.).</span></span>

        * <span data-ttu-id="fd27c-121">如果主索引鍵的值設定，狀態會設定為不變。</span><span class="sxs-lookup"><span data-stu-id="fd27c-121">If the primary key is set to a value, the state is set to unchanged.</span></span>

    *  <span data-ttu-id="fd27c-122">如果主索引鍵不是產生的資料庫，實體是放在根狀態相同。</span><span class="sxs-lookup"><span data-stu-id="fd27c-122">If the primary key is not database generated, the entity is put in the same state as the root.</span></span>

### <a name="code-first-database-initialization"></a><span data-ttu-id="fd27c-123">第一個資料庫的初始化程式碼</span><span class="sxs-lookup"><span data-stu-id="fd27c-123">Code First database initialization</span></span>

<span data-ttu-id="fd27c-124">**EF6 有大量的識別常數，它會執行選取的資料庫連接和初始化資料庫。這些規則包括：**</span><span class="sxs-lookup"><span data-stu-id="fd27c-124">**EF6 has a significant amount of magic it performs around selecting the database connection and initializing the database. Some of these rules include:**</span></span>

* <span data-ttu-id="fd27c-125">如果不執行任何設定，則 EF6 將選取的資料庫，SQL Express 或 LocalDb。</span><span class="sxs-lookup"><span data-stu-id="fd27c-125">If no configuration is performed, EF6 will select a database on SQL Express or LocalDb.</span></span>

* <span data-ttu-id="fd27c-126">連接字串是否具有相同名稱做為內容的應用程式中`App/Web.config`檔案，將會使用此連接。</span><span class="sxs-lookup"><span data-stu-id="fd27c-126">If a connection string with the same name as the context is in the applications `App/Web.config` file, this connection will be used.</span></span>

* <span data-ttu-id="fd27c-127">如果資料庫不存在，則會建立它。</span><span class="sxs-lookup"><span data-stu-id="fd27c-127">If the database does not exist, it is created.</span></span>

* <span data-ttu-id="fd27c-128">如果沒有任何模型中的資料表存在於資料庫中，目前模型的結構描述會加入至資料庫。</span><span class="sxs-lookup"><span data-stu-id="fd27c-128">If none of the tables from the model exist in the database, the schema for the current model is added to the database.</span></span> <span data-ttu-id="fd27c-129">如果已啟用移轉，它們用來建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="fd27c-129">If migrations are enabled, then they are used to create the database.</span></span>

* <span data-ttu-id="fd27c-130">如果資料庫存在，而且 EF6 先前建立的結構描述，與目前模型的相容性檢查的結構描述。</span><span class="sxs-lookup"><span data-stu-id="fd27c-130">If the database exists and EF6 had previously created the schema, then the schema is checked for compatibility with the current model.</span></span> <span data-ttu-id="fd27c-131">如果模型已變更，因為建立結構描述，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="fd27c-131">An exception is thrown if the model has changed since the schema was created.</span></span>

<span data-ttu-id="fd27c-132">**EF Core 不會執行任何此識別常數。**</span><span class="sxs-lookup"><span data-stu-id="fd27c-132">**EF Core does not perform any of this magic.**</span></span>

* <span data-ttu-id="fd27c-133">程式碼中，必須明確設定資料庫連接。</span><span class="sxs-lookup"><span data-stu-id="fd27c-133">The database connection must be explicitly configured in code.</span></span>

* <span data-ttu-id="fd27c-134">不會執行初始化。</span><span class="sxs-lookup"><span data-stu-id="fd27c-134">No initialization is performed.</span></span> <span data-ttu-id="fd27c-135">您必須使用`DbContext.Database.Migrate()`套用移轉 (或`DbContext.Database.EnsureCreated()`和`EnsureDeleted()`來建立/刪除資料庫而不需使用 移轉)。</span><span class="sxs-lookup"><span data-stu-id="fd27c-135">You must use `DbContext.Database.Migrate()` to apply migrations (or `DbContext.Database.EnsureCreated()` and `EnsureDeleted()` to create/delete the database without using migrations).</span></span>

### <a name="code-first-table-naming-convention"></a><span data-ttu-id="fd27c-136">程式碼的第一個資料表命名慣例</span><span class="sxs-lookup"><span data-stu-id="fd27c-136">Code First table naming convention</span></span>

<span data-ttu-id="fd27c-137">EF6 會透過計算預設的資料表名稱對應至實體的複數表示服務執行的實體類別名稱。</span><span class="sxs-lookup"><span data-stu-id="fd27c-137">EF6 runs the entity class name through a pluralization service to calculate the default table name that the entity is mapped to.</span></span>

<span data-ttu-id="fd27c-138">EF Core 使用的名稱`DbSet`實體中公開衍生內容的屬性。</span><span class="sxs-lookup"><span data-stu-id="fd27c-138">EF Core uses the name of the `DbSet` property that the entity is exposed in on the derived context.</span></span> <span data-ttu-id="fd27c-139">如果沒有實體`DbSet`用屬性，然後才是類別名稱。</span><span class="sxs-lookup"><span data-stu-id="fd27c-139">If the entity does not have a `DbSet` property, then the class name is used.</span></span>
