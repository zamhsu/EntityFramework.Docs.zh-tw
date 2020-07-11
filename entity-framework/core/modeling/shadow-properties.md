---
title: 陰影屬性-EF Core
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: 75369266-d2b9-4416-b118-ed238f81f599
uid: core/modeling/shadow-properties
ms.openlocfilehash: b24ff85d8f5910a5625910e7225a94112d769824
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238277"
---
# <a name="shadow-properties"></a><span data-ttu-id="876b3-102">陰影屬性</span><span class="sxs-lookup"><span data-stu-id="876b3-102">Shadow Properties</span></span>

<span data-ttu-id="876b3-103">陰影屬性是未在您的 .NET 實體類別中定義，但已針對 EF Core 模型中的實體類型定義的屬性。</span><span class="sxs-lookup"><span data-stu-id="876b3-103">Shadow properties are properties that are not defined in your .NET entity class but are defined for that entity type in the EF Core model.</span></span> <span data-ttu-id="876b3-104">這些屬性的值和狀態純粹是在變更追蹤程式中進行維護。</span><span class="sxs-lookup"><span data-stu-id="876b3-104">The value and state of these properties is maintained purely in the Change Tracker.</span></span> <span data-ttu-id="876b3-105">當資料庫中有資料不應在對應的實體類型上公開時，陰影屬性會很有用。</span><span class="sxs-lookup"><span data-stu-id="876b3-105">Shadow properties are useful when there is data in the database that should not be exposed on the mapped entity types.</span></span>

## <a name="foreign-key-shadow-properties"></a><span data-ttu-id="876b3-106">外鍵陰影屬性</span><span class="sxs-lookup"><span data-stu-id="876b3-106">Foreign key shadow properties</span></span>

<span data-ttu-id="876b3-107">陰影屬性最常用於外鍵屬性，其中兩個實體之間的關聯性是由資料庫中的外鍵值所表示，但是在實體類型上，會使用實體類型之間的導覽屬性來管理關聯性。</span><span class="sxs-lookup"><span data-stu-id="876b3-107">Shadow properties are most often used for foreign key properties, where the relationship between two entities is represented by a foreign key value in the database, but the relationship is managed on the entity types using navigation properties between the entity types.</span></span> <span data-ttu-id="876b3-108">依照慣例，EF 會在發現關聯性時引進陰影屬性，但在相依實體類別中找不到外鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="876b3-108">By convention, EF will introduce a shadow property when a relationship is discovered but no foreign key property is found in the dependent entity class.</span></span>

<span data-ttu-id="876b3-109">屬性會命名為 `<navigation property name><principal key property name>` (相依實體（指向主體實體）上的導覽，用於命名) 。</span><span class="sxs-lookup"><span data-stu-id="876b3-109">The property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming).</span></span> <span data-ttu-id="876b3-110">如果主體索引鍵屬性名稱包含導覽屬性的名稱，則名稱會是 `<principal key property name>` 。</span><span class="sxs-lookup"><span data-stu-id="876b3-110">If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`.</span></span> <span data-ttu-id="876b3-111">如果相依實體上沒有導覽屬性，則會在其位置使用主體類型名稱。</span><span class="sxs-lookup"><span data-stu-id="876b3-111">If there is no navigation property on the dependent entity, then the principal type name is used in its place.</span></span>

<span data-ttu-id="876b3-112">例如，下列程式代碼清單會導致將 `BlogId` 陰影屬性引進至 `Post` 實體：</span><span class="sxs-lookup"><span data-stu-id="876b3-112">For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a><span data-ttu-id="876b3-113">設定陰影屬性</span><span class="sxs-lookup"><span data-stu-id="876b3-113">Configuring shadow properties</span></span>

<span data-ttu-id="876b3-114">您可以使用流暢的 API 來設定陰影屬性。</span><span class="sxs-lookup"><span data-stu-id="876b3-114">You can use the Fluent API to configure shadow properties.</span></span> <span data-ttu-id="876b3-115">一旦呼叫的字串多載之後 `Property` ，您就可以將任何其他屬性的設定呼叫鏈在一起。</span><span class="sxs-lookup"><span data-stu-id="876b3-115">Once you have called the string overload of `Property`, you can chain any of the configuration calls you would for other properties.</span></span> <span data-ttu-id="876b3-116">在下列範例中，因為沒有 `Blog` 名為的 CLR 屬性 `LastUpdated` ，所以會建立陰影屬性：</span><span class="sxs-lookup"><span data-stu-id="876b3-116">In the following sample, since `Blog` has no CLR property named `LastUpdated`, a shadow property is created:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

<span data-ttu-id="876b3-117">如果提供給方法的名稱 `Property` 符合現有屬性的名稱 (陰影屬性，或實體類別上定義的) ，則程式碼將會設定該現有的屬性，而不會引進新的陰影屬性。</span><span class="sxs-lookup"><span data-stu-id="876b3-117">If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.</span></span>

## <a name="accessing-shadow-properties"></a><span data-ttu-id="876b3-118">存取陰影屬性</span><span class="sxs-lookup"><span data-stu-id="876b3-118">Accessing shadow properties</span></span>

<span data-ttu-id="876b3-119">您可以透過 API 取得和變更陰影屬性值 `ChangeTracker` ：</span><span class="sxs-lookup"><span data-stu-id="876b3-119">Shadow property values can be obtained and changed through the `ChangeTracker` API:</span></span>

``` csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

<span data-ttu-id="876b3-120">您可以透過靜態方法，在 LINQ 查詢中參考陰影屬性 `EF.Property` ：</span><span class="sxs-lookup"><span data-stu-id="876b3-120">Shadow properties can be referenced in LINQ queries via the `EF.Property` static method:</span></span>

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

<span data-ttu-id="876b3-121">因為變更追蹤器不會追蹤傳回的實體，所以無法在沒有追蹤查詢之後存取陰影屬性。</span><span class="sxs-lookup"><span data-stu-id="876b3-121">Shadow properties cannot be accessed after a no-tracking query since the entities returned are not tracked by the change tracker.</span></span>
