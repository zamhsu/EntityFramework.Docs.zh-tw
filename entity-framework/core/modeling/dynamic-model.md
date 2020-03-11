---
title: 在具有相同 DbCoNtext 類型的多個模型之間交替-EF Core
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/dynamic-model
ms.openlocfilehash: a160f0d382ee2a3ac7130ce1ac98eb24b3f79394
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416357"
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a>在具有相同 DbCoNtext 類型的多個模型之間交替

內建的模型 `OnModelCreating` 可以在內容上使用屬性來變更模型的建立方式。 例如，假設您想要根據某些屬性，以不同的方式設定實體：

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnModelCreating)]

可惜的是，這段程式碼不會有任何作用，因為 EF 會建立模型，並只執行 `OnModelCreating` 一次，基於效能考慮而快取結果。 不過，您可以連結到模型快取機制，使 EF 知道產生不同模型的屬性。

## <a name="imodelcachekeyfactory"></a>IModelCacheKeyFactory

EF 會使用 `IModelCacheKeyFactory` 來產生模型的快取索引鍵;根據預設，EF 會假設針對任何指定的內容類型，此模型將會相同，因此此服務的預設執行會傳回只包含內容類型的索引鍵。 若要從相同的內容類型產生不同的模型，您必須將 `IModelCacheKeyFactory` 服務取代為正確的執行。產生的索引鍵將會使用 `Equals` 方法與其他模型索引鍵進行比較，並將所有影響模型的變數納入考慮：

下列的執行會在產生模型快取索引鍵時，將 `IgnoreIntProperty` 納入考慮：

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicModelCacheKeyFactory.cs?name=DynamicModel)]

最後，在您的內容 `OnConfiguring`中註冊新的 `IModelCacheKeyFactory`：

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnConfiguring)]

如需詳細內容，請參閱[完整的範例專案](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel)。
