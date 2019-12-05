---
title: 繼承-EF Core
description: 如何使用 Entity Framework Core 設定實體類型繼承
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 10/27/2016
uid: core/modeling/inheritance
ms.openlocfilehash: 4d43a432174c92ab7f3f9d78a234aefb0a4a17e8
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824671"
---
# <a name="inheritance"></a><span data-ttu-id="d3fe3-103">繼承</span><span class="sxs-lookup"><span data-stu-id="d3fe3-103">Inheritance</span></span>

<span data-ttu-id="d3fe3-104">EF 模型中的繼承是用來控制實體類別中的繼承在資料庫中的呈現方式。</span><span class="sxs-lookup"><span data-stu-id="d3fe3-104">Inheritance in the EF model is used to control how inheritance in the entity classes is represented in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="d3fe3-105">慣例</span><span class="sxs-lookup"><span data-stu-id="d3fe3-105">Conventions</span></span>

<span data-ttu-id="d3fe3-106">根據預設，它是由資料庫提供者決定如何在資料庫中表示繼承。</span><span class="sxs-lookup"><span data-stu-id="d3fe3-106">By default, it is up to the database provider to determine how inheritance will be represented in the database.</span></span> <span data-ttu-id="d3fe3-107">請參閱[繼承（關係資料庫）](relational/inheritance.md) ，以瞭解如何使用關係資料庫提供者來處理這種情況。</span><span class="sxs-lookup"><span data-stu-id="d3fe3-107">See [Inheritance (Relational Database)](relational/inheritance.md) for how this is handled with a relational database provider.</span></span>

<span data-ttu-id="d3fe3-108">只有在模型中明確包含兩個或多個繼承類型時，EF 才會設定繼承。</span><span class="sxs-lookup"><span data-stu-id="d3fe3-108">EF will only setup inheritance if two or more inherited types are explicitly included in the model.</span></span> <span data-ttu-id="d3fe3-109">EF 不會掃描模型中未包含的基底或衍生類型。</span><span class="sxs-lookup"><span data-stu-id="d3fe3-109">EF will not scan for base or derived types that were not otherwise included in the model.</span></span> <span data-ttu-id="d3fe3-110">您可以藉由在繼承階層架構中的每個型別公開一個*DbSet\<TEntity >* ，來包含模型中的型別。</span><span class="sxs-lookup"><span data-stu-id="d3fe3-110">You can include types in the model by exposing a *DbSet\<TEntity>* for each type in the inheritance hierarchy.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?highlight=3-4&name=Model)]

<span data-ttu-id="d3fe3-111">如果您不想要針對階層中的一或多個實體公開*DbSet\<TEntity >* ，您可以使用流暢的 API 來確保它們包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="d3fe3-111">If you don't want to expose a *DbSet\<TEntity>* for one or more entities in the hierarchy, you can use the Fluent API to ensure they are included in the model.</span></span>
<span data-ttu-id="d3fe3-112">如果您不依賴慣例，可以使用 `HasBaseType`明確地指定基底類型。</span><span class="sxs-lookup"><span data-stu-id="d3fe3-112">And if you don't rely on conventions, you can specify the base type explicitly using `HasBaseType`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceModelBuilder.cs?highlight=7&name=Context)]

> [!NOTE]
> <span data-ttu-id="d3fe3-113">您可以使用 `.HasBaseType((Type)null)` 從階層中移除實體類型。</span><span class="sxs-lookup"><span data-stu-id="d3fe3-113">You can use `.HasBaseType((Type)null)` to remove an entity type from the hierarchy.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="d3fe3-114">資料註釋</span><span class="sxs-lookup"><span data-stu-id="d3fe3-114">Data Annotations</span></span>

<span data-ttu-id="d3fe3-115">您不能使用資料批註來設定繼承。</span><span class="sxs-lookup"><span data-stu-id="d3fe3-115">You cannot use Data Annotations to configure inheritance.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="d3fe3-116">Fluent API</span><span class="sxs-lookup"><span data-stu-id="d3fe3-116">Fluent API</span></span>

<span data-ttu-id="d3fe3-117">用於繼承的流暢 API 取決於您所使用的資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="d3fe3-117">The Fluent API for inheritance depends on the database provider you are using.</span></span> <span data-ttu-id="d3fe3-118">如需您可以為關係資料庫提供者執行的設定，請參閱[繼承（關係資料庫）](relational/inheritance.md) 。</span><span class="sxs-lookup"><span data-stu-id="d3fe3-118">See [Inheritance (Relational Database)](relational/inheritance.md) for the configuration you can perform for a relational database provider.</span></span>
