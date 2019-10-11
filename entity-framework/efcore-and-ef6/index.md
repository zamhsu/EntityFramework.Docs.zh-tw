---
title: 比較 Entity Framework 6 與 Entity Framework Core - EF
description: 提供如何在 Entity Framework 6 和 Entity Framework Core 之間選擇的指引。
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a6b9cd22-6803-4c6c-a4d4-21147c0a81cb
uid: efcore-and-ef6/index
ms.openlocfilehash: 9fe4905de5bd81fce083d620724b7fad4c6dd11b
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182037"
---
# <a name="compare-ef-core--ef6"></a>比較 EF Core 與 EF6

Entity Framework 是 .NET 物件關聯式對應程式 (O/RM)。 本文比較這兩個版本：Entity Framework 6 與 Entity Framework Core。

## <a name="entity-framework-6"></a>Entity Framework 6

Entity Framework 6 (EF6) 是經過試驗和測試的資料存取技術。 該技術在 2008 年首次隨附於 .NET Framework 3.5 SP1 和 Visual Studio 2008 SP1 中發行。 從 4.1 版本開始，則以 [EntityFramework](https://www.nuget.org/packages/EntityFramework/) NuGet 套件的形式提供。 從 3.0 開始，EF6 在 .NET Framework 4.x 和 .NET Core 上執行。

EF6 仍然是受支援的產品，並會持續提供 Bug 修正和小幅度的改善。

## <a name="entity-framework-core"></a>Entity Framework Core

Entity Framework Core (EF Core) 是完全重寫的 EF6，首次發行時間為 2016 年。 它隨附於 Nuget 套件，主要項目為 [Microsoft.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/)。 EF Core 是在 .NET Core 上執行的跨平台產品。

EF Core 旨在提供類似於 EF6 的開發人員體驗。 大部分的頂層 API 都維持不變，因此使用過 EF6 的開發人員會對 EF Core 感到熟悉。

## <a name="feature-comparison"></a>功能比較

EF Core 提供的新功能將無法在 EF6 中實作 (例如[替代金鑰](xref:core/modeling/alternate-keys)、[批次更新](xref:core/what-is-new/ef-core-1.0#relational-batching-of-statements)及 [LINQ 查詢中的混合用戶端/資料庫評估](xref:core/querying/client-eval)。 但由於它是新的程式碼基底，所以也缺少 EF6 具有的一些功能。

下表比較 EF Core 和 EF6 中提供的功能。 這是一個上位階的比較，並不會列出每個功能，也不會解釋不同 EF 版本中同一項功能間的差異。

EF Core 行表示功能首次出現所在的產品版本。

### <a name="creating-a-model"></a>建立模型

| **功能**                                           | **EF 6** | **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| 基本類別對應                                   | 是      | 1.0                                   |
| 含參數的建構函式                          |          | 2.1                                   |
| 屬性值轉換                            |          | 2.1                                   |
| 不含索引鍵的對應類型                             |          | 2.1                                   |
| 慣例                                           | 是      | 1.0                                   |
| 自訂慣例                                    | 是      | 1.0 (部分)                         |
| 資料註解                                      | 是      | 1.0                                   |
| Fluent API                                            | 是      | 1.0                                   |
| 繼承：單表 (TPH)                | 是      | 1.0                                   |
| 繼承：一類一表 (TPT)                     | 是      |                                       |
| 繼承：一具象類別一表 (TPC)           | 是      |                                       |
| 陰影狀態屬性                               |          | 1.0                                   |
| 替代金鑰                                        |          | 1.0                                   |
| 多對多但沒有聯結實體                      | 是      |                                       |
| 產生金鑰：資料庫                              | 是      | 1.0                                   |
| 產生金鑰：用戶端                                |          | 1.0                                   |
| 複雜/擁有的型別                                   | 是      | 2.0                                   |
| 空間資料                                          | 是      | 2.2                                   |
| 模型的圖形化視覺效果                      | 是      |                                       |
| 圖形化模型編輯器                                | 是      |                                       |
| 模型格式：程式碼                                    | 是      | 1.0                                   |
| 模型格式：EDMX (XML)                              | 是      |                                       |
| 從資料庫建立模型：命令列              | 是      | 1.0                                   |
| 從資料庫建立模型：VS 精靈                 | 是      |                                       |
| 從資料庫更新模型                            | Partial  |                                       |
| 全域查詢篩選條件                                  |          | 2.0                                   |
| 資料表分割                                       | 是      | 2.0                                   |
| 實體分割                                      | 是      |                                       |
| 資料庫純量函式對應                      | 差     | 2.0                                   |
| 欄位對應                                         |          | 1.1                                   |
| 可為 Null 的參考型別 (C# 8.0)                     |          | 3.0                                   |

### <a name="querying-data"></a>查詢資料

| **功能**                                           | **EF6**  | **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| LINQ 查詢                                          | 是      | 1.0 (針對複雜查詢仍在進行中) |
| 可讀取產生的 SQL                                | 差     | 1.0                                   |
| GroupBy 轉譯                                   | 是      | 2.1                                   |
| 載入相關資料：積極式                           | 是      | 1.0                                   |
| 載入相關資料：衍生類型的積極式載入 |          | 2.1                                   |
| 載入相關資料：緩慢                            | 是      | 2.1                                   |
| 載入相關資料：明確                        | 是      | 1.1                                   |
| 原始 SQL 查詢：實體類型                         | 是      | 1.0                                   |
| 原始 SQL 查詢：無索引鍵實體類型                 | 是      | 2.1                                   |
| 原始 SQL 查詢：使用 LINQ 撰寫                  |          | 1.0                                   |
| 明確地編譯查詢                           | 差     | 2.0                                   |
| 以文字為基礎的查詢語言 (Entity SQL)                | 是      |                                       |
| await foreach (C# 8.0)                                |          | 3.0                                   |

### <a name="saving-data"></a>儲存資料

| **功能**                                           | **EF6**  | **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| 變更追蹤：快照集                             | 是      | 1.0                                   |
| 變更追蹤：通知                         | 是      | 1.0                                   |
| 變更追蹤：Proxy                              | 是      |                                       |
| 存取追蹤狀態                               | 是      | 1.0                                   |
| 開放式並行存取                                | 是      | 1.0                                   |
| 異動                                          | 是      | 1.0                                   |
| 陳述式的批次處理                                |          | 1.0                                   |
| 預存程序對應                              | 是      |                                       |
| 已中斷連線的圖形低階 API                     | 差     | 1.0                                   |
| 已中斷連線的圖形端對端                         |          | 1.0 (部分)                         |

### <a name="other-features"></a>其他功能

| **功能**                                           | **EF6**  | **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| 移轉                                            | 是      | 1.0                                   |
| 資料庫建立/刪除 API                       | 是      | 1.0                                   |
| 種子資料                                             | 是      | 2.1                                   |
| 連線恢復功能                                 | 是      | 1.1                                   |
| 生命週期攔截程序 (事件、攔截)                | 是      |                                       |
| 簡易記錄 (Database.Log)                         | 是      |                                       |
| DbContext 共用                                     |          | 2.0                                   |

### <a name="database-providers"></a>資料庫提供者

| **功能**                                           | **EF6**  | **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| SQL Server                                            | 是      | 1.0                                   |
| MySQL                                                 | 是      | 1.0                                   |
| PostgreSQL                                            | 是      | 1.0                                   |
| Oracle                                                | 是      | 1.0                                   |
| SQLite                                                | 是      | 1.0                                   |
| SQL Server Compact                                    | 是      | 1.0 <sup>(1)</sup>                    |
| DB2                                                   | 是      | 1.0                                   |
| Firebird                                              | 是      | 2.0                                   |
| Jet (Microsoft Access)                                |          | 2.0 <sup>(1)</sup>                    |
| Cosmos DB                                             |          | 3.0                                   |
| 記憶體內部 (測試用)                               |          | 1.0                                   |

<sup>1</sup> SQL Server Compact 和 Jet 提供者只適用於 .NET Framework (不適用於 .NET Core)。

### <a name="net-implementations"></a>.NET 實作

| **功能**                                           | **EF6**            | **EF Core**                           |
|:------------------------------------------------------|:-------------------|:--------------------------------------|
| .NET Framework                                        | 是                | 1.0 (已在 3.0 中移除)                  |
| .NET Core                                             | 是 (已在 6.3 中新增) | 1.0                                   |
| Mono & Xamarin                                        |                    | 1.0 (進行中)                     |
| UWP                                                   |                    | 1.0 (進行中)                     |

## <a name="guidance-for-new-applications"></a>新應用程式的指引

如果下列兩個條件成立，請考慮為新應用程式使用 EF Core：
* 該應用程式需要 .NET Core 的功能。 如需詳細資訊，請參閱[在 .NET Core 和 .NET Framework 之間為伺服器應用程式進行選擇](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server)。
* EF Core 支援應用程式所需的所有功能。 若缺少所需功能，請查看 [EF Core 藍圖](xref:core/what-is-new/index) 了解將來是否有計劃可支援該功能。 

如果下列兩個條件成立，請考慮使用 EF6：
* 該應用程式會在 Windows 和 .NET Framework 4.0 或更新版本上執行。
* EF6 支援應用程式所需的所有功能。

## <a name="guidance-for-existing-ef6-applications"></a>現有 EF6 應用程式的指引

因為 EF Core 在本質上有所變更，所以不建議將 EF6 應用程式移至 EF Core，除非有進行該變更的充分理由。 若要移至 EF Core 來使用新功能，請確認您已了解其限制。 如需詳細資訊，請參閱[從 EF6 移植到 EF Core](porting/index.md)。 **從 EF6 移至 EF Core 不像升級而較像移植。**

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱文件：
* <xref:core/index>
* <xref:ef6/index>
