---
title: EF Core 擁有實體型別：
author: julielerman
ms.date: 2/26/2018
ms.assetid: 2B0BADCE-E23E-4B28-B8EE-537883E16DF3
uid: core/modeling/owned-entities
ms.openlocfilehash: afbc853feab56d31a8ceba0da05fe6dc508b65bb
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994065"
---
# <a name="owned-entity-types"></a><span data-ttu-id="37bb5-102">擁有的實體類型</span><span class="sxs-lookup"><span data-stu-id="37bb5-102">Owned Entity Types</span></span>

>[!NOTE]
> <span data-ttu-id="37bb5-103">這項功能是在 EF Core 2.0 的新功能。</span><span class="sxs-lookup"><span data-stu-id="37bb5-103">This feature is new in EF Core 2.0.</span></span>

<span data-ttu-id="37bb5-104">EF Core 可讓您可以永遠只會出現在導覽屬性的其他實體類型的模型實體類型。</span><span class="sxs-lookup"><span data-stu-id="37bb5-104">EF Core allows you to model entity types that can only ever appear on navigation properties of other entity types.</span></span> <span data-ttu-id="37bb5-105">這些稱為_擁有的實體類型_。</span><span class="sxs-lookup"><span data-stu-id="37bb5-105">These are called _owned entity types_.</span></span> <span data-ttu-id="37bb5-106">包含擁有的實體類型的實體是其_擁有者_。</span><span class="sxs-lookup"><span data-stu-id="37bb5-106">The entity containing an owned entity type is its _owner_.</span></span>

## <a name="explicit-configuration"></a><span data-ttu-id="37bb5-107">明確的設定</span><span class="sxs-lookup"><span data-stu-id="37bb5-107">Explicit configuration</span></span>

<span data-ttu-id="37bb5-108">擁有的實體類型永遠不會包含 EF Core 的模型中的慣例。</span><span class="sxs-lookup"><span data-stu-id="37bb5-108">Owned entity types are never included by EF Core in the model by convention.</span></span> <span data-ttu-id="37bb5-109">您可以使用`OwnsOne`方法中的`OnModelCreating`或加上註解具有型別`OwnedAttribute`（EF Core 2.1 的新） 來設定為擁有的型別類型。</span><span class="sxs-lookup"><span data-stu-id="37bb5-109">You can use the `OwnsOne` method in `OnModelCreating` or annotate the type with `OwnedAttribute` (new in EF Core 2.1) to configure the type as an owned type.</span></span>

<span data-ttu-id="37bb5-110">在此範例中，StreetAddress 是任何身分識別屬性的類型。</span><span class="sxs-lookup"><span data-stu-id="37bb5-110">In this example, StreetAddress is a type with no identity property.</span></span> <span data-ttu-id="37bb5-111">它用做訂單類型的屬性，指定特定訂單的送貨地址。</span><span class="sxs-lookup"><span data-stu-id="37bb5-111">It is used as a property of the Order type to specify the shipping address for a particular order.</span></span> <span data-ttu-id="37bb5-112">在  `OnModelCreating`，我們使用`OwnsOne`方法，以指定 ShippingAddress 屬性是擁有類型的實體順序。</span><span class="sxs-lookup"><span data-stu-id="37bb5-112">In `OnModelCreating`, we use the `OwnsOne` method to specify that the ShippingAddress property is an Owned Entity of the Order type.</span></span>

``` csharp
public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public StreetAddress ShippingAddress { get; set; }
}

// OnModelCreating
modelBuilder.Entity<Order>().OwnsOne(p => p.ShippingAddress);
```

<span data-ttu-id="37bb5-113">如果 [ShippingAddress] 屬性是私用在訂單類型，您可以使用的字串版本`OwnsOne`方法：</span><span class="sxs-lookup"><span data-stu-id="37bb5-113">If the ShippingAddress property is private in the Order type, you can use the string version of the `OwnsOne` method:</span></span>

``` csharp
modelBuilder.Entity<Order>().OwnsOne(typeof(StreetAddress), "ShippingAddress");
```

<span data-ttu-id="37bb5-114">在此範例中，我們會使用`OwnedAttribute`能夠達成此相同的目標：</span><span class="sxs-lookup"><span data-stu-id="37bb5-114">In this example, we use the `OwnedAttribute` to achieve the same goal:</span></span>

