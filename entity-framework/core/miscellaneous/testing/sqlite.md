---
title: 使用 SQLite EF Core 進行測試
description: 使用 SQLite 測試 EF Core 應用程式
author: ajcvickers
ms.date: 04/24/2020
uid: core/miscellaneous/testing/sqlite
ms.openlocfilehash: 327fdc230df2a3b4094accf93fffa81f92e0a931
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538279"
---
# <a name="using-sqlite-to-test-an-ef-core-application"></a><span data-ttu-id="3ed79-103">使用 SQLite 測試 EF Core 應用程式</span><span class="sxs-lookup"><span data-stu-id="3ed79-103">Using SQLite to test an EF Core application</span></span>

> [!WARNING]
> <span data-ttu-id="3ed79-104">使用 SQLite 可以是測試 EF Core 應用程式的有效方式。</span><span class="sxs-lookup"><span data-stu-id="3ed79-104">Using SQLite can be an effective way to test an EF Core application.</span></span>
> <span data-ttu-id="3ed79-105">不過，SQLite 的行為與其他資料庫系統不同，可能會發生問題。</span><span class="sxs-lookup"><span data-stu-id="3ed79-105">However, problems can arise where SQLite behaves differently from other database systems.</span></span> <span data-ttu-id="3ed79-106">如需問題和取捨的討論，請參閱[測試使用 EF Core 的程式碼](xref:core/miscellaneous/testing/index)。</span><span class="sxs-lookup"><span data-stu-id="3ed79-106">See [Testing code that uses EF Core](xref:core/miscellaneous/testing/index) for a discussion of the issues and trade-offs.</span></span>  

<span data-ttu-id="3ed79-107">本檔會針對[示範如何測試使用 EF Core 之應用程式的範例](xref:core/miscellaneous/testing/testing-sample)中所介紹的概念來使用。</span><span class="sxs-lookup"><span data-stu-id="3ed79-107">This document builds uses on the concepts introduced in [Sample showing how to test applications that use EF Core](xref:core/miscellaneous/testing/testing-sample).</span></span>
<span data-ttu-id="3ed79-108">此處顯示的程式碼範例來自此範例。</span><span class="sxs-lookup"><span data-stu-id="3ed79-108">The code examples shown here come from this sample.</span></span>

## <a name="using-sqlite-in-memory-databases"></a><span data-ttu-id="3ed79-109">使用 SQLite 記憶體內部資料庫</span><span class="sxs-lookup"><span data-stu-id="3ed79-109">Using SQLite in-memory databases</span></span>

<span data-ttu-id="3ed79-110">一般來說，SQLite 會將資料庫建立為簡單檔案，並使用您的應用程式在同進程中存取檔案。</span><span class="sxs-lookup"><span data-stu-id="3ed79-110">Normally, SQLite creates databases as simple files and accesses the file in-process with your application.</span></span>
<span data-ttu-id="3ed79-111">這非常快速，特別是在使用快速[SSD](https://en.wikipedia.org/wiki/Solid-state_drive)時。</span><span class="sxs-lookup"><span data-stu-id="3ed79-111">This is very fast, especially when using a fast [SSD](https://en.wikipedia.org/wiki/Solid-state_drive).</span></span> 

<span data-ttu-id="3ed79-112">SQLite 也可以使用純粹在記憶體中建立的資料庫。</span><span class="sxs-lookup"><span data-stu-id="3ed79-112">SQLite can also use databases created purely in-memory.</span></span>
<span data-ttu-id="3ed79-113">只要您瞭解記憶體內部資料庫存留期，這就很容易與 EF Core 搭配使用：</span><span class="sxs-lookup"><span data-stu-id="3ed79-113">This is easy to use with EF Core as long as you understand the in-memory database lifetime:</span></span>
* <span data-ttu-id="3ed79-114">當資料庫的連接開啟時，就會建立該資料庫。</span><span class="sxs-lookup"><span data-stu-id="3ed79-114">The database is created when the connection to it is opened</span></span>
* <span data-ttu-id="3ed79-115">當資料庫的連接已關閉時，就會刪除該資料庫</span><span class="sxs-lookup"><span data-stu-id="3ed79-115">The database is deleted when the connection to it is closed</span></span>

<span data-ttu-id="3ed79-116">EF Core 在指定時，將會使用已開啟的連接，而且永遠不會嘗試關閉它。</span><span class="sxs-lookup"><span data-stu-id="3ed79-116">EF Core will use an already open connection when given one, and will never attempt to close it.</span></span>
<span data-ttu-id="3ed79-117">因此，使用 EF Core 搭配記憶體內部 SQLite 資料庫的關鍵，是在將連接傳遞給 EF 之前，先開啟連線。</span><span class="sxs-lookup"><span data-stu-id="3ed79-117">So the key to using EF Core with an in-memory SQLite database is to open the connection before passing it to EF.</span></span>  

<span data-ttu-id="3ed79-118">此[範例](xref:core/miscellaneous/testing/testing-sample)會使用下列程式碼來達到此動作：</span><span class="sxs-lookup"><span data-stu-id="3ed79-118">The [sample](xref:core/miscellaneous/testing/testing-sample) achieves this with the following code:</span></span>

[!code-csharp[SqliteInMemory](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteInMemoryItemsControllerTest.cs?name=SqliteInMemory)]

<span data-ttu-id="3ed79-119">注意：</span><span class="sxs-lookup"><span data-stu-id="3ed79-119">Notice:</span></span>
* <span data-ttu-id="3ed79-120">`CreateInMemoryDatabase`方法會建立 SQLite 記憶體內部資料庫，並開啟其連接。</span><span class="sxs-lookup"><span data-stu-id="3ed79-120">The `CreateInMemoryDatabase` method creates a SQLite in-memory database and opens the connection to it.</span></span>
* <span data-ttu-id="3ed79-121">建立`DbConnection`的會從解壓縮`ContextOptions`並儲存。</span><span class="sxs-lookup"><span data-stu-id="3ed79-121">The created `DbConnection` is extracted from the `ContextOptions` and saved.</span></span>
* <span data-ttu-id="3ed79-122">處置測試時，會處置此連接，而不會流失資源。</span><span class="sxs-lookup"><span data-stu-id="3ed79-122">The connection is disposed when the test is disposed so that resources are not leaked.</span></span> 

> [!NOTE]
> <span data-ttu-id="3ed79-123">[問題 #16103](https://github.com/dotnet/efcore/issues/16103)是追蹤讓此連接管理變得更容易的方式。</span><span class="sxs-lookup"><span data-stu-id="3ed79-123">[Issue #16103](https://github.com/dotnet/efcore/issues/16103) is tracking ways to make this connection management easier.</span></span> 
