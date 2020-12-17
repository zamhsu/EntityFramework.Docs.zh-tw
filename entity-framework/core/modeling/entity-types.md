---
title: 實體類型-EF Core
description: 如何使用 Entity Framework Core 來設定和對應實體類型
author: roji
ms.date: 10/06/2020
uid: core/modeling/entity-types
ms.openlocfilehash: ca8cb8560afe374218e763bc0476839187a40ece
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635766"
---
# <a name="entity-types"></a><span data-ttu-id="49b4e-103">實體類型</span><span class="sxs-lookup"><span data-stu-id="49b4e-103">Entity Types</span></span>

<span data-ttu-id="49b4e-104">在您的內容中包含類型的 DbSet，表示它包含在 EF Core 的模型中;我們通常會將這類型別稱為 *實體*。</span><span class="sxs-lookup"><span data-stu-id="49b4e-104">Including a DbSet of a type on your context means that it is included in EF Core's model; we usually refer to such a type as an *entity*.</span></span> <span data-ttu-id="49b4e-105">EF Core 可以從資料庫讀取和寫入實體實例，如果您使用關係資料庫，EF Core 可以透過遷移來為您的實體建立資料表。</span><span class="sxs-lookup"><span data-stu-id="49b4e-105">EF Core can read and write entity instances from/to the database, and if you're using a relational database, EF Core can create tables for your entities via migrations.</span></span>

## <a name="including-types-in-the-model"></a><span data-ttu-id="49b4e-106">在模型中包含類型</span><span class="sxs-lookup"><span data-stu-id="49b4e-106">Including types in the model</span></span>

<span data-ttu-id="49b4e-107">依照慣例，在內容中公開于 DbSet 屬性中的型別會以實體的形式包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="49b4e-107">By convention, types that are exposed in DbSet properties on your context are included in the model as entities.</span></span> <span data-ttu-id="49b4e-108">也會包含方法中指定的實體類型 `OnModelCreating` ，如同以遞迴方式探索其他探索到之實體類型的導覽屬性所找到的任何類型。</span><span class="sxs-lookup"><span data-stu-id="49b4e-108">Entity types that are specified in the `OnModelCreating` method are also included, as are any types that are found by recursively exploring the navigation properties of other discovered entity types.</span></span>

<span data-ttu-id="49b4e-109">下列程式碼範例中包含所有類型：</span><span class="sxs-lookup"><span data-stu-id="49b4e-109">In the code sample below, all types are included:</span></span>

* <span data-ttu-id="49b4e-110">`Blog` 包含在內容上的 DbSet 屬性中，因此會包含在內。</span><span class="sxs-lookup"><span data-stu-id="49b4e-110">`Blog` is included because it's exposed in a DbSet property on the context.</span></span>
* <span data-ttu-id="49b4e-111">`Post` 包含，因為它是透過 `Blog.Posts` 導覽屬性來探索。</span><span class="sxs-lookup"><span data-stu-id="49b4e-111">`Post` is included because it's discovered via the `Blog.Posts` navigation property.</span></span>
* <span data-ttu-id="49b4e-112">`AuditEntry` 因為它是在中指定的 `OnModelCreating` 。</span><span class="sxs-lookup"><span data-stu-id="49b4e-112">`AuditEntry` because it is specified in `OnModelCreating`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs?name=EntityTypes&highlight=3,7,16)]

## <a name="excluding-types-from-the-model"></a><span data-ttu-id="49b4e-113">從模型排除類型</span><span class="sxs-lookup"><span data-stu-id="49b4e-113">Excluding types from the model</span></span>

<span data-ttu-id="49b4e-114">如果您不想要將類型包含在模型中，您可以將它排除：</span><span class="sxs-lookup"><span data-stu-id="49b4e-114">If you don't want a type to be included in the model, you can exclude it:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="49b4e-115">資料批註</span><span class="sxs-lookup"><span data-stu-id="49b4e-115">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?name=IgnoreType&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="49b4e-116">Fluent API</span><span class="sxs-lookup"><span data-stu-id="49b4e-116">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?name=IgnoreType&highlight=3)]

***

### <a name="excluding-from-migrations"></a><span data-ttu-id="49b4e-117">從遷移中排除</span><span class="sxs-lookup"><span data-stu-id="49b4e-117">Excluding from migrations</span></span>

