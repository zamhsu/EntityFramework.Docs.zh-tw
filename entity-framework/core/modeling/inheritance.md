---
title: "繼承的 EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 754be334-dd21-450e-9d22-2591e80012a2
ms.technology: entity-framework-core
uid: core/modeling/inheritance
ms.openlocfilehash: f0394bc55dfbfea8277b1ddf898361165dd1fe51
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="inheritance"></a><span data-ttu-id="14598-102">繼承</span><span class="sxs-lookup"><span data-stu-id="14598-102">Inheritance</span></span>

<span data-ttu-id="14598-103">EF 模型中的繼承用來控制如何在實體類別中的繼承表示在資料庫中。</span><span class="sxs-lookup"><span data-stu-id="14598-103">Inheritance in the EF model is used to control how inheritance in the entity classes is represented in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="14598-104">慣例</span><span class="sxs-lookup"><span data-stu-id="14598-104">Conventions</span></span>

<span data-ttu-id="14598-105">依照慣例，會決定資料庫提供者，以判斷繼承在資料庫中的呈現方式。</span><span class="sxs-lookup"><span data-stu-id="14598-105">By convention, it is up to the database provider to determine how inheritance will be represented in the database.</span></span> <span data-ttu-id="14598-106">請參閱[繼承 （關聯式資料庫）](relational/inheritance.md)如這與關聯式資料庫提供者的處理方式。</span><span class="sxs-lookup"><span data-stu-id="14598-106">See [Inheritance (Relational Database)](relational/inheritance.md) for how this is handled with a relational database provider.</span></span>

<span data-ttu-id="14598-107">如果兩個或多個繼承的型別會明確地包含在模型中，EF 只會設定繼承。</span><span class="sxs-lookup"><span data-stu-id="14598-107">EF will only setup inheritance if two or more inherited types are explicitly included in the model.</span></span> <span data-ttu-id="14598-108">EF 不會掃描基底或衍生的類型，否則不包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="14598-108">EF will not scan for base or derived types that were not otherwise included in the model.</span></span> <span data-ttu-id="14598-109">您可以在模型中加入類型，藉由公開*DbSet<TEntity>* 繼承階層中每個類型。</span><span class="sxs-lookup"><span data-stu-id="14598-109">You can include types in the model by exposing a *DbSet<TEntity>* for each type in the inheritance hierarchy.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/InheritanceDbSets.cs?highlight=3-4&name=Model)]

<span data-ttu-id="14598-110">如果您不想要公開*DbSet<TEntity>* 對於階層中的一個或多個實體，您可以使用 fluent 應用程式開發的應用程式開發介面，以確保它們會包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="14598-110">If you don't want to expose a *DbSet<TEntity>* for one or more entities in the hierarchy, you can use the Fluent API to ensure they are included in the model.</span></span>
<span data-ttu-id="14598-111">如果您不要依賴慣例，您可以指定使用明確的基底類型和`HasBaseType`。</span><span class="sxs-lookup"><span data-stu-id="14598-111">And if you don't rely on conventions you can specify the base type explicitly using `HasBaseType`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/InheritanceModelBuilder.cs?highlight=7&name=Context)]

> [!NOTE]
> <span data-ttu-id="14598-112">您可以使用`.HasBaseType((Type)null)`若要從階層中移除實體類型。</span><span class="sxs-lookup"><span data-stu-id="14598-112">You can use `.HasBaseType((Type)null)` to remove an entity type from the hierarchy.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="14598-113">資料註釋</span><span class="sxs-lookup"><span data-stu-id="14598-113">Data Annotations</span></span>

<span data-ttu-id="14598-114">若要設定繼承，您無法使用資料註解。</span><span class="sxs-lookup"><span data-stu-id="14598-114">You cannot use Data Annotations to configure inheritance.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="14598-115">關於 fluent 應用程式開發介面</span><span class="sxs-lookup"><span data-stu-id="14598-115">Fluent API</span></span>

<span data-ttu-id="14598-116">繼承 Fluent API 取決於您使用的資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="14598-116">The Fluent API for inheritance depends on the database provider you are using.</span></span> <span data-ttu-id="14598-117">請參閱[繼承 （關聯式資料庫）](relational/inheritance.md)關聯式資料庫提供者，您可以執行的組態。</span><span class="sxs-lookup"><span data-stu-id="14598-117">See [Inheritance (Relational Database)](relational/inheritance.md) for the configuration you can perform for a relational database provider.</span></span>
