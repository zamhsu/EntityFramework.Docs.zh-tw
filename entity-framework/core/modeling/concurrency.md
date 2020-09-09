---
title: 並行權杖-EF Core
description: 在 Entity Framework Core 模型中設定開放式並行存取控制的並行標記
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: bc8b1cb0-befe-4b67-8004-26e6c5f69385
uid: core/modeling/concurrency
ms.openlocfilehash: 57718a1b0146f437bb4126524889c81ae0dfd895
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617547"
---
# <a name="concurrency-tokens"></a>並行語彙基元

> [!NOTE]
> 此頁面記載如何設定並行存取權杖。 請參閱 [處理平行存取衝突](xref:core/saving/concurrency) ，以取得並行存取控制如何運作 EF Core 的詳細說明，以及如何在應用程式中處理並行衝突的範例。

設定為並行權杖的屬性會用來執行開放式並行存取控制。

## <a name="configuration"></a>設定

### <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Concurrency.cs?name=Concurrency&highlight=5)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Concurrency.cs?name=Concurrency&highlight=5)]

***

## <a name="timestamprowversion"></a>Timestamp/rowversion

Timestamp/rowversion 是一種屬性，它會在每次插入或更新資料列時，自動產生新的值。 屬性也會被視為並行 token，以確保如果您正在更新的資料列自從查詢之後已經變更，就會收到例外狀況。 精確的詳細資料取決於所使用的資料庫提供者;針對 SQL Server，通常會使用 *byte []* 屬性，其會設定為資料庫中的 *ROWVERSION* 資料行。

您可以將屬性設定為 timestamp/rowversion，如下所示：

### <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Timestamp.cs?name=Timestamp&highlight=7)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Timestamp.cs?name=Timestamp&highlight=9,17)]

***