> [!NOTE]
> <span data-ttu-id="49b4e-118">在 EF Core 5.0 中引進了從遷移中排除資料表的功能。</span><span class="sxs-lookup"><span data-stu-id="49b4e-118">The ability to exclude tables from migrations was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="49b4e-119">將相同的實體類型對應到多個型別時，有時會很有用 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="49b4e-119">It is sometimes useful to have the same entity type mapped in multiple `DbContext` types.</span></span> <span data-ttu-id="49b4e-120">尤其是 [在使用系](https://www.martinfowler.com/bliki/BoundedContext.html)結內容時更是如此，因為每個限定的內容通常會有不同的 `DbContext` 類型。</span><span class="sxs-lookup"><span data-stu-id="49b4e-120">This is especially true when using [bounded contexts](https://www.martinfowler.com/bliki/BoundedContext.html), for which it is common to have a different `DbContext` type for each bounded context.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableExcludeFromMigrations.cs?name=TableExcludeFromMigrations&highlight=4)]

<span data-ttu-id="49b4e-121">使用此設定時，將不會建立 `AspNetUsers` 資料表，但 `IdentityUser` 仍會包含在模型中，而且可以正常使用。</span><span class="sxs-lookup"><span data-stu-id="49b4e-121">With this configuration migrations will not create the `AspNetUsers` table, but `IdentityUser` is still included in the model and can be used normally.</span></span>

<span data-ttu-id="49b4e-122">如果您需要再次使用遷移來開始管理資料表，則應該在未排除的位置建立新的遷移 `AspNetUsers` 。</span><span class="sxs-lookup"><span data-stu-id="49b4e-122">If you need to start managing the table using migrations again then a new migration should be created where `AspNetUsers` is not excluded.</span></span> <span data-ttu-id="49b4e-123">下一個遷移現在會包含對資料表所做的任何變更。</span><span class="sxs-lookup"><span data-stu-id="49b4e-123">The next migration will now contain any changes made to the table.</span></span>

## <a name="table-name"></a><span data-ttu-id="49b4e-124">資料表名稱</span><span class="sxs-lookup"><span data-stu-id="49b4e-124">Table name</span></span>

<span data-ttu-id="49b4e-125">依照慣例，每個實體類型都會設定成對應至資料庫資料表，名稱與公開實體的 DbSet 屬性相同。</span><span class="sxs-lookup"><span data-stu-id="49b4e-125">By convention, each entity type will be set up to map to a database table with the same name as the DbSet property that exposes the entity.</span></span> <span data-ttu-id="49b4e-126">如果指定的實體沒有 DbSet 存在，則會使用類別名稱。</span><span class="sxs-lookup"><span data-stu-id="49b4e-126">If no DbSet exists for the given entity, the class name is used.</span></span>

<span data-ttu-id="49b4e-127">您可以手動設定資料表名稱：</span><span class="sxs-lookup"><span data-stu-id="49b4e-127">You can manually configure the table name:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="49b4e-128">資料批註</span><span class="sxs-lookup"><span data-stu-id="49b4e-128">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableName.cs?Name=TableName&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="49b4e-129">Fluent API</span><span class="sxs-lookup"><span data-stu-id="49b4e-129">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableName.cs?Name=TableName&highlight=3-4)]

<span data-ttu-id="49b4e-130">\*\*_</span><span class="sxs-lookup"><span data-stu-id="49b4e-130">\*\*_</span></span>

## <a name="table-schema"></a><span data-ttu-id="49b4e-131">資料表結構描述</span><span class="sxs-lookup"><span data-stu-id="49b4e-131">Table schema</span></span>

<span data-ttu-id="49b4e-132">使用關係資料庫時，資料表會依照慣例建立在您資料庫的預設架構中。</span><span class="sxs-lookup"><span data-stu-id="49b4e-132">When using a relational database, tables are by convention created in your database's default schema.</span></span> <span data-ttu-id="49b4e-133">例如，Microsoft SQL Server 會使用 `dbo` 架構 (SQLite 不支援架構) 。</span><span class="sxs-lookup"><span data-stu-id="49b4e-133">For example, Microsoft SQL Server will use the `dbo` schema (SQLite does not support schemas).</span></span>

