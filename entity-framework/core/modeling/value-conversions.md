---
title: 值轉換-EF Core
description: 在 Entity Framework Core 模型中設定值轉換器
author: ajcvickers
ms.date: 02/19/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/value-conversions
ms.openlocfilehash: 79e54392bf5503b4b651f25ce6e5fc63d418df90
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616661"
---
# <a name="value-conversions"></a><span data-ttu-id="3f1d7-103">值轉換</span><span class="sxs-lookup"><span data-stu-id="3f1d7-103">Value Conversions</span></span>

> [!NOTE]  
> <span data-ttu-id="3f1d7-104">此功能是 EF Core 2.1 中的新功能。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-104">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="3f1d7-105">值轉換器可在讀取或寫入資料庫時，允許轉換屬性值。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-105">Value converters allow property values to be converted when reading from or writing to the database.</span></span> <span data-ttu-id="3f1d7-106">這項轉換可從某個值轉換為相同類型的另一個值 (例如，將字串) 或從某個類型的值加密為另一個類型的值 (例如，在資料庫中來回轉換字串的列舉值。 ) </span><span class="sxs-lookup"><span data-stu-id="3f1d7-106">This conversion can be from one value to another of the same type (for example, encrypting strings) or from a value of one type to a value of another type (for example, converting enum values to and from strings in the database.)</span></span>

## <a name="fundamentals"></a><span data-ttu-id="3f1d7-107">基礎</span><span class="sxs-lookup"><span data-stu-id="3f1d7-107">Fundamentals</span></span>

<span data-ttu-id="3f1d7-108">值轉換器的指定方式為 `ModelClrType` 和 `ProviderClrType` 。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-108">Value converters are specified in terms of a `ModelClrType` and a `ProviderClrType`.</span></span> <span data-ttu-id="3f1d7-109">模型類型是實體類型中屬性的 .NET 類型。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-109">The model type is the .NET type of the property in the entity type.</span></span> <span data-ttu-id="3f1d7-110">提供者類型是資料庫提供者所瞭解的 .NET 型別。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-110">The provider type is the .NET type understood by the database provider.</span></span> <span data-ttu-id="3f1d7-111">例如，若要將列舉儲存為資料庫中的字串，模型類型為列舉的型別，而提供者類型為 `String` 。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-111">For example, to save enums as strings in the database, the model type is the type of the enum, and the provider type is `String`.</span></span> <span data-ttu-id="3f1d7-112">這兩種類型可以相同。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-112">These two types can be the same.</span></span>

<span data-ttu-id="3f1d7-113">轉換會使用兩個 `Func` 運算式樹狀架構來定義：一個從 `ModelClrType` 到 `ProviderClrType` ，另一個從轉換 `ProviderClrType` 為 `ModelClrType` 。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-113">Conversions are defined using two `Func` expression trees: one from `ModelClrType` to `ProviderClrType` and the other from `ProviderClrType` to `ModelClrType`.</span></span> <span data-ttu-id="3f1d7-114">使用運算式樹狀架構可將它們編譯成資料庫存取程式碼，以進行有效率的轉換。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-114">Expression trees are used so that they can be compiled into the database access code for efficient conversions.</span></span> <span data-ttu-id="3f1d7-115">針對複雜的轉換，運算式樹狀架構可能是對執行轉換的方法進行簡單的呼叫。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-115">For complex conversions, the expression tree may be a simple call to a method that performs the conversion.</span></span>

## <a name="configuring-a-value-converter"></a><span data-ttu-id="3f1d7-116">設定值轉換器</span><span class="sxs-lookup"><span data-stu-id="3f1d7-116">Configuring a value converter</span></span>

<span data-ttu-id="3f1d7-117">值轉換是在的屬性中定義的 `OnModelCreating` `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-117">Value conversions are defined on properties in the `OnModelCreating` of your `DbContext`.</span></span> <span data-ttu-id="3f1d7-118">例如，假設列舉和實體型別定義為：</span><span class="sxs-lookup"><span data-stu-id="3f1d7-118">For example, consider an enum and entity type defined as:</span></span>

``` csharp
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
```

<span data-ttu-id="3f1d7-119">然後，您可以在中定義轉換， `OnModelCreating` 以將列舉值儲存為字串 (例如，"Donkey"、"Mule"、... ) 在資料庫中：</span><span class="sxs-lookup"><span data-stu-id="3f1d7-119">Then conversions can be defined in `OnModelCreating` to store the enum values as strings (for example, "Donkey", "Mule", ...) in the database:</span></span>

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Rider>()
        .Property(e => e.Mount)
        .HasConversion(
            v => v.ToString(),
            v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));
}
```

