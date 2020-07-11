---
title: 擁有的實體類型-EF Core
description: 如何在使用 Entity Framework Core 時設定擁有的實體類型或匯總
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/06/2019
uid: core/modeling/owned-entities
ms.openlocfilehash: 6ff98d005c0a868d420509571378756c56edc54a
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238121"
---
# <a name="owned-entity-types"></a><span data-ttu-id="b1322-103">擁有的實體類型</span><span class="sxs-lookup"><span data-stu-id="b1322-103">Owned Entity Types</span></span>

<span data-ttu-id="b1322-104">EF Core 可讓您將只能出現在其他實體類型之導覽屬性的實體類型模型化。</span><span class="sxs-lookup"><span data-stu-id="b1322-104">EF Core allows you to model entity types that can only ever appear on navigation properties of other entity types.</span></span> <span data-ttu-id="b1322-105">這些稱為「_自有實體類型_」。</span><span class="sxs-lookup"><span data-stu-id="b1322-105">These are called _owned entity types_.</span></span> <span data-ttu-id="b1322-106">包含自有實體類型的實體是_其擁有者。_</span><span class="sxs-lookup"><span data-stu-id="b1322-106">The entity containing an owned entity type is its _owner_.</span></span>

<span data-ttu-id="b1322-107">擁有的實體基本上是擁有者的一部分，如果沒有，就不能存在，它們在概念上類似于[匯總](https://martinfowler.com/bliki/DDD_Aggregate.html)。</span><span class="sxs-lookup"><span data-stu-id="b1322-107">Owned entities are essentially a part of the owner and cannot exist without it, they are conceptually similar to [aggregates](https://martinfowler.com/bliki/DDD_Aggregate.html).</span></span> <span data-ttu-id="b1322-108">這表示擁有的實體是由與擁有者之關聯性的相依端定義。</span><span class="sxs-lookup"><span data-stu-id="b1322-108">This means that the owned entity is by definition on the dependent side of the relationship with the owner.</span></span>

## <a name="explicit-configuration"></a><span data-ttu-id="b1322-109">明確設定</span><span class="sxs-lookup"><span data-stu-id="b1322-109">Explicit configuration</span></span>

<span data-ttu-id="b1322-110">模型中的 EF Core 絕對不會包含擁有的實體類型。</span><span class="sxs-lookup"><span data-stu-id="b1322-110">Owned entity types are never included by EF Core in the model by convention.</span></span> <span data-ttu-id="b1322-111">您可以使用 `OwnsOne` 中的方法，或以 `OnModelCreating` `OwnedAttribute` EF Core 2.1 中的 (新增來標注類型) 將類型設定為擁有的類型。</span><span class="sxs-lookup"><span data-stu-id="b1322-111">You can use the `OwnsOne` method in `OnModelCreating` or annotate the type with `OwnedAttribute` (new in EF Core 2.1) to configure the type as an owned type.</span></span>

<span data-ttu-id="b1322-112">在此範例中， `StreetAddress` 是不含識別屬性的類型。</span><span class="sxs-lookup"><span data-stu-id="b1322-112">In this example, `StreetAddress` is a type with no identity property.</span></span> <span data-ttu-id="b1322-113">它用做訂單類型的屬性，指定特定訂單的送貨地址。</span><span class="sxs-lookup"><span data-stu-id="b1322-113">It is used as a property of the Order type to specify the shipping address for a particular order.</span></span>

<span data-ttu-id="b1322-114">`OwnedAttribute`從另一個實體類型參考時，我們可以使用將它視為擁有的實體：</span><span class="sxs-lookup"><span data-stu-id="b1322-114">We can use the `OwnedAttribute` to treat it as an owned entity when referenced from another entity type:</span></span>

[!code-csharp[StreetAddress](../../../samples/core/Modeling/OwnedEntities/StreetAddress.cs?name=StreetAddress)]

[!code-csharp[Order](../../../samples/core/Modeling/OwnedEntities/Order.cs?name=Order)]

<span data-ttu-id="b1322-115">您也可以使用 `OwnsOne` 中的方法，將 `OnModelCreating` `ShippingAddress` 屬性指定為實體類型的自有實體 `Order` ，並視需要設定其他 facet。</span><span class="sxs-lookup"><span data-stu-id="b1322-115">It is also possible to use the `OwnsOne` method in `OnModelCreating` to specify that the `ShippingAddress` property is an Owned Entity of the `Order` entity type and to configure additional facets if needed.</span></span>

[!code-csharp[OwnsOne](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOne)]

<span data-ttu-id="b1322-116">如果 `ShippingAddress` 屬性在型別中是私用的 `Order` ，您可以使用方法的字串版本 `OwnsOne` ：</span><span class="sxs-lookup"><span data-stu-id="b1322-116">If the `ShippingAddress` property is private in the `Order` type, you can use the string version of the `OwnsOne` method:</span></span>

[!code-csharp[OwnsOneString](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneString)]

<span data-ttu-id="b1322-117">如需詳細內容，請參閱[完整的範例專案](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities)。</span><span class="sxs-lookup"><span data-stu-id="b1322-117">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities) for more context.</span></span>

