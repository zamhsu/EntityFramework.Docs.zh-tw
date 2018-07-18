---
title: EF Core 1.0 中的新增功能 - EF Core
author: divega
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 20A25111-AEBE-4BC2-83A5-3F651952DF72
ms.technology: entity-framework-core
uid: core/what-is-new/ef-core-1.0
ms.openlocfilehash: af7cf490ef2b04afb02461279fbe67c1c7fa3d95
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37949018"
---
# <a name="features-included-in-ef-core-10"></a>EF Core 1.0 中包含的功能

## <a name="platforms"></a>平台
### <a name="net-framework-451"></a>.NET Framework 4.5.1
包括主控台、 WPF、WinForms、ASP.NET 4 等。
### <a name="net-standard-13"></a>.NET Standard 1.3
包括以 Windows、OSX 和 Linux 上的 .NET Framework 與 .NET Core 兩者為目標的 ASP.NET Core。

## <a name="modelling"></a>模型建構
### <a name="basic-modelling"></a>基本模型建構
以具有常見純量型別 (`int`、`string` 等) get/set 屬性的 POCO 實體為基礎。
### <a name="relationships-and-navigation-properties"></a>關聯性與瀏覽屬性
您可以在以外部索引鍵為基礎的模型中指定一對多與一對零或一對一關聯性。 簡單集合或參考型別的瀏覽屬性可以與這些關聯性關聯。
### <a name="built-in-conventions"></a>內建慣例
這些會根據實體類別的圖形建置初始模型。
### <a name="fluent-api"></a>Fluent API
可讓您在您的上下文中覆寫 `OnModelCreating` 方法，以進一步設定由慣例所探索到的模型。
### <a name="data-annotations"></a>資料註解
這些屬性可新增到您的實體類別/屬性，且會影響 EF 模型。 例如，新增 `[Required]` 可讓 EF 知道該屬性為必要。
### <a name="relational-table-mapping"></a>關聯式資料表對應
可讓實體對應到資料表/資料行。
### <a name="key-value-generation"></a>索引鍵值產生
包括用戶端產生與資料庫產生。
### <a name="database-generated-values"></a>資料庫產生的值
可讓值由資料庫在插入時產生 (預設值) 或在更新時產生 (計算資料行)。
### <a name="sequences-in-sql-server"></a>SQL Server 中的序列
可讓您在模型中定義序列物件。
### <a name="unique-constraints"></a>唯一的條件約束
可讓您定義替代索引鍵，並讓您定義以該索引鍵為目標的關聯性。
### <a name="indexes"></a>索引
在模型中定義索引會自動在資料庫中引進索引。 也支援唯一索引。
### <a name="shadow-state-properties"></a>陰影狀態屬性
可讓您在未宣告且未儲存在 .NET 類別中但可由 EF Core 追蹤並更新的模型中定義屬性。 通常用於外部索引鍵屬性 (當不想要在物件中公開這些時)。
### <a name="table-per-hierarchy-inheritance-pattern"></a>每一階層資料表繼承模式
可讓您使用鑑別子資料行來識別資料庫中指定資料列的實體類型，將繼承階層中的實體儲存到單一資料表。
### <a name="model-validation"></a>模型驗證
偵測模型中的無效模式並提供實用的錯誤訊息。

## <a name="change-tracking"></a>Change tracking
### <a name="snapshot-change-tracking"></a>快照集變更追蹤
可讓您透過比較目前狀態與原始狀態的複本 (快照集)，來自動偵測實體中的變更。
### <a name="notification-change-tracking"></a>通知變更追蹤
可讓您的實體在屬性值被修改時通知變更追蹤者。
### <a name="accessing-tracked-state"></a>存取追蹤狀態
透過 `DbContext.Entry` 與 `DbContext.ChangeTracker`。
### <a name="attaching-detached-entitiesgraphs"></a>附加取消附加的實體/圖表
新的 `DbContext.AttachGraph` API 有助於將實體重新附加到上下文，以減少使用新的/已修改的實體。

## <a name="saving-data"></a>儲存資料
### <a name="basic-save-functionality"></a>基本儲存功能
可讓您將對實體執行個體所做的變更儲存到資料庫中。
### <a name="optimistic-concurrency"></a>開放式並行存取
保護資料避免被使用者所進行的變更覆寫，因為資料是從資料庫所擷取的。
### <a name="async-savechanges"></a>非同步儲存變更
在資料庫處理從 `SaveChanges` 發出的命令時，可以釋放目前的執行緒以處理其他要求。
### <a name="database-transactions"></a>資料庫交易
表示 `SaveChanges` 一律都是不可部分完成 (表示它會完全成功，或不會對資料庫做任何變更)。 也有交易相關 API 可在內容執行個體等之間共用交易。
### <a name="relational-batching-of-statements"></a>關聯性：陳述式的批次處理
透過以批次方式在單一來回行程中對資料庫執行多個 INSERT/UPDATE/DELETE 命令。

## <a name="query"></a>查詢
### <a name="basic-linq-support"></a>基本 LINQ 支援
提供使用 LINQ 從資料庫擷取資料的功能。
### <a name="mixed-clientserver-evaluation"></a>混合用戶端/伺服器評估
可讓查詢包含無法在資料庫中評估因而必須在資料擷取到記憶體之後評估的邏輯。
### <a name="notracking"></a>NoTracking
查詢可讓您在內容不需要監視對實體執行個體所做的變更 (這在唯讀結果的情況下非常實用) 時，有更快的查詢執行效能。
### <a name="eager-loading"></a>積極式載入
提供 `Include` 與 `ThenInclude` 方法，以識別也應該在查詢時擷取的相關資料。
### <a name="async-query"></a>非同步查詢
在資料庫處理查詢時，可以釋放目前的執行緒 (與其關聯資源) 以處理其他要求。
### <a name="raw-sql-queries"></a>原始 SQL 查詢
提供 `DbSet.FromSql` 方法，以使用原始 SQL 查詢來擷取資料。 這些查詢也可以使用 LINQ 來撰寫。

## <a name="database-schema-management"></a>資料庫結構描述管理       
### <a name="database-creationdeletion-apis"></a>資料庫建立/刪除 API
大部分是針對您想要在不使用移轉的情況下快速建立/刪除資料庫的測試所設計。
### <a name="relational-database-migrations"></a>關聯式資料庫移轉
可讓關聯式資料庫結構描述隨著您的模型變更而發展。
### <a name="reverse-engineer-from-database"></a>資料庫還原工程
根據現有的關聯式資料庫結構描述架構 EF 模型。

## <a name="database-providers"></a>資料庫提供者
### <a name="sql-server"></a>SQL Server
連線到 Microsoft SQL Server 2008 與更新的版本。
### <a name="sqlite"></a>SQLite
連線到 SQLite 3 資料庫。
### <a name="in-memory"></a>記憶體內部
設計為在不需要連線到實際資料庫的情況下輕鬆進行測試。
### <a name="3rd-party-providers"></a>協力廠商提供者
有數個提供者提供其他資料庫引擎。 如需完整清單，請參閱[資料庫提供者](../providers/index.md)。
