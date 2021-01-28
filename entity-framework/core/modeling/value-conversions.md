---
title: 值轉換-EF Core
description: 在 Entity Framework Core 模型中設定值轉換器
author: ajcvickers
ms.date: 01/16/2021
uid: core/modeling/value-conversions
ms.openlocfilehash: d9d3753c7f0b257a2109e4af1f587df913c15b44
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983439"
---
# <a name="value-conversions"></a><span data-ttu-id="1bf6f-103">值轉換</span><span class="sxs-lookup"><span data-stu-id="1bf6f-103">Value Conversions</span></span>

<span data-ttu-id="1bf6f-104">值轉換器可在讀取或寫入資料庫時，允許轉換屬性值。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-104">Value converters allow property values to be converted when reading from or writing to the database.</span></span> <span data-ttu-id="1bf6f-105">這項轉換可從某個值轉換為相同類型的另一個值 (例如，將字串) 或從某個類型的值加密為另一個類型的值 (例如，在資料庫中來回轉換字串的列舉值。 ) </span><span class="sxs-lookup"><span data-stu-id="1bf6f-105">This conversion can be from one value to another of the same type (for example, encrypting strings) or from a value of one type to a value of another type (for example, converting enum values to and from strings in the database.)</span></span>

> [!TIP]
> <span data-ttu-id="1bf6f-106">您可以 [從 GitHub 下載範例程式碼](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/)，以執行並偵測到本檔中的所有程式碼。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-106">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).</span></span>

## <a name="overview"></a><span data-ttu-id="1bf6f-107">概觀</span><span class="sxs-lookup"><span data-stu-id="1bf6f-107">Overview</span></span>

<span data-ttu-id="1bf6f-108">值轉換器的指定方式為 `ModelClrType` 和 `ProviderClrType` 。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-108">Value converters are specified in terms of a `ModelClrType` and a `ProviderClrType`.</span></span> <span data-ttu-id="1bf6f-109">模型類型是實體類型中屬性的 .NET 類型。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-109">The model type is the .NET type of the property in the entity type.</span></span> <span data-ttu-id="1bf6f-110">提供者類型是資料庫提供者所瞭解的 .NET 型別。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-110">The provider type is the .NET type understood by the database provider.</span></span> <span data-ttu-id="1bf6f-111">例如，若要將列舉儲存為資料庫中的字串，模型類型為列舉的型別，而提供者類型為 `String` 。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-111">For example, to save enums as strings in the database, the model type is the type of the enum, and the provider type is `String`.</span></span> <span data-ttu-id="1bf6f-112">這兩種類型可以相同。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-112">These two types can be the same.</span></span>

<span data-ttu-id="1bf6f-113">轉換會使用兩個 `Func` 運算式樹狀架構來定義：一個從 `ModelClrType` 到 `ProviderClrType` ，另一個從轉換 `ProviderClrType` 為 `ModelClrType` 。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-113">Conversions are defined using two `Func` expression trees: one from `ModelClrType` to `ProviderClrType` and the other from `ProviderClrType` to `ModelClrType`.</span></span> <span data-ttu-id="1bf6f-114">使用運算式樹狀架構可將它們編譯成資料庫存取委派，以進行有效率的轉換。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-114">Expression trees are used so that they can be compiled into the database access delegate for efficient conversions.</span></span> <span data-ttu-id="1bf6f-115">運算式樹狀架構可能包含對轉換方法的簡單呼叫，以進行複雜的轉換。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-115">The expression tree may contain a simple call to a conversion method for complex conversions.</span></span>

> [!NOTE]
> <span data-ttu-id="1bf6f-116">已設定值轉換的屬性可能也需要指定 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> 。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-116">A property that has been configured for value conversion may also need to specify a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601>.</span></span> <span data-ttu-id="1bf6f-117">如需詳細資訊，請參閱下列範例和 [值](xref:core/modeling/value-comparers) 比較子檔。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-117">See the examples below, and the [Value Comparers](xref:core/modeling/value-comparers) documentation for more information.</span></span>

## <a name="configuring-a-value-converter"></a><span data-ttu-id="1bf6f-118">設定值轉換器</span><span class="sxs-lookup"><span data-stu-id="1bf6f-118">Configuring a value converter</span></span>

<span data-ttu-id="1bf6f-119">值轉換是在中設定 <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-119">Value conversions are configured in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="1bf6f-120">例如，假設列舉和實體型別定義為：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-120">For example, consider an enum and entity type defined as:</span></span>

<!--
        public class Rider
        {
            public int Id { get; set; }
            public EquineBeast Mount { get; set; }
        }

        public enum EquineBeast
        {
            Donkey,
            Mule,
            Horse,
            Unicorn
        }
-->
[!code-csharp[BeastAndRider](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=BeastAndRider)]

<span data-ttu-id="1bf6f-121">您可以在中設定轉換， <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> 以將列舉值儲存為資料庫中的字串，例如 "Donkey"、"Mule" 等等。您只需要提供一個從轉換成的函式 `ModelClrType` ，並將另一個函式 `ProviderClrType` 轉換成相反的轉換：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-121">Conversions can be configured in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> to store the enum values as strings such as "Donkey", "Mule", etc. in the database; you simply need to provide one function which converts from the `ModelClrType` to the `ProviderClrType`, and another for the opposite conversion:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion(
                        v => v.ToString(),
                        v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));
            }
-->
[!code-csharp[ExplicitConversion](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ExplicitConversion)]

> [!NOTE]
> <span data-ttu-id="1bf6f-122">`null`永遠不會將值傳遞至值轉換器。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-122">A `null` value will never be passed to a value converter.</span></span> <span data-ttu-id="1bf6f-123">在資料庫資料行中的 null 一律是實體實例中的 null，反之亦然。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-123">A null in a database column is always a null in the entity instance, and vice-versa.</span></span> <span data-ttu-id="1bf6f-124">這可讓轉換的執行變得更容易，並可讓它們在可為 null 且不可為 null 的屬性之間共用。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-124">This makes the implementation of conversions easier and allows them to be shared amongst nullable and non-nullable properties.</span></span> <span data-ttu-id="1bf6f-125">如需詳細資訊，請參閱 [GitHub 問題 #13850](https://github.com/dotnet/efcore/issues/13850) 。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-125">See [GitHub issue #13850](https://github.com/dotnet/efcore/issues/13850) for more information.</span></span>

## <a name="pre-defined-conversions"></a><span data-ttu-id="1bf6f-126">預先定義的轉換</span><span class="sxs-lookup"><span data-stu-id="1bf6f-126">Pre-defined conversions</span></span>

<span data-ttu-id="1bf6f-127">EF Core 包含許多預先定義的轉換，以避免需要手動寫入轉換函數。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-127">EF Core contains many pre-defined conversions that avoid the need to write conversion functions manually.</span></span> <span data-ttu-id="1bf6f-128">相反地，EF Core 會根據模型中的屬性型別和要求的資料庫提供者類型，挑選要使用的轉換。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-128">Instead, EF Core will pick the conversion to use based on the property type in the model and the requested database provider type.</span></span>

<span data-ttu-id="1bf6f-129">例如，列舉至字串轉換是以上述範例的方式來使用，但是當提供者類型設定為使用泛型型別時，EF Core 會自動執行這 `string` 項作業 <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A> ：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-129">For example, enum to string conversions are used as an example above, but EF Core will actually do this automatically when the provider type is configured as `string` using the generic type of <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A>:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion<string>();
            }
-->
[!code-csharp[ConversionByClrType](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByClrType)]

<span data-ttu-id="1bf6f-130">您可以藉由明確指定資料庫資料行類型來達成相同的目的。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-130">The same thing can be achieved by explicitly specifying the database column type.</span></span> <span data-ttu-id="1bf6f-131">例如，如果實體類型定義如下：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-131">For example, if the entity type is defined like so:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="1bf6f-132">資料批註</span><span class="sxs-lookup"><span data-stu-id="1bf6f-132">Data Annotations</span></span>](#tab/data-annotations)

<!--
        public class Rider2
        {
            public int Id { get; set; }

            [Column(TypeName = "nvarchar(24)")]
            public EquineBeast Mount { get; set; }
        }
-->
[!code-csharp[ConversionByDatabaseType](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByDatabaseType)]

### <a name="fluent-api"></a>[<span data-ttu-id="1bf6f-133">Fluent API</span><span class="sxs-lookup"><span data-stu-id="1bf6f-133">Fluent API</span></span>](#tab/fluent-api)

<!--
                modelBuilder
                    .Entity<Rider2>()
                    .Property(e => e.Mount)
                    .HasColumnType("nvarchar(24)");
-->
[!code-csharp[ConversionByDatabaseTypeFluent](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByDatabaseTypeFluent)]

***

<span data-ttu-id="1bf6f-134">然後，列舉值會以字串的形式儲存在資料庫中，而不需要在中進行任何進一步的設定 <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> 。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-134">Then the enum values will be saved as strings in the database without any further configuration in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span>

## <a name="the-valueconverter-class"></a><span data-ttu-id="1bf6f-135">ValueConverter 類別</span><span class="sxs-lookup"><span data-stu-id="1bf6f-135">The ValueConverter class</span></span>

<span data-ttu-id="1bf6f-136"><xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A>如上面所示呼叫會建立 <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> 實例，並在屬性上設定它。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-136">Calling <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A> as shown above will create a <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> instance and set it on the property.</span></span> <span data-ttu-id="1bf6f-137">`ValueConverter`可以改為明確建立。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-137">The `ValueConverter` can instead be created explicitly.</span></span> <span data-ttu-id="1bf6f-138">例如：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-138">For example:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new ValueConverter<EquineBeast, string>(
                    v => v.ToString(),
                    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion(converter);
            }