## <a name="implicit-keys"></a><span data-ttu-id="b1322-118">隱含索引鍵</span><span class="sxs-lookup"><span data-stu-id="b1322-118">Implicit keys</span></span>

<span data-ttu-id="b1322-119">`OwnsOne`透過參考導覽所設定或探索的擁有類型，一律與擁有者具有一對一的關聯性，因此它們不需要自己的索引鍵值，因為外鍵值是唯一的。</span><span class="sxs-lookup"><span data-stu-id="b1322-119">Owned types configured with `OwnsOne` or discovered through a reference navigation always have a one-to-one relationship with the owner, therefore they don't need their own key values as the foreign key values are unique.</span></span> <span data-ttu-id="b1322-120">在上述範例中，型別不 `StreetAddress` 需要定義索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="b1322-120">In the previous example, the `StreetAddress` type does not need to define a key property.</span></span>  

<span data-ttu-id="b1322-121">若要瞭解 EF Core 如何追蹤這些物件，您可以知道主要索引鍵是建立為所擁有類型的[陰影屬性](xref:core/modeling/shadow-properties)。</span><span class="sxs-lookup"><span data-stu-id="b1322-121">In order to understand how EF Core tracks these objects, it is useful to know that a primary key is created as a [shadow property](xref:core/modeling/shadow-properties) for the owned type.</span></span> <span data-ttu-id="b1322-122">擁有的類型之實例的索引鍵值，會與擁有者實例的索引鍵值相同。</span><span class="sxs-lookup"><span data-stu-id="b1322-122">The value of the key of an instance of the owned type will be the same as the value of the key of the owner instance.</span></span>

## <a name="collections-of-owned-types"></a><span data-ttu-id="b1322-123">擁有類型的集合</span><span class="sxs-lookup"><span data-stu-id="b1322-123">Collections of owned types</span></span>

> [!NOTE]
> <span data-ttu-id="b1322-124">此為 EF Core 2.2 中的新功能。</span><span class="sxs-lookup"><span data-stu-id="b1322-124">This feature is new in EF Core 2.2.</span></span>

<span data-ttu-id="b1322-125">若要設定所擁有類型的集合，請使用 `OwnsMany` 中的 `OnModelCreating` 。</span><span class="sxs-lookup"><span data-stu-id="b1322-125">To configure a collection of owned types use `OwnsMany` in `OnModelCreating`.</span></span>

<span data-ttu-id="b1322-126">擁有的類型需要主要金鑰。</span><span class="sxs-lookup"><span data-stu-id="b1322-126">Owned types need a primary key.</span></span> <span data-ttu-id="b1322-127">如果 .NET 類型上沒有良好的候選項目屬性，EF Core 可以嘗試建立一個。</span><span class="sxs-lookup"><span data-stu-id="b1322-127">If there are no good candidates properties on the .NET type, EF Core can try to create one.</span></span> <span data-ttu-id="b1322-128">不過，當擁有的型別是透過集合所定義時，就無法只建立陰影屬性來當做外鍵同時作為擁有者實例的「擁有者」和「主鍵」，就像我們一樣 `OwnsOne` ：每個擁有者都可以有多個擁有的型別實例，因此擁有者的索引鍵不足以提供每個擁有之實例的唯一身分識別。</span><span class="sxs-lookup"><span data-stu-id="b1322-128">However, when owned types are defined through a collection, it isn't enough to just create a shadow property to act as both the foreign key into the owner and the primary key of the owned instance, as we do for `OwnsOne`: there can be multiple owned type instances for each owner, and hence the key of the owner isn't enough to provide a unique identity for each owned instance.</span></span>

