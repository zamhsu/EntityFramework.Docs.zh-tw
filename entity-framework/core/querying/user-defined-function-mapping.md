---
title: 使用者定義函數對應-EF Core
description: 將使用者定義函數對應至資料庫函式
author: maumar
ms.date: 11/23/2020
uid: core/user-defined-function-mapping
ms.openlocfilehash: ba60abdc9c81b34b8f4ed8f501cf2f7e52ba9d7d
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657793"
---
# <a name="user-defined-function-mapping"></a><span data-ttu-id="edd0f-103">使用者定義函數對應</span><span class="sxs-lookup"><span data-stu-id="edd0f-103">User-defined function mapping</span></span>

<span data-ttu-id="edd0f-104">EF Core 允許在查詢中使用使用者定義的 SQL 函數。</span><span class="sxs-lookup"><span data-stu-id="edd0f-104">EF Core allows for using user-defined SQL functions in queries.</span></span> <span data-ttu-id="edd0f-105">若要這樣做，在模型設定期間，必須將函式對應至 CLR 方法。</span><span class="sxs-lookup"><span data-stu-id="edd0f-105">To do that, the functions need to be mapped to a CLR method during model configuration.</span></span> <span data-ttu-id="edd0f-106">將 LINQ 查詢轉譯為 SQL 時，會呼叫使用者定義函數，而不是它所對應的 CLR 函數。</span><span class="sxs-lookup"><span data-stu-id="edd0f-106">When translating the LINQ query to SQL, the user-defined function is called instead of the CLR function it has been mapped to.</span></span>

## <a name="mapping-a-method-to-a-sql-function"></a><span data-ttu-id="edd0f-107">將方法對應至 SQL 函數</span><span class="sxs-lookup"><span data-stu-id="edd0f-107">Mapping a method to a SQL function</span></span>

<span data-ttu-id="edd0f-108">為了說明使用者定義函數對應的運作方式，讓我們定義下列實體：</span><span class="sxs-lookup"><span data-stu-id="edd0f-108">To illustrate how user-defined function mapping work, let's define the following entities:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#Entities)]

<span data-ttu-id="edd0f-109">和下列模型設定：</span><span class="sxs-lookup"><span data-stu-id="edd0f-109">And the following model configuration:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#EntityConfiguration)]

<span data-ttu-id="edd0f-110">Blog 可以有許多貼文，而且每一篇文章都可以有許多批註。</span><span class="sxs-lookup"><span data-stu-id="edd0f-110">Blog can have many posts and each post can have many comments.</span></span>

<span data-ttu-id="edd0f-111">接下來，建立使用者定義函數，此函式會 `CommentedPostCountForBlog` 根據 blog，針對指定的 blog 傳回至少一個批註的貼文計數 `Id` ：</span><span class="sxs-lookup"><span data-stu-id="edd0f-111">Next, create the user-defined function `CommentedPostCountForBlog`, which returns the count of posts with at least one comment for a given blog, based on the blog `Id`:</span></span>

```sql
CREATE FUNCTION dbo.CommentedPostCountForBlog(@id int)
RETURNS int
AS
BEGIN
    RETURN (SELECT COUNT(*)
        FROM [Posts] AS [p]
        WHERE ([p].[BlogId] = @id) AND ((
            SELECT COUNT(*)
            FROM [Comments] AS [c]
            WHERE [p].[PostId] = [c].[PostId]) > 0));
END
```

<span data-ttu-id="edd0f-112">若要在 EF Core 中使用這個函式，我們會定義下列 CLR 方法，並將其對應至使用者定義函數：</span><span class="sxs-lookup"><span data-stu-id="edd0f-112">To use this function in EF Core, we define the following CLR method, which we map to the user-defined function:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#BasicFunctionDefinition)]

<span data-ttu-id="edd0f-113">CLR 方法的主體並不重要。</span><span class="sxs-lookup"><span data-stu-id="edd0f-113">The body of the CLR method is not important.</span></span> <span data-ttu-id="edd0f-114">除非 EF Core 無法轉譯其引數，否則不會叫用此方法的用戶端。</span><span class="sxs-lookup"><span data-stu-id="edd0f-114">The method will not be invoked client-side, unless EF Core can't translate its arguments.</span></span> <span data-ttu-id="edd0f-115">如果可以轉譯引數，EF Core 只在意方法簽章。</span><span class="sxs-lookup"><span data-stu-id="edd0f-115">If the arguments can be translated, EF Core only cares about the method signature.</span></span>