-->
[!code-csharp[ConversionByConverterInstance](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByConverterInstance)]

<span data-ttu-id="1bf6f-139">當多個屬性使用相同的轉換時，這會很有用。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-139">This can be useful when multiple properties use the same conversion.</span></span>

## <a name="built-in-converters"></a><span data-ttu-id="1bf6f-140">內建轉換器</span><span class="sxs-lookup"><span data-stu-id="1bf6f-140">Built-in converters</span></span>

<span data-ttu-id="1bf6f-141">如先前所述，EF Core 隨附一組預先定義的 <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> 類別，可在 <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion> 命名空間中找到。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-141">As mentioned above, EF Core ships with a set of pre-defined <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> classes, found in the <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion> namespace.</span></span> <span data-ttu-id="1bf6f-142">在許多情況下，EF 會根據模型中的屬性型別和資料庫中所要求的型別，選擇適當的內建轉換器，如上所示列舉。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-142">In many cases EF will choose the appropriate built-in converter based on the type of the property in the model and the type requested in the database, as shown above for enums.</span></span> <span data-ttu-id="1bf6f-143">例如， `.HasConversion<int>()` 在屬性上使用 `bool` 會導致 EF Core 將 bool 值轉換成數位零和一個值：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-143">For example, using `.HasConversion<int>()` on a `bool` property will cause EF Core to convert bool values to numerical zero and one values:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                modelBuilder
                    .Entity<User>()
                    .Property(e => e.IsActive)
                    .HasConversion<int>();
            }
-->
[!code-csharp[ConversionByBuiltInBoolToInt](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByBuiltInBoolToInt)]

<span data-ttu-id="1bf6f-144">這與建立內建的實例 <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> 和明確設定的方式相同：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-144">This is functionally the same as creating an instance of the built-in <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> and setting it explicitly:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new BoolToZeroOneConverter<int>();

                modelBuilder
                    .Entity<User>()
                    .Property(e => e.IsActive)
                    .HasConversion(converter);
            }
-->
[!code-csharp[ConversionByBuiltInBoolToIntExplicit](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByBuiltInBoolToIntExplicit)]

<span data-ttu-id="1bf6f-145">下表摘要說明從模型/屬性類型到資料庫提供者類型的常用預先定義轉換。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-145">The following table summarizes commonly-used pre-defined conversions from model/property types to database provider types.</span></span> <span data-ttu-id="1bf6f-146">在資料表中 `any_numeric_type` ，表示下列其中一個：、、、、、、、、、、 `int` `short` `long` `byte` `uint` `ushort` `ulong` `sbyte` `char` `decimal` `float` 或 `double` 。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-146">In the table `any_numeric_type` means one of `int`, `short`, `long`, `byte`, `uint`, `ushort`, `ulong`, `sbyte`, `char`, `decimal`, `float`, or `double`.</span></span>

| <span data-ttu-id="1bf6f-147">模型/屬性類型</span><span class="sxs-lookup"><span data-stu-id="1bf6f-147">Model/property type</span></span> | <span data-ttu-id="1bf6f-148">提供者/資料庫類型</span><span class="sxs-lookup"><span data-stu-id="1bf6f-148">Provider/database type</span></span> | <span data-ttu-id="1bf6f-149">轉換</span><span class="sxs-lookup"><span data-stu-id="1bf6f-149">Conversion</span></span>                                                | <span data-ttu-id="1bf6f-150">使用方式</span><span class="sxs-lookup"><span data-stu-id="1bf6f-150">Usage</span></span>
|:--------------------|------------------------|-----------------------------------------------------------|------
| <span data-ttu-id="1bf6f-151">bool</span><span class="sxs-lookup"><span data-stu-id="1bf6f-151">bool</span></span>                | <span data-ttu-id="1bf6f-152">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="1bf6f-152">any_numeric_type</span></span>       | <span data-ttu-id="1bf6f-153">False/true 至0/1</span><span class="sxs-lookup"><span data-stu-id="1bf6f-153">False/true to 0/1</span></span>                                         | `.HasConversion<any_numeric_type>()`
|                     | <span data-ttu-id="1bf6f-154">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="1bf6f-154">any_numeric_type</span></span>       | <span data-ttu-id="1bf6f-155">False/true 表示任何兩個數字</span><span class="sxs-lookup"><span data-stu-id="1bf6f-155">False/true to any two numbers</span></span>                             | <span data-ttu-id="1bf6f-156">使用<xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601></span><span class="sxs-lookup"><span data-stu-id="1bf6f-156">Use <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601></span></span>
|                     | <span data-ttu-id="1bf6f-157">字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-157">string</span></span>                 | <span data-ttu-id="1bf6f-158">False/true 表示 "Y"/"N"</span><span class="sxs-lookup"><span data-stu-id="1bf6f-158">False/true to "Y"/"N"</span></span>                                     | `.HasConversion<string>()`
|                     | <span data-ttu-id="1bf6f-159">字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-159">string</span></span>                 | <span data-ttu-id="1bf6f-160">False/true 表示任何兩個字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-160">False/true to any two strings</span></span>                             | <span data-ttu-id="1bf6f-161">使用<xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter></span><span class="sxs-lookup"><span data-stu-id="1bf6f-161">Use <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter></span></span>
| <span data-ttu-id="1bf6f-162">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="1bf6f-162">any_numeric_type</span></span>    | <span data-ttu-id="1bf6f-163">bool</span><span class="sxs-lookup"><span data-stu-id="1bf6f-163">bool</span></span>                   | <span data-ttu-id="1bf6f-164">0/1 到 false/true</span><span class="sxs-lookup"><span data-stu-id="1bf6f-164">0/1 to false/true</span></span>                                         | `.HasConversion<bool>()`
|                     | <span data-ttu-id="1bf6f-165">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="1bf6f-165">any_numeric_type</span></span>       | <span data-ttu-id="1bf6f-166">簡單轉換</span><span class="sxs-lookup"><span data-stu-id="1bf6f-166">Simple cast</span></span>                                               | `.HasConversion<any_numeric_type>()`
|                     | <span data-ttu-id="1bf6f-167">字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-167">string</span></span>                 | <span data-ttu-id="1bf6f-168">字串形式的數位</span><span class="sxs-lookup"><span data-stu-id="1bf6f-168">The number as a string</span></span>                                    | `.HasConversion<string>()`
| <span data-ttu-id="1bf6f-169">列舉</span><span class="sxs-lookup"><span data-stu-id="1bf6f-169">Enum</span></span>                | <span data-ttu-id="1bf6f-170">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="1bf6f-170">any_numeric_type</span></span>       | <span data-ttu-id="1bf6f-171">列舉的數值</span><span class="sxs-lookup"><span data-stu-id="1bf6f-171">The numeric value of the enum</span></span>                             | `.HasConversion<any_numeric_type>()`
|                     | <span data-ttu-id="1bf6f-172">字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-172">string</span></span>                 | <span data-ttu-id="1bf6f-173">列舉值的字串表示</span><span class="sxs-lookup"><span data-stu-id="1bf6f-173">The string representation of the enum value</span></span>               | `.HasConversion<string>()`
| <span data-ttu-id="1bf6f-174">字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-174">string</span></span>              | <span data-ttu-id="1bf6f-175">bool</span><span class="sxs-lookup"><span data-stu-id="1bf6f-175">bool</span></span>                   | <span data-ttu-id="1bf6f-176">將字串剖析為 bool</span><span class="sxs-lookup"><span data-stu-id="1bf6f-176">Parses the string as a bool</span></span>                               | `.HasConversion<bool>()`
|                     | <span data-ttu-id="1bf6f-177">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="1bf6f-177">any_numeric_type</span></span>       | <span data-ttu-id="1bf6f-178">將字串剖析為指定的數位類型</span><span class="sxs-lookup"><span data-stu-id="1bf6f-178">Parses the string as the given numeric type</span></span>               | `.HasConversion<any_numeric_type>()`
|                     | <span data-ttu-id="1bf6f-179">char</span><span class="sxs-lookup"><span data-stu-id="1bf6f-179">char</span></span>                   | <span data-ttu-id="1bf6f-180">字串的第一個字元</span><span class="sxs-lookup"><span data-stu-id="1bf6f-180">The first character of the string</span></span>                         | `.HasConversion<char>()`
|                     | <span data-ttu-id="1bf6f-181">Datetime</span><span class="sxs-lookup"><span data-stu-id="1bf6f-181">DateTime</span></span>               | <span data-ttu-id="1bf6f-182">將字串剖析為 DateTime</span><span class="sxs-lookup"><span data-stu-id="1bf6f-182">Parses the string as a DateTime</span></span>                           | `.HasConversion<DateTime>()`
|                     | <span data-ttu-id="1bf6f-183">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="1bf6f-183">DateTimeOffset</span></span>         | <span data-ttu-id="1bf6f-184">將字串剖析為 DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="1bf6f-184">Parses the string as a DateTimeOffset</span></span>                     | `.HasConversion<DateTimeOffset>()`
|                     | <span data-ttu-id="1bf6f-185">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="1bf6f-185">TimeSpan</span></span>               | <span data-ttu-id="1bf6f-186">將字串剖析為 TimeSpan</span><span class="sxs-lookup"><span data-stu-id="1bf6f-186">Parses the string as a TimeSpan</span></span>                           | `.HasConversion<TimeSpan>()`
|                     | <span data-ttu-id="1bf6f-187">Guid</span><span class="sxs-lookup"><span data-stu-id="1bf6f-187">Guid</span></span>                   | <span data-ttu-id="1bf6f-188">將字串剖析為 Guid</span><span class="sxs-lookup"><span data-stu-id="1bf6f-188">Parses the string as a Guid</span></span>                               | `.HasConversion<Guid>()`
|                     | <span data-ttu-id="1bf6f-189">byte[]</span><span class="sxs-lookup"><span data-stu-id="1bf6f-189">byte[]</span></span>                 | <span data-ttu-id="1bf6f-190">以 UTF8 位元組表示的字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-190">The string as UTF8 bytes</span></span>                                  | `.HasConversion<byte[]>()`
| <span data-ttu-id="1bf6f-191">char</span><span class="sxs-lookup"><span data-stu-id="1bf6f-191">char</span></span>                | <span data-ttu-id="1bf6f-192">字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-192">string</span></span>                 | <span data-ttu-id="1bf6f-193">單一字元字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-193">A single character string</span></span>                                 | `.HasConversion<string>()`
| <span data-ttu-id="1bf6f-194">Datetime</span><span class="sxs-lookup"><span data-stu-id="1bf6f-194">DateTime</span></span>            | <span data-ttu-id="1bf6f-195">long</span><span class="sxs-lookup"><span data-stu-id="1bf6f-195">long</span></span>                   | <span data-ttu-id="1bf6f-196">編碼的日期/時間保留 DateTime. 種類</span><span class="sxs-lookup"><span data-stu-id="1bf6f-196">Encoded date/time preserving DateTime.Kind</span></span>                | `.HasConversion<long>()`
|                     | <span data-ttu-id="1bf6f-197">long</span><span class="sxs-lookup"><span data-stu-id="1bf6f-197">long</span></span>                   | <span data-ttu-id="1bf6f-198">蜱</span><span class="sxs-lookup"><span data-stu-id="1bf6f-198">Ticks</span></span>                                                     | <span data-ttu-id="1bf6f-199">使用<xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter></span><span class="sxs-lookup"><span data-stu-id="1bf6f-199">Use <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter></span></span>
|                     | <span data-ttu-id="1bf6f-200">字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-200">string</span></span>                 | <span data-ttu-id="1bf6f-201">不因文化特性而異的日期/時間字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-201">Invariant culture date/time string</span></span>                        | `.HasConversion<string>()`
| <span data-ttu-id="1bf6f-202">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="1bf6f-202">DateTimeOffset</span></span>      | <span data-ttu-id="1bf6f-203">long</span><span class="sxs-lookup"><span data-stu-id="1bf6f-203">long</span></span>                   | <span data-ttu-id="1bf6f-204">具有位移的編碼日期/時間</span><span class="sxs-lookup"><span data-stu-id="1bf6f-204">Encoded date/time with offset</span></span>                             | `.HasConversion<long>()`
|                     | <span data-ttu-id="1bf6f-205">字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-205">string</span></span>                 | <span data-ttu-id="1bf6f-206">具有位移的非變異文化特性日期/時間字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-206">Invariant culture date/time string with offset</span></span>            | `.HasConversion<string>()`
| <span data-ttu-id="1bf6f-207">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="1bf6f-207">TimeSpan</span></span>            | <span data-ttu-id="1bf6f-208">long</span><span class="sxs-lookup"><span data-stu-id="1bf6f-208">long</span></span>                   | <span data-ttu-id="1bf6f-209">蜱</span><span class="sxs-lookup"><span data-stu-id="1bf6f-209">Ticks</span></span>                                                     | `.HasConversion<long>()`
|                     | <span data-ttu-id="1bf6f-210">字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-210">string</span></span>                 | <span data-ttu-id="1bf6f-211">不變文化特性時間範圍字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-211">Invariant culture time span string</span></span>                        | `.HasConversion<string>()`
| <span data-ttu-id="1bf6f-212">Uri</span><span class="sxs-lookup"><span data-stu-id="1bf6f-212">Uri</span></span>                 | <span data-ttu-id="1bf6f-213">字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-213">string</span></span>                 | <span data-ttu-id="1bf6f-214">字串形式的 URI</span><span class="sxs-lookup"><span data-stu-id="1bf6f-214">The URI as a string</span></span>                                       | `.HasConversion<string>()`
| <span data-ttu-id="1bf6f-215">PhysicalAddress</span><span class="sxs-lookup"><span data-stu-id="1bf6f-215">PhysicalAddress</span></span>     | <span data-ttu-id="1bf6f-216">字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-216">string</span></span>                 | <span data-ttu-id="1bf6f-217">字串形式的位址</span><span class="sxs-lookup"><span data-stu-id="1bf6f-217">The address as a string</span></span>                                   | `.HasConversion<string>()`
|                     | <span data-ttu-id="1bf6f-218">byte[]</span><span class="sxs-lookup"><span data-stu-id="1bf6f-218">byte[]</span></span>                 | <span data-ttu-id="1bf6f-219">以位元組由大到小的網路順序的位元組</span><span class="sxs-lookup"><span data-stu-id="1bf6f-219">Bytes in big-endian network order</span></span>                         | `.HasConversion<byte[]>()`
| <span data-ttu-id="1bf6f-220">IPAddress</span><span class="sxs-lookup"><span data-stu-id="1bf6f-220">IPAddress</span></span>           | <span data-ttu-id="1bf6f-221">字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-221">string</span></span>                 | <span data-ttu-id="1bf6f-222">字串形式的位址</span><span class="sxs-lookup"><span data-stu-id="1bf6f-222">The address as a string</span></span>                                   | `.HasConversion<string>()`
|                     | <span data-ttu-id="1bf6f-223">byte[]</span><span class="sxs-lookup"><span data-stu-id="1bf6f-223">byte[]</span></span>                 | <span data-ttu-id="1bf6f-224">以位元組由大到小的網路順序的位元組</span><span class="sxs-lookup"><span data-stu-id="1bf6f-224">Bytes in big-endian network order</span></span>                         | `.HasConversion<byte[]>()`
| <span data-ttu-id="1bf6f-225">Guid</span><span class="sxs-lookup"><span data-stu-id="1bf6f-225">Guid</span></span>                | <span data-ttu-id="1bf6f-226">字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-226">string</span></span>                 | <span data-ttu-id="1bf6f-227">' Dddddddd-dddd-dddd-dddd-dddddddddddd ' 格式的 GUID</span><span class="sxs-lookup"><span data-stu-id="1bf6f-227">The GUID in 'dddddddd-dddd-dddd-dddd-dddddddddddd' format</span></span> | `.HasConversion<string>()`
|                     | <span data-ttu-id="1bf6f-228">byte[]</span><span class="sxs-lookup"><span data-stu-id="1bf6f-228">byte[]</span></span>                 | <span data-ttu-id="1bf6f-229">.NET 二進位序列化順序的位元組</span><span class="sxs-lookup"><span data-stu-id="1bf6f-229">Bytes in .NET binary serialization order</span></span>                  | `.HasConversion<byte[]>()`

