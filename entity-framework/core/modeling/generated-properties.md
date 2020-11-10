---
title: 產生的值-EF Core
description: 如何在使用 Entity Framework Core 時設定屬性的值產生
author: AndriySvyryd
ms.date: 11/06/2019
uid: core/modeling/generated-properties
ms.openlocfilehash: 347cedbf5fdebc985d75c6cad3c28f17d1344993
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429620"
---
# <a name="generated-values"></a>產生的值

## <a name="value-generation-patterns"></a>值產生模式

有三個值產生模式可用於屬性：

* 不產生任何值
* 在 add 上產生的值
* 在新增或更新時產生的值

### <a name="no-value-generation"></a>不產生任何值

無值產生表示您一律會提供有效的值以儲存至資料庫。 必須先將此有效值指派給新的實體，才能將其加入至內容。

### <a name="value-generated-on-add"></a>在 add 上產生的值

在 add 產生的值表示會為新的實體產生值。

根據所使用的資料庫提供者而定，值可能會由 EF 或資料庫中的用戶端產生。 如果值是由資料庫所產生，則當您將實體新增至內容時，EF 可能會指派暫時值。 這個暫存值接著會由資料庫產生的值取代 `SaveChanges()` 。

如果您將實體新增至有指派給屬性之值的內容，EF 會嘗試插入該值，而不是產生新的值。 如果屬性未獲指派 CLR 預設值 (for、for `null` `string` `0` `int` 、 `Guid.Empty` for、 `Guid` 等 ) ，則會將屬性視為具有指派的值。 如需詳細資訊，請參閱 [產生屬性的明確值](xref:core/saving/explicit-values-generated-properties)。

> [!WARNING]
> 針對新增的實體產生值的方式將取決於所使用的資料庫提供者。 資料庫提供者可能會自動為某些屬性類型設定值產生，但其他專案可能會要求您手動設定值的產生方式。
>
> 例如，使用 SQL Server 時，將會自動產生 `GUID` 屬性 (使用 SQL Server 順序 GUID 演算法) 的值。 但是，如果您指定 `DateTime` 在 add 上產生屬性，您就必須設定一個方法來產生值。 其中一個方法是設定的預設值 `GETDATE()` ，請參閱 [預設值](#default-values)。

### <a name="value-generated-on-add-or-update"></a>在新增或更新時產生的值

在 add 或 update 上產生的值表示每次將記錄儲存 (插入或更新) 時都會產生新的值。

如同 `value generated on add` ，如果您在新加入的實體實例上指定屬性的值，則會插入該值，而不是要產生的值。 您也可以在更新時設定明確值。 如需詳細資訊，請參閱 [產生屬性的明確值](xref:core/saving/explicit-values-generated-properties)。

> [!WARNING]
> 針對新增和更新的實體產生值的方式，將取決於所使用的資料庫提供者。 資料庫提供者可能會自動設定某些屬性類型的值產生，而有些則需要您手動設定值的產生方式。
>
> 例如，使用 SQL Server 時， `byte[]` 設定為 [加入] 或 [更新] 和 [標記為並行標記] 所產生的屬性，將會以資料類型進行設定， `rowversion` 因此將會在資料庫中產生這些值。 但是，如果您指定 `DateTime` 在新增或更新時產生屬性，則必須設定要產生值的方式。 其中一種方法是設定預設值 `GETDATE()` (請參閱 [預設值](#default-values)) ，以產生新資料列的值。 然後，您可以使用資料庫觸發程式在更新期間產生值 (例如下列範例觸發程式) 。
>
> [!code-sql[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.sql)]

## <a name="value-generated-on-add"></a>在 add 上產生的值

依照慣例，如果應用程式未提供值，則 short、int、long 或 Guid 類型的非複合主鍵會設定為已針對插入的實體產生值。 您的資料庫提供者通常會處理必要的設定;例如，SQL Server 中的數值主要索引鍵會自動設定為識別資料行。

您可以設定任何屬性，將其值產生給插入的實體，如下所示：

### <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

***

> [!WARNING]
> 這只是為了讓 EF 知道針對已加入的實體產生值，並不保證 EF 會設定實際的機制來產生值。 如需詳細資訊，請參閱新增一節所 [產生的值](#value-generated-on-add) 。

### <a name="default-values"></a>預設值

在關係資料庫中，可以使用預設值來設定資料行。如果插入的資料列沒有該資料行的值，則會使用預設值。

您可以設定屬性的預設值：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValue.cs?name=DefaultValue&highlight=5)]

您也可以指定用來計算預設值的 SQL 片段：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

指定預設值會將屬性隱含地設定為 add 上產生的值。

## <a name="value-generated-on-add-or-update"></a>在新增或更新時產生的值

### <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

**_

> [!WARNING]
> 這只是為了讓 EF 知道針對已新增或更新的實體產生值，並不保證 EF 會設定實際的機制來產生值。 如需詳細資訊，請參閱 [新增或更新](#value-generated-on-add-or-update) 一節所產生的值。

### <a name="computed-columns"></a>計算資料行

在大部分的關係資料庫上，您可以將資料行設定成在資料庫中計算其值，通常會有運算式參考其他資料行：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=DefaultComputedColumn)]

上述程式會建立 _virtual * 計算資料行，其值會在每次從資料庫提取時計算。 您也可以指定將計算資料行 *儲存* (有時稱為 *保存* ) ，這表示它會在每次更新資料列時計算，並與一般資料行一起儲存在磁片上：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=StoredComputedColumn)]

> [!NOTE]
> EF Core 5.0 已新增建立預存計算資料行的支援。

## <a name="no-value-generation"></a>不產生任何值

如果慣例為值產生設定，則通常需要停用屬性的值產生。 例如，如果您有 int 類型的主鍵，則會隱含地將它設定為 add 所產生的值。您可以透過下列方式停用此功能：

### <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=3)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=5)]

***
