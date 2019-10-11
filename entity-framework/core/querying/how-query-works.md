---
title: 查詢的運作方式 - EF Core
author: rowanmiller
ms.date: 09/26/2018
ms.assetid: de2e34cd-659b-4cab-b5ed-7a979c6bf120
uid: core/querying/how-query-works
ms.openlocfilehash: bc085755f39b1288f092a8b2df892c1bf82a89f1
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72186264"
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
   3. 系統會將一或多個查詢傳送到資料庫，並會傳回結果集 (結果是來自資料庫的值，而非實體執行個體)
3. 針對結果集中的每個項目
   1. 如果這是追蹤查詢，EF 就會檢查資料是否代表已存在於內容執行個體變更追蹤器中的實體
      * 如果是，就會傳回現有的實體
      * 如果不是，則會建立新的實體，設定變更追蹤，並傳回新的實體
   2. 如果這是非追蹤查詢，EF 就會檢查資料是否代表已存在於此查詢結果集中的實體
      * 如果是，就會傳回現有的實體 <sup>(1)</sup>
      * 如果不是，則會建立並傳回新的實體

<sup>(1)</sup> 非追蹤查詢會使用弱式參考來持續追蹤已經傳回的實體。 如果先前具有相同身分識別的結果超出範圍，且執行記憶體回收，您可能會得到新的實體執行個體。

## <a name="when-queries-are-executed"></a>查詢執行時

當您呼叫 LINQ 運算子時，只是單純在建置查詢的記憶體內表示方式。 只有在取用結果時，才會將查詢傳送到資料庫。

以下是導致將查詢傳送到資料庫的最常見作業：
* 在 `for` 迴圈中逐一查看結果
* 使用 `ToList`、`ToArray`、`Single`、`Count` 之類的運算子
* 將查詢的結果資料繫結到 UI

> [!WARNING]  
> **一律驗證使用者輸入：** 雖然 EF Core 使用參數來防止 SQL 插入式攻擊，並在查詢中將常值進行轉義，但它並不會驗證輸入。 在將不受信任來源的值用於 LINQ 查詢中、指派給實體屬性或傳遞至其他 EF Core API 之前，應該執行針對每個應用程式需求的適當驗證。 這包括用來以動態方式建構查詢的任何使用者輸入。 即使在使用 LINQ 時，如果您接受使用者輸入來建置運算式，就必須確定只會建構預期的運算式。
