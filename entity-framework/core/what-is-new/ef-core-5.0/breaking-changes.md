---
title: EF Core 5.0-EF Core 的重大變更
description: Entity Framework Core 5.0 中引進的重大變更完整清單
author: bricelam
ms.date: 09/08/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: bc6db48edcd7406b31ec2b4369cabf5d55fb4578
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618683"
---
# <a name="breaking-changes-in-ef-core-50"></a>EF Core 5.0 中的重大變更

下列 API 和行為變更可能會中斷現有應用程式更新為 EF Core 5.0.0。

## <a name="summary"></a>摘要

| **重大變更**                                                                                                                   | **影響** |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [從主體到相依的導覽上的必要項具有不同的語義](#required-dependent)                                 | 中     |
| [已從 SQLite NTS 擴充功能移除 HasGeometricDimension 方法](#geometric-sqlite)                                                   | 低        |
| [當實體狀態從已卸離變更為未變更、已更新或已刪除時，會呼叫值產生器](#non-added-generation)  | 低        |
| [IMigrationsModelDiffer 現在使用 IRelationalModel](#relational-model)                                                                 | 低        |
| [鑒別子是唯讀的](#read-only-discriminators)                                                                             | 低        |

<a name="geometric-sqlite"></a>
### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a>已從 SQLite NTS 擴充功能移除 HasGeometricDimension 方法

[追蹤問題 #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

**舊行為**

HasGeometricDimension 用來在幾何資料行上啟用 (Z 和 M) 的其他維度。 但是，它只會影響資料庫的建立。 不需要將它指定為使用其他維度來查詢值。 使用額外的維度插入或更新值時，也無法正確運作 ([請參閱 #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)) 。

**新行為**

若要啟用以其他維度插入和更新幾何值 (Z 和 M) ，必須將維度指定為數據行類型名稱的一部分。 這比起 SpatiaLite 的 AddGeometryColumn 函式的基礎行為更緊密。

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

**新行為**

由於已新增對必要相依性的支援，現在可以視需要將任何參考導覽標示出來，這表示在上方顯示的情況下，外鍵將會定義于關聯性的另一端，而且屬性不會標示為必要項。

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

<a name="non-added-generation"></a>
### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a>當實體狀態從已卸離變更為未變更、已更新或已刪除時，會呼叫值產生器

[追蹤問題 #15289](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

**舊行為**

只有當實體狀態變更為 [已加入] 時，才會呼叫值產生器。

**新行為**

現在當實體狀態從卸離變更為未變更、已更新或已刪除，而且屬性包含預設值時，就會呼叫值產生器。

**為什麼**

需要進行這種變更，才能使用不會保存到資料存放區的屬性，以及在用戶端上永遠產生其值的方式，來改善體驗。

**風險降低**

若要防止呼叫值產生器，請在狀態變更之前將非預設值指派給屬性。

<a name="relational-model"></a>
### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a>IMigrationsModelDiffer 現在使用 IRelationalModel

[追蹤問題 #20305](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

**舊行為**

`IMigrationsModelDiffer` API 的定義使用 `IModel` 。

**新行為**

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

**新行為**

在上述案例中，將會擲回例外狀況。

**為什麼**

EF 不會預期實體類型在仍在追蹤時變更，因此變更鑒別子值會使內容處於不一致的狀態，而這可能會導致非預期的行為。

**風險降低**

如果需要變更鑒別子值，而且在呼叫鑒別子之後，會立即處置內容 `SaveChanges` ：

```cs
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```
