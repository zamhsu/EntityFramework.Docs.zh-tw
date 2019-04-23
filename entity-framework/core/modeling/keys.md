---
title: 索引鍵 （主要）-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 912ffef7-86a0-4cdc-a776-55f907459d20
uid: core/modeling/keys
ms.openlocfilehash: 51d163b867085f42f415dbd7afa9e311ab1781a0
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929832"
---
# <a name="keys-primary"></a>索引鍵 （主要）

索引鍵做為主要的唯一識別碼，每個實體執行個體。 使用關聯式資料庫時這會對應至的概念*主索引鍵*。 您也可以設定不是主索引鍵的唯一識別碼 (請參閱[替代索引鍵](alternate-keys.md)如需詳細資訊)。 

其中一種下列方法可用來安裝/建立主索引鍵。

## <a name="conventions"></a>慣例

依照慣例，屬性名為`Id`或`<type name>Id`會設定為實體的索引鍵。

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/KeyId.cs?highlight=3)] -->
``` csharp
class Car
{
    public string Id { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/KeyTypeNameId.cs?highlight=3)] -->
``` csharp
class Car
{
    public string CarId { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```

## <a name="data-annotations"></a>資料註釋

您可以使用資料註解來設定實體的索引鍵的單一屬性。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/KeySingle.cs?highlight=13)]

## <a name="fluent-api"></a>Fluent API

您可以使用 Fluent API 來設定實體的索引鍵的單一屬性。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/KeySingle.cs?highlight=11,12)]

您也可以使用 Fluent API 來設定多個屬性 （又稱為複合索引鍵） 的實體索引鍵。 複合索引鍵可以只設定使用 Fluent API-慣例永遠不會設定複合索引鍵而且您可以使用資料註解來設定其中一個。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/KeyComposite.cs?highlight=11,12)]
