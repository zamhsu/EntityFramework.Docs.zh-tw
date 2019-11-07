---
title: 值轉換-EF Core
author: ajcvickers
ms.date: 02/19/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/value-conversions
ms.openlocfilehash: 93774bc1bc3887f982faeac151825a6643c1107c
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73654787"
---
# <a name="value-conversions"></a><span data-ttu-id="36d0d-102">值轉換</span><span class="sxs-lookup"><span data-stu-id="36d0d-102">Value Conversions</span></span>

> [!NOTE]  
> <span data-ttu-id="36d0d-103">此功能是 EF Core 2.1 中的新功能。</span><span class="sxs-lookup"><span data-stu-id="36d0d-103">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="36d0d-104">值轉換器可在讀取或寫入資料庫時，允許轉換屬性值。</span><span class="sxs-lookup"><span data-stu-id="36d0d-104">Value converters allow property values to be converted when reading from or writing to the database.</span></span> <span data-ttu-id="36d0d-105">這項轉換可以是從某個值到另一個類型（例如，將字串加密），或從某個型別的值轉換成另一個類型的值（例如，將列舉值轉換成資料庫中的字串，以及將其從資料庫中的一個或多個）。</span><span class="sxs-lookup"><span data-stu-id="36d0d-105">This conversion can be from one value to another of the same type (for example, encrypting strings) or from a value of one type to a value of another type (for example, converting enum values to and from strings in the database.)</span></span>

## <a name="fundamentals"></a><span data-ttu-id="36d0d-106">Fundamentals</span><span class="sxs-lookup"><span data-stu-id="36d0d-106">Fundamentals</span></span>

<span data-ttu-id="36d0d-107">值轉換器是以 `ModelClrType` 和 `ProviderClrType`的角度來指定。</span><span class="sxs-lookup"><span data-stu-id="36d0d-107">Value converters are specified in terms of a `ModelClrType` and a `ProviderClrType`.</span></span> <span data-ttu-id="36d0d-108">模型類型是實體類型中屬性的 .NET 類型。</span><span class="sxs-lookup"><span data-stu-id="36d0d-108">The model type is the .NET type of the property in the entity type.</span></span> <span data-ttu-id="36d0d-109">提供者類型是資料庫提供者所瞭解的 .NET 類型。</span><span class="sxs-lookup"><span data-stu-id="36d0d-109">The provider type is the .NET type understood by the database provider.</span></span> <span data-ttu-id="36d0d-110">例如，若要將列舉當做字串儲存在資料庫中，模型類型就是 enum 的類型，而提供者類型是 `String`。</span><span class="sxs-lookup"><span data-stu-id="36d0d-110">For example, to save enums as strings in the database, the model type is the type of the enum, and the provider type is `String`.</span></span> <span data-ttu-id="36d0d-111">這兩種類型可以相同。</span><span class="sxs-lookup"><span data-stu-id="36d0d-111">These two types can be the same.</span></span>

<span data-ttu-id="36d0d-112">轉換是使用兩個 `Func` 運算式樹狀架構所定義：一個從 `ModelClrType` 到 `ProviderClrType`，另一個則是從 `ProviderClrType` 到 `ModelClrType`。</span><span class="sxs-lookup"><span data-stu-id="36d0d-112">Conversions are defined using two `Func` expression trees: one from `ModelClrType` to `ProviderClrType` and the other from `ProviderClrType` to `ModelClrType`.</span></span> <span data-ttu-id="36d0d-113">使用運算式樹狀架構，讓它們可以編譯成資料庫存取程式碼，以進行有效率的轉換。</span><span class="sxs-lookup"><span data-stu-id="36d0d-113">Expression trees are used so that they can be compiled into the database access code for efficient conversions.</span></span> <span data-ttu-id="36d0d-114">針對複雜的轉換，運算式樹狀架構可能是簡單的呼叫方法來執行轉換。</span><span class="sxs-lookup"><span data-stu-id="36d0d-114">For complex conversions, the expression tree may be a simple call to a method that performs the conversion.</span></span>

## <a name="configuring-a-value-converter"></a><span data-ttu-id="36d0d-115">設定值轉換器</span><span class="sxs-lookup"><span data-stu-id="36d0d-115">Configuring a value converter</span></span>

<span data-ttu-id="36d0d-116">值轉換會定義在 `DbContext`之 `OnModelCreating` 的屬性上。</span><span class="sxs-lookup"><span data-stu-id="36d0d-116">Value conversions are defined on properties in the `OnModelCreating` of your `DbContext`.</span></span> <span data-ttu-id="36d0d-117">例如，假設列舉和實體類型定義為：</span><span class="sxs-lookup"><span data-stu-id="36d0d-117">For example, consider an enum and entity type defined as:</span></span>

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

