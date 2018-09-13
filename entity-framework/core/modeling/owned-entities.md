---
title: EF Core 擁有實體型別：
author: julielerman
ms.date: 02/26/2018
ms.assetid: 2B0BADCE-E23E-4B28-B8EE-537883E16DF3
uid: core/modeling/owned-entities
ms.openlocfilehash: 1104a8a9a4540e33624fad69c47f2f950c6669bf
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45489410"
---
# <a name="owned-entity-types"></a>擁有的實體類型

>[!NOTE]
> 這項功能是在 EF Core 2.0 的新功能。

EF Core 可讓您可以永遠只會出現在導覽屬性的其他實體類型的模型實體類型。 這些稱為_擁有的實體類型_。 包含擁有的實體類型的實體是其_擁有者_。

## <a name="explicit-configuration"></a>明確的設定

擁有的實體類型永遠不會包含 EF Core 的模型中的慣例。 您可以使用`OwnsOne`方法中的`OnModelCreating`或加上註解具有型別`OwnedAttribute`（EF Core 2.1 的新） 來設定為擁有的型別類型。

在此範例中，StreetAddress 是任何身分識別屬性的類型。 它用做訂單類型的屬性，指定特定訂單的送貨地址。 在  `OnModelCreating`，我們使用`OwnsOne`方法，以指定 ShippingAddress 屬性是擁有類型的實體順序。

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

如果 [ShippingAddress] 屬性是私用在訂單類型，您可以使用的字串版本`OwnsOne`方法：

``` csharp
modelBuilder.Entity<Order>().OwnsOne(typeof(StreetAddress), "ShippingAddress");
```

在此範例中，我們會使用`OwnedAttribute`能夠達成此相同的目標：

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

## <a name="implicit-keys"></a>隱含索引鍵

在 EF Core 2.0 和 2.1 中，參考導覽屬性可以指向擁有的型別。 不支援擁有類型的集合。 這些參考擁有的類型與擁有者一定有一對一的關係，因此他們不需要自己的索引鍵值。 在上述範例中，StreetAddress 類型不必定義索引鍵的屬性。  

若要了解如何 EF Core 會追蹤這些物件，最好考慮主索引鍵會建立為[陰影屬性](xref:core/modeling/shadow-properties)擁有的類型。 擁有類型的執行個體的索引鍵的值將會擁有者執行個體的索引鍵的值相同。      

## <a name="mapping-owned-types-with-table-splitting"></a>擁有的類型與資料表分割的對應

使用關聯式資料庫時，依照慣例，擁有的類型會對應到相同資料表的擁有者。 這需要在兩個資料表分割： 某些資料行都會用來儲存資料的擁有者，以及將擁有的實體的資料用於某些資料行。 這是一項稱為資料表分割的一般功能。

> [!TIP]
> 擁有儲存資料表分割的類型可以是非常類似使用複雜類型中使用 EF6。

依照慣例，EF Core 會遵循模式擁有的實體類型的屬性名稱的資料庫資料行_EntityProperty_OwnedEntityProperty_。 因此 StreetAddress 屬性會出現在 「 訂單 」 資料表 ShippingAddress_Street 和 ShippingAddress_City 的名稱。

您可以將附加`HasColumnName`方法來重新命名這些資料行。 StreetAddress 所在的公用屬性的情況下，可能會對應

``` csharp
modelBuilder.Entity<Order>().OwnsOne(
    o => o.ShippingAddress,
    sa =>
        {
            sa.Property(p=>p.Street).HasColumnName("ShipsToStreet");
            sa.Property(p=>p.City).HasColumnName("ShipsToCity");
        });
```

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a>共用相同的.NET 型別，在多個擁有的類型

擁有的實體類型可以是相同的.NET 型別，做為另一個擁有的實體類型，因此.NET 型別可能不足以識別擁有的類型。

