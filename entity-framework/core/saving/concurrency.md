---
title: 處理並行存取衝突 - EF Core
author: rowanmiller
ms.author: divega
ms.date: 03/03/2018
ms.technology: entity-framework-core
uid: core/saving/concurrency
ms.openlocfilehash: 288d9c6fced5ebbaa2c366248c68547502c3698e
ms.sourcegitcommit: 8f3be0a2a394253efb653388ec66bda964e5ee1b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2018
ms.locfileid: "29745473"
---
# <a name="handling-concurrency-conflicts"></a>處理並行存取衝突

> [!NOTE]
> 本頁記載並行存取在 EF Core 中的運作方式，以及如何處理您應用程式中的並行存取衝突。 如需有關如何在模型中設定並行存取語彙基元的詳細資料，請參閱[並行存取語彙基元](xref:core/modeling/concurrency)。

> [!TIP]
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Concurrency/) \(英文\)。

「資料庫並行存取」係指多個處理程序或使用者同時存取或變更資料庫中的相同資料。 「並行存取控制」係指在發生並行變更時用來確保資料一致性的特定機制。

EF Core 實作「開放式並行存取控制」，意謂著它會讓多個處理程序或使用者獨立進行變更，而無同步處理或鎖定的額外負荷。 在理想的情況下，這些變更不會互相影響，因此將能夠成功。 在最糟的情況下，會有兩個或更多個處理程序嘗試進行衝突變更，而只有其中一個應該會成功。

## <a name="how-concurrency-control-works-in-ef-core"></a>並行存取控制在 EF Core 中如何運作

設定為並行存取語彙基元的屬性會用來實作開放式並行存取控制：每當在 `SaveChanges` 期間執行更新或刪除作業時，資料庫上的並行存取語彙基元值都會與 EF Core 所讀取的原始值進行比較。

- 如果值相符，作業便能完成。
- 如果值不相符，EF Core 就會假設另一位使用者已執行衝突作業，而將目前的交易中止。

當另一位使用者已執行與目前作業衝突的作業時，此情況稱為「並行存取衝突」。

資料庫提供者需負責實作並行存取語彙基元值的比較。

在關聯式資料庫上，EF Core 會檢查所有 `UPDATE` 或 `DELETE` 陳述式的 `WHERE` 子句中是否有並行存取語彙基元的值。 在執行這些陳述式之後，EF Core 會讀取受影響的資料列數目。

如果沒有任何資料列受影響，即表示偵測到並行存取衝突，而 EF Core 就會擲回 `DbUpdateConcurrencyException`。

例如，我們可以將 `Person` 上的 `LastName` 設定為並行存取語彙基元。 然後，在 Person 上進行的任何更新作業就會包含 `WHERE` 子句中的並行存取檢查：

``` sql
UPDATE [Person] SET [FirstName] = @p1
WHERE [PersonId] = @p0 AND [LastName] = @p2;
```

## <a name="resolving-concurrency-conflicts"></a>解決並行存取衝突

接續先前的範例，如果一位使用者嘗試儲存對 `Person` 所做的一些變更，但另一位使用者已經變更 `LastName`，系統將會擲回例外狀況。

此時，應用程式可以直接通知使用者因變更發生衝突而導致更新失敗，然後繼續進行。 但提示使用者確定此記錄仍代表相同的實際人員並重試作業，可能是較理想的做法。

此程序是「解決並行存取衝突」的一個範例。

解決並行存取衝突牽涉到將來自目前 `DbContext` 的擱置中變更與資料庫中的值合併。 要合併的值將因應用程式而異，且可能由使用者輸入來指示。

**有三組值可供協助解決並行存取衝突：**

* 「目前值」係指應用程式嘗試寫入至資料庫的值。

* 「原始值」係指在進行任何編輯之前，原先從資料庫擷取到的值。

* 「資料庫值」係指目前儲存在資料庫中的值。

處理並行存取衝突的一般方法是：

1. 在 `SaveChanges` 期間攔截 `DbUpdateConcurrencyException`。
2. 使用 `DbUpdateConcurrencyException.Entries` 為受影響的實體準備一組新的變更。
3. 重新整理並行存取語彙基元的原始值以反映資料庫中的目前值。
4. 重試處理程序，直到沒有發生任何衝突為止。

在下列範例中，`Person.FirstName` 和 `Person.LastName` 被設定為並行存取語彙基元。 在您包含應用程式特定邏輯以選擇所要儲存值的位置中，有一個 `// TODO:` 註解。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Concurrency/Sample.cs?name=ConcurrencyHandlingCode&highlight=34-35)]
