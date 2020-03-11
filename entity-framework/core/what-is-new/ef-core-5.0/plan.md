---
title: 規劃 Entity Framework Core 5。0
author: ajcvickers
ms.date: 01/14/2020
uid: core/what-is-new/ef-core-5.0/plan.md
ms.openlocfilehash: c5b7300c61c2f668b6f9393ae51bf9ebddf330a7
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417873"
---
# <a name="plan-for-entity-framework-core-50"></a>規劃 Entity Framework Core 5。0

如[規劃](../release-planning.md)程式中所述，我們已將專案關係人的輸入收集到 EF Core 5.0 版本的暫時計畫。

> [!IMPORTANT] 
> 此計畫仍然是進行中的工作。 這裡沒有任何承諾。 此計畫是一個起點，會隨著我們深入瞭解而演變。 目前未計畫5.0 的某些專案可能會提取。 目前規劃5.0 的某些專案可能會一度。

### <a name="version-number-and-release-date"></a>版本號碼和發行日期。

EF Core 5.0 目前排定與[.net 5.0](https://devblogs.microsoft.com/dotnet/introducing-net-5/)同時發行。 已選擇版本 "5.0" 以配合 .NET 5.0。

### <a name="supported-platforms"></a>支援的平台

EF Core 5.0 計畫在任何 .NET 5.0 平臺上執行，其以[這些平臺與 .Net Core 的交集為](https://devblogs.microsoft.com/dotnet/introducing-net-5/)基礎。 這代表 .NET Standard 的意義，而實際使用的 TFM 仍然是 TBD。

EF Core 5.0 不會在 .NET Framework 上執行。

### <a name="breaking-changes"></a>重大變更

EF Core 5.0 將包含一些重大變更，但它們的嚴重程度比 EF Core 3.0 的情況低。 我們的目標是讓大部分的應用程式都能在不中斷的情況下進行更新。

資料庫提供者應該會有一些重大變更，特別是關於 TPT 支援。 不過，我們預期更新5.0 提供者的工作將會小於更新3.0 所需的時間。

## <a name="themes"></a>主題

我們已解壓縮幾個主要區域或主題，這會構成 EF Core 5.0 中大型投資的基礎。

## <a name="many-to-many-navigation-properties-aka-skip-navigations"></a>多對多導覽屬性（即「略過流覽」）

潛在客戶開發人員： @smitpatel 和 @AndriySvyryd

由[#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)追蹤

T 恤尺寸： L

狀態：進行中

「多對多」是 GitHub 待處理專案上[最常要求的功能](https://github.com/aspnet/EntityFrameworkCore/issues/1368)（~ 407 投票）。

整體支援多對多關聯性會以[#10508](https://github.com/aspnet/EntityFrameworkCore/issues/10508)的方式進行追蹤。 這可分為三個主要區域：

* 略過導覽屬性。 這些可讓模型用於查詢等，而不需要參考基礎聯結資料表實體。 （[#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)）
* 屬性包實體類型。 這些專案允許將標準 CLR 型別（例如 `Dictionary`）用於實體實例，因此每個實體型別都不需要明確的 CLR 型別。 （5.0 的 Stretch： [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914)）。
* 方便設定多對多關聯性的便利。 （5.0 的延展）。

我們相信，想要多對多支援的最重要封鎖程式，在商務邏輯（例如查詢）中無法使用「自然」關聯性，而不需要參考聯結資料表。 聯結資料表實體類型可能仍然存在，但不應該取得商務邏輯的方式。 這就是我們選擇解決 [略過 5.0] 的流覽屬性的原因。

此時，多對多的其他部分會被視為 EF Core 5.0 的延展目標。 這表示它們目前不在計畫中5.0，但如果一切順利，我們希望能夠將其提取到。

## <a name="table-per-type-tpt-inheritance-mapping"></a>每一類型的資料表（TPT）繼承對應

首席開發人員： @AndriySvyryd

由[#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266)追蹤

T 恤尺寸： XL

狀態：未啟動

我們正在進行 TPT，因為這兩者都是高度要求的功能（~ 254 的投票; 第三個），因為它需要一些低層級的變更，我們覺得這適用于整體 .NET 5 計畫的基本本質。 我們預期這會導致資料庫提供者的中斷性變更，但這應該比3.0 所需的變更少很多。

## <a name="filtered-include"></a>篩選的包含

首席開發人員： @maumar

由[#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833)追蹤

T 恤尺寸： M

狀態：未啟動

篩選的「包含」是一種高度要求的功能（大約317的投票; 第二個整體），這不是大量的工作，因此我們認為會解除封鎖或更輕鬆地進行目前需要模型層級篩選或更複雜查詢的許多案例。

## <a name="rationalize-totable-toquery-toview-fromsql-etc"></a>合理化 ToTable、ToQuery、ToView、FromSql 等。

潛在客戶開發人員： @maumar 和 @smitpatel

由[#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270)追蹤

T 恤尺寸： L

狀態：未啟動

我們已在舊版中進行了支援原始 SQL、無索引鍵類型和相關區域的進度。 不過，所有專案的整體運作方式都有差距和不一致的情況。 5\.0 的目標是要修正這些問題，並建立定義、遷移和使用不同類型實體及其相關聯查詢和資料庫成品的絕佳體驗。 這也可能牽涉到已編譯查詢 API 的更新。

請注意，這個專案可能會導致某些應用層級的中斷性變更，因為我們目前擁有的一些功能過於寬鬆，所以可能很快就會導致使用者 pits 失敗。 我們可能會同時封鎖這部分功能，並提供有關該怎麼做的指引。

## <a name="general-query-enhancements"></a>一般查詢增強功能

潛在客戶開發人員： @smitpatel 和 @maumar

由[5.0 里程碑中標示為 `area-query` 的問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)所追蹤

T 恤尺寸： XL

狀態：進行中

已針對 EF Core 3.0 廣泛重寫查詢轉譯程式碼。 這種情況下，查詢程式碼通常會有更健全的狀態。 針對5.0，我們不打算在支援 TPT 和略過導覽屬性所需的範圍內進行主要查詢變更。 不過，仍然需要進行一些工作，以修正3.0 檢修中剩餘的技術債務。 我們也計畫修正許多 bug，並執行小型的增強功能，以進一步改善整體的查詢體驗。

## <a name="migrations-and-deployment-experience"></a>遷移與部署體驗

潛在客戶開發人員： @bricelam

由[#19587](https://github.com/dotnet/efcore/issues/19587)追蹤

T 恤尺寸： L

狀態：進行中

目前，許多開發人員會在應用程式啟動時遷移其資料庫。 這很簡單，但不建議這麼做，因為：

* 多個執行緒/進程/伺服器可能會嘗試同時遷移資料庫
* 應用程式可能會在發生這種情況時，嘗試存取不一致的狀態
* 通常不應授與修改架構的資料庫許可權以執行應用程式
* 如果發生錯誤，很難還原為「清除」狀態

我們想要在此提供更好的體驗，讓您可以輕鬆地在部署時遷移資料庫。 這應該：

* 在 Linux、Mac 和 Windows 上工作
* 在命令列上是不錯的體驗
* 容器的支援案例
* 使用常用的真實世界部署工具/流程
* 至少整合到 Visual Studio

結果可能是 EF Core 中的許多小改良（例如，SQLite 上較佳的遷移），並提供與其他小組的指引和更長期的共同作業，以改善超越 EF 的端對端體驗。

## <a name="ef-core-platforms-experience"></a>EF Core 平臺體驗 

潛在客戶開發人員： @roji 和 @bricelam

由[#19588](https://github.com/dotnet/efcore/issues/19588)追蹤

T 恤尺寸： L

狀態：未啟動

我們有在傳統的 MVC web 應用程式中使用 EF Core 的絕佳指引。 其他平臺和應用程式模型的指引可能遺失或過期。 針對 EF Core 5.0，我們計畫調查、改善及記載搭配 EF Core 使用的體驗：

* Blazor
* Xamarin，包括使用 AOT/連結器案例
* WinForms/WPF/WinUI，可能還有其他 U.I。 frameworks

這可能是 EF Core 中的許多小改進，以及與其他小組的指引和更長期的共同作業，以改善超越 EF 的端對端體驗。

我們打算查看的特定領域包括：

* 部署，包括使用 EF 工具（例如）進行遷移的經驗
* 應用程式模型，包括 Xamarin 和 Blazor，還有其他可能
* SQLite 體驗，包括空間體驗和資料表重建
* AOT 和連結體驗
* 診斷整合，包括效能計數器

## <a name="performance"></a>效能

首席開發人員： @roji

由[5.0 里程碑中標示為 `area-perf` 的問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)所追蹤

T 恤尺寸： L

狀態：進行中

針對 EF Core，我們計畫改善我們的效能基準測試套件，並對執行時間進行有引導的效能改進。 此外，我們計畫在3.0 發行週期內完成新的 ADO.NET 批次處理 API，這是原型。 此外，在 ADO.NET 層，我們也為 Npgsql 提供者規劃了額外的效能改進。

在這項工作中，我們也計畫在適當的情況下新增 ADO.NET/EF 核心效能計數器和其他診斷。

## <a name="architecturalcontributor-documentation"></a>架構/參與者檔

潛在客戶文檔： @ajcvickers

由[#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)追蹤

T 恤尺寸： L

狀態：未啟動

這裡的概念是要讓您更輕鬆地瞭解 EF Core 內部的狀況。 這對任何使用 EF Core 的人來說都很有用，但主要動機是讓外部人員更容易：

* 參與 EF Core 程式碼
* 建立資料庫提供者
* 建立其他擴充功能

## <a name="microsoftdatasqlite-documentation"></a>Microsoft. Sqlite 檔

潛在客戶文檔： @bricelam

由[#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)追蹤

T 恤尺寸： M

狀態：已完成。 新的檔已[上線于 Microsoft 檔網站](https://docs.microsoft.com/dotnet/standard/data/sqlite/?tabs=netcore-cli)。

EF 小組也擁有 ADO.NET 提供者。 我們計畫將此提供者完整記錄為5.0 版本的一部分。

## <a name="general-documentation"></a>一般檔

潛在客戶文檔： @ajcvickers

[5.0 里程碑的](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)檔存放庫中的問題追蹤

T 恤尺寸： L

狀態：進行中

我們已在更新3.0 和3.1 版本的檔。 我們也在努力：
  * 深入瞭解開始使用檔，使其更平易近人/更容易遵循
  * 重新組織檔，使其更容易尋找及新增交互參考
  * 新增更多詳細資料和對現有檔的說明
  * 更新範例並新增更多範例

## <a name="fixing-bugs"></a>修正 bug

由[5.0 里程碑中標示為 `type-bug` 的問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)所追蹤

開發人員： @roji、@maumar、@bricelam、@smitpatel、@AndriySvyryd、@ajcvickers

T 恤尺寸： L

狀態：進行中

在撰寫本文時，我們會在5.0 版本（已修正62）中修正 135 bug，但與上面的_一般查詢增強功能_相比，有相當大的重迭。

傳入的速率（最後在里程碑中運作的問題）是3.0 發行期間每月大約23個問題。 並非所有這些都必須在5.0 中修正。 我們打算在5.0 時間範圍內修正其他150問題，這是粗略的估計。

## <a name="small-enhancements"></a>小型增強功能

由[5.0 里程碑中標示為 `type-enhancement` 的問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)所追蹤

開發人員： @roji、@maumar、@bricelam、@smitpatel、@AndriySvyryd、@ajcvickers

T 恤尺寸： L

狀態：進行中

除了上面所述的更大功能之外，我們也針對5.0 排定了許多較小的改進，以修正「紙張切割」。 請注意，上述的許多增強功能也都涵蓋在上面所述的一般主題。

## <a name="below-the-line"></a>底下-行

由[標示為 `consider-for-next-release`的問題](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)所追蹤

這些是目前**未**針對5.0 版本排程的錯誤修正和增強功能，但我們會根據上述工作的進度，查看是否為延展目標。

此外，我們在規劃時，一律會考慮[最投票的問題](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc)。 從版本中去除這些問題總是很麻煩，但我們對我們所擁有的資源需要有實際的計畫。

## <a name="feedback"></a>意見反應

您對計劃的意見反應很重要。 若要指出問題的重要性，最佳方式是在 GitHub 上針對該問題投票 (大拇指)。 此資料接著會饋送至下一版的[規劃](../release-planning.md)程式。
