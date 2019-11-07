---
title: 外鍵條件約束-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: dbaf4bac-1fd5-46c0-ac57-64d7153bc574
uid: core/modeling/relational/fk-constraints
ms.openlocfilehash: df739f01a799ec8edad4cf44d8cf50edf292992f
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656002"
---
# <a name="foreign-key-constraints"></a>外部索引鍵條件約束

> [!NOTE]  
> 本節中的組態一般適用於關聯式資料庫。 當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。

針對模型中的每個關聯性引進外鍵條件約束。

## <a name="conventions"></a>慣例

依照慣例，外鍵條件約束會命名為 `FK_<dependent type name>_<principal type name>_<foreign key property name>`。 對於複合外鍵 `<foreign key property name>` 會變成以底線分隔的外鍵屬性名稱清單。

## <a name="data-annotations"></a>資料註釋

外鍵條件約束名稱無法使用資料批註來設定。

## <a name="fluent-api"></a>Fluent API

您可以使用流暢的 API 來設定關聯性的外鍵條件約束名稱。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/RelationshipConstraintName.cs?name=Constraint&highlight=12)]
