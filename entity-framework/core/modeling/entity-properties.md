---
title: 實體屬性-EF Core
description: 如何使用 Entity Framework Core 設定和對應實體屬性
author: roji
ms.date: 12/10/2019
ms.assetid: e9dff604-3469-4a05-8f9e-18ac281d82a9
uid: core/modeling/entity-properties
ms.openlocfilehash: e4a1867a90df1fb277e7dd44b93d6c2d47895030
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664152"
---
# <a name="entity-properties"></a><span data-ttu-id="beeb0-103">實體屬性</span><span class="sxs-lookup"><span data-stu-id="beeb0-103">Entity Properties</span></span>

<span data-ttu-id="beeb0-104">模型中的每個實體類型都有一組屬性，EF Core 會從資料庫讀取和寫入。</span><span class="sxs-lookup"><span data-stu-id="beeb0-104">Each entity type in your model has a set of properties, which EF Core will read and write from the database.</span></span> <span data-ttu-id="beeb0-105">如果您要使用關係資料庫，實體屬性會對應到資料表資料行。</span><span class="sxs-lookup"><span data-stu-id="beeb0-105">If you're using a relational database, entity properties map to table columns.</span></span>

## <a name="included-and-excluded-properties"></a><span data-ttu-id="beeb0-106">包含和排除的屬性</span><span class="sxs-lookup"><span data-stu-id="beeb0-106">Included and excluded properties</span></span>

<span data-ttu-id="beeb0-107">依照慣例，所有具有 getter 和 setter 的公用屬性都會包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="beeb0-107">By convention, all public properties with a getter and a setter will be included in the model.</span></span>

<span data-ttu-id="beeb0-108">可以排除特定的屬性，如下所示：</span><span class="sxs-lookup"><span data-stu-id="beeb0-108">Specific properties can be excluded as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="beeb0-109">資料批註</span><span class="sxs-lookup"><span data-stu-id="beeb0-109">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreProperty.cs?name=IgnoreProperty&highlight=6)]

### <a name="fluent-api"></a>[<span data-ttu-id="beeb0-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="beeb0-110">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreProperty.cs?name=IgnoreProperty&highlight=3,4)]

***

## <a name="column-names"></a><span data-ttu-id="beeb0-111">資料行名稱</span><span class="sxs-lookup"><span data-stu-id="beeb0-111">Column names</span></span>

<span data-ttu-id="beeb0-112">依照慣例，當使用關係資料庫時，實體屬性會對應至名稱與屬性相同的資料表資料行。</span><span class="sxs-lookup"><span data-stu-id="beeb0-112">By convention, when using a relational database, entity properties are mapped to table columns having the same name as the property.</span></span>

<span data-ttu-id="beeb0-113">如果您想要使用不同的名稱來設定資料行，您可以如下所示：</span><span class="sxs-lookup"><span data-stu-id="beeb0-113">If you prefer to configure your columns with different names, you can do so as following:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="beeb0-114">資料批註</span><span class="sxs-lookup"><span data-stu-id="beeb0-114">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnName.cs?Name=ColumnName&highlight=3)]

### <a name="fluent-api"></a>[<span data-ttu-id="beeb0-115">Fluent API</span><span class="sxs-lookup"><span data-stu-id="beeb0-115">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnName.cs?Name=ColumnName&highlight=3-5)]

***

## <a name="column-data-types"></a><span data-ttu-id="beeb0-116">資料行資料類型</span><span class="sxs-lookup"><span data-stu-id="beeb0-116">Column data types</span></span>

