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
# <a name="value-conversions"></a>值轉換

值轉換器可在讀取或寫入資料庫時，允許轉換屬性值。 這項轉換可從某個值轉換為相同類型的另一個值 (例如，將字串) 或從某個類型的值加密為另一個類型的值 (例如，在資料庫中來回轉換字串的列舉值。 ) 

> [!TIP]
> 您可以 [從 GitHub 下載範例程式碼](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/)，以執行並偵測到本檔中的所有程式碼。

## <a name="overview"></a>概觀

值轉換器的指定方式為 `ModelClrType` 和 `ProviderClrType` 。 模型類型是實體類型中屬性的 .NET 類型。 提供者類型是資料庫提供者所瞭解的 .NET 型別。 例如，若要將列舉儲存為資料庫中的字串，模型類型為列舉的型別，而提供者類型為 `String` 。 這兩種類型可以相同。

轉換會使用兩個 `Func` 運算式樹狀架構來定義：一個從 `ModelClrType` 到 `ProviderClrType` ，另一個從轉換 `ProviderClrType` 為 `ModelClrType` 。 使用運算式樹狀架構可將它們編譯成資料庫存取委派，以進行有效率的轉換。 運算式樹狀架構可能包含對轉換方法的簡單呼叫，以進行複雜的轉換。

> [!NOTE]
> 已設定值轉換的屬性可能也需要指定 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> 。 如需詳細資訊，請參閱下列範例和 [值](xref:core/modeling/value-comparers) 比較子檔。

## <a name="configuring-a-value-converter"></a>設定值轉換器

值轉換是在中設定 <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> 。 例如，假設列舉和實體型別定義為：

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

