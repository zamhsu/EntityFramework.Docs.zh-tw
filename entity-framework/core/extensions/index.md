---
title: 工具和延伸模組 - EF Core
author: ErikEJ
ms.date: 04/11/2020
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
uid: core/extensions/index
ms.openlocfilehash: 246cf687f2a087e9a9a569c875b27712ebe80c5c
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538338"
---
# <a name="ef-core-tools--extensions"></a>EF Core 工具和延伸模組

這些工具和延伸模組可為 Entity Framework Core 2.1 及更新版本提供額外的功能。

> [!IMPORTANT]  
> 延伸模組以各種來源建置而成，且不屬於 Entity Framework Core 專案維護的一部份。 考慮使用協力廠商延伸模組時，請務必評估其品質、授權、相容性、支援等，確保其符合您的需求。 特別是針對舊版 EF Core 所建置的延伸模組，可能需要予以更新，才能搭配最新版本使用。

## <a name="tools"></a>工具

### <a name="llblgen-pro"></a>LLBLGen Pro

LLBLGen Pro 是實體模型化解決方案，具備 Entity Framework 與 Entity Framework Core 的支援。 您可加以利用來輕鬆定義實體模型，並將其對應至資料庫，優先使用資料庫或模型後，即可開始直接撰寫查詢。 適用於 EF Core：2、3

