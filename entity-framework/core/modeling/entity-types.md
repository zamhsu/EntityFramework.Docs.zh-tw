---
title: 實體類型-EF Core
description: 如何使用 Entity Framework Core 設定和對應實體類型
author: roji
ms.date: 12/03/2019
ms.assetid: cbe6935e-2679-4b77-8914-a8d772240cf1
uid: core/modeling/entity-types
ms.openlocfilehash: b3d9ad753637d021d9aa52965da38091ae690f77
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417230"
---
# <a name="entity-types"></a><span data-ttu-id="6bd5a-103">實體型別</span><span class="sxs-lookup"><span data-stu-id="6bd5a-103">Entity Types</span></span>

<span data-ttu-id="6bd5a-104">在您的內容中包含類型的 DbSet，表示它包含在 EF Core 的模型中;我們通常會將這類類型稱為「*實體*」（entity）。</span><span class="sxs-lookup"><span data-stu-id="6bd5a-104">Including a DbSet of a type on your context means that it is included in EF Core's model; we usually refer to such a type as an *entity*.</span></span> <span data-ttu-id="6bd5a-105">EF Core 可以從資料庫讀取和寫入實體實例，而且如果您使用關係資料庫，EF Core 可以透過遷移來為您的實體建立資料表。</span><span class="sxs-lookup"><span data-stu-id="6bd5a-105">EF Core can read and write entity instances from/to the database, and if you're using a relational database, EF Core can create tables for your entities via migrations.</span></span>

## <a name="including-types-in-the-model"></a><span data-ttu-id="6bd5a-106">包含模型中的類型</span><span class="sxs-lookup"><span data-stu-id="6bd5a-106">Including types in the model</span></span>

<span data-ttu-id="6bd5a-107">依照慣例，在內容的 DbSet 屬性中公開的類型會以實體的形式包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="6bd5a-107">By convention, types that are exposed in DbSet properties on your context are included in the model as entities.</span></span> <span data-ttu-id="6bd5a-108">在 `OnModelCreating` 方法中指定的實體類型也包含在內，如同以遞迴方式探索其他探索到之實體類型的導覽屬性所找到的任何類型一樣。</span><span class="sxs-lookup"><span data-stu-id="6bd5a-108">Entity types that are specified in the `OnModelCreating` method are also included, as are any types that are found by recursively exploring the navigation properties of other discovered entity types.</span></span>

<span data-ttu-id="6bd5a-109">在下面的程式碼範例中，包含所有類型：</span><span class="sxs-lookup"><span data-stu-id="6bd5a-109">In the code sample below, all types are included:</span></span>

* <span data-ttu-id="6bd5a-110">包含 `Blog`，因為它是在內容的 DbSet 屬性中公開。</span><span class="sxs-lookup"><span data-stu-id="6bd5a-110">`Blog` is included because it's exposed in a DbSet property on the context.</span></span>
* <span data-ttu-id="6bd5a-111">包含 `Post`，因為它是透過 `Blog.Posts` 導覽屬性進行探索。</span><span class="sxs-lookup"><span data-stu-id="6bd5a-111">`Post` is included because it's discovered via the `Blog.Posts` navigation property.</span></span>
* <span data-ttu-id="6bd5a-112">`AuditEntry`，因為它是在 `OnModelCreating`中指定。</span><span class="sxs-lookup"><span data-stu-id="6bd5a-112">`AuditEntry` because it is specified in `OnModelCreating`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs?name=EntityTypes&highlight=3,7,16)]

## <a name="excluding-types-from-the-model"></a><span data-ttu-id="6bd5a-113">從模型中排除類型</span><span class="sxs-lookup"><span data-stu-id="6bd5a-113">Excluding types from the model</span></span>

<span data-ttu-id="6bd5a-114">如果您不想要將類型包含在模型中，您可以將它排除：</span><span class="sxs-lookup"><span data-stu-id="6bd5a-114">If you don't want a type to be included in the model, you can exclude it:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="6bd5a-115">資料註解</span><span class="sxs-lookup"><span data-stu-id="6bd5a-115">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?name=IgnoreType&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="6bd5a-116">流暢的 API</span><span class="sxs-lookup"><span data-stu-id="6bd5a-116">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?name=IgnoreType&highlight=3)]

***

## <a name="table-name"></a><span data-ttu-id="6bd5a-117">資料表名稱</span><span class="sxs-lookup"><span data-stu-id="6bd5a-117">Table name</span></span>

<span data-ttu-id="6bd5a-118">依照慣例，每個實體類型都會設定為對應至資料庫資料表，其名稱與公開實體的 DbSet 屬性相同。</span><span class="sxs-lookup"><span data-stu-id="6bd5a-118">By convention, each entity type will be set up to map to a database table with the same name as the DbSet property that exposes the entity.</span></span> <span data-ttu-id="6bd5a-119">如果指定的實體沒有 DbSet 存在，則會使用類別名稱。</span><span class="sxs-lookup"><span data-stu-id="6bd5a-119">If no DbSet exists for the given entity, the class name is used.</span></span>

<span data-ttu-id="6bd5a-120">您可以手動設定資料表名稱：</span><span class="sxs-lookup"><span data-stu-id="6bd5a-120">You can manually configure the table name:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="6bd5a-121">資料註解</span><span class="sxs-lookup"><span data-stu-id="6bd5a-121">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableName.cs?Name=TableName&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="6bd5a-122">流暢的 API</span><span class="sxs-lookup"><span data-stu-id="6bd5a-122">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableName.cs?Name=TableName&highlight=3-4)]

***

## <a name="table-schema"></a><span data-ttu-id="6bd5a-123">資料表結構描述</span><span class="sxs-lookup"><span data-stu-id="6bd5a-123">Table schema</span></span>

<span data-ttu-id="6bd5a-124">使用關係資料庫時，資料表會依慣例建立于您資料庫的預設架構中。</span><span class="sxs-lookup"><span data-stu-id="6bd5a-124">When using a relational database, tables are by convention created in your database's default schema.</span></span> <span data-ttu-id="6bd5a-125">例如，Microsoft SQL Server 會使用 `dbo` 架構（SQLite 不支援架構）。</span><span class="sxs-lookup"><span data-stu-id="6bd5a-125">For example, Microsoft SQL Server will use the `dbo` schema (SQLite does not support schemas).</span></span>

<span data-ttu-id="6bd5a-126">您可以設定要在特定架構中建立的資料表，如下所示：</span><span class="sxs-lookup"><span data-stu-id="6bd5a-126">You can configure tables to be created in a specific schema as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="6bd5a-127">資料註解</span><span class="sxs-lookup"><span data-stu-id="6bd5a-127">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="6bd5a-128">流暢的 API</span><span class="sxs-lookup"><span data-stu-id="6bd5a-128">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=3-4)]

***

<span data-ttu-id="6bd5a-129">您也可以使用 Fluent API 定義模型層級的預設架構，而不是指定每個資料表的架構：</span><span class="sxs-lookup"><span data-stu-id="6bd5a-129">Rather than specifying the schema for each table, you can also define the default schema at the model level with the fluent API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultSchema.cs?name=DefaultSchema&highlight=3)]

<span data-ttu-id="6bd5a-130">請注意，設定預設架構也會影響其他資料庫物件，例如序列。</span><span class="sxs-lookup"><span data-stu-id="6bd5a-130">Note that setting the default schema will also affect other database objects, such as sequences.</span></span>
