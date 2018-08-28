---
title: 產生的值-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: eb082011-11a1-41b4-a108-15daafa03e80
uid: core/modeling/generated-properties
ms.openlocfilehash: a3656eb1d2dc79ceead04e3a142a58e8afb3cbce
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996773"
---
# <a name="generated-values"></a>產生的值

## <a name="value-generation-patterns"></a>值，產生的模式

有三種可用於屬性的值產生模式。

### <a name="no-value-generation"></a>沒有值產生

沒有值產生表示您將會永遠提供有效的值儲存到資料庫。 有效的值必須指派給新的實體，新增至內容之前。

### <a name="value-generated-on-add"></a>新增產生的值

在產生的值新增表示值產生新的實體。

根據所使用的資料庫提供者，值可能會產生 EF 或在資料庫中的用戶端。 如果由資料庫所產生的值，然後 EF 時可能會指派暫時的值您將實體加入至內容。 期間的資料庫產生值接著會取代此暫存值`SaveChanges()`。

如果您將實體新增至內容，其值指派給屬性時，EF 會嘗試將該值，而不是產生新的。 屬性會被視為具有值，如果它不會指派 CLR 預設值指派 (`null` for `string`，`0`如`int`，`Guid.Empty`如`Guid`等。)。 如需詳細資訊，請參閱 <<c0> [ 產生屬性的明確值](../saving/explicit-values-generated-properties.md)。

> [!WARNING]  
> 值為已加入之實體的產生方式將取決於所使用的資料庫提供者。 資料庫提供者可能會自動設定值，產生的某些屬性類型，但有些則可能需要您手動設定 產生值的方式。
>
> 例如，使用 SQL Server 時，值將會自動產生的`GUID`（使用 SQL Server 的循序 GUID 演算法） 的屬性。 不過，如果您指定`DateTime`產生屬性上新增，則您必須設定要產生之值的方式。 若要這樣做，一個方法是設定預設值是`GETDATE()`，請參閱 <<c2> [ 預設值](relational/default-values.md)。

### <a name="value-generated-on-add-or-update"></a>產生的值上新增或更新

產生的值上新增或更新可讓您表示每次儲存記錄時 （insert 或 update），就會產生新的值。

例如`value generated on add`，如果您的實體，而不是所產生的值，將會插入值的新加入的執行個體上指定屬性的值。 它也可更新時，設定明確的值。 如需詳細資訊，請參閱 <<c0> [ 產生屬性的明確值](../saving/explicit-values-generated-properties.md)。

> [!WARNING]
> 如何新增和更新實體，產生的值將取決於所使用的資料庫提供者。 資料庫提供者可能會自動安裝程式針對某些屬性類型，值產生，而有些則會需要您手動設定 產生值的方式。
> 
> 例如，當使用 SQL Server`byte[]`屬性設定為產生上新增或更新並標示為並行語彙基元，會使用設定`rowversion`資料類型-如此一來，值將會在資料庫中。 不過，如果您指定`DateTime`產生屬性上新增或更新，則您必須設定要產生之值的方式。 若要這樣做，一個方法是設定預設值是`GETDATE()`(請參閱 <<c2> [ 預設值](relational/default-values.md)) 來產生新的資料列的值。 您接著可以使用的資料庫觸發程序來產生期間更新 （例如，下列的範例觸發程序） 的值。
> 
> [!code-sql[Main](../../../samples/core/Modeling/FluentAPI/Samples/ValueGeneratedOnAddOrUpdate.sql)]

## <a name="conventions"></a>慣例

依照慣例，非複合主索引鍵，型別 short、 int、 long、 或是 Guid 將會有值上產生新增的設定。 所有其他屬性不會設定任何值產生。

## <a name="data-annotations"></a>資料註釋

### <a name="no-value-generation-data-annotations"></a>沒有值產生 （資料註解）

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/ValueGeneratedNever.cs#Sample)]

### <a name="value-generated-on-add-data-annotations"></a>在產生的值加入 （資料註解）

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/ValueGeneratedOnAdd.cs#Sample)]

> [!WARNING]  
> 這只是可讓 EF 知道產生新增實體的值，也無法保證 EF 將安裝程式的實際機制來產生值。 請參閱[上產生的值加入](#value-generated-on-add)一節以取得更多詳細資料。

### <a name="value-generated-on-add-or-update-data-annotations"></a>在產生的值新增或更新 （資料註解）

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/ValueGeneratedOnAddOrUpdate.cs#Sample)]

> [!WARNING]  
> 這只是可讓 EF 知道產生新增或更新實體的值，也無法保證 EF 將安裝程式的實際機制來產生值。 請參閱[上產生的值新增或更新](#value-generated-on-add-or-update)一節以取得更多詳細資料。

## <a name="fluent-api"></a>Fluent API

若要變更為指定屬性的值產生模式，您可以使用 Fluent API。

### <a name="no-value-generation-fluent-api"></a>沒有值產生 (Fluent API)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/ValueGeneratedNever.cs#Sample)]

### <a name="value-generated-on-add-fluent-api"></a>在產生的值加入 (Fluent API)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/ValueGeneratedOnAdd.cs#Sample)]

> [!WARNING]  
> `ValueGeneratedOnAdd()` 只可讓 EF 知道產生新增實體的值，也無法保證 EF 將安裝程式的實際機制來產生值。  請參閱[上產生的值加入](#value-generated-on-add)一節以取得更多詳細資料。

### <a name="value-generated-on-add-or-update-fluent-api"></a>在產生的值新增或更新 (Fluent API)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/ValueGeneratedOnAddOrUpdate.cs#Sample)]

> [!WARNING]  
> 這只是可讓 EF 知道產生新增或更新實體的值，也無法保證 EF 將安裝程式的實際機制來產生值。 請參閱[上產生的值新增或更新](#value-generated-on-add-or-update)一節以取得更多詳細資料。
