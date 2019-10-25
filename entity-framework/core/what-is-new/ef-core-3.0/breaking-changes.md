---
title: EF Core 3.0 的中斷性變更 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: EE2878C9-71F9-4FA5-9BC4-60517C7C9830
uid: core/what-is-new/ef-core-3.0/breaking-changes
ms.openlocfilehash: b2e3881e3454377dab7851cba999ed6b891def4e
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72812131"
---
# <a name="breaking-changes-included-in-ef-core-30"></a>EF Core 3.0 中包含的重大變更
下列 API 和行為變更可能會在將現有的應用程式升級至3.0.0 時中斷。
這些變更預期只會影響[提供者變更](xref:core/providers/provider-log)底下記載的資料庫提供者。

## <a name="summary"></a>總結

| **重大變更**                                                                                               | **產生** |
|:------------------------------------------------------------------------------------------------------------------|------------|
| [不會再於用戶端評估 LINQ 查詢](#linq-queries-are-no-longer-evaluated-on-the-client)         | High       |
| [EF Core 3.0 以 .NET Standard 2.1 為目標，而非以 .NET Standard 2.0 為目標](#netstandard21) | High      |
| [EF Core 命令列工具 dotnet ef 不再是 .NET Core SDK 的一部分](#dotnet-ef) | High      |
| [DetectChanges 接受存放區產生的索引鍵值](#dc) | High      |
| [FromSql、ExecuteSql 和 ExecuteSqlAsync 已重新命名](#fromsql) | High      |
| [查詢類型已與實體類型合併](#qt) | High      |
| [Entity Framework Core 不再屬於 ASP.NET Core 共用架構](#no-longer) | Medium      |
| [根據預設，串聯刪除現在會立即發生](#cascade) | Medium      |
| [相關實體的積極式載入現在會出現在單一查詢中](#eager-loading-single-query) | Medium      |
| [DeleteBehavior.Restrict 具有更簡潔的語意](#deletebehavior) | Medium      |
| [自有類型關聯性的設定 API 已變更](#config) | Medium      |
| [各個屬性會使用獨立的記憶體內部整數索引鍵產生](#each) | Medium      |
| [無追蹤查詢已不再執行身分識別解析](#notrackingresolution) | Medium      |
| [中繼資料 API 變更](#metadata-api-changes) | Medium      |
| [提供者獨有的中繼資料 API 變更](#provider) | Medium      |
| [已移除 UseRowNumberForPaging](#urn) | Medium      |
| [無法撰寫與預存程式搭配使用時的 FromSql 方法](#fromsqlsproc) | Medium      |
| [FromSql 方法只能在查詢根目錄上指定](#fromsql) | 低      |
| [~~查詢執行會在偵錯層級記錄~~已還原](#qe) | 低      |
| [實體執行個體上不會再設定暫存索引鍵值](#tkv) | 低      |
| [現在可以選用與主體共用資料表的相依實體](#de) | 低      |
| [所有與並行語彙基元資料行共用資料表的實體，都必須將其對應到屬性](#aes) | 低      |
| [未對應類型的繼承屬性，現在會對應到所有衍生類型的單一資料行](#ip) | 低      |
| [外部索引鍵屬性慣例不會再比對與主體屬性相同的名稱](#fkp) | 低      |
| [如果在 TransactionScope 完成之前未再使用，資料庫連線現在會關閉](#dbc) | 低      |
| [根據預設，會使用支援欄位](#backing-fields-are-used-by-default) | 低      |
| [找到多個相容的支援欄位時擲回](#throw-if-multiple-compatible-backing-fields-are-found) | 低      |
| [僅欄位的屬性名稱應與欄位名稱相符](#field-only-property-names-should-match-the-field-name) | 低      |
| [AddDbContext/AddDbContextPool 再也不會呼叫 AddLogging 與 AddMemoryCache](#adddbc) | 低      |
| [DbContext.Entry 現在會執行本機 DetectChanges](#dbe) | 低      |
| [根據預設，字串和位元組陣列索引鍵不會由用戶端產生](#string-and-byte-array-keys-are-not-client-generated-by-default) | 低      |
| [ILoggerFactory 現在是限定範圍的服務](#ilf) | 低      |
| [消極式載入 Proxy 不再假設導覽屬性已完全載入](#lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded) | 低      |
| [現在根據預設，過度建立內部服務提供者會是錯誤](#excessive-creation-of-internal-service-providers-is-now-an-error-by-default) | 低      |
| [使用單一字串呼叫的 HasOne/HasMany 新行為](#nbh) | 低      |
| [數個非同步方法的傳回型別已從 Task 變更為 ValueTask](#rtnt) | 低      |
| [Relational:TypeMapping 註解現在變成只有 TypeMapping](#rtt) | 低      |
| [衍生類型上的 ToTable 會擲回例外狀況](#totable-on-a-derived-type-throws-an-exception) | 低      |
| [EF Core 不會再為 SQLite FK 強制傳送 pragma](#pragma) | 低      |
| [Microsoft.EntityFrameworkCore.Sqlite 現在相依於 SQLitePCLRaw.bundle_e_sqlite3](#sqlite3) | 低      |
| [GUID 值現在於 SQLite 上的儲存形式為 TEXT](#guid) | 低      |
| [Char 值現在於 SQLite 上的儲存形式為 TEXT](#char) | 低      |
| [移轉識別碼現在會使用不因文化特性而異的行事曆來產生](#migid) | 低      |
| [已從 IDbContextOptionsExtension 移除延伸模組資訊/中繼資料](#xinfo) | 低      |
| [已為 LogQueryPossibleExceptionWithAggregateOperator 重新命名](#lqpe) | 低      |
| [讓 API 的外部索引鍵限制式名稱更清楚](#clarify) | 低      |
| [IRelationalDatabaseCreator.HasTables/HasTablesAsync 已設為公用](#irdc2) | 低      |
| [Microsoft.EntityFrameworkCore.Design 現在是 DevelopmentDependency 套件](#dip) | 低      |
| [SQLitePCL.raw 已更新為 2.0.0 版](#SQLitePCL) | 低      |
| [NetTopologySuite 已更新為 2.0.0 版](#NetTopologySuite) | 低      |
| [SqlClient 是用來取代 SqlClient 的資料。](#SqlClient) | 低      |
| [必須設定多個不明確的自我參考關聯性](#mersa) | 低      |
| [DbFunction。架構為 null 或空字串，將其設定為模型的預設架構](#udf-empty-string) | 低      |

### <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a>不會再於用戶端評估 LINQ 查詢

[追蹤問題 #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[另請參閱問題 #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)

**舊行為**

3\.0 以前，在 EF Core 無法將屬於查詢的運算式轉換成 SQL 或參數時，它會自動在用戶端評估運算式。
根據預設，對可能相當耗費資源的運算式進行用戶端評估只會觸發警告。

**新行為**

從 3.0 開始，EF Core 只允許在用戶端評估最上層投影的運算式 (查詢中的最後一個 `Select()` 呼叫)。
當其他查詢部分中的運算式無法轉換成 SQL 或參數時，則會擲回例外狀況。

**原因**

查詢的自動用戶端評估可執行許多查詢，即使無法轉譯查詢的重要部分也一樣。
此行為可能會導致非預期且可能造成傷害的行為，而且該行為可能只會出現在生產環境中。
例如，`Where()` 呼叫中無法轉譯的條件可能會導致資料表中的所有資料列從資料庫伺服器移轉，並在用戶端套用篩選。
如果資料表只包含幾個開發中的資料列，此情況可能很容易未被察覺；但當應用程式移至生產環境時，由於資料表可能包含數百萬個資料列，因此影響會很大。
用戶端評估警告也證明很容易在開發期間遭到忽略。

此外，自動用戶端評估可能會導致改善特定運算式的查詢轉譯造成版本間非預期的中斷性變更問題。

**風險降低**

如果無法完整轉譯查詢，請以可轉譯的格式來重寫查詢，或是使用 `AsEnumerable()`、`ToList()` 或類似函數來明確將資料帶回用戶端，以便接著使用 LINQ-to-Objects 加以處理。

<a name="netstandard21"></a>
### <a name="ef-core-30-targets-net-standard-21-rather-than-net-standard-20"></a>EF Core 3.0 以 .NET Standard 2.1 為目標，而非以 .NET Standard 2.0 為目標

[追蹤問題 #15498](https://github.com/aspnet/EntityFrameworkCore/issues/15498)

**舊行為**

在 3.0 之前，EF Core 以 .NET Standard 2.0 為目標，且執行於支援該標準的所有平台上，包括 .NET Framework。

**新行為**

從 3.0 開始，EF Core 以 .NET Standard 2.1 為目標，且執行於支援此標準的所有平台上。 這不包括 .NET Framework。

**原因**

此為跨 .NET 技術的策略性決策之一部分，著重於 .NET Core 與其他現代化 .NET 平台 (例如 Xamarin) 的能力。

**風險降低**

請考慮移至現代化的 .NET 平台。 如果無法如此做，請繼續使用 EF Core 2.1 或 EF Core 2.2，這兩者皆支援 .NET Framework。

<a name="no-longer"></a>
### <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a>Entity Framework Core 不再屬於 ASP.NET Core 共用架構

[追蹤問題 Announcements#325](https://github.com/aspnet/Announcements/issues/325)

**舊行為**

在 ASP.NET Core 3.0 以前，當您新增 `Microsoft.AspNetCore.App` 或 `Microsoft.AspNetCore.All` 的套件參考時，它會包含 EF Core 及部分 EF Core 資料提供者 (例如 SQL Server 提供者)。

**新行為**

從 3.0 開始，ASP.NET Core 共用架構不會包含 EF Core 或任何 EF Core 資料提供者。

**原因**

在這項變更之前，取得 EF Core 會根據應用程式是否以 ASP.NET Core 和 SQL Server 為目標而需要不同的步驟。 此外，升級 ASP.NET Core 會強制升級 EF Core 和 SQL Server 提供者，這不一定符合需求。

透過這項變更，取得 EF Core 的體驗對所有提供者、支援的 .NET 實作和應用程式類型都相同。
開發人員現在也可以精確控制何時升級 EF Core 和 EF Core 資料提供者。

**風險降低**

若要在 ASP.NET Core 3.0 應用程式或其他支援的應用程式中使用 EF Core，請明確將套件參考加入應用程式會使用的 EF Core 資料庫提供者。

<a name="dotnet-ef"></a>
### <a name="the-ef-core-command-line-tool-dotnet-ef-is-no-longer-part-of-the-net-core-sdk"></a>EF Core 命令列工具 dotnet ef 不再是 .NET Core SDK 的一部分

[追蹤問題 #14016](https://github.com/aspnet/EntityFrameworkCore/issues/14016)

**舊行為**

在 3.0 之前，`dotnet ef` 工具包含在 .NET Core SDK，並可以輕易地從任何專案的命令列使用，而不需要額外步驟。 

**新行為**

從 3.0 開始，.NET SDK 不包含 `dotnet ef` 工具，因此您必須明確地將它安裝為本機或全域工具才能使用。 

**原因**

這項變更可讓我們將 `dotnet ef` 當作 NuGet 上一般的 .NET CLI 工具來散發和更新，這點與 EF Core 3.0 一律當作 NuGet 套件散發的事實一致。

**風險降低**

若要能夠管理移轉或支撐 `DbContext`，請安裝 `dotnet-ef` 作為全域工具：

  ``` console
    $ dotnet tool install --global dotnet-ef
  ```

您也可以在還原專案相依性時取得它作為本機工具 (該專案是使用[工具資訊清單檔](https://github.com/dotnet/cli/issues/10288)將它宣告為工具相依性)。

<a name="fromsql"></a>
### <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a>FromSql、ExecuteSql 和 ExecuteSqlAsync 已重新命名

[追蹤問題 #10996](https://github.com/aspnet/EntityFrameworkCore/issues/10996)

**舊行為**

在 EF Core 3.0 之前，這些方法名稱已多載以使用一般字串，或應插入至 SQL 和參數的字串。

**新行為**

從 EF Core 3.0 開始，請使用 `FromSqlRaw`、`ExecuteSqlRaw` 和 `ExecuteSqlRawAsync` 建立參數化查詢，其中參數會分別從查詢字串傳遞。
例如:

```C#
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

使用 `FromSqlInterpolated`、`ExecuteSqlInterpolated` 和 `ExecuteSqlInterpolatedAsync` 建立參數化查詢，其中參數會作為插入查詢字串的一部分傳回。
例如:

```C#
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

請注意，上述兩個查詢都會產生具有相同 SQL 參數的相同參數化 SQL。

**原因**

像這樣的方法多載，使得原本要呼叫插入字串方法很容易意外呼叫原始字串方法，或反之。
這可能會導致查詢在應該參數化時不進行參數化。

**風險降低**

切換至使用新的方法名稱。

<a name="fromsqlsproc"></a>
### <a name="fromsql-method-when-used-with-stored-procedure-cannot-be-composed"></a>無法撰寫與預存程式搭配使用時的 FromSql 方法

[追蹤問題 #15392](https://github.com/aspnet/EntityFrameworkCore/issues/15392)

**舊行為**

在 EF Core 3.0 之前，FromSql 方法會嘗試偵測是否可以根據傳遞的 SQL 來進行撰寫。 當 SQL 不是可組合的，如同預存程式，它會進行用戶端評估。 下列查詢的運作方式是在伺服器上執行預存程式，並在用戶端上進行 FirstOrDefault。

```C#
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").FirstOrDefault();
```

**新行為**

從 EF Core 3.0 開始，EF Core 不會嘗試剖析 SQL。 因此，如果您在 FromSqlRaw/FromSqlInterpolated 之後撰寫，則 EF Core 會藉由導致子查詢來撰寫 SQL。 因此，如果您使用具有組合的預存程式，則會收到無效 SQL 語法的例外狀況。

**原因**

EF Core 3.0 不支援自動用戶端評估，因為它容易發生錯誤，如[這裡](#linq-queries-are-no-longer-evaluated-on-the-client)所述。

**緩解**

如果您使用 FromSqlRaw/FromSqlInterpolated 中的預存程式，您就知道它無法由撰寫，因此您可以在 FromSql 方法呼叫之後加入__enumerable.asenumerable/AsAsyncEnumerable__ ，以避免伺服器端上的任何組合。

```C#
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").AsEnumerable().FirstOrDefault();
```

<a name="fromsql"></a>

### <a name="fromsql-methods-can-only-be-specified-on-query-roots"></a>FromSql 方法只能在查詢根目錄上指定

[追蹤問題 #15704](https://github.com/aspnet/EntityFrameworkCore/issues/15704)

**舊行為**

在 EF Core 3.0 之前，可以在查詢中的任何位置指定 `FromSql` 方法。

**新行為**

從 EF Core 3.0 開始，新的 `FromSqlRaw` 與 `FromSqlInterpolated` 方法 (取代 `FromSql`) 只能在查詢根目錄上指定，亦即直接在 `DbSet<>` 上指定。 嘗試在其他任何位置指定它們，將會導致編譯錯誤。

**原因**

在 `DbSet` 以外的任何地方指定 `FromSql` 沒有新增的意義或附加價值，而且在某些情況下可能會導致模稜兩可。

**風險降低**

`FromSql` 引動過程應該直接移至它們適用的 `DbSet`。

<a name="notrackingresolution"></a>
### <a name="no-tracking-queries-no-longer-perform-identity-resolution"></a>無追蹤查詢已不再執行身分識別解析

[追蹤問題 #13518](https://github.com/aspnet/EntityFrameworkCore/issues/13518)

**舊行為**

在 EF Core 3.0 之前，每次出現具有給定類型與識別碼的實體時，皆會使用相同的實體執行個體。 如此符合追蹤查詢的行為。 例如，下列查詢：

```C#
var results = context.Products.Include(e => e.Category).AsNoTracking().ToList();
```
會為每個與給定類別相關聯的 `Product`，傳回相同的 `Category` 執行個體。

**新行為**

從 EF Core 3.0 開始，當具有給定類型與識別碼的實體，出現在傳回圖形的不同位置時，將會建立不同的實體執行個體。 例如，即使當兩個產品與相同的類別相關聯，上述查詢現在會為每個 `Category` 傳回新的 `Product` 執行個體。

**原因**

身分識別解析 (也就是，決定實體與之前所發生的實體具有相同的類型與識別碼) 會加入額外的效能與記憶體負荷。 這通常會執行為何一開始就使用無追蹤查詢的計數器。 此外，雖然身分識別解析有時非常有用，但若實體要序列化並會傳送給用戶端 (對無追蹤查詢而言很常見)，則不需要。

**風險降低**

若需要身分識別解析，請使用追蹤查詢。

<a name="qe"></a>

### <a name="query-execution-is-logged-at-debug-level-reverted"></a>~~查詢執行會在偵錯層級記錄~~已還原

[追蹤問題 #14523](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

我們還原此變更的原因是 EF Core 3.0 中的新設定允許應用程式指定任何事件的記錄層級。 例如，若要將 SQL 的記錄切換到 `Debug`，請明確地在 `OnConfiguring` 或 `AddDbContext` 中設定層級：
```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Debug)));
```

<a name="tkv"></a>

### <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a>實體執行個體上不會再設定暫存索引鍵值

[追蹤問題 #12378](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

**舊行為**

在 EF Core 3.0 以前，會對所有索引鍵屬性指派暫存值，這些屬性稍後會有資料庫產生的實值。
這些暫存值通常是龐大的負值。

**新行為**

從 3.0 開始，EF Core 會將暫存索引鍵值儲存為實體追蹤資訊的一部分，至於索引鍵屬性本身則保持不變。

**原因**

這項變更的目的是為了防止在將某個 `DbContext` 執行個體先前追蹤的實體移至不同的 `DbContext` 執行個體時，錯誤地把暫存索引鍵值變成永久值。 

**風險降低**

若應用程式會將主索引鍵指派給外部索引鍵以形成實體間關聯，則可能會在主索引鍵是由存放區產生並屬於 `Added` 狀態的實體時採用舊行為。
這可透過下列方式來避免：
* 不使用存放區產生的索引鍵。
* 設定導覽屬性以形成關聯性，而不是設定外部索引鍵值。
* 從實體的追蹤資訊取得實際暫存索引鍵值。
例如，`context.Entry(blog).Property(e => e.Id).CurrentValue` 會傳回暫存值，即使尚未設定 `blog.Id` 本身也一樣。

<a name="dc"></a>

### <a name="detectchanges-honors-store-generated-key-values"></a>DetectChanges 接受存放區產生的索引鍵值

[追蹤問題 #14616](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

**舊行為**

在 EF Core 3.0 以前，`DetectChanges` 所發現未被追蹤的實體會以 `Added` 狀態追蹤，並在呼叫 `SaveChanges` 時以新的資料列插入。

**新行為**

從 EF Core 3.0 開始，如果實體使用產生的索引鍵值並已設定一些索引鍵值，則實體會以 `Modified` 狀態追蹤。
這表示實體的資料列假設存在，而且會在呼叫 `SaveChanges` 時更新。
如果未設定索引鍵值，或者如果實體類型未使用產生的索引鍵，則新的實體仍會如同舊版以 `Added` 追蹤。

**原因**

這項變更的目的是為了更輕鬆一致地使用中斷連接的實體圖形，同時使用存放區產生的索引鍵。

**風險降低**

如果實體類型已設定為使用產生的索引鍵，但針對新的執行個體明確設定了索引鍵值，這項變更可能會中斷應用程式。
修正方法是明確設定索引鍵屬性不使用產生的值。
例如，使用 Fluent API：

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

或者，使用資料註解：

```C#
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```
<a name="cascade"></a>
### <a name="cascade-deletions-now-happen-immediately-by-default"></a>現在預設會立即發生串聯刪除

[追蹤問題 #10114](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

**舊行為**

在 3.0 以前，除非呼叫 SaveChanges，否則 EF Core 不會套用串聯動作 (刪除必要主體或提供必要主體關聯性時刪除相依實體)。

**新行為**

從 3.0 開始，EF Core 會在偵測到觸發條件時立即套用串聯動作。
例如，呼叫 `context.Remove()` 刪除主要實體會導致所有追蹤的相關必要相依項目也會立即設定為 `Deleted`。

**原因**

這項變更的目的是為了改善資料繫結和稽核情節的體驗，在這些情節中了解呼叫 `SaveChanges`「之前」將刪除哪些實體是很重要的。

**風險降低**

透過設定 `context.ChangedTracker` 可以還原舊行為。
例如:

```C#
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```
<a name="eager-loading-single-query"></a>
### <a name="eager-loading-of-related-entities-now-happens-in-a-single-query"></a>相關實體的積極式載入現在會出現在單一查詢中

[追蹤問題 #18022](https://github.com/aspnet/EntityFrameworkCore/issues/18022)

**舊行為**

在3.0 之前，立即透過 `Include` 運算子載入集合導覽，會導致在關係資料庫上產生多個查詢，每個相關實體類型各一個。

**新行為**

從3.0 開始，EF Core 會在關係資料庫上產生具有聯結的單一查詢。

**原因**

發出多個查詢來執行單一 LINQ 查詢，會造成許多問題，包括負面效能，因為需要多個資料庫往返，而當每個查詢可能觀察到資料庫的不同狀態時，就會發生資料一致性問題。

**風險降低**

雖然技術上來說這不是重大變更，但當單一查詢在集合導覽上包含大量的 `Include` 運算子時，可能會對應用程式效能造成相當大的影響。 如需詳細資訊和以更有效率的方式重寫查詢，[請參閱此批註](https://github.com/aspnet/EntityFrameworkCore/issues/18022#issuecomment-542397085)。

**

<a name="deletebehavior"></a>
### <a name="deletebehaviorrestrict-has-cleaner-semantics"></a>DeleteBehavior.Restrict 具有更簡潔的語意

[追蹤問題 #12661](https://github.com/aspnet/EntityFrameworkCore/issues/12661)

**舊行為**

在 3.0 以前，`DeleteBehavior.Restrict` 使用 `Restrict` 語意在資料庫中建立外部索引鍵，但也以不明顯的方式變更內部修復。

**新行為**

從 3.0 開始，`DeleteBehavior.Restrict` 會確保外部索引鍵使用 `Restrict` 語意來建立 (也就是不會有重疊顯示，拋出條件約束違規)，不會影響 EF 內部修復。

**原因**

這項變更可藉由直覺方式提升使用 `DeleteBehavior` 的體驗，而不會發生非預期的副作用。

**風險降低**

使用 `DeleteBehavior.ClientNoAction` 可以還原舊行為。

<a name="qt"></a>
### <a name="query-types-are-consolidated-with-entity-types"></a>查詢類型會與實體類型合併

[追蹤問題 #14194](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

**舊行為**

在 EF Core 3.0 以前，[查詢類型](xref:core/modeling/keyless-entity-types)可讓您查詢未以結構化方式定義主索引鍵的資料。
換句話說，查詢類型是用於對應沒有索引鍵的實體類型 (較有可能來自檢視，但也有可能來自資料表)，而一般實體類型是用於索引鍵可供使用時 (較有可能來自資料表，但也有可能來自檢視)。

**新行為**

查詢類型現在會成為沒有主索引鍵的實體類型。
無索引鍵的實體類型功能與舊版查詢類型相同。

**原因**

這項變更的目的是為了降低查詢類型用途的混淆。
具體來說，它們是無索引鍵的實體類型，因此本質上是唯讀的，但不應該只因為實體類型必須是唯讀就使用。
同樣地，它們通常會對應至檢視，但這只是因為檢視通常未定義索引鍵。

**風險降低**

API 的下列組件現已淘汰：
* **`ModelBuilder.Query<>()`** - 必須改為呼叫 `ModelBuilder.Entity<>().HasNoKey()` 將實體類型標示為沒有索引鍵。
為了避免在必須有主索引鍵但不符合慣例時設定錯誤，目前仍未將此設定為慣例。
* **`DbQuery<>`** - 應改用 `DbSet<>`。
* **`DbContext.Query<>()`** - 應改用 `DbContext.Set<>()`。

<a name="config"></a>
### <a name="configuration-api-for-owned-type-relationships-has-changed"></a>自有類型關聯性的設定 API 已變更

[追蹤問題 #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[追蹤問題 #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[追蹤問題 #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)

**舊行為**

在 EF Core 3.0 以前，會在呼叫 `OwnsOne` 或 `OwnsMany` 之後直接執行自有關聯性的設定。 

**新行為**

從 EF Core 3.0 開始，現在會有 Fluent API 使用 `WithOwner()` 將導覽屬性設定為擁有者。
例如:

```C#
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

擁有者與自有之間關聯性的相關設定現在應該在 `WithOwner()` 之後鏈結，類似於其他關聯性的設定方式。
但自有類型本身的設定仍會在 `OwnsOne()/OwnsMany()` 之後鏈結。
例如:

```C#
modelBuilder.Entity<Order>.OwnsOne(e => e.Details, eb =>
    {
        eb.WithOwner()
            .HasForeignKey(e => e.AlternateId)
            .HasConstraintName("FK_OrderDetails");
            
        eb.ToTable("OrderDetails");
        eb.HasKey(e => e.AlternateId);
        eb.HasIndex(e => e.Id);

        eb.HasOne(e => e.Customer).WithOne();

        eb.HasData(
            new OrderDetails
            {
                AlternateId = 1,
                Id = -1
            });
    });
```

此外，使用自有類型目標呼叫 `Entity()`、`HasOne()` 或 `Set()` 現在會擲回例外狀況。

**原因**

這項變更的目的是為了更清楚地劃分設定自有類型本身，以及設定自有類型的「關聯性」。
如此可避免 `HasForeignKey` 等方法的模稜兩可和混淆。

**風險降低**

將自有類型關聯性的設定變更為使用新的 API 介面，如上述範例所示。

<a name="de"></a>

### <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a>現在可選用以主體來共用資料表的相依實體

[追蹤問題 #9005](https://github.com/aspnet/EntityFrameworkCore/issues/9005)

**舊行為**

請考慮下列模型：
```C#
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```
在 EF Core 3.0 之前，如果 `OrderDetails` 由 `Order` 擁有，或明確對應至相同的資料表，則在新增新的 `Order` 時一律需要 `OrderDetails` 執行個體。


**新行為**

從 3.0 開始，EF 允許新增 `Order` 而不需要 `OrderDetails`，並會對應所有 `OrderDetails` 屬性，除了可為 Null 之資料行的主索引鍵以外。
查詢時，如果任何必要的屬性不具有值，或如果其具有主索引鍵以外的不必要屬性，且所有屬性都是 `null`，則 EF Core 會將 `OrderDetails` 設為 `null`。

**風險降低**

如果您的模型具有與所有選擇性資料行共用相依資料表，但指向該資料表的導覽不預期為 `null`，則應修改應用程式，以處理當導覽為 `null` 時的情況。 如果這不可行，則應將必要屬性新增至實體類型，或至少應有一個屬性指派其非 `null` 的值。

<a name="aes"></a>

### <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a>共用具有並行語彙基元資料行的所有實體，都必須將其對應至屬性

[追蹤問題 #14154](https://github.com/aspnet/EntityFrameworkCore/issues/14154)

**舊行為**

請考慮下列模型：
```C#
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public byte[] Version { get; set; }
    public OrderDetails Details { get; set; }
}

public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Order>()
        .Property(o => o.Version).IsRowVersion().HasColumnName("Version");
}
```
在 EF Core 3.0之前，如果 `OrderDetails` 由 `Order` 擁有，或明確對應至相同資料表，那麼僅更新 `OrderDetails` 將不會更新用戶端上的 `Version` 值，且下一次更新將會失敗。


**新行為**

從 3.0 開始，EF Core 會將新的 `Version` 值傳播至 `Order` (如果其擁有 `OrderDetails`)。 否則，在模型驗證期間會擲回例外狀況。

**原因**

這項變更的目的，是為了避免在僅更新對應至相同資料表的其中一個實體時，出現過時的並行語彙基元值。

**風險降低**

共用資料表的所有實體，都必須包含對應至並行語彙基元資料行的屬性。 在陰影狀態中建立一個是可能的：
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

<a name="ip"></a>

### <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a>未對應類型的繼承屬性，現在會對應至所有衍生類型的單一資料行

[追蹤問題 #13998](https://github.com/aspnet/EntityFrameworkCore/issues/13998)

**舊行為**

請考慮下列模型：
```C#
public abstract class EntityBase
{
    public int Id { get; set; }
}

public abstract class OrderBase : EntityBase
{
    public int ShippingAddress { get; set; }
}

public class BulkOrder : OrderBase
{
}

public class Order : OrderBase
{
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Ignore<OrderBase>();
    modelBuilder.Entity<EntityBase>();
    modelBuilder.Entity<BulkOrder>();
    modelBuilder.Entity<Order>();
}
```

在 EF Core 3.0 之前，`ShippingAddress` 屬性會根據預設，為 `BulkOrder` 和 `Order` 對應至個別資料行。

**新行為**

從 3.0 開始，EF Core 只會為 `ShippingAddress` 建立一個資料行。

**原因**

舊行為是非預期的。

**風險降低**

屬性仍可以在衍生類型上明確對應至個別資料行：

```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Ignore<OrderBase>();
    modelBuilder.Entity<EntityBase>();
    modelBuilder.Entity<BulkOrder>()
        .Property(o => o.ShippingAddress).HasColumnName("BulkShippingAddress");
    modelBuilder.Entity<Order>()
        .Property(o => o.ShippingAddress).HasColumnName("ShippingAddress");
}
```

<a name="fkp"></a>

### <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a>外部索引鍵屬性慣例不會再比對與主體屬性相同的名稱

[追蹤問題 #13274](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

**舊行為**

請考慮下列模型：
```C#
public class Customer
{
    public int CustomerId { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```
在 EF Core 3.0 以前，`CustomerId` 屬性依照慣例會用於外部索引鍵。
不過，如果 `Order` 是自有類型，則這也會將 `CustomerId` 設為主索引鍵，而這通常不符合預期。

**新行為**

從 3.0 開始，如果屬性的名稱與主體屬性相同，依照慣例，EF Core 不會嘗試將屬性用於外部索引鍵。
但仍會比對與主體屬性名稱串連的主體類型名稱，以及與主體屬性名稱模式串連的導覽名稱。
例如:

```C#
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```

```C#
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int BuyerId { get; set; }
    public Customer Buyer { get; set; }
}
```

**原因**

這項變更的目的是為了避免錯誤地在自有類型上定義主索引鍵屬性。

**風險降低**

如果屬性預定會作為外部索引鍵，並因此成為主索引鍵的一部分，請明確進行這類設定。

<a name="dbc"></a>

### <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a>如果在 TransactionScope 完成之前未再使用，則資料庫連線現在已關閉

[追蹤問題 #14218](https://github.com/aspnet/EntityFrameworkCore/issues/14218)

**舊行為**

在 EF Core 3.0 之前，如果內容在 `TransactionScope` 內開啟連線，則當目前 `TransactionScope` 處於作用中時，連線將保持開啟。

```C#
using (new TransactionScope())
{
    using (AdventureWorks context = new AdventureWorks())
    {
        context.ProductCategories.Add(new ProductCategory());
        context.SaveChanges();

        // Old behavior: Connection is still open at this point
        
        var categories = context.ProductCategories().ToList();
    }
}
```

**新行為**

從 3.0 開始，EF Core 在使用完連線後會將其關閉。

**原因**

此變更允許在相同 `TransactionScope` 中使用多個內容。 新的行為也符合 EF6。

**風險降低**

如果連線需要保持開啟，則明確呼叫 `OpenConnection()` 可確保 EF Core 不會過早將其關閉：

```C#
using (new TransactionScope())
{
    using (AdventureWorks context = new AdventureWorks())
    {
        context.Database.OpenConnection();
        context.ProductCategories.Add(new ProductCategory());
        context.SaveChanges();
        
        var categories = context.ProductCategories().ToList();
        context.Database.CloseConnection();
    }
}
```

<a name="each"></a>

### <a name="each-property-uses-independent-in-memory-integer-key-generation"></a>每個屬性會使用獨立的記憶體內部整數索引鍵產生

[追蹤問題 #6872](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

**舊行為**

在 EF Core 3.0 以前，會針對所有記憶體內部整數索引鍵屬性使用一個共用值產生器。

**新行為**

從 EF Core 3.0 開始，當使用記憶體內部資料庫時，每個整數索引鍵屬性都會取得自己的值產生器。
此外，如果已刪除資料庫，則會重設所有資料表的索引鍵產生。

**原因**

這項變更的目的是為了讓記憶體內部索引鍵產生與實際資料庫索引鍵產生更加一致，並改善在使用記憶體內部資料庫時隔離個別測試的能力。

**風險降低**

這可能會中斷需要設定特定記憶體內部索引鍵值的應用程式。
請考慮改為不依賴特定索引鍵值，或更新以符合新行為。

### <a name="backing-fields-are-used-by-default"></a>預設會使用支援欄位

[追蹤問題 #12430](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

**舊行為**

在 3.0 以前，即使屬性的支援欄位已知，EF Core 預設仍會使用屬性 getter 和 setter 方法來讀取和寫入屬性值。
例外是查詢執行，其中如果支援欄位已知，則會直接設定。

**新行為**

從 EF Core 3.0 開始，如果屬性的支援欄位已知，則 EF Core 會一律使用支援欄位來讀取和寫入該屬性。
如果應用程式需要將額外的行為編碼到 getter 或 setter 方法中，這可能會導致應用程式中斷。

**原因**

這項變更的目的是為了防止 EF Core 預設在執行涉及實體的資料庫作業時，錯誤地觸發商務邏輯。

**風險降低**

透過在 `ModelBuilder` 上設定屬性存取模式可以還原 3.0 以前的行為。
例如:

```C#
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

### <a name="throw-if-multiple-compatible-backing-fields-are-found"></a>找到多個相容的支援欄位時擲回

[追蹤問題 #12523](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

**舊行為**

在 EF Core 3.0 以前，如果有多個欄位符合尋找屬性支援欄位的規則，則會根據特定優先順序來選擇一個欄位。
這可能會導致在模稜兩可的情況下使用錯誤的欄位。

**新行為**

從 EF Core 3.0 開始，如果有多個欄位符合相同的屬性，則會擲回例外狀況。

**原因**

這項變更的目的是為了避免在只能有一個正確欄位的情況下，自動使用某個欄位而非另一個欄位。

**風險降低**

若屬性的支援欄位模稜兩可，則必須明確指定要使用的欄位。
例如，使用 Fluent API：

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

### <a name="field-only-property-names-should-match-the-field-name"></a>僅限欄位的屬性名稱應與欄位名稱相符

**舊行為**

在 EF Core 3.0 之前，屬性可以由字串值指定，而且如果在 .NET 類型上找不到具有該名稱的屬性，則 EF Core 會嘗試使用慣例規則將它與欄位進行比對。
```C#
private class Blog
{
    private int _id;
    public string Name { get; set; }
}
```
```C#
modelBuilder
    .Entity<Blog>()
    .Property("Id");
```

**新行為**

從 EF Core 3.0 開始，僅限欄位的屬性必須與欄位名稱完全相符。

```C#
modelBuilder
    .Entity<Blog>()
    .Property("_id");
```

**原因**

此變更是為了避免對兩個名稱相似的屬性使用相同欄位，也使僅限欄位屬性之比對規則與對應至 CLR 屬性的屬性相同。

**風險降低**

僅限欄位屬性必須命名為與其所對應欄位相同的名稱。
在3.0 以後的 EF Core 版本中，我們計畫重新啟用明確設定與屬性名稱不同的功能變數名稱（請參閱問題[#15307](https://github.com/aspnet/EntityFrameworkCore/issues/15307)）：

```C#
modelBuilder
    .Entity<Blog>()
    .Property("Id")
    .HasField("_id");
```

<a name="adddbc"></a>

### <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a>AddDbContext/AddDbContextPool 再也不會呼叫 AddLogging 與 AddMemoryCache

[追蹤問題 #14756](https://github.com/aspnet/EntityFrameworkCore/issues/14756)

**舊行為**

在 EF Core 3.0 之前，呼叫 `AddDbContext` 或 `AddDbContextPool` 也會透過呼叫 [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) 與 [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) 來向 DI 註冊記錄與記憶體快取服務。

**新行為**

從 EF Core 3.0 開始，`AddDbContext` 與 `AddDbContextPool` 再也不會向相依性插入 (DI) 註冊這些服務。

**原因**

EF Core 3.0 不會要求這些服務必須存在於應用程式的 DI 容器中。 不過，若 `ILoggerFactory` 已在應用程式的 DI 容器中註冊，則它仍會被 EF Core 使用。

**風險降低**

若您的應用程式需要這些服務，請使用  [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) 或 [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) 明確地向 DI 容器註冊它們。

<a name="dbe"></a>

### <a name="dbcontextentry-now-performs-a-local-detectchanges"></a>DbContext.Entry 現在會執行本機 DetectChanges

[追蹤問題 #13552](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

**舊行為**

在 EF Core 3.0 以前，呼叫 `DbContext.Entry` 會導致偵測所有追蹤實體的變更。
這可確保在 `EntityEntry` 中公開的狀態為最新狀態。

**新行為**

從 EF Core 3.0 開始，呼叫 `DbContext.Entry` 現在只會嘗試在指定實體及其相關的任何追蹤主要實體中偵測變更。
這表示呼叫此方法可能還無法偵測到其他位置的變更，因此可能會影響應用程式狀態。

請注意，如果 `ChangeTracker.AutoDetectChangesEnabled` 設定為 `false`，甚至是此本機變更偵測都會停用。

其他導致變更偵測的方法 (例如 `ChangeTracker.Entries` 和 `SaveChanges`) 仍會對所有追蹤實體進行完整的 `DetectChanges`。

**原因**

這項變更的目的是為了改善使用 `context.Entry` 的預設效能。

**風險降低**

在呼叫 `Entry` 之前明確呼叫 `ChgangeTracker.DetectChanges()` 可確保 3.0 以前的行為。

### <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a>字串和位元組陣列索引鍵預設不是由用戶端產生

[追蹤問題 #14617](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

**舊行為**

在 EF Core 3.0 以前，可以使用 `string` 和 `byte[]` 索引鍵屬性，而不需要明確設定非 Null 值。
在此情況下，會在用戶端以 GUID 形式產生索引鍵值，再序列化為 `byte[]` 的位元組。

**新行為**

從 EF Core 3.0 開始，系統會擲回例外狀況，指出尚未設定任何索引鍵值。

**原因**

這項變更是因為用戶端產生的 `string`/`byte[]` 值通常不太有用，而且預設行為使它很難以一般方式來推論產生的索引鍵值。

**風險降低**

藉由明確指定索引鍵屬性應該在未設定其他非 Null 值時使用產生的值，即可取得 3.0 以前的行為。
例如，使用 Fluent API：

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

或者，使用資料註解：

```C#
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

<a name="ilf"></a>

### <a name="iloggerfactory-is-now-a-scoped-service"></a>ILoggerFactory 現在是限定範圍的服務

[追蹤問題 #14698](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

**舊行為**

在 EF Core 3.0 以前，`ILoggerFactory` 會註冊為單一服務。

**新行為**

從 EF Core 3.0 開始，`ILoggerFactory` 現在會註冊為限定範圍。

**原因**

這項變更的目的是為了允許記錄器與 `DbContext` 執行個體產生關聯，這可啟用其他功能，並避免某些異常行為案例，例如內部服務提供者遽增。

**風險降低**

這項變更不應影響應用程式程式碼，除非在 EF Core 內部服務提供者上使用自訂服務註冊該程式碼。
但這並不常見。
在這些情況下，大部分的項目仍會運作，但相依於 `ILoggerFactory` 的任何單一服務需要變更，才能以不同方式取得 `ILoggerFactory`。

如果您遇到上述情況，請在 [EF Core GitHub 問題追蹤器](https://github.com/aspnet/EntityFrameworkCore/issues)上提出問題，讓我們知道您使用 `ILoggerFactory` 的方式，以便進一步了解未來如何才不會再次中斷。

### <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a>消極式載入 Proxy 停止假設導覽屬性已完全載入

[追蹤問題 #12780](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

**舊行為**

在 EF Core 3.0 以前，一旦處置 `DbContext` 之後，就無從得知實體上取自該內容的指定導覽屬性是否已完全載入。
Proxy 會改為假設如有非 Null 值，會載入參考導覽；如果不是空的，則會載入集合導覽。
在這些情況下，嘗試消極式載入不會執行任何作業。

**新行為**

從 EF Core 3.0 開始，Proxy 會追蹤是否載入導覽屬性。
這表示嘗試存取在處置內容之後載入的導覽屬性一律不會執行任何作業，即使已載入的導覽是空的或 Null 也一樣。
相反地，如果在處置內容之後嘗試存取未載入的導覽屬性，則會擲回例外狀況，即使導覽屬性不是空集合也一樣。
如果發生這種情況，則表示應用程式程式碼嘗試在無效的時間使用消極式載入，應用程式應該變更為不要這麼做。

**原因**

這項變更的目的是為了在已處置的 `DbContext` 執行個體上嘗試消極式載入時，使行為一致且正確。

**風險降低**

將應用程式程式碼更新為不要嘗試對已處置的內容進行消極式載入，或將此設定為不執行任何作業，如例外狀況訊息中所述。

### <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a>過度建立內部服務提供者現在預設是錯誤

[追蹤問題 #10236](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

**舊行為**

在 EF Core 3.0 以前，當應用程式建立異常數目的內部服務提供者時，會記錄一則警告。

**新行為**

從 EF Core 3.0 開始，此警告現在會視為錯誤，並會擲回例外狀況。 

**原因**

這項變更的目的是為了透過更明確公開此異常案例，藉以開發更完善的應用程式程式碼。

**風險降低**

遇到此錯誤時的最適當動作是了解根本原因，並停止建立這麼多的內部服務提供者。
不過，透過設定 `DbContextOptionsBuilder` 可以將錯誤轉換回警告。
例如:

```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

<a name="nbh"></a>

### <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a>使用單一字串呼叫之 HasOne/HasMany 的新行為

[追蹤問題 #9171](https://github.com/aspnet/EntityFrameworkCore/issues/9171)

**舊行為**

在 EF Core 3.0 之前，使用單一字串呼叫 `HasOne` 或 `HasMany` 的程式碼會以令人困惑的方式解譯。
例如:
```C#
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

程式碼看起來像是它使用 `Entrance` 瀏覽屬性將 `Samurai` 與一些其他實體類型相關，這可能是私用屬性。

在現實中，此程式碼會在不使用瀏覽屬性的情況下嘗試建立與一些實體 (稱為 `Entrance`) 的關係。

**新行為**

從 EF Core 3.0 開始，上述程式碼現在會執行像以前一樣的動作。

**原因**

舊行為令人非常困惑，特別是當讀取設定程式碼與尋找錯誤時。

**風險降低**

這只會造成已明確針對類型名稱使用字串設定關係，而未明確指定瀏覽屬性的應用程式中斷。
這不是常見情況。
先前的行為可透過明確地傳遞瀏覽屬性名稱的 `null` 來取得。
例如:

```C#
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

<a name="rtnt"></a>

### <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a>數個非同步方法的傳回類型已從 Task 變更為 ValueTask

[追蹤問題 #15184](https://github.com/aspnet/EntityFrameworkCore/issues/15184)

**舊行為**

下列非同步方法先前傳回了 `Task<T>`：

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* `ValueGenerator.NextValueAsync()` (和衍生類別)

**新行為**

上述方法現在會透過相同的 `T` 傳回 `ValueTask<T>`，如同以前一樣。

**原因**

這項變更可降低叫用這些方法時產生的堆積配置數目，可改善一般效能。

**風險降低**

僅等待上述 API 的應用程式只需要重新編譯，而不需要變更來源。
更複雜的使用方式 (例如將傳回的 `Task` 傳遞給 `Task.WhenAny()`) 通常需要藉由呼叫 `AsTask()` 將傳回的 `ValueTask<T>` 轉換為 `Task<T>`。
請注意，這會抵消這項變更所帶來的配置減少。

<a name="rtt"></a>

### <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a>Relational:TypeMapping 註解現僅為 TypeMapping

[追蹤問題 #9913](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

**舊行為**

類型對應註解的註解名稱之前是 "Relational:TypeMapping"。

**新行為**

類型對應註解的註解名稱現在是 "TypeMapping"。

**原因**

類型對應現在不只用於關聯式資料庫提供者。

**風險降低**

這只會中斷直接將類型對應當做註解存取的應用程式，但這並不常見。
最適當的修正動作是使用 API 介面存取類型對應，而不是直接使用註解。

### <a name="totable-on-a-derived-type-throws-an-exception"></a>衍生類型上的 ToTable 會擲回例外狀況 

[追蹤問題 #11811](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

**舊行為**

在 EF Core 3.0 以前，會忽略衍生類型上呼叫的 `ToTable()`，因為唯一的繼承對應策略是對此案例無效的 TPH。 

**新行為**

從 EF Core 3.0 開始，以及在更新版本中準備新增 TPT 和 TPC 支援時，在衍生類型上呼叫的 `ToTable()` 現在會擲回例外狀況，以避免未來發生非預期的對應變更。

**原因**

目前無法將衍生類型對應至不同的資料表。
這項變更可避免未來有效執行時的中斷情況。

**風險降低**

避免嘗試將衍生類型對應至其他資料表。

### <a name="forsqlserverhasindex-replaced-with-hasindex"></a>ForSqlServerHasIndex 已取代為 HasIndex 

[追蹤問題 #12366](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

**舊行為**

在 EF Core 3.0 以前，`ForSqlServerHasIndex().ForSqlServerInclude()` 可讓您設定搭配 `INCLUDE` 使用的資料行。

**新行為**

從 EF Core 3.0 開始，關聯式層級現在支援對索引使用 `Include`。
使用 `HasIndex().ForSqlServerInclude()`。

**原因**

這項變更的目的是為了能夠使用 `Include` 將所有資料庫提供者的索引 API 合併到一個位置。

**風險降低**

使用新的 API，如上所示。

### <a name="metadata-api-changes"></a>中繼資料 API 變更

[追蹤問題 #214](https://github.com/aspnet/EntityFrameworkCore/issues/214)

**新行為**

下列屬性已轉換為擴充方法：

* `IEntityType.QueryFilter` -> `GetQueryFilter()`
* `IEntityType.DefiningQuery` -> `GetDefiningQuery()`
* `IProperty.IsShadowProperty` -> `IsShadowProperty()`
* `IProperty.BeforeSaveBehavior` -> `GetBeforeSaveBehavior()`
* `IProperty.AfterSaveBehavior` -> `GetAfterSaveBehavior()`

**原因**

這項變更可簡化上述介面的實作。

**風險降低**

使用新的擴充方法。

<a name="provider"></a>

### <a name="provider-specific-metadata-api-changes"></a>提供者特定的中繼資料 API 變更

[追蹤問題 #214](https://github.com/aspnet/EntityFrameworkCore/issues/214)

**新行為**

提供者特定的擴充方法會壓平合併：

* `IProperty.Relational().ColumnName` -> `IProperty.GetColumnName()`
* `IEntityType.SqlServer().IsMemoryOptimized` -> `IEntityType.IsMemoryOptimized()`
* `PropertyBuilder.UseSqlServerIdentityColumn()` -> `PropertyBuilder.UseIdentityColumn()`

**原因**

此變更可簡化上述延伸方法的實作。

**風險降低**

使用新的擴充方法。

<a name="pragma"></a>

### <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a>EF Core 不會再傳送 SQLite FK 強制的 pragma

[追蹤問題 #12151](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

**舊行為**

在 EF Core 3.0 以前，當開啟 SQLite 連線時，EF Core 會傳送 `PRAGMA foreign_keys = 1`。

**新行為**

從 EF Core 3.0 開始，當開啟 SQLite 連線時，EF Core 不會再傳送 `PRAGMA foreign_keys = 1`。

**原因**

這項變更是因為 EF Core 預設會使用 `SQLitePCLRaw.bundle_e_sqlite3`，這也表示預設會開啟 FK 強制，而不需要在每次開啟連線時明確啟用。

**風險降低**

根據預設，會在預設用於 EF Core 的 SQLitePCLRaw.bundle_e_sqlite3 中啟用外部索引鍵。
在其他情況下，則可以藉由在您的連接字串中指定 `Foreign Keys=True` 來啟用外部索引鍵。

<a name="sqlite3"></a>

### <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundle_e_sqlite3"></a>Microsoft.EntityFrameworkCore.Sqlite 現在相依於 SQLitePCLRaw.bundle_e_sqlite3

**舊行為**

在 EF Core 3.0 以前，EF Core 會使用 `SQLitePCLRaw.bundle_green`。

**新行為**

從 EF Core 3.0 開始，EF Core 會使用 `SQLitePCLRaw.bundle_e_sqlite3`。

**原因**

這項變更的目的是為了讓用於 iOS 的 SQLite 版本與其他平台一致。

**風險降低**

若要在 iOS 上使用原生 SQLite 版本，請設定 `Microsoft.Data.Sqlite` 使用不同的 `SQLitePCLRaw` 套件組合。

<a name="guid"></a>

### <a name="guid-values-are-now-stored-as-text-on-sqlite"></a>GUID 值現在於 SQLite 上的儲存形式為 TEXT

[追蹤問題 #15078](https://github.com/aspnet/EntityFrameworkCore/issues/15078)

**舊行為**

GUID 值先前在 SQLite 上的儲存形式為 BLOB 值。

**新行為**

GUID 值現在會儲存為 TEXT。

**原因**

GUID 的二進位格式未標準化。 以 TEXT 的形式儲存值會提高資料庫與其他技術的相容性。

**風險降低**

您可以參考以下方式執行 SQL，來將現有的資料庫移轉至新的格式。

``` sql
UPDATE MyTable
SET GuidColumn = hex(substr(GuidColumn, 4, 1)) ||
                 hex(substr(GuidColumn, 3, 1)) ||
                 hex(substr(GuidColumn, 2, 1)) ||
                 hex(substr(GuidColumn, 1, 1)) || '-' ||
                 hex(substr(GuidColumn, 6, 1)) ||
                 hex(substr(GuidColumn, 5, 1)) || '-' ||
                 hex(substr(GuidColumn, 8, 1)) ||
                 hex(substr(GuidColumn, 7, 1)) || '-' ||
                 hex(substr(GuidColumn, 9, 2)) || '-' ||
                 hex(substr(GuidColumn, 11, 6))
WHERE typeof(GuidColumn) == 'blob';
```

在 EF Core 中，您也可以在這些屬性上設定值轉換器來繼續使用原本的行為。

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

Microsoft.Data.Sqlite 依然可以同時從 BLOB 及 TEXT 資料行讀取 GUID 值；但因為參數和常數的預設格式已變更，所以您可能需要對多數涉及 GUID 的案例採取動作。

<a name="char"></a>

### <a name="char-values-are-now-stored-as-text-on-sqlite"></a>Char 值現在於 SQLite 上會儲存為文字

[追蹤問題 #15020](https://github.com/aspnet/EntityFrameworkCore/issues/15020)

**舊行為**

Char 值原先在 SQLite 上儲存為整數值。 舉例來說，char 值 *A* 原先會儲存為整數值 65。

**新行為**

Char 值現在會儲存為 TEXT。

**原因**

將值儲存為 TEXT 不但更加自然，也使資料庫與其他技術的相容性更高。

**風險降低**

您可以參考以下方式執行 SQL，來將現有的資料庫移轉至新的格式。

``` sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

在 EF Core 中，您也可以在這些屬性上設定值轉換器來繼續使用原本的行為。

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

Microsoft.Data.Sqlite 也保留了讀取 INTEGER 和 TEXT 欄位字元值的功能，所以部分案例可能不需要任何動作。

<a name="migid"></a>

### <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a>移轉識別碼現在會使用不因文化特性而異的行事曆產生

[追蹤問題 #12978](https://github.com/aspnet/EntityFrameworkCore/issues/12978)

**舊行為**

移轉識別碼原先會使用目前文化特性 (Culture) 的行事曆產生。

**新行為**

移轉識別碼現在一律會使用不因文化特性而異的行事曆 (西曆) 產生。

**原因**

更新資料庫或解決合併衝突時，移轉的順序相當重要。 使用無差異的行事曆可避免順序問題，使小組成員系統行事曆不同的問題不會發生。

**風險降低**

此變更會影響年份大於西曆行事曆的非西曆行事曆使用者 (例如泰國佛曆)。 現有的移轉識別碼必須更新，以使新的移轉會在現有的移轉之後排序。

您可在移轉設計工具檔案的移轉屬性中找到移轉識別碼。

``` diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

移轉歷程記錄資料表也必須更新。

``` sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

<a name="urn"></a>

### <a name="userownumberforpaging-has-been-removed"></a>已移除 UseRowNumberForPaging

[追蹤問題 #16400](https://github.com/aspnet/EntityFrameworkCore/issues/16400)

**舊行為**

在 EF Core 3.0 之前，`UseRowNumberForPaging` 可用來問與 SQL Server 2008 相容的分頁產生 SQL。

**新行為**

從 EF Core 3.0 開始，EF 將只會針對與新版 SQL Server 相容的分頁產生 SQL。 

**原因**

我們正在進行此變更，因為 [SQL Server 2008 不再是支援的產品](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) \(英文\) 且更新此功能以搭配 EF Core 3.0 中的查詢變更使用是一個大工程。

**風險降低**

我們建議更新為新版 SQL Server，或使用較高的相容性層級，以支援產生的 SQL。 儘管如此，若您無法這樣做，請[在追蹤問題下註解](https://github.com/aspnet/EntityFrameworkCore/issues/16400)並提供詳細資料。 我們可能會根據意見反應重新審視此決定。

<a name="xinfo"></a>

### <a name="extension-infometadata-has-been-removed-from-idbcontextoptionsextension"></a>已從 IDbContextOptionsExtension 移除延伸模組資訊/中繼資料

[追蹤問題 #16119](https://github.com/aspnet/EntityFrameworkCore/issues/16119)

**舊行為**

用於提供有關延伸模組織中繼資料的 `IDbContextOptionsExtension` 包含方法。

**新行為**

這些方法已移動到新的 `DbContextOptionsExtensionInfo` 抽象基底類別，這是從新的 `IDbContextOptionsExtension.Info` 屬性傳回的。

**原因**

在從 2.0 升級到 3.0 的程序中，我們必須新增或變更這些方法數次。
將它們分成新的抽象基底類別可讓我們更輕鬆地在不變更現有延伸模組的情況下進行此類變更。

**風險降低**

更新延伸模組以遵循新模式。
您可以在 EF Core 原始程式碼中各種不同延伸模組之 `IDbContextOptionsExtension` 的許多實作中找到範例。

<a name="lqpe"></a>

### <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a>已重新命名 LogQueryPossibleExceptionWithAggregateOperator

[追蹤問題 #10985](https://github.com/aspnet/EntityFrameworkCore/issues/10985)

**變更**

`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` 已經重新命名為 `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`。

**原因**

使這個警告事件的命名與其他所有警告事件一致。

**風險降低**

使用新的名稱。 (注意，事件識別碼未變更。)

<a name="clarify"></a>

### <a name="clarify-api-for-foreign-key-constraint-names"></a>讓 API 的外部索引鍵限制式名稱更清楚

[追蹤問題 #10730](https://github.com/aspnet/EntityFrameworkCore/issues/10730)

**舊行為**

在 EF Core 3.0 前，外部索引鍵限制式名稱僅為 "name"。 例如:

```C#
var constraintName = myForeignKey.Name;
```

**新行為**

從 EF Core 3.0 開始，外部索引鍵限制式名稱現為 "constraint name"。 例如:

```C#
var constraintName = myForeignKey.ConstraintName;
```

**原因**

此變更可讓此領域中的命名一致，同時清楚指出這是外部索引鍵限制式的名稱，而非定義外部索引鍵的資料行或屬性名稱。

**風險降低**

使用新的名稱。

<a name="irdc2"></a>

### <a name="irelationaldatabasecreatorhastableshastablesasync-have-been-made-public"></a>IRelationalDatabaseCreator.HasTables/HasTablesAsync 已設定為公用

[追蹤問題 #15997](https://github.com/aspnet/EntityFrameworkCore/issues/15997)

**舊行為**

在 EF Core 3.0 之前，這些方法已受保護。

**新行為**

從 EF Core 3.0 開始，這些方法為公用。

**原因**

這些方法是由 EF 用來判斷資料庫是否已建立但為空資料庫。 當判斷是否要套用移轉時，這在 EF 外部也很實用。

**風險降低**

變更任何覆寫的可存取性。

<a name="dip"></a>

### <a name="microsoftentityframeworkcoredesign-is-now-a-developmentdependency-package"></a>Microsoft.EntityFrameworkCore.Design 現在是 DevelopmentDependency 套件

[追蹤問題 #11506](https://github.com/aspnet/EntityFrameworkCore/issues/11506)

**舊行為**

在 EF Core 3.0 之前，Microsoft.EntityFrameworkCore.Design 是標準 NuGet 套件，其組件可由相依於它的的專案參考。

**新行為**

從 EF Core 3.0 開始，它是 DevelopmentDependency 套件。 這表示相依性將不會以可轉移方式流動到其他專案，而且您預設再也無法參考其組件。

**原因**

此套件旨在用於設計階段。 部署的應用程式不應該參考它。 將套件設定為 DevelopmentDependency 會加強此建議。

**風險降低**

若您必須參考此套件以覆寫 EF Core 的設計階段行為，您可以更新您專案中的 PackageReference 項目中繼資料。 若以可轉移方式透過 Microsoft.EntityFrameworkCore.Tools 參考該套件，您將必須新增明確的 PackageReference 到該套件以變更其中繼資料。

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.0.0">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

<a name="SQLitePCL"></a>

### <a name="sqlitepclraw-updated-to-version-200"></a>SQLitePCL.raw 已更新為 2.0.0 版

[追蹤問題 #14824](https://github.com/aspnet/EntityFrameworkCore/issues/14824)

**舊行為**

Microsoft.EntityFrameworkCore.Sqlite 先前相依於 SQLitePCL.raw 的 1.1.12 版。

**新行為**

我們已更新套件，以相依于版本2.0.0。

**原因**

2\.0.0 版的 SQLitePCL.raw 以 .NET Standard 2.0 為目標。 它先前以 .NET Standard 1.1 為目標，這需要大量的大量的可轉移套件才能運作。

**風險降低**

SQLitePCL.raw version 2.0.0 包括一些中斷性變更。 如需詳細資訊，請參閱[版本資訊](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) \(英文\)。

<a name="NetTopologySuite"></a>

### <a name="nettopologysuite-updated-to-version-200"></a>NetTopologySuite 已更新為 2.0.0 版

[追蹤問題 #14825](https://github.com/aspnet/EntityFrameworkCore/issues/14825)

**舊行為**

空間套件先前相依於 NetTopologySuite 1.15.1 版。

**新行為**

我們已更新我們的套件以相依於 2.0.0 版。

**原因**

NetTopologySuite 2.0.0 版旨在解決 EF Core 使用者遇到的數個可用性問題。

**風險降低**

NetTopologySuite 2.0.0 版包括一些中斷性變更。 如需詳細資訊，請參閱[版本資訊](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) \(英文\)。

<a name="SqlClient"></a>

### <a name="microsoftdatasqlclient-is-used-instead-of-systemdatasqlclient"></a>SqlClient 是用來取代 SqlClient 的資料。

[追蹤問題 #15636](https://github.com/aspnet/EntityFrameworkCore/issues/15636)

**舊行為**

Microsoft.entityframeworkcore 先前的相依于 SqlClient。

**新行為**

我們已更新套件，以相依于 SqlClient。

**原因**

SqlClient 是用於 SQL Server 的旗艦版資料存取驅動程式，而 SqlClient 不再是開發的重點。
某些重要功能（例如 Always Encrypted）僅適用于 SqlClient。

**風險降低**

如果您的程式碼會直接相依于 SqlClient，您必須將它變更為參考 SqlClient。因為這兩個套件會維持非常高程度的 API 相容性，所以這應該只是簡單的封裝和命名空間變更。

<a name="mersa"></a>

### <a name="multiple-ambiguous-self-referencing-relationships-must-be-configured"></a>必須設定多個不明確的自我參考關聯性 

[追蹤問題 #13573](https://github.com/aspnet/EntityFrameworkCore/issues/13573)

**舊行為**

具有多個自我參考單向導覽屬性和相符 FK 的實體類型，不當設定為單一關聯性。 例如:

```C#
public class User 
{
        public Guid Id { get; set; }
        public User CreatedBy { get; set; }
        public User UpdatedBy { get; set; }
        public Guid CreatedById { get; set; }
        public Guid? UpdatedById { get; set; }
}
```

**新行為**

這種情況現在會在模型建立過程中偵測到，而且會擲回例外狀況，指出模型不明確。

**原因**

產生的模型不明確，在這種情況下通常會有錯誤。

**風險降低**

使用關聯性的完整設定。 例如:

```C#
modelBuilder
     .Entity<User>()
     .HasOne(e => e.CreatedBy)
     .WithMany();
 
 modelBuilder
     .Entity<User>()
     .HasOne(e => e.UpdatedBy)
     .WithMany();
```

<a name="udf-empty-string"></a>
### <a name="dbfunctionschema-being-null-or-empty-string-configures-it-to-be-in-models-default-schema"></a>DbFunction。架構為 null 或空字串，將其設定為模型的預設架構

[追蹤問題 #12757](https://github.com/aspnet/EntityFrameworkCore/issues/12757)

**舊行為**

以架構為空字串所設定的 DbFunction，在沒有架構的情況下被視為內建函數。 例如，下列程式碼會將 `DatePart` CLR 函數對應至 SqlServer 上 `DATEPART` 內建函數。

```C#
[DbFunction("DATEPART", Schema = "")]
public static int? DatePart(string datePartArg, DateTime? date) => throw new Exception();

```

**新行為**

所有的 DbFunction 對應都會被視為對應至使用者定義的函數。 因此，空的字串值會將函數放在模型的預設架構內。 這可能是透過 Fluent API `modelBuilder.HasDefaultSchema()` 明確設定的架構，否則為 `dbo`。

**原因**

先前的架構是空的，這是將該函式內建的方法，但該邏輯僅適用于 SqlServer，其中內建函數不屬於任何架構。

**風險降低**

手動設定 DbFunction 的轉譯，以將其對應至內建函數。

```C#
modelBuilder
    .HasDbFunction(typeof(MyContext).GetMethod(nameof(MyContext.DatePart)))
    .HasTranslation(args => SqlFunctionExpression.Create("DatePart", args, typeof(int?), null));
```
