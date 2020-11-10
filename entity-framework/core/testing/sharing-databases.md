---
title: 在測試之間共用資料庫-EF Core
description: 顯示如何在多個測試之間共用資料庫的範例
author: ajcvickers
ms.date: 04/25/2020
uid: core/testing/sharing-databases
ms.openlocfilehash: 95b756c80b983356a07fd836aa1b02f2835e6629
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431408"
---
# <a name="sharing-databases-between-tests"></a>在測試之間共用資料庫

[EF Core 測試範例示範](xref:core/testing/testing-sample)如何針對不同的資料庫系統測試應用程式。
針對該範例，每個測試都會建立新的資料庫。
在使用 SQLite 或 EF 記憶體內部資料庫時，這是很好的模式，但在使用其他資料庫系統時可能需要大量的負擔。

這個範例是以先前的範例為基礎，將資料庫建立移至測試裝置。
如此一來，就只會針對所有測試建立和植入單一 SQL Server 資料庫一次。

> [!TIP]
> 在這裡繼續進行之前，請務必先完成 [EF Core 測試範例](xref:core/testing/testing-sample) 。

針對相同資料庫撰寫多個測試並不難。
秘訣是以測試不會在執行時往返的方式來執行。
這需要瞭解：

* 如何安全地在測試之間共用物件
* 當測試架構平行執行測試時
* 如何針對每個測試讓資料庫保持乾淨狀態  

## <a name="the-fixture"></a>裝置

我們將使用測試裝置在測試之間共用物件。
[XUnit 檔](https://xunit.net/docs/shared-context.html)說明應該使用的裝置」。當您想要建立單一測試內容，並在類別中的所有測試之間共用時，並在類別中的所有測試完成後將它清除。

> [!TIP]
> 此範例會使用 [XUnit](https://xunit.net/)，但其他測試架構（包括 [NUnit](https://nunit.org/)）中也有類似的概念。

這表示我們需要將資料庫建立和植入移至裝置類別。
其看起來會像下面這樣：

[!code-csharp[SharedDatabaseFixture](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseFixture.cs?name=SharedDatabaseFixture)]

現在，請注意函式如何：

* 在裝置的存留期內建立單一資料庫連接
* 藉由呼叫方法來建立和植入資料庫 `Seed`

立即忽略鎖定;我們稍後會再回來。

> [!TIP]
> 建立和植入程式碼不需要是非同步。
> 使其非同步會使程式碼變得複雜，而不會改善測試的效能或輸送量。

建立資料庫的方式是先刪除任何現有的資料庫，然後再建立新的資料庫。
這樣可確保資料庫符合目前的 EF 模型，即使自上次執行測試之後已經變更。

> [!TIP]
> 您可以使用 [respawn](https://jimmybogard.com/tag/respawn/) 之類的方式，以較快的方式「清理」現有的資料庫，而不是每次重新建立它。
> 但是，在執行這項操作時，必須小心確保資料庫架構是與 EF 模型保持最新的狀態。

處置裝置時，就會處置資料庫連接。
您也可以考慮在此時刪除測試資料庫。
但是，如果多個測試類別正在共用這些裝置，則這將需要額外的鎖定和參考計數。
此外，讓測試資料庫仍可用於偵測失敗的測試通常會很有用。  

## <a name="using-the-fixture"></a>使用裝置

XUnit 具有共同的模式，可將測試裝置與測試類別產生關聯：

[!code-csharp[UsingTheFixture](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=UsingTheFixture)]

XUnit 現在會建立單一裝置實例，並將它傳遞給測試類別的每個實例。
 (記得第一個 [測試範例](xref:core/testing/testing-sample) ，XUnit 會在每次執行測試時建立新的測試類別實例。 ) 這表示資料庫將建立並植入一次，然後每個測試都會使用此資料庫。

請注意，單一類別內的測試不會以平行方式執行。
這表示，每個測試都可以使用相同的資料庫連接，即使 `DbConnection` 物件不是安全線程也是一樣。

## <a name="maintaining-database-state"></a>維護資料庫狀態

測試通常需要使用插入、更新和刪除來改變測試資料。
但是這些變更會影響其他需要乾淨、植入資料庫的測試。

在交易內執行改變測試，即可處理此情況。
例如：

[!code-csharp[CanAddItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=CanAddItem)]

請注意，當測試開始時，即會建立交易，並在完成時處置。
處置交易會導致其回復，因此其他測試將看不到任何變更。

建立內容的 helper 方法 (查看上述的裝置程式碼) 接受此交易並選擇 DbCoNtext 使用它。

## <a name="sharing-the-fixture"></a>共用裝置

您可能已注意到有關建立和植入資料庫的鎖定程式碼。
這個範例並不需要這麼做，因為只有一個測試類別會使用此裝置，因此只會建立單一裝置實例。

不過，您可能會想要在多個測試類別中使用相同的裝置。
XUnit 會為每個類別建立一個裝置實例。
這些可能會由不同的執行緒平行執行測試使用。
因此，請務必具有適當的鎖定，以確保只有一個執行緒會建立和植入資料庫。

> [!TIP]
> 這裡有一個簡單的 `lock` 問題。
> 不需要再嘗試任何更複雜的作業，例如任何無鎖定模式。
