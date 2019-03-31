---
title: 記錄檔的提供者影響的變更-EF Core
author: ajcvickers
ms.author: avickers
ms.date: 08/08/2018
ms.assetid: 7CEF496E-A5B0-4F5F-B68E-529609B23EF9
ms.technology: entity-framework-core
uid: core/providers/provider-log
ms.openlocfilehash: 229c15ec0402e1706318593a099236f723d80595
ms.sourcegitcommit: ab847dd881d51122e695b7cd8c025fcf3a5a9033
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2019
ms.locfileid: "58678384"
---
# <a name="provider-impacting-changes"></a>提供者影響的變更

此頁面包含提取要求可能需要其他的資料庫提供者，以回應的作者在 EF Core 存放庫上所做的連結。 目的是要讓現有的協力廠商資料庫提供者作者提供一個起始點，其提供者更新為新版本時。

我們會將此記錄檔開頭 2.1 2.2 的變更。 我們用之前 2.1 [ `providers-beware` ](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware)並[ `providers-fyi` ](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi)上我們的問題和提取要求標籤。

## <a name="22-----30"></a>2.2 ---> 3.0

請注意，許多[重大變更的應用程式層級](../what-is-new/ef-core-3.0/breaking-changes.md)也會影響提供者。

* https://github.com/aspnet/EntityFrameworkCore/pull/14022
  * 移除過時的 Api 和摺疊的選擇性參數多載
  * Removed DatabaseColumn.GetUnderlyingStoreType()
* https://github.com/aspnet/EntityFrameworkCore/pull/14589
  * 移除過時的 Api
* https://github.com/aspnet/EntityFrameworkCore/pull/15044
  * 子類別的 CharTypeMapping 可能已損毀，因為在基底實作中修正一些 bug 所需的行為變更。
* https://github.com/aspnet/EntityFrameworkCore/pull/15090
  * 新增 IDatabaseModelFactory 基底類別，並加以更新，以使用參數物件來降低未來的符號。
* https://github.com/aspnet/EntityFrameworkCore/pull/15123
  * 若要降低未來的符號用於 MigrationsSqlGenerator 參數物件。
* https://github.com/aspnet/EntityFrameworkCore/pull/14972
  * 記錄層級明確設定所需的某些變更可能會使用提供者的 Api。 具體來說，如果提供者直接使用記錄基礎結構，則這項變更可能會中斷使用。 此外，使用的基礎結構 （這會是公用的） 的提供者從現在開始會需要衍生自`LoggingDefinitions`或`RelationalLoggingDefinitions`。 請參閱 SQL Server 和範例的記憶體提供者。
* https://github.com/aspnet/EntityFrameworkCore/pull/15091
  * 核心、 關聯式和抽象概念的資源字串現在是公用的。
  * `CoreLoggerExtensions` 和`RelationalLoggerExtensions`現在是公用。 當記錄會在核心或關聯性層級定義的事件時，提供者應該使用這些 Api。 不會存取記錄資源直接;這些是仍內部。
  * `IRawSqlCommandBuilder` 已從單一服務為範圍的服務
  * `IMigrationsSqlGenerator` 已從單一服務為範圍的服務
* https://github.com/aspnet/EntityFrameworkCore/pull/14706
  * 建置關聯式命令的基礎結構具有已變成公用，因此將它安全地提供者使用，並稍微重構。
  * `IRelationalCommandBuilderFactory`已從單一服務為範圍的服務
  * `IShaperCommandContextFactory` 已從單一服務為範圍的服務
  * `ISelectExpressionFactory` 已從單一服務為範圍的服務
* https://github.com/aspnet/EntityFrameworkCore/pull/14733
  * `ILazyLoader` 已從範圍的服務變更為暫時性的服務
* https://github.com/aspnet/EntityFrameworkCore/pull/14610
  * `IUpdateSqlGenerator` 已從範圍的服務變更為單一服務
  * 此外，`ISingletonUpdateSqlGenerator`已移除
* https://github.com/aspnet/EntityFrameworkCore/pull/15067
  * 許多提供者所使用的內部程式碼現在已公開
  * 它不應該再參考 necssary`IndentedStringBuilder`因為它有已從分解出來公開它的位置
  * 用法`NonCapturingLazyInitializer`應該取代成`LazyInitializer`從 BCL
* https://github.com/aspnet/EntityFrameworkCore/pull/14608
  * 這項變更會完整涵蓋應用程式的重大變更文件中。 對於提供者，這可能是多個影響，因為測試的 EF core 通常會導致達到此限制，因此為了達到這個較少可能已變更的測試基礎結構。
* https://github.com/aspnet/EntityFrameworkCore/issues/13961
  * `EntityMaterializerSource` 已簡化，
* https://github.com/aspnet/EntityFrameworkCore/pull/14895
  * StartsWith 轉譯已變更的提供者可能希望/需要因應方式
* https://github.com/aspnet/EntityFrameworkCore/pull/15168
  * 慣例設定服務已變更。 現在應該是提供者繼承自 「 ProviderConventionSet"或"RelationalConventionSet 」。
  * 自訂可以透過新增`IConventionSetCustomizer`服務，但這要使用其他擴充功能，不提供者。
  * 在執行階段使用的慣例應該解決從`IConventionSetBuilder`。

## <a name="21-----22"></a>2.1 ---> 2.2