<span data-ttu-id="b1322-129">這兩個最直接的解決辦法是：</span><span class="sxs-lookup"><span data-stu-id="b1322-129">The two most straightforward solutions to this are:</span></span>

- <span data-ttu-id="b1322-130">在與指向擁有者的外鍵無關的新屬性上定義代理主鍵。</span><span class="sxs-lookup"><span data-stu-id="b1322-130">Defining a surrogate primary key on a new property independent of the foreign key that points to the owner.</span></span> <span data-ttu-id="b1322-131">包含的值在所有擁有者中都必須是唯一的 (例如，如果父系 {1} 具有子系 {1} ，則父系 {2} 不能有子 {1}) ，因此值不會有任何固有的意義。</span><span class="sxs-lookup"><span data-stu-id="b1322-131">The contained values would need to be unique across all owners (e.g. if Parent {1} has Child {1}, then Parent {2} cannot have Child {1}), so the value doesn't have any inherent meaning.</span></span> <span data-ttu-id="b1322-132">因為外鍵不是主鍵的一部分，所以可以變更其值，因此您可以將子系從一個父系移到另一個父代，但這通常會針對匯總的語義進行。</span><span class="sxs-lookup"><span data-stu-id="b1322-132">Since the foreign key is not part of the primary key its values can be changed, so you could move a child from one parent to another one, however this usually goes against aggregate semantics.</span></span>
- <span data-ttu-id="b1322-133">使用外鍵和其他屬性做為複合索引鍵。</span><span class="sxs-lookup"><span data-stu-id="b1322-133">Using the foreign key and an additional property as a composite key.</span></span> <span data-ttu-id="b1322-134">對於指定的 (父系而言，其他屬性值現在只需要是唯一的，因此如果父系 {1} 具有 child， {1,1} 父系 {2} 仍然可以有子 {2,1}) 。</span><span class="sxs-lookup"><span data-stu-id="b1322-134">The additional property value now only needs to be unique for a given parent (so if Parent {1} has Child {1,1} then Parent {2} can still have Child {2,1}).</span></span> <span data-ttu-id="b1322-135">藉由讓主鍵的外鍵部分成為主要金鑰的一部分，擁有者與擁有的實體之間的關聯性就會變成不可變，並更清楚反映匯總的語法。</span><span class="sxs-lookup"><span data-stu-id="b1322-135">By making the foreign key part of the primary key the relationship between the owner and the owned entity becomes immutable and reflects aggregate semantics better.</span></span> <span data-ttu-id="b1322-136">這就是 EF Core 預設執行的工作。</span><span class="sxs-lookup"><span data-stu-id="b1322-136">This is what EF Core does by default.</span></span>

<span data-ttu-id="b1322-137">在此範例中，我們將使用 `Distributor` 類別：</span><span class="sxs-lookup"><span data-stu-id="b1322-137">In this example we'll use the `Distributor` class:</span></span>

[!code-csharp[Distributor](../../../samples/core/Modeling/OwnedEntities/Distributor.cs?name=Distributor)]

<span data-ttu-id="b1322-138">根據預設，用於透過導覽屬性所參考之自有類型的主要索引鍵為， `ShippingCenters` `("DistributorId", "Id")` 其中 `"DistributorId"` 是 FK，而 `"Id"` 是唯一 `int` 值。</span><span class="sxs-lookup"><span data-stu-id="b1322-138">By default the primary key used for the owned type referenced through the `ShippingCenters` navigation property will be `("DistributorId", "Id")` where `"DistributorId"` is the FK and `"Id"` is a unique `int` value.</span></span>

<span data-ttu-id="b1322-139">若要設定不同的 PK 呼叫 `HasKey` ：</span><span class="sxs-lookup"><span data-stu-id="b1322-139">To configure a different PK call `HasKey`:</span></span>

