---
title: "值轉換為 EF 核心"
author: ajcvickers
ms.author: divega
ms.date: 02/19/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
ms.technology: entity-framework-core
uid: core/modeling/value-conversions
ms.openlocfilehash: 50acba39cdec16caa9300fcaf47ab6242a4f69fb
ms.sourcegitcommit: b2d94cebdc32edad4fecb07e53fece66437d1b04
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="value-conversions"></a><span data-ttu-id="fbe0c-102">值的轉換</span><span class="sxs-lookup"><span data-stu-id="fbe0c-102">Value Conversions</span></span>

> [!NOTE]  
> <span data-ttu-id="fbe0c-103">這項功能是在 EF 核心 2.1 中新功能。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-103">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="fbe0c-104">值轉換器可讓讀取或寫入資料庫時要轉換的屬性值。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-104">Value converters allow property values to be converted when reading from or writing to the database.</span></span> <span data-ttu-id="fbe0c-105">這項轉換可以從另一個相同類型 （例如，加密的字串） 的一個值或值的一種類型的值，另一個類型 （例如，轉換列舉的值與資料庫中的字串）。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-105">This conversion can be from one value to another of the same type (for example, encrypting strings) or from a value of one type to a value of another type (for example, converting enum values to and from strings in the database.)</span></span>

## <a name="fundamentals"></a><span data-ttu-id="fbe0c-106">Fundamentals</span><span class="sxs-lookup"><span data-stu-id="fbe0c-106">Fundamentals</span></span>

<span data-ttu-id="fbe0c-107">指定的值轉換器`ModelClrType`和`ProviderClrType`。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-107">Value converters are specified in terms of a `ModelClrType` and a `ProviderClrType`.</span></span> <span data-ttu-id="fbe0c-108">模型類型是屬性的.NET 型別中的實體類型。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-108">The model type is the .NET type of the property in the entity type.</span></span> <span data-ttu-id="fbe0c-109">提供者類型為資料庫提供者所了解的.NET 類型。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-109">The provider type is the .NET type understood by the database provider.</span></span> <span data-ttu-id="fbe0c-110">例如，將列舉儲存成資料庫中的字串，模型型別是列舉的類型和提供者類型是`String`。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-110">For example, to save enums as strings in the database, the model type is the type of the enum, and the provider type is `String`.</span></span> <span data-ttu-id="fbe0c-111">這兩種類型可以是相同。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-111">These two types can be the same.</span></span>

<span data-ttu-id="fbe0c-112">轉換會使用兩個定義`Func`運算式樹狀架構： 從`ModelClrType`至`ProviderClrType`和從其他`ProviderClrType`至`ModelClrType`。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-112">Conversions are defined using two `Func` expression trees: one from `ModelClrType` to `ProviderClrType` and the other from `ProviderClrType` to `ModelClrType`.</span></span> <span data-ttu-id="fbe0c-113">運算式樹狀架構會使用，因此，它們可以編譯成資料庫存取程式碼的有效轉換。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-113">Expression trees are used so that they can be compiled into the database access code for efficient conversions.</span></span> <span data-ttu-id="fbe0c-114">針對複雜的轉換，運算式樹狀架構可能是一個簡單的呼叫會執行轉換的方法。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-114">For complex conversions, the expression tree may be a simple call to a method that performs the conversion.</span></span>

## <a name="configuring-a-value-converter"></a><span data-ttu-id="fbe0c-115">設定的值轉換器</span><span class="sxs-lookup"><span data-stu-id="fbe0c-115">Configuring a value converter</span></span>

