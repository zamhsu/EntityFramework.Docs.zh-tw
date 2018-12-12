---
title: 工具和延伸模組 - EF Core
author: ErikEJ
ms.date: 07/03/2018
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
uid: core/extensions/index
ms.openlocfilehash: 67eae6cb943b974cc9cd581b8054836d2e37b1e9
ms.sourcegitcommit: a6082a2caee62029f101eb1000656966195cd6ee
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2018
ms.locfileid: "53181990"
---
# <a name="ef-core-tools--extensions"></a>EF Core 工具和延伸模組

工具和延伸模組提供 Entity Framework Core 的額外功能。

> [!IMPORTANT]  
> 延伸模組以各種來源建置而成，且不屬於 Entity Framework Core 專案維護的一部份。 考慮使用協力廠商延伸模組時，請務必評估品質、授權、合規性、支援等，確保其符合您的需求。

## <a name="tools"></a>工具

### <a name="llblgen-pro"></a>LLBLGen Pro

LLBLGen Pro 是實體模型化解決方案，具備 Entity Framework 與 Entity Framework Core 的支援。 您可加以利用來輕鬆定義實體模型，並將其對應至資料庫，優先使用資料庫或模型後，即可開始直接撰寫查詢。

[網站](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a>Devart Entity Developer

Devart Entity 是功能強大的 ORM 設計工具，適用於 ADO.NET Entity Framework、NHibernate、LinqConnect、Telerik Data Access、LINQ 到 SQL。 您可使用模型優先和資料庫優先方法來設計您的 ORM 模型，並為其產生 C# 或 Visual Basic .NET 程式碼。 其為您帶來新的 ORM 模型設計方式，這不僅提高了生產力，還有助於資料庫應用程式的開發。

[網站](https://www.devart.com/entitydeveloper/)

### <a name="ef-core-power-tools"></a>EF Core Power Tools

Visual Studio 2017+ 延伸模組。 您可從現有的資料庫或 SQL Server Database 專案執行 DbContext 及 POCO 類別的還原工程，並透過各種方式對 DbContext 進行視覺化及檢查。

[GitHub Wiki](https://github.com/ErikEJ/SqlCeToolbox/wiki/EF-Core-Power-Tools)

### <a name="entity-framework-visual-editor"></a>Entity Framework 視覺效果編輯器

這個 Visual Studio 2017 延伸模組增添了 ORM 設計工具，適用於 Entity Framework 6、Core 2.0 及 Core 2.1 類別的視覺設計。 程式碼會使用 T4 範本來產生，因此能夠完全自訂，以滿足各種需求。 繼承、單向及雙向關聯均有支援，列舉亦然，還能夠對類別進行色彩編碼以及新增文字區塊，以說明設計中可能較不易懂的部分。

[Marketplace](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner) \(英文\)

### <a name="catfactory"></a>CatFactory

CatFactory 是 .NET Core 及 Entity Framework Core 的 Scaffolding 引擎。 CatFactory 的概念為從 SQL Server 執行個體匯出現有的資料庫，接著搭配資料庫模型中的代表、Scaffold 實體、組態、存放庫及更多。

[GitHub 存放庫](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a>LoreSoft 的 Entity Framework Core Generator

Entity Framework Core Generator (efg) 是可以從現有資料庫產生 EF Core 模型的 .NET Core CLI 工具，更像是 `dotnet ef dbcontext scaffold`。 不過，其不同處為它也支援安全的程式碼 [regeneration](https://efg.loresoft.com/en/latest/regeneration/) (重新產生)。 重新產生可透過區域取代或剖析對應檔案達成。 此工具也支援產生檢視模型、驗證及物件對應程式程式碼。 如需詳細資訊，請參閱教學課程及產品文件連結。

[教學課程](http://www.loresoft.com/Generate-ASP-NET-Web-API)
[文件](https://efg.loresoft.com/en/latest/)

## <a name="extensions"></a>延伸模組

### <a name="microsoftentityframeworkcoreautohistory"></a>Microsoft.EntityFrameworkCore.AutoHistory

支援自動記錄資料變更歷程記錄的 Microsoft.EntityFrameworkCore 外掛程式。

[GitHub 存放庫](https://github.com/Arch/AutoHistory/)

### <a name="microsoftentityframeworkcoredynamiclinq"></a>Microsoft.EntityFrameworkCore.DynamicLinq

Microsoft.EntityFrameworkCore 的動態 LINQ 延伸模組，可新增非同步支援

 [GitHub 存放庫](https://github.com/StefH/System.Linq.Dynamic.Core/)

### <a name="efcorepractices"></a>EFCore.Practices

嘗試在支援測試的 API 中擷取中上等級的做法 - 包括可掃描 N+1 個查詢的小型架構。

[GitHub 存放庫](https://github.com/riezebosch/efcore-practices/tree/master/src/EFCore.Practices/)

### <a name="efsecondlevelcachecore"></a>EFSecondLevelCache.Core

第二個層級快取程式庫。 第二個層級快取是查詢快取。 EF 命令的結果會存放在快取中，使相同的 EF 命令會從快取擷取其資料，而不必再次向資料庫再次執行命令。

[GitHub 存放庫](https://github.com/VahidN/EFSecondLevelCache.Core/)

### <a name="detachedentityframework"></a>Detached.EntityFramework

載入並儲存整個已中斷連結的實體圖表 (具備子實體與清單的實體)。 此想法出自於 [GraphDiff](https://github.com/refactorthis/GraphDiff/)。 此想法同時也新增了部分外掛程式，來減輕部分工作的重複性，例如稽核與分頁。

[GitHub 存放庫](https://github.com/leonardoporro/Detached/)

### <a name="entityframeworkcoreprimarykey"></a>EntityFrameworkCore.PrimaryKey

從任何實體將主索引鍵 (包含複合索引鍵) 擷取為字典。

[GitHub 存放庫](https://github.com/NickStrupat/EntityFramework.PrimaryKey/)

### <a name="entityframeworkcorerx"></a>EntityFrameworkCore.Rx

Entity Framework 實體之經常性可視項的被動延伸模組包裝函式。

[GitHub 存放庫](https://github.com/NickStrupat/EntityFramework.Rx/)

### <a name="entityframeworkcoretriggers"></a>EntityFrameworkCore.Triggers

為您的實體新增插入、更新及刪除事件的觸發程序。 以下有三種事件，分別為：失敗前、失敗後以及失敗時。

[GitHub 存放庫](https://github.com/NickStrupat/EntityFramework.Triggers/)

### <a name="entityframeworkcoretypedoriginalvalues"></a>EntityFrameworkCore.TypedOriginalValues

為您實體屬性的 OriginalValue 取得具類型的存取權。 支援簡單與複雜的屬性，但不支援瀏覽/集合。

[GitHub 存放庫](https://github.com/NickStrupat/EntityFramework.TypedOriginalValues/)

### <a name="geco"></a>Geco

Geco 提供反向模型產生器，並具有複數/單數支援、以 C# 6.0 插入字串為基礎的可編輯範本，並在 .Net Core 上執行。 其也提供了種子指令碼產生器，具有 SQL 合併指令碼及指令碼執行器。

[Github 存放庫](https://github.com/iQuarc/Geco)

### <a name="linqkitmicrosoftentityframeworkcore"></a>LinqKit.Microsoft.EntityFrameworkCore

LinqKit.Microsoft.EntityFrameworkCore 是一組免費的延伸模組，適用於 LINQ、SQL 及 EntityFrameworkCore 進階使用者。 具備 Include(...) 與 IDbAsync 支援。

[GitHub 存放庫](https://github.com/scottksmith95/LINQKit/)

### <a name="neinlinqentityframeworkcore"></a>NeinLinq.EntityFrameworkCore

NeinLinq.EntityFrameworkCore 為使用 LINQ 提供者提供了實用的延伸模組，例如僅支援 .NET 函式少數子集的 Entity Framework、重複使用函式、重寫查詢、甚至能使 null 不出現，還能使用可翻譯的述詞與選取器來建置動態查詢。

[GitHub 存放庫](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a>Microsoft.EntityFrameworkCore.UnitOfWork

Microsoft.EntityFrameworkCore 的外掛程式，可支援存放庫、工作模式的單位，以及支援分散式交易的多個資料庫。

[GitHub 存放庫](https://github.com/Arch/UnitOfWork/)

### <a name="entityframeworklazyloading"></a>EntityFramework.LazyLoading

EF Core 1.1 的消極式載入

[GitHub 存放庫](https://github.com/darxis/EntityFramework.LazyLoading)

### <a name="efcorebulkextensions"></a>EFCore.BulkExtensions

適用於大量作業 (插入、更新、刪除) 的 EntityFrameworkCore 延伸模組。

[GitHub 存放庫](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a>Bricelam.EntityFrameworkCore.Pluralizer

新增設計階段複數表示到 EF Core。

[GitHub 存放庫](https://github.com/bricelam/EFCore.Pluralizer)
