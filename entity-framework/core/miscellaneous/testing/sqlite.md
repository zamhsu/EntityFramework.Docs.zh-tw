---
title: "SQLite-EF 核心的測試"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 7a2b75e2-1875-4487-9877-feff0651b5a6
ms.technology: entity-framework-core
uid: core/miscellaneous/testing/sqlite
ms.openlocfilehash: 8fcb4b1a37da6f52219ebe3c672160627fe28fab
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="testing-with-sqlite"></a><span data-ttu-id="393de-102">使用 SQLite 測試</span><span class="sxs-lookup"><span data-stu-id="393de-102">Testing with SQLite</span></span>

<span data-ttu-id="393de-103">SQLite 有的記憶體中模式，可讓您使用 SQLite 撰寫測試針對關聯式資料庫，而不實際的資料庫作業的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="393de-103">SQLite has an in-memory mode that allows you to use SQLite to write tests against a relational database, without the overhead of actual database operations.</span></span>

> [!TIP]  
> <span data-ttu-id="393de-104">您可以檢視這篇文章[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing)GitHub 上</span><span class="sxs-lookup"><span data-stu-id="393de-104">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing) on GitHub</span></span>

## <a name="example-testing-scenario"></a><span data-ttu-id="393de-105">範例測試案例</span><span class="sxs-lookup"><span data-stu-id="393de-105">Example testing scenario</span></span>

<span data-ttu-id="393de-106">請考慮下列服務，可讓應用程式程式碼來執行一些部落格與相關的作業。</span><span class="sxs-lookup"><span data-stu-id="393de-106">Consider the following service that allows application code to perform some operations related to blogs.</span></span> <span data-ttu-id="393de-107">它會在內部使用`DbContext`連線至 SQL Server 資料庫。</span><span class="sxs-lookup"><span data-stu-id="393de-107">Internally it uses a `DbContext` that connects to a SQL Server database.</span></span> <span data-ttu-id="393de-108">它可能有助於交換此內容，讓我們可以撰寫有效率測試此服務而不必修改程式碼，或執行許多工作，建立測試連接到記憶體中 SQLite 資料庫內容的雙引號。</span><span class="sxs-lookup"><span data-stu-id="393de-108">It would be useful to swap this context to connect to an in-memory SQLite database so that we can write efficient tests for this service without having to modify the code, or do a lot of work to create a test double of the context.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BlogService.cs)]

## <a name="get-your-context-ready"></a><span data-ttu-id="393de-109">準備您的內容</span><span class="sxs-lookup"><span data-stu-id="393de-109">Get your context ready</span></span>

### <a name="avoid-configuring-two-database-providers"></a><span data-ttu-id="393de-110">避免設定兩個資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="393de-110">Avoid configuring two database providers</span></span>

<span data-ttu-id="393de-111">在測試中您要從外部設定要使用的 InMemory 提供者的內容。</span><span class="sxs-lookup"><span data-stu-id="393de-111">In your tests you are going to externally configure the context to use the InMemory provider.</span></span> <span data-ttu-id="393de-112">如果您要設定資料庫提供者藉由覆寫`OnConfiguring`您的內容，則您需要加入一些條件式的程式碼，以確保您只設定資料庫提供者，如果其中一個已尚未設定。</span><span class="sxs-lookup"><span data-stu-id="393de-112">If you are configuring a database provider by overriding `OnConfiguring` in your context, then you need to add some conditional code to ensure that you only configure the database provider if one has not already been configured.</span></span>

> [!TIP]  
> <span data-ttu-id="393de-113">如果您使用 ASP.NET Core，然後您應該不需要此程式碼由於資料庫提供者設定 （在 Startup.cs) 的內容之外。</span><span class="sxs-lookup"><span data-stu-id="393de-113">If you are using ASP.NET Core, then you should not need this code since your database provider is configured outside of the context (in Startup.cs).</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#OnConfiguring)]

### <a name="add-a-constructor-for-testing"></a><span data-ttu-id="393de-114">加入測試的建構函式</span><span class="sxs-lookup"><span data-stu-id="393de-114">Add a constructor for testing</span></span>

<span data-ttu-id="393de-115">若要啟用測試不同的資料庫最簡單的方式是修改內容公開 （expose） 的建構函式可接受`DbContextOptions<TContext>`。</span><span class="sxs-lookup"><span data-stu-id="393de-115">The simplest way to enable testing against a different database is to modify your context to expose a constructor that accepts a `DbContextOptions<TContext>`.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#Constructors)]

> [!TIP]  
> <span data-ttu-id="393de-116">`DbContextOptions<TContext>`告知其設定，例如連接到哪一個資料庫的所有內容。</span><span class="sxs-lookup"><span data-stu-id="393de-116">`DbContextOptions<TContext>` tells the context all of its settings, such as which database to connect to.</span></span> <span data-ttu-id="393de-117">這是建立在您的內容中執行 OnConfiguring 方法相同的物件。</span><span class="sxs-lookup"><span data-stu-id="393de-117">This is the same object that is built by running the OnConfiguring method in your context.</span></span>

## <a name="writing-tests"></a><span data-ttu-id="393de-118">撰寫測試</span><span class="sxs-lookup"><span data-stu-id="393de-118">Writing tests</span></span>

<span data-ttu-id="393de-119">要測試與此提供者的索引鍵是能夠告訴使用 SQLite，並控制記憶體中資料庫範圍的內容。</span><span class="sxs-lookup"><span data-stu-id="393de-119">The key to testing with this provider is the ability to tell the context to use SQLite, and control the scope of the in-memory database.</span></span> <span data-ttu-id="393de-120">開啟和關閉連接會控制資料庫的範圍。</span><span class="sxs-lookup"><span data-stu-id="393de-120">The scope of the database is controlled by opening and closing the connection.</span></span> <span data-ttu-id="393de-121">資料庫範圍的連接已開啟的持續時間。</span><span class="sxs-lookup"><span data-stu-id="393de-121">The database is scoped to the duration that the connection is open.</span></span> <span data-ttu-id="393de-122">您通常想全新資料庫的每個測試方法。</span><span class="sxs-lookup"><span data-stu-id="393de-122">Typically you want a clean database for each test method.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/TestProject/SQLite/BlogServiceTests.cs)]
