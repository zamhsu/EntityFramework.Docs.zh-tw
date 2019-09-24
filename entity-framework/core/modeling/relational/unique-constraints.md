---
title: 替代索引鍵（唯一條件約束）-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3d419dcf-2b5d-467c-b408-ea03d830721a
uid: core/modeling/relational/unique-constraints
ms.openlocfilehash: 7afcb804aeeccbd5e07c228a8fd9850ca00a2919
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197611"
---
# <a name="alternate-keys-unique-constraints"></a>替代索引鍵 (唯一條件約束)

> [!NOTE]  
> 本節中的組態一般適用於關聯式資料庫。 當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。

模型中的每個替代索引鍵都會引進 unique 條件約束。

## <a name="conventions"></a>慣例

依照慣例，針對替代金鑰引進的索引和條件約束將會命名`AK_<type name>_<property name>`為。 對於複合的替代`<property name>`索引鍵，會變成以底線分隔的屬性名稱清單。

## <a name="data-annotations"></a>資料註釋

不能使用資料批註來設定唯一的條件約束。

## <a name="fluent-api"></a>Fluent API

您可以使用流暢的 API 來設定替代金鑰的索引和條件約束名稱。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/AlternateKeyName.cs?name=Model&highlight=9)]
