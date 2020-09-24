---
title: EF Core 5.0-EF Core 的重大變更
description: Entity Framework Core 5.0 中引進的重大變更完整清單
author: bricelam
ms.date: 09/09/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 8e9df4e2ff81e20cf5a36855247c5aff89ea2394
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2020
ms.locfileid: "91210363"
---
# <a name="breaking-changes-in-ef-core-50"></a>EF Core 5.0 中的重大變更

下列 API 和行為變更可能會中斷現有應用程式更新為 EF Core 5.0.0。

## <a name="summary"></a>摘要

| **重大變更**                                                                                                                   | **影響** |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [從主體到相依的導覽上的必要項具有不同的語義](#required-dependent)                                 | 中     |
| [定義查詢會取代為提供者特定的方法](#defining-query)                                                          | 中     |
| [已從 SQLite NTS 擴充功能移除 HasGeometricDimension 方法](#geometric-sqlite)                                                   | 低度        |
| [Cosmos：現在已將資料分割索引鍵新增至主要索引鍵](#cosmos-partition-key)                                                        | 低度        |
| [Cosmos： `id` 屬性已重新命名為 `__id`](#cosmos-id)                                                                                 | 低度        |
| [Cosmos： byte [] 現在會儲存為 base64 字串，而不是數位陣列](#cosmos-byte)                                             | 低度        |
| [Cosmos： GetPropertyName 和 SetPropertyName 已重新命名](#cosmos-metadata)                                                          | 低度        |
| [當實體狀態從卸離變更為未變更、更新或刪除時，會呼叫值產生器](#non-added-generation) | 低度        |
| [IMigrationsModelDiffer 現在使用 IRelationalModel](#relational-model)                                                                 | 低度        |
| [鑒別子是唯讀的](#read-only-discriminators)                                                                             | 低度        |
| [提供者特定的 EF。InMemory 提供者的函數方法擲回](#no-client-methods)                                              | 低度        |

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a>已從 SQLite NTS 擴充功能移除 HasGeometricDimension 方法

[追蹤問題 #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

**舊行為**

HasGeometricDimension 用來在幾何資料行上啟用 (Z 和 M) 的其他維度。 但是，它只會影響資料庫的建立。 不需要將它指定為使用其他維度來查詢值。 使用額外的維度插入或更新值時，也無法正確運作 ([請參閱 #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)) 。

**新的行為**

若要啟用以其他維度插入和更新幾何值 (Z 和 M) ，必須將維度指定為數據行類型名稱的一部分。 此 API 更接近 SpatiaLite 的 AddGeometryColumn 函式的基礎行為。

**為什麼**

在資料行類型中指定維度之後使用 HasGeometricDimension 是不必要且多餘的，因此我們會完全移除 HasGeometricDimension。

**風險降低**

使用 `HasColumnType` 指定維度：

```cs
modelBuilder.Entity<GeoEntity>(
    x =>
    {
        // Allow any GEOMETRY value with optional Z and M values
        x.Property(e => e.Geometry).HasColumnType("GEOMETRYZM");

        // Allow only POINT values with an optional Z value
        x.Property(e => e.Point).HasColumnType("POINTZ");
    });
```

<a name="required-dependent"></a>

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a>從主體到相依的導覽上的必要項具有不同的語義

[追蹤問題 #17286](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

**舊行為**

只有導覽至主體可設定為必要。 因此， `RequiredAttribute` 在相依 (的實體上使用，包含外鍵的實體) 會改為在定義實體類型上建立外鍵。

**新的行為**

由於已新增對必要相依性的支援，現在可以視需要將任何參考導覽標記，這表示在上方顯示的情況下，外鍵將會定義于關聯性的另一端，而且屬性不會標示為必要項。

`IsRequired`在指定相依端點之前呼叫，現在是不明確的：

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

**為什麼**

若要啟用必要相依性的支援，必須要有新的行為 ([請參閱 #12100](https://github.com/dotnet/efcore/issues/12100)) 。

**風險降低**

`RequiredAttribute`從導覽中移除相依性，並將其放在主體的導覽上，或在中設定關聯性 `OnModelCreating` ：

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a>Cosmos：現在已將資料分割索引鍵新增至主要索引鍵

[追蹤問題 #15289](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

**舊行為**

分割區索引鍵屬性只會新增至包含的替代索引鍵 `id` 。

**新的行為**

依慣例，資料分割索引鍵屬性現在也會依慣例新增至主要索引鍵。

**為什麼**

這項變更可讓模型更符合 Azure Cosmos DB 的語義，並改善 `Find` 和某些查詢的效能。

**風險降低**

若要避免將分割區索引鍵屬性加入至主鍵，請在中進行設定 `OnModelCreating` 。

```cs
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a>Cosmos： `id` 屬性已重新命名為 `__id`

[追蹤問題 #17751](https://github.com/aspnet/EntityFrameworkCore/issues/17751)

**舊行為**

對應至 JSON 屬性的陰影屬性 `id` 也稱為 `id` 。

**新的行為**

慣例所建立的陰影屬性現在已命名 `__id` 。

**為什麼**

這種變更可讓 `id` 屬性與實體型別上的現有屬性不衝突。

**風險降低**

若要返回3.x 行為，請 `id` 在中設定屬性 `OnModelCreating` 。

```cs
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a>Cosmos： byte [] 現在會儲存為 base64 字串，而不是數位陣列

[追蹤問題 #17306](https://github.com/aspnet/EntityFrameworkCore/issues/17306)

**舊行為**

Byte [] 類型的屬性已儲存為數字陣列。

**新的行為**

Byte [] 類型的屬性現在會儲存為 base64 字串。

**為什麼**

Byte [] 的這個標記法符合預期，而且是主要 JSON 序列化程式庫的預設行為。

**風險降低**

以數位陣列形式儲存的現有資料仍會正確地進行查詢，但目前不支援變更插入行為的方式。 如果這項限制會封鎖您的案例，請針對[此問題](https://github.com/aspnet/EntityFrameworkCore/issues/17306)進行批註

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a>Cosmos： GetPropertyName 和 SetPropertyName 已重新命名

[追蹤問題 #17874](https://github.com/aspnet/EntityFrameworkCore/issues/17874)

**舊行為**

先前已呼叫擴充方法 `GetPropertyName` ，而且 `SetPropertyName`

**新的行為**

移除舊的 API 並新增新 `GetJsonPropertyName` 方法： `SetJsonPropertyName`

**為什麼**

這項變更會移除這些方法所設定的混淆。

**風險降低**

使用新的 API。

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a>當實體狀態從卸離變更為未變更、更新或刪除時，會呼叫值產生器

[追蹤問題 #15289](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

**舊行為**

只有當實體狀態變更為 [已加入] 時，才會呼叫值產生器。

**新的行為**

現在當實體狀態從卸離變更為未變更、已更新或已刪除，且屬性包含預設值時，就會呼叫值產生器。

**為什麼**

需要進行這種變更，才能使用不會保存到資料存放區的屬性，以及在用戶端上永遠產生其值的方式，來改善體驗。

**風險降低**

若要防止呼叫值產生器，請在狀態變更之前，將非預設值指派給屬性。

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a>IMigrationsModelDiffer 現在使用 IRelationalModel

[追蹤問題 #20305](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

**舊行為**

`IMigrationsModelDiffer` API 的定義使用 `IModel` 。

**新的行為**

`IMigrationsModelDiffer` API 現在會使用 `IRelationalModel` 。 不過，模型快照集仍會包含， `IModel` 因為此程式碼是應用程式的一部分，而且 Entity Framework 無法變更它，而不會進行較大的中斷變更。

**為什麼**

`IRelationalModel` 這是新加入的資料庫架構標記法。 使用它來尋找差異會更快且更精確。

**風險降低**

您可以使用下列程式碼，將模型 `snapshot` 與模型進行比較， `context` 如下所示：

```cs
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

[追蹤問題 #21154](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

**舊行為**

您可以在呼叫之前變更鑒別子值 `SaveChanges`

**新的行為**

在上述案例中，將會擲回例外狀況。

**為什麼**

EF 不會預期實體類型在仍在追蹤時變更，因此變更鑒別子值會使內容處於不一致的狀態，而這可能會導致非預期的行為。

**風險降低**

如果需要變更鑒別子值，而且會在呼叫後立即處置內容 `SaveChanges` ，則可以將鑒別子設為可變：

```cs
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a>定義查詢會取代為提供者特定的方法

[追蹤問題 #18903](https://github.com/dotnet/efcore/issues/18903)

**舊行為**

實體類型已對應至在核心層級定義查詢。 任何時候，實體類型的查詢根目錄中使用的實體類型已由任何提供者的定義查詢所取代。

**新的行為**

用於定義查詢的 Api 已被取代。 引進了新的提供者特定 Api。

**為什麼**

當查詢中使用查詢根時，定義查詢會實作為取代查詢，但有幾個問題：

- 如果定義查詢是使用 in 方法投射實體型別 `new { ... }` `Select` ，則將其識別為實體需要額外的工作，並使其與 EF Core 在查詢中處理名義類型的方式不一致。
- 針對關聯式提供者， `FromSql` 仍需要以 LINQ 運算式形式傳遞 SQL 字串。

最初定義查詢是以用戶端視圖的形式導入，以用於無索引鍵實體的記憶體內部提供者 (類似于關係資料庫) 中的資料庫檢視。 這類定義可讓您輕鬆地針對記憶體中資料庫測試應用程式。 之後，這些應用程式很有用，但卻變得不一致，而且難以理解的行為。 所以我們決定簡化這個概念。 我們對記憶體內部提供者進行以 LINQ 為基礎的定義查詢，並以不同的方式加以處理。 如需詳細資訊，請 [參閱此問題](https://github.com/dotnet/efcore/issues/20023)。

**風險降低**

若為關聯式提供者，請使用 `ToSqlQuery` 中的方法 `OnModelCreating` 並傳入要用於實體類型的 SQL 字串。
對於記憶體中的提供者，請使用 `ToInMemoryQuery` 中的方法， `OnModelCreating` 並傳入 LINQ 查詢以用於實體型別。

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a>提供者特定的 EF。InMemory 提供者的函數方法擲回

[追蹤問題 #20294](https://github.com/dotnet/efcore/issues/20294)

**舊行為**

提供者特定的 EF。函數方法包含用戶端執行的執行，可讓它們在 InMemory 提供者上執行。 例如， `EF.Functions.DateDiffDay` 是一種 Sql Server 特定的方法，它是在 InMemory 提供者上處理。

**新的行為**

提供者特定方法已更新，在其方法主體中擲回例外狀況，以封鎖在用戶端上進行評估。

**為什麼**

提供者特定的方法會對應至資料庫函式。 對應資料庫函式所完成的計算無法永遠在 LINQ 的用戶端上複寫。 在用戶端上執行相同的方法時，可能會導致伺服器的結果不同。 因為這些方法是在 LINQ 中用來轉譯為特定的資料庫函式，所以不需要在用戶端上進行評估。 因為 InMemory 提供者是不同的 *資料庫*，所以這些方法不適用於此提供者。 嘗試針對 InMemory 提供者或任何其他未轉譯這些方法的提供者執行這些專案時，會擲回例外狀況。

**風險降低**

因為無法準確模擬資料庫函式的行為，所以您應該針對與生產環境中相同類型的資料庫，測試包含它們的查詢。
