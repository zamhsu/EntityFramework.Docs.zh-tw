---
title: 索引的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 4581e7ba-5e7f-452c-9937-0aaf790ba10a
uid: core/modeling/relational/indexes
ms.openlocfilehash: 605b30ce710d9034deab97f695496ec66a576565
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993213"
---
# <a name="indexes"></a>索引

> [!NOTE]  
> 本節中的組態一般適用於關聯式資料庫。 當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。

關聯式資料庫中的索引會對應至相同的概念為 Entity framework core 中的索引。

## <a name="conventions"></a>慣例

依照慣例，索引會命名為`IX_<type name>_<property name>`。 對於複合索引`<property name>`會變成屬性名稱的底線分隔清單。

## <a name="data-annotations"></a>資料註釋

索引不可以使用資料註解來設定。

## <a name="fluent-api"></a>Fluent API

您可以使用 Fluent API 來設定索引的名稱。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexName.cs?name=Model&highlight=9)]

您也可以指定篩選條件。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexFilter.cs?name=Model&highlight=9)]

當使用 SQL Server 提供者 EF 加上 ' IS NOT NULL' 篩選所有可為 null 的資料行，則唯一索引的一部分。 若要覆寫此慣例，您可以提供`null`值。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexNoFilter.cs?name=Model&highlight=10)]
