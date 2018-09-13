---
title: Entity Framework-EF6 的過去版本
author: divega
ms.date: 10/23/2016
ms.assetid: 1060bb99-765f-4f32-aaeb-d6635d3dbd3e
ms.openlocfilehash: 4c711bb48938e5c0432881c61766b0bff66498f2
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490138"
---
# <a name="past-releases-of-entity-framework"></a>過去的 Entity Framework 的版本

Entity Framework 的第一個版本已發行在 2008 中，為.NET Framework 3.5 SP1 和 Visual Studio 2008 SP1 的一部分。

從 EF4.1 版已隨附[EntityFramework NuGet 套件](https://www.nuget.org/packages/EntityFramework/)-目前是其中一個最受歡迎的套件在 NuGet.org 上。

版本之間 4.1 和 5.0，EntityFramework NuGet 套件會擴充.NET framework 隨附的 EF 程式庫。   

從版本 6 開始，EF 成為開放原始碼專案，並也移完全出頻外表單.NET Framework。
這表示當您 EntityFramework 第 6 版的 NuGet 套件新增至應用程式時，您會收到不相依於隨附於.NET Framework 的 EF 位元的 EF 文件庫的完整複本。
這協助稍微加速開發的速度和新功能的傳遞。

在 2016 年 6 月中，我們發行 EF Core 1.0。 EF Core 新的程式碼基底為基礎，而且是為更輕量且可延伸的 EF 版本。
目前 EF Core 是 Entity Framework 小組在 Microsoft 開發的重點。
這表示沒有任何新的主要功能，計劃 EF6。 不過 EF6 仍會維護為開放原始碼專案和支援的 Microsoft 產品。

以下是過去的版本中，清單依反向時間順序的詳細資訊，每個版本中引進的新功能的相關資訊。

## <a name="ef-613"></a>EF 6.1.3
EF 6.1.3 執行階段已於 2015 年 10 月發行至 NuGet。
此版本包含唯一的修正程式，以高優先順序的瑕疵品和迴歸 6.1.2 上回報版本。
的修正包括：

- 查詢： EF 6.1.2 中迴歸： OUTER APPLY 引進和更複雜的查詢 1 對 1 關聯性的"let"子句
- TPT 問題隱藏繼承的類別中的基底類別屬性
- 在文字中包含 'go' 這個字時，就會失敗 DbMigration.Sql
- 建立相容性旗標 UnionAll 和 Intersect 壓平合併的支援
- 使用多個包含查詢 （使用 6.1.1） 6.1.2 中無法運作
- 從 EF 6.1.1 到 6.1.2 升級之後，「 您將有在您的 SQL 語法錯誤 」 的例外狀況

## <a name="ef-612"></a>EF 6.1.2
EF 6.1.2 執行階段已於 2014 年 12 月發行至 NuGet。
這個版本是大部分相關 bug 修正。 我們也會接受一些值得注意的變更，從社群的成員：
- **查詢參數可以設定從 app/web.configuration 檔案的快取**
    ``` xml
    <entityFramework>
      <queryCache size='1000' cleaningIntervalInSeconds='-1'/>
    </entityFramework>
    ```
- **SqlFile 和 SqlResource 方法 DbMigration**可讓您執行 SQL 指令碼儲存為檔案或內嵌的資源。

## <a name="ef-611"></a>EF 6.1.1
EF 6.1.1 執行階段已於 2014 年 6 月發行至 NuGet。
此版本包含許多人已遇到之問題的修正程式。 其他項目：
- 開啟 EF5 edmx EF6 設計工具中的小數有效位數的設計工具： 時發生錯誤
- Localdb 的預設執行個體偵測邏輯並不適用於 SQL Server 2014

## <a name="ef-610"></a>EF 6.1.0
EF 6.1.0 執行階段已於 2014 年 3 月發行至 NuGet。
這個次要更新包含大量的新功能：

- **工具 彙總**提供一致的方式，來建立新的 EF 模型。 這項功能[擴充 ADO.NET 實體資料模型精靈，可讓您建立的 Code First 模型](~/ef6/modeling/code-first/workflows/existing-database.md)，包括反向工程，從現有的資料庫。 這些功能是先前在 EF Power Tools 中的 Beta 品質中提供。
- **[處理交易認可失敗](~/ef6/fundamentals/connection-resiliency/commit-failures.md)** 提供其使用的新引進的功能來攔截交易作業 CommitFailureHandler。 CommitFailureHandler 認可交易的同時，可讓從連線失敗自動復原。
- **[IndexAttribute](~/ef6/modeling/code-first/data-annotations.md)** 允許將指定的索引`[Index]`Code First 模型中屬性的屬性 （或屬性）。 程式碼第一次將接著建立對應的索引在資料庫中。
- **公開的對應 API**提供 EF 對如何對應到資料行和資料表在資料庫中的屬性及型別資訊的存取權。 在過去版本此 API 的內部。
- **[若要設定攔截器，透過 App/Web.config 檔案的能力](~/ef6/fundamentals/configuring/config-file.md)** 允許新增，而不需要重新編譯應用程式的攔截器。
- **System.Data.Entity.Infrastructure.Interception.DatabaseLogger**是新的攔截器，可讓您輕鬆地記錄到檔案中的所有資料庫作業。 舊版的功能結合，這可讓您輕鬆地[開啟 部署的應用程式的資料庫作業的記錄](~/ef6/fundamentals/configuring/config-file.md)，而不需要重新編譯。
- **移轉模型變更偵測**已經過改良，以便包含 scaffold 的移轉會變得更精確; 也已經改良變更偵測程序的效能。
- **效能改進**在初始化期間降低的資料庫作業，包括 LINQ 查詢中的 null 的相等比較的最佳化更快速檢視層代 （模型建立） 的更多案例，且更有效率具有多個關聯的追蹤實體的具體化。

## <a name="ef-602"></a>EF 6.0.2
EF 6.0.2 執行階段已於 2013 年 12 月發行至 NuGet。
此修補程式版本僅限於解決問題所導入，因而在 EF6 版本 （因為 EF5 迴歸的效能/行為）。

## <a name="ef-601"></a>EF 6.0.1
EF 6.0.1 執行階段發行至 NuGet 年 10 月的 2013 同時使用 EF 6.0.0，因為後者內嵌在有鎖定之前的幾個月的 Visual Studio 的版本。
此修補程式版本僅限於解決問題所導入，因而在 EF6 版本 （因為 EF5 迴歸的效能/行為）。
最值得注意的變更已修正一些效能問題，在 EF 模型的熱身期間。
這是因為熱身效能是專注於 EF6 和這些問題已取消在 EF6 中所做的其他效能提升的一些重要的。

## <a name="ef-60"></a>EF 6.0
EF 6.0.0 執行階段已於 2013 年 10 月發行至 NuGet。
這是完整的 EF 執行階段包含在第一版[EntityFramework NuGet 套件](https://www.nuget.org/packages/EntityFramework/)其不需依賴 EF 的位元.NET Framework 的一部分。
將執行階段的其餘部分移至 NuGet 套件需要幾項重大變更現有的程式碼。
請參閱 〈[升級至 Entity Framework 6](upgrading-to-ef6.md)針對升級所需的手動步驟的更多詳細資料。

此版本包含許多新功能。
下列功能適用於使用 Code First 或 EF 設計工具建立的模型：

- **[非同步查詢和儲存](~/ef6/fundamentals/async.md)** .NET 4.5 中新增工作式非同步模式所導入的支援。
- **[連接恢復功能](~/ef6/fundamentals/connection-resiliency/retry-logic.md)** 可啟用從暫時性連接失敗的自動修復。
- **[程式碼為基礎的組態](~/ef6/fundamentals/configuring/code-based.md)** 可讓您選擇執行傳統上執行中的設定檔 – 組態 – 程式碼中。
- **[相依性解析](~/ef6/fundamentals/configuring/dependency-resolution.md)** 引進支援服務定位程式的模式，我們已細分可取代為自訂實作的功能的某些部分。
- **[攔截/SQL 記錄](~/ef6/fundamentals/logging-and-interception.md)** 用於攔截的 EF 作業中提供建置在上方的簡單 SQL 記錄的低階建置組塊。
- **測試能力改良**輕鬆地建立測試替身 DbContext 和 DbSet 時[使用的模擬架構](~/ef6/fundamentals/testing/mocking.md)或是[撰寫您自己測試雙精度浮點數](~/ef6/fundamentals/testing/writing-test-doubles.md)。
- **[您現在可以使用已經開啟 DbConnection 建立 DbContext](~/ef6/fundamentals/connection-management.md)** 讓其中很有幫助如果連接無法開啟，建立內容時的案例 (例如共用元件之間的連線位置您可以不保證連線狀態）。
- **[改善的交易支援](~/ef6/saving/transactions.md)** 提供架構，以及改善的方式，建立交易，以在架構中的外部交易的支援。
- **列舉、 空間和更佳的效能，在.NET 4.0** -藉由移動用來在.NET Framework 中 EF NuGet 封裝是我們現在就能夠從在.NET 上 EF5 提供列舉支援、 空間資料類型和效能增強功能的核心元件4.0。
- **更佳的效能 Enumerable.Contains 的 LINQ 查詢中**。
- **存留時間 （檢視表產生） 的改善的暖**，特別是針對大型模型。
- **隨插即用的複數&amp;單數服務**。
- **自訂實作 Equals 或 GetHashCode**現在支援在實體類別。
- **DbSet.AddRange/RemoveRange**提供最佳化的方式，以新增或移除集中的多個實體。
- **DbChangeTracker.HasChanges**提供簡單且有效率的方式來查看是否有任何暫止的變更儲存到資料庫。
- **SqlCeFunctions**提供 SQL Compact SqlFunctions 相等。

下列功能僅適用於第一個程式碼：

- **[自訂程式碼的第一個慣例](~/ef6/modeling/code-first/conventions/custom.md)** 允許撰寫自己的慣例，為了避免重複的組態。 我們提供可讓您撰寫更複雜的慣例 simple API for 輕量型的慣例，以及一些更複雜的建置組塊。
- **[第一個對應到 Insert/Update/Delete 預存程序的程式碼](~/ef6/modeling/code-first/fluent/cud-stored-procedures.md)** 現在支援。
- **[具有等冪性移轉指令碼](~/ef6/modeling/code-first/migrations/index.md)** 讓您產生可以在任何版本升級到最新版本的資料庫升級的 SQL 指令碼。
- **[可設定的移轉歷程記錄資料表](~/ef6/modeling/code-first/migrations/history-customization.md)** 可讓您自訂的移轉歷程記錄資料表的定義。 這是特別適用於需要適當的資料類型等，以指定才能正常運作的移轉歷程記錄資料表的資料庫提供者。
- **每個資料庫的多個內容**移除一個 Code First 模型，每個資料庫時使用移轉，或 Code First 自動建立資料庫時，先前的限制。
- **[DbModelBuilder.HasDefaultSchema](~/ef6/modeling/code-first/fluent/types-and-properties.md)** 是新的程式碼第一個 API，可讓 Code First 模型在一個位置中設定的預設資料庫結構描述。 先前的程式碼第一次的預設結構描述是硬式編碼為&quot;dbo&quot;和設定資料表所屬的結構描述的唯一方式是透過 ToTable API。
- **DbModelBuilder.Configurations.AddFromAssembly 方法**可讓您輕鬆地新增組件中定義，當您使用 Code First Fluent API 組態類別的所有組態類別。
- **[自訂的移轉作業](http://romiller.com/2013/02/27/ef6-writing-your-own-code-first-migration-operations/)** 讓您同時新增其他要用於您的程式碼型的移轉作業。
- **預設交易隔離等級變更為 READ_COMMITTED_SNAPSHOT**使用 Code First，允許更多的延展性和較少的死結 （deadlock） 所建立的資料庫。
- **實體和複雜類型現在可 nestedinside 類別**。 |

## <a name="ef-50"></a>EF 5.0
EF 5.0.0 執行階段已於 2012 年 8 月的發行至 NuGet。
此版本導入了一些新的功能，包括列舉支援、 資料表值函式、 空間資料類型和各種效能改良。

Entity Framework 設計工具，在 Visual Studio 2012 也引進支援多個-圖表每個模型，在設計介面和批次匯入預存程序的圖形的色彩標示。

以下是內容的我們匯集了專為 EF 5 版本清單。

-   [EF 5 版本的文章](http://blogs.msdn.com/b/adonet/archive/2012/08/15/ef5-released.aspx)
-   EF5 的新功能
    -   [列舉支援中的程式碼第一次](~/ef6/modeling/code-first/data-types/enums.md)
    -   [在 EF 設計工具中的列舉支援](~/ef6/modeling/designer/data-types/enums.md)
    -   [空間資料類型的程式碼在第一次](~/ef6/modeling/code-first/data-types/spatial.md)
    -   [在 EF 設計工具中的空間資料類型](~/ef6/modeling/designer/data-types/spatial.md)
    -   [空間類型的提供者支援](~/ef6/fundamentals/providers/spatial-support.md)
    -   [資料表值函式](~/ef6/modeling/designer/advanced/tvfs.md)
    -   [每個模型的多個圖表](~/ef6/modeling/designer/multiple-diagrams.md)
-   設定您的模型
    -   [建立模型](~/ef6/modeling/index.md)
    -   [連線和模型](~/ef6/fundamentals/configuring/connection-strings.md)
    -   [效能考量](~/ef6/fundamentals/performance/perf-whitepaper.md)
    -   [使用 Microsoft SQL Azure](~/ef6/fundamentals/connection-resiliency/retry-logic.md)
    -   [組態檔設定](~/ef6/fundamentals/configuring/config-file.md)
    -   [字彙](~/ef6/resources/glossary.md)
    -   Code First
        -   [Code First 至新的資料庫 （逐步解說和影片）](~/ef6/modeling/code-first/workflows/new-database.md)
        -   [Code First 至現有的資料庫 （逐步解說和影片）](~/ef6/modeling/code-first/workflows/existing-database.md)
        -   [慣例](~/ef6/modeling/code-first/conventions/built-in.md)
        -   [資料註解](~/ef6/modeling/code-first/data-annotations.md)
        -   [Fluent API-設定/對應屬性] & [型別](~/ef6/modeling/code-first/fluent/types-and-properties.md)
        -   [Fluent API-設定關聯性](~/ef6/modeling/code-first/fluent/relationships.md)
        -   [VB.NET 使用 Fluent API](~/ef6/modeling/code-first/fluent/vb.md)
        -   [Code First 移轉](~/ef6/modeling/code-first/migrations/index.md)
        -   [自動的 Code First 移轉](~/ef6/modeling/code-first/migrations/automatic.md)
        -   [Migrate.exe](~/ef6/modeling/code-first/migrations/migrate-exe.md)
        -   [定義 DbSets](~/ef6/modeling/code-first/dbsets.md)
    -   EF Designer
        -   [模型的第一個 （逐步解說和影片）](~/ef6/modeling/designer/workflows/model-first.md)
        -   [Database First （逐步解說和影片）](~/ef6/modeling/designer/workflows/database-first.md)
        -   [複雜類型](~/ef6/modeling/designer/data-types/complex-types.md)
        -   [關聯/關聯性](~/ef6/modeling/designer/relationships.md)
        -   [TPT 繼承模式](~/ef6/modeling/designer/inheritance/tpt.md)
        -   [TPH 繼承模式](~/ef6/modeling/designer/inheritance/tph.md)
        -   [預存程序的查詢](~/ef6/modeling/designer/stored-procedures/query.md)
        -   [多個結果集的預存程序](~/ef6/modeling/designer/advanced/multiple-result-sets.md)
        -   [插入、 更新和刪除預存程序](~/ef6/modeling/designer/stored-procedures/cud.md)
        -   [將實體對應至多個資料表 （實體分割）](~/ef6/modeling/designer/entity-splitting.md)
        -   [將多個實體對應至一個資料表 （資料表分割）](~/ef6/modeling/designer/table-splitting.md)
        -   [定義查詢](~/ef6/modeling/designer/advanced/defining-query.md)
        -   [程式碼產生範本](~/ef6/modeling/designer/codegen/index.md)
        -   [還原至 ObjectContext](~/ef6/modeling/designer/codegen/legacy-objectcontext.md)
-   使用您的模型
    -   [使用 DbContext](~/ef6/fundamentals/working-with-dbcontext.md)
    -   [尋找查詢實體](~/ef6/querying/index.md)
    -   [使用關聯性](~/ef6/fundamentals/relationships.md)
    -   [載入相關的實體](~/ef6/querying/related-data.md)
    -   [使用本機資料](~/ef6/querying/local-data.md)
    -   [多層式架構應用程式](~/ef6/fundamentals/disconnected-entities/index.md)
    -   [原始 SQL 查詢](~/ef6/querying/raw-sql.md)
    -   [開放式並行存取模式](~/ef6/saving/concurrency.md)
    -   [使用 Proxy](~/ef6/fundamentals/proxies.md)
    -   [自動偵測變更](~/ef6/saving/change-tracking/auto-detect-changes.md)
    -   [無追蹤查詢](~/ef6/querying/no-tracking.md)
    -   [Load 方法](~/ef6/querying/load-method.md)
    -   [新增/附加和實體狀態](~/ef6/saving/change-tracking/entity-state.md)
    -   [使用屬性值](~/ef6/saving/change-tracking/property-values.md)
    -   [使用 WPF (Windows Presentation Foundation) 資料繫結](~/ef6/fundamentals/databinding/wpf.md)
    -   [資料繫結與 WinForms (Windows Form)](~/ef6/fundamentals/databinding/winforms.md)

## <a name="ef-431"></a>EF 4.3.1
EF 4.3.1 執行階段於不久後 EF 4.3.0 年 2 月 2012年發行至 NuGet。
此修補程式版本包含 EF 4.3 版修正一些 bug，並引進更佳的客戶使用 EF 4.3 使用 Visual Studio 2012 的 LocalDB 支援。

以下是我們匯集了 EF 4.3.1 版本為內容的清單，大部分的 EF 4.1 所提供的內容仍會套用到 EF 4.3 以及。

-   [EF 4.3.1 版本部落格文章](http://blogs.msdn.com/b/adonet/archive/2012/02/29/ef4-3-1-and-ef5-beta-1-available-on-nuget.aspx)

## <a name="ef-43"></a>EF 4.3
EF 4.3.0 執行階段已於 2012 年 2 月的發行至 NuGet。
此版本包含新的 Code First 移轉功能，可讓 Code First 以累加方式變更隨著發展您的 Code First 模型所建立的資料庫。

以下是我們匯集了專為 EF 4.3 版的內容的清單，大部分的 EF 4.1 所提供的內容仍會套用到 EF 4.3 以及：
-   [EF 4.3 版本文章](http://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-released.aspx)
-   [EF 4.3 程式碼型的移轉逐步解說](http://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-code-based-migrations-walkthrough.aspx)
-   [EF 4.3 自動移轉的逐步解說](http://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-automatic-migrations-walkthrough.aspx)

## <a name="ef-42"></a>EF 4.2
EF 4.2.0 執行階段在 2011 年 11 月發行至 NuGet。
此版本包含 bug 修正，ef 4.1.1 版本。
因為這個版本只包含 bug 修正，可能是 EF 4.1.2 修補版本，但我們選擇可讓我們移離基礎日期，我們使用 4.1.x 中的修補程式版本號碼會釋放，並採用移到 4.2[語意 Versionsing](https://semver.org)語意化版本控制標準。

以下是我們匯集了專為 EF 4.2 版內容的清單，EF 4.1 所提供的內容仍會套用到 EF 4.2 以及。

-   [EF 4.2 版本文章](http://blogs.msdn.com/b/adonet/archive/2011/11/01/ef-4-2-released.aspx)
-   [程式碼的第一個逐步解說](http://blogs.msdn.com/b/adonet/archive/2011/09/28/ef-4-2-code-first-walkthrough.aspx)
-   [模型與資料庫的第一個逐步解說](http://blogs.msdn.com/b/adonet/archive/2011/09/28/ef-4-2-model-amp-database-first-walkthrough.aspx)

## <a name="ef-411"></a>EF 4.1.1
EF 4.1.10715 執行階段在 2011 年 7 月發行至 NuGet。
除了 bug 修正此修補程式版本導入一些元件，才能讓設計階段工具，來使用 Code First 模型更容易。
Code First 移轉 （隨附於 EF 4.3） 和 EF Power Tools 會使用這些元件。

您會發現奇怪的版本號碼 4.1.10715 的封裝。
我們用來使用日期基礎修補程式版本之前我們決定採用,[語意版本設定](https://semver.org)。
將此版本為 EF 4.1 修補程式 1 （或 EF 4.1.1）。

以下是我們幫助 4.1.1 內容的清單版本：

-   [EF 4.1.1 發行文章](http://blogs.msdn.com/b/adonet/archive/2011/07/25/ef-4-1-update-1-released.aspx)

## <a name="ef-41"></a>EF 4.1
EF 4.1.10331 執行階段所要發佈在 NuGet 上而在 2011 年 4 月的第一個。
此版本包含簡化的 DbContext API 與 Code First 的工作流程。

您會發現奇怪的版本號碼，4.1.10331，真的應該已 4.1。 此外還有 4.1.10311 應該已經過 4.1.0-rc 版本 （'rc' 代表 'release candidate'）。
我們用來使用日期基礎修補程式版本之前我們決定採用,[語意版本設定](https://semver.org)。

以下是我們匯集了 4.1 版內容的清單。 其中許多內容仍適用於最新版本的 Entity Framework:

-   [EF 4.1 版本文章](http://blogs.msdn.com/b/adonet/archive/2011/04/11/ef-4-1-released.aspx)
-   [程式碼的第一個逐步解說](http://blogs.msdn.com/b/adonet/archive/2011/03/15/ef-4-1-code-first-walkthrough.aspx)
-   [模型與資料庫的第一個逐步解說](http://blogs.msdn.com/b/adonet/archive/2011/03/15/ef-4-1-model-amp-database-first-walkthrough.aspx)
-   [SQL Azure 同盟和 Entity Framework](http://blogs.msdn.com/b/adonet/archive/2012/01/10/sql-azure-federations-and-the-entity-framework.aspx)

## <a name="ef-40"></a>EF 4.0
此版本中已包含在.NET Framework 4 和 Visual Studio 2010 中中的 2010 年 4 月。
重要的新功能，在此版本包含 POCO 支援、 外部索引鍵的對應、 消極式載入，測試能力改良，可自訂的程式碼產生和 Model First 的工作流程。

雖然它是 Entity Framework 的第二個版本，其命名為 EF 4，以配合其隨附的.NET Framework 版本。
在此版本中之後, 我們會啟動 nuget 提供 Entity Framework，並採用語意版本設定，因為我們已不再繫結至.NET Framework 版本。

請注意，某些後續版本的.NET Framework 有隨附包含 EF 位元的重大更新。
事實上，許多 EF 5.0 的新功能已實作為這些位元上的改進。
不過，為了 ef 合理化版本設定案例，我們繼續參考 EF 4.0 執行階段中，是.NET Framework 的一部分，而所有較新版本組成的 EF 位元[EntityFramework NuGet 套件](https://www.nuget.org/packages/EntityFramework/)。         

## <a name="ef-35"></a>EF 3.5
Entity Framework 的初始版本已包含在.NET 3.5 Service Pack 1 和 Visual Studio 2008 SP1，在 2008 年八月發行。
此版本提供基本的 O/RM 支援使用 Database First 的工作流程。
