---
title: 計算資料行-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e9d81f06-805d-45c9-97c2-3546df654829
uid: core/modeling/relational/computed-columns
ms.openlocfilehash: 4e92ed6d785f3b7bdf54015101bdcddb9e4e0e1c
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655923"
---
# <a name="computed-columns"></a>計算資料行

> [!NOTE]  
> 本節中的組態一般適用於關聯式資料庫。 當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。

計算資料行是在資料庫中計算其值的資料行。 計算資料行可以使用資料表中的其他資料行來計算其值。

## <a name="conventions"></a>慣例

依照慣例，計算資料行不會在模型中建立。

## <a name="data-annotations"></a>資料註釋

無法使用資料批註來設定計算資料行。

## <a name="fluent-api"></a>Fluent API

您可以使用流暢的 API 來指定屬性應對應至計算資料行。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/ComputedColumn.cs?name=ComputedColumn&highlight=9)]
