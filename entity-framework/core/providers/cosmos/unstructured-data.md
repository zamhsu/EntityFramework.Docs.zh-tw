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
# <a name="working-with-unstructured-data-in-ef-core-azure-cosmos-db-provider"></a><span data-ttu-id="d276d-102">使用 EF Core Azure Cosmos DB 提供者中的非結構化資料</span><span class="sxs-lookup"><span data-stu-id="d276d-102">Working with Unstructured Data in EF Core Azure Cosmos DB Provider</span></span>

<span data-ttu-id="d276d-103">EF Core 的設計可讓您輕鬆地使用遵循模型中所定義之架構的資料。</span><span class="sxs-lookup"><span data-stu-id="d276d-103">EF Core was designed to make it easy to work with data that follows a schema defined in the model.</span></span> <span data-ttu-id="d276d-104">不過，Azure Cosmos DB 的其中一個優點是儲存資料圖形的彈性。</span><span class="sxs-lookup"><span data-stu-id="d276d-104">However one of the strengths of Azure Cosmos DB is the flexibility in the shape of the data stored.</span></span>

## <a name="accessing-the-raw-json"></a><span data-ttu-id="d276d-105">存取原始 JSON</span><span class="sxs-lookup"><span data-stu-id="d276d-105">Accessing the raw JSON</span></span>

<span data-ttu-id="d276d-106">您可以透過名為`"__jObject"`的[陰影狀態](../../modeling/shadow-properties.md)中包含的特殊屬性來存取不受追蹤的屬性 EF Core，其中包含`JObject`代表從存放區接收的資料，以及將儲存的資料：</span><span class="sxs-lookup"><span data-stu-id="d276d-106">It is possible to access the properties that are not tracked by EF Core through a special property in [shadow-state](../../modeling/shadow-properties.md) named `"__jObject"` that contains a `JObject` representing the data recieved from the store and data that will be stored:</span></span>

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
> <span data-ttu-id="d276d-107">`"__jObject"`屬性是 EF Core 基礎結構的一部分，而且只應做為最後的手段，因為未來版本中可能會有不同的行為。</span><span class="sxs-lookup"><span data-stu-id="d276d-107">The `"__jObject"` property is part of the EF Core infrastructure and should only be used as a last resort as it is likely to have different behavior in future releases.</span></span>

> [!NOTE]
> <span data-ttu-id="d276d-108">對實體所做的變更將會覆寫`"__jObject"`在`SaveChanges`期間儲存的值。</span><span class="sxs-lookup"><span data-stu-id="d276d-108">Changes to the entity will override the values stored in `"__jObject"` during `SaveChanges`.</span></span>

## <a name="using-cosmosclient"></a><span data-ttu-id="d276d-109">使用 CosmosClient</span><span class="sxs-lookup"><span data-stu-id="d276d-109">Using CosmosClient</span></span>

<span data-ttu-id="d276d-110">若要完全分離 EF Core 從取得`CosmosClient`屬於`DbContext` [Azure Cosmos DB SDK 一部分](https://docs.microsoft.com/en-us/azure/cosmos-db/sql-api-get-started)的物件：</span><span class="sxs-lookup"><span data-stu-id="d276d-110">To decouple completely from EF Core get the `CosmosClient` object that is [part of the Azure Cosmos DB SDK](https://docs.microsoft.com/en-us/azure/cosmos-db/sql-api-get-started) from `DbContext`:</span></span>

[!code-csharp[CosmosClient](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=3&name=CosmosClient)]

## <a name="missing-property-values"></a><span data-ttu-id="d276d-111">遺漏屬性值</span><span class="sxs-lookup"><span data-stu-id="d276d-111">Missing property values</span></span>

<span data-ttu-id="d276d-112">在上述範例中，我們從`"TrackingNumber"`訂單中移除屬性。</span><span class="sxs-lookup"><span data-stu-id="d276d-112">In the previous example we removed the `"TrackingNumber"` property from the order.</span></span> <span data-ttu-id="d276d-113">因為索引在 Cosmos DB 中的運作方式，所以參考遺漏屬性的查詢不在投影中，而是會傳回非預期的結果。</span><span class="sxs-lookup"><span data-stu-id="d276d-113">Because of how indexing works in Cosmos DB, queries that reference the missing property somewhere else than in the projection could return unexpected results.</span></span> <span data-ttu-id="d276d-114">例如：</span><span class="sxs-lookup"><span data-stu-id="d276d-114">For example:</span></span>

[!code-csharp[MissingProperties](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?name=MissingProperties)]

<span data-ttu-id="d276d-115">已排序的查詢實際上不會傳回任何結果。</span><span class="sxs-lookup"><span data-stu-id="d276d-115">The sorted query actually returns no results.</span></span> <span data-ttu-id="d276d-116">這表示當直接使用存放區時，一定要特別注意一個 EF Core 所對應的屬性。</span><span class="sxs-lookup"><span data-stu-id="d276d-116">This means that one should take care to always populate properties mapped by EF Core when working with the store directly.</span></span>

> [!NOTE]
> <span data-ttu-id="d276d-117">在未來的 Cosmos 版本中，此行為可能會變更。</span><span class="sxs-lookup"><span data-stu-id="d276d-117">This behavior might change in future versions of Cosmos.</span></span> <span data-ttu-id="d276d-118">例如，目前索引編制原則是否定義複合索引 {Id/？</span><span class="sxs-lookup"><span data-stu-id="d276d-118">For instance, currently if the indexing policy defines the composite index {Id/?</span></span> <span data-ttu-id="d276d-119">ASC、TrackingNumber/？</span><span class="sxs-lookup"><span data-stu-id="d276d-119">ASC, TrackingNumber/?</span></span> <span data-ttu-id="d276d-120">Asc）}，然後查詢具有 ' ORDER BY c.Id asc，c. 鑒別子 asc '__會__傳回遺漏`"TrackingNumber"`屬性的專案。</span><span class="sxs-lookup"><span data-stu-id="d276d-120">ASC)}, then a query the has 'ORDER BY c.Id ASC, c.Discriminator ASC' __would__ return items that are missing the `"TrackingNumber"` property.</span></span>