### <a name="test-only-changes"></a>僅限測試的變更

* [https://github.com/aspnet/EntityFrameworkCore/pull/12057](https://github.com/aspnet/EntityFrameworkCore/pull/12057) -在測試中允許可自訂的 SQL 分隔符號
  * 測試可讓非嚴格的浮動點比較 BuiltInDataTypesTestBase 中的變更
  * 允許要重複使用不同的 SQL 分隔符號使用的查詢測試的測試變更
* [https://github.com/aspnet/EntityFrameworkCore/pull/12072](https://github.com/aspnet/EntityFrameworkCore/pull/12072) -將 DbFunction 測試加入至關聯式規格測試
  * 例如，針對所有的資料庫提供者，可以執行這些測試
* [https://github.com/aspnet/EntityFrameworkCore/pull/12362](https://github.com/aspnet/EntityFrameworkCore/pull/12362) -非同步測試清除
  * 移除`Wait`呼叫，不需要非同步處理，並重新命名某些測試方法
* [https://github.com/aspnet/EntityFrameworkCore/pull/12666](https://github.com/aspnet/EntityFrameworkCore/pull/12666) -統一記錄測試基礎結構
  * 新增`CreateListLoggerFactory`和移除一些先前記錄基礎結構，而這需要使用這些測試回應的提供者
* [https://github.com/aspnet/EntityFrameworkCore/pull/12500](https://github.com/aspnet/EntityFrameworkCore/pull/12500) -執行更多的查詢測試，以同步和非同步
  * 測試名稱和分解已經變更，這將需要使用這些測試回應的提供者
* [https://github.com/aspnet/EntityFrameworkCore/pull/12766](https://github.com/aspnet/EntityFrameworkCore/pull/12766) -重新命名 ComplexNavigations 模型中的巡覽
  * 使用這些測試的提供者可能需要做出回應
* [https://github.com/aspnet/EntityFrameworkCore/pull/12141](https://github.com/aspnet/EntityFrameworkCore/pull/12141) -傳回集區，而不是功能測試在處置內容
  * 這項變更會包含可能需要提供者，以因應一些測試重構


### <a name="test-and-product-code-changes"></a>測試和產品程式碼變更

* [https://github.com/aspnet/EntityFrameworkCore/pull/12109](https://github.com/aspnet/EntityFrameworkCore/pull/12109) -彙總 RelationalTypeMapping.Clone 方法
  * 允許在衍生類別中簡化 RelationalTypeMapping 的 2.1 中的變更。 我們不認為這重大提供者，但提供者可以利用這項變更其衍生類型中對應的類別。
* [https://github.com/aspnet/EntityFrameworkCore/pull/12069](https://github.com/aspnet/EntityFrameworkCore/pull/12069) -已標記或具名查詢
  * 新增用於標記 LINQ 查詢以及做為 SQL 中的註解會顯示這些標記的基礎結構。 這可能需要以回應 SQL 層代中的提供者。
* [https://github.com/aspnet/EntityFrameworkCore/pull/13115](https://github.com/aspnet/EntityFrameworkCore/pull/13115) -支援透過 NTS 的空間資料
  * 讓型別對應和成員轉譯外部提供者註冊
    * 提供者必須呼叫基底。FindMapping() 在 ITypeMappingSource 實作，才能正常運作中
  * 請遵循此模式，以將空間的支援新增至您的提供者的提供者間也保持一致。
* [https://github.com/aspnet/EntityFrameworkCore/pull/13199](https://github.com/aspnet/EntityFrameworkCore/pull/13199) 新增服務提供者建立的增強型偵錯
  * 可讓實作新的介面，可協助人員了解內部的服務提供者正在重新建置 DbContextOptionsExtensions
* [https://github.com/aspnet/EntityFrameworkCore/pull/13289](https://github.com/aspnet/EntityFrameworkCore/pull/13289) -新增 CanConnect API 使用的健康狀態檢查
  * 此提取要求將加入的概念`CanConnect`這會由 ASP.NET Core 健全狀況檢查，來判斷資料庫是否可用。 根據預設，關聯式的實作只會呼叫`Exist`，但如有必要，提供者可以實作不同的項目。 非關聯式的提供者必須實作新的 API，讓健康情況檢查，才能使用。
* [https://github.com/aspnet/EntityFrameworkCore/pull/13306](https://github.com/aspnet/EntityFrameworkCore/pull/13306) -更新基底 RelationalTypeMapping 不設定 DbParameter 大小
  * 停止設定預設的大小，因為它可能會造成截斷。 提供者可能需要新增自己的邏輯，如果需要設定大小。
* (https://github.com/aspnet/EntityFrameworkCore/pull/13372) -RevEng:一定要指定十進位資料行的資料行類型
  * Scaffold 程式碼，而不是設定依照慣例，一律先設定十進位資料行的資料行類型。
  * 提供者應該不需要在其端上的任何變更。
* [https://github.com/aspnet/EntityFrameworkCore/pull/13469](https://github.com/aspnet/EntityFrameworkCore/pull/13469) -將 CaseExpression 加入產生案例 SQL 運算式
* [https://github.com/aspnet/EntityFrameworkCore/pull/13648](https://github.com/aspnet/EntityFrameworkCore/pull/13648) -新增能夠在 SqlFunctionExpression 來改善存放區型別推斷結果引數的指定類型對應。
