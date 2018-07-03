---
title: 繼承的 EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 754be334-dd21-450e-9d22-2591e80012a2
ms.technology: entity-framework-core
uid: core/modeling/inheritance
ms.openlocfilehash: f0394bc55dfbfea8277b1ddf898361165dd1fe51
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052778"
---
# <a name="inheritance"></a>繼承

EF 模型中的繼承用來控制如何在實體類別中的繼承表示在資料庫中。

## <a name="conventions"></a>慣例

依照慣例，會決定資料庫提供者，以判斷繼承在資料庫中的呈現方式。 請參閱[繼承 （關聯式資料庫）](relational/inheritance.md)如這與關聯式資料庫提供者的處理方式。

如果兩個或多個繼承的型別會明確地包含在模型中，EF 只會設定繼承。 EF 不會掃描基底或衍生的類型，否則不包含在模型中。 您可以在模型中加入類型，藉由公開*DbSet<TEntity>* 繼承階層中每個類型。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/InheritanceDbSets.cs?highlight=3-4&name=Model)]

如果您不想要公開*DbSet<TEntity>* 對於階層中的一個或多個實體，您可以使用 fluent 應用程式開發的應用程式開發介面，以確保它們會包含在模型中。
如果您不要依賴慣例，您可以指定使用明確的基底類型和`HasBaseType`。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/InheritanceModelBuilder.cs?highlight=7&name=Context)]

> [!NOTE]
> 您可以使用`.HasBaseType((Type)null)`若要從階層中移除實體類型。

## <a name="data-annotations"></a>資料註釋

若要設定繼承，您無法使用資料註解。

## <a name="fluent-api"></a>關於 fluent 應用程式開發介面

繼承 Fluent API 取決於您使用的資料庫提供者。 請參閱[繼承 （關聯式資料庫）](relational/inheritance.md)關聯式資料庫提供者，您可以執行的組態。
