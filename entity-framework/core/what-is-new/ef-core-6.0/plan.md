---
title: 規劃 Entity Framework Core 6。0
description: 規劃 EF Core 6.0 的主題和功能
author: ajcvickers
ms.date: 01/12/2021
uid: core/what-is-new/ef-core-6.0/plan
ms.openlocfilehash: 612461bc6ad30778baa5c6d10dda5cabac91dcb2
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129725"
---
# <a name="plan-for-entity-framework-core-60"></a>規劃 Entity Framework Core 6。0

如 [規劃](xref:core/what-is-new/release-planning)程式中所述，我們已將專案關係人的輸入收集至 Entity Framework Core (EF Core) 6.0 版的方案。

與舊版不同的是，此計畫不會嘗試涵蓋6.0 版本的所有工作。 相反地，它會指出我們要在此版本中投資的位置和方式，但在我們收集意見反應並學習發行時，您可以彈性地調整範圍或提取新的工作。

> [!IMPORTANT]
> 此計畫不是承諾用量。 它是將隨著我們深入瞭解而演進的起點。 目前未規劃6.0 的某些專案可能會被提取。 目前已規劃6.0 的某些專案可能會一度。

## <a name="general-information"></a>一般資訊

### <a name="version-number-and-release-date"></a>版本號碼和發行日期

EF Core 6.0 是 EF Core 5.0 之後的下一個版本，而且目前排定在11月2021與 .NET 6 同時發行。

### <a name="supported-platforms"></a>支援的平台

EF Core 6.0 目前以 .NET 5 為目標。 這可能會隨著我們附近的版本而更新為 .NET 6。 EF Core 6.0 不會以任何 .NET Standard 版本為目標;如需詳細資訊，請參閱 [.NET Standard 的未來](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/)。

EF Core 6.0 不會在 .NET Framework 上執行。

