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
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a>在具有相同 DbCoNtext 類型的多個模型之間交替

內建的模型 `OnModelCreating` 可以在內容上使用屬性來變更模型的建立方式。 例如，它可以用來排除特定的屬性：

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicContext.cs?name=Class)]

## <a name="imodelcachekeyfactory"></a>IModelCacheKeyFactory

不過，如果您嘗試執行上述動作而沒有其他變更，則每次為 `IgnoreIntProperty`的任何值建立新內容時，都會取得相同的模型。 這是由 EF 用來改善效能的模型快取機制所造成，只會叫用 `OnModelCreating` 一次並快取模型。

根據預設，EF 會假設針對任何指定的內容類型，此模型會是相同的。 若要完成這項工作，`IModelCacheKeyFactory` 的預設執行會傳回只包含內容類型的索引鍵。 若要變更這種情況，您必須取代 `IModelCacheKeyFactory` 服務。 新的執行必須傳回可與其他模型索引鍵比較的物件，其使用的 `Equals` 方法會將所有影響模型的變數納入考慮：

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicModelCacheKeyFactory.cs?name=Class)]
