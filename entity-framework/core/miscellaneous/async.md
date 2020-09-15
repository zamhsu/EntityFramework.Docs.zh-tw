---
title: 非同步程式設計-EF Core
description: 使用 Entity Framework Core 以非同步方式查詢和儲存資料
author: roji
ms.date: 9/2/2020
uid: core/miscellaneous/async
ms.openlocfilehash: cefbe32b34a38ed6d749ef3ddfff210d5db12332
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071894"
---
# <a name="asynchronous-programming"></a>非同步程式設計

非同步作業可避免在資料庫中執行查詢時封鎖執行緒。 非同步作業對於在豐富的用戶端應用程式中保持回應式 UI 很重要，也可以在 web 應用程式中增加輸送量，以便在 web 應用程式中釋放執行緒，以服務其他要求。

在 .NET standard 之後，EF Core 提供非同步對應給所有執行 i/o 的同步方法。 這些與同步處理方法具有相同的效果，而且可以搭配 c # `async` 和關鍵字使用 `await` 。 例如，不使用 DbCoNtext，它會在執行資料庫 i/o 時封鎖執行緒，DbCoNtext. SaveChangesAsync 可以使用：

[!code-csharp[Main](../../../samples/core/Miscellaneous/Async/Program.cs#SaveChangesAsync)]

如需詳細資訊，請參閱 [一般 c # 非同步程式設計](/dotnet/csharp/async)檔。

> [!WARNING]
> EF Core 不支援在相同的內容實例上執行多個平行作業。 您應該一律等候作業完成，再開始下一項作業。 這通常會 `await` 在每個非同步作業上使用關鍵字來完成。

> [!NOTE]
> EF Core 將取消權杖傳遞給使用中的基礎資料庫提供者 (例如 SqlClient) 。 這些權杖可能會或可能不會被接受-請洽詢您資料庫提供者的檔。  

## <a name="async-linq-operators"></a>非同步 LINQ 運算子

為了支援以非同步方式執行 LINQ 查詢，EF Core 提供一組非同步擴充方法，可執行查詢並傳回結果。 這些對等的標準、同步 LINQ 運算子包括 ToListAsync、SingleAsync、AsAsyncEnumerable 等等：

[!code-csharp[Main](../../../samples/core/Miscellaneous/Async/Program.cs#ToListAsync)]

請注意，某些 LINQ 運算子（例如 Where 或 OrderBy）沒有任何非同步版本，因為這些運算子只會建立 LINQ 運算式樹狀架構，而不會在資料庫中執行查詢。 只有造成查詢執行的運算子具有非同步對應專案。

> [!IMPORTANT]
> EF Core 非同步擴充方法定義於 `Microsoft.EntityFrameworkCore` 命名空間中。 您必須先匯入此命名空間，才能使用方法。

## <a name="client-side-async-linq-operators"></a>用戶端非同步 LINQ 運算子

以上所討論的非同步 LINQ 運算子只能用於 EF 查詢-您無法將它們與用戶端 LINQ to Objects 查詢搭配使用。 若要在 EF 之外執行用戶端非同步 LINQ 作業，請使用 system.string [封裝](https://www.nuget.org/packages/System.Interactive.Async);在用戶端上執行無法在伺服器上進行評估的作業時，此封裝會特別有用。

可惜的是，參考 system.string 會導致套用至 EF DbSets 的 LINQ 運算子發生不明確的調用編譯錯誤;這會讓您難以在相同專案中使用 EF 和 Async。 若要解決此問題，請將 AsQueryable 新增至您的 DbSet：

[!code-csharp[Main](../../../samples/core/Miscellaneous/AsyncWithSystemInteractive/Program.cs#SystemInteractiveAsync)]
