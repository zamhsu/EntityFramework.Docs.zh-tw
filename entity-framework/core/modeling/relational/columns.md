---
title: 資料行對應-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 05a47de9-1078-488e-a823-b516a4208f33
uid: core/modeling/relational/columns
ms.openlocfilehash: eaffc0cc1642f64edabeeef974f5f6de7a23b656
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197209"
---
# <a name="column-mapping"></a>資料行對應

> [!NOTE]  
> 本節中的組態一般適用於關聯式資料庫。 當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。

資料行對應會識別應該在資料庫中從查詢並儲存到哪些資料行資料。

## <a name="conventions"></a>慣例

依照慣例，每個屬性都將設定為對應至名稱與屬性相同的資料行。

## <a name="data-annotations"></a>資料註釋

您可以使用資料批註來設定要對應屬性的資料行。

[!code-csharp[Main](../../../../samples/core/Modeling/DataAnnotations/Relational/Column.cs?highlight=13)]

## <a name="fluent-api"></a>Fluent API

您可以使用流暢的 API 來設定要對應屬性的資料行。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/Column.cs?highlight=11-13)]
