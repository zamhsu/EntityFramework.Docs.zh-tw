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
# <a name="owned-entity-types"></a>擁有的實體類型

>[!NOTE]
> 這項功能是在 EF Core 2.0 的新功能。

EF Core 可讓您可以永遠只會出現在導覽屬性的其他實體類型的模型實體類型。 這些稱為_擁有的實體類型_。 包含擁有的實體類型的實體是其_擁有者_。

## <a name="explicit-configuration"></a>明確的設定

擁有的實體類型永遠不會包含 EF Core 的模型中的慣例。 您可以使用`OwnsOne`方法中的`OnModelCreating`或加上註解具有型別`OwnedAttribute`（EF Core 2.1 的新） 來設定為擁有的型別類型。

在此範例中，`StreetAddress`是任何身分識別屬性的類型。 它用做訂單類型的屬性，指定特定訂單的送貨地址。

我們可以使用`OwnedAttribute`以將其視為擁有的實體時從另一個實體類型參考：

[!code-csharp[StreetAddress](../../../samples/core/Modeling/OwnedEntities/StreetAddress.cs?name=StreetAddress)]

[!code-csharp[Order](../../../samples/core/Modeling/OwnedEntities/Order.cs?name=Order)]

也可以使用`OwnsOne`方法中的`OnModelCreating`來指定`ShippingAddress`屬性是擁有實體`Order`實體類型，並視需要設定其他 facet。

[!code-csharp[OwnsOne](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOne)]

如果`ShippingAddress`屬性是在私人`Order`類型，您可以使用的字串版本`OwnsOne`方法：

[!code-csharp[OwnsOneString](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneString)]

請參閱[完整的範例專案](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities)詳細內容。 

## <a name="implicit-keys"></a>隱含索引鍵

擁有的類型設定`OwnsOne`或透過參考的瀏覽探索到一定有一對一的關聯性和擁有者，因此它們不需要自己金鑰的值因為外部索引鍵值，都是唯一。 在上述範例中，`StreetAddress`型別不需要定義索引鍵的屬性。  

若要了解如何 EF Core 會追蹤這些物件，最好考慮主索引鍵會建立為[陰影屬性](xref:core/modeling/shadow-properties)擁有的類型。 擁有類型的執行個體的索引鍵的值將會擁有者執行個體的索引鍵的值相同。

## <a name="collections-of-owned-types"></a>擁有的類型集合

>[!NOTE]
> 此為 EF Core 2.2 中的新功能。

若要設定擁有類型的集合`OwnsMany`應該用於`OnModelCreating`。 不過的主索引鍵不會設定依照慣例，所以必須明確加以指定。 通常會使用複雜的金鑰，這些類型的合併的擁有者和其他的唯一屬性也可以在陰影狀態中的外部索引鍵的實體：

[!code-csharp[OwnsMany](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsMany)]

## <a name="mapping-owned-types-with-table-splitting"></a>擁有的類型與資料表分割的對應

當使用關聯式資料庫，依慣例參考擁有類型會對應至相同資料表的擁有者。 這需要在兩個資料表分割： 某些資料行都會用來儲存資料的擁有者，以及將擁有的實體的資料用於某些資料行。 這是一項稱為資料表分割的一般功能。

> [!TIP]
> 擁有儲存資料表分割的類型可以用同樣的複雜類型中使用 EF6。

依照慣例，EF Core 會遵循模式擁有的實體類型的屬性名稱的資料庫資料行_Navigation_OwnedEntityProperty_。 因此`StreetAddress`屬性會出現在 [訂單] 資料表的名稱 'ShippingAddress_Street' 和 'ShippingAddress_City'。

您可以將附加`HasColumnName`方法來重新命名這些資料行：

[!code-csharp[ColumnNames](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=ColumnNames)]

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a>共用相同的.NET 型別，在多個擁有的類型

