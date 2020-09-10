---
title: 非同步程式設計-EF Core
description: 使用 Entity Framework Core 以非同步方式查詢和儲存資料
author: roji
ms.date: 9/2/2020
ms.assetid: 38f71624-7a68-4c72-a918-3e7b858ef090
uid: core/miscellaneous/async
ms.openlocfilehash: c5dff82fe2442cd5a21c143933cac2c5d4588281
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620752"
---
# <a name="asynchronous-programming"></a><span data-ttu-id="1efe7-103">非同步程式設計</span><span class="sxs-lookup"><span data-stu-id="1efe7-103">Asynchronous Programming</span></span>

<span data-ttu-id="1efe7-104">非同步作業可避免在資料庫中執行查詢時封鎖執行緒。</span><span class="sxs-lookup"><span data-stu-id="1efe7-104">Asynchronous operations avoid blocking a thread while the query is executed in the database.</span></span> <span data-ttu-id="1efe7-105">非同步作業對於在豐富的用戶端應用程式中保持回應式 UI 很重要，也可以在 web 應用程式中增加輸送量，以便在 web 應用程式中釋放執行緒，以服務其他要求。</span><span class="sxs-lookup"><span data-stu-id="1efe7-105">Async operations are important for keeping a responsive UI in rich client applications, and can also increase throughput in web applications where they free up the thread to service other requests in web applications.</span></span>

<span data-ttu-id="1efe7-106">在 .NET standard 之後，EF Core 提供非同步對應給所有執行 i/o 的同步方法。</span><span class="sxs-lookup"><span data-stu-id="1efe7-106">Following the .NET standard, EF Core provides asynchronous counterparts to all synchronous methods which perform I/O.</span></span> <span data-ttu-id="1efe7-107">這些與同步處理方法具有相同的效果，而且可以搭配 c # `async` 和關鍵字使用 `await` 。</span><span class="sxs-lookup"><span data-stu-id="1efe7-107">These have the same effects as the sync methods, and can be used with the C# `async` and `await` keywords.</span></span> <span data-ttu-id="1efe7-108">例如，不使用 DbCoNtext，它會在執行資料庫 i/o 時封鎖執行緒，DbCoNtext. SaveChangesAsync 可以使用：</span><span class="sxs-lookup"><span data-stu-id="1efe7-108">For example, instead of using DbContext.SaveChanges, which will block a thread while database I/O is performed, DbContext.SaveChangesAsync can be used:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Async/Program.cs#SaveChangesAsync)]

