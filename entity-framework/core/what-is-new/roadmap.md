---
title: Entity Framework Core 藍圖
author: divega
ms.date: 02/20/2018
ms.assetid: 834C9729-7F6E-4355-917D-DE3EE9FE149E
uid: core/what-is-new/roadmap
ms.openlocfilehash: 7eba9e1a8e145ef407f844ff3a3ab3069495b7ae
ms.sourcegitcommit: e66745c9f91258b2cacf5ff263141be3cba4b09e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2019
ms.locfileid: "54058730"
---
# <a name="entity-framework-core-roadmap"></a>Entity Framework Core 藍圖

> [!IMPORTANT]
> 請注意，後續版本的功能集與上市時間，隨時可能會變動，此外，即使我們試圖將此頁面保持在最新狀態，可能還是無法隨時反映我們最新的計劃。

### <a name="ef-core-30"></a>EF Core 3.0

儘管您已購買 EF Core 2.2，我們的主要焦點現在是 EF Core 3.0，這對應至 .NET Core 3.0 與 ASP.NET 3.0 版。

我們尚未完成任何新功能，因此在 2018 年 12 月 [發佈到 NuGet Gallery 的 EF Core 3.0 Preview 1 套件](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/3.0.0-preview.18572.1) 只包含[錯誤 (Bug) 修正、小幅改進與為了針對 3.0 工作做準備所進行的變更](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aissue+milestone%3A3.0.0+is%3Aclosed+label%3Aclosed-fixed)。

事實上，我們仍必須精簡我們的 3.0 [版本規劃](#release-planning-process)，以確認我們有可在配置時間內完成的正確功能集。
當我們有更清楚的資訊時將與您分享，這裡僅提供概略方向與我們要處理的工作：

- **LINQ 改善 ([#12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795))**：LINQ 可讓您以偏好的語言撰寫資料庫查詢，進而利用豐富的型別資訊優勢來取得 IntelliSense 與編譯階段型別檢查。
  但是 LINQ 也可以讓您撰寫不限數目的複雜查詢，這通常是 LINQ 提供者所面臨的艱難挑戰。
  在 EF Core 的前幾個版本中，我們透過搞清楚查詢的哪些部分可以轉譯為 SQL，接著允許查詢的剩餘部分在用戶端上的記憶體中執行來克服其中的一些問題。
  這個用戶端執行在某些案例中是令人滿意的，但在其他案例中會導致無效率的查詢，而在應用程式部署到生產階段之前，您可能無法發現這種無效率的查詢。
  在 EF Core 3.0 中，我們計畫變更我們的 LINQ 實作，以及測試它的方式。
  目標是讓它更強固 (例如，避免補充版本發行中的查詢中斷)、能夠正確將更多運算式轉譯為 SQL、在更多案例中產生有效率的查詢，以及防止為偵測到無效率查詢的情況。

- **Cosmos DB 支援 ([#8443](https://github.com/aspnet/EntityFrameworkCore/issues/8443))**：我們正在處理適用於 EF Core 的 Cosmos DB 提供者，以讓開發人員熟悉 EF 程式設計模型，以輕鬆地使用 Azure Cosmos DB 做為應用程式資料庫。
  目標是讓 .NET 開發人員能更輕鬆地發揮 Cosmos DB 的優點，例如全球散發、「一律可用」可用性、彈性的延展能力，以及低延遲。
  提供者將啟用大部分的 EF Core 功能，例如自動變更追蹤、LINQ 與值轉換 (根據 Cosmos DB 中的 SQL API)。 我們在 EF Core 2.2 之前開始此工作，而且[我們已建立一些預覽版提供者供使用](https://blogs.msdn.microsoft.com/dotnet/2018/10/17/announcing-entity-framework-core-2-2-preview-3/)。
  新的計畫是以 EF Core 3.0 為基礎繼續開發提供者。   

- **C# 8.0 支援 ([#12047](https://github.com/aspnet/EntityFrameworkCore/issues/12047))**：我們想要讓客戶利用 [C# 8.0 中的新功能](https://blogs.msdn.microsoft.com/dotnet/2018/11/12/building-c-8-0/)，例如非同步串流 (包括 await for each) 與可為 Null 值參考類型 (當使用 EF Core 時)。

- **反向工程資料庫檢視到查詢類型 ([#1679](https://github.com/aspnet/EntityFrameworkCore/issues/1679))**：在 EF Core 2.1 中，我們已加入對查詢類型的支援，它可以代表可從資料庫讀取但無法上傳的資料。
  查詢類型非常適用於對應資料庫檢視，因此在 EF Core 3.0 中，我們想要將針對資料庫檢視建立查詢類型的作業自動化。

- **屬性包實體 ([#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) 與 [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914))**：此功能是有關於啟用會在已編製索引之屬性內而非正常屬性內存放資料的實體，也有關於能夠使用相同.NET 類別的執行個體 (可能是像 `Dictionary<string, object>` 這麼簡單的東西) 以代表相同 EF Core 模型中的不同實體類型。
  此功能是在不需要聯結實體的情況下支援多對多關聯性的墊腳石，這是使用者最希望擁有的 EF Core 改良功能。

- **.NET Core 上的 EF 6.3 ([EF6 #271](https://github.com/aspnet/EntityFramework6/issues/271))**：我們了解許多現有的應用程式都使用舊版 EF，而且因為要獲得 .NET Core 的優點而將它們移植到 EF Core 有時候需要非常多的精力。
  因此，我們會讓下一個版本的 EF 6 能夠在 .NET Core 3.0 上直執行。
  我們這樣做的原因是要讓您能以變更最小的方式移植現有的應用程式。
  將有一些限制 (例如，它將需要新的提供者，將不會啟用 SQL Server 的空間支援)，而且沒有針對 EF 6 規劃的新功能。

同時，您可以使用[問題追蹤器中的此查詢](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3A3.0.0+sort%3Areactions-%2B1-desc)來查看暫時指派到 3.0 的工作項目。

## <a name="schedule"></a>排程

EF Core 的發行時間與 [.NET Core 發行時間](https://github.com/dotnet/core/blob/master/roadmap.md)及 [ASP.NET Core 發行時間](https://github.com/aspnet/Home/wiki/Roadmap)保持同步。

## <a name="backlog"></a>待辦項目

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
