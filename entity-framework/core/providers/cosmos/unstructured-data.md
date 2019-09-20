---
title: Azure Cosmos DB 提供者-使用非結構化資料-EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 09/12/2019
ms.assetid: b47d41b6-984f-419a-ab10-2ed3b95e3919
uid: core/providers/cosmos/unstructured-data
ms.openlocfilehash: 86bb0f7915c8a2561e7d5cd5dffc27474218a112
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/20/2019
ms.locfileid: "71150813"
---
# <a name="working-with-unstructured-data-in-ef-core-azure-cosmos-db-provider"></a>使用 EF Core Azure Cosmos DB 提供者中的非結構化資料

EF Core 的設計可讓您輕鬆地使用遵循模型中所定義之架構的資料。 不過，Azure Cosmos DB 的其中一個優點是儲存資料圖形的彈性。

## <a name="accessing-the-raw-json"></a>存取原始 JSON

您可以透過名為`"__jObject"`的[陰影狀態](../../modeling/shadow-properties.md)中包含的特殊屬性來存取不受追蹤的屬性 EF Core，其中包含`JObject`代表從存放區接收的資料，以及將儲存的資料：

[!code-csharp[Unmapped](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=21-23&name=Unmapped)]

``` json
{
    "Id": 1,
    "Discriminator": "Order",
    "TrackingNumber": null,
    "id": "Order|1",
    "Address": {
        "ShipsToCity": "London",
        "Discriminator": "StreetAddress",
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
> `"__jObject"`屬性是 EF Core 基礎結構的一部分，而且只應做為最後的手段，因為未來版本中可能會有不同的行為。

> [!NOTE]
> 對實體所做的變更將會覆寫`"__jObject"`在`SaveChanges`期間儲存的值。

## <a name="using-cosmosclient"></a>使用 CosmosClient

若要完全分離 EF Core 從取得`CosmosClient`屬於`DbContext` [Azure Cosmos DB SDK 一部分](https://docs.microsoft.com/en-us/azure/cosmos-db/sql-api-get-started)的物件：

[!code-csharp[CosmosClient](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=3&name=CosmosClient)]

## <a name="missing-property-values"></a>遺漏屬性值

在上述範例中，我們從`"TrackingNumber"`訂單中移除屬性。 因為索引在 Cosmos DB 中的運作方式，所以參考遺漏屬性的查詢不在投影中，而是會傳回非預期的結果。 例如：

[!code-csharp[MissingProperties](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?name=MissingProperties)]

已排序的查詢實際上不會傳回任何結果。 這表示當直接使用存放區時，一定要特別注意一個 EF Core 所對應的屬性。

> [!NOTE]
> 在未來的 Cosmos 版本中，此行為可能會變更。 例如，目前索引編制原則是否定義複合索引 {Id/？ ASC、TrackingNumber/？ Asc）}，然後查詢具有 ' ORDER BY c.Id asc，c. 鑒別子 asc '__會__傳回遺漏`"TrackingNumber"`屬性的專案。