---
title: 實體類型-EF Core
description: 如何使用 Entity Framework Core 來設定和對應實體類型
author: roji
ms.date: 10/06/2020
uid: core/modeling/entity-types
ms.openlocfilehash: 9094193640e7cab6db3fed7ae0ab818a455156ca
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429581"
---
# <a name="entity-types"></a>實體類型

在您的內容中包含類型的 DbSet，表示它包含在 EF Core 的模型中;我們通常會將這類型別稱為 *實體* 。 EF Core 可以從資料庫讀取和寫入實體實例，如果您使用關係資料庫，EF Core 可以透過遷移來為您的實體建立資料表。

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
> 在 EF Core 5.0 中新增了從遷移中排除資料表的功能。

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

**_

## <a name="table-schema"></a>資料表結構描述

使用關係資料庫時，資料表會依照慣例建立在您資料庫的預設架構中。 例如，Microsoft SQL Server 會使用 `dbo` 架構 (SQLite 不支援架構) 。

您可以設定要在特定架構中建立的資料表，如下所示：

### <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=1)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=3-4)]

_**

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
> 若要使用記憶體內部提供者來測試對應至 views 的實體類型，請透過將它們對應至查詢 `ToInMemoryQuery` 。 如需詳細資訊，請參閱使用此技術的可執行 [範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) 。
