---
title: Entity Framework EF6 的案例研究
author: divega
ms.date: 10/23/2016
ms.assetid: cd5d3ae3-717d-4095-a2ef-0e8fd72b1a2f
ms.openlocfilehash: d7982a3f89ac1e57b48039d828f287adf6dc5068
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417078"
---
# <a name="microsoft-case-studies-for-entity-framework"></a>適用于 Entity Framework 的 Microsoft 個案研究
此頁面上的案例研究會反白顯示一些已採用 Entity Framework 的實際生產專案。
> [!NOTE]
> 這些案例研究的詳細版本已不再于 Microsoft 網站上提供。 因此，已移除連結。

## <a name="epicor"></a>Epicor
Epicor 是大型的全球軟體公司（具有超過400的開發人員），可為超過150個國家/地區的公司開發企業資源規劃（ERP）解決方案。
其旗艦版產品 Epicor 9 是以服務導向架構（SOA）為基礎，使用 .NET Framework。
小組會面臨許多客戶要求，以提供語言整合式查詢（LINQ）的支援，而且也想要減少其後端 SQL Server 的負載，而團隊決定升級至 Visual Studio 2010 和 .NET Framework 4.0。
藉由使用 Entity Framework 4.0，他們就能夠達到這些目標，也能大幅簡化開發和維護。
特別的是，Entity Framework 豐富的 T4 支援可以讓他們完全控制其產生的程式碼，並自動建立以效能儲存的功能，例如預先編譯的查詢和快取。

> 「我們最近使用現有的程式碼進行了一些效能測試，而我們可以減少 SQL Server 90% 的要求。
這是因為 ADO.NET Entity Framework 4）。」 – Erik Johnson，產品研究副總裁  

## <a name="veracity-solutions"></a>真實性解決方案
取得的事件規劃軟體系統很難維護並延伸長期的真實性解決方案，Visual Studio 2010 將其重新撰寫成以 Silverlight 4 為基礎的強大且便於使用的豐富網際網路應用程式。
他們可以使用 .NET RIA Services，快速地在 Entity Framework 之上建立服務層，以避免程式碼重複，並允許跨層進行通用驗證和驗證邏輯。  

> 「我們是在第一次引進 Entity Framework 時銷售，而 Entity Framework 4 證明更好。
工具已經過改良，而且可以更輕鬆地操作定義概念模型、儲存模型，以及這些模型之間對應的 .edmx 檔案 .。。有了 Entity Framework，我就可以讓資料存取層在一天內運作，然後再建立一次。
Entity Framework 是我們事實上的資料存取層;我不知道為什麼有人不會使用它。」 -Joe McBride，資深開發人員

## <a name="nec-display-solutions-of-america"></a>地區的 NEC 顯示解決方案
NEC 想要進入以數位位置為基礎之廣告的市場，其中包含可受益于廣告商和網路擁有者的解決方案，並增加自己的收益。
為了這麼做，它會啟動一對 web 應用程式，將傳統 ad 活動中所需的手動程式自動化。
這些網站是使用 ASP.NET、Silverlight 3、AJAX 和 WCF 所建立，以及資料存取層中的 Entity Framework 來與 SQL Server 2008 通訊。

> 「有了 SQL Server，我們覺得我們可以取得我們所需的輸送量，以即時提供資訊給廣告者和網路，並提供可靠性以協助確保要徑任務應用程式中的資訊一律可供使用」-Mike Corcoran，IT 主管

## <a name="darwin-dimensions"></a>達爾文維度
使用廣泛的 Microsoft 技術，達爾文團隊將建立 Evolver，這是一個線上的頭像入口網站，取用者可以用來建立令人驚歎、逼真的虛擬人偶，以便在遊戲、動畫及社交網路頁面中使用。
有了 Entity Framework 的生產力優勢，並提取 Windows Workflow Foundation （WF）和 Windows Server AppFabric 這類元件（高擴充性的記憶體中應用程式快取），小組在35% 的時間內就能提供令人驚奇的產品開發時間。
雖然小組成員會分割到多個國家/地區，但小組會遵循敏捷式開發程式與每週發行。

 > 「我們不會試著建立科技的技術。 在啟動時，請務必利用可節省時間和金錢的技術。
 .NET 是快速、符合成本效益的開發選擇。」 – Zachary Olsen，架構設計師  

## <a name="silverware"></a>Silverware
在開發小型和中型餐廳群組的銷售點（POS）解決方案超過15年的經驗後，Silverware 的開發小組已設定為使用更多企業層級功能來增強其產品，以便更大餐廳鏈。
使用最新版本的 Microsoft 開發工具，他們能夠以前所未有的速度比以往更快建立新的方案四倍。
LINQ 和 Entity Framework 這類重要的新功能，可讓您更輕鬆地從 Crystal 報表移至 SQL Server 2008 和 SQL Server Reporting Services （SSRS），以滿足其資料儲存和報告需求。

> 「有效的資料管理是 SilverWare 成功的關鍵，這就是我們決定採用 SQL Reporting 的原因。」 -Nicholas Romanidis，IT/軟體工程主管
