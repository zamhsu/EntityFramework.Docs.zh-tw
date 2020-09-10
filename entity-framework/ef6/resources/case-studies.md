---
title: Entity Framework 的案例研究-EF6
description: Entity Framework 6 的個案研究
author: divega
ms.date: 10/23/2016
ms.assetid: cd5d3ae3-717d-4095-a2ef-0e8fd72b1a2f
ms.openlocfilehash: f6cabe34cfbdc5f15758678f458b2c0898198137
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620234"
---
# <a name="microsoft-case-studies-for-entity-framework"></a>適用于 Entity Framework 的 Microsoft 個案研究
此頁面上的個案研究會強調一些已採用 Entity Framework 的真實世界生產專案。
> [!NOTE]
> 這些案例研究的詳細版本已不再于 Microsoft 網站上提供。 因此已移除連結。

## <a name="epicor"></a>Epicor
Epicor 是一家大型的全球軟體公司 (有超過400的開發人員) ，為超過150個國家/地區的公司開發企業資源規劃 (ERP) 解決方案。
其旗艦產品（Epicor 9）是以服務導向的架構為基礎， (SOA) 使用 .NET Framework。
由於有許多客戶要求提供 (LINQ) 的語言整合式查詢支援，而且也想要減少其後端 SQL Server 的負載，因此小組決定升級到 Visual Studio 2010 和 .NET Framework 4.0。
使用 Entity Framework 4.0，他們能夠達成這些目標，也大幅簡化了開發和維護工作。
尤其是 Entity Framework 的豐富 T4 支援可讓他們完全掌控其產生的程式碼，並自動建立以效能節省的功能，例如預先編譯的查詢和快取。

> 「我們最近使用了現有的程式碼進行過一些效能測試，而且我們能夠減少90% 的 SQL Server 要求。
這是因為 ADO.NET Entity Framework 4」。 –產品研究副總裁 Erik Johnson  

## <a name="veracity-solutions"></a>真實性解決方案
我們取得的事件規劃軟體系統將難以維護及擴充，而是使用 Visual Studio 2010 的長期真實性解決方案，將它重新撰寫為以 Silverlight 4 為基礎且容易使用的強大豐富網際網路應用程式。
使用 .NET RIA Services，它們可以在 Entity Framework 上快速建立服務層，以避免程式碼重複，並允許跨層的一般驗證和驗證邏輯。  

> 「我們是在第一次引進 Entity Framework 時銷售，而 Entity Framework 4 已證明更好。
工具經過改良，而且可以更輕鬆地管理定義概念模型、儲存體模型，以及這些模型之間對應的 .edmx 檔。有了 Entity Framework，我可以在一天內開始使用資料存取層，並在我的過程中建立。
Entity Framework 是我們的資料存取層，我不知道什麼人都不會使用它。」 – Joe McBride，資深開發人員

## <a name="nec-display-solutions-of-america"></a>NEC 顯示北美洲的解決方案
NEC 想要透過解決方案來進入市場以進行數位位置型廣告，以受益于廣告商和網路擁有者並增加其收入。
為了達成此目的，它會啟動一組 web 應用程式，以將傳統 ad 活動中所需的手動程式自動化。
這些網站是使用 ASP.NET、Silverlight 3、AJAX 和 WCF 建立的，以及資料存取層中的 Entity Framework，以與 SQL Server 2008 交談。

> 「有了 SQL Server，我們認為我們可以取得我們所需的輸送量，以即時提供資訊給廣告人員和網路，並提供可靠性，以協助確保要徑任務應用程式中的資訊一律可供使用」-Mike Corcoran 的主管

## <a name="darwin-dimensions"></a>Darwin 維度
使用各式各樣的 Microsoft 技術，Darwin 團隊的團隊可建立 Evolver，這是一個線上的內部人員入口網站，消費者可以用它來建立美觀、逼真的虛擬人偶，以在遊戲、動畫和社交網路頁面中使用。
藉由 Entity Framework 的生產力優勢，以及 Windows Workflow Foundation (WF) 和 Windows Server AppFabric 這類元件 (可高度擴充的記憶體內部應用程式快取) ，小組就能在35% 的開發時間內提供絕佳的產品。
儘管將小組成員分割成多個國家/地區，但小組會遵循每週發行的敏捷式開發流程。

 > 「我們試著不要為技術創造技術。 一開始，我們一定要運用可節省時間和金錢的技術。
 .NET 是快速、符合成本效益的開發的選擇。」 – Zachary Olsen，架構設計人員  

## <a name="silverware"></a>銀器
有超過15年的開發銷售點 (POS) 解決方案適用于小型和中型餐廳群組，Silverware 的開發團隊會設定為利用更多企業級功能增強其產品，以吸引較大型的餐廳鏈。
使用 Microsoft 開發工具的最新版本，可以比以往更快速地建立新的方案四倍。
LINQ 和 Entity Framework 這類重要的新功能，可讓您更輕鬆地從 Crystal Reports 移至 SQL Server 2008，並 SQL Server Reporting Services (SSRS) 以滿足其資料儲存和報告需求。

> 「有效的資料管理是 SilverWare 成功的關鍵，因此我們決定採用 SQL Reporting」。 -IT/軟體工程主管的 Nicholas Romanidis
