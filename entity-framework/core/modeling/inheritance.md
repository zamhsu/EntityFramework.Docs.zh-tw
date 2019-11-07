---
title: 繼承-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 754be334-dd21-450e-9d22-2591e80012a2
uid: core/modeling/inheritance
ms.openlocfilehash: abc1caa4d3839b7cdb52b316bcfc8f648b609b70
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655679"
---
# <a name="inheritance"></a><span data-ttu-id="b38ac-102">繼承</span><span class="sxs-lookup"><span data-stu-id="b38ac-102">Inheritance</span></span>

<span data-ttu-id="b38ac-103">EF 模型中的繼承是用來控制實體類別中的繼承在資料庫中的呈現方式。</span><span class="sxs-lookup"><span data-stu-id="b38ac-103">Inheritance in the EF model is used to control how inheritance in the entity classes is represented in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="b38ac-104">慣例</span><span class="sxs-lookup"><span data-stu-id="b38ac-104">Conventions</span></span>

<span data-ttu-id="b38ac-105">依照慣例，由資料庫提供者決定如何在資料庫中表示繼承。</span><span class="sxs-lookup"><span data-stu-id="b38ac-105">By convention, it is up to the database provider to determine how inheritance will be represented in the database.</span></span> <span data-ttu-id="b38ac-106">請參閱[繼承（關係資料庫）](relational/inheritance.md) ，以瞭解如何使用關係資料庫提供者來處理這種情況。</span><span class="sxs-lookup"><span data-stu-id="b38ac-106">See [Inheritance (Relational Database)](relational/inheritance.md) for how this is handled with a relational database provider.</span></span>

<span data-ttu-id="b38ac-107">只有在模型中明確包含兩個或多個繼承類型時，EF 才會設定繼承。</span><span class="sxs-lookup"><span data-stu-id="b38ac-107">EF will only setup inheritance if two or more inherited types are explicitly included in the model.</span></span> <span data-ttu-id="b38ac-108">EF 不會掃描模型中未包含的基底或衍生類型。</span><span class="sxs-lookup"><span data-stu-id="b38ac-108">EF will not scan for base or derived types that were not otherwise included in the model.</span></span> <span data-ttu-id="b38ac-109">您可以藉由在繼承階層架構中的每個型別公開一個*DbSet\<TEntity >* ，來包含模型中的型別。</span><span class="sxs-lookup"><span data-stu-id="b38ac-109">You can include types in the model by exposing a *DbSet\<TEntity>* for each type in the inheritance hierarchy.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?highlight=3-4&name=Model)]

<span data-ttu-id="b38ac-110">如果您不想要針對階層中的一或多個實體公開*DbSet\<TEntity >* ，您可以使用流暢的 API 來確保它們包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="b38ac-110">If you don't want to expose a *DbSet\<TEntity>* for one or more entities in the hierarchy, you can use the Fluent API to ensure they are included in the model.</span></span>
<span data-ttu-id="b38ac-111">如果您不依賴慣例，可以使用 `HasBaseType`明確地指定基底類型。</span><span class="sxs-lookup"><span data-stu-id="b38ac-111">And if you don't rely on conventions, you can specify the base type explicitly using `HasBaseType`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceModelBuilder.cs?highlight=7&name=Context)]

> [!NOTE]
> <span data-ttu-id="b38ac-112">您可以使用 `.HasBaseType((Type)null)` 從階層中移除實體類型。</span><span class="sxs-lookup"><span data-stu-id="b38ac-112">You can use `.HasBaseType((Type)null)` to remove an entity type from the hierarchy.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="b38ac-113">資料註釋</span><span class="sxs-lookup"><span data-stu-id="b38ac-113">Data Annotations</span></span>

<span data-ttu-id="b38ac-114">您不能使用資料批註來設定繼承。</span><span class="sxs-lookup"><span data-stu-id="b38ac-114">You cannot use Data Annotations to configure inheritance.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="b38ac-115">Fluent API</span><span class="sxs-lookup"><span data-stu-id="b38ac-115">Fluent API</span></span>

<span data-ttu-id="b38ac-116">用於繼承的流暢 API 取決於您所使用的資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="b38ac-116">The Fluent API for inheritance depends on the database provider you are using.</span></span> <span data-ttu-id="b38ac-117">如需您可以為關係資料庫提供者執行的設定，請參閱[繼承（關係資料庫）](relational/inheritance.md) 。</span><span class="sxs-lookup"><span data-stu-id="b38ac-117">See [Inheritance (Relational Database)](relational/inheritance.md) for the configuration you can perform for a relational database provider.</span></span>
