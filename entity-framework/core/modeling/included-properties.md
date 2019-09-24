---
title: 包括 & 排除屬性-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e9dff604-3469-4a05-8f9e-18ac281d82a9
uid: core/modeling/included-properties
ms.openlocfilehash: cd111af891ef0bbaccf515eed0c1991f105bd362
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197420"
---
# <a name="including--excluding-properties"></a>包含與排除屬性

將屬性包含在模型中，表示 EF 具有該屬性的相關中繼資料，並會嘗試讀取及寫入資料庫的值。

## <a name="conventions"></a>慣例

依照慣例，具有 getter 和 setter 的公用屬性將會包含在模型中。

## <a name="data-annotations"></a>資料註釋

您可以使用資料批註，從模型中排除屬性。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreProperty.cs?highlight=17)]

## <a name="fluent-api"></a>Fluent API

您可以使用流暢的 API，從模型中排除屬性。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreProperty.cs?highlight=12,13)]