> [!NOTE]  
> <span data-ttu-id="3f1d7-120">`null`永遠不會將值傳遞至值轉換器。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-120">A `null` value will never be passed to a value converter.</span></span> <span data-ttu-id="3f1d7-121">這可讓轉換的執行變得更容易，並可讓它們在可為 null 且不可為 null 的屬性之間共用。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-121">This makes the implementation of conversions easier and allows them to be shared amongst nullable and non-nullable properties.</span></span>

## <a name="the-valueconverter-class"></a><span data-ttu-id="3f1d7-122">ValueConverter 類別</span><span class="sxs-lookup"><span data-stu-id="3f1d7-122">The ValueConverter class</span></span>

<span data-ttu-id="3f1d7-123">`HasConversion`如上面所示呼叫會建立 `ValueConverter` 實例，並在屬性上設定它。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-123">Calling `HasConversion` as shown above will create a `ValueConverter` instance and set it on the property.</span></span> <span data-ttu-id="3f1d7-124">`ValueConverter`可以改為明確建立。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-124">The `ValueConverter` can instead be created explicitly.</span></span> <span data-ttu-id="3f1d7-125">例如：</span><span class="sxs-lookup"><span data-stu-id="3f1d7-125">For example:</span></span>

``` csharp
var converter = new ValueConverter<EquineBeast, string>(
    v => v.ToString(),
    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

<span data-ttu-id="3f1d7-126">當多個屬性使用相同的轉換時，這會很有用。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-126">This can be useful when multiple properties use the same conversion.</span></span>

> [!NOTE]  
> <span data-ttu-id="3f1d7-127">目前沒有任何方法可以在一個地方指定指定類型的每個屬性都必須使用相同的值轉換器。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-127">There is currently no way to specify in one place that every property of a given type must use the same value converter.</span></span> <span data-ttu-id="3f1d7-128">未來的版本將會考慮這項功能。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-128">This feature will be considered for a future release.</span></span>

## <a name="built-in-converters"></a><span data-ttu-id="3f1d7-129">內建轉換器</span><span class="sxs-lookup"><span data-stu-id="3f1d7-129">Built-in converters</span></span>

<span data-ttu-id="3f1d7-130">EF Core 隨附一組預先定義的 `ValueConverter` 類別，可在 `Microsoft.EntityFrameworkCore.Storage.ValueConversion` 命名空間中找到。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-130">EF Core ships with a set of pre-defined `ValueConverter` classes, found in the `Microsoft.EntityFrameworkCore.Storage.ValueConversion` namespace.</span></span> <span data-ttu-id="3f1d7-131">這些警告是：</span><span class="sxs-lookup"><span data-stu-id="3f1d7-131">These are:</span></span>

* <span data-ttu-id="3f1d7-132">`BoolToZeroOneConverter` -Bool 至零和1</span><span class="sxs-lookup"><span data-stu-id="3f1d7-132">`BoolToZeroOneConverter` - Bool to zero and one</span></span>
* <span data-ttu-id="3f1d7-133">`BoolToStringConverter` -Bool 至字串，例如 "Y" 和 "N"</span><span class="sxs-lookup"><span data-stu-id="3f1d7-133">`BoolToStringConverter` - Bool to strings such as "Y" and "N"</span></span>
* <span data-ttu-id="3f1d7-134">`BoolToTwoValuesConverter` -Bool 至任何兩個值</span><span class="sxs-lookup"><span data-stu-id="3f1d7-134">`BoolToTwoValuesConverter` - Bool to any two values</span></span>
* <span data-ttu-id="3f1d7-135">`BytesToStringConverter` -位元組陣列至 Base64 編碼的字串</span><span class="sxs-lookup"><span data-stu-id="3f1d7-135">`BytesToStringConverter` - Byte array to Base64-encoded string</span></span>
* <span data-ttu-id="3f1d7-136">`CastingConverter` -只需要型別轉換的轉換</span><span class="sxs-lookup"><span data-stu-id="3f1d7-136">`CastingConverter` - Conversions that require only a type cast</span></span>
* <span data-ttu-id="3f1d7-137">`CharToStringConverter` -Char 至單一字元字串</span><span class="sxs-lookup"><span data-stu-id="3f1d7-137">`CharToStringConverter` - Char to single character string</span></span>
* <span data-ttu-id="3f1d7-138">`DateTimeOffsetToBinaryConverter` -DateTimeOffset 至二進位編碼的64位值</span><span class="sxs-lookup"><span data-stu-id="3f1d7-138">`DateTimeOffsetToBinaryConverter` - DateTimeOffset to binary-encoded 64-bit value</span></span>
* <span data-ttu-id="3f1d7-139">`DateTimeOffsetToBytesConverter` -DateTimeOffset 至位元組陣列</span><span class="sxs-lookup"><span data-stu-id="3f1d7-139">`DateTimeOffsetToBytesConverter` - DateTimeOffset to byte array</span></span>
* <span data-ttu-id="3f1d7-140">`DateTimeOffsetToStringConverter` -DateTimeOffset 至字串</span><span class="sxs-lookup"><span data-stu-id="3f1d7-140">`DateTimeOffsetToStringConverter` - DateTimeOffset to string</span></span>
* <span data-ttu-id="3f1d7-141">`DateTimeToBinaryConverter` -DateTime 至64位值，包括 >datetimekind.unspecified</span><span class="sxs-lookup"><span data-stu-id="3f1d7-141">`DateTimeToBinaryConverter` - DateTime to 64-bit value including DateTimeKind</span></span>
* <span data-ttu-id="3f1d7-142">`DateTimeToStringConverter` -DateTime 至字串</span><span class="sxs-lookup"><span data-stu-id="3f1d7-142">`DateTimeToStringConverter` - DateTime to string</span></span>
* <span data-ttu-id="3f1d7-143">`DateTimeToTicksConverter` -DateTime 至刻度</span><span class="sxs-lookup"><span data-stu-id="3f1d7-143">`DateTimeToTicksConverter` - DateTime to ticks</span></span>
* <span data-ttu-id="3f1d7-144">`EnumToNumberConverter` -對基礎數位的列舉</span><span class="sxs-lookup"><span data-stu-id="3f1d7-144">`EnumToNumberConverter` - Enum to underlying number</span></span>
* <span data-ttu-id="3f1d7-145">`EnumToStringConverter` -列舉至字串</span><span class="sxs-lookup"><span data-stu-id="3f1d7-145">`EnumToStringConverter` - Enum to string</span></span>
* <span data-ttu-id="3f1d7-146">`GuidToBytesConverter` -位元組陣列的 Guid</span><span class="sxs-lookup"><span data-stu-id="3f1d7-146">`GuidToBytesConverter` - Guid to byte array</span></span>
* <span data-ttu-id="3f1d7-147">`GuidToStringConverter` -字串的 Guid</span><span class="sxs-lookup"><span data-stu-id="3f1d7-147">`GuidToStringConverter` - Guid to string</span></span>
* <span data-ttu-id="3f1d7-148">`NumberToBytesConverter` -位元組陣列的任何數值</span><span class="sxs-lookup"><span data-stu-id="3f1d7-148">`NumberToBytesConverter` - Any numerical value to byte array</span></span>
* <span data-ttu-id="3f1d7-149">`NumberToStringConverter` -字串的任何數值</span><span class="sxs-lookup"><span data-stu-id="3f1d7-149">`NumberToStringConverter` - Any numerical value to string</span></span>
* <span data-ttu-id="3f1d7-150">`StringToBytesConverter` -字串至 UTF8 位元組</span><span class="sxs-lookup"><span data-stu-id="3f1d7-150">`StringToBytesConverter` - String to UTF8 bytes</span></span>
* <span data-ttu-id="3f1d7-151">`TimeSpanToStringConverter` -TimeSpan 至字串</span><span class="sxs-lookup"><span data-stu-id="3f1d7-151">`TimeSpanToStringConverter` - TimeSpan to string</span></span>
* <span data-ttu-id="3f1d7-152">`TimeSpanToTicksConverter` -TimeSpan 至刻度</span><span class="sxs-lookup"><span data-stu-id="3f1d7-152">`TimeSpanToTicksConverter` - TimeSpan to ticks</span></span>

<span data-ttu-id="3f1d7-153">請注意， `EnumToStringConverter` 包含在此清單中。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-153">Notice that `EnumToStringConverter` is included in this list.</span></span> <span data-ttu-id="3f1d7-154">這表示不需要明確指定轉換，如上所示。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-154">This means that there is no need to specify the conversion explicitly, as shown above.</span></span> <span data-ttu-id="3f1d7-155">相反地，只使用內建的轉換器：</span><span class="sxs-lookup"><span data-stu-id="3f1d7-155">Instead, just use the built-in converter:</span></span>

``` csharp
var converter = new EnumToStringConverter<EquineBeast>();

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

