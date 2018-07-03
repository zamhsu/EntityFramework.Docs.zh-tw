---
title: 資料型別-EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 9d2e647f-29e4-483b-af00-74269eb06e8f
ms.technology: entity-framework-core
uid: core/modeling/relational/data-types
ms.openlocfilehash: fd4668a3f9554eb9d3b1161d5dddce2fcdcac712
ms.sourcegitcommit: 860ec5d047342fbc4063a0de881c9861cc1f8813
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2017
ms.locfileid: "26053498"
---
# <a name="data-types"></a>資料類型

> [!NOTE]  
> 本節中的設定是一般適用於關聯式資料庫。 當您安裝的關聯式資料庫提供者，如下所示的擴充方法會變成可用 (因為共用*Microsoft.EntityFrameworkCore.Relational*封裝)。

資料類型是指屬性所對應的資料行的資料庫特定型別。

## <a name="conventions"></a>慣例

依照慣例，資料庫提供者，請選取屬性的 CLR 型別為基礎的資料類型。 它也會考量其他中繼資料，例如設定[最大長度](../max-length.md)、 屬性是否為組件的主索引鍵等。

例如，SQL Server 會使用`datetime2(7)`如`DateTime`屬性，和`nvarchar(max)`的`string`屬性 (或`nvarchar(450)`的`string`用做為索引鍵屬性)。

## <a name="data-annotations"></a>資料註釋

您可以使用資料註解來指定確切的資料類型資料行。

下列程式碼的設定，例如`Url`為最大長度的非 unicode 字串`200`和`Rating`的有效位數為 decimal`5`及縮放的`2`。

[!code-csharp[Main](../../../../samples/core/Modeling/DataAnnotations/Samples/Relational/DataType.cs?name=Entities&highlight=4,6)]

## <a name="fluent-api"></a>關於 fluent 應用程式開發介面

您也可以使用 fluent 應用程式開發的應用程式開發介面來指定相同的資料類型的資料行。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/DataType.cs?name=Model&highlight=9-10)]
