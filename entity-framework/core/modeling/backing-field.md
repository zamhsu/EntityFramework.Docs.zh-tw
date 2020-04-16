---
title: 支援欄位 - EF 核心
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: d6faf7c0a07c7b855c1ce402ae29e19c80b9d3b4
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434171"
---
# <a name="backing-fields"></a>支援欄位

備份欄位允許 EF 讀取和/或寫入欄位而不是屬性。 當類中的封裝用於限制和/或增強應用程式代碼訪問數據的語義時,這非常有用,但該值應從資料庫讀取和/或寫入資料庫,而無需使用這些限制/增強功能。

## <a name="basic-configuration"></a>基本設定

按照慣例,以下欄位將發現為給定屬性的後備欄位(按優先順序順序順序出)。 

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

在下面的範例中,`Url`屬性設定`_url`為 備份欄位:

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

請注意,僅對模型中包含的屬性發現支援欄位。 有關模型中包含哪些屬性的詳細資訊,請參閱[包括&排除屬性](included-properties.md)。

您還可以使用資料註解或 Fluent API 設定備份欄位,例如,如果欄位名稱與上述約定不對應:

### <a name="data-annotations"></a>[資料註解](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a>欄位與屬性存取

默認情況下,EF 將始終讀取和寫入備份欄位(假設一個字段已正確配置)並且永遠不會使用該屬性。 但是,EF 還支援其他訪問模式。 例如,以下範例指示 EF 僅在具體化時寫入備份欄位,並在所有其他情況下使用 該屬性:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

有關支援的完整選項集,請參考[屬性存取模式的枚舉](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode)。

> [!NOTE]
> 使用 EF Core 3.0`PreferFieldDuringConstruction`時`PreferField`,預設屬性 存取模式從變更為 。

## <a name="field-only-properties"></a>只欄位屬性

您還可以在模型中創建一個概念屬性,該屬性在實體類中沒有相應的 CLR 屬性,而是使用欄位將數據存儲在實體中。 這與[陰影屬性](shadow-properties.md)不同,其中數據存儲在更改跟蹤器中,而不是存儲在實體的 CLR 類型中。 當實體類使用方法而不是屬性來獲取/設置值時,或者在域模型中根本不不應公開欄位(例如主鍵)的情況下,通常使用僅欄位屬性。

透過`Property(...)`API 中提供名稱來設定僅欄位屬性:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

如果找不到屬性,EF 將嘗試查找具有給定名稱的 CLR 屬性,或者查找欄位。 如果未找到屬性,也找不到欄位,則將改為設置陰影屬性。

您可能需要從 LINQ 查詢中引用僅欄位屬性,但此類欄位通常是私有的。 可以使用 LINQ`EF.Property(...)`查詢中的方法參考該欄位:

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
