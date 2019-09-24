---
title: 繼承-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 754be334-dd21-450e-9d22-2591e80012a2
uid: core/modeling/inheritance
ms.openlocfilehash: 1f20c455176b5922364584f8c7688c15a4c3f0f9
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197281"
---
# <a name="inheritance"></a>繼承

EF 模型中的繼承是用來控制實體類別中的繼承在資料庫中的呈現方式。

## <a name="conventions"></a>慣例

依照慣例，由資料庫提供者決定如何在資料庫中表示繼承。 請參閱[繼承（關係資料庫）](relational/inheritance.md) ，以瞭解如何使用關係資料庫提供者來處理這種情況。

只有在模型中明確包含兩個或多個繼承類型時，EF 才會設定繼承。 EF 不會掃描模型中未包含的基底或衍生類型。 您可以在繼承階層架構中公開每個類型的*DbSet<TEntity>*  ，以將類型包含在模型中。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?highlight=3-4&name=Model)]

如果您不想要針對階層中的一或多個實體公開*DbSet<TEntity>*  ，您可以使用流暢的 API 來確保它們包含在模型中。
如果您不依賴慣例，可以使用`HasBaseType`明確地指定基底類型。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceModelBuilder.cs?highlight=7&name=Context)]

> [!NOTE]
> 您可以使用`.HasBaseType((Type)null)`從階層移除實體類型。

## <a name="data-annotations"></a>資料註釋

您不能使用資料批註來設定繼承。

## <a name="fluent-api"></a>Fluent API

用於繼承的流暢 API 取決於您所使用的資料庫提供者。 如需您可以為關係資料庫提供者執行的設定，請參閱[繼承（關係資料庫）](relational/inheritance.md) 。
