---
title: 並行語彙基元-EF Core
author: rowanmiller
ms.date: 03/03/2018
ms.assetid: bc8b1cb0-befe-4b67-8004-26e6c5f69385
uid: core/modeling/concurrency
ms.openlocfilehash: db768c1de99000be91d33764ccd3c3924237f8bb
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197461"
---
# <a name="concurrency-tokens"></a>並行語彙基元

> [!NOTE]
> 此頁面記載如何設定並行標記。 請參閱[處理並行存取衝突](../saving/concurrency.md)的並行存取控制的 EF Core 及如何處理您的應用程式中的並行存取衝突的範例的運作方式的詳細說明。

設定為並行標記的屬性會用來執行開放式並行存取控制。

## <a name="conventions"></a>慣例

依照慣例，屬性永遠不會設定為並行標記。

## <a name="data-annotations"></a>資料註釋

您可以使用資料批註，將屬性設定為並行標記。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Concurrency.cs#ConfigureConcurrencyAnnotations)]

## <a name="fluent-api"></a>Fluent API

您可以使用流暢的 API 將屬性設定為並行標記。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Concurrency.cs#ConfigureConcurrencyFluent)]

## <a name="timestamprow-version"></a>時間戳記/資料列版本

時間戳記是一個屬性，在每次插入或更新資料列時，資料庫就會產生新的值。 屬性也會被視為並行標記。 這可確保當其他人在您查詢資料之後，修改了您嘗試更新的資料列時，您會收到例外狀況。

如何達成此目的是要使用的資料庫提供者。 針對 SQL Server，時間戳記通常用於*byte []* 屬性，它會設定為資料庫中的*ROWVERSION*資料行。

### <a name="conventions"></a>慣例

依照慣例，屬性永遠不會設定為時間戳記。

### <a name="data-annotations"></a>資料註釋

您可以使用資料批註，將屬性設定為時間戳記。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Timestamp.cs#ConfigureTimestampAnnotations)]

### <a name="fluent-api"></a>Fluent API

您可以使用流暢的 API，將屬性設定為時間戳記。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Timestamp.cs#ConfigureTimestampFluent)]
