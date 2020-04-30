---
title: EF Core 測試範例-EF Core
description: 範例顯示如何測試使用 EF Core 的應用程式
author: ajcvickers
ms.date: 04/22/2020
uid: core/miscellaneous/testing/testing-sample
no-loc:
- Item
- Tag
- Items
- Tags
- items
- tags
ms.openlocfilehash: dda7191df7646aa06aab51d8d7891bd0ba155674
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82564284"
---
# <a name="ef-core-testing-sample"></a>EF Core 測試範例

> [!TIP]
> 本檔中的程式碼可以在 GitHub 上找到可執行檔[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/)。
> 請注意，其中有些測試**應該會失敗**。 其原因如下所述。 

本檔會逐步解說用來測試使用 EF Core 之程式碼的範例。

## <a name="the-application"></a>應用程式

此[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/)包含兩個專案：
- ItemsWebApi：一個非常簡單的 Web API，以單一控制器[ASP.NET Core 支援](/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio)
- 測試：用來測試控制器的[XUnit](https://xunit.net/)測試專案

### <a name="the-model-and-business-rules"></a>模型和商務規則

支援此 API 的模型有兩個實體類型Items ： Tags和。

* Items具有區分大小寫的名稱和的集合Tags。
* 每Tag個都有一個標籤和一個計數，代表已套用至的Item次數。
* 每Item個都只能有Tag一個具有指定標籤的。
  * 如果專案被標記為相同標籤一次以上，則具有該標籤之現有標記的計數會遞增，而不是建立新的標記。 
* 刪除應該Item會刪除所有相關Tags聯的。

#### <a name="the-item-entity-type"></a>Item實體類型

`Item`實體類型：

[!code-csharp[ItemEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Item.cs?name=ItemEntityType)]

及其在中`DbContext.OnModelCreating`的設定：

[!code-csharp[ConfigureItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureItem)]

請注意，實體類型會限制它可以用來反映領域模型和商務規則的方式。 尤其是：
- 主要金鑰會直接對應到`_id`欄位，而不會公開公開
  - EF 會偵測並使用私用的函式，以接受主要金鑰值和名稱。
- `Name`屬性是唯讀的，而且只會在此函式中設定。 
- Tags會公開為`IReadOnlyList<Tag>` ，以防止任意修改。
  - EF 藉由`Tags`比對屬性`_tags`與支援欄位，藉由比對其名稱來建立關聯。 
  - `AddTag`方法會採用標記標籤，並執行前述的商務規則。
    也就是說，只有新標籤才會加入標記。
    否則，現有標籤上的計數會遞增。
- `Tags`導覽屬性是針對多對一關聯性所設定
  - 不需要從Tag到Item的導覽屬性，因此不會包含。
  - 此外， Tag不會定義外鍵屬性。
    取而代之的是，EF 會建立和管理陰影狀態的屬性。

#### <a name="the-tag-entity-type"></a>Tag實體類型

`Tag`實體類型：

[!code-csharp[TagEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Tag.cs?name=TagEntityType)]

及其在中`DbContext.OnModelCreating`的設定：

[!code-csharp[ConfigureTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureTag)]

類似于Item， Tag會隱藏其主要金鑰，並`Label`讓屬性成為唯讀。

### <a name="the-itemscontroller"></a>ItemsController

Web API 控制器非常基本。
它會透過`DbContext`函式插入，從相依性插入容器取得：

[!code-csharp[Constructor](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Constructor)]

它有方法可以取得Items Item具有指定名稱的所有或：

[!code-csharp[Get](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Get)]

它有一個方法可以加入新Item的：

[!code-csharp[PostItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostItem)]

使用標籤標記的Item方法：

[!code-csharp[PostTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostTag)]

以及刪除Item和所有相關聯Tags的方法：

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=DeleteItem)]

大部分的驗證和錯誤處理都已移除，以減少雜亂的情況。

## <a name="the-tests"></a>測試

這些測試會組織成以多個資料庫提供者設定來執行：
* SQL Server 提供者，這是應用程式所使用的提供者
* SQLite 提供者
* 使用記憶體內部 SQLite 資料庫的 SQLite 提供者
* EF 記憶體內部資料庫提供者

這是藉由將所有測試放在基類中，然後從這個類別繼承，以測試每個提供者。

> [!TIP]
> 如果您不是使用 LocalDB，就必須變更 SQL Server 連接字串。

> [!TIP]
> 如需使用 SQLite 進行記憶體內部測試的指引，請參閱[使用 sqlite 進行測試](xref:core/miscellaneous/testing/sqlite)。 

下列兩項測試預期會失敗：
* `Can_remove_item_and_all_associated_tags`搭配 EF 記憶體內部資料庫提供者執行時
* `Can_add_item_differing_only_by_case`與 SQL Server 提供者一起執行時

下面將詳細說明這一點。

### <a name="setting-up-and-seeding-the-database"></a>設定和植入資料庫

XUnit 就像大部分的測試架構一樣，會為每個測試回合建立新的測試類別實例。
此外，XUnit 也不會平行執行指定測試類別內的測試。 這表示我們可以在測試的函式中設定和設定資料庫，而且它會在每個測試的已知狀態中。