<span data-ttu-id="36d0d-118">然後，可以在 `OnModelCreating` 中定義轉換，將列舉值儲存為資料庫中的字串（例如，"Donkey"、"Mule"、...）：</span><span class="sxs-lookup"><span data-stu-id="36d0d-118">Then conversions can be defined in `OnModelCreating` to store the enum values as strings (for example, "Donkey", "Mule", ...) in the database:</span></span>

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
> <span data-ttu-id="36d0d-119">`null` 值永遠不會傳遞至值轉換器。</span><span class="sxs-lookup"><span data-stu-id="36d0d-119">A `null` value will never be passed to a value converter.</span></span> <span data-ttu-id="36d0d-120">這可讓轉換的執行變得更容易，並可讓它們在可為 null 和不可為 null 的屬性之間共用。</span><span class="sxs-lookup"><span data-stu-id="36d0d-120">This makes the implementation of conversions easier and allows them to be shared amongst nullable and non-nullable properties.</span></span>

## <a name="the-valueconverter-class"></a><span data-ttu-id="36d0d-121">ValueConverter 類別</span><span class="sxs-lookup"><span data-stu-id="36d0d-121">The ValueConverter class</span></span>

<span data-ttu-id="36d0d-122">如上面所示呼叫 `HasConversion` 將會建立 `ValueConverter` 實例，並在屬性上設定它。</span><span class="sxs-lookup"><span data-stu-id="36d0d-122">Calling `HasConversion` as shown above will create a `ValueConverter` instance and set it on the property.</span></span> <span data-ttu-id="36d0d-123">您可以改為明確建立 `ValueConverter`。</span><span class="sxs-lookup"><span data-stu-id="36d0d-123">The `ValueConverter` can instead be created explicitly.</span></span> <span data-ttu-id="36d0d-124">例如:</span><span class="sxs-lookup"><span data-stu-id="36d0d-124">For example:</span></span>