> [!NOTE]
> <span data-ttu-id="edd0f-116">在此範例中，方法定義在上 `DbContext` ，但也可以定義為其他類別內的靜態方法。</span><span class="sxs-lookup"><span data-stu-id="edd0f-116">In the example, the method is defined on `DbContext`, but it can also be defined as a static method inside other classes.</span></span>

<span data-ttu-id="edd0f-117">此函式定義現在可以與模型設定中的使用者定義函數相關聯：</span><span class="sxs-lookup"><span data-stu-id="edd0f-117">This function definition can now be associated with user-defined function in the model configuration:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#BasicFunctionConfiguration)]

<span data-ttu-id="edd0f-118">根據預設，EF Core 會嘗試將 CLR 函數對應至具有相同名稱的使用者定義函數。</span><span class="sxs-lookup"><span data-stu-id="edd0f-118">By default, EF Core tries to map CLR function to a user-defined function with the same name.</span></span> <span data-ttu-id="edd0f-119">如果名稱不同，我們可以使用 `HasName` 來為要對應的使用者定義函數提供正確的名稱。</span><span class="sxs-lookup"><span data-stu-id="edd0f-119">If the names differ, we can use `HasName` to provide the correct name for the user-defined function we want to map to.</span></span>

<span data-ttu-id="edd0f-120">現在，執行下列查詢：</span><span class="sxs-lookup"><span data-stu-id="edd0f-120">Now, executing the following query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#BasicQuery)]

