---
title: 查詢類型-EF Core
author: anpete
ms.author: anpete
ms.date: 2/26/2018
ms.assetid: 9F4450C5-1A3F-4BB6-AC19-9FAC64292AAD
ms.technology: entity-framework-core
uid: core/modeling/query-types
ms.openlocfilehash: f16e3a130f3a4f92b2bf6014f2df0ca4eec56a25
ms.sourcegitcommit: 038acd91ce2f5a28d76dcd2eab72eeba225e366d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2018
ms.locfileid: "34163170"
---
# <a name="query-types"></a>查詢類型
> [!NOTE]
> 這項功能的新 EF Core 2.1

EF Core 模型可以包含實體類型，除了_查詢類型_，可用來執行資料庫查詢不會對應到實體類型的資料。

查詢類型包含實體類型有許多相似之處：

- 它們也可以加入至模型可能是在`OnModelCreating`，或透過 「 設定 」 上的屬性衍生_DbContext_。
- 它們支援許多相同的對應功能，像是繼承對應時，導覽屬性 （請參閱下面的限制），然後在關聯式存放區，讓您設定的目標資料庫物件和透過 fluent API 方法或資料註解的資料行。

不過它們是不同的實體中的類型，它們：

- 不需要定義的索引鍵。
- 永遠不會追蹤變更_DbContext_ ，因此會永遠不會插入、 更新或刪除資料庫上。
- 永遠不會探索到的慣例。
- 特別是只支援瀏覽對應功能的子集：
  - 它們可能永遠不會做為關聯性的主要端點。
  - 它們只可以包含指向實體參考導覽屬性。
  - 實體不能包含導覽屬性來查詢類型。
- 都能解決_ModelBuilder_使用`Query`方法而非`Entity`方法。
- 對應索引_DbContext_透過屬性的型別`DbQuery<T>`而非 `DbSet<T>`
- 會對應至資料庫物件使用`ToView`方法，而非`ToTable`。
- 會對應至_定義查詢_-定義查詢是做為查詢類型的資料來源在模型中宣告的第二個查詢。

主要的使用方式案例的查詢類型的部分包括：

- 臨機操作做為傳回型別`FromSql()`查詢。
- 將對應至資料庫檢視。
- 對應至沒有定義主索引鍵的資料表。
- 對應至模型中定義的查詢。

> [!TIP]
> 對應至資料庫物件的查詢類型用來達成`ToView`fluent 應用程式開發的應用程式開發介面。 EF Core 的觀點而言，此方法中指定的資料庫物件是_檢視_，這表示它會被視為唯讀查詢來源而且無法更新目標的、 插入或刪除作業。 不過，這不表示資料庫物件的資料庫檢視實際需要-它也可以將被視為唯讀的資料庫資料表。 相反地，實體類型 EF Core 假設中指定的資料庫物件`ToTable`方法可用來當做_資料表_，這表示它可以當做查詢來源，但是也鎖定更新刪除和插入作業。 事實上，您可以指定資料庫檢視中的名稱`ToTable`和所有項目應該會正常運作，只要在檢視器設定為可在資料庫上更新。

## <a name="example"></a>範例

下列範例會示範如何使用查詢型別來查詢資料庫檢視。

> [!TIP]
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFrameworkCore/tree/dev/samples/QueryTypes) \(英文\)。

首先，我們會定義簡單的部落格和後置模型：

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#Entities)]

接下來，我們會定義簡單的資料庫檢視，可讓我們查詢的每個部落格相關聯的文章編號：

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#View)]

接下來，我們會定義以保存從資料庫檢視結果的類別：

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#QueryType)]

接下來，我們設定中的查詢類型_OnModelCreating_使用`modelBuilder.Query<T>`應用程式開發介面。
我們可以使用標準 fluent 應用程式開發設定應用程式開發介面來設定查詢類型的對應：

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#Configuration)]

最後，我們可以查詢資料庫檢視，以標準方式：

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#Query)]

> [!TIP]
> 請注意，我們也已定義的內容層級查詢屬性 (DbQuery) 做為查詢對這種類型的根。
