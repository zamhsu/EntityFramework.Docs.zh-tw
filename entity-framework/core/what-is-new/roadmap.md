---
title: Entity Framework Core 藍圖
author: divega
ms.date: 02/20/2018
ms.assetid: 834C9729-7F6E-4355-917D-DE3EE9FE149E
uid: core/what-is-new/roadmap
ms.openlocfilehash: f466d112e5ed8661ad1e2d91079c1c01d34b9002
ms.sourcegitcommit: a013e243a14f384999ceccaf9c779b8c1ae3b936
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2019
ms.locfileid: "57463226"
---
# <a name="entity-framework-core-roadmap"></a>Entity Framework Core 藍圖

> [!IMPORTANT]
> 請注意，後續版本的功能集與上市時間，隨時可能會變動，此外，即使我們試圖將此頁面保持在最新狀態，可能還是無法隨時反映我們最新的計劃。

### <a name="ef-core-30"></a>EF Core 3.0

EF Core 2.2 已然問世，我們現在的主要焦點為 EF Core 3.0。
如需該版本中包含規劃的[新功能](xref:core/what-is-new/ef-core-3.0/features)和目的性的[中斷性變更](xref:core/what-is-new/ef-core-3.0/breaking-changes)等資訊，請參閱 [EF Core 3.0 的新功能](xref:core/what-is-new/ef-core-3.0/index)。

## <a name="schedule"></a>排程

EF Core 發行排程與 [.NET Core 發行排程](https://github.com/dotnet/core/blob/master/roadmap.md)保持同步。

## <a name="backlog"></a>Backlog

我們問題追蹤器中的[待辦項目里程碑](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog+sort%3Areactions-%2B1-desc)，包含我們希望有朝一日能努力解決，或認為社群中有人能處理的問題。
歡迎客戶提交意見，並針對這些問題投票。
我們鼓勵有意願處理這些問題參與者，先針對如何開始著手進行討論。

我們並不保證會針對特定 EF Core 版本中的任何指定功能進行處理。
就如同所有軟體專案、優先順序、發行排程及可用資源，都可能隨時變更。
但是，若我們想要解決特定時間範圍內的問題，我們會將其指派到發行里程碑，而非指派到待辦項目里程碑。
我們會定期將問題在待辦項目里程碑與發行里程碑之間移動，作為我們的[發行計劃程序](#release-planning-process)。

若我們預計不會處理此問題，很可能就會結束此問題。
若我們取得先前已結束問題的新資訊，就會重新評估該問題。

## <a name="release-planning-process"></a>發行計劃程序

在我們收到的問題中，經常有人問起我們如何決定將某功能加入特定的版本。
我們的待辦項目絕不必然等同於發行計劃。
EF6 中若存在某功能，也並不代表該功能必須施行於 EF Core 中。

很難詳述我們計劃發行所遵循的整個程序。
其中大部分是在討論特定的功能、商機與優先順序，且程序本身也會隨著每次發行而演進。
但在我們決定下一步該做什麼時，可以先總結我們試圖回答的常見問題：

1. **我們認為有多少開發人員會使用此功能，而該功能會使其應用程式/體驗獲得多少改善？** 若要解答此問題，我們會從各種來源收集意見，其中一個來源就包括了對問題的留言與投票。

2. **如果我們尚未實作這項功能，那有哪些因應措施可用？** 舉例來說，許多開發人員在缺少原生多對多支援時，都會想到運用聯合資料表作為因應措施。 很明顯地，並非所有開發人員都想這麼做，但其中許多人都做得到，而這也是我們決策中所考量的因素。

3. **實作此功能是否會涉及 EF Core 的架構，使我們更加需要實作其他功能？** 我們偏好可作為其他功能建置組塊的功能。 例如，屬性包實體可協助我們往多對多支援邁進，且實體建構函式讓我們的消極式載入支援變得可行。

4. **這項功能是擴充點嗎？** 因為擴充點使開發人員能夠輕鬆地依自己的習慣使用，並填補任何功能上的缺失，所以我們偏好擴充點勝過於一般功能。

5. **此功能與其他產品搭配使用時的綜效如何？** 我們偏好讓 EF Core 能與其他產品搭配使用，或大幅改善與其他產品 (例如 .NET Core、最新版的 Visual Studio、Microsoft Azure 等) 一併使用的體驗之功能。

6. **可參與功能開發的人員技能如何，以及如何善用這些資源？** 每位 EF 小組的成員及我們社群的參與者，在不同領域都各有不同程度的經驗，所以我們必須據此進行規劃。 即使我們希望將「所有人力」集中在像是 GroupBy 翻譯，或多對多關聯性等特定功能上，也無法實際付諸行動。

如先前所說，程序會隨著每次發行而演進。
未來我們會嘗試為社群的成員新增更多機會，以促進我們的發行計劃。
例如，我們希望讓檢閱功能的草稿設計與發行計劃本身更加容易。
