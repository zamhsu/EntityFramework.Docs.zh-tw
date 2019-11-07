---
title: Entity Framework 詞彙-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 3f05ffdd-49bc-499c-9732-4a368bf5d2d7
uid: ef6/resources/glossary
ms.openlocfilehash: df0da4a68b3d2c882d9673417ee5fe335eccae2b
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656150"
---
# <a name="entity-framework-glossary"></a>Entity Framework 詞彙
## <a name="code-first"></a>Code First
使用程式碼建立 Entity Framework 模型。 模型可以目標為現有的資料庫或新的資料庫。

## <a name="context"></a>內容
代表與資料庫之會話的類別，可讓您查詢和儲存資料。 內容衍生自 DbCoNtext 或 ObjectCoNtext 類別。

## <a name="convention-code-first"></a>慣例（Code First）
一種規則，Entity Framework 用來從您的類別中推斷您模型的形狀。

## <a name="database-first"></a>Database First
使用 EF 設計工具建立以現有資料庫為目標的 Entity Framework 模型。

## <a name="eager-loading"></a>積極式載入
載入相關資料的模式，其中一種實體類型的查詢也會在查詢過程中載入相關的實體。

## <a name="ef-designer"></a>EF Designer
Visual Studio 中的視覺化設計工具，可讓您使用方塊和線條建立 Entity Framework 模型。

## <a name="entity"></a>實體
代表像是客戶、產品和訂單這類應用程式資料的類別或物件。

## <a name="entity-data-model"></a>實體資料模型
描述實體的模型，以及它們之間的關聯性。 EF 使用 EDM 來描述開發人員程式所針對的概念模型。 EDM 是以 Chen 所引進的實體關聯性模型為基礎。 EDM 原本的開發主要目標，是要成為 Microsoft 開發人員和伺服器技術的一套通用資料模型。 EDM 也會當做 OData 通訊協定的一部分來使用。

## <a name="explicit-loading"></a>明確式載入
載入相關資料的模式，其中會呼叫 API 來載入相關的物件。

## <a name="fluent-api"></a>Fluent API
可以用來設定 Code First 模型的 API。

## <a name="foreign-key-association"></a>外鍵關聯
實體之間的關聯，其中代表外鍵的屬性會包含在相依實體的類別中。 例如，Product 包含 [類別] 屬性。

## <a name="identifying-relationship"></a>識別關聯性
一種關聯性，其中主要實體的主索引鍵也是相依實體之主索引鍵的一部分。 在這種關聯性中，如果沒有主要實體，相依實體就無法存在。

## <a name="independent-association"></a>獨立關聯
實體之間的關聯，其中沒有屬性代表相依實體之類別中的外鍵。 例如，Product 類別包含與 Category 的關聯性，但沒有任何類別目錄屬性。 Entity Framework 會在兩個關聯端的實體狀態以外，追蹤關聯的狀態。

## <a name="lazy-loading"></a>消極式載入
當存取導覽屬性時，會自動載入相關物件的載入相關資料的模式。

## <a name="model-first"></a>Model First
使用 EF 設計工具建立 Entity Framework 模型，然後用它來建立新的資料庫。

## <a name="navigation-property"></a>導覽屬性
參考另一個實體之實體的屬性。 例如，Product 包含 Category 導覽屬性，而 Category 包含 Products 導覽屬性。

## <a name="poco"></a>POCO
一般舊 CLR 物件的縮寫。 沒有任何架構相依性的簡單使用者類別。 在 EF 的內容中，不是從 EntityObject 衍生的實體類別會執行任何介面，或攜帶 EF 中定義的任何屬性。 與持續性架構分離的這類實體類別也稱為「持續性未知」。  

## <a name="relationship-inverse"></a>反向關聯性
關聯性的相反結束，例如 product。類別和類別。基礎.

## <a name="self-tracking-entity"></a>自我追蹤實體
由程式碼產生範本所建立的實體，可協助進行多層式開發。

## <a name="table-per-concrete-type-tpc"></a>每個具體的資料表類型（TPC）
對應繼承的方法，其中階層中的每個非抽象類別型都會對應到資料庫中的個別資料表。

## <a name="table-per-hierarchy-tph"></a>每個階層的資料表（TPH）
對應繼承的方法，其中階層中的所有類型都會對應到資料庫中的相同資料表。 鑒別子資料行是用來識別與每個資料列相關聯的類型。

## <a name="table-per-type-tpt"></a>每一類型的資料表（TPT）
對應繼承的方法，其中階層中所有類型的通用屬性會對應至資料庫中的相同資料表，但每個類型特有的屬性會對應至個別的資料表。

## <a name="type-discovery"></a>類型探索
識別應該是 Entity Framework 模型之一部分之類型的程式。
