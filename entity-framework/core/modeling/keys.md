---
title: 金鑰-EF Core
description: 如何在使用 Entity Framework Core 時設定實體類型的索引鍵
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/06/2019
uid: core/modeling/keys
ms.openlocfilehash: abd65a5ea079a49fd7a3bbc84a9337f6ee19fab1
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416464"
---
# <a name="keys"></a><span data-ttu-id="56ec9-103">索引鍵</span><span class="sxs-lookup"><span data-stu-id="56ec9-103">Keys</span></span>

<span data-ttu-id="56ec9-104">索引鍵可作為每個實體實例的唯一識別碼。</span><span class="sxs-lookup"><span data-stu-id="56ec9-104">A key serves as a unique identifier for each entity instance.</span></span> <span data-ttu-id="56ec9-105">EF 中的大部分實體都有單一索引鍵，其對應至關係資料庫中的*主鍵*概念（針對沒有索引鍵的實體，請參閱[無索引鍵實體](xref:core/modeling/keyless-entity-types)）。</span><span class="sxs-lookup"><span data-stu-id="56ec9-105">Most entities in EF have a single key, which maps to the concept of a *primary key* in relational databases (for entities without keys, see [Keyless entities](xref:core/modeling/keyless-entity-types)).</span></span> <span data-ttu-id="56ec9-106">實體可以有超出主要金鑰的其他索引鍵（如需詳細資訊，請參閱[替代金鑰](#alternate-keys)）。</span><span class="sxs-lookup"><span data-stu-id="56ec9-106">Entities can have additional keys beyond the primary key (see [Alternate Keys](#alternate-keys) for more information).</span></span>

<span data-ttu-id="56ec9-107">依照慣例，名為 `Id` 或 `<type name>Id` 的屬性會設定為實體的主要索引鍵。</span><span class="sxs-lookup"><span data-stu-id="56ec9-107">By convention, a property named `Id` or `<type name>Id` will be configured as the primary key of an entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyId.cs?name=KeyId&highlight=3,11)]

> [!NOTE]
> <span data-ttu-id="56ec9-108">[擁有的實體類型](xref:core/modeling/owned-entities)會使用不同的規則來定義索引鍵。</span><span class="sxs-lookup"><span data-stu-id="56ec9-108">[Owned entity types](xref:core/modeling/owned-entities) use different rules to define keys.</span></span>

<span data-ttu-id="56ec9-109">您可以將單一屬性設定為實體的主要金鑰，如下所示：</span><span class="sxs-lookup"><span data-stu-id="56ec9-109">You can configure a single property to be the primary key of an entity as follows:</span></span>

## <a name="data-annotations"></a>[<span data-ttu-id="56ec9-110">資料註解</span><span class="sxs-lookup"><span data-stu-id="56ec9-110">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/KeySingle.cs?name=KeySingle&highlight=3)]

## <a name="fluent-api"></a>[<span data-ttu-id="56ec9-111">流暢的 API</span><span class="sxs-lookup"><span data-stu-id="56ec9-111">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeySingle.cs?name=KeySingle&highlight=4)]

***

<span data-ttu-id="56ec9-112">您也可以將多個屬性設定為實體的索引鍵，這就是所謂的複合索引鍵。</span><span class="sxs-lookup"><span data-stu-id="56ec9-112">You can also configure multiple properties to be the key of an entity - this is known as a composite key.</span></span> <span data-ttu-id="56ec9-113">複合索引鍵只能使用流暢的 API 來設定;慣例永遠不會設定複合索引鍵，而且您不能使用資料批註來設定它。</span><span class="sxs-lookup"><span data-stu-id="56ec9-113">Composite keys can only be configured using the Fluent API; conventions will never setup a composite key, and you can not use Data Annotations to configure one.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyComposite.cs?name=KeyComposite&highlight=4)]

