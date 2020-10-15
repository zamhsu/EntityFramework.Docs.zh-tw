---
title: 擁有的實體類型-EF Core
description: 使用 Entity Framework Core 時如何設定擁有的實體類型或匯總
author: AndriySvyryd
ms.date: 11/06/2019
uid: core/modeling/owned-entities
ms.openlocfilehash: a49d9aab735232dfd5a3db456410d527f94f3c18
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063773"
---
# <a name="owned-entity-types"></a><span data-ttu-id="bcb99-103">擁有的實體類型</span><span class="sxs-lookup"><span data-stu-id="bcb99-103">Owned Entity Types</span></span>

<span data-ttu-id="bcb99-104">EF Core 可讓您建立只能出現在其他實體類型導覽屬性上的實體類型模型。</span><span class="sxs-lookup"><span data-stu-id="bcb99-104">EF Core allows you to model entity types that can only ever appear on navigation properties of other entity types.</span></span> <span data-ttu-id="bcb99-105">這些稱為 _擁有的實體類型_。</span><span class="sxs-lookup"><span data-stu-id="bcb99-105">These are called _owned entity types_.</span></span> <span data-ttu-id="bcb99-106">包含擁有之實體類型的實體是其 _擁有者_。</span><span class="sxs-lookup"><span data-stu-id="bcb99-106">The entity containing an owned entity type is its _owner_.</span></span>

<span data-ttu-id="bcb99-107">擁有的實體基本上是擁有者的一部分，而且不存在，而且在概念上類似于 [匯總](https://martinfowler.com/bliki/DDD_Aggregate.html)。</span><span class="sxs-lookup"><span data-stu-id="bcb99-107">Owned entities are essentially a part of the owner and cannot exist without it, they are conceptually similar to [aggregates](https://martinfowler.com/bliki/DDD_Aggregate.html).</span></span> <span data-ttu-id="bcb99-108">這表示所擁有的實體是在與擁有者之關聯性的相依端上定義。</span><span class="sxs-lookup"><span data-stu-id="bcb99-108">This means that the owned entity is by definition on the dependent side of the relationship with the owner.</span></span>

## <a name="explicit-configuration"></a><span data-ttu-id="bcb99-109">明確設定</span><span class="sxs-lookup"><span data-stu-id="bcb99-109">Explicit configuration</span></span>

<span data-ttu-id="bcb99-110">依慣例，模型中的 EF Core 絕不會包含擁有的實體類型。</span><span class="sxs-lookup"><span data-stu-id="bcb99-110">Owned entity types are never included by EF Core in the model by convention.</span></span> <span data-ttu-id="bcb99-111">您可以使用中的方法，或使用將類型 `OwnsOne` `OnModelCreating` 標注 `OwnedAttribute` 為，以將類型設定為擁有的類型。</span><span class="sxs-lookup"><span data-stu-id="bcb99-111">You can use the `OwnsOne` method in `OnModelCreating` or annotate the type with `OwnedAttribute` to configure the type as an owned type.</span></span>

<span data-ttu-id="bcb99-112">在此範例中， `StreetAddress` 是沒有識別屬性的型別。</span><span class="sxs-lookup"><span data-stu-id="bcb99-112">In this example, `StreetAddress` is a type with no identity property.</span></span> <span data-ttu-id="bcb99-113">它用做訂單類型的屬性，指定特定訂單的送貨地址。</span><span class="sxs-lookup"><span data-stu-id="bcb99-113">It is used as a property of the Order type to specify the shipping address for a particular order.</span></span>

<span data-ttu-id="bcb99-114">`OwnedAttribute`當您從另一個實體型別參考時，可以使用將它視為擁有的實體：</span><span class="sxs-lookup"><span data-stu-id="bcb99-114">We can use the `OwnedAttribute` to treat it as an owned entity when referenced from another entity type:</span></span>

[!code-csharp[StreetAddress](../../../samples/core/Modeling/OwnedEntities/StreetAddress.cs?name=StreetAddress)]

[!code-csharp[Order](../../../samples/core/Modeling/OwnedEntities/Order.cs?name=Order)]

<span data-ttu-id="bcb99-115">您也可以使用 `OwnsOne` 中的方法，將 `OnModelCreating` `ShippingAddress` 屬性指定為實體類型的擁有實體 `Order` ，並視需要設定其他 facet。</span><span class="sxs-lookup"><span data-stu-id="bcb99-115">It is also possible to use the `OwnsOne` method in `OnModelCreating` to specify that the `ShippingAddress` property is an Owned Entity of the `Order` entity type and to configure additional facets if needed.</span></span>

[!code-csharp[OwnsOne](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOne)]

<span data-ttu-id="bcb99-116">如果 `ShippingAddress` 屬性在類型中是私用的 `Order` ，您可以使用方法的字串版本 `OwnsOne` ：</span><span class="sxs-lookup"><span data-stu-id="bcb99-116">If the `ShippingAddress` property is private in the `Order` type, you can use the string version of the `OwnsOne` method:</span></span>

[!code-csharp[OwnsOneString](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneString)]

<span data-ttu-id="bcb99-117">請參閱 [完整的範例專案](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities) ，以取得更多內容。</span><span class="sxs-lookup"><span data-stu-id="bcb99-117">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities) for more context.</span></span>

