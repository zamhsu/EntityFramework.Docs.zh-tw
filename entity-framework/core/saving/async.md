---
title: "非同步儲存-EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 01/24/2017
ms.assetid: b64a606e-ecd9-4807-829a-b6ec05ade33f
ms.technology: entity-framework-core
uid: core/saving/async
ms.openlocfilehash: 640e5f131b0e9ef4e5028d1dcaf80f3e5bbd9d9b
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="asynchronous-saving"></a><span data-ttu-id="3f4ac-102">非同步儲存</span><span class="sxs-lookup"><span data-stu-id="3f4ac-102">Asynchronous Saving</span></span>

<span data-ttu-id="3f4ac-103">非同步儲存可避免封鎖執行緒的變更會寫入至資料庫時。</span><span class="sxs-lookup"><span data-stu-id="3f4ac-103">Asynchronous saving avoids blocking a thread while the changes are written to the database.</span></span> <span data-ttu-id="3f4ac-104">這可避免凍結大型用戶端應用程式的 UI。</span><span class="sxs-lookup"><span data-stu-id="3f4ac-104">This can be useful to avoid freezing the UI of a thick-client application.</span></span> <span data-ttu-id="3f4ac-105">非同步作業，也可以增加 web 應用程式，其中執行緒可以釋放資料庫作業完成時服務的其他要求中的輸送量。</span><span class="sxs-lookup"><span data-stu-id="3f4ac-105">Asynchronous operations can also increase throughput in a web application, where the thread can be freed up to service other requests while the database operation completes.</span></span> <span data-ttu-id="3f4ac-106">如需詳細資訊，請參閱[在 C# 中的非同步程式設計](https://docs.microsoft.com/dotnet/csharp/async)。</span><span class="sxs-lookup"><span data-stu-id="3f4ac-106">For more information, see [Asynchronous Programming in C#](https://docs.microsoft.com/dotnet/csharp/async).</span></span>

> [!WARNING]  
> <span data-ttu-id="3f4ac-107">EF 核心不支援在相同的內容執行個體上執行的多個平行作業。</span><span class="sxs-lookup"><span data-stu-id="3f4ac-107">EF Core does not support multiple parallel operations being run on the same context instance.</span></span> <span data-ttu-id="3f4ac-108">您應該永遠等候作業完成，然後再開始下一項作業。</span><span class="sxs-lookup"><span data-stu-id="3f4ac-108">You should always wait for an operation to complete before beginning the next operation.</span></span> <span data-ttu-id="3f4ac-109">這通常是使用`await`上每個非同步作業的關鍵字。</span><span class="sxs-lookup"><span data-stu-id="3f4ac-109">This is typically done by using the `await` keyword on each asynchronous operation.</span></span>

<span data-ttu-id="3f4ac-110">Entity Framework Core 提供`DbContext.SaveChangesAsync()`做為非同步替代`DbContext.SaveChanges()`。</span><span class="sxs-lookup"><span data-stu-id="3f4ac-110">Entity Framework Core provides `DbContext.SaveChangesAsync()` as an asynchronous alternative to `DbContext.SaveChanges()`.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Async/Sample.cs#Sample)]
