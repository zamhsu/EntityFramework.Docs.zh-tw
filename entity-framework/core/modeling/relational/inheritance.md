---
title: 繼承（關係資料庫）-EF Core
description: 如何使用 Entity Framework Core 在關係資料庫中設定實體類型繼承
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/06/2019
uid: core/modeling/relational/inheritance
ms.openlocfilehash: 30e25aa2968ceab03404baddb46e0ae59fc3ea6b
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824751"
---
# <a name="inheritance-relational-database"></a>繼承 (關聯式資料庫)

> [!NOTE]  
> 本節中的組態一般適用於關聯式資料庫。 當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。

EF 模型中的繼承是用來控制實體類別中的繼承在資料庫中的呈現方式。

> [!NOTE]  
> 目前，只有每個階層的資料表（TPH）模式會在 EF Core 中執行。 其他常見的模式（例如，每個類型的資料表（TPT）和每個具體的資料表類型（TPC））尚無法使用。

## <a name="conventions"></a>慣例

根據預設，系統會使用每個階層的資料表（TPH）模式來對應繼承。 TPH 會使用單一資料表來儲存階層中所有類型的資料。 鑒別子資料行是用來識別每個資料列所代表的類型。

只有在模型中明確包含兩個或多個繼承類型時，EF Core 才會設定繼承（如需詳細資訊，請參閱[繼承](../inheritance.md)）。

以下範例顯示簡單的繼承案例，以及使用 TPH 模式儲存在關係資料庫資料表中的資料。 *鑒別*子資料行會識別每個資料列中所儲存的*Blog*類型。

[!code-csharp[Main](../../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs#Model)]

![影像](_static/inheritance-tph-data.png)

>[!NOTE]
> 使用 TPH 對應時，視需要自動將資料庫資料行設為可為 null。

## <a name="data-annotations"></a>資料註釋

您不能使用資料批註來設定繼承。

## <a name="fluent-api"></a>Fluent API

您可以使用流暢的 API 來設定鑒別子資料行的名稱和類型，以及用來識別階層中每個類型的值。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/InheritanceTPHDiscriminator.cs#Inheritance)]

## <a name="configuring-the-discriminator-property"></a>設定鑒別子屬性

在上述範例中，會在階層的基底實體上建立鑒別子做為[陰影屬性](xref:core/modeling/shadow-properties)。 由於它是模型中的屬性，因此可以像其他屬性一樣進行設定。 例如，若要設定預設值時使用的最大長度（依慣例鑒別子）：

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/DefaultDiscriminator.cs#DiscriminatorConfiguration)]

鑒別子也可以對應至實體中的 .NET 屬性，並加以設定。 例如：

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs#NonShadowDiscriminator)]

## <a name="shared-columns"></a>共用資料行

當兩個同級實體類型具有相同名稱的屬性時，預設會將它們對應至兩個不同的資料行。 但是，如果它們相容，則可以對應到相同的資料行：

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs#SharedTPHColumns)]