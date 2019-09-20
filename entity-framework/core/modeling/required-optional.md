---
title: 必要/選用屬性-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ddaa0a54-9f43-4c34-aae3-f95c96c69842
uid: core/modeling/required-optional
ms.openlocfilehash: 7200cd2eeeba2f22365ef09b1f50edd077240130
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149156"
---
# <a name="required-and-optional-properties"></a>必要和選擇性屬性

如果屬性可包含`null`，則會將其視為選擇性。 如果`null`不是要指派給屬性的有效值，則會將它視為必要的屬性。

## <a name="conventions"></a>慣例

依照慣例，其 .net 類型可以包含 null 的屬性會設定為選擇性（`string`、 `int?`、 `byte[]`等）。 CLR 類型不能包含 null 的屬性會設定為必要（`int`、 `decimal`、 `bool`等）。

> [!NOTE]  
> 其 .NET 類型不能包含 null 的屬性無法設定為選擇性。 Entity Framework，一律會將屬性視為必要。

## <a name="data-annotations"></a>資料註釋

您可以使用資料批註來指出屬性是必要的。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Required.cs?highlight=14)]

## <a name="fluent-api"></a>Fluent API

您可以使用流暢的 API 來表示需要屬性。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Required.cs?highlight=11-13)]

