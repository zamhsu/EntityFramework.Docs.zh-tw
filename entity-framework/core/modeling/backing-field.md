---
title: 支援欄位-EF Core
description: 在 Entity Framework Core 模型中設定屬性的支援欄位
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: 36fc64efa08f2cdeb7005e3fc6335298f2426bde
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617578"
---
# <a name="backing-fields"></a>支援欄位

支援欄位可讓 EF 讀取和/或寫入欄位，而不是屬性。 當使用類別中的封裝來限制應用程式程式碼存取資料的方式時，這會很有用，但在不使用這些限制/增強功能的情況下，應該在資料庫中讀取和/或寫入值。

## <a name="basic-configuration"></a>基本設定

依照慣例，會將下欄欄位探索為指定屬性的支援欄位， (以優先順序) 列出。 

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

在下列範例中， `Url` 屬性已設定為具有 `_url` 其支援欄位：

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

請注意，只有在模型中包含的屬性才會探索支援欄位。 如需有關模型中包含哪些屬性的詳細資訊，請參閱 [包含屬性的 & （包括屬性](xref:core/modeling/entity-properties)）。

您也可以使用 EFCore 5.0) 或流暢 API 中提供的資料批註 (來設定支援欄位，例如，如果功能變數名稱未對應到上述慣例：

### <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a>欄位和屬性存取

根據預設，EF 一律會讀取及寫入支援欄位-假設其中一個已正確設定，而且永遠不會使用屬性。 不過，EF 也支援其他存取模式。 例如，下列範例會指示 EF 只在具體化時寫入支援欄位，並在所有其他情況下使用屬性：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

如需一組完整的支援選項，請參閱 [PropertyAccessMode 列舉](/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) 。

> [!NOTE]
> 使用 EF Core 3.0 時，預設屬性存取模式會從變更 `PreferFieldDuringConstruction` 為 `PreferField` 。

## <a name="field-only-properties"></a>僅限欄位屬性

您也可以在模型中建立在 entity 類別中沒有對應 CLR 屬性的概念屬性，但改為使用欄位將資料儲存在實體中。 這與 [陰影屬性](xref:core/modeling/shadow-properties)不同，因為資料會儲存在變更追蹤器中，而不是儲存在實體的 CLR 型別中。 僅限欄位屬性通常會在實體類別使用方法（而非屬性）取得/設定值時使用，或者，如果欄位不應該在領域模型中公開， (例如主鍵) 。

您可以藉由在 API 中提供名稱來設定僅限欄位的屬性 `Property(...)` ：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

EF 將嘗試尋找具有指定名稱的 CLR 屬性，如果找不到屬性，則為欄位。 如果找不到屬性或欄位，則會改為設定陰影屬性。

您可能需要從 LINQ 查詢參考僅限欄位的屬性，但是這類欄位通常是私用的。 您可以 `EF.Property(...)` 在 LINQ 查詢中使用方法來參考欄位：

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
