---
title: 過去的 Entity Framework 版本 EF6
description: 過去的 Entity Framework 版本
author: ajcvickers
ms.date: 09/12/2019
uid: ef6/what-is-new/past-releases
ms.openlocfilehash: 990b543b2baff0a70e32bf3d5ce624b419c925b8
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064311"
---
# <a name="past-releases-of-entity-framework"></a>過去的 Entity Framework 版本

Entity Framework 的第一個版本是在2008中發行，.NET Framework 3.5 SP1 和 Visual Studio 2008 SP1 的一部分。

從 EF 4.1 版本開始，它隨附于 [EntityFramework NuGet 套件](https://www.nuget.org/packages/EntityFramework/) -NuGet.org 上目前最受歡迎的套件之一。

在版本4.1 和5.0 之間，EntityFramework NuGet 套件擴充了隨附于 .NET Framework 一部分的 EF 程式庫。

從第6版開始，EF 變成開放原始碼專案，而且也從 .NET Framework 完全移出範圍外。
這表示當您將 EntityFramework 第6版 NuGet 套件新增至應用程式時，您會取得 EF 程式庫的完整複本，而該程式庫不相依于 .NET Framework 中隨附的 EF 位。
這有助於稍微加速開發和傳遞新功能的步調。

在2016年6月，我們發行了 EF Core 1.0。 EF Core 是以新的程式碼基底為基礎，而且是設計成更輕量且可擴充的 EF 版本。
目前 EF Core 是 Microsoft Entity Framework 團隊開發的主要焦點。
這表示沒有規劃 EF6 的新主要功能。 不過，EF6 仍維持為開放原始碼專案和支援的 Microsoft 產品。

以下是過去版本的清單（以反向時間順序排列），並提供每個版本中所引進新功能的資訊。

## <a name="ef-tools-update-in-visual-studio-2017-157"></a>Visual Studio 2017 15.7 中的 EF 工具更新
在 2018 年 5 月，我們在 Visual Studio 2017 15.7 中發行了 EF Tools 的更新版本。
其中包含一些常見難題的改善：

- 修正使用者介面協助工具的數個 Bug
- 從現有資料庫產生模型時，SQL Server 效能降低的因應措施 [#4](https://github.com/aspnet/entityframework6/issues/4)
- 支援為 SQL Server 上的大型模型更新模型[#185](https://github.com/aspnet/EntityFramework6/issues/185)

EF Tools 這個新版本有另一項功能改進，就是在新專案中建立模型時，會安裝 EF 6.2 執行階段。 利用舊版的 Visual Studio，就可以安裝 NuGet 套件的對應版本來使用 EF 6.2 執行階段 (以及 EF 的任何過去版本)。

## <a name="ef-620"></a>EF 6.2。0
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

## <a name="ef-613"></a>EF 6.1。3
EF 6.1.3 runtime 已于2015年10月發行至 NuGet。
此版本只包含6.1.2 版本上回報的高優先順序瑕疵和回歸的修正。
修正包括：

- 查詢： EF 6.1.2 中的回歸： OUTER 適用于1:1 關聯性和 "let" 子句的引入和更複雜的查詢
- 在繼承類別中隱藏基類屬性的 TPT 問題
- 當單字 ' go ' 包含在文字中時，DbMigration 會失敗
- 建立 UnionAll 和 Intersect 簡維支援的相容性旗標
- 使用多個 Include 的查詢無法在 6.1.2 (于 6.1.1) 中運作
- 從 EF 6.1.1 升級至6.1.2 之後發生「SQL 語法錯誤」例外狀況

## <a name="ef-612"></a>EF 6.1。2
EF 6.1.2 runtime 已于2014年12月發行至 NuGet。
此版本大多是關於 bug 修正。 我們也已接受來自該社區成員的幾項值得注意的變更：
- **您可以從應用程式/web.configuration 檔設定查詢快取參數**
    ``` xml
    <entityFramework>
      <queryCache size='1000' cleaningIntervalInSeconds='-1'/>
    </entityFramework>
    ```
- **DbMigration 上的 SqlFile 和 SqlResource 方法** 可讓您執行以檔案或內嵌資源形式儲存的 SQL 腳本。

## <a name="ef-611"></a>EF 6.1。1
EF 6.1.1 執行時間已在2014年6月發行至 NuGet。
此版本包含許多人遇到之問題的修正程式。 還有其他：
- 設計工具：在 EF6 表設計工具中開啟具有小數有效位數的 EF5 edmx 時發生錯誤
- LocalDB 的預設實例偵測邏輯不適用於 SQL Server 2014

## <a name="ef-610"></a>EF 6.1。0
EF 6.1.0 runtime 已于2014年3月發行至 NuGet。
這項次要更新包含大量的新功能：

- **工具合併** 可提供一致的方式來建立新的 EF 模型。 這項功能會 [擴充 ADO.NET 實體資料模型 wizard，以支援建立 Code First 模型](xref:ef6/modeling/code-first/workflows/existing-database)，包括從現有資料庫進行反向工程。 這些功能先前在 EF Power Tools 中提供 Beta 版品質。
- **[處理交易認可失敗](xref:ef6/fundamentals/connection-resiliency/commit-failures)** 會提供 CommitFailureHandler，讓您利用新引進的功能來攔截交易作業。 CommitFailureHandler 可在認可交易時，允許從連接失敗中自動復原。
- **[IndexAttribute](xref:ef6/modeling/code-first/data-annotations)** 可讓您藉由將屬性放 `[Index]` 在 Code First 模型中) 的屬性 (或屬性來指定索引。 Code First 接著會在資料庫中建立對應的索引。
- **公開對應 API** 可讓您存取 EF 對於屬性和類型如何對應至資料庫中的資料行和資料表的資訊。 在過去的版本中，此 API 是內部的。
- 透過**[應用程式/Web.config 檔設定攔截](xref:ef6/fundamentals/configuring/config-file)** 器的功能，可以在不重新編譯應用程式的情況下新增攔截器。
- **DatabaseLogger**是新的攔截器，可讓您輕鬆地將所有資料庫作業記錄到檔案中。 結合了先前的功能，可讓您輕鬆地針對已 [部署的應用程式切換資料庫作業的記錄](xref:ef6/fundamentals/configuring/config-file)，而不需要重新編譯。
- 已改善**遷移模型變更偵測**，讓 scaffold 遷移更準確;變更偵測程式的效能也已經增強。
- **效能改進** ，包括初始化期間的資料庫作業減少、LINQ 查詢中 null 相等比較的優化、更快速地產生 (模型建立) 在更多案例中，以及更有效率地具體化具有多個關聯的追蹤實體。

## <a name="ef-602"></a>EF 6.0。2
EF 6.0.2 runtime 已于2013年12月發行至 NuGet。
此修補程式版本僅限於修正在 EF6 版本中引進的問題， (在 EF5) 之後效能/行為中的回歸。

## <a name="ef-601"></a>EF 6.0。1
EF 6.0.1 執行時間已于2013年10月與 EF 6.0.0 同時發行至 NuGet，因為後者內嵌于已在幾個月內鎖定的 Visual Studio 版本中。
此修補程式版本僅限於修正在 EF6 版本中引進的問題， (在 EF5) 之後效能/行為中的回歸。
最值得注意的變更是修正 EF 模型準備時的一些效能問題。
這一點很重要，因為準備效能是 EF6 的重點，而這些問題否定了 EF6 的一些其他效能提升。

## <a name="ef-60"></a>EF 6。0
EF 6.0.0 runtime 已于2013年10月發行至 NuGet。
這是 [EntityFramework NuGet 套件](https://www.nuget.org/packages/EntityFramework/) 中包含完整 EF runtime 的第一個版本，其不相依于屬於 .NET FRAMEWORK 的 ef 位。
將執行時間的其餘部分移至 NuGet 套件時，需要對現有程式碼進行一些重大變更。
請參閱 [升級至 Entity Framework 6](xref:ef6/what-is-new/upgrading-to-ef6) 的一節，以取得升級所需之手動步驟的詳細資料。

此版本包含許多新功能。
下列功能適用于使用 Code First 或 EF 設計工具建立的模型：

- **[非同步查詢和儲存](xref:ef6/fundamentals/async)** 新增了在 .net 4.5 中引進的工作架構非同步模式的支援。
- **[連接復原](xref:ef6/fundamentals/connection-resiliency/retry-logic)** 功能可讓您從暫時性連接失敗中自動復原。
- 以程式**[代碼為基礎](xref:ef6/fundamentals/configuring/code-based)** 的設定可讓您選擇執行設定，也就是在程式碼中，傳統上是在設定檔中執行的設定。
- 相依性**[解析](xref:ef6/fundamentals/configuring/dependency-resolution)** 引進了服務定位器模式的支援，我們也考慮了一些可由自訂的實作為取代的功能。
- **[攔截/SQL 記錄](xref:ef6/fundamentals/logging-and-interception)** 提供低層級的建立區塊，可攔截 EF 作業，並以最上層的簡單 SQL 記錄來攔截。
- 可測試性**改進**可讓您更輕鬆地在[使用模擬架構](xref:ef6/fundamentals/testing/mocking)或[撰寫您自己的測試雙精度](xref:ef6/fundamentals/testing/writing-test-doubles)浮點數時，為 DbCoNtext 和 DbSet 建立測試雙精確度。
- 您**[現在可以使用已開啟的 DbConnection 來建立 DbCoNtext](xref:ef6/fundamentals/connection-management)** ，這可讓您在建立內容 (時，如果連接可以開啟，例如共用元件之間的連接（您無法保證連線) 的狀態），就會有説明的情況。
- **[改良的交易支援](xref:ef6/saving/transactions)** 提供架構外部交易的支援，以及在架構內建立交易的改良方式。
- **.Net 4.0 上的列舉、空間及更佳的效能** -藉由將 .NET Framework 的核心元件移至 EF NuGet 套件中，我們現在可以提供列舉支援、空間資料類型，以及 .net 4.0 上的 EF5 效能改進。
- **改進可列舉的效能。包含在 LINQ 查詢中**。
- **改善 (視圖產生) **的準備時間，特別是針對大型模型。
- **插即用複數表示 &amp; 單數服務**。
- 現在支援在實體類別上進行**Equals 或 GetHashCode 的自訂**執行。
- **DbSet. >addrange/RemoveRange** 提供優化的方式，可從集合中新增或移除多個實體。
- **DbChangeTracker** 可讓您輕鬆且有效率地查看是否有任何暫止的變更要儲存至資料庫。
- **SqlCeFunctions** 提供相當於 SQLFUNCTIONS 的 SQL Compact。

下列功能只適用于 Code First：

- **[自訂 Code First 慣例](xref:ef6/modeling/code-first/conventions/custom)** 可讓您撰寫自己的慣例，以協助避免重複的設定。 我們針對輕量慣例提供簡單的 API，以及一些更複雜的組建區塊，可讓您撰寫更複雜的慣例。
- 現在支援**[Insert/Update/Delete 預存程式的 Code First 對應](xref:ef6/modeling/code-first/fluent/cud-stored-procedures)**。
- **[等冪的遷移腳本](xref:ef6/modeling/code-first/migrations/index)** 可讓您產生可將任何版本的資料庫升級至最新版本的 SQL 腳本。
- **[可設定的遷移歷程記錄資料表](xref:ef6/modeling/code-first/migrations/history-customization)** 可讓您自訂遷移歷程記錄資料表的定義。 這特別適用于需要適當資料類型等的資料庫提供者，才能指定讓遷移歷程記錄資料表正常運作。
- 使用遷移時或 Code First 自動為您建立資料庫時，**每個資料庫的多個**內容會移除每個資料庫的一個 Code First 模型的先前限制。
- **[DbModelBuilder](xref:ef6/modeling/code-first/fluent/types-and-properties)** 是新的 Code First API，可讓您在一個位置設定 Code First 模型的預設資料庫架構。 先前的 Code First 預設架構已硬式編碼為 &quot; dbo &quot; ，而設定資料表所屬之架構的唯一方式是透過 ToTable API。
- **DbModelBuilder.Configurations。AddFromAssembly 方法** 可讓您在使用設定類別搭配 Code First 的流暢 API 時，輕鬆地新增元件中定義的所有設定類別。
- **[自訂的遷移作業](https://romiller.com/2013/02/27/ef6-writing-your-own-code-first-migration-operations/)** 可讓您新增其他作業，以用於以程式碼為基礎的遷移。
- 針對使用 Code First 所建立的資料庫，**預設交易隔離等級會變更為 READ_COMMITTED_SNAPSHOT** ，可提供更多的擴充性和較少的鎖死。
- **實體和複雜類型現在可以是 nestedinside 類別**。

## <a name="ef-50"></a>EF 5。0
EF 5.0.0 執行時間已于2012年8月發行至 NuGet。
此版本導入了一些新功能，包括列舉支援、資料表值函式、空間資料類型和各種效能改進。

Visual Studio 2012 中的 Entity Framework Designer 也為每個模型推出了多個圖表的支援、設計介面上的圖形色彩，以及預存程式的批次匯入。

以下是我們特別為 EF 5 版本組合的內容清單：

-   [EF 5 發行 Post](https://blogs.msdn.com/b/adonet/archive/2012/08/15/ef5-released.aspx)
-   EF5 中的新功能
    -   [Code First 中的列舉支援](xref:ef6/modeling/code-first/data-types/enums)
    -   [EF 設計工具中的列舉支援](xref:ef6/modeling/designer/data-types/enums)
    -   [Code First 中的空間資料類型](xref:ef6/modeling/code-first/data-types/spatial)
    -   [EF 設計工具中的空間資料類型](xref:ef6/modeling/designer/data-types/spatial)
    -   [空間類型的提供者支援](xref:ef6/fundamentals/providers/spatial-support)
    -   [資料表值函式](xref:ef6/modeling/designer/advanced/tvfs)
    -   [每個模型的多個圖表](xref:ef6/modeling/designer/multiple-diagrams)
-   設定您的模型
    -   [建立模型](xref:ef6/modeling/index)
    -   [連接和模型](xref:ef6/fundamentals/configuring/connection-strings)
    -   [效能考量](xref:ef6/fundamentals/performance/perf-whitepaper)
    -   [使用 Microsoft SQL Azure](xref:ef6/fundamentals/connection-resiliency/retry-logic)
    -   [設定檔設定](xref:ef6/fundamentals/configuring/config-file)
    -   [詞彙](xref:ef6/resources/glossary)
    -   Code First
        -   [Code First 至新的資料庫 (逐步解說和影片) ](xref:ef6/modeling/code-first/workflows/new-database)
        -   [Code First 至現有的資料庫 (逐步解說和影片) ](xref:ef6/modeling/code-first/workflows/existing-database)
        -   [慣例](xref:ef6/modeling/code-first/conventions/built-in)
        -   [資料批註](xref:ef6/modeling/code-first/data-annotations)
        -   [流暢的 API-設定/對應屬性 & 類型](xref:ef6/modeling/code-first/fluent/types-and-properties)
        -   [流暢的 API-設定關聯性](xref:ef6/modeling/code-first/fluent/relationships)
        -   [使用 VB.NET 的流暢 API](xref:ef6/modeling/code-first/fluent/vb)
        -   [Code First 移轉](xref:ef6/modeling/code-first/migrations/index)
        -   [自動 Code First 移轉](xref:ef6/modeling/code-first/migrations/automatic)
        -   [Migrate.exe](xref:ef6/modeling/code-first/migrations/migrate-exe)
        -   [定義 DbSets](xref:ef6/modeling/code-first/dbsets)
    -   EF Designer
        -   [Model First (逐步解說和影片) ](xref:ef6/modeling/designer/workflows/model-first)
        -   [Database First (逐步解說和影片) ](xref:ef6/modeling/designer/workflows/database-first)
        -   [複雜類型](xref:ef6/modeling/designer/data-types/complex-types)
        -   [關聯/關係](xref:ef6/modeling/designer/relationships)
        -   [TPT 繼承模式](xref:ef6/modeling/designer/inheritance/tpt)
        -   [TPH 繼承模式](xref:ef6/modeling/designer/inheritance/tph)
        -   [使用預存程式進行查詢](xref:ef6/modeling/designer/stored-procedures/query)
        -   [具有多個結果集的預存程式](xref:ef6/modeling/designer/advanced/multiple-result-sets)
        -   [使用預存程式插入、更新 & 刪除](xref:ef6/modeling/designer/stored-procedures/cud)
        -   [將實體對應至多個資料表 (實體分割) ](xref:ef6/modeling/designer/entity-splitting)
        -   [將多個實體對應至一個資料表， (資料表分割) ](xref:ef6/modeling/designer/table-splitting)
        -   [定義查詢](xref:ef6/modeling/designer/advanced/defining-query)
        -   [程式碼產生範本](xref:ef6/modeling/designer/codegen/index)
        -   [正在還原至 ObjectCoNtext](xref:ef6/modeling/designer/codegen/legacy-objectcontext)
-   使用您的模型
    -   [使用 DbContext](xref:ef6/fundamentals/working-with-dbcontext)
    -   [查詢/尋找實體](xref:ef6/querying/index)
    -   [使用關聯性](xref:ef6/fundamentals/relationships)
    -   [載入相關實體](xref:ef6/querying/related-data)
    -   [使用本機資料](xref:ef6/querying/local-data)
    -   [多層式應用程式](xref:ef6/fundamentals/disconnected-entities/index)
    -   [原始 SQL 查詢](xref:ef6/querying/raw-sql)
    -   [開放式平行存取模式](xref:ef6/saving/concurrency)
    -   [使用 proxy](xref:ef6/fundamentals/proxies)
    -   [自動偵測變更](xref:ef6/saving/change-tracking/auto-detect-changes)
    -   [不追蹤的查詢](xref:ef6/querying/no-tracking)
    -   [Load 方法](xref:ef6/querying/load-method)
    -   [新增/附加和實體狀態](xref:ef6/saving/change-tracking/entity-state)
    -   [使用屬性值](xref:ef6/saving/change-tracking/property-values)
    -   [使用 WPF 的資料系結 (Windows Presentation Foundation) ](xref:ef6/fundamentals/databinding/wpf)
    -   [使用 WinForms (Windows Forms) 的資料系結 ](xref:ef6/fundamentals/databinding/winforms)

## <a name="ef-431"></a>EF 4.3。1
Ef 4.3.1 runtime 已于2012年2月于年2月發行至 NuGet，在 EF 4.3.0 之後。
此修補程式版本包含一些 EF 4.3 版本的 bug 修正，並為使用 EF 4.3 搭配 Visual Studio 2012 的客戶引進了更好的 LocalDB 支援。

以下是我們特別為 EF 4.3.1 版本提供的內容清單，針對 EF 4.1 所提供的內容大多也適用于 EF 4.3：

-   [EF 4.3.1 Release Blog 文章](https://blogs.msdn.com/b/adonet/archive/2012/02/29/ef4-3-1-and-ef5-beta-1-available-on-nuget.aspx)

## <a name="ef-43"></a>EF 4。3
EF 4.3.0 runtime 已于2012年2月發行至 NuGet。
此版本包含新的 Code First 移轉功能，可讓 Code First 所建立的資料庫在您的 Code First 模型演進時，以累加方式變更。

以下是我們特別為 EF 4.3 版本提供的內容清單，針對 EF 4.1 提供的內容大多也適用于 EF 4.3：
-   [EF 4.3 發行 Post](https://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-released.aspx)
-   [EF 4.3 Code-Based 遷移逐步解說](https://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-code-based-migrations-walkthrough.aspx)
-   [EF 4.3 自動遷移逐步解說](https://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-automatic-migrations-walkthrough.aspx)

## <a name="ef-42"></a>EF 4。2
EF 4.2.0 runtime 已于2011年11月發行至 NuGet。
此版本包含 EF 4.1.1 版本的 bug 修正。
因為此版本只包含 bug 修正，所以它可能是 EF 4.1.2 patch 版本，但我們選擇移至4.2，讓我們可以離開我們在 4.1. x 版本中使用的產品修補程式版本號碼，並採用語義 [Versionsing](https://semver.org) 標準來進行語義版本控制。

以下是我們特別為 EF 4.2 版本提供的內容清單，針對 EF 4.1 提供的內容也適用于 EF 4.2：

-   [EF 4.2 發行 Post](https://blogs.msdn.com/b/adonet/archive/2011/11/01/ef-4-2-released.aspx)
-   [Code First 逐步解說](https://blogs.msdn.com/b/adonet/archive/2011/09/28/ef-4-2-code-first-walkthrough.aspx)
-   [模型 & Database First 逐步解說](https://blogs.msdn.com/b/adonet/archive/2011/09/28/ef-4-2-model-amp-database-first-walkthrough.aspx)

## <a name="ef-411"></a>EF 4.1。1
EF 4.1.10715 runtime 已于2011年7月發行至 NuGet。
除了 bug 修正之外，此修補程式還引進了一些元件，讓設計階段工具可以更輕鬆地使用 Code First 模型。
這些元件是由 EF 4.3) 和 EF Power Tools 中包含的 Code First 移轉 (所使用。

您將會注意到套件的奇怪版本號碼4.1.10715。
我們在決定採用 [語義版本控制](https://semver.org)之前，先使用以日期為基礎的修補程式版本。
請將此版本視為 EF 4.1 patch 1 (或 EF 4.1.1) 。

以下是我們為4.1.1 版本結合的內容清單：

-   [EF 4.1.1 發行文章](https://blogs.msdn.com/b/adonet/archive/2011/07/25/ef-4-1-update-1-released.aspx)

## <a name="ef-41"></a>EF 4。1
EF 4.1.10331 執行時間是在2011年4月發佈到 NuGet 上的第一個執行時間。
此版本包含簡化的 DbCoNtext API 和 Code First 工作流程。

您將會注意到奇怪的版本號碼4.1.10331，它應該是4.1。 此外，4.1.10311 版本應該已 4.1.0 rc (「rc」代表「候選版」 ) 。
我們在決定採用 [語義版本控制](https://semver.org)之前，先使用以日期為基礎的修補程式版本。

以下是我們在4.1 版中彙集的內容清單。 其中大部分仍適用于 Entity Framework 的更新版本：

-   [EF 4.1 發行 Post](https://blogs.msdn.com/b/adonet/archive/2011/04/11/ef-4-1-released.aspx)
-   [Code First 逐步解說](https://blogs.msdn.com/b/adonet/archive/2011/03/15/ef-4-1-code-first-walkthrough.aspx)
-   [模型 & Database First 逐步解說](https://blogs.msdn.com/b/adonet/archive/2011/03/15/ef-4-1-model-amp-database-first-walkthrough.aspx)
-   [SQL Azure 同盟和 Entity Framework](https://blogs.msdn.com/b/adonet/archive/2012/01/10/sql-azure-federations-and-the-entity-framework.aspx)

## <a name="ef-40"></a>EF 4。0
此版本隨附于2010年4月 .NET Framework 4 和 Visual Studio 2010。
此版本中的重要新功能包括 POCO 支援、外鍵對應、消極式載入、可測試性改進、可自訂的程式碼產生和 Model First 工作流程。

雖然這是 Entity Framework 的第二個版本，但它名為 EF 4，以配合隨附的 .NET Framework 版本。
在此版本之後，我們開始讓 Entity Framework 可在 NuGet 上使用，並採用語義版本控制，因為我們不再系結到 .NET Framework 版本。

請注意，.NET Framework 的某些後續版本已隨附包含 EF bits 的重大更新。
事實上，EF 5.0 的許多新功能已實作為這些位的增強功能。
不過，為了合理化 EF 的版本控制案例，我們會繼續參考屬於 EF 4.0 執行時間 .NET Framework 一部分的 EF 位，而所有較新的版本則包含 [EntityFramework NuGet 套件](https://www.nuget.org/packages/EntityFramework/)。

## <a name="ef-35"></a>EF 3。5
Entity Framework 的初始版本已隨附于 .NET 3.5 Service Pack 1 和 Visual Studio 2008 SP1，發行日期為2008年8月。
此版本提供使用 Database First 工作流程的基本 O/RM 支援。
