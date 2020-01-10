---
title: 擁有的實體類型-EF Core
description: 如何在使用 Entity Framework Core 時設定擁有的實體類型或匯總
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/06/2019
uid: core/modeling/owned-entities
ms.openlocfilehash: 30b91b6e66b6c0f516d1ba12485304b52770cbef
ms.sourcegitcommit: 4e86f01740e407ff25e704a11b1f7d7e66bfb2a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2020
ms.locfileid: "75781231"
---
# <a name="owned-entity-types"></a>擁有的實體類型

EF Core 可讓您將只能出現在其他實體類型之導覽屬性的實體類型模型化。 這些稱為「_自有實體類型_」。 包含自有實體類型的實體是_其擁有者。_

擁有的實體基本上是擁有者的一部分，如果沒有，就不能存在，它們在概念上類似于[匯總](https://martinfowler.com/bliki/DDD_Aggregate.html)。 這表示擁有的型別是與擁有者關聯性之相依端的定義。

## <a name="explicit-configuration"></a>明確設定

模型中的 EF Core 絕對不會包含擁有的實體類型。 您可以使用 `OnModelCreating` 中的 `OwnsOne` 方法，或以 `OwnedAttribute` （EF Core 2.1 中的新功能）標注類型，將類型設定為自有類型。

在此範例中，`StreetAddress` 是不含識別屬性的類型。 它用做訂單類型的屬性，指定特定訂單的送貨地址。

從另一個實體類型參考時，我們可以使用 `OwnedAttribute` 將它視為擁有的實體：

[!code-csharp[StreetAddress](../../../samples/core/Modeling/OwnedEntities/StreetAddress.cs?name=StreetAddress)]

[!code-csharp[Order](../../../samples/core/Modeling/OwnedEntities/Order.cs?name=Order)]

您也可以使用 `OnModelCreating` 中的 `OwnsOne` 方法，將 `ShippingAddress` 屬性指定為 `Order` 實體類型的擁有實體，並視需要設定其他 facet。

[!code-csharp[OwnsOne](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOne)]

如果 `ShippingAddress` 屬性是 `Order` 類型中的私用，您可以使用 `OwnsOne` 方法的字串版本：

[!code-csharp[OwnsOneString](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneString)]

如需詳細內容，請參閱[完整的範例專案](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities)。

## <a name="implicit-keys"></a>隱含索引鍵

擁有以 `OwnsOne` 或透過參考導覽探索到的類型，一律與擁有者具有一對一的關聯性，因此，它們不需要自己的索引鍵值，因為外鍵值是唯一的。 在上述範例中，`StreetAddress` 型別不需要定義索引鍵屬性。  

若要瞭解 EF Core 如何追蹤這些物件，您可以知道主要索引鍵是建立為所擁有類型的[陰影屬性](xref:core/modeling/shadow-properties)。 擁有的類型之實例的索引鍵值，會與擁有者實例的索引鍵值相同。

## <a name="collections-of-owned-types"></a>擁有類型的集合

> [!NOTE]
> 此為 EF Core 2.2 中的新功能。

若要設定擁有類型的集合，請使用 `OnModelCreating`中的 `OwnsMany`。

擁有的類型需要主要金鑰。 如果 .NET 類型上沒有良好的候選項目屬性，EF Core 可以嘗試建立一個。 不過，當擁有的型別是透過集合所定義時，就無法只建立陰影屬性來當做外鍵同時做為擁有者實例和擁有者的主要索引鍵，如同 `OwnsOne`：每個擁有者都可以有多個擁有的型別實例，因此擁有者的索引鍵也不足以提供每個擁有之實例的唯一身分識別。

這兩個最直接的解決辦法是：

- 在與指向擁有者的外鍵無關的新屬性上定義代理主鍵。 包含的值在所有擁有者中都必須是唯一的（例如，如果父 {1} 具有子 {1}，則父 {2} 不能有子 {1}），因此值不會有任何固有的意義。 因為外鍵不是主鍵的一部分，所以可以變更其值，因此您可以將子系從一個父系移到另一個父代，但這通常會針對匯總的語義進行。
- 使用外鍵和其他屬性做為複合索引鍵。 對於指定的父系，其他屬性值現在只需要是唯一的（因此，如果父 {1} 具有子 {1,1}，父系 {2} 仍然可以有子 {2,1}）。 藉由讓主鍵的外鍵部分成為主要金鑰的一部分，擁有者與擁有的實體之間的關聯性就會變成不可變，並更清楚反映匯總的語法。 這就是 EF Core 預設執行的工作。

在此範例中，我們將使用 `Distributor` 類別：

[!code-csharp[Distributor](../../../samples/core/Modeling/OwnedEntities/Distributor.cs?name=Distributor)]

根據預設，透過 `ShippingCenters` 導覽屬性所參考之自有型別的主要金鑰將會 `("DistributorId", "Id")`，其中 `"DistributorId"` 是 FK，而 `"Id"` 是唯一的 `int` 值。

若要設定不同的 PK 呼叫 `HasKey`：

[!code-csharp[OwnsMany](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsMany)]

> [!NOTE]
> 在 EF Core 3.0 之前 `WithOwner()` 方法不存在，因此應該移除此呼叫。 此外，也不會自動探索主鍵，因此一定會指定它。

## <a name="mapping-owned-types-with-table-splitting"></a>對應具有資料表分割的擁有類型

使用關係資料庫時，根據預設，參考擁有的類型會對應至與擁有者相同的資料表。 這需要將資料表分割為兩個：某些資料行將用來儲存擁有者的資料，而有些資料行則用來儲存擁有之實體的資料。 這是稱為「[資料表分割](table-splitting.md)」的常見功能。

根據預設，EF Core 會在模式_Navigation_OwnedEntityProperty_後面，為所擁有實體類型的屬性命名資料庫資料行。 因此，`StreetAddress` 屬性會出現在名為 ' ShippingAddress_Street ' 和 ' ShippingAddress_City ' 的 ' Orders ' 資料表中。

您可以使用 `HasColumnName` 方法來重新命名這些資料行：

[!code-csharp[ColumnNames](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=ColumnNames)]

> [!NOTE]
> 大部分的一般實體類型設定方法（例如「[忽略](/dotnet/api/microsoft.entityframeworkcore.metadata.builders.ownednavigationbuilder.ignore)」）都可以用相同的方式呼叫。

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a>在多個擁有的類型之間共用相同的 .NET 類型

擁有的實體類型可以與另一個擁有的實體類型屬於相同的 .NET 類型，因此 .NET 類型可能不足以識別擁有的類型。

在這些情況下，從擁有者指向自有實體的屬性會變成所擁有實體類型的_定義導覽_。 從 EF Core 的觀點來看，定義的導覽會與 .NET 類型一起屬於類型的身分識別。

例如，在下列類別中 `ShippingAddress` 和 `BillingAddress` 都是相同的 .NET 類型，`StreetAddress`：

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

若要瞭解 EF Core 如何區分這些物件的追蹤實例，請將定義的導覽與擁有者的索引鍵值以及擁有之類型的 .NET 類型做為實例的一部分，可能會很有用。

## <a name="nested-owned-types"></a>嵌套擁有的類型

在此範例中 `OrderDetails` 擁有 `BillingAddress` 和 `ShippingAddress`，這兩者都是 `StreetAddress` 類型。 然後 `DetailedOrder` 類型擁有 `OrderDetails`。

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/OwnedEntities/DetailedOrder.cs?name=DetailedOrder)]

