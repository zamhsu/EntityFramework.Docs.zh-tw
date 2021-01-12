---
title: 金鑰-EF Core
description: 使用 Entity Framework Core 時如何設定實體類型的索引鍵
author: AndriySvyryd
ms.date: 11/06/2019
uid: core/modeling/keys
ms.openlocfilehash: 805396a13227aa62ed86ac17c742d055d7a22bbf
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129183"
---
# <a name="keys"></a>索引鍵

索引鍵作為每個實體實例的唯一識別碼。 EF 中的大部分實體都有單一索引鍵，它會對應至關係資料庫中 *主鍵* 的概念， (沒有索引鍵的實體，請參閱 [無索引鍵實體](xref:core/modeling/keyless-entity-types)) 。 實體可以有超出主鍵的額外索引鍵 (如需詳細資訊，請參閱其他索引 [鍵](#alternate-keys)) 。

依照慣例，名為或的屬性 `Id` `<type name>Id` 將會設定為實體的主要索引鍵。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyId.cs?name=KeyId&highlight=3,11)]

> [!NOTE]
> [擁有的實體類型](xref:core/modeling/owned-entities) 使用不同的規則來定義索引鍵。

您可以將單一屬性設定為實體的主要索引鍵，如下所示：

## <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/KeySingle.cs?name=KeySingle&highlight=3)]

## <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeySingle.cs?name=KeySingle&highlight=4)]

***

您也可以將多個屬性設定為實體的索引鍵，這稱為複合索引鍵。 複合索引鍵只能使用流暢的 API 進行設定;慣例永遠不會設定複合索引鍵，而且您不能使用資料批註來設定它。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyComposite.cs?name=KeyComposite&highlight=4)]

## <a name="primary-key-name"></a>主要金鑰名稱

依照慣例，會以名稱建立關係資料庫的主鍵 `PK_<type name>` 。 您可以設定 primary key 條件約束的名稱，如下所示：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyName.cs?name=KeyName&highlight=5)]

## <a name="key-types-and-values"></a>金鑰類型和值

雖然 EF Core 支援使用任何基本類型的屬性做為主鍵（包括 `string` 、和其他），但 `Guid` `byte[]` 並非所有資料庫都支援所有類型作為索引鍵。 在某些情況下，可以自動將索引鍵值轉換為支援的類型，否則應 [手動指定](xref:core/modeling/value-conversions)轉換。

將新實體新增至內容時，索引鍵屬性必須一律具有非預設值，但某些類型會 [由資料庫產生](xref:core/modeling/generated-properties)。 在這種情況下，當加入實體以供追蹤時，EF 會嘗試產生暫時的值。 呼叫 [SaveChanges](/dotnet/api/Microsoft.EntityFrameworkCore.DbContext.SaveChanges) 之後，暫存值就會由資料庫產生的值取代。

> [!Important]
> 如果索引鍵屬性的值是由資料庫產生的，而且在加入實體時指定了非預設值，則 EF 會假設實體已經存在於資料庫中，並會嘗試更新它，而不是插入新的實體。 若要避免這種情況，請關閉值產生，或查看 [如何為產生的屬性指定明確值](xref:core/saving/explicit-values-generated-properties)。

## <a name="alternate-keys"></a>替代索引鍵

除了主鍵之外，替代索引鍵也可做為每個實體實例的替代唯一識別碼;它可用來做為關聯性的目標。 使用關係資料庫時，這會對應至替代索引鍵資料行 (s) 的唯一索引/條件約束的概念，以及一或多個參考 () 之資料行的外鍵條件約束。

> [!TIP]
> 如果您只想要在資料行上強制執行唯一性，請定義唯一索引，而不是替代索引鍵 (查看) 的 [索引](xref:core/modeling/indexes) 。 在 EF 中，替代索引鍵是唯讀的，而且會在唯一索引上提供其他語義，因為它們可以當做外鍵的目標使用。

當您需要時，通常會為您引進替代索引鍵，而您不需要手動設定。 依照慣例，當您識別不是主要索引鍵做為關聯性目標的屬性時，就會為您引進替代索引鍵。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/AlternateKey.cs?name=AlternateKey&highlight=12)]

您也可以將單一屬性設定為替代索引鍵：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeySingle.cs?name=AlternateKeySingle&highlight=4)]

您也可以將多個屬性設定為替代索引鍵， (稱為複合的替代索引鍵) ：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeyComposite.cs?name=AlternateKeyComposite&highlight=4)]

最後，依照慣例，針對替代索引鍵引進的索引和條件約束將會命名為 `AK_<type name>_<property name>` (針對複合替代索引鍵，會 `<property name>` 變成) 的屬性名稱清單（以底線分隔）。 您可以設定替代索引鍵的索引和 unique 條件約束的名稱：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeyName.cs?name=AlternateKeyName&highlight=5)]
