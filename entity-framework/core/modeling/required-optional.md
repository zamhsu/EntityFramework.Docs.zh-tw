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
# <a name="required-and-optional-properties"></a><span data-ttu-id="bef93-102">必要和選擇性屬性</span><span class="sxs-lookup"><span data-stu-id="bef93-102">Required and Optional Properties</span></span>

<span data-ttu-id="bef93-103">如果屬性可包含`null`，則會將其視為選擇性。</span><span class="sxs-lookup"><span data-stu-id="bef93-103">A property is considered optional if it is valid for it to contain `null`.</span></span> <span data-ttu-id="bef93-104">如果`null`不是要指派給屬性的有效值，則會將它視為必要的屬性。</span><span class="sxs-lookup"><span data-stu-id="bef93-104">If `null` is not a valid value to be assigned to a property then it is considered to be a required property.</span></span>

<span data-ttu-id="bef93-105">對應至關係資料庫架構時，會將所需的屬性建立為不可為 null 的資料行，並將選擇性屬性建立為可為 null 的資料行。</span><span class="sxs-lookup"><span data-stu-id="bef93-105">When mapping to a relational database schema, required properties are created as non-nullable columns, and optional properties are created as nullable columns.</span></span>

## <a name="conventions"></a><span data-ttu-id="bef93-106">慣例</span><span class="sxs-lookup"><span data-stu-id="bef93-106">Conventions</span></span>

<span data-ttu-id="bef93-107">依照慣例，其 .NET 類型可以包含 null 的屬性會設定為選擇性，而其 .NET 類型不能包含 null 的屬性則會設定為必要。</span><span class="sxs-lookup"><span data-stu-id="bef93-107">By convention, a property whose .NET type can contain null will be configured as optional, whereas properties whose .NET type cannot contain null will be configured as required.</span></span> <span data-ttu-id="bef93-108">例如，所有具有 .net 實數值型別（`int`、 `decimal`、 `bool`等）的屬性都會設定為必要，而且所有具有可為 null 的 .net 實值`int?`類型`decimal?`（ `bool?`、、等）的屬性都是設定為選擇性。</span><span class="sxs-lookup"><span data-stu-id="bef93-108">For example, all properties with .NET value types (`int`, `decimal`, `bool`, etc.) are configured as required, and all properties with nullable .NET value types (`int?`, `decimal?`, `bool?`, etc.) are configured as optional.</span></span>

<span data-ttu-id="bef93-109">C#8引進了一個稱為[nullable 參考型別](/dotnet/csharp/tutorials/nullable-reference-types)的新功能，可讓您標注參考型別，指出其是否有效，以包含 null。</span><span class="sxs-lookup"><span data-stu-id="bef93-109">C# 8 introduced a new feature called [nullable reference types](/dotnet/csharp/tutorials/nullable-reference-types), which allows reference types to be annotated, indicating whether it is valid for them to contain null or not.</span></span> <span data-ttu-id="bef93-110">預設會停用這項功能，如果已啟用，它會以下列方式修改 EF Core 的行為：</span><span class="sxs-lookup"><span data-stu-id="bef93-110">This feature is disabled by default, and if enabled, it modifies EF Core's behavior in the following way:</span></span>

* <span data-ttu-id="bef93-111">如果停用可為 null 的參考型別（預設值），則所有具有 .NET 參考型別的屬性都會依照慣例設定`string`為選擇性（例如）。</span><span class="sxs-lookup"><span data-stu-id="bef93-111">If nullable reference types are disabled (the default), all properties with .NET reference types are configured as optional by convention (e.g. `string`).</span></span>
* <span data-ttu-id="bef93-112">如果已啟用可為 null 的參考型別，則會根據其C# .net 類型的 null 屬性來`string?`設定屬性：將會設定為`string`選擇性，而會設定為必要。</span><span class="sxs-lookup"><span data-stu-id="bef93-112">If nullable reference types are enabled, properties will be configured based on the C# nullability of their .NET type: `string?` will be configured as optional, whereas `string` will be configured as required.</span></span>

<span data-ttu-id="bef93-113">下列範例顯示具有必要和選擇性屬性的實體類型，且可為 null 參考功能已停用（預設值）並已啟用：</span><span class="sxs-lookup"><span data-stu-id="bef93-113">The following example shows an entity type with required and optional properties, with the nullable reference feature disabled (the default) and enabled:</span></span>

# <a name="without-nullable-reference-types-defaulttabwithout-nrt"></a>[<span data-ttu-id="bef93-114">沒有可為 null 的參考型別（預設值）</span><span class="sxs-lookup"><span data-stu-id="bef93-114">Without nullable reference types (default)</span></span>](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=4-8)]

# <a name="with-nullable-reference-typestabwith-nrt"></a>[<span data-ttu-id="bef93-115">具有可為 null 的參考型別</span><span class="sxs-lookup"><span data-stu-id="bef93-115">With nullable reference types</span></span>](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

<span data-ttu-id="bef93-116">建議使用可為 null 的參考型別，因為它會C#將程式碼中所表示的 null 屬性流向 EF Core 的模型和資料庫，並毋使用流暢的 API 或資料批註來表示兩次相同的概念。</span><span class="sxs-lookup"><span data-stu-id="bef93-116">Using nullable reference types is recommended since it flows the nullability expressed in C# code to EF Core's model and to the database, and obviates the use of the Fluent API or Data Annotations to express the same concept twice.</span></span>

> [!NOTE]
> <span data-ttu-id="bef93-117">在現有專案上啟用可為 null 的參考型別時，請特別注意：先前設定為選擇性的參考類型屬性現在會設定為必要，除非它們已明確標注成可為 null。</span><span class="sxs-lookup"><span data-stu-id="bef93-117">Exercise caution when enabling nullable reference types on an existing project: reference type properties which were previously configured as optional will now be configured as required, unless they are explicitly annotated to be nullable.</span></span> <span data-ttu-id="bef93-118">管理關係資料庫架構時，這可能會產生可改變資料庫資料行之 null 屬性的遷移。</span><span class="sxs-lookup"><span data-stu-id="bef93-118">When managing a relational database schema, this may cause migrations to be generated which alter the database column's nullability.</span></span>

<span data-ttu-id="bef93-119">如需可為 null 的參考型別，以及如何搭配 EF Core 使用它們的詳細資訊，[請參閱這項功能的專屬檔頁面](xref:core/miscellaneous/nullable-reference-types)。</span><span class="sxs-lookup"><span data-stu-id="bef93-119">For more information on nullable reference types and how to use them with EF Core, [see the dedicated documentation page for this feature](xref:core/miscellaneous/nullable-reference-types).</span></span>

## <a name="configuration"></a><span data-ttu-id="bef93-120">設定</span><span class="sxs-lookup"><span data-stu-id="bef93-120">Configuration</span></span>

<span data-ttu-id="bef93-121">依照慣例，您可以將屬性設定為必要，如下所示：</span><span class="sxs-lookup"><span data-stu-id="bef93-121">A property that would be optional by convention can be configured to be required as follows:</span></span>

# <a name="data-annotationstabdata-annotations"></a>[<span data-ttu-id="bef93-122">資料註解</span><span class="sxs-lookup"><span data-stu-id="bef93-122">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?highlight=14)]

# <a name="fluent-apitabfluent-api"></a>[<span data-ttu-id="bef93-123">流暢的 API</span><span class="sxs-lookup"><span data-stu-id="bef93-123">Fluent API</span></span>](#tab/fluent-api) 

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?highlight=11-13)]

***