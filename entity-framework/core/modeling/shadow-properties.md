---
title: 陰影和索引子屬性-EF Core
description: 在 Entity Framework Core 模型中設定陰影和索引子屬性
author: AndriySvyryd
ms.date: 10/09/2020
uid: core/modeling/shadow-properties
ms.openlocfilehash: 180478212b683a271d2519cc1a4c79be5d3f11b9
ms.sourcegitcommit: 42bbf7f68e92c364c5fff63092d3eb02229f568d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94503185"
---
# <a name="shadow-and-indexer-properties"></a><span data-ttu-id="7d14f-103">陰影和索引子屬性</span><span class="sxs-lookup"><span data-stu-id="7d14f-103">Shadow and Indexer Properties</span></span>

<span data-ttu-id="7d14f-104">陰影屬性是在您的 .NET 實體類別中未定義的屬性，但是是在 EF Core 模型中為該實體類型定義的。</span><span class="sxs-lookup"><span data-stu-id="7d14f-104">Shadow properties are properties that aren't defined in your .NET entity class but are defined for that entity type in the EF Core model.</span></span> <span data-ttu-id="7d14f-105">這些屬性的值和狀態只會在變更追蹤程式中維護。</span><span class="sxs-lookup"><span data-stu-id="7d14f-105">The value and state of these properties is maintained purely in the Change Tracker.</span></span> <span data-ttu-id="7d14f-106">當資料庫中的資料不應該在對應的實體類型上公開時，陰影屬性會很有用。</span><span class="sxs-lookup"><span data-stu-id="7d14f-106">Shadow properties are useful when there's data in the database that shouldn't be exposed on the mapped entity types.</span></span>

<span data-ttu-id="7d14f-107">索引子屬性是實體型別屬性，由 .NET 實體類別中的 [索引子](/dotnet/csharp/programming-guide/indexers/) 支援。</span><span class="sxs-lookup"><span data-stu-id="7d14f-107">Indexer properties are entity type properties, which are backed by an [indexer](/dotnet/csharp/programming-guide/indexers/) in .NET entity class.</span></span> <span data-ttu-id="7d14f-108">您可以使用 .NET 類別實例上的索引子來存取它們。</span><span class="sxs-lookup"><span data-stu-id="7d14f-108">They can be accessed using the indexer on the .NET class instances.</span></span> <span data-ttu-id="7d14f-109">它也可讓您在不變更 CLR 類別的情況下，將其他屬性加入至實體類型。</span><span class="sxs-lookup"><span data-stu-id="7d14f-109">It also allows you to add additional properties to the entity type without changing the CLR class.</span></span>

## <a name="foreign-key-shadow-properties"></a><span data-ttu-id="7d14f-110">外鍵陰影屬性</span><span class="sxs-lookup"><span data-stu-id="7d14f-110">Foreign key shadow properties</span></span>

<span data-ttu-id="7d14f-111">陰影屬性最常用於外鍵屬性，也就是兩個實體之間的關聯性是由資料庫中的外鍵值表示，但在實體類型上使用實體類型之間的導覽屬性來管理關聯性。</span><span class="sxs-lookup"><span data-stu-id="7d14f-111">Shadow properties are most often used for foreign key properties, where the relationship between two entities is represented by a foreign key value in the database, but the relationship is managed on the entity types using navigation properties between the entity types.</span></span> <span data-ttu-id="7d14f-112">依照慣例，當探索到關聯性，但在相依實體類別中找不到任何外鍵屬性時，EF 將會引入陰影屬性。</span><span class="sxs-lookup"><span data-stu-id="7d14f-112">By convention, EF will introduce a shadow property when a relationship is discovered but no foreign key property is found in the dependent entity class.</span></span>

<span data-ttu-id="7d14f-113">屬性將會命名為 `<navigation property name><principal key property name>` (相依實體上的導覽（指向主體實體）用於命名) 。</span><span class="sxs-lookup"><span data-stu-id="7d14f-113">The property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming).</span></span> <span data-ttu-id="7d14f-114">如果主要索引鍵屬性名稱包含導覽屬性的名稱，則名稱就是 `<principal key property name>` 。</span><span class="sxs-lookup"><span data-stu-id="7d14f-114">If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`.</span></span> <span data-ttu-id="7d14f-115">如果相依實體上沒有導覽屬性，則會在其位置使用主體類型名稱。</span><span class="sxs-lookup"><span data-stu-id="7d14f-115">If there is no navigation property on the dependent entity, then the principal type name is used in its place.</span></span>

<span data-ttu-id="7d14f-116">例如，下列程式代碼清單會導致將 `BlogId` 陰影屬性引進 `Post` 實體：</span><span class="sxs-lookup"><span data-stu-id="7d14f-116">For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a><span data-ttu-id="7d14f-117">設定陰影屬性</span><span class="sxs-lookup"><span data-stu-id="7d14f-117">Configuring shadow properties</span></span>

<span data-ttu-id="7d14f-118">您可以使用流暢的 API 來設定陰影屬性。</span><span class="sxs-lookup"><span data-stu-id="7d14f-118">You can use the Fluent API to configure shadow properties.</span></span> <span data-ttu-id="7d14f-119">一旦您呼叫的字串多載之後 `Property` ，您就可以將任何設定呼叫連結至其他屬性。</span><span class="sxs-lookup"><span data-stu-id="7d14f-119">Once you have called the string overload of `Property`, you can chain any of the configuration calls you would for other properties.</span></span> <span data-ttu-id="7d14f-120">在下列範例中，因為沒有 `Blog` 名為的 CLR 屬性 `LastUpdated` ，所以會建立陰影屬性：</span><span class="sxs-lookup"><span data-stu-id="7d14f-120">In the following sample, since `Blog` has no CLR property named `LastUpdated`, a shadow property is created:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

<span data-ttu-id="7d14f-121">如果提供給方法的名稱 `Property` 符合現有屬性的名稱 (陰影屬性，或是實體類別上定義的) ，則程式碼將會設定該現有的屬性，而不是引入新的陰影屬性。</span><span class="sxs-lookup"><span data-stu-id="7d14f-121">If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.</span></span>

## <a name="accessing-shadow-properties"></a><span data-ttu-id="7d14f-122">存取陰影屬性</span><span class="sxs-lookup"><span data-stu-id="7d14f-122">Accessing shadow properties</span></span>

<span data-ttu-id="7d14f-123">您可以透過 API 取得及變更陰影屬性值 `ChangeTracker` ：</span><span class="sxs-lookup"><span data-stu-id="7d14f-123">Shadow property values can be obtained and changed through the `ChangeTracker` API:</span></span>

```csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

