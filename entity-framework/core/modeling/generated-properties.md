---
title: 產生的值-EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: eb082011-11a1-41b4-a108-15daafa03e80
ms.technology: entity-framework-core
uid: core/modeling/generated-properties
ms.openlocfilehash: 88ccc2da3c2b6cbba8920d7113c82e769b459897
ms.sourcegitcommit: 507a40ed050fee957bcf8cf05f6e0ec8a3b1a363
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2018
ms.locfileid: "31812634"
---
# <a name="generated-values"></a>產生的值

## <a name="value-generation-patterns"></a>值，產生的模式

有三種可以用於屬性的值產生模式。

### <a name="no-value-generation"></a>沒有值產生

沒有值層代表示您將一律提供有效的值，以儲存到資料庫。 有效的值必須指派給新的實體，再新增至內容。

### <a name="value-generated-on-add"></a>在產生的值，則新增

在產生的值加入表示針對新的實體所產生的值。

根據所使用的資料庫提供者，值可能會產生 EF 或在資料庫中的用戶端。 如果由資料庫所產生的值，然後 EF 可能指派暫存值，當您將實體加入至內容。 然後將由期間的資料庫產生值取代這個暫存值`SaveChanges()`。

如果您加入實體指派給屬性的值的內容，然後 EF 會嘗試插入的值，而不會產生一個新。 屬性會被視為具有值，如果它不指定 CLR 預設值指派 (`null`如`string`，`0`如`int`，`Guid.Empty`如`Guid`等。)。 如需詳細資訊，請參閱[產生屬性的明確值](../saving/explicit-values-generated-properties.md)。

> [!WARNING]  
> 加入的實體產生值的方式將取決於所使用的資料庫提供者。 資料庫提供者可能會自動設定某些屬性類型，為所產生的值，但有些則可能需要您手動安裝程式如何產生值。
>
> 例如，使用 SQL Server 時，值就會自動產生的`GUID`（使用 SQL Server 的循序 GUID 演算法） 的屬性。 不過，如果您指定`DateTime`產生屬性上新增，則您必須設定要產生之值的方式。 若要這樣做的一種方式為設定預設值是`GETDATE()`，請參閱[預設值](relational/default-values.md)。

### <a name="value-generated-on-add-or-update"></a>產生的值上加入或更新

產生的值上加入或更新，表示每次 （insert 或 update），則會儲存記錄，就會產生新的值。

像`value generated on add`，如果您的實體，而不是所產生的值，將會插入值的新加入的執行個體上指定屬性的值。 它也可更新時，設定明確的值。 如需詳細資訊，請參閱[產生屬性的明確值](../saving/explicit-values-generated-properties.md)。

> [!WARNING]
> 如何新增和更新的實體產生值將取決於所使用的資料庫提供者。 而有些則會需要您手動安裝程式如何產生值的資料庫提供者可能會自動安裝程式為某些屬性類型，所產生的值。
> 
> 例如，當使用 SQL Server`byte[]`屬性設定為產生上新增或更新並標示為並行語彙基元，將會安裝於`rowversion`資料型別-如此將會在資料庫中產生的值。 不過，如果您指定`DateTime`產生屬性上加入或更新，則您必須設定要產生之值的方式。 若要這樣做的一種方式為設定預設值是`GETDATE()`(請參閱[預設值](relational/default-values.md)) 來產生新的資料列的值。 您接著可以使用的資料庫觸發程序期間更新 （例如，下列的範例觸發程序） 產生的值。
> 
> [!code-sql[Main](../../../samples/core/Modeling/FluentAPI/Samples/ValueGeneratedOnAddOrUpdate.sql)]

## <a name="conventions"></a>慣例

依照慣例，非複合主索引鍵的型別 short、 int、 long、 或 Guid 將安裝程式已新增產生的值。 所有其他屬性沒有值層代將安裝程式。

## <a name="data-annotations"></a>資料註釋

### <a name="no-value-generation-data-annotations"></a>沒有值層代 （資料附註）

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/ValueGeneratedNever.cs#Sample)]

### <a name="value-generated-on-add-data-annotations"></a>在產生的值加入 （資料附註）

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/ValueGeneratedOnAdd.cs#Sample)]

> [!WARNING]  
> 這只可讓 EF 知道的值會產生已加入的實體，並不保證 EF 將會安裝的實際機制來產生值。 請參閱[上產生的值，則新增](#value-generated-on-add)> 一節以取得詳細資料。

### <a name="value-generated-on-add-or-update-data-annotations"></a>在產生的值，則新增或更新 （資料附註）

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/ValueGeneratedOnAddOrUpdate.cs#Sample)]

> [!WARNING]  
> 這只可讓 EF 知道的值會產生已加入或更新實體，並不保證 EF 將會安裝的實際機制來產生值。 請參閱[上產生的值加入或更新](#value-generated-on-add-or-update)> 一節以取得詳細資料。

## <a name="fluent-api"></a>Fluent API

若要變更給定屬性的值產生模式，您可以使用 fluent 應用程式開發的應用程式開發介面。

### <a name="no-value-generation-fluent-api"></a>沒有值層代 (Fluent API)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/ValueGeneratedNever.cs#Sample)]

### <a name="value-generated-on-add-fluent-api"></a>在產生的值加入 (Fluent API)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/ValueGeneratedOnAdd.cs#Sample)]

> [!WARNING]  
> `ValueGeneratedOnAdd()` 只讓 EF 知道的值會產生已加入的實體，並不保證 EF 將會安裝的實際機制來產生值。  請參閱[上產生的值，則新增](#value-generated-on-add)> 一節以取得詳細資料。

### <a name="value-generated-on-add-or-update-fluent-api"></a>在產生的值，則新增或更新 (Fluent API)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/ValueGeneratedOnAddOrUpdate.cs#Sample)]

> [!WARNING]  
> 這只可讓 EF 知道的值會產生已加入或更新實體，並不保證 EF 將會安裝的實際機制來產生值。 請參閱[上產生的值加入或更新](#value-generated-on-add-or-update)> 一節以取得詳細資料。