``` csharp
var converter = new ValueConverter<EquineBeast, string>(
    v => v.ToString(),
    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

<span data-ttu-id="36d0d-125">當多個屬性使用相同的轉換時，這會很有用。</span><span class="sxs-lookup"><span data-stu-id="36d0d-125">This can be useful when multiple properties use the same conversion.</span></span>

> [!NOTE]  
> <span data-ttu-id="36d0d-126">目前沒有任何方法可以在一個位置指定指定類型的每個屬性都必須使用相同的值轉換器。</span><span class="sxs-lookup"><span data-stu-id="36d0d-126">There is currently no way to specify in one place that every property of a given type must use the same value converter.</span></span> <span data-ttu-id="36d0d-127">未來的版本將會考慮這項功能。</span><span class="sxs-lookup"><span data-stu-id="36d0d-127">This feature will be considered for a future release.</span></span>

## <a name="built-in-converters"></a><span data-ttu-id="36d0d-128">內建轉換器</span><span class="sxs-lookup"><span data-stu-id="36d0d-128">Built-in converters</span></span>

<span data-ttu-id="36d0d-129">EF Core 隨附一組預先定義的 `ValueConverter` 類別，在 `Microsoft.EntityFrameworkCore.Storage.ValueConversion` 命名空間中找到。</span><span class="sxs-lookup"><span data-stu-id="36d0d-129">EF Core ships with a set of pre-defined `ValueConverter` classes, found in the `Microsoft.EntityFrameworkCore.Storage.ValueConversion` namespace.</span></span> <span data-ttu-id="36d0d-130">這些是：</span><span class="sxs-lookup"><span data-stu-id="36d0d-130">These are:</span></span>

* <span data-ttu-id="36d0d-131">`BoolToZeroOneConverter`-Bool 到零和一個</span><span class="sxs-lookup"><span data-stu-id="36d0d-131">`BoolToZeroOneConverter` - Bool to zero and one</span></span>
* <span data-ttu-id="36d0d-132">`BoolToStringConverter`-Bool 到字串（例如 "Y" 和 "N"）</span><span class="sxs-lookup"><span data-stu-id="36d0d-132">`BoolToStringConverter` - Bool to strings such as "Y" and "N"</span></span>
* <span data-ttu-id="36d0d-133">`BoolToTwoValuesConverter`-Bool 到任何兩個值</span><span class="sxs-lookup"><span data-stu-id="36d0d-133">`BoolToTwoValuesConverter` - Bool to any two values</span></span>
* <span data-ttu-id="36d0d-134">`BytesToStringConverter` 位元組陣列到 Base64 編碼的字串</span><span class="sxs-lookup"><span data-stu-id="36d0d-134">`BytesToStringConverter` - Byte array to Base64-encoded string</span></span>
* <span data-ttu-id="36d0d-135">只需要類型轉換的 `CastingConverter` 轉換</span><span class="sxs-lookup"><span data-stu-id="36d0d-135">`CastingConverter` - Conversions that require only a type cast</span></span>
* <span data-ttu-id="36d0d-136">`CharToStringConverter`-Char 至單一字元字串</span><span class="sxs-lookup"><span data-stu-id="36d0d-136">`CharToStringConverter` - Char to single character string</span></span>
* <span data-ttu-id="36d0d-137">`DateTimeOffsetToBinaryConverter`-DateTimeOffset 到二進位編碼的64位值</span><span class="sxs-lookup"><span data-stu-id="36d0d-137">`DateTimeOffsetToBinaryConverter` - DateTimeOffset to binary-encoded 64-bit value</span></span>
* <span data-ttu-id="36d0d-138">`DateTimeOffsetToBytesConverter`-DateTimeOffset 到位元組陣列</span><span class="sxs-lookup"><span data-stu-id="36d0d-138">`DateTimeOffsetToBytesConverter` - DateTimeOffset to byte array</span></span>
* <span data-ttu-id="36d0d-139">`DateTimeOffsetToStringConverter`-DateTimeOffset 到字串</span><span class="sxs-lookup"><span data-stu-id="36d0d-139">`DateTimeOffsetToStringConverter` - DateTimeOffset to string</span></span>
* <span data-ttu-id="36d0d-140">`DateTimeToBinaryConverter`-DateTime 至64位值，包括 Datetimekind.utc</span><span class="sxs-lookup"><span data-stu-id="36d0d-140">`DateTimeToBinaryConverter` - DateTime to 64-bit value including DateTimeKind</span></span>
* <span data-ttu-id="36d0d-141">`DateTimeToStringConverter`-日期時間到字串</span><span class="sxs-lookup"><span data-stu-id="36d0d-141">`DateTimeToStringConverter` - DateTime to string</span></span>
* <span data-ttu-id="36d0d-142">`DateTimeToTicksConverter`-DateTime 到刻度</span><span class="sxs-lookup"><span data-stu-id="36d0d-142">`DateTimeToTicksConverter` - DateTime to ticks</span></span>
* <span data-ttu-id="36d0d-143">`EnumToNumberConverter`-列舉到基礎號碼</span><span class="sxs-lookup"><span data-stu-id="36d0d-143">`EnumToNumberConverter` - Enum to underlying number</span></span>
* <span data-ttu-id="36d0d-144">`EnumToStringConverter`-列舉為字串</span><span class="sxs-lookup"><span data-stu-id="36d0d-144">`EnumToStringConverter` - Enum to string</span></span>
* <span data-ttu-id="36d0d-145">`GuidToBytesConverter`-Guid 到位元組陣列</span><span class="sxs-lookup"><span data-stu-id="36d0d-145">`GuidToBytesConverter` - Guid to byte array</span></span>
* <span data-ttu-id="36d0d-146">`GuidToStringConverter`-Guid 至字串</span><span class="sxs-lookup"><span data-stu-id="36d0d-146">`GuidToStringConverter` - Guid to string</span></span>
* <span data-ttu-id="36d0d-147">`NumberToBytesConverter`-位元組陣列的任何數值</span><span class="sxs-lookup"><span data-stu-id="36d0d-147">`NumberToBytesConverter` - Any numerical value to byte array</span></span>
* <span data-ttu-id="36d0d-148">`NumberToStringConverter`-字串的任何數值</span><span class="sxs-lookup"><span data-stu-id="36d0d-148">`NumberToStringConverter` - Any numerical value to string</span></span>
* <span data-ttu-id="36d0d-149">`StringToBytesConverter`-字串到 UTF8 位元組</span><span class="sxs-lookup"><span data-stu-id="36d0d-149">`StringToBytesConverter` - String to UTF8 bytes</span></span>
* <span data-ttu-id="36d0d-150">`TimeSpanToStringConverter`-TimeSpan 到字串</span><span class="sxs-lookup"><span data-stu-id="36d0d-150">`TimeSpanToStringConverter` - TimeSpan to string</span></span>
* <span data-ttu-id="36d0d-151">`TimeSpanToTicksConverter`-TimeSpan 到刻度</span><span class="sxs-lookup"><span data-stu-id="36d0d-151">`TimeSpanToTicksConverter` - TimeSpan to ticks</span></span>

<span data-ttu-id="36d0d-152">請注意，`EnumToStringConverter` 包含在這份清單中。</span><span class="sxs-lookup"><span data-stu-id="36d0d-152">Notice that `EnumToStringConverter` is included in this list.</span></span> <span data-ttu-id="36d0d-153">這表示不需要明確指定轉換，如上所示。</span><span class="sxs-lookup"><span data-stu-id="36d0d-153">This means that there is no need to specify the conversion explicitly, as shown above.</span></span> <span data-ttu-id="36d0d-154">相反地，只要使用內建的轉換器：</span><span class="sxs-lookup"><span data-stu-id="36d0d-154">Instead, just use the built-in converter:</span></span>

``` csharp
var converter = new EnumToStringConverter<EquineBeast>();

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