> [!TIP]
> <span data-ttu-id="bcb99-118">擁有的實體類型可標示為必要，如需詳細資訊，請參閱 [必要的一對一相依項](xref:core/modeling/relationships#one-to-one) 。</span><span class="sxs-lookup"><span data-stu-id="bcb99-118">The owned entity type can be marked as required, see [Required one-to-one dependents](xref:core/modeling/relationships#one-to-one) for more information.</span></span>

## <a name="implicit-keys"></a><span data-ttu-id="bcb99-119">隱含索引鍵</span><span class="sxs-lookup"><span data-stu-id="bcb99-119">Implicit keys</span></span>

<span data-ttu-id="bcb99-120">`OwnsOne`透過參考導覽設定或探索的擁有型別，一律與擁有者有一對一的關聯性，因此它們不需要自己的索引鍵值，因為外鍵值是唯一的。</span><span class="sxs-lookup"><span data-stu-id="bcb99-120">Owned types configured with `OwnsOne` or discovered through a reference navigation always have a one-to-one relationship with the owner, therefore they don't need their own key values as the foreign key values are unique.</span></span> <span data-ttu-id="bcb99-121">在上述範例中， `StreetAddress` 類型不需要定義索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="bcb99-121">In the previous example, the `StreetAddress` type does not need to define a key property.</span></span>  

<span data-ttu-id="bcb99-122">為了瞭解 EF Core 如何追蹤這些物件，知道主鍵是建立為所擁有類型的 [陰影屬性](xref:core/modeling/shadow-properties) 會很有説明。</span><span class="sxs-lookup"><span data-stu-id="bcb99-122">In order to understand how EF Core tracks these objects, it is useful to know that a primary key is created as a [shadow property](xref:core/modeling/shadow-properties) for the owned type.</span></span> <span data-ttu-id="bcb99-123">擁有之類型實例的索引鍵值將會與擁有者實例之索引鍵的值相同。</span><span class="sxs-lookup"><span data-stu-id="bcb99-123">The value of the key of an instance of the owned type will be the same as the value of the key of the owner instance.</span></span>

## <a name="collections-of-owned-types"></a><span data-ttu-id="bcb99-124">擁有類型的集合</span><span class="sxs-lookup"><span data-stu-id="bcb99-124">Collections of owned types</span></span>

<span data-ttu-id="bcb99-125">若要設定所擁有類型的集合，請 `OwnsMany` 在中使用 `OnModelCreating` 。</span><span class="sxs-lookup"><span data-stu-id="bcb99-125">To configure a collection of owned types use `OwnsMany` in `OnModelCreating`.</span></span>

<span data-ttu-id="bcb99-126">擁有的類型需要主鍵。</span><span class="sxs-lookup"><span data-stu-id="bcb99-126">Owned types need a primary key.</span></span> <span data-ttu-id="bcb99-127">如果 .NET 類型上沒有適合的候選屬性，EF Core 可以嘗試建立一個。</span><span class="sxs-lookup"><span data-stu-id="bcb99-127">If there are no good candidates properties on the .NET type, EF Core can try to create one.</span></span> <span data-ttu-id="bcb99-128">不過，當擁有的型別是透過集合定義時，您只需要建立一個影子屬性，就可以同時作為擁有者的外鍵和所擁有實例的主鍵，就像我們所做的一樣 `OwnsOne` ：每個擁有者可以有多個自有的型別實例，因此擁有者的金鑰也不足以提供每個擁有實例的唯一身分識別。</span><span class="sxs-lookup"><span data-stu-id="bcb99-128">However, when owned types are defined through a collection, it isn't enough to just create a shadow property to act as both the foreign key into the owner and the primary key of the owned instance, as we do for `OwnsOne`: there can be multiple owned type instances for each owner, and hence the key of the owner isn't enough to provide a unique identity for each owned instance.</span></span>

<span data-ttu-id="bcb99-129">這兩個最簡單的解決方案是：</span><span class="sxs-lookup"><span data-stu-id="bcb99-129">The two most straightforward solutions to this are:</span></span>

- <span data-ttu-id="bcb99-130">在新屬性上定義代理主鍵，而此屬性與指向擁有者的外鍵無關。</span><span class="sxs-lookup"><span data-stu-id="bcb99-130">Defining a surrogate primary key on a new property independent of the foreign key that points to the owner.</span></span> <span data-ttu-id="bcb99-131">包含的值在所有擁有者中都必須是唯一的 (例如，如果父系 {1} 有子系 {1} ，則父系 {2} 不能有子 {1}) ，因此值沒有任何固有意義。</span><span class="sxs-lookup"><span data-stu-id="bcb99-131">The contained values would need to be unique across all owners (e.g. if Parent {1} has Child {1}, then Parent {2} cannot have Child {1}), so the value doesn't have any inherent meaning.</span></span> <span data-ttu-id="bcb99-132">因為外鍵不是主鍵的一部分，所以可以變更其值，因此您可以將子系從某個父系移至另一個父代，但這通常會針對匯總語義進行。</span><span class="sxs-lookup"><span data-stu-id="bcb99-132">Since the foreign key is not part of the primary key its values can be changed, so you could move a child from one parent to another one, however this usually goes against aggregate semantics.</span></span>
- <span data-ttu-id="bcb99-133">使用外鍵和其他屬性做為複合索引鍵。</span><span class="sxs-lookup"><span data-stu-id="bcb99-133">Using the foreign key and an additional property as a composite key.</span></span> <span data-ttu-id="bcb99-134">針對指定的父系，其他屬性值現在只需要是唯一的 (因此，如果父系 {1} 有子系， {1,1} 則父代 {2} 仍可以有子系 {2,1}) 。</span><span class="sxs-lookup"><span data-stu-id="bcb99-134">The additional property value now only needs to be unique for a given parent (so if Parent {1} has Child {1,1} then Parent {2} can still have Child {2,1}).</span></span> <span data-ttu-id="bcb99-135">藉由讓主鍵的外鍵部分成為擁有者與擁有之實體之間的關聯性，會變成不可變的，並更妥善地反映匯總語義。</span><span class="sxs-lookup"><span data-stu-id="bcb99-135">By making the foreign key part of the primary key the relationship between the owner and the owned entity becomes immutable and reflects aggregate semantics better.</span></span> <span data-ttu-id="bcb99-136">這是 EF Core 預設的情況。</span><span class="sxs-lookup"><span data-stu-id="bcb99-136">This is what EF Core does by default.</span></span>

<span data-ttu-id="bcb99-137">在此範例中，我們會使用 `Distributor` 類別。</span><span class="sxs-lookup"><span data-stu-id="bcb99-137">In this example we'll use the `Distributor` class.</span></span>

[!code-csharp[Distributor](../../../samples/core/Modeling/OwnedEntities/Distributor.cs?name=Distributor)]

<span data-ttu-id="bcb99-138">根據預設，透過導覽屬性參考的擁有類型所使用的主鍵會是 `ShippingCenters` `("DistributorId", "Id")` `"DistributorId"` FK，而 `"Id"` 是唯一的 `int` 值。</span><span class="sxs-lookup"><span data-stu-id="bcb99-138">By default the primary key used for the owned type referenced through the `ShippingCenters` navigation property will be `("DistributorId", "Id")` where `"DistributorId"` is the FK and `"Id"` is a unique `int` value.</span></span>

<span data-ttu-id="bcb99-139">設定不同的主要金鑰呼叫 `HasKey` 。</span><span class="sxs-lookup"><span data-stu-id="bcb99-139">To configure a different primary key call `HasKey`.</span></span>

[!code-csharp[OwnsMany](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsMany)]

## <a name="mapping-owned-types-with-table-splitting"></a><span data-ttu-id="bcb99-140">使用資料表分割來對應擁有的類型</span><span class="sxs-lookup"><span data-stu-id="bcb99-140">Mapping owned types with table splitting</span></span>

<span data-ttu-id="bcb99-141">使用關係資料庫時，根據預設，參考擁有的類型會對應至與擁有者相同的資料表。</span><span class="sxs-lookup"><span data-stu-id="bcb99-141">When using relational databases, by default reference owned types are mapped to the same table as the owner.</span></span> <span data-ttu-id="bcb99-142">這需要將資料表分割成兩個：某些資料行將用來儲存擁有者的資料，而某些資料行會用來儲存擁有實體的資料。</span><span class="sxs-lookup"><span data-stu-id="bcb99-142">This requires splitting the table in two: some columns will be used to store the data of the owner, and some columns will be used to store data of the owned entity.</span></span> <span data-ttu-id="bcb99-143">這是稱為 [資料表分割](xref:core/modeling/table-splitting)的常見功能。</span><span class="sxs-lookup"><span data-stu-id="bcb99-143">This is a common feature known as [table splitting](xref:core/modeling/table-splitting).</span></span>

<span data-ttu-id="bcb99-144">根據預設，EF Core 會在模式 _Navigation_OwnedEntityProperty_之後，為擁有的實體類型的屬性命名資料庫資料行。</span><span class="sxs-lookup"><span data-stu-id="bcb99-144">By default, EF Core will name the database columns for the properties of the owned entity type following the pattern _Navigation_OwnedEntityProperty_.</span></span> <span data-ttu-id="bcb99-145">因此， `StreetAddress` 屬性會出現在名稱為 ' ShippingAddress_Street ' 和 ' ShippingAddress_City ' 的 ' Orders ' 資料表中。</span><span class="sxs-lookup"><span data-stu-id="bcb99-145">Therefore the `StreetAddress` properties will appear in the 'Orders' table with the names 'ShippingAddress_Street' and 'ShippingAddress_City'.</span></span>

<span data-ttu-id="bcb99-146">您可以使用 `HasColumnName` 方法來重新命名這些資料行。</span><span class="sxs-lookup"><span data-stu-id="bcb99-146">You can use the `HasColumnName` method to rename those columns.</span></span>

[!code-csharp[ColumnNames](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=ColumnNames)]

> [!NOTE]
> <span data-ttu-id="bcb99-147">大部分的一般實體型別設定方法（例如 [Ignore](/dotnet/api/microsoft.entityframeworkcore.metadata.builders.ownednavigationbuilder.ignore) ）都可以相同的方式呼叫。</span><span class="sxs-lookup"><span data-stu-id="bcb99-147">Most of the normal entity type configuration methods like [Ignore](/dotnet/api/microsoft.entityframeworkcore.metadata.builders.ownednavigationbuilder.ignore) can be called in the same way.</span></span>

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a><span data-ttu-id="bcb99-148">在多個擁有的類型之間共用相同的 .NET 類型</span><span class="sxs-lookup"><span data-stu-id="bcb99-148">Sharing the same .NET type among multiple owned types</span></span>

<span data-ttu-id="bcb99-149">擁有的實體類型可以是與另一個擁有的實體類型相同的 .NET 型別，因此 .NET 類型可能不足以識別所擁有的類型。</span><span class="sxs-lookup"><span data-stu-id="bcb99-149">An owned entity type can be of the same .NET type as another owned entity type, therefore the .NET type may not be enough to identify an owned type.</span></span>

<span data-ttu-id="bcb99-150">在這些情況下，從擁有者指向擁有之實體的屬性會變成所擁有實體類型的 _定義導覽_ 。</span><span class="sxs-lookup"><span data-stu-id="bcb99-150">In those cases, the property pointing from the owner to the owned entity becomes the _defining navigation_ of the owned entity type.</span></span> <span data-ttu-id="bcb99-151">從 EF Core 的觀點來看，定義流覽是類型與 .NET 類型的身分識別的一部分。</span><span class="sxs-lookup"><span data-stu-id="bcb99-151">From the perspective of EF Core, the defining navigation is part of the type's identity alongside the .NET type.</span></span>

<span data-ttu-id="bcb99-152">例如，在下列類別中， `ShippingAddress` 和 `BillingAddress` 都是相同的 .net 型別 `StreetAddress` 。</span><span class="sxs-lookup"><span data-stu-id="bcb99-152">For example, in the following class `ShippingAddress` and `BillingAddress` are both of the same .NET type, `StreetAddress`.</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

<span data-ttu-id="bcb99-153">若要瞭解 EF Core 將如何區分這些物件的追蹤實例，請考慮定義導覽已成為實例索引鍵的一部分，以及擁有者的索引鍵和所擁有類型之 .NET 類型的值。</span><span class="sxs-lookup"><span data-stu-id="bcb99-153">In order to understand how EF Core will distinguish tracked instances of these objects, it may be useful to think that the defining navigation has become part of the key of the instance alongside the value of the key of the owner and the .NET type of the owned type.</span></span>

## <a name="nested-owned-types"></a><span data-ttu-id="bcb99-154">嵌套擁有的類型</span><span class="sxs-lookup"><span data-stu-id="bcb99-154">Nested owned types</span></span>

<span data-ttu-id="bcb99-155">在此範例 `OrderDetails` 中 `BillingAddress` `ShippingAddress` ，會擁有和，兩者都是 `StreetAddress` 類型。</span><span class="sxs-lookup"><span data-stu-id="bcb99-155">In this example `OrderDetails` owns `BillingAddress` and `ShippingAddress`, which are both `StreetAddress` types.</span></span> <span data-ttu-id="bcb99-156">然後 `DetailedOrder` 類型擁有 `OrderDetails`。</span><span class="sxs-lookup"><span data-stu-id="bcb99-156">Then `OrderDetails` is owned by the `DetailedOrder` type.</span></span>

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/OwnedEntities/DetailedOrder.cs?name=DetailedOrder)]

