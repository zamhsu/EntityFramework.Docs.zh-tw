---
title: Entity Framework Core 藍圖
author: divega
ms.author: divega
ms.date: 02/20/2018
ms.assetid: 834C9729-7F6E-4355-917D-DE3EE9FE149E
ms.technology: entity-framework-core
uid: core/what-is-new/roadmap
ms.openlocfilehash: 5aef679df2ecdfe7f59458c8994d0d17b4a889ff
ms.sourcegitcommit: 2ef0a4a90b01edd22b9206f8729b8de459ef8cab
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2018
---
# <a name="entity-framework-core-roadmap"></a>Entity Framework Core 藍圖

> [!IMPORTANT]
> 請注意，後續版本的功能集與上市時間，隨時可能會變動，此外，即使我們試圖將此頁面保持在最新狀態，可能還是無法隨時反映我們最新的計劃。

EF Core 2.1 的第一個預覽版於 2018 年 2 月發行。 您現已可於 [EF Core 2.1 中的新功能](xref:core/what-is-new/ef-core-2.1)找到此版本的詳細資訊。

我們預計每月還會額外發行 EF Core 2.1 預覽版，並會於 2018 年的第二個日曆季度發行最終版本。

我們尚未完成 2.1 之後下一個版本的[發行計劃](#release-planning-process)。

## <a name="schedule"></a>排程

EF Core 的發行時間與 [.NET Core 發行時間](https://github.com/dotnet/core/blob/master/roadmap.md)及 [ASP.NET Core 發行時間](https://github.com/aspnet/Home/wiki/Roadmap)保持同步。

## <a name="backlog"></a>待辦項目

我們在問題追蹤程式內採用了[待辦項目里程碑](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog+sort%3Areactions-%2B1-desc)，來保留詳細的問題與功能清單。 客戶可在這些項目上留言和附議。

對於我們合理預期將於某個時間點解決，或是能夠由社群成員處理的問題，我們偏好將問題開放而不結案。但這並不表示我們傾向於在特定時間範圍內予以解決，除非我們在[發行計劃程序](#release-planning-process)中為問題指派特定的里程碑。

如果我們不打算實作某項功能，就可能會關閉該問題。 若我們收到問題的新資訊，也可能在日後重新評估已經結案的問題。

話雖如此，我們目前對於未來所握有的資訊並不充足，無法斷言「我們將於 Y 時間/版本推出 X 功能」。因為所有軟體專案、優先順序、發行時間和可用的資源，都可能隨時會變動。

## <a name="release-planning-process"></a>發行計劃程序

在我們收到的問題中，經常有人問起我們如何決定將某功能加入特定的版本。 我們的待辦項目絕不必然等同發行計劃。 EF6 中若存在某功能，也並不代表該功能必定會施行於 EF Core 中。

我們很難在此詳細描述規劃發行的整個程序，部分原因是重點幾乎都集中在商談特定功能、商機與優先順序上，而另一部分則是因為程序本身會隨著每次發行而有所變動。 不過，在我們決定下一步該做什麼時，總結我們試圖回答的常見問題可就相對簡單多了：

1. **我們認為有多少開發人員會使用此功能，而該功能會使其應用程式/體驗獲得多少改善？** 我們會彙總各式來源的意見反應來做出結論，其中一項就包括了對問題的留言與投票。

2. **如果我們尚未實作這項功能，有哪些因應措施可用？** 舉例來說，許多開發人員在缺少原生多對多支援時，都會想到運用聯合資料表以作為因應措施。 很顯然並非所有開發人員都能這麼做，但大多數人都可以，而這就是我們會列入的考量。

3. **實作此功能是否會涉及 EF Core 的架構，使我們更加需要實作其他功能？** 我們傾向於加入其他功能有建置組塊用途的功能，例如為擁有之類型所製作的資料表分割功能，即可協助我們更可進行 TPT 支援。

4. **這項功能是擴充點嗎？** 我們傾向於加入擴充點，原因是擴充點能使開發人員更輕鬆地運用自己的習慣，並以該方式取得某些缺少的功能。 我們規劃在消極式載入作業開始前，加入一些擴充點。

5. **此功能與其他產品搭配使用時的綜效如何？** 我們傾向於加入可讓 EF Core 能其他產品搭配使用或大幅改善使用其他產品 (例如.NET Core、最新版的 Visual Studio、Microsoft Azure 等) 之體驗的功能。

6. **可參與功能開發的人員素質如何，以及如何善用這些資源？** 每位 EF 小組的成員以至於我們的社群參與者，在不同領域各有不同程度的經驗，而我們必須據此進行規劃。 即使我們希望將所有人力皆集中在像是 GroupBy 翻譯或多對多關聯性等特殊功能上，也無法實際付諸行動。

如上所述，此程序會隨著每次發行而變動，而我們也希望在未來為開發人員社群的成員提供更多機會，以為發行計劃投入更多心力，例如將對功能及發行計劃本身之建議草稿的檢閱過程簡化。
