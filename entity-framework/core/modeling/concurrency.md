---
title: 並行語彙基元-EF Core
author: rowanmiller
ms.author: divega
ms.date: 03/03/2018
ms.assetid: bc8b1cb0-befe-4b67-8004-26e6c5f69385
ms.technology: entity-framework-core
uid: core/modeling/concurrency
ms.openlocfilehash: f3cf28d5c54e63aa76058e9fe1d9f3de5b37d579
ms.sourcegitcommit: 8f3be0a2a394253efb653388ec66bda964e5ee1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2018
ms.locfileid: "29745474"
---
# <a name="concurrency-tokens"></a>並行語彙基元

> [!NOTE]
> 此頁面說明如何設定並行語彙基元。 請參閱[處理並行存取衝突](../saving/concurrency.md)的並行存取控制的 EF Core 及如何處理您的應用程式中的並行存取衝突的範例的運作方式的詳細說明。

屬性設定為並行語彙基元可用於實作開放式並行存取控制。

## <a name="conventions"></a>慣例

依照慣例，屬性會永遠不會設定為並行語彙基元。

## <a name="data-annotations"></a>資料註釋

您可以使用資料註解將屬性設定為並行語彙基元。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Concurrency.cs#ConfigureConcurrencyAnnotations)]

## <a name="fluent-api"></a>Fluent API

您可以使用 fluent 應用程式開發的應用程式開發介面，將屬性設定為並行語彙基元。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Concurrency.cs#ConfigureConcurrencyFluent)]

## <a name="timestamprow-version"></a>時間戳記/資料列版本

時間戳記是屬性，產生新的值是由資料庫每次插入或更新資料列。 屬性也會被視為並行語彙基元。 這可確保如果其他人已修改的資料列，您嘗試更新您查詢的資料後，就會收到例外狀況。

如何達成這會決定所使用的資料庫提供者。 SQL Server 的時間戳記通常用在*byte []* 屬性，將會安裝為*ROWVERSION*資料庫中的資料行。

### <a name="conventions"></a>慣例

依照慣例，屬性會永遠不會設定為時間戳記。

### <a name="data-annotations"></a>資料註釋

您可以使用資料註解屬性設定為時間戳記。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Timestamp.cs#ConfigureTimestampAnnotations)]

### <a name="fluent-api"></a>Fluent API

您可以使用 fluent 應用程式開發的應用程式開發介面的屬性設定為時間戳記。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Timestamp.cs#ConfigureTimestampFluent)]
