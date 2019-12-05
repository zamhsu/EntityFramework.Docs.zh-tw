---
title: 金鑰（主要）-EF Core
description: 如何在使用 Entity Framework Core 時設定實體類型的索引鍵
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/06/2019
uid: core/modeling/keys
ms.openlocfilehash: fdaccb42259ba9dad97a05c626edd0291ca96cb0
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824617"
---
# <a name="keys-primary"></a><span data-ttu-id="9d5e0-103">索引鍵 (主要)</span><span class="sxs-lookup"><span data-stu-id="9d5e0-103">Keys (primary)</span></span>

<span data-ttu-id="9d5e0-104">索引鍵可作為每個實體實例的主要唯一識別碼。</span><span class="sxs-lookup"><span data-stu-id="9d5e0-104">A key serves as the primary unique identifier for each entity instance.</span></span> <span data-ttu-id="9d5e0-105">當使用關係資料庫時，這會對應至*主鍵*的概念。</span><span class="sxs-lookup"><span data-stu-id="9d5e0-105">When using a relational database this maps to the concept of a *primary key*.</span></span> <span data-ttu-id="9d5e0-106">您也可以設定非主要金鑰的唯一識別碼（如需詳細資訊，請參閱[替代金鑰](alternate-keys.md)）。</span><span class="sxs-lookup"><span data-stu-id="9d5e0-106">You can also configure a unique identifier that is not the primary key (see [Alternate Keys](alternate-keys.md) for more information).</span></span>

<span data-ttu-id="9d5e0-107">您可以使用下列其中一種方法來設定/建立主要金鑰。</span><span class="sxs-lookup"><span data-stu-id="9d5e0-107">One of the following methods can be used to setup/create a primary key.</span></span>

## <a name="conventions"></a><span data-ttu-id="9d5e0-108">慣例</span><span class="sxs-lookup"><span data-stu-id="9d5e0-108">Conventions</span></span>

<span data-ttu-id="9d5e0-109">根據預設，名為 `Id` 或 `<type name>Id` 的屬性會設定為實體的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="9d5e0-109">By default, a property named `Id` or `<type name>Id` will be configured as the key of an entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyId.cs?name=KeyId&highlight=3)]

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyTypeNameId.cs?name=KeyId&highlight=3)]

> [!NOTE]
> <span data-ttu-id="9d5e0-110">[擁有的實體類型](xref:core/modeling/owned-entities)會使用不同的規則來定義索引鍵。</span><span class="sxs-lookup"><span data-stu-id="9d5e0-110">[Owned entity types](xref:core/modeling/owned-entities) use different rules to define keys.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="9d5e0-111">資料註釋</span><span class="sxs-lookup"><span data-stu-id="9d5e0-111">Data Annotations</span></span>

<span data-ttu-id="9d5e0-112">您可以使用資料批註，將單一屬性設定為實體的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="9d5e0-112">You can use Data Annotations to configure a single property to be the key of an entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/KeySingle.cs?highlight=13)]

## <a name="fluent-api"></a><span data-ttu-id="9d5e0-113">Fluent API</span><span class="sxs-lookup"><span data-stu-id="9d5e0-113">Fluent API</span></span>

<span data-ttu-id="9d5e0-114">您可以使用流暢的 API，將單一屬性設定為實體的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="9d5e0-114">You can use the Fluent API to configure a single property to be the key of an entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeySingle.cs?highlight=11,12)]

<span data-ttu-id="9d5e0-115">您也可以使用流暢的 API，將多個屬性設定為實體的索引鍵（也稱為複合索引鍵）。</span><span class="sxs-lookup"><span data-stu-id="9d5e0-115">You can also use the Fluent API to configure multiple properties to be the key of an entity (known as a composite key).</span></span> <span data-ttu-id="9d5e0-116">複合索引鍵只能使用流暢的 API 慣例來設定複合索引鍵，而且您不能使用資料批註來設定它。</span><span class="sxs-lookup"><span data-stu-id="9d5e0-116">Composite keys can only be configured using the Fluent API - conventions will never setup a composite key and you can not use Data Annotations to configure one.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyComposite.cs?highlight=11,12)]

## <a name="key-types-and-values"></a><span data-ttu-id="9d5e0-117">金鑰類型和值</span><span class="sxs-lookup"><span data-stu-id="9d5e0-117">Key types and values</span></span>

<span data-ttu-id="9d5e0-118">EF Core 支援使用任何基本類型的屬性作為主鍵，包括 `string`、`Guid`、`byte[]` 及其他專案。</span><span class="sxs-lookup"><span data-stu-id="9d5e0-118">EF Core supports using properties of any primitive type as the primary key, including `string`, `Guid`, `byte[]` and others.</span></span> <span data-ttu-id="9d5e0-119">但並非所有的資料庫都支援它們。</span><span class="sxs-lookup"><span data-stu-id="9d5e0-119">But not all databases support them.</span></span> <span data-ttu-id="9d5e0-120">在某些情況下，索引鍵值可以自動轉換成支援的類型，否則應該[手動指定](xref:core/modeling/value-conversions)轉換。</span><span class="sxs-lookup"><span data-stu-id="9d5e0-120">In some cases the key values can be converted to a supported type automatically, otherwise the conversion should be [specified manually](xref:core/modeling/value-conversions).</span></span>

<span data-ttu-id="9d5e0-121">將新的實體加入至內容時，索引鍵屬性必須一律具有非預設值，但某些類型會[由資料庫產生](xref:core/modeling/generated-properties)。</span><span class="sxs-lookup"><span data-stu-id="9d5e0-121">Key properties must always have a non-default value when adding a new entity to the context, but some types will be [generated by the database](xref:core/modeling/generated-properties).</span></span> <span data-ttu-id="9d5e0-122">在這種情況下，當加入實體以供追蹤時，EF 會嘗試產生暫存值。</span><span class="sxs-lookup"><span data-stu-id="9d5e0-122">In that case EF will try to generate a temporary value when the entity is added for tracking purposes.</span></span> <span data-ttu-id="9d5e0-123">在呼叫[SaveChanges](/dotnet/api/Microsoft.EntityFrameworkCore.DbContext.SaveChanges)之後，暫時的值將會被資料庫產生的值所取代。</span><span class="sxs-lookup"><span data-stu-id="9d5e0-123">After [SaveChanges](/dotnet/api/Microsoft.EntityFrameworkCore.DbContext.SaveChanges) is called the temporary value will be replaced by the value generated by the database.</span></span>

> [!Important]
> <span data-ttu-id="9d5e0-124">如果索引鍵屬性具有資料庫產生的值，而且在加入實體時指定了非預設值，則 EF 會假設實體已存在於資料庫中，並會嘗試更新它，而不是插入新的。</span><span class="sxs-lookup"><span data-stu-id="9d5e0-124">If a key property has value generated by the database and a non-default value is specified when an entity is added then EF will assume that the entity already exists in the database and will try to update it instead of inserting a new one.</span></span> <span data-ttu-id="9d5e0-125">若要避免這種關閉值的產生，或查看[如何為產生的屬性指定明確的值](../saving/explicit-values-generated-properties.md)。</span><span class="sxs-lookup"><span data-stu-id="9d5e0-125">To avoid this turn off value generation or see [how to specify explicit values for generated properties](../saving/explicit-values-generated-properties.md).</span></span>