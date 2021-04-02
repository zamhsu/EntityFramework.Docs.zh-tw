---
title: 使用者定義函數對應-EF Core
description: 將使用者定義函數對應至資料庫函式
author: maumar
ms.date: 11/23/2020
uid: core/querying/user-defined-function-mapping
ms.openlocfilehash: 3b581a3e70112a3b538b55e47ed63da6571a179b
ms.sourcegitcommit: 196ebb726d99c2fa3f702d599f4bdae5e938cb1f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2021
ms.locfileid: "106165964"
---
# <a name="user-defined-function-mapping"></a>使用者定義函數對應

EF Core 允許在查詢中使用使用者定義的 SQL 函數。 若要這樣做，在模型設定期間，必須將函式對應至 CLR 方法。 將 LINQ 查詢轉譯為 SQL 時，會呼叫使用者定義函數，而不是它所對應的 CLR 函數。

## <a name="mapping-a-method-to-a-sql-function"></a>將方法對應至 SQL 函數

為了說明使用者定義函數對應的運作方式，讓我們定義下列實體：

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#Entities)]

和下列模型設定：

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#EntityConfiguration)]

Blog 可以有許多貼文，而且每一篇文章都可以有許多批註。

接下來，建立使用者定義函數，此函式會 `CommentedPostCountForBlog` 根據 blog，針對指定的 blog 傳回至少一個批註的貼文計數 `Id` ：

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

若要在 EF Core 中使用這個函式，我們會定義下列 CLR 方法，並將其對應至使用者定義函數：

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#BasicFunctionDefinition)]

CLR 方法的主體並不重要。 除非 EF Core 無法轉譯其引數，否則不會叫用此方法的用戶端。 如果可以轉譯引數，EF Core 只在意方法簽章。

> [!NOTE]
> 在此範例中，方法定義在上 `DbContext` ，但也可以定義為其他類別內的靜態方法。

此函式定義現在可以與模型設定中的使用者定義函數相關聯：

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#BasicFunctionConfiguration)]

根據預設，EF Core 會嘗試將 CLR 函數對應至具有相同名稱的使用者定義函數。 如果名稱不同，我們可以使用 `HasName` 來為要對應的使用者定義函數提供正確的名稱。

現在，執行下列查詢：

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#BasicQuery)]

將會產生這個 SQL：

```sql
SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE [dbo].[CommentedPostCountForBlog]([b].[BlogId]) > 1
```

## <a name="mapping-a-method-to-a-custom-sql"></a>將方法對應至自訂 SQL

EF Core 也允許轉換成特定 SQL 的使用者定義函數。 在 `HasTranslation` 使用者定義函數設定期間，會使用方法來提供 SQL 運算式。

在下列範例中，我們會建立一個函式，以計算兩個整數之間的百分比差異。

CLR 方法如下所示：

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#HasTranslationFunctionDefinition)]

函式定義如下所示：

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#HasTranslationFunctionConfiguration)]

定義函數之後，就可以在查詢中使用它。 EF Core 會根據從 HasTranslation 所建立的 SQL 運算式樹狀結構，將方法主體直接轉譯成 SQL，而不是呼叫 database 函數。 下列 LINQ 查詢：

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#HasTranslationQuery)]

產生下列 SQL：

```sql
SELECT 100 * (ABS(CAST([p].[BlogId] AS float) - 3) / ((CAST([p].[BlogId] AS float) + 3) / 2))
FROM [Posts] AS [p]
```

## <a name="configuring-nullability-of-user-defined-function-based-on-its-arguments"></a>根據使用者自訂函數的引數，設定其 null 屬性

如果使用者定義函數只能 `null` 在其中一個或多個引數為時傳回 `null` ，EFCore 會提供指定該函式的方式，以產生更高效能的 SQL。 您可以藉由新增相關函式參數模型設定的呼叫來完成此 `PropagatesNullability()` 工作。

為了說明這一點，請定義使用者功能 `ConcatStrings` ：

```sql
CREATE FUNCTION [dbo].[ConcatStrings] (@prm1 nvarchar(max), @prm2 nvarchar(max))
RETURNS nvarchar(max)
AS
BEGIN
    RETURN @prm1 + @prm2;
END
```

還有兩個對應至它的 CLR 方法：

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#NullabilityPropagationFunctionDefinition)]

方法) 內的模型設定 (如下所示 `OnModelCreating` ：

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#NullabilityPropagationModelConfiguration)]

第一個函式是以標準方式進行設定。 第二個函式會設定為利用 null 屬性傳播優化，以提供有關函數如何針對 null 參數的行為的詳細資訊。

發出下列查詢時：

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#NullabilityPropagationExamples)]

我們會取得這個 SQL：

```sql
SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE ([dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) <> N'Lorem ipsum...') OR [dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) IS NULL

SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE ([dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) <> N'Lorem ipsum...') OR ([b].[Url] IS NULL OR [b].[Rating] IS NULL)
```

第二個查詢不需要重新評估函數本身，即可測試其 null 屬性。

> [!NOTE]
> 只有在函式 `null` 的參數為時，才會傳回此優化 `null` 。

## <a name="mapping-a-queryable-function-to-a-table-valued-function"></a>將可查詢函數對應至資料表值函式

EF Core 也支援使用使用者定義的 CLR 方法對應至資料表值函式，以傳回 `IQueryable` 實體類型的，讓 EF Core 以參數對應 tvf。 此程式類似于將純量使用者定義函數對應至 SQL 函數：我們需要資料庫中的 TVF、LINQ 查詢中使用的 CLR 函數，以及兩者之間的對應。

例如，我們將使用資料表值函式，傳回至少有一個批註符合指定「贊」閾值的所有貼文：

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

CLR 方法簽章如下所示：

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#QueryableFunctionDefinition)]

> [!TIP]
> `FromExpression`CLR 函數主體中的呼叫可讓您使用函式，而不是一般 DbSet。

以下是對應：

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#QueryableFunctionConfigurationHasDbFunction)]

> [!CAUTION]
> 在修正 [問題 23408](https://github.com/dotnet/efcore/issues/23408) 之前，實體類型的對應會 `IQueryable` 覆寫 DbSet 資料表的預設對應。 必要時（例如，當實體不是無索引鍵）時，必須使用方法明確指定資料表的對應 `ToTable` 。

> [!NOTE]
> 可查詢函數必須對應至資料表值函式，而且無法使用 `HasTranslation` 。

對應函式時，下列查詢：

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#TableValuedFunctionQuery)]

生產：

```sql
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [dbo].[PostsWithPopularComments](@likeThreshold) AS [p]
ORDER BY [p].[Rating]
```
