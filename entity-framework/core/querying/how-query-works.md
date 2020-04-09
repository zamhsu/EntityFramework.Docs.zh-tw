---
title: 查詢的運作方式 - EF Core
author: ajcvickers
ms.date: 03/17/2020
ms.assetid: de2e34cd-659b-4cab-b5ed-7a979c6bf120
uid: core/querying/how-query-works
ms.openlocfilehash: e8a50efe31468ea8df211602636dd474550bc0ef
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80136245"
---
# <a name="how-queries-work"></a>查詢的運作方式

Entity Framework Core 使用 Language Integrated Query (LINQ) 查詢來自資料庫的資料。 LINQ 可讓您使用 C# (或您選擇的 .NET 語言)，根據衍生內容和實體類別來寫入強型別查詢。

## <a name="the-life-of-a-query"></a>查詢的生命週期

以下是每個查詢所經歷程序的高階概觀。

1. LINQ 查詢會由 Entity Framework Core 所處理，以建置已準備好由資料庫提供者所處理的表示方式
   1. 結果會被快取，因此並不需要在每次執行查詢時進行此處理程序
2. 結果會傳遞給資料庫提供者
   1. 資料庫提供者會識別查詢的哪些組件可在資料庫中評估
   2. 查詢的這些組件會轉譯為資料庫特定的查詢語言 (例如，針對關聯式資料庫會轉譯為 SQL)
   3. 查詢傳送到資料庫並傳回結果集(結果是來自資料庫的值,而不是實體實例)
3. 針對結果集中的每個項目
   1. 如果這是追蹤查詢，EF 就會檢查資料是否代表已存在於內容執行個體變更追蹤器中的實體
      * 如果是，就會傳回現有的實體
      * 如果不是，則會建立新的實體，設定變更追蹤，並傳回新的實體
   2. 如果這是無跟蹤查詢,則始終創建並返回新實體

## <a name="when-queries-are-executed"></a>查詢執行時

當您呼叫 LINQ 運算子時，只是單純在建置查詢的記憶體內表示方式。 只有在取用結果時，才會將查詢傳送到資料庫。

以下是導致將查詢傳送到資料庫的最常見作業：

* 在 `for` 迴圈中逐一查看結果
* 使用運算子(`ToList`如、、`Single``Count``ToArray`或等效的非同步重載)

> [!WARNING]  
> **一律驗證使用者輸入：** 雖然 EF Core 使用參數以及在查詢中逸出常值來防止 SQL 插入式攻擊，但是它不會驗證輸入。 根據應用程式的要求,應在 LINQ 查詢中使用來自不受信任的源的值、分配給實體屬性或傳遞給其他 EF Core API 之前執行適當的驗證。 這包括用來以動態方式建構查詢的任何使用者輸入。 即使在使用 LINQ 時，如果您接受使用者輸入來建置運算式，就必須確定只會建構預期的運算式。
