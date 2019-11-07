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
# <a name="value-conversions"></a>值轉換

> [!NOTE]  
> 此功能是 EF Core 2.1 中的新功能。

值轉換器可在讀取或寫入資料庫時，允許轉換屬性值。 這項轉換可以是從某個值到另一個類型（例如，將字串加密），或從某個型別的值轉換成另一個類型的值（例如，將列舉值轉換成資料庫中的字串，以及將其從資料庫中的一個或多個）。

## <a name="fundamentals"></a>Fundamentals

值轉換器是以 `ModelClrType` 和 `ProviderClrType`的角度來指定。 模型類型是實體類型中屬性的 .NET 類型。 提供者類型是資料庫提供者所瞭解的 .NET 類型。 例如，若要將列舉當做字串儲存在資料庫中，模型類型就是 enum 的類型，而提供者類型是 `String`。 這兩種類型可以相同。

轉換是使用兩個 `Func` 運算式樹狀架構所定義：一個從 `ModelClrType` 到 `ProviderClrType`，另一個則是從 `ProviderClrType` 到 `ModelClrType`。 使用運算式樹狀架構，讓它們可以編譯成資料庫存取程式碼，以進行有效率的轉換。 針對複雜的轉換，運算式樹狀架構可能是簡單的呼叫方法來執行轉換。

## <a name="configuring-a-value-converter"></a>設定值轉換器

值轉換會定義在 `DbContext`之 `OnModelCreating` 的屬性上。 例如，假設列舉和實體類型定義為：

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

然後，可以在 `OnModelCreating` 中定義轉換，將列舉值儲存為資料庫中的字串（例如，"Donkey"、"Mule"、...）：

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
> `null` 值永遠不會傳遞至值轉換器。 這可讓轉換的執行變得更容易，並可讓它們在可為 null 和不可為 null 的屬性之間共用。

## <a name="the-valueconverter-class"></a>ValueConverter 類別

如上面所示呼叫 `HasConversion` 將會建立 `ValueConverter` 實例，並在屬性上設定它。 您可以改為明確建立 `ValueConverter`。 例如:

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
> 目前沒有任何方法可以在一個位置指定指定類型的每個屬性都必須使用相同的值轉換器。 未來的版本將會考慮這項功能。

## <a name="built-in-converters"></a>內建轉換器

EF Core 隨附一組預先定義的 `ValueConverter` 類別，在 `Microsoft.EntityFrameworkCore.Storage.ValueConversion` 命名空間中找到。 這些是：

* `BoolToZeroOneConverter`-Bool 到零和一個
* `BoolToStringConverter`-Bool 到字串（例如 "Y" 和 "N"）
* `BoolToTwoValuesConverter`-Bool 到任何兩個值
* `BytesToStringConverter` 位元組陣列到 Base64 編碼的字串
* 只需要類型轉換的 `CastingConverter` 轉換
* `CharToStringConverter`-Char 至單一字元字串
* `DateTimeOffsetToBinaryConverter`-DateTimeOffset 到二進位編碼的64位值
* `DateTimeOffsetToBytesConverter`-DateTimeOffset 到位元組陣列
* `DateTimeOffsetToStringConverter`-DateTimeOffset 到字串
* `DateTimeToBinaryConverter`-DateTime 至64位值，包括 Datetimekind.utc
* `DateTimeToStringConverter`-日期時間到字串
* `DateTimeToTicksConverter`-DateTime 到刻度
* `EnumToNumberConverter`-列舉到基礎號碼
* `EnumToStringConverter`-列舉為字串
* `GuidToBytesConverter`-Guid 到位元組陣列
* `GuidToStringConverter`-Guid 至字串
* `NumberToBytesConverter`-位元組陣列的任何數值
* `NumberToStringConverter`-字串的任何數值
* `StringToBytesConverter`-字串到 UTF8 位元組
* `TimeSpanToStringConverter`-TimeSpan 到字串
* `TimeSpanToTicksConverter`-TimeSpan 到刻度

請注意，`EnumToStringConverter` 包含在這份清單中。 這表示不需要明確指定轉換，如上所示。 相反地，只要使用內建的轉換器：

``` csharp
var converter = new EnumToStringConverter<EquineBeast>();

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

請注意，所有內建的轉換器都是無狀態的，因此單一實例可以安全地由多個屬性共用。

## <a name="pre-defined-conversions"></a>預先定義的轉換

如果是內建轉換器所在的一般轉換，則不需要明確指定轉換器。 相反地，只需設定應使用的提供者類型，EF 就會自動使用適當的內建轉換器。 列舉到字串轉換是用來做為範例，但如果已設定提供者類型，則 EF 會自動執行此動作：

``` csharp
modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion<string>();
```

也可以藉由明確地指定資料行類型來達到相同的目的。 例如，如果實體類型的定義如下：

``` csharp
public class Rider
{
    public int Id { get; set; }

    [Column(TypeName = "nvarchar(24)")]
    public EquineBeast Mount { get; set; }
}
```

然後，列舉值會以字串的形式儲存在資料庫中，而不會在 `OnModelCreating`中進行進一步的設定。

## <a name="limitations"></a>限制

值轉換系統有幾個已知的目前限制：

* 如先前所述，`null` 無法轉換。
* 目前沒有任何方法可將一個屬性的轉換散佈到多個資料行，反之亦然。
* 使用值轉換可能會影響 EF Core 將運算式轉譯為 SQL 的能力。 這類情況會記錄一則警告。
未來的版本將會考慮移除這些限制。
