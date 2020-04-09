---
title: 非同步查詢 - EF Core
author: smitpatel
ms.date: 10/03/2019
ms.assetid: b6429b14-cba0-4af4-878f-b829777c89cb
uid: core/querying/async
ms.openlocfilehash: ce26db32a616dac5edac2a8451014ae63cbfc12d
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "78417750"
---
# <a name="asynchronous-queries"></a><span data-ttu-id="f61a9-102">非同步查詢</span><span class="sxs-lookup"><span data-stu-id="f61a9-102">Asynchronous Queries</span></span>

<span data-ttu-id="f61a9-103">非同步查詢可避免在資料庫中執行查詢時封鎖執行緒。</span><span class="sxs-lookup"><span data-stu-id="f61a9-103">Asynchronous queries avoid blocking a thread while the query is executed in the database.</span></span> <span data-ttu-id="f61a9-104">非同步查詢對於在厚客戶端應用程式中保留回應式 UI 非常重要。</span><span class="sxs-lookup"><span data-stu-id="f61a9-104">Async queries are important for keeping a responsive UI in thick-client applications.</span></span> <span data-ttu-id="f61a9-105">它們還可以提高 Web 應用程式中的輸送量,在 Web 應用程式中釋放線程以為 Web 應用程式中的其他請求提供服務。</span><span class="sxs-lookup"><span data-stu-id="f61a9-105">They can also increase throughput in web applications where they free up the thread to service other requests in web applications.</span></span> <span data-ttu-id="f61a9-106">有關詳細資訊,請參閱[C# 中的非同步編程](/dotnet/csharp/async)。</span><span class="sxs-lookup"><span data-stu-id="f61a9-106">For more information, see [Asynchronous Programming in C#](/dotnet/csharp/async).</span></span>

> [!WARNING]  
> <span data-ttu-id="f61a9-107">EF Core 不支援在同一上下文實例上運行多個並行操作。</span><span class="sxs-lookup"><span data-stu-id="f61a9-107">EF Core doesn't support multiple parallel operations being run on the same context instance.</span></span> <span data-ttu-id="f61a9-108">您應該一律等候作業完成，再開始下一項作業。</span><span class="sxs-lookup"><span data-stu-id="f61a9-108">You should always wait for an operation to complete before beginning the next operation.</span></span> <span data-ttu-id="f61a9-109">這通常是在每個異步操作上`await`使用關鍵字來完成的。</span><span class="sxs-lookup"><span data-stu-id="f61a9-109">This is typically done by using the `await` keyword on each async operation.</span></span>

<span data-ttu-id="f61a9-110">實體框架核心提供了一組類似於 LINQ 方法的非同步擴充方法,這些方法執行查詢並返回結果。</span><span class="sxs-lookup"><span data-stu-id="f61a9-110">Entity Framework Core provides a set of async extension methods similar to the LINQ methods, which execute a query and return results.</span></span> <span data-ttu-id="f61a9-111">範例包括`ToListAsync()``ToArrayAsync()`、 `SingleAsync()`、 、 、 、</span><span class="sxs-lookup"><span data-stu-id="f61a9-111">Examples include `ToListAsync()`, `ToArrayAsync()`, `SingleAsync()`.</span></span> <span data-ttu-id="f61a9-112">某些 LINQ 運算符沒有非同步版本,`Where(...)`例如,`OrderBy(...)`或者因為這些方法僅建構 LINQ 運算式樹,並且不會導致在資料庫中執行查詢。</span><span class="sxs-lookup"><span data-stu-id="f61a9-112">There are no async versions of some LINQ operators such as `Where(...)` or `OrderBy(...)` because these methods only build up the LINQ expression tree and don't cause the query to be executed in the database.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="f61a9-113">EF Core 非同步擴充方法定義於 `Microsoft.EntityFrameworkCore` 命名空間中。</span><span class="sxs-lookup"><span data-stu-id="f61a9-113">The EF Core async extension methods are defined in the `Microsoft.EntityFrameworkCore` namespace.</span></span> <span data-ttu-id="f61a9-114">您必須先匯入此命名空間，才能使用方法。</span><span class="sxs-lookup"><span data-stu-id="f61a9-114">This namespace must be imported for the methods to be available.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Async/Sample.cs#ToListAsync)]
