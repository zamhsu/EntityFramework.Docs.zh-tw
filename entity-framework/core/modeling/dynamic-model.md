---
title: 在具有相同 DbCoNtext 類型的多個模型之間交替 EF Core
description: 使用 Entity Framework Core，在具有相同 DbCoNtext 類型的多個模型之間交替
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/dynamic-model
ms.openlocfilehash: 436cb46e002438f391c654b64efdfacf494d580d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617524"
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a><span data-ttu-id="07280-103">在具有相同 DbCoNtext 類型的多個模型之間交替</span><span class="sxs-lookup"><span data-stu-id="07280-103">Alternating between multiple models with the same DbContext type</span></span>

<span data-ttu-id="07280-104">內建的模型 `OnModelCreating` 可以使用內容上的屬性來變更模型的建立方式。</span><span class="sxs-lookup"><span data-stu-id="07280-104">The model built in `OnModelCreating` can use a property on the context to change how the model is built.</span></span> <span data-ttu-id="07280-105">例如，假設您想要根據某些屬性，以不同的方式設定實體：</span><span class="sxs-lookup"><span data-stu-id="07280-105">For example, suppose you wanted to configure an entity differently based on some property:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnModelCreating)]

<span data-ttu-id="07280-106">可惜的是，此程式碼不會像一樣運作，因為 EF 只會建立模型並執行 `OnModelCreating` 一次，因此基於效能考慮而快取結果。</span><span class="sxs-lookup"><span data-stu-id="07280-106">Unfortunately, this code wouldn't work as-is, since EF builds the model and runs `OnModelCreating` only once, caching the result for performance reasons.</span></span> <span data-ttu-id="07280-107">不過，您可以連接到模型快取機制，讓 EF 知道屬性產生不同的模型。</span><span class="sxs-lookup"><span data-stu-id="07280-107">However, you can hook into the model caching mechanism to make EF aware of the property producing different models.</span></span>

## <a name="imodelcachekeyfactory"></a><span data-ttu-id="07280-108">IModelCacheKeyFactory</span><span class="sxs-lookup"><span data-stu-id="07280-108">IModelCacheKeyFactory</span></span>

<span data-ttu-id="07280-109">EF 使用 `IModelCacheKeyFactory` 來產生模型的快取索引鍵; 根據預設，ef 會假設針對任何指定的內容類型，模型會是相同的，因此此服務的預設實值會傳回只包含內容類型的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="07280-109">EF uses the `IModelCacheKeyFactory` to generate cache keys for models; by default, EF assumes that for any given context type the model will be the same, so the default implementation of this service returns a key that just contains the context type.</span></span> <span data-ttu-id="07280-110">若要從相同的內容類型產生不同的模型，您必須使用 `IModelCacheKeyFactory` 正確的實值來取代服務; 產生的索引鍵會與其他使用方法的模型索引鍵進行比較，並將 `Equals` 所有影響模型的變數納入考慮。</span><span class="sxs-lookup"><span data-stu-id="07280-110">To produce different models from the same context type, you need to replace the `IModelCacheKeyFactory` service with the correct implementation; the generated key will be compared to other model keys using the `Equals` method, taking into account all the variables that affect the model.</span></span>

<span data-ttu-id="07280-111">在產生模型快取索引鍵時，下列實作為考慮 `UseIntProperty` ：</span><span class="sxs-lookup"><span data-stu-id="07280-111">The following implementation takes the `UseIntProperty` into account when producing a model cache key:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicModelCacheKeyFactory.cs?name=DynamicModel)]

<span data-ttu-id="07280-112">最後， `IModelCacheKeyFactory` 在您的內容中註冊新的 `OnConfiguring` ：</span><span class="sxs-lookup"><span data-stu-id="07280-112">Finally, register your new `IModelCacheKeyFactory` in your context's `OnConfiguring`:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnConfiguring)]

<span data-ttu-id="07280-113">請參閱 [完整的範例專案](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel) ，以取得更多內容。</span><span class="sxs-lookup"><span data-stu-id="07280-113">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel) for more context.</span></span>
