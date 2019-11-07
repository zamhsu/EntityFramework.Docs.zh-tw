---
title: 產生的值-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: eb082011-11a1-41b4-a108-15daafa03e80
uid: core/modeling/generated-properties
ms.openlocfilehash: 6643d3c5c9b3363e450e820793f449a41e2eba80
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655752"
---
# <a name="generated-values"></a>產生的值

## <a name="value-generation-patterns"></a>值產生模式

有三個可用於屬性的實值產生模式：

* 未產生值
* Add 上產生的值
* 新增或更新時產生的值

### <a name="no-value-generation"></a>未產生值

[無值產生] 表示您一律會提供有效的值以儲存至資料庫。 這個有效值必須先指派給新的實體，才會加入至內容中。

### <a name="value-generated-on-add"></a>Add 上產生的值

[新增] 上產生的值表示會為新實體產生值。

視所使用的資料庫提供者而定，值可能是由 EF 或資料庫中的用戶端產生。 如果值是由資料庫產生，則當您將實體加入內容時，EF 可能會指派暫存值。 此暫存值接著會在 `SaveChanges()`期間由資料庫產生的值取代。

如果您將實體加入至具有指派給屬性之值的內容，則 EF 會嘗試插入該值，而不是產生新的值。 如果未指派 CLR 預設值（`string`的`null`、`int`的 `0`、`Guid.Empty` 等等），則會將屬性視為具有指派的值。 如需詳細資訊，請參閱[產生的屬性的明確值](../saving/explicit-values-generated-properties.md)。

> [!WARNING]  
> 為新增的實體產生值的方式，將取決於所使用的資料庫提供者。 資料庫提供者可以自動設定某些屬性類型的值產生，但其他則可能需要您手動設定產生值的方式。
>
> 例如，使用 SQL Server 時，將會自動產生 `GUID` 屬性（使用 SQL Server 連續 GUID 演算法）的值。 不過，如果您指定在 add 上產生 `DateTime` 屬性，則必須設定要產生值的方式。 執行此動作的其中一種方式是設定 `GETDATE()`的預設值，請參閱[預設值](relational/default-values.md)。

### <a name="value-generated-on-add-or-update"></a>新增或更新時產生的值

[新增] 或 [更新] 上產生的值表示每次儲存記錄（插入或更新）時，都會產生新的值。

如同 `value generated on add`，如果您在新加入的實體實例上指定屬性的值，就會插入該值，而不是要產生的值。 您也可以在更新時設定明確的值。 如需詳細資訊，請參閱[產生的屬性的明確值](../saving/explicit-values-generated-properties.md)。

> [!WARNING]
> 為新增和更新的實體產生值的方式，將取決於所使用的資料庫提供者。 資料庫提供者可以自動設定某些屬性類型的值產生，而有些則需要您手動設定產生值的方式。
>
> 例如，使用 SQL Server 時，會以 `rowversion` 資料類型來設定在新增或更新時所產生的 `byte[]` 屬性，並標示為並行標記，因此會在資料庫中產生值。 不過，如果您指定在 [新增] 或 [更新] 上產生 `DateTime` 屬性，則必須設定一種方法來產生值。 執行此動作的其中一種方式是設定 `GETDATE()` 的預設值（請參閱[預設值](relational/default-values.md)），以產生新資料列的值。 接著，您可以使用資料庫觸發程式在更新期間（例如下列範例觸發程式）產生值。
>
> [!code-sql[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.sql)]

## <a name="conventions"></a>慣例

依照慣例，將會設定 short、int、long 或 Guid 類型的非複合主鍵，使其具有在 add 上產生的值。 所有其他屬性都將設定為不產生值。

## <a name="data-annotations"></a>資料註釋

### <a name="no-value-generation-data-annotations"></a>沒有值產生（資料批註）

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedNever.cs#Sample)]

### <a name="value-generated-on-add-data-annotations"></a>Add （資料批註）上產生的值

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAdd.cs#Sample)]

> [!WARNING]  
> 這只是讓 EF 知道為新增的實體產生值，並不保證 EF 會設定實際的機制來產生值。 如需詳細資訊，請參閱 add 一節所[產生的值](#value-generated-on-add)。

### <a name="value-generated-on-add-or-update-data-annotations"></a>在新增或更新時產生的值（資料批註）

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAddOrUpdate.cs#Sample)]

> [!WARNING]  
> 這只是讓 EF 知道為新增或更新的實體產生值，並不保證 EF 會設定實際的機制來產生值。 如需詳細資訊，請參閱[add 或 update 一節中產生的值](#value-generated-on-add-or-update)。

## <a name="fluent-api"></a>Fluent API

您可以使用流暢的 API 來變更指定屬性的值產生模式。

### <a name="no-value-generation-fluent-api"></a>無值產生（流暢 API）

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedNever.cs#Sample)]

### <a name="value-generated-on-add-fluent-api"></a>Add （流暢 API）上產生的值

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAdd.cs#Sample)]

> [!WARNING]  
> `ValueGeneratedOnAdd()` 只是讓 EF 知道為新增的實體產生值，並不保證 EF 會設定實際的機制來產生值。  如需詳細資訊，請參閱 add 一節所[產生的值](#value-generated-on-add)。

### <a name="value-generated-on-add-or-update-fluent-api"></a>新增或更新時產生的值（流暢的 API）

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.cs#Sample)]

> [!WARNING]  
> 這只是讓 EF 知道為新增或更新的實體產生值，並不保證 EF 會設定實際的機制來產生值。 如需詳細資訊，請參閱[add 或 update 一節中產生的值](#value-generated-on-add-or-update)。
