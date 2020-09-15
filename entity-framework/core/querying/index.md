---
title: 查詢資料 - EF Core
description: 在 Entity Framework Core 中進行查詢的相關資訊概觀
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/index
ms.openlocfilehash: 0bc1ef7d5595d5ed534a118fdc33e1a78648582e
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071117"
---
# <a name="querying-data"></a>查詢資料

Entity Framework Core 使用 Language Integrated Query (LINQ) 查詢來自資料庫的資料。 LINQ 可讓您使用 C# (或您選擇的 .NET 語言) 來撰寫強型別查詢。 它使用您的衍生內容與實體類別來參考資料庫物件。 EF Core 會將 LINQ 查詢的表示法剖析為資料庫提供者。 資料庫提供者接著會將它轉譯為資料庫特定查詢語言 (例如，關聯式資料庫的 SQL)。

> [!TIP]
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) \(英文\)。

下列片段顯示一些有關如何使用 Entity Framework Core 完成常見工作的範例。

## <a name="loading-all-data"></a>載入所有資料

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#LoadingAllData)]

## <a name="loading-a-single-entity"></a>載入單一實體

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#LoadingSingleEntity)]

## <a name="filtering"></a>篩選

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#Filtering)]

## <a name="further-readings"></a>進階閱讀

- 深入了解 [LINQ 查詢運算式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)
- 如需有關 EF Core 中查詢處理方式的詳細資訊，請參閱[查詢運作方式](xref:core/querying/how-query-works)。