<span data-ttu-id="3f1d7-156">請注意，所有內建的轉換器都是無狀態的，因此單一實例可以由多個屬性安全地共用。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-156">Note that all the built-in converters are stateless and so a single instance can be safely shared by multiple properties.</span></span>

## <a name="pre-defined-conversions"></a><span data-ttu-id="3f1d7-157">預先定義的轉換</span><span class="sxs-lookup"><span data-stu-id="3f1d7-157">Pre-defined conversions</span></span>

<span data-ttu-id="3f1d7-158">針對內建轉換器存在的一般轉換，不需要明確指定轉換器。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-158">For common conversions for which a built-in converter exists there is no need to specify the converter explicitly.</span></span> <span data-ttu-id="3f1d7-159">相反地，只需設定應該使用的提供者類型，EF 就會自動使用適當的內建轉換器。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-159">Instead, just configure which provider type should be used and EF will automatically use the appropriate built-in converter.</span></span> <span data-ttu-id="3f1d7-160">列舉至字串轉換是以上述範例的形式使用，但如果已設定提供者類型，EF 將會自動執行這項作業：</span><span class="sxs-lookup"><span data-stu-id="3f1d7-160">Enum to string conversions are used as an example above, but EF will actually do this automatically if the provider type is configured:</span></span>

