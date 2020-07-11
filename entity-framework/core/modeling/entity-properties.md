---
title: 實體屬性-EF Core
description: 如何使用 Entity Framework Core 設定和對應實體屬性
author: lajones
ms.date: 05/27/2020
ms.assetid: e9dff604-3469-4a05-8f9e-18ac281d82a9
uid: core/modeling/entity-properties
ms.openlocfilehash: d4e4c50d8c7febf5e42e9aa39352c0bb6a6bd409
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238212"
---
# <a name="entity-properties"></a>實體屬性

模型中的每個實體類型都有一組屬性，EF Core 會從資料庫讀取和寫入。 如果您要使用關係資料庫，實體屬性會對應到資料表資料行。

## <a name="included-and-excluded-properties"></a>包含和排除的屬性

依照慣例，所有具有 getter 和 setter 的公用屬性都會包含在模型中。

可以排除特定的屬性，如下所示：

### <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreProperty.cs?name=IgnoreProperty&highlight=6)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreProperty.cs?name=IgnoreProperty&highlight=3,4)]

***

## <a name="column-names"></a>資料行名稱

依照慣例，當使用關係資料庫時，實體屬性會對應至名稱與屬性相同的資料表資料行。

如果您想要使用不同的名稱來設定資料行，您可以依照下列程式碼片段來進行：

### <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnName.cs?Name=ColumnName&highlight=3)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnName.cs?Name=ColumnName&highlight=3-5)]

***

## <a name="column-data-types"></a>資料行資料類型

