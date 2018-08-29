---
title: 資料型別-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9d2e647f-29e4-483b-af00-74269eb06e8f
uid: core/modeling/relational/data-types
ms.openlocfilehash: 9060f66c752be01090ce40be6bf3a32f348ce571
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993517"
---
# <a name="data-types"></a>資料類型

> [!NOTE]  
> 本節中的組態一般適用於關聯式資料庫。 當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。

資料類型是指屬性所對應的資料行的資料庫特定型別。

## <a name="conventions"></a>慣例

依照慣例，資料庫提供者，請選取屬性的 CLR 型別為基礎的資料類型。 它也會納入考慮，請在其他中繼資料，例如已設定[最大長度](../max-length.md)、 屬性是否為組件的主索引鍵等。

比方說，SQL Server 會使用`datetime2(7)`for`DateTime`屬性，和`nvarchar(max)`如`string`屬性 (或`nvarchar(450)`的`string`來做為金鑰的屬性)。

## <a name="data-annotations"></a>資料註釋

您可以使用資料註解來指定確切的資料類型資料行。

例如下列程式碼會設定`Url`為非 unicode 字串的最大長度`200`並`Rating`為十進位整數位數為`5`及調整的`2`。

[!code-csharp[Main](../../../../samples/core/Modeling/DataAnnotations/Samples/Relational/DataType.cs?name=Entities&highlight=4,6)]

## <a name="fluent-api"></a>Fluent API

您也可以使用 Fluent API，來指定相同的資料類型的資料行。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/DataType.cs?name=Model&highlight=9-10)]
