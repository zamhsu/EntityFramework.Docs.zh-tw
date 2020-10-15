---
title: Azure Cosmos DB 提供者-使用非結構化資料-EF Core
description: 如何使用 Entity Framework Core 處理 Azure Cosmos DB 非結構化資料
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/unstructured-data
ms.openlocfilehash: ac497c9f5540557b931db935f4f3ca480edf010d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064020"
---
# <a name="working-with-unstructured-data-in-ef-core-azure-cosmos-db-provider"></a>使用 EF Core Azure Cosmos DB 提供者中的非結構化資料

EF Core 的設計可讓您輕鬆地使用在模型中定義之架構之後的資料。 不過 Azure Cosmos DB 的優點之一，就是儲存資料圖形的彈性。

## <a name="accessing-the-raw-json"></a>存取原始 JSON

您可以透過名為的 [陰影狀態](xref:core/modeling/shadow-properties) 中的特殊屬性（ `"__jObject"` 包含 `JObject` 代表從存放區接收的資料和將儲存的資料），存取 EF Core 不會追蹤的屬性：

[!code-csharp[Unmapped](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=23,24&name=Unmapped)]

```json
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
> `"__jObject"`屬性是 EF Core 基礎結構的一部分，而且只能做為最後的手段，因為在未來的版本中可能會有不同的行為。

> [!NOTE]
> 對實體所做的變更將會覆寫在期間儲存的值 `"__jObject"` `SaveChanges` 。

## <a name="using-cosmosclient"></a>使用 CosmosClient

若要完全分離 EF Core 請從下列來源取得屬於[AZURE COSMOS DB SDK 一部分](/azure/cosmos-db/sql-api-get-started)的[CosmosClient](/dotnet/api/Microsoft.Azure.Cosmos.CosmosClient)物件 `DbContext` ：

[!code-csharp[CosmosClient](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=3&name=CosmosClient)]

## <a name="missing-property-values"></a>遺漏的屬性值

在上述範例中，我們已 `"TrackingNumber"` 從訂單中移除該屬性。 由於索引編制在 Cosmos DB 中的運作方式，在投影中參考遺漏屬性的查詢可能會傳回非預期的結果。 例如︰

[!code-csharp[MissingProperties](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?name=MissingProperties)]

排序的查詢實際上不會傳回任何結果。 這表示當您直接使用存放區時，應該小心填入 EF Core 所對應的屬性。

> [!NOTE]
> 此行為在 Cosmos 的未來版本中可能會變更。 比方說，目前索引編制原則定義了複合索引 {Id/？ ASC、TrackingNumber/？ ASC) }，則具有 ' ORDER BY c.Id ASC，c. 鑒別 ASC ' 的查詢 __會__ 傳回遺漏該屬性的專案 `"TrackingNumber"` 。