<span data-ttu-id="1bf6f-230">請注意，這些轉換會假設值的格式適合進行轉換。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-230">Note that these conversions assume that the format of the value is appropriate for the conversion.</span></span> <span data-ttu-id="1bf6f-231">例如，如果字串值無法剖析為數字，則將字串轉換成數位將會失敗。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-231">For example, converting strings to numbers will fail if the string values cannot be parsed as numbers.</span></span>

<span data-ttu-id="1bf6f-232">內建轉換器的完整清單如下：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-232">The full list of built-in converters is:</span></span>

* <span data-ttu-id="1bf6f-233">轉換 bool 屬性：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-233">Converting bool properties:</span></span>
  * <span data-ttu-id="1bf6f-234"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter> -Bool 至字串，例如 "Y" 和 "N"</span><span class="sxs-lookup"><span data-stu-id="1bf6f-234"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter> - Bool to strings such as "Y" and "N"</span></span>
  * <span data-ttu-id="1bf6f-235"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601> -Bool 至任何兩個值</span><span class="sxs-lookup"><span data-stu-id="1bf6f-235"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601> - Bool to any two values</span></span>
  * <span data-ttu-id="1bf6f-236"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> -Bool 至零和1</span><span class="sxs-lookup"><span data-stu-id="1bf6f-236"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> - Bool to zero and one</span></span>
* <span data-ttu-id="1bf6f-237">轉換位元組陣列屬性：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-237">Converting byte array properties:</span></span>
  * <span data-ttu-id="1bf6f-238"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BytesToStringConverter> -位元組陣列至 Base64 編碼的字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-238"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BytesToStringConverter> - Byte array to Base64-encoded string</span></span>
* <span data-ttu-id="1bf6f-239">只需要型別轉換的任何轉換</span><span class="sxs-lookup"><span data-stu-id="1bf6f-239">Any conversion that requires only a type-cast</span></span>
  * <span data-ttu-id="1bf6f-240"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CastingConverter%602> -只需要型別轉換的轉換</span><span class="sxs-lookup"><span data-stu-id="1bf6f-240"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CastingConverter%602> - Conversions that require only a type cast</span></span>
* <span data-ttu-id="1bf6f-241">轉換 char 屬性：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-241">Converting char properties:</span></span>
  * <span data-ttu-id="1bf6f-242"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CharToStringConverter> -Char 至單一字元字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-242"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CharToStringConverter> - Char to single character string</span></span>
* <span data-ttu-id="1bf6f-243">轉換 <xref:System.DateTimeOffset> 屬性：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-243">Converting <xref:System.DateTimeOffset> properties:</span></span>
  * <span data-ttu-id="1bf6f-244"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBinaryConverter> - <xref:System.DateTimeOffset> 至二進位編碼的64位值</span><span class="sxs-lookup"><span data-stu-id="1bf6f-244"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBinaryConverter> - <xref:System.DateTimeOffset> to binary-encoded 64-bit value</span></span>
  * <span data-ttu-id="1bf6f-245"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBytesConverter> - <xref:System.DateTimeOffset> 至位元組陣列</span><span class="sxs-lookup"><span data-stu-id="1bf6f-245"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBytesConverter> - <xref:System.DateTimeOffset> to byte array</span></span>
  * <span data-ttu-id="1bf6f-246"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToStringConverter> - <xref:System.DateTimeOffset> 至字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-246"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToStringConverter> - <xref:System.DateTimeOffset> to string</span></span>
