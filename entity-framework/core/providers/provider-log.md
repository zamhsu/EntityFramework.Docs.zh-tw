---
title: 提供者影響的變更記錄-EF Core
description: Entity Framework Core 中影響提供者的變更記錄檔
author: ajcvickers
ms.date: 08/08/2018
uid: core/providers/provider-log
ms.openlocfilehash: 8ba2c738c87ea918e5f2c10f4446a7043c97a501
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430153"
---
# <a name="provider-impacting-changes"></a>會影響提供者的變更

此頁面包含在 EF Core 存放庫上提出之提取要求的連結，可能需要其他資料庫提供者的作者進行回應。 其目的是要在將提供者更新為新版本時，為現有協力廠商資料庫提供者的作者提供起點。

我們會從2.1 到2.2 的變更開始此記錄檔。 在2.1 之前，我們使用 [`providers-beware`](https://github.com/dotnet/efcore/labels/providers-beware) 和 [`providers-fyi`](https://github.com/dotnet/efcore/labels/providers-fyi) 標籤來解決問題和提取要求。

## <a name="22-----3x"></a>2.2---> 3。x

請注意，許多 [應用層級的重大變更](xref:core/what-is-new/ef-core-3.x/breaking-changes) 也會影響提供者。

* <https://github.com/dotnet/efcore/pull/14022>
  * 移除淘汰的 Api 和折迭的選擇性參數多載
  * 已移除 DatabaseColumn. GetUnderlyingStoreType ( # A1
* <https://github.com/dotnet/efcore/pull/14589>
  * 已移除淘汰的 Api
* <https://github.com/dotnet/efcore/pull/15044>
  * CharTypeMapping 的子類別可能已中斷，因為修正基底執行中的幾個錯誤所需的行為變更。
* <https://github.com/dotnet/efcore/pull/15090>
  * 新增 IDatabaseModelFactory 的基類，並將其更新為使用辨識物件來減少未來的中斷。
* <https://github.com/dotnet/efcore/pull/15123>
  * 使用 MigrationsSqlGenerator 中的參數物件來減少未來的中斷。
* <https://github.com/dotnet/efcore/pull/14972>
  * 明確設定記錄層級，需要提供者可能會使用的 Api 有一些變更。 具體而言，如果提供者會直接使用記錄基礎結構，則這種變更可能會中斷該用途。 此外，使用基礎結構 (的提供者將會) 向前公開的，必須衍生自 `LoggingDefinitions` 或 `RelationalLoggingDefinitions` 。 如需範例，請參閱 SQL Server 和記憶體中的提供者。
* <https://github.com/dotnet/efcore/pull/15091>
  * Core、關聯式和抽象資源字串現在是公用的。
  * `CoreLoggerExtensions` 和 `RelationalLoggerExtensions` 現在是公用的。 當記錄在核心或關聯式層級定義的事件時，提供者應該使用這些 Api。 請勿直接存取記錄資源;這些仍是內部的。
  * `IRawSqlCommandBuilder` 從單一服務變更為已設定範圍的服務
  * `IMigrationsSqlGenerator` 從單一服務變更為已設定範圍的服務
* <https://github.com/dotnet/efcore/pull/14706>
  * 建立關聯式命令的基礎結構已成為公用，因此提供者可以安全地使用它，並稍微重構。
* <https://github.com/dotnet/efcore/pull/14733>
  * `ILazyLoader` 已從範圍服務變更為暫時性服務
* <https://github.com/dotnet/efcore/pull/14610>
  * `IUpdateSqlGenerator` 已從範圍服務變更為單一服務
  * 此外，已 `ISingletonUpdateSqlGenerator` 移除
* <https://github.com/dotnet/efcore/pull/15067>
  * 許多提供者所使用的內部程式碼現已公開
  * 它應該不會再 necssary 參考， `IndentedStringBuilder` 因為它已從公開的位置中分解出來
  * 的使用 `NonCapturingLazyInitializer` 方式應取代為 `LazyInitializer` BCL 中的
* <https://github.com/dotnet/efcore/pull/14608>
  * 這項變更會在應用程式重大變更檔中完整涵蓋。 對於提供者而言，這可能會產生更大的影響，因為測試 EF core 通常會導致這個問題發生，因此測試基礎結構的變更可能不太可能。
* <https://github.com/dotnet/efcore/issues/13961>
  * `EntityMaterializerSource` 已簡化
* <https://github.com/dotnet/efcore/pull/14895>
  * StartsWith 轉譯已變更為提供者可能想要/需要回應的方式
* <https://github.com/dotnet/efcore/pull/15168>
  * 慣例設定服務已變更。 提供者現在應該繼承自 "ProviderConventionSet" 或 "RelationalConventionSet"。
  * 您可以透過服務新增自訂 `IConventionSetCustomizer` ，但這是為了供其他擴充功能使用，而非提供者使用。
  * 在執行時間時使用的慣例應該解析為 `IConventionSetBuilder` 。
* <https://github.com/dotnet/efcore/pull/15288>
  * 資料植入已重構為公用 API，以避免需要使用內部類型。 這應該只會影響非關聯式提供者，因為植入是由所有關系型提供者的基底關聯式類別所處理。

## <a name="21-----22"></a>2.1---> 2。2

### <a name="test-only-changes"></a>僅限測試變更

* <https://github.com/dotnet/efcore/pull/12057> -允許測試中可自訂的 SQL 分隔符號
  * 在 BuiltInDataTypesTestBase 中允許非嚴格浮點數比較的測試變更
  * 測試可讓查詢測試與不同 SQL 分隔符號重複使用的變更
* <https://github.com/dotnet/efcore/pull/12072> -將 DbFunction 測試加入至關聯式規格測試
  * 可以針對所有資料庫提供者執行這些測試
* <https://github.com/dotnet/efcore/pull/12362> -非同步測試清除
  * 移除 `Wait` 呼叫、不需要的非同步，並重新命名某些測試方法
* <https://github.com/dotnet/efcore/pull/12666> -統一記錄測試基礎結構
  * 已新增 `CreateListLoggerFactory` 和移除先前的一些記錄基礎結構，此基礎結構將需要使用這些測試的提供者來回應
* <https://github.com/dotnet/efcore/pull/12500> -以同步和非同步方式執行更多查詢測試
  * 測試名稱和分解已變更，這將需要使用這些測試的提供者回應
* <https://github.com/dotnet/efcore/pull/12766> -重新命名 ComplexNavigations 模型中的導覽
  * 使用這些測試的提供者可能需要回應
* <https://github.com/dotnet/efcore/pull/12141> -將內容傳回到集區，而不是在功能測試中處置
  * 這種變更包含一些可能需要提供者回應的測試重構

### <a name="test-and-product-code-changes"></a>測試和產品程式碼變更

* <https://github.com/dotnet/efcore/pull/12109> -合併 RelationalTypeMapping 複製方法
  * 對 RelationalTypeMapping 2.1 所做的變更，在衍生類別中允許簡化。 我們不認為這會中斷給提供者，但提供者可以在其衍生型別對應類別中利用這項變更。
* <https://github.com/dotnet/efcore/pull/12069> -已標記或命名的查詢
  * 新增用來標記 LINQ 查詢的基礎結構，並在 SQL 中將這些標記顯示為批註。 這可能需要提供者在 SQL 產生中做出反應。
* <https://github.com/dotnet/efcore/pull/13115> -透過 NTS 支援空間資料
  * 允許在提供者之外註冊類型對應和成員轉譯器
    * 提供者必須呼叫基底。FindMapping 在其 ITypeMappingSource 實施中 ( # A1 以使其運作
  * 遵循此模式，將空間支援新增至提供者之間一致的提供者。
* <https://github.com/dotnet/efcore/pull/13199> -新增增強的偵錯工具，以建立服務提供者
  * 允許 DbCoNtextOptionsExtensions 執行新的介面，以協助人們瞭解如何重新建立內部服務提供者
* <https://github.com/dotnet/efcore/pull/13289> -新增 CanConnect API 供健康情況檢查使用
  * 此 PR `CanConnect` 會新增 ASP.NET Core 健康狀態檢查所使用的概念，以判斷資料庫是否可供使用。 根據預設，關聯式執行只會呼叫 `Exist` ，但是提供者可以視需要執行不同的動作。 非關聯式提供者將需要執行新的 API，才能讓健康情況檢查可供使用。
* <https://github.com/dotnet/efcore/pull/13306> -將基底 RelationalTypeMapping 更新為不設定 DbParameter 大小
  * 預設會停止設定大小，因為它可能會導致截斷。 如果需要設定大小，則提供者可能需要新增自己的邏輯。
* <https://github.com/dotnet/efcore/pull/13372> -RevEng：一律指定 decimal 資料行的資料行類型
  * 一律為 scaffold 程式碼中的十進位資料行設定資料行類型，而不是依照慣例進行設定。
  * 提供者應該不需要在其端上進行任何變更。
* <https://github.com/dotnet/efcore/pull/13469> -新增產生 SQL 案例運算式的 CaseExpression
* <https://github.com/dotnet/efcore/pull/13648> -新增在 SqlFunctionExpression 上指定類型對應的功能，以改善引數和結果的存放區類型推斷。
