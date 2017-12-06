---
title: "交替執行多個模型與 DbContext 類型相同的 EF 核心"
author: AndriySvyryd
uid: core/modeling/dynamic-model
ms.openlocfilehash: e6828c62c55ae6f48d46a20528268264c3f22b26
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a><span data-ttu-id="a89c8-102">交替執行多個模型與 DbContext 類型相同</span><span class="sxs-lookup"><span data-stu-id="a89c8-102">Alternating between multiple models with the same DbContext type</span></span>

<span data-ttu-id="a89c8-103">建立模型`OnModelCreating`內容上無法使用的屬性來變更模型的建立方式。</span><span class="sxs-lookup"><span data-stu-id="a89c8-103">The model built in `OnModelCreating` could use a property on the context to change how the model is built.</span></span> <span data-ttu-id="a89c8-104">比方說它無法用來排除特定的屬性：</span><span class="sxs-lookup"><span data-stu-id="a89c8-104">For example it could be used to exclude a certain property:</span></span>

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicContext.cs?name=Class)]

## <a name="imodelcachekeyfactory"></a><span data-ttu-id="a89c8-105">IModelCacheKeyFactory</span><span class="sxs-lookup"><span data-stu-id="a89c8-105">IModelCacheKeyFactory</span></span>
<span data-ttu-id="a89c8-106">不過如果您嘗試執行上述不需要任何其他變更您會得到相同的模型每次在新的內容建立的任何值`IgnoreIntProperty`。</span><span class="sxs-lookup"><span data-stu-id="a89c8-106">However if you tried doing the above without additional changes you would get the same model every time a new context is created for any value of `IgnoreIntProperty`.</span></span> <span data-ttu-id="a89c8-107">這因為模型快取的機制來改善效能，藉由只叫用使用 EF`OnModelCreating`一次和快取模型。</span><span class="sxs-lookup"><span data-stu-id="a89c8-107">This is caused by the model caching mechanism EF uses to improve the performance by only invoking `OnModelCreating` once and caching the model.</span></span>

<span data-ttu-id="a89c8-108">依預設 EF 會假設為任何指定的內容類型的模型將會相同。</span><span class="sxs-lookup"><span data-stu-id="a89c8-108">By default EF assumes that for any given context type the model will be the same.</span></span> <span data-ttu-id="a89c8-109">若要完成這項作業的預設實作`IModelCacheKeyFactory`傳回只包含內容類型的金鑰。</span><span class="sxs-lookup"><span data-stu-id="a89c8-109">To accomplish this the default implementation of `IModelCacheKeyFactory` returns a key that just contains the context type.</span></span> <span data-ttu-id="a89c8-110">若要變更您要取代此`IModelCacheKeyFactory`服務。</span><span class="sxs-lookup"><span data-stu-id="a89c8-110">To change this you need to replace the `IModelCacheKeyFactory` service.</span></span> <span data-ttu-id="a89c8-111">新的實作必須傳回使用其他模型索引鍵進行比較的物件`Equals`列入考量會影響模型的所有變數的方法：</span><span class="sxs-lookup"><span data-stu-id="a89c8-111">The new implementation needs to return an object that can be compared to other model keys using the `Equals` method that takes into account all the variables that affect the model:</span></span>

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicModelCacheKeyFactory.cs?name=Class)]
