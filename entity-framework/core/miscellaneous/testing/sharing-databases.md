---
title: 在測試之間共用資料庫-EF Core
description: 顯示如何在多個測試之間共用資料庫的範例
author: ajcvickers
ms.date: 04/25/2020
uid: core/miscellaneous/testing/sharing-databases
ms.openlocfilehash: 96216fb8afd6916402637de3ffab04b79d37e11c
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82564277"
---
# <a name="sharing-databases-between-tests"></a>在測試之間共用資料庫

[EF Core 測試範例示範](xref:core/miscellaneous/testing/testing-sample)如何針對不同的資料庫系統測試應用程式。
針對該範例，每個測試都會建立新的資料庫。
當使用 SQLite 或 EF 記憶體內部資料庫時，這是很好的模式，但是在使用其他資料庫系統時，可能會有相當大的負擔。

這個範例是以先前的範例為基礎，藉由將資料庫建立移至測試裝置。
如此一來，就可以為所有測試建立單一 SQL Server 資料庫，而且只植入一次。

> [!TIP]
> 請務必先完成[EF Core 測試範例](xref:core/miscellaneous/testing/testing-sample)，再繼續這裡。

針對相同的資料庫撰寫多個測試並不容易。
這個技巧是以測試不會在執行的過程中執行的方式來進行。
這需要瞭解：
* 如何安全地在測試之間共用物件
* 當測試架構平行執行測試時
* 如何讓資料庫保持在每個測試的乾淨狀態  

## <a name="the-fixture"></a>裝置

我們將使用測試裝置，在測試之間共用物件。
[XUnit 檔](https://xunit.net/docs/shared-context.html)說明當您想要建立單一測試內容並在類別中的所有測試之間共用時，應該使用的裝置，並在類別中的所有測試完成之後加以清除。

> [!TIP]
> 這個範例使用[XUnit](https://xunit.net/)，但其他測試架構中也有類似的概念，包括[NUnit](https://nunit.org/)。

這表示我們需要將資料庫建立和植入移動到裝置類別。
看起來如下：

[!code-csharp[SharedDatabaseFixture](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseFixture.cs?name=SharedDatabaseFixture)]

現在，請注意如何執行此函式：
* 建立裝置存留期的單一資料庫連線
* 藉由呼叫`Seed`方法來建立和植入該資料庫 

立即忽略鎖定;我們稍後會再回來。

> [!TIP]
> 建立和植入程式碼不需要是非同步。
> 讓它成為非同步會使程式碼變得複雜，而且不會改善效能或測試的輸送量。

建立資料庫的方式是先刪除任何現有的資料庫，然後再建立新的資料庫。
這可確保資料庫符合目前的 EF 模型，即使自上次測試執行之後已經變更。

> [!TIP]
> 使用類似[respawn](https://jimmybogard.com/tag/respawn/)的專案（而不是每次重新建立），可以更快速地「清理」現有的資料庫。
> 不過，執行此動作時，必須特別小心，以確保資料庫架構與 EF 模型是最新的。

處置裝置時，會處置資料庫連接。
您也可以考慮在此時刪除測試資料庫。
不過，如果裝置正在由多個測試類別共用，這將需要額外的鎖定和參考計數。
此外，讓測試資料庫仍然可以用於偵測失敗的測試通常會很有用。  

## <a name="using-the-fixture"></a>使用裝置

XUnit 具有共同的模式，可將測試裝置與測試類別產生關聯：

[!code-csharp[UsingTheFixture](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=UsingTheFixture)]

XUnit 現在會建立單一裝置實例，並將它傳遞給測試類別的每個實例。
（請記得第一個[測試範例](xref:core/miscellaneous/testing/testing-sample)，XUnit 會在每次執行測試時建立新的測試類別實例）。這表示資料庫會建立並植入一次，然後每個測試都會使用這個資料庫。

請注意，單一類別內的測試不會平行執行。
這表示，即使`DbConnection`物件不是安全線程，每個測試都可以使用相同的資料庫連接。

## <a name="maintaining-database-state"></a>維護資料庫狀態

測試通常需要使用插入、更新和刪除來改變測試資料。
但是，這些變更會影響其他需要乾淨、植入之資料庫的測試。

這可以藉由在交易內執行改變測試來處理。
例如：

[!code-csharp[CanAddItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=CanAddItem)]

請注意，當測試完成時，就會建立交易並加以處置。
處置交易會使其回復，因此其他測試將看不到任何變更。

建立內容的 helper 方法（請參閱上述的裝置程式碼）會接受此交易，並選擇 DbCoNtext 使用它。 

## <a name="sharing-the-fixture"></a>共用裝置

您可能已注意到鎖定程式碼是關於建立和植入資料庫。
這個範例不需要這麼做，因為只有一個測試類別會使用裝置，因此只會建立單一裝置實例。

不過，您可能會想要將相同的裝置與多個測試類別搭配使用。
XUnit 會為每個類別建立一個裝置實例。
這些可能會由平行執行測試的不同執行緒使用。
因此，請務必具有適當的鎖定，以確保只有一個執行緒會建立和植入資料庫。

> [!TIP]
> 這裡有`lock`一個簡單的問題。
> 不需要再嘗試任何複雜的作業，例如任何無鎖定模式。
