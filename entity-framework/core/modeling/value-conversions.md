---
title: 值轉換-EF Core
description: 在 Entity Framework Core 模型中設定值轉換器
author: ajcvickers
ms.date: 02/19/2018
uid: core/modeling/value-conversions
ms.openlocfilehash: 1d347eb6a7fcdcb55239e1fa854f6c38ab081b21
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072547"
---
# <a name="value-conversions"></a>值轉換

> [!NOTE]  
> 此功能是 EF Core 2.1 中的新功能。

值轉換器可在讀取或寫入資料庫時，允許轉換屬性值。 這項轉換可從某個值轉換為相同類型的另一個值 (例如，將字串) 或從某個類型的值加密為另一個類型的值 (例如，在資料庫中來回轉換字串的列舉值。 ) 

## <a name="fundamentals"></a>基礎

值轉換器的指定方式為 `ModelClrType` 和 `ProviderClrType` 。 模型類型是實體類型中屬性的 .NET 類型。 提供者類型是資料庫提供者所瞭解的 .NET 型別。 例如，若要將列舉儲存為資料庫中的字串，模型類型為列舉的型別，而提供者類型為 `String` 。 這兩種類型可以相同。

轉換會使用兩個 `Func` 運算式樹狀架構來定義：一個從 `ModelClrType` 到 `ProviderClrType` ，另一個從轉換 `ProviderClrType` 為 `ModelClrType` 。 使用運算式樹狀架構可將它們編譯成資料庫存取程式碼，以進行有效率的轉換。 針對複雜的轉換，運算式樹狀架構可能是對執行轉換的方法進行簡單的呼叫。

## <a name="configuring-a-value-converter"></a>設定值轉換器

值轉換是在的屬性中定義的 `OnModelCreating` `DbContext` 。 例如，假設列舉和實體型別定義為：

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

然後，您可以在中定義轉換， `OnModelCreating` 以將列舉值儲存為字串 (例如，"Donkey"、"Mule"、... ) 在資料庫中：

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
> `null`永遠不會將值傳遞至值轉換器。 這可讓轉換的執行變得更容易，並可讓它們在可為 null 且不可為 null 的屬性之間共用。

## <a name="the-valueconverter-class"></a>ValueConverter 類別

`HasConversion`如上面所示呼叫會建立 `ValueConverter` 實例，並在屬性上設定它。 `ValueConverter`可以改為明確建立。 例如：

``` csharp
var converter = new ValueConverter<EquineBeast, string>(
    v => v.ToString(),
    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

當多個屬性使用相同的轉換時，這會很有用。

> [!NOTE]  
> 目前沒有任何方法可以在一個地方指定指定類型的每個屬性都必須使用相同的值轉換器。 未來的版本將會考慮這項功能。

## <a name="built-in-converters"></a>內建轉換器

EF Core 隨附一組預先定義的 `ValueConverter` 類別，可在 `Microsoft.EntityFrameworkCore.Storage.ValueConversion` 命名空間中找到。 這些警告是：

* `BoolToZeroOneConverter` -Bool 至零和1
* `BoolToStringConverter` -Bool 至字串，例如 "Y" 和 "N"
* `BoolToTwoValuesConverter` -Bool 至任何兩個值
* `BytesToStringConverter` -位元組陣列至 Base64 編碼的字串
* `CastingConverter` -只需要型別轉換的轉換
* `CharToStringConverter` -Char 至單一字元字串
* `DateTimeOffsetToBinaryConverter` -DateTimeOffset 至二進位編碼的64位值
* `DateTimeOffsetToBytesConverter` -DateTimeOffset 至位元組陣列
* `DateTimeOffsetToStringConverter` -DateTimeOffset 至字串
* `DateTimeToBinaryConverter` -DateTime 至64位值，包括 >datetimekind.unspecified
* `DateTimeToStringConverter` -DateTime 至字串
* `DateTimeToTicksConverter` -DateTime 至刻度
* `EnumToNumberConverter` -對基礎數位的列舉
* `EnumToStringConverter` -列舉至字串
* `GuidToBytesConverter` -位元組陣列的 Guid
* `GuidToStringConverter` -字串的 Guid
* `NumberToBytesConverter` -位元組陣列的任何數值
* `NumberToStringConverter` -字串的任何數值
* `StringToBytesConverter` -字串至 UTF8 位元組
* `TimeSpanToStringConverter` -TimeSpan 至字串
* `TimeSpanToTicksConverter` -TimeSpan 至刻度

請注意， `EnumToStringConverter` 包含在此清單中。 這表示不需要明確指定轉換，如上所示。 相反地，只使用內建的轉換器：

``` csharp
var converter = new EnumToStringConverter<EquineBeast>();

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

請注意，所有內建的轉換器都是無狀態的，因此單一實例可以由多個屬性安全地共用。

## <a name="pre-defined-conversions"></a>預先定義的轉換

針對內建轉換器存在的一般轉換，不需要明確指定轉換器。 相反地，只需設定應該使用的提供者類型，EF 就會自動使用適當的內建轉換器。 列舉至字串轉換是以上述範例的形式使用，但如果已設定提供者類型，EF 將會自動執行這項作業：

``` csharp
modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion<string>();
```

您可以明確指定資料行類型來達成相同的目的。 例如，如果實體類型定義如下：

``` csharp
public class Rider
{
    public int Id { get; set; }

    [Column(TypeName = "nvarchar(24)")]
    public EquineBeast Mount { get; set; }
}
```

然後，列舉值會以字串的形式儲存在資料庫中，而不需要在中進行任何進一步的設定 `OnModelCreating` 。

## <a name="limitations"></a>限制

值轉換系統有一些已知的目前限制：

* 如上所述， `null` 無法轉換。
* 目前沒有任何方法可將某個屬性的轉換散佈至多個資料行，反之亦然。
* 使用值轉換可能會影響 EF Core 將運算式轉譯為 SQL 的能力。 將會記錄這類案例的警告。
未來的版本將會考慮移除這些限制。
