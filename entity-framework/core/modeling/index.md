---
title: 建立和設定模型 - EF Core
author: rowanmiller
ms.date: 11/05/2019
ms.assetid: 88253ff3-174e-485c-b3f8-768243d01ee1
uid: core/modeling/index
ms.openlocfilehash: 0f44d9684ca5c8435d83085f9038860309bd82a2
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "78412773"
---
# <a name="creating-and-configuring-a-model"></a>建立和設定模型

Entity Framework 會使用一組慣例，根據您實體類別的圖形建置模型。 您可以指定其他組態來補充及 (或) 覆寫慣例探索到的項目。

本文涵蓋可套用至將目標設為任何資料存放區之模型的組態，以及將目標設為任何關聯式資料庫時可套用的組態。 提供者也可以啟用特定資料存放區專屬的組態。 如需提供者專屬組態的文件，請參閱 [資料庫提供者](../providers/index.md) 一節。

> [!TIP]  
> 您可以在 GitHub 上檢視本文的 [範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples) 。

## <a name="use-fluent-api-to-configure-a-model"></a>使用 Fluent API 設定模型

您可以覆寫衍生內容中的  `OnModelCreating` 方法，並用  `ModelBuilder API` 來設定模型。 這是最強大的組態方法，讓您能夠指定組態而不修改實體類別。 Fluent API 組態具有最高的優先順序，且會覆寫慣例及資料註解。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?highlight=12-14)]

## <a name="use-data-annotations-to-configure-a-model"></a>使用資料註解

您也可以將屬性 (又稱資料註解) 套用到類別及屬性。 資料註解會覆寫慣例，但會受到 Fluent API 組態覆寫。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?highlight=15)]
