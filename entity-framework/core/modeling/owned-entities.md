---
title: EF Core 擁有實體型別：
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 02/26/2018
ms.assetid: 2B0BADCE-E23E-4B28-B8EE-537883E16DF3
uid: core/modeling/owned-entities
ms.openlocfilehash: b2d72b08de79939904bf4e726c695440c906a8aa
ms.sourcegitcommit: 7bde8e6ad3c4565a4638646ce04bcf5e66f7b5fd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2019
ms.locfileid: "54069200"
---
# <a name="owned-entity-types"></a><span data-ttu-id="c45ce-102">擁有的實體類型</span><span class="sxs-lookup"><span data-stu-id="c45ce-102">Owned Entity Types</span></span>

>[!NOTE]
> <span data-ttu-id="c45ce-103">這項功能是在 EF Core 2.0 的新功能。</span><span class="sxs-lookup"><span data-stu-id="c45ce-103">This feature is new in EF Core 2.0.</span></span>

<span data-ttu-id="c45ce-104">EF Core 可讓您可以永遠只會出現在導覽屬性的其他實體類型的模型實體類型。</span><span class="sxs-lookup"><span data-stu-id="c45ce-104">EF Core allows you to model entity types that can only ever appear on navigation properties of other entity types.</span></span> <span data-ttu-id="c45ce-105">這些稱為_擁有的實體類型_。</span><span class="sxs-lookup"><span data-stu-id="c45ce-105">These are called _owned entity types_.</span></span> <span data-ttu-id="c45ce-106">包含擁有的實體類型的實體是其_擁有者_。</span><span class="sxs-lookup"><span data-stu-id="c45ce-106">The entity containing an owned entity type is its _owner_.</span></span>

## <a name="explicit-configuration"></a><span data-ttu-id="c45ce-107">明確的設定</span><span class="sxs-lookup"><span data-stu-id="c45ce-107">Explicit configuration</span></span>

<span data-ttu-id="c45ce-108">擁有的實體類型永遠不會包含 EF Core 的模型中的慣例。</span><span class="sxs-lookup"><span data-stu-id="c45ce-108">Owned entity types are never included by EF Core in the model by convention.</span></span> <span data-ttu-id="c45ce-109">您可以使用`OwnsOne`方法中的`OnModelCreating`或加上註解具有型別`OwnedAttribute`（EF Core 2.1 的新） 來設定為擁有的型別類型。</span><span class="sxs-lookup"><span data-stu-id="c45ce-109">You can use the `OwnsOne` method in `OnModelCreating` or annotate the type with `OwnedAttribute` (new in EF Core 2.1) to configure the type as an owned type.</span></span>

<span data-ttu-id="c45ce-110">在此範例中，`StreetAddress`是任何身分識別屬性的類型。</span><span class="sxs-lookup"><span data-stu-id="c45ce-110">In this example, `StreetAddress` is a type with no identity property.</span></span> <span data-ttu-id="c45ce-111">它用做訂單類型的屬性，指定特定訂單的送貨地址。</span><span class="sxs-lookup"><span data-stu-id="c45ce-111">It is used as a property of the Order type to specify the shipping address for a particular order.</span></span>

<span data-ttu-id="c45ce-112">我們可以使用`OwnedAttribute`以將其視為擁有的實體時從另一個實體類型參考：</span><span class="sxs-lookup"><span data-stu-id="c45ce-112">We can use the `OwnedAttribute` to treat it as an owned entity when referenced from another entity type:</span></span>

[!code-csharp[StreetAddress](../../../samples/core/Modeling/OwnedEntities/StreetAddress.cs?name=StreetAddress)]

[!code-csharp[Order](../../../samples/core/Modeling/OwnedEntities/Order.cs?name=Order)]

<span data-ttu-id="c45ce-113">也可以使用`OwnsOne`方法中的`OnModelCreating`來指定`ShippingAddress`屬性是擁有實體`Order`實體類型，並視需要設定其他 facet。</span><span class="sxs-lookup"><span data-stu-id="c45ce-113">It is also possible to use the `OwnsOne` method in `OnModelCreating` to specify that the `ShippingAddress` property is an Owned Entity of the `Order` entity type and to configure additional facets if needed.</span></span>

[!code-csharp[OwnsOne](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOne)]

