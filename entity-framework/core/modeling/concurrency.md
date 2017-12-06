---
title: "並行語彙基元-EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: bc8b1cb0-befe-4b67-8004-26e6c5f69385
ms.technology: entity-framework-core
uid: core/modeling/concurrency
ms.openlocfilehash: 6574a9098d38c4aa525ffb4896adb01082420b5f
ms.sourcegitcommit: 860ec5d047342fbc4063a0de881c9861cc1f8813
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2017
---
# <a name="concurrency-tokens"></a>並行語彙基元

如果屬性設定為並行語彙基元 EF 會檢查沒有其他使用者在儲存變更至該記錄時，已修改該資料庫中的值。 EF 使用開放式並行存取模式，這表示它會假設的值未經變更，然後再次嘗試儲存資料，但它找到的值變更時，會擲回。

比方說，我們可能想設定`LastName`上`Person`是並行語彙基元。 這表示，如果某個使用者嘗試儲存的某些變更`Person`，但另一位使用者已變更`LastName`則會擲回例外狀況。 這可能是需要這樣做，使您的應用程式可以提示使用者，以確保此記錄仍代表相同的實際人員之前將其變更。

> [!NOTE]
> 此頁面說明如何設定並行語彙基元。 請參閱[處理並行](../saving/concurrency.md)如需如何在您的應用程式中使用開放式並行存取的範例。

## <a name="how-concurrency-tokens-work-in-ef"></a>並行語彙基元在 EF 中的運作方式

資料存放區可以檢查任何記錄正在更新或刪除仍有並行語彙基元內容原本從資料庫載入資料時所指定的相同值來強制執行並行語彙基元。

例如，關聯式資料庫來達成此目的包括在並行語彙基元`WHERE`的任何子句`UPDATE`或`DELETE`命令和檢查已受影響的資料列數目。 如果並行語彙基元仍符合，則會更新一個資料列。 如果資料庫中的值已變更，會不更新任何資料列。

```sql
UPDATE [Person] SET [FirstName] = @p1
WHERE [PersonId] = @p0 AND [LastName] = @p2;
```

## <a name="conventions"></a>慣例

依照慣例，屬性會永遠不會設定為並行語彙基元。

## <a name="data-annotations"></a>資料註釋

您可以使用資料註解將屬性設定為並行語彙基元。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Concurrency.cs#ConfigureConcurrencyAnnotations)]

## <a name="fluent-api"></a>關於 fluent 應用程式開發介面

您可以使用 fluent 應用程式開發的應用程式開發介面，將屬性設定為並行語彙基元。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Concurrency.cs#ConfigureConcurrencyFluent)]

## <a name="timestamprow-version"></a>時間戳記/資料列版本

時間戳記是屬性，產生新的值是由資料庫每次插入或更新資料列。 屬性也會被視為並行語彙基元。 這可確保如果其他人已修改的資料列，您嘗試更新您查詢的資料後，就會收到例外狀況。

如何達成這會決定所使用的資料庫提供者。 SQL Server 的時間戳記通常用在*byte []*屬性，將會安裝為*ROWVERSION*資料庫中的資料行。

### <a name="conventions"></a>慣例

依照慣例，屬性會永遠不會設定為時間戳記。

### <a name="data-annotations"></a>資料註釋

您可以使用資料註解屬性設定為時間戳記。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Timestamp.cs#ConfigureTimestampAnnotations)]

### <a name="fluent-api"></a>關於 fluent 應用程式開發介面

您可以使用 fluent 應用程式開發的應用程式開發介面的屬性設定為時間戳記。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Timestamp.cs#ConfigureTimestampFluent)]