您可以在中設定轉換， <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> 以將列舉值儲存為資料庫中的字串，例如 "Donkey"、"Mule" 等等。您只需要提供一個從轉換成的函式 `ModelClrType` ，並將另一個函式 `ProviderClrType` 轉換成相反的轉換：

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
> `null`永遠不會將值傳遞至值轉換器。 在資料庫資料行中的 null 一律是實體實例中的 null，反之亦然。 這可讓轉換的執行變得更容易，並可讓它們在可為 null 且不可為 null 的屬性之間共用。 如需詳細資訊，請參閱 [GitHub 問題 #13850](https://github.com/dotnet/efcore/issues/13850) 。

## <a name="pre-defined-conversions"></a>預先定義的轉換

EF Core 包含許多預先定義的轉換，以避免需要手動寫入轉換函數。 相反地，EF Core 會根據模型中的屬性型別和要求的資料庫提供者類型，挑選要使用的轉換。

例如，列舉至字串轉換是以上述範例的方式來使用，但是當提供者類型設定為使用泛型型別時，EF Core 會自動執行這 `string` 項作業 <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A> ：

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

您可以藉由明確指定資料庫資料行類型來達成相同的目的。 例如，如果實體類型定義如下：

### <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

<!--
        public class Rider2
        {
            public int Id { get; set; }

            [Column(TypeName = "nvarchar(24)")]
            public EquineBeast Mount { get; set; }
        }
-->
[!code-csharp[ConversionByDatabaseType](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByDatabaseType)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

<!--
                modelBuilder
                    .Entity<Rider2>()
                    .Property(e => e.Mount)
                    .HasColumnType("nvarchar(24)");
-->
[!code-csharp[ConversionByDatabaseTypeFluent](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByDatabaseTypeFluent)]

***

然後，列舉值會以字串的形式儲存在資料庫中，而不需要在中進行任何進一步的設定 <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> 。

## <a name="the-valueconverter-class"></a>ValueConverter 類別

<xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A>如上面所示呼叫會建立 <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> 實例，並在屬性上設定它。 `ValueConverter`可以改為明確建立。 例如：

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

當多個屬性使用相同的轉換時，這會很有用。

## <a name="built-in-converters"></a>內建轉換器

如先前所述，EF Core 隨附一組預先定義的 <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> 類別，可在 <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion> 命名空間中找到。 在許多情況下，EF 會根據模型中的屬性型別和資料庫中所要求的型別，選擇適當的內建轉換器，如上所示列舉。 例如， `.HasConversion<int>()` 在屬性上使用 `bool` 會導致 EF Core 將 bool 值轉換成數位零和一個值：

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

這與建立內建的實例 <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> 和明確設定的方式相同：

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

下表摘要說明從模型/屬性類型到資料庫提供者類型的常用預先定義轉換。 在資料表中 `any_numeric_type` ，表示下列其中一個：、、、、、、、、、、 `int` `short` `long` `byte` `uint` `ushort` `ulong` `sbyte` `char` `decimal` `float` 或 `double` 。

| 模型/屬性類型 | 提供者/資料庫類型 | 轉換                                                | 使用方式
|:--------------------|------------------------|-----------------------------------------------------------|------
| bool                | any_numeric_type       | False/true 至0/1                                         | `.HasConversion<any_numeric_type>()`
|                     | any_numeric_type       | False/true 表示任何兩個數字                             | 使用<xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601>
|                     | 字串                 | False/true 表示 "Y"/"N"                                     | `.HasConversion<string>()`
|                     | 字串                 | False/true 表示任何兩個字串                             | 使用<xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter>
| any_numeric_type    | bool                   | 0/1 到 false/true                                         | `.HasConversion<bool>()`
|                     | any_numeric_type       | 簡單轉換                                               | `.HasConversion<any_numeric_type>()`
|                     | 字串                 | 字串形式的數位                                    | `.HasConversion<string>()`
| 列舉                | any_numeric_type       | 列舉的數值                             | `.HasConversion<any_numeric_type>()`
|                     | 字串                 | 列舉值的字串表示               | `.HasConversion<string>()`
| 字串              | bool                   | 將字串剖析為 bool                               | `.HasConversion<bool>()`
|                     | any_numeric_type       | 將字串剖析為指定的數位類型               | `.HasConversion<any_numeric_type>()`
|                     | char                   | 字串的第一個字元                         | `.HasConversion<char>()`
|                     | Datetime               | 將字串剖析為 DateTime                           | `.HasConversion<DateTime>()`
|                     | DateTimeOffset         | 將字串剖析為 DateTimeOffset                     | `.HasConversion<DateTimeOffset>()`
|                     | TimeSpan               | 將字串剖析為 TimeSpan                           | `.HasConversion<TimeSpan>()`
|                     | Guid                   | 將字串剖析為 Guid                               | `.HasConversion<Guid>()`
|                     | byte[]                 | 以 UTF8 位元組表示的字串                                  | `.HasConversion<byte[]>()`
| char                | 字串                 | 單一字元字串                                 | `.HasConversion<string>()`
| Datetime            | long                   | 編碼的日期/時間保留 DateTime. 種類                | `.HasConversion<long>()`
|                     | long                   | 蜱                                                     | 使用<xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter>
|                     | 字串                 | 不因文化特性而異的日期/時間字串                        | `.HasConversion<string>()`
| DateTimeOffset      | long                   | 具有位移的編碼日期/時間                             | `.HasConversion<long>()`
|                     | 字串                 | 具有位移的非變異文化特性日期/時間字串            | `.HasConversion<string>()`
| TimeSpan            | long                   | 蜱                                                     | `.HasConversion<long>()`
|                     | 字串                 | 不變文化特性時間範圍字串                        | `.HasConversion<string>()`
| Uri                 | 字串                 | 字串形式的 URI                                       | `.HasConversion<string>()`
| PhysicalAddress     | 字串                 | 字串形式的位址                                   | `.HasConversion<string>()`
|                     | byte[]                 | 以位元組由大到小的網路順序的位元組                         | `.HasConversion<byte[]>()`
| IPAddress           | 字串                 | 字串形式的位址                                   | `.HasConversion<string>()`
|                     | byte[]                 | 以位元組由大到小的網路順序的位元組                         | `.HasConversion<byte[]>()`
| Guid                | 字串                 | ' Dddddddd-dddd-dddd-dddd-dddddddddddd ' 格式的 GUID | `.HasConversion<string>()`
|                     | byte[]                 | .NET 二進位序列化順序的位元組                  | `.HasConversion<byte[]>()`

請注意，這些轉換會假設值的格式適合進行轉換。 例如，如果字串值無法剖析為數字，則將字串轉換成數位將會失敗。

內建轉換器的完整清單如下：

* 轉換 bool 屬性：
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter> -Bool 至字串，例如 "Y" 和 "N"
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601> -Bool 至任何兩個值
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> -Bool 至零和1
* 轉換位元組陣列屬性：
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BytesToStringConverter> -位元組陣列至 Base64 編碼的字串
* 只需要型別轉換的任何轉換
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CastingConverter%602> -只需要型別轉換的轉換
* 轉換 char 屬性：
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CharToStringConverter> -Char 至單一字元字串
* 轉換 <xref:System.DateTimeOffset> 屬性：
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBinaryConverter> - <xref:System.DateTimeOffset> 至二進位編碼的64位值
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBytesConverter> - <xref:System.DateTimeOffset> 至位元組陣列
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToStringConverter> - <xref:System.DateTimeOffset> 至字串
* 轉換 <xref:System.DateTime> 屬性：
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToBinaryConverter> - <xref:System.DateTime> 至64位值（包括 >datetimekind.unspecified）
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToStringConverter> - <xref:System.DateTime> 至字串
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter> - <xref:System.DateTime> 刻度
* 轉換列舉屬性：
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToNumberConverter%602> -對基礎數位的列舉
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToStringConverter%601> -列舉至字串
* 轉換 <xref:System.Guid> 屬性：
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToBytesConverter> - <xref:System.Guid> 至位元組陣列
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToStringConverter> - <xref:System.Guid> 至字串
* 轉換 <xref:System.Net.IPAddress> 屬性：
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToBytesConverter> - <xref:System.Net.IPAddress> 至位元組陣列
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToStringConverter> - <xref:System.Net.IPAddress> 至字串
* 將數值 (int、double、decimal 等 ) 屬性轉換：
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToBytesConverter%601> -位元組陣列的任何數值
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToStringConverter%601> -字串的任何數值
* 轉換 <xref:System.Net.NetworkInformation.PhysicalAddress> 屬性：
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToBytesConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> 至位元組陣列
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToStringConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> 至字串
* 轉換字串屬性：
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBoolConverter> -字串（例如 "Y" 和 "N" 至 bool）
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBytesConverter> -字串至 UTF8 位元組
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToCharConverter> -字串至字元
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeConverter> -字串至 <xref:System.DateTime>
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeOffsetConverter> -字串至 <xref:System.DateTimeOffset>
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToEnumConverter%601> -字串至列舉
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToGuidConverter> -字串至 <xref:System.Guid>
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToNumberConverter%601> -字串至數數值型別
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToTimeSpanConverter> -字串至 <xref:System.TimeSpan>
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToUriConverter> -字串至 <xref:System.Uri>
* 轉換 <xref:System.TimeSpan> 屬性：
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToStringConverter> - <xref:System.TimeSpan> 至字串
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToTicksConverter> - <xref:System.TimeSpan> 刻度
* 轉換 <xref:System.Uri> 屬性：
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.UriToStringConverter> - <xref:System.Uri> 至字串

請注意，所有內建的轉換器都是無狀態的，因此單一實例可以由多個屬性安全地共用。

## <a name="column-facets-and-mapping-hints"></a>資料行 facet 和對應提示

某些資料庫類型具有修改資料儲存方式的 facet。 這些包括：

* 小數和日期/時間資料行的精確度和小數位數
* 二進位和字串資料行的大小/長度
* 字串資料行的 Unicode

您可以使用值轉換器，以一般方式設定這些 facet，並且將套用至轉換的資料庫類型。 例如，從列舉轉換成字串時，可以將資料庫資料行指定為非 Unicode，並儲存最多20個字元：

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

或者，明確建立轉換器時：

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

這會在 `varchar(20)` 使用 EF Core 的 SQL Server 遷移時產生資料行：

```sql
CREATE TABLE [Rider] (
    [Id] int NOT NULL IDENTITY,
    [Mount] varchar(20) NOT NULL,
    CONSTRAINT [PK_Rider] PRIMARY KEY ([Id]));
```

但是，如果依預設，所有資料 `EquineBeast` 行都應該是 `varchar(20)` ，則可以將這項資訊提供給值轉換器，做為 <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ConverterMappingHints> 。 例如：

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

現在，每當使用這個轉換器時，資料庫資料行將是非 unicode，最大長度為20。 但是，這些只是提示，因為已在對應屬性上明確設定的任何 facet 覆寫這些提示。

## <a name="examples"></a>範例

### <a name="simple-value-objects"></a>簡單值物件

這個範例會使用簡單類型來包裝基本型別。 當您想要讓模型中的型別更明確 (，因此更具型別安全的) 而不是基本型別時，這個方法就很有用。 在此範例中，該類型是 `Dollars` ，它會包裝小數基本：

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

這可用於實體類型：

<!--
        public class Order
        {
            public int Id { get; set; }

            public Dollars Price { get; set; }
        }
-->
[!code-csharp[SimpleValueObjectModel](../../../samples/core/Modeling/ValueConversions/SimpleValueObject.cs?name=SimpleValueObjectModel)]

`decimal`儲存在資料庫中時，會轉換為基礎：

<!--
                modelBuilder.Entity<Order>()
                    .Property(e => e.Price)
                    .HasConversion(
                        v => v.Amount,
                        v => new Dollars(v));
-->
[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/SimpleValueObject.cs?name=ConfigureImmutableStructProperty)]

> [!NOTE]
> 此值物件會實作為 [唯讀結構](/dotnet/csharp/language-reference/builtin-types/struct)。 這表示 EF Core 可以在不發生問題的情況下，快照並比較值。 如需詳細資訊，請參閱 [值](xref:core/modeling/value-comparers) 比較子。

### <a name="composite-value-objects"></a>複合值物件

在上述範例中，值物件類型只包含單一屬性。 值物件類型比較常見的方式，是撰寫多個同時形成領域概念的屬性。 例如， `Money` 包含數量和貨幣的一般類型：

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

此值物件可以像之前一樣用在實體類型中：

<!--
        public class Order
        {
            public int Id { get; set; }

            public Money Price { get; set; }
        }
-->
[!code-csharp[CompositeValueObjectModel](../../../samples/core/Modeling/ValueConversions/CompositeValueObject.cs?name=CompositeValueObjectModel)]

值轉換器目前只能將值轉換成單一資料庫資料行的值。 這項限制表示必須將物件中的所有屬性值編碼成單一資料行值。 這通常是藉由在物件進入資料庫時進行序列化，然後再重新還原序列化的方式來處理。例如，使用 <xref:System.Text.Json> ：

<!--
                modelBuilder.Entity<Order>()
                    .Property(e => e.Price)
                    .HasConversion(
                        v => JsonSerializer.Serialize(v, null),
                        v => JsonSerializer.Deserialize<Money>(v, null));
-->
[!code-csharp[ConfigureCompositeValueObject](../../../samples/core/Modeling/ValueConversions/CompositeValueObject.cs?name=ConfigureCompositeValueObject)]

> [!NOTE]
> 我們計畫允許將物件對應至 EF Core 6.0 中的多個資料行，而不需要在此使用序列化。 這是 [GitHub 問題 #13947](https://github.com/dotnet/efcore/issues/13947)所追蹤。

> [!NOTE]
> 如同上述範例，這個值物件會實作為 [唯讀結構](/dotnet/csharp/language-reference/builtin-types/struct)。 這表示 EF Core 可以在不發生問題的情況下，快照並比較值。 如需詳細資訊，請參閱 [值](xref:core/modeling/value-comparers) 比較子。

### <a name="collections-of-primitives"></a>基本類型的集合

序列化也可以用來儲存基本值的集合。 例如：

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

<xref:System.Text.Json>再次使用：

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

`ICollection<string>` 表示可變的參考型別。 這表示需要 a， <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> 以便 EF Core 可以正確地追蹤和偵測變更。 如需詳細資訊，請參閱 [值](xref:core/modeling/value-comparers) 比較子。

### <a name="collections-of-value-objects"></a>值物件的集合

結合上述兩個範例，我們可以建立值物件的集合。 例如，假設有一 `AnnualFinance` 種類型可將一年的 blog 財務模型：

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

此類型會撰寫 `Money` 我們先前建立的數種類型：

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

接著，我們可以將的集合新增 `AnnualFinance` 至我們的實體類型：

<!--
        public class Blog
        {
            public int Id { get; set; }
            public string Name { get; set; }
            
            public IList<AnnualFinance> Finances { get; set; }
        }
-->
[!code-csharp[ValueObjectCollectionModel](../../../samples/core/Modeling/ValueConversions/ValueObjectCollection.cs?name=ValueObjectCollectionModel)]

然後再次使用序列化來儲存此：

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
> 同樣地，這種轉換需要 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> 。 如需詳細資訊，請參閱 [值](xref:core/modeling/value-comparers) 比較子。

### <a name="value-objects-as-keys"></a>值物件做為索引鍵

有時基本索引鍵屬性可以包裝在值物件中，以在指派值時加入額外的型別安全層級。 例如，我們可以針對 blog 和 post 的金鑰類型來執行金鑰類型：

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

然後，您可以在網域模型中使用這些功能：

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

請注意， `Blog.Id` 不會不慎指派 `PostKey` ，且 `Post.Id` 不會不慎指派給 `BlogKey` 。 同樣地， `Post.BlogId` 必須將外鍵屬性指派給 `BlogKey` 。

> [!NOTE]
> 顯示此模式並不表示我們建議您這樣做。 請仔細考慮這種抽象層級是否有助於或阻礙您的開發體驗。 此外，請考慮使用導覽和產生的金鑰，而不是直接處理索引鍵值。

然後可以使用值轉換器來對應這些索引鍵屬性：

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
> 目前具有轉換的索引鍵屬性無法使用產生的索引鍵值。 投票給 [GitHub 問題 #11597](https://github.com/dotnet/efcore/issues/11597) 移除此限制。

### <a name="use-ulong-for-timestamprowversion"></a>針對 timestamp/rowversion 使用 ulong

SQL Server 支援使用[8 位元組二進位資料 `rowversion` / `timestamp` 行](/sql/t-sql/data-types/rowversion-transact-sql)的自動[開放式並行](xref:core/saving/concurrency)存取。 這些一律會使用8位元組陣列，從資料庫讀取和寫入。 不過，位元組陣列是可變動的參考型別，可讓它們有點難以處理。 值轉換器允許 `rowversion` 改為對應至 `ulong` 屬性，這比位元組陣列更適合且容易使用。 例如，假設有一個 `Blog` 具有 ulong 並行存取權杖的實體：

<!--
        public class Blog
        {
            public int Id { get; set; }
            public string Name { get; set; }
            public ulong Version { get; set; }
        }
-->
[!code-csharp[ULongConcurrencyModel](../../../samples/core/Modeling/ValueConversions/ULongConcurrency.cs?name=ULongConcurrencyModel)]

這可以使用值轉換器對應到 SQL server 資料 `rowversion` 行：

<!--
                modelBuilder.Entity<Blog>()
                    .Property(e => e.Version)
                    .IsRowVersion()
                    .HasConversion<byte[]>();
-->
[!code-csharp[ConfigureULongConcurrency](../../../samples/core/Modeling/ValueConversions/ULongConcurrency.cs?name=ConfigureULongConcurrency)]

### <a name="specify-the-datetimekind-when-reading-dates"></a>指定讀取日期時的日期時間類型

<xref:System.DateTime.Kind%2A?displayProperty=nameWithType>當將儲存為或時，SQL Server 會捨棄旗標 <xref:System.DateTime> [`datetime`](/sql/t-sql/data-types/datetime-transact-sql) [`datetime2`](/sql/t-sql/data-types/datetime2-transact-sql) 。 這表示從資料庫傳回的日期時間值一律為 <xref:System.DateTimeKind> 的 `Unspecified` 。

值轉換器可以用兩種方式來處理此情況。 首先，EF Core 有一個值轉換器，會建立8個位元組的不透明值，以保留 `Kind` 旗標。 例如：

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.PostedOn)
                    .HasConversion<long>();
-->
[!code-csharp[ConfigurePreserveDateTimeKind1](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind1)]

這可讓具有不同旗標的 DateTime 值 `Kind` 在資料庫中混合。

這種方法的問題在於資料庫不再具有可辨識的 `datetime` 或資料 `datetime2` 行。 因此，通常一律會將 UTC 時間儲存 (或較不常的當地時間) ，然後忽略旗標， `Kind` 或使用值轉換器將它設定為適當的值。 例如，下列轉換器可確保 `DateTime` 從資料庫讀取的值會有 <xref:System.DateTimeKind> `UTC` ：

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.LastUpdated)
                    .HasConversion(
                        v => v,
                        v => new DateTime(v.Ticks, DateTimeKind.Utc));
-->
[!code-csharp[ConfigurePreserveDateTimeKind2](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind2)]

如果在實體實例中設定本機和 UTC 值的混合，則可以使用轉換器，在插入之前適當地進行轉換。 例如：

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.LastUpdated)
                    .HasConversion(
                        v => v.ToUniversalTime(),
                        v => new DateTime(v.Ticks, DateTimeKind.Utc));
