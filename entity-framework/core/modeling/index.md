---
title: 建立和設定模型 - EF Core
description: 使用 Entity Framework Core 建立及設定模型的概觀
author: AndriySvyryd
ms.date: 10/13/2020
uid: core/modeling/index
ms.openlocfilehash: b18db0059efd335abe2fc44bbc78e0106717e058
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429607"
---
# <a name="creating-and-configuring-a-model"></a>建立和設定模型

Entity Framework 會使用一組慣例，根據您實體類別的圖形建置模型。 您可以指定其他組態來補充及 (或) 覆寫慣例探索到的項目。

本文涵蓋可套用至將目標設為任何資料存放區之模型的組態，以及將目標設為任何關聯式資料庫時可套用的組態。 提供者也可以啟用特定資料存放區專屬的組態。 針對提供者專屬組態的文件，請參閱[資料庫提供者](xref:core/providers/index)一節。

> [!TIP]  
> 您可以在 GitHub 上查看這篇文章的 [範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples) 。

## <a name="use-fluent-api-to-configure-a-model"></a>使用 Fluent API 設定模型

您可以覆寫衍生內容中的 `OnModelCreating` 方法，並使用 `ModelBuilder API` 來設定模型。 這是最強大的組態方法，讓您能夠指定組態而不修改實體類別。 Fluent API 組態具有最高的優先順序，且會覆寫慣例及資料註解。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?highlight=12-14)]

### <a name="grouping-configuration"></a>群組設定

若要減少方法的大小， <xref:System.Data.Entity.DbContext.OnModelCreating%2A> 可以將實體類型的所有設定都解壓縮到個別的類別來執行 <xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601> 。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=IEntityTypeConfiguration)]

然後，直接 `Configure` 從叫用方法 `OnModelCreating` 。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=ApplyIEntityTypeConfiguration)]

您可以套用在指定元件中執行的型別中所指定的所有設定 `IEntityTypeConfiguration` 。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=ApplyConfigurationsFromAssembly)]

> [!NOTE]
> 設定的套用順序是未定義的，因此，只有當訂單不重要時，才應該使用此方法。

## <a name="use-data-annotations-to-configure-a-model"></a>使用資料註解

您也可以將屬性 (又稱資料註解) 套用到類別及屬性。 資料註解會覆寫慣例，但會受到 Fluent API 組態覆寫。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?highlight=15)]
