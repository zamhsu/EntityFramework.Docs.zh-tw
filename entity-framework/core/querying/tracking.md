---
title: 追蹤與 No-Tracking 的查詢-EF Core
description: Entity Framework Core 中追蹤和無追蹤查詢的資訊
author: smitpatel
ms.date: 11/09/2020
uid: core/querying/tracking
ms.openlocfilehash: 1b3c1db702438390c0de4a2ad5d13e868a522b65
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128897"
---
# <a name="tracking-vs-no-tracking-queries"></a>追蹤與 No-Tracking 的查詢

追蹤行為會控制 Entity Framework Core 是否會在其變更追蹤程式中保留實體實例的相關資訊。 如果追蹤實體，即會在 `SaveChanges()` 期間，將實體中偵測到的任何變更保存於資料庫。 EF Core 也會修正追蹤查詢結果中的實體與變更追蹤器中的實體之間的導覽屬性。

> [!NOTE]
> 永遠不會追蹤[無索引鍵的實體類型](xref:core/modeling/keyless-entity-types)。 本文提及實體類型時，是指已定義索引鍵的實體類型。

> [!TIP]
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Tracking) \(英文\)。

## <a name="tracking-queries"></a>追蹤查詢

預設會追蹤傳回實體類型的查詢。 這表示您可以變更這些實體實例，並讓這些變更持續進行 `SaveChanges()` 。 在下列範例中，將會偵測到對部落格評等的變更，並在 `SaveChanges()` 期間將其保存於資料庫。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#Tracking)]

當追蹤查詢傳回結果時，EF Core 會檢查實體是否已存在於內容中。 如果 EF Core 找到現有的實體，則會傳回相同的實例。 EF Core 不會以資料庫值覆寫專案中實體屬性的目前值和原始值。 如果在內容中找不到實體，EF Core 將會建立新的實體實例，並將它附加至內容。 查詢結果不包含任何加入至內容的實體，但尚未儲存至資料庫。

## <a name="no-tracking-queries"></a>無追蹤查詢

如果要在唯讀案例中使用結果，則不追蹤的查詢很實用。 執行速度較快，因為不需要設定變更追蹤資訊。 如果您不需要更新從資料庫取出的實體，則應該使用無追蹤查詢。 您可以將個別查詢交換為不追蹤。 沒有任何追蹤查詢也會根據資料庫中會忽略任何本機變更或新增實體的結果提供結果。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#NoTracking)]

您也可以在內容執行個體層級變更預設的追蹤行為：

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#ContextDefaultTrackingBehavior)]

## <a name="identity-resolution"></a>身分識別解析

由於追蹤查詢會使用變更追蹤器，因此 EF Core 會在追蹤查詢中進行識別解析。 具體化實體時，如果已追蹤，EF Core 將會從變更追蹤器傳回相同的實體實例。 如果結果多次包含相同的實體，您就會在每次發生時取回相同的實例。 無追蹤查詢不會使用變更追蹤器，也不會進行識別解析。 因此，即使相同的實體包含在結果中多次，您仍會取回實體的新實例。 在 EF Core 3.0 之前的版本中，這個行為不同，請參閱 [先前的版本](#previous-versions)。

從 EF Core 5.0 開始，您可以在相同的查詢中結合上述兩個行為。 也就是說，您可以有沒有追蹤查詢，這會在結果中進行識別解析。 就像可 `AsNoTracking()` 查詢的運算子一樣，我們也新增了另一個運算子 `AsNoTrackingWithIdentityResolution()` 。 列舉中也加入了相關聯的專案 <xref:Microsoft.EntityFrameworkCore.QueryTrackingBehavior> 。 當您將查詢設定為使用沒有追蹤的身分識別解析時，我們會在產生查詢結果時，于背景使用獨立的變更追蹤器，因此每個實例僅具體化一次。 因為這項變更追蹤器與內容中的追蹤程式不同，所以內容不會追蹤結果。 完整列舉查詢之後，變更追蹤器就會超出範圍，並視需要進行垃圾收集。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#NoTrackingWithIdentityResolution)]

## <a name="tracking-and-custom-projections"></a>追蹤和自訂投影

即使查詢的結果型別不是實體型別，EF Core 仍會依預設追蹤包含在結果中的實體類型。 下列查詢會傳回匿名類型，並且將在結果集中追蹤 `Blog` 的執行個體。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#CustomProjection1)]

如果結果集包含來自 LINQ 組合的實體類型，EF Core 將會追蹤這些型別。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#CustomProjection2)]

如果結果集未包含任何實體類型，則不會進行追蹤。 在下列查詢中，我們會從實體 (傳回具有某些值的匿名型別，但不會) 實際實體類型的實例。 查詢沒有任何追蹤的實體。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#CustomProjection3)]

 EF Core 支援在最上層投影中進行用戶端評估。 如果 EF Core 具體化實體實例以進行用戶端評估，則會進行追蹤。 在這裡，因為我們 `blog` 要將實體傳遞給用戶端方法 `StandardizeURL` ，EF Core 也會追蹤 blog 實例。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#ClientProjection)]

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#ClientMethod)]

EF Core 不會追蹤結果中包含的無索引鍵實體實例。 但是 EF Core 會根據上述規則，追蹤實體類型的所有其他實例與索引鍵。

上述一些規則在 EF Core 3.0 之前的運作方式不同。 如需詳細資訊，請參閱 [先前的版本](#previous-versions)。

## <a name="previous-versions"></a>舊版

在3.0 版之前，EF Core 在追蹤的完成方式上有一些差異。 值得注意的差異如下：

- 如 [ [用戶端與伺服器評估](xref:core/querying/client-eval) ] 頁面中所述，在3.0 版之前的查詢的任何部分中 EF Core 支援的用戶端評估。 用戶端評估會造成實體具體化，而不是結果的一部分。 因此 EF Core 分析結果來偵測要追蹤的內容。此設計有一些差異，如下所示：
  - 投射中的用戶端評估，這會造成具體化但未傳回具體化實體實例。 下列範例不會追蹤 `blog` 實體。
    [!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#ClientProjection)]

  - 在某些情況下，EF Core 不會追蹤 LINQ 組合所推出的物件。 下列範例沒有追蹤 `Post` 。
    [!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#CustomProjection2)]

- 每當查詢結果包含無索引鍵的實體類型時，就會進行整個查詢進行非追蹤。 這表示不會追蹤具有索引鍵的實體類型，也就是結果。
- EF Core 在無追蹤查詢中進行身分識別解析。 它使用弱式參考來追蹤已經傳回的實體。 因此，如果結果集包含相同的實體多次，則每次出現時，您都會取得相同的實例。 雖然先前具有相同身分識別的結果超出範圍，且已進行垃圾收集，但 EF Core 會傳回新的實例。
