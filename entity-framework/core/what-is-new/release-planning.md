---
title: EF 核心發佈計劃
author: ajcvickers
ms.date: 01/28/2020
uid: core/what-is-new/release_planning.md
ms.openlocfilehash: 71045b8d49c319a73f74443612bedd84ee33ab8a
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "78417333"
---
# <a name="release-planning-process"></a>發行計劃程序

在我們收到的問題中，經常有人問起我們如何決定將某功能加入特定的版本。
本文檔概述了我們使用的過程。
隨著我們找到更好的規劃方法,這個過程在不斷發展,但一般想法保持不變。

## <a name="different-kinds-of-releases"></a>不同類型的版本

不同類型的版本包含不同類型的更改。
這反過來意味著針對不同類型的版本,發佈計劃是不同的。

### <a name="patch-releases"></a>修補程式版本

修補程式版本僅更改版本的「修補程式」 部分。
例如,EF 核心 3.1。**1**是修補程式 EF Core 3.1 中發現的問題的發佈。**0**.

修補程式版本旨在修復關鍵客戶 Bug。
這意味著修補程式版本不包含新功能。
除非在特殊情況下,否則修補程式版本中不允許進行 API 更改。

在修補程式版本中進行更改的柱線非常高。
這是因為修補程式版本不引入新 Bug 至關重要。
因此,決策過程強調高價值和低風險。

如果出現:
  * 它影響多個客戶
  * 它是從上一個版本的回歸
  * 失敗導致資料損毀

如果出現以下問題,我們不太可能修補問題:
  * 有合理的解決方法
  * 修復有破壞其他東西的高風險
  * 錯誤處於拐角匣中

此條柱逐漸在[長期支援 (LTS)](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)發佈的生存期內上升。 這是因為 LTS 版本強調穩定性。

關於是否修補問題的最終決定由微軟的 .NET 主管做出。

### <a name="minor-releases"></a>次要版本

次要版本僅更改版本的"次要"部分。
例如,EF 核心 3。**1**.0 是改進 EF 核心 3 的發佈。**0**.0。

次要版本:
* 旨在提高上一版本的品質和功能
* 通常包含錯誤修復與新功能
* 不包括故意中斷變更
* 將一些預先預覽到 NuGet

### <a name="major-releases"></a>主要版本

主要版本更改 EF"主要"
例如,EF Core **3**.0.0 是一個主要版本,在 EF Core 2.2.x 上向前邁出了一大步。

主要版本:
* 旨在提高上一版本的品質和功能
* 通常包含錯誤修復與新功能
  * 一些新功能可能是 EF Core 工作方式的根本改變
* 通常包括有意中斷的變更
  * 在我們學習時,重大變革是不斷發展的 EF 核心的必要部分
  * 但是,由於潛在的客戶影響,我們非常仔細地考慮做出任何重大改變。 過去,我們可能過於積極地進行重大變革。 今後,我們將努力盡量減少破壞應用程式的更改,並減少中斷資料庫提供程式和擴展的更改。
* 將許多預先發行預覽推送到 NuGet

## <a name="planning-for-majorminor-releases"></a>主要/次要版本的規劃

### <a name="github-issue-tracking"></a>GitHub 問題追蹤

GitHub[https://github.com/dotnet/efcore](https://github.com/dotnet/efcore)( ) 是所有 EF 核心規劃的真諦源。

GitHub 上的問題有:

* 狀態
  * [尚未解決未決](https://github.com/dotnet/efcore/issues)問題。
  * [已解決已](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aclosed)解決的問題。
    * 已修復的所有問題都[標有閉合修復](https://github.com/dotnet/efcore/issues?q=is%3Aissue+label%3Aclosed-fixed+is%3Aclosed)。 已修復並合併了標記為閉合修復的問題,但可能尚未釋放。
    * 其他`closed-`標籤指示關閉問題的其他原因。 例如,重複項用閉複式標記。
* 類型
  * [錯誤](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Atype-bug)表示 Bug。
  * [增強功能](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Atype-enhancement)表示現有功能中的新功能或更好的功能。
* 里程碑
  * 團隊正在考慮[沒有里程碑的問題](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+no%3Amilestone)。 尚未就如何處理該問題作出決定,或正在考慮改變該決定。
  * [積壓工作里程碑中的問題](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog),是 EF 團隊將考慮在將來的版本中處理的專案
    * 積壓工作中的問題可能會[標記為"考慮下一個版本](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Aconsider-for-next-release)",指示此工作項在下一個版本的清單中處於較高位置。
  * 版本控制里程碑中的未打開問題是團隊計劃在這一版本中處理的專案。 例如,[這些是我們計劃為 EF Core 5.0 解決的問題](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A5.0.0)。
  * 版本控制里程碑中的已關閉問題是該版本已完成的問題。 請注意,該版本可能尚未發佈。 例如,[這些是 EF 核心 3.0 完成的問題](https://github.com/dotnet/efcore/issues?q=is%3Aissue+milestone%3A3.0.0+is%3Aclosed)。
* 票!
  * 投票是表明問題對您很重要的最佳方式。
  * 要投票,只需為問題添加"豎起大拇指👍"。 例如[,這些是投票率最高的問題](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc)
  * 如果您覺得這增加了價值,請還請註釋描述需要該功能的具體原因。 註釋"+1"或類似值不會增加價值。

### <a name="the-planning-process"></a>規劃程序

規劃過程涉及的不僅僅是從積壓工作中獲取請求最多的功能。
這是因為我們以多種方式從多個利益相關者收集反饋。
然後,我們根據:

* 從客戶輸入
* 其他利益相關者提供的意見
* 戰略方向
* 可用資源
* 排程

我們問的一些問題包括:

1. **我們認為多少開發人員會使用此功能，而它讓他們的應用程式或體驗改善多少？** 為了回答這個問題，我們從許多來源收集意見反應 — 問題的留言與投票是那些來源之一。 與重要客戶的具體互動是另一回事。

2. **如果我們尚未實現此功能,人們可以使用哪些解決方法?** 舉例來說，許多開發人員在缺少原生多對多支援時，都會想到運用聯合資料表作為因應措施。 很明顯地，並非所有開發人員都想這麼做，但其中許多人都做得到，而這也是我們決策中所考量的因素。

3. **實作此功能是否會涉及 EF Core 的架構，使我們更加需要實作其他功能？** 我們偏好可作為其他功能建置組塊的功能。 比方說，屬性包實體可以協助我們邁向多對多的支援，而實體建構函式則促成了消極式載入支援。

4. **這項功能是擴充點嗎？** 因為擴充點使開發人員能夠輕鬆地依自己的習慣使用，並填補任何功能上的缺失，所以我們偏好擴充點勝過於一般功能。

5. **此功能與其他產品搭配使用時的綜效如何？** 我們偏好能促成或大幅改善 EF Core 搭配其他產品使用體驗的功能，例如 .NET Core、最新版本的 Visual Studio、Microsoft Azure 等等。

6. **可用於處理某項功能的人員有哪些技能,我們如何才能最好地利用這些資源?** 每位 EF 小組的成員及我們社群的參與者，在不同領域都各有不同程度的經驗，所以我們必須據此進行規劃。 即使我們希望將「所有人力」集中在像是 GroupBy 翻譯，或多對多關聯性等特定功能上，也無法實際付諸行動。
