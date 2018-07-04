---
title: InMemory-EF Core的測試
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 0d0590f1-1ea3-4d5c-8f44-db17395cd3f3
ms.technology: entity-framework-core
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: 33690e3424d0777930d3cb8167575fb0f4ddd8f7
ms.sourcegitcommit: d096484dcf9eff73d9943fa60db7a418b10ca0b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2018
ms.locfileid: "27995583"
---
# <a name="testing-with-inmemory"></a><span data-ttu-id="25c91-102">使用 InMemory 測試</span><span class="sxs-lookup"><span data-stu-id="25c91-102">Testing with InMemory</span></span>

<span data-ttu-id="25c91-103">InMemory 提供者時，您想要測試元件使用的近似於連接至實際的資料庫，而不實際的資料庫作業的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="25c91-103">The InMemory provider is useful when you want to test components using something that approximates connecting to the real database, without the overhead of actual database operations.</span></span>

> [!TIP]  
> <span data-ttu-id="25c91-104">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="25c91-104">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing) on GitHub.</span></span>

## <a name="inmemory-is-not-a-relational-database"></a><span data-ttu-id="25c91-105">InMemory 不是關聯式資料庫</span><span class="sxs-lookup"><span data-stu-id="25c91-105">InMemory is not a relational database</span></span>

<span data-ttu-id="25c91-106">EF Core資料庫提供者可能沒有關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="25c91-106">EF Core database providers do not have to be relational databases.</span></span> <span data-ttu-id="25c91-107">InMemory 的設計是為了測試，一般用途的資料庫，而且不是模擬的關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="25c91-107">InMemory is designed to be a general purpose database for testing, and is not designed to mimic a relational database.</span></span>

<span data-ttu-id="25c91-108">包括的一些範例：</span><span class="sxs-lookup"><span data-stu-id="25c91-108">Some examples of this include:</span></span>
* <span data-ttu-id="25c91-109">InMemory 可讓您儲存違反參考完整性條件約束，關聯式資料庫中的資料。</span><span class="sxs-lookup"><span data-stu-id="25c91-109">InMemory will allow you to save data that would violate referential integrity constraints in a relational database.</span></span>

* <span data-ttu-id="25c91-110">如果您使用 DefaultValueSql(string) 屬性在模型中，這是關聯式資料庫 API 並不會影響對 InMemory 執行時。</span><span class="sxs-lookup"><span data-stu-id="25c91-110">If you use DefaultValueSql(string) for a property in your model, this is a relational database API and will have no effect when running against InMemory.</span></span>

> [!TIP]  
> <span data-ttu-id="25c91-111">針對許多測試目的將不重要了這些差異。</span><span class="sxs-lookup"><span data-stu-id="25c91-111">For many test purposes these differences will not matter.</span></span> <span data-ttu-id="25c91-112">不過，如果您想要測試的行為與類似，則為 true 的關聯式資料庫，請考慮使用[SQLite 記憶體中模式](sqlite.md)。</span><span class="sxs-lookup"><span data-stu-id="25c91-112">However, if you want to test against something that behaves more like a true relational database, then consider using [SQLite in-memory mode](sqlite.md).</span></span>

## <a name="example-testing-scenario"></a><span data-ttu-id="25c91-113">範例測試案例</span><span class="sxs-lookup"><span data-stu-id="25c91-113">Example testing scenario</span></span>

<span data-ttu-id="25c91-114">請考慮下列服務，可讓應用程式程式碼來執行一些部落格與相關的作業。</span><span class="sxs-lookup"><span data-stu-id="25c91-114">Consider the following service that allows application code to perform some operations related to blogs.</span></span> <span data-ttu-id="25c91-115">它會在內部使用`DbContext`連線至 SQL Server 資料庫。</span><span class="sxs-lookup"><span data-stu-id="25c91-115">Internally it uses a `DbContext` that connects to a SQL Server database.</span></span> <span data-ttu-id="25c91-116">它可能有助於交換此內容，讓我們可以撰寫有效率測試此服務而不必修改程式碼，或執行許多工作，建立測試連接到 InMemory 資料庫內容的 double。</span><span class="sxs-lookup"><span data-stu-id="25c91-116">It would be useful to swap this context to connect to an InMemory database so that we can write efficient tests for this service without having to modify the code, or do a lot of work to create a test double of the context.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BlogService.cs)]

