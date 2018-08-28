---
title: 替代索引鍵 （唯一條件約束）-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3d419dcf-2b5d-467c-b408-ea03d830721a
uid: core/modeling/relational/unique-constraints
ms.openlocfilehash: 7ec58ee31aac79e15329dc8542f37fd117772fbe
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994187"
---
# <a name="alternate-keys-unique-constraints"></a>替代索引鍵 （唯一條件約束）

> [!NOTE]  
> 本節中的組態一般適用於關聯式資料庫。 當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。

針對每個模型中的替代索引鍵引進 unique 條件約束。

## <a name="conventions"></a>慣例

依照慣例，導入的替代索引鍵的條件約束與索引將會命名為`AK_<type name>_<property name>`。 對於複合的替代索引鍵`<property name>`會變成屬性名稱的底線分隔清單。

## <a name="data-annotations"></a>資料註釋

Unique 條件約束不可以使用資料註解來設定。

## <a name="fluent-api"></a>Fluent API

您可以使用 Fluent API 來設定其他的索引鍵的索引和條件約束名稱。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/AlternateKeyName.cs?name=Model&highlight=9)]