<span data-ttu-id="49b4e-134">您可以設定要在特定架構中建立的資料表，如下所示：</span><span class="sxs-lookup"><span data-stu-id="49b4e-134">You can configure tables to be created in a specific schema as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="49b4e-135">資料批註</span><span class="sxs-lookup"><span data-stu-id="49b4e-135">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="49b4e-136">Fluent API</span><span class="sxs-lookup"><span data-stu-id="49b4e-136">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=3-4)]

_*_

<span data-ttu-id="49b4e-137">您也可以使用流暢的 API，在模型層級定義預設架構，而不是指定每個資料表的架構：</span><span class="sxs-lookup"><span data-stu-id="49b4e-137">Rather than specifying the schema for each table, you can also define the default schema at the model level with the fluent API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultSchema.cs?name=DefaultSchema&highlight=3)]

<span data-ttu-id="49b4e-138">請注意，設定預設架構也會影響其他資料庫物件，例如序列。</span><span class="sxs-lookup"><span data-stu-id="49b4e-138">Note that setting the default schema will also affect other database objects, such as sequences.</span></span>

## <a name="view-mapping"></a><span data-ttu-id="49b4e-139">視圖對應</span><span class="sxs-lookup"><span data-stu-id="49b4e-139">View mapping</span></span>

<span data-ttu-id="49b4e-140">您可以使用流暢的 API，將實體類型對應至資料庫檢視。</span><span class="sxs-lookup"><span data-stu-id="49b4e-140">Entity types can be mapped to database views using the Fluent API.</span></span>

> [!Note]
> <span data-ttu-id="49b4e-141">EF 會假設參考的視圖已經存在於資料庫中，而不會在遷移時自動建立。</span><span class="sxs-lookup"><span data-stu-id="49b4e-141">EF will assume that the referenced view already exists in the database, it will not create it automatically in a migration.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ViewNameAndSchema.cs?name=ViewNameAndSchema&highlight=1)]

 <span data-ttu-id="49b4e-142">對應至 view 會移除預設的資料表對應，但是從 EF 5.0 開始，實體類型也可以明確地對應到資料表。</span><span class="sxs-lookup"><span data-stu-id="49b4e-142">Mapping to a view will remove the default table mapping, but starting with EF 5.0 the entity type can also be mapped to a table explicitly.</span></span> <span data-ttu-id="49b4e-143">在此情況下，查詢對應將用於查詢，而資料表對應將用於更新。</span><span class="sxs-lookup"><span data-stu-id="49b4e-143">In this case the query mapping will be used for queries and the table mapping will be used for updates.</span></span>

> [!TIP]
> <span data-ttu-id="49b4e-144">若要使用記憶體內部提供者來測試對應至 views 的實體類型，請透過將它們對應至查詢 `ToInMemoryQuery` 。</span><span class="sxs-lookup"><span data-stu-id="49b4e-144">To test entity types mapped to views using the in-memory provider map them to a query via `ToInMemoryQuery`.</span></span> <span data-ttu-id="49b4e-145">如需詳細資訊，請參閱使用此技術的可執行 [範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) 。</span><span class="sxs-lookup"><span data-stu-id="49b4e-145">See a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) using this technique for more details.</span></span>

## <a name="table-valued-function-mapping"></a><span data-ttu-id="49b4e-146">資料表值函數對應</span><span class="sxs-lookup"><span data-stu-id="49b4e-146">Table-valued function mapping</span></span>

<span data-ttu-id="49b4e-147">您可以將實體類型對應至資料表值函式 (TVF) ，而不是資料庫中的資料表。</span><span class="sxs-lookup"><span data-stu-id="49b4e-147">It's possible to map an entity type to a table-valued function (TVF) instead of a table in the database.</span></span> <span data-ttu-id="49b4e-148">為了說明這一點，讓我們使用多篇文章來定義代表 blog 的另一個實體。</span><span class="sxs-lookup"><span data-stu-id="49b4e-148">To illustrate this, let's define another entity that represents blog with multiple posts.</span></span> <span data-ttu-id="49b4e-149">在此範例中，實體是 [無索引鍵](xref:core/modeling/keyless-entity-types)，但不一定要這樣做。</span><span class="sxs-lookup"><span data-stu-id="49b4e-149">In the example, the entity is [keyless](xref:core/modeling/keyless-entity-types), but it doesn't have to be.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs#BlogWithMultiplePostsEntity)]

