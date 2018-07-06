---
title: 值轉換為 EF Core
author: ajcvickers
ms.author: divega
ms.date: 02/19/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
ms.technology: entity-framework-core
uid: core/modeling/value-conversions
ms.openlocfilehash: 3e97c05a87ad9b4817c03f446031ea6c74704f5b
ms.sourcegitcommit: 605e42232854ce44bae09624a6eebc35b8e2473b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2018
ms.locfileid: "34191112"
---
# <a name="value-conversions"></a>值的轉換

> [!NOTE]  
> 這項功能是在 EF Core 2.1 中新功能。

值轉換器可讓讀取或寫入資料庫時要轉換的屬性值。 這項轉換可以從另一個相同類型 （例如，加密的字串） 的一個值或值的一種類型的值，另一個類型 （例如，轉換列舉的值與資料庫中的字串）。

## <a name="fundamentals"></a>Fundamentals

指定的值轉換器`ModelClrType`和`ProviderClrType`。 模型類型是屬性的.NET 型別中的實體類型。 提供者類型為資料庫提供者所了解的.NET 類型。 例如，將列舉儲存成資料庫中的字串，模型型別是列舉的類型和提供者類型是`String`。 這兩種類型可以是相同。

轉換會使用兩個定義`Func`運算式樹狀架構： 從`ModelClrType`至`ProviderClrType`和從其他`ProviderClrType`至`ModelClrType`。 運算式樹狀架構會使用，因此，它們可以編譯成資料庫存取程式碼的有效轉換。 針對複雜的轉換，運算式樹狀架構可能是一個簡單的呼叫會執行轉換的方法。

## <a name="configuring-a-value-converter"></a>設定的值轉換器

中的程式 DbContext OnModelCreating 屬性未定義值的轉換。 例如，考慮列舉和實體類型定義為：
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
然後 OnModelCreating （例如，儲存為字串的列舉值中可以定義轉換「 Donkey"，"騾子"，...)在資料庫中：
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
> A`null`值絕不會傳遞至值轉換器。 這會使您更輕鬆的轉換實作，並讓他們在可為 null 與不可為 null 的屬性之間會共用。

## <a name="the-valueconverter-class"></a>ValueConverter 類別

呼叫`HasConversion`如上所示將建立`ValueConverter`執行個體，並將其設定的屬性。 `ValueConverter`改為明確建立。 例如：
```Csharp
var converter = new ValueConverter<EquineBeast, string>(
    v => v.ToString(),
    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```
當多個屬性使用相同的轉換時，這非常有用。

> [!NOTE]  
> 目前沒有任何方法可以在一個位置指定特定類型的每個屬性都必須使用相同的值轉換器。 這項功能會被視為在未來的版本。

## <a name="built-in-converters"></a>內建轉換程式

EF Core 隨附一組預先定義的`ValueConverter`中找到的類別`Microsoft.EntityFrameworkCore.Storage.ValueConversion`命名空間。 這些是：
* `BoolToZeroOneConverter` 為零，而另一個 Bool
* `BoolToStringConverter` -字串，例如"Y"和"N"Bool
* `BoolToTwoValuesConverter` -任何兩個值 Bool
* `BytesToStringConverter` 以 Base64 編碼字串的位元組陣列
* `CastingConverter` -需要 Csharp 轉換的轉換
* `CharToStringConverter` -Char 為單一字元字串
* `DateTimeOffsetToBinaryConverter` -DateTimeOffset 二進位編碼的 64 位元值
* `DateTimeOffsetToBytesConverter` 為位元組陣列 DateTimeOffset
* `DateTimeOffsetToStringConverter` -DateTimeOffset 字串
* `DateTimeToBinaryConverter` -包括 DateTimeKind 的 64 位元值的日期時間
* `DateTimeToStringConverter` 日期時間字串
* `DateTimeToTicksConverter` 日期時間以刻度為單位
* `EnumToNumberConverter` 列舉為基礎的數字
* `EnumToStringConverter` 列舉字串
* `GuidToBytesConverter` 的位元組陣列 Rights-guid
* `GuidToStringConverter` Guid 字串
* `NumberToBytesConverter` 的位元組陣列任何數值
* `NumberToStringConverter` -任何數值字串
* `StringToBytesConverter` -UTF8 位元組字串
* `TimeSpanToStringConverter` -TimeSpan 字串
* `TimeSpanToTicksConverter` -要刻度的時間範圍

請注意，`EnumToStringConverter`包含在此清單。 這表示不需要轉換明確指定，如上所示。 相反地，只要使用內建的轉換器：
```Csharp
var converter = new EnumToStringConverter<EquineBeast>();

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```
請注意，所有內建的轉換器是無狀態，因此單一執行個體可以安全地共用的多個屬性。

## <a name="pre-defined-conversions"></a>預先定義的轉換

一般轉換的內建的轉換器沒有需要明確指定轉換器。 相反地，只要設定應該使用哪一個提供者類型和 EF 會自動使用適當的組建中轉換子。 列舉字串轉換，會使用上述項目，例如，但是 EF 會實際執行此作業會自動如果設定提供者類型：
```Csharp
modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion<string>();
```
達到相同的動作時，可以藉由明確指定資料行類型。 例如，如果實體類型的定義也因此：
```Csharp
public class Rider
{
    public int Id { get; set; }

    [Column(TypeName = "nvarchar(24)")]
    public EquineBeast Mount { get; set; }
}
```
列舉值會儲存成不需任何進一步設定 OnModelCreating 在資料庫中的字串。

## <a name="limitations"></a>限制

有幾個已知目前系統的限制值轉換：
* 如上所述，`null`無法轉換。
* 目前沒有任何方法可以散佈某個屬性轉換為多個資料行，反之亦然。
* 使用值的轉換可能會影響 EF Core 能夠將運算式轉譯為 SQL。 這種情況下，將會記錄警告。
移除這些限制都被視為未來的版本。
