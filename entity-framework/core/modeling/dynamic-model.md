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
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a>交替執行多個模型與 DbContext 類型相同

建立模型`OnModelCreating`內容上無法使用的屬性來變更模型的建立方式。 比方說它無法用來排除特定的屬性：

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicContext.cs?name=Class)]

## <a name="imodelcachekeyfactory"></a>IModelCacheKeyFactory
不過如果您嘗試執行上述不需要任何其他變更您會得到相同的模型每次在新的內容建立的任何值`IgnoreIntProperty`。 這因為模型快取的機制來改善效能，藉由只叫用使用 EF`OnModelCreating`一次和快取模型。

依預設 EF 會假設為任何指定的內容類型的模型將會相同。 若要完成這項作業的預設實作`IModelCacheKeyFactory`傳回只包含內容類型的金鑰。 若要變更您要取代此`IModelCacheKeyFactory`服務。 新的實作必須傳回使用其他模型索引鍵進行比較的物件`Equals`列入考量會影響模型的所有變數的方法：

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicModelCacheKeyFactory.cs?name=Class)]