<span data-ttu-id="c45ce-114">如果`ShippingAddress`屬性是在私人`Order`類型，您可以使用的字串版本`OwnsOne`方法：</span><span class="sxs-lookup"><span data-stu-id="c45ce-114">If the `ShippingAddress` property is private in the `Order` type, you can use the string version of the `OwnsOne` method:</span></span>

[!code-csharp[OwnsOneString](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneString)]

<span data-ttu-id="c45ce-115">請參閱[完整的範例專案](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities)詳細內容。</span><span class="sxs-lookup"><span data-stu-id="c45ce-115">See the [full sample project](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities) for more context.</span></span> 

## <a name="implicit-keys"></a><span data-ttu-id="c45ce-116">隱含索引鍵</span><span class="sxs-lookup"><span data-stu-id="c45ce-116">Implicit keys</span></span>

<span data-ttu-id="c45ce-117">擁有的類型設定`OwnsOne`或透過參考的瀏覽探索到一定有一對一的關聯性和擁有者，因此它們不需要自己金鑰的值因為外部索引鍵值，都是唯一。</span><span class="sxs-lookup"><span data-stu-id="c45ce-117">Owned types configured with `OwnsOne` or discovered through a reference navigation always have a one-to-one relationship with the owner, therefore they don't need their own key values as the foreign key values are unique.</span></span> <span data-ttu-id="c45ce-118">在上述範例中，`StreetAddress`型別不需要定義索引鍵的屬性。</span><span class="sxs-lookup"><span data-stu-id="c45ce-118">In the previous example, the `StreetAddress` type does not need to define a key property.</span></span>  

<span data-ttu-id="c45ce-119">若要了解如何 EF Core 會追蹤這些物件，最好考慮主索引鍵會建立為[陰影屬性](xref:core/modeling/shadow-properties)擁有的類型。</span><span class="sxs-lookup"><span data-stu-id="c45ce-119">In order to understand how EF Core tracks these objects, it is useful to think that a primary key is created as a [shadow property](xref:core/modeling/shadow-properties) for the owned type.</span></span> <span data-ttu-id="c45ce-120">擁有類型的執行個體的索引鍵的值將會擁有者執行個體的索引鍵的值相同。</span><span class="sxs-lookup"><span data-stu-id="c45ce-120">The value of the key of an instance of the owned type will be the same as the value of the key of the owner instance.</span></span>

## <a name="collections-of-owned-types"></a><span data-ttu-id="c45ce-121">擁有的類型集合</span><span class="sxs-lookup"><span data-stu-id="c45ce-121">Collections of owned types</span></span>

>[!NOTE]
> <span data-ttu-id="c45ce-122">此為 EF Core 2.2 中的新功能。</span><span class="sxs-lookup"><span data-stu-id="c45ce-122">This feature is new in EF Core 2.2.</span></span>

<span data-ttu-id="c45ce-123">若要設定擁有類型的集合`OwnsMany`應該用於`OnModelCreating`。</span><span class="sxs-lookup"><span data-stu-id="c45ce-123">To configure a collection of owned types `OwnsMany` should be used in `OnModelCreating`.</span></span> <span data-ttu-id="c45ce-124">不過的主索引鍵不會設定依照慣例，所以必須明確加以指定。</span><span class="sxs-lookup"><span data-stu-id="c45ce-124">However the primary key will not be configured by convention, so it needs to be specified explicitly.</span></span> <span data-ttu-id="c45ce-125">通常會使用複雜的金鑰，這些類型的合併的擁有者和其他的唯一屬性也可以在陰影狀態中的外部索引鍵的實體：</span><span class="sxs-lookup"><span data-stu-id="c45ce-125">It is common to use a complex key for these type of entities incorporating the foreign key to the owner and an additional unique property that can also be in shadow state:</span></span>

[!code-csharp[OwnsMany](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsMany)]

## <a name="mapping-owned-types-with-table-splitting"></a><span data-ttu-id="c45ce-126">擁有的類型與資料表分割的對應</span><span class="sxs-lookup"><span data-stu-id="c45ce-126">Mapping owned types with table splitting</span></span>

