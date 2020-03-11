---
title: 金鑰-EF Core
description: 如何在使用 Entity Framework Core 時設定實體類型的索引鍵
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/06/2019
uid: core/modeling/keys
ms.openlocfilehash: abd65a5ea079a49fd7a3bbc84a9337f6ee19fab1
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416464"
---
# <a name="keys"></a>索引鍵

索引鍵可作為每個實體實例的唯一識別碼。 EF 中的大部分實體都有單一索引鍵，其對應至關係資料庫中的*主鍵*概念（針對沒有索引鍵的實體，請參閱[無索引鍵實體](xref:core/modeling/keyless-entity-types)）。 實體可以有超出主要金鑰的其他索引鍵（如需詳細資訊，請參閱[替代金鑰](#alternate-keys)）。

依照慣例，名為 `Id` 或 `<type name>Id` 的屬性會設定為實體的主要索引鍵。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyId.cs?name=KeyId&highlight=3,11)]

> [!NOTE]
> [擁有的實體類型](xref:core/modeling/owned-entities)會使用不同的規則來定義索引鍵。

您可以將單一屬性設定為實體的主要金鑰，如下所示：

## <a name="data-annotations"></a>[資料註解](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/KeySingle.cs?name=KeySingle&highlight=3)]

## <a name="fluent-api"></a>[流暢的 API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeySingle.cs?name=KeySingle&highlight=4)]

***

您也可以將多個屬性設定為實體的索引鍵，這就是所謂的複合索引鍵。 複合索引鍵只能使用流暢的 API 來設定;慣例永遠不會設定複合索引鍵，而且您不能使用資料批註來設定它。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyComposite.cs?name=KeyComposite&highlight=4)]

## <a name="primary-key-name"></a>主要金鑰名稱

依照慣例，會以 `PK_<type name>`的名稱來建立關係資料庫的主要索引鍵。 您可以設定 primary key 條件約束的名稱，如下所示：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyName.cs?name=KeyName&highlight=5)]

## <a name="key-types-and-values"></a>金鑰類型和值

雖然 EF Core 支援使用任何基本類型的屬性做為主鍵，包括 `string`、`Guid`、`byte[]` 和其他專案，但並非所有的資料庫都支援所有類型做為索引鍵。 在某些情況下，索引鍵值可以自動轉換成支援的類型，否則應該[手動指定](xref:core/modeling/value-conversions)轉換。

將新的實體加入至內容時，索引鍵屬性必須一律具有非預設值，但某些類型會[由資料庫產生](xref:core/modeling/generated-properties)。 在這種情況下，當加入實體以供追蹤時，EF 會嘗試產生暫存值。 在呼叫[SaveChanges](/dotnet/api/Microsoft.EntityFrameworkCore.DbContext.SaveChanges)之後，暫時的值將會被資料庫產生的值所取代。

> [!Important]
> 如果索引鍵屬性的值是由資料庫產生，而且在加入實體時指定非預設值，則 EF 會假設實體已存在於資料庫中，而且會嘗試更新它，而不是插入新的。 若要避免這種關閉值的產生，或查看[如何為產生的屬性指定明確的值](../saving/explicit-values-generated-properties.md)。

## <a name="alternate-keys"></a>替代索引鍵

除了主鍵以外，另一個索引鍵會做為每個實體實例的替代唯一識別碼;它可用來做為關聯性的目標。 當使用關係資料庫時，這會對應至替代索引鍵資料行上唯一索引/條件約束的概念，以及一個或多個參考資料行的 foreign key 條件約束。

> [!TIP]
> 如果您只想要在資料行上強制執行唯一性，請定義唯一索引，而不是替代金鑰（請參閱[索引](indexes.md)）。 在 EF 中，其他索引鍵是唯讀的，而且會在唯一的索引上提供額外的語義，因為它們可以當做外鍵的目標使用。

您通常會在需要時為您引進替代金鑰，而不需要手動設定。 依照慣例，當您識別的屬性不是主要索引鍵做為關聯性的目標時，會為您引進替代索引鍵。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/AlternateKey.cs?name=AlternateKey&highlight=12)]

您也可以將單一屬性設定為替代金鑰：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeySingle.cs?name=AlternateKeySingle&highlight=4)]

您也可以將多個屬性設定為替代金鑰（也稱為複合替代金鑰）：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeyComposite.cs?name=AlternateKeyComposite&highlight=4)]

最後，依照慣例，針對替代索引鍵導入的索引和條件約束將會命名為 `AK_<type name>_<property name>` （對於複合的替代索引鍵 `<property name>` 會變成以底線分隔的屬性名稱清單）。 您可以設定替代金鑰的索引和 unique 條件約束的名稱：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeyName.cs?name=AlternateKeyName&highlight=5)]