<span data-ttu-id="edd0f-121">將會產生這個 SQL：</span><span class="sxs-lookup"><span data-stu-id="edd0f-121">Will produce this SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE [dbo].[CommentedPostCountForBlog]([b].[BlogId]) > 1
```

## <a name="mapping-a-method-to-a-custom-sql"></a><span data-ttu-id="edd0f-122">將方法對應至自訂 SQL</span><span class="sxs-lookup"><span data-stu-id="edd0f-122">Mapping a method to a custom SQL</span></span>

<span data-ttu-id="edd0f-123">EF Core 也允許轉換成特定 SQL 的使用者定義函數。</span><span class="sxs-lookup"><span data-stu-id="edd0f-123">EF Core also allows for user-defined functions that get converted to a specific SQL.</span></span> <span data-ttu-id="edd0f-124">在 `HasTranslation` 使用者定義函數設定期間，會使用方法來提供 SQL 運算式。</span><span class="sxs-lookup"><span data-stu-id="edd0f-124">The SQL expression is provided using `HasTranslation` method during user-defined function configuration.</span></span>

<span data-ttu-id="edd0f-125">在下列範例中，我們會建立一個函式，以計算兩個整數之間的百分比差異。</span><span class="sxs-lookup"><span data-stu-id="edd0f-125">In the example below, we'll create a function that computes percentage difference between two integers.</span></span>

<span data-ttu-id="edd0f-126">CLR 方法如下所示：</span><span class="sxs-lookup"><span data-stu-id="edd0f-126">The CLR method is as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#HasTranslationFunctionDefinition)]

<span data-ttu-id="edd0f-127">函式定義如下所示：</span><span class="sxs-lookup"><span data-stu-id="edd0f-127">The function definition is as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#HasTranslationFunctionConfiguration)]

<span data-ttu-id="edd0f-128">定義函數之後，就可以在查詢中使用它。</span><span class="sxs-lookup"><span data-stu-id="edd0f-128">Once we define the function, it can be used in the query.</span></span> <span data-ttu-id="edd0f-129">EF Core 會根據從 HasTranslation 所建立的 SQL 運算式樹狀結構，將方法主體直接轉譯成 SQL，而不是呼叫 database 函數。</span><span class="sxs-lookup"><span data-stu-id="edd0f-129">Instead of calling database function, EF Core will translate the method body directly into SQL based on the SQL expression tree constructed from the HasTranslation.</span></span> <span data-ttu-id="edd0f-130">下列 LINQ 查詢：</span><span class="sxs-lookup"><span data-stu-id="edd0f-130">The following LINQ query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#HasTranslationQuery)]

<span data-ttu-id="edd0f-131">產生下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="edd0f-131">Produces the following SQL:</span></span>

```sql
SELECT 100 * (ABS(CAST([p].[BlogId] AS float) - 3) / ((CAST([p].[BlogId] AS float) + 3) / 2))
FROM [Posts] AS [p]
```

## <a name="mapping-a-queryable-function-to-a-table-valued-function"></a><span data-ttu-id="edd0f-132">將可查詢函數對應至資料表值函式</span><span class="sxs-lookup"><span data-stu-id="edd0f-132">Mapping a queryable function to a table-valued function</span></span>

<span data-ttu-id="edd0f-133">EF Core 也支援使用使用者定義的 CLR 方法對應至資料表值函式，以傳回 `IQueryable` 實體類型的，讓 EF Core 以參數對應 tvf。</span><span class="sxs-lookup"><span data-stu-id="edd0f-133">EF Core also supports mapping to a table-valued function using a user-defined CLR method returning an `IQueryable` of entity types, allowing EF Core to map TVFs with parameters.</span></span> <span data-ttu-id="edd0f-134">此程式類似于將純量使用者定義函數對應至 SQL 函數：我們需要資料庫中的 TVF、LINQ 查詢中使用的 CLR 函數，以及兩者之間的對應。</span><span class="sxs-lookup"><span data-stu-id="edd0f-134">The process is similar to mapping a scalar user-defined function to a SQL function: we need a TVF in the database, a CLR function that is used in the LINQ queries, and a mapping between the two.</span></span>

<span data-ttu-id="edd0f-135">例如，我們將使用資料表值函式，傳回至少有一個批註符合指定「贊」閾值的所有貼文：</span><span class="sxs-lookup"><span data-stu-id="edd0f-135">As an example, we'll use a table-valued function that returns all posts having at least one comment that meets a given "Like" threshold:</span></span>

```sql
CREATE FUNCTION dbo.PostsWithPopularComments(@likeThreshold int)
RETURNS TABLE
AS
RETURN
(
    SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
    FROM [Posts] AS [p]
    WHERE (
        SELECT COUNT(*)
        FROM [Comments] AS [c]
        WHERE ([p].[PostId] = [c].[PostId]) AND ([c].[Likes] >= @likeThreshold)) > 0
)
```

<span data-ttu-id="edd0f-136">CLR 方法簽章如下所示：</span><span class="sxs-lookup"><span data-stu-id="edd0f-136">The CLR method signature is as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#QueryableFunctionDefinition)]

> [!TIP]
> <span data-ttu-id="edd0f-137">`FromExpression`CLR 函數主體中的呼叫可讓您使用函式，而不是一般 DbSet。</span><span class="sxs-lookup"><span data-stu-id="edd0f-137">The `FromExpression` call in the CLR function body allows for the function to be used instead of a regular DbSet.</span></span>

<span data-ttu-id="edd0f-138">以下是對應：</span><span class="sxs-lookup"><span data-stu-id="edd0f-138">And below is the mapping:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#QueryableFunctionConfigurationHasDbFunction)]

> [!CAUTION]
> <span data-ttu-id="edd0f-139">在修正 [問題 23408](https://github.com/dotnet/efcore/issues/23408) 之前，實體類型的對應會 `IQueryable` 覆寫 DbSet 資料表的預設對應。</span><span class="sxs-lookup"><span data-stu-id="edd0f-139">Until [issue 23408](https://github.com/dotnet/efcore/issues/23408) is fixed, mapping to an `IQueryable` of entity types overrides the default mapping to a table for the DbSet.</span></span> <span data-ttu-id="edd0f-140">必要時（例如，當實體不是無索引鍵）時，必須使用方法明確指定資料表的對應 `ToTable` 。</span><span class="sxs-lookup"><span data-stu-id="edd0f-140">If necessary - for example when the entity is not keyless - mapping to the table must be specified explicitly using `ToTable` method.</span></span>

> [!NOTE]
> <span data-ttu-id="edd0f-141">可查詢函數必須對應至資料表值函式，而且無法使用 `HasTranslation` 。</span><span class="sxs-lookup"><span data-stu-id="edd0f-141">Queryable function must be mapped to a table-valued function and can't use of `HasTranslation`.</span></span>

<span data-ttu-id="edd0f-142">對應函式時，下列查詢：</span><span class="sxs-lookup"><span data-stu-id="edd0f-142">When the function is mapped, the following query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#TableValuedFunctionQuery)]

<span data-ttu-id="edd0f-143">生產：</span><span class="sxs-lookup"><span data-stu-id="edd0f-143">Produces:</span></span>

```sql
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [dbo].[PostsWithPopularComments](@likeThreshold) AS [p]
ORDER BY [p].[Rating]
```
