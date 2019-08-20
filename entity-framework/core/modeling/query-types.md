---
title: 查詢類型-EF Core
author: anpete
ms.date: 02/26/2018
ms.assetid: 9F4450C5-1A3F-4BB6-AC19-9FAC64292AAD
uid: core/modeling/query-types
ms.openlocfilehash: c023d442b0fa2728bd20694a55ebb3a7b5c0efd1
ms.sourcegitcommit: 87e72899d17602f7526d6ccd22f3c8ee844145df
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2019
ms.locfileid: "69628417"
---
# <a name="query-types"></a>查詢類型
> [!NOTE]
> 這項功能的新 EF Core 2.1

EF Core 模型可以包含實體類型，除了_查詢類型_，可用來執行資料庫查詢不會對應到實體類型的資料。

## <a name="compare-query-types-to-entity-types"></a>比較查詢類型，與實體類型

查詢類型如下所示的實體類型中，它們：

- 可以是加入至模型中`OnModelCreating`或透過 「 設定 」 上的屬性衍生_DbContext_。
- 支援許多相同的對應功能，例如繼承對應和導覽屬性。 在關聯式存放區，他們可以設定的目標資料庫物件和透過 fluent API 方法或資料註解的資料行。

不過，它們是不同於實體中的型別，它們：

- 不需要定義的索引鍵。
- 會永遠不會追蹤變更_DbContext_ ，因此會永遠不會插入、 更新或刪除資料庫。
- 永遠不會探索到的慣例。
- 只有特別支援瀏覽對應功能的子集：
  - 它們可能永遠不會做為關聯性的主要末端。
  - 它們只可以包含指向實體的參考導覽屬性。
  - 實體不能包含查詢類型的導覽屬性。
- 都能解決_ModelBuilder_使用`Query`方法而非`Entity`方法。
- 在對應_DbContext_透過屬性的型別`DbQuery<T>`而非 `DbSet<T>`
- 會對應至使用的資料庫物件`ToView`方法，而非`ToTable`。
- 可對應到_定義查詢_-定義查詢是次要的查詢，可做為查詢類型的資料來源模型中宣告。

## <a name="usage-scenarios"></a>使用案例

查詢類型的主要使用案例包括：

- 臨機操作做為傳回型別`FromSql()`查詢。
- 將對應至資料庫檢視。
- 沒有定義主索引鍵的資料表的對應。
- 對應至模型中定義的查詢。

## <a name="mapping-to-database-objects"></a>對應至資料庫物件

對應至資料庫物件的查詢類型用來達成`ToView`fluent API。 EF Core 的觀點而言，此方法中指定的資料庫物件是_檢視_，這表示它會被視為唯讀查詢來源而且無法更新目標的、 插入或刪除作業。 不過，這不表示資料庫物件實際上需要為資料庫檢視，-它也可以將視為唯讀的資料庫資料表。 相反地，實體類型 EF Core 假設中指定的資料庫物件`ToTable`方法可用來當做_資料表_，這表示它可以當做查詢來源，但是也鎖定更新刪除和插入作業。 事實上，您可以在其中指定的名稱中的資料庫檢視`ToTable`和所有項目應可正常運作，只要檢視已成為可更新資料庫。

## <a name="example"></a>範例

下列範例示範如何使用查詢型別來查詢資料庫檢視。

> [!TIP]
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/QueryTypes) \(英文\)。

首先，我們會定義一個簡單的部落格和後置模型：

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#Entities)]

接下來，我們會定義簡單的資料庫檢視，讓我們查詢每個部落格相關聯的貼文數目：

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#View)]

接下來，我們會定義類別以包裝資料庫檢視的結果：

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#QueryType)]

接下來，我們設定中的查詢類型_OnModelCreating_使用`modelBuilder.Query<T>`API。
我們可以使用標準的 fluent 組態 Api 來設定查詢類型的對應：

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#Configuration)]

接下來, 我們會`DbContext`設定, 以`DbQuery<T>`包含:

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#DbQuery)]

最後，我們可以查詢資料庫檢視，以標準方式：

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#Query)]

> [!TIP]
> 請注意，我們也已定義的內容層級查詢屬性 (DbQuery) 做為查詢對這種類型的根。