[!code-csharp[OrderStatus](../../../samples/core/Modeling/OwnedEntities/OrderStatus.cs?name=OrderStatus)]

<span data-ttu-id="bcb99-157">每次流覽至擁有的型別時，會定義具有完全獨立設定的個別實體類型。</span><span class="sxs-lookup"><span data-stu-id="bcb99-157">Each navigation to an owned type defines a separate entity type with completely independent configuration.</span></span>

<span data-ttu-id="bcb99-158">除了嵌套擁有的類型之外，擁有的類型也可以參考可以是擁有者或不同實體的一般實體，只要擁有的實體位於相依的端。</span><span class="sxs-lookup"><span data-stu-id="bcb99-158">In addition to nested owned types, an owned type can reference a regular entity which can be either the owner or a different entity as long as the owned entity is on the dependent side.</span></span> <span data-ttu-id="bcb99-159">這項功能會在 EF6 中的複雜類型之外設定擁有的實體類型。</span><span class="sxs-lookup"><span data-stu-id="bcb99-159">This capability sets owned entity types apart from complex types in EF6.</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

## <a name="configuring-owned-types"></a><span data-ttu-id="bcb99-160">設定自有類型</span><span class="sxs-lookup"><span data-stu-id="bcb99-160">Configuring Owned Types</span></span>