-->
[!code-csharp[ConfigurePreserveDateTimeKind3](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind3)]

> [!NOTE]
> 請仔細考慮將所有資料庫存取程式碼統一，以隨時使用 UTC 時間，只在向使用者呈現資料時處理當地時間。

### <a name="use-case-insensitive-string-keys"></a>使用不區分大小寫的字串索引鍵

某些資料庫（包括 SQL Server）預設會執行不區分大小寫的字串比較。 相反地，.NET 預設會執行區分大小寫的字串比較。 這表示外鍵值（例如 "DotNet"）會符合 SQL Server 上的主鍵值 "DotNet"，但不會在 EF Core 中符合。 索引鍵的值比較子可以用來強制 EF Core 不區分大小寫的字串比較，例如資料庫。 例如，假設有一個具有字串索引鍵的 blog/post 模型：

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

如果某些 `Post.BlogId` 值有不同的大小寫，這將無法如預期般運作。 造成的錯誤取決於應用程式的執行情況，但通常會包含未正確 [修正](xref:core/change-tracking/relationship-changes) 之物件的圖表，以及/或因為 FK 值錯誤而失敗的更新。 您可以使用值比較子來修正這個錯誤：

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
> .NET 字串比較和資料庫字串比較在大小寫上可能不同。 這種模式適用于簡單的 ASCII 索引鍵，但對於具有任何特定文化特性字元的金鑰，可能會失敗。 如需詳細資訊，請參閱定序 [和區分大小寫](xref:core/miscellaneous/collations-and-case-sensitivity) 。