<span data-ttu-id="1efe7-109">如需詳細資訊，請參閱 [一般 c # 非同步程式設計](/dotnet/csharp/async)檔。</span><span class="sxs-lookup"><span data-stu-id="1efe7-109">For more information, see [the general C# asynchronous programming docs](/dotnet/csharp/async).</span></span>

> [!WARNING]
> <span data-ttu-id="1efe7-110">EF Core 不支援在相同的內容實例上執行多個平行作業。</span><span class="sxs-lookup"><span data-stu-id="1efe7-110">EF Core doesn't support multiple parallel operations being run on the same context instance.</span></span> <span data-ttu-id="1efe7-111">您應該一律等候作業完成，再開始下一項作業。</span><span class="sxs-lookup"><span data-stu-id="1efe7-111">You should always wait for an operation to complete before beginning the next operation.</span></span> <span data-ttu-id="1efe7-112">這通常會 `await` 在每個非同步作業上使用關鍵字來完成。</span><span class="sxs-lookup"><span data-stu-id="1efe7-112">This is typically done by using the `await` keyword on each async operation.</span></span>

> [!NOTE]
> <span data-ttu-id="1efe7-113">EF Core 將取消權杖傳遞給使用中的基礎資料庫提供者 (例如 SqlClient) 。</span><span class="sxs-lookup"><span data-stu-id="1efe7-113">EF Core passes cancellation tokens down to the underlying database provider in use (e.g. Microsoft.Data.SqlClient).</span></span> <span data-ttu-id="1efe7-114">這些權杖可能會或可能不會被接受-請洽詢您資料庫提供者的檔。</span><span class="sxs-lookup"><span data-stu-id="1efe7-114">These tokens may or may not be honored - consult your database provider's documentation.</span></span>  

## <a name="async-linq-operators"></a><span data-ttu-id="1efe7-115">非同步 LINQ 運算子</span><span class="sxs-lookup"><span data-stu-id="1efe7-115">Async LINQ operators</span></span>

<span data-ttu-id="1efe7-116">為了支援以非同步方式執行 LINQ 查詢，EF Core 提供一組非同步擴充方法，可執行查詢並傳回結果。</span><span class="sxs-lookup"><span data-stu-id="1efe7-116">In order to support executing LINQ queries asynchronously, EF Core provides a set of async extension methods which execute the query and return results.</span></span> <span data-ttu-id="1efe7-117">這些對等的標準、同步 LINQ 運算子包括 ToListAsync、SingleAsync、AsAsyncEnumerable 等等：</span><span class="sxs-lookup"><span data-stu-id="1efe7-117">These counterparts to the standard, synchronous LINQ operators include ToListAsync, SingleAsync, AsAsyncEnumerable, etc.:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Async/Program.cs#ToListAsync)]

<span data-ttu-id="1efe7-118">請注意，某些 LINQ 運算子（例如 Where 或 OrderBy）沒有任何非同步版本，因為這些運算子只會建立 LINQ 運算式樹狀架構，而不會在資料庫中執行查詢。</span><span class="sxs-lookup"><span data-stu-id="1efe7-118">Note that there are no async versions of some LINQ operators such as Where or OrderBy, because these only build up the LINQ expression tree and don't cause the query to be executed in the database.</span></span> <span data-ttu-id="1efe7-119">只有造成查詢執行的運算子具有非同步對應專案。</span><span class="sxs-lookup"><span data-stu-id="1efe7-119">Only operators which cause query execution have async counterparts.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1efe7-120">EF Core 非同步擴充方法定義於 `Microsoft.EntityFrameworkCore` 命名空間中。</span><span class="sxs-lookup"><span data-stu-id="1efe7-120">The EF Core async extension methods are defined in the `Microsoft.EntityFrameworkCore` namespace.</span></span> <span data-ttu-id="1efe7-121">您必須先匯入此命名空間，才能使用方法。</span><span class="sxs-lookup"><span data-stu-id="1efe7-121">This namespace must be imported for the methods to be available.</span></span>

## <a name="client-side-async-linq-operators"></a><span data-ttu-id="1efe7-122">用戶端非同步 LINQ 運算子</span><span class="sxs-lookup"><span data-stu-id="1efe7-122">Client-side async LINQ operators</span></span>

<span data-ttu-id="1efe7-123">以上所討論的非同步 LINQ 運算子只能用於 EF 查詢-您無法將它們與用戶端 LINQ to Objects 查詢搭配使用。</span><span class="sxs-lookup"><span data-stu-id="1efe7-123">The async LINQ operators discussed above can only be used on EF queries - you cannot use them with client-side LINQ to Objects query.</span></span> <span data-ttu-id="1efe7-124">若要在 EF 之外執行用戶端非同步 LINQ 作業，請使用 system.string [封裝](https://www.nuget.org/packages/System.Interactive.Async);在用戶端上執行無法在伺服器上進行評估的作業時，此封裝會特別有用。</span><span class="sxs-lookup"><span data-stu-id="1efe7-124">To perform client-side async LINQ operations outside of EF, use the [System.Interactive.Async package](https://www.nuget.org/packages/System.Interactive.Async); this package can be especially useful for performing operations on the client that cannot be translated for evaluation at the server.</span></span>

<span data-ttu-id="1efe7-125">可惜的是，參考 system.string 會導致套用至 EF DbSets 的 LINQ 運算子發生不明確的調用編譯錯誤;這會讓您難以在相同專案中使用 EF 和 Async。</span><span class="sxs-lookup"><span data-stu-id="1efe7-125">Unfortunately, referencing System.Interactive.Async causes ambiguous invocation compilation errors on LINQ operators applied to EF's DbSets; this makes it hard to use both EF and System.Interactive.Async in the same project.</span></span> <span data-ttu-id="1efe7-126">若要解決此問題，請將 AsQueryable 新增至您的 DbSet：</span><span class="sxs-lookup"><span data-stu-id="1efe7-126">To work around this issue, add AsQueryable to your DbSet:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/AsyncWithSystemInteractive/Program.cs#SystemInteractiveAsync)]
