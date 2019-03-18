---
title: Entity Framework 詞彙 EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 3f05ffdd-49bc-499c-9732-4a368bf5d2d7
ms.openlocfilehash: 4e42e5870879524b814cecdc361e688d36f0180f
ms.sourcegitcommit: 6c4e06bc62d98442530e93a44725e38e59483d42
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58131392"
---
# <a name="entity-framework-glossary"></a>Entity Framework 詞彙
## <a name="code-first"></a>Code First
建立 Entity Framework 模型使用的程式碼。 模型可以將現有的資料庫或新的資料庫為目標。

## <a name="context"></a>內容
類別，表示與資料庫，可讓您查詢和儲存資料的工作階段。 內容是衍生自 DbContext 或 ObjectContext 的類別。

## <a name="convention-code-first"></a>慣例 (Code First)
Entity Framework 使用推斷的模型從您的類別圖形的規則。

## <a name="database-first"></a>第一次資料庫
建立使用 EF 設計工具中，Entity Framework 模型為目標的現有資料庫。

## <a name="eager-loading"></a>積極式載入
載入模式，其中某一類型實體的查詢也會載入相關的實體做為查詢一部分的相關的資料。

## <a name="ef-designer"></a>EF Designer
在 Visual Studio 中，可讓您建立使用方塊和線的 Entity Framework 模型的視覺化設計工具。

## <a name="entity"></a>實體
代表像是客戶、產品和訂單這類應用程式資料的類別或物件。

## <a name="entity-data-model"></a>實體資料模型
描述實體與其間的關聯性模型。 EF 使用 EDM 來描述概念模型，針對開發人員計劃。 EDM 建置在 Dr 所導入的實體關聯性模型之上。Peter Chen。 原本是 EDM 所開發的一系列來自 Microsoft 的開發人員和伺服器技術，成為 common data model 的主要目標。 EDM 也會做為 OData 通訊協定的一部分。

## <a name="explicit-loading"></a>明確式載入
載入模式，藉由呼叫 API 載入相關的物件的相關的資料。

## <a name="fluent-api"></a>Fluent API
API 可用來設定 Code First 模型。

## <a name="foreign-key-association"></a>外部索引鍵的關聯
代表外部索引鍵的屬性，其中包含相依的實體類別中的實體之間的關聯。 例如，產品包含 CategoryId 屬性。

## <a name="identifying-relationship"></a>識別關聯性
一種關聯性，其中主要實體的主索引鍵也是相依實體之主索引鍵的一部分。 在這種關聯性中，如果沒有主要實體，相依實體就無法存在。

## <a name="independent-association"></a>獨立關聯
實體之間的關聯沒有任何表示外部索引鍵為相依實體類別中的屬性。 例如，Product 類別包含類別，但沒有 CategoryId 屬性關聯性。 Entity Framework 會追蹤與兩個關聯兩端之實體的狀態分開關聯的狀態。

## <a name="lazy-loading"></a>消極式載入
載入模式，其中相關的物件時，自動載入在存取導覽屬性的相關的資料。

## <a name="model-first"></a>第一次建立模型
建立 Entity Framework 模型，使用 EF 設計工具中，然後用來建立新的資料庫。

## <a name="navigation-property"></a>導覽屬性
參考另一個實體的實體屬性。 例如，產品包含的類別目錄 」 瀏覽屬性，而類別包含的產品導覽屬性。

## <a name="poco"></a>POCO
純舊 CLR 物件的首字母縮寫。 簡單的使用者具有的類別，沒有與任何架構的相依性。 在 EF，不是衍生自 EntityObject、 會實作任何介面或攜帶在 EF 中定義任何屬性的實體類別的內容。 這種分離持續性架構的實體類別也會稱為 「 「 持續 」。  

## <a name="relationship-inverse"></a>關聯性反向
相反的另一端的關聯性，例如，產品。分類和分類。產品。

## <a name="self-tracking-entity"></a>自我追蹤實體
從程式碼產生範本，可協助進行多層式架構開發建置實體。

## <a name="table-per-concrete-type-tpc"></a>資料表每個具象類型 (TPC)
對應繼承階層中的每個非抽象類型對應至個別資料庫中的資料表的其中一種方法。

## <a name="table-per-hierarchy-tph"></a>每個階層的資料表 (TPH)
對應其中在階層中的所有型別會對應到資料庫中相同的資料表繼承的方法。 鑑別子資料行用來識別哪種類型的每個資料列相關聯。

## <a name="table-per-type-tpt"></a>每一類一表 (TPT)
對應的繼承階層中的所有類型的通用屬性會對應至在資料庫中，相同的資料表，但每個類型特有的屬性會對應至個別資料表的方法。

## <a name="type-discovery"></a>類型探索
識別應該是 Entity Framework 模型的一部分的型別程序。
