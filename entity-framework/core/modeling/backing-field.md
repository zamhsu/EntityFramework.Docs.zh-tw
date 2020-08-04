---
title: 支援欄位-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: e015c4f3fca767d25bee179c027813bd9fcf4c07
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526754"
---
# <a name="backing-fields"></a>支援欄位

支援欄位可以讓 EF 讀取及/或寫入欄位，而不是屬性。 當使用類別中的封裝來限制和/或透過應用程式代碼存取資料時，這項功能會很有用，但在不使用這些限制/增強功能的情況下，應該從資料庫讀取和/或寫入此值。

## <a name="basic-configuration"></a>基本設定

依照慣例，會將下欄欄位探索為指定屬性的支援欄位（依優先順序列出）。 

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

在下列範例中， `Url` 屬性已設定為， `_url` 其支援欄位如下：

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

請注意，只有模型中包含的屬性才會探索支援欄位。 如需模型中包含哪些屬性的詳細資訊，請參閱[包含 & 排除屬性](included-properties.md)。

您也可以使用資料批註（可在 EFCore 5.0 中取得）或流暢的 API 來設定支援欄位，例如，如果功能變數名稱未對應到上述慣例：

### <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a>欄位和屬性存取

根據預設，EF 一律會讀取和寫入支援欄位（假設其中一個已正確設定），而且永遠不會使用屬性。 不過，EF 也支援其他存取模式。 例如，下列範例會指示 EF 僅在具體化時寫入支援欄位，並在所有其他情況下使用屬性：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

如需一組完整的支援選項，請參閱[PropertyAccessMode 列舉](/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode)。

> [!NOTE]
> 使用 EF Core 3.0，預設屬性存取模式從變更 `PreferFieldDuringConstruction` 為 `PreferField` 。

## <a name="field-only-properties"></a>僅限欄位屬性

您也可以在模型中建立概念屬性，其在實體類別中沒有對應的 CLR 屬性，而是使用欄位將資料儲存在實體中。 這與[陰影屬性](shadow-properties.md)不同，其中的資料會儲存在變更追蹤程式中，而不是實體的 CLR 型別中。 僅限欄位屬性通常會在實體類別使用方法（而非屬性）來取得/設定值，或在網域模型中不應該公開欄位（例如主鍵）的情況下使用。

您可以在 API 中提供名稱，以設定僅限欄位的屬性 `Property(...)` ：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

EF 會嘗試尋找具有指定名稱的 CLR 屬性，如果找不到屬性，則為欄位。 如果找不到屬性和欄位，則會改為設定陰影屬性。

您可能需要從 LINQ 查詢參考僅限欄位的屬性，但這類欄位通常是私用的。 您可以 `EF.Property(...)` 在 LINQ 查詢中使用方法來參考欄位：

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
