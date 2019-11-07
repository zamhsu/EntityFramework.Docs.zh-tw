---
title: 包括 & 排除類型-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: cbe6935e-2679-4b77-8914-a8d772240cf1
uid: core/modeling/included-types
ms.openlocfilehash: 1249e71c02e58afe7fe06b3fdcf523dfa0c9b17c
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655732"
---
# <a name="including--excluding-types"></a><span data-ttu-id="95e78-102">包含與排除類型</span><span class="sxs-lookup"><span data-stu-id="95e78-102">Including & Excluding Types</span></span>

<span data-ttu-id="95e78-103">在模型中包含類型，表示 EF 具有該類型的中繼資料，並會嘗試從資料庫讀取及寫入實例。</span><span class="sxs-lookup"><span data-stu-id="95e78-103">Including a type in the model means that EF has metadata about that type and will attempt to read and write instances from/to the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="95e78-104">慣例</span><span class="sxs-lookup"><span data-stu-id="95e78-104">Conventions</span></span>

<span data-ttu-id="95e78-105">依照慣例，在您的模型中，`DbSet` 屬性中公開的類型會包含在您的模型中。</span><span class="sxs-lookup"><span data-stu-id="95e78-105">By convention, types that are exposed in `DbSet` properties on your context are included in your model.</span></span> <span data-ttu-id="95e78-106">此外，也包含 `OnModelCreating` 方法中所述的類型。</span><span class="sxs-lookup"><span data-stu-id="95e78-106">In addition, types that are mentioned in the `OnModelCreating` method are also included.</span></span> <span data-ttu-id="95e78-107">最後，以遞迴方式探索已探索類型的導覽屬性所找到的任何類型也會包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="95e78-107">Finally, any types that are found by recursively exploring the navigation properties of discovered types are also included in the model.</span></span>

<span data-ttu-id="95e78-108">**例如，在下列程式碼中，會探索所有三種類型：**</span><span class="sxs-lookup"><span data-stu-id="95e78-108">**For example, in the following code listing all three types are discovered:**</span></span>

* <span data-ttu-id="95e78-109">`Blog`，因為它是在內容的 `DbSet` 屬性中公開</span><span class="sxs-lookup"><span data-stu-id="95e78-109">`Blog` because it is exposed in a `DbSet` property on the context</span></span>

* <span data-ttu-id="95e78-110">`Post`，因為它是透過 `Blog.Posts` 導覽屬性進行探索</span><span class="sxs-lookup"><span data-stu-id="95e78-110">`Post` because it is discovered via the `Blog.Posts` navigation property</span></span>

* <span data-ttu-id="95e78-111">`AuditEntry`，因為 `OnModelCreating` 中提及</span><span class="sxs-lookup"><span data-stu-id="95e78-111">`AuditEntry` because it is mentioned in `OnModelCreating`</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/IncludedTypes.cs?name=IncludedTypes&highlight=3,7,16)]

## <a name="data-annotations"></a><span data-ttu-id="95e78-112">資料註釋</span><span class="sxs-lookup"><span data-stu-id="95e78-112">Data Annotations</span></span>

<span data-ttu-id="95e78-113">您可以使用資料批註，從模型中排除型別。</span><span class="sxs-lookup"><span data-stu-id="95e78-113">You can use Data Annotations to exclude a type from the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?highlight=20)]

## <a name="fluent-api"></a><span data-ttu-id="95e78-114">Fluent API</span><span class="sxs-lookup"><span data-stu-id="95e78-114">Fluent API</span></span>

<span data-ttu-id="95e78-115">您可以使用流暢的 API，從模型中排除型別。</span><span class="sxs-lookup"><span data-stu-id="95e78-115">You can use the Fluent API to exclude a type from the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?highlight=12)]
