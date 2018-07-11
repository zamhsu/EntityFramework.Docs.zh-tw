---
title: 值轉換為 EF Core
author: ajcvickers
ms.author: divega
ms.date: 02/19/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
ms.technology: entity-framework-core
uid: core/modeling/value-conversions
ms.openlocfilehash: 5bfb6111ac450db91f3f1a7074a924a1c8400ce7
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37949088"
---
# <a name="value-conversions"></a><span data-ttu-id="423a9-102">值的轉換</span><span class="sxs-lookup"><span data-stu-id="423a9-102">Value Conversions</span></span>

> [!NOTE]  
> <span data-ttu-id="423a9-103">這項功能是在 EF Core 2.1 中新功能。</span><span class="sxs-lookup"><span data-stu-id="423a9-103">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="423a9-104">值轉換器允許讀取或寫入資料庫時，要轉換的屬性值。</span><span class="sxs-lookup"><span data-stu-id="423a9-104">Value converters allow property values to be converted when reading from or writing to the database.</span></span> <span data-ttu-id="423a9-105">這項轉換可從另一個相同類型 （例如，加密的字串） 的一個值或值的一種類型的值，另一種類型 （例如，轉換列舉的值與資料庫中的字串）。</span><span class="sxs-lookup"><span data-stu-id="423a9-105">This conversion can be from one value to another of the same type (for example, encrypting strings) or from a value of one type to a value of another type (for example, converting enum values to and from strings in the database.)</span></span>

## <a name="fundamentals"></a><span data-ttu-id="423a9-106">Fundamentals</span><span class="sxs-lookup"><span data-stu-id="423a9-106">Fundamentals</span></span>

<span data-ttu-id="423a9-107">指定的值轉換器`ModelClrType`和`ProviderClrType`。</span><span class="sxs-lookup"><span data-stu-id="423a9-107">Value converters are specified in terms of a `ModelClrType` and a `ProviderClrType`.</span></span> <span data-ttu-id="423a9-108">模型型別是.NET 型別中的實體類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="423a9-108">The model type is the .NET type of the property in the entity type.</span></span> <span data-ttu-id="423a9-109">資料庫提供者所了解.NET 型別提供者類型。</span><span class="sxs-lookup"><span data-stu-id="423a9-109">The provider type is the .NET type understood by the database provider.</span></span> <span data-ttu-id="423a9-110">比方說，將列舉儲存成資料庫中的字串中，模型型別是列舉，類型和提供者類型是`String`。</span><span class="sxs-lookup"><span data-stu-id="423a9-110">For example, to save enums as strings in the database, the model type is the type of the enum, and the provider type is `String`.</span></span> <span data-ttu-id="423a9-111">這兩種類型可以相同。</span><span class="sxs-lookup"><span data-stu-id="423a9-111">These two types can be the same.</span></span>

<span data-ttu-id="423a9-112">轉換會定義使用兩個`Func`運算式樹狀架構： 來自`ModelClrType`來`ProviderClrType`以及從其他`ProviderClrType`到`ModelClrType`。</span><span class="sxs-lookup"><span data-stu-id="423a9-112">Conversions are defined using two `Func` expression trees: one from `ModelClrType` to `ProviderClrType` and the other from `ProviderClrType` to `ModelClrType`.</span></span> <span data-ttu-id="423a9-113">因此它們可以編譯成有效的轉換與資料庫存取程式碼，可以使用運算式樹狀架構。</span><span class="sxs-lookup"><span data-stu-id="423a9-113">Expression trees are used so that they can be compiled into the database access code for efficient conversions.</span></span> <span data-ttu-id="423a9-114">針對複雜的轉換，運算式樹狀架構可能是一個簡單的呼叫，以執行轉換的方法。</span><span class="sxs-lookup"><span data-stu-id="423a9-114">For complex conversions, the expression tree may be a simple call to a method that performs the conversion.</span></span>

## <a name="configuring-a-value-converter"></a><span data-ttu-id="423a9-115">設定的值轉換器</span><span class="sxs-lookup"><span data-stu-id="423a9-115">Configuring a value converter</span></span>

