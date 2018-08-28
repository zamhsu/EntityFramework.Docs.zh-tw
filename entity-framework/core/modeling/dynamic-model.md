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
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a>具有相同 DbContext 類型的多個模型之間交替

內建模型`OnModelCreating`內容無法使用的屬性來變更模型的建置方式。 比方說它無法用來排除特定的屬性：

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicContext.cs?name=Class)]

## <a name="imodelcachekeyfactory"></a>IModelCacheKeyFactory
不過如果您嘗試這樣做而不需要額外的變更會得到相同的模型每次在新的內容建立的任何值`IgnoreIntProperty`。 這種情形因模型快取以改善效能，藉由只叫用 EF 所使用的機制`OnModelCreating`一次，快取模型。

根據預設 EF 會假設為任何指定的內容類型的模型將會相同。 若要這麼做的預設實作`IModelCacheKeyFactory`傳回只包含內容類型的金鑰。 若要變更此您需要更換`IModelCacheKeyFactory`服務。 新的實作必須使用其他模型索引鍵傳回物件可比較`Equals`會考量會影響模型的所有變數的方法：

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicModelCacheKeyFactory.cs?name=Class)]