<span data-ttu-id="49b4e-150">接下來，在資料庫中建立下列資料表值函式，此函式只會傳回包含多篇文章的 blog，以及與這些 blog 相關聯的貼文數目：</span><span class="sxs-lookup"><span data-stu-id="49b4e-150">Next, create the following table-valued function in the database, which returns only blogs with multiple posts as well as the number of posts associated with each of these blogs:</span></span>

```sql
CREATE FUNCTION dbo.BlogsWithMultiplePosts()
RETURNS TABLE
AS
RETURN
(
    SELECT b.Url, COUNT(p.BlogId) AS PostCount
    FROM Blogs AS b
    JOIN Posts AS p ON b.BlogId = p.BlogId
    GROUP BY b.BlogId, b.Url
    HAVING COUNT(p.BlogId) > 1
)
```

<span data-ttu-id="49b4e-151">現在，實體 `BlogWithMultiplePost` 可透過下列方式對應至此函式：</span><span class="sxs-lookup"><span data-stu-id="49b4e-151">Now, the entity `BlogWithMultiplePost` can be mapped to this function in a following way:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs#QueryableFunctionConfigurationToFunction)]

> [!NOTE]
> <span data-ttu-id="49b4e-152">為了將實體對應至資料表值函式，函式必須是無參數。</span><span class="sxs-lookup"><span data-stu-id="49b4e-152">In order to map an entity to a table-valued function the function must be parameterless.</span></span>

<span data-ttu-id="49b4e-153">傳統的實體屬性將會對應至 TVF 所傳回的相符資料行。</span><span class="sxs-lookup"><span data-stu-id="49b4e-153">Conventionally the entity properties will be mapped to matching columns returned by the TVF.</span></span> <span data-ttu-id="49b4e-154">如果 TVF 傳回的資料行與實體屬性的名稱不同，則可以使用方法來設定它 `HasColumnName` ，就像對應到一般資料表一樣。</span><span class="sxs-lookup"><span data-stu-id="49b4e-154">If the columns returned by TVF has different name than entity property then it can be configured using `HasColumnName` method, just like when mapping to a regular table.</span></span>

<span data-ttu-id="49b4e-155">當實體類型對應至資料表值函式時，查詢：</span><span class="sxs-lookup"><span data-stu-id="49b4e-155">When the entity type is mapped to a table-valued function, the query:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Program.cs#ToFunctionQuery)]

<span data-ttu-id="49b4e-156">產生下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="49b4e-156">Produces the following SQL:</span></span>

```sql
SELECT [b].[Url], [b].[PostCount]
FROM [dbo].[BlogsWithMultiplePosts]() AS [b]
WHERE [b].[PostCount] > 3
```

## <a name="table-comments"></a><span data-ttu-id="49b4e-157">資料表批註</span><span class="sxs-lookup"><span data-stu-id="49b4e-157">Table comments</span></span>

<span data-ttu-id="49b4e-158">您可以設定在資料庫資料表上設定的任意文字批註，讓您可以在資料庫中記錄您的架構：</span><span class="sxs-lookup"><span data-stu-id="49b4e-158">You can set an arbitrary text comment that gets set on the database table, allowing you to document your schema in the database:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="49b4e-159">資料批註</span><span class="sxs-lookup"><span data-stu-id="49b4e-159">Data Annotations</span></span>](#tab/data-annotations)

> [!NOTE]
> <span data-ttu-id="49b4e-160">透過資料批註設定批註是在 EF Core 5.0 中引進。</span><span class="sxs-lookup"><span data-stu-id="49b4e-160">Setting comments via data annotations was introduced in EF Core 5.0.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableComment.cs?name=TableComment&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="49b4e-161">Fluent API</span><span class="sxs-lookup"><span data-stu-id="49b4e-161">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableComment.cs?name=TableComment&highlight=4)]

<span data-ttu-id="49b4e-162">_\*\*</span><span class="sxs-lookup"><span data-stu-id="49b4e-162">_\*\*</span></span>
