---
title: Entity Framework-EF6 的個案研究
author: divega
ms.date: 10/23/2016
ms.assetid: cd5d3ae3-717d-4095-a2ef-0e8fd72b1a2f
ms.openlocfilehash: d7982a3f89ac1e57b48039d828f287adf6dc5068
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490872"
---
# <a name="microsoft-case-studies-for-entity-framework"></a>Entity Framework 的 Microsoft 案例研究
案例研究，在此頁面上反白顯示都利用 Entity Framework 的幾個實際生產環境專案。
> [!NOTE]
> 這些案例研究的詳細的版本已不再從 Microsoft 網站上取得。 因此已移除的連結。

## <a name="epicor"></a>Epicor
Epicor 是 （有超過 400 個開發人員） 在超過 150 個國家/地區的公司開發企業資源規劃 (ERP) 解決方案的大型的全球軟體公司。
其旗艦產品 Epicor 9，以在服務導向架構 (SOA) 使用.NET Framework 為基礎。
若要升級至 Visual Studio 2010 和.NET Framework 4.0，面臨許多的客戶要求，以支援 Language Integrated Query (LINQ)，和也想要降低後端 SQL 伺服器上的負載，該團隊決定。
它們使用 Entity Framework 4.0，才能夠達成這些目標，也可大幅簡化開發與維護。
特別的是，Entity Framework 的豐富的 T4 支援能讓他們全面掌控其產生的程式碼，並會自動建置效能節約功能，例如預先編譯的查詢和快取中。

> 「 我們進行一些效能測試，最近與現有的程式碼，而且我們能夠減少要求至 SQL Server 90%。
這是因為 ADO.NET Entity Framework 4。 」 – Erik Johnson，副總裁產品研究  

## <a name="veracity-solutions"></a>真實性解決方案
需要取得事件計劃的軟體系統，很難維護及擴充長期來看，真實性解決方案可以用 Visual Studio 2010 來重新撰寫功能強大且容易使用的豐富網際網路應用程式建立的 Silverlight 4。
使用.NET RIA Services 時，他們能夠快速建置 Entity Framework，避免程式碼重複，並允許進行通用驗證，並驗證邏輯層之上的服務層級。  

> 「 我們所銷售的 Entity Framework 時引進，和 Entity Framework 4 已證實是更好。
工具也有所提升，並更輕鬆地操作定義概念模型、 儲存體模型和這些模型之間對應的.edmx 檔案...使用 Entity Framework，我可以取得一天內使用該資料存取層，並建置它，我進行。
Entity Framework 是我們的實際資料存取層;我不知道為什麼任何人都不會使用它。 」 – Joe McBride 資深開發人員

## <a name="nec-display-solutions-of-america"></a>America 專屬的 NEC 顯示解決方案
NEC 想要輸入數位位置為基礎的廣告，廣告商和網路擁有者中獲益，並提升自己的營收的解決方案的市場。
若要這樣做，就會啟動一組自動化手動程序需要在傳統的廣告活動中的 web 應用程式。
使用 ASP.NET、 Silverlight 3、 AJAX 和 WCF，以及資料存取層中的 Entity Framework，與 SQL Server 2008 所建置的網站。

> 「 SQL server，我們認為我們可以取得我們提供廣告商和網路服務與即時及可靠性，以協助確保一律會使用我們的任務關鍵性應用程式中的資訊中的資訊時所需的輸送量 」-Mike Corcoran協理 IT

## <a name="darwin-dimensions"></a>達爾文港維度
使用 Microsoft 廣泛的技術，達爾文港的小組著手建立 Evolver-取用者可以用來建立用於令人讚嘆、 lifelike 虛擬人偶遊戲、 動畫和社交網路的頁面中的線上虛擬人偶入口網站。
使用 Entity Framework 和 Windows Workflow Foundation (WF) 和 Windows Server AppFabric （高度可擴充的記憶體內部應用程式快取） 等元件中提取的生產力優勢，小組就可以令人讚嘆的產品提供 35%較少開發時間。
儘管團隊成員分成多個國家/地區，小組遵循敏捷式開發程序，並提供每週的版本。

 > 「 我們試著不要建立技術的目的的技術。 為啟動時，很重要，我們運用技術，可以節省時間與金錢。
 .NET 是快速且符合成本效益的開發選擇。 」 – Zachary Olsen，架構設計人員  

## <a name="silverware"></a>銀器
15 年以上的開發小型和中型的餐廳群組的銷售點 (POS) 解決方案的經驗，開發小組在銀器著手來增強他們的產品，具有更多的企業層級功能以吸引更大餐廳鏈結。
使用最新版的 Microsoft 開發工具，它們能夠建置新的方案四倍速度。
索引鍵的新功能，例如 LINQ 和 Entity Framework，更輕鬆地從 Crystal Reports 移轉至 SQL Server 2008 和 SQL Server Reporting Services (SSRS) 來儲存其資料和報告需求。

> 「 有效的資料管理是機碼的銀器 – 成功而這就是為什麼我們決定採用 SQL Reporting。 」 -Nicholas Romanidis，協理 IT / 軟體工程