<span data-ttu-id="7d14f-124">您可以透過靜態方法，在 LINQ 查詢中參考陰影屬性 `EF.Property` ：</span><span class="sxs-lookup"><span data-stu-id="7d14f-124">Shadow properties can be referenced in LINQ queries via the `EF.Property` static method:</span></span>

```csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

<span data-ttu-id="7d14f-125">因為變更追蹤器不會追蹤傳回的實體，所以無法在無追蹤查詢之後存取陰影屬性。</span><span class="sxs-lookup"><span data-stu-id="7d14f-125">Shadow properties cannot be accessed after a no-tracking query since the entities returned are not tracked by the change tracker.</span></span>

## <a name="configuring-indexer-properties"></a><span data-ttu-id="7d14f-126">設定索引子屬性</span><span class="sxs-lookup"><span data-stu-id="7d14f-126">Configuring indexer properties</span></span>

<span data-ttu-id="7d14f-127">您可以使用流暢的 API 來設定索引子屬性。</span><span class="sxs-lookup"><span data-stu-id="7d14f-127">You can use the Fluent API to configure indexer properties.</span></span> <span data-ttu-id="7d14f-128">一旦呼叫方法之後 `IndexerProperty` ，您就可以將任何設定呼叫連結至其他屬性。</span><span class="sxs-lookup"><span data-stu-id="7d14f-128">Once you've called the method `IndexerProperty`, you can chain any of the configuration calls you would for other properties.</span></span> <span data-ttu-id="7d14f-129">在下列範例中， `Blog` 已定義索引子，而且將用來建立索引子屬性。</span><span class="sxs-lookup"><span data-stu-id="7d14f-129">In the following sample, `Blog` has an indexer defined and it will be used to create an indexer property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexerProperty.cs?name=ShadowProperty&highlight=3)]

<span data-ttu-id="7d14f-130">如果提供給方法的名稱 `IndexerProperty` 符合現有索引子屬性的名稱，則程式碼會設定該現有的屬性。</span><span class="sxs-lookup"><span data-stu-id="7d14f-130">If the name supplied to the `IndexerProperty` method matches the name of an existing indexer property, then the code will configure that existing property.</span></span> <span data-ttu-id="7d14f-131">如果實體類型具有由實體類別上的屬性所支援的屬性，則會擲回例外狀況，因為必須透過索引子存取索引子屬性。</span><span class="sxs-lookup"><span data-stu-id="7d14f-131">If the entity type has a property, which is backed by a property on the entity class, then an exception is thrown since indexer properties must only be accessed via the indexer.</span></span>

## <a name="property-bag-entity-types"></a><span data-ttu-id="7d14f-132">屬性包實體類型</span><span class="sxs-lookup"><span data-stu-id="7d14f-132">Property bag entity types</span></span>

> [!NOTE]
> <span data-ttu-id="7d14f-133">已在 EF Core 5.0 中新增屬性包實體類型的支援。</span><span class="sxs-lookup"><span data-stu-id="7d14f-133">Support for Property bag entity types was added in EF Core 5.0.</span></span>

<span data-ttu-id="7d14f-134">僅包含索引子屬性的實體類型稱為「屬性包」實體類型。</span><span class="sxs-lookup"><span data-stu-id="7d14f-134">Entity types that contain only indexer properties are known as property bag entity types.</span></span> <span data-ttu-id="7d14f-135">這些實體類型沒有陰影屬性，而 EF 會建立索引子屬性。</span><span class="sxs-lookup"><span data-stu-id="7d14f-135">These entity types don't have shadow properties, instead EF will create indexer properties.</span></span> <span data-ttu-id="7d14f-136">目前僅 `Dictionary<string, object>` 支援做為屬性包實體類型。</span><span class="sxs-lookup"><span data-stu-id="7d14f-136">Currently only `Dictionary<string, object>` is supported as a property bag entity type.</span></span> <span data-ttu-id="7d14f-137">它必須設定為具有唯一名稱的共用實體類型，而且 `DbSet` 必須使用呼叫來執行對應的屬性 `Set` 。</span><span class="sxs-lookup"><span data-stu-id="7d14f-137">It must be configured as a shared entity type with a unique name and the corresponding `DbSet` property must be implemented using a `Set` call.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedType.cs?name=SharedType&highlight=3,7)]
