---
title: 必要/選用的屬性-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ddaa0a54-9f43-4c34-aae3-f95c96c69842
uid: core/modeling/required-optional
ms.openlocfilehash: 564d9e62e2ed4f1a52b569630ed4994529e31dc1
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929806"
---
# <a name="required-and-optional-properties"></a>必要和選擇性的屬性

屬性會被視為選擇性是否有效，才能包含`null`。 如果`null`不是有效的值指派給屬性，則它會被視為是必要的屬性。

## <a name="conventions"></a>慣例

依照慣例，其 CLR 型別可以包含 null 的屬性會設定為選擇性 (`string`， `int?`， `byte[]`，依此類推。)。 將設定其 CLR 類型不能包含 null 的屬性視需要而定 (`int`， `decimal`， `bool`，依此類推。)。

> [!NOTE]  
> CLR 類型不能包含 null 的屬性無法設定為選擇性。 屬性會一律視為 Entity Framework 所需。

## <a name="data-annotations"></a>資料註釋

您可以使用資料註解表示是必要屬性。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Required.cs?highlight=14)]

## <a name="fluent-api"></a>Fluent API

您可以使用 Fluent API，以表示所需的屬性。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Required.cs?highlight=11-13)]

