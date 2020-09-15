---
title: Entity Framework Core 概觀 - EF Core
description: Entity Framework Core 的一般簡介概觀
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: bc2a2676-bc46-493f-bf49-e3cc97994d57
uid: core/index
ms.openlocfilehash: 4816f2d57590ddd2ad4807daa2cc1c5000cd5713
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619491"
---
# <a name="entity-framework-core"></a>Entity Framework Core

Entity Framework (EF) Core 是常見 Entity Framework 資料存取技術的輕量型、可擴充、[開放原始碼](https://github.com/aspnet/EntityFrameworkCore)且跨平台版本。

EF Core 可以當成物件關聯式對應程式 (O/RM)，讓 .NET 開發人員使用 .NET 物件來處理資料庫，而可以省略大部分以往必須自行撰寫的資料存取程式碼。

EF Core 支援許多資料庫引擎，如需詳細資料，請參閱[資料庫提供者](xref:core/providers/index)。

## <a name="the-model"></a>模型

運用 EF Core，使用模型來執行資料存取。 模型包含多個實體類別以及一個代表含資料庫之工作階段的內容物件，可讓您查詢和儲存資料。 若要深入了解，請參閱[建立模型](xref:core/modeling/index)。

您可以從現有資料庫產生模型、撰寫符合您資料庫的模型程式碼，或使用[EF 移轉](xref:core/managing-schemas/migrations/index)來從您的模型建立資料庫，然後讓它隨著您模型的變更逐步發展。

[!code-csharp[Main](../../samples/core/Intro/Model.cs)]

## <a name="querying"></a>查詢

使用 Language Integrated Query (LINQ)，從資料庫中擷取實體類別執行個體。 若要深入了解，請參閱[查詢資料](xref:core/querying/index)。

[!code-csharp[Main](../../samples/core/Intro/Program.cs#Querying)]

## <a name="saving-data"></a>儲存資料

使用您實體類別的執行個體，建立、刪除和修改資料庫中的資料。 若要深入了解，請參閱[儲存資料](xref:core/saving/index)。

[!code-csharp[Main](../../samples/core/Intro/Program.cs#SavingData)]

## <a name="next-steps"></a>後續步驟

如需簡介教學課程，請參閱 [Entity Framework Core 使用者入門](xref:core/get-started/index)。
