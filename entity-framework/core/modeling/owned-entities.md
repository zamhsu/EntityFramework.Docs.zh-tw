---
title: 擁有的實體類型-EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 02/26/2018
ms.assetid: 2B0BADCE-E23E-4B28-B8EE-537883E16DF3
uid: core/modeling/owned-entities
ms.openlocfilehash: a0665bfa27134b8dc3eba854ff3f7b1af4b69217
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655928"
---
# <a name="owned-entity-types"></a><span data-ttu-id="5df3c-102">擁有的實體類型</span><span class="sxs-lookup"><span data-stu-id="5df3c-102">Owned Entity Types</span></span>

> [!NOTE]
> <span data-ttu-id="5df3c-103">這項功能在 EF Core 2.0 中是新的。</span><span class="sxs-lookup"><span data-stu-id="5df3c-103">This feature is new in EF Core 2.0.</span></span>

<span data-ttu-id="5df3c-104">EF Core 可讓您將只能出現在其他實體類型之導覽屬性的實體類型模型化。</span><span class="sxs-lookup"><span data-stu-id="5df3c-104">EF Core allows you to model entity types that can only ever appear on navigation properties of other entity types.</span></span> <span data-ttu-id="5df3c-105">這些稱為「_自有實體類型_」。</span><span class="sxs-lookup"><span data-stu-id="5df3c-105">These are called _owned entity types_.</span></span> <span data-ttu-id="5df3c-106">包含自有實體類型的實體是_其擁有者。_</span><span class="sxs-lookup"><span data-stu-id="5df3c-106">The entity containing an owned entity type is its _owner_.</span></span>

