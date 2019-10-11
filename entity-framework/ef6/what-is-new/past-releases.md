---
title: Entity Framework 的過去版本-EF6
author: divega
ms.date: 09/12/2019
ms.assetid: 1060bb99-765f-4f32-aaeb-d6635d3dbd3e
ms.openlocfilehash: 478dec6b2401efd554e84a231fe78e71dcbf5771
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182112"
---
# <a name="past-releases-of-entity-framework"></a>過去的 Entity Framework 版本

第一版的 Entity Framework 是在2008中發行，屬於 .NET Framework 3.5 SP1 和 Visual Studio 2008 SP1 的一部分。

從 EF 4.1 版本開始，其隨附為[EntityFramework NuGet 套件](https://www.nuget.org/packages/EntityFramework/)-目前 NuGet.org 最熱門的套件之一。

在版本4.1 和5.0 之間，EntityFramework NuGet 套件會擴充 .NET Framework 隨附的 EF 程式庫。

從第6版開始，EF 變成開放原始碼專案，並已完全以頻外形式移出 .NET Framework。
這表示當您將 EntityFramework 第6版 NuGet 封裝新增至應用程式時，您會取得 EF 程式庫的完整複本，而不會相依于 .NET Framework 中隨附的 EF 位。
這有助於稍微加速開發和傳遞新功能的速度。

我們在2016年6月發行 EF Core 1.0。 EF Core 是以新的程式碼基底為基礎，並設計為 EF 的更輕量且可擴充的版本。
目前 EF Core 是 Microsoft Entity Framework 小組開發的主要焦點。
這表示沒有規劃 EF6 的新主要功能。 不過，EF6 仍會保留為開放原始碼專案和支援的 Microsoft 產品。

以下是過去版本的清單（以反向的時間順序），其中包含每個版本中引進之新功能的資訊。

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
- Bug：重試查詢或 SQL 命令失敗，並顯示「SqlParameter 已包含在另一個 SqlParameterCollection 中」[#81](https://github.com/aspnet/EntityFramework6/issues/81)
- Bug：DbQuery.ToString() 評估經常在偵錯工具中逾時 [#73](https://github.com/aspnet/EntityFramework6/issues/73)

## <a name="ef-613"></a>EF 之 EF6.1。3
EF 之 ef6.1.3 執行時間已于2015年10月發行至 NuGet。
此版本僅包含6.1.2 版本上回報之高優先順序瑕疵和回歸的修正。
修正包括：

- 查詢:EF 6.1.2 中的回歸：外部適用于1:1 關聯性和 "let" 子句的引進和更複雜的查詢
- 在繼承的類別中隱藏基類屬性的 TPT 問題
- 當文字中包含 ' go ' 一字時，DbMigration 就會失敗
- 建立 UnionAll 和 Intersect 簡維支援的相容性旗標
- 具有多個包含的查詢無法在6.1.2 中運作（在6.1.1 中工作）
- 從 EF 6.1.1 升級至6.1.2 之後，「您的 SQL 語法發生錯誤」例外狀況

## <a name="ef-612"></a>EF 6.1。2
EF 6.1.2 執行時間已在2014年12月發行至 NuGet。
此版本大多是關於錯誤修正。 我們也接受了一些關於此社區成員的值得注意的變更：
- **您可以從應用程式/web 設定檔案來設定查詢快取參數**
    ``` xml
    <entityFramework>
      <queryCache size='1000' cleaningIntervalInSeconds='-1'/>
    </entityFramework>
    ```
- **DbMigration 上的 SqlFile 和 SqlResource 方法**可讓您執行儲存為檔案或內嵌資源的 SQL 腳本。

## <a name="ef-611"></a>EF 6.1。1
EF 6.1.1 runtime 已于2014年6月發行至 NuGet。
此版本包含許多人遇到之問題的修正程式。 還有其他：
- 設計在 EF6 表設計工具中，以十進位有效位數開啟 EF5 edmx 時發生錯誤
- LocalDB 的預設實例偵測邏輯不適用於 SQL Server 2014

## <a name="ef-610"></a>EF 6.1。0
EF 6.1.0 執行時間已于2014年3月發行至 NuGet。
此次要更新包含大量的新功能：

- **工具匯總**提供一致的方式來建立新的 EF 模型。 這項功能會[擴充 ADO.NET 實體資料模型 wizard，以支援建立 Code First 模型](~/ef6/modeling/code-first/workflows/existing-database.md)，包括從現有的資料庫進行反向工程。 這些功能先前已在 EF Power tool 的 Beta 版品質中提供。
- **[處理交易認可失敗](~/ef6/fundamentals/connection-resiliency/commit-failures.md)** 會提供 CommitFailureHandler，利用新引進的功能來攔截交易作業。 CommitFailureHandler 可讓您在認可交易時，從連線失敗中自動復原。
- **[IndexAttribute](~/ef6/modeling/code-first/data-annotations.md)** 允許藉由將 `[Index]` 屬性放在 Code First 模型中的屬性（或屬性），來指定索引。 Code First 接著會在資料庫中建立對應的索引。
- **公用對應 API**可讓您存取 EF 對於屬性和類型如何對應至資料庫中的資料行和資料表的資訊。 在過去的版本中，此 API 是內部的。
- 透過 **[App/web.config 檔案設定攔截器的功能](~/ef6/fundamentals/configuring/config-file.md)** ，可讓您在不需重新編譯應用程式的情況下新增攔截器。
- **DatabaseLogger**是新的攔截器，可讓您輕鬆地將所有資料庫作業記錄到檔案中。 結合先前的功能，可讓您輕鬆地切換已[部署應用程式的資料庫作業記錄](~/ef6/fundamentals/configuring/config-file.md)，而不需要重新編譯。
- 已改善**遷移模型變更偵測**，讓 scaffold 的遷移更為精確;變更偵測程式的效能也已經增強。
- **效能改進**，包括在初始化期間減少資料庫作業、在 LINQ 查詢中優化 null 相等比較、在更多案例中產生更快速的視圖（模型建立），以及更有效率的具體化具有多個關聯的追蹤實體。

## <a name="ef-602"></a>EF 6.0。2
EF 6.0.2 執行時間已在2013年12月發行至 NuGet。
此修補程式版本僅限於修正在 EF6 版本中引進的問題（自 EF5 以來的效能/行為衰退）。

## <a name="ef-601"></a>EF 6.0。1
EF 6.0.1 執行時間已與 EF 6.0.0 同時在2013年10月發行至 NuGet，因為後者已內嵌在幾個月前鎖定的 Visual Studio 版本中。
此修補程式版本僅限於修正在 EF6 版本中引進的問題（自 EF5 以來的效能/行為衰退）。
最值得注意的變更是修正 EF 模型在準備時的一些效能問題。
這一點很重要，因為準備效能是 EF6 的重點領域，而這些問題已否定一些其他在 EF6 中的效能提升。

## <a name="ef-60"></a>EF 6。0
EF 6.0.0 執行時間已于2013年10月發行至 NuGet。
這是在[EntityFramework NuGet 套件](https://www.nuget.org/packages/EntityFramework/)中包含完整 EF 執行時間的第一個版本，而這不相依于屬於 .NET FRAMEWORK 的 ef 位。
將執行時間的其餘部分移至 NuGet 套件時，需要對現有程式碼進行一些重大變更。
請參閱[升級至 Entity Framework 6](upgrading-to-ef6.md)一節，以取得升級所需之手動步驟的詳細資料。

此版本包含許多新功能。
下列功能適用于使用 Code First 或 EF 設計工具建立的模型：

- **[非同步查詢和儲存](~/ef6/fundamentals/async.md)** 會加入 .net 4.5 中所引進之以工作為基礎的非同步模式的支援。
- 連線恢復功能可讓您從暫時性連線失敗中自動 **[復原](~/ef6/fundamentals/connection-resiliency/retry-logic.md)** 。
- 以 **[程式碼為基礎](~/ef6/fundamentals/configuring/code-based.md)** 的設定可讓您選擇執行設定–這在傳統上是在設定檔中執行（在程式碼中）。
- 相依性 **[解析](~/ef6/fundamentals/configuring/dependency-resolution.md)** 引進服務定位器模式的支援，而且我們已分解一些可由自訂的執行取代的功能。
- **[攔截/SQL 記錄](~/ef6/fundamentals/logging-and-interception.md)** 會提供低層級的建立區塊，以攔截 EF 作業，並建立最上層的簡單 SQL 記錄。
- 當您[使用模擬架構](~/ef6/fundamentals/testing/mocking.md)或[撰寫您自己的測試加倍](~/ef6/fundamentals/testing/writing-test-doubles.md)時，可**測試**性的改良讓您更輕鬆地為 DbCoNtext 和 DbSet 建立測試雙精度浮點數。
- **[現在可以使用已開啟的 DbConnection 來建立 DbCoNtext，](~/ef6/fundamentals/connection-management.md)** 這可讓您在建立內容時開啟連線時（例如，在您無法保證的元件之間共用連接），這樣會很有説明。連接的狀態）。
- **[改良的交易支援](~/ef6/saving/transactions.md)** 提供架構外部交易的支援，以及在架構內建立交易的改良方式。
- **.Net 4.0 的列舉、空間和更好的效能**-藉由將過去 .NET Framework 的核心元件移至 EF NuGet 套件，我們現在可以提供 enum 支援、空間資料類型，以及 .net 4.0 上 EF5 的效能改進。
- 已改善可列舉**的效能。包含在 LINQ 查詢中**。
- **改善的準備時間（視圖產生）** ，特別是針對大型模型。
- **隨插即用複數表示 &amp; 單數服務**。
- 現在支援實體類別上**Equals 或 GetHashCode 的自訂**執行。
- **DbSet. AddRange/RemoveRange**提供了從集合中新增或移除多個實體的最佳方式。
- **DbChangeTracker。 HasChanges**提供一個簡單又有效率的方式，來查看是否有任何暫止的變更要儲存到資料庫中。
- **SqlCeFunctions**提供相當於 SQLFUNCTIONS 的 SQL Compact。

下列功能僅適用于 Code First：

- **[自訂 Code First 慣例](~/ef6/modeling/code-first/conventions/custom.md)** 允許撰寫您自己的慣例，以協助避免重複的設定。 我們提供輕量慣例的簡單 API，以及一些更複雜的建立區塊，可讓您撰寫更複雜的慣例。
- 現在支援 **[插入/更新/刪除預存程式的 Code First 對應](~/ef6/modeling/code-first/fluent/cud-stored-procedures.md)** 。
- **[等冪遷移腳本](~/ef6/modeling/code-first/migrations/index.md)** 可讓您產生 SQL 腳本，以便將任何版本的資料庫升級至最新版本。
- **[可設定的遷移記錄資料表](~/ef6/modeling/code-first/migrations/history-customization.md)** 可讓您自訂遷移記錄資料表的定義。 這特別適用于需要適當資料類型等的資料庫提供者，才能指定要讓遷移記錄資料表正常運作。
- 使用遷移時或 Code First 自動為您建立資料庫時，每個資料庫的**多個**內容都會移除每個資料庫之一個 Code First 模型的先前限制。
- **[DbModelBuilder. HasDefaultSchema](~/ef6/modeling/code-first/fluent/types-and-properties.md)** 是新的 Code First API，可讓 Code First 模型的預設資料庫架構在一處進行設定。 先前 Code First 預設架構已硬式編碼為 @no__t 0dbo @ no__t-1，而設定資料表所屬架構的唯一方式是透過 ToTable API。
- 當您使用具有 Code First 流暢 API 的設定類別時， **AddFromAssembly 方法**可讓您輕鬆地新增元件中定義的所有 configuration 類別。
- **[自訂的遷移作業](https://romiller.com/2013/02/27/ef6-writing-your-own-code-first-migration-operations/)** 可讓您新增其他作業，以用於以程式碼為基礎的遷移。
- 針對使用 Code First 所建立的資料庫，**預設交易隔離等級會變更為 READ_COMMITTED_SNAPSHOT** ，以提供更多的擴充性和較少的鎖死。
- **實體和複雜類型現在可以是 nestedinside 類別**。 |

## <a name="ef-50"></a>EF 5。0
EF 5.0.0 執行時間已于2012年8月發行至 NuGet。
此版本引進一些新功能，包括列舉支援、資料表值函式、空間資料類型和各種效能改進。

Visual Studio 2012 中的 Entity Framework Designer 也引進了每個模型的多個圖表支援、設計介面上的圖形著色，以及預存程式的批次匯入。

以下是我們特別為 EF 5 版本放在一起的內容清單。

-   [EF 5 版本文章](https://blogs.msdn.com/b/adonet/archive/2012/08/15/ef5-released.aspx)
-   EF5 的新功能
    -   [Code First 中的列舉支援](~/ef6/modeling/code-first/data-types/enums.md)
    -   [EF 設計工具中的列舉支援](~/ef6/modeling/designer/data-types/enums.md)
    -   [Code First 中的空間資料類型](~/ef6/modeling/code-first/data-types/spatial.md)
    -   [EF 設計工具中的空間資料類型](~/ef6/modeling/designer/data-types/spatial.md)
    -   [空間類型的提供者支援](~/ef6/fundamentals/providers/spatial-support.md)
    -   [資料表值函式](~/ef6/modeling/designer/advanced/tvfs.md)
    -   [每個模型多個圖表](~/ef6/modeling/designer/multiple-diagrams.md)
-   設定模型
    -   [建立模型](~/ef6/modeling/index.md)
    -   [連接和模型](~/ef6/fundamentals/configuring/connection-strings.md)
    -   [效能考量](~/ef6/fundamentals/performance/perf-whitepaper.md)
    -   [使用 Microsoft SQL Azure](~/ef6/fundamentals/connection-resiliency/retry-logic.md)
    -   [設定檔設定](~/ef6/fundamentals/configuring/config-file.md)
    -   [字彙](~/ef6/resources/glossary.md)
    -   Code First
        -   [Code First 至新的資料庫（逐步解說和影片）](~/ef6/modeling/code-first/workflows/new-database.md)
        -   [Code First 到現有的資料庫（逐步解說和影片）](~/ef6/modeling/code-first/workflows/existing-database.md)
        -   [慣例](~/ef6/modeling/code-first/conventions/built-in.md)
        -   [資料註解](~/ef6/modeling/code-first/data-annotations.md)
        -   [流暢的 API-設定/對應屬性 & 類型](~/ef6/modeling/code-first/fluent/types-and-properties.md)
        -   [流暢的 API-設定關聯性](~/ef6/modeling/code-first/fluent/relationships.md)
        -   [使用 VB.NET 的流暢 API](~/ef6/modeling/code-first/fluent/vb.md)
        -   [Code First 移轉](~/ef6/modeling/code-first/migrations/index.md)
        -   [自動 Code First 移轉](~/ef6/modeling/code-first/migrations/automatic.md)
        -   [遷移 .exe](~/ef6/modeling/code-first/migrations/migrate-exe.md)
        -   [定義 DbSets](~/ef6/modeling/code-first/dbsets.md)
    -   EF Designer
        -   [Model First （逐步解說和影片）](~/ef6/modeling/designer/workflows/model-first.md)
        -   [Database First （逐步解說和影片）](~/ef6/modeling/designer/workflows/database-first.md)
        -   [複雜類型](~/ef6/modeling/designer/data-types/complex-types.md)
        -   [關聯/關係](~/ef6/modeling/designer/relationships.md)
        -   [TPT 繼承模式](~/ef6/modeling/designer/inheritance/tpt.md)
        -   [TPH 繼承模式](~/ef6/modeling/designer/inheritance/tph.md)
        -   [使用預存程式進行查詢](~/ef6/modeling/designer/stored-procedures/query.md)
        -   [具有多個結果集的預存程式](~/ef6/modeling/designer/advanced/multiple-result-sets.md)
        -   [使用預存程式來插入、更新 & 刪除](~/ef6/modeling/designer/stored-procedures/cud.md)
        -   [將實體對應至多個資料表（實體分割）](~/ef6/modeling/designer/entity-splitting.md)
        -   [將多個實體對應到一個資料表（資料表分割）](~/ef6/modeling/designer/table-splitting.md)
        -   [定義查詢](~/ef6/modeling/designer/advanced/defining-query.md)
        -   [程式碼產生範本](~/ef6/modeling/designer/codegen/index.md)
        -   [還原至 ObjectCoNtext](~/ef6/modeling/designer/codegen/legacy-objectcontext.md)
-   使用您的模型
    -   [使用 DbContext](~/ef6/fundamentals/working-with-dbcontext.md)
    -   [查詢/尋找實體](~/ef6/querying/index.md)
    -   [使用關聯性](~/ef6/fundamentals/relationships.md)
    -   [載入相關實體](~/ef6/querying/related-data.md)
    -   [使用本機資料](~/ef6/querying/local-data.md)
    -   [多層式架構應用程式](~/ef6/fundamentals/disconnected-entities/index.md)
    -   [原始 SQL 查詢](~/ef6/querying/raw-sql.md)
    -   [開放式平行存取模式](~/ef6/saving/concurrency.md)
    -   [使用 proxy](~/ef6/fundamentals/proxies.md)
    -   [自動偵測變更](~/ef6/saving/change-tracking/auto-detect-changes.md)
    -   [無追蹤查詢](~/ef6/querying/no-tracking.md)
    -   [Load 方法](~/ef6/querying/load-method.md)
    -   [新增/附加和實體狀態](~/ef6/saving/change-tracking/entity-state.md)
    -   [使用屬性值](~/ef6/saving/change-tracking/property-values.md)
    -   [使用 WPF 的資料系結（Windows Presentation Foundation）](~/ef6/fundamentals/databinding/wpf.md)
    -   [使用 WinForms （Windows Forms）進行資料系結](~/ef6/fundamentals/databinding/winforms.md)

## <a name="ef-431"></a>EF 4.3。1
Ef 4.3.1 執行時間已在 EF 4.3.0 之後的2012年2月發行至 NuGet。
此修補程式版本包含 EF 4.3 版本的一些 bug 修正，並為使用 EF 4.3 與 Visual Studio 2012 的客戶引進了更好的 LocalDB 支援。

以下是我們特別為 EF 4.3.1 版本放在一起的內容清單，針對 EF 4.1 提供的大部分內容也適用于 EF 4.3。

-   [EF 4.3.1 Release Blog 文章](https://blogs.msdn.com/b/adonet/archive/2012/02/29/ef4-3-1-and-ef5-beta-1-available-on-nuget.aspx)

## <a name="ef-43"></a>EF 4。3
EF 4.3.0 執行時間已于2012年2月發行至 NuGet。
此版本包含新的 Code First 移轉功能，可讓 Code First 所建立的資料庫隨著您的 Code First 模型發展，以累加方式變更。

以下是我們特別為 EF 4.3 版本放在一起的內容清單，針對 EF 4.1 提供的大部分內容也也適用于 EF 4.3：
-   [EF 4.3 發行文章](https://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-released.aspx)
-   [EF 4.3 以程式碼為基礎的遷移逐步解說](https://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-code-based-migrations-walkthrough.aspx)
-   [EF 4.3 自動遷移逐步解說](https://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-automatic-migrations-walkthrough.aspx)

## <a name="ef-42"></a>EF 4。2
EF 4.2.0 執行時間已于2011年11月發行至 NuGet。
此版本包含 EF 4.1.1 版本的錯誤修正。
因為此版本只包含 bug 修正，所以它可能是 EF 4.1.2 修補程式版本，但我們選擇改為4.2，以讓我們從 4.1. x 版中使用的以日期為基礎的修補程式版本號碼中移出，並採用適用于 s 的[語義 Versionsing](https://semver.org)標準emantic 版本設定。

以下是我們特別為 EF 4.2 版本放在一起的內容清單，針對 EF 4.1 提供的內容也會套用至 EF 4.2。

-   [EF 4.2 發行文章](https://blogs.msdn.com/b/adonet/archive/2011/11/01/ef-4-2-released.aspx)
-   [Code First 逐步解說](https://blogs.msdn.com/b/adonet/archive/2011/09/28/ef-4-2-code-first-walkthrough.aspx)
-   [模型 & Database First 逐步解說](https://blogs.msdn.com/b/adonet/archive/2011/09/28/ef-4-2-model-amp-database-first-walkthrough.aspx)

## <a name="ef-411"></a>EF 4.1。1
EF 4.1.10715 執行時間已于2011年7月發行至 NuGet。
除了錯誤修正之外，此修補程式版本也引進了一些元件，讓設計階段工具更容易與 Code First 模型搭配使用。
這些元件是由 Code First 移轉（包含在 EF 4.3 中）和 EF Power Tools 所使用。

您會注意到封裝的奇怪版本號碼4.1.10715。
我們會先使用以日期為基礎的修補程式版本，再決定採用[語義版本](https://semver.org)設定。
請將此版本視為 EF 4.1 patch 1 （或 EF 4.1.1）。

以下是我們為4.1.1 版本放在一起的內容清單：

-   [EF 4.1.1 Release 文章](https://blogs.msdn.com/b/adonet/archive/2011/07/25/ef-4-1-update-1-released.aspx)

## <a name="ef-41"></a>EF 4。1
EF 4.1.10331 執行時間是在 NuGet 上發行的第一個（2011年4月）。
此版本包含簡化的 DbCoNtext API 和 Code First 工作流程。

您會注意到奇怪的版本號碼4.1.10331，這應該真的是4.1。 此外，還有4.1.10311 版本應該已 4.1.0-rc （' rc ' 代表「候選版」）。
我們會先使用以日期為基礎的修補程式版本，再決定採用[語義版本](https://semver.org)設定。

以下是我們為4.1 版本放在一起的內容清單。 其中大部分仍適用于較新版本的 Entity Framework：

-   [EF 4.1 發行文章](https://blogs.msdn.com/b/adonet/archive/2011/04/11/ef-4-1-released.aspx)
-   [Code First 逐步解說](https://blogs.msdn.com/b/adonet/archive/2011/03/15/ef-4-1-code-first-walkthrough.aspx)
-   [模型 & Database First 逐步解說](https://blogs.msdn.com/b/adonet/archive/2011/03/15/ef-4-1-model-amp-database-first-walkthrough.aspx)
-   [SQL Azure 同盟和 Entity Framework](https://blogs.msdn.com/b/adonet/archive/2012/01/10/sql-azure-federations-and-the-entity-framework.aspx)

## <a name="ef-40"></a>EF 4。0
此版本隨附于 .NET Framework 4，Visual Studio 2010，2010年4月。
此版本中的重要新功能包括 POCO 支援、外鍵對應、消極式載入、可測試性改進、可自訂的程式碼產生和 Model First 工作流程。

雖然它是 Entity Framework 的第二個版本，但它名為 EF 4，以配合隨附的 .NET Framework 版本。
在此版本之後，我們開始將 Entity Framework 提供給 NuGet，並採用了語義版本設定，因為我們不再系結至 .NET Framework 版本。

請注意，某些後續的 .NET Framework 版本已隨附包含 EF bits 的重大更新。
事實上，EF 5.0 的許多新功能已實作為這些位的改進。
不過，為了合理化 EF 的版本控制案例，我們會繼續參考屬於 EF 4.0 執行時間 .NET Framework 一部分的 EF 位，而所有較新的版本都是由[EntityFramework NuGet 套件](https://www.nuget.org/packages/EntityFramework/)所組成。

## <a name="ef-35"></a>EF 3。5
Entity Framework 的初始版本已包含在 .NET 3.5 Service Pack 1 和 Visual Studio 2008 SP1 中，于2008年8月發行。
此版本提供了使用 Database First 工作流程的基本 O/RM 支援。