在這些情況下，擁有者從指向擁有的實體屬性會變成_定義導覽_擁有的實體型別。 EF Core 的觀點而言，定義的導覽是.NET 型別與型別之身分識別的一部分。   

例如，在下列類別中，ShippingAddress 和 BillingAddress 都屬於相同的.NET 型別，StreetAddress:

``` csharp
public class Order
{
    public int Id { get; set; }
    public StreetAddress ShippingAddress { get; set; }
    public StreetAddress BillingAddress { get; set; }
}
```

若要了解如何 EF Core 會區別這些物件的追蹤執行個體，可能很有用考量定義瀏覽已經成為擁有者的索引鍵與值一起執行個體索引鍵的一部分，並且擁有的類型的.NET 類型。

## <a name="nested-owned-types"></a>巢狀的擁有的類型

在此範例中 OrderDetails 擁有 BillingAddress 和 ShippingAddress，也就是這兩種 StreetAddress 類型。 然後 OrderDetails 訂單類型所擁有。

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

可鏈結`OwnsOne`fluent 對應來設定此模型中的方法：

``` csharp
modelBuilder.Entity<Order>().OwnsOne(p => p.OrderDetails, od =>
    {
        od.OwnsOne(c => c.BillingAddress);
        od.OwnsOne(c => c.ShippingAddress);
    });
```

您可達到相同的項目使用`OwnedAttribute`OrderDetails 和 StreetAdress 上。

巢狀的擁有類型，除了擁有的類型可以參考的一般實體。 在下列範例中，國家/地區會是一般的非擁有實體：

``` csharp
public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
    public Country Country { get; set; }
}
```

這項功能在 EF6 中設定的複雜型別除了擁有的實體類型。

## <a name="storing-owned-types-in-separate-tables"></a>儲存在個別的資料表擁有的類型

也不同於 EF6 複雜類型，擁有的類型可以儲存在個別的資料表擁有者。 若要覆寫慣例，將擁有的類型對應到相同資料表的擁有者，您可以直接呼叫`ToTable`並提供不同的資料表名稱。 下列範例將 OrderDetails 和其兩個位址從對應到個別的資料表順序：

``` csharp
modelBuilder.Entity<Order>().OwnsOne(p => p.OrderDetails, od =>
    {
        od.OwnsOne(c => c.BillingAddress);
        od.OwnsOne(c => c.ShippingAddress);
        od.ToTable("OrderDetails");
    });
```

## <a name="querying-owned-types"></a>查詢擁有的類型

查詢擁有者時，預設會包含擁有的類型。 您不需要使用`Include`方法，即使擁有的類型會儲存在個別的資料表。 根據之前所述的模型，下列查詢將會提取順序、 OrderDetails 和兩個擁有的 StreetAddresses，從資料庫的所有暫止訂單：

``` csharp
var orders = context.Orders.Where(o => o.Status == OrderStatus.Pending);
```  

## <a name="limitations"></a>限制

其中某些限制是基礎如何擁有實體類型的運作，但其他限制，我們可以在未來版本中移除：

### <a name="shortcomings-in-previous-versions"></a>在舊版的缺點
- 在 EF Core 2.0 中，巡覽至擁有無法在衍生的實體型別宣告實體類型，除非所擁有的實體會明確對應至個別的資料表擁有者階層架構。 EF Core 2.1 中已移除這項限制

### <a name="current-shortcomings"></a>目前的缺點
- 包含繼承階層架構可讓您擁有不支援實體類型
- 擁有的實體類型不能指向集合導覽屬性 （目前支援巡覽的唯一參考）
- 巡覽至擁有的實體類型不能是 null，除非它們會明確對應至個別的資料表擁有者
- 擁有的實體類型的執行個體不能共用的多個擁有者 （這是無法使用擁有的實體型別實作的值物件的知名案例）

### <a name="by-design-restrictions"></a>依據設計限制
- 您無法建立 `DbSet<T>`
- 您不能呼叫`Entity<T>()`上擁有的類型 `ModelBuilder`
