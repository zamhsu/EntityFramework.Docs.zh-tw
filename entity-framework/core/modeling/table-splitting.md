---
title: 資料表分割-EF Core
description: 如何使用 Entity Framework Core 設定資料表分割
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 01/03/2020
uid: core/modeling/table-splitting
ms.openlocfilehash: c38d3ee0efa82f84a1051017ae40c9f3fdd57f1f
ms.sourcegitcommit: 4e86f01740e407ff25e704a11b1f7d7e66bfb2a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2020
ms.locfileid: "75781166"
---
# <a name="table-splitting"></a>資料表分割

EF Core 允許將兩個或多個實體對應至單一資料列。 這稱為「_資料表分割_」或「_資料表共用_」。

## <a name="configuration"></a>組態

若要使用資料表分割，必須將實體類型對應至相同的資料表，請將主鍵對應至相同的資料行，並且至少在一個實體類型的主鍵與相同資料表中的另一個關聯性之間設定一個關聯性。

資料表分割的常見案例是只使用資料表中的資料行子集，以獲得更佳的效能或封裝。

在此範例中 `Order` 代表 `DetailedOrder`的子集。

[!code-csharp[Order](../../../samples/core/Modeling/TableSplitting/Order.cs?name=Order)]

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/TableSplitting/DetailedOrder.cs?name=DetailedOrder)]

除了所需的設定之外，我們還會呼叫 `Property(o => o.Status).HasColumnName("Status")` 將 `DetailedOrder.Status` 對應到與 `Order.Status`相同的資料行。

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=TableSplitting)]

> [!TIP]
> 如需詳細內容，請參閱[完整的範例專案](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting)。

## <a name="usage"></a>使用

使用資料表分割來儲存和查詢實體的方式與其他實體相同：

[!code-csharp[Usage](../../../samples/core/Modeling/TableSplitting/Program.cs?name=Usage)]

## <a name="optional-dependent-entity"></a>選擇性的相依實體

> [!NOTE]
> 這項功能是在 EF Core 3.0 中引進。

如果相依實體所使用的所有資料行都 `NULL` 在資料庫中，則查詢時不會建立它的實例。 這可讓您建立選擇性相依實體的模型，其中主體上的關聯性屬性會是 null。 請注意，這也會發生在所有相依的屬性都是選擇性的，而且設定為 `null`，這可能不是預期的。

## <a name="concurrency-tokens"></a>並行標記

如果共用資料表的任何實體類型有並行 token，則也必須將它包含在所有其他實體類型中。 這是必要的，以便在只更新對應至相同資料表的其中一個實體時，避免過時的並行標記值。

若要避免將並行權杖公開給取用程式碼，您可以建立一個做為[陰影屬性](xref:core/modeling/shadow-properties)：

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=ConcurrencyToken&highlight=2)]