``` csharp
[Owned]
public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public StreetAddress ShippingAddress { get; set; }
}
```

## <a name="implicit-keys"></a><span data-ttu-id="37bb5-115">隱含索引鍵</span><span class="sxs-lookup"><span data-stu-id="37bb5-115">Implicit keys</span></span>

<span data-ttu-id="37bb5-116">在 EF Core 2.0 和 2.1 中，參考導覽屬性可以指向擁有的型別。</span><span class="sxs-lookup"><span data-stu-id="37bb5-116">In EF Core 2.0 and 2.1, only reference navigation properties can point to owned types.</span></span> <span data-ttu-id="37bb5-117">不支援擁有類型的集合。</span><span class="sxs-lookup"><span data-stu-id="37bb5-117">Collections of owned types are not supported.</span></span> <span data-ttu-id="37bb5-118">這些參考擁有的類型與擁有者一定有一對一的關係，因此他們不需要自己的索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="37bb5-118">These reference owned types always have a one-to-one relationship with the owner, therefore they don't need their own key values.</span></span> <span data-ttu-id="37bb5-119">在上述範例中，StreetAddress 類型不必定義索引鍵的屬性。</span><span class="sxs-lookup"><span data-stu-id="37bb5-119">In the previous example, the StreetAddress type does not need to define a key property.</span></span>  

<span data-ttu-id="37bb5-120">若要了解如何 EF Core 會追蹤這些物件，最好考慮主索引鍵會建立為[陰影屬性](xref:core/modeling/shadow-properties)擁有的類型。</span><span class="sxs-lookup"><span data-stu-id="37bb5-120">In order to understand how EF Core tracks these objects, it is useful to think that a primary key is created as a [shadow property](xref:core/modeling/shadow-properties) for the owned type.</span></span> <span data-ttu-id="37bb5-121">擁有類型的執行個體的索引鍵的值將會擁有者執行個體的索引鍵的值相同。</span><span class="sxs-lookup"><span data-stu-id="37bb5-121">The value of the key of an instance of the owned type will be the same as the value of the key of the owner instance.</span></span>      

## <a name="mapping-owned-types-with-table-splitting"></a><span data-ttu-id="37bb5-122">擁有的類型與資料表分割的對應</span><span class="sxs-lookup"><span data-stu-id="37bb5-122">Mapping owned types with table splitting</span></span>

<span data-ttu-id="37bb5-123">使用關聯式資料庫時，依照慣例，擁有的類型會對應到相同資料表的擁有者。</span><span class="sxs-lookup"><span data-stu-id="37bb5-123">When using relational databases, by convention owned types are mapped to the same table as the owner.</span></span> <span data-ttu-id="37bb5-124">這需要在兩個資料表分割： 某些資料行都會用來儲存資料的擁有者，以及將擁有的實體的資料用於某些資料行。</span><span class="sxs-lookup"><span data-stu-id="37bb5-124">This requires splitting the table in two: some columns will be used to store the data of the owner, and some columns will be used to store data of the owned entity.</span></span> <span data-ttu-id="37bb5-125">這是一項稱為資料表分割的一般功能。</span><span class="sxs-lookup"><span data-stu-id="37bb5-125">This is a common feature known as table splitting.</span></span>

> [!TIP]
> <span data-ttu-id="37bb5-126">擁有儲存資料表分割的類型可以是非常類似使用複雜類型中使用 EF6。</span><span class="sxs-lookup"><span data-stu-id="37bb5-126">Owned types stored with table splitting can be used very similarly to how complex types are used in EF6.</span></span>

<span data-ttu-id="37bb5-127">依照慣例，EF Core 會遵循模式擁有的實體類型的屬性名稱的資料庫資料行_EntityProperty_OwnedEntityProperty_。</span><span class="sxs-lookup"><span data-stu-id="37bb5-127">By convention, EF Core will name the database columns for the properties of the owned entity type following the pattern _EntityProperty_OwnedEntityProperty_.</span></span> <span data-ttu-id="37bb5-128">因此 StreetAddress 屬性會出現在 「 訂單 」 資料表 ShippingAddress_Street 和 ShippingAddress_City 的名稱。</span><span class="sxs-lookup"><span data-stu-id="37bb5-128">Therefore the StreetAddress properties will appear in the Orders table with the names ShippingAddress_Street and ShippingAddress_City.</span></span>

