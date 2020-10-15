---
title: EF Core 測試範例-EF Core
description: 範例顯示如何測試使用 Entity Framework Core 的應用程式
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
ms.openlocfilehash: 07cde296f07a883ba6abf45f94a31e072fb6d6cb
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063045"
---
# <a name="ef-core-testing-sample"></a>EF Core 測試範例

> [!TIP]
> 您可以在 GitHub 上找到這份檔中的程式碼，做為可執行檔 [範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/)。
> 請注意，其中有些測試 **預期會失敗**。 以下將說明這種情況的原因。

本檔會逐步解說使用 EF Core 之測試程式碼的範例。

## <a name="the-application"></a>應用程式

此 [範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) 包含兩個專案：

- ItemsWebApi：透過單一控制器[ASP.NET Core 支援的簡易 WEB API](/aspnet/core/tutorials/first-web-api)
- 測試：用來測試控制器的 [XUnit](https://xunit.net/) 測試專案

### <a name="the-model-and-business-rules"></a>模型和商務規則

支援此 API 的模型有兩個實體類型： Items 和 Tags 。

- Items 有區分大小寫的名稱和的集合 Tags 。
- 每個 Tag 都有一個標籤和一個計數，代表它已套用至的次數 Item 。
- 每個都 Item 只能有一個 Tag 具有指定標籤的。
  - 如果專案的標記具有相同的標籤一次以上，則具有該標籤之現有標記的計數會遞增，而不是建立新標記。
- 刪除 Item 會刪除所有相關聯的 Tags 。

#### <a name="the-no-locitem-entity-type"></a>Item實體類型

`Item`實體類型：

[!code-csharp[ItemEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Item.cs?name=ItemEntityType)]

以及其在中的設定 `DbContext.OnModelCreating` ：

[!code-csharp[ConfigureItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureItem)]

請注意，實體類型會限制可用來反映領域模型和商務規則的方式。 尤其是：

- 主要金鑰會直接對應到 `_id` 欄位，而不會公開
  - EF 會偵測並使用私用的函式，以接受主要金鑰值和名稱。
- `Name`屬性是唯讀的，而且只會在函式中設定。
- Tags 會公開為 `IReadOnlyList<Tag>` ，以防止任意修改。
  - EF 藉由比對 `Tags` 屬性與支援欄位的名稱，來建立其關聯 `_tags` 。
  - `AddTag`方法會採用標記標籤，並執行上述的商務規則。
    也就是說，只會為新標籤新增標記。
    否則，現有標籤上的計數會遞增。
- 已 `Tags` 針對多對一關聯性設定導覽屬性
  - 不需要從到的導覽屬性 Tag Item ，因此不包含它。
  - 此外，也不 Tag 會定義外鍵屬性。
    相反地，EF 會建立和管理陰影狀態的屬性。

#### <a name="the-no-loctag-entity-type"></a>Tag實體類型

`Tag`實體類型：

[!code-csharp[TagEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Tag.cs?name=TagEntityType)]

以及其在中的設定 `DbContext.OnModelCreating` ：

[!code-csharp[ConfigureTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureTag)]

同樣地 Item ， Tag 會隱藏其主鍵，並讓 `Label` 屬性成為唯讀。

### <a name="the-no-locitemscontroller"></a>Items控制器

Web API 控制器相當基本。
它會透過函式 `DbContext` 插入，從相依性插入容器取得：

[!code-csharp[Constructor](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Constructor)]

它有方法可取得所有 Items 或 Item 具有指定名稱的：

[!code-csharp[Get](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Get)]

它有一種方法可以新增 Item ：

[!code-csharp[PostItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostItem)]

Item使用標籤標記的方法：

[!code-csharp[PostTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostTag)]

以及刪除 Item 和所有相關聯的方法 Tags ：

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=DeleteItem)]

已移除大部分的驗證和錯誤處理，以減少雜亂。

## <a name="the-tests"></a>測試

測試的組織方式是使用多個資料庫提供者設定來執行：

- SQL Server 提供者，也就是應用程式所使用的提供者
- SQLite 提供者
- 使用記憶體內部 SQLite 資料庫的 SQLite 提供者
- EF 記憶體內部資料庫提供者

做法是將所有測試放在基類中，然後繼承自這個類別，以測試每個提供者。

> [!TIP]
> 如果您不是使用 LocalDB，您將需要變更 SQL Server 連接字串。
> 請參閱使用 [sqlite 進行測試](xref:core/miscellaneous/testing/sqlite) ，以取得使用 sqlite 進行記憶體內部測試的指引。

下列兩項測試預期會失敗：

- `Can_remove_item_and_all_associated_tags` 使用 EF 記憶體內部資料庫提供者執行時
- `Can_add_item_differing_only_by_case` 使用 SQL Server 提供者執行時

以下將詳細說明這一點。

### <a name="setting-up-and-seeding-the-database"></a>設定和植入資料庫

XUnit 和大部分的測試架構一樣，將會為每個測試回合建立新的測試類別實例。
此外，XUnit 也不會平行執行指定測試類別內的測試。
這表示我們可以在測試的函式中設定和設定資料庫，而且每個測試的狀態都是已知狀態。