* <span data-ttu-id="1bf6f-247">轉換 <xref:System.DateTime> 屬性：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-247">Converting <xref:System.DateTime> properties:</span></span>
  * <span data-ttu-id="1bf6f-248"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToBinaryConverter> - <xref:System.DateTime> 至64位值（包括 >datetimekind.unspecified）</span><span class="sxs-lookup"><span data-stu-id="1bf6f-248"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToBinaryConverter> - <xref:System.DateTime> to 64-bit value including DateTimeKind</span></span>
  * <span data-ttu-id="1bf6f-249"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToStringConverter> - <xref:System.DateTime> 至字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-249"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToStringConverter> - <xref:System.DateTime> to string</span></span>
  * <span data-ttu-id="1bf6f-250"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter> - <xref:System.DateTime> 刻度</span><span class="sxs-lookup"><span data-stu-id="1bf6f-250"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter> - <xref:System.DateTime> to ticks</span></span>
* <span data-ttu-id="1bf6f-251">轉換列舉屬性：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-251">Converting enum properties:</span></span>
  * <span data-ttu-id="1bf6f-252"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToNumberConverter%602> -對基礎數位的列舉</span><span class="sxs-lookup"><span data-stu-id="1bf6f-252"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToNumberConverter%602> - Enum to underlying number</span></span>
  * <span data-ttu-id="1bf6f-253"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToStringConverter%601> -列舉至字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-253"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToStringConverter%601> - Enum to string</span></span>
* <span data-ttu-id="1bf6f-254">轉換 <xref:System.Guid> 屬性：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-254">Converting <xref:System.Guid> properties:</span></span>
  * <span data-ttu-id="1bf6f-255"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToBytesConverter> - <xref:System.Guid> 至位元組陣列</span><span class="sxs-lookup"><span data-stu-id="1bf6f-255"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToBytesConverter> - <xref:System.Guid> to byte array</span></span>
  * <span data-ttu-id="1bf6f-256"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToStringConverter> - <xref:System.Guid> 至字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-256"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToStringConverter> - <xref:System.Guid> to string</span></span>
* <span data-ttu-id="1bf6f-257">轉換 <xref:System.Net.IPAddress> 屬性：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-257">Converting <xref:System.Net.IPAddress> properties:</span></span>
  * <span data-ttu-id="1bf6f-258"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToBytesConverter> - <xref:System.Net.IPAddress> 至位元組陣列</span><span class="sxs-lookup"><span data-stu-id="1bf6f-258"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToBytesConverter> - <xref:System.Net.IPAddress> to byte array</span></span>
  * <span data-ttu-id="1bf6f-259"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToStringConverter> - <xref:System.Net.IPAddress> 至字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-259"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToStringConverter> - <xref:System.Net.IPAddress> to string</span></span>
* <span data-ttu-id="1bf6f-260">將數值 (int、double、decimal 等 ) 屬性轉換：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-260">Converting numeric (int, double, decimal, etc.) properties:</span></span>
  * <span data-ttu-id="1bf6f-261"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToBytesConverter%601> -位元組陣列的任何數值</span><span class="sxs-lookup"><span data-stu-id="1bf6f-261"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToBytesConverter%601> - Any numerical value to byte array</span></span>
  * <span data-ttu-id="1bf6f-262"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToStringConverter%601> -字串的任何數值</span><span class="sxs-lookup"><span data-stu-id="1bf6f-262"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToStringConverter%601> - Any numerical value to string</span></span>
* <span data-ttu-id="1bf6f-263">轉換 <xref:System.Net.NetworkInformation.PhysicalAddress> 屬性：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-263">Converting <xref:System.Net.NetworkInformation.PhysicalAddress> properties:</span></span>
  * <span data-ttu-id="1bf6f-264"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToBytesConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> 至位元組陣列</span><span class="sxs-lookup"><span data-stu-id="1bf6f-264"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToBytesConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> to byte array</span></span>
  * <span data-ttu-id="1bf6f-265"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToStringConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> 至字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-265"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToStringConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> to string</span></span>
* <span data-ttu-id="1bf6f-266">轉換字串屬性：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-266">Converting string properties:</span></span>
  * <span data-ttu-id="1bf6f-267"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBoolConverter> -字串（例如 "Y" 和 "N" 至 bool）</span><span class="sxs-lookup"><span data-stu-id="1bf6f-267"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBoolConverter> - Strings such as "Y" and "N" to bool</span></span>
  * <span data-ttu-id="1bf6f-268"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBytesConverter> -字串至 UTF8 位元組</span><span class="sxs-lookup"><span data-stu-id="1bf6f-268"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBytesConverter> - String to UTF8 bytes</span></span>
  * <span data-ttu-id="1bf6f-269"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToCharConverter> -字串至字元</span><span class="sxs-lookup"><span data-stu-id="1bf6f-269"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToCharConverter> - String to character</span></span>
  * <span data-ttu-id="1bf6f-270"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeConverter> -字串至 <xref:System.DateTime></span><span class="sxs-lookup"><span data-stu-id="1bf6f-270"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeConverter> - String to <xref:System.DateTime></span></span>
  * <span data-ttu-id="1bf6f-271"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeOffsetConverter> -字串至 <xref:System.DateTimeOffset></span><span class="sxs-lookup"><span data-stu-id="1bf6f-271"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeOffsetConverter> - String to <xref:System.DateTimeOffset></span></span>
  * <span data-ttu-id="1bf6f-272"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToEnumConverter%601> -字串至列舉</span><span class="sxs-lookup"><span data-stu-id="1bf6f-272"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToEnumConverter%601> - String to enum</span></span>
  * <span data-ttu-id="1bf6f-273"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToGuidConverter> -字串至 <xref:System.Guid></span><span class="sxs-lookup"><span data-stu-id="1bf6f-273"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToGuidConverter> - String to <xref:System.Guid></span></span>
  * <span data-ttu-id="1bf6f-274"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToNumberConverter%601> -字串至數數值型別</span><span class="sxs-lookup"><span data-stu-id="1bf6f-274"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToNumberConverter%601> - String to numeric type</span></span>
  * <span data-ttu-id="1bf6f-275"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToTimeSpanConverter> -字串至 <xref:System.TimeSpan></span><span class="sxs-lookup"><span data-stu-id="1bf6f-275"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToTimeSpanConverter> - String to <xref:System.TimeSpan></span></span>
  * <span data-ttu-id="1bf6f-276"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToUriConverter> -字串至 <xref:System.Uri></span><span class="sxs-lookup"><span data-stu-id="1bf6f-276"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToUriConverter> - String to <xref:System.Uri></span></span>
* <span data-ttu-id="1bf6f-277">轉換 <xref:System.TimeSpan> 屬性：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-277">Converting <xref:System.TimeSpan> properties:</span></span>
  * <span data-ttu-id="1bf6f-278"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToStringConverter> - <xref:System.TimeSpan> 至字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-278"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToStringConverter> - <xref:System.TimeSpan> to string</span></span>
  * <span data-ttu-id="1bf6f-279"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToTicksConverter> - <xref:System.TimeSpan> 刻度</span><span class="sxs-lookup"><span data-stu-id="1bf6f-279"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToTicksConverter> - <xref:System.TimeSpan> to ticks</span></span>
* <span data-ttu-id="1bf6f-280">轉換 <xref:System.Uri> 屬性：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-280">Converting <xref:System.Uri> properties:</span></span>
  * <span data-ttu-id="1bf6f-281"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.UriToStringConverter> - <xref:System.Uri> 至字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-281"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.UriToStringConverter> - <xref:System.Uri> to string</span></span>

<span data-ttu-id="1bf6f-282">請注意，所有內建的轉換器都是無狀態的，因此單一實例可以由多個屬性安全地共用。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-282">Note that all the built-in converters are stateless and so a single instance can be safely shared by multiple properties.</span></span>

## <a name="column-facets-and-mapping-hints"></a><span data-ttu-id="1bf6f-283">資料行 facet 和對應提示</span><span class="sxs-lookup"><span data-stu-id="1bf6f-283">Column facets and mapping hints</span></span>

<span data-ttu-id="1bf6f-284">某些資料庫類型具有修改資料儲存方式的 facet。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-284">Some database types have facets that modify how the data is stored.</span></span> <span data-ttu-id="1bf6f-285">這些包括：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-285">These include:</span></span>

* <span data-ttu-id="1bf6f-286">小數和日期/時間資料行的精確度和小數位數</span><span class="sxs-lookup"><span data-stu-id="1bf6f-286">Precision and scale for decimals and date/time columns</span></span>
* <span data-ttu-id="1bf6f-287">二進位和字串資料行的大小/長度</span><span class="sxs-lookup"><span data-stu-id="1bf6f-287">Size/length for binary and string columns</span></span>
* <span data-ttu-id="1bf6f-288">字串資料行的 Unicode</span><span class="sxs-lookup"><span data-stu-id="1bf6f-288">Unicode for string columns</span></span>

<span data-ttu-id="1bf6f-289">您可以使用值轉換器，以一般方式設定這些 facet，並且將套用至轉換的資料庫類型。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-289">These facets can be configured in the normal way for a property that uses a value converter, and will apply to the converted database type.</span></span> <span data-ttu-id="1bf6f-290">例如，從列舉轉換成字串時，可以將資料庫資料行指定為非 Unicode，並儲存最多20個字元：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-290">For example, when converting from an enum to strings, we can specify that the database column should be non-Unicode and store up to 20 characters:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion<string>()
                    .HasMaxLength(20)
                    .IsUnicode(false);
            }