<span data-ttu-id="fbe0c-116">中的程式 DbContext OnModelCreating 屬性未定義值的轉換。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-116">Value conversions are defined on properties in the OnModelCreating of your DbContext.</span></span> <span data-ttu-id="fbe0c-117">例如，考慮列舉和實體類型定義為：</span><span class="sxs-lookup"><span data-stu-id="fbe0c-117">For example, consider an enum and entity type defined as:</span></span>
```Csharp
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
<span data-ttu-id="fbe0c-118">然後 OnModelCreating （例如，儲存為字串的列舉值中可以定義轉換「 Donkey"，"騾子"，...)在資料庫中：</span><span class="sxs-lookup"><span data-stu-id="fbe0c-118">Then conversions can be defined in OnModelCreating to store the enum values as strings (e.g. "Donkey", "Mule", ...) in the database:</span></span>
```Csharp
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
> <span data-ttu-id="fbe0c-119">A`null`值絕不會傳遞至值轉換器。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-119">A `null` value will never be passed to a value converter.</span></span> <span data-ttu-id="fbe0c-120">這會使您更輕鬆的轉換實作，並讓他們在可為 null 與不可為 null 的屬性之間會共用。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-120">This makes the implementation of conversions easier and allows them to be shared amongst nullable and non-nullable properties.</span></span>

## <a name="the-valueconverter-class"></a><span data-ttu-id="fbe0c-121">ValueConverter 類別</span><span class="sxs-lookup"><span data-stu-id="fbe0c-121">The ValueConverter class</span></span>

<span data-ttu-id="fbe0c-122">呼叫`HasConversion`如上所示將建立`ValueConverter`執行個體，並將其設定的屬性。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-122">Calling `HasConversion` as shown above will create a `ValueConverter` instance and set it on the property.</span></span> <span data-ttu-id="fbe0c-123">`ValueConverter`改為明確建立。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-123">The `ValueConverter` can instead be created explicitly.</span></span> <span data-ttu-id="fbe0c-124">例如: </span><span class="sxs-lookup"><span data-stu-id="fbe0c-124">For example:</span></span>
```Csharp
var converter = new ValueConverter<EquineBeast, string>(
    v => v.ToString(),
    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```
<span data-ttu-id="fbe0c-125">當多個屬性使用相同的轉換時，這非常有用。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-125">This can be useful when multiple properties use the same conversion.</span></span>

> [!NOTE]  
> <span data-ttu-id="fbe0c-126">目前沒有任何方法可以在一個位置指定特定類型的每個屬性都必須使用相同的值轉換器。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-126">There is currently no way to specify in one place that every property of a given type must use the same value converter.</span></span> <span data-ttu-id="fbe0c-127">這項功能會被視為在未來的版本。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-127">This feature will be considered for a future release.</span></span>

## <a name="built-in-converters"></a><span data-ttu-id="fbe0c-128">內建轉換程式</span><span class="sxs-lookup"><span data-stu-id="fbe0c-128">Built-in converters</span></span>

