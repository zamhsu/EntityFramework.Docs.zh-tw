---
title: 使用 SQLite EF Core 測試
description: 使用 SQLite 測試 Entity Framework Core 應用程式
author: ajcvickers
ms.date: 04/24/2020
uid: core/miscellaneous/testing/sqlite
ms.openlocfilehash: b580ed58cb9466c8eac32f71951734f4bd565733
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617695"
---
# <a name="using-sqlite-to-test-an-ef-core-application"></a><span data-ttu-id="84159-103">使用 SQLite 測試 EF Core 應用程式</span><span class="sxs-lookup"><span data-stu-id="84159-103">Using SQLite to test an EF Core application</span></span>

> [!WARNING]
> <span data-ttu-id="84159-104">使用 SQLite 可以是測試 EF Core 應用程式的有效方式。</span><span class="sxs-lookup"><span data-stu-id="84159-104">Using SQLite can be an effective way to test an EF Core application.</span></span>
> <span data-ttu-id="84159-105">不過，SQLite 的行為會與其他資料庫系統不同，因此可能會發生問題。</span><span class="sxs-lookup"><span data-stu-id="84159-105">However, problems can arise where SQLite behaves differently from other database systems.</span></span> <span data-ttu-id="84159-106">請參閱使用 EF Core 討論問題和取捨的 [測試程式碼](xref:core/miscellaneous/testing/index) 。</span><span class="sxs-lookup"><span data-stu-id="84159-106">See [Testing code that uses EF Core](xref:core/miscellaneous/testing/index) for a discussion of the issues and trade-offs.</span></span>  

<span data-ttu-id="84159-107">本檔是以範例中所介紹的概念為基礎， [示範如何測試使用 EF Core 的應用程式](xref:core/miscellaneous/testing/testing-sample)。</span><span class="sxs-lookup"><span data-stu-id="84159-107">This document builds uses on the concepts introduced in [Sample showing how to test applications that use EF Core](xref:core/miscellaneous/testing/testing-sample).</span></span>
<span data-ttu-id="84159-108">此處所示的程式碼範例來自此範例。</span><span class="sxs-lookup"><span data-stu-id="84159-108">The code examples shown here come from this sample.</span></span>

## <a name="using-sqlite-in-memory-databases"></a><span data-ttu-id="84159-109">使用 SQLite 記憶體內部資料庫</span><span class="sxs-lookup"><span data-stu-id="84159-109">Using SQLite in-memory databases</span></span>

<span data-ttu-id="84159-110">一般來說，SQLite 會將資料庫建立為簡單的檔案，並以您的應用程式在同進程存取檔案。</span><span class="sxs-lookup"><span data-stu-id="84159-110">Normally, SQLite creates databases as simple files and accesses the file in-process with your application.</span></span>
<span data-ttu-id="84159-111">這非常快速，尤其是在使用快速 [SSD](https://en.wikipedia.org/wiki/Solid-state_drive)時。</span><span class="sxs-lookup"><span data-stu-id="84159-111">This is very fast, especially when using a fast [SSD](https://en.wikipedia.org/wiki/Solid-state_drive).</span></span> 

<span data-ttu-id="84159-112">SQLite 也可以使用純粹在記憶體中建立的資料庫。</span><span class="sxs-lookup"><span data-stu-id="84159-112">SQLite can also use databases created purely in-memory.</span></span>
<span data-ttu-id="84159-113">只要您瞭解記憶體內部資料庫存留期，就很容易使用 EF Core：</span><span class="sxs-lookup"><span data-stu-id="84159-113">This is easy to use with EF Core as long as you understand the in-memory database lifetime:</span></span>
* <span data-ttu-id="84159-114">當資料庫的連接開啟時，就會建立資料庫</span><span class="sxs-lookup"><span data-stu-id="84159-114">The database is created when the connection to it is opened</span></span>
* <span data-ttu-id="84159-115">當資料庫的連接關閉時，就會刪除資料庫。</span><span class="sxs-lookup"><span data-stu-id="84159-115">The database is deleted when the connection to it is closed</span></span>

<span data-ttu-id="84159-116">EF Core 將會使用已開啟的連接（如果有的話），而且永遠不會嘗試關閉它。</span><span class="sxs-lookup"><span data-stu-id="84159-116">EF Core will use an already open connection when given one, and will never attempt to close it.</span></span>
<span data-ttu-id="84159-117">因此，搭配記憶體內部 SQLite 資料庫使用 EF Core 的關鍵是先開啟連接，然後再將它傳遞給 EF。</span><span class="sxs-lookup"><span data-stu-id="84159-117">So the key to using EF Core with an in-memory SQLite database is to open the connection before passing it to EF.</span></span>  

<span data-ttu-id="84159-118">此 [範例](xref:core/miscellaneous/testing/testing-sample) 會使用下列程式碼來達成此程式碼：</span><span class="sxs-lookup"><span data-stu-id="84159-118">The [sample](xref:core/miscellaneous/testing/testing-sample) achieves this with the following code:</span></span>

[!code-csharp[SqliteInMemory](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteInMemoryItemsControllerTest.cs?name=SqliteInMemory)]

<span data-ttu-id="84159-119">注意：</span><span class="sxs-lookup"><span data-stu-id="84159-119">Notice:</span></span>
* <span data-ttu-id="84159-120">`CreateInMemoryDatabase`方法會在記憶體內部資料庫中建立 SQLite，並開啟與其的連接。</span><span class="sxs-lookup"><span data-stu-id="84159-120">The `CreateInMemoryDatabase` method creates a SQLite in-memory database and opens the connection to it.</span></span>
* <span data-ttu-id="84159-121">建立的 `DbConnection` 會從解壓縮 `ContextOptions` 並儲存。</span><span class="sxs-lookup"><span data-stu-id="84159-121">The created `DbConnection` is extracted from the `ContextOptions` and saved.</span></span>
* <span data-ttu-id="84159-122">當處置測試時，就會處置此連接，如此就不會流失資源。</span><span class="sxs-lookup"><span data-stu-id="84159-122">The connection is disposed when the test is disposed so that resources are not leaked.</span></span> 

> [!NOTE]
> <span data-ttu-id="84159-123">[問題 #16103](https://github.com/dotnet/efcore/issues/16103) 正在追蹤讓此連接管理變得更容易的方法。</span><span class="sxs-lookup"><span data-stu-id="84159-123">[Issue #16103](https://github.com/dotnet/efcore/issues/16103) is tracking ways to make this connection management easier.</span></span> 
