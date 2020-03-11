---
title: 自訂 Code First 慣例-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: dd2bdbd9-ae9e-470a-aeb8-d0ba160499b7
ms.openlocfilehash: cfd7f7cad532dca5227793c04d7d91e977ea5e4e
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78419224"
---
# <a name="custom-code-first-conventions"></a>自訂 Code First 慣例
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。

使用 Code First 您的模型是使用一組慣例從類別計算而來。 預設[Code First 慣例](~/ef6/modeling/code-first/conventions/built-in.md)會決定哪些屬性會成為實體的主要金鑰、實體所對應的資料表名稱，以及十進位資料行預設具有的精確度和小數位數。

有時候，這些預設慣例並不適合您的模型，而且您必須使用資料批註或流暢的 API 來設定許多個別實體來解決這些問題。 自訂 Code First 慣例可讓您定義自己的慣例，為您的模型提供設定預設值。 在本逐步解說中，我們將探討不同類型的自訂慣例，以及如何建立它們。


## <a name="model-based-conventions"></a>以模型為基礎的慣例

本頁面涵蓋適用于自訂慣例的 DbModelBuilder API。 此 API 應該足以撰寫大部分的自訂慣例。 不過，也能夠撰寫以模型為基礎的慣例-在建立後操作最終模型的慣例-以處理 advanced 案例。 如需詳細資訊，請參閱以[模型為基礎的慣例](~/ef6/modeling/code-first/conventions/model.md)。

 

## <a name="our-model"></a>我們的模型

讓我們從定義可用於慣例的簡單模型開始。 將下列類別新增至您的專案。

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

讓我們撰寫一個慣例，將任何名為 Key 的屬性設定為其實體類型的主要金鑰。

在模型產生器上啟用慣例，可以藉由覆寫內容中的 OnModelCreating 來存取。 更新 ProductCoNtext 類別，如下所示：

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

現在，名為 Key 之模型中的任何屬性，都會設定為其元件所屬實體的主要索引鍵。

我們也可以篩選我們要設定的屬性類型，讓我們的慣例更為明確：

``` csharp
    modelBuilder.Properties<int>()
                .Where(p => p.Name == "Key")
                .Configure(p => p.IsKey());
```

這會將名為 Key 的所有屬性設定為其實體的主要金鑰，但僅限於其為整數。

IsKey 方法有一個有趣的功能，就是它是累加的。 這表示如果您在多個屬性上呼叫 IsKey，它們全都會成為複合索引鍵的一部分。 要注意的是，當您為索引鍵指定多個屬性時，也必須指定這些屬性的順序。 若要這麼做，您可以呼叫 HasColumnOrder 方法，如下所示：

``` csharp
    modelBuilder.Properties<int>()
                .Where(x => x.Name == "Key")
                .Configure(x => x.IsKey().HasColumnOrder(1));

    modelBuilder.Properties()
                .Where(x => x.Name == "Name")
                .Configure(x => x.IsKey().HasColumnOrder(2));
```

此程式碼會設定模型中的類型，使其具有由 int 索引鍵資料行和字串名稱資料行組成的複合索引鍵。 如果我們在設計工具中看到模型，它看起來會像這樣：

![複合索引鍵](~/ef6/media/compositekey.png)

屬性慣例的另一個範例是設定我的模型中的所有日期時間屬性，以對應至 SQL Server 中的 datetime2 型別，而不是 DateTime。 您可以使用下列各項來達到此目的：

``` csharp
    modelBuilder.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));
```

 

## <a name="convention-classes"></a>慣例類別

定義慣例的另一種方式是使用慣例類別來封裝您的慣例。 當使用慣例類別時，您會建立繼承自 ModelConfiguration 的慣例類別的型別。

我們可以藉由執行下列動作，以先前所示的 datetime2 慣例來建立慣例類別：

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

若要告訴 EF 使用此慣例，您可以將它新增至 OnModelCreating 中的慣例集合，如果您已經遵循此逐步解說，就會如下所示：

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Properties<int>()
                    .Where(p => p.Name.EndsWith("Key"))
                    .Configure(p => p.IsKey());

        modelBuilder.Conventions.Add(new DateTime2Convention());
    }