EF Core 6.0 將與 .NET 6 保持一致，以 [ (LTS) 版本的長期支援](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。

### <a name="breaking-changes"></a>重大變更

EF Core 6.0 將包含少量的 [重大變更](xref:core/what-is-new/ef-core-6.0/breaking-changes) ，因為我們會持續發展 EF Core 和 .net 平臺。 我們的目標是要讓大部分的應用程式在不中斷的情況下進行更新。

## <a name="themes"></a>佈景主題

下列領域將構成 EF Core 6.0 中的大型投資基礎。

## <a name="highly-requested-features"></a>高度要求的功能

同樣地，在 [規劃過程](xref:core/what-is-new/release-planning) 中，主要的輸入是來自 [ 👍 GitHub 上功能的投票 () ](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc)。 針對 EF Core 6.0，我們計畫使用下列高度要求的功能：

### <a name="sql-server-temporal-tables"></a>SQL Server 時態表

追蹤 [#4693](https://github.com/dotnet/efcore/issues/4693)

狀態：未啟動

T 恤尺寸：大型

時態表支援在 _任何時間點_，查詢儲存在資料表中的資料，而不只是一般資料表所儲存的最新資料。 EF Core 6.0 將允許透過遷移來建立時態表，以及允許透過 LINQ 查詢來存取資料。

這項工作一開始的範圍是此 [問題的描述](https://github.com/dotnet/efcore/issues/4693#issuecomment-625048974)。 我們可能會根據發行期間的意見反應，提取其他支援。

### <a name="json-columns"></a>JSON 資料行

追蹤 [#4021](https://github.com/dotnet/efcore/issues/4021)

狀態：未啟動

T 恤尺寸：中

這項功能會介紹可由任何資料庫提供者所執行之 JSON 支援的常見機制和模式。 我們將與此社區合作，將 [Npgsql](https://github.com/npgsql/efcore.pg) 和 [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)的現有執行保持一致，並同時新增 SQL Server 和 SQLite 的支援。

### <a name="columnattributeorder"></a>ColumnAttribute。訂單

追蹤 [#10059](https://github.com/dotnet/efcore/issues/10059)

狀態：未啟動

T 恤尺寸：小型

使用遷移或 **建立資料表** 時，這項功能將允許任意順序的資料行 `EnsureCreated` 。 請注意，變更現有資料表中的資料行順序需要重建資料表，但這不是我們計畫在任何 EF Core 版本中支援的。

## <a name="performance"></a>效能

雖然 EF Core 的速度通常比 EF6 還快，但仍有一些可能會大幅改善效能的區域。 我們計畫在 EF Core 6.0 中處理數個區域，同時改善我們的效能基礎結構和測試。

本主題將牽涉到許多反復調查，以通知我們將資源放在哪裡。 我們計畫開始：

### <a name="performance-infrastructure-and-new-tests"></a>效能基礎結構和新的測試

狀態：未啟動

T 恤尺寸：中

EF Core 程式碼基底已包含一組每日執行的效能基準測試。 針對6.0，我們計畫改善這些測試的基礎結構，以及加入新的測試。 我們也會分析主線效能案例，並修正找到的任何低度水果。

### <a name="compiled-models"></a>編譯的模型

追蹤 [#1906](https://github.com/dotnet/efcore/issues/1906)

狀態：未啟動

T 恤尺寸： X-大型

編譯的模型可讓您產生已編譯的 EF 模型形式。 這會提供更好的啟動效能，以及存取模型時的效能通常較佳。

### <a name="techempower-fortunes"></a>TechEmpower Fortunes

追蹤 [#23611](https://github.com/dotnet/efcore/issues/23611)

狀態：未啟動

T 恤尺寸： X-大型

我們已針對于 postgresql 資料庫在 .NET 上執行業界標準的 [TechEmpower 基準](https://www.techempower.com/benchmarks/) 檢驗，多年來。 [Fortunes 基準測試](https://www.techempower.com/benchmarks/#section=data-r19&hw=ph&test=fortune)與 EF 案例特別相關。 我們有多個此基準測試的變化，包括：

- 直接使用 ADO.NET 的執行。 這是此處所列三個最快的執行。
- 使用 [Dapper](https://www.nuget.org/packages/Dapper/)的執行。 這比直接使用 ADO.NET 慢，但仍然快速。
- 使用 EF Core 的實作為。 這是目前最慢的三個執行。

EF Core 6.0 的目標是要讓 EF Core 效能符合 TechEmpower Fortunes 基準測試上的 Dapper。  (這是很大的挑戰，但我們最好盡可能找出最接近的外觀。 ) 

### <a name="linkeraot"></a>連結器/AOT

追蹤 [#10963](https://github.com/dotnet/efcore/issues/10963)

狀態：未啟動

T 恤尺寸：中

EF Core 會執行大量的執行時間程式碼產生。 這對相依于連結器樹狀結構（例如 Xamarin 和 Blazor）的應用程式模型，以及不允許動態編譯的平臺（例如 iOS）來說相當困難。 我們將繼續在此空間中調查 EF Core 6.0 的一部分，並以我們可以的方式進行目標改進。 但是，我們不希望完全關閉6.0 時間範圍內的間距。

## <a name="migrations-and-deployment"></a>遷移和部署

從 [針對 EF Core 5.0 完成的調查](xref:core/what-is-new/ef-core-5.0/plan#migrations-and-deployment-experience)中，我們計畫為管理遷移和部署資料庫帶來更好的支援。 這包括兩個主要區域：

### <a name="migrations-bundles"></a>遷移套件組合

追蹤 [#19693](https://github.com/dotnet/efcore/issues/19693)

狀態：未啟動

T 恤尺寸：中

遷移套件組合是獨立的可執行檔，可將遷移套用至實際執行的資料庫。 此行為會比對 `dotnet ef database update` ，但應讓 SSH/Docker/PowerShell 部署更容易，因為所有需要的專案都包含在單一可執行檔中。

### <a name="managing-migrations"></a>管理移轉

追蹤 [#22945](https://github.com/dotnet/efcore/issues/22945)

狀態：未啟動

T 恤尺寸：大型

針對應用程式所建立的遷移數目可能會變得很負擔。 此外，這些遷移通常會與應用程式一起部署，即使這不是必要的。 在 EF Core 6.0 中，我們計畫透過更好的工具和專案/元件管理來改善此項。 我們打算解決的兩個特定問題是 [squash 許多的遷移](https://github.com/dotnet/efcore/issues/2174) ，並 [重新產生乾淨的模型快照](https://github.com/dotnet/efcore/issues/18557)集。

## <a name="improve-existing-features-and-fix-bugs"></a>改善現有的功能並修正 bug

此版本目前已規劃任何 [指派給6.0.0 里程碑的問題或 bug](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0) 。 這包括許多小型增強功能和 bug 修正。

### <a name="ef6-query-parity"></a>EF6 查詢同位檢查

由[標示為 ' ef6 ' 的問題和6.0 里程碑的問題](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aef6-parity+milestone%3A6.0.0)追蹤

狀態：未啟動

T 恤尺寸：大型

除了 EF6 不支援的模式之外，EF Core 5.0 支援 EF6 支援的大部分查詢模式。 針對 EF Core 6.0，我們計畫關閉間距，並讓支援的 EF Core 查詢成為支援 EF6 查詢的真正超集合。 這會藉由調查間距來推動，但已包含 GroupBy 問題，例如，[平移 groupby 後面接著 FirstOrDefault](https://github.com/dotnet/efcore/issues/12088) [，以及](https://github.com/dotnet/efcore/issues/10753)[基本](https://github.com/dotnet/efcore/issues/11624)和未對應類型的原始 SQL 查詢。  

### <a name="value-objects"></a>值物件

追蹤 [#9906](https://github.com/dotnet/efcore/issues/9906)

狀態：未啟動

T 恤尺寸：中

它先前是小組視圖，其擁有的實體（預定用於 [匯總支援](https://www.martinfowler.com/bliki/DDD_Aggregate.html)）也是 [值物件](https://www.martinfowler.com/bliki/ValueObject.html)的合理近似值。 體驗已顯示為不是這種情況。 因此，在 EF Core 6.0 中，我們計畫提供更佳的體驗，著重于領域導向設計中的價值物件需求。 這種方法會以值轉換器為基礎，而非擁有的實體。

這項工作一開始的範圍是允許 [對應到多個資料行的值轉換器](https://github.com/dotnet/efcore/issues/13947)。 我們可能會根據發行期間的意見反應，提取其他支援。

### <a name="cosmos-database-provider"></a>Cosmos 資料庫提供者

由[標示為 ' cosmos ' 和6.0 里程碑的問題](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Aarea-cosmos)追蹤

狀態：未啟動

T 恤尺寸：大型

我們會主動收集 EF Core 6.0 中對 Cosmos 提供者所做的改善意見反應。 當我們深入瞭解時，我們將會更新這份檔。 目前，請務必針對 👍 您需要的 Cosmos 功能投票 () 。

### <a name="expose-model-building-conventions-to-applications"></a>將模型建立慣例公開給應用程式

追蹤 [#214](https://github.com/dotnet/efcore/issues/214)

狀態：未啟動

T 恤尺寸：中

EF Core 會使用一組用來從 .NET 類型建立模型的慣例。 這些慣例目前由資料庫提供者控制。 在 EF Core 6.0 中，我們想要讓應用程式連結並變更這些慣例。

### <a name="zero-bug-balance-zbb"></a>零 (ZBB) 的錯誤餘額

由[ `type-bug` 6.0 里程碑中標記](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A6.0.0+label%3Atype-bug+)為的問題追蹤

狀態：進行中

T 恤尺寸：大型

我們計畫在 EF Core 6.0 時間範圍內修正所有未處理的 bug。 請注意下列事項：

- 這特別適用于標示為「 [類型-bug」的](https://github.com/dotnet/efcore/issues?q=is%3Aissue+label%3Atype-bug)問題。
- 有一些例外狀況，例如 bug 需要設計變更或新功能才能正常修正。 這些問題將會以標籤標示 `blocked` 。
- 當需要時，我們會根據風險來回報 bug，因為我們已接近 GA/RTM 版本。

### <a name="miscellaneous-features"></a>其他功能

由[ `type-enhancement` 6.0 里程碑中標記](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement)為的問題追蹤

狀態：進行中

T 恤尺寸：大型

針對 EF 6.0 規劃的其他功能包括（但不限於）：

- [非導覽集合的分割查詢](https://github.com/dotnet/efcore/issues/21234)
- [偵測反向工程中的簡單聯結資料表，並建立多對多關聯性](https://github.com/dotnet/efcore/issues/22475)
- [在 SQLite 和 SQL Server 上完成完整/自由文字搜尋](https://github.com/dotnet/efcore/issues/4823)
- [SQL Server 空間索引](https://github.com/dotnet/efcore/issues/12538)
- [機制/API，針對模型中指定之類型的任何屬性指定預設轉換](https://github.com/dotnet/efcore/issues/10784)
- [從 ADO.NET 使用新的批次處理 API](https://github.com/dotnet/efcore/issues/18990)

## <a name="net-integration"></a>.NET 整合

EF Core 小組也可以處理數個相關但獨立的技術。 尤其是，我們打算處理：

### <a name="enhancements-to-systemdata"></a>System. 資料的增強功能

Dotnet\runtime 存放庫[中標示為 `area-System.Data` 6.0 里程碑的問題](https://github.com/dotnet/runtime/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-System.Data+milestone%3A6.0.0)追蹤

狀態：未啟動

T 恤尺寸：大型

此工作包括：

- 新的 [批次處理 API](https://github.com/dotnet/runtime/issues/28633)的實作為。
- 繼續與其他 .NET 小組和小組合作，以瞭解併發展 ADO.NET。
- [針對 DiagnosticSource 中的追蹤進行標準化，以追蹤元件中的資料](https://github.com/dotnet/runtime/issues/22336)。

### <a name="enhancements-to-microsoftdatasqlite"></a>對資料 Sqlite 的增強功能

由在[ `type-enhancement` `area-adonet-sqlite` 6.0 里程碑中以和標示的問題](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement+label%3Aarea-adonet-sqlite)追蹤

狀態：進行中

T 恤尺寸：中

我們規劃了數個小型的改進，包括 [連接](https://github.com/dotnet/efcore/issues/13837) 共用以及效能的備妥 [語句](https://github.com/dotnet/efcore/issues/14044) 。

### <a name="nullable-reference-types"></a>可為 Null 的參考型別

追蹤 [#14150](https://github.com/dotnet/efcore/issues/14150)

狀態：進行中

T 恤尺寸：大型

我們會標注 EF Core 程式碼，以使用 [可為 null 的參考型別](/dotnet/csharp/nullable-references)。

## <a name="experiments-and-investigations"></a>實驗和調查

EF 小組計畫在 EF Core 6.0 時間範圍內投入時間，在兩個領域進行實驗和調查。 這是一個學習程式，因此不會針對6.0 版本規劃任何具體的交付專案。

### <a name="sqlservercore"></a>SqlServer. 核心

在[.Net Data Lab](https://github.com/dotnet/datalab/)存放庫中追蹤

狀態：未啟動

T 恤尺寸：進行中

[SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) 是功能完整的 ADO.NET 資料庫提供者，適用于 SQL Server。 它支援 .NET Core 和 .NET Framework 上廣泛的 SQL Server 功能。 不過，它也是大型和舊的程式碼基底，其行為之間有許多複雜的互動。 這會讓您難以使用較新的 .NET Core 功能來調查可能的收益。 因此，我們會開始與小組共同作業的實驗，以判斷針對 .NET 進行高度執行的 SQL Server 驅動程式有何潛力。

> [!IMPORTANT]
> SqlClient 的投資不會改變。 它會繼續是連接到 SQL Server 與 SQL Azure 的建議方式，不論是否有 EF Core。 它會繼續支援新的 SQL Server 功能。

### <a name="graphql"></a>GraphQL

狀態：未啟動

T 恤尺寸：進行中

過去幾年來， [GraphQL](https://graphql.org/)一直都在各種不同的平臺上。 我們計畫調查空間，並找出使用 .NET 改善體驗的方式。 這將牽涉到與社區合作，瞭解和支援現有的生態系統。 它可能也牽涉到 Microsoft 的特定投資，其形式為現有工作的投稿或開發 Microsoft stack 中的免費作品。

### <a name="dataverse-formerly-common-data-services"></a>DataVerse (先前的 Common Data Service) 

狀態：未啟動

T 恤尺寸：進行中

[DataVerse](/powerapps/maker/data-platform/data-platform-intro) 是以資料行為基礎的資料存放區，專為快速開發商務應用程式所設計。 它會自動處理複雜的資料類型，例如 (Blob 的二進位物件) 而且具有內建的實體和關聯性，例如組織和連絡人。 有一個 SDK，但開發人員可能會受益于讓 EF Core 提供者使用 advanced LINQ 查詢、利用工作單位並擁有一致的資料存取 API。 小組會考慮不同的方法來改善連接至 DataVerse 的 .NET 開發人員體驗。

## <a name="below-the-cut-line"></a>低於-剪下行

由[標記為的 `consider-for-current-release` 問題](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-current-release)追蹤

這些是目前 **尚未** 針對6.0 版本排程的 bug 修正和增強功能，但我們將會根據整個版本中的意見反應，以及上述工作的進度進行探討。 這些問題可能會提取到 EF Core 6.0，並自動成為下一版的候選項目。

此外，我們在規劃時，一律會考慮 [最投票的問題](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) 。 從版本中剪下這些問題一直都是很麻煩的，但我們的確需要針對我們所擁有的資源進行實際的規劃。 確定您已投票 (👍 所需功能的) 。

## <a name="suggestions"></a>建議

您對計劃的意見反應很重要。 指出問題重要性的最佳方式是 👍 在 GitHub 上針對該問題投票 () 。 然後，此資料會送入下一版的 [規劃](xref:core/what-is-new/release-planning) 程式。
