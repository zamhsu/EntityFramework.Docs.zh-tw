---
title: 非同步查詢 - EF Core
author: smitpatel
ms.date: 10/03/2019
ms.assetid: b6429b14-cba0-4af4-878f-b829777c89cb
uid: core/querying/async
ms.openlocfilehash: ce26db32a616dac5edac2a8451014ae63cbfc12d
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "78417750"
---
# <a name="asynchronous-queries"></a>非同步查詢

非同步查詢可避免在資料庫中執行查詢時封鎖執行緒。 非同步查詢對於在厚客戶端應用程式中保留回應式 UI 非常重要。 它們還可以提高 Web 應用程式中的輸送量,在 Web 應用程式中釋放線程以為 Web 應用程式中的其他請求提供服務。 有關詳細資訊,請參閱[C# 中的非同步編程](/dotnet/csharp/async)。

> [!WARNING]  
> EF Core 不支援在同一上下文實例上運行多個並行操作。 您應該一律等候作業完成，再開始下一項作業。 這通常是在每個異步操作上`await`使用關鍵字來完成的。

實體框架核心提供了一組類似於 LINQ 方法的非同步擴充方法,這些方法執行查詢並返回結果。 範例包括`ToListAsync()``ToArrayAsync()`、 `SingleAsync()`、 、 、 、 某些 LINQ 運算符沒有非同步版本,`Where(...)`例如,`OrderBy(...)`或者因為這些方法僅建構 LINQ 運算式樹,並且不會導致在資料庫中執行查詢。

> [!IMPORTANT]  
> EF Core 非同步擴充方法定義於 `Microsoft.EntityFrameworkCore` 命名空間中。 您必須先匯入此命名空間，才能使用方法。

[!code-csharp[Main](../../../samples/core/Querying/Async/Sample.cs#ToListAsync)]