<span data-ttu-id="bcb99-161">您可以 `OwnsOne` 在流暢的呼叫中連結方法來設定此模型：</span><span class="sxs-lookup"><span data-stu-id="bcb99-161">It is possible to chain the `OwnsOne` method in a fluent call to configure this model:</span></span>

[!code-csharp[OwnsOneNested](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneNested)]

<span data-ttu-id="bcb99-162">請注意， `WithOwner` 用來定義在擁有者上指向之導覽屬性的呼叫。</span><span class="sxs-lookup"><span data-stu-id="bcb99-162">Notice the `WithOwner` call used to define the navigation property pointing back at the owner.</span></span> <span data-ttu-id="bcb99-163">若要定義不屬於擁有權關聯性的擁有者實體類型的流覽，則 `WithOwner()` 應該在沒有任何引數的情況下呼叫。</span><span class="sxs-lookup"><span data-stu-id="bcb99-163">To define a navigation to the owner entity type that's not part of the ownership relationship `WithOwner()` should be called without any arguments.</span></span>

<span data-ttu-id="bcb99-164">也可以使用和來達成此結果 `OwnedAttribute` `OrderDetails` `StreetAddress` 。</span><span class="sxs-lookup"><span data-stu-id="bcb99-164">It is also possible to achieve this result using `OwnedAttribute` on both `OrderDetails` and `StreetAddress`.</span></span>