<span data-ttu-id="423a9-116">在您的 DbContext 的 OnModelCreating 屬性上定義值的轉換。</span><span class="sxs-lookup"><span data-stu-id="423a9-116">Value conversions are defined on properties in the OnModelCreating of your DbContext.</span></span> <span data-ttu-id="423a9-117">例如，請考慮已列舉和實體類型定義為：</span><span class="sxs-lookup"><span data-stu-id="423a9-117">For example, consider an enum and entity type defined as:</span></span>
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
<span data-ttu-id="423a9-118">然後可以定義轉換，在 OnModelCreating 中儲存為字串 （例如，"Donkey"、"騾子"，...） 在資料庫中的列舉值：</span><span class="sxs-lookup"><span data-stu-id="423a9-118">Then conversions can be defined in OnModelCreating to store the enum values as strings (for example, "Donkey", "Mule", ...) in the database:</span></span>
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
> <span data-ttu-id="423a9-119">A`null`值將永遠不會傳遞至值轉換器。</span><span class="sxs-lookup"><span data-stu-id="423a9-119">A `null` value will never be passed to a value converter.</span></span> <span data-ttu-id="423a9-120">這會更輕鬆轉換的實作，並讓他們在可為 null 與不可為 null 的屬性之間共用。</span><span class="sxs-lookup"><span data-stu-id="423a9-120">This makes the implementation of conversions easier and allows them to be shared amongst nullable and non-nullable properties.</span></span>

## <a name="the-valueconverter-class"></a><span data-ttu-id="423a9-121">ValueConverter 類別</span><span class="sxs-lookup"><span data-stu-id="423a9-121">The ValueConverter class</span></span>

<span data-ttu-id="423a9-122">呼叫`HasConversion`如上所示將建立`ValueConverter`執行個體，並將它設定的屬性。</span><span class="sxs-lookup"><span data-stu-id="423a9-122">Calling `HasConversion` as shown above will create a `ValueConverter` instance and set it on the property.</span></span> <span data-ttu-id="423a9-123">`ValueConverter`改為明確建立。</span><span class="sxs-lookup"><span data-stu-id="423a9-123">The `ValueConverter` can instead be created explicitly.</span></span> <span data-ttu-id="423a9-124">例如: </span><span class="sxs-lookup"><span data-stu-id="423a9-124">For example:</span></span>
```Csharp
var converter = new ValueConverter<EquineBeast, string>(
    v => v.ToString(),
    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```
<span data-ttu-id="423a9-125">這項功能在多個屬性，請使用相同的轉換時很有用的。</span><span class="sxs-lookup"><span data-stu-id="423a9-125">This can be useful when multiple properties use the same conversion.</span></span>

> [!NOTE]  
> <span data-ttu-id="423a9-126">目前沒有方法可以指定在一處指定型別的每個屬性，必須使用相同的值轉換器。</span><span class="sxs-lookup"><span data-stu-id="423a9-126">There is currently no way to specify in one place that every property of a given type must use the same value converter.</span></span> <span data-ttu-id="423a9-127">這項功能會被視為在未來的版本。</span><span class="sxs-lookup"><span data-stu-id="423a9-127">This feature will be considered for a future release.</span></span>

## <a name="built-in-converters"></a><span data-ttu-id="423a9-128">內建的轉換器</span><span class="sxs-lookup"><span data-stu-id="423a9-128">Built-in converters</span></span>

