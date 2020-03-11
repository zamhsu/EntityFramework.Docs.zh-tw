---
title: 非同步查詢 - EF Core
author: smitpatel
ms.date: 10/03/2019
ms.assetid: b6429b14-cba0-4af4-878f-b829777c89cb
uid: core/querying/async
ms.openlocfilehash: ce26db32a616dac5edac2a8451014ae63cbfc12d
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417750"
---
# <a name="asynchronous-queries"></a><span data-ttu-id="d01b7-102">非同步查詢</span><span class="sxs-lookup"><span data-stu-id="d01b7-102">Asynchronous Queries</span></span>

<span data-ttu-id="d01b7-103">非同步查詢可避免在資料庫中執行查詢時封鎖執行緒。</span><span class="sxs-lookup"><span data-stu-id="d01b7-103">Asynchronous queries avoid blocking a thread while the query is executed in the database.</span></span> <span data-ttu-id="d01b7-104">非同步查詢對於在豐富型用戶端應用程式中保持回應式 UI 是很重要的。</span><span class="sxs-lookup"><span data-stu-id="d01b7-104">Async queries are important for keeping a responsive UI in thick-client applications.</span></span> <span data-ttu-id="d01b7-105">它們也可以增加 web 應用程式中的輸送量，讓他們釋放執行緒來服務 web 應用程式中的其他要求。</span><span class="sxs-lookup"><span data-stu-id="d01b7-105">They can also increase throughput in web applications where they free up the thread to service other requests in web applications.</span></span> <span data-ttu-id="d01b7-106">如需詳細資訊，請參閱[使用 C# 進行非同步程式設計](/dotnet/csharp/async)。</span><span class="sxs-lookup"><span data-stu-id="d01b7-106">For more information, see [Asynchronous Programming in C#](/dotnet/csharp/async).</span></span>

> [!WARNING]  
> <span data-ttu-id="d01b7-107">EF Core 不支援在相同的內容實例上執行多個平行作業。</span><span class="sxs-lookup"><span data-stu-id="d01b7-107">EF Core doesn't support multiple parallel operations being run on the same context instance.</span></span> <span data-ttu-id="d01b7-108">您應該一律等候作業完成，再開始下一項作業。</span><span class="sxs-lookup"><span data-stu-id="d01b7-108">You should always wait for an operation to complete before beginning the next operation.</span></span> <span data-ttu-id="d01b7-109">這通常是在每個非同步作業上使用 `await` 關鍵字來完成。</span><span class="sxs-lookup"><span data-stu-id="d01b7-109">This is typically done by using the `await` keyword on each async operation.</span></span>

<span data-ttu-id="d01b7-110">Entity Framework Core 提供一組非同步擴充方法，類似于 LINQ 方法，其會執行查詢並傳回結果。</span><span class="sxs-lookup"><span data-stu-id="d01b7-110">Entity Framework Core provides a set of async extension methods similar to the LINQ methods, which execute a query and return results.</span></span> <span data-ttu-id="d01b7-111">範例包括 `ToListAsync()`、`ToArrayAsync()``SingleAsync()`。</span><span class="sxs-lookup"><span data-stu-id="d01b7-111">Examples include `ToListAsync()`, `ToArrayAsync()`, `SingleAsync()`.</span></span> <span data-ttu-id="d01b7-112">有些 LINQ 運算子（例如 `Where(...)` 或 `OrderBy(...)`）沒有非同步版本，因為這些方法只會建立 LINQ 運算式樹狀架構，而不會導致在資料庫中執行查詢。</span><span class="sxs-lookup"><span data-stu-id="d01b7-112">There are no async versions of some LINQ operators such as `Where(...)` or `OrderBy(...)` because these methods only build up the LINQ expression tree and don't cause the query to be executed in the database.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="d01b7-113">EF Core 非同步擴充方法定義於 `Microsoft.EntityFrameworkCore` 命名空間中。</span><span class="sxs-lookup"><span data-stu-id="d01b7-113">The EF Core async extension methods are defined in the `Microsoft.EntityFrameworkCore` namespace.</span></span> <span data-ttu-id="d01b7-114">您必須先匯入此命名空間，才能使用方法。</span><span class="sxs-lookup"><span data-stu-id="d01b7-114">This namespace must be imported for the methods to be available.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Async/Sample.cs#ToListAsync)]