<span data-ttu-id="5df3c-107">擁有的實體基本上是擁有者的一部分，如果沒有，就不能存在，它們在概念上類似于[匯總](https://martinfowler.com/bliki/DDD_Aggregate.html)。</span><span class="sxs-lookup"><span data-stu-id="5df3c-107">Owned entities are essentially a part of the owner and cannot exist without it, they are conceptually similar to [aggregates](https://martinfowler.com/bliki/DDD_Aggregate.html).</span></span>

## <a name="explicit-configuration"></a><span data-ttu-id="5df3c-108">明確設定</span><span class="sxs-lookup"><span data-stu-id="5df3c-108">Explicit configuration</span></span>

<span data-ttu-id="5df3c-109">模型中的 EF Core 絕對不會包含擁有的實體類型。</span><span class="sxs-lookup"><span data-stu-id="5df3c-109">Owned entity types are never included by EF Core in the model by convention.</span></span> <span data-ttu-id="5df3c-110">您可以使用 `OnModelCreating` 中的 `OwnsOne` 方法，或以 `OwnedAttribute` （EF Core 2.1 中的新功能）標注類型，將類型設定為自有類型。</span><span class="sxs-lookup"><span data-stu-id="5df3c-110">You can use the `OwnsOne` method in `OnModelCreating` or annotate the type with `OwnedAttribute` (new in EF Core 2.1) to configure the type as an owned type.</span></span>

<span data-ttu-id="5df3c-111">在此範例中，`StreetAddress` 是不含識別屬性的類型。</span><span class="sxs-lookup"><span data-stu-id="5df3c-111">In this example, `StreetAddress` is a type with no identity property.</span></span> <span data-ttu-id="5df3c-112">它用做訂單類型的屬性，指定特定訂單的送貨地址。</span><span class="sxs-lookup"><span data-stu-id="5df3c-112">It is used as a property of the Order type to specify the shipping address for a particular order.</span></span>

<span data-ttu-id="5df3c-113">從另一個實體類型參考時，我們可以使用 `OwnedAttribute` 將它視為擁有的實體：</span><span class="sxs-lookup"><span data-stu-id="5df3c-113">We can use the `OwnedAttribute` to treat it as an owned entity when referenced from another entity type:</span></span>

[!code-csharp[StreetAddress](../../../samples/core/Modeling/OwnedEntities/StreetAddress.cs?name=StreetAddress)]

[!code-csharp[Order](../../../samples/core/Modeling/OwnedEntities/Order.cs?name=Order)]

<span data-ttu-id="5df3c-114">您也可以使用 `OnModelCreating` 中的 `OwnsOne` 方法，將 `ShippingAddress` 屬性指定為 `Order` 實體類型的擁有實體，並視需要設定其他 facet。</span><span class="sxs-lookup"><span data-stu-id="5df3c-114">It is also possible to use the `OwnsOne` method in `OnModelCreating` to specify that the `ShippingAddress` property is an Owned Entity of the `Order` entity type and to configure additional facets if needed.</span></span>

[!code-csharp[OwnsOne](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOne)]

<span data-ttu-id="5df3c-115">如果 `ShippingAddress` 屬性是 `Order` 類型中的私用，您可以使用 `OwnsOne` 方法的字串版本：</span><span class="sxs-lookup"><span data-stu-id="5df3c-115">If the `ShippingAddress` property is private in the `Order` type, you can use the string version of the `OwnsOne` method:</span></span>

[!code-csharp[OwnsOneString](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneString)]

<span data-ttu-id="5df3c-116">如需詳細內容，請參閱[完整的範例專案](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities)。</span><span class="sxs-lookup"><span data-stu-id="5df3c-116">See the [full sample project](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities) for more context.</span></span>

## <a name="implicit-keys"></a><span data-ttu-id="5df3c-117">隱含索引鍵</span><span class="sxs-lookup"><span data-stu-id="5df3c-117">Implicit keys</span></span>

<span data-ttu-id="5df3c-118">擁有以 `OwnsOne` 或透過參考導覽探索到的類型，一律與擁有者具有一對一的關聯性，因此，它們不需要自己的索引鍵值，因為外鍵值是唯一的。</span><span class="sxs-lookup"><span data-stu-id="5df3c-118">Owned types configured with `OwnsOne` or discovered through a reference navigation always have a one-to-one relationship with the owner, therefore they don't need their own key values as the foreign key values are unique.</span></span> <span data-ttu-id="5df3c-119">在上述範例中，`StreetAddress` 型別不需要定義索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="5df3c-119">In the previous example, the `StreetAddress` type does not need to define a key property.</span></span>  

<span data-ttu-id="5df3c-120">若要瞭解 EF Core 如何追蹤這些物件，您可以知道主要索引鍵是建立為所擁有類型的[陰影屬性](xref:core/modeling/shadow-properties)。</span><span class="sxs-lookup"><span data-stu-id="5df3c-120">In order to understand how EF Core tracks these objects, it is useful to know that a primary key is created as a [shadow property](xref:core/modeling/shadow-properties) for the owned type.</span></span> <span data-ttu-id="5df3c-121">擁有的類型之實例的索引鍵值，會與擁有者實例的索引鍵值相同。</span><span class="sxs-lookup"><span data-stu-id="5df3c-121">The value of the key of an instance of the owned type will be the same as the value of the key of the owner instance.</span></span>

## <a name="collections-of-owned-types"></a><span data-ttu-id="5df3c-122">擁有類型的集合</span><span class="sxs-lookup"><span data-stu-id="5df3c-122">Collections of owned types</span></span>

> [!NOTE]
> <span data-ttu-id="5df3c-123">此為 EF Core 2.2 中的新功能。</span><span class="sxs-lookup"><span data-stu-id="5df3c-123">This feature is new in EF Core 2.2.</span></span>

<span data-ttu-id="5df3c-124">若要設定擁有類型的集合，請使用 `OnModelCreating`中的 `OwnsMany`。</span><span class="sxs-lookup"><span data-stu-id="5df3c-124">To configure a collection of owned types use `OwnsMany` in `OnModelCreating`.</span></span>

<span data-ttu-id="5df3c-125">擁有的類型需要主要金鑰。</span><span class="sxs-lookup"><span data-stu-id="5df3c-125">Owned types need a primary key.</span></span> <span data-ttu-id="5df3c-126">如果 .NET 類型上沒有良好的候選項目屬性，EF Core 可以嘗試建立一個。</span><span class="sxs-lookup"><span data-stu-id="5df3c-126">If there are no good candidates properties on the .NET type, EF Core can try to create one.</span></span> <span data-ttu-id="5df3c-127">不過，當擁有的型別是透過集合來定義時，只是建立一個陰影屬性，同時做為擁有者的外鍵和所擁有之實例的主鍵，如同 `OwnsOne`：每個都可以有多個擁有的型別實例。擁有者，因此擁有者的金鑰不足，無法為每個擁有的實例提供唯一的身分識別。</span><span class="sxs-lookup"><span data-stu-id="5df3c-127">However, when owned types are defined through a collection, it isn't enough to just create a shadow property to act as both the foreign key into the owner and the primary key of the owned instance, as we do for `OwnsOne`: there can be multiple owned type instances for each owner, and hence the key of the owner isn't enough to provide a unique identity for each owned instance.</span></span>

<span data-ttu-id="5df3c-128">這兩個最直接的解決辦法是：</span><span class="sxs-lookup"><span data-stu-id="5df3c-128">The two most straightforward solutions to this are:</span></span>

- <span data-ttu-id="5df3c-129">在與指向擁有者的外鍵無關的新屬性上定義代理主鍵。</span><span class="sxs-lookup"><span data-stu-id="5df3c-129">Defining a surrogate primary key on a new property independent of the foreign key that points to the owner.</span></span> <span data-ttu-id="5df3c-130">包含的值在所有擁有者中都必須是唯一的（例如，如果父 {1} 具有子 {1}，則父 {2} 不能有子 {1}），因此值不會有任何固有的意義。</span><span class="sxs-lookup"><span data-stu-id="5df3c-130">The contained values would need to be unique across all owners (e.g. if Parent {1} has Child {1}, then Parent {2} cannot have Child {1}), so the value doesn't have any inherent meaning.</span></span> <span data-ttu-id="5df3c-131">因為外鍵不是主鍵的一部分，所以可以變更其值，因此您可以將子系從一個父系移到另一個父代，但這通常會針對匯總的語義進行。</span><span class="sxs-lookup"><span data-stu-id="5df3c-131">Since the foreign key is not part of the primary key its values can be changed, so you could move a child from one parent to another one, however this usually goes against aggregate semantics.</span></span>
- <span data-ttu-id="5df3c-132">使用外鍵和其他屬性做為複合索引鍵。</span><span class="sxs-lookup"><span data-stu-id="5df3c-132">Using the foreign key and an additional property as a composite key.</span></span> <span data-ttu-id="5df3c-133">對於指定的父系，其他屬性值現在只需要是唯一的（因此，如果父 {1} 具有子 {1,1}，父系 {2} 仍然可以有子 {2,1}）。</span><span class="sxs-lookup"><span data-stu-id="5df3c-133">The additional property value now only needs to be unique for a given parent (so if Parent {1} has Child {1,1} then Parent {2} can still have Child {2,1}).</span></span> <span data-ttu-id="5df3c-134">藉由讓主鍵的外鍵部分成為主要金鑰的一部分，擁有者與擁有的實體之間的關聯性就會變成不可變，並更清楚反映匯總的語法。</span><span class="sxs-lookup"><span data-stu-id="5df3c-134">By making the foreign key part of the primary key the relationship between the owner and the owned entity becomes immutable and reflects aggregate semantics better.</span></span> <span data-ttu-id="5df3c-135">這就是 EF Core 預設執行的工作。</span><span class="sxs-lookup"><span data-stu-id="5df3c-135">This is what EF Core does by default.</span></span>

<span data-ttu-id="5df3c-136">在此範例中，我們將使用 `Distributor` 類別：</span><span class="sxs-lookup"><span data-stu-id="5df3c-136">In this example we'll use the `Distributor` class:</span></span>

[!code-csharp[Distributor](../../../samples/core/Modeling/OwnedEntities/Distributor.cs?name=Distributor)]

<span data-ttu-id="5df3c-137">根據預設，透過 `ShippingCenters` 導覽屬性所參考之自有型別的主要金鑰將會 `("DistributorId", "Id")`，其中 `"DistributorId"` 是 FK，而 `"Id"` 是唯一的 `int` 值。</span><span class="sxs-lookup"><span data-stu-id="5df3c-137">By default the primary key used for the owned type referenced through the `ShippingCenters` navigation property will be `("DistributorId", "Id")` where `"DistributorId"` is the FK and `"Id"` is a unique `int` value.</span></span>

<span data-ttu-id="5df3c-138">若要設定不同的 PK 呼叫 `HasKey`：</span><span class="sxs-lookup"><span data-stu-id="5df3c-138">To configure a different PK call `HasKey`:</span></span>

[!code-csharp[OwnsMany](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsMany)]

> [!NOTE]
> <span data-ttu-id="5df3c-139">在 EF Core 3.0 之前 `WithOwner()` 方法不存在，因此應該移除此呼叫。</span><span class="sxs-lookup"><span data-stu-id="5df3c-139">Before EF Core 3.0 `WithOwner()` method didn't exist so this call should be removed.</span></span>

## <a name="mapping-owned-types-with-table-splitting"></a><span data-ttu-id="5df3c-140">對應具有資料表分割的擁有類型</span><span class="sxs-lookup"><span data-stu-id="5df3c-140">Mapping owned types with table splitting</span></span>

<span data-ttu-id="5df3c-141">使用關係資料庫時，根據預設，參考擁有的類型會對應至與擁有者相同的資料表。</span><span class="sxs-lookup"><span data-stu-id="5df3c-141">When using relational databases, by default reference owned types are mapped to the same table as the owner.</span></span> <span data-ttu-id="5df3c-142">這需要將資料表分割為兩個：某些資料行將用來儲存擁有者的資料，而有些資料行則用來儲存擁有之實體的資料。</span><span class="sxs-lookup"><span data-stu-id="5df3c-142">This requires splitting the table in two: some columns will be used to store the data of the owner, and some columns will be used to store data of the owned entity.</span></span> <span data-ttu-id="5df3c-143">這是稱為「[資料表分割](table-splitting.md)」的常見功能。</span><span class="sxs-lookup"><span data-stu-id="5df3c-143">This is a common feature known as [table splitting](table-splitting.md).</span></span>

<span data-ttu-id="5df3c-144">根據預設，EF Core 會在模式_Navigation_OwnedEntityProperty_後面，為所擁有實體類型的屬性命名資料庫資料行。</span><span class="sxs-lookup"><span data-stu-id="5df3c-144">By default, EF Core will name the database columns for the properties of the owned entity type following the pattern _Navigation_OwnedEntityProperty_.</span></span> <span data-ttu-id="5df3c-145">因此，`StreetAddress` 屬性會出現在名為 ' ShippingAddress_Street ' 和 ' ShippingAddress_City ' 的 ' Orders ' 資料表中。</span><span class="sxs-lookup"><span data-stu-id="5df3c-145">Therefore the `StreetAddress` properties will appear in the 'Orders' table with the names 'ShippingAddress_Street' and 'ShippingAddress_City'.</span></span>

<span data-ttu-id="5df3c-146">您可以使用 `HasColumnName` 方法來重新命名這些資料行：</span><span class="sxs-lookup"><span data-stu-id="5df3c-146">You can use the `HasColumnName` method to rename those columns:</span></span>

[!code-csharp[ColumnNames](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=ColumnNames)]

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a><span data-ttu-id="5df3c-147">在多個擁有的類型之間共用相同的 .NET 類型</span><span class="sxs-lookup"><span data-stu-id="5df3c-147">Sharing the same .NET type among multiple owned types</span></span>

<span data-ttu-id="5df3c-148">擁有的實體類型可以與另一個擁有的實體類型屬於相同的 .NET 類型，因此 .NET 類型可能不足以識別擁有的類型。</span><span class="sxs-lookup"><span data-stu-id="5df3c-148">An owned entity type can be of the same .NET type as another owned entity type, therefore the .NET type may not be enough to identify an owned type.</span></span>

<span data-ttu-id="5df3c-149">在這些情況下，從擁有者指向自有實體的屬性會變成所擁有實體類型的_定義導覽_。</span><span class="sxs-lookup"><span data-stu-id="5df3c-149">In those cases, the property pointing from the owner to the owned entity becomes the _defining navigation_ of the owned entity type.</span></span> <span data-ttu-id="5df3c-150">從 EF Core 的觀點來看，定義的導覽會與 .NET 類型一起屬於類型的身分識別。</span><span class="sxs-lookup"><span data-stu-id="5df3c-150">From the perspective of EF Core, the defining navigation is part of the type's identity alongside the .NET type.</span></span>

<span data-ttu-id="5df3c-151">例如，在下列類別中 `ShippingAddress` 和 `BillingAddress` 都是相同的 .NET 類型，`StreetAddress`：</span><span class="sxs-lookup"><span data-stu-id="5df3c-151">For example, in the following class `ShippingAddress` and `BillingAddress` are both of the same .NET type, `StreetAddress`:</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

<span data-ttu-id="5df3c-152">若要瞭解 EF Core 如何區分這些物件的追蹤實例，請將定義的導覽與擁有者的索引鍵值以及擁有之類型的 .NET 類型做為實例的一部分，可能會很有用。</span><span class="sxs-lookup"><span data-stu-id="5df3c-152">In order to understand how EF Core will distinguish tracked instances of these objects, it may be useful to think that the defining navigation has become part of the key of the instance alongside the value of the key of the owner and the .NET type of the owned type.</span></span>

## <a name="nested-owned-types"></a><span data-ttu-id="5df3c-153">嵌套擁有的類型</span><span class="sxs-lookup"><span data-stu-id="5df3c-153">Nested owned types</span></span>

<span data-ttu-id="5df3c-154">在此範例中 `OrderDetails` 擁有 `BillingAddress` 和 `ShippingAddress`，這兩者都是 `StreetAddress` 類型。</span><span class="sxs-lookup"><span data-stu-id="5df3c-154">In this example `OrderDetails` owns `BillingAddress` and `ShippingAddress`, which are both `StreetAddress` types.</span></span> <span data-ttu-id="5df3c-155">然後 `DetailedOrder` 類型擁有 `OrderDetails`。</span><span class="sxs-lookup"><span data-stu-id="5df3c-155">Then `OrderDetails` is owned by the `DetailedOrder` type.</span></span>

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/OwnedEntities/DetailedOrder.cs?name=DetailedOrder)]