### <a name="handle-fixed-length-database-strings"></a>處理固定長度的資料庫字串

上一個範例不需要值轉換器。 不過，轉換器對於固定長度的資料庫字串類型（例如或）很 `char(20)` 有用 `nchar(20)` 。 只要將值插入資料庫中，固定長度的字串就會填補到其完整長度。 這表示會將 "" 的索引鍵值 `dotnet` 從資料庫讀回為 " `dotnet..............` "，其中 `.` 代表空白字元。 這樣就不會與未填補的索引鍵值正確地比較。

值轉換子可用來在讀取索引鍵值時修剪填補。 這可以與上一個範例中的值比較子結合，以正確地比較固定長度不區分大小寫的 ASCII 金鑰。 例如：

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

### <a name="encrypt-property-values"></a>加密屬性值

值轉換器可以在將屬性值傳送至資料庫之前用來加密，然後將其解密。例如，使用字串反轉作為實際加密演算法的替代方式：

<!--
                modelBuilder.Entity<User>().Property(e => e.Password).HasConversion(
                    v => new string(v.Reverse().ToArray()),
                    v => new string(v.Reverse().ToArray()));
-->
[!code-csharp[ConfigureEncryptPropertyValues](../../../samples/core/Modeling/ValueConversions/EncryptPropertyValues.cs?name=ConfigureEncryptPropertyValues)]

