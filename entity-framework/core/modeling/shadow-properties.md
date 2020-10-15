---
title: 陰影和索引子屬性-EF Core
description: 在 Entity Framework Core 模型中設定陰影和索引子屬性
author: AndriySvyryd
ms.date: 10/09/2020
uid: core/modeling/shadow-properties
ms.openlocfilehash: 417ab57a4a77ecf626e54eeca900744d84e3fe08
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063890"
---
# <a name="shadow-and-indexer-properties"></a><span data-ttu-id="35d98-103">陰影和索引子屬性</span><span class="sxs-lookup"><span data-stu-id="35d98-103">Shadow and Indexer Properties</span></span>

<span data-ttu-id="35d98-104">陰影屬性是未在 .NET 實體類別中定義的屬性，但是是在 EF Core 模型中為該實體類型定義的。</span><span class="sxs-lookup"><span data-stu-id="35d98-104">Shadow properties are properties that are not defined in your .NET entity class but are defined for that entity type in the EF Core model.</span></span> <span data-ttu-id="35d98-105">這些屬性的值和狀態只會在變更追蹤程式中維護。</span><span class="sxs-lookup"><span data-stu-id="35d98-105">The value and state of these properties is maintained purely in the Change Tracker.</span></span> <span data-ttu-id="35d98-106">當資料庫中的資料不應該在對應的實體類型上公開時，陰影屬性會很有用。</span><span class="sxs-lookup"><span data-stu-id="35d98-106">Shadow properties are useful when there is data in the database that should not be exposed on the mapped entity types.</span></span>

## <a name="foreign-key-shadow-properties"></a><span data-ttu-id="35d98-107">外鍵陰影屬性</span><span class="sxs-lookup"><span data-stu-id="35d98-107">Foreign key shadow properties</span></span>

<span data-ttu-id="35d98-108">陰影屬性最常用於外鍵屬性，也就是兩個實體之間的關聯性是由資料庫中的外鍵值表示，但在實體類型上使用實體類型之間的導覽屬性來管理關聯性。</span><span class="sxs-lookup"><span data-stu-id="35d98-108">Shadow properties are most often used for foreign key properties, where the relationship between two entities is represented by a foreign key value in the database, but the relationship is managed on the entity types using navigation properties between the entity types.</span></span> <span data-ttu-id="35d98-109">依照慣例，當探索到關聯性，但在相依實體類別中找不到任何外鍵屬性時，EF 將會引入陰影屬性。</span><span class="sxs-lookup"><span data-stu-id="35d98-109">By convention, EF will introduce a shadow property when a relationship is discovered but no foreign key property is found in the dependent entity class.</span></span>