[!code-csharp[OwnsMany](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsMany)]

> [!NOTE]
> <span data-ttu-id="b1322-140">在 EF Core 3.0 `WithOwner()` 方法不存在之前，請先移除此呼叫。</span><span class="sxs-lookup"><span data-stu-id="b1322-140">Before EF Core 3.0 `WithOwner()` method didn't exist so this call should be removed.</span></span> <span data-ttu-id="b1322-141">此外，也不會自動探索主鍵，因此一定要指定。</span><span class="sxs-lookup"><span data-stu-id="b1322-141">Also the primary key was not discovered automatically so it always had to be specified.</span></span>

## <a name="mapping-owned-types-with-table-splitting"></a><span data-ttu-id="b1322-142">對應具有資料表分割的擁有類型</span><span class="sxs-lookup"><span data-stu-id="b1322-142">Mapping owned types with table splitting</span></span>

<span data-ttu-id="b1322-143">使用關係資料庫時，根據預設，參考擁有的類型會對應至與擁有者相同的資料表。</span><span class="sxs-lookup"><span data-stu-id="b1322-143">When using relational databases, by default reference owned types are mapped to the same table as the owner.</span></span> <span data-ttu-id="b1322-144">這需要將資料表分割為兩個：某些資料行將用來儲存擁有者的資料，而有些資料行則用來儲存擁有之實體的資料。</span><span class="sxs-lookup"><span data-stu-id="b1322-144">This requires splitting the table in two: some columns will be used to store the data of the owner, and some columns will be used to store data of the owned entity.</span></span> <span data-ttu-id="b1322-145">這是稱為「[資料表分割](table-splitting.md)」的常見功能。</span><span class="sxs-lookup"><span data-stu-id="b1322-145">This is a common feature known as [table splitting](table-splitting.md).</span></span>

<span data-ttu-id="b1322-146">根據預設，EF Core 會在模式_Navigation_OwnedEntityProperty_後面，為所擁有實體類型的屬性命名資料庫資料行。</span><span class="sxs-lookup"><span data-stu-id="b1322-146">By default, EF Core will name the database columns for the properties of the owned entity type following the pattern _Navigation_OwnedEntityProperty_.</span></span> <span data-ttu-id="b1322-147">因此，這些 `StreetAddress` 屬性會出現在名為 ' ShippingAddress_Street ' 和 ' ShippingAddress_City ' 的 ' Orders ' 資料表中。</span><span class="sxs-lookup"><span data-stu-id="b1322-147">Therefore the `StreetAddress` properties will appear in the 'Orders' table with the names 'ShippingAddress_Street' and 'ShippingAddress_City'.</span></span>

<span data-ttu-id="b1322-148">您可以使用 `HasColumnName` 方法來重新命名這些資料行：</span><span class="sxs-lookup"><span data-stu-id="b1322-148">You can use the `HasColumnName` method to rename those columns:</span></span>

[!code-csharp[ColumnNames](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=ColumnNames)]

> [!NOTE]
> <span data-ttu-id="b1322-149">大部分的一般實體類型設定方法（例如「[忽略](/dotnet/api/microsoft.entityframeworkcore.metadata.builders.ownednavigationbuilder.ignore)」）都可以用相同的方式呼叫。</span><span class="sxs-lookup"><span data-stu-id="b1322-149">Most of the normal entity type configuration methods like [Ignore](/dotnet/api/microsoft.entityframeworkcore.metadata.builders.ownednavigationbuilder.ignore) can be called in the same way.</span></span>

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a><span data-ttu-id="b1322-150">在多個擁有的類型之間共用相同的 .NET 類型</span><span class="sxs-lookup"><span data-stu-id="b1322-150">Sharing the same .NET type among multiple owned types</span></span>

<span data-ttu-id="b1322-151">擁有的實體類型可以與另一個擁有的實體類型屬於相同的 .NET 類型，因此 .NET 類型可能不足以識別擁有的類型。</span><span class="sxs-lookup"><span data-stu-id="b1322-151">An owned entity type can be of the same .NET type as another owned entity type, therefore the .NET type may not be enough to identify an owned type.</span></span>

