---
title: 外鍵條件約束-EF Core
author: AndriySvyryd
ms.date: 11/21/2019
uid: core/modeling/relational/fk-constraints
ms.openlocfilehash: 2855137adf2ba3c9edaabd15a05f7a209f00f685
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824586"
---
# <a name="foreign-key-constraints"></a><span data-ttu-id="fb82d-102">外部索引鍵條件約束</span><span class="sxs-lookup"><span data-stu-id="fb82d-102">Foreign Key Constraints</span></span>

> [!NOTE]  
> <span data-ttu-id="fb82d-103">本節中的組態一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="fb82d-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="fb82d-104">當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。</span><span class="sxs-lookup"><span data-stu-id="fb82d-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="fb82d-105">針對模型中的每個關聯性引進外鍵條件約束。</span><span class="sxs-lookup"><span data-stu-id="fb82d-105">A foreign key constraint is introduced for each relationship in the model.</span></span>

## <a name="conventions"></a><span data-ttu-id="fb82d-106">慣例</span><span class="sxs-lookup"><span data-stu-id="fb82d-106">Conventions</span></span>

<span data-ttu-id="fb82d-107">依照慣例，外鍵條件約束會命名為 `FK_<dependent type name>_<principal type name>_<foreign key property name>`。</span><span class="sxs-lookup"><span data-stu-id="fb82d-107">By convention, foreign key constraints are named `FK_<dependent type name>_<principal type name>_<foreign key property name>`.</span></span> <span data-ttu-id="fb82d-108">對於複合外鍵 `<foreign key property name>` 會變成以底線分隔的外鍵屬性名稱清單。</span><span class="sxs-lookup"><span data-stu-id="fb82d-108">For composite foreign keys `<foreign key property name>` becomes an underscore separated list of foreign key property names.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="fb82d-109">資料註釋</span><span class="sxs-lookup"><span data-stu-id="fb82d-109">Data Annotations</span></span>

<span data-ttu-id="fb82d-110">外鍵條件約束名稱無法使用資料批註來設定。</span><span class="sxs-lookup"><span data-stu-id="fb82d-110">Foreign key constraint names cannot be configured using data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="fb82d-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="fb82d-111">Fluent API</span></span>

<span data-ttu-id="fb82d-112">您可以使用流暢的 API 來設定關聯性的外鍵條件約束名稱。</span><span class="sxs-lookup"><span data-stu-id="fb82d-112">You can use the Fluent API to configure the foreign key constraint name for a relationship.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/RelationshipConstraintName.cs?name=Constraint&highlight=12)]