<span data-ttu-id="35d98-110">屬性將會命名為 `<navigation property name><principal key property name>` (相依實體上的導覽（指向主體實體）用於命名) 。</span><span class="sxs-lookup"><span data-stu-id="35d98-110">The property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming).</span></span> <span data-ttu-id="35d98-111">如果主要索引鍵屬性名稱包含導覽屬性的名稱，則名稱就是 `<principal key property name>` 。</span><span class="sxs-lookup"><span data-stu-id="35d98-111">If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`.</span></span> <span data-ttu-id="35d98-112">如果相依實體上沒有導覽屬性，則會在其位置使用主體類型名稱。</span><span class="sxs-lookup"><span data-stu-id="35d98-112">If there is no navigation property on the dependent entity, then the principal type name is used in its place.</span></span>

<span data-ttu-id="35d98-113">例如，下列程式代碼清單會導致將 `BlogId` 陰影屬性引進 `Post` 實體：</span><span class="sxs-lookup"><span data-stu-id="35d98-113">For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a><span data-ttu-id="35d98-114">設定陰影屬性</span><span class="sxs-lookup"><span data-stu-id="35d98-114">Configuring shadow properties</span></span>

<span data-ttu-id="35d98-115">您可以使用流暢的 API 來設定陰影屬性。</span><span class="sxs-lookup"><span data-stu-id="35d98-115">You can use the Fluent API to configure shadow properties.</span></span> <span data-ttu-id="35d98-116">一旦您呼叫的字串多載之後 `Property` ，您就可以將任何設定呼叫連結至其他屬性。</span><span class="sxs-lookup"><span data-stu-id="35d98-116">Once you have called the string overload of `Property`, you can chain any of the configuration calls you would for other properties.</span></span> <span data-ttu-id="35d98-117">在下列範例中，因為沒有 `Blog` 名為的 CLR 屬性 `LastUpdated` ，所以會建立陰影屬性：</span><span class="sxs-lookup"><span data-stu-id="35d98-117">In the following sample, since `Blog` has no CLR property named `LastUpdated`, a shadow property is created:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

<span data-ttu-id="35d98-118">如果提供給方法的名稱 `Property` 符合現有屬性的名稱 (陰影屬性，或是實體類別上定義的) ，則程式碼將會設定該現有的屬性，而不是引入新的陰影屬性。</span><span class="sxs-lookup"><span data-stu-id="35d98-118">If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.</span></span>

## <a name="accessing-shadow-properties"></a><span data-ttu-id="35d98-119">存取陰影屬性</span><span class="sxs-lookup"><span data-stu-id="35d98-119">Accessing shadow properties</span></span>

<span data-ttu-id="35d98-120">您可以透過 API 取得及變更陰影屬性值 `ChangeTracker` ：</span><span class="sxs-lookup"><span data-stu-id="35d98-120">Shadow property values can be obtained and changed through the `ChangeTracker` API:</span></span>

```csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

<span data-ttu-id="35d98-121">您可以透過靜態方法，在 LINQ 查詢中參考陰影屬性 `EF.Property` ：</span><span class="sxs-lookup"><span data-stu-id="35d98-121">Shadow properties can be referenced in LINQ queries via the `EF.Property` static method:</span></span>

```csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

<span data-ttu-id="35d98-122">因為變更追蹤器不會追蹤傳回的實體，所以無法在無追蹤查詢之後存取陰影屬性。</span><span class="sxs-lookup"><span data-stu-id="35d98-122">Shadow properties cannot be accessed after a no-tracking query since the entities returned are not tracked by the change tracker.</span></span>

## <a name="property-bag-entity-types"></a><span data-ttu-id="35d98-123">屬性包實體類型</span><span class="sxs-lookup"><span data-stu-id="35d98-123">Property bag entity types</span></span>

> [!NOTE]
> <span data-ttu-id="35d98-124">已在 EF Core 5.0 中新增屬性包實體類型的支援。</span><span class="sxs-lookup"><span data-stu-id="35d98-124">Support for Property bag entity types was added in EF Core 5.0.</span></span>

<span data-ttu-id="35d98-125">僅包含索引子屬性的實體類型稱為「屬性包」實體類型。</span><span class="sxs-lookup"><span data-stu-id="35d98-125">Entity types that contain only indexer properties are known as property bag entity types.</span></span> <span data-ttu-id="35d98-126">這些實體類型沒有陰影屬性。</span><span class="sxs-lookup"><span data-stu-id="35d98-126">These entity types don't have shadow properties.</span></span> <span data-ttu-id="35d98-127">目前僅 `Dictionary<string, object>` 支援做為屬性包實體類型。</span><span class="sxs-lookup"><span data-stu-id="35d98-127">Currently only `Dictionary<string, object>` is supported as a property bag entity type.</span></span> <span data-ttu-id="35d98-128">這表示必須將它設定為具有唯一名稱的共用實體型別，而且 `DbSet` 必須使用呼叫來執行對應的屬性 `Set` 。</span><span class="sxs-lookup"><span data-stu-id="35d98-128">This means that it must be configured as a shared entity type with a unique name and the corresponding `DbSet` property must be implemented using a `Set` call.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedType.cs?name=SharedType&highlight=3,7)]