<span data-ttu-id="37bb5-129">您可以將附加`HasColumnName`方法來重新命名這些資料行。</span><span class="sxs-lookup"><span data-stu-id="37bb5-129">You can append the `HasColumnName` method to rename those columns.</span></span> <span data-ttu-id="37bb5-130">StreetAddress 所在的公用屬性的情況下，可能會對應</span><span class="sxs-lookup"><span data-stu-id="37bb5-130">In the case where StreetAddress is a public property, the mappings would be</span></span>

``` csharp
modelBuilder.Entity<Order>().OwnsOne(
    o => o.ShippingAddress,
    sa =>
        {
            sa.Property(p=>p.Street).HasColumnName("ShipsToStreet");
            sa.Property(p=>p.City).HasColumnName("ShipsToCity");
        });
```

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a><span data-ttu-id="37bb5-131">共用相同的.NET 型別，在多個擁有的類型</span><span class="sxs-lookup"><span data-stu-id="37bb5-131">Sharing the same .NET type among multiple owned types</span></span>

<span data-ttu-id="37bb5-132">擁有的實體類型可以是相同的.NET 型別，做為另一個擁有的實體類型，因此.NET 型別可能不足以識別擁有的類型。</span><span class="sxs-lookup"><span data-stu-id="37bb5-132">An owned entity type can be of the same .NET type as another owned entity type, therefore the .NET type may not be enough to identify an owned type.</span></span>

<span data-ttu-id="37bb5-133">在這些情況下，擁有者從指向擁有的實體屬性會變成_定義導覽_擁有的實體型別。</span><span class="sxs-lookup"><span data-stu-id="37bb5-133">In those cases, the property pointing from the owner to the owned entity becomes the _defining navigation_ of the owned entity type.</span></span> <span data-ttu-id="37bb5-134">EF Core 的觀點而言，定義的導覽是.NET 型別與型別之身分識別的一部分。</span><span class="sxs-lookup"><span data-stu-id="37bb5-134">From the perspective of EF Core, the defining navigation is part of the type's identity alongside the .NET type.</span></span>   

<span data-ttu-id="37bb5-135">例如，在下列類別中，ShippingAddress 和 BillingAddress 都屬於相同的.NET 型別，StreetAddress:</span><span class="sxs-lookup"><span data-stu-id="37bb5-135">For example, in the following class, ShippingAddress and BillingAddress are both of the same .NET type, StreetAddress:</span></span>

``` csharp
public class Order
{
    public int Id { get; set; }
    public StreetAddress ShippingAddress { get; set; }
    public StreetAddress BillingAddress { get; set; }
}
```

<span data-ttu-id="37bb5-136">若要了解如何 EF Core 會區別這些物件的追蹤執行個體，可能很有用考量定義瀏覽已經成為擁有者的索引鍵與值一起執行個體索引鍵的一部分，並且擁有的類型的.NET 類型。</span><span class="sxs-lookup"><span data-stu-id="37bb5-136">In order to understand how EF Core will distinguish tracked instances of these objects, it may be useful to think that the defining navigation has become part of the key of the instance alongside the value of the key of the owner and the .NET type of the owned type.</span></span>

## <a name="nested-owned-types"></a><span data-ttu-id="37bb5-137">巢狀的擁有的類型</span><span class="sxs-lookup"><span data-stu-id="37bb5-137">Nested owned types</span></span>

<span data-ttu-id="37bb5-138">在此範例中 OrderDetails 擁有 BillingAddress 和 ShippingAddress，也就是這兩種 StreetAddress 類型。</span><span class="sxs-lookup"><span data-stu-id="37bb5-138">In this example OrderDetails owns BillingAddress and ShippingAddress, which are both StreetAddress types.</span></span> <span data-ttu-id="37bb5-139">然後 OrderDetails 訂單類型所擁有。</span><span class="sxs-lookup"><span data-stu-id="37bb5-139">Then OrderDetails is owned by the Order type.</span></span>

