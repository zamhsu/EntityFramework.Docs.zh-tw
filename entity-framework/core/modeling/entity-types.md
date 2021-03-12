---
title: 實體類型-EF Core
description: 如何使用 Entity Framework Core 設定和對應實體類型
author: roji
ms.date: 10/06/2020
uid: core/modeling/entity-types
ms.openlocfilehash: 6c8029b64210dd9bc69fd53d9a3b4aa06bf519e0
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023987"
---
# <a name="entity-types"></a>實體類型

在您的內容中包含類型的 DbSet，表示它包含在 EF Core 的模型中;我們通常會將這類型別稱為 *實體*。 EF Core 可以從資料庫讀取和寫入實體實例，如果您使用關係資料庫，EF Core 可以透過遷移來為您的實體建立資料表。

## <a name="including-types-in-the-model"></a>在模型中包含類型

依照慣例，在內容中公開于 DbSet 屬性中的型別會以實體的形式包含在模型中。 也會包含方法中指定的實體類型 `OnModelCreating` ，如同以遞迴方式探索其他探索到之實體類型的導覽屬性所找到的任何類型。

下列程式碼範例中包含所有類型：

* `Blog` 包含在內容上的 DbSet 屬性中，因此會包含在內。
* `Post` 包含，因為它是透過 `Blog.Posts` 導覽屬性來探索。
* `AuditEntry` 因為它是在中指定的 `OnModelCreating` 。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs?name=EntityTypes&highlight=3,7,16)]

## <a name="excluding-types-from-the-model"></a>從模型排除類型

如果您不想要將類型包含在模型中，您可以將它排除：

### <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?name=IgnoreType&highlight=1)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?name=IgnoreType&highlight=3)]

***

### <a name="excluding-from-migrations"></a>從遷移中排除

> [!NOTE]
> EF Core 5.0 引進了從遷移中排除資料表的功能。

將相同的實體類型對應到多個型別時，有時會很有用 `DbContext` 。 尤其是 [在使用系](https://www.martinfowler.com/bliki/BoundedContext.html)結內容時更是如此，因為每個限定的內容通常會有不同的 `DbContext` 類型。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableExcludeFromMigrations.cs?name=TableExcludeFromMigrations&highlight=4)]

使用此設定時，將不會建立 `AspNetUsers` 資料表，但 `IdentityUser` 仍會包含在模型中，而且可以正常使用。

如果您需要再次使用遷移來開始管理資料表，則應該在未排除的位置建立新的遷移 `AspNetUsers` 。 下一個遷移現在會包含對資料表所做的任何變更。

## <a name="table-name"></a>資料表名稱

依照慣例，每個實體類型都會設定成對應至資料庫資料表，名稱與公開實體的 DbSet 屬性相同。 如果指定的實體沒有 DbSet 存在，則會使用類別名稱。

您可以手動設定資料表名稱：

### <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableName.cs?Name=TableName&highlight=1)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableName.cs?Name=TableName&highlight=3-4)]

***

## <a name="table-schema"></a>資料表結構描述

使用關係資料庫時，資料表會依照慣例建立在您資料庫的預設架構中。 例如，Microsoft SQL Server 將使用 `dbo` 架構 (SQLite 不支援架構) 。

您可以設定要在特定架構中建立的資料表，如下所示：

### <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=1)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=3-4)]

***

您也可以使用流暢的 API，在模型層級定義預設架構，而不是指定每個資料表的架構：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultSchema.cs?name=DefaultSchema&highlight=3)]

請注意，設定預設架構也會影響其他資料庫物件，例如序列。

## <a name="view-mapping"></a>視圖對應

您可以使用流暢的 API，將實體類型對應至資料庫檢視。

> [!Note]
> EF 會假設參考的視圖已經存在於資料庫中，而不會在遷移時自動建立。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ViewNameAndSchema.cs?name=ViewNameAndSchema&highlight=1)]

 對應至 view 會移除預設的資料表對應，但是從 EF 5.0 開始，實體類型也可以明確地對應到資料表。 在此情況下，查詢對應將用於查詢，而資料表對應將用於更新。

> [!TIP]
> 若要使用記憶體內部提供者來測試對應至 views 的實體類型，請透過將它們對應至查詢 `ToInMemoryQuery` 。 如需詳細資訊，請參閱使用此技術的可執行 [範例](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Testing/ItemsWebApi/) 。

## <a name="table-valued-function-mapping"></a>資料表值函數對應

您可以將實體類型對應至資料表值函式 (TVF) ，而不是資料庫中的資料表。 為了說明這一點，讓我們使用多篇文章來定義代表 blog 的另一個實體。 在此範例中，實體是 [無索引鍵](xref:core/modeling/keyless-entity-types)，但不一定要這樣做。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs#BlogWithMultiplePostsEntity)]

接下來，在資料庫中建立下列資料表值函式，此函式只會傳回包含多篇文章的 blog，以及與這些 blog 相關聯的貼文數目：

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

現在，實體 `BlogWithMultiplePost` 可透過下列方式對應至此函式：

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs#QueryableFunctionConfigurationToFunction)]

> [!NOTE]
> 為了將實體對應至資料表值函式，函式必須是無參數。

傳統的實體屬性將會對應至 TVF 所傳回的相符資料行。 如果 TVF 傳回的資料行與實體屬性的名稱不同，則可以使用方法來設定它 `HasColumnName` ，就像對應到一般資料表一樣。

當實體類型對應至資料表值函式時，查詢：

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Program.cs#ToFunctionQuery)]

產生下列 SQL：

```sql
SELECT [b].[Url], [b].[PostCount]
FROM [dbo].[BlogsWithMultiplePosts]() AS [b]
WHERE [b].[PostCount] > 3
```

## <a name="table-comments"></a>資料表批註

您可以設定在資料庫資料表上設定的任意文字批註，讓您可以在資料庫中記錄您的架構：

### <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

> [!NOTE]
> 透過資料批註設定批註是在 EF Core 5.0 中引進。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableComment.cs?name=TableComment&highlight=1)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableComment.cs?name=TableComment&highlight=4)]

***
