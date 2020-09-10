---
title: Azure Cosmos DB 提供者-使用非結構化資料-EF Core
description: 如何使用 Entity Framework Core 處理 Azure Cosmos DB 非結構化資料
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/unstructured-data
ms.openlocfilehash: 9f96af00e8fcb012c33fc7528787560ea3a5e481
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619049"
---
# <a name="working-with-unstructured-data-in-ef-core-azure-cosmos-db-provider"></a><span data-ttu-id="8273a-103">使用 EF Core Azure Cosmos DB 提供者中的非結構化資料</span><span class="sxs-lookup"><span data-stu-id="8273a-103">Working with Unstructured Data in EF Core Azure Cosmos DB Provider</span></span>

<span data-ttu-id="8273a-104">EF Core 的設計可讓您輕鬆地使用在模型中定義之架構之後的資料。</span><span class="sxs-lookup"><span data-stu-id="8273a-104">EF Core was designed to make it easy to work with data that follows a schema defined in the model.</span></span> <span data-ttu-id="8273a-105">不過 Azure Cosmos DB 的優點之一，就是儲存資料圖形的彈性。</span><span class="sxs-lookup"><span data-stu-id="8273a-105">However one of the strengths of Azure Cosmos DB is the flexibility in the shape of the data stored.</span></span>

## <a name="accessing-the-raw-json"></a><span data-ttu-id="8273a-106">存取原始 JSON</span><span class="sxs-lookup"><span data-stu-id="8273a-106">Accessing the raw JSON</span></span>

<span data-ttu-id="8273a-107">您可以透過名為的 [陰影狀態](xref:core/modeling/shadow-properties) 中的特殊屬性（ `"__jObject"` 包含 `JObject` 代表從存放區接收的資料和將儲存的資料），存取 EF Core 不會追蹤的屬性：</span><span class="sxs-lookup"><span data-stu-id="8273a-107">It is possible to access the properties that are not tracked by EF Core through a special property in [shadow-state](xref:core/modeling/shadow-properties) named `"__jObject"` that contains a `JObject` representing the data recieved from the store and data that will be stored:</span></span>

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
> <span data-ttu-id="8273a-108">`"__jObject"`屬性是 EF Core 基礎結構的一部分，而且只能做為最後的手段，因為在未來的版本中可能會有不同的行為。</span><span class="sxs-lookup"><span data-stu-id="8273a-108">The `"__jObject"` property is part of the EF Core infrastructure and should only be used as a last resort as it is likely to have different behavior in future releases.</span></span>

> [!NOTE]
> <span data-ttu-id="8273a-109">對實體所做的變更將會覆寫在期間儲存的值 `"__jObject"` `SaveChanges` 。</span><span class="sxs-lookup"><span data-stu-id="8273a-109">Changes to the entity will override the values stored in `"__jObject"` during `SaveChanges`.</span></span>

## <a name="using-cosmosclient"></a><span data-ttu-id="8273a-110">使用 CosmosClient</span><span class="sxs-lookup"><span data-stu-id="8273a-110">Using CosmosClient</span></span>

<span data-ttu-id="8273a-111">若要完全分離 EF Core 請從下列來源取得屬於[AZURE COSMOS DB SDK 一部分](/azure/cosmos-db/sql-api-get-started)的[CosmosClient](/dotnet/api/Microsoft.Azure.Cosmos.CosmosClient)物件 `DbContext` ：</span><span class="sxs-lookup"><span data-stu-id="8273a-111">To decouple completely from EF Core get the [CosmosClient](/dotnet/api/Microsoft.Azure.Cosmos.CosmosClient) object that is [part of the Azure Cosmos DB SDK](/azure/cosmos-db/sql-api-get-started) from `DbContext`:</span></span>

[!code-csharp[CosmosClient](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=3&name=CosmosClient)]

## <a name="missing-property-values"></a><span data-ttu-id="8273a-112">遺漏的屬性值</span><span class="sxs-lookup"><span data-stu-id="8273a-112">Missing property values</span></span>

<span data-ttu-id="8273a-113">在上述範例中，我們已 `"TrackingNumber"` 從訂單中移除該屬性。</span><span class="sxs-lookup"><span data-stu-id="8273a-113">In the previous example we removed the `"TrackingNumber"` property from the order.</span></span> <span data-ttu-id="8273a-114">由於索引編制在 Cosmos DB 中的運作方式，在投影中參考遺漏屬性的查詢可能會傳回非預期的結果。</span><span class="sxs-lookup"><span data-stu-id="8273a-114">Because of how indexing works in Cosmos DB, queries that reference the missing property somewhere else than in the projection could return unexpected results.</span></span> <span data-ttu-id="8273a-115">例如：</span><span class="sxs-lookup"><span data-stu-id="8273a-115">For example:</span></span>

[!code-csharp[MissingProperties](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?name=MissingProperties)]

<span data-ttu-id="8273a-116">排序的查詢實際上不會傳回任何結果。</span><span class="sxs-lookup"><span data-stu-id="8273a-116">The sorted query actually returns no results.</span></span> <span data-ttu-id="8273a-117">這表示當您直接使用存放區時，應該小心填入 EF Core 所對應的屬性。</span><span class="sxs-lookup"><span data-stu-id="8273a-117">This means that one should take care to always populate properties mapped by EF Core when working with the store directly.</span></span>

> [!NOTE]
> <span data-ttu-id="8273a-118">此行為在 Cosmos 的未來版本中可能會變更。</span><span class="sxs-lookup"><span data-stu-id="8273a-118">This behavior might change in future versions of Cosmos.</span></span> <span data-ttu-id="8273a-119">比方說，目前索引編制原則定義了複合索引 {Id/？</span><span class="sxs-lookup"><span data-stu-id="8273a-119">For instance, currently if the indexing policy defines the composite index {Id/?</span></span> <span data-ttu-id="8273a-120">ASC、TrackingNumber/？</span><span class="sxs-lookup"><span data-stu-id="8273a-120">ASC, TrackingNumber/?</span></span> <span data-ttu-id="8273a-121">ASC) }，則具有 ' ORDER BY c.Id ASC，c. 鑒別 ASC ' 的查詢 __會__ 傳回遺漏該屬性的專案 `"TrackingNumber"` 。</span><span class="sxs-lookup"><span data-stu-id="8273a-121">ASC)}, then a query that has 'ORDER BY c.Id ASC, c.Discriminator ASC' __would__ return items that are missing the `"TrackingNumber"` property.</span></span>