-->
[!code-csharp[ConversionByClrTypeWithFacets](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByClrTypeWithFacets)]

<span data-ttu-id="1bf6f-291">或者，明確建立轉換器時：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-291">Or, when creating the converter explicitly:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new ValueConverter<EquineBeast, string>(
                    v => v.ToString(),
                    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion(converter)
                    .HasMaxLength(20)
                    .IsUnicode(false);
            }
-->
[!code-csharp[ConversionByConverterInstanceWithFacets](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByConverterInstanceWithFacets)]

<span data-ttu-id="1bf6f-292">這會在 `varchar(20)` 使用 EF Core 的 SQL Server 遷移時產生資料行：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-292">This results in a `varchar(20)` column when using EF Core migrations against SQL Server:</span></span>

```sql
CREATE TABLE [Rider] (
    [Id] int NOT NULL IDENTITY,
    [Mount] varchar(20) NOT NULL,
    CONSTRAINT [PK_Rider] PRIMARY KEY ([Id]));
```

<span data-ttu-id="1bf6f-293">但是，如果依預設，所有資料 `EquineBeast` 行都應該是 `varchar(20)` ，則可以將這項資訊提供給值轉換器，做為 <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ConverterMappingHints> 。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-293">However, if by default all `EquineBeast` columns should be `varchar(20)`, then this information can be given to the value converter as a <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ConverterMappingHints>.</span></span> <span data-ttu-id="1bf6f-294">例如：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-294">For example:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new ValueConverter<EquineBeast, string>(
                    v => v.ToString(),
                    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v),
                    new ConverterMappingHints(size: 20, unicode: false));

                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion(converter);
            }
-->
[!code-csharp[ConversionByConverterInstanceWithMappingHints](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByConverterInstanceWithMappingHints)]

<span data-ttu-id="1bf6f-295">現在，每當使用這個轉換器時，資料庫資料行將是非 unicode，最大長度為20。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-295">Now any time this converter is used, the database column will be non-unicode with a max length of 20.</span></span> <span data-ttu-id="1bf6f-296">但是，這些只是提示，因為已在對應屬性上明確設定的任何 facet 覆寫這些提示。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-296">However, these are only hints since they are be overridden by any facets explicitly set on the mapped property.</span></span>

## <a name="examples"></a><span data-ttu-id="1bf6f-297">範例</span><span class="sxs-lookup"><span data-stu-id="1bf6f-297">Examples</span></span>

### <a name="simple-value-objects"></a><span data-ttu-id="1bf6f-298">簡單值物件</span><span class="sxs-lookup"><span data-stu-id="1bf6f-298">Simple value objects</span></span>

<span data-ttu-id="1bf6f-299">這個範例會使用簡單類型來包裝基本型別。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-299">This example uses a simple type to wrap a primitive type.</span></span> <span data-ttu-id="1bf6f-300">當您想要讓模型中的型別更明確 (，因此更具型別安全的) 而不是基本型別時，這個方法就很有用。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-300">This can be useful when you want the type in your model to be more specific (and hence more type-safe) than a primitive type.</span></span> <span data-ttu-id="1bf6f-301">在此範例中，該類型是 `Dollars` ，它會包裝小數基本：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-301">In this example, that type is `Dollars`, which wraps the decimal primitive:</span></span>

<!--
        public readonly struct Dollars
        {
            public Dollars(decimal amount) 
                => Amount = amount;
            
            public decimal Amount { get; }

            public override string ToString() 
                => $"${Amount}";
        }
-->
[!code-csharp[SimpleValueObject](../../../samples/core/Modeling/ValueConversions/SimpleValueObject.cs?name=SimpleValueObject)]

<span data-ttu-id="1bf6f-302">這可用於實體類型：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-302">This can be used in an entity type:</span></span>

<!--
        public class Order
        {
            public int Id { get; set; }

            public Dollars Price { get; set; }
        }
-->
[!code-csharp[SimpleValueObjectModel](../../../samples/core/Modeling/ValueConversions/SimpleValueObject.cs?name=SimpleValueObjectModel)]

<span data-ttu-id="1bf6f-303">`decimal`儲存在資料庫中時，會轉換為基礎：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-303">And converted to the underlying `decimal` when stored in the database:</span></span>

<!--
                modelBuilder.Entity<Order>()
                    .Property(e => e.Price)
                    .HasConversion(
                        v => v.Amount,
                        v => new Dollars(v));
-->
[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/SimpleValueObject.cs?name=ConfigureImmutableStructProperty)]

> [!NOTE]
> <span data-ttu-id="1bf6f-304">此值物件會實作為 [唯讀結構](/dotnet/csharp/language-reference/builtin-types/struct)。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-304">This value object is implemented as a [readonly struct](/dotnet/csharp/language-reference/builtin-types/struct).</span></span> <span data-ttu-id="1bf6f-305">這表示 EF Core 可以在不發生問題的情況下，快照並比較值。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-305">This means that EF Core can snapshot and compare values without issue.</span></span> <span data-ttu-id="1bf6f-306">如需詳細資訊，請參閱 [值](xref:core/modeling/value-comparers) 比較子。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-306">See [Value Comparers](xref:core/modeling/value-comparers) for more information.</span></span>

### <a name="composite-value-objects"></a><span data-ttu-id="1bf6f-307">複合值物件</span><span class="sxs-lookup"><span data-stu-id="1bf6f-307">Composite value objects</span></span>

<span data-ttu-id="1bf6f-308">在上述範例中，值物件類型只包含單一屬性。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-308">In the previous example, the value object type contained only a single property.</span></span> <span data-ttu-id="1bf6f-309">值物件類型比較常見的方式，是撰寫多個同時形成領域概念的屬性。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-309">It is more common for a value object type to compose multiple properties that together form a domain concept.</span></span> <span data-ttu-id="1bf6f-310">例如， `Money` 包含數量和貨幣的一般類型：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-310">For example, a general `Money` type that contains both the amount and the currency:</span></span>

<!--
        public readonly struct Money
        {
            [JsonConstructor]
            public Money(decimal amount, Currency currency)
            {
                Amount = amount;
                Currency = currency;
            }

            public override string ToString()
                => (Currency == Currency.UsDollars ? "$" : "£") + Amount;

            public decimal Amount { get; }
            public Currency Currency { get; }
        }

        public enum Currency
        {
            UsDollars,
            PoundsStirling
        }
-->
[!code-csharp[CompositeValueObject](../../../samples/core/Modeling/ValueConversions/CompositeValueObject.cs?name=CompositeValueObject)]

<span data-ttu-id="1bf6f-311">此值物件可以像之前一樣用在實體類型中：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-311">This value object can be used in an entity type as before:</span></span>

<!--
        public class Order
        {
            public int Id { get; set; }

            public Money Price { get; set; }
        }
-->
[!code-csharp[CompositeValueObjectModel](../../../samples/core/Modeling/ValueConversions/CompositeValueObject.cs?name=CompositeValueObjectModel)]

<span data-ttu-id="1bf6f-312">值轉換器目前只能將值轉換成單一資料庫資料行的值。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-312">Value converters can currently only convert values to and from a single database column.</span></span> <span data-ttu-id="1bf6f-313">這項限制表示必須將物件中的所有屬性值編碼成單一資料行值。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-313">This limitation means that all property values from the object must be encoded into a single column value.</span></span> <span data-ttu-id="1bf6f-314">這通常是藉由在物件進入資料庫時進行序列化，然後再重新還原序列化的方式來處理。例如，使用 <xref:System.Text.Json> ：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-314">This is typically handled by serializing the object as it goes into the database, and then deserializing it again on the way out. For example, using <xref:System.Text.Json>:</span></span>

<!--
                modelBuilder.Entity<Order>()
                    .Property(e => e.Price)
                    .HasConversion(
                        v => JsonSerializer.Serialize(v, null),
                        v => JsonSerializer.Deserialize<Money>(v, null));
-->
[!code-csharp[ConfigureCompositeValueObject](../../../samples/core/Modeling/ValueConversions/CompositeValueObject.cs?name=ConfigureCompositeValueObject)]

> [!NOTE]
> <span data-ttu-id="1bf6f-315">我們計畫允許將物件對應至 EF Core 6.0 中的多個資料行，而不需要在此使用序列化。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-315">We plan to allow mapping an object to multiple columns in EF Core 6.0, removing the need to use serialization here.</span></span> <span data-ttu-id="1bf6f-316">這是 [GitHub 問題 #13947](https://github.com/dotnet/efcore/issues/13947)所追蹤。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-316">This is tracked by [GitHub issue #13947](https://github.com/dotnet/efcore/issues/13947).</span></span>

> [!NOTE]
> <span data-ttu-id="1bf6f-317">如同上述範例，這個值物件會實作為 [唯讀結構](/dotnet/csharp/language-reference/builtin-types/struct)。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-317">As with the previous example, this value object is implemented as a [readonly struct](/dotnet/csharp/language-reference/builtin-types/struct).</span></span> <span data-ttu-id="1bf6f-318">這表示 EF Core 可以在不發生問題的情況下，快照並比較值。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-318">This means that EF Core can snapshot and compare values without issue.</span></span> <span data-ttu-id="1bf6f-319">如需詳細資訊，請參閱 [值](xref:core/modeling/value-comparers) 比較子。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-319">See [Value Comparers](xref:core/modeling/value-comparers) for more information.</span></span>

### <a name="collections-of-primitives"></a><span data-ttu-id="1bf6f-320">基本類型的集合</span><span class="sxs-lookup"><span data-stu-id="1bf6f-320">Collections of primitives</span></span>

<span data-ttu-id="1bf6f-321">序列化也可以用來儲存基本值的集合。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-321">Serialization can also be used to store a collection of primitive values.</span></span> <span data-ttu-id="1bf6f-322">例如：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-322">For example:</span></span>

<!--
        public class Post
        {
            public int Id { get; set; }
            public string Title { get; set; }
            public string Contents { get; set; }

            public ICollection<string> Tags { get; set; }
        }
-->
[!code-csharp[PrimitiveCollectionModel](../../../samples/core/Modeling/ValueConversions/PrimitiveCollection.cs?name=PrimitiveCollectionModel)]

<span data-ttu-id="1bf6f-323"><xref:System.Text.Json>再次使用：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-323">Using <xref:System.Text.Json> again:</span></span>

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.Tags)
                    .HasConversion(
                        v => JsonSerializer.Serialize(v, null),
                        v => JsonSerializer.Deserialize<List<string>>(v, null),
                        new ValueComparer<ICollection<string>>(
                            (c1, c2) => c1.SequenceEqual(c2),
                            c => c.Aggregate(0, (a, v) => HashCode.Combine(a, v.GetHashCode())),
                            c => (ICollection<string>)c.ToList()));
