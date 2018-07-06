---
title: EF Core 備份欄位-
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
ms.technology: entity-framework-core
uid: core/modeling/backing-field
ms.openlocfilehash: e95417b3368d09a64f9ec02ae40c38dc770c2e6b
ms.sourcegitcommit: 860ec5d047342fbc4063a0de881c9861cc1f8813
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2017
ms.locfileid: "26053458"
---
# <a name="backing-fields"></a>支援欄位

> [!NOTE]  
> 這項功能是在 EF Core 1.1 的新功能。

支援欄位允許 EF 讀取及/或寫入欄位，而不是屬性。 這有助於進行應用程式碼，在類別中的封裝 （encapsulation） 用來限制的使用及/或加強周圍資料的存取權的語意，但值應該進行讀取及/或寫入資料庫，而不使用這些限制 /增強功能。

## <a name="conventions"></a>慣例

依照慣例，會探索下列欄位做為備份給定的屬性 （依照優先順序列出） 的欄位。 欄位只會探索模型中包含的屬性。 屬性包含在模型的詳細資訊，請參閱[包括 & 排除內容](included-properties.md)。

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/BackingField.cs#Sample)]

設定支援欄位時，EF 會直接寫入該欄位時具體化實體執行個體從資料庫 （而非使用屬性 setter）。 如果 EF 需要讀取或寫入值，在其他時候，它會盡可能使用屬性。 比方說，如果 EF 需要更新屬性的值，它會使用屬性 setter，如果有定義。 如果屬性是唯讀，則會寫入至欄位。

## <a name="data-annotations"></a>資料註釋

無法設定支援的欄位，使用資料註解。

## <a name="fluent-api"></a>關於 fluent 應用程式開發介面

您可以使用 fluent 應用程式開發的應用程式開發介面來設定屬性的支援欄位。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingField.cs#Sample)]

### <a name="controlling-when-the-field-is-used"></a>控制當使用此欄位

您可以設定當 EF 使用的欄位或屬性。 請參閱[PropertyAccessMode 列舉](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode)解支援的選項。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingFieldAccessMode.cs#Sample)]

### <a name="fields-without-a-property"></a>欄位沒有屬性

您也可以在實體類別中，沒有對應的 CLR 屬性，但改為使用欄位來儲存在實體中的資料在模型中建立概念的屬性。 這點不同於[遮蔽屬性](shadow-properties.md)、 變更追蹤程式中儲存資料。 此外，這通常會用如果實體類別會使用方法來取得或設定值。

您可以提供 EF 中的欄位名稱`Property(...)`應用程式開發介面。 如果沒有具有指定名稱的屬性，然後 EF 會尋找的欄位。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingFieldNoProperty.cs#Sample)]

您也可以選擇輸入屬性的名稱，而不是欄位名稱。 建立模型時，會再使用此名稱，最值得注意的是它會使用對應至資料庫中的資料行名稱。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingFieldConceptualProperty.cs#Sample)]

當實體類別中沒有任何屬性時，您可以使用`EF.Property(...)`在 LINQ 查詢中參考的概念模型中的部分屬性的方法。

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "Url"));
```