<span data-ttu-id="423a9-129">EF Core 隨附一組預先定義的`ValueConverter`中找到的類別`Microsoft.EntityFrameworkCore.Storage.ValueConversion`命名空間。</span><span class="sxs-lookup"><span data-stu-id="423a9-129">EF Core ships with a set of pre-defined `ValueConverter` classes, found in the `Microsoft.EntityFrameworkCore.Storage.ValueConversion` namespace.</span></span> <span data-ttu-id="423a9-130">這些是：</span><span class="sxs-lookup"><span data-stu-id="423a9-130">These are:</span></span>
* <span data-ttu-id="423a9-131">`BoolToZeroOneConverter` 位以零和一 Bool</span><span class="sxs-lookup"><span data-stu-id="423a9-131">`BoolToZeroOneConverter` - Bool to zero and one</span></span>
* <span data-ttu-id="423a9-132">`BoolToStringConverter` -Bool，例如"Y"和"n 名"的字串</span><span class="sxs-lookup"><span data-stu-id="423a9-132">`BoolToStringConverter` - Bool to strings such as "Y" and "N"</span></span>
* <span data-ttu-id="423a9-133">`BoolToTwoValuesConverter` -任兩個值 Bool</span><span class="sxs-lookup"><span data-stu-id="423a9-133">`BoolToTwoValuesConverter` - Bool to any two values</span></span>
* <span data-ttu-id="423a9-134">`BytesToStringConverter` Base64 編碼字串的位元組陣列</span><span class="sxs-lookup"><span data-stu-id="423a9-134">`BytesToStringConverter` - Byte array to Base64-encoded string</span></span>
* <span data-ttu-id="423a9-135">`CastingConverter` -需要的轉換僅 Csharp 轉型</span><span class="sxs-lookup"><span data-stu-id="423a9-135">`CastingConverter` - Conversions that require only a Csharp cast</span></span>
* <span data-ttu-id="423a9-136">`CharToStringConverter` -Char 單一字元字串</span><span class="sxs-lookup"><span data-stu-id="423a9-136">`CharToStringConverter` - Char to single character string</span></span>
* <span data-ttu-id="423a9-137">`DateTimeOffsetToBinaryConverter` -二進位編碼的 64 位元值 DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="423a9-137">`DateTimeOffsetToBinaryConverter` - DateTimeOffset to binary-encoded 64-bit value</span></span>
* <span data-ttu-id="423a9-138">`DateTimeOffsetToBytesConverter` 為位元組陣列 DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="423a9-138">`DateTimeOffsetToBytesConverter` - DateTimeOffset to byte array</span></span>
* <span data-ttu-id="423a9-139">`DateTimeOffsetToStringConverter` -字串 DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="423a9-139">`DateTimeOffsetToStringConverter` - DateTimeOffset to string</span></span>
* <span data-ttu-id="423a9-140">`DateTimeToBinaryConverter` -包括 DateTimeKind 的 64 位元值的日期時間</span><span class="sxs-lookup"><span data-stu-id="423a9-140">`DateTimeToBinaryConverter` - DateTime to 64-bit value including DateTimeKind</span></span>
* <span data-ttu-id="423a9-141">`DateTimeToStringConverter` 日期時間字串</span><span class="sxs-lookup"><span data-stu-id="423a9-141">`DateTimeToStringConverter` - DateTime to string</span></span>
* <span data-ttu-id="423a9-142">`DateTimeToTicksConverter` -日期時間刻度</span><span class="sxs-lookup"><span data-stu-id="423a9-142">`DateTimeToTicksConverter` - DateTime to ticks</span></span>
* <span data-ttu-id="423a9-143">`EnumToNumberConverter` -列舉為基礎的數字</span><span class="sxs-lookup"><span data-stu-id="423a9-143">`EnumToNumberConverter` - Enum to underlying number</span></span>
* <span data-ttu-id="423a9-144">`EnumToStringConverter` -字串列舉</span><span class="sxs-lookup"><span data-stu-id="423a9-144">`EnumToStringConverter` - Enum to string</span></span>
* <span data-ttu-id="423a9-145">`GuidToBytesConverter` -Guid，以位元組陣列</span><span class="sxs-lookup"><span data-stu-id="423a9-145">`GuidToBytesConverter` - Guid to byte array</span></span>
* <span data-ttu-id="423a9-146">`GuidToStringConverter` -字串 Guid</span><span class="sxs-lookup"><span data-stu-id="423a9-146">`GuidToStringConverter` - Guid to string</span></span>
* <span data-ttu-id="423a9-147">`NumberToBytesConverter` -位元組陣列任何數值</span><span class="sxs-lookup"><span data-stu-id="423a9-147">`NumberToBytesConverter` - Any numerical value to byte array</span></span>
* <span data-ttu-id="423a9-148">`NumberToStringConverter` -任何數值字串</span><span class="sxs-lookup"><span data-stu-id="423a9-148">`NumberToStringConverter` - Any numerical value to string</span></span>
* <span data-ttu-id="423a9-149">`StringToBytesConverter` -UTF8 位元組字串</span><span class="sxs-lookup"><span data-stu-id="423a9-149">`StringToBytesConverter` - String to UTF8 bytes</span></span>
* <span data-ttu-id="423a9-150">`TimeSpanToStringConverter` -TimeSpan 字串</span><span class="sxs-lookup"><span data-stu-id="423a9-150">`TimeSpanToStringConverter` - TimeSpan to string</span></span>
* <span data-ttu-id="423a9-151">`TimeSpanToTicksConverter` -要刻度的時間範圍</span><span class="sxs-lookup"><span data-stu-id="423a9-151">`TimeSpanToTicksConverter` - TimeSpan to ticks</span></span>

