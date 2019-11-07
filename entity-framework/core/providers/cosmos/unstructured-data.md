---
title: Azure Cosmos DB 提供者-使用非結構化資料-EF Core
description: 如何使用 Entity Framework Core 處理 Azure Cosmos DB 非結構化資料
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/unstructured-data
ms.openlocfilehash: 0bfccbfd3af6e209967004752b5a3947d644544b
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655522"
---
# <a name="working-with-unstructured-data-in-ef-core-azure-cosmos-db-provider"></a><span data-ttu-id="2c9de-103">使用 EF Core Azure Cosmos DB 提供者中的非結構化資料</span><span class="sxs-lookup"><span data-stu-id="2c9de-103">Working with Unstructured Data in EF Core Azure Cosmos DB Provider</span></span>

<span data-ttu-id="2c9de-104">EF Core 的設計可讓您輕鬆地使用遵循模型中所定義之架構的資料。</span><span class="sxs-lookup"><span data-stu-id="2c9de-104">EF Core was designed to make it easy to work with data that follows a schema defined in the model.</span></span> <span data-ttu-id="2c9de-105">不過，Azure Cosmos DB 的其中一個優點是儲存資料圖形的彈性。</span><span class="sxs-lookup"><span data-stu-id="2c9de-105">However one of the strengths of Azure Cosmos DB is the flexibility in the shape of the data stored.</span></span>

## <a name="accessing-the-raw-json"></a><span data-ttu-id="2c9de-106">存取原始 JSON</span><span class="sxs-lookup"><span data-stu-id="2c9de-106">Accessing the raw JSON</span></span>

<span data-ttu-id="2c9de-107">您可以透過名為 `"__jObject"` 的[陰影狀態](../../modeling/shadow-properties.md)中的特殊屬性來存取未追蹤的屬性 EF Core，其中包含的 `JObject` 代表從存放區接收的資料，以及將儲存的資料：</span><span class="sxs-lookup"><span data-stu-id="2c9de-107">It is possible to access the properties that are not tracked by EF Core through a special property in [shadow-state](../../modeling/shadow-properties.md) named `"__jObject"` that contains a `JObject` representing the data recieved from the store and data that will be stored:</span></span>

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
> <span data-ttu-id="2c9de-108">`"__jObject"` 屬性是 EF Core 基礎結構的一部分，而且只應作為最後的手段，因為未來版本中可能會有不同的行為。</span><span class="sxs-lookup"><span data-stu-id="2c9de-108">The `"__jObject"` property is part of the EF Core infrastructure and should only be used as a last resort as it is likely to have different behavior in future releases.</span></span>

> [!NOTE]
> <span data-ttu-id="2c9de-109">對實體所做的變更將會覆寫 `SaveChanges`期間儲存在 `"__jObject"` 中的值。</span><span class="sxs-lookup"><span data-stu-id="2c9de-109">Changes to the entity will override the values stored in `"__jObject"` during `SaveChanges`.</span></span>

## <a name="using-cosmosclient"></a><span data-ttu-id="2c9de-110">使用 CosmosClient</span><span class="sxs-lookup"><span data-stu-id="2c9de-110">Using CosmosClient</span></span>

<span data-ttu-id="2c9de-111">若要完全分離，EF Core 從 `DbContext`取得屬於[AZURE COSMOS DB SDK 一部分](/azure/cosmos-db/sql-api-get-started)的[CosmosClient](/dotnet/api/Microsoft.Azure.Cosmos.CosmosClient)物件：</span><span class="sxs-lookup"><span data-stu-id="2c9de-111">To decouple completely from EF Core get the [CosmosClient](/dotnet/api/Microsoft.Azure.Cosmos.CosmosClient) object that is [part of the Azure Cosmos DB SDK](/azure/cosmos-db/sql-api-get-started) from `DbContext`:</span></span>

[!code-csharp[CosmosClient](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=3&name=CosmosClient)]

## <a name="missing-property-values"></a><span data-ttu-id="2c9de-112">遺漏屬性值</span><span class="sxs-lookup"><span data-stu-id="2c9de-112">Missing property values</span></span>

<span data-ttu-id="2c9de-113">在上述範例中，我們從訂單中移除 `"TrackingNumber"` 屬性。</span><span class="sxs-lookup"><span data-stu-id="2c9de-113">In the previous example we removed the `"TrackingNumber"` property from the order.</span></span> <span data-ttu-id="2c9de-114">因為索引在 Cosmos DB 中的運作方式，所以參考遺漏屬性的查詢不在投影中，而是會傳回非預期的結果。</span><span class="sxs-lookup"><span data-stu-id="2c9de-114">Because of how indexing works in Cosmos DB, queries that reference the missing property somewhere else than in the projection could return unexpected results.</span></span> <span data-ttu-id="2c9de-115">例如:</span><span class="sxs-lookup"><span data-stu-id="2c9de-115">For example:</span></span>

[!code-csharp[MissingProperties](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?name=MissingProperties)]

<span data-ttu-id="2c9de-116">已排序的查詢實際上不會傳回任何結果。</span><span class="sxs-lookup"><span data-stu-id="2c9de-116">The sorted query actually returns no results.</span></span> <span data-ttu-id="2c9de-117">這表示當直接使用存放區時，一定要特別注意一個 EF Core 所對應的屬性。</span><span class="sxs-lookup"><span data-stu-id="2c9de-117">This means that one should take care to always populate properties mapped by EF Core when working with the store directly.</span></span>

> [!NOTE]
> <span data-ttu-id="2c9de-118">在未來的 Cosmos 版本中，此行為可能會變更。</span><span class="sxs-lookup"><span data-stu-id="2c9de-118">This behavior might change in future versions of Cosmos.</span></span> <span data-ttu-id="2c9de-119">例如，目前索引編制原則是否定義複合索引 {Id/？</span><span class="sxs-lookup"><span data-stu-id="2c9de-119">For instance, currently if the indexing policy defines the composite index {Id/?</span></span> <span data-ttu-id="2c9de-120">ASC、TrackingNumber/？</span><span class="sxs-lookup"><span data-stu-id="2c9de-120">ASC, TrackingNumber/?</span></span> <span data-ttu-id="2c9de-121">ASC）}，然後查詢具有 ' ORDER BY c.Id ASC，c. 鑒別子 ASC '__會__傳回遺漏 `"TrackingNumber"` 屬性的專案。</span><span class="sxs-lookup"><span data-stu-id="2c9de-121">ASC)}, then a query the has 'ORDER BY c.Id ASC, c.Discriminator ASC' __would__ return items that are missing the `"TrackingNumber"` property.</span></span>
