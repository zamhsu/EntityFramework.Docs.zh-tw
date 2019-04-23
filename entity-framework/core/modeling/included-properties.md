---
title: 包含與排除內容-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e9dff604-3469-4a05-8f9e-18ac281d82a9
uid: core/modeling/included-properties
ms.openlocfilehash: 022534091bb48e491c8808791a401216a339d7b0
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929819"
---
# <a name="including--excluding-properties"></a>包含與排除屬性

包括在模型中的屬性，表示 EF 具有該屬性的相關中繼資料，並會嘗試讀取和寫入，或將資料庫的值。

## <a name="conventions"></a>慣例

依照慣例，getter 和 setter 的公用屬性，將會包含在模型中。

## <a name="data-annotations"></a>資料註釋

若要從模型中排除的屬性，您可以使用資料註解。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/IgnoreProperty.cs?highlight=17)]

## <a name="fluent-api"></a>Fluent API

您可以使用 Fluent API，若要從模型中排除的屬性。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/IgnoreProperty.cs?highlight=12,13)]