[!code-csharp[OrderStatus](../../../samples/core/Modeling/OwnedEntities/OrderStatus.cs?name=OrderStatus)]

<span data-ttu-id="5df3c-156">除了嵌套的擁有類型之外，擁有的類型也可以參考一般實體，只要擁有的實體位於相依端，就可以是擁有者或不同的實體。</span><span class="sxs-lookup"><span data-stu-id="5df3c-156">In addition to nested owned types, an owned type can reference a regular entity, it can be either the owner or a different entity as long as the owned entity is on the dependent side.</span></span> <span data-ttu-id="5df3c-157">這項功能會在 EF6 中設定除了複雜類型之外的自有實體類型。</span><span class="sxs-lookup"><span data-stu-id="5df3c-157">This capability sets owned entity types apart from complex types in EF6.</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

<span data-ttu-id="5df3c-158">您可以在流暢的呼叫中將 `OwnsOne` 方法連鎖，以設定此模型：</span><span class="sxs-lookup"><span data-stu-id="5df3c-158">It is possible to chain the `OwnsOne` method in a fluent call to configure this model:</span></span>

[!code-csharp[OwnsOneNested](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneNested)]

<span data-ttu-id="5df3c-159">請注意，用來設定在擁有者上指向之導覽屬性的 `WithOwner` 呼叫。</span><span class="sxs-lookup"><span data-stu-id="5df3c-159">Notice the `WithOwner` call used to configure the navigation property pointing back at the owner.</span></span>

