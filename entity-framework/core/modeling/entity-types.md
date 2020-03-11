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
# <a name="entity-types"></a>實體型別

在您的內容中包含類型的 DbSet，表示它包含在 EF Core 的模型中;我們通常會將這類類型稱為「*實體*」（entity）。 EF Core 可以從資料庫讀取和寫入實體實例，而且如果您使用關係資料庫，EF Core 可以透過遷移來為您的實體建立資料表。

## <a name="including-types-in-the-model"></a>包含模型中的類型

依照慣例，在內容的 DbSet 屬性中公開的類型會以實體的形式包含在模型中。 在 `OnModelCreating` 方法中指定的實體類型也包含在內，如同以遞迴方式探索其他探索到之實體類型的導覽屬性所找到的任何類型一樣。

在下面的程式碼範例中，包含所有類型：

* 包含 `Blog`，因為它是在內容的 DbSet 屬性中公開。
* 包含 `Post`，因為它是透過 `Blog.Posts` 導覽屬性進行探索。
* `AuditEntry`，因為它是在 `OnModelCreating`中指定。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs?name=EntityTypes&highlight=3,7,16)]

## <a name="excluding-types-from-the-model"></a>從模型中排除類型

如果您不想要將類型包含在模型中，您可以將它排除：

### <a name="data-annotations"></a>[資料註解](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?name=IgnoreType&highlight=1)]

### <a name="fluent-api"></a>[流暢的 API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?name=IgnoreType&highlight=3)]

***

## <a name="table-name"></a>資料表名稱

依照慣例，每個實體類型都會設定為對應至資料庫資料表，其名稱與公開實體的 DbSet 屬性相同。 如果指定的實體沒有 DbSet 存在，則會使用類別名稱。

您可以手動設定資料表名稱：

### <a name="data-annotations"></a>[資料註解](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableName.cs?Name=TableName&highlight=1)]

### <a name="fluent-api"></a>[流暢的 API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableName.cs?Name=TableName&highlight=3-4)]

***

## <a name="table-schema"></a>資料表結構描述

使用關係資料庫時，資料表會依慣例建立于您資料庫的預設架構中。 例如，Microsoft SQL Server 會使用 `dbo` 架構（SQLite 不支援架構）。

您可以設定要在特定架構中建立的資料表，如下所示：

### <a name="data-annotations"></a>[資料註解](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=1)]

### <a name="fluent-api"></a>[流暢的 API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=3-4)]

***

您也可以使用 Fluent API 定義模型層級的預設架構，而不是指定每個資料表的架構：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultSchema.cs?name=DefaultSchema&highlight=3)]

請注意，設定預設架構也會影響其他資料庫物件，例如序列。
