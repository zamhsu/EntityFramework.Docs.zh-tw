---
title: 交替執行多個模型與 DbContext 類型相同的 EF Core
author: AndriySvyryd
ms.date: 12/10/2017
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/dynamic-model
ms.openlocfilehash: 1d87efb668c12a2862583fba16a6c444b3cda9de
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994982"
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a><span data-ttu-id="334dc-102">具有相同 DbContext 類型的多個模型之間交替</span><span class="sxs-lookup"><span data-stu-id="334dc-102">Alternating between multiple models with the same DbContext type</span></span>

<span data-ttu-id="334dc-103">內建模型`OnModelCreating`內容無法使用的屬性來變更模型的建置方式。</span><span class="sxs-lookup"><span data-stu-id="334dc-103">The model built in `OnModelCreating` could use a property on the context to change how the model is built.</span></span> <span data-ttu-id="334dc-104">比方說它無法用來排除特定的屬性：</span><span class="sxs-lookup"><span data-stu-id="334dc-104">For example it could be used to exclude a certain property:</span></span>

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicContext.cs?name=Class)]

## <a name="imodelcachekeyfactory"></a><span data-ttu-id="334dc-105">IModelCacheKeyFactory</span><span class="sxs-lookup"><span data-stu-id="334dc-105">IModelCacheKeyFactory</span></span>
<span data-ttu-id="334dc-106">不過如果您嘗試這樣做而不需要額外的變更會得到相同的模型每次在新的內容建立的任何值`IgnoreIntProperty`。</span><span class="sxs-lookup"><span data-stu-id="334dc-106">However if you tried doing the above without additional changes you would get the same model every time a new context is created for any value of `IgnoreIntProperty`.</span></span> <span data-ttu-id="334dc-107">這種情形因模型快取以改善效能，藉由只叫用 EF 所使用的機制`OnModelCreating`一次，快取模型。</span><span class="sxs-lookup"><span data-stu-id="334dc-107">This is caused by the model caching mechanism EF uses to improve the performance by only invoking `OnModelCreating` once and caching the model.</span></span>

<span data-ttu-id="334dc-108">根據預設 EF 會假設為任何指定的內容類型的模型將會相同。</span><span class="sxs-lookup"><span data-stu-id="334dc-108">By default EF assumes that for any given context type the model will be the same.</span></span> <span data-ttu-id="334dc-109">若要這麼做的預設實作`IModelCacheKeyFactory`傳回只包含內容類型的金鑰。</span><span class="sxs-lookup"><span data-stu-id="334dc-109">To accomplish this the default implementation of `IModelCacheKeyFactory` returns a key that just contains the context type.</span></span> <span data-ttu-id="334dc-110">若要變更此您需要更換`IModelCacheKeyFactory`服務。</span><span class="sxs-lookup"><span data-stu-id="334dc-110">To change this you need to replace the `IModelCacheKeyFactory` service.</span></span> <span data-ttu-id="334dc-111">新的實作必須使用其他模型索引鍵傳回物件可比較`Equals`會考量會影響模型的所有變數的方法：</span><span class="sxs-lookup"><span data-stu-id="334dc-111">The new implementation needs to return an object that can be compared to other model keys using the `Equals` method that takes into account all the variables that affect the model:</span></span>

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicModelCacheKeyFactory.cs?name=Class)]