<span data-ttu-id="b1322-152">在這些情況下，從擁有者指向自有實體的屬性會變成所擁有實體類型的_定義導覽_。</span><span class="sxs-lookup"><span data-stu-id="b1322-152">In those cases, the property pointing from the owner to the owned entity becomes the _defining navigation_ of the owned entity type.</span></span> <span data-ttu-id="b1322-153">從 EF Core 的觀點來看，定義的導覽會與 .NET 類型一起屬於類型的身分識別。</span><span class="sxs-lookup"><span data-stu-id="b1322-153">From the perspective of EF Core, the defining navigation is part of the type's identity alongside the .NET type.</span></span>

<span data-ttu-id="b1322-154">例如，在下列類別中， `ShippingAddress` 和 `BillingAddress` 都是相同的 .net 類型 `StreetAddress` ：</span><span class="sxs-lookup"><span data-stu-id="b1322-154">For example, in the following class `ShippingAddress` and `BillingAddress` are both of the same .NET type, `StreetAddress`:</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

<span data-ttu-id="b1322-155">若要瞭解 EF Core 如何區分這些物件的追蹤實例，請將定義的導覽與擁有者的索引鍵值以及擁有之類型的 .NET 類型做為實例的一部分，可能會很有用。</span><span class="sxs-lookup"><span data-stu-id="b1322-155">In order to understand how EF Core will distinguish tracked instances of these objects, it may be useful to think that the defining navigation has become part of the key of the instance alongside the value of the key of the owner and the .NET type of the owned type.</span></span>

## <a name="nested-owned-types"></a><span data-ttu-id="b1322-156">嵌套擁有的類型</span><span class="sxs-lookup"><span data-stu-id="b1322-156">Nested owned types</span></span>

<span data-ttu-id="b1322-157">在此範例 `OrderDetails` 中 `BillingAddress` ，擁有和 `ShippingAddress` ，兩者都是 `StreetAddress` 類型。</span><span class="sxs-lookup"><span data-stu-id="b1322-157">In this example `OrderDetails` owns `BillingAddress` and `ShippingAddress`, which are both `StreetAddress` types.</span></span> <span data-ttu-id="b1322-158">然後 `DetailedOrder` 類型擁有 `OrderDetails`。</span><span class="sxs-lookup"><span data-stu-id="b1322-158">Then `OrderDetails` is owned by the `DetailedOrder` type.</span></span>

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/OwnedEntities/DetailedOrder.cs?name=DetailedOrder)]

[!code-csharp[OrderStatus](../../../samples/core/Modeling/OwnedEntities/OrderStatus.cs?name=OrderStatus)]

<span data-ttu-id="b1322-159">對自有類型的每個導覽會定義具有完全獨立設定的個別實體類型。</span><span class="sxs-lookup"><span data-stu-id="b1322-159">Each navigation to an owned type defines a separate entity type with completely independent configuration.</span></span>

<span data-ttu-id="b1322-160">除了嵌套的擁有類型之外，擁有的類型也可以參考一般實體，只要擁有的實體位於相依端，就可以是擁有者或不同的實體。</span><span class="sxs-lookup"><span data-stu-id="b1322-160">In addition to nested owned types, an owned type can reference a regular entity which can be either the owner or a different entity as long as the owned entity is on the dependent side.</span></span> <span data-ttu-id="b1322-161">這項功能會在 EF6 中設定除了複雜類型之外的自有實體類型。</span><span class="sxs-lookup"><span data-stu-id="b1322-161">This capability sets owned entity types apart from complex types in EF6.</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

## <a name="configuring-owned-types"></a><span data-ttu-id="b1322-162">設定擁有的類型</span><span class="sxs-lookup"><span data-stu-id="b1322-162">Configuring Owned Types</span></span>

<span data-ttu-id="b1322-163">您可以 `OwnsOne` 在流暢的呼叫中連結方法，以設定此模型：</span><span class="sxs-lookup"><span data-stu-id="b1322-163">It is possible to chain the `OwnsOne` method in a fluent call to configure this model:</span></span>

[!code-csharp[OwnsOneNested](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneNested)]

