---
title: 工具和延伸模組 - EF Core
description: Entity Framework Core 的外部工具與延伸模組
author: ErikEJ
ms.date: 02/21/2021
uid: core/extensions/index
ms.openlocfilehash: 67d078250d8bfdb845cf98949ee1412a8fe842bd
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024377"
---
# <a name="ef-core-tools--extensions"></a>EF Core 工具和延伸模組

這些工具和延伸模組可為 Entity Framework Core 2.1 及更新版本提供額外的功能。

> [!IMPORTANT]
> 延伸模組以各種來源建置而成，且不屬於 Entity Framework Core 專案維護的一部份。 考慮使用協力廠商延伸模組時，請務必評估其品質、授權、相容性、支援等，確保其符合您的需求。 特別是針對舊版 EF Core 所建置的延伸模組，可能需要予以更新，才能搭配最新版本使用。

## <a name="tools"></a>工具

### <a name="llblgen-pro"></a>LLBLGen Pro

LLBLGen Pro 是實體模型化解決方案，具備 Entity Framework 與 Entity Framework Core 的支援。 您可加以利用來輕鬆定義實體模型，並將其對應至資料庫，優先使用資料庫或模型後，即可開始直接撰寫查詢。 適用於 EF Core：2、3。

[網站](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a>Devart Entity Developer

Entity Developer 是一種功能強大的 O/RM 設計工具，可用於 ADO.NET Entity Framework、NHibernate、LinqConnect、Telerik 資料存取和 LINQ to SQL。 它支援使用 model first 或 database first 方法，以及 C# 或 Visual Basic 程式碼產生，以視覺化的方式設計 EF Core 模型。 EF Core：2，3，5。

[網站](https://www.devart.com/entitydeveloper/)

### <a name="nhydrate-orm-for-entity-framework"></a>Entity Framework 的 nHydrate ORM

為 Entity Framework 建立強型別擴充類別的 O/RM。 產生的程式碼為 Entity Framework Core。 因此兩者沒有任何不同。 這不是 EF 或自訂 O/RM 的替代方案。 這是一種視覺化的模型化層，可讓小組管理複雜的資料庫結構描述。 適用於 Git 這類的 SCM 軟體，允許多使用者存取模型，但具有最少的衝突。 安裝程式會追蹤模型變更並建立升級指令碼。 適用於 EF Core：3.

[Github 儲存機制](https://github.com/nHydrate/nHydrate)

### <a name="ef-core-power-tools"></a>EF Core Power Tools

EF Core Power Tools 是 Visual Studio 延伸模組，在簡單使用者介面中公開各種設計階段工作。 包括現有資料庫和 [SQL Server DACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications) 之 DbContext 及實體類別的反向工程、資料庫移轉的管理，以及模型視覺效果。 EF Core：3，5。

[GitHub Wiki](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a>Entity Framework 視覺效果編輯器

Entity Framework 視覺效果編輯器是 Visual Studio 延伸模組，為 EF 6 和 EF Core 類別的視覺效果設計新增了 O/RM 設計工具。 因為程式碼會透過 T4 範本產生，所以能加以自訂來滿足各種需求。 它支援繼承、單向及雙向關聯、列舉，還能夠對類別進行色彩編碼以及新增文字區塊，以說明設計中可能較不易懂的部分。 EF Core：2，3，5。

[Marketplace](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner) \(英文\)

### <a name="catfactory"></a>CatFactory

CatFactory 是 .NET Core 的 Scaffolding 引擎，可以自動從 SQL Server 資料庫產生 DbContext 類別、實體、對應組態及存放庫類別。 適用於 EF Core：2.

[GitHub 存放庫](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a>LoreSoft 的 Entity Framework Core Generator

Entity Framework Core 產生器 (efg) 是 .NET Core CLI 工具，可以從現有資料庫產生 EF Core 模型，與 `dotnet ef dbcontext scaffold` 十分相似，但它也透過區域取代或剖析對應檔案，支援安全的程式碼[重新產生](https://efg.loresoft.com/en/latest/regeneration/)。 這項工具也支援產生檢視模型、驗證及物件對應程式程式碼。 適用於 EF Core：2.

[教學課程](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[文件](https://efg.loresoft.com/en/latest/)

## <a name="extensions"></a>延伸模組

### <a name="microsoftentityframeworkcoreautohistory"></a>Microsoft.EntityFrameworkCore.AutoHistory

外掛程式庫，能夠自動將 EF Core 執行的資料變更記錄到記錄資料表中。 適用於 EF Core：2、3。

[GitHub 存放庫](https://github.com/Arch/AutoHistory/)

### <a name="efcoresecondlevelcacheinterceptor"></a>EFCoreSecondLevelCacheInterceptor

第二個層級快取是查詢快取。 EF 命令的結果會存放在快取中，使相同的 EF 命令會從快取擷取其資料，而不必再次向資料庫再次執行命令。 EF Core：3，5。

[GitHub 存放庫](https://github.com/VahidN/EFCoreSecondLevelCacheInterceptor)

### <a name="geco"></a>Geco

Geco (產生器主控台) 是以主控台專案為基礎的簡單程式碼產生器，在 .NET Core 上執行，並使用插入 C# 的字串產生程式碼。 Geco 包括適用於 EF Core 的反向模型產生器，支援複數、單數和可編輯範本。 它也提供種子資料指令碼產生器、指令碼執行器和資料庫清除工具。 適用於 EF Core：2.

[GitHub 存放庫](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a>EntityFrameworkCore.Scaffolding.Handlebars

可讓您搭配 Handlebars 範本使用 Entity Framework Core 工具鏈，自訂從現有資料庫進行反向工程的類別。 EF Core：2，3，5。

[GitHub 存放庫](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a>NeinLinq.EntityFrameworkCore

NeinLinq 擴充了 LINQ 提供者 (例如 Entity Framework)，並提供以下功能：重複使用函式、重寫查詢，以及使用可翻譯的述詞和選取器建置動態查詢。 EF Core：2，3，5。

[GitHub 存放庫](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a>Microsoft.EntityFrameworkCore.UnitOfWork

Microsoft.EntityFrameworkCore 的外掛程式，可支援存放庫、工作模式的單位，以及支援分散式交易的多個資料庫。 適用於 EF Core：2、3。

[GitHub 存放庫](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a>EFCore.BulkExtensions

適用於大量作業 (插入、更新、刪除) 的 EF Core 延伸模組。 適用於 EF Core：2、3。

[GitHub 存放庫](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a>Bricelam.EntityFrameworkCore.Pluralizer

新增設計階段複數表示。 適用於 EF Core：2、3。

[GitHub 存放庫](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a>Toolbelt.EntityFrameworkCore.IndexAttribute

[Index] 屬性的復興 (附有模型組建的延伸模組)。 EF Core：2，3，5。

[GitHub 存放庫](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="verifyentityframework"></a>確認 EntityFramework。

擴充 [驗證](https://github.com/VerifyTests/Verify) 以允許使用 EntityFramework 進行快照集測試。 EF Core：3，5。

[GitHub 存放庫](https://github.com/VerifyTests/Verify.EntityFramework)

### <a name="localdb"></a>LocalDb

提供 [SQL Server Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb) 周圍的包裝函式，以簡化對 Entity Framework 執行測試的程式。 EF Core：3，5。

[GitHub 存放庫](https://github.com/SimonCropp/LocalDb)

### <a name="effluentvalidation"></a>EfFluentValidation

將 [FluentValidation](https://fluentvalidation.net/) 支援新增至 Entity Framework。 EF Core：3，5。

[GitHub 存放庫](https://github.com/SimonCropp/EfFluentValidation)

### <a name="efcoretemporalsupport"></a>EFCore.TemporalSupport

時態性支援的實作。 適用於 EF Core：2.

[GitHub 存放庫](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a>EfCoreTemporalTable

使用下列引進的擴充方法，輕鬆地在慣用的資料庫上執行時態性查詢：`AsTemporalAll()`、`AsTemporalAsOf(date)`、`AsTemporalFrom(startDate, endDate)`、`AsTemporalBetween(startDate, endDate)`、`AsTemporalContained(startDate, endDate)`。 適用於 EF Core：3.

[GitHub 存放庫](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="entityframeworkcoretemporaltables"></a>EntityFrameworkCore.TemporalTables

Entity Framework Core 的延伸模組程式庫，可讓使用 SQL Server 的開發人員輕鬆地使用時態表。 適用於 EF Core：2、3。

[GitHub 存放庫](https://github.com/findulov/EntityFrameworkCore.TemporalTables)

### <a name="entityframeworkcorecacheable"></a>EntityFrameworkCore.Cacheable

高效能第二層查詢快取。 適用於 EF Core：2.

[GitHub 存放庫](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entityframeworkcorencache"></a>EntityFrameworkCore.NCache

NCache Entity Framework Core Provider 是快取查詢結果的分散式第二層級快取提供者。 NCache 的分散式架構使其更具可擴縮性與高可用性。 適用于 EF Core 2、3。

[網站](https://www.alachisoft.com/ncache/ef-core-cache.html)

### <a name="entityframeworkcoretriggered"></a>Microsoft.entityframeworkcore 觸發

EF Core 的觸發程式。 在認可至資料庫之前和之後，回應 DbCoNtext 中的變更。 觸發程式是完全非同步，且支援相依性插入、繼承、串聯等。 EF Core：3，5。

[GitHub 存放庫](https://github.com/koenbeuk/EntityFrameworkCore.Triggered)

### <a name="entity-framework-plus"></a>Entity Framework Plus

使用下功能延伸您的 DbContext：包括篩選、稽核、快取、查詢未來、批次刪除、批次更新等。 EF Core：2，3，5。

[網站](https://entityframework-plus.net/)
[GitHub 存放庫](https://github.com/zzzprojects/EntityFramework-Plus)

### <a name="entity-framework-extensions"></a>Entity Framework 擴充功能

使用高效能批次作業延伸您的 DbContext：BulkSaveChanges、BulkInsert、BulkUpdate、BulkDelete、BulkMerge 等。 EF Core：2，3，5。

[網站](https://entityframework-extensions.net/)

### <a name="expressionify"></a>Expressionify

新增在 LINQ Lambda 中呼叫擴充方法的支援。 適用於 EF Core：3.

[GitHub 存放庫](https://github.com/ClaveConsulting/Expressionify)

### <a name="elinq"></a>ELinq

關聯式資料的 Language-integrated Query (LINQ) 技術。 可供使用 C# 來撰寫強型別查詢。 適用於 EF Core：3.

- 查詢建立的完整 C# 支援：Lambda 內部的多個陳述式、變數、函式等。
- 與 SQL 沒有語意隔閡。 ELinq 宣告 SQL 語句 (例如 `SELECT` ， `FROM` `WHERE`) 為第一個類別的 c # 方法，結合熟悉的語法與 intellisense，型別安全和重構。

因此，SQL 會變成在本機公開其 API 的「另一個」類別庫，實際上就是「整合語言的 SQL」  。

[網站](https://entitylinq.com/)

### <a name="ramses"></a>Ramses

生命週期勾點 (呼叫 SaveChanges)。 適用於 EF Core：2、3。

[GitHub 存放庫](https://github.com/JValck/Ramses)

### <a name="efcorenamingconventions"></a>EFCore.NamingConventions

這會自動使所有資料表與資料行名稱採用全為大寫或小寫字母，並以底線作為空格的格式。 適用於 EF Core：3.

[GitHub 存放庫](https://github.com/efcore/EFCore.NamingConventions)

### <a name="simplersoftwareentityframeworkcoresqlservernodatime"></a>SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime

針對 NodaTime 類型，將原生支援新增至 SQL Server 的 EntityFrameworkCore。 EF Core：3，5。

[GitHub 存放庫](https://github.com/StevenRasmussen/EFCore.SqlServer.NodaTime)

### <a name="dabbleentityframeworkcoretemporalquery"></a>Dabble.EntityFrameworkCore.Temporal.Query

Entity Framework Core 3.1 的 LINQ 延伸模組，以支援 Microsoft SQL Server 時態表查詢。 適用於 EF Core：3.

[GitHub 存放庫](https://github.com/Adam-Langley/efcore-temporal-query)

### <a name="entityframeworkcoresqlserverhierarchyid"></a>EntityFrameworkCore.SqlServer.HierarchyId

為 SQL Server EF Core 提供者新增 hierarchyid 支援。 適用於 EF Core：3.

[GitHub 存放庫](https://github.com/efcore/EFCore.SqlServer.HierarchyId)

### <a name="linq2dbentityframeworkcore"></a>linq2db.EntityFrameworkCore

將 LINQ 查詢轉換為 SQL 運算式的替代轉譯程式。 EF Core：3，5。

包括對進階 SQL 功能的支援，例如 CTE、大量複製、資料表提示、視窗型函式、暫存資料表，以及資料庫端的建立/更新/刪除作業。

[GitHub 存放庫](https://github.com/linq2db/linq2db.EntityFrameworkCore)

### <a name="efcoresoftdelete"></a>EFCore.SoftDelete

虛刪除實體的實作。 適用於 EF Core：3.

[NuGet](https://www.nuget.org/packages/EFCore.SoftDelete)

### <a name="entityframeworkcoreconfigurationmanager"></a>EntityFrameworkCore.ConfigurationManager

擴充 EF Core 以解析 App.config 的連接字串。適用于 EF Core：3。

[GitHub 存放庫](https://github.com/efcore/EFCore.ConfigurationManager)

### <a name="detached-mapper"></a>卸離對應程式

DTO-Entity 對應程式具有組合/匯總處理 (類似于 GraphDiff) 。 EF Core：3，5。

[NuGet](https://www.nuget.org/packages/Detached.Mappers.EntityFramework)

### <a name="entityframeworkcoresqlitenodatime"></a>Microsoft.entityframeworkcore。 NodaTime

在使用[SQLite](https://sqlite.org)時新增對[NodaTime](https://nodatime.org)類型的支援。 適用于 EF Core：5。

[GitHub 存放庫](https://github.com/khellang/EFCore.Sqlite.NodaTime)

### <a name="erikejentityframeworkcoresqlserverdacpac"></a>ErikEJ. Microsoft.entityframeworkcore。 .Dacpac

啟用從 SQL Server 資料層應用程式封裝的 EF Core 模型進行反向工程， ( .dacpac) 。 EF Core：3，5。

[GitHub Wiki](https://github.com/ErikEJ/EFCorePowerTools/wiki/ErikEJ.EntityFrameworkCore.SqlServer.Dacpac)

### <a name="erikejentityframeworkcoredgmlbuilder"></a>ErikEJ. Microsoft.entityframeworkcore. DgmlBuilder

產生 DGML (圖形) 內容，以視覺化方式呈現您的 DbCoNtext。 將 AsDgml () 擴充方法加入至 DbCoNtext 類別。 EF Core：3，5。

[GitHub Wiki](https://github.com/ErikEJ/EFCorePowerTools/wiki/Inspect-your-DbContext-model)

### <a name="entityframeworkexceptions"></a>EntityFramework。例外狀況

使用 Entity Framework Core 時，所有資料庫例外狀況都會包裝在就 dbupdateexception 中。 EntityFramework 會處理所有資料庫特定的詳細資料，以找出違反了哪些條件約束，並可讓您在 `UniqueConstraintException` `CannotInsertNullException` `MaxLengthExceededException` `NumericOverflowException` `ReferenceConstraintException` 查詢違反資料庫條件約束時，使用具類型的例外狀況，例如、、、。

支援 SQL Server、Postgres、MySql、SQLite 和 Oracle

EF Core：3，5。

[GitHub 儲存機制](https://github.com/Giorgi/EntityFramework.Exceptions)
