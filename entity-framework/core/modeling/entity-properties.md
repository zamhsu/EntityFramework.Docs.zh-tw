---
title: 實體屬性-EF Core
description: 如何使用 Entity Framework Core 來設定和對應實體屬性
author: roji
ms.date: 05/27/2020
uid: core/modeling/entity-properties
ms.openlocfilehash: 55c6f31543d4ce3257cf203eaf9fd2191301ea7e
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429592"
---
# <a name="entity-properties"></a><span data-ttu-id="a1ad8-103">實體屬性</span><span class="sxs-lookup"><span data-stu-id="a1ad8-103">Entity Properties</span></span>

<span data-ttu-id="a1ad8-104">模型中的每個實體類型都有一組屬性，EF Core 會從資料庫讀取和寫入。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-104">Each entity type in your model has a set of properties, which EF Core will read and write from the database.</span></span> <span data-ttu-id="a1ad8-105">如果您是使用關係資料庫，實體屬性會對應到資料表資料行。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-105">If you're using a relational database, entity properties map to table columns.</span></span>

## <a name="included-and-excluded-properties"></a><span data-ttu-id="a1ad8-106">包含和排除的屬性</span><span class="sxs-lookup"><span data-stu-id="a1ad8-106">Included and excluded properties</span></span>

<span data-ttu-id="a1ad8-107">依照慣例，所有具有 getter 和 setter 的公用屬性都會包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-107">By convention, all public properties with a getter and a setter will be included in the model.</span></span>

<span data-ttu-id="a1ad8-108">可以排除特定的屬性，如下所示：</span><span class="sxs-lookup"><span data-stu-id="a1ad8-108">Specific properties can be excluded as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="a1ad8-109">資料批註</span><span class="sxs-lookup"><span data-stu-id="a1ad8-109">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreProperty.cs?name=IgnoreProperty&highlight=6)]

### <a name="fluent-api"></a>[<span data-ttu-id="a1ad8-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="a1ad8-110">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreProperty.cs?name=IgnoreProperty&highlight=3,4)]

***

## <a name="column-names"></a><span data-ttu-id="a1ad8-111">資料行名稱</span><span class="sxs-lookup"><span data-stu-id="a1ad8-111">Column names</span></span>

<span data-ttu-id="a1ad8-112">依照慣例，當使用關係資料庫時，實體屬性會對應到名稱與屬性相同的資料表資料行。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-112">By convention, when using a relational database, entity properties are mapped to table columns having the same name as the property.</span></span>

<span data-ttu-id="a1ad8-113">如果您想要使用不同的名稱來設定資料行，可以這麼做，如下列程式碼片段所示：</span><span class="sxs-lookup"><span data-stu-id="a1ad8-113">If you prefer to configure your columns with different names, you can do so as following code snippet:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="a1ad8-114">資料批註</span><span class="sxs-lookup"><span data-stu-id="a1ad8-114">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnName.cs?Name=ColumnName&highlight=3)]

### <a name="fluent-api"></a>[<span data-ttu-id="a1ad8-115">Fluent API</span><span class="sxs-lookup"><span data-stu-id="a1ad8-115">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnName.cs?Name=ColumnName&highlight=3-5)]

<span data-ttu-id="a1ad8-116">\*\*_</span><span class="sxs-lookup"><span data-stu-id="a1ad8-116">\*\*_</span></span>

## <a name="column-data-types"></a><span data-ttu-id="a1ad8-117">資料行資料類型</span><span class="sxs-lookup"><span data-stu-id="a1ad8-117">Column data types</span></span>