[!code-csharp[OrderStatus](../../../samples/core/Modeling/OwnedEntities/OrderStatus.cs?name=OrderStatus)]

對自有類型的每個導覽會定義具有完全獨立設定的個別實體類型。

除了嵌套的擁有類型之外，擁有的類型也可以參考一般實體，只要擁有的實體位於相依端，就可以是擁有者或不同的實體。 這項功能會在 EF6 中設定除了複雜類型之外的自有實體類型。

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

您可以在流暢的呼叫中將 `OwnsOne` 方法連鎖，以設定此模型：

[!code-csharp[OwnsOneNested](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneNested)]

請注意，用來設定在擁有者上指向之導覽屬性的 `WithOwner` 呼叫。 若要設定不屬於擁有權關聯性之擁有者實體類型的導覽，`WithOwner()` 應該在沒有任何引數的情況下呼叫。

您可以使用 `OrderDetails` 和 `StreetAddress`上的 `OwnedAttribute` 來達到結果。

## <a name="storing-owned-types-in-separate-tables"></a>將擁有的類型儲存在不同的資料表中

也不同于 EF6 複雜類型，擁有的類型可以儲存在擁有者的個別資料表中。 若要覆寫將擁有的類型對應到與擁有者相同之資料表的慣例，您只要呼叫 `ToTable` 並提供不同的資料表名稱即可。 下列範例會將 `OrderDetails` 和它的兩個位址對應到 `DetailedOrder`的個別資料表：

[!code-csharp[OwnsOneTable](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneTable)]

您也可以使用 `TableAttribute` 來完成這項操作，但請注意，如果有多個導覽屬於自有類型，這會失敗，因為在這種情況下，多個實體類型會對應至相同的資料表。

## <a name="querying-owned-types"></a>查詢擁有的類型

查詢擁有者時，預設會包含擁有的類型。 即使自有的類型儲存在不同的資料表中，也不需要使用 `Include` 方法。 根據先前所述的模型，下列查詢會從資料庫取得 `Order`、`OrderDetails` 和兩個擁有的 `StreetAddresses`：

[!code-csharp[DetailedOrderQuery](../../../samples/core/Modeling/OwnedEntities/Program.cs?name=DetailedOrderQuery)]

## <a name="limitations"></a>限制

其中一些限制是擁有的實體類型如何工作的基礎，但有些則是我們可以在未來版本中移除的限制：

### <a name="by-design-restrictions"></a>依設計的限制

- 您無法為擁有的類型建立 `DbSet<T>`
- 您無法在 `ModelBuilder` 上使用擁有的類型來呼叫 `Entity<T>()`

### <a name="current-shortcomings"></a>目前的缺點

- 擁有的實體類型不能有繼承階層
- 擁有的實體類型的參考導覽不可以是 null，除非它們已明確對應至擁有者的個別資料表
- 擁有的實體類型實例無法由多個擁有者共用（這是已知的值物件的情況，無法使用自有的實體類型來執行）

### <a name="shortcomings-in-previous-versions"></a>舊版的缺點

- 在 EF Core 2.0 中，除非擁有的實體明確對應至擁有者階層中的個別資料表，否則無法在衍生實體類型中宣告所擁有之實體類型的導覽。 EF Core 2.1 已移除這項限制
- 在 EF Core 2.0 和2.1 中，僅支援擁有類型的參考導覽。 EF Core 2.2 已移除這項限制
