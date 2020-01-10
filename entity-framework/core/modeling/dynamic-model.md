---
title: 在具有相同 DbCoNtext 類型的多個模型之間交替-EF Core
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/dynamic-model
ms.openlocfilehash: 156d5666cbd9352b274ddc70c99704ca62aeb1fd
ms.sourcegitcommit: 4e86f01740e407ff25e704a11b1f7d7e66bfb2a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2020
ms.locfileid: "75781127"
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a><span data-ttu-id="cddfe-102">在具有相同 DbCoNtext 類型的多個模型之間交替</span><span class="sxs-lookup"><span data-stu-id="cddfe-102">Alternating between multiple models with the same DbContext type</span></span>

<span data-ttu-id="cddfe-103">內建的模型 `OnModelCreating` 可以在內容上使用屬性來變更模型的建立方式。</span><span class="sxs-lookup"><span data-stu-id="cddfe-103">The model built in `OnModelCreating` can use a property on the context to change how the model is built.</span></span> <span data-ttu-id="cddfe-104">例如，假設您想要根據某些屬性，以不同的方式設定實體：</span><span class="sxs-lookup"><span data-stu-id="cddfe-104">For example, suppose you wanted to configure an entity differently based on some property:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnModelCreating)]

<span data-ttu-id="cddfe-105">可惜的是，這段程式碼不會有任何作用，因為 EF 會建立模型，並只執行 `OnModelCreating` 一次，基於效能考慮而快取結果。</span><span class="sxs-lookup"><span data-stu-id="cddfe-105">Unfortunately, this code wouldn't work as-is, since EF builds the model and runs `OnModelCreating` only once, caching the result for performance reasons.</span></span> <span data-ttu-id="cddfe-106">不過，您可以連結到模型快取機制，使 EF 知道產生不同模型的屬性。</span><span class="sxs-lookup"><span data-stu-id="cddfe-106">However, you can hook into the model caching mechanism to make EF aware of the property producing different models.</span></span>

## <a name="imodelcachekeyfactory"></a><span data-ttu-id="cddfe-107">IModelCacheKeyFactory</span><span class="sxs-lookup"><span data-stu-id="cddfe-107">IModelCacheKeyFactory</span></span>

<span data-ttu-id="cddfe-108">EF 會使用 `IModelCacheKeyFactory` 來產生模型的快取索引鍵;根據預設，EF 會假設針對任何指定的內容類型，此模型將會相同，因此此服務的預設執行會傳回只包含內容類型的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="cddfe-108">EF uses the `IModelCacheKeyFactory` to generate cache keys for models; by default, EF assumes that for any given context type the model will be the same, so the default implementation of this service returns a key that just contains the context type.</span></span> <span data-ttu-id="cddfe-109">若要從相同的內容類型產生不同的模型，您必須將 `IModelCacheKeyFactory` 服務取代為正確的執行。產生的索引鍵將會使用 `Equals` 方法與其他模型索引鍵進行比較，並將所有影響模型的變數納入考慮：</span><span class="sxs-lookup"><span data-stu-id="cddfe-109">To produce different models from the same context type, you need to replace the `IModelCacheKeyFactory` service with the correct  implementation; the generated key will be compared to other model keys using the `Equals` method, taking into account all the variables that affect the model:</span></span>

<span data-ttu-id="cddfe-110">下列的執行會在產生模型快取索引鍵時，將 `IgnoreIntProperty` 納入考慮：</span><span class="sxs-lookup"><span data-stu-id="cddfe-110">The following implementation takes the `IgnoreIntProperty` into account when producing a model cache key:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicModelCacheKeyFactory.cs?name=DynamicModel)]

<span data-ttu-id="cddfe-111">最後，在您的內容 `OnConfiguring`中註冊新的 `IModelCacheKeyFactory`：</span><span class="sxs-lookup"><span data-stu-id="cddfe-111">Finally, register your new `IModelCacheKeyFactory` in your context's `OnConfiguring`:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnConfiguring)]

<span data-ttu-id="cddfe-112">如需詳細內容，請參閱[完整的範例專案](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel)。</span><span class="sxs-lookup"><span data-stu-id="cddfe-112">See the [full sample project](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel) for more context.</span></span>
