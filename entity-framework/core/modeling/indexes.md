---
title: 索引-EF Core
description: 在 Entity Framework Core 模型中設定索引
author: roji
ms.date: 12/16/2019
ms.assetid: 85b92003-b692-417d-ac1d-76d40dce664b
uid: core/modeling/indexes
ms.openlocfilehash: 1ce40a9219dde56478b1ff8891841b53d9ba1934
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619355"
---
# <a name="indexes"></a>索引

在許多資料存放區中，索引是常見的概念。 雖然它們在資料存放區中的執行方式可能不同，但它們是用來根據資料行 (或資料行集進行查閱，) 更有效率。

無法使用資料批註建立索引。 您可以使用流暢的 API，在單一資料行上指定索引，如下所示：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Index.cs?name=Index&highlight=4)]

您也可以在一個以上的資料行上指定索引：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexComposite.cs?name=Composite&highlight=4)]

> [!NOTE]
> 依照慣例，會在每個屬性 (中建立索引，或使用一組屬性) 做為外鍵。
>
> EF Core 僅針對每一組不同的屬性支援一個索引。 如果您使用流暢的 API 來設定一組屬性的索引，這些屬性已定義索引（依照慣例或先前的設定），則您將變更該索引的定義。 如果您想要進一步設定依慣例建立的索引，這會很有用。

## <a name="index-uniqueness"></a>索引唯一性

根據預設，索引不是唯一的：多個資料列可以有相同的值， (s) 用於索引的資料行集。 您可以建立唯一的索引，如下所示：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexUnique.cs?name=IndexUnique&highlight=5)]

嘗試插入多個具有相同索引資料行集值的實體，將會擲回例外狀況。

## <a name="index-name"></a>索引名稱

依照慣例，在關係資料庫中建立的索引會命名為 `IX_<type name>_<property name>` 。 如果是複合索引， `<property name>` 則會變成以底線分隔的屬性名稱清單。

您可以使用流暢的 API 來設定在資料庫中建立的索引名稱：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexName.cs?name=IndexName&highlight=5)]

## <a name="index-filter"></a>索引篩選

某些關係資料庫可讓您指定篩選或部分索引。 這可讓您只編制資料行值的子集索引，減少索引的大小，並同時改善效能和磁碟空間使用量。 如需 SQL Server 篩選索引的詳細資訊， [請參閱檔](/sql/relational-databases/indexes/create-filtered-indexes)。

您可以使用流暢的 API 來指定索引的篩選準則，提供為 SQL 運算式：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexFilter.cs?name=IndexFilter&highlight=5)]

使用 SQL Server 提供者 EF 時 `'IS NOT NULL'` ，會針對屬於唯一索引一部分的可為 null 的資料行加入篩選。 若要覆寫此慣例，您可以提供 `null` 值。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexNoFilter.cs?name=IndexNoFilter&highlight=6)]

## <a name="included-columns"></a>包含的資料行

某些關係資料庫可讓您設定一組包含在索引中的資料行，但不是其「索引鍵」的一部分。 當查詢中的所有資料行都包含在索引中做為索引鍵或非索引鍵資料行時，這可大幅提升查詢效能，因為資料表本身不需要存取。 如需 SQL Server 內含資料行的詳細資訊， [請參閱檔](/sql/relational-databases/indexes/create-indexes-with-included-columns)。

在下列範例中，資料 `Url` 行是索引鍵的一部分，因此該資料行上的任何查詢篩選都可以使用索引。 此外，只存取和資料行的 `Title` 查詢 `PublishedOn` 不需要存取資料表，而且會更有效率地執行：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexInclude.cs?name=IndexInclude&highlight=5-9)]