-->
[!code-csharp[ConfigurePrimitiveCollection](../../../samples/core/Modeling/ValueConversions/PrimitiveCollection.cs?name=ConfigurePrimitiveCollection)]

<span data-ttu-id="1bf6f-324">`ICollection<string>` 表示可變的參考型別。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-324">`ICollection<string>` represents a mutable reference type.</span></span> <span data-ttu-id="1bf6f-325">這表示需要 a， <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> 以便 EF Core 可以正確地追蹤和偵測變更。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-325">This means that a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> is needed so that EF Core can track and detect changes correctly.</span></span> <span data-ttu-id="1bf6f-326">如需詳細資訊，請參閱 [值](xref:core/modeling/value-comparers) 比較子。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-326">See [Value Comparers](xref:core/modeling/value-comparers) for more information.</span></span>

### <a name="collections-of-value-objects"></a><span data-ttu-id="1bf6f-327">值物件的集合</span><span class="sxs-lookup"><span data-stu-id="1bf6f-327">Collections of value objects</span></span>

<span data-ttu-id="1bf6f-328">結合上述兩個範例，我們可以建立值物件的集合。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-328">Combining the previous two examples together we can create a collection of value objects.</span></span> <span data-ttu-id="1bf6f-329">例如，假設有一 `AnnualFinance` 種類型可將一年的 blog 財務模型：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-329">For example, consider an `AnnualFinance` type that models blog finances for a single year:</span></span>

<!--
        public readonly struct AnnualFinance
        {
            [JsonConstructor]
            public AnnualFinance(int year, Money income, Money expenses)
            {
                Year = year;
                Income = income;
                Expenses = expenses;
            }

            public int Year { get; }
            public Money Income { get; }
            public Money Expenses { get; }
            public Money Revenue => new Money(Income.Amount - Expenses.Amount, Income.Currency);
        }
-->
[!code-csharp[ValueObjectCollection](../../../samples/core/Modeling/ValueConversions/ValueObjectCollection.cs?name=ValueObjectCollection)]

<span data-ttu-id="1bf6f-330">此類型會撰寫 `Money` 我們先前建立的數種類型：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-330">This type composes several of the `Money` types we created previously:</span></span>

<!--
        public readonly struct Money
        {
            [JsonConstructor]
            public Money(decimal amount, Currency currency)
            {
                Amount = amount;
                Currency = currency;
            }

            public override string ToString()
                => (Currency == Currency.UsDollars ? "$" : "£") + Amount;

            public decimal Amount { get; }
            public Currency Currency { get; }
        }

        public enum Currency
        {
            UsDollars,
            PoundsStirling
        }
-->
[!code-csharp[ValueObjectCollectionMoney](../../../samples/core/Modeling/ValueConversions/ValueObjectCollection.cs?name=ValueObjectCollectionMoney)]

<span data-ttu-id="1bf6f-331">接著，我們可以將的集合新增 `AnnualFinance` 至我們的實體類型：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-331">We can then add a collection of `AnnualFinance` to our entity type:</span></span>

<!--
        public class Blog
        {
            public int Id { get; set; }
            public string Name { get; set; }
            
            public IList<AnnualFinance> Finances { get; set; }
        }
-->
[!code-csharp[ValueObjectCollectionModel](../../../samples/core/Modeling/ValueConversions/ValueObjectCollection.cs?name=ValueObjectCollectionModel)]

<span data-ttu-id="1bf6f-332">然後再次使用序列化來儲存此：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-332">And again use serialization to store this:</span></span>

<!--
                modelBuilder.Entity<Blog>()
                    .Property(e => e.Finances)
                    .HasConversion(
                        v => JsonSerializer.Serialize(v, null),
                        v => JsonSerializer.Deserialize<List<AnnualFinance>>(v, null),
                        new ValueComparer<IList<AnnualFinance>>(
                            (c1, c2) => c1.SequenceEqual(c2),
                            c => c.Aggregate(0, (a, v) => HashCode.Combine(a, v.GetHashCode())),
                            c => (IList<AnnualFinance>)c.ToList()));
-->
[!code-csharp[ConfigureValueObjectCollection](../../../samples/core/Modeling/ValueConversions/ValueObjectCollection.cs?name=ConfigureValueObjectCollection)]

> [!NOTE]
> <span data-ttu-id="1bf6f-333">同樣地，這種轉換需要 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> 。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-333">As before, this conversion requires a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601>.</span></span> <span data-ttu-id="1bf6f-334">如需詳細資訊，請參閱 [值](xref:core/modeling/value-comparers) 比較子。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-334">See [Value Comparers](xref:core/modeling/value-comparers) for more information.</span></span>

### <a name="value-objects-as-keys"></a><span data-ttu-id="1bf6f-335">值物件做為索引鍵</span><span class="sxs-lookup"><span data-stu-id="1bf6f-335">Value objects as keys</span></span>

<span data-ttu-id="1bf6f-336">有時基本索引鍵屬性可以包裝在值物件中，以在指派值時加入額外的型別安全層級。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-336">Sometimes primitive key properties may be wrapped in value objects to add an additional level of type-safety in assigning values.</span></span> <span data-ttu-id="1bf6f-337">例如，我們可以針對 blog 和 post 的金鑰類型來執行金鑰類型：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-337">For example, we could implement a key type for blogs, and a key type for posts:</span></span>

<!--
        public readonly struct BlogKey
        {
            public BlogKey(int id) => Id = id;
            public int Id { get; }
        }

        public readonly struct PostKey
        {
            public PostKey(int id) => Id = id;
            public int Id { get; }
        }
-->
[!code-csharp[KeyValueObjects](../../../samples/core/Modeling/ValueConversions/KeyValueObjects.cs?name=KeyValueObjects)]

<span data-ttu-id="1bf6f-338">然後，您可以在網域模型中使用這些功能：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-338">These can then be used in the domain model:</span></span>

<!--
        public class Blog
        {
            public BlogKey Id { get; set; }
            public string Name { get; set; }

            public ICollection<Post> Posts { get; set; }
        }

        public class Post
        {
            public PostKey Id { get; set; }

            public string Title { get; set; }
            public string Content { get; set; }

            public BlogKey? BlogId { get; set; }
            public Blog Blog { get; set; }
        }
-->
[!code-csharp[KeyValueObjectsModel](../../../samples/core/Modeling/ValueConversions/KeyValueObjects.cs?name=KeyValueObjectsModel)]

<span data-ttu-id="1bf6f-339">請注意， `Blog.Id` 不會不慎指派 `PostKey` ，且 `Post.Id` 不會不慎指派給 `BlogKey` 。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-339">Notice that `Blog.Id` cannot accidentally be assigned a `PostKey`, and `Post.Id` cannot accidentally be assigned a `BlogKey`.</span></span> <span data-ttu-id="1bf6f-340">同樣地， `Post.BlogId` 必須將外鍵屬性指派給 `BlogKey` 。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-340">Similarly, the `Post.BlogId` foreign key property must be assigned a `BlogKey`.</span></span>

> [!NOTE]
> <span data-ttu-id="1bf6f-341">顯示此模式並不表示我們建議您這樣做。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-341">Showing this pattern does not mean we recommend it.</span></span> <span data-ttu-id="1bf6f-342">請仔細考慮這種抽象層級是否有助於或阻礙您的開發體驗。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-342">Carefully consider whether this level of abstraction is helping or hampering your development experience.</span></span> <span data-ttu-id="1bf6f-343">此外，請考慮使用導覽和產生的金鑰，而不是直接處理索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-343">Also, consider using navigations and generated keys instead of dealing with key values directly.</span></span>

<span data-ttu-id="1bf6f-344">然後可以使用值轉換器來對應這些索引鍵屬性：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-344">These key properties can then be mapped using value converters:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var blogKeyConverter = new ValueConverter<BlogKey, int>(
                    v => v.Id,
                    v => new BlogKey(v));

                modelBuilder.Entity<Blog>().Property(e => e.Id).HasConversion(blogKeyConverter);

                modelBuilder.Entity<Post>(
                    b =>
                        {
                            b.Property(e => e.Id).HasConversion(v => v.Id, v => new PostKey(v));
                            b.Property(e => e.BlogId).HasConversion(blogKeyConverter);
                        });
            }
-->
[!code-csharp[ConfigureKeyValueObjects](../../../samples/core/Modeling/ValueConversions/KeyValueObjects.cs?name=ConfigureKeyValueObjects)]

