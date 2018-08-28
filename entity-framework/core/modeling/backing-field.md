---
title: EF Core 備份欄位-
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: 79221b6f7968675ff10f80d5df181b674b6a20c9
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994089"
---
# <a name="backing-fields"></a>支援欄位

> [!NOTE]  
> 這項功能是在 EF Core 1.1 的新功能。

支援欄位可讓 EF 來讀取和/或寫入的欄位，而不是屬性。 這有助於進行應用程式程式碼封裝在類別中的用來限制的使用和/或增強對資料的存取語意，但值應該是從讀取和/或寫入資料庫，而不需使用這些限制 /增強功能。

## <a name="conventions"></a>慣例

依照慣例，您會探索下列欄位，為支援給定的屬性 （依照優先順序列出） 的欄位。 欄位只會探索模型中包含的屬性。 屬性包含在模型的詳細資訊，請參閱[包含與排除屬性](included-properties.md)。

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/BackingField.cs#Sample)]

設定支援欄位時，EF 會直接寫入該欄位時具體化實體執行個體，從資料庫 （而不使用屬性 setter）。 如果 EF 需要讀取或寫入值，在其他時候，它會盡可能使用屬性。 比方說，如果 EF 必須更新屬性的值，它會使用屬性 setter，如果有定義。 如果屬性是唯讀，則會寫入至欄位。

## <a name="data-annotations"></a>資料註釋

無法設定支援欄位，使用資料註解。

## <a name="fluent-api"></a>Fluent API

您可以使用 Fluent API 來設定屬性的支援欄位。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingField.cs#Sample)]

### <a name="controlling-when-the-field-is-used"></a>控制欄位的使用時機

您可以設定當 EF 所使用的欄位或屬性。 請參閱[PropertyAccessMode 列舉](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode)解支援的選項。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingFieldAccessMode.cs#Sample)]

### <a name="fields-without-a-property"></a>不含屬性的欄位

您也可以在模型中的實體類別，沒有對應的 CLR 屬性，但改為使用欄位，來將資料儲存在實體中建立的概念的屬性。 這點不同於[遮蔽屬性](shadow-properties.md)、 變更追蹤程式中儲存資料。 此外，這通常會用如果實體類別會使用方法來取得或設定值。

您可以提供 EF 中的欄位名稱`Property(...)`API。 如果沒有具有指定名稱的屬性，然後 EF 會尋找欄位。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingFieldNoProperty.cs#Sample)]

您也可以選擇為屬性提供的名稱，而不是欄位名稱。 建立模型時，則會使用此名稱，最值得注意的是它會使用對應至資料庫中的資料行名稱。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingFieldConceptualProperty.cs#Sample)]

當這個實體類別中沒有任何屬性時，您可以使用`EF.Property(...)`方法在 LINQ 查詢中參考的概念模型的一部分的屬性。

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "Url"));
```