<span data-ttu-id="b1322-164">請注意， `WithOwner` 用來定義在擁有者上指向之導覽屬性的呼叫。</span><span class="sxs-lookup"><span data-stu-id="b1322-164">Notice the `WithOwner` call used to define the navigation property pointing back at the owner.</span></span> <span data-ttu-id="b1322-165">若要定義不屬於擁有權關聯性之擁有者實體類型的導覽， `WithOwner()` 請在不使用任何引數的情況下呼叫。</span><span class="sxs-lookup"><span data-stu-id="b1322-165">To define a navigation to the owner entity type that's not part of the ownership relationship `WithOwner()` should be called without any arguments.</span></span>

<span data-ttu-id="b1322-166">您也可以在和上使用來達到這個結果 `OwnedAttribute` `OrderDetails` `StreetAddress` 。</span><span class="sxs-lookup"><span data-stu-id="b1322-166">It is also possible to achieve this result using `OwnedAttribute` on both `OrderDetails` and `StreetAddress`.</span></span>

<span data-ttu-id="b1322-167">此外，請注意 `Navigation` 呼叫。</span><span class="sxs-lookup"><span data-stu-id="b1322-167">In addition, notice the `Navigation` call.</span></span> <span data-ttu-id="b1322-168">在 EFCore 5.0 中，可以[針對非擁有的導覽屬性](relationships.md#configuring-navigation-properties)進一步設定所擁有類型的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="b1322-168">In EFCore 5.0, navigation properties to owned types can be further configured [as for non-owned navigation properties](relationships.md#configuring-navigation-properties).</span></span>

## <a name="storing-owned-types-in-separate-tables"></a><span data-ttu-id="b1322-169">將擁有的類型儲存在不同的資料表中</span><span class="sxs-lookup"><span data-stu-id="b1322-169">Storing owned types in separate tables</span></span>

<span data-ttu-id="b1322-170">也不同于 EF6 複雜類型，擁有的類型可以儲存在擁有者的個別資料表中。</span><span class="sxs-lookup"><span data-stu-id="b1322-170">Also unlike EF6 complex types, owned types can be stored in a separate table from the owner.</span></span> <span data-ttu-id="b1322-171">若要覆寫將擁有的類型對應到與擁有者相同之資料表的慣例，您只要呼叫 `ToTable` ，並提供不同的資料表名稱即可。</span><span class="sxs-lookup"><span data-stu-id="b1322-171">In order to override the convention that maps an owned type to the same table as the owner, you can simply call `ToTable` and provide a different table name.</span></span> <span data-ttu-id="b1322-172">下列範例會將 `OrderDetails` 和它的兩個位址對應至不同的資料表， `DetailedOrder` 如下所示：</span><span class="sxs-lookup"><span data-stu-id="b1322-172">The following example will map `OrderDetails` and its two addresses to a separate table from `DetailedOrder`:</span></span>

[!code-csharp[OwnsOneTable](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneTable)]

<span data-ttu-id="b1322-173">您也可以使用 `TableAttribute` 來完成這項操作，但請注意，如果有多個導覽屬於自有類型，這會失敗，因為在這種情況下，多個實體類型會對應至相同的資料表。</span><span class="sxs-lookup"><span data-stu-id="b1322-173">It is also possible to use the `TableAttribute` to accomplish this, but note that this would fail if there are multiple navigations to the owned type since in that case multiple entity types would be mapped to the same table.</span></span>

## <a name="querying-owned-types"></a><span data-ttu-id="b1322-174">查詢擁有的類型</span><span class="sxs-lookup"><span data-stu-id="b1322-174">Querying owned types</span></span>

<span data-ttu-id="b1322-175">查詢擁有者時，預設會包含擁有的類型。</span><span class="sxs-lookup"><span data-stu-id="b1322-175">When querying the owner the owned types will be included by default.</span></span> <span data-ttu-id="b1322-176">`Include`即使自有的類型儲存在不同的資料表中，也不需要使用方法。</span><span class="sxs-lookup"><span data-stu-id="b1322-176">It is not necessary to use the `Include` method, even if the owned types are stored in a separate table.</span></span> <span data-ttu-id="b1322-177">根據先前所述的模型，下列查詢將會取得 `Order` ， `OrderDetails` 而這兩個資料庫是由資料庫所擁有 `StreetAddresses` ：</span><span class="sxs-lookup"><span data-stu-id="b1322-177">Based on the model described before, the following query will get `Order`, `OrderDetails` and the two owned `StreetAddresses` from the database:</span></span>

[!code-csharp[DetailedOrderQuery](../../../samples/core/Modeling/OwnedEntities/Program.cs?name=DetailedOrderQuery)]

## <a name="limitations"></a><span data-ttu-id="b1322-178">限制</span><span class="sxs-lookup"><span data-stu-id="b1322-178">Limitations</span></span>

<span data-ttu-id="b1322-179">其中一些限制是擁有的實體類型如何工作的基礎，但有些則是我們可以在未來版本中移除的限制：</span><span class="sxs-lookup"><span data-stu-id="b1322-179">Some of these limitations are fundamental to how owned entity types work, but some others are restrictions that we may be able to remove in future releases:</span></span>

### <a name="by-design-restrictions"></a><span data-ttu-id="b1322-180">依設計的限制</span><span class="sxs-lookup"><span data-stu-id="b1322-180">By-design restrictions</span></span>

- <span data-ttu-id="b1322-181">您無法 `DbSet<T>` 為擁有的類型建立</span><span class="sxs-lookup"><span data-stu-id="b1322-181">You cannot create a `DbSet<T>` for an owned type</span></span>
- <span data-ttu-id="b1322-182">您無法 `Entity<T>()` 在上使用自有類型呼叫`ModelBuilder`</span><span class="sxs-lookup"><span data-stu-id="b1322-182">You cannot call `Entity<T>()` with an owned type on `ModelBuilder`</span></span>

### <a name="current-shortcomings"></a><span data-ttu-id="b1322-183">目前的缺點</span><span class="sxs-lookup"><span data-stu-id="b1322-183">Current shortcomings</span></span>

- <span data-ttu-id="b1322-184">擁有的實體類型不能有繼承階層</span><span class="sxs-lookup"><span data-stu-id="b1322-184">Owned entity types cannot have inheritance hierarchies</span></span>
- <span data-ttu-id="b1322-185">擁有的實體類型的參考導覽不可以是 null，除非它們已明確對應至擁有者的個別資料表</span><span class="sxs-lookup"><span data-stu-id="b1322-185">Reference navigations to owned entity types cannot be null unless they are explicitly mapped to a separate table from the owner</span></span>
- <span data-ttu-id="b1322-186">擁有的實體類型實例無法由多個擁有者共用 (這是已知的值物件的案例，無法使用擁有的實體類型來執行) </span><span class="sxs-lookup"><span data-stu-id="b1322-186">Instances of owned entity types cannot be shared by multiple owners (this is a well-known scenario for value objects that cannot be implemented using owned entity types)</span></span>

### <a name="shortcomings-in-previous-versions"></a><span data-ttu-id="b1322-187">舊版的缺點</span><span class="sxs-lookup"><span data-stu-id="b1322-187">Shortcomings in previous versions</span></span>

- <span data-ttu-id="b1322-188">在 EF Core 2.0 中，除非擁有的實體明確對應至擁有者階層中的個別資料表，否則無法在衍生實體類型中宣告所擁有之實體類型的導覽。</span><span class="sxs-lookup"><span data-stu-id="b1322-188">In EF Core 2.0, navigations to owned entity types cannot be declared in derived entity types unless the owned entities are explicitly mapped to a separate table from the owner hierarchy.</span></span> <span data-ttu-id="b1322-189">EF Core 2.1 已移除這項限制</span><span class="sxs-lookup"><span data-stu-id="b1322-189">This limitation has been removed in EF Core 2.1</span></span>
- <span data-ttu-id="b1322-190">在 EF Core 2.0 和2.1 中，僅支援擁有類型的參考導覽。</span><span class="sxs-lookup"><span data-stu-id="b1322-190">In EF Core 2.0 and 2.1 only reference navigations to owned types were supported.</span></span> <span data-ttu-id="b1322-191">EF Core 2.2 已移除這項限制</span><span class="sxs-lookup"><span data-stu-id="b1322-191">This limitation has been removed in EF Core 2.2</span></span>
