---
title: 值轉換為 EF Core
author: ajcvickers
ms.date: 02/19/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/value-conversions
ms.openlocfilehash: d6b51a0a70ee527844b6fe995f39bec534dbaba8
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996284"
---
# <a name="value-conversions"></a>值的轉換

> [!NOTE]  
> 這項功能是在 EF Core 2.1 中新功能。

值轉換器允許讀取或寫入資料庫時，要轉換的屬性值。 這項轉換可從另一個相同類型 （例如，加密的字串） 的一個值或值的一種類型的值，另一種類型 （例如，轉換列舉的值與資料庫中的字串）。

## <a name="fundamentals"></a>Fundamentals

指定的值轉換器`ModelClrType`和`ProviderClrType`。 模型型別是.NET 型別中的實體類型的屬性。 資料庫提供者所了解.NET 型別提供者類型。 比方說，將列舉儲存成資料庫中的字串中，模型型別是列舉，類型和提供者類型是`String`。 這兩種類型可以相同。

轉換會定義使用兩個`Func`運算式樹狀架構： 來自`ModelClrType`來`ProviderClrType`以及從其他`ProviderClrType`到`ModelClrType`。 因此它們可以編譯成有效的轉換與資料庫存取程式碼，可以使用運算式樹狀架構。 針對複雜的轉換，運算式樹狀架構可能是一個簡單的呼叫，以執行轉換的方法。

## <a name="configuring-a-value-converter"></a>設定的值轉換器

在屬性上定義的值轉換`OnModelCreating`的程式`DbContext`。 例如，請考慮已列舉和實體類型定義為：
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
然後可以在定義轉換`OnModelCreating`來儲存列舉值，以在資料庫中的字串 （例如，"Donkey"、"騾子"，...）：
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
> A`null`值將永遠不會傳遞至值轉換器。 這會更輕鬆轉換的實作，並讓他們在可為 null 與不可為 null 的屬性之間共用。

## <a name="the-valueconverter-class"></a>ValueConverter 類別

呼叫`HasConversion`如上所示將建立`ValueConverter`執行個體，並將它設定的屬性。 `ValueConverter`改為明確建立。 例如: 
```Csharp
var converter = new ValueConverter<EquineBeast, string>(
    v => v.ToString(),
    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```
這項功能在多個屬性，請使用相同的轉換時很有用的。

> [!NOTE]  
> 目前沒有方法可以指定在一處指定型別的每個屬性，必須使用相同的值轉換器。 這項功能會被視為在未來的版本。

## <a name="built-in-converters"></a>內建的轉換器

EF Core 隨附一組預先定義的`ValueConverter`中找到的類別`Microsoft.EntityFrameworkCore.Storage.ValueConversion`命名空間。 這些是：
* `BoolToZeroOneConverter` 位以零和一 Bool
* `BoolToStringConverter` -Bool，例如"Y"和"n 名"的字串
* `BoolToTwoValuesConverter` -任兩個值 Bool
* `BytesToStringConverter` Base64 編碼字串的位元組陣列
* `CastingConverter` -需要的轉換僅 Csharp 轉型
* `CharToStringConverter` -Char 單一字元字串
* `DateTimeOffsetToBinaryConverter` -二進位編碼的 64 位元值 DateTimeOffset
* `DateTimeOffsetToBytesConverter` 為位元組陣列 DateTimeOffset
* `DateTimeOffsetToStringConverter` -字串 DateTimeOffset
* `DateTimeToBinaryConverter` -包括 DateTimeKind 的 64 位元值的日期時間
* `DateTimeToStringConverter` 日期時間字串
* `DateTimeToTicksConverter` -日期時間刻度
* `EnumToNumberConverter` -列舉為基礎的數字
* `EnumToStringConverter` -字串列舉
* `GuidToBytesConverter` -Guid，以位元組陣列
* `GuidToStringConverter` -字串 Guid
* `NumberToBytesConverter` -位元組陣列任何數值
* `NumberToStringConverter` -任何數值字串
* `StringToBytesConverter` -UTF8 位元組字串
* `TimeSpanToStringConverter` -TimeSpan 字串
* `TimeSpanToTicksConverter` -要刻度的時間範圍

請注意，`EnumToStringConverter`包含這份清單中。 這表示不需要轉換明確指定，如上所示。 相反地，只要使用內建的轉換器：
```Csharp
var converter = new EnumToStringConverter<EquineBeast>();

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```
請注意，所有內建的轉換器是無狀態，且因此單一執行個體可以安全地共用多個屬性。

## <a name="pre-defined-conversions"></a>預先定義的轉換

有內建的轉換子的通用轉換中，則不需要明確指定轉換子。 相反地，只要設定應該使用哪一個提供者類型和 EF 會自動使用適當的內建轉換子。 字串轉換的列舉做為上述範例，但 EF 會實際如果這麼做會自動設定提供者類型：
```Csharp
modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion<string>();
```
藉由明確指定資料行類型，可以達成相同的動作。 例如，如果實體類型的定義想讓：
```Csharp
public class Rider
{
    public int Id { get; set; }

    [Column(TypeName = "nvarchar(24)")]
    public EquineBeast Mount { get; set; }
}
```
列舉值將會儲存為字串，而不需要任何進一步的設定，在資料庫中則`OnModelCreating`。

## <a name="limitations"></a>限制

有一些已知的目前限制的值轉換系統：
* 如上所述，`null`無法轉換。
* 目前沒有任何方法可以分散到多個資料行或反向轉換一個屬性。
* 使用值的轉換可能會影響 EF Core 能夠將運算式轉譯為 SQL。 這種情況下，就會記錄警告。
移除這些限制會考慮在未來的版本。
