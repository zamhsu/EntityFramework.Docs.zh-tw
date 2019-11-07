---
title: 索引-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 85b92003-b692-417d-ac1d-76d40dce664b
uid: core/modeling/indexes
ms.openlocfilehash: d1b5cd6853cd24f7e1aa3aace2f0a3455c657cc1
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655704"
---
# <a name="indexes"></a>索引

索引是許多資料存放區的通用概念。 雖然其在資料存放區中的執行可能會有所不同，但它們是用來讓根據資料行（或資料行集）進行查閱更有效率。

## <a name="conventions"></a>慣例

依照慣例，索引會在當做外鍵使用的每一個屬性（或一組屬性）中建立。

## <a name="data-annotations"></a>資料註釋

無法使用資料批註來建立索引。

## <a name="fluent-api"></a>Fluent API

您可以使用流暢的 API，在單一屬性上指定索引。 根據預設，索引不是唯一的。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Index.cs?name=Index&highlight=7,8)]

您也可以指定索引應該是唯一的，這表示沒有兩個實體可以有指定屬性的相同值。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexUnique.cs?name=ModelBuilder&highlight=3)]

您也可以在一個以上的資料行上指定索引。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexComposite.cs?name=Composite&highlight=7,8)]

> [!TIP]  
> 每一組不同的屬性只有一個索引。 如果您使用流暢的 API，在已定義索引（依慣例或先前的設定）的一組屬性上設定索引，則您將會變更該索引的定義。 如果您想要進一步設定依照慣例所建立的索引，這會很有用。
