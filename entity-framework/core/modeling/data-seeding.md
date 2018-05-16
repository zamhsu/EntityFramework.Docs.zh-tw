---
title: 資料植入的 EF 核心
author: AndriySvyryd
ms.author: divega
ms.date: 02/23/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
ms.technology: entity-framework-core
uid: core/modeling/data-seeding
ms.openlocfilehash: 7028e1923152b27f56721dab75aae8b9c2f5ad75
ms.sourcegitcommit: 038acd91ce2f5a28d76dcd2eab72eeba225e366d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2018
---
# <a name="data-seeding"></a>資料植入

> [!NOTE]  
> 這項功能是在 EF 核心 2.1 中新功能。

資料植入可讓提供初始資料來擴展資料庫。 不同於在 EF6，在 EF 核心植入的資料是做為模型設定一部分實體類型相關聯。 然後 EF 核心[移轉](xref:core/managing-schemas/migrations/index)可以自動計算項目插入、 更新或刪除作業必須升級至新版的模型資料庫時套用。

例如，您可以使用設定的種子資料這`Blog`中`OnModelCreating`:

[!code-csharp[Main](../../../samples/core/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

若要加入實體有關聯性的外部索引鍵值必須指定。 外部索引鍵屬性都常陰影狀態，因此請使用能夠將設定值的匿名類別：

[!code-csharp[Main](../../../samples/core/DataSeeding/DataSeedingContext.cs?name=PostSeed)]

一旦加入的實體，建議使用[移轉](xref:core/managing-schemas/migrations/index)套用變更。 

或者，您可以使用`context.Database.EnsureCreated()`以建立新的資料庫包含的種子資料，例如針對測試資料庫，或使用記憶體中的提供者。 請注意，如果資料庫已經存在，`EnsureCreated()`將兩者都不會更新結構描述或資料庫中的種子資料。
