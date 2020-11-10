---
title: 擁有的實體類型-EF Core
description: 使用 Entity Framework Core 時如何設定擁有的實體類型或匯總
author: AndriySvyryd
ms.date: 11/06/2019
uid: core/modeling/owned-entities
ms.openlocfilehash: 36f756b70c9ad1727c48b5c789fd324c9dc6cd29
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429431"
---
# <a name="owned-entity-types"></a>擁有的實體類型

EF Core 可讓您建立只能出現在其他實體類型導覽屬性上的實體類型模型。 這些稱為 _擁有的實體類型_ 。 包含擁有之實體類型的實體是其 _擁有者_ 。

擁有的實體基本上是擁有者的一部分，而且不存在，而且在概念上類似于 [匯總](https://martinfowler.com/bliki/DDD_Aggregate.html)。 這表示所擁有的實體是在與擁有者之關聯性的相依端上定義。

## <a name="explicit-configuration"></a>明確設定

依慣例，模型中的 EF Core 絕不會包含擁有的實體類型。 您可以使用中的方法，或使用將類型 `OwnsOne` `OnModelCreating` 標注 `OwnedAttribute` 為，以將類型設定為擁有的類型。

在此範例中， `StreetAddress` 是沒有識別屬性的型別。 它用做訂單類型的屬性，指定特定訂單的送貨地址。

`OwnedAttribute`當您從另一個實體型別參考時，可以使用將它視為擁有的實體：

[!code-csharp[StreetAddress](../../../samples/core/Modeling/OwnedEntities/StreetAddress.cs?name=StreetAddress)]

[!code-csharp[Order](../../../samples/core/Modeling/OwnedEntities/Order.cs?name=Order)]

您也可以使用 `OwnsOne` 中的方法，將 `OnModelCreating` `ShippingAddress` 屬性指定為實體類型的擁有實體 `Order` ，並視需要設定其他 facet。

[!code-csharp[OwnsOne](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOne)]

如果 `ShippingAddress` 屬性在類型中是私用的 `Order` ，您可以使用方法的字串版本 `OwnsOne` ：

[!code-csharp[OwnsOneString](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneString)]

上述模型會對應至下列資料庫架構：

![包含所擁有參考之實體的資料庫模型 Sceenshot](_static/owned-entities-ownsone.png)

請參閱 [完整的範例專案](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities) ，以取得更多內容。

> [!TIP]
> 擁有的實體類型可標示為必要，如需詳細資訊，請參閱 [必要的一對一相依項](xref:core/modeling/relationships#one-to-one) 。

## <a name="implicit-keys"></a>隱含索引鍵

`OwnsOne`透過參考導覽設定或探索的擁有型別，一律與擁有者有一對一的關聯性，因此它們不需要自己的索引鍵值，因為外鍵值是唯一的。 在上述範例中， `StreetAddress` 類型不需要定義索引鍵屬性。  

為了瞭解 EF Core 如何追蹤這些物件，知道主鍵是建立為所擁有類型的 [陰影屬性](xref:core/modeling/shadow-properties) 會很有説明。 擁有之類型實例的索引鍵值將會與擁有者實例之索引鍵的值相同。

## <a name="collections-of-owned-types"></a>擁有類型的集合

若要設定所擁有類型的集合，請 `OwnsMany` 在中使用 `OnModelCreating` 。

擁有的類型需要主鍵。 如果 .NET 類型上沒有適合的候選屬性，EF Core 可以嘗試建立一個。 不過，當擁有的型別是透過集合定義時，您只需要建立一個影子屬性，就可以同時作為擁有者的外鍵和所擁有實例的主鍵，就像我們所做的一樣 `OwnsOne` ：每個擁有者可以有多個自有的型別實例，因此擁有者的金鑰也不足以提供每個擁有實例的唯一身分識別。

這兩個最簡單的解決方案是：

- 在新屬性上定義代理主鍵，而此屬性與指向擁有者的外鍵無關。 包含的值在所有擁有者中都必須是唯一的 (例如，如果父系 {1} 有子系 {1} ，則父系 {2} 不能有子 {1}) ，因此值沒有任何固有意義。 因為外鍵不是主鍵的一部分，所以可以變更其值，因此您可以將子系從某個父系移至另一個父代，但這通常會針對匯總語義進行。
- 使用外鍵和其他屬性做為複合索引鍵。 針對指定的父系，其他屬性值現在只需要是唯一的 (因此，如果父系 {1} 有子系， {1,1} 則父代 {2} 仍可以有子系 {2,1}) 。 藉由讓主鍵的外鍵部分成為擁有者與擁有之實體之間的關聯性，會變成不可變的，並更妥善地反映匯總語義。 這是 EF Core 預設的情況。

在此範例中，我們會使用 `Distributor` 類別。

[!code-csharp[Distributor](../../../samples/core/Modeling/OwnedEntities/Distributor.cs?name=Distributor)]

根據預設，透過導覽屬性參考的擁有類型所使用的主鍵會是 `ShippingCenters` `("DistributorId", "Id")` `"DistributorId"` FK，而 `"Id"` 是唯一的 `int` 值。

設定不同的主要金鑰呼叫 `HasKey` 。

[!code-csharp[OwnsMany](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsMany)]

上述模型會對應至下列資料庫架構：

![包含所擁有集合之實體的資料庫模型 Sceenshot](_static/owned-entities-ownsmany.png)

## <a name="mapping-owned-types-with-table-splitting"></a>使用資料表分割來對應擁有的類型

使用關係資料庫時，根據預設，參考擁有的類型會對應至與擁有者相同的資料表。 這需要將資料表分割成兩個：某些資料行將用來儲存擁有者的資料，而某些資料行會用來儲存擁有實體的資料。 這是稱為 [資料表分割](xref:core/modeling/table-splitting)的常見功能。

根據預設，EF Core 會在模式 _Navigation_OwnedEntityProperty_ 之後，為擁有的實體類型的屬性命名資料庫資料行。 因此， `StreetAddress` 屬性會出現在名稱為 ' ShippingAddress_Street ' 和 ' ShippingAddress_City ' 的 ' Orders ' 資料表中。

您可以使用 `HasColumnName` 方法來重新命名這些資料行。

[!code-csharp[ColumnNames](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=ColumnNames)]

> [!NOTE]
> 大部分的一般實體型別設定方法（例如 [Ignore](/dotnet/api/microsoft.entityframeworkcore.metadata.builders.ownednavigationbuilder.ignore) ）都可以相同的方式呼叫。

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a>在多個擁有的類型之間共用相同的 .NET 類型

擁有的實體類型可以是與另一個擁有的實體類型相同的 .NET 型別，因此 .NET 類型可能不足以識別所擁有的類型。

在這些情況下，從擁有者指向擁有之實體的屬性會變成所擁有實體類型的 _定義導覽_ 。 從 EF Core 的觀點來看，定義流覽是類型與 .NET 類型的身分識別的一部分。

例如，在下列類別中， `ShippingAddress` 和 `BillingAddress` 都是相同的 .net 型別 `StreetAddress` 。

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

若要瞭解 EF Core 將如何區分這些物件的追蹤實例，請考慮定義導覽已成為實例索引鍵的一部分，以及擁有者的索引鍵和所擁有類型之 .NET 類型的值。

## <a name="nested-owned-types"></a>嵌套擁有的類型

在此範例 `OrderDetails` 中 `BillingAddress` `ShippingAddress` ，會擁有和，兩者都是 `StreetAddress` 類型。 然後 `DetailedOrder` 類型擁有 `OrderDetails`。

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/OwnedEntities/DetailedOrder.cs?name=DetailedOrder)]