<span data-ttu-id="423a9-152">請注意，`EnumToStringConverter`包含這份清單中。</span><span class="sxs-lookup"><span data-stu-id="423a9-152">Notice that `EnumToStringConverter` is included in this list.</span></span> <span data-ttu-id="423a9-153">這表示不需要轉換明確指定，如上所示。</span><span class="sxs-lookup"><span data-stu-id="423a9-153">This means that there is no need to specify the conversion explicitly, as shown above.</span></span> <span data-ttu-id="423a9-154">相反地，只要使用內建的轉換器：</span><span class="sxs-lookup"><span data-stu-id="423a9-154">Instead, just use the built-in converter:</span></span>
```Csharp
var converter = new EnumToStringConverter<EquineBeast>();

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```
<span data-ttu-id="423a9-155">請注意，所有內建的轉換器是無狀態，且因此單一執行個體可以安全地共用多個屬性。</span><span class="sxs-lookup"><span data-stu-id="423a9-155">Note that all the built-in converters are stateless and so a single instance can be safely shared by multiple properties.</span></span>

## <a name="pre-defined-conversions"></a><span data-ttu-id="423a9-156">預先定義的轉換</span><span class="sxs-lookup"><span data-stu-id="423a9-156">Pre-defined conversions</span></span>

<span data-ttu-id="423a9-157">有內建的轉換子的通用轉換中，則不需要明確指定轉換子。</span><span class="sxs-lookup"><span data-stu-id="423a9-157">For common conversions for which a built-in converter exists there is no need to specify the converter explicitly.</span></span> <span data-ttu-id="423a9-158">相反地，只要設定應該使用哪一個提供者類型和 EF 會自動使用適當的內建轉換子。</span><span class="sxs-lookup"><span data-stu-id="423a9-158">Instead, just configure which provider type should be used and EF will automatically use the appropriate build-in converter.</span></span> <span data-ttu-id="423a9-159">字串轉換的列舉做為上述範例，但 EF 會實際如果這麼做會自動設定提供者類型：</span><span class="sxs-lookup"><span data-stu-id="423a9-159">Enum to string conversions are used as an example above, but EF will actually do this automatically if the provider type is configured:</span></span>
```Csharp
modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion<string>();
```
<span data-ttu-id="423a9-160">藉由明確指定資料行類型，可以達成相同的動作。</span><span class="sxs-lookup"><span data-stu-id="423a9-160">The same thing can be achieved by explicitly specifying the column type.</span></span> <span data-ttu-id="423a9-161">例如，如果實體類型的定義想讓：</span><span class="sxs-lookup"><span data-stu-id="423a9-161">For example, if the entity type is defined like so:</span></span>
```Csharp
public class Rider
{
    public int Id { get; set; }

    [Column(TypeName = "nvarchar(24)")]
    public EquineBeast Mount { get; set; }
}
```
<span data-ttu-id="423a9-162">然後列舉值將會儲存為字串的資料庫沒有任何進一步的設定，在 OnModelCreating 中。</span><span class="sxs-lookup"><span data-stu-id="423a9-162">Then the enum values will be saved as strings in the database without any further configuration in OnModelCreating.</span></span>

## <a name="limitations"></a><span data-ttu-id="423a9-163">限制</span><span class="sxs-lookup"><span data-stu-id="423a9-163">Limitations</span></span>

<span data-ttu-id="423a9-164">有一些已知的目前限制的值轉換系統：</span><span class="sxs-lookup"><span data-stu-id="423a9-164">There are a few known current limitations of the value convertion system:</span></span>
* <span data-ttu-id="423a9-165">如上所述，`null`無法轉換。</span><span class="sxs-lookup"><span data-stu-id="423a9-165">As noted above, `null` cannot be converted.</span></span>
* <span data-ttu-id="423a9-166">目前沒有任何方法可以分散到多個資料行或反向轉換一個屬性。</span><span class="sxs-lookup"><span data-stu-id="423a9-166">There is currently no way to spread a conversion of one property to multiple columns or vice-versa.</span></span>
* <span data-ttu-id="423a9-167">使用值的轉換可能會影響 EF Core 能夠將運算式轉譯為 SQL。</span><span class="sxs-lookup"><span data-stu-id="423a9-167">Use of value conversions may impact the ability of EF Core to translate expressions to SQL.</span></span> <span data-ttu-id="423a9-168">這種情況下，就會記錄警告。</span><span class="sxs-lookup"><span data-stu-id="423a9-168">A warning will be logged for such cases.</span></span>
<span data-ttu-id="423a9-169">移除這些限制會考慮在未來的版本。</span><span class="sxs-lookup"><span data-stu-id="423a9-169">Removal of these limitations is being considered for a future release.</span></span>
