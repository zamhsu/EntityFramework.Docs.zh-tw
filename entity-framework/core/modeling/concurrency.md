---
title: 並行語彙基元-EF Core
author: rowanmiller
ms.date: 03/03/2018
ms.assetid: bc8b1cb0-befe-4b67-8004-26e6c5f69385
uid: core/modeling/concurrency
ms.openlocfilehash: 0051d416544a11385f99d36e45843c5b20725af7
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994222"
---
# <a name="concurrency-tokens"></a>並行語彙基元

> [!NOTE]
> 此頁面說明如何設定並行語彙基元。 請參閱[處理並行存取衝突](../saving/concurrency.md)的並行存取控制的 EF Core 及如何處理您的應用程式中的並行存取衝突的範例的運作方式的詳細說明。

屬性設定為並行語彙基元用來實作開放式並行存取控制。

## <a name="conventions"></a>慣例

依照慣例，屬性會永遠不會設定為並行語彙基元。

## <a name="data-annotations"></a>資料註釋

若要將屬性設定為並行語彙基元，您可以使用資料註解。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Concurrency.cs#ConfigureConcurrencyAnnotations)]

## <a name="fluent-api"></a>Fluent API

您可以使用 Fluent API，若要將屬性設定為並行語彙基元。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Concurrency.cs#ConfigureConcurrencyFluent)]

## <a name="timestamprow-version"></a>時間戳記/資料列版本

時間戳記是屬性，其中一個新的值由資料庫產生每次插入或更新資料列。 屬性也會被視為並行語彙基元。 這可確保如果其他人已修改的資料列，您想要更新您查詢資料後，您會收到例外狀況。

如何達成這是由所使用的資料庫提供者。 SQL Server 的時間戳記通常用在*byte []* 屬性，將會設定為*ROWVERSION*資料庫中的資料行。

### <a name="conventions"></a>慣例

依照慣例，屬性會永遠不會設定為時間戳記。

### <a name="data-annotations"></a>資料註釋

若要將屬性設定為時間戳記，您可以使用資料註解。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Timestamp.cs#ConfigureTimestampAnnotations)]

### <a name="fluent-api"></a>Fluent API

您可以使用 Fluent API 來設定的屬性為時間戳記。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Timestamp.cs#ConfigureTimestampFluent)]
