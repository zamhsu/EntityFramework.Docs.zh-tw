---
title: 自訂程式碼 First 慣例 EF6
author: divega
ms.date: 2016-10-23
ms.assetid: dd2bdbd9-ae9e-470a-aeb8-d0ba160499b7
ms.openlocfilehash: a0e8080037cf86640275f498ed159c847ff5c057
ms.sourcegitcommit: 0d36e8ff0892b7f034b765b15e041f375f88579a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2018
ms.locfileid: "44251059"
---
# <a name="custom-code-first-conventions"></a>自訂程式碼的第一個慣例
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。

當使用 Code First 模型會計算從您使用一組慣例的類別。 預設值[程式碼的第一個慣例](~/ef6/modeling/code-first/conventions/built-in.md)判斷項目，例如其屬性變成實體的主索引鍵]、 [實體對應至，資料表和哪些有效位數和小數位數的十進位資料行有預設的名稱。

有時候這些預設慣例不適合您的模型，而且您必須藉由設定許多使用資料註解或 Fluent API 的個別實體可以解決它們。 自訂程式碼的第一個慣例可讓您定義自己提供組態的預設值適用於您機型的慣例。 在本逐步解說中，我們將探討不同類型的自訂慣例，以及如何建立每個。


## <a name="model-based-conventions"></a>以模型為基礎的慣例

本頁涵蓋自訂慣例 DbModelBuilder API。 此 API 應該就足以撰寫大部分的自訂慣例。 不過，還有能夠撰寫模型為基礎的慣例-操作最終模型，建立之後的慣例來處理進階的案例。 如需詳細資訊，請參閱 <<c0> [ 模型為基礎的慣例](~/ef6/modeling/code-first/conventions/model.md)。

 

## <a name="our-model"></a>我們的模型

一開始先定義一個簡單的模型，我們可以使用我們的慣例。 將下列類別新增至您的專案。

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

 

## <a name="introducing-custom-conventions"></a>介紹自訂慣例

讓我們撰寫會設定名為實體型別的主索引鍵的索引鍵的任何屬性的慣例。

模型產生器，可以藉由覆寫內容中的 OnModelCreating 存取已啟用慣例。 更新 ProductContext 類別如下所示：

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

現在，我們名為索引鍵的模型中的任何屬性會設定為任何實體的主索引鍵的一部分。

我們也可以讓我們慣例具體方法是透過篩選，我們要設定屬性的型別：

``` csharp
    modelBuilder.Properties<int>()
                .Where(p => p.Name == "Key")
                .Configure(p => p.IsKey());
```

這會設定所有的屬性稱為索引鍵值必須是主要索引鍵的實體，但前提是它們是整數。

IsKey 方法的一個有趣的功能是它是加總。 這表示如果您在多個屬性上呼叫 IsKey，它們將所有複合的索引鍵的一部分。 有一點要注意的是，當您指定多個屬性的索引鍵時您也必須指定這些屬性的順序。 您可以藉由呼叫方法，如下所示 HasColumnOrder 來這樣做：

``` csharp
    modelBuilder.Properties<int>()
                .Where(x => x.Name == "Key")
                .Configure(x => x.IsKey().HasColumnOrder(1));

    modelBuilder.Properties()
                .Where(x => x.Name == "Name")
                .Configure(x => x.IsKey().HasColumnOrder(2));
```

此程式碼會設定在我們的模型有 int 索引鍵資料行和字串名稱資料行所組成的複合索引鍵的類型。 如果我們在設計工具中檢視模型看起來應該像這樣：

![複合索引鍵](~/ef6/media/compositekey.png)

屬性的慣例的另一個範例是設定在我的模型對應至 SQL Server 中的 datetime2 類型，而不是日期時間中的所有日期時間屬性。 您可以使用下列來達到此目的：

``` csharp
    modelBuilder.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));
```

 

## <a name="convention-classes"></a>慣例類別

另一種定義慣例是使用慣例的類別，來封裝您的慣例。 使用慣例的類別時您會建立繼承自慣例 System.Data.Entity.ModelConfiguration.Conventions 命名空間中類別的型別。

我們可以使用我們先前透過下列方式顯示的 datetime2 慣例來建立慣例類別：

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

告知 EF 來使用此慣例，將它新增至慣例集合在 OnModelCreating，如果您已遵循本逐步解說會看起來像這樣：

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Properties<int>()
                    .Where(p => p.Name.EndsWith("Key"))
                    .Configure(p => p.IsKey());

        modelBuilder.Conventions.Add(new DateTime2Convention());
    }
