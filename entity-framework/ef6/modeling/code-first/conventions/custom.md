---
title: 自訂 Code First 慣例-EF6
description: Entity Framework 6 中的自訂 Code First 慣例
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/conventions/custom
ms.openlocfilehash: 5142f58d229dd7743fd4d5c7f0004c080e22c9e4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066456"
---
# <a name="custom-code-first-conventions"></a>自訂 Code First 慣例
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。

使用 Code First 您的模型會使用一組慣例從您的類別計算。 預設的 [Code First 慣例](xref:ef6/modeling/code-first/conventions/built-in) 會決定哪些屬性會成為實體的主鍵、實體所對應的資料表名稱，以及 decimal 資料行預設的精確度和小數位數。

這些預設慣例有時不適合您的模型，而且您必須使用資料批註或流暢的 API 來設定許多個別實體來解決這些預設慣例。 自訂 Code First 慣例可讓您定義自己的慣例，以提供模型的設定預設值。 在這個逐步解說中，我們將探討不同類型的自訂慣例，以及如何建立每個類型。


## <a name="model-based-conventions"></a>Model-Based 慣例

本頁面涵蓋自訂慣例的 DbModelBuilder API。 此 API 應該足以撰寫大部分的自訂慣例。 不過，您也可以撰寫以模型為基礎的慣例慣例，在建立模型時操作最終模型，以處理先進的案例。 如需詳細資訊，請參閱以 [模型為基礎的慣例](xref:ef6/modeling/code-first/conventions/model)。

 

## <a name="our-model"></a>我們的模型

讓我們從定義可搭配慣例使用的簡單模型開始。 將下列類別新增至您的專案。

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Linq;

    public class ProductContext : DbContext
    {
        static ProductContext()
        {
            Database.SetInitializer(new DropCreateDatabaseIfModelChanges<ProductContext>());
        }

        public DbSet<Product> Products { get; set; }
    }

    public class Product
    {
        public int Key { get; set; }
        public string Name { get; set; }
        public decimal? Price { get; set; }
        public DateTime? ReleaseDate { get; set; }
        public ProductCategory Category { get; set; }
    }

    public class ProductCategory
    {
        public int Key { get; set; }
        public string Name { get; set; }
        public List<Product> Products { get; set; }
    }
```

 

## <a name="introducing-custom-conventions"></a>自訂慣例簡介

讓我們撰寫一項慣例，將名為 Key 的任何屬性設定為其實體類型的主鍵。

模型產生器上已啟用慣例，可透過覆寫內容中的 OnModelCreating 來存取。 更新 ProductCoNtext 類別，如下所示：

``` csharp
    public class ProductContext : DbContext
    {
        static ProductContext()
        {
            Database.SetInitializer(new DropCreateDatabaseIfModelChanges<ProductContext>());
        }

        public DbSet<Product> Products { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Properties()
                        .Where(p => p.Name == "Key")
                        .Configure(p => p.IsKey());
        }
    }
```

現在，模型中名為 Key 的任何屬性，將會設定為其所屬的任何實體的主鍵。

我們也可以篩選要設定的屬性類型，讓我們的慣例更明確：

``` csharp
    modelBuilder.Properties<int>()
                .Where(p => p.Name == "Key")
                .Configure(p => p.IsKey());
```

這會將名為 Key 的所有屬性設定為其實體的主鍵，但只有在它們是整數時。

IsKey 方法有一個有趣的功能，就是它是加法。 這表示，如果您在多個屬性上呼叫 IsKey，它們全都會成為複合索引鍵的一部分。 有一點要注意的是，當您為索引鍵指定多個屬性時，您也必須指定這些屬性的順序。 若要這麼做，您可以呼叫 HasColumnOrder 方法，如下所示：

``` csharp
    modelBuilder.Properties<int>()
                .Where(x => x.Name == "Key")
                .Configure(x => x.IsKey().HasColumnOrder(1));

    modelBuilder.Properties()
                .Where(x => x.Name == "Name")
                .Configure(x => x.IsKey().HasColumnOrder(2));
```

這段程式碼會將模型中的型別設定為具有由 int 索引鍵資料行和字串名稱資料行所組成的複合索引鍵。 如果我們在設計工具中看到模型，它看起來會像這樣：

![複合索引鍵](~/ef6/media/compositekey.png)

屬性慣例的另一個範例，是在模型中設定所有 DateTime 屬性，以對應至 SQL Server 中的 datetime2 型別，而非 DateTime。 您可以使用下列內容達成此目的：

``` csharp
    modelBuilder.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));
```

 

## <a name="convention-classes"></a>慣例類別

定義慣例的另一種方式是使用慣例類別來封裝您的慣例。 使用慣例類別時，您會建立一個繼承自 ModelConfiguration 命名空間中慣例類別的型別。

我們可以藉由執行下列步驟，以稍早所示的 datetime2 慣例來建立慣例類別：

``` csharp
    public class DateTime2Convention : Convention
    {
        public DateTime2Convention()
        {
            this.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));        
        }
    }
