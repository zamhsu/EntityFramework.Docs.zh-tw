---
title: 索引的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 4581e7ba-5e7f-452c-9937-0aaf790ba10a
uid: core/modeling/relational/indexes
ms.openlocfilehash: 7dcf27dedbde45302a462a4c41a811b9868e40bb
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197010"
---
# <a name="indexes"></a>索引

> [!NOTE]  
> 本節中的組態一般適用於關聯式資料庫。 當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。

關係資料庫中的索引會對應到與 Entity Framework 核心中的索引相同的概念。

## <a name="conventions"></a>慣例

依照慣例，索引會命名`IX_<type name>_<property name>`為。 對於複合索引`<property name>` ，會變成以底線分隔的屬性名稱清單。

## <a name="data-annotations"></a>資料註釋

無法使用資料批註來設定索引。

## <a name="fluent-api"></a>Fluent API

您可以使用流暢的 API 來設定索引的名稱。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/IndexName.cs?name=Model&highlight=9)]

您也可以指定篩選準則。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/IndexFilter.cs?name=Model&highlight=9)]

使用 SQL Server 提供者 EF 時，針對屬於唯一索引一部分的所有可為 null 的資料行，加入 ' IS NOT Null ' 篩選準則。 若要覆寫此慣例，您`null`可以提供值。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/IndexNoFilter.cs?name=Model&highlight=10)]

### <a name="include-columns-in-sql-server-indexes"></a>在 SQL Server 索引中包含資料行

當查詢中的所有資料行都包含在索引中當做索引鍵或非索引鍵資料行時，您可以設定[內含資料行的索引](https://docs.microsoft.com/sql/relational-databases/indexes/create-indexes-with-included-columns)，以大幅提升查詢效能。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/ForSqlServerHasIndex.cs?name=Model)]