```

您可以看到我們加入我們的執行個體至慣例集合。 繼承自慣例提供方便的分組和共用跨小組或專案的慣例。 例如，您就可以讓一組常用的組織的所有專案使用的慣例的類別程式庫。

 

## <a name="custom-attributes"></a>自訂屬性

另一個好的使用的慣例是讓新的屬性，以設定模型時使用。 為了說明這點，讓我們建立的屬性，可用來將字串屬性標示為非 Unicode。

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    public class NonUnicode : Attribute
    {
    }
```

現在，讓我們建立的慣例，以將此屬性套用至我們的模型：

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<NonUnicode>().Any())
                .Configure(c => c.IsUnicode(false));
```

使用此慣例中，我們可以新增任何的字串屬性，這表示在資料庫中的資料行的 NonUnicode 屬性將會儲存為 varchar，而不是 nvarchar。

關於此慣例，請注意一件事是，如果將 NonUnicode 屬性放在字串屬性以外的任何項目則會擲回例外狀況。 它會因為您無法設定 IsUnicode 字串以外的任何型別上。 如果發生這種情況，則您可以讓您的慣例更具體來說，使它會篩選掉任何不是字串的項目。

雖然上述慣例適用於定義自訂屬性沒有另一個 API，可以更容易使用，特別是當您想要從屬性類別的屬性。

此範例中，我們即將更新我們的屬性，並將它變更為 IsUnicode 屬性 (attribute)，讓它看起來像這樣：

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

一旦我們擁有此資料庫，我們可以設定 bool 我們的屬性，以判斷屬性應該 Unicode 的慣例。 我們可以在我們已藉由存取 設定類別，像這樣的 ClrProperty 的慣例來這麼做：

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<IsUnicode>().Any())
                .Configure(c => c.IsUnicode(c.ClrPropertyInfo.GetCustomAttribute<IsUnicode>().Unicode));
```

這很簡單，但還有更簡潔的方法，使用 Having 達到這個目的的慣例 API 的方法。 Having 方法具有參數輸入 Func&lt;PropertyInfo、 T&gt;可接受的 PropertyInfo Where 相同方法，但應該會傳回物件。 如果傳回的物件為 null，就不能設定屬性，這表示您可以篩選出與它的屬性一樣的位置，但它不同，因為它也會擷取傳回的物件，並將它傳遞到設定方法。 其運作如下所示：

``` csharp
    modelBuilder.Properties()
                .Having(x =>x.GetCustomAttributes(false).OfType<IsUnicode>().FirstOrDefault())
                .Configure((config, att) => config.IsUnicode(att.Unicode));
```

自訂屬性不是唯一的理由来使用 Having 方法，您就很有用，您需要設定您的型別或屬性時，您要篩選上的項目原因的任何地方。

 

## <a name="configuring-types"></a>設定類型

到目前為止所有我們慣例已針對屬性，但您的模型中設定類型的慣例 API 的另一個區域。 體驗類似於我們到目前為止，所看到的慣例，但內部設定的選項會在該實體，而不是屬性層級。

型別層級的慣例可以是很有用的事情之一就變更的資料表命名慣例，將對應至現有的結構描述不同於 EF 的預設值，或建立新的資料庫使用不同的命名慣例。 若要這樣做我們首先需要的方法，可以接受在我們的模型型別的 TypeInfo 並傳回該類型的資料表名稱應該為：

``` csharp
    private string GetTableName(Type type)
    {
        var result = Regex.Replace(type.Name, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

這個方法會採用型別，並傳回有底線，而不是 CamelCase 使用小寫的字串。 在我們的模型，這表示，ProductCategory 類別會對應至資料表 product\_而不是 [ProductCategories] 類別。

一旦我們擁有該方法我們可以像這樣的慣例中呼叫它：

``` csharp
    modelBuilder.Types()
                .Configure(c => c.ToTable(GetTableName(c.ClrType)));
