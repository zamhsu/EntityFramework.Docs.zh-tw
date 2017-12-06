---
title: "如何查詢工作的 EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: de2e34cd-659b-4cab-b5ed-7a979c6bf120
ms.technology: entity-framework-core
uid: core/querying/overview
ms.openlocfilehash: 7fd2940d559f82016d7a8fc3fdcf3af0d5b8bc8f
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="how-queries-work"></a>查詢的運作方式

Entity Framework Core 會使用從資料庫查詢資料的語言整合 Query (LINQ)。 LINQ 可讓您使用 C# （或您所選擇的.NET 語言） 撰寫強型別衍生內容和實體類別為基礎的查詢。

## <a name="the-life-of-a-query"></a>生命週期的查詢

以下是每個查詢所經歷的程序的高層級概觀。

1. 建立表示準備好要處理的資料庫提供者的 Entity Framework Core 處理 LINQ 查詢
   1. 結果會快取，因此不需要每次執行查詢來完成這項處理
2. 結果會傳遞至資料庫提供者
   1. 資料庫提供者會識別查詢的哪些部分可以評估在資料庫中
   2. 這些組件的查詢會轉譯為資料庫特定的查詢語言 (例如關聯式資料庫的 SQL)
   3. 一個或多個查詢會傳送至資料庫而傳回的結果集 （結果會是從資料庫中的實體執行個體的值）
3. 針對結果集中的每個項目
   1. 如果這是追蹤查詢，會檢查 EF 如果其資料代表的實體已在變更追蹤程式的內容執行個體
      * 如果是的話，會傳回現有的實體
      * 如果不是，建立新的實體，變更追蹤，則安裝程式，並傳回新的實體
   2. 如果這不是追蹤查詢，會檢查 EF 如果其資料代表的實體已在設定這個查詢的結果
      * 如果現有的實體，則會傳回<sup>(1)</sup>
      * 如果不是，建立並傳回新的實體

<sup>(1)</sup>沒有追蹤的查詢使用弱式參考來追蹤已傳回的實體。 如果具有相同識別是之前的結果超出範圍，而且執行記憶體回收，可能會收到新的實體執行個體。

## <a name="when-queries-are-executed"></a>執行查詢時

當您呼叫 LINQ 運算子時，而只累積在記憶體中表示的查詢。 當結果被耗用時，查詢才會傳送到資料庫。

造成查詢傳送給資料庫的最常見作業為：
* 逐一查看結果`for`迴圈
* 使用運算子，例如`ToList`， `ToArray`， `Single`，`Count`
* 資料繫結至 UI 查詢的結果

> [!WARNING]  
> **一律驗證使用者輸入：**時的 EF 提供從 SQL 資料隱碼攻擊的防護，不會執行輸入的任何一般驗證。 因此如果值傳遞至應用程式開發介面，在 LINQ 查詢中，指派給等實體屬性，使用來自不受信任的來源則適當的驗證，每個應用程式需求，應該執行。 這包括用來以動態方式建構查詢的任何使用者輸入。 即使使用 LINQ，如果您接受使用者輸入建立運算式，您必須確定比預期運算式只可用於建構。
