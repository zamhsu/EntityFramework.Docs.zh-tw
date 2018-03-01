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
# <a name="data-seeding"></a><span data-ttu-id="d07a1-102">植入的資料</span><span class="sxs-lookup"><span data-stu-id="d07a1-102">Data Seeding</span></span>

> [!NOTE]  
> <span data-ttu-id="d07a1-103">這項功能是在 EF 核心 2.1 中新功能。</span><span class="sxs-lookup"><span data-stu-id="d07a1-103">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="d07a1-104">資料植入可讓提供初始資料來擴展資料庫。</span><span class="sxs-lookup"><span data-stu-id="d07a1-104">Data seeding allows to provide initial data to populate a database.</span></span> <span data-ttu-id="d07a1-105">不同於在 EF6，在 EF 核心植入的資料是做為模型設定一部分實體類型相關聯。</span><span class="sxs-lookup"><span data-stu-id="d07a1-105">Unlike in EF6, in EF Core, seeding data is associated with an entity type as part of the model configuration.</span></span> <span data-ttu-id="d07a1-106">然後 EF 核心移轉可以自動計算項目插入、 更新或刪除作業必須升級至新版的模型資料庫時套用。</span><span class="sxs-lookup"><span data-stu-id="d07a1-106">Then EF Core migrations can automatically compute what insert, update or delete operations need to be applied when upgrading the database to a new version of the model.</span></span>

<span data-ttu-id="d07a1-107">例如，您可以使用設定的種子資料這`Blog`中`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="d07a1-107">As an example, you can use this to configure seed data for a `Blog` in `OnModelCreating`:</span></span>

[!code-csharp[Main](../../../samples/core/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

<span data-ttu-id="d07a1-108">若要加入實體有關聯性的外部索引鍵值必須指定。</span><span class="sxs-lookup"><span data-stu-id="d07a1-108">To add entities that have a relationship the foreign key values need to be specified.</span></span> <span data-ttu-id="d07a1-109">外部索引鍵屬性都常陰影狀態，因此請使用能夠將設定值的匿名類別：</span><span class="sxs-lookup"><span data-stu-id="d07a1-109">Frequently the foreign key properties are in shadow state, so to be able to set the values an anonymous class should be used:</span></span>

[!code-csharp[Main](../../../samples/core/DataSeeding/DataSeedingContext.cs?name=PostSeed)]