<span data-ttu-id="c45ce-127">當使用關聯式資料庫，依慣例參考擁有類型會對應至相同資料表的擁有者。</span><span class="sxs-lookup"><span data-stu-id="c45ce-127">When using relational databases, by convention reference owned types are mapped to the same table as the owner.</span></span> <span data-ttu-id="c45ce-128">這需要在兩個資料表分割： 某些資料行都會用來儲存資料的擁有者，以及將擁有的實體的資料用於某些資料行。</span><span class="sxs-lookup"><span data-stu-id="c45ce-128">This requires splitting the table in two: some columns will be used to store the data of the owner, and some columns will be used to store data of the owned entity.</span></span> <span data-ttu-id="c45ce-129">這是一項稱為資料表分割的一般功能。</span><span class="sxs-lookup"><span data-stu-id="c45ce-129">This is a common feature known as table splitting.</span></span>

> [!TIP]
> <span data-ttu-id="c45ce-130">擁有儲存資料表分割的類型可以用同樣的複雜類型中使用 EF6。</span><span class="sxs-lookup"><span data-stu-id="c45ce-130">Owned types stored with table splitting can be used similarly to how complex types are used in EF6.</span></span>

<span data-ttu-id="c45ce-131">依照慣例，EF Core 會遵循模式擁有的實體類型的屬性名稱的資料庫資料行_Navigation_OwnedEntityProperty_。</span><span class="sxs-lookup"><span data-stu-id="c45ce-131">By convention, EF Core will name the database columns for the properties of the owned entity type following the pattern _Navigation_OwnedEntityProperty_.</span></span> <span data-ttu-id="c45ce-132">因此`StreetAddress`屬性會出現在 [訂單] 資料表的名稱 'ShippingAddress_Street' 和 'ShippingAddress_City'。</span><span class="sxs-lookup"><span data-stu-id="c45ce-132">Therefore the `StreetAddress` properties will appear in the 'Orders' table with the names 'ShippingAddress_Street' and 'ShippingAddress_City'.</span></span>

<span data-ttu-id="c45ce-133">您可以將附加`HasColumnName`方法來重新命名這些資料行：</span><span class="sxs-lookup"><span data-stu-id="c45ce-133">You can append the `HasColumnName` method to rename those columns:</span></span>

[!code-csharp[ColumnNames](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=ColumnNames)]

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a><span data-ttu-id="c45ce-134">共用相同的.NET 型別，在多個擁有的類型</span><span class="sxs-lookup"><span data-stu-id="c45ce-134">Sharing the same .NET type among multiple owned types</span></span>

<span data-ttu-id="c45ce-135">擁有的實體類型可以是相同的.NET 型別，做為另一個擁有的實體類型，因此.NET 型別可能不足以識別擁有的類型。</span><span class="sxs-lookup"><span data-stu-id="c45ce-135">An owned entity type can be of the same .NET type as another owned entity type, therefore the .NET type may not be enough to identify an owned type.</span></span>

<span data-ttu-id="c45ce-136">在這些情況下，擁有者從指向擁有的實體屬性會變成_定義導覽_擁有的實體型別。</span><span class="sxs-lookup"><span data-stu-id="c45ce-136">In those cases, the property pointing from the owner to the owned entity becomes the _defining navigation_ of the owned entity type.</span></span> <span data-ttu-id="c45ce-137">EF Core 的觀點而言，定義的導覽是.NET 型別與型別之身分識別的一部分。</span><span class="sxs-lookup"><span data-stu-id="c45ce-137">From the perspective of EF Core, the defining navigation is part of the type's identity alongside the .NET type.</span></span>   

<span data-ttu-id="c45ce-138">例如，在下列類別`ShippingAddress`並`BillingAddress`都屬於相同的.NET 型別， `StreetAddress`:</span><span class="sxs-lookup"><span data-stu-id="c45ce-138">For example, in the following class `ShippingAddress` and `BillingAddress` are both of the same .NET type, `StreetAddress`:</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

<span data-ttu-id="c45ce-139">若要了解如何 EF Core 會區別這些物件的追蹤執行個體，可能很有用考量定義瀏覽已經成為擁有者的索引鍵與值一起執行個體索引鍵的一部分，並且擁有的類型的.NET 類型。</span><span class="sxs-lookup"><span data-stu-id="c45ce-139">In order to understand how EF Core will distinguish tracked instances of these objects, it may be useful to think that the defining navigation has become part of the key of the instance alongside the value of the key of the owner and the .NET type of the owned type.</span></span>

## <a name="nested-owned-types"></a><span data-ttu-id="c45ce-140">巢狀的擁有的類型</span><span class="sxs-lookup"><span data-stu-id="c45ce-140">Nested owned types</span></span>