<span data-ttu-id="beeb0-117">當使用關係資料庫時，資料庫提供者會根據屬性的 .NET 類型來選取資料類型。</span><span class="sxs-lookup"><span data-stu-id="beeb0-117">When using a relational database, the database provider selects a data type based on the .NET type of the property.</span></span> <span data-ttu-id="beeb0-118">它也會考慮其他中繼資料，例如設定的[最大長度](#maximum-length)、屬性是否為主要金鑰的一部分等等。</span><span class="sxs-lookup"><span data-stu-id="beeb0-118">It also takes into account other metadata, such as the configured [maximum length](#maximum-length), whether the property is part of a primary key, etc.</span></span>

<span data-ttu-id="beeb0-119">例如，SQL Server 會將 `DateTime` 屬性對應至資料 `datetime2(7)` 行，並將屬性對應至資料 `string` `nvarchar(max)` 行（或用於做為索引 `nvarchar(450)` 鍵的屬性）。</span><span class="sxs-lookup"><span data-stu-id="beeb0-119">For example, SQL Server maps `DateTime` properties to `datetime2(7)` columns, and `string` properties to `nvarchar(max)` columns (or to `nvarchar(450)` for properties that are used as a key).</span></span>

<span data-ttu-id="beeb0-120">您也可以設定您的資料行，以指定資料行的精確資料類型。</span><span class="sxs-lookup"><span data-stu-id="beeb0-120">You can also configure your columns to specify an exact data type for a column.</span></span> <span data-ttu-id="beeb0-121">例如，下列程式碼會將設定 `Url` 為非 unicode 字串，其最大長度為 `200` ，並將設定 `Rating` 為小數， `5` 且有效位數為 `2` ：</span><span class="sxs-lookup"><span data-stu-id="beeb0-121">For example the following code configures `Url` as a non-unicode string with maximum length of `200` and `Rating` as decimal with precision of `5` and scale of `2`:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="beeb0-122">資料批註</span><span class="sxs-lookup"><span data-stu-id="beeb0-122">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnDataType.cs?name=ColumnDataType&highlight=4,6)]

### <a name="fluent-api"></a>[<span data-ttu-id="beeb0-123">Fluent API</span><span class="sxs-lookup"><span data-stu-id="beeb0-123">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnDataType.cs?name=ColumnDataType&highlight=5-6)]

***

### <a name="maximum-length"></a><span data-ttu-id="beeb0-124">長度上限</span><span class="sxs-lookup"><span data-stu-id="beeb0-124">Maximum length</span></span>

<span data-ttu-id="beeb0-125">設定最大長度會向資料庫提供者提供有關要為指定屬性選擇之適當資料行資料類型的提示。</span><span class="sxs-lookup"><span data-stu-id="beeb0-125">Configuring a maximum length provides a hint to the database provider about the appropriate column data type to choose for a given property.</span></span> <span data-ttu-id="beeb0-126">最大長度僅適用于陣列資料類型，例如 `string` 和 `byte[]` 。</span><span class="sxs-lookup"><span data-stu-id="beeb0-126">Maximum length only applies to array data types, such as `string` and `byte[]`.</span></span>

> [!NOTE]
> <span data-ttu-id="beeb0-127">Entity Framework 在傳遞資料給提供者之前，不會執行任何長度上限的驗證。</span><span class="sxs-lookup"><span data-stu-id="beeb0-127">Entity Framework does not do any validation of maximum length before passing data to the provider.</span></span> <span data-ttu-id="beeb0-128">由提供者或資料存放區負責驗證是否適當。</span><span class="sxs-lookup"><span data-stu-id="beeb0-128">It is up to the provider or data store to validate if appropriate.</span></span> <span data-ttu-id="beeb0-129">例如，當以 SQL Server 為目標時，超過最大長度會導致例外狀況，因為基礎資料行的資料類型將不會允許儲存過量的資料。</span><span class="sxs-lookup"><span data-stu-id="beeb0-129">For example, when targeting SQL Server, exceeding the maximum length will result in an exception as the data type of the underlying column will not allow excess data to be stored.</span></span>

<span data-ttu-id="beeb0-130">在下列範例中，設定最大長度500將會 `nvarchar(500)` 在 SQL Server 上建立類型的資料行：</span><span class="sxs-lookup"><span data-stu-id="beeb0-130">In the following example, configuring a maximum length of 500 will cause a column of type `nvarchar(500)` to be created on SQL Server:</span></span>

#### <a name="data-annotations"></a>[<span data-ttu-id="beeb0-131">資料批註</span><span class="sxs-lookup"><span data-stu-id="beeb0-131">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?name=MaxLength&highlight=4)]

#### <a name="fluent-api"></a>[<span data-ttu-id="beeb0-132">Fluent API</span><span class="sxs-lookup"><span data-stu-id="beeb0-132">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?name=MaxLength&highlight=3-5)]

***

## <a name="required-and-optional-properties"></a><span data-ttu-id="beeb0-133">必要和選擇性屬性</span><span class="sxs-lookup"><span data-stu-id="beeb0-133">Required and optional properties</span></span>

