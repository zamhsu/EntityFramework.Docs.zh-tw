---
title: 實體屬性-EF Core
description: 如何使用 Entity Framework Core 來設定和對應實體屬性
author: roji
ms.date: 05/27/2020
uid: core/modeling/entity-properties
ms.openlocfilehash: 67ea6eb9cc62c01c16ef33d4d1d1597576ec8b3d
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003404"
---
# <a name="entity-properties"></a>實體屬性

模型中的每個實體類型都有一組屬性，EF Core 會從資料庫讀取和寫入。 如果您是使用關係資料庫，實體屬性會對應到資料表資料行。

## <a name="included-and-excluded-properties"></a>包含和排除的屬性

依照慣例，所有具有 getter 和 setter 的公用屬性都會包含在模型中。

可以排除特定的屬性，如下所示：

### <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreProperty.cs?name=IgnoreProperty&highlight=6)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreProperty.cs?name=IgnoreProperty&highlight=3,4)]

***

## <a name="column-names"></a>資料行名稱

依照慣例，當使用關係資料庫時，實體屬性會對應到名稱與屬性相同的資料表資料行。

如果您想要使用不同的名稱來設定資料行，可以這麼做，如下列程式碼片段所示：

### <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnName.cs?Name=ColumnName&highlight=3)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnName.cs?Name=ColumnName&highlight=3-5)]

**_

## <a name="column-data-types"></a>資料行資料類型