<span data-ttu-id="c45ce-141">在此範例中`OrderDetails`擁有`BillingAddress`並`ShippingAddress`，這兩者都`StreetAddress`型別。</span><span class="sxs-lookup"><span data-stu-id="c45ce-141">In this example `OrderDetails` owns `BillingAddress` and `ShippingAddress`, which are both `StreetAddress` types.</span></span> <span data-ttu-id="c45ce-142">然後 `DetailedOrder` 類型擁有 `OrderDetails`。</span><span class="sxs-lookup"><span data-stu-id="c45ce-142">Then `OrderDetails` is owned by the `DetailedOrder` type.</span></span>

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/OwnedEntities/DetailedOrder.cs?name=DetailedOrder)]

[!code-csharp[OrderStatus](../../../samples/core/Modeling/OwnedEntities/OrderStatus.cs?name=OrderStatus)]

<span data-ttu-id="c45ce-143">巢狀的擁有類型，除了擁有的類型可以參考的一般實體，您可以擁有者或不同的實體，只要擁有的實體上的相依端。</span><span class="sxs-lookup"><span data-stu-id="c45ce-143">In addition to nested owned types, an owned type can reference a regular entity, it can be either the owner or a different entity as long as the owned entity is on the dependent side.</span></span> <span data-ttu-id="c45ce-144">這項功能在 EF6 中設定的複雜型別除了擁有的實體類型。</span><span class="sxs-lookup"><span data-stu-id="c45ce-144">This capability sets owned entity types apart from complex types in EF6.</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

<span data-ttu-id="c45ce-145">可鏈結`OwnsOne`fluent 的呼叫，來設定此模型中的方法：</span><span class="sxs-lookup"><span data-stu-id="c45ce-145">It is possible to chain the `OwnsOne` method in a fluent call to configure this model:</span></span>

[!code-csharp[OwnsOneNested](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneNested)]

<span data-ttu-id="c45ce-146">您也可達到相同的項目使用`OwnedAttribute`兩者`OrderDetails`和`StreetAdress`。</span><span class="sxs-lookup"><span data-stu-id="c45ce-146">It is also possible to achieve the same thing using `OwnedAttribute` on both `OrderDetails` and `StreetAdress`.</span></span>

## <a name="storing-owned-types-in-separate-tables"></a><span data-ttu-id="c45ce-147">儲存在個別的資料表擁有的類型</span><span class="sxs-lookup"><span data-stu-id="c45ce-147">Storing owned types in separate tables</span></span>

<span data-ttu-id="c45ce-148">也不同於 EF6 複雜類型，擁有的類型可以儲存在個別的資料表擁有者。</span><span class="sxs-lookup"><span data-stu-id="c45ce-148">Also unlike EF6 complex types, owned types can be stored in a separate table from the owner.</span></span> <span data-ttu-id="c45ce-149">若要覆寫慣例，將擁有的類型對應到相同資料表的擁有者，您可以直接呼叫`ToTable`並提供不同的資料表名稱。</span><span class="sxs-lookup"><span data-stu-id="c45ce-149">In order to override the convention that maps an owned type to the same table as the owner, you can simply call `ToTable` and provide a different table name.</span></span> <span data-ttu-id="c45ce-150">下列範例會將對應`OrderDetails`及到不同的表格，從其兩個位址`DetailedOrder`:</span><span class="sxs-lookup"><span data-stu-id="c45ce-150">The following example will map `OrderDetails` and its two addresses to a separate table from `DetailedOrder`:</span></span>

[!code-csharp[OwnsOneTable](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneTable)]

## <a name="querying-owned-types"></a><span data-ttu-id="c45ce-151">查詢擁有的類型</span><span class="sxs-lookup"><span data-stu-id="c45ce-151">Querying owned types</span></span>

<span data-ttu-id="c45ce-152">查詢擁有者時，預設會包含擁有的類型。</span><span class="sxs-lookup"><span data-stu-id="c45ce-152">When querying the owner the owned types will be included by default.</span></span> <span data-ttu-id="c45ce-153">您不需要使用`Include`方法，即使擁有的類型會儲存在個別的資料表。</span><span class="sxs-lookup"><span data-stu-id="c45ce-153">It is not necessary to use the `Include` method, even if the owned types are stored in a separate table.</span></span> <span data-ttu-id="c45ce-154">根據之前所述的模型，下列查詢將會得到`Order`，`OrderDetails`擁有兩個`StreetAddresses`從資料庫：</span><span class="sxs-lookup"><span data-stu-id="c45ce-154">Based on the model described before, the following query will get `Order`, `OrderDetails` and the two owned `StreetAddresses` from the database:</span></span>