```

若要告訴 EF 使用此慣例，請將它新增至 OnModelCreating 的慣例集合，如果您已遵循本逐步解說的說明，將會如下所示：

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Properties<int>()
                    .Where(p => p.Name.EndsWith("Key"))
                    .Configure(p => p.IsKey());

        modelBuilder.Conventions.Add(new DateTime2Convention());
    }
```

如您所見，我們會將慣例的實例新增至慣例集合。 繼承自慣例提供在小組或專案之間分組和共用慣例的便利方式。 例如，您可以有一個類別庫，其中包含您所有組織專案都使用的一組通用慣例。

 

## <a name="custom-attributes"></a>自訂屬性

慣例的另一個絕佳用途，是要在設定模型時啟用新的屬性。 為了說明這一點，讓我們建立一個屬性，讓我們可以用來將字串屬性標示為非 Unicode。

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    public class NonUnicode : Attribute
    {
    }
```

現在，讓我們建立將此屬性套用至模型的慣例：

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<NonUnicode>().Any())
                .Configure(c => c.IsUnicode(false));
```

使用這個慣例，我們可以將 NonUnicode 屬性加入至任何字串屬性，這表示資料庫中的資料行會儲存為 Varchar 而非 Nvarchar。

有一點要注意的是，如果您將 NonUnicode 屬性放在字串屬性以外的任何字元，則會擲回例外狀況。 這樣做的原因是因為您無法在字串以外的任何類型上設定 IsUnicode。 如果發生這種情況，您可以讓您的慣例更明確，讓它篩選掉不是字串的任何事物。

雖然上述慣例適用于定義自訂屬性，但還有另一個 API 可能更容易使用，特別是當您想要使用屬性類別的屬性時。

在此範例中，我們要更新屬性，並將其變更為 IsUnicode 屬性，使其看起來像這樣：

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    internal class IsUnicode : Attribute
    {
        public bool Unicode { get; set; }

        public IsUnicode(bool isUnicode)
        {
            Unicode = isUnicode;
        }
    }
```

一旦有了這種情況之後，我們就可以在屬性上設定 bool，以告知慣例屬性是否應該是 Unicode。 我們可以藉由存取設定類別的 ClrProperty 來達成此目的，如下所示：

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<IsUnicode>().Any())
                .Configure(c => c.IsUnicode(c.ClrPropertyInfo.GetCustomAttribute<IsUnicode>().Unicode));
```

這很簡單，但使用「慣例 API」的 Having 方法可以更簡潔地達成此目的。 Having 方法具有 Func PropertyInfo 型別的參數 &lt; ， &gt; 它接受的 PropertyInfo 與 Where 方法相同，但預期會傳回物件。 如果傳回的物件為 null，則不會設定此屬性，這表示您可以像在 Where 一樣地篩選出屬性，但它也不同，因為它也會捕捉傳回的物件，並將它傳遞至設定方法。 其運作方式如下所示：

``` csharp
    modelBuilder.Properties()
                .Having(x => x.GetCustomAttributes(false).OfType<IsUnicode>().FirstOrDefault())
                .Configure((config, att) => config.IsUnicode(att.Unicode));
```

自訂屬性不是使用 Having 方法的唯一原因，它很適合您在設定類型或屬性時所要篩選的某個內容的任何位置。

 

## <a name="configuring-types"></a>設定類型

到目前為止，我們所有的慣例都是針對屬性，但是慣例 API 還有另一個區域，可在您的模型中設定類型。 此體驗類似于目前為止所見的慣例，但 [設定] 內的選項會在實體上，而不是屬性層級。

類型層級慣例的其中一件事，就是變更資料表命名慣例，以對應至與 EF 預設值不同的現有架構，或使用不同的命名慣例來建立新的資料庫。 若要這樣做，我們必須先有方法可以接受模型中類型的 TypeInfo，並傳回該型別的資料表名稱應該是什麼：

``` csharp
    private string GetTableName(Type type)
    {
        var result = Regex.Replace(type.Name, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

這個方法會採用類型，並傳回使用小寫和底線而非 CamelCase 的字串。 在我們的模型中，這表示 ProductCategory 類別會對應到稱為 product category 的資料表， \_ 而不是 ProductCategories。

有了這個方法之後，就可以用像這樣的慣例來呼叫它：

``` csharp
    modelBuilder.Types()
                .Configure(c => c.ToTable(GetTableName(c.ClrType)));
