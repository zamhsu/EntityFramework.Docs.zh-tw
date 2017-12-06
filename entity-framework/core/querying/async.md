---
title: "非同步查詢的 EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 01/24/2017
ms.assetid: b6429b14-cba0-4af4-878f-b829777c89cb
ms.technology: entity-framework-core
uid: core/querying/async
ms.openlocfilehash: 6554f04d0edfe0ca2ee72ebed8b878a1997a9500
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="asynchronous-queries"></a>非同步查詢

非同步查詢避免封鎖執行緒，而資料庫中執行查詢。 這可避免凍結大型用戶端應用程式的 UI。 非同步作業，也可以增加 web 應用程式，其中執行緒可以釋放資料庫作業完成時服務的其他要求中的輸送量。 如需詳細資訊，請參閱[在 C# 中的非同步程式設計](https://docs.microsoft.com/dotnet/csharp/async)。

> [!WARNING]  
> EF 核心不支援在相同的內容執行個體上執行的多個平行作業。 您應該永遠等候作業完成，然後再開始下一項作業。 這通常是使用`await`上每個非同步作業的關鍵字。

Entity Framework Core 提供一組可用來當做替代造成查詢執行的 LINQ 方法並傳回結果的非同步擴充方法。 範例包括`ToListAsync()`， `ToArrayAsync()`，`SingleAsync()`等等。沒有 LINQ 運算子的非同步版本例如`Where(...)`，`OrderBy(...)`等因為這些方法僅建置 LINQ 運算式樹狀架構，而不會造成查詢執行在資料庫中。

> [!IMPORTANT]  
> EF 核心非同步擴充方法中定義`Microsoft.EntityFrameworkCore`命名空間。 此命名空間必須匯入可用的方法。

[!code-csharp[Main](../../../samples/core/Querying/Querying/Async/Sample.cs#Sample)]