> [!NOTE]
> 目前沒有任何方法可從值轉換器內取得目前 DbCoNtext 或其他會話狀態的參考。 這會限制可使用的加密類型。 投票給 [GitHub 問題 #11597](https://github.com/dotnet/efcore/issues/12205) 移除此限制。

> [!WARNING]
> 如果您變換自己的加密以保護敏感性資料，請務必瞭解所有的含意。 請考慮改為使用預先建立的加密機制，例如 SQL Server 上的 [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) 。

## <a name="limitations"></a>限制

值轉換系統有一些已知的目前限制：

* 目前沒有任何方法可以在一個地方指定指定類型的每個屬性都必須使用相同的值轉換器。 👍如果這是您所需的內容，請針對[GitHub #10784 問題](https://github.com/dotnet/efcore/issues/10784) () 投票。
* 如上所述， `null` 無法轉換。 👍如果這是您所需的內容，請針對[GitHub #13850 問題](https://github.com/dotnet/efcore/issues/13850) () 投票。
* 目前沒有任何方法可將某個屬性的轉換散佈至多個資料行，反之亦然。 👍如果這是您所需的內容，請針對[GitHub #13947 問題](https://github.com/dotnet/efcore/issues/13947) () 投票。
* 透過值轉換器對應的大部分索引鍵不支援值產生。 👍如果這是您所需的內容，請針對[GitHub #11597 問題](https://github.com/dotnet/efcore/issues/11597) () 投票。
* 值轉換無法參考目前的 DbCoNtext 實例。 👍如果這是您所需的內容，請針對[GitHub #11597 問題](https://github.com/dotnet/efcore/issues/12205) () 投票。

未來的版本將會考慮移除這些限制。
