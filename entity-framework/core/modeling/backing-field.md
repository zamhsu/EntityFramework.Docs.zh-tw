---
title: 支援欄位-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: 288440a4494117fe59d27187e24424c4d2fd44ab
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72811872"
---
# <a name="backing-fields"></a>支援欄位

> [!NOTE]  
> 這項功能在 EF Core 1.1 中是新的。

支援欄位可以讓 EF 讀取及/或寫入欄位，而不是屬性。 當使用類別中的封裝來限制和/或透過應用程式代碼存取資料時，這會很有用，但應該在不使用這些限制的情況下，從資料庫讀取和/或寫入值增強.

## <a name="conventions"></a>慣例

依照慣例，會將下欄欄位探索為指定屬性的支援欄位（依優先順序列出）。 只有模型中包含的屬性才會探索欄位。 如需模型中包含哪些屬性的詳細資訊，請參閱[包含 & 排除屬性](included-properties.md)。

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

當支援欄位已設定時，EF 會在從資料庫具體化實體實例時直接寫入該欄位（而不是使用屬性 setter）。 如果 EF 需要在其他時間讀取或寫入值，則會盡可能使用屬性。 例如，如果 EF 需要更新屬性的值，則會使用屬性 setter （如果有定義的話）。 如果屬性是唯讀的，則它會寫入至欄位。

## <a name="data-annotations"></a>資料註釋

無法使用資料批註來設定支援欄位。

## <a name="fluent-api"></a>Fluent API

您可以使用流暢的 API 來設定屬性的支援欄位。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs#Sample)]

### <a name="controlling-when-the-field-is-used"></a>控制使用欄位的時機

您可以設定 EF 使用欄位或屬性的時機。 如需支援的選項，請參閱[PropertyAccessMode 列舉](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode)。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs#Sample)]

### <a name="fields-without-a-property"></a>沒有屬性的欄位

您也可以在模型中建立概念屬性，其在實體類別中沒有對應的 CLR 屬性，而是使用欄位將資料儲存在實體中。 這與[陰影屬性](shadow-properties.md)不同，其中的資料會儲存在變更追蹤器中。 如果實體類別使用方法來取得/設定值，通常會使用此專案。

您可以為 EF 提供 `Property(...)` API 中的功能變數名稱。 如果沒有具有指定名稱的屬性，則 EF 會尋找欄位。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

當實體類別中沒有屬性時，您可以在 LINQ 查詢中使用 `EF.Property(...)` 方法，來參考概念上為模型一部分的屬性。

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