```

如您所見，我們會將慣例的實例新增至慣例集合。 繼承自慣例可提供便利的方式來分組和共用小組或專案之間的慣例。 例如，您可以有一個類別庫，其中包含一組通用慣例，讓您的所有組織專案都使用。

 

## <a name="custom-attributes"></a>自訂屬性

另一個很棒的慣例使用，就是要在設定模型時，使用新的屬性。 為了說明這一點，讓我們建立一個可以用來將字串屬性標示為非 Unicode 的屬性。

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    public class NonUnicode : Attribute
    {
    }
```

現在，讓我們建立一個慣例，將此屬性套用至我們的模型：

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<NonUnicode>().Any())
                .Configure(c => c.IsUnicode(false));
```

在此慣例中，我們可以將 NonUnicode 屬性加入至我們的任何字串屬性，這表示資料庫中的資料行將會儲存為 Varchar 而非 Nvarchar。

關於此慣例，有一點要注意的是，如果您將 NonUnicode 屬性放在字串屬性以外的任何地方，則會擲回例外狀況。 這是因為您無法在字串以外的任何類型上設定 IsUnicode。 如果發生這種情況，您可以讓慣例更明確，讓它篩選掉不是字串的任何專案。

雖然上述慣例適用于定義自訂屬性，但還有另一個 API 可以更容易使用，特別是當您想要使用屬性類別中的屬性時。

在此範例中，我們將更新屬性，並將其變更為 IsUnicode 屬性，讓它看起來像這樣：

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

這麼做之後，我們可以在屬性上設定 bool，以告知慣例屬性是否應為 Unicode。 我們可以藉由存取設定類別的 ClrProperty 來執行這項操作，如下所示：

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<IsUnicode>().Any())
                .Configure(c => c.IsUnicode(c.ClrPropertyInfo.GetCustomAttribute<IsUnicode>().Unicode));
```

這很簡單，但使用慣例 API 的 Having 方法，可以更簡潔的方式達到此目的。 Having 方法具有 Func 類型的參數&lt;PropertyInfo，T&gt; 會接受與 Where 方法相同的 PropertyInfo，但預期會傳回物件。 如果傳回的物件是 null，則不會設定屬性，這表示您可以用它來篩選屬性，就像在其中一樣，但它也會捕捉傳回的物件，並將它傳遞給 Configure 方法。 其運作方式如下所示：

``` csharp
    modelBuilder.Properties()
                .Having(x =>x.GetCustomAttributes(false).OfType<IsUnicode>().FirstOrDefault())
                .Configure((config, att) => config.IsUnicode(att.Unicode));
```

自訂屬性不是唯一使用 Having 方法的理由，在設定型別或屬性時，您需要進行篩選的任何地方都很有用。

 

## <a name="configuring-types"></a>設定類型

到目前為止，我們所有的慣例都適用于屬性，但在您的模型中設定類型的慣例 API 有另一個區域。 此體驗與我們目前所見的慣例類似，但 [設定] 內的選項會位於實體而非屬性層級。

類型層級慣例的其中一項功能非常適合用來變更資料表命名慣例，也就是對應到與 EF 預設值不同的現有架構，或者使用不同的命名慣例來建立新的資料庫。 若要這麼做，我們必須先有方法可以接受模型中類型的 TypeInfo，並傳回該類型的資料表名稱應為：

``` csharp
    private string GetTableName(Type type)
    {
        var result = Regex.Replace(type.Name, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

這個方法會採用類型，並傳回使用小寫加底線的字串，而不是 CamelCase。 在我們的模型中，這表示 ProductCategory 類別會對應至名為 product\_category 的資料表，而不是 ProductCategories。

有了這個方法之後，我們就可以像這樣的慣例來呼叫它：

``` csharp
    modelBuilder.Types()
                .Configure(c => c.ToTable(GetTableName(c.ClrType)));