<span data-ttu-id="bcb99-165">此外，請注意 `Navigation` 呼叫。</span><span class="sxs-lookup"><span data-stu-id="bcb99-165">In addition, notice the `Navigation` call.</span></span> <span data-ttu-id="bcb99-166">在 EFCore 5.0 中，您可以 [針對非擁有的導覽屬性](xref:core/modeling/relationships#configuring-navigation-properties)，進一步設定所擁有類型的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="bcb99-166">In EFCore 5.0, navigation properties to owned types can be further configured [as for non-owned navigation properties](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

## <a name="storing-owned-types-in-separate-tables"></a><span data-ttu-id="bcb99-167">將擁有的類型儲存在不同的資料表中</span><span class="sxs-lookup"><span data-stu-id="bcb99-167">Storing owned types in separate tables</span></span>

<span data-ttu-id="bcb99-168">與 EF6 複雜類型不同的是，擁有的類型可以儲存在擁有者的個別資料表中。</span><span class="sxs-lookup"><span data-stu-id="bcb99-168">Also unlike EF6 complex types, owned types can be stored in a separate table from the owner.</span></span> <span data-ttu-id="bcb99-169">為了覆寫將擁有的型別對應至與擁有者相同之資料表的慣例，您只要呼叫 `ToTable` 並提供不同的資料表名稱即可。</span><span class="sxs-lookup"><span data-stu-id="bcb99-169">In order to override the convention that maps an owned type to the same table as the owner, you can simply call `ToTable` and provide a different table name.</span></span> <span data-ttu-id="bcb99-170">下列範例會將 `OrderDetails` 它的兩個位址對應到不同的資料表， `DetailedOrder` 如下所示：</span><span class="sxs-lookup"><span data-stu-id="bcb99-170">The following example will map `OrderDetails` and its two addresses to a separate table from `DetailedOrder`:</span></span>

[!code-csharp[OwnsOneTable](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneTable)]

<span data-ttu-id="bcb99-171">您也可以使用 `TableAttribute` 來完成這項操作，但請注意，如果擁有的類型有多個導覽，則這會失敗，因為在這種情況下，多個實體類型會對應至相同的資料表。</span><span class="sxs-lookup"><span data-stu-id="bcb99-171">It is also possible to use the `TableAttribute` to accomplish this, but note that this would fail if there are multiple navigations to the owned type since in that case multiple entity types would be mapped to the same table.</span></span>

## <a name="querying-owned-types"></a><span data-ttu-id="bcb99-172">查詢擁有的類型</span><span class="sxs-lookup"><span data-stu-id="bcb99-172">Querying owned types</span></span>

<span data-ttu-id="bcb99-173">查詢擁有者時，預設會包含擁有的類型。</span><span class="sxs-lookup"><span data-stu-id="bcb99-173">When querying the owner the owned types will be included by default.</span></span> <span data-ttu-id="bcb99-174">`Include`即使擁有的類型儲存在不同的資料表中，也不需要使用方法。</span><span class="sxs-lookup"><span data-stu-id="bcb99-174">It is not necessary to use the `Include` method, even if the owned types are stored in a separate table.</span></span> <span data-ttu-id="bcb99-175">根據之前所述的模型，下列查詢將會取得 `Order` ， `OrderDetails` 以及資料庫所擁有的兩個 `StreetAddresses` ：</span><span class="sxs-lookup"><span data-stu-id="bcb99-175">Based on the model described before, the following query will get `Order`, `OrderDetails` and the two owned `StreetAddresses` from the database:</span></span>

[!code-csharp[DetailedOrderQuery](../../../samples/core/Modeling/OwnedEntities/Program.cs?name=DetailedOrderQuery)]

## <a name="limitations"></a><span data-ttu-id="bcb99-176">限制</span><span class="sxs-lookup"><span data-stu-id="bcb99-176">Limitations</span></span>

<span data-ttu-id="bcb99-177">其中有些限制是擁有的實體類型運作方式的基礎，但有些則是我們可能會在未來版本中移除的限制：</span><span class="sxs-lookup"><span data-stu-id="bcb99-177">Some of these limitations are fundamental to how owned entity types work, but some others are restrictions that we may be able to remove in future releases:</span></span>

### <a name="by-design-restrictions"></a><span data-ttu-id="bcb99-178">依設計限制</span><span class="sxs-lookup"><span data-stu-id="bcb99-178">By-design restrictions</span></span>

- <span data-ttu-id="bcb99-179">您無法 `DbSet<T>` 為擁有的類型建立。</span><span class="sxs-lookup"><span data-stu-id="bcb99-179">You cannot create a `DbSet<T>` for an owned type.</span></span>
- <span data-ttu-id="bcb99-180">您無法 `Entity<T>()` 在上使用自有的型別呼叫 `ModelBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="bcb99-180">You cannot call `Entity<T>()` with an owned type on `ModelBuilder`.</span></span>
- <span data-ttu-id="bcb99-181">多個擁有者無法共用擁有的實體類型實例 (這是已知的值物件案例，無法使用所擁有的實體類型) 來執行。</span><span class="sxs-lookup"><span data-stu-id="bcb99-181">Instances of owned entity types cannot be shared by multiple owners (this is a well-known scenario for value objects that cannot be implemented using owned entity types).</span></span>

### <a name="current-shortcomings"></a><span data-ttu-id="bcb99-182">目前的缺點</span><span class="sxs-lookup"><span data-stu-id="bcb99-182">Current shortcomings</span></span>

- <span data-ttu-id="bcb99-183">擁有的實體類型不能有繼承階層</span><span class="sxs-lookup"><span data-stu-id="bcb99-183">Owned entity types cannot have inheritance hierarchies</span></span>

### <a name="shortcomings-in-previous-versions"></a><span data-ttu-id="bcb99-184">舊版的缺點</span><span class="sxs-lookup"><span data-stu-id="bcb99-184">Shortcomings in previous versions</span></span>

- <span data-ttu-id="bcb99-185">在 EF Core 2.x 參考導覽至擁有的實體類型時，除非它們明確地對應至擁有者的個別資料表，否則不能是 null。</span><span class="sxs-lookup"><span data-stu-id="bcb99-185">In EF Core 2.x reference navigations to owned entity types cannot be null unless they are explicitly mapped to a separate table from the owner.</span></span>
- <span data-ttu-id="bcb99-186">在 EF Core 3.x 中，對應至與擁有者相同之資料表的擁有實體類型資料行一律會標示為可為 null。</span><span class="sxs-lookup"><span data-stu-id="bcb99-186">In EF Core 3.x the columns for owned entity types mapped to the same table as the owner are always marked as nullable.</span></span>
