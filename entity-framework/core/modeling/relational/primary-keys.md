---
title: 主要金鑰-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c78f8f42-564a-45a4-aca7-3ede9f7ed2bc
uid: core/modeling/relational/primary-keys
ms.openlocfilehash: c195cc37859ea0689b5c6fbb84051564cf96c83a
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656055"
---
# <a name="primary-keys"></a>主索引鍵

> [!NOTE]  
> 本節中的組態一般適用於關聯式資料庫。 當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。

針對每個實體類型的索引鍵，引進了 primary key 條件約束。

## <a name="conventions"></a>慣例

依照慣例，資料庫中的主要金鑰將會命名為 `PK_<type name>`。

## <a name="data-annotations"></a>資料註釋

不能使用資料批註來設定主鍵的關係資料庫特定層面。

## <a name="fluent-api"></a>Fluent API

您可以使用流暢的 API 來設定資料庫中的 primary key 條件約束名稱。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/KeyName.cs?name=KeyName&highlight=9)]
