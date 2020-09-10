---
title: 比較 EF6 和 EF Core
description: 選擇 EF6 或 EF Core 的指引。
author: ajcvickers
ms.date: 01/23/2019
ms.assetid: a6b9cd22-6803-4c6c-a4d4-21147c0a81cb
uid: efcore-and-ef6/index
ms.openlocfilehash: 8e99cd9863d9f206ef3ae4d68226283019db835f
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619748"
---
# <a name="compare-ef-core--ef6"></a>比較 EF Core 與 EF6

## <a name="ef-core"></a>EF Core

Entity Framework Core ([EF Core](xref:core/index)) 是適用於 .NET 的新式物件資料庫對應程式。 其支援 LINQ 查詢、變更追蹤、更新以及結構描述移轉。

EF Core 透過[資料庫提供者外掛程式模型](xref:core/providers/index)來搭配使用 SQL Server/SQL Azure、SQLite、Azure Cosmos DB、MySQL、PostgreSQL 及更多資料庫。

## <a name="ef6"></a>EF6

Entity Framework 6 ([EF6](xref:ef6/index)) 則是專為 .NET Framework 設計的物件關聯式對應程式，但支援 .NET Core。 EF6 是穩定且受支援的產品，但已不再積極開發。

## <a name="feature-comparison"></a>功能比較

EF Core 提供的新功能不會在 EF6 中實作。 不過，並非所有 EF6 功能目前都已在 EF Core 中實作。

下表比較 EF Core 和 EF6 中提供的功能。 這是要點概述比較，所以不會一一列出每項功能，也不會說明不同 EF 版本中相同功能的差異。

EF Core 行表示功能首次出現所在的產品版本。

### <a name="creating-a-model"></a>建立模型

