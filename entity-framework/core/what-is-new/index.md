---
title: 新功能 - EF Core
author: divega
ms.date: 02/20/2018
ms.assetid: C21F89EE-FB08-4ED9-A2A0-76CB7656E6E4
uid: core/what-is-new/index
ms.openlocfilehash: 2ca4915fca515b4bdbfeb77bc7b02f15ce1704b6
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197734"
---
# <a name="whats-new-in-ef-core"></a>EF Core 中的新功能

## <a name="recent-releases"></a>最新發行

- **EF Core 3.0** (最新的穩定版本) 
  - [新功能](xref:core/what-is-new/ef-core-3.0/index) 
  - 升級時應該注意的[中斷性變更](xref:core/what-is-new/ef-core-3.0/breaking-changes)
- [EF Core 2.2](xref:core/what-is-new/ef-core-2.2)
- [EF Core 2.1](xref:core/what-is-new/ef-core-2.1) (最新的長期支援版本)

## <a name="product-roadmap"></a>產品藍圖

> [!IMPORTANT]
> 請注意，後續版本的功能集與上市時間，隨時可能會變動，此外，即使我們試圖將此頁面保持在最新狀態，可能還是無法隨時反映我們最新的計劃。

### <a name="future-releases"></a>未來的版本

- **EF Core 3.1**  
  - 積極開發中
  - 將包含[微幅效能、品質與穩定性改善](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3A3.1.0+sort%3Areactions-desc) \(英文\)
  - 已規劃為長期支援 (LTS) 版本
  - 目前已排定 2019 年 12 月
- **EF Core "vNext"**   
  - 隨附於 .NET 5 之 EF Core 的下一個主要版本
  - 這個版本的規劃尚未開始，也未宣佈任何功能。  

### <a name="schedule"></a>排程

EF Core 發行排程與 [.NET Core 發行排程](https://github.com/dotnet/core/blob/master/roadmap.md)保持同步。

### <a name="backlog"></a>Backlog

我們問題追蹤器中的[待處理項目里程碑](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog+sort%3Areactions-%2B1-desc)包含我們預期某天要處理的問題，或是我們認為社群成員可以處理的問題。
歡迎客戶提交意見，並針對這些問題投票。
我們鼓勵有意願處理這些問題參與者，先針對如何開始著手進行討論。

我們絕未保證我們會在特定 EF Core 版本處理任何指定的功能。
就如同所有軟體專案、優先順序、發行排程及可用資源，都可能隨時變更。
但是，若我們想要解決特定時間範圍內的問題，我們會將其指派到發行里程碑，而非指派到待辦項目里程碑。
我們會定期將問題在待辦項目里程碑與發行里程碑之間移動，作為我們的[發行計劃程序](#release-planning-process)。

若我們預計不會處理此問題，很可能就會結束此問題。
若我們取得先前已結束問題的新資訊，就會重新評估該問題。

### <a name="release-planning-process"></a>發行計劃程序

在我們收到的問題中，經常有人問起我們如何決定將某功能加入特定的版本。
我們的待辦項目絕不必然等同於發行計劃。
EF6 中若存在某功能，也並不代表該功能必須施行於 EF Core 中。

很難詳述我們計劃發行所遵循的整個程序。
其中大部分是在討論特定的功能、商機與優先順序，且程序本身也會隨著每次發行而演進。
但在我們決定下一步該做什麼時，可以先總結我們試圖回答的常見問題：

1. **我們認為多少開發人員會使用此功能，而它讓他們的應用程式或體驗改善多少？** 為了回答這個問題，我們從許多來源收集意見反應 — 問題的留言與投票是那些來源之一。

2. **如果我們尚未實作這項功能，那有哪些因應措施可用？** 舉例來說，許多開發人員在缺少原生多對多支援時，都會想到運用聯合資料表作為因應措施。 很明顯地，並非所有開發人員都想這麼做，但其中許多人都做得到，而這也是我們決策中所考量的因素。

3. **實作此功能是否會涉及 EF Core 的架構，使我們更加需要實作其他功能？** 我們偏好可作為其他功能建置組塊的功能。 比方說，屬性包實體可以協助我們邁向多對多的支援，而實體建構函式則促成了消極式載入支援。

4. **這項功能是擴充點嗎？** 因為擴充點使開發人員能夠輕鬆地依自己的習慣使用，並填補任何功能上的缺失，所以我們偏好擴充點勝過於一般功能。

5. **此功能與其他產品搭配使用時的綜效如何？** 我們偏好能促成或大幅改善 EF Core 搭配其他產品使用體驗的功能，例如 .NET Core、最新版本的 Visual Studio、Microsoft Azure 等等。

6. **可參與功能開發的人員技能如何，以及如何善用這些資源？** 每位 EF 小組的成員及我們社群的參與者，在不同領域都各有不同程度的經驗，所以我們必須據此進行規劃。 即使我們希望將「所有人力」集中在像是 GroupBy 翻譯，或多對多關聯性等特定功能上，也無法實際付諸行動。

如先前所說，程序會隨著每次發行而演進。
在未來，我們會嘗試新增更多機會，讓社群的成員能提供對我們發行計劃的投入。
例如，我們希望讓檢閱功能的草稿設計與發行計劃本身更加容易。