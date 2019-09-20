---
title: EF Core 擁有實體型別：
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 02/26/2018
ms.assetid: 2B0BADCE-E23E-4B28-B8EE-537883E16DF3
uid: core/modeling/owned-entities
ms.openlocfilehash: f69bae2de28156876e0aa57376b5dfac053adb9c
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149143"
---
# <a name="owned-entity-types"></a>擁有的實體類型

>[!NOTE]
> 這項功能是在 EF Core 2.0 的新功能。

EF Core 可讓您可以永遠只會出現在導覽屬性的其他實體類型的模型實體類型。 這些稱為「_自有實體類型_」。 包含自有實體類型的實體是_其擁有者。_

擁有的實體基本上是擁有者的一部分，如果沒有，就不能存在，它們在概念上類似于[匯總](https://martinfowler.com/bliki/DDD_Aggregate.html)。

## <a name="explicit-configuration"></a>明確設定

擁有的實體類型永遠不會包含 EF Core 的模型中的慣例。 您可以使用`OwnsOne`方法中的`OnModelCreating`或加上註解具有型別`OwnedAttribute`（EF Core 2.1 的新） 來設定為擁有的型別類型。

在此範例中`StreetAddress` ，是不含識別屬性的類型。 它用做訂單類型的屬性，指定特定訂單的送貨地址。

從另一個實體`OwnedAttribute`類型參考時，我們可以使用將它視為擁有的實體：

[!code-csharp[StreetAddress](../../../samples/core/Modeling/OwnedEntities/StreetAddress.cs?name=StreetAddress)]

[!code-csharp[Order](../../../samples/core/Modeling/OwnedEntities/Order.cs?name=Order)]

您`OwnsOne`也可以使用中`OnModelCreating`的方法，將`ShippingAddress`屬性指定為`Order`實體類型的自有實體，並視需要設定其他 facet。

[!code-csharp[OwnsOne](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOne)]

如果屬性在型別中是私用的，您可以使用`OwnsOne`方法的字串版本： `Order` `ShippingAddress`

[!code-csharp[OwnsOneString](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneString)]

如需詳細內容，請參閱[完整的範例專案](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities)。 

## <a name="implicit-keys"></a>隱含索引鍵

透過參考導覽所`OwnsOne`設定或探索的擁有類型，一律與擁有者具有一對一的關聯性，因此它們不需要自己的索引鍵值，因為外鍵值是唯一的。 在上述範例中， `StreetAddress`型別不需要定義索引鍵屬性。  

若要瞭解 EF Core 如何追蹤這些物件，您可以知道主要索引鍵是建立為所擁有類型的[陰影屬性](xref:core/modeling/shadow-properties)。 擁有的類型之實例的索引鍵值，會與擁有者實例的索引鍵值相同。

## <a name="collections-of-owned-types"></a>擁有類型的集合

> [!NOTE]
> 此為 EF Core 2.2 中的新功能。

若要設定所擁有類型的集合`OwnsMany` ， `OnModelCreating`請使用中的。

擁有的類型需要主要金鑰。 如果 .NET 類型上沒有良好的候選項目屬性，EF Core 可以嘗試建立一個。 不過，當擁有的型別是透過集合來定義時，只是建立一個陰影屬性，同時做為擁有者的外鍵和擁有之實例的主鍵，如同我們的`OwnsOne`作法：可以有多個擁有的類型實例每個擁有者，因此擁有者的金鑰不足，無法為每個擁有的實例提供唯一的身分識別。

這兩個最直接的解決辦法是：
- 在與指向擁有者的外鍵無關的新屬性上定義代理主鍵。 包含的值在所有擁有者中都必須是唯一的（例如， {1}如果父系{1}具有子系{2} ，則父系{1}不能有子系），因此值不會有任何固有的意義。 因為外鍵不是主鍵的一部分，所以可以變更其值，因此您可以將子系從一個父系移到另一個父代，但這通常會針對匯總的語義進行。
- 使用外鍵和其他屬性做為複合索引鍵。 對於指定的父系，其他屬性值現在只需要是唯一的（因此，如果{1}父系具有{1,1} child， {2}父系仍然可以有{2,1}子系）。 藉由讓主鍵的外鍵部分成為主要金鑰的一部分，擁有者與擁有的實體之間的關聯性就會變成不可變，並更清楚反映匯總的語法。 這就是 EF Core 預設執行的工作。

在此範例中， `Distributor`我們將使用類別：

[!code-csharp[Distributor](../../../samples/core/Modeling/OwnedEntities/Distributor.cs?name=Distributor)]

根據`ShippingCenters`預設，用於透過導覽屬性所參考之`("DistributorId", "Id")`自有類型的主要索引鍵為， `"DistributorId"`其中是 FK， `"Id"`而是唯一`int`值。

若要設定不同的 PK `HasKey`呼叫：

[!code-csharp[OwnsMany](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsMany)]

> [!NOTE]
> 在 EF Core 3.0 `WithOwner()`方法不存在之前，請先移除此呼叫。

## <a name="mapping-owned-types-with-table-splitting"></a>對應具有資料表分割的擁有類型

使用關係資料庫時，根據預設，參考擁有的類型會對應至與擁有者相同的資料表。 這需要將資料表分割為兩個：某些資料行將用來儲存擁有者的資料，而有些資料行則用來儲存擁有之實體的資料。 這是稱為「[資料表分割](table-splitting.md)」的常見功能。

根據預設，EF Core 會在模式_Navigation_OwnedEntityProperty_之後，為所擁有實體類型的屬性命名資料庫資料行。 因此， `StreetAddress`這些屬性會出現在 ' Orders ' 資料表中，名稱為 ' ShippingAddress_Street ' 和 ' ShippingAddress_City '。

您可以使用`HasColumnName`方法來重新命名這些資料行：

[!code-csharp[ColumnNames](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=ColumnNames)]

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a>在多個擁有的類型之間共用相同的 .NET 類型

擁有的實體類型可以與另一個擁有的實體類型屬於相同的 .NET 類型，因此 .NET 類型可能不足以識別擁有的類型。

在這些情況下，從擁有者指向自有實體的屬性會變成所擁有實體類型的_定義導覽_。 EF Core 的觀點而言，定義的導覽是.NET 型別與型別之身分識別的一部分。   

例如，在下列類別`ShippingAddress`中，和`BillingAddress`都是相同的 .net 類型： `StreetAddress`

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

若要了解如何 EF Core 會區別這些物件的追蹤執行個體，可能很有用考量定義瀏覽已經成為擁有者的索引鍵與值一起執行個體索引鍵的一部分，並且擁有的類型的.NET 類型。

## <a name="nested-owned-types"></a>嵌套擁有的類型

在此範例`OrderDetails`中`BillingAddress` ， `ShippingAddress`擁有和，兩者`StreetAddress`都是類型。 然後 `DetailedOrder` 類型擁有 `OrderDetails`。

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/OwnedEntities/DetailedOrder.cs?name=DetailedOrder)]