```

此慣例會將模型中的每個類型設定為對應至 GetTableName 方法所傳回的資料表名稱。 此慣例等同于使用流暢的 API 針對模型中的每個實體呼叫 ToTable 方法。

有一點要注意的是，當您呼叫 ToTable 時，EF 會採用您提供的字串做為確切的資料表名稱，而不需要在決定資料表名稱時，通常會執行的任何複數表示。 這就是為什麼我們慣例的資料表名稱是產品\_的類別，而不是產品\_的類別。 我們可以自行呼叫複數表示服務，以在我們的慣例中解決此問題。

在下列程式碼中，我們將使用 EF6 中新增的相依性[解析](~/ef6/fundamentals/configuring/dependency-resolution.md)功能來抓取 EF 已使用的複數表示服務，並將複數化我們的資料表名稱。

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

ToTable 的另一個重要層面是，如果您將類型明確對應至指定的資料表，則可以改變 EF 將使用的對應策略。 如果您針對繼承階層架構中的每個類型呼叫 ToTable，傳遞類型名稱做為上述的資料表名稱，則您會將預設的每一階層資料表（TPH）對應策略變更為每一類型的資料表（TPT）。 描述這一點的最佳方式是 whith 具體的範例：

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

根據預設，employee 和 manager 都會對應到資料庫中的相同資料表（員工）。 資料表將同時包含員工和經理與鑒別子資料行，以告訴您每個資料列中儲存了哪種類型的實例。 這是 TPH 對應，因為階層有單一資料表。 不過，如果您在這兩個 classe 上呼叫 ToTable，則每個類型都會對應到它自己的資料表（也稱為 TPT），因為每個類型都有自己的資料表。

``` csharp
    modelBuilder.Types()
                .Configure(c=>c.ToTable(c.ClrType.Name));
```

上述程式碼會對應至如下所示的資料表結構：

![tpt 範例](~/ef6/media/tptexample.jpg)

您可以透過幾種方式來避免這種情況，並維護預設的 TPH 對應：

1.  針對階層中的每個類型，使用相同的資料表名稱來呼叫 ToTable。
2.  只在階層的基類（在我們的範例中為 employee）上呼叫 ToTable。

 

## <a name="execution-order"></a>執行順序

慣例會以最後的 wins 方式運作，與流暢的 API 相同。 這表示如果您撰寫兩個慣例來設定相同屬性的相同選項，則最後一個會執行 wins。 例如，在下列程式碼中，所有字串的最大長度都設為500，但我們接著將模型中所有名為 Name 的屬性設定為最大長度250。

``` csharp
    modelBuilder.Properties<string>()
                .Configure(c => c.HasMaxLength(500));

    modelBuilder.Properties<string>()
                .Where(x => x.Name == "Name")
                .Configure(c => c.HasMaxLength(250));
```

因為將最大長度設定為250的慣例是在將所有字串設定為500的情況下，所以模型中所有名為 Name 的屬性都會有一個250的 MaxLength，而任何其他字串（例如描述）都會是500。 以這種方式使用慣例，表示您可以在模型中提供類型或屬性的一般慣例，然後針對不同的子集加以覆寫。

流暢的 API 和資料批註也可以用來覆寫特定案例中的慣例。 在上述範例中，如果我們使用了流暢的 API 來設定屬性的最大長度，則我們可以將它放在慣例之前或之後，因為更具體的流暢 API 將會優先于較一般的設定慣例。

 

## <a name="built-in-conventions"></a>內建慣例

因為自訂慣例可能會受到預設 Code First 慣例的影響，所以新增慣例以在另一個慣例之前或之後執行可能會很有用。 若要這麼做，您可以在衍生的 DbCoNtext 上使用慣例集合的 AddBefore 和 AddAfter 方法。 下列程式碼會新增我們稍早建立的慣例類別，讓它能夠在內建的金鑰探索慣例之前執行。

``` csharp
    modelBuilder.Conventions.AddBefore<IdKeyDiscoveryConvention>(new DateTime2Convention());
```

在新增必須在內建慣例之前或之後執行的慣例時，這會是最常使用的，您可以在這裡找到內建慣例清單： [ModelConfiguration. 慣例 Namespace](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)。

您也可以移除您不想要套用至模型的慣例。 若要移除慣例，請使用 Remove 方法。 以下是移除 PluralizingTableNameConvention 的範例。

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
```
