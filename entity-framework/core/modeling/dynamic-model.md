---
title: 在具有相同 DbCoNtext 類型的多個模型之間交替-EF Core
author: AndriySvyryd
ms.date: 12/10/2017
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/dynamic-model
ms.openlocfilehash: 034076b1595894e80b98467354f6c9f139bd7426
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655718"
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a><span data-ttu-id="3c5a4-102">在具有相同 DbCoNtext 類型的多個模型之間交替</span><span class="sxs-lookup"><span data-stu-id="3c5a4-102">Alternating between multiple models with the same DbContext type</span></span>

<span data-ttu-id="3c5a4-103">內建的模型 `OnModelCreating` 可以在內容上使用屬性來變更模型的建立方式。</span><span class="sxs-lookup"><span data-stu-id="3c5a4-103">The model built in `OnModelCreating` could use a property on the context to change how the model is built.</span></span> <span data-ttu-id="3c5a4-104">例如，它可以用來排除特定的屬性：</span><span class="sxs-lookup"><span data-stu-id="3c5a4-104">For example it could be used to exclude a certain property:</span></span>

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicContext.cs?name=Class)]

## <a name="imodelcachekeyfactory"></a><span data-ttu-id="3c5a4-105">IModelCacheKeyFactory</span><span class="sxs-lookup"><span data-stu-id="3c5a4-105">IModelCacheKeyFactory</span></span>

<span data-ttu-id="3c5a4-106">不過，如果您嘗試執行上述動作而沒有其他變更，則每次為 `IgnoreIntProperty`的任何值建立新內容時，都會取得相同的模型。</span><span class="sxs-lookup"><span data-stu-id="3c5a4-106">However if you tried doing the above without additional changes you would get the same model every time a new context is created for any value of `IgnoreIntProperty`.</span></span> <span data-ttu-id="3c5a4-107">這是由 EF 用來改善效能的模型快取機制所造成，只會叫用 `OnModelCreating` 一次並快取模型。</span><span class="sxs-lookup"><span data-stu-id="3c5a4-107">This is caused by the model caching mechanism EF uses to improve the performance by only invoking `OnModelCreating` once and caching the model.</span></span>

<span data-ttu-id="3c5a4-108">根據預設，EF 會假設針對任何指定的內容類型，此模型會是相同的。</span><span class="sxs-lookup"><span data-stu-id="3c5a4-108">By default EF assumes that for any given context type the model will be the same.</span></span> <span data-ttu-id="3c5a4-109">若要完成這項工作，`IModelCacheKeyFactory` 的預設執行會傳回只包含內容類型的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="3c5a4-109">To accomplish this the default implementation of `IModelCacheKeyFactory` returns a key that just contains the context type.</span></span> <span data-ttu-id="3c5a4-110">若要變更這種情況，您必須取代 `IModelCacheKeyFactory` 服務。</span><span class="sxs-lookup"><span data-stu-id="3c5a4-110">To change this you need to replace the `IModelCacheKeyFactory` service.</span></span> <span data-ttu-id="3c5a4-111">新的執行必須傳回可與其他模型索引鍵比較的物件，其使用的 `Equals` 方法會將所有影響模型的變數納入考慮：</span><span class="sxs-lookup"><span data-stu-id="3c5a4-111">The new implementation needs to return an object that can be compared to other model keys using the `Equals` method that takes into account all the variables that affect the model:</span></span>

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicModelCacheKeyFactory.cs?name=Class)]
