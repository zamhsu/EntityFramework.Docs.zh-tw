---
title: 使用 InMemory 進行測試-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 0d0590f1-1ea3-4d5c-8f44-db17395cd3f3
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: fcd2f99ad06fd30ef9e36fd1e5a6a09fe0a45d07
ms.sourcegitcommit: 4e86f01740e407ff25e704a11b1f7d7e66bfb2a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2020
ms.locfileid: "75781114"
---
# <a name="testing-with-inmemory"></a><span data-ttu-id="71e48-102">使用 InMemory 進行測試</span><span class="sxs-lookup"><span data-stu-id="71e48-102">Testing with InMemory</span></span>

<span data-ttu-id="71e48-103">當您想要使用近似連接到實際資料庫的專案來測試元件，而不會產生實際資料庫作業的額外負荷時，InMemory 提供者會很有用。</span><span class="sxs-lookup"><span data-stu-id="71e48-103">The InMemory provider is useful when you want to test components using something that approximates connecting to the real database, without the overhead of actual database operations.</span></span>

> [!TIP]  
> <span data-ttu-id="71e48-104">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="71e48-104">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing) on GitHub.</span></span>

## <a name="inmemory-is-not-a-relational-database"></a><span data-ttu-id="71e48-105">InMemory 不是關係資料庫</span><span class="sxs-lookup"><span data-stu-id="71e48-105">InMemory is not a relational database</span></span>

<span data-ttu-id="71e48-106">EF Core 資料庫提供者不一定要是關係資料庫。</span><span class="sxs-lookup"><span data-stu-id="71e48-106">EF Core database providers do not have to be relational databases.</span></span> <span data-ttu-id="71e48-107">InMemory 的設計是用來測試的一般用途資料庫，而不是設計來模擬關係資料庫。</span><span class="sxs-lookup"><span data-stu-id="71e48-107">InMemory is designed to be a general purpose database for testing, and is not designed to mimic a relational database.</span></span>

<span data-ttu-id="71e48-108">其中一些範例包括：</span><span class="sxs-lookup"><span data-stu-id="71e48-108">Some examples of this include:</span></span>

* <span data-ttu-id="71e48-109">InMemory 可讓您儲存在關係資料庫中違反參考完整性條件約束的資料。</span><span class="sxs-lookup"><span data-stu-id="71e48-109">InMemory will allow you to save data that would violate referential integrity constraints in a relational database.</span></span>
* <span data-ttu-id="71e48-110">如果您針對模型中的屬性使用 DefaultValueSql （string），這就是關係資料庫 API，且在對 InMemory 執行時不會有任何作用。</span><span class="sxs-lookup"><span data-stu-id="71e48-110">If you use DefaultValueSql(string) for a property in your model, this is a relational database API and will have no effect when running against InMemory.</span></span>
* <span data-ttu-id="71e48-111">不支援透過[時間戳記/資料列版本](xref:core/modeling/concurrency#timestamprowversion)（`[Timestamp]` 或 `IsRowVersion`）進行平行存取。</span><span class="sxs-lookup"><span data-stu-id="71e48-111">[Concurrency via Timestamp/row version](xref:core/modeling/concurrency#timestamprowversion) (`[Timestamp]` or `IsRowVersion`) is not supported.</span></span> <span data-ttu-id="71e48-112">如果使用舊的並行標記來進行更新，則不會擲回任何[DbUpdateConcurrencyException](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception) 。</span><span class="sxs-lookup"><span data-stu-id="71e48-112">No [DbUpdateConcurrencyException](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception) will be thrown if an update is done using an old concurrency token.</span></span>

> [!TIP]  
> <span data-ttu-id="71e48-113">基於許多測試目的，這些差異並不重要。</span><span class="sxs-lookup"><span data-stu-id="71e48-113">For many test purposes these differences will not matter.</span></span> <span data-ttu-id="71e48-114">不過，如果您想要針對行為更像是真正關係資料庫的專案進行測試，請考慮使用[SQLite 記憶體中模式](sqlite.md)。</span><span class="sxs-lookup"><span data-stu-id="71e48-114">However, if you want to test against something that behaves more like a true relational database, then consider using [SQLite in-memory mode](sqlite.md).</span></span>

## <a name="example-testing-scenario"></a><span data-ttu-id="71e48-115">範例測試案例</span><span class="sxs-lookup"><span data-stu-id="71e48-115">Example testing scenario</span></span>

<span data-ttu-id="71e48-116">請考慮下列可讓應用程式程式碼執行與 blog 相關之作業的服務。</span><span class="sxs-lookup"><span data-stu-id="71e48-116">Consider the following service that allows application code to perform some operations related to blogs.</span></span> <span data-ttu-id="71e48-117">在內部，它會使用連接到 SQL Server 資料庫的 `DbContext`。</span><span class="sxs-lookup"><span data-stu-id="71e48-117">Internally it uses a `DbContext` that connects to a SQL Server database.</span></span> <span data-ttu-id="71e48-118">交換此內容以連接到 InMemory 資料庫，讓我們可以為此服務撰寫有效率的測試，而不需要修改程式碼，或執行許多工作來建立內容的測試 double，會是很有用的做法。</span><span class="sxs-lookup"><span data-stu-id="71e48-118">It would be useful to swap this context to connect to an InMemory database so that we can write efficient tests for this service without having to modify the code, or do a lot of work to create a test double of the context.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BlogService.cs)]

