---
title: 使用 EF 記憶體內部資料庫進行測試-EF Core
author: ajcvickers
description: 使用 EF 記憶體內部資料庫來測試 EF Core 應用程式
ms.date: 10/27/2016
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: f31b3bdedb8c339dbb6baa9d7f2031d023d5757f
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538355"
---
# <a name="testing-with-the-ef-in-memory-database"></a><span data-ttu-id="6f6d5-103">使用 EF 記憶體內部資料庫進行測試</span><span class="sxs-lookup"><span data-stu-id="6f6d5-103">Testing with the EF In-Memory Database</span></span>

> [!WARNING]
> <span data-ttu-id="6f6d5-104">EF 記憶體內部資料庫的行為通常與關係資料庫不同。</span><span class="sxs-lookup"><span data-stu-id="6f6d5-104">The EF in-memory database often behaves differently than relational databases.</span></span>
> <span data-ttu-id="6f6d5-105">請在完全瞭解涉及的問題和取捨之後，才使用 EF 記憶體內部資料庫，如[使用 EF Core 的測試程式碼](xref:core/miscellaneous/testing/index)中所述。</span><span class="sxs-lookup"><span data-stu-id="6f6d5-105">Only use the EF in-memory database after fully understanding the issues and trade-offs involved, as discussed in [Testing code that uses EF Core](xref:core/miscellaneous/testing/index).</span></span>  

> [!TIP]
> <span data-ttu-id="6f6d5-106">SQLite 是關聯式提供者，也可以使用記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="6f6d5-106">SQLite is a relational provider and can also use in-memory databases.</span></span>
> <span data-ttu-id="6f6d5-107">請考慮使用這項測試，以更密切地比對常見的關係資料庫行為。</span><span class="sxs-lookup"><span data-stu-id="6f6d5-107">Consider using this for testing to more closely match common relational database behaviors.</span></span>
> <span data-ttu-id="6f6d5-108">這會在[使用 SQLite 來測試 EF Core 應用程式](xref:core/miscellaneous/testing/sqlite)中討論。</span><span class="sxs-lookup"><span data-stu-id="6f6d5-108">This is covered in [Using SQLite to test an EF Core application](xref:core/miscellaneous/testing/sqlite).</span></span>   

<span data-ttu-id="6f6d5-109">此頁面上的資訊現在位於其他位置：</span><span class="sxs-lookup"><span data-stu-id="6f6d5-109">The information on this page now lives in other locations:</span></span>
* <span data-ttu-id="6f6d5-110">如需使用 EF 記憶體內部資料庫進行測試的一般資訊，請參閱[使用 EF Core 的測試程式碼](xref:core/miscellaneous/testing/index)。</span><span class="sxs-lookup"><span data-stu-id="6f6d5-110">See [Testing code that uses EF Core](xref:core/miscellaneous/testing/index) for general information on testing with the EF in-memory database.</span></span>
* <span data-ttu-id="6f6d5-111">如需使用 EF 記憶體內部資料庫的範例，請參閱[示範如何測試使用 EF Core 的應用程式](xref:core/miscellaneous/testing/testing-sample)。</span><span class="sxs-lookup"><span data-stu-id="6f6d5-111">See [Sample showing how to test applications that use EF Core](xref:core/miscellaneous/testing/testing-sample) for a sample using the EF in-memory database.</span></span>
* <span data-ttu-id="6f6d5-112">如需 EF 記憶體內部資料庫的一般資訊，請參閱[ef 記憶體內部資料庫提供者](xref:core/providers/in-memory/index)。</span><span class="sxs-lookup"><span data-stu-id="6f6d5-112">See [The EF in-memory database provider](xref:core/providers/in-memory/index) for general information about the EF in-memory database.</span></span>
