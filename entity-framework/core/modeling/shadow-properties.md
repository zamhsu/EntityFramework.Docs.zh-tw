---
title: 陰影屬性-EF Core
description: 在 Entity Framework Core 模型中設定陰影屬性
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: 75369266-d2b9-4416-b118-ed238f81f599
uid: core/modeling/shadow-properties
ms.openlocfilehash: 06dbba7a4f6da247e6afde96debb0762fa55ce7e
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616711"
---
# <a name="shadow-properties"></a><span data-ttu-id="fa0a8-103">陰影屬性</span><span class="sxs-lookup"><span data-stu-id="fa0a8-103">Shadow Properties</span></span>

<span data-ttu-id="fa0a8-104">陰影屬性是未在 .NET 實體類別中定義的屬性，但是是在 EF Core 模型中為該實體類型定義的。</span><span class="sxs-lookup"><span data-stu-id="fa0a8-104">Shadow properties are properties that are not defined in your .NET entity class but are defined for that entity type in the EF Core model.</span></span> <span data-ttu-id="fa0a8-105">這些屬性的值和狀態只會在變更追蹤程式中維護。</span><span class="sxs-lookup"><span data-stu-id="fa0a8-105">The value and state of these properties is maintained purely in the Change Tracker.</span></span> <span data-ttu-id="fa0a8-106">當資料庫中的資料不應該在對應的實體類型上公開時，陰影屬性會很有用。</span><span class="sxs-lookup"><span data-stu-id="fa0a8-106">Shadow properties are useful when there is data in the database that should not be exposed on the mapped entity types.</span></span>

## <a name="foreign-key-shadow-properties"></a><span data-ttu-id="fa0a8-107">外鍵陰影屬性</span><span class="sxs-lookup"><span data-stu-id="fa0a8-107">Foreign key shadow properties</span></span>

<span data-ttu-id="fa0a8-108">陰影屬性最常用於外鍵屬性，也就是兩個實體之間的關聯性是由資料庫中的外鍵值表示，但在實體類型上使用實體類型之間的導覽屬性來管理關聯性。</span><span class="sxs-lookup"><span data-stu-id="fa0a8-108">Shadow properties are most often used for foreign key properties, where the relationship between two entities is represented by a foreign key value in the database, but the relationship is managed on the entity types using navigation properties between the entity types.</span></span> <span data-ttu-id="fa0a8-109">依照慣例，當探索到關聯性，但在相依實體類別中找不到任何外鍵屬性時，EF 將會引入陰影屬性。</span><span class="sxs-lookup"><span data-stu-id="fa0a8-109">By convention, EF will introduce a shadow property when a relationship is discovered but no foreign key property is found in the dependent entity class.</span></span>

<span data-ttu-id="fa0a8-110">屬性將會命名為 `<navigation property name><principal key property name>` (相依實體上的導覽（指向主體實體）用於命名) 。</span><span class="sxs-lookup"><span data-stu-id="fa0a8-110">The property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming).</span></span> <span data-ttu-id="fa0a8-111">如果主要索引鍵屬性名稱包含導覽屬性的名稱，則名稱就是 `<principal key property name>` 。</span><span class="sxs-lookup"><span data-stu-id="fa0a8-111">If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`.</span></span> <span data-ttu-id="fa0a8-112">如果相依實體上沒有導覽屬性，則會在其位置使用主體類型名稱。</span><span class="sxs-lookup"><span data-stu-id="fa0a8-112">If there is no navigation property on the dependent entity, then the principal type name is used in its place.</span></span>

<span data-ttu-id="fa0a8-113">例如，下列程式代碼清單會導致將 `BlogId` 陰影屬性引進 `Post` 實體：</span><span class="sxs-lookup"><span data-stu-id="fa0a8-113">For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a><span data-ttu-id="fa0a8-114">設定陰影屬性</span><span class="sxs-lookup"><span data-stu-id="fa0a8-114">Configuring shadow properties</span></span>

<span data-ttu-id="fa0a8-115">您可以使用流暢的 API 來設定陰影屬性。</span><span class="sxs-lookup"><span data-stu-id="fa0a8-115">You can use the Fluent API to configure shadow properties.</span></span> <span data-ttu-id="fa0a8-116">一旦您呼叫的字串多載之後 `Property` ，您就可以將任何設定呼叫連結至其他屬性。</span><span class="sxs-lookup"><span data-stu-id="fa0a8-116">Once you have called the string overload of `Property`, you can chain any of the configuration calls you would for other properties.</span></span> <span data-ttu-id="fa0a8-117">在下列範例中，因為沒有 `Blog` 名為的 CLR 屬性 `LastUpdated` ，所以會建立陰影屬性：</span><span class="sxs-lookup"><span data-stu-id="fa0a8-117">In the following sample, since `Blog` has no CLR property named `LastUpdated`, a shadow property is created:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

<span data-ttu-id="fa0a8-118">如果提供給方法的名稱 `Property` 符合現有屬性的名稱 (陰影屬性，或是實體類別上定義的) ，則程式碼將會設定該現有的屬性，而不是引入新的陰影屬性。</span><span class="sxs-lookup"><span data-stu-id="fa0a8-118">If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.</span></span>

## <a name="accessing-shadow-properties"></a><span data-ttu-id="fa0a8-119">存取陰影屬性</span><span class="sxs-lookup"><span data-stu-id="fa0a8-119">Accessing shadow properties</span></span>

<span data-ttu-id="fa0a8-120">您可以透過 API 取得及變更陰影屬性值 `ChangeTracker` ：</span><span class="sxs-lookup"><span data-stu-id="fa0a8-120">Shadow property values can be obtained and changed through the `ChangeTracker` API:</span></span>

``` csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

<span data-ttu-id="fa0a8-121">您可以透過靜態方法，在 LINQ 查詢中參考陰影屬性 `EF.Property` ：</span><span class="sxs-lookup"><span data-stu-id="fa0a8-121">Shadow properties can be referenced in LINQ queries via the `EF.Property` static method:</span></span>

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

<span data-ttu-id="fa0a8-122">因為變更追蹤器不會追蹤傳回的實體，所以無法在無追蹤查詢之後存取陰影屬性。</span><span class="sxs-lookup"><span data-stu-id="fa0a8-122">Shadow properties cannot be accessed after a no-tracking query since the entities returned are not tracked by the change tracker.</span></span>