``` csharp
modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion<string>();
```

<span data-ttu-id="3f1d7-161">您可以明確指定資料行類型來達成相同的目的。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-161">The same thing can be achieved by explicitly specifying the column type.</span></span> <span data-ttu-id="3f1d7-162">例如，如果實體類型定義如下：</span><span class="sxs-lookup"><span data-stu-id="3f1d7-162">For example, if the entity type is defined like so:</span></span>

``` csharp
public class Rider
{
    public int Id { get; set; }

    [Column(TypeName = "nvarchar(24)")]
    public EquineBeast Mount { get; set; }
}
```

<span data-ttu-id="3f1d7-163">然後，列舉值會以字串的形式儲存在資料庫中，而不需要在中進行任何進一步的設定 `OnModelCreating` 。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-163">Then the enum values will be saved as strings in the database without any further configuration in `OnModelCreating`.</span></span>

## <a name="limitations"></a><span data-ttu-id="3f1d7-164">限制</span><span class="sxs-lookup"><span data-stu-id="3f1d7-164">Limitations</span></span>

<span data-ttu-id="3f1d7-165">值轉換系統有一些已知的目前限制：</span><span class="sxs-lookup"><span data-stu-id="3f1d7-165">There are a few known current limitations of the value conversion system:</span></span>

* <span data-ttu-id="3f1d7-166">如上所述， `null` 無法轉換。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-166">As noted above, `null` cannot be converted.</span></span>
* <span data-ttu-id="3f1d7-167">目前沒有任何方法可將某個屬性的轉換散佈至多個資料行，反之亦然。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-167">There is currently no way to spread a conversion of one property to multiple columns or vice-versa.</span></span>
* <span data-ttu-id="3f1d7-168">使用值轉換可能會影響 EF Core 將運算式轉譯為 SQL 的能力。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-168">Use of value conversions may impact the ability of EF Core to translate expressions to SQL.</span></span> <span data-ttu-id="3f1d7-169">將會記錄這類案例的警告。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-169">A warning will be logged for such cases.</span></span>
<span data-ttu-id="3f1d7-170">未來的版本將會考慮移除這些限制。</span><span class="sxs-lookup"><span data-stu-id="3f1d7-170">Removal of these limitations is being considered for a future release.</span></span>