> [!TIP]
> 這個範例會針對每個測試重新建立資料庫。
> 這適用于 SQLite 和 EF 記憶體內部資料庫測試，但可能牽涉到與其他資料庫系統的嚴重負擔，包括 SQL Server。
> [跨測試共用資料庫](xref:core/miscellaneous/testing/sharing-databases)涵蓋了降低此額外負荷的方法。

執行每項測試時：
* 已針對使用中的提供者設定 DbCoNtextOptions，並傳遞至基類的函式
  * 這些選項會儲存在屬性中，並在整個測試中用於建立 DbCoNtext 實例
* 呼叫種子方法來建立和植入資料庫
  * Seed 方法藉由刪除並重新建立資料庫，來確保其已清除
  * 某些知名的測試實體會建立並儲存至資料庫

[!code-csharp[Seeding](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=Seeding)]

然後，每個具體的測試類別都會繼承自這個。
例如：

[!code-csharp[SqliteItemsControllerTest](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteItemsControllerTest.cs?name=SqliteItemsControllerTest)]

### <a name="test-structure"></a>測試結構

即使應用程式使用相依性插入，測試也不會。
在這裡使用相依性插入是可行的，但它所需的額外程式碼卻沒有太大的價值。
相反地，會使用`new`建立 DbCoNtext，然後直接將它當做相依性傳遞至控制器。

然後每個測試都會在控制器上執行受測的方法，並判斷提示結果是否符合預期。
例如：

[!code-csharp[CanGetItems](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanGetItems)]

請注意，會使用不同的 DbCoNtext 實例來植入資料庫，並執行測試。 這可確保在植入時，測試不會使用（或往返）內容所追蹤的實體。
它也更符合 web 應用程式和服務中發生的情況。

變動資料庫的測試會在測試中建立第二個 DbCoNtext 實例，原因類似。
也就是說，建立新的、乾淨的內容，然後從資料庫中讀取，以確保變更真的儲存到資料庫中。 例如：

[!code-csharp[CanAddItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItem)]

另外兩個相關的測試涵蓋了加入標記的商務邏輯。

[!code-csharp[CanAddTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddTag)]

[!code-csharp[CanUpTagCount](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanUpTagCount)]

## <a name="issues-using-different-database-providers"></a>使用不同資料庫提供者的問題

使用與生產應用程式中使用的不同資料庫系統進行測試，可能會導致問題。
這會在[測試使用 EF Core 之程式碼](xref:core/miscellaneous/testing/index)的概念層級中討論。  
下列各節涵蓋此範例中的測試所示範的兩個這類問題的範例。

### <a name="test-passes-when-application-is-broken"></a>應用程式中斷時的測試階段

我們的應用程式有一個需求，就是「Items有區分大小寫的名稱和集合Tags。」
這非常容易測試：

[!code-csharp[CanAddItemCaseInsensitive](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItemCaseInsensitive)]

針對 EF 記憶體內部資料庫執行這項測試，表示一切正常。
使用 SQLite 時，所有專案仍然看起來沒問題。
但是，在針對 SQL Server 執行時，測試會失敗！

```
System.InvalidOperationException : Sequence contains more than one element
   at System.Linq.ThrowHelper.ThrowMoreThanOneElementException()
   at System.Linq.Enumerable.Single[TSource](IEnumerable`1 source)
   at Microsoft.EntityFrameworkCore.Query.Internal.QueryCompiler.Execute[TResult](Expression query)
   at Microsoft.EntityFrameworkCore.Query.Internal.EntityQueryProvider.Execute[TResult](Expression expression)
   at System.Linq.Queryable.Single[TSource](IQueryable`1 source, Expression`1 predicate)
   at Tests.ItemsControllerTest.Can_add_item_differing_only_by_case()
```

這是因為 EF 記憶體內部資料庫和 SQLite 資料庫預設都會區分大小寫。
另一方面，SQL Server 則不區分大小寫！ 

根據設計，EF Core 不會變更這些行為，因為強制變更區分大小寫可能會對效能造成重大影響。

一旦我們知道這是一個問題，我們就可以修正應用程式，並在測試中進行補償。
不過，這裡的重點是，如果只使用 EF 記憶體內部資料庫或 SQLite 提供者進行測試，就可能會遺漏此 bug。

### <a name="test-fails-when-application-is-correct"></a>當應用程式正確時，測試會失敗 

我們的應用程式有另一個需求，就是「刪除Item應該刪除所有相關Tags聯的」。
同樣地，很容易測試：

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=DeleteItem)]

這項測試會在 SQL Server 和 SQLite 上傳遞，但會因 EF 記憶體內部資料庫而失敗！

```
Assert.False() Failure
Expected: False
Actual:   True
   at Tests.ItemsControllerTest.Can_remove_item_and_all_associated_tags()
```

在此情況下，應用程式會正常運作，因為 SQL Server 支援[cascade 刪除](xref:core/saving/cascade-delete)。 SQLite 也支援「串聯刪除」，如同大部分的關係資料庫，因此在 SQLite 上測試此功能會運作。
另一方面，EF 記憶體內部資料庫不[支援串聯刪除](https://github.com/dotnet/efcore/issues/3924)。
這表示應用程式的這個部分無法使用 EF 記憶體內部資料庫提供者進行測試。