## <a name="get-your-context-ready"></a><span data-ttu-id="71e48-119">讓您的內容就緒</span><span class="sxs-lookup"><span data-stu-id="71e48-119">Get your context ready</span></span>

### <a name="avoid-configuring-two-database-providers"></a><span data-ttu-id="71e48-120">避免設定兩個資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="71e48-120">Avoid configuring two database providers</span></span>

<span data-ttu-id="71e48-121">在您的測試中，您將會在外部設定內容以使用 InMemory 提供者。</span><span class="sxs-lookup"><span data-stu-id="71e48-121">In your tests you are going to externally configure the context to use the InMemory provider.</span></span> <span data-ttu-id="71e48-122">如果您要藉由覆寫內容中的 `OnConfiguring` 來設定資料庫提供者，則必須新增一些條件式程式碼，以確保只有在資料庫提供者尚未設定時，才會進行設定。</span><span class="sxs-lookup"><span data-stu-id="71e48-122">If you are configuring a database provider by overriding `OnConfiguring` in your context, then you need to add some conditional code to ensure that you only configure the database provider if one has not already been configured.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#OnConfiguring)]

> [!TIP]  
> <span data-ttu-id="71e48-123">如果您使用 ASP.NET Core，則不應該需要此程式碼，因為您的資料庫提供者已經設定在內容外部（在 Startup.cs 中）。</span><span class="sxs-lookup"><span data-stu-id="71e48-123">If you are using ASP.NET Core, then you should not need this code since your database provider is already configured outside of the context (in Startup.cs).</span></span>

### <a name="add-a-constructor-for-testing"></a><span data-ttu-id="71e48-124">新增用於測試的函數</span><span class="sxs-lookup"><span data-stu-id="71e48-124">Add a constructor for testing</span></span>

<span data-ttu-id="71e48-125">針對不同的資料庫啟用測試的最簡單方式是修改您的內容，以公開接受 `DbContextOptions<TContext>`的函式。</span><span class="sxs-lookup"><span data-stu-id="71e48-125">The simplest way to enable testing against a different database is to modify your context to expose a constructor that accepts a `DbContextOptions<TContext>`.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#Constructors)]

> [!TIP]  
> <span data-ttu-id="71e48-126">`DbContextOptions<TContext>` 會告訴內容其所有設定，例如要連接的資料庫。</span><span class="sxs-lookup"><span data-stu-id="71e48-126">`DbContextOptions<TContext>` tells the context all of its settings, such as which database to connect to.</span></span> <span data-ttu-id="71e48-127">這是在您的內容中執行 OnConfiguring 方法所建立的相同物件。</span><span class="sxs-lookup"><span data-stu-id="71e48-127">This is the same object that is built by running the OnConfiguring method in your context.</span></span>

## <a name="writing-tests"></a><span data-ttu-id="71e48-128">撰寫測試</span><span class="sxs-lookup"><span data-stu-id="71e48-128">Writing tests</span></span>

<span data-ttu-id="71e48-129">使用此提供者進行測試的關鍵在於能夠告訴內容使用 InMemory 提供者，並控制記憶體內部資料庫的範圍。</span><span class="sxs-lookup"><span data-stu-id="71e48-129">The key to testing with this provider is the ability to tell the context to use the InMemory provider, and control the scope of the in-memory database.</span></span> <span data-ttu-id="71e48-130">您通常會想要針對每個測試方法使用乾淨的資料庫。</span><span class="sxs-lookup"><span data-stu-id="71e48-130">Typically you want a clean database for each test method.</span></span>

<span data-ttu-id="71e48-131">以下是使用 InMemory 資料庫的測試類別範例。</span><span class="sxs-lookup"><span data-stu-id="71e48-131">Here is an example of a test class that uses the InMemory database.</span></span> <span data-ttu-id="71e48-132">每個測試方法都會指定唯一的資料庫名稱，這表示每個方法都有自己的 InMemory 資料庫。</span><span class="sxs-lookup"><span data-stu-id="71e48-132">Each test method specifies a unique database name, meaning each method has its own InMemory database.</span></span>

>[!TIP]
> <span data-ttu-id="71e48-133">若要使用 `.UseInMemoryDatabase()` 的擴充方法，請參考[microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)的 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="71e48-133">To use the `.UseInMemoryDatabase()` extension method, reference the NuGet package [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/TestProject/InMemory/BlogServiceTests.cs)]
