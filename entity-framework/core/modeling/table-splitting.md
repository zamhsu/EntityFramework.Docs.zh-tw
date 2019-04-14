---
title: 資料表分割-EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 04/10/2019
ms.assetid: 0EC2CCE1-BD55-45D8-9EA9-20634987F094
uid: core/modeling/table-splitting
ms.openlocfilehash: 4a0bfaf017106a0bfdff084b1c472bdc17459a89
ms.sourcegitcommit: 8f801993c9b8cd8a8fbfa7134818a8edca79e31a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2019
ms.locfileid: "59562582"
---
# <a name="table-splitting"></a>資料表分割

>[!NOTE]
> 這項功能是在 EF Core 2.0 的新功能。

EF Core，可讓兩個或多個實體對應至單一資料列。 這就叫做_資料表分割_或是_資料表共用_。

## <a name="configuration"></a>組態

若要使用資料表分割的實體類型必須對應至相同的資料表，具有主索引鍵對應至相同的資料行和至少一個設定一個實體類型的主索引鍵與另一個在相同的資料表之間的關聯性。

資料表分割的常見案例更高的效能或封裝，以使用資料表中的資料行子集。

在此範例中`Order`代表子集`DetailedOrder`。

[!code-csharp[Order](../../../samples/core/Modeling/TableSplitting/Order.cs?name=Order)]

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/TableSplitting/DetailedOrder.cs?name=DetailedOrder)]

在我們呼叫除了必要的組態`HasBaseType((string)null)`不必對應`DetailedOrder`中的相同階層`Order`。

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=TableSplitting&highlight=3)]

請參閱[完整的範例專案](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting)詳細內容。

## <a name="usage"></a>使用量

儲存及查詢使用資料表分割的實體都在相同的方式與其他實體，唯一的差別在於，必須插入追蹤共用資料列的所有實體。

[!code-csharp[Usage](../../../samples/core/Modeling/TableSplitting/Program.cs?name=Usage)]

## <a name="concurrency-tokens"></a>並行語彙基元

如果任何共用資料表的實體類型並行語彙基元它必須包含在所有其他實體類型，以避免過時的並行語彙基元的值，只是其中一個實體對應至相同的資料表更新時。

若要避免將其公開到使用的程式碼就可以建立一個陰影狀態中的。

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=ConcurrencyToken&highlight=2)]