## <a name="primary-key-name"></a><span data-ttu-id="56ec9-114">主要金鑰名稱</span><span class="sxs-lookup"><span data-stu-id="56ec9-114">Primary key name</span></span>

<span data-ttu-id="56ec9-115">依照慣例，會以 `PK_<type name>`的名稱來建立關係資料庫的主要索引鍵。</span><span class="sxs-lookup"><span data-stu-id="56ec9-115">By convention, on relational databases primary keys are created with the name `PK_<type name>`.</span></span> <span data-ttu-id="56ec9-116">您可以設定 primary key 條件約束的名稱，如下所示：</span><span class="sxs-lookup"><span data-stu-id="56ec9-116">You can configure the name of the primary key constraint as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyName.cs?name=KeyName&highlight=5)]

## <a name="key-types-and-values"></a><span data-ttu-id="56ec9-117">金鑰類型和值</span><span class="sxs-lookup"><span data-stu-id="56ec9-117">Key types and values</span></span>

<span data-ttu-id="56ec9-118">雖然 EF Core 支援使用任何基本類型的屬性做為主鍵，包括 `string`、`Guid`、`byte[]` 和其他專案，但並非所有的資料庫都支援所有類型做為索引鍵。</span><span class="sxs-lookup"><span data-stu-id="56ec9-118">While EF Core supports using properties of any primitive type as the primary key, including `string`, `Guid`, `byte[]` and others, not all databases support all types as keys.</span></span> <span data-ttu-id="56ec9-119">在某些情況下，索引鍵值可以自動轉換成支援的類型，否則應該[手動指定](xref:core/modeling/value-conversions)轉換。</span><span class="sxs-lookup"><span data-stu-id="56ec9-119">In some cases the key values can be converted to a supported type automatically, otherwise the conversion should be [specified manually](xref:core/modeling/value-conversions).</span></span>

<span data-ttu-id="56ec9-120">將新的實體加入至內容時，索引鍵屬性必須一律具有非預設值，但某些類型會[由資料庫產生](xref:core/modeling/generated-properties)。</span><span class="sxs-lookup"><span data-stu-id="56ec9-120">Key properties must always have a non-default value when adding a new entity to the context, but some types will be [generated by the database](xref:core/modeling/generated-properties).</span></span> <span data-ttu-id="56ec9-121">在這種情況下，當加入實體以供追蹤時，EF 會嘗試產生暫存值。</span><span class="sxs-lookup"><span data-stu-id="56ec9-121">In that case EF will try to generate a temporary value when the entity is added for tracking purposes.</span></span> <span data-ttu-id="56ec9-122">在呼叫[SaveChanges](/dotnet/api/Microsoft.EntityFrameworkCore.DbContext.SaveChanges)之後，暫時的值將會被資料庫產生的值所取代。</span><span class="sxs-lookup"><span data-stu-id="56ec9-122">After [SaveChanges](/dotnet/api/Microsoft.EntityFrameworkCore.DbContext.SaveChanges) is called the temporary value will be replaced by the value generated by the database.</span></span>

> [!Important]
> <span data-ttu-id="56ec9-123">如果索引鍵屬性的值是由資料庫產生，而且在加入實體時指定非預設值，則 EF 會假設實體已存在於資料庫中，而且會嘗試更新它，而不是插入新的。</span><span class="sxs-lookup"><span data-stu-id="56ec9-123">If a key property has its value generated by the database and a non-default value is specified when an entity is added, then EF will assume that the entity already exists in the database and will try to update it instead of inserting a new one.</span></span> <span data-ttu-id="56ec9-124">若要避免這種關閉值的產生，或查看[如何為產生的屬性指定明確的值](../saving/explicit-values-generated-properties.md)。</span><span class="sxs-lookup"><span data-stu-id="56ec9-124">To avoid this turn off value generation or see [how to specify explicit values for generated properties](../saving/explicit-values-generated-properties.md).</span></span>

