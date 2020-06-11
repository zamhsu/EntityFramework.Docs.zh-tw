---
title: 繼承-EF Core
description: 如何使用 Entity Framework Core 設定實體類型繼承
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 10/27/2016
uid: core/modeling/inheritance
ms.openlocfilehash: 0e94013a0b894b162f4bb3ca8e7acb1aca349011
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664048"
---
# <a name="inheritance"></a><span data-ttu-id="cee1c-103">繼承</span><span class="sxs-lookup"><span data-stu-id="cee1c-103">Inheritance</span></span>

<span data-ttu-id="cee1c-104">EF 可以將 .NET 類型階層架構對應到資料庫。</span><span class="sxs-lookup"><span data-stu-id="cee1c-104">EF can map a .NET type hierarchy to a database.</span></span> <span data-ttu-id="cee1c-105">這可讓您以平常的程式碼撰寫 .NET 實體，使用基底和衍生類型，並讓 EF 順暢地建立適當的資料庫架構、發出查詢等。類型階層的對應實際詳細資料與提供者相關。此頁面描述關係資料庫內容中的繼承支援。</span><span class="sxs-lookup"><span data-stu-id="cee1c-105">This allows you to write your .NET entities in code as usual, using base and derived types, and have EF seamlessly create the appropriate database schema, issue queries, etc. The actual details of how a type hierarchy is mapped are provider-dependent; this page describes inheritance support in the context of a relational database.</span></span>

<span data-ttu-id="cee1c-106">目前，EF Core 只支援每個階層的資料表（TPH）模式。</span><span class="sxs-lookup"><span data-stu-id="cee1c-106">At the moment, EF Core only supports the table-per-hierarchy (TPH) pattern.</span></span> <span data-ttu-id="cee1c-107">TPH 會使用單一資料表來儲存階層中所有類型的資料，而且會使用鑒別子資料行來識別每個資料列所代表的類型。</span><span class="sxs-lookup"><span data-stu-id="cee1c-107">TPH uses a single table to store the data for all types in the hierarchy, and a discriminator column is used to identify which type each row represents.</span></span>

> [!NOTE]
> <span data-ttu-id="cee1c-108">EF Core 尚不支援 EF6 所支援的每一類型資料表（TPT）和每個具體的資料表類型（TPC）。</span><span class="sxs-lookup"><span data-stu-id="cee1c-108">The table-per-type (TPT) and table-per-concrete-type (TPC), which are supported by EF6, are not yet supported by EF Core.</span></span> <span data-ttu-id="cee1c-109">TPT 是規劃 EF Core 5.0 的主要功能。</span><span class="sxs-lookup"><span data-stu-id="cee1c-109">TPT is a major feature planned for EF Core 5.0.</span></span>

## <a name="entity-type-hierarchy-mapping"></a><span data-ttu-id="cee1c-110">實體類型階層架構對應</span><span class="sxs-lookup"><span data-stu-id="cee1c-110">Entity type hierarchy mapping</span></span>

<span data-ttu-id="cee1c-111">依照慣例，EF 不會自動掃描基底或衍生類型;這表示，如果您想要對應階層中的 CLR 型別，您必須在模型上明確指定該型別。</span><span class="sxs-lookup"><span data-stu-id="cee1c-111">By convention, EF will not automatically scan for base or derived types; this means that if you want a CLR type in your hierarchy to be mapped, you must explicitly specify that type on your model.</span></span> <span data-ttu-id="cee1c-112">例如，僅指定階層的基底類型並不會導致 EF Core 隱含地包含其所有子類型。</span><span class="sxs-lookup"><span data-stu-id="cee1c-112">For example, specifying only the base type of a hierarchy will not cause EF Core to implicitly include all of its sub-types.</span></span>

<span data-ttu-id="cee1c-113">下列範例會公開的 DbSet `Blog` 及其子類別 `RssBlog` 。</span><span class="sxs-lookup"><span data-stu-id="cee1c-113">The following sample exposes a DbSet for `Blog` and its subclass `RssBlog`.</span></span> <span data-ttu-id="cee1c-114">如果 `Blog` 有任何其他子類別，它就不會包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="cee1c-114">If `Blog` has any other subclass, it will not be included in the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?name=InheritanceDbSets&highlight=3-4)]

