---
title: 索引-EF Core
author: roji
ms.date: 12/16/2019
ms.assetid: 85b92003-b692-417d-ac1d-76d40dce664b
uid: core/modeling/indexes
ms.openlocfilehash: 9565b499ababace3595153e7159e017d2df1cc5a
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526741"
---
# <a name="indexes"></a>索引

索引是許多資料存放區的通用概念。 雖然其在資料存放區中的執行可能會有所不同，但它們是用來讓根據資料行（或資料行集）進行查閱更有效率。

無法使用資料批註來建立索引。 您可以使用流暢的 API，在單一資料行上指定索引，如下所示：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Index.cs?name=Index&highlight=4)]

您也可以在一個以上的資料行上指定索引：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexComposite.cs?name=Composite&highlight=4)]

> [!NOTE]
> 依照慣例，索引會在當做外鍵使用的每一個屬性（或一組屬性）中建立。
>
> EF Core 只支援每一組不同屬性的一個索引。 如果您使用流暢的 API，在已定義索引（依慣例或先前的設定）的一組屬性上設定索引，則您將會變更該索引的定義。 如果您想要進一步設定依照慣例所建立的索引，這會很有用。

## <a name="index-uniqueness"></a>索引唯一性

根據預設，索引不是唯一的：多個資料列允許索引的資料行集具有相同的值。 您可以建立唯一的索引，如下所示：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexUnique.cs?name=IndexUnique&highlight=5)]

嘗試使用相同的索引資料行集值插入多個實體時，將會擲回例外狀況。

## <a name="index-name"></a>索引名稱

依照慣例，在關係資料庫中建立的索引會命名為 `IX_<type name>_<property name>` 。 對於複合索引， `<property name>` 會變成以底線分隔的屬性名稱清單。

您可以使用流暢的 API 來設定在資料庫中建立的索引名稱：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexName.cs?name=IndexName&highlight=5)]

## <a name="index-filter"></a>索引篩選

某些關係資料庫可讓您指定篩選或部分索引。 這可讓您只為數據行值的子集編制索引，減少索引的大小，並同時改善效能和磁碟空間的使用。 如需 SQL Server 篩選索引的詳細資訊，[請參閱檔](/sql/relational-databases/indexes/create-filtered-indexes)集。

您可以使用流暢的 API 來指定索引的篩選準則（以 SQL 運算式形式提供）：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexFilter.cs?name=IndexFilter&highlight=5)]

使用 SQL Server 提供者 EF 時 `'IS NOT NULL'` ，會為屬於唯一索引一部分的所有可為 null 的資料行加入篩選。 若要覆寫此慣例，您可以提供 `null` 值。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexNoFilter.cs?name=IndexNoFilter&highlight=6)]

## <a name="included-columns"></a>包含的資料行

某些關係資料庫可讓您設定一組資料行，這些資料行會包含在索引中，但不屬於其「金鑰」的一部分。 當查詢中的所有資料行都包含在索引中當做索引鍵或非索引鍵資料行時，這可以大幅改善查詢效能，因為資料表本身不需要存取。 如需 SQL Server 內含資料行的詳細資訊，[請參閱檔](/sql/relational-databases/indexes/create-indexes-with-included-columns)集。

在下列範例中，資料 `Url` 行是索引鍵的一部分，因此對該資料行進行的任何查詢篩選都可以使用此索引。 此外，只存取和資料行的 `Title` 查詢 `PublishedOn` 不需要存取資料表，而且會更有效率地執行：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexInclude.cs?name=IndexInclude&highlight=5-9)]
