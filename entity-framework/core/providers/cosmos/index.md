---
title: Azure Cosmos DB 提供者 - EF Core
description: 資料庫提供者的文件，內容說明如何搭配使用 Entity Framework Core 與 Azure Cosmos DB SQL API
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/index
ms.openlocfilehash: 6cac695288d9ba84968b7fab6361f55e9b51be67
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656083"
---
# <a name="ef-core-azure-cosmos-db-provider"></a><span data-ttu-id="4f25d-103">EF Core Azure Cosmos DB 提供者</span><span class="sxs-lookup"><span data-stu-id="4f25d-103">EF Core Azure Cosmos DB Provider</span></span>

>[!NOTE]
> <span data-ttu-id="4f25d-104">這是新的 EF Core 3.0 提供者。</span><span class="sxs-lookup"><span data-stu-id="4f25d-104">This provider is new in EF Core 3.0.</span></span>

<span data-ttu-id="4f25d-105">此資料庫提供者可讓 Entity Framework Core 與 Azure Cosmos DB 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="4f25d-105">This database provider allows Entity Framework Core to be used with Azure Cosmos DB.</span></span> <span data-ttu-id="4f25d-106">[Entity Framework Core 專案](https://github.com/aspnet/EntityFrameworkCore)的維護包含此提供者。</span><span class="sxs-lookup"><span data-stu-id="4f25d-106">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

<span data-ttu-id="4f25d-107">強烈建議您在閱讀本節之前先熟悉 [Azure Cosmos DB 文件](/azure/cosmos-db/introduction)。</span><span class="sxs-lookup"><span data-stu-id="4f25d-107">It is strongly recommended to familiarize yourself with the [Azure Cosmos DB documentation](/azure/cosmos-db/introduction) before reading this section.</span></span>

>[!NOTE]
> <span data-ttu-id="4f25d-108">只能搭配 Azure Cosmos DB 的 SQL API 使用此提供者。</span><span class="sxs-lookup"><span data-stu-id="4f25d-108">This provider only works with the SQL API of Azure Cosmos DB.</span></span>

## <a name="install"></a><span data-ttu-id="4f25d-109">安裝</span><span class="sxs-lookup"><span data-stu-id="4f25d-109">Install</span></span>

<span data-ttu-id="4f25d-110">安裝 [Microsoft.EntityFrameworkCore.Cosmos NuGet 套件](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/)。</span><span class="sxs-lookup"><span data-stu-id="4f25d-110">Install the [Microsoft.EntityFrameworkCore.Cosmos NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/).</span></span>

## <a name="net-core-clitabdotnet-core-cli"></a>[<span data-ttu-id="4f25d-111">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4f25d-111">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

``` console
dotnet add package Microsoft.EntityFrameworkCore.Cosmos
```

## <a name="visual-studiotabvs"></a>[<span data-ttu-id="4f25d-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f25d-112">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Cosmos
```

***

## <a name="get-started"></a><span data-ttu-id="4f25d-113">開始使用</span><span class="sxs-lookup"><span data-stu-id="4f25d-113">Get started</span></span>

> [!TIP]  
> <span data-ttu-id="4f25d-114">您可以檢視本文中的 [GitHut 範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Cosmos)。</span><span class="sxs-lookup"><span data-stu-id="4f25d-114">You can view this article's [sample on GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Cosmos).</span></span>

<span data-ttu-id="4f25d-115">與其他提供者一樣，第一步是呼叫 [UseCosmos](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosDbContextOptionsExtensions.UseCosmos)：</span><span class="sxs-lookup"><span data-stu-id="4f25d-115">Like for other providers the first step is to call [UseCosmos](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosDbContextOptionsExtensions.UseCosmos):</span></span>

[!code-csharp[Configuration](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Configuration)]

> [!WARNING]
> <span data-ttu-id="4f25d-116">為了簡便，此處將端點和索引鍵進行了硬式編碼，但應在生產應用程式中[以安全的方式儲存它們](/aspnet/core/security/app-secrets#secret-manager)</span><span class="sxs-lookup"><span data-stu-id="4f25d-116">The endpoint and key are hardcoded here for simplicity, but in a production app these should be [stored securily](/aspnet/core/security/app-secrets#secret-manager)</span></span>

<span data-ttu-id="4f25d-117">在此範例中，`Order` 是簡單實體，其參考了[擁有的類型](../../modeling/owned-entities.md) `StreetAddress`。</span><span class="sxs-lookup"><span data-stu-id="4f25d-117">In this example `Order` is a simple entity with a reference to the [owned type](../../modeling/owned-entities.md) `StreetAddress`.</span></span>

[!code-csharp[Order](../../../../samples/core/Cosmos/ModelBuilding/Order.cs?name=Order)]

[!code-csharp[StreetAddress](../../../../samples/core/Cosmos/ModelBuilding/StreetAddress.cs?name=StreetAddress)]

<span data-ttu-id="4f25d-118">遵循一般的 EF 模式儲存及查詢資料：</span><span class="sxs-lookup"><span data-stu-id="4f25d-118">Saving and quering data follows the normal EF pattern:</span></span>

[!code-csharp[HelloCosmos](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=HelloCosmos)]

> [!IMPORTANT]
> <span data-ttu-id="4f25d-119">如果要建立需要的容器或插入[種子資料](../../modeling/data-seeding.md) (如果存在於模型中)，就必須呼叫 [EnsureCreatedAsync](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreatedAsync)。</span><span class="sxs-lookup"><span data-stu-id="4f25d-119">Calling [EnsureCreatedAsync](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreatedAsync) is necessary to create the required containers and insert the [seed data](../../modeling/data-seeding.md) if present in the model.</span></span> <span data-ttu-id="4f25d-120">但是，`EnsureCreatedAsync` 僅應在部署期間呼叫，在一般作業期間呼叫可能會導致效能問題。</span><span class="sxs-lookup"><span data-stu-id="4f25d-120">However `EnsureCreatedAsync` should only be called during deployment, not normal operation, as it may cause performance issues.</span></span>

## <a name="cosmos-specific-model-customization"></a><span data-ttu-id="4f25d-121">Cosmos 專用模型自訂</span><span class="sxs-lookup"><span data-stu-id="4f25d-121">Cosmos-specific model customization</span></span>

<span data-ttu-id="4f25d-122">根據預設，所有實體類型都會對應至相同容器，並以衍生的內容命名 (在此案例中為 `"OrderContext"`)。</span><span class="sxs-lookup"><span data-stu-id="4f25d-122">By default all entity types are mapped to the same container, named after the derived context (`"OrderContext"` in this case).</span></span> <span data-ttu-id="4f25d-123">若要變更預設容器名稱，請使用 [HasDefaultContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosModelBuilderExtensions.HasDefaultContainer)：</span><span class="sxs-lookup"><span data-stu-id="4f25d-123">To change the default container name use [HasDefaultContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosModelBuilderExtensions.HasDefaultContainer):</span></span>

[!code-csharp[DefaultContainer](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=DefaultContainer)]

<span data-ttu-id="4f25d-124">若要將實體類型對應至不同的容器，請使用 [ToContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToContainer)：</span><span class="sxs-lookup"><span data-stu-id="4f25d-124">To map an entity type to a different container use [ToContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToContainer):</span></span>

[!code-csharp[Container](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Container)]

<span data-ttu-id="4f25d-125">為了識別其指定項目代表 EF Core 的實體類型，即使沒有衍生的實體類型，也會新增鑑別子值。</span><span class="sxs-lookup"><span data-stu-id="4f25d-125">To identify the entity type that a given item represent EF Core adds a discriminator value even if there are no derived entity types.</span></span> <span data-ttu-id="4f25d-126">鑑別子的名稱和值[可以變更](../../modeling/inheritance.md)。</span><span class="sxs-lookup"><span data-stu-id="4f25d-126">The name and value of the discriminator [can be changed](../../modeling/inheritance.md).</span></span>

<span data-ttu-id="4f25d-127">如果不會在同一個容器中儲存其他實體類型，就可以呼叫 [HasNoDiscriminator](/dotnet/api/Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasNoDiscriminator) 將鑑別子移除：</span><span class="sxs-lookup"><span data-stu-id="4f25d-127">If no other entity type will ever be stored in the same container the discriminator can be removed by calling [HasNoDiscriminator](/dotnet/api/Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasNoDiscriminator):</span></span>

[!code-csharp[NoDiscriminator](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=NoDiscriminator)]

### <a name="partition-keys"></a><span data-ttu-id="4f25d-128">資料分割索引鍵</span><span class="sxs-lookup"><span data-stu-id="4f25d-128">Partition keys</span></span>

<span data-ttu-id="4f25d-129">根據預設，EF Core 會使用對 `"__partitionKey"` 設定的分割區索引鍵來建立容器，但不會在插入項目時提供任何值。</span><span class="sxs-lookup"><span data-stu-id="4f25d-129">By default EF Core will create containers with the partition key set to `"__partitionKey"` without supplying any value for it when inserting items.</span></span> <span data-ttu-id="4f25d-130">不過，如果要充分利用 Azure Cosmos 的效能功能，就必須使用[謹慎選取的分割區索引鍵](/azure/cosmos-db/partition-data)。</span><span class="sxs-lookup"><span data-stu-id="4f25d-130">But to fully leverage the performance capabilities of Azure Cosmos a [carefully selected partition key](/azure/cosmos-db/partition-data) should be used.</span></span> <span data-ttu-id="4f25d-131">您可以透過呼叫 [HasPartitionKey](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.HasPartitionKey) 來設定分割區索引鍵：</span><span class="sxs-lookup"><span data-stu-id="4f25d-131">It can be configured by calling [HasPartitionKey](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.HasPartitionKey):</span></span>

[!code-csharp[PartitionKey](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=PartitionKey)]

>[!NOTE]
><span data-ttu-id="4f25d-132">只要分割區索引鍵屬性會[轉換成字串](xref:core/modeling/value-conversions)，就不限於任何類型。</span><span class="sxs-lookup"><span data-stu-id="4f25d-132">The partition key property can be of any type as long as it is [converted to string](xref:core/modeling/value-conversions).</span></span>

<span data-ttu-id="4f25d-133">分割區索引鍵屬性設定完成後，應該都會具有 null 以外的值。</span><span class="sxs-lookup"><span data-stu-id="4f25d-133">Once configured the partition key property should always have a non-null value.</span></span> <span data-ttu-id="4f25d-134">您可在發出查詢時新增條件，讓查詢成為單一分割區。</span><span class="sxs-lookup"><span data-stu-id="4f25d-134">When issuing a query a condition can be added to make it single-partition.</span></span>

[!code-csharp[PartitionKey](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=PartitionKey)]

## <a name="embedded-entities"></a><span data-ttu-id="4f25d-135">內嵌實體</span><span class="sxs-lookup"><span data-stu-id="4f25d-135">Embedded entities</span></span>

<span data-ttu-id="4f25d-136">針對 Cosmos，擁有的實體會以擁有者身分內嵌在相同項目中。</span><span class="sxs-lookup"><span data-stu-id="4f25d-136">For Cosmos owned entities are embedded in the same item as the owner.</span></span> <span data-ttu-id="4f25d-137">若要變更屬性名稱，請使用 [ToJsonProperty](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToJsonProperty)：</span><span class="sxs-lookup"><span data-stu-id="4f25d-137">To change a property name use [ToJsonProperty](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToJsonProperty):</span></span>

[!code-csharp[PropertyNames](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=PropertyNames)]

<span data-ttu-id="4f25d-138">使用此組態，來自以上範例的順序會以如下方式儲存：</span><span class="sxs-lookup"><span data-stu-id="4f25d-138">With this configuration the order from the example above is stored like this:</span></span>

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
    "_rid": "6QEKAM+BOOABAAAAAAAAAA==",
    "_self": "dbs/6QEKAA==/colls/6QEKAM+BOOA=/docs/6QEKAM+BOOABAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-683c-692e763901d5\"",
    "_attachments": "attachments/",
    "_ts": 1568163674
}
```

<span data-ttu-id="4f25d-139">同時也會內嵌自有實體的集合。</span><span class="sxs-lookup"><span data-stu-id="4f25d-139">Collections of owned entities are embedded as well.</span></span> <span data-ttu-id="4f25d-140">在下一個範例中，我們將使用 `Distributor` 類別和 `StreetAddress` 的集合：</span><span class="sxs-lookup"><span data-stu-id="4f25d-140">For the next example we'll use the `Distributor` class with a collection of `StreetAddress`:</span></span>

[!code-csharp[Distributor](../../../../samples/core/Cosmos/ModelBuilding/Distributor.cs?name=Distributor)]

<span data-ttu-id="4f25d-141">自有實體不需提供明確索引鍵值來儲存：</span><span class="sxs-lookup"><span data-stu-id="4f25d-141">The owned entities don't need to provide explicit key values to be stored:</span></span>

[!code-csharp[OwnedCollection](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=OwnedCollection)]

<span data-ttu-id="4f25d-142">它們會以如下方式保存：</span><span class="sxs-lookup"><span data-stu-id="4f25d-142">They will be persisted in this way:</span></span>

``` json
{
    "Id": 1,
    "Discriminator": "Distributor",
    "id": "Distributor|1",
    "ShippingCenters": [
        {
            "City": "Phoenix",
            "Street": "500 S 48th Street"
        },
        {
            "City": "Anaheim",
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

<span data-ttu-id="4f25d-143">在內部，EF Core 一律需要具有所有追蹤實體的唯一索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="4f25d-143">Internally EF Core always needs to have unique key values for all tracked entities.</span></span> <span data-ttu-id="4f25d-144">根據預設，為擁有類型的集合所建立主索引鍵，由指向擁有者的外部索引鍵屬性和與 JSON 陣列中索引對應的 `int` 屬性組成。</span><span class="sxs-lookup"><span data-stu-id="4f25d-144">The primary key created by default for collections of owned types consists of the foreign key properties pointing to the owner and an `int` property corresponding to the index in the JSON array.</span></span> <span data-ttu-id="4f25d-145">若要擷取這些值，可以使用項目 API：</span><span class="sxs-lookup"><span data-stu-id="4f25d-145">To retrieve these values entry API could be used:</span></span>

[!code-csharp[ImpliedProperties](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=ImpliedProperties)]

> [!TIP]
> <span data-ttu-id="4f25d-146">必要時，您可以變更自有實體類型的預設主索引鍵，但應明確提供索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="4f25d-146">When necessary the default primary key for the owned entity types can be changed, but then key values should be provided explicitly.</span></span>

## <a name="working-with-disconnected-entities"></a><span data-ttu-id="4f25d-147">使用已中斷連線的實體</span><span class="sxs-lookup"><span data-stu-id="4f25d-147">Working with disconnected entities</span></span>

<span data-ttu-id="4f25d-148">每個項目都必須具有 `id` 值，該值對於特定分割區索引鍵是唯一的。</span><span class="sxs-lookup"><span data-stu-id="4f25d-148">Every item needs to have an `id` value that is unique for the given partition key.</span></span> <span data-ttu-id="4f25d-149">根據預設，EF Core 會使用 “|” 作為分隔符號，將鑑別子和主索引鍵值相結合以產生值。</span><span class="sxs-lookup"><span data-stu-id="4f25d-149">By default EF Core generates the value by concatenating the discriminator and the primary key values, using '|' as a delimiter.</span></span> <span data-ttu-id="4f25d-150">僅當實體進入 `Added` 狀態時才會產生索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="4f25d-150">The key values are only generated when an entity enters the `Added` state.</span></span> <span data-ttu-id="4f25d-151">在[連結實體](../../saving/disconnected-entities.md)時，如果它們在 .NET 類型上不具有 `id` 屬性來儲存值，則可能會發生問題。</span><span class="sxs-lookup"><span data-stu-id="4f25d-151">This might pose a problem when [attaching entities](../../saving/disconnected-entities.md) if they don't have an `id` property on the .NET type to store the value.</span></span>

<span data-ttu-id="4f25d-152">若要解決此限制，您可以手動建立並設定 `id` 值，或先將實體標記為已新增，然後將其變更為所需狀態：</span><span class="sxs-lookup"><span data-stu-id="4f25d-152">To work around this limitation one could create and set the `id` value manually or mark the entity as added first, then changing it to the desired state:</span></span>

[!code-csharp[Attach](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?highlight=4&name=Attach)]

<span data-ttu-id="4f25d-153">以下是輸出 JSON：</span><span class="sxs-lookup"><span data-stu-id="4f25d-153">This is the resulting JSON:</span></span>

``` json
{
    "Id": 1,
    "Discriminator": "Distributor",
    "id": "Distributor|1",
    "ShippingCenters": [
        {
            "City": "Phoenix",
            "Street": "500 S 48th Street"
        }
    ],
    "_rid": "JBwtAN8oNYEBAAAAAAAAAA==",
    "_self": "dbs/JBwtAA==/colls/JBwtAN8oNYE=/docs/JBwtAN8oNYEBAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-9377-d7a1ae7c01d5\"",
    "_attachments": "attachments/",
    "_ts": 1572917100
}
```
