---
title: 資料表分割-EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 04/10/2019
ms.assetid: 0EC2CCE1-BD55-45D8-9EA9-20634987F094
uid: core/modeling/table-splitting
ms.openlocfilehash: 684fcfbb66debfd1b89e23c8aaf0a32909378c6b
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149191"
---
# <a name="table-splitting"></a>資料表分割

>[!NOTE]
> 這項功能是在 EF Core 2.0 的新功能。

EF Core 允許將兩個或多個實體對應至單一資料列。 這稱為「_資料表分割_」或「_資料表共用_」。

## <a name="configuration"></a>Configuration

若要使用資料表分割，必須將實體類型對應至相同的資料表，請將主鍵對應至相同的資料行，並且至少在一個實體類型的主鍵與相同資料表中的另一個關聯性之間設定一個關聯性。

資料表分割的常見案例是只使用資料表中的資料行子集，以獲得更佳的效能或封裝。

在此範例`Order`中，表示的`DetailedOrder`子集。

[!code-csharp[Order](../../../samples/core/Modeling/TableSplitting/Order.cs?name=Order)]

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/TableSplitting/DetailedOrder.cs?name=DetailedOrder)]

除了所需的設定之外，我們`Property(o => o.Status).HasColumnName("Status")`也`Order.Status`會`DetailedOrder.Status`呼叫來對應至與相同的資料行。

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=TableSplitting&highlight=3)]

> [!TIP]
> 如需詳細內容，請參閱[完整的範例專案](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting)。

## <a name="usage"></a>使用量

使用資料表分割來儲存和查詢實體的方式與其他實體相同。 從 EF Core 3.0 開始，相依實體參考可以是`null`。 如果相依實體所使用的所有資料行都`NULL`是資料庫，則查詢時將不會建立它的實例。 這也會發生在所有屬性都是選擇性的，而且`null`設定為，這可能不是預期的。

[!code-csharp[Usage](../../../samples/core/Modeling/TableSplitting/Program.cs?name=Usage)]

## <a name="concurrency-tokens"></a>並行標記

如果共用資料表的任何實體類型具有並行 token，則必須將它包含在所有其他實體類型中，以避免只有對應到相同資料表的其中一個實體更新時，才會有過時的並行標記值。

為了避免將它公開給取用的程式碼，您可以在陰影狀態中建立一個。

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=ConcurrencyToken&highlight=2)]