<span data-ttu-id="cee1c-115">此模型會對應至下列資料庫架構（請注意隱含建立的*鑒別*子資料行，它會識別每個資料列中所儲存的*Blog*類型）：</span><span class="sxs-lookup"><span data-stu-id="cee1c-115">This model is mapped to the following database schema (note the implicitly-created *Discriminator* column, which identifies which type of *Blog* is stored in each row):</span></span>

![image](_static/inheritance-tph-data.png)

>[!NOTE]
> <span data-ttu-id="cee1c-117">使用 TPH 對應時，視需要自動將資料庫資料行設為可為 null。</span><span class="sxs-lookup"><span data-stu-id="cee1c-117">Database columns are automatically made nullable as necessary when using TPH mapping.</span></span> <span data-ttu-id="cee1c-118">例如， *RssUrl*資料行可為 null，因為一般的*Blog*實例沒有該屬性。</span><span class="sxs-lookup"><span data-stu-id="cee1c-118">For example, the *RssUrl* column is nullable because regular *Blog* instances do not have that property.</span></span>

<span data-ttu-id="cee1c-119">如果您不想要針對階層中的一或多個實體公開 DbSet，您也可以使用流暢的 API 來確保它們包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="cee1c-119">If you don't want to expose a DbSet for one or more entities in the hierarchy, you can also use the Fluent API to ensure they are included in the model.</span></span>

> [!TIP]
> <span data-ttu-id="cee1c-120">如果您不依賴慣例，可以使用明確地指定基底類型 `HasBaseType` 。</span><span class="sxs-lookup"><span data-stu-id="cee1c-120">If you don't rely on conventions, you can specify the base type explicitly using `HasBaseType`.</span></span> <span data-ttu-id="cee1c-121">您也可以使用 `.HasBaseType((Type)null)` 從階層移除實體類型。</span><span class="sxs-lookup"><span data-stu-id="cee1c-121">You can also use `.HasBaseType((Type)null)` to remove an entity type from the hierarchy.</span></span>

## <a name="discriminator-configuration"></a><span data-ttu-id="cee1c-122">鑒別子設定</span><span class="sxs-lookup"><span data-stu-id="cee1c-122">Discriminator configuration</span></span>

<span data-ttu-id="cee1c-123">您可以設定鑒別子資料行的名稱和類型，以及用來識別階層中每個類型的值：</span><span class="sxs-lookup"><span data-stu-id="cee1c-123">You can configure the name and type of the discriminator column and the values that are used to identify each type in the hierarchy:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorConfiguration.cs?name=DiscriminatorConfiguration&highlight=4-6)]

<span data-ttu-id="cee1c-124">在上述範例中，EF 已在階層的基底實體上以[陰影屬性](xref:core/modeling/shadow-properties)的形式，隱含地新增鑒別子。</span><span class="sxs-lookup"><span data-stu-id="cee1c-124">In the examples above, EF added the discriminator implicitly as a [shadow property](xref:core/modeling/shadow-properties) on the base entity of the hierarchy.</span></span> <span data-ttu-id="cee1c-125">此屬性可以設定為與任何其他內容相同：</span><span class="sxs-lookup"><span data-stu-id="cee1c-125">This property can be configured like any other:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorPropertyConfiguration.cs?name=DiscriminatorPropertyConfiguration&highlight=4-5)]

<span data-ttu-id="cee1c-126">最後，鑒別子也可以對應至實體中的一般 .NET 屬性：</span><span class="sxs-lookup"><span data-stu-id="cee1c-126">Finally, the discriminator can also be mapped to a regular .NET property in your entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs?name=NonShadowDiscriminator&highlight=4)]

## <a name="shared-columns"></a><span data-ttu-id="cee1c-127">共用資料行</span><span class="sxs-lookup"><span data-stu-id="cee1c-127">Shared columns</span></span>

<span data-ttu-id="cee1c-128">根據預設，當階層中的兩個同級實體類型具有相同名稱的屬性時，它們會對應至兩個不同的資料行。</span><span class="sxs-lookup"><span data-stu-id="cee1c-128">By default, when two sibling entity types in the hierarchy have a property with the same name, they will be mapped to two separate columns.</span></span> <span data-ttu-id="cee1c-129">不過，如果其類型相同，則可以對應到相同的資料庫資料行：</span><span class="sxs-lookup"><span data-stu-id="cee1c-129">However, if their type is identical they can be mapped to the same database column:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs?name=SharedTPHColumns&highlight=9,13)]
