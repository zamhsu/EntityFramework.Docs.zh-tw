---
title: 替代索引鍵 （唯一條件約束）-EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 3d419dcf-2b5d-467c-b408-ea03d830721a
ms.technology: entity-framework-core
uid: core/modeling/relational/unique-constraints
ms.openlocfilehash: 1b7e2bef6ede95f8c27211ba00dcc6b97cccde9b
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052788"
---
# <a name="alternate-keys-unique-constraints"></a>替代索引鍵 （唯一條件約束）

> [!NOTE]  
> 本節中的設定是一般適用於關聯式資料庫。 當您安裝的關聯式資料庫提供者，如下所示的擴充方法會變成可用 (因為共用*Microsoft.EntityFrameworkCore.Relational*封裝)。

針對每個模型中的替代索引鍵引進 unique 條件約束。

## <a name="conventions"></a>慣例

依照慣例，會引進其他的索引鍵的條件約束與索引將被命名為`AK_<type name>_<property name>`。 對於複合替代索引鍵`<property name>`變成屬性名稱的底線分隔清單。

## <a name="data-annotations"></a>資料註釋

Unique 條件約束不可以使用資料註解來設定。

## <a name="fluent-api"></a>關於 fluent 應用程式開發介面

您可以使用 fluent 應用程式開發的應用程式開發介面來設定其他的索引鍵的索引和條件約束名稱。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/AlternateKeyName.cs?name=Model&highlight=9)]
