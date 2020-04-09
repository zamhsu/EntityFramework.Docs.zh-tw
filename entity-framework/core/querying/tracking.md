---
title: 追蹤與無追蹤查詢 - EF 核心
author: smitpatel
ms.date: 10/10/2019
ms.assetid: e17e060c-929f-4180-8883-40c438fbcc01
uid: core/querying/tracking
ms.openlocfilehash: a6c71c12f429f1324abe91d1b2cef96312bec051
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "78417646"
---
# <a name="tracking-vs-no-tracking-queries"></a>追蹤與無追蹤查詢

跟蹤行為控制實體框架核心是否在其更改跟蹤器中保留有關實體實例的資訊。 如果追蹤實體，即會在 `SaveChanges()` 期間，將實體中偵測到的任何變更保存於資料庫。 EF Core 還將修復追蹤查詢結果中的實體和更改跟蹤器中的實體之間的導航屬性。

> [!NOTE]
> 從不追蹤[無鍵實體型態](xref:core/modeling/keyless-entity-types)。 無論本文提到實體類型,它是指定義了密鑰的實體類型。

> [!TIP]  
> 您可以在 GitHub 上查看本文[的範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying)。

## <a name="tracking-queries"></a>追蹤查詢

預設會追蹤傳回實體類型的查詢。 這意味著您可以對這些實體實例進行更改,並保留這些變更`SaveChanges()`。 在下列範例中，將會偵測到對部落格評等的變更，並在 `SaveChanges()` 期間將其保存於資料庫。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#Tracking)]

## <a name="no-tracking-queries"></a>無追蹤查詢

如果要在唯讀案例中使用結果，則不追蹤的查詢很實用。 它們執行得更快,因為無需設置更改跟蹤資訊。 如果不需要更新從資料庫中檢索的實體,則應使用無跟蹤查詢。 您可以將單個查詢交換為無跟蹤。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#NoTracking)]

您也可以在內容執行個體層級變更預設的追蹤行為：

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#ContextDefaultTrackingBehavior)]

## <a name="identity-resolution"></a>身分識別解析

由於追蹤查詢使用更改跟蹤器,EF Core 將在追蹤查詢中執行標識解析。 實體具體化時,如果已跟蹤該實體,EF Core 將從更改跟蹤器返回同一實體實例。 如果結果多次包含同一實體,則每個匹配項都會返回同一實例。 無追蹤查詢不使用更改跟蹤器,也不會執行標識解析。 因此,即使同一實體多次包含在結果中,您也會返回實體的新實例。 此行為在 EF Core 3.0 之前的版本不同,請參閱[以前的版本](#previous-versions)。

## <a name="tracking-and-custom-projections"></a>追蹤並自訂投影

即使查詢的結果類型不是實體類型,EF Core 仍將在預設情況下跟蹤結果中包含的實體類型。 下列查詢會傳回匿名類型，並且將在結果集中追蹤 `Blog` 的執行個體。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#CustomProjection1)]

如果結果集包含來自 LINQ 組合的實體類型,EF Core 將追蹤它們。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#CustomProjection2)]

如果結果集不包含任何實體類型,則不執行任何跟蹤。 在下面的查詢中,我們返回一個匿名類型,其中包含來自實體的某些值(但沒有實際實體類型的實例)。 查詢中沒有跟蹤的實體。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#CustomProjection3)]

 EF Core 支援在頂級投影中執行客戶端評估。 如果 EF Core 實現了用於客戶端評估的實體實例,則將跟蹤該實例。 在這裏,由於我們將實體傳遞`blog`給`StandardizeURL`用戶端方法 ,EF Core 也將跟蹤博客實例。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#ClientProjection)]

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#ClientMethod)]

EF Core 不跟蹤結果中包含的無鑰匙實體實例。 但是,EF Core 根據上述規則使用密鑰跟蹤實體類型的所有其他實例。

在 EF Core 3.0 之前,上述某些規則的工作方式不同。 有關詳細資訊,請參閱[以前的版本](#previous-versions)。

## <a name="previous-versions"></a>舊版本

在版本 3.0 之前,EF Core 在追蹤方式上存在一些差異。 顯著差異如下:

- 如[在用戶端與伺服器評估](xref:core/querying/client-eval)頁中所述,EF Core 支援在版本 3.0 之前查詢的任何部分中的客戶端評估。 用戶端評估導致實體的物化,而實體不是結果的一部分。 因此,EF Core 分析了結果,以檢測要跟蹤的內容。此設計存在以下某些差異:
  - 投影中的客戶端評估導致具體化,但沒有返回具體化實體實例。 以下示例未跟蹤`blog`實體。
    [!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#ClientProjection)]

  - 在某些情況下,EF Core 未跟蹤 LINQ 組合物中的物件。 下面的範例未追蹤`Post`。
    [!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#CustomProjection2)]

- 每當查詢結果包含無鍵實體類型時,整個查詢都會被非跟蹤。 這意味著實體類型與鍵,結果也沒有被跟蹤。
- EF Core 在無跟蹤查詢中做了標識解析。 它使用弱引用來跟蹤已返回的實體。 因此,如果結果集多次包含相同的實體,則每個匹配項都會獲得相同的實例。 儘管如果以前具有相同標識的結果已出範圍並回收了垃圾,但 EF Core 返回了一個新實例。
