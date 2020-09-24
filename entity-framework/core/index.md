---
title: Entity Framework Core 概觀 - EF Core
description: Entity Framework Core 的一般入門總覽
author: ajcvickers
ms.date: 9/20/2020
uid: core/index
ms.openlocfilehash: 011900b55bc941d6ae6a7ac8aca6001713088c69
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2020
ms.locfileid: "91210376"
---
# <a name="entity-framework-core"></a><span data-ttu-id="1e021-103">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="1e021-103">Entity Framework Core</span></span>

<span data-ttu-id="1e021-104">Entity Framework (EF) Core 是常見 Entity Framework 資料存取技術的輕量型、可擴充、[開放原始碼](https://github.com/aspnet/EntityFrameworkCore)且跨平台版本。</span><span class="sxs-lookup"><span data-stu-id="1e021-104">Entity Framework (EF) Core is a lightweight, extensible, [open source](https://github.com/aspnet/EntityFrameworkCore) and cross-platform version of the popular Entity Framework data access technology.</span></span>

<span data-ttu-id="1e021-105">EF Core 可以作為物件關聯式對應程式 (O/RM) ，其：</span><span class="sxs-lookup"><span data-stu-id="1e021-105">EF Core can serve as an object-relational mapper (O/RM), which:</span></span>

* <span data-ttu-id="1e021-106">可讓 .NET 開發人員使用 .NET 物件來處理資料庫。</span><span class="sxs-lookup"><span data-stu-id="1e021-106">Enables .NET developers to work with a database using .NET objects.</span></span>
* <span data-ttu-id="1e021-107">免除通常需要撰寫的大部分資料存取程式碼的需求。</span><span class="sxs-lookup"><span data-stu-id="1e021-107">Eliminates the need for most of the data-access code that typically needs to be written.</span></span>

<span data-ttu-id="1e021-108">EF Core 支援許多資料庫引擎，如需詳細資料，請參閱[資料庫提供者](xref:core/providers/index)。</span><span class="sxs-lookup"><span data-stu-id="1e021-108">EF Core supports many database engines, see [Database Providers](xref:core/providers/index) for details.</span></span>

## <a name="the-model"></a><span data-ttu-id="1e021-109">模型</span><span class="sxs-lookup"><span data-stu-id="1e021-109">The model</span></span>

<span data-ttu-id="1e021-110">運用 EF Core，使用模型來執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="1e021-110">With EF Core, data access is performed using a model.</span></span> <span data-ttu-id="1e021-111">模型是由實體類別和表示與資料庫之會話的內容物件所組成。</span><span class="sxs-lookup"><span data-stu-id="1e021-111">A model is made up of entity classes and a context object that represents a session with the database.</span></span> <span data-ttu-id="1e021-112">內容物件可讓您查詢和儲存資料。</span><span class="sxs-lookup"><span data-stu-id="1e021-112">The context object allows querying and saving data.</span></span> <span data-ttu-id="1e021-113">如需詳細資訊，請參閱 [建立模型](xref:core/modeling/index)。</span><span class="sxs-lookup"><span data-stu-id="1e021-113">For more information, see [Creating a Model](xref:core/modeling/index).</span></span>

<span data-ttu-id="1e021-114">EF 支援下列模型開發方法：</span><span class="sxs-lookup"><span data-stu-id="1e021-114">EF supports the following model development approaches:</span></span>

* <span data-ttu-id="1e021-115">從現有的資料庫產生模型。</span><span class="sxs-lookup"><span data-stu-id="1e021-115">Generate a model from an existing database.</span></span>
* <span data-ttu-id="1e021-116">將模型手動編寫成符合資料庫的程式碼。</span><span class="sxs-lookup"><span data-stu-id="1e021-116">Hand code a model to match the database.</span></span>
* <span data-ttu-id="1e021-117">建立模型之後，請使用 [EF 遷移](xref:core/managing-schemas/migrations/index) 從模型建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="1e021-117">Once a model is created, use [EF Migrations](xref:core/managing-schemas/migrations/index) to create a database from the model.</span></span> <span data-ttu-id="1e021-118">當模型變更時，遷移可讓資料庫演進。</span><span class="sxs-lookup"><span data-stu-id="1e021-118">Migrations allow evolving the database as the model changes.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Model.cs)]

## <a name="querying"></a><span data-ttu-id="1e021-119">查詢</span><span class="sxs-lookup"><span data-stu-id="1e021-119">Querying</span></span>

