---
title: 金鑰（主要）-EF Core
description: 如何在使用 Entity Framework Core 時設定實體類型的索引鍵
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/06/2019
uid: core/modeling/keys
ms.openlocfilehash: fdaccb42259ba9dad97a05c626edd0291ca96cb0
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824617"
---
# <a name="keys-primary"></a>索引鍵 (主要)

索引鍵可作為每個實體實例的主要唯一識別碼。 當使用關係資料庫時，這會對應至*主鍵*的概念。 您也可以設定非主要金鑰的唯一識別碼（如需詳細資訊，請參閱[替代金鑰](alternate-keys.md)）。

您可以使用下列其中一種方法來設定/建立主要金鑰。

## <a name="conventions"></a>慣例

根據預設，名為 `Id` 或 `<type name>Id` 的屬性會設定為實體的索引鍵。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyId.cs?name=KeyId&highlight=3)]

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyTypeNameId.cs?name=KeyId&highlight=3)]

> [!NOTE]
> [擁有的實體類型](xref:core/modeling/owned-entities)會使用不同的規則來定義索引鍵。

## <a name="data-annotations"></a>資料註釋

您可以使用資料批註，將單一屬性設定為實體的索引鍵。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/KeySingle.cs?highlight=13)]

## <a name="fluent-api"></a>Fluent API

您可以使用流暢的 API，將單一屬性設定為實體的索引鍵。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeySingle.cs?highlight=11,12)]

您也可以使用流暢的 API，將多個屬性設定為實體的索引鍵（也稱為複合索引鍵）。 複合索引鍵只能使用流暢的 API 慣例來設定複合索引鍵，而且您不能使用資料批註來設定它。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyComposite.cs?highlight=11,12)]

## <a name="key-types-and-values"></a>金鑰類型和值

EF Core 支援使用任何基本類型的屬性作為主鍵，包括 `string`、`Guid`、`byte[]` 及其他專案。 但並非所有的資料庫都支援它們。 在某些情況下，索引鍵值可以自動轉換成支援的類型，否則應該[手動指定](xref:core/modeling/value-conversions)轉換。

將新的實體加入至內容時，索引鍵屬性必須一律具有非預設值，但某些類型會[由資料庫產生](xref:core/modeling/generated-properties)。 在這種情況下，當加入實體以供追蹤時，EF 會嘗試產生暫存值。 在呼叫[SaveChanges](/dotnet/api/Microsoft.EntityFrameworkCore.DbContext.SaveChanges)之後，暫時的值將會被資料庫產生的值所取代。

> [!Important]
> 如果索引鍵屬性具有資料庫產生的值，而且在加入實體時指定了非預設值，則 EF 會假設實體已存在於資料庫中，並會嘗試更新它，而不是插入新的。 若要避免這種關閉值的產生，或查看[如何為產生的屬性指定明確的值](../saving/explicit-values-generated-properties.md)。