## <a name="get-your-context-ready"></a><span data-ttu-id="25c91-117">準備您的內容</span><span class="sxs-lookup"><span data-stu-id="25c91-117">Get your context ready</span></span>

### <a name="avoid-configuring-two-database-providers"></a><span data-ttu-id="25c91-118">避免設定兩個資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="25c91-118">Avoid configuring two database providers</span></span>

<span data-ttu-id="25c91-119">在測試中您要從外部設定要使用的 InMemory 提供者的內容。</span><span class="sxs-lookup"><span data-stu-id="25c91-119">In your tests you are going to externally configure the context to use the InMemory provider.</span></span> <span data-ttu-id="25c91-120">如果您要設定資料庫提供者藉由覆寫`OnConfiguring`您的內容，則您需要加入一些條件式的程式碼，以確保您只設定資料庫提供者，如果其中一個已尚未設定。</span><span class="sxs-lookup"><span data-stu-id="25c91-120">If you are configuring a database provider by overriding `OnConfiguring` in your context, then you need to add some conditional code to ensure that you only configure the database provider if one has not already been configured.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#OnConfiguring)]

> [!TIP]  
> <span data-ttu-id="25c91-121">如果您使用 ASP.NET Core，然後您應該不需要此程式碼由於外部 （在 Startup.cs) 的內容已設定您的資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="25c91-121">If you are using ASP.NET Core, then you should not need this code since your database provider is already configured outside of the context (in Startup.cs).</span></span>

### <a name="add-a-constructor-for-testing"></a><span data-ttu-id="25c91-122">加入測試的建構函式</span><span class="sxs-lookup"><span data-stu-id="25c91-122">Add a constructor for testing</span></span>

<span data-ttu-id="25c91-123">若要啟用測試不同的資料庫最簡單的方式是修改內容公開 （expose） 的建構函式可接受`DbContextOptions<TContext>`。</span><span class="sxs-lookup"><span data-stu-id="25c91-123">The simplest way to enable testing against a different database is to modify your context to expose a constructor that accepts a `DbContextOptions<TContext>`.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#Constructors)]

> [!TIP]  
> <span data-ttu-id="25c91-124">`DbContextOptions<TContext>`告知其設定，例如連接到哪一個資料庫的所有內容。</span><span class="sxs-lookup"><span data-stu-id="25c91-124">`DbContextOptions<TContext>` tells the context all of its settings, such as which database to connect to.</span></span> <span data-ttu-id="25c91-125">這是建立在您的內容中執行 OnConfiguring 方法相同的物件。</span><span class="sxs-lookup"><span data-stu-id="25c91-125">This is the same object that is built by running the OnConfiguring method in your context.</span></span>

## <a name="writing-tests"></a><span data-ttu-id="25c91-126">撰寫測試</span><span class="sxs-lookup"><span data-stu-id="25c91-126">Writing tests</span></span>

<span data-ttu-id="25c91-127">要測試與此提供者的索引鍵是能夠判斷要使用的 InMemory 提供者，並控制記憶體中資料庫範圍的內容。</span><span class="sxs-lookup"><span data-stu-id="25c91-127">The key to testing with this provider is the ability to tell the context to use the InMemory provider, and control the scope of the in-memory database.</span></span> <span data-ttu-id="25c91-128">您通常想全新資料庫的每個測試方法。</span><span class="sxs-lookup"><span data-stu-id="25c91-128">Typically you want a clean database for each test method.</span></span>

<span data-ttu-id="25c91-129">以下是使用 InMemory 資料庫測試類別的範例。</span><span class="sxs-lookup"><span data-stu-id="25c91-129">Here is an example of a test class that uses the InMemory database.</span></span> <span data-ttu-id="25c91-130">每個測試方法指定唯一的資料庫名稱，這表示每一種方法有它自己的 InMemory 資料庫。</span><span class="sxs-lookup"><span data-stu-id="25c91-130">Each test method specifies a unique database name, meaning each method has its own InMemory database.</span></span>

>[!TIP]
> <span data-ttu-id="25c91-131">若要使用`.UseInMemoryDatabase()`擴充方法，參考的 NuGet 套件`Microsoft.EntityFrameworkCore.InMemory`。</span><span class="sxs-lookup"><span data-stu-id="25c91-131">To use the `.UseInMemoryDatabase()` extension method, reference the NuGet package `Microsoft.EntityFrameworkCore.InMemory`.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/TestProject/InMemory/BlogServiceTests.cs)]
