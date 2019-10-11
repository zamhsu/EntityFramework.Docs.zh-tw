---
title: 非同步查詢 - EF Core
author: smitpatel
ms.date: 10/03/2019
ms.assetid: b6429b14-cba0-4af4-878f-b829777c89cb
uid: core/querying/async
ms.openlocfilehash: ce26db32a616dac5edac2a8451014ae63cbfc12d
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181827"
---
# <a name="asynchronous-queries"></a>非同步查詢

非同步查詢可避免在資料庫中執行查詢時封鎖執行緒。 非同步查詢對於在豐富型用戶端應用程式中保持回應式 UI 是很重要的。 它們也可以增加 web 應用程式中的輸送量，讓他們釋放執行緒來服務 web 應用程式中的其他要求。 如需詳細資訊，請參閱[使用 C# 進行非同步程式設計](/dotnet/csharp/async)。

> [!WARNING]  
> EF Core 不支援在相同的內容實例上執行多個平行作業。 您應該一律等候作業完成，再開始下一項作業。 這通常是在每個非同步作業上使用 `await` 關鍵字來完成。

Entity Framework Core 提供一組非同步擴充方法，類似于 LINQ 方法，其會執行查詢並傳回結果。 範例包括 `ToListAsync()`，`ToArrayAsync()`，`SingleAsync()`。 有些 LINQ 運算子（例如 `Where(...)` 或 `OrderBy(...)`）沒有非同步版本，因為這些方法只會建立 LINQ 運算式樹狀架構，而不會導致在資料庫中執行查詢。

> [!IMPORTANT]  
> EF Core 非同步擴充方法定義於 `Microsoft.EntityFrameworkCore` 命名空間中。 您必須先匯入此命名空間，才能使用方法。

[!code-csharp[Main](../../../samples/core/Querying/Async/Sample.cs#ToListAsync)]