<span data-ttu-id="1e021-120">您可以使用 [ (LINQ) ](/dotnet/csharp/programming-guide/concepts/linq/)的語言整合式查詢，從資料庫取出實體類別的實例。</span><span class="sxs-lookup"><span data-stu-id="1e021-120">Instances of your entity classes are retrieved from the database using Language [Integrated Query (LINQ)](/dotnet/csharp/programming-guide/concepts/linq/).</span></span> <span data-ttu-id="1e021-121">如需詳細資訊，請參閱 [查詢資料](xref:core/querying/index)。</span><span class="sxs-lookup"><span data-stu-id="1e021-121">For more information, see [Querying Data](xref:core/querying/index).</span></span>

[!code-csharp[Main](../../samples/core/Intro/Program.cs#Querying)]

## <a name="saving-data"></a><span data-ttu-id="1e021-122">儲存資料</span><span class="sxs-lookup"><span data-stu-id="1e021-122">Saving data</span></span>

<span data-ttu-id="1e021-123">使用您實體類別的執行個體，建立、刪除和修改資料庫中的資料。</span><span class="sxs-lookup"><span data-stu-id="1e021-123">Data is created, deleted, and modified in the database using instances of your entity classes.</span></span> <span data-ttu-id="1e021-124">若要深入了解，請參閱[儲存資料](xref:core/saving/index)。</span><span class="sxs-lookup"><span data-stu-id="1e021-124">See [Saving Data](xref:core/saving/index) to learn more.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Program.cs#SavingData)]

## <a name="ef-orm-considerations"></a><span data-ttu-id="1e021-125">EF ORM 考慮</span><span class="sxs-lookup"><span data-stu-id="1e021-125">EF ORM considerations</span></span>

<span data-ttu-id="1e021-126">雖然 EF Core 在抽象化許多程式設計細節時很好用，但還是有一些適用于任何 ORM 的最佳作法，可避免在生產應用程式中發生常見的陷阱：</span><span class="sxs-lookup"><span data-stu-id="1e021-126">While EF Core is good at abstracting many programming details, there are some best practices applicable to any ORM that help to avoid common pitfalls in production apps:</span></span>

 - <span data-ttu-id="1e021-127">基礎資料庫伺服器的中繼層級知識或更高版本，對於在高效能的生產環境應用程式中設計、分析、分析和遷移資料而言是不可或缺的。</span><span class="sxs-lookup"><span data-stu-id="1e021-127">Intermediate-level knowledge or higher of the underlying database server is essential to architect, debug, profile, and migrate data in high performance production apps.</span></span> <span data-ttu-id="1e021-128">例如，主要與外鍵、條件約束、索引、正規化、DML 和 DDL 語句、資料類型、分析等等的知識。</span><span class="sxs-lookup"><span data-stu-id="1e021-128">For example, knowledge of primary and foreign keys, constraints, indexes, normalization, DML and DDL statements, data types, profiling, etc.</span></span>
- <span data-ttu-id="1e021-129">功能和整合測試：請務必盡可能將生產環境複寫至：</span><span class="sxs-lookup"><span data-stu-id="1e021-129">Functional and integration testing:  It's important to replicate the production environment as closely as possible to:</span></span>
  - <span data-ttu-id="1e021-130">找出應用程式中只有在使用特定版本的資料庫伺服器時才會顯示的問題。</span><span class="sxs-lookup"><span data-stu-id="1e021-130">Find issues in the app that only show up when using a specific versions or edition of the database server .</span></span>
  - <span data-ttu-id="1e021-131">升級 EF Core 和其他相依性時攔截重大變更。</span><span class="sxs-lookup"><span data-stu-id="1e021-131">Catch breaking changes when upgrading EF Core and other dependencies.</span></span> <span data-ttu-id="1e021-132">例如，加入或升級 ASP.NET Core、OData 或 Automapper 等架構。</span><span class="sxs-lookup"><span data-stu-id="1e021-132">For example, adding or upgrading frameworks like ASP.NET Core, OData, or Automapper.</span></span> <span data-ttu-id="1e021-133">這些相依性可能會以非預期的方式影響 EF Core。</span><span class="sxs-lookup"><span data-stu-id="1e021-133">These dependencies can affect EF Core in unexpected ways.</span></span>
- <span data-ttu-id="1e021-134">具有代表性負載的效能和壓力測試。</span><span class="sxs-lookup"><span data-stu-id="1e021-134">Performance and stress testing with representative loads.</span></span> <span data-ttu-id="1e021-135">部分功能的最簡單用途無法妥善調整。</span><span class="sxs-lookup"><span data-stu-id="1e021-135">The naïve usage of some features doesn't scale well.</span></span> <span data-ttu-id="1e021-136">例如，多個集合包括過度使用消極式載入、非索引資料行上的條件式查詢、具有存放區產生值的大量更新和插入、缺乏並行處理、大型模型、不適當的快取原則。</span><span class="sxs-lookup"><span data-stu-id="1e021-136">For example, multiple collections Includes, heavy use of lazy loading, conditional queries on non-indexed columns, massive updates and inserts with store-generated values, lack of concurrency handling, large models, inadequate cache policy.</span></span>
- <span data-ttu-id="1e021-137">安全性審核：例如，處理連接字串和其他秘密、非部署作業的資料庫許可權、原始 SQL 的輸入驗證、機密資料的加密。</span><span class="sxs-lookup"><span data-stu-id="1e021-137">Security review: For example, handling of connection strings and other secrets, database permissions for non-deployment operation, input validation for raw SQL, encryption for sensitive data.</span></span>
- <span data-ttu-id="1e021-138">請確定記錄和診斷功能足夠且可用。</span><span class="sxs-lookup"><span data-stu-id="1e021-138">Make sure logging and diagnostics are sufficient and usable.</span></span> <span data-ttu-id="1e021-139">例如，適當的記錄設定、查詢標記以及 Application Insights。</span><span class="sxs-lookup"><span data-stu-id="1e021-139">For example, appropriate logging configuration, query tags, and Application Insights.</span></span>
- <span data-ttu-id="1e021-140">錯誤復原。</span><span class="sxs-lookup"><span data-stu-id="1e021-140">Error recovery.</span></span> <span data-ttu-id="1e021-141">針對常見的失敗案例（例如版本復原、回溯伺服器、向外延展和負載平衡、DoS 緩和和資料備份）做好應變。</span><span class="sxs-lookup"><span data-stu-id="1e021-141">Prepare contingencies for common failure scenarios such as version rollback, fallback servers, scale-out and load balancing, DoS mitigation, and data backups.</span></span>
- <span data-ttu-id="1e021-142">應用程式部署和遷移。</span><span class="sxs-lookup"><span data-stu-id="1e021-142">Application deployment and migration.</span></span> <span data-ttu-id="1e021-143">規劃如何在部署期間套用遷移;在應用程式啟動時執行它可能會受到並行處理問題的影響，且需要的許可權比一般作業所需的更高。</span><span class="sxs-lookup"><span data-stu-id="1e021-143">Plan out how migrations are going to be applied during deployment; doing it at application start can suffer from concurrency issues and requires higher permissions than necessary for normal operation.</span></span> <span data-ttu-id="1e021-144">使用暫存有助於在遷移期間發生嚴重錯誤時進行修復。</span><span class="sxs-lookup"><span data-stu-id="1e021-144">Use staging to facilitate recovery from fatal errors during migration.</span></span> <span data-ttu-id="1e021-145">如需詳細資訊，請參閱套用 [遷移](xref:core/managing-schemas/migrations/applying)。</span><span class="sxs-lookup"><span data-stu-id="1e021-145">For more information, see [Applying Migrations](xref:core/managing-schemas/migrations/applying).</span></span>
- <span data-ttu-id="1e021-146">已產生之遷移的詳細檢查和測試。</span><span class="sxs-lookup"><span data-stu-id="1e021-146">Detailed examination and testing of generated migrations.</span></span> <span data-ttu-id="1e021-147">在將遷移套用至生產資料之前，應先徹底測試。</span><span class="sxs-lookup"><span data-stu-id="1e021-147">Migrations should be thoroughly tested before being applied to production data.</span></span> <span data-ttu-id="1e021-148">當資料表包含生產資料時，架構的圖形和資料行類型不容易變更。</span><span class="sxs-lookup"><span data-stu-id="1e021-148">The shape of the schema and the column types cannot be easily changed once the tables contain production data.</span></span> <span data-ttu-id="1e021-149">例如，在 SQL Server 上， `nvarchar(max)` 和 `decimal(18, 2)` 不是對應至字串和 decimal 屬性之資料行的最佳類型，但這些是 EF 使用的預設值，因為它並不知道您的特定案例。</span><span class="sxs-lookup"><span data-stu-id="1e021-149">For example, on SQL Server, `nvarchar(max)` and `decimal(18, 2)` are rarely the best types for columns mapped to string and decimal properties, but those are the defaults that EF uses because it doesn't have knowledge of your specific scenario.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1e021-150">後續步驟</span><span class="sxs-lookup"><span data-stu-id="1e021-150">Next steps</span></span>

<span data-ttu-id="1e021-151">如需簡介教學課程，請參閱 [Entity Framework Core 使用者入門](xref:core/get-started/index)。</span><span class="sxs-lookup"><span data-stu-id="1e021-151">For introductory tutorials, see [Getting Started with Entity Framework Core](xref:core/get-started/index).</span></span>
