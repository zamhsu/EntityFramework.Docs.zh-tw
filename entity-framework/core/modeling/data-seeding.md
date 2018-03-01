---
title: "資料植入的 EF 核心"
author: AndriySvyryd
ms.author: divega
ms.date: 02/23/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
ms.technology: entity-framework-core
uid: core/modeling/data-seeding
ms.openlocfilehash: 693ffe44e247a79e01ac7c98a36472bf2c68d37f
ms.sourcegitcommit: b2d94cebdc32edad4fecb07e53fece66437d1b04
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="data-seeding"></a>植入的資料

> [!NOTE]  
> 這項功能是在 EF 核心 2.1 中新功能。

資料植入可讓提供初始資料來擴展資料庫。 不同於在 EF6，在 EF 核心植入的資料是做為模型設定一部分實體類型相關聯。 然後 EF 核心移轉可以自動計算項目插入、 更新或刪除作業必須升級至新版的模型資料庫時套用。

例如，您可以使用設定的種子資料這`Blog`中`OnModelCreating`:

[!code-csharp[Main](../../../samples/core/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

若要加入實體有關聯性的外部索引鍵值必須指定。 外部索引鍵屬性都常陰影狀態，因此請使用能夠將設定值的匿名類別：

[!code-csharp[Main](../../../samples/core/DataSeeding/DataSeedingContext.cs?name=PostSeed)]
