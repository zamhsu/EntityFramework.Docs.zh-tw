---
title: 預設值-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e541366a-130f-47dd-9997-1b110a11febe
uid: core/modeling/relational/default-values
ms.openlocfilehash: b6ac283d551e2c6ee119f7de6933363b5d8793a1
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655909"
---
# <a name="default-values"></a>預設值

> [!NOTE]  
> 本節中的組態一般適用於關聯式資料庫。 當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。

如果插入新的資料列，但未指定資料行的值，則資料行的預設值就是將插入的值。

## <a name="conventions"></a>慣例

依照慣例，預設值為 [未設定]。

## <a name="data-annotations"></a>資料註釋

您不能使用資料批註來設定預設值。

## <a name="fluent-api"></a>Fluent API

您可以使用流暢的 API 來指定屬性的預設值。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/DefaultValue.cs?name=DefaultValue&highlight=9)]

您也可以指定用來計算預設值的 SQL 片段。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/DefaultValueSql.cs?name=DefaultValueSql&highlight=9)]