使用關係資料庫時，資料庫提供者會根據屬性的 .NET 類型選取資料類型。 它也會考慮其他中繼資料，例如設定的 [最大長度](#maximum-length)、屬性是否為主鍵的一部分等等。

例如，SQL Server 會將屬性對應至資料行，並將屬性對應至資料 `DateTime` `datetime2(7)` `string` `nvarchar(max)` 行 (或用於 `nvarchar(450)` 作為索引鍵) 的屬性。

您也可以設定資料行來指定資料行的精確資料類型。 例如，下列程式碼會將 `Url` 最大長度為的非 unicode 字串設定為，並將精確度設定為 `200` `Rating` decimal `5` `2` ：

### <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnDataType.cs?name=ColumnDataType&highlight=4,6)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnDataType.cs?name=ColumnDataType&highlight=5-6)]

_*_

### <a name="maximum-length"></a>長度上限

設定 [最大長度] 會為資料庫提供者提供適當的資料行資料類型的提示，以選擇指定的屬性。 最大長度只適用于陣列資料類型，例如 `string` 和 `byte[]` 。

> [!NOTE]
> Entity Framework 在將資料傳遞給提供者之前，不會執行任何最大長度驗證。 由提供者或資料存放區進行驗證（如果適合的話）。 例如，以 SQL Server 為目標時，超過最大長度將會導致例外狀況，因為基礎資料行的資料類型不允許儲存多餘的資料。

在下列範例中，設定最大長度500會導致 `nvarchar(500)` 在 SQL Server 上建立類型的資料行：

#### <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?name=MaxLength&highlight=4)]

#### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?name=MaxLength&highlight=3-5)]

_*_

### <a name="precision-and-scale"></a>精確度和小數位數

從 EFCore 5.0 開始，您可以使用流暢的 API 來設定精確度和小數位數。 它會告訴資料庫提供者所需的資料行需要多少儲存體。 它只適用于資料類型，而提供者允許精確度和小數位數的變化-通常是 `decimal` 和 `DateTime` 。

`decimal`若為屬性，precision 會定義表示資料行將包含的任何值所需的最大位數，且 scale 會定義所需的最大小數位數。 `DateTime`若為屬性，precision 會定義用來表示秒數的最大位數，且不使用小數位數。

> [!NOTE]
> Entity Framework 在將資料傳遞給提供者之前，不會執行任何精確度或規模的驗證。 由提供者或資料存放區視需要進行驗證。 例如，以 SQL Server 為目標時，資料類型的資料行 `datetime` 不允許設定有效位數，而一個資料行的有效 `datetime2` 位數可以是介於0和7（含）之間。

在下列範例中， `Score` 將屬性設定為具有有效位數14和小數位數2會導致 `decimal(14,2)` 在 SQL Server 上建立類型的資料行，並將屬性設定為具有有效位數3的資料 `LastUpdated` 行，將會導致類型的資料行 `datetime2(3)` ：

#### <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

目前無法透過資料批註設定精確度和小數位數。

#### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/PrecisionAndScale.cs?name=PrecisionAndScale&highlight=3-9)]

> [!NOTE]
> 您永遠不會定義小數位數，而不需要先定義有效位數，因此用來定義尺規的流暢 API 就是 `HasPrecision(precision, scale)` 。

_*_

## <a name="required-and-optional-properties"></a>必要和選用屬性

如果屬性可包含，則會將其視為選擇性 `null` 。 如果不是 `null` 要指派給屬性的有效值，則會將它視為必要屬性。 對應到關係資料庫架構時，需要的屬性會建立為不可為 null 的資料行，而選擇性的屬性會建立為可為 null 的資料行。

### <a name="conventions"></a>慣例

依照慣例，其 .NET 型別可以包含 null 的屬性會設定為選擇性，而 .NET 型別不能包含 null 的屬性則會設定為必要。 例如，所有具有 .net 實值型別 (`int` 、 `decimal` 、等 `bool` ) 的屬性都會設定為必要，且所有具有可為 null 之 .net 實值型別的屬性 (`int?` 、 `decimal?` ) 、等等） `bool?` 都會設定為選擇性。

C # 8 引進了新功能，稱為 [可為 null 的參考型別 (NRT) ](/dotnet/csharp/tutorials/nullable-reference-types)，可讓您標注參考型別，以指出其是否有效，以包含 null。 預設會停用這項功能，並以下列方式影響 EF Core 的行為：

_ 如果 (預設) 停用可為 null 的參考型別，則所有具有 .NET 參考型別的屬性都會依照慣例設定為選擇性 (例如 `string`) 。
* 如果已啟用可為 null 的參考型別，則會根據其 .NET 類型的 c # null 屬性來設定屬性： `string?` 將設定為選擇性，但 `string` 會設定為必要。

下列範例顯示具有必要和選擇性屬性的實體類型，並停用 (預設) 和啟用的可為 null 參考功能：

#### <a name="without-nrt-default"></a>[沒有 NRT (預設) ](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=4-8)]

#### <a name="with-nrt"></a>[使用 NRT](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

建議使用可為 null 的參考型別，因為它會將以 c # 程式碼表示的 null 屬性傳送給 EF Core 的模型和資料庫，並毋使用流暢的 API 或資料注釋，以表達相同的概念兩次。

> [!NOTE]
> 在現有的專案上啟用可為 null 的參考型別時，請務必注意：先前設定為選用的參考型別屬性現在會設定為必要，除非它們明確標注為可為 null。 管理關係資料庫架構時，這可能會導致產生遷移，進而改變資料庫資料行的 null 屬性。

如需可為 null 的參考型別，以及如何搭配 EF Core 使用這些類型的詳細資訊， [請參閱這項功能的專用檔頁面](xref:core/miscellaneous/nullable-reference-types)。

### <a name="explicit-configuration"></a>明確設定

慣例可以設定為選擇性的屬性，如下所示：

#### <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?name=Required&highlight=4)]

#### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?name=Required&highlight=3-5)]

***

## <a name="column-collations"></a>資料行定序

> [!NOTE]
> 這項功能是在 EF Core 5.0 中引進。

您可以在文字資料行上定義定序，以決定如何比較和排序定序。 例如，下列程式碼片段會將 SQL Server 資料行設定為不區分大小寫：

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?range=42-43)]

如果資料庫中的所有資料行都需要使用特定定序，請改為在資料庫層級定義定序。

您可以在 [定 [序檔] 頁面](xref:core/miscellaneous/collations-and-case-sensitivity)中找到有關定序 EF Core 支援的一般資訊。