<span data-ttu-id="beeb0-134">如果屬性可包含，則會將其視為選擇性 `null` 。</span><span class="sxs-lookup"><span data-stu-id="beeb0-134">A property is considered optional if it is valid for it to contain `null`.</span></span> <span data-ttu-id="beeb0-135">如果不是要指派給屬性的有效值，則會將 `null` 它視為必要的屬性。</span><span class="sxs-lookup"><span data-stu-id="beeb0-135">If `null` is not a valid value to be assigned to a property then it is considered to be a required property.</span></span> <span data-ttu-id="beeb0-136">對應至關係資料庫架構時，會將所需的屬性建立為不可為 null 的資料行，並將選擇性屬性建立為可為 null 的資料行。</span><span class="sxs-lookup"><span data-stu-id="beeb0-136">When mapping to a relational database schema, required properties are created as non-nullable columns, and optional properties are created as nullable columns.</span></span>

### <a name="conventions"></a><span data-ttu-id="beeb0-137">慣例</span><span class="sxs-lookup"><span data-stu-id="beeb0-137">Conventions</span></span>

<span data-ttu-id="beeb0-138">依照慣例，其 .NET 類型可以包含 null 的屬性會設定為選擇性，而其 .NET 類型不能包含 null 的屬性則會設定為必要。</span><span class="sxs-lookup"><span data-stu-id="beeb0-138">By convention, a property whose .NET type can contain null will be configured as optional, whereas properties whose .NET type cannot contain null will be configured as required.</span></span> <span data-ttu-id="beeb0-139">例如，所有具有 .net 實數值型別（ `int` 、、等）的屬性 `decimal` `bool` 都會設定為必要，而且所有具有可為 null 的 .net 實數值型別（ `int?` 、、等）的屬性 `decimal?` `bool?` 都會設定為選擇性。</span><span class="sxs-lookup"><span data-stu-id="beeb0-139">For example, all properties with .NET value types (`int`, `decimal`, `bool`, etc.) are configured as required, and all properties with nullable .NET value types (`int?`, `decimal?`, `bool?`, etc.) are configured as optional.</span></span>

<span data-ttu-id="beeb0-140">C # 8 引進了一個稱為[nullable 參考型別](/dotnet/csharp/tutorials/nullable-reference-types)的新功能，可讓您標注參考型別，指出其是否有效，以包含 null。</span><span class="sxs-lookup"><span data-stu-id="beeb0-140">C# 8 introduced a new feature called [nullable reference types](/dotnet/csharp/tutorials/nullable-reference-types), which allows reference types to be annotated, indicating whether it is valid for them to contain null or not.</span></span> <span data-ttu-id="beeb0-141">預設會停用這項功能，如果已啟用，它會以下列方式修改 EF Core 的行為：</span><span class="sxs-lookup"><span data-stu-id="beeb0-141">This feature is disabled by default, and if enabled, it modifies EF Core's behavior in the following way:</span></span>

* <span data-ttu-id="beeb0-142">如果停用可為 null 的參考型別（預設值），則所有具有 .NET 參考型別的屬性都會依照慣例設定為選擇性（例如 `string` ）。</span><span class="sxs-lookup"><span data-stu-id="beeb0-142">If nullable reference types are disabled (the default), all properties with .NET reference types are configured as optional by convention (e.g. `string`).</span></span>
* <span data-ttu-id="beeb0-143">如果已啟用可為 null 的參考型別，則會根據其 .NET 類型的 c # null 屬性來設定屬性： `string?` 將會設定為選擇性，而 `string` 會設定為必要。</span><span class="sxs-lookup"><span data-stu-id="beeb0-143">If nullable reference types are enabled, properties will be configured based on the C# nullability of their .NET type: `string?` will be configured as optional, whereas `string` will be configured as required.</span></span>

<span data-ttu-id="beeb0-144">下列範例顯示具有必要和選擇性屬性的實體類型，且可為 null 參考功能已停用（預設值）並已啟用：</span><span class="sxs-lookup"><span data-stu-id="beeb0-144">The following example shows an entity type with required and optional properties, with the nullable reference feature disabled (the default) and enabled:</span></span>

#### <a name="without-nullable-reference-types-default"></a>[<span data-ttu-id="beeb0-145">沒有可為 null 的參考型別（預設值）</span><span class="sxs-lookup"><span data-stu-id="beeb0-145">Without nullable reference types (default)</span></span>](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=4-8)]

#### <a name="with-nullable-reference-types"></a>[<span data-ttu-id="beeb0-146">具有可為 null 的參考型別</span><span class="sxs-lookup"><span data-stu-id="beeb0-146">With nullable reference types</span></span>](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

