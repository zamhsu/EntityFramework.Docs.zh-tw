---
title: 提供者影響變更的記錄-EF Core
author: ajcvickers
ms.author: avickers
ms.date: 08/08/2018
ms.assetid: 7CEF496E-A5B0-4F5F-B68E-529609B23EF9
ms.technology: entity-framework-core
uid: core/providers/provider-log
ms.openlocfilehash: b911a2da493e20c4e4ce6f1e25024bd0efd38b44
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656121"
---
# <a name="provider-impacting-changes"></a>會影響提供者的變更

此頁面包含在 EF Core 存放庫上提出之提取要求的連結，可能需要其他資料庫提供者的作者進行回應。 其目的是要在將提供者更新為新版本時，為現有協力廠商資料庫提供者的作者提供起點。

我們正在啟動此記錄檔，並從2.1 變更為2.2。 在2.1 之前，我們在我們的問題和提取要求中使用了[`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware)和[`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi)標籤。

## <a name="22-----30"></a>2.2---> 3。0

請注意，許多[應用層級的重大變更](../what-is-new/ef-core-3.0/breaking-changes.md)也會影響提供者。

* <https://github.com/aspnet/EntityFrameworkCore/pull/14022>
  * 已移除過時的 Api 和折迭的選擇性參數多載
  * 已移除 DatabaseColumn。 GetUnderlyingStoreType （）
* <https://github.com/aspnet/EntityFrameworkCore/pull/14589>
  * 已移除過時的 Api
* <https://github.com/aspnet/EntityFrameworkCore/pull/15044>
  * CharTypeMapping 的子類別可能已中斷，因為在基底執行中修正一些 bug 所需的行為變更。
* <https://github.com/aspnet/EntityFrameworkCore/pull/15090>
  * 已新增 IDatabaseModelFactory 的基類，並將其更新為使用辨識物件來減少未來的中斷。
* <https://github.com/aspnet/EntityFrameworkCore/pull/15123>
  * 在 MigrationsSqlGenerator 中使用參數物件，以減少未來的中斷。
* <https://github.com/aspnet/EntityFrameworkCore/pull/14972>
  * 明確設定記錄層級需要對提供者可能會使用的 Api 進行一些變更。 具體而言，如果提供者直接使用記錄基礎結構，則這種變更可能會中斷該使用。 此外，使用基礎結構的提供者（也就是公用）將需要衍生自 `LoggingDefinitions` 或 `RelationalLoggingDefinitions`。 如需範例，請參閱 SQL Server 和記憶體內部提供者。
* <https://github.com/aspnet/EntityFrameworkCore/pull/15091>
  * 核心、關聯式和抽象資源字串現在是公用的。
  * `CoreLoggerExtensions` 和 `RelationalLoggerExtensions` 現在是公用的。 當記錄在核心或關係層級定義的事件時，提供者應該使用這些 Api。 請勿直接存取記錄資源;這些仍然是內部的。
  * `IRawSqlCommandBuilder` 已從單一服務變更為已設定範圍的服務
  * `IMigrationsSqlGenerator` 已從單一服務變更為已設定範圍的服務
* <https://github.com/aspnet/EntityFrameworkCore/pull/14706>
  * 建立關聯式命令的基礎結構已公開，因此提供者可以安全地使用它，並稍微重構。
* <https://github.com/aspnet/EntityFrameworkCore/pull/14733>
  * `ILazyLoader` 已從已設定範圍的服務變更為暫時性服務
* <https://github.com/aspnet/EntityFrameworkCore/pull/14610>
  * `IUpdateSqlGenerator` 已從已設定範圍的服務變更為單一服務
  * 此外，已移除 `ISingletonUpdateSqlGenerator`
* <https://github.com/aspnet/EntityFrameworkCore/pull/15067>
  * 目前提供者所使用的許多內部程式碼都已公開
  * 它應該不會再 necssary 為參考 `IndentedStringBuilder`，因為它已從公開它的地方排除
  * `NonCapturingLazyInitializer` 的使用方式應取代為 BCL 中的 `LazyInitializer`
* <https://github.com/aspnet/EntityFrameworkCore/pull/14608>
  * 這項變更完全涵蓋在應用程式重大變更檔中。 對於提供者而言，這可能會更有影響，因為測試 EF core 通常會導致此問題，因此測試基礎結構已變更，使其更不可能發生。
* <https://github.com/aspnet/EntityFrameworkCore/issues/13961>
  * `EntityMaterializerSource` 已簡化
* <https://github.com/aspnet/EntityFrameworkCore/pull/14895>
  * StartsWith 轉譯以提供者可能想要/需要回應的方式變更
* <https://github.com/aspnet/EntityFrameworkCore/pull/15168>
  * 慣例設定服務已變更。 提供者現在應該繼承自 "ProviderConventionSet" 或 "RelationalConventionSet"。
  * 您可以透過 `IConventionSetCustomizer` 服務加入自訂，但這是為了供其他延伸模組使用，而不是提供者。
  * 在執行時間使用的慣例應從 `IConventionSetBuilder`解析。
