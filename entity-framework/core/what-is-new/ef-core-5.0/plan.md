---
title: 規劃 Entity Framework Core 5。0
description: 規劃 Entity Framework Core 5.0 的功能
author: ajcvickers
ms.date: 08/22/2020
uid: core/what-is-new/ef-core-5.0/plan
ms.openlocfilehash: ba56a5f3c79dacc25b3402be114c57067da49433
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129040"
---
# <a name="plan-for-entity-framework-core-50"></a>規劃 Entity Framework Core 5。0

> [!IMPORTANT]
> [現在已發行](xref:core/what-is-new/index)EF Core 5.0。 此頁面會保留為方案的歷程記錄。

如 [規劃](xref:core/what-is-new/release-planning)程式中所述，我們已將專案關係人的輸入收集到 EF Core 5.0 版的暫時計畫。

> [!IMPORTANT]
> 此計畫仍是進行中的工作。 這裡沒有任何承諾。 此計畫是將隨著我們深入瞭解而演進的起點。 目前未規劃5.0 的某些專案可能會被提取。 目前已規劃5.0 的某些專案可能會一度。

## <a name="general-information"></a>一般資訊

### <a name="version-number-and-release-date"></a>版本號碼和發行日期

目前已排程與 [.net 5.0](https://devblogs.microsoft.com/dotnet/introducing-net-5/)同時發行 EF Core 5.0。 選擇的 "5.0" 版本與 .NET 5.0 一致。

### <a name="supported-platforms"></a>支援的平台

EF Core 5.0 計畫在任何 .NET Standard 2.1 平臺上執行，包括 .NET 5.0。 這是較常見的 .NET 將 [平臺聚合至 .Net Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/)的一部分。

EF Core 5.0 不會在 .NET Framework 上執行。

### <a name="breaking-changes"></a>重大變更

EF Core 5.0 將包含一些 [重大變更](xref:core/what-is-new/ef-core-5.0/breaking-changes)，但在 EF Core 3.0 的情況下，這些變更將會比以往更低。 我們的目標是要讓大部分的應用程式在不中斷的情況下進行更新。

資料庫提供者應該會有一些重大變更，尤其是針對 TPT 支援。 不過，我們預期針對5.0 更新提供者的工作，將會少於更新3.0 所需的時間。

## <a name="themes"></a>佈景主題

我們已將一些主要區域或主題解壓縮，以構成 EF Core 5.0 中的大型投資基礎。

## <a name="fully-transparent-many-to-many-mapping-by-convention"></a>依慣例完全透明的多對多對應

領導開發人員： @smitpatel 、 @AndriySvyryd 和 @lajones

追蹤 [#10508](https://github.com/dotnet/efcore/issues/10508)

T 恤尺寸： L

狀態：完成

「多對多」是 [最常要求的功能](https://github.com/dotnet/efcore/issues/1368) (~ 506 投票) 在 GitHub 待處理專案上。

多對多關聯性的支援分成三個主要區域：

* 略過導覽屬性--下一個主題所涵蓋的內容。
* 屬性包實體類型。 這些可讓標準的 CLR 型別 (例如 `Dictionary`) 用於實體實例，因此每個實體類型都不需要明確的 clr 型別。 由 [#9914](https://github.com/dotnet/efcore/issues/9914)追蹤。
* 方便設定多對多關聯性的方便。

除了略過流覽支援以外，我們現在會將多對多的其他區域提取至 EF Core 5.0，以提供完整的體驗。

## <a name="many-to-many-navigation-properties-aka-skip-navigations"></a>多對多導覽屬性 (. k. a "skip 導覽" ) 

領導開發人員： @smitpatel 和 @AndriySvyryd

追蹤 [#19003](https://github.com/dotnet/efcore/issues/19003)

T 恤尺寸： L

狀態：完成

如第一個主題所述，多對多支援有許多方面。
本主題專門追蹤略過導覽的使用。
我們相信，需要多對多支援的最重要封鎖程式，無法在商務邏輯（例如查詢）中使用「自然」關聯性，而不需要參考聯結資料表。
聯結資料表實體類型可能仍存在，但不應該以商務邏輯的方式取得。

## <a name="table-per-type-tpt-inheritance-mapping"></a>每一類型的資料表 (TPT) 繼承對應

首席開發人員： @AndriySvyryd 和 @smitpatel

追蹤 [#2266](https://github.com/dotnet/efcore/issues/2266)

T 恤尺寸： XL

狀態：完成

我們正在進行 TPT，因為它是高度要求的功能 (~ 289 的投票;第三個整體) ，因為它需要一些低層級的變更，因此我們覺得適用于整體 .NET 5 計畫的基礎本質。 我們預期這會造成資料庫提供者的重大變更，但這應該比3.0 所需的變更更不嚴重。

## <a name="filtered-include"></a>篩選的包含

首席開發人員： @maumar

追蹤 [#1833](https://github.com/dotnet/efcore/issues/1833)

T 恤尺寸： M

狀態：完成

篩選包含高度要求的功能 (~ 376 的投票;第2個整體) 不是大量的工作，而且我們相信您會在目前需要模型層級篩選或更複雜查詢的情況下，解除封鎖或簡化許多案例。

## <a name="split-include"></a>分割包含

首席開發人員： @smitpatel

追蹤 [#20892](https://github.com/dotnet/efcore/issues/20892)

T 恤尺寸： L

狀態：完成

EF Core 3.0 變更了預設行為，以針對指定的 LINQ 查詢建立單一 SQL 查詢。
這會對使用包含多個集合的查詢造成大型效能衰退。

在 EF Core 5.0 中，我們會保留新的預設行為。
不過，EF Core 5.0 現在會允許產生多個集合的查詢，其中包含單一查詢導致效能不佳的情況。

## <a name="required-one-to-one-dependents"></a>需要一對一相依項

領導開發人員： @AndriySvyryd 和 @smitpatel

追蹤 [#12100](https://github.com/dotnet/efcore/issues/12100)

T 恤尺寸： M

狀態：完成

在 EF Core 3.0 中，包括擁有的類型在內的所有相依性都是選擇性的 (例如，Person 可以是 null) 。 在 EF Core 5.0 中，可以視需要設定相依項。

## <a name="rationalize-totable-toquery-toview-fromsql-etc"></a>將 ToTable、ToQuery、ToView、FromSql 等合理化

領導開發人員： @AndriySvyryd 和 @smitpatel

追蹤 [#17270](https://github.com/dotnet/efcore/issues/17270)

T 恤尺寸： L

狀態：完成

在舊版中，我們已在支援原始 SQL、無索引鍵類型和相關區域的過程中進行了進展。 不過，所有專案整體運作的方式都有差距和不一致的情況。 5.0 的目標是要修正這些問題，並建立良好的體驗來定義、遷移和使用不同類型的實體及其相關聯的查詢和資料庫成品。 這也可能牽涉到已編譯查詢 API 的更新。

請注意，此專案可能會導致一些應用層級的重大變更，因為我們目前所擁有的一些功能過於寬鬆，因此可以快速地讓人們進入失敗的 pits。 我們最後可能會封鎖這其中一些功能，以及應如何取代的指引。

## <a name="general-query-enhancements"></a>一般查詢增強功能

領導開發人員： @smitpatel 和 @maumar

由[ `area-query` 5.0 里程碑中標記](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)為的問題追蹤

T 恤尺寸： XL

狀態：完成

已針對 EF Core 3.0 大量重寫查詢轉譯程式碼。 由於這個原因，查詢程式碼通常會以更健全的狀態進行。 針對5.0，我們不打算在支援 TPT 和略過導覽屬性所需的外部進行主要查詢變更。 不過，仍有很多工作需要修正從3.0 的檢修中留下的一些技術債務。 我們也計畫修正許多 bug，並執行小規模的增強功能，進一步改善整體查詢體驗。

## <a name="migrations-and-deployment-experience"></a>遷移和部署體驗

首席開發人員： @bricelam

追蹤 [#19587](https://github.com/dotnet/efcore/issues/19587)

T 恤尺寸： L

狀態：限域/完成

範圍： [遷移組合功能](https://github.com/dotnet/efcore/issues/19693) 已延後，直到 EF Core 5.0 版本為止。 不過， [與遷移相關](https://github.com/dotnet/efcore/issues/19587#issuecomment-668794460) 的數個其他目標改進將包含在 EF Core 5。0

目前，許多開發人員會在應用程式啟動時遷移其資料庫。 這很簡單，但不建議使用，因為：

* 多個執行緒/進程/伺服器可能會嘗試同時遷移資料庫
* 當發生這種情況時，應用程式可能會嘗試存取不一致的狀態
* 通常不應針對應用程式執行，授與修改架構的資料庫許可權
* 如果發生錯誤，則很難還原為乾淨狀態

我們想要在此提供更好的體驗，讓您在部署時可以輕鬆地遷移資料庫。 這應該是：

* 在 Linux、Mac 和 Windows 上工作
* 在命令列上提供絕佳的體驗
* 使用容器的支援案例
* 使用常用的真實世界部署工具/流程
* 至少整合到 Visual Studio

結果可能是 EF Core 的許多小改良 (例如，在 SQLite) 上進行更佳的遷移，以及與其他小組的指引和長期共同作業，以改善超越 EF 的端對端體驗。

## <a name="ef-core-platforms-experience"></a>EF Core 平臺體驗

領導開發人員： @roji 和 @bricelam

追蹤 [#19588](https://github.com/dotnet/efcore/issues/19588)

T 恤尺寸： L

狀態：範圍/完成

範圍：針對 Blazor、Xamarin、WinForms 和 WPF 發佈平臺指引和範例。 現在已針對 EF Core 6.0 規劃 Xamarin 和其他 AOT/連結器工作。

對於在傳統的 MVC web 應用程式中使用 EF Core，我們有良好的指引。 其他平臺和應用程式模型的指引已遺失或已過期。 針對 EF Core 5.0，我們計畫調查、改進和記錄搭配使用 EF Core 的體驗：

* Blazor
* Xamarin，包括使用 AOT/連結器案例
* WinForms/WPF/WinUI，可能還有其他 U.I。 frameworks

這可能是 EF Core 中的許多小改良，以及與其他小組的指引和長期共同作業，以改善超越 EF 的端對端體驗。

我們打算查看的特定區域包括：

* 部署，包括使用 EF 工具（例如遷移）的體驗
* 應用程式模型（包括 Xamarin 和 Blazor），也可能是其他模型
* SQLite 體驗，包括空間體驗和資料表重建
* AOT 和連結體驗
* 診斷整合，包括效能計數器

## <a name="performance"></a>效能

首席開發人員： @roji

由[ `area-perf` 5.0 里程碑中標記](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)為的問題追蹤

T 恤尺寸： L

狀態：限域/完成

範圍： Npgsql 提供者的主要效能改進已完成。 現在已針對 EF Core 6.0 規劃其他效能工作。

針對 EF Core，我們計畫改善效能基準測試套件，並對執行時間進行導向的效能改進。 此外，我們計畫在3.0 發行週期期間，完成新的 ADO.NET 批次處理 API。 此外，在 ADO.NET 層，我們會為 Npgsql 提供者規劃額外的效能改進。

在這項工作中，我們也計畫在適當的情況下新增 ADO.NET/EF 核心效能計數器和其他診斷。

## <a name="architecturalcontributor-documentation"></a>架構/參與者檔

主管文檔： @ajcvickers

追蹤 [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)

T 恤尺寸： L

狀態：剪下

這裡的目的是要讓您更輕鬆地瞭解 EF Core 內部的進展。 這對任何使用 EF Core 的人都很有用，但主要動機是讓外部人員更輕鬆地：

* 參與 EF Core 程式碼
* 建立資料庫提供者
* 建立其他延伸模組

更新：很可惜，此計畫太確保建構完善。
我們仍然認為這很重要，但不幸的是，它不會 EF Core 5.0。

## <a name="microsoftdatasqlite-documentation"></a>Microsoft. Sqlite 檔

主管文檔： @bricelam

追蹤 [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)

T 恤尺寸： M

狀態：已完成。 新的檔位於 [Microsoft 檔網站上](/dotnet/standard/data/sqlite/?tabs=netcore-cli)。

EF 小組也擁有 ADO.NET 提供者。 我們計畫在5.0 版中完整記錄此提供者。

## <a name="general-documentation"></a>一般檔

主管文檔： @ajcvickers

由[5.0 里程碑的](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)檔存放庫中的問題所追蹤

T 恤尺寸： L

狀態：進行中

我們已在更新3.0 和3.1 版的檔集。 我們也在努力：

* 深入瞭解使用者入門檔，使其更平易近人/更容易遵循
* 重新組織檔，讓您更容易尋找及新增交叉參考
* 新增更多詳細資料並對現有檔進行說明
* 更新範例和新增更多範例

## <a name="fixing-bugs"></a>修正 bug

由[ `type-bug` 5.0 里程碑中標記](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)為的問題追蹤

開發人員： @roji 、 @maumar 、 @bricelam 、 @smitpatel 、 @AndriySvyryd 、 @ajcvickers

T 恤尺寸： L

狀態：進行中

在撰寫本文時，我們會將135的 bug 分級為在5.0 版 (中修正（62已) 修正），但是上述的 _一般查詢增強功能_ 一節會有顯著的重迭。

傳入的速率 (的問題最後是里程碑的工作) 在3.0 版本的過程中，每個月大約23個問題。 並非所有這些都必須在5.0 中修正。 大致上，我們打算修正5.0 時間範圍中的額外150問題。

## <a name="small-enhancements"></a>小型增強功能

由[ `type-enhancement` 5.0 里程碑中標記](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)為的問題追蹤

開發人員： @roji 、 @maumar 、 @bricelam 、 @smitpatel 、 @AndriySvyryd 、 @ajcvickers

T 恤尺寸： L

狀態：完成

除了上面所述的更大功能之外，我們也為5.0 排定了許多較小的改進，以修正「紙張-剪下」。 請注意，上述的許多增強功能也都包含在上面所述的一般主題中。

## <a name="below-the-line"></a>以下-行

由[標記為的 `consider-for-next-release` 問題](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)追蹤

這些是目前 **尚未** 針對5.0 版本排程的 bug 修正和增強功能，但我們將根據上述工作的進度來查看延展目標。

此外，我們在規劃時，一律會考慮 [最投票的問題](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) 。 從版本中剪下這些問題一直都是很麻煩的，但我們的確需要針對我們所擁有的資源進行實際的規劃。

## <a name="suggestions"></a>建議

您對計劃的意見反應很重要。 若要指出問題的重要性，最佳方式是在 GitHub 上針對該問題投票 (大拇指)。 然後，此資料會送入下一版的 [規劃](xref:core/what-is-new/release-planning) 程式。