<span data-ttu-id="5df3c-160">您可以使用 `OrderDetails` 和 `StreetAdress`上的 `OwnedAttribute` 來達到結果。</span><span class="sxs-lookup"><span data-stu-id="5df3c-160">It is possible to achieve the result using `OwnedAttribute` on both `OrderDetails` and `StreetAdress`.</span></span>

## <a name="storing-owned-types-in-separate-tables"></a><span data-ttu-id="5df3c-161">將擁有的類型儲存在不同的資料表中</span><span class="sxs-lookup"><span data-stu-id="5df3c-161">Storing owned types in separate tables</span></span>

<span data-ttu-id="5df3c-162">也不同于 EF6 複雜類型，擁有的類型可以儲存在擁有者的個別資料表中。</span><span class="sxs-lookup"><span data-stu-id="5df3c-162">Also unlike EF6 complex types, owned types can be stored in a separate table from the owner.</span></span> <span data-ttu-id="5df3c-163">若要覆寫將擁有的類型對應到與擁有者相同之資料表的慣例，您只要呼叫 `ToTable` 並提供不同的資料表名稱即可。</span><span class="sxs-lookup"><span data-stu-id="5df3c-163">In order to override the convention that maps an owned type to the same table as the owner, you can simply call `ToTable` and provide a different table name.</span></span> <span data-ttu-id="5df3c-164">下列範例會將 `OrderDetails` 和它的兩個位址對應到 `DetailedOrder`的個別資料表：</span><span class="sxs-lookup"><span data-stu-id="5df3c-164">The following example will map `OrderDetails` and its two addresses to a separate table from `DetailedOrder`:</span></span>

