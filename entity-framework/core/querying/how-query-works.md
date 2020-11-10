---
title: 查詢的運作方式 - EF Core
description: Entity Framework Core 如何在內部編譯及執行查詢的一般資訊
author: ajcvickers
ms.date: 03/17/2020
uid: core/querying/how-query-works
ms.openlocfilehash: 7b3014cf64f8467ccbec10598ea1bb47304dfe43
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430465"
---
# <a name="how-queries-work"></a>查詢的運作方式

Entity Framework Core 使用 Language Integrated Query (LINQ) 查詢來自資料庫的資料。 LINQ 可讓您使用 C# (或您選擇的 .NET 語言)，根據衍生內容和實體類別來寫入強型別查詢。

> [!NOTE]
> 這篇文章已過期，必須更新其中的某些部分，以考慮在查詢管線設計中發生變更。 如果您對此處提及的任何行為有任何疑問，請 [提出問題](https://github.com/dotnet/efcore/issues/new/choose)。

## <a name="the-life-of-a-query"></a>查詢的生命週期

下列描述是每個查詢所經歷之進程的概要說明。

1. LINQ 查詢會由 Entity Framework Core 所處理，以建置已準備好由資料庫提供者所處理的表示方式
   1. 結果會被快取，因此並不需要在每次執行查詢時進行此處理程序
2. 結果會傳遞給資料庫提供者
   1. 資料庫提供者會識別查詢的哪些組件可在資料庫中評估
   2. 查詢的這些元件會轉譯為資料庫特定的查詢語言 (例如，關係資料庫的 SQL) 
   3. 系統會將查詢傳送至資料庫，並傳回結果集 (結果是資料庫的值，而不是實體實例) 
3. 針對結果集中的每個項目
   1. 如果查詢是追蹤查詢，EF 就會檢查資料是否代表已在內容實例的變更追蹤器中的實體。
      * 如果是，就會傳回現有的實體
      * 如果沒有，則會建立新的實體、設定變更追蹤，並傳回新的實體
   2. 如果查詢是無追蹤查詢，則一律會建立並傳回新的實體

## <a name="when-queries-are-executed"></a>查詢執行時

當您呼叫 LINQ 運算子時，您只是要建立查詢的記憶體中標記法。 只有在取用結果時，才會將查詢傳送到資料庫。

以下是導致將查詢傳送到資料庫的最常見作業：

* 在 `for` 迴圈中逐一查看結果
* 使用運算子 `ToList` ，例如、、 `ToArray` `Single` 、 `Count` 或對等的非同步多載

> [!WARNING]  
> **一律驗證使用者輸入：** 雖然 EF Core 使用參數以及在查詢中逸出常值來防止 SQL 插入式攻擊，但是它不會驗證輸入。 每個應用程式需求的適當驗證都應該在 LINQ 查詢中使用不受信任來源的值、指派給實體屬性或傳遞給其他 EF Core Api 之前執行。 這包括用來以動態方式建構查詢的任何使用者輸入。 即使在使用 LINQ 時，如果您接受使用者輸入來建置運算式，就必須確定只會建構預期的運算式。
