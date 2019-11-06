---
title: 追蹤與不追蹤查詢-EF Core
author: smitpatel
ms.date: 10/10/2019
ms.assetid: e17e060c-929f-4180-8883-40c438fbcc01
uid: core/querying/tracking
ms.openlocfilehash: 66988f936ab75e17620398c8f21e4a32bbc950bd
ms.sourcegitcommit: 37d0e0fd1703467918665a64837dc54ad2ec7484
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72445946"
---
# <a name="tracking-vs-no-tracking-queries"></a>追蹤與不追蹤查詢的比較

追蹤行為會控制 Entity Framework Core 是否會在其變更追蹤程式中保存實體實例的相關資訊。 如果追蹤實體，即會在 `SaveChanges()` 期間，將實體中偵測到的任何變更保存於資料庫。 EF Core 也會修正追蹤查詢結果中的實體與變更追蹤器中的實體之間的導覽屬性。

> [!NOTE]
> 永遠不會追蹤[無索引鍵的實體類型](xref:core/modeling/keyless-entity-types)。 本文提及實體類型的任何地方，都是指已定義索引鍵的實體類型。

> [!TIP]  
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) \(英文\)。

## <a name="tracking-queries"></a>追蹤查詢

預設會追蹤傳回實體類型的查詢。 這表示您可以對這些實體實例進行變更，並 `SaveChanges()` 保存這些變更。 在下列範例中，將會偵測到對部落格評等的變更，並在 `SaveChanges()` 期間將其保存於資料庫。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#Tracking)]

## <a name="no-tracking-queries"></a>無追蹤查詢

如果要在唯讀案例中使用結果，則不追蹤的查詢很實用。 因為不需要設定變更追蹤資訊，所以執行速度會更快。 如果您不需要更新從資料庫中取出的實體，則應該使用無追蹤查詢。 您可以將個別查詢交換為不追蹤。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#NoTracking)]

您也可以在內容執行個體層級變更預設的追蹤行為：

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#ContextDefaultTrackingBehavior)]

## <a name="identity-resolution"></a>身分識別解析

由於追蹤查詢會使用變更追蹤器，因此 EF Core 會在追蹤查詢中執行識別解析。 當具體化實體時，如果已追蹤，EF Core 將會從變更追蹤程式傳回相同的實體實例。 如果結果多次包含相同的實體，您就會在每次發生時取得相同的實例。 無追蹤查詢不會使用變更追蹤器，也不會執行識別解析。 如此一來，即使相同的實體多次包含在結果中，您也會得到新的實體實例。 在 EF Core 3.0 之前的版本中，此行為不同，請參閱[先前的版本](#previous-versions)。

## <a name="tracking-and-custom-projections"></a>追蹤和自訂投影

即使查詢的結果型別不是實體型別，EF Core 仍會預設追蹤包含在結果中的實體類型。 下列查詢會傳回匿名類型，並且將在結果集中追蹤 `Blog` 的執行個體。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#CustomProjection1)]

如果結果集包含來自 LINQ 撰寫的實體類型，EF Core 將會加以追蹤。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#CustomProjection2)]

如果結果集未包含任何實體類型，則不會進行任何追蹤。 在下列查詢中，我們會傳回匿名型別與實體的某些值（但不含實際實體類型的實例）。 查詢中沒有任何已追蹤的實體。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#CustomProjection3)]

 EF Core 支援在最上層投影中進行用戶端評估。 如果 EF Core 具體化實體實例以進行用戶端評估，則會進行追蹤。 在這裡，因為我們會將 @no__t 0 實體傳遞至用戶端方法 `StandardizeURL`，EF Core 也會追蹤 blog 實例。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#ClientProjection)]

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#ClientMethod)]

EF Core 不會追蹤結果中包含的無索引鍵實體實例。 但是 EF Core 會根據上述規則，追蹤實體類型的所有其他實例與索引鍵。

在 EF Core 3.0 之前，部分規則的運作方式不同。 如需詳細資訊，請參閱[先前的版本](#previous-versions)。

## <a name="previous-versions"></a>舊版本

在3.0 版之前，EF Core 在追蹤完成的方式上有一些差異。 值得注意的差異如下：

- 如[用戶端與伺服器評估](xref:core/querying/client-eval)頁面中所述，在3.0 版之前，請在查詢的任何部分中 EF Core 支援的用戶端評估。 用戶端評估導致實體具體化，這不是結果的一部分。 因此 EF Core 分析結果來偵測要追蹤的內容。這種設計有一些差異，如下所示：
  - 預測中的用戶端評估，這會導致具體化，但未傳回具體化實體實例。 下列範例未追蹤 `blog` 的實體。
    [!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#ClientProjection)]

  - 在某些情況下，EF Core 不會追蹤由 LINQ 撰寫所傳入的物件。 下列範例未追蹤 `Post`。
    [!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#CustomProjection2)]

- 每當查詢結果包含無索引鍵的實體類型時，整個查詢就會進行非追蹤。 這表示不會追蹤具有索引鍵的實體類型，也就是結果中的。
- EF Core 沒有追蹤查詢中的身分識別解析。 它使用弱式參考來追蹤已經傳回的實體。 因此，如果結果集包含相同的實體倍數，您就會針對每個發生次數取得相同的實例。 不過，如果先前具有相同身分識別的結果超出範圍，且已進行垃圾收集，EF Core 會傳回新的實例。
