---
title: EF Core 版本規劃
author: ajcvickers
ms.date: 01/28/2020
uid: core/what-is-new/release_planning
ms.openlocfilehash: df933ac2462fcc18c53f49d862836fd2d6a4dd99
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370184"
---
# <a name="release-planning-process"></a>發行計劃程序

在我們收到的問題中，經常有人問起我們如何決定將某功能加入特定的版本。
本檔概述我們所使用的程式。
此程式會持續進化，因為我們找到更好的計畫方式，但一般的想法保持不變。

## <a name="different-kinds-of-releases"></a>不同種類的版本

不同類型的版本包含不同類型的變更。
這也表示不同版本的發行計畫會有所不同。

### <a name="patch-releases"></a>修補程式版本

修補程式版本只會變更版本的「修補」部分。
例如，EF Core 3.1。**1**是在 EF Core 3.1 中找到修補問題的發行版本。**0**。

修補程式版本主要是用來修正重要的客戶錯誤。
這表示修補程式版本不包含新功能。
除了特殊情況以外，修補程式版本中不允許 API 變更。

在修補程式版本中進行變更的橫條非常高。
這是因為修補程式發行不會引進新的 bug。
因此，決策流程強調高價值和低風險。

在下列情況中，我們較可能會修補問題：
  * 它會影響多個客戶
  * 這是來自前一版的回歸
  * 失敗會導致資料損毀

在下列情況中，我們較不可能修補問題：
  * 有合理的因應措施
  * 這項修正有高風險，可能會中斷其他工作
  * Bug 是在角落案例中

此橫條會在[長期支援（LTS）](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)版本的存留期間逐漸上升。 這是因為 LTS 版本強調了穩定性。

關於修補問題的最後一項決定是由 Microsoft 的 .NET 主管所做出。

### <a name="minor-releases"></a>次要版本

次要版本只會變更版本的「次要」部分。
例如，EF Core 3。**1**.0 是改善 EF Core 3 的版本。**0**. 0。

次要版本：
* 旨在改善舊版的品質和功能
* 通常包含 bug 修正和新功能
* 不包含刻意的重大變更
* 有幾個發行前版本預覽已推送至 NuGet

### <a name="major-releases"></a>主要版本

主要版本會變更 EF 「主要」版本號碼。
例如，EF Core **3**. 0.0 是一種主要版本，可在 EF Core 2.2. x 之前進行一大進步。

主要版本：
* 旨在改善舊版的品質和功能
* 通常包含 bug 修正和新功能
  * 部分新功能可能是 EF Core 運作方式的基本變更
* 通常包含刻意的重大變更
  * 中斷性變更是我們所學習到不斷演進 EF Core 的必要部分
  * 不過，我們認為非常謹慎地進行任何重大變更，因為潛在客戶的影響。 過去的重大變更，我們可能過於積極。 接下來，我們將努力盡可能減少中斷應用程式的變更，並減少中斷資料庫提供者和擴充功能的變更。
* 有許多發行前版本預覽已推送至 NuGet

## <a name="planning-for-majorminor-releases"></a>主要/次要版本規劃

### <a name="github-issue-tracking"></a>GitHub 問題追蹤

GitHub （ [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore) ）是所有 EF Core 規劃的真實來源。

GitHub 上的問題有：

* 狀態
  * 尚未解決[開啟](https://github.com/dotnet/efcore/issues)的問題。
  * 已解決[關閉](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aclosed)的問題。
    * 已修正的所有問題都會[以封閉式-fixed 標記](https://github.com/dotnet/efcore/issues?q=is%3Aissue+label%3Aclosed-fixed+is%3Aclosed)。 已修正併合並標記為封閉式的問題，但可能尚未發行。
    * 其他 `closed-` 標籤則表示關閉問題的其他原因。 例如，重複專案會以已關閉的重複標記。
* 類型
  * [Bug](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Atype-bug)代表 bug。
  * [增強](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Atype-enhancement)功能代表現有功能中的新功能或較佳的功能。
* 里程碑
  * 小組不會考慮[任何里程碑的問題](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+no%3Amilestone)。 尚未進行此問題的決定，或考慮的變更也不是如此。
  * 待處理專案（ [Backlog）里程碑中的問題](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog)是 EF 小組在未來的版本中將考慮的專案
    * 待處理專案中的問題可能會標記為 [[考慮-下一版](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Aconsider-for-next-release)]，表示下一版的清單中此工作專案很高。
  * 版本化里程碑中的開啟問題是小組計畫在該版本中處理的專案。 例如，[這些是我們打算針對 EF Core 5.0 進行處理的問題](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A5.0.0)。
  * 已建立版本之里程碑中的已關閉問題是針對該版本完成的問題。 請注意，版本可能尚未發行。 例如，[這些是針對 EF Core 3.0 而完成的問題](https://github.com/dotnet/efcore/issues?q=is%3Aissue+milestone%3A3.0.0+is%3Aclosed)。
* 投票!
  * 投票是指出問題對您很重要的最佳方式。
  * 若要投票，只要在問題中加入「拇指」即可 👍 。 例如，[這些是最常投票的問題](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc)
  * 如果您覺得這會增加價值，請也將批註描述您需要此功能的特定原因。 批註 "+ 1" 或類似的不會增加值。

### <a name="the-planning-process"></a>規劃程序

規劃程式比只從待處理專案取得最上層要求的功能更多。
這是因為我們會以多種方式收集來自多個專案關係人的意見反應。
然後，我們會根據下列內容來塑造發行：

* 客戶的輸入
* 來自其他專案關係人的輸入
* 策略性方向
* 可用的資源
* 排程

我們提出的一些問題是：

1. **我們認為多少開發人員會使用此功能，而它讓他們的應用程式或體驗改善多少？** 為了回答這個問題，我們從許多來源收集意見反應 — 問題的留言與投票是那些來源之一。 具有重要客戶的特定合作是另一個。

2. **如果我們尚未實行這項功能，有哪些因應措施可供使用者使用？** 舉例來說，許多開發人員在缺少原生多對多支援時，都會想到運用聯合資料表作為因應措施。 很明顯地，並非所有開發人員都想這麼做，但其中許多人都做得到，而這也是我們決策中所考量的因素。

3. **實作此功能是否會涉及 EF Core 的架構，使我們更加需要實作其他功能？** 我們偏好可作為其他功能建置組塊的功能。 比方說，屬性包實體可以協助我們邁向多對多的支援，而實體建構函式則促成了消極式載入支援。

4. **這項功能是擴充點嗎？** 因為擴充點使開發人員能夠輕鬆地依自己的習慣使用，並填補任何功能上的缺失，所以我們偏好擴充點勝過於一般功能。

5. **此功能與其他產品搭配使用時的綜效如何？** 我們偏好能促成或大幅改善 EF Core 搭配其他產品使用體驗的功能，例如 .NET Core、最新版本的 Visual Studio、Microsoft Azure 等等。

6. **有哪些人員可以用來處理某項功能的技能，以及如何充分利用這些資源？** 每位 EF 小組的成員及我們社群的參與者，在不同領域都各有不同程度的經驗，所以我們必須據此進行規劃。 即使我們希望將「所有人力」集中在像是 GroupBy 翻譯，或多對多關聯性等特定功能上，也無法實際付諸行動。