<span data-ttu-id="fbe0c-129">EF 核心隨附一組預先定義的`ValueConverter`中找到的類別`Microsoft.EntityFrameworkCore.Storage.Converters`命名空間。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-129">EF Core ships with a set of pre-defined `ValueConverter` classes, found in the `Microsoft.EntityFrameworkCore.Storage.Converters` namespace.</span></span> <span data-ttu-id="fbe0c-130">這些是：</span><span class="sxs-lookup"><span data-stu-id="fbe0c-130">These are:</span></span>
* <span data-ttu-id="fbe0c-131">`BoolToZeroOneConverter` 為零，而另一個 Bool</span><span class="sxs-lookup"><span data-stu-id="fbe0c-131">`BoolToZeroOneConverter` - Bool to zero and one</span></span>
* <span data-ttu-id="fbe0c-132">`BoolToStringConverter` -字串，例如"Y"和"N"Bool</span><span class="sxs-lookup"><span data-stu-id="fbe0c-132">`BoolToStringConverter` - Bool to strings such as "Y" and "N"</span></span>
* <span data-ttu-id="fbe0c-133">`BoolToTwoValuesConverter` -任何兩個值 Bool</span><span class="sxs-lookup"><span data-stu-id="fbe0c-133">`BoolToTwoValuesConverter` - Bool to any two values</span></span>
* <span data-ttu-id="fbe0c-134">`BytesToStringConverter` 以 Base64 編碼字串的位元組陣列</span><span class="sxs-lookup"><span data-stu-id="fbe0c-134">`BytesToStringConverter` - Byte array to Base64-encoded string</span></span>
* <span data-ttu-id="fbe0c-135">`CastingConverter` -需要 Csharp 轉換的轉換</span><span class="sxs-lookup"><span data-stu-id="fbe0c-135">`CastingConverter` - Conversions that require only a Csharp cast</span></span>
* <span data-ttu-id="fbe0c-136">`CharToStringConverter` -Char 為單一字元字串</span><span class="sxs-lookup"><span data-stu-id="fbe0c-136">`CharToStringConverter` - Char to single character string</span></span>
* <span data-ttu-id="fbe0c-137">`DateTimeOffsetToBinaryConverter` -DateTimeOffset 二進位編碼的 64 位元值</span><span class="sxs-lookup"><span data-stu-id="fbe0c-137">`DateTimeOffsetToBinaryConverter` - DateTimeOffset to binary-encoded 64-bit value</span></span>
* <span data-ttu-id="fbe0c-138">`DateTimeOffsetToBytesConverter` 為位元組陣列 DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="fbe0c-138">`DateTimeOffsetToBytesConverter` - DateTimeOffset to byte array</span></span>
* <span data-ttu-id="fbe0c-139">`DateTimeOffsetToStringConverter` -DateTimeOffset 字串</span><span class="sxs-lookup"><span data-stu-id="fbe0c-139">`DateTimeOffsetToStringConverter` - DateTimeOffset to string</span></span>
* <span data-ttu-id="fbe0c-140">`DateTimeToBinaryConverter` -包括 DateTimeKind 的 64 位元值的日期時間</span><span class="sxs-lookup"><span data-stu-id="fbe0c-140">`DateTimeToBinaryConverter` - DateTime to 64-bit value including DateTimeKind</span></span>
* <span data-ttu-id="fbe0c-141">`DateTimeToStringConverter` 日期時間字串</span><span class="sxs-lookup"><span data-stu-id="fbe0c-141">`DateTimeToStringConverter` - DateTime to string</span></span>
* <span data-ttu-id="fbe0c-142">`DateTimeToTicksConverter` 日期時間以刻度為單位</span><span class="sxs-lookup"><span data-stu-id="fbe0c-142">`DateTimeToTicksConverter` - DateTime to ticks</span></span>
* <span data-ttu-id="fbe0c-143">`EnumToNumberConverter` 列舉為基礎的數字</span><span class="sxs-lookup"><span data-stu-id="fbe0c-143">`EnumToNumberConverter` - Enum to underlying number</span></span>
* <span data-ttu-id="fbe0c-144">`EnumToStringConverter` 列舉字串</span><span class="sxs-lookup"><span data-stu-id="fbe0c-144">`EnumToStringConverter` - Enum to string</span></span>
* <span data-ttu-id="fbe0c-145">`GuidToBytesConverter` 的位元組陣列 Rights-guid</span><span class="sxs-lookup"><span data-stu-id="fbe0c-145">`GuidToBytesConverter` - Guid to byte array</span></span>
* <span data-ttu-id="fbe0c-146">`GuidToStringConverter` Guid 字串</span><span class="sxs-lookup"><span data-stu-id="fbe0c-146">`GuidToStringConverter` - Guid to string</span></span>
* <span data-ttu-id="fbe0c-147">`NumberToBytesConverter` 的位元組陣列任何數值</span><span class="sxs-lookup"><span data-stu-id="fbe0c-147">`NumberToBytesConverter` - Any numerical value to byte array</span></span>
* <span data-ttu-id="fbe0c-148">`NumberToStringConverter` -任何數值字串</span><span class="sxs-lookup"><span data-stu-id="fbe0c-148">`NumberToStringConverter` - Any numerical value to string</span></span>
* <span data-ttu-id="fbe0c-149">`StringToBytesConverter` -UTF8 位元組字串</span><span class="sxs-lookup"><span data-stu-id="fbe0c-149">`StringToBytesConverter` - String to UTF8 bytes</span></span>
* <span data-ttu-id="fbe0c-150">`TimeSpanToStringConverter` -TimeSpan 字串</span><span class="sxs-lookup"><span data-stu-id="fbe0c-150">`TimeSpanToStringConverter` - TimeSpan to string</span></span>
* <span data-ttu-id="fbe0c-151">`TimeSpanToTicksConverter` -要刻度的時間範圍</span><span class="sxs-lookup"><span data-stu-id="fbe0c-151">`TimeSpanToTicksConverter` - TimeSpan to ticks</span></span>

