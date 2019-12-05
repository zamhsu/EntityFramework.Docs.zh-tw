---
title: 索引（關係資料庫）-EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/modeling/relational/indexes
ms.openlocfilehash: e14615275f85ee9b6b32d080905465d33963feca
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824571"
---
# <a name="indexes-relational-database"></a>索引（關係資料庫）

> [!NOTE]  
> 本節中的組態一般適用於關聯式資料庫。 當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。

關係資料庫中的索引會對應到與 Entity Framework 核心中的索引相同的概念。

## <a name="conventions"></a>慣例

依照慣例，索引會命名為 `IX_<type name>_<property name>`。 若為複合索引，`<property name>` 會變成以底線分隔的屬性名稱清單。

## <a name="data-annotations"></a>資料註釋

無法使用資料批註來設定索引。

## <a name="fluent-api"></a>Fluent API

您可以使用流暢的 API 來設定索引的名稱。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/IndexName.cs?name=Model&highlight=9)]

您也可以指定篩選準則。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/IndexFilter.cs?name=Model&highlight=9)]

使用 SQL Server 提供者 EF 時，會為屬於唯一索引一部分的所有可為 null 的資料行加入 `'IS NOT NULL'` 篩選準則。 若要覆寫此慣例，您可以提供 `null` 值。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/IndexNoFilter.cs?name=Model&highlight=10)]

### <a name="include-columns-in-sql-server-indexes"></a>在 SQL Server 索引中包含資料行

當查詢中的所有資料行都包含在索引中當做索引鍵或非索引鍵資料行時，您可以設定[內含資料行的索引](https://docs.microsoft.com/sql/relational-databases/indexes/create-indexes-with-included-columns)，以大幅提升查詢效能。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/IndexInclude.cs?name=Model)]
