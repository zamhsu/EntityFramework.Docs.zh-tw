---
title: 繼承-EF Core
description: 如何使用 Entity Framework Core 設定實體類型繼承
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 10/27/2016
uid: core/modeling/inheritance
ms.openlocfilehash: 507854e3acc0347adee612e516b3e2e0b10f55cf
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417292"
---
# <a name="inheritance"></a><span data-ttu-id="80e9c-103">繼承</span><span class="sxs-lookup"><span data-stu-id="80e9c-103">Inheritance</span></span>

<span data-ttu-id="80e9c-104">EF 可以將 .NET 類型階層架構對應到資料庫。</span><span class="sxs-lookup"><span data-stu-id="80e9c-104">EF can map a .NET type hierarchy to a database.</span></span> <span data-ttu-id="80e9c-105">這可讓您以平常的程式碼撰寫 .NET 實體，使用基底和衍生類型，並讓 EF 順暢地建立適當的資料庫架構、發出查詢等。類型階層的對應實際詳細資料與提供者相關。此頁面描述關係資料庫內容中的繼承支援。</span><span class="sxs-lookup"><span data-stu-id="80e9c-105">This allows you to write your .NET entities in code as usual, using base and derived types, and have EF seamlessly create the appropriate database schema, issue queries, etc. The actual details of how a type hierarchy is mapped are provider-dependent; this page describes inheritance support in the context of a relational database.</span></span>

<span data-ttu-id="80e9c-106">目前，EF Core 只支援每個階層的資料表（TPH）模式。</span><span class="sxs-lookup"><span data-stu-id="80e9c-106">At the moment, EF Core only supports the table-per-hierarchy (TPH) pattern.</span></span> <span data-ttu-id="80e9c-107">TPH 會使用單一資料表來儲存階層中所有類型的資料，而且會使用鑒別子資料行來識別每個資料列所代表的類型。</span><span class="sxs-lookup"><span data-stu-id="80e9c-107">TPH uses a single table to store the data for all types in the hierarchy, and a discriminator column is used to identify which type each row represents.</span></span>

> [!NOTE]
> <span data-ttu-id="80e9c-108">EF Core 尚不支援 EF6 所支援的每一類型資料表（TPT）和每個具體的資料表類型（TPC）。</span><span class="sxs-lookup"><span data-stu-id="80e9c-108">The table-per-type (TPT) and table-per-concrete-type (TPC), which are supported by EF6, are not yet supported by EF Core.</span></span> <span data-ttu-id="80e9c-109">TPT 是規劃 EF Core 5.0 的主要功能。</span><span class="sxs-lookup"><span data-stu-id="80e9c-109">TPT is a major feature planned for EF Core 5.0.</span></span>

## <a name="entity-type-hierarchy-mapping"></a><span data-ttu-id="80e9c-110">實體類型階層架構對應</span><span class="sxs-lookup"><span data-stu-id="80e9c-110">Entity type hierarchy mapping</span></span>

<span data-ttu-id="80e9c-111">依照慣例，只有在模型中明確包含兩個或多個繼承類型時，EF 才會設定繼承。</span><span class="sxs-lookup"><span data-stu-id="80e9c-111">By convention, EF will only set up inheritance if two or more inherited types are explicitly included in the model.</span></span> <span data-ttu-id="80e9c-112">EF 不會自動掃描模型中未包含的基底或衍生類型。</span><span class="sxs-lookup"><span data-stu-id="80e9c-112">EF will not automatically scan for base or derived types that are not otherwise included in the model.</span></span>

<span data-ttu-id="80e9c-113">您可以在繼承階層架構中公開每個類型的 DbSet，以將類型包含在模型中：</span><span class="sxs-lookup"><span data-stu-id="80e9c-113">You can include types in the model by exposing a DbSet for each type in the inheritance hierarchy:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?name=InheritanceDbSets&highlight=3-4)]

<span data-ttu-id="80e9c-114">此模型會對應至下列資料庫架構（請注意隱含建立的*鑒別*子資料行，其可識別每個資料列中儲存的是哪一種類型的*Blog* ）：</span><span class="sxs-lookup"><span data-stu-id="80e9c-114">This model be mapped to the following database schema (note the implicitly-created *Discriminator* column, which identifies which type of *Blog* is stored in each row):</span></span>

