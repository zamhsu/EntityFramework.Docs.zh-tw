---
title: 資料表分割-EF Core
description: 如何使用 Entity Framework Core 設定資料表分割
author: AndriySvyryd
ms.date: 01/03/2020
uid: core/modeling/table-splitting
ms.openlocfilehash: 71d332ec3f500f48e12863c71ac44ce4d60699f0
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063032"
---
# <a name="table-splitting"></a>資料表分割

EF Core 允許將兩個或多個實體對應至單一資料列。 這稱為「 _資料表分割_ 」或「 _資料表共用_」。

## <a name="configuration"></a>組態

若要使用資料表分割，必須將實體類型對應至相同的資料表、將主鍵對應至相同的資料行，並在一個實體類型的主鍵與另一個實體類型的主鍵之間設定至少一個關聯性。

資料表分割的常見案例是只使用資料表中的資料行子集，以獲得更佳的效能或封裝。

在此範例中， `Order` 表示的子集 `DetailedOrder` 。

[!code-csharp[Order](../../../samples/core/Modeling/TableSplitting/Order.cs?name=Order)]

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/TableSplitting/DetailedOrder.cs?name=DetailedOrder)]

除了所需的設定以外，我們 `Property(o => o.Status).HasColumnName("Status")` 也會呼叫以對應 `DetailedOrder.Status` 至相同的資料行 `Order.Status` 。

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=TableSplitting)]

> [!TIP]
> 請參閱 [完整的範例專案](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting) ，以取得更多內容。

## <a name="usage"></a>使用方式

使用資料表分割來儲存和查詢實體的方式，與其他實體的方式相同：

[!code-csharp[Usage](../../../samples/core/Modeling/TableSplitting/Program.cs?name=Usage)]

## <a name="optional-dependent-entity"></a>選擇性的相依實體

> [!NOTE]
> 這項功能是在 EF Core 3.0 中引進。

如果相依實體所使用的所有資料行都 `NULL` 在資料庫中，則在查詢時不會建立它的實例。 這可讓您建立選擇性的相依實體模型，其中主體的關聯性屬性會是 null。 請注意，如果所有相依的屬性都是選擇性的，且設定為 `null` （可能不是預期），也會發生這種情況。

## <a name="concurrency-tokens"></a>並行權杖

如果任何共用資料表的實體類型都有並行 token，則它也必須包含在所有其他實體類型中。 這是必要的，以便在只更新對應到相同資料表的其中一個實體時，避免過時的並行標記值。

為了避免將並行存取權杖公開給使用中的程式碼，您可以建立一個做為 [陰影屬性](xref:core/modeling/shadow-properties)：

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=ConcurrencyToken&highlight=2)]