```

這個慣例會設定每個型別，在我們的模型對應至我們 GetTableName 方法所傳回的資料表名稱。 此規範相當於使用 Fluent API 模型中的每個實體呼叫 ToTable 方法。

要注意一點是，當您呼叫 ToTable EF 會需要您提供完整的資料表名稱，不含任何它通常會執行時判斷資料表名稱的複數表示為字串。 這就是為什麼我們慣例的資料表名稱是產品\_類別，而不是產品\_類別。 我們可以解決問題，在我們自己進行複數表示服務的呼叫。

我們將使用下列程式碼[相依性解析](~/ef6/fundamentals/configuring/dependency-resolution.md)擷取複數表示服務會使用 EF 的 EF6 中新增的功能和複數化我們資料表的名稱。

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
> GetService 的泛型版本是擴充方法 System.Data.Entity.Infrastructure.DependencyResolution 命名空間中的，您必須新增 using 陳述式，您才能使用它的內容。

### <a name="totable-and-inheritance"></a>ToTable 和繼承

ToTable 的另一個重要層面是，如果您必須明確地將型別對應至指定的資料表，則您可以變更將會使用 EF 的對應策略。 如果您呼叫 ToTable 繼承階層架構中的每一個型別時，傳遞的型別名稱做為資料表的名稱，如同上述，則您將會變更預設值表-table-per Hierarchy (TPH) 對應策略以每一類一表 (TPT)。 描述此最佳的辦法是 whith 具體的範例：

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

預設情況下同時使用 employee 和 manager 會對應至資料庫中相同的資料表 （員工）。 資料表包含員工和經理會告訴您何種類型的執行個體儲存在每個資料列中的鑑別子資料行。 這會是 TPH 對應，因為沒有單一階層的資料表。 不過，如果您在這兩個 classe 呼叫 ToTable 然後每個型別會改為對應至其本身的資料表，也稱為 TPT 因為每個類型都有自己的資料表。

``` csharp
    modelBuilder.Types()
                .Configure(c=>c.ToTable(c.ClrType.Name));
```

上述程式碼會對應至資料表結構看起來如下所示：

![tpt 範例](~/ef6/media/tptexample.jpg)

您可以避免這項目，並維護預設 TPH 對應，透過幾種方式：

1.  呼叫 ToTable 階層中每個類型相同的資料表名稱。
2.  只在階層中，在我們的範例是員工的基底類別上呼叫 ToTable。

 

## <a name="execution-order"></a>執行順序

慣例以最後一個 wins 方式運作，Fluent API 相同。 這表示，如果您要撰寫兩個設定相同屬性的相同選項的慣例，則最後一個執行 wins。 舉例來說，下列程式碼中的所有字串的最大長度設為 500，但接著就設定在模型中才能具有最大長度 250 名為 Name 的所有屬性。

``` csharp
    modelBuilder.Properties<string>()
                .Configure(c => c.HasMaxLength(500));

    modelBuilder.Properties<string>()
                .Where(x => x.Name == "Name")
                .Configure(c => c.HasMaxLength(250));
```

要設定長度上限為 250 的慣例後的所有字串都設定為 500，因為在我們的模型稱為名稱的所有屬性會有長度上限為 250 任何其他字串，例如描述時，會是 500。 使用慣例，如此一來表示，您可以提供一般慣例類型或內容中您模型，然後覆寫它們以提供不同的子集。

資料註解與 Fluent API 也可用來覆寫慣例，以在特定情況下。 在上面的範例如果我們使用 Fluent API 來設定屬性的最大長度然後我們可能已將它放之前或之後的慣例，因為更特定的 Fluent API，將會贏得更一般的設定慣例。

 

## <a name="built-in-conventions"></a>內建慣例

自訂慣例可能會受到預設 Code First 慣例，因為它可用來將慣例新增到執行之前或之後另一個的慣例。 若要這樣做中，您可以使用慣例集合 AddBefore 和 AddAfter 的方法在您的衍生 DbContext 上。 下列程式碼會新增我們稍早建立，讓它將會執行之前的內建慣例類別中索引鍵探索慣例。

``` csharp
    modelBuilder.Conventions.AddBefore<IdKeyDiscoveryConvention>(new DateTime2Convention());
```

這將會充分使用的，新增需要執行之前或之後的內建慣例的慣例時，一份內建的慣例可以在這裡找到： [System.Data.Entity.ModelConfiguration.Conventions 命名空間](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).

您也可以移除您不想套用至您的模型的慣例。 若要移除的慣例，請使用 Remove 方法。 以下是移除 PluralizingTableNameConvention 的範例。

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
```