``` csharp
public class Order
{
    public int Id { get; set; }
    public OrderDetails OrderDetails { get; set; }
    public OrderStatus Status { get; set; }
}

public class OrderDetails
{
    public StreetAddress BillingAddress { get; set; }
    public StreetAddress ShippingAddress { get; set; }
}

public enum OrderStatus
{
    Pending,
    Shipped
}

public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
}
```

<span data-ttu-id="37bb5-140">可鏈結`OwnsOne`fluent 對應來設定此模型中的方法：</span><span class="sxs-lookup"><span data-stu-id="37bb5-140">It is possible to chain the `OwnsOne` method in a fluent mapping to configure this model:</span></span>

``` csharp
modelBuilder.Entity<Order>().OwnsOne(p => p.OrderDetails, od =>
    {
        od.OwnsOne(c => c.BillingAddress);
        od.OwnsOne(c => c.ShippingAddress);
    });
```

<span data-ttu-id="37bb5-141">您可達到相同的項目使用`OwnedAttribute`OrderDetails 和 StreetAdress 上。</span><span class="sxs-lookup"><span data-stu-id="37bb5-141">It is possible to achieve the same thing using `OwnedAttribute` on both OrderDetails and StreetAdress.</span></span>

<span data-ttu-id="37bb5-142">巢狀的擁有類型，除了擁有的類型可以參考的一般實體。</span><span class="sxs-lookup"><span data-stu-id="37bb5-142">In addition to nested owned types, an owned type can reference a regular entity.</span></span> <span data-ttu-id="37bb5-143">在下列範例中，國家/地區會是一般的非擁有實體：</span><span class="sxs-lookup"><span data-stu-id="37bb5-143">In the following example, Country is a regular non-owned entity:</span></span>

``` csharp
public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
    public Country Country { get; set; }
}
```

<span data-ttu-id="37bb5-144">這項功能在 EF6 中設定的複雜型別除了擁有的實體類型。</span><span class="sxs-lookup"><span data-stu-id="37bb5-144">This capability sets owned entity types apart from complex types in EF6.</span></span>

## <a name="storing-owned-types-in-separate-tables"></a><span data-ttu-id="37bb5-145">儲存在個別的資料表擁有的類型</span><span class="sxs-lookup"><span data-stu-id="37bb5-145">Storing owned types in separate tables</span></span>

<span data-ttu-id="37bb5-146">也不同於 EF6 複雜類型，擁有的類型可以儲存在個別的資料表擁有者。</span><span class="sxs-lookup"><span data-stu-id="37bb5-146">Also unlike EF6 complex types, owned types can be stored in a separate table from the owner.</span></span> <span data-ttu-id="37bb5-147">若要覆寫慣例，將擁有的類型對應到相同資料表的擁有者，您可以直接呼叫`ToTable`並提供不同的資料表名稱。</span><span class="sxs-lookup"><span data-stu-id="37bb5-147">In order to override the convention that maps an owned type to the same table as the owner, you can simply call `ToTable` and provide a different table name.</span></span> <span data-ttu-id="37bb5-148">下列範例將 OrderDetails 和其兩個位址從對應到個別的資料表順序：</span><span class="sxs-lookup"><span data-stu-id="37bb5-148">The following example will map OrderDetails and its two addresses to a separate table from Order:</span></span>

``` csharp
modelBuilder.Entity<Order>().OwnsOne(p => p.OrderDetails, od =>
    {
        od.OwnsOne(c => c.BillingAddress);
        od.OwnsOne(c => c.ShippingAddress);
        od.ToTable("OrderDetails");
    });
```

## <a name="querying-owned-types"></a><span data-ttu-id="37bb5-149">查詢擁有的類型</span><span class="sxs-lookup"><span data-stu-id="37bb5-149">Querying owned types</span></span>

