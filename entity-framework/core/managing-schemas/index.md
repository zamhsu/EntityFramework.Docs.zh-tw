---
title: 管理資料庫結構描述 - EF Core
author: bricelam
ms.date: 10/30/2017
ms.openlocfilehash: c1ebe33b5575cab76a54721ef86ecbcb7ff8b98b
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994381"
---
# <a name="managing-database-schemas"></a><span data-ttu-id="73c5a-102">管理資料庫結構描述</span><span class="sxs-lookup"><span data-stu-id="73c5a-102">Managing Database Schemas</span></span>
<span data-ttu-id="73c5a-103">EF Core 提供兩種主要方式來保持 EF Core 模型與資料庫結構描述同步。若要從兩者中進行選擇，請決定 EF Core 模型還是資料庫結構描述為真實來源。</span><span class="sxs-lookup"><span data-stu-id="73c5a-103">EF Core provides two primary ways of keeping your EF Core model and database schema in sync. To choose between the two, decide whether your EF Core model or the database schema is the source of truth.</span></span>

<span data-ttu-id="73c5a-104">如果您想要 EF Core 模型成為真實來源，請使用[移轉][1]。</span><span class="sxs-lookup"><span data-stu-id="73c5a-104">If you want your EF Core model to be the source of truth, use [Migrations][1].</span></span> <span data-ttu-id="73c5a-105">當您變更 EF Core 模型時，這種方式會以遞增方式將對應的結構描述變更套用至資料庫；因此，它會與 EF Core 模型相容。</span><span class="sxs-lookup"><span data-stu-id="73c5a-105">As you make changes to your EF Core model, this approach incrementally applies the corresponding schema changes to your database so that it remains compatible with your EF Core model.</span></span>

<span data-ttu-id="73c5a-106">如果您想要資料庫結構描述成為真實來源，請使用[反向工程][2]。</span><span class="sxs-lookup"><span data-stu-id="73c5a-106">Use [Reverse Engineering][2] if you want your database schema to be the source of truth.</span></span> <span data-ttu-id="73c5a-107">此方式可讓您將資料庫結構描述反向工程到 EF Core 模型，以支援 DbContext 和實體類型類別。</span><span class="sxs-lookup"><span data-stu-id="73c5a-107">This approach allows you to scaffold a DbContext and the entity type classes by reverse engineering your database schema into an EF Core model.</span></span>

> [!NOTE]
> <span data-ttu-id="73c5a-108">[建立和捨棄 API][3] 也可以從 EF Core 模型建立資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="73c5a-108">The [create and drop APIs][3] can also create the database schema from your EF Core model.</span></span> <span data-ttu-id="73c5a-109">不過，它們主要用於測試、原型設計，以及可接受捨棄資料庫的其他案例。</span><span class="sxs-lookup"><span data-stu-id="73c5a-109">However, they are primarily for testing, prototyping, and other scenarios where dropping the database is acceptable.</span></span>


  [1]: migrations/index.md
  [2]: scaffolding.md
  [3]: ensure-created.md