<span data-ttu-id="a1ad8-118">使用關係資料庫時，資料庫提供者會根據屬性的 .NET 類型選取資料類型。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-118">When using a relational database, the database provider selects a data type based on the .NET type of the property.</span></span> <span data-ttu-id="a1ad8-119">它也會考慮其他中繼資料，例如設定的 [最大長度](#maximum-length)、屬性是否為主鍵的一部分等等。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-119">It also takes into account other metadata, such as the configured [maximum length](#maximum-length), whether the property is part of a primary key, etc.</span></span>

<span data-ttu-id="a1ad8-120">例如，SQL Server 會將屬性對應至資料行，並將屬性對應至資料 `DateTime` `datetime2(7)` `string` `nvarchar(max)` 行 (或用於 `nvarchar(450)` 作為索引鍵) 的屬性。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-120">For example, SQL Server maps `DateTime` properties to `datetime2(7)` columns, and `string` properties to `nvarchar(max)` columns (or to `nvarchar(450)` for properties that are used as a key).</span></span>

<span data-ttu-id="a1ad8-121">您也可以設定資料行來指定資料行的精確資料類型。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-121">You can also configure your columns to specify an exact data type for a column.</span></span> <span data-ttu-id="a1ad8-122">例如，下列程式碼會將 `Url` 最大長度為的非 unicode 字串設定為，並將精確度設定為 `200` `Rating` decimal `5` `2` ：</span><span class="sxs-lookup"><span data-stu-id="a1ad8-122">For example, the following code configures `Url` as a non-unicode string with maximum length of `200` and `Rating` as decimal with precision of `5` and scale of `2`:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="a1ad8-123">資料批註</span><span class="sxs-lookup"><span data-stu-id="a1ad8-123">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnDataType.cs?name=ColumnDataType&highlight=4,6)]

### <a name="fluent-api"></a>[<span data-ttu-id="a1ad8-124">Fluent API</span><span class="sxs-lookup"><span data-stu-id="a1ad8-124">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnDataType.cs?name=ColumnDataType&highlight=5-6)]

_*_

### <a name="maximum-length"></a><span data-ttu-id="a1ad8-125">長度上限</span><span class="sxs-lookup"><span data-stu-id="a1ad8-125">Maximum length</span></span>

<span data-ttu-id="a1ad8-126">設定 [最大長度] 會為資料庫提供者提供適當的資料行資料類型的提示，以選擇指定的屬性。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-126">Configuring a maximum length provides a hint to the database provider about the appropriate column data type to choose for a given property.</span></span> <span data-ttu-id="a1ad8-127">最大長度只適用于陣列資料類型，例如 `string` 和 `byte[]` 。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-127">Maximum length only applies to array data types, such as `string` and `byte[]`.</span></span>

> [!NOTE]
> <span data-ttu-id="a1ad8-128">Entity Framework 在將資料傳遞給提供者之前，不會執行任何最大長度驗證。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-128">Entity Framework does not do any validation of maximum length before passing data to the provider.</span></span> <span data-ttu-id="a1ad8-129">由提供者或資料存放區進行驗證（如果適合的話）。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-129">It is up to the provider or data store to validate if appropriate.</span></span> <span data-ttu-id="a1ad8-130">例如，以 SQL Server 為目標時，超過最大長度將會導致例外狀況，因為基礎資料行的資料類型不允許儲存多餘的資料。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-130">For example, when targeting SQL Server, exceeding the maximum length will result in an exception as the data type of the underlying column will not allow excess data to be stored.</span></span>

<span data-ttu-id="a1ad8-131">在下列範例中，設定最大長度500會導致 `nvarchar(500)` 在 SQL Server 上建立類型的資料行：</span><span class="sxs-lookup"><span data-stu-id="a1ad8-131">In the following example, configuring a maximum length of 500 will cause a column of type `nvarchar(500)` to be created on SQL Server:</span></span>

#### <a name="data-annotations"></a>[<span data-ttu-id="a1ad8-132">資料批註</span><span class="sxs-lookup"><span data-stu-id="a1ad8-132">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?name=MaxLength&highlight=4)]

#### <a name="fluent-api"></a>[<span data-ttu-id="a1ad8-133">Fluent API</span><span class="sxs-lookup"><span data-stu-id="a1ad8-133">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?name=MaxLength&highlight=3-5)]

_*_

### <a name="precision-and-scale"></a><span data-ttu-id="a1ad8-134">精確度和小數位數</span><span class="sxs-lookup"><span data-stu-id="a1ad8-134">Precision and Scale</span></span>

<span data-ttu-id="a1ad8-135">從 EFCore 5.0 開始，您可以使用流暢的 API 來設定精確度和小數位數。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-135">Starting with EFCore 5.0, you can use fluent API to configure the precision and scale.</span></span> <span data-ttu-id="a1ad8-136">它會告訴資料庫提供者所需的資料行需要多少儲存體。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-136">It tells the database provider how much storage is needed for a given column.</span></span> <span data-ttu-id="a1ad8-137">它只適用于資料類型，而提供者允許精確度和小數位數的變化-通常是 `decimal` 和 `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-137">It only applies to data types where the provider allows the precision and scale to vary - usually `decimal` and `DateTime`.</span></span>

<span data-ttu-id="a1ad8-138">`decimal`若為屬性，precision 會定義表示資料行將包含的任何值所需的最大位數，且 scale 會定義所需的最大小數位數。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-138">For `decimal` properties, precision defines the maximum number of digits needed to express any value the column will contain, and scale defines the maximum number of decimal places needed.</span></span> <span data-ttu-id="a1ad8-139">`DateTime`若為屬性，precision 會定義用來表示秒數的最大位數，且不使用小數位數。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-139">For `DateTime` properties, precision defines the maximum number of digits needed to express fractions of seconds, and scale is not used.</span></span>

> [!NOTE]
> <span data-ttu-id="a1ad8-140">Entity Framework 在將資料傳遞給提供者之前，不會執行任何精確度或規模的驗證。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-140">Entity Framework does not do any validation of precision or scale before passing data to the provider.</span></span> <span data-ttu-id="a1ad8-141">由提供者或資料存放區視需要進行驗證。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-141">It is up to the provider or data store to validate as appropriate.</span></span> <span data-ttu-id="a1ad8-142">例如，以 SQL Server 為目標時，資料類型的資料行 `datetime` 不允許設定有效位數，而一個資料行的有效 `datetime2` 位數可以是介於0和7（含）之間。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-142">For example, when targeting SQL Server, a column of data type `datetime` does not allow the precision to be set, whereas a `datetime2` one can have precision between 0 and 7 inclusive.</span></span>

<span data-ttu-id="a1ad8-143">在下列範例中， `Score` 將屬性設定為具有有效位數14和小數位數2會導致 `decimal(14,2)` 在 SQL Server 上建立類型的資料行，並將屬性設定為具有有效位數3的資料 `LastUpdated` 行，將會導致類型的資料行 `datetime2(3)` ：</span><span class="sxs-lookup"><span data-stu-id="a1ad8-143">In the following example, configuring the `Score` property to have precision 14 and scale 2 will cause a column of type `decimal(14,2)` to be created on SQL Server, and configuring the `LastUpdated` property to have precision 3 will cause a column of type `datetime2(3)`:</span></span>

#### <a name="data-annotations"></a>[<span data-ttu-id="a1ad8-144">資料批註</span><span class="sxs-lookup"><span data-stu-id="a1ad8-144">Data Annotations</span></span>](#tab/data-annotations)

<span data-ttu-id="a1ad8-145">目前無法透過資料批註設定精確度和小數位數。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-145">Precision and scale cannot currently be configured via data annotations.</span></span>

#### <a name="fluent-api"></a>[<span data-ttu-id="a1ad8-146">Fluent API</span><span class="sxs-lookup"><span data-stu-id="a1ad8-146">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/PrecisionAndScale.cs?name=PrecisionAndScale&highlight=3-9)]

> [!NOTE]
> <span data-ttu-id="a1ad8-147">您永遠不會定義小數位數，而不需要先定義有效位數，因此用來定義尺規的流暢 API 就是 `HasPrecision(precision, scale)` 。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-147">Scale is never defined without first defining precision, so the Fluent API for defining the scale is `HasPrecision(precision, scale)`.</span></span>

_*_

## <a name="required-and-optional-properties"></a><span data-ttu-id="a1ad8-148">必要和選用屬性</span><span class="sxs-lookup"><span data-stu-id="a1ad8-148">Required and optional properties</span></span>

<span data-ttu-id="a1ad8-149">如果屬性可包含，則會將其視為選擇性 `null` 。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-149">A property is considered optional if it is valid for it to contain `null`.</span></span> <span data-ttu-id="a1ad8-150">如果不是 `null` 要指派給屬性的有效值，則會將它視為必要屬性。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-150">If `null` is not a valid value to be assigned to a property then it is considered to be a required property.</span></span> <span data-ttu-id="a1ad8-151">對應到關係資料庫架構時，需要的屬性會建立為不可為 null 的資料行，而選擇性的屬性會建立為可為 null 的資料行。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-151">When mapping to a relational database schema, required properties are created as non-nullable columns, and optional properties are created as nullable columns.</span></span>

### <a name="conventions"></a><span data-ttu-id="a1ad8-152">慣例</span><span class="sxs-lookup"><span data-stu-id="a1ad8-152">Conventions</span></span>

<span data-ttu-id="a1ad8-153">依照慣例，其 .NET 型別可以包含 null 的屬性會設定為選擇性，而 .NET 型別不能包含 null 的屬性則會設定為必要。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-153">By convention, a property whose .NET type can contain null will be configured as optional, whereas properties whose .NET type cannot contain null will be configured as required.</span></span> <span data-ttu-id="a1ad8-154">例如，所有具有 .net 實值型別 (`int` 、 `decimal` 、等 `bool` ) 的屬性都會設定為必要，且所有具有可為 null 之 .net 實值型別的屬性 (`int?` 、 `decimal?` ) 、等等） `bool?` 都會設定為選擇性。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-154">For example, all properties with .NET value types (`int`, `decimal`, `bool`, etc.) are configured as required, and all properties with nullable .NET value types (`int?`, `decimal?`, `bool?`, etc.) are configured as optional.</span></span>

<span data-ttu-id="a1ad8-155">C # 8 引進了新功能，稱為 [可為 null 的參考型別 (NRT) ](/dotnet/csharp/tutorials/nullable-reference-types)，可讓您標注參考型別，以指出其是否有效，以包含 null。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-155">C# 8 introduced a new feature called [nullable reference types (NRT)](/dotnet/csharp/tutorials/nullable-reference-types), which allows reference types to be annotated, indicating whether it is valid for them to contain null or not.</span></span> <span data-ttu-id="a1ad8-156">預設會停用這項功能，並以下列方式影響 EF Core 的行為：</span><span class="sxs-lookup"><span data-stu-id="a1ad8-156">This feature is disabled by default, and affects EF Core's behavior in the following way:</span></span>

<span data-ttu-id="a1ad8-157">_ 如果 (預設) 停用可為 null 的參考型別，則所有具有 .NET 參考型別的屬性都會依照慣例設定為選擇性 (例如 `string`) 。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-157">_ If nullable reference types are disabled (the default), all properties with .NET reference types are configured as optional by convention (for example, `string`).</span></span>
* <span data-ttu-id="a1ad8-158">如果已啟用可為 null 的參考型別，則會根據其 .NET 類型的 c # null 屬性來設定屬性： `string?` 將設定為選擇性，但 `string` 會設定為必要。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-158">If nullable reference types are enabled, properties will be configured based on the C# nullability of their .NET type: `string?` will be configured as optional, but `string` will be configured as required.</span></span>

<span data-ttu-id="a1ad8-159">下列範例顯示具有必要和選擇性屬性的實體類型，並停用 (預設) 和啟用的可為 null 參考功能：</span><span class="sxs-lookup"><span data-stu-id="a1ad8-159">The following example shows an entity type with required and optional properties, with the nullable reference feature disabled (the default) and enabled:</span></span>

#### <a name="without-nrt-default"></a>[<span data-ttu-id="a1ad8-160">沒有 NRT (預設) </span><span class="sxs-lookup"><span data-stu-id="a1ad8-160">Without NRT (default)</span></span>](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=4-8)]

#### <a name="with-nrt"></a>[<span data-ttu-id="a1ad8-161">使用 NRT</span><span class="sxs-lookup"><span data-stu-id="a1ad8-161">With NRT</span></span>](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

<span data-ttu-id="a1ad8-162">建議使用可為 null 的參考型別，因為它會將以 c # 程式碼表示的 null 屬性傳送給 EF Core 的模型和資料庫，並毋使用流暢的 API 或資料注釋，以表達相同的概念兩次。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-162">Using nullable reference types is recommended since it flows the nullability expressed in C# code to EF Core's model and to the database, and obviates the use of the Fluent API or Data Annotations to express the same concept twice.</span></span>

> [!NOTE]
> <span data-ttu-id="a1ad8-163">在現有的專案上啟用可為 null 的參考型別時，請務必注意：先前設定為選用的參考型別屬性現在會設定為必要，除非它們明確標注為可為 null。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-163">Exercise caution when enabling nullable reference types on an existing project: reference type properties which were previously configured as optional will now be configured as required, unless they are explicitly annotated to be nullable.</span></span> <span data-ttu-id="a1ad8-164">管理關係資料庫架構時，這可能會導致產生遷移，進而改變資料庫資料行的 null 屬性。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-164">When managing a relational database schema, this may cause migrations to be generated which alter the database column's nullability.</span></span>

<span data-ttu-id="a1ad8-165">如需可為 null 的參考型別，以及如何搭配 EF Core 使用這些類型的詳細資訊， [請參閱這項功能的專用檔頁面](xref:core/miscellaneous/nullable-reference-types)。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-165">For more information on nullable reference types and how to use them with EF Core, [see the dedicated documentation page for this feature](xref:core/miscellaneous/nullable-reference-types).</span></span>

### <a name="explicit-configuration"></a><span data-ttu-id="a1ad8-166">明確設定</span><span class="sxs-lookup"><span data-stu-id="a1ad8-166">Explicit configuration</span></span>

<span data-ttu-id="a1ad8-167">慣例可以設定為選擇性的屬性，如下所示：</span><span class="sxs-lookup"><span data-stu-id="a1ad8-167">A property that would be optional by convention can be configured to be required as follows:</span></span>

#### <a name="data-annotations"></a>[<span data-ttu-id="a1ad8-168">資料批註</span><span class="sxs-lookup"><span data-stu-id="a1ad8-168">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?name=Required&highlight=4)]

#### <a name="fluent-api"></a>[<span data-ttu-id="a1ad8-169">Fluent API</span><span class="sxs-lookup"><span data-stu-id="a1ad8-169">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?name=Required&highlight=3-5)]

***

## <a name="column-collations"></a><span data-ttu-id="a1ad8-170">資料行定序</span><span class="sxs-lookup"><span data-stu-id="a1ad8-170">Column collations</span></span>

> [!NOTE]
> <span data-ttu-id="a1ad8-171">這項功能是在 EF Core 5.0 中引進。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-171">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="a1ad8-172">您可以在文字資料行上定義定序，以決定如何比較和排序定序。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-172">A collation can be defined on text columns, determining how they are compared and ordered.</span></span> <span data-ttu-id="a1ad8-173">例如，下列程式碼片段會將 SQL Server 資料行設定為不區分大小寫：</span><span class="sxs-lookup"><span data-stu-id="a1ad8-173">For example, the following code snippet configures a SQL Server column to be case-insensitive:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?range=42-43)]

<span data-ttu-id="a1ad8-174">如果資料庫中的所有資料行都需要使用特定定序，請改為在資料庫層級定義定序。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-174">If all columns in a database need to use a certain collation, define the collation at the database level instead.</span></span>

<span data-ttu-id="a1ad8-175">您可以在 [定 [序檔] 頁面](xref:core/miscellaneous/collations-and-case-sensitivity)中找到有關定序 EF Core 支援的一般資訊。</span><span class="sxs-lookup"><span data-stu-id="a1ad8-175">General information about EF Core support for collations can be found in the [collation documentation page](xref:core/miscellaneous/collations-and-case-sensitivity).</span></span>