## <a name="alternate-keys"></a><span data-ttu-id="56ec9-125">替代索引鍵</span><span class="sxs-lookup"><span data-stu-id="56ec9-125">Alternate Keys</span></span>

<span data-ttu-id="56ec9-126">除了主鍵以外，另一個索引鍵會做為每個實體實例的替代唯一識別碼;它可用來做為關聯性的目標。</span><span class="sxs-lookup"><span data-stu-id="56ec9-126">An alternate key serves as an alternate unique identifier for each entity instance in addition to the primary key; it can be used as the target of a relationship.</span></span> <span data-ttu-id="56ec9-127">當使用關係資料庫時，這會對應至替代索引鍵資料行上唯一索引/條件約束的概念，以及一個或多個參考資料行的 foreign key 條件約束。</span><span class="sxs-lookup"><span data-stu-id="56ec9-127">When using a relational database this maps to the concept of a unique index/constraint on the alternate key column(s) and one or more foreign key constraints that reference the column(s).</span></span>

> [!TIP]
> <span data-ttu-id="56ec9-128">如果您只想要在資料行上強制執行唯一性，請定義唯一索引，而不是替代金鑰（請參閱[索引](indexes.md)）。</span><span class="sxs-lookup"><span data-stu-id="56ec9-128">If you just want to enforce uniqueness on a column, define a unique index rather than an alternate key (see [Indexes](indexes.md)).</span></span> <span data-ttu-id="56ec9-129">在 EF 中，其他索引鍵是唯讀的，而且會在唯一的索引上提供額外的語義，因為它們可以當做外鍵的目標使用。</span><span class="sxs-lookup"><span data-stu-id="56ec9-129">In EF, alternate keys are read-only and provide additional semantics over unique indexes because they can be used as the target of a foreign key.</span></span>

<span data-ttu-id="56ec9-130">您通常會在需要時為您引進替代金鑰，而不需要手動設定。</span><span class="sxs-lookup"><span data-stu-id="56ec9-130">Alternate keys are typically introduced for you when needed and you do not need to manually configure them.</span></span> <span data-ttu-id="56ec9-131">依照慣例，當您識別的屬性不是主要索引鍵做為關聯性的目標時，會為您引進替代索引鍵。</span><span class="sxs-lookup"><span data-stu-id="56ec9-131">By convention, an alternate key is introduced for you when you identify a property which isn't the primary key as the target of a relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/AlternateKey.cs?name=AlternateKey&highlight=12)]

<span data-ttu-id="56ec9-132">您也可以將單一屬性設定為替代金鑰：</span><span class="sxs-lookup"><span data-stu-id="56ec9-132">You can also configure a single property to be an alternate key:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeySingle.cs?name=AlternateKeySingle&highlight=4)]

<span data-ttu-id="56ec9-133">您也可以將多個屬性設定為替代金鑰（也稱為複合替代金鑰）：</span><span class="sxs-lookup"><span data-stu-id="56ec9-133">You can also configure multiple properties to be an alternate key (known as a composite alternate key):</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeyComposite.cs?name=AlternateKeyComposite&highlight=4)]

<span data-ttu-id="56ec9-134">最後，依照慣例，針對替代索引鍵導入的索引和條件約束將會命名為 `AK_<type name>_<property name>` （對於複合的替代索引鍵 `<property name>` 會變成以底線分隔的屬性名稱清單）。</span><span class="sxs-lookup"><span data-stu-id="56ec9-134">Finally, by convention, the index and constraint that are introduced for an alternate key will be named `AK_<type name>_<property name>` (for composite alternate keys `<property name>` becomes an underscore separated list of property names).</span></span> <span data-ttu-id="56ec9-135">您可以設定替代金鑰的索引和 unique 條件約束的名稱：</span><span class="sxs-lookup"><span data-stu-id="56ec9-135">You can configure the name of the alternate key's index and unique constraint:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeyName.cs?name=AlternateKeyName&highlight=5)]