[網站](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a>Devart Entity Developer

Devart Entity 是功能強大的 ORM 設計工具，適用於 ADO.NET Entity Framework、NHibernate、LinqConnect、Telerik Data Access、LINQ 到 SQL。 它支援使用 model first 或 database first 方法，以及 C# 或 Visual Basic 程式碼產生，以視覺化的方式設計 EF Core 模型。 適用於 EF Core：2.

[網站](https://www.devart.com/entitydeveloper/)

### <a name="nhydrate-orm-for-entity-framework"></a>Entity Framework 的 nHydrate ORM

為 Entity Framework 建立可延伸類別的強型別 ORM。 產生的程式碼為 Entity Framework Core。 因此兩者沒有任何不同。 這不能取代 EF 或自訂 ORM。 這是一種視覺化的模型化層，可讓小組管理複雜的資料庫結構描述。 適用於 Git 這類的 SCM 軟體，允許多使用者存取模型，但具有最少的衝突。 安裝程式會追蹤模型變更並建立升級指令碼。 適用於 EF Core：3.

[Github 網站](https://github.com/nHydrate/nHydrate)

### <a name="ef-core-power-tools"></a>EF Core Power Tools

EF Core Power Tools 是 Visual Studio 延伸模組，在簡單使用者介面中公開各種設計階段工作。 包括現有資料庫和 [SQL Server DACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) 之 DbContext 及實體類別的反向工程、資料庫移轉的管理，以及模型視覺效果。 適用於 EF Core：2、3。

[GitHub Wiki](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a>Entity Framework 視覺效果編輯器

Entity Framework 視覺效果編輯器是 Visual Studio 延伸模組，為 EF 6 和 EF Core 類別的視覺效果設計新增 ORM 設計工具。 因為程式碼會透過 T4 範本產生，所以能加以自訂來滿足各種需求。 它支援繼承、單向及雙向關聯、列舉，還能夠對類別進行色彩編碼以及新增文字區塊，以說明設計中可能較不易懂的部分。 適用於 EF Core：2.

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

外掛程式庫，能夠自動將 EF Core 執行的資料變更記錄到記錄資料表中。 適用於 EF Core：2.

[GitHub 存放庫](https://github.com/Arch/AutoHistory/)

### <a name="efcoresecondlevelcacheinterceptor"></a>EFCoreSecondLevelCacheInterceptor

第二個層級快取是查詢快取。 EF 命令的結果會存放在快取中，使相同的 EF 命令會從快取擷取其資料，而不必再次向資料庫再次執行命令。 適用於 EF Core：3.

[GitHub 存放庫](https://github.com/VahidN/EFCoreSecondLevelCacheInterceptor)

### <a name="geco"></a>Geco

Geco (產生器主控台) 是以主控台專案為基礎的簡單程式碼產生器，在 .NET Core 上執行，並使用插入 C# 的字串產生程式碼。 Geco 包括適用於 EF Core 的反向模型產生器，支援複數、單數和可編輯範本。 它也提供種子資料指令碼產生器、指令碼執行器和資料庫清除工具。 適用於 EF Core：2.

[GitHub 存放庫](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a>EntityFrameworkCore.Scaffolding.Handlebars 

可讓您搭配 Handlebars 範本使用 Entity Framework Core 工具鏈，自訂從現有資料庫進行反向工程的類別。 適用於 EF Core：2、3。

[GitHub 存放庫](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a>NeinLinq.EntityFrameworkCore 

NeinLinq 擴充了 LINQ 提供者 (例如 Entity Framework)，並提供以下功能：重複使用函式、重寫查詢，以及使用可翻譯的述詞和選取器建置動態查詢。 適用於 EF Core：2、3。

[GitHub 存放庫](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a>Microsoft.EntityFrameworkCore.UnitOfWork

Microsoft.EntityFrameworkCore 的外掛程式，可支援存放庫、工作模式的單位，以及支援分散式交易的多個資料庫。 適用於 EF Core：2.

[GitHub 存放庫](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a>EFCore.BulkExtensions

適用於大量作業 (插入、更新、刪除) 的 EF Core 延伸模組。 適用於 EF Core：2、3。

[GitHub 存放庫](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a>Bricelam.EntityFrameworkCore.Pluralizer

新增設計階段複數表示。 適用於 EF Core：2.

[GitHub 存放庫](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a>Toolbelt.EntityFrameworkCore.IndexAttribute

[Index] 屬性的復興 (附有模型組建的延伸模組)。 適用於 EF Core：2、3。

[GitHub 存放庫](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a>EfCore.InMemoryHelpers

提供 EF Core 記憶體內部資料庫提供者的包裝函式。 使它的運作更接近關聯式提供者。 適用於 EF Core：2.

[GitHub 存放庫](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a>EFCore.TemporalSupport

時態性支援的實作。 適用於 EF Core：2.

[GitHub 存放庫](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a>EfCoreTemporalTable

使用下列引進的擴充方法，輕鬆地在慣用的資料庫上執行時態性查詢：`AsTemporalAll()`、`AsTemporalAsOf(date)`、`AsTemporalFrom(startDate, endDate)`、`AsTemporalBetween(startDate, endDate)`、`AsTemporalContained(startDate, endDate)`。 適用於 EF Core：3.

[GitHub 存放庫](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="efcoretimetraveler"></a>EFCore.TimeTraveler

允許使用已定義的 EF Core 程式碼、實體和對應，對 [SQL Server 時態性記錄](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel)進行完整功能的 Entity Framework Core 查詢。  藉由將程式碼包裝在 `using (TemporalQuery.AsOf(targetDateTime)) {...}` 中來移動時間。 適用於 EF Core：3.

[GitHub 存放庫](https://github.com/VantageSoftware/EFCore.TimeTraveler)


### <a name="entityframeworkcoretemporaltables"></a>EntityFrameworkCore.TemporalTables

Entity Framework Core 的延伸模組程式庫，可讓使用 SQL Server 的開發人員輕鬆地使用時態表。 適用於 EF Core：2.

[GitHub 存放庫](https://github.com/findulov/EntityFrameworkCore.TemporalTables)


### <a name="entityframeworkcorecacheable"></a>EntityFrameworkCore.Cacheable

高效能第二層查詢快取。 適用於 EF Core：2.

[GitHub 存放庫](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entity-framework-plus"></a>Entity Framework Plus

使用下功能延伸您的 DbContext：包括篩選、稽核、快取、查詢未來、批次刪除、批次更新等。 適用於 EF Core：2、3。

[網站](https://entityframework-plus.net/)
[GitHub 存放庫](https://github.com/zzzprojects/EntityFramework-Plus)

### <a name="entity-framework-extensions"></a>Entity Framework 擴充功能

使用高效能批次作業延伸您的 DbContext：BulkSaveChanges、BulkInsert、BulkUpdate、BulkDelete、BulkMerge 等。 適用於 EF Core：2、3。

[網站](https://entityframework-extensions.net/)

### <a name="expressionify"></a>Expressionify

新增在 LINQ Lambda 中呼叫擴充方法的支援。 適用於 EF Core：3.

[GitHub 存放庫](https://github.com/ClaveConsulting/Expressionify)

### <a name="xlinq"></a>XLinq

關聯式資料的 Language-integrated Query (LINQ) 技術。 可供使用 C# 來撰寫強型別查詢。 適用於 EF Core：3.

- 查詢建立的完整 C# 支援：Lambda 內部的多個陳述式、變數、函式等。
- 與 SQL 沒有語意隔閡。 XLinq 會將 SQL 陳述式 (例如 `SELECT`、`FROM`、`WHERE`) 宣告為第一級 C# 方法，以結合熟悉的語法與 IntelliSense、型別安全和重構。

因此，SQL 會變成在本機公開其 API 的「另一個」類別庫，實際上就是「整合語言的 SQL」  。

[網站](http://xlinq.live/)

### <a name="ramses"></a>Ramses

生命週期勾點 (呼叫 SaveChanges)。 適用於 EF Core：2、3。

[GitHub 存放庫](https://github.com/JValck/Ramses)

### <a name="efcorenamingconventions"></a>EFCore.NamingConventions

這會自動使所有資料表與資料行名稱採用全為大寫或小寫字母，並以底線作為空格的格式。 適用於 EF Core：3.

[GitHub 存放庫](https://github.com/efcore/EFCore.NamingConventions)

### <a name="simplersoftwareentityframeworkcoresqlservernodatime"></a>SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime

針對 NodaTime 類型，將原生支援新增至 SQL Server 的 EntityFrameworkCore。 適用於 EF Core：3.

[GitHub 存放庫](https://github.com/StevenRasmussen/EFCore.SqlServer.NodaTime)

### <a name="dabbleentityframeworkcoretemporalquery"></a>Dabble.EntityFrameworkCore.Temporal.Query

Entity Framework Core 3.1 的 LINQ 延伸模組，以支援 Microsoft SQL Server 時態表查詢。 適用於 EF Core：3.

[GitHub 存放庫](https://github.com/Adam-Langley/efcore-temporal-query)

### <a name="entityframeworkcoresqlserverhierarchyid"></a>EntityFrameworkCore.SqlServer.HierarchyId

為 SQL Server EF Core 提供者新增 hierarchyid 支援。 適用於 EF Core：3.

[GitHub 存放庫](https://github.com/efcore/EFCore.SqlServer.HierarchyId)
