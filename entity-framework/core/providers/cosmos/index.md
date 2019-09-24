---
title: Azure Cosmos DB 提供者 - EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 09/12/2019
ms.assetid: 28264681-4486-4891-888c-be5e4ade24f1
uid: core/providers/cosmos/index
ms.openlocfilehash: c753bb71089c91cbb26b970cddd118645fb18d56
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/20/2019
ms.locfileid: "71150725"
---
# <a name="ef-core-azure-cosmos-db-provider"></a><span data-ttu-id="917d5-102">EF Core Azure Cosmos DB 提供者</span><span class="sxs-lookup"><span data-stu-id="917d5-102">EF Core Azure Cosmos DB Provider</span></span>

>[!NOTE]
> <span data-ttu-id="917d5-103">這是新的 EF Core 3.0 提供者。</span><span class="sxs-lookup"><span data-stu-id="917d5-103">This provider is new in EF Core 3.0.</span></span>

<span data-ttu-id="917d5-104">此資料庫提供者可讓 Entity Framework Core 與 Azure Cosmos DB 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="917d5-104">This database provider allows Entity Framework Core to be used with Azure Cosmos DB.</span></span> <span data-ttu-id="917d5-105">[Entity Framework Core 專案](https://github.com/aspnet/EntityFrameworkCore)的維護包含此提供者。</span><span class="sxs-lookup"><span data-stu-id="917d5-105">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

<span data-ttu-id="917d5-106">強烈建議您在閱讀本節之前先熟悉 [Azure Cosmos DB 文件](https://docs.microsoft.com/en-us/azure/cosmos-db/introduction)。</span><span class="sxs-lookup"><span data-stu-id="917d5-106">It is strongly recommended to familiarize yourself with the [Azure Cosmos DB documentation](https://docs.microsoft.com/en-us/azure/cosmos-db/introduction) before reading this section.</span></span>

## <a name="install"></a><span data-ttu-id="917d5-107">安裝</span><span class="sxs-lookup"><span data-stu-id="917d5-107">Install</span></span>

<span data-ttu-id="917d5-108">安裝 [Microsoft.EntityFrameworkCore.Cosmos NuGet 套件](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/)。</span><span class="sxs-lookup"><span data-stu-id="917d5-108">Install the [Microsoft.EntityFrameworkCore.Cosmos NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/).</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Cosmos
```

## <a name="get-started"></a><span data-ttu-id="917d5-109">開始使用</span><span class="sxs-lookup"><span data-stu-id="917d5-109">Get Started</span></span>

> [!TIP]  
> <span data-ttu-id="917d5-110">您可以檢視本文中的 [GitHut 範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Cosmos)。</span><span class="sxs-lookup"><span data-stu-id="917d5-110">You can view this article's [sample on GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Cosmos).</span></span>

<span data-ttu-id="917d5-111">與其他提供者一樣，第一步是呼叫 `UseCosmos`：[!code-csharp[Configuration](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Configuration)]</span><span class="sxs-lookup"><span data-stu-id="917d5-111">Like for other providers the first step is to call `UseCosmos`: [!code-csharp[Configuration](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Configuration)]</span></span>

> [!WARNING]
> <span data-ttu-id="917d5-112">為了簡便，此處將端點和索引鍵進行了硬式編碼，但應在生產應用程式中[以安全的方式儲存它們](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager)</span><span class="sxs-lookup"><span data-stu-id="917d5-112">The endpoint and key are hardcoded here for simplicity, but in a production app these should be [stored securily](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager)</span></span>

<span data-ttu-id="917d5-113">在此範例中，`Order` 是簡單實體，其參考了[擁有的類型](../../modeling/owned-entities.md) `StreetAddress`。</span><span class="sxs-lookup"><span data-stu-id="917d5-113">In this example `Order` is a simple entity with a reference to the [owned type](../../modeling/owned-entities.md) `StreetAddress`.</span></span>

[!code-csharp[Order](../../../../samples/core/Cosmos/ModelBuilding/Order.cs?name=Order)]

[!code-csharp[StreetAddress](../../../../samples/core/Cosmos/ModelBuilding/StreetAddress.cs?name=StreetAddress)]

<span data-ttu-id="917d5-114">遵循一般的 EF 模式儲存及查詢資料：[!code-csharp[HelloCosmos](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=HelloCosmos)]</span><span class="sxs-lookup"><span data-stu-id="917d5-114">Saving and quering data follows the normal EF pattern: [!code-csharp[HelloCosmos](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=HelloCosmos)]</span></span>

> [!IMPORTANT]
> <span data-ttu-id="917d5-115">呼叫 `EnsureCreated` 對於建立所需集合與插入[種子資料](../../modeling/data-seeding.md) (如果存在於模型中) 是必要的。</span><span class="sxs-lookup"><span data-stu-id="917d5-115">Calling `EnsureCreated` is necessary to create the required collections and insert the [seed data](../../modeling/data-seeding.md) if present in the model.</span></span> <span data-ttu-id="917d5-116">但是，`EnsureCreated` 僅應在部署期間呼叫，在一般作業期間呼叫可能會導致效能問題。</span><span class="sxs-lookup"><span data-stu-id="917d5-116">However `EnsureCreated` should only be called during deployment, not normal operation, as it may cause performance issues.</span></span>

## <a name="cosmos-specific-model-customization"></a><span data-ttu-id="917d5-117">Cosmos 特定的模型自訂</span><span class="sxs-lookup"><span data-stu-id="917d5-117">Cosmos-specific Model Customization</span></span>

<span data-ttu-id="917d5-118">根據預設，所有實體類型都會對應至相同容器，並以衍生的內容命名 (在此案例中為 `"OrderContext"`)。</span><span class="sxs-lookup"><span data-stu-id="917d5-118">By default all entity types are mapped to the same container, named after the derived context (`"OrderContext"` in this case).</span></span> <span data-ttu-id="917d5-119">若要變更預設容器名稱，請使用 `HasDefaultContainer`：</span><span class="sxs-lookup"><span data-stu-id="917d5-119">To change the default container name use `HasDefaultContainer`:</span></span>

[!code-csharp[DefaultContainer](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=DefaultContainer)]

<span data-ttu-id="917d5-120">若要將實體類型對應至不同的容器，請使用 `ToContainer`：</span><span class="sxs-lookup"><span data-stu-id="917d5-120">To map an entity type to a different container use `ToContainer`:</span></span>

[!code-csharp[Container](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Container)]

<span data-ttu-id="917d5-121">為了識別其指定項目代表 EF Core 的實體類型，即使沒有衍生的實體類型，也會新增鑑別子值。</span><span class="sxs-lookup"><span data-stu-id="917d5-121">To identify the entity type that a given item represent EF Core adds a discriminator value even if there are no derived entity types.</span></span> <span data-ttu-id="917d5-122">鑑別子的名稱和值[可以變更](../../modeling/inheritance.md)。</span><span class="sxs-lookup"><span data-stu-id="917d5-122">The name and value of the discriminator [can be changed](../../modeling/inheritance.md).</span></span>

## <a name="embedded-entities"></a><span data-ttu-id="917d5-123">內嵌實體</span><span class="sxs-lookup"><span data-stu-id="917d5-123">Embedded Entities</span></span>

<span data-ttu-id="917d5-124">針對 Cosmos，擁有的實體會以擁有者身分內嵌在相同項目中。</span><span class="sxs-lookup"><span data-stu-id="917d5-124">For Cosmos owned entities are embedded in the same item as the owner.</span></span> <span data-ttu-id="917d5-125">若要變更屬性名稱，請使用 `ToJsonProperty`：</span><span class="sxs-lookup"><span data-stu-id="917d5-125">To change a property name use `ToJsonProperty`:</span></span>

[!code-csharp[PropertyNames](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=PropertyNames)]

<span data-ttu-id="917d5-126">使用此組態，來自以上範例的順序會以如下方式儲存：</span><span class="sxs-lookup"><span data-stu-id="917d5-126">With this configuration the order from the example above is stored like this:</span></span>

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
    "_rid": "6QEKAM+BOOABAAAAAAAAAA==",
    "_self": "dbs/6QEKAA==/colls/6QEKAM+BOOA=/docs/6QEKAM+BOOABAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-683c-692e763901d5\"",
    "_attachments": "attachments/",
    "_ts": 1568163674
}
```

<span data-ttu-id="917d5-127">同時也會內嵌自有實體的集合。</span><span class="sxs-lookup"><span data-stu-id="917d5-127">Collections of owned entities are embedded as well.</span></span> <span data-ttu-id="917d5-128">在下一個範例中，我們將使用 `Distributor` 類別和 `StreetAddress` 的集合：</span><span class="sxs-lookup"><span data-stu-id="917d5-128">For the next example we'll use the `Distributor` class with a collection of `StreetAddress`:</span></span>

[!code-csharp[Distributor](../../../../samples/core/Cosmos/ModelBuilding/Distributor.cs?name=Distributor)]

<span data-ttu-id="917d5-129">自有實體不需提供明確索引鍵值來儲存：</span><span class="sxs-lookup"><span data-stu-id="917d5-129">The owned entities don't need to provide explicit key values to be stored:</span></span>

[!code-csharp[OwnedCollection](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=OwnedCollection)]

<span data-ttu-id="917d5-130">它們會以如下方式保存：</span><span class="sxs-lookup"><span data-stu-id="917d5-130">They will be persisted in this way:</span></span>

``` json
{
    "Id": 1,
    "Discriminator": "Distributor",
    "id": "Distributor|1",
    "ShippingCenters": [
        {
            "City": "Phoenix",
            "Discriminator": "StreetAddress",
            "Street": "500 S 48th Street"
        },
        {
            "City": "Anaheim",
            "Discriminator": "StreetAddress",
            "Street": "5650 Dolly Ave"
        }
    ],
    "_rid": "6QEKANzISj0BAAAAAAAAAA==",
    "_self": "dbs/6QEKAA==/colls/6QEKANzISj0=/docs/6QEKANzISj0BAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-683c-7b2b439701d5\"",
    "_attachments": "attachments/",
    "_ts": 1568163705
}
```

<span data-ttu-id="917d5-131">在內部，EF Core 一律需要具有所有追蹤實體的唯一索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="917d5-131">Internally EF Core always needs to have unique key values for all tracked entities.</span></span> <span data-ttu-id="917d5-132">根據預設，為擁有類型的集合所建立主索引鍵，由指向擁有者的外部索引鍵屬性和與 JSON 陣列中索引對應的 `int` 屬性組成。</span><span class="sxs-lookup"><span data-stu-id="917d5-132">The primary key created by default for collections of owned types consists of the foreign key properties pointing to the owner and an `int` property corresponding to the index in the JSON array.</span></span> <span data-ttu-id="917d5-133">若要擷取這些值，可以使用項目 API：</span><span class="sxs-lookup"><span data-stu-id="917d5-133">To retrieve these values entry API could be used:</span></span>

[!code-csharp[ImpliedProperties](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=ImpliedProperties)]

> [!TIP]
> <span data-ttu-id="917d5-134">必要時，您可以變更自有實體類型的預設主索引鍵，但應明確提供索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="917d5-134">When necessary the default primary key for the owned entity types can be changed, but then key values should be provided explicitly.</span></span>

## <a name="working-with-disconnected-entities"></a><span data-ttu-id="917d5-135">使用已中斷連線的實體</span><span class="sxs-lookup"><span data-stu-id="917d5-135">Working with Disconnected Entities</span></span>

<span data-ttu-id="917d5-136">每個項目都必須具有 `id` 值，該值對於特定分割區索引鍵是唯一的。</span><span class="sxs-lookup"><span data-stu-id="917d5-136">Every item needs to have an `id` value that is unique for the given partition key.</span></span> <span data-ttu-id="917d5-137">根據預設，EF Core 會使用 “|” 作為分隔符號，將鑑別子和主索引鍵值相結合以產生值。</span><span class="sxs-lookup"><span data-stu-id="917d5-137">By default EF Core generates the value by concatenating the discriminator and the primary key values, using '|' as a delimiter.</span></span> <span data-ttu-id="917d5-138">僅當實體進入 `Added` 狀態時才會產生索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="917d5-138">The key values are only generated when an entity enters the `Added` state.</span></span> <span data-ttu-id="917d5-139">在[連結實體](../../saving/disconnected-entities.md)時，如果它們在 .NET 類型上不具有 `id` 屬性來儲存值，則可能會發生問題。</span><span class="sxs-lookup"><span data-stu-id="917d5-139">This might pose a problem when [attaching entities](../../saving/disconnected-entities.md) if they don't have an `id` property on the .NET type to store the value.</span></span>

<span data-ttu-id="917d5-140">若要解決此限制，您可以手動建立並設定 `id` 值，或先將實體標記為已新增，然後將其變更為所需狀態：</span><span class="sxs-lookup"><span data-stu-id="917d5-140">To work around this limitation one could create and set the `id` value manually or mark the entity as added first, then changing it to the desired state:</span></span>

[!code-csharp[Attach](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?highlight=4&name=Attach)]

<span data-ttu-id="917d5-141">以下是輸出 JSON：</span><span class="sxs-lookup"><span data-stu-id="917d5-141">This is the resulting JSON:</span></span>

``` json
{
    "Id": 1,
    "Discriminator": "Order",
    "TrackingNumber": null,
    "id": "Order|1",
    "Address": {
        "ShipsToCity": "London",
        "Discriminator": "StreetAddress",
        "ShipsToStreet": "3 Abbey Road"
    },
    "_rid": "6QEKAM+BOOABAAAAAAAAAA==",
    "_self": "dbs/6QEKAA==/colls/6QEKAM+BOOA=/docs/6QEKAM+BOOABAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-683c-8f7ac48f01d5\"",
    "_attachments": "attachments/",
    "_ts": 1568163739
}
```