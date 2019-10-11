---
title: 工具和延伸模組 - EF Core
author: ErikEJ
ms.date: 01/07/2019
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
uid: core/extensions/index
ms.openlocfilehash: 0c9671eb77181d85cd493341cd1abf842d13fb0e
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181264"
---
# <a name="ef-core-tools--extensions"></a>EF Core 工具和延伸模組

這些工具和延伸模組可為 Entity Framework Core 2.0 及更新版本提供額外的功能。

> [!IMPORTANT]  
> 延伸模組以各種來源建置而成，且不屬於 Entity Framework Core 專案維護的一部份。 考慮使用協力廠商延伸模組時，請務必評估其品質、授權、相容性、支援等，確保其符合您的需求。

## <a name="tools"></a>工具

### <a name="llblgen-pro"></a>LLBLGen Pro

LLBLGen Pro 是實體模型化解決方案，具備 Entity Framework 與 Entity Framework Core 的支援。 您可加以利用來輕鬆定義實體模型，並將其對應至資料庫，優先使用資料庫或模型後，即可開始直接撰寫查詢。

[網站](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a>Devart Entity Developer

Devart Entity 是功能強大的 ORM 設計工具，適用於 ADO.NET Entity Framework、NHibernate、LinqConnect、Telerik Data Access、LINQ 到 SQL。 它支援使用 model first 或 database first 方法，以及 C# 或 Visual Basic 程式碼產生，以視覺化的方式設計 EF Core 模型。 

[網站](https://www.devart.com/entitydeveloper/)

### <a name="ef-core-power-tools"></a>EF Core Power Tools

EF Core Power Tools 是 Visual Studio 2017 延伸模組，在簡單使用者介面中新增各種設計階段工作。 包括現有資料庫和 [SQL Server DACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) 之 DbContext 及實體類別的反向工程、資料庫移轉的管理，以及模型視覺效果。

[GitHub Wiki](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a>Entity Framework 視覺效果編輯器

Entity Framework 視覺效果編輯器是 Visual Studio 延伸模組，為 EF 6 和 EF Core 類別的視覺效果設計新增 ORM 設計工具。 因為程式碼會透過 T4 範本產生，所以能加以自訂來滿足各種需求。 它支援繼承、單向及雙向關聯、列舉，還能夠對類別進行色彩編碼以及新增文字區塊，以說明設計中可能較不易懂的部分。

[Marketplace](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner) \(英文\)

### <a name="catfactory"></a>CatFactory

CatFactory 是 .NET Core 的 Scaffolding 引擎，可以自動從 SQL Server 資料庫產生 DbContext 類別、實體、對應組態及存放庫類別。

[GitHub 存放庫](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a>LoreSoft 的 Entity Framework Core Generator

Entity Framework Core 產生器 (efg) 是 .NET Core CLI 工具，可以從現有資料庫產生 EF Core 模型，與 `dotnet ef dbcontext scaffold` 十分相似，但它也透過區域取代或剖析對應檔案，支援安全的程式碼[重新產生](https://efg.loresoft.com/en/latest/regeneration/)。 這項工具也支援產生檢視模型、驗證及物件對應程式程式碼。 

[教學課程](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[文件](https://efg.loresoft.com/en/latest/)

## <a name="extensions"></a>延伸模組

### <a name="microsoftentityframeworkcoreautohistory"></a>Microsoft.EntityFrameworkCore.AutoHistory

外掛程式庫，能夠自動將 EF Core 執行的資料變更記錄到記錄資料表中。

[GitHub 存放庫](https://github.com/Arch/AutoHistory/)

### <a name="microsoftentityframeworkcoredynamiclinq"></a>Microsoft.EntityFrameworkCore.DynamicLinq

System.Linq.Dynamic 的 .NET Core/.NET Standard 連接埠，包括 EF Core 的非同步支援。
System.Linq.Dynamic 原為 Microsoft 範例，說明如何從字串運算式 (而非程式碼) 動態建構 LINQ 查詢。

[GitHub 存放庫](https://github.com/StefH/System.Linq.Dynamic.Core/)

### <a name="efsecondlevelcachecore"></a>EFSecondLevelCache.Core

將 EF Core 查詢的結果儲存至第二層快取的延伸模組，如此一來可避免後續執行相同查詢時，直接從快取存取資料庫及擷取資料。

[GitHub 存放庫](https://github.com/VahidN/EFSecondLevelCache.Core/)

### <a name="entityframeworkcoreprimarykey"></a>EntityFrameworkCore.PrimaryKey

這個文件庫允許從任何實體將主索引鍵 (包括複合索引鍵) 的值擷取為字典。

[GitHub 存放庫](https://github.com/NickStrupat/EntityFramework.PrimaryKey/)

### <a name="entityframeworkcoretypedoriginalvalues"></a>EntityFrameworkCore.TypedOriginalValues

這個文件庫提供實體屬性原始值的強型別存取。 

[GitHub 存放庫](https://github.com/NickStrupat/EntityFramework.TypedOriginalValues/)

### <a name="geco"></a>Geco

Geco (產生器主控台) 是以主控台專案為基礎的簡單程式碼產生器，在 .NET Core 上執行，並使用插入 C# 的字串產生程式碼。 Geco 包括適用於 EF Core 的反向模型產生器，支援複數、單數和可編輯範本。 它也提供種子資料指令碼產生器、指令碼執行器和資料庫清除工具。

[GitHub 存放庫](https://github.com/iQuarc/Geco)

### <a name="linqkitmicrosoftentityframeworkcore"></a>LinqKit.Microsoft.EntityFrameworkCore

LinqKit.Microsoft.EntityFrameworkCore 是 LINQKit 文件庫之與 EF Core 相容的版本。 LINQKit 是適用於 LINQ to SQL 和 Entity Framework 進階使用者的免費延伸模組組合。 它提供了進階功能，例如述詞運算式的動態建置，以及在子查詢中使用運算式變數。  

[GitHub 存放庫](https://github.com/scottksmith95/LINQKit/)

### <a name="neinlinqentityframeworkcore"></a>NeinLinq.EntityFrameworkCore

NeinLinq 擴充了 LINQ 提供者 (例如 Entity Framework)，並提供以下功能：重複使用函式、重寫查詢，以及使用可翻譯的述詞和選取器建置動態查詢。

[GitHub 存放庫](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a>Microsoft.EntityFrameworkCore.UnitOfWork

Microsoft.EntityFrameworkCore 的外掛程式，可支援存放庫、工作模式的單位，以及支援分散式交易的多個資料庫。

[GitHub 存放庫](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a>EFCore.BulkExtensions

適用於大量作業 (插入、更新、刪除) 的 EF Core 延伸模組。

[GitHub 存放庫](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a>Bricelam.EntityFrameworkCore.Pluralizer

新增設計階段複數表示到 EF Core。

[GitHub 存放庫](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="pomelofoundationpomeloentityframeworkcoreextensionstosql"></a>PomeloFoundation/Pomelo.EntityFrameworkCore.Extensions.ToSql

簡單的擴充方法，可取得 EF Core 在簡單案例中為所指定 LINQ 查詢產生的 SQL 陳述式。 由於 EF Core 可以為單一 LINQ 查詢產生多個 SQL 陳述式，並視參數值產生不同的 SQL 陳述式，因此 ToSql 方法僅限於簡單案例。

[GitHub 存放庫](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.Extensions.ToSql)

### <a name="toolbeltentityframeworkcoreindexattribute"></a>Toolbelt.EntityFrameworkCore.IndexAttribute

EF Core [Index] 屬性的復興 (附有模型建置的延伸模組)。

[GitHub 存放庫](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a>EfCore.InMemoryHelpers

提供 EF Core 記憶體內部資料庫提供者的包裝函式。 使它的運作更接近關聯式提供者。

[GitHub 存放庫](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a>EFCore.TemporalSupport

EF Core 時態性支援的實作。

[GitHub 存放庫](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="entityframeworkcorecacheable"></a>EntityFrameworkCore.Cacheable

EF Core 的高效能第二層查詢快取。

[GitHub 存放庫](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entity-framework-plus"></a>Entity Framework Plus

使用下功能延伸您的 DbContext：包括篩選、稽核、快取、查詢未來、批次刪除、批次更新等。

[網站](https://entityframework-plus.net/)
[GitHub 存放庫](https://github.com/zzzprojects/EntityFramework-Plus)

### <a name="entity-framework-extensions"></a>Entity Framework 擴充功能

使用高效能批次作業延伸您的 DbContext：BulkSaveChanges、BulkInsert、BulkUpdate、BulkDelete、BulkMerge 等。

[網站](https://entityframework-extensions.net/)

### <a name="reconciler"></a>協調器

插入、更新及移除各自的實體，藉此將存放區中的實體圖表更新為指定的圖表。

[GitHub 存放庫](https://github.com/jtheisen/reconciler)
