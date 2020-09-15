---
title: 載入相關資料 - EF Core
description: 使用 Entity Framework Core 載入相關資料的不同策略
author: roji
ms.date: 9/11/2020
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: 5c1addac6735de39d5c72f447d5d06280411a21e
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90078917"
---
# <a name="loading-related-data"></a><span data-ttu-id="43679-103">載入相關資料</span><span class="sxs-lookup"><span data-stu-id="43679-103">Loading Related Data</span></span>

<span data-ttu-id="43679-104">Entity Framework Core 可讓您在模型中使用導覽屬性來載入相關實體。</span><span class="sxs-lookup"><span data-stu-id="43679-104">Entity Framework Core allows you to use the navigation properties in your model to load related entities.</span></span> <span data-ttu-id="43679-105">有三種常見的 O/RM 模式可用來載入相關資料。</span><span class="sxs-lookup"><span data-stu-id="43679-105">There are three common O/RM patterns used to load related data.</span></span>

* <span data-ttu-id="43679-106">**[積極式載入](xref:core/querying/related-data/eager)** 表示相關的資料會從資料庫載入做為初始查詢的一部分。</span><span class="sxs-lookup"><span data-stu-id="43679-106">**[Eager loading](xref:core/querying/related-data/eager)** means that the related data is loaded from the database as part of the initial query.</span></span>
* <span data-ttu-id="43679-107">**[明確載入](xref:core/querying/related-data/explicit)** 表示相關資料會在稍後明確從資料庫載入。</span><span class="sxs-lookup"><span data-stu-id="43679-107">**[Explicit loading](xref:core/querying/related-data/explicit)** means that the related data is explicitly loaded from the database at a later time.</span></span>
* <span data-ttu-id="43679-108">消極式**[載入](xref:core/querying/related-data/lazy)** 表示在存取導覽屬性時，會從資料庫以透明的方式載入相關資料。</span><span class="sxs-lookup"><span data-stu-id="43679-108">**[Lazy loading](xref:core/querying/related-data/lazy)** means that the related data is transparently loaded from the database when the navigation property is accessed.</span></span>

> [!TIP]
> <span data-ttu-id="43679-109">您可以在 GitHub 上查看本節中的 [範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) 。</span><span class="sxs-lookup"><span data-stu-id="43679-109">You can view the [samples](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) under this section on GitHub.</span></span>