* <https://github.com/aspnet/EntityFrameworkCore/pull/15288>
  * 資料植入已重構為公用 API，以避免需要使用內部類型。 這只會影響非關聯式提供者，因為植入是由所有關系型提供者的基底關聯式類別所處理。

## <a name="21-----22"></a>2.1---> 2。2

### <a name="test-only-changes"></a>僅限測試變更

* <https://github.com/aspnet/EntityFrameworkCore/pull/12057>-允許在測試中進行可自訂的 SQL delimeters
  * 在 BuiltInDataTypesTestBase 中允許非嚴格浮點比較的測試變更
  * 測試變更，讓查詢測試可以與不同的 SQL delimeters 重複使用
* <https://github.com/aspnet/EntityFrameworkCore/pull/12072>-將 DbFunction 測試加入至關聯式規格測試
  * 這些測試可以針對所有資料庫提供者執行
* <https://github.com/aspnet/EntityFrameworkCore/pull/12362>-非同步測試清除
  * 移除 `Wait` 呼叫、不必要的非同步，並重新命名一些測試方法
* <https://github.com/aspnet/EntityFrameworkCore/pull/12666>-統一記錄測試基礎結構
  * 已新增 `CreateListLoggerFactory` 並移除一些先前的記錄基礎結構，這將需要使用這些測試的提供者來回應
* <https://github.com/aspnet/EntityFrameworkCore/pull/12500>-以同步和非同步方式執行更多查詢測試
  * 測試名稱和分解已變更，這將需要使用這些測試的提供者來回應
* 在 ComplexNavigations 模型中 <https://github.com/aspnet/EntityFrameworkCore/pull/12766> 重新命名導覽
  * 使用這些測試的提供者可能需要回應
* <https://github.com/aspnet/EntityFrameworkCore/pull/12141>-將內容傳回集區，而不是在功能測試中處置
  * 這種變更包括一些可能需要提供者回應的測試重構

### <a name="test-and-product-code-changes"></a>測試和產品程式碼變更

* <https://github.com/aspnet/EntityFrameworkCore/pull/12109>-合併 RelationalTypeMapping 方法
  * 2\.1 中的變更為允許在衍生類別中簡化的 RelationalTypeMapping。 我們不認為這會中斷提供者，但提供者可以在其衍生的型別對應類別中利用這項變更。
* <https://github.com/aspnet/EntityFrameworkCore/pull/12069> 標記或命名的查詢
  * 新增用於標記 LINQ 查詢的基礎結構，並讓這些標記在 SQL 中顯示為批註。 這可能需要提供者在 SQL 產生方面做出反應。
* <https://github.com/aspnet/EntityFrameworkCore/pull/13115>-透過 NTS 支援空間資料
  * 允許在提供者外部註冊類型對應和成員轉譯器
    * 提供者必須呼叫 base。其 ITypeMappingSource 實現中的 FindMapping （）可讓 it 工作
  * 遵循此模式可將空間支援新增至您的提供者，以在提供者之間保持一致。
* <https://github.com/aspnet/EntityFrameworkCore/pull/13199>-新增服務提供者建立的增強式偵錯工具
  * 可讓 DbCoNtextOptionsExtensions 實作為新介面，協助人們瞭解重新建立內部服務提供者的原因
* <https://github.com/aspnet/EntityFrameworkCore/pull/13289>-新增 CanConnect API 以供健康狀態檢查使用
  * 此 PR 會新增 `CanConnect` 的概念，ASP.NET Core 健康狀態檢查將會使用這些功能來判斷資料庫是否可用。 根據預設，關聯式執行只會呼叫 `Exist`，但提供者可以視需要執行不同的動作。 非關聯式提供者必須執行新的 API，健康狀態檢查才可供使用。
* <https://github.com/aspnet/EntityFrameworkCore/pull/13306>-更新基底 RelationalTypeMapping，使其不會設定 DbParameter 大小
  * 預設會停止設定大小，因為它可能會造成截斷。 如果需要設定大小，提供者可能需要新增自己的邏輯。
* <https://github.com/aspnet/EntityFrameworkCore/pull/13372>-RevEng：一律指定十進位資料行的資料行類型
  * 一律在 scaffold 程式碼中設定十進位資料行的資料行類型，而不是依照慣例進行設定。
  * 提供者不應要求其結尾的任何變更。
* <https://github.com/aspnet/EntityFrameworkCore/pull/13469>-新增用於產生 SQL 案例運算式的 CaseExpression
* <https://github.com/aspnet/EntityFrameworkCore/pull/13648>-新增在 SqlFunctionExpression 上指定型別對應的功能，以改善引數和結果的存放區型別推斷。
