---
title: EF Core 5.0 中的重大變更-EF Core
description: Entity Framework Core 5.0 中引進的重大變更完整清單
author: bricelam
ms.date: 11/07/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: e52b77e8c19ab3005aee50e1cf4e170d3ee1f502
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023441"
---
# <a name="breaking-changes-in-ef-core-50"></a>EF Core 5.0 中的重大變更

下列 API 和行為變更可能會中斷現有的應用程式更新至 EF Core 5.0.0。

## <a name="summary"></a>摘要

| **重大變更**                                                                                                                   | **影響** |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [EF Core 5.0 不支援 .NET Framework](#netstandard21)                                                                         | 中     |
| [IProperty. GetColumnName () 現已淘汰](#getcolumnname-obsolete)                                                                  | 中     |
| [小數必須有精確度和小數位數](#decimals)                                                                            | 中     |
| [從主體到相依的導覽上的必要項具有不同的語義](#required-dependent)                                 | 中     |
| [定義查詢會取代為提供者特定的方法](#defining-query)                                                          | 中     |
| [查詢不會覆寫非 null 的參考導覽](#nonnullreferences)                                                   | 中     |
| [ToView () 由遷移以不同方式處理](#toview)                                                                              | 中     |
| [ToTable (null) 將實體類型標示為未對應至資料表](#totable)                                                              | 中     |
| [已從 SQLite NTS 擴充功能移除 HasGeometricDimension 方法](#geometric-sqlite)                                                   | 低        |
| [Cosmos：現在已將資料分割索引鍵新增至主要索引鍵](#cosmos-partition-key)                                                        | 低        |
| [Cosmos： `id` 屬性已重新命名為 `__id`](#cosmos-id)                                                                                 | 低        |
| [Cosmos： byte [] 現在會儲存為 base64 字串，而不是數位陣列](#cosmos-byte)                                             | 低        |
| [Cosmos： GetPropertyName 和 SetPropertyName 已重新命名](#cosmos-metadata)                                                          | 低        |
| [當實體狀態從卸離變更為未變更、更新或刪除時，會呼叫值產生器](#non-added-generation) | 低        |
| [IMigrationsModelDiffer 現在使用 IRelationalModel](#relational-model)                                                                 | 低        |
| [鑒別子是唯讀的](#read-only-discriminators)                                                                             | 低        |
| [提供者特定的 EF。InMemory 提供者的函數方法擲回](#no-client-methods)                                              | 低        |
| [IndexBuilder. HasName 現已淘汰](#index-obsolete)                                                                               | 低        |
| [Pluralizer 現已包含給樣板反轉工程模型](#pluralizer)                                                 | 低        |
| [INavigationBase 取代某些 Api 中的 INavigation，以支援略過導覽](#inavigationbase)                                     | 低        |
| [某些使用 `Distinct` 或已不再支援之相互關聯集合的查詢 `GroupBy`](#collection-distinct-groupby) | 低        |
| [不支援在投射中使用可查詢型別的集合](#queryable-projection)                                          | 低        |

## <a name="medium-impact-changes"></a>中度影響變更

<a name="netstandard21"></a>

### <a name="ef-core-50-does-not-support-net-framework"></a>EF Core 5.0 不支援 .NET Framework

[追蹤問題 #15498](https://github.com/dotnet/efcore/issues/15498)

#### <a name="old-behavior"></a>舊的行為

EF Core 3.1 的目標是 .net Standard 2.0，.NET Framework 支援此功能。

#### <a name="new-behavior"></a>新的行為

EF Core 5.0 的目標是 .net Standard 2.1，.NET Framework 並不支援。 這表示 EF Core 5.0 無法搭配 .NET Framework 應用程式使用。

#### <a name="why"></a>原因

這是跨 .NET 團隊進行廣泛移動的一部分，其目標是統一至單一 .NET 目標架構。 如需詳細資訊，請參閱 [.Net Standard 的未來](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/)。

#### <a name="mitigations"></a>風險降低

.NET Framework 應用程式可以繼續使用 EF Core 3.1，這是 [ (LTS) 版本的長期支援](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。 或者，您也可以將應用程式更新為使用 .NET Core 3.1 或 .NET 5，這兩者都支援 .NET Standard 2.1。

<a name="getcolumnname-obsolete"></a>

### <a name="ipropertygetcolumnname-is-now-obsolete"></a>IProperty. GetColumnName () 現已淘汰

[追蹤問題 #2266](https://github.com/dotnet/efcore/issues/2266)

#### <a name="old-behavior"></a>舊的行為

`GetColumnName()` 傳回屬性所對應之資料行的名稱。

#### <a name="new-behavior"></a>新的行為

`GetColumnName()` 仍會傳回屬性所對應之資料行的名稱，但這種行為現在不明確，因為 EF Core 5 支援 TPT 和同時對應至 view 或函式，而這些對應可以針對相同的屬性使用不同的資料行名稱。

#### <a name="why"></a>原因

我們將此方法標示為過時，以引導使用者更精確地進行多載 <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName(Microsoft.EntityFrameworkCore.Metadata.IProperty,Microsoft.EntityFrameworkCore.Metadata.StoreObjectIdentifier@)> 。

#### <a name="mitigations"></a>風險降低

使用下列程式碼來取得特定資料表的資料行名稱：

```csharp
var columnName = property.GetColumnName(StoreObjectIdentifier.Table("Users", null)));
```

<a name="decimals"></a>

### <a name="precision-and-scale-are-required-for-decimals"></a>小數必須有精確度和小數位數

[追蹤問題 #19293](https://github.com/dotnet/efcore/issues/19293)

#### <a name="old-behavior"></a>舊的行為

EF Core 通常不會在物件上設定精確度和小數位數 <xref:Microsoft.Data.SqlClient.SqlParameter> 。 這表示完整的精確度和小數位數已傳送至 SQL Server，此時 SQL Server 會根據資料庫資料行的有效位數和小數位數來舍入。

#### <a name="new-behavior"></a>新的行為

EF Core 現在會使用針對 EF Core 模型中的屬性所設定的值，來設定參數的有效位數和小數位數。 這表示四捨五入現在會在 SqlClient 中發生。 因此，如果設定的有效位數和小數位數不符合資料庫的精確度和小數位數，則會出現舍入的變化。

#### <a name="why"></a>原因

較新的 SQL Server 功能（包括 [永遠加密]）需要完整指定參數 facet。 此外，SqlClient 進行了四捨五入的變更，而不是截斷十進位值，因此符合 SQL Server 行為。 這使得 EF Core 可以設定這些 facet，而不需要變更正確設定的小數行為。

#### <a name="mitigations"></a>風險降低

使用包含有效位數和小數位數的型別名稱來對應您的十進位屬性。 例如：

```csharp
public class Blog
{
    public int Id { get; set; }

    [Column(TypeName = "decimal(16, 5")]
    public decimal Score { get; set; }
}
```

或 `HasPrecision` 在模型建立 api 中使用。 例如：

```csharp
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>().Property(e => e.Score).HasPrecision(16, 5);
    }
```

<a name="required-dependent"></a>

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a>從主體到相依的導覽上的必要項具有不同的語義

[追蹤問題 #17286](https://github.com/dotnet/efcore/issues/17286)

#### <a name="old-behavior"></a>舊的行為

只有導覽至主體可設定為必要。 因此， `RequiredAttribute` 在相依 (的實體上使用，包含外鍵的實體) 會改為在定義實體類型上建立外鍵。

#### <a name="new-behavior"></a>新的行為

由於已新增對必要相依性的支援，現在可以視需要將任何參考導覽標記，這表示在上方顯示的情況下，外鍵將會定義于關聯性的另一端，而且屬性不會標示為必要項。

`IsRequired`在指定相依端點之前呼叫，現在是不明確的：

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

#### <a name="why"></a>原因

若要啟用必要相依性的支援，必須要有新的行為 ([請參閱 #12100](https://github.com/dotnet/efcore/issues/12100)) 。

#### <a name="mitigations"></a>風險降低

`RequiredAttribute`從導覽中移除相依性，並將其放在主體的導覽上，或在中設定關聯性 `OnModelCreating` ：

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a>定義查詢會取代為提供者特定的方法

[追蹤問題 #18903](https://github.com/dotnet/efcore/issues/18903)

#### <a name="old-behavior"></a>舊的行為

實體類型已對應至在核心層級定義查詢。 任何時候，實體類型的查詢根目錄中使用的實體類型已由任何提供者的定義查詢所取代。

#### <a name="new-behavior"></a>新的行為

用於定義查詢的 Api 已被取代。 引進了新的提供者特定 Api。

#### <a name="why"></a>原因

當查詢中使用查詢根時，定義查詢會實作為取代查詢，但有幾個問題：

- 如果定義查詢是使用 in 方法投射實體型別 `new { ... }` `Select` ，則將其識別為實體需要額外的工作，並使其與 EF Core 在查詢中處理名義類型的方式不一致。
- 針對關聯式提供者， `FromSql` 仍需要以 LINQ 運算式形式傳遞 SQL 字串。

最初定義查詢時，是以用戶端視圖的形式導入，以用於無索引鍵實體的 In-Memory 提供者 (類似于關係資料庫) 中的資料庫檢視。 這類定義可讓您輕鬆地針對記憶體中資料庫測試應用程式。 之後，這些應用程式很有用，但卻變得不一致，而且難以理解的行為。 所以我們決定簡化這個概念。 我們對 In-Memory 提供者進行以 LINQ 為基礎的定義查詢，並以不同的方式加以處理。 如需詳細資訊，請 [參閱此問題](https://github.com/dotnet/efcore/issues/20023)。

#### <a name="mitigations"></a>風險降低

若為關聯式提供者，請使用 `ToSqlQuery` 中的方法 `OnModelCreating` 並傳入要用於實體類型的 SQL 字串。
針對 In-Memory 提供者，請使用 `ToInMemoryQuery` 中的方法， `OnModelCreating` 並傳入 LINQ 查詢以用於實體型別。

<a name="nonnullreferences"></a>

### <a name="non-null-reference-navigations-are-not-overwritten-by-queries"></a>查詢不會覆寫非 null 的參考導覽

[追蹤問題 #2693](https://github.com/dotnet/EntityFramework.Docs/issues/2693)

#### <a name="old-behavior"></a>舊的行為

在 EF Core 3.1 中，已初始化為非 null 值的參考導覽立即，有時會由資料庫中的實體實例覆寫，而不論索引鍵值是否相符。 不過，在其他情況下，EF Core 3.1 會相反地，並保留現有的非 null 值。

#### <a name="new-behavior"></a>新的行為

從 EF Core 5.0 開始，查詢所傳回的實例永遠不會覆寫非 null 的參考導覽。

請注意，仍支援對空集合進行 _集合_ 導覽的積極式初始化。

#### <a name="why"></a>原因

將參考導覽屬性初始化為「空白」實體實例，會導致不明確的狀態。 例如：

```csharp
public class Blog
{
     public int Id { get; set; }
     public Author Author { get; set; ) = new Author();
}
```

通常，Blog 和作者的查詢會先建立 `Blog` 實例，然後 `Author` 根據資料庫所傳回的資料，設定適當的實例。 不過，在此情況下，每個 `Blog.Author` 屬性都已初始化為空的 `Author` 。 除了 EF Core 無法得知此實例是否為「空白」。 因此，覆寫此實例可能會以無訊息方式擲回有效的 `Author` 。 因此，EF Core 5.0 現在一致地不會覆寫已初始化的導覽。

在大部分的情況下，這項新行為也會與 EF6 的行為一致，不過在調查時，我們也發現 EF6 中有一些不一致的情況。

#### <a name="mitigations"></a>風險降低

如果遇到此中斷，則修正程式是停止立即初始化參考導覽屬性。

<a name="toview"></a>

### <a name="toview-is-treated-differently-by-migrations"></a>ToView () 由遷移以不同方式處理

[追蹤問題 #2725](https://github.com/dotnet/efcore/issues/2725)

#### <a name="old-behavior"></a>舊的行為

`ToView(string)`除了將實體類型對應至視圖之外，呼叫的呼叫也會忽略該實體類型。

#### <a name="new-behavior"></a>新的行為

現在 `ToView(string)` 除了將實體類型對應至視圖之外，還會將實體類型標示為未對應到資料表。 這會導致在升級至 EF Core 5 之後第一次遷移，以嘗試卸載此實體類型的預設資料表，因為它不會再被忽略。

#### <a name="why"></a>原因

EF Core 現在允許同時將實體類型對應至資料表和視圖，因此不 `ToView` 會再由遷移所忽略的有效指標。

#### <a name="mitigations"></a>風險降低

使用下列程式碼，將對應的資料表標示為從遷移中排除：

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<User>().ToTable("UserView", t => t.ExcludeFromMigrations());
}
```

<a name="totable"></a>

### <a name="totablenull-marks-the-entity-type-as-not-mapped-to-a-table"></a>ToTable (null) 將實體類型標示為未對應至資料表

[追蹤問題 #21172](https://github.com/dotnet/efcore/issues/21172)

#### <a name="old-behavior"></a>舊的行為

`ToTable(null)` 會將資料表名稱重設為預設值。

#### <a name="new-behavior"></a>新的行為

`ToTable(null)` 現在會將實體類型標示為未對應到任何資料表。

#### <a name="why"></a>原因

EF Core 現在允許同時將實體類型對應至資料表和視圖，因此 `ToTable(null)` 用來表示它不會對應到任何資料表。

#### <a name="mitigations"></a>風險降低

如果資料表名稱未對應到 view 或 DbFunction，請使用下列程式碼將它重設為預設值：

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<User>().Metadata.RemoveAnnotation(RelationalAnnotationNames.TableName);
}
```

## <a name="low-impact-changes"></a>低影響的變更

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a>已從 SQLite NTS 擴充功能移除 HasGeometricDimension 方法

[追蹤問題 #14257](https://github.com/dotnet/efcore/issues/14257)

#### <a name="old-behavior"></a>舊的行為

HasGeometricDimension 用來在幾何資料行上啟用 (Z 和 M) 的其他維度。 但是，它只會影響資料庫的建立。 不需要將它指定為使用其他維度來查詢值。 使用額外的維度插入或更新值時，也無法正確運作 ([請參閱 #14257](https://github.com/dotnet/efcore/issues/14257)) 。

#### <a name="new-behavior"></a>新的行為

若要啟用以其他維度插入和更新幾何值 (Z 和 M) ，必須將維度指定為數據行類型名稱的一部分。 此 API 更接近 SpatiaLite 的 AddGeometryColumn 函式的基礎行為。

#### <a name="why"></a>原因

在資料行類型中指定維度之後使用 HasGeometricDimension 是不必要且多餘的，因此我們會完全移除 HasGeometricDimension。

#### <a name="mitigations"></a>風險降低

使用 `HasColumnType` 指定維度：

```csharp
modelBuilder.Entity<GeoEntity>(
    x =>
    {
        // Allow any GEOMETRY value with optional Z and M values
        x.Property(e => e.Geometry).HasColumnType("GEOMETRYZM");

        // Allow only POINT values with an optional Z value
        x.Property(e => e.Point).HasColumnType("POINTZ");
    });
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a>Cosmos：現在已將資料分割索引鍵新增至主要索引鍵

[追蹤問題 #15289](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a>舊的行為

分割區索引鍵屬性只會新增至包含的替代索引鍵 `id` 。

#### <a name="new-behavior"></a>新的行為

依慣例，資料分割索引鍵屬性現在也會依慣例新增至主要索引鍵。

#### <a name="why"></a>原因

這項變更可讓模型與 Azure Cosmos DB 語義更一致，並改善 `Find` 和某些查詢的效能。

#### <a name="mitigations"></a>風險降低

若要避免將分割區索引鍵屬性加入至主鍵，請在中進行設定 `OnModelCreating` 。

```csharp
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a>Cosmos： `id` 屬性已重新命名為 `__id`

[追蹤問題 #17751](https://github.com/dotnet/efcore/issues/17751)

#### <a name="old-behavior"></a>舊的行為

對應至 JSON 屬性的陰影屬性 `id` 也稱為 `id` 。

#### <a name="new-behavior"></a>新的行為

慣例所建立的陰影屬性現在已命名 `__id` 。

#### <a name="why"></a>原因

這種變更可讓 `id` 屬性與實體型別上的現有屬性不衝突。

#### <a name="mitigations"></a>風險降低

若要返回3.x 行為，請 `id` 在中設定屬性 `OnModelCreating` 。

```csharp
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a>Cosmos： byte [] 現在會儲存為 base64 字串，而不是數位陣列

[追蹤問題 #17306](https://github.com/dotnet/efcore/issues/17306)

#### <a name="old-behavior"></a>舊的行為

Byte [] 類型的屬性已儲存為數字陣列。

#### <a name="new-behavior"></a>新的行為

Byte [] 類型的屬性現在會儲存為 base64 字串。

#### <a name="why"></a>原因

Byte [] 的這個標記法符合預期，而且是主要 JSON 序列化程式庫的預設行為。

#### <a name="mitigations"></a>風險降低

以數位陣列形式儲存的現有資料仍會正確地進行查詢，但目前不支援變更插入行為的方式。 如果這項限制會封鎖您的案例，請針對[此問題](https://github.com/dotnet/efcore/issues/17306)進行批註

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a>Cosmos： GetPropertyName 和 SetPropertyName 已重新命名

[追蹤問題 #17874](https://github.com/dotnet/efcore/issues/17874)

#### <a name="old-behavior"></a>舊的行為

先前已呼叫擴充方法 `GetPropertyName` ，而且 `SetPropertyName`

#### <a name="new-behavior"></a>新的行為

移除舊的 API 並新增新 `GetJsonPropertyName` 方法： `SetJsonPropertyName`

#### <a name="why"></a>原因

這項變更會移除這些方法所設定的混淆。

#### <a name="mitigations"></a>風險降低

使用新的 API。

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a>當實體狀態從卸離變更為未變更、更新或刪除時，會呼叫值產生器

[追蹤問題 #15289](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a>舊的行為

只有當實體狀態變更為 [已加入] 時，才會呼叫值產生器。

#### <a name="new-behavior"></a>新的行為

現在當實體狀態從卸離變更為未變更、已更新或已刪除，且屬性包含預設值時，就會呼叫值產生器。

#### <a name="why"></a>原因

需要進行這種變更，才能使用不會保存到資料存放區的屬性，以及在用戶端上永遠產生其值的方式，來改善體驗。

#### <a name="mitigations"></a>風險降低

若要防止呼叫值產生器，請在狀態變更之前，將非預設值指派給屬性。

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a>IMigrationsModelDiffer 現在使用 IRelationalModel

[追蹤問題 #20305](https://github.com/dotnet/efcore/issues/20305)

#### <a name="old-behavior"></a>舊的行為

`IMigrationsModelDiffer` API 的定義使用 `IModel` 。

#### <a name="new-behavior"></a>新的行為

`IMigrationsModelDiffer` API 現在會使用 `IRelationalModel` 。 不過，模型快照集仍會包含， `IModel` 因為此程式碼是應用程式的一部分，而且 Entity Framework 不會進行較大的中斷變更，因此無法變更它。

#### <a name="why"></a>原因

`IRelationalModel` 這是新加入的資料庫架構標記法。 使用它來尋找差異會更快且更精確。

#### <a name="mitigations"></a>風險降低

您可以使用下列程式碼，將模型 `snapshot` 與模型進行比較， `context` 如下所示：

```csharp
var dependencies = context.GetService<ProviderConventionSetBuilderDependencies>();
var relationalDependencies = context.GetService<RelationalConventionSetBuilderDependencies>();

var typeMappingConvention = new TypeMappingConvention(dependencies);
typeMappingConvention.ProcessModelFinalizing(((IConventionModel)modelSnapshot.Model).Builder, null);

var relationalModelConvention = new RelationalModelConvention(dependencies, relationalDependencies);
var sourceModel = relationalModelConvention.ProcessModelFinalized(snapshot.Model);

var modelDiffer = context.GetService<IMigrationsModelDiffer>();
var hasDifferences = modelDiffer.HasDifferences(
    ((IMutableModel)sourceModel).FinalizeModel().GetRelationalModel(),
    context.Model.GetRelationalModel());
```

我們打算在 6.0 ([查看 #22031](https://github.com/dotnet/efcore/issues/22031) 的這項體驗) 

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a>鑒別子是唯讀的

[追蹤問題 #21154](https://github.com/dotnet/efcore/issues/21154)

#### <a name="old-behavior"></a>舊的行為

您可以在呼叫之前變更鑒別子值 `SaveChanges`

#### <a name="new-behavior"></a>新的行為

在上述案例中，將會擲回例外狀況。

#### <a name="why"></a>原因

EF 不會預期實體類型在仍在追蹤時變更，因此變更鑒別子值會使內容處於不一致的狀態，而這可能會導致非預期的行為。

#### <a name="mitigations"></a>風險降低

如果需要變更鑒別子值，而且會在呼叫後立即處置內容 `SaveChanges` ，則可以將鑒別子設為可變：

```csharp
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a>提供者特定的 EF。InMemory 提供者的函數方法擲回

[追蹤問題 #20294](https://github.com/dotnet/efcore/issues/20294)

#### <a name="old-behavior"></a>舊的行為

提供者特定的 EF。函數方法包含用戶端執行的執行，可讓它們在 InMemory 提供者上執行。 例如， `EF.Functions.DateDiffDay` 是一種 Sql Server 特定的方法，它是在 InMemory 提供者上處理。

#### <a name="new-behavior"></a>新的行為

提供者特定方法已更新，在其方法主體中擲回例外狀況，以封鎖在用戶端上進行評估。

#### <a name="why"></a>原因

提供者特定的方法會對應至資料庫函式。 對應資料庫函式所完成的計算無法永遠在 LINQ 的用戶端上複寫。 在用戶端上執行相同的方法時，可能會導致伺服器的結果不同。 因為這些方法是在 LINQ 中用來轉譯為特定的資料庫函式，所以不需要在用戶端上進行評估。 因為 InMemory 提供者是不同的 *資料庫*，所以這些方法不適用於此提供者。 嘗試針對 InMemory 提供者或任何其他未轉譯這些方法的提供者執行這些專案時，會擲回例外狀況。

#### <a name="mitigations"></a>風險降低

因為無法準確模擬資料庫函式的行為，所以您應該針對與生產環境中相同類型的資料庫，測試包含它們的查詢。

<a name="index-obsolete"></a>

### <a name="indexbuilderhasname-is-now-obsolete"></a>IndexBuilder. HasName 現已淘汰

[追蹤問題 #21089](https://github.com/dotnet/efcore/issues/21089)

#### <a name="old-behavior"></a>舊的行為

之前，只能在一組指定的屬性上定義一個索引。 使用 IndexBuilder. HasName 設定索引的資料庫名稱。

#### <a name="new-behavior"></a>新的行為

相同的集合或屬性現在允許多個索引。 這些索引現在是由模型中的名稱來區分。 依照慣例，會使用模型名稱做為資料庫名稱;不過，您也可以使用 HasDatabaseName 獨立設定它。

#### <a name="why"></a>原因

未來，我們想要在相同的屬性集上啟用遞增和遞減索引，或具有不同定序的索引。 這項變更會將另一個步驟移至該方向。

#### <a name="mitigations"></a>風險降低

先前呼叫 IndexBuilder 的任何程式碼都應該更新為改為呼叫 HasDatabaseName。

如果您的專案包含在 EF Core 版本2.0.0 之前產生的遷移，您可以放心地忽略這些檔案中的警告，並藉由新增來將它隱藏 `#pragma warning disable 612, 618` 。

<a name="pluralizer"></a>

### <a name="a-pluralizer-is-now-included-for-scaffolding-reverse-engineered-models"></a>Pluralizer 現已包含給樣板反轉工程模型

[追蹤問題 #11160](https://github.com/dotnet/efcore/issues/11160)

#### <a name="old-behavior"></a>舊的行為

先前，您必須安裝個別的 pluralizer 套件，才能在將資料庫架構還原為 DbCoNtext 和實體類型時，複數化 DbSet 和集合導覽名稱和單數化資料表名稱。

#### <a name="new-behavior"></a>新的行為

EF Core 現在包含使用 [Humanizer](https://github.com/Humanizr/Humanizer) 程式庫的 pluralizer。 這是 Visual Studio 用來建議變數名稱的相同程式庫。

#### <a name="why"></a>原因

針對集合屬性使用複數形式的單字以及類型和參考屬性的單數形式，在 .NET 中慣用。

#### <a name="mitigations"></a>風險降低

若要停用 pluralizer，請使用 `--no-pluralize` 上的選項 `dotnet ef dbcontext scaffold` 或開啟上的 `-NoPluralize` 參數 `Scaffold-DbContext` 。

<a name="inavigationbase"></a>

### <a name="inavigationbase-replaces-inavigation-in-some-apis-to-support-skip-navigations"></a>INavigationBase 取代某些 Api 中的 INavigation，以支援略過導覽

[追蹤問題 #2568](https://github.com/dotnet/EntityFramework.Docs/issues/2568)

#### <a name="old-behavior"></a>舊的行為

5.0 之前的 EF Core 只支援一個以介面表示的導覽屬性形式 `INavigation` 。

#### <a name="new-behavior"></a>新的行為

EF Core 5.0 引進了使用「略過導覽」的多對多關聯性。 這些是由介面表示 `ISkipNavigation` ，而的大部分功能 `INavigation` 都已向下推送至通用基底介面： `INavigationBase` 。

#### <a name="why"></a>原因

一般與略過導覽之間的大部分功能都相同。 不過，略過導覽與外鍵的關聯性與一般導覽不同，因為所涉及的 Fk 不會直接在關聯性的任一端，而是在聯結實體中。

#### <a name="mitigations"></a>風險降低

在許多情況下，應用程式可以切換為使用新的基底介面，而不需要進行其他變更。 不過，在使用導覽來存取外鍵屬性的情況下，應用程式程式碼應該只限于一般導覽，或更新為一般和略過導覽的適當事物。

<a name="collection-distinct-groupby"></a>

### <a name="some-queries-with-correlated-collection-that-also-use-distinct-or-groupby-are-no-longer-supported"></a>某些使用 `Distinct` 或已不再支援之相互關聯集合的查詢 `GroupBy`

[追蹤問題 #15873](https://github.com/dotnet/efcore/issues/15873)

**舊行為**

先前的查詢牽涉到相關聯的集合，後面接著 `GroupBy` ，以及使用 `Distinct` 我們允許執行的一些查詢。

GroupBy 範例：

```csharp
context.Parents
    .Select(p => p.Children
        .GroupBy(c => c.School)
        .Select(g => g.Key))
```

`Distinct` 範例-明確 `Distinct` 查詢內部集合投射未包含主鍵的查詢：

```csharp
context.Parents
    .Select(p => p.Children
        .Select(c => c.School)
        .Distinct())
```

如果內部集合包含任何重複專案，這些查詢可能會傳回不正確的結果，但如果內部集合中的所有專案都是唯一的，則這些查詢會正確運作。

**新的行為**

不再支援這些查詢。 擲回例外狀況，表示我們沒有足夠的資訊來正確建立結果。

**為什麼**

針對相互關聯的集合案例，我們需要知道實體的主鍵，才能將集合實體指派給正確的父系。 當內部集合不使用 `GroupBy` 或時 `Distinct` ，遺漏的主鍵可以直接加入投影中。 但是，如果是 `GroupBy` ， `Distinct` 則無法完成，因為它會變更 `GroupBy` 或運算的結果 `Distinct` 。

**風險降低**

請將查詢重寫為不使用 `GroupBy` `Distinct` 內部集合上的或作業，並改為在用戶端上執行這些作業。

```csharp
context.Parents
    .Select(p => p.Children.Select(c => c.School))
    .ToList()
    .Select(x => x.GroupBy(c => c).Select(g => g.Key))
```

```csharp
context.Parents
    .Select(p => p.Children.Select(c => c.School))
    .ToList()
    .Select(x => x.Distinct())
```

<a name="queryable-projection"></a>

### <a name="using-a-collection-of-queryable-type-in-projection-is-not-supported"></a>不支援在投射中使用可查詢型別的集合

[追蹤問題 #16314](https://github.com/dotnet/efcore/issues/16314)

**舊行為**

先前，在某些情況下，可以在投射內使用可查詢型別的集合，例如作為函式的引數 `List<T>` ：

```csharp
context.Blogs
    .Select(b => new List<Post>(context.Posts.Where(p => p.BlogId == b.Id)))
```

**新的行為**

不再支援這些查詢。 擲回例外狀況，表示我們無法建立可查詢型別的物件，並建議如何修正此問題。

**為什麼**

我們無法具體化可查詢型別的物件，因此會自動使用型別來建立它們 `List<T>` 。 這通常會造成例外狀況，因為類型不符，但不太清楚，而且可能會對某些使用者感到驚訝。 我們決定要辨識模式，並擲回更有意義的例外狀況。

**風險降低**

在 `ToList()` 投射中的可查詢物件之後加入呼叫：

```csharp
context.Blogs.Select(b => context.Posts.Where(p => p.BlogId == b.Id).ToList())
```
