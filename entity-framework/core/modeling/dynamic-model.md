---
title: 在具有相同 DbCoNtext 類型的多個模型之間交替-EF Core
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/dynamic-model
ms.openlocfilehash: 35743b5ba87bb4239d7f758320f9facccc74330f
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664217"
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a><span data-ttu-id="71a02-102">在具有相同 DbCoNtext 類型的多個模型之間交替</span><span class="sxs-lookup"><span data-stu-id="71a02-102">Alternating between multiple models with the same DbContext type</span></span>

<span data-ttu-id="71a02-103">內建的模型 `OnModelCreating` 可以在內容上使用屬性，以變更模型的建立方式。</span><span class="sxs-lookup"><span data-stu-id="71a02-103">The model built in `OnModelCreating` can use a property on the context to change how the model is built.</span></span> <span data-ttu-id="71a02-104">例如，假設您想要根據某些屬性，以不同的方式設定實體：</span><span class="sxs-lookup"><span data-stu-id="71a02-104">For example, suppose you wanted to configure an entity differently based on some property:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnModelCreating)]

<span data-ttu-id="71a02-105">可惜的是，這段程式碼不會有任何作用，因為 EF 會建立模型並 `OnModelCreating` 只執行一次，基於效能考慮而快取結果。</span><span class="sxs-lookup"><span data-stu-id="71a02-105">Unfortunately, this code wouldn't work as-is, since EF builds the model and runs `OnModelCreating` only once, caching the result for performance reasons.</span></span> <span data-ttu-id="71a02-106">不過，您可以連結到模型快取機制，使 EF 知道產生不同模型的屬性。</span><span class="sxs-lookup"><span data-stu-id="71a02-106">However, you can hook into the model caching mechanism to make EF aware of the property producing different models.</span></span>

## <a name="imodelcachekeyfactory"></a><span data-ttu-id="71a02-107">IModelCacheKeyFactory</span><span class="sxs-lookup"><span data-stu-id="71a02-107">IModelCacheKeyFactory</span></span>

<span data-ttu-id="71a02-108">EF 會使用 `IModelCacheKeyFactory` 來產生模型的快取索引鍵; 根據預設，ef 會假設針對任何指定的內容類型，此模型將會相同，因此此服務的預設執行會傳回只包含內容類型的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="71a02-108">EF uses the `IModelCacheKeyFactory` to generate cache keys for models; by default, EF assumes that for any given context type the model will be the same, so the default implementation of this service returns a key that just contains the context type.</span></span> <span data-ttu-id="71a02-109">若要從相同的內容類型產生不同的模型，您需要以 `IModelCacheKeyFactory` 正確的實值來取代服務; 產生的索引鍵將會使用方法與其他模型索引鍵進行比較，並考慮 `Equals` 所有會影響模型的變數。</span><span class="sxs-lookup"><span data-stu-id="71a02-109">To produce different models from the same context type, you need to replace the `IModelCacheKeyFactory` service with the correct implementation; the generated key will be compared to other model keys using the `Equals` method, taking into account all the variables that affect the model.</span></span>

<span data-ttu-id="71a02-110">產生模型快取索引鍵時，下列執行會將 `UseIntProperty` 納入考慮：</span><span class="sxs-lookup"><span data-stu-id="71a02-110">The following implementation takes the `UseIntProperty` into account when producing a model cache key:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicModelCacheKeyFactory.cs?name=DynamicModel)]

<span data-ttu-id="71a02-111">最後，在您的內容中註冊您的新 `IModelCacheKeyFactory` `OnConfiguring` ：</span><span class="sxs-lookup"><span data-stu-id="71a02-111">Finally, register your new `IModelCacheKeyFactory` in your context's `OnConfiguring`:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnConfiguring)]

<span data-ttu-id="71a02-112">如需詳細內容，請參閱[完整的範例專案](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel)。</span><span class="sxs-lookup"><span data-stu-id="71a02-112">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel) for more context.</span></span>
