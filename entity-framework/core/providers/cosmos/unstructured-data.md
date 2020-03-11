---
title: Azure Cosmos DB 提供者-使用非結構化資料-EF Core
description: 如何使用 Entity Framework Core 處理 Azure Cosmos DB 非結構化資料
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/unstructured-data
ms.openlocfilehash: 69f979d46174ff56310b334f28438ac271f45155
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417189"
---
# <a name="working-with-unstructured-data-in-ef-core-azure-cosmos-db-provider"></a>使用 EF Core Azure Cosmos DB 提供者中的非結構化資料

EF Core 的設計可讓您輕鬆地使用遵循模型中所定義之架構的資料。 不過，Azure Cosmos DB 的其中一個優點是儲存資料圖形的彈性。

## <a name="accessing-the-raw-json"></a>存取原始 JSON

您可以透過名為 `"__jObject"` 的[陰影狀態](../../modeling/shadow-properties.md)中的特殊屬性來存取未追蹤的屬性 EF Core，其中包含的 `JObject` 代表從存放區接收的資料，以及將儲存的資料：

[!code-csharp[Unmapped](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=23,24&name=Unmapped)]

``` json
{
    "Id": 1,
    "PartitionKey": "1",
    "TrackingNumber": null,
    "id": "1",
    "Address": {
        "ShipsToCity": "London",
        "ShipsToStreet": "221 B Baker St"
    },
    "_rid": "eLMaAK8TzkIBAAAAAAAAAA==",
    "_self": "dbs/eLMaAA==/colls/eLMaAK8TzkI=/docs/eLMaAK8TzkIBAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-683e-0a12bf8d01d5\"",
    "_attachments": "attachments/",
    "BillingAddress": "Clarence House",
    "_ts": 1568164374
}
```

> [!WARNING]
> `"__jObject"` 屬性是 EF Core 基礎結構的一部分，而且只應作為最後的手段，因為未來版本中可能會有不同的行為。

> [!NOTE]
> 對實體所做的變更將會覆寫 `SaveChanges`期間儲存在 `"__jObject"` 中的值。

## <a name="using-cosmosclient"></a>使用 CosmosClient

若要完全分離，EF Core 從 `DbContext`取得屬於[AZURE COSMOS DB SDK 一部分](/azure/cosmos-db/sql-api-get-started)的[CosmosClient](/dotnet/api/Microsoft.Azure.Cosmos.CosmosClient)物件：

[!code-csharp[CosmosClient](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=3&name=CosmosClient)]

## <a name="missing-property-values"></a>遺漏屬性值

在上述範例中，我們從訂單中移除 `"TrackingNumber"` 屬性。 因為索引在 Cosmos DB 中的運作方式，所以參考遺漏屬性的查詢不在投影中，而是會傳回非預期的結果。 例如：

[!code-csharp[MissingProperties](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?name=MissingProperties)]

已排序的查詢實際上不會傳回任何結果。 這表示當直接使用存放區時，一定要特別注意一個 EF Core 所對應的屬性。

> [!NOTE]
> 在未來的 Cosmos 版本中，此行為可能會變更。 例如，目前索引編制原則是否定義複合索引 {Id/？ ASC、TrackingNumber/？ ASC）}，則具有 ' ORDER BY c.Id ASC，c. 鑒別子 ASC ' 的查詢__會__傳回遺漏 `"TrackingNumber"` 屬性的專案。
