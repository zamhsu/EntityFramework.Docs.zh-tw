---
title: 明確載入相關資料-EF Core
description: 使用 Entity Framework Core 明確載入相關資料
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/explicit
ms.openlocfilehash: 4cfc11237b498f5357476ee4ad96fdc279cd3fee
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90078927"
---
# <a name="explicit-loading-of-related-data"></a>明確載入相關資料

## <a name="explicit-loading"></a>明確式載入

您可以透過 `DbContext.Entry(...)` API 來明確地載入導覽屬性。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

您也可以透過執行會傳回相關實體的個別查詢，來明確地載入導覽屬性。 如果已啟用變更追蹤，則當查詢具體化實體時，EF Core 會自動將新載入之實體的導覽屬性設定為參考已載入的任何實體，並將已載入之實體的導覽屬性設定為參考新載入的實體。

## <a name="querying-related-entities"></a>查詢相關實體

您也可以取得表示導覽屬性內容的 LINQ 查詢。

它可讓您在查詢上套用其他運算子。 下限範例：在相關實體上套用匯總運算子，而不將其載入記憶體中。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

您也可以篩選要將哪些相關實體載入至記憶體。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]