![image](_static/inheritance-tph-data.png)

>[!NOTE]
> <span data-ttu-id="80e9c-116">使用 TPH 對應時，視需要自動將資料庫資料行設為可為 null。</span><span class="sxs-lookup"><span data-stu-id="80e9c-116">Database columns are automatically made nullable as necessary when using TPH mapping.</span></span> <span data-ttu-id="80e9c-117">例如， *RssUrl*資料行可為 null，因為一般的*Blog*實例沒有該屬性。</span><span class="sxs-lookup"><span data-stu-id="80e9c-117">For example, the *RssUrl* column is nullable because regular *Blog* instances do not have that property.</span></span>

<span data-ttu-id="80e9c-118">如果您不想要針對階層中的一或多個實體公開 DbSet，您也可以使用流暢的 API 來確保它們包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="80e9c-118">If you don't want to expose a DbSet for one or more entities in the hierarchy, you can also use the Fluent API to ensure they are included in the model.</span></span>

> [!TIP]
> <span data-ttu-id="80e9c-119">如果您不依賴慣例，可以使用 `HasBaseType`明確地指定基底類型。</span><span class="sxs-lookup"><span data-stu-id="80e9c-119">If you don't rely on conventions, you can specify the base type explicitly using `HasBaseType`.</span></span> <span data-ttu-id="80e9c-120">您也可以使用 `.HasBaseType((Type)null)` 從階層中移除實體類型。</span><span class="sxs-lookup"><span data-stu-id="80e9c-120">You can also use `.HasBaseType((Type)null)` to remove an entity type from the hierarchy.</span></span>

## <a name="discriminator-configuration"></a><span data-ttu-id="80e9c-121">鑒別子設定</span><span class="sxs-lookup"><span data-stu-id="80e9c-121">Discriminator configuration</span></span>

<span data-ttu-id="80e9c-122">您可以設定鑒別子資料行的名稱和類型，以及用來識別階層中每個類型的值：</span><span class="sxs-lookup"><span data-stu-id="80e9c-122">You can configure the name and type of the discriminator column and the values that are used to identify each type in the hierarchy:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorConfiguration.cs?name=DiscriminatorConfiguration&highlight=4-6)]

<span data-ttu-id="80e9c-123">在上述範例中，EF 已在階層的基底實體上以[陰影屬性](xref:core/modeling/shadow-properties)的形式，隱含地新增鑒別子。</span><span class="sxs-lookup"><span data-stu-id="80e9c-123">In the examples above, EF added the discriminator implicitly as a [shadow property](xref:core/modeling/shadow-properties) on the base entity of the hierarchy.</span></span> <span data-ttu-id="80e9c-124">此屬性可以設定為與任何其他內容相同：</span><span class="sxs-lookup"><span data-stu-id="80e9c-124">This property can be configured like any other:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorPropertyConfiguration.cs?name=DiscriminatorPropertyConfiguration&highlight=4-5)]

<span data-ttu-id="80e9c-125">最後，鑒別子也可以對應至實體中的一般 .NET 屬性：</span><span class="sxs-lookup"><span data-stu-id="80e9c-125">Finally, the discriminator can also be mapped to a regular .NET property in your entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs?name=NonShadowDiscriminator&highlight=4)]

## <a name="shared-columns"></a><span data-ttu-id="80e9c-126">共用資料行</span><span class="sxs-lookup"><span data-stu-id="80e9c-126">Shared columns</span></span>

<span data-ttu-id="80e9c-127">根據預設，當階層中的兩個同級實體類型具有相同名稱的屬性時，它們會對應至兩個不同的資料行。</span><span class="sxs-lookup"><span data-stu-id="80e9c-127">By default, when two sibling entity types in the hierarchy have a property with the same name, they will be mapped to two separate columns.</span></span> <span data-ttu-id="80e9c-128">不過，如果其類型相同，則可以對應到相同的資料庫資料行：</span><span class="sxs-lookup"><span data-stu-id="80e9c-128">However, if their type is identical they can be mapped to the same database column:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs?name=SharedTPHColumns&highlight=9,13)]