> [!NOTE]
> <span data-ttu-id="1bf6f-345">目前具有轉換的索引鍵屬性無法使用產生的索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-345">Currently key properties with conversions cannot use generated key values.</span></span> <span data-ttu-id="1bf6f-346">投票給 [GitHub 問題 #11597](https://github.com/dotnet/efcore/issues/11597) 移除此限制。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-346">Vote for [GitHub issue #11597](https://github.com/dotnet/efcore/issues/11597) to have this limitation removed.</span></span>

### <a name="use-ulong-for-timestamprowversion"></a><span data-ttu-id="1bf6f-347">針對 timestamp/rowversion 使用 ulong</span><span class="sxs-lookup"><span data-stu-id="1bf6f-347">Use ulong for timestamp/rowversion</span></span>

<span data-ttu-id="1bf6f-348">SQL Server 支援使用[8 位元組二進位資料 `rowversion` / `timestamp` 行](/sql/t-sql/data-types/rowversion-transact-sql)的自動[開放式並行](xref:core/saving/concurrency)存取。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-348">SQL Server supports automatic [optimistic concurrency](xref:core/saving/concurrency) using [8-byte binary `rowversion`/`timestamp` columns](/sql/t-sql/data-types/rowversion-transact-sql).</span></span> <span data-ttu-id="1bf6f-349">這些一律會使用8位元組陣列，從資料庫讀取和寫入。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-349">These are always read from and written to the database using an 8-byte array.</span></span> <span data-ttu-id="1bf6f-350">不過，位元組陣列是可變動的參考型別，可讓它們有點難以處理。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-350">However, byte arrays are a mutable reference type, which makes them somewhat painful to deal with.</span></span> <span data-ttu-id="1bf6f-351">值轉換器允許 `rowversion` 改為對應至 `ulong` 屬性，這比位元組陣列更適合且容易使用。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-351">Value converters allow the `rowversion` to instead be mapped to a `ulong` property, which is much more appropriate and easy to use than the byte array.</span></span> <span data-ttu-id="1bf6f-352">例如，假設有一個 `Blog` 具有 ulong 並行存取權杖的實體：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-352">For example, consider a `Blog` entity with a ulong concurrency token:</span></span>

<!--
        public class Blog
        {
            public int Id { get; set; }
            public string Name { get; set; }
            public ulong Version { get; set; }
        }
-->
[!code-csharp[ULongConcurrencyModel](../../../samples/core/Modeling/ValueConversions/ULongConcurrency.cs?name=ULongConcurrencyModel)]

<span data-ttu-id="1bf6f-353">這可以使用值轉換器對應到 SQL server 資料 `rowversion` 行：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-353">This can be mapped to a SQL server `rowversion` column using a value converter:</span></span>

<!--
                modelBuilder.Entity<Blog>()
                    .Property(e => e.Version)
                    .IsRowVersion()
                    .HasConversion<byte[]>();
-->
[!code-csharp[ConfigureULongConcurrency](../../../samples/core/Modeling/ValueConversions/ULongConcurrency.cs?name=ConfigureULongConcurrency)]

### <a name="specify-the-datetimekind-when-reading-dates"></a><span data-ttu-id="1bf6f-354">指定讀取日期時的日期時間類型</span><span class="sxs-lookup"><span data-stu-id="1bf6f-354">Specify the DateTime.Kind when reading dates</span></span>

<span data-ttu-id="1bf6f-355"><xref:System.DateTime.Kind%2A?displayProperty=nameWithType>當將儲存為或時，SQL Server 會捨棄旗標 <xref:System.DateTime> [`datetime`](/sql/t-sql/data-types/datetime-transact-sql) [`datetime2`](/sql/t-sql/data-types/datetime2-transact-sql) 。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-355">SQL Server discards the <xref:System.DateTime.Kind%2A?displayProperty=nameWithType> flag when storing a <xref:System.DateTime> as a [`datetime`](/sql/t-sql/data-types/datetime-transact-sql) or [`datetime2`](/sql/t-sql/data-types/datetime2-transact-sql).</span></span> <span data-ttu-id="1bf6f-356">這表示從資料庫傳回的日期時間值一律為 <xref:System.DateTimeKind> 的 `Unspecified` 。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-356">This means that DateTime values coming back from the database always have a <xref:System.DateTimeKind> of `Unspecified`.</span></span>

<span data-ttu-id="1bf6f-357">值轉換器可以用兩種方式來處理此情況。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-357">Value converters can be used in two ways to deal with this.</span></span> <span data-ttu-id="1bf6f-358">首先，EF Core 有一個值轉換器，會建立8個位元組的不透明值，以保留 `Kind` 旗標。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-358">First, EF Core has a value converter that creates an 8-byte opaque value which preserves the `Kind` flag.</span></span> <span data-ttu-id="1bf6f-359">例如：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-359">For example:</span></span>

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.PostedOn)
                    .HasConversion<long>();
-->
[!code-csharp[ConfigurePreserveDateTimeKind1](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind1)]

<span data-ttu-id="1bf6f-360">這可讓具有不同旗標的 DateTime 值 `Kind` 在資料庫中混合。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-360">This allows DateTime values with different `Kind` flags to be mixed in the database.</span></span>

<span data-ttu-id="1bf6f-361">這種方法的問題在於資料庫不再具有可辨識的 `datetime` 或資料 `datetime2` 行。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-361">The problem with this approach is that the database no longer has recognizable `datetime` or `datetime2` columns.</span></span> <span data-ttu-id="1bf6f-362">因此，通常一律會將 UTC 時間儲存 (或較不常的當地時間) ，然後忽略旗標， `Kind` 或使用值轉換器將它設定為適當的值。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-362">So instead it is common to always store UTC time (or, less commonly, always local time) and then either ignore the `Kind` flag or set it to the appropriate value using a value converter.</span></span> <span data-ttu-id="1bf6f-363">例如，下列轉換器可確保 `DateTime` 從資料庫讀取的值會有 <xref:System.DateTimeKind> `UTC` ：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-363">For example, the converter below ensures that the `DateTime` value read from the database will have the <xref:System.DateTimeKind> `UTC`:</span></span>

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.LastUpdated)
                    .HasConversion(
                        v => v,
                        v => new DateTime(v.Ticks, DateTimeKind.Utc));
-->
[!code-csharp[ConfigurePreserveDateTimeKind2](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind2)]

<span data-ttu-id="1bf6f-364">如果在實體實例中設定本機和 UTC 值的混合，則可以使用轉換器，在插入之前適當地進行轉換。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-364">If a mix of local and UTC values are being set in entity instances, then the converter can be used to convert appropriately before inserting.</span></span> <span data-ttu-id="1bf6f-365">例如：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-365">For example:</span></span>

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.LastUpdated)
                    .HasConversion(
                        v => v.ToUniversalTime(),
                        v => new DateTime(v.Ticks, DateTimeKind.Utc));
-->
[!code-csharp[ConfigurePreserveDateTimeKind3](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind3)]

> [!NOTE]
> <span data-ttu-id="1bf6f-366">請仔細考慮將所有資料庫存取程式碼統一，以隨時使用 UTC 時間，只在向使用者呈現資料時處理當地時間。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-366">Carefully consider unifying all database access code to use UTC time all the time, only dealing with local time when presenting data to users.</span></span>

### <a name="use-case-insensitive-string-keys"></a><span data-ttu-id="1bf6f-367">使用不區分大小寫的字串索引鍵</span><span class="sxs-lookup"><span data-stu-id="1bf6f-367">Use case-insensitive string keys</span></span>

<span data-ttu-id="1bf6f-368">某些資料庫（包括 SQL Server）預設會執行不區分大小寫的字串比較。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-368">Some databases, including SQL Server, perform case-insensitive string comparisons by default.</span></span> <span data-ttu-id="1bf6f-369">相反地，.NET 預設會執行區分大小寫的字串比較。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-369">.NET, on the other hand, performs case-sensitive string comparisons by default.</span></span> <span data-ttu-id="1bf6f-370">這表示外鍵值（例如 "DotNet"）會符合 SQL Server 上的主鍵值 "DotNet"，但不會在 EF Core 中符合。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-370">This means that a foreign key value like "DotNet" will match the primary key value "dotnet" on SQL Server, but will not match it in EF Core.</span></span> <span data-ttu-id="1bf6f-371">索引鍵的值比較子可以用來強制 EF Core 不區分大小寫的字串比較，例如資料庫。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-371">A value comparer for keys can be used to force EF Core into case-insensitive string comparisons like in the database.</span></span> <span data-ttu-id="1bf6f-372">例如，假設有一個具有字串索引鍵的 blog/post 模型：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-372">For example, consider a blog/posts model with string keys:</span></span>

<!--
        public class Blog
        {
            public string Id { get; set; }
            public string Name { get; set; }

            public ICollection<Post> Posts { get; set; }
        }

        public class Post
        {
            public string Id { get; set; }
            public string Title { get; set; }
            public string Content { get; set; }

            public string BlogId { get; set; }
            public Blog Blog { get; set; }
        }
-->
[!code-csharp[CaseInsensitiveStringsModel](../../../samples/core/Modeling/ValueConversions/CaseInsensitiveStrings.cs?name=CaseInsensitiveStringsModel)]

<span data-ttu-id="1bf6f-373">如果某些 `Post.BlogId` 值有不同的大小寫，這將無法如預期般運作。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-373">This will not work as expected if some of the `Post.BlogId` values have different casing.</span></span> <span data-ttu-id="1bf6f-374">造成的錯誤取決於應用程式的執行情況，但通常會包含未正確 [修正](xref:core/change-tracking/relationship-changes) 之物件的圖表，以及/或因為 FK 值錯誤而失敗的更新。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-374">The errors caused by this will depend on what the application is doing, but typically involve graphs of objects that are not [fixed-up](xref:core/change-tracking/relationship-changes) correctly, and/or updates that fail because the FK value is wrong.</span></span> <span data-ttu-id="1bf6f-375">您可以使用值比較子來修正這個錯誤：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-375">A value comparer can be used to correct this:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var comparer = new ValueComparer<string>(
                    (l, r) => string.Equals(l, r, StringComparison.OrdinalIgnoreCase),
                    v => v.ToUpper().GetHashCode(),
                    v => v);

                modelBuilder.Entity<Blog>()
                    .Property(e => e.Id)
                    .Metadata.SetValueComparer(comparer);

                modelBuilder.Entity<Post>(
                    b =>
                        {
                            b.Property(e => e.Id).Metadata.SetValueComparer(comparer);
                            b.Property(e => e.BlogId).Metadata.SetValueComparer(comparer);
                        });
            }