[!code-csharp[OwnsOneTable](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneTable)]

## <a name="querying-owned-types"></a><span data-ttu-id="5df3c-165">查詢擁有的類型</span><span class="sxs-lookup"><span data-stu-id="5df3c-165">Querying owned types</span></span>

<span data-ttu-id="5df3c-166">查詢擁有者時，預設會包含擁有的類型。</span><span class="sxs-lookup"><span data-stu-id="5df3c-166">When querying the owner the owned types will be included by default.</span></span> <span data-ttu-id="5df3c-167">即使自有的類型儲存在不同的資料表中，也不需要使用 `Include` 方法。</span><span class="sxs-lookup"><span data-stu-id="5df3c-167">It is not necessary to use the `Include` method, even if the owned types are stored in a separate table.</span></span> <span data-ttu-id="5df3c-168">根據先前所述的模型，下列查詢會從資料庫取得 `Order`、`OrderDetails` 和兩個擁有的 `StreetAddresses`：</span><span class="sxs-lookup"><span data-stu-id="5df3c-168">Based on the model described before, the following query will get `Order`, `OrderDetails` and the two owned `StreetAddresses` from the database:</span></span>

[!code-csharp[DetailedOrderQuery](../../../samples/core/Modeling/OwnedEntities/Program.cs?name=DetailedOrderQuery)]

