---
title: 非同步儲存 - EF Core
author: rowanmiller
ms.date: 01/24/2017
ms.assetid: b64a606e-ecd9-4807-829a-b6ec05ade33f
uid: core/saving/async
ms.openlocfilehash: 0823b86f0579dd3e42f6bd2aebfb433d3cbe00ab
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "78417639"
---
# <a name="asynchronous-saving"></a><span data-ttu-id="b1130-102">非同步儲存</span><span class="sxs-lookup"><span data-stu-id="b1130-102">Asynchronous Saving</span></span>

<span data-ttu-id="b1130-103">非同步儲存可避免在將變更寫入至資料庫時阻斷執行緒。</span><span class="sxs-lookup"><span data-stu-id="b1130-103">Asynchronous saving avoids blocking a thread while the changes are written to the database.</span></span> <span data-ttu-id="b1130-104">若要避免凍結大型用戶端應用程式的 UI，這非常實用。</span><span class="sxs-lookup"><span data-stu-id="b1130-104">This can be useful to avoid freezing the UI of a thick-client application.</span></span> <span data-ttu-id="b1130-105">非同步作業也可以提高 Web 應用程式中的輸送量，其中可在資料庫作業完成時釋出執行緒，來為其他要求提供服務。</span><span class="sxs-lookup"><span data-stu-id="b1130-105">Asynchronous operations can also increase throughput in a web application, where the thread can be freed up to service other requests while the database operation completes.</span></span> <span data-ttu-id="b1130-106">有關詳細資訊,請參閱[C# 中的非同步編程](https://docs.microsoft.com/dotnet/csharp/async)。</span><span class="sxs-lookup"><span data-stu-id="b1130-106">For more information, see [Asynchronous Programming in C#](https://docs.microsoft.com/dotnet/csharp/async).</span></span>

> [!WARNING]  
> <span data-ttu-id="b1130-107">EF Core 不支援在同一個內容執行個體上執行多個平行作業。</span><span class="sxs-lookup"><span data-stu-id="b1130-107">EF Core does not support multiple parallel operations being run on the same context instance.</span></span> <span data-ttu-id="b1130-108">您應該一律等候作業完成，再開始下一項作業。</span><span class="sxs-lookup"><span data-stu-id="b1130-108">You should always wait for an operation to complete before beginning the next operation.</span></span> <span data-ttu-id="b1130-109">通常在每個非同步作業上使用 `await` 關鍵字，即可達到此目的。</span><span class="sxs-lookup"><span data-stu-id="b1130-109">This is typically done by using the `await` keyword on each asynchronous operation.</span></span>

<span data-ttu-id="b1130-110">Entity Framework Core 提供 `DbContext.SaveChangesAsync()` 作為 `DbContext.SaveChanges()`的替代方案。</span><span class="sxs-lookup"><span data-stu-id="b1130-110">Entity Framework Core provides `DbContext.SaveChangesAsync()` as an asynchronous alternative to `DbContext.SaveChanges()`.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Async/Sample.cs#Sample)]
