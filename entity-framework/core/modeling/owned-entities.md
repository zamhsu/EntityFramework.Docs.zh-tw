---
title: EF Core 擁有實體型別：
author: julielerman
ms.author: divega
ms.date: 2/26/2018
ms.assetid: 2B0BADCE-E23E-4B28-B8EE-537883E16DF3
ms.technology: entity-framework-core
uid: core/modeling/owned-entities
ms.openlocfilehash: f2f05499a3e3494f420d916df2db19667a6f1e29
ms.sourcegitcommit: 26f33758c47399ae933f22fec8e1d19fa7d2c0b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/19/2018
---
# <a name="owned-entity-types"></a>擁有的實體類型

>[!NOTE]
> 這項功能是在 EF Core 2.0 的新功能。

EF Core 可讓您可以永遠只會出現在導覽屬性的其他實體類型的模型實體類型。 這種讀取稱為_擁有實體類型_。 包含擁有的實體類型的實體為其_擁有者_。

## <a name="explicit-configuration"></a>明確的設定

擁有的實體類型永遠不會包含 EF Core 的模型中的慣例。 您可以使用`OwnsOne`方法中的`OnModelCreating`或加上註解具有型別`OwnedAttribute`（EF Core 2.1 的新） 來設定為擁有的型別類型。

在此範例中，StreetAddress 是沒有 identity 屬性的類型。 它用做訂單類型的屬性，指定特定訂單的送貨地址。 在`OnModelCreating`，我們使用`OwnsOne`方法，以指定 ShippingAddress 屬性所擁有的實體順序類型。

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

如果中的順序類型 [ShippingAddress] 屬性，您可以使用的字串版本`OwnsOne`方法：

``` csharp
modelBuilder.Entity<Order>().OwnsOne(typeof(StreetAddress), "ShippingAddress");
```

在此範例中，我們使用`OwnedAttribute`來達成相同的目標：

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

在 EF Core 2.0 和 2.1 中，參考導覽屬性可以指向擁有的型別。 不支援擁有型別的集合。 這些參考擁有類型一律與擁有者有一對一的關聯性，因此它們不需要自己的索引鍵值。 在上述範例中，StreetAddress 型別不必定義索引鍵內容。  

為了了解如何 EF Core 追蹤這些物件，是可以將主索引鍵會建立為[陰影屬性](xref:core/modeling/shadow-properties)擁有的類型。 擁有型別的執行個體索引鍵的值將會擁有者執行個體的索引鍵的值相同。      

## <a name="mapping-owned-types-with-table-splitting"></a>擁有與分割資料表的類型對應

時使用關聯式資料庫，依慣例所擁有的型別會對應到相同資料表的擁有者。 這需要兩個資料表分割： 某些資料行用於儲存資料的擁有者，以及某些資料行用於儲存擁有實體的資料。 這是常見的功能，又稱為分割資料表。

> [!TIP]
> 擁有與分割資料表一起儲存的型別可以是多複雜類型中使用 EF6 非常類似使用。

依照慣例，EF Core 會遵循模式擁有的實體類型的屬性名稱的資料庫資料行_EntityProperty_OwnedEntityProperty_。 因此 StreetAddress 屬性會出現在 「 訂單 」 資料表 ShippingAddress_Street 和 ShippingAddress_City 的名稱。

您可以將附加`HasColumnName`方法來重新命名這些資料行。 在 StreetAddress 所在的公用屬性的情況下，會對應

``` csharp
modelBuilder.Entity<Order>().OwnsOne(
    o => o.ShippingAddress,
    sa =>
        {
            sa.Property(p=>p.Street).HasColumnName("ShipsToStreet");
            sa.Property(p=>p.City).HasColumnName("ShipsToCity");
        });
```

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a>共用相同的.NET 型別，在多個擁有類型之間

擁有的實體類型可以是相同的.NET 型別為其他擁有的實體類型，因此.NET 型別可能不夠來識別擁有的型別。

在這些情況下，擁有者從指向擁有的實體屬性會變成_定義巡覽_擁有的實體類型。 EF Core 的觀點而言，定義的導覽是.NET 型別與型別之身分識別的一部分。   

例如，在下列類別中，ShippingAddress 和 BillingAddress 都屬於相同的.NET 類型，StreetAddress:

``` csharp
public class Order
{
    public int Id { get; set; }
    public StreetAddress ShippingAddress { get; set; }
    public StreetAddress BillingAddress { get; set; }
}
```

若要了解如何 EF Core 會區別這些物件的追蹤執行個體，可能很有用考量定義瀏覽已經成為擁有者的索引鍵與值一起執行個體索引鍵的一部分，並且擁有的類型的.NET 類型。

## <a name="nested-owned-types"></a>巢狀擁有的型別

在此範例中 OrderDetails 即擁有 BillingAddress ShippingAddress，也就是這兩種 StreetAddress 類型。 然後 OrderDetails 的擁有者順序類型。

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

public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
}
```

可鏈結`OwnsOne`fluent 應用程式開發，此模型設定對應中的方法：

``` csharp
modelBuilder.Entity<Order>().OwnsOne(p => p.OrderDetails, od =>
    {
        od.OwnsOne(c => c.BillingAddress);
        od.OwnsOne(c => c.ShippingAddress);
    });
```

可達成相同的項目使用`OwnedAttribute`OrderDetails 和 StreetAdress 上。

除了擁有的巢狀類型，擁有的型別可以參考一般實體。 在下列範例中，國家 （地區） 是規則 （也就是沒有擁有者） 的實體：

``` csharp
public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
    public Country Country { get; set; }
}
```

這項功能將設定 EF6 複雜型別除了擁有的實體類型。

## <a name="storing-owned-types-in-separate-tables"></a>儲存擁有個別的資料表中的型別

也不同於 EF6 複雜類型，擁有的類型可以儲存在個別的資料表擁有者。 若要覆寫將擁有的型別對應到相同資料表的擁有者的慣例，您可以直接呼叫`ToTable`並提供不同的資料表名稱。 下列範例將 OrderDetails 和其兩個位址從對應到不同的資料表順序：

``` csharp
modelBuilder.Entity<Order>().OwnsOne(p => p.OrderDetails, od =>
    {
        od.OwnsOne(c => c.BillingAddress);
        od.OwnsOne(c => c.ShippingAddress);
    }).ToTable("OrderDetails");
```

## <a name="querying-owned-types"></a>查詢擁有的型別

查詢擁有者時，預設會包含擁有的類型。 不需要使用`Include`方法，即使擁有的型別會儲存在個別的資料表。 根據之前所述的模型，下列查詢將會提取順序、 OrderDetails 和兩個附屬的 StreeAddresses，從資料庫的所有暫止訂單：

``` csharp
var orders = context.Orders.Where(o => o.Status == OrderStatus.Pending);
```  

## <a name="limitations"></a>限制

以下是擁有的實體類型的一些限制。 這些限制是如何擁有的型別工作的基礎，但某些則是我們想要在未來版本中移除的時間點限制：

### <a name="current-shortcomings"></a>目前的缺點
- 不支援擁有型別的繼承
- 無法指向擁有的型別集合導覽屬性
- 由於 tvf 使用資料表分割的預設擁有型別也有下列限制，除非明確地對應到不同的資料表：
   - 它們不能擁有的衍生型別
   - 無法設定定義導覽屬性為 null （也就是擁有相同的資料表上的類型都需要）

### <a name="by-design-restrictions"></a>依據設計限制
- 無法建立 `DbSet<T>`
- 您不能呼叫`Entity<T>()`上擁有的類型 `ModelBuilder`
