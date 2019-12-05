---
title: 繼承（關係資料庫）-EF Core
description: 如何使用 Entity Framework Core 在關係資料庫中設定實體類型繼承
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/06/2019
uid: core/modeling/relational/inheritance
ms.openlocfilehash: 30e25aa2968ceab03404baddb46e0ae59fc3ea6b
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824751"
---
# <a name="inheritance-relational-database"></a><span data-ttu-id="66afd-103">繼承 (關聯式資料庫)</span><span class="sxs-lookup"><span data-stu-id="66afd-103">Inheritance (Relational Database)</span></span>

> [!NOTE]  
> <span data-ttu-id="66afd-104">本節中的組態一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="66afd-104">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="66afd-105">當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。</span><span class="sxs-lookup"><span data-stu-id="66afd-105">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="66afd-106">EF 模型中的繼承是用來控制實體類別中的繼承在資料庫中的呈現方式。</span><span class="sxs-lookup"><span data-stu-id="66afd-106">Inheritance in the EF model is used to control how inheritance in the entity classes is represented in the database.</span></span>

> [!NOTE]  
> <span data-ttu-id="66afd-107">目前，只有每個階層的資料表（TPH）模式會在 EF Core 中執行。</span><span class="sxs-lookup"><span data-stu-id="66afd-107">Currently, only the table-per-hierarchy (TPH) pattern is implemented in EF Core.</span></span> <span data-ttu-id="66afd-108">其他常見的模式（例如，每個類型的資料表（TPT）和每個具體的資料表類型（TPC））尚無法使用。</span><span class="sxs-lookup"><span data-stu-id="66afd-108">Other common patterns like table-per-type (TPT) and table-per-concrete-type (TPC) are not yet available.</span></span>

## <a name="conventions"></a><span data-ttu-id="66afd-109">慣例</span><span class="sxs-lookup"><span data-stu-id="66afd-109">Conventions</span></span>

<span data-ttu-id="66afd-110">根據預設，系統會使用每個階層的資料表（TPH）模式來對應繼承。</span><span class="sxs-lookup"><span data-stu-id="66afd-110">By default, inheritance will be mapped using the table-per-hierarchy (TPH) pattern.</span></span> <span data-ttu-id="66afd-111">TPH 會使用單一資料表來儲存階層中所有類型的資料。</span><span class="sxs-lookup"><span data-stu-id="66afd-111">TPH uses a single table to store the data for all types in the hierarchy.</span></span> <span data-ttu-id="66afd-112">鑒別子資料行是用來識別每個資料列所代表的類型。</span><span class="sxs-lookup"><span data-stu-id="66afd-112">A discriminator column is used to identify which type each row represents.</span></span>

<span data-ttu-id="66afd-113">只有在模型中明確包含兩個或多個繼承類型時，EF Core 才會設定繼承（如需詳細資訊，請參閱[繼承](../inheritance.md)）。</span><span class="sxs-lookup"><span data-stu-id="66afd-113">EF Core will only setup inheritance if two or more inherited types are explicitly included in the model (see [Inheritance](../inheritance.md) for more details).</span></span>

<span data-ttu-id="66afd-114">以下範例顯示簡單的繼承案例，以及使用 TPH 模式儲存在關係資料庫資料表中的資料。</span><span class="sxs-lookup"><span data-stu-id="66afd-114">Below is an example showing a simple inheritance scenario and the data stored in a relational database table using the TPH pattern.</span></span> <span data-ttu-id="66afd-115">*鑒別*子資料行會識別每個資料列中所儲存的*Blog*類型。</span><span class="sxs-lookup"><span data-stu-id="66afd-115">The *Discriminator* column identifies which type of *Blog* is stored in each row.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs#Model)]

![影像](_static/inheritance-tph-data.png)

>[!NOTE]
> <span data-ttu-id="66afd-117">使用 TPH 對應時，視需要自動將資料庫資料行設為可為 null。</span><span class="sxs-lookup"><span data-stu-id="66afd-117">Database columns are automatically made nullable as necessary when using TPH mapping.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="66afd-118">資料註釋</span><span class="sxs-lookup"><span data-stu-id="66afd-118">Data Annotations</span></span>

<span data-ttu-id="66afd-119">您不能使用資料批註來設定繼承。</span><span class="sxs-lookup"><span data-stu-id="66afd-119">You cannot use Data Annotations to configure inheritance.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="66afd-120">Fluent API</span><span class="sxs-lookup"><span data-stu-id="66afd-120">Fluent API</span></span>

<span data-ttu-id="66afd-121">您可以使用流暢的 API 來設定鑒別子資料行的名稱和類型，以及用來識別階層中每個類型的值。</span><span class="sxs-lookup"><span data-stu-id="66afd-121">You can use the Fluent API to configure the name and type of the discriminator column and the values that are used to identify each type in the hierarchy.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/InheritanceTPHDiscriminator.cs#Inheritance)]

## <a name="configuring-the-discriminator-property"></a><span data-ttu-id="66afd-122">設定鑒別子屬性</span><span class="sxs-lookup"><span data-stu-id="66afd-122">Configuring the discriminator property</span></span>

<span data-ttu-id="66afd-123">在上述範例中，會在階層的基底實體上建立鑒別子做為[陰影屬性](xref:core/modeling/shadow-properties)。</span><span class="sxs-lookup"><span data-stu-id="66afd-123">In the examples above, the discriminator is created as a [shadow property](xref:core/modeling/shadow-properties) on the base entity of the hierarchy.</span></span> <span data-ttu-id="66afd-124">由於它是模型中的屬性，因此可以像其他屬性一樣進行設定。</span><span class="sxs-lookup"><span data-stu-id="66afd-124">Since it is a property in the model, it can be configured just like other properties.</span></span> <span data-ttu-id="66afd-125">例如，若要設定預設值時使用的最大長度（依慣例鑒別子）：</span><span class="sxs-lookup"><span data-stu-id="66afd-125">For example, to set the max length when the default, by-convention discriminator is being used:</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/DefaultDiscriminator.cs#DiscriminatorConfiguration)]

<span data-ttu-id="66afd-126">鑒別子也可以對應至實體中的 .NET 屬性，並加以設定。</span><span class="sxs-lookup"><span data-stu-id="66afd-126">The discriminator can also be mapped to a .NET property in your entity and configure it.</span></span> <span data-ttu-id="66afd-127">例如：</span><span class="sxs-lookup"><span data-stu-id="66afd-127">For example:</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs#NonShadowDiscriminator)]

## <a name="shared-columns"></a><span data-ttu-id="66afd-128">共用資料行</span><span class="sxs-lookup"><span data-stu-id="66afd-128">Shared columns</span></span>

<span data-ttu-id="66afd-129">當兩個同級實體類型具有相同名稱的屬性時，預設會將它們對應至兩個不同的資料行。</span><span class="sxs-lookup"><span data-stu-id="66afd-129">When two sibling entity types have a property with the same name they will be mapped to two separate columns by default.</span></span> <span data-ttu-id="66afd-130">但是，如果它們相容，則可以對應到相同的資料行：</span><span class="sxs-lookup"><span data-stu-id="66afd-130">But if they are compatible they can be mapped to the same column:</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs#SharedTPHColumns)]