<span data-ttu-id="37bb5-150">查詢擁有者時，預設會包含擁有的類型。</span><span class="sxs-lookup"><span data-stu-id="37bb5-150">When querying the owner the owned types will be included by default.</span></span> <span data-ttu-id="37bb5-151">您不需要使用`Include`方法，即使擁有的類型會儲存在個別的資料表。</span><span class="sxs-lookup"><span data-stu-id="37bb5-151">It is not necessary to use the `Include` method, even if the owned types are stored in a separate table.</span></span> <span data-ttu-id="37bb5-152">根據之前所述的模型，下列查詢將會提取順序、 OrderDetails 和兩個擁有的 StreetAddresses，從資料庫的所有暫止訂單：</span><span class="sxs-lookup"><span data-stu-id="37bb5-152">Based on the model described before, the following query will pull Order, OrderDetails and the two owned StreetAddresses for all pending orders from the database:</span></span>

``` csharp
var orders = context.Orders.Where(o => o.Status == OrderStatus.Pending);
```  

## <a name="limitations"></a><span data-ttu-id="37bb5-153">限制</span><span class="sxs-lookup"><span data-stu-id="37bb5-153">Limitations</span></span>

<span data-ttu-id="37bb5-154">其中某些限制是基礎如何擁有實體類型的運作，但其他限制，我們可以在未來版本中移除：</span><span class="sxs-lookup"><span data-stu-id="37bb5-154">Some of these limitations are fundamental to how owned entity types work, but some others are restrictions that we may be able to remove in future releases:</span></span>

### <a name="shortcomings-in-previous-versions"></a><span data-ttu-id="37bb5-155">在舊版的缺點</span><span class="sxs-lookup"><span data-stu-id="37bb5-155">Shortcomings in previous versions</span></span>
- <span data-ttu-id="37bb5-156">在 EF Core 2.0 中，巡覽至擁有無法在衍生的實體型別宣告實體類型，除非所擁有的實體會明確對應至個別的資料表擁有者階層架構。</span><span class="sxs-lookup"><span data-stu-id="37bb5-156">In EF Core 2.0, navigations to owned entity types cannot be declared in derived entity types unless the owned entities are explicitly mapped to a separate table from the owner hierarchy.</span></span> <span data-ttu-id="37bb5-157">EF Core 2.1 中已移除這項限制</span><span class="sxs-lookup"><span data-stu-id="37bb5-157">This limitation has been removed in EF Core 2.1</span></span>

### <a name="current-shortcomings"></a><span data-ttu-id="37bb5-158">目前的缺點</span><span class="sxs-lookup"><span data-stu-id="37bb5-158">Current shortcomings</span></span>
- <span data-ttu-id="37bb5-159">包含繼承階層架構可讓您擁有不支援實體類型</span><span class="sxs-lookup"><span data-stu-id="37bb5-159">Inheritance hierarchies that include owned entity types are not supported</span></span>
- <span data-ttu-id="37bb5-160">擁有的實體類型不能指向集合導覽屬性 （目前支援巡覽的唯一參考）</span><span class="sxs-lookup"><span data-stu-id="37bb5-160">Owned entity types cannot be pointed at by a collection navigation property (only reference navigations are currently supported)</span></span>
- <span data-ttu-id="37bb5-161">巡覽至擁有的實體類型不能是 null，除非它們會明確對應至個別的資料表擁有者</span><span class="sxs-lookup"><span data-stu-id="37bb5-161">Navigations to owned entity types cannot be null unless they are explicitly mapped to a separate table from the owner</span></span>
- <span data-ttu-id="37bb5-162">擁有的實體類型的執行個體不能共用的多個擁有者 （這是無法使用擁有的實體型別實作的值物件的知名案例）</span><span class="sxs-lookup"><span data-stu-id="37bb5-162">Instances of owned entity types cannot be shared by multiple owners (this is a well-known scenario for value objects that cannot be implemented using owned entity types)</span></span>

### <a name="by-design-restrictions"></a><span data-ttu-id="37bb5-163">依據設計限制</span><span class="sxs-lookup"><span data-stu-id="37bb5-163">By-design restrictions</span></span>
- <span data-ttu-id="37bb5-164">您無法建立 `DbSet<T>`</span><span class="sxs-lookup"><span data-stu-id="37bb5-164">You cannot create a `DbSet<T>`</span></span>
- <span data-ttu-id="37bb5-165">您不能呼叫`Entity<T>()`上擁有的類型 `ModelBuilder`</span><span class="sxs-lookup"><span data-stu-id="37bb5-165">You cannot call `Entity<T>()` with an owned type on `ModelBuilder`</span></span>