[!code-csharp[OrderStatus](../../../samples/core/Modeling/OwnedEntities/OrderStatus.cs?name=OrderStatus)]

每次流覽至擁有的型別時，會定義具有完全獨立設定的個別實體類型。

除了嵌套擁有的類型之外，擁有的類型也可以參考可以是擁有者或不同實體的一般實體，只要擁有的實體位於相依的端。 這項功能會在 EF6 中的複雜類型之外設定擁有的實體類型。

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

## <a name="configuring-owned-types"></a>設定自有類型

您可以 `OwnsOne` 在流暢的呼叫中連結方法來設定此模型：

[!code-csharp[OwnsOneNested](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneNested)]

請注意， `WithOwner` 用來定義在擁有者上指向之導覽屬性的呼叫。 若要定義不屬於擁有權關聯性的擁有者實體類型的流覽，則 `WithOwner()` 應該在沒有任何引數的情況下呼叫。

也可以使用和來達成此結果 `OwnedAttribute` `OrderDetails` `StreetAddress` 。

此外，請注意 `Navigation` 呼叫。 在 EFCore 5.0 中，您可以 [針對非擁有的導覽屬性](xref:core/modeling/relationships#configuring-navigation-properties)，進一步設定所擁有類型的導覽屬性。

上述模型會對應至下列資料庫架構：

![包含嵌套所擁有參考之實體的資料庫模型 Sceenshot](_static/owned-entities-nested.png)

## <a name="storing-owned-types-in-separate-tables"></a>將擁有的類型儲存在不同的資料表中

與 EF6 複雜類型不同的是，擁有的類型可以儲存在擁有者的個別資料表中。 為了覆寫將擁有的型別對應至與擁有者相同之資料表的慣例，您只要呼叫 `ToTable` 並提供不同的資料表名稱即可。 下列範例會將 `OrderDetails` 它的兩個位址對應到不同的資料表， `DetailedOrder` 如下所示：

[!code-csharp[OwnsOneTable](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneTable)]

您也可以使用 `TableAttribute` 來完成這項操作，但請注意，如果擁有的類型有多個導覽，則這會失敗，因為在這種情況下，多個實體類型會對應至相同的資料表。

## <a name="querying-owned-types"></a>查詢擁有的類型

查詢擁有者時，預設會包含擁有的類型。 `Include`即使擁有的類型儲存在不同的資料表中，也不需要使用方法。 根據之前所述的模型，下列查詢將會取得 `Order` ， `OrderDetails` 以及資料庫所擁有的兩個 `StreetAddresses` ：

[!code-csharp[DetailedOrderQuery](../../../samples/core/Modeling/OwnedEntities/Program.cs?name=DetailedOrderQuery)]

## <a name="limitations"></a>限制

其中有些限制是擁有的實體類型運作方式的基礎，但有些則是我們可能會在未來版本中移除的限制：

### <a name="by-design-restrictions"></a>依設計限制

- 您無法 `DbSet<T>` 為擁有的類型建立。
- 您無法 `Entity<T>()` 在上使用自有的型別呼叫 `ModelBuilder` 。
- 多個擁有者無法共用擁有的實體類型實例 (這是已知的值物件案例，無法使用所擁有的實體類型) 來執行。

### <a name="current-shortcomings"></a>目前的缺點

- 擁有的實體類型不能有繼承階層

### <a name="shortcomings-in-previous-versions"></a>舊版的缺點

- 在 EF Core 2.x 參考導覽至擁有的實體類型時，除非它們明確地對應至擁有者的個別資料表，否則不能是 null。
- 在 EF Core 3.x 中，對應至與擁有者相同之資料表的擁有實體類型資料行一律會標示為可為 null。
