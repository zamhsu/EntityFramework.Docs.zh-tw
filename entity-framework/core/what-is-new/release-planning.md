---
title: EF Core 發行規劃
description: 如何完成 Entity Framework Core 規劃和發行的資訊
author: ajcvickers
ms.date: 01/28/2020
uid: core/what-is-new/release-planning
ms.openlocfilehash: 91a130b4ddae66f626bcff8e70283a7e03192542
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618565"
---
# <a name="release-planning-process"></a>發行計劃程序

在我們收到的問題中，經常有人問起我們如何決定將某功能加入特定的版本。
本檔概述我們所使用的程式。
當我們找到更好的計畫方式，但一般構想保持不變時，此程式會持續演進。

## <a name="different-kinds-of-releases"></a>不同種類的版本

不同種類的版本包含不同類型的變更。
這表示不同版本的發行計畫會有所不同。

### <a name="patch-releases"></a>修補程式版本

修補程式版本只會變更版本的「修補程式」部分。
例如，EF Core 3.1。**1** 是在 EF Core 3.1 中找到修補程式問題的版本。**0**。

修補程式版本旨在修正重要的客戶錯誤。
這表示修補程式版本不包含新功能。
除了在特殊情況下，修補程式版本中不允許 API 變更。

在修補程式版本中進行變更的橫條很高。
這是因為修補程式版本不會引進新的 bug。
因此，決策流程強調高價值和低風險。

如果發生下列情況，比較可能會修補問題：
  * 它會影響多個客戶
  * 這是先前版本的回歸
  * 失敗會造成資料損毀

如果有下列情況，則不太可能修補問題：
  * 有合理的解決方法
  * 修正程式有高風險中斷其他
  * Bug 位於角落案例中

此橫條會逐漸增加長期支援的存留期 [ (LTS) ](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) 版本。 這是因為 LTS 版本強調穩定性。

關於修補問題的最後一項決定是由 Microsoft 的 .NET 主管所提出。

### <a name="minor-releases"></a>次要版本

次要版本只會變更版本的「次要」部分。
例如，EF Core 3。**1**.0 是 EF Core 3 改善的版本。**0**. 0。

次要版本：
* 旨在改善舊版的品質與功能
* 通常包含 bug 修正和新功能
* 請勿包含蓄意的重大變更
* 將幾個發行前版本預覽推送至 NuGet

### <a name="major-releases"></a>主要版本

主要版本會變更 EF 「主要」版本號碼。
例如，EF Core **3**. 0.0 是一種主要版本，可在 EF Core 2.2. x 上前進一大步。

主要版本：
* 旨在改善舊版的品質與功能
* 通常包含 bug 修正和新功能
  * 某些新功能可能是 EF Core 運作方式的基本變更
* 通常包含蓄意的重大變更
  * 重大變更是不斷演進 EF Core 的一部分，因為我們學習
  * 不過，由於潛在的客戶影響，我們很謹慎地考慮進行任何中斷性變更。 我們可能過度積極變更了過去的重大變更。 接下來，我們將盡力減少中斷應用程式的變更，並減少中斷資料庫提供者和延伸模組的變更。
* 將許多發行前版本預覽推送至 NuGet

## <a name="planning-for-majorminor-releases"></a>規劃主要/次要版本

### <a name="github-issue-tracking"></a>GitHub 問題追蹤

GitHub ([https://github.com/dotnet/efcore](https://github.com/dotnet/efcore)) 是所有 EF Core 規劃的真實來源。

GitHub 上的問題有：

* 狀態
  * 尚未解決[開啟](https://github.com/dotnet/efcore/issues)的問題。
  * 已[解決已解決的問題。](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aclosed)
    * 所有已修正的問題都會 [標記為已關閉修正](https://github.com/dotnet/efcore/issues?q=is%3Aissue+label%3Aclosed-fixed+is%3Aclosed)。 已修正併合並標記為已關閉的問題，但可能尚未發行。
    * 其他 `closed-` 標籤表示關閉問題的其他原因。 例如，重複專案會標記為已關閉重複。
* 類型
  * [Bug](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Atype-bug) 表示 bug。
  * [增強](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Atype-enhancement) 功能代表現有功能中的新功能或更好的功能。
* 里程碑
  * 小組會考慮[沒有里程碑的問題](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+no%3Amilestone)。 關於此問題的決策尚未進行，或是考慮到決策的變更。
  * [待處理專案里程碑中的問題](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog) 是 EF 小組在未來的版本中會考慮使用的專案
    * 待處理專案（backlog）中的問題可能會 [標記為下一版](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Aconsider-for-next-release) ，表示此工作專案在下一版的清單中會很高。
  * 在已建立版本的里程碑中開啟問題是小組計畫在該版本中使用的專案。 例如， [這些是我們打算針對 EF Core 5.0 進行處理的問題](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A5.0.0)。
  * 已建立版本之里程碑中的已關閉問題是該版本已完成的問題。 請注意，版本可能尚未發行。 例如， [這些是 EF Core 3.0 完成的問題](https://github.com/dotnet/efcore/issues?q=is%3Aissue+milestone%3A3.0.0+is%3Aclosed)。
* 票！
  * 投票是指出問題對您很重要的最佳方式。
  * 若要投票，請只在問題中新增「拇指向上」 👍 。 例如， [這些是最常投票的問題](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc)
  * 如果您覺得這會增加價值，也請批註描述您需要此功能的特定原因。 批註 "+ 1" 或類似的不會增加值。

### <a name="the-planning-process"></a>規劃程序

規劃程式比起從待處理專案取得最上層要求的功能更多。
這是因為我們會以多種方式收集來自多個專案關係人的意見反應。
接著，我們會根據下列內容來塑造發行：

* 客戶的輸入
* 來自其他專案關係人的輸入
* 策略性方向
* 可用的資源
* 排程

我們提出的一些問題如下：

1. **我們認為多少開發人員會使用此功能，而它讓他們的應用程式或體驗改善多少？** 為了回答這個問題，我們從許多來源收集意見反應 — 問題的留言與投票是那些來源之一。 與重要客戶相關的特定合作是另一種。

2. **如果我們尚未執行這項功能，有哪些因應措施可供使用？** 舉例來說，許多開發人員在缺少原生多對多支援時，都會想到運用聯合資料表作為因應措施。 很明顯地，並非所有開發人員都想這麼做，但其中許多人都做得到，而這也是我們決策中所考量的因素。

3. **實作此功能是否會涉及 EF Core 的架構，使我們更加需要實作其他功能？** 我們偏好可作為其他功能建置組塊的功能。 比方說，屬性包實體可以協助我們邁向多對多的支援，而實體建構函式則促成了消極式載入支援。

4. **這項功能是擴充點嗎？** 因為擴充點使開發人員能夠輕鬆地依自己的習慣使用，並填補任何功能上的缺失，所以我們偏好擴充點勝過於一般功能。

5. **此功能與其他產品搭配使用時的綜效如何？** 我們偏好能促成或大幅改善 EF Core 搭配其他產品使用體驗的功能，例如 .NET Core、最新版本的 Visual Studio、Microsoft Azure 等等。

6. **可以用來處理某項功能的人員技能有哪些，以及如何充分利用這些資源？** 每位 EF 小組的成員及我們社群的參與者，在不同領域都各有不同程度的經驗，所以我們必須據此進行規劃。 即使我們希望將「所有人力」集中在像是 GroupBy 翻譯，或多對多關聯性等特定功能上，也無法實際付諸行動。
