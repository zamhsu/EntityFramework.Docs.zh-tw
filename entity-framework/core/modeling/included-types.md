---
title: 包括 & 排除類型-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: cbe6935e-2679-4b77-8914-a8d772240cf1
uid: core/modeling/included-types
ms.openlocfilehash: 1249e71c02e58afe7fe06b3fdcf523dfa0c9b17c
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655732"
---
# <a name="including--excluding-types"></a>包含與排除類型

在模型中包含類型，表示 EF 具有該類型的中繼資料，並會嘗試從資料庫讀取及寫入實例。

## <a name="conventions"></a>慣例

依照慣例，在您的模型中，`DbSet` 屬性中公開的類型會包含在您的模型中。 此外，也包含 `OnModelCreating` 方法中所述的類型。 最後，以遞迴方式探索已探索類型的導覽屬性所找到的任何類型也會包含在模型中。

**例如，在下列程式碼中，會探索所有三種類型：**

* `Blog`，因為它是在內容的 `DbSet` 屬性中公開

* `Post`，因為它是透過 `Blog.Posts` 導覽屬性進行探索

* `AuditEntry`，因為 `OnModelCreating` 中提及

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/IncludedTypes.cs?name=IncludedTypes&highlight=3,7,16)]

## <a name="data-annotations"></a>資料註釋

您可以使用資料批註，從模型中排除型別。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?highlight=20)]

## <a name="fluent-api"></a>Fluent API

您可以使用流暢的 API，從模型中排除型別。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?highlight=12)]
