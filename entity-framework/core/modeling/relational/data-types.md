---
title: 資料類型-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9d2e647f-29e4-483b-af00-74269eb06e8f
uid: core/modeling/relational/data-types
ms.openlocfilehash: d667cbcb821e321faed36d097b531c7c55b81248
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149167"
---
# <a name="data-types"></a>資料類型

> [!NOTE]  
> 本節中的組態一般適用於關聯式資料庫。 當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。

「資料類型」指的是對應屬性之資料行的資料庫特定類型。

## <a name="conventions"></a>慣例

依照慣例，資料庫提供者會根據屬性的 .NET 類型來選取資料類型。 它也會考慮其他中繼資料，例如設定的[最大長度](../max-length.md)、屬性是否為主要金鑰的一部分等等。

例如，SQL Server 會使用`datetime2(7)`做`DateTime`為屬性， `nvarchar(max)`並`string`用於屬性（ `nvarchar(450)`或`string`用於做為索引鍵的屬性）。

## <a name="data-annotations"></a>資料註釋

您可以使用資料批註來指定資料行的精確資料類型。

例如`Url` ，下列程式碼會將設定為非 unicode 字串，其最大`200`長度為，並`Rating`將設定`5`為有效位數`2`和小數位數。

[!code-csharp[Main](../../../../samples/core/Modeling/DataAnnotations/Samples/Relational/DataType.cs?name=Entities&highlight=4,6)]

## <a name="fluent-api"></a>Fluent API

您也可以使用流暢的 API 來為數據行指定相同的資料類型。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/DataType.cs?name=Model&highlight=9-10)]