擁有的實體類型可以是相同的.NET 型別，做為另一個擁有的實體類型，因此.NET 型別可能不足以識別擁有的類型。

在這些情況下，擁有者從指向擁有的實體屬性會變成_定義導覽_擁有的實體型別。 EF Core 的觀點而言，定義的導覽是.NET 型別與型別之身分識別的一部分。   

例如，在下列類別`ShippingAddress`並`BillingAddress`都屬於相同的.NET 型別， `StreetAddress`:

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

若要了解如何 EF Core 會區別這些物件的追蹤執行個體，可能很有用考量定義瀏覽已經成為擁有者的索引鍵與值一起執行個體索引鍵的一部分，並且擁有的類型的.NET 類型。

## <a name="nested-owned-types"></a>巢狀的擁有的類型

在此範例中`OrderDetails`擁有`BillingAddress`並`ShippingAddress`，這兩者都`StreetAddress`型別。 然後 `DetailedOrder` 類型擁有 `OrderDetails`。

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/OwnedEntities/DetailedOrder.cs?name=DetailedOrder)]

[!code-csharp[OrderStatus](../../../samples/core/Modeling/OwnedEntities/OrderStatus.cs?name=OrderStatus)]

巢狀的擁有類型，除了擁有的類型可以參考的一般實體，您可以擁有者或不同的實體，只要擁有的實體上的相依端。 這項功能在 EF6 中設定的複雜型別除了擁有的實體類型。

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

可鏈結`OwnsOne`fluent 的呼叫，來設定此模型中的方法：

[!code-csharp[OwnsOneNested](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneNested)]

您也可達到相同的項目使用`OwnedAttribute`兩者`OrderDetails`和`StreetAdress`。

## <a name="storing-owned-types-in-separate-tables"></a>儲存在個別的資料表擁有的類型

也不同於 EF6 複雜類型，擁有的類型可以儲存在個別的資料表擁有者。 若要覆寫慣例，將擁有的類型對應到相同資料表的擁有者，您可以直接呼叫`ToTable`並提供不同的資料表名稱。 下列範例會將對應`OrderDetails`及到不同的表格，從其兩個位址`DetailedOrder`:

[!code-csharp[OwnsOneTable](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneTable)]

## <a name="querying-owned-types"></a>查詢擁有的類型

查詢擁有者時，預設會包含擁有的類型。 您不需要使用`Include`方法，即使擁有的類型會儲存在個別的資料表。 根據之前所述的模型，下列查詢將會得到`Order`，`OrderDetails`擁有兩個`StreetAddresses`從資料庫：

[!code-csharp[DetailedOrderQuery](../../../samples/core/Modeling/OwnedEntities/Program.cs?name=DetailedOrderQuery)]

## <a name="limitations"></a>限制

其中某些限制是基礎如何擁有實體類型的運作，但其他限制，我們可以在未來版本中移除：

### <a name="by-design-restrictions"></a>依據設計限制
- 您無法建立`DbSet<T>`擁有的類型
- 您不能呼叫`Entity<T>()`上擁有的類型 `ModelBuilder`

### <a name="current-shortcomings"></a>目前的缺點
- 包含繼承階層架構可讓您擁有不支援實體類型
- 若要參考導覽擁有的實體類型不能是 null，除非它們會明確對應至個別的資料表擁有者
- 擁有的實體類型的執行個體不能共用的多個擁有者 （這是無法使用擁有的實體型別實作的值物件的知名案例）

### <a name="shortcomings-in-previous-versions"></a>在舊版的缺點
- 在 EF Core 2.0 中，巡覽至擁有無法在衍生的實體型別宣告實體類型，除非所擁有的實體會明確對應至個別的資料表擁有者階層架構。 EF Core 2.1 中已移除這項限制
- 在 EF Core 2.0 和 2.1 的唯一參考所支援的擁有類型的導覽。 EF Core 2.2 中已移除這項限制
