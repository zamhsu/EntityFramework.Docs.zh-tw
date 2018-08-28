---
title: 繼承的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 754be334-dd21-450e-9d22-2591e80012a2
uid: core/modeling/inheritance
ms.openlocfilehash: c5fa9d13dec8cfc3e1cac69e471f509cbbb9e4c5
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995892"
---
# <a name="inheritance"></a>繼承

EF 模型中的繼承用來控制如何將實體類別中的繼承表示在資料庫中。

## <a name="conventions"></a>慣例

依照慣例，決定以決定如何將繼承表示在資料庫中的資料庫提供者。 請參閱[繼承 （關聯式資料庫）](relational/inheritance.md)針對此與關聯式資料庫提供者的處理方式。

如果模型中明確包含兩個或多個繼承的類型，EF 將只會設定繼承。 EF 會掃描基底或衍生的類型，否則為未包含在模型中。 您可以在模型中包含類型，藉由公開*DbSet<TEntity>* 繼承階層架構中每個類型。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/InheritanceDbSets.cs?highlight=3-4&name=Model)]

如果您不想要公開*DbSet<TEntity>* 對於階層中的一或多個實體，您可以使用 Fluent API，以確保它們會包含在模型。
如果您不要依賴慣例，您就可以指定使用明確的基底類型和`HasBaseType`。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/InheritanceModelBuilder.cs?highlight=7&name=Context)]

> [!NOTE]
> 您可以使用`.HasBaseType((Type)null)`若要從階層中移除的實體類型。

## <a name="data-annotations"></a>資料註釋

您無法使用資料註解來設定繼承。

## <a name="fluent-api"></a>Fluent API

適用於繼承的 Fluent API 取決於您使用的資料庫提供者。 請參閱[繼承 （關聯式資料庫）](relational/inheritance.md)關聯式資料庫提供者，您可以執行的組態。
