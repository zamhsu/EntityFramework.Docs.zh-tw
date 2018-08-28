---
title: 繼承的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 754be334-dd21-450e-9d22-2591e80012a2
uid: core/modeling/inheritance
ms.openlocfilehash: c5fa9d13dec8cfc3e1cac69e471f509cbbb9e4c5
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995892"
---
# <a name="inheritance"></a><span data-ttu-id="94641-102">繼承</span><span class="sxs-lookup"><span data-stu-id="94641-102">Inheritance</span></span>

<span data-ttu-id="94641-103">EF 模型中的繼承用來控制如何將實體類別中的繼承表示在資料庫中。</span><span class="sxs-lookup"><span data-stu-id="94641-103">Inheritance in the EF model is used to control how inheritance in the entity classes is represented in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="94641-104">慣例</span><span class="sxs-lookup"><span data-stu-id="94641-104">Conventions</span></span>

<span data-ttu-id="94641-105">依照慣例，決定以決定如何將繼承表示在資料庫中的資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="94641-105">By convention, it is up to the database provider to determine how inheritance will be represented in the database.</span></span> <span data-ttu-id="94641-106">請參閱[繼承 （關聯式資料庫）](relational/inheritance.md)針對此與關聯式資料庫提供者的處理方式。</span><span class="sxs-lookup"><span data-stu-id="94641-106">See [Inheritance (Relational Database)](relational/inheritance.md) for how this is handled with a relational database provider.</span></span>

<span data-ttu-id="94641-107">如果模型中明確包含兩個或多個繼承的類型，EF 將只會設定繼承。</span><span class="sxs-lookup"><span data-stu-id="94641-107">EF will only setup inheritance if two or more inherited types are explicitly included in the model.</span></span> <span data-ttu-id="94641-108">EF 會掃描基底或衍生的類型，否則為未包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="94641-108">EF will not scan for base or derived types that were not otherwise included in the model.</span></span> <span data-ttu-id="94641-109">您可以在模型中包含類型，藉由公開*DbSet<TEntity>* 繼承階層架構中每個類型。</span><span class="sxs-lookup"><span data-stu-id="94641-109">You can include types in the model by exposing a *DbSet<TEntity>* for each type in the inheritance hierarchy.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/InheritanceDbSets.cs?highlight=3-4&name=Model)]

<span data-ttu-id="94641-110">如果您不想要公開*DbSet<TEntity>* 對於階層中的一或多個實體，您可以使用 Fluent API，以確保它們會包含在模型。</span><span class="sxs-lookup"><span data-stu-id="94641-110">If you don't want to expose a *DbSet<TEntity>* for one or more entities in the hierarchy, you can use the Fluent API to ensure they are included in the model.</span></span>
<span data-ttu-id="94641-111">如果您不要依賴慣例，您就可以指定使用明確的基底類型和`HasBaseType`。</span><span class="sxs-lookup"><span data-stu-id="94641-111">And if you don't rely on conventions you can specify the base type explicitly using `HasBaseType`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/InheritanceModelBuilder.cs?highlight=7&name=Context)]

> [!NOTE]
> <span data-ttu-id="94641-112">您可以使用`.HasBaseType((Type)null)`若要從階層中移除的實體類型。</span><span class="sxs-lookup"><span data-stu-id="94641-112">You can use `.HasBaseType((Type)null)` to remove an entity type from the hierarchy.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="94641-113">資料註釋</span><span class="sxs-lookup"><span data-stu-id="94641-113">Data Annotations</span></span>

<span data-ttu-id="94641-114">您無法使用資料註解來設定繼承。</span><span class="sxs-lookup"><span data-stu-id="94641-114">You cannot use Data Annotations to configure inheritance.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="94641-115">Fluent API</span><span class="sxs-lookup"><span data-stu-id="94641-115">Fluent API</span></span>

<span data-ttu-id="94641-116">適用於繼承的 Fluent API 取決於您使用的資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="94641-116">The Fluent API for inheritance depends on the database provider you are using.</span></span> <span data-ttu-id="94641-117">請參閱[繼承 （關聯式資料庫）](relational/inheritance.md)關聯式資料庫提供者，您可以執行的組態。</span><span class="sxs-lookup"><span data-stu-id="94641-117">See [Inheritance (Relational Database)](relational/inheritance.md) for the configuration you can perform for a relational database provider.</span></span>
