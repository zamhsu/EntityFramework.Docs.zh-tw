---
title: 使用 SQLite EF Core 進行測試
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 7a2b75e2-1875-4487-9877-feff0651b5a6
uid: core/miscellaneous/testing/sqlite
ms.openlocfilehash: f7f847d8c766c0d4d7577ea6760ee72a17f84933
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417299"
---
# <a name="testing-with-sqlite"></a><span data-ttu-id="06860-102">使用 SQLite 進行測試</span><span class="sxs-lookup"><span data-stu-id="06860-102">Testing with SQLite</span></span>

<span data-ttu-id="06860-103">SQLite 具有記憶體中模式，可讓您使用 SQLite 針對關係資料庫撰寫測試，而不會產生實際資料庫作業的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="06860-103">SQLite has an in-memory mode that allows you to use SQLite to write tests against a relational database, without the overhead of actual database operations.</span></span>

> [!TIP]  
> <span data-ttu-id="06860-104">您可以在 GitHub 上查看這篇文章的[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing)</span><span class="sxs-lookup"><span data-stu-id="06860-104">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing) on GitHub</span></span>

## <a name="example-testing-scenario"></a><span data-ttu-id="06860-105">範例測試案例</span><span class="sxs-lookup"><span data-stu-id="06860-105">Example testing scenario</span></span>

<span data-ttu-id="06860-106">請考慮下列可讓應用程式程式碼執行與 blog 相關之作業的服務。</span><span class="sxs-lookup"><span data-stu-id="06860-106">Consider the following service that allows application code to perform some operations related to blogs.</span></span> <span data-ttu-id="06860-107">在內部，它會使用連接到 SQL Server 資料庫的 `DbContext`。</span><span class="sxs-lookup"><span data-stu-id="06860-107">Internally it uses a `DbContext` that connects to a SQL Server database.</span></span> <span data-ttu-id="06860-108">交換此內容以連接到記憶體中的 SQLite 資料庫，讓我們可以為此服務撰寫有效率的測試，而不需要修改程式碼，或執行許多工作來建立內容的測試 double，這會很有説明。</span><span class="sxs-lookup"><span data-stu-id="06860-108">It would be useful to swap this context to connect to an in-memory SQLite database so that we can write efficient tests for this service without having to modify the code, or do a lot of work to create a test double of the context.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BlogService.cs)]

## <a name="get-your-context-ready"></a><span data-ttu-id="06860-109">讓您的內容就緒</span><span class="sxs-lookup"><span data-stu-id="06860-109">Get your context ready</span></span>

### <a name="avoid-configuring-two-database-providers"></a><span data-ttu-id="06860-110">避免設定兩個資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="06860-110">Avoid configuring two database providers</span></span>

<span data-ttu-id="06860-111">在您的測試中，您將會在外部設定內容以使用 InMemory 提供者。</span><span class="sxs-lookup"><span data-stu-id="06860-111">In your tests you are going to externally configure the context to use the InMemory provider.</span></span> <span data-ttu-id="06860-112">如果您要藉由覆寫內容中的 `OnConfiguring` 來設定資料庫提供者，則必須新增一些條件式程式碼，以確保只有在資料庫提供者尚未設定時，才會進行設定。</span><span class="sxs-lookup"><span data-stu-id="06860-112">If you are configuring a database provider by overriding `OnConfiguring` in your context, then you need to add some conditional code to ensure that you only configure the database provider if one has not already been configured.</span></span>

> [!TIP]  
> <span data-ttu-id="06860-113">如果您使用 ASP.NET Core，則不應該需要此程式碼，因為您的資料庫提供者是在內容外部設定（在 Startup.cs 中）。</span><span class="sxs-lookup"><span data-stu-id="06860-113">If you are using ASP.NET Core, then you should not need this code since your database provider is configured outside of the context (in Startup.cs).</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#OnConfiguring)]

### <a name="add-a-constructor-for-testing"></a><span data-ttu-id="06860-114">新增用於測試的函數</span><span class="sxs-lookup"><span data-stu-id="06860-114">Add a constructor for testing</span></span>

<span data-ttu-id="06860-115">針對不同的資料庫啟用測試的最簡單方式是修改您的內容，以公開接受 `DbContextOptions<TContext>`的函式。</span><span class="sxs-lookup"><span data-stu-id="06860-115">The simplest way to enable testing against a different database is to modify your context to expose a constructor that accepts a `DbContextOptions<TContext>`.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#Constructors)]

> [!TIP]  
> <span data-ttu-id="06860-116">`DbContextOptions<TContext>` 會告訴內容其所有設定，例如要連接的資料庫。</span><span class="sxs-lookup"><span data-stu-id="06860-116">`DbContextOptions<TContext>` tells the context all of its settings, such as which database to connect to.</span></span> <span data-ttu-id="06860-117">這是在您的內容中執行 OnConfiguring 方法所建立的相同物件。</span><span class="sxs-lookup"><span data-stu-id="06860-117">This is the same object that is built by running the OnConfiguring method in your context.</span></span>

## <a name="writing-tests"></a><span data-ttu-id="06860-118">撰寫測試</span><span class="sxs-lookup"><span data-stu-id="06860-118">Writing tests</span></span>

<span data-ttu-id="06860-119">使用此提供者進行測試的關鍵在於能夠告訴內容使用 SQLite，並控制記憶體內部資料庫的範圍。</span><span class="sxs-lookup"><span data-stu-id="06860-119">The key to testing with this provider is the ability to tell the context to use SQLite, and control the scope of the in-memory database.</span></span> <span data-ttu-id="06860-120">資料庫的範圍是藉由開啟和關閉連接來控制。</span><span class="sxs-lookup"><span data-stu-id="06860-120">The scope of the database is controlled by opening and closing the connection.</span></span> <span data-ttu-id="06860-121">資料庫的範圍是連接開啟的持續時間。</span><span class="sxs-lookup"><span data-stu-id="06860-121">The database is scoped to the duration that the connection is open.</span></span> <span data-ttu-id="06860-122">您通常會想要針對每個測試方法使用乾淨的資料庫。</span><span class="sxs-lookup"><span data-stu-id="06860-122">Typically you want a clean database for each test method.</span></span>

>[!TIP]
> <span data-ttu-id="06860-123">若要使用 `SqliteConnection()` 和 `.UseSqlite()` 擴充方法，請參考[microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)的 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="06860-123">To use `SqliteConnection()` and the `.UseSqlite()` extension method, reference the NuGet package [Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/TestProject/SQLite/BlogServiceTests.cs)]
