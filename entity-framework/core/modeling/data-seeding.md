---
title: 資料植入的 EF Core
author: AndriySvyryd
ms.date: 02/23/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/data-seeding
ms.openlocfilehash: 48ba2389de4b57dbe4c2b2124911c71440d45556
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994475"
---
# <a name="data-seeding"></a><span data-ttu-id="4c645-102">資料植入</span><span class="sxs-lookup"><span data-stu-id="4c645-102">Data Seeding</span></span>

> [!NOTE]  
> <span data-ttu-id="4c645-103">這項功能是在 EF Core 2.1 中新功能。</span><span class="sxs-lookup"><span data-stu-id="4c645-103">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="4c645-104">資料植入可讓提供初始資料來填入資料庫。</span><span class="sxs-lookup"><span data-stu-id="4c645-104">Data seeding allows to provide initial data to populate a database.</span></span> <span data-ttu-id="4c645-105">不同於在 EF6，在 EF Core 植入的資料是做為模型設定一部分實體類型相關聯。</span><span class="sxs-lookup"><span data-stu-id="4c645-105">Unlike in EF6, in EF Core, seeding data is associated with an entity type as part of the model configuration.</span></span> <span data-ttu-id="4c645-106">然後 EF Core[移轉](xref:core/managing-schemas/migrations/index)可以自動計算項目插入、 更新或刪除作業必須升級至新版的模型資料庫時套用。</span><span class="sxs-lookup"><span data-stu-id="4c645-106">Then EF Core [migrations](xref:core/managing-schemas/migrations/index) can automatically compute what insert, update or delete operations need to be applied when upgrading the database to a new version of the model.</span></span>

<span data-ttu-id="4c645-107">例如，您可以使用設定的種子資料這`Blog`在`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="4c645-107">As an example, you can use this to configure seed data for a `Blog` in `OnModelCreating`:</span></span>

[!code-csharp[Main](../../../samples/core/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

<span data-ttu-id="4c645-108">若要新增實體有關聯性外部索引鍵值，必須指定。</span><span class="sxs-lookup"><span data-stu-id="4c645-108">To add entities that have a relationship the foreign key values need to be specified.</span></span> <span data-ttu-id="4c645-109">經常外部索引鍵屬性位於陰影狀態，因此為了要能夠將值設定匿名類別應該使用：</span><span class="sxs-lookup"><span data-stu-id="4c645-109">Frequently the foreign key properties are in shadow state, so to be able to set the values an anonymous class should be used:</span></span>

[!code-csharp[Main](../../../samples/core/DataSeeding/DataSeedingContext.cs?name=PostSeed)]

<span data-ttu-id="4c645-110">一旦加入的實體，建議使用[移轉](xref:core/managing-schemas/migrations/index)以套用變更。</span><span class="sxs-lookup"><span data-stu-id="4c645-110">Once entities have been added, it is recommended to use [migrations](xref:core/managing-schemas/migrations/index) to apply changes.</span></span> 

<span data-ttu-id="4c645-111">或者，您可以使用`context.Database.EnsureCreated()`來建立新的資料庫，其中包含的種子資料，例如針對測試資料庫，或使用記憶體中的提供者。</span><span class="sxs-lookup"><span data-stu-id="4c645-111">Alternatively, you can use `context.Database.EnsureCreated()` to create a new database containing the seed data, for example for a test database or when using the in-memory provider.</span></span> <span data-ttu-id="4c645-112">請注意，如果資料庫已經存在，`EnsureCreated()`將不會更新結構描述或資料庫中的種子資料。</span><span class="sxs-lookup"><span data-stu-id="4c645-112">Note that if the database already exists, `EnsureCreated()` will neither update the schema nor the seed data in the database.</span></span>