[!code-csharp[DetailedOrderQuery](../../../samples/core/Modeling/OwnedEntities/Program.cs?name=DetailedOrderQuery)]

## <a name="limitations"></a><span data-ttu-id="c45ce-155">限制</span><span class="sxs-lookup"><span data-stu-id="c45ce-155">Limitations</span></span>

<span data-ttu-id="c45ce-156">其中某些限制是基礎如何擁有實體類型的運作，但其他限制，我們可以在未來版本中移除：</span><span class="sxs-lookup"><span data-stu-id="c45ce-156">Some of these limitations are fundamental to how owned entity types work, but some others are restrictions that we may be able to remove in future releases:</span></span>

### <a name="by-design-restrictions"></a><span data-ttu-id="c45ce-157">依據設計限制</span><span class="sxs-lookup"><span data-stu-id="c45ce-157">By-design restrictions</span></span>
- <span data-ttu-id="c45ce-158">您無法建立`DbSet<T>`擁有的類型</span><span class="sxs-lookup"><span data-stu-id="c45ce-158">You cannot create a `DbSet<T>` for an owned type</span></span>
- <span data-ttu-id="c45ce-159">您不能呼叫`Entity<T>()`上擁有的類型 `ModelBuilder`</span><span class="sxs-lookup"><span data-stu-id="c45ce-159">You cannot call `Entity<T>()` with an owned type on `ModelBuilder`</span></span>

### <a name="current-shortcomings"></a><span data-ttu-id="c45ce-160">目前的缺點</span><span class="sxs-lookup"><span data-stu-id="c45ce-160">Current shortcomings</span></span>
- <span data-ttu-id="c45ce-161">包含繼承階層架構可讓您擁有不支援實體類型</span><span class="sxs-lookup"><span data-stu-id="c45ce-161">Inheritance hierarchies that include owned entity types are not supported</span></span>
- <span data-ttu-id="c45ce-162">若要參考導覽擁有的實體類型不能是 null，除非它們會明確對應至個別的資料表擁有者</span><span class="sxs-lookup"><span data-stu-id="c45ce-162">Reference navigations to owned entity types cannot be null unless they are explicitly mapped to a separate table from the owner</span></span>
- <span data-ttu-id="c45ce-163">擁有的實體類型的執行個體不能共用的多個擁有者 （這是無法使用擁有的實體型別實作的值物件的知名案例）</span><span class="sxs-lookup"><span data-stu-id="c45ce-163">Instances of owned entity types cannot be shared by multiple owners (this is a well-known scenario for value objects that cannot be implemented using owned entity types)</span></span>

### <a name="shortcomings-in-previous-versions"></a><span data-ttu-id="c45ce-164">在舊版的缺點</span><span class="sxs-lookup"><span data-stu-id="c45ce-164">Shortcomings in previous versions</span></span>
- <span data-ttu-id="c45ce-165">在 EF Core 2.0 中，巡覽至擁有無法在衍生的實體型別宣告實體類型，除非所擁有的實體會明確對應至個別的資料表擁有者階層架構。</span><span class="sxs-lookup"><span data-stu-id="c45ce-165">In EF Core 2.0, navigations to owned entity types cannot be declared in derived entity types unless the owned entities are explicitly mapped to a separate table from the owner hierarchy.</span></span> <span data-ttu-id="c45ce-166">EF Core 2.1 中已移除這項限制</span><span class="sxs-lookup"><span data-stu-id="c45ce-166">This limitation has been removed in EF Core 2.1</span></span>
- <span data-ttu-id="c45ce-167">在 EF Core 2.0 和 2.1 的唯一參考所支援的擁有類型的導覽。</span><span class="sxs-lookup"><span data-stu-id="c45ce-167">In EF Core 2.0 and 2.1 only reference navigations to owned types were supported.</span></span> <span data-ttu-id="c45ce-168">EF Core 2.2 中已移除這項限制</span><span class="sxs-lookup"><span data-stu-id="c45ce-168">This limitation has been removed in EF Core 2.2</span></span>
