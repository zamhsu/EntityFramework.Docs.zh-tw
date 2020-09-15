---
title: 管理資料庫結構描述 - EF Core
description: 使用 Entity Framework Core 管理資料庫結構描述的策略概觀
author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/index
ms.openlocfilehash: e4f8c82125534e9e7e0c2de552bce336a544a2aa
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619502"
---
# <a name="managing-database-schemas"></a><span data-ttu-id="11723-103">管理資料庫結構描述</span><span class="sxs-lookup"><span data-stu-id="11723-103">Managing Database Schemas</span></span>

<span data-ttu-id="11723-104">EF Core 提供兩種主要方式來保持 EF Core 模型與資料庫結構描述同步。若要從兩者中進行選擇，請決定 EF Core 模型還是資料庫結構描述為真實來源。</span><span class="sxs-lookup"><span data-stu-id="11723-104">EF Core provides two primary ways of keeping your EF Core model and database schema in sync. To choose between the two, decide whether your EF Core model or the database schema is the source of truth.</span></span>

<span data-ttu-id="11723-105">如果您想要 EF Core 模型成為真實來源，請使用[移轉][1]。</span><span class="sxs-lookup"><span data-stu-id="11723-105">If you want your EF Core model to be the source of truth, use [Migrations][1].</span></span> <span data-ttu-id="11723-106">當您變更 EF Core 模型時，這種方式會以遞增方式將對應的結構描述變更套用至資料庫；因此，它會與 EF Core 模型相容。</span><span class="sxs-lookup"><span data-stu-id="11723-106">As you make changes to your EF Core model, this approach incrementally applies the corresponding schema changes to your database so that it remains compatible with your EF Core model.</span></span>

<span data-ttu-id="11723-107">如果您想要資料庫結構描述成為真實來源，請使用[反向工程][2]。</span><span class="sxs-lookup"><span data-stu-id="11723-107">Use [Reverse Engineering][2] if you want your database schema to be the source of truth.</span></span> <span data-ttu-id="11723-108">此方式可讓您將資料庫結構描述反向工程到 EF Core 模型，以支援 DbContext 和實體類型類別。</span><span class="sxs-lookup"><span data-stu-id="11723-108">This approach allows you to scaffold a DbContext and the entity type classes by reverse engineering your database schema into an EF Core model.</span></span>

> [!NOTE]
> <span data-ttu-id="11723-109">[建立和捨棄 API][3] 也可以從 EF Core 模型建立資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="11723-109">The [create and drop APIs][3] can also create the database schema from your EF Core model.</span></span> <span data-ttu-id="11723-110">不過，它們主要用於測試、原型設計，以及可接受捨棄資料庫的其他案例。</span><span class="sxs-lookup"><span data-stu-id="11723-110">However, they are primarily for testing, prototyping, and other scenarios where dropping the database is acceptable.</span></span>

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/managing-schemas/scaffolding
  [3]: xref:core/managing-schemas/ensure-created