```

此慣例會將模型中的每個型別設定為對應至 GetTableName 方法所傳回的資料表名稱。 這個慣例相當於使用流暢的 API 針對模型中的每個實體呼叫 ToTable 方法。

有一點要注意的是，當您呼叫 ToTable 時，EF 會將您提供的字串做為精確的資料表名稱，而不是在判斷資料表名稱時，通常會執行的任何複數表示。 這就是為什麼我們慣例中的資料表名稱是產品類別目錄， \_ 而不是產品類別目錄的原因 \_ 。 我們可以藉由對複數表示服務進行呼叫，在我們的慣例中解決此問題。

在下列程式碼中，我們將使用 EF6 中加入的相依性 [解析](xref:ef6/fundamentals/configuring/dependency-resolution) 功能，來抓取 EF 已使用的複數表示服務，並複數化我們的資料表名稱。

``` csharp
    private string GetTableName(Type type)
    {
        var pluralizationService = DbConfiguration.DependencyResolver.GetService<IPluralizationService>();

        var result = pluralizationService.Pluralize(type.Name);

        result = Regex.Replace(result, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

> [!NOTE]
> GetService 的泛型版本是 DependencyResolution 命名空間中的擴充方法，您必須將 using 語句加入至您的內容，才能使用它。

### <a name="totable-and-inheritance"></a>ToTable 和繼承

ToTable 的另一個重要層面是，如果您明確地將型別對應到指定的資料表，則可以改變 EF 將使用的對應策略。 如果您針對繼承階層架構中的每個型別呼叫 ToTable，然後將型別名稱當作資料表的名稱來傳遞，那麼您就可以將每個階層的預設資料表 (TPH) 對應策略變更為每個類型的資料表 (TPT) 。 描述這項工作的最佳方式是 whith 具體的範例：

``` csharp
    public class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }
    }

    public class Manager : Employee
    {
        public string SectionManaged { get; set; }
    }
```

根據預設，employee 和 manager 都會對應到資料庫中 (員工) 的相同資料表。 資料表將會包含具有鑒別子資料行的員工和經理，以告訴您每個資料列中儲存的實例類型。 這是 TPH 對應，因為階層有單一資料表。 但是，如果您在這兩個 classe 上呼叫 ToTable，則每個類型都會改為對應至它自己的資料表（也稱為 TPT），因為每個類型都有自己的資料表。

``` csharp
    modelBuilder.Types()
                .Configure(c=>c.ToTable(c.ClrType.Name));
```

上述程式碼會對應到如下所示的資料表結構：

![tpt 範例](~/ef6/media/tptexample.jpg)

您可以透過幾種方式來避免這種情況，並維持預設的 TPH 對應：

1.  針對階層中的每個類型，使用相同的資料表名稱來呼叫 ToTable。
2.  請只針對階層的基類（在我們的範例中為 employee）呼叫 ToTable。

 

## <a name="execution-order"></a>執行順序

慣例的運作方式與流暢的 API 相同。 這表示，如果您撰寫兩個慣例來設定相同屬性的相同選項，則最後一個會執行 wins。 例如，在下列程式碼中，所有字串的最大長度都會設定為500，但接著會在模型中設定名為 Name 的所有屬性，使其長度上限為250。

``` csharp
    modelBuilder.Properties<string>()
                .Configure(c => c.HasMaxLength(500));

    modelBuilder.Properties<string>()
                .Where(x => x.Name == "Name")
                .Configure(c => c.HasMaxLength(250));
```

由於將 [最大長度] 設定為250的慣例是在將所有字串設為500的之後，因此模型中名為 Name 的所有屬性都會有 MaxLength 250，而任何其他字串（例如描述）會是500。 以這種方式使用慣例，表示您可以在模型中提供類型或屬性的一般慣例，然後針對不同的子集覆寫它們。

流暢的 API 和資料批註也可以用來覆寫特定情況下的慣例。 在上述範例中，如果我們使用了流暢的 API 來設定屬性的最大長度，則在慣例之前或之後，我們可以將它放在慣例之前或之後，因為更明確的流暢 API 將會贏得更一般的設定慣例。

 

## <a name="built-in-conventions"></a>內建慣例

因為自訂慣例可能會受到預設 Code First 慣例的影響，所以將慣例新增為在另一個慣例之前或之後執行，可能會很有用。 若要這樣做，您可以在衍生的 DbCoNtext 上使用慣例集合的 AddBefore 和 AddAfter 方法。 下列程式碼會新增我們稍早建立的慣例類別，使其可在內建的金鑰探索慣例之前執行。

``` csharp
    modelBuilder.Conventions.AddBefore<IdKeyDiscoveryConvention>(new DateTime2Convention());
```

當您新增需要在內建慣例之前或之後執行的慣例時，這會是最常用的功能，您可以在這裡找到內建的慣例清單： [ModelConfiguration 命名空間](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)。

您也可以移除您不想套用至模型的慣例。 若要移除慣例，請使用 Remove 方法。 以下是移除 PluralizingTableNameConvention 的範例。

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
```
