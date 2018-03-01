---
title: "索引的 EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 4581e7ba-5e7f-452c-9937-0aaf790ba10a
ms.technology: entity-framework-core
uid: core/modeling/relational/indexes
ms.openlocfilehash: f577fccfefc6908edf2ac47ae630323d7a9f5f2b
ms.sourcegitcommit: b2d94cebdc32edad4fecb07e53fece66437d1b04
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="indexes"></a>索引

> [!NOTE]  
> 本節中的組態一般適用於關聯式資料庫。 當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。

關聯式資料庫中的索引對應到相同的概念與 Entity Framework 的核心中的索引。

## <a name="conventions"></a>慣例

根據慣例，索引會命名為`IX_<type name>_<property name>`。 針對複合索引`<property name>`變成屬性名稱的底線分隔清單。

## <a name="data-annotations"></a>資料註釋

索引不可以使用資料註解來設定。

## <a name="fluent-api"></a>Fluent API

您可以使用 fluent 應用程式開發的應用程式開發介面來設定索引的名稱。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexName.cs?name=Model&highlight=9)]

您也可以指定篩選器。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexFilter.cs?name=Model&highlight=9)]

當使用 SQL Server 提供者 EF 加上 ' IS NOT NULL' 篩選所有可為 null 的資料行唯一索引的一部分。 若要覆寫您可以提供這個慣例`null`值。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexNoFilter.cs?name=Model&highlight=10)]
