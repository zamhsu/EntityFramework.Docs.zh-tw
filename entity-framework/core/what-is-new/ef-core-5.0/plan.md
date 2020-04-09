---
title: 實體框架核心 5.0 規劃
author: ajcvickers
ms.date: 01/14/2020
uid: core/what-is-new/ef-core-5.0/plan.md
ms.openlocfilehash: 8b4ca32524869019c04d5a4d4d55967f68181cd7
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80136226"
---
# <a name="plan-for-entity-framework-core-50"></a>實體框架核心 5.0 規劃

如[規劃過程中](../release-planning.md)所述,我們已經收集了利益相關者對 EF Core 5.0 版本的暫定計劃的投入。

> [!IMPORTANT] 
> 這個計劃仍在進行中。 這裡沒有什麼是承諾。 這個計劃是一個起點,隨著我們學習到更多,它將不斷演變。 一些目前未計劃為 5.0 的事情可能會被拉進去。 目前計劃為 5.0 的一些事情可能會被罰出場。

### <a name="version-number-and-release-date"></a>版本號和發佈日期。

EF Core 5.0 目前計劃與[.NET 5.0 同時](https://devblogs.microsoft.com/dotnet/introducing-net-5/)發佈。 選擇版本"5.0"與 .NET 5.0 對齊。

### <a name="supported-platforms"></a>支援的平台

EF Core 5.0 計劃基於[這些平臺與 .NET Core 的融合](https://devblogs.microsoft.com/dotnet/introducing-net-5/),在任何 .NET 5.0 平臺上運行。 就 .NET 標準和實際使用的 TFM 而言,這意味著什麼是 TBD。

EF 核心 5.0 將不會在 .NET 框架上運行。

### <a name="breaking-changes"></a>重大變更

EF Core 5.0 將包含一些重大更改,但這些變化將比 EF Core 3.0 的嚴重程度要小得多。 我們的目標是允許絕大多數應用程式在不中斷的情況下進行更新。

預計資料庫提供程式會有一些重大更改,尤其是在 TPT 支援方面。 但是,我們預計為 5.0 更新提供程式的工作將小於 3.0 更新所需的工作。

## <a name="themes"></a>主題

我們已經提取了幾個主要領域或主題,這些領域或主題將成為 EF Core 5.0 大型投資的基礎。

## <a name="many-to-many-navigation-properties-aka-skip-navigations"></a>多對多導航屬性(即"跳過導航")

首席開發人員:@smitpatel和@AndriySvyryd

由[#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)追蹤

T恤尺寸:L

狀態:正在進行

多對多是 GitHub 積壓工作[中請求最多的功能](https://github.com/aspnet/EntityFrameworkCore/issues/1368)(+407 票)。

對多對多關係的支援被跟蹤為[#10508。](https://github.com/aspnet/EntityFrameworkCore/issues/10508) 這可以分為三個主要領域:

* 跳過導航屬性。 這些允許模型用於查詢等,而無需引用基礎聯接表實體。 [(#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003))
* 屬性包實體類型。 這些允許將標準 CLR 類型`Dictionary`(例如 )用於實體實例,以便每個實體類型不需要顯式 CLR 類型。 (拉伸 5.0: [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914).)
* 糖易於配置多對多關係。 (拉伸為 5.0。

我們認為,對於那些需要多對多支援的人來說,最重要的阻止程式是不能在業務邏輯(如查詢)中使用"自然"關係,而不提及聯接表。 聯接表實體類型可能仍然存在,但不應妨礙業務邏輯。 這就是為什麼我們選擇解決 5.0 的跳過導航屬性的原因。

此時,許多對多的其他部分正在被作為 EF Core 5.0 的延伸目標進行追求。 這意味著他們目前不在5.0的計劃中,但如果進展順利,我們希望能吸引他們。

## <a name="table-per-type-tpt-inheritance-mapping"></a>依類型表 (TPT) 繼承映射

首席開發人員:@AndriySvyryd

由[#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266)追蹤

T恤尺寸:XL

狀態:正在進行

我們之所以做TPT,是因為它既是一個高度要求的功能(+254票;整體排名第三),而且它需要一些我們認為適合整體.NET 5計劃的基本性質的低級別更改。 我們預計這將導致資料庫提供程式的突發更改,儘管這些更改應比3.0所需的更改嚴重得多。

## <a name="filtered-include"></a>過濾器

首席開發人員:@maumar

由[#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833)追蹤

T恤尺寸:M

狀態:正在進行

過濾包含是一個高度請求的功能(+317票;總體排名第二),它不是大量工作,我們相信這將取消阻止或使當前需要模型級篩選器或更複雜的查詢的許多方案更容易。

## <a name="rationalize-totable-toquery-toview-fromsql-etc"></a>合理化到表,查詢,查看,從Sql等。

首席開發人員:@maumar和@smitpatel

由[#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270)追蹤

T恤尺寸:L

狀態:正在進行

我們在以前的版本中在支援原始 SQL、無鑰匙類型和相關領域方面取得了進展。 然而,在一切工作方式上,一切事物都存在差距和不一致之處。 5.0 的目標是修復這些,並創建定義、遷移和使用不同類型的實體及其關聯查詢和資料庫專案的良好體驗。 這還可能涉及對已編譯查詢 API 的更新。

請注意,此專案可能會導致一些應用程式級的中斷更改,因為我們目前擁有的某些功能過於寬鬆,因此可以快速導致人們陷入失敗坑。 我們最終可能會阻止某些功能,以及有關如何操作的指導。

## <a name="general-query-enhancements"></a>一般查詢增強功能

首席開發人員:@smitpatel和@maumar

按[5.0`area-query`里程碑中標記的問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)進行追蹤

T恤尺寸:XL

狀態:正在進行

為 EF Core 3.0 廣泛重寫了查詢轉換代碼。 因此,查詢代碼通常處於更加健壯的狀態。 對於 5.0,我們不打算在支援 TPT 和跳過導航屬性所需的外部進行重大查詢更改。 然而,要修復3.0大修遺留下來的一些技術債務,仍有許多工作要做。 我們還計劃修復許多 Bug 並實施小型增強功能,以進一步改善總體查詢體驗。

## <a name="migrations-and-deployment-experience"></a>遷移及部署經驗

首席開發人員:@bricelam

由[#19587](https://github.com/dotnet/efcore/issues/19587)追蹤

T恤尺寸:L

狀態:正在進行

目前,許多開發人員在應用程式啟動時遷移其資料庫。 這很容易,但不建議這樣做,因為:

* 多個線程/行程/伺服器可能會嘗試同時移移資料庫
* 應用程式可能會嘗試訪問不一致的狀態,而這種情況正在發生
* 通常,不應授予資料庫許可權以修改架構以進行應用程式執行
* 如果出現問題,很難恢復到乾淨狀態

我們希望在這裡提供更好的體驗,從而在部署時輕鬆遷移資料庫。 這應:

* Linux、Mac 和 Windows 上工作
* 在命令列上做好體驗
* 支援容器機制
* 使用常用的實際部署工具/串流
* 至少整合到視覺化工作室

其結果可能是 EF Core 的許多小改進(例如,在 SQLite 上更好的遷移),以及與其他團隊的指導和長期協作,以改善超越 EF 的端到端體驗。

## <a name="ef-core-platforms-experience"></a>EF 核心平台體驗 

首席開發人員:@roji和@bricelam

由[#19588](https://github.com/dotnet/efcore/issues/19588)追蹤

T恤尺寸:L

狀態:未啟動

我們在傳統的 MVC 類似 Web 應用程式中使用 EF Core 有很好的指導。 其他平臺和應用模型的指導要麼丟失,要麼過期。 對於 EF Core 5.0,我們計劃調查、改進和記錄使用 EF Core 的經驗:

* Blazor
* Xamarin,包括使用 AOT/連結器故事
* WinForms/WPF/WinUI 可能還有其他 U.I. frameworks

這可能是 EF Core 的許多小改進,以及與其他團隊的指導和長期協作,以改善超越 EF 的端到端體驗。

我們計劃研究的具體領域包括:

* 部署,包括使用 EF 工具(如移轉)的經驗
* 應用程式模型,包括 Xamarin 和 Blazor,可能還有其他
* SQLite 體驗,包括空間體驗和桌面重建
* AOT 和連結體驗
* 診斷整合,包括 perf 計數器

## <a name="performance"></a>效能

首席開發人員:@roji

按[5.0`area-perf`里程碑中標記的問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)進行追蹤

T恤尺寸:L

狀態:正在進行

對於 EF Core,我們計劃改進我們的性能基準套件,並針對運行時進行定向性能改進。 此外,我們還計劃完成在 3.0 發佈週期中原型設計的新 ADO.NET 批處理 API。 此外,在ADO.NET層,我們計劃對Npgsql提供程式進行其他性能改進。

作為這項工作的一部分,我們還計劃根據需要添加ADO.NET/EF核心性能計數器和其他診斷。

## <a name="architecturalcontributor-documentation"></a>架構/貢獻者文件

鉛文件記錄器:@ajcvickers

由[#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)追蹤

T恤尺寸:L

狀態:正在進行

這裡的想法是讓您更容易地瞭解 EF Core 的內部情況。 這對使用 EF Core 的任何人都很有用,但主要動機是讓外部人員更容易:

* 為 EF 核心代碼做出貢獻
* 建立資料庫提供者
* 產生其他擴充

## <a name="microsoftdatasqlite-documentation"></a>微軟.Data.Sqlite 文件

鉛文件記錄器:@bricelam

由[#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)追蹤

T恤尺寸:M

狀態:已完成。 新的文檔[是微軟文檔網站上的](https://docs.microsoft.com/dotnet/standard/data/sqlite/?tabs=netcore-cli)即時。

EF 團隊還擁有 Microsoft.Data.Sqlite ADO.NET供應商。 我們計劃作為 5.0 版本的一部分完整記錄此提供程式。

## <a name="general-documentation"></a>一般文件

鉛文件記錄器:@ajcvickers

按[5.0 里程碑中文件回購中的問題](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)進行追蹤

T恤尺寸:L

狀態:正在進行

我們已經在更新 3.0 和 3.1 版本的文檔。 我們還在致力於:
  * 對入門文檔進行大修,使其更易近人/更易於遵循
  * 重整文件,使尋找更容易並新增交叉參考
  * 新增額外資訊和說明
  * 更新範例並新增更多範例

## <a name="fixing-bugs"></a>修復錯誤

按[5.0`type-bug`里程碑中標記的問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)進行追蹤

開發人員: @roji @maumar @bricelam @smitpatel、 @AndriySvyryd、 、 、@ajcvickers

T恤尺寸:L

狀態:正在進行

在編寫本文時,我們有 135 個 bug 在 5.0 版本中被分對修復(已有 62 個 Bug 已修復),但與上面_的一般查詢增強部分_存在顯著重疊。

在 3.0 版本中,傳入率(最終作為里程碑中工作的問題)每月大約 23 個問題。 並非所有這些都需要固定在 5.0 中。 粗略估計,我們計劃在 5.0 個時間範圍內解決另外 150 個問題。

## <a name="small-enhancements"></a>小型增強功能

按[5.0`type-enhancement`里程碑中標記的問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)進行追蹤

開發人員: @roji @maumar @bricelam @smitpatel、 @AndriySvyryd、 、 、@ajcvickers

T恤尺寸:L

狀態:正在進行

除了上面概述的更大功能外,我們還計劃對 5.0 進行許多較小的改進,以修復"剪紙"。 請注意,上述較一般的主題也涵蓋了其中許多增強功能。

## <a name="below-the-line"></a>線下

按[標籤為`consider-for-next-release`](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)

這些是當前**未**為 5.0 版本安排的 Bug 修復和增強功能,但我們會根據上述工作的進度來考慮拉伸目標。

此外,在規劃時,我們總是考慮[投票最多的問題](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc)。 從發佈中削減這些問題總是痛苦的,但我們確實需要一個現實的計劃,以我認為我們擁有的資源。

## <a name="feedback"></a>意見反應

您對計劃的意見反應很重要。 若要指出問題的重要性，最佳方式是在 GitHub 上針對該問題投票 (大拇指)。 然後,此資料將饋入下一個版本[的規劃過程](../release-planning.md)。