[!code-csharp[OrderStatus](../../../samples/core/Modeling/OwnedEntities/OrderStatus.cs?name=OrderStatus)]

除了嵌套的擁有類型之外，擁有的類型也可以參考一般實體，只要擁有的實體位於相依端，就可以是擁有者或不同的實體。 這項功能會在 EF6 中設定除了複雜類型之外的自有實體類型。

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

您可以在流暢的呼叫`OwnsOne`中連結方法，以設定此模型：

[!code-csharp[OwnsOneNested](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneNested)]

請注意`WithOwner` ，用來設定在擁有者上指向之導覽屬性的呼叫。

您可以`OwnedAttribute` `OrderDetails`在和`StreetAdress`上使用來達到結果。

## <a name="storing-owned-types-in-separate-tables"></a>將擁有的類型儲存在不同的資料表中

也不同于 EF6 複雜類型，擁有的類型可以儲存在擁有者的個別資料表中。 若要覆寫將擁有的類型對應到與擁有者相同之資料表的慣例，您只要呼叫`ToTable` ，並提供不同的資料表名稱即可。 下列範例會將和`OrderDetails`它的兩個位址對應至不同的`DetailedOrder`資料表，如下所示：

[!code-csharp[OwnsOneTable](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneTable)]

## <a name="querying-owned-types"></a>查詢擁有的類型

查詢擁有者時，預設會包含擁有的類型。 即使自有的類型儲存在不同`Include`的資料表中，也不需要使用方法。 根據先前所述的模型，下列查詢將會取得`Order`， `OrderDetails`而這兩個`StreetAddresses`資料庫是由資料庫所擁有：

[!code-csharp[DetailedOrderQuery](../../../samples/core/Modeling/OwnedEntities/Program.cs?name=DetailedOrderQuery)]

## <a name="limitations"></a>限制

其中一些限制是擁有的實體類型如何工作的基礎，但有些則是我們可以在未來版本中移除的限制：

### <a name="by-design-restrictions"></a>依設計的限制
- 您無法`DbSet<T>`為擁有的類型建立
- 您無法在`Entity<T>()`上使用自有類型呼叫`ModelBuilder`

### <a name="current-shortcomings"></a>目前的缺點
- 不支援包含所擁有之實體類型的繼承階層
- 擁有的實體類型的參考導覽不可以是 null，除非它們已明確對應至擁有者的個別資料表
- 擁有的實體類型實例無法由多個擁有者共用（這是已知的值物件的情況，無法使用自有的實體類型來執行）

### <a name="shortcomings-in-previous-versions"></a>舊版的缺點
- 在 EF Core 2.0 中，除非擁有的實體明確對應至擁有者階層中的個別資料表，否則無法在衍生實體類型中宣告所擁有之實體類型的導覽。 EF Core 2.1 已移除這項限制
- 在 EF Core 2.0 和2.1 中，僅支援擁有類型的參考導覽。 EF Core 2.2 已移除這項限制
