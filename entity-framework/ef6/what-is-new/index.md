---
title: 新功能 - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 41d1f86b-ce66-4bf2-8963-48514406fb4c
caps.latest.revision: 3
ms.openlocfilehash: dba6403fa341e1abfe8da488a19cf8520e3ea574
ms.sourcegitcommit: 390f3a37bc55105ed7cc5b0e0925b7f9c9e80ba6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2018
ms.locfileid: "37914172"
---
# <a name="whats-new-in-ef6"></a>EF6 的新功能

強烈建議您使用 Entity Framework 的最新發行版本，以確保您取得最新的功能和最高的穩定性。
不過，我們了解您可能需要使用先前的版本，或者可能想要體驗最新發行前版本中的新改善。
若要安裝特定版本的 EF，請參閱[取得 Entity Framework](~/ef6/fundamentals/install.md)。

此頁面記每個新版本所包含的功能。

## <a name="recent-releases"></a>最新發行

### <a name="ef-tools-update-in-visual-studio-2017-157"></a>Visual Studio 2017 15.7 中的 EF 工具更新

在 2018 年 5 月，我們在 Visual Studio 2017 15.7 中發行了 EF Tools 的更新版本。
其中包含一些常見難題的改善：

- 修正使用者介面協助工具的數個 Bug
- 從現有資料庫產生模型時，SQL Server 效能降低的因應措施 [#4](https://github.com/aspnet/entityframework6/issues/4)
- 支援為 SQL Server 上的大型模型更新模型[#185](https://github.com/aspnet/EntityFramework6/issues/185)

EF Tools 這個新版本中的另一項改進，是在新專案中建立模型時，安裝 EF 6.2 執行階段。 利用舊版的 Visual Studio，就可以安裝 NuGet 套件的對應版本來使用 EF 6.2 執行階段 (以及 EF 的任何過去版本)。

### <a name="ef-62-runtime"></a>EF 6.2 執行階段

EF 6.2 執行階段已於 2017 年 10 月發行至 NuGet。
絕大部分歸功於開放原始碼參與者社群的努力，EF 6.2 包含許多 [Bug 修正](https://github.com/aspnet/entityframework6/issues?utf8=%E2%9C%93&q=is%3Aissue%20milestone%3A6.2.0%20is%3Aclosed%20label%3Aclosed-fixed%20-label%3Aarea-tools%20label%3Atype-bug)和[產品增強功能](https://github.com/aspnet/entityframework6/issues?utf8=%E2%9C%93&q=is%3Aissue%20milestone%3A6.2.0%20is%3Aclosed%20label%3Aclosed-fixed%20-label%3Aarea-tools%20label%3Atype-enhancement%20)。

以下是影響 EF 6.2 執行階段之最重要變更的簡短清單：

- 從永續性快取載入完成的 Code First 模型來減少啟動時間 [#275](https://github.com/aspnet/EntityFramework6/issues/275)
- Fluent API 用來定義索引 [#274](https://github.com/aspnet/EntityFramework6/issues/274)
- DbFunctions.Like() 用來讓您撰寫在 SQL 中轉譯為 LIKE 的 LINQ 查詢 [#241](https://github.com/aspnet/EntityFramework6/issues/241)
- Migrate.exe 現在支援 -script 選項 [#240](https://github.com/aspnet/EntityFramework6/issues/240)
- EF6 現在可以使用 SQL Server 中的序列所產生的索引鍵值 [#165](https://github.com/aspnet/EntityFramework6/issues/165)
- SQL Azure 執行策略之暫時性錯誤的更新清單 [#83](https://github.com/aspnet/EntityFramework6/issues/83)
- Bug：重試查詢或 SQL 命令失敗，並顯示「SqlParameter 已由另一個 SqlParameterCollection 所包含」 [#81](https://github.com/aspnet/EntityFramework6/issues/81)
- Bug：DbQuery.ToString() 評估經常在偵錯工具中逾時 [#73](https://github.com/aspnet/EntityFramework6/issues/73)

## <a name="future-releases"></a>未來版本

如需 EF6 未來版本的資訊，請查看我們的[藍圖](roadmap.md)。

## <a name="past-releases"></a>過去版本

[過去版本](past-releases.md)頁面包含所有舊版 EF 與每一版所引進之主要功能的封存。
