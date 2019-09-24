---
title: 必要和選擇性屬性-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ddaa0a54-9f43-4c34-aae3-f95c96c69842
uid: core/modeling/required-optional
ms.openlocfilehash: fd9e96e6f79965e63b07c21217edd004fd5c4d54
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197845"
---
# <a name="required-and-optional-properties"></a>必要和選擇性屬性

如果屬性可包含`null`，則會將其視為選擇性。 如果`null`不是要指派給屬性的有效值，則會將它視為必要的屬性。

對應至關係資料庫架構時，會將所需的屬性建立為不可為 null 的資料行，並將選擇性屬性建立為可為 null 的資料行。

## <a name="conventions"></a>慣例

依照慣例，其 .NET 類型可以包含 null 的屬性會設定為選擇性，而其 .NET 類型不能包含 null 的屬性則會設定為必要。 例如，所有具有 .net 實數值型別（`int`、 `decimal`、 `bool`等）的屬性都會設定為必要，而且所有具有可為 null 的 .net 實值`int?`類型`decimal?`（ `bool?`、、等）的屬性都是設定為選擇性。

C#8引進了一個稱為[nullable 參考型別](/dotnet/csharp/tutorials/nullable-reference-types)的新功能，可讓您標注參考型別，指出其是否有效，以包含 null。 預設會停用這項功能，如果已啟用，它會以下列方式修改 EF Core 的行為：

* 如果停用可為 null 的參考型別（預設值），則所有具有 .NET 參考型別的屬性都會依照慣例設定`string`為選擇性（例如）。
* 如果已啟用可為 null 的參考型別，則會根據其C# .net 類型的 null 屬性來`string?`設定屬性：將會設定為`string`選擇性，而會設定為必要。

下列範例顯示具有必要和選擇性屬性的實體類型，且可為 null 參考功能已停用（預設值）並已啟用：

# <a name="without-nullable-reference-types-defaulttabwithout-nrt"></a>[沒有可為 null 的參考型別（預設值）](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=4-8)]

# <a name="with-nullable-reference-typestabwith-nrt"></a>[具有可為 null 的參考型別](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

建議使用可為 null 的參考型別，因為它會C#將程式碼中所表示的 null 屬性流向 EF Core 的模型和資料庫，並毋使用流暢的 API 或資料批註來表示兩次相同的概念。

> [!NOTE]
> 在現有專案上啟用可為 null 的參考型別時，請特別注意：先前設定為選擇性的參考類型屬性現在會設定為必要，除非它們已明確標注成可為 null。 管理關係資料庫架構時，這可能會產生可改變資料庫資料行之 null 屬性的遷移。

如需可為 null 的參考型別，以及如何搭配 EF Core 使用它們的詳細資訊，[請參閱這項功能的專屬檔頁面](xref:core/miscellaneous/nullable-reference-types)。

## <a name="configuration"></a>設定

依照慣例，您可以將屬性設定為必要，如下所示：

# <a name="data-annotationstabdata-annotations"></a>[資料註解](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?highlight=14)]

# <a name="fluent-apitabfluent-api"></a>[流暢的 API](#tab/fluent-api) 

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?highlight=11-13)]

***