<span data-ttu-id="fbe0c-152">請注意，`EnumToStringConverter`包含在此清單。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-152">Notice that `EnumToStringConverter` is included in this list.</span></span> <span data-ttu-id="fbe0c-153">這表示不需要轉換明確指定，如上所示。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-153">This means that there is no need to specify the conversion explicitly, as shown above.</span></span> <span data-ttu-id="fbe0c-154">相反地，只要使用內建的轉換器：</span><span class="sxs-lookup"><span data-stu-id="fbe0c-154">Instead, just use the built-in converter:</span></span>
```Csharp
var converter = new EnumToStringConverter<EquineBeast>();

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```
<span data-ttu-id="fbe0c-155">請注意，所有內建的轉換器是無狀態，因此單一執行個體可以安全地共用的多個屬性。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-155">Note that all the built-in converters are stateless and so a single instance can be safely shared by multiple properties.</span></span>

## <a name="pre-defined-conversions"></a><span data-ttu-id="fbe0c-156">預先定義的轉換</span><span class="sxs-lookup"><span data-stu-id="fbe0c-156">Pre-defined conversions</span></span>

<span data-ttu-id="fbe0c-157">一般轉換的內建的轉換器沒有需要明確指定轉換器。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-157">For common conversions for which a built-in converter exists there is no need to specify the converter explicitly.</span></span> <span data-ttu-id="fbe0c-158">相反地，只要設定應該使用哪一個提供者類型和 EF 會自動使用適當的組建中轉換子。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-158">Instead, just configure which provider type should be used and EF will automatically use the appropriate build-in converter.</span></span> <span data-ttu-id="fbe0c-159">列舉字串轉換，會使用上述項目，例如，但是 EF 會實際執行此作業會自動如果設定提供者類型：</span><span class="sxs-lookup"><span data-stu-id="fbe0c-159">Enum to string conversions are used as an example above, but EF will actually do this automatically if the provider type is configured:</span></span>
```Csharp
modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion<string>();
```
<span data-ttu-id="fbe0c-160">達到相同的動作時，可以藉由明確指定資料行類型。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-160">The same thing can be achieved by explicitly specifying the column type.</span></span> <span data-ttu-id="fbe0c-161">例如，如果實體類型的定義也因此：</span><span class="sxs-lookup"><span data-stu-id="fbe0c-161">For example, if the entity type is defined like so:</span></span>
```Csharp
public class Rider
{
    public int Id { get; set; }

    [Column(TypeName = "nvarchar(24)")]
    public EquineBeast Mount { get; set; }
}
```
<span data-ttu-id="fbe0c-162">列舉值會儲存成不需任何進一步設定 OnModelCreating 在資料庫中的字串。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-162">Then the enum values will be saved as strings in the database without any further configuration in OnModelCreating.</span></span>

## <a name="limitations"></a><span data-ttu-id="fbe0c-163">限制</span><span class="sxs-lookup"><span data-stu-id="fbe0c-163">Limitations</span></span>

<span data-ttu-id="fbe0c-164">有幾個已知目前系統的限制值轉換：</span><span class="sxs-lookup"><span data-stu-id="fbe0c-164">There are a few known current limitations of the value convertion system:</span></span>
* <span data-ttu-id="fbe0c-165">如上所述，`null`無法轉換。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-165">As noted above, `null` cannot be converted.</span></span>
* <span data-ttu-id="fbe0c-166">目前沒有任何方法可以散佈某個屬性轉換為 multuple 資料行，反之亦然。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-166">There is currently no way to spread a conversion of one property to multuple columns or vice-versa.</span></span>
* <span data-ttu-id="fbe0c-167">使用值的轉換可能會影響 EF 核心能夠將運算式轉譯為 SQL。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-167">Use of value conversions may impact the ability of EF Core to translate expressions to SQL.</span></span> <span data-ttu-id="fbe0c-168">這種情況下，將會記錄警告。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-168">A warning will be logged for such cases.</span></span>
<span data-ttu-id="fbe0c-169">移除這些限制都被視為未來的版本。</span><span class="sxs-lookup"><span data-stu-id="fbe0c-169">Removal of these limitations is being considered for a future release.</span></span>