| **功能**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| 基本類別對應                                   | 是      | 1.0                                   |
| 含參數的建構函式                          |          | 2.1                                   |
| 屬性值轉換                            |          | 2.1                                   |
| 不含索引鍵的對應類型                             |          | 2.1                                   |
| 慣例                                           | 是      | 1.0                                   |
| 自訂慣例                                    | 是      | 1.0 (部分；[#214](https://github.com/dotnet/efcore/issues/214)) |
| 資料註解                                      | 是      | 1.0                                   |
| Fluent API                                            | 是      | 1.0                                   |
| 繼承：單表 (TPH)                | 是      | 1.0                                   |
| 繼承：一類一表 (TPT)                     | 是      | 已規劃在 5.0 中 ([#2266](https://github.com/dotnet/efcore/issues/2266)) |
| 繼承：一具象類別一表 (TPC)           | 是      | 5.0 的延展 ([#3170](https://github.com/dotnet/efcore/issues/3170)) <sup>(1)</sup> |
| 陰影狀態屬性                               |          | 1.0                                   |
| 替代金鑰                                        |          | 1.0                                   |
| 多對多導覽                              | 是      | 已規劃在 5.0 中 ([#19003](https://github.com/dotnet/efcore/issues/19003)) |
| 多對多但沒有聯結實體                      | 是      | 待處理項目 ([#1368](https://github.com/dotnet/efcore/issues/1368)) |
| 金鑰產生：資料庫                              | 是      | 1.0                                   |
| 金鑰產生：用戶端                                |          | 1.0                                   |
| 複雜/擁有的型別                                   | 是      | 2.0                                   |
| 空間資料                                          | 是      | 2.2                                   |
| 模型格式：程式碼                                    | 是      | 1.0                                   |
| 從資料庫建立模型：命令列              | 是      | 1.0                                   |
| 從資料庫更新模型                            | Partial  | 待處理項目 ([#831](https://github.com/dotnet/efcore/issues/831)) |
| 全域查詢篩選條件                                  |          | 2.0                                   |
| 資料表分割                                       | 是      | 2.0                                   |
| 實體分割                                      | 是      | 5.0 的延展 ([#620](https://github.com/dotnet/efcore/issues/620)) <sup>(1)</sup> |
| 資料庫純量函式對應                      | 差     | 2.0                                   |
| 欄位對應                                         |          | 1.1                                   |
| 可為 Null 的參考型別 (C# 8.0)                     |          | 3.0                                   |
| 模型的圖形化視覺效果                      | 是      | 未規劃任何支援 <sup>(2)</sup>     |
| 圖形化模型編輯器                                | 是      | 未規劃任何支援 <sup>(2)</sup>     |
| 模型格式：EDMX (XML)                              | 是      | 未規劃任何支援 <sup>(2)</sup>     |
| 從資料庫建立模型：VS 精靈                 | 是      | 未規劃任何支援 <sup>(2)</sup>     |

### <a name="querying-data"></a>查詢資料

| **功能**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| LINQ 查詢                                          | 是      | 1.0                                   |
| 可讀取產生的 SQL                                | 差     | 1.0                                   |
| GroupBy 轉譯                                   | 是      | 2.1                                   |
| 載入相關資料：積極                           | 是      | 1.0                                   |
| 載入相關資料：衍生類型的積極式載入 |          | 2.1                                   |
| 載入相關資料：消極                            | 是      | 2.1                                   |
| 載入相關資料：明確                        | 是      | 1.1                                   |
| 原始 SQL 查詢：實體類型                         | 是      | 1.0                                   |
| 原始 SQL 查詢：無索引鍵實體類型                 | 是      | 2.1                                   |
| 原始的 SQL 查詢：使用 LINQ 撰寫                  |          | 1.0                                   |
| 明確地編譯查詢                           | 差     | 2.0                                   |
| await foreach (C# 8.0)                                |          | 3.0                                   |
| 以文字為基礎的查詢語言 (Entity SQL)                | 是      | 未規劃任何支援 <sup>(2)</sup>     |

### <a name="saving-data"></a>儲存資料

| **功能**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| 變更追蹤：快照集                             | 是      | 1.0                                   |
| 變更追蹤：通知                         | 是      | 1.0                                   |
| 變更追蹤：Proxy                              | 是      | 5.0 中已合併 ([#10949](https://github.com/dotnet/efcore/issues/10949)) |
| 存取追蹤狀態                               | 是      | 1.0                                   |
| 開放式並行存取                                | 是      | 1.0                                   |
| 交易                                          | 是      | 1.0                                   |
| 陳述式的批次處理                                |          | 1.0                                   |
| 預存程序對應                              | 是      | 待處理項目 ([#245](https://github.com/dotnet/efcore/issues/245)) |
| 已中斷連線的圖形低階 API                     | 差     | 1.0                                   |
| 已中斷連線的圖形端對端                         |          | 1.0 (部分；[#5536](https://github.com/dotnet/efcore/issues/5536)) |

### <a name="other-features"></a>其他功能

| **功能**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| 移轉                                            | 是      | 1.0                                   |
| 資料庫建立/刪除 API                       | 是      | 1.0                                   |
| 種子資料                                             | 是      | 2.1                                   |
| 恢復連線                                 | 是      | 1.1                                   |
| 攔截器                                          | 是      | 3.0                                   |
| 事件                                                | 是      | 3.0 (部分；[#626](https://github.com/dotnet/efcore/issues/626)) |
| 簡易記錄 (Database.Log)                         | 是      | 5.0 中已合併 ([#1199](https://github.com/dotnet/efcore/issues/1199)) |
| DbContext 共用                                     |          | 2.0                                   |

### <a name="database-providers-sup3sup"></a>資料庫提供者 <sup>(3)</sup>

| **功能**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| SQL Server                                            | 是      | 1.0                                   |
| MySQL                                                 | 是      | 1.0                                   |
| PostgreSQL                                            | 是      | 1.0                                   |
| Oracle                                                | 是      | 1.0                                   |
| SQLite                                                | 是      | 1.0                                   |
| SQL Server Compact                                    | 是      | 1.0 <sup>(4)</sup>                    |
| DB2                                                   | 是      | 1.0                                   |
| Firebird                                              | 是      | 2.0                                   |
| Jet (Microsoft Access)                                |          | 2.0 <sup>(4)</sup>                    |
| Azure Cosmos DB                                       |          | 3.0                                   |
| 記憶體內部 (測試用)                               |          | 1.0                                   |

<sup>1</sup> 不可能達到指定版本的延展目標。 但若一切順利，我們就可以嘗試達成目標。

<sup>2</sup> EF Core 不會實作某些 EF6 功能。 這些功能依賴 EF6 的基礎實體資料模型 (EDM) 及/或投資報酬率相對低的複雜功能。 我們一向歡迎您提供意見反應，但 EF Core 雖然做到了許多 EF6 做不到的事，EF Core 卻無法支援 EF6 的所有功能。

<sup>3</sup> 由協力廠商實作的 EF Core 資料庫提供者，在更新為 EF Core 的新主要版本時可能會延遲。 如需詳細資訊，請參閱[資料庫提供者](xref:core/providers/index)。

<sup>4</sup> SQL Server Compact 和 Jet 提供者僅適用於 .NET Framework (不適用於 .NET Core)。

### <a name="supported-platforms"></a>支援的平台

EF Core 3.1 透過使用 .NET Standard 2.0，在 .NET Core 和 .NET Framework 上執行。 不過，EF Core 5.0 不會在 .NET Framework 上執行。 如需詳細資料，請參閱 [平台](xref:core/platforms/index)。

EF6.4 透過多目標在 .NET Core 和 .NET Framework 上執行。

## <a name="guidance-for-new-applications"></a>新應用程式的指引

除非應用程式需要[只有 .NET Framework 支援](/dotnet/standard/choosing-core-framework-server)的項目，否則請在 .NET Core 上使用 EF Core 處理所有新的應用程式。

## <a name="guidance-for-existing-ef6-applications"></a>現有 EF6 應用程式的指引

EF Core 不能無預警取代 EF6。 從 EF6 移至 EF Core 可能需要變更您的應用程式。

將 EF6 應用程式移至 .NET Core 時：

* 如果資料存取程式碼穩定，且不太可能演進或需要新的功能，則請繼續使用 EF6。
* 如果資料存取程式碼不斷演進，或應用程式需要只有 EF Core 提供的新功能，請移轉到 EF Core。
* 通常也會為了效能而移轉到 EF Core。 不過，並非所有案例都能加快速度，所以請事先做一些分析。

如需詳細資訊，請參閱[從 EF6 移植到 EF Core](xref:efcore-and-ef6/porting/index)。
