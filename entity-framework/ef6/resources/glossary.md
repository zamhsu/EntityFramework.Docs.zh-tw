---
title: Entity Framework 詞彙-EF6
description: Entity Framework 6 詞彙
author: divega
ms.date: 10/23/2016
ms.assetid: 3f05ffdd-49bc-499c-9732-4a368bf5d2d7
uid: ef6/resources/glossary
ms.openlocfilehash: 19d5e9e3a480337c2bcb93be5f989cc622b67dad
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620204"
---
# <a name="entity-framework-glossary"></a>Entity Framework 詞彙
## <a name="code-first"></a>Code First
使用程式碼建立 Entity Framework 模型。 模型可以將目標設為現有的資料庫或新的資料庫。

## <a name="context"></a>內容
代表資料庫之會話的類別，可讓您查詢和儲存資料。 內容衍生自 DbCoNtext 或 ObjectCoNtext 類別。

## <a name="convention-code-first"></a>慣例 (Code First) 
Entity Framework 用來從您的類別推斷模型形狀的規則。

## <a name="database-first"></a>Database First
使用以現有資料庫為目標的 EF 設計工具建立 Entity Framework 模型。

## <a name="eager-loading"></a>積極式載入
載入相關資料的模式，其中一種實體類型的查詢也會將相關實體載入查詢的一部分。

## <a name="ef-designer"></a>EF Designer
Visual Studio 中的視覺化設計工具，可讓您使用方塊和線條建立 Entity Framework 模型。

## <a name="entity"></a>實體
代表像是客戶、產品和訂單這類應用程式資料的類別或物件。

## <a name="entity-data-model"></a>實體資料模型
描述實體的模型以及它們之間的關聯性。 EF 使用 EDM 來描述開發人員程式的概念模型。 由 Dr. Peter Chen 所引進之實體關聯性模型的 EDM 組建。 EDM 最初開發的主要目標是要成為 Microsoft 開發人員和伺服器技術套件之間的通用資料模型。 EDM 也可做為 OData 通訊協定的一部分。

## <a name="explicit-loading"></a>明確式載入
載入相關物件的模式，其會藉由呼叫 API 來載入相關的資料。

## <a name="fluent-api"></a>Fluent API
可以用來設定 Code First 模型的 API。

## <a name="foreign-key-association"></a>外鍵關聯
實體之間的關聯，其中表示外鍵的屬性會包含在相依實體的類別中。 例如，Product 包含 [類別類別] 屬性。

## <a name="identifying-relationship"></a>識別關聯性
主要實體的主索引鍵為相依實體之主索引鍵一部分的關聯性。 在這種關聯性中，相依實體一定要與主要實體一起存在。

## <a name="independent-association"></a>獨立關聯
實體之間的關聯，其中沒有任何屬性代表相依實體之類別中的外鍵。 例如，Product 類別包含類別的關聯性，但沒有任何類別目錄屬性。 Entity Framework 會追蹤關聯的狀態，而不是在兩個關聯端的實體狀態之外。

## <a name="lazy-loading"></a>消極式載入
當存取導覽屬性時，會自動載入相關物件的載入相關資料模式。

## <a name="model-first"></a>Model First
使用 EF 設計工具建立 Entity Framework 模型，然後用它來建立新的資料庫。

## <a name="navigation-property"></a>導覽屬性
參考另一個實體之實體的屬性。 例如，Product 包含類別目錄導覽屬性，而類別則包含 Products 導覽屬性。

## <a name="poco"></a>POCO
純舊 CLR 物件的縮寫。 與任何架構沒有相依性的簡單使用者類別。 在 EF 的內容中，不是衍生自 EntityObject 的實體類別會執行任何介面，或攜帶 EF 中定義的任何屬性。 從持續性架構分離的這類實體類別也稱為「持續性未知」。  

## <a name="relationship-inverse"></a>反向關聯性
關聯性的另一端，例如 product。類別目錄和類別目錄。產品。

## <a name="self-tracking-entity"></a>自我追蹤實體
從程式碼產生範本建立的實體，可協助進行多層式開發。

## <a name="table-per-concrete-type-tpc"></a>每個具象型別的資料表 (TPC) 
對應繼承的方法，其中階層中的每個非抽象類別型都會對應到資料庫中的不同資料表。

## <a name="table-per-hierarchy-tph"></a>每個階層的資料表 (TPH) 
對應繼承的方法，其中階層中的所有類型都會對應到資料庫中的相同資料表。 鑒別子資料行 (s) 用來識別每個資料列相關聯的類型。

## <a name="table-per-type-tpt"></a>每一類型的資料表 (TPT) 
對應繼承的方法，其中階層中所有類型的通用屬性都會對應到資料庫中的相同資料表，但每個類型唯一的屬性會對應至不同的資料表。

## <a name="type-discovery"></a>類型探索
識別應該是 Entity Framework 模型一部分之類型的程式。
