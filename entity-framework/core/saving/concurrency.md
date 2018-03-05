---
title: "處理並行衝突 EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 03/03/2018
ms.technology: entity-framework-core
uid: core/saving/concurrency
ms.openlocfilehash: 288d9c6fced5ebbaa2c366248c68547502c3698e
ms.sourcegitcommit: 8f3be0a2a394253efb653388ec66bda964e5ee1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2018
---
# <a name="handling-concurrency-conflicts"></a>處理並行存取衝突

> [!NOTE]
> 此頁面會記載在 EF 核心中的並行存取如何運作以及如何處理您的應用程式中的並行存取衝突。 請參閱[並行語彙基元](xref:core/modeling/concurrency)如需有關如何設定您的模型中的並行語彙基元的詳細資訊。

> [!TIP]
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Concurrency/) \(英文\)。

_資料庫並行_指的情況下，多個處理程序或使用者存取或變更資料庫中相同的資料在相同的時間。 _並行控制_指的是用來確保資料一致性的並行的變更出現在特定的機制。

EF 核心實作_開放式並行存取控制_，也就是說，它會讓多個處理序或使用者進行變更，無同步處理的額外負荷，或鎖定。 在理想的情況下，這些變更不會影響彼此，因此將無法成功。 在最差的情況下，兩個或多個處理程序會嘗試進行衝突的變更，只有其中應該會成功。

## <a name="how-concurrency-control-works-in-ef-core"></a>在 EF 核心中的並行存取控制如何運作

屬性設定為並行語彙基元可用於實作開放式並行存取控制： 每當執行 update 或 delete 作業期間`SaveChanges`，會比對原始資料庫上的並行語彙基元值EF 核心所讀取的值。

- 如果值相符，才能完成此作業。
- 如果值不相符，EF 核心會假設其他使用者已執行衝突的作業，並中止目前交易。

這種情況時另一位使用者已執行與目前作業衝突的作業稱為_並行衝突_。

資料庫提供者會負責實作的並行語彙基元值比較。

關聯式資料庫在 EF 核心包含值的並行語彙基元中的核取`WHERE`的任何子句`UPDATE`或`DELETE`陳述式。 在執行之後的陳述式，EF 核心會讀取受影響的資料列數目。

如果沒有資料列受到影響，偵測到並行衝突時，並 EF 核心擲回`DbUpdateConcurrencyException`。

例如，我們可能會想要設定`LastName`上`Person`是並行語彙基元。 則人員的任何更新作業將會包含在並行存取檢查`WHERE`子句：

``` sql
UPDATE [Person] SET [FirstName] = @p1
WHERE [PersonId] = @p0 AND [LastName] = @p2;
```

## <a name="resolving-concurrency-conflicts"></a>解決並行衝突

繼續上述範例中，如果某個使用者嘗試儲存的某些變更`Person`，但另一位使用者已在變更`LastName`將會擲回例外狀況。

此時，應用程式可能只是通知使用者更新但不會成功，因為有衝突的變更，並且上移動。 但是，它可能會想要提示使用者以確保此記錄仍代表相同的實際人員，並再次嘗試操作。

此程序是範例_解決並行衝突_。

解決並行衝突包括合併暫止的變更，從目前`DbContext`與資料庫中的值。 合併哪些值會根據應用程式而異，而且可能指示由使用者輸入。

**有三組可用來協助解決並行衝突的值：**

* **目前的值**是應用程式嘗試寫入資料庫的值。

* **原始值**原先擷取從資料庫中，進行任何編輯之前的值。

* **資料庫值**是目前資料庫中儲存的值。

處理並行存取衝突的一般方法是：

1. 攔截`DbUpdateConcurrencyException`期間`SaveChanges`。
2. 使用`DbUpdateConcurrencyException.Entries`準備受影響之實體的一組新的變更。
3. 重新整理以反映資料庫中的目前值的並行語彙基元的原始值。
4. 重試處理程序，直到不發生任何衝突。

在下列範例中，`Person.FirstName`和`Person.LastName`是安裝做為並行語彙基元。 沒有`// TODO:`納入應用程式特定的邏輯，以選擇要儲存的值的位置中的註解。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Concurrency/Sample.cs?name=ConcurrencyHandlingCode&highlight=34-35)]
