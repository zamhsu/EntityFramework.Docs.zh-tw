---
title: 並行標記-EF Core
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: bc8b1cb0-befe-4b67-8004-26e6c5f69385
uid: core/modeling/concurrency
ms.openlocfilehash: bfeb611f222f7195fe22d920b452b40cc4addf90
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417285"
---
# <a name="concurrency-tokens"></a>並行語彙基元

> [!NOTE]
> 此頁面記載如何設定並行標記。 如需並行存取控制如何在 EF Core 上運作的詳細說明，以及如何在您的應用程式中處理並行衝突的範例，請參閱[處理平行存取衝突](../saving/concurrency.md)。

設定為並行標記的屬性會用來執行開放式並行存取控制。

## <a name="configuration"></a>組態

### <a name="data-annotations"></a>[資料註解](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Concurrency.cs?name=Concurrency&highlight=5)]

### <a name="fluent-api"></a>[流暢的 API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Concurrency.cs?name=Concurrency&highlight=5)]

***

## <a name="timestamprowversion"></a>時間戳記/rowversion

Timestamp/rowversion 是一個屬性，每次插入或更新資料列時，資料庫就會自動產生新的值。 屬性也會被視為並行標記，以確保當您要更新的資料列在查詢後已變更時，您會收到例外狀況。 精確的詳細資料取決於所使用的資料庫提供者。針對 SQL Server，通常會使用*byte []* 屬性，其會設定為資料庫中的*ROWVERSION*資料行。

您可以將屬性設定為時間戳記/rowversion，如下所示：

### <a name="data-annotations"></a>[資料註解](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Timestamp.cs?name=Timestamp&highlight=7)]

### <a name="fluent-api"></a>[流暢的 API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Timestamp.cs?name=Timestamp&highlight=9,17)]

***