## <a name="limitations"></a><span data-ttu-id="5df3c-169">限制</span><span class="sxs-lookup"><span data-stu-id="5df3c-169">Limitations</span></span>

<span data-ttu-id="5df3c-170">其中一些限制是擁有的實體類型如何工作的基礎，但有些則是我們可以在未來版本中移除的限制：</span><span class="sxs-lookup"><span data-stu-id="5df3c-170">Some of these limitations are fundamental to how owned entity types work, but some others are restrictions that we may be able to remove in future releases:</span></span>

### <a name="by-design-restrictions"></a><span data-ttu-id="5df3c-171">依設計的限制</span><span class="sxs-lookup"><span data-stu-id="5df3c-171">By-design restrictions</span></span>

- <span data-ttu-id="5df3c-172">您無法為擁有的類型建立 `DbSet<T>`</span><span class="sxs-lookup"><span data-stu-id="5df3c-172">You cannot create a `DbSet<T>` for an owned type</span></span>
- <span data-ttu-id="5df3c-173">您無法在 `ModelBuilder` 上使用擁有的類型來呼叫 `Entity<T>()`</span><span class="sxs-lookup"><span data-stu-id="5df3c-173">You cannot call `Entity<T>()` with an owned type on `ModelBuilder`</span></span>

### <a name="current-shortcomings"></a><span data-ttu-id="5df3c-174">目前的缺點</span><span class="sxs-lookup"><span data-stu-id="5df3c-174">Current shortcomings</span></span>

- <span data-ttu-id="5df3c-175">不支援包含所擁有之實體類型的繼承階層</span><span class="sxs-lookup"><span data-stu-id="5df3c-175">Inheritance hierarchies that include owned entity types are not supported</span></span>
- <span data-ttu-id="5df3c-176">擁有的實體類型的參考導覽不可以是 null，除非它們已明確對應至擁有者的個別資料表</span><span class="sxs-lookup"><span data-stu-id="5df3c-176">Reference navigations to owned entity types cannot be null unless they are explicitly mapped to a separate table from the owner</span></span>
- <span data-ttu-id="5df3c-177">擁有的實體類型實例無法由多個擁有者共用（這是已知的值物件的情況，無法使用自有的實體類型來執行）</span><span class="sxs-lookup"><span data-stu-id="5df3c-177">Instances of owned entity types cannot be shared by multiple owners (this is a well-known scenario for value objects that cannot be implemented using owned entity types)</span></span>

### <a name="shortcomings-in-previous-versions"></a><span data-ttu-id="5df3c-178">舊版的缺點</span><span class="sxs-lookup"><span data-stu-id="5df3c-178">Shortcomings in previous versions</span></span>

- <span data-ttu-id="5df3c-179">在 EF Core 2.0 中，除非擁有的實體明確對應至擁有者階層中的個別資料表，否則無法在衍生實體類型中宣告所擁有之實體類型的導覽。</span><span class="sxs-lookup"><span data-stu-id="5df3c-179">In EF Core 2.0, navigations to owned entity types cannot be declared in derived entity types unless the owned entities are explicitly mapped to a separate table from the owner hierarchy.</span></span> <span data-ttu-id="5df3c-180">EF Core 2.1 已移除這項限制</span><span class="sxs-lookup"><span data-stu-id="5df3c-180">This limitation has been removed in EF Core 2.1</span></span>
- <span data-ttu-id="5df3c-181">在 EF Core 2.0 和2.1 中，僅支援擁有類型的參考導覽。</span><span class="sxs-lookup"><span data-stu-id="5df3c-181">In EF Core 2.0 and 2.1 only reference navigations to owned types were supported.</span></span> <span data-ttu-id="5df3c-182">EF Core 2.2 已移除這項限制</span><span class="sxs-lookup"><span data-stu-id="5df3c-182">This limitation has been removed in EF Core 2.2</span></span>
