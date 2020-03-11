---
title: 實體屬性-EF Core
description: 如何使用 Entity Framework Core 設定和對應實體屬性
author: roji
ms.date: 12/10/2019
ms.assetid: e9dff604-3469-4a05-8f9e-18ac281d82a9
uid: core/modeling/entity-properties
ms.openlocfilehash: b67603fbffd1f1c8506bc21f8972c851eb8eef29
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417213"
---
# <a name="entity-properties"></a>實體屬性

模型中的每個實體類型都有一組屬性，EF Core 會從資料庫讀取和寫入。 如果您要使用關係資料庫，實體屬性會對應到資料表資料行。

## <a name="included-and-excluded-properties"></a>包含和排除的屬性

依照慣例，所有具有 getter 和 setter 的公用屬性都會包含在模型中。

可以排除特定的屬性，如下所示：

### <a name="data-annotations"></a>[資料註解](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreProperty.cs?name=IgnoreProperty&highlight=6)]

### <a name="fluent-api"></a>[流暢的 API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreProperty.cs?name=IgnoreProperty&highlight=3,4)]

***

## <a name="column-names"></a>資料行名稱

依照慣例，當使用關係資料庫時，實體屬性會對應至名稱與屬性相同的資料表資料行。

如果您想要使用不同的名稱來設定資料行，您可以如下所示：

### <a name="data-annotations"></a>[資料註解](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnName.cs?Name=ColumnName&highlight=3)]

### <a name="fluent-api"></a>[流暢的 API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnName.cs?Name=ColumnName&highlight=3-5)]

***

## <a name="column-data-types"></a>資料行資料類型

當使用關係資料庫時，資料庫提供者會根據屬性的 .NET 類型來選取資料類型。 它也會考慮其他中繼資料，例如設定的[最大長度](#maximum-length)、屬性是否為主要金鑰的一部分等等。

例如，SQL Server 會將 `DateTime` 屬性對應至 `datetime2(7)` 資料行，以及 `string` 屬性來 `nvarchar(max)` 資料行（或 `nvarchar(450)` 做為索引鍵使用的屬性）。

您也可以設定您的資料行，以指定資料行的精確資料類型。 例如，下列程式碼會將 `Url` 設定為非 unicode 字串，其最大長度為 `200`，而 `Rating` 為十進位，且有效位數為 `2`的 `5` 和小數位數：

### <a name="data-annotations"></a>[資料註解](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnDataType.cs?name=ColumnDataType&highlight=4,6)]

### <a name="fluent-api"></a>[流暢的 API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnDataType.cs?name=ColumnDataType&highlight=5-6)]

***

### <a name="maximum-length"></a>最大長度

設定最大長度會向資料庫提供者提供有關要為指定屬性選擇之適當資料行資料類型的提示。 最大長度僅適用于陣列資料類型，例如 `string` 和 `byte[]`。

> [!NOTE]
> Entity Framework 在傳遞資料給提供者之前，不會執行任何長度上限的驗證。 由提供者或資料存放區負責驗證是否適當。 例如，當以 SQL Server 為目標時，超過最大長度會導致例外狀況，因為基礎資料行的資料類型將不會允許儲存過量的資料。

在下列範例中，將最大長度設定為500將會在 SQL Server 上建立 `nvarchar(500)` 類型的資料行：

#### <a name="data-annotations"></a>[資料註解](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?name=MaxLength&highlight=4)]

#### <a name="fluent-api"></a>[流暢的 API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?name=MaxLength&highlight=3-5)]

***

## <a name="required-and-optional-properties"></a>必要和選擇性屬性

如果屬性有效包含 `null`，則會將其視為選擇性。 如果 `null` 不是要指派給屬性的有效值，則會被視為必要的屬性。 對應至關係資料庫架構時，會將所需的屬性建立為不可為 null 的資料行，並將選擇性屬性建立為可為 null 的資料行。

### <a name="conventions"></a>慣例

依照慣例，其 .NET 類型可以包含 null 的屬性會設定為選擇性，而其 .NET 類型不能包含 null 的屬性則會設定為必要。 例如，所有具有 .NET 實數值型別（`int`、`decimal`、`bool`等等）的屬性都會設定為必要，而且所有具有可為 null 的 .NET 實數值型別（`int?`、`decimal?`、`bool?`等等）的屬性都會設定為選擇性。

C#8引進了一個稱為[nullable 參考型別](/dotnet/csharp/tutorials/nullable-reference-types)的新功能，可讓您標注參考型別，指出其是否有效，以包含 null。 預設會停用這項功能，如果已啟用，它會以下列方式修改 EF Core 的行為：

* 如果停用可為 null 的參考型別（預設值），則所有具有 .NET 參考型別的屬性都會依照慣例設定為選擇性（例如 `string`）。
* 如果已啟用可為 null 的參考型別，則會根據其C# .net 類型的 null 屬性來設定內容： `string?` 將設定為選擇性，而 `string` 將會設定為必要。

下列範例顯示具有必要和選擇性屬性的實體類型，且可為 null 參考功能已停用（預設值）並已啟用：

#### <a name="without-nullable-reference-types-default"></a>[沒有可為 null 的參考型別（預設值）](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=4-8)]

#### <a name="with-nullable-reference-types"></a>[具有可為 null 的參考型別](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

建議使用可為 null 的參考型別，因為它會C#將程式碼中所表示的 null 屬性流向 EF Core 的模型和資料庫，並毋使用流暢的 API 或資料批註來表示兩次相同的概念。

> [!NOTE]
> 在現有專案上啟用可為 null 的參考型別時，請特別注意：先前設定為選擇性的參考類型屬性現在會設定為必要，除非它們已明確標注成可為 null。 管理關係資料庫架構時，這可能會產生可改變資料庫資料行之 null 屬性的遷移。

如需可為 null 的參考型別，以及如何搭配 EF Core 使用它們的詳細資訊，[請參閱這項功能的專屬檔頁面](xref:core/miscellaneous/nullable-reference-types)。

### <a name="explicit-configuration"></a>明確設定

依照慣例，您可以將屬性設定為必要，如下所示：

#### <a name="data-annotations"></a>[資料註解](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?name=Required&highlight=4)]

#### <a name="fluent-api"></a>[流暢的 API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?name=Required&highlight=3-5)]

***
