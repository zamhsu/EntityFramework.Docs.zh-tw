---
title: "非同步儲存-EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 01/24/2017
ms.assetid: b64a606e-ecd9-4807-829a-b6ec05ade33f
ms.technology: entity-framework-core
uid: core/saving/async
ms.openlocfilehash: 640e5f131b0e9ef4e5028d1dcaf80f3e5bbd9d9b
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="asynchronous-saving"></a>非同步儲存

非同步儲存可避免封鎖執行緒的變更會寫入至資料庫時。 這可避免凍結大型用戶端應用程式的 UI。 非同步作業，也可以增加 web 應用程式，其中執行緒可以釋放資料庫作業完成時服務的其他要求中的輸送量。 如需詳細資訊，請參閱[在 C# 中的非同步程式設計](https://docs.microsoft.com/dotnet/csharp/async)。

> [!WARNING]  
> EF 核心不支援在相同的內容執行個體上執行的多個平行作業。 您應該永遠等候作業完成，然後再開始下一項作業。 這通常是使用`await`上每個非同步作業的關鍵字。

Entity Framework Core 提供`DbContext.SaveChangesAsync()`做為非同步替代`DbContext.SaveChanges()`。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Async/Sample.cs#Sample)]
