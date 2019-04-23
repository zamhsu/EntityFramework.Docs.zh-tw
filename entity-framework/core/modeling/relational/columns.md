---
title: 資料行對應的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 05a47de9-1078-488e-a823-b516a4208f33
uid: core/modeling/relational/columns
ms.openlocfilehash: 22fcafbfcf9daf765c94e6ca9c42d7770d3e7d07
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929858"
---
# <a name="column-mapping"></a>資料行對應

> [!NOTE]  
> 本節中的組態一般適用於關聯式資料庫。 當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。

資料行對應會識別哪一個資料行的資料應該從查詢，並儲存到資料庫中。

## <a name="conventions"></a>慣例

依照慣例，請將對應至名稱與屬性相同的資料行設定的每個屬性。

## <a name="data-annotations"></a>資料註釋

若要設定屬性所對應的資料行，您可以使用資料註解。

[!code-csharp[Main](../../../../samples/core/Modeling/DataAnnotations/Samples/Relational/Column.cs?highlight=13)]

## <a name="fluent-api"></a>Fluent API

您可以使用 Fluent API 來設定屬性所對應的資料行。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/Column.cs?highlight=11-13)]
