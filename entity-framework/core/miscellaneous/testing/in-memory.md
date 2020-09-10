---
title: 使用 EF 記憶體內部資料庫進行測試-EF Core
description: 使用 EF 記憶體內部資料庫來測試 Entity Framework Core 的應用程式
author: ajcvickers
ms.date: 10/27/2016
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: 353f67fb0e78fefa74fc77d302e505bacb692ed4
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619389"
---
# <a name="testing-with-the-ef-in-memory-database"></a><span data-ttu-id="c0acf-103">使用 EF 記憶體內部資料庫進行測試</span><span class="sxs-lookup"><span data-stu-id="c0acf-103">Testing with the EF In-Memory Database</span></span>

> [!WARNING]
> <span data-ttu-id="c0acf-104">EF 記憶體內部資料庫的行為通常與關係資料庫不同。</span><span class="sxs-lookup"><span data-stu-id="c0acf-104">The EF in-memory database often behaves differently than relational databases.</span></span>
> <span data-ttu-id="c0acf-105">在完全瞭解所涉及的問題和取捨之後，請只使用 EF 記憶體內部資料庫，如 [使用 EF Core 的測試程式碼](xref:core/miscellaneous/testing/index)中所述。</span><span class="sxs-lookup"><span data-stu-id="c0acf-105">Only use the EF in-memory database after fully understanding the issues and trade-offs involved, as discussed in [Testing code that uses EF Core](xref:core/miscellaneous/testing/index).</span></span>  

> [!TIP]
> <span data-ttu-id="c0acf-106">SQLite 是關聯式提供者，也可以使用記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="c0acf-106">SQLite is a relational provider and can also use in-memory databases.</span></span>
> <span data-ttu-id="c0acf-107">請考慮使用此項進行測試，以更接近常見的關係資料庫行為。</span><span class="sxs-lookup"><span data-stu-id="c0acf-107">Consider using this for testing to more closely match common relational database behaviors.</span></span>
> <span data-ttu-id="c0acf-108">這涵蓋在 [使用 SQLite 來測試 EF Core 應用程式](xref:core/miscellaneous/testing/sqlite)。</span><span class="sxs-lookup"><span data-stu-id="c0acf-108">This is covered in [Using SQLite to test an EF Core application](xref:core/miscellaneous/testing/sqlite).</span></span>   

<span data-ttu-id="c0acf-109">此頁面上的資訊現在存在於其他位置：</span><span class="sxs-lookup"><span data-stu-id="c0acf-109">The information on this page now lives in other locations:</span></span>
* <span data-ttu-id="c0acf-110">請參閱 [使用 EF Core 的測試程式碼，](xref:core/miscellaneous/testing/index) 以取得使用 EF 記憶體內部資料庫進行測試的一般資訊。</span><span class="sxs-lookup"><span data-stu-id="c0acf-110">See [Testing code that uses EF Core](xref:core/miscellaneous/testing/index) for general information on testing with the EF in-memory database.</span></span>
* <span data-ttu-id="c0acf-111">請參閱範例，其中顯示如何使用 EF in memory 資料庫 [測試使用 EF Core 的應用程式](xref:core/miscellaneous/testing/testing-sample) 。</span><span class="sxs-lookup"><span data-stu-id="c0acf-111">See [Sample showing how to test applications that use EF Core](xref:core/miscellaneous/testing/testing-sample) for a sample using the EF in-memory database.</span></span>
* <span data-ttu-id="c0acf-112">如需 EF 記憶體內部資料庫的一般資訊，請參閱 [ef 記憶體內部資料庫提供者](xref:core/providers/in-memory/index) 。</span><span class="sxs-lookup"><span data-stu-id="c0acf-112">See [The EF in-memory database provider](xref:core/providers/in-memory/index) for general information about the EF in-memory database.</span></span>