<span data-ttu-id="beeb0-147">建議使用可為 null 的參考型別，因為它會將 c # 程式碼中所表示的 null 屬性流向 EF Core 的模型和資料庫，並毋使用流暢的 API 或資料批註來表示兩次相同的概念。</span><span class="sxs-lookup"><span data-stu-id="beeb0-147">Using nullable reference types is recommended since it flows the nullability expressed in C# code to EF Core's model and to the database, and obviates the use of the Fluent API or Data Annotations to express the same concept twice.</span></span>

> [!NOTE]
> <span data-ttu-id="beeb0-148">在現有專案上啟用可為 null 的參考型別時，請特別注意：先前設定為選擇性的參考類型屬性現在會設定為必要，除非它們已明確標注成可為 null。</span><span class="sxs-lookup"><span data-stu-id="beeb0-148">Exercise caution when enabling nullable reference types on an existing project: reference type properties which were previously configured as optional will now be configured as required, unless they are explicitly annotated to be nullable.</span></span> <span data-ttu-id="beeb0-149">管理關係資料庫架構時，這可能會產生可改變資料庫資料行之 null 屬性的遷移。</span><span class="sxs-lookup"><span data-stu-id="beeb0-149">When managing a relational database schema, this may cause migrations to be generated which alter the database column's nullability.</span></span>

<span data-ttu-id="beeb0-150">如需可為 null 的參考型別，以及如何搭配 EF Core 使用它們的詳細資訊，[請參閱這項功能的專屬檔頁面](xref:core/miscellaneous/nullable-reference-types)。</span><span class="sxs-lookup"><span data-stu-id="beeb0-150">For more information on nullable reference types and how to use them with EF Core, [see the dedicated documentation page for this feature](xref:core/miscellaneous/nullable-reference-types).</span></span>

### <a name="explicit-configuration"></a><span data-ttu-id="beeb0-151">明確設定</span><span class="sxs-lookup"><span data-stu-id="beeb0-151">Explicit configuration</span></span>

<span data-ttu-id="beeb0-152">依照慣例，您可以將屬性設定為必要，如下所示：</span><span class="sxs-lookup"><span data-stu-id="beeb0-152">A property that would be optional by convention can be configured to be required as follows:</span></span>

#### <a name="data-annotations"></a>[<span data-ttu-id="beeb0-153">資料批註</span><span class="sxs-lookup"><span data-stu-id="beeb0-153">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?name=Required&highlight=4)]

#### <a name="fluent-api"></a>[<span data-ttu-id="beeb0-154">Fluent API</span><span class="sxs-lookup"><span data-stu-id="beeb0-154">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?name=Required&highlight=3-5)]

***

## <a name="column-collations"></a><span data-ttu-id="beeb0-155">資料行定序</span><span class="sxs-lookup"><span data-stu-id="beeb0-155">Column collations</span></span>

> [!NOTE]
> <span data-ttu-id="beeb0-156">這項功能會在 EF Core 5.0 中引進。</span><span class="sxs-lookup"><span data-stu-id="beeb0-156">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="beeb0-157">定序可以在文字資料行上定義，以決定其比較和排序方式。</span><span class="sxs-lookup"><span data-stu-id="beeb0-157">A collation can be defined on text columns, determining how they are compared and ordered.</span></span> <span data-ttu-id="beeb0-158">例如，下列程式會將 SQL Server 資料行設定為不區分大小寫：</span><span class="sxs-lookup"><span data-stu-id="beeb0-158">For example, the following configures a SQL Server column to be case-insensitive:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?range=42-43)]

<span data-ttu-id="beeb0-159">如果資料庫中的所有資料行都必須使用特定定序，請改為在資料庫層級上定義定序。</span><span class="sxs-lookup"><span data-stu-id="beeb0-159">If all columns in a database need to use a certain collation, define the collation at the database level instead.</span></span>

<span data-ttu-id="beeb0-160">如需有關定序的 EF Core 支援的一般資訊，請參閱定[序檔頁面](xref:core/miscellaneous/collations-and-case-sensitivity.md)。</span><span class="sxs-lookup"><span data-stu-id="beeb0-160">General information about EF Core support for collations can be found in the [collation documentation page](xref:core/miscellaneous/collations-and-case-sensitivity.md).</span></span>