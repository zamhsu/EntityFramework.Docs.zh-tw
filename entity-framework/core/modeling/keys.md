---
title: 金鑰（主要）-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 912ffef7-86a0-4cdc-a776-55f907459d20
uid: core/modeling/keys
ms.openlocfilehash: 8b32bf6417890a954c933a5973a2c90c609beeca
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197277"
---
# <a name="keys-primary"></a>索引鍵 (主要)

索引鍵可作為每個實體實例的主要唯一識別碼。 當使用關係資料庫時，這會對應至*主鍵*的概念。 您也可以設定非主要金鑰的唯一識別碼（如需詳細資訊，請參閱[替代金鑰](alternate-keys.md)）。 

您可以使用下列其中一種方法來設定/建立主要金鑰。

## <a name="conventions"></a>慣例

依照慣例，名為`Id`或`<type name>Id`的屬性會設定為實體的索引鍵。

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/KeyId.cs?highlight=3)] -->
``` csharp
class Car
{
    public string Id { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/KeyTypeNameId.cs?highlight=3)] -->
``` csharp
class Car
{
    public string CarId { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```

## <a name="data-annotations"></a>資料註釋

您可以使用資料批註，將單一屬性設定為實體的索引鍵。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/KeySingle.cs?highlight=13)]

## <a name="fluent-api"></a>Fluent API

您可以使用流暢的 API，將單一屬性設定為實體的索引鍵。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeySingle.cs?highlight=11,12)]

您也可以使用流暢的 API，將多個屬性設定為實體的索引鍵（也稱為複合索引鍵）。 複合索引鍵只能使用流暢的 API 慣例來設定複合索引鍵，而且您不能使用資料批註來設定它。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyComposite.cs?highlight=11,12)]