-->
[!code-csharp[ConfigureCaseInsensitiveStrings](../../../samples/core/Modeling/ValueConversions/CaseInsensitiveStrings.cs?name=ConfigureCaseInsensitiveStrings)]

> [!NOTE]
> <span data-ttu-id="1bf6f-376">.NET 字串比較和資料庫字串比較在大小寫上可能不同。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-376">.NET string comparisons and database string comparisons can differ in more than just case sensitivity.</span></span> <span data-ttu-id="1bf6f-377">這種模式適用于簡單的 ASCII 索引鍵，但對於具有任何特定文化特性字元的金鑰，可能會失敗。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-377">This pattern works for simple ASCII keys, but may fail for keys with any kind of culture-specific characters.</span></span> <span data-ttu-id="1bf6f-378">如需詳細資訊，請參閱定序 [和區分大小寫](xref:core/miscellaneous/collations-and-case-sensitivity) 。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-378">See [Collations and Case Sensitivity](xref:core/miscellaneous/collations-and-case-sensitivity) for more information.</span></span>

### <a name="handle-fixed-length-database-strings"></a><span data-ttu-id="1bf6f-379">處理固定長度的資料庫字串</span><span class="sxs-lookup"><span data-stu-id="1bf6f-379">Handle fixed-length database strings</span></span>

<span data-ttu-id="1bf6f-380">上一個範例不需要值轉換器。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-380">The previous example did not need a value converter.</span></span> <span data-ttu-id="1bf6f-381">不過，轉換器對於固定長度的資料庫字串類型（例如或）很 `char(20)` 有用 `nchar(20)` 。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-381">However, a converter can be useful for fixed-length database string types like `char(20)` or `nchar(20)`.</span></span> <span data-ttu-id="1bf6f-382">只要將值插入資料庫中，固定長度的字串就會填補到其完整長度。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-382">Fixed-length strings are padded to their full length whenever a value is inserted into the database.</span></span> <span data-ttu-id="1bf6f-383">這表示會將 "" 的索引鍵值 `dotnet` 從資料庫讀回為 " `dotnet..............` "，其中 `.` 代表空白字元。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-383">This means that a key value of "`dotnet`" will be read back from the database as "`dotnet..............`", where `.` represents a space character.</span></span> <span data-ttu-id="1bf6f-384">這樣就不會與未填補的索引鍵值正確地比較。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-384">This will then not compare correctly with key values that are not padded.</span></span>

<span data-ttu-id="1bf6f-385">值轉換子可用來在讀取索引鍵值時修剪填補。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-385">A value converter can be used to trim the padding when reading key values.</span></span> <span data-ttu-id="1bf6f-386">這可以與上一個範例中的值比較子結合，以正確地比較固定長度不區分大小寫的 ASCII 金鑰。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-386">This can be combined with the value comparer in the previous example to compare fixed length case-insensitive ASCII keys correctly.</span></span> <span data-ttu-id="1bf6f-387">例如：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-387">For example:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new ValueConverter<string, string>(
                    v => v,
                    v => v.Trim());
                
                var comparer = new ValueComparer<string>(
                    (l, r) => string.Equals(l, r, StringComparison.OrdinalIgnoreCase),
                    v => v.ToUpper().GetHashCode(),
                    v => v);

                modelBuilder.Entity<Blog>()
                    .Property(e => e.Id)
                    .HasColumnType("char(20)")
                    .HasConversion(converter, comparer);

                modelBuilder.Entity<Post>(
                    b =>
                        {
                            b.Property(e => e.Id).HasColumnType("char(20)").HasConversion(converter, comparer);
                            b.Property(e => e.BlogId).HasColumnType("char(20)").HasConversion(converter, comparer);
                        });
            }
-->
[!code-csharp[ConfigureFixedLengthStrings](../../../samples/core/Modeling/ValueConversions/FixedLengthStrings.cs?name=ConfigureFixedLengthStrings)]

### <a name="encrypt-property-values"></a><span data-ttu-id="1bf6f-388">加密屬性值</span><span class="sxs-lookup"><span data-stu-id="1bf6f-388">Encrypt property values</span></span>

<span data-ttu-id="1bf6f-389">值轉換器可以在將屬性值傳送至資料庫之前用來加密，然後將其解密。例如，使用字串反轉作為實際加密演算法的替代方式：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-389">Value converters can be used to encrypt property values before sending them to the database, and then decrypt them on the way out. For example, using string reversal as a substitute for a real encryption algorithm:</span></span>

<!--
                modelBuilder.Entity<User>().Property(e => e.Password).HasConversion(
                    v => new string(v.Reverse().ToArray()),
                    v => new string(v.Reverse().ToArray()));
-->
[!code-csharp[ConfigureEncryptPropertyValues](../../../samples/core/Modeling/ValueConversions/EncryptPropertyValues.cs?name=ConfigureEncryptPropertyValues)]

> [!NOTE]
> <span data-ttu-id="1bf6f-390">目前沒有任何方法可從值轉換器內取得目前 DbCoNtext 或其他會話狀態的參考。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-390">There is currently no way to get a reference to the current DbContext, or other session state, from within a value converter.</span></span> <span data-ttu-id="1bf6f-391">這會限制可使用的加密類型。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-391">This limits the kinds of encryption that can be used.</span></span> <span data-ttu-id="1bf6f-392">投票給 [GitHub 問題 #11597](https://github.com/dotnet/efcore/issues/12205) 移除此限制。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-392">Vote for [GitHub issue #11597](https://github.com/dotnet/efcore/issues/12205) to have this limitation removed.</span></span>

> [!WARNING]
> <span data-ttu-id="1bf6f-393">如果您變換自己的加密以保護敏感性資料，請務必瞭解所有的含意。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-393">Make sure to understand all the implications if you roll your own encryption to protect sensitive data.</span></span> <span data-ttu-id="1bf6f-394">請考慮改為使用預先建立的加密機制，例如 SQL Server 上的 [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) 。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-394">Consider instead using pre-built encryption mechanisms, such as [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) on SQL Server.</span></span>

## <a name="limitations"></a><span data-ttu-id="1bf6f-395">限制</span><span class="sxs-lookup"><span data-stu-id="1bf6f-395">Limitations</span></span>

<span data-ttu-id="1bf6f-396">值轉換系統有一些已知的目前限制：</span><span class="sxs-lookup"><span data-stu-id="1bf6f-396">There are a few known current limitations of the value conversion system:</span></span>

* <span data-ttu-id="1bf6f-397">目前沒有任何方法可以在一個地方指定指定類型的每個屬性都必須使用相同的值轉換器。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-397">There is currently no way to specify in one place that every property of a given type must use the same value converter.</span></span> <span data-ttu-id="1bf6f-398">👍如果這是您所需的內容，請針對[GitHub #10784 問題](https://github.com/dotnet/efcore/issues/10784) () 投票。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-398">Please vote (👍) for [GitHub issue #10784](https://github.com/dotnet/efcore/issues/10784) if this is something you need.</span></span>
* <span data-ttu-id="1bf6f-399">如上所述， `null` 無法轉換。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-399">As noted above, `null` cannot be converted.</span></span> <span data-ttu-id="1bf6f-400">👍如果這是您所需的內容，請針對[GitHub #13850 問題](https://github.com/dotnet/efcore/issues/13850) () 投票。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-400">Please vote (👍) for [GitHub issue #13850](https://github.com/dotnet/efcore/issues/13850) if this is something you need.</span></span>
* <span data-ttu-id="1bf6f-401">目前沒有任何方法可將某個屬性的轉換散佈至多個資料行，反之亦然。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-401">There is currently no way to spread a conversion of one property to multiple columns or vice-versa.</span></span> <span data-ttu-id="1bf6f-402">👍如果這是您所需的內容，請針對[GitHub #13947 問題](https://github.com/dotnet/efcore/issues/13947) () 投票。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-402">Please vote (👍) for [GitHub issue #13947](https://github.com/dotnet/efcore/issues/13947) if this is something you need.</span></span>
* <span data-ttu-id="1bf6f-403">透過值轉換器對應的大部分索引鍵不支援值產生。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-403">Value generation is not supported for most keys mapped through value converters.</span></span> <span data-ttu-id="1bf6f-404">👍如果這是您所需的內容，請針對[GitHub #11597 問題](https://github.com/dotnet/efcore/issues/11597) () 投票。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-404">Please vote (👍) for [GitHub issue #11597](https://github.com/dotnet/efcore/issues/11597) if this is something you need.</span></span>
* <span data-ttu-id="1bf6f-405">值轉換無法參考目前的 DbCoNtext 實例。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-405">Value conversions cannot reference the current DbContext instance.</span></span> <span data-ttu-id="1bf6f-406">👍如果這是您所需的內容，請針對[GitHub #11597 問題](https://github.com/dotnet/efcore/issues/12205) () 投票。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-406">Please vote (👍) for [GitHub issue #11597](https://github.com/dotnet/efcore/issues/12205) if this is something you need.</span></span>

<span data-ttu-id="1bf6f-407">未來的版本將會考慮移除這些限制。</span><span class="sxs-lookup"><span data-stu-id="1bf6f-407">Removal of these limitations is being considered for future releases.</span></span>