> [!TIP]
> 此範例會為每個測試重新建立資料庫。
> 這適用于 SQLite 和 EF 記憶體內部資料庫測試，但可能涉及與其他資料庫系統（包括 SQL Server）的大量額外負荷。
> [跨測試共用資料庫](xref:core/miscellaneous/testing/sharing-databases)涵蓋了降低此額外負荷的方法。

執行每個測試時：

- 已針對使用中的提供者設定 DbCoNtextOptions，並傳遞至基類的函式
  - 這些選項會儲存在屬性中，並在整個測試中用來建立 DbCoNtext 實例
- 呼叫種子方法來建立和植入資料庫
  - 植入方法會藉由刪除並重新建立，來確保資料庫是乾淨的
  - 系統會建立一些知名的測試實體，並將其儲存至資料庫

[!code-csharp[Seeding](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=Seeding)]

每個具體的測試類別都會繼承自這個類別。
例如︰

[!code-csharp[SqliteItemsControllerTest](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteItemsControllerTest.cs?name=SqliteItemsControllerTest)]

### <a name="test-structure"></a>測試結構

雖然應用程式使用相依性插入，但測試卻沒有。
您可以在這裡使用相依性插入，但所需的額外程式碼不會有太大的價值。
相反地，會使用建立 DbCoNtext，然後直接以相依性的 `new` 形式傳遞至控制器。

然後，每個測試會在控制器上執行受測試的方法，並判斷提示結果如預期般執行。
例如︰

[!code-csharp[CanGetItems](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanGetItems)]

請注意，會使用不同的 DbCoNtext 實例來植入資料庫，並執行測試。
這可確保測試不會使用 (，或在植入時，不使用) 內容所追蹤的實體。
也更符合 web 應用程式和服務中發生的情況。

基於類似的原因，改變資料庫的測試會在測試中建立第二個 DbCoNtext 實例。
也就是說，您可以建立新的、乾淨的內容，然後從資料庫中讀取資料，以確保變更已儲存至資料庫。
例如︰

[!code-csharp[CanAddItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItem)]

有兩個稍微複雜的測試涵蓋了有關加入的商務邏輯 tags 。

[!code-csharp[CanAddTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddTag)]

[!code-csharp[CanUpTagCount](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanUpTagCount)]

## <a name="issues-using-different-database-providers"></a>使用不同資料庫提供者的問題

使用與生產環境應用程式不同的資料庫系統進行測試可能會導致問題。
這些會在 [使用 EF Core 的測試程式碼](xref:core/miscellaneous/testing/index)的概念層級中討論。
下列各節涵蓋此範例中的測試所示範之這類問題的兩個範例。

### <a name="test-passes-when-the-application-is-broken"></a>應用程式中斷時的測試階段

我們應用程式的其中一項需求是「 Items 有區分大小寫的名稱和集合。」 Tags
這相當簡單，可進行測試：

[!code-csharp[CanAddItemCaseInsensitive](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItemCaseInsensitive)]

針對 EF 記憶體內部資料庫執行這項測試表示一切正常。
使用 SQLite 時，一切仍然沒問題。
但是針對 SQL Server 執行時，測試會失敗！

```output
System.InvalidOperationException : Sequence contains more than one element
   at System.Linq.ThrowHelper.ThrowMoreThanOneElementException()
   at System.Linq.Enumerable.Single[TSource](IEnumerable`1 source)
   at Microsoft.EntityFrameworkCore.Query.Internal.QueryCompiler.Execute[TResult](Expression query)
   at Microsoft.EntityFrameworkCore.Query.Internal.EntityQueryProvider.Execute[TResult](Expression expression)
   at System.Linq.Queryable.Single[TSource](IQueryable`1 source, Expression`1 predicate)
   at Tests.ItemsControllerTest.Can_add_item_differing_only_by_case()
```

這是因為 EF 記憶體內部資料庫和 SQLite 資料庫預設都有區分大小寫。
另一方面，SQL Server 不區分大小寫！

根據設計，EF Core 不會變更這些行為，因為強制變更區分大小寫可能會對效能造成重大影響。

一旦我們知道這是問題，我們就可以修正應用程式，並在測試中進行補償。
不過，這裡的重點是，如果只使用 EF in memory 資料庫或 SQLite 提供者進行測試，就會遺漏這個 bug。

### <a name="test-fails-when-the-application-is-correct"></a>當應用程式正確時測試失敗

我們應用程式的另一項需求是「刪除 Item 所有相關聯的」 Tags 。
同樣地，您也可以輕鬆地進行測試：

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=DeleteItem)]

這項測試會 SQL Server 和 SQLite 傳遞，但無法與 EF 記憶體內部資料庫一起使用！

```output
Assert.False() Failure
Expected: False
Actual:   True
   at Tests.ItemsControllerTest.Can_remove_item_and_all_associated_tags()
```

在此情況下，應用程式會正常運作，因為 SQL Server 支援串聯 [刪除](xref:core/saving/cascade-delete)。
SQLite 也支援串聯刪除，與大部分的關係資料庫相同，因此在 SQLite 上進行測試可正常運作。
另一方面，EF 記憶體內部資料庫 [不支援串聯刪除](https://github.com/dotnet/efcore/issues/3924)。
這表示應用程式的這個部分無法使用 EF 記憶體內部資料庫提供者進行測試。