<span data-ttu-id="36d0d-155">請注意，所有內建的轉換器都是無狀態的，因此單一實例可以安全地由多個屬性共用。</span><span class="sxs-lookup"><span data-stu-id="36d0d-155">Note that all the built-in converters are stateless and so a single instance can be safely shared by multiple properties.</span></span>

## <a name="pre-defined-conversions"></a><span data-ttu-id="36d0d-156">預先定義的轉換</span><span class="sxs-lookup"><span data-stu-id="36d0d-156">Pre-defined conversions</span></span>

<span data-ttu-id="36d0d-157">如果是內建轉換器所在的一般轉換，則不需要明確指定轉換器。</span><span class="sxs-lookup"><span data-stu-id="36d0d-157">For common conversions for which a built-in converter exists there is no need to specify the converter explicitly.</span></span> <span data-ttu-id="36d0d-158">相反地，只需設定應使用的提供者類型，EF 就會自動使用適當的內建轉換器。</span><span class="sxs-lookup"><span data-stu-id="36d0d-158">Instead, just configure which provider type should be used and EF will automatically use the appropriate built-in converter.</span></span> <span data-ttu-id="36d0d-159">列舉到字串轉換是用來做為範例，但如果已設定提供者類型，則 EF 會自動執行此動作：</span><span class="sxs-lookup"><span data-stu-id="36d0d-159">Enum to string conversions are used as an example above, but EF will actually do this automatically if the provider type is configured:</span></span>

``` csharp
modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion<string>();
```

<span data-ttu-id="36d0d-160">也可以藉由明確地指定資料行類型來達到相同的目的。</span><span class="sxs-lookup"><span data-stu-id="36d0d-160">The same thing can be achieved by explicitly specifying the column type.</span></span> <span data-ttu-id="36d0d-161">例如，如果實體類型的定義如下：</span><span class="sxs-lookup"><span data-stu-id="36d0d-161">For example, if the entity type is defined like so:</span></span>

``` csharp
public class Rider
{
    public int Id { get; set; }

    [Column(TypeName = "nvarchar(24)")]
    public EquineBeast Mount { get; set; }
}
```

<span data-ttu-id="36d0d-162">然後，列舉值會以字串的形式儲存在資料庫中，而不會在 `OnModelCreating`中進行進一步的設定。</span><span class="sxs-lookup"><span data-stu-id="36d0d-162">Then the enum values will be saved as strings in the database without any further configuration in `OnModelCreating`.</span></span>

## <a name="limitations"></a><span data-ttu-id="36d0d-163">限制</span><span class="sxs-lookup"><span data-stu-id="36d0d-163">Limitations</span></span>

<span data-ttu-id="36d0d-164">值轉換系統有幾個已知的目前限制：</span><span class="sxs-lookup"><span data-stu-id="36d0d-164">There are a few known current limitations of the value conversion system:</span></span>

* <span data-ttu-id="36d0d-165">如先前所述，`null` 無法轉換。</span><span class="sxs-lookup"><span data-stu-id="36d0d-165">As noted above, `null` cannot be converted.</span></span>
* <span data-ttu-id="36d0d-166">目前沒有任何方法可將一個屬性的轉換散佈到多個資料行，反之亦然。</span><span class="sxs-lookup"><span data-stu-id="36d0d-166">There is currently no way to spread a conversion of one property to multiple columns or vice-versa.</span></span>
* <span data-ttu-id="36d0d-167">使用值轉換可能會影響 EF Core 將運算式轉譯為 SQL 的能力。</span><span class="sxs-lookup"><span data-stu-id="36d0d-167">Use of value conversions may impact the ability of EF Core to translate expressions to SQL.</span></span> <span data-ttu-id="36d0d-168">這類情況會記錄一則警告。</span><span class="sxs-lookup"><span data-stu-id="36d0d-168">A warning will be logged for such cases.</span></span>
<span data-ttu-id="36d0d-169">未來的版本將會考慮移除這些限制。</span><span class="sxs-lookup"><span data-stu-id="36d0d-169">Removal of these limitations is being considered for a future release.</span></span>