當使用關係資料庫時，資料庫提供者會根據屬性的 .NET 類型來選取資料類型。 它也會考慮其他中繼資料，例如設定的[最大長度](#maximum-length)、屬性是否為主要金鑰的一部分等等。

例如，SQL Server 會將 `DateTime` 屬性對應至資料行，並將屬性對應至 `datetime2(7)` `string` (的資料行，或用於做為索引 `nvarchar(max)` `nvarchar(450)` 鍵) 的屬性。

您也可以設定您的資料行，以指定資料行的精確資料類型。 例如，下列程式碼會將設定 `Url` 為非 unicode 字串，其最大長度為 `200` ，並將設定 `Rating` 為小數，且有效位數為 `5` `2` ：

### <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnDataType.cs?name=ColumnDataType&highlight=4,6)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnDataType.cs?name=ColumnDataType&highlight=5-6)]

***

### <a name="maximum-length"></a>長度上限

設定最大長度會向資料庫提供者提供有關要為指定屬性選擇之適當資料行資料類型的提示。 最大長度僅適用于陣列資料類型，例如 `string` 和 `byte[]` 。

> [!NOTE]
> Entity Framework 在傳遞資料給提供者之前，不會執行任何長度上限的驗證。 由提供者或資料存放區負責驗證是否適當。 例如，當以 SQL Server 為目標時，超過最大長度會導致例外狀況，因為基礎資料行的資料類型將不會允許儲存過量的資料。

在下列範例中，設定最大長度500將會 `nvarchar(500)` 在 SQL Server 上建立類型的資料行：

#### <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?name=MaxLength&highlight=4)]

#### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?name=MaxLength&highlight=3-5)]

***

### <a name="precision-and-scale"></a>精確度和小數位數

從 EFCore 5.0 開始，您可以使用 Fluent API 來設定精確度和小數位數。 它會告訴資料庫提供者，指定的資料行需要多少儲存空間。 它只適用于資料類型，提供者允許有效位數和小數位數（通常是 `decimal` 和） `DateTime` 。

對於 `decimal` 屬性，precision 會定義資料行所包含的任何值所需的最大位數，而 scale 則會定義所需的最大小數位數。 對於 `DateTime` 屬性而言，精確度會定義表示秒數所需的最大位數，而不會使用 scale。

> [!NOTE]
> Entity Framework 在傳遞資料給提供者之前，不會執行任何有效位數或小數位數的驗證。 視需要驗證提供者或資料存放區。 例如，當以 SQL Server 為目標時，資料類型的資料行不 `datetime` 允許設定有效位數，而 `datetime2` 可以有介於0到7（含）之間的有效位數。

在下列範例中， `Score` 將屬性設定為具有有效位數14和小數位數2，將會 `decimal(14,2)` 在 SQL Server 上建立類型的資料行，並將屬性設定為具有有效位數3的資料 `LastUpdated` 行 `datetime2(3)` ：

#### <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

目前無法使用資料批註來設定。

#### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/PrecisionAndScale.cs?name=PrecisionAndScale&highlight=3-9)]

> [!NOTE]
> 永遠不會定義小數位數，而不會先定義精確度，因此，用於定義小數位數的流暢 API 是 `HasPrecision(precision, scale)` 。

***

## <a name="required-and-optional-properties"></a>必要和選擇性屬性

如果屬性可包含，則會將其視為選擇性 `null` 。 如果不是要指派給屬性的有效值，則會將 `null` 它視為必要的屬性。 對應至關係資料庫架構時，會將所需的屬性建立為不可為 null 的資料行，並將選擇性屬性建立為可為 null 的資料行。

### <a name="conventions"></a>慣例

依照慣例，其 .NET 類型可以包含 null 的屬性會設定為選擇性，而其 .NET 類型不能包含 null 的屬性則會設定為必要。 例如，所有具有 .net 實數值型別 (`int` 、) 、等的屬性 `decimal` `bool` 都會設定為必要，而所有具有可為 null 的 .net 實數值型別 (`int?` 、) 、等等的屬性 `decimal?` `bool?` 則會設定為選擇性。

C # 8 引進了一個稱為[nullable 參考型別](/dotnet/csharp/tutorials/nullable-reference-types)的新功能，可讓您標注參考型別，指出其是否有效，以包含 null。 預設會停用這項功能，如果已啟用，它會以下列方式修改 EF Core 的行為：

* 如果 (預設) 停用可為 null 的參考型別，則具有 .NET 參考型別的所有屬性都會依慣例 (設定為選擇性，例如 `string`) 。
* 如果已啟用可為 null 的參考型別，則會根據其 .NET 類型的 c # null 屬性來設定屬性： `string?` 將會設定為選擇性，但 `string` 會設定為必要。

下列範例顯示具有必要和選擇性屬性的實體類型，並停用可為 null 的參考功能 (預設) 並啟用：

#### <a name="without-nullable-reference-types-default"></a>[沒有可為 null 的參考型別 (預設值) ](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=4-8)]

#### <a name="with-nullable-reference-types"></a>[具有可為 null 的參考型別](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

建議使用可為 null 的參考型別，因為它會將 c # 程式碼中所表示的 null 屬性流向 EF Core 的模型和資料庫，並毋使用流暢的 API 或資料批註來表示兩次相同的概念。

> [!NOTE]
> 在現有專案上啟用可為 null 的參考型別時，請特別注意：先前設定為選擇性的參考類型屬性現在會設定為必要，除非它們已明確標注成可為 null。 管理關係資料庫架構時，這可能會產生可改變資料庫資料行之 null 屬性的遷移。

如需可為 null 的參考型別，以及如何搭配 EF Core 使用它們的詳細資訊，[請參閱這項功能的專屬檔頁面](xref:core/miscellaneous/nullable-reference-types)。

### <a name="explicit-configuration"></a>明確設定

依照慣例，您可以將屬性設定為必要，如下所示：

#### <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?name=Required&highlight=4)]

#### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?name=Required&highlight=3-5)]

***

## <a name="column-collations"></a>資料行定序

> [!NOTE]
> 這項功能會在 EF Core 5.0 中引進。

定序可以在文字資料行上定義，以決定其比較和排序方式。 例如，下列程式碼片段會將 SQL Server 的資料行設定為不區分大小寫：

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?range=42-43)]

如果資料庫中的所有資料行都必須使用特定定序，請改為在資料庫層級上定義定序。

如需有關定序的 EF Core 支援的一般資訊，請參閱定[序檔頁面](xref:core/miscellaneous/collations-and-case-sensitivity)。
