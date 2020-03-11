---
title: 繼承-EF Core
description: 如何使用 Entity Framework Core 設定實體類型繼承
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 10/27/2016
uid: core/modeling/inheritance
ms.openlocfilehash: 507854e3acc0347adee612e516b3e2e0b10f55cf
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417292"
---
# <a name="inheritance"></a>繼承

EF 可以將 .NET 類型階層架構對應到資料庫。 這可讓您以平常的程式碼撰寫 .NET 實體，使用基底和衍生類型，並讓 EF 順暢地建立適當的資料庫架構、發出查詢等。類型階層的對應實際詳細資料與提供者相關。此頁面描述關係資料庫內容中的繼承支援。

目前，EF Core 只支援每個階層的資料表（TPH）模式。 TPH 會使用單一資料表來儲存階層中所有類型的資料，而且會使用鑒別子資料行來識別每個資料列所代表的類型。

> [!NOTE]
> EF Core 尚不支援 EF6 所支援的每一類型資料表（TPT）和每個具體的資料表類型（TPC）。 TPT 是規劃 EF Core 5.0 的主要功能。

## <a name="entity-type-hierarchy-mapping"></a>實體類型階層架構對應

依照慣例，只有在模型中明確包含兩個或多個繼承類型時，EF 才會設定繼承。 EF 不會自動掃描模型中未包含的基底或衍生類型。

您可以在繼承階層架構中公開每個類型的 DbSet，以將類型包含在模型中：

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?name=InheritanceDbSets&highlight=3-4)]

此模型會對應至下列資料庫架構（請注意隱含建立的*鑒別*子資料行，其可識別每個資料列中儲存的是哪一種類型的*Blog* ）：

![image](_static/inheritance-tph-data.png)

>[!NOTE]
> 使用 TPH 對應時，視需要自動將資料庫資料行設為可為 null。 例如， *RssUrl*資料行可為 null，因為一般的*Blog*實例沒有該屬性。

如果您不想要針對階層中的一或多個實體公開 DbSet，您也可以使用流暢的 API 來確保它們包含在模型中。

> [!TIP]
> 如果您不依賴慣例，可以使用 `HasBaseType`明確地指定基底類型。 您也可以使用 `.HasBaseType((Type)null)` 從階層中移除實體類型。

## <a name="discriminator-configuration"></a>鑒別子設定

您可以設定鑒別子資料行的名稱和類型，以及用來識別階層中每個類型的值：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorConfiguration.cs?name=DiscriminatorConfiguration&highlight=4-6)]

在上述範例中，EF 已在階層的基底實體上以[陰影屬性](xref:core/modeling/shadow-properties)的形式，隱含地新增鑒別子。 此屬性可以設定為與任何其他內容相同：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorPropertyConfiguration.cs?name=DiscriminatorPropertyConfiguration&highlight=4-5)]

最後，鑒別子也可以對應至實體中的一般 .NET 屬性：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs?name=NonShadowDiscriminator&highlight=4)]

## <a name="shared-columns"></a>共用資料行

根據預設，當階層中的兩個同級實體類型具有相同名稱的屬性時，它們會對應至兩個不同的資料行。 不過，如果其類型相同，則可以對應到相同的資料庫資料行：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs?name=SharedTPHColumns&highlight